
# 9 Kontrolni iskazi

[08 Promenljive, konstante i osnovni tipovi][08]  
[00 Sadržaj][00]  
[10 Funkcije][10]  

**Šta ćete naučiti u ovom poglavlju?**

- Razumeti zašto su nam potrebni kontrolni iskazi

**Obrađeni tehnički koncepti!**

- Kontrolni iskazi
  - if / else
  - if / else if / else
  - switch
  - for iskaz sa jednim uslovom
  - for iskaz sa klauzulom for
- Tok izvršenja
- Bulovski izrazi
- Operatori poređenja
- Petlja

## Svet bez kontrolnih izjava

U prethodnom poglavlju videli smo da:

- Možemo definisati promenljive sa pridruženim tipom
- Tim promenljivim možemo dodeliti vrednost
- Takođe možemo kreirati konstante (koje mogu biti tipizirane ili netipizirane)

Samo sa ovim znanjem možemo da napravimo zanimljive programe. Međutim, nećemo moći da implementiramo složenu logiku. Uzmimo primer. Evo zahteva za naš hotelski projekat:

- Kada zaposleni u hotelu pokrene aplikaciju za upravljanje, može videti ekran A kada nema slobodnih soba.
- Kada je bar jedna soba dostupna, videće ekran B (u žičanom modelu B, dostupne su tri sobe). XXX mora biti zamenjeno imenom hotela.

### Skelet aplikacije

Hajde da počnemo sa izgradnjom programa. Počećemo sa skeletom aplikacije:

```go
package main

func main() {

}
```

U drugom redu možete pronaći deklaraciju paketa. Nalazimo se unutar paketa "main". A zatim imamo funkciju "main". (kasnije ćemo videti pojmove funkcije i paketa).

Hajde da izgradimo naš program:

```go
go build -o skeleton conditionals/firstApplication/withoutConditionals/1_skeleton/main.go
```

Koristimo komandu `go build`. `-o skeleton` znači da ćemo našem binarnom fajlu dati ime "skeleton". Zatim ćemo navesti putanju do naše `go` datoteke. Evo rezimea komande:

```sh
go build -o BINARY_NAME PATH/TO/MAIN.GO
```

### Zaglavlja

Kada izvršimo program, ništa se ne dešava.

Dodajmo nekoliko izjava našem programu:

```go
// control-statements/first-statements/main.go
package main

import "fmt"

func main() {
    hotelName := "The Gopher Hotel"
    fmt.Println("Hotel " + hotelName)
}
```

Ovde definišemo promenljivu "hotelName". Inicijalizujemo je stringom koji sadrži naziv našeg hotela. Zatim koristimo `fmt.Println` da ispišemo na ekran string "Hotel": spojen sa stringom "The Gopher Hotel" koji se nalazi u promenljivoj "hotelName". Hajde da kompajliramo i pokrenemo naš program:

```sh
go build -o v1 conditionals/firstApplication/withoutConditionals/2_header/main.go
./v1
Hotel The Gopher Hotel
```

### Konstantne vrednosti

Sledeći korak je prikazivanje "Trenutno nema slobodnih soba" ili "N slobodnih soba" u zavisnosti od broja raspoloživih soba. Broj slobodnih soba je promenljiv. Ima smisla kreirati promenljivu pod nazivom "roomsAvailable". Ova promenljiva je neoznačeni ceo broj (3, 4, 112, 0).

Kako dobijamo stvarnu vrednost "roomsAvailable"? Potrebno nam je da dobijemo ukupan broj soba (A) i broj zauzetih soba (B), vrednost naše promenljive je A−B.

```go
// control-statements/first-app-without-conditionals/main.go
package main

import "fmt"

func main() {
    hotelName := "The Gopher Hotel"
    fmt.Println("Hotel " + hotelName)
    var roomsAvailable uint8
    var rooms, roomsOccupied uint8 = 100, 10
    roomsAvailable = rooms - roomsOccupied //*\label{cond1diff}
    fmt.Println(roomsAvailable, "rooms available")
}
```

U prethodnom listingu smo dodali deklaraciju 3 promenljive (sve sa tipom uint8)

- roomsAvailable - broj soba koje se mogu iznajmiti
- rooms: - ukupan broj soba u hotelu
- roomsOccupied: - broj soba koje su trenutno zauzete

Imajte na umu da "roomsAvailable" je deklarisano, a nije inicijalizovano, dok su "rooms" i "roomsOccupied" deklarisani i inicijalizovani istovremeno.

Zatim dodeljujemo "roomsAvailable" vrednost "rooms" - "roomsOccupied".

### Slučajne vrednosti

U prethodnom odeljku, direktno smo u program unosili vrednosti za "rooms" i "roomsOccupied". Te vrednosti ćemo preuzeti iz baze podataka ili API pozivom u stvarnom životu. Da bismo ovo simulirali (pre nego što kasnije naučimo), dobićemo slučajni broj. Da bismo to uradili, Go ima praktičnu funkciju:

```go
rand.Intn(100)
```

Funkcija `Intn` iz paketa `rand` će generisati nenegativan slučajni broj između 0 i broja navedenog u parametrima. Na primer, ako pozovemo `rand.Intn(100)`, generisaćemo slučajni broj između 0 i 100.

```go
// control-statements/random/main.go
package main

import (
    "fmt"
    "math/rand"
)

func main() {
    hotelName := "The Gopher Hotel"
    fmt.Println("Hotel " + hotelName)
    var roomsAvailable int
    var rooms, roomsOccupied int = 100, rand.Intn(100)
    roomsAvailable = rooms - roomsOccupied
    fmt.Println(roomsAvailable, "rooms available")
}
```

Počinjemo dodavanjem importa paketa `math/rand`. Ovo je obavezno da biste mogli da koristite `rand.Intn`.

Moramo promeniti tip naše tri promenljive jer `rand.Intn` će generisati `int`, a ne `uint8`.

Zatim dodeljujemo `roomsOccupied` vrednost `rand.Intn(100)`. Drugim rečima, vrednost  `roomsOccupied` će varirati između 0 i 100.

Hajde da izgradimo i pokrenemo naš program:

```sh
$ go build -o random conditionals/firstApplication/withoutConditionals/4_randomValues/main.go $./random
Hotel The Gopher Hotel
19 rooms available
```

Pokrenimo drugi put:

```sh
$./random
Hotel The Gopher Hotel
19 rooms available
```

Rezultat je čudan. Siguran sam da ste očekivali drugu vrednost osim 19! Izgleda da je slučajni broj fiksiran. (možete pokušati da ga izvršite 3, 4, 5 puta. I dalje ćete dobiti istu vrednost). Da bi ispravno radila, biblioteka slučajnih brojeva mora biti dodata u funkciju. Dodatak je broj koji se koristi za inicijalizaciju pseudo-slučajnog generatora.

