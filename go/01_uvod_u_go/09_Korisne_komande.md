
# Korisne komande

- [[Radni prostori]][08]  
- [[Sadržaj]][00]  
- [[Pointeri]][10]

- `go fmt` formatira izvorni kod i to sprovodi tako da se možemo fokusirati na to kako bi naš kod trebalo da funkcioniše, a ne na to kako bi naš kod trebalo da izgleda.

  ```sh
  go fmt
  ```

  Ovo može delovati malo čudno u početku, posebno ako dolazite iz sveta Javascripta ili Pythona, ali prilično je lepo ne brinuti o pravilima lintovanja.

- `go vet` izveštava o verovatnim greškama u našim paketima. Ako napravim grešku u sintaksi, a zatim pokrenem `go vet`, trebalo bi da me obavesti o greškama.

  ```sh
  go vet
  ```

- `go env` ispisuje sve informacije o go okruženju, o nekim od ovih promenljivih za vreme izgradnje ćemo saznati kasnije.

- `go doc` prikazuje dokumentaciju za paket ili simbol, evo primera fmt paketa.

   ```sh
   go doc -src fmt Printf
   ```

- `go help` komandu koristimo da vidimo koje su druge komande dostupne.

  ```sh
  go help
  ```

Kao što vidimo, imamo:

- `go fix` - pronalazi Go programe koji koriste stare API-je i prepisuje ih da koriste novije.
- `go generate` - obično se koristi za generisanje koda.
- `go install` - kompajlira i instalira pakete i zavisnosti.
- `go clean` - koristi se za čišćenje datoteka koje generišu kompajleri.

Neke druge veoma važne komande su `go build` i `go test`, ali ćemo o njima detaljnije saznati kasnije u kursu.

## Build

Izrada statičkih binarnih datoteka jedna je od najboljih karakteristika Go jezika koja nam omogućava efikasno isporučivanje našeg koda.

To možemo vrlo lako uraditi pomoću `go build` komande.

```go
package main
import "fmt"

func main() {
    fmt.Println("I am a binary!")
}
```

```sh
go build
```

Ovo proizvedi binarnu datoteku sa imenom našeg modula.

Takođe možemo samo odrediti naziv prevedenog binarnog izlaza.

```sh
go build -o app
```

Sada, da bismo ovo pokrenuli, jednostavno treba da ga izvršimo.

```sh
./app
I am a binary!
```

### Promenljive okruženja značajne za compile-time

Važne promenljive okruženja su pre svega `$GOOS` i `$GOARCH`. Ove promenljive okruženja nam pomažu da napravimo go programe za različite operativne sisteme i  arhitekture procesora.

Možemo navesti sve podržane arhitekture pomoću `go tool` komande.

```sh
go tool dist list
android/amd64
ios/amd64
js/wasm
linux/amd64
windows/arm64
...
```

Evo primera za izgradnju izvršne datoteke za Windows iz macOS-a!

```sh
GOOS=windows
GOARCH=amd64 
go build -o app.exe
```

Promenljiva `$CGO_ENABLED` nam omogućava da konfigurišemo `CGO`, što je način u Go-u za pozivanje C koda. Ovo nam pomaže da napravimo statički povezan binarni fajl koji radi bez ikakvih spoljnih zavisnosti.

Ovo je prilično korisno, recimo, kada želimo da pokrenemo naše go binarne datoteke u docker kontejneru sa minimalnim spoljnim zavisnostima.

Evo primera kako se koristi:

```sh
CGO_ENABLED=0
go build -o app
```

- [[Radni prostori]][08]  
- [[Sadržaj]][00]  
- [[Pointeri]][10]

[08]: 08_Radni_prostori.md
[00]: toc.md
[10]: 10_Pointeri.md
