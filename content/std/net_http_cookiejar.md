---
Title: "cookiejar"
Draft: false
URL: /std/net/http/cookiejar/
---

## Source Code

🛝 [Playground link](https://goplay.tools/snippet/Nw1bWGV_h9X)

## Other packages used

- [`fmt`]()
- [`net/http`]()
- [`net/url`]()
- [`strings`]()
- [`time`](/std/time/)
- [`golang.org/x/net/publicsuffix`]()

## Constants and Variables

🤷 None to be found!

## Examples

### New

```go
func ExampleNew() {
	// NOTE(jay): The only thing that [cookiejar.New] takes is a [*cookiejar.Options] which
	// only has one exported field [cookiejar.Options.PublicSuffixList]. This means the only
	// real options to **currently** supply are `nil` or a [*cookiejar.Options] with a
	// PublicSuffixList. All values stay the same except for the [*cookiejar.Jar.psList] in
	// the Output.
	//
	// It is impossible for [cookiejar.New] to return an error.
	jar, _ := cookiejar.New(nil)
	fmt.Printf("jar: %#v\n\n", jar)

	jar, _ = cookiejar.New(&cookiejar.Options{})
	fmt.Printf("jar: %#v\n\n", jar)

	jar, _ = cookiejar.New(&cookiejar.Options{PublicSuffixList: examplePSL{}})
	fmt.Printf("jar: %#v\n\n", jar)

	jar, _ = cookiejar.New(&cookiejar.Options{PublicSuffixList: publicsuffix.List})
	fmt.Printf("jar: %#v\n", jar)
	// Output:
	// jar: &cookiejar.Jar{psList:cookiejar.PublicSuffixList(nil), mu:sync.Mutex{state:0, sema:0x0}, entries:map[string]map[string]cookiejar.entry{}, nextSeqNum:0x0}
	//
	// jar: &cookiejar.Jar{psList:cookiejar.PublicSuffixList(nil), mu:sync.Mutex{state:0, sema:0x0}, entries:map[string]map[string]cookiejar.entry{}, nextSeqNum:0x0}
	//
	// jar: &cookiejar.Jar{psList:main.examplePSL{}, mu:sync.Mutex{state:0, sema:0x0}, entries:map[string]map[string]cookiejar.entry{}, nextSeqNum:0x0}
	//
	// jar: &cookiejar.Jar{psList:publicsuffix.list{}, mu:sync.Mutex{state:0, sema:0x0}, entries:map[string]map[string]cookiejar.entry{}, nextSeqNum:0x0}
}
```

### Jar_Cookies

```go
func ExampleJar_Cookies() {
	jar, _ := cookiejar.New(nil)
	u, _ := url.Parse("http://example.com/path/cookie/belongs")
	jar.SetCookies(u, []*http.Cookie{
		{
			Name:  "Cookie-Name",
			Value: "Cookie-Value",

			// NOTE(jay): will NOT persist when grabbing Cookies from jar.
			Path:     "/path/cookie/belongs", // will be ""
			Secure:   true,                   // will be false
			HttpOnly: true,                   // will be false
		},
		{
			// NOTE(jay): Same [*http.Cookie] will be ignored; not in Output
			Name:  "Cookie-Name",
			Value: "Cookie-Value",
			Path:  "/path/cookie/belongs",
		},
		{
			// NOTE(jay): not in Output
			Name:  "Bad-Path",
			Value: "does-not-work",
			Path:  "/where/is/this",
		},
		{
			Name:  "_csrf",
			Value: "apodijf901112j00000fqwe",

			// NOTE(jay): will NOT persist when grabbing Cookies from jar.
			Path:   "/path/cookie/belongs", // will be ""
			Domain: "example.com",          // will be ""

			// NOTE(jay): MaxAge takes prescedence over Expires.
			Expires: time.Now().Add(-8 * time.Hour),
			MaxAge:  15 * int(time.Minute.Seconds()),

			// NOTE(jay): This SHOULD be rejected.
			// According to
			// https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite:
			//
			// 	Cookies with SameSite=None must now also specify the Secure attribute (they
			// 	require a secure context/HTTPS).
			SameSite: http.SameSiteNoneMode,
		},
	})
	fmt.Printf("all zero values besides Name and Value: %#v\n\n", jar.Cookies(u)[1])
	fmt.Printf("cookies: %v\n", jar.Cookies(u))
	// Output:
	// all zero values besides Name and Value: &http.Cookie{Name:"_csrf", Value:"apodijf901112j00000fqwe", Path:"", Domain:"", Expires:time.Date(1, time.January, 1, 0, 0, 0, 0, time.UTC), RawExpires:"", MaxAge:0, Secure:false, HttpOnly:false, SameSite:0, Raw:"", Unparsed:[]string(nil)}
	//
	// cookies: [Cookie-Name=Cookie-Value _csrf=apodijf901112j00000fqwe]
}
```

### Jar_SetCookies

```go
func ExampleJar_SetCookies() {
	jar, _ := cookiejar.New(nil)
	u, _ := url.Parse("https://gophergo.dev")
	jar.SetCookies(u, []*http.Cookie{
		{
			Name:  "sessionId",
			Value: "91nsr7fn8aX",
			// With no `Max-Age` or `Expires` Attribute the [*http.Cookie] expires when the user
			// agent closes the session/shuts down.
		},
		{
			// NOTE(jay): Notice this isn't in the Output
			Name:   "_csrf",
			Value:  "74563212d814be0c771e103080542b4451b3Bs%3A5%%3Bs%3A32%3A%22btpkm46%22%3B%7D",
			MaxAge: -1, // Means delete this cookie now!
		},
		{
			Name:  "__Secure-Token", // Special `__Secure` Name only available on HTTPS
			Value: "b89Klee13oiVv",

			Domain: "gophergo.dev",

			MaxAge: 24 * int(time.Hour.Seconds()), // In amount of seconds == 86400

			Secure:   true, // Must set Secure with `__Secure`... Duh! 😜
			SameSite: http.SameSiteLaxMode,
		},
		{
			Name:  "__Host-Id",
			Value: "a38afesWfa7b4",

			Path:   "/", // Must be "/" for special `__Host` Name
			Domain: "",  // Cannot be set with special `__Host` Name

			Expires: time.Now().Add(2592000 * time.Second), //  One month

			Secure:   true, // Must be true for special `__Host` Name
			HttpOnly: true,
			SameSite: http.SameSiteStrictMode,
		},
	})
	fmt.Printf("Cookies for %s: %v\n", u, jar.Cookies(u))
	// Output:
	// Cookies for https://gophergo.dev: [sessionId=91nsr7fn8aX __Secure-Token=b89Klee13oiVv __Host-Id=a38afesWfa7b4]
}
```

### Options

```go
func ExampleOptions() {
	fmt.Printf("%#v\n", cookiejar.Options{PublicSuffixList: greatForTestingButINSECURE{}})
	// Output: cookiejar.Options{PublicSuffixList:main.greatForTestingButINSECURE{}}
}
```

### PublicSuffixList

```go
// greatForTestingButINSECURE is useful for testing an implementation, but is inherently
// insecure, from the docs:
//
//	An implementation that always returns "" is valid and may be useful for testing but
//	it is not secure: it means that the HTTP server for foo.com can set a cookie for
//	bar.com.
type greatForTestingButINSECURE struct{}

func (greatForTestingButINSECURE) PublicSuffix(string) string {
	return ""
}

func (greatForTestingButINSECURE) String() string {
	return ""
}

type examplePSL struct{}

// PublicSuffix is great for grasping the concept of what to return given a domain, but it
// is not something to copy and paste and use. It's better to use
// [publicsuffix.PublicSuffix] if you want a correct and solid implementation. Another
// name for a public suffix is the effective Top Level Domain (eTLD) as some sites (like
// co.uk) have more than one ending to the entire TLD. You may also find
// [publicsuffix.EffectiveTLDPlusOne] intriguing.
func (l examplePSL) PublicSuffix(domain string) string {
	// domain can look like "www.blog.gophergo.dev"
	return domain[1+strings.LastIndex(domain, "."):]
}

func (l examplePSL) String() string {
	return "v0.1.2b 2006-01-02T15:04:05Z"
}
```

```go
func ExamplePublicSuffixList() {
	psl := examplePSL{}
	var _ cookiejar.PublicSuffixList = greatForTestingButINSECURE{}
	var _ cookiejar.PublicSuffixList = publicsuffix.List
	var _ cookiejar.PublicSuffixList = psl

	etld := func(domain string) {
		fmt.Printf("naive public suffix: %q\n", psl.PublicSuffix(domain))
		fmt.Printf("good public suffix:  %q\n", publicsuffix.List.PublicSuffix(domain))
	}
	etld("www.blog.gophergo.dev")
	etld("videos.gophers.com")
	etld("space.gogo.xyz")
	etld("tea.gostore.co.uk") // naive approach fails

	fmt.Printf("\n%s", psl)
	// Output:
	// naive public suffix: "dev"
	// good public suffix:  "dev"
	// naive public suffix: "com"
	// good public suffix:  "com"
	// naive public suffix: "xyz"
	// good public suffix:  "xyz"
	// naive public suffix: "uk"
	// good public suffix:  "co.uk"
	//
	// v0.1.2b 2006-01-02T15:04:05Z
}
```
