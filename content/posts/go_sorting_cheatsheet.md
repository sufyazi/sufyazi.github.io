---
title: "Mastering Go Sorting: Your Interview Cheatsheet"
author: "Joydeep Bhattacharya"
authorAvatarPath: "https://github.com/linkwithjoydeep.png?size=150"
date: '2025-08-27T10:30:00+05:30'
summary: "Master Go's built-in sorting capabilities for technical interviews. From basic slices.Sort() to custom comparison functions and stable sorting - everything you need to ace sorting questions."
description: "Complete guide to Go sorting functions including slices.Sort(), slices.SortFunc(), and slices.SortStableFunc() with real interview examples and performance considerations."
toc: true
readTime: true
autonumber: true
math: false
tags: ["go", "algorithms", "interview-prep", "sorting"]
showTags: true
hideBackToTop: false
fediverse: "@username@instance.url"
---

Sorting algorithms are a cornerstone of computer science interviews, but here's the thing: you don't always need to implement quicksort from scratch. Go's powerful built-in sorting capabilities can help you solve complex problems elegantly while demonstrating your knowledge of the standard library.

Whether you're preparing for a FAANG interview or just want to level up your Go skills, this comprehensive guide covers everything you need to know about sorting in Go.

## The Foundation: slices.Sort()

Go 1.21 introduced the `slices` package, making sorting more intuitive than ever. For basic data types, `slices.Sort()` is all you need:

```go
package main

import (
    "fmt"
    "slices"
)

func main() {
    // Sort string slices
    strs := []string{"c", "a", "b"}
    slices.Sort(strs)
    fmt.Println("Strings:", strs)
    // Output: Strings: [a b c]
    
    // Sort integers
    ints := []int{7, 2, 4}
    slices.Sort(ints)
    fmt.Println("Ints:", ints)
    // Output: Ints: [2 4 7]
}
```

The beauty of `slices.Sort()` lies in its simplicity. It works with any slice of comparable elements and uses Go's type system to determine the appropriate comparison logic. Under the hood, it implements a hybrid sorting algorithm that combines quicksort and heapsort for optimal performance across different data patterns.

## Custom Sorting with slices.SortFunc()

Real-world problems rarely involve sorting simple integers. This is where `slices.SortFunc()` shines. It accepts a comparison function that defines your custom ordering logic.

The comparison function signature is crucial to understand:

```go
func(a, b T) int
```

Your function should return:
- A **negative number** when `a < b`
- **Zero** when `a == b`
- A **positive number** when `a > b`

### Understanding the Comparison Function

Let me break this down with a concrete example. When Go needs to determine if element `a` should come before element `b` in the sorted result, it calls your comparison function:

```go
// If you want ascending order (smallest to largest)
func compareAscending(a, b int) int {
    if a < b {
        return -1  // "a should come before b"
    } else if a > b {
        return 1   // "a should come after b" 
    }
    return 0       // "a and b are equal, order doesn't matter"
}
```

The **positive number** case (`a > b`) tells Go: "element `a` is greater than element `b`, so `a` should come **after** `b` in the final sorted order."

For example, if you're comparing `7` and `2`:
- `7 > 2`, so return positive number
- This means `7` should come after `2`  
- Result: `[2, 7]` (ascending order)

### Reverse Sorting

One of the most common interview variations is reverse sorting:

```go
func main() {
    ints := []int{7, 2, 4}
    slices.SortFunc(ints, func(a, b int) int {
        return cmp.Compare(b, a) // Notice the reversed arguments
    })
    fmt.Println("Ints:", ints)
    // Output: Ints: [7 4 2]
}
```

The key insight here is swapping the arguments to `cmp.Compare()`. When comparing `7` and `2`, instead of `cmp.Compare(7, 2)` which returns positive (meaning 7 > 2), we do `cmp.Compare(2, 7)` which returns negative (meaning 2 < 7, so 7 should come first).

### Sorting by Custom Criteria

Let's tackle a common interview scenario: sorting strings by length.

```go
func main() {
    fruits := []string{"peach", "banana", "kiwi"}
    
    // Sort by string length
    slices.SortFunc(fruits, func(a, b string) int {
        return cmp.Compare(len(a), len(b))
    })
    
    fmt.Println(fruits)
    // Output: [kiwi peach banana]
}
```

This pattern scales beautifully to more complex scenarios. The comparison function encapsulates your sorting logic, making your code both readable and maintainable.

### Sorting Complex Data Structures

Here's where interviews get interesting. Sorting structs by fields is a frequent ask:

