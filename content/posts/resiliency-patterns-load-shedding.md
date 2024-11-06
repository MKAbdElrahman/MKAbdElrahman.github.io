---
title: "Resiliency Patterns: Implementing The Load Shedding Pattern in Go"  
date: "2024-11-03"  
description: ""  
tags: [Go, Resiliency Patterns]  
---

**Suppose your web service is experiencing a surge in traffic.**

When the load increases beyond what your service can handle, it can lead to slow responses, timeouts, and ultimately crashes. Load shedding allows your service to selectively drop requests when the load is too high. A targeted approach can also be used, where only low-priority requests are dropped, preserving capacity for critical requests.

To effectively implement load shedding, you need to establish a mechanism to continuously monitor relevant control signals. Common control signals include:

- **Active Connections**: The current number of connections being handled by your server.
- **Server Resources**: The percentage of CPU resources being utilized or the memory in use.

Monitoring the number of active connections is simple, but the problem is that not all requests allocate the same amount of resources. It makes sense to monitor the load in production and determine a threshold, provided the load profile is expected to remain stable in the short term.

Dynamic monitoring of server resources is useful when requests of unequal loads have similar probabilities of being triggered.

**More on Prioritization**

The criteria for dropping requests can differ. Sometimes, users with premium access should have their requests prioritized in critical situations, such as during high load. If all users are equal, we can prioritize some requests that require fewer resources but have higher strategic value.

## Implementation

Load shedding is typically implemented as middleware, allowing you to intercept incoming requests and either accept or reject them.

The `LoadSheddingMiddleware` function creates a middleware that drops requests based on active connection thresholds.

```go
import (
	"fmt"
	"net/http"
	"sync/atomic"
)

func LoadSheddingMiddleware(maxActiveConnections int64) func(http.Handler) http.Handler {
	var activeConnections int64

	return func(next http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
			currentConnections := atomic.LoadInt64(&activeConnections)
			if currentConnections >= maxActiveConnections {
				http.Error(w, "Service Unavailable - Load Shedding in Effect", http.StatusServiceUnavailable)
				return
			}

			// Increment active connections
			atomic.AddInt64(&activeConnections, 1)
			defer atomic.AddInt64(&activeConnections, -1)

			next.ServeHTTP(w, r)
		})
	}
}
```

Instead of rejecting the request entirely, we could suggest to the user to retry later, or provide a partial result rather than a full response. This falls under the umbrella of graceful degradation, which we will discuss another time.
