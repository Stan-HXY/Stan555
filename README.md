# Learn Go in ~5mins

This is inspired by [A half-hour to learn Rust](https://fasterthanli.me/articles/a-half-hour-to-learn-rust)
and [Zig in 30 minutes](https://gist.github.com/ityonemo/769532c2017ed9143f3571e5ac104e50).

## Basics

Your first Go program as a classical "Hello World" is pretty simple:

First we create a workspace for our project:

```console
$ mkdir hello
$ cd hello
```

Next we create and initialize a Go module:

```console
$ go mod init hello
```

Then we write some code using our favorite editor in a file called `main.go` in the directory `hello` you created above:

```go
package main

import "fmt"

func main() {
  fmt.Println("Hello World!")
}
```

And finally we build and produce a binary:

```console
$ go build
```

You should now have a `hello` binary in your workspace, if you run it you should also get the output:

```console
$ ./hello
Hello World!
```

## Variables

You can create variables in Go in one of two ways:

```go
var x int
```

Other types include `int`, `int32`, `int64`, `float32`, `float64`, `bool`
and `string` (_and a few others..._), there are also unsigned variants of the
integer types prefixed with `u`, e.g: `uint8` which is the same as a `byte`.

Or implicitly with inferred types by creating and assigning a value with:

```go
x := 42
```

Values are assigned by using the `=` operator:

```go
x = 1
```

**NOTE:** Most of the time you don't need to use the `var` keyword _unless_
          you are creating a variable and assigning it a value later or want
          a zero or `nil` value for some reason (_like a `nil` map).

## Functions

Functions are declared with the `func` keyword:

```go
func hello(name string) string {
  return fmt.Sprintf("Hello %s", name)
}
```

Functions with a return type **must** explicitly return a value.

Functions can return more than one value (_commonly used to return errors and values_):

```go
func isEven(n int) (bool, error) {
  if n <= 0 {
    return false, fmt.Errorf("error: n must be > 0")
  }
  return n % 2 == 0, nil
}
```

Go also supports functions as first-class citizens and as such supports many
aspects of functional programming, including closures, returning  functions
and passing functions around as values. For example:

```go
func AddN(n int) func(x int) int {
  return func(x int) int {
    return x + n
  }
}
```

## Structs

As Go is a multi-paradigm language, it also support "object orientated"
programming by way of "structs" (_borrowed from C_). Objects / Structs are
defined with the `struct` keyword:

```go
type Account struct {
  Id      int
  Balance float64
}
```

Fields are defined similar to variables and are accessed with the dot-operator `.`:

```go
account := Account{}
fmt.Printf("Balance: $%0.2f", account.Balance)
```

## Methods

Structs (_objects_) can also have methods. Unlike other languages however Go
does not support multiple-inheritance nor does it have classes
(_you can however embed structs into other structs_).

Methods are created like functions but take a "receiver" as the first argument:

```go
type Account struct {
  id  int
  bal float64
}

func (a *Account) String() string {
  return fmt.Sprintf("Account[%d]: $0.2f", a.id, a.bal)
}

func (a *Account) Deposit(amt flaot64) float64 {
  a.bal += amt
  return a.bal
}

func (a *Account) Withdraw(amt float64) float64 {
  a.bal -= amt
  return a.bal
}

func (a *Account) Balance() float64 {
  return a.bal
}
```

These are called "pointer receiver" methods because the first argument is a
pointer to a struct of type `Account` denoted by `a *Account`.

You can also define methods on a struct like this:

```go
type Circle struct {
  Radius float64
}

func (c Circle) Area() float64 {
  return 3.14 * c.Radius * c.Radius
}
```

In this case methods cannot modify any part of the struct `Circle`,
they can only read it's fields. They are effectively "immutable".

## Arrays and Slices

Like other langauges Go has Arrays, but unlike other languages Go's arrays are
more similar to C where they are of fixed size. You create a fixed sized array
by specifying it's size and type like this:

```go
xs := [4]int{1, 2, 3, 4}
```

Most of the time however you will deal with Slices, which behave more like
lists in other languages like Python where they are resized automatically.

Sliaces are created by omitting the size:

```go
xs := []int{1, 2, 3, 4}
```

Slices can also be created and appended to:

```go
xs := []int{}
xs = append(xs, 1)
xs = append(xs, 2)
xs = append(xs, 3)
```

You can access an slice's elements by indexing:

```go
xs[1]  // 2
```

You can also access a subset of an array or slice by "slicing" it:

```go
ys := xs[1:] // [2, 3]
```

You can iterate over an array/slice by using the `range` keyword:

```go
for i, x := range xs {
  fmt.Printf("xs[%d] = %d\n", i, x)
}
```

## Maps

Go has a builtin data structure for storing key/value pairs called maps
(_called hash table, hash map, dictionary or associative array in other languages_).

You create a map by using the keyword `map` and defining a type for keys
and type for  values `map[Tk]Tv`, for example a map with keys as strings
and values as integers can be defined as:

```go
var counts map[string]int
```

You can assign values to a map just like arrays by using curly braces `{...}`
where keys and values are separated by a colon `:`, for example:

```go
counts := map[string]int{
  "Apples": 4,
  "Oranges": 7,
}
```

Maps can be indexed by their keys just like arrays/slices:

```go
counts["Apples"]  // 4
```

And iterated over similar to array/slices:

```go
for key, value := range counts {
  fmt.Printf("%s: %d\n", key, value)
}
```

The only important thing to note about maps in Go is you **must** initialize
a map before using it, a `nil` map will cause a program error and panic:

```go
var counts map[string]int
counts["Apples"] = 7  // This will cause an error and panic!
```

You **must** initialize a map before use by using the `make()` function:

```go
counts := make(map[string]int)
counts["Apples"] = 7
```

## Flow control structures

Go only has one looping construct  as seen in the previous sections:

```go
sum := 0
for i := 0; i < 10; i++ {
  sum += i
}
```

The basic `for` loop has three components separated by semicolons:

- the init statement: executed before the first iteration
- the condition expression: evaluated before every iteration
- the post statement: executed at the end of every iteration

If you omit the condition you effectively have an infinite loop:

```go
for {
}
// This line is never reached!
```

Go has the usual `if` statement along with `else if` and `else` for branching:

```go
N := 42
func Guess(n int) string {
  if n == 42 {
    return "You got it!"
  } else if n < N {
    return "Too low! Try again..."
  } else {
    return "Too high! Try again..."
  }
}        
```

**Note:** The last `else` _could_ have been omitted and been written as
          `return "Too high~ Try again..."`, as it would have been
          functionally equivalent.

There is also a `switch` statement that can be used in place of multiple `if`
and `else if` statements, for example:

```go
func FizzBuzz(n int) string {
  switch n {
  case n % 15 == 0:
    return "FizzBuzz"
  case n % 3 == 0:
    return "Fizz"
  case n % 5 == 0:
    return "Buzz"
  default:
    return fmt.Sprintf("%d", n)
  }
}
```

Functions can be executed at the end of a function anywhere in your function
by "deferring" their execution by using the `defer` keyword. This is commonly
used to close resources automatically at the end of a function, for example:

```go
package main

import (
  "os"
  "fmt"
)

func Goodbye(name string) {
  fmt.Printf("Goodbye %s", name)
}

func Hello(name string) {
  defer Goodbye(name)
  fmt.Printf("Hello %s", name)
}

func main() {
  user := os.Getenv("User")
  Hello(user)
}
```

This will output when run:

```console
$ ./hello
Hello prologic
Goodbye prologic
```

## Error handling

Errors are values in Go and you return them from functions. For example
opening a file with `os.Open` returns a pointer to the open file and `nil`
error on success, otherwise a `nil` pointer and the error that occurred:

```go
f, err := os.Open("/path/to/file")
```

You check for errors like any other value:

```go
f, err := os.Open("/path/to/file")
if err == nil {
  // do something with f
}
```

It is idiomatic Go to check for non-`nil` errors from functions and return
early, for example:

```go
func AppendFile(fn, text string) error {
  f, err := os.OpenFile(fn, os.O_CREATE|os.O_APPEND|os.WR_ONLY, 0644)
  if err != nil {
    return fmt.Errorf("error opening file for writing: %w", err)
  }
  defer f.Close()
  
  if _, err := f.Write([]byte(text)); err != nil {
    return fmt.Errorf("error writing text to fiel: %w", err)
  }
  
  return nil
}
```

## Creating and import packages

Finally Go (_like every other decent languages_) has a module system where you
can create packages and import them. We saw earlier In [Basics](Basics) how we
create a module with `go mod init` when starting a new project.

Go packages are just a directory containing Go source code.
The only difference is the top-line of each module (_each `*.go` source file_):

Create a Go package by first creating a directory for it:

```shell
$ mkdir shapes
```

And initializing it with `go mod init`:

```shell
$ cd shapes
$ go mod init github.com/prologic/shapes
```

Now let's create a source module called `circle.go` using our favorite editor:

```go
package shapes

type Circle struct {
  Radius float64
}

func (c Circle) String() string {
  return fmt.Sprintf("Circle(%0.2f)", c.Radius)
}

func (c Circle) Area() float64 {
  return 3.14 * c.Radius * c.Radius
}
```

It is important to note that in order to "export" functions, structs or
package scoped variables or constants, they **must** be capitalized or the Go
compiler will not export those symbols and you will not be able access them
from importing the package.

Now create a Git repository on [Github](https://github.com) called "shapes"
and push your package to it:

```console
$ git init
$ git commit -a -m "Initial Commit"
$ git remote add origin git@github.com:prologic/shapes.git
$ git push -u origin master
```

You can import the new package `shapes` by using it's fully qualified "importpath"
as `github.com/prologic/shapes`. Go automatically knows hot to fetch and build
the package given its import path.

Example:

Let's create a simple program using the package `github.com/prologic/shapes`:

```console
$ mkdir hello
$ go mod init hello
```

And let's write the code for `main.go` using our favorite editor:

```go
package main

import (
  "fmt"

  "github.com/prologic/shapes"
)

func main() {
  c := shapes.Circle{Radius: 5}
  fmt.Printf("Area of %s: %0.2f\n", c, c.Area())
}
```

Building it with `go build`:

```console
$ go build
```

And finally let's test it out by running the resulting binary:

```console
$ ./hello
Area of Circle(5.00): 78.50
```

Congratulations! 🎉

## Now you're a Gopher!

That's it!  Now you know a fairly decent chunk of Go. 
Some (*pretty important*) things I didn't cover include:

- Writing unit tests, writing tests in Go is really easy!
  See [testing](https://pkg.go.dev/testing)
- The standard library, Go has a huge amount of useful packages in the
  standard library. See
  [Standard Library](https://pkg.go.dev/std).
- Goroutines and Channels, Go's builtin concurrency is really powerful
  and easy to use.
  See [Concurrency](https://tour.golang.org/concurrency/1).
- Cross-Compilation, compiling your program for other architectures
  and operating systems is super easy. Just set the `GOOS` and `GOARCH`
  environment variables when building.

For more details, check the latest [documentation](https://golang.org/doc/),
or for a less half-baked tutorial, please read the official
[Go Tutorial](https://golang.org/doc/tutorial/getting-started) and [A Tour of Go](https://tour.golang.org/welcome/1).

Other great tutorials you can read:

- [Learn X in Y minutes](https://learnxinyminutes.com/docs/go/) (_Where X=Go_)
