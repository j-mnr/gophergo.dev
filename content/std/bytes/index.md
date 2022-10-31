---
Title: "bytes"
Draft: false
---

## Source Code

🛝 [Playground link](https://goplay.tools/snippet/VvSTEKHthuR)

## Other packages used

- [`errors`]()
- [`fmt`]()
- [`io`]()
- [`unicode`]()
- [`golang.org/x/text/cases`]()
- [`golang.org/x/text/language`]()

## Constants and Variables

```go
const MinRead = 512

var ErrTooLarge = errors.New("bytes.Buffer: too large")
```

## Examples

### `MinRead`

```go
func ExampleMinRead() {
	var buf bytes.Buffer
	fmt.Printf("len: %d; cap: %d\n", buf.Len(), buf.Cap())
	buf.ReadFrom(bytes.NewReader([]byte(" "))) // Even passing empty increases capacity
	fmt.Printf("len: %d; cap: %d\n\n", buf.Len(), buf.Cap())

	var enuf bytes.Buffer
	enuf.Grow(bytes.MinRead + 64) // Grow's lowest amount is 64 bytes
	fmt.Printf("len: %d; cap: %d\n", enuf.Len(), enuf.Cap())
	enuf.ReadFrom(bytes.NewReader([]byte(" ")))
	fmt.Printf("len: %d; cap: %d\n\n", enuf.Len(), enuf.Cap())
	// Output:
	// len: 0; cap: 0
	// len: 1; cap: 1024
	//
	// len: 0; cap: 576
	// len: 1; cap: 576
}
```


### `ErrTooLarge`

```go
func ExampleErrTooLarge() {
	// NOTE(jay): See [bytes.Buffer.Grow] for another example.
	var buf bytes.Buffer
	defer func() {
		if r := recover(); r != nil {
			switch err := r.(type) {
			case error:
				if errors.Is(err, bytes.ErrTooLarge) {
					fmt.Printf("it's too big: %v\n", err)
				}
			default:
				fmt.Println(r)
			}
		}
	}()
	buf.Grow(1 << 62)
	// Output:
	// it's too big: bytes.Buffer: too large
}
```


### `Compare`

```go
// https://github.com/pingcap/tidb/blob/00791e7968ffad2de33d7af7f6f8a21580e2ab7e/ddl/cluster.go#L280
// https://github.com/ethereum/go-ethereum/blob/fb75f11e87420ec25ff72f7eeeb741fa8974e87e/trie/proof.go#L251-L258
// https://github.com/kubernetes/kubernetes/blob/61ca612cbb85efa13444a6d8ae517cd5e9c151a4/pkg/api/v1/endpoints/util.go#L177
// https://github.com/cockroachdb/cockroach/blob/1c377714b802ae9b354344a81f2611a96eb3255f/pkg/storage/pebble.go#L113
// https://github.com/hashicorp/vault/blob/6bede501766490007fb8d9323b9a37ef2883c07b/helper/dhutil/dhutil.go#L70
func ExampleCompare() {
	a4b := []byte("aaaab")
	a3b := []byte("aaab")
	a2 := []byte("aa")

	tellMe := func(a, b []byte) {
		switch bytes.Compare(a, b) {
		case -1:
			fmt.Printf("🤏 %q\thas a lexical score less than %q\n", a, b)
		case 0:
			fmt.Printf("🟰 %q\thas a lexical score that equals %q\n", a, b)
		case 1:
			fmt.Printf("💪 %q\thas a lexical score greater than %q\n", a, b)
		}
	}
	tellMe(a4b, a3b)
	tellMe(a2, a3b)
	tellMe(a4b, a2)
	tellMe(a2, a2)
	// Output:
	// 🤏 "aaaab"	has a lexical score less than "aaab"
	// 🤏 "aa"	has a lexical score less than "aaab"
	// 💪 "aaaab"	has a lexical score greater than "aa"
	// 🟰 "aa"	has a lexical score that equals "aa"
}
```

### `Contains`

```go
func ExampleContains() {
	data := []byte("My dentist tells me that chewing bricks is very bad for your teeth.")
	findOut := func(a, b []byte) {
		switch bytes.Contains(a, b) {
		case true:
			fmt.Printf("✅ %q contains %q\n", a, b)
		case false:
			fmt.Printf("❌ %q does NOT contain %q\n", a, b)
		}
	}
	findOut(data, []byte("my"))
	findOut(data, []byte("brushing"))
	findOut(data, []byte("wing"))
	findOut(data, []byte("hat"))
	findOut(data, []byte("My"))
	// Output:
	// ❌ "My dentist tells me that chewing bricks is very bad for your teeth." does NOT contain "my"
	// ❌ "My dentist tells me that chewing bricks is very bad for your teeth." does NOT contain "brushing"
	// ✅ "My dentist tells me that chewing bricks is very bad for your teeth." contains "wing"
	// ✅ "My dentist tells me that chewing bricks is very bad for your teeth." contains "hat"
	// ✅ "My dentist tells me that chewing bricks is very bad for your teeth." contains "My"
}
```

### `ContainsAny`

```go
func ExampleContainsAny() {
	data := []byte("At that moment I was the most fearsome weasel in the entire swamp.")
	findOut := func(a []byte, chars string) {
		switch bytes.ContainsAny(a, chars) {
		case true:
			fmt.Printf("✅ %q contains one of these characters %q\n", a, chars)
		case false:
			fmt.Printf("❌ %q does NOT contain any of these characters %q\n", a, chars)
		}
	}

	findOut(data, "stuff")
	findOut(data, "x")
	findOut(data, "XXX")
	findOut(data, " ")
	// Output:
	// ✅ "At that moment I was the most fearsome weasel in the entire swamp." contains one of these characters "stuff"
	// ❌ "At that moment I was the most fearsome weasel in the entire swamp." does NOT contain any of these characters "x"
	// ❌ "At that moment I was the most fearsome weasel in the entire swamp." does NOT contain any of these characters "XXX"
	// ✅ "At that moment I was the most fearsome weasel in the entire swamp." contains one of these characters " "
}
```

### `ContainsRune`

```go
func ExampleContainsRune() {
	data := []byte{0xf0, 0x9f, 0x98, 0x81}
	findOut := func(a []byte, r rune) {
		switch bytes.ContainsRune(a, r) {
		case true:
			fmt.Printf("✅ %q contains %q\n", a, r)
		case false:
			fmt.Printf("❌ %q does NOT contain %q\n", a, r)
		}
	}
	findOut(data, '😁')
	findOut(data, 0x1f601)
	findOut(data, '\U0001f601')
	findOut(data, '\u274c')
	// Output:
	// ✅ "😁" contains '😁'
	// ✅ "😁" contains '😁'
	// ✅ "😁" contains '😁'
	// ❌ "😁" does NOT contain '❌'
}
```

### `Count`

```go
func ExampleCount() {
	data := []byte("It isn't true that my mattress is made of cotton candy.")
	emojis := []byte("🫶🙌👌👍🔥🙃😎😁🤗")
	howMany := func(a, b []byte) {
		fmt.Printf("there are %d of %q in %q\n", bytes.Count(a, b), b, a)
	}
	howMany(data, []byte("t"))
	howMany(data, []byte("is"))
	howMany(data, []byte(""))
	howMany(emojis, []byte{0xf0})
	howMany(emojis, []byte(""))
	// Output:
	// there are 9 of "t" in "It isn't true that my mattress is made of cotton candy."
	// there are 2 of "is" in "It isn't true that my mattress is made of cotton candy."
	// there are 56 of "" in "It isn't true that my mattress is made of cotton candy."
	// there are 9 of "\xf0" in "\U0001faf6🙌👌👍🔥🙃😎😁🤗"
	// there are 10 of "" in "\U0001faf6🙌👌👍🔥🙃😎😁🤗"
}
```

### `Cut`

```go
func ExampleCut() {
	data := []byte("Today we gathered moss for my uncle's wedding.")
	split := func(a, b []byte) {
		be, af, found := bytes.Cut(a, b)
		switch found {
		case true:
			fmt.Printf("✅ before: %q, after: %q, found: %t\n", be, af, found)
		case false:
			fmt.Printf("❌ could NOT find %q in %q:\n\tbefore: %q, after: %q\n", b, a, be, af)
		}
	}
	split(data, []byte("my"))
	split(data, []byte(" "))
	split(data, []byte("XXX"))
	// Output:
	// ✅ before: "Today we gathered moss for ", after: " uncle's wedding.", found: true
	// ✅ before: "Today", after: "we gathered moss for my uncle's wedding.", found: true
	// ❌ could NOT find "XXX" in "Today we gathered moss for my uncle's wedding.":
	// 	before: "Today we gathered moss for my uncle's wedding.", after: ""
}
```

### `Equal`

```go
func ExampleEqual() {
	data := []byte("Peter found road kill an excellent way to save money on dinner.")
	same := func(a, b []byte) {
		switch bytes.Equal(a, b) {
		case true:
			fmt.Printf("✅ %q is equal to %q\n", a, b)
		case false:
			fmt.Printf("❌ %q is NOT equal to %q\n", a, b)
		}
	}
	same([]byte(""), nil)
	same([]byte(""), []byte(""))
	same([]byte{}, []byte(""))
	same(data, []byte("Peter found road kill an excellent way to save money on dinner."))
	same(data, []byte("peter found road kill an excellent way to save money on dinner."))
	// Output:
	// ✅ "" is equal to ""
	// ✅ "" is equal to ""
	// ✅ "" is equal to ""
	// ❌ "Peter found road kill an excellent way to save money on dinner." is NOT equal to "Peter found road kill an excellent way to save\u00a0money on dinner."
	// ❌ "Peter found road kill an excellent way to save money on dinner." is NOT equal to "peter found road kill an excellent way to save money on dinner."
}
```

### `EqualFold`

```go
func ExampleEqualFold() {
	data := []byte("There's probably enough glass in my cupboard to build an undersea aquarium.")
	same := func(a, b []byte) {
		switch bytes.EqualFold(a, b) {
		case true:
			fmt.Printf("✅ %q is case-insensitively equal to %q\n", a, b)
		case false:
			fmt.Printf("❌ %q is NOT case-insensitively equal to %q\n", a, b)
		}
	}
	same([]byte(""), nil)
	same([]byte(""), []byte(""))
	same([]byte{}, nil)
	same([]byte("aaa"), []byte("AAA"))
	same(data, []byte("THERE'S pRoBabLY eNOuGh GlAsS iN my CuPBOaRd To BuILd AN uNdErSeA AqUaRiUM."))
	// Output:
	// ✅ "" is case-insensitively equal to ""
	// ✅ "" is case-insensitively equal to ""
	// ✅ "" is case-insensitively equal to ""
	// ✅ "aaa" is case-insensitively equal to "AAA"
	// ✅ "There's probably enough glass in my cupboard to build an undersea aquarium." is case-insensitively equal to "THERE'S pRoBabLY eNOuGh GlAsS iN my CuPBOaRd To BuILd AN uNdErSeA AqUaRiUM."
}
```

### `Fields`

```go
func ExampleFields() {
	// NOTE(jay): All acceptable field separators
	// 	'\t', '\n', '\v', '\f', '\r', ' ', U+0085 (NEL), U+00A0 (NBSP).
	fields := func(a []byte) {
		fmt.Println("process fields:")
		for i, f := range bytes.Fields(a) {
			fmt.Printf("%d: %q\n", i, f)
		}
	}
	fields([]byte("I'm\tworried\nby\vthe\ffact\rthat my\u0085daughter\u00a0looks to the local\r\n\r\ncarpet\t\t\t\t\tseller                   as a role model."))
	fields([]byte("   \t \n \v \f \r \u0085 \u00a0        "))
	// Output:
	// process fields:
	// 0: "I'm"
	// 1: "worried"
	// 2: "by"
	// 3: "the"
	// 4: "fact"
	// 5: "that"
	// 6: "my"
	// 7: "daughter"
	// 8: "looks"
	// 9: "to"
	// 10: "the"
	// 11: "local"
	// 12: "carpet"
	// 13: "seller"
	// 14: "as"
	// 15: "a"
	// 16: "role"
	// 17: "model."
	// process fields:
}
```

### `FieldsFunc`

```go
func ExampleFieldsFunc() {
	dateSep := func(a []byte) {
		fmt.Println("process fields:")
		for i, f := range bytes.FieldsFunc(a, func(r rune) bool {
			return r == '-' || r == '/' || unicode.IsSpace(r)
		}) {
			fmt.Printf("%d: %q\n", i, f)
		}
	}
	dateSep([]byte("8/1/33"))
	dateSep([]byte("8-1-33"))
	dateSep([]byte("8-1-33 12-20-20    03/04/22 \t 06-/06-/07 "))
	// Output:
	// process fields:
	// 0: "8"
	// 1: "1"
	// 2: "33"
	// process fields:
	// 0: "8"
	// 1: "1"
	// 2: "33"
	// process fields:
	// 0: "8"
	// 1: "1"
	// 2: "33"
	// 3: "12"
	// 4: "20"
	// 5: "20"
	// 6: "03"
	// 7: "04"
	// 8: "22"
	// 9: "06"
	// 10: "06"
	// 11: "07"
}
```

### `HasPrefix`

```go
func ExampleHasPrefix() {
	data := []byte("I want a giraffe, but I'm a turtle eating waffles.")
	prefixed := func(a, b []byte) {
		switch bytes.HasPrefix(a, b) {
		case true:
			fmt.Printf("✅ %q has %q as a prefix\n", a, b)
		case false:
			fmt.Printf("❌ %q does NOT have %q as a prefix\n", a, b)
		}
	}
	prefixed(data, []byte("I want"))
	prefixed(data, []byte("I want waffle fries"))
	prefixed(data, []byte("I want a giraffe, but I'm a turtle eating waffles."))
	// Output:
	// ✅ "I want a giraffe, but I'm a turtle eating waffles." has "I want" as a prefix
	// ❌ "I want a giraffe, but I'm a turtle eating waffles." does NOT have "I want waffle fries" as a prefix
	// ✅ "I want a giraffe, but I'm a turtle eating waffles." has "I want a giraffe, but I'm a turtle eating waffles." as a prefix
}
```

### `HasSuffix`

```go
func ExampleHasSuffix() {
	data := []byte("If my calculator had a history, it would be more embarrassing than my browser history.")
	suffixed := func(a, b []byte) {
		switch bytes.HasSuffix(a, b) {
		case true:
			fmt.Printf("✅ %q has %q as a suffix\n", a, b)
		case false:
			fmt.Printf("❌ %q does NOT have %q as a suffix\n", a, b)
		}
	}
	suffixed(data, []byte("history."))
	suffixed(data, []byte("embarrassing my browser history."))
	suffixed(data, []byte("If my calculator had a history, it would be more embarrassing than my browser history."))
	// Output:
	// ✅ "If my calculator had a history, it would be more embarrassing than my browser history." has "history." as a suffix
	// ❌ "If my calculator had a history, it would be more embarrassing than my browser history." does NOT have "embarrassing my browser history." as a suffix
	// ✅ "If my calculator had a history, it would be more embarrassing than my browser history." has "If my calculator had a history, it would be more embarrassing than my browser history." as a suffix
}
```

### `Index`

```go
func ExampleIndex() {
	data := []byte("He felt that dining on the bridge brought romance to his relationship with his cat.")
	idxOf := func(a, b []byte) {
		i := bytes.Index(a, b)
		if i == -1 {
			fmt.Printf("❌ %q could not be found in %q, index is %d\n", b, a, i)
			return
		}
		fmt.Printf("✅ %q starts at index: %d of %q\n\tyield: %q\n", b, i, a, a[i:])
	}
	idxOf(data, []byte("H"))
	idxOf(data, []byte("He"))
	idxOf(data, []byte("he"))
	idxOf(data, []byte("cat"))
	idxOf(data, []byte("dog"))
	// Output:
	// ✅ "H" starts at index: 0 of "He felt that dining on the bridge brought romance to his relationship with his cat."
	// 	yield: "He felt that dining on the bridge brought romance to his relationship with his cat."
	// ✅ "He" starts at index: 0 of "He felt that dining on the bridge brought romance to his relationship with his cat."
	// 	yield: "He felt that dining on the bridge brought romance to his relationship with his cat."
	// ✅ "he" starts at index: 24 of "He felt that dining on the bridge brought romance to his relationship with his cat."
	// 	yield: "he bridge brought romance to his relationship with his cat."
	// ✅ "cat" starts at index: 79 of "He felt that dining on the bridge brought romance to his relationship with his cat."
	// 	yield: "cat."
	// ❌ "dog" could not be found in "He felt that dining on the bridge brought romance to his relationship with his cat.", index is -1
}
```

### `IndexAny`

```go
func ExampleIndexAny() {
	data := []byte("Shakespeare was a famous 17th-century diesel mechanic.")
	findAny := func(a []byte, chars string) {
		i := bytes.IndexAny(a, chars)
		if i == -1 {
			fmt.Printf("❌ %q does NOT contain any of these characters %q\n", a, chars)
			return
		}
		fmt.Printf("✅ %q contains one of these characters %q at index: %d and is %q\n",
			a, chars, i, a[i])
	}
	findAny(data, "Smechanic")
	findAny(data, "")
	findAny(data, "zy18*/q")
	findAny(data, "zx8*/q")
	// Output:
	// ✅ "Shakespeare was a famous 17th-century diesel mechanic." contains one of these characters "Smechanic" at index: 0 and is 'S'
	// ❌ "Shakespeare was a famous 17th-century diesel mechanic." does NOT contain any of these characters ""
	// ✅ "Shakespeare was a famous 17th-century diesel mechanic." contains one of these characters "zy18*/q" at index: 25 and is '1'
	// ❌ "Shakespeare was a famous 17th-century diesel mechanic." does NOT contain any of these characters "zx8*/q"
}
```

### `IndexByte`

```go
func ExampleIndexByte() {
	data := []byte("It's not often you find a soggy banana on the street.")
	byteMe := func(a []byte, b byte) {
		i := bytes.IndexByte(a, b)
		if i == -1 {
			fmt.Printf("%q does not have byte: %q\n", a, b)
			return
		}
		fmt.Printf("%q is found at %d of %q and yields: %q\n", b, i, a, a[i:])
	}
	byteMe(data, 'b')
	byteMe(data, 'x')
	// Output:
	// 'b' is found at 32 of "It's not often you find a soggy banana on the street." and yields: "banana on the street."
	// "It's not often you find a soggy banana on the street." does not have byte: 'x'
}
```

### `IndexFunc`

```go
func ExampleIndexFunc() {
	data := []byte("I caught my squirrel rustling through my gym bag.")
	nonPrintable := func(a []byte) {
		i := bytes.IndexFunc(a, func(r rune) bool {
			return !unicode.IsPrint(r)
		})
		if i == -1 {
			fmt.Printf("❌ %q does NOT contain any non-printable characters\n", a)
			return
		}
		fmt.Printf("✅ %q contains a non-printable character at %d and is %q\n", a, i, a[i])
	}
	data[20] = '\t'
	nonPrintable(data)
	data[20] = ' '
	nonPrintable(data)
	// Output:
	// ✅ "I caught my squirrel\trustling through my gym bag." contains a non-printable character at 20 and is '\t'
	// ❌ "I caught my squirrel rustling through my gym bag." does NOT contain any non-printable characters
}
```

### `IndexRune`

```go
func ExampleIndexRune() {
	data := []byte("Nobody questions who built the pyramids in Mexico🥑.")
	runed := func(a []byte, r rune) {
		i := bytes.IndexRune(a, r)
		if i == -1 {
			fmt.Printf("❌ %q does NOT contain the rune %q\n", a, r)
			return
		}
		fmt.Printf("✅ %q contains the rune at index: %d\n", a, i)
	}
	runed(data, '🥑')
	runed(data, '🌮')
	// Output:
	// ✅ "Nobody questions who built the pyramids in Mexico🥑." contains the rune at index: 49
	// ❌ "Nobody questions who built the pyramids in Mexico🥑." does NOT contain the rune '🌮'
}
```

### `Join`

```go
func ExampleJoin() {
	data := [][]byte{
		{73},
		{97, 108, 119, 97, 121, 115},
		{100, 114, 101, 97, 109, 101, 100},
		{97, 98, 111, 117, 116},
		{98, 101, 105, 110, 103},
		{115, 116, 114, 97, 110, 100, 101, 100},
		{111, 110},
		{97},
		{100, 101, 115, 101, 114, 116},
		{105, 115, 108, 97, 110, 100},
		{117, 110, 116, 105, 108},
		{105, 116},
		{97, 99, 116, 117, 97, 108, 108, 121},
		{104, 97, 112, 112, 101, 110, 101, 100, 46},
	}
	fmt.Printf("joined bytes: %q\n", bytes.Join(data, []byte(" ")))

	oddWS := []byte("Nobody has\nencountered an explosive\r\ndaisy\t\tand lived to            tell the tale.")
	fmt.Printf("sanitized bytes: %q\n", bytes.Join(bytes.Fields(oddWS), []byte(" ")))

	// Output:
	// joined bytes: "I always dreamed about being stranded on a desert island until it actually happened."
	// sanitized bytes: "Nobody has encountered an explosive daisy and lived to tell the tale."
}
```

### `LastIndex`

```go
func ExampleLastIndex() {
	data := []byte("The fog was so dense even a laser decided it wasn't worth the effort.")
	lastIdx := func(a, b []byte) {
		i := bytes.LastIndex(a, b)
		if i == -1 {
			fmt.Printf("❌ %q could not be found in %q, index is %d\n", b, a, i)
			return
		}
		fmt.Printf("✅ last occurence of %q starts at index: %d of %q\n\tyield: %q\n", b, i, a, a[i:])
	}
	lastIdx(data, []byte("f"))
	lastIdx(data, []byte("XXX"))
	// Output:
	// ✅ last occurence of "f" starts at index: 64 of "The fog was so dense even a laser decided it wasn't worth the effort."
	// 	yield: "fort."
	// ❌ "XXX" could not be found in "The fog was so dense even a laser decided it wasn't worth the effort.", index is -1
}
```

### `LastIndexAny`

```go
func ExampleLastIndexAny() {
	data := []byte("Patricia found the meaning of life in a bowl of Cheerios.")
	findAny := func(a []byte, chars string) {
		i := bytes.LastIndexAny(a, chars)
		if i == -1 {
			fmt.Printf("❌ %q does NOT contain any of these characters %q\n", a, chars)
			return
		}
		fmt.Printf("✅ %q contains one of these characters %q last found at index: %d and is %q\n",
			a, chars, i, a[i])
	}
	findAny(data, "PCheerios")
	findAny(data, "")
	findAny(data, "zxy*/q")
	// Output:
	// ✅ "Patricia found the meaning of life in a bowl of Cheerios." contains one of these characters "PCheerios" last found at index: 55 and is 's'
	// ❌ "Patricia found the meaning of life in a bowl of Cheerios." does NOT contain any of these characters ""
	// ❌ "Patricia found the meaning of life in a bowl of Cheerios." does NOT contain any of these characters "zxy*/q"
}
```

### `LastIndexByte`

```go
func ExampleLastIndexByte() {
	data := []byte("Be careful with that butter knife.")
	byteMeLast := func(a []byte, b byte) {
		i := bytes.LastIndexByte(a, b)
		if i == -1 {
			fmt.Printf("%q does not have byte: %q\n", a, b)
			return
		}
		fmt.Printf("%q is found at last index: %d of %q and yields: %q\n", b, i, a, a[i:])
	}
	byteMeLast(data, 'f')
	byteMeLast(data, 'x')
	// Output:
	// 'f' is found at last index: 31 of "Be careful with that butter knife." and yields: "fe."
	// "Be careful with that butter knife." does not have byte: 'x'
}
```

### `LastIndexFunc`

```go
func ExampleLastIndexFunc() {
	data := []byte("I ate a sock... because people on the Internet told me to.")
	lastPeriod := func(a []byte) {
		i := bytes.LastIndexFunc(a, func(r rune) bool {
			return r == '.'
		})
		if i == -1 {
			fmt.Printf("❌ %q does NOT contain a '.'\n", a)
			return
		}
		fmt.Printf("✅ %q contains a '.' at last index: %d and yields %q\n", a, i, a[i:])
	}
	data[len(data)-1] = ','
	lastPeriod(data)
	data[len(data)-1] = '.'
	lastPeriod(data)
	lastPeriod(bytes.ReplaceAll(data, []byte("."), []byte("")))
	// Output:
	// ✅ "I ate a sock... because people on the Internet told me to," contains a '.' at last index: 14 and yields ". because people on the Internet told me to,"
	// ✅ "I ate a sock... because people on the Internet told me to." contains a '.' at last index: 57 and yields "."
	// ❌ "I ate a sock because people on the Internet told me to" does NOT contain a '.'
}
```

### `Map`

```go
func ExampleMap() {
	data := []byte("Th)e l!yric^s., of the213 so*ng() sound230ed li$$ke fi===n93gerna;ils on a cha_+}\\lkboa<]rd.")
	fmt.Printf("from:\t%q\nto:\t%q",
		data,
		bytes.Map(
			func(r rune) rune {
				switch {
				case !unicode.IsLetter(r) && !unicode.IsSpace(r):
					return -1
				case r > 'm':
					return unicode.ToUpper(r)
				default:
					return r
				}
			}, data),
	)
	// Output:
	// from:	"Th)e l!yric^s., of the213 so*ng() sound230ed li$$ke fi===n93gerna;ils on a cha_+}\\lkboa<]rd."
	// to:	"The lYRicS Of The SONg SOUNded like fiNgeRNailS ON a chalkbOaRd"
}
```

### `Repeat`

```go
func ExampleRepeat() {
	data := []byte("The glacier came alive as the climbers hiked closer.")
	defer func() {
		if r := recover(); r != nil {
			fmt.Printf("panic: %q\n", r)
			fmt.Printf("%q\n", bytes.Repeat(data, 2))
		}
	}()
	fmt.Println(bytes.Repeat(data, 1<<62)) // XXX(jay): This panics
	// Output:
	// panic: "bytes: Repeat count causes overflow"
	// "The glacier came alive as the climbers hiked closer.The glacier came alive as the climbers hiked closer."
}
```

### `Replace`

```go
func ExampleReplace() {
	data := []byte("It's much more difficult to play tennis with a bowling ball than it is to bowl with a tennis ball, but tennis is difficult in general.")
	etp := func(a, old, _new []byte, n int) {
		fmt.Printf("orig:\t%q\nnew:\t%q\n", a, bytes.Replace(a, old, _new, n))
		fmt.Println("-------------", "replaced", n, "-------------")
	}
	etp(data, []byte("difficult"), []byte("easy😎"), 1)
	etp(data, []byte("difficult"), []byte("easy😎"), 2)
	etp(data, []byte("tennis"), []byte("football🏈"), -1) // == [bytes.ReplaceAll]
	// Output:
	// orig:	"It's much more difficult to play tennis with a bowling ball than it is to bowl with a tennis ball, but tennis is difficult in general."
	// new:	"It's much more easy😎 to play tennis with a bowling ball than it is to bowl with a tennis ball, but tennis is difficult in general."
	// ------------- replaced 1 -------------
	// orig:	"It's much more difficult to play tennis with a bowling ball than it is to bowl with a tennis ball, but tennis is difficult in general."
	// new:	"It's much more easy😎 to play tennis with a bowling ball than it is to bowl with a tennis ball, but tennis is easy😎 in general."
	// ------------- replaced 2 -------------
	// orig:	"It's much more difficult to play tennis with a bowling ball than it is to bowl with a tennis ball, but tennis is difficult in general."
	// new:	"It's much more difficult to play football🏈 with a bowling ball than it is to bowl with a football🏈 ball, but football🏈 is difficult in general."
	// ------------- replaced -1 -------------
}
```

### `ReplaceAll`

```go
func ExampleReplaceAll() {
	data := []byte("It's much more difficult to play tennis with a bowling ball than it is to bowl with a tennis ball, but tennis is difficult in general.")
	fmt.Printf("orig:\t%q\nnew:\t%q\n",
		data, bytes.ReplaceAll(data, []byte("tennis"), []byte("football🏈")),
	)
	// Output:
	// orig:	"It's much more difficult to play tennis with a bowling ball than it is to bowl with a tennis ball, but tennis is difficult in general."
	// new:	"It's much more difficult to play football🏈 with a bowling ball than it is to bowl with a football🏈 ball, but football🏈 is difficult in general."
}
```

### `Runes`

```go
func ExampleRunes() {
	data := []byte("😄👋😂💬🎉🤔👀")
	fmt.Printf("useless:\t%v\nmore info:\t%U\n", data, bytes.Runes(data))
	// Output:
	// useless:	[240 159 152 132 240 159 145 139 240 159 152 130 240 159 146 172 240 159 142 137 240 159 164 148 240 159 145 128]
	// more info:	[U+1F604 U+1F44B U+1F602 U+1F4AC U+1F389 U+1F914 U+1F440]
}
```

### `Split`

```go
func ExampleSplit() {
	data := []byte("He🍌loved🍌eating🍌his🍌bananas🍌in🍌hot🍌dog🍌buns.")
	fmt.Printf("%q\n", bytes.Split(data, []byte("🍌")))
	fmt.Printf("%q\n", bytes.Split(data, nil))
	// Output:
	// ["He" "loved" "eating" "his" "bananas" "in" "hot" "dog" "buns."]
	// ["H" "e" "🍌" "l" "o" "v" "e" "d" "🍌" "e" "a" "t" "i" "n" "g" "🍌" "h" "i" "s" "🍌" "b" "a" "n" "a" "n" "a" "s" "🍌" "i" "n" "🍌" "h" "o" "t" "🍌" "d" "o" "g" "🍌" "b" "u" "n" "s" "."]
}
```

### `SplitAfter`

```go
func ExampleSplitAfter() {
	data := []byte("The🦃Guinea🦃fowl🦃flies🦃through🦃the🦃air🦃with🦃all🦃the🦃grace🦃of🦃a🦃turtle.")
	fmt.Printf("%q\n", bytes.SplitAfter(data, []byte("🦃")))
	fmt.Printf("%q\n", bytes.SplitAfter(data, []byte{}))
	// Output:
	// ["The🦃" "Guinea🦃" "fowl🦃" "flies🦃" "through🦃" "the🦃" "air🦃" "with🦃" "all🦃" "the🦃" "grace🦃" "of🦃" "a🦃" "turtle."]
	// ["T" "h" "e" "🦃" "G" "u" "i" "n" "e" "a" "🦃" "f" "o" "w" "l" "🦃" "f" "l" "i" "e" "s" "🦃" "t" "h" "r" "o" "u" "g" "h" "🦃" "t" "h" "e" "🦃" "a" "i" "r" "🦃" "w" "i" "t" "h" "🦃" "a" "l" "l" "🦃" "t" "h" "e" "🦃" "g" "r" "a" "c" "e" "🦃" "o" "f" "🦃" "a" "🦃" "t" "u" "r" "t" "l" "e" "."]
}
```

### `SplitAfterN`

```go
func ExampleSplitAfterN() {
	data := []byte("He🎭had🎭a🎭wall🎭full🎭of🎭masks🎭so🎭she🎭could🎭wear🎭a🎭different🎭face🎭every🎭day.")
	splits := func(a, b []byte, n int) {
		nsplits := bytes.SplitAfterN(a, b, n)
		fmt.Printf("len: %d, values: %q\n", len(nsplits), nsplits)
	}
	splits(data, []byte("🎭"), 0)  // useful for obfuscation?
	splits(data, []byte("🎭"), -1) // == [bytes.SplitAfter]
	splits(data, []byte("🎭"), 4)
	splits(data, []byte("🎭"), 20)
	// Output:
	// len: 0, values: []
	// len: 16, values: ["He🎭" "had🎭" "a🎭" "wall🎭" "full🎭" "of🎭" "masks🎭" "so🎭" "she🎭" "could🎭" "wear🎭" "a🎭" "different🎭" "face🎭" "every🎭" "day."]
	// len: 4, values: ["He🎭" "had🎭" "a🎭" "wall🎭full🎭of🎭masks🎭so🎭she🎭could🎭wear🎭a🎭different🎭face🎭every🎭day."]
	// len: 16, values: ["He🎭" "had🎭" "a🎭" "wall🎭" "full🎭" "of🎭" "masks🎭" "so🎭" "she🎭" "could🎭" "wear🎭" "a🎭" "different🎭" "face🎭" "every🎭" "day."]
}
```

### `SplitN`

```go
func ExampleSplitN() {
	data := []byte("Purple💜is💜the💜best💜city💜in💜the💜forest.")
	splits := func(a, b []byte, n int) {
		nsplits := bytes.SplitN(a, b, n)
		fmt.Printf("len: %d, values: %q\n", len(nsplits), nsplits)
	}
	splits(data, []byte("💜"), 0)  // useful for obfuscation?
	splits(data, []byte("💜"), -1) // == [bytes.SplitAfter]
	splits(data, []byte("💜"), 3)
	splits(data, []byte("💜"), 10)
	// Output:
	// len: 0, values: []
	// len: 8, values: ["Purple" "is" "the" "best" "city" "in" "the" "forest."]
	// len: 3, values: ["Purple" "is" "the💜best💜city💜in💜the💜forest."]
	// len: 8, values: ["Purple" "is" "the" "best" "city" "in" "the" "forest."]
}
```

### `Title`

```go
func ExampleTitle() {
	// XXX(jay): Don't use this! DEPRECATED
	// [bytes.Title]
	src := []byte("The rain pelted the windshield as the darkness engulfed us.")
	dst := make([]byte, len(src))
	// Use this in it's place.
	nDst, nSrc, err := cases.Title(language.English, cases.NoLower).Transform(dst, src, true)
	fmt.Printf("orig:\t%q\nresult:\t%q\nnDst: %d\tnSrc: %d\terr: %v\n",
		src, dst, nDst, nSrc, err)
	// Output:
	// orig:	"The rain pelted the windshield as the darkness engulfed us."
	// result:	"The Rain Pelted The Windshield As The Darkness Engulfed Us."
	// nDst: 59	nSrc: 59	err: <nil>
}
```

### `ToLower`

```go
func ExampleToLower() {
	data := []byte("PEopLE wHo InsIst on pICKInG tHEIr tEEtH wItH tHEIr ELBows ArE so AnnoyInG!")
	fmt.Printf("before:\t%q\nafter:\t%q\n", data, bytes.ToLower(data))
	// Output:
	// before:	"PEopLE wHo InsIst on pICKInG tHEIr tEEtH wItH tHEIr ELBows ArE so AnnoyInG!"
	// after:	"people who insist on picking their teeth with their elbows are so annoying!"
}
```

### `ToLowerSpecial`

```go
func ExampleToLowerSpecial() {
	fmt.Printf("%q\n", bytes.ToLowerSpecial(unicode.TurkishCase, []byte("Genç ÇOCUK, saDEce teSTTen çıKMak IÇin kolunu kırmakla suÇlandı.")))
	// Output:
	// "genç çocuk, sadece testten çıkmak ıçin kolunu kırmakla suçlandı."
}
```

### `ToTitle`

```go
func ExampleToTitle() {
	// NOTE(jay): Compare with [bytes.ToUpper] and [bytes.Title]
	data := []byte("He found a leprechaun in his walnut shell.")
	data2 := []byte("он нашел лепрекона в скорлупе грецкого ореха.")
	comp := func(a []byte) {
		fmt.Printf("before:\t%q\nafter:\t%q\n", a, bytes.ToTitle(a))
	}
	comp(data)
	comp(data2)
	// Output:
	// before:	"He found a leprechaun in his walnut shell."
	// after:	"HE FOUND A LEPRECHAUN IN HIS WALNUT SHELL."
	// before:	"он нашел лепрекона в скорлупе грецкого ореха."
	// after:	"ОН НАШЕЛ ЛЕПРЕКОНА В СКОРЛУПЕ ГРЕЦКОГО ОРЕХА."
}
```

### `ToTitleSpecial`

```go
func ExampleToTitleSpecial() {
	fmt.Printf("%q\n", bytes.ToTitleSpecial(unicode.TurkishCase, []byte("Genç ÇOCUK, saDEce teSTTen çıKMak IÇin kolunu kırmakla suÇlandı.")))
	// Output:
	// "GENÇ ÇOCUK, SADECE TESTTEN ÇIKMAK IÇİN KOLUNU KIRMAKLA SUÇLANDI."
}
```

### `ToUpper`

```go
func ExampleToUpper() {
	// NOTE(jay): Compare with [bytes.ToTitle] and [bytes.Title]
	data := []byte("He found a leprechaun in his walnut shell.")
	data2 := []byte("он нашел лепрекона в скорлупе грецкого ореха.")
	comp := func(a []byte) {
		fmt.Printf("before:\t%q\nafter:\t%q\n", a, bytes.ToUpper(a))
	}
	comp(data)
	comp(data2)
	// Output:
	// before:	"He found a leprechaun in his walnut shell."
	// after:	"HE FOUND A LEPRECHAUN IN HIS WALNUT SHELL."
	// before:	"он нашел лепрекона в скорлупе грецкого ореха."
	// after:	"ОН НАШЕЛ ЛЕПРЕКОНА В СКОРЛУПЕ ГРЕЦКОГО ОРЕХА."
}
```

### `ToUpperSpecial`

```go
func ExampleToUpperSpecial() {
	fmt.Printf("%q\n", bytes.ToUpperSpecial(unicode.TurkishCase, []byte("Genç ÇOCUK, saDEce teSTTen çıKMak IÇin kolunu kırmakla suÇlandı.")))
	// Output:
	// "GENÇ ÇOCUK, SADECE TESTTEN ÇIKMAK IÇİN KOLUNU KIRMAKLA SUÇLANDI."
}
```

### `ToValidUTF8`

```go
func ExampleToValidUTF8() {
	// NOTE(jay): Bad bytes taken from
	// 	https://www.cl.cam.ac.uk/~mgk25/ucs/examples/UTF-8-test.txt
	data := []byte{
		0x54,
		0xc0, 0xaf, // Bad bytes
		0x6f, 0x64, 0x61,
		0xff, // Bad byte
		0x79, 0x20, 0x69,
		0xfe, // Bad byte
		0x73, 0x20,
		0xc1, 0xbf, // Bad bytes
		0x74, 0x68, 0x65, 0x20,
		0xf8, 0x87, 0xbf, 0xbf, // Bad bytes
		0x64, 0x61, 0x79, 0x20, 0x49, 0x27, 0x6c, 0x6c, 0x20, 0x66,
		0xc0, 0x80, 0xfc, 0x80, 0x80, 0x80, 0x80, // Bad bytes
		0x69, 0x6e, 0x61, 0x6c, 0x6c, 0x79, 0x20, 0x6b, 0x6e, 0x6f, 0x77, 0x20,
		0xed, 0xa0, 0x80, // Bad bytes
		0x77, 0x68, 0x61, 0x74, 0x20, 0x62, 0x72, 0x69, 0x63, 0x6b, 0x20, 0x74,
		0xed, 0xa0, 0x80, 0xed, 0xb0, 0x80, // Bad bytes
		0x61, 0x73, 0x74, 0x65, 0x73, 0x20, 0x6c, 0x69, 0x6b, 0x65, 0x2e,
	}
	fmt.Printf("%q\n", bytes.ToValidUTF8(data, []byte("")))
	fmt.Printf("%q\n", bytes.ToValidUTF8(data, []byte("🥴")))
	// Output:
	// "Today is the day I'll finally know what brick tastes like."
	// "T🥴oda🥴y i🥴s 🥴the 🥴day I'll f🥴inally know 🥴what brick t🥴astes like."
}
```

### `Trim`

```go
func ExampleTrim() {
	data := []byte("🍞🥜🥜🍇🍞👵Peanut butter and jelly caused the elderly lady to think about her past.🍞🥜🥜🍓🍓🍞👵")
	fmt.Printf("trimmed: %q", bytes.Trim(data, "👵🍇🍞🥜🍓"))
	// Output:
	// trimmed: "Peanut butter and jelly caused the elderly lady to think about her past."
}
```

### `TrimFunc`

```go
func ExampleTrimFunc() {
	data := []byte(`
/####%%%#######%%%#######'#######'#######'#######'####'#######'#######'#######%%%#######%%%######\
*####%%%#######%%%###################################################################%%%#######%%%######*
*####%%%#### `The secret ingredient to his wonderful life was crime. ###%%%######*`
*######%##########%######################################################################%##########%#######*
\####'#'#######'#'###################################################################'#'#######'#'######/
`[1:])
	fmt.Printf("cleaned: %q\n", bytes.TrimFunc(data, func(r rune) bool {
		return r == '\n' || r == ' ' || r == '#' || r == '\\' ||
			r == '*' || r == '\'' || r == '/' || r == '%'
	}))
	// Output:
	// cleaned: "The secret ingredient to his wonderful life was crime."
}
```

### `TrimLeft`

```go
func ExampleTrimLeft() {
	data := []byte("🕵️‍♂️📔🎤🗣The mysterious diary records the voice.")
	fmt.Printf("trimmed: %q\n", bytes.TrimLeft(data, "📔🕵️‍🗣♂️🎤"))
	// Output:
	// trimmed: "The mysterious diary records the voice."
}
```

### `TrimLeftFunc`

```go
func ExampleTrimLeftFunc() {
	data := []byte(`
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%             _       _                  %
%  _ __   ___| |_ ___| |__   ___  _ __   %
% | '_ \ / _ \ __/ __| '_ \ / _ \| '_ \  %
% | |_) |  __/ |_\__ \ | | | (_) | |_) | %
% | .__/ \___|\__|___/_| |_|\___/| .__/  %
% |_|                            |_|     %
%                                        %
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
The pet shop stocks everything you need to keep your anaconda happy.`)
	fmt.Printf("trimmed: %q\n", bytes.TrimLeftFunc(data, func(r rune) bool {
		return unicode.In(r, unicode.Punct, unicode.White_Space) || r == '|'
	}))
	// Output:
	// trimmed: "The pet shop stocks everything you need to keep your anaconda happy."
}
```

