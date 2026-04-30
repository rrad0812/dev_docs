# Go tutorijal 11

## 11 Nizovi i isečci

### 11.1 Nizovi

Niz je kolekcija elemenata koji pripadaju istom tipu. Na primer, kolekcija celih brojeva 5, 8, 9, 79, 76 formira niz. Mešanje vrednosti različitih tipova, na primer, niz koji sadrži i stringove i cele brojeve, nije dozvoljeno u Go-u.

Niz pripada tipu `[n]T`. `n` označava broj elemenata u nizu i `T` predstavlja tip svakog elementa. Broj elemenata `n` je takođe deo tipa.

Postoje različiti načini za deklarisanje nizova. Pogledajmo ih jedan po jedan.

#### 11.1.1 Standardna deklaracija niza

```go
package main

import (
    "fmt"
)

func main() {

    fmt.Println("\n --- Array ---")
    
    var a [3]int //int array with length 3
    fmt.Println(a)
}
```

`var a [3]int` deklariše celobrojni niz dužine 3.

Svim elementima u nizu se automatski dodeljuje nulta vrednost tipa niza. U ovom slučaju a je celobrojni niz i stoga su svim elementima a dodeljena 0, nulta vrednosti tipa int. Pokretanje gornjeg programa će ispisati:

```sh
[0 0 0]
```

#### 11.1.2 Indeks niza

Indeks niza počinje od `0` i završava na `length - 1`.

#### 11.1.2 Dodela vrednosti nizu

```go
func main() {

    fmt.Println("\n --- ArrayAssig ---")

    var a [3]int // int array with length 3

    a[0] = 12    // array index starts at 0
    a[1] = 78
    a[2] = 50    // array index finite on length -1

    fmt.Println("Array a is", a)
}
```

a[0] = 12 dodeljuje vrednost prvom elementu niza.

Ova program će ispisati:

```sh
    >> [12 78 50]
```

#### 11.1.3 Deklaracija niza skraćenom deklaracijom

```go
func main() {

    fmt.Println("\n --- ArrayShortHandDecl ---")

    a := [3]int{12, 78, 50} // short hand declaration to create array
    fmt.Println("Array a:=[3]{12, 78, 50} is", a)
}
```

Gornji program će ispisati isti izlaz

```sh
    >> [12 78 50]
```

Nije neophodno da se svim elementima u nizu dodeli vrednost tokom skraćene deklaracije.

```go
func main() {

    fmt.Println("\n --- ArrayShortHandPartially ---")

    a := [3]int{12}
    fmt.Println("Array a:=[3]int{12} is", a)
}
```

U gornjem programu, `a := [3]int{12}` deklariše se niz dužine 3, ali mu je data samo jedna vrednost 12. Preostala 2 elementa se dodeljuju 0 automatski. Ovaj program će ispisati

```sh
[12 0 0]
```

#### 11.1.4 Deklaracija niza sa neodređenom dužinom

Možete čak i zanemariti dužinu niza u deklaraciji i zameniti ga sa ... i pustiti kompajler da pronađe dužinu za vas.

```go
func main() {

    fmt.Println("\n --- aasArrayElipsisDecl ---")

    a := [...]int{12, 78, 50} // ... makes the compiler determine the length
    fmt.Println("Array a:=[...]int{12, 78, 50}", a)
}
```

#### 11.1.5 Veličina niza kao deo tipa niza

Veličina niza je deo tipa. Stoga su `[5]int` i `[25]int` različiti tipovi. Zbog toga se veličina nizova ne može menjati. Ne brinite o ovom ograničenju, jer isečci postoje da bi ovo ograničenje prevazišli.

```go
func main() {

    fmt.Println("\n --- ArrayTypes ---")

    a := [3]int{5, 78, 8}
    var b [5]int

    fmt.Printf("Array a is: %d, array b is: %d\n", a, b)
    // b = a //not possible since [3]int and [5]int are distinct types
    fmt.Printf("type of array a is: %T, array b is: %T\n", a, b)
}
```

U ovom programu, pokušavamo dodeliti varijablu tipa `[3]int` varijabli tipa `[5]int`. Ovo nije dozvoljeno i stoga će kompajler ispisati sledeću grešku

