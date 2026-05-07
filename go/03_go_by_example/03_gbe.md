# Go by Example III

[Sadržaj][00]

## 51 String Functions

The standard library’s strings package provides many useful string-related functions. Here are some examples to give you a sense of the package.

```go
package main

import (
    "fmt"
    s "strings"
)

// We alias fmt.Println to a shorter name as we’ll use it a lot below.
var p = fmt.Println

func main() {

    // Here’s a sample of the functions available in strings. Since these are 
    // functions from the package, not methods on the string object itself, we 
    // need to pass the string in question as the first argument to the 
    // function. You can find more functions in the strings package docs.
    p("Contains:  ", s.Contains("test", "es"))
    p("Count:     ", s.Count("test", "t"))
    p("HasPrefix: ", s.HasPrefix("test", "te"))
    p("HasSuffix: ", s.HasSuffix("test", "st"))
    p("Index:     ", s.Index("test", "e"))
    p("Join:      ", s.Join([]string{"a", "b"}, "-"))
    p("Repeat:    ", s.Repeat("a", 5))
    p("Replace:   ", s.Replace("foo", "o", "0", -1))
    p("Replace:   ", s.Replace("foo", "o", "0", 1))
    p("Split:     ", s.Split("a-b-c-d-e", "-"))
    p("ToLower:   ", s.ToLower("TEST"))
    p("ToUpper:   ", s.ToUpper("test"))
}
```

```sh
go run string-functions.go
Contains:   true
Count:      2
HasPrefix:  true
HasSuffix:  true
Index:      1
Join:       a-b
Repeat:     aaaaa
Replace:    f00
Replace:    f0o
Split:      [a b c d e]
ToLower:    test
ToUpper:    TEST
```

[Sadržaj][00]

## 52 String Formatting

Go offers excellent support for string formatting in the printf tradition. Here are some examples of common string formatting tasks.

```go
package main

import (
    "fmt"
    "os"
)

type point struct {
    x, y int
}

func main() {

    // Go offers several printing “verbs” designed to format general Go 
    // values. For example, this prints an instance of our point struct.
    p := point{1, 2}
    fmt.Printf("struct1: %v\n", p)

    // If the value is a struct, the %+v variant will include the struct’s 
    // field names.
    fmt.Printf("struct2: %+v\n", p)

    // The %#v variant prints a Go syntax representation of the value, i.e. 
    // the source code snippet that would produce that value.
    fmt.Printf("struct3: %#v\n", p)

    // To print the type of a value, use %T.
    fmt.Printf("type: %T\n", p)

    // Formatting booleans is straight-forward.
    fmt.Printf("bool: %t\n", true)

    // There are many options for formatting integers. Use %d for standard, 
    // base-10 formatting.
    fmt.Printf("int: %d\n", 123)

    // This prints a binary representation.
    fmt.Printf("bin: %b\n", 14)

    // This prints the character corresponding to the given integer.
    fmt.Printf("char: %c\n", 33)

    // %x provides hex encoding.
    fmt.Printf("hex: %x\n", 456)

    // There are also several formatting options for floats. For basic decimal 
    // formatting use %f.
    fmt.Printf("float1: %f\n", 78.9)

    // %e and %E format the float in (slightly different versions of) 
    // scientific notation.
    fmt.Printf("float2: %e\n", 123400000.0)
    fmt.Printf("float3: %E\n", 123400000.0)

    // For basic string printing use %s.
    fmt.Printf("str1: %s\n", "\"string\"")

    // To double-quote strings as in Go source, use %q.
    fmt.Printf("str2: %q\n", "\"string\"")

    // As with integers seen earlier, %x renders the string in base-16, with 
    // two output characters per byte of input.
    fmt.Printf("str3: %x\n", "hex this")

    // To print a representation of a pointer, use %p.
    fmt.Printf("pointer: %p\n", &p)

    // When formatting numbers you will often want to control the width and 
    // precision of the resulting figure. To specify the width of an integer, 
    // use a number after the % in the verb. By default the result will be 
    // right-justified and padded with spaces.
    fmt.Printf("width1: |%6d|%6d|\n", 12, 345)

    // You can also specify the width of printed floats, though usually you’ll 
    // also want to restrict the decimal precision at the same time with the 
    // width.precision syntax.
    fmt.Printf("width2: |%6.2f|%6.2f|\n", 1.2, 3.45)
    
    // To left-justify, use the - flag.
    fmt.Printf("width3: |%-6.2f|%-6.2f|\n", 1.2, 3.45)

    // You may also want to control width when formatting strings, especially to ensure that they 
    // align in table-like output. For basic right-justified width.
    fmt.Printf("width4: |%6s|%6s|\n", "foo", "b")

    // To left-justify use the - flag as with numbers.
    fmt.Printf("width5: |%-6s|%-6s|\n", "foo", "b")

    // So far we’ve seen Printf, which prints the formatted string to os.Stdout. Sprintf formats 
    // and returns a string without printing it anywhere.
    s := fmt.Sprintf("sprintf: a %s", "string")
    fmt.Println(s)

    // You can format+print to io.Writers other than os.Stdout using Fprintf.
    fmt.Fprintf(os.Stderr, "io: an %s\n", "error")
}
```

```sh
go run string-formatting.go
struct1: {1 2}
struct2: {x:1 y:2}
struct3: main.point{x:1, y:2}
type: main.point
bool: true
int: 123
bin: 1110
char: !
hex: 1c8
float1: 78.900000
float2: 1.234000e+08
float3: 1.234000E+08
str1: "string"
str2: "\"string\""
str3: 6865782074686973
pointer: 0xc0000ba000
width1: |    12|   345|
width2: |  1.20|  3.45|
width3: |1.20  |3.45  |
width4: |   foo|     b|
width5: |foo   |b     |
sprintf: a string
io: an error
```

[Sadržaj][00]

## 53 Text Templates

Go offers built-in support for creating dynamic content or showing customized output to the user with the text/template package. A sibling package named html/template provides the same API but has additional security features and should be used for generating HTML.

```go
package main

import (
    "os"
    "text/template"
)

func main() {

    // We can create a new template and parse its body from a string. Templates are a mix of static 
    // text and “actions” enclosed in {{...}} that are used to dynamically insert content.
    t1 := template.New("t1")
    t1, err := t1.Parse("Value is {{.}}\n")
    if err != nil {
        panic(err)
    }

    // Alternatively, we can use the template.Must function to panic in case Parse returns an 
    // error. This is especially useful for templates initialized in the global scope.
    t1 = template.Must(t1.Parse("Value: {{.}}\n"))

    // By “executing” the template we generate its text with specific values for its actions. The 
    // {{.}} action is replaced by the value passed as a parameter to Execute.
    t1.Execute(os.Stdout, "some text")
    t1.Execute(os.Stdout, 5)
    t1.Execute(os.Stdout, []string{
        "Go",
        "Rust",
        "C++",
        "C#",
    })

    // Helper function we’ll use below.
    Create := func(name, t string) *template.Template {
        return template.Must(template.New(name).Parse(t))
    }

    // If the data is a struct we can use the {{.FieldName}} action to access its fields. The 
    // fields should be exported to be accessible when a template is executing.
    t2 := Create("t2", "Name: {{.Name}}\n")

    t2.Execute(os.Stdout, struct {
        Name string
    }{"Jane Doe"})

    // The same applies to maps; with maps there is no restriction on the case of key names.
    t2.Execute(os.Stdout, map[string]string{
        "Name": "Mickey Mouse",
    })

    // if/else provide conditional execution for templates. A value is considered false if it’s the 
    // default value of a type, such as 0, an empty string, nil pointer, etc. This sample 
    // demonstrates another feature of templates: using - in actions to trim whitespace.
    t3 := Create("t3",
        "{{if . -}} yes {{else -}} no {{end}}\n")
    t3.Execute(os.Stdout, "not empty")
    t3.Execute(os.Stdout, "")

    // range blocks let us loop through slices, arrays, maps or channels. Inside the range block 
    // {{.}} is set to the current item of the iteration.
    t4 := Create("t4",
        "Range: {{range .}}{{.}} {{end}}\n")
    t4.Execute(os.Stdout,
        []string{
            "Go",
            "Rust",
            "C++",
            "C#",
        })
}
```

```sh
go run templates.go 
Value: some text
Value: 5
Value: [Go Rust C++ C#]
Name: Jane Doe
Name: Mickey Mouse
yes 
no 
Range: Go Rust C++ C# 
```

[Sadržaj][00]

## 54 Regular Expressions

Go offers built-in support for regular expressions. Here are some examples of common regexp-related tasks in Go.