### `TrimPrefix`

```go
func ExampleTrimPrefix() {
	data := []byte("He had reached the point where he was paranoid about being paranoid.")
	fmt.Printf("trimmed: %q\n", bytes.TrimPrefix(data, []byte("He had reached the point where")))
	fmt.Printf("trimmed: %q\n", bytes.TrimPrefix(data, []byte("He had reached")))
	fmt.Printf("trimmed: %q\n", bytes.TrimPrefix(data, []byte("XXX")))
	// Output:
	// trimmed: " he was paranoid about being paranoid."
	// trimmed: " the point where he was paranoid about being paranoid."
	// trimmed: "He had reached the point where he was paranoid about being paranoid."
}
```

### `TrimRight`

```go
func ExampleTrimRight() {
	fmt.Printf("trimmed: %q", bytes.TrimRight(
		[]byte("Imagine his surprise when he discovered that the safe was full of pudding.🫃🍮🍮🍮🍮🍮🍮🍮🍮🍮"),
		"🍮🫃🧷"))
	// Output:
	// trimmed: "Imagine his surprise when he discovered that the safe was full of pudding."
}
```

### `TrimRightFunc`

```go
func ExampleTrimRightFunc() {
	fmt.Printf("trimmed: %q\n", bytes.TrimRightFunc(
		[]byte(`Harrold felt confident that nobody would ever suspect his spy pigeon.
			-- Narrator 04/20/69`),
		func(r rune) bool {
			return unicode.In(r, unicode.Letter, unicode.Digit, unicode.White_Space) ||
				r == '-' || r == '/'
		}))
	// Output:
	// trimmed: "Harrold felt confident that nobody would ever suspect his spy pigeon."
}
```

