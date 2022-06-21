---
Lesson: 23
Title: "Goroutine"
Draft: false
---

Let's introduce one of the stars 🤩 of our show. The humble goroutine, started
with the keyword `go`. Don't let it's brevity fool you, it's a very powerful
weapon ⚔️  and the bread 🍞 and butter 🧈 of concurrency.

```go
func completeTasks() {
	go makeDinner()
	go doLaundry()
	go watchLatestEpisode()
}
```

The above mirrors the [tasks](/basics/concurrency) done in the introduction to
concurrency, but in Go code form.

Goroutines declare different tasks  🍽️ 🧺📺 they are going to finish to
completion **They do not have to execute in parallel❗** They open the
possibility to do so.

Though let's not get ahead of ourselves. What is a goroutine? Well it's
actually a play on words 😹 There is something known as a {{<
anchor-new-window "coroutine" "https://en.wikipedia.org/wiki/Coroutine" >}}
that appears in certain languages. I'll be 💯 % honest and say that wiki link
is kinda garbage for an explanation on what a coroutine is, so we can do
better.

We have coroutines (from now on going to be called goroutines) 🔄 and threads
🧵 they are similar because they both have their own work to
complete, their own stack, their own variables, and their own pointer, but the
difference is a thread 🧵 runs in parallel with other threads, and goroutines
are collaborative 🤝 and are therefore concurrent.

Let's say you have a CPU with 8 cores. You can have 8 threads 🧵 doing work at
the same time. Each thread can have **tons** of goroutines 🔄 **All 8 threads**
are running at the same time, but only one 1️⃣ goroutine is running on each of
those threads. Therefore you have 8 threads and 8 goroutines running.

If we zoom in 🔍 on 1 thread 🧵 we can see it has 1000 goroutines🔄 and if we
check only 1 goroutine is running but it gives up execution for one of the
other 999 goroutines 🔄 that needs it. By the way this is a simplification, so
don't take this to heart ❤️ We just want to capture the idea -- **Concurrency is
not parallelism and goroutines do not run in parallel, but instead
concurrently.**

## Setup

Let's make our directory `goroutine` and the files we want inside of that
directory `example_test.go` `goroutine.go`

```sh
mkdir goroutine
touch goroutine/example_test.go goroutine/goroutine.go
```

Now let's open up `goroutine.go` and for the very first line we'll add
```go
package goroutine
```
Next for `example_test.go` for the very first line we'll add
```go
package goroutine_test
```

## Can't stop, addicted to the... Async 😂

Now the first thing we need to understand about asynchronous programming, is
what _asynchronous_ means. And more importantly what it doesn't mean.
**Asynchronous does not mean concurrent because a callback 📱 can be given to a
function and the program can sit around ⌛ _waiting for the results_.**

```go
type Item struct {
	// other values here
	// ...
	updated bool
}
// Fetch immediately returns, then fetches the item and does some work on it.
// The caller of `Fetch("some string", func (i Item) { ... })` will **wait**
// around for the Item to change before moving forward. Not concurrent.
func Fetch(name string, waitOnThisCallback func(Item)) {
	go func() {
		// grab an Item somehow with `name`
		waitOnThisCallback(item)
	}()
}
```

Concurrent programs **do not wait** 🏃 When a goroutine is blocked it
relinquishes execution to another goroutine that needs to do its work 👈 This
is concurrency (grab ✊ more work when doing nothing), not asynchronicity (Make
a task execute on a different ~~thread~~ goroutine, for us gophers).

🤔 It still may be confusing so let's see why `async` is such a popular topic
in other languages, by looking at the benefits.

1. Avoid blocking ✋ UI and network threads 🧵
2. Reduce idle threads 🧵
3. Reclaim stack 📚 frames
4. Initiate concurrent 🔄 work

In Go our goroutines are 👑 King/Queen 👑 Let's see why none of the benefits
are beneficial in Go.

1. The runtime manages threads -- there is no blocking; no need to touch the
   kernel to switch goroutines
2. Goroutines are tiny 🤏 therefore idle goroutines are insignificant in
   comparison.
3. Each goroutine has its own stack _and_ variables exist as long as they have
   a reference, the stacks are managed for us and so is the space. Thanks Go!
   😘
4. This 👉 **concurrency** 👈 is _literally_ what a goroutine does

You know what asynchronicity actually does? Create {{< anchor-new-window
"caller-side ambiguity" "https://youtu.be/5zXAHh5tJqQ?t=421" >}} 😑 (Great talk
by Bryan C. Mills, definitely give it a watch!👍) and that's why it should be
avoided like the plague 🤢 At least in Go. Other languages have to use it
because they have no choice 🤷 they have nothing better. The benefits above
apply to their language. Go does have something better -- goroutines.