```sh
./prog.go:6:7: cannot use a (type [3]int) as type [5]int in assignment
```

#### 11.1.6 Nizovi su vrednosni tipovi

Nizovi u Go-u su vrednosni, a ne referentni tipovi. To znači da kada se dodele novoj varijabli, "kopija" originalnog niza se dodjeljuje novoj varijabli. Ako se naprave promene na novoj varijabli, to se neće odraziti na originalni niz.

```go
func main() {

    fmt.Println("\n --- ArrayAsValues ---")

    a := [...]string{"USA", "China", "India", "Germany", "France"}
    b := a // a copy of a is assigned to b
    b[0] = "Singapore"
    fmt.Println("a is ", a)
    fmt.Println("b is ", b)
}
```

U gornjem programu, kopija niza a je dodeljena nizu b. potom je prvi element niza b promenjen u Singapore. Ovo se neće odraziti na originalni niz a. Program će ispisati:

```sh
a is [USA China India Germany France]
b is [Singapore China India Germany France]
```

Slično tome, kada se nizovi prosleđuju funkcijama kao parametri, oni se prosleđuju po vrednosti i originalni niz ostaje nepromenjen.

```go
func changeLocal(num [5]int) {
    num[0] = 55
    fmt.Println("inside function changeLocal passed array is changed: ", num)
}

func main() {

    fmt.Println("\n --- ArrayPassByValue ---")

    num := [...]int{5, 6, 7, 8, 8}
    fmt.Println("before passing to function changeLocal array num is:", num)
    changeLocal(num) //num is passed by value
    fmt.Println("after passing to function changeLocal array num is:", num)
}
```

U gornjem programu, niz num se prosleđuje funkciji "changeLocal" po vrednosti stoga se neće menjati iako se unutar funkcije menja. Ovaj program će ispisati:

```sh
before passing to function changeLocal array num is: [5 6 7 8 8]
inside function changeLocal passed array is changed: [55 6 7 8 8]
after passing to function array num is: [5 6 7 8 8]
```

#### 11.1.7 Dužina niza

Dužina niza se pronalazi prosleđivanjem niza kao parametra funkciji `len`.

```go
func main() {

    fmt.Println("\n --- aasArrayLenght ---")

    a := [...]float64{67.7, 89.8, 21, 78}
    fmt.Println("array a is", a)
    fmt.Println("length of a is", len(a))
}
```

Izlaz gornjeg programa je

```go
array a is [67.7, 89.8, 21, 78]
length of a is 4
```

#### 11.1.8 Iteriranje nizova korištenjem range

Petlja for se može koristiti za iteraciju kroz elemente niza.

```go
func main() {

    fmt.Println("\n --- aasArrayIter ---")

    a := [...]float64{67.7, 89.8, 21, 78}
    for i := 0; i < len(a); i++ { //looping from 0 to the length of the array
        fmt.Printf("%d th element of a is %v\n", i, a[i])
    }
}
```

Gornji program koristi for petlju za iteraciju kroz elemente niza počevši od indeksa `0` do `length-1`. Ovaj program radi i ispisaće,

```sh
0 th element of a is 67.70
1 th element of a is 89.80
2 th element of a is 21.00
3 th element of a is 78.00
```

Go pruža bolji i koncizniji način iteracije kroz niz korištenjem `for range` petlje `range` vraća i indeks i vrednost na tom indeksu. Prepišimo gornji kod koristeći range. Takođe ćemo pronaći zbir svih elemenata niza.

```go
func main() {

    fmt.Println("\n --- aasArrayRange ---")

    a := [...]float64{67.7, 89.8, 21, 78}
    sum := float64(0)

    for i, v := range a { //range returns both the index and value
        fmt.Printf("%d the element of a is %v\n", i, v)
        sum += v
    }
    fmt.Println("\nsum of all elements of a", sum)
}
```

U gornjem programu je dat oblik `for` petlje. Vratiće i indeks i vrednost na tom
indeksu. Ispisujemo vrednosti i izračunavamo zbir svih elemenata niza.

Izlaz programa je:

```sh
0 the element of a is 67.70
1 the element of a is 89.80
2 the element of a is 21.00
3 the element of a is 78.00

sum of all elements of a 256.5
```

U slučaju da želite samo vrednost i želite zanemariti indeks, to možete učiniti zamenom indeksa sa `_` - praznim identifikatorom.

```go
for _, v := range a { //ignores index

}
```

Gornja for petlja ignoriše indeks. Slično tome, vrednost se također može ignorisati.

#### 11.1.9 Višedimenzionalni nizovi

Nizovi koje smo do sada kreirali su svi jednodimenzionalni. Moguće je kreirati
i višedimenzionalne nizove.

```go
func printarray(a [3][2]string) {
    for _, v1 := range a {
        for _, v2 := range v1 {
            fmt.Printf("%s ", v2)
        }
        fmt.Printf("\n")
    }
}

func main() {

    fmt.Println("\n --- aasArrayMultiDim ---")

    a := [3][2]string{
        {"lion", "tiger"},
        {"cat", "dog"},
        {"pigeon", "peacock"}, //this comma is necessary.
        // The compiler will complain if you omit this comma
    }

    printarray(a)

    var b [3][2]string
    b[0][0] = "apple"
    b[0][1] = "samsung"
    b[1][0] = "microsoft"
    b[1][1] = "google"
    b[2][0] = "AT&T"
    b[2][1] = "T-Mobile"

    fmt.Printf("\n")

    printarray(b)
}
```

U gornjem programu, deklarisan je dvodimenzionalni niz stringova "a" korištenjem skraćene sintakse. Zarez na kraju je neophodan. To je zbog činjenice da lekser automatski ubacuje tačka-zarez prema jednostavnim pravilima. Molimo pročitajte <https://golang.org/doc/effective_go.html#semicolons> ako ste zainteresirani da saznate više o tome zašto je tačka-zarez potreban.

Još jedan 2D niz "b" je deklarisan u liniji broj 23 i stringovi se dodaju u njega jedan po jedan za svaki indeks. Ovo je još jedan način inicijalizace 2D niza.

Funkcija "printarray" koristi dve petlje tipa "for range" za ispis sadržaja dvodimenzionalnih nizova. Gornji program će ispisati:

```sh
lion tiger
cat dog
pigeon peacock

apple samsung
microsoft google
AT&T T-Mobile
```

Nizovi se čine dovoljno fleksibilnima, ali oni dolaze s ograničenjem da su fiksne dužine. Nije moguće povećati dužinu niza. Tu na scenu stupaju isečci. U stvari, u Go-u, isečci su češći od konvencionalnih nizova.

### 11.2 Isečci

Isečak je praktičan, fleksibilan i moćan omotač preko niza. Isečci ne poseduju nikakve podatke same po sebi. Oni su samo reference na postojeće nizove.

#### 11.2.1 Kreiranje isečka

Isečak sa elementima tipa T predstavljen je sa []T.

```go
func main() {

    fmt.Println("\n --- Slice ---")

    a := [5]int{76, 77, 78, 79, 80}
    var b []int = a[1:4] //creates a slice from a[1] to a[3]
    fmt.Println(b)
}
```

Sintaksa `a[start:end]` kreira isečak kao deo niza `a` počevši od indeksa `start` do
indeksa `end - 1`.

Dakle u gornjem programu `a[1:4]` kreira reprezentaciju dela niza a počevši od
indeksa `1` do `4 - 1`. Stoga promenljiva b, koja je isečak, ima  vrednosti `[77 78 79]`.

#### 11,2,2 Kreiranje isečka preko literala isečka

Pogledajmo još jedan način kreiranja isečka.

```go
func main() {

    fmt.Println("\n --- SlicesCreate ---")

    c := []int{6, 7, 8} //creates an array and returns a slice reference
    fmt.Println(c)
}
```

U gornjem programu, `c := []int{6, 7, 8}` kreira se niz sa 3 cela broja i vraća
referencu na njkega, koja je pohranjena u c.

#### 11.2.3 Modifikovanje isečka

Isečak ne poseduje vlastite podatke. To je samo reprezentacija osnovnog niza. Sve izmene napravljene na isečku će se odraziti na osnovnom nizu.

