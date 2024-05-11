---
title: "Coding Best Practices: The Composed Method"
date: "2024-05-10"
description: ""
categories: ["Coding Best Practices", "Go"]
---

After reading Kent Beck's book Tidy First, I decided to check out another one by him. This time it's about the best practices in Smalltalk. Even though the book seems old, its ideas are relevant. Let me introduce you to the first pattern from the book, called the Composed Method pattern. This pattern might have different names nowadays, but its main idea is to encourage using smaller functions with names that clearly say what they do.

Later, Martin Fowler talked about this idea in his book on refactoring code, calling it "Extract Method."

Kent Beck first explains why some developers like to write long functions:

- They might think that calling functions takes overhead. But with today's fast computers, even if we need to make our code super fast, it's better to start with well-organized code and then make it faster later.
- Beginner Smalltalk programmers often say they can't figure out where the 'real' work is happening. But as you get better, you won't need to understand how every little piece works together. Good function names should tell you enough.

## The How

Then Beck explains how to use the pattern, giving two main ways:

- Top Down: First, you think about the main steps, and each step becomes a function you write later. The step should be in the same level of abstraction.

- Bottom Up: This is like the "Extract Method" idea, where you find a group of cohesive steps that tend to go together and turn them into a function.

## Example

```go

type CartItem struct {
    Price    float64
    Quantity int
    Discount float64
}

func calculateTotalPrice(cartItems []CartItem) float64 {
    totalPrice := 0.0

    for _, item := range cartItems {

        if isItemAvailable(item) {
            totalPrice += calculateItemPrice(item)
        }
    }
    return totalPrice
}

func isItemAvailable(item CartItem) bool {
    return item.Quantity > 0
}

func calculateItemPrice(item CartItem) float64 {
    itemPrice := item.Price * float64(item.Quantity)
    if item.Discount > 0 {
        itemPrice -= item.Discount
    }
    return itemPrice
}
```

In this sketch, the `calculateTotalPrice` function represents the composed method, which orchestrates the calculation process. It calls two helper functions: `isItemAvailable` to check if an item is available, and `calculateItemPrice` to calculate the price of a single item. These helper functions encapsulate smaller tasks and contribute to the clarity and maintainability of the code.
