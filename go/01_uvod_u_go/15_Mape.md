
# Mape

- [[Isečci]][14]  
- [[Sadržaj]][00]  
- [[Interfejsi]][16]

Mapa je neuređena kolekcija parova `key-value`. Ona preslikava ključeve u vrednosti. Ključevi su jedinstveni unutar mape, dok vrednosti možda nisu.

Mape se koriste za brzo pretraživanje, pronalaženje i brisanje podataka na osnovu ključeva. To je jedna od najčešće korišćenih struktura podataka.

## Deklaracija

Mapa se deklariše korišćenjem sledeće sintakse:

```go
var m map[K]V
```

Gde je K tip ključa, a V je tip vrednosti.

Na primer, evo kako možemo deklarisati mapu string ključeva i int vrednosti.

```go
func main() {
    var m map[string]int

    fmt.Println(m)
}
```

```sh
go run main.go
nil
```

Kao što vidimo, nulta vrednost mape je `nil`.

Mapa `nil` nema ključeve. Štaviše, svaki pokušaj dodavanja ključeva mapi `nil` rezultiraće greškom tokom izvršavanja.

> [!Note]
> Ovako deklarisana mapa ne služi ničemu, jer nema svoj alocirani prostor, zbog
> toga se njoj ne može dodati ni jedan par `key:value`. Jedino se može ići sa
> `new` funkcijom, dobiti alokacija i potom to dodeliti ovako deklarisanoj mapi.

## Inicijalizacija

Postoji više načina za inicijalizaciju mape.

### Funkcija make

Možemo koristiti ugrađenu `make` funkciju koja dodeljuje memoriju za referencirane tipove podataka i inicijalizuje njihove osnovne strukture podataka.

```go
func main() {
    var m = make(map[string]int)

    fmt.Println(m)
}
```

```sh
go run main.go
map[]
```

### Map literal

Drugi način je korišćenje literala mape.

```go
func main() {
    var m = map[string]int{
        "a": 0,
        "b": 1,
    }

    fmt.Println(m)
}
```

Imajte na umu da je završni zarez obavezan u postavci svakog para u novi red.

```sh
go run main.go
map[a:0 b:1]
```

Kao i uvek, možemo koristiti i naše prilagođene tipove.

```go
type User struct {
    Name string
}

func main() {
    var m = map[string]User{
        "a": User{"Peter"},
        "b": User{"Seth"},
    }

    fmt.Println(m)
}
```

Možemo čak i ukloniti tip vrednosti i Go će to shvatiti!

```go
var m = map[string]User{
    "a": {"Peter"},
    "b": {"Seth"},
}
```

```sh
go run main.go
map[a:{Peter} b:{Seth}]
```

### Dodavanje vrednosti na mapu

Sada, hajde da vidimo kako možemo dodati vrednost našoj mapi.

```go
func main() {
    var m = map[string]User{
        "a": {"Peter"},
        "b": {"Seth"},
    }

    m["c"] = User{"Steve"}
    fmt.Println(m)
}
```

```sh
go run main.go
map[a:{Peter} b:{Seth} c:{Steve}]
```

### Preuzmimanje vrednosti sa mape

Takođe možemo da preuzmemo vrednosti sa mape pomoću ključa.

```go
...
c := m["c"]
fmt.Println("Key c:", c)
```

```sh
go run main.go
key c: {Steve}
```

Šta ako koristimo ključ koji nije prisutan na mapi?

```go
...
d := m["d"]
fmt.Println("Key d:", d)
```

Da, pogodili ste! Dobićemo nultu vrednost tipa vrednosti mape.

```sh
go run main.go
Key c: {Steve}
Key d: {}
```

### Provera prisutnosti ključa

Kada preuzmete vrednost dodeljenu datom ključu, vraća se i dodatna bulova vrednost. Bulova promenljiva će biti `true` ako ključ postoji, a `false` u suprotnom.

Hajde da ovo pokušamo na jednom primeru:

```go
...
c, ok := m["c"]
fmt.Println("Key c:", c, ok)

d, ok := m["d"]
fmt.Println("Key d:", d, ok)
```

```sh
go run main.go
Key c: {Steve} Present: true
Key d: {} Present: false
```

### Ažuriranje mape

Takođe možemo ažurirati vrednost ključa jednostavnim ponovnim dodeljivanjem ključa.

```go
...
m["a"] = "Roger"
```

```sh
go run main.go
map[a:{Roger} b:{Seth} c:{Steve}]
```

### Brisanje iz mape

Ili, možemo obrisati ključ koristeći ugrađenu `delete` funkciju.

Evo kako izgleda sintaksa:

```go
delete(m, "a")
```

Prvi argument je mapa, a drugi je ključ koji želimo da obrišemo.

Funkcija `delete()` ne vraća nikakvu vrednost. Takođe, ne radi ništa ako ključ ne postoji u mapi.

```sh
go run main.go
map[a:{Roger} c:{Steve}]
```

### Iteracija mape

Slično nizovima ili isečcima, možemo iterirati kroz mape pomoću `range` ključne reči.

```go
package main
import "fmt"

func main() {
    var m = map[string]User{
        "a": {"Peter"},
        "b": {"Seth"},
    }
    m["c"] = User{"Steve"}
    for key, value := range m {
        fmt.Println("Key: %s, Value: %v", key, value)
    }
}
```

```sh
go run main.go
Key: c, Value: {Steve}
Key: a, Value: {Peter}
Key: b, Value: {Seth}
```

Imajte na umu da je mapa neuređena kolekcija i stoga nije garantovano da će redosled iteracije mape biti isti svaki put kada je iteriramo.

### Svojstava mapa

Mape su `referentni` tipovi, što znači da kada dodelimo mapu novoj promenljivoj, obe se odnose na istu osnovnu strukturu podataka.

Stoga će promene koje izvrši jedna promenljiva biti vidljive drugoj.

```go
package main

import "fmt"

type User struct {
    Name string
}

func main() {
    var m1 = map[string]User{
        "a": {"Peter"},
        "b": {"Seth"},
    }

    m2 := m1
    m2["c"] = User{"Steve"}

    fmt.Println(m1) 
    fmt.Println(m2) 
}
```

```go
map[a:{Peter} b:{Seth} c:{Steve}]
map[a:{Peter} b:{Seth} c:{Steve}]
```

- [[Isečci]][14]  
- [[Sadržaj]][00]  
- [[Interfejsi]][16]

[14]: 14_Isečci.md
[00]: toc.md
[16]: 16_Interfejsi.md
