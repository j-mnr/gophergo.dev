---
Lesson: 3
Title: "Constant"
Draft: false
---

Constants are variables that cannot be changed.

If you have taken any math or physics classes you've probably used a constant
before. Pi 🥧 `π` is a constant and the speed of light 🔦 `c` is a constant.

Here's a quick overview of why we **love** ❤️ constants as developers:

1. Readability 📖 -- Removes [magic numbers](https://en.wikipedia.org/wiki/Magic_number_(programming)#Unnamed_numerical_constants) from code
1. Flexibility 🧘 -- untyped constants
1. Safety 🦺 -- Never changes and can be used everywhere!
1. Speed 🏃‍️💨 -- The compiler can optimize for us 👍
1. High Precision 🎯 -- We can make **BIG** 👀 numbers

Many times in programming we use numbers to do things for us. For example, we
might want a default formatted `string` for our `fmt.Printf` functions or
logging. You might have a limit to a resource, like the number of
[`goroutines`](/basics/concurrency/23-goroutine) or the number of connections
to a database. A constant is perfect for these situations!

## Magic Numbers 🙅

We as developers we are modern day 🧙‍♀️ mages 🧙‍♂️   We can do lots
of things that normal mortals can't and it's because of our profound knowledge
of the 🦾 technological 🦿 arts. Grokking -- understanding something deeply
through intuition -- is an ability we have. One thing that we can't stand is
unexplained _magic_ 😠 and there's nothing worse than [magic
numbers](https://en.wikipedia.org/wiki/Magic_number_(programming)#Unnamed_numerical_constants)
to make it hard to grok code 😢 Let's look at two examples and you tell me
which one is easier to grok.

{{< side-by-side >}}func calculateCost(food []int) int {
  sum := 0
  if food[0] == 3 {
    sum += 4.49
  } else {
    sum += 2.59
  }
  if food[1] == 'c' {
    sum +=  2.89
  } else if food[1] == 'm' {
    sum += 4.39
  }
  if food[2] == 12 {
    sum += 2.79
  } else {
    sum += 4.59
  }
  return sum
}

func calculateCost(food []int) int {
  sum := 0
  if food[0] == wholeWheatBread {
    sum += wholeWheatPrice
  } else {
    sum += whitePrice
  }
  if food[1] == sharpCheddar {
    sum +=  sharpCheddarPrice
  } else if food[1] == mozzarella {
    sum += mozzarellaPrice
  }
  if food[2] == dozenEggs {
    sum += dozenEggsPrice
  } else {
    sum += eighteenEggsPrice
  }
  return sum
}
{{< /side-by-side >}}

This 👆👆👆 is an extreme example  but it should now be clear just how much
confusion magic numbers can bring. _We_ make magic, not our numbers.

## Allowable Constants

Constants are super useful and seen throughout many codebases. In Go you're
only allowed to declare certain types as constant. Types that can be guaranteed
to not change, can be constant. Many of these values are known as primitive
types. `int`, `rune` (aka `int32`), `byte` (aka `uint8`) `float`, `bool`. The
only exception is a `string` which can also be declared as constant! This can
be confusing if you knew `string` is basically a mutable data structure, a
slice of bytes `[]byte` 😯

Let's expand a little on why a `string` is an exception. We need to understand
that just like `byte` is an alias for `uint8` (unsigned integer with 8 slots),
`string` is an alias for `[]byte` or what's known as a slice of bytes. We
haven't gotten into [slices](/basics/09-slice) yet,  but just know they can
change. In programming when something can change it's called mutable. I'll say
it again -- slices are mutable, slices **can** change. Constants **cannot**
change and are immutable.

Now the question! 🤔 How is a mutable slice of bytes `[]byte` able to be
declared as an immutable value `const`? Well... I lied 🙃 though a `string`
is _basically_ a slice of bytes `[]byte` it holds a little more extra
information than what a normal slice holds **and** guarantees that each `byte`
(the letters and characters) in a string cannot be changed. Therefore the slice
cannot be changed **and** the values inside, the bytes, cannot either. This
guarantees `string` to be immutable.

This may come as a surprise if you've tried to change a `string`, because
you're allowed to do this: `var twoStrings = "string1" + "string2"` and it will
come out as `string1string2` if you `fmt.Println(twoStrings)`

The reason it works is because an **entirely new**❗ `string` is being made in
it's place. Done out of convenience, expectations, or legacy -- it is what it
is. Just know when you add ➕ to a `string` you're making an **entirely new**
`string` and that's expensive 💰💰💰 in programming. Imagine every time you put
your socks 🧦 in a drawer that you had to find another, bigger drawer for each
pair you put in; lots of time 🕗 wasted, lots of resources wasted. When dealing
with creating and appending long running strings you can use
`strings.StringBuilder` 👷 and write various kinds of types to it!

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `constant.go`

```go
// Stuck is an untyped string
const Stuck = "This variable can never be reassigned."

// Stuck = "This won't work, Stuck is constant!"
// NOTE(jay): cannot assign to Stuck (untyped string constant "This variable can
// never be reassigned.")

// HeartEyes is an untyped rune
const HeartEyes = '😍'

// Arithmetic is an untyped float
const Arithmetic = 600 / 3.421

// AlwaysTrue is an untyped bool
const AlwaysTrue = true

// MaxByteValue is a constant byte value. To let the compiler know we wanted a
// byte instead of an int, we can specifically tell it we want that value.
const MaxByteValue byte = 255

// Create a grouping of const values, not needed to type the `const` keyword
// over and over again. This also works for `var`!
const (
	IsConst                   = true
	IsInGrouping              = true
	OneAndQuarterAsUntypedInt = 5 / 4
)

// However you **cannot** declare arrays, slices, maps, or structs constant.
// NOTE(jay): (value of type [2]string) is not constant
// const myArray = [2]string{"won't", "work"}
// const mySlice = []string{"still", "doesn't", "work"}
// const myMap = map[string]int{}
// const me = struct{ name string }{name: "Jay"}
```

#### `example_test.go`

```go
func ExampleStuck() {
	fmt.Println(constant.Stuck)
	// Output:
	// This variable can never be reassigned.
}

func ExampleHeartEyes() {
	// We can convert constants just like variables
	fmt.Println(string(constant.HeartEyes))
	// Output:
	// 😍
}

func ExampleArithmetic() {
	fmt.Println(constant.Arithmetic)
	// Output:
	// 175.38731365097925
}

func ExampleAlwaysTrue() {
	fmt.Println(constant.AlwaysTrue)
	// Output:
	// true
}
```

## _Speed_ 💨 and Precision 🎯 

We can make some big numbers with `const` and I mean **really 🅱IG**. This is
the only time that we are allowed to declare numbers past the unsigned max
integer of 64 bits value. 👀 That's a mouthful, but if you wanted to see that
number in the console in `cmd/main.go` you can put the line
`fmt.Println(uint64(math.MaxUint64))` that will show you the biggest possible
number representable for unsigned integers.

**But**... We can go **BIGGER** 😲 

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `constant.go`

```go
const piThousand = 3.141592653589793238462643383279502884197169399375105820974944592307816406286208998628034825342117067982148086513282306647093844609550582231725359408128481117450284102701938521105559644622948954930381964428810975665933446128475648233786783165271201909145648566923460348610454326648213393607260249141273724587006606315588174881520920962829254091715364367892590360011330530548820466521384146951941511609433057270365759591953092186117381932611793105118548074462379962749567351885752724891227938183011949129833673362440656643086021394946395224737190702179860943702770539217176293176752384674818467669405132000568127145263560827785771342757789609173637178721468440901224953430146549585371050792279689258923542019956112129021960864034418159813629774771309960518707211349999998372978049951059731732816096318595024459455346908302642522308253344685035261931188171010003137838752886587533208381420617177669147303598253490428755468731159562863882353787593751957781857780532171226806613001927876611195909216420198

// BigPreciseConstants shows that the compiler won't complain if you make a
// value that goes for 1000s of digits if it's a constant. Just when it comes
// time to use that constant it must be casted to a type, such as float64.
func BigPreciseConstants() { fmt.Printf("%.64f", float64(piThousand)) }
```

#### `example_test.go`

```go
func ExampleBigPreciseConstants() {
	constant.BigPreciseConstants()
	// Output:
	// 3.1415926535897931159979634685441851615905761718750000000000000000
}
```

## Untyped Constants

Everything in Go has a `type`, unless it's a constant. Why is this important
and what does it mean? Sometimes we want to put certain values into a function
_but_ Go needs us to convert it to a specific `type`. Wouldn't it just be nice
if we didn't have to do that small conversion? Constants actually allow this!

[Enums](/basics/27-enum) in Go are `const` values and therefore have underlying
types that can be converted.

### Coding Time!

![https://twitter.com/egonelbre](/egon-elbre/gopher-heart-eyes.png "gopher-heart-eyes by Egon Elbre")

#### `constant.go`

```go
// UntypedConst shows that constants can have values that will be automatically
// converted to the necessary type that the function needs at runtime.
func UntypedConst() {
	// const values do not have a type and therefore are very useful when you
	// don't want to have to do explicit casting.
	const untyped = 42
	// We don't care what this function does, we only care what it looks like
	// math.IsInf(float64, int)
	fmt.Println(math.IsInf(untyped, untyped))
	// If we try this with typed int we have to cast it.
	var typed int = 42 // or typed := 42
	fmt.Println(math.IsInf(float64(typed), typed))
}
```

#### `example_test.go`

```go
func ExampleUntypedConst() {
	constant.UntypedConst()
	// Output:
	// false
	// false
}
```

This may still be a little confusing as I've pulled in something from the
standard library, that we're not familiar with yet **and** it's math 😬 So
let's do one more! This time _we'll_ make our own `type` 😄

#### `constant.go`

```go
const UntypedString = "I fit wherever the underlying type of something is a string!"

// myString has an underlying type of `string` **but** it's `type` is myString
type myString string

// Print takes in a myString type and prints it to standard out.
func Print(s myString) { fmt.Println(s) }
```

#### `example_test.go`

```go
func ExamplePrint() {
	constant.Print(constant.UntypedString)
	// Output:
	// I fit wherever the underlying type of something is a string!
}
```

## Source File 📄

[The Source File](https://goplay.tools/snippet/GoRjdcb3RFw)

## Test File 📝

[The Test File](https://goplay.tools/snippet/rNCrCVwuHpc)
