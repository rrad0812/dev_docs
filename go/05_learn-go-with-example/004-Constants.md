
# Go tutorijal 04

[Sadržaj](000-Sadržaj.md)

## 4 Konstante

Konstante u Gou se koriste za označavanje fiksnih statičkih vrednosti kao što su:

```go
95
"I love Go"
67.89
```

i tako dalje. Konstante se uglavnom koriste za predstavljanje vrednosti koje se ne menjaju tokom životnog veka aplikacije.

### 4.1 Deklarisanje konstante

Ključna reč `const` se koristi za deklarisanje konstante u Go-u. Pogledajmo primer:

```go
package vars

import (
    "fmt"
    "math"
)

func main() {

    fmt.Println("\n --- ctoDecl ---")

    const a = 50
    fmt.Println(a)
}
```

U gornjem kodu u "a" se nalazi konstanta vrednost 50.

### 4.2 Deklarisanje grupe konstanti

Postoji i druga sintaksa za definisanje grupe konstanti korištenjem jedne naredbe. Primer definisanja grupe konstanti:

```go
func main() {

    fmt.Println("\n --- ctoMultiDecl ---")

    const (
        retryLimit = 4
        httpMethod = "GET"
    )

    fmt.Println(retryLimit)
    fmt.Println(httpMethod)
}
```

U gornjem programu deklarisali smo 2 konstante "retryLimit" i "httpMethod".

### 4.3 Vrednost konstante u vreme kompajliranja

Konstantama, kao što im samo ime govori, ne mogu se ponovo dodeliti druge vrednosti. U programu ispod, pokušavamo dodeliti drugu vrednost 89 na "a". To nije dozvoljeno jer "a" je konstanta. Ovaj program neće uspeti, pri kompajliranju i dobićemo grešku:

```sh
cannot assign to a (neither addressable nor a map index expression).
```

```go
func ctoReAssignError() {

    fmt.Println("\n --- ctoReAssignError ---")

    const a = 55 // sllowed
    fmt.Println("a is", a)
    
    // a = 89 //reassignment not allowed
    fmt.Println("a is", a)
    fmt.Println("In Go, reassigment of constant is forbiden.")
}
```

> [!Note]
Vrednost konstante treba biti poznata u vreme kompajliranja.

Stoga se ne može dodeliti vrednost koju vraća poziv funkcije jer se poziv funkcije odvija u vreme
izvršavanja.

```go
func main() {

    fmt.Println("\n --- ctoCompileTime ---")

    var a = math.Sqrt(4) //allowed
    fmt.Println("var a is", a)

    // const b = math.Sqrt(4) //not allowed
    const b = 2
    fmt.Println("const b is", b)
}
```

U gornjem programu, "a" je varijabla i stoga se može dodeliti rezultat funkcije math.Sqrt(4). "b" je konstanta i vrednost "b" mora biti poznata u vreme kompajliranja. Funkcija `math.Sqrt(4)` će biti evaluirana samo tokom izvršavanja i stoga `const b = math.Sqrt(4)` se kompajlira sa greškom:

```sh
./prog.go:11:12: math.Sqrt(4) (value of type float64) is not constant
```

### 4.4 String konstante, tipizirane i netipizirane konstante

Bilo koja vrednost zatvorena između dvostrukih navodnika je string konstanta u
Gou. Na primer, stringovi poput "Hello World", "Sam" su svi konstante u Gou.

Kom tipu pripada string konstanta? Odgovor je da su to "netipizirane" konstante.

String konstanta poput "Zdravo svete" nema nijedan tip.

```go
const hello = "Hello World"
```

U gornjoj liniji koda, konstanta "hello" nema tip.

Go je jezik sa strogo definisanim tipom. Sve varijable zahtevaju eksplicitni
tip. Kako funkcioniše sledeći program koji dodjeljuje varijabli "name"
netipiziranu konstantu "n"?

