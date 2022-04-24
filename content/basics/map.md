---
title: "Maps"
draft: false
lesson: TODO
---

Time for maps! 🗺️ and just like maps we use to point to specific locations in
the world, a `map` in Go in no different! We use a `key` to point to a `value`.
In other languages they are called Dictionaries, Hash Tables, or Associative
Arrays. [Wikipedia](https://en.wikipedia.org/wiki/Associative_array) provides
us with a showing of just how timeless ⏳ this data structure truly is.

## Setup

Let's make our directory `maps` and the files we want inside of that directory
`example_test.go` `maps.go`

```sh
mkdir maps
touch maps/example_test.go maps/maps.go
```

Now let's open up `maps.go` and for the very first line we'll add
```go
package maps
```
Next for `example_test.go` for the very first line we'll add
```go
package maps_test
```

## Basic Operations

When using a `map` there really is only three 3️⃣ things to worry about.

1. Adding key-value pairs
1. Removing key-value pairs 
1. Getting key-value pairs.

The same syntax we saw in the previous lesson on slices apply to maps. So we
can set keys `myMap[0] = 4` like we do with a slice index `mySlice[0] = 4`. So
it has the same functionality that a slice does, then why bother ❓ Well, we
can do more than just `int`! For example: `myMap["myString"]`,
`myMap[myStruct]`, `myMap[false]` are all valid!

OK we've just learned it has _more_ functionality than slices, 🤔 does that
make slices obsolete? Well no, the two data structures serve different
purposes. A map **does not** have a guaranteed order for it's values.

When do we use a `map` in place of a `slice`? Well like in real life, when we
have some map 🗺️ we have a _specific_ location we're pointing to. We _know_
that we want to go a certain place given `key` coordinates; with a `slice` we
can put all of our gophers

{{< inline-img "/looking-around.gif" >}}

in a row.

Think of it like a hammer 🔨 and a screwdriver🪛. We could probably screw in a
screw with the back of a hammer and smash a nail with the end of a screwdriver,
but it'd be much easier using the right tool for the right job.

Therefore when we have values that we want to be able to point to specifically,
and want to be able to add and remove things quickly without worrying about
order, we'll use a `map`. When we have values that we want in an order, we'll
use a `slice`.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### maps.go

```go
// MapBasic shows how to make a new map and how to add and remove keys and
// values to it.
func MapBasic() {
	myMap := make(map[string]int, 4) // We can leave out the size too!
	myMap["key1"] = 100
	myMap["key2"] = 20
	myMap["key3"] = -3
	myMap["key4"] = 0xc0ffee
	fmt.Println("print myMap:", myMap)

	delete(myMap, "key1")
	delete(myMap, "key2")
	delete(myMap, "key3")
	delete(myMap, "key4")
	fmt.Println("empty after deleting myMap keys:", myMap)
}
```

#### example_test.go

```go
func ExampleMapBasic() {
	maps.MapBasic()
	// Output:
	// print myMap: map[key1:100 key2:20 key3:-3 key4:12648430]
	// empty after deleting myMap keys: map[]
}
```

## Check for values

Maps 🗺️ need to be updated from time to time because location can change, but
you wouldn't update a location if it hadn't been changed, right? And also what
if Google maps told you to take a right on a road that has a no entry 🚫 sign!
So we need a way to _check_ ✅ if a value exists for a certain key _then_ we
can do something with it.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### maps.go

```go
// MapValueExists shows how to check if a value exists in a given map or not.
func MapValueExists() {
	inlineMap := map[string]string{"key1": "VALUE1", "key2": "VALUE2"}
	// Will return zero values if the value is not present.
	val1, present := inlineMap["key1"]
	fmt.Printf("value: %v is present? %t\n", val1, present)

	none, found := inlineMap["non-existent"]
	fmt.Printf("value: %v is present? %t\n", none, found)

	if val, exists := inlineMap["key2"]; exists {
		fmt.Println("value:", val, "is there do something with it.")
	}
	if val, ok := inlineMap["non-existent"]; ok {
		fmt.Println("This statement will never be reached!", val)
	}
}
```

#### example_test.go

```go
func ExampleMapValueExists() {
	maps.MapValueExists()
	// Output:
	// value: VALUE1 is present? true
	// value:  is present? false
	// value: VALUE2 is there do something with it.
}
```


## Set

We just saw that a `slice` is used when we want a collection of a certain
`type` that has an order to it. We now know we want to use a `map` when we
don't care about the order and just want to be able to grab a value from a
given key (key-value pair). Well, what if we don't care about the order **and**
we don't care about any value?

Enter the [Set](https://en.wikipedia.org/wiki/Set_(abstract_data_type)). If you
come from another programming language you are probably already aware what it
is. A `set` has attributes of both a `slice` and a `map`. It only has values
inside of it (like a slice), but it has no order (like a map).

It is important to note there is no actual `set` in Go, but that's because we
don't need one. A `set` is just a `map` with no values. 🪄 Ta da!

There is something important to note, however❗ In Go, the empty struct
`struct{}` is much like the `nil` value because it signals nothing is there,
and what makes it special is, it is a `type`. So when reading Go code, if you
find a `struct{}` somewhere, just know it's a placeholder. It only signals to
the reader, us, that a `type` is required, but we don't use that `type` for
anything, just the underlying structure.

[Read why struct{} is soooo good!](https://dave.cheney.net/2014/03/25/the-empty-struct)

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### maps.go

```go
// MapAsSet demonstrates how to create a `set` data structure -- an unordered
// collection of some type with very quick lookup and insertion -- the idomatic
// Go way.
//
// The time to use a `set` is when you don't care about the order of your
// values and you want to be able to add, delete, or get values instantly!
func MapAsSet() {
	type important string
	// struct{} takes up no space. When we see it we can think of a typed `nil`
	mySet := make(map[important]struct{})
	// We don't have to explicitly type cast these as "[ANYTHING HERE]" is an
	// untyped string and will type itself to what it needs to at runtime.
	mySet["fast lookup"] = struct{}{}
	mySet["unordered"] = struct{}{}
	mySet["fast insert"] = struct{}{}
	mySet["collection"] = struct{}{}

	// Simulate getting a string from some other function. We want to check if
	// our `set` has that key, so we type cast it `important(...)` and do
	// something with the information that our `set` has that key.
	gotFromOtherFunc := "fast insert"
	if _, found := mySet[important(gotFromOtherFunc)]; found {
		fmt.Println("We found the key we were looking for!")
		delete(mySet, important(gotFromOtherFunc))
	} else {
		fmt.Println("We didn't find that key, so let's do something else!")
		mySet[important(gotFromOtherFunc)] = struct{}{}
	}
}
```

#### example_test.go

```go
func ExampleMapAsSet() {
	maps.MapAsSet()
	// Output:
	// We found the key we were looking for!
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png)

```go
package maps

import "fmt"

// https://en.wikipedia.org/wiki/Associative_array

// MapBasic shows how to make a new map and how to add and remove keys and
// values to it.
func MapBasic() {
	myMap := make(map[string]int, 4) // We can leave out the size too!
	myMap["key1"] = 100
	myMap["key2"] = 20
	myMap["key3"] = -3
	myMap["key4"] = 0xc0ffee
	fmt.Println("print myMap:", myMap)

	delete(myMap, "key1")
	delete(myMap, "key2")
	delete(myMap, "key3")
	delete(myMap, "key4")
	fmt.Println("empty after deleting myMap keys:", myMap)
}

// MapValueExists shows how to check if a value exists in a given map or not.
func MapValueExists() {
	inlineMap := map[string]string{"key1": "VALUE1", "key2": "VALUE2"}
	// Will return zero values if the value is not present.
	val1, present := inlineMap["key1"]
	fmt.Printf("value: %v is present? %t\n", val1, present)

	none, found := inlineMap["non-existent"]
	fmt.Printf("value: %v is present? %t\n", none, found)

	if val, exists := inlineMap["key2"]; exists {
		fmt.Println("value:", val, "is there do something with it.")
	}
	if val, ok := inlineMap["non-existent"]; ok {
		fmt.Println("This statement will never be reached!", val)
	}
}

// MapAsSet demonstrates how to create a `set` data structure -- an unordered
// collection of some type with very quick lookup and insertion -- the idomatic
// Go way.
//
// The time to use a `set` is when you don't care about the order of your
// values and you want to be able to add, delete, or get values instantly!
func MapAsSet() {
	type important string
	// struct{} takes up no space. When we see it we can think of a typed `nil`
	mySet := make(map[important]struct{})
	// We don't have to explicitly type cast these as "[ANYTHING HERE]" is an
	// untyped string and will type itself to what it needs to at runtime.
	mySet["fast lookup"] = struct{}{}
	mySet["unordered"] = struct{}{}
	mySet["fast insert"] = struct{}{}
	mySet["collection"] = struct{}{}

	// Simulate getting a string from some other function. We want to check if
	// our `set` has that key, so we type cast it `important(...)` and do
	// something with the information that our `set` has that key.
	gotFromOtherFunc := "fast insert"
	if _, found := mySet[important(gotFromOtherFunc)]; found {
		fmt.Println("We found the key we were looking for!")
		delete(mySet, important(gotFromOtherFunc))
	} else {
		fmt.Println("We didn't find that key, so let's do something else!")
		mySet[important(gotFromOtherFunc)] = struct{}{}
	}
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif)

```go
package maps_test

import "basics/maps"

func ExampleMapBasic() {
	maps.MapBasic()
	// Output:
	// print myMap: map[key1:100 key2:20 key3:-3 key4:12648430]
	// empty after deleting myMap keys: map[]
}

func ExampleMapValueExists() {
	maps.MapValueExists()
	// Output:
	// value: VALUE1 is present? true
	// value:  is present? false
	// value: VALUE2 is there do something with it.
}

func ExampleMapAsSet() {
	maps.MapAsSet()
	// Output:
	// We found the key we were looking for!
}
```
