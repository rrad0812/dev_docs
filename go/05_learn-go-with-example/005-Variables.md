
# Go Tutorijal 05

[Sadržaj](000-Sadržaj.md)

## 5 Promenljive

Promenljiva je ime dato memorijskoj lokaciji za čuvanje vrednosti specifičnog tipa. Postoje razne sintakse za deklaracija promenljivih u Gou. Pogledajmo ih jednu po jednu.

### 5.1 Deklaracija jedne promenljive

> [!Note]
>Sintaksa:
>
> ```go
> var name type
> ```

Pogledajmo primer:

```go
package main

import (
    "fmt"
)

func main() {

    fmt.Println("\n --- Single var declaration ---")

    var age int // variable declaration
    fmt.Println("My initial age is", age)
}
```

Izjava var age int deklariše promenljivu age tipa int.Promenljivoj nije dodeljena vrednost. Ako promenljivoj nije dodeljena vrednost, Go automatski inicijalizira sa nultom vrednosti promenljive za dati tip. U ovom slučaju, promenljivoj age je dodeljena vrednost 0, koja je nulta vrednost tipa int.

### 5.2 Deklaracija i dodela vrednosti

> [!Note]
> Sintaksa:
>
> ```go
> var name type
> ...
> name = value
> ...
> ```

Varijabli se može dodeliti bilo koja vrednost njenog tipa. U gornjem programu,promeljivoj "age" se može dodeliti bilo koja celobrojna vrednost.

```go
func main() {

    fmt.Println("\n --- Single var declaration and assigment---")

    var age int // variable declaration
    fmt.Println("My initial age is", age)

    age = 29 //assignment
    fmt.Println("My age after first assignment is", age)

    age = 54 //assignment
    fmt.Println("My age after second assignment is", age)
}
```

### 5.3 Deklaracija promenljive sa početnom vrednosti

> [!Note]
> Sintaksa:
>
> ```go
> var name type = initial_value
> ```

Promenljiva se takođe može inicijatizovati sa vrednosti kada je deklarisana, Vrednost mora biti deklarisanog tipa.

```go
func main() {

    fmt.Println("\n --- Single var declaration and init ---")

    var age int = 29 // variable declaration with initial value
    fmt.Println("My initial age is", age)
}
```

U gornjem programu, promenljiva age je tipa `int` i ima inicijalnu vrednost 29.

### 5.4 Deklaracija sa zaključivanjem tipa

> [!Note]
> Sintaksa:
>
> ```go
> var name = initial_value
> ```

Ako promenljiva ima početnu vrednost, Go će automatski da se zaključi tip te promenljive koristeći njenu početnu vrednost. Otuda ako promenljiva ima početna vrednost, deklaracija tipa promenljive može se izostaviti.

U sledećem primeru možemo videti da je tip int promenljive age uklonjen u redu br.3. Pošto promenljiva age ima početnu vrednost 29, Go će zaključiti da je tip promenljive `int`.

```go
func main() {

    fmt.Println("\n --- Single var declaration, init and infered ---")

    var age = 29 // type will be inferred - int
    fmt.Println("My initial age is", age)
}
```

### 5.5 Višestruka deklaracija promenljivih

> [!Note]
> Sintaksa:
>
> ```go
> var name1, name2 type = initial_value1, initial_value2
> ```

Ovo je sintaksa za više deklaracija promenljivih u jednom izjavi.

```go
func main() {

    fmt.Println("\n --- Multiple var declaration and init ---")

    var price, quantity int = 5000, 100 //declaring multiple variables
    fmt.Println("price is", price, "quantity is", quantity)
}
```

### 5.6 Deklaracija više promenljivh sa zaklučivanjem tipa

> [!Note]
> Sintaksa:
>
> ```go
> var name1, name2 = initial_value1, initial_value2
> ```

Tip se može ukloniti ako promenljive imaju početnu vrednost. U programu iznad promenljive imaju početne vrednosti, tip `int` se može ukloniti.

```go
func main() {

    fmt.Println("\n --- Multiple var declaration and infered ---")

    var price, quantity = 5000, 100 //declaring multiple variables with type inference
    fmt.Println("price is", price, "quantity is", quantity)
}
```

### 5.7 Podrazumevana - nulta vrednost za više promenljivih

Kao što ste verovatno pretpostavili, ako početna vrednost promenljive nije navedena imaće dodeljenu nultu vrednost, koja je nulta vrednost za deklarisani tip u Gou.

```go
func main() {

    fmt.Println("\n --- Multiple var declaration and assigment ---")

    var price, quantity int
    fmt.Println("price is", price, "quantity is", quantity)

    price = 3000
    quantity = 500
    fmt.Println("new price is", price, "new quantity is", quantity)
}
```

### 5.8 Grupna deklaracija više promenljivih različitog tipa

Možda će biti slučajeva u kojima bismo želeli da deklarišemo i inicijalizujemo promenljive koje pripadaju različitim tipovima u jednoj izjavi.

> [!Note]
> Sintaksa:
>
> ```go
> var (
>     name1 = initialvalue1
>     name2 = initialvalue2
> )
> ```

Sledeći program koristi gornju sintaksu za deklaraciju promenljivih različitih tipova.

