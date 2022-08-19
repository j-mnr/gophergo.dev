---
Lesson: 21
Title: "Panic & Recover"
Draft: false
---


{{< figure src="/maria-letta/goroutines/5.svg" alt="Goroutines 5"
attr=`[goroutines-5](https://github.com/MariaLetta/free-gophers-pack/blob/master/goroutines/svg/5.svg)
[Maria Letta](https://instagram.com/maria_letta_art)-
[License](https://github.com/MariaLetta/free-gophers-pack/blob/master/LICENSE)`
width=1080px >}}

IT'S _NOW_ TIME TO `panic` 😱 AHHHHHHHHH! -- **But wait**, before we completely
lose our 💩Let's take a deep  breath in 🧘 to `recover` and exhale 🌬️

So if the name didn't give it away, a `panic` is about as bad as it gets in Go.
This is the absolute worst situation to find yourself in because it means your
program has done something unspeakably horrible 😈 Which is usually a `SIGSEGV`
aka "Seg Fault" aka "Segmentation Violation" and that means it tried to access
memory 🥸 that didn't belong to it 👺 Just like in real life, if you take
something that doesn't belong to you, 👹 you're going to have to pay the
consequences. 💀 Though this isn't the only reason for a `panic` and we can
call `panic` ourselves.

{{% setup-instructions lessonname="panic_recover" %}}

## Panic and Defer

It is incredibly easy to cause a `panic` you just call the function... _Ta-da_!
😂 So that begs the question, why would you `panic`? It is usually done in the
`main` function of a program.

Let's say that you have a car 🚗 that you need to drive to work everyday,
because your work is an hour drive and there are no buses 🚌 or metros 🚇 to
take you to work. If you lose the key 🔑 can you make it to work? 🤔 The answer
is no. Therefore, all the work you were going to do that day? 🌅 _Not_ going to
happen. So cancel the whole thing!

Let's say our application has access to a database 🏛️ and in order to use the
database it has credentials, a username and password, like a key 🔑 What if the
application can't find those credentials? Or the credentials are wrong? Is
there any reason to start the application? No! 🙅 All the work it was going to
get done with the database 🏛️ will never happen because it can't access it.

In a situation like that 👆 it's common to `panic`. When there is absolutely no
moving forward from an error without human 💁 intervention we `panic`.

Sometimes a `panic` will happen unexpectedly. Let's say on your way to work and
your wheel 🛞 blows out. You can  do some cleanup by calling 🤙 your work and
let them know you'll be late, calling someone to come tow 🪝 your car 🚗 to a
shop

In our app we are connecting to the database 🏛️  and half way through, the
connection 🔌 drops! Well you still need to cleanup the connection 🧹 and
remove the garbage of the response you _were_ getting.

This is where `defer` comes in. Even if a function calls `panic( ... )` whether
you made it do it or not, a `defer` statement will **always** be ran.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `panic_recover.go`

```go
// AfterDefer shows that even if a panic occurs in a function a `defer`
// statement will **always** execute. This is to make sure system resources are
// cleaned up and why we can `recover` in the first place.
func AfterDefer() {
	defer fmt.Println("defer: Still print statement even with a panic")
	panic("💣 TIME TO BLOW UP!!!")
}
```

#### `example_test.go`

