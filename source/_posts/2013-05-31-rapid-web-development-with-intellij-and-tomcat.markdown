---
layout: post
title: "Rapid Web Development with Intellij and Tomcat"
date: 2013-05-31 02:31
comments: true
categories:
 - Intellij
 - Tomcat
 - Java
---
At a previous position, I was a big fan of using the Maven Jetty Plugin to develop web apps, particularly when I was working on JSPs that needed some tweaking for different browsers. The main drawback to this approach was that using the plugin for multiple applications was cumbersome and the documentation (at the time) wasn't that great. 

For the last few months, I've been doing all of my development through Intellij and Tomcat using a Run/Debug configuration for the app, and I didn't hit a snag, until yesterday.

One of the big perks of using the Run/Debug configuration is the availability of the "Reload classes and resources" function when you make a change. Selecting this option gets you near-Jetty speed when you want to make changes to classes or JSPs and see those changes reflected immediately. There are a few caviats though. You will still need to restart the container if you:

 * change a method signature
 * add a class
 * rename a class
 * change your Spring context

However, if you are changing the inner workings of a method or altering a JSP, a class/resource reload will reflect your change almost instantly.

The important gotcha that I found today, is that this option is only available if all the applications running your container are running exploded, which isn't a big deal if you only run one app, but when you run 7 it can be an issue. If any of your apps aren't exploded, you will see a different option, "Hot Swap", which, from what I can tell, is about the same as a complete restart from a performance perspective.

I made the mistake of selecting one of my wars, rather than the exploded version of the war, and got bit until I double checked my app configuration. Hope this saves you some pain.