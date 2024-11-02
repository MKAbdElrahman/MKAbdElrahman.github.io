---
title: "Resiliency Patterns: Implemeting The Retry Pattern in Go"
date: "2024-11-01"
description: ""
tags: [Go, Resiliency Patterns]
---

**Suppose service A needs to send a request to service B.**

If service B is unavailable, or requests are timing out due to high load or congestion, or if there’s an issue with the network that caused the request to fail, retrying the request might make sense—provided the operation is idempotent (can be retried without changing the result).

To implement a retry algorithm effectively, we need to consider:

- When to stop retrying?
- How long to wait between retries?

**When to Stop Retrying?**

Infinite retries aren’t practical, as they could overwhelm the server once it’s back online. A fixed number of retries is reasonable, but this "magic" number should be determined based on how long the client can afford to wait for a response. In Go, we can skip setting an arbitrary retry count by stopping retries when the request context is canceled.

**How Long to Wait Between Retries?**

There are multiple strategies for setting retry intervals. A fixed delay between retries is simple, but exponential backoff (doubling the wait time after each retry) can allow more recovery time. However, for systems with significant load, fixed backoff intervals can create synchronized waves of requests, adding strain to the server. A solution is to add random delays, or “jitter,” to help balance the server load.


### Implementing Retry Logic in Go

Let’s start with a basic retry mechanism and then gradually add features to make it more resilient.

#### 1. A Simple Retry Loop

First, we define a basic retry loop that keeps calling a function until it succeeds:

```go
type RPC func(context.Context) error

func AdaptWithRetry(rpc RPC) RPC {
	return func(ctx context.Context) error {
		for {
			if err := rpc(ctx); err == nil {
				return nil // Exit on success
			}
		}
	}
}
```

This setup works but retries indefinitely, potentially overwhelming the server if there’s no success. To improve it, let’s add some control over the retry process.

#### 2. Adding a Fixed Delay Between Retries

Using `time.After`, we can introduce a fixed delay between retry attempts, giving the server some breathing room.

```go
func AdaptWithRetry(rpc RPC, wait time.Duration) RPC {
	return func(ctx context.Context) error {
		for {
			select {
			case <-time.After(wait):
				if err := rpc(ctx); err == nil {
					return nil // Success
				}
			}
		}
	}
}
```

While this is gentler on the server, it still retries indefinitely. To avoid prolonged waiting, let's add a maximum retry limit.

#### 3. Enforcing a Retry Limit and Context Cancellation

Adding a retry limit and listening for `ctx.Done()` allows us to respect the caller’s context and prevent endless retry attempts.

```go
func AdaptWithRetry(rpc RPC, wait time.Duration, maxRetries int) RPC {
	return func(ctx context.Context) error {
		for attempt := 1; attempt <= maxRetries; attempt++ {
			select {
			case <-time.After(wait):
				if err := rpc(ctx); err == nil {
					return nil // Success
				}
			case <-ctx.Done():
				return ctx.Err() // Stop on context cancellation
			}
		}
		return errors.New("retry failed: max retries reached")
	}
}
```

Now, if the maximum number of retries is reached, an error is returned. This structure also supports context cancellation, allowing for cleaner shutdowns.

#### 4. Introducing Backoff Strategies

A fixed delay might not be ideal in all cases. To customize wait times, we can define a `BackoffStrategy` function type and implement both a `FixedBackoff` and `ExponentialBackoff` strategy.

```go
type BackoffStrategy func(attempt int) time.Duration

func FixedBackoff(duration time.Duration) BackoffStrategy {
	return func(attempt int) time.Duration {
		return duration
	}
}

func ExponentialBackoff(baseDelay time.Duration) BackoffStrategy {
	return func(attempt int) time.Duration {
		return baseDelay * (1 << attempt) // 2^attempt * baseDelay
	}
}

func AdaptWithRetry(rpc RPC, maxRetries int, backoff BackoffStrategy) RPC {
	return func(ctx context.Context) error {
		for attempt := 1; attempt <= maxRetries; attempt++ {
			wait := backoff(attempt)
			select {
			case <-time.After(wait):
				if err := rpc(ctx); err == nil {
					return nil // Success
				}
			case <-ctx.Done():
				return ctx.Err() // Stop on context cancellation
			}
		}
		return errors.New("retry failed: max retries reached")
	}
}
```

