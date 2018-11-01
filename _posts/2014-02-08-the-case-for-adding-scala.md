---
id: 16
title: The Case for Adding Scala
date: 2014-02-08T22:43:00+00:00
author: Michael
layout: post
permalink: /2014/02/the-case-for-adding-scala/
blogger_blog:
  - www.thepensiveprogrammer.com
blogger_author:
  - Michael Atwood
blogger_permalink:
  - /2014/02/the-case-for-adding-scala.html
blogger_internal:
  - /feeds/848423877370929683/posts/default/5286954577764751633
categories:
  - java
  - languages
  - programming
---
Scala is a language that I have been examining for awhile. Early on the library versioning issues really turned me off, but the seem to have been solved. It&#8217;s also a very large language that requires programmer discipline to not abuse. Twitter has been a big proponent of Scala and the [Effective Scala](http://twitter.github.io/effectivescala/) document is a great start on how not to abuse the language features.

This [post](http://kirkwylie.blogspot.com/2011/01/scala-considered-harmful-for-large.html) argues against Scala in large projects or in mixed projects. The points made about tooling and interoperability I think have for the most part been fixed since the post was made in 2010. Intellij works great with Scala and intermingles Scala and Java in the same project without any issue.

One of the big points that is made in the post against adding Scala is that it raises the bar for someone to contribute to a project. While is does raise the bar, is Scala really the straw that breaks the camel&#8217;s back in this case? Any modern Java project is already going to be loaded with Java, Spring, Hibernate at a minimum. I also wouldn&#8217;t write any Java without using [Guava](https://code.google.com/p/guava-libraries/)&nbsp;and the components it provides like immutable collections, predicates and options. &nbsp;Do those also raise the bar too high for a new contributor?

The post also argues against multi-language projects. Again, every project is already multi-language. Java, SQL, HQL, HTML, JS, SpEL to name the common ones. Scala appears to get singled out because it can be abused. The problem with that line of logic is that any language can be abused. Project standards are key anytime there is more than one developer.

So, after some discussion we are including Scala into our Java projects. I view it as an easy way to evaluate if Scala can work for us. I also think (hope) that in the long run Scala can become our language of choice for working on the JVM.