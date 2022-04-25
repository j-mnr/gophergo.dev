---
Lesson: 5
Title: "Switch"
Draft: false
---

A `switch` statement is made up of `case` statements and if necessary a
`default` statement, if none of the `case` statements match. We can think of
`switch` as being a more versatile `if/else` statement. We have the ability to
do everything the same as `if/else` statements with a clearer and possibly more
pointed statement. We prefer a `switch` over an `if/else` in Go, if possible.

## Setup

Let's make our directory `switches` and the files we want inside of that directory
`example_test.go` `switches.go`

```sh
mkdir switches
touch switches/example_test.go switches/switches.go
```

Now let's open up `switches.go` and for the very first line we'll add
```go
package switches
```
Next for `example_test.go` for the very first line we'll add
```go
package switches_test
```

## Switch Statement

In the last lesson we learned about `if/else` statements. They are great, but
very limited in what they do and how they function. They are much more verbose
(they say a lot without doing much more) than simple `switch` statements in Go
and it is normal in Go to be short and sweet. ❤️

![https://twitter.com/egonelbre](/egon-elbre/gopher-blushing.png)

### Compare with If/Else

In programming we're always seeking to create simple systems. The less you have
to think about, the better you will program. This goes for every software
engineer on the planet. This goes for **literally** every person on the planet
😂, right? The less you have to focus on the more you can get your task done.
Simple. We can see with our `switch` we **only** need to think about the very
first `rune` in the provided `string` and that's it!

```go
myStr := "Switches can focus content"
switch myStr[0] {
  case 's':
    // Do stuff
  case 'S':
    // Do stuff
  default:
    // Do if nothing else matches
}
```

```go
myStr := "If else makes no guarantee"
if (myStr[0] == 'i') {
  // Do stuff
} else if (myStr[1] == 'F') {
  // Nothing to 👆 do with first rune!
  // Do stuff
} else if (myStr == "No focus") {
  // Nothing to do 👆 with runes!
  // Do stuff
} else {
  // Do if nothing else matches
}
```

With `if/else` statements there is nothing driving us to _only_ look at
the first `rune` in the `string`, we can look at the second one or we could
just make some other statement that has nothing to do with the topic at hand!
😬

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### switches.go

```go
// SwitchBasic shows how to switch what logic to perform depending on a case
// and if that none of the criteria are met we can perform some default logic.
func SwitchBasic() {
	i := 0
	switch i {
	case 1:
		fmt.Println("i is one")
	case 2:
		fmt.Println("i is two")
	case 3:
		fmt.Println("i is three")
	default:
		fmt.Println("i does not have a matching case.")
	}
}
```

#### example_test.go

```go
func ExampleSwitchBasic() {
	switches.SwitchBasic()
	// Output:
	// i does not have a matching case.
}
```

## Multiple Cases

`switch` statements don't stop there, however. They allow for having multiple
values for the same `case`. Which leads to much easier to read and understand
code.

### Compare with If/Else

It does happen where you have something that can be captured under multiple
statements with `if/else` and you have two choices at that point, either put
them all together in one long run on statement or break them up into multiple
`else if` blocks and repeat the code ☠️ We'll do the first one because duplicate
code is unmaintainable code.

```go
myFluctuatingInt := 8
if (myFluctuatingInt == 0 || myFluctuatingInt == 1 || myFluctuatingInt == 2) {
  // Do stuff
} else if (myFluctuatingInt == 3 || myFluctuatingInt == 4 || myFluctuatingInt
== 5 || myFluctuatingInt == 6 || myFluctuatingInt == 7 || myFluctuatingInt == 8) {
  // Do stuff
}
```

```go
myFluctuatingInt := 8
switch myFluctuatingInt {
  case 0, 1, 2:
    // Do stuff
  case 3, 4, 5, 6, 7, 8:
    // Do stuff
}
```

As we can see it is **much** clearer to use a `switch` statement, here. We
boil down what is important to look at and then allow for things to be done in
a flexible manner. This is great engineering! 😉

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### switches.go

We need to add the line `import time` to the top of our file, next to `import
fmt` for this to work. Don't worry about it for now, we'll understand more on
importing later.

```go
// SwitchMultiple shows that we can perform the same logic for multiple cases
// using the same `case` keyword by comma separating the values.
func SwitchMultiple() {
	month := time.August
	switch month {
	case time.December, time.January, time.February:
		fmt.Println("Winter is here.")
	case time.June, time.July, time.August:
		fmt.Println("Must have some Summer flare.")
	case time.October, time.November, time.September:
		fmt.Println("Autumn is in the air.")
	case time.March, time.April, time.May:
		fmt.Println("Looks like it's Spring!")
	}
}
```

Feel free to play around with the month and run `go test` to hit the other
cases!

#### example_test.go

```go
func ExampleSwitchMultiple() {
	switches.SwitchMultiple()
	// Output:
	// Must have some Summer flare.
}
```

## Type Assertions

In a static language (one that uses `type` for everything), it's hard to use
data coming into the application that doesn't have a type. One example is JSON
data in web development.

When JSON data comes into the application it can be in any form! 😲 There could
be `int` types, `bool` types, `string` types, `[]string` a string slice, or a
big giant `struct{}` type, or anything! 😵 Therefore we need to be able to
_assert_ on what that `type` is and process it accordingly.

In Go we can almost say the empty interface `interface{}` is of `type` `any`.
Meaning that anything can fit in the empty interface `interface{}` which is
true! But it's still of type `interface{}` not `any`. It's out of scope for
what we need to learn but if you're interested, here's some 🤓[reading
material](https://jordanorelli.com/post/32665860244/how-to-use-interfaces-in-go)
📖

Also we will learn about `interface` soon enough so don't worry that this is
confusing for now, You got this! 💪

### Compare with If/Else

There is no comparison with `if/else` in this case. It can't do it 🤷

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### switches.go

```go
// SwitchType shows us that we can do type assertions using switch statements!
// This is particularly useful when getting JSON data with no idea what's
// inside.
func SwitchType(i interface{}) {
	switch t := i.(type) {
		case int:
			fmt.Printf("You seem like an %T-eresting type.\n", t)
		case bool:
			fmt.Printf("You %T! I knew it was you all along.\n", t)
		case []string:
			fmt.Printf("Hey, hey. Save me a slice! %T\n", t)
		default:
			fmt.Printf("We've never seen a %T like this.\n", t)
	}
}
```

#### example_test.go

```go
func ExampleSwitchType() {
	switches.SwitchType(true)
	switches.SwitchType(8)
	switches.SwitchType([]string{"some", "strings"})
	switches.SwitchType(struct{}{})
	// Output:
	// You bool! I knew it was you all along.
	// You seem like an int-eresting type.
	// Hey, hey. Save me a slice! []string
	// We've never seen a struct {} like this.
}
```

## Switch No Value Given

So we've seen `switch` statements do well in a lot of areas compared to
`if/else`, but one area we may see lacking is the ability to use `bool`
assertions! 🥲 Well, fear not! We can do _exactly_ that, in fact!

### Compare with If/Else

We see in this case both `switch` and `if/else` perform the same. Though,
personally I find it much easier to refactor `switch` statements. Meaning, I
find it much easier to move a `case` statement around or add another one and in
`vim` the `%` key lets me go to the top of a `switch` statement when I'm on the
`}` unlike with an `if/else` statement.

```go
apple := "🍎"
if (apple == "🍏") {
  // Do stuff
} else if (apple == "🍐") {
  // Do stuff
} else if (apple == "🍌") {
  // Do stuff
}
```

```go
apple := "🍎"
switch {
case apple == "🍏":
  // Do stuff
case apple == "🍐":
  // Do stuff
case apple == "🍌":
  // Do stuff
}
```

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### switches.go

```go
// SwitchNoValue shows that you don't have to give a value to the `switch`
// statement, but instead perform true or false (bool) assertions on a given
// value.
func SwitchNoValue() {
	// This will get your current month! So this test may fail, see if you can't
	// update it to make it pass! 😁
	t := time.Now().Month()
	switch {
	case t <= time.February || t == time.December:
		fmt.Println("Winter is here.")
	case t <= time.May:
		fmt.Println("Looks like it's Spring!")
	case t <= time.August:
		fmt.Println("Must have some Summer flare.")
	case t <= time.November:
		fmt.Println("Autumn is in the air.")
	}
}
```

#### example_test.go

```go
func ExampleSwitchNoValue() {
	switches.SwitchNoValue()
	// Output:
	// Looks like it's Spring!
}
```

## Fallthrough A Switch

For completeness we have the `fallthrough` keyword in Go. It is pretty much
obsolete because the reason you want things to fall through would be to hit
multiple `case` statements, but we already know we can do that. 😏 And in using
it without explaining, it can lead to spaghetti 🍝 code. So it isn't very
useful, but if you find a use let me know! 😁 👍

### Compare with If/Else

`if/else` has no ability to `fallthrough` to another `if/else` statement.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### switches.go

```go
// SwitchFallthrough shows off the **very** rarely used `fallthrough` keyword
// in Go. If you're using `fallthrough` there's probably a better solution to
// your problem.
func SwitchFallthrough() {
	switch "three" {
	case "three":
		fmt.Println("Floor number three")
		fallthrough
	case "two":
		fmt.Println("Floor number two")
		fallthrough
	case "one":
		fmt.Println("Floor number one")
		fallthrough
	default:
		fmt.Println("Now arriving bottom floor.")
	}
}
```

#### example_test.go

```go
func ExampleSwitchFallthrough() {
	switches.SwitchFallthrough()
	// Output:
	// Floor number three
	// Floor number two
	// Floor number one
	// Now arriving bottom floor.
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png)

```go
package switches

import (
	"fmt"
	"time"
)

// SwitchBasic shows how to switch what logic to perform depending on a case
// and if that none of the criteria are met we can perform some default logic.
func SwitchBasic() {
	i := 0
	switch i {
	case 1:
		fmt.Println("i is one")
	case 2:
		fmt.Println("i is two")
	case 3:
		fmt.Println("i is three")
	default:
		fmt.Println("i does not have a matching case.")
	}
}

// SwitchMultiple shows that we can perform the same logic for multiple cases
// using the same `case` keyword by comma separating the values.
func SwitchMultiple() {
	month := time.August
	switch month {
	case time.December, time.January, time.February:
		fmt.Println("Winter is here.")
	case time.June, time.July, time.August:
		fmt.Println("Must have some Summer flare.")
	case time.October, time.November, time.September:
		fmt.Println("Autumn is in the air.")
	case time.March, time.April, time.May:
		fmt.Println("Looks like it's Spring!")
	}
}

// SwitchType shows us that we can do type assertions using switch statements!
// This is particularly useful when getting JSON data with no idea what's
// inside.
func SwitchType(i interface{}) {
	switch t := i.(type) {
	case int:
		fmt.Printf("You seem like an %T-eresting type.\n", t)
	case bool:
		fmt.Printf("You %T! I knew it was you all along.\n", t)
	case []string:
		fmt.Printf("Hey, hey. Save me a slice! %T\n", t)
	default:
		fmt.Printf("We've never seen a %T like this.\n", t)
	}
}

// SwitchNoValue shows that you don't have to give a value to the `switch`
// statement, but instead perform true or false (bool) assertions on a given
// value.
func SwitchNoValue() {
	// This will get the current month. So this test may fail, see if you can't
	// update the test with the correct string to make it pass! 😁
	t := time.Now().Month()
	switch {
	case t <= time.February || t == time.December:
		fmt.Println("Winter is here.")
	case t <= time.May:
		fmt.Println("Looks like it's Spring!")
	case t <= time.August:
		fmt.Println("Must have some Summer flare.")
	case t <= time.November:
		fmt.Println("Autumn is in the air.")
	}
}

// SwitchFallthrough shows off the **very** rarely used `fallthrough` keyword
// in Go. If you're using `fallthrough` there's probably a better solution to
// your problem.
func SwitchFallthrough() {
	switch "three" {
	case "three":
		fmt.Println("Floor number three")
		fallthrough
	case "two":
		fmt.Println("Floor number two")
		fallthrough
	case "one":
		fmt.Println("Floor number one")
		fallthrough
	default:
		fmt.Println("Now arriving bottom floor.")
	}
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif)

```go
package switches_test

import "basics/switches"

func ExampleSwitchBasic() {
	switches.SwitchBasic()
	// Output:
	// i does not have a matching case.
}

func ExampleSwitchMultiple() {
	switches.SwitchMultiple()
	// Output:
	// Must have some Summer flare.
}

func ExampleSwitchType() {
	switches.SwitchType(true)
	switches.SwitchType(8)
	switches.SwitchType([]string{"some", "strings"})
	switches.SwitchType(struct{}{})
	// Output:
	// You bool! I knew it was you all along.
	// You seem like an int-eresting type.
	// Hey, hey. Save me a slice! []string
	// We've never seen a struct {} like this.
}

func ExampleSwitchNoValue() {
	switches.SwitchNoValue()
	// Output:
	// Looks like it's Spring!
}

func ExampleSwitchFallthrough() {
	switches.SwitchFallthrough()
	// Output:
	// Floor number three
	// Floor number two
	// Floor number one
	// Now arriving bottom floor.
}
```
