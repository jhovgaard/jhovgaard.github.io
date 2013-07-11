--- 
layout: post
title: "Deploying: Add Git support to your IIS server"
author: "Jonas Hovgaard"
comments: true
permalink: /deploying-git-support-iis-server/
date: 2012/07/12
description: "Using Kudu you can enable .NET deploying via Git on your local IIS."
---
## Have you heard?

Recently [Microsoft bragged about the new and much more awesome Azure][1].

One of the more cool things they announced is the ability to deploy your applications directly from your git repository (using **git push**) and straight to your Azure hosted website. It loads your master branch, compiles everything and upload the whole thing automatically. Pretty sick!

Now you may think *"Cool story bro, but I host my own IIS!"* - Well, no problem! [David Fowler][2] and [David Ebbo][3] are the creators of Project Kudu - the engine behind automated deployment via Git on Azure. These guys did an awesome job by not limiting this project to Azure, making us able to use it on IIS. Now time to grok it!

## Clone the repo

Point your browser to <https://github.com/projectkudu/kudu> and [clone the master][4] down to your disk.

## Build n' start!

> Below I'll assume that you have IIS 7.x installed on your local machine. If you want to go live with Kudu, simply deploy the **Kudu.Web** project to a site on your production/online IIS as usual.

Now open up the **Kudu.sln** in Visual Studio. Mark the **Kudu.Web** project as active (makes it bold) and hit CTRL+F5.

If everything went well you should now see the Kudu dashboard, looking like this:

<a href="/postfiles/kudu-dashboard.png" target="_blank"><img src="/postfiles/kudu-dashboard.png" alt="Kudu Dashboard" class="maxwidth" /></a>

## Create your first Git enabled site

In your Kudu dashboard:

*   Click the "Create application" link in the top menu.
*   Enter a preferred name for the IIS site (I.e. "jhovgaardnet").
*   Click the blue "Create application" button.

Kudu is now creating a brand new IIS site directly on your IIS (!!) and showing you some geeky stuff:

<a href="/postfiles/kudu-created.png" target="_blank"><img src="/postfiles/kudu-created.png" alt="Kudu website created" class="maxwidth" /></a>

It tells the URL to the Git remote we can push apps to, the URL to the site itself and the service URL which actually is the Git remote from before.

Let's take a quick look in IIS to see what happend:

<a href="/postfiles/kudu-iis.png" target="_blank"><img src="/postfiles/kudu-iis.png" alt="Kudu sites in IIS" class="maxwidth" /></a>

So Kudu is creating 2 sites with a kudu_ prefix. The first is the main site, the one hosting your application. The second is the "git remote site"/service site. Both sites is running on the same application pool.

If we go to the application URL we see a default screen:

<a href="/postfiles/kudu-iis-default-site.png" target="_blank"><img src="/postfiles/kudu-iis-default-site.png" alt="Kudu IIS default site" class="maxwidth" /></a>

## Let's push it!

Alright, let's assume that you want to push a FunnelWeb blog like mine to the newly created site. First clone your fork of FunnelWeb (or whatever application you like) to your disk. Next open up a git shell (if you're using [Github for Windows][5], go to the repository, select **tools** then **open a shell here**). We now need to add a new remote, using the Git URL we got from Kudu. In my case I typed this:

    git remote add kudu http://localhost:63185/jhovgaardnet.git
    

Git now knows about the repository located on our local IIS, making us able to do a simple push of our **master** like this:

    git push kudu master
    

Awesome! If you didn't mess it up, this is what you should see (not necessary the dependency installations of course):

<img src="/postfiles/kudu-deployed.png" class="maxwidth" />

Now updating the application URL shows my fully deployed blog, just as expected. Yes, it's freaking awesome!

## Conclusion

The next thing for you will be to deploy Kudu on your production IIS, setting up sites as Kudu sites, setting correct bindings, etc.

I really think Kudu is swell and I hope to see some interesting forks of Kudu that moves it closer to a continuous integration system with [Github hooks][6], unit testing abilities, etc. TeamCity is cool, but it's expensive and heavy. We need a slick open source .NET alternative and I hope Kudu is the beginning!

What do you think? Can you see the possibilities? Are you going to use Kudu?

Thanks for reading guys!

If you read so far, [why don't you follow me on Twitter? :-)][7]

 [1]: http://www.meetwindowsazure.com/
 [2]: http://weblogs.asp.net/davidfowler/
 [3]: http://blog.davidebbo.com/
 [4]: https://help.github.com/articles/fork-a-repo
 [5]: http://windows.github.com/
 [6]: https://github.com/projectkudu/kudu/tree/githubhook
 [7]: http://twitter.com/#!/jhovgaard