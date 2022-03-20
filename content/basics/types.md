---
title: "Types"
draft: false
lesson: 1
---

Types are the building blocks of all applications. They are like the words of a
language.

We use words to make sentences and sentences to make paragraphs.

We use types to declare variables and variables to make functions.

## Setup

Let's make our directory `types` and the files we want inside of that directory
`example_test.go` `types.go`

```sh
mkdir types
touch types/example_test.go types/types.go
```

Now let's open up `types.go` and for the very first line we'll add
```go
package types
```
Next for `example_test.go` for the very first line we'll add
```go
package types_test
```

## Bool

![https://twitter.com/egonelbre](/gopher-wondering.png)

A `bool` is a way for us to control the flow of our application. You can think
of `bool` as a forked road. You are given two choices -- go right or go left.
That is all it can do, but don't let that fool you, it's a very powerful tool
that is seen **everywhere**. `bool` is most useful when you want to execute a
piece of code _only_ when a certain criteria is met.

### Uses

![https://twitter.com/egonelbre](/gopher-idea.png)

Let's say you write a program that will greet people, but you want it to greet
someone special 😉 with a personal message. How could you do that? You would
check if the person has that special person's name and if it did, `fmt.Println`
a special message for them! 😘

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

```go
// Bool shows us the type bool which is short for boolean. A bool is either
// true or false.
func Bool() {
	fmt.Println("true || false = true. false is a bool Type")
	fmt.Printf("%t || %t = %t. %t is a %T Type",
		true, false, true || false, false, false)
}
```

## String

![https://twitter.com/egonelbre](/gopher-wondering.png)

A `string` is named as such because it's a string of letters. In computer
speak, there is no such thing as letters or characters, it's all numbers! 🤯 So
we need to tell the computer that we want all of these numbers (represented as
letters or characters) to be stringed together. You may have heard the term
"string of text" as well, same thing.

To give a visual example, the word "example" is a string of the letters
e-x-a-m-p-l-e

It is also good to know when writing code in `Go` a string **must** have
quotation marks around it. `"Gopher"` is a `string`, but `Gopher` is a
variable.

### Uses

![https://twitter.com/egonelbre](/gopher-idea.png)

`string` is used everytime you want to write something ... which is a lot of
the time. 😂 It would be very hard to program without them.

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

```go
// String shows us how to make a sequence of characters (or runes in Go) in a
// row, surrounded by double quote marks "".
func String() {
	fmt.Println("go", "+", "gopher", "=", `"gogopher"`, "and is of Type string")
	fmt.Printf("%s + %s = %q and is of Type %T",
		"go", "gopher", "go"+"gopher", "")
}
```

## Rune

![https://twitter.com/egonelbre](/gopher-wondering.png)

A `rune` is the letters or characters of a `string`. If you have come from
other programming language you may have heard of the `char` type. They are
similar, but a `rune` can occupy **all** letters and characters of the entire
world. The `char` type found in other languages, is restricted to the [Extended
ASCII Table](https://www.lookuptables.com/text/extended-ascii-table) which only
has 256 values. This is known in `go` as a `byte`, in other words `char` and
`byte` are the same thing or in programming: `char == byte`

For example `'a'` is a `rune` and a `byte`, but `'第'` is _only_ a `rune`.

A `rune` **must** be surrounded by single quotation marks. `'X'` is a `rune`,
`"X"` is a `string` and `X` is a variable.

### Uses

![https://twitter.com/egonelbre](/gopher-idea.png)

Anytime you're dealing with the letters in a `string` you're dealing with a
`rune` or a `byte` and in programming we deal with **lots** of `string` types,
so getting comfortable with `rune` will get you comfortable with manipulating
`string` types

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

```go
// Rune shows us how to represent and manipulate each value in a string.
func Rune() {
	fmt.Println("'k' is an int32 Type. When strings are built, they use rune" +
			"values. Another way to say rune is int32, they mean the same thing!\n" +
			"'k' is actually 107")
		fmt.Printf("'%s' is an %T Type. When strings are built, they use rune"+
				"values. Another way to say rune is int32, they mean the same thing!\n'%s'"+
				"is actually %d", string('k'), 'k', string('k'), 'k')
}
```

## Int

![https://twitter.com/egonelbre](/gopher-wondering.png)

An `int` is an integer or what we would think of as a regular number. All of
these are of type `int`:

`-1, -2000, 2000, 1, 0, 895059, -1234567890, 9876543210`

It does need to be said there are different _sizes_ of the `int` type. That
means depending on which `int` you use, there is a limit to how high or how low
you can go. For example, if you use `int32` it means you can go from
`-2,147,483,648` to `2,147,483,647` if you go out of bounds, you'll find
yourself in trouble! 🤕 Though you're safe in most cases when you just use
`int`, so don't worry about it for now. We'll cover it later; just know it
exists.

### Uses

![https://twitter.com/egonelbre](/gopher-idea.png)

Many times you need to manipulate numbers in programming, whether that be for
the amount of seconds you want to wait `time.Sleep(1 * time.Second)` or if you
want to give something a name, without much overhead (This is known as an enum)
you can give it an `int` type with a good descriptive name; doing math,
grabbing an index, etc., there are many reasons to use the `int` type.

### Coding Time!

![https://twitter.com/egonelbree](/gopher-heart-eyes.png)

```go
// Int short for integer shows us how to use the int type in Go. We can also do
// arithmetic like we would expect.
func Int() {
	fmt.Println("2 + 2 =", 2+2, "and is of Type int")
	fmt.Printf("%d + %d = %d and is of Type %T", 2, 2, 2+2, 0)
}
```

## Float

![https://twitter.com/egonelbre](/gopher-wondering.png)

`float` is short for decimal floating point. Which is a mouthful 👀 So it's a
good thing we just call it `float`. A `float` is just like an `int` except that
there is a point `.` somewhere that will give it precision. For example all of
these are of type `float`:

`123456.78, 8765.43, 123.00, 1.2345678, 1234567.8, 0.000012345678, 0.0
12345678000000000.1`

And also I pointed out before that there are different types of `int`, the same
goes with `float`. There is `float32` and `float64`, all you really need to
know is `float64` can hold bigger numbers than `float32` and leave it at that
for now.

### Uses

![https://twitter.com/egonelbre](/gopher-idea.png)

When you're looking for more precision than what an `int` will give you, like
with data science or number crunching or dollar amounts, distances, etc., then
you'll want to use a `float`.

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

```go
// Float short for floating point number shows how to represent numbers with
// decimal values in Go.
func Float() {
	fmt.Println("1.23 + 4.56 =", 1.23+4.56, "and is of Type float64")
	fmt.Printf("%.2f + %.2f = %.2f and is of Type %T", 1.23, 4.56, 1.23+4.56, 0.0)
}
```

## Complex

![https://twitter.com/egonelbre](/gopher-wondering.png)

A `complex` number is a number which comprises of a real and an imaginary part.

I'll be honest and say I don't use these at all 😅 but they are here for
completeness and if you had a need to deal with `complex` numbers, then `Go`
has them for you! 😁 👍

And for a final note on different types, there is a smaller `complex64` and a
bigger `complex128`.

### Uses

![https://twitter.com/egonelbre](/gopher-idea.png)

According to some article I just DDG'd (DuckDuckGo) 🥸

> electricity, quadratic equations, signal processing in cellular technology
> and wireless technologies, as well as radar and biology.

Neat 🙂

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

```go
// Complex shows how to use complex numbers in Go... If you would ever need
// them ¯\_(ツ)_/¯
func Complex() {
	fmt.Println("(2.94-2.31i) + (1.43+2.65i) = (4.37+0.341i)" +
		" and is of Type complex128")
	fmt.Printf("%.3g + %.3g = %.3g and is of Type %T",
		cmplx.Acos(-5+1i), cmplx.Acos(1+-7i), cmplx.Acos(-5+1i)+cmplx.Acos(1+-7i),
		cmplx.Acos(0))
}
```

## The Whole File

![https://twitter.com/egonelbre](/gopher-victorious.png)

```go
package types

import (
	"fmt"
	"math/cmplx"
)

// Bool shows us the type bool which is short for boolean. A bool is either
// true or false.
func Bool() {
	fmt.Println("true || false = true. false is a bool Type")
	fmt.Printf("%t || %t = %t. %t is a %T Type",
		true, false, true || false, false, false)
}

// String shows us how to make a sequence of characters (or runes in Go) in a
// row, surrounded by double quote marks "".
func String() {
	fmt.Println("go", "+", "gopher", "=", `"gogopher"`, "and is of Type string")
	fmt.Printf("%s + %s = %q and is of Type %T",
		"go", "gopher", "go"+"gopher", "")
}

// Rune shows us how to represent and manipulate each value in a string.
func Rune() {
	fmt.Println("'k' is an int32 Type. When strings are built, they use rune" +
		"values. Another way to say rune is int32, they mean the same thing!\n" +
		"'k' is actually 107")
	fmt.Printf("'%s' is an %T Type. When strings are built, they use rune"+
		"values. Another way to say rune is int32, they mean the same thing!\n'%s'"+
		"is actually %d", string('k'), 'k', string('k'), 'k')
}

// Int short for integer shows us how to use the int type in Go. We can also do
// arithmetic like we would expect.
func Int() {
	fmt.Println("2 + 2 =", 2+2, "and is of Type int")
	fmt.Printf("%d + %d = %d and is of Type %T", 2, 2, 2+2, 0)
}

// Float short for floating point number shows how to represent numbers with
// decimal values in Go.
func Float() {
	fmt.Println("1.23 + 4.56 =", 1.23+4.56, "and is of Type float64")
	fmt.Printf("%.2f + %.2f = %.2f and is of Type %T", 1.23, 4.56, 1.23+4.56, 0.0)
}

// Complex shows how to use complex numbers in Go... If you would ever need
// them ¯\_(ツ)_/¯
func Complex() {
	fmt.Println("(2.94-2.31i) + (1.43+2.65i) = (4.37+0.341i)" +
		" and is of Type complex128")
	fmt.Printf("%.3g + %.3g = %.3g and is of Type %T",
		cmplx.Acos(-5+1i), cmplx.Acos(1+-7i), cmplx.Acos(-5+1i)+cmplx.Acos(1+-7i),
		cmplx.Acos(0))
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/typing-furiously.gif)

```go
package types_test

import "basics/types"

func ExampleBool() {
	types.Bool()
	// Output:
	// true || false = true. false is a bool Type
	// true || false = true. false is a bool Type
}

func ExampleString() {
	types.String()
	// Output:
	// go + gopher = "gogopher" and is of Type string
	// go + gopher = "gogopher" and is of Type string
}

func ExampleRune() {
	types.Rune()
	// Output:
	// 'k' is an int32 Type. When strings are built, they use rune values.
	// Another way to say rune is int32, they mean the same thing!
	// 'k' is actually 107
	// 'k' is an int32 Type. When strings are built, they use rune values.
	// Another way to say rune is int32, they mean the same thing!
	// 'k' is actually 107
}

func ExampleInt() {
	types.Int()
	// Output:
	// 2 + 2 = 4 and is of Type int
	// 2 + 2 = 4 and is of Type int
}

func ExampleFloat() {
	types.Float()
	// Output:
	// 1.23 + 4.56 = 5.79 and is of Type float64
	// 1.23 + 4.56 = 5.79 and is of Type float64
}

func ExampleComplex() {
	types.Complex()
	// Output:
	// (2.94-2.31i) + (1.43+2.65i) = (4.37+0.341i) and is of Type complex128
	// (2.94-2.31i) + (1.43+2.65i) = (4.37+0.341i) and is of Type complex128
}
```
