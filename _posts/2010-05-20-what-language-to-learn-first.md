---
id: 32
title: What Language To Learn First?
date: 2010-05-20T01:05:00+00:00
author: Michael
layout: post
permalink: /2010/05/what-language-to-learn-first/
blogger_blog:
  - www.thepensiveprogrammer.com
blogger_author:
  - Michael Atwood
blogger_permalink:
  - /2010/05/what-language-to-learn-first.html
blogger_internal:
  - /feeds/848423877370929683/posts/default/983313612342785216
categories:
  - languages
  - programming
---
When looking at various programming forums I see this question over and over. Many responders usually jump in suggesting languages from C to Java to Ruby to Lisp. Well, maybe not Lisp but you get the point. The problem with so quickly answering the above question is that I think the answer is:

  * dependent on the individual
  * learning a SINGLE language should not be the goal

Personally, I learn by seeing the details. I don&#8217;t have any problem working at an abstract level (or with infamous car analogies), but before I&#8217;m completely comfortable at any given level I need to see the details of what is going on behind the scenes. An example of my personal learning style comes from my first compsci class in college where [Turbo Pascal](http://en.wikipedia.org/wiki/Turbo_Pascal) was used to teach object oriented programming. As an aside, the [school](http://www.cs.cofc.edu/) soon after moved to Java, but at the time teaching object oriented programming so early on in a curriculum was fairly progressive. A key part to any compsci curriculum is the teaching of pointers. Pointers are generally considered a complicated topic, so instead of just explaining what they were in detail the professor instead built these odd analogies. Today I don&#8217;t exactly remember the analogies, but I still remember them more confusing that helpful even after fully understanding pointers. I do recall though that they had something to with houses and phone lines. 🙂

After struggling for days trying to understand the analogies that were being used I asked one of my professors to explain in detail just what is a pointer. My professor proceeded to draw a diagram on the whiteboard similar to the one in this wiki [article](http://en.wikipedia.org/wiki/Pointer_(computing)), to which I immediately asked, &#8220;that&#8217;s it?!&#8221; All of the methods that my professors to shield me from the details in an effort to help me understand the topic were actually getting in the way of how I learned. I needed to see the details, and at that point my understanding was immediate. Because of my need to see the details in order to understand the whole, I would consider myself a person who learns from the bottom up.

For a person who learns from the bottom up and wants to take things apart and see the details of how it works before understanding the whole, a language like C is the perfect language to start with. C has a [concise](http://en.wikipedia.org/wiki/C_programming_language#Minimalism), fairly simple syntax and hides very little of what is happening on the hardware underneath. 

Now, contrast this with a person learns from the top down. This person needs to see the entire picture before looking at the details. This person may never look at the details unless they are forced. This person may also be perfectly content to stay at higher levels of abstraction and often doesn&#8217;t care what is going on behind the scenes as long as the abstractions work. In essence this person is a top down learner. He wants to understand the entire picture before delving into the details and then only delving as far as absolutely necessary. 

For a top down learner, starting with a language like Ruby is a great choice. Using Ruby, the top down learner can see [immediate results](http://en.wikipedia.org/wiki/Ruby_(programming_language)#Examples) and hide a lot of the details of what is going on behind the scenes. The person can learn to create programs early on without being bothered to understand the nuances of what is happening on the computer hardware to make the programs work.

At this point I want to state that I&#8217;m not saying one type of learning is better than the other. In my opinion both types of students need to learn the same languages, but it is important to realize that people have different learning styles that should be accommodated. This brings me to my second point, that the question should not be about what single language to learn first, but what set of languages to learn and in what order.

I&#8217;ve made the case above that some people learn from the bottom up and some from the top down. This does not mean that they should end up learning different languages though, and at some point should be equally proficient in a similar set of languages. A person who learns from the bottom up when it comes to a single language should apply the same strategy to a set of languages. This means that this student should learn C, then a language like Java or C#, and then move onto the higher level languages like Ruby and Python (then onto functional languages, but I consider those beyond the scope of this article). This person ends up applying their single language learning strategy to the larger problem of learning to program in general.

For the person who learns from a top down approach they would simply flip the order of languages. Start with Ruby, then move to C#, and finally move to C making sure to understand how each level decomposes from the level above or below the current level.

Whether a student goes from the top down or bottom up, both should pay special attention what each abstraction level brings to the table. Where are the [leaks](http://en.wikipedia.org/wiki/Leaky_abstraction) in the abstraction? How are they solved? What problems are best solved by each level of abstraction? What are the shortcomings of each language? Advantages? How are the languages the same and different? By researching and answering questions like these the student will gain a much deeper understanding of all the languages they learn. &nbsp;Each time the student learns a new language they will see new ideas and concepts in languages they thought they already knew.

I hope the next time someone asks what programming language to learn first I can point them to this article because the answer isn&#8217;t as simple as C/C++/Ruby/Java/Perl/Python/&#8230;.