```go
package main

import (
    "bytes"
    "fmt"
    "regexp"
)

func main() {

    // This tests whether a pattern matches a string.
    match, _ := regexp.MatchString("p([a-z]+)ch", "peach")
    fmt.Println(match)

    // Above we used a string pattern directly, but for other regexp tasks you’ll need to Compile 
    // an optimized Regexp struct.
    r, _ := regexp.Compile("p([a-z]+)ch")

    // Many methods are available on these structs. Here’s a match test like we saw earlier.
    fmt.Println(r.MatchString("peach"))

    // This finds the match for the regexp.
    fmt.Println(r.FindString("peach punch"))

    // This also finds the first match but returns the start and end indexes for the match instead 
    // of the matching text.
    fmt.Println("idx:", r.FindStringIndex("peach punch"))

    // The Submatch variants include information about both the whole-pattern matches and the 
    // submatches within those matches. For example this will return information for both p([a-z]+)
    // ch and ([a-z]+).
    fmt.Println(r.FindStringSubmatch("peach punch"))

    // Similarly this will return information about the indexes of matches and submatches.
    fmt.Println(r.FindStringSubmatchIndex("peach punch"))

    // The All variants of these functions apply to all matches in the input, not just the first. 
    // For example to find all matches for a regexp.
    fmt.Println(r.FindAllString("peach punch pinch", -1))

    // These All variants are available for the other functions we saw above as well.
    fmt.Println("all:", r.FindAllStringSubmatchIndex(
        "peach punch pinch", -1))

    // Providing a non-negative integer as the second argument to these functions will limit the 
    // number of matches.
    fmt.Println(r.FindAllString("peach punch pinch", 2))

    // Our examples above had string arguments and used names like MatchString. We can also provide 
    // []byte arguments and drop String from the function name.
    fmt.Println(r.Match([]byte("peach")))

    // When creating global variables with regular expressions you can use the MustCompile 
    // variation of Compile. MustCompile panics instead of returning an error, which makes it safer 
    // to use for global variables.
    r = regexp.MustCompile("p([a-z]+)ch")
    fmt.Println("regexp:", r)

    // The regexp package can also be used to replace subsets of strings with other values.
    fmt.Println(r.ReplaceAllString("a peach", "<fruit>"))

    // The Func variant allows you to transform matched text with a given function.
    in := []byte("a peach")
    out := r.ReplaceAllFunc(in, bytes.ToUpper)
    fmt.Println(string(out))
}
```

```sh
go run regular-expressions.go
true
true
peach
idx: [0 5]
[peach ea]
[0 5 1 3]
[peach punch pinch]
all: [[0 5 1 3] [6 11 7 9] [12 17 13 15]]
[peach punch]
true
regexp: p([a-z]+)ch
a <fruit>
a PEACH
```

For a complete reference on Go regular expressions check the regexp package docs.

[Sadržaj][00]

## 55 JSON

Go offers built-in support for JSON encoding and decoding, including to and from built-in and custom data types.

```go
package main

import (
    "encoding/json"
    "fmt"
    "os"
    "strings"
)

// We’ll use these two structs to demonstrate encoding and decoding of custom types below.
type response1 struct {
    Page   int
    Fruits []string
}

// Only exported fields will be encoded/decoded in JSON. Fields must start with capital letters to 
// be exported.
type response2 struct {
    Page   int      `json:"page"`
    Fruits []string `json:"fruits"`
}

func main() {

    // First we’ll look at encoding basic data types to JSON strings. Here are some examples for 
    // atomic values.
    bolB, _ := json.Marshal(true)
    fmt.Println(string(bolB))

    intB, _ := json.Marshal(1)
    fmt.Println(string(intB))

    fltB, _ := json.Marshal(2.34)
    fmt.Println(string(fltB))

    strB, _ := json.Marshal("gopher")
    fmt.Println(string(strB))

    // And here are some for slices and maps, which encode to JSON arrays and objects as you’d 
    // expect.
    slcD := []string{"apple", "peach", "pear"}
    slcB, _ := json.Marshal(slcD)
    fmt.Println(string(slcB))

    mapD := map[string]int{"apple": 5, "lettuce": 7}
    mapB, _ := json.Marshal(mapD)
    fmt.Println(string(mapB))

    // The JSON package can automatically encode your custom data types. It will only include 
    // exported fields in the encoded output and will by default use those names as the JSON keys.
    res1D := &response1{
        Page:   1,
        Fruits: []string{"apple", "peach", "pear"}}
    res1B, _ := json.Marshal(res1D)
    fmt.Println(string(res1B))

    // You can use tags on struct field declarations to customize the encoded JSON key names. Check 
    // the definition of response2 above to see an example of such tags.
    res2D := &response2{
        Page:   1,
        Fruits: []string{"apple", "peach", "pear"}}
    res2B, _ := json.Marshal(res2D)
    fmt.Println(string(res2B))

    // Now let’s look at decoding JSON data into Go values. Here’s an example for a generic data 
    // structure.
    byt := []byte(`{"num":6.13,"strs":["a","b"]}`)

    // We need to provide a variable where the JSON package can put the decoded data. This map
    // [string]interface{} will hold a map of strings to arbitrary data types.
    var dat map[string]interface{}

    // Here’s the actual decoding, and a check for associated errors.
    if err := json.Unmarshal(byt, &dat); err != nil {
        panic(err)
    }
    fmt.Println(dat)

    // In order to use the values in the decoded map, we’ll need to convert them to their 
    // appropriate type. For example here we convert the value in num to the expected float64 type.
    num := dat["num"].(float64)
    fmt.Println(num)

    // Accessing nested data requires a series of conversions.
    strs := dat["strs"].([]interface{})
    str1 := strs[0].(string)
    fmt.Println(str1)

    // We can also decode JSON into custom data types. This has the advantages of adding additional 
    // type-safety to our programs and eliminating the need for type assertions when accessing the 
    // decoded data.
    str := `{"page": 1, "fruits": ["apple", "peach"]}`
    res := response2{}
    json.Unmarshal([]byte(str), &res)
    fmt.Println(res)
    fmt.Println(res.Fruits[0])

    // In the examples above we always used bytes and strings as intermediates between the data and 
    // JSON representation on standard out. We can also stream JSON encodings directly to os.
    // Writers like os.Stdout or even HTTP response bodies.
    enc := json.NewEncoder(os.Stdout)
    d := map[string]int{"apple": 5, "lettuce": 7}
    enc.Encode(d)

    // Streaming reads from os.Readers like os.Stdin or HTTP request bodies is done with json.
    // Decoder.
    dec := json.NewDecoder(strings.NewReader(str))
    res1 := response2{}
    dec.Decode(&res1)
    fmt.Println(res1)
}
```

```sh
go run json.go
true
1
2.34
"gopher"
["apple","peach","pear"]
{"apple":5,"lettuce":7}
{"Page":1,"Fruits":["apple","peach","pear"]}
{"page":1,"fruits":["apple","peach","pear"]}
map[num:6.13 strs:[a b]]
6.13
a
{1 [apple peach]}
apple
{"apple":5,"lettuce":7}
{1 [apple peach]}
```

We’ve covered the basic of JSON in Go here, but check out the JSON and Go blog post and JSON package docs for more.

[Sadržaj][00]

## 56 XML

Go offers built-in support for XML and XML-like formats with the encoding/xml package.

```go
package main

import (
    "encoding/xml"
    "fmt"
)

// Plant will be mapped to XML. Similarly to the JSON examples, field tags contain directives for 
// the encoder and decoder. Here we use some special features of the XML package: the XMLName field 
// name dictates the name of the XML element representing this struct; id,attr means that the Id 
// field is an XML attribute rather than a nested element.
type Plant struct {
    XMLName xml.Name `xml:"plant"`
    Id      int      `xml:"id,attr"`
    Name    string   `xml:"name"`
    Origin  []string `xml:"origin"`
}

func (p Plant) String() string {
    return fmt.Sprintf("Plant id=%v, name=%v, origin=%v",
        p.Id, p.Name, p.Origin)
}

func main() {
    coffee := &Plant{Id: 27, Name: "Coffee"}
    coffee.Origin = []string{"Ethiopia", "Brazil"}

    // Emit XML representing our plant; using MarshalIndent to produce a more human-readable output.
    out, _ := xml.MarshalIndent(coffee, " ", "  ")
    fmt.Println(string(out))

    // To add a generic XML header to the output, append it explicitly.
    fmt.Println(xml.Header + string(out))

    // Use Unmarshal to parse a stream of bytes with XML into a data structure. If the XML is 
    // malformed or cannot be mapped onto Plant, a descriptive error will be returned.
    var p Plant
    if err := xml.Unmarshal(out, &p); err != nil {
        panic(err)
    }
    fmt.Println(p)

    tomato := &Plant{Id: 81, Name: "Tomato"}
    tomato.Origin = []string{"Mexico", "California"}

    // The parent>child>plant field tag tells the encoder to nest all plants under 
    // <parent><child>...
    type Nesting struct {
        XMLName xml.Name `xml:"nesting"`
        Plants  []*Plant `xml:"parent>child>plant"`
    }

    nesting := &Nesting{}
    nesting.Plants = []*Plant{coffee, tomato}

    out, _ = xml.MarshalIndent(nesting, " ", "  ")
    fmt.Println(string(out))
}
```

