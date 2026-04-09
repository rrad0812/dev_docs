
# Panic i recover

- [[Greške]][17]  
- [[Sadržaj]][00]  
- [[Testiranje]][19]

Ranije smo saznali da je idiomatski način rešavanja abnormalnih uslova u Go programu korišćenje grešaka. Iako su greške dovoljne za većinu slučajeva, postoje neke situacije u kojima program ne može da nastavi sa radom.

U tim slučajevima možemo koristiti ugrađenu `panic` funkciju.

## Panic funkcija

```go
func panic(interface{})
```

`Panic` je ugrađena funkcija koja zaustavlja normalno izvršavanje trenutne goroutine. Kada funkcija pozove panic, normalno izvršavanje funkcije se odmah zaustavlja i kontrola se vraća pozivaocu. Ovo se ponavlja dok se program ne završi sa porukom panike i tragom steka.

**Napomena**: O `goroutine` ćemo razgovarati kasnije u kursu.

Da vidimo kako možemo da koristimo `panic` funkciju.

```go
package main

func main() {
    WillPanic()
}
func WillPanic() {
    panic("Woah")
}
```

I ako ovo pokrenemo, možemo videti `panic` u akciji.

```sh
go run main.go
panic: Woah
goroutine 1 [running]:
```

```go
main.WillPanic(...)
    .../main.go:8
main.main()
    .../main.go:4 +0x38
exit status 2
```

Naš program je ispisao poruku panike, nakon čega je usledio trag steka, a zatim je prekinut.

Pitanje je šta učiniti kada se dogodi neočekivana panika?

### Recover funkcija

Moguće je povratiti kontrolu nad programom koji izaziva paniku koristeći ugrađenu `recover` funkciju, zajedno sa `defer` ključnom reči.

```go
func recover() interface{}
```

Hajde da pokušamo primer kreiranjem `handlePanic` funkcije. A zatim je možemo pozvati koristeći `defer`.

```go
package main
import "fmt"

func main() {
    WillPanic()
}
func handlePanic() {
    data := recover()
    fmt.Println("Recovered:", data)
}
func WillPanic() {
    defer handlePanic()
    panic("Woah")
}
```

```sh
go run main.go
Recovered: Woah
```

Kao što vidimo, naša panika je oporavljena i sada naš program može da nastavi sa izvršavanjem.

Na kraju, pomenuću da se `panic` i `recover` mogu smatrati sličnim `try/catch` idiomu u drugim jezicima. Ali jedan važan faktor je da treba da izbegavamo paniku i da se oporavljamo i koristimo greške kad god je to moguće.

Ako je tako, onda nas to dovodi do pitanja, kada bi trebalo da koristimo panic?

## Slučajevi upotrebe panic funkcije

Postoje dva validna slučaja upotrebe za panic:

### Nepopravljiva greška

Može biti situacija u kojoj program jednostavno ne može da nastavi svoje izvršavanje.

Na primer, čitanje konfiguracione datoteke što je važno za pokretanje programa, jer nema šta drugo da se uradi ako samo čitanje datoteke ne uspe.

### Greška programera

Ovo je najčešća situacija. Na primer, dereferenciranje pointera kada je vrednost `nil` izazvalo bi paniku.

- [[Greške]][17]  
- [[Sadržaj]][00]  
- [[Testiranje]][19]

[17]: 17_Greške.md
[00]: toc.md
[19]: 19_Testiranje.md