### `TrimSpace`

```go
func ExampleTrimSpace() {
	data := []byte("\t \n \v \f \r \u0085 \u00a0   You bite up because of your lower jaw.😬   \u00a0 \u0085 \r \f \v \n \t")
	fmt.Printf("with space:\t%q\nwithout:\t%q\n", data, bytes.TrimSpace(data))
	// Output:
	// with space:	"\t \n \v \f \r \u0085 \u00a0   You bite up because of your lower jaw.😬   \u00a0 \u0085 \r \f \v \n \t"
	// without:	"You bite up because of your lower jaw.😬"
}
```

### `TrimSuffix`

```go
func ExampleTrimSuffix() {
	data := []byte("He put heat on the wound to see what would grow.")
	fmt.Printf("trimmed: %q\n", bytes.TrimSuffix(data, []byte("what would grow.")))
	fmt.Printf("trimmed: %q\n", bytes.TrimSuffix(data, []byte("grow.")))
	fmt.Printf("trimmed: %q\n", bytes.TrimSuffix(data, []byte("He put")))
	// Output:
	// trimmed: "He put heat on the wound to see "
	// trimmed: "He put heat on the wound to see what would "
	// trimmed: "He put heat on the wound to see what would grow."
}
```

### `NewBuffer`

```go
func ExampleNewBuffer() {
	fmt.Printf("%#v", bytes.NewBuffer([]byte("Create a new Buffer with a pre-allocated byte array.")))
	// Output:
	// &bytes.Buffer{buf:[]uint8{0x43, 0x72, 0x65, 0x61, 0x74, 0x65, 0x20, 0x61, 0x20, 0x6e, 0x65, 0x77, 0x20, 0x42, 0x75, 0x66, 0x66, 0x65, 0x72, 0x20, 0x77, 0x69, 0x74, 0x68, 0x20, 0x61, 0x20, 0x70, 0x72, 0x65, 0x2d, 0x61, 0x6c, 0x6c, 0x6f, 0x63, 0x61, 0x74, 0x65, 0x64, 0x20, 0x62, 0x79, 0x74, 0x65, 0x20, 0x61, 0x72, 0x72, 0x61, 0x79, 0x2e}, off:0, lastRead:0}
}
```

