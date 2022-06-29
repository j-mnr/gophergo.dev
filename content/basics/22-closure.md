---
Lesson: 22
Title: "Closure"
Draft: false
---

Precious gems ⚪ tucked deep inside; created in closed off environments. Yes
that's right **Closures!!!!**  What...? 🤨 You were thinking pearls? Well, they
might as well be with how useful closures are! 😁 But before we get ahead of
ourselves, what is a closure? 🤔 Essentially it's a function and that's it.
Simple right? We've [already covered those](/basics/13-function). Now the
thing that trips up **_plenty_** of developers, new and old, is Go supports
first class functions.

If you're unfamiliar with first-class functions -- Strap in and put on a helmet
⛑️ -- because I'm about to blow your mind 🤯

## Setup

Let's make our directory `closure` and the files we want inside of that directory
`example_test.go` `closure.go`

```sh
mkdir closure
touch closure/example_test.go closure/closure.go
```

Now let's open up `closure.go` and for the very first line we'll add
```go
package closure
```
Next for `example_test.go` for the very first line we'll add
```go
package closure_test
```

## Generators

Functions in Go are first-class 🧐🎩 -- Mm, yes! Quite exquisite, indeed. --
This means that they are no different then a common `int`. [Sound
familiar?](basics/19-error/#errors-are-just-values) 😏 There's a recurring
theme in Go to have as many things as simple and easy to understand as
possible. If you know how to deal with an `int` 6️⃣ you know how to deal with an
`error` ⛔ if you know how to deal with a `string` 🧵 you know how to deal with
a `func` 🕺

In other words a function can be a variable, it can be a parameter, and it can
be returned 🤯 When you accept this. You will become the One 😎

This is what it means to have first-class functions. Let's take a quick look at
things we've done plenty of times before and apply them to functions.

```go
var myInt int = 55
func Add(a, b int) int {
	return a + b
}

var myFunc = func() { fmt.Println("I'm just like any other variable.") }
func Add(a, b func()) func() {
	return func() {
		a()
		b()
	}
}
```

Here we get a taste 👅 of just how powerful 🦾 this can be if we open our minds
🧠 to the possibilities. We can _compose_ functions together to make larger
functions, we can access function scoped variables in functions, we can extend
functions by wrapping them in more functions. The world 🌎 is our oyster🦪

Now, let's look at the obligatory closure example. A generator ⚡ We'll see
that our closure closes around the variable inside of the function scope. When
we call it multiple times the value will increment! And if we want a fresh
generator ⚡ we just call our function again.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `closure.go`

```go
// AsGenerator is an example of the idea of closures. The state of the
// function is sealed away (closed away) and it keeps that state even after the
// scope is destroyed. Think of an oyster 🦪 with a grain of sand, turning into
// a pearl. ⚪
func AsGenerator() func() int {
	startNum := 0
	return func() int {
		startNum++
		return startNum
	}
}
```

#### `example_test.go`

```go
func ExampleAsGenerator() {
	countUp := closure.AsGenerator()
	fmt.Print(countUp(), countUp(), countUp(), countUp(), countUp(), countUp())
	startOver := closure.AsGenerator()
	fmt.Println()
	fmt.Println("New number generator starting over:", startOver())
	fmt.Print("Old number generator still going: ", countUp())
	// Output:
	// 1 2 3 4 5 6
	// New number generator starting over: 1
	// Old number generator still going: 7
}
```

## Accessing Data Safely

If you're familiar with first-class functions then the above example is
trivial, but if you're new. It may feel like absolute  magic 🪄. So let's break
down closures a little bit more. Again, a closure is just a function. Why it
gets the name closure is dependent _where_ we place 🤲 that function. When we
place it _inside_ of another function we create a little environment 🏞️ that it
can live 🤸 and breath 🧘 in. Why that's important is it allows access to
variables _inside_ our function that no other `func`, `struct`, `type`, or
`interface` has access to.

In the meantime, closures allow us to give what we developers call "state" to a
function it would otherwise not be able to access. The term state is just like
your state of being.

Think of your current state of being. Are you hungry? 🍔 Are you tired?😪 Are
you happy? 😀 Are you curious? 🐱 Now, **think of how you came to be in _that_
state** If you are hungry, you arrived in the state of hunger because your
stomach digested all of it's food and is craving more. If you are tired, your
body has been up for so many hours that it adds some chemicals ⚗️  to the
environment that is your brain 🧠 to go to bed🛏️ If you're happy something or
someone has added themselves into your environment that **allowed you access to
that state** of being.

It's a bad idea to have global 🌐 variables. This is what's known as global
state. That is, the state of being of the application can be affected by
**anything**. Imagine if there was something about you that if **anyone** said
something about, they could control your emotions 👀, your state of being. Bad
idea to have that piece of state, right? 😬

It's not a bad idea to have a state of being. It's just better to control it
where it's most important. Train yourself 🥋 to have a better state of mind,
that way you become the master of your own destiny, young grasshopper 🦗 Just
like in life we _also_ seek to have more manageable state in our applications.
One way to do that is with closures.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `closure.go`

```go
// ForAccessingData shows we can access variables that do not belong to the
// inner function aka our closure.
func ForAccessingData() {
	notInClosure := "How does it know I exist?"
	func() {
		fmt.Println("This is a function, that is allowed to see local variables 👉",
			notInClosure)
	}()
	// NOTE(jay): This is just like how we can grab a global slice and change the
	// innards of it. -- `GlobalSlice[0] = "Some other value"` Because the
	// `GlobalSlice` is in scope of the function we can change it. Essentially
	// the "global" area has expanded for our closure allowing us access to the
	// `notInClosure` variable.
}

// NOTE(jay): This cannot be done outside of the 👆 above function. Closures
// are given special permission to access variables in the current scope.
// func doesNotWork() {
// 	fmt.Println("This is a function, that is allowed to see local variables 👉",
// 		notInClosureScope)
// }
```

#### `example_test.go`

```go
func ExampleForAccessingData() {
	closure.ForAccessingData()
	// Output:
	// This is a function, that is allowed to see local variables 👉 How does it know I exist?
}
```

## Decorator Pattern

There is a _very_ popular design pattern known as the [Decorator
Pattern](https://refactoring.guru/design-patterns/decorator) 🎍 It's called
that because it adds a little flair to something like a 🎀`func`🎀 without
actually changing any of the underlying implementation of the `func`.

In Python 🐍 and JS 📜 they literally call them decorators and in Java ☕ they
are called annotations (**disclaimer** not all annotations function as
decorators). In Go we support higher-order functions (aka first-class
functions) and therefore can create our own decorators, because we can take in
a `func` as a parameter and return that `func` as an output.

It can't be overstated how useful this pattern is in programming. It is a must
learn and it's thanks to closures that we can do it in Go. To repeat myself, we
don't want to change the underlying code inside of a function we want to
decorate it 💝 with the details we want to see.

A common use is with middleware. Which is an odd term, I admit, but for our
definition it's what goes in-between our logic on the backend.... That _still_
may be confusing so let's solve a problem.

We're going to make lots of routes for our API that people can get all types of
information on locations. 💒 🏰 🛕 🗽 🏛️  🗿 Now imagine that every single
place we have a different route for. Next, lets add some authorization. If
someone wants to use our API they have to sign-in. What happens to all of our
routes? 💒 🏰 🛕 🗽 🏛️  🗿 Do we have to make a _new_ function for **every
single one????** 😩 Well no! Let's keep the routes the same and we'll
_decorate_ ❇️  them with some "middleware". That is, we'll make a function that
checks the user signed in and wrap our routes with that function. Ta-da! 🪄 The
functions stay the same but get more sophisticated. There's the added bonus
that we can add more routes 🛖 and add that functionality just as easy.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `closure.go`

```go
// MyS is used for showing off examples with closures and accessing
// the values of MyS from within the closures.
type MyS struct {
	MyStr     string
	IsChanged bool
}

// AsMiddleware is an example of not changing the existing function, but
// only adding new features to it. In this example we can make a logger. It
// will check what the values passed in are before and after the function call
// without interfering with the original function! Pretty neat 💯
func AsMiddleware(myFunc func(strct *MyS, n *int)) func(strct *MyS, n *int) {
	return func(strct *MyS, n *int) {
		fmt.Printf("this is a statement that happens **BEFORE** myFunc:\n"+
			"Here are the values before changing them: %+v and %d\n", strct, *n)
		myFunc(strct, n)
		fmt.Printf("this is a statement that happens **AFTER** myFunc:\n"+
			"Here are the values after changing them: %+v and %d\n", strct, *n)
	}
}
```

#### `example_test.go`

```go
func ExampleAsMiddleware() {
	changesStuff := func(strct *closure.MyS, n *int) {
		strct.MyStr = "A Whole New String from a closure! 🤯"
		strct.IsChanged = true
		*n = 9999
	}
	logsStuff := closure.AsMiddleware(changesStuff)
	strct := &closure.MyS{MyStr: "My cool String 😎", IsChanged: false}
	intPtr := new(int)
	logsStuff(strct, intPtr)
	// Output:
	// this is a statement that happens **BEFORE** myFunc:
	// Here are the values before changing them: &{MyStr:My cool String 😎 IsChanged:false} and 0
	// this is a statement that happens **AFTER** myFunc:
	// Here are the values after changing them: &{MyStr:A Whole New String from a closure! 🤯 IsChanged:true} and 9999
}
```

## Accessing More Data

More examples are always better on a topic that has intimidating names like
higher-order function or first-class function. So we'll look at another way to
use a closure. All of the ways discussed are pragmatic and can be found in many
applications, so let's continue the trend 📈

There is a special 🌟 type of function in the standard library known as
`http.HandlerFunc`. `http` is the package and `HandlerFunc` is the type. In
order to be a `http.HandlerFunc` you must have a declaration like this
`func(http.ResponseWriter, *http.Request)`. It is absolutely necessary to look
like that or you won't be able to use it as a `http.Handler` and then you can't
do `http.ListenAndServe` with your function. Now let's say we want to pass some
data to our own `HandlerFunc`. How would we do it? 🤔 You guessed it! Closures
😁

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `closure.go`

```go
// ForAccessingMoreData is an example of feeding values to a function that
// does not accept that type or more values. This is very common with the
// `http.HandlerFunc` which is required to have exactly 2 parameters
// `http.ResponseWriter` and `*http.Request`. So how do we make a
// `http.HandlerFunc` have more parameters? Closures of course!
func ForAccessingMoreData(strct MyS) func(http.ResponseWriter, *http.Request) {
	// This func here 👇matches with this func here👆
	return func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintln(w, strct.MyStr)
	}
}
```

#### `example_test.go`

Here we `import httptest` to get the actual body from the response to see the
output. It's not important to know how to test right now, but if you're
wondering about it in the future, Go has it 🤙

```go
func ExampleForAccessingMoreData() {
	// Create a handler that now has access to outside resources **without**
	// adding additonal parameters to the original function. Super Cool! 😁
	handler := closure.ForAccessingMoreData(closure.MyS{
		MyStr: "Accessed this data from an input to function without closure" +
			" knowing where it came from!"})

	// This is how to test HTTP Handlers, it will be covered much later
	rr := httptest.NewRecorder()
	http.HandlerFunc(handler).ServeHTTP(rr, nil)
	// What we want to see coming from ResponseRecorder aka our Response
	fmt.Println(rr.Body.String())
	// Output:
	//  Accessed this data from an input to function without closure knowing where it came from!
}
```

## Standard Library Uses

It is very natural 🏞️  in Go to ask for functions from the caller 📞 In other
words, it's very common to see that you'll be asked to provide functions to
other functions. Much like being asked to provide a `string` or `int` or
`struct{ ... }` you'll also see `func( ... ) ( ... )`. Many times, because Go
allows it, we inline these functions and make anonymous 🥸 functions (functions
without a name) so that it makes it more obvious they only serve one purpose --
Being in this one function call and that's it.

Anonymous 🥸 functions are another name for a closure, but don't think they are
any different.... Now that I think about it: higher-order function, first-class
function, anonymous function, closure -- These are all words used to describe
the same thing in Go. A function. 😂 So try not to get _too_ lost in it all.
We're doing what we've been doing since lesson 0. Making functions.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

Here we look at how to `map` runes into other runes in a `string` and how to
search through sorted slices.

#### `closure.go`

```go
// ForStandardLibrary is an example of when the standard library will
// ask you to provide a closure in order to complete the functions parameters.
// In this we see two examples: `strings.Map` and `sort.Search`
func ForStandardLibrary() {
	rot13 := "pybfherf ner pbby"
	mappedStr := strings.Map(func(r rune) rune {
		if r == ' ' {
			return r
		}
		r -= 13
		// We might go outside of alphabet range a-z, so we correct for it here.
		switch {
		case r < 'a':
			return r + 'z' - 'a' + 1
		case r > 'z':
			return r%'z' + 'a' - 1
		default:
			return r
		}
	}, rot13)
	fmt.Println(mappedStr)

	sortedInts := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16}
	indexAfter13 := sort.Search(len(sortedInts), func(i int) bool {
		return sortedInts[i] > 13
	})
	indexBefore13 := sort.Search(len(sortedInts), func(i int) bool {
		return sortedInts[i] < 13
	})
	indexOf13 := sort.Search(len(sortedInts), func(i int) bool {
		return sortedInts[i] == 13
	})
	fmt.Printf("indices found from Binary Search of 13:\nAfter: %d\n"+
		"Before: %d\nEqual: %d", indexAfter13, indexBefore13, indexOf13)
}
```

#### `example_test.go`

```go
func ExampleForStandardLibrary() {
	closure.ForStandardLibrary()
	// Output:
	// closures are cool
	// indices found from Binary Search of 13:
	// After: 14
	// Before: 0
	// Equal: 13
}
```

## Avoiding Callback Hell 😈

Certain languages are single threaded 🧵 Which means they cannot have multiple
threads, which means they can't juggle 🤹 multiple tasks at once. They have to
focus on a single task, complete it, and move on to the next one. Sometimes,

> **we as developers are so preoccupied with whether or not we can do
> something, that we don't stop to think whether we should**

And in those single threaded 🧵 languages, there are hacky 🫢, ugly, 🫣 and
misfortunate workarounds to "allow" for multi-threaded behavior. This is by no
means a dig at the language designers, more so a wake up call ☎️ to use the
right language for the right job. Does your language support concurrency and
multi-threaded nature? 👍 Use it! Does it not? Maybe stop ✋ trying to make the
wrench 🔧 cut a piece of wood🪵 and pick up a saw? 🪚

One such of these problems encountered in JavaScript is known as "Callback
Hell" You really don't need to care about it, because you'll never experience
in Go. Go was built with concurrency in mind. And you don't have to smack your
head into the wall 😵 trying to get things to work and look right. In Go we
have synchronous code that runs on one thread and we can just as easily make
asynchronous code with the `go` keyword. It will then run in a separate
`goroutine` (we can think of it as a lightweight thread) and we can wait for it
like we would in synchronous code. We'll go into more detail into concurrency
in the next lesson! 😲

For now we have two different examples of declaring closures inside and outside
of functions. We can see how to pass in arguments to a closure in `closure.go`
and how we can call the other functions declared as variables in
`example_test.go`

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `closure.go`

```go
// AvoidCallbackHell is an example of how, in Go, there is no such thing
// as "Callback Hell" because you can always call work synchronously and if you
// want to do it asynchronously you would use a goroutine with `go` to do it.
// If you are unfamiliar with "Callback Hell" it's not important to learn about
// it. You won't experience it in Go because it's much more well designed!
func AvoidCallbackHell() {
	// result1 is DoWork1 as a closure
	result1 := func(args ...int) int {
		sum := 0
		for _, n := range args {
			sum += n
		}
		return sum
	}(1, 2, 3, 4, 5, 6, 7, 8)

	// result2 is DoWork2 as a closure
	result2 := func(sum int) float32 {
		crunchNums := math.Pi * 2
		return float32(sum) * float32(crunchNums)
	}(result1)

	// result3 is DoWork3 as a closure
	result3 := func(f float32) string {
		return fmt.Sprintf("%0.4f", f)
	}(result2)

	fmt.Printf("Some padding for final result:\n%14s", result3)
}

// DoWork1 is a global private closure. It creates the sum of several numbers.
var DoWork1 = func(args ...int) int {
	sum := 0
	for _, n := range args {
		sum += n
	}
	return sum
}

// DoWork2 acts like it's doing some heavy number crunching and using the value
// that it obtains from DoWork1.
var DoWork2 = func(sum int) float32 {
	crunchNums := math.Pi * 2
	return float32(sum) * float32(crunchNums)
}

// DoWork3 changes the float32 data type into a string with 4 places of
// precision.
var DoWork3 = func(f float32) string {
	return fmt.Sprintf("%0.4f", f)
}
```

#### `example_test.go`

```go
func ExampleAvoidCallbackHell() {
	// `go` keyword creates a new goroutine it will be covered in a later lesson.
	// Just know it let's us multi-task or better put -- introduce concurrency.
	go func() {
		result1 := closure.DoWork1(1, 2, 3, 4, 5, 6, 7, 8)
		result2 := closure.DoWork2(result1)
		result3 := closure.DoWork3(result2)
		fmt.Printf("\nSome padding for final result:\n%14s", result3)
	}()
	go closure.AvoidCallbackHell()

	fmt.Println("Done asynchronously! If you're not FIRST you're LAST")
	// Simulate long enough time for goroutine to finish above.
	time.Sleep(3 * time.Millisecond)
	// Output:
	// Done asynchronously! If you're not FIRST you're LAST
	// Some padding for final result:
	//       226.1947
	// Some padding for final result:
	//       226.1947
}
```

## Gotcha!! 🪤

If you're unfamiliar with what a "gotcha" is. In programming, when you expect a
certain thing to happen because you've built an intuition for it, but something
unexpected 😨 happens, you got, got. In other words... _Gotcha!_ 😈

Imagine if you tried pulling open your front door 🚪 that you've always pulled
open and one day it just won't open. You pull with all 🦵 your strength 💪 and
it doesn't budge. Then you push it slightly and it creaks open ... _Gotcha!_ 😈

It's time ⏲️ I must admit, not everything can be perfect and this is one of
those instances where the behavior _wants_ to be intuitive, but it falls short.
🤷 It leads to plenty of confusion and bugs 🐞 in a Go program. What I'm
speaking about is whether the **reference** or the **value** is captured by a
closure 🦪

Many times you can avoid this in Go, by **not** using closures and instead
using an anonymous function with a parameter, but sometimes you just have to
redeclare your variable. Many times you may see this 👉 `someVal := someVal`
this is known as shadowing 🥷 and it's to get around this unintuitive behavior.

The behavior I'm talking about is expecting the value of a variable to be
captured in a closure, when in fact the pointer (reference) to it is captured.
Trust me when I say you've already experienced this or will in the future. This
is unintuitive because Go doesn't actually hide away pointers like many other
programming languages do. Go also boasts no magic under the hood 🚗 and it
succeeds at that in **plenty** of instances. However, in this instance, we have
what looks like `someValue`, being treated like `*somePointer` 🤷 Can't win 'em
all I suppose.

Let's understand what's happening so we can be aware of it, avoid it, **and**
how we can fix it 🔧 😁

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `closure.go`

```go
// Gotcha shows that closures will grab from outside of their environment, but
// closures have no control of those variables having their values changed.
func Gotcha() {
	limit := 4
	funcs := make([]func(), limit)
	for gotcha := 0; gotcha < limit; gotcha++ {
		funcs[gotcha] = func() {
			fmt.Printf("number: %d and pointer: %p\n", gotcha, &gotcha)
		}
	}
	for i := 0; i < limit; i++ {
		funcs[i]()
	}
}

// GotchaFix shows a deeper understanding of closures and how we can avoid the
// very common beginner mistake of
func GotchaFix() {
	limit := 4
	funcs := make([]func(), limit)
	for gotcha := 0; gotcha < limit; gotcha++ {
		// NOTE(jay): this is known as shadowing. This is confusing and is normally
		// avoided if possible. The explanation is we are creating a new variable
		// scoped inside of the for loop and therefore this `gotcha` won't change
		// only the `gotcha` on the for loop scope will change.
		//
		// This also works and you could put gotcha2 in place of gotcha in the
		// closure if it helps your understanding.
		//
		// gotcha2 := gotcha
		gotcha := gotcha

		// 👇 This also works and it's less confusing for the uninitiated. Again,
		// we are creating a new variable `gotcha2` inside of the for loop, that is
		// the same as the `gotcha` on the for loop scope.
		// gotcha2 := gotcha
		funcs[gotcha] = func() {
			fmt.Printf("number: %d and pointer: %p\n", gotcha, &gotcha)
		}
	}

	// Instead of using a closure we can use an anonymous function which has a
	// parameter passed in.
	funcs2 := make([]func(int), limit)
	for gotcha := 0; gotcha < limit; gotcha++ {
		funcs2[gotcha] = func(i int) {
			fmt.Printf("number: %d and pointer: %p\n", i, &i)
		}
	}

	fmt.Println("fix 1:")
	for i := 0; i < limit; i++ {
		funcs[i]()
	}
	fmt.Println("fix 2:")
	for i := 0; i < limit; i++ {
		funcs2[i](i)
	}
}
```

#### `example_test.go`

```go
func ExampleGotcha() {
	// XXX(jay): These tests will never pass because the pointers will never be
	// the same each time we run the tests! The gotcha still happens though!
	closure.Gotcha()
	// Output:
	// number: 4 and pointer: 0xc000016490
	// number: 4 and pointer: 0xc000016490
	// number: 4 and pointer: 0xc000016490
	// number: 4 and pointer: 0xc000016490
}

func ExampleGotchaFix() {
	// XXX(jay): These tests will never pass because the pointers will never be
	// the same each time we run the tests! The fix works, though!
	closure.GotchaFix()
	// Output:
	// fix 1:
	// number: 0 and pointer: 0xc0000164a8
	// number: 1 and pointer: 0xc0000164b0
	// number: 2 and pointer: 0xc0000164b8
	// number: 3 and pointer: 0xc0000164c0
	// fix 2:
	// number: 0 and pointer: 0xc0000164c8
	// number: 1 and pointer: 0xc0000164d0
	// number: 2 and pointer: 0xc0000164d8
	// number: 3 and pointer: 0xc0000164e0
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png "gopher-victorious by Egon Elbre")

```go
package closure

import (
	"fmt"
	"math"
	"net/http"
	"sort"
	"strings"
)

// AsGenerator is an example of the idea of closures. The state of the
// function is sealed away (closed away) and it keeps that state even after the
// scope is destroyed. Think of an oyster 🦪 with a grain of sand, turning into
// a pearl. ⚪
func AsGenerator() func() int {
	startNum := 0
	return func() int {
		startNum++
		return startNum
	}
}

// ForAccessingData shows we can access variables that do not belong to the
// inner function aka our closure.
func ForAccessingData() {
	notInClosure := "How does it know I exist?"
	func() {
		fmt.Println("This is a function, that is allowed to see local variables 👉",
			notInClosure)
	}()
	// NOTE(jay): This is just like how we can grab a global slice and change the
	// innards of it. -- `GlobalSlice[0] = "Some other value"` Because the
	// `GlobalSlice` is in scope of the function we can change it. Essentially
	// the "global" area has expanded for our closure allowing us access to the
	// `notInClosure` variable.
}

// NOTE(jay): This cannot be done outside of the 👆 above function. Closures
// are given special permission to access variables in the current scope.
// func doesNotWork() {
// 	fmt.Println("This is a function, that is allowed to see local variables 👉",
// 		notInClosureScope)
// }

// MyS is used for showing off examples with closures and accessing
// the values of MyS from within the closures.
type MyS struct {
	MyStr     string
	IsChanged bool
}

// AsMiddleware is an example of not changing the existing function, but
// only adding new features to it. In this example we can make a logger. It
// will check what the values passed in are before and after the function call
// without interfering with the original function! Pretty neat 💯
func AsMiddleware(myFunc func(strct *MyS, n *int)) func(strct *MyS, n *int) {
	return func(strct *MyS, n *int) {
		fmt.Printf("this is a statement that happens **BEFORE** myFunc:\n"+
			"Here are the values before changing them: %+v and %d\n", strct, *n)
		myFunc(strct, n)
		fmt.Printf("this is a statement that happens **AFTER** myFunc:\n"+
			"Here are the values after changing them: %+v and %d\n", strct, *n)
	}
}

// ForAccessingMoreData is an example of feeding values to a function that
// does not accept that type or more values. This is very common with the
// `http.HandlerFunc` which is required to have exactly 2 parameters
// `http.ResponseWriter` and `*http.Request`. So how do we make a
// `http.HandlerFunc` have more parameters? Closures of course!
func ForAccessingMoreData(strct MyS) func(http.ResponseWriter, *http.Request) {
	// This func here 👇matches with this func here👆
	return func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintln(w, strct.MyStr)
	}
}

// ForStandardLibrary is an example of when the standard library will
// ask you to provide a closure in order to complete the functions parameters.
// In this we see two examples: `strings.Map` and `sort.Search`
func ForStandardLibrary() {
	rot13 := "pybfherf ner pbby"
	mappedStr := strings.Map(func(r rune) rune {
		if r == ' ' {
			return r
		}
		r -= 13
		// We might go outside of alphabet range a-z, so we correct for it here.
		switch {
		case r < 'a':
			return r + 'z' - 'a' + 1
		case r > 'z':
			return r%'z' + 'a' - 1
		default:
			return r
		}
	}, rot13)
	fmt.Println(mappedStr)

	sortedInts := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16}
	indexAfter13 := sort.Search(len(sortedInts), func(i int) bool {
		return sortedInts[i] > 13
	})
	indexBefore13 := sort.Search(len(sortedInts), func(i int) bool {
		return sortedInts[i] < 13
	})
	indexOf13 := sort.Search(len(sortedInts), func(i int) bool {
		return sortedInts[i] == 13
	})
	fmt.Printf("indices found from Binary Search of 13:\nAfter: %d\n"+
		"Before: %d\nEqual: %d", indexAfter13, indexBefore13, indexOf13)
}

