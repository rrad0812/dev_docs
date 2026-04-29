
# Go tutorijal 08

[Sadržaj](000-Sadržaj.md)

## 8 Uslovna izjava

### 8.1 If izjava

If izjava ima uslov i izvršava blok koda ako se taj uslov procenjuje na `true`. Izvršava se alternativni blok ako se uslov procenjuje na `false` .

U ovom tutorialu ćemo pogledati različite sintaksa za korišćenje if izjava.

> [!Note]
> **Sintaksa**
>
> ```go
> if condition {
>
> }
> ```

Ako se uslov procenjuje na `true`, blok koda između velikih zagrada `{` i `}` se izvršava.

Za razliku od drugih jezika poput C, blok zagrade `{}` su obavezne čak i ako postoji
samo jedna linija koda između njih {}.

**Primer**  
Hajde da napišemo jednostavan program da bismo saznali da li je broj paran ili
neparan.

```go
package main

import (
    "fmt"
)

func main() {

    fmt.Println("\n --- If ---")

    num := 10
    if num%2 == 0 { //checks if number is even
        fmt.Println("The number", num, "is even.")
        return
    }
    fmt.Println("The number", num, "is odd.")
}
```

U gornjem programu, uslov "num%2==0" proverava da li je ostatak deljenja sa 2 nula. Pošto je 0 u ovom slučaju, biće štampan tekst:

```sh
The number 10 is even.
```

### 8.2 If else izjava

`If else` izjava ima opcionu `else` konstrukciju čiji blok koda zatvoren u velike zagrade `{}` će se izvršiti ako uslov u if izjavi se ocenjuje na false.

> [!Note]
> **Sintaksa**  
>
> ```go
> if condition {
>
> } else {
>
> }
>```

Prepišimo gornji program da bismo pronašli da li je broj paran ili neparan:

```go
func main() {

    fmt.Println("\n --- if...Else ---")

    num := 11
    if num%2 == 0 { //checks if number is even
        fmt.Println("The number", num, "is even")
    } else {
        fmt.Println("The number", num, "is odd")
    }
}
```

U gornjem kodu, umesto da se vratite ako je uslov true kao što smo to uradili prethodno, stvaramo drugu izjavu koja će se izvršiti ako je uslov false. U ovom slučaju, 11 je neparno, if uslov je false i linije koda u else izjavi se izvršava.

Gornji program će štampati:

```sh
The number 11 is odd
```

### 8.3 If…else if… else izjava

`If` izjava takođe ima fakultativnu `else if` komponentu.

> [!Note]
> **Sintaksa**  
>
>```go
> if condition1 {  
> ...  
> } else if condition2 {  
> ...  
> } else {  
> ...  
> }
> ```

Uslovi se procenjuju od vrha do dna izjave.

- Ako je `uslov1` `true`, onda se `if` blok koda izvršava.

- Ako je `uslov1` `false` i `uslov2` `true`, izvršava se `else if` blok koda.

- Ako su `uslov1` i `uslov2` `false` izvršava se blok `else` koda.

Može se pojaviti bilo koji broj `else if` komponenti `if` izjave.

Uopšteno, zavisno od toga koji je uslov `true` njemu pripadajući blok koda u velikim zagradama se izvršava, ili ako nijedan nije `true`, izvršava se `else` blok koda, ako je prisutan.

Hajde da napravimo program koji ispisuje cenu autobuske karte prema dobi putnika. Program mora da zadovolji sledeće zahteve:

- Ako je starost putnika manje od 5 godina, karta je besplatna.
- Ako je starost putnika između 5 i 22 godine, karta je 10 USD.
- Ako je starost putnika preko 22 godine, karta je 15 dolara.

```go
func main() {

    fmt.Println("\n --- if...else if...else ---")

    age := 10
    ticketPrice := 0

    if age < 5 {
        ticketPrice = 0
    } else if age >= 5 && age <= 22 {
        ticketPrice = 10
    } else {
        ticketPrice = 15
    }
    fmt.Printf("Ticket price is $%d\n", ticketPrice)
}
```

U gornjem programu, starost putnika je postavljena na 10. Uslov u liniji 10 je `true` i otuda će program odštampati

```sh
Ticket price is $10
```

Pokušajte da promenite "age" da biste testirali kod.

### 8.4 If izjava sa dodelom

Postoji još jedna varijanta kada `if` izjava uključuje opcioni izraz kratke dodele, koji se izvrši pre nego što se uslov proceni. Njegova sintaksa je:

> [!Note]
> **Sintaksa**
>
> ```go
> if assignment-statement; condition {
>
> }
> ```

