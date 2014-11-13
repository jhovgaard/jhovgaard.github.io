---
layout: post
title: "Servant.io available in public preview"
author: "Jonas Hovgaard"
comments: true
description: "Servant.io - a hosted manager for your web server"
---

<a href="https://www.servant.io" target="_blank">
  <img src="https://www.servant.io/Content/images/screens.png" class="banner" />
</a>

## What have changed?

Some of you may already know about Servant for IIS - a lightweight web interface for IIS (Which is still available at GitHub of course). <a href="https://www.servant.io" target="_blank">Servant.io</a> is the next step in making management of web servers even easier and much more awesome.

What we have done is simply move Servant to a hosted cloud environment like you know it from Boundary, New Relic and services like that. We have developed a small client/agent to be able to communicate with your servers (<a href="https://github.com/jhovgaard/servant" target="_blank">the source is open and available for everyone</a>).

## <a href="https://www.servant.io" target="_blank">Servant.io</a> brings new features

We're working like crazy apes to bring new features. Below I'll list the features we have pushed live so far.


* **Web farm support:** One of the biggest feature requests on Servant for IIS was multi-server/web farm support. <a href="https://www.servant.io" target="_blank">Servant.io</a> now fully supports IIS web farm setups. You simply select one server to be your primary configuration, and Servant will take care of keeping your sites in sync.

* **Deployment:** We bring automated deployment to both single servers and web farms. You upload a zip file containing you site, and Servant will extract the site on every server. Servant always extract to a new folder when deploying, and it doesn't change the path until the site is completely ready. **This dramatically reduces downtime on deployment.**

* **Plug and play:** One super important thing for us is that it won't be annoying to install the Servant Client. To overcome firewall issues we communicate entirely on port 443 (HTTPS). We use WebSockets (SignalR actually) to send commands to your server so you don't have to open for incoming traffic. All communication is of course securely done via SSL.

* **Security:** Everyone knows that no software is completely secure. We do a lot to keep Servant super safe, but if something bad happens we introduced a completely automated and silent updater. This makes us able to push security fixes instantly to your server. Everything is happening without any effort required by you. Your Servant Client will always be up to date.


## The future

We have endless numbers of features that we want to introduce, but right now this is what we have in the official pipeline.

* **Drag and drop deployment:** Simply drag a folder from your disk onto your site in Servant, and we will handle zipping and deploying.

* **Deploy from Visual Studio/IDE:** We're developing support for deployment (server + web farm) via FTP. This will enable you to deploy to all your servers directly from Visual Studio/your IDE.

* **Powershell/command line access:** You will be able to execute commands and powershell on your server directly from the <a href="https://www.servant.io" target="_blank">Servant.io</a> web interface. Executing commands on every server in a farm will also be possible.

## Pricing

<a href="https://www.servant.io" target="_blank">Servant.io</a> is currently in public preview which is completely free for everyone. When we feel like the product is worth paying for, we will introduce a price model. 

I promise prices will be fair and will match the size of your organization. Also we will have a discount for students + **completely free for open source organizations!**


<br/>


That's all for now. I hope you're as excited as we are. We want to bring the best web server manager ever.


Please [leave a comment]({{ page.url }}#comments), I love feedback :-)