// AvoidCallbackHell is an example of how, in Go, there is no such thing
// as "Callback Hell" because you can always call work synchronously and if you
// want to do it asynchronously you would use a goroutine with `go` to do it.
// If you are unfamiliar with "Callback Hell" it's not important to learn about
// it. You won't experience it in Go because it's much more well designed!
func AvoidCallbackHell() {
	// result1 is DoWork1 as a closure
	result1 := func(args ...int) int {
		sum := 0
		for _, n := range args {
			sum += n
		}
		return sum
	}(1, 2, 3, 4, 5, 6, 7, 8)

	// result2 is DoWork2 as a closure
	result2 := func(sum int) float32 {
		crunchNums := math.Pi * 2
		return float32(sum) * float32(crunchNums)
	}(result1)

	// result3 is DoWork3 as a closure
	result3 := func(f float32) string {
		return fmt.Sprintf("%0.4f", f)
	}(result2)

	fmt.Printf("Some padding for final result:\n%14s", result3)
}

// DoWork1 is a global private closure. It creates the sum of several numbers.
var DoWork1 = func(args ...int) int {
	sum := 0
	for _, n := range args {
		sum += n
	}
	return sum
}

// DoWork2 acts like it's doing some heavy number crunching and using the value
// that it obtains from DoWork1.
var DoWork2 = func(sum int) float32 {
	crunchNums := math.Pi * 2
	return float32(sum) * float32(crunchNums)
}

