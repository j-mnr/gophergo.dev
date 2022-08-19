---
Lesson: 27
Title: "Enums"
Draft: false
---

Enumerations! 👻 Scary looking word, but don't worry, we'll **list** it out 🗒
**step by step** 👣 until we're done. **One** example **after the other** 🔢 until
we've exhausted them. 😹 

An enumeration or better yet an enum is a collection of values, usually an
`int` type of a certain size (`uint8`, `int64`, ...), that allow us to
concentrate on what we deem as important values and they're awesome ! 😁 👍

I'll be upfront and say Go doesn't actually have enums... 🤷 At least not a
keyword for it. Though once we understand _what_ an enum accomplishes we'll see
that they do exist in Go, just in a freer 🕊 form.

To make it crystal 🔮 clear let's give ourselves a problem. Let's say we are
tasked with creating sections for a store, [Produce 🥦 Meat 🥩 Seafood 🦐
Bakery 🥖 Frozen 🥶] into our codebase. How would we go about making each
section? 🤔 You might be tempted to just put them in a `string` and call it
good 👏, but then how do we know they are of like things? Because this 👉
`"poison"` 🤮 is a `string` and so is `"produce"`. Why isn't there a poison
section? OK, OK, so they need their own type, `section` **and** let's put them
in a slice too, `sections := []section{"produce", "meat",...}` and now we've
solved the problem of knowing they belong together, but how would we know
`"produce"` 🍅 is `0` in the slice? Better yet, _why_ would we need to know
that? 🤨 And finally what happens if someone adds something in front of
`"produce"` 👀 or worse changes `"produce"` to `"poison"` `sections[0] =
"poison"` 😬

Tough problem, right? Well not with an enum. Enums are made for this exact
purpose. **List through a group of Constant like-minded values.** They also come with the
benefit of being light-weight 🪶 types. Remember, computers 🖥 only understand
numbers and more precisely `0`'s and `1`'s. So it's _very_ inefficient to use
`string` in the first place like we tried previously, along with all of the
other inefficiencies and shortcomings.

{{% setup-instructions lessonname="enum" %}}

## One `iota` at a time

There is a keyword in Go called `iota`, it will take a `const` block of values
and assign them values from `0` to the length of the block. This is the way we
declare enums in Go.

```go
const (
	Zero  = iota // 0
	One          // 1
	Two          // 2
	Three        // 3
)
```

