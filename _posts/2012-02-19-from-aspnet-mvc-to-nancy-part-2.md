--- 
layout: post
title: "From ASP.NET MVC to Nancy - Part 2"
author: "Jonas Hovgaard"
comments: true
description: "This second part digs into the use of IntelliSense combined with Nancy by doing simple modelbinding on GET/POST actions."
tags:
- from-aspnet-mvc-to-nancy
---
<a href="/postfiles/nancylogo-transparent.png" target="_blank"><img src="/postfiles/nancylogo-transparent.png" class="intextimage" /></a>

> This is a series of posts. If you just dropped into this article, you really should [start by reading part 1][1].

Alright. Note to my self: Never say "tomorrow I will..." in a blog post unless the stuff to do already is done :P Sorry for the delay but here we go again.

## Nancy Modelbinding - moving data from our module to the view

Today we're going to move an object from our module/controller to the view. Next we're going the other way, posting a new object from the view and back to the module.

## Creating a **BlogPost** object

First, let's create an actual object to return to our View. We make a classic **BlogPost** object in a folder called Objects:

    using System.Collections.Generic;
    namespace nancytest.Objects
    {
        public class BlogPost
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }
            public List<string> Tags { get; set; }
    
            public BlogPost()
            {
                Tags = new List<string>();
            }
        }
    }
    

## Return the new **BlogPost** object to our view

Start by extending our "/" action to create a new **BlogPost** object and return it to our Index view:

    using Nancy;
    using nancytest.Objects;
    
    namespace nancytest.Modules
    {
        public class HomeModule : NancyModule
        {
            public HomeModule()
            {
            Get["/"] = parameters => {
                var blogPost = new BlogPost {
                    Id = 1,
                    Title = "From ASP.NET MVC to Nancy - Part 2",
                    Content = "Lorem ipsum...",
                    Tags = {"c#", "aspnetmvc", "nancy"}
                };
    
                return View["Index", blogPost];
            };
            }
        }
    }
    

Notice that I simply pass in my object as the second parameter when I return my view.

## Preparing the view for **BlogPost** and enabling IntelliSense

Open up **Views/Home/Index.cshtml**. We need to tell Razor what model it should use. In ASP.NET MVC we would do it like this:

    @model nancytest.Objects.BlogPost
    

Unfortunately [Microsoft made some bad decisions designing Razor][2]. Because of that we're not able to do this using Nancy. Instead insert this line of code in the very top of the page:

    @inherits Nancy.ViewEngines.Razor.NancyRazorViewBase<nancytest.Objects.BlogPost>
    

This tells Razor about the model - Nancy Style!

Take it for a spin by adding some @Model usage on **Views/Home/Index.cshtml**:

    @inherits Nancy.ViewEngines.Razor.NancyRazorViewBase<nancytest.Objects.BlogPost>
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title></title>
    </head>
        <body>
            <table>
                <tr>
                    <td>Id:</td>
                    <td>@Model.Id</td>
                </tr>
                <tr>
                    <td>Title:</td>
                    <td>@Model.Title</td>
                </tr>
                <tr>
                    <td>Content:</td>
                    <td>@Model.Content</td>
                </tr>
                <tr>
                    <td>Tags:</td>
                    <td>@string.Join(", ", Model.Tags)</td>
                </tr>
            </table>
        </body>
    </html>
    

Did you just freaking notice how natural that felt for IntelliSense? Or do you instead wondering why I'm so psyched about an old feature in Visual Studio? Well, the reason for that is in the early versions of Nancy, IntelliSense wasn't available and Visual Studio didn't recognize the .cshtml as Razor view pages, so even simple HTML made VS throw errors. These days are over and Nancy works, as you can see, like a magical unicorn in VS.

Do some CTRL+SHIFT+B and CTRL+F5 magic and you should see something like this:

<a href="/postfiles/part2-view.png" target="_blank"><img src="/postfiles/part2-view.png" class="maxwidth" /></a>

## Time for the post action. Let's pretend we create a **BlogPost**

Jump right back into **HomeModule** and add a new action mapped to /new/ (GET) like this:

    Get["/new/"] = parameters => {
        return View["New"];
    };
    

Next create a **New.cshtml** in the folder **Views/Home**. Again we declare it to use our BlogPost object as model:

    @inherits Nancy.ViewEngines.Razor.NancyRazorViewBase<nancytest.Objects.BlogPost>
    

Create a simple form, posting to /new/:

    @inherits Nancy.ViewEngines.Razor.NancyRazorViewBase<nancytest.Objects.BlogPost>
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title></title>
    </head>
        <body>
            <form action="/new/" method="post">
                <table>
                    <tr>
                        <td>Id:</td>
                        <td><input type="text" name="Id" value="@Model.Id" /></td>
                    </tr>
                    <tr>
                        <td>Title:</td>
                        <td><input type="text" name="Title" value="@Model.Title" /></td>
                    </tr>
                    <tr>
                        <td>Content:</td>
                        <td><input type="text" name="Content" value="@Model.Content"/></td>
                    </tr>
                    <tr>
                        <td>Tags:</td>
                        <td><input type="text" name="Tags" value="@string.Join(",", Model.Tags)"/></td>
                    </tr>
                    <tr>
                        <td colspan="2"><input type="submit" value="Add new"/></td>
                    </tr>
                </table>
            </form>
        </body>
    </html>
    

Next up is creating the action handling the POST coming from this form. Jump back into our **HomeModule** and add a new action:

    Post["/new/"] = paramters => {
        var blogPost = this.Bind<BlogPost>();
        // Redirects the user to our Index action with a "status" value as a querystring.
        return Response.AsRedirect("/?status=added&title=" + blogPost.Title);
    };
    

Also, don't forget to import the **Nancy.ModelBinding** namespace in the top of our class like this:

    using Nancy.ModelBinding;
    

Awesome! Here we throw the user back to our Index page (root) including some stuff in the querystring for our convenience.

Nancy is just getting more and more fantastic. Did you notice the extremely fast build times and the native feeling when you just do raw web development?

That's all folks! In post 3 I'll start by looking into the IoC container [TinyIoC][3] which is by default integrated in Nancy.

### [Still psyched about Nancy? Click here to go directly to part 3! ][4]

[Click here to download the source for this post.][5]

**Please, if you enjoyed reading this post - share and comment it!**

 [1]: http://jhovgaard.net/from-aspnet-mvc-to-nancy-part-1
 [2]: http://groups.google.com/group/nancy-web-framework/browse_thread/thread/ffe653b84d6be3f6
 [3]: https://github.com/grumpydev/TinyIoC
 [4]: http://jhovgaard.net/from-aspnet-mvc-to-nancy-part-3
 [5]: /postfiles/nancytest-part2.zip