```go
func main() {

    fmt.Println("\n --- Multiple var group declaration and infered ---")

    var (
        name   = "Naveen"
        age    = 38
        height int
    )

    fmt.Println("my name is", name)
    fmt.Println("my age is", age)
    fmt.Println("my height is", height)
}
```

Ovde radimo grupnu deklaraciju promenljivih "name" tipa `string`, "age" i "height" tipa `int`.

### 5.9 Kratka deklaracija promenljive

Go pruža još jedan sažet način da deklariše promenljive.Ovo je poznato kao kratka
deklaracija i koristi walrus `:=` operater.

> [!Note]
> Sintaksa:
>
> ```go
> name := initial_value
> ```

Sledeći program koristi sintaksu kratke ruke za deklaraciju i inicijalizaciju
promenljive count sa celobrojnom vrednosti 10. Go će automatski zaključiti da je "count" tipa `int`.

```go
func main() {

    fmt.Println("\n --- Shorthand var declaration ---")

    count := 10
    fmt.Println("Count =", count)
}
```

Takođe je moguće deklarisati više promenljivih u jednoj liniji koristeći sintaksu
kratake deklaracije.

```go
func shorthandMultipleVarInOneLine() {

    fmt.Println("\n --- Shorthand multiple var declaration ---")

    name, age := "Naveen", 29 //short hand declaration
    fmt.Println("my name is", name)
    fmt.Println("my age is", age)
}
```

Gornji program deklariše dva varijable name i age tipa string i int redom.

Kratka deklaracija zahteva početne vrednosti za sve promenljive na levoj strani
strana znaka walrus operatora.

> [!Note]
> Sve varijable deklarisane sa walrus operatorom moraju imati početne vrednosti.

Sledeći program će ispisati grešaku je jednoj od dve promenljive nije dodeljena
vrednost.

```go
func main() {

    fmt.Println("\n --- Shorthand multiple var declaration and infered ---")

    name, age := "Naveen", 29       // if age is not init thi is error
    fmt.Println("my name is", name, "age is", age)
}
```

> [!Note]
> Bar jedna promenljiva mora biti novo-deklarisana.

Sintaksa kratke deklaracije može se koristiti samo kada je bar jedna od promenljivih na levoj strani warus operatora ( `:=` ) novo-deklarisana.

Razmotrimo sledeći program:

```go
func main() {

    fmt.Println("\n --- Mininimum One Var Must be New Decl in shorthand decl ---")

    a, b := 20, 30 // declare variables a and b
    fmt.Println("a is", a, "b is", b)

    b, c := 40, 50 // b is already declared but c is new
    fmt.Println("b is", b, "c is", c)

    b, c = 80, 90 // assign new values to already declared variables b and c
    fmt.Println("changed b is", b, "c is", c)
}
```

U gornjem programu, na liniji br.7, promenljiva b je već dekarisana, ali c
promenljiva je je novodeklarisana i otuda je sve OK.

> [!Note]
> Kratka deklaracija ne može se izvršiti ponovljena.

Ako pokrenemo program ispod

```go
func main() {

    fmt.Println("\n --- Shorthand decl cant be duplicate ---")

    a, b := 20, 30 //a and b declared
    fmt.Println("a is", a, "b is", b)

    // Sledeće bi proizvelo grešku
    // a, b := 40, 50 // error, no new variables
}
```

greška će biti ispisana:

```sh
./prog.go: 8:7: Nema novih promenljivih na levoj strani :=. 
```

Ovo je, jer su i promenljive a i b već deklarisane i nema novo-deklarisanee promenljive na levoj strani :=.

### 5.10 Runtime dodela vrednosti promenljivoj

> [!Note]
> Promenljivima se mogu dodeliti vrednosti koje se računaju tokom runtime-a.

Razmotrimo sledeći program,

```go
func main() {

    fmt.Println("\n --- Shorthand decl runtime var eval ---")

    a, b := 145.8, 543.8
    c := math.Min(a, b)
    fmt.Println("Minimum value is", c)
}
```

Za gornji program potreban je `math` paket i `min` funkcija u tom paketu. Ne brinite o tome, razgovaraćemo detaljno i o paketima i o funkcijama u predstojećim tutorijalima. Sve što trebamo znati je da se vrednost C izračunava u runtime-u i to je minimum vrednosti a i b.

> [!Note]
> Ne možete da promenite tip deklarisane promenljive.

Pošto je Go jako tipizirani jezik, vrednost promenljive deklarisane kao pripadnice jednog tipa ne može biti dodeljen kao vrednost promenljive deklarisanoj kao pripadnica drugog tipa.

Sledeći program će ispisati grešku. Ne možete koristite "Naveen" (`string` konstanta) kao `int` vrednost, jer je age proglašeno kao tip `int` a mi pokušavamo da joj dodelimo vrednost tipa `string`.

```go
func main() {

    fmt.Println("\n --- cant change type of var declaration ---")

    age := 29           // age is int
    //age = "Naveen"    // error since we are trying to assign a string to
                        // a variable of type int
    fmt.Println(age)
}
```

[Sadržaj](000-Sadržaj.md)