```sh
go run xml.go
 <plant id="27">
   <name>Coffee</name>
   <origin>Ethiopia</origin>
   <origin>Brazil</origin>
 </plant>
<?xml version="1.0" encoding="UTF-8"?>
 <plant id="27">
   <name>Coffee</name>
   <origin>Ethiopia</origin>
   <origin>Brazil</origin>
 </plant>
Plant id=27, name=Coffee, origin=[Ethiopia Brazil]
 <nesting>
   <parent>
     <child>
       <plant id="27">
         <name>Coffee</name>
         <origin>Ethiopia</origin>
         <origin>Brazil</origin>
       </plant>
       <plant id="81">
         <name>Tomato</name>
         <origin>Mexico</origin>
         <origin>California</origin>
       </plant>
     </child>
   </parent>
 </nesting>
```

[Sadržaj][00]

## 57 Time

Go offers extensive support for times and durations; here are some examples.

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    p := fmt.Println

    // We’ll start by getting the current time.
    now := time.Now()
    p(now)

    // You can build a time struct by providing the year, month, day, etc. Times are always 
    // associated with a Location, i.e. time zone.
    then := time.Date(
        2009, 11, 17, 20, 34, 58, 651387237, time.UTC)
    p(then)

    // You can extract the various components of the time value as expected.
    p(then.Year())
    p(then.Month())
    p(then.Day())
    p(then.Hour())
    p(then.Minute())
    p(then.Second())
    p(then.Nanosecond())
    p(then.Location())

    // The Monday-Sunday Weekday is also available.
    p(then.Weekday())

    // These methods compare two times, testing if the first occurs before, after, or at the same 
    // time as the second, respectively.
    p(then.Before(now))
    p(then.After(now))
    p(then.Equal(now))

    // The Sub methods returns a Duration representing the interval between two times.
    diff := now.Sub(then)
    p(diff)

    // We can compute the length of the duration in various units.
    p(diff.Hours())
    p(diff.Minutes())
    p(diff.Seconds())
    p(diff.Nanoseconds())

    // You can use Add to advance a time by a given duration, or with a - to move backwards by a 
    // duration.
    p(then.Add(diff))
    p(then.Add(-diff))
}
```

```sh
go run time.go
2012-10-31 15:50:13.793654 +0000 UTC
2009-11-17 20:34:58.651387237 +0000 UTC
2009
November
17
20
34
58
651387237
UTC
Tuesday
true
false
false
25891h15m15.142266763s
25891.25420618521
1.5534752523711128e+06
9.320851514226677e+07
93208515142266763
2012-10-31 15:50:13.793654 +0000 UTC
2006-12-05 01:19:43.509120474 +0000 UTC
```

[Sadržaj][00]

## 58 Epoch

A common requirement in programs is getting the number of seconds, milliseconds, or nanoseconds since the Unix epoch. Here’s how to do it in Go.

```go
package main

import (
    "fmt"
    "time"
)

func main() {

    // Use time.Now with Unix, UnixMilli or UnixNano to get elapsed time since the Unix epoch in 
    // seconds, milliseconds or nanoseconds, respectively.
    now := time.Now()
    fmt.Println(now)

    fmt.Println(now.Unix())
    fmt.Println(now.UnixMilli())
    fmt.Println(now.UnixNano())

    // You can also convert integer seconds or nanoseconds since the epoch into the corresponding 
    // time.
    fmt.Println(time.Unix(now.Unix(), 0))
    fmt.Println(time.Unix(0, now.UnixNano()))
}
```

```sh
go run epoch.go 
2012-10-31 16:13:58.292387 +0000 UTC
1351700038
1351700038292
1351700038292387000
2012-10-31 16:13:58 +0000 UTC
2012-10-31 16:13:58.292387 +0000 UTC
```

[Sadržaj][00]

## 59 Time Formatting / Parsing

Go supports time formatting and parsing via pattern-based layouts.

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    p := fmt.Println

    // Here’s a basic example of formatting a time according to RFC3339, using the corresponding 
    // layout constant.
    t := time.Now()
    p(t.Format(time.RFC3339))

    // Time parsing uses the same layout values as Format.
    t1, _ := time.Parse(time.RFC3339, "2012-11-01T22:08:41+00:00")
    p(t1)

    // Format and Parse use example-based layouts. Usually you’ll use a constant from time for 
    // these layouts, but you can also supply custom layouts. Layouts must use the reference time 
    // Mon Jan 2 15:04:05 MST 2006 to show the pattern with which to format/parse a given time/
    // string. The example time must be exactly as shown: the year 2006, 15 for the hour, Monday 
    // for the day of the week, etc.
    p(t.Format("3:04PM"))
    p(t.Format("Mon Jan _2 15:04:05 2006"))
    p(t.Format("2006-01-02T15:04:05.999999-07:00"))
    form := "3 04 PM"
    t2, _ := time.Parse(form, "8 41 PM")
    p(t2)

    // For purely numeric representations you can also use standard string formatting with the 
    // extracted components of the time value.
    fmt.Printf("%d-%02d-%02dT%02d:%02d:%02d-00:00\n",
        t.Year(), t.Month(), t.Day(),
        t.Hour(), t.Minute(), t.Second())

    // Parse will return an error on malformed input explaining the parsing problem.
    _, err := time.Parse("Mon Jan _2 15:04:05 2006", "8:41PM")
    p(err)
}
```

```sh
go run time-formatting-parsing.go 
2014-04-15T18:00:15-07:00
2012-11-01 22:08:41 +0000 +0000
6:00PM
Tue Apr 15 18:00:15 2014
2014-04-15T18:00:15.161182-07:00
0000-01-01 20:41:00 +0000 UTC
2014-04-15T18:00:15-00:00
parsing time "8:41PM" as "Mon Jan _2 15:04:05 2006": ...
```

[Sadržaj][00]

## 60 Random Numbers

Go’s math/rand/v2 package provides pseudorandom number generation.

```go
package main

import (
    "fmt"
    "math/rand/v2"
)

func main() {

    // For example, rand.IntN returns a random int n, 0 <= n < 100.
    fmt.Print(rand.IntN(100), ",")
    fmt.Print(rand.IntN(100))
    fmt.Println()

    // rand.Float64 returns a float64 f, 0.0 <= f < 1.0.
    fmt.Println(rand.Float64())

    // This can be used to generate random floats in other ranges, for example 5.0 <= f' < 10.0.
    fmt.Print((rand.Float64()*5)+5, ",")
    fmt.Print((rand.Float64() * 5) + 5)
    fmt.Println()

    // If you want a known seed, create a new rand.Source and pass it into the New constructor. 
    // NewPCG creates a new PCG source that requires a seed of two uint64 numbers.
    s2 := rand.NewPCG(42, 1024)
    r2 := rand.New(s2)
    fmt.Print(r2.IntN(100), ",")
    fmt.Print(r2.IntN(100))
    fmt.Println()

    s3 := rand.NewPCG(42, 1024)
    r3 := rand.New(s3)
    fmt.Print(r3.IntN(100), ",")
    fmt.Print(r3.IntN(100))
    fmt.Println()
}
```

Some of the generated numbers may be different when you run the sample.

```sh
go run random-numbers.go
68,56
0.8090228139659177
5.840125017402497,6.937056298890035
94,49
94,49
```

See the math/rand/v2 package docs for references on other random quantities that Go can provide.

[Sadržaj][00]

## 61 Number Parsing

Parsing numbers from strings is a basic but common task in many programs; here’s how to do it in Go.

```go
package main

// The built-in package strconv provides the number parsing.
import (
    "fmt"
    "strconv"
)

func main() {

    // With ParseFloat, this 64 tells how many bits of precision to parse.
    f, _ := strconv.ParseFloat("1.234", 64)
    fmt.Println(f)

    // For ParseInt, the 0 means infer the base from the string. 64 requires that the result fit in 
    // 64 bits.
    i, _ := strconv.ParseInt("123", 0, 64)
    fmt.Println(i)

    // ParseInt will recognize hex-formatted numbers.
    d, _ := strconv.ParseInt("0x1c8", 0, 64)
    fmt.Println(d)

    // A ParseUint is also available.
    u, _ := strconv.ParseUint("789", 0, 64)
    fmt.Println(u)

    // Atoi is a convenience function for basic base-10 int parsing.
    k, _ := strconv.Atoi("135")
    fmt.Println(k)

    // Parse functions return an error on bad input.
    _, e := strconv.Atoi("wat")
    fmt.Println(e)
}
```

```sh
go run number-parsing.go 
1.234
123
456
789
135
strconv.ParseInt: parsing "wat": invalid syntax
```

[Sadržaj][00]

## 62 URL Parsing

URLs provide a uniform way to locate resources. Here’s how to parse URLs in Go.

