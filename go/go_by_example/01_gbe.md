
# Go by Example I

[Sadržaj][00]

## 01 Hello World

Our first program will print the classic “hello world” message. Here’s the full source code.

```go
package main

import "fmt"

func main() {
    fmt.Println("hello world")
}
```

To run the program, put the code in "hello-world.go" and use `go run`.

```sh
$ go run hello-world.go
hello world
```

Sometimes we’ll want to build our programs into binaries. We can do this using go build.

```sh
$ go build hello-world.go
$ ls
hello-world    hello-world.go
```

We can then execute the built binary directly.

```sh
$ ./hello-world
hello world
```

Now that we can run and build basic Go programs.

[Sadržaj][00]

## 02 Values

Go has various value types including strings, integers, floats, booleans, etc. Here are a few basic examples.

```go
package main

import "fmt"

func main() {

    // Strings, which can be added together with +.
    fmt.Println("go" + "lang")

    // Integers and floats.
    fmt.Println("1+1 =", 1+1)
    fmt.Println("7.0/3.0 =", 7.0/3.0)

    // Booleans, with boolean operators as you’d expect.
    fmt.Println(true && false)
    fmt.Println(true || false)
    fmt.Println(!true)
}
```

```sh
$ go run values.go
golang
1+1 = 2
7.0/3.0 = 2.3333333333333335
false
true
false
```

[Sadržaj][00]

## 03 Variables

In Go, variables are explicitly declared and used by the compiler to e.g. check type-correctness of function calls.

```go
package main

import "fmt"

func main() {

    // var declares 1 or more variables.
    var a = "initial"
    fmt.Println(a)

    // You can declare multiple variables at once.
    var b, c int = 1, 2
    fmt.Println(b, c)

    // Go will infer the type of initialized variables.
    var d = true
    fmt.Println(d)

    // Variables declared without a corresponding initialization are
    // zero-valued. For example, the zero value for an int is 0.
    var e int
    fmt.Println(e)

    // The := syntax is shorthand for declaring and initializing a variable, 
    // e.g. for var f string = "apple" in this case. This syntax is only 
    // available inside functions.
    f := "apple"
    fmt.Println(f)
}
```

```sh
$ go run variables.go
initial
1 2
true
0
apple
```

[Sadržaj][00]

## 04 Constants

Go supports constants of character, string, boolean, and numeric values.

```go
package main

import (
    "fmt"
    "math"
)

// const declares a constant value.
const s string = "constant"

func main() {
    fmt.Println(s)

    // A const statement can also appear inside a function body.
    const n = 500000000

    // Constant expressions perform arithmetic with arbitrary precision.
    const d = 3e20 / n
    fmt.Println(d)

    // A numeric constant has no type until it’s given one, such as by an
    // explicit conversion.
    fmt.Println(int64(d))

    // A number can be given a type by using it in a context that requires one, 
    // such as a variable assignment or function call. For example, here math.
    // Sin expects a float64.
    fmt.Println(math.Sin(n))
}
```

```sh
$ go run constant.go 
constant
6e+11
600000000000
-0.28470407323754404
```

[Sadržaj][00]

## 05 For

for is Go’s only looping construct. Here are some basic types of for loops.

```go
package main

import "fmt"

func main() {

    // The most basic type, with a single condition.
    i := 1
    for i <= 3 {
        fmt.Println(i)
        i = i + 1
    }

    // A classic initial/condition/after for loop.
    for j := 0; j < 3; j++ {
        fmt.Println(j)
    }

    // Another way of accomplishing the basic “do this N times” iteration 
    // is range over an integer.
    for i := range 3 {
        fmt.Println("range", i)
    }

    // for without a condition will loop repeatedly until you break out 
    // of the loop or return from the enclosing function.
    for {
        fmt.Println("loop")
        break
    }

    // You can also continue to the next iteration of the loop.
    for n := range 6 {
        if n%2 == 0 {
            continue
        }
        fmt.Println(n)
    }
}
```

```sh
$ go run for.go
1
2
3
0
1
2
range 0
range 1
range 2
loop
1
3
5
```

We’ll see some other for forms later when we look at range statements, channels, and other data structures.

[Sadržaj][00]

## 06 If/Else

Branching with if and else in Go is straight-forward.

```go
package main

import "fmt"

func main() {

    // Here’s a basic example.
    if 7%2 == 0 {
        fmt.Println("7 is even")
    } else {
        fmt.Println("7 is odd")
    }

    // You can have an if statement without an else.
    if 8%4 == 0 {
        fmt.Println("8 is divisible by 4")
    }

    // Logical operators like && and || are often useful in conditions.
    if 8%2 == 0 || 7%2 == 0 {
        fmt.Println("either 8 or 7 are even")
    }

    // A statement can precede conditionals; any variables declared in this 
    // statement are available in the current and all subsequent branches.
    if num := 9; num < 0 {
        fmt.Println(num, "is negative")
    } else if num < 10 {
        fmt.Println(num, "has 1 digit")
    } else {
        fmt.Println(num, "has multiple digits")
    }
}
```

