---
title: Camunda tradeoffs
slug: camunda-tradeoffs
publishDate: 24 April 2025
description: What are the tradeoffs for using Camunda, a process engine, in backend development?
tags: [ opinion, backend, spring boot ]
---

> ⚠️ Disclaimer: my experience with Camunda is mainly with version 7, although some points here still hold with Camunda
> 8+.


![Camunda boat drowning](/assets/blog/2025-04-24-camunda-tradeoffs/camunda-tradeoffs.webp)

[Camunda][1] is a tool that can help you manage the state of a business process. It integrates with Spring Boot as a
java library, where it connects to the process engine, loading any [BPMN][2] files in you resources folder. It will
handle the state of complicated, long-running workflows, offer insight and many more small features. It sounds great on
paper, but is it really worth it?

In this post I'll share the most important learnings during my 4 years of developing a backend application with Camunda.

## The start

My first experience with Camunda was quite positive. Their nice dashboard, that came out of the box in Camunda 7, and
functional Camunda Modeler to build BPMN files with drag and drop allowed us to do quick prototyping. We had a simple
flow running within the hour.

We just needed to setup a PostgresQL database where Camunda would store its state and voìla!

There was some overhead as the startup time of our Spring Boot application increased significantly, but this was not
unexpected.

## Custom code

So now that we had the basics setup, it was time for the actual automation that Camunda claims to help with. In the BPMN
modeler you create a service task that can call a Spring Bean using an expression in plain text, containing the class
name. In hindsight, this was already the moment I thought to myself "but what if I rename the class and forget to update
the BPMN model?".



## Reusable BPMN models


## Process Migrations

## Decoupling... or not?

## Camunda 7 to Camunda 8

## Summary

- Coupling between BPMN model and Java/Kotlin code (which is difficult to refactor)

## Conclusion

[1]: https://camunda.com/

[2]: https://nl.wikipedia.org/wiki/Business_Process_Model_and_Notation