```go
package main

import (
    "fmt"
    "net"
    "net/url"
)

func main() {

    // We’ll parse this example URL, which includes a scheme, authentication info, host, port, 
    // path, query params, and query fragment.
    s := "postgres://user:pass@host.com:5432/path?k=v#f"

    // Parse the URL and ensure there are no errors.
    u, err := url.Parse(s)
    if err != nil {
        panic(err)
    }

    // Accessing the scheme is straightforward.
    fmt.Println(u.Scheme)

    // User contains all authentication info; call Username and Password on this for individual 
    // values.
    fmt.Println(u.User)
    fmt.Println(u.User.Username())
    p, _ := u.User.Password()
    fmt.Println(p)

    // The Host contains both the hostname and the port, if present. Use SplitHostPort to extract 
    // them.
    fmt.Println(u.Host)
    host, port, _ := net.SplitHostPort(u.Host)
    fmt.Println(host)
    fmt.Println(port)

    // Here we extract the path and the fragment after the #.
    fmt.Println(u.Path)
    fmt.Println(u.Fragment)

    // To get query params in a string of k=v format, use RawQuery. You can also parse query params 
    // into a map. The parsed query param maps are from strings to slices of strings, so index into 
    // [0] if you only want the first value.
    fmt.Println(u.RawQuery)
    m, _ := url.ParseQuery(u.RawQuery)
    fmt.Println(m)
    fmt.Println(m["k"][0])
}
```

Running our URL parsing program shows all the different pieces that we extracted.

```sh
go run url-parsing.go 
postgres
user:pass
user
pass
host.com:5432
host.com
5432
/path
f
k=v
map[k:[v]]
v
```

[Sadržaj][00]

## 63 SHA256 Hashes

SHA256 hashes are frequently used to compute short identities for binary or text blobs. For example, TLS/SSL certificates use SHA256 to compute a certificate’s signature. Here’s how to compute SHA256 hashes in Go.

```go
package main

// Go implements several hash functions in various crypto/* packages.

import (
    "crypto/sha256"
    "fmt"
)

func main() {
    s := "sha256 this string"

    // Here we start with a new hash.
    h := sha256.New()

    // Write expects bytes. If you have a string s, use []byte(s) to coerce it to bytes.
    h.Write([]byte(s))

    // This gets the finalized hash result as a byte slice. The argument to Sum can be used to 
    // append to an existing byte slice: it usually isn’t needed.
    bs := h.Sum(nil)

    fmt.Println(s)
    fmt.Printf("%x\n", bs)
}
```

Running the program computes the hash and prints it in a human-readable hex format.

```sh
go run sha256-hashes.go
sha256 this string
1af1dfa857bf1d8814fe1af8983c18080019922e557f15a8a...
```

You can compute other hashes using a similar pattern to the one shown above. For example, to compute SHA512 hashes import crypto/sha512 and use sha512.New().

Note that if you need cryptographically secure hashes, you should carefully research hash strength!

[Sadržaj][00]

## 64 Base64 Encoding

Go provides built-in support for base64 encoding/decoding.

```go
package main

// This syntax imports the encoding/base64 package with the b64 name instead of the default base64. 
// It’ll save us some space below.

import (
    b64 "encoding/base64"
    "fmt"
)

func main() {

    // Here’s the string we’ll encode/decode.
    data := "abc123!?$*&()'-=@~"

    // Go supports both standard and URL-compatible base64. Here’s how to encode using the standard 
    // encoder. The encoder requires a []byte so we convert our string to that type.
    sEnc := b64.StdEncoding.EncodeToString([]byte(data))
    fmt.Println(sEnc)

    // Decoding may return an error, which you can check if you don’t already know the input to be 
    // well-formed.
    sDec, _ := b64.StdEncoding.DecodeString(sEnc)
    fmt.Println(string(sDec))
    fmt.Println()

    // This encodes/decodes using a URL-compatible base64 format.
    uEnc := b64.URLEncoding.EncodeToString([]byte(data))
    fmt.Println(uEnc)
    uDec, _ := b64.URLEncoding.DecodeString(uEnc)
    fmt.Println(string(uDec))
}
```

The string encodes to slightly different values with the standard and URL base64 encoders (trailing + vs -) but they both decode to the original string as desired.

```sh
go run base64-encoding.go
YWJjMTIzIT8kKiYoKSctPUB+
abc123!?$*&()'-=@~
YWJjMTIzIT8kKiYoKSctPUB-
abc123!?$*&()'-=@~
```

[Sadržaj][00]

## 65 Reading Files

Reading and writing files are basic tasks needed for many Go programs. First we’ll look at some examples of reading files.

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "path/filepath"
)

// Reading files requires checking most calls for errors. This helper will streamline our error 
// checks below.
func check(e error) {
    if e != nil {
        panic(e)
    }
}

func main() {
    
    // Perhaps the most basic file reading task is slurping a file’s entire contents into memory.
    path := filepath.Join(os.TempDir(), "dat")
    dat, err := os.ReadFile(path)
    check(err)
    fmt.Print(string(dat))

    // You’ll often want more control over how and what parts of a file are read. For these tasks, 
    // start by Opening a file to obtain an os.File value.
    f, err := os.Open(path)
    check(err)

    // Read some bytes from the beginning of the file. Allow up to 5 to be read but also note how 
    // many actually were read.
    b1 := make([]byte, 5)
    n1, err := f.Read(b1)
    check(err)
    fmt.Printf("%d bytes: %s\n", n1, string(b1[:n1]))

    // You can also Seek to a known location in the file and Read from there.
    o2, err := f.Seek(6, io.SeekStart)
    check(err)
    b2 := make([]byte, 2)
    n2, err := f.Read(b2)
    check(err)
    fmt.Printf("%d bytes @ %d: ", n2, o2)
    fmt.Printf("%v\n", string(b2[:n2]))

    // Other methods of seeking are relative to the current cursor position,
    _, err = f.Seek(2, io.SeekCurrent)
    check(err)

    // and relative to the end of the file.
    _, err = f.Seek(-4, io.SeekEnd)
    check(err)

    // The io package provides some functions that may be helpful for file reading. For example, 
    // reads like the ones above can be more robustly implemented with ReadAtLeast.
    o3, err := f.Seek(6, io.SeekStart)
    check(err)
    b3 := make([]byte, 2)
    n3, err := io.ReadAtLeast(f, b3, 2)
    check(err)
    fmt.Printf("%d bytes @ %d: %s\n", n3, o3, string(b3))

    // There is no built-in rewind, but Seek(0, io.SeekStart) accomplishes this.
    _, err = f.Seek(0, io.SeekStart)
    check(err)

    // The bufio package implements a buffered reader that may be useful both for its efficiency 
    // with many small reads and because of the additional reading methods it provides.
    r4 := bufio.NewReader(f)
    b4, err := r4.Peek(5)
    check(err)
    fmt.Printf("5 bytes: %s\n", string(b4))

    // Close the file when you’re done (usually this would be scheduled immediately after Opening 
    // with defer).
    f.Close()
}
```

```sh
echo "hello" > /tmp/dat
echo "go" >>   /tmp/dat
go run reading-files.go
hello
go
5 bytes: hello
2 bytes @ 6: go
2 bytes @ 6: go
5 bytes: hello
```

[sadržaj][00]

## 66 Writing Files

Writing files in Go follows similar patterns to the ones we saw earlier for reading.

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "path/filepath"
)

func check(e error) {
    if e != nil {
        panic(e)
    }
}

func main() {

    /// To start, here’s how to dump a string (or just bytes) into a file.
    d1 := []byte("hello\ngo\n")
    path1 := filepath.Join(os.TempDir(), "dat1")
    err := os.WriteFile(path1, d1, 0644)
    check(err)

    // For more granular writes, open a file for writing.
    path2 := filepath.Join(os.TempDir(), "dat2")
    f, err := os.Create(path2)
    check(err)

    // It’s idiomatic to defer a Close immediately after opening a file.
    defer f.Close()

    // You can Write byte slices as you’d expect.
    d2 := []byte{115, 111, 109, 101, 10}
    n2, err := f.Write(d2)
    check(err)
    fmt.Printf("wrote %d bytes\n", n2)

    // A WriteString is also available.
    n3, err := f.WriteString("writes\n")
    check(err)
    fmt.Printf("wrote %d bytes\n", n3)

    // Issue a Sync to flush writes to stable storage.
    f.Sync()

    // bufio provides buffered writers in addition to the buffered readers we saw earlier.
    w := bufio.NewWriter(f)
    n4, err := w.WriteString("buffered\n")
    check(err)
    fmt.Printf("wrote %d bytes\n", n4)

    // Use Flush to ensure all buffered operations have been applied to the underlying writer.
    w.Flush()
}
```

Try running the file-writing code.

```sh
go run writing-files.go 
wrote 5 bytes
wrote 7 bytes
wrote 9 bytes
```

Then check the contents of the written files.

```sh
cat /tmp/dat1
hello
go
cat /tmp/dat2
some
writes
buffered
```

[Sadržaj][00]

## 67 Line Filters

A line filter is a common type of program that reads input on stdin, processes it, and then prints some derived result to stdout. grep and sed are common line filters.

Here’s an example line filter in Go that writes a capitalized version of all input text. You can use this pattern to write your own Go line filters.

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
)

