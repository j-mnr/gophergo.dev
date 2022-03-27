---
title: "For Loop"
draft: false
lesson: 5
---

L➿➿➿➿➿➿ps, they just go on and on. At least, that's what we're aiming for
when we use them. 😉

In programming there are times when you have some code that looks _very_
repetitive. (Listen carefully! 👂 This is where it starts getting fun!)

```go
func NoLoops() {
	fmt.Println("This is line number 1")
	fmt.Println("This is line number 2")
	fmt.Println("This is line number 3")
	fmt.Println("This is line number 4")
	fmt.Println("This is line number 5")
}
```

Look at all that repeated code! Not only is this a waste of time, it's a waste
of lines, and a waste of reading! We as software engineers **pride** ourselves
off of Keeping It Stupidly Simple (KISS). We are pattern recognition machines.
We recognize and exploit the patterns we find to make for the most efficient,
readable, and optimized code possible. It's _really_ fun❗ Trust me 😁 the more
you get the computer to do the work for you the better you're going to feel. So
can you spot the pattern in our `NoLoops` function?

The only thing that changes between each line of code is the number. 🤔 So if
we had a way to _loop_ over that same line of code and only update the number
we'd save a lot of lines and it would be much more maintainable.

```go
func Loop() {
	for i := 1; i <= 5; i++ {
		fmt.Println("This is line number", i)
	}
}
```

## Setup

Let's make our directory `for-loop` and the files we want inside of that
directory `example_test.go` `for_loop.go`

```sh
mkdir for-loop
touch for_loop/example_test.go for_loop/for_loop.go
```

Now let's open up `for_loop.go` and for the very first line we'll add
```go
package for_loop
```
Next for `example_test.go` for the very first line we'll add
```go
package for_loop_test
```

## For Loop

The `for` keyword in Go is the only way to loop in the whole language, but it
is **more** than sufficient as we'll find out by the end of the lesson. What
you tell the computer is

> For `this value` I want you to keep going `until you reach this condition`...
> Oh and `do this` every time you loop back to the top. K? Thx, byeee~ 👋😘

This is one of those features you just have to play with to understand. So....

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### for_loop.go

