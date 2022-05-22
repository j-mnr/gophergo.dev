---
Lesson: 19
Title: "Errors"
Draft: false
---

🤖 🚨🚨 **ERROR** 🚨🚨 🤖 🚨🚨 **ERROR** 🚨🚨 🤖 This is not a drill we're
going down❗ The story's 📖 coming to a close❗ The application 🖥️ is going to
crash❗ -- Calm down ❗❗ This is no time to [panic]() (coming soon!). Like any
Gopher worth their salt, we'll handle our `error` gracefully.

What's an `error` in Go and why should we handle it gracefully? Let's start
with the first question. An `error` is a value like any other value (`int`,
`float64`, `bool`, `string`, `map`, ... ). It, however, signifies that the
result you're returned is **erroneous** or **bad** ❌ in some way. Many times
we see errors paired 🍐 with a good value (`(string, error)`). This is a real
shining ✨ achievement of Go. It's _so easy_ to return multiple values and it's
_so easy_ to see which functions or methods can give bad results. Therefore
you'll never be guessing if a function you call is going to blow up 💥 👀 in
your face 😑

It is up to _you_ to handle that `error` gracefully 🦢 But what does that even
mean? Well say there's a fire 🔥 in your kitchen. Would you sit around waiting
for the fire alarm 🚨 and sprinklers 💦 💦 to go off? That's **not** handling
a bad situation with grace 🦢 What you'd do instead, would be to find a way to
put the fire out 🧯 or call the necessary people to get the job done 🚒 That
same logic applies in our applications we create. When a function `func
CanGoWrong( ... ) (string, error) { ... }` gives us an `error` **we do
not ignore it, we handle it gracefully** 🦢 

## Setup

Let's make our directory `errs` and the files we want inside of that directory
`example_test.go` `errs.go`

```sh
mkdir errs
touch errs/example_test.go errs/errs.go
```

Now let's open up `errs.go` and for the very first line we'll add
```go
package errs
```
Next for `example_test.go` for the very first line we'll add
```go
package errs_test
```

⚠️ **Note** The reason we don't call our package `error` is because that would
lead to massive confusion if we wanted the `error` interface or our `error`
package. We also can't call it `errors` as that's _also_ used in the standard
library as a package and would lead to more confusion.

## Create Error

OK, so I'm going to repeat this a lot -- **errors are just values**. And like
any good ol' value we can initialize (create) them just like **everything**
else we've made up to this point. We can do it manually 💪 or we can get a
little help from the standard library📚📚📚. We'll do both for practice but
let's start with the easier approach and use what's in the language.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### errs.go

```go
// ErrorNew returns an error based on the way we want it to be made. Which can
// be done with the standard `errors` package or for more formatting options
// the `fmt` package. If the way is not recognized nil is returned.
func ErrorNew(way string) error {
	switch way {
	case "fmt":
		return fmt.Errorf("we can use fmt to have formatting verbs: %q", way)
	case "errors":
		return errors.New("an error has occurred")
	default:
		return nil
	}
}
```

#### example_test.go

Notice the `type` shown with our `%#v` formatting verb is an
`errors.errorString` `struct` aka another packages unexported `type` [sound
familiar?](/basics/14-struct/#example_testgo-1) 😏

```go
func ExampleErrorNew() {
	fmt.Printf(
		"%v\n%#v\n%#v\n%+v\n",
		errs.ErrorNew("fmt"),
		errs.ErrorNew("fmt"),
		errs.ErrorNew("errors"),
		errs.ErrorNew("🤷"),
	)
	// Output:
	// we can use fmt to have formatting verbs: "fmt"
	// &errors.errorString{s:"we can use fmt to have formatting verbs: \"fmt\""}
	// &errors.errorString{s:"an error has occurred"}
	// <nil>
}
```

## Errors Are Just Values

First, if we see 👀 what an `error` actually is in the standard library it
won't be so mystical and magical 🧙 to us -- {{< anchor-new-window "here it is"
"https://cs.opensource.google/go/go/+/refs/tags/go1.18.2:src/builtin/builtin.go;l=270"
>}} or if you don't want to click away it looks like this

