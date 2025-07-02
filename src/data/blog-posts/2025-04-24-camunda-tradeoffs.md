---
title: Camunda tradeoffs
slug: camunda-tradeoffs
publishDate: 24 April 2025
description: What are the tradeoffs for using Camunda, a process engine, in backend development?
tags: [ opinion, backend, spring boot ]
---

> ⚠️ Disclaimer: my experience with Camunda is mainly with version 7, but most points here still hold with Camunda 8+.


![Camunda boat drowning](/assets/blog/2025-04-24-camunda-tradeoffs/camunda-tradeoffs.webp)

[Camunda][1] is a tool that can help you manage the state of a business process. It integrates with Spring Boot as a
java library, where it connects to the process engine, loading any [BPMN][2] files in you resources folder. It will
handle the state of complicated, long-running workflows, offer insight into the process, and many more small features. 
It sounds great on paper, but is the developer experience really that effortless?

In this post I'll share the most important learnings during my 4 years of developing a backend application with Camunda.

## The start

My first experience with Camunda was quite positive. Their nice dashboard, that came out of the box in Camunda 7, and
functional Camunda Modeler to build BPMN files with drag and drop, allowed us to do quick prototyping. We had a simple
flow running within the hour.

We just needed to setup a PostgresQL database where Camunda would store its state and voìla!

There was some overhead as the startup time of our Spring Boot application increased significantly, but this was not
a big issue at the time.

## Custom code

So now that we had the basics setup, it was time for the actual automation that Camunda claims to help with. In the BPMN
modeler you create service tasks that can call a Spring Bean. The name of this bean needs to be defined as a string 
literal in the modeler. This already raised a question with me: "what if I rename the bean and forget to update
the BPMN model?". A valid concern as we learned later.

In the executed code Camunda provides access to the process instance ID (called businessProcessId) - super useful if 
you want to store external data and link it to a specific instance.


## Retries
Camunda also provides out of the box retries, even supporting incremental backoff. This is a great feature for when
the code is connecting to external system and transient errors will disappear when a good retry strategy is chosen.

For us, there was one downside to the fact that retries are handled outside the code: we didn't want to get any 
`ERROR` logs alerts before the retry is exhausted. We alert on every `ERROR` log and don't want to get notified when 
a retry might fix the issue.

We were able to solve it by implementing custom error handler that looks at the retries Camunda still has left.

## Reusable BPMN models

![Camunda sub processes](/assets/blog/2025-04-24-camunda-tradeoffs/camunda-subprocess.webp)


## Process Migrations

## Decoupling... or not?

## Camunda 7 to Camunda 8

## Summary

- Coupling between BPMN model and Java/Kotlin code (which is difficult to refactor)

## Conclusion

[1]: https://camunda.com/

[2]: https://nl.wikipedia.org/wiki/Business_Process_Model_and_Notation