func main() {

    // Wrapping the unbuffered os.Stdin with a buffered scanner gives us a convenient Scan method 
    // that advances the scanner to the next token; which is the next line in the default scanner.
    scanner := bufio.NewScanner(os.Stdin)

    // Text returns the current token, here the next line, from the input.
    for scanner.Scan() {
        ucl := strings.ToUpper(scanner.Text())

        // Write out the uppercased line.
        fmt.Println(ucl)
    }

    // Check for errors during Scan. End of file is expected and not reported by Scan as an error.
    if err := scanner.Err(); err != nil {
        fmt.Fprintln(os.Stderr, "error:", err)
        os.Exit(1)
    }
}
```

To try out our line filter, first make a file with a few lowercase lines.

```sh
echo 'hello'   > /tmp/lines
echo 'filter' >> /tmp/lines
```

Then use the line filter to get uppercase lines.

```sh
cat /tmp/lines | go run line-filters.go
HELLO
FILTER
```

[Sadržaj][00]

## 68 File Paths

The filepath package provides functions to parse and construct file paths in a way that is portable between operating systems; dir/file on Linux vs. dir\file on Windows, for example.

```go
package main

import (
    "fmt"
    "path/filepath"
    "strings"
)

func main() {

    // Join should be used to construct paths in a portable way. It takes any number of arguments 
    // and constructs a hierarchical path from them.
    p := filepath.Join("dir1", "dir2", "filename")
    fmt.Println("p:", p)

    // You should always use Join instead of concatenating /s or \s manually. In addition to 
    // providing portability, Join will also normalize paths by removing superfluous separators and 
    // directory changes.
    fmt.Println(filepath.Join("dir1//", "filename"))
    fmt.Println(filepath.Join("dir1/../dir1", "filename"))

    // Dir and Base can be used to split a path to the directory and the file. Alternatively, Split 
    // will return both in the same call.
    fmt.Println("Dir(p):", filepath.Dir(p))
    fmt.Println("Base(p):", filepath.Base(p))

    // We can check whether a path is absolute.
    fmt.Println(filepath.IsAbs("dir/file"))
    fmt.Println(filepath.IsAbs("/dir/file"))

    filename := "config.json"

    // Some file names have extensions following a dot. We can split the extension out of such 
    // names with Ext.
    ext := filepath.Ext(filename)
    fmt.Println(ext)

    // To find the file’s name with the extension removed, use strings.TrimSuffix.
    fmt.Println(strings.TrimSuffix(filename, ext))

    // Rel finds a relative path between a base and a target. It returns an error if the target 
    // cannot be made relative to base.
    rel, err := filepath.Rel("a/b", "a/b/t/file")
    if err != nil {
        panic(err)
    }
    fmt.Println(rel)

    rel, err = filepath.Rel("a/b", "a/c/t/file")
    if err != nil {
        panic(err)
    }
    fmt.Println(rel)
}
```

```sh
go run file-paths.go
p: dir1/dir2/filename
dir1/filename
dir1/filename
Dir(p): dir1/dir2
Base(p): filename
false
true
.json
config
t/file
../c/t/file
```

[Sadržaj][00]

## 69 Directories

Go has several useful functions for working with directories in the file system.

```go
package main

import (
    "fmt"
    "io/fs"
    "os"
    "path/filepath"
)

func check(e error) {
    if e != nil {
        panic(e)
    }
}

func main() {

    // Create a new sub-directory in the current working directory.
    err := os.Mkdir("subdir", 0755)
    check(err)

    // When creating temporary directories, it’s good practice to defer their removal. os.RemoveAll 
    // will delete a whole directory tree (similarly to rm -rf).
    defer os.RemoveAll("subdir")

    // Helper function to create a new empty file.
    createEmptyFile := func(name string) {
        d := []byte("")
        check(os.WriteFile(name, d, 0644))
    }

    createEmptyFile("subdir/file1")

    // We can create a hierarchy of directories, including parents with MkdirAll. This is similar 
    // to the command-line mkdir -p.
    err = os.MkdirAll("subdir/parent/child", 0755)
    check(err)

    createEmptyFile("subdir/parent/file2")
    createEmptyFile("subdir/parent/file3")
    createEmptyFile("subdir/parent/child/file4")

    // ReadDir lists directory contents, returning a slice of os.DirEntry objects.
    c, err := os.ReadDir("subdir/parent")
    check(err)

    fmt.Println("Listing subdir/parent")
    for _, entry := range c {
        fmt.Println(" ", entry.Name(), entry.IsDir())
    }

    // Chdir lets us change the current working directory, similarly to cd.
    err = os.Chdir("subdir/parent/child")
    check(err)

    // Now we’ll see the contents of subdir/parent/child when listing the current directory.
    c, err = os.ReadDir(".")
    check(err)

    fmt.Println("Listing subdir/parent/child")
    for _, entry := range c {
        fmt.Println(" ", entry.Name(), entry.IsDir())
    }

    // cd back to where we started.
    err = os.Chdir("../../..")
    check(err)

    // We can also visit a directory recursively, including all its sub-directories. WalkDir 
    // accepts a callback function to handle every file or directory visited.
    fmt.Println("Visiting subdir")
    err = filepath.WalkDir("subdir", visit)
}

// visit is called for every file or directory found recursively by filepath.WalkDir.

func visit(path string, d fs.DirEntry, err error) error {
    if err != nil {
        return err
    }
    fmt.Println(" ", path, d.IsDir())
    return nil
}
```

```sh
go run directories.go
Listing subdir/parent
  child true
  file2 false
  file3 false
Listing subdir/parent/child
  file4 false
Visiting subdir
  subdir true
  subdir/file1 false
  subdir/parent true
  subdir/parent/child true
  subdir/parent/child/file4 false
  subdir/parent/file2 false
  subdir/parent/file3 false
```

[Sadržaj][00]

## 70 Temporary Files and Directories

Throughout program execution, we often want to create data that isn’t needed after the program exits. Temporary files and directories are useful for this purpose since they don’t pollute the file system over time.

```go
package main

import (
    "fmt"
    "os"
    "path/filepath"
)

func check(e error) {
    if e != nil {
        panic(e)
    }
}

func main() {

    // The easiest way to create a temporary file is by calling os.CreateTemp. It creates a file 
    // and opens it for reading and writing. We provide "" as the first argument, so os.CreateTemp 
    // will create the file in the default location for our OS.
    f, err := os.CreateTemp("", "sample")
    check(err)

    // Display the name of the temporary file. On Unix-based OSes the directory will likely be /
    // tmp. The file name starts with the prefix given as the second argument to os.CreateTemp and 
    // the rest is chosen automatically to ensure that concurrent calls will always create 
    // different file names.
    fmt.Println("Temp file name:", f.Name())

    // Clean up the file after we’re done. The OS is likely to clean up temporary files by itself 
    // after some time, but it’s good practice to do this explicitly.
    defer os.Remove(f.Name())

    // We can write some data to the file.
    _, err = f.Write([]byte{1, 2, 3, 4})
    check(err)

    // If we intend to write many temporary files, we may prefer to create a temporary directory. 
    // os.MkdirTemp’s arguments are the same as CreateTemp’s, but it returns a directory name 
    // rather than an open file.
    dname, err := os.MkdirTemp("", "sampledir")
    check(err)
    fmt.Println("Temp dir name:", dname)

    defer os.RemoveAll(dname)

    // Now we can synthesize temporary file names by prefixing them with our temporary directory.
    fname := filepath.Join(dname, "file1")
    err = os.WriteFile(fname, []byte{1, 2}, 0666)
    check(err)
}
```

```sh
go run temporary-files-and-directories.go
Temp file name: /tmp/sample610887201
Temp dir name: /tmp/sampledir898854668
```

[Sadržaj][00]

## 71 Embed Directive

//go:embed is a compiler directive that allows programs to include arbitrary files and folders in the Go binary at build time. Read more about the embed directive here.

```go
package main

// Import the embed package; if you don’t use any exported identifiers from this package, you can 
// do a blank import with _ "embed".

import (
    "embed"
)

// embed directives accept paths relative to the directory containing the Go source file. This 
// directive embeds the contents of the file into the string variable immediately following it.

//go:embed folder/single_file.txt
var fileString string

// Or embed the contents of the file into a []byte.

//go:embed folder/single_file.txt
var fileByte []byte

// We can also embed multiple files or even folders with wildcards. This uses a variable of the 
// embed.FS type, which implements a simple virtual file system.

//go:embed folder/single_file.txt
//go:embed folder/*.hash
var folder embed.FS

func main() {

    // Print out the contents of single_file.txt.
    print(fileString)
    print(string(fileByte))

    // Retrieve some files from the embedded folder.
    content1, _ := folder.ReadFile("folder/file1.hash")
    print(string(content1))

    content2, _ := folder.ReadFile("folder/file2.hash")
    print(string(content2))
}
```

Use these commands to run the example. (Note: due to limitation on go playground, this example can only be run on your local machine.)

```sh
mkdir -p folder
echo "hello go" > folder/single_file.txt
echo "123" > folder/file1.hash
echo "456" > folder/file2.hash
```

```sh
go run embed-directive.go
hello go
hello go
123
456
```

[Sadržaj][00]

## 72 Testing and Benchmarking

Unit testing is an important part of writing principled Go programs. The testing package provides the tools we need to write unit tests and the go test command runs tests.

For the sake of demonstration, this code is in package main, but it could be any package. Testing code typically lives in the same package as the code it tests.

```go
package main

