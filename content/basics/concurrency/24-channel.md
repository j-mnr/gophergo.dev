---
Lesson: 24
Title: "Channel"
Draft: false
---

Channels! 📺 No not on the TV. 🏞 Nope not a channel of water either. What
we're talking about is a channel of _**data**_ 👩‍💻 Now why do we need
channels of data? 🤔 Good question! Here's the thing. We're not in the land of
boba tea 🧋 and honey 🍯 anymore. This is bat 🦇 country. This is the wild,
wild west 🤠. This is chaos 🌪 🌋 incarnate 👹.

### THIS IS SPAR--concurrency.... This is concurrency.

We as developers never recognize how good we have it until we introduce
concurrency into our programs 😂 Humans think in synchronous ways. As soon as
we try to juggle 🤹 multiple topics, bad things happen. _Really_ bad things.
I'll let the "experts" in concurrency over at Uber [tell you all about it
😹](https://www.uber.com/blog/data-race-patterns-in-go/). To sum up their
findings they found nearly 👀 **One Thousand** 👀 concurrency problems in their
codebases across Uber

> Wow! That's a lot of bugs for an $80+ billion dollar company to have!

I hear you say, and yes it is, but it's because concurrency is hard. 😬 [I've
literally already said that](/basics/concurrency/) and trust me when I say,
they're not the only multi-billion dollar company with concurrency problems
happening in their codebases. Across all programming languages -- Concurrency.
Is. Hard. 💎

So let's make it easier to understand! 😄 We do that by understanding how we
can control what our [goroutines](/basics/concurrency/23-goroutine) have access
to. One of those control mechanisms is channels ⛹️‍♀️👋🏀⬅➖➖➖🏀⬅ ⛹

{{% setup-instructions lessonname="channel" %}}

## Communicating Sequential Processes (CSP)

