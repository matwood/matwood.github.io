---
id: 36
title: Customizing The UITextField Format For Currency
date: 2010-03-06T23:50:00+00:00
author: Michael
layout: post
guid: https://thepensiveprogrammer.com/index.php/2010/03/06/customizing-the-uitextfield-format-for-currency/
permalink: /2010/03/customizing-the-uitextfield-format-for-currency/
blogger_blog:
  - www.thepensiveprogrammer.com
blogger_author:
  - Michael Atwood
blogger_permalink:
  - /2010/03/customizing-uitextfield-formatting-for.html
blogger_internal:
  - /feeds/848423877370929683/posts/default/310355382938659563
categories:
  - iphone
  - obj-c
---
\*\* Update! \*\*
  
Make sure to read [part two](https://thepensiveprogrammer.com/2011/12/uitextfield-format-for-currency-revisited/) of this article to learn about how this solution was optimized.

When I was building iAutoCalc for the iPhone I was looking for an easy way to validate and format the data as it was entered into a UITextField.  I found plenty of articles to do the validation, but nothing that showed a way to arbitrarily format the field as data was entered.  After tinkering around I came up with a pretty neat method of providing a locale correct format for currency data.  I haven&#8217;t played with it anymore yet for other format styles, but I think it could be easily adopted for any format style.

The exact problem I wanted to solve was to have the user only be allowed to enter numeric data and have that data formatted as a local specific currency with proper grouping separators.  For example, if the user entered 20000 the UITextField would display $20,000.  I also wanted the formatting to happen real time as the user entered the data.

First we need to create a currency formatter:

    
    NSNumberFormatter* currencyFormatter = [[[NSNumberFormatter alloc] init] autorelease];
    [currencyFormatter setFormatterBehavior:NSNumberFormatterBehavior10_4];
    [currencyFormatter setNumberStyle:NSNumberFormatterCurrencyStyle];
    [currencyFormatter setMaximumFractionDigits:0];
    [currencyFormatter setLocale:[NSLocale currentLocale]];
    

The above creates a basic, local specific currency formatter without any decimal places.

We also need to create a set to help us reject all non-numbers:

<pre><code class="objc">
//set up the reject character set
NSMutableCharacterSet *numberSet = [[NSCharacterSet decimalDigitCharacterSet] mutableCopy];
  [numberSet formUnionWithCharacterSet:[NSCharacterSet whitespaceCharacterSet]];
  nonNumberSet = [[numberSet invertedSet] retain];
  [numberSet release];
</code></pre>

I create both the currencyFormatter and nonNumberSet in the init function for use in the text change callback method below.

Now we need a hook into the UITextField that will tell our controller know when the text has been updated. The UITextFieldDelegate does just that by providing the callback method:

<pre><code class="objc">
-(BOOL)textField:(UITextField *)textField 
  shouldChangeCharactersInRange:(NSRange)range 
  replacementString:(NSString *)string
</code></pre>

Each time a character is typed into the UITextField the above function will execute prior to the character being entered. If the function returns YES the character will be entered into the UITextField. For more information on this method see the [Apple Developer Documentation](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UITextFieldDelegate_Protocol/UITextFieldDelegate/UITextFieldDelegate.html#//apple_ref/occ/intfm/UITextFieldDelegate/textField:shouldChangeCharactersInRange:replacementString:).

Below is the full listing of the completed function:

<pre><code class="objc">
-(BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string{
 BOOL result = NO; //default to reject
 
 if([string length] == 0){ //backspace
  result = YES;
 }
 else{
  if([string stringByTrimmingCharactersInSet:nonNumberSet].length > 0){
   result = YES;
  }
 }
 
 //here we deal with the UITextField on our own
 if(result){
  //grab a mutable copy of what's currently in the UITextField
  NSMutableString* mstring = [[textField text] mutableCopy];
  if([mstring length] == 0){
   //special case...nothing in the field yet, so set a currency symbol first
   [mstring appendString:[[NSLocale currentLocale] objectForKey:NSLocaleCurrencySymbol]];
   
   //now append the replacement string
   [mstring appendString:string];
  }
  else{
   //adding a char or deleting?
   if([string length] > 0){
    [mstring insertString:string atIndex:range.location];
   }
   else {
    //delete case - the length of replacement string is zero for a delete
    [mstring deleteCharactersInRange:range];
   }
  }
  
  //to get the grouping separators properly placed
  //first convert the string into a number. The function
  //will ignore any grouping symbols already present -- NOT in iOS4!
  //fix added below - remove locale specific currency separators first
  NSString* localeSeparator = [[NSLocale currentLocale] 
                                 objectForKey:NSLocaleGroupingSeparator];
  NSNumber* number = [currencyFormatter numberFromString:[mstring
                        stringByReplacingOccurrencesOfString:localeSeparator 
                                                  withString:@""]];
  [mstring release];
  
  //now format the number back to the proper currency string
  //and get the grouping separators added in and put it in the UITextField
  [textField setText:[currencyFormatter stringFromNumber:number]];
 }
 
 //always return no since we are manually changing the text field
 return NO;
}
</code></pre>

The things to note about the finished function is that I am always returning NO from the function. I do this because I am manually managing what characters end up in the text field.

Next, notice the odd empty string case where I put in the localized currency symbol. The problem is that the currency formatter will not convert a string to a number without a currency symbol. It doesn&#8217;t care about grouping symbols, but the lack of a currency symbol will cause it to return nothing. This case makes sure that the currency symbol is always the first character in the text field.

Finally is the trick that properly places the localized grouping separators. After making the correct string by inserting or removing characters based on the information the callback passes in, we turn that string into its number representation and then turn around and format it right back into a string. This process removes any grouping symbols (localized of course) and then puts them back in in the correct locations.