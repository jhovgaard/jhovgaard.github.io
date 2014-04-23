---
layout: post
title: "Enable CSRF Protection in Nancy"
author: "Jonas Hovgaard"
comments: true
description: "In this post I explain how to enable CSRF Protection of on NancyFX Nancy."
---

<img src="/postfiles/nancy-csrf.png" alt="Enable CSRF Protection in Nancy" class="banner" />

## What is CSRF?

Alright, I'll make this quick since most of you will know this already. 

CSRF stands for Cross-Site Request Forgery. What it bascially means is, that you make one website do a request to another website, without letting the user know. 

To ensure that a request is coming from your own website, we have to push a unique string into a hidden input in every form that we post - this is called the `AntiForgeryToken`.


If you want to know more about CSRF go check out [the Wikipedia page](http://en.wikipedia.org/wiki/Cross-site_request_forgery), it's actually very well explained.

## CSRF and Nancy
Like any other functionality in Nancy, you will __not__ get a full blown plug-and-play solution. As always Nancy is all about lightweight and total freedom, so we have to make a little effort:

1. Enable CSRF support in the `Bootstrapper`.
2. Insert the `AntiForgeryToken` in every `<form>` element of our application.
3. Validate the posted CSRF token using the built-in Nancy Helper method `ValidateCsrfToken()`.
4. Handle when Nancy can't accept a given token (or if none is provided).

Alright let's go.

## 1. Enable CSRF support
Create a `NancyDefaultBootstrapper` class or use your existing. Add the following line to your `ApplicationStartup()` method:

	Nancy.Security.Csrf.Enable(pipelines);

My bootstrapper now looks like this:	


	using Nancy;
	using Nancy.Bootstrapper;
	using Nancy.TinyIoc;
	
	namespace NancyCsrfDemo
	{
	    public class Bootstrapper : DefaultNancyBootstrapper
	    {
	        protected override void ApplicationStartup(TinyIoCContainer container, IPipelines pipelines)
	        {
	            base.ApplicationStartup(container, pipelines);
	            Nancy.Security.Csrf.Enable(pipelines);
	        }
	    }
	}


## Add the AntiForgeryToken to the form
Alright, you should already have some views in your application, one of them containing some form. In my example I'm using Razor as the viewengine.

To add the AntiForgeryToken to my form I simply add `Html.AntiForgeryToken()` to anywhere inside my `<form>` element.

My view looks like this:

	<!DOCTYPE html>
	
	<html>
	<head>
	    <meta name="viewport" content="width=device-width" />
	    <title></title>
	</head>
	    <body>
	        <h1>Welcome to Nancy CSRF Demo, @ViewBag.Name</h1>
	        <form action="/" method="POST">
	            Name:<br/>
	            <input type="text" name="name" value="@ViewBag.Name" />
	            <input type="submit" />
	            @Html.AntiForgeryToken()
	        </form>
	    </body>
	</html>

The generated value of `@Html.AntiForgeryToken()` looks something like this (the string will change for each request):

	<input type="hidden" name="NCSRF" value="AAEAAAD/////AQAAAAAAAAAMAgAAAD1OYW5jeSwgVmVyc2lvbj0wLjIyLjIuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1udWxsBQEAAAAYTmFuY3kuU2VjdXJpdHkuQ3NyZlRva2VuAwAAABw8UmFuZG9tQnl0ZXM+a19fQmFja2luZ0ZpZWxkHDxDcmVhdGVkRGF0ZT5rX19CYWNraW5nRmllbGQVPEhtYWM+a19fQmFja2luZ0ZpZWxkBwAHAg0CAgAAAAkDAAAAOrVPNrYs0YgJBAAAAA8DAAAACgAAAAI4qmisW7sYu2FlDwQAAAAgAAAAAsdnVjuuU1fzpb58LEjF1pcK98u9ENMjG0viyxLpvlv/CwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA="/>


## Validate the posted token
When the user clicks my submit button their browser will send my application the value of the AntiForgeryToken/CSRF token. We need to handle this.

Nancy have a builtin method called `ValidateCsrfToken()`. The method will automatically lookup Request.Form.Ncsrf and throw a `CsrfValidationException` if the token is not valid.

The exception will end up showing a Error 500 page to the user, which isn't fortunately. To overcome this we catch this exact exception and show some other result. Here's my NancyModule:

	using Nancy;
	using Nancy.Security;
	
	namespace NancyCsrfDemo
	{
	    public class HomeModule : NancyModule
	    {
	        public HomeModule()
	        {
	            Get["/"] = p =>
	            {
	                return View["Index"];
	            };
	
	            Post["/"] = p =>
	            {
	                try
	                {
	                    this.ValidateCsrfToken();
	                }
	                catch (CsrfValidationException)
	                {
	                    return Response.AsText("Csrf Token not valid.").WithStatusCode(403);
	                }
	                
	                ViewBag.Name = Request.Form.Name;
	                return View["Index"];
	            };
	        }   
	    }
	}   

As you can I simply return a text response to the user, telling them the token is invalid. Also I'm returning a 403 Forbidden status code instead of 500 Internal Error. You may choose another solution :-)

## Simple as that!
That is how CSRF protection works using Nancy. Please notice I'm not showing best practice here. You should move the try-catch into a BaseModule (maybe use [the Before hook](https://github.com/NancyFx/Nancy/wiki/The-before-and-after-module-hooks)), there's no need to copy/paste that code into every action of your application.

If I helped you out, please [leave a comment]({{ page.url }}#comments), I love feedback :-)

The full example/demo is available at GitHub:
[https://github.com/jhovgaard/nancy-csrfdemo](https://github.com/jhovgaard/nancy-csrfdemo)