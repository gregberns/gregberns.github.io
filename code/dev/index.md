---
title: Code>Dev
layout: default
---

#Senior Software Developer

Build and maintain the InfoBurst Platform. Work closely with customers to solve challenging issues and gather new feature requirements. Provide support in complex customer projects, helping guide users to a successful project outcome.

###Primary Responsibilities

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

###Development Projects

####REST API

Built a REST API on top of existing platform, to expose existing functionality and promote further use of the underlying platform. Most of the functionality was hidden under a SOAP API, which means that modern web apps could not easily access the data.

By exposing the platform through a simple web interface, we could quickly develop web applications leveraging the platform's power. With this flexibility, we were able to deliver features to customers quickly.

####APIs & SDKs

Primary developer building the interface between InfoBurst and the SAP Business Objects Platform (BO).
When Business Objects version 4.0 (BI4.0) was released, there was major changes made to the SDKs which required us to re-write the interface between InfoBurst and BO. I was in charge of writting both the initial BI4.0 Java interface, and later, the BI4.1 Web Service interface.

####SQL Builder

For those not in the 'know', SQL is a structured language used to retrieve and update data from a database.
There was a desire from our users to have an easy way to create these SQL statements, without having to know the 
language. The problem was that, not only would I have to 'write' the SQL for them, but I'd also have to read SQL,
which is a more difficult task. I embarked on this task without understanding the rabbit hole I was about to 
go down.

####Semantic Layer

After building the basics of the SQL Builder, and understanding how to read and generate SQL, I embarked on an even more ambitious task: could I look at a whole database and generate SQL based on its structure? The answer turned out to be yes.

###Team Lead - Dashboard Design Tool

Worked on a team to develop a new ‘drag-drop’ dashboard design tool, for business users to create powerful HTML dashboards, in the browser. When the project moved from a simple proof-of-concept to an activly developed project, oversight was needed to create a powerful tool that accomplished many of the tasks customers could do in other tools.

+ Gather customer requirements and drive feature development
+ Led exploratory testing efforts to identify workflow issues and create optimizations 
+ Improve workflows to deliver a powerful, functional user experience
+ Built new features and fixed bugs in JavaScript and jQuery code
+ Improved 'look and feel' of UI through further development of CSS3
+ Developed server-side APIs to allow dashboards to be saved and published
+ Learned dashboard tool's code base, and provided bug fixes and features.
