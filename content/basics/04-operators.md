---
Lesson: 4
Title: "Operators"
Draft: false
---

The name operator can sound intimidating, but trust me when I say it's really
simple. Lets say we have a soccer player ⚽ and they kick the ball. What would
you call them? A kicker! And what about something that cooks rice? 🍚 A cooker!
And what about a zombie that doesn't do anything else but walk around? A
walker!!!!! 🧟💥🔫💥🔫💥🔫 GET 'EM


I think you see the point. If we have a _thing_ that does _something_
repeatedly we put "-er" on the end of it. So an _operator_ operates on
_something_. It may operate on numbers, booleans, strings, runes, you
name it!

{{% setup-instructions lessonname="operators" %}}

## Comparing Values

Many times in programming you want to be able to compare if two values **are
equal** `==` or if they are **not equal** `!=`; if a value is bigger `>` or
smaller `<` than another one.

These operators operate on two values and return `true` or `false` with what
they get back. You've definitely been a comparison operator in your life.

> Hey, Which one of 🍍 these🍍 is bigger or are they about the same? `>=`
>
> Both of those horror movies are equally bad. `==`
>
> Cartoons and Anime are **_not_** the same!!! `!=`

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `operators.go`

```go
// Comparison shows how to compare values in programming. This is
// essential part of development and for control flow.
func Comparison() {
	const (
		trueVal  = true
		falseVal = false
	)
	fmt.Printf("trueVal == falseVal: %t\n"+
		"Is trueVal equal to falseVal? false\n",
		trueVal == falseVal)
	fmt.Printf("trueVal != falseVal: %t\n"+
		"Is trueVal NOT equal to falseVal? true\n",
		trueVal != falseVal)
	fmt.Println()

	const (
		name1 = "Goober"
		name2 = "Goomba"
	)
	fmt.Printf("name1 == name2: %t\nIs name1 equal to name2? false\n",
		name1 == name2)
	fmt.Printf("name1 != name2: %t\nIs name1 NOT equal to name2? true\n",
		name1 != name2)
	fmt.Println()

	const (
		bigNum  = 100000
		lilNum1 = 10
		lilNum2 = 10
	)
	fmt.Printf("lilNum1 == lilNum2: %t\nIs lilNum1 equal to lilNum2? true\n",
		lilNum1 == lilNum2)
	fmt.Printf("lilNum1 != lilNum2: %t\nIs lilNum1 NOT equal to lilNum2? false\n",
		lilNum1 != lilNum2)
	fmt.Println()

	fmt.Printf("lilNum1 < bigNum: %t\nIs lilNum1 less than bigNum? true\n",
		lilNum1 < bigNum)
	fmt.Printf("bigNum > lilNum2: %t\nIs bigNum greater than lilNum2? true\n",
		bigNum > lilNum2)
	fmt.Printf(
		"lilNum1 <= lilNum2: %t\nIs lilNum1 less than or equal to lilNum2? true\n",
		lilNum1 <= lilNum2)
	fmt.Printf("lilNum1 >= lilNum2: %t\n"+
		"Is lilNum1 greater than or equal to lilNum2? true\n",
		lilNum1 >= lilNum2)
}
```

#### `example_test.go`

```go
func ExampleComparison() {
	operators.Comparison()
	// Output:
	// trueVal == falseVal: false
	// Is trueVal equal to falseVal? false
	// trueVal != falseVal: true
	// Is trueVal NOT equal to falseVal? true
	//
	// name1 == name2: false
	// Is name1 equal to name2? false
	// name1 != name2: true
	// Is name1 NOT equal to name2? true
	//
	// lilNum1 == lilNum2: true
	// Is lilNum1 equal to lilNum2? true
	// lilNum1 != lilNum2: false
	// Is lilNum1 NOT equal to lilNum2? false
	//
	// lilNum1 < bigNum: true
	// Is lilNum1 less than bigNum? true
	// bigNum > lilNum2: true
	// Is bigNum greater than lilNum2? true
	// lilNum1 <= lilNum2: true
	// Is lilNum1 less than or equal to lilNum2? true
	// lilNum1 >= lilNum2: true
	// Is lilNum1 greater than or equal to lilNum2? true
}
```
## Logical Operators 🧠