Note that you don’t need parentheses around conditions in Go, but that the braces are required.

```sh
$ go run if-else.go
7 is odd
8 is divisible by 4
either 8 or 7 are even
9 has 1 digit
```

There is no ternary operator if in Go, so you’ll need to use a full if statement even for basic conditions.

[Sadržaj][00]

## 07 Switch

Switch statements express conditionals across many branches.

```go
package main

import (
    "fmt"
    "time"
)

func main() {

    // Here’s a basic switch.
    i := 2
    fmt.Print("Write ", i, " as ")
    switch i {
    case 1:
        fmt.Println("one")
    case 2:
        fmt.Println("two")
    case 3:
        fmt.Println("three")
    }

    // You can use commas to separate multiple expressions in the same case
    // statement. We use the optional default case in this example as well.
    switch time.Now().Weekday() {
    case time.Saturday, time.Sunday:
        fmt.Println("It's the weekend")
    default:
        fmt.Println("It's a weekday")
    }

    // switch without an expression is an alternate way to express if/else 
    // logic. Here we also show how the case expressions can be non-constants.
    t := time.Now()
    switch {
    case t.Hour() < 12:
        fmt.Println("It's before noon")
    default:
        fmt.Println("It's after noon")
    }

    // A type switch compares types instead of values. You can use this to 
    // discover the type of an interface value. In this example, the variable t 
    // will have the type corresponding to its clause.
    whatAmI := func(i interface{}) {
        switch t := i.(type) {
        case bool:
            fmt.Println("I'm a bool")
        case int:
            fmt.Println("I'm an int")
        default:
            fmt.Printf("Don't know type %T\n", t)
        }
    }

whatAmI(true)
    whatAmI(1)
    whatAmI("hey")
}
```

```sh
$ go run switch.go 
Write 2 as two
It's a weekday
It's after noon
I'm a bool
I'm an int
Don't know type string
```

[Sadržaj][00]

## 08 Arrays

In Go, an array is a numbered sequence of elements of a specific length. In typical Go code, slices are much more common; arrays are useful in some special scenarios.

```go
package main

import "fmt"

func main() {

    //Here we create an array a that will hold exactly 5 ints. The type of 
    // elements and length are both part of the array’s type. By default an 
    // array is zero-valued, which for ints means 0s.
    var a [5]int
    fmt.Println("emp:", a)

    // We can set a value at an index using the array[index] = value syntax, 
    // and get a value with array[index].
    a[4] = 100
    fmt.Println("set:", a)
    fmt.Println("get:", a[4])

    // The builtin len returns the length of an array.
    fmt.Println("len:", len(a))

    // Use this syntax to declare and initialize an array in one line.
    b := [5]int{1, 2, 3, 4, 5}
    fmt.Println("dcl:", b)

    // You can also have the compiler count the number of elements for you 
    // with ...
    b = [...]int{1, 2, 3, 4, 5}
    fmt.Println("dcl:", b)

    // If you specify the index with :, the elements in between will be zeroed.
    b = [...]int{100, 3: 400, 500}
    fmt.Println("idx:", b)

    // Array types are one-dimensional, but you can compose types to build 
    // multi-dimensional data structures.
    var twoD [2][3]int
    for i := range 2 {
        for j := range 3 {
            twoD[i][j] = i + j
        }
    }
    fmt.Println("2d: ", twoD)

    // You can create and initialize multi-dimensional arrays at once too.
    twoD = [2][3]int{
        {1, 2, 3},
        {1, 2, 3},
    }
    fmt.Println("2d: ", twoD)
}
```

Note that arrays appear in the form [v1 v2 v3 ...] when printed with fmt.Println.

```sh
$ go run arrays.go
emp: [0 0 0 0 0]
set: [0 0 0 0 100]
get: 100
len: 5
dcl: [1 2 3 4 5]
dcl: [1 2 3 4 5]
idx: [100 0 0 400 500]
2d:  [[0 1 2] [1 2 3]]
2d:  [[1 2 3] [1 2 3]]
```

[Sadržaj][00]

## 09 Slices

Slices are an important data type in Go, giving a more powerful interface to sequences than arrays.

