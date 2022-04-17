---
title: "Function"
draft: false
lesson: TODO
---

It's time for the **FUNC** 🪩🕺 Can you handle it?

Function that is and of course you can handle it! 😄 We've been using them this
entire time. We've needed some preliminary exposure to other keywords before we
can dig into functions. So let's get `func`-y 😹 and boogie on down to our next
lesson.

In it's purest form a function takes in zero to many inputs and produces one to
many outputs. We can think of the simple addition operator `+` as a function,
because reliably **every** time you put a number to its left and put a number
to its right then outputted is another number `4 + 5 --> 9`. That means `+`
would be a function that takes in two inputs and produces one output.

## Setup

Let's make our directory `functions` and the files we want inside of that directory
`example_test.go` `functions.go`

```sh
mkdir functions
touch functions/example_test.go functions/functions.go
```

Now let's open up `functions.go` and for the very first line we'll add
```go
package functions
```
Next for `example_test.go` for the very first line we'll add
```go
package functions_test
```

## Private Functions

This is going to be the first time we'll be explicitly talking about package
visibility, but we've been using it this entire time. In Go there is no
keywords dedicated to `private` or `public` things. You make something public
by using TitleCase and you make something private by using camelCase.

Why would someone want a private... anything? 🤔 First off it helps with
readability, if a variable or function is private I **know** that it isn't used
outside of a package. Where as if something is Public ... It could be used
anywhere 😵 Second it can help break up code into small easily understood
units. Sometimes functions can span 100s of lines❗ It makes code much
easier to read seeing small private function calls. Let's not do a full example
but we can read this:

```go
func EvaluateSpeed(difficulty string) (int, error) {
	test, err := createTest(difficulty)
	if err != nil {
		return 0, err
	}
	g := findWillingGopher(test)
	seconds, err := doTypingTest(g)
	if err != nil {
		return 0, err
	}
	return seconds, nil
}
```

We don't know what each of those calls are doing but it reads like a story.