#### `stubBuffer`

```go
func stubBuffer() *bytes.Buffer {
	return bytes.NewBuffer([]byte("The hand sanitizer was actually clear glue."))
}
```

### `NewBufferString`

```go
func ExampleNewBufferString() {
	fmt.Printf("%#v", bytes.NewBufferString("This can also be done with a string."))
	// Output:
	// &bytes.Buffer{buf:[]uint8{0x54, 0x68, 0x69, 0x73, 0x20, 0x63, 0x61, 0x6e, 0x20, 0x61, 0x6c, 0x73, 0x6f, 0x20, 0x62, 0x65, 0x20, 0x64, 0x6f, 0x6e, 0x65, 0x20, 0x77, 0x69, 0x74, 0x68, 0x20, 0x61, 0x20, 0x73, 0x74, 0x72, 0x69, 0x6e, 0x67, 0x2e}, off:0, lastRead:0}
}
```

### `Buffer.Bytes`

```go
func ExampleBuffer_Bytes() {
	buf := stubBuffer()
	for _, b := range buf.Bytes() {
		// Do something with the bytes.
		fmt.Print(b)
	}
	cp := make([]byte, buf.Len())
	copy(cp, buf.Bytes())
	fmt.Println("\n", cp)
	// Output:
	// 84104101321049711010032115971101051161051221011143211997115329799116117971081081213299108101971143210310811710146
	//  [84 104 101 32 104 97 110 100 32 115 97 110 105 116 105 122 101 114 32 119 97 115 32 97 99 116 117 97 108 108 121 32 99 108 101 97 114 32 103 108 117 101 46]
}
```