```go
package main

import (
    "fmt"
    "slices"
)

func main() {

    // Unlike arrays, slices are typed only by the elements they contain (not the number
    // of elements). An uninitialized slice equals to nil and has length 0.

    var s []string
    fmt.Println("uninit:", s, s == nil, len(s) == 0)
    
    // To create a slice with non-zero length, use the builtin make. Here we make a slice of strings 
    // of length 3 (initially zero-valued). By default a new slice’s capacity is equal to its 
    // length; if we know the slice is going to grow ahead of time, it’s possible to pass a capacity 
    // explicitly as an additional parameter to make.
    s = make([]string, 3)
    fmt.Println("emp:", s, "len:", len(s), "cap:", cap(s))

    // We can set and get just like with arrays.
    s[0] = "a"
    s[1] = "b"
    s[2] = "c"
    fmt.Println("set:", s)
    fmt.Println("get:", s[2])

    // len returns the length of the slice as expected.
    fmt.Println("len:", len(s))

    // In addition to these basic operations, slices support several more that make them richer than 
    // arrays. One is the builtin append, which returns a slice containing one or more new values. 
    // Note that we need to accept a return value from append as we may get a new slice value.
    s = append(s, "d")
    s = append(s, "e", "f")
    fmt.Println("apd:", s)

    // Slices can also be copy’d. Here we create an empty slice c of the same length as s and copy 
    // into c from s.
    c := make([]string, len(s))
    copy(c, s)
    fmt.Println("cpy:", c)

    // Slices support a “slice” operator with the syntax slice[low:high]. For example, this gets a 
    // slice of the elements s[2], s[3], and s[4].
    l := s[2:5]
    fmt.Println("sl1:", l)

    // This slices up to (but excluding) s[5].
    l = s[:5]
    fmt.Println("sl2:", l)

    // And this slices up from (and including) s[2].
    l = s[2:]
    fmt.Println("sl3:", l)

    // We can declare and initialize a variable for slice in a single line as well.
    t := []string{"g", "h", "i"}
    fmt.Println("dcl:", t)

    // The slices package contains a number of useful utility functions for slices.
    t2 := []string{"g", "h", "i"}
    if slices.Equal(t, t2) {
        fmt.Println("t == t2")
    }

    // Slices can be composed into multi-dimensional data structures. The length of the inner slices 
    // can   vary, unlike with multi-dimensional arrays.
    twoD := make([][]int, 3)
    for i := range 3 {
        innerLen := i + 1
        twoD[i] = make([]int, innerLen)
        for j := range innerLen {
            twoD[i][j] = i + j
        }
    }
    fmt.Println("2d: ", twoD)
}
```

Note that while slices are different types than arrays, they are rendered similarly by fmt.Println.

```sh
$ go run slices.go
uninit: [] true true
emp: [  ] len: 3 cap: 3
set: [a b c]
get: c
len: 3
apd: [a b c d e f]
cpy: [a b c d e f]
sl1: [c d e]
sl2: [a b c d e]
sl3: [c d e f]
dcl: [g h i]
t == t2
2d:  [[0] [1 2] [2 3 4]]
```

[Sadržaj][00]

## 10 Maps

Maps are Go’s built-in associative data type (sometimes called hashes or dicts in other languages).

```go
package main

import (
    "fmt"
    "maps"
)

func main() {

    // To create an empty map, use the builtin make: make(map[key-type]val-type).
    m := make(map[string]int)

    // Set key/value pairs using typical name[key] = val syntax.
    m["k1"] = 7
    m["k2"] = 13

    // Printing a map with e.g. fmt.Println will show all of its key/value pairs.
    fmt.Println("map:", m)

    // Get a value for a key with name[key].
    v1 := m["k1"]
    fmt.Println("v1:", v1)

    // If the key doesn’t exist, the zero value of the value type is returned.
    v3 := m["k3"]
    fmt.Println("v3:", v3)

    // The builtin len returns the number of key/value pairs when called on a map.
    fmt.Println("len:", len(m))

    // The builtin delete removes key/value pairs from a map.
    delete(m, "k2")
    fmt.Println("map:", m)

    // To remove all key/value pairs from a map, use the clear builtin.
    clear(m)
    fmt.Println("map:", m)

    // The optional second return value when getting a value from a map indicates if the key was 
    // present in the map. This can be used to disambiguate between missing keys and keys with zero values like 0 or "". Here we didn’t need the value itself, so we ignored it with the blank identifier _.
    _, prs := m["k2"]
    fmt.Println("prs:", prs)

    // You can also declare and initialize a new map in the same line with this syntax.
    n := map[string]int{"foo": 1, "bar": 2}
    fmt.Println("map:", n)

    // The maps package contains a number of useful utility functions for maps.
    n2 := map[string]int{"foo": 1, "bar": 2}
    if maps.Equal(n, n2) {
        fmt.Println("n == n2")
    }
}
```

Note that maps appear in the form map[k:v k:v] when printed with fmt.Println.