```go
func main() {

    fmt.Println("\n --- SliceChange ---")

    darr := [...]int{57, 89, 90, 82, 100, 78, 67, 69, 59}
    dslice := darr[2:5]

    fmt.Println("array before", darr)

    for i := range dslice {
        dslice[i]++
    }

    fmt.Println("array after", darr)
}
```

U gornjem programu, kreiramo "dslice" iz niza "darr", iz indeksa 2, 3, 4. Petlja
for povećava vrednost na vrednostima na ovim indeksima za jedan. Kada ispišemo
niz nakon petlje for, možemo videti da se promene na isečku odražavaju na nizu.

Izlaz programa je:

```sh
array before [57 89 90 82 100 78 67 69 59]
array after [57 89 91 83 101 78 67 69 59]
```

Kada više isečaka deli isti osnovni niz, promena koja se napravi na svakom od
isečaka će se odraziti na nizu.

```go
func main() {

    fmt.Println("\n --- SliceManipulation ---")

    numa := [3]int{78, 79, 80} // create an array
    nums1 := numa[:]           //creates a slice which contains all elements of the array
    nums2 := numa[:]           //creates a slice which contains all elements of the array

    fmt.Println("array, nums1 and nums2")
    fmt.Println("before change", numa, nums1, nums2)

    nums1[0] = 100 // Change slice nums1 and array numa
    fmt.Println("after change to slice nums1", numa, nums1, nums2)

    nums2[1] = 101 // Change slice nums2 and array numa
    fmt.Println("after change to slice nums2", numa, nums1, nums2)
}
```

Izrazom "numa[:]", gde nedostaju početne i krajnje vrednosti, zadane vrednosti za
početak i kraj su "0" i "len(numa)" respektivno. Oba isečka, "nums1" i "nums2" dele isti deo niza "numa". Izlaz programa je:

```sh
array, nums1 and nums2
before change [78 79 80] [78 79 80] [78 79 80]
after change to slice nums1 [100 79 80] [100 79 80] [100 79 80]
after change to slice nums2 [100 101 80] [100 101 80] [100 101 80]
```

Iz izlaza je jasno da kada isečci dele isti niz, modifikacije napravljene na
isečcima se odražavaju na nizu.

#### 11.2.4 Dužina i kapacitet isečka

- **Dužina isečka** je broj elemenata u isečku.
- **Kapacitet isečka** je broj elemenata u osnovnom nizu počevši od indeksa niza od kojeg je isečak kreiran do kraja niza.

Napišimo malo koda da bismo ovo bolje razumeli.

```go
func main() {

    fmt.Println("\n --- SliceLenAndCap ---")

    fruitarray := [...]string{"apple", "orange", "grape", "mango", "water melon",
        "pine apple", "chikoo"}

    fruitslice := fruitarray[1:3]
    fmt.Printf("length of slice is %d and capacity is %d\n", len(fruitslice),
        cap(fruitslice)) //length of fruitslice is 2 and capacity is 6
}
```

U gornjem programu, "fruitslice "je kreiran od indeksa 1 do 3 od niza "fruitarray".
Stoga je dužina "fruitslice" jednaka 2.

Dužina "fruitarray" je 7. "fruiteslice" je kreirana od indeksa 1 niza "fruitarray".
Stoga je kapacitet "fruitslice" broj elemenata u fruitarray počevši od indeksa 1,
tj. od "orange", ta vrednost je jednaka 6. Stoga je kapacitet "fruitslice" jednaka 6. Program ispisuje dužinu isečka 2 i kapacitet 6.

> [!Note]
> Isečak se može ponovo rezati do svog kapaciteta. Sve preko toga će uzrokovati
> da program izbaci grešku tokom izvođenja.

```go
func aasSliceReSlicing() {

    fmt.Println("\n --- aasSliceReSlicing ---")

    fruitarray := [...]string{"apple", "orange", "grape", "mango",
        "water melon", "pine apple", "chikoo"}

    fruitslice := fruitarray[1:3]

    //length of is 2 and capacity is 6
    fmt.Printf("length of slice %d capacity %d\n", len(fruitslice),
        cap(fruitslice))

    //re-slicing fruitslice till its capacity
    fruitslice = fruitslice[:cap(fruitslice)]

    fmt.Println("After re-slicing length is", len(fruitslice), "and capacity is",
        cap(fruitslice))
}
```

