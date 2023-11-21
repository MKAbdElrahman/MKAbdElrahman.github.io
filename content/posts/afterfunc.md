+++
authors = ["Mohamed Kamal"]
title = "Go Context Package: AfterFunc"
date = "2023-11-20"
description = ""
tags = [
    "Go",
    "context",
    "concurrecny",
]
categories = [
    "Go",
    "Concurrency",
]
series = ["Go Context Package"]
+++



The `AfterFunc` function in Go's context package provides a robust tool for scheduling the execution of a specified function after a given context concludes, be it due to cancellation or a timeout. This blog post delves into the practical application of `AfterFunc` through an illustrative example, shedding light on its functionality and potential use cases.

#### The `AfterFunc` Signature

```go
func AfterFunc(ctx Context, f func()) (stop func() bool)
```

- `ctx`: The context that determines when the function `f` should be executed.
- `f`: The function to be executed after the context is done.
- `stop`: A function that, when called, stops the association of the context with the function `f`. It returns true if it successfully prevents `f` from being run and false otherwise.





### Basic Example

```go
func main() {
    // Creating a background context
    ctx := context.Background()

    // Setting a timeout of 2 seconds for the context
    ctx, cancel := context.WithTimeout(ctx, 2*time.Second)
    defer cancel()

    // Creating a WaitGroup to ensure main doesn't exit before AfterFunc completes
    var wg sync.WaitGroup
    wg.Add(1)

    // Registering an AfterFunc to be called after 2 seconds
    stop := context.AfterFunc(ctx, func() {
        // Simulating some work that takes 4 seconds
        time.Sleep(4 * time.Second)

        // Indicating that the AfterFunc work is done
        wg.Done()
    })
    
    // Ensuring that the stop function is called to clean up resources
    defer stop()

    // Waiting for the AfterFunc to complete its work before allowing main to exit
    wg.Wait()
}
```

In this example, a background context is created, and a timeout of 2 seconds is set using `context.WithTimeout`.

A `sync.WaitGroup` is employed to synchronize the main goroutine with the goroutine spawned by `AfterFunc`. The `defer cancel()` ensures that the context is canceled when main exits, and `defer stop()` ensures that resources associated with `AfterFunc` are cleaned up properly.

The registered `AfterFunc` performs some simulated work that takes 4 seconds, and the `WaitGroup` (`wg`) is used to wait until this work is completed before allowing the main goroutine to exit.


## Notes


**1: Multiple Calls to `AfterFunc`**


Multiple calls to `AfterFunc` on the same context execute independently. They don't replace or interfere with each other, allowing for concurrent execution of multiple scheduled functions.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	context.AfterFunc(ctx, func() {
		fmt.Println("First function executed")
	})

	context.AfterFunc(ctx, func() {
		fmt.Println("Second function executed")
	})

	time.AfterFunc(2*time.Second, cancel)

	time.Sleep(3 * time.Second)
}
```

```bash
➜ go run example2.go 
Second function executed
First function executed
```



**2: Stopping Association with `stop` Function**

Here, the `stop` function is called immediately after scheduling the function. It stops the association, preventing the scheduled function from execution.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	stop := context.AfterFunc(ctx, func() {
		fmt.Println("This should be canceled")
	})

	stop()

	cancel()

	time.Sleep(2 * time.Second)
}
```



**3: Checking Success of the `stop` Operation**


The `stop` function returns a boolean indicating whether it successfully stopped the association. It helps handle scenarios where explicit control is needed based on the state of the scheduled function.


```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	stop := context.AfterFunc(ctx, func() {
		fmt.Println("This should be canceled")
	})

	success := stop()

	if success {
		fmt.Println("Association successfully stopped")
	} else {
		fmt.Println("Association was already stopped or the function has started")
	}

	cancel()

	success = stop()

	if success {
		fmt.Println("Association successfully stopped")
	} else {
		fmt.Println("Association was already stopped or the function has started")
	}

	time.Sleep(2 * time.Second)
}
```
```bash
➜ go run .
Association successfully stopped
Association was already stopped or the function has started
```