```go
// The error built-in interface type is the conventional interface for
// representing an error condition, with the nil value representing no error.
type error interface {
	Error() string
}
```

🪄 TADA!!!! 😁 What? Not amazed? It's nothing special you say? Well of course
not! It's **just a value**, to be more specific an `interface` value or we'd
actually say an unexported `interface`. And as we already learned from
[interfaces](/basics/17-interfaces/#interface-basics), if you satisfy the
behavior `Error() string` you implement that `interface`. We've done this
before and we'll do it again! 💪😤

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### errs.go

```go
// realError is a living, breathing, 100% real error. It is important to
// understand that in Go -- errors are values. If the type implements the error
// interface which has 1 method -- Error() string -- it is an error.
type realError bool

// Error is the only method in the builtin error interface. It returns a
// message of what went wrong.
func (e realError) Error() string {
	return "this is a real error that can be returned if something goes wrong"
}

// ErrorCustom shows that implementing the builtin error interface is very easy
// to do and can be used to return custom errors instead of the most common
// unexported `errorString` struct in the `errors` package.
func ErrorCustom() error {
	return realError(true)
}
```

#### example_test.go

The same way `fmt.Println` and friends use the `String() string` method of a
`type` if it has one, an `error` with the `Error() string` method is called for
us automatically. 👌 Nice touch Go 👍

```go
func ExampleErrorCustom() {
	if err := errs.ErrorCustom(); err != nil {
		fmt.Println(err)
	}
	// Output: this is a real error that can be returned if something goes wrong
}
```

## Errors Are Just Values Part 2: ⚡Electric⚡ Boogaloo

> **Errors are just values** 🤪 OK, Yeah I get it, but what does that _mean_?

It means the only important difference between them and an `int` is they signal
something has gone wrong. That's because we all agree the word `error` means
something went wrong. Beyond that semantic meaning we give. Everything is the
same. You can add methods to your custom errors, you can make any `type`
implement an error, you can have your error implement _other_ interfaces, you
can have a `struct` full of fields that can be used in the `Error` method, you
can even _extend_ the `error` `interface` (More on that down below). The power
of an `error` is that its an everyday citizen 🚶 just doing its part in the
application 🏙️

These are all things we've already done in previous lessons, so since we know
how to deal with values, we know how to deal with errors.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### errs.go

```go
// TooBigError is an exported error that will tell the caller if the number
// input is too big.
type TooBigError int64

func (e TooBigError) Error() string {
	return fmt.Sprintf("number too big: %d", e)
}

// phoneNumberError is an unexported error that informs the caller when a
// bad phone number was passed in.
type phoneNumberError string

func (e phoneNumberError) Error() string {
	// We need to explicitly convert e to a string here or else we'll get
	// XXX: arg e causes a recursive Error method call.
	return fmt.Sprint("phone number must have 10 digits: ", string(e))
}

// InvalidRuneError is an error that let's the caller know the input rune does
// not work with the function.
type InvalidRuneError rune

func (e InvalidRuneError) Error() string {
	// We need to explicitly convert e to a string here or else we'll get
	// XXX: arg e causes a recursive Error method call.
	return fmt.Sprintf("input rune is not a valid english letter: %q", string(e))
}

// bearer is a simple interface much like error. It is important to recognize
// errors are values, meaning bearer and error are no different from one
// another. Anything you'd expect a normal value to do, error can to.
type bearer interface {
	Bearer() string
}

// UndeadWarrior is a bearer of a great curse and must travel to distant lands
// in hopes of finally removing it from themself.
type UndeadWarrior struct{}

// Bearer is a method like Error that takes no arguments and returns a string.
func (w UndeadWarrior) Bearer() string {
	return "Rise if you would. For that is our curse."
}

func (w UndeadWarrior) String() string { return w.Bearer() }

// ErrorManyCustoms shows how to deal with many custom errors in a single
// function and shows that errors are just values that are returned by also
// returning a bearer which is very similar in behavior to an error.
func ErrorManyCustoms(n uint32, phoneNo string, ltr rune) (bearer, error) {
	if n > uint32(math.Pow(2, 31)) {
		return nil, TooBigError(n)
	}
	nDigits := 0
	for _, r := range phoneNo {
		if r >= '0' && r <= '9' {
			nDigits++
		}
	}
	if nDigits != 10 {
		return nil, phoneNumberError(phoneNo)
	}
	if !(ltr >= 'A' && ltr <= 'Z' || ltr >= 'a' && ltr <= 'z') {
		return nil, InvalidRuneError(ltr)
	}
	return UndeadWarrior{}, nil
}
```

#### example_test.go

Here we drive the point home that we return both `bearer` and `error` which are
two unexported interfaces with eerily similar methods to each other.

```go
func ExampleErrorManyCustoms() {
	if _, err := errs.ErrorManyCustoms(
		uint32(math.Pow(2, 32)-1), "(555)867-5309", 'A'); err != nil {
		fmt.Println(err)
	}
	if _, err := errs.ErrorManyCustoms(0xff, "(555)67-5309", 'z'); err != nil {
		fmt.Println(err)
	}
	if _, err := errs.ErrorManyCustoms(0b1, "(555)867-5309", '🤪'); err != nil {
		fmt.Println(err)
	}
	bearer, err := errs.ErrorManyCustoms(0o7, "(555)867-5309", 'G')
	if err != nil {
		panic(err)
	}
	fmt.Println(bearer)
	// Output:
	// number too big: 4294967295
	// phone number must have 10 digits: (555)67-5309
	// input rune is not a valid english letter: "🤪"
	// Rise if you would. For that is our curse.
}
```

## Extend and Customize Errors

Extending ⛓️ errors is exactly as we've seen before in
[interfaces](/basics/17-interfaces/#coding-time-1) because `error` _is_ an
interface, but we can at least ask _why_ would we extend `error`? The same
reason we want to extend _any_ interfaces functionality. We want more specified
behavior. There are plenty of custom error types in the standard library 📚📚📚
but one very common one is the `net.Error` which looks like

```go
type Error interface {
	error
	Timeout() bool   // Is the error a timeout?
	Temporary() bool // Is the error temporary?
}
```

See how they [embed](/basics/18-embed) the `error` interface into their new
`interface` and then add more specific behaviors that you would expect from a
HTTP Response. 🗒️✏️Note if you don't know what to expect from a HTTP response,
don't worry as we're about to make our own extended custom `error`!

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### errs.go

```go
// ConnectionError extends the behavior of a basic error with more methods that
// could be useful for someone making a call. It can be used to check if
// someone missed the call and try again or if the number had been
// disconnected.
type ConnectionError interface {
	error
	Disconnect() bool // Is the person disconnected?
	Miss() bool       // Did they miss the contact?
}

// CallError implements a ConnectionError. We can imagine other Errors that
// implement ConnectionError like: TransreceiverError, MorseError,
// NetworkError, etc ...
type CallError struct{ Number string }

func (e CallError) Error() string {
	var reason string
	switch {
	case e.Disconnect():
		reason = "it has been disconnected"
	case e.Miss():
		reason = "no one picked up the phone"
	default:
		reason = "something went wrong, please try again"
	}
	return fmt.Sprintln("the number you dialed could not be reached:", reason)
}

// Disconnect satisfies part of ConnectionError.
func (e CallError) Disconnect() bool {
	if e.Number[:3] == "555" {
		return true
	}
	return false
}

// Miss satisfies part of ConnectionError.
func (e CallError) Miss() bool {
	if e.Number[0] == '7' {
		return true
	}
	return false
}

// ErrorExtendBasic shows how to extend the simple error interface to have more
// functionality using composition and embedding the error interface into our
// new ConnectionError.
func ErrorExtendBasic(phoneNo string) ConnectionError {
	return CallError{Number: phoneNo}
}
```

#### example_test.go

```go
func ExampleErrorExtendBasic() {
	if err := errs.ErrorExtendBasic("555-212-4958").(errs.ConnectionError); err != nil {
		fmt.Printf("%#v\n%s\n", err, err)
	}
	if err := errs.ErrorExtendBasic("777-390-9911").(errs.ConnectionError); err != nil {
		fmt.Printf("%#v\n%v\n", err, err)
		if err.Miss() {
			fmt.Println("Call again...")
		}
	}
	// Output:
	// errs.CallError{Number:"555-212-4958"}
	// the number you dialed could not be reached: it has been disconnected
	//
	// errs.CallError{Number:"777-390-9911"}
	// the number you dialed could not be reached: no one picked up the phone
	//
	// Call again...
}
```

## Wrap Errors

Almost time to wrap this lesson up, but wait! 🤨 We have to talk about
wrapping. Not this rapping 🧍🎤 💬 "🤑 🏎️ 👚 👩", but _this_ wrapping 🎁 like
a ribbon 🎀 around a present. That's because in applications it's fine to have
errors and to use them appropriately, but as your application grows, you'll
find that more and more of your functions call smaller functions that can
return errors.

So what do we do when we see an `error` is returned from a function? Ignore it?
**NO** 🙅 We handle it gracefully.🤔 Well doesn't that `error` we made _have_
all the information it already needs? Yes! It does and good question! 😁 But
what it doesn't have is **context** and in the land of the soft wares, context
is king 🤴 Imagine if you told someone close to you

> I got in a car crash 🚗💥🚗 today...

What do they say?
> 🗯️ What? Are you OK? Where? Did you do it? Is the other car
OK? How bad was it?

Context is king 🤴 and we want to give a little context to our errors when they
may be coming from different spaces 👾 and places. You may find yourself
calling the same `func ReallyCoolFunc( ... ) error { ... }` in multiple places
in your code base. If you receive that `error` back, sure you know it came from
`ReallyCoolFunc` but _where_ from the several places you called it? Which place
is where that `error` occurred.

Now how do we accomplish wrapping? With the formatting verb `%w`. It is a
special verb that `w`raps the `error` with a surrounding `error` that we
create. This is one of those play with it to realize it things.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### errs.go

```go
// ErrorWrapOtherErrors shows how to put an error inside of another error. This
// is very helpful when you have many moving parts in your application. We want
// to know **where** the error originated and what places it went along the
// way.
func ErrorWrapOtherErrors() error {
	if err := pkgBufioCall(); err != nil {
		return pkgHTTPCall(pkgJSONCall(pkgZipCall(err)))
	}
	return nil
}

func pkgHTTPCall(e error) error {
	return fmt.Errorf("http: Server closed: %w", e)
}

func pkgJSONCall(e error) error {
	return fmt.Errorf("json: syntax error, unexpected ',': %w", e)
}

func pkgZipCall(e error) error {
	return fmt.Errorf("zip: not a valid zip file: %w", e)
}

func pkgBufioCall() error {
	return errors.New("bufio.Scanner: token too long")
}
```

#### example_test.go

Here we use another of the standard library package `errors` functions `Unwrap`
to show what the process was like as we unwrap the `error`.

```go
func ExampleErrorWrapOtherErrors() {
	if err := errs.ErrorWrapOtherErrors(); err != nil {
		fmt.Println("Wrapped error:", err)
		for err != nil {
			err = errors.Unwrap(err)
			fmt.Println("Unwrapping error:", err)
		}
	}
	// Output:
	// Wrapped error: http: Server closed: json: syntax error, unexpected ',': zip: not a valid zip file: bufio.Scanner: token too long
	// Unwrapping error: json: syntax error, unexpected ',': zip: not a valid zip file: bufio.Scanner: token too long
	// Unwrapping error: zip: not a valid zip file: bufio.Scanner: token too long
	// Unwrapping error: bufio.Scanner: token too long
	// Unwrapping error: <nil>
}
```

## Why Always Return Nil? 🤔

If we haven't realized up to this point, when a function works properly and one
of it's return values is an `error` the ending line of the function is
**always** `return nil` for the `error`, why? This has more to do with
interfaces and how we check if an `error` occurred in Go.

When you have a trash bin 🪣 and it's empty, do you say 💬 My trash bin is
**nothing**? No of course not. That makes no sense, you can say 💬 My trash is
empty / is filling up / is full. But you _can't_ say 💬 My trash is nothing.

Well that same logic applies to an `interface`. Simply declaring the existence
of something other than nothing makes it something. 😵‍💫 Or in much more
concrete terms 😂 declaring `error` makes it an `error` **not** `nil` This is
because interfaces have a `type` and a value associated with them. So when you
do `var myErr *CustomError` _even_ doing `var myErr *error` you're making
something come into existence. And by definition something is not nothing.
Therefore to avoid this confusion and to write idiomatic Go code we'll `return
nil`

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### errs.go

```go
// ErrorNotNil shows that a nil error value does not equal nil. In other words
// setting an error to nil and returning that error will not give you nil. It
// shows the idiomatic Go way of returning nothing if there is no error.
func ErrorNotNil(doItWrong bool) error {
	// var incorrect *CallError = nil 👈 Same as below, but this is wrong because
	// nil is the zero value, but try this explicit form out if you want.
	var incorrect *CallError
	if doItWrong {
		return incorrect
	}
	return nil
}
```

#### example_test.go

Here we can see the `type` is a pointer to `errs.CallError` `struct` and it's
_value_ is `nil`.

```go
func ExampleErrorNotNil() {
	if err := errs.ErrorNotNil(true); err != nil {
		fmt.Printf("YAH GOOFED! %#v", err)
	}

	if err := errs.ErrorNotNil(false); err != nil {
		fmt.Println("Never going to see this")
	}
	// Output:
	// YAH GOOFED! (*errs.CallError)(nil)
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png)

```go
package errs

import (
	"errors"
	"fmt"
	"math"
)

// ErrorNew returns an error based on the way we want it to be made. Which can
// be done with the standard `errors` package or for more formatting options
// the `fmt` package. If the way is not recognized nil is returned.
func ErrorNew(way string) error {
	switch way {
	case "fmt":
		return fmt.Errorf("we can use fmt to have formatting verbs: %q", way)
	case "errors":
		return errors.New("an error has occurred")
	default:
		return nil
	}
}

// realError is a living, breathing, 100% real error. It is important to
// understand that in Go -- errors are values. If the type implements the error
// interface which has 1 method -- Error() string -- it is an error.
type realError bool

// Error is the only method in the builtin error interface. It returns a
// message of what went wrong.
func (e realError) Error() string {
	return "this is a real error that can be returned if something goes wrong"
}

// ErrorCustom shows that implementing the builtin error interface is very easy
// to do and can be used to return custom errors instead of the most common
// unexported `errorString` struct in the `errors` package.
func ErrorCustom() error {
	return realError(true)
}

// TooBigError is an exported error that will tell the caller if the number
// input is too big.
type TooBigError int64

func (e TooBigError) Error() string {
	return fmt.Sprintf("number too big: %d", e)
}

// phoneNumberError is an unexported error that informs the caller when a
// bad phone number was passed in.
type phoneNumberError string

func (e phoneNumberError) Error() string {
	// We need to explicitly convert e to a string here or else we'll get
	// XXX: arg e causes a recursive Error method call.
	return fmt.Sprint("phone number must have 10 digits: ", string(e))
}

// InvalidRuneError is an error that let's the caller know the input rune does
// not work with the function.
type InvalidRuneError rune

func (e InvalidRuneError) Error() string {
	// We need to explicitly convert e to a string here or else we'll get
	// XXX: arg e causes a recursive Error method call.
	return fmt.Sprintf("input rune is not a valid english letter: %q", string(e))
}

// bearer is a simple interface much like error. It is important to recognize
// errors are values, meaning bearer and error are no different from one
// another. Anything you'd expect a normal value to do, error can to.
type bearer interface {
	Bearer() string
}

// UndeadWarrior is a bearer of a great curse and must travel to distant lands
// in hopes of finally removing it from themself.
type UndeadWarrior struct{}

// Bearer is a method like Error that takes no arguments and returns a string.
func (w UndeadWarrior) Bearer() string {
	return "Rise if you would. For that is our curse."
}

func (w UndeadWarrior) String() string { return w.Bearer() }

// ErrorManyCustoms shows how to deal with many custom errors in a single
// function and shows that errors are just values that are returned by also
// returning a bearer which is very similar in behavior to an error.
func ErrorManyCustoms(n uint32, phoneNo string, ltr rune) (bearer, error) {
	if n > uint32(math.Pow(2, 31)) {
		return nil, TooBigError(n)
	}
	nDigits := 0
	for _, r := range phoneNo {
		if r >= '0' && r <= '9' {
			nDigits++
		}
	}
	if nDigits != 10 {
		return nil, phoneNumberError(phoneNo)
	}
	if !(ltr >= 'A' && ltr <= 'Z' || ltr >= 'a' && ltr <= 'z') {
		return nil, InvalidRuneError(ltr)
	}
	return UndeadWarrior{}, nil
}

// ConnectionError extends the behavior of a basic error with more methods that
// could be useful for someone making a call. It can be used to check if
// someone missed the call and try again or if the number had been
// disconnected.
type ConnectionError interface {
	error
	Disconnect() bool // Is the person disconnected?
	Miss() bool       // Did they miss the contact?
}

// CallError implements a ConnectionError. We can imagine other Errors that
// implement ConnectionError like: TransreceiverError, MorseError,
// NetworkError, etc ...
type CallError struct{ Number string }

func (e CallError) Error() string {
	var reason string
	switch {
	case e.Disconnect():
		reason = "it has been disconnected"
	case e.Miss():
		reason = "no one picked up the phone"
	default:
		reason = "something went wrong, please try again"
	}
	return fmt.Sprintln("the number you dialed could not be reached:", reason)
}

// Disconnect satisfies part of ConnectionError.
func (e CallError) Disconnect() bool {
	if e.Number[:3] == "555" {
		return true
	}
	return false
}

// Miss satisfies part of ConnectionError.
func (e CallError) Miss() bool {
	if e.Number[0] == '7' {
		return true
	}
	return false
}

// ErrorExtendBasic shows how to extend the simple error interface to have more
// functionality using composition and embedding the error interface into our
// new ConnectionError.
func ErrorExtendBasic(phoneNo string) ConnectionError {
	return CallError{Number: phoneNo}
}

// ErrorWrapOtherErrors shows how to put an error inside of another error. This
// is very helpful when you have many moving parts in your application. We want
// to know **where** the error originated and what places it went along the
// way.
func ErrorWrapOtherErrors() error {
	if err := pkgBufioCall(); err != nil {
		return pkgHTTPCall(pkgJSONCall(pkgZipCall(err)))
	}
	return nil
}

func pkgHTTPCall(e error) error {
	return fmt.Errorf("http: Server closed: %w", e)
}

func pkgJSONCall(e error) error {
	return fmt.Errorf("json: syntax error, unexpected ',': %w", e)
}

func pkgZipCall(e error) error {
	return fmt.Errorf("zip: not a valid zip file: %w", e)
}

func pkgBufioCall() error {
	return errors.New("bufio.Scanner: token too long")
}

// ErrorNotNil shows that a nil error value does not equal nil. In other words
// setting an error to nil and returning that error will not give you nil. It
// shows the idiomatic Go way of returning nothing if there is no error.
func ErrorNotNil(doItWrong bool) error {
	// var incorrect *CallError = nil 👈 Same as below, but this is wrong because
	// nil is the zero value, but just to show you can do the above as well.
	var incorrect *CallError
	if doItWrong {
		return incorrect
	}
	return nil
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif)

```go
package errs_test

import (
	"basics/errs"
	"errors"
	"fmt"
	"math"
)

func ExampleErrorNew() {
	fmt.Printf(
		"%v\n%#v\n%#v\n%+v\n",
		errs.ErrorNew("fmt"),
		errs.ErrorNew("fmt"),
		errs.ErrorNew("errors"),
		errs.ErrorNew("🤷"),
	)
	// Output:
	// we can use fmt to have formatting verbs: "fmt"
	// &errors.errorString{s:"we can use fmt to have formatting verbs: \"fmt\""}
	// &errors.errorString{s:"an error has occurred"}
	// <nil>
}

func ExampleErrorCustom() {
	if err := errs.ErrorCustom(); err != nil {
		fmt.Println(err)
	}
	// Output: this is a real error that can be returned if something goes wrong
}

func ExampleErrorManyCustoms() {
	if _, err := errs.ErrorManyCustoms(
		uint32(math.Pow(2, 32)-1), "(555)867-5309", 'A'); err != nil {
		fmt.Println(err)
	}
	if _, err := errs.ErrorManyCustoms(0xff, "(555)67-5309", 'z'); err != nil {
		fmt.Println(err)
	}
	if _, err := errs.ErrorManyCustoms(0b1, "(555)867-5309", '🤪'); err != nil {
		fmt.Println(err)
	}
	bearer, err := errs.ErrorManyCustoms(0o7, "(555)867-5309", 'G')
	if err != nil {
		panic(err)
	}
	fmt.Println(bearer)
	// Output:
	// number too big: 4294967295
	// phone number must have 10 digits: (555)67-5309
	// input rune is not a valid english letter: "🤪"
	// Rise if you would. For that is our curse.
}

func ExampleErrorExtendBasic() {
	if err := errs.ErrorExtendBasic("555-212-4958").(errs.ConnectionError); err != nil {
		fmt.Printf("%#v\n%s\n", err, err)
	}
	if err := errs.ErrorExtendBasic("777-390-9911").(errs.ConnectionError); err != nil {
		fmt.Printf("%#v\n%v\n", err, err)
		if err.Miss() {
			fmt.Println("Call again...")
		}
	}
	// Output:
	// errs.CallError{Number:"555-212-4958"}
	// the number you dialed could not be reached: it has been disconnected
	//
	// errs.CallError{Number:"777-390-9911"}
	// the number you dialed could not be reached: no one picked up the phone
	//
	// Call again...
}

func ExampleErrorWrapOtherErrors() {
	if err := errs.ErrorWrapOtherErrors(); err != nil {
		fmt.Println("Wrapped error:", err)
		for err != nil {
			err = errors.Unwrap(err)
			fmt.Println("Unwrapping error:", err)
		}
	}
	// Output:
	// Wrapped error: http: Server closed: json: syntax error, unexpected ',': zip: not a valid zip file: bufio.Scanner: token too long
	// Unwrapping error: json: syntax error, unexpected ',': zip: not a valid zip file: bufio.Scanner: token too long
	// Unwrapping error: zip: not a valid zip file: bufio.Scanner: token too long
	// Unwrapping error: bufio.Scanner: token too long
	// Unwrapping error: <nil>
}

func ExampleErrorNotNil() {
	if err := errs.ErrorNotNil(true); err != nil {
		fmt.Printf("YAH GOOFED! %#v", err)
	}

	if err := errs.ErrorNotNil(false); err != nil {
		fmt.Println("Never going to see this")
	}
	// Output:
	// YAH GOOFED! (*errs.CallError)(nil)
}
```
