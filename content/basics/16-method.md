---
Lesson: 16
Title: "Method"
Draft: false
---

There's a good chance you're coming from another programming language while
reading this and if that's true, it must be explicitly said:

Go does not support _Object-Oriented Programming_ (OOP). Sorry not sorry 🙃 I
say that because it has been stated (and argued 😂)

[time](https://codeburst.io/inheritance-is-evil-stop-using-it-6c4f1caf5117)-
[and](https://neethack.com/2017/04/Why-inheritance-is-bad/)-
[time](https://bytecrafter.blogspot.com/2008/05/why-inheritance-is-bad.html)-
[again](https://softwareengineering.stackexchange.com/questions/134097/why-should-i-prefer-composition-over-inheritance)

That inheritance belongs in the bin 🗑️  Now don't get me wrong

- Abstraction? ✅ We got that
- Polymorphism? ✅ We got that
- Encapsulation? ✅ We got that
- Inheritance? ❌ 🙅 No we have something better -- **Composition**

Many principles that make OOP are great 😁👍 and just because your language
supports Inheritance, doesn't _mean_ you have to use it. It's just the
_glaring_ fault that inheritance brings. Much like a chair 🪑 That has a half
broken leg. Are you going to sit on it? 😏 But that's OK, because Go does
better! We don't have to look into the future 🔮, we don't have to break
encapsulation and we don't have to share unnecessary state or methods 😬 And
better yet! We get to have our cake🍰 and eat it too because if you are
familiar with OOP, the style can be mimicked in Go. So don't fret my OOP
friends you are welcome and have a place here 🥰 and the first step is
introducing methods.

Speaking of methods, what is a method? 🤔 If you're brand new to programming,
Welcome! 👋😁 I hope you have as much fun as I do engineering solutions to my
clients problems (that client sometimes being myself 😂). Now back to the
question.

What's a method? A method is identical to a function. It just changes the star
of the show 🌟 to the `struct` that you pass in. _Functional programming_
revolves around inputs going to outputs and chaining 🔗🔗🔗🔗 them together.
Where as OOP revolves around a `struct`, in the case of Go, and updating the
fields (also known as updating encapsulated state) of that `struct` while
having inputs interact with it; there may or may not be an output.

{{% setup-instructions lessonname="method" %}}

## Initial Setup

All of our examples will fail until we do [the last
part](#pretty-printing-with-string) so hold on to your britches partner 🤠 or
run them in the meantime and see what output it expects in comparison to what
we will write for each of our tests. It'll be 🐝 good learning 😄

Now we're going to use a `Gopher` as a `struct` like we did in the previous
lesson on [structs](/basics/14-struct) and we'll create a constructor just the
same. Using idiomatic Go naming conventions by calling it `New` as we already
learned about.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `method.go`

```go
type Gopher struct {
	name      string
	isCoding  bool
	favNumber int
}

// New returns a constructed Gopher of the given values.
func New(name string, isCoding bool, favNumber int) *Gopher {
	return &Gopher{name: name, isCoding: isCoding, favNumber: favNumber}
}
```

#### `example_test.go`

```go
func ExampleNew() {
	g := method.New("Ghidra", false, 0xBEEF)
	fmt.Print(g)
	// Output: Hi! I'm Ghidra and my favorite number is 48879
}
```

## Pointer Receiver 🆚 Value Receiver

Now on to the meat 🥩 and 🥔 potatoes of this lesson. **Methods!** There are
two types of methods:

1. Pointer Receiver Methods
1. Value Receiver Methods

As we learned in the [last lesson](/basics/15-pointer) pointers **change**
things, and when you pass by value you make a copy of that thing.

Said again a method is making our `Gopher` the star of the show
![https://twitter.com/egonelbre](/egon-elbre/gopher-smiling-blushing.png "gopher-smiling-blushing by Egon Elbre")
because we're focusing on them and the inputs are secondary to our `Gopher`.

Now why would we want to change our `Gopher`? (Pointer Receiver) and why would
we only pass in a copy of our `Gopher`? (Value Receiver)

Short answer: It's a signal to you and anyone reading your code.

### Value Receiver Method

Remember when something is [`const`](/basics/03-constants) -- We're done. Brain
🧠 shut off. We don't have to **_EVER_** worry about it changing, ever. That's
so nice. Set it 🧑‍🍳 and forget it. Meaning when a method is a value
receiver you can know with 💯 % certainty that it **will not change the
`struct`**. Therefore when you are sure that you don't need to change anything
in the `struct` you should use a value receiver method.

### Pointer Receiver Method

These are much more typical and closer to what OOP strives for. If you are
taking in inputs to change the underlying `struct` we **must use** a pointer
receiver method. This trips up new developers all the time when their `struct`
is not changing, with the method they put on it. So let's go see exactly what
happens! 🏃

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `method.go`

```go
// DoesNotChangeFavNumber is a value receiver method that does not change the
// favNumber of the Gopher.
func (g Gopher) DoesNotChangeFavNumber(favNumber int) {
	// Since `g` is a copy and not a pointer `*`, the favNumber we update is
	// specific to this copy of `g`.
	// The original `g` still has its favNumber intact.
	g.favNumber = favNumber
}

// DoesChangeFavNumber is a pointer receiver method that will change the
// favNumber of the Gopher.
func (g *Gopher) DoesChangeFavNumber(favNumber int) {
	// We can drop the dereference to the pointer `(*g)` to just `g` if we wanted
	// because Go will automagically 🪄 dereference pointer values for us!
	(*g).favNumber = favNumber
	// This works too and is preferred:
	// g.favNumber = favNumber
}
```

#### `example_test.go`

```go
func ExampleGopher_DoesChangeFavNumber() {
	g := method.New("Gorple", false, 99)
	g.DoesChangeFavNumber(800)
	fmt.Println(g)
	g.DoesChangeFavNumber(-28298)
	fmt.Println(g)
	g.DoesChangeFavNumber(0xDEAD)
	fmt.Println(g)
	g.DoesChangeFavNumber(0xC0FFEE)
	fmt.Println(g)
	// Output:
	// Hi! I'm Gorple and my favorite number is 800
	// Hi! I'm Gorple and my favorite number is -28298
	// Hi! I'm Gorple and my favorite number is 57005
	// Hi! I'm Gorple and my favorite number is 12648430
}

func ExampleGopher_DoesNotChangeFavNumber() {
	g := method.New("Galum", false, 99)
	g.DoesNotChangeFavNumber(800)
	fmt.Println(g)
	g.DoesNotChangeFavNumber(-28298)
	fmt.Println(g)
	g.DoesNotChangeFavNumber(0xDEAD)
	fmt.Println(g)
	g.DoesNotChangeFavNumber(0xC0FFEE)
	fmt.Println(g)
	// Output:
	// Hi! I'm Galum and my favorite number is 99
	// Hi! I'm Galum and my favorite number is 99
	// Hi! I'm Galum and my favorite number is 99
	// Hi! I'm Galum and my favorite number is 99
}
```

## Methods 🆚 Functions

In Go you **cannot** overload functions. For example a simple add function
`func Add(x, y int) int` **cannot** be redeclared as `func Add(x, y float64)
float 64`. The complier will complain that the function was "redeclared in this
block". So what happens if we declare a method and a function with the same
name and parameters? 🤔

We'll find out when we get to coding, but first let's talk 🗣️ about methods and
functions one more time. For some people it's easier to see most, if not
everything as a **thing**. For example a dog 🐕 or a frog 🐸 maybe some fog 🌫️
and they want to tell stories about _those_ things. The `Dog.Bark()` or the
`Frog.Jump()` or `Fog.Appear()` this is perfect for methods then. Methods exist
on that `type`. The story 📖 is driven by these objects.

For other people it's easier to see cut ✂️  and dry 🏜️  inputs to output. The
action performed and the result of each function creates the story
`FindFirst(dog, HasCollar)`, `Collect(frogs, NotFullBucket)`, `Test(fog,
WithHygrometer)` this is perfect for functions and even better in Go as
functions are first class and allow for higher-ordered functions. We can see
that in action as we pass a supporting function into the above functions. 👆

For other people they use the right tool 🪚 for the right job 🪵. Sometimes a
method makes more sense other times a function does and Go is flexible in the
regard that it allows you to program for your situation. Even when you want to
have the same name and parameters for a method to a `struct` and to a function
😉

After you code up the below examples, think of which makes more sense for the
situation.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `method.go`

```go
// StartCoding is a pointer receiver method that starts the Gopher to coding.
func (g *Gopher) StartCoding() { g.isCoding = true }

// StopCoding is a pointer receiver method that stops the Gopher from coding.
func (g *Gopher) StopCoding() { g.isCoding = false }

// StartCoding is a package function that starts the Gopher to coding.
func StartCoding(g *Gopher) { g.isCoding = true }

// StopCoding is a package function that stops the Gopher from coding.
func StopCoding(g *Gopher) { g.isCoding = false }
```

#### `example_test.go`

```go
func ExampleStartCoding() {
	g := method.New("Geany", false, 3333)
	method.StartCoding(g)
	fmt.Println(g)
	// Output:
	// Let me get back to you after I'm done coding.
}

func ExampleStopCoding() {
	g := method.New("Ghjil", true, 0b010101011110111111)
	method.StopCoding(g)
	fmt.Println(g)
	// Output:
	// Hi! I'm Ghjil and my favorite number is 87999
}

func ExampleGopher_StartCoding() {
	g := method.New("Garkicye", true, 0xBA5ED)
	g.StartCoding()
	fmt.Println(g)
	// Output:
	// Let me get back to you after I'm done coding.
}

func ExampleGopher_StopCoding() {
	g := method.New("Gremeri", true, 87999)
	g.StopCoding()
	fmt.Println(g)
	// Output:
	// Hi! I'm Gremeri and my favorite number is 87999
}
```

## Pretty Printing with String()

A feather 🪶 light introduction to the next lesson, interfaces. There exists in
the `fmt` package a _very_ useful `interface` known as `Stringer` the docs (the
documentation) say

> The String method is used to print values passed as an operand to any format
> that accepts a string or to an unformatted printer such as **Print**

but don't take my word for it, open up your terminal and put `go doc
fmt.Stringer` and see what you get 😀

The thing about an `interface` is it only defines behavior, and has no
implementation. Which means they are impossible to test because they don't _do_
something they _define_ something. So congratulate 🥳🎊🥳 yourself after you
code this up because you will have implemented your very first `interface`!
🎉🎉

I wanted to show off where a real world 🌐 example of a value receiver is
useful. In order to satisfy the `Stringer interface` you must use a value
receiver. And now you'll know how to make your `struct` pretty print. That is
to say, have a nice formatted message that's easy to understand.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `method.go`

```go
// String satisfies the fmt.Stringer interface and will be used anytime we try
// to fmt.Print(g), fmt.Printf(g), fmt.Println(g) -- g == Gopher
// We can see it is a value receiver method, meaning it takes a copy of a
// Gopher. This is because we **don't** want to update any values of our
// original Gopher and by using a value receiver method we make sure that
// cannot happen.
func (g Gopher) String() string {
	if g.isCoding {
		return "Let me get back to you after I'm done coding."
	} else {
		return fmt.Sprint(
			"Hi! I'm ", g.name, " and my favorite number is ", g.favNumber)
	}
}
```

#### `example_test.go`

```go
func ExampleGopher_String() {
	fmt.Println(method.New("Gasitti", false, 42))
	fmt.Println(method.New("Gon", true, 42))
	// Output:
	// Hi! I'm Gasitti and my favorite number is 42
	// Let me get back to you after I'm done coding.
}
```

## Source File 📄

[The Source File](https://goplay.tools/snippet/tGiclWDwAIM)

## Test File 📝

[The Test File](https://goplay.tools/snippet/EKt0YgJpQxc)