import (
    "fmt"
    "testing"
)

// We’ll be testing this simple implementation of an integer minimum. Typically, the code we’re 
// testing would be in a source file named something like intutils.go, and the test file for it 
// would then be named intutils_test.go.
func IntMin(a, b int) int {
    if a < b {
        return a
    }
    return b
}

// A test is created by writing a function with a name beginning with Test.
func TestIntMinBasic(t *testing.T) {
    ans := IntMin(2, -2)
    if ans != -2 {

        // t.Error* will report test failures but continue executing the test. t.Fatal* will report 
        // test failures and stop the test immediately.
        t.Errorf("IntMin(2, -2) = %d; want -2", ans)
    }
}

// Writing tests can be repetitive, so it’s idiomatic to use a table-driven style, where test 
// inputs and expected outputs are listed in a table and a single loop walks over them and performs 
// the test logic.
func TestIntMinTableDriven(t *testing.T) {
    var tests = []struct {
        a, b int
        want int
    }{
        {0, 1, 0},
        {1, 0, 0},
        {2, -2, -2},
        {0, -1, -1},
        {-1, 0, -1},
    }

    // t.Run enables running “subtests”, one for each table entry. These are shown separately when 
    // executing go test -v.
    for _, tt := range tests {
        testname := fmt.Sprintf("%d,%d", tt.a, tt.b)
        t.Run(testname, func(t *testing.T) {
            ans := IntMin(tt.a, tt.b)
            if ans != tt.want {
                t.Errorf("got %d, want %d", ans, tt.want)
            }
        })
    }
}

// Benchmark tests typically go in _test.go files and are named beginning with Benchmark. Any code
//  that’s required for the benchmark to run but should not be measured goes before this loop.

func BenchmarkIntMin(b *testing.B) {
    for b.Loop() {

        // The benchmark runner will automatically execute this loop body many times to determine a 
        // reasonable estimate of the run-time of a single iteration.
        IntMin(1, 2)
    }
}
```

Run all tests in the current project in verbose mode.

```sh
go test -v
== RUN   TestIntMinBasic
--- PASS: TestIntMinBasic (0.00s)
=== RUN   TestIntMinTableDriven
=== RUN   TestIntMinTableDriven/0,1
=== RUN   TestIntMinTableDriven/1,0
=== RUN   TestIntMinTableDriven/2,-2
=== RUN   TestIntMinTableDriven/0,-1
=== RUN   TestIntMinTableDriven/-1,0
--- PASS: TestIntMinTableDriven (0.00s)
    --- PASS: TestIntMinTableDriven/0,1 (0.00s)
    --- PASS: TestIntMinTableDriven/1,0 (0.00s)
    --- PASS: TestIntMinTableDriven/2,-2 (0.00s)
    --- PASS: TestIntMinTableDriven/0,-1 (0.00s)
    --- PASS: TestIntMinTableDriven/-1,0 (0.00s)
PASS
ok      examples/testing-and-benchmarking    0.023s
```

Run all benchmarks in the current project. All tests are run prior to benchmarks. The bench flag filters benchmark function names with a regexp.

```sh
go test -bench=.
goos: darwin
goarch: arm64
pkg: examples/testing
BenchmarkIntMin-8 1000000000 0.3136 ns/op
PASS
ok      examples/testing-and-benchmarking    0.351s
```

[Sadržaj][00]

## 73 Command-Line Arguments

Command-line arguments are a common way to parameterize execution of programs. For example, go run hello.go uses run and hello.go arguments to the go program.

```go
package main

import (
    "fmt"
    "os"
)

func main() {

    // os.Args provides access to raw command-line arguments. Note that the first value in this 
    // slice is the path to the program, and os.Args[1:] holds the arguments to the program.
    argsWithProg := os.Args
    argsWithoutProg := os.Args[1:]

    // You can get individual args with normal indexing.
    arg := os.Args[3]

    fmt.Println(argsWithProg)
    fmt.Println(argsWithoutProg)
    fmt.Println(arg)
}
```

To experiment with command-line arguments it’s best to build a binary with go build first.

```sh
go build command-line-arguments.go
./command-line-arguments a b c d
[./command-line-arguments a b c d]       
[a b c d]
c
```

[Sadržaj][00]

## 74 Command-Line Flags

Command-line flags are a common way to specify options for command-line programs. For example, in wc -l the -l is a command-line flag.

```go
package main

// Go provides a flag package supporting basic command-line flag parsing. We’ll use this package to 
// implement our example command-line program.

import (
    "flag"
    "fmt"
)

func main() {

    // Basic flag declarations are available for string, integer, and boolean options. Here we 
    // declare a string flag word with a default value "foo" and a short description. This flag.
    // String function returns a string pointer (not a string value); we’ll see how to use this 
    // pointer below.
    wordPtr := flag.String("word", "foo", "a string")

    // This declares numb and fork flags, using a similar approach to the word flag.
    numbPtr := flag.Int("numb", 42, "an int")
    forkPtr := flag.Bool("fork", false, "a bool")

    // It’s also possible to declare an option that uses an existing var declared elsewhere in the 
    // program. Note that we need to pass in a pointer to the flag declaration function.
    var svar string
    flag.StringVar(&svar, "svar", "bar", "a string var")

    // Once all flags are declared, call flag.Parse() to execute the command-line parsing.
    flag.Parse()

    // Here we’ll just dump out the parsed options and any trailing positional arguments. Note that 
    // we need to dereference the pointers with e.g. *wordPtr to get the actual option values.
    fmt.Println("word:", *wordPtr)
    fmt.Println("numb:", *numbPtr)
    fmt.Println("fork:", *forkPtr)
    fmt.Println("svar:", svar)
    fmt.Println("tail:", flag.Args())
}
```

To experiment with the command-line flags program it’s best to first compile it and then run the resulting binary directly.

```sh
go build command-line-flags.go
```

Try out the built program by first giving it values for all flags.

```sh
./command-line-flags -word=opt -numb=7 -fork -svar=flag
word: opt
numb: 7
fork: true
svar: flag
tail: []
```

Note that if you omit flags they automatically take their default values.

```sh
./command-line-flags -word=opt
word: opt
numb: 42
fork: false
svar: bar
tail: []
```

Trailing positional arguments can be provided after any flags.

```sh
./command-line-flags -word=opt a1 a2 a3
word: opt
...
tail: [a1 a2 a3]
```

Note that the flag package requires all flags to appear before positional arguments (otherwise the flags will be interpreted as positional arguments).

```sh
./command-line-flags -word=opt a1 a2 a3 -numb=7
word: opt
numb: 42
fork: false
svar: bar
tail: [a1 a2 a3 -numb=7]
```

Use -h or --help flags to get automatically generated help text for the command-line program.

```sh
./command-line-flags -h
Usage of ./command-line-flags:
  -fork=false: a bool
  -numb=42: an int
  -svar="bar": a string var
  -word="foo": a string
```

If you provide a flag that wasn’t specified to the flag package, the program will print an error message and show the help text again.

```sh
./command-line-flags -wat
flag provided but not defined: -wat
Usage of ./command-line-flags:
...
```

[Sadržaj][00]

## 75 Command-Line Subcommands

Some command-line tools, like the go tool or git have many subcommands, each with its own set of flags. For example, go build and go get are two different subcommands of the go tool. The flag package lets us easily define simple subcommands that have their own flags.

```go
package main

import (
    "flag"
    "fmt"
    "os"
)

func main() {

    // We declare a subcommand using the NewFlagSet function, and proceed to define new flags 
    // specific for this subcommand.
    fooCmd := flag.NewFlagSet("foo", flag.ExitOnError)
    fooEnable := fooCmd.Bool("enable", false, "enable")
    fooName := fooCmd.String("name", "", "name")

    // For a different subcommand we can define different supported flags.
    barCmd := flag.NewFlagSet("bar", flag.ExitOnError)
    barLevel := barCmd.Int("level", 0, "level")

    // The subcommand is expected as the first argument to the program.
    if len(os.Args) < 2 {
        fmt.Println("expected 'foo' or 'bar' subcommands")
        os.Exit(1)
    }

    // Check which subcommand is invoked.
    switch os.Args[1] {

    // For every subcommand, we parse its own flags and have access to trailing positional 
    // arguments.
    case "foo":
        fooCmd.Parse(os.Args[2:])
        fmt.Println("subcommand 'foo'")
        fmt.Println("  enable:", *fooEnable)
        fmt.Println("  name:", *fooName)
        fmt.Println("  tail:", fooCmd.Args())
    case "bar":
        barCmd.Parse(os.Args[2:])
        fmt.Println("subcommand 'bar'")
        fmt.Println("  level:", *barLevel)
        fmt.Println("  tail:", barCmd.Args())
    default:
        fmt.Println("expected 'foo' or 'bar' subcommands")
        os.Exit(1)
    }
}
```

```sh
go build command-line-subcommands.go 
```

First invoke the foo subcommand.

```sh
./command-line-subcommands foo -enable -name=joe a1 a2
subcommand 'foo'
  enable: true
  name: joe
  tail: [a1 a2]
