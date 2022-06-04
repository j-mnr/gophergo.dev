---
Lesson: 17
Title: "Interface"
Draft: false
---

The word interface sounds intimidating 😰 and some explanations can be more
harmful 🤕 than helpful. Even worse there are plenty of definitions of
interface in software engineering and in programming languages😩 **but** we're
not going to look at a rigorous definition of what interface means. Nor are we
going to cover how other languages define their interface. We're _Go_-ing 😹 to
keep it as simple and easy as possible.

Maybe knowing the definition of interface will help? 🤔

> An interface is a shared boundary across which two or more separate
> components of a system exchange information.

... Maybe not 😂 Well, the definition is accurate, but let's break it down a
little further. When it comes to Go an `interface` is something that **defines
behavior**. We can pass an `interface` to a function just like we do any other
`type`, _but_ here's the important part, how many types can we pass in for that
interface? 🤔 Well since you define behavior and **not** a concrete type, you
can pass infinitely many other types 🤯 as long as they fulfill the behavior.

This is where the "two or more separate components of a system" comes in. We
can also say "two 2️⃣ or more ➕ separate types for a function parameter".
It is with this power that makes an `interface` so strong 💪

## Setup

Let's make our directory `interfaces` and the files we want inside of that directory
`example_test.go` `interfaces.go`

```sh
mkdir interfaces
touch interfaces/example_test.go interfaces/interfaces.go
```

Now let's open up `interfaces.go` and for the very first line we'll add
```go
package interfaces
```
Next for `example_test.go` for the very first line we'll add
```go
package interfaces_test
```

## Interface Basics

An `interface` defines behavior. As long as you fulfill the desired behavior
you implement the interface.

Are you a runner? 🏃 Then you satisfy the

```go
type Runner interface{ Run(track string) (hours float32) }
```
Do you play video games? 🎮 Then you satisfy the

```go
type Gamer interface{ Game(title string) (hours float32) }
```
Do you rock climb? 🧗 Then you satisfy the

```go
type Climber interface{ Climb(grade uint8) (reachedTop bool) }
```
Do you play an instrument? 🎸 🎺 🥁 🎷 🎻 🪕 🎹 Then you satisfy the

```go
type Musician interface{ Play(melody string) (sounds string) }
```

And that's a Go `interface` in a nutshell; know that just like a `struct` there
is no best way to define an `interface`. It's used for what you need it to be.
So we could make an even broader `interface` for the `Runner` and `Climber` up
above 👆 for anyone who does any type of physical activity.🥊🤺 ⛷️ 🏊 🎾 🏒 🧘

```go
type Exerciser interface{ Exercise(activity string) (hours float32) }
```

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### interfaces.go

```go
// Walker is the interface that wraps the basic Walk method.
type Walker interface{ Walk() }

// Swimmer is the interface that wraps the basic Swim method.
type Swimmer interface{ Swim() }

// Flyer is the interface that wraps the basic Fly method.
type Flyer interface{ Fly() }
```


#### example_test.go

There is no good way to test a pure definition of a behavior (aka an
`interface`) 🤷 There's no logic. But! We can at least see how we can tell if
our `struct` satisfies the `interface`. You may come across this `_ =
SomeInterface(SomeStruct{})` in a codebase and be thoroughly confused. The
developer is checking that the `SomeStruct` implements the `SomeInterface`, if
it doesn't the program won't compile 😯

```go
type Seal struct{}

func (s Seal) Swim() { fmt.Println("Swims up to the surface. Arr! Arr! 🦭") }

func ExampleWalker() {
	t := interfaces.Walker(interfaces.Duck{})
	// NOTE(jay): cannot convert (Seal literal) (value of type Seal) to
	// interfaces.Walker (Seal does not implement interfaces.Walker
	// (missing method Walk))
	// _ = interfaces.Walker(Seal{})
	fmt.Printf("%#v", t)
	// Output: interfaces.Duck{IsFlying:false}
}
```

## Embedding Interfaces

There will be a dedicated lesson on embedding (coming soon!) but this will
serve to supplement it. When we make interfaces we keep them small, **do not**
bloat your `interface`. What I mean is how easy is it to implement this
interface?

```go
type Printer interface{ Print(size int) }

type HPPrinter struct{ HasPaper, HasInk bool }

func (p HPPrinter) Print(size int) {
	if !p.HasPaper || !p.HasInk {
		return
	}
	fmt.Printf("PRINTING CONTENTS TO SIZE %d\n", size)
	// Do more stuff
}
```

compared to this one 😑

