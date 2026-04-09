
# Tipovi podataka

- [[Promenljive]][02]  
- [[Sadržaj]][00]  
- [[Kontrola toka]][04]

## String

U jeziku Go, string je niz bajtova. Deklarišu se ili pomoću dvostrukih navodnika ili povratnih navodnika koji omogućuju da se string proteže preko više redova.

```go
var name string = "My name is Go"
var bio string = `I am statically typed.
    I was designed at Google.`
```

## Bool

Sledeći tip je bool koji se koristi za čuvanje bulovih vrednosti. Može imati dve moguće vrednosti - `true` ili `false`.

```go
var value bool = false
var isItTrue bool = true
```

### bool operatori

Možemo koristiti sledeće operatore na bulovim tipovima:

 Tip | Sintaksa
---- | ------------
Logično | && \|\| !  
Jednakost | == !=

## Numerički tipovi

### Označeni i neoznačeni celi brojevi

Go ima nekoliko ugrađenih tipova celih brojeva različitih veličina za čuvanje označenih i neoznačenih celih brojeva.

Veličina generičkih tipova int i uint zavisi od platforme. To znači da je širina 32 bita na 32-bitnom sistemu i 64 bita na 64-bitnom sistemu.

**Syntax označenih celih brojeva**:

```go
var i int     = 404                 // Platform dependent
var i8 int8   = 127                 // -128 to 127
var i16 int16 = 32767               // -2^15 to 2^15 - 1
var i32 int32 = -2147483647         // -2^31 to 2^31 - 1
var i64 int64 = 9223372036854775807 // -2^63 to 2^63 - 1
```

Slično označenim celim brojevima, imamo i neoznačene cele brojeve.

**Syntax neoznačenih celih brojeva**:

```go
var ui uint     = 404                 // Platform dependent
var ui8 uint8   = 255                 // 0 to 255
var ui16 uint16 = 65535               // 0 to 2^16
var ui32 uint32 = 2147483647          // 0 to 2^32
var ui64 uint64 = 9223372036854775807 // 0 to 2^64
var uiptr uintptr                     // Integer representation of a memory address
```

Ako ste primetili, postoji i `uintptr` tip neoznačenog celobrojnog pointera, koji je celobrojna reprezentacija memorijske adrese. Ne preporučuje se njegova upotreba, tako da ne moramo da brinemo o tome.

Pa koji bi tip celog broja trebalo da koristimo?

Preporučuje se da kad god nam je potrebna celobrojna vrednost, koristimo samo `int` osim ako nemamo konkretan razlog za korišćenje nekog od tipova celog broja ili neoznačenog celog broja.

### Byte i rune

Golang ima dva dodatna celobrojna tipa koja se zovu `byte` i `rune` koji su alijasi za tipove podataka `uint8` i `int32`, respektivno.

```go
type byte = uint8 // alias
type rune = int32 // alias
```

`rune` predstavlja *Unicode kodnu tačku*.

```go
var b byte = 'a'
var r rune = '🍕'
```

### Pokretni zarez

Zatim, imamo tipove sa pokretnim zarezom koji se koriste za čuvanje brojeva sa decimalnom komponentom.

Go ima dva tipa pokretnog zareza `float32` i `float64`. Oba tipa prate standard IEEE-754.

Podrazumevani tip za vrednosti sa pokretnim zarezom je `float64`.

```go
var f32 float32 = 1.7812 // IEEE-754 32-bit
var f64 float64 = 3.1415 // IEEE-754 64-bit
```

#### Numerički operatori

Go pruža nekoliko operatora za izvršavanje operacija nad numeričkim tipovima.

 Tip | Sintaksa
 --- | --------
 Aritmetika | + - * / %
 Poređenje | == != < > <= >=
 Bitski | & \| ^ << >>
 Povećanje/smanjenje | ++ --
 Dodela | = += -= *= /= %= <<= >>= &= \|= ^=

### Kompleksni brojevi

U Gou postoje dva kompleksna tipa, `complex128`, gde su i realni i imaginarni delovi `float64` i `complex64`, gde su realni i imaginarni delovi `float32`.

Kompleksne brojeve možemo definisati ili koristeći ugrađenu funkciju `complex` ili kao literale.

```go
var c1 complex128 = complex(10, 1)
var c2 complex64 = 12 + 4i
```

### Nulte vrednosti

U programskom jeziku Go, svakoj promenljivoj deklarisanoj bez eksplicitne početne vrednosti dodeljuje se njena nulta vrednost.

Na primer, deklarišimo neke promenljive:

```go
var i int
var f float64
var b bool
var s string
fmt.Printf("%v %v %v %q\n", i, f, b, s)
```

```h
go run main.go
0 0 false ""
```

Dakle, kao što vidimo, vrednosti `int` i `float` se dodeljuju kao `0`, `bool` kao `false` i `string` kao prazan string `""`. Ovo se sasvim razlikuje od načina na koji to rade drugi jezici, većina jezika inicijalizuje nedodeljene promenljive kao null ili undefined.

Ovo je odlično, ali šta su ti simboli procenta u našoj `Printf` funkciji? Kao što ste već pretpostavili, oni se koriste za formatiranje izlaza i o njima ćemo saznati više kasnije.

### Konverzija tipa

Sada kada smo videli kako tipovi podataka funkcionišu, hajde da vidimo kako se vrši konverzija tipova.

```go
i := 42
f := float64(i)
u := uint(f)

fmt.Printf("%T %T", f, u)
```

```sh
go run main.go
float64 uint
```

