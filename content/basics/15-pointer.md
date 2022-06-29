---
Lesson: 15
Title: "Pointer"
Draft: false
---

Pointers in nutshell 👆👆👆 TA-DA!!!! Now you get pointers! 😂 Seriously
though, it's that easy. I remember the first time I was introduced to pointers
they seemed so mystical and hard to understand, but trust me when you get it,
you get it.

Let's Go 😹 get it then! A pointer points to 👉 something. There
are **thousands** of pointers that point to you. Are you on social media (IG 📷
FB 📖 TW 🐦 TT 🎵 🔗in, Snap👻)? That's a pointer.They point to you 👈.
Driver's license number? Student ID? Debit card? Bank routing number? YouTube
account? Your physical home address? Yup, all pointers. All of these things can
get to the **true** value of you, _indirectly_. They are not you but they
_point_ 👉 to you.

## Setup

Let's make our directory `pointer` and the files we want inside of that directory
`example_test.go` `pointer.go`

```sh
mkdir pointer
touch pointer/example_test.go pointer/pointer.go
```

Now let's open up `pointer.go` and for the very first line we'll add
```go
package pointer
```
Next for `example_test.go` for the very first line we'll add
```go
package pointer_test
```

## Pass By Value

Pass by value is what we usually do in many other programming languages. The
topic can be a little tricky to talk about 🤡 so I need to define what I mean
by pass by value which is: **pass a copy of the entire thing**.

If you're coming from many other languages you've probably never touched
pointers. From the 10+ languages I've used the only languages that _don't_ hide
pointers from you are `c, c++, rust` and `go`. Though **that is by no means a
complete list** 😅 This is only from my experience.

Whether a language (`python, java, js`) hides pointers from you or not, they
are still there. If you code in a language that doesn't have pointers, it's
just because the language doesn't support it, not that they are not there. I
can guarantee that when your code is compiled in **_any_** language there are
pointers 👆👇👉👈 under the hood 🚗 So learning pointers will help you grow as
a developer not just in Go. Good fundamentals make you a master at your craft.

Pointers are everywhere. Which is why I needed to define what _I_ mean by
**pass by value**, for example in `java` they claim _everything is pass by
value_ which if you know the language everything is a bloated `Object` and
would make all of your methods _really_ 😬 💸💸 expensive 💸💸 😢 and slow 🐌,
but the way `java` defines pass by value is **pass a copy of anything**. And
what it's passing is a pointer. That pointer 👈 is copied into the method,
which is very cheap and fast 🌩️ It's cheap and fast because it's just a number,
an address, more on that later.

In languages that _don't_ support pointers, it's a moot _point_ 😹 to bring up
"Pass by value 🆚 Pass by reference" because, well, you don't care! It get's
into the function/method/procedure in any case and you just do your business
with it all the same. It's usually collection types people get tripped up on,
which we'll go over 👇 [down below](#pass-by-reference-pointer-in-depth). See
what I did there? I made a pointer to another topic 😂

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `pointer.go`

```go
// PassByValue passes an int by value. When an integer is passed by value it is
// copied into the function meaning whatever we do to this `intValue` it will
// never change the original because it's a copy.
func PassByValue(intVal int) {
	intVal = 100
	fmt.Println("intVal in function:", intVal)
}
```

#### `example_test.go`

```go
func ExamplePassByValue() {
	intVal := 8
	pointer.PassByValue(intVal)
	fmt.Println("intVal after passing value:", intVal)
	// Output:
	// intVal in function: 100
	// intVal after passing value: 8
}
```

## Pass By Reference (Pointer)

Now we are going to pass in a pointer to our function, this is commonly
referred to as "pass by reference", because it's **not** the actual value, it's
a reference to it, it _points_ 👉 to the value. To talk to your friend you have
to have them right there in front of you, right? **Of course not** ❗ You can
call 📱 them. You _indirectly_ get in contact with them through **some number**
555-382-9109