```sh
$ go run maps.go 
map: map[k1:7 k2:13]
v1: 7
v3: 0
len: 2
map: map[k1:7]
map: map[]
prs: false
map: map[bar:2 foo:1]
n == n2
```

[Sadržaj][00]

## 11 Functions

Functions are central in Go. We’ll learn about functions with a few different examples.

```go
package main

import "fmt"

// Here’s a function that takes two ints and returns their sum as an int.
func plus(a int, b int) int {

    // Go requires explicit returns, i.e. it won’t automatically return the value of the last 
    // expression.
    return a + b
}

// When you have multiple consecutive parameters of the same type, you may omit the type name for 
// the like-typed parameters up to the final parameter that declares the type.
func plusPlus(a, b, c int) int {
    return a + b + c
}

func main() {

    // Call a function just as you’d expect, with name(args).
    res := plus(1, 2)
    fmt.Println("1+2 =", res)

    res = plusPlus(1, 2, 3)
    fmt.Println("1+2+3 =", res)
}
```

```sh
$ go run functions.go 
1+2 = 3
1+2+3 = 6
```

There are several other features to Go functions. One is multiple return values, which we’ll look at next.

[Sadržaj][00]

## 12 Multiple Return Values

Go has built-in support for multiple return values. This feature is used often in idiomatic Go, for example to return both result and error values from a function.

```go
package main

import "fmt"

// The (int, int) in this function signature shows that the function returns 2 ints.
func vals() (int, int) {
    return 3, 7
}

func main() {

    // Here we use the 2 different return values from the call with multiple assignment.
    a, b := vals()
    fmt.Println(a)
    fmt.Println(b)

    // If you only want a subset of the returned values, use the blank identifier _.
    _, c := vals()
    fmt.Println(c)
}
```

```sh
$ go run multiple-return-values.go
3
7
7
```

Accepting a variable number of arguments is another nice feature of Go functions; we’ll look at this next.

[Sadržaj][00]

## 13 Variadic Functions

Variadic functions can be called with any number of trailing arguments. For example, fmt.Println is a common variadic function.

```go
package main

import "fmt"

// Here’s a function that will take an arbitrary number of ints as arguments.
func sum(nums ...int) {
    fmt.Print(nums, " ")
    total := 0

    // Within the function, the type of nums is equivalent to []int. We can call len(nums), iterate 
    // over it with range, etc.

    for _, num := range nums {
        total += num
    }
    fmt.Println(total)
}

func main() {

    // Variadic functions can be called in the usual way with individual arguments.
    sum(1, 2)
    sum(1, 2, 3)

    // If you already have multiple args in a slice, apply them to a variadic function using func
    // (slice...) like this.

    nums := []int{1, 2, 3, 4}
    sum(nums...)
}
```

```go
$ go run variadic-functions.go 
[1 2] 3
[1 2 3] 6
[1 2 3 4] 10
```

Another key aspect of functions in Go is their ability to form closures, which we’ll look at next.

[Sadržaj][00]

## 14 Closures

Go supports anonymous functions, which can form closures. Anonymous functions are useful when you want to define a function inline without having to name it.

```go
package main

import "fmt"

// This function intSeq returns another function, which we define anonymously in the body of intSeq. 
// The returned function closes over the variable i to form a closure.
func intSeq() func() int {
    i := 0
    return func() int {
        i++
        return i
    }
}

func main() {

    // We call intSeq, assigning the result (a function) to nextInt. This function value captures 
    // its own i value, which will be updated each time we call nextInt.
    nextInt := intSeq()

    // See the effect of the closure by calling nextInt a few times.
    fmt.Println(nextInt())
    fmt.Println(nextInt())
    fmt.Println(nextInt())

    // To confirm that the state is unique to that particular function, create and test a new one.
    newInts := intSeq()
    fmt.Println(newInts())
}
```

```sh
$ go run closures.go
1
2
3
1
```

The last feature of functions we’ll look at for now is recursion.

[Sadržaj][00]

## 15 Recursion

Go supports recursive functions. Here’s a classic example.

```go
package main

import "fmt"

// This fact function calls itself until it reaches the base case of fact(0).
func fact(n int) int {
    if n == 0 {
        return 1
    }
    return n * fact(n-1)
}

func main() {
    fmt.Println(fact(7))

    // Anonymous functions can also be recursive, but this requires explicitly declaring a variable 
    // with var to store the function before it’s defined.
    var fib func(n int) int

    fib = func(n int) int {
        if n < 2 {
            return n
        }

    // Since fib was previously declared in main, Go knows which function to call with fib here.
        return fib(n-1) + fib(n-2)
    }

    fmt.Println(fib(7))
}
```

```sh
$ go run recursion.go 
5040
13
```

