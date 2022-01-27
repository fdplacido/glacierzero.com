---
title: "Using Go Maps: pointers and value types"
date: 2022-01-27T00:11:38+01:00
images: ["/blog/img/go_hash_maps.png"]
tags: ["go", "data structures"]
draft: false
---

Hash maps are often a good data structure when we want to access or store pairs of associated data; pairs commonly known as key-value. 

What makes a hash table (a map) a great data structure is that internally it will compute an index based on the key, that will point us to where our value is. This is what makes them so fast to insert _O(1)_, delete _O(1)_ and search _O(1)_ for most cases (on average) with unordered data, while taking _O(n)_ space. This makes them much faster (on average) than arrays to search, insert and delete elements on it.

Given a key, we directly know where in memory our value will be. Not everything is great about hash tables though. Elements will be sparsely distributed in memory, which makes it cache **un**-friendly, you can have [collisions]() when generating the indices, and the worst-case scenarios are usually _O(n)_ and must be taken into consideration.

Go lang inadvertently exposes some of this behaviour when trying to perform certain operations on them, like assigning a struct field in a map. Using go maps one may try to do something like this:

```go
package main

type MyStruct struct {
    ID      int
    Amount  float64
}

func main() {
    operations := make(map[string]MyStruct)
    operations["Name1"] = MyStruct{256, 1.234}
    operations["Name1"].Amount = 5.678
}
```

for what we would get the following error:

```sh
./got1.go:11:32: cannot assign to struct field operations["Name1"].Amount in map
```

We can add new key-values to the map with no issue:

```go
operations["Name2"] = MyStruct{128, 9.876}
```

But we cannot modify an existing struct value. This is because in Go, map values are not [addressable](https://go.dev/ref/spec#Address_operators). In Go maps values can change their location in memory, for example when a hash map grows it may reallocate to use extra space; in that case the old memory locations will become invalid, so assigning this way is not allowed.

There are various solutions or ways around this issue.
We can read the value into a new variable and then assign the new variable to that key:

```go
operations := make(map[string]MyStruct)
operations["Name1"] = MyStruct{256, 1.234}
mod_op := operations["Name1"]
mod_op.Amount = 5.678
operations["Name1"] = mod_op
```

A similar approach is to check if there is a value: Go returns a copy of the value and a bool to know if there was a value or not. Since we get a copy of the value, we can modify it and re-assign it:

```go
operations := make(map[string]MyStruct)
operations["Name1"] = MyStruct{256, 1.234}
if op, ok := operations["Name1"]; ok {
    op.Amount = 5.678
    operations["Name1"] = op
} else {
  // not ok: There was no value for "Name1"
}
```

A third approach is to directly use pointers: a map of keys to pointers to values. This approach can have a performance benefit, specially for big structs if these are being copied:

```go
operations := make(map[string]*MyStruct)
operations["Name1"] = &MyStruct{256, 1.234}
operations["Name1"].Amount = 5.678
```

Go lang Map design is tricky when coming from other languages. Under the hood maps are pointers to to [`runtime.hmap`](https://go.dev/src/runtime/map.go), and not values as one might expect, causing the confusion.