--- 
layout: post
title: "C# Generating new priority/index order"
author: "Jonas Hovgaard"
comments: true
permalink: /c-generating-new-priorityindex-order/
description: "I just came by one of those scenarios where you need to generate new priority indexes because the user wants to reorganize some list."
---
Sup!

I just came by one of those scenarios where you need to generate new priority indexes because the user wants to reorganize some list.

Normally I iterate through my list of objects and then calculate the new priority/order number on each loop (by doing difference stuff whether the new index is positive nor negative).

But this time I freestyled a little with NHibernate and the List generic. The user gets a list of “pages” that can be reordered.

I came out with this awesome method:

    public void SetNewPagePriority(int pageId, int newPriority)
    {
        newPriority--; //Abstract the user from zero-index
        var page = _pageService.GetById(pageId);
        if(page == null) return;
    
        var pagesInNewOrder = page.Test.Pages.OrderBy(x =&gt; x.Priority).ToList();
        pagesInNewOrder.RemoveAt(pagesInNewOrder.IndexOf(page));
        pagesInNewOrder.Insert(newPriority, page);
    
        //Sets the new priority in the database using the current index of the loop.
        for(var i = 0; i &lt; pagesIsNewOrder.Count; i++)
            pagesInNewOrder[i].Priority = i;
    }
    

The code simply removes the object that needs to be reorganized from the list (NHibernate is clever enough to not delete the object from the database at this time), and then pushes it into the new location.

After that I simply iterate the newly reorganized list and sets the current index. NHibernate will then commit the changes for me.