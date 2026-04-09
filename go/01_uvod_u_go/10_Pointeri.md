
# Pointeri

- [[Korisne komande]][09]  
- [[Sadržaj]][00]  
- [[Strukture]][11]

`pointer` je promenljiva koja se koristi za čuvanje memorijske adrese druge promenljive.

Može se deklarisati ovako:

```go
var x *T
```

Gde je T tip kao što su `int`, `string`, `float32`, i tako dalje.

Jednostavan primer za pointere u akciji.

```go
package main
import "fmt"

func main() {
    var p *int
    fmt.Println(p)
}
```

```sh
go run main.go
nil
```

Ovo štampa `nil`. `nil` je unapred deklarisani identifikator u Go-u koji predstavlja nultu vrednost za `pointere`, `interfejse`, `kanale`, `mape` i `isečke`.

Ovo je baš kao ono što smo naučili u odeljku o promenljivim i tipovima podataka, gde smo videli da neinicijalizovani `int` ima nultu vrednost `0`, a `bool` ima `false` i tako dalje.

## Referenciranje (uzimanje adrese) promenljive

Vrednost pointerskoj promenljivoj dodeljujemo ovako:

```go
package main
import "fmt"

func main() {
    a := 10
    var p *int = &a
    fmt.Println("address:", p)
}
```

Koristimo `&` operator da bismo preuzeli memorijsku adresu promenljive. Ovo se naziva *referenciranje promenljive*.

```sh
go run main.go
0xc0000b8000
```

Referenca je vrednost memorijske adrese promenljive.

## Dereferenciranje (uzimanje vrednosti) pointerske promenljive

Takođe možemo koristiti `*` operator da bismo preuzeli vrednost sačuvanu u promenljivoj na koju pointer pokazuje. Ovo se naziva i *dereferenciranje promenljive*.

```go
package main
import "fmt"

func main() {
    a := 10
    var p *int = &a
    fmt.Println("address:", p)
    fmt.Println("value:", *p)
}
```

```sh
go run main.go
address: 0xc000018030
value: 10
```

Ne samo da joj možemo pristupiti već i promeniti je pomoću pointera.

```go
package main
import "fmt"

func main() {
    a := 10
    var p *int = &a
    fmt.Println("before", a)
    fmt.Println("address:", p)
    *p = 20
    fmt.Println("after:", a)
}
```

```sh
go run main.go
before 10
address: 0xc000192000
after: 20
```

## Pointeri kao argumenti funkcije

Pointeri se takođe mogu koristiti kao argumenti za funkciju kada treba da prosledimo neke podatke referencom.

Evo jednog primera:

```go
myFunction(&a)
...

func myFunction(ptr *int) {}
```

## Funkcija new

Drugi način inicijalizacije pointera je korišćenje `new` funkcije koja uzima tip kao argument, alocira dovoljno memorije da smesti vrednost tog tipa i vraća pointer na nju.

Evo jednog primera:

```go
package main

import "fmt"

func main() {
    p := new(int)
    *p = 100
    fmt.Println("value", *p)
    fmt.Println("address", p)
}
```

go run main.go
value 100
address 0xc000018030

## Pointer na pointer

Možemo li da napravimo pointer na pointer? Odgovor je da, možemo!

```go
package main
import "fmt"

func main() {
    p := new(int)
    *p = 100
    p1 := &p
    fmt.Println("P value", *p, " address", p)
    fmt.Println("P1 value", *p1, " address", p)
    fmt.Println("Dereferenced value", **p1)
}
```

```sh
go run main.go
P value 100  address 0xc0000be000
P1 value 0xc0000be000  address 0xc0000be000
Dereferenced value 100
```

**Napomena**; Obratite pažnju kako vrednost *p1* odgovara adresi od *p*.  

**Napomena**: Važno je znati da pointeri u Go-u *ne podržavaju pointersku aritmetiku* kao u C-u ili C++-u.

```go
p1 := p * 2 // Compiler Error: invalid operation
```

Možemo uporediti dva pointera istog tipa radi jednakosti koristeći == operator.

```go
p := &a
p1 := &a
fmt.Println(p == p1)
```

Zašto su nam potrebni pointeri? Nema definitivnog odgovora na to, a pointeri su samo još jedna korisna funkcija koja nam pomaže da efikasno mutiramo naše podatke bez kopiranja velike količine podataka.

- [[Korisne komande]][09]  
- [[Sadržaj]][00]  
- [[Strukture]][11]

[09]: 09_Korisne_komande.md
[00]: toc.md
[11]: 11_Strukture.md