[Sadržaj][00]

## 16 Range over Built-in Types

range iterates over elements in a variety of built-in data structures. Let’s see how to use range with some of the data structures we’ve already learned.

```go
package main

import "fmt"

func main() {

    // Here we use range to sum the numbers in a slice. Arrays work like this too.
    nums := []int{2, 3, 4}
    sum := 0
    for _, num := range nums {
        sum += num
    }
    fmt.Println("sum:", sum)

    // range on arrays and slices provides both the index and value for each entry. Above we didn’t 
    // need the index, so we ignored it with the blank identifier _. Sometimes we actually want the 
    // indexes though.
    for i, num := range nums {
        if num == 3 {
            fmt.Println("index:", i)
        }
    }

    // range on map iterates over key/value pairs.
    kvs := map[string]string{"a": "apple", "b": "banana"}
    for k, v := range kvs {
        fmt.Printf("%s -> %s\n", k, v)
    }

    // range can also iterate over just the keys of a map.
    for k := range kvs {
        fmt.Println("key:", k)
    }

    // range on strings iterates over Unicode code points. The first value is the starting byte 
    // index of the rune and the second the rune itself. See Strings and Runes for more details.
    for i, c := range "go" {
        fmt.Println(i, c)
    }
}
```

```sh
$ go run range-over-built-in-types.go
sum: 9
index: 1
a -> apple
b -> banana
key: a
key: b
0 103
1 111
```

[Sadržaj][00]

## 17 Pointers

Go supports pointers, allowing you to pass references to values and records within your program.

```go
package main

import "fmt"

// We’ll show how pointers work in contrast to values with 2 functions: zeroval and zeroptr. 
// zeroval has an int parameter, so arguments will be passed to it by value. zeroval will get a 
// copy of ival distinct from the one in the calling function.
func zeroval(ival int) {
    ival = 0
}

// zeroptr in contrast has an *int parameter, meaning that it takes an int pointer. The *iptr code
// in the function body then dereferences the pointer from its memory address to the current value 
// at that address. Assigning a value to a dereferenced pointer changes the value at the referenced 
// address.
func zeroptr(iptr *int) {
    *iptr = 0
}

func main() {
    i := 1
    fmt.Println("initial:", i)

    zeroval(i)
    fmt.Println("zeroval:", i)

    // The &i syntax gives the memory address of i, i.e. a pointer to i.
    zeroptr(&i)
    fmt.Println("zeroptr:", i)

    // Pointers can be printed too.
    fmt.Println("pointer:", &i)
}
```

zeroval doesn’t change the i in main, but zeroptr does because it has a reference to the memory address for that variable.

```sh
$ go run pointers.go
initial: 1
zeroval: 1
zeroptr: 0
pointer: 0x42131100
```

[Sadržaj][00]

## 18 Strings and Runes

A Go string is a read-only slice of bytes. The language and the standard library treat strings specially - as containers of text encoded in UTF-8. In other languages, strings are made of “characters”. In Go, the concept of a character is called a rune - it’s an integer that represents a Unicode code point. This Go blog post is a good introduction to the topic.

```go
package main

import (
    "fmt"
    "unicode/utf8"
)

func main() {

    // s is a string assigned a literal value representing the word “hello” in the Thai language. 
    // Go string literals are UTF-8 encoded text.
    const s = "สวัสดี"

    // Since strings are equivalent to []byte, this will produce the length of the raw bytes stored 
    // within.
    fmt.Println("Len:", len(s))

    // Indexing into a string produces the raw byte values at each index. This loop generates the 
    // hex values of all the bytes that constitute the code points in s.
    for i := 0; i < len(s); i++ {
        fmt.Printf("%x ", s[i])
    }
    fmt.Println()

    // To count how many runes are in a string, we can use the utf8 package. Note that the run-time 
    // of RuneCountInString depends on the size of the string, because it has to decode each UTF-8 
    // rune sequentially. Some Thai characters are represented by UTF-8 code points that can span 
    // multiple bytes, so the result of this count may be surprising.
    fmt.Println("Rune count:", utf8.RuneCountInString(s))

    // A range loop handles strings specially and decodes each rune along with its offset in the 
    // string.
    for idx, runeValue := range s {
        fmt.Printf("%#U starts at %d\n", runeValue, idx)
    }

    // We can achieve the same iteration by using the utf8.DecodeRuneInString function explicitly.
    fmt.Println("\nUsing DecodeRuneInString")
    for i, w := 0, 0; i < len(s); i += w {
        runeValue, width := utf8.DecodeRuneInString(s[i:])
        fmt.Printf("%#U starts at %d\n", runeValue, i)
        w = width

    // This demonstrates passing a rune value to a function.
        examineRune(runeValue)
    }
}

func examineRune(r rune) {

    // Values enclosed in single quotes are rune literals. We can compare a rune value to a rune 
    // literal directly.
    if r == 't' {
        fmt.Println("found tee")
    } else if r == 'ส' {
        fmt.Println("found so sua")
    }
}
```