This is not to say Go doesn't have asynchronous programming. The word
asynchronous and concurrent are very similar. It's just good to know there's a
small difference. The devil's 😈 in the details, as they say. Though in Go we
often talk about concurrent work more than we do asynchronous work and that's
because...

Everything, and I do mean **everything** relies on goroutines in Go. The main
function of a Go program? `func main() { ... }` That's a goroutine. 🤯 Named
functions? Goroutine. 😲 Anonymous functions? Goroutine. 🙂 Closures?
Goroutine. 🙃 It's not surprising **_at all_** to see a Go application with
more than a 1000+ 👀 goroutines running concurrently.... I told you the support
is very good, didn't I? 😸

When spinning up another goroutine from the main goroutine. If the main
goroutine exits, it cuts the execution short for the other goroutines. The main
goroutine **does not wait** for the other goroutines to finish execution. We
can think of the main goroutine like the trunk of a tree 🌲 if we cut 🪓 down
the trunk 🪵 the whole thing falls.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### goroutine.go

```go
// WillNotWait shows us what it means to be asynchronous. In Go we spin up
// goroutines that have their own stack and their own goals to accomplish. It's
// very common to see 1000s of goroutines running in a go application!
func WillNotWait() {
	// NOTE(jay): This will be seen if we run `go test` for `ExampleWillNotWait`
	// it just won't be a part of the main goroutine's output because it exits.
	go toofast()
}

func toofast() { fmt.Println("We'll never see this... without waiting") }
```

#### example_test.go

```go
func ExampleWillNotWait() {
	goroutine.WillNotWait()
	// Output:
}
```

## In Sync

The reason we have independent goroutines is so they can do work and when
they're done, share their results. This requires some orchestration 🎼 🎻 🎺 Go
has first class support with channels `chan` but that's in a coming lesson
(coming soon!)

Concurrency is a building block 🧱 to parallelism. It's dangerous to think the
more goroutines we throw into an application the faster it will perform. That's
like putting gas ⛽ on a fire 🔥 hoping to put it out 😵

If we can do the work right now, don't put it in a goroutine. Our last
function? We _too_ quickly tried to add another goroutine and found our first
accidental lesson with concurrency. What we did was try to create another
goroutine to do extra work ❌ **_in parallel_** ❌, what actually happened was
the main goroutine still had work to do and didn't give up any time for that
other goroutine to start doing its work. The main goroutine finished and shut
⚰️ everything else down. So we learned **Start goroutines when you have
_concurrent_ work to do now.**

It's dead simple to add concurrency into the program -- `go` -- so when we see
there are tasks in the program that don't rely on each other or could be
scheduled in such a way across threads _then_ add concurrency.

___

Small aside -- a really fun thing to look into is {{< anchor-new-window
"Amdahl's Law" "https://en.wikipedia.org/wiki/Amdahl%27s_law" >}} 🧑‍⚖
which says in theory

> If 95% of the program can be parallelized, the theoretical maximum speedup
> with a CPU with 16 cores is 10 times the amount.

**Ten times** the amount❗❗ That's amazing❗ 😍 but that's with 95%
parallelization.... And 16 cores. For someone like me and for many programs
it'd be something closer to 8 cores and 50%, which is still 2 times as fast❗
In order to parallelize we _first_ need to have concurrency and to have
concurrency we need separate tasks and to separate those tasks we use
goroutines.

___

In the meantime, we can play around with and understand goroutines better by
artificially making our main goroutine take longer than any of our other
goroutines we make. We will make the main goroutine wait 🕐 🕑 🕒 which will
signal to the scheduler that _other_ goroutines can run right now as we wait on
the main goroutine (concurrency in action). We will then see the output from
the other goroutine we make! And with that we will have our very first success
with creating a concurrent function 🥳🎉🥳🎉🥳🎉

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### goroutine.go

```go
// SwitchToOther shows us how to artificially allow the goroutine we spawn to
// finish and exit, by slowing down the main goroutine. This is **not** how
// it's done in go. We use channels for true concurrency, but this is important
// to see before we introduce channels.
func SwitchToOther() {
	go toofast()
	// Make it wait 8 milliseconds to see separate goroutines output.
	time.Sleep(8 * time.Millisecond)
}
```

#### example_test.go

```go
func ExampleSwitchToOther() {
	goroutine.SwitchToOther()
	// Output:
	// We'll never see this... without waiting
}
```

## What's a goroutine accept? 🤔

