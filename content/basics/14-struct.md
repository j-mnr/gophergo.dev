---
Lesson: 14
Title: "Struct"
Draft: false
---

Data structure aka `struct` is used to group like data together. For example
humans are made up of data. 😯 And not only are humans made up of data, 😮 that
data can be seen in different views! 😲 What do I mean by this?

Think about it from a doctor's perspective, what might a doctor take data
about? 🤔 Your temperature, your weight, your height, blood pressure, etc.

Now what data does a bank collect from a person? Social Security Number,
government issued ID, number of accounts, deposits, withdrawals, etc.

🤯 Crazy, right? Humans are data, not only humans, but **_everything_** is data
and can be represented that way from all different kinds of views 🤯

So why do we care? As your program grows it will become very, and I mean
_very_, hard to only have arbitrary `string`, `rune`, `int`, `bool`, and `float`
types running around in your code base. So, _instead_ of declaring thousands of
oddball 🤪 variables, lets group them up, into a logical structure, a data
structure, a `struct` 😄

## Setup

Let's make our directory `structs` and the files we want inside of that directory
`example_test.go` `structs.go`

```sh
mkdir structs
touch structs/example_test.go structs/structs.go
```

Now let's open up `structs.go` and for the very first line we'll add
```go
package structs
```
Next for `example_test.go` for the very first line we'll add
```go
package CHANGEME_test
```

## Declare Struct

You know what else is a `struct`? A Gopher.

{{< figure src="/maria-letta/characters/36.svg" alt="characters-36"
attr=`[characters-36](https://github.com/MariaLetta/free-gophers-pack/blob/master/characters/svg/36.svg) by
[Maria Letta](https://instagram.com/maria_letta_art)-
[License](https://github.com/MariaLetta/free-gophers-pack/blob/master/LICENSE)`
width=100px >}}

{{< figure src="/maria-letta/characters/27.svg" alt="characters-27"
attr=`[characters-27](https://github.com/MariaLetta/free-gophers-pack/blob/master/characters/svg/27.svg) by
[Maria Letta](https://instagram.com/maria_letta_art)-
[License](https://github.com/MariaLetta/free-gophers-pack/blob/master/LICENSE)`
width=100px >}}

That means you and I can be represented as a `struct`. So what basic things
make up a gopher? 🤔 We have a name, we have an age, and there's a really good
chance we'll be coding. So let's do it! 😁

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

You'll notice how nice and neat 🤩 everything lines up. I love ❤️ how it looks
but I wasn't the one who did it. If you haven't found it already, Go comes with
a command-line tool simply called `go` not too hard to remember 😂. You can use
`go fmt structs.go` on the command-line and it will do the pretty formatting
for you. Your text-editor is likely smart enough to do that for you with a
shortcut, why not look around the interwebs for it? Search _"format go code
<YOUR-TEXT-EDITOR/IDE-HERE>"_ 😉

#### structs.go

```go
// Gopher is a public struct that can be made outside of the package. It
// consists of some basic fields that all gophers have and a privateField that
// can only be accessed in the package. We make things private so the people
// using our library (this code) don't have to worry about certain fields.
type Gopher struct {
	Name         string
	Age          int
	IsCoding     bool
	privateField string
}

// city is a private struct with a struct inside of it! 🤯 That's because a
// gopher is a `type` and we can put ALL types into a struct. That means we can
// put city into another struct called state and it would haves cities in it
// with gophers in them! 🤯
type city struct {
	gophers         []Gopher
	gopherAddresses map[Gopher]string
}
```

## New Struct

We've made our structs, now it's time to use them! This process is often
referred to as "instantiation". Big word, doesn't matter, it means to make.

> Instantiate a `Gopher` `struct` == Make a `Gopher` `struct`

But if you remember in the previous lesson on [functions](/basics/13-function)
when something is private it can't be accessed outside of the package! Let's
look at how we would _go_ 😹 about letting others access something private.

Many times when you make a `struct` you want to be able to _construct_ it. The
only problem you would face is if you had private fields, like our
`privateField` field in our gopher. The problem is you'd have no way of setting
it! As a small taste of what we will code, if I _do_ allow my `struct` to be
public and turn it into `Gopher`, we still can't set or `privateField` for that
`struct`!

```go
noWork := structs.Gopher{
	Name:         "Goki",
	Age:          0xDEAD,
	IsCoding:     false,
	// 👇👇👇 Causes error
	privateField: "",
}
```

We'll get `unknown field privateField in struct literal`. So the way we get
around that is by creating a function that can make our `struct`. A constructor
of structs. I say that because often times you will hear `New` functions
referred to as constructors, because that's **exactly** what they do; construct
a `struct`.