Let's understand the _why_ of channels for a second. It all started with [Tony
Hoare's Communicating sequential
processes](https://en.wikipedia.org/wiki/Communicating_sequential_processes).
There's no denying it. The world works in parallel. Meaning many things are
happening all at once independent of each other. Think of your biological
parents 👴 👵 what are they doing right now? And does it have anything to do
with you? No. What about your neighbor? 🏡 They live their own life completely
ignorant of your existence.

The world 🌏 is parallel. Since that's true, and we have programming
languages (which are for humans made by humans to solve human problems) then
why don't all languages expose concurrency? 🤔 Answer ... 🤷 I dunno 😂

Seriously, I could speculate:

1. Concurrency is _impossible_ to implement, with the way certain languages are
   designed
1. Concurrency is _really hard_ to get right, so just skip it.
1. Do concurrency and [screw it up, Java style](https://www.cs.kent.ac.uk/projects/ofa/java-threads/0.html) 😎

The list could go on, I just really have no idea. And by the way, you may have
noticed I used the word concurrency and not the word parallelism. That was on
purpose, we build parallel systems with concurrent groundwork. No groundwork,
no system. [Learn more about it with Rob!
😁](https://www.youtube.com/watch?v=oV9rvDllKEg)

I hope at this point you recognize concurrency is hard 💀 so how do we tackle
it then? 🤔 Very carefully. If we only allow a goroutine to own what it's
currently using and no other of the 1000's 👀 of goroutines to use that thing
then we've solved concurrency! 🥳 🎉 🎉 Now how do we do _that_? 🤔

Let's think about a teacher 👩‍🏫 and class of children 👧🧒🧒👧🧒👦👧👦👧👧🧒🧒 at school
🏫

> OK, kids time for some 🍭 candy 🍬 I want each of you to grab one piece from
> the table and no fighting.

Do you think they're going to listen? 😏 **_Helllllllllll_ No** they're not
going to listen! They're going to push and shove 👊 and trample for the damn
sweets 🧁! 😂 And possibly take several pieces! (This is a human example of a
data race).

How do we solve the dilemma with the kids, then? 🤔 Well one way is only allow
them access to the resource (The sweets 🍫) through a controlled **channel**,
the teacher 👩‍🏫 What if we gave a kid (a goroutine) one piece of candy
🍪 (a resource) and then repeated that for all of the kids (goroutines)? Now
that child (goroutine) eats that candy 🍨 (does work to the resource it owns)
and isn't bothered by any other kid (goroutine). No sharing required! 👏👏👏
⚠Note⚠ The students (goroutines) had no other way of obtaining the candy
(resource) _besides_ the teacher (channel).

This is Communicating Sequential Processes (CSP) in a nutshell 🌰 We don't
communicate by sharing there are resources (candy to eat), we share
resources (candy) by communicating we have them ready to be eaten.

> [Don't communicate by sharing memory, share memory by communicating.](https://go.dev/blog/codelab-share)

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### `channel.go`

```go
// KidEats imitates a goroutine (this function in this case) taking in a
// resource, doing something with it and then returning some result.
func KidEats(candy string) string {
	fmt.Println("🧒 💬 I'm eating " + candy)
	return "wrapper"
}
```

#### `example_test.go`

```go
func ExampleKidEats() {
	teacher := make(chan string)
	// This function could easily be called HandoutCandy
	go func() {
		teacher <- "🍭"
		teacher <- "🍬"
		teacher <- "🍫"
		teacher <- "🧁"
		teacher <- "🍪"
	}()

	garbage := make(chan string)
	for i := 0; i < 5; i++ {
		go func() {
			garbage <- channel.KidEats(<-teacher)
		}()
	}

	bin := make([]string, 5)
	for i := 0; i < 5; i++ {
		// instead of throwing away the result, we would use it in a real app.
		bin[i] = <-garbage
	}
	fmt.Println("All the wrappers have been collected.")
	// Unordered output:
	// 🧒 💬 I'm eating 🍭
	// 🧒 💬 I'm eating 🍬
	// 🧒 💬 I'm eating 🍪
	// 🧒 💬 I'm eating 🍫
	// 🧒 💬 I'm eating 🧁
	// All the wrappers have been collected.
}
```

## The Arrow `<-` Operator

Before we continue, a quick 🏇 note on the new operator that Go introduces with
channels. The arrow operator `<-`, as I call it, is used for _sending_ **and**
_receiving_. It all depends on where you put it. e.g. `myChan <-
"**sending** this into a channel"` or `recvFrom := <-myChan`. And that's all
there is to it. Simple and plain, like vanilla ice cream🍦

The important thing to recognize when using `<-` is you're likely to block 🧱
execution of code. This is what all programs do by default, execute 1 line at a
time. Synchronously. When you put `go` in front of a function you're giving
that function freedom to _not_ block the flow of execution, but at the end of
the day 🌇 you're going to want your results from all of your goroutines that
you started up. So to make sure all your results come back we can use channels
and block 🧱 with `<-` until we've gotten what we want.

We can see that being done with the `garbage := make(chan string)` channel. It
blocks 🧱 5 times in the for loop. If we made that for loop `i < 6` then it
would block forever because no other goroutine would send anything to the
garbage 🚮 channel and the function would never complete 😢

## Unbuffered Channels

Now that we understand the idea of CSP, we can understand the tool
(channel) better and explain the code we wrote above.

First, we'll start with the channel we made `teacher := make(chan string)`,
it's what's known as an **unbuffered** channel. A buffer 🗄️ is a place where
values can be stored in and taken out from at varying rates. So if we have an
_un_-buffered channel it means we have no place to store values ❌🗄️. With no
place to store values it means we have to exchange them right then and there.

⚠ Something important to note about unbuffered channels is they ⛔ block ⛔
when they receive `channel <- "receive value"` **AND** when they send `sent :=
<-channel` This seems like a small 🤏 detail but it's fundamental to
understanding the difference with buffered channels.

Essentially we can think of unbuffered channels as truly needing two goroutines
to function correctly, which makes sense 🪙, because why would we use a
channel in the first place if we weren't expecting to _Communicate_ something?

As an example let's think about a chef 👨‍🍳 and their food 🥙. Why would
a chef make any food if no one was going to eat it? Answer: They wouldn't!
Another example is in baseball there's a pitcher ⚾ and a catcher 🖐 if the
pitcher is missing what does the catcher do? Answer: Nothing! One more example
is calling someone on the phone 📳 if they didn't answer would you start
talking 🗣️? Answer: Nope! Last example, two players on a basketball team.
⛹️‍♀️👋🏀⬅➖➖➖🏀⬅ ⛹ If one of them was missing would the other blindly
throw the ball to "pass" it to no one else? Answer: Of course not!

All of these examples show that there is a sender and a receiver for the
resource in each case 💼. So make sure you know who's giving the values and
who's taking the values or else you're gonna find yourself in a pickle 🥒 just
always remember it takes two people to tango 💃💃 and two goroutines to use
unbuffered channels.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### `channel.go`

```go
// UnbufferedNoReceiveCausesPanic will cause a panic if you run it by
// itself. This is because there is a very well understood idiom in Go:
//
// "Don't communicate by sharing memory; share memory by communicating."
//
//	-- Rob Pike
//
// This means that unbuffered channels are **required** to communicate with
// other goroutines, i.e.
// for every send `c <- "stuff"`
// there must be a receive `giveme := <-c` from another goroutine.
//
// XXX(jay): Running this in main will cause a panic!
//
//	fatal error: all goroutines are asleep - deadlock!
func UnbufferedNoReceiveCausesPanic() {
	c := make(chan string)
	c <- "doesn't matter going to panic"
	fmt.Println("This doesn't work", <-c, "there is no communication")
}

// Unbuffered shows how to push values into an unbuffered channel. To be
// unbuffered means there is no limit to the amount of values you can push into
// the channel.
func Unbuffered(c chan int) {
	for i := 1; i <= 9001; i++ {
		c <- i
	}
	close(c)
}
```

#### `example_test.go`

```go
func ExampleUnbufferedNoReceiveCausesPanic() {
	// XXX(jay): Removing this from a separate goroutine will block forever.
	go channel.UnbufferedNoReceiveCausesPanic()
	// Output:
}

func ExampleUnbuffered_drain_channel() {
	c := make(chan int)
	done := make(chan struct{}, 1)

	go channel.Unbuffered(c)

	go func(c chan int, done chan struct{}) {
		for n := range c {
			if n > 12 && n < 8998 { // too much output to care about
				continue
			}
			if n > 9000 {
				fmt.Print(n)
				break
			}
			fmt.Print(n, " ")
		}
		done <- struct{}{}
	}(c, done)

	// NOTE(jay): This is how we make the main goroutine,
	// `ExampleUnbuffered_drain_channel`, wait for the 2 other goroutines we spun
	// up. If we remove this then it would exit without letting the others do
	// their job.
	<-done

	fmt.Println("\nOVER 9000!!!")

	// Output:
	// 1 2 3 4 5 6 7 8 9 10 11 12 8998 8999 9000 9001
	// OVER 9000!!!
}

func ExampleUnbuffered_channel_has_values_left() {
	c := make(chan int)
	go channel.Unbuffered(c)
	<-c // drain values because we can
	fmt.Println(<-c)
	// drain value after to show we don't block because we have more values to
	// receive.
	<-c
	// Output: 2
}
```

## Buffered Channels

When we give a channel a buffer `buffed := make(chan string, 5)` it leaves
space for goroutines to drop off the value into the buffer and continue doing
their job. We can think of it like a mailbox 📭. When mail is delivered 📨
there's nothing stopping them from delivering more 📨 mail 📨 the next day. It's only
when the mailbox reaches capacity 📬 and no more mail fits when the mail is no
longer delivered.

Buffered channels work the exact same. The analogy even works for receiving
mail too. Let's say you're waiting for your package 📦 and you're a goroutine
`pkg := <-mail`. If the mail (separate goroutine) is still on it's way 🚚
you're stuck waiting ⌚. The same way if we see the line `pkg := <-mail` and
there is no value in `mail` we pause execution for that goroutine.

But as long as your mailbox 📭 is not full, more mail can be pushed into it.

```go
📭 := make(chan string, 10) // can fit ten letters before full
📭 <- "✉ Letter 1: does not block"
📭 <- "✉ Letter 2: does not block"
📭 <- "✉ Letter 3: does not block"
fmt.Println("This line will print without a problem!")
```

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### `channel.go`

```go
// BufferedChanWorks demonstrates you can use a buffered channel in the same
// goroutine without creating a deadlock because there is an area to store the
// extra values that will be pushed into the channel, a buffer.
func BufferedChanWorks() {
	c := make(chan string, 1)
	c <- "This is a useless to do, but great for understanding"
	// NOTE(jay): We could not do this
	//   c <- "Some other value into the buffed channel with only 1 opening"
	// because it _would_ block waiting for space to put the value in.
	fmt.Println(<-c)
}

// Buffered shows you that when you buffer a channel that is the final
// capacity that the channel can reach of a certain type and if we try to go
// over we will panic! But we can actually send values into the channel, unlike
// unbuffered channels which would cause a panic.
func Buffered(c chan string) {
	c <- "first value to channel"
	c <- "second value to channel"
	// XXX(jay): If we uncomment this line we panic with
	//    fatal error: all goroutines are asleep - deadlock!
	// This is because this buffered channel can only be filled up to
	// its limit -- 2 and since no one is taking the values out, we block
	// indefinitely and find ourselves in a deadlock!
	// c <- "third thing"
	close(c)
}
```

#### `example_test.go`

```go
func ExampleBufferedChanWorks() {
	channel.BufferedChanWorks()
	// Output: This is a useless to do, but great for understanding
}

func ExampleBuffered() {
	c := make(chan string, 2)
	done := make(chan struct{}, 1)

	go channel.Buffered(c)

	go func(c chan string, done chan struct{}) {
		for s := range c {
			fmt.Println(s)
		}
		done <- struct{}{}
	}(c, done)

	<-done

	// Output:
	// first value to channel
	// second value to channel
}
```

## Channel Synchronization

There are 4 things that haven't been explained yet with the `Example*`
functions we've wrote.

1. What is the `done` channel and why is it there?
1. [What does `close` do to a channel?](#closing-a-channel)
1. [When can you `range` over a channel?](#range-over-channel)

We'll explore the first question here and the others will follow. To understand
why we need the `done` channel I think the easiest thing would be to comment it
out 🧽 and see what happens to our test.

```go
func ExampleBuffered() {
	c := make(chan string, 2)
	done := make(chan struct{}, 1)

	go channel.Buffered(c)

	go func(c chan string, done chan struct{}) {
		for s := range c {
			fmt.Println(s)
		}
		// done <- struct{}{}
	}(c, done)
	// <-done

	// Output:
	// first value to channel
	// second value to channel
}
```

If you tried it you'll see we got nothing ☐ but want something ⬜ This is one
way to solve the [synchronization
problem](/basics/concurrency/23-goroutine/#in-sync) we talked about in the last
lesson. Remember goroutines don't care about other goroutines (by design) they
are left to their own devices 🧑‍🔧, and if one finishes it does clean up
🧹. This includes the main goroutine. So when the main goroutine hits the
bottom of the tasks it was given it exits without waiting for others to finish.
We have to _communicate_ to the main goroutine to wait ✋ for some results.

We figured out that this line 👉 `myVal := <-myChan` blocks until it can give a
value. So what happens if we don't need to capture the value? We could do `_ :=
<-myChan`, but why stop there? Just remove the useless variable declaration
altogether! `<-myChan` and maybe give it a good name like `<-done` to signal
that a separate goroutine that's going to put some sentinel (filler/dummy)
value into that channel is done and the primary goroutine (the
`ExampleBuffered` goroutine in the above case) can continue execution. This can
also be seen with [the first example](#example_testgo) where we throw out the
result `<-garbage` into a `bin` 🗑. If we didn't collect the garbage the test
would block (and fail) there too.

And on a final note, this is a concurrency pattern, it can also be done using
[`sync.WaitGroup`]() (coming soon!). Creating a `sync.WaitGroup` isn't better
or worse, it's just a different way to do it and it depends on what you're
doing for which you would use.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### `channel.go`

```go
// CommunicateDone is an example of how to let a completely separate goroutine
// (which has no idea what any other goroutines are doing) know when it is safe
// to continue execution by communicating it has finished the work that it was
// tasked with doing.
func CommunicateDone(done chan<- struct{}) {
	fmt.Println("Starting up to do some hard work.")
	time.Sleep(250 * time.Millisecond)
	fmt.Println("All finished with the hard work.")
	done <- struct{}{}
}
```

#### `example_test.go`

```go
func ExampleCommunicateDone() {
	done := make(chan struct{}, 1)
	go channel.CommunicateDone(done)
	<-done
	// Output:
	// Starting up to do some hard work.
	// All finished with the hard work.
}
```

## Closing A Channel

We use `myChan := make(chan string)` to create a channel, `myChan <- "send
value"` to send values to a channel and `recvFrom := <-myChan` to receive a
value from a channel. So we have everything we need right? 🤔 What else could
you want from a channel?

The answer is more communication 📢 because who's to say, when receiving values
from a channel, that they are good values? For example what if we had a channel
of `int` and we receive this 👉 `0 0 0 0 0 0 0` 🤨 Hmmm, little fishy 🐟 isn't
it?

What if we know our channel is going to run out of values, but we just don't
know _when_ it's going to run out? With the ability to make, send, and receive
on a channel, we have no way of knowing when to stop looking 🕵️ for more
values.

Enter `close`, its exact purpose is to make that communication possible. We're
going to expand our receive syntax. It works just like [checking a
`map`](/basics/10-map/#check-for-values) 🗺, but instead of looking for the
presence of a value, we check if the channel is still open `val, open :=
<-myChan`.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### `channel.go`

```go
// ClosingChannels is an example function that shows there is a second value you can look
// at when receiving a value from a channel. This second value tells you if the channel is
// still open, which is a good clue (if the value is the zero value of its type)
// that there are no more values to receive on that channel.
func ClosingChannels(kitchen <-chan rune, done chan<- struct{}) {
	for {
		order, open := <-kitchen
		if !open {
			fmt.Println("channel has closed; zero value of channel:", order)
			done <- struct{}{}
			return
		}
		fmt.Printf("requested to make: %q\n", order)
	}
}
```

#### `example_test.go`

```go
func ExampleClosingChannels() {
	done := make(chan struct{})
	kitchen := make(chan rune)

	go channel.ClosingChannels(kitchen, done)
	for _, order := range []rune{'🍔', '🍲', '🍗', '🍟', '🥞', '🍕', '🥪'} {
		kitchen <- order
	}
	close(kitchen)
	fmt.Println("All orders have been sent to the kitchen.")

	// The output is **always** ordered, do you know why? 🤔 Think 💪
	<-done
	// Output:
	// requested to make: '🍔'
	// requested to make: '🍲'
	// requested to make: '🍗'
	// requested to make: '🍟'
	// requested to make: '🥞'
	// requested to make: '🍕'
	// requested to make: '🥪'
	// All orders have been sent to the kitchen.
	// channel has closed; zero value of channel: 0
}
```

## Range Over Channel

Good to see we can manually check whether a channel is closed or not, but it's
a little inconvenient to need to write a loop ➿ like that. If only we had some
mechanism that allows us to _range_ 😹 over a set of values 🤔 ... Hmm, maybe a
while loop? -- KIDDING! 🤪 Of course, we can `range` over a channel. In Go when
you have a collection of things you can `range` over them and channels are no
different.

This was covered in the [`range`
lesson](/basics/11-range/#range-through-channel) shortly, but for completeness
and more examples (as more examples are always better 🙌) we'll go over it
here.

When it comes to `range` over a `chan` there is no ❌ second value, as we don't
_need_ to check if the channel is closed, the loop ➿ does it for us. So all we
need to worry about is doing something with the value and that the channel is
properly closed 🚪.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### `channel.go`

```go
// Range shows how to range over both a buffered and unbuffered channel. It is good to
// note that neither of the channels are closed by this function. It's almost never a good
// idea for the receiver to be closing the channels.
func Range(buf, unbuf chan string, done chan struct{}) {
	go func() {
		for s := range buf {
			fmt.Println(s)
		}
		done <- struct{}{}
	}()
	go func() {
		for s := range unbuf {
			fmt.Println(s)
		}
		done <- struct{}{}
	}()
}
```

#### `example_test.go`

```go
func ExampleRange() {
	buf := make(chan string, 4)
	unbuf := make(chan string)
	done := make(chan struct{})

	buf <- "It's possible to close"
	buf <- "a non-empty channel and"
	buf <- "still have the remaining"
	buf <- "values be received"
	close(buf)
	go channel.Range(buf, unbuf, done)

	unbuf <- "Unbuffered channels"
	unbuf <- "will block on a v := <-receive"
	unbuf <- "and on a send <- v"
	unbuf <- "this is important to remember"
	close(unbuf)

	<-done
	<-done
	// Unordered output:
	// Unbuffered channels
	// will block on a v := <-receive
	// and on a send <- v
	// this is important to remember
	// It's possible to close
	// a non-empty channel and
	// still have the remaining
	// values be received
}
```


## Channel Directions

We know that we can `myChan <- send` values and also `receive := <-myChan`
values, but what if we only wanted one or the other? Like the above example on
`range`, we can see we only ever **_received_** 📦 values from the channels and
never sent 📨 any. The opposite can be said for the `done` channel; we only
sent 📨 values into it, never read from it.

So with that in mind, can we make a clearer API? 😏 I think you already know
the answer since we're talking about it. Yes! Of course, we can make sure that
when a function receives a channel that it is send-only 📨 or receive 📦 only.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### `channel.go`

```go
// ReceiveValueFrom shows you how to declare a channel that will only
// allow values to receieve from the channel.
func ReceiveValueFrom(c <-chan string) {
	// XXX(jay): This won't work, compiler says:
	//    invalid operation: cannot send to receive-only type <-chan string
	//    [compiler: InvalidSend]
	// c <- ""
	one := <-c
	two := <-c
	three, four := <-c, <-c
	fmt.Println(one, two, three, four)
}

// SendValueInto shows you how to declare a channel that will only allow
// values to be sent into the channel.
func SendValueInto(c chan<- string) {
	// XXX(jay): This won't work, compiler says:
	//  invalid operation: cannot receive from send-only channel c (variable of
	//  type chan<- string) [compiler: InvalidReceive]
	// wontWork := <-c
	c <- "send"
	c <- "all"
	c <- "the"
	c <- "values"
	close(c)
}
```

#### `example_test.go`

```go
func ExampleSendValueInto() {
	c := make(chan string)

	go channel.SendValueInto(c)

	go channel.ReceiveValueFrom(c)

	// XXX(jay): This is lazy 🦥 should use a `done` channel in its place.
	time.Sleep(5 * time.Millisecond)
	// Output: send all the values
}

func ExampleReceiveValueFrom() {
	c := make(chan string, 4)

	c <- "not necessary to"
	c <- "close a channel"
	c <- "if the other goroutine"
	c <- "isn't draining it"

	go channel.ReceiveValueFrom(c)

	// XXX(jay): This is lazy 🦥 should use a `done` channel in its place.
	time.Sleep(5 * time.Millisecond)
	// Output: not necessary to close a channel if the other goroutine isn't draining it
}
```

## `select`

If you've followed along the entire time, Hell yeah! 😁 Glad you're enjoying
it, but back to the point, it may _seem_ like there will always be values to
give from channel to channel. When in actuality that's almost never going to
happen 😅

When writing this lesson up I created **several** deadlocks 💀🔒, all of which
were my own fault, but something that can easily avoid these situations is the
`select` keyword in Go. It looks **a lot** like a [`switch`
statement](/basics/06-switch), which is even more reason to love the `switch`
statement 😏

I said previously that if you have a buffered channel it will block on `myChan
<- sendingValue` if the buffer is full 📬 and if there are no values it will
block on `receivingVal := <-myChan`. An unbuffered channel **always** blocks,
there needs to be a goroutine ready to receive when a value is sent and vice
versa.

Getting into a sticky 🕸 situation like blocking when you didn't mean to, is a
good way to kill your program ☠☠ dead 🧟. We can get around blocking by using a
`select` statement with it's `default` keyword (again very much like a `switch`
statement).

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### `channel.go`

```go
// Select shows off all of the features of the [select] keyword. It's very similar to the
// [switch] keyword, but for concurrency. In here we see that the best features of
// [select] are it's ability to make all channel types non-blocking on sends and receives
// with the [default] keyword and also the ability to create multi-branching paths.
func Select(fullbuf, unbuf chan string, done chan struct{}) {
	select {
	case fullbuf <- "cannot push this into a full buffer":
	default:
		fmt.Println("Did not block after trying to send value into a full buffered channel.")
	}

	select {
	case recvVal := <-unbuf:
		fmt.Println("We will never receive this value because no one is sending", recvVal)
	case <-unbuf:
		// NOTE(jay): We don't have to capture the value, maybe we just want to know if there
		// are values in the buffer. Again, this would block under normal circumstances.
	default:
		fmt.Println("Did not block after trying to receive value from an unbuffered channel")
	}
	done <- struct{}{}

	for {
		select {
		case v := <-fullbuf:
			fmt.Println(v)
		case v := <-unbuf:
			fmt.Println(v)
		case <-time.After(100 * time.Millisecond): // Just like a timeout.
			done <- struct{}{}
			return
			// NOTE(jay): In this case we would get into trouble if we used the [default] case
			// because it's NOT verifiable that after the `case v := <-unbuf` the next value for
			// `unbuf <- "some string"` will be sent before the next iteration in this for loop.
			// case default:
			// done <- struct{}{}
			// return
		}
	}
}
```

#### `example_test.go`

```go
func ExampleSelect() {
	fullbuf := make(chan string, 5)
	unbuf := make(chan string)
	done := make(chan struct{})

	fullbuf <- "A [select] does not guarantee"
	fullbuf <- "execution of case ordering"
	fullbuf <- "meaning the second case may be"
	fullbuf <- "selected to fire even if the"
	fullbuf <- "first case is ready to fire"

	go channel.Select(fullbuf, unbuf, done)
	<-done
	unbuf <- "If one or more of the communications"
	unbuf <- "can proceed, a single one that can"
	unbuf <- "proceed is chosen via a uniform"
	unbuf <- "pseudo-random selection."
	<-done

	// Unordered output:
	// Did not block after trying to send value into a full buffered channel.
	// Did not block after trying to receive value from an unbuffered channel
	// A [select] does not guarantee
	// execution of case ordering
	// meaning the second case may be
	// selected to fire even if the
	// first case is ready to fire
	// If one or more of the communications
	// can proceed, a single one that can
	// proceed is chosen via a uniform
	// pseudo-random selection.
}
```

## Source File 📄

[The Source File](https://goplay.tools/snippet/7sHPIhOjfPy)

## Test File 📝

[The Test File](https://goplay.tools/snippet/iuv5bgPSg2Y)