By defining different backoff strategies, we can choose the appropriate delay for each retry attempt.

#### 5. Adding Jitter to Reduce Load Spikes

To avoid synchronized retry attempts, which can cause traffic spikes, let’s add jitter to our backoff strategy.

```go
func JitteredBackoff(baseDelay time.Duration, maxJitter time.Duration) BackoffStrategy {
	return func(attempt int) time.Duration {
		jitter := time.Duration(rand.Int63n(int64(maxJitter)))
		return baseDelay*(1<<attempt) + jitter
	}
}
```

The `JitteredBackoff` function introduces randomness to each delay, helping to distribute retry attempts more evenly.

### Using the Retry Mechanism

Here’s a full example of how to use different backoff strategies with `AdaptWithRetry`:


```go
package main

import (
	"context"
	"errors"
	"fmt"
	"math/rand"
	"time"
)

type RPC func(context.Context) error

type BackoffStrategy func(attempt int) time.Duration

func FixedBackoff(duration time.Duration) BackoffStrategy {
	return func(attempt int) time.Duration {
		return duration
	}
}

func ExponentialBackoff(baseDelay time.Duration) BackoffStrategy {
	return func(attempt int) time.Duration {
		return baseDelay * (1 << attempt) // 2^attempt * baseDelay
	}
}

func JitteredBackoff(baseDelay time.Duration, maxJitter time.Duration) BackoffStrategy {
	return func(attempt int) time.Duration {
		jitter := time.Duration(rand.Int63n(int64(maxJitter)))
		return baseDelay*(1<<attempt) + jitter
	}
}

func AdaptWithRetry(rpc RPC, maxRetries int, backoff BackoffStrategy) RPC {
	return func(ctx context.Context) error {
		for attempt := 1; attempt <= maxRetries; attempt++ {
			wait := backoff(attempt)
			fmt.Printf("Attempt %d: waiting %v before retry\n", attempt, wait)

			select {
			case <-time.After(wait):
				err := rpc(ctx)
				if err == nil {
					fmt.Printf("Attempt %d: success\n", attempt)
					return nil // Success
				}
				fmt.Printf("Attempt %d: failed with error: %v\n", attempt, err)
			case <-ctx.Done():
				fmt.Println("Context canceled, stopping retries")
				return ctx.Err() // Stop if context is canceled
			}
		}
		fmt.Println("Retry failed: max retries reached")
		return errors.New("retry failed: max retries reached")
	}
}

func UnreliableRPC(ctx context.Context) error {
	if rand.Float32() < 0.7 {
		return errors.New("temporary failure")
	}
	return nil
}

func main() {
	rand.Seed(time.Now().UnixNano()) // Seed the random number generator

	ctx := context.Background()

	// Fixed backoff example
	fmt.Println("Starting fixed backoff example")
	reliableRPC := AdaptWithRetry(UnreliableRPC, 5, FixedBackoff(2*time.Second))
	err := reliableRPC(ctx)
	fmt.Println("Result with fixed backoff:", err)

	// Exponential backoff example
	fmt.Println("\nStarting exponential backoff example")
	reliableRPC = AdaptWithRetry(UnreliableRPC, 5, ExponentialBackoff(500*time.Millisecond))
	err = reliableRPC(ctx)
	fmt.Println("Result with exponential backoff:", err)

	// Jittered backoff example
	fmt.Println("\nStarting jittered backoff example")
	reliableRPC = AdaptWithRetry(UnreliableRPC, 5, JitteredBackoff(500*time.Millisecond, 200*time.Millisecond))
	err = reliableRPC(ctx)
	fmt.Println("Result with jittered backoff:", err)
}

```