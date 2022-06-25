---
Lesson: 18
Title: "Embed"
Draft: false
---

Embedding is what's done to diamonds 💎 when they are put in a ring 💍 You take
something and place it **deeply** within something else. So that it becomes a
part of it. Maybe you've embedded your phone case to your phone? 📱 I know I
have, the thing's impossible to get off!

## Setup

Let's make our directory `embed` and the files we want inside of that directory
`example_test.go` `embed.go`

```sh
mkdir embed
touch embed/example_test.go embed/embed.go
```

Now let's open up `embed.go` and for the very first line we'll add
```go
package embed
```
Next for `example_test.go` for the very first line we'll add
```go
package embed_test
```

## Embedding Structs

The code does a good job of showing _how_ to embed, but not _why_, so let's
understand why we would embed in the first place.

There are times when you want to add data to fields but don't want to touch the
original `struct`. Well in this case you can just wrap 🌯 that `struct` and put
new information with it's own methods **without needing to rewrite any code** 🥹
this is such a common strategy in software engineering it has a name {{<
anchor-new-window "decorator"
"https://refactoring.guru/design-patterns/decorator" >}}. Embedding allows for
reusable code that can be composed into a larger `struct`.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `embed.go`

```go
// Gopher is an example struct to show off embedding
type Gopher struct {
	Name     string
	Age      int
	IsCoding bool

	privateField string
}

// GopherV2 has a Gopher embedded inside of it and has some additional fields.
type GopherV2 struct {
	// We can make this private if we want like:
	// `gopher Gopher`
	// but that would remove the inner type promotion, meaning we can't do fun
	// and easy stuff like `myGopherV2.Name` 👍, we would have to do
	// `myGopherV2.gopher.Name` 👎
	Gopher

	Friends []string
	Ratings map[string]int
}

// GopherV3 has a GopherV2 which has a Gopher in it, so it is allowed to access
// all the fields and methods of both GopherV2 and Gopher along with it's own
// fields and methods.
type GopherV3 struct {
	GopherV2
	Badge rune
}

// Struct is an example function that shows how to initialize both
// versions of embedded structs and how to access the embedded fields.
func Struct() {
	gala := GopherV2{
		Gopher: Gopher{
			Name:         "Gala",
			Age:          24,
			IsCoding:     false,
			privateField: "Embedding doesn't change access modifiers"},
		Friends: []string{"Gabby", "Gael", "Garth", "Gazsi"},
		Ratings: map[string]int{"coffee": 3, "tea": 7, "chocolate": 9},
	}
	fmt.Printf("GopherV2: %#v\n", gala)
	fmt.Println()

	// Change our GopherV2
	gala.Name = "gopher"
	gala.Age = 26
	gala.IsCoding = true
	gala.privateField = "Can be changed because in same package."
	gala.Friends = append(gala.Friends, "Gandalf")
	gala.Ratings["garlic bread"] = 10
	fmt.Printf("changed GopherV2: %#v\n", gala)

	// This is here to show that you **always** need to have `field: field{}`
	// when making your struct inline, but don't need to when accessing fields
	// e.g. `g.name` works! Instead of `g.GopherV2.Gopher.name`
	g := GopherV3{
		GopherV2: GopherV2{
			Gopher: Gopher{
				Name:         "Ground",
				Age:          0xdead,
				IsCoding:     true,
				privateField: "Access granted"},
			Friends: []string{"Gunter"},
			Ratings: map[string]int{"embedding": 10},
		},
		Badge: '🤯',
	}
	fmt.Printf("Three layers of embedding %+v\n", g)
}
```

#### `example_test.go`

```go
func ExampleStruct() {
	embed.Struct()
	// Output:
	// GopherV2: embed.GopherV2{Gopher:embed.Gopher{Name:"Gala", Age:24, IsCoding:false, privateField:"Embedding doesn't change access modifiers"}, Friends:[]string{"Gabby", "Gael", "Garth", "Gazsi"}, Ratings:map[string]int{"chocolate":9, "coffee":3, "tea":7}}
	//
	// changed GopherV2: embed.GopherV2{Gopher:embed.Gopher{Name:"gopher", Age:26, IsCoding:true, privateField:"Can be changed because in same package."}, Friends:[]string{"Gabby", "Gael", "Garth", "Gazsi", "Gandalf"}, Ratings:map[string]int{"chocolate":9, "coffee":3, "garlic bread":10, "tea":7}}
	// Three layers of embedding {GopherV2:{Gopher:{Name:Ground Age:57005 IsCoding:true privateField:Access granted} Friends:[Gunter] Ratings:map[embedding:10]} Badge:129327}
}
```

