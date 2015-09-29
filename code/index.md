---
title: Code
layout: default
---

##Summary

Self-taught software developer, deeply familiar with the enterprise product development lifecycle: R&D, development, testing, customer support, and pre-sales. Primarily a backend .NET developer, with plenty of web experience. Helped build, test, and support an enterprise grade product used at 250+ sites worldwide.

Worked on small team, doing any job needed, to build and support InfoBurst, a niche software platform that distributes hundreds of thousands of Business Intelligence (BI) documents a day, at sites worldwide. Servicing enterprise customers, from Fortune 500(Intel, Pfizer, BP) to small businesses and government organizations.

Moved from customer support to senior developer, along the way, learning all aspects of product development. Built close relationships with customers and consultants to help successfully execute dozens of projects.

My versatile skill set allows me to communicate with everyone from high level managers to developers; develop back-end and front-end applications; understand other's code; and debug. My background provides me with a large set of skills, which allow me to work in a variety of positions.

##Resume

[Click to view resume.](https://docs.google.com/document/d/1lcO355gFLsXSTq_V-3dW4K8c6aPhtvF3XxopUrf8f0s/pub)

[Click to download PDF version of resume.](https://docs.google.com/document/d/1lcO355gFLsXSTq_V-3dW4K8c6aPhtvF3XxopUrf8f0s/export?format=pdf)

##Background

In college, I started a summer landscaping business in my hometown. It lead me to get a business degree at Northern 
Illinois University. For many students, they think getting a business degree is a good idea because it will make 
for a great 'career'. I liked the challenge of what I was doing during the summer, from tracking expenses and 
payments, to working with clients, and finding new sources of revenue. 

A year or two after graduating, with a desire to see something new, I moved to Arizona, and found the two loves 
of my life.

I signed on with a small business intelligence (BI) consulting company, that had a team building powerful tools, 
on top of a highly respected BI platform, now called SAP Business Objects. They built a tool that took the drudgery 
out of peoples life by delivering reports to where people needed them.

Though I didn't have the slightest clue how software actually worked, I quickly learned.

At some points, it feels like a bit of an obsession, but I'm ok with that.


##Primary Projects

###InfoBurst Enterprise

####Enterprise Report Distribution Tool

Worked for 7 years on an Enterprise Report Distribution tool, providing support, development, and training, 
working closely with customers helping them get issues resolved, developing new features, and training customers 
to use the product.

Started as a support technician and product tester, then over time, moved into a development role, 
by learning .NET and how to solve problems through code.

More details...


####APIs & SDKs

Primary developer, building the interface between InfoBurst and the BI Platform. 
Wrote both the initial BI4.0 Java interface, and later, the BI4.1 Web Service interface.

* Business Objects 4.0 Web Wervice - REST based API
* Business Objects 4.1 Java SDK
* Business Objects XI3.1 Web Service and Full Client SDK's

#####BI 4.1 Web Service Challenges

The issue was, and continues to be, that the implementation of the BI4.1 REST API did not do everything we 
needed it to do. That meant that we could not be fully backwards compatible, which would not be acceptable, 
especially for many current customers.

To deal with this, the implementation had to deal with not one, but three different BI4 APIs/SDKs, 
and provide a single interface back to the InfoBurst Platform. 
This meant the code needed to work with a REST API, a SOAP SDK, and a 'Fat Client' SDK.
Each one needed to be tied together, so the calls from the InfoBurst platform were seamless.

Once implemented, the new interface was deployed at client sites, and provided significant performance 
improvements, and is now utilized at a majority of InfoBurst sites.

#####BI 4.1 Java SDK Challenges
InfoBurst is a .NET application, and when BI4.0 was released, the only interface available was a Java SDK. 
Of course, there is no easy way to interface between a .NET and Java application.
If we could solve this problem, we could provide our customers with a solution and help us get new customers 
durring a time of great demand.

Before solving this problem, we had to convert 5,000 lines of VB.NET code into Java code, 
to ensure we could achieve what was needed through the Java SDK.

Once we knew the functions we needed were possible, we needed to build a 'Bridge' between the .NET and Java 
processes. To do this, a new Java process was started, which started a SOAP API. From there, the .NET application
could start a SOAP client and send messages to the Java 'server'. Once the processing was complete on the .NET 
side, it needed to tell the Java server to shut down and kill its own process.

This was quite challenging to get working, but it allowed us to get 'ahead of the curve', 
and release a version of InfoBurst that supported BI4.0 almost a year and a half before the new API was released.
This resulted in several sales to new customers, who were moving to the new BI version.



###Anubis

####BI Dashboard Development Tool

<!-- Add a hover over that shows definitions/explanations of what is being discussed
      Example: "BI Dashboard Design Tool - Allows a business user or analyst to create a business 
      dashboard by dragging components onto a canvas, adding logic between components, and wiring 
      up data from the server.
-->

Helped guide a junior developer in the building of a BI dashboard design tool. While I did help write some code,
due to my knowledge of the domain, my primary role was to provide guidance on what to build next, testing what 
was being built, and push for workflow/UX improvements. Over six months time, the product moved from a barebones
design tool, to one that in many ways was more powerful than the design tool we were comparing ourselves against.

More details...

##Interesting Projects

###SQL Builder

For those not in the 'know', SQL is a structured language used to retrieve and update data from a database.
There was a desire from our users to have an easy way to create these SQL statements, without having to know the 
language. The problem was that, not only would I have to 'write' the SQL for them, but I'd also have to read SQL,
which is a more difficult task. I embarked on this task without understanding the rabbit hole I was about to 
go down.

###Semantic Layer

After building the basics of the SQL Builder, and understanding how to read and generate SQL, 
I embarked on an even more ambitious task: could I look at a whole database 
and generate SQL based on its structure?
The answer turned out to be yes.

##Technology

###Languages

* .NET - VB & C#
* Web - JavaScript, HTML, CSS
* Java

###Frameworks

####Web

* Angular
* React
* Kendo
* jQuery

####Server Side

* RESTSharp
* SyncFusion - XLSIO, PDF
* Akka.NET

###Applications

####IDE

* Visual Studio
* Eclipse
* VS Code
* Sublime
* Notepad++

####Database

* Sql Server (2005-Current) (Most Familiar DB Type)
* SQLite
* Oracle

####Business Intelligence

Business Objects XIR2 - BI4.1

* Administration + Server Scaling
* Webi
* Universe Designer
* Crystal
* Deski

####General

* Microsoft Office (Does a developer really need to include this??)
