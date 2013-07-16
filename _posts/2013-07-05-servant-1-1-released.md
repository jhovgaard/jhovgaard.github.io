--- 
layout: post
title: "Servant 1.1 released"
author: "Jonas Hovgaard"
comments: true
description: "Servant 1.1 includes a lot of fixes and new features. I'll go through them in this post."
---

<a href="http://www.servant.io" target="_blank">
	<img src="/postfiles/servant-1.1-banner.png" alt="Servant for IIS" class="banner" />
</a>

At last, Servant 1.1 is now released and publicly available. The release of the first version was a very overwhelming experience. I received a lot of wonderful feedback from people who loved what Servant brought to the IIS. At the same time I was also taught that developers simply don't accept bugs and glitches in production software. Servant 1.1 is all about that - bugfixes and performance improvements (boring stuff, I agree). 

I'll walk through the most exciting changes, starting with new features.

## HTTPS support
I'm introducing HTTPS support for the Servant engine itself. That means that you're now able to change the URL of Servant to for example `https://servant.example.com`. When Servant detects HTTPS in the entered URL a self-signed certificate will be created and installed on the server. You will see the nasty browser warning telling you that the site is not safe. That's expected. To avoid it you would need to buy a SSL certificate for Servant. I don't expect requests for this feature, but if you need it, please add/vote the idea at <a href="http://servant.uservoice.com/" target="_blank">the UserVoice site</a>.

## Automatic error reporting and usage statistics
I had a hard time solving users' problems. To overcome this, exceptions in Servant is now being sent to Raygun.io. Also all traffic is tracked using Google Analytics. Everything is totally anonymous. 

For Google Analytics Servant uses <a href="https://support.google.com/analytics/answer/2763052?hl=en" target="_blank">IP Anonymization</a> to hide your servers IP and <a href="https://developers.google.com/analytics/devguides/collection/analyticsjs/domains" target="_blank">disable cookieDomain</a> to hide your hostname.

For error reporting Servant isn't sending any personal data either. Reports are limited to machine informations (hardware, OS info), URL (without hostname) and the stacktrace of the  thrown exception.

If you don't like any data leaving your server you can disable it all under settings or during the installation.

## Support for virtual applications
<a href="/postfiles/servant-1.1-virtualapplications.png" target="_blank">
	<img src="/postfiles/servant-1.1-virtualapplications.png" class="intextimage" />
</a>
This is step 1/2 of the most requested feature for Servant: support for virtual applications under sites. A lot of people found Servant completely useless because their IIS setup was completely depending on virtual applications and directories. Applications are now supported. Directories is coming, I just need to figure out a smart way to visualize it.

<div class="clear"> </div>

## Intelligent exception parsing
<a href="/postfiles/servant-1.1-intelligentexceptionparsning.png" target="_blank">
	<img src="/postfiles/servant-1.1-intelligentexceptionparsning.png" class="intextimage" />
</a>
Servant now parses exception more intelligently. Instead of showing a huge list of `HttpException` for example, exceptions are now being parsed and the top exception message is shown. The message is gathered from the stacktrace's first line, exactly how IIS shows errors.

<div class="clear"> </div>

## IIS Default Web Site were missing
Yes, the biggest bug on Servant 1.0 has been fixed. A lot of users experienced that the site created by IIS on installation simply wasn't available in Servant. It turned out that this site contained special protocol bindings such as net.tcp. Servant only supports HTTP/HTTPS. I've fixed it by ignoring all other protocols meaning the default web site is now available from Servant too. How could this get through the beta I ask myself...


So here you go, a stable release of Servant! The release includes a lot of additional bugfixes which isn't mentioned in this post. Of course there will still be more bugs and glitches but hopefully few enough to overcome.

## What about Servant 1.2, any new features?
What a great question! Servant 1.1 was in no doubt hard work, simply because it was boring (isn't bugfixing always?). Developing Servant 1.2 is super fun, because it's all about new features, great features actually. 

The feature 1.2 will be remembered for is... Ready? <b>Full blown totally automated full stack deployment!</b> Yes! Servant will download your source from a Git repository, it will build your project, deploy it to not one but all of your Servant-enabled servers. It will even warm up each server using proxy (to support loadbalancing setups) and at last it rolls back the deploy if a server returns anything but 200 OK.

So that's it for now. Go <a href="http://www.servant.io" target="_blank">download Servant 1.1</a> and test it out yourself. 

What do you think about this release? Are you excited about the upcoming deploy feature?

Have a great day!

Jonas