// DoWork3 changes the float32 data type into a string with 4 places of
// precision.
var DoWork3 = func(f float32) string {
	return fmt.Sprintf("%0.4f", f)
}

// Gotcha shows that closures will grab from outside of their environment, but
// closures have no control of those variables having their values changed.
func Gotcha() {
	limit := 4
	funcs := make([]func(), limit)
	for gotcha := 0; gotcha < limit; gotcha++ {
		funcs[gotcha] = func() {
			fmt.Printf("number: %d and pointer: %p\n", gotcha, &gotcha)
		}
	}
	for i := 0; i < limit; i++ {
		funcs[i]()
	}
}

// GotchaFix shows a deeper understanding of closures and how we can avoid the
// very common beginner mistake of
func GotchaFix() {
	limit := 4
	funcs := make([]func(), limit)
	for gotcha := 0; gotcha < limit; gotcha++ {
		// NOTE(jay): this is known as shadowing. This is confusing and is normally
		// avoided if possible. The explanation is we are creating a new variable
		// scoped inside of the for loop and therefore this `gotcha` won't change
		// only the `gotcha` on the for loop scope will change.
		//
		// This also works and you could put gotcha2 in place of gotcha in the
		// closure if it helps your understanding.
		//
		// gotcha2 := gotcha
		gotcha := gotcha

		// 👇 This also works and it's less confusing for the uninitiated. Again,
		// we are creating a new variable `gotcha2` inside of the for loop, that is
		// the same as the `gotcha` on the for loop scope.
		// gotcha2 := gotcha
		funcs[gotcha] = func() {
			fmt.Printf("number: %d and pointer: %p\n", gotcha, &gotcha)
		}
	}

	// Instead of using a closure we can use an anonymous function which has a
	// parameter passed in.
	funcs2 := make([]func(int), limit)
	for gotcha := 0; gotcha < limit; gotcha++ {
		funcs2[gotcha] = func(i int) {
			fmt.Printf("number: %d and pointer: %p\n", i, &i)
		}
	}

	fmt.Println("fix 1:")
	for i := 0; i < limit; i++ {
		funcs[i]()
	}
	fmt.Println("fix 2:")
	for i := 0; i < limit; i++ {
		funcs2[i](i)
	}
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif "typing-furiously by Egon Elbre")

