---
title: Code>Dev
layout: default
---

#Senior Software Developer

Build and maintain the InfoBurst Platform. Work closely with customers to solve challenging issues and gather new feature requirements. Provide support in complex customer projects, helping guide users to a successful project outcome.

##Primary Responsibilities

+ Mentor, train, and provide ‘high level guidance’ to customers, consultants, developers, and support techs
+ Built RESTful API to expose core product functionality
+ Developed of a SQL builder and parser that would allow non-technical users to generate complex SQL queries
+ Built client for third-party RESTful API. Code currently used at 100+ sites
+ Built .NET to Java ‘bridge’, to communicate between .NET platform and Java SDK
+ Manage complex customer projects to deliver successful, on-time projects
+ Develop web based, line of business applications, for customer projects utilizing AngularJS, Bootstrap, jQuery, and KendoUI 
+ Presented talks at conferences and customer meetups
+ Built platform used for complex BI report distribution 
+ Utilized SAP Business Objects API to export reports and deliver custom reporting to clients
+ Built and managed internal automated systems and processes (CI, Installers, Build Systems)
+ Pushed team to improve internal processes (weekly meetings, better communication) 

#Futher Descriptions

+ (REST API)
+ (APIs & SDKs)

##Primary Project: InfoBurst Enterprise

###Enterprise Report Distribution Tool

Worked for 7 years on an Enterprise Report Distribution tool, providing support, development, and training, 
working closely with customers helping them get issues resolved, developing new features, and training customers 
to use the product.

Started as a support technician and product tester, then over time, moved into a development role, 
by learning .NET and how to solve problems through code.

###REST API

Built a REST API on top of existing platform, to expose existing functionality and promote further use of the underlying platform. Most of the functionality was hidden under a SOAP API, which means that modern web apps could not easily access the data.

By exposing the platform through a simple web interface, we could quickly develop web applications leveraging the platform's power. With this flexibility, we were able to deliver features to customers quickly.

###APIs & SDKs

Primary developer, building the interface between InfoBurst and the SAP Business Objects Platform (BO).
When Business Objects version 4.0 (BI4.0) was released, there was major changes made to the SDKs.
This required us to re-write the interface between InfoBurst and BO.
I was in charge of writting both the initial BI4.0 Java interface, and later, the BI4.1 Web Service interface.

* Business Objects 4.0 Web Wervice - REST based API
* Business Objects 4.1 Java SDK
* Business Objects XI3.1 Web Service and Full Client SDK's

####BI 4.1 Web Service Challenges

In a later version of BI4.0, they released a RESTful API, that was leaps and bounds better than any API released by BO previously. But, because parts of the API were incomplete, there were some challenges actually implementing a backwards compatible interface.

The issue was, and continues to be, that the implementation of the BI4.1 REST API did not do everything we 
needed it to do. That meant that we could not be fully backwards compatible, which would not be acceptable, 
especially for many current customers.

To deal with this, the implementation had to deal with not one, but three different BI4 APIs/SDKs, 
and provide a single interface back to the InfoBurst Platform. 
This meant the code needed to work with a REST API, a SOAP SDK, and a 'Fat Client' SDK.
Each one needed to be tied together, so the calls from the InfoBurst platform were seamless.

Once implemented, the new interface was deployed at client sites, and provided significant performance 
improvements, and is now utilized at a majority of InfoBurst sites.

####BI 4.1 Java SDK Challenges

When Business Objects released the 4.0 version, the previous APIs used, were either depricated, or broken. To get a solution working, the only API to use was their Java SDK.

InfoBurst is a .NET application, and there are no easy ways to interface between a .NET and Java application.
If we could solve this problem, we could provide our customers with a solution and help us get new customers 
during a time of great demand.

The first step was to convert 6,000 lines of VB.NET code into Java code, to ensure we could achieve what was needed through the Java SDK.

Once we knew the functions we needed were possible, we needed to build a 'Bridge' between the .NET and Java 
processes. To do this, a new Java process was started, which started a SOAP API 'server'. From there, the .NET application could start a SOAP client and send messages to the Java 'server'. Once the processing was complete on the .NET side, it needed to tell the Java server to shut down and kill its own process.

This was quite challenging to get working, but it allowed us to get 'ahead of the curve', and release a version of InfoBurst that supported BI4.0 almost a year and a half before the new API was released.
This resulted in several sales to new customers, who were moving to the new BI version.


###Team Lead - Dashboard Design Tool

Worked on a team to develop a new ‘drag-drop’ dashboard design tool, for business users to create powerful HTML dashboards, in the browser. When the project moved from a simple proof-of-concept to an activly developed project, oversight was needed to create a powerful tool that accomplished many of the tasks customers could do in other tools.

+ Gather customer requirements and drive feature development
+ Led exploratory testing efforts to identify workflow issues and create optimizations 
+ Improve workflows to deliver a powerful, functional user experience
+ Built new features and fixed bugs in JavaScript and jQuery code
+ Improved 'look and feel' of UI through further development of CSS3
+ Developed server-side APIs to allow dashboards to be saved and published
+ Learned dashboard tool's code base, and provided bug fixes and features.
