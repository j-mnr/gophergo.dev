---
Title: "time"
Draft: false
---

## Source Code

🛝 [Playground link](https://goplay.tools/snippet/jByh-Mrf1Ss)

## Other packages used

- [`fmt`]()
- [`math`]()
- [`strconv`]()

## Constants and Variables

```go
const (
	Nanosecond  time.Duration = 1
	Microsecond               = 1000 * Nanosecond
	Millisecond               = 1000 * Microsecond
	Second                    = 1000 * Millisecond
	Minute                    = 60 * Second
	Hour                      = 60 * Minute
)

// NOTE(jay): Predefined layouts for [time.Time.Format] and [time.Parse].
const (
	Layout      = "01/02 03:04:05PM '06 -0700" // The reference time, in numerical order.
	ANSIC       = "Mon Jan _2 15:04:05 2006"
	UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
	RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
	RFC822      = "02 Jan 06 15:04 MST"
	RFC822Z     = "02 Jan 06 15:04 -0700" // RFC822 with numeric zone
	RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
	RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
	RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // RFC1123 with numeric zone
	RFC3339     = "2006-01-02T15:04:05Z07:00"
	RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
	Kitchen     = "3:04PM"
	// Handy time stamps.
	Stamp      = "Jan _2 15:04:05"
	StampMilli = "Jan _2 15:04:05.000"
	StampMicro = "Jan _2 15:04:05.000000"
	StampNano  = "Jan _2 15:04:05.000000000"
)

const (
	January time.Month = 1 + iota
	February
	March
	April
	May
	June
	July
	August
	September
	October
	November
	December
)

const (
	Sunday time.Weekday = iota
	Monday
	Tuesday
	Wednesday
	Thursday
	Friday
	Saturday
)

var (
	LocationUTC   = time.UTC
	LocationLocal = time.Local
)
```

## Examples

### `After`

```go
func ExampleAfter() {
	// NOTE(docs): The underlying [time.Timer] is not recovered by the garbage collector
	// until the timer fires. If efficiency is a concern, use [time.NewTimer] instead and
	// call [time.Timer.Stop] if the timer is no longer needed.
	t := time.Now()
	neverfire := make(chan time.Time, 1)
	for i := 0; i < 3; i++ {
		select {
		case <-neverfire:
			fmt.Println("We will never enter this case")
		case <-time.After(30 * time.Millisecond): // == <-time.NewTimer(30 * time.Millisecond).C
			fmt.Println("Took too long to fire accumulated time:",
				-t.Sub(time.Now()).Truncate(time.Millisecond))
		}
	}
	// Output:
	// Took too long to fire accumulated time: 30ms
	// Took too long to fire accumulated time: 60ms
	// Took too long to fire accumulated time: 90ms
}
```

### `Sleep`

```go
func ExampleSleep() {
	t := time.Now()
	time.Sleep(time.Millisecond) // [time.Millisecond] is a [time.Duration]
	fmt.Printf("time slept: %s\n", -t.Sub(time.Now()).Truncate(time.Millisecond))
	time.Sleep(1000 * time.Nanosecond)
	fmt.Printf("time slept: %s\n", -t.Sub(time.Now()).Truncate(time.Millisecond))
	time.Sleep(40*time.Millisecond + 16*time.Microsecond + 84*time.Nanosecond)
	fmt.Printf("time slept: %s\n", -t.Sub(time.Now()).Truncate(time.Millisecond))
	// Output:
	// time slept: 1ms
	// time slept: 1ms
	// time slept: 41ms
}
```

### `Tick`

```go
func ExampleTick() {
	// XXX(jay): This leaks memory!!! Use at your own risk or use [time.NewTicker] instead.
	t := time.Now()
	tkr := time.Tick(30 * time.Millisecond)
	tmr := time.After(100 * time.Millisecond)
	for keepGoing := true; keepGoing; {
		select {
		case <-tkr:
			fmt.Println("Ticker fired, accumulated time:",
				-t.Sub(time.Now()).Truncate(time.Millisecond))
		case <-tmr:
			keepGoing = false
		}
	}
	fmt.Println("total time elapsed:", -t.Sub(time.Now()).Truncate(time.Millisecond))
	// Output:
	// Ticker fired, accumulated time: 30ms
	// Ticker fired, accumulated time: 60ms
	// Ticker fired, accumulated time: 90ms
	// total time elapsed: 100ms
}
```

### `ParseDuration`

```go
func ExampleParseDuration() {
	showDur := func(s string) {
		d, _ := time.ParseDuration(s)
		fmt.Printf("string: %q parsed to duration: %q\n", s, d)
	}
	showDur("123456789ns") // nanoseconds
	fmt.Println()

	// NOTE(jay): There is support for both the scientific micro symbol \u00b5 and the greek
	// letter "mu" \u03bc. They will be interpreted to microseconds.
	showDur("987654321\u00b5s")
	showDur("987654321\u03bcs")
	// NOTE(jay): In vim in INSERT mode, you can hit <C-v> and the characters: u00b5 to get
	// unicode character micro symbol to write to the buffer. Same for u03bc. If you don't
	// use vim the time API also accepts the ASCII letter "u" as a substitute.
	showDur("987654321µs") // == \u00b5 or <C-v>u00b5
	showDur("987654321μs") // == \u03bc or <C-v>u03bc
	showDur("987654321us") // == ASCII 117
	fmt.Println()

	showDur("123456789ms")          // milliseconds
	showDur("987654321.123456789s") // seconds
	showDur("123456789m")           // minutes
	showDur("1234h")                // hours
	fmt.Println()

	showDur("2562047h47m16.854775807s")  // max duration possible
	showDur("+12h34m56s778ms991µs233ns") // The kitchen sink
	showDur("-98h76m54.321098765s")      // negative value
	showDur("-1h1.1234567890123456789s") // more precision drops off after nano (first "9")
	// Output:
	// string: "123456789ns" parsed to duration: "123.456789ms"
	//
	// string: "987654321µs" parsed to duration: "16m27.654321s"
	// string: "987654321μs" parsed to duration: "16m27.654321s"
	// string: "987654321µs" parsed to duration: "16m27.654321s"
	// string: "987654321μs" parsed to duration: "16m27.654321s"
	// string: "987654321us" parsed to duration: "16m27.654321s"
	//
	// string: "123456789ms" parsed to duration: "34h17m36.789s"
	// string: "987654321.123456789s" parsed to duration: "274348h25m21.123456789s"
	// string: "123456789m" parsed to duration: "2057613h9m0s"
	// string: "1234h" parsed to duration: "1234h0m0s"
	//
	// string: "2562047h47m16.854775807s" parsed to duration: "2562047h47m16.854775807s"
	// string: "+12h34m56s778ms991µs233ns" parsed to duration: "12h34m56.778991233s"
	// string: "-98h76m54.321098765s" parsed to duration: "-99h16m54.321098765s"
	// string: "-1h1.1234567890123456789s" parsed to duration: "-1h0m1.123456789s"
}
```

### `ParseDuration_errors`

```go
func ExampleParseDuration_errors() {
	showDur := func(s string) {
		d, err := time.ParseDuration(s)
		fmt.Printf("error: %v, string: %q zero value duration: %q\n", err, s, d)
	}
	showDur("")           // Don't pass empty string
	showDur("  1m  ")     // strip your strings of spaces
	showDur("987654321h") // error too many hours
	// NOTE(jay): only accepted values "ns" "us" "µs" "μs" "ms" "s" "m" "h"
	showDur("987654321x")
	showDur("123456789") // MUST put a unit
	// Output:
	// error: time: invalid duration "", string: "" zero value duration: "0s"
	// error: time: invalid duration "  1m  ", string: "  1m  " zero value duration: "0s"
	// error: time: invalid duration "987654321h", string: "987654321h" zero value duration: "0s"
	// error: time: unknown unit "x" in duration "987654321x", string: "987654321x" zero value duration: "0s"
	// error: time: missing unit in duration "123456789", string: "123456789" zero value duration: "0s"
}
```

### `Since`

```go
func ExampleSince() {
	// NOTE(jay): Compare with [time.Until]
	dostuff := func(t time.Time) {
		//  👇 == time.Now().Sub(stubTime())
		if time.Since(t) > 10*time.Second {
			fmt.Println("doing important thing (switch service, call different endpoint, ping node)")
		}
	}
	dostuff(time.Now().Add(11 * time.Second))
	dostuff(time.Now().Add(-11 * time.Second))
	// Output:
	// doing important thing (switch service, call different endpoint, ping node)
}
```

### `Until`

```go
func ExampleUntil() {
	// NOTE(jay): Compare with [time.Since]
	dostuff := func(t time.Time) {
		//  👇 == stubTime().Sub(time.Now())
		if time.Until(t) < 5*time.Minute {
			fmt.Println("doing that important thing (refresh token, send metrics, get pulse)")
		}
	}
	dostuff(time.Now().Add(10 * time.Minute))
	dostuff(time.Now().Add(-10 * time.Minute))
	// Output:
	// doing that important thing (refresh token, send metrics, get pulse)
}
```

### `Duration`

```go
func ExampleDuration() {
	// NOTE(jay): A [time.Duration] is just a type based off of int64. This means it's
	// comparable (from int64), orderable (from int64), provides type safety for clearer
	// APIs, and focuses the intention of all of the representations of what an int64 could
	// be. Here's the table of how it looks:
	//
	// Nanosecond  Duration = 1
	// Microsecond          = 1000 * Nanosecond
	// Millisecond          = 1000 * Microsecond
	// Second               = 1000 * Millisecond
	// Minute               = 60 * Second
	// Hour                 = 60 * Minute
	pollServer := func(d time.Duration) {
		if d < time.Millisecond {
			fmt.Println(d, "is too quick to poll the server for information!")
			return
		}
		fmt.Println("polling server every", d)
	}
	configuredAmount := 1000
	// Must cast to show intention of basing off of a [time.Duration]. Still could be better
	// as 1000 says nothing. 1000 seconds, minutes, nanoseconds?!
	pollServer(time.Duration(configuredAmount))
	pollServer(time.Duration(configuredAmount) * time.Millisecond)
	pollServer(1 * time.Hour)
	pollServer(1*time.Hour + 30*time.Minute + 45*time.Second)
	pollServer(2*time.Hour - 70*time.Minute - 30*time.Second)
	pollServer(1 * time.Hour / 3)
	pollServer(1 * time.Second / 3)
	pollServer(10000) // Probably not what you want
	// Output:
	// 1µs is too quick to poll the server for information!
	// polling server every 1s
	// polling server every 1h0m0s
	// polling server every 1h30m45s
	// polling server every 49m30s
	// polling server every 20m0s
	// polling server every 333.333333ms
	// 10µs is too quick to poll the server for information!
}
```

#### `stubDuration`

```go
func stubDuration() time.Duration {
	d, err := time.ParseDuration("9h8m7s6ms5µs4ns")
	if err != nil {
		panic(err)
	}
	return d
}
```

### `Duration.Abs`

```go
func ExampleDuration_Abs() {
	neg, _ := time.ParseDuration("-1234h5678m")
	fmt.Printf(`
absolute value of positive duration: %q
absolute value of negative duration: %q
absolute value of special case:      %q
`[1:],
		stubDuration().Abs(),
		neg.Abs(),
		time.Duration(math.MinInt64).Abs())
	// Output:
	// absolute value of positive duration: "9h8m7.006005004s"
	// absolute value of negative duration: "1328h38m0s"
	// absolute value of special case:      "2562047h47m16.854775807s"
}
```

### `Duration.Hours`

```go
func ExampleDuration_Hours() {
	d := stubDuration()
	fmt.Printf("duration: %s and hours: %f\n", d, d.Hours())
	// Output:
	// duration: 9h8m7.006005004s and hours: 9.135279
}
```

### `Duration.Microseconds`

```go
func ExampleDuration_Microseconds() {
	d := stubDuration()
	fmt.Printf("duration: %s and microseconds: %d\n", d, d.Microseconds())
	// Output:
	// duration: 9h8m7.006005004s and microseconds: 32887006005
}
```

### `Duration.Milliseconds`

```go
func ExampleDuration_Milliseconds() {
	d := stubDuration()
	fmt.Printf("duration: %s and milliseconds: %d\n", d, d.Milliseconds())
	// Output:
	// duration: 9h8m7.006005004s and milliseconds: 32887006
}
```

### `Duration.Minutes`

```go
func ExampleDuration_Minutes() {
	d := stubDuration()
	fmt.Printf("duration: %s and minutes: %f\n", d, d.Minutes())
	// Output:
	// duration: 9h8m7.006005004s and minutes: 548.116767
}
```

### `Duration.Nanoseconds`

```go
func ExampleDuration_Nanoseconds() {
	d := stubDuration()
	fmt.Printf("duration: %s and nanoseconds: %d\n", d, d.Nanoseconds())
	// Output:
	// duration: 9h8m7.006005004s and nanoseconds: 32887006005004
}
```

### `Duration.Round`

```go
func ExampleDuration_Round() {
	// NOTE(jay): Rounds AWAY from zero on halfway values compare with
	// [time.Duration.Truncate].
	rounding := func(d time.Duration, m time.Duration) {
		fmt.Printf("without rounding:\t\t\t%q\nrounding (to a multiple of %q):\t%q\n\n",
			d, m, d.Round(m))
	}
	rounding(13*time.Second, 5*time.Second)
	rounding(27*time.Hour, 4*time.Hour)
	rounding(55*time.Minute, 10*time.Minute)
	rounding(150*time.Millisecond, 100*time.Millisecond)
	rounding(1800*time.Second, 997*time.Second)
	// Doesn't round AWAY always. Nearest multiple is 997, which it hits, instead of 1993.
	rounding(1000*time.Millisecond, 997*time.Millisecond)

	rounding(12345678900*time.Nanosecond, 1*time.Second)
	rounding(9876543210*time.Nanosecond, 0) // Returns d unchanged
	// Output:
	// without rounding:			"13s"
	// rounding (to a multiple of "5s"):	"15s"
	//
	// without rounding:			"27h0m0s"
	// rounding (to a multiple of "4h0m0s"):	"28h0m0s"
	//
	// without rounding:			"55m0s"
	// rounding (to a multiple of "10m0s"):	"1h0m0s"
	//
	// without rounding:			"150ms"
	// rounding (to a multiple of "100ms"):	"200ms"
	//
	// without rounding:			"30m0s"
	// rounding (to a multiple of "16m37s"):	"33m14s"
	//
	// without rounding:			"1s"
	// rounding (to a multiple of "997ms"):	"997ms"
	//
	// without rounding:			"12.3456789s"
	// rounding (to a multiple of "1s"):	"12s"
	//
	// without rounding:			"9.87654321s"
	// rounding (to a multiple of "0s"):	"9.87654321s"
}
```

### `Duration.Seconds`

```go
func ExampleDuration_Seconds() {
	d := stubDuration()
	fmt.Printf("duration: %s and seconds: %f\n", d, d.Seconds())
	// Output:
	// duration: 9h8m7.006005004s and seconds: 32887.006005
}
```

### `Duration.String`

```go
func ExampleDuration_String() {
	fmt.Printf("zero value: %q\n", time.Duration(0))
	// NOTE(jay): the call to String() is to be explicit, but it's unnecessary. Look into
	// the [fmt] package for more information.
	fmt.Printf("inserts units ('h', 'm', 's', etc.): %q\n", stubDuration().String())
	smol, _ := time.ParseDuration("00.00123456s")
	fmt.Printf("never starts with 0: %q\n", smol)
	// Output:
	// zero value: "0s"
	// inserts units ('h', 'm', 's', etc.): "9h8m7.006005004s"
	// never starts with 0: "1.23456ms"
}
```

### `Duration.Truncate`

```go
func ExampleDuration_Truncate() {
	// NOTE(jay): Rounds TOWARDS zero ALWAYS compare with [time.Duration.Round]
	trunc := func(d time.Duration, m time.Duration) {
		fmt.Printf("without truncating:\t\t\t%q\ntruncating (to a multiple of %q):\t%q\n\n",
			d, m, d.Truncate(m))
	}
	trunc(13*time.Second, 5*time.Second)
	trunc(27*time.Hour, 4*time.Hour)
	trunc(55*time.Minute, 10*time.Minute)
	trunc(150*time.Millisecond, 100*time.Millisecond)
	trunc(1800*time.Second, 997*time.Second)
	// ALWAYS rounds down. Next multiple is 1994, but hits 997.
	trunc(1993*time.Microsecond, 997*time.Microsecond)

	trunc(12345678900*time.Nanosecond, 1*time.Second)
	trunc(9876543210*time.Nanosecond, 0) // Returns d unchanged
	// Output:
	// without truncating:			"13s"
	// truncating (to a multiple of "5s"):	"10s"
	//
	// without truncating:			"27h0m0s"
	// truncating (to a multiple of "4h0m0s"):	"24h0m0s"
	//
	// without truncating:			"55m0s"
	// truncating (to a multiple of "10m0s"):	"50m0s"
	//
	// without truncating:			"150ms"
	// truncating (to a multiple of "100ms"):	"100ms"
	//
	// without truncating:			"30m0s"
	// truncating (to a multiple of "16m37s"):	"16m37s"
	//
	// without truncating:			"1.993ms"
	// truncating (to a multiple of "997µs"):	"997µs"
	//
	// without truncating:			"12.3456789s"
	// truncating (to a multiple of "1s"):	"12s"
	//
	// without truncating:			"9.87654321s"
	// truncating (to a multiple of "0s"):	"9.87654321s"
}
```

### `FixedZone`

```go
func ExampleFixedZone() {
	// NOTE(jay): To be FIXED means Daylight Saving Time is not recognized so there is no
	// shift in hours throughout the year.
	fixt := time.FixedZone("fakeUTC10", int((10 * time.Hour).Seconds())) // == 10*60*60
	t, _ := time.ParseInLocation(time.ANSIC, "Mon Jan 2 15:04:05 2006", fixt)
	loc, _ := time.LoadLocation("Australia/Sydney")
	t2, _ := time.ParseInLocation(time.ANSIC, "Mon Jan 2 15:04:05 2006", loc)
	fmt.Printf(`
fixed zone:               %q
fixed zone in future:     %q
not-fixed zone:           %q
not-fixed zone in future: %q`[1:],
		t, t.Add(4*30*24*time.Hour),
		t2, t2.Add(4*30*24*time.Hour),
	)
	// Output:
	// fixed zone:               "2006-01-02 15:04:05 +1000 fakeUTC10"
	// fixed zone in future:     "2006-05-02 15:04:05 +1000 fakeUTC10"
	// not-fixed zone:           "2006-01-02 15:04:05 +1100 AEDT"
	// not-fixed zone in future: "2006-05-02 14:04:05 +1000 AEST"
}
```

### `LoadLocation`

```go
func ExampleLoadLocation() {
	// NOTE(jay): You can find all of the recognized locations from
	// 	`$GOROOT/lib/time/zoneinfo.zip`
	// If you use vim you can run
	// 	`vim $(go env GOROOT)/lib/time/zoneinfo.zip` to see the names without unzipping
	// the contents.

	// NOTE(docs):
	// LoadLocation looks for the IANA Time Zone database in the following
	// locations in order:
	// - the directory or uncompressed zip file named by the ZONEINFO environment variable
	// - on a Unix system, the system standard installation location
	// - $GOROOT/lib/time/zoneinfo.zip
	// - the time/tzdata package, if it was imported

	whereami := func(name string) {
		loc, err := time.LoadLocation(name)
		fmt.Printf("location: %q, any error: %v\n", loc, err)
	}
	whereami("Zulu")
	whereami("US/Eastern")
	whereami("Etc/GMT+5")
	whereami("Pacific/Fiji")
	whereami("Europe/Prague")
	whereami("Does not exist")
	// Output:
	// location: "Zulu", any error: <nil>
	// location: "US/Eastern", any error: <nil>
	// location: "Etc/GMT+5", any error: <nil>
	// location: "Pacific/Fiji", any error: <nil>
	// location: "Europe/Prague", any error: <nil>
	// location: "UTC", any error: unknown time zone Does not exist
}
```

### `LoadLocationFromTZData`

```go
func ExampleLoadLocationFromTZData() {
	data := []byte{
		'T', 'Z', 'i', 'f', '2', 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
		0, 0, 0, 0, 0, 0, 0, 0, 0, 0, '', 0, 0, 0, '', 0, 0, 0, '', '', 0, 0, 0,
		' ', '', '¢', '', '¡', 'y', '', 'ð', 'È', 'Y', '^', '', 'É', '	', 'ù',
		'p', 'É', 'Ó', '½', 0, 'Ë', '', '', 'ð', 'Ë', '|', '@', 0, 'Ò', ';', '>', 'ð',
		'Ó', '', '{', '', 'Ô', 'B', '­', 'ð', 'Õ', 'E', '"', 0, 'Ö', 'L', '¿', 'ð',
		'×', '<', '¿', 0, 'Ø', '', 'f', 'p', 'Ù', '', 'ò', '', 'Ù', 'A', '|', 'ð',
		'', 'º', 'R', ' ', '', 'i', '', '', ' ', '~', '', ' ', '!', 'I', '}',
		'', '"', 'g', '¡', ' ', '#', ')', '_', '', '$', 'G', '', ' ', '%', '',
		'|', '', '&', '\'', 'e', ' ', '&', 'ò', '^', '', '(', '', 'G', ' ', '(', 'Ò',
		'@', '', '', '', '', '', '', '', '', '', '', '', '', '',
		'', '', '', '', '', '', '', '', '', '', '', '', '', '',
		'', '', '', 0, 0, 'q', '×', 0, 0, 0, 0, '~', '', '', '', 0, 0, 'p',
		'', 0, '', 'L', 'M', 'T', 0, 'C', 'D', 'T', 0, 'C', 'S', 'T', 0, 'T', 'Z', 'i',
		'f', '2', 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
		0, 0, 0, 0, 0, '', 0, 0, 0, '', 0, 0, 0, '', 'ÿ', 'ÿ', 'ÿ', 'ÿ', '~', '6', 'C',
		')', 'ÿ', 'ÿ', 'ÿ', 'ÿ', ' ', '', '¢', '', 'ÿ', 'ÿ', 'ÿ', 'ÿ', '¡', 'y', '',
		'ð', 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'È', 'Y', '^', '', 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'É', '	', 'ù',
		'p', 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'É', 'Ó', '½', 0, 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'Ë', '', '', 'ð',
		'ÿ', 'ÿ', 'ÿ', 'ÿ', 'Ë', '|', '@', 0, 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'Ò', ';', '>', 'ð', 'ÿ',
		'ÿ', 'ÿ', 'ÿ', 'Ó', '', '{', '', 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'Ô', 'B', '­', 'ð', 'ÿ',
		'ÿ', 'ÿ', 'ÿ', 'Õ', 'E', '"', 0, 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'Ö', 'L', '¿', 'ð', 'ÿ', 'ÿ',
		'ÿ', 'ÿ', '×', '<', '¿', 0, 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'Ø', '', 'f', 'p', 'ÿ', 'ÿ', 'ÿ',
		'ÿ', 'Ù', '', 'ò', '', 'ÿ', 'ÿ', 'ÿ', 'ÿ', 'Ù', 'A', '|', 'ð', 0, 0, 0, 0, '',
		'º', 'R', ' ', 0, 0, 0, 0, '', 'i', '', '', 0, 0, 0, 0, ' ', '~', '',
		' ', 0, 0, 0, 0, '!', 'I', '}', '', 0, 0, 0, 0, '"', 'g', '¡', ' ', 0, 0, 0, 0,
		'#', ')', '_', '', 0, 0, 0, 0, '$', 'G', '', ' ', 0, 0, 0, 0, '%', '', '|',
		'', 0, 0, 0, 0, '&', '\'', 'e', ' ', 0, 0, 0, 0, '&', 'ò', '^', '', 0, 0, 0, 0,
		'(', '', 'G', ' ', 0, 0, 0, 0, '(', 'Ò', '@', '', '', '', '', '', '',
		'', '', '', '', '', '', '', '', '', '', '', '', '', '',
		'', '', '', '', '', '', '', '', '', '', 0, 0, 'q', '×', 0, 0, 0,
		0, '~', '', '', '', 0, 0, 'p', '', 0, '', 'L', 'M', 'T', 0, 'C', 'D',
		'T', 0, 'C', 'S', 'T', 0, 'C', 'S', 'T', '-', '8',
	}
	loc, err := time.LoadLocationFromTZData("Asia/Shanghai", data)
	fmt.Println(loc, err)
	t, _ := time.ParseInLocation(time.ANSIC, "Mon Jan 2 15:04:05 2006", loc)
	fmt.Println(t)
	loc, err = time.LoadLocationFromTZData("MT", []byte{})
	fmt.Println(loc, "error:", err)
	// Output:
	// Asia/Shanghai <nil>
	// 2006-01-02 15:04:05 +0800 CST
	// UTC error: malformed time zone information
}
```

### `Local`

```go
func ExampleLocal() { // variable of type *Location
	t, _ := time.ParseInLocation(time.ANSIC, "Mon Jan 2 15:04:05 2006", time.Local)
	// XXX(jay): This may fail for you depending on where you are. I'm on US East coast so
	// my local time is Eastern.
	fmt.Println(t)
	// Output:
	// 2006-01-02 15:04:05 -0500 EST
}
```

### `UTC`

```go
func ExampleUTC() { // variable of type *Location
	t, _ := time.ParseInLocation(time.ANSIC, "Mon Jan 2 15:04:05 2006", time.UTC)
	fmt.Println(t)
	// Output:
	// 2006-01-02 15:04:05 +0000 UTC
}
```

### `Location.String`

```go
func ExampleLocation_String() {
	fmt.Println(time.FixedZone("Just grabs this string aka it's name", 0))
	// Output:
	// Just grabs this string aka it's name
}
```

### `Month.String`

```go
func ExampleMonth_String() {
	fmt.Println("Months of the year:")
	for i := time.Month(1); i <= time.December; i++ {
		fmt.Println(i)
	}
	// Output:
	// Months of the year:
	// January
	// February
	// March
	// April
	// May
	// June
	// July
	// August
	// September
	// October
	// November
	// December
}
```

### `ParseError.Error`

```go
func ExampleParseError_Error() {
	// NOTE(jay): This is only ever returned from [time.Parse]. You should never have to
	// create your own [time.ParseError].
	tickterr := func(broke, layout, value string) {
		_, err := time.Parse(layout, value)
		fmt.Printf("broke: %s: %v\n", broke, err)
	}
	// Good time: "2006-01-02T15:04:05Z07:00"
	tickterr("format", time.RFC1123, "2006-01-02T15:04:05Z07:00")
	tickterr("month", time.RFC3339, "2006-31-02T15:04:05Z07:00")
	tickterr("day", time.RFC3339, "2006-01-35T15:04:05Z")
	tickterr("hour", time.RFC3339, "2006-01-02T25:04:05Z07:00")
	tickterr("yearday", "Mon, __2 Jan 2006 15:04:05 -0700", "Sun, 372 Dec 2006 15:04:05 -0700")
	tickterr("extra", time.RFC1123, "Mon, 02 Jan 2006 15:04:05 MST       ")

	// Output:
	// broke: format: parsing time "2006-01-02T15:04:05Z07:00" as "Mon, 02 Jan 2006 15:04:05 MST": cannot parse "2006-01-02T15:04:05Z07:00" as "Mon"
	// broke: month: parsing time "2006-31-02T15:04:05Z07:00": month out of range
	// broke: day: parsing time "2006-01-35T15:04:05Z": day out of range
	// broke: hour: parsing time "2006-01-02T25:04:05Z07:00": hour out of range
	// broke: yearday: parsing time "Sun, 372 Dec 2006 15:04:05 -0700": day-of-year out of range
	// broke: extra: parsing time "Mon, 02 Jan 2006 15:04:05 MST       ": extra text: "       "
}
```

### `NewTicker`

```go
func ExampleNewTicker() {
	var tkr *time.Ticker
	t := time.Now()
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("You put in zero seconds didn't you? Panic!", r)
			tkr = time.NewTicker(50 * time.Millisecond)
		}
		for i := 0; i < 4; i++ {
			fmt.Printf("time passed: %s\n", -t.Sub(<-tkr.C).Truncate(50*time.Millisecond))
		}
	}()
	tkr = time.NewTicker(0) // XXX(jay): Don't put any negative values either
	// Output:
	// You put in zero seconds didn't you? Panic! non-positive interval for NewTicker
	// time passed: 50ms
	// time passed: 100ms
	// time passed: 150ms
	// time passed: 200ms
}
```

### `Ticker.Reset`

```go
func ExampleTicker_Reset() {
	// NOTE(jay): Compare with [Timer.Reset]
	tkr := time.NewTicker(8 * time.Minute)
	t := time.Now()
	// XXX(jay): Do NOT put anything below 1 into Reset() it will panic.
	tkr.Reset(20 * time.Millisecond)
	for i := 0; i < 3; i++ {
		fmt.Printf("time passed: %s\n", -t.Sub(<-tkr.C).Round(20*time.Millisecond))
	}
	// Output:
	// time passed: 20ms
	// time passed: 40ms
	// time passed: 60ms
}
```

### `Ticker.Stop`

```go
func ExampleTicker_Stop() {
	// NOTE(jay): Compare with [Timer.Stop]
	tkr := time.NewTicker(40 * time.Millisecond)
	t := time.Now()
	tmr := time.NewTimer(90 * time.Millisecond)
	for stayIn := true; stayIn; {
		select {
		case tick := <-tkr.C:
			fmt.Printf("time passed: %s\n", -t.Sub(tick).Truncate(40*time.Millisecond))
		case <-tmr.C:
			tmr.Stop()
			fmt.Println("Stop ticker")
			tkr.Stop() // No output for Stop()
			stayIn = false
		}
	}
	// XXX(jay): THIS WILL BLOCK FOREVER! Stop() does not close the channel. So that you
	// don't get zero value [time.Time]s for whoever is listening. Also a receive only
	// channel (`<-chan`) cannot be closed.
	// <-tkr.C
	fmt.Println("No more ticks")
	// Output:
	// time passed: 40ms
	// time passed: 80ms
	// Stop ticker
	// No more ticks
}
```

### `Date`

```go
func ExampleDate() {
	// NOTE(jay): The return value, [time.Time], is **NOT** a pointer.
	date := func(t time.Time) {
		fmt.Printf("The call to Date() gives: %q\n", t)
	}
	//             Year   Month    Day Hour Min Sec Nanosec Location
	date(time.Date(2053, time.June, 23, 16, 30, 12, 69420, time.UTC))
	date(time.Date(2053, time.July, -7, 16, 30, 12, 69420, time.UTC))
	date(time.Date(2053, time.July, -7, 12, 270, 12, 69420, time.UTC))
	date(time.Date(2052, time.December, 205, 12, 270, 12, 69420, time.UTC))
	date(time.Date(2053, time.June, 0, 0, 0, 2046612, 69420, time.UTC))
	date(time.Date(2053, time.June, 0, 0, 0,
		int((23*24*time.Hour + 16*time.Hour + 30*time.Minute + 12*time.Second).Seconds()),
		69420, time.UTC))

	defer func() {
		if r := recover(); r != nil {
			fmt.Println("Panic!", r)
		}
	}()
	date(time.Date(0, 0, 0, 0, 0, 0, 0, time.UTC))
	time.Date(2053, time.June, 23, 16, 30, 12, 69420, nil)
	// Output:
	// The call to Date() gives: "2053-06-23 16:30:12.00006942 +0000 UTC"
	// The call to Date() gives: "2053-06-23 16:30:12.00006942 +0000 UTC"
	// The call to Date() gives: "2053-06-23 16:30:12.00006942 +0000 UTC"
	// The call to Date() gives: "2053-06-23 16:30:12.00006942 +0000 UTC"
	// The call to Date() gives: "2053-06-23 16:30:12.00006942 +0000 UTC"
	// The call to Date() gives: "2053-06-23 16:30:12.00006942 +0000 UTC"
	// The call to Date() gives: "-0001-11-30 00:00:00 +0000 UTC"
	// Panic! time: missing Location in call to Date
}
```

### `Now`

```go
func ExampleNow() {
	// NOTE(jay): The return value, [time.Time], is **NOT** a pointer and contains a
	// monotonic clock for calculations with [time.Time.Sub], [time.Time.Before],
	// [time.Since], [time.Until]. Useful for timing calls in the program.

	// XXX(jay): This will always fail because time keeps moving forward, but the Example is
	// fine for that instant in time.
	fmt.Println("time with monotonic (m=) clock:", time.Now())
	// Output:
	// time with monotonic (m=) clock: 2022-10-24 13:59:35.330450335 -0400 EDT m=+0.587629146
}
```

### `Parse`

```go
func ExampleParse() {
	// NOTE(jay): The return value, [time.Time], is **NOT** a pointer.
	// NOTE(jay): To see all possible errors returned look at [ExampleParseError_Error].
	t, _ := time.Parse(time.ANSIC, "Mon Jun 23 16:15:04 2053")
	fmt.Println("time with no location information:", t)
	// NOTE(jay): This only works because I'm in the East side of the USA. If I switched it
	// to PST (west coast USA) the timezone information would not be captured. To capture
	// that information use [time.ParseInLocation].
	t, _ = time.Parse(time.UnixDate, "Mon Jun 23 16:15:04 EST 2053")
	fmt.Println("date-time in my location:         ", t)
	t, _ = time.Parse(time.UnixDate, "Mon Jun 23 16:15:04 PST 2053")
	fmt.Println("date-time in other location:      ", t)
	// Output:
	// time with no location information: 2053-06-23 16:15:04 +0000 UTC
	// date-time in my location:          2053-06-23 17:15:04 -0400 EDT
	// date-time in other location:       2053-06-23 16:15:04 +0000 PST
}
```

### `ParseInLocation`

```go
func ExampleParseInLocation() {
	// NOTE(jay): The return value, [time.Time], is **NOT** a pointer.
	loc, _ := time.LoadLocation("US/Central")
	// NOTE(jay): To see all possible errors returned look at [ExampleParseError_Error].
	// NOTE(docs): in the absence of time zone information, [time.Parse] interprets a time
	// as UTC; [time.ParseInLocation] interprets the time as in the given location.
	t, _ := time.ParseInLocation(time.ANSIC, "Mon Jun 23 16:15:04 2053", loc)
	fmt.Println("time in US/Central location:", t)
	// NOTE(docs): when given a zone offset or abbreviation, Parse tries to match it against
	// the Local location; ParseInLocation uses the given location.
	loc, _ = time.LoadLocation("US/Pacific")
	t, _ = time.ParseInLocation(time.UnixDate, "Mon Jun 23 16:15:04 PST 2053", loc)
	fmt.Println("time in US/Pacific location:", t)
	t, _ = time.ParseInLocation(time.ANSIC, "Mon Jun 23 16:15:04 2053", time.Local)
	fmt.Println("Local time:", t)
	t, _ = time.ParseInLocation(time.ANSIC, "Mon Jun 23 16:15:04 2053", time.UTC)
	fmt.Println("UTC time:  ", t)
	// Output:
	// time in US/Central location: 2053-06-23 16:15:04 -0500 CDT
	// time in US/Pacific location: 2053-06-23 17:15:04 -0700 PDT
	// Local time: 2053-06-23 16:15:04 -0400 EDT
	// UTC time:   2053-06-23 16:15:04 +0000 UTC
}
```

### `Unix`

```go
func ExampleUnix() {
	// NOTE(jay): The return value, [time.Time], is **NOT** a pointer.

	// NOTE(jay): The epoch (pronounced epic 🤘) is the "beginning" of time.
	epoch := time.Unix(0, 0)
	fmt.Println("The beginning of computer time:     ", epoch.UTC())
	fmt.Println("time given back uses your location: ", epoch)

	onlyns := time.Unix(0, 1666534916366123456)
	fmt.Println("not required to pass in seconds:    ", onlyns.UTC())
	onlys := time.Unix(1666534916, 0)
	fmt.Println("not required to pass in nanoseconds:", onlys.UTC())
	// Output:
	// The beginning of computer time:      1970-01-01 00:00:00 +0000 UTC
	// time given back uses your location:  1969-12-31 19:00:00 -0500 EST
	// not required to pass in seconds:     2022-10-23 14:21:56.366123456 +0000 UTC
	// not required to pass in nanoseconds: 2022-10-23 14:21:56 +0000 UTC
}
```

### `UnixMicro`

```go
func ExampleUnixMicro() {
	// NOTE(jay): The return value, [time.Time], is **NOT** a pointer.
	epoch := time.UnixMilli(0)
	fmt.Printf(`
time:                          %q
time returned with time.Local: %q
UTC:                           %q`[1:],
		stubTime(),
		time.UnixMicro(stubTime().Sub(epoch).Microseconds()),
		time.UnixMicro(stubTime().Sub(epoch).Microseconds()).UTC())
	// Output:
	// time:                          "2053-06-23 16:15:04 -0500 CDT"
	// time returned with time.Local: "2053-06-23 17:15:04 -0400 EDT"
	// UTC:                           "2053-06-23 21:15:04 +0000 UTC"
}
```

### `UnixMilli`

```go
func ExampleUnixMilli() {
	// NOTE(jay): The return value, [time.Time], is **NOT** a pointer.

	// NOTE(jay): Open up your web browser, navigate to the developer console (F12 usually),
	// and type in the following `Date.now()` and press Enter. A UnixMilli timestamp will
	// appear and TADA 🪄 your front-end (JS) and back-end (Go) now have an easy, compact,
	// standard format to share dates that transcend timezones and DST, with their own
	// supported APIs. 👍
	fmt.Println(time.UnixMilli(1666534916366))
	// Output:
	// 2022-10-23 10:21:56.366 -0400 EDT
}
```

#### `stubTime`

```go
func stubTime() time.Time {
	loc, _ := time.LoadLocation("US/Central")
	t, err := time.ParseInLocation(time.RFC3339, "2053-06-23T16:15:04-05:00", loc)
	if err != nil { // How the??
		panic(err)
	}
	return t
}
```

### `Time.Add`

```go
func ExampleTime_Add() {
	// NOTE(jay): Compare to [time.Time.Sub] which returns a [time.Duration]
	t := stubTime()
	// NOTE(jay): If you need to add something bigger than a day use [time.Time.AddDate]
	fmt.Printf("Add seconds:\t%q\n", t.Add(55*time.Second))
	fmt.Printf("Add minutes:\t%q\n", t.Add(32*time.Minute))
	fmt.Printf("Add hours:\t%q\n", t.Add(4*time.Hour))
	// Output:
	// Add seconds:	"2053-06-23 16:15:59 -0500 CDT"
	// Add minutes:	"2053-06-23 16:47:04 -0500 CDT"
	// Add hours:	"2053-06-23 20:15:04 -0500 CDT"
}
```

### `Time.AddDate`

```go
func ExampleTime_AddDate() {
	// NOTE(jay): If you need to add something smaller than a day use [time.Time.Add]
	t := stubTime()
	newtime := func(years, months, days int) {
		fmt.Printf("time before:\n%q\ntime after adding %d year(s), %d month(s), and %d day(s)\n%q\n\n",
			t, years, months, days, t.AddDate(years, months, days))
	}
	newtime(0, 0, 0)
	newtime(10, 10, 10)
	newtime(-20, -32, -100)
	newtime(-20000, 0, 0)

	// XXX(jay): This will always fail because time moves forward, but the Output is still
	// good for that exact moment in time.
	now := time.Now()
	fmt.Printf("with monotonic clock:    %q\nwithout monotonic clock: %q",
		now, now.Truncate(0))

	// Output:
	// time before:
	// "2053-06-23 16:15:04 -0500 CDT"
	// time after adding 0 year(s), 0 month(s), and 0 day(s)
	// "2053-06-23 16:15:04 -0500 CDT"
	//
	// time before:
	// "2053-06-23 16:15:04 -0500 CDT"
	// time after adding 10 year(s), 10 month(s), and 10 day(s)
	// "2064-05-03 16:15:04 -0500 CDT"
	//
	// time before:
	// "2053-06-23 16:15:04 -0500 CDT"
	// time after adding -20 year(s), -32 month(s), and -100 day(s)
	// "2030-07-15 16:15:04 -0500 CDT"
	//
	// time before:
	// "2053-06-23 16:15:04 -0500 CDT"
	// time after adding -20000 year(s), 0 month(s), and 0 day(s)
	// "-17947-06-23 16:15:04 -0550 LMT"
	//
	// with monotonic clock:    "2022-10-21 12:08:22.030193411 -0400 EDT m=+0.042259575"
	// without monotonic clock: "2022-10-21 12:08:22.030193411 -0400 EDT"
}
```

### `Time.After`

```go
func ExampleTime_After() {
	// NOTE(jay): For comparing. Also see [time.Time.Before] and [time.Time.Equal]
	t := stubTime()
	future := t.Add(10000 * time.Hour)
	past := t.AddDate(-31, 4, -5)
	compare := func(other time.Time) {
		if !t.After(other) {
			fmt.Printf("❌ %q is NOT after %q\n", t, other)
			return
		}
		fmt.Printf("✅ %q is after %q\n", t, other)
	}
	compare(future)
	compare(past)
	// Output:
	// ❌ "2053-06-23 16:15:04 -0500 CDT" is NOT after "2054-08-14 08:15:04 -0500 CDT"
	// ✅ "2053-06-23 16:15:04 -0500 CDT" is after "2022-10-18 16:15:04 -0500 CDT"
}
```

### `Time.AppendFormat`

```go
func ExampleTime_AppendFormat() {
	appends := make([]byte, 1<<2)
	nocap := make([]byte, 0, 1<<8)
	var fill []byte
	fmted := func(b []byte, format, title string) {
		// NOTE(jay): Like the name implies, if there was data in the byte slice before it
		// won't be overwritten and if there isn't enough space it will create a new slice
		// with enough space to hold the entire formatted string.
		a := stubTime().AppendFormat(b, format)
		fmt.Printf("b Len: %d, b Cap: %d\na Len: %d, a Cap: %d\n%s:\tresult: %q\n\n",
			len(b), cap(b), len(a), cap(a), title, a)
	}
	fmted(appends, time.RFC3339, "appends to end")

	fmted(fill, time.RFC1123, "fills up")

	fmted(nocap, time.Stamp, "cap the same")
	// Output:
	// b Len: 4, b Cap: 4
	// a Len: 29, a Cap: 32
	// appends to end:	result: "\x00\x00\x00\x002053-06-23T16:15:04-05:00"
	//
	// b Len: 0, b Cap: 0
	// a Len: 29, a Cap: 32
	// fills up:	result: "Mon, 23 Jun 2053 16:15:04 CDT"
	//
	// b Len: 0, b Cap: 256
	// a Len: 15, a Cap: 256
	// cap the same:	result: "Jun 23 16:15:04"
}
```

### `Time.Before`

```go
func ExampleTime_Before() {
	// NOTE(jay): For comparing. Also see [time.Time.After] and [time.Time.Equal]
	t := stubTime()
	future := t.Add(10000 * time.Hour)
	past := t.AddDate(-31, 4, -5)
	compare := func(other time.Time) {
		if !t.Before(other) {
			fmt.Printf("❌ %q is NOT before %q\n", t, other)
			return
		}
		fmt.Printf("✅ %q is before %q\n", t, other)
	}
	compare(future)
	compare(past)
	// Output:
	// ✅ "2053-06-23 16:15:04 -0500 CDT" is before "2054-08-14 08:15:04 -0500 CDT"
	// ❌ "2053-06-23 16:15:04 -0500 CDT" is NOT before "2022-10-18 16:15:04 -0500 CDT"
}
```

### `Time.Clock`

```go
func ExampleTime_Clock() {
	// NOTE(jay): batch call for [time.Time.Hour], [time.Time.Minute], [time.Time.Second].
	t := stubTime()
	h, m, s := t.Clock()
	fmt.Printf("Example:\nhour: %d, minute: %d, second: %d\n", h, m, s)
	// XXX(jay): This may fail to produce same output for you depending on your location.
	h, m, s = t.Local().Clock()
	fmt.Printf("Local:\nhour: %d, minute: %d, second: %d\n", h, m, s)
	h, m, s = t.UTC().Clock()
	fmt.Printf("UTC:\nhour: %d, minute: %d, second: %d\n", h, m, s)
	// Output:
	// Example:
	// hour: 16, minute: 15, second: 4
	// Local:
	// hour: 17, minute: 15, second: 4
	// UTC:
	// hour: 21, minute: 15, second: 4
}
```

### `Time.Date`

```go
func ExampleTime_Date() {
	// NOTE(jay): Batch call for [time.Time.Year], [time.Time.Month], [time.Time.Day].
	y, m, d := stubTime().Date()
	fmt.Printf("time: %q, year: %d, month: %s, day: %d\n", stubTime(), y, m, d)
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", year: 2053, month: June, day: 23
}
```

### `Time.Day`

```go
func ExampleTime_Day() {
	// NOTE(jay): Compare with [time.Time.Date]
	// NOTE(jay): Compare with [time.Time.Weekday] and [time.Time.YearDay]
	fmt.Printf("time: %q, day: %d", stubTime(), stubTime().Day())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", day: 23
}
```

### `Time.Equal`

```go
func ExampleTime_Equal() {
	// NOTE(jay): For comparing. Also see [time.Time.Before] and [time.Time.After]

	// NOTE(docs): the Go == operator compares not just the time instant but also
	// the Location and the monotonic clock reading. Therefore, Time values should
	// not be used as map or database keys without first guaranteeing that the
	// identical Location has been set for all values, which can be achieved
	// through use of the UTC or Local method, and that the monotonic clock reading
	// has been stripped by setting t = t.Round(0). In general, prefer t.Equal(u)
	// to t == u, since t.Equal uses the most accurate comparison available and
	// correctly handles the case when only one of its arguments has a monotonic
	// clock reading.

	dif := func(t1, t2 time.Time) {
		fmt.Printf(`
time1: %q
time2: %q
double equal is: %t
Equal method is: %t
standardize to UTC double equal: %t

`[1:],
			t1, t2, t1 == t2, t1.Equal(t2), t1.UTC() == t2.UTC())
	}
	// XXX(jay): This will always fail as time moves forward, but the output is sound.
	t := time.Now()
	dif(t, t.Round(0))

	t1, _ := time.ParseInLocation(time.RFC3339, "2053-06-23T16:15:04-05:00",
		time.FixedZone("Same", 0))
	t2, _ := time.ParseInLocation(time.RFC3339, "2053-06-23T16:15:04-05:00",
		time.FixedZone("Really", 0))
	dif(t1, t2)
	// Output:
	// time1: "2022-10-24 18:43:41.478903797 -0400 EDT m=+0.589706406"
	// time2: "2022-10-24 18:43:41.478903797 -0400 EDT"
	// double equal is: false
	// Equal method is: true
	// standardize to UTC double equal: true
	//
	// time1: "2053-06-23 16:15:04 -0500 -0500"
	// time2: "2053-06-23 16:15:04 -0500 -0500"
	// double equal is: false
	// Equal method is: true
	// standardize to UTC double equal: true
}
```

### `Time.Format`

```go
func ExampleTime_Format() {
	loc, _ := time.LoadLocation("US/Central")
	t, err := time.ParseInLocation(
		time.RFC3339Nano, "2053-06-23T16:15:04.123456789-05:00", loc)
	if err != nil { // How the??
		panic(err)
	}
	fmt.Printf("Layout:\t\t%q\n", t.Format(time.Layout))
	fmt.Printf("ANSIC:\t\t%q\n", t.Format(time.ANSIC))
	fmt.Printf("UnixDate:\t%q\n", t.Format(time.UnixDate))
	fmt.Printf("RubyDate:\t%q\n", t.Format(time.RubyDate))

	// NOTE(jay): RFC822, RFC850, and RFC1123 formats should be applied only to local times.
	// Applying them to UTC times will use "UTC" as the time zone abbreviation, while
	// strictly speaking those RFCs require the use of "GMT" in that case.
	fmt.Printf("RFC822:\t\t%q\n", t.Format(time.RFC822))
	fmt.Printf("RFC822Z:\t%q\n", t.Format(time.RFC822Z))
	fmt.Printf("RFC850:\t\t%q\n", t.Format(time.RFC850))
	// NOTE(jay): In general RFC1123Z should be used instead of RFC1123 for servers that
	// insist on that format, even better to use RFC3339.
	fmt.Printf("RFC1123:\t%q\n", t.Format(time.RFC1123))
	fmt.Printf("RFC1123Z:\t%q\n", t.Format(time.RFC1123Z))

	// NOTE(jay): RFC3339 should be preferred for new protocols over RFC1123 and RFC1123Z.
	fmt.Printf("RFC3339:\t%q\n", t.Format(time.RFC3339))
	fmt.Printf("RFC3339Nano:\t%q\n", t.Format(time.RFC3339Nano))

	fmt.Printf("Kitchen:\t%q\n", t.Format(time.Kitchen))

	// NOTE(jay): These work well for logging/metrics.
	fmt.Printf("Stamp:\t\t%q\n", t.Format(time.Stamp))
	fmt.Printf("StampMilli:\t%q\n", t.Format(time.StampMilli))
	fmt.Printf("StampMicro:\t%q\n", t.Format(time.StampMicro))
	fmt.Printf("StampNano:\t%q\n", t.Format(time.StampNano))

	// NOTE(jay): The refernce time this format must follow "01/02 03:04:05PM '06 -0700"
	fmt.Printf("Custom fmt:\t%q\n", t.Format("03:04:05PM (MST) Mon Jan 02 2006"))
	// Output:
	// Layout:		"06/23 04:15:04PM '53 -0500"
	// ANSIC:		"Mon Jun 23 16:15:04 2053"
	// UnixDate:	"Mon Jun 23 16:15:04 CDT 2053"
	// RubyDate:	"Mon Jun 23 16:15:04 -0500 2053"
	// RFC822:		"23 Jun 53 16:15 CDT"
	// RFC822Z:	"23 Jun 53 16:15 -0500"
	// RFC850:		"Monday, 23-Jun-53 16:15:04 CDT"
	// RFC1123:	"Mon, 23 Jun 2053 16:15:04 CDT"
	// RFC1123Z:	"Mon, 23 Jun 2053 16:15:04 -0500"
	// RFC3339:	"2053-06-23T16:15:04-05:00"
	// RFC3339Nano:	"2053-06-23T16:15:04.123456789-05:00"
	// Kitchen:	"4:15PM"
	// Stamp:		"Jun 23 16:15:04"
	// StampMilli:	"Jun 23 16:15:04.123"
	// StampMicro:	"Jun 23 16:15:04.123456"
	// StampNano:	"Jun 23 16:15:04.123456789"
	// Custom fmt:	"04:15:04PM (CDT) Mon Jun 23 2053"
}
```

### `Time.GoString`

```go
func ExampleTime_GoString() {
	fmt.Printf("time: %q\nsource code: %s\n", stubTime(), stubTime().GoString())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT"
	// source code: time.Date(2053, time.June, 23, 16, 15, 4, 0, time.Location("US/Central"))
}
```

### `Time.GobDecode`

```go
func ExampleTime_GobDecode() {
	// NOTE(jay): Unsafe for concurrent use.
	data, err := stubTime().GobEncode()
	back := &time.Time{}
	back.GobDecode(data)
	fmt.Printf(`
time:                     %q
marshalled:               %q
error:                    %v
unmarshalled (lost name): %q`[1:],
		stubTime(), data, err, back)
	// Output:
	// time:                     "2053-06-23 16:15:04 -0500 CDT"
	// marshalled:               "\x01\x00\x00\x00\x0f\x14\x96\x97X\x00\x00\x00\x00\xfe\xd4"
	// error:                    <nil>
	// unmarshalled (lost name): "2053-06-23 16:15:04 -0500 -0500"
}
```

### `Time.GobEncode`

```go
func ExampleTime_GobEncode() {
	// NOTE(jay): Loses information about Daylight Saving Time by removing the
	// [time.Time.Location]'s name.
	data, err := stubTime().GobEncode()
	back := &time.Time{}
	back.GobDecode(data)
	fmt.Printf(`
time:                     %q
marshalled:               %q
error:                    %v
unmarshalled (lost name): %q`[1:],
		stubTime(), data, err, back)
	// Output:
	// time:                     "2053-06-23 16:15:04 -0500 CDT"
	// marshalled:               "\x01\x00\x00\x00\x0f\x14\x96\x97X\x00\x00\x00\x00\xfe\xd4"
	// error:                    <nil>
	// unmarshalled (lost name): "2053-06-23 16:15:04 -0500 -0500"
}
```

### `Time.Hour`

```go
func ExampleTime_Hour() {
	// NOTE(jay): Compare to [time.Time.Clock]
	fmt.Printf("time: %q, hour: %d", stubTime(), stubTime().Hour())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", hour: 16
}
```

### `Time.ISOWeek`

```go
func ExampleTime_ISOWeek() {
	// NOTE(docs): Jan 01 to Jan 03 of year n might belong to week 52 or 53 of year n-1, and
	// Dec 29 to Dec 31 might belong to week 1 of year n+1.
	y, w := stubTime().ISOWeek()
	fmt.Printf("time: %q, year: %d, ISO week: %d", stubTime(), y, w)
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", year: 2053, ISO week: 26
}
```

### `Time.In`

```go
func ExampleTime_In() {
	// NOTE(docs): The methods [time.Time.Local], [time.Time.UTC], and [time.Time.In] return
	// a [time.Time] with a specific location. Changing the location in this way changes
	// only the presentation; it does not change the instant in time being denoted.

	t1 := stubTime()
	loc, _ := time.LoadLocation("Pacific/Yap")
	t2 := t1.In(loc)
	loc, _ = time.LoadLocation("Canada/Saskatchewan")
	t3 := t1.In(loc)
	fmt.Printf(`
time1: %q
time2: %q
time3: %q
equal: t1 and t2: %t, t2 and t3: %t
duration difference: t1 and t2: %s, t2 and t3: %s
t1 is before t2? %t, t2 is before t3? %t,
t1 is after t2?  %t, t2 is after t3?  %t,
`[1:],
		t1, t2, t3,
		t1.Equal(t2), t2.Equal(t3),
		t1.Sub(t2), t2.Sub(t3),
		t1.Before(t2), t2.Before(t3),
		t1.After(t2), t2.After(t3),
	)
	// Output:
	// time1: "2053-06-23 16:15:04 -0500 CDT"
	// time2: "2053-06-24 07:15:04 +1000 +10"
	// time3: "2053-06-23 15:15:04 -0600 CST"
	// equal: t1 and t2: true, t2 and t3: true
	// duration difference: t1 and t2: 0s, t2 and t3: 0s
	// t1 is before t2? false, t2 is before t3? false,
	// t1 is after t2?  false, t2 is after t3?  false,
}
```

### `Time.IsDST`

```go
func ExampleTime_IsDST() {
	t := stubTime()
	out := t.Add(-4 * 30 * 24 * time.Hour)
	fmt.Printf("time %q is in Daylight Savings Tims: %v\n", t, t.IsDST())
	fmt.Printf("time %q is in Daylight Savings Tims: %v\n", out, out.IsDST())
	// Output:
	// time "2053-06-23 16:15:04 -0500 CDT" is in Daylight Savings Tims: true
	// time "2053-02-23 15:15:04 -0600 CST" is in Daylight Savings Tims: false
}
```

### `Time.IsZero`

```go
func ExampleTime_IsZero() {
	var ptime *time.Time // == ptime := new(time.Time)
	var vstime time.Time // == vstime := time.Time{}
	t := time.Now()

	defer func() {
		if r := recover(); r != nil {
			// NOTE(jay): This matches what all the time package functions return [time.Time]
			// NOT [*time.Time]
			fmt.Printf("not safe to use as pointer: %v\n", r)
			fmt.Printf("var time is zero value: %t\ntime now is zero value: %t\n",
				vstime.IsZero(), t.IsZero())
		}
	}()

	ptime.IsZero()
	// Output:
	// not safe to use as pointer: runtime error: invalid memory address or nil pointer dereference
	// var time is zero value: true
	// time now is zero value: false
}
```

### `Time.Local`

```go
func ExampleTime_Local() {
	// NOTE(docs): The methods [time.Time.Local], [time.Time.UTC], and [time.Time.In] return
	// a [time.Time] with a specific location. Changing the location in this way changes
	// only the presentation; it does not change the instant in time being denoted and
	// therefore does not affect the computations described in earlier paragraphs.
	t1 := stubTime()
	local := t1.Local()
	fmt.Printf(`
time:  %q
local: %q
equal: %t
duration difference: %s
time is before local? %t
time is after  local? %t
`[1:],
		t1, local,
		t1.Equal(local),
		t1.Sub(local),
		t1.Before(local),
		t1.After(local),
	)
	// Output:
	// time:  "2053-06-23 16:15:04 -0500 CDT"
	// local: "2053-06-23 17:15:04 -0400 EDT"
	// equal: true
	// duration difference: 0s
	// time is before local? false
	// time is after  local? false
}
```

### `Time.Location`

```go
func ExampleTime_Location() {
	fmt.Printf("time: %q, location: %s\n", stubTime(), stubTime().Location())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", location: US/Central
}
```

### `Time.MarshalBinary`

```go
func ExampleTime_MarshalBinary() {
	// NOTE(jay): Loses information about Daylight Saving Time by removing the
	// [time.Time.Location]'s name.
	data, err := stubTime().MarshalBinary()
	back := &time.Time{}
	back.UnmarshalBinary(data)
	fmt.Printf(`
time:                     %q
marshalled:               %q
error:                    %v
unmarshalled (lost name): %q`[1:],
		stubTime(), data, err, back)
	// Output:
	// time:                     "2053-06-23 16:15:04 -0500 CDT"
	// marshalled:               "\x01\x00\x00\x00\x0f\x14\x96\x97X\x00\x00\x00\x00\xfe\xd4"
	// error:                    <nil>
	// unmarshalled (lost name): "2053-06-23 16:15:04 -0500 -0500"
}
```

### `Time.MarshalJSON`

```go
func ExampleTime_MarshalJSON() {
	// NOTE(jay): Loses information about Daylight Saving Time by removing the
	// [time.Time.Location]'s name.
	data, err := stubTime().MarshalJSON()
	back := &time.Time{}
	back.UnmarshalJSON(data)
	fmt.Printf(`
time:                     %q
marshalled:               %q
error:                    %v
unmarshalled (lost name): %q`[1:],
		stubTime(), data, err, back)
	// Output:
	// time:                     "2053-06-23 16:15:04 -0500 CDT"
	// marshalled:               "\"2053-06-23T16:15:04-05:00\""
	// error:                    <nil>
	// unmarshalled (lost name): "2053-06-23 16:15:04 -0500 -0500"
}
```

### `Time.MarshalText`

```go
func ExampleTime_MarshalText() {
	// NOTE(jay): Loses information about Daylight Saving Time by removing the
	// [time.Time.Location]'s name.
	data, err := stubTime().MarshalText()
	back := &time.Time{}
	back.UnmarshalText(data)
	fmt.Printf(`
time:                     %q
marshalled:               %q
error:                    %v
unmarshalled (lost name): %q`[1:],
		stubTime(), data, err, back)
	// Output:
	// time:                     "2053-06-23 16:15:04 -0500 CDT"
	// marshalled:               "2053-06-23T16:15:04-05:00"
	// error:                    <nil>
	// unmarshalled (lost name): "2053-06-23 16:15:04 -0500 -0500"
}
```

### `Time.Minute`

```go
func ExampleTime_Minute() {
	// NOTE(jay): Compare to [time.Time.Clock]
	fmt.Printf("time: %q, minutes: %d", stubTime(), stubTime().Minute())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", minutes: 15
}
```

### `Time.Month`

```go
func ExampleTime_Month() {
	// NOTE(jay): Compare with [time.Time.Date]
	m := stubTime().Month()
	fmt.Printf("time: %q, month: %s or %d", stubTime(), m, m)
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", month: June or 6
}
```

### `Time.Nanosecond`

```go
func ExampleTime_Nanosecond() {
	t, _ := time.Parse(time.RFC3339Nano, "2053-06-23T16:15:04.123456789-05:00")
	fmt.Printf("time: %q, nanoseconds: %d", t, t.Nanosecond())
	// Output:
	// time: "2053-06-23 16:15:04.123456789 -0500 -0500", nanoseconds: 123456789
}
```

### `Time.Round`

```go
func ExampleTime_Round() {
	// NOTE(docs): Round operates on the time as an absolute duration since the zero time;
	// it does not operate on the presentation form of the time. Thus, Round(Hour) may
	// return a time with a non-zero minute, depending on the time's Location.
	fmt.Println("round at hour:", stubTime().Round(time.Hour))
	fmt.Println("round at min: ", stubTime().Round(4*time.Minute))

	// XXX(jay): This will always fail because time moves forward, but the Output is still
	// good for that exact moment in time.
	now := time.Now()
	fmt.Printf("With monotonic clock:    %q\nwithout monotonic clock: %q",
		now, now.Round(0))

	// Output:
	// round at hour: 2053-06-23 16:00:00 -0500 CDT
	// round at min:  2053-06-23 16:16:00 -0500 CDT
	// With monotonic clock:    "2022-10-21 12:08:22.030193411 -0400 EDT m=+0.042259575"
	// without monotonic clock: "2022-10-21 12:08:22.030193411 -0400 EDT"
}
```

### `Time.Second`

```go
func ExampleTime_Second() {
	// NOTE(jay): Compare to [time.Time.Clock]
	fmt.Printf("time: %q, seconds: %d", stubTime(), stubTime().Second())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", seconds: 4
}
```

### `Time.String`

```go
func ExampleTime_String() {
	// NOTE(docs): The returned string is meant for debugging; for a stable serialized
	// representation, use t.MarshalText, t.MarshalBinary, or t.Format with an explicit
	// format string.

	// XXX(jay): This will always fail because time moves forward, but the Output is still
	// good for that exact moment in time.
	fmt.Printf("parsed time:\t\t\t%q\ntime with monotonic clock:\t%q\n",
		stubTime().String(), time.Now())
	// Output:
	// parsed time:			"2053-06-23 16:15:04 -0500 CDT"
	// time with monotonic clock:	"2022-10-22 15:58:18.885764818 -0400 EDT m=+0.005218664"
}
```

### `Time.Sub`

```go
func ExampleTime_Sub() {
	// NOTE(jay): Compare to [time.Time.Add] which produces a [time.Time]
	future := time.Date(2060, time.January, 1, 0, 0, 0, 0, time.UTC)
	d := stubTime().Sub(future)
	fmt.Printf("time:     %q\nsubtract: %q\nequals:   %s\n", stubTime(), future, d)
	// Output:
	// time:     "2053-06-23 16:15:04 -0500 CDT"
	// subtract: "2060-01-01 00:00:00 +0000 UTC"
	// equals:   -57170h44m56s
}
```

### `Time.Truncate`

```go
func ExampleTime_Truncate() {
	t := stubTime()
	for i := time.Duration(1); i < 100000; i++ {
		if t.Truncate(i*time.Hour).Minute() != 0 {
			fmt.Println(t.Truncate(i*time.Hour), i)
		}
		if t.Truncate(i*time.Minute).Second() != 0 {
			fmt.Println(t.Truncate(i*time.Minute), i)
		}
	}
	fmt.Println("trunc at hour:", stubTime().Truncate(time.Hour))
	fmt.Println("trunc at min: ", stubTime().Truncate(4*time.Minute))

	// XXX(jay): This will always fail because time moves forward, but the Output is still
	// good for that exact moment in time.
	now := time.Now()
	fmt.Printf("With monotonic clock:    %q\nwithout monotonic clock: %q",
		now, now.Truncate(0))

	// Output:
	// trunc at hour: 2053-06-23 16:00:00 -0500 CDT
	// trunc at min:  2053-06-23 16:12:00 -0500 CDT
	// With monotonic clock:    "2022-10-21 12:08:22.030193411 -0400 EDT m=+0.042259575"
	// without monotonic clock: "2022-10-21 12:08:22.030193411 -0400 EDT"
}
```

### `Time.UTC`

```go
func ExampleTime_UTC() {
	// NOTE(docs): The methods [time.Time.Local], [time.Time.UTC], and [time.Time.In] return
	// a [time.Time] with a specific location. Changing the location in this way changes
	// only the presentation; it does not change the instant in time being denoted and
	// therefore does not affect the computations described in earlier paragraphs.

	t1 := stubTime()
	utc := t1.UTC()
	fmt.Printf(`
time:  %q
UTC:   %q
equal: %t
duration difference: %s
time is before UTC? %t
time is after  UTC? %t
`[1:],
		t1, utc,
		t1.Equal(utc),
		t1.Sub(utc),
		t1.Before(utc),
		t1.After(utc),
	)
	// Output:
	// time:  "2053-06-23 16:15:04 -0500 CDT"
	// UTC:   "2053-06-23 21:15:04 +0000 UTC"
	// equal: true
	// duration difference: 0s
	// time is before UTC? false
	// time is after  UTC? false
}
```

### `Time.Unix`

```go
func ExampleTime_Unix() {
	// NOTE(jay): A Unix Timestamp is the amount of seconds that have elapsed since the Unix
	// epoch (January 1, 1970 UTC). It is a great tool to have when dealing with time
	// because **the timestamp does NOT change depending on your location in the world 🌐**
	// This means if you have an application that deals with multiple timezones a unix
	// timestamp is a pefect representations that standardizes across all timezones. Also
	// unlike a RFC3339 string, a Unix timestamp fits into 32 bytes for the next 4 billion
	// years.
	t := stubTime()
	fmt.Printf("time: %q, unix timestamp: %d\n", t, t.Unix())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", unix timestamp: 2634326104
}
```

### `Time.UnixMicro`

```go
func ExampleTime_UnixMicro() {
	t := stubTime().Add(123456 * time.Microsecond)
	fmt.Printf("time: %q, unix timestamp(µs): %d\n", t, t.UnixMicro())
	// Output:
	// time: "2053-06-23 16:15:04.123456 -0500 CDT", unix timestamp(µs): 2634326104123456
}
```

### `Time.UnixMilli`

```go
func ExampleTime_UnixMilli() {
	t := stubTime().Add(123 * time.Millisecond)
	fmt.Printf("time: %q, unix timestamp(ms): %d\n", t, t.UnixMilli())
	// Output:
	// time: "2053-06-23 16:15:04.123 -0500 CDT", unix timestamp(ms): 2634326104123
}
```

### `Time.UnixNano`

```go
func ExampleTime_UnixNano() {
	// NOTE(jay): Popular psuedo random seed
	// `rand.New(rand.NewSource(time.Now().UnixNano()))`
	t := stubTime().Add(123456789 * time.Nanosecond)
	fmt.Printf("time: %q, unix timestamp(ns): %d\n", t, t.UnixNano())
	// Output:
	// time: "2053-06-23 16:15:04.123456789 -0500 CDT", unix timestamp(ns): 2634326104123456789
}
```

### `Time.UnmarshalBinary`

```go
func ExampleTime_UnmarshalBinary() {
	// NOTE(jay): Unsafe for concurrent use.
	data, err := stubTime().MarshalBinary()
	back := &time.Time{}
	back.UnmarshalBinary(data)
	fmt.Printf(`
time:                     %q
marshalled:               %q
error:                    %v
unmarshalled (lost name): %q`[1:],
		stubTime(), data, err, back)
	// Output:
	// time:                     "2053-06-23 16:15:04 -0500 CDT"
	// marshalled:               "\x01\x00\x00\x00\x0f\x14\x96\x97X\x00\x00\x00\x00\xfe\xd4"
	// error:                    <nil>
	// unmarshalled (lost name): "2053-06-23 16:15:04 -0500 -0500"
}
```

### `Time.UnmarshalJSON`

```go
func ExampleTime_UnmarshalJSON() {
	// NOTE(jay): Unsafe for concurrent use.
	data, err := stubTime().MarshalJSON()
	back := &time.Time{}
	back.UnmarshalJSON(data)
	fmt.Printf(`
time:                     %q
marshalled:               %q
error:                    %v
unmarshalled (lost name): %q`[1:],
		stubTime(), data, err, back)
	// Output:
	// time:                     "2053-06-23 16:15:04 -0500 CDT"
	// marshalled:               "\"2053-06-23T16:15:04-05:00\""
	// error:                    <nil>
	// unmarshalled (lost name): "2053-06-23 16:15:04 -0500 -0500"
}
```

### `Time.UnmarshalText`

```go
func ExampleTime_UnmarshalText() {
	// NOTE(jay): Unsafe for concurrent use.
	data, err := stubTime().MarshalText()
	back := &time.Time{}
	back.UnmarshalText(data)
	fmt.Printf(`
time:                     %q
marshalled:               %q
error:                    %v
unmarshalled (lost name): %q`[1:],
		stubTime(), data, err, back)
	// Output:
	// time:                     "2053-06-23 16:15:04 -0500 CDT"
	// marshalled:               "2053-06-23T16:15:04-05:00"
	// error:                    <nil>
	// unmarshalled (lost name): "2053-06-23 16:15:04 -0500 -0500"
}
```

### `Time.Weekday`

```go
func ExampleTime_Weekday() {
	// NOTE(jay): Compare with [time.Time.Day] and [time.Time.YearDay]
	wd := stubTime().Weekday()
	fmt.Printf("%q is on a %s, day %d of a week\n",
		stubTime().Format("Jan 02"), wd, wd+1)
	// Output:
	// "Jun 23" is on a Monday, day 2 of a week
}
```

### `Time.Year`

```go
func ExampleTime_Year() {
	// NOTE(jay): Compare with [time.Time.Date]
	fmt.Printf("time: %q, year: %d", stubTime(), stubTime().Year())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", year: 2053
}
```

### `Time.YearDay`

```go
func ExampleTime_YearDay() {
	// NOTE(jay): Compare with [time.Time.Day] and [time.Time.Weekday]
	fmt.Printf("time: %q, year day: %d", stubTime(), stubTime().YearDay())
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT", year day: 174
}
```

### `Time.Zone`

```go
func ExampleTime_Zone() {
	n, o := stubTime().Zone()
	fmt.Printf("time: %q\nabbreviated name of TZ: %s; offset in seconds east of UTC: %d\n",
		stubTime(), n, o)
	// Output:
	// time: "2053-06-23 16:15:04 -0500 CDT"
	// abbreviated name of TZ: CDT; offset in seconds east of UTC: -18000
}
```

### `Time.ZoneBounds`

```go
func ExampleTime_ZoneBounds() {
	t := time.Unix(1221681866, 0).UTC() // UTC isn't part of a zone.
	s, e := t.ZoneBounds()
	fmt.Printf("time:\t%v\nstart:\t%v\nend:\t%v\n", t, s, e)
	fmt.Println()

	t = stubTime()
	s, e = t.ZoneBounds()
	fmt.Printf("time:\t%v\nstart:\t%v\nend:\t%v\n", t, s, e)
	// Output:
	// time:	2008-09-17 20:04:26 +0000 UTC
	// start:	0001-01-01 00:00:00 +0000 UTC
	// end:	0001-01-01 00:00:00 +0000 UTC
	//
	// time:	2053-06-23 16:15:04 -0500 CDT
	// start:	2053-03-09 03:00:00 -0500 CDT
	// end:	2053-11-02 01:00:00 -0600 CST
}
```

### `AfterFunc`

```go
func ExampleAfterFunc() {
	longTimer := time.AfterFunc(5*time.Minute, func() {
		fmt.Println("This is never going to run")
	})

	time.AfterFunc(20*time.Millisecond, func() {
		fmt.Println("1. I was first but fire second(2)!")
	})

	time.AfterFunc(70*time.Millisecond, func() {
		fmt.Println("2. I was second but fire fourth(4).")
	})

	time.AfterFunc(40*time.Millisecond, func() {
		fmt.Println("3. I was third and fire third(3).")
	})

	time.AfterFunc(5*time.Millisecond, func() {
		fmt.Println("4. I was last but fire first(1)!")
	})
	time.Sleep(100 * time.Millisecond)
	longTimer.Stop()
	// Output:
	// 4. I was last but fire first(1)!
	// 1. I was first but fire second(2)!
	// 3. I was third and fire third(3).
	// 2. I was second but fire fourth(4).
}
```

### `AfterFunc_reset`

```go
func ExampleAfterFunc_reset() {
	// NOTE(docs): For a Timer created with AfterFunc(d, f), Reset either reschedules when f
	// will run, in which case Reset returns true, or schedules f to run again, in which
	// case it returns false. When Reset returns false, Reset neither waits for the prior f
	// to complete before returning nor does it guarantee that the subsequent goroutine
	// running f does not run concurrently with the prior one. If the caller needs to know
	// whether the prior execution of f is completed, it must coordinate with f explicitly.
	ngo := 0
	results := make(chan string, 4)
	t := time.AfterFunc(100*time.Minute, func() {
		ngo++
		ngo := ngo // some _sussy_ shadowing
		results <- "do some work in here " + strconv.Itoa(ngo)
		time.Sleep(10 * time.Millisecond)
		results <- "finish some work over there " + strconv.Itoa(ngo)
	})
	didReset := func(b bool) {
		switch b {
		case true:
			fmt.Println("Reset() successfully rescheduled when AfterFunc() will run")
		case false:
			fmt.Println("Reset() did NOT reschedule when AfterFunc() will run, the goroutine",
				"was already running or has finished running.")
		}
	}
	fmt.Println("Reset() does NOT wait for previous goroutine to finish:")
	didReset(t.Reset(100 * time.Millisecond))
	fmt.Println(<-results) // First goroutine starts
	didReset(t.Reset(time.Nanosecond))
	fmt.Println(<-results) // Second goroutine starts
	fmt.Println(<-results) // First goroutine ends
	fmt.Println(<-results) // Second goroutine ends
	// Unordered output:
	// Reset() does NOT wait for previous goroutine to finish:
	// Reset() successfully rescheduled when AfterFunc() will run
	// do some work in here 1
	// Reset() did NOT reschedule when AfterFunc() will run, the goroutine was already running or has finished running.
	// do some work in here 2
	// finish some work over there 2
	// finish some work over there 1
}
```

### `AfterFunc_stop`

```go
func ExampleAfterFunc_stop() {
	// NOTE(docs): For a timer created with AfterFunc(d, f), if t.Stop returns false, then
	// the timer has already expired and the function f has been started in its own
	// goroutine; Stop does not wait for f to complete before returning. If the caller needs
	// to know whether f is completed, it must coordinate with f explicitly.
	t := time.AfterFunc(time.Nanosecond, func() {
		fmt.Println("running")
		time.Sleep(10 * time.Millisecond)
		fmt.Println("finished") // Notice this is last
	})
	time.Sleep(time.Microsecond)
	if !t.Stop() {
		// Notice the return value didn't wait for AfterFunc() to finish.
		fmt.Println("Stop() did not stop the goroutine from starting")
		// XXX(jay): This will block forever!!!!
		// <-t.C
		//
		// When AfterFunc() makes a [time.Timer] it never initializes [time.Timer.C].
	}
	time.Sleep(11 * time.Millisecond)
	// Unordered output:
	// running
	// Stop() did not stop the goroutine from starting
	// finished
}
```

### `NewTimer`

```go
func ExampleNewTimer() {
	// NOTE(jay): A timer is really only worth it's channel firing (which is a good thing).
	// The API is very straight forward, you either let the timer fire, stop it from firing,
	// or reset it (to a possibly new duration). Any time you're looking to add a:
	// 	"maximum amount of time before I give up." thing.
	// [time.Timer] is perfect.
	longRunningProcess := time.NewTimer(150 * time.Millisecond)
	stopIfTakingTooLong := time.NewTimer(100 * time.Millisecond)
	select {
	case <-longRunningProcess.C:
		fmt.Println("Finished the long running process in time to give results.")
	case <-stopIfTakingTooLong.C:
		fmt.Println("Abort the long running process it's stalled and not worth it.")
		// Cleanup resources
		longRunningProcess.Stop()
	}
	// Output:
	// Abort the long running process it's stalled and not worth it.
}
```

### `Timer.Reset`

```go
func ExampleTimer_Reset() {
	// NOTE(jay): Compare with [Ticker.Reset]
	t := time.Now()
	tkr := time.NewTicker(50 * time.Millisecond)
	tmr := time.NewTimer(51 * time.Millisecond)
	noReset := time.NewTimer(51 * time.Millisecond)
	for i := 0; i < 4; i++ {
		select {
		case <-tkr.C:
			if i == 2 {
				tkr.Stop()
			}
			// NOTE(docs): For a Timer created with NewTimer, Reset should be invoked only on
			// stopped or expired timers with drained channels.
			// This should not be done concurrent to other receives from the Timer's
			// channel.
			//
			if !tmr.Stop() {
				<-tmr.C
			}
			// NOTE(docs): It is not possible to use Reset's return value correctly, as there is
			// a race condition between draining the channel and the new timer expiring. The
			// return value exists to preserve compatibility with existing programs.
			fmt.Println("timer was reset?", tmr.Reset(51*time.Millisecond))
		case <-tmr.C:
			fmt.Printf("the total time before reaching the reset timer: %s\n",
				time.Since(t).Truncate(151*time.Millisecond))
		case <-noReset.C:
			fmt.Println("we make it into here on attempt:", i+1,
				"because the noReset timer was still going")
		}
	}
	// Output:
	// timer was reset? false
	// we make it into here on attempt: 2 because the noReset timer was still going
	// timer was reset? false
	// the total time before reaching the reset timer: 151ms
}
```

### `Timer.Stop`

```go
func ExampleTimer_Stop() {
	// NOTE(jay): Compare with [Ticker.Stop]
	tmr := time.NewTimer(1 * time.Hour)
	tmr.Stop() // Not good enough to just do this.
	// XXX(jay): This will block forever!!! Stop() does NOT close the channel, you can NOT
	// expect any zero value coming from the channel.
	// <-tmr.C

	// NOTE(jay): It's better to drain the channel to release resources (if fired).
	tmr = time.NewTimer(time.Nanosecond)
	time.Sleep(time.Microsecond)
	if wasStopped := tmr.Stop(); !wasStopped {
		fmt.Printf(`the call to Stop() did not stop the timer: %t
and because of that we need to drain the channel.`,
			!wasStopped)
		// NOTE(jay): Always drain the channel to free resources and if you Reset() it you
		// won't receive the incorrect previous [time.Time] from the channel, or worse,
		// possibly deadlocking.
		<-tmr.C
	}
	// Output:
	// the call to Stop() did not stop the timer: true
	// and because of that we need to drain the channel.
}
```

### `Weekday.String`

```go
func ExampleWeekday_String() {
	fmt.Println("Days of the week:")
	for i := time.Weekday(0); i <= time.Saturday; i++ {
		fmt.Println(i)
	}
	// Output:
	// Days of the week:
	// Sunday
	// Monday
	// Tuesday
	// Wednesday
	// Thursday
	// Friday
	// Saturday
}
```
