
# Go tutorijal 03

[Sadržaj](000-Sadržaj.md)

## 3 Tipovi podataka

Sledeći su osnovni tipovi podataka dostupni u Gou:

**Bool tip**:

```go
bool
```

**Numerički tipovi**:

| Vrsta | - | - | - | - | - |
| ----- | :-: | :-: | :-: | :-: | :-: |
| Celobrojni | int8 | int16 | int32 | int64 | int |
| Celobrojni neoznačeni | uint8 | uint16 | uint32 | uint64 | uint |
| Float | float32 | float64 | | | |
| Kompleksni | complex64 | complex128 | | | |
| Bajt | byte | | | | |
| Rune | rune | | | | |

**String tip**:

```go
string
```

### 3.1 bool tip

bool tip predstavlja logičke vrednosti. Može biti `true` ili `false` vrednost.

```go
func main() {
    fmt.Println("\n --- Bool type ---")
    
    a := true
    b := false
    fmt.Println("a:", a, "b:", b)

    c := a && b
    fmt.Println("c:= a && b", c)

    d := a || b
    fmt.Println("d := a || b", d)
}
```

U gornjem programu, dodeljeno je promenljivoj "a" vrednost `true` i promenljivoj "b" vradnost `false`.

`&&` je boolean operator koji se vraća `true` kada su oba operanda `true`.

Promenljivoj "c" je dodeljena vrednost `a && b`. U ovom slučaju c je `false`, uslov da su i "a" i "b" i `true` nije ispunjen.

`||` operator vraća `true` kada je jedan od operanda `true`. U ovom slučaju "d" je
`true`.

### 3.2 Numerički tipovi

#### 3.2.1 Označeni celi brojevi

Slede tipovi označenih celih brojeva dostupnih u Gou.

| Naziv | Veličina | Opis | Opseg |
| ----- | -------- | ---- | ----- |
| int8 | 8 bit | signed integers 8 bits | -128 to 127 |
| int16 | 16 bit | signed integers 16 bits | -32768 to 32767 |
| int32 | 32 bit | signed integers 32 bits | -2147483648 to 2147483647 |
| int64 | 64 bit | signed integers 64 bits | -9223372036854775808 do   9223372036854775807 |
| int | 32 ili 64 bit | Zavisi od  arhitekture. Treba koristiti int da predstavljate cele brojeve, osim ako ne postoji potreba za korišćenjem određene veličine celog broja. 32 bita u 32-bitnom sistemima i 64-bitni u 64-bitnom sistemima. | -2147483648 do 2147483647 u 32-bitnom sistemima i -9223372036854775808 do 9223372036854775807 u 64-bitnim sistemima. |

```go
func main() {

    fmt.Println("\n --- Signed int type ---")

    var a int = 89
    b := 95
    fmt.Println("value of a is", a, "and b is", b)
}
```

U gornjem programu promenljive "a"  i "b" su tipa `int`, s tom razlikom što se tip promenljive "b" zaključuje. Kao što smo gore naveli, veličina `int`-a je 32 bita na 32-bitnim sistemima i 64 bita na 64-bitnim sistemima. Hajde proverimo ovu tvrdnju.

#### 3.2.2 Tip i veličina promenljive u memoriji

Tip promenljive može se štampati pomoću `%T` specifikatora formata sa funkcijom `Printf`. Go ima `unsafe` paket koji ima funkciju koja vraća veličinu promenljive
u bajtovima. `Unsafe` paket treba koristi sa pažnjom, jer može otvoriti pitanja
prenosivosti, ali u svrhu ovog tutorijala možemo ga koristiti.

Sledeći program štampa tip i veličinu promenljivih "a" i "b". `%T` je specifikator
formata za štampanje tipa, a `%d` se koristi se za štampanje vrednosti `int` promenljivih.

```go
func main() {

    fmt.Println("\n --- Type and Size of Variables ---")

    var a = 89 // Infered type of a
    b := 95    // Infered type of b
    fmt.Println("Value of a is", a, "and b is", b)

    fmt.Printf("Type of a is %T, size of a is %d bytes", a, unsafe.Sizeof(a))
    fmt.Printf("\ntype of b is %T, size of b is %d bytes \n", b, unsafe.Sizeof(b))
}
```

Gornji program će odštampati sledeći izlaz:

```sh
>> Value of a is 89 and b is 95
>> Type of a is int, size of a is 8 bytes
>> Type of b is int, size of b is 8 bytes
```

