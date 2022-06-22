---
Lesson: 20
Title: "Defer"
Draft: false
---

Sometimes we have to postpone things, like that trip to Hawaii 😎 🏖️ or buying
the tickets to that big game 🏟️ or cleaning up the house 🧹 or waiting for a
💲**Big Sale**💲 on our favorite items. This is what's known as deferring. You
can also defer to someone else for their advice, like if someone asks 🗨️ "Why's
the sky blue?" -- I would say 💬 "Uhhh 😅, I defer to my high school science
teacher."

The same thing can be done in Go. We can `defer` function calls to be executed.
Why is this useful? 🤔 There are two main driving reasons I can see 👁️ as to
why we use `defer`. Readability and control flow. In Go it's idiomatic (common)
to see code that tends towards the {{< anchor-new-window "return early pattern"
"https://medium.com/swlh/return-early-pattern-3d18a41bba8" >}}... _very_
common. It's almost _wrong_ to nest clauses in Go and reading that code _I'd_
raise an eyebrow of concern 🤨

The thing is, if we aren't afraid to return early in Go, we **must** have a way
of making sure that the resources that our functions grab can be released **at
every single point of return** ❗❗ What's that in the sky? It's a bird 🐦 It's
a plane ✈️ It's `defer` 🦸

The functional part of `defer` comes with it's ability to run a function **no
matter where we exit** from the function. The other added benefit is the code
that will get a resource can let go of it on the next line. This makes
readability and _glanceability_  much higher and makes catching 🕸️🪰 bugs much
easier because you're not managing _where_ you have to let that resource go.

## Setup

Let's make our directory `defers` and the files we want inside of that directory
`example_test.go` `defers.go`

```sh
mkdir defers
touch defers/example_test.go defers/defers.go
```

Now let's open up `defers.go` and for the very first line we'll add
```go
package defers
```
Next for `example_test.go` for the very first line we'll add
```go
package defers_test
```

## Where's it Start 🙂 Where's it End 🙃

A `defer` statement can be _anywhere_ in a function and it will **always** be
the last statement ran before the return. It is typical to see `defer` after
some resource has been acquired so that it can be released at the end of the
functions lifetime 🌈

From the late great Ricky Bobby 🏎️

> If you ain't first you're last.

`defer` takes that to heart 💜 and is always the last line to be executed
before the return. It's important to note that it's _before_ the `return`
statement is executed.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### defers.go

```go
// RunAtEnd shows that using the `defer` keyword will make that function
// run at the very end of our function.
func RunAtEnd() {
	defer fmt.Println("defer: this is the first line, but the last output.")
	fmt.Println("This is the second line, but first to be printed.")
	fmt.Println("A couple of more lines for good measure.")
	fmt.Println("Never hurt anyone.")
}
```

#### example_test.go

```go
func ExampleRunAtEnd() {
	defers.RunAtEnd()
	// Output:
	// This is the second line, but first to be printed.
	// A couple of more lines for good measure.
	// Never hurt anyone.
	// defer: this is the first line, but the last output.
}
```

## Last In, First Out 📚 (LIFO)

What happens if we have multiple `defer` statements? 🤔 If you're familiar with
a `stack` it works the same. If you're not -- `defer` statements work like a
stack of books 📚 If you want to get to the first `defer` (first book📘) you
have to remove the top two 📕📗 and that would be the same as executing them.
So to get to `defer` 1 we have to execute `defer` 3 and then `defer` 2 -- The
same as the books we remove book 3📕 and then book 2📗 to get book 1 📘

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### defers.go

```go
// LIFO shows that if using multiple defers they work in a Last In First
// Out (LIFO) fashion.
func LIFO() {
	defer fmt.Println("First In. Third Out")
	fmt.Println("Random line 1")
	fmt.Println("Random line 2")
	defer fmt.Println("Second In. Second Out")
	fmt.Println("Random line 3")
	defer fmt.Println("Third In. First Out")
}
```

#### example_test.go

```go
func ExampleLIFO() {
	defers.LIFO()
	// Output:
	// Random line 1
	// Random line 2
	// Random line 3
	// Third In. First Out
	// Second In. Second Out
	// First In. Third Out
}
```

## Argument's Evaluated