It is idiomatic Go to have `New` as a constructor name or have the name of the
`struct` appended to the function, e.g. `func NewGopher( ... ) gopher { ... }`

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### structs.go

```go
// New is a constructor of a gopher, since New is exported (because it is
// capitalized) we can call it outside of the package, while keeping everything
// about a gopher internal!
func New(name string, age int, isCoding bool, privateField string) gopher {
	return gopher{
		Name:         name,
		Age:          age,
		IsCoding:     isCoding,
		privateField: privateField,
	}
}
```

#### example_test.go

```go
func ExampleNew() {
	literalGopher := structs.Gopher{
		Name:     "Gitral",
		Age:      0o703,
		IsCoding: true,
	}
	fmt.Printf("Can never set privateField: %#v\n", literalGopher)

	constructedGopher :=
		structs.New("Jay", 29, true, "once set, can't be changed.")
	fmt.Printf("%#v\n", constructedGopher)

	constructedGopher.Age = 58
	constructedGopher.IsCoding = false
	constructedGopher.Name = "Jöt"
	// NOTE(jay): Can't do!
	// constructedGopher.privateField = "Not possible!"
	fmt.Printf("%#v\n", constructedGopher)

	// Output:
	// Can never set privateField: structs.Gopher{Name:"Gitral", Age:451, IsCoding:true, privateField:""}
	// structs.Gopher{Name:"Jay", Age:29, IsCoding:true, privateField:"once set, can't be changed."}
	// structs.Gopher{Name:"Jöt", Age:58, IsCoding:false, privateField:"once set, can't be changed."}
}
```

## Basics of Struct

Remember, a `struct` is just a way to keep your code neat. It's not magical 🪄
it's only for organization. Think about it for the mechanical engineers. They
have raw materials, wood 🪵 metal ⚙️ , and other supporting materials. Would
they just slap concrete into a mix of wood and metal and _pray_ 🙏 that their
structure doesn't fall? Of course not! That would be a terrible building 🏚️
They plan out _how_ the structure is going to look, what supports it may need,
what loads it could handle, what natural disasters 🌪️ should it stand up to and
then take action.

We want to do the same thing; make our `struct` with purpose. It should be
looking to _solve_ some issue. We can solve the issue by grouping them in a
logical structure that makes sense for our current situation.

We've introduced it in the `example_test.go` but we'll go into more detail here
about using our structs that we make. We have our data structure, now lets use
it! For right now we'll just talk about accessing the fields we assign
and also about setting or resetting those fields if they need to be updated.

In that sense, you can think of a `struct` as a pre-defined
[map](/basics/10-map) 🗺️ with keys that can have different value types! 🤯 We can
then draw the conclusion that `struct` gives you more freedom than a `map`,
which is great in many situations, but can also be a weakness if **all** of
your values are the same `type` because you're adding complexity where it
doesn't belong. Let's show an example where it would be better to use a `map`
instead of a `struct`

```go
type country string
type capital string
// Simple, easy, straight forward and fast.
mCountries := map[country]capital{
	"Mongolia": "Ulaanbaatar",
	"Burundi":  "Gitega",
	"Pakistan": "Islamabad",
	"Denmark":  "Copenhagen",
	"Ethiopia": "Addis Ababa",
}
// Complex, hard to follow, hard to understand, construct new structs and
// have no way of accessing country immediately. We have to search entire slice
// before we get our country.
type Country struct {
	Name    string
	Capital string
}
sCountries := []Country{
	Country{Name: "Syria", Capital: "Damascus"},
	Country{Name: "Georgia", Capital: "Tbilisi"},
	Country{Name: "Tanzania", Capital: "Dodoma"},
	Country{Name: "Jamaica", Capital: "Kingston"},
	Country{Name: "Venezuela", Capital: "Caracas"},
}
```

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### structs.go

