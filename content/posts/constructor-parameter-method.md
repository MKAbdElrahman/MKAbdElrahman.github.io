---
title: "Coding Best Practices: The Constructor Parameter Method"
date: "2024-05-12"
description: ""
categories: ["Coding Best Practices", "Go"]
---

This is the third pattern discussed in the Smalltalk Patterns book. I found it difficult to understand Kent's intention at first, but here's my interpretation.

In the previous "best practice" or pattern, we discussed the significance of constructors as the sole point of truth for healthy object creation.

This time, the focus is on passing parameters to the constructor to initialize the object.

Here's the first point:

- Directly assigning the parameters to the object isn't safe.

```go
func NewPoint(x int, y int) Point{
    return Point{
        x: x,
        y: y,
    }
}
```

- Creating a specific setter for each parameter might lead to code duplication across setters.

```go
func NewPoint(x int, y int) (*Point, error){
    p := &Point{
    }

   err :=  p.setX(x)
   if err != nil {
    // handle error
   }
}


func (p *Point) setX(x int) error{
    // validation - return error if validation fails
    p.x = x

    return nil
}

func (p *Point) setY(y int) error{
    // validation - return error if validation fails
    p.y = y

    return nil
}
```

Here, the same validation logic is repeated in both setters.

- The author suggests combining the setting logic for multiple parameters into a single function if they share the same validation logic. However, I believe another solution could be to extract only the validation logic into a separate helper function and reuse it in both setters, which might be better.

```go
func NewPoint(x int, y int) (*Point, error){
    p := &Point{}

    err := validateCoordinate(x)
    if err != nil {
        // handle error
    } else {
        p.x = x
    }

    err = validateCoordinate(y)
    if err != nil {
        // handle error
    } else {
        p.y = y
    }

    return p, nil
}

func validateCoordinate(coord int) error {
    // Validation logic - return error if validation fails
    // For example:
    if coord < 0 || coord > 100 {
        return errors.New("coordinate value out of range")
    }
    return nil
}

```
