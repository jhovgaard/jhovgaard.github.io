--- 
layout: post
title: "Visual Studio 2012: Web Project is incompatible"
author: "Jonas Hovgaard"
comments: true
permalink: /visual-studio-2012-web-project-is-incompatible/
date: 2012/06/12
description: "Loading a MVC2 web project for the first time in Visual Studio 2012 says 'This project is incompatible with the current edition of Visual Studio.'"
---
## The problem

So you just installed Visual Studio 2012 and hit your .sln file for the very first time. Visual Studio boots up and you feel excited. Then bang, you see something like this:

<a href="/postfiles/vs2012bug.png" target="_blank"><img src="/postfiles/vs2012bug.png" alt="VS2012 MVC bug" class="maxwidth" /></a>

I've also seen errors like these:

*   The application which this project type is based on was not found. 
*   This project is incompatible with this version of visual studio 2012.
*   This project is incompatible with the current edition of Visual Studio.
*   Incompatible. The project will load in the background.
*   Load failed. The project requires user input.

The reason is that Visual Studio 2012 doesn't support ASP.NET MVC 1 or 2.

## The solution

Download and run the [ASP.NET MVC 3 Application Upgrader][1] on your solution. After doing that you should be able to open your solution.

If your project isn't using MVC or you can't get the upgrader to run, try [following Eilon's post][2].

**Thanks to Eilon for pointing out the correct solution in comments.**

 [1]: http://aspnet.codeplex.com/releases/view/59008
 [2]: http://weblogs.asp.net/leftslipper/archive/2009/01/20/opening-an-asp-net-mvc-project-without-having-asp-net-mvc-installed-the-project-type-is-not-supported-by-this-installation.aspx