### Rasejene slučajne vrednosti

```go
// control-statements/random-seeded/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    hotelName := "The Gopher Hotel"
    fmt.Println("Hotel " + hotelName)
    
    var roomsAvailable int
    
    rand.Seed(time.Now().UTC().UnixNano())
    
    var rooms, roomsOccupied int = 100, rand.Intn(100)
    roomsAvailable = rooms - roomsOccupied
    fmt.Println(roomsAvailable, "rooms available")
}
```

Naš generator slučajnih brojeva koristimo kao početnu vrednost, `time.Now().UTC().UnixNano()` što je broj proteklih nanosekundi između 1. januara 1970. u ponoć po UTC i sadašnjeg vremena. Imajte na umu da se ovaj trenutak naziva i UNIX epoha.

Hajde da napravimo i pokrenemo naš program:

```sh
go build -o random2 conditionals/firstApplication/withoutConditionals/5_randomValuesSeeded/main.go
./random2
Hotel The Gopher Hotel
Seven rooms available
```

Pokreni to drugi put:

```sh
$./random2
Hotel The Gopher Hotel
36 rooms available
```

Radi!

**Kada je roomsAvailable jednako 0, specifikacija se ne poštuje!**

Kada je slučajna vrednost jednaka 100, onda će promenljiva "roomsAvailable" biti jednaka 0. Program će ispisati sledeće:

```sh
$./random2
Hotel The Gopher Hotel
0 rooms available
```

Ovo nije tačno. Specifikacija nije ispoštovana. Trebalo bi da prikaže:

```sh
Hotel The Gopher Hotel
No available rooms now
```

Sa našim trenutnim znanjem, ne možemo ispuniti specifikaciju.

Potreban nam je alat koji nam omogućava da ispišemo traženi tekst ako "roomsAvailable" je jednako nuli. Konkretno, naš program treba da ima dva moguća izlaza. Kada je hotel pun, drugi kada hotel nije. Program ima uslov i dva moguća ishoda u zavisnosti od uslova.

## Bulovi izrazi

### Definicija

Bulov izraz je iskaz koji će biti izračunat tokom izvršavanja programa i koji bi trebalo da vrati ili tačno ili netačno. Drugim rečima, bulov izraz je pitanje na koje se može odgovoriti sa tačno ili netačno. Evo nekoliko primera:

- Da li je soba dostupna?
- Da li je kupac platio svoj račun?
- Da li je hotel pun?
- Da li smo poslali imejl sa porukom o rezervaciji kupcu?

Bulov izraz se izračunava tokom izvršavanja programa, što znači da kada kreiramo program, nismo u mogućnosti da ga izračunamo.

Rezultat će zavisiti od vrednosti podataka koje će program učitati. Na primer, kada konstruišemo naš program, učitaćemo iz baze podataka broj raspoloživih soba. Stavićemo taj broj u promenljivu i uporedićemo ga sa 0. Ako je broj raspoloživih soba jednak 0, onda je odgovor na pitanje "Da li je hotel pun?" tačan.

### Bulovi izrazi kroz primere

Evo nekoliko primera bulovih izraza:

```go
// control-statements/boolean-expressions/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UTC().UnixNano())
    var rooms, roomsOccupied int = 100, rand.Intn(100)

    fmt.Println("rooms :", rooms, " roomsOccupied :", roomsOccupied)

    // Example 1: is there more rooms than roomsOccupied
    fmt.Println("boolean expression : 'rooms > roomsOccupied' is equal to :")
    fmt.Println(rooms > roomsOccupied) //*\label{condExpBool1}

    // Example 2: is there more roomsOccupied than rooms
    fmt.Println("boolean expression : 'roomsOccupied > rooms' is equal to :")
    fmt.Println(roomsOccupied > rooms) //*\label{condExpBool2}

    // Example 3: is roomsOccupied equal to rooms
    fmt.Println("boolean expression : 'roomsOccupied == rooms' is equal to :")
    fmt.Println(roomsOccupied == rooms) //*\label{condExpBool3}
}
```

U ovom programu počinjemo pozivanjem funkcije `Seed` iz paketa `math/rand`. Ova funkcija će "postaviti seme" za generator pseudo-slučajnih vrednosti. Kada se postavi seme, generator će izbaciti različite brojeve za svako pokretanje.

Zatim definišemo dve `int` promenljive "rooms", "roomsOccupied" koje su inicijalizovane sa 100, respektivno, i vrednošću koju vraća `rand.Intn(100)` (slučajni ceo broj između 0 i 100).

U liniji sa `rooms > roomsOccupied` kreiramo bulov izraz. Kada se program izvrši, proveriće da li je `rooms` veće od `roomsOccupied`. Ako jeste, vrednost `rooms > roomsOccupied` će biti `true`. Ako nije, vrednost će biti `false`.

U liniji sa `roomsOccupied > rooms` kreiramo bulov izraz. Kada se program izvrši, proveriće da li je `roomsOccupied` veće od rooms. Ako jeste, vrednost `roomsOccupied > rooms` će biti `true`. Ako nije, vrednost će biti `false`.

U ovom programu, definisali smo treći bulov izraz `roomsOccupied == rooms`. Ovaj izraz će biti `true` ako su vrednosti dve promenljive jednake, ako nisu, biće jednak `false`.

Hajde da izgradimo i izvršimo program:

```sh
$ go build -o boolExp conditionals/booleanExpression/main.go
$./boolExp
rooms : 100  roomsOccupied : 98
boolean expression : 'rooms > roomsOccupied' is equal to :
true
boolean expression : 'roomsOccupied > rooms' is equal to :
false
boolean expression : 'roomsOccupied == rooms' is equal to :
false
```

U ovom izvršenju:

- rooms = 100
- roomsOccupied = 98

Naši bulovi izrazi će biti:

- `rooms > roomsOccupied` = true

- `roomsOccupied > rooms` = false

- `roomsOccupied == rooms` = false

U ta tri bulova izraza, koristili smo simbole >, == koji su operatori poređenja. U sledećem odeljku ćemo ih navesti.

### Operatori poređenja

Da biste uporedili vrednost dve vrednosti, možete koristiti šest operatora:

| Operatori poređenja | Značenje |
| :-----------------: | -------- |
| == | jednako |
| != | nije jednako |
| > | veće od |
| >= | veće od  ili jednako |
| < | manje od |
| <= | manje od ili jednako |

Evo programa koji koristi svih šest operatora:

```go
// control-statements/comparison-operators/main.go
package main

import "fmt"

func main() {
    a := 21
    b := 42

    fmt.Println(a == b) // false
    fmt.Println(a == a) // true
    fmt.Println(a != b) // true
    fmt.Println(a > b)  // false
    fmt.Println(a < b)  // true
    fmt.Println(a <= b) // true
    fmt.Println(a <= a) // true
    fmt.Println(a >= a) // true
}
```

