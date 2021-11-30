---
layout: post
title: Decoupling Framework from Logic in the UI
date: '2017-07-09T13:04:09.233Z'
categories: []
keywords: [software]
---

Recently there has been a shift in UI architecture away from MVC/MVVM toward React/Redux and Observables. In my opinion, this is a very good thing, but vigilance still needs to be maintained with these new architectures.

Here’s my problem with MV*: you fetch data from a server, and put the data (or state) into the View. This isn’t a problem until you need a second view that requires the same data. So then you need to either fetch the data twice or cache it, both of which introduce their own problems.

Instead, with the Redux paradigm, we fetch the data, place it into a global state and each view can now reference that single state. When the state updates, the view is simply refreshed by the framework, not the developer.

From what I’ve seen, this both makes decoupling easy and forces decoupling of the business logic from the view logic. We’ve always been told this is good, but for some reason in MV* applications our views (or components/controllers) are packed with business logic. That is coupling. Coupling the rendering/UI framework to the business logic.

We’ve learned that on the backend, coupling the business logic to the application framework has negative consequences down the road. When we have framework code such as ASP.NET referenced in our business logic, and we want to replace the framework, such as move from WebForms to MVC, the migration is painful or impossible because we’ve let the framework bleed into our business logic.

This is why and how the Onion Architecture or Hexagonal Architecture have come to be. They push to ensure the entity structure and business logic are at the core of the application. When resources are needed, they can be injected through abstracted interfaces (databases through repository pattern), or the core code can be referenced and executed (such as WebApi).

This decoupling ensures our core logic lives on past the life of our application frameworks. If you haven’t tried to migrate or update a legacy application it may be difficult to fully appreciate this sentiment.

Getting back to the UI side, when we allow the UI framework to bleed into our business logic, we make a trade-off: speed of development today vs maintainability, testability and evolvability, also known as taking on technical debt. On a throw away project, this obviously is not a problem. But if you are working on re-writing an application for the second or third time, because the prior versions are now unmaintainable, it’s important to understand the tradeoffs and to make the right decisions this time.

Decoupling the UI framework from the business logic, is one of those tradeoffs. Ripping out the business logic from the views and controllers and moving that logic to a more appropriate location, not only reduces the complexity in the view, but also makes your code much easier to test, change and maintain.

Now, if you’ve chosen something like React/Redux and separated the logic from the components, you still need to be vigilant for framework creep. Watch out! What happens when you want to move away from Redux? It will happen. Ensure there are nice clean boundaries, and Redux doesn’t bleed into your core code.

That’s all for now. I would have liked to comment on Observables, but I haven’t worked with them enough, so I’ll leave that to you, dear reader, to think about further.