U gornjem kodu, izjava o dodeli se prvo izvrši pre nego što se uslov proceni.

Prepišimo program koji izračunava cenu autobuske karte na sledeći način:

```go
func main() {

    fmt.Println("\n --- if...Else...ShortAssig ---")

    ticketPrice := 0

    if age := 10; age < 5 {
        ticketPrice = 0
    } else if age >= 5 && age <= 22 {
        ticketPrice = 10
    } else {
        ticketPrice = 15
    }
    fmt.Printf("Ticket price is $%d\n", ticketPrice)
}
```

U gornjem kodu promenljiva "age" se inicijalizuje u `if` kodu. Promenljivoj "age" se može pristupiti samo u okviru `if` konstrukcije. tj. opseg promenljive "age" je ograničeno na izjavu `if`. Ako pokušamo da pristupimo promenljivoj "age" van izjave `if...else if...else` kompajler će se žaliti.

Ova sintaksa često je prikladna kada proglasimo promenljivu u svrhu if ispitivanja.Korišćenje ove sintakse u takvim slučajevima osigurava da je opseg promenljive samo u okviru if izjave.

### 8.5 Gotcha

`else` izjava bi trebalo da počne u istoj liniji nakon zatvaranja bloka velike zagrade `}` `if` izjave. Ako ne, kompajler će se žaliti.

Shvatimo to putem programa:

```go
func main() {

    fmt.Println("\n --- if...Else...Gotcha ---")

    num := 10
    if num%2 == 0 { //checks if number is even
        fmt.Println("the number is even")
    } else {
        // else {        // This is syntax error
        fmt.Println("the number is odd")
    }
}
```

U gornjem programu, else izjava ne počinje u istoj liniji posle zatvaranja `if` bloka `}`.  Umesto toga, počinje na sledećoj liniji. Ovo nije dozvoljeno u Go. Ako pokrenete ovaj program, prevodilac štampa grešku:

```sh
syntax error: unexpected keyword else, expected }
(exit status 1)
```

Razlog je zbog automatskog ubacivanja tačke-zarez od strane kompajlera. Možete
pročitati više o ovome na <https://go.dev/ref/spec#semicolons>.

U pravilima je precizirano da će se umetnuti zarez nakon zatvaranja `}`, ako je
to konačni znak linije. Dakle, zarez je automatski ubačen nakon zatvaranja
izjave, od strane kompajlera.

Dakle, naš program zapravo postaje:

```go
...
if num%2 == 0 {
      fmt.Println("the number is even")
};  //semicolon inserted by Go Compiler
else {
      fmt.Println("the number is odd")
}
```

posle umetanja tačke-zarez.

Pošto je {...} else {...} jedna izjava, zarez ne bi trebao biti prisutan usred nje.Otuda ovaj program se ne kompajlira. Stoga to je sintaktički zahtev da else postavite u istoj liniji kao zatvaranje bloka `}`.

Dakle prepisano, gornji kod da bi ispunio sintaksne zahteve će izgledati ovako:

```go
func main() {

    num := 10

    if num%2 == 0 { //checks if number is even
        fmt.Println("the number is even")
    } else {
        fmt.Println("the number is odd")
    }
}
```

Sada se kompajler neće žaliti.

### 8.6 Idiomatski Go

Videli smo razne `if else` konstrukcije i u stvari smo videli više načina da napišete isti program. Na primer, videli smo više načina pisanja program koji proverava da li je broj paran ili neparan upotrebom različitih if else konstrukta.

Koji je idiomatski način kodiranja u Gou? U Go filozofiji, bolje je izbeći nepotrebne grane i uvlačenje koda. Takođe, smatra se da je bolji povratak što ranije moguće.Osigurao sam program.

Gornji program bi mogao dakle da se napiše ovako:

```go
func main() {

    if num := 10; num % 2 == 0 { //checks if number is even
        fmt.Println(num,"is even")
    }  else {
        fmt.Println(num,"is odd")
    }
}
```

Idiomatski način pisanja gore navedenog programa u Go-ovoj filozofiji je izbegavanje `else` grane i povratak odmah ako je uslov `true`.

```go
func main() {

    fmt.Println("\n --- ifElseIdiom ---")

    num := 10
    if num%2 == 0 { //checks if number is even
        fmt.Println(num, "is even")
        return
    }
    fmt.Println(num, "is odd")
}
```

U gornjem programu čim saznamo da je broj paran se vraćamo odmah. Ovo izbegava nepotrebnu izvršavanje kodova. Na ovaj način se rade stvari u Gou 😃.Imajte to na umu kad god pišete Go program.

[Sadržaj](000-Sadržaj.md)