Operator poređenja koji se koristi za proveru jednakosti je, ==a ne =. Poslednji je simbol koji se koristi za dodelu, a ne za poređenje. Zamena ==sa =je česta greška, zato pazite na to.

### Bulovi izrazi i operatori poređenja

**Zahtevi**:

Napišite program koji definiše dve int promenljive "ageJohn", "agePaul". One su nasumično inicijalizovane. Te dve promenljive mogu da variraju od 0 do 110 (isključeno, pa je maksimalni broj 109). "ageJohn" predstavlja godine Johna, agePaul godine Paula. Na primer, program ispisuje sledeće linije:

- John ima 42 godine
- Paul ima 1 godinu
- Istina je da je John stariji od Paula
- Netačno je da John i Paul imaju iste godine

**Rešenje**:

```go
// control-statements/application-solution/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UTC().UnixNano())
    var agePaul, ageJohn int = rand.Intn(110), rand.Intn(110)

    fmt.Println("John is", ageJohn, "years old")
    fmt.Println("Paul is", agePaul, "years old")
    fmt.Println("It is", ageJohn > agePaul, "that John is older than Paul")
    fmt.Println("It is", ageJohn == agePaul, "that John and Paul have the same age")
}
```

Hajde da napravimo i pokrenemo naš program:

```sh
$ go build -o app1 conditionals/application1/main.go
$./app1
John is 71 years old
Paul is 41 years old
It is true that John older than Paul.
It is false that John and Paul have the same age.
```

Možemo pokušati da ga pokrenemo drugi put:

```sh
$./app1
John is 32 years old
Paul is 58 years old
It is false that John is older than Paul
It is false that John and Paul have the same age
```

**Napomene**:

"ageJohn > agePaul" je bulov izraz. Biće izračunat tokom izvršavanja. Ako je "ageJohn" veće od "agePaul", onda će biti jednako "true", u suprotnom "false". Korišćeni operator poređenja je `>`.

"ageJohn == agePaul" je takođe logički izraz. Testiraće jednakost između dve promenljive. Operator poređenja je `==`.

## If / else iskaz

Naredba `if-else` vam omogućava da izvršite instrukciju(e) kada je vrednost bulovog izraza tačna (`true`). Kada je vrednost bulovog izraza netačna (`false`), program izvršava `else` skup instrukcija.

> **if boolean-expression {**  
    ... instruction executed if boolean-expression is true ...  
**} else {**  
    ... instruction executed if boolean-expression is false ...  
**}**

**Primer**:

```go
// control-statements/if-else/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UTC().UnixNano())
    var agePaul, ageJohn int = rand.Intn(110), rand.Intn(110)

    fmt.Println("John is", ageJohn, "years old")
    fmt.Println("Paul is", agePaul, "years old")

    if agePaul > ageJohn {
        fmt.Println("Paul is older than John")
    } else {
        fmt.Println("Paul is younger than John, or both have the same age")
    }
}
```

**Objašnjenje koda**  
Počinjemo sa postavljanjem početnih vrednosti u naš generator pseudo-slučajnih brojeva. Zatim definišemo dve celobrojne promenljive: "ageJohn" i "ageJohn". Dodeljujemo vrednost "rand.Intn(110)" tim promenljivim. Ona predstavlja pseudo-slučajni broj između 0 i 109.

Ispisujemo vrednost tih promenljivih. Onda je najzanimljiviji deo u sledećem redu. Pokrećemo if/else strukturu:

- Bulov izraz je "agePaul > ageJohn"

- Kod koji će biti izvršen ako je ovaj izraz tačan je
  
  ```go
  fmt.Println("Paul is older than John")
  ```

- Ako je izraz netačan, izvršiće se ovaj kod:
  
  ```go
  fmt.Println("Paul is younger than John, or both have the same age")
  ```

**Kompilacija i izvršavanje koda**  
Prvo, kompajliramo program:

```go
go build -o ifelse conditionals/ifElse/syntax/main.go
```

Sada možemo da ga izvršimo:

```sh
$./ifelse
John is 13 years old
Paul is 69 years old
Paul is older than John
```

Hajde da pokušamo da to izvršimo drugi put:

```go
John is 61 years old
Paul is 58 years old
Paul is younger than John, or both have the same age
```

**Komentar**  
Naš program može imati dva različita izlaza u zavisnosti od vrednosti "agePaul" i "ageJohn". Sa `if-else` naredbom, možemo kreirati dve grane u programu.

### Ugnjedeni if / else iskazi

Možete ugnježdavati **if/else** naredbe onoliko puta koliko želite. To može biti korisno ako želite da kreirate dodatne podgrane.

**Primer**:

```go
// control-statements/nested-if-else/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UTC().UnixNano())
    var agePaul, ageJohn int = rand.Intn(110), rand.Intn(110)

    fmt.Println("John is", ageJohn, "years old")
    fmt.Println("Paul is", agePaul, "years old")

    if agePaul > ageJohn {
        fmt.Println("Paul is older than John")
    } else {
        // another if/else structure
        if agePaul == ageJohn {
            fmt.Println("Paul and John have the same age")
        } else {
            fmt.Println("Paul is younger than John")
        }
    }
}
```

**Objašnjenje koda**
Unutar bloka else, kreirali smo još jednu `if-else` naredbu:

```go
if agePaul == ageJohn {
   fmt.Println("Paul and John have the same age")
} else {
   fmt.Println("Paul is younger than John")
}
```

**Kompilacija i izvršavanje koda**
Evo komande za izgradnju programa:

```sh
go build -o nestedifelse conditionals/ifElse/nested/main.go
```

Hajde da ga izvršimo:

```sh
./nestedifelse
John is 97 years old
Paul is 73 years old
Paul is younger than John
```

**Komentar**
Čak i ako je ova vrsta konstrukcije legalna, ne preporučujem je. Razlog je jednostavan: teško je čitati i razumeti. Možete je lako izbeći korišćenjem drugih korisnih konstrukcija (kao što je switch, na primer).

### If bez else

> **if boolean-expression {**  
    ... instruction executed if boolean-expression is true ...  
**}

Ova vrsta naredbe je veoma česta. Ista je kao `if/else` naredba, bez bloka `else`. Ova naredba će kreirati granu koja će se izvršiti ako se bulov izraz proceni kao tačno.

Program će izračunati bulov izraz.

- Ako se izraz proceni kao tačan, onda će se izvršiti prvi kod. Nakon izvršenja prvog koda, izvršiće se drugi blok koda.

- Ako se izraz proceni kao netačan, drugi kod će biti izvršen direktno.

**Primer**:

```go
// control-statements/if-without-else/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UTC().UnixNano())
    var agePaul, ageJohn int = rand.Intn(110), rand.Intn(110)

    fmt.Println("John is", ageJohn, "years old")
    fmt.Println("Paul is", agePaul, "years old")

    if agePaul > ageJohn {
        fmt.Println("Paul is older than John")
    }
    fmt.Println("End of the Program")
}
```