```go
package closure_test

import (
	"basics/closure"
	"fmt"
	"net/http"
	"net/http/httptest"
	"time"
)

func ExampleAsGenerator() {
	countUp := closure.AsGenerator()
	fmt.Print(countUp(), countUp(), countUp(), countUp(), countUp(), countUp())
	startOver := closure.AsGenerator()
	fmt.Println()
	fmt.Println("New number generator starting over:", startOver())
	fmt.Print("Old number generator still going: ", countUp())
	// Output:
	// 1 2 3 4 5 6
	// New number generator starting over: 1
	// Old number generator still going: 7
}

func ExampleForAccessingData() {
	closure.ForAccessingData()
	// Output:
	// This is a function, that is allowed to see local variables 👉 How does it know I exist?
}

func ExampleAsMiddleware() {
	changesStuff := func(strct *closure.MyS, n *int) {
		strct.MyStr = "A Whole New String from a closure! 🤯"
		strct.IsChanged = true
		*n = 9999
	}
	logsStuff := closure.AsMiddleware(changesStuff)
	strct := &closure.MyS{MyStr: "My cool String 😎", IsChanged: false}
	intPtr := new(int)
	logsStuff(strct, intPtr)
	// Output:
	// this is a statement that happens **BEFORE** myFunc:
	// Here are the values before changing them: &{MyStr:My cool String 😎 IsChanged:false} and 0
	// this is a statement that happens **AFTER** myFunc:
	// Here are the values after changing them: &{MyStr:A Whole New String from a closure! 🤯 IsChanged:true} and 9999
}

func ExampleForAccessingMoreData() {
	// Create a handler that now has access to outside resources **without**
	// adding additonal parameters to the original function. Super Cool! 😁
	handler := closure.ForAccessingMoreData(closure.MyS{
		MyStr: "Accessed this data from an input to function without closure" +
			" knowing where it came from!"})

	// This is how to test HTTP Handlers, it will be covered much later
	rr := httptest.NewRecorder()
	http.HandlerFunc(handler).ServeHTTP(rr, nil)
	// What we want to see coming from ResponseRecorder aka our Response
	fmt.Println(rr.Body.String())
	// Output:
	//  Accessed this data from an input to function without closure knowing where it came from!
}

func ExampleForStandardLibrary() {
	closure.ForStandardLibrary()
	// Output:
	// closures are cool
	// indices found from Binary Search of 13:
	// After: 14
	// Before: 0
	// Equal: 13
}

func ExampleAvoidCallbackHell() {
	// `go` keyword creates a new goroutine it will be covered in a later lesson.
	// Just know it let's us multi-task or better put -- introduce concurrency.
	go func() {
		result1 := closure.DoWork1(1, 2, 3, 4, 5, 6, 7, 8)
		result2 := closure.DoWork2(result1)
		result3 := closure.DoWork3(result2)
		fmt.Printf("\nSome padding for final result:\n%14s", result3)
	}()
	go closure.AvoidCallbackHell()

	fmt.Println("Done asynchronously! If you're not FIRST you're LAST")
	// Simulate long enough time for goroutine to finish above.
	time.Sleep(3 * time.Millisecond)
	// Output:
	// Done asynchronously! If you're not FIRST you're LAST
	// Some padding for final result:
	//       226.1947
	// Some padding for final result:
	//       226.1947
}

func ExampleGotcha() {
	// XXX(jay): These tests will never pass because the pointers will never be
	// the same each time we run the tests! The gotcha still happens though!
	closure.Gotcha()
	// Output:
	// number: 4 and pointer: 0xc000016490
	// number: 4 and pointer: 0xc000016490
	// number: 4 and pointer: 0xc000016490
	// number: 4 and pointer: 0xc000016490
}

func ExampleGotchaFix() {
	// XXX(jay): These tests will never pass because the pointers will never be
	// the same each time we run the tests! The fix works, though!
	closure.GotchaFix()
	// Output:
	// fix 1:
	// number: 0 and pointer: 0xc0000164a8
	// number: 1 and pointer: 0xc0000164b0
	// number: 2 and pointer: 0xc0000164b8
	// number: 3 and pointer: 0xc0000164c0
	// fix 2:
	// number: 0 and pointer: 0xc0000164c8
	// number: 1 and pointer: 0xc0000164d0
	// number: 2 and pointer: 0xc0000164d8
	// number: 3 and pointer: 0xc0000164e0
}
```
