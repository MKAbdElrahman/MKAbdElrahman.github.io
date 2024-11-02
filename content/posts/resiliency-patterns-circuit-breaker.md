---
title: "Resiliency Patterns: Implementing The Circuit Breaker Pattern in Go"
date: "2024-10-31"
description: ""
tags: [Go, Resiliency Patterns]
---


The Circuit Breaker pattern helps protect services from cascading failures when they need to communicate over a network. Here’s how it works:

Imagine **Service A** needs to send requests to **Service B**. If **Service B** is down or not responding, continuing to send requests could make the issue worse. Service A might also be relied upon by other services (**Service C, D, F**), so a failure in Service B could trigger a chain reaction of failures.

The **Circuit Breaker** helps Service A handle failures in a smarter way by:
- Giving Service B some time to recover.
- Sending appropriate error messages or cached responses to other dependent services.

The Circuit Breaker works by monitoring failed responses. If these failures exceed a certain threshold within a given time window, it “opens” the circuit to block further calls. After some time, Service A will check Service B’s status to see if it’s back online. If it is, Service A resumes normal operation; otherwise, it keeps blocking requests. Different Circuit Breaker implementations can customize the wait times, health checks, and retry strategies based on their specific needs.

Circuit breakers can also be manually controlled if a dependency is particularly risky or needs temporary isolation.

### Implementing the Circuit Breaker in Go

In Go, the Circuit Breaker fits well with the language’s philosophy of making network-related issues explicit and handling them in code rather than through hidden components.

Separating network concerns from business logic keeps your code simpler and more reusable. We’ll wrap our RPC methods with Circuit Breaker logic so that concurrency and error-handling remain outside the business logic.

Let's walk through implementing a Circuit Breaker step-by-step in Go. Each step will introduce a new concept so we can build a fully functional Circuit Breaker incrementally.


### Step 1: Define the Basic Circuit Breaker Wrapper

First, let’s define a function that takes an RPC call (a function that could fail) and a failure threshold. The threshold will define how many failed attempts are allowed before the circuit breaker "opens" (blocks further requests).

Here’s the skeleton:

```go
package main

import (
	"context"
	"errors"
	"time"
)

type RPCCaller func(context.Context) error

func WrapWithBreaker(f RPCCaller, threshold int) RPCCaller {
    return func(ctx context.Context) error {
        return f(ctx)
    }
}
```

This setup allows us to wrap any function of type `RPCCaller`, so the Circuit Breaker can control when the function is allowed to run. At this point, it’s simply a wrapper that passes the call through.



### Step 2: Add a Failure Counter and a Timestamp for the Last Attempt

Now, we’ll add a counter to track how many times the function has failed and a `lastAttempt` timestamp to control when the next attempt can be made. We initialize `failures` to zero and `lastAttempt` to the current time.

```go
func WrapWithBreaker(f RPCCaller, threshold int) RPCCaller {
    var failures int
    var lastAttempt = time.Now()

    return func(ctx context.Context) error {
        return f(ctx)
    }
}
```

The `failures` variable will count consecutive failed attempts, and `lastAttempt` will record the timestamp of the most recent attempt. We’ll use these to decide when to "open" the circuit and prevent further calls if failures exceed the threshold.


### Step 3: Implement the Circuit Breaker Logic

Next, let’s add a check at the start of our wrapped function to see if the failure count has reached the threshold. If it has, we’ll block the call and return an error saying that the circuit is "open" (meaning it’s not allowing further requests to the failing service).

We’ll also add a placeholder for a backoff strategy that defines how long the circuit remains open before attempting another call:

```go
func WrapWithBreaker(f RPCCaller, threshold int) RPCCaller {
    var failures int
    var lastAttempt = time.Now()

    return func(ctx context.Context) error {
        // If failures exceed threshold, block further calls
        if failures >= threshold {
            retryDelay := backoffStrategy(failures, threshold)
            if time.Since(lastAttempt) < retryDelay {
                return errors.New("service unavailable, circuit open")
            }
        }

        // Attempt to call the RPC function
        return f(ctx)
    }
}
```
Here’s what’s happening:
1. If `failures` exceeds `threshold`, we use a `backoffStrategy` to determine how long we should wait before trying again.
2. If the time since `lastAttempt` is less than `retryDelay`, we immediately return an error, blocking the call.
3. If enough time has passed, we allow the function to proceed.


### Step 4: Attempt the RPC Call and Update the Failure Count

Now, let’s actually try to call the RPC function and update `failures` based on whether it succeeds or fails. If the call succeeds, we reset the `failures` counter to zero. If it fails, we increment `failures` and update `lastAttempt` to the current time.

```go
func WrapWithBreaker(f RPCCaller, threshold int) RPCCaller {
    var failures int
    var lastAttempt = time.Now()

    return func(ctx context.Context) error {
        // If failures exceed threshold, block further calls
        if failures >= threshold {
            retryDelay := backoffStrategy(failures, threshold)
            if time.Since(lastAttempt) < retryDelay {
                return errors.New("service unavailable, circuit open")
            }
        }

        // Attempt to call the RPC function
        err := f(ctx)
        lastAttempt = time.Now() // Update the last attempt time

        if err != nil {
            failures++ // Increment failures if the call fails
            return err
        }

        failures = 0 // Reset failures if the call succeeds
        return nil
    }
}
```

This addition ensures that:
- **Failed attempts** increase the `failures` count, bringing the circuit closer to an open state.
- **Successful attempts** reset the `failures` counter, closing the circuit and allowing normal operation to continue.


### Step 5: Add Mutex for Thread Safety in Concurrent Environments

This function might be called from multiple goroutines. To ensure thread safety, we’ll add a `sync.Mutex` to protect shared state (`failures` and `lastAttempt`), preventing race conditions when these variables are accessed or modified concurrently.

```go
import "sync"

func WrapWithBreaker(f RPCCaller, threshold int) RPCCaller {
    var failures int
    var lastAttempt = time.Now()
    var m sync.Mutex

    return func(ctx context.Context) error {
        m.Lock()
        defer m.Unlock()

        // If failures exceed threshold, block further calls
        if failures >= threshold {
            retryDelay := backoffStrategy(failures, threshold)
            if time.Since(lastAttempt) < retryDelay {
                return errors.New("service unavailable, circuit open")
            }
        }

        // Attempt to call the RPC function
        err := f(ctx)
        lastAttempt = time.Now() // Update last attempt time

        if err != nil {
            failures++ // Increment failures if the call fails
            return err
        }

        failures = 0 // Reset failures if the call succeeds
        return nil
    }
}
```

Now, every time the function is called:
- The `m.Lock()` and `defer m.Unlock()` ensure that no other goroutines modify `failures` or `lastAttempt` at the same time, making it safe for concurrent use.


### Step 6: Define the Backoff Strategy (Optional)

A backoff strategy could be as simple as a fixed delay or a more complex exponential backoff. Here’s a simple example of a fixed backoff delay:

```go
func backoffStrategy(failures, threshold int) time.Duration {
    return time.Second * 2 // Wait for 2 seconds before allowing the next attempt
}
```

This `backoffStrategy` function returns a fixed 2-second delay for retrying, but you could make it more sophisticated based on your needs.