Možemo zaključiti iz gornjeg izlaza da su "a" i "b" tipa `int` i da imaju veličinu
8 bajtova (64 bita). Izlaz će se razlikovati ako pokrenete gornji program na
32-bitnom sistemu. U 32-bitnom sistemu, "a" i "b" zauzeće 4 bajta (32 bita).

#### 3.2.3 Neoznačeni celi brojevi

Neoznačeni celi broj kao što ime označava se može koristiti samo za čuvanje
pozitivnih celih brojeva. Sledeće su nepotpisane vrste podataka dostupne u Gou.

| naziv | veličina | opisa podataka | opseg |
| ----- | -------- | -------------- | ----- |
| uint8 | 8 bit | unsigned integers 8 bits | 0 to 255 |
| uint16 | 16 bit | unsigned integers 16 bits | 0 to 65535 |
| uint32 | 32 bit | unsigned integers 32 bits | 0 to 4294967295 |
| uint64 | 64 bit | unsigned integers 64 bits | 0 to 18446744073709551615 |
| uint | 32 ili 64 bit | neoznačeni celi brojevi u zavisnosti od osnovne            arhitekture 32 bita na 32-bitnom sistemima i 64 bita na 64 bitnom sistemima | 0 do 4294967295 na 32-bitnim sistemima i 0 do 18446744073709551615 na 64-bitnim sistemima. |

Neoznačeni celi brojevi se koriste na mestima gde negativne vrednosti nisu
primenljive.

U sledećem programu promenljive a i b su tipa uint.

```go
func main() {

    fmt.Println("\n --- Unsigned int type ---")
    var a uint = 60
    var b uint = 30
    c := a * b

    fmt.Println("a = ", a, "b = ", b)
    fmt.Println("c = a * b = ", c)
    fmt.Printf("Data type of variable c is %T\n", c)
}
```

Pošto su "a" i "b" tipa `uint`, zaključeni tip promenljive "c" je takođe `uint`.

#### 3.2.4 Tip plutajućeg zareza

| Tip | Opis |
| --- | ---- |
| float32 | 32 bit floating point |
| float64 | 64 bit floating point |

Sledi jednostavan program za ilustraciju celih i plutajućih tipova

```go
func main() {

    fmt.Println("\n --- Float point type ---")

    a, b := 5.67, 8.97
    fmt.Printf("value of of a is %.2f, b is %.2f\n", a, b)
    fmt.Printf("type of a is %T, b is %T\n", a, b)

    sum := a + b
    diff := a - b
    fmt.Printf("sum of %.2f and %.2f is %.2f, diff is %.2f\n", a, b, sum, diff)

    no1, no2 := 56, 89
    fmt.Printf("value of of no1 is %.2f, no2 is %.2f\n", a, b)
    fmt.Printf("type of no1 is %T, no2 is %T\n", no1, no2)
    fmt.Printf("sum of %d and %d is %d, diff is %.d\n", no1, no2, no1+no2, no1-no2)
}
```

Tipovi "a" i "b" su zaključeni iz dodeljene vrednosti. U ovom slučaju "a" i "b" su tipa `float64`. `Float64` je podrazumevani tip za vrednosti plutajućeg zareza. Dodajemo "a" i "b" i dodelimo ga promenljivoj "sum". Oduzmemo b od a i dodelimo ga promenljivoj "diff".

3.2.5 Kompleksni tip

| Naziv | Opis podataka |
| ----- | ------------- |
| Complex64 | Kompleksni brojevi sa float32 realnim i imaginarnim delovima |
| Complex128 | Kompleksni brojevi sa float64 realnim i imaginarnim delovima |

Funkcija `complex` iz standardna biblioteke "complex" koristi se za konstrukciju kompleksnog broja sa realnim i imaginarnim delovima. `complex` funkcija ima sledeću definiciju:

```go
func complex(r, i FloatType) ComplexType
```

Ona uzima realnu i imaginarnu vrednost kao parametar i vraća se kompleksni tip.

I realni i imaginarni delovi moraju biti istog tipa, ili `float32` ili `float64`.
Ako su i realni i imaginarni delovi `float32`, ova funkcija vraća kompleksnu vrednost tipa `complex64`. Ako su i realni i imaginarni delovi tipa `float64`,
ova funkcija vraća kompleksnu vrednost tipa `complex128`.

Kompleksni brojevi se mogu kreirati i pomoću kratke sintakse:

```go
c := 6 + 7i
```

Napišimo mali program za razumevanje kompleksnih brojeva.

