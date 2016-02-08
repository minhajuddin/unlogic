---
comments: true
date: 2009-04-12T15:38:28Z
published: true
tags:
- core graphics
- iPhone
title: Compositing images on an iPhone with Core Graphics
url: /2009/04/12/compositing-images-on-an-iphone-with-core-graphics/
---

How do you take two images, one with rotation and scale applied, and compose the two together in memory and save the result to the device? We make use of core graphics and a little bit of math.

<!--more-->

To set the scene properly here’s what we have: we have an image that’s been scaled and rotated and positioned on screen, over which we would like to place an image that will fill the screen and always be the same size, this is the overlay. Our overlay is 320×480 and positioned centrally in the display and contains transparency to let the background show through. Both images are PNG files.

So what we want to do is create two frames, one for the background image and one for the overlay. Then we create a graphics context for the background image, rotate it, draw our image into it, position it on screen, and then composite the overlay on top of that. Once that’s done we grab the image (now composited) from the graphics context and write it to the device.

Ready? Then let’s set up a frame for both the background image and the overlay

{{< highlight objective-c >}}
// Assume that overlay and userImage are our UIImages
// overlay and background images respectively
// Overlay frame
CGRect frame;
frame.origin = CGPointMake(0,0);
frame.size = CGSizeMake(320,480);

// background frame
// Scale is the factor the image is scaled by (float)
CGRect bgFrame;
bgFrame.size = userImage.size;
bgFrame.size.width *= scale;
bgFrame.size.height *= scale;
{{< / highlight >}}

In order to draw our image rotated we actually need to rotate the context and then draw our image into it. We also need to know the size of the rectangle that contains the rotated context. We’ll use some simple trig to work that out. We need this new size in order to correctly position the background, as core graphics uses the top left corner to place the context and after the rotation the top left corner has moved somewhat. 

{{< highlight objective-c >}}
// Get the size of the background frame (unrotated)
CGSize size = bgFrame.size;

// Work out the bounding box for the rotated image
float h = size.height;
float w = size.width;
// Using the trig we need to know 90deg - the actual rotation angle
float beta = DEGREES_TO_RADIANS(90.0 - (rotation));

// Do the trig for the new size
float h1 = h*fabs(sin(beta)) + w*fabs(cos(beta));
float w1 = w*fabs(sin(beta)) + h*fabs(cos(beta));
// CG works in radians - Using a macro to do the work
float angle = DEGREES_TO_RADIANS(rotation);
CGSize tempFrame;
tempFrame.height = h1;
tempFrame.width = w1;

// Rotation Context starts here
// draw the image into it
UIGraphicsBeginImageContext(tempFrame);
CGContextRef ctx = UIGraphicsGetCurrentContext();
// Rotate around center
CGContextTranslateCTM(ctx, tempFrame.width/2.0, tempFrame.height/2.0);
CGContextRotateCTM(ctx, angle);
CGContextTranslateCTM(ctx, -tempFrame.width/2.0, -tempFrame.height/2.0);

// Origin is this for some reason. One day I'll figure it out
bgFrame.origin = CGPointMake((w1 - bgFrame.size.width)/2.0,
                             (h1 - bgFrame.size.height)/2.0);

// Draw it
[userImage.image drawInRect:bgFrame];

// Get it
UIImage *copy = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
{{< / highlight >}}

You will notice I'm a bit bewildered as to why the origin of the background frame is what it is. I figured this out by experimenting a bit, but haven't yet got round to working out the exact science/reason behind it. If you know why, I'd appreciate you leaving a comment here, or emailing me, so I can update this page.

Home stretch now, we're almost done. Just draw the overlay and then nab the contents of the context and write it to the device

{{< highlight objective-c >}}
// On to the comp
bgFrame.size.height = h1;
bgFrame.size.width = w1;
// position is where the original image was placed on screen
// in this case the coordinate for the position was the center of the image
// hence the adjustments
bgFrame.origin = CGPointMake(position.x - (bgFrame.size.width/2.0),
                            480 - (bgFrame.size.height/2.0) - position.y);

UIGraphicsBeginImageContext(frame.size);
// draw the background and overlay
[copy drawInRect:bgFrame];
[overlay drawInRect:frame];
[overlay release];

// Get and save the comp image
UIImage *newImage = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
UIImageWriteToSavedPhotosAlbum(newImage, self, nil, nil);
{{< / highlight >}}

And there we go. You can adjust the ``UIImageWriteToSavedPhotosAlbum`` if you need the callback, but I omitted it in this case.