```go
type ReallyCoolPrinter interface {
	Print(size int)
	AddressBook(user string) []string
	Lock(pass string) bool
	Wifi(signal int)
	Call(number string)
	Fax(number string)
	Bluetooth(signal int)
}

// ... Nope.
```

So _if_ you need to make an `interface` which isn't very often, but _if_ you
do, then 1 or 2 behaviors is enough. You may think "What if I want an
`interface` to have all the things though?" Well you can still do that
**super** easy in Go because of **Composition**. It's really easy to compose
interfaces together with embedding, so let's _Go_ 😹 check it out.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### interfaces.go

```go
// WalkSwimmer is the interface that groups the basic Walk and Swim methods.
type WalkSwimmer interface {
	Walker
	Swimmer
}

// WalkSwimmerFlyer is the interface that groups the basic Walk, Swim and Fly
// methods.
type WalkSwimFlyer interface {
	Walker
	Swimmer
	Flyer
}
```

#### example_test.go

```go
func ExampleWalkSwimmer() {
	t := interfaces.WalkSwimmer(interfaces.Person(0))
	// NOTE(jay): cannot convert (Seal literal) (value of type Seal) to
	// interfaces.WalkSwimmer (Seal does not implement interfaces.WalkSwimmer
	// (missing method Walk))
	// _ = interfaces.WalkSwimmer(Seal{})
	fmt.Printf("%#v", t)
	// Output: 0x0
}
```

## Implement Interface

Alright! We've made our interfaces now it's time to implement them! 🦾 To
implement an `interface` means to have a `type` that satisfies the behavior
of that interface. To do that means make a method with the same signature of
the function in the `interface`.

In Go there is no explicit keyword that forces you to implement an interface.
All interfaces that a `type` implements are _implicit_. This is a **super**
🦸 powerful feature of Go. It means any `type` can implement any amount of
interfaces solely by having the defined behaviors bound to it. **This is more
of a reason to keep your interfaces short and sweet.** You will never have to
do this 👇🤢

```go
type Gross struct implements Walker, Talker, Stalker, Rocker, Mocker, Jocker,
Blocker, Gawker, Bocker, Knocker, Locker, Hawker {
	This👆LooksStupid       string
	AndIsHugelyUnproductive uint32
}
```

It will only ever be:

```go
type MyStruct struct {
	ImportantValue           uint16
	ClientWantsThis          string
	OtherValueWorthLookingAt []SomeStruct

	privateMapForInternals map[token]string
}

// Create the methods that satisfy the interfaces you want down below. You can
// always mix and match, add and remove, the world is your oyster 🦪
func (s MyStruct) Walk() { ... }
func (s MyStruct) Talk() { ... }
func (s MyStruct) Rock() { ... }
```

Or even better _types_ can satisfy interfaces:

```go
type Guarder interface{ Guard() (os.File, error) }

type SecurityLevel uint8

// Guard will make sure that only certain clearances of security levels can
// access certain documents. An error is thrown if the file cannot be found or
// the SecurityLevel does not have the clearance to view that file.
func (sl SecurityLevel) Guard() (os.File, error) { ... }
```

Absolutely Gorgeous, isn't it? 🤩 I can't stress it enough, Go supports
**Composition**. Therefore use the language to its fullest and **compose** your
`type` to satisfy multiple interfaces. Let's see how we would do that 😁

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### interfaces.go

```go
// Person is a new type and it can satisfy all of our interfaces, the type system
// in Go is very powerful for features like this.
type Person uint8

func (p Person) Walk() { fmt.Println("I'm walking, 🚶 yes indeed!") }
func (p Person) Swim() { fmt.Println("Splish Splash 🌊") }
func (p Person) Fly()  { fmt.Println("Time for takeoff 🛫") }

// Duck is a struct type that satisfies all of our interfaces as well. We can
// see it has some extra information on whether or not it is flying.
type Duck struct{ IsFlying bool }

func (d Duck) Walk() { fmt.Println("The duck 🦆 waddles forward.") }
func (d Duck) Swim() { fmt.Println("The duck 🦆 paddles around.") }
func (d Duck) Fly()  { fmt.Println("The duck 🦆 flies up.") }

// GoForWalk is an example of using an interface to satisfy a condition we want
// our parameters (person and duck) to have, without forcing someone to have
// exact implementations. So they could put in anything that can walk.
func GoForWalk(person Walker, duck Walker) {
	fmt.Println("It was looking like a great day outside. ☀️")
	fmt.Println("Two very different types decided to go for a walk.")
	person.Walk()
	duck.Walk()
	fmt.Println("They ran into each other and locked eyes 👀. What will happen now?")
	person.Walk()
	duck.Walk()
	fmt.Println("Looks like they decided to continue their walk together! 😄")
}
```