```sh
$ go run strings-and-runes.go
Len: 18
e0 b8 aa e0 b8 a7 e0 b8 b1 e0 b8 aa e0 b8 94 e0 b8 b5 
Rune count: 6
U+0E2A 'ส' starts at 0
U+0E27 'ว' starts at 3
U+0E31 'ั' starts at 6
U+0E2A 'ส' starts at 9
U+0E14 'ด' starts at 12
U+0E35 'ี' starts at 15

Using DecodeRuneInString
U+0E2A 'ส' starts at 0
found so sua
U+0E27 'ว' starts at 3
U+0E31 'ั' starts at 6
U+0E2A 'ส' starts at 9
found so sua
U+0E14 'ด' starts at 12
U+0E35 'ี' starts at 15
```

[Sadržaj][00]

## 19 Structs

Go’s structs are typed collections of fields. They’re useful for grouping data together to form records.

```go
package main

import "fmt"

// This person struct type has name and age fields.


type person struct {
    name string
    age  int
}

// newPerson constructs a new person struct with the given name.
func newPerson(name string) *person {

    // Go is a garbage collected language; you can safely return a pointer to a local variable - it 
    // will only be cleaned up by the garbage collector when there are no active references to it.
    p := person{name: name}
    p.age = 42
    return &p
}

func main() {

    // This syntax creates a new struct.
    fmt.Println(person{"Bob", 20})

    // You can name the fields when initializing a struct.
    fmt.Println(person{name: "Alice", age: 30})

    // Omitted fields will be zero-valued.
    fmt.Println(person{name: "Fred"})

    // An & prefix yields a pointer to the struct.
    fmt.Println(&person{name: "Ann", age: 40})

    // It’s idiomatic to encapsulate new struct creation in constructor functions
    fmt.Println(newPerson("Jon"))

    // Access struct fields with a dot.
    s := person{name: "Sean", age: 50}
    fmt.Println(s.name)

    // You can also use dots with struct pointers - the pointers are automatically dereferenced.
    sp := &s
    fmt.Println(sp.age)

    // Structs are mutable.
    sp.age = 51
    fmt.Println(sp.age)

    // If a struct type is only used for a single value, we don’t have to give it a name. The value 
    // can have an anonymous struct type. This technique is commonly used for table-driven tests.
    dog := struct {
        name   string
        isGood bool
    }{
        "Rex",
        true,
    }
    fmt.Println(dog)
}
```

```sh
$ go run structs.go
{Bob 20}
{Alice 30}
{Fred 0}
&{Ann 40}
&{Jon 42}
Sean
50
51
{Rex true}
```

[Sadržaj][00]

## 20 Methods

Go supports methods defined on struct types.

```go
package main

import "fmt"

type rect struct {
    width, height int
}

// This area method has a receiver type of *rect.
func (r *rect) area() int {
    return r.width * r.height
}

// Methods can be defined for either pointer or value receiver types. Here’s an example of a value 
// receiver.
func (r rect) perim() int {
    return 2*r.width + 2*r.height
}

func main() {
    r := rect{width: 10, height: 5}

    // Here we call the 2 methods defined for our struct.
    fmt.Println("area: ", r.area())
    fmt.Println("perim:", r.perim())

    // Go automatically handles conversion between values and pointers for method calls. You may 
    // want to use a pointer receiver type to avoid copying on method calls or to allow the method 
    // to mutate the receiving struct.
    rp := &r
    fmt.Println("area: ", rp.area())
    fmt.Println("perim:", rp.perim())
}
```

```sh
$ go run methods.go 
area:  50
perim: 30
area:  50
perim: 30
```

Next we’ll look at Go’s mechanism for grouping and naming related sets of methods: interfaces.

[Sadržaj][00]

## 21 Interfaces

Interfaces are named collections of method signatures.

