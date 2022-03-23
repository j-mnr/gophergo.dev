---
title: "Hello Gophers"
date: 2022-03-21T20:25:47-04:00
draft: true
lesson: 0
---

## Install Go

Before we can begin writing Go code we'll need to, would you believe it? [Install Go](https://go.dev/doc/install)

## Install Text Editor

If you already have a text editor of choice, then you can skip this step. If
you don't know what a text editor is, then let me explain.

Software Engineers are modern day wizards, witches, and warlocks. How so? We
can make updates **anywhere** in the world to a codebase and **everywhere** in
the world is affected. We can bend reality to our will by making software that
can change physics, images, voices, appearances. We can summon, literally, tens
of thousands of computers to serve us in a moment's notice.

OK, so programmers are wizards, what's the point? Well, like anyone who
practices the magic arts, you need a tool. Instead of using a wand, developers
use text editors or IDEs (Integrated Development Environment). Text editors
allow us to use our powers.

So now it's time to choose your Text Editor, I've ranked them by popularity:

1. [VSCode](https://code.visualstudio.com/) (Free / Easy)
1. [Neovim](https://github.com/neovim/neovim/wiki/Installing-Neovim) (Free /
   Hard)
1. [Vim](https://www.vim.org/download.php) (Free / Hard)
1. [GoLand](https://www.jetbrains.com/go/) (Pay / Medium)
1. [Sublime Text](https://www.sublimetext.com/download) (Pay / ???)
1. [Atom](https://flight-manual.atom.io/getting-started/sections/installing-atom/#platform-windows) (Free / Medium)
1. [Emacs](https://www.gnu.org/software/emacs/download.html) (Free / Hard)
1. [Notepad++](https://notepad-plus-plus.org/downloads/) (Free / Easy / Windows)

I use **Neovim** but if you're a beginner, I **highly** recommend **VSCode**.
**Vim** is a very, _very_ powerful text editor (I once updated 20 lines of 50
files, 1000 lines of code, with a single vim command), but it is **not**
beginner friendly. If you know what you're doing and want to improve your speed
and understanding as a developer give **Neovim** or **Vim** a try; with
**VSCode** you want to search for the [Go
extension](https://code.visualstudio.com/docs/languages/go) and install it;
**Goland** already has **Go** things installed.

## Setup

We have **Go** installed and we have a text editor, now it's time to start our
first project❗ Are you excited⁉️ I am 😁 From this point on I'll assume we're
using **VSCode**. Open **VSCode** and hit Ctrl+\` to open the
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

Now you should see a little directory named `cmd` on the left open it
and click `main.go`

Now time to write your first `Go` code.

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}
```

Lets it explain each line.

### package main

`Go` needs you to tell it where it should pack all of the functionality you're
going to make in this file. You can have something like `package hello` at the
top and have many files that have `package hello` at the top of their files as
well. When `Go` builds it that entire package gets... Well, packaged together
😂 Make sense? In other words grouped or bundled together.

`package main` is special because this is where `Go` will begin execution of
your program. If you're trying to run a `Go` app you **must** have a `package
main` with a `func main()`.

### import "fmt"

`fmt` is a package! Just like the `package main` we made one line above, but
we don't want to have to write _everything_, so we can import others amazing
code, and amazing the `fmt` package is. It's a very common package that's
imported in many, many `Go` programs. Later we'll make our own packages and we
can run them here in `cmd/main`.

### func main() {...}

`func main()` is the place where it all begins and it all ends, your
application that is. Here is where you will start and end anything and
everything you want your program to do. So always remember if you want to `go
run` your application, it needs to be in `package main` and it must be inside
of the `func main()` function.

### fmt.Println(...)

Here we _call_ the `fmt` package that we imported to use it's `Println`
function. `Println` is short for print line. It's called that because it will
print a line of text to your terminal, also known as standard out. You can pass
lots of things to `Println`, but we'll see more of that in later lessons.

## Run it!

Save the file and type `go run cmd/main.go` in your terminal.

and 🥳🥳🎉🎊🎉 You're officially a Gopher!

![https://twitter.com/egonelbre](/gopher-dance-long-3x.gif)

Why not celebrate and [Make You're Own Gopher](https://gopherize.me/) and if
you want there's a very supportive community over on
[Slack](https://invite.slack.golangbridge.org/)

All that said and done. Let's head on over to the next lesson!

[Types]({{< relref "types" >}} "That's My Type!")

## Troubleshooting

It's very important that you're in the `basics` directory when you do this. If
you get an error message like: `stat main.go: no such file or directory` that
means you tried to execute something that wasn't there! `go` is telling you,

> I looked for the file `main.go` where you asked me to look and it's not
> there! So it looks like I can't run it.

So make sure you're in the correct directory.