These operators add reason to your code. They pair great with the comparison
operators as we'll find out when we code them up. Again, don't let the name
fool you, it's nothing you don't use in everyday life. Let's take some real
world examples and put that into developer talk.

### (AND, &&)

> If it's Tuesday AND it's sunny ☀️ out, I'll go for a jog 🏃
>
> `isTuesday && isSunny` --> jog

It **must** be Tuesday and sunny, if **either** is false, you're not going for
a run.

### (OR, ||)

> We can watch that new scary movie OR our favorite rom-com, what do you think?
>
> We can `watchScaryMovie || watchRomCom` --> watch a movie

Here there is a choice, maybe there are both movies 🎥, or maybe you decide on
one or the other, but in all three of those decisions you **will** watch a
movie. In other words **as long as one is true, the statements above are
true.**

What happens if you can't make it to the movies to see the new scary movie 😱
and your best friend "borrowed" 😑 your movie? Then you won't watch anything!
And therefore it would be **false if there is no scary movie and no rom-com.**

### (NOT, !)

> As long as there are NOT anchovies 🐟 on the pizza🍕 I can eat it.
>
> as long as `!hasAnchovies` --> eat pizza

There are times when you want the opposite of a value. It's essential in every
day speech, just like it is in development. Imagine not 👈having words to
negate sentences! That wouldn't 👈work well🙃 I just used not 👈twice 😂

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `operators.go`

```go
// Logical shows how to introduce more granular logic into your
// program with the logical operators.
func Logical() {
	theAnswer := '*'
	toTheUniverse := 42
	percentC := 42
	percentD := 42
	// BOTH statements must evaluate to the `bool` true or it will output false.
	fmt.Printf("Is the answer to the universe as rune %c or as int %d? %t\n",
		percentC, percentD, theAnswer == 42 && toTheUniverse == '*')
	fmt.Printf("Is 84 NOT equal to 84 AND something true? %t\n",
		84 != 84 && true)
	fmt.Println()

	this := "Some Value"
	that := "Other Value"
	lastRuneThis := this[len(this)-1]
	lastRuneThat := that[len(that)-1]
	// One of the statements must be true for the output to be true.
	fmt.Printf("Is one of these statements true?\n"+
		`1. %q == "Some Value" OR 2. %q == "DOESN'T MATTER"`+
		"?\nAnswer: %t\n", this, that,
		this == "Some Value" || that == "DOESN'T MATTER")

	fmt.Printf("If the second one is true?\n"+
		`1. %q == "FALSE" OR 2. %q == "Other Value"`+
		"?\nAnswer: %t\n", this, that,
		this == "Some Value" || that == "DOESN'T MATTER")

	fmt.Printf("How many can we use?\n"+
		"1. %q == %q OR 2. %c == %c OR 3. %d == %d OR 4. %c == %c\n Answer: %t\n",
		this, that, this[0], that[0], 64, 640, lastRuneThis, lastRuneThat,
		this == that || this[0] == that[0] || 42+24 == 640 || lastRuneThis == lastRuneThat)
	fmt.Println()

	goodFood := true
	badSmell := false
	fmt.Printf("Would you eat goodFood? %t\nWhat if it smelled bad? %t\n",
		goodFood, badSmell)
	fmt.Printf(
		"Would you eat NOT goodFood? %t\nWhat if it did NOT smell bad? %t\n",
		!goodFood, !badSmell)

	fmt.Printf(
		"🤔 What if it was NOT goodFood, but it did NOT smellBad? %t\n",
		!goodFood || !badSmell)

	fmt.Printf(
		"🤔 What if it was goodFood AND it did NOT smellBad? %t\n",
		goodFood && !badSmell)
}
```

#### `example_test.go`

```go
func ExampleLogical() {
	operators.Logical()
	// Output:
	// Is the answer to the universe as rune * or as int 42? true
	// Is 84 NOT equal to 84 AND something true? false
	//
	// Is one of these statements true?
	// 1. "Some Value" == "Some Value" OR 2. "Other Value" == "DOESN'T MATTER"?
	// Answer: true
	// If the second one is true?
	// 1. "Some Value" == "FALSE" OR 2. "Other Value" == "Other Value"?
	// Answer: true
	// How many can we use?
	// 1. "Some Value" == "Other Value" OR 2. S == O OR 3. 64 == 640 OR 4. e == e
	//  Answer: true
	//
	// Would you eat goodFood? true
	// What if it smelled bad? false
	// Would you eat NOT goodFood? false
	// What if it did NOT smell bad? true
	// 🤔 What if it was NOT goodFood, but it did NOT smellBad? true
	// 🤔 What if it was goodFood AND it did NOT smellBad? true
}
```