I kao što vidimo, ispisuje se tip kao float64 i uint.

Imajte na umu da se ovo razlikuje od parsiranja.

## Alias tipa

Alias tipa je uveden od verzije Go 1.9. On omogućava programerima da obezbede alternativno ime za postojeći tip i da ga koriste naizmenično sa osnovnim tipom.

```go
package main
import "fmt"

type MyAlias = string

func main() {
    var str MyAlias = "I am an alias"
    fmt.Printf("%T - %s", str, str) // Output: string - I am an alias
}
```

## Definisani tipovi

Definisani tipovi, za razliku od aliasa tipova ne koriste znak
jednakosti.

```go
package main
import "fmt"

type MyDefined string

func main() {
    var str MyDefined = "I am defined"
    fmt.Printf("%T - %s", str, str) // Output: main.MyDefined - I am defined
}
```

Definisani tipovi rade više od pukog davanja imena tipu. Prvo definišu novi imenovani tip od osnovnog tipa. Međutim, ovaj definisani tip se razlikuje od bilo kog drugog tipa, uključujući i njegov osnovni tip.

Stoga se definisani tip ne može koristiti naizmenično sa osnovnim tipom kao alias tipa. U početku je malo zbunjujuće, nadam se da će ovaj primer razjasniti stvari.

```go
package main
import "fmt"

type MyAlias = string
type MyDefined string

func main() {
    var alias MyAlias
    var def MyDefined

    // ✅ Works
    var copy1 string = alias

    // ❌ Cannot use def (variable of type MyDefined) as string value in variable
    var copy2 string = def

    fmt.Println(copy1, copy2)
}
```

Kao što vidimo, ne možemo koristiti definisani tip naizmenično sa osnovnim tipom, za razliku od aliasa tipa.

## Formatiranje stringova

`fmt` paket sadrži mnogo funkcija. Da bismo uštedeli vreme, razmotrićemo najčešće korišćene funkcije.

- Počnimo sa `fmt.Print` našom glavnom funkcijom.
  
  ```go
  fmt.Print("What", "is", "your", "name?")
  fmt.Print("My", "name", "is", "golang")
  ```

  ```sh
  go run main.go
  Whatisyourname?Mynameisgolang
  ```

  `fmt.Print` ne formatira izlaz, jednostavno uzima string i ispisuje ga.

- Zatim, imamo `fmt.Println` što je isto kao i `fmt.Print`, ali dodaje novi
  red na kraju i takođe ubacuje razmak između argumenata.

  ```go
  fmt.Println("What", "is", "your", "name?")
  fmt.Println("My", "name", "is", "golang")
  ```
  
  ```sh
  go run main.go
  What is your name?
  My name is golang
  ```

- Zatim, `fmt.Printf` poznat nam je i kao  "Formatizater štampe", koji nam  omogućava formatiranje brojeva, stringova, bulovih vrednosti i još mnogo toga.

  Pogledajmo jedan primer.
  
  ```go
  name := "golang"
  fmt.Println("What is your name?")
  fmt.Printf("My name is %s", name)
  ```

  ```sh
  go run main.go
  What is your name?
  My name is golang
  ```

  Kao što vidimo, to `%s` je zamenjeno našom *name* promenljivom.

Ali pitanje je šta je `%s` i šta znači? Dakle, `%s` je jedan od *glagola annotacije* i oni govore funkciji kako da formatira argumente. Pomoću njih možemo kontrolisati stvari poput širine, tipova i preciznosti, a ima ih mnogo.

Sada, hajde da brzo pogledamo još nekoliko primera. Ovde ćemo pokušati da izračunamo procenat i ispišemo ga u konzolu.

```go
...
percent := (7.0 / 9) * 100
fmt.Printf("%f", percent)
...
```

```sh
go run main.go
77.777778
```

Recimo da želimo preciznost od 2 mesta, to možemo uraditi i korišćenjem `%.2f`.

Takođe, da bismo dodali znak procenta, moraćemo da ga izbegnemo.

```go
...
percent := (7.0 / 9) * 100
fmt.Printf("%.2f%%", percent)
...
```

```sh
go run main.go
77.78 %
```

Ovo nas dovodi do `fmt.Sprint`, `fmt.Sprintln` i `fmt.Sprintf`. One su u osnovi iste kao i funkcije za štampanje, jedina razlika je što vraćaju string umesto da ga ispisuju.

Hajde da pogledamo jedan primer.

```go
...
s := fmt.Sprintf("hex:%x bin:%b", 10 ,10)
fmt.Println(s)
...
```

```sh
go run main.go
hex:a bin:1010
```

Dakle, kao što vidimo, `fmt.Sprintf` formatira naš ceo broj kao heksadecimalan ili binarni i vraća ga kao string.

Na kraju, imamo višelinijske string literale, koji se mogu koristiti na ovaj
način.

```go
...
msg := `
Hello from
multiline
`
fmt.Println(msg)
...
```

Odlično! Ali ovo je samo vrh ledenog brega... zato obavezno pogledajte dokumentaciju za `fmt` paket.

Za one koji dolaze sa C/C++ pozadinom, ovo bi trebalo da deluje prirodno, ali ako dolazite, recimo, sa Pythona ili Javascripta, ovo bi u početku moglo biti malo čudno. Ali je veoma moćno i videćete da se ova funkcionalnost koristi prilično intenzivno.

- [[Promenljive]][02]  
- [[Sadržaj]][00]  
- [[Kontrola toka]][04]

[02]: 02_Promenljive.md
[00]: toc.md
[04]: 04_Kontrola_toka.md
