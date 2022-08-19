---
Lesson: 2
Title: "Variables"
Draft: false
---

A Variable is something that can inherently _**change**_ 🧑 ➡️ 🧓 To be variable
_means_ to be adaptable or changeable 👩 🔄 👨 You may think of it as varied or
versatile; saying 🗨️  **I'm adaptable** is the same as saying🗨️  **I'm
variable**

We can take the types we learned about in the previous lesson and put them into
something we name and can _**change**_ the value of. A variable! So in
programming, a variable is a type value with a name that can change it's
value.

{{% setup-instructions lessonname="variable" %}}

## Explicit Declaration and Zero Values

![https://twitter.com/egonelbre](/egon-elbre/gopher-wondering.png "gopher-wondering by Egon Elbre")

In Go, everything has a `type` and the compiler (the thing that
makes your program into something your computer can use) _needs_ to know what
`type` something is. This is so it can make optimizations, help us out
when we make errors, and other things not important right now.

When we create a variable with the `var` keyword we need to give it a name and
a `type`. When we do this it can be said that the variable has been declared
(now exists) _and_ has been initialized (has a value). So if you don't
assign a value for a type, Go will automatically assign it the zero value for
that type (we can think of it as the default value). What are those zero
values? **Glad you asked!** 😁

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `variable.go`

```go
// Zero shows off all of the zero values or what might also be
// thought of as Zero values for each type when they are not assigned
// anything.
func Zero() {
	var name string
	var x int
	var f float32
	var isHard bool
	var r rune
	var slice []int
	var map_ map[int]int
	fmt.Printf(`
string zero value is: %q
int zero value is: %d
float32 zero value is: %f
bool zero value is: %t
rune zero value is: %d or %q
slice zero value is: %+v
map zero value is: %+v`,
		name, x, f, isHard, r, string(r), slice, map_)
}
```

#### `example_test.go`

```go
func ExampleZero() {
	variable.Zero()
	// Output:
	// string zero value is: ""
	// int zero value is: 0
	// float32 zero value is: 0.000000
	// bool zero value is: false
	// rune zero value is: 0 or "\x00"
	// slice zero value is: []
	// map zero value is: map[]
}
```

## Initialization

![https://twitter.com/egonelbre](/egon-elbre/gopher-wondering.png "gopher-wondering by Egon Elbre")

Initialization is a part of developer jargon. It means to make something. Let's
look at an example, if you here

> You need to initialize that before you use it.

You're being told that the value, or lack their of, won't work and you need to
turn it into something that will work. This happens with our above code and the
`new` keyword that returns a pointer (coming soon). So we should give our
variables values that we want, in other words, we should initialize them.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `variable.go`

```go
// Explicit shows that we can explicitly type a variable to a specific
// type if we wanted to.
func Explicit() {
	// It works for all types: string, bool, int, float, rune...
	var name string = "Jay"
	// Emojis are unicode characters, they are supported in Go through runes
	var emoji rune = '😁'
	// You can do multiple declarations
	var min, max int = 0, 1000
	var isTrue bool = true
	fmt.Printf("My Name's %s! %s,\nFrom %d to %d\nGiven bool value: %t",
		name, string(emoji),
		min, max,
		isTrue)
}
```

#### `example_test.go`

```go
func ExampleExplicit() {
	variable.Explicit()
	// Output:
	// My Name's Jay! 😁,
	// From 0 to 1000
	// Given bool value: true
}
```

## Implicit Declaration

![https://twitter.com/egonelbre](/egon-elbre/gopher-wondering.png "gopher-wondering by Egon Elbre")

Go's compiler is pretty smart and it knows what `type` your variable is
depending on what the right hand side says. We can use this to our advantage!
We don't have to type out every single `type`, because as you get more
experience you'll see it's obvious what `type` something is.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `variable.go`

```go
// Implicit shows that we don't need to declare what the type is as
// long as we initialize it on the right.
func Implicit() {
	// It works for all types: string, bool, int, float, rune...
	var question = "Does anyone have any room for"
	// Emojis are unicode characters, they are supported in Go through runes
	var emoji = '🥧'
	var pi = 3.14159
	var isTrue = false
	fmt.Printf("%s %s,\nMore Pi %f\nGiven bool value: %t",
		question, string(emoji),
		pi,
		isTrue)
}
```

#### `example_test.go`

```go
func ExampleImplicit() {
	variable.Implicit()
	// Output:
	// Does anyone have any room for 🥧,
	// More Pi 3.141590
	// Given bool value: false
	//
}
```

## Assignment Operator

![https://twitter.com/egonelbre](/egon-elbre/gopher-wondering.png "gopher-wondering by Egon Elbre")

There is an even faster, easier, and more idomatic way of declaring a variable.
`:=` is the assignment operator, which **I** call the gopher operator because
if this 👉 **:)** is a smiley face then this 👉 **:=** is two beady eyes and
some buck teeth. Remind you of anything cute, blue, and furry?

![https://twitter.com/egonelbre](/egon-elbre/aww.gif "aww by Egon Elbre")

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `variable.go`

```go
// AssignmentOperator shows the preferred and idomatic way of declaring
// variables in Go. With the `:=` operator
func AssignmentOperator() {
	// It works for all types: string, bool, int, float, rune...
	statement := "Short and Sweet. Very Nice!"
	emoji := '👍'
	mySpecialNumber := 12648430
	isQuick := true
	fmt.Printf("%s %s\nI really need %x\nStill works? %t",
		statement, string(emoji), mySpecialNumber, isQuick)
}
```

#### `example_test.go`

```go
func ExampleAssignmentOperator() {
	variable.AssignmentOperator()
	// Output:
	// Short and Sweet. Very Nice! 👍
	// I really need c0ffee
	// Still works? true
}
```

## Source File 📄

[The Source File](https://goplay.tools/snippet/bwQk-IxHg5w)

## Test File 📝

[The Test File](https://goplay.tools/snippet/vaF12YE6Tu0)