U gornjem programu "fruitslice" je ponovo skraćeno na svoj kapacitet. Gornji program daje:

```sh
length of slice 2 capacity 6
After re-slicing length is 6 and capacity is 6
```

#### 11.2.5 Kreiranje isečka pomoću funkcije make

Funkcija

```go
func make([]T, len, cap) []T
```

se može koristiti za kreiranje isečka prosleđivanjem tipa, dužine i opciono kapaciteta. Parametar kapaciteta je opcionalan i podrazumeva se da je isti kao dužina. Funkcija `make` kreira niz i vraća referencu na isečak.

```go
func make() {

    fmt.Println("\n --- SliceMake ---")

    i := make([]int, 5)
    fmt.Printf("Type of slice i is %T, and value is %v\n", i, i)
    fmt.Printf("Lenght of slice i is %d, and capacitet slice i is %d\n", len(i),
       cap(i))
}
```

Vrednosti se po defaultu poništavaju kada se isečka kreira pomoću naredbe make.

Gornji program će ispisati

```sh
Type of isečak i is []int, and value is [0 0 0 0 0]
Lenght of slice i is 5, and capacitet slice i is 5.
```

#### 11.2.6 Dodavanje na isečak

Kao što već znamo, nizovi su ograničeni na fiksnu dužinu i njihova dužina se ne
može povećati. Isečci su dinamički i novi elementi se mogu dodati isečku pomoću
`append` funkcije. Definicija funkcije `append` je

```go
func append(s []T, x ...T) []T
```

x ...T u definiciji funkcije `append` znači da funkcija prihvata varijabilni broj
argumenata za parametar x. Ove vrste funkcija nazivaju se `variadic` funkcije.

Jedno pitanje bi vas moglo mučiti. Ako su isečci podržani nizovima, a sami
nizovi su fiksne dužine, kako to da je isečak dinamičke dužine? Pa, ono što se
dešava "ispod haube" je da se, kada se novi elementi dodaju isečku, kreira novi
niz. Elementi postojećeg niza se kopiraju u ovaj novi niz i vraća se nova
referenca na isečak za ovaj novi niz. Kapacitet novog isečka je sada dvostruko
veći od kapaciteta starog. Sledeći program će razjasniti stvari.

```go
func main() {

    fmt.Println("\n --- SliceMake ---")

    cars := []string{"Ferrari", "Honda", "Ford"}
    fmt.Println("cars:", cars, "has old length", len(cars),
        "and capacity", cap(cars)) //capacity of cars is 3
    cars = append(cars, "Toyota")
    fmt.Println("cars:", cars, "has new length", len(cars),
        "and capacity", cap(cars)) //capacity of cars is doubled to 6
}
```

U gornjem programu, kapacitet "cars" je inicijalno 3. Dodajemo novi element u `cars` sa `append(cars, "Toyota")`. Ponovo dodeljujemo isečak vraćen od `append`. Sada se kapacitet `cars` udvostručuje i postaje 6. Izlaz gornjeg programa je:

```sh
cars: [Ferrari Honda Ford] has old length 3 and capacity 3
cars: [Ferrari Honda Ford Toyota] has new length 4 and capacity 6
```

Nulta vrednost tipa isečka je `nil`. `nil` isečak ima dužinu i kapacitet `0`. Moguće
je dodati vrednosti isečku `nil` pomoću funkcije `append`.

```go
func main() {

    fmt.Println("\n --- SliceNil ---")

    var names []string //zero value of a slice is nil

    fmt.Println("slice is nil going to append")
    names = append(names, "John", "Sebastian", "Vinay")
    fmt.Println("names contents:", names)
}
```

U gornjem programu "names" je `nil` i dodali smo 3 stringa na "names". Izlaz programa
je:

```sh
slice is nil going to append
names contents: [John Sebastian Vinay]
```

Takođe je moguće dodati jedan isečak drugom pomoću `...` operatora. Više o ovom
operatoru možete saznati u tutorijalu o `variadic` funkcijama.

