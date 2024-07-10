---

title: "Sagas Quantatively: Fantasy Fiction"
date: "2024-07-09"
description: ""
tags: [Software Architecture]
categories: ["Software Architecture","Microservices","Saga"]
---

Last time, we discussed the first saga cataloged by Mark Richards and Neal Ford in their book, "Software Architecture: The Hard Parts". We saw how the epic saga is exponentially sensitive to the availabilities of the orchestrator and the orchestrated services. We also noted how latency worsens with increasing availability due to the sequential blocking model.  Today, we will discuss the second saga they called the fantasy fiction saga. In this saga, we switch from a blocking communication model to a non-blocking one while maintaining the atomicity constraint.  In a previous post, I discussed why we should avoid using the terms synchronous and asynchronous, and instead use more explicit terms like blocking request-reply, non-blocking request-reply, and event-driven. Let’s apply these concepts to the fantasy fiction saga.

**Constraints**  
The communication between the orchestrator and the orchestrated services is non-blocking request-reply, which means:  

- Non-blocking: Once the orchestrator sends a request to an orchestrated service, it doesn't block waiting for a reply before proceeding.

- Request-reply: The orchestrator expects a reply for its request.  We also impose the atomicity constraint, which means the orchestrator service must either return successfully or, in case of failure, it cannot return until it reverses all the changes made to the orchestrated services.  

 **Analysis**

 Compared to the epic saga, we expect lower latency as the domain services are all called in parallel. This means the latency is limited by the slowest orchestrated service call, rather than the latencies being additive as in the epic saga. If the availability of the domain service or the orchestrator is alpha, we expect the overall availability to be alpha^2 regardless of the number of orchestrated domain services. However, the order of changes in the domain services is not guaranteed by this design. The responsiveness is enhanced  alot for the succeeded requests by , but  not for the failed requests as the atomicity constraint still prevents us from returning to the caller of the orchestrator.

 The following curve shows the request success percentage for the  epic saga (blocking) and the fantasy fiction saga (non blocking).   There are three orchestrated services manages by each orchestra tor. The network unreliability is accounted for all requests  including the compensating update.  We can see that the the success percentage is not affected by switching from blocking to non-blocking.


 ![alt text](/images/saga/fantasy/success_ratio.png)


Let's see how the latencies are affected:

The first observation is the latency for the fantasy fiction saga is a almost three time smaller than the epic saga.  The latency for success cases is constant as expected.  The latency   of  the failed requests seems to increase slightly with availability. While the latency for the succeeded cases is enhanced a lot, the failed requests are still suffering.

![alt text](/images/saga/fantasy/latencies.png)