The `go` keyword spins up a new goroutine, but what can we put after `go`? Well
any function. Whether it be a public 📣 or private 🔒named
[function](/basics/13-function), an anonymous function 🥸, a [closure
🦪](/basics/22-closure) or even [methods!](/basics/16-method) (All of these are
functions, so we could also just say `go` takes any function).

The reason being, a goroutine needs to execute a set of instructions. If we
tried `go 1` what instructions are we trying to do ⁉️ It wouldn't make any sense
to do that. So, when we have work that needs to be done, we do it in a separate
goroutine.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### goroutine.go

```go
func AcceptableTypes(val any) {
	// The `go` keyword needs a function and that is all, even if it is an
	// anonymous function, it can still be used in a goroutine
	go func(comingFrom string) {
		fmt.Println("coming from:", comingFrom)
	}("anonymous function goroutine")

	go func(v any) {
		switch t := val.(type) {
		case string:
			fmt.Printf("you chose %T: %s\n", t, t)
		case int:
			fmt.Printf("you chose %T: %d\n", t, t)
		case bool:
			fmt.Printf("you chose %T: %t\n", t, t)
		case float64:
			fmt.Printf("you chose %T: %f\n", t, t)
		case []struct{}:
			fmt.Printf("you chose %T: %#v\n", t, t)
		default:
			fmt.Printf("What is this? 👀 %T: %#v\n", t, t)
		}
	}(val)

	a := async("My cool new type 😎")
	go a.myMethod()

	go SwitchToOther()

	// NOTE(jay): We have to wait (`time.Sleep`), because the main goroutine will
	// shutdown other goroutines and exit immediately. Comment out 👇 to see
	time.Sleep(8 * time.Millisecond)
	fmt.Println("👋👋👋 Time to exit")
	fmt.Println()
}
```

#### example_test.go

```go
func ExampleAcceptableTypes() {
	// XXX(jay): This is going to fail!!!! Remember -- Goroutines **do not
	// execute in the order they are in a function** They execute asynchronously
	// (not line by line). We may get lucky and have this pass every so often,
	// but it's not guaranteed!
	goroutine.AcceptableTypes([]struct{}{{}, {}, {}})
	goroutine.AcceptableTypes(struct {
		name string
		age  int
	}{"Gary", 900})
	// Output:
	// We'll never see this... without waiting
	// My cool new type 😎 from a method: use in a new goroutine if you want!
	// coming from: anonymous function goroutine
	// you chose []struct {}: []struct {}{struct {}{}, struct {}{}, struct {}{}}
	// 👋👋👋 Time to exit
	//
	// coming from: anonymous function goroutine
	// We'll never see this... without waiting
	// My cool new type 😎 from a method: use in a new goroutine if you want!
	// What is this? 👀 struct { name string; age int }: struct { name string; age int }{name: "Gary", age:900}
	// 👋👋👋 Time to exit
}
```

## No Deterministic Order

We saw from the previous example that the test we made fails 9 times out of 10.
It's only when the stars align ⭐ ⭐ ⭐ and the goroutines finish in the order
they were created will it pass. Let's really drive 🚗 that point home with one
more example.

It's very important to understand these fundamentals of goroutines. In
understanding goroutines we understand asynchronicity. In understanding
goroutines we understand concurrency. Every tool 🛠️ has a purpose in your
toolkit 🧰 Concurrency is no different. Goroutines are no different. They are
not the end all be all. They are the bees knees 🐝 and the cat's meow 🐱 but
they won't solve your life. They just make concurrency trivially easy 😍 in Go.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### goroutine.go

```go
// NoOrder shows that asynchronous truly means there is no determined order.
// That the goroutines are not in sync and are not in serialized order. We
// spawn several goroutines and without sorting the outputs **this output will
// never be deterministic** this means the order is determined by which
// goroutine got time scheduled first.
func NoOrder() {
	for i := 0; i < 3; i++ {
		go processData(fmt.Sprintf("goroutine%d", i))
	}
	go processData("goroutine3")
	go processData("goroutine4")
	go processData("goroutine5")
	time.Sleep(3 * time.Millisecond)
}

func processData(comingFrom string) { fmt.Println("coming from:", comingFrom) }
```

#### example_test.go

Use `go test -run NoOrder` to just see the output for the `NoOrder` example.

```go
func ExampleNoOrder() {
	// XXX(jay): This is going to fail!!!! Remember -- Goroutines **do not
	// execute in the order they are in a function** They execute asynchronously
	// (not line by line). We may get lucky and have this pass every so often,
	// but it's not guaranteed!
	goroutine.NoOrder()
	// Output:
	// coming from: goroutine5
	// coming from: goroutine0
	// coming from: goroutine1
	// coming from: goroutine2
	// coming from: goroutine3
	// coming from: goroutine4
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png)

```go
package goroutine