Now we understand what a pointer is but not what it is in programming. A
pointer is actually a lot simpler than real life, because it's only ever one
thing. A pointer is only an address. **Exactly** like your physical home
address 🏡 if you were the value. If we go in the direction we're 👉 pointed to
we'll reach the source and we can then change the value. A friend comes over 🚶
to your house and changes your mood from bored 🥱 to happy 😄

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `pointer.go`

```go
// PassByReference passes a pointer to the function. This value is an address,
// like your street address. If we derefence it `*intPointer` we can change the
// original value it was pointing to.
func PassByReference(intPointer *int) {
	*intPointer = 100
	// We can't check address in test because it's different every time the
	// program starts again.
	// fmt.Println("intPointer in function:", intPointer) <-- Check address
	fmt.Println("intVal in function:", *intPointer)
}
```

#### `example_test.go`

```go
func ExamplePassByReference() {
	intVal := 8
	intPtr := &intVal
	pointer.PassByReference(intPtr)
	// pointer.PassByReference(&intVal) <-- Also works!
	fmt.Println("intVal after derefence:", intVal)
	// Output:
	// intVal in function: 100
	// intVal after derefence: 100
}
```

## More Pass By Reference (Pointer)

So we've seen that this works with one of the basic/builtin/primitive types,
`int`, but what about the other types we learned about _way_ back in [lesson
one](/basics/01-types). It should come to no surprise that, they _do_ work with
pointers.

It really can't be stated enough. A pointer is an address. It's a really funny
looking address, but it's not for you to walk 🚶 to, it's for the program to
walk to and it can read the address just fine. It may help to expose us to some
of these addresses, just so they aren't so arcane 🧙 and magical 🪄

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

With the `fmt` package we've been using for so long there is a way to get the
address (aka the pointer) to any value with the formatting directive `%p`, so
remember **p for pointer**! Or if you already have a pointer you can use
`fmt.Println(myPtr)` and you can see the address too!

#### `pointer.go`

```go
// PassMoreByReferences shows you that you can pass all of the other primitive
// data types and derefence '*' them and change their values.
func PassMoreByReferences(sPtr *string, bPtr *bool, rPtr *rune, fPtr *float64) {
	// Uncomment and `go test` to see what these values are before you
	// dereference them and change the values at their locations.
	// fmt.Printf("string: %p\nbool: %p\nrune: %p\nfloat: %p\n",
	// 	sPtr, bPtr, rPtr, fPtr)
	// fmt.Println("addresses:", sPtr, bPtr, rPtr, fPtr)
	*sPtr = "Dereferenced and changed"
	*bPtr = true
	*rPtr = '🤡'
	*fPtr = 3.14159
}
```

When I uncommented the above ☝️  print statements I got a failing example 😂
and these values.

```txt
string: 0xc0001a4000
bool: 0xc00018c015
rune: 0xc00018c018
float: 0xc00018c020
addresses: 0xc0001a4000 0xc00018c015 0xc00018c018 0xc00018c020
```

