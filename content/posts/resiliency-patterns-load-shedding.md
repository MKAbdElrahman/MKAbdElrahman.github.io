
---
title: "Resiliency Patterns: Implemeting The Load Shedding Pattern in Go"
date: "2024-11-03"
description: ""
tags: [Go, Resiliency Patterns]
---


**Suppose your web service is experiencing a surge in traffic.**

When the load increases beyond what your service can handle, it can lead to slow responses, timeouts, and ultimately crashes. Load shedding allows your service to selectively drop requests when the load is too high. A targeted approach can also be used, where only low-priority requests are dropped, preserving capacity for critical requests.

To effectively implement load shedding, you need to establish a mechanism to continuously monitor relevant control signals. Common control signals include:

- **Active Connections**: The current number of connections being handled by your server.
- **CPU Usage**: The percentage of CPU resources being utilized.
- **Memory Usage**: The amount of memory currently in use.
- **Average Request Latency**: The time taken to process requests on average.

## Implementation

Load shedding is usually implemented as middleware, allowing you to intercept incoming requests and either accept or reject them.

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

Thresholds like `maxActiveConnections` can be derived empirically from load testing. Other metrics, such as CPU utilization, can be used dynamically to avoid hard-coded server-specific thresholds. I've also seen more softer implementations where the fraction of dropped requests increases from 0 to 1 (complete drop) under extreme load. The observed metrics and drop-off strategy should ideally be based on monitoring server behavior in production rather than relying on pre-implemented algorithms that may not work effectively. 

I hope I’ve touched on the key ideas so you can go ahead and implement your own load shedding solution!

