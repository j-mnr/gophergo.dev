{{ $lessonname := .Get "lessonname" }}
## Setup

Let's make our directory `{{ $lessonname }}` and the files we want inside of
that directory `example_test.go` `{{ $lessonname }}.go`

```sh
mkdir {{ $lessonname }}
touch {{ $lessonname }}/example_test.go {{ $lessonname }}/{{ $lessonname }}.go
```

Now let's open up `{{ $lessonname }}.go` and for the very first line we'll add
```go
package {{ $lessonname }}
```


Next for `example_test.go` for the very first line we'll add
```go
package {{ $lessonname }}_test
```

We can `import basics/{{ $lessonname }}` into `cmd/main.go` and run functions
from there with `go run cmd/main.go` and also to run our `example_test.go` 👍
we use `go test` in the commandline.
