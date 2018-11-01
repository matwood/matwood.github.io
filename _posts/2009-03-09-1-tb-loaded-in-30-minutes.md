---
id: 46
title: 1 TB Loaded in 30 Minutes!
date: 2009-03-09T20:24:00+00:00
author: Michael
layout: post
guid: https://thepensiveprogrammer.com/index.php/2009/03/09/1-tb-loaded-in-30-minutes/
permalink: /2009/03/1-tb-loaded-in-30-minutes/
blogger_blog:
  - www.thepensiveprogrammer.com
blogger_author:
  - Michael Atwood
blogger_permalink:
  - /2009/03/1-tb-loaded-in-30-minutes.html
blogger_internal:
  - /feeds/848423877370929683/posts/default/1710906948255972658
categories:
  - etl
  - mssql
  - ssis
---
[This](http://msdn.microsoft.com/en-us/library/dd537533.aspx) is a great new white paper out of Microsoft. In it they explain how to move a lot of data from flat files into [MSSQL](http://www.microsoft.com/sqlserver/2008/en/us/default.aspx) using SSIS. Some key take aways from the paper and my personal experiences loading data into MSSQL.

  * If at all possible get your source systems to provide flat files to the ETL processes. The popular ways like web services and ODBC are nice, but they tend not to perform as well when you start moving a lot of data.
  * Run SSIS on a separate server from the final destination database server. This is key for balancing the load and being able to easily up later.
  * Test, analyze, change, repeat. While the authors did use the standard off the shelf versions of the MSSQL and SSIS, they did have to tweak them to get the results they were looking for. It is critical to analyze your solution and make adjustments.

Provide other ETL best practices below.