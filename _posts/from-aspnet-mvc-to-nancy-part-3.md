--- 
layout: post
title: "From ASP.NET MVC to Nancy - Part 3"
author: "Jonas Hovgaard"
comments: true
permalink: /from-aspnet-mvc-to-nancy-part-3/
date: 2012/02/23
description: "In this third part we take a look at dependency injection in Nancy using the built-in IoC container TinyIoC."
tags:
- from-aspnet-mvc-to-nancy
---



<a href="http://nancyfx.org" target="_blank"><img src="/postfiles/nancylogo-transparent.png" class="intextimage" /></a>

> This is a series of posts. If you just dropped into this article, you really should [start by reading part 1][1].

Wauw! Nancy is really the thing at the time! Part 1 and 2 have only been online for 5 days and you guys already made 1500 pageviews. I'm impressed! Unfortunately I haven't recieved much feedback. Please make a comment, [hit me on Twitter][2] or email (j at jhovgaard dot dk). I need that feedback to make better posts. Thanks! :-)

Today's Nancy topic is a bit more complex. We're going to work a bit with Dependency Injection. Whether you're an experienced injector, or you think this Dependency Injection is just another buzzword really doesn't matter. This stuff will hopefully be so easy to understand that everybody gets it.

## 1. Installing TinyIoC on our Nancy project

Congratulations! You're successfully installed TinyIoC! Yes, it was already included in Nancy, but let's celebrate anyway.

## 2. Creating a service

Alright, I'm not going to try explain what dependency injection is. If you don't know, try to figure out what's going on here, it's really that simple!

In this example we're creating an **WebApplicationService.cs**. No doubt it's a bad example, but we need to keep things simple. Our new service will have these abilities:

*   Return us a DateTime value for it's creation time.
*   Tell us how many time we asked for it's creation time ;-)

Go ahead and create **WebApplicationService.cs** in our **/Models** folder. Next, paste these methods into your new service:

    public class WebApplicationService
    {
        private readonly DateTime _created;
        private int _totalRequest;
    
        public WebApplicationService()
        {
            _created = DateTime.Now;
            _totalRequest = 0;
        }
    
        public DateTime GetCreationDate()
        {
            _totalRequest++;
            return _created;
        }
    
        public int TimesRequested()
        {
            return _totalRequest;
        }
    }
    

## 3. Time for the mandatory administration section

Let's create a ViewModel for our first action in the *admin* section, by creating a **IndexModel.cs** in /Views/Admin/Models/ (just create the folders). Drop in these properties in the **IndexModel.cs** file:

    using System;
    
    namespace nancytest.Views.Admin.Models
    {
        public class IndexModel
        {
            public DateTime CreationDate { get; set; }
            public int TotalRequests { get; set; }
        }
    }
    

Next up, add our new action to **AdminModule.cs** and use depedency injection to serve the WebApplicationService to our Module:

    using Nancy;
    using nancytest.Models;
    
    namespace nancytest.Modules
    {
        public class AdminModule : NancyModule
        {
            public AdminModule(WebApplicationService webApplicationService) : base("/admin")
            {
                Get["/"] = parameters => {
                    var model = new Views.Admin.Models.IndexModel {
                        CreationDate = webApplicationService.GetCreationDate(),
                        TotalRequests = webApplicationService.TimesRequested()
                    };
    
                    return View["Index", model];
                };
            }
        }
    }
    

Let's take a deeper look at this code. First thing to notice is the expansion of the constructor **: base("/admin")**. This tells Nancy that every action's URL inside this Module starts with /admin. The next big thing is the injection of our WebApplicationService. This is extremely simple, isn't it? We just throw in the objects we want initialized in the parameter list of the constructor and Nancy together with TinyIoC takes care of everything. By default all injections on concrete/base classes will be made as multi-instance, meaning that every call (http request) creates a new instance of the object. If we had used an interface (could be IWebApplicationService) TinyIoC detects this and make the object behave like a singleton (meaning only a single instance of the object, shared over all http requests).

Before we can see all this in action we need (of course) to create the Index view. Create a new HTML page called **Index.cshtml** inside the **/Views/Admin** folder. Make it look like this:

    @inherits Nancy.ViewEngines.Razor.NancyRazorViewBase<nancytest.Views.Admin.Models.IndexModel>
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title></title>
    </head>
    <body>
        <h1>Welcome to the admin page</h1>
        <table>
            <tr>
                <td>Creation date for WebApplicationService:</td>
                <td>@Model.CreationDate</td>
            </tr>
            <tr>
                <td>Total request on the service:</td>
                <td>@Model.TotalRequests</td>
            </tr>
        </table>
    </body>
    </html>
    

Save everything and power it up (CTRL+F5). Point your browser to /admin. If everything is as expected you should see something like this:

<a href="/postfiles/part3-browser.png" target="_blank"><img src="/postfiles/part3-browser.png" class="maxwidth" /></a>

## 4. Defining the lifecycle of WebApplicationService

Okay, try to do some refreshes on our /admin page. You see? The creation date changes. That's a bug combined with the intention of our service. To fix this, we need to tell Nancy that we don't wanna use the default life cycle (which is multi-instance for concrete classes like ours), but rather wants to treat it as a singleton (meaning the object will only be created one time).

To do this we create a custom bootstrapper. This could seem a bit complex, but it really isn't when you get the understanding (you didn't figure that out, huh? Hehe). Create a **MyBootstrapper.cs** class in our **/Models** folder. Make it look like this:

    using Nancy.Bootstrapper;
    using TinyIoC;
    
    namespace nancytest.Models
    {
        public class MyBootstrapper : Nancy.DefaultNancyBootstrapper
        {
            protected override void ApplicationStartup(TinyIoCContainer container, IPipelines pipelines)
            {
                base.ApplicationStartup(container, pipelines);
                container.Register<WebApplicationService>().AsSingleton();
            }
        }
    }
    

I know. Freaking overrides and stuff. But actually most of this stuff is standard code. The only thing to notice here is this line:

    container.Register<WebApplicationService>().AsSingleton();
    

This tells Nancy that our WebApplicationService... Wait, you already get it, right? That's how an awesome framework roll!

Again, save everything and hit CTRL+F5. Point your browser to /admin again and try refresh a couple of times. Do you see the difference? Our service is now only created once, making us able to count the requests and persist the **CreationDate**.

One last thing? Didn't you expect we needed to tell Nancy about our custom bootstrapper? I did. But because Nancy is <span style="text-decoration:line-through">trying to be</span> the best micro MVC framework, it just detects our bootstrapper and uses that instead. No need for Global.asax, just plug and play.

## 5. Conclusion

In this post we learned how to use the integrated dependency injection of Nancy provided by TinyIoC. Also, we learned how to modify objects' lifecycle and last we learned how to create a base url for a NancyModule.

I'm not finished with Nancy yet and I believe there will be 2 or 3 additional parts. If you have some specific topic you want me to cover, let me know :-)

[Click here to download the source for this post.][3]

**Please, if you enjoyed reading this post - share and comment it!**

 [1]: http://jhovgaard.net/from-aspnet-mvc-to-nancy-part-1
 [2]: http://twitter.com/jhovgaard
 [3]: /postfiles/nancytest-part3.zip