### `Buffer.Cap`

```go
func ExampleBuffer_Cap() {
	// NOTE(jay): compare with Reader.Size()
	fmt.Println("the total space allocated for the buffer's data is:", stubBuffer().Cap())
	// Output:
	// the total space allocated for the buffer's data is: 43
}
```

### `Buffer.Grow`

```go
func ExampleBuffer_Grow() {
	buf := stubBuffer()
	fmt.Println("the total space allocated for the buffer's data is:", buf.Cap())
	buf.Grow(1 << 9)
	fmt.Println("the total space allocated for the buffer's data is:", buf.Cap())
	defer func() {
		if r := recover(); r != nil {
			fmt.Println(r) // bytes.Buffer: too large
		}
	}()
	defer func() {
		if r := recover(); r != nil {
			fmt.Println(r) // bytes.Buffer.Grow: negative count
			buf.Grow(1 << 62)
		}
	}()
	buf.Grow(-1)
	// Output:
	// the total space allocated for the buffer's data is: 43
	// the total space allocated for the buffer's data is: 576
	// bytes.Buffer.Grow: negative count
	// bytes.Buffer: too large
}
```

### `Buffer.Len`

```go
func ExampleBuffer_Len() {
	buf := stubBuffer()
	fmt.Println("the number of bytes in the UNREAD portion:", buf.Len())
	fmt.Println("the total space allocated for the buffer's data is:", buf.Cap())
	p := make([]byte, 1<<4)
	buf.Read(p)
	fmt.Println("the number of bytes in the UNREAD portion:", buf.Len())
	fmt.Println("the total space allocated for the buffer's data is:", buf.Cap())

	fmt.Println("\ndon't forget Len updates dynamically based on UNREAD bytes")
	for i := 0; i < buf.Len(); i++ {
		b, _ := buf.ReadByte()
		fmt.Printf(" %q", b)
	}
	b, err := buf.ReadByte() // err should be [io.EOF]
	fmt.Printf("\ndidn't make it to the end: byte: %q err: %v", b, err)
\(\/\/.*\)\(io\.\w\+\)\(.*\)
	// Output:
	// the number of bytes in the UNREAD portion: 43
	// the total space allocated for the buffer's data is: 43
	// the number of bytes in the UNREAD portion: 27
	// the total space allocated for the buffer's data is: 43
	//
	// don't forget Len updates dynamically based on UNREAD bytes
	//  'e' 'r' ' ' 'w' 'a' 's' ' ' 'a' 'c' 't' 'u' 'a' 'l' 'l'
	// didn't make it to the end: byte: 'y' err: <nil>
}
```

