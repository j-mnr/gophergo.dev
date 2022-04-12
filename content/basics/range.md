---
title: "Range"
draft: false
lesson: TODO
---

The `range` keyword works the same way we use it in speech.

Example 1
> What are your feelings on all of the Star Wars movies?

> Eh, my feelings range from hot garbage to best movies of all time.

Example 2
> If you come to my town we have a **huge** range of restaurants to choose
> from. 🌮🍙🍺🍲 🥙

Example 3
> Move in closer! You're not in range of the camera! 📸

We use `range` to specify a start and an end and to move through each one of
those items until we've seen the whole collection. These collection of items
can be any of the ones we talked about in previous lessons (arrays, slices,
strings, maps) and a new `type` channel or `chan`(coming soon!).

## Setup

Let's make our directory `ranges` and the files we want inside of that directory
`example_test.go` `ranges.go`

```sh
mkdir ranges
touch ranges/example_test.go ranges/ranges.go
```

Now let's open up `ranges.go` and for the very first line we'll add
```go
package ranges
```
Next for `example_test.go` for the very first line we'll add
```go
package ranges_test
```

## Understanding Why

Before we look into `range` let's understand why it's even in the language. If
you're new to programming you may have figured out you can grab each value in
an array by using a for loop.

```go
myArr := [5]int{0, 1, 2, 3, 4}
for i := 0; i < 5; i++ {
	num := myArr[i]
	fmt.Println(num)
}
```

