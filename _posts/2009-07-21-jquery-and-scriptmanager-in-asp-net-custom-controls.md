---
id: 41
title: jQuery and Scriptmanager in asp.net Custom Controls
date: 2009-07-21T17:00:00+00:00
author: Michael
layout: post
guid: https://thepensiveprogrammer.com/index.php/2009/07/21/jquery-and-scriptmanager-in-asp-net-custom-controls/
permalink: /2009/07/jquery-and-scriptmanager-in-asp-net-custom-controls/
blogger_blog:
  - www.thepensiveprogrammer.com
blogger_author:
  - Michael Atwood
blogger_permalink:
  - /2009/07/jquery-and-scriptmanager-in-aspnet.html
blogger_internal:
  - /feeds/848423877370929683/posts/default/4319693935493283726
categories:
  - 'c#'
  - jQuery
---
I dove into jQuery today with the intention of using the datepicker control in an asp.net custom control that I had built. I ended up having to jump through a few hoops to get it to work properly so I thought I would share what I discovered.

First, lets start with a simple user control like the one below:

What I wanted to do was make the date text box control work as a jQuery date picker and have the custom user control work properly with UpdatePanels. In order to have a text box work as a jQuery date picker you simply have to call the code below (described in detail [here](http://jqueryui.com/demos/datepicker/)):

The problem when using user controls is that it&#8217;s impossible to know the exact control name of the other controls contained in the user control because asp.net renames those controls at will in order to avoid conflicts. To solve this problem we need to use the c# code below in order to get the mangled name that asp.net creates:

At that point all that is left to do is call register the datepicker() function with the ScriptManager. Since my control was created to only work with an internal website, I could guarantee that the page containing the control would always have ScriptManger tag initialized. If you are working with a generic control that may or may not be in an update panel check out a useful proxy class [here](http://www.west-wind.com/weblog/posts/9601.aspx).

Here is the code that _should_ work to register the jQuery script required to put initialize the date picker control with the txtDate text box.

I said should work above because this code leads to the error described in [this](http://support.microsoft.com/kb/927917) knowledge base article.

> Error KB927917 in IE8: Unable to modify the parent container element before the child element is closed.

What is happening is that the ScriptManager is registering the startup script in a block right before the closing form tag. The problem stems from the fact that jQuery is trying to modify the body tag when it builds the date picker pop up. The body tag is a parent of form tag and the form tag is not yet closed. Bummer.

After more searching I came across this jQuery [tutorial](http://docs.jquery.com/Tutorials:Introducing_$%28document%29.ready%28%29) that introduces the (document).ready() function. This turned out to be the key. By including this function in the registered code the datepicker() waits until the page or update panel finishes loading before creating the date picker popup. The final code listing:

One final note. Most sites I found said to use the control and the control type as the parameters to the RegisterStartupScript function. Using the control parameters works until you add more complexity to the user control. For example, after adding a databound grid, changes to the underlying data would update the grid and thus the update panel the control resided on, but not re-register the date picker script. The fix for this was to pass the page itself to the register script function.