#### example_test.go

```go
func ExampleGoForWalk() {
	m := interfaces.Person(0)
	d := interfaces.Duck{}
	interfaces.GoForWalk(m, d)
	// Output:
	// It was looking like a great day outside. ☀️
	// Two very different types decided to go for a walk.
	// I'm walking, 🚶 yes indeed!
	// The duck 🦆 waddles forward.
	// They ran into each other and locked eyes 👀. What will happen now?
	// I'm walking, 🚶 yes indeed!
	// The duck 🦆 waddles forward.
	// Looks like they decided to continue their walk together! 😄
}
```

## Interface Limitations For Parameter

If you're new to programming it may confuse you 😕 if you pass in a duck🦆 to a
function that accepts a `Swimmer` and you can't call `duck.Fly()` in the
function. It makes sense that you **cannot** do that, however. If you pass in a
`Swimmer` all that `type` is, is a `type` that satisfies the `Swim()` method
and that's it. The function knows _nothing_ else about your `type` only that it
can swim.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### interfaces.go

```go
// SoarIntoTheClouds will take the Flyer and put them sky high into the clouds.
func SoarIntoTheClouds(f Flyer) {
	fmt.Println("The clouds ☁️ look so good today!")
	f.Fly()
 	// NOTE(jay): Notice we **cannot** call the other duck or person methods
	//  (type Flyer has no field or method Walk/Swim)
	// f.Walk()
	// f.Swim()
	fmt.Println("Feels good to be on Cloud Nine. 😎")
}
```

#### example_test.go

```go
func ExampleSoarIntoTheClouds() {
	interfaces.SoarIntoTheClouds(interfaces.Duck{})
	// Output:
	// The clouds ☁️ look so good today!
	// The duck 🦆 flies up.
	// Feels good to be on Cloud Nine. 😎
}
```

## Remove Limitations With Type Cast

You may find yourself in a situation where the `type` you are putting in can be
anything, in Go this is the `interface{}` `type` or the easier to read `any`
`type`. When you have something like that it's nice to be able to turn it into
some type you can do your logic on, but we don't want to go casting `any`-thing
to random types without first checking, this is where
[`switch`](/basics/06-switch) comes to the rescue 🦸

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### interfaces.go

```go
// InterfacesToConcreteType shows us how we would turn an interface that only
// knows the Swim method into its concrete type, which allows us to gain access
// to that type's other methods and fields.
func InterfacesToConcreteType(s Swimmer) {
	// NOTE(jay): s.isFlying undefined (type Swimmer has no field or method isFlying)
	// This shows us that even if Duck had more methods or had any fields we only
	// can use what is satisfied by the interface.
	// s.isFlying
	d, ok := s.(Duck)
	switch ok {
	case true:
		fmt.Println("Looks like this is a Duck! 🦆")
		if d.IsFlying {
			fmt.Println("And it is flying.")
		} else {
			fmt.Println("And it isn't flying.")
		}
	case false:
		fmt.Println("This isn't any type of Duck I've ever seen....")
	}
	// If we are unsure of the type we can use a switch type statement from a
	// previous lesson. Very useful for JSON responses.
	switch t := s.(type) {
	case WalkSwimFlyer:
		t.Fly()
		t.Walk()
		t.Swim()
	case Duck:
		if !t.IsFlying {
			t.IsFlying = true
		}
		t.Fly()
	default:
		fmt.Printf("This type %T doesn't have a mapping in the switch\n", t)
	}
}
```

#### example_test.go

```go
func ExampleInterfacesToConcreteType() {
	fmt.Println("Put in Duck")
	interfaces.InterfacesToConcreteType(interfaces.Duck{IsFlying: true})
	fmt.Println("Put in Person")
	interfaces.InterfacesToConcreteType(interfaces.Person(0))
	fmt.Println("Put in our own Swimmer (Seal)")
	interfaces.InterfacesToConcreteType(Seal{})
	// Output:
	// Put in Duck
	// Looks like this is a Duck! 🦆
	// And it is flying.
	// The duck 🦆 flies up.
	// The duck 🦆 waddles forward.
	// The duck 🦆 paddles around.
	// Put in Person
	// This isn't any type of Duck I've ever seen....
	// Time for takeoff 🛫
	// I'm walking, 🚶 yes indeed!
	// Splish Splash 🌊
	// Put in our own Swimmer (Seal)
	// This isn't any type of Duck I've ever seen....
	// This type interfaces_test.Seal doesn't have a mapping in the switch
}
```