## Arithmetic (Math) Operators ➕ ➖ ➗ ✖️

Yay! Math... 😬😂 You'll find it no surprise that all your favorite math
operators are back in action! And the same as ever! Which is good in this case.
What if I told you when you did `2 + 2` in Go it came out to `22`? You'd be
pretty angry 😡 I'd imagine.

There is one important operator that you've probably never heard of or seen in
math class. At least _I_ never saw it in math class. It's the modulo `%`
operator. It **only** works on `int` types. That's because it's job is to give
you the remainder as a whole number. Another way you can think of it is lazy
😪. If I asked you:

> What's left if you divide 10 by three? `10 / 3`

You would think 🤔

> Ten divided by three 💭 `10 - 3 = 7; 7 - 3 = 4; 4 - 3 = 1; 1!` One! The
> answer is one!

and there you have it. The modulo `%` operator and by the way, we software
engineers _despise_ words longer than 5 characters. Show us a long word and
we'll find a way to shorten it, no doubt 😂. It's almost weird hearing it
called by it's full name and that's because I would say something like:

> What's 167 mod 8?

You may be wondering why there is an **entire** operator dedicated to something
you've probably never heard of, yeah? It's a good question to have 😁👍 The
reason it's so useful is because it lets you loop ➿ numbers round and round;
more on loops in a later lesson. But think of it this way what numbers can come
out of

`[10, 9, 8, 7, 6, 5, 4, 3, 2, 1, 0] % 3`?

`[ 1, 0, 2, 1, 0, 2, 1, 0, 2, 1, 0]` 👈

We've made sure we never go past 2! Pretty cool right ⁉️ 😁

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `operators.go`

```go
// Arithmetic shows that all of the operators you know and love ❤️ from
// math class 🫠 still work the same as in class.
func Arithmetic() {
	var (
		myInt     = 21
		myFloat   = 6.28
		myComplex = -5 + 2i
	)
	fmt.Printf("nine plus ten does NOT equal twenty-one\n"+
		"9 + 10 != %d: %t\n", myInt, 9+10 != myInt)
	fmt.Printf("Is pi greater than 3?\n"+
		"%.2f - 3.14 >= 3: %t\n", myFloat, myFloat-3.14 >= 3)
	fmt.Printf("my head hurts 🥴\n%.3g * %.3g = %.3g\n",
		myComplex, myComplex, myComplex*myComplex)
	fmt.Printf("tau divided by tau is one\n"+
		"%.4f / %.4f: %.4f", myFloat, myFloat, myFloat/myFloat)
	fmt.Println()

	// ONLY FOR THE INTS!!
	// 21 % 5 == 21 - 5 - 5 - 5 - 5 == 1
	fmt.Printf("%d %% 5 == 1\n"+
		"What whole number remains when I try to divide %d by 5? %d\n",
		myInt, myInt, myInt%5)

	// 21 % 4 == 21 - 4 - 4 - 4 - 4 - 4 == 1
	fmt.Printf("%d %% 4 == 1\n"+
		"What whole number remains when I try to divide %d by 4? %d\n",
		myInt, myInt, myInt%4)

	// 21 % 3 == 21 - 3 - 3 - 3 - 3 - 3 - 3 - 3 == 0
	fmt.Printf("%d %% 3 == 0\n"+
		"What whole number remains when I try to divide %d by 3? %d\n",
		myInt, myInt, myInt%3)

	// 21 % 21 == 21 - 21 == 0
	fmt.Printf("%d %% 21 == 0\n"+
		"What whole number remains when I try to divide %d by 21? %d\n",
		myInt, myInt, myInt%21)
}
```

#### `example_test.go`