As you can see they are all in
[hexadecimal](https://www.freecodecamp.org/news/hexadecimal-number-system/)
representation, but more importantly, it's a number. If someone lives on a
street in a house 🏡 They _must_ have leading digits to **the exact location**
Something like **1234** Square Road. The address `0xc0001a4000` is the same as
`1234` It's where I go if I want to find the string's actual value.


#### `example_test.go`

```go
func ExamplePassMoreByReferences() {
	s := "This is going to change"
	b := false
	r := '🔥'
	f := 2.139284094893
	fmt.Printf(`Before changing values:
string value: %q
bool value: %t
rune as emoji value: %s
float value: %f

`, s, b, string(r), f)

	pointer.PassMoreByReferences(&s, &b, &r, &f)
	fmt.Printf(`After changing values:
string value: %q
bool value: %t
rune as emoji value: %s
float value: %f

`, s, b, string(r), f)
	// Output:
	// Before changing values:
	// string value: "This is going to change"
	// bool value: false
	// rune as emoji value: 🔥
	// float value: 2.139284
	//
	// After changing values:
	// string value: "Dereferenced and changed"
	// bool value: true
	// rune as emoji value: 🤡
	// float value: 3.141590
}
```

## Pass By Reference (Pointer) In Depth

I hope you're enjoying your first encounter with Pointers 😁 **BUT WAIT!**
That's a lie! 👺 We've been using pointers for awhile now before this lesson
with [slices](/basics/09-slice) and [maps](/basics/10-map) Now you may be
thinking

> 🤔 I didn't see any funny looking operators like the address `&` operator and
> the pointer indirection `*` operator in those lessons.

And you'd be right, but a _loooooooooooong_ time ago back in the pre-historic
era 🦕 when programming first started the `[]` operator was made as syntactic
sugar 🍬 for the pointer indirection `*` operator. This means every time you've
ever done and will do `mySlice[5] = 20` you're actually dealing with pointers,
we can't do it in Go as pointer arithmetic is not allowed, but here's what
we're doing in `c`

```c
int *mySlice = calloc(8,  sizeof(int));
mySlice += 5;
*mySlice = 20;
mySlice -= 5;
```

As you can see it's more involved to move through your array, wouldn't it just
be nice to have a way to access the fifth index? Oh! There is 🙂

```c
int mySlice[8];
mySlice[5] = 20;
```

So we can thank 🙏 the `[]` operator made a long time ago that we don't have to
go through so much trouble and overhead of remembering where we are and doing
math to get to it 😬. Maps also got the `[]` operator as well and maps are also
pointers that can be dereferenced.

There is more detail about slices we could go into, but it'd be getting a
little too deep. I feel like I've already stretched our minds 🧠 enough with
the quick history lesson. If you are interested there is an amazing explanation
by Rob Pike on [The Go Blog](https://go.dev/blog/slices)

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `pointer.go`

```go
type ChangeThings struct {
	Int  int
	Str  string
	Rune rune
}

// PassCollections shows what happens when we try to dereference slices, maps,
// and structs. Structs are special in that they are NOT a pointer like slices
// and maps and therefore need to have a pointer passed in if we want to change
// their inner values.
func PassCollections(slice []string, mp map[string]rune, ctCopy ChangeThings,
	ctPtr *ChangeThings) {

	// With fmt.Printf("%p, %p, %p, %p", slice, mp, ctCopy, ctPtr) you can see
	// the address of all of the arguments, we don't do it here because every
	// time the function executes the address will change! 🤯 and therefore the
	// test would, Never! pass.
	for i := range slice {
		if i == 0 {
			slice[i] = "Dereferenced by `[]` operator. It acts just like `*` operator"
		} else {
			slice[i] = fmt.Sprintf("me %d", i+1)
		}
	}
	for k, v := range mp {
		mp[k] = v % 7 // Dereferenced again with `[]` operator
	}
	ctCopy.Int = 0xF4B1E
	ctCopy.Str = "Changing a copies value doesn't work on the original."
	ctCopy.Rune = '🌞'
	ctPtr.Int = 0xF4B1E
	ctPtr.Str = "Dereferenced with the `.` operator, like `*` and `[]`."
	ctPtr.Rune = '🌞'
	// These lines do nothing, as the function makes a copy of everything it
	// receives. What we really wanted was to release the original collection
	// types to garbage collection. That must be done where it is instantiated.
	// Not when it is passed to a function as a copy.
	slice = nil
	mp = nil
	ctPtr = nil
	// NOTE(jay): won't work
	// ctCopy = nil
	//
	// This makes it more obvious that we have don't have a pointer. A pointer
	// can always be set to nil. We set it to a zero valued ChangeThings struct.
	ctCopy = ChangeThings{}
}
```

#### `example_test.go`

```go
func ExamplePassCollections() {
	sl := []string{"Look", "at", "all", "my", "values"}
	mp := map[string]rune{
		"boxing":     '🥊',
		"chestnut":   '🌰',
		"ocean":      '🌊',
		"heart-eyes": '😍',
		"microphone": '🎤',
		"hibiscus":   '🌺',
	}
	asCopy := pointer.ChangeThings{
		Int:  777,
		Str:  "Will I change?",
		Rune: '🌔',
	}
	asCopy2 := pointer.ChangeThings{
		Int:  777,
		Str:  "Will I change?",
		Rune: '🌔',
	}
	asPtr := &asCopy2
	/////////////////////////////////////////////////////////////////////////////
	fmt.Printf(`Before changing values:
slice value: %#v
map value: %#v
copy of struct value: %#v
pointer to struct value: %#v
copy2 of struct value: %#v

`, sl, mp, asCopy, asPtr, asCopy2)

	pointer.PassCollections(sl, mp, asCopy, asPtr)
	/////////////////////////////////////////////////////////////////////////////
	fmt.Printf(`After changing values:
slice value: %#v
map value: %#v
copy of struct value: %#v
pointer to struct value: %#v
copy2 of struct value: %#v

`, sl, mp, asCopy, asPtr, asCopy2)

	/////////////////////////////////////////////////////////////////////////////
	sl = nil
	mp = nil
	asPtr = nil
	asCopy = pointer.ChangeThings{}
	asCopy2 = pointer.ChangeThings{}
	fmt.Printf(`Actually remove all elements from collection types:
pointer: %#v
pointer: %#v
pointer: %#v
literal: %#v
literal: %#v
`, sl, mp, asPtr, asCopy, asCopy2)
	// Output:
	// Before changing values:
	// slice value: []string{"Look", "at", "all", "my", "values"}
	// map value: map[string]int32{"boxing":129354, "chestnut":127792, "heart-eyes":128525, "hibiscus":127802, "microphone":127908, "ocean":127754}
	// copy of struct value: pointer.ChangeThings{Int:777, Str:"Will I change?", Rune:127764}
	// pointer to struct value: &pointer.ChangeThings{Int:777, Str:"Will I change?", Rune:127764}
	// copy2 of struct value: pointer.ChangeThings{Int:777, Str:"Will I change?", Rune:127764}
	//
	// After changing values:
	// slice value: []string{"Dereferenced by `[]` operator. It acts just like `*` operator", "me 2", "me 3", "me 4", "me 5"}
	// map value: map[string]int32{"boxing":1, "chestnut":0, "heart-eyes":5, "hibiscus":3, "microphone":4, "ocean":4}
	// copy of struct value: pointer.ChangeThings{Int:777, Str:"Will I change?", Rune:127764}
	// pointer to struct value: &pointer.ChangeThings{Int:1002270, Str:"Dereferenced with the `.` operator, like `*` and `[]`.", Rune:127774}
	// copy2 of struct value: pointer.ChangeThings{Int:1002270, Str:"Dereferenced with the `.` operator, like `*` and `[]`.", Rune:127774}
	//
	// Actually remove all elements from collection types:
	// pointer: []string(nil)
	// pointer: map[string]int32(nil)
	// pointer: (*pointer.ChangeThings)(nil)
	// literal: pointer.ChangeThings{Int:0, Str:"", Rune:0}
	// literal: pointer.ChangeThings{Int:0, Str:"", Rune:0}
}
```

## The Whole File

![https://twitter.com/egonelbre](/egon-elbre/gopher-victorious.png "gopher-victorious by Egon Elbre")

```go
package pointer

import "fmt"

// PassByValue passes an int by value. When an integer is passed by value it is
// copied into the function meaning whatever we do to this `intValue` it will
// never change the original because it's a copy.
func PassByValue(intVal int) {
	intVal = 100
	fmt.Println("intVal in function:", intVal)
}

// PassByReference passes a pointer to the function. This value is an address,
// like your street address. If we derefence it `*intPointer` we can change the
// original value it was pointing to.
func PassByReference(intPointer *int) {
	*intPointer = 100
	// We can't check address in test because it's different every time the
	// program starts again.
	// fmt.Println("intPointer in function:", intPointer) <-- Check address
	fmt.Println("intVal in function:", *intPointer)
}

// PassMoreByReferences shows you that you can pass all of the other primitive
// data types and derefence '*' them and change their values.
func PassMoreByReferences(sPtr *string, bPtr *bool, rPtr *rune, fPtr *float64) {
	// Uncomment and `go test` to see what these values are before you
	// dereference them and change the values at their locations.
	// fmt.Printf("string: %p\nbool: %p\nrune: %p\nfloat: %p\n",
	// 	sPtr, bPtr, rPtr, fPtr)
	// fmt.Println("addresses:", sPtr, bPtr, rPtr, fPtr)
	*sPtr = "Dereferenced and changed"
	*bPtr = true
	*rPtr = '🤡'
	*fPtr = 3.14159
}

type ChangeThings struct {
	Int  int
	Str  string
	Rune rune
}

// PassCollections shows what happens when we try to dereference slices, maps,
// and structs. Structs are special in that they are NOT a pointer like slices
// and maps and therefore need to have a pointer passed in if we want to change
// their inner values.
func PassCollections(slice []string, mp map[string]rune, ctCopy ChangeThings,
	ctPtr *ChangeThings) {

	// With fmt.Printf("%p, %p, %p, %p", slice, mp, ctCopy, ctPtr) you can see
	// the address of all of the arguments, we don't do it here because every
	// time the function executes the address will change! 🤯 and therefore the
	// test would, Never! pass.
	for i := range slice {
		if i == 0 {
			slice[i] = "Dereferenced by `[]` operator. It acts just like `*` operator"
		} else {
			slice[i] = fmt.Sprintf("me %d", i+1)
		}
	}
	for k, v := range mp {
		mp[k] = v % 7 // Dereferenced again with `[]` operator
	}
	ctCopy.Int = 0xF4B1E
	ctCopy.Str = "Changing a copies value doesn't work on the original."
	ctCopy.Rune = '🌞'
	ctPtr.Int = 0xF4B1E
	ctPtr.Str = "Dereferenced with the `.` operator, like `*` and `[]`."
	ctPtr.Rune = '🌞'
	// These lines do nothing, as the function makes a copy of everything it
	// receives. What we really wanted was to release the original collection
	// types to garbage collection. That must be done where it is instantiated.
	// Not when it is passed to a function as a copy.
	slice = nil
	mp = nil
	ctPtr = nil
	// NOTE(jay): won't work
	// ctCopy = nil
	//
	// This makes it more obvious that we have don't have a pointer. A pointer
	// can always be set to nil. We set it to a zero valued ChangeThings struct.
	ctCopy = ChangeThings{}
}
```

## All Of The Outputs To Our Examples

![https://twitter.com/egonelbre](/egon-elbre/typing-furiously.gif "typing-furiously by Egon Elbre")

```go
package pointer_test

import (
	"basics/pointer"
	"fmt"
)

func ExamplePassByValue() {
	intVal := 8
	pointer.PassByValue(intVal)
	fmt.Println("intVal after passing value:", intVal)
	// Output:
	// intVal in function: 100
	// intVal after passing value: 8
}

func ExamplePassByReference() {
	intVal := 8
	intPtr := &intVal
	pointer.PassByReference(intPtr)
	// pointer.PassByReference(&intVal) <-- Also works!
	fmt.Println("intVal after derefence:", intVal)
	// Output:
	// intVal in function: 100
	// intVal after derefence: 100
}

func ExamplePassMoreByReferences() {
	s := "This is going to change"
	b := false
	r := '🔥'
	f := 2.139284094893
	fmt.Printf(`Before changing values:
string value: %q
bool value: %t
rune as emoji value: %s
float value: %f

`, s, b, string(r), f)

	pointer.PassMoreByReferences(&s, &b, &r, &f)
	fmt.Printf(`After changing values:
string value: %q
bool value: %t
rune as emoji value: %s
float value: %f

`, s, b, string(r), f)
	// Output:
	// Before changing values:
	// string value: "This is going to change"
	// bool value: false
	// rune as emoji value: 🔥
	// float value: 2.139284
	//
	// After changing values:
	// string value: "Dereferenced and changed"
	// bool value: true
	// rune as emoji value: 🤡
	// float value: 3.141590
}

func ExamplePassCollections() {
	sl := []string{"Look", "at", "all", "my", "values"}
	mp := map[string]rune{
		"boxing":     '🥊',
		"chestnut":   '🌰',
		"ocean":      '🌊',
		"heart-eyes": '😍',
		"microphone": '🎤',
		"hibiscus":   '🌺',
	}
	asCopy := pointer.ChangeThings{
		Int:  777,
		Str:  "Will I change?",
		Rune: '🌔',
	}
	asCopy2 := pointer.ChangeThings{
		Int:  777,
		Str:  "Will I change?",
		Rune: '🌔',
	}
	asPtr := &asCopy2
	/////////////////////////////////////////////////////////////////////////////
	fmt.Printf(`Before changing values:
slice value: %#v
map value: %#v
copy of struct value: %#v
pointer to struct value: %#v
copy2 of struct value: %#v

`, sl, mp, asCopy, asPtr, asCopy2)

	pointer.PassCollections(sl, mp, asCopy, asPtr)
	/////////////////////////////////////////////////////////////////////////////
	fmt.Printf(`After changing values:
slice value: %#v
map value: %#v
copy of struct value: %#v
pointer to struct value: %#v
copy2 of struct value: %#v

`, sl, mp, asCopy, asPtr, asCopy2)

	/////////////////////////////////////////////////////////////////////////////
	sl = nil
	mp = nil
	asPtr = nil
	asCopy = pointer.ChangeThings{}
	asCopy2 = pointer.ChangeThings{}
	fmt.Printf(`Actually remove all elements from collection types:
pointer: %#v
pointer: %#v
pointer: %#v
literal: %#v
literal: %#v
`, sl, mp, asPtr, asCopy, asCopy2)
	// Output:
	// Before changing values:
	// slice value: []string{"Look", "at", "all", "my", "values"}
	// map value: map[string]int32{"boxing":129354, "chestnut":127792, "heart-eyes":128525, "hibiscus":127802, "microphone":127908, "ocean":127754}
	// copy of struct value: pointer.ChangeThings{Int:777, Str:"Will I change?", Rune:127764}
	// pointer to struct value: &pointer.ChangeThings{Int:777, Str:"Will I change?", Rune:127764}
	// copy2 of struct value: pointer.ChangeThings{Int:777, Str:"Will I change?", Rune:127764}
	//
	// After changing values:
	// slice value: []string{"Dereferenced by `[]` operator. It acts just like `*` operator", "me 2", "me 3", "me 4", "me 5"}
	// map value: map[string]int32{"boxing":1, "chestnut":0, "heart-eyes":5, "hibiscus":3, "microphone":4, "ocean":4}
	// copy of struct value: pointer.ChangeThings{Int:777, Str:"Will I change?", Rune:127764}
	// pointer to struct value: &pointer.ChangeThings{Int:1002270, Str:"Dereferenced with the `.` operator, like `*` and `[]`.", Rune:127774}
	// copy2 of struct value: pointer.ChangeThings{Int:1002270, Str:"Dereferenced with the `.` operator, like `*` and `[]`.", Rune:127774}
	//
	// Actually remove all elements from collection types:
	// pointer: []string(nil)
	// pointer: map[string]int32(nil)
	// pointer: (*pointer.ChangeThings)(nil)
	// literal: pointer.ChangeThings{Int:0, Str:"", Rune:0}
	// literal: pointer.ChangeThings{Int:0, Str:"", Rune:0}
}
```