```go
func main() {
    type Person struct {
        name string
        age  int
    }
    
    people := []Person{
        {name: "Jax", age: 37},
        {name: "TJ", age: 25},
        {name: "Alex", age: 72},
    }
    
    // Sort by age (ascending)
    slices.SortFunc(people, func(a, b Person) int {
        return cmp.Compare(a.age, b.age)
    })
    
    fmt.Println(people)
    // Output: [{TJ 25} {Jax 37} {Alex 72}]
}
```

For multi-field sorting (common in advanced interviews), you can chain comparisons:

```go
// Sort by age, then by name for ties
slices.SortFunc(people, func(a, b Person) int {
    if ageComp := cmp.Compare(a.age, b.age); ageComp != 0 {
        return ageComp
    }
    return cmp.Compare(a.name, b.name)
})
```

## The Power of cmp.Compare()

The `cmp.Compare()` function is your Swiss Army knife for comparisons. Introduced alongside the `slices` package, it provides a consistent interface for comparing any ordered type:

```go
import "cmp"

// Returns:
// -1 if x < y
//  0 if x == y
// +1 if x > y
result := cmp.Compare(x, y)
```

This function works seamlessly with integers, floats, strings, and any type that implements the comparable constraint. It eliminates the boilerplate of manual comparisons and reduces the chance of bugs in your comparison logic.

## Stable Sorting: When Order Matters

Here's a critical detail that can make or break an interview answer: **Go's standard sorting functions are not stable**. This means equal elements might change their relative order during sorting.

For stable sorting, Go provides `slices.SortStableFunc()`:

```go
func main() {
    type Student struct {
        name  string
        grade int
    }
    
    students := []Student{
        {name: "Alice", grade: 85},
        {name: "Bob", grade: 90},
        {name: "Charlie", grade: 85},
        {name: "David", grade: 90},
    }
    
    // Stable sort by grade - students with same grade keep their original order
    slices.SortStableFunc(students, func(a, b Student) int {
        return cmp.Compare(a.grade, b.grade)
    })
    
    fmt.Println(students)
    // Output: [{Alice 85} {Charlie 85} {Bob 90} {David 90}]
    // Notice Alice comes before Charlie (original order preserved)
}
```

**Interview tip**: Always clarify with your interviewer whether stability is required. It demonstrates your attention to detail and understanding of algorithmic nuances.

## Performance Considerations

Go's sorting functions are highly optimized:

- **Time complexity**: O(n log n) average and worst-case
- **Space complexity**: O(log n) for recursion stack
- **Algorithm**: Hybrid of quicksort and heapsort (introsort)

The implementation automatically switches algorithms based on data patterns, ensuring consistent performance across different inputs.

## Common Interview Patterns

### Pattern 1: Sort and Search
Many interview problems combine sorting with binary search:

```go
// Sort first, then use binary search
slices.Sort(numbers)
index := slices.BinarySearch(numbers, target)
```

### Pattern 2: Custom Comparisons
Problems often require sorting by computed values:

```go
// Sort points by distance from origin
slices.SortFunc(points, func(a, b Point) int {
    distA := a.x*a.x + a.y*a.y
    distB := b.x*b.x + b.y*b.y
    return cmp.Compare(distA, distB)
})
```

### Pattern 3: Multiple Sort Keys
Enterprise-level problems often involve complex sorting criteria:

```go
// Sort employees by department, then by salary (desc), then by name
slices.SortFunc(employees, func(a, b Employee) int {
    if deptComp := cmp.Compare(a.department, b.department); deptComp != 0 {
        return deptComp
    }
    if salaryComp := cmp.Compare(b.salary, a.salary); salaryComp != 0 { // Note: reversed for desc
        return salaryComp
    }
    return cmp.Compare(a.name, b.name)
})
```

## Quick Reference Guide

| Operation | Function | Key Points |
|-----------|----------|------------|
| Basic sort | `slices.Sort(slice)` | Works with comparable types, not stable |
| Custom sort | `slices.SortFunc(slice, cmp)` | Flexible, not stable |
| Stable custom sort | `slices.SortStableFunc(slice, cmp)` | Preserves equal element order |
| Comparison | `cmp.Compare(x, y)` | Returns -1, 0, or +1 |

## The Bottom Line

Mastering Go's sorting capabilities gives you a significant advantage in technical interviews. The key is knowing when to use each function and understanding the trade-offs between performance, stability, and code clarity.

Remember: great engineers don't just write working code—they write code that's maintainable, efficient, and demonstrates deep understanding of the tools at their disposal. Go's sorting functions help you achieve all three.

The next time you encounter a sorting problem in an interview, you'll have the confidence to choose the right tool for the job and explain your reasoning clearly. That's the difference between a good candidate and a great one.

*Want to dive deeper into Go's standard library? The `slices` package contains many other useful functions that can simplify your interview solutions. Happy coding!*