```go
func ExampleArithmetic() {
	operators.Arithmetic()
	// Output:
	// nine plus ten does NOT equal twenty-one
	// 9 + 10 != 21: true
	// Is pi greater than 3?
	// 6.28 - 3.14 >= 3: true
	// my head hurts 🥴
	// (-5+2i) * (-5+2i) = (21-20i)
	// tau divided by tau is one
	// 6.2800 / 6.2800: 1.0000
	// 21 % 5 == 1
	// What whole number remains when I try to divide 21 by 5? 1
	// 21 % 4 == 1
	// What whole number remains when I try to divide 21 by 4? 1
	// 21 % 3 == 0
	// What whole number remains when I try to divide 21 by 3? 0
	// 21 % 21 == 0
	// What whole number remains when I try to divide 21 by 21? 0
}
```

## Concatenation (String) Operator

The word concatenation is a really fancy way of saying link 🔗 things. We can
link things together (or _add_ ➕ them) and that's the same as saying we have
concatenated them. So string concatenation is adding strings together.

If you have "cat" and you have "dog" what happens when you "cat" ➕ "dog"❓
"catdog"! 🐱🐶

Note that it's the only one that works. There is no subtracting or dividing or
multiplying strings. The reason? Let's use an example: How do you multiply
`"cat" * "dog"`? 🙀 I rest my case 😹

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `operators.go`

```go
// String shows the only operator that works with the `string` type,
// which is the `+` operator or concatenation
func String() {
	const blt = "Bacon🥓Lettuce🥬Tomato🍅"
	var (
		t  = "Tomato🍅"
		bl = "Bacon🥓"
		l  = "Lettuce🥬"
	)
	// bl = bl + l
	bl += l
	fmt.Printf("%s == %s: %t\nIs blt equal to bl+t? true\n",
		blt, bl+t, blt == bl+t)
	fmt.Printf("%s != %s: %t\nIs blt NOT equal to bl? true\n",
		blt, bl, blt == bl)
}
```

#### `example_test.go`

```go
func ExampleString() {
	operators.String()
	// Output:
	// Bacon🥓Lettuce🥬Tomato🍅 == Bacon🥓Lettuce🥬Tomato🍅: true
	// Is blt equal to bl+t? true
	// Bacon🥓Lettuce🥬Tomato🍅 != Bacon🥓Lettuce🥬: false
	// Is blt NOT equal to bl? true
}
```

## Bitwise Operators

⚠️  WARNING ⚠️ **Big** Nerd 🤓 talk coming up. If you don't get this don't worry.
It took me a **SOLID** 4 months of continuously practicing these when I first
started to understand what's going on. I can't cover the fundamentals of bits
and bytes and if you're missing that, you'll need to understand it before these
operators are going to make sense.

These operators are _rarely_ used in Backend applications. They are useful when
you are trying to squeeeeeze out every single iota of performance. Efficiency
matters with things like games 🎮 or cryptography🔑, creating compilers, or
even creating database engines. So if you're looking to get into those fields
you might want to look into this deeper and listen 👂 up!

You'll notice in Go we can represent numbers in their binary format by
appending `0b` to the front of the number. This was done with purpose because
you can do `40 & 42` but when a number comes out, you'll be wildly confused how
you got there. So _instead_ lets see the bits flip!

Many of the bitwise family do the same thing as their logical cousins. That's
why such a strong corollary can be derived from where bitwise and logical
operators get their names. I'll give a brief human example of each of the
operators, then we'll get into the code.

### (AND, &&, &)

There is no way to subtract `-` bits like we understand in math, **but** an `&`
_will_ remove bits if they aren't both `1` and therefore acts like
subtraction.

> Give me one bulbasaur AND one charmander or you don't get one pikachu!
>
> Give me `bulbasaur && charmander` --> `pikachu`
>
> Give me `1 & 1` --> `1`, `0 & 1` --> `0`, `1 & 0` --> `0`, `0 & 0` --> `0`

### (OR, ||, |)

We cannot use the `+` plus sign with bits, **but** we can add more bits with
`|` because it favors a `1` over `0`. This is how we do addition in bitland 👾

> Get me a burger 🍔 OR a slice of pizza 🍕 --> 🍔
>
> Get me `🍔 || 🍕` --> 🍔
>
> Get me `1 | 0` --> `1`, `0 | 1` --> `1`, `0 | 0` --> `0`, `1 | 1` --> `1`

### (XOR, ! with &&, ^)

We can use `^` to flip 🙃 bits 🙂

