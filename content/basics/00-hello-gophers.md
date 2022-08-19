---
Lesson: 0
Title: "Hello Gophers"
Draft: false
---

## Install Go

Before we can begin writing Go code we'll need to, would you believe it?
[Install Go](https://go.dev/doc/install)

## Install Text Editor

If you already have a text editor of choice, then you can skip this step. If
you don't know what a text editor is, then let me explain.

### Software Engineers are modern day wizards.

{{< figure src="/maria-letta/goroutines/14.svg" alt="Goroutines 14"
attr=`[goroutines-14](https://github.com/MariaLetta/free-gophers-pack/blob/master/goroutines/svg/14.svg) by
[Maria Letta](https://instagram.com/maria_letta_art)-
[License](https://github.com/MariaLetta/free-gophers-pack/blob/master/LICENSE)`
width=720px >}}

How so?
- We can make updates **anywhere** in the world 🌍 to a piece of software and
  **everywhere** in the world 🌎 🌏 is affected.
- We can bend reality to our will by making software that can change physics,
  images 🌌 , voices 👹💬, appearances 😈 -> 😊
- We can summon, literally, tens of thousands of computers 🤖🤖🤖🤖🤖🤖 to
  serve us in a moment's notice.

OK, so programmers are wizards🧙‍♂️  🧙, what's the point? Well, like
anyone who practices the magic arts🧝🧚, you need a tool. Instead of using a
wand 🪄 developers use text editors or IDEs (Integrated Development
Environment). Text editors allow us to use our powers.

### Time to choose your Text Editor

#### 1. [VSCode](https://code.visualstudio.com/)

{{< figure src="/ashley-mcnamara/VS CODE GOPHER.png" alt="VSCode Gopher"
attr=`[VS Code Gopher](https://github.com/ashleymcnamara/gophers/blob/master/VS%20CODE%20GOPHER.png)
by [Ashley McNamara](https://twitter.com/ashleymcnamara)-
[License](https://creativecommons.org/licenses/by/4.0/)`
width=240px >}}

#### 2. [Vim](https://www.vim.org/download.php)

{{< figure src="/egon-elbre/vim-go.svg" alt="Vim Go" attr=`[Vim
Go](https://github.com/egonelbre/gophers/blob/master/vector/projects/vim-go.svg) by
[Egon Elbre](https://twitter.com/egonelbre/)-
[License](https://creativecommons.org/publicdomain/zero/1.0/)` width=240px >}}

I use **[Neovim](https://github.com/neovim/neovim/wiki/Installing-Neovim)** but if you're a beginner, I _highly_ recommend **VSCode**.
**Vim** is a very, _very_ powerful 🦸‍♀️ text editor, but it is **not**
beginner friendly. If you know what you're doing and want to improve your speed
and understanding as a developer give **Neovim** or **Vim** a try; Here's an
article to get started with [Go in
Vim](https://farazdagi.com/posts/2015-01-10-vim-as-golang-ide/) or with
**VSCode** you want to search for the [Go
extension](https://code.visualstudio.com/docs/languages/go) and install it.

## Setup

We have **Go** installed and we have our text editor, now it's time to start
our first project❗ Are you excited⁉️ I am 😁 From this point on I'll assume
we're beginners and using **VSCode**. Open **VSCode** and hit Ctrl+\` (That's
the backtick or the key right next to `1` on a QWERTY keyboard) to open the
[Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) and
type the following in the terminal:

1. `mkdir basics` -- make a directory and name it basics (Our project name)
1. `cd basics` -- change directory into our project basics
1. `go mod init basics` -- initialize (start) a go module named basics
1. `mkdir cmd` -- make a directory and name it cmd (Idiomatic to do in Go)
1. `touch cmd/main.go` -- make a file named main.go in the cmd directory
1. `code -r .` -- make VSCode update the working directory to our project

{{< asciinema "asciicast-la4abLSpvMffT6OWtG2Ublapj"
  "https://asciinema.org/a/la4abLSpvMffT6OWtG2Ublapj.js" >}}

Now you should see a little directory named `cmd` on the left. Open it
and click `main.go`

## First Program

Now time to write your first `Go` code.

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}
```

Lets explain each line.

### `package main`

`Go` needs you to tell it where it should pack all of the functionality you're
going to make in this file. You can have something like `package hello` at the
top and have many files that have `package hello` at the top of their files as
well. When `Go` builds it that entire package gets... Well, packaged together
😂 Make sense? In other words grouped or bundled together. Think of noodles 🍜
being put into a take out box 🥡 The files we make are the noodles 🍜 and the
`package` we declare is the take out box 🥡 

`package main` is special because this is where `Go` will begin execution of
your program. If you're trying to run a `Go` app you **must** have a `package
main` with a `func main()`.

### `import "fmt"`

`fmt` is a package 📦! Just like the `package main` we made one line above, but
it comes from the standard library 📚. The standard library is a place where
lots of amazing packages exists, all we have to do is `import` them! Later
we'll make our own packages and we can run them here in `cmd/main`.

### `func main()`

`func main()` is the place where it all begins and it all ends 🏁, your
application that is. Here is where you will start and end anything and
everything you want your program to do. So always remember if you want to `go
run` your application, it needs to be in `package main` and it must be inside
of the `func main()` function.

### `fmt.Println( ... )`

Here we _call_ the `fmt` package that we imported to use it's `Println`
function. `Println` is short for print 🖨️line. It's called that because it will
print a line of text to your terminal, also known as standard out. You can pass
lots of things to `Println`, but we'll see more of that in later lessons.

## Run your very first program

Save the file and type `go run cmd/main.go` into your terminal. [Playground
link](https://goplay.tools)

### 🥳🥳 Congratulations! 🎉🎊🎉 You're officially a Gopher!

![https://twitter.com/egonelbre](/egon-elbre/gopher-dance-long-3x.gif "gopher-dance-long-3x by Egon Elbre")

Why not celebrate and **[Make Your Own Gopher](https://gopherize.me/)** and if
you want there's a very supportive community over on
**[Slack](https://invite.slack.golangbridge.org/)** and
**[Reddit](https://www.reddit.com/r/golang/)** and finally **[Golang
Weekly](https://golangweekly.com/)** 📰 is great for getting ideas about what's
going on in the Go community.

## Problems?

It's very important that you're in the `basics` directory when you run `go run
cmd/main.go`. If you get an error message like: `stat main.go: no such file or
directory` that means you tried to execute something that wasn't there! `go` is
telling you,

> I looked for the file `main.go` where you asked me to look and it's not
> there! So it looks like I can't run it.

So make sure you're in the the `basics` directory, you can find out by
typing `pwd` in your terminal and pressing `Enter/Return`

If you're still facing problems send an email to `info@gophergo.dev`
