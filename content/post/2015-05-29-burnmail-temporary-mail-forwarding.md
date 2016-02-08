---
comments: true
date: 2015-05-29T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-05-29 09:31:41 +0100
share: null
tags:
- email
- mail
- forwrding
- antispam
- burnmail
- imadethis
title: 'Burnmail: temporary mail forwarding'
url: /2015/05/29/burnmail-temporary-mail-forwarding/
---

A while back I got curious about how easy it would be to setup a temporary
email forwarding service, akin to [Meltmail](https://meltmail.com/). So I got 
to it and made it. 

In essence you will create a temporary email address that will forward all
mail it receives to your inbox. Once it expires, it drops all incoming mail.

It's pretty much ready now, hence why I am posting about it.

The differences between Burnmail (yeah, best I could do for now) and other 
services is:

* forwarding can be expired by time or by number of received mails
* all tasks are accomplished through email. There's no web interface
* forwarding address is based on your original address with a short, random
  string appended, to make it easy to remember. 
* addreses are also easy to type on a mobile device, 
  as the extra string consists of digits and lowercase chars only.
* commands are forgiving and short

The plan is to clean up the code and then release that on Github, but in the 
meantime feel free to make use of it. 

Here's how to use it:

## Creating a new Burnmail address

To create a new Burnmail address, send an email to <burn@kthnxbai.co.uk>,
from the address that you want your Burnmail forwarded to. In the subject
you need to specify the expiration term. The following commands are valid:

    * time [n] - n is 3, 6, 12, 24, or 48 hours. It will automatically
      select the closest value to the one you specify.
    * uses [n] - n is between 1 and 30. It will be automatically capped to 30.

By using the `time` command you will create an address that expires after `n`
hours. The `uses` command creates an address that expires after `n` emails have
been received through that address.

Once your forwarder has been set up, you will receive a confirmation email with
your Burnmaill address. Check your spam folder just in case.

## Getting a list of emails

To get a list of your current Burnmail addresses for an email address,
send an email to <burn@kthnxbai.co.uk> with the subject `stat`. 
An email listing all your current forwarders for that address and their 
expiration terms will be sent to you.

## Deleting Burnmail

To delete a Burnmail send an email to <burn@kthnxbai.co.uk> with the subject
`kill address`, where `address` is the burnmail address to kill. 


## Thoughts?

That's it for now. Make sure to check your spam folder if you aren't 
receiving any mail.

If you have any suggestions for features, or notice something broken,
please let me know.

Also bear in mind that it's early days so there might be bugs.
