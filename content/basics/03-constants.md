---
Lesson: 3
Title: "Constants"
Draft: false
---

Constants are variables that cannot be changed.

If you have taken any math or physics classes you've probably used a constant
before. Pi `π` is a constant and the speed of light `c` is a constant.

There are times in programming you want to declare something, but don't want it
to change. For example, we might want a default format `string` for our
`fmt.Printf` functions or logging. You might have a very special number that
limits a resource, like the number of `goroutines` (coming soon) or the number
of connections to a database. A constant is perfect for these situations!

## Allowable Constants

Constants are super useful and seen throughout many codebases, in Go, you're
only allowed to declare certain types as constant. Types that can be guaranteed
to not change, can be constant. Many of these values are known as primitive
types. `int`, `rune` (aka `int32`), `byte` (aka `uint8`) `float`, `bool`. The
only exception is a `string` which can also be declared as constant! This may
or may not make since to you if you've dealt with strings in other programming
languages.

Let's expand a little on why a `string` is an exception. We need to
understand that just like `byte` is an alias for `uint8` (unsigned integer with
8 slots), `string` is an alias for `[]byte` or what's known as a slice of
bytes. We haven't gotten into slices yet, (coming soon) but just know they can
change. In programming when something can change it's called mutable. I'll say
it again, slices are mutable. Constants **cannot** change and are known as
immutable.

Now the question! How is a mutable slice of bytes (`[]byte`) able to be
declared as an immutable value (`const`)? Well, I lied 🙃 though a `string`
is _basically_ a slice of bytes (`[]byte`) it holds a little more extra
information than what a normal slice holds **and** guarantees that each `byte`
(the letters and characters) in a string cannot be changed. Therefore the slice
cannot be changed **and** the values inside, the bytes, cannot either. This
guarantees `string` to be immutable.

This may come as a surprise if you've tried to change a `string`, because
you're allowed to do this: `var twoStrings = "string1" + "string2"` and it will
come out as `string1string2` if you `fmt.Println` it. The reason it works is
because an entirely **new**❗❗ `string` is being made in it's place. Done out
of convenience, expectations, or legacy it is what it is. Just know when you
add to a `string` you're making an entirely **new** `string` and that's
expensive in programming.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### contants.go

```go
// Stuck is an untyped string
const Stuck = "This variable can never be reassigned."

// Stuck = "This won't work, Stuck is constant!"
// XXX: cannot assign to Stuck (untyped string constant "This variable can
// never be reassigned.")

// HeartEyes is an untyped rune
const HeartEyes = '😍'

// Arithmetic is an untyped float
const Arithmetic = 600 / 3.421

// AlwaysTrue is an untyped bool
const AlwaysTrue = true

// MaxByteValue is a constant byte value. To let the compiler know we wanted a
// byte instead of an int, we can specifically tell it we want that value.
const MaxByteValue byte = 255

// Create a grouping of const values, not needed to type the `const` keyword
// over and over again. This also works for `var`!
const (
	IsConst                   = true
	IsInGrouping              = true
	OneAndQuarterAsUntypedInt = 5 / 4
)

// However you **cannot** declare arrays, slices, maps, or structs constant.
// XXX: (value of type [2]string) is not constant
// const myArray = [2]string{"won't", "work"}
// const mySlice = []string{"still", "doesn't", "work"}
// const myMap = map[string]int{}
// const me = struct{ name string }{name: "Jay"}
```

#### example_test.go

```go
func ExampleStuck() {
	fmt.Println(constants.Stuck)
	// Output:
	// This variable can never be reassigned.
}

func ExampleHeartEyes() {
	// We can convert constants just like variables
	fmt.Println(string(constants.HeartEyes))
	// Output:
	// 😍
}

func ExampleArithmetic() {
	fmt.Println(constants.Arithmetic)
	// Output:
	// 175.38731365097925
}

func ExampleAlwaysTrue() {
	fmt.Println(constants.AlwaysTrue)
	// Output:
	// true
}
```

## Untyped Constants

Everything in Go has a type.... Unless it's a constant 🤷 At least not until it
is actually used in the our application.

### Uses

Why is this important and what does it mean? Sometimes we want to put certain
values into a function _but_ Go needs us to convert it to a specific `type`.
Wouldn't it just be nice if we didn't have to do that small conversion?
Constants actually allow this! Enums (coming soon) in Go are `const` values and
therefore have underlying types that can be converted

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### constants.go