### `Buffer.Next`

```go
func ExampleBuffer_Next() {
	buf := stubBuffer()
	// NOTE(jay): Memory allocated for returned byte slice. Not as efficient as Buffer.Read
	data := buf.Next(1 << 5)
	fmt.Printf("data Len: %d, Cap: %d, result: %q\n", len(data), cap(data), data)
	data = buf.Next(1 << 62) // Fewer than 1 << 62 returns entire buffer.
	fmt.Printf("data Len: %d, Cap: %d, result: %q\n", len(data), cap(data), data)
	buf.Write([]byte("This is not reflected in data and invalidates data as up to date."))
	fmt.Printf("same Len: %d, Cap: %d, result: %q\n", len(data), cap(data), data)
	// Output:
	// data Len: 32, Cap: 43, result: "The hand sanitizer was actually "
	// data Len: 11, Cap: 11, result: "clear glue."
	// same Len: 11, Cap: 11, result: "clear glue."
}
```

### `Buffer.Read`

```go
func ExampleBuffer_Read() {
	buf := stubBuffer()
	data := make([]byte, 1<<4)
	n, err := buf.Read(data)
	fmt.Printf("number bytes read into data: %d, any error: %v, result: %q\n", n, err, data[:n])
	n, err = buf.Read(data)
	fmt.Printf("number bytes read into data: %d, any error: %v, result: %q\n", n, err, data[:n])
	n, err = buf.Read(data)
	fmt.Printf("number bytes read into data: %d, any error: %v, result: %q\n", n, err, data[:n])
	n, err = buf.Read(data)
	fmt.Printf("number bytes read into data: %d, any error: %v, result: %q\n", n, err, data)
	// Output:
	// number bytes read into data: 16, any error: <nil>, result: "The hand sanitiz"
	// number bytes read into data: 16, any error: <nil>, result: "er was actually "
	// number bytes read into data: 11, any error: <nil>, result: "clear glue."
	// number bytes read into data: 0, any error: EOF, result: "clear glue.ally "
}
```

### `Buffer.ReadByte`

```go
func ExampleBuffer_ReadByte() {
	buf := stubBuffer()
	b, err := buf.ReadByte()
	fmt.Printf("byte: %q, err: %v\n", b, err)
	n := buf.Len()
	for i := 0; i < n; i++ {
		buf.ReadByte()
	}
	b, err = buf.ReadByte()
	fmt.Printf("byte: %q, err: %v\n", b, err)
	// Output:
	// byte: 'T', err: <nil>
	// byte: '\x00', err: EOF
}
```

### `Buffer.ReadBytes`

```go
func ExampleBuffer_ReadBytes() {
	// NOTE(jay): Compare to Buffer.ReadString
	buf := stubBuffer()
	line, err := buf.ReadBytes('r')
	fmt.Printf("err: %v\tline: %q\n", err, line)
	line, err = buf.ReadBytes('.')
	fmt.Printf("err: %v\tline: %q\n\n", err, line)

	buf = stubBuffer()
	line, err = buf.ReadBytes('x')
	fmt.Printf("err: %v\tline: %q\n", err, line)
	// Output:
	// err: <nil>	line: "The hand sanitizer"
	// err: <nil>	line: " was actually clear glue."
	//
	// err: EOF	line: "The hand sanitizer was actually clear glue."
}
```

### `Buffer.ReadFrom`

```go
func ExampleBuffer_ReadFrom() {
	buf := stubBuffer()
	buf.ReadFrom(bytes.NewReader([]byte(" There's a message for you if you look to the left.")))
	fmt.Printf("%q\n", buf.String())
	// Output:
	// "The hand sanitizer was actually clear glue. There's a message for you if you look to the left."
}
```

### `Buffer.ReadRune`

```go
func ExampleBuffer_ReadRune() {
	buf := bytes.NewBuffer([]byte("❌😕"))
	for b, err := buf.ReadByte(); err == nil; b, err = buf.ReadByte() {
		fmt.Printf(" %q", b)
	}
	fmt.Printf("\n\n")

	buf = bytes.NewBuffer([]byte("😁👍"))
	for r, n, err := buf.ReadRune(); err == nil; r, n, err = buf.ReadRune() {
		fmt.Printf("%q size: %d\n", r, n)
	}
	// Output:
	// 'â' '\u009d' '\u008c' 'ð' '\u009f' '\u0098' '\u0095'
	//
	// '😁' size: 4
	// '👍' size: 4
}
```

### `Buffer.ReadString`

```go
func ExampleBuffer_ReadString() {
	// NOTE(jay): Compare to Buffer.ReadBytes
	buf := stubBuffer()
	line, err := buf.ReadString('r')
	fmt.Printf("err: %v\tline: %q\n", err, line)
	line, err = buf.ReadString('.')
	fmt.Printf("err: %v\tline: %q\n\n", err, line)

	buf = stubBuffer()
	line, err = buf.ReadString('x')
	fmt.Printf("err: %v\tline: %q\n", err, line)
	// Output:
	// err: <nil>	line: "The hand sanitizer"
	// err: <nil>	line: " was actually clear glue."
	//
	// err: EOF	line: "The hand sanitizer was actually clear glue."
}
```

