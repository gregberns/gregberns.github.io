---
layout: post
title: Engineering Rigor
date: '2017-07-01T05:50:47.318Z'
categories: []
keywords: [software]
---

Of course there are some very interesting talks out there, but this one contains some truly interesting insights. It may take a more experienced dev to appreciate the minutia but understanding these disciplined practices will help us reduce headaches and produce more reliable software.

[Code as Risk • Kevlin Henney](https://www.youtube.com/watch?v=YyhfK-aBo-Y)

> "In failure we reveal something much more profound about how our system is built…(and what does failure) tell us about the management of the organization around (the system)"

Maybe we should check all config values at the beginning of the program. Read: [Early Detection of Configuration Errors to Reduce Failure Damage](https://www.usenix.org/system/files/conference/osdi16/osdi16-xu.pdf)

Building this discipline to check all your inputs before you put them into (pure) functions to process them will reduce difficult to read logic code that contains null checks, validations, and throws…

> "Be conservative in what you do, be conservative in what you accept from others"

Let’s diligently work to improve the discipline and rigor we use in our daily engineering challenges…

> "Testing is the Engineering Rigor of Software Development"