The word `iota` is a Greek letter and has a meaning of being atomic ⚛️ or the
smallest possible unit. I've never seen any sources on this, but I believe the
term comes from the programming language
[APL](https://en.wikipedia.org/wiki/APL_syntax_and_symbols) (search for `iota`)
which takes the smallest subunit to increment by, `1`, and creates a list of a
sequence of consecutive numbers. `{0, 1, 2, 3, 4, 5}`

Don't worry if that doesn't make sense 😅 the history lesson of `iota` isn't
important, it's just valuable to know that `iota` is how we declare our
enums in Go. We will create blocks of [constant](/basics/03-constant) values
and assign the `type` to the first one and set it equal to `iota` and like
magic 🪄 we've made our enum.

We can do lots of fancy things with `iota` like:

- `1 << iota`
- `0 - iota`
- `(1<<31 - 1) - iota`

to name a few, but we're going to keep it simple. You can find more examples in
the [standard
library](https://sourcegraph.com/search?q=context:global+repo:%5Egithub%5C.com/golang/go%24+%3D+.*+iota+rev:master+lang:go+&patternType=regexp)

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### enum.go

```go
type Difficulty int

const (
	Easy     Difficulty = iota // iota == 0
	Medium                     // iota == 1
	Hard                       // iota == 2
	VeryHard                   // iota == 3
)
```

#### example_test.go

```go
func ExampleDifficulty() {
	// NOTE(jay): we use [1:] to get rid of the `\n` newline character at the
	// very start of this string. This is very common to see when using
	// multi-line strings in Go. We would have it look like
	// `Easy: %d
	//Medium: %d ...`
	// otherwise, which can be hard to read.
	fmt.Printf(`
Easy: %d
Medium: %d
Hard: %d
VeryHard: %d`[1:], enum.Easy, enum.Medium, enum.Hard, enum.VeryHard)
	// Output:
	// Easy: 0
	// Medium: 1
	// Hard: 2
	// VeryHard: 3
}
```

## A _very_ necessary tool for Enums

Enums are great for packing and collecting an idea and have and will always
serve that purpose in the application... However, there are a great many
applications that talk 💬 with other services 🗨 and if we just send the value
`2` to another service, it might not know what it means. And also when we are
debugging 🐞 seeing some constant value of `1` doesn't tell us much does it?

This was a known limitation and there is a `golang.org/x` tool 🔧 that creates
efficient `String` implementations for our enums. I **highly** recommend you
install this on your system, but we'll also see how to make it more robust
[down below 👇](#better-directive-and-solution) So, you can skip this if you
want.

In the terminal, in your home directory you need to run this `go install
golang.org/x/tools/cmd/stringer@latest` if this is your first time running this
subcommand, then a quick view `go install
<SOME_MODULE>/<MAIN_BIN>@<BUILD_TAG>` and also congratulations! 🥳 You just
installed your very first Go binary _with_ Go! You should be able to type
`stringer` in the terminal and see something that **doesn't say** `command not
found/cmdlet not recognized`. Now next time someone tells you 🗣 "That's
written in Go" you'll know how to install it. Find the repo it belongs to, find
the executable, in `cmd`, and put the build tag. Also if you want to see full
documentation for `stringer` you can go to where Go goes 😹 to grab it
https://golang.org/x/tools/cmd/stringer

Now a small overview on [Compiler
Directives](https://pkg.go.dev/cmd/compile#hdr-Compiler_Directives). There are
two ways✌ to write comments, for humans and for the compiler. If your comment
has no space `//go:generate` it's not for humans 🤖, if it does `// go
generates this` it's for humans 🙋 and that's all. Read 📖 more about it up
above if you want, but it's out of scope 🔭 for this lesson.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### enum.go

When we run `go generate` from the command line the compiler looks for
directives and executes them. Remember this will not work if you didn't install
`stringer`, but we'll fix it in a coming topic.

```go
//go:generate stringer -type=Status -trimprefix=Status
type Status uint8

const (
	StatusPending Status = iota
	StatusActive
	StatusInactive
	StatusDeactivated
)
```

#### example_test.go

We use [goroutines](/basics/) and `sync.WaitGroup` (coming soon!) here. It's
not important to understand them. Just recognize we use our enum values and
`string` values come out! 😲

```go
func ExampleStatus() {
	// NOTE(jay): You must run `go generate` or there will be no String method.
	type worker struct {
		s enum.Status
	}
	var wg sync.WaitGroup
	wg.Add(5)
	workers := make([]*worker, 5)
	for i := range workers {
		workers[i] = &worker{}
	}
	for _, w := range workers {
		go func(w *worker) {
			if w.s == enum.StatusPending {
				fmt.Println("worker is", w.s, "giving work")
			}
			w.s = enum.StatusActive
			// Act like a worker is doing work here...
			d := time.Duration(rand.Intn(255))
			time.Sleep(d * time.Millisecond)
			w.s = enum.StatusInactive
			fmt.Println("worker is done with it's work", w.s)
			// Cannot update workers slice here because it will result in data race
			if d > 127 {
				w.s = enum.StatusDeactivated
				fmt.Println("worker took too long", w.s)
			}
			wg.Done()
		}(w)
	}
	wg.Wait()

	fastest := make([]*worker, 0, len(workers))
	for _, w := range workers {
		if w.s != enum.StatusDeactivated {
			fastest = append(fastest, w)
		}
	}
	fmt.Println("The fastest remaining:", len(fastest))
	// XXX(jay): This may fail!!! Remember goroutines don't run one after the
	// other **and** we use `rand` here to get a random number, lots of unknowns.

	// Output:
	// worker is Pending giving work
	// worker is Pending giving work
	// worker is Pending giving work
	// worker is Pending giving work
	// worker is Pending giving work
	// worker is done with it's work Inactive
	// worker is done with it's work Inactive
	// worker is done with it's work Inactive
	// worker took too long Deactivated
	// worker is done with it's work Inactive
	// worker took too long Deactivated
	// worker is done with it's work Inactive
	// worker took too long Deactivated
	// The fastest remaining: 2
}
```

## Default value problem

Here's a question, what happens if we just _made_ an enum value? 🤔 🤨 As in,
what if we did this 👉 `var s enum.Status`? It'd **default** to `0` and that
would be the `Pending` status, but what if someone else gives us information
and the way _they_ say there is no information is with `0`? 👀 How would we
tell that they meant 💬 "I have no data for this value", if _our_ value is
`Pending`? Answer... We wouldn't 💀 This isn't something special to Go, this is a
[common
practice](https://stackoverflow.com/questions/12593061/define-an-unknown-or-null-value-in-an-enum)
in [other
languages](https://docs.microsoft.com/en-us/previous-versions/dotnet/netframework-3.0/ms182149(v=vs.80)?redirectedfrom=MSDN)
too. That means we want to at the _very least_ start our `iota` at one instead
of zero -- `iota + 1`.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### enum.go

```go
// PollAPI acts like we call out to an API that informs us of the current State
// of a request we made. Let's pretend that if we ask about some request it
// doesn't know, it gives back the `int` default value (0).
func PollAPI(found bool) State {
	if !found {
		return 0
	}
	return Progressing
}

//go:generate stringer -type=State
type State int

const (
	Start       State = iota + 1 // iota == 1
	Progressing                  // iota == 2
	Success                      // iota == 3
	Fail                         // iota == 4
)
```

#### example_test.go

```go
func ExampleState() {
	// NOTE(jay): You must run `go generate` or there will be no String method.
	if s := enum.PollAPI(true); s != 0 {
		fmt.Println("The current state is:", s)   // same as s.String()
		fmt.Printf("The current state is: %s", s) // can also have %d
	}
	if s := enum.PollAPI(false); s != 0 {
		fmt.Print("Never going to make it in here")
		fmt.Print("Never going to make it in here")
	}
	// Output:
	// The current state is: Progressing
	// The current state is: Progressing
}
```

## Better directive and solution

Let's bolster 🦾 our previous two points.

### A better directive

It's great that we have `stringer` on our system now and can use it
wherever we want, whenever we want for plenty of enums, but what if we run
this somewhere else, like in the cloud ☁ ? It will try to run `stringer
-type=State` on the command line and it's going to error out! 😵 It doesn't
have `stringer` installed, but we can make sure no matter where we _Go_ 😹 we
will have `stringer` with us.

A looooong time ago ⌛ we [setup our project](/basics/00-hello-gophers/#setup)
to have the module name `basics`. When setting up a module you're allowed
access to download libraries and tools to support your module. We can do the
same right now! 👾 In the terminal we can run `go get -u
golang.org/x/tools/cmd/stringer` and you'll see a couple of dependencies
download for our module.

```sh
go: upgraded golang.org/x/mod v0.6.0-dev.0.20220106191415-9b9b3d81d5e3 => v0.6.0-dev.0.20220419223038-86c51ed26bb4
go: upgraded golang.org/x/sys v0.0.0-20211019181941-9d821ace8654 => v0.0.0-20220708085239-5a0f0661e09d
go: upgraded golang.org/x/tools v0.1.10 => v0.1.11
```

Now our module has `stringer` and if we ran this in a pipeline to `go generate`
the necessary tools will be found! 👍 🎉 We will need to update the directive
with the complete path to `stringer` `golang.org/x/tools/cmd/stringer`

### A better default value solution

I alluded 🥸 to it in the previous topic. One solution is to make sure none of
our values is the default value `0` and for the cherry 🍒 on top, we can call
that default value by a special name, usually `Unknown` 🥷 or `None` ⚫

This is important because if you're asked:

> What's the coldest planet 🪐 in the solar system?

And you say nothing, that means you failed ❌ to respond to the question and
you're asked again. If you say, 🤷 "I don't know", then it means you recognize
what the question means, but don't have an answer. Once more:

> What was the hottest day 🥵 of the week in August last year?

Again, saying _nothing_ means you **don't** acknowledge the question. Maybe you
don't understand dates 📅 or what hot ♨ is, but it's inherently different from
saying, 💬 "I don't know" or "A Tuesday or A Friday ... Maybe?"

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### enum.go

```go
//go:generate go run golang.org/x/tools/cmd/stringer -type=Sport
type Sport int

const (
	Unknown Sport = iota
	Baseball
	Soccer
	Boxing
	Tennis
	Hockey
)
```

#### example_test.go

```go
func ExampleSport() {
	var s enum.Sport
	fmt.Println(s.String())
	// enum.Baseball.String() works too, but isn't preferred
	fmt.Println(enum.Baseball)
	s = enum.Boxing // == 3. Change this to see our cases print other messages.
	switch s {
	case enum.Baseball:
		fmt.Println("Time to play ball!")
	case enum.Boxing:
		fmt.Println("Round 1! Fight!")
	case enum.Soccer:
		fmt.Println("GOOOOOOOOOOAAAAAAAAAAAL!!!")
	case enum.Hockey:
		fmt.Println("Let's get on the ice!")
	case enum.Tennis:
		fmt.Println("Time for tennis!")
	default:
		fmt.Println("That sport hasn't been added yet! Maybe put in a PR?")
	}
	// Output:
	// Unknown
	// Baseball
	// Round 1! Fight!
}
```

## A Clearer Enum

These enums just keep getting **better and better** 😁 Alright, next! 😤 It's
great that we can have a collection of like-minded values, but how is someone
supposed to _know_ what values belong to the enum? 🤔 Think about it 🧠 for our
last example we had `enum.Sport`. Did your favorite sport 🎾 make it on that
list? Probably not, so how do we let people know each value belongs to the same
enum?

We can group our enum values with `iota` and our names with a **prefix**. By
doing this it's transparent 🪟 what values belong to the enum. We can even see
this in the standard library with [`fs.FileMode`](https://pkg.go.dev/io/fs#FileMode)

And you know what's great about `stringer`? It comes with a `-trimprefix` flag!
🚩 So when we create our `String` for the enum we can remove the prefix. Nice
😎👍

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### enum.go

```go
//go:generate go run golang.org/x/tools/cmd/stringer -type=Role -trimprefix=Role
type Role uint8

const (
	RoleUnknown Role = iota // iota == 0 -- Good for unknown to be 0!
	RoleGuest
	RoleMember
	RoleModerator
	RoleAdmin
)
```

#### example_test.go

```go
func ExampleRole() {
	// Let's act like we're routing user traffic and depending on their `Role` we
	// will guide them to a certain page.
	var r enum.Role
	for ; r < 6; r++ {
		switch r {
		case enum.RoleUnknown:
			fmt.Println(r, "should be directed to the login page.")
		case enum.RoleGuest:
			fmt.Println(r, "should be allowed to chat with restrictions.")
		case enum.RoleMember:
			fmt.Println(r, "should be directed to their home page.")
		case enum.RoleModerator:
			fmt.Println(r, "should be routed to the moderator overview page.")
		case enum.RoleAdmin:
			fmt.Println(r, "should be routed to the Administration domain.")
		default:
			fmt.Println("This is not a role!", r)
		}
	}
	// Output:
	// Unknown should be directed to the login page.
	// Guest should be allowed to chat with restrictions.
	// Member should be directed to their home page.
	// Moderator should be routed to the moderator overview page.
	// Admin should be routed to the Administration domain.
	// This is not a role! Role(5)
}
```

## Real _powers_ 😹 of Enums

`1 << iota` is the same as {{< topower 2 iota >}} `1 << 4 ==` {{< topower 2 4
>}} `== 16 == 0b00010000` so with the powers of 2 we can do some real fun
stuff.

Now a real shining 🌟 achievement of enums, is the ability to compose them with
each other. This allows for variability and flexibility in the inputs you can
accept. Let's say you have flavors of ice cream 🍦 and you want to be able to
make a bowl 🥣 with strawberry 🍓 chocolate 🍫 and vanilla 🤍 you can add all
three together and give it a new name! 🍨

```go
type flavor uint8

const (
  Vanilla flavor = 1 << iota
  Chocolate
  Strawberry

  Neapolitan = Vanilla | Chocolate | Strawberry
)
```

If you're unsure about bit math check out the [operators
lesson](/basics/04-operators#bitwise-operators) but essentially bitwise OR `|`
is used for addition and bitwise AND `&` for subtraction, ❗ this will be
important very soon.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### enum.go

```go
//go:generate go run golang.org/x/tools/cmd/stringer -type=Direction -trimprefix=Direction
type Direction uint8

const (
	DirectionNorth     Direction = 1 << iota // 1 << 0 == 0b00000001 or 1
	DirectionNorthWest                       // 1 << 1 == 0b00000010 or 2
	DirectionWest                            // 1 << 2 == 0b00000100 or 4
	DirectionSouthWest                       // 1 << 3 == 0b00001000 or 8
	DirectionSouth                           // 1 << 4 == 0b00010000 or 16
	DirectionSouthEast                       // 1 << 5 == 0b00100000 or 32
	DirectionEast                            // 1 << 6 == 0b01000000 or 64
	DirectionNorthEast                       // 1 << 7 == 0b10000000 or 128

	DirectionUnknown Direction = 0
)
```

#### example_test.go

```go
func ExampleDirection() {
	type compass struct{ d enum.Direction }
	c := compass{d: enum.DirectionNorth}
	rotate := func(turnLeft bool) {
		if turnLeft {
			c.d = enum.Direction(bits.RotateLeft8(uint8(c.d), 1))
		} else {
			c.d = enum.Direction(bits.RotateLeft8(uint8(c.d), -1))
		}
		fmt.Println("Compass now facing:", c.d)
	}
	for i := 0; i < 8; i++ {
		rotate(true)
	}
	// Output:
	// Compass now facing: NorthWest
	// Compass now facing: West
	// Compass now facing: SouthWest
	// Compass now facing: South
	// Compass now facing: SouthEast
	// Compass now facing: East
	// Compass now facing: NorthEast
	// Compass now facing: North
}
```

## The Ultimate Enum

In today's world 🌏 it's very hard to find applications that _just_ use enums
internally, meaning we will often find ourselves outsourcing the enum value in
some format. That could be:

- **JSON** from one REST API to another
- **GraphQL** to serve a client specific data
- **SQL** to store into a database (PostgreSQL 🐘 for example)
- **YAML** in order to read from or write to a configuration file
- **Text** 📜 for a file your most basic needs

Or any other format you might think of. There are plenty of these and just
having `stringer` isn't going to cut it for all of these, but **fear not!**
There is a forked version of `stringer` by the name of `enumer` .... The name's
pretty self explanatory 😂 It does what `stringer` does already, but also
generates other helper methods that make our lives easier 🏖  I'm not going to
go through _everything_ that `enumer` provides, but give it a look over
yourself to see 👍

And if you wanted this on your system 💻 like I have it on mine you need to run
`go install github.com/dmarkham/enumer@latest` and why not go give give Dan
Markham some love ❤️ and [star ⭐ the repo](https://github.com/dmarkham/enumer)
and don't forget to `go generate` after writing the code.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### enum.go

```go
//go:generate go run github.com/dmarkham/enumer -type=Day -trimprefix=Day -json -text -yaml -sql -gqlgen
type Day uint8

const (
	DaySunday    Day = 1 << iota // 1 << 0 == 0b00000001 or 1
	DayMonday                    // 1 << 1 == 0b00000010 or 2
	DayTuesday                   // 1 << 2 == 0b00000100 or 4
	DayWednesday                 // 1 << 3 == 0b00001000 or 8
	DayThursday                  // 1 << 5 == 0b00010000 or 16
	DayFriday                    // 1 << 5 == 0b00100000 or 32
	DaySaturday                  // 1 << 6 == 0b01000000 or 64

	DayUnknown Day = 0
	// DayAll is a mask that adds up all of the days -- 0b01111111 or 127
	DayAll = DayMonday | DayTuesday | DayWednesday | DayThursday |
		DayFriday | DaySaturday | DaySunday
	// DayWeekdays Mask for weekdays -- 0b00111110 or 62
	DayWeekdays = DayMonday | DayTuesday | DayWednesday | DayThursday | DayFriday
	// DayWeekend Mask for weekend -- 0b01000001 or 65
	DayWeekend = DaySaturday | DaySunday
)
```

#### example_test.go

```go
func ExampleDay() {
	for d := enum.DaySunday; d <= enum.DaySaturday; d <<= 1 {
		switch {
		case d&enum.DayWeekend != 0:
			fmt.Println(d, "-- IT'S TIME TO PARTYYY!!")
		case d&enum.DayWeekdays != 0:
			fmt.Println(d, "-- Time for work...")
		}
	}
	// Output:
	// Sunday -- IT'S TIME TO PARTYYY!!
	// Monday -- Time for work...
	// Tuesday -- Time for work...
	// Wednesday -- Time for work...
	// Thursday -- Time for work...
	// Friday -- Time for work...
	// Saturday -- IT'S TIME TO PARTYYY!!
}

func ExampleDay_second() {
	for i := enum.Day(0); i < enum.DayAll; i++ {
		switch enum.DayAll & i {
		case enum.DayMonday, enum.DayWednesday, enum.DayFriday:
			fmt.Println("Time to workout 💪 it's", i)
		case enum.DayTuesday, enum.DayThursday:
			fmt.Println("Time to rest 🛌💤 on", i)
			// XXX(jay): If you uncomment this you're going to get 127 lines of
			// output... You've been warned -- but it will show why we mask.
			// default:
			// 	fmt.Println("Not a true `enum.Day`", i)
		}
	}
	// Output:
	// Time to workout 💪 it's Monday
	// Time to rest 🛌💤 on Tuesday
	// Time to workout 💪 it's Wednesday
	// Time to rest 🛌💤 on Thursday
	// Time to workout 💪 it's Friday
}
```

##### `enumer` methods

These are _some_ of the methods provided to us by `enumer`, if you want the
documentation on what these methods do, visit the `day_enumer.go` file
generated by `enumer` after running `go generate`

```go
func ExampleDayString() {
	d, err := enum.DayString("Wednesday")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("We got the day: %d -- %q\n", d, d)
	}
	d, err = enum.DayString("wednesday")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("We got the day: %d -- %q\n", d, d)
	}
	d, err = enum.DayString("chewsday")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("We got the day: %d -- %q\n", d, d)
	}
	// Output:
	// We got the day: 8 -- "Wednesday"
	// We got the day: 8 -- "Wednesday"
	// chewsday does not belong to Day values
}