> 1. Create a test with a difficulty.
> 1. If error (difficulty doesn't exist, maybe?) return error
> 1. Find a gopher who's wants to participate in the typing test
> 1. Have gopher do the test, which produces how long it took or an error
> 1. If error (spilled tea 🍵 on keyboard, maybe?) return error and 0 seconds
> 1. Return how long it took them

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### functions.go

```go
// privateFunc is an example function, that is not exported. It is not always
// necessary to document unexported or private functions, because they won't
// show up in any documentation. It is only a good idea when the function does
// something that you wouldn't expect and should explain it.
func privateFunc() {
	fmt.Println("This function can only be called from within this package.")
}
```

#### example_test.go

```go
// This will not work, uncomment it and see what error it gives you.
// func ExampleprivateFunc() {
// 	// XXX: privateFunc not exported by package functions
// 	functions.privateFunc()
// }
```

## Public Functions

Now over to the Public side of things. We've been using them the entire time,
but lets understand why. When we create our tests we are under a different
package `functions_test` this is by design, and **WOW** what a good design it
is. 👏👏👏 We get to experience what our clients would use and we test our
application's behavior.

I could go on for _days_ about how well the `package <MY_PACKAGE>_test` is
designed, but all we need to know is we are making our functions public so that
we can call them outside of the package and yes, even though they look very
similar (`functions` and `functions_test`) they are completely separated
packages.

This means you've known how to call functions outside of the packages we've
been creating this entire time! 😁 So don't be afraid when calling something
like `fmt.Printf`. You're literally doing the same thing that the language
designers did in their package `fmt` and would you look at that 😲, they even
have a {{< anchor-new-window "fmt_test"
"https://cs.opensource.google/go/go/+/refs/tags/go1.18.1:src/fmt/example_test.go"
>}} package. 😄

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### functions.go

```go
// FuncPublic is an example function, that is exported. It is always a good
// idea to document your exported functions and variables, so that other
// developers can know how to use your code! Run `go doc --all .` in your
// terminal in this package and see what you get!
func FuncPublic() {
	fmt.Println("This function is exported and can be called anywhere.")
}
```

#### example_test.go

```go
func ExampleFuncPublic() {
	functions.FuncPublic()
	// Output:
	// This function is exported and can be called anywhere.
}
```

## Adding Parameters To Functions

A parameter is what you call your inputs to a function. Let's return to our
example with `+`. When adding two numbers we can generalize it to:

`(numberOne int) + (numberTwo int) --> int`

We call `numberOne` and `numberTwo` parameters.

You may also here some people refer to them as arguments. In most contexts it's
fine to use parameter and argument interchangeably, but know there is a
difference between the two and when we talk about making a function the correct
word is parameter. When calling the already made function, you pass arguments.
Everyone would understand if you said parameter or argument to a function
though, so no big deal.

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### functions.go

```go
// FuncWithParams is an example function, that shows you how to pass in
// multiple arguments to a function and use them.
func FuncWithParams(name string, value int, emoji rune) {
	fmt.Printf("%s looks like %s and is a %d/10", name, string(emoji), value)
}
```

#### example_test.go

```go
func ExampleFuncWithParams() {
	functions.FuncWithParams("Mechanical Arm", 9, '🦾')
	// Output:
	// Mechanical Arm looks like 🦾 and is a 9/10
}
```

## Return an Output

A true function has an output. If you have a function that takes in inputs but
doesn't have outputs, that's referred to as a procedure (There's a famous style
of programming called -- procedural programming), but like before it's not
really important to know that. If you call a function with no outputs a
function, it's fine.

When we want to return something from our function it works like the example of
the plus operator `(numberOne int) + (numberTwo int) --> int` and now let's
turn it Go style!

`func Add(numberOne int, numberTwo int) int { return numberOne + numberTwo }`

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### functions.go

```go
// FuncWithReturn is an example function on how to specify what type you want a
// function to return.
func FuncWithReturn() string {
	return "It's just this easy to return a type"
}
```

#### example_test.go

```go
func ExampleFuncWithReturn() {
	fmt.Println(functions.FuncWithReturn())
	// Output:
	// It's just this easy to return a type
}
```

## Return Multiple Types

A very nice feature to have in Go is returning whatever we want and how many
we want. There are other languages that don't allow for this, which if you're
unlucky enough to use those languages, then you have to do wacky 🤪 things to
return your multiple types in some _ugly, hackish_ manner.

This won't work with our previous example so let's think of modifying it 🤔
Maybe we want to return a number _and_ a boolean if the number is greater than
1000, so that we can do something special with it.

```go
func Add(n1 int, n2 int) (int, bool) {
	sum := n1 + n2
	if sum > 1000 {
		return sum, true
	}
	return sum, false
}
```

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### functions.go

```go
// FuncWithMultipleReturn is an example function that will return two types at
// the same time.
func FuncWithMultipleReturn() ([]int, bool) {
	canDoMultipleReturns := true
	return []int{1, 2, 3, 4, 5}, canDoMultipleReturns
}
```

#### example_test.go

```go
func ExampleFuncWithMultipleReturn() {
	fmt.Println(functions.FuncWithMultipleReturn())
	// Output:
	// [1 2 3 4 5] true
}
```

## Returns With Names!

With our last example it wasn't really obvious _why_ we were returning `true`
or `false`. You had to read the source code to know why the function would
return `true` or `false`. So how can we fix that? 🤔 Well, two things✌️
documentation and documentation 😂 We can document what the function does
**and** we can document the return types with a name! 🤯

```go
// Add takes in two numbers adds them together and checks if they are over
// 1000. It always returns the sum and for the second output returns true if
// the sum is over (greater than) one thousand.
func Add(n1 int, n2 int) (sum int, gtThousand bool) {
	sum = n1 + n2
	if sum > 1000 {
		return sum, true
	}
	return sum, false
}
```

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### functions.go

```go
// FuncWithNamedReturn is an example function that shows how you can name all
// of your parameters and all of your return types if you want to. You will
// notice we don't have to specify the type over and over if they are the same
// type. i.e. (email string, url string) == (email, url string)
func FuncWithNamedReturn(name, scheme, host, path, query string) (email, url string) {
	// Notice we don't use `:=` for email and url. The function already makes
	// them for us when we named them up above.
	email = name + "@" + host
	url = scheme + host + path + query
	// An empty return will look for your 2 named returns and output them.
	return
	// return email, url also works! And is more readable, so go with this. 👍
}
```

#### example_test.go

```go
func ExampleFuncWithNamedReturn() {
	fmt.Println(functions.FuncWithNamedReturn("Gamba",
		"https://", "gophergo.dev", "/fun-with-funcs", "?isFun=yes&isEasy=yes"))
	// Output:
	// Gamba@gophergo.dev https://gophergo.dev/fun-with-funcs?isFun=yes&isEasy=yes
}
```

## Variadic Arguments (Varargs)

What in the GREAT GOOGLY MOOGLY 👀 is a **variadic** _anything_ ⁉️  Great
question! 😁 It has some formal definition that we don't care about. All we
need to know is -- it is usually shortened to `varargs` and means you can have a
_variable_ number of arguments (parameters) of a certain `type`. More plainly,
the function can take zero, one, or many parameters of a certain `type`.

It, essentially, gives more control 🎛️ to the caller (the person calling our
function), but makes our lives (the writer of the function) a little more
difficult. I'll explain more during coding in `example_test.go`.

So how do we get this fancy stuff into our functions? Well with an operator of
course; the `...` operator. According to the
{{< anchor-new-window "language specification"
"https://go.dev/ref/spec#Passing_arguments_to_..._parameters"
>}} there is no name for the `...` operator 😥 In other languages they have
similar operators with names, (splat/spread), but our `...` operator doesn't
just pull out all values (from a slice), it also puts them all in. So we'll
take a page out of the Lua programming language and call it the {{<
anchor-new-window "pack/unpack"
"https://www.lua.org/manual/5.4/manual.html#6.6" >}} operator.

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### functions.go

When we use the pack operator `...` it will take all of the values and turn it
into a slice of a type, for this we make a slice of int `[]int` and as we
figured out in the previous lesson on [range](/basics/range/#by-value) we can
loop through a slice nicely with `range`.

```go
// FuncVariadic is an example function. It takes in an arbitrary amount of
// `int`s and allows you to use all of them, the way you see fit. This can be
// seen as a more powerful version of `[]int`, and it works for all types.
func FuncVariadic(varargsNums ...int) (sum int) {
	for _, n := range varargsNums {
		sum += n
	}
	return sum
}
```

#### example_test.go

Here we give the caller the ability to pass in no values, some values, or an
entire slice with the unpack operator `...` We briefly looked at this on our lesson with [slice](/basics/slice/#append) We can see how convenient that it
for them, but for more complex functions we may need to do checks for no values
or other edge cases.

```go
func ExampleFuncVariadic() {
	fmt.Println(functions.FuncVariadic())
	fmt.Println(functions.FuncVariadic(1, 2, 3))
	nums := []int{4, 5, 6, 7, 8, 9, 10, 11, 12}
	fmt.Println(functions.FuncVariadic(nums...))
	// Output:
	// 0
	// 6
	// 72
}
```

## The Whole File

![https://twitter.com/egonelbre](/gopher-victorious.png)

```go
package functions

import "fmt"

// privateFunc is an example function, that is not exported. It is not always
// necessary to document unexported or private functions, because they won't
// show up in any documentation. It is only a good idea when the function does
// something that you wouldn't expect and should explain it.
func privateFunc() {
	fmt.Println("This function can only be called from within this package.")
}

// FuncPublic is an example function, that is exported. It is always a good
// idea to document your exported functions and variables, so that other
// developers can know how to use your code! Run `go doc --all .` in your
// terminal in this package and see what you get!
func FuncPublic() {
	fmt.Println("This function is exported and can be called anywhere.")
}

// FuncWithParams is an example function, that shows you how to pass in
// multiple arguments to a function and use them.
func FuncWithParams(name string, value int, emoji rune) {
	fmt.Printf("%s looks like %s and is a %d/10", name, string(emoji), value)
}

// FuncWithReturn is an example function on how to specify what type you want a
// function to return.
func FuncWithReturn() string {
	return "It's just this easy to return a type"
}

// FuncWithMultipleReturn is an example function that will return two types at
// the same time.
func FuncWithMultipleReturn() ([]int, bool) {
	canDoMultipleReturns := true
	return []int{1, 2, 3, 4, 5}, canDoMultipleReturns
}

// FuncWithNamedReturn is an example function that shows how you can name all
// of your parameters and all of your return types if you want to. You will
// notice we don't have to specify the type over and over if they are the same
// type. i.e. (email string, url string) == (email, url string)
func FuncWithNamedReturn(name, scheme, host, path, query string) (email, url string) {
	// Notice we don't use `:=` for email and url. The function already makes
	// them for us when we named them up above.
	email = name + "@" + host
	url = scheme + host + path + query
	// An empty return will look for your 2 named returns and output them.
	return
	// return email, url also works! And is more readable, so go with this. 👍
}

// FuncVariadic is an example function. It takes in an arbitrary amount of
// `int`s and allows you to use all of them, the way you see fit. This can be
// seen as a more powerful version of `[]int`, and it works for all types.
func FuncVariadic(varargsNums ...int) (sum int) {
	for _, n := range varargsNums {
		sum += n
	}
	return sum
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/typing-furiously.gif)

```go
package functions_test

import (
	"basics/functions"
	"fmt"
)

// This will not work, uncomment it and see what error it gives you.
// func ExampleprivateFunc() {
// 	// XXX: privateFunc not exported by package functions
// 	functions.privateFunc()
// }

func ExampleFuncPublic() {
	functions.FuncPublic()
	// Output:
	// This function is exported and can be called anywhere.
}

func ExampleFuncWithParams() {
	functions.FuncWithParams("Mechanical Arm", 9, '🦾')
	// Output:
	// Mechanical Arm looks like 🦾 and is a 9/10
}

func ExampleFuncWithReturn() {
	fmt.Println(functions.FuncWithReturn())
	// Output:
	// It's just this easy to return a type
}

func ExampleFuncWithMultipleReturn() {
	fmt.Println(functions.FuncWithMultipleReturn())
	// Output:
	// [1 2 3 4 5] true
}

func ExampleFuncWithNamedReturn() {
	fmt.Println(functions.FuncWithNamedReturn("Gamba",
		"https://", "gophergo.dev", "/fun-with-funcs", "?isFun=yes&isEasy=yes"))
	// Output:
	// Gamba@gophergo.dev https://gophergo.dev/fun-with-funcs?isFun=yes&isEasy=yes
}

func ExampleFuncVariadic() {
	fmt.Println(functions.FuncVariadic())
	fmt.Println(functions.FuncVariadic(1, 2, 3))
	nums := []int{4, 5, 6, 7, 8, 9, 10, 11, 12}
	fmt.Println(functions.FuncVariadic(nums...))
	// Output:
	// 0
	// 6
	// 72
}
```