```go
func main() {

    fmt.Println("\n --- Complex type ---")

    c1 := complex(5, 7)
    c2 := 8 + 27i
    fmt.Println("c1 = ", c1)
    fmt.Println("c2 = ", c2)

    cadd := c1 + c2
    fmt.Println("sum = c1 + c2 = ", cadd)

    cmul := c1 * c2
    fmt.Println("product = c1 * c2 =", cmul)
}
```

U gornjem programu "c1" i "c2" su dva kompleksna broja. "c1" ima 5 kao realni deo i "7" kao imaginarni deo. "c2" ima "8" kao realni deo i "27" kao imaginarni deo.

Promenljivoj "cadd" je dodeljena suma "c1" i "c2" a promenljivoj "cmul" dodeljen je
proizvod "c1" i "c2".

Ovaj program će se štampati kao izlaz:

```sh
c1 = (5+7i)
c2 = (8+27i)
sum: (13+34i)
product: (-149+191i)
```

#### 3.2.5 Ostali numerički tipovi

`byte` je `alias` `uint8`
`rune` je `alias` `int32`

Detaljnije ćemo raspravljati o `byte` i `rune` kada budemo radili stringove.

### 3.3 Tip string

Stringovi su kolekcija bajtova u Gou.

U redu je ako ova definicija nema nikakvog
smisla trenutno. Za sada možemo da pretpostavimo da će string biti kolekcija
znakova. Mi ćemo detaljno naučiti stringove u zasebnom string tutorijalu.

Napišimo program koristeći stringove.

```go
func main() {

    fmt.Println("\n --- String type ---")

    first := "Radosav"
    last := "Radovanović"

    name := first + " " + last
    fmt.Println("My name is", name)
}
```

U gornjem programu, prvo je dodeljen string "Radosav", potom je dodeljen string
"Radovanović". Stringovi se mogu nastaviti pomoću operatora +.

Promenljivoj name je dodeljena vrednost first + " " + last.

Postoje još nekoliko operacija koje se mogu izvesti na stringovima. Pogledaćemo
ih u detaljnom, zasebnom tutorijalu.

### 3.4 Konverzija tipa

Go je veoma strog sa eksplicitnim tipiziranjem. Ne postoji automatska promocija
tipa ili konverzija. Pogledajmo šta to znači na primeru:

```go
func main() {

    fmt.Println("\n --- Type conversion error ---")
    a := 80   // int
    b := 91.8 // float64

    // sum := a + b // int + float64 not allowed
    // fmt.Println(sum)
    fmt.Println("a =", a, "b =", b)
    fmt.Println("a+b is error, a and b su diff types")
}
```

Gornji kod je savršeno legalan na C jeziku, ali u Gou ovaj program se neće kompajlirati. "a" je tipa `int` i "b" je tipa `float64`. Pokušavamo da dodamo 2 broja
različitih tipova, što u Gou nije dozvoljeno.

Kada pokrenete program, dobićete sledeću grešku u kompilaciji

```sh
./prog.go:10:9: invalid operation: a + b (mismatched types int and float64)
```

Da biste ispravili grešku, i "a" i "b" moraju biti istog tipa. Hajde da konvertujemo
"b" u int.

> [!Note]
> **T(v)** je sintaksa konverzije vrednosti **v** u tip **T**.

```go
func main() {

    fmt.Println("\n --- Type conversion ---")

    a := 80   //int
    b := 91.8 //float64
    fmt.Println("a =", a, "b =", b)

    sum := a + int(b) // int(b) conv. float64 to int
    fmt.Println("a =", a, "int(b) =", int(b))
    fmt.Println("sum = a + int(b) =", sum)
}
```

Pošto je b pretvoren iz float64 u int, njegov decimalni deo će biti skraćen
videćemo 171 kao izlaz.

Eksplicitna konverzija tipa je obavezna, da bi konvertovali tip vrednosti
promenljive u neki drugi tip. To je objašnjeno u sledećem programu.

```go
func main() {

    fmt.Println("\n --- Type conversion 2---")
    i := 10
    fmt.Printf("value of i = %d, type of i = %T\n", i, i)

    var j float64 = float64(i) //this statement will not work without explicit conversion
    fmt.Printf("value of j = %.2f, type of i = %T\n", j, j)
}
```

Ovde je int eksplicitno pretvoren u float64, a zatim dodeljen promenljivoj j.
Kada pokušate da dodelite i u j bez eksplicitne konverzije kompajler će baciti
grešku.

[Sadržaj](000-Sadržaj.md)