## Embedding Has No Side Effects

This may be the first time you've ever seen embedding done and you may have the
question

> When I embed a `struct` does that _change_ anything about either of those
> structs? 🤔

It's a good question to have. 😁 👍 And the answer is nothing unexpected will
happen when you put a `struct` into another `struct`. You get all the benefits
of being able to rely on inner type promotion _and_ your `struct` is as you
would expect it to behave.

Let's dig a little more into that phrase "inner type promotion". When you embed
a `struct` the fields of that `struct` are promoted to the encompassing
`struct`. For example let's have a bike in a moped in a motorcycle. 🚲 -> 🛵 ->
🏍️ All bikes have wheels, but we don't need to say
`motorcycle.moped.bike.wheels` We can just say what would make sense,
`motorcycle.wheels` Likewise a moped can have a light on it 🔦 but we don't
need to say `motorcycle.moped.light` It's obvious that our motorcycle would be
a direct upgrade from the moped so we can promote that field `light` 🔦 up to
the motorcycle.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")
{.myclass}

#### `embed.go`

```go
// City is a struct showing structs with embedded fields doesn't change
// anything about the way we expect a struct to behave
type City struct{ Residents []GopherV3 }

// Deep shows that structs with embedded fields act no differently with
// the embedded fields.
func Deep() {
	c := City{
		Residents: []GopherV3{
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Gance",
						Age:          0b001010,
						IsCoding:     true,
						privateField: "Not accessible outside package."},
					Friends: []string{"Guzz"},
					Ratings: map[string]int{"space": 10},
				},
				Badge: '🚀',
			},
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Guuba",
						Age:          0o777,
						IsCoding:     false,
						privateField: "Can be accessed in package."},
					Friends: []string{"Ghorm", "Gokil"},
					Ratings: map[string]int{"death metal": 10},
				},
				Badge: '🌋',
			},
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Gerry",
						Age:          88,
						IsCoding:     true,
						privateField: "Put stuff here"},
					Friends: []string{"Gaqlyn", "Gicard", "Gosemary"},
					Ratings: map[string]int{"naps": 10},
				},
				Badge: '🎉',
			},
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Gustion",
						Age:          21,
						IsCoding:     true,
						privateField: "that you need to use"},
					Friends: []string{"Gidea", "Gno"},
					Ratings: map[string]int{"carbs": 10},
				},
				Badge: '🍢',
			},
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Guna",
						Age:          0b11111111,
						IsCoding:     false,
						privateField: "but other packages don't."},
					Friends: []string{"Gouda"},
					Ratings: map[string]int{"haircuts": 3},
				},
				Badge: '🌜',
			},
		},
	}
	fmt.Printf("%+v\n\n", c)
	for _, g := range c.Residents {
		// g.GopherV2.Gopher.privateField
		fmt.Printf("%s ", g.privateField)
	}
}
```

#### `example_test.go`

```go
func ExampleDeep() {
	embed.Deep()
	// Output:
	// {Residents:[{GopherV2:{Gopher:{Name:Gance Age:10 IsCoding:true privateField:Not accessible outside package.} Friends:[Guzz] Ratings:map[space:10]} Badge:128640} {GopherV2:{Gopher:{Name:Guuba Age:511 IsCoding:false privateField:Can be accessed in package.} Friends:[Ghorm Gokil] Ratings:map[death metal:10]} Badge:127755} {GopherV2:{Gopher:{Name:Gerry Age:88 IsCoding:true privateField:Put stuff here} Friends:[Gaqlyn Gicard Gosemary] Ratings:map[naps:10]} Badge:127881} {GopherV2:{Gopher:{Name:Gustion Age:21 IsCoding:true privateField:that you need to use} Friends:[Gidea Gno] Ratings:map[carbs:10]} Badge:127842} {GopherV2:{Gopher:{Name:Guna Age:255 IsCoding:false privateField:but other packages don't.} Friends:[Gouda] Ratings:map[haircuts:3]} Badge:127772}]}
	//
	// Not accessible outside package. Can be accessed in package. Put stuff here that you need to use but other packages don't.
}
```

## Embedding For Interface

This is where embedding gets _**really powerful**_. That's because you'll find
yourself needing to satisfy an `interface` from time to time in the standard
library or from other libraries in general. Maybe you don't want to create the
needed methods yourself. Well if you embed a `struct` that already satisfies
that `interface`, you now satisfy that `interface` 😏 It's by far one of the
coolest 🧊 things that comes with Go's composition and embedding features.
Need an `io.Reader`? Three separate ways to satisfy the interface