I'll be honest ✋ this doesn't seem intuitive and an _argument_ 😹 can be made
for either side. It's little endian versus big endian all over again 😱 The
thing is we have an answer in this case. Here's the problem, we have a `defer`
statement with a small function, `f`, we want ran at the end of our big
function , `F`, **but** we pass in an argument to that small function `f(arg)`.
When is that small function `f(arg)` executed with that argument? At the end of
the big function `F` or where you declare it...? Let's find out 😏

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### defers.go

```go
// ArgumentsEvaluated shows the arguments passed to a `defer`'s function
// are immediately evaluated and not evaluated at the end of the function's
// lifetime
func ArgumentsEvaluated() {
	whatWillIBe := 42
	defer fmt.Println("Number in defer:", whatWillIBe)
	whatWillIBe = 9001
	fmt.Println("Number at end of function:", whatWillIBe)
}
```

#### example_test.go

```go
func ExampleArgumentsEvaluated() {
	defers.ArgumentsEvaluated()
	// Output:
	// Number at end of function: 9001
	// Number in defer: 42
}
```

## Sneaky Named Returns

Another sneaky thing that can happen with `defer` is which values you pass to
it may give you what feels like unexpected behavior. This is a good time to
**not** dig too deep into it and just have knowledge of it... Else you'll find
your head spinning 😵‍💫

