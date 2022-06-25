---
Lesson: 5
Title: "If/Else"
Draft: false
---

I'll give you two options, `if` you follow this lesson then you'll learn about
control flow in programming `else` you'll never know how to control the
execution of your code 💀

## Setup

Let's make our directory `if-else` and the files we want inside of that
directory `example_test.go` `if_else.go`

```sh
mkdir if_else
touch if-else/example_test.go if-else/if_else.go
```

Now let's open up `if_else.go` and for the very first line we'll add
```go
package if_else
```
Next for `example_test.go` for the very first line we'll add
```go
package if_else_test
```


## If Else Statements

We use `if` and `else` in programming the same as we use it in real life: `if`
you eat that piece of cake 🍰 then you're going to go over your calories for
the day. `if` you want to go for a walk 🐕 then you better be a good boy!🐶
`if` she doesn't show up in the next five minutes we're leaving, `else` we can
finally have dinner 🦞 `if` it's too much money💵 we can put it back `else` we
got ourselves a new crystal ball! 🔮

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `if_else.go`

```go
// IfElse shows you how to control the flow of logic in your application using
// if and else statements. It's also good to be aware that there is no ternary
// operator in Go.
func IfElse() {
	i := 5
	if i < 4 {
		fmt.Println("This statement will not be printed.")
	}
	if i > 4 {
		fmt.Println("This statement will print because i > 4 ==", i > 4)
	}

	if i > 10 {
		fmt.Println("This statement will not be printed.")
	} else {
		fmt.Println("We will always reach and print this statement.")
	}
}
```

#### `example_test.go`

```go
func ExampleIfElse() {
	if_else.IfElse()
	// Output:
	// This statement will print because i > 4 == true
	// We will always reach and print this statement.
}
```

## Else If

Much like life there are usually more than two options: `if` it's windy 🌬️
outside, I'm going to put on my windbreaker, `else if` it's sunny ☀️ I'm going
to put on my sunglasses 😎 and shorts 🩳 `else if` it's raining 🌧️ I need to
bring my umbrella 🌂 `else` it must be freezing so I'll need a heavy coat 🧥

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `if_else.go`

```go
// ElseIf shows you that you can branch you logic as many times as you want
// with an `else if` block
func ElseIf() {
	i := 8
	if i > 8 {
		fmt.Println("This statement will not be printed.")
	} else if i == 6 {
		fmt.Println("This statement will not be printed.")
	} else if i == 7 {
		fmt.Println("This statement will not be printed.")
	} else if i == 8 {
		fmt.Println("i == 8 so we will reach into this else if block!")
	} else {
		fmt.Println("This statement will not be printed.")
	}
}
```

#### `example_test.go`

```go
func ExampleElseIf() {
	if_else.ElseIf()
	// Output:
	// i == 8 so we will reach into this else if block!
}
```

## Scoped In If Statement

A _really_ nice feature, Go has is allowing for you to scope your variable
**inside** of your `if` statement. You may wonder 🤔

> Why is that a really nice feature?

Good question! Keep 'em coming 😁 You see, many times in programming we want to
take away something called "cognitive burden". Which is just a fancy way of
saying

> The less you have to think about, the better.

If you have a file of 60 variables, can you handle that ❓ What about 600
variables ❓ 6,000 variables ⁉️ You can see that the more you have to spend your
time thinking on, the less time your coding and the more bugs 🐛 you will
produce. How do we solve this? Remove complexity. You know what's simple? A
single variable ☝️ that will not exist after the statement you read. You _never_
have to think about it again. It lives in that one space and nowhere else. It's
a _really_ nice feature and we should **definitely** take advantage of it in
our code when we can.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `if_else.go`

```go
// DeclareInIf shows you that if you want your variable to be scoped to just an
// `if` block you can do it in Go!
func DeclareInIf() {
	if i := 5; i < 4 {
		fmt.Println("This statement will not be printed.")
	} else if i == 5 {
		fmt.Println("We can reuse i for the entire if-else statement!")
	}
	// NOTE(jay): Since `i` was declared in the scope of the `if` statement, it doesn't
	// exist outside of that scope, so if we uncomment this, we will get an
	// error: "Undeclared name: i"
	// if i == 5 {
	// 	fmt.Println("This will never work.")
	// }
}
```

#### `example_test.go`

```go
func ExampleDeclareInIf() {
	if_else.DeclareInIf()
	// Output:
	// We can reuse i for the entire if-else statement!
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png "gopher-victorious by Egon Elbre")

```go
package if_else

import "fmt"

// IfElse shows you how to control the flow of logic in your application using
// if and else statements. It's also good to be aware that there is no ternary
// operator in Go.
func IfElse() {
	i := 5
	if i < 4 {
		fmt.Println("This statement will not be printed.")
	}
	if i > 4 {
		fmt.Println("This statement will print because i > 4 ==", i > 4)
	}

	if i > 10 {
		fmt.Println("This statement will not be printed.")
	} else {
		fmt.Println("We will always reach and print this statement.")
	}
}

// ElseIf shows you that you can branch you logic as many times as you want
// with an `else if` block
func ElseIf() {
	i := 8
	if i > 8 {
		fmt.Println("This statement will not be printed.")
	} else if i == 6 {
		fmt.Println("This statement will not be printed.")
	} else if i == 7 {
		fmt.Println("This statement will not be printed.")
	} else if i == 8 {
		fmt.Println("i == 8 so we will reach into this else if block!")
	} else {
		fmt.Println("This statement will not be printed.")
	}
}

// DeclareInIf shows you that if you want your variable to be scoped to just an
// `if` block you can do it in Go!
func DeclareInIf() {
	if i := 5; i < 4 {
		fmt.Println("This statement will not be printed.")
	} else if i == 5 {
		fmt.Println("We can reuse i for the entire if-else statement!")
	}
	// NOTE(jay): Since `i` was declared in the scope of the `if` statement, it doesn't
	// exist outside of that scope, so if we uncomment this, we will get an
	// error: "Undeclared name: i"
	// if i == 5 {
	// 	fmt.Println("This will never work.")
	// }
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif "typing-furiously by Egon Elbre")

```go
package if_else_test

import if_else "basics/if-else"

func ExampleIfElse() {
	if_else.IfElse()
	// Output:
	// This statement will print because i > 4 == true
	// We will always reach and print this statement.
}

func ExampleElseIf() {
	if_else.ElseIf()
	// Output:
	// i == 8 so we will reach into this else if block!
}

func ExampleDeclareInIf() {
	if_else.DeclareInIf()
	// Output:
	// We can reuse i for the entire if-else statement!
}
```