**Objašnjenje koda**
Prvi redovi su identični programu iz prethodnog odeljka. Ukratko, definišemo dve celobrojne promenljive koje će biti inicijalizovane slučajnom vrednošću između 0 i 109.

Bulov izraz je agePaul > ageJohn. Kada se vrednost ovog izraza izračuna kao tačna, iskaz se izvršava.fmt.Println("Paul is older than John")

**Kompilacija i izvršavanje koda**:

```sh
$ go build -o ifWithoutElse conditionals/ifWithoutElse/main.go
$./ifWithoutElse
John is 64 years old
Paul is 32 years old
End of the Program
```

Evo još jednog izvodnog rezultata:

```sh
$./ifWithoutElse
John is ten years old
Paul is 72 years old
Paul is older than John
End of the Program
```

**Komentar**:
Možete primetiti da se u prvom izvršavanju, bulov izraz ocenjuje kao netačno (DŽon je stariji od Pola,3 2<6 432<64je netačno). Kao posledica toga, izjava se ne izvršava.fmt.Println("Paul is older than John")

Međutim, u drugom izvršavanju, izraz se procenjuje kao tačno (7 2<1 072<10(tačno je). U tom izdanju Pol je stariji nego što je DŽon odštampan na ekranu!

### if / else if / else

Ponekad ćete morati da izračunate više od jednog bulovog izraza i kreirate više od dve grane.

> **if boolean-expression-1 {**  
    ... instruction executed if boolean-expression-1 is true ...  
**} else if boolean-expression-2 {**  
    ... instruction executed if boolean-expression-2 is true ...  
**} else if boolean-expression-3 {**  
    ... instruction executed if boolean-expression-3 is true ...  
**} else {**  
    ... instruction executed if all of boolean-expression are false ...  
**}**

Kada se prvi proceni kao tačan, izvršiće se blok koda jedan. U suprotnom slučaju, procenjuje se drugi bulov izraz. Kada je tačan, izvršava se blok koda dva. Naprotiv, izvršava se treći bulov izraz... Četvrti blok koda (else) se izvršava kada se sva tri bulova izraza procene kao netačna.

Minimalni broj bulovih izraza za ovu konstrukciju je dva.

**Primer**:

```go
// control-statements/if-else-if/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UTC().UnixNano())
    var agePaul, ageJohn int = rand.Intn(110), rand.Intn(110)

    fmt.Println("John is", ageJohn, "years old")
    fmt.Println("Paul is", agePaul, "years old")

    if agePaul > ageJohn {
        fmt.Println("Paul is older than John")
    } else if agePaul == ageJohn {
        fmt.Println("Paul and John have the same age")
    } else {
        fmt.Println("Paul is younger than John")
    }
}
```

**Objašnjenje koda**:

Kao i prethodno, definišemo i inicijalizujemo (na slučajnu vrednost) dve intpromenljive ( "agePaul" i "ageJohn"). Zatim, počinje `if / else if / else`. Prvi bulov izraz je "agePaul > ageJohn". Drugi bulov izraz je "agePaul == ageJohn".

Ako se prvi bulov uslov proceni kao tačan, tekst "Paul is older than John" će biti ispisan na ekranu, a zatim se program zatvara. Drugi bulov izraz neće biti procenjen.

Kada se prvi bulov izraz proceni kao netačno, procenjuje se drugi bulov izraz. Ako je drugi tačan, onda ispisujemo "Paul and John have the same age" na ekran.

Kada je ovaj drugi izraz netačan, to znači da su i "agePaul > ageJohn" i "agePaul == ageJohn" netačni. To znači da je "agePaul < ageJohn". Ako vam nije jasno, izrazimo to rečima :

- Paul nije stariji od John
- Paul i John nemaju isti broj godina
- Zaključno, Paul je mlađi od Johna.

**Kompilacija i izvršavanje koda**:

```sh
$ go build -o ifElseIfElse conditionals/ifElseifElse/main.go
$./ifElseIfElse
John is 106 years old
Paul is two years old
Paul is younger than John
```

- Prvi i drugi bulov izraz su u ovom pokretanju procenjeni kao netačno.

  ```go
  fmt.Println("Paul is younger than John")se izvršava.
  ```

**Komentar**
Kao što ste primetili, naredba if `/ else if / else` je opširna. Takođe je smatram složenom za razumevanje. Naredba `switch-case` može biti lakša za razumevanje.

## Switch-case

Pomoću `switch` naredbe možete kreirati grane u vašem programu koje će se izvršavati u zavisnosti od vrednosti izraza. Ovaj izraz se naziva `switch iskaz`. Evo nekoliko primera izraza koje možemo koristiti u slučaju switch-a:

- ageJohn
- agePaul + ageJohn
- agePaul * ageJohn(množenje)
- agePaul / ageJohn(odeljenje)
- agePaul > ageJohn
- fmt.Println("this is a text")

**Sintaksa**:

**switch: iskaz; glavni_izraz{**  
  **case: izraz-1**  
    ... instructions-1 ...  
  **case izraz-2**  
    ... instructions-2 ...  
  **case izraz-3**  
    ... instructions-3 ...  
  ...  
  **default: (optional)**  
    ... default instructions ...  
**}**

Izrazi u granama će biti izračunati i upoređeni sa vrednošću glavnog izraza. Ako postoji jednakost, grana se izvršava.

- U switch zaglavlju možete primetiti da imate dva OPCIONALNA elementa:
  - iskaz praćen tačkom-zarezom,
  - glavni izraz.

- Zatim imate niz slučajeva. Svaki slučaj ima isti format:
  - case expression:

- Takođe možete staviti listu izraza u slučaj:
  - case expression1, expression2, expression3:

- Poslednji deo switch case-a je `default`. On je opcionalan.

**Primer**:

```go
//... define agePaul and ageJohn

// switch expression
switch ageJohn {  
case 10:
   fmt.Println("John is 10 years old")
case 20:
   fmt.Println("John is 20 years old")
case 100:
   fmt.Println("John is 100 years old")
default:
   fmt.Println("John has neither 10,20 nor 100 years old")
}

// switch statement; expression
switch ageSum := ageJohn + agePaul; ageSum { 
case 10:
   fmt.Println("ageJohn + agePaul = 10")
case 20, 30, 40: 
   fmt.Println("ageJohn + agePaul = 20 or 30 or 40")
case 2 * agePaul:
   fmt.Println("ageJohn + agePaul = 2 times agePaul")
}

// switch (without statement, without expression)
switch { 
case agePaul > ageJohn:
   fmt.Println("agePaul > ageJohn")
case agePaul == ageJohn:
   fmt.Println("agePaul == ageJohn")
case agePaul < ageJohn:
   fmt.Println("agePaul < ageJohn")
}
```

**Objašnjenje koda**
U ovom primeru, izostavili smo definiciju funkcije `main` (kao i definiciju paketa). Definicija "agePaul" i "ageJohn" je takođe izostavljena. Kompletan kod možete pronaći u pratećem repozitorijumu koda.

- Definisali smo `switch` naredbu:
  - sa izrazom "ageJohn":
  - Program će proceniti vrednost ageJohn i uporediti je sa vrednošću izraza u slučajevima:
    10, 20 i 100.
  - Ovde su naši `case` izrazi samo vrednosti.
  - Ako vrednost ageJohn nije jednaka 10, 20 ili 100, onda se izvršava `default` slučaj.

- Potom smo definisali smo `switch` iskaz:
  - sa definisanjem promenljive "ageSum" i dodelom "ageJohn + agePaul"
  - sa glavnim izrazom "ageSum".
  - Program će prvo izvršiti naredbu koja definiše "ageSum" zatim će proceniti ageSum i uporediti
    sa vrednošću izraza koje definiše u pojedinim slučajevima:
  - 10, (20, 30, 40), i 2 * agePaul
  - Drugi slučaj je lista izraza.
  - Treći slučaj 2 * agePaul je izraz koji treba izračunati.

- Zatim smo definisali `switch` iskaz bez ikakvog izraza.
  - `Switch` bez izraza je ekvivalentan poređenju slučajeva sa vrednošću `true`.

    ```go
    switch {
    case agePaul > ageJohn:
       //...
    }
    ```

    je ekvivalentan sa:

    ```go
    switch true {
    case agePaul > ageJohn:
       //...
    }
    ```

    Bulovski izrazi se očekuju u slučajevima (kada nijedan izraz nije naveden u zaglavlju prekidača)

**Kompilacija i izvršavanje koda**:

```sh
$ go build -o switchCase conditionals/switch/main.go
$./switchCase
John is 92 years old
Paul is 68 years old
John has neither 10,20 nor 100 years old
agePaul < ageJohn
```

**Komentar**  
Switch case se intenzivno koristi u Go programima. Lako se čita, a sintaksa je takođe laka za učenje.

## For iskaz samo sa uslovom

**for condition {**  
    ... instructions ...  
**}**

Pomoću for iskaza možete ponoviti izvršavanje bloka koda nekoliko puta. Broj izvršavanja zavisi od uslova. Dok uslov nije ispunjen, blok koda se izvršava. Kada je uslov ispunjen, ponavljanje se zaustavlja.

Uslov je bulov izraz; na primer "da li je promenljiva X jednaka 42?" ili "da li je promenljiva X veća od 47" ili "da li je imejl poslat?". Bulov izraz će vratiti ili tačno ili netačno.

Naredba for sa jednim uslovom sastoji se od ključne reči for. Odmah posle ključne reči nalazi se uslov, nakon čega sledi otvorena vitičasta zagrada. Instrukcije koje treba ponoviti dodaju se posle početne vitičaste zagrade. Naredba for se završava zatvarajućom vitičastom zagradom.

**Primer**:

```go
// control-statements/for-statement/main.go
package main

import "fmt"

func main() {
    const emailToSend = 3
    emailSent := 0

    for emailSent < emailToSend {
        fmt.Println("sending email..")
        emailSent++ //*\label{forWithSingle2}
    }
    fmt.Println("end of program")
}
```

**Objašnjenje koda**:

- Definišemo konstantu tipa ceo broj "emailToSend". Vrednost konstante je postavljena na 3.
- Zatim, definišemo promenljivu "emailSent" koja je inicijalizovana vrednošću 0.
- Zatim kreiramo for petlju:
  - Uslov za for petlju je "emailSent < emailToSend"
    - Izvršno okruženje procenjuje ovaj uslov.
    - Ako je rezultat evaluacije tačan, onda će iskazi

      ```go
      fmt.Println("sending email..")
      emailSent++
      ```

      biće izvršeni.

  - Kada se uslov proceni kao netačan, prethodne naredbe se ne izvršavaju.

**Kompilacija i izvršavanje koda**:

```sh
$ go build -o forLoopSingleCond conditionals/forLoopSingleCondition/main.go
$./forLoopSingleCond
sending email..
sending email..
sending email..
end of program
```

**Komentar**:

- Dvaput proverite svoj bulov izraz da biste bili sigurni da će biti netačan u nekom trenutku.
- Ako imate bulov izraz koji je uvek tačan, vaš program će se izvršavati neograničeno.
- Možda ste primetili da je sintaksno uslov opcionalan. O tome ćemo kasnije govoriti.

## For iskaz sa inicijalnom klauzulom

Ova vrsta for iskaza je konceptualno ista kao i prethodna. U ovoj for petlji, moraćemo da dodamo dve naredbe:

- Init iskaz
- Post iskaz

**for init_statement; condition; post_statement {**  
    ... instructions ...  
**}**

Pomoću for petlje možete ponoviti izvršavanje instrukcija. Broj ponavljanja je obično definisan brojačem (koji je promenljiva). Kada vrednost brojača pređe definisanu vrednost, ponavljanje se zaustavlja.

```go
const MAX := 2;  

for loopCounter := 1; loopCounter >  MAX; loopCounter++ {  
    fmt.Printf(loopCounter)  
}
```  

Hajde da detaljno opišemo proces koji ovde imamo:

- Prvo definišemo MAX konstantu. To je broj. Na primer, inicijalizujemo je vrednošću 2.
- Zatim definišemo brojač u petlji - loopCounter​​. ​​To je int promenljiva. Inicijalizujemo je na
  vrednost 1.
- Testiramo da li brojač petlji prevazilazi MAX, što nije slučaj (loopCounter​​​​​​​​=1, MAX=2)
- Izvršimo blok koda unutar for petlje
- Izvršavam post iskaz, dodajemo 1 na loopCounter, sada je loopCounter = 2
- Testiramo da li loopCounter prevazilazi ​​MAX što i dalje nije slučaj, tj. imaju istu vrednost = 2
- Izvršimo blok koda unutar for petlje
- Izvršavam post iskaz, dodajemo 1 na loopCounter, sada je loopCounter = 3
- Testiramo da li je loopCounter > MAX. Ovaj put to je tačno, završavamo petlju.
- Kraj programa

Koliko puta je blok koda izvršen? 2 puta (ovo je vrednost MAX).

Pokušajte da uradite istu vežbu, ali ovaj put inicijalizujte vrednost brojača petlje ​​​​​​​na 0.

**Rečnik**:

Petlja for sa sobom nosi specifičan rečnik:

- Init iskaz: U ovom iskazu inicijalizujemo brojač petlje.
- Condition: ovo je bulov izraz (kada se izračuna, trebalo bi da vrati vrednost tačno ili netačno).
  Uslov će biti izračunat na početku svake petlje.
- Post iskaz: Ovo je iskaz koji će se izvršiti NAKON svake petlje.

**Sintaksa**:

Ključna reč koja se ovde koristi je **for**

- Nakon ključne reči, dodaćete:
  - Init iskaz
  - Tačka-zarez
  - Condition
  - Tačka-zarez
  - Post iskaz.

- Imajte na umu da su init iskaz, uslov i post iskaz svi opcioni. Ovaj slučaj ćemo obraditi u sledećem odeljku, zato ne brinite o tome.

- Blok instrukcija (koji će se izvršiti nekoliko puta) nalazi se unutar otvorene i zatvorene vitičaste zagrade.

**Primer**:

```go
// control-statements/for-statement/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UTC().UnixNano())
    var ageJohn int = rand.Intn(110)
    fmt.Println("John is", ageJohn, "years old")

    for i := 0; i < ageJohn; i++ {
        fmt.Println("interation N ", i)
    }

}
```

**Objašnjenje koda**:

Definišemo promenljivu "ageJohn" i inicijalizujemo je na slučajnu vrednost između 0 i 109. Nakon što ispišemo njenu vrednost pomoću naredbe `Println`, dodajemo naredbu `for`. Ova naredba se sastoji od sledećih elemenata: init, conditio i post delova.

- Na početku petlje biće kreirana i inicijalizovana promenljiva "i".  
  Ovo je naš brojač. (Obično se brojači nazivaju: i, j ili k).

- Zatim definišemo uslov petlje:

  ```go
  i < ageJohn
  ```

  Ovo je bulov izraz, što znači da će se tokom izvršavanja proceniti kao tačno ili netačno.

  Ovaj bulov izraz će biti izračunat u svakoj petlji.  
  Ako vrati vrednost "false", petlja se zaustavlja.

- Post iskaz je na kraju

  ```go
  i++
  ```

  Biće izvršeno nakon svake petlje.

  i++ je skraćenica za i = i + 1  
  Povećavamo vrednost i; drugim rečima, dodajemo 1 na i.

- `fmt.Println("iteration N ", i)` će se izvršavati u svakoj petlji. Program će ispisati
  "iteracija N X". Ako je starost Johna jednaka 3, onda ćemo na ekranu ispisati:

    iteracija n 0
    iteracija n 1
    iteracija n 2

Odštampali smo samo tri puta. Vrednost i je 0, zatim 1, i na kraju 2.

**Kompilacija i izvršavanje koda**:

```sh
$ go build -o forLoopClause conditionals/forLoopClause/main.go
$./forLoopClause
John is 7 years old
interation N 0
interation N 1
interation N 2
interation N 3
interation N 4
interation N 5
interation N 6
```

**Komentar**:

- Prva vrednost i je 0, a ne 1; zapamtite da je naša inicijalna naredba.i := 0
  - Ako je inicijalna prva vrednost i = 1, prva rečenica ispisana na ekranu bila bi iteracija br. 1.
    Ovo je često zbunjujuće za početnike. U stvarnom svetu, započinjemo nabrajanje sa vrednošću jedan, a ne 0!  
    To (često) nije slučaj u računarstvu. Indeksi obično počinju vrednošću 0.
- For petlje sa klauzulama opsega se intenzivno koriste.

## Praktična primena

Napravićemo hotelsku aplikaciju. Počećemo sa programom za recepciju koji koriste recepcionari. Na recepciji, recepcionar će dočekati goste. Klijenti će pitati recepcionara za slobodnu sobu. On treba da zna:

- Koliko soba je sada dostupno
- Koliko dugo je svaka soba dostupna

Napisaćemo program koji će to uraditi na osnovu lažnih podataka.
Imajte na umu da smo predstavili istu aplikaciju u dve različite države.

**Napomene**:

- Ukupan broj soba je konstantan i jednak 134.
- Broj dostupnih soba ćemo generisati nasumično.
- Radi jednostavnosti: raspoložive sobe imaju lažni broj koji počinje sa 110
  - Ako su dostupne tri sobe, onda su sobe numerisane: 110, 111, 112
- Broj ljudi koje možete smestiti u sobu i broj dostupnih noćenja biće generisani nasumično
  - Minimum je 1
  - Maksimum je 10
- Stopa popunjenosti je definisana na sledeći način:
  - stopa popunjenosti​​​​​​
- Broj zauzetih soba​ = Ukupan broj soba​​ ​-​ ​​​​​stopa popunjenosti
- Nivo popunjenosti je definisan na sledeći način:
  - Stopa popunjenosti od 0% do 30%: Niska
  - Popunjenost od 30% do 60%: Srednja
  - Od 60% do 100% popunjenosti: Visoka

**Rešenje**:

```go
// control-statements/application-solution-2/main.go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    const hotelName = "Gopher Paris Inn"
    const totalRooms = 134
    const firstRoomNumber = 110

    rand.Seed(time.Now().UTC().UnixNano())
    roomsOccupied := rand.Intn(totalRooms)
    roomsAvailable := totalRooms - roomsOccupied

    occupancyRate := (float64(roomsOccupied) / float64(totalRooms)) * 100
    var occupancyLevel string
    if occupancyRate > 70 {
        occupancyLevel = "High"
    } else if occupancyRate > 20 {
        occupancyLevel = "Medium"
    } else {
        occupancyLevel = "Low"
    }

    fmt.Println("Hotel:", hotelName)
    fmt.Println("Number of rooms", totalRooms)
    fmt.Println("Rooms available", roomsAvailable)
    fmt.Println("                  Occupancy Level:", occupancyLevel)
    fmt.Printf("                  Occupancy Rate: %0.2f %%\n", occupancyRate)

    if roomsAvailable > 0 {
        fmt.Println("Rooms:")
        for i := 0; roomsAvailable > i; i++ {
            roomNumber := firstRoomNumber + i
            size := rand.Intn(6) + 1
            nights := rand.Intn(10) + 1
            fmt.Println(roomNumber, ":", size, "people /", nights, " nights ")
        }
    } else {
        fmt.Println("No rooms available for tonight")
    }
}
```

**Objašnjenja**:

- Konstante

  Počinjemo definisanjem tri konstante:
  
  - hotelName - drži ime našeg hotela
  - totalRooms - ukupan broj soba
  - firstRoomNumber - broj prve sobe. Sobi će biti numerisane 110, 111,... Potrebno je da sačuvamo početnu tačku kasnijeg nabrajanja.  
  Zašto konstante? Zato što se te vrednosti neće menjati tokom izvršavanja programa. One su fiksne.  
  Imajte na umu da su te tri konstante netipizovane.

- Generisanje slučajnog celog broja (broj soba koje se trenutno iznajmljuju)
  Zatim, pošto ćemo koristiti slučajni broj, potrebno je da generatoru slučajnih brojeva postavimo sledeću instrukciju:
  
  ```go
  rand.Seed(time.Now().UTC().UnixNano())
  ```
  
  Promenljiva "roomsOccupied" predstavlja broj soba koje klijenti koriste. To je slučajan broj između 0 i totalRooms. Da bismo generisali takav broj, koristimo instrukciju :
  
  ```go
  rand.Intn(totalRooms)
  ```
  
- Stopa popunjenosti: celobrojna ili float?  
  Stopa je procenat. Matematički, to je broj sa pokretnim zarezom koji varira od 0 do 1.
  
  - 1 je ekvivalentno 100%
  - 0,5655 je ekvivalentno 56,55%
  - 0,81115 je ekvivalentno 81,12% (zaokružili smo broj).

    Da biste dobili procenat, potrebno je da pomnožite sa 100. Da biste dobili procentualnu vrednost, možda ćete biti u iskušenju da napišete sledeći kod:

    ```go
    occupancyRate := (roomsOccupied / totalRooms) * 100
    ```

  Ovim kodom "occupancyRate" je ceo broj. Promenljiva je definisana i dodeljena u istoj naredbi. NJen tip nije napisan. Shodno tome, njen tip će biti zaključen na osnovu vrednosti koja joj je dodeljena. U tom slučaju, vrednost je "(roomsOccupied / totalRooms) * 100".  Hajde da razložimo:

  - "roomsOccupied" je tipa int (rezultat "rand.Intn(totalRooms)").
  - totalRoomsje netipizirana celobrojna konstanta.
  - 100 je ceo broj.

  Kao posledica toga, vrednost "(roomsOccupied / totalRooms) * 100" je ceo broj.

  Ne želimo ceo broj; sa celim brojem, izgubićemo brojeve posle decimalnog separatora. Umesto toga, želimo da koristimo broj sa pokretnim zarezom. Moramo da biramo između dva primitivna tipa:

  - float64  
  - float32

  Moramo znati minimalne i maksimalne vrednosti oba tipa da bismo izabrali. Sa tim podacima možemo pronaći najbolje rešenje:

  - float32  
    - Minimum:  1.401298464324817070923729583289916131280×10^(−45)  
    - Maksimum:83.40282346638528859811704183484516925440×10^38

  - float64
    - Minimum:  44.940656458412465441765687928682213723651×10^(−324)  
    - Maksimum:​​​​ 81.797693134862315708145274237317043567981×10^308

  float32 savršeno odgovara našim potrebama.

  Gde sam našao te brojeve? To su konstante definisane u izvornom kodu programa go (matematički paket) možete ih proveriti preko ovog linka: <https://golang.org/pkg/math/#pkg-constants>

**Mala digresija: naučna notacija**:

Možda se zbunite oko stepena desetice (10^308). Uzmimo primer da bismo to razumeli:

```go
1.55​ × 10^3 = 1.55 ​ ×10 × 10 × 10 = 15.5 × 10 × 10 = 155 × 10 = 1550
```

Imajte na umu da je decimalni separator pomeren udesno tri puta.

Uzmimo još jedan primer: svetlosna godina (udaljenost koju svetlost pređe za godinu dana):

```go
9.461​​ × 10^15 = 9,461,000,000,000,000
```

Ovo je veoma veliki broj. Pogodnije je zapisati ga stepenom broja 10!

797693134862315708145274237317043567981 × 10^308je veoma, veoma veliki broj! Da bismo ga zapisali, potrebno je da pomerimo decimalni separator udesno 308 puta!

Kada je stepen broja deset negativan, moramo pomeriti decimalni separator ulevo.940656458412465441765687928682213723651 × 10(−324)je veoma, veoma, veoma mali broj.

**Konverzija int u float32**:

Da bismo konvertovali int u float32, koristimo sledeću sintaksu:

```go
occupancyRate := (float32(roomsOccupied) / float32(totalRooms)) * 100
```

`float32(roomsOccupied)` je `float32`, isto za `float32(totalRooms)`. Kao posledica toga, tip stope popunjenosti je `float32` zato što je njena vrednost inicijalizacije `float32`:

- Deljenje dva `float32` daje `float32`
- Množenje broja `float32` sa 100 je i dalje `float32`

**Nivo popunjenosti: if / else if / else**:

"occupancyLevel" zavisi od vrednosti promenljive "occupancyRate".

- Od 0 do 20 => Nisko
- Od 20 do 70 => Srednje
- Od 70 do 100 => Visoko

Koristili smo naredbu `if / else if / else` da bismo postavili vrednost occupationalLevel. Zašto? Zato što imamo tri nivoa, tri moguće vrednosti. Naredba `if / else if / else` je kontrolna naredba koja je savršeno prilagođena. Evo izvoda izvornog koda:

```go
var occupancyLevel string

if occupancyRate > 70 {
   occupancyLevel = "High"
} else if occupancyRate > 20 {
   occupancyLevel = "Medium"
} else {
   occupancyLevel = "Low"
}
```

Kreiramo promenljivu "occupancyLevel" tipa string. Ako je "occupancyRate > 70", onda je ""occupancyLevel" visoka (High). Ako se proceni kao netačno (false), to znači da je vrednost promenljive manja ili jednaka 70. Znamo samo da je između 0 i 70.

Drugi bulov izraz je "occupancyRate > 20". Ovaj bulov izraz se izračunava samo kada je vrednost occupancyRate između 0 i 70. Ako je tačno, to znači da je vrednost između 20 i 70, što je ekvivalentno vrednosti "occupancyLevel" jednakoj Medium.

Ako je "occupancyRate < 20", to znači da je između 0 i 20. U ovom slučaju, imamo Low nivo.

**Nivo popunjenosti: alternativni račun preko switch-a**:

```go
switch {
    case occupancyRate > 70:
       occupancyLevel = "High"
    case occupancyRate > 20:
       occupancyLevel = "Medium"
    default:
       occupancyLevel = "Low"
}
```

Imajte na umu da ovaj slučaj switch-a nema izraz niti iskaz. Ekvivalentno je sa:

```go
switch true {
    //...
}
```

Pronaći ćemo slučaj koji će biti jednak `true`. Ako se nijedan ne pronađe, izvršava se `default` blok (slično bloku `else`).

Takođe možemo da obrišemo podrazumevani slučaj inicijalizacijom vrednosti occupancyLevel na "Low":

```go
occupancyLevel := "Low"

switch {
    case occupancyRate > 70:
       occupancyLevel = "High"
    case occupancyRate > 20:
       occupancyLevel = "Medium"
}
```

Vrednost promenljive će biti poništena u slučaju High i Medium.

**Prikazivanje informacija na ekranu**:

Koristili smo paket fmt:

```go
fmt.Println("Hotel:", hotelName)
fmt.Println("Number of rooms", totalRooms)
fmt.Println("Rooms available", roomsAvailable)
fmt.Println("                  Occupancy Level:", occupancyLevel)
fmt.Printf("                   Occupancy Rate: %0.2f %%\n", occupancyRate)
```

Imajte na umu da smo koristili `Printf`. Ova metoda će ispisati string kao `Println`. Zanimljivo je da možete ubrizgati promenljive/konstantne vrednosti unutar stringa. " Occupancy Rate: %0.2f %%\n" se naziva "specifikator formata". To je specifikacija formata. U ovoj specifikaciji možemo dodati glagole formatiranja. Glagol formatiranja se koristi da naznači štampaču da želimo da ubrizgamo vrednost u odštampani izlaz. Glagol označava kako želimo da formatira vrednost.

`%0.2f` označava da želimo da ispišemo vrednost sa pokretnim zarezom ( f ) i da želimo da prikažemo samo dve decimale posle decimalnog separatora ( 0.2 ). Imajte na umu da glagoli formatiranja uvek počinju znakom `%`. Evo nekih korisnih glagola formatiranja:

- `%s` za štampanje stringa.  
  Npr.:fmt.Printf("Hotel: %s", hotelName)

- `%d` za štampanje celog broja u bazi 10.  
  Npr.:fmt.Printf("Number of rooms: %d", totalRooms)

Ako želite da odštampate znak procenta, sintaksa je `%%`. Takođe imajte na umu da se za generisanje preloma reda dodaje znak `\n` na kraj našeg specifikatora formata.

**Petlja for**:

Kada je vrednost "roomsAvailable" veća od 0, program će izvršiti petlju for:

```go
for i := 0; roomsAvailable > i; i++ {
   roomNumber := firstRoomNumber + i
   size := rand.Intn(6) + 1
   nights := rand.Intn(10) + 1
   fmt.Println(roomNumber, ":", size, "people /", nights, " nights ")
}
```

- Inicijalna naredba će inicijalizovati i sa 0.
- Uslov je. "roomsAvailable > i". Petlja će prestati da se izvršava kada ovaj uslov postane netačan. Kada vrednost "i" postane jednaka "roomsAvailable" (ili veća).
- Naredba post je "i++". Ekvivalentna je sa i = i + 1. Inkrementuje i na kraju svake petlje.

Koliko iteracija će biti izvršeno?

Uzmimo primer:

- Ako su dostupne tri sobe, telo for petlje (između vitičastih zagrada) će se izvršiti 3 puta. i će biti jednako 0, 1 i 2.

  - Prva petlja:  
    - i inicijalizovano na 0  
      Tri je veće od 0 ( i) => možemo pokrenuti izvršenje tela

    - i se inkrementira (naknadna naredba). Nova vrednost i je 1

  - Druga petlja:
    - Tri je veće od 1 ( i) => možemo pokrenuti izvršenje tela => možemo pokrenuti izvršenje tela
    - i se inkrementira (naknadna naredba). Nova vrednost i je 2

  - Treća petlja
    - Tri je veće od 2 ( i) => možemo pokrenuti izvršenje tela => možemo pokrenuti izvršenje tela
    - i se inkrementira (naknadna naredba). Nova vrednost i je 3

  - Četvrta petlja
    - Tri nije veće od tri => stajemo.

  - Kada su četiri sobe dostupne, kod se izvršava četiri puta.

Pokušajte sami da detaljno opišete petlje da biste to dokazali.

Sada, uzmimo opšti slučaj: broj raspoloživih soba se čuva u promenljivoj "roomsAvailable", stoga je broj iteracija jednak vrednosti "roomsAvailable".

**Telo petlje for**:

- Unutar tela petlje for program:

  - Dodajemo vrednost "i" da "firstRoomNumber" bismo generisali broj sobe (110, 111, 112,...).  
    Rezultat sabiranja se čuva u promenljivoj roomNumber.

  - Generišemo slučajnu vrednost za size i nights.
    Imajte na umu da generisanoj slučajnoj vrednosti dodajemo 1. To je zato što će `rand.Intn(10)` generisati slučajni broj između 0 uključenih i 10 isključenih: (moguće vrednosti su: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9). Ako želimo broj između 1 i 10, potrebno je da mu dodamo 1.

  - Zatim ispisujemo vrednost kako je očekivano u specifikaciji:

    ```go
    fmt.Println(roomNumber, ":", size, "people /", nights, " nights ")
    ```

    Alternativa je korišćenje Printf-a sa glagolima formatiranja:

    ```go
    fmt.Printf("%d: %d people / %d nights\n", roomNumber, size, nights)
    ```

    Oba rade; drugi je lakši za čitanje i elegantniji (po mom mišljenju)

## Testirajte sebe

### Pitanja i odgovori

1. Šta je bulov izraz?
   - "Bulov izraz" je izraz koji proizvodi bulovu vrednost kada se
     izračuna. Izraz je sintaksni entitet u programskom jeziku koji se može proceniti da bi se odredila njegova vrednost.

2. Navedite četiri bulova operatora.
   - ==
   - !=
   - <
   - \>
   - <=
   - =

3. Koliko puta će reč "Go" biti ispisana na ekranu sa sledećim
   programima  
   a) `for i := 0; i < 10; i++ { fmt.Println("Go”) }`  
   b) `for i := 1; i < 10; i++ { fmt.Println("Go”) }`  
   c) `for { fmt.Println("Idi”) }`  
   d) `for i := 10; i < 10; i++ { fmt.Println("Go”) }`  
   - 10
   - 9
   - Beskonačan broj (beskonačna petlja)
   - 0

### Ključno

- Bulova vrednost je tip koji može biti jednak true ili false.
- Podrazumevano, bulova vrednost je false. (Ovo su nulte vrednosti bulovih vrednosti u programu Go)
- Bulov izraz proizvodi bulovsku vrednost.  
  Npr.:  
  `ageJohn > agePaul`  
  `ageJohn == agePaul`  
  Biće procenjeno kao `true` ili `false`, u zavisnosti od vrednosti ageJohn i agePaul
- `25 > 32` Biće procenjeno `false`
- Uslovne konstrukcije će koristiti bulove izraze
- Najčešće korišćene uslovne konstrukcije su:
  - `if / else`
  - `if bez else`
  - `switch-case`
  - `for` petlje
  - Konstrukcije `if / else if / else` mogu biti teške za čitanje/razumevanje (možda koristiti
    switch case?)
- Često ćete videti konstrukciju `if without else` u Go kodu (obično da bi se proverile greške).

[08 Promenljive, konstante i osnovni tipovi][08]  
[00 Sadržaj][00]  
[10 Funkcije][10]

[08]: 08_Promenljive_konstante_i_osnovni_tipovi.md
[00]: 00_Sadržaj.md
[10]: 10_Funkcije.md