Though if you're curious, this has to do with what **scope** `defer` works on.
Many languages work around what's known as the block scope, but `defer` works
on the functional scope. A function can have named returns as we learned in the
lesson on [functions](/basics/13-function/#returns-with-names) which puts that
named parameter on the function's scope, not the inside block's scope,
therefore it  _won't_ be able to push that to function level... Confusing?
Yeah, I told you 😵

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### defers.go

```go
// NamedReturn shows that before we return a named value, if we change it
// in a deferred function, it will change it before returning the value.
// Compare with Return.
func NamedReturn() (gi string) {
	gi = "Golden Idol"
	defer indianaJones(&gi)
	return gi
}

// Return shows that depending on the scope, in the function or out of the
// function, the return value will change. Compare with NamedReturn.
func Return() string {
	gi := "Golden Idol"
	defer indianaJones(&gi)
	return gi
}

func indianaJones(s *string) { *s = "Bag of sand" }
```

#### example_test.go

```go
func ExampleNamedReturn() {
	fmt.Println(defers.NamedReturn())
	// Output: Bag of sand
}

func ExampleReturn() {
	fmt.Println(defers.Return())
	// Output: Golden Idol
}
```

## Panic! At the 🪩 Function...😹

Here is where we see our first very necessary use of `defer`. In Go, there are
no exceptions like you might find in other languages. Situations that produce
errors are handled differently in Go as we found out [in the previous
lesson](/basics/19-error). However, there is a difference between a bad
situation 😮 and a catastrophic 🌊 🌋 🌪️ 🌩️situation.

This distinction in Go is made with `panic`, but we'll get into that more in
the [next lesson](/basics/21-panic-recover). Just know in order to recover from
such a situation, we need to `recover` before the function ends, but in order
to `recover` we must `defer`.

After this point we've covered all we need to know about `defer` so feel free
to head over to the [next lesson](/basics/21-panic-recover) But if you come
from a language that doesn't have a `defer` or if you're new to it all, the
next topic has lots of examples of where we use `defer` in Go.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### defers.go

```go
// RecoverPanic shows how to recover from a panic by using a `defer`
// statement and the `recover` keyword that will be covered in the next lesson.
func RecoverPanic(calmdown bool) {
	if calmdown {
		defer recuperate()
	}
	panics()
	fmt.Println("Looks like we recovered in time.")
}

func panics() {
	panic("WE'RE ALL GOING DOWN, THIS IS THE END!!!")
}

func recuperate() {
	if r := recover(); r != nil {
		fmt.Println("Recovered from:", r)
	}
}
```

#### example_test.go

```go
func ExampleRecoverPanic() {
	// NOTE(jay): Uncomment this if you're a brave soul 🫣
	// defers.RecoverPanic(false)
	defers.RecoverPanic(true)
	// Output: Recovered from: WE'RE ALL GOING DOWN, THIS IS THE END!!!
}
```

## Out in the Wild 🐯 (Useful places to use `defer`)

I admit that `defer` may be completely new to the uninitiated, veterans, and
experts alike, depending on which language you come from, if any. So I've put
together a small example set of where `defer` is absolutely necessary to call.

⚠️⚠️ **WARNING** ⚠️⚠️ None of these examples will be thoroughly explained as I do
the rest of our code. Each example deserves it's own lesson entirely. This is
meant more as a sample set to open your mind 🤯 to the possibilities of
`defer`.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### Closing A File

##### defers.go

```go
// FileClose shows that we **always close** file descriptors after we open
// them.
func FileClose() {
	f, err := os.OpenFile("example.txt", os.O_RDWR|os.O_CREATE, 0744)
	if err != nil {
		fmt.Println(err)
	}
	defer closeFile(f)

	_, err = f.WriteString("👋 🌏")
	if err != nil {
		fmt.Println(err)
	}
}

func closeFile(f *os.File) {
	fmt.Println("closing")
	if err := f.Close(); err != nil {
		fmt.Fprintf(os.Stderr, "error: %v\n", err)
		os.Exit(1)
	}
}
```

##### example_test.go

```go
func ExampleFileClose() {
	defers.FileClose()
	f, err := os.Open("example.txt")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Print("Reading contents of example.txt: ")
	io.Copy(os.Stdout, f)
	// Output:
	// closing
	// Reading contents of example.txt: 👋 🌏
}
```

#### Removing Temporary Files

##### defers.go

```go
// TempFileRemoveClose shows that with a temp file (and temp directories)
// we should clean up after ourselves by removing the file and closing the file
// descriptor.... **Always remove and close** temp file descriptors.
func TempFileRemoveClose() {
	f, err := os.CreateTemp(".", "tempfile")
	if err != nil {
		fmt.Println(err)
	}
	defer removeFile(f)
	defer closeFile(f)
}

func removeFile(f *os.File) {
	fmt.Println("removing")
	if err := os.Remove(f.Name()); err != nil {
		fmt.Fprintf(os.Stderr, "error: %v\n", err)
		os.Exit(1)
	}
}
```

##### example_test.go

```go
func ExampleTempFileRemoveClose() {
	defers.TempFileRemoveClose()
	// Output:
	// closing
	// removing
}
```

#### Closing HTTP Response Bodies

##### defers.go

```go
// HTTPBodyClose shows that we **always close** our `*http.Response` body
// after it successfully completes.
func HTTPBodyClose() {
	res, err := http.Get("https://gophergo.dev")
	if err != nil {
		fmt.Fprintf(os.Stderr, "error: %v\n", err)
		os.Exit(1)
	}
	defer res.Body.Close()

	buf := make([]byte, 4096)
	io.ReadFull(res.Body, buf)
	fmt.Printf("Does the first 4KiB of data have Gopher Go in it? %t\n",
		bytes.Contains(buf, []byte("Gopher Go")))
}
```

##### example_test.go

```go
func ExampleHTTPBodyClose() {
	defers.HTTPBodyClose()
	// Output: Does the first 4KiB of data have Gopher Go in it? true
}
```

#### Cancel Context

##### defers.go

```go
// CancelContext shows a contrived way of canceling a context. Though it
// is a toy example, it is still very important that you **always cancel** your
// context after you're done using it.
func CancelContext(d time.Duration) {
	ctx := context.Background()
	ctx, cancel := context.WithTimeout(ctx, 10*time.Millisecond)
	defer cancel()
	t := time.NewTimer(d)
	select {
	case <-ctx.Done():
		fmt.Println("You're too slow!!!")
	case <-t.C:
		fmt.Println("Don't forget to cancel!")
	}
}
```

##### example_test.go

```go
func ExampleCancelContext() {
	defers.CancelContext(300 * time.Millisecond)
	defers.CancelContext(5 * time.Millisecond)
	// Output:
	// You're too slow!!!
	// Don't forget to cancel!
}
```

#### Mutex Locks

##### defers.go

```go
// account acts as a bank account with a balance
type account struct {
	balance int64
	sync.Mutex
}

// NewAccount ...
func NewAccount(balance int64) *account { return &account{balance: balance} }

// Balance gets the amount of money stored in cents. It locks the account from
// other transactions, such as a deposit, so the statement is accurate.
func (a *account) Balance() int64 {
	a.Lock()
	defer a.Unlock()

	return a.balance
}

// Deposit places money into the account. It locks the account so that other
// transactions, such as Withdrawal, cannot effect the balance while it's being
// updated.
func (a *account) Deposit(amount int64) (int64, error) {
	if amount < 0 {
		return -1, errors.New("Cannot deposit a negative amount.")
	}
	a.Lock()
	defer a.Unlock()

	a.balance += amount
	return a.balance, nil
}
```

##### example_test.go

```go
func ExampleNewAccount() {
	a := defers.NewAccount(1000)
	a.Deposit(100)
	fmt.Println("Balance is:", a.Balance())
	// Output: Balance is: 1100
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png "gopher-victorious by Egon Elbre")

```go
package defers

import (
	"bytes"
	"context"
	"errors"
	"fmt"
	"io"
	"net/http"
	"os"
	"sync"
	"time"
)

// RunAtEnd shows that using the `defer` keyword will make that function
// run at the very end of our function.
func RunAtEnd() {
	defer fmt.Println("defer: this is the first line, but the last output.")
	fmt.Println("This is the second line, but first to be printed.")
	fmt.Println("A couple of more lines for good measure.")
	fmt.Println("Never hurt anyone.")
}

// LIFO shows that if using multiple defers they work in a Last In First
// Out (LIFO) fashion.
func LIFO() {
	defer fmt.Println("First In. Third Out")
	fmt.Println("Random line 1")
	fmt.Println("Random line 2")
	defer fmt.Println("Second In. Second Out")
	fmt.Println("Random line 3")
	defer fmt.Println("Third In. First Out")
}

// ArgumentsEvaluated shows the arguments passed to a `defer`'s function
// are immediately evaluated and not evaluated at the end of the function's
// lifetime
func ArgumentsEvaluated() {
	whatWillIBe := 42
	defer fmt.Println("Number in defer:", whatWillIBe)
	whatWillIBe = 9001
	fmt.Println("Number at end of function:", whatWillIBe)
}

// NamedReturn shows that before we return a named value, if we change it
// in a deferred function, it will change it before returning the value.
// Compare with Return.
func NamedReturn() (gi string) {
	gi = "Golden Idol"
	defer indianaJones(&gi)
	return gi
}

// Return shows that depending on the scope, in the function or out of the
// function, the return value will change. Compare with NamedReturn.
func Return() string {
	gi := "Golden Idol"
	defer indianaJones(&gi)
	return gi
}

func indianaJones(s *string) { *s = "Bag of sand" }

// RecoverPanic shows how to recover from a panic by using a `defer`
// statement and the `recover` keyword that will be covered in the next lesson.
func RecoverPanic(calmdown bool) {
	if calmdown {
		defer recuperate()
	}
	panics()
	fmt.Println("Looks like we recovered in time.")
}

func panics() {
	panic("WE'RE ALL GOING DOWN, THIS IS THE END!!!")
}

func recuperate() {
	if r := recover(); r != nil {
		fmt.Println("Recovered from:", r)
	}
}

// FileClose shows that we **always close** file descriptors after we open
// them.
func FileClose() {
	f, err := os.OpenFile("example.txt", os.O_RDWR|os.O_CREATE, 0744)
	if err != nil {
		fmt.Println(err)
	}
	defer closeFile(f)

	_, err = f.WriteString("👋 🌏")
	if err != nil {
		fmt.Println(err)
	}
}

func closeFile(f *os.File) {
	fmt.Println("closing")
	if err := f.Close(); err != nil {
		fmt.Fprintf(os.Stderr, "error: %v\n", err)
		os.Exit(1)
	}
}

// TempFileRemoveClose shows that with a temp file (and temp directories)
// we should clean up after ourselves by removing the file and closing the file
// descriptor.... **Always remove and close** temp file descriptors.
func TempFileRemoveClose() {
	f, err := os.CreateTemp(".", "tempfile")
	if err != nil {
		fmt.Println(err)
	}
	defer removeFile(f)
	defer closeFile(f)
}

func removeFile(f *os.File) {
	fmt.Println("removing")
	if err := os.Remove(f.Name()); err != nil {
		fmt.Fprintf(os.Stderr, "error: %v\n", err)
		os.Exit(1)
	}
}

// HTTPBodyClose shows that we **always close** our `*http.Response` body
// after it successfully completes.
func HTTPBodyClose() {
	res, err := http.Get("https://gophergo.dev")
	if err != nil {
		fmt.Fprintf(os.Stderr, "error: %v\n", err)
		os.Exit(1)
	}
	defer res.Body.Close()

	buf := make([]byte, 4096)
	io.ReadFull(res.Body, buf)
	fmt.Printf("Does the first 4KiB of data have Gopher Go in it? %t\n",
		bytes.Contains(buf, []byte("Gopher Go")))
}

// CancelContext shows a contrived way of canceling a context. Though it
// is a toy example, it is still very important that you **always cancel** your
// context after you're done using it.
func CancelContext(d time.Duration) {
	ctx := context.Background()
	ctx, cancel := context.WithTimeout(ctx, 10*time.Millisecond)
	defer cancel()
	t := time.NewTimer(d)
	select {
	case <-ctx.Done():
		fmt.Println("You're too slow!!!")
	case <-t.C:
		fmt.Println("Don't forget to cancel!")
	}
}

// account acts as a bank account with a balance
type account struct {
	balance int64
	sync.Mutex
}

// NewAccount ...
func NewAccount(balance int64) *account { return &account{balance: balance} }

// Balance gets the amount of money stored in cents. It locks the account from
// other transactions, such as a deposit, so the statement is accurate.
func (a *account) Balance() int64 {
	a.Lock()
	defer a.Unlock()

	return a.balance
}

// Deposit places money into the account. It locks the account so that other
// transactions, such as Withdrawal, cannot effect the balance while it's being
// updated.
func (a *account) Deposit(amount int64) (int64, error) {
	if amount < 0 {
		return -1, errors.New("Cannot deposit a negative amount.")
	}
	a.Lock()
	defer a.Unlock()

	a.balance += amount
	return a.balance, nil
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif "typing-furiously by Egon Elbre")

```go
package defers_test

import (
	"basics/defers"
	"fmt"
	"io"
	"os"
	"time"
)

func ExampleRunAtEnd() {
	defers.RunAtEnd()
	// Output:
	// This is the second line, but first to be printed.
	// A couple of more lines for good measure.
	// Never hurt anyone.
	// defer: this is the first line, but the last output.
}

func ExampleLIFO() {
	defers.LIFO()
	// Output:
	// Random line 1
	// Random line 2
	// Random line 3
	// Third In. First Out
	// Second In. Second Out
	// First In. Third Out
}

func ExampleArgumentsEvaluated() {
	defers.ArgumentsEvaluated()
	// Output:
	// Number at end of function: 9001
	// Number in defer: 42
}

func ExampleNamedReturn() {
	fmt.Println(defers.NamedReturn())
	// Output: Bag of sand
}

func ExampleReturn() {
	fmt.Println(defers.Return())
	// Output: Golden Idol
}

func ExampleRecoverPanic() {
	// NOTE(jay): Uncomment this if you're a brave soul 🫣
	// defers.RecoverPanic(false)
	defers.RecoverPanic(true)
	// Output: Recovered from: WE'RE ALL GOING DOWN, THIS IS THE END!!!
}

// Honorable Mentions /////////////////////////////////////////////////////////

func ExampleFileClose() {
	defers.FileClose()
	f, err := os.Open("example.txt")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Print("Reading contents of example.txt: ")
	io.Copy(os.Stdout, f)
	// Output:
	// closing
	// Reading contents of example.txt: 👋 🌏
}

func ExampleTempFileRemoveClose() {
	defers.TempFileRemoveClose()
	// Output:
	// closing
	// removing
}

func ExampleHTTPBodyClose() {
	defers.HTTPBodyClose()
	// Output: Does the first 4KiB of data have Gopher Go in it? true
}

func ExampleCancelContext() {
	defers.CancelContext(300 * time.Millisecond)
	defers.CancelContext(5 * time.Millisecond)
	// Output:
	// You're too slow!!!
	// Don't forget to cancel!
}

func ExampleNewAccount() {
	a := defers.NewAccount(1000)
	a.Deposit(100)
	fmt.Println("Balance is:", a.Balance())
	// Output: Balance is: 1100
}
```
