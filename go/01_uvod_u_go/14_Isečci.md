
# Isečci

- [[Nizovi]][13]  
- [[Sadržaj]][00]  
- [[Mape]][15]

Nizovi su korisni, ali pomalo nefleksibilni zbog ograničenja koje izaziva njihova fiksna veličina. Ovo nas dovodi do isečka. `Isečak` je segment niza. Isečci se nadovezuju na nizove i pružaju veću snagu,fleksibilnost i praktičnost.

Isečak se sastoji od tri stvari:

- `Pointer-referenca` na osnovni niz.
- `Dužina` segmenta niza koji isečak sadrži.
- `Kapacitet`, maksimalna veličina do koje segment može da raste.

Baš kao i kod `len` funkcije, možemo odrediti kapacitet segmenta koristeći ugrađenu `cap` funkciju. Evo primera:

```go
package main

import "fmt"

func main() {
    a := [5]int{20, 15, 5, 30, 25}
    s := a[1:4]

    // Output: Array: [20 15 5 30 25], Length: 5, Capacity: 5
    fmt.Printf("Array: %v, Length: %d, Capacity: %d\n", a, len(a), cap(a))

    // Output: Slice [15 5 30], Length: 3, Capacity: 4
    fmt.Printf("Slice: %v, Length: %d, Capacity: %d", s, len(s), cap(s))
}
```

## Deklaracija isečka

Da vidimo kako možemo deklarisati isečak.

```go
var s []T
```

Prilikom deklracije ne navodimo dužinu isečka. Hajde da deklarišemo isečak stringova i vidimo kako to funkcioniše.

```go
func main() {
    var s []string
    fmt.Println(s)
    fmt.Println(s == nil)
}
```

```sh
go run main.go
[]
true
```

Za razliku od nizova, nulta vrednost isečka je `nil`.

## Inicijalizacija

Jedan od načina iicijalizacije isečka je korišćenje ugrađene `make` funkcije.

```go
make([]T, len, cap) []T
```

```go
func main() {
    var s = make([]string, 0, 0)

    fmt.Println(s)
}
```

```sh
go run main.go
[]
```

Slično nizovima, možemo koristiti `literal slice` da inicijalizujemo naš isečak.

```go
func main() {
    var s = []string{"Go", "TypeScript"}

    fmt.Println(s)
}
```

```sh
go run main.go
[Go TypeScript]
```

Drugi način je kreiranje isečka iz niza. Pošto je isečak segment niza, možemo kreirati isečak od indeksa `low` do indeksa `high` na sledeći način:

```go
a[low:high]
```

```go
func main() {
    var a = [4]string{
        "C++",
        "Go",
        "Java",
        "TypeScript",
    }

    s1 := a[0:2] // Select from 0 to 2
    s2 := a[:3]  // Select first 3
    s3 := a[2:]  // Select last 2

    fmt.Println("Array:", a)
    fmt.Println("Slice 1:", s1)
    fmt.Println("Slice 2:", s2)
    fmt.Println("Slice 3:", s3)
}
```

```sh
go run main.go
Array: [C++ Go Java TypeScript]
Slice 1: [C++ Go]
Slice 2: [C++ Go Java]
Slice 3: [Java TypeScript]
```

Nedostatak `low` indeksa podrazumeva 0, a nedostatak `high` indeksa podrazumeva dužinu osnovnog niza (`len(a)`).

Ono što treba napomenuti je da možemo kreirati isečak i iz drugih isečaka, a ne samo iz nizova.

```go
var a = []string{
    "C++",
    "Go",
    "Java",
    "TypeScript",
}
```

## Iteracija

Možemo iterirati kroz isečak na isti način kao što iterirate kroz niz, koristeći petlju `for` sa `len` funkcijom ili `range` ključnom reči.

## Funkcije na isečku

Slede ugrađene funkcije za isecanje koje su dostupne u Go-u.

### Copy

Funkcija `copy()` kopira elemente iz jednog isečka u drugi. Prihvata 2 isečka, odredište i izvor. Vraća broj kopiranih elemenata.

```go
func copy(dst, src []T) int
```

Da vidimo kako možemo da je koristimo.

```go
func main() {
    s1 := []string{"a", "b", "c", "d"}
    s2 := make([]string, len(s1))

    e := copy(s2, s1)

    fmt.Println("Src:", s1)
    fmt.Println("Dst:", s2)
    fmt.Println("Elements:", e)
}
```

```sh
go run main.go
Src: [a b c d]
Dst: [a b c d]
Elements: 4
```

Kao što se i očekivalo, naša 4 elementa iz izvornog isečka su kopirana u odredišni isečak.

### Append

Isečku možemo dodati elemente koristeći ugrađenu `append` funkciju koja dodaje nove elemente na kraj datog isečka.

Funkcija `append` prihvata isečak i promenljiv broj argumenata. Vraća novi isečak koji sadrži sve elemente.

```go
append(slice []T, elems ...T) []T
```

```go
func main() {
    s1 := []string{"a", "b", "c", "d"}
    s2 := append(s1, "e", "f")

    fmt.Println("s1:", s1)
    fmt.Println("s2:", s2)
}
```

```sh
go run main.go
s1: [a b c d]
s2: [a b c d e f]
```

Kao što vidimo, novi elementi su dodani i vraćen je novi isečak.

Ali ako dati isečak nema dovoljan kapacitet za nove elemente, onda se dodeljuje novi osnovni niz sa većim kapacitetom. Svi elementi iz osnovnog niza postojećeg isečka se kopiraju u ovaj novi niz, a zatim se dodaju novi elementi.

### Svojstva isečaka

Isečci su referentni tipovi, za razliku od nizova. To znači da će modifikovanje elemenata isečka modifikovati odgovarajuće elemente u referenciranom nizu.

```go
package main

import "fmt"

func main() {
    a := [7]string{"Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"}
    s := a[0:2]
    s[0] = "Sun"

    fmt.Println(a) // Output: [Sun Tue Wed Thu Fri Sat Sun]
    fmt.Println(s) // Output: [Sun Tue]
}
```

Isečci se mogu koristiti i sa varijadičkim tipovima.

```go
package main

import "fmt"

func main() {
    values := []int{1, 2, 3}
    sum := add(values...)
    fmt.Println(sum)
}

func add(values ...int) int {
    sum := 0
    for _, v := range values {
        sum += v
    }

    return sum
}
```

- [[Nizovi]][13]  
- [[Sadržaj]][00]  
- [[Mape]][15]

[13]: 13_Nizovi.md
[00]: toc.md
[15]: 15_Mape.md
