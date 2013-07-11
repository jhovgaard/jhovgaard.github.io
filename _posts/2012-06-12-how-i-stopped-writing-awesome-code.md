--- 
layout: post
title: How I stopped writing awesome code
author: Jonas Hovgaard
description: I don't write unit tests, interfaces, use IOC or heavy ORMs. Yet I'm not a brogrammer.
---
## The problem

If writing awesome code is using all the best practices I can find, writing interfaces, unit tests and using top notch IoC containers to control my repositories and services all over my application's different layers - Then I'm not writing awesome code at all!

I've been that guy, the one writing the awesome code, but I stopped. I'm not awesome any more. Instead, I'm productive, I'm so damn productive!

## I don't write unit tests

[ZOMG][1] you may think. You are right, I lose so much safety. I don't know if I break other parts of my code if I change something.

I've spent so much time writing all of these tests, even before I ever wrote the code to test (that should be a "wtf"), and still my boss complained that functions suddenly didn't worked any more.

To make my boss (or your customers) happy again I started investigating what I needed to make sure worked. The answer was the result in the browser. In my test scenario I found out that the important things for my boss was to be sure that these functions worked flawless:

*   Creating a new profile
*   Logging into the new profile
*   Receive welcome message on first login (that was a visible div) 
*   Log out 
*   Login again 
*   Delete profile 
*   Not being able to login after deletion 
*   And everything else you write unit tests for.

My boss or your customers don't care about the status of our unit tests. They look at the result in the browser and so should I.

To overcome this challenge, I downloaded [the Selenium IDE for Firefox][2]. Selenium is neat tool that enables you to run a list of commands directly in a browser. It gives you a list of all failed and succeeded tests (yeah, like your current unit tests). The big ass difference here is that these results is based on the end result (the HTML returned), not something techy stuff going on even before the browser.

I don't wanna teach how to use Selenium (spam me in comments if you want me to), I just want to point out that **my tests tells me what will make my boss sad, and your test tells you what code that breaks**. If you don't agree, please comment - that's the purpose of this post :-)

## I don't do interfaces or use IOC

This started because of the lost connection between my concrete class and the place I ended when I hit F12 in Visual Studio (Go to declaration). This meant that not even Visual Studio understood what class my **IUserService** actually was. How could that be? What is the advantage? In my personal scenario (creating around 20-30 apps every year), I did it to be able to change the concrete class later on. The funny fact is that I never ever did this, except for one time.

I agree, it's absolutely awesome that I can change the concrete type or lifecycle with ease, but for gods sake, I don't even dare to count the hours I have spent creating these abstractions, making me able to do something **I never freaking do**.

Oh, I forgot something. I did spent a lot of time writing interfaces (yeah okay, Resharper did it, but was annoying when I created new methods). But also I spent hours trying to understand what Ninject or StructureMap did with my classes. When I finally understood, I handed over the application to the next developer who also was forced to spent time understanding some IOC.

How hard could it be to initialize every class like this:

    var userService = new UserService();
    

I could even create my classes as static, making them singletons or whatever to each individual class. My co-programmers could understand what was going on immediately, without finding and understanding my **AwesomeIoc.Init()** method.

Sure, again I lose some functionality and safety but seriously, programming is like Diablo 3 - You don't need armor and life, if you kill in one hit!

*For non gamers: I mean you don't need all that functionality, if your productivity gets much higher without it.*

## I don't use regular ORMs

One day I was struggling with NHibernate, trying to make it send the correct query to my SQL server (not MySQL, just mine). Funny thing was - I knew what it should sent. So one thing was obvious - I didn't used NHibernate to help me write complex SQL queries - I'm no brogrammer, so I know how to write those magic strings!

So why did I use it? The answer was: to map the data from my database to my "domain" classes (I hate that word, it's POCOs). Also I used it to track all changes to the POCO's properties.

I started searching Github for better alternatives and I found [Simple.Data][3] and [Dapper][4]. This is extremely simple ORMs. Dapper is the most simple, letting me write true SQL directly in C#, and gives me the results as POCOs. Simple.Data is a bit more complex. You don't write SQL directly, but use chained lamba, to instruct how to query.

Whether I use [Simple.Data][3] or [Dapper][4] I no more struggle with things I already know. I'm not afraid of relations and I don't have to open a Profiler to find out what queries my ORM sends to the SQL server.

Again, with thoughts back to Diablo 3, I gained more damage, but lost some armor. However, my accumulated damage (meaning productivity) is now so high, I just kill it all instantly!

## I do everything to make things more simple

I found out that simple code makes me smile. It makes me able to create new functions extremely fast, without hitting "walls". So whenever I write code, I always try to keep it simple. Not [DRY][5], but simple! If some code gets more complicated when [DRYing][5] it, I don't care about copy pasting (however, most of the time I just rewrite the DRY code, making it simpler).

I don't use Microsoft's big mother MVC framework, but instead [stick with minimalistic and simple frameworks like NancyFX][6]. Whatever I do, I just try to do it more simple.

## The result is the conclusion

My personal result of doing all of this is productivity and better products. I can't tell if I did it all wrong, and that's why I'm writing better code now, but I truly believe that I'm not alone. In fact I think that most of us regular web developers, tend to do the same "mistakes" as I did.

Please, let me hear your thoughts. Do you think I'm just a noob who doesn't understand shit? Is it all too "brogrammerish", or do you actually agree with me? Whatever the reason, thanks for your comment!

If you read so far, [why don't you follow me on Twitter? :-)][7]

## Update 13th June 2012

Because of the big attention this post got, I wanna point out a few things:

I agree, the post was meant to provoke. However the post wasn't written to flame unit tests. They are awesome and extremely important on large projects with a bigger lifespan. However, I believe they aren't always necessary on smaller projects. The intention with this post is to make us all remember to be critical to our tools and remember that "best practices" can't be best in all situations.

The post is targeted to the "average" developer (those we often forget), who's responsible for developing the smaller applications, not necessary visible to the public, but extremely important for the right companies.

I do use TDD, I do use interfaces and IOCs, I do use complex ORMs and big frameworks, but only, and only when I really need it (I kinda try to reduce the noise in my apps). Like said in the comments: It's a question about choosing the right tools for the job.

Oh btw, to avoid further flaming - Yes, you're right, I'm a big noob who writes spaghetti code that isn't maintainable at all.</sarcasm>

## Update 12th July 2012

A few people have responded via blog post. Check em' out here:

*   [Should you write awesome code?][8]
*   [Why Would I Write Unit Tests When I Get Paid by the Hour?][9]
*   [Food for thought][10]

Thanks for joining the discussion guys :-)

 [1]: http://www.urbandictionary.com/zoom.php?imageid=46380
 [2]: http://seleniumhq.org/download/
 [3]: https://github.com/markrendle/Simple.Data
 [4]: http://code.google.com/p/dapper-dot-net/
 [5]: http://en.wikipedia.org/wiki/Don't_repeat_yourself
 [6]: http://jhovgaard.net/from-aspnet-mvc-to-nancy-part-1
 [7]: http://twitter.com/#!/jhovgaard
 [8]: http://endlessobsession.com/blog/should-you-write-awesome-code
 [9]: http://www.codalicio.us/2012/06/why-would-i-write-unit-tests-when-i-get.html
 [10]: http://engineersjourney.wordpress.com/2012/06/13/food-for-thought/