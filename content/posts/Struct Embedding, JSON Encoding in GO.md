---
layout: post
title: Struct Embedding, JSON Encoding in GO
tags: [programming,golang,json-encoding,json,struct-embeding]
date:  "2024-02-01"
---


- Embeding a struct (just writing the type) creates a “has a relationship” with the parent. If the child implements any interface parent can directly invoke those methods.
- If there are more than one embedding and they implement a common method it’s okay as long as we don’t attempt to directly invoke it on parent (trying to do so results in compilation error - ambiguous selector)
- While encoding json (marshalling), encoder internally checks if the type implements json.Marshaler interface. Thus in such cases marshaling such struct would result in the implementation of it’s type. (even if parent has exported fields those will be discarded)
- But what if struct has more than one embedded types and both of them implement json.Marshaler interface ?


### Sample Program

```go
package main

import (
	"encoding/json"
	"fmt"
	"time"
)

type FirstEmbed struct {
}

type SecondEmbed struct{}

func (s SecondEmbed) MarshalJSON() ([]byte, error) {
	return []byte(`"second_embed"`), nil
}

func (t FirstEmbed) MarshalJSON() ([]byte, error) {
	return []byte(`"first_embed"`), nil
}

func main() {
	// json.Marshaler
	result, _ := json.Marshal(struct {
		ID int
		time.Time
	}{1, time.Now()})
	fmt.Println("ID,time.Time encoded =>", string(result))

	result, _ = json.Marshal(struct {
		FirstEmbed
		time.Time
	}{
		FirstEmbed{},
		time.Now(),
	})
	fmt.Println("FirstEmbed, time.Time encoded =>", string(result))

	result, _ = json.Marshal(struct {
		FirstEmbed
	}{
		FirstEmbed{},
	})
	fmt.Println("FirstEmbed encoded =>", string(result))

	result, _ = json.Marshal(struct {
		SecondEmbed
	}{
		SecondEmbed{},
	})
	fmt.Println("SecondEmbed encoded =>", string(result))

	result, _ = json.Marshal(struct {
		FirstEmbed
		SecondEmbed
	}{
		FirstEmbed{},
		SecondEmbed{},
	})
	fmt.Println("FirstEmbed, SecondEmbed encoded =>", string(result))

	result, _ = json.Marshal(struct {
		FirstEmbed
		SecondEmbed
		time.Time
	}{
		FirstEmbed{},
		SecondEmbed{},
		time.Now(),
	})
	fmt.Println("FirstEmbed, SecondEmbed, time.Time encoded =>", string(result))

	// struct {
	// 	FirstEmbed
	// 	SecondEmbed
	// 	time.Time
	// }{
	// 	FirstEmbed{},
	// 	SecondEmbed{},
	// 	time.Now(),
	// }.MarshalJSON() -> attempting a direct invocation triggers ambiguous selector compilation error

	// whats the behavior when there are two embedded types (both from standard library)
	result, _ = json.Marshal(struct {
		time.Time
		json.RawMessage
	}{
		time.Now(),
		json.RawMessage{},
	})
	fmt.Println("time.Time, json.RawMessage encoded =>", string(result))

}
```


Output
```bash
ID,time.Time encoded => "2024-01-30T10:56:04.484165+05:45"
FirstEmbed, time.Time encoded => "2024-01-30T10:56:04.484429+05:45"
FirstEmbed encoded => "first_embed"
SecondEmbed encoded => "second_embed"
FirstEmbed, SecondEmbed encoded => {}
FirstEmbed, SecondEmbed, time.Time encoded => "2024-01-30T10:56:04.484492+05:45"
time.Time, json.RawMessage encoded => "2024-01-30T10:56:04.484503+05:45"
```