```

Now try bar.

```sh
./command-line-subcommands bar -level 8 a1
subcommand 'bar'
  level: 8
  tail: [a1]
```

But bar won’t accept foo’s flags.

```sh
./command-line-subcommands bar -enable a1
flag provided but not defined: -enable
Usage of bar:
  -level int
        level
```

[Sadržaj][00]

## 76 Environment Variables

Environment variables are a universal mechanism for conveying configuration information to Unix programs. Let’s look at how to set, get, and list environment variables.

```go
package main

import (
    "fmt"
    "os"
    "strings"
)

func main() {

    // To set a key/value pair, use os.Setenv. To get a value for a key, use os.Getenv. This will 
    // return an empty string if the key isn’t present in the environment.
    os.Setenv("FOO", "1")
    fmt.Println("FOO:", os.Getenv("FOO"))
    fmt.Println("BAR:", os.Getenv("BAR"))

    // Use os.Environ to list all key/value pairs in the environment. This returns a slice of 
    // strings in the form KEY=value. You can strings.SplitN them to get the key and value. Here we 
    // print all the keys.
    fmt.Println()
    for _, e := range os.Environ() {
        pair := strings.SplitN(e, "=", 2)
        fmt.Println(pair[0])
    }
}
```

// Running the program shows that we pick up the value for FOO that we set in the program, but that
// BAR is empty.

```sh
go run environment-variables.go
FOO: 1
BAR: 
```

The list of keys in the environment will depend on your particular machine.

```sh
TERM_PROGRAM
PATH
SHELL
...
FOO
```

If we set BAR in the environment first, the running program picks that value up.

```sh
BAR=2 go run environment-variables.go
FOO: 1
BAR: 2
...
```

[Sadržaj][00]

## 77 Logging

The Go standard library provides straightforward tools for outputting logs from Go programs, with the log package for free-form output and the log/slog package for structured output.

```go
package main

import (
    "bytes"
    "fmt"
    "log"
    "os"

    "log/slog"
)

func main() {

    // Simply invoking functions like Println from the log package uses the standard logger, which 
    // is already pre-configured for reasonable logging output to os.Stderr. Additional methods 
    // like Fatal* or Panic* will exit the program after logging.
    log.Println("standard logger")

    // Loggers can be configured with flags to set their output format. By default, the standard 
    // logger has the log.Ldate and log.Ltime flags set, and these are collected in log.LstdFlags. 
    // We can change its flags to emit time with microsecond accuracy, for example.
    log.SetFlags(log.LstdFlags | log.Lmicroseconds)
    log.Println("with micro")

    // It also supports emitting the file name and line from which the log function is called.
    log.SetFlags(log.LstdFlags | log.Lshortfile)
    log.Println("with file/line")

    // It may be useful to create a custom logger and pass it around. When creating a new logger, 
    // we can set a prefix to distinguish its output from other loggers.
    mylog := log.New(os.Stdout, "my:", log.LstdFlags)
    mylog.Println("from mylog")

    // We can set the prefix on existing loggers (including the standard one) with the SetPrefix 
    // method.
    mylog.SetPrefix("ohmy:")
    mylog.Println("from mylog")

    // Loggers can have custom output targets; any io.Writer works.
    var buf bytes.Buffer
    buflog := log.New(&buf, "buf:", log.LstdFlags)

    // This call writes the log output into buf.
    buflog.Println("hello")

    // This will actually show it on standard output.
    fmt.Print("from buflog:", buf.String())

    // The slog package provides structured log output. For example, logging in JSON format is 
    // straightforward.
    jsonHandler := slog.NewJSONHandler(os.Stderr, nil)
    myslog := slog.New(jsonHandler)
    myslog.Info("hi there")

    // In addition to the message, slog output can contain an arbitrary number of key=value pairs.
    myslog.Info("hello again", "key", "val", "age", 25)
}
```

Sample output; the date and time emitted will depend on when the example ran.

```sh
go run logging.go
2023/08/22 10:45:16 standard logger
2023/08/22 10:45:16.904141 with micro
2023/08/22 10:45:16 logging.go:40: with file/line
my:2023/08/22 10:45:16 from mylog
ohmy:2023/08/22 10:45:16 from mylog
from buflog:buf:2023/08/22 10:45:16 hello
```

These are wrapped for clarity of presentation on the website; in reality they are emitted on a single line.

```sh
{"time":"2023-08-22T10:45:16.904166391-07:00",
 "level":"INFO","msg":"hi there"}
{"time":"2023-08-22T10:45:16.904178985-07:00",
    "level":"INFO","msg":"hello again",
    "key":"val","age":25}
```

[Sadržaj][00]

## 78 HTTP Client

The Go standard library comes with excellent support for HTTP clients and servers in the net/http package. In this example we’ll use it to issue simple HTTP requests.

```go
package main

import (
    "bufio"
    "fmt"
    "net/http"
)

func main() {

    // Issue an HTTP GET request to a server. http.Get is a convenient shortcut around creating an 
    // http.Client object and calling its Get method; it uses the http.DefaultClient object which 
    // has useful default settings.
    resp, err := http.Get("https://gobyexample.com")
    if err != nil {
        panic(err)
    }
    defer resp.Body.Close()

    // Print the HTTP response status.
    fmt.Println("Response status:", resp.Status)

    // Print the first 5 lines of the response body.
    scanner := bufio.NewScanner(resp.Body)
    for i := 0; scanner.Scan() && i < 5; i++ {
        fmt.Println(scanner.Text())
    }

    if err := scanner.Err(); err != nil {
        panic(err)
    }
}
```

```sh
go run http-clients.go
Response status: 200 OK
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Go by Example</title>
```

[Sadržaj][00]

## 79 HTTP Server

Writing a basic HTTP server is easy using the net/http package.

```go
package main

import (
    "fmt"
    "net/http"
)

// A fundamental concept in net/http servers is handlers. A handler is an object implementing the 
// http.Handler interface. A common way to write a handler is by using the http.HandlerFunc adapter 
// on functions with the appropriate signature.
func hello(w http.ResponseWriter, req *http.Request) {

    // Functions serving as handlers take a http.ResponseWriter and a http.Request as arguments. 
    // The response writer is used to fill in the HTTP response. Here our simple response is just 
    // “hello\n”.
    fmt.Fprintf(w, "hello\n")
}


func headers(w http.ResponseWriter, req *http.Request) {

// This handler does something a little more sophisticated by reading all the HTTP request 
    // headers and echoing them into the response body.
    for name, headers := range req.Header {
        for _, h := range headers {
            fmt.Fprintf(w, "%v: %v\n", name, h)
        }
    }
}

func main() {
    
    // We register our handlers on server routes using the http.HandleFunc convenience function. It 
    // sets up the default router in the net/http package and takes a function as an argument.
    http.HandleFunc("/hello", hello)
    http.HandleFunc("/headers", headers)

    // Finally, we call the ListenAndServe with the port and a handler. nil tells it to use the 
    // default router we’ve just set up.
    http.ListenAndServe(":8090", nil)
}
```

Run the server in the background.

```sh
go run http-server.go &
```

Access the /hello route.

```sh
curl localhost:8090/hello
hello
```

[Sadržaj][00]

## 80 TCP Server

The net package provides the tools we need to easily build TCP socket servers.

```go
package main

import (
    "bufio"
    "fmt"
    "log"
    "net"
    "strings"
)

func main() {

    // net.Listen starts the server on the given network (TCP) and address (port 8090 on all 
    // interfaces).
    listener, err := net.Listen("tcp", ":8090")
    if err != nil {
        log.Fatal("Error listening:", err)
    }

    // Close the listener to free the port when the application exits.
    defer listener.Close()

    // Loop indefinitely to accept new client connections.
    for {
        
        // Wait for a connection.
        conn, err := listener.Accept()
        if err != nil {
            log.Println("Error accepting conn:", err)
            continue
        }
        
        // We use a goroutine here to handle the connection so that the main loop can continue 
        // accepting more connections.
        go handleConnection(conn)
    }
}