```go
// StructBasic shows you how to initialize (make) structs, manipulate all the
// values within a struct by getting and setting the values and use them in
// other structs. We also return a `city` struct here to show you can give back
// unexported types from exported functions.
func StructBasic() city {
	// Make a gopher and have ALL fields set to the zero value.
	var zero Gopher
	// Make a gopher and set all fields to what we want them to be.
	gordo := Gopher{
		Name:         "Gordo",
		Age:          22,
		IsCoding:     true,
		privateField: "Set it and forget it",
	}
	// Make a gopher and only set the fields we care about, leaving the rest to
	// be initialized (made) with their zero values.
	gary := Gopher{Name: "Gary"}
	anon := Gopher{Age: 42, IsCoding: true, privateField: "Scanning 60000 ports"}
	fmt.Printf("zero valued gopher: %#v\n", zero)
	fmt.Printf("gordo gopher: %#v\n", gordo)
	fmt.Printf("gary gopher: %#v\n", gary)
	fmt.Printf("anon gopher: %#v\n", anon)
	fmt.Println()

	// Access a value by using the `.` and the fields name
	gary.Age = 33
	gary.privateField = "Searching: Why does my husband fart so much."
	fmt.Printf("gary gopher: %#v\n", gary)
	anon.Name = "Garfunkel"
	fmt.Printf("anon gopher: %#v\n", anon)

	teska := city{
		Gophers: []Gopher{gordo, gary, anon},
		GopherAddresses: map[Gopher]string{
			gordo: "123 Lemon Dr.",
			gary:  "889 Galaway Ave.",
			anon:  "543 W 8th St.",
		},
	}
	// Since teska has a slice of gophers we can get it and range over each of
	// them in a for loop. g == gopher
	for _, g := range teska.Gophers {
		// Access each gopher's IsCoding field. In the slice of gophers we are
		// accessing from the city!
		if g.IsCoding {
			fmt.Println(g.Name, "is in the middle of coding! Come back soon.")
			continue
		}
		fmt.Println(g, "lives at", teska.GopherAddresses[g])
	}
	fmt.Println()

	// zero out a gopher, not needed here, but you can see how it is done.
	gordo.Age = 0
	gordo.Name = ""
	gordo.IsCoding = false
	gordo.privateField = ""
	fmt.Printf("gordo gopher: %#v\nzero  gopher: %#v\n", gordo, zero)
	return teska
}
```

#### example_test.go

Here we can see we can _use_ an unexported `struct` 🤯 because the function
`structs.StructBasic` returns it. Why do this? It means the person using your
code can't create a `city` _but_ they can get one pre-made and ready to be
used.

```go
func ExampleStructBasic() {
	city := structs.StructBasic()
	for _, g := range city.Gophers {
		if g.Name == "Garfunkel" {
			fmt.Printf("We found him: %s\n", city.GopherAddresses[g])
		}
	}
	// Output:
	// zero valued gopher: structs.Gopher{Name:"", Age:0, IsCoding:false, privateField:""}
	// gordo gopher: structs.Gopher{Name:"Gordo", Age:22, IsCoding:true, privateField:"Set it and forget it"}
	// gary gopher: structs.Gopher{Name:"Gary", Age:0, IsCoding:false, privateField:""}
	// anon gopher: structs.Gopher{Name:"", Age:42, IsCoding:true, privateField:"Scanning 60000 ports"}
	//
	// gary gopher: structs.Gopher{Name:"Gary", Age:33, IsCoding:false, privateField:"Searching: Why does my husband fart so much."}
	// anon gopher: structs.Gopher{Name:"Garfunkel", Age:42, IsCoding:true, privateField:"Scanning 60000 ports"}
	// Gordo is in the middle of coding! Come back soon.
	// {Gary 33 false Searching: Why does my husband fart so much.} lives at 889 Galaway Ave.
	// Garfunkel is in the middle of coding! Come back soon.
	//
	// gordo gopher: structs.Gopher{Name:"", Age:0, IsCoding:false, privateField:""}
	// zero  gopher: structs.Gopher{Name:"", Age:0, IsCoding:false, privateField:""}
	// We found him: 543 W 8th St.
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png "gopher-victorious by Egon Elbre")

```go
package structs

import "fmt"

// Gopher is a public struct that can be made outside of the package. It
// consists of some basic fields that all gophers have and a privateField that
// can only be accessed in the package. We make things private or better said
// -- unexported -- so the people using our library (this code) don't have to
// worry about certain fields.
type Gopher struct {
	Name         string
	Age          int
	IsCoding     bool
	privateField string
}

// city is a unexported struct with a struct inside of it! 🤯 That's because a
// gopher is a `type` and we can put ALL types into a struct. That means we can
// put city into another struct called state and it would haves cities in it
// with gophers in them! 🤯
type city struct {
	Gophers         []Gopher
	GopherAddresses map[Gopher]string
}

// New is a constructor of a Gopher, since New is exported (because it is
// capitalized) we can call it outside of the package, while being able to set
// private fields of our Gopher!
func New(name string, age int, isCoding bool, privateField string) Gopher {
	return Gopher{
		Name:         name,
		Age:          age,
		IsCoding:     isCoding,
		privateField: privateField,
	}
}