func ExampleDayValues() {
	for _, d := range enum.DayValues() {
		fmt.Println("The day is", uint8(d), d)
	}
	// Output:
	// The day is 0 Unknown
	// The day is 1 Sunday
	// The day is 2 Monday
	// The day is 4 Tuesday
	// The day is 8 Wednesday
	// The day is 16 Thursday
	// The day is 32 Friday
	// The day is 64 Saturday
}

func ExampleDayStrings() {
	for _, d := range enum.DayStrings() {
		fmt.Printf("Only the string %q\n", d)
	}
	// Output:
	// Only the string "Unknown"
	// Only the string "Sunday"
	// Only the string "Monday"
	// Only the string "Tuesday"
	// Only the string "Wednesday"
	// Only the string "Thursday"
	// Only the string "Friday"
	// Only the string "Saturday"
}

func ExampleDay_IsADay() {
	notADay := enum.Day(0)
	if notADay.IsADay() && notADay != enum.DayUnknown {
		fmt.Println("Well I'll be a monkey's uncle 🐒")
	}
	notADay = enum.Day(255)
	if notADay.IsADay() {
		fmt.Println("Well I'll be a monkey's uncle 🐒")
	}
	notADay = enum.DayWeekend
	if notADay.IsADay() {
		fmt.Println("Still won't work, it's not a Day, it's a mask for them.")
	}
	notADay = enum.DayFriday
	if notADay.IsADay() {
		fmt.Println("We validated that", notADay, "is finally, truly, a Day.")
	}
	// Output:
	// We validated that Friday is finally, truly, a Day.
}

