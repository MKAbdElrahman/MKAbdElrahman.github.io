+++
authors = ["Mohamed Kamal"]
title = "Go Context Package: Cause-aware Context Cancellation"
date = "2023-11-21"
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


The context package in Go has recently introduced  cause-aware context cancellation functions. This update includes the `WithCancelCause`, `WithDeadlineCause`, and `WithTimeoutCause` functions, as well as the `Cause` function for retrieving the cause of context cancellation.


```go
func WithCancelCause(parent Context) (ctx Context, cancel CancelCauseFunc)
```
This function is analogous to `WithCancel`, but it returns a `CancelCauseFunc` that allows setting an error as the cause of cancellation.


```go
func WithDeadlineCause(parent Context, d time.Time, cause error) (Context, CancelFunc)
```
Combining features of `WithDeadline` with the ability to provide a cause for context cancellation.

```go
func WithTimeoutCause(parent Context, timeout time.Duration, cause error) (Context, CancelFunc)
```
Similar to `WithTimeout`, this function allows specifying a cause for context cancellation.

```go
func Cause(c Context) error
```
Retrieves the cause of a context's cancellation.


### Understanding the `Cause` Functionality

The purpose of the `Cause` function is straightforward: it returns a non-nil error explaining the reason for the cancellation of a given context (`c`). If the context has not been canceled, the function returns nil.  

### Exploring Examples

Let's delve into practical examples to illustrate the functionality of the `Cause` function.

#### Example 1: Context Canceled with Error

In this scenario, we create a parent context and a child context, explicitly cancel the child context, and then use the `Cause` function to retrieve the cancellation error.

```go
package main

import (
	"context"
	"errors"
	"fmt"
)

func main() {
	parentContext := context.Background()

	childContext, cancel := context.WithCancelCause(parentContext)

	cancel(errors.New("the context was explicitly canceled"))

	err := context.Cause(childContext)

	if err != nil {
		fmt.Printf("Context canceled with error: %v\n", err)
	} else {
		fmt.Println("Context not canceled yet.")
	}
}
```

Output:

```bash
➜ go run cause.go
Context canceled with error: the context was explicitly canceled
```

In this example, the `Cause` function correctly identifies the cancellation error.


#### Example 2: Context Not Canceled Yet

Here, we explore a scenario where the context has not been canceled, and the `Cause` function correctly returns nil.

```go
package main

import (
	"context"
	"fmt"
)

func main() {
	parentContext := context.Background()

	err := context.Cause(parentContext)

	if err != nil {
		fmt.Printf("Context canceled with error: %v\n", err)
	} else {
		fmt.Println("Context not canceled yet.")
	}
}
```

Output:

```bash
➜ go run cause.go
Context not canceled yet.
```

In this example, the `Cause` function recognizes that the context has not been canceled and returns nil.
#### Example 3: Hierarchy of Contexts

The `Cause` function exhibits an intriguing feature: "The first cancellation of c or one of its parents sets the cause." Let's explore this by creating a hierarchy of contexts.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	backgroundContext := context.Background()

	parentContext, cancelParent := context.WithCancelCause(backgroundContext)

	childContext, _ := context.WithCancel(parentContext)

	cancelParent(errors.New("the parent context was cancelled"))

	err := context.Cause(childContext)

	if err != nil {
		fmt.Printf("Context canceled with error: %v\n", err)
	} else {
		fmt.Println("Context not canceled yet.")
	}

	time.Sleep(3 * time.Second)
}
```

Here, despite canceling the parent context, we can retrieve the cause of cancellation from the child context.