// handleConnection handles a single client connection, reading one line of text from the client
// and returning a response.
func handleConnection(conn net.Conn) {

    // Closing the connection releases resources when we are finished interacting with the client.
    defer conn.Close()

    // Use bufio.NewReader to read one line of data from the client (terminated by a newline).
    reader := bufio.NewReader(conn)
    message, err := reader.ReadString('\n')
    if err != nil {
        log.Printf("Read error: %v", err)
        return
    }

    // Create and send a response back to the client, demonstrating two-way communication.
    ackMsg := strings.ToUpper(strings.TrimSpace(message))
    response := fmt.Sprintf("ACK: %s\n", ackMsg)
    _, err = conn.Write([]byte(response))
    if err != nil {
        log.Printf("Server write error: %v", err)
    }
}
```

Run the TCP server in the background.

```sh
go run tcp-server.go &
```

Send data and capture the response using netcat.

```sh
echo "Hello from netcat" | nc localhost 8090
ACK: HELLO FROM NETCAT
```

[Sadržaj][00]

## 81 Context

In the previous example we looked at setting up a simple HTTP server. HTTP servers are useful for demonstrating the usage of context.Context for controlling cancellation. A Context carries deadlines, cancellation signals, and other request-scoped values across API boundaries and goroutines.

```go
package main

import (
    "fmt"
    "net/http"
    "time"
)

func hello(w http.ResponseWriter, req *http.Request) {

    // A context.Context is created for each request by the net/http machinery, and is available
    // with the Context() method.
    ctx := req.Context()
    fmt.Println("server: hello handler started")
    defer fmt.Println("server: hello handler ended")

    // Wait for a few seconds before sending a reply to the client. This could simulate some work 
    // the server is doing. While working, keep an eye on the context’s Done() channel for a signal 
    // that we should cancel the work and return as soon as possible.
    select {
    case <-time.After(10 * time.Second):
        fmt.Fprintf(w, "hello\n")
    case <-ctx.Done():
        // The context’s Err() method returns an error that explains why the Done() channel was 
        // closed.
        err := ctx.Err()
        fmt.Println("server:", err)
        internalError := http.StatusInternalServerError
        http.Error(w, err.Error(), internalError)
    }
}

func main() {

    // As before, we register our handler on the “/hello” route, and start serving.
    http.HandleFunc("/hello", hello)
    http.ListenAndServe(":8090", nil)
}
```

Run the server in the background.

```sh
go run context.go &
```

Simulate a client request to /hello, hitting Ctrl+C shortly after starting to signal cancellation.

```sh
curl localhost:8090/hello
server: hello handler started
^C
server: context canceled
server: hello handler ended
```

[Sadržaj][00]

## 82 Spawning Processes

Sometimes our Go programs need to spawn other processes.

```go
package main

import (
    "errors"
    "fmt"
    "io"
    "os/exec"
)

func main() {

    // We’ll start with a simple command that takes no arguments or input and just prints something 
    // to stdout. The exec.Command helper creates an object to represent this external process.
    dateCmd := exec.Command("date")

    // The Output method runs the command, waits for it to finish and collects its standard output. 
    // If there were no errors, dateOut will hold bytes with the date info.
    dateOut, err := dateCmd.Output()
    if err != nil {
        panic(err)
    }
    fmt.Println("> date")
    fmt.Println(string(dateOut))

    // Output and other methods of Command will return *exec.Error if there was a problem executing 
    // the command (e.g. wrong path), and *exec.ExitError if the command ran but exited with a 
    // non-zero return code.
    _, err = exec.Command("date", "-x").Output()
    if err != nil {
        if e, ok := errors.AsType[*exec.Error](err); ok {
            fmt.Println("failed executing:", e)
        } else if e, ok := errors.AsType[*exec.ExitError](err); ok {
            exitCode := e.ExitCode()
            fmt.Println("command exit rc =", exitCode)
        } else {
            panic(err)
        }
    }

    // Next we’ll look at a slightly more involved case where we pipe data to the external process 
    // on its stdin and collect the results from its stdout.
    grepCmd := exec.Command("grep", "hello")

    // Here we explicitly grab input/output pipes, start the process, write some input to it, read 
    // the resulting output, and finally wait for the process to exit.
    grepIn, _ := grepCmd.StdinPipe()
    grepOut, _ := grepCmd.StdoutPipe()
    grepCmd.Start()
    grepIn.Write([]byte("hello grep\ngoodbye grep"))
    grepIn.Close()
    grepBytes, _ := io.ReadAll(grepOut)
    grepCmd.Wait()

    // We omitted error checks in the above example, but you could use the usual if err != nil 
    // pattern for all of them. We also only collect the StdoutPipe results, but you could collect 
    // the StderrPipe in exactly the same way.
    fmt.Println("> grep hello")
    fmt.Println(string(grepBytes))

    // Note that when spawning commands we need to provide an explicitly delineated command and 
    // argument array, vs. being able to just pass in one command-line string. If you want to spawn 
    // a full command with a string, you can use bash’s -c option:
    lsCmd := exec.Command("bash", "-c", "ls -a -l -h")
    lsOut, err := lsCmd.Output()
    if err != nil {
        panic(err)
    }
    fmt.Println("> ls -a -l -h")
    fmt.Println(string(lsOut))
}
```

The spawned programs return output that is the same as if we had run them directly from the command-line.

```sh
go run spawning-processes.go 
> date
Thu 05 May 2022 10:10:12 PM PDT

date doesn’t have a -x flag so it will exit with an error message and non-zero return code.

command exit rc = 1
> grep hello
hello grep

> ls -a -l -h
drwxr-xr-x  4 mark 136B Oct 3 16:29 .
drwxr-xr-x 91 mark 3.0K Oct 3 12:50 ..
-rw-r--r--  1 mark 1.3K Oct 3 16:28 spawning-processes.go
```

[Sadržaj][00]

## 83 Exec'ing Processes

In the previous example we looked at spawning external processes. We do this when we need an external process accessible to a running Go process. Sometimes we just want to completely replace the current Go process with another (perhaps non-Go) one. To do this we’ll use Go’s implementation of the classic exec function.

```go
package main

import (
    "os"
    "os/exec"
    "syscall"
)

func main() {

    // For our example we’ll exec ls. Go requires an absolute path to the binary we want to 
    // execute, so we’ll use exec.LookPath to find it (probably /bin/ls).
    binary, lookErr := exec.LookPath("ls")
    if lookErr != nil {
        panic(lookErr)
    }

    // Exec requires arguments in slice form (as opposed to one big string). We’ll give ls a few 
    // common arguments. Note that the first argument should be the program name.
    args := []string{"ls", "-a", "-l", "-h"}

    // Exec also needs a set of environment variables to use. Here we just provide our current 
    // environment.
    env := os.Environ()

    // Here’s the actual syscall.Exec call. If this call is successful, the execution of our 
    // process will end here and be replaced by the /bin/ls -a -l -h process. If there is an error 
    // we’ll get a return value.
    execErr := syscall.Exec(binary, args, env)
    if execErr != nil {
        panic(execErr)
    }
}
```

When we run our program it is replaced by ls.

```sh
go run execing-processes.go
total 16
drwxr-xr-x  4 mark 136B Oct 3 16:29 .
drwxr-xr-x 91 mark 3.0K Oct 3 12:50 ..
-rw-r--r--  1 mark 1.3K Oct 3 16:28 execing-processes.go
```

Note that Go does not offer a classic Unix fork function. Usually this isn’t an issue though, since starting goroutines, spawning processes, and exec’ing processes covers most use cases for fork.

[Sadržaj][00]

## 84 Signals

Sometimes we’d like our Go programs to intelligently handle Unix signals. For example, we might want a server to gracefully shutdown when it receives a SIGTERM, or a command-line tool to stop processing input if it receives a SIGINT. Here’s a modern way to handle signals using contexts.

```go
package main

import (
    "context"
    "fmt"
    "os/signal"
    "syscall"
)

// signal.NotifyContext returns a context that’s canceled when one of the listed signals arrives.

func main() {

    ctx, stop := signal.NotifyContext(
        context.Background(), syscall.SIGINT, syscall.SIGTERM)
    defer stop()

    // The program will wait here until one of the configured signals is received.
    fmt.Println("awaiting signal")
    <-ctx.Done()

    // context.Cause reports why the context was canceled. For a signal-triggered cancellation, 
    // this includes the signal value.
    fmt.Println()
    fmt.Println(context.Cause(ctx))
    fmt.Println("exiting")
}
```

When we run this program it will block waiting for a signal. By typing ctrl-C (which the terminal shows as ^C) we can send a SIGINT signal, causing the program to print the cancellation cause and then exit.

```sh
go run signals.go
awaiting signal
^C
interrupt signal received
exiting
```

[Sadržaj][00]

## 85 Exit

Use os.Exit to immediately exit with a given status.

```go
package main

import (
    "fmt"
    "os"
)

func main() {

    // defers will not be run when using os.Exit, so this fmt.Println will never be called.
    defer fmt.Println("!")

    // Exit with status 3.
    os.Exit(3)
}
```

Note that unlike e.g. C, Go does not use an integer return value from main to indicate exit status. If you’d like to exit with a non-zero status you should use os.Exit.

If you run exit.go using go run, the exit will be picked up by go and printed.

```sh
go run exit.go
exit status 3
```

By building and executing a binary you can see the status in the terminal.

```sh
go build exit.go
./exit
echo $?
3
```

Note that the ! from our program never got printed.

[Sadržaj][00]

[00]: 00_gbe.md