## Using Embedded Interfaces

Remember how easy it was to **compose** our interfaces? Well now we can use any
combination of them to make up any function we like. We can even add ➕ more if
we wanted, this is the power of **composition**

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png)

#### interfaces.go

```go
// VisitWaterPark takes in a type that can both walk and swim **and** a type
// that can walk, swim, and fly. It uses all of these methods inside of the
// function and **cannot** use anymore methods than the declared one for that
// type.
func VisitWaterPark(ws WalkSwimmer, wsf WalkSwimFlyer) {
	fmt.Println("Two very different types decided to go to a water park.")
	ws.Walk()
	wsf.Walk()
	fmt.Println("They both make it in and find a pool to dive into.")
	wsf.Swim()
	ws.Swim()
	fmt.Printf("Uh oh, looks like %T didn't like that!\n", wsf)
	wsf.Fly()
}
```

#### example_test.go

```go
func ExampleVisitWaterPark() {
	interfaces.VisitWaterPark(interfaces.Person(0), interfaces.Duck{})
	// Output:
	// Two very different types decided to go to a water park.
	// I'm walking, 🚶 yes indeed!
	// The duck 🦆 waddles forward.
	// They both make it in and find a pool to dive into.
	// The duck 🦆 paddles around.
	// Splish Splash 🌊
	// Uh oh, looks like interfaces.Duck didn't like that!
	// The duck 🦆 flies up.
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png)

```go
package interfaces

import (
	"fmt"
)

// Walker is the interface that wraps the basic Walk method.
type Walker interface{ Walk() }

// Swimmer is the interface that wraps the basic Swim method.
type Swimmer interface{ Swim() }

// Flyer is the interface that wraps the basic Fly method.
type Flyer interface{ Fly() }

// WalkSwimmer is the interface that groups the basic Walk and Swim methods.
type WalkSwimmer interface {
	Walker
	Swimmer
}

// WalkSwimmerFlyer is the interface that groups the basic Walk, Swim and Fly
// methods.
type WalkSwimFlyer interface {
	Walker
	Swimmer
	Flyer
}

// Person is a new type and it can satisfy all of our interfaces, the type system
// in Go is very powerful for features like this.
type Person uint8

func (p Person) Walk() { fmt.Println("I'm walking, 🚶 yes indeed!") }
func (p Person) Swim() { fmt.Println("Splish Splash 🌊") }
func (p Person) Fly()  { fmt.Println("Time for takeoff 🛫") }

// Duck is a struct type that satisfies all of our interfaces as well. We can
// see it has some extra information on whether or not it is flying.
type Duck struct{ IsFlying bool }

func (d Duck) Walk() { fmt.Println("The duck 🦆 waddles forward.") }
func (d Duck) Swim() { fmt.Println("The duck 🦆 paddles around.") }
func (d Duck) Fly()  { fmt.Println("The duck 🦆 flies up.") }

// GoForWalk is an example of using an interface to satisfy a condition we want
// our parameters (person and duck) to have, without forcing someone to have
// exact implementations. So they could put in anything that can walk.
func GoForWalk(person Walker, duck Walker) {
	fmt.Println("It was looking like a great day outside. ☀️")
	fmt.Println("Two very different types decided to go for a walk.")
	person.Walk()
	duck.Walk()
	fmt.Println("They ran into each other and locked eyes 👀. What will happen now?")
	person.Walk()
	duck.Walk()
	fmt.Println("Looks like they decided to continue their walk together! 😄")
}

// SoarIntoTheClouds will take the Flyer and put them sky high into the clouds.
func SoarIntoTheClouds(f Flyer) {
	fmt.Println("The clouds ☁️ look so good today!")
	f.Fly()
 	// NOTE(jay): Notice we **cannot** call the other duck or person methods
	//  (type Flyer has no field or method Walk/Swim)
	// f.Walk()
	// f.Swim()
	fmt.Println("Feels good to be on Cloud Nine. 😎")
}

// InterfacesToConcreteType shows us how we would turn an interface that only
// knows the Swim method into its concrete type, which allows us to gain access
// to that type's other methods and fields.
func InterfacesToConcreteType(s Swimmer) {
	// NOTE(jay): s.isFlying undefined (type Swimmer has no field or method isFlying)
	// This shows us that even if Duck had more methods or had any fields we only
	// can use what is satisfied by the interface.
	// s.isFlying
	d, ok := s.(Duck)
	switch ok {
	case true:
		fmt.Println("Looks like this is a Duck! 🦆")
		if d.IsFlying {
			fmt.Println("And it is flying.")
		} else {
			fmt.Println("And it isn't flying.")
		}
	case false:
		fmt.Println("This isn't any type of Duck I've ever seen....")
	}
	// If we are unsure of the type we can use a switch type statement from a
	// previous lesson. Very useful for JSON responses.
	switch t := s.(type) {
	case WalkSwimFlyer:
		t.Fly()
		t.Walk()
		t.Swim()
	case Duck:
		if !t.IsFlying {
			t.IsFlying = true
		}
		t.Fly()
	default:
		fmt.Printf("This type %T doesn't have a mapping in the switch\n", t)
	}
}

// VisitWaterPark takes in a type that can both walk and swim **and** a type
// that can walk, swim, and fly. It uses all of these methods inside of the
// function and **cannot** use anymore methods than the declared one for that
// type.
func VisitWaterPark(ws WalkSwimmer, wsf WalkSwimFlyer) {
	fmt.Println("Two very different types decided to go to a water park.")
	ws.Walk()
	wsf.Walk()
	fmt.Println("They both make it in and find a pool to dive into.")
	wsf.Swim()
	ws.Swim()
	fmt.Printf("Uh oh, looks like %T didn't like that!\n", wsf)
	wsf.Fly()
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif)

```go
package interfaces_test

import (
	"basics/interfaces"
	"fmt"
)

type Seal struct{}

func (s Seal) Swim() { fmt.Println("Swims up to the surface. Arr! Arr! 🦭") }

func ExampleWalker() {
	t := interfaces.Walker(interfaces.Duck{})
	// NOTE(jay): cannot convert (Seal literal) (value of type Seal) to
	// interfaces.Walker (Seal does not implement interfaces.Walker
	// (missing method Walk))
	// _ = interfaces.Walker(Seal{})
	fmt.Printf("%#v", t)
	// Output: interfaces.Duck{IsFlying:false}
}

func ExampleWalkSwimmer() {
	t := interfaces.WalkSwimmer(interfaces.Person(0))
	// NOTE(jay): cannot convert (Seal literal) (value of type Seal) to
	// interfaces.WalkSwimmer (Seal does not implement interfaces.WalkSwimmer
	// (missing method Walk))
	// _ = interfaces.WalkSwimmer(Seal{})
	fmt.Printf("%#v", t)
	// Output: 0x0
}

func ExampleGoForWalk() {
	m := interfaces.Person(0)
	d := interfaces.Duck{}
	interfaces.GoForWalk(m, d)
	// Output:
	// It was looking like a great day outside. ☀️
	// Two very different types decided to go for a walk.
	// I'm walking, 🚶 yes indeed!
	// The duck 🦆 waddles forward.
	// They ran into each other and locked eyes 👀. What will happen now?
	// I'm walking, 🚶 yes indeed!
	// The duck 🦆 waddles forward.
	// Looks like they decided to continue their walk together! 😄
}

func ExampleSoarIntoTheClouds() {
	interfaces.SoarIntoTheClouds(interfaces.Duck{})
	// Output:
	// The clouds ☁️ look so good today!
	// The duck 🦆 flies up.
	// Feels good to be on Cloud Nine. 😎
}

func ExampleInterfacesToConcreteType() {
	fmt.Println("Put in Duck")
	interfaces.InterfacesToConcreteType(interfaces.Duck{IsFlying: true})
	fmt.Println("Put in Person")
	interfaces.InterfacesToConcreteType(interfaces.Person(0))
	fmt.Println("Put in our own Swimmer (Seal)")
	interfaces.InterfacesToConcreteType(Seal{})
	// Output:
	// Put in Duck
	// Looks like this is a Duck! 🦆
	// And it is flying.
	// The duck 🦆 flies up.
	// The duck 🦆 waddles forward.
	// The duck 🦆 paddles around.
	// Put in Person
	// This isn't any type of Duck I've ever seen....
	// Time for takeoff 🛫
	// I'm walking, 🚶 yes indeed!
	// Splish Splash 🌊
	// Put in our own Swimmer (Seal)
	// This isn't any type of Duck I've ever seen....
	// This type interfaces_test.Seal doesn't have a mapping in the switch
}

func ExampleVisitWaterPark() {
	interfaces.VisitWaterPark(interfaces.Person(0), interfaces.Duck{})
	// Output:
	// Two very different types decided to go to a water park.
	// I'm walking, 🚶 yes indeed!
	// The duck 🦆 waddles forward.
	// They both make it in and find a pool to dive into.
	// The duck 🦆 paddles around.
	// Splish Splash 🌊
	// Uh oh, looks like interfaces.Duck didn't like that!
	// The duck 🦆 flies up.
}
```