```go
func ctoUntyped() {

    fmt.Println("\n --- ctoUntiped ---")

    const n = "Sam"
    var name = n

    fmt.Printf("Type of name is %T, and value is %v\n", name, name)
}
```

> [!Note]
> Netipizirane konstante imaju predefinisani - zadani tip koji je s njima povezan
> i one ga dodjeljuju ako i samo ako to zahteva kod.

U naredbi

```go
var name = n
```

promenljiva "name" zahteva tip i dobija ga iz zadanog tipa konstante "n", što je
`string`.

Postoji li način za kreiranje tipizirane konstante? Odgovor je da. Sledeći kod kreira tipiziranu konstantu.

```go
const name string = "Hello World"
```

"name" u gornjem kodu je konstanta tipa `string`.

Go je jezik sa strogo definisanim tipovima. Mešanje tipova tokom izvršenja
nije dozvoljeno. Pogledajmo šta to znači uz pomoć jednog programa:

```go
func ctoMixedType() {

    fmt.Println("\n --- ctoMixedType ---")

    var defaultName = "Sam" // allowed

    // Define a new type myString
    // if we write type myString = string it is alias for string type
    type myString string
    var customName myString = "Sam" // allowed

    // customName = defaultName // not allowed, type mixed
    fmt.Printf("Type of defaultName is %T\n", defaultName)
    fmt.Printf("Type of customName is %T\n", customName)
}
```

U gornjem kodu, prvo kreiramo varijablu "defaultName" i dodjeljujemo je konstantu
vrednost "Sam". Podrazumevani tip konstante "Sam" je `string`, tako da je nakon dodele "defaultName" tipa `string`.

U sledećem redu kreiramo novi tip "myString".

> [!Note]
> Ako želimo da to bude alias:
>
> ```go
> type myString = string
> ```

Zatim kreiramo varijablu "customName" tipa "myString" i dodelimo joj konstantu "Sam". Budući da konstanta "Sam" `string` tipa, a da je bazni tip "myString" tipa isto `string` može se dodeliti promenljivoj tipa myString. Stoga je ovo dodeljivanje dozvoljeno i "customName" dobija tip "myString".

Sada imamo varijablu "defaultName" tipa `string` i drugu varijablu "customName" tipa "myString" . Iako znamo da je "myString" `string`, Go-ova politika strogog tipiziranja ne dozvoljava da se varijable jednog tipa dodele drugom. Stoga dodeljivanje

```go
customName = defaultNamene
```

nije dozvoljeno i kompajler izbacuje grešku:

```sh
../prog.go:9:15: cannot use defaultName (variable of type string) as myString value in assignment
```

Da bi gornji program radio, "defaultName" se mora pretvoriti u tip myString.
To je urađeno u sledećem programu:

```go
func ctoConv() {

    fmt.Println("\n --- ctoConv ---")

    var defaultName = "Sam" //allowed

    type myString string
    var customName myString = "Sam" //allowed

    customName = myString(defaultName) //allowed

    fmt.Println(customName)
}
```

### 4.5 Bool-ove konstante

Booleove konstante se ne razlikuju od string konstanti. To su dve netipizirane
konstante `true` i `false`. Ista pravila za string konstante primjenjuju se na
bool-ove, tako da ih ovde nećemo ponavljati. Sledeći je jednostavan program za
objašnjenje bool-ovih konstanti:

```go
func main() {

    fmt.Println("\n --- ctoBool ---")

    const trueConst = true
    type myBool bool

    var defaultBool = trueConst       //allowed
    var customBool myBool = trueConst //allowed

    // defaultBool = customBool       // not allowed
    defaultBool = bool(customBool)    // allowed

    fmt.Printf("Type of defaultBool is %T\n", defaultBool)
    fmt.Printf("Type of customBool is %T\n", customBool)
}
```

Gore navedeni program je samorazumljiv.

### 4,6 Numeričke konstante