```go
package main

import (
    "fmt"
    "math"
)

// Here’s a basic interface for geometric shapes.
type geometry interface {
    area() float64
    perim() float64
}

// For our example we’ll implement this interface on rect and circle types.
type rect struct {
    width, height float64
}

type circle struct {
    radius float64
}

// To implement an interface in Go, we just need to implement all the methods in the interface. 
// Here we implement geometry on rects.
func (r rect) area() float64 {
    return r.width * r.height
}

func (r rect) perim() float64 {
    return 2*r.width + 2*r.height
}

// The implementation for circles.
func (c circle) area() float64 {
    return math.Pi * c.radius * c.radius
}

func (c circle) perim() float64 {
    return 2 * math.Pi * c.radius
}

// If a variable has an interface type, then we can call methods that are in the named interface. 
// Here’s a generic measure function taking advantage of this to work on any geometry.
func measure(g geometry) {
    fmt.Println(g)
    fmt.Println(g.area())
    fmt.Println(g.perim())
}

// Sometimes it’s useful to know the runtime type of an interface value. One option is using a type 
// assertion as shown here; another is a type switch.
func detectCircle(g geometry) {
    if c, ok := g.(circle); ok {
        fmt.Println("circle with radius", c.radius)
    }
}

func main() {
    r := rect{width: 3, height: 4}
    c := circle{radius: 5}

    // The circle and rect struct types both implement the geometry interface so we can use 
    // instances of these structs as arguments to measure.
    measure(r)
    measure(c)

    detectCircle(r)
    detectCircle(c)
}
```

```sh
$ go run interfaces.go
{3 4}
12
14
{5}
78.53981633974483
31.41592653589793
circle with radius 5
```

To understand how Go’s interfaces work under the hood, check out this blog post.

[Sadržaj][00]

## 22 Enums

Enumerated types (enums) are a special case of sum types. An enum is a type that has a fixed number of possible values, each with a distinct name. Go doesn’t have an enum type as a distinct language feature, but enums are simple to implement using existing language idioms.

```go
package main

import "fmt"

// Our enum type ServerState has an underlying int type.
type ServerState int

// The possible values for ServerState are defined as constants. The special keyword iota generates 
// successive constant values automatically; in this case 0, 1, 2 and so on.
const (
    StateIdle ServerState = iota
    StateConnected
    StateError
    StateRetrying
)

// By implementing the fmt.Stringer interface, values of ServerState can be printed out or
//  converted to strings.

// This can get cumbersome if there are many possible values. In such cases the stringer tool can 
// be used in conjunction with go:generate to automate the process. See this post for a longer 
// explanation.
var stateName = map[ServerState]string{
    StateIdle:      "idle",
    StateConnected: "connected",
    StateError:     "error",
    StateRetrying:  "retrying",
}

func (ss ServerState) String() string {
    return stateName[ss]
}

// If we have a value of type int, we cannot pass it to transition - the compiler will complain 
// about type mismatch. This provides some degree of compile-time type safety for enums.
func main() {
    ns := transition(StateIdle)
    fmt.Println(ns)

    ns2 := transition(ns)
    fmt.Println(ns2)
}

// transition emulates a state transition for a server; it takes the existing state and returns a 
// new state.
func transition(s ServerState) ServerState {
    switch s {
    case StateIdle:
        return StateConnected
    case StateConnected, StateRetrying:
        // Suppose we check some predicates here to determine the next state…
        return StateIdle
    case StateError:
        return StateError
    default:
        panic(fmt.Errorf("unknown state: %s", s))
    }
}
```

```sh
$ go run enums.go
connected
idle
```

[Sadržaj][00]

## 23 Struct Embedding

Go supports embedding of structs and interfaces to express a more seamless composition of types. This is not to be confused with //go:embed which is a go directive introduced in Go version 1.16+ to embed files and folders into the application binary.

```go
package main

import "fmt"

type base struct {
    num int
}

func (b base) describe() string {
    return fmt.Sprintf("base with num=%v", b.num)
}

// A container embeds a base. An embedding looks like a field without a name.
type container struct {
    base
    str string
}

func main() {

    // When creating structs with literals, we have to initialize the embedding explicitly; here 
    // the embedded type serves as the field name.
    co := container{
        base: base{
            num: 1,
        },
        str: "some name",
    }

    // We can access the base’s fields directly on co, e.g. co.num.
    fmt.Printf("co={num: %v, str: %v}\n", co.num, co.str)

    // Alternatively, we can spell out the full path using the embedded type name.
    fmt.Println("also num:", co.base.num)

    // Since container embeds base, the methods of base also become methods of a container. Here we 
    // invoke a method that was embedded from base directly on co.
    // fmt.Println("describe:", co.describe())
    type describer interface {
        describe() string
    }

    // Embedding structs with methods may be used to bestow interface implementations onto other 
    // structs. Here we see that a container now implements the describer interface because it 
    // embeds base.
    var d describer = co
    fmt.Println("describer:", d.describe())
}
```

```sh
$ go run struct-embedding.go
co={num: 1, str: some name}
also num: 1
describe: base with num=1
describer: base with num=1
```

[Sadržaj][00]

## 24 Generics

Starting with version 1.18, Go has added support for generics, also known as type parameters.

