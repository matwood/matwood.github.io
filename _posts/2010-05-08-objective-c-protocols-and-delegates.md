---
id: 33
title: Objective-C Protocols and Delegates
date: 2010-05-08T00:00:00+00:00
author: Michael
layout: post
permalink: /2010/05/objective-c-protocols-and-delegates/
blogger_blog:
  - www.thepensiveprogrammer.com
blogger_author:
  - Michael Atwood
blogger_permalink:
  - /2010/05/objective-c-protocols-and-delegates.html
blogger_internal:
  - /feeds/848423877370929683/posts/default/6928662155541114110
categories:
  - iphone
  - obj-c
---
My earlier [article](https://thepensiveprogrammer.com/index.php/2010/03/06/customizing-the-uitextfield-format-for-currency/) about building a currency formatter for a UITextField generated a few comments and some confusion about how to use delegates in iPhone programming. In this article/tutorial I hope to clear up any confusion and tie it back to using the the currency formatter with the UITextField.

### What Is A Protocol?

The Apple documentation on a protocol can be found [here](http://developer.apple.com/mac/library/documentation/cocoa/conceptual/objectivec/articles/ocProtocols.html). The documentation summarizes that:

> Protocols declare methods that can be implemented by any class. Protocols are useful in at least three situations:
> 
>   * To declare methods that others are expected to implement
>   * To declare the interface to an object while concealing its class
>   * To capture similarities among classes that are not hierarchically related

The situation that we are interested in is the first one that allows methods to be declared that others are expected to implement. In this situation protocols are very similar to Java and C# interfaces. In our particular case Apples UIKit framework provides a protocol for use with the UITextField. This protocol is conveniently named [UITextFieldDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UITextFieldDelegate_Protocol/UITextFieldDelegate/UITextFieldDelegate.html). Defined in this protocol are 7 optional methods that a program can implement to change the behavior of a UITextField. Keep in mind that Apple has only provided the skeleton of the method and not any actual implementation. The implementation is left up to the programmer to implement for their specific needs. In our case we only need to implement 2 of the methods.

So now we have all we need to create a stubbed out version of our UITextFieldDelegate protocol implementation:

The @interface line is where we tell the compiler that we are implementing the UITextFieldDelegate with this class. Keep in mind that it doesn&#8217;t have to be a separate class like here, and could instead be the controller or any other class where you think the delegate methods should be implemented. I like doing a separate class since it makes the delegate easy to reuse and keeps the code modularized.

Since we have not yet provided any implementation, if we took this above code as is and made it the delegate of a UITextField the text field would work the same way it does without a delegate attached. Since this post is not about the implementation of the currency format delegate I&#8217;m going to point readers to my [previous post](https://thepensiveprogrammer.com/index.php/2010/03/06/customizing-the-uitextfield-format-for-currency/) on implementation and move on to how to use the delegate.

### What Is A Delegate?

Once again the Apple [documentation](http://developer.apple.com/iphone/library/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html) is quite good at explaining delegation. In delegation programming one object (in our case the UITextField) relies on another object (the CurrencyFormatDelegate) to provide a certain set of functionality. As the Apple documentation explains, using delegation programming allows the programmer to customize the behavior of framework objects without having to subclass. In turn this allows the programmer to aggregate multiple delegates into one object and extend the functionality of the existing framework. Now, what does all this mean for the currency formatter? In order to get our delegate to execute at the proper time we have to attach it to our UITextField. Assume that we have already defined and linked a UITextField in Interface Builder and we called it currencyTextField. In the controllers viewDidLoad we just need to assign the CurrencyFormatDelegate our UITextFields delegate property.

You&#8217;ll notice a bit of object management code to make sure that we properly allocate and release the delegate when we are done with it. The key line in all of the controller code is:

It is here where we tell the text field to user our delegate and call our implementation of the protocol methods.

I hope that this clears up some of the issues people were having while trying to use my text field currency formatter. The code above should mostly work as is since I took it from a working program and pared it down for the post. If there are any copy and paste issues they shouldn&#8217;t be too hard to correct.

### Further Reading

The [delegate pattern](http://en.wikipedia.org/wiki/Delegation_pattern) is a fundamental object oriented design pattern. If you want to know more about the delegate pattern and design patters in general check out [Design Patterns](http://www.amazon.com/gp/product/0201633612/ref=as_li_ss_tl?ie=UTF8&tag=thepensprog-20&linkCode=as2&camp=217145&creative=399369&creativeASIN=0201633612).