### `Buffer.Reset`

```go
func ExampleBuffer_Reset() {
	buf := stubBuffer()
	buf.Reset() // buf.Trancate(0)
	fmt.Printf("reset:\tlen: %d and cap: %d\n", buf.Len(), buf.Cap())
	var _new bytes.Buffer
	fmt.Printf("new:\tlen: %d and cap: %d\n\n", _new.Len(), _new.Cap())

	line, err := buf.ReadBytes('.')
	fmt.Printf("line: %q, error: %v\n\n", line, err)

	buf.WriteString("The glacier came alive as the climbers hiked closer.")
	line, err = buf.ReadBytes('.')
	fmt.Printf("line: %q, error: %v\n", line, err)
	// Output:
	// reset:	len: 0 and cap: 43
	// new:	len: 0 and cap: 0
	//
	// line: "", error: EOF
	//
	// line: "The glacier came alive as the climbers hiked closer.", error: <nil>
}
```

### `Buffer.String`

```go
func ExampleBuffer_String() {
	fmt.Printf("%v\n", stubBuffer().String())
	fmt.Printf("%v\n\n", stubBuffer().Bytes())

	var buf *bytes.Buffer
	buf = nil
	fmt.Printf("%v\n", buf.String())
	// Output:
	// The hand sanitizer was actually clear glue.
	// [84 104 101 32 104 97 110 100 32 115 97 110 105 116 105 122 101 114 32 119 97 115 32 97 99 116 117 97 108 108 121 32 99 108 101 97 114 32 103 108 117 101 46]
	//
	// <nil>
}
```

### `Buffer.Truncate`

```go
func ExampleBuffer_Truncate() {
	buf := stubBuffer()
	buf.Truncate(0) // buf.Reset()
	fmt.Printf("reset:\tlen: %d and cap: %d\n\n", buf.Len(), buf.Cap())

	buf = stubBuffer()
	buf.Truncate(16)
	fmt.Printf("trunc:\tlen: %d and cap: %d\n", buf.Len(), buf.Cap())
	fmt.Printf("data after truncation: %q\n", buf.String())
	// Output:
	// reset:	len: 0 and cap: 43
	//
	// trunc:	len: 16 and cap: 43
	// data after truncation: "The hand sanitiz"
}
```

### `Buffer.UnreadByte`

```go
func ExampleBuffer_UnreadByte() {
	buf := bytes.NewBuffer([]byte("se al stuf"))
	var out bytes.Buffer
	repeat := false
	for b, err := buf.ReadByte(); err == nil; b, err = buf.ReadByte() {
		out.WriteByte(b)
		switch b {
		case 'e', 'l', 'f':
			repeat = !repeat
			if repeat {
				buf.UnreadByte()
			}
		}
	}
	fmt.Printf("%q\n\n", out.String())

	buf.ReadBytes('|') // Drain buf
	buf.ReadByte()     // err == [io.EOF]
	err := buf.UnreadByte()
	fmt.Printf("%v\n\n", err)

	buf.Write([]byte("fill"))
	buf.ReadByte()
	buf.Write([]byte("cause error"))
	err = buf.UnreadByte()
	fmt.Printf("%v\n", err)
	// Output:
	// "see all stuff"
	//
	// bytes.Buffer: UnreadByte: previous operation was not a successful read
	//
	// bytes.Buffer: UnreadByte: previous operation was not a successful read
}
```

### `Buffer.UnreadRune`

```go
func ExampleBuffer_UnreadRune() {
	seeingDbl := bytes.NewBuffer([]byte("👀"))
	r1, n, err := seeingDbl.ReadRune()
	seeingDbl.UnreadRune()
	r2, _, _ := seeingDbl.ReadRune()
	fmt.Printf("size: %d, result: %s %s\n\n", n, string(r1), string(r2))

	buf := stubBuffer()
	buf.ReadBytes('|') // Drain buf
	buf.ReadRune()     // err == [io.EOF]
	err = buf.UnreadRune()
	fmt.Printf("%v\n\n", err)

	buf.Write([]byte("fill"))
	buf.ReadRune()
	buf.Write([]byte("cause error"))
	err = buf.UnreadRune()
	fmt.Printf("%v\n", err)
	// Output:
	// size: 4, result: 👀 👀
	//
	// bytes.Buffer: UnreadRune: previous operation was not a successful ReadRune
	//
	// bytes.Buffer: UnreadRune: previous operation was not a successful ReadRune
}
```

### `Buffer.Write`

```go
func ExampleBuffer_Write() {
	buf := stubBuffer()
	n, err := buf.Write([]byte(" I may struggle with geography, but I'm sure I'm somewhere around here."))
	fmt.Printf("number of bytes written: %d, any error: %v, result: %q\n", n, err, buf.String())
	// XXX(jay): Panics with [bytes.ErrorTooLarge] if string too large to grow buf, see
	// [Buffer.Grow] for example of [bytes.ErrorTooLarge].

	// Output:
	// number of bytes written: 71, any error: <nil>, result: "The hand sanitizer was actually clear glue. I may struggle with geography, but I'm sure I'm somewhere around here."
}
```

### `Buffer.WriteByte`

```go
func ExampleBuffer_WriteByte() {
	buf := stubBuffer()
	fmt.Printf("any error: %v, result: %q\n", buf.WriteByte('}'), buf.String())
	// XXX(jay): Panics with [bytes.ErrorTooLarge] if string too large to grow buf, see
	// [Buffer.Grow] for example of [bytes.ErrorTooLarge].

	// Output:
	// any error: <nil>, result: "The hand sanitizer was actually clear glue.}"
}
```

### `Buffer.WriteRune`

```go
func ExampleBuffer_WriteRune() {
	buf := stubBuffer()
	n, err := buf.WriteRune('🧴')
	fmt.Printf("number of bytes written: %d, any error: %v, result: %q\n", n, err, buf.String())

	// XXX(jay): Panics with [bytes.ErrorTooLarge] if string too large to grow buf, see
	// [Buffer.Grow] for example of [bytes.ErrorTooLarge].

	// Output:
	// number of bytes written: 4, any error: <nil>, result: "The hand sanitizer was actually clear glue.🧴"
}
```

### `Buffer.WriteString`

```go
func ExampleBuffer_WriteString() {
	buf := stubBuffer()
	buf.WriteString(" There was no telling what thoughts would come from the machine.")
	fmt.Printf("%q\n\n", buf.String())

	// XXX(jay): Panics with [bytes.ErrorTooLarge] if string too large to grow buf, see
	// [Buffer.Grow] for example of [bytes.ErrorTooLarge].

	// Output:
	// "The hand sanitizer was actually clear glue. There was no telling what thoughts would come from the machine."
}
```

### `Buffer.WriteTo`

```go
func ExampleBuffer_WriteTo() {
	buf := stubBuffer()
	dblbuf := stubBuffer()
	n, err := buf.WriteTo(dblbuf)
	fmt.Printf("number of bytes written: %d, error: %v\n", n, err)
	fmt.Printf("all the written data plus contents of other writer:\n\t%q\n", dblbuf.String())
	// Output:
	// number of bytes written: 43, error: <nil>
	// all the written data plus contents of other writer:
	// 	"The hand sanitizer was actually clear glue.The hand sanitizer was actually clear glue."
}
```

### `NewReader`

```go
func ExampleNewReader() {
	fmt.Printf("%#v", bytes.NewReader([]byte("All the bytes to read.")))
	// Output:
	// &bytes.Reader{s:[]uint8{0x41, 0x6c, 0x6c, 0x20, 0x74, 0x68, 0x65, 0x20, 0x62, 0x79, 0x74, 0x65, 0x73, 0x20, 0x74, 0x6f, 0x20, 0x72, 0x65, 0x61, 0x64, 0x2e}, i:0, prevRune:-1}
}
```

#### `stubReader`

```go
func stubReader() *bytes.Reader {
	return bytes.NewReader([]byte("The changing of down comforters to cotton bedspreads always meant the gophers had returned."))
}
```

### `Reader.Len`

```go
func ExampleReader_Len() {
	fmt.Println("the number of bytes in the UNREAD portion:", stubReader().Len())
	// Output:
	// the number of bytes in the UNREAD portion: 91
}
```

### `Reader.Read`

```go
func ExampleReader_Read() {
	r := stubReader()
	data := make([]byte, 1<<5)
	n, err := r.Read(data)
	fmt.Printf("bytes read: %d, any error: %v, result: %q\n", n, err, data[:n])
	n, err = r.Read(data)
	fmt.Printf("bytes read: %d, any error: %v, result: %q\n", n, err, data[:n])
	n, err = r.Read(data)
	fmt.Printf("bytes read: %d, any error: %v, result: %q\n", n, err, data[:n])
	n, err = r.Read(data)
	fmt.Printf("bytes read: %d, any error: %v, result: %q\n", n, err, data)
	// Output:
	// bytes read: 32, any error: <nil>, result: "The changing of down comforters "
	// bytes read: 32, any error: <nil>, result: "to cotton bedspreads always mean"
	// bytes read: 27, any error: <nil>, result: "t the gophers had returned."
	// bytes read: 0, any error: EOF, result: "t the gophers had returned. mean"
}
```