func ExampleDay_MarshalJSON() {
	// NOTE(jay): In order to understand why this works we need to understand
	// about the `json` package. In order to `Marshal` types, they must satisfy
	// the `json.Marshaler` interface, which has one method (like any good
	// interface) `MarshalJSON`. The `Marshal` method calls each values
	// `MarshalJSON` method if it has one -- `v.MarshalJSON`. Check out
	// [enum.Day.MarshalJSON] for it's implementation.
	type routine struct {
		Sets      uint8    `json:"sets"`
		Reps      uint8    `json:"reps"`
		Exercises []string `json:"exercises"`
		Day       enum.Day `json:"day"`
	}
	r, err := json.Marshal(routine{
		Sets:      3,
		Reps:      15,
		Exercises: []string{"squats", "calf raises", "leg curls", "hip thrusters"},
		Day:       enum.DayTuesday,
	})
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(r))
	// Output:
	// {"sets":3,"reps":15,"exercises":["squats","calf raises","leg curls","hip thrusters"],"day":"Tuesday"}
}

func ExampleDay_UnmarshalJSON() {
	// This mimics some JSON Object we get back from an API or a file.
	resp := `{"sets":5,"reps":21,"exercises":["squats","lunges","leg curls","hip thrusters"],"day":"Thursday"}`
	type routine struct {
		Sets      uint8    `json:"sets"`
		Reps      uint8    `json:"reps"`
		Exercises []string `json:"exercises"`
		Day       enum.Day `json:"day"`
	}
	r := routine{}
	if err := json.Unmarshal([]byte(resp), &r); err != nil {
		log.Fatal(err)
	}
	fmt.Printf("%+v\nday isn't a string: %d\n", r, r.Day)
	// Output:
	// {Sets:5 Reps:21 Exercises:[squats lunges leg curls hip thrusters] Day:Thursday}
	// day isn't a string: 16
}
```

## Odd forms 🤪 from newcomers

To end the lesson we're going to look at ways _not_ to declare an enum in Go.
As we've seen 👁  there is no keyword for enum in Go, but there is definitely a
bad way of trying to make our own enums in Go. We need to remember 🧠 an enum
is powerful because it is a constrained list that can be **ENUMERATED** or
listed out. If you **cannot** enumerate your enum ... It's not an enum 😂 I
feel like that goes without saying, but let's be clear as possible.

You may be tempted to create a `const` block of values and call that an enum,
but again, Go doesn't actually have enums and even if it did you would need a
way to enumerate those values you have. If you can't do that, it's not an enum.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### enum.go

##### Block of strings

This solves the 💬 "I don't want to make a `String()` method" problem, because
the values are already `string` type. Yes, they are `string` and that's
it. As we saw `stringer` already does this for us, but better. So if you don't
need `enumer` but want `String()` methods made for your enum, just use
`stringer`. Again, there is no way to enumerate these values, nor can we use
`stringer` or `enumer` on these **and** it's a `string` not a `uint8` meaning
in comparison it's inefficient 👎

```go
type StrWeekday string

