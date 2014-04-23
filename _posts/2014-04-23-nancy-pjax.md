---
layout: post
title: "How to use PJAX with Nancy"
author: "Jonas Hovgaard"
comments: true
description: "In this post I'll show how to use PJAX in a Nancy application."
---
## What is PJAX and why?

Today most web applications use a layout page, because we need to maintain the same HTML code over multiple pages. This HTML could be the header, footer and so on.

When the user clicks around our application they need to download this header and footer again and again and again.

Some developers use AJAX to download data from the server, and then manipulate with the DOM using Javascript to show this new data. Some use big Javascript frameworks and call their application a SPA. This is clever, but isn't compatible with search engines and non-javascript users. So developers is forced to support two "versions" - a AJAX version and a pure HTML version. Cumbersome and expensive if you care about SEO.

[PJAX](https://github.com/defunkt/jquery-pjax) fixes this issue. When enabled it will tell the server if it should include the layout HTML or not. If Javascript is enabled (not a crawler) there's no reason to include the layout. If not, the server returns the complete HTML. 

> Quote from [https://github.com/defunkt/jquery-pjax](https://github.com/defunkt/jquery-pjax): pjax works by grabbing html from your server via ajax and replacing the content of a container on your page with the ajax'd html. It then updates the browser's current url using pushState without reloading your page's layout or any resources (js, css), giving the appearance of a fast, full page load. But really it's just ajax and pushState.

So to get to the point: PJAX is a plug-and-play plugin that reduces bandwidth usage, increase performance and is fully compatible with non-javascript users.

Also, please notice that PJAX is also part of the YUI library: [http://yuilibrary.com/yui/docs/pjax/](http://yuilibrary.com/yui/docs/pjax/), however I will use the standalone version in this demo.

## Setting up the demo

I'll go through setting up the demo application real quick, since this article isn't about Nancy, but PJAX. If you're new to Nancy I suggest you start by reading [my getting started on Nancy blog posts](http://www.jhovgaard.com/from-aspnet-mvc-to-nancy-part-1).

Required Nuget packages for the demo is the following:

	install-package nancy.hosting.aspnet
    install-package nancy.viewengines.razor
	install-package jquery
    install-package jquery.pjax

After installing Nuget packages I move the script folder into another folder called "Content" only to follow Nancy conventions.

A HomeModule containing a set of actions: 

	using Nancy;
	
	namespace NancyPjaxDemo
	{
	    public class HomeModule : NancyModule
	    {
	        public HomeModule()
	        {
	            Get["/"] = p =>
	            {
	                return View["Index"];
	            };
	
	            Get["/first/"] = p =>
	            {
	                return View["First"];
	            };
	
	            Get["/second/"] = p =>
	            {
	                return View["Second"];
	            };
	        }
	    }
	}

A _Layout.cstml view:

	@using System
	<!DOCTYPE html>
	
	<html>
	<head>
	    <meta name="viewport" content="width=device-width" />
	    <title>@ViewBag.Title</title>
	</head>
	<body>
		Layout loaded: @DateTime.Now
	    <div id="container">
	        @RenderBody()
	    </div>
	</body>
	</html>

A Index.cshtml view:
	
	@{
	    Layout = "_Layout.cshtml";
	}

	<h1>Index page</h1>

	<ul>
	    <li><a href="/first/">First link</a></li>
	    <li><a href="/second/">Second link</a></li>
	</ul>

And at last a First.cshtml and Second.cshtml:

	@{
	    Layout = "_Layout.cshtml";
	}
	
	<h1>Welcome to second page!</h1>
	
	<a href="/">Go back to the index page</a>

My solution explorer now looks like this:

<img src="/postfiles/nancy-pjax-solutionexplorer.png" class="maxwidth" />

## Setting up the Javascript

Perfect, our demo is ready. Time to set up PJAX. First, let's update the layout page to include jQuery and the PJAX library. I'll add the following two lines just before my `</body>` tag:

	<script src="~/Content/Scripts/jquery-2.1.0.min.js"></script>
    <script src="~/Content/Scripts/jquery.pjax.js"></script>

Next I'll tell PJAX to use my `#container` element which you can find in my _Layout.cshtml page:

	<script>
        $(document).pjax('a', '#container', { timeout: 1000 });
    </script>

This tells PJAX that whenever a user clicks a link, the content should be loaded into `#container`. The exact same behavior as Razor's `@RenderBody()`.

To summerize, my `_Layout.cshtml` page now looks like this:

	@using System
	<!DOCTYPE html>
	
	<html>
	<head>
	    <meta name="viewport" content="width=device-width" />
	    <title>@ViewBag.Title</title>
	</head>
	    <body>
	        Layout loaded: @DateTime.Now
	        <div id="container">
	            @RenderBody()
	        </div>
	    
	        <script src="~/Content/Scripts/jquery-2.1.0.min.js"></script>
	        <script src="~/Content/Scripts/jquery.pjax.js"></script>
	        <script>
	            $(function() {
	                $(document).pjax('a', '#container', { timeout: 1000 });
	            });
	        </script>
	    </body>
	</html>


Now when I click through my application I will see that PJAX now sends each request using AJAX instead of regular pageloads.

<img src="/postfiles/nancy-pjax-firstrequest.png" class="maxwidth"/>

You'll also notice the `X-PJAX: true` header (marked with yellow). This tells our application that this request should not contain the layout HTML, only the new HTML for the requested page.

Because PJAX detects that we are returning the Layout HTML again, it fires a regular pageload. What we need to do is tell Nancy to forget about the Layout page if we see this header.

## Setting up Nancy for PJAX

First, we need to provide all views a bool indication whetever the request is coming from PJAX or not. I'll do this by using the Before hook in Nancy in my `HomeModule`:

	Before += ctx => {
        ViewBag.IsPjax = Request.Headers.Keys.Contains("X-PJAX");
        return null;
    };

This will make my HomeModule look like this:

	using System.Linq;
	using Nancy;
		
	namespace NancyPjaxDemo
	{
	    public class HomeModule : NancyModule
	    {
	        public HomeModule()
	        {
	            Before += ctx =>
	            {
	                ViewBag.IsPjax = Request.Headers.Keys.Contains("X-PJAX");
	                return null;
	            };
	
	            Get["/"] = p =>
	            {
	                return View["Index"];
	            };
	
	            Get["/first/"] = p =>
	            {
	                return View["First"];
	            };
	
	            Get["/second/"] = p =>
	            {
	                return View["Second"];
	            };
	        }
	    }
	}

Great, now `ViewBag.IsPjax` can tell is if the request is coming from PJAX. Now we simply need to add an if-sentence to our Views, to make them able to ditch the layout if request is coming from PJAX:
	
	@{
	    Layout = ViewBag.IsPjax ? null : "_Layout.cshtml";
	}

I'll do this everywhere I set the Layout in Razor. In my demo it will be Index.cshtml, First.cshtml and Second.cshtml.

Now when I click around my application, I'll see that the "Layout loaded" timestamp doesn't change, but the content and URL does! If I disable Javascript, everything still works as any other regular webpage.

<img src="/postfiles/nancy-pjax-pjaxrequests.png" class="maxwidth" />

## We're done!

That's it, one complete PJAX application. You should notice that when you write Javascript for PJAX application it is very important that all events is attached to the `#container` object. For example to handle a click event on some button, you should __NOT__ do this:

__Wrong way:__

	$('#MyButton').click(function() {});

__Right way:__

	$('#container').on('click', '#MyButton', function() {});

The reason is that PJAX will inject the content of your pages into the existing DOM. The "wrong example" only hooks up to instances of my `#MyButton` on intilization, but PJAX injects #MyButton after initilization. 

I hope you see the power of PJAX. I really do think this is the next big thing. I already use it in production and it is a huge timesaver and have really changed the way I do web applications. To be honest I tend to write much more C# than Javascript now, after PJAX. Simplicity at it's best. PJAX have my vote for the no. 1 web technology of 2014.

The full source of this demo is available at GitHub: [https://github.com/jhovgaard/nancy-pjax-demo](https://github.com/jhovgaard/nancy-pjax-demo).

Please [leave a comment]({{ page.url }}#comments), I love feedback :-)

