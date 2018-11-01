---
id: 27
title: UITextField Format For Currency Revisited
date: 2011-12-02T21:43:00+00:00
author: Michael
layout: post
permalink: /2011/12/uitextfield-format-for-currency-revisited/
blogger_blog:
  - www.thepensiveprogrammer.com
blogger_author:
  - Michael Atwood
blogger_permalink:
  - /2011/12/uitextfield-format-for-currency.html
blogger_internal:
  - /feeds/848423877370929683/posts/default/3279732876547210874
categories:
  - iphone
  - obj-c
---
Since my first post on formatting a UITextField with proper currency symbols and separators I think I have learned a lot more about objective-c and more importantly the UI components provided by Apple. &nbsp;In this article I will revisit how to format a UITextField with the correct currency symbol and grouping separators.

The original goal was to create a text field that would update in real time both with the currency symbol and grouping separators as numbers were typed in. &nbsp;The first working solution can be found [here](https://thepensiveprogrammer.com/2010/03/customizing-the-uitextfield-format-for-currency/), but it had some short comings&nbsp;particularly&nbsp;in code complexity. &nbsp;The main problem was that I was trying to keep the string formatting correct so that I could decode the string to an NSNumber using a currency formatter and then encode the string again to an NSString. &nbsp;This was needless complicating the code when dealing with inputting characters. The original code:

    
    //MARK: -
    //MARK: UITextFieldDelegate Implementation
    -(BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range 
                                                          replacementString:(NSString *)string{
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
    

Instead it was much easier to just strip out the currency symbol and group separators and then use a basic number formatter to turn the string into a NSNumber. &nbsp;I was able to remove an entire section of code:

    
     ...
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
    ...
    

Finally, take the newly formed NSNumber and run it through the currency formatter to create the desired output. &nbsp;The entire new method is below:

    
    //MARK: -
    //MARK: UITextFieldDelegate Implementation
    -(BOOL)textField:(UITextField *)textField 
        shouldChangeCharactersInRange:(NSRange)range 
                    replacementString:(NSString *)string{
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
    
            //adding a char or deleting?
            if([string length] > 0){
                [mstring insertString:string atIndex:range.location];
            }
            else {
                //delete case - the length of replacement string is zero for a delete
                [mstring deleteCharactersInRange:range];
            }
            
            //remove any possible symbols so the formatter will work
            NSString* clean_string = [[mstring stringByReplacingOccurrencesOfString:localGroupingSeparator 
                                                                         withString:@""]
                                                   stringByReplacingOccurrencesOfString:localCurrencySymbol 
                                                                             withString:@""];
            
            //clean up mstring since it's no longer needed
            [mstring release];
            
            NSNumber* number = [basicFormatter numberFromString: clean_string];
      
      //now format the number back to the proper currency string
      //and get the grouping separators added in and put it in the UITextField
      [textField setText:[currencyFormatter stringFromNumber:number]];
     }
     
     //always return no since we are manually changing the text field
     return NO;
    }
    

First thing to notice is the cleanup of the code between lines 19 and 21. There is no more special case, and thus reduced complexity. Line 32 is what allowed the removal of the special case. Instead of trying to work with symbols and groupings they are now ignored completely and removed before being added back in. The variable clean_string will end up a string with only numbers and will be easily parsed into an NSNumber by a basic NSNumberFormatter. 

There are a few class instance variables that need to be set. The init and associated dealloc is below even though they are not necessarily related to the optimizations above.

    
    //MARK: -
    //MARK: Object Management
    -(id)init{
        if ((self = [super init])){
      NSLocale* locale = [NSLocale currentLocale];
      localCurrencySymbol = [locale objectForKey:NSLocaleCurrencySymbol];
      localGroupingSeparator = [locale objectForKey:NSLocaleGroupingSeparator];
      
      currencyFormatter = [[Formatters currencyFormatterWithNoFraction] retain];
                    basicFormatter = [[Formatters basicFormatter] retain];
      
      //set up the reject character set
      NSMutableCharacterSet *numberSet = [[NSCharacterSet decimalDigitCharacterSet] mutableCopy];
      [numberSet formUnionWithCharacterSet:[NSCharacterSet whitespaceCharacterSet]];
      nonNumberSet = [[numberSet invertedSet] retain];
      [numberSet release];
        }
        return self;
    }
    
    -(void)dealloc {
        [nonNumberSet release];
        [currencyFormatter release];
        [localCurrencySymbol release];
        [localGroupingSeparator release];
        [basicFormatter release];
     
        [super dealloc];
    }
    

Formatters is a custom class that contains various class methods to return ready to use formatters. In the next article I will show how it is setup. In the meantime two new NSNumberFormatters could have just been declared inline. See the Apple reference documentation for [NSNumberFormatter](http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSNumberFormatter_Class/Reference/Reference.html).   
The above improvement cut out some code that was not needed, but most of all made the logic easier to follow. &nbsp;Instead of having to worry about special cases with currency symbols when inserting new characters, they can now just be ignored. &nbsp;The formatters do all of the heavy lifting as they should.