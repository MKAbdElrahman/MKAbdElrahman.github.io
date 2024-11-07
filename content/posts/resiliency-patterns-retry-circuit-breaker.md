---
title: "Resiliency Patterns: 
Retry and Circuit Breaker, Intuition Behind Them and How they Work Together"
date: "2024-11-06"
description: ""
tags: [Go, Resiliency Patterns]
---


The purpose of this blog post to understand intituvely the retry and circuit breaker reseliency patterns. They are ofen involved to work around the unreliabilty of the network. Both are used to  handle error but differ in their  timescale. Mainly retrys are used to handle short term errors, those are errors that happen because of a netwrok glithch, the cause of the problem is expected to to be fixed in few seconds or less. Circuit Beakers is a smarter retry pattern. Here  the issue is expected to last for a long period of time, instead of hammering the server continously, we use smart strategy to probe its health instead of making the problem worse. May be the server is under high load and retrying only worsens the problem.  


In order to understand the two patterns deeply, lets see them on a graph


![retry-ideal](/images/retry-pattern/retry-ideal.png)

The top black solid line represnets server is running normally. When is the server is down, its represneted by dashed red line.  In this situation The server is running normally for almost all the time, but for  a short period of time represented by red, the server is down, a request sent in that peroid is unlikely be answerd with a retry a couole of few times. The retry pattern works great for these schenarios but its fundamentally based on the assumptuin that the server is up and well most of the time and dowen times are rare. 

What if the server is not there, what if its being overloaded and most of client reuqests are timout, retry continously  we are accelerating the death of the server



Let't think about this, if we tried say three times, why we should we retry again, problably is something is wrong and the error is not short term as we assume. 


Instead of that after the inital retry we wait for a longer period of time and then retry another retry groub

![retry-groups](/images/retry-pattern/retry-groups.png)


but how much should we retry those retry groups? It depnds on how you qualify an error as long term, now we are shure that there is something terribly wrong with our server and we should  stop hammering our server and minimize sending requests to the absolute necessity. 


one way to do this after the inital group of retry and no success, we block any incoming request, wait for a period of longer time and try sending one probing request like a health check, or even allow one user request although i don't like playing with users. If that request failed wait for another llonger period of time until the probing request succeed. This what is exaclty know as circuit breaker, its a pattern for minmizing retry requests, in someway its a retry pattern on a longer time scale but with bias toward retrying once when it has historical experiece that server is down. 


![alt text](/images/retry-pattern/retry-groups-healthchecks.png)




From these we can see, that both rerey and  circuit beaker complemet each other. One is short term and one is long term. 