
# Metode

- [[Strukture]][11]  
- [[Sadržaj]][00]  
- [[Nizovi]][13]

Tehnički gledano, Go nije objektno orijentisan programski jezik. Nema klase, objekte i nasleđivanje.

Međutim, Go ima tipove. I možete definisati metode na tipovima.

Metoda nije ništa drugo do funkcija sa posebnim argumentom - **prijemnikom**. Da vidimo kako možemo deklarisati metode.

```go
func (receiver T) Name(params) (returnTypes) {}
```

Argument prijemnika ima ime i tip. Pojavljuje se između ključne reči `func` i imena metode.

Definišimo Car strukturu.

```go
type Car struct {
    Name string
    Year int
}
```

Sada definišemo metodu `IsLatest` koja će nam reći da li je automobil proizveden u poslednjih 5 godina.

```go
func (c Car) IsLatest() bool {
    return c.Year >= 2020
}
```

Kao što vidite, možemo pristupiti instanci Car koristeći promenljivu prijemnika c. Volim da smatram promenljivu prijemnika kao `this` ključnu reč iz objektno orijentisanog sveta.

Sada bi trebalo da budemo u mogućnosti da pozovemo ovu metodu nakon što inicijalizujemo našu strukturu, baš kao što to radimo sa klasama u drugim jezicima.

```go
func main() {
    c := Car{"Tesla", 2021}
    fmt.Println("IsLatest", c.IsLatest())
}
```

## Metode sa pointer prijemnicima

Svi primeri koje smo ranije videli imali su vrednosni prijemnik. Kod vrednosnih prijemnika, metoda radi na kopiji vrednosti koja joj je prosleđena. Stoga, sve izmene izvršene na prijemniku unutar metoda nisu vidljive pozivaocu.

Na primer, napravimo još jednu metodu pod nazivom *UpdateName* koja će ažurirati tip *Car*.

```go
func (c Car) UpdateName(name string) {
    c.Name = name
}
```

Sada, hajde da ovo pokrenemo.

```go
func main() {
    c := Car{"Tesla", 2021}
    c.UpdateName("Toyota")
    fmt.Println("Car:", c)
}
```

```sh
go run main.go
Car: {Tesla 2021}
```

Izgleda da ime nije ažurirano, pa sada prebacimo naš prijemnik na tip pointera i pokušajmo ponovo.

```go
func (c *Car) UpdateName(name string) {
    c.Name = name
}
```

```sh
go run main.go
Car: {Toyota 2021}
```

Metode sa pointer prijemnicima mogu da menjaju vrednost na koju prijemnik pokazuje. Takve modifikacije su vidljive i pozivaocu metode.

## Svojstva metoda

Go je dovoljno pametan da pravilno interpretira naš poziv funkcije i stoga su pozivi metoda pointer prijemnika samo sintaksički šećer koji Go pruža radi praktičnosti.

```go
(&c).UpdateName(...)
c.UpdateName(...)
```

Gornja dva poziva su ekvivalentna.

Prilikom deklaracije možemo izostaviti naziv promenljive prijemnika ako ne planiramo da je koristimo unutar tela metode.

```go
func (Car) UpdateName(...) {}
```

Metode nisu ograničene samo na strukture, već se mogu koristiti i sa tipovima koji nisu strukture.

```go
package main
import "fmt"

type MyInt int

func (i MyInt) isGreater(value int) bool {
    return i > MyInt(value)
}

func main() {
    i := MyInt(10)
    fmt.Println(i.isGreater(5))
}
```

### Zašto metode umesto funkcija?

Zašto koristiti metode umesto funkcija? Kao i uvek, nema posebnog odgovora na ovo, i ni na koji način jedno nije bolje od drugog. Umesto toga, trebalo bi ih koristiti na odgovarajući način kada se situacija pojavi.

Jedna stvar koja mi trenutno pada na pamet je da nam metode mogu pomoći da izbegnemo sukobe u imenovanju. Pošto je metoda vezana za određeni tip, možemo imati ista imena metoda za više prijemnika.

Ali na kraju, to bi moglo da se svede na preferencije, kao što je "pozivi metoda su mnogo lakši za čitanje i razumevanje od poziva funkcija" ili obrnuto.

- [[Strukture]][11]  
- [[Sadržaj]][00]  
- [[Nizovi]][13]

[11]: 11_Strukture.md
[00]: toc.md
[13]: 13_Nizovi.md