```go
package main

import "fmt"

// As an example of a generic function, SlicesIndex takes a slice of any comparable type and an 
// element of that type and returns the index of the first occurrence of v in s, or -1 if not 
// present. The comparable constraint means that we can compare values of this type with the == and 
// != operators. For a more thorough explanation of this type signature, see this blog post. Note 
// that this function exists in the standard library as slices.Index.

func SlicesIndex[S ~[]E, E comparable](s S, v E) int {
    for i := range s {
        if v == s[i] {
            return i
        }
    }
    return -1
}

// As an example of a generic type, List is a singly-linked list with values of any type.
type List[T any] struct {
    head, tail *element[T]
}

type element[T any] struct {
    next *element[T]
    val  T
}

// We can define methods on generic types just like we do on regular types, but we have to keep the 
// type parameters in place. The type is List[T], not List.
func (lst *List[T]) Push(v T) {
    if lst.tail == nil {
        lst.head = &element[T]{val: v}
        lst.tail = lst.head
    } else {
        lst.tail.next = &element[T]{val: v}
        lst.tail = lst.tail.next
    }
}

// AllElements returns all the List elements as a slice. In the next example we’ll see a more 
// idiomatic way of iterating over all elements of custom types.
func (lst *List[T]) AllElements() []T {
    var elems []T
    for e := lst.head; e != nil; e = e.next {
        elems = append(elems, e.val)
    }
    return elems
}

func main() {
    var s = []string{"foo", "bar", "zoo"}

    // When invoking generic functions, we can often rely on type inference. Note that we don’t 
    // have to specify the types for S and E when calling SlicesIndex - the compiler infers them 
    // automatically.
    fmt.Println("index of zoo:", SlicesIndex(s, "zoo"))

    // … though we could also specify them explicitly.
    _ = SlicesIndex[[]string, string](s, "zoo")

    lst := List[int]{}
    lst.Push(10)
    lst.Push(13)
    lst.Push(23)
    fmt.Println("list:", lst.AllElements())
}
```

```sh
$ go run generics.go
index of zoo: 2
list: [10 13 23]
```

[Sadržaj][00]

## 25 Range over Iterators

Starting with version 1.23, Go has added support for iterators, which lets us range over pretty much anything!

```go
package main

import (
    "fmt"
    "iter"
    "slices"
    "strings"
)

// Let’s look at the List type from the previous example again. In that example we had an 
// AllElements method that returned a slice of all elements in the list. With Go iterators, we can 
// do it better - as shown below.
type List[T any] struct {
    head, tail *element[T]
}

type element[T any] struct {
    next *element[T]
    val  T
}

func (lst *List[T]) Push(v T) {
    if lst.tail == nil {
        lst.head = &element[T]{val: v}
        lst.tail = lst.head
    } else {
        lst.tail.next = &element[T]{val: v}
        lst.tail = lst.tail.next
    }
}

// All returns an iterator, which in Go is a function with a special signature.

func (lst *List[T]) All() iter.Seq[T] {
    return func(yield func(T) bool) {

    // The iterator function takes another function as a parameter, called yield by convention (but 
    // the name can be arbitrary). It will call yield for every element we want to iterate over, 
    // and note yield’s return value for a potential early termination.
        for e := lst.head; e != nil; e = e.next {
            if !yield(e.val) {
                return
            }
        }
    }
}

// Iteration doesn’t require an underlying data structure, and doesn’t even have to be finite! 
// Here’s a function returning an iterator over Fibonacci numbers: it keeps running as long as 
// yield keeps returning true.
func genFib() iter.Seq[int] {
    return func(yield func(int) bool) {
        a, b := 0, 1

        for {
            if !yield(a) {
                return
            }
            a, b = b, a+b
        }
    }
}

func main() {
    lst := List[int]{}
    lst.Push(10)
    lst.Push(13)
    lst.Push(23)

    // Since List.All returns an iterator, we can use it in a regular range loop.
    for e := range lst.All() {
        fmt.Println(e)
    }

    // Packages like slices have a number of useful functions to work with iterators. For example, 
    // Collect takes any iterator and collects all its values into a slice.
    all := slices.Collect(lst.All())
    fmt.Println("all:", all)

    // Standard library packages now expose iterator helpers too. For example, strings.SplitSeq 
    // iterates over parts of a byte slice without first building a result slice.
    for part := range strings.SplitSeq("go-by-example", "-") {
        fmt.Printf("part: %s\n", part)
    }
    for n := range genFib() {

        // Once the loop hits break or an early return, the yield function passed to the iterator 
        // will return false.
        if n >= 10 {
            break
        }
        fmt.Println(n)
    }
}
```

```sh
$ go run range-over-iterators.go
10
13
23
all: [10 13 23]
part: go
part: by
part: example
0
1
1
2
3
5
8
```

[Sadržaj][00]

[00]: 00_gbe.md
