
# Demistifikacija Go modula

[Nazad](./README.md)

## Objašnjenje preuzimanja, instaliranja i sređivanja

Dobrodošli, Go programeri! Dok pravite aplikacije, stalno ćete komunicirati sa moćnim Go alatima komandne linije. Među najosnovnijim su:

1. `go get`
2. `go install`
3. `go mod tidy`

Iako svi oni upravljaju Go kodom, služe različitim svrhama u životnom ciklusu razvoja.

Razumevanje njihovih uloga nije samo izbegavanje grešaka; već savladavanje čistog, efikasnog i reproducibilnog toka rada. Ovaj vodič će pružiti jasno, osnovno razumevanje svake komande, objašnjavajući "šta", "zašto" i "kada" za njihovo pravilno korišćenje.

## Uloga Go modula

Pre nego što istražimo komande, moramo prvo razumeti njihovo okruženje:  Go moduli. Od Go 1.11, modul je postao standardna jedinica za Go razvoj. Modul je kolekcija Go paketa sa go.mod datotekom u svom korenu.

Zamislite datoteku "go.mod" kao plan za vaš projekat . Ona definiše:

- Jedinstveno ime vašeg projekta (putanja njegovog modula).
- Lista svih njegovih zavisnosti i njihovih specifičnih verzija.

Ovaj sistem modula je kontekst u kome naše tri komande funkcionišu.

### `go get` - Projektni bibliotekar

Zamislite `go get` kao  bibliotekara za vaš specifični projekat. Njegov zadatak je da upravlja knjigama (bibliotekama trećih strana) koje su potrebne vašem projektu za funkcionisanje.

- **Osnovna svrha**: Dodavanje i ažuriranje zavisnosti koje zahteva vaš trenutni modul.
- **Kako funkcioniše**: Kada pokrenete komandu `go get`, ona preuzima izvorni kod paketa i, što je
  najvažnije,  ažurira vaše datoteke `go.mod` i `go.sum` kako bi zabeležila da ovaj projekat sada zavisi od te specifične verzije biblioteke.
- **Kada ga koristiti**:  Koristite komandu `go get` kada želite da dodate novu biblioteku svom
  projektu ili eksplicitno nadogradite postojeću.
- **Primer upotrebe**

  ```sh
  // Add or update a dependency for your project
  go get example.com/some/package@latest
  ```

> [!Info]
> **Napomena o evoluciji Goa**  
> U verzijama Goa pre verzije 1.17, komanda `go get` se takođe koristila za
> instaliranje izvršnih datoteka. Ovo je ključna tačka zabune za mnoge. U
> modernom Gou to više nije slučaj. Njegova uloga je usavršena da bi se
> isključivo fokusirala na upravljanje zavisnostima projekata.

### `go install` - Sistemski alat

Ako je `go get` bibliotekar vašeg projekta, onda je `go install` kreator alata za ceo vaš sistem. Njegov zadatak nije da upravlja bibliotekama vašeg projekta, već da gradi i instalira Go programe kao izvršne komande koje možete pokrenuti sa bilo kog mesta.

- **Osnovna svrha**: Kompajlirati Go program i postaviti rezultujući izvršni binarni fajl na
  centralnu lokaciju (GOPATH/bin ili GOBIN), čineći ga dostupnim iz PATH vašeg sistema.
- **Kako funkcioniše**:  `go install` kompajlira glavni paket koji navedete. Ne menja "go.mod"
  datoteku vašeg trenutnog projekta, jer je `go install` alat namenjen vama, programeru, a ne samom projektu.
- **Kada ga koristiti**:  Koristite komandu go install da biste dobili alate komandne linije
  napisane u programskom jeziku Go, kao što su linteri, generatori koda ili drugi uslužni programi za razvoj.
- **Primer upotrebe**

  ```sh
  // Install a development tool from a remote repository
  go install golang.org/x/tools/cmd/goimports@latest

  // Compile and install the main package from your current project
  go install .
  ```

### `go mod tidy` - Vredna domaćica

Zamislite `go mod tidy` kao  domaćicu za svoj projekat. Nakon što ste kodirali — dodavali, uklanjali i menjali import naredbe — vaša "go.mod" datoteka može postati nesinhronizovana sa vašim stvarnim kodom. `go mod tidy` to čisti.

- **Osnovna svrha**:  Da osigurate da vaša go.mod datoteka savršeno odražava zavisnosti vašeg
  projekta.
- **Kako funkcioniše**:  Pažljivo skenira sav izvorni kod vašeg projekta i:
  - Dodaje  sve nedostajuće zavisnosti u go.mod koje su uvezene u kod.
  - Uklanja  sve zavisnosti iz go.mod koje se više nigde ne koriste.
  - Ažurira  datoteku go.sum kontrolnim zbirovima za sve potrebne zavisnosti.
- **Kada ga koristiti**:  Najbolja praksa je pokrenuti `go mod tidy` nakon što ste napravili značajne
  izmene koda, a posebno  pre nego što pošaljete "go.mod" datoteku u kontrolu verzija. Ovo osigurava da vaš projekat ostane u čistom i konzistentnom stanju.

## Uporedni rezime

| Karakteristika | go get (Bibliotekar) | go install (Alatničar) | go mod tidy (Domaćica) |
| -------------- | -------------------- | ---------------------- | ---------------------- |
| Primarna uloga | Upravlja  zavisnostima projekta | Instalira  sistemske alate | Čisti zavisnosti projekta |
| Obim | Specifično za projekat | Na nivou celog sistema (globalno) | Specifično za projekat |
| Uticaj na go.mod | Menja ga da bi pratio zavisnosti | NE  menja ga | Sinhronizuje ga i skraćuje |
| Glavni izlaz | Ažurirana go.mod datoteka | Izvršni program u vašem GOBIN-u | Čista, konzistentna go.mod datoteka |

## Zaključak

Internalizacijom ovih različitih uloga, možete razviti intuitivniji i bezgrešniji radni tok u Go-u.

- Koristite komandu  `go get`  da biste upravljali bibliotekama koje su potrebne vašem projektu.
- Koristite komandu  `go install`  da biste opremili svoje razvojno okruženje alatima.
- Koristite komandu  `go mod tidy`  da bi lista zavisnosti vašeg projekta bila čista i tačna.

Savladavanje ovih komandi je fundamentalni korak ka pisanju profesionalnih, održivih i ponovljivih Go aplikacija.

[Nazad](./README.md)
