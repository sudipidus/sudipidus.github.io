---
layout: post
title: Loop Iterator variable scope in golang
tags: [functional-programming,programming,currying,golang]
date:  "2024-02-01"
---


### Per Iteration Vs Per Loop

In most of the languages, the loop variable (iterator) has a per iteration scope, meaning in each iteration a new copy of that variable is made. But in golang it's the same variable that has the scope throughout the loop. All the changes (increments) apply to the same placeholder and if you are temporarily capturing them and spawning go routines or creating a function placeholder (for later execution) you start to see unexpected consequences.

Note: This is going to change 1.22 onwards.

---

#### Example 
```go
package main

import (
	"fmt"
	"sync"
)

func main() {

	// Printing with normal loop
	for i := 0; i < 5; i++ {
		fmt.Println(i)
	}
	// Output: 01234

	// Invoking anon function go routine
	wg := &sync.WaitGroup{}
	for i := 0; i < 5; i++ {
		wg.Add(1)
		go func() {
			fmt.Print(i)
			wg.Done()
		}()
	}
	// No predictable output, for the most part it would print the final value of i
	// loop iterates much faster than go routines' execution

	// Fixing this by passing a separate copy
	for i := 0; i < 5; i++ {
		//copy := i
		wg.Add(1)
		go func() {
			fmt.Print(copy)
			wg.Done()
		}()
	}
	// Prints (01234) - not in that order because it's go routine

	// Fixing by passing it as a copy but as an argument to function since go passes by copy
	for i := 0; i < 5; i++ {
		wg.Add(1)
		go func(i int) {
			fmt.Print(i)
			wg.Done()
		}(i)
	}
	// Prints all (different order though)

	// Saving execution for later
	var executeLater []func()
	for i := 0; i < 5; i++ {
		executeLater = append(executeLater, func() { fmt.Print(i) })
	}

	for _, each := range executeLater {
		each()
	}
	// Prints 5555 for most part (again unpredictable)

	wg.Wait()
}

```



References:
- https://bugzilla.mozilla.org/show_bug.cgi?id=1619047 (let's encrypt bug)
- https://go.dev/blog/loopvar-preview (go official blog)