Some things just can't have good output. To see this in action we need to go to
our `cmd/main.go` made _all_ the way back in [lesson
0](/basics/00-hello-gophers/#func-main) You'll need to import our new package
and run it. I did it like

```go
import (
  pnr "basics/panic-recover"
)
```

And then put it in `main()` to see we actually get `defer` output!

We'll keep this example here just in case we ever want to see what we expect.

```go
func ExampleAfterDefer() {
	// NOTE(jay): No way to test output of a panic. That's **not** something you can
	// make an example out of, but we can at least have one to look at by
	// uncommenting 👇 the line below.
	// pnr.AfterDefer()
	// NOTE(jay): Would be Output:
	// defer: Still print statement even with a panic
	// panic: 💣 TIME TO BLOW UP!!!
}
```

## Panic And Recover

Now -- you're ready! This time you have two pairs of keys 🔑 and you've learned
how to change a tire 🛞 on a car 🚗 And it just so happened that day, your keys
were hidden by your cat 😹 _and_ you walk out to your car to see a flat tire!
Well no worries, you can `recover` because you were prepared for the worst to
happen.

This time, in the app, instead of having one database 🏛️  that we connect to we
have two and if neither of them work, we'll have an in-memory database that
will record everything, write it to a local file, and try to connect 🔌 to the
database every 5 minutes to dump the new data to it. These are suboptimal
solutions, but if it's doing work or not, we get work done 💪😤

It's important to recognize that **without `defer` you cannot `recover`** As we
looked at above, a `defer` statement will run even if there is a `panic`.
`recover` is just a function. It will execute on the line it's asked to run and
check that no `panic` has occurred. We don't want to check for a `panic`
_before_ a `panic` occurs, we want to check _after_. And how do we make sure
that our function is the last thing ran before the `return` statement? A
[`defer`](/basics/20-defer/#wheres-it-start--wheres-it-end-)😏

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `panic_recover.go`

```go
// KeepCalm shows how we can `recover` from a `panic` by using a `defer`
// statement that calls `recover()`. You **must** put recover in a `defer`
// statement or else it won't work.
func KeepCalm() {
	defer recuperate()
	// NOTE(jay): This will not stop the panic
	// recover()
	panic("😱 AWWW 💩WE'RE GOING DOWN!")
}

func recuperate() {
	if err := recover(); err != nil {
		fmt.Println("recovered from:", err)
	}
}
```

#### `example_test.go`

```go
func ExampleKeepCalm() {
	pnr.KeepCalm()
	// Output:
	// recovered from: 😱 AWWW 💩WE'RE GOING DOWN!
}
```

## I Didn't Do That!

More often then causing a `panic`, we'll encounter a `panic` 👀 through means
we hadn't expected or hoped for. 😩 This usually presents itself with the ever
popular _nil pointer_ or _index out of bounds_ runtime errors. 🙃

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `panic_recover.go`

```go
type myStruct struct{ cantAccess string }

func (s *myStruct) CausePanic() string { return s.cantAccess }

func NilPointer() {
	s := new(myStruct)
	s = nil // NOTE(jay): Obviously dangerous, but it happens in mysterious ways.
	fmt.Println(s.CausePanic())
}

func NewMap() {
	m := new(map[string]string)
	(*m)["nil map"] = "causes panic!"
	// We actually want:
	// ma := make(map[string]string)
	// ma["not nil"] = "works"
}

func IndexOut() {
	daBomb := []string{"set", "us", "up", "da bomb."}
	fmt.Println(daBomb[len(daBomb)])
	// We actually want:
	// fmt.Println(daBomb[len(daBomb)-1])
}
```

## Understanding Panic Stack Trace

Let's cause a panic with `pnr.IndexOut()` to get an index out of range.

```text
panic: runtime error: index out of range [4] with length 4

goroutine 1 [running]:
main.main()
        /home/jay/basics/cmd/main.go:7 +0x1b
exit status 2
```

This is a rather tame 🧸 `panic`, let's go through each line regardless.

---
This is what the actual `panic` had to say about why it halted the execution of
the entire program.

```text
panic: runtime error: index out of range [4] with length 4
```
---
There can be more than one `goroutine` running. We will cover goroutines next
lesson, just know this is the first one (main goroutine) and they are
lightweight threads of execution.

```text
goroutine 1 [running]:
```
---

This shows the package and function that had a `panic`

```text
main.main()
```
---

This shows the absolute path to the file that was a part of the stack trace
`/home/jay/basics/cmd/main.go` **and** what line it happened on `:7` **and**
where this function was on the stack `+0x1b` in hexadecimal. For some knowledge
the stack fills up like a bucket 🪣 Meaning `0x00` is the bottom and `0x1b` is
_very_ close to the bottom. It's `27` in decimal.

```text
        /home/jay/basics/cmd/main.go:7 +0x1b
```
---

Anything other than an exit status of 0 means something went wrong.

```text
exit status 2
```
---

That wasn't too bad right? 😀 Well I'd be lying if I said that's what you'll
normally get when you see a panic 😬 We can cause a _real_ looking panic with
our `example_test.go`

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `example_test.go`

```go
func ExampleNilPointer() {
	// NOTE(jay): No way to test output of a panic. That's **not** something you can
	// make an example out of, but we can at least have one to look at by
	// uncommenting 👇 the line below.
	// pnr.NilPointer()
	// NOTE(jay): Would be Output:
	// panic: runtime error: invalid memory address or nil pointer dereference [recovered]
	//         panic: runtime error: invalid memory address or nil pointer dereference
	// [signal SIGSEGV: segmentation violation code=0x1 addr=0x0 pc=0x4ef6f6]
	//
	// goroutine 1 [running]:
	// testing.(*InternalExample).processRunResult(0xc000057c68, {0x0, 0x0}, 0xc00007eb60?, 0x0, {0x5027e0, 0x5ef9f0})
	//         /usr/lib/go/src/testing/example.go:91 +0x4e5
	// testing.runExample.func2()
	//         /usr/lib/go/src/testing/run_example.go:59 +0x11c
	// panic({0x5027e0, 0x5ef9f0})
	//         /usr/lib/go/src/runtime/panic.go:838 +0x207
	// basics/panic-recover.(*myStruct).CausePanic(...)
	//         /home/jay/basics/panic-recover/panic_recover.go:25
	// basics/panic-recover.NilPointer()
	//         /home/jay/basics/panic-recover/panic_recover.go:30 +0x16
	// basics/panic-recover_test.ExampleNilPointer()
	//         /home/jay/basics/panic-recover/example_test.go:18 +0x17
	// testing.runExample({{0x5201b1, 0x13}, 0x527dd0, {0x0, 0x0}, 0x0})
	//         /usr/lib/go/src/testing/run_example.go:63 +0x28d
	// testing.runExamples(0xc000057e58, {0x5f4360?, 0x3, 0x0?})
	//         /usr/lib/go/src/testing/example.go:44 +0x186
	// testing.(*M).Run(0xc00010c140)
	//         /usr/lib/go/src/testing/testing.go:1721 +0x689
	// main.main()
	//         _testmain.go:53 +0x1aa
	// exit status 2
}
```

😲 Let's go through _this_ stack trace! Usually when you see something like the
above you should look for what you've done. Meaning what functions and files
and directories look familiar to you? That's probably where you should start
trying to fix your `panic` message.

So I see `basics/panic-recover.(*myStruct).CausePanic(...)` ❗ Hey That's
`myStruct` I know that and `/home/jay/basics/panic-recover/panic_recover.go:25`
that's my `go` file! I'd look there on line 25 to see what the problem is, but
we're not here just to see that. Let's go through the whole stack trace.

---
```text
panic: runtime error: invalid memory address or nil pointer dereference [recovered]
        panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=0x1 addr=0x0 pc=0x4ef6f6]

goroutine 1 [running]:
```

This is telling us that there was a nil pointer dereference, `nil.someValue` 👈
We did this and that caused a segmentation violation `SIGSEGV` also known as a
segfault with code=`1` address=`0` and program counter=`5175030`

A program counter is a register in a computer processor that contains the
address of the instruction being executed at the current time. We can see that
whether the pc is in hexadecimal or decimal it doesn't make sense to us humans.
It doesn't need to. It's just an address. You can see 1234 Square Rd 🏠 and
understand it's an address, but does it mean anything to you? Not at all.

The information we can really gather from the above text is _what happened_?

---
```text
testing.(*InternalExample).processRunResult(0xc000057c68, {0x0, 0x0}, 0xc00007eb60?, 0x0, {0x5027e0, 0x5ef9f0})
        /usr/lib/go/src/testing/example.go:91 +0x4e5
testing.runExample.func2()
        /usr/lib/go/src/testing/run_example.go:59 +0x11c
```

The [InternalExample
struct](https://cs.opensource.google/go/go/+/master:src/testing/example.go;l=91;drc=7791e934c882fd103357448aee0fd577b20013ce;bpv=0)
panicked at line 91 high up on the stack trace at `0x4e5` or `1253` All of the
hexadecimal values in between `processRunResult(0xc000057c68, {0x0, 0x0},
0xc00007eb60?, 0x0, {0x5027e0, 0x5ef9f0})` are pointers too all of the
arguments that the method takes and it happened in `example.go` which is in the
standard library `testing` package.

And an [anonymous
function](https://cs.opensource.google/go/go/+/master:src/testing/run_example.go;l=59;bpv=0)
on line 59 in the `run_example.go` file panicked because the above
`InternalExample` panicked.


---
```text
panic({0x5027e0, 0x5ef9f0})
        /usr/lib/go/src/runtime/panic.go:838 +0x207
basics/panic-recover.(*myStruct).CausePanic(...)
        /home/jay/basics/panic-recover/panic_recover.go:25
basics/panic-recover.NilPointer()
        /home/jay/basics/panic-recover/panic_recover.go:30 +0x16
basics/panic-recover_test.ExampleNilPointer()
        /home/jay/basics/panic-recover/example_test.go:18 +0x17
```

The place where [panic is
defined](https://cs.opensource.google/go/go/+/master:src/runtime/panic.go;l=838;bpv=0)
in the runtime package in the file `panic.go` at line 838 and fairly high on
the stack trace at `0x207` or `519` in decimal.

Next we see all of our code that has files, directories and packages that we
made! This is where we should check. We have control over all of these values.
Everything we've seen up until now has been done for us by a different library.
This is what we control, however. So when looking at a stack trace look for
what _**you**_ did and what _**you**_ care about. We can see how close to the
bottom of the stack both of the functions are at `0x16` and `0x17` or `22` and
`23` respectively.

---
```text
testing.runExample({{0x5201b1, 0x13}, 0x527dd0, {0x0, 0x0}, 0x0})
        /usr/lib/go/src/testing/run_example.go:63 +0x28d
testing.runExamples(0xc000057e58, {0x5f4360?, 0x3, 0x0?})
        /usr/lib/go/src/testing/example.go:44 +0x186
testing.(*M).Run(0xc00010c140)
        /usr/lib/go/src/testing/testing.go:1721 +0x689
main.main()
        _testmain.go:53 +0x1aa
exit status 2
```

We see a [runExample function
call](https://cs.opensource.google/go/go/+/master:src/testing/run_example.go;l=63;bpv=0)
that has some pointers to the passed in arguments that happened in the
`testing` package in the `run_example.go` file on line 63. Much higher than our
function calls at `0x28d` or `653` in decimal.

Another function call to
[runExamples](https://cs.opensource.google/go/go/+/master:src/testing/example.go;l=44;bpv=0)
a different function from the above in the same package in `example.go` on line
44 and closer to the bottom of the stack at `0x186` or `390` in decimal.

Finally we see our last actual call of a library using the [testing M struct's
Run
method](https://cs.opensource.google/go/go/+/master:src/testing/testing.go;l=1728;bpv=0)
with one pointer to a `code` argument which is an `int`. This happens on line
1721!!!! Of the file at `0x689`!!! Or `1673` in decimal, very high up on the
stack.

And every Go program must have a `main` function. It is made for us by the
builtin `testing` package. Thanks Go! 😁👍

## Source File 📄

[The Source File](https://goplay.tools/snippet/pb9Fzgrk1cV)

## Test File 📝

[The Test File](https://goplay.tools/snippet/Hoat9FLMZ72)