### `Reader.ReadAt`

```go
func ExampleReader_ReadAt() {
	// NOTE(jay): For more information read [io.SeekerAt] interface
	r := stubReader()
	data := make([]byte, 1<<5)
	n, err := r.ReadAt(data, 66)
	fmt.Printf("bytes read: %d, any error: %v, result: %q\n", n, err, data[:n])
	n, err = r.ReadAt(data, 0)
	fmt.Printf("bytes read: %d, any error: %v, result: %q\n", n, err, data[:n])
	n, err = r.ReadAt(data, 1<<5)
	fmt.Printf("bytes read: %d, any error: %v, result: %q\n", n, err, data[:n])
	// Output:
	// bytes read: 25, any error: EOF, result: "the gophers had returned."
	// bytes read: 32, any error: <nil>, result: "The changing of down comforters "
	// bytes read: 32, any error: <nil>, result: "to cotton bedspreads always mean"
}
```

### `Reader.ReadByte`

```go
func ExampleReader_ReadByte() {
	r := stubReader()
	b, err := r.ReadByte()
	fmt.Printf("byte: %q, err: %v\n", b, err)
	n := r.Len()
	for i := 0; i < n; i++ {
		r.ReadByte()
	}
	b, err = r.ReadByte()
	fmt.Printf("byte: %q, err: %v\n", b, err)
	// Output:
	// byte: 'T', err: <nil>
	// byte: '\x00', err: EOF
}
```

### `Reader.ReadRune`

```go
func ExampleReader_ReadRune() {
	rdr := bytes.NewReader([]byte("❌😕"))
	for b, err := rdr.ReadByte(); err == nil; b, err = rdr.ReadByte() {
		fmt.Printf(" %q", b)
	}
	fmt.Printf("\n\n")

	rdr = bytes.NewReader([]byte("😁👍"))
	for r, n, err := rdr.ReadRune(); err == nil; r, n, err = rdr.ReadRune() {
		fmt.Printf("%q size: %d\n", r, n)
	}
	// Output:
	// 'â' '\u009d' '\u008c' 'ð' '\u009f' '\u0098' '\u0095'
	//
	// '😁' size: 4
	// '👍' size: 4
}
```

### `Reader.Reset`

```go
func ExampleReader_Reset() {
	r := stubReader()
	fmt.Printf("reset:\tlen: %d and cap: %d\n", r.Len(), r.Size())
	// resets or switches to new Reader
	r.Reset([]byte("All you need to do is pick up the pen and begin."))
	fmt.Printf("reset:\tlen: %d and cap: %d\n", r.Len(), r.Size())

	data := make([]byte, bytes.MinRead)
	n, err := r.Read(data)
	fmt.Printf("number bytes read into data: %d, any error: %v\nresult: %q\n", n, err, data[:n])
	// Output:
	// reset:	len: 91 and cap: 91
	// reset:	len: 48 and cap: 48
	// number bytes read into data: 48, any error: <nil>
	// result: "All you need to do is pick up the pen and begin."
}
```

### `Reader.Seek`

```go
func ExampleReader_Seek() {
	r := stubReader()
	data := make([]byte, bytes.MinRead)
	newOffset, err := r.Seek(66, io.SeekStart)
	fmt.Printf("offset in reader: %d, any error: %v\n", newOffset, err)
	n, _ := r.Read(data)
	fmt.Printf("data read in: %q\n\n", data[:n])

	newData := make([]byte, bytes.MinRead)
	newOffset, err = r.Seek(0, io.SeekStart)
	fmt.Printf("offset in reader: %d, any error: %v\n", newOffset, err)
	n, _ = r.Read(newData)
	fmt.Printf("newData read in: %q\n\n", newData[:n])

	data = make([]byte, bytes.MinRead)
	newOffset, err = r.Seek(35, io.SeekStart)
	fmt.Printf("offset in reader: %d, any error: %v\n", newOffset, err)
	n, _ = r.Read(data)
	fmt.Printf("repurpose data slice: %q\n", data[:n])
	// Output:
	// offset in reader: 66, any error: <nil>
	// data read in: "the gophers had returned."
	//
	// offset in reader: 0, any error: <nil>
	// newData read in: "The changing of down comforters to cotton bedspreads always meant the gophers had returned."
	//
	// offset in reader: 35, any error: <nil>
	// repurpose data slice: "cotton bedspreads always meant the gophers had returned."
}
```

### `Reader.Seek_whence`

```go
func ExampleReader_Seek_whence() {
	// NOTE(jay): For more information check the [io.Seeker] interface
	r := stubReader()
	readWord := func(r *bytes.Reader, wordSize int) []byte {
		word := make([]byte, wordSize)
		for i := 0; i < wordSize; i++ {
			b, err := r.ReadByte()
			if err != nil {
				panic(err)
			}
			word[i] = b
		}
		return word
	}

	// The changing of down comforters to cotton bedspreads always meant the gophers had returned.
	the := readWord(r, 3)
	r.Seek(-21, io.SeekEnd)    // end: "... gophers had returned."
	gophers := readWord(r, 7)  // pos: " had returned."
	r.Seek(-7, io.SeekCurrent) // move: "gophers had returned."
	_go := readWord(r, 2)      // pos: "phers had returned."
	r.Seek(16, io.SeekStart)   // start: "The changing of down..."
	down := readWord(r, 4)     // pos: " comforters to cotton..."
	r.Seek(12, io.SeekCurrent) // move: "to cotton bedspreads always..."
	to := readWord(r, 2)       // pos: " cotton bedspreads always..."
	r.Seek(1, io.SeekCurrent)  // move: "cotton bedspreads always..."
	cotton := readWord(r, 6)   // pos: "bedspreads always meant..."
	r.Seek(4, io.SeekCurrent)  // move: "spreads always meant..."
	spreads := readWord(r, 7)  // pos: " always meant the..."
	r.Seek(-1, io.SeekEnd)     // end: "."
	dot := readWord(r, 1)      // pos: EOF
	fmt.Printf("%s %s %s %s %s %s-%s%s", the, gophers, _go, down, to, cotton, spreads, dot)
	// Output:
	// The gophers go down to cotton-spreads.
}
```

### `Reader.Size`

```go
func ExampleReader_Size() {
	// NOTE(jay): compare with Buffer.Cap()
	r := stubReader()
	data := make([]byte, 1<<4)
	r.Read(data)
	fmt.Printf("the total space allocated for the reader: %d and length of unread data: %d\n",
		r.Size(), r.Len())
	// Output:
	// the total space allocated for the reader: 91 and length of unread data: 75
}
```

### `Reader.UnreadByte`

```go
func ExampleReader_UnreadByte() {
	// NOTE(jay): For more info checkout [io.ByteReader]
	r := bytes.NewReader([]byte("se al stuf"))
	var out bytes.Buffer
	repeat := false
	for b, err := r.ReadByte(); err == nil; b, err = r.ReadByte() {
		out.WriteByte(b)
		switch b {
		case 'e', 'l', 'f':
			repeat = !repeat
			if repeat {
				r.UnreadByte()
			}
		}
	}
	fmt.Printf("%q\n\n", out.String())

	drain := make([]byte, 1<<8)
	r.Read(drain) // Drain r
	r.ReadByte()  // err == [io.EOF]
	err := r.UnreadByte()
	fmt.Printf("does not return error at EOF: %v\n\n", err)

	fmt.Printf("error: %v\n", stubReader().UnreadByte())
	// Output:
	// "see all stuff"
	//
	// does not return error at EOF: <nil>
	//
	// error: bytes.Reader.UnreadByte: at beginning of slice
}
```

### `Reader.UnreadRune`

```go
func ExampleReader_UnreadRune() {
	// NOTE(jay): For more info checkout [io.RuneReader]
	seeingDbl := bytes.NewReader([]byte("👀"))
	r1, n, err := seeingDbl.ReadRune()
	seeingDbl.UnreadRune()
	r2, _, _ := seeingDbl.ReadRune()
	fmt.Printf("size: %d, result: %s %s\n\n", n, string(r1), string(r2))

	r := stubReader()
	drain := make([]byte, 1<<8)
	r.Read(drain) // Drain r
	r.ReadRune()  // err == [io.EOF]
	err = r.UnreadRune()
	fmt.Printf("error: %v\n\n", err)

	// Output:
	// size: 4, result: 👀 👀
	//
	// error: bytes.Reader.UnreadRune: previous operation was not ReadRune
}
```

### `Reader.WriteTo`

```go
func ExampleReader_WriteTo() {
	r := stubReader()
	var buf bytes.Buffer
	// NOTE(jay): To see what other Writers exist in the std lib use `guru`.
	// 	https://docs.google.com/document/d/1_Y9xCEMj5S-7rv2ooHpZNH15JgRT5iM742gJkw5LtmQ/edit
	n, err := r.WriteTo(&buf) // Can panic or give error in **_very_** bad circumstances.
	fmt.Printf("wrote: %d, error: %v\ntransfer: %q\n", n, err, buf.String())
	// Output:
	// wrote: 91, error: <nil>
	// transfer: "The changing of down comforters to cotton bedspreads always meant the gophers had returned."
}
```
