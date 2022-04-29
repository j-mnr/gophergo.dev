---
Lesson: 9
Title: "Slices"
Draft: false
---

We just got done speaking on arrays and it's a perfect time to start talking
about slices. The more versatile versions of arrays. This is because we
**don't** have to declare a size to a slice. Slices can grow indefinitely!
But wait! There's more! 😲 They also have certain global functions specific to
them! And to top it all off they come with a really nice optimization for
strings.

Also feel free to checkout the community driven [Slice
Tricks](https://github.com/golang/go/wiki/SliceTricks)! And if you want more
input from one of the creators of Go, Rob Pike, check out the [Go
Blog](https://go.dev/blog/slices) on it!

We'll take this 🥧 one _slice_ at a time 😹

## Setup

Let's make our directory `slices` and the files we want inside of that directory
`example_test.go` `slices.go`

```sh
mkdir slices
touch slices/example_test.go slices/slices.go
```

Now let's open up `slices.go` and for the very first line we'll add
```go
package slices
```
Next for `example_test.go` for the very first line we'll add
```go
package slices_test
```

## Initialize

Initializing (making) a slice is easier than an array, because we don't have to
think about how many elements we want in it, we just declare it.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### slices.go

```go
// SliceBasic shows how to create a slice and how to set and get values in it.
func SliceBasic() {
	// Don't add a number between the
	// `[]` brackets and we `make` slices if we
	// want to have a capacity and length
	slice := make([]string, 3)
	fmt.Println("empty:", slice)

	slice[0] = "|set zeroeth value|"
	slice[1] = "|set first value|"
	slice[2] = "|set second value|"
	fmt.Println("full:", slice)
	fmt.Println("pick a value:", slice[2])
	fmt.Println("capacity:", cap(slice))
	fmt.Println("length:", len(slice))

	inline := []int{0, 1, 2, 3, 4}
	fmt.Println("Can be declared inline", inline)
}
```

#### example_test.go

```go
func ExampleSliceBasic() {
	slices.SliceBasic()
	// Output:
	// empty: [  ]
	// full: [|set zeroeth value| |set first value| |set second value|]
	// pick a value: |set second value|
	// capacity: 3
	// length: 3
	// Can be declared inline [0 1 2 3 4]
}
```

## Append

The magic 🌈 of slices. The `append` builtin function allows us to take our
slice and append (add to the end) one or many values. While this is a _very_
nice feature. We need to understand that everything comes with a cost.

Imagine if your fridge **doubled** in size if you over filled it❗ You might
damage your roof or your walls 🏠 And now you are spending twice the amount of
money 💲 on electricity 🔌 but you do have a fridge that doubles in size....
Which is still pretty cool 😀

Just know, if you know how big you need your slice to be _ahead_ of time, you
should set that size. Just like in life if you know how much material you
need, you'll get that much.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### slices.go

Here we introduce the unpacking operator `...` which is further explained in
[functions](/basics/function/#variadic-arguments-varargs) So, don't worry
we'll get to it!

```go
// SliceAppend shows how to put more elements into a slice even if we don't
// have the capacity for it using `append`.
func SliceAppend() {
	// Why wouldn't I do this always?
	var slice []string
	// Good Question! Lets answer it!
	fmt.Println("capacity:", cap(slice))
	fmt.Println("length:", len(slice))

	slice = append(slice, "append a single value")
	slice = append(slice, "append", "multiple", "values")
	fmt.Println("capacity:", cap(slice))
	fmt.Println("length:", len(slice))
	fmt.Println("We had to go find more space! Which takes time and effort!")
	fmt.Println("slice:", slice)

	unpackAllThese := []string{"`...`", "is used to put", "all the values in", "at the same time"}
	slice = append(slice, unpackAllThese...)
	fmt.Println("capacity:", cap(slice))
	fmt.Println("length:", len(slice))
	fmt.Println("We had to go find even more space!!!")
	fmt.Println("slice:", slice)
}
```

#### example_test.go

```go
func ExampleSliceAppend() {
	slices.SliceAppend()
	// Output:
	// capacity: 0
	// length: 0
	// capacity: 4
	// length: 4
	// We had to go find more space! Which takes time and effort!
	// slice: [append a single value append multiple values]
	// capacity: 8
	// length: 8
	// We had to go find even more space!!!
	// slice: [append a single value append multiple values `...` is used to put all the values in at the same time]
}
```

## Copy

There are times when you need to copy values of one slice to another, without
changing the original slice. I won't go into it, but in general, we love 😍
immutability in programming. We love ❤️ when things can't change, because if
they can't change we don't have to worry about them. The `copy` builtin
function, does what it says it does, and allows us to imitate immutability. The
source slice is kept intact, while the destination slice receives a copy of the
values.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### slices.go

```go
// SliceCopy shows how to copy one slice into another slice using the builtin
// `copy` function.
func SliceCopy() {
	// src is short for source
	srcSlice := make([]int, 10)
	fmt.Println("empty srcSlice:", srcSlice)
	for i := 0; i < 10; i++ {
		srcSlice[i] = i
	}
	fmt.Println("full srcSlice:", srcSlice)

	// dst is short for destination
	dstSlice := make([]int, len(srcSlice))
	fmt.Println("empty dstSlice:", dstSlice)
	copy(dstSlice, srcSlice)
	fmt.Println("full dstSlice:", dstSlice)
}
```

#### example_test.go

```go
func ExampleSliceCopy() {
	slices.SliceCopy()
	// Output:
	// empty srcSlice: [0 0 0 0 0 0 0 0 0 0]
	// full srcSlice: [0 1 2 3 4 5 6 7 8 9]
	// empty dstSlice: [0 0 0 0 0 0 0 0 0 0]
	// full dstSlice: [0 1 2 3 4 5 6 7 8 9]
}
```

## Panic! At the Slice

We will cover `panic` in Go in a later lesson, but this could be your first
time running into it! I know it was mine 😂 That is, let's ask a question: What
happens if you try and access an index that isn't in the slice? 🤔

Well let's go back to our hotel analogy from the last lesson on arrays. If I
was a guest in room 2, but I accidentally went into room 3, I'd get in trouble.
I've gone somewhere that I shouldn't have access to!

Same thing happens with both arrays and slices. If you try and access memory
outside of the capacity, you're going to have a bad time ⏰

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### slices.go

I don't like showing off things we haven't learned yet, but later we will
understand what `panic`, `recover`, and `defer` are doing. For now copy and
paste and come back later after learning about `panic`, `recover` and `defer`.

```go
// SliceIndexOutOfRangePanic shows us what happens when we try to access an
// index that does not exist in a slice.
func SliceIndexOutOfRangePanic() {
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("slice paniced!\n", r)
		}
	}()

	sl := make([]int, 5)
	// Change -1 to 0 to see the panic happen at the other end of the slice.
	for i := -1; i < len(sl)+1; i++ {
		fmt.Println("XXX: this is going to panic before we ever see this!", sl[i])
	}
}
```

#### example_test.go

```go
func ExampleSliceIndexOutOfRangePanic() {
	slices.SliceIndexOutOfRangePanic()
	// Output:
	// slice paniced!
	//  runtime error: index out of range [-1]
}
```

## What's with the name?

So all the way up until now, we've been calling it a slice. Slice this, slice
that, slice with certain `len`, slice with certain `cap`. But why slice? If
you come from other programming languages you may be familiar with Lists. These
are, in fact, very similar to Lists in other languages, the biggest feature and
why they are called slice is because of how efficient the `slice` operation
with the slice operator `:` is. What efficiency you may ask? Well I'll let
[Rob](https://en.wikipedia.org/wiki/Rob_Pike) speak on it.

> An important consequence of this slice-like design for strings is that
> creating a substring is very efficient. All that needs to happen is the
> creation of a two-word string header. Since the string is read-only, the
> original string and the string resulting from the slice operation can share
> the same array safely. 
>  -- Rob Pike

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### slices.go

```go
// SliceSlices shows us why a slice is called a slice and that's because we can
// take slices (pieces) of a slice depending on our needs using the `:` slice
// operator.
func SliceSlices() {
	var slice = []string{"zero", "one", "two", "three", "four", "five"}
	fmt.Printf("sliceUpToThirdIndex: %v\nlength: %d capacity: %d\n",
		slice,
		len(slice),
		cap(slice))

	sliceUpToThirdIndex := slice[:3]
	fmt.Printf("sliceUpToThirdIndex: %v\nlength: %d capacity: %d\n",
		sliceUpToThirdIndex,
		len(sliceUpToThirdIndex),
		cap(sliceUpToThirdIndex))

	sliceStartAtIndexTwo := slice[2:]
	fmt.Printf("sliceStartAtIndexTwo: %v\nlength: %d capacity: %d\n",
		sliceStartAtIndexTwo,
		len(sliceStartAtIndexTwo),
		cap(sliceStartAtIndexTwo))

	sliceFromOneUpToFour := slice[1:4]
	fmt.Printf("sliceFromOneUpToFour: %v\nlength: %d capacity: %d\n",
		sliceFromOneUpToFour,
		len(sliceFromOneUpToFour),
		cap(sliceFromOneUpToFour))
	s := "Max Efficiency"
	fmt.Println(s[4:], "to the", s[:3], "for substrings")
}
```

#### example_test.go

```go
func ExampleSliceSlices() {
	slices.SliceSlices()
	// Output:
	// sliceUpToThirdIndex: [zero one two three four five]
	// length: 6 capacity: 6
	// sliceUpToThirdIndex: [zero one two]
	// length: 3 capacity: 6
	// sliceStartAtIndexTwo: [two three four five]
	// length: 4 capacity: 4
	// sliceFromOneUpToFour: [one two three]
	// length: 3 capacity: 5
	// Efficiency to the Max for substrings
}
```

## Matrix 😎

It's time ⏲️ to enter -- SYKE! -- if you were following along from last lesson
on arrays you knew this was coming, if not: A matrix is an array of arrays or
in this case a slice of slices! Why the fancy term? Well try saying slice of
slices over and over, it's just _too_ extra. Now we can grow our slices _in_
our growable slice 🤯. It's the exact same as array of arrays, but you can add
more if you want. So don't be intimidated, you got this! 💪😤

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### slices.go

```go
// SliceMatrix shows how to make a matrix also known as a 2d array, but still
// have the flexibility of slices!
func SliceMatrix() {
	// We will allocate three slices in a slice
	matrix := make([][]int, 3)
	fmt.Println("matrix empty:", matrix)
	for i := 0; i < 3; i++ {
		innerSliceLen := i + 1
		matrix[i] = make([]int, innerSliceLen)
		for j := 0; j < innerSliceLen; j++ {
			matrix[i][j] = i + j
		}
	}
	fmt.Println("matrix full:", matrix)

	// and we can treat each slice like we would any other slice.
	matrix[0] = append(matrix[0], 21)
	fmt.Println("matrix append first slice with value:", matrix)
}
```

#### example_test.go

```go
func ExampleSliceMatrix() {
	slices.SliceMatrix()
	// Output:
	// matrix empty: [[] [] []]
	// matrix full: [[0] [1 2] [2 3 4]]
	// matrix append first slice with value: [[0 21] [1 2] [2 3 4]]
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png)

```go
package slices

import "fmt"

// SliceBasic shows how to create a slice and how to set and get values in it.
func SliceBasic() {
	// Don't add a number between the
	// `[]` brackets and we `make` slices if we
	// want to have a capacity and length
	slice := make([]string, 3)
	fmt.Println("empty:", slice)

	slice[0] = "|set zeroeth value|"
	slice[1] = "|set first value|"
	slice[2] = "|set second value|"
	fmt.Println("full:", slice)
	fmt.Println("pick a value:", slice[2])
	fmt.Println("capacity:", cap(slice))
	fmt.Println("length:", len(slice))

	inline := []int{0, 1, 2, 3, 4}
	fmt.Println("Can be declared inline", inline)
}

// SliceAppend shows how to put more elements into a slice even if we don't
// have the capacity for it using `append`.
func SliceAppend() {
	// Why wouldn't I do this always?
	var slice []string
	// Good Question! Lets answer it!
	fmt.Println("capacity:", cap(slice))
	fmt.Println("length:", len(slice))

	slice = append(slice, "append a single value")
	slice = append(slice, "append", "multiple", "values")
	fmt.Println("capacity:", cap(slice))
	fmt.Println("length:", len(slice))
	fmt.Println("We had to go find more space! Which takes time and effort!")
	fmt.Println("slice:", slice)

	unpackAllThese := []string{"`...`", "is used to put", "all the values in", "at the same time"}
	slice = append(slice, unpackAllThese...)
	fmt.Println("capacity:", cap(slice))
	fmt.Println("length:", len(slice))
	fmt.Println("We had to go find even more space!!!")
	fmt.Println("slice:", slice)
}

// SliceCopy shows how to copy one slice into another slice using the builtin
// `copy` function.
func SliceCopy() {
	// src is short for source
	srcSlice := make([]int, 10)
	fmt.Println("empty srcSlice:", srcSlice)
	for i := 0; i < 10; i++ {
		srcSlice[i] = i
	}
	fmt.Println("full srcSlice:", srcSlice)

	// dst is short for destination
	dstSlice := make([]int, len(srcSlice))
	fmt.Println("empty dstSlice:", dstSlice)
	copy(dstSlice, srcSlice)
	fmt.Println("full dstSlice:", dstSlice)
}

// SliceIndexOutOfRangePanic shows us what happens when we try to access an
// index that does not exist in a slice.
func SliceIndexOutOfRangePanic() {
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("slice paniced!\n", r)
		}
	}()

	sl := make([]int, 5)
	// Change -1 to 0 to see the panic happen at the other end of the slice.
	for i := -1; i < len(sl)+1; i++ {
		fmt.Println("XXX: this is going to panic before we ever see this!", sl[i])
	}
}

// SliceSlices shows us why a slice is called a slice and that's because we can
// take slices (pieces) of a slice depending on our needs using the `:` slice
// operator.
func SliceSlices() {
	var slice = []string{"zero", "one", "two", "three", "four", "five"}
	fmt.Printf("sliceUpToThirdIndex: %v\nlength: %d capacity: %d\n",
		slice,
		len(slice),
		cap(slice))

	sliceUpToThirdIndex := slice[:3]
	fmt.Printf("sliceUpToThirdIndex: %v\nlength: %d capacity: %d\n",
		sliceUpToThirdIndex,
		len(sliceUpToThirdIndex),
		cap(sliceUpToThirdIndex))

	sliceStartAtIndexTwo := slice[2:]
	fmt.Printf("sliceStartAtIndexTwo: %v\nlength: %d capacity: %d\n",
		sliceStartAtIndexTwo,
		len(sliceStartAtIndexTwo),
		cap(sliceStartAtIndexTwo))

	sliceFromOneUpToFour := slice[1:4]
	fmt.Printf("sliceFromOneUpToFour: %v\nlength: %d capacity: %d\n",
		sliceFromOneUpToFour,
		len(sliceFromOneUpToFour),
		cap(sliceFromOneUpToFour))
	s := "Max Efficiency"
	fmt.Println(s[4:], "to the", s[:3], "for substrings")
}

// SliceMatrix shows how to make a matrix also known as a 2d array, but still
// have the flexibility of slices!
func SliceMatrix() {
	// We will allocate three slices in a slice
	matrix := make([][]int, 3)
	fmt.Println("matrix empty:", matrix)
	for i := 0; i < 3; i++ {
		innerSliceLen := i + 1
		matrix[i] = make([]int, innerSliceLen)
		for j := 0; j < innerSliceLen; j++ {
			matrix[i][j] = i + j
		}
	}
	fmt.Println("matrix full:", matrix)

	// and we can treat each slice like we would any other slice.
	matrix[0] = append(matrix[0], 21)
	fmt.Println("matrix append first slice with value:", matrix)
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif)

```go
package slices_test

import "basics/slices"

func ExampleSliceBasic() {
	slices.SliceBasic()
	// Output:
	// empty: [  ]
	// full: [|set zeroeth value| |set first value| |set second value|]
	// pick a value: |set second value|
	// capacity: 3
	// length: 3
	// Can be declared inline [0 1 2 3 4]
}

func ExampleSliceAppend() {
	slices.SliceAppend()
	// Output:
	// capacity: 0
	// length: 0
	// capacity: 4
	// length: 4
	// We had to go find more space! Which takes time and effort!
	// slice: [append a single value append multiple values]
	// capacity: 8
	// length: 8
	// We had to go find even more space!!!
	// slice: [append a single value append multiple values `...` is used to put all the values in at the same time]
}

func ExampleSliceCopy() {
	slices.SliceCopy()
	// Output:
	// empty srcSlice: [0 0 0 0 0 0 0 0 0 0]
	// full srcSlice: [0 1 2 3 4 5 6 7 8 9]
	// empty dstSlice: [0 0 0 0 0 0 0 0 0 0]
	// full dstSlice: [0 1 2 3 4 5 6 7 8 9]
}

func ExampleSliceIndexOutOfRangePanic() {
	slices.SliceIndexOutOfRangePanic()
	// Output:
	// slice paniced!
	//  runtime error: index out of range [-1]
}

func ExampleSliceSlices() {
	slices.SliceSlices()
	// Output:
	// sliceUpToThirdIndex: [zero one two three four five]
	// length: 6 capacity: 6
	// sliceUpToThirdIndex: [zero one two]
	// length: 3 capacity: 6
	// sliceStartAtIndexTwo: [two three four five]
	// length: 4 capacity: 4
	// sliceFromOneUpToFour: [one two three]
	// length: 3 capacity: 5
	// Efficiency to the Max for substrings
}

func ExampleSliceMatrix() {
	slices.SliceMatrix()
	// Output:
	// matrix empty: [[] [] []]
	// matrix full: [[0] [1 2] [2 3 4]]
	// matrix append first slice with value: [[0 21] [1 2] [2 3 4]]
}
```
