---
layout: post
title: Modeling Systems with Category Theory like in Domain Driven Design
date: '2018-05-09T03:25:06.242Z'
categories: []
keywords: [software]
---

I’ve mentioned ‘category theory’ before and this video is a decent intro to it: [Category Theory by Tom LaGatta](https://www.youtube.com/watch?v=o6L6XeNdd_k). It might also be worth reading the intro to [Category Theory for Scientists](http://math.mit.edu/~dspivak/CT4S.pdf) because its quite easy to grasp.

Some have asked after reading [Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/): "But how can I apply this?" Hopefully this explanation and parts of this video might help tie these concepts together.

So, to whet your interest…

Over the last couple years Domain Driven Design has become quite popular when designing and building systems. In some ways Category Theory is like DDD. You can use concepts from Category Theory to help model systems or small sets of functions and think through designs more thoroughly.

In DDD, we have Entities, Values, and other things. Lets add the idea of ‘Functions’ to that list, which represent the transformation from one entity/value to another. So take a `NewMember` entity and a function which transforms it into an `ActiveAccount` object. We’ll use this notation to symbolize a function that takes a `NewMember` and returns an `ActiveAccount`:

```
NewMember -> ActiveAccount
```

Remember in DDD we don’t need to worry about persistence (saving to a database) or network IO. We just care about the objects/entities moving and changing as they move through the system.

We might also have another function that takes our `ActiveAccount` object and turns it into a `InactiveAccount` object. Or a function symbolized like this:

```
ActiveAccount -> InactiveAccount
```

Now the interesting thing is that if we have two transformations: a `NewMember` to `ActiveAccount` and `ActiveAccount` to `InactiveAccount`, we also have a way of getting directly from `NewMember` to `InactiveAccount`. (Why we might want to do this in our domain, I’m not sure, but its just an example).

Because we have this:

```
NewMember -> ActiveAccount -> InactiveAccount
```

We implicitly can do this:

```
NewMember -> InactiveAccount
```

OOP has the idea of ‘information hiding’, well this is ‘transformation hiding’.

OK, so why’s this useful!?!?

We’ll when modeling a system, if you define the objects that are going to move through the system and define the functions that transform those objects, you can design more accurately and find gaping holes much more quickly.

The above is a **serious** simplification of category theory… but essentially it’s a system of objects and ‘morphisms’. Objects are the entities and values, and morphisms are just the functions or transformations described above. Think of the morphisms as a C#/Java interface that describes the signature but doesn’t worry about the implementation. Category Theory provides:

> a new semantics for talking about programs, allowing people to investigate how programs combine and compose to create other programs, without caring about the specifics of implementation.  
> - [Category Theory for Scientists](http://math.mit.edu/~dspivak/CT4S.pdf) page 10

With that, take a look at [Category Theory by Tom LaGatta](https://www.youtube.com/watch?v=o6L6XeNdd_k). Hopefully it will help grasp the basics and apply it to real world scenarios.

A couple notes:

*   Minutes 12-ish to 49 of the talk are pretty mathy, so may be a bit rough for those like me. If you get bored, skip to around 50 mins and it’ll get into modeling diagrams into categories, genealogy as categories, database schema as categories, etc
*   In the talk he mentions [Category Theory for Scientists](http://math.mit.edu/~dspivak/CT4S.pdf) and theres the link. The intro is worth reading. "data models are categories, agent actions are monoid actions, context can be modeled by monads" WHAT!! Yes.
*   The arrow notation above is actually something called ‘denotational design’ or ‘denotational semantics’ and it can be really helpful when modeling systems or even simple set of functions.)
*   I found this talk based on the [Magic Read Along podcast episode #15](http://www.magicreadalong.com/episode/15)