```go
func main() {

    fmt.Println("\n --- SliceElipsis ---")

    veggies := []string{"potatoes", "tomatoes", "brinjal"}
    fruits := []string{"oranges", "apples"}

    food := append(veggies, fruits...)

    fmt.Println("food:", food)
}
```

U gornjem programu, "food" se kreira dodavanjem "fruits" na "veggies". Izlaz
programa je:

```sh
food: [potatoes tomatoes brinjal oranges apples]
```

#### 11.2.7 Prosleđivanje isečka funkciji

Isečci se mogu smatrati interno predstavljeni tipom strukture. Evo kako to izgleda,

```go
type slice struct {
    Length        int
    Capacity      int
    ZerothElement *byte
}
```

Isečak sadrži dužinu, kapacitet i pokazivač na nulti element niza. Kada se isečak
prosledi funkciji, čak i ako se prosleđuje po vrednosti, varijabla će se odnositi
na isti osnovni niz. Stoga, kada se isečak prosledi funkciji kao parametar, promene napravljene unutar funkcije vidljive su i izvan funkce. Napišimo program
da to proverimo.

```go
func subtactOne(numbers []int) {
    for i := range numbers {
        numbers[i] -= 2
    }
}

func main() {

    fmt.Println("\n --- SlicePassByRef ---")

    nos := []int{8, 7, 6}
    fmt.Println("slice before function call", nos)

    // function modifies the slice
    subtactOne(nos)
    
    // modifications are visible outside
    fmt.Println("slice after function call", nos)
}
```

Poziv funkcije u gornjem programu smanjuje svaki element isečka za 2. Kada se isečak ispiše nakon poziva funkcije, ove promene su vidljive. Ako se sećate, ovo se razlikuje od niza gde promene napravljene na nizu unutar funkcije nisu vidljive izvan funkcije. Izlaz gornjeg programa je:

```sh
slice before function call [8 7 6]
slice after function call [6 5 4]
```

#### 11.2.8 Višedimenzionalni isečci

Slično nizovima, isečci mogu imati više dimenzija.

```go
func main() {

    fmt.Println("\n --- SliceMultiDim ---")

    pls := [][]string{
        {"C", "C++"},
        {"JavaScript"},
        {"Go", "Rust"},
    }

    for _, v1 := range pls {
        for _, v2 := range v1 {
            fmt.Printf("%s ", v2)
        }
        fmt.Printf("\n")
    }
}
```

Izlaz programa je:

```sh
C C++
JavaScript
Go Rust
```

#### 11.2.9 Optimizacija memorije

Isečci sadrže referencu na osnovni niz. Sve dok je isečak u memoriji, niz se ne može sakupljati kao smeće. Ovo bi moglo biti zabrinjavajuće kada je u pitanju upravljanje memorijom. Pretpostavimo da imamo vrlo veliki niz i da nas zanima obrada samo njegovog malog dela. Stoga kreiramo isečak iz tog niza i počinjemo sa obradom isečka. Važno je ovde napomenuti da će niz i dalje biti u memoriji jer se isečak referencira na njega.

Jedan od načina za rešavanje ovog problema je korištenje funkcije `copy`,

```go
func copy(dst, src []T) int
```

za pravljenje kopije tog dela. Na ovaj način možemo koristiti novi deo, a originalni niz se može sakupljati kao smeće.

```go
func countries() []string {
    countries := []string{"USA", "Singapore", "Germany", "India", "Australia"}
    neededCountries := countries[:len(countries)-2]
    countriesCpy := make([]string, len(neededCountries))
    copy(countriesCpy, neededCountries) //copies neededCountries to countriesCpy
    return countriesCpy
}

func main() {
    countriesNeeded := countries()
    fmt.Println(countriesNeeded)
}
```

U gornjeg programu,

```go
neededCountries := countries[:len(countries)-2]
```

kreira isečak koji iz "countries" niza isključuje poslednja 2 elementa. Kopiranje
"neededCountries" isečka u novi "countryCpy" isečak kreira i novi osnovni niz.

Sada "countriess" niz može da ide u smeće jer ga "countryCpy" isečak ne referencira.
