---
id: 44
title: 32-Bit To 64-Bit Upgrade
date: 2009-03-13T16:17:00+00:00
author: Michael
layout: post
guid: https://thepensiveprogrammer.com/index.php/2009/03/13/32-bit-to-64-bit-upgrade/
permalink: /2009/03/32-bit-to-64-bit-upgrade/
blogger_blog:
  - www.thepensiveprogrammer.com
blogger_author:
  - Michael Atwood
blogger_permalink:
  - /2009/03/32-bit-to-64-bit-upgrade.html
blogger_internal:
  - /feeds/848423877370929683/posts/default/5754264411330789302
categories:
  - Uncategorized
---
I&#8217;ve been busy upgrading and moving a MSSQL server and all of the associated applications from a 32-bit Windows 2000 server to a 64-bit Windows 2003 server. The process has been a lot of trial and error. Below are some tips to use when moving from 32-bit to 64-bit.

Try to find a 64-bit equivalent of your application. If you wrote the application, recompile it. If you bought the application try to find an upgrade. If you have to use the 32-bit application keep reading.

If you have to run 32-bit applications using WOW (Windows On Windows) remember that nearly all of the system configurations and libraries are separate between 32/64-bit. The problem is it is not always obvious how to handle each case. The registry for example uses the same regedit.exe for both editing both the 32-bit and 64-bit keys. The difference is that all of the 32-bit keys can be found in the [HKEY\_LOCAL\_MACHINESOFTWAREWow6432Node]. There are some exceptions for COM, but if you have a 32-bit application that just isn&#8217;t working check its registry keys to make sure they are in the right place.

External drivers are another issue. From my experience many 32-bit ODBC drivers install just fine under WOW. If you come across one that doesn&#8217;t, I couldn&#8217;t find much else to do other than find a more recent version. While we&#8217;re on ODBC it (unlike regedit grr) has two completely separate apps for managing 32-bit and 64-bit ODBC entries. 32-bit applications will only see the entries in the 32-bit ODBC manager and vice versa for 64-bit. This is another gotcha to check if a legacy application isn&#8217;t working and it is database driven.

Since it is a MSSQL server that is being moved there is one final piece to remember. DTS packages only run in the 32-bit container (even when running MSSQL-64). This means that any external ODBC drivers that they may need also have to be installed in the 32-bit ODBC manager. Another small annoyance is that the DTS package editor no longer works under 64-bit windows. This means that all DTS package editing has to happen on a 32-bit machine. All the more reason to finally upgrade those last remaining DTS packages to SSIS.

Good luck!