```go
type MyStructV1 struct{ io.Reader }
type MyStructV2 struct{ strings.Reader }
type MyStructV3 struct{ *os.File }
```

These aren't meant to be useful examples but open your eyes 👀 to the ability
to compose structs into exactly what you need very quickly with smaller
pre-built components.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `embed.go`

```go
type Human interface{ Greet() string }

type Bear interface{ Growl() string }

type Pig interface{ MudBath(minutes int) }

// HumanBearPig is the thing of nightmares.
type HumanBearPig interface {
	Human // half human
	Bear  // half bear
	Pig   // half pig
}

func (g Gopher) Greet() string { return "Hi-dilly-ho, neighborinos!" }
func (g Gopher) Growl() string { return "Rawr XD" }
func (g Gopher) MudBath(m int) { fmt.Printf("Rolled around for %d mins", m) }

func (g GopherV2) Growl() string { return "GOPHERV2 GRRRWAUGH!!" }
func (g GopherV2) MudBath(m int) { fmt.Printf("V2:Sat in mud for %d mins", m) }

func (g GopherV3) Greet() string { return "GOPHERV3 SAYS WHADDUP!!" }

// Interface shows that we need a struct that will satisfy the entire
// interface, but we don't care if that struct or the embedded structs inside
// of it satisfy the interface.
func Interface(hbp HumanBearPig) {
	fmt.Println("It was a dark and rainy night. The moon 🌕 felt so bright.")
	fmt.Println("A strange passerby. Avert my gaze, I try.")
	fmt.Println(hbp.Greet())
	fmt.Println("The person yelled to me; suddenly something broke free.")
	fmt.Println(hbp.Growl())
	fmt.Println("I ran as fast I could, through the streets to the forests of wood.")
	fmt.Println("A final leap over some mud. The thing ceased with a great thud.")
	hbp.MudBath(20)
}
```

#### `example_test.go`

```go
func ExampleInterface() {
	embed.Interface(embed.Gopher{})
	// Output:
	// It was a dark and rainy night. The moon 🌕 felt so bright.
	// A strange passerby. Avert my gaze, I try.
	// Hi-dilly-ho, neighborinos!
	// The person yelled to me; suddenly something broke free.
	// Rawr XD
	// I ran as fast I could, through the streets to the forests of wood.
	// A final leap over some mud. The thing ceased with a great thud.
	// Rolled around for 20 mins
}

func ExampleInterface_v2() {
	embed.Interface(embed.GopherV2{})
	// Output:
	// It was a dark and rainy night. The moon 🌕 felt so bright.
	// A strange passerby. Avert my gaze, I try.
	// Hi-dilly-ho, neighborinos!
	// The person yelled to me; suddenly something broke free.
	// GOPHERV2 GRRRWAUGH!!
	// I ran as fast I could, through the streets to the forests of wood.
	// A final leap over some mud. The thing ceased with a great thud.
	// V2:Sat in mud for 20 mins
}

func ExampleInterface_v3() {
	embed.Interface(embed.GopherV3{})
	// Output:
	// It was a dark and rainy night. The moon 🌕 felt so bright.
	// A strange passerby. Avert my gaze, I try.
	// GOPHERV3 SAYS WHADDUP!!
	// The person yelled to me; suddenly something broke free.
	// GOPHERV2 GRRRWAUGH!!
	// I ran as fast I could, through the streets to the forests of wood.
	// A final leap over some mud. The thing ceased with a great thud.
	// V2:Sat in mud for 20 mins
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png "gopher-victorious by Egon Elbre")

```go
package embed

import "fmt"

// Gopher is an example struct to show off embedding
type Gopher struct {
	Name     string
	Age      int
	IsCoding bool

	privateField string
}

// GopherV2 has a Gopher embedded inside of it and has some additional fields.
type GopherV2 struct {
	// We can make this private if we want like:
	// `gopher Gopher`
	// but that would remove the inner type promotion, meaning we can't do fun
	// and easy stuff like `myGopherV2.Name` 👍, we would have to do
	// `myGopherV2.gopher.Name` 👎
	Gopher

	Friends []string
	Ratings map[string]int
}

// GopherV3 has a GopherV2 which has a Gopher in it, so it is allowed to access
// all the fields and methods of both GopherV2 and Gopher along with it's own
// fields and methods.
type GopherV3 struct {
	GopherV2
	Badge rune
}

// Struct is an example function that shows how to initialize both
// versions of embedded structs and how to access the embedded fields.
func Struct() {
	gala := GopherV2{
		Gopher: Gopher{
			Name:         "Gala",
			Age:          24,
			IsCoding:     false,
			privateField: "Embedding doesn't change access modifiers"},
		Friends: []string{"Gabby", "Gael", "Garth", "Gazsi"},
		Ratings: map[string]int{"coffee": 3, "tea": 7, "chocolate": 9},
	}
	fmt.Printf("GopherV2: %#v\n", gala)
	fmt.Println()

	// Change our GopherV2
	gala.Name = "gopher"
	gala.Age = 26
	gala.IsCoding = true
	gala.privateField = "Can be changed because in same package."
	gala.Friends = append(gala.Friends, "Gandalf")
	gala.Ratings["garlic bread"] = 10
	fmt.Printf("changed GopherV2: %#v\n", gala)

	// This is here to show that you **always** need to have `field: field{}`
	// when making your struct inline, but don't need to when accessing fields
	// e.g. `g.name` works! Instead of `g.GopherV2.Gopher.name`
	g := GopherV3{
		GopherV2: GopherV2{
			Gopher: Gopher{
				Name:         "Ground",
				Age:          0xdead,
				IsCoding:     true,
				privateField: "Access granted"},
			Friends: []string{"Gunter"},
			Ratings: map[string]int{"embedding": 10},
		},
		Badge: '🤯',
	}
	fmt.Printf("Three layers of embedding %+v\n", g)
}

// City is a struct showing structs with embedded fields doesn't change
// anything about the way we expect a struct to behave
type City struct{ Residents []GopherV3 }

// City shows that structs with embedded fields act no differently with
// the embedded fields.
func City() {
	c := City{
		Residents: []GopherV3{
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Gance",
						Age:          0b001010,
						IsCoding:     true,
						privateField: "Not accessible outside package."},
					Friends: []string{"Guzz"},
					Ratings: map[string]int{"space": 10},
				},
				Badge: '🚀',
			},
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Guuba",
						Age:          0o777,
						IsCoding:     false,
						privateField: "Can be accessed in package."},
					Friends: []string{"Ghorm", "Gokil"},
					Ratings: map[string]int{"death metal": 10},
				},
				Badge: '🌋',
			},
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Gerry",
						Age:          88,
						IsCoding:     true,
						privateField: "Put stuff here"},
					Friends: []string{"Gaqlyn", "Gicard", "Gosemary"},
					Ratings: map[string]int{"naps": 10},
				},
				Badge: '🎉',
			},
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Gustion",
						Age:          21,
						IsCoding:     true,
						privateField: "that you need to use"},
					Friends: []string{"Gidea", "Gno"},
					Ratings: map[string]int{"carbs": 10},
				},
				Badge: '🍢',
			},
			{ // 👈 Notice we don't need to put GopherV3 here
				GopherV2: GopherV2{
					Gopher: Gopher{
						Name:         "Guna",
						Age:          0b11111111,
						IsCoding:     false,
						privateField: "but other packages don't."},
					Friends: []string{"Gouda"},
					Ratings: map[string]int{"haircuts": 3},
				},
				Badge: '🌜',
			},
		},
	}
	fmt.Printf("%+v\n\n", c)
	for _, g := range c.Residents {
		// g.GopherV2.Gopher.privateField
		fmt.Printf("%s ", g.privateField)
	}
}

type Human interface{ Greet() string }

type Bear interface{ Growl() string }

type Pig interface{ MudBath(minutes int) }

// HumanBearPig is the thing of nightmares.
type HumanBearPig interface {
	Human // half human
	Bear  // half bear
	Pig   // half pig
}

func (g Gopher) Greet() string { return "Hi-dilly-ho, neighborinos!" }
func (g Gopher) Growl() string { return "Rawr XD" }
func (g Gopher) MudBath(m int) { fmt.Printf("Rolled around for %d mins", m) }

func (g GopherV2) Growl() string { return "GOPHERV2 GRRRWAUGH!!" }
func (g GopherV2) MudBath(m int) { fmt.Printf("V2:Sat in mud for %d mins", m) }

func (g GopherV3) Greet() string { return "GOPHERV3 SAYS WHADDUP!!" }

// Interface shows that we need a struct that will satisfy the entire
// interface, but we don't care if that struct or the embedded structs inside
// of it satisfy the interface.
func Interface(hbp HumanBearPig) {
	fmt.Println("It was a dark and rainy night. The moon 🌕 felt so bright.")
	fmt.Println("A strange passerby. Avert my gaze, I try.")
	fmt.Println(hbp.Greet())
	fmt.Println("The person yelled to me; suddenly something broke free.")
	fmt.Println(hbp.Growl())
	fmt.Println("I ran as fast I could, through the streets to the forests of wood.")
	fmt.Println("A final leap over some mud. The thing ceased with a great thud.")
	hbp.MudBath(20)
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif "typing-furiously by Egon Elbre")

```go
package embed_test

import "basics/embed"

func ExampleStruct() {
	embed.Struct()
	// Output:
	// GopherV2: embed.GopherV2{Gopher:embed.Gopher{Name:"Gala", Age:24, IsCoding:false, privateField:"Embedding doesn't change access modifiers"}, Friends:[]string{"Gabby", "Gael", "Garth", "Gazsi"}, Ratings:map[string]int{"chocolate":9, "coffee":3, "tea":7}}
	//
	// changed GopherV2: embed.GopherV2{Gopher:embed.Gopher{Name:"gopher", Age:26, IsCoding:true, privateField:"Can be changed because in same package."}, Friends:[]string{"Gabby", "Gael", "Garth", "Gazsi", "Gandalf"}, Ratings:map[string]int{"chocolate":9, "coffee":3, "garlic bread":10, "tea":7}}
	// Three layers of embedding {GopherV2:{Gopher:{Name:Ground Age:57005 IsCoding:true privateField:Access granted} Friends:[Gunter] Ratings:map[embedding:10]} Badge:129327}
}

func ExampleCity() {
	embed.City()
	// Output:
	// {Residents:[{GopherV2:{Gopher:{Name:Gance Age:10 IsCoding:true privateField:Not accessible outside package.} Friends:[Guzz] Ratings:map[space:10]} Badge:128640} {GopherV2:{Gopher:{Name:Guuba Age:511 IsCoding:false privateField:Can be accessed in package.} Friends:[Ghorm Gokil] Ratings:map[death metal:10]} Badge:127755} {GopherV2:{Gopher:{Name:Gerry Age:88 IsCoding:true privateField:Put stuff here} Friends:[Gaqlyn Gicard Gosemary] Ratings:map[naps:10]} Badge:127881} {GopherV2:{Gopher:{Name:Gustion Age:21 IsCoding:true privateField:that you need to use} Friends:[Gidea Gno] Ratings:map[carbs:10]} Badge:127842} {GopherV2:{Gopher:{Name:Guna Age:255 IsCoding:false privateField:but other packages don't.} Friends:[Gouda] Ratings:map[haircuts:3]} Badge:127772}]}
	//
	// Not accessible outside package. Can be accessed in package. Put stuff here that you need to use but other packages don't.
}

func ExampleInterface() {
	embed.Interface(embed.Gopher{})
	// Output:
	// It was a dark and rainy night. The moon 🌕 felt so bright.
	// A strange passerby. Avert my gaze, I try.
	// Hi-dilly-ho, neighborinos!
	// The person yelled to me; suddenly something broke free.
	// Rawr XD
	// I ran as fast I could, through the streets to the forests of wood.
	// A final leap over some mud. The thing ceased with a great thud.
	// Rolled around for 20 mins
}

func ExampleInterface_v2() {
	embed.Interface(embed.GopherV2{})
	// Output:
	// It was a dark and rainy night. The moon 🌕 felt so bright.
	// A strange passerby. Avert my gaze, I try.
	// Hi-dilly-ho, neighborinos!
	// The person yelled to me; suddenly something broke free.
	// GOPHERV2 GRRRWAUGH!!
	// I ran as fast I could, through the streets to the forests of wood.
	// A final leap over some mud. The thing ceased with a great thud.
	// V2:Sat in mud for 20 mins
}

func ExampleInterface_v3() {
	embed.Interface(embed.GopherV3{})
	// Output:
	// It was a dark and rainy night. The moon 🌕 felt so bright.
	// A strange passerby. Avert my gaze, I try.
	// GOPHERV3 SAYS WHADDUP
	// The person yelled to me; suddenly something broke free.
	// GOPHERV2 GRRRWAUGH!!
	// I ran as fast I could, through the streets to the forests of wood.
	// A final leap over some mud. The thing ceased with a great thud.
	// V2:Sat in mud for 20 mins
}
```