// StructBasic shows you how to initialize (make) structs, manipulate all the
// values within a struct by getting and setting the values and use them in
// other structs. We also return a `city` struct here to show you can give back
// unexported types from exported functions.
func StructBasic() city {
	// Make a gopher and have ALL fields set to the zero value.
	var zero Gopher
	// Make a gopher and set all fields to what we want them to be.
	gordo := Gopher{
		Name:         "Gordo",
		Age:          22,
		IsCoding:     true,
		privateField: "Set it and forget it",
	}
	// Make a gopher and only set the fields we care about, leaving the rest to
	// be initialized (made) with their zero values.
	gary := Gopher{Name: "Gary"}
	anon := Gopher{Age: 42, IsCoding: true, privateField: "Scanning 60000 ports"}
	fmt.Printf("zero valued gopher: %#v\n", zero)
	fmt.Printf("gordo gopher: %#v\n", gordo)
	fmt.Printf("gary gopher: %#v\n", gary)
	fmt.Printf("anon gopher: %#v\n", anon)
	fmt.Println()

	// Access a value by using the `.` and the fields name
	gary.Age = 33
	gary.privateField = "Searching: Why does my husband fart so much."
	fmt.Printf("gary gopher: %#v\n", gary)
	anon.Name = "Garfunkel"
	fmt.Printf("anon gopher: %#v\n", anon)

	teska := city{
		Gophers: []Gopher{gordo, gary, anon},
		GopherAddresses: map[Gopher]string{
			gordo: "123 Lemon Dr.",
			gary:  "889 Galaway Ave.",
			anon:  "543 W 8th St.",
		},
	}
	// Since teska has a slice of gophers we can get it and range over each of
	// them in a for loop. g == gopher
	for _, g := range teska.Gophers {
		// Access each gopher's IsCoding field. In the slice of gophers we are
		// accessing from the city!
		if g.IsCoding {
			fmt.Println(g.Name, "is in the middle of coding! Come back soon.")
			continue
		}
		fmt.Println(g, "lives at", teska.GopherAddresses[g])
	}
	fmt.Println()

	// zero out a gopher, not needed here, but you can see how it is done.
	gordo.Age = 0
	gordo.Name = ""
	gordo.IsCoding = false
	gordo.privateField = ""
	fmt.Printf("gordo gopher: %#v\nzero  gopher: %#v\n", gordo, zero)
	return teska
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif "typing-furiously by Egon Elbre")

```go
package structs_test

import (
	"basics/structs"
	"fmt"
)

func ExampleNew() {
	literalGopher := structs.Gopher{
		Name:     "Gitral",
		Age:      0o703,
		IsCoding: true,
	}
	fmt.Printf("Can never set privateField: %#v\n", literalGopher)

	constructedGopher :=
		structs.New("Jay", 29, true, "once set, can't be changed.")
	fmt.Printf("%#v\n", constructedGopher)

	constructedGopher.Age = 58
	constructedGopher.IsCoding = false
	constructedGopher.Name = "Jöt"
	// NOTE(jay): Can't do!
	// constructedGopher.privateField = "Not possible!"
	fmt.Printf("%#v\n", constructedGopher)

	// Output:
	// Can never set privateField: structs.Gopher{Name:"Gitral", Age:451, IsCoding:true, privateField:""}
	// structs.Gopher{Name:"Jay", Age:29, IsCoding:true, privateField:"once set, can't be changed."}
	// structs.Gopher{Name:"Jöt", Age:58, IsCoding:false, privateField:"once set, can't be changed."}
}

func ExampleStructBasic() {
	city := structs.StructBasic()
	for _, g := range city.Gophers {
		if g.Name == "Garfunkel" {
			fmt.Printf("We found him: %s\n", city.GopherAddresses[g])
		}
	}
	// Output:
	// zero valued gopher: structs.Gopher{Name:"", Age:0, IsCoding:false, privateField:""}
	// gordo gopher: structs.Gopher{Name:"Gordo", Age:22, IsCoding:true, privateField:"Set it and forget it"}
	// gary gopher: structs.Gopher{Name:"Gary", Age:0, IsCoding:false, privateField:""}
	// anon gopher: structs.Gopher{Name:"", Age:42, IsCoding:true, privateField:"Scanning 60000 ports"}
	//
	// gary gopher: structs.Gopher{Name:"Gary", Age:33, IsCoding:false, privateField:"Searching: Why does my husband fart so much."}
	// anon gopher: structs.Gopher{Name:"Garfunkel", Age:42, IsCoding:true, privateField:"Scanning 60000 ports"}
	// Gordo is in the middle of coding! Come back soon.
	// {Gary 33 false Searching: Why does my husband fart so much.} lives at 889 Galaway Ave.
	// Garfunkel is in the middle of coding! Come back soon.
	//
	// gordo gopher: structs.Gopher{Name:"", Age:0, IsCoding:false, privateField:""}
	// zero  gopher: structs.Gopher{Name:"", Age:0, IsCoding:false, privateField:""}
	// We found him: 543 W 8th St.
}
```
