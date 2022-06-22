---
Lesson: 8
Title: "Arrays"
Draft: false
---

In programming there is the One and the Many. One tree with Many branches. One
`string` with Many `rune` or `byte` types. One array to Many values. In
programming we commonly refer to these as collection types, because they
collect all the things! You can think of a fruit basket. It collects all the
fruits `[ 🍎, 🍐, 🍌 🍍, 🍓 ]` or a zoo collects all the animal `[ 🐒, 🦛, 🐴,
🐼, 🦫, 🦒 ]`

An array is no different. It collects whatever `type` you tell it to collect,
but! There is one important detail about arrays. They have limited space. Just
like a fruit basket can hold so many fruits and a zoo can only hold so many
animals, the same goes for arrays.

## Setup

Let's make our directory `array` and the files we want inside of that directory
`example_test.go` `array.go`

```sh
mkdir array
touch array/example_test.go array/array.go
```

Now let's open up `array.go` and for the very first line we'll add
```go
package array
```
Next for `example_test.go` for the very first line we'll add
```go
package array_test
```

## Initialize

The only thing we really need to know about arrays is how to initialize
(create) them, set values for each index, and get those values.

This is a really good time to mention that we count starting at 0 in software
engineering. So instead of 1, 2, 3. We say 0, 1, 2. Don't worry, you'll get
used to it over time. Everyone does 😜 And there is a very good reason for it,
but it's _waaaaay_ outta scope to learn about.

This is also a good time to explain what an index is. So we know we have our
array of 4 elements, but how do I get any of them? Well I have to call them
📲 by their number! Think of a hotel🏨 it is a collection of guests
`[ 🙍, 🤵, 🧕, 🧔, 🧜, 🧏 ]` who all have room numbers, yeah? So, if I wanted
to tell the guest in room 0 their pizza 🍕 arrived, who would I call? Room 0,
of course! Same thing, here. We give that room number a _special_ name, an
index.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### array.go

```go
// Arrays shows all the ways to make and manipulate arrays in Go.
func Arrays() {
	var arr [4]int
	fmt.Println("Length of arr:", len(arr))
	fmt.Println("Empty:", arr)
	arr[0] = 0
	arr[1] = 1
	arr[2] = 2
	arr[3] = 3
	fmt.Println("Filled:", arr)
	fmt.Println("Grab one value:", arr[3])

	inline := [4]int{2, 3, 4, 5}
	fmt.Println("Can be declared inline", inline)

	// If you don't want to count how many values, but you don't want a slice.
	// you can use the `...` syntax which will make an array of however many
	// values that you initialize it with.
	constSlice := [...]int{8, 9, 10, 11, 15}
	fmt.Printf("Just an array %T\nValues:%v", constSlice, constSlice)
}
```

#### example_test.go

```go
func ExampleArrays() {
	array.Arrays()
	// Output:
	// Length of arr: 4
	// Empty: [0 0 0 0]
	// Filled: [0 1 2 3]
	// Grab one value: 3
	// Can be declared inline [2 3 4 5]
	// Just an array [5]int
	// Values:[8 9 10 11 15]
}
```

## Matrix 😎

It's time... To hack into the ma-- JUST KIDDING! It's not that cool 🙃.
With all the special names I'm giving you, let's throw ⚾ in one more. When you
make an array of arrays, what do you get? A matrix! Just understand it's a name
for array of arrays, because that takes way too much effort to say.

Let's conceptualize what a matrix (or an array of arrays) actually looks like.
Let's go back to our hotel 🏨 example. We've upgraded! And instead of just one
floor, we're going to have three floors of six rooms each. Or in programming
terms `[3][6]Rooms` You can also think of it as `[rows][columns]Items` if that
helps.

```text
The hotel
[
  [ 🙍, 🤵, 🧕, 🧔, 🧜, 🧏 ], floor 0
  [ 🧗, ⛹️, 🧖, 👱, 🤸, 🙎 ], floor 1
  [ 🧍, 🧎, 💁, 🧘, 🧏, 🧕 ], floor 2
     0   1   2   3   4   5
     Room numbers
]
```

So if we wanted the person in the tuxedo 🤵 we would go to floor 0 room 1 to
get them, or in dev speak `[0][1]Rooms`

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### array.go

```go
// Matrix shows how to make a 2-dimensional array (a matrix) in Go.
func Matrix() {
	var matrix [4][5]int
	for i := 0; i < 4; i++ {
		for j := 0; j < 5; j++ {
			matrix[i][j] = i + j
		}
	}
	fmt.Println("Matrix:", matrix)
}
```

#### example_test.go

```go
func ExampleMatrix() {
	array.Matrix()
	// Output:
	// Matrix: [[0 1 2 3 4] [1 2 3 4 5] [2 3 4 5 6] [3 4 5 6 7]]
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png "gopher-victorious by Egon Elbre")

```go
package array

import "fmt"

// Arrays shows all the ways to make and manipulate arrays in Go.
func Arrays() {
	var arr [4]int
	fmt.Println("Length of arr:", len(arr))
	fmt.Println("Empty:", arr)
	arr[0] = 0
	arr[1] = 1
	arr[2] = 2
	arr[3] = 3
	fmt.Println("Filled:", arr)
	fmt.Println("Grab one value:", arr[3])

	inline := [4]int{2, 3, 4, 5}
	fmt.Println("Can be declared inline", inline)

	// If you don't want to count how many values, but you don't want a slice.
	// you can use the `...` syntax which will make an array of however many
	// values that you initialize it with.
	constSlice := [...]int{8, 9, 10, 11, 15}
	fmt.Printf("Just an array %T\nValues:%v", constSlice, constSlice)
}

// Matrix shows how to make a 2-dimensional array (a matrix) in Go.
func Matrix() {
	var matrix [4][5]int
	for i := 0; i < 4; i++ {
		for j := 0; j < 5; j++ {
			matrix[i][j] = i + j
		}
	}
	fmt.Println("Matrix:", matrix)
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif "typing-furiously by Egon Elbre")

```go
package array_test

import "basics/array"

func ExampleArrays() {
	array.Arrays()
	// Output:
	// Length of arr: 4
	// Empty: [0 0 0 0]
	// Filled: [0 1 2 3]
	// Grab one value: 3
	// Can be declared inline [2 3 4 5]
	// Just an array [5]int
	// Values:[8 9 10 11 15]
}

func ExampleMatrix() {
	array.Matrix()
	// Output:
	// Matrix: [[0 1 2 3 4] [1 2 3 4 5] [2 3 4 5 6] [3 4 5 6 7]]
}
```