// Look at how we have to repeat the type over and over and over and over....
const (
	StrMonday    StrWeekday = "monday"
	StrTuesday   StrWeekday = "tuesday"
	StrWednesday StrWeekday = "wednesday"
	StrThursday  StrWeekday = "thursday"
	StrFriday    StrWeekday = "friday"
	StrSaturday  StrWeekday = "saturday"
	StrSunday    StrWeekday = "sunday"
)
```

##### "Unchangeable" enum

This tries to solve the problem that we can do `enum.Day(33)` which means we
can pass in a bad value for `Day` instead of using the supplied enum values
`DayMonday,DayTuesday...`, but as we saw with `enumer` we get `IsADay()` method
which will check for us if the value is _actually_ a `Day`. So with this we
cannot modify the inside `slug` because it's not exported and therefore our
enum is "unchangeable", but again there is no way to enumerate this and
**nothing** is stopping us from changing the `var` values.... We _still_ have
changeable values, which goes against being an enum (Constant values); Making
the code more complex, less robust, less readable, not idiomatic, and
impossible to use `enumer` on 👎

```go
type StructWeekday struct{ slug string }

func (w StructWeekday) String() string { return w.slug }

// **NONE** of these are constant, they can all change **AND** look at all of
// the repetition of `StructWeekday{...}`
var (
	StructMonday    = StructWeekday{"monday"}
	StructTuesday   = StructWeekday{"tuesday"}
	StructWednesday = StructWeekday{"wednesday"}
	StructThursday  = StructWeekday{"thursday"}
	StructFriday    = StructWeekday{"friday"}
	StructSaturday  = StructWeekday{"saturday"}
	StructSunday    = StructWeekday{"sunday"}
)
```

#### example_test.go

##### Block of strings

```go
func ExampleStrWeekday() {
	// XXX(jay): We cannot manipulate any `StrWeekday` like we did in the
	// previous example **AND** we can make inappropriate values....
	fmt.Println(enum.StrWeekday("This isn't a weekday"))

	// No way to **ENUMERATE** our Enum now.... Have to go through every single
	// one.
	fmt.Println("Tedious and not enumerable hence why it's not an enum --",
		enum.StrMonday)
	fmt.Println("Tedious and not enumerable hence why it's not an enum --",
		enum.StrTuesday)
	// ... Till the end because I'm not typing all of this
	fmt.Println("Tedious and not enumerable hence why it's not an enum --",
		enum.StrSaturday)
	// Output:
	// This isn't a weekday
	// Tedious and not enumerable hence why it's not an enum -- monday
	// Tedious and not enumerable hence why it's not an enum -- tuesday
	// Tedious and not enumerable hence why it's not an enum -- saturday
}
```

##### "Unchangeable" enum

```go
func ExampleStructWeekday() {
	// XXX(jay): **All** `StructWeekday` values can be changed
	enum.StructMonday = enum.StructWednesday
	enum.StructTuesday = enum.StructWednesday
	enum.StructWednesday = enum.StructFriday
	enum.StructThursday = enum.StructSaturday
	enum.StructFriday = enum.StructSaturday
	enum.StructSaturday = enum.StructSunday
	enum.StructSunday = enum.StructWeekday{}
	// No way to **ENUMERATE** our Enum now.... Have to go through every single
	// one.
	fmt.Println("Tedious and not enumerable hence why it's not an enum --",
		enum.StructMonday)
	fmt.Println("Tedious and not enumerable hence why it's not an enum --",
		enum.StructTuesday)
	// ... Till the end because I'm not typing all of this
	fmt.Println("Tedious and not enumerable hence why it's not an enum --",
		enum.StructSaturday)
	// Output:
	// Tedious and not enumerable hence why it's not an enum -- wednesday
	// Tedious and not enumerable hence why it's not an enum -- wednesday
	// Tedious and not enumerable hence why it's not an enum -- sunday
}
```

## Source File 📄

[The Source File](https://goplay.tools/snippet/0hcceqy2I_c)

## Test File 📝

[The Test File](https://goplay.tools/snippet/gIL4FkUs-NG)