Numeričke konstante uključuju cele, s pomičnim zarezom i kompleksne konstante.
Postoje neke suptilnosti kod numeričkih konstanti.

Pogledajmo nekoliko primera kako bismo stvari razjasnili:

```go
func ctoNumeric() {

    fmt.Println("\n --- ctoNumeric ---")

    const c = 5
    var intVar int = c

    var int32Var int32 = c
    var float64Var float64 = c
    var complex64Var complex64 = c

    fmt.Println("intVar", intVar, "\nint32Var", int32Var, "\nfloat64Var",
        float64Var, "\ncomplex64Var", complex64Var)
}
```

U gornjem programu, konstanta "c" je untyped i ima vrednost 5. Možda se pitate
koji je podrazumevani tip promenljive "c" i ako ga ima, kako ga onda dodelimo
varijablama različitih tipova. Odgovor leži u sintaksi promenljive c. Sledeći
program će stvari pojasniti.

```go
func main() {

    fmt.Println("\n --- ctoNumeric2 ---")

    var i = 5
    var f = 5.6

    var c = 5 + 6i
    fmt.Printf("i's type is %T, f's type is %T, c's type is %T\n", i, f, c)
}
```

U gornjem programu, tip svake promenljive određen je sintaksom numeričke konstante. 5 je celi broj, 5.6 je broj s pomičnim zarezom, a 5 + 6i je kompleksan broj. Kada se gornji program pokrene, ispisuje se

```sh
i's type is int, f's type is float64, c's type is complex128
```

S ovim znanjem, pokušajmo razumeti kako sledeći program funkcioniše:

```go
func ctoNumeric3() {

    fmt.Println("\n --- ctoNumeric3 ---")

    const c = 5

    var intVar int = c
    var int32Var int32 = c
    var float64Var float64 = c
    var complex64Var complex64 = c

    fmt.Println("intVar", intVar, "\nint32Var", int32Var, "\nfloat64Var",
        float64Var, "\ncomplex64Var", complex64Var)
}
```

U gornjem programu, vrednost c je 5, a sintaksa c je generička. Može predstavljati broj s pomičnim zarezom, celi broj ili čak kompleksan broj bez
imaginarnog dela. Stoga je moguće da se dodeli bilo kojem kompatibilnom tipu.

Zadani tip ovih vrsta konstanti može se smatrati generičkim na osnovu konteksta
u kojem se koriste.

U dodeli:

```go
var intVar int = c
```

zahteva se da "c" bude `int`, pa postaje `int` konstanta.

Dok u sledećoj dodeli:

```go
var complex64Var complex64 = c
```

zahteva se da "c" bude kompleksan broj i stoga postaje kompleksna konstanta.
Prilično zgodno. Naravno ovo je moguće samo kod komapatabilnih tipova.

Sledeća dodela neće uspeti:

```go
const d = 5.11
var intVar int = d
```

sa greškom:

```sh
cannot use d (untyped float constant 5.11) as int value in variable declaration (exit status 1)
```

### 4.7 Numerički izrazi

Numeričke konstante se mogu slobodno mešati i uparivati ​​u izrazima, a tip je
potreban samo kada su dodeljene promenljivim ili korištene na bilo kojem mestu
u kodu koje zahteva tip.
*/

```go
func ctoExpr() {

    fmt.Println("\n --- ctoExpr ---")

    var a = 5.9 / 8
    fmt.Printf("a = 5.9/8 = %v, and type of a is %T\n", a, a)
}
```

U gornjem programu, 5.9 je `float` po sintaksi, a 8 je `int` po sintaksi.
Ipak, 5.9/8 je dozvoljeno jer su oba numeričke konstante. Rezultat deljenja je
0.7375 što je je `float`  stoga je promenljiva "a" tipa `float`. Izlaz programa je:

```sh
a = 5.9/8 = 0.7375, and type of a is float64
```

[Sadržaj](000-Sadržaj.md)
