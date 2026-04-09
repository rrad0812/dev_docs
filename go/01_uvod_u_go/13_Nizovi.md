
# Nizovi

- [[Metode]][12]  
- [[Sadržaj]][00]  
- [[Isečci]][14]

Niz je kolekcija elemenata istog tipa fiksne veličine. Elementi niza se čuvaju sekvencijalno i može im se pristupiti pomoću njihovih indeksa.

## Deklaracija niza

Niz možemo deklarisati na sledeći način:

```go
var a [n]T
```

Ovde je `n` dužina i `T` može biti bilo kog tipa kao što je ceo broj, string ili korisnički definisane strukture. Dužina niza je deo tipa niza, ili dva niza različiti dužina su različitog tipa.

Sada, hajde da deklarišemo niz celih brojeva dužine 4 i odštampamo ga.

```go
func main() {
    var arr [4]int
    fmt.Println(arr)
}
```

```sh
go run main.go
[0 0 0 0]
```

Podrazumevano, svi elementi niza su inicijalizovani nultom vrednošću odgovarajućeg tipa niza.

## Inicijalizacija

Niz  inicijalizujemo koristeći `array literal`.

```go
var a [n]T = [n]T{V1, V2, ... Vn}
```

```go
func main() {
    var arr = [4]int{1, 2, 3, 4}

    fmt.Println(arr)
}
```

```sh
go run main.go
[1 2 3 4]
```

Možemo čak i da napravimo skraćenu deklaraciju i inicijalizaciju.

```go
arr := [4]int{1, 2, 3, 4}
```

## Pristup članovima niza

Elementima možemo pristupiti koristeći `index` jer su sačuvani sekvencijalno.

```go
func main() {
    arr := [4]int{1, 2, 3, 4}

    fmt.Println(arr[0])
}
```

```sh
go run main.go
1
```

## Iteriranje niza

Prvi način za iteriranje niza je korišćenje petlje `for` sa `len` funkcijom koja daje dužinu niza.

```go
func main() {
    arr := [4]int{1, 2, 3, 4}

    for i := 0; i < len(arr); i++ {
        fmt.Printf("Index: %d, Element: %d\n", i, arr[i])
    }
}
```

```sh
go run main.go
Index: 0, Element: 1
Index: 1, Element: 2
Index: 2, Element: 3
Index: 3, Element: 4
```

Drugi način je korišćenje `range` ključne reči sa `for` petljom.

```go
func main() {
    arr := [4]int{1, 2, 3, 4}

    for i, e := range arr {
        fmt.Printf("Index: %d, Element: %d\n", i, e)
    }
}
```

```sh
go run main.go
Index: 0, Element: 1
Index: 1, Element: 2
Index: 2, Element: 3
Index: 3, Element: 4
```

Ovaj primer funkcioniše isto kao i prethodni.

Ključna reč `range` je prilično svestrana i može se koristiti na više načina.

```go
for i, e := range arr {}     // Normal usage of range
for _, e := range arr {}     // Omit index with _ and use element
for i := range arr {}        // Use index only
for range arr {}             // Simply loop over the array
```

## Višedimenzionalni nizovi

Možemo kreirati višedimenzionalne nizove u programskom jeziku Go.

Hajde da pogledamo jedan primer:

```go
func main() {
    arr := [2][4]int{
        {1, 2, 3, 4},
        {5, 6, 7, 8},
    }
    for i, e := range arr {
        fmt.Printf("Index: %d, Element: %d\n", i, e)
    }
}
```

```sh
go run main.go
Index: 0, Element: [1 2 3 4]
Index: 1, Element: [5 6 7 8]
```

Možemo dozvoliti kompajleru da zaključi dužinu niza koristeći ... elipsis umesto dužine.

```go
func main() {
    arr := [...]int{
        {1, 2, 3, 4},
        {5, 6, 7, 8},
    }
    for i, e := range arr {
        fmt.Printf("Index: %d, Element: %d\n", i, e)
    }
}
```

```sh
go run main.go
Index: 0, Element: [1 2 3 4]
Index: 1, Element: [5 6 7 8]
```

## Svojstva nizova

Dužina niza je deo njegovog tipa. Dakle, niz a i b su potpuno različiti tipovi i ne možemo dodeliti jedan drugom, ako su različite dužine.

To znači da ne možemo promeniti veličinu niza, jer bi promena veličine niza značila promenu njegovog tipa.

```go
package main

func main() {
    var a = [4]int{1, 2, 3, 4}
    var b [2]int = a // Error, cannot use a (type [4]int) as type [2]int in assignment
}
```

Nizovi u programskom jeziku Go su vrednosni tipovi, za razliku od drugih jezika poput C, C++ i Java gde su nizovi referentni tipovi.

To znači da kada dodelimo niz novoj promenljivoj ili prosledimo niz funkciji, ceo niz se kopira.

Dakle, ako napravimo bilo kakve izmene u ovom kopiranom nizu, originalni niz neće biti pogođen i ostaće nepromenjen.

```go
package main
import "fmt"

func main() {
    var a = [7]string{"Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"}
    var b = a // Copy of a is assigned to b
    b[0] = "Monday"
    fmt.Println(a) // Output: [Mon Tue Wed Thu Fri Sat Sun]
    fmt.Println(b) // Output: [Monday Tue Wed Thu Fri Sat Sun]
}
```

- [[Metode]][12]  
- [[Sadržaj]][00]  
- [[Isečci]][14]

[12]: 12_Metode.md
[00]: toc.md
[14]: 14_Isečci.md