> Grab a ticket 🎟️ if we have money💸 AND there's no line
>
> Grab `(money && !line)` --> 🎟️
>
> Grab `1 ^ 0` --> `1`, `1 ^ 1` --> `0`, `0 ^ 0` --> `0`, `0 ^ 1` --> `1`

### Bit Clear aka BOOM BOOM

Lastly know there is an operator `&^` that will clear 💣 💥 some bits for you.
There's no good real life example to give, but the code explains what it does,
so Let's Go! 😀

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `operators.go`

```go
// Bitwise shows how to manipulate the bits of integer types:
// `int`, int8`, `int16`, `int32`, `int64`,
// `uint`, uint8`, `uint16`, `uint32`, `uint64`,
func Bitwise() {
	// bitwise AND -- & -- works just like logical AND -- &&
	// BOTH statements must be true to output true otherwise it's false
	// BOTH numbers must be one to output one otherwise it's zero
	fmt.Printf("1011111101 &\n1101010011 ==\n%b\nBonus as an int %d\n",
		0b1011111101&0b1101010011, 0b1011111101&0b1101010011)
	fmt.Println()

	// bitwise OR -- | -- works just like logical OR -- ||
	// One of the statements must be true to output true otherwise it's false
	// One of the numbers must be one to output one otherwise it's zero
	fmt.Printf("1111100000 |\n0000011111 ==\n%b\nBonus as an int %d\n",
		0b1111100000|0b0000011111, 0b1111100000|0b0000011111)
	fmt.Println()

	// ^    bitwise XOR            integers
	// bitwise XOR -- ^ -- or more formally eXclusive OR
	// works like the logical NOT -- ! -- operator with two statements.
	// Logically it is the equivalent of saying:
	//   One of the statements is false AND one of the statements is true
	//   Then I will output true; i.e. if both statements are NOT exclusive
	//   Then I'm going to output false.
	// If both numbers are 0 --> 0
	// If both numbers are 1 --> 0
	// If one number is 1 and the other number is 0 --> 1
	fmt.Printf("010111110000 ^\n101011110000 ==\n%b\nBonus as an int %d\n",
		0b010111110000^0b101011110000, 0b010111110000^0b101011110000)
	fmt.Println()

	// bit clear -- &^ -- is used to NUKE 💣 a bit.
	// When you want a specific bit or set of bits gone you can use bit clear
	// It is logically the same as NOT -- ! -- and AND -- && -- together
	// ! (true && true) --> ! (true) --> false
	// ! (1 && 1) --> ! (1) --> 0
	fmt.Printf("111111111111 &^\n000001111000 ==\n%b\nBonus as an int %d\n",
		0b111111111111&^0b000001111000, 0b111111111111&^0b000001111000)
	fmt.Println()

	// shifting bits with left shift -- << -- and right shift -- >> --
	// moves all bits by a given amount.
	fmt.Printf("Shift all bits one left\n"+
		"0100100101 << 1 ==\n%b\nBonus as an int %d\n",
		0b0100100101<<1, 0b0100100101<<1)
	fmt.Printf("Shift all bits two right\n"+
		"0100100101 >> 2 ==\n%010b\nBonus as an int %d\n",
		0b0100100101>>2, 0b0100100101>>2)
}
```

#### `example_test.go`

```go
func ExampleBitwise() {
	operators.Bitwise()
	//Output:
	// 1011111101 &
	// 1101010011 ==
	// 1001010001
	// Bonus as an int 593
	//
	// 1111100000 |
	// 0000011111 ==
	// 1111111111
	// Bonus as an int 1023
	//
	// 010111110000 ^
	// 101011110000 ==
	// 111100000000
	// Bonus as an int 3840
	//
	// 111111111111 &^
	// 000001111000 ==
	// 111110000111
	// Bonus as an int 3975
	//
	// Shift all bits one left
	// 0100100101 << 1 ==
	// 1001001010
	// Bonus as an int 586
	// Shift all bits two right
	// 0100100101 >> 2 ==
	// 0001001001
	// Bonus as an int 73
}
```

## Source File 📄

[The Source File](https://goplay.tools/snippet/4JTLktfai-c)

## Test File 📝

[The Test File](https://goplay.tools/snippet/OD1kLawe2fE)