and if you recall in the [slice](/basics/slice#append) lesson, when we learned
about `append` we found how to get the length of a slice `len(mySlice)` so you
can do the same for slices.

```go
mySlice := []int{0, 1, 2, 3, 4}
mySlice = append(mySlice, 5, 6, 7, 8)
for i := 0; i < len(mySlice); i++ {
	num := mySlice[i]
	fmt.Println(num)
}
```

So what's the big deal? 🤔 Do we need another word we have to learn `range` to
do the _exact_ thing, we can already accomplish? Good question! 😁 Ask
everything! You'll get very far in programming with that mentality. 👍

There are major advantages for using the `range` keyword:

### Lexical Scoping

Like the lesson on [if/else](/basics/if-else#scoped-in-if-statement) shows we
can scope what we care about in the `if` block. The `range` keyword does the
same for our index/key and value.

### Extended

It is nice to work on arrays and slices, but it also works on `string`, `map`
and `chan` types as well.

### Universal

You know exactly what you're trying to accomplish with `range` unlike a `for`
loop

```go
sl2 := []int{1, 2, 3}
sl1 := []string{"one", "two", "three"}
for i := 0; i < len(sl1); i++ {
	s := sl2[i]
	fmt.Println("my string, right? 🙃", s)
}
//////////////////////////////////////////
i := 4
sl1 := []string{"one", "two", "three"}
for i < len(sl1) {
	fmt.Println("Can't see me 👁️", sl1[i])
}
```

## By Index

Now we want to know that we will start at the beginning of our slice and we
want to go through all of the indexes (or indices) to the end of it, how can we
know _for sure_ that will happen?

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### ranges.go

```go
// RangeIndex shows us how to grab the index of each element in a slice.
func RangeIndex() {
	uselessSlice := make([]struct{}, 10)
	for i := range uselessSlice {
		fmt.Println("index:", i)
	}
	// You can also inline this!
	// for i := range make([]struct{}, 10) {
	// 	fmt.Println("index:", i)
	// }
}
```

#### example_test.go

```go
func ExampleRangeIndex() {
	ranges.RangeIndex()
	// Output:
	// index: 0
	// index: 1
	// index: 2
	// index: 3
	// index: 4
	// index: 5
	// index: 6
	// index: 7
	// index: 8
	// index: 9
}
```

## By Value

Now we really don't care about the index, we just want the values inside of the
slice, how can we accomplish this? In Go, you can't have an unused variable,
_but_ 🍑 you can have an `_` underscored variable. With this we're clearly
stating to Go

> I know this variable is not used and I am choosing _not_ to use it.

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### ranges.go

```go
// RangeValues shows that we can ignore the index using a range loop if we
// don't need it.
func RangeValues() {
	friends := []string{"Gabby", "Gorm", "Gunter"}
	// You cannot have unused variables in Go, but you can use _ to ignore them.
	for _, f := range friends {
		fmt.Println("friend:", f)
	}
}
```

#### example_test.go

```go
func ExampleRangeValues() {
	ranges.RangeValues()
	// Output:
	// friend: Gabby
	// friend: Gorm
	// friend: Gunter
}
```

## By Index And Value

For completeness, what if we are going to use both the index and the value in a
`range` statement? Then we **Just Do It!**

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### ranges.go

```go
// RangeIndexAndValues shows how to grab both the index and the value of each
// element in a slice.
func RangeIndexAndValues() {
	nums := []int{1, 2, 3, 4, 5}
	for i, n := range nums {
		fmt.Printf("index: %d, access value: %d, range value: %d\n", i, nums[i], n)
		// This can also be written as: nums[i] = n * n
		nums[i] *= n
	}
	fmt.Println("nums:", nums)
}
```

#### example_test.go

```go
func ExampleRangeIndexAndValues() {
	ranges.RangeIndexAndValues()
	// Output:
	// index: 0, access value: 1, range value: 1
	// index: 1, access value: 2, range value: 2
	// index: 2, access value: 3, range value: 3
	// index: 3, access value: 4, range value: 4
	// index: 4, access value: 5, range value: 5
	// nums: [1 4 9 16 25]
}
```

## Range Through Map

We are now at a place where a simple `for` loop wouldn't help. How would we
tell the for loop to go through every _key-value pair_ of a map with a simple
number❓ Instead of that `range` makes it much easier for us and if you
remember the lesson on [maps](/basics/map#basic-operations) they and slices
share a lot of similarities. It's just using the right tool 🔧 for the right
job 🔩, they both have a key to access a value. That's why we can use a `map`
just like a `slice` of some type.

⚠️  **WARNING!** ⚠️
The key-value pairs that come from `range` do **not** have to be in the order
they were placed. Meaning if you run our `example_test.go` a couple of times
with `go test` you'll see it will fail! 🤯 This is expected! The order of a map
is **not** guaranteed.

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### ranges.go

```go
// RangeMap shows that we can loop through the entries of a map (key and value)
// using range. Maps are not ordered in Go!
func RangeMap() {
	isMarried := map[string]bool{"Gaph": true, "Gene": false, "Gable": false}
	for key, val := range isMarried {
		if val == true {
			fmt.Println(key, "is married.")
		} else {
			fmt.Println(key, "is not married.")
		}
	}
}
```

#### example_test.go

```go
func ExampleRangeMap() {
	// XXX: This may fail from time to time!
	// There is no order in maps!
	// Why not run it a few times to see? 🙂
	ranges.RangeMap()
	// Output:
	// Gaph is married.
	// Gene is not married.
	// Gable is not married.
}
```

## Range Through String

We're going to talk in-depth about runes (coming soon!), but it's good to know
that by using `range`, a `string` will produce an index and a `rune` **not** a
`byte`. This is for native support with
[UTF-8](https://blog.hubspot.com/website/what-is-utf-8) strings, or more
broadly I could say strings that support **all** the languages. So you can
write Go in whatever language you so please. 🥰

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### ranges.go

```go
// RangeString shows that we can get the index and rune value of each character
// in a string.
func RangeString() {
	for i, r := range "gophergo.dev" {
		fmt.Println("index:", i, "rune:", r, "representation:", string(r))
	}
}
```

#### example_test.go

```go
func ExampleRangeString() {
	ranges.RangeString()
	// Output:
	// index: 0 rune: 103 representation: g
	// index: 1 rune: 111 representation: o
	// index: 2 rune: 112 representation: p
	// index: 3 rune: 104 representation: h
	// index: 4 rune: 101 representation: e
	// index: 5 rune: 114 representation: r
	// index: 6 rune: 103 representation: g
	// index: 7 rune: 111 representation: o
	// index: 8 rune: 46 representation: .
	// index: 9 rune: 100 representation: d
	// index: 10 rune: 101 representation: e
	// index: 11 rune: 118 representation: v
}
```

## Range Through Channel

We will talk about channels (coming soon!) later on after covering more
fundamental topics, but we're talking about `range` right now and if we ever
come back to this lesson we'll have a complete look at what types `range` works
with.

For now write ✏️ 🗒️ this down and come back to it after learning about channels
to soak it all in 🧽

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### ranges.go

```go
// RangeChannel shows that we can grab values from a channel until it is
// closed.
func RangeChannel() {
	ch := make(chan string, 6)
	ch <- "We can get"
	ch <- "values from a channel"
	ch <- "continuously."
	ch <- "Just make sure"
	ch <- "you close the channel"
	ch <- "at some time 😉"
	// XXX: Make sure the channel is closed!
	// You will be stuck in an infinite loop without it.
	close(ch)
	for val := range ch {
		fmt.Println(val)
	}
}
```

#### example_test.go

```go
func ExampleRangeChannel() {
	ranges.RangeChannel()
	// Output:
	// We can get
	// values from a channel
	// continuously.
	// Just make sure
	// you close the channel
	// at some time 😉
}
```

## Lexical Scoping

We've talked about scoping before and we'll talk about it some more. Scoping is
what allows certain variables to live 👶 and die 💀 in certain spaces. Scoping
is _soooo_ important in programming that JS developers got so fed up with their
`var` keyword [that they made two more](https://www.freecodecamp.org/news/understanding-let-const-and-var-keywords/), _just_ for scope.

In Go we don't have those troubles, the language is very well designed, but
what we do have is human minds 🧠 and without necessary information we may feel
like things don't make sense 😵

When I first started programming it was so weird to me that even though I
**have** my value from the `for` loop, it wouldn't allow me to change that
value in my collection. This is better explained with code, so...

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### ranges.go

```go
// RangeScopedValues shows that you get copies of the values of all of the
// collections (slice, string, map) that we can range over and changing those
// values does NOT change the collection.
func RangeScopedValues() {
	scopedSlice := []int{0, 1, 2, 3, 4}
	scopedString := "NOT changed"
	scopedMap := map[string]bool{"x": true, "y": true, "z": true}
	fmt.Println("Try to change by just the value")
	for _, num := range scopedSlice {
		b := num
		num = 9
		fmt.Println("before:", b, "after:", num)
		fmt.Println("Never changes:", scopedSlice)
	}
	fmt.Println("Same!", scopedSlice)
	for _, r := range scopedString {
		b := r
		r = 'X'
		fmt.Println("before:", b, "after:", r)
	}
	fmt.Println("Same!", scopedString)
	for _, val := range scopedMap {
		b := val
		val = false
		fmt.Println("before:", b, "after:", val)
	}
	fmt.Println("Same!", scopedMap)
	fmt.Println()
	fmt.Println("Change by index (by dereference)")
	for i, n := range scopedSlice {
		if n < 4 {
			scopedSlice[i] = 9
		}
	}
	fmt.Println("Changed!", scopedSlice)
	// XXX: Remember strings are immutable! You can't do this!
	// for i, _ := range scopedString {
	// 	scopedString[i] = byte('X')
	// 	scopedString[i] = 'X'
	// }
	byteSlice := []byte(scopedString)
	for i, b := range byteSlice {
		// if our byte is lowercase we change it.
		if b >= 'a' && b <= 'z' {
			byteSlice[i] = 'X'
		}
	}
	fmt.Println("Changed!", string(byteSlice))
	for key := range scopedMap {
		scopedMap[key] = false
	}
	fmt.Println("Changed!", scopedMap)
}
```

#### example_test.go

```go
func ExampleRangeScopedValues() {
	ranges.RangeScopedValues()
	// Output:
	// Try to change by just the value
	// before: 0 after: 9
	// Never changes: [0 1 2 3 4]
	// before: 1 after: 9
	// Never changes: [0 1 2 3 4]
	// before: 2 after: 9
	// Never changes: [0 1 2 3 4]
	// before: 3 after: 9
	// Never changes: [0 1 2 3 4]
	// before: 4 after: 9
	// Never changes: [0 1 2 3 4]
	// Same! [0 1 2 3 4]
	// before: 78 after: 88
	// before: 79 after: 88
	// before: 84 after: 88
	// before: 32 after: 88
	// before: 99 after: 88
	// before: 104 after: 88
	// before: 97 after: 88
	// before: 110 after: 88
	// before: 103 after: 88
	// before: 101 after: 88
	// before: 100 after: 88
	// Same! NOT changed
	// before: true after: false
	// before: true after: false
	// before: true after: false
	// Same! map[x:true y:true z:true]
	//
	// Change by index (by dereference)
	// Changed! [9 9 9 9 4]
	// Changed! NOT XXXXXXX
	// Changed! map[x:false y:false z:false]
}
```

### Scoping Wrap up

When we create a `for` loop and we assign values to that for loop

`for i, n := range numbers {...}`

here 👆 👆 we can see we're _assigning_ values, right? Meaning we're picking 🤏
an index and a value from our numbers slice and _assigning_ it to `i` and `n`
and just like in an `if/else` statement, those values are **scoped** 🔭 to that
`for` loop. They are copies from the numbers slice, not the actual values.

## The Whole File

![https://twitter.com/egonelbre](/gopher-victorious.png)

```go
package ranges

import "fmt"

// RangeIndex shows us how to grab the index of each element in a slice.
func RangeIndex() {
	uselessSlice := make([]struct{}, 10)
	for i := range uselessSlice {
		fmt.Println("index:", i)
	}
	// You can also inline this!
	// for i := range make([]struct{}, 10) {
	// 	fmt.Println("index:", i)
	// }
}

// RangeValues shows that we can ignore the index using a range loop if we
// don't need it.
func RangeValues() {
	friends := []string{"Gabby", "Gorm", "Gunter"}
	// You cannot have unused variables in Go, but you can use _ to ignore them.
	for _, f := range friends {
		fmt.Println("friend:", f)
	}
}

// RangeIndexAndValues shows how to grab both the index and the value of each
// element in a slice.
func RangeIndexAndValues() {
	nums := []int{1, 2, 3, 4, 5}
	for i, n := range nums {
		fmt.Printf("index: %d, access value: %d, range value: %d\n", i, nums[i], n)
		// This can also be written as: nums[i] = n * n
		nums[i] *= n
	}
	fmt.Println("nums:", nums)
}

// RangeMap shows that we can loop through the entries of a map (key and value)
// using range. Maps are not ordered in Go!
func RangeMap() {
	isMarried := map[string]bool{"Gaph": true, "Gene": false, "Gable": false}
	for key, val := range isMarried {
		if val == true {
			fmt.Println(key, "is married.")
		} else {
			fmt.Println(key, "is not married.")
		}
	}
}

// RangeString shows that we can get the index and rune value of each character
// in a string.
func RangeString() {
	for i, r := range "gophergo.dev" {
		fmt.Println("index:", i, "rune:", r, "representation:", string(r))
	}
}

// RangeChannel shows that we can grab values from a channel until it is
// closed.
func RangeChannel() {
	ch := make(chan string, 6)
	ch <- "We can get"
	ch <- "values from a channel"
	ch <- "continuously."
	ch <- "Just make sure"
	ch <- "you close the channel"
	ch <- "at some time 😉"
	// XXX: Make sure the channel is closed!
	// You will be stuck in an infinite loop without it.
	close(ch)
	for val := range ch {
		fmt.Println(val)
	}
}

// RangeScopedValues shows that you get copies of the values of all of the
// collections (slice, string, map) that we can range over and changing those
// values does NOT change the collection.
func RangeScopedValues() {
	scopedSlice := []int{0, 1, 2, 3, 4}
	scopedString := "NOT changed"
	scopedMap := map[string]bool{"x": true, "y": true, "z": true}
	fmt.Println("Try to change by just the value")
	for _, num := range scopedSlice {
		b := num
		num = 9
		fmt.Println("before:", b, "after:", num)
		fmt.Println("Never changes:", scopedSlice)
	}
	fmt.Println("Same!", scopedSlice)
	for _, r := range scopedString {
		b := r
		r = 'X'
		fmt.Println("before:", b, "after:", r)
	}
	fmt.Println("Same!", scopedString)
	for _, val := range scopedMap {
		b := val
		val = false
		fmt.Println("before:", b, "after:", val)
	}
	fmt.Println("Same!", scopedMap)
	fmt.Println()
	fmt.Println("Change by index (by dereference)")
	for i, n := range scopedSlice {
		if n < 4 {
			scopedSlice[i] = 9
		}
	}
	fmt.Println("Changed!", scopedSlice)
	// XXX: Remember strings are immutable! You can't do this!
	// for i, _ := range scopedString {
	// 	scopedString[i] = byte('X')
	// 	scopedString[i] = 'X'
	// }
	byteSlice := []byte(scopedString)
	for i, b := range byteSlice {
		// if our byte is lowercase we change it.
		if b >= 'a' && b <= 'z' {
			byteSlice[i] = 'X'
		}
	}
	fmt.Println("Changed!", string(byteSlice))
	for key := range scopedMap {
		scopedMap[key] = false
	}
	fmt.Println("Changed!", scopedMap)
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/typing-furiously.gif)

```go
package ranges_test

import "basics/ranges"

func ExampleRangeIndex() {
	ranges.RangeIndex()
	// Output:
	// index: 0
	// index: 1
	// index: 2
	// index: 3
	// index: 4
	// index: 5
	// index: 6
	// index: 7
	// index: 8
	// index: 9
}

func ExampleRangeValues() {
	ranges.RangeValues()
	// Output:
	// friend: Gabby
	// friend: Gorm
	// friend: Gunter
}

func ExampleRangeIndexAndValues() {
	ranges.RangeIndexAndValues()
	// Output:
	// index: 0, access value: 1, range value: 1
	// index: 1, access value: 2, range value: 2
	// index: 2, access value: 3, range value: 3
	// index: 3, access value: 4, range value: 4
	// index: 4, access value: 5, range value: 5
	// nums: [1 4 9 16 25]
}

func ExampleRangeMap() {
	// XXX: This may fail from time to time!
	// There is no order in maps!
	// Why not run it a few times to see? 🙂
	ranges.RangeMap()
	// Output:
	// Gaph is married.
	// Gene is not married.
	// Gable is not married.
}

func ExampleRangeString() {
	ranges.RangeString()
	// Output:
	// index: 0 rune: 103 representation: g
	// index: 1 rune: 111 representation: o
	// index: 2 rune: 112 representation: p
	// index: 3 rune: 104 representation: h
	// index: 4 rune: 101 representation: e
	// index: 5 rune: 114 representation: r
	// index: 6 rune: 103 representation: g
	// index: 7 rune: 111 representation: o
	// index: 8 rune: 46 representation: .
	// index: 9 rune: 100 representation: d
	// index: 10 rune: 101 representation: e
	// index: 11 rune: 118 representation: v
}

func ExampleRangeChannel() {
	ranges.RangeChannel()
	// Output:
	// We can get
	// values from a channel
	// continuously.
	// Just make sure
	// you close the channel
	// at some time 😉
}

func ExampleRangeScopedValues() {
	ranges.RangeScopedValues()
	// Output:
	// Try to change by just the value
	// before: 0 after: 9
	// Never changes: [0 1 2 3 4]
	// before: 1 after: 9
	// Never changes: [0 1 2 3 4]
	// before: 2 after: 9
	// Never changes: [0 1 2 3 4]
	// before: 3 after: 9
	// Never changes: [0 1 2 3 4]
	// before: 4 after: 9
	// Never changes: [0 1 2 3 4]
	// Same! [0 1 2 3 4]
	// before: 78 after: 88
	// before: 79 after: 88
	// before: 84 after: 88
	// before: 32 after: 88
	// before: 99 after: 88
	// before: 104 after: 88
	// before: 97 after: 88
	// before: 110 after: 88
	// before: 103 after: 88
	// before: 101 after: 88
	// before: 100 after: 88
	// Same! NOT changed
	// before: true after: false
	// before: true after: false
	// before: true after: false
	// Same! map[x:true y:true z:true]
	//
	// Change by index (by dereference)
	// Changed! [9 9 9 9 4]
	// Changed! NOT XXXXXXX
	// Changed! map[x:false y:false z:false]
}
```