import (
	"fmt"
	"time"
)

// WillNotWait shows us what it means to be asynchronous. In Go we spin up
// goroutines that have their own stack and their own goals to accomplish. It's
// very common to see 1000s of goroutines running in a go application!
func WillNotWait() {
	// NOTE(jay): This will be seen if we run `go test` for `ExampleWillNotWait`
	// it just won't be a part of the main goroutine's output because it exits.
	go toofast()
}

func toofast() { fmt.Println("We'll never see this... without waiting") }

// SwitchToOther shows us how to artificially allow the goroutine we spawn to
// finish and exit, by slowing down the main goroutine. This is **not** how
// it's done in go. We use channels for true concurrency, but this is important
// to see before we introduce channels.
func SwitchToOther() {
	go toofast()
	// Make it wait 8 milliseconds to see separate goroutines output.
	time.Sleep(8 * time.Millisecond)
}

type async string

func (a async) myMethod() {
	fmt.Println(a, "from a method: use in a new goroutine if you want!")
}

func AcceptableTypes(val any) {
	// The `go` keyword needs a function and that is all, even if it is an
	// anonymous function, it can still be used in a goroutine
	go func(comingFrom string) {
		fmt.Println("coming from:", comingFrom)
	}("anonymous function goroutine")

	go func(v any) {
		switch t := val.(type) {
		case string:
			fmt.Printf("you chose %T: %s\n", t, t)
		case int:
			fmt.Printf("you chose %T: %d\n", t, t)
		case bool:
			fmt.Printf("you chose %T: %t\n", t, t)
		case float64:
			fmt.Printf("you chose %T: %f\n", t, t)
		case []struct{}:
			fmt.Printf("you chose %T: %#v\n", t, t)
		default:
			fmt.Printf("What is this? 👀 %T: %#v\n", t, t)
		}
	}(val)

	a := async("My cool new type 😎")
	go a.myMethod()

	go SwitchToOther()

	// NOTE(jay): We have to wait (`time.Sleep`), because the main goroutine will
	// shutdown other goroutines and exit immediately. Comment out 👇 to see
	time.Sleep(8 * time.Millisecond)
	fmt.Println("👋👋👋 Time to exit")
	fmt.Println()
}

// NoOrder shows that asynchronous truly means there is no determined order.
// That the goroutines are not in sync and are not in serialized order. We
// spawn several goroutines and without sorting the outputs **this output will
// never be deterministic** this means the order is determined by which
// goroutine got time scheduled first.
func NoOrder() {
	for i := 0; i < 3; i++ {
		go processData(fmt.Sprintf("goroutine%d", i))
	}
	go processData("goroutine3")
	go processData("goroutine4")
	go processData("goroutine5")
	time.Sleep(3 * time.Millisecond)
}

func processData(routine string) { fmt.Println("coming from:", routine) }
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif)

```go
package goroutine_test

import "basics/goroutine"

func ExampleWillNotWait() {
	goroutine.WillNotWait()
	// Output:
}

func ExampleSwitchToOther() {
	goroutine.SwitchToOther()
	// Output:
	// We'll never see this... without waiting
}

func ExampleAcceptableTypes() {
	// XXX(jay): This is going to fail!!!! Remember -- Goroutines **do not
	// execute in the order they are in a function** They execute asynchronously
	// (not line by line). We may get lucky and have this pass every so often,
	// but it's not guaranteed!
	goroutine.AcceptableTypes([]struct{}{{}, {}, {}})
	goroutine.AcceptableTypes(struct {
		name string
		age  int
	}{"Gary", 900})
	// Output:
	// We'll never see this... without waiting
	// My cool new type 😎 from a method: use in a new goroutine if you want!
	// coming from: anonymous function goroutine
	// you chose []struct {}: []struct {}{struct {}{}, struct {}{}, struct {}{}}
	// 👋👋👋 Time to exit
	//
	// coming from: anonymous function goroutine
	// We'll never see this... without waiting
	// My cool new type 😎 from a method: use in a new goroutine if you want!
	// What is this? 👀 struct { name string; age int }: struct { name string; age int }{name: "Gary", age:900}
	// 👋👋👋 Time to exit
}

func ExampleNoOrder() {
	// XXX(jay): This is going to fail!!!! Remember -- Goroutines **do not
	// execute in the order they are in a function** They execute asynchronously
	// (not line by line). We may get lucky and have this pass every so often,
	// but it's not guaranteed!
	goroutine.NoOrder()
	// Output:
	// coming from: goroutine5
	// coming from: goroutine0
	// coming from: goroutine1
	// coming from: goroutine2
	// coming from: goroutine3
	// coming from: goroutine4
}
```