```go
// UntypedConst shows that constants can have values that will be automatically
// converted to the necessary type that the function needs at runtime.
func UntypedConst() {
	// const values do not have a type and therefore are very useful when you
	// don't want to have to do explicit casting.
	const untyped = 42
	// We don't care what this function does, we only care what it looks like
	// math.IsInf(float64, int)
	fmt.Println(math.IsInf(untyped, untyped))
	// If we try this with typed int we have to cast it.
	var typed int = 42 // or typed := 42
	fmt.Println(math.IsInf(float64(typed), typed))
}
```

#### example_test.go

```go
func ExampleUntypedConst() {
	constants.UntypedConst()
	// Output:
	// false
	// false
}
```

This may still be a little confusing as I've pulled in something from the
standard library, that we're not familiar with yet **and** it's math 😬 So
let's do one more! This time _we'll_ make our own `type` 😄

#### constants.go

```go
const UntypedString = "I fit wherever the underlying type of something is a string!"

// myString has an underlying type of `string` **but** it's `type` is myString
type myString string

// Print takes in a myString type and prints it to standard out.
func Print(s myString) { fmt.Println(s) }
```

#### example_test.go

```go
func ExamplePrint() {
	constants.Print(constants.UntypedString)
	// Output:
	// I fit wherever the underlying type of something is a string!
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png)

```go
package constants

import (
	"fmt"
	"math"
)

// Stuck is an untyped string
const Stuck = "This variable can never be reassigned."

// Stuck = "This won't work, Stuck is constant!"
// XXX: cannot assign to Stuck (untyped string constant "This variable can
// never be reassigned.")

// HeartEyes is an untyped rune
const HeartEyes = '😍'

// Arithmetic is an untyped float
const Arithmetic = 600 / 3.421

// AlwaysTrue is an untyped bool
const AlwaysTrue = true

// MaxByteValue is a constant byte value. To let the compiler know we wanted a
// byte instead of an int, we can specifically tell it we want that value.
const MaxByteValue byte = 255

// Create a grouping of const values, not needed to type the `const` keyword
// over and over again. This also works for `var`!
const (
	IsConst                   = true
	IsInGrouping              = true
	OneAndQuarterAsUntypedInt = 5 / 4
)

// However you **cannot** declare arrays, slices, maps, or structs constant.
// XXX: (value of type [2]string) is not constant
// const myArray = [2]string{"won't", "work"}
// const mySlice = []string{"still", "doesn't", "work"}
// const myMap = map[string]int{}
// const me = struct{ name string }{name: "Jay"}

// UntypedConst shows that constants can have values that will be automatically
// converted to the necessary type that the function needs at runtime.
func UntypedConst() {
	// const values do not have a type and therefore are very useful when you
	// don't want to have to do explicit casting.
	const untyped = 42
	// We don't care what this function does, we only care what it looks like
	// math.IsInf(float64, int)
	fmt.Println(math.IsInf(untyped, untyped))
	// If we try this with typed int we have to cast it.
	var typed int = 42 // or typed := 42
	fmt.Println(math.IsInf(float64(typed), typed))
}

// UntypedString shows us that it is flexible to be **any** type that has
// `string` as the underlying type.
const UntypedString = "I fit wherever the underlying type of something is a string!"

// myString has an underlying type of `string` **but** it's `type` is myString
type myString string

// Print takes in a myString type and prints it to standard out.
func Print(s myString) { fmt.Println(s) }
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif)

```go
package constants_test

import (
	"basics/constants"
	"fmt"
)

func ExampleStuck() {
	fmt.Println(constants.Stuck)
	// Output:
	// This variable can never be reassigned.
}

func ExampleHeartEyes() {
	// We can convert constants just like variables
	fmt.Println(string(constants.HeartEyes))
	// Output:
	// 😍
}

func ExampleArithmetic() {
	fmt.Println(constants.Arithmetic)
	// Output:
	// 175.38731365097925
}

func ExampleAlwaysTrue() {
	fmt.Println(constants.AlwaysTrue)
	// Output:
	// true
}

func ExampleUntypedConst() {
	constants.UntypedConst()
	// Output:
	// false
	// false
}

func ExamplePrint() {
	constants.Print(constants.UntypedString)
	// Output:
	// I fit wherever the underlying type of something is a string!
}
```