```go
// ForLoop shows how to initialize and run through a for loop. The structure of
// a for loop looks like `for initialize; stop condition; increment {`
func ForLoop() {
	// We initialize a variable `i := 0`
	// We end the statement with a semicolon `;`
	// So that we can start a new statement.
	// We declare what condition we want to stop at `i <= 10;`
	// This means if `i` is less than or equal to 10 keep looping.
	// If it's 11 or higher. Stop!
	// We increment the values that will change on the next loop `i++`
	// This also could be read as `i = i + 1`
	for i := 0; i <= 10; i++ {
		fmt.Println("For loop i:", i)
	}
}
```

#### example_test.go

```go
func ExampleForLoop() {
	for_loop.ForLoop()
	// Output:
	// For loop i: 0
	// For loop i: 1
	// For loop i: 2
	// For loop i: 3
	// For loop i: 4
	// For loop i: 5
	// For loop i: 6
	// For loop i: 7
	// For loop i: 8
	// For loop i: 9
	// For loop i: 10
}
```

## While Loop

In other programming languages there is another form of looping through code
known as a while loop. It's called that because you're telling the program.

> While `a certain condition` is true continue looping.

Remember how I was talking about recognizing patterns at the start of this
lesson? Well the people behind Go recognized something. A while loop is a `for`
loop without a start or next part. So it would be:

`for` {{< ln_thr_code "no need := 0;" >}} `stop condition` {{< ln_thr_code
";no next" >}} `{`

![https://twitter.com/egonelbre](/gopher-thinking.png)

Do we _really_ need another keyword for the same loop? It would then require
it's own syntax and knowledge and may spawn more feature requests like being
able to `do while` or something else -- You know what? Let's just not.

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### for_loop.go

```go
// WhileLoop shows how to create a while loop in Go by just using a for loop. A
// while loop is just a for loop with only the condition to stop looping.
// `for stop condition {`
func WhileLoop() {
	// In other languages there is something called a while loop.
	// It only stops on a condition. Like the middle part of a `for` loop.
	i := 1
	for i <= 128 {
		fmt.Println("While loop i:", i)
		// This can also be written as
		// i = i + i
		i += i
	}
}
```

#### example_test.go

```go
func ExampleWhileLoop() {
	for_loop.WhileLoop()
	// Output:
	// While loop i: 1
	// While loop i: 2
	// While loop i: 4
	// While loop i: 8
	// While loop i: 16
	// While loop i: 32
	// While loop i: 64
	// While loop i: 128
}
```

## Forever Loop

You may have thought of something looking at the previous example.

![https://twitter.com/egonelbre](/gopher-thinking.png)

> What happens if we have a condition that never evaluates to true?

That's a great question! Keep 'em coming 😄 To my knowledge it doesn't have a
name, but _I'm_ going to call it a forever loop because that's essentially what
it is. A loop that has no exit! It's very simple to accomplish in Go just use
`for {` and Ta-da!! The program will **never** terminate. 👀

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

As a precaution I've put in a `break` statement, which we're going to learn
about next, but if you want, delete the break statement and in `cmd/main.go`
you can do

```go
import for_loop "basics/for-loop"

func main() {
  for_loop.ForeverLoop()
}
```

to see it print continuous statements out. Press Control + C to interrupt the
programs execution 💀

#### for_loop.go

```go
// ForeverLoop shows how to create a loop that will never stop running if there
// is no break condition inside of it.
func ForeverLoop() {
	// A `for` loop with no terminating condition, will never stop!
	// Unless we force ourselves out of the loop by `break`ing it.
	fmt.Println("How many print statements do you want?\nAnother One,")
	for {
		fmt.Println("And Another One,")
		// XXX: This loop will not stop if you remove this. Try it out!
		// Use CTRL+C to stop the program.
		break
	}
	fmt.Println("We the best.")
}
```

#### example_test.go

```go
func ExampleForeverLoop() {
	for_loop.ForeverLoop()
	// Output:
	// How many print statements do you want?
	// Another One,
	// And Another One,
	// We the best.
}
```

## Continue and Break

Sometimes you want your code to stop (`break`) early if a condition is met or
to `continue` to the top of the next loop if a condition is met. This gives you
even more flexibility and control over the flow of your program.

### Uses

#### continue

Let's say you're counting the number of letters (`byte`) in a `string`, but if
the letter (`byte`) is a certain letter (`byte`) you **don't** want to count it
because reasons....

![https://twitter.com/egonelbre](/gopher-not-sure-if.png)

IT COULD HAPPEN! You never know! Anyways, you would make an `if` statement and
check whether the letter was equal to that letter and not count it. `if ltr ==
'j' { continue }`

#### break

Sometimes you have more than one terminating condition that wouldn't fit well
into the `for` loop declaration `stop condition;` part. You can break anywhere
in the loop execution as well! Let's say you're having fun with numbers and you
find out when applying a formula to a number the number **always** goes to
zero. So you make your loop: `for myNum := num; myNum == 0; myNum =
mandelBrot(myNum) {` but then find out some of the numbers never go to zero! 😱
We could then check `if myNum > 100000000 { break }` and maybe do some extra
stuff in there.

### Coding Time!

![https://twitter.com/egonelbre](/gopher-heart-eyes.png)

#### for_loop.go

```go
// ContinueBreakLoop shows how we can skip logic downwards by using a continue
// statement and how we can break out of a loop on a certain condition.
func ContinueBreakLoop() {
	// We can also skip while looping if we don't want to perform any action.
	i := 0
	for {
		i++
		if i%2 == 0 {
			// This will make sure the logic below is not ran. It will make us go
			// back to the start of the loop where `i++` is.
			continue
		}
		fmt.Println("ContinueBreak loop i:", i)
		if i == 25 {
			// This will break us out from the forever loop
			break
		}
	}
}
```

#### example_test.go

```go
func ExampleContinueBreakLoop() {
	for_loop.ContinueBreakLoop()
	// Output:
	// ContinueBreak loop i: 1
	// ContinueBreak loop i: 3
	// ContinueBreak loop i: 5
	// ContinueBreak loop i: 7
	// ContinueBreak loop i: 9
	// ContinueBreak loop i: 11
	// ContinueBreak loop i: 13
	// ContinueBreak loop i: 15
	// ContinueBreak loop i: 17
	// ContinueBreak loop i: 19
	// ContinueBreak loop i: 21
	// ContinueBreak loop i: 23
	// ContinueBreak loop i: 25
}
```

## The Whole File

![https://twitter.com/egonelbre](/gopher-victorious.png)

```go
package for_loop

import "fmt"

// ForLoop shows how to initialize and run through a for loop. The structure of
// a for loop looks like `for initialize; stop condition; increment {`
func ForLoop() {
	// We initialize a variable `i := 0`
	// We end the statement with a semicolon `;`
	// So that we can start a new statement.
	// We declare what condition we want to stop at `i <= 10;`
	// This means if `i` is less than or equal to 10 keep looping.
	// If it's 11 or higher. Stop!
	// We increment the values that will change on the next loop `i++`
	// This also could be read as `i = i + 1`
	for i := 0; i <= 10; i++ {
		fmt.Println("For loop i:", i)
	}
}

// WhileLoop shows how to create a while loop in Go by just using a for loop. A
// while loop is just a for loop with only the condition to stop looping.
// `for stop condition {`
func WhileLoop() {
	// In other languages there is something called a while loop.
	// It only stops on a condition. Like the middle part of a `for` loop.
	i := 1
	for i <= 128 {
		fmt.Println("While loop i:", i)
		// This can also be written as
		// i = i + i
		i += i
	}
}

// ForeverLoop shows how to create a loop that will never stop running if there
// is no break condition inside of it.
func ForeverLoop() {
	// A `for` loop with no terminating condition, will never stop!
	// Unless we force ourselves out of the loop by `break`ing it.
	fmt.Println("How many print statements do you want?\nAnother One,")
	for {
		fmt.Println("And Another One,")
		// XXX: This loop will not stop if you remove this. Try it out!
		// Use CTRL+C to stop the program.
		break
	}
	fmt.Println("We the best.")
}

// ContinueBreakLoop shows how we can skip logic downwards by using a continue
// statement and how we can break out of a loop on a certain condition.
func ContinueBreakLoop() {
	// We can also skip while looping if we don't want to perform any action.
	i := 0
	for {
		i++
		if i%2 == 0 {
			// This will make sure the logic below is not ran. It will make us go
			// back to the start of the loop where `i++` is.
			continue
		}
		fmt.Println("ContinueBreak loop i:", i)
		if i == 25 {
			// This will break us out from the forever loop
			break
		}
	}
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/typing-furiously.gif)

```go
package for_loop_test

import for_loop "basics/for-loop"

func ExampleForLoop() {
	for_loop.ForLoop()
	// Output:
	// For loop i: 0
	// For loop i: 1
	// For loop i: 2
	// For loop i: 3
	// For loop i: 4
	// For loop i: 5
	// For loop i: 6
	// For loop i: 7
	// For loop i: 8
	// For loop i: 9
	// For loop i: 10
}

func ExampleWhileLoop() {
	for_loop.WhileLoop()
	// Output:
	// While loop i: 1
	// While loop i: 2
	// While loop i: 4
	// While loop i: 8
	// While loop i: 16
	// While loop i: 32
	// While loop i: 64
	// While loop i: 128
}

func ExampleForeverLoop() {
	for_loop.ForeverLoop()
	// Output:
	// How many print statements do you want?
	// Another One,
	// And Another One,
	// We the best.
}

func ExampleContinueBreakLoop() {
	for_loop.ContinueBreakLoop()
	// Output:
	// ContinueBreak loop i: 1
	// ContinueBreak loop i: 3
	// ContinueBreak loop i: 5
	// ContinueBreak loop i: 7
	// ContinueBreak loop i: 9
	// ContinueBreak loop i: 11
	// ContinueBreak loop i: 13
	// ContinueBreak loop i: 15
	// ContinueBreak loop i: 17
	// ContinueBreak loop i: 19
	// ContinueBreak loop i: 21
	// ContinueBreak loop i: 23
	// ContinueBreak loop i: 25
}
```
