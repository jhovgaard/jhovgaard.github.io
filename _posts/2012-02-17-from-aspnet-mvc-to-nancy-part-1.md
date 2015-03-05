--- 
layout: post
title: "From ASP.NET MVC to Nancy - Part 1"
author: "Jonas Hovgaard"
comments: true
permalink: /from-aspnet-mvc-to-nancy-part-1/
description: "This post is the first in a series of post on getting started with Nancy (NancyFX) as an experienced ASP.NET MVC developer."
tags:
- from-aspnet-mvc-to-nancy
---

<a href="/postfiles/nancylogo-transparent.png" target="_blank"><img src="/postfiles/nancylogo-transparent.png" class="intextimage" /></a>
If you have ever been in the world of Ruby you would know about this little framework called Sinatra. In it's core concept it's a MVC framework, just extremely simple and fast.

Nancy is a Micro .NET framework inspired by Sinatra. If you ever felt that ASP.NET MVC is too heavy, too clumsy and stands in your way, Nancy is definitely made for you.

I'm an average .NET developer. I work for a middle-sized company where we build a lot of applications in ASP.NET MVC 3 and because of that, this series of posts will be designed for exactly that purpose: moving from ASP.NET MVC 3 to Nancy.

## Creating the application

Grap a new instance of Visual Studio 2010 and create a new **Empty ASP.NET Application**. Yes that is what we want. Personally this was my first time using this template.

Next we're going to install Nancy using Nuget. Right click *References* in the solution explorer and click *Manage Nuget Packages...*. Search for Nuget and install **Nancy.Hosting.Aspnet** and **Nancy.Viewengines.Razor**:

<a href="/postfiles/part1-nuget.png" target="_blank"><img src="/postfiles/part1-nuget.png" class="maxwidth" /></a>

This will install Nancy including Razor support. Also it will add Razor as a BuildProvider in your web.config, enabling Visual Studio to support IntelliSense in our views.

## Time to get nasty - the good way...

Yay! We're ready! You remember controllers in MVC3 right? In Nancy we got something like that, it's just called Modules, but seen from our eyes it's the same thing.

*   Create a new folder called **Modules**
*   Create a new class called **HomeModule.cs**

Again, in MVC3, inside our controller, we had a massive amounts of "Actions" defined as methods. This is not nearly as complex in Nancy. Let me create an Index-action for you:

    public class HomeModule : NancyModule
    {
        public HomeModule()
        {
            Get["/"] = parameters => {
                return View["Index"];
            };
        }
    }
    

zOMG! See what's going on here? First I create a "parameterless constructor". Fancy word, but this is actually just the **public HomeModule()** part. So inside our constructor (if you're in for seh winz you call this a "ctor") I both define the route (the ones we know from Global.asax) and the action it self. At last I return a view called Index.

## Creating the "Index" view

Next up is our Razor Index view. Create a new folder called **Views**. In here create another folder called **Home**. Now right click the folder and create a HTML page called **Index.cshtml**. Put in some motivating text and hit CTRL+F5.

Now two things can happen. You'll see a scary YSOD page telling you that the page "/Views/Home/Index.cshtml couldn't be served, or you will see your motivating text, served directly from Nancy. If you got the scary one, point your browser to the root of the application. If the last I would like to congratulate you with your first Nancy application :-)

### [This was part 1. Wanna know something about Nancy's ModelBinding, ViewModels and so on? Go for part 2 :-)][1]

[Click here to download the source for this post.][2]

**Please, if you enjoyed reading this post - share it!**

 [1]: http://www.jhovgaard.com/from-aspnet-mvc-to-nancy-part-2/
 [2]: /postfiles/nancytest-part1.zip