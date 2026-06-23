
# Kako instalirati Go na Ubuntu

[Nazad](./README.md)

Ažurirano 25. februara 2026

## Uvod

Go, koji se ponekad naziva i "Golang", je programski jezik otvorenog koda koji je objavio Google 2012. Široko se koristi za pravljenje pouzdanih alata komandne linije, pozadinskih usluga i aplikacija koje su izvorne u oblaku, a poznat je po svojoj jednostavnoj sintaksi, jakoj standardnoj biblioteci i brzom vremenu kompajliranja.

U ovom vodiču ćete instalirati Go na Ubuntu sistem koristeći jednu od tri uobičajena metoda (zvanični tarball, APT ili snap), konfigurisati svoj shell PATH tako da vaš terminal dosledno koristi ispravnu go binarnu verziju i verifikovati instalaciju tako što ćete izgraditi i pokrenuti mali modul baziran na „Hello, World!“ program. Takođe ćete naučiti kako da napravite izvršnu binarnu datoteku, razumete gde se instaliraju kompajlirani alati i rešite uobičajene probleme kao što su konflikti verzija uzrokovani višestrukim instalacijama. Uspešno podešavanje Go-a se uglavnom svodi na odabir pravog metoda instalacije za vaše okruženje i uveravanje da vaš PATH ukazuje na Go lanac alata koji nameravate da koristite.

Postavite svoje Go aplikacije sa GitHub-a koristeći DigitalOcean App Platform. Neka se DigitalOcean fokusira na skaliranje vaše aplikacije.

Ključni za poneti:

- Odaberite odgovarajući metod instalacije za svoje potrebe. Zvanični tarball nudi najviše kontrole verzija i najbolji je za produkciju i CI/CD, dok je apt pogodan za brza podešavanja, a snap je pogodan za radne stanice za programere.

- Uvek proverite preuzetu Go arhivu. Provera SHA-256 kontrolne sume osigurava da je datoteka autentična i da nije oštećena ili neovlašćena pre instalacije.

- Ispravna PATH konfiguracija je neophodna. Nakon instaliranja Go (posebno preko tarball-a), morate dodati /usr/local/go/bin u svoj PATH tako da komanda go radi globalno.

- Izbegavajte višestruke Go instalacije osim ako nisu namerno. Istovremena instalacija Go preko tarball-a, apt-a ili snap-a može izazvati sukobe verzija u zavisnosti od PATH redosleda.

- Koristite Go module za savremeni razvoj. Moduli su podrazumevano omogućeni u modernim Go verzijama i eliminišu potrebu za postavljanjem projekata unutar GOPATH-a.

- Razumeti gde se instaliraju binarne datoteke. Instalirani alati obično idu u $(go env GOPATH)/bin (ili $GOBIN ako je podešeno), a taj direktorij takođe mora biti u vašoj PATH.

- Proverite svoju instalaciju od kraja do kraja. Pokretanje go verzije, provera koji ide i pravljenje jednostavnog „Zdravo, svet!“ program potvrđuje da Go radi ispravno.

- Većina problema nakon instalacije je vezana za PATH. Greške poput „komanda nije pronađena“ ili pogrešne verzije Go obično potiču od nedostajućih PATH unosa ili višestrukih instalacija.

## Preduslovi

Ovaj vodič zahteva Ubuntu sistem konfigurisan sa ne-root korisnikom sa sudo privilegijama i zaštitnim zidom kao što je opisano u Početnom podešavanju servera sa Ubuntuom.

**Napomena**: Ovaj vodič je testiran sa Ubuntu 24.04 i Go verzijom go1.26.0.

## Izbor metoda instalacije

Postoje tri osnovna načina za instaliranje Go na Ubuntu. Tačan izbor zavisi od vašeg okruženja i zahteva.

| Metoda | Preporučeno za | Kontrola verzija | Update Behavior | Pogodnost proizvodnje |
| ------ | --------------- | --------------- | --------------- | ---------------------- |
| Official tarball | Serveri, proizvodnja, precizno verzionisanje | Potpuna kontrola | Manual | Da |
| apt | Brzo podešavanje, lokalni razvoj | Limited | Vezano za Ubuntu repo | Ponekad |
| snap | Radne stanice za programere | Umereno | Automatic | Obično samo za programere |

### Metod 1: Instalirajte Go koristeći zvanični tarball

Instaliranje Go iz zvaničnog tarball-a vam daje najveću kontrolu nad Go verzijom i osigurava da koristite upstream izdanje direktno od Go tima. Ovaj metod je poželjniji ako vam je potrebna najnovija Go verzija čim bude objavljena, ako želite predvidljivo upravljanje verzijama, ako postavljate razvojna okruženja ili CI sisteme, ili ako ne želite da se oslanjate na ciklus ažuriranja Ubuntu paketa. Za razliku od apt metode, ovaj pristup instalira Go u `/usr/local/go` i zahteva od vas da ručno upravljate ažuriranjima, što je često poželjno za razvojne tokove.

#### Korak 1: Preuzmite najnoviju verziju**

Prvo preuzmite zvaničnu Go binarnu arhivu sa Go projekta.

Otvorite zvaničnu Go stranicu za preuzimanje u svom pretraživaču i potražite najnovije izdanje Linuk AMD64 i kopirajte njegovu vezu za preuzimanje.

U vreme pisanja ovog teksta, najnovije izdanje je **go1.26.0**. Najnovija stabilna verzija može da se promeni tokom vremena, pa se pobrinite da zamenite broj verzije u dolenavedenim komandama sa trenutnom verzijom navedenom na go.dev.

Sada preuzmite arhivu koristeći curl:

```sh
cd ~
curl -OL https://go.dev/dl/go1.26.0.linux-amd64.tar.gz
```

Here:

```sh
cd ~    - ensures the file downloads to your home directory
-O      - saves the file with its original filename
-L      - follows redirects (required because go.dev redirects downloads)
```

Always replace the filename with the latest version shown on `go.dev`. Installing outdated Go versions can lead to module incompatibilities and missing security fixes.

You can confirm the file downloaded successfully:

```sh
ls -lh go*.tar.gz
```

#### Korak 2: Potvrdite kontrolnu sumu

Pre instaliranja bilo kog ručno preuzetog softvera, trebalo bi da proverite njegov integritet. Ovo osigurava da:

- Preuzimanje nije oštećeno
- Datoteka nije manipulisana
- Instalirate autentično Go izdanje

Da biste proverili njegov integritet, pokrenite:

```sh
sha256sum go1.26.0.linux-amd64.tar.gz
```

Ova komanda izračunava SHA-256 heš arhive. Vaš izlaz će izgledati slično sledećem (samo primer — vaša stvarna vrednost mora da odgovara kontrolnoj sumi prikazanoj na stranici za preuzimanje):

```sh
<SHA256_CHECKSUM_FROM_GO_DOWNLOADS_PAGE>  go1.26.0.linux-amd64.tar.gz
```

Zatim se vratite na stranicu za preuzimanje Go, pronađite kontrolni zbir naveden za datoteku koju ste preuzeli i pažljivo je uporedite sa hešom prikazanim u izlazu vašeg terminala. Ako se dve vrednosti tačno poklapaju, arhiva je netaknuta i bezbedna za korišćenje. Međutim, ako se razlikuju, trebalo bi da izbrišete preuzetu datoteku, ponovo je preuzmete sa zvaničnog izvora i izbegavate da nastavite sa ekstrakcijom dok se kontrolni sumi ne podudaraju.

Izvođenje ove verifikacije je posebno važno na proizvodnim sistemima, gde instaliranje oštećene ili neovlašćene binarne datoteke može dovesti do problema sa bezbednošću i stabilnošću.

#### Korak 3: Uklonite sve postojeće instalacije

Ako je Go prethodno instaliran pomoću tarball metode, uklonite staru verziju da biste sprečili konflikte.

```sh
sudo rm -rf /usr/local/go
```

Instalacija tarball-a ne nadograđuje automatski postojeće verzije, tako da stare datoteke mogu ostati i uzrokovati nepredvidivo ponašanje; uklanjanje `/usr/local/go` osigurava da nećete završiti sa više Go stabala koja mogu da pokvare gradnje. Ova komanda je bezbedna čak i ako Go trenutno nije instaliran; jednostavno neće učiniti ništa.

**Upozorenje**: Nemojte uklanjati `/usr/bin/go` ako postoji iz apt instalacije; uklonite samo /usr/local/go za tarball instalacije.

Ako niste sigurni gde je Go instaliran, pokrenite:

```sh
which go
```

#### Korak 4: Raspakujte Go arhivu

Sada raspakujte arhivu u /usr/local, što je standardna lokacija za ručno instalirani softver, tako što ćete pokrenuti:

```sh
sudo tar -C /usr/local -xzf go1.26.0.linux-amd64.tar.gz
```

Oznaka `-C /usr/local` govori tar-u da raspakuje u `/usr/local`, `-x` izdvaja datoteke iz arhive, `-z` omogućava gzip dekompresiju, a `-f` specificira ime arhivske datoteke za rad.

Nakon ekstrakcije, Go će biti instaliran na:

```sh
/usr/local/go
```

Ovo možete potvrditi pokretanjem:

```sh
ls /usr/local/go
```

U ovoj fazi, Go je instaliran, ali još nije dostupan u vašem shell PATH, zbog čega je potreban sledeći korak.

#### Korak 5: Dodajte Go na svoje PATH-ove

Da biste koristili komandu go sa bilo kog mesta u terminalu, morate da dodate Go-ov binarni direktorijum u svoj PATH.

**Izaberite ispravnu datoteku ljuske**  
Na Ubuntu-u, datoteka koju treba da uredite zavisi od ljuske koju koristite:

Ako koristite Bash ljusku (koja je podrazumevana na mnogim Ubuntu sistemima), trebalo bi da ažurirate `~/.profile` datoteku jer se učitava za školjke za prijavljivanje i osigurava da se promene PATH zadržavaju u svim sesijama.

```sh
nano ~/.profile
```

Ako koristite Zsh ljusku, trebalo bi umesto toga da uredite ~/.zshrc datoteku, koja je primarna konfiguraciona datoteka koju Zsh čita kada pokreće novu interaktivnu ljusku.

```sh
nano ~/.zshrc
```

Ako niste sigurni koja je školjka trenutno aktivna, možete pokrenuti sledeću komandu u svom terminalu da proverite:

```sh
echo $SHELL 
```

Ova komanda štampa putanju vaše trenutne ljuske, pomažući vam da odlučite koji konfiguracioni fajl da izmenite.

Uređivanje ispravne datoteke osigurava da se Go binarna putanja pravilno učita svaki put kada započnete novu sesiju terminala.

**Dodajte binarnu putanju Goa**
Da bi komanda `go` bila dostupna iz bilo kog direktorijuma, potrebno je da dodate Go-ov binarni direktorijum u svoj PATH. Promenljiva okruženja PATH je lista direktorijuma razdvojenih dve tačke koje vaša školjka pretražuje kada unesete komandu.

Dodajte sledeći red na kraj datoteke koju ste otvorili (~/.profile za Bash školjke za prijavu ili ~/.zshrc za Zsh):

```sh
export PATH=$PATH:/usr/local/go/bin
```

Ova linija čuva vašu postojeću PATH vrednost i zatim dodaje `/usr/local/go/bin` na njen kraj. Direktorijum `/usr/local/go/bin` sadrži izvršnu datoteku `go` (i alatke kao što je `gofmt`), pa je dodavanje u PATH ono što omogućava `da go version`, `go env`, `go run` i druge komande rade bez unošenja pune putanje (na primer, `/usr/local/go/bin/go`).

Ako već imate drugu Go instalaciju na sistemu (na primer, iz apt), imajte na umu da je PATH redosled bitan. Koji god direktorijum se prvi pojavi na PATH je onaj koji će vaša školjka koristiti kada otkucate `go`.

Sačuvajte i izađite iz uređivača.

**Ponovo učitajte svoju shell konfiguraciju**
Nakon što uredite konfiguracionu datoteku ljuske, vaša trenutna terminalska sesija neće automatski videti promenu dok se datoteka ponovo ne učita. Možete da primenite ažuriranu konfiguraciju bez odjavljivanja tako što ćete "source-sovati" datoteku u vašoj trenutnoj ljusci.

Za Bash:

```sh
source ~/.profile
```

Za Zsh:

```sh
source ~/.zshrc
```

Ako želite, takođe možete otvoriti novu terminalsku sesiju umesto izvora datoteke. Cilj je jednostavno osigurati da je ažurirana PATH vrednost aktivna u ljusci u kojoj izvodite komande Goa.

#### Korak 6: Proverite instalaciju

Sada potvrdite da je Go ispravno instaliran i dostupan iz vaše ljuske:

```sh
go version
```

Očekivani izlaz će se razlikovati u zavisnosti od verzije koju ste instalirali, ali bi trebalo da izgleda slično sledećem:

```sh
go version go1.26.0 linux/amd64
```

Ako vidite ovakav izlaz, Go je instaliran i vaš PATH je ispravno konfigurisan.

Ako želite da potvrdite da komanda `go` dolazi iz instalacije tarball-a, možete da proverite i rešenu putanju:

```sh
which go
```

Za tarball instalaciju, izlaz bi obično trebao biti:

```sh
/usr/local/go/bin/go
```

**Opcionalno: Verifikuj Go okruženje**:

Za dodatnu potvrdu, možete pregledati promenljive okruženja Go koje kontrolišu gde je Go instaliran i gde čuva datoteke vašeg radnog prostora:

```sh
go env GOROOT GOPATH
```

Sa tarball instalacijom, `GOROOT` će obično biti `/usr/local/go`. Ako niste eksplicitno podesili `GOPATH`, on je obično podrazumevano podešen na `$HOME/go` (na primer, `/home/sammy/go`). `GOPATH` direktorijum se obično koristi za Go build kešove i za instalirane binarne datoteke alata kada pokrenete `go install`.

### Metod 2: Instalirajte Go koristeći apt

Ovaj metod instalira Go iz Ubuntu-ovih zvaničnih APT repozitorija i njime upravlja kao i sa svakim drugim sistemskim paketom. Ovaj pristup je često zgodan na serverima jer se jasno integriše sa nadogradnjom OS-a, bezbednosnim ažuriranjima i alatima za upravljanje konfiguracijom.

Trebalo bi da imate jedno ograničenje na umu pre nego što odaberete ovaj metod: Go verzija koju obezbeđuje Ubuntu je izabrana radi stabilnosti i možda nije najnovije izdanje koje je dostupno na `go.dev`. Ako vam je potrebna određena Go verzija za projekat, ili ako su vam odmah potrebne najnovije funkcije jezika i alata, zvanični tarball metod obično bolje odgovara.

Ako želite da vidite koju Go verziju će Ubuntu instalirati pre nego što unesete bilo kakve promene, prvo možete da proverite verziju kandidata:

```sh
apt policy golang-go
```

Ako je apt policy neraspoloživo, možete koristiti ekvivalentnu komandu:

```sh
apt-cache policy golang-go
```

#### Korak 1: Ažuriranje liste paketa

Prvo, ažurirajte listu paketa:

```sh
sudo apt update
```

Ova komanda ažurira vaš lokalni indeks paketa tako da vaš sistem zna o najnovijim verzijama dostupnim u spremištima. Prvo pokretanje `apt update`-a pomaže da se osigura da `apt install` ispravno rešava zavisnosti i da instalira najnoviju verziju spremišta Go-a za vaše izdanje Ubuntu-a.

#### Korak 2: Instalirajte Go

Sada možete direktno instalirati Go tako što ćete pokrenuti:

```sh
sudo apt install golang-go
```

Ova komanda instalira `golang-go` paket i sve potrebne zavisnosti. Sa APT instalacijom, komanda `go` je obično dostupna u `/usr/bin/go`, a datoteke Go alata se čuvaju u direktorijumima kojima upravlja sistem (obično pod `/usr/lib/...`).

#### Korak 3: Potvrdite instalaciju

Da biste potvrdili instalaciju, pokrenite:

```sh
go version
```

Možete potvrditi i sa:

```sh
which go
```

Ako želite jaču proveru (posebno ako sumnjate na višestruke Go instalacije), navedite sve `go` binarne datoteke koje su vidljive vašoj ljusci:

```sh
type -a go
```

Na kraju, možete proveriti gde je Go-ov root direktorijum sa:

```sh
go env GOROOT
```

Sa APT instalacijom, GOROOT će obično pokazivati negde ispod `/usr/lib/...` umesto `/usr/local/go`.

Uobičajeno je da Ubuntu-ova spremišta pružaju stariju verziju Go-a nego tarbaal. Za razvojna okruženja u kojima je paritet verzija važan (na primer, kada treba da se poklapate sa Go verzijom koja se koristi u CI/CD ili produkciji), trebalo bi da preferirate zvaničnu tarball instalaciju.

Ako odlučite da ostanete sa APT-om, možete da nadogradite Go kasnije koristeći normalan tok nadogradnje:

```sh
sudo apt update
sudo apt upgrade golang-go
```

Ako želite da uklonite APT-managed verziju Goa, možete ga deinstalirati sa:

```sh
sudo apt remove golang-go
```

Ako želite da uklonite konfiguracione fajlove i očistite nekorišćene zavisnosti, možete koristiti `purge` i `autoremove`:

```sh
sudo apt purge golang-go
sudo apt autoremove
```

Kao i kod tarball metode, trebalo bi da izbegavate mešanje metoda instalacije osim ako namerno ne upravljate sa više verzija. Ako instalirate Go preko APT-a i kasnije instalirate tarball u /usr/local/go, PATH redosled će odrediti koja se go komanda pokreće, što može dovesti do zbunjujućih i nedoslednih rezultata.

### Metod 3: Instalirajte Go koristeći snap

Snap paketi pružaju automatska ažuriranja i iskustvo upravljane instalacije preko Canonical-ovog snap sistema. Ovaj pristup može biti zgodan na radnim stanicama za programere jer smanjuje ručno održavanje, ali generalno pruža manje direktne kontrole nad tačno koju verziju Go-a koristite u bilo kom trenutku.

Takođe treba da budete svesni da binarni fajlovi instalirani snap-om obično žive pod direktorijumom `/snap` i da su izloženi preko `/snap/bin`, što može uticati na to kako ćete dijagnostikovati sukobe PATH i verzija ako takođe instalirate Go koristeći apt ili tarball.

#### Korak 1: Instalirajte Go preko Snap-a

Koristite sledeću komandu da biste instalirali Go:

```sh
sudo snap install go --classic
```

Oznaka `--classic` je ovde važna jer instalira Go snap sa klasičnim ograničenjem. U praksi, ovo lancu alata Go daje širi pristup vašem sistemu datoteka i direktorijumima za programere od strogo ograničenog snap-a, koji ima tendenciju da smanji trenje za tipične Go razvojne zadatke.

Ako komanda `snap` nije dostupna na vašem sistemu, možete instalirati podršku za `snap` pomoću:

```sh
sudo apt update
sudo apt install snapd
```

#### Korak 2: Potvrdite instalaciju

Proverite instalaciju koristeći:

```sh
go version
```

Nakon potvrde Go verzije, takođe je korisno potvrditi odakle dolazi `go` binarni fajl:

```sh
which go
```

Na mnogim sistemima, go instaliran sa `snap` će se biti u `/snap/bin/go`. Ako vidite drugu putanju, možda koristite apt instalaciju (`/usr/bin/go`) ili tarball instalaciju (`/usr/local/go/bin/go`).

Za dodatnu verifikaciju, takođe možete da pregledate Go-ov osnovni direktorijum:

```sh
go env GOROOT
```

Sa `snap`-om, GOROOT obično pokazuje negde ispod `/snap/...`, što se i očekuje.

Snap instalira Go u upravljanom okruženju koje ažurira sistem `snap`. Može dobro da funkcioniše za razvojne sisteme, ali nudi manje direktne kontrole nad verzijama u poređenju sa tarball metodom, zbog čega mnogi timovi i dalje preferiraju tarball pristup za CI/CD i proizvodna okruženja.

Ako želite da vidite detalje o instalaciji specifične za snap, možete pokrenuti:

```sh
snap list go
snap info go
```

Da biste ažurirali na najnoviju `snap` dostupnu reviziju:

```sh
sudo snap refresh go
```

Da biste uklonili Go koji je instaliran sa `snap`, koristite:

```sh
sudo snap remove go
```

## Testiranje vaše instalacije

Sada kada je Go instaliran i putanje postavljene za vaš server, možete pokušati da kreirate svoju "Hello, World!" aplikaciju kako biste bili sigurni da Go radi.

Prvo napravite novi direktorijum za svoj projekat. Ovde ćete kreirati datoteke modula i izgraditi svoj program:

```sh
mkdir hello
```

Zatim pređite u direktorijum koji ste upravo kreirali:

```sh
cd hello
```

Kada napravite i pokrenete Go kod, zavisnostima se upravlja preko modula projekta. Možete da podesite modul tako što ćete kreirati `go.mod` datoteku pomoću komande `go mod init`:

```sh
go mod init your_domain/hello
```

Zatim kreirajte `hello.go` datoteku u željenom uređivaču teksta:

```sh
nano hello.go
```

Dodajte sledeći tekst u `hello.go` datoteku:

"hello.go"

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

Zatim sačuvajte i zatvorite datoteku hello.go.

Testirajte svoj kod da biste proverili da li štampa "Hello, World!" pozdrav:

```sh
go run .
```

Izlaz

```sh
Hello, World!
```

Komanda `go run` kompajlira i pokreće paket Go sa liste `.go` izvornih datoteka iz novog `hello` direktorijuma koji ste kreirali i putanje koju ste uvezli. Ali, takođe možete koristiti `go build` da napravite izvršnu datoteku koja vam može uštedeti vreme.

## Pretvaranje vašeg Go koda u binarni izvršni fajl

Komanda `go run` se obično koristi kao prečica za kompajliranje i pokretanje programa dok još uvek pravite česte promene. Kada budete spremni da pokrenete program bez kompajliranja svaki put, možete koristiti `go build` da kreirate izvršni binarni fajl. Ovo proizvodi jednu izvršnu datoteku koju možete direktno pokrenuti. Nakon toga, možete koristiti `go install` da postavite svoj program na putanju `GOPATH/bin` izvršnih datoteka tako da možete da ga pokrenete sa bilo kog mesta na vašem sistemu.

Isprobajte i pokrenite `go build`. Uverite se da ste ovo pokrenuli iz istog direktorijuma u ​​kome je uskladištena datoteka `hello.go`:

```sh
go build
```

Sledeće pokrenite sa `./hello` da potvrdite da radi:

```sh
./hello
```

Izlaz

```sh
Hello, World!
```

Ovo potvrđuje da ste uspešno kompajlirali svoj `hello.go` kod u izvršnu binarnu datoteku. Međutim, ovu binarnu datoteku možete pozvati samo iz ovog direktorijuma. Ako želite da pokrenete ovaj program sa druge lokacije na vašem serveru, trebalo bi da navedete punu putanju datoteke binarne datoteke da biste je izvršili.

Unošenje pune putanje binarne datoteke može brzo postati dosadno. Kao alternativu, možete pokrenuti komandu `go install`. Ovo je slično `go build`-u, ali umesto da ostavite izvršni fajl u trenutnom direktorijumu, `go install` ga postavlja u direktorijum `$GOPATH/bin`, što će vam omogućiti da ga pokrenete sa bilo koje lokacije na vašem serveru.

U ovom primeru možete pokrenuti go install iz direktorijuma projekta da biste napravili i instalirali program. Go podrazumevano instalira binarne datoteke u `$(go env GOPATH)/bin` (ili u `$GOBIN` ako ste ga podesili). Ako želite da potvrdite gde će hello binarni fajl biti instaliran, možete da proverite njegovu ciljnu putanju pomoću liste za pokretanje:

```sh
go list -f '{{.Target}}'
```

`go list` generiše listu svih imenovanih Go paketa uskladištenih u trenutnom radnom direktorijumu. Oznaka `-f` će dovesti do toga da go lista vrati izlaz u drugom formatu na osnovu bilo kog šablona paketa koji joj prosledite. Ova komanda joj govori da koristi `Target` šablon, što će dovesti do toga da go lista vrati putanju za instalaciju svih paketa uskladištenih u ovom direktorijumu:

Izlaz

```sh
/home/
sammy/go/bin/hello
```

Ovo je putanja za instalaciju binarne datoteke koju ste kreirali pomoću go build-a. To znači da je direktorijum gde je instaliran ovaj binarni fajl /home/sammy/go/bin/.

Dodajte ovaj instalacioni direktorijum na putanju ljuske vašeg sistema. Obavezno promenite istaknuti deo ove komande tako da odražava direktorijum za instalaciju binarne datoteke na vašem sistemu, ako je drugačiji:

```sh
export PATH=$PATH:$(go env GOPATH)/bin
```

Konačno, pokrenite `go install` da kompajlirate i instalirate paket:

```sh
go install .
```

Pokušajte pokretanje sa `hello`:

```sh
hello
```

Izlaz

```sh
Hello, World!
```

## Razumevanje /usr/local/go/bin i PATH

Kada instalirate Go koristeći zvanični tarball, Go alatni lanac se instalira pod `/usr/local/go`, a izvršni programi koje pokrećete sa terminala nalaze se u direktorijumu `bin`.

Za tarball instalaciju, direktorijum koji sadrži izvršni fajl `go` je:

```sh
/usr/local/go/bin
```

Promenljiva okruženja PATH govori vašoj ljusci gde da traži izvršne datoteke kada otkucate komandu kao što je `go`, `gofmt` ili `goimports`. Kada pokrenete `go version`, vaša ljuska pretražuje svaki direktorijum u PATH s leva na desno i izvršava prvu odgovarajuću `go` binarnu datoteku koju pronađe.

Možete da proverite svoj PATH koristeći:

```sh
echo $PATH
```

Ako `/usr/local/go/bin` nije uključen, vaš sistem ne može pronaći komandu `go`.

Ako vidite grešku kao što je komanda nije pronađena: go nakon instaliranja tarball-a, to skoro uvek znači da `/usr/local/go/bin` nedostaje u PATH-u ili da se prvo bira druga Go instalacija.

Da biste potvrdili koji `go` binarni sistem trenutno koristi, pokrenite:

```sh
which go
```

Ako sumnjate na višestruke instalacije, navedite svaku go binarnu datoteku vidljivu vašoj ljusci:

```sh
type -a go
```

Za tarball metod, obično želite da go ide u `/usr/local/go/bin/go`.

Dodavanje `/usr/local/go/bin` u PATH osigurava da su alati `go`, `gofmt` i `go` globalno dostupni iz bilo kog direktorijuma.

Mnogi programeri takođe dodaju `$HOME/go/bin` u PATH. Ovo je podrazumevana lokacija gde Go postavlja binarne datoteke za alate komandne linije koje instalirate sa `go install` i sprečava drugi, uobičajeni problem „komanda nije pronađena“ kasnije kada počnete da instalirate alatke za programere zasnovane na Go-u.

## Bash vs Zsh konfiguracija

Ubuntu koristi Bash kao podrazumevanu školjku na većini instalacija, ali mnogi programeri prelaze na Zsh. U kontekstu instaliranja Go-a, ovo je važno jer dodavanje `/usr/local/go/bin` (i često `$HOME/go/bin`) u vašu PATH funkcioniše samo ako dodate liniju za export u datoteku za pokretanje ljuske koju vaš terminal zapravo čita.

Da biste proverili koju ljusku vaš nalog podrazumevano koristi, pokrenite:

```sh
echo $SHELL
```

Ova komanda štampa putanju do vaše ljuske za prijavu (na primer, `/bin/bash` ili `/usr/bin/zsh`).

Međutim, imajte na umu da mnoge terminalske aplikacije pokreću interaktivnu ljusku koja nije ljuska za prijavu, dok SSH sesije obično pokreću ljusku za prijavu. Zato je korisno razumeti koje datoteke se čitaju u svakom slučaju.

| Shell | Config File |
| ----- | ----------- |
| Bash | \~/.profile or \~/.bashrc |
| Zsh | \~/.zshrc |

U praksi, ovo su najčešća i najpouzdanija mesta za postavljanje Go PATH izvoza:

- Ako koristite Bash i želite da putanja Go radi za SSH prijave i druge ljuske za prijavljivanje, dodajte liniju za export u `~/.profile`. Na Ubuntu-u, podrazumevani `~/.profile` često automatski pokreće `~/.bashrc` za interaktivne sesije, ali ne biste se trebali oslanjati na to ponašanje ako je datoteka prilagođena.

- Ako koristite Bash i prvenstveno vam je stalo do interaktivnih prozora terminala na radnoj površini, dodajte liniju za export u `~/.bashrc`.

- Ako koristite Zsh, dodajte liniju za export u `~/.zshrc` za interaktivne terminale. Ako vam je potreban i za Zsh školjke za prijavu, `~/.zprofile` je ekvivalent ljuske za prijavu.

Ako niste sigurni koju datoteku vaš terminal čita, možete dodati liniju za export u odgovarajuću datoteku za prijavu i interaktivnu datoteku za vašu školjku, a zatim ukloniti duplikate kasnije. Kada to uradite, obratite pažnju na PATH redosled, jer će se prvi put naći na vašem PATH-u. Ovo je posebno važno ako ste instalirali Go koristeći više metoda (na primer, i apt i tarball, ili snap i tarball).

## GOPATH protiv Go modula

Ovo je jedan od najpogrešnijih delova Go-a jer stariji tutorijali i stariji alati pretpostavljaju tok rada koji je usredsređen na GOPATH. Modern Go podrazumevano koristi module, ali GOPATH i dalje postoji i još uvek utiče na to gde se postavljaju određeni keš i instalirani binarni fajlovi.

### Šta je GOPATH?

GOPATH je bio originalni mehanizam radnog prostora koji se koristio pre Go 1.11. U eri pre-modula, Go je očekivao da vaš izvorni kod i zavisnosti žive unutar određene strukture direktorijuma, a mnoge komande su pretpostavljale taj raspored.

Podrazumevana lokacija GOPATH je:

```sh
~/go
```

Ovaj direktorijum obično sadrži sledeće poddirektorijume:

```sh
~/go/bin
~/go/pkg
~/go/src
```

Istorijski gledano, svi Go projekti su morali da postoje unutar `GOPATH/src`.

Danas generalno ne morate da postavljate svoje projekte unutar `GOPATH/src`, ali GOPATH direktorijum se i dalje koristi za nekoliko stvari, uključujući podrazumevanu lokaciju za instalirane binarne datoteke `$GOPATH/bin` i razne keš memorije.

### Šta su Go moduli?

Go moduli su standardni sistem zavisnosti i verzija za Go projekte. Modul je definisan `go.mod` datotekom, koja deklariše putanju modula i beleži verzije potrebnih zavisnosti.

Od Go 1.16:

- Moduli su podrazumevano omogućeni, tako da obično ne morate da postavljate GO111MODULE.
- Projekti mogu da žive bilo gde u vašem sistemu datoteka, ne samo unutar GOPATH-a.
- Zavisnosti su definisane u `go.mod` i verifikovane kontrolnim sumama u `go.sum`.

Primer:

```sh
mkdir hello
cd hello
go mod init example.com/hello
```

Ovo kreira:

```sh
go.mod
```

Moduli eliminišu potrebu za ručnim upravljanjem GOPATH-om za većinu tokova posla.

U projektu zasnovanom na modulu, uobičajene komande uključuju `go mod tidy` (za dodavanje i uklanjanje zavisnosti po potrebi), `go test ./...` (za pokretanje testova u modulu) i `go build` (za prevođenje).

### Kada se GOPATH još uvek koristi

GOPATH je i dalje relevantan na nekoliko mesta, čak i kada koristite module:

- Prevedene binarne datoteke instalirane preko `go install` (posebno `go install ekample.com/tool@version`) se smeštaju u `$GOBIN` ako je podešen, a inače u `$GOPATH/bin`.

  ```sh
  $GOPATH/bin
  ```

- Da biste bili sigurni da su instalirani alati izvršni sa bilo kog mesta, dodajte:

  ```sh
  export PATH=$PATH:$(go env GOPATH)/bin
  ```

Ako ste već ranije dodali `$HOME/go/bin` u svoj PATH, ova komanda postiže isti cilj na dinamičniji način jer koristi trenutnu vrednost `go env GOPATH`.

#### Upravljanje verzijama, nadogradnja i deinstaliranje

U ovom odeljku ćete videti kako da proverite Go verziju koja je trenutno aktivna u vašoj ljusci, kako da bezbedno nadogradite Go i kako da uklonite Go ako vam više nije potreban. Ovi zadaci su jednostavni kada znate koju go binarnu datoteku koristi vaš terminal, ali mogu postati zbunjujući ako ste instalirali Go više puta (na primer, koristeći i APT i zvanični tarball) i vaš PATH bira drugačiju instalaciju nego što ste očekivali. Ako prvo proverite verziju i binarnu lokaciju, možete samouvereno da unosite promene i izbegnete kršenje postojećih projekata.

#### Proverite trenutnu verziju

Koristite sledeću komandu da proverite trenutnu verziju Go:

```sh
go version
```

Ako želite da potvrdite koji metod instalacije trenutno koristite, takođe je korisno da proverite razrešenu binarnu putanju i Go-ov osnovni direktorijum:

```sh
which go
go env GOROOT
```

#### Nadogrdnja Goa (tarball metod)

Za tarball instalaciju, nadogradnja Goa je ručni proces jer sami upravljate datotekama u `/usr/local/go`.

- Preuzmite novi tarball sa go.dev.
- Uklonite stari instalacioni direktorijum:

  ```sh
  sudo rm -rf /usr/local/go
  ```

- Raspakujte novu verziju.
- Potvrdite nadogradnju pomoću `go version`.

Ako se čini da se verzija ne menja u vašoj trenutnoj terminalnoj sesiji nakon nadogradnje, možete da obrišete keš komande ljuske i pokušate ponovo:

```sh
hash -r
go version
```

### Deinstaliranje Goa

Tačni koraci za deinstalaciju zavise od toga kako ste instalirali Go, jer svaki metod postavlja lanac alata na drugu lokaciju. Pre nego što bilo šta uklonite, dobra je ideja da potvrdite koji go binarni fajl trenutno koristite kako ne biste izbrisali pogrešan direktorijum.

```sh
which go
go env GOROOT
```

Kada saznate gde je Go instaliran, sledite metod podudaranja u nastavku.

#### Tarball metod

Ako ste instalirali Go iz zvaničnog tarball-a, Go direktorijum je obično `/usr/local/go`. Deinstaliranje tarball metode znači brisanje tog direktorijuma i zatim uklanjanje svih PATH unosa koje ste dodali za `/usr/local/go/bin`.

```sh
sudo rm -rf /usr/local/go
```

Zatim uklonite liniju za export Goa iz vaše datoteke za pokretanje ljuske (na primer, `~/.profile`, `~/.bashrc` ili `~/.zshrc`) tako da vaša ljuska ne nastavi da upućuje na direktorijum koji više ne postoji. Nakon što uredite datoteku, ponovo je učitajte ili započnite novu sesiju terminala.

Na kraju, proverite da li se vaša školjka više ne razrešava go na instalaciju tarball-a:

```sh
hash -r 2>/dev/null || true
which go
```

Ako go i dalje vraća putanju, verovatno još uvek imate Go instaliran preko APT-a ili snap-a, ili imate drugu Go binarnu datoteku ranije u svom PATH-u.

#### apt metod

Ako ste instalirali Go koristeći APT, uklonite paket koristeći `apt remove`. Ovo održava vaš sistem u skladu sa načinom na koji se upravlja drugim paketima, a takođe olakšava kasniju ponovnu instalaciju ako se predomislite.

```sh
sudo apt remove golang-go
```

Ako takođe želite da uklonite konfiguracione datoteke paketa (kada je primenljivo) i očistite neiskorišćene zavisnosti, možete da koristite čišćenje i automatsko uklanjanje:

```sh
sudo apt purge golang-go
sudo apt autoremove
```

Nakon deinstaliranja, možete potvrditi da APT više ne pruža Go sa sledećim komandama:

```sh
which go
go version
```

Ako `go version` i dalje radi, to obično znači da je još uvek prisutna druga Go instalacija (na primer, tarball instalacija u `/usr/local/go` ili `snap` instalacija pod `/snap`).

#### snap metod

Ako ste instalirali Go koristeći snap, uklonite ga pomoću `s`nap remove`. Ovo briše instalaciju kojom se upravlja snap i sprečava buduća automatska ažuriranja.

```sh
sudo snap remove go
```

Nakon uklanjanja, proverite da li se komanda go više ne razrešava u `/snap/bin/go`:

```sh
which go
type -a go
```

Ako ste ručno dodali liniju za export PATH koja se odnosi na Go, trebalo bi da uklonite i tu liniju tako da konfiguracija ljuske odgovara trenutnom stanju vašeg sistema.

### Uobičajeni problemi i rešavanje problema

Hajde da pokrijemo neke od najčešćih problema na koje ljudi nailaze nakon instaliranja Go na Ubuntu i objasnimo kako da ih dijagnostikujemo korišćenjem jednostavnih, ponovljivih provera. U većini slučajeva, osnovni uzrok je ili nedostatak PATH unosa ili višestruke Go instalacije na istom sistemu, tako da se koraci za rešavanje problema fokusiraju na potvrđivanje koje go binarne datoteke zapravo koristi vaša školjka.

**komanda nije pronađena: idi**  
Ova greška znači da vaša ljuska ne može da pronađe izvršni go u bilo kom od direktorijuma navedenih u vašoj PATH, ili znači da ste instalirali Go, ali još niste otvorili novu terminalsku sesiju ili ponovo učitali konfiguraciju ljuske.

Počnite tako što ćete potvrditi da li se go uopšte može rešiti:

```sh
command -v go || which go
```

Ako te komande ne štampaju ništa, proverite svoj PATH i potvrdite da li je Go binarni direktorijum prisutan:

```sh
echo "$PATH" | tr ':' '\n'
```

Ako ste instalirali Go koristeći zvanični tarball, direktorijum koji vam je obično potreban je `/usr/local/go/bin`. Možete potvrditi da binarni fajl postoji na disku pomoću sledeće komande:

```sh
ls -l /usr/local/go/bin/go
```

Ako datoteka postoji, ali go još uvek nije pronađen, dodajte `/usr/local/go/bin` u vašu datoteku za pokretanje ljuske (kao što je ~/.profile, ~/.bashrc ili ~/.zshrc) i zatim je ponovo učitajte. Ako datoteka ne postoji, ponovo proverite korake instalacije i uverite se da je tarball raspakovan u `/usr/local`.

**Napomena**: Ako želite da brzo vidite svoj PATH jedan unos po redu, ovo je zgodna dijagnostička komanda:

```sh
echo "$PATH" | tr ':' '\n'
```

**Pojavljuje se pogrešna verzija**
Ovaj problem se skoro uvek dešava kada postoji više od jedne Go instalacije na istoj mašini, a vaša školjka bira drugu verziju od one koju ste nameravali da koristite. Na primer, APT obično instalira Go na `/usr/bin/go`, snap ga često izlaže na `/snap/bin/go`, a tarball metod ga instalira na `/usr/local/go/bin/go`.

Prvo proverite verziju i rešenu putanju:

```sh
go version
which go
```

Ako želite jaču proveru, izlistajte svaki go koji je vidljiv vašoj ljusci:

```sh
type -a go
```

Takođe možete potvrditi koje stablo instalacije Go misli da koristi tako što ćete proveriti GOROOT:

```sh
go env GOROOT
```

Kada identifikujete neželjenu instalaciju, možete je ili deinstalirati ili prilagoditi svoju PATH tako da se direktorijum za nameravanu instalaciju pojavi ranije. Nakon što promenite PATH, obrišite keš komande za traženje komandi (ako vaša školjka kešira putanje komandi) i ponovo proverite:

```sh
hash -r 2>/dev/null || true
which go
go version
```

**Go instalacija ne čini binarne datoteke dostupnim**
Ako `go install` uspe, ali alatka koju ste instalirali ne može da se pronađe (na primer, pokrenete komandu i dobijete "komanda nije pronađena"), najčešće objašnjenje je da direktorijum u koji Go postavlja instalirane binarne datoteke nije na vašem PATH.

Počnite tako što ćete proveriti gde će Go instalirati binarne datoteke na vašem sistemu:

```sh
go env GOBIN GOPATH
```

Ako je GOBIN podešen, Go instalira binarne datoteke tamo. Ako je GOBIN prazan, Go obično instalira binarne datoteke u `$(go env GOPATH)/bin`, što se obično rešava u `$HOME/go/bin`.

Zatim potvrdite da li je taj direktorijum već u vašem PATH:

```sh
echo "$PATH" | tr ':' '\n' | grep -E "^$(go env GOPATH)/bin$" || true
```

Ako nedostaje, dodajte sledeći red u datoteku za pokretanje ljuske i ponovo učitajte svoju ljusku:

```sh
export PATH=$PATH:$(go env GOPATH)/bin
```

Nakon ponovnog učitavanja, pokrenite alat koji ste instalirali, da biste potvrdili da je binarni fajl sada vidljiv.

**Greške modula**
Ako vidite sledeću grešku:

```sh
go: go.mod fajl nije pronađen
```

Inicijalizujte modul:

```sh
go mod init example.com/project
```

Ova greška se obično javlja kada pokrenete go komande u direktorijumu koji nije deo modula i ne sadrži `go.mod` datoteku. U većini slučajeva, popravka je ili da se promenite u ispravan direktorijum projekta ili da inicijalizujete novi modul u direktorijumu koji nameravate da koristite kao koren projekta.

Ako niste sigurni da li Go misli da ste u modulu, možete proveriti `GOMOD` vrednost. Kada ste unutar modula, `GOMOD` pokazuje na `go.mod` datoteku, a kada ste izvan modula, često će biti prazan ili postavljen na `/dev/null:

```sh
go env GOMOD
```

Ako ste nameravali da radite unutar postojećeg projekta, idite do direktorijuma koji sadrži `go.mod` i ponovo pokrenite komandu. Ako ste nameravali da pokrenete novi projekat, pokrenite `go mod init` jednom, a zatim koristite `go mod tidy` za rešavanje i snimanje zavisnosti.

## Česta pitanja

### Kako da instaliram najnoviju verziju Go na Ubuntu

Najpouzdaniji način da instalirate najnovije Go izdanje na Ubuntu je da koristite zvanični tarball sa Go veb lokacije. Ubuntu-ova spremišta često zaostaju za najnovijom verzijom.

Uklonite bilo koju postojeću Go instalaciju (opcionalno, ali preporučeno):

```sh
sudo rm -rf /usr/local/go
```

Preuzmite najnoviji Go tarball. Posetite `go.dev/dl/` i kopirajte vezu za najnoviju verziju Linux AMD64, a zatim je preuzmite:

```sh
curl -OL https://go.dev/dl/go1.26.0.linux-amd64.tar.gz
```

Rapakujte u `/usr/local`:

```sh
sudo tar -C /usr/local -xzf go1.26.0.linux-amd64.tar.gz
```

Dodajte Go u PATH:

```sh
grep -qxF 'export PATH=$PATH:/usr/local/go/bin' ~/.profile || 
    echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile
source ~/.profile
```

Verify the installation:

```sh
go version
```

Ovaj metod obezbeđuje da uvek dobijate najnovije zvanično izdanje direktno od Go tima.

### Da li je Go dostupan u Ubuntu spremištu

Da. Go je dostupan preko Ubuntuovih APT repozitorija.

Možete ga instalirati sa:

```sh
sudo apt update
sudo apt install golang-go
```

Verzija u Ubuntu repozitorijumima je obično starija od najnovijeg izdanja, što znači da možda nećete odmah dobiti najnovije jezičke funkcije i poboljšanja lanca alata. Kao rezultat toga, APT metod je najpogodniji za brza podešavanja, stabilna okruženja u kojima nisu potrebne najnovije Go funkcije i CI ili reproducibilne verzije koje su namerno vezane za Ubuntu upakovane verzije.

Da biste proverili dostupnu verziju:

```sh
apt show golang-go
```

### Koji je preporučeni način za instaliranje Go na Linux-u

Preporučeni način za instaliranje Go na Linux-u je korišćenje zvaničnog tarball-a koji se nalazi na `go.dev`. Ovaj metod vam daje najnoviju stabilnu verziju direktno od Go tima i obezbeđuje dosledno ponašanje u svim distribucijama Linuxa. Iako je APT paketom lakše upravljati, većina programera preferira zvanični tarball jer ostaje ažuriran i usklađen sa Go-ovom zvaničnom dokumentacijom.

### Kako da nadogradim Go na noviju verziju na Ubuntu

Proces nadogradnje zavisi od toga kako ste instalirali Go, jer svaki metod instalacije drugačije upravlja verzijama.

- Ako ste instalirali Go koristeći zvanični tarball, možete ga nadograditi prateći ove korake:

  - Trebalo bi da proverite koju go binarnu datoteku trenutno koristite kako ne biste slučajno nadogradili pogrešnu instalaciju.
  
    ```sh
    which go
    go version
    ```
  
  - Trebalo bi da uklonite postojeći tarball instalacioni direktorijum na `/usr/local/go` kako bi novo izdanje bilo čisto instalirano.
  
    ```sh
    sudo rm -rf /usr/local/go
    ```
  
  - Trebalo bi da preuzmete noviji tarball sa `go.dev` i ponovo ga raspakujete u `/usr/local`.
  
  - Trebalo bi da proverite nadogradnju i po potrebi obrišite keš komandi ljuske.
  
    ```sh
    hash -r 2>/dev/null || istina
    go verzija
    ```

- Ako ste instalirali Go koristeći APT, možete ga nadograditi pomoću standardnog toka nadogradnje paketa, ali treba da imate na umu da će se ovo nadograditi samo na verziju dostupnu u Ubuntu skladištima:

  ```sh
  sudo apt update
  sudo apt upgrade golang-go
  ```

- Ako ste instalirali Go koristeći snap, možete ga nadograditi tako što ćete osvežiti snap na najnoviju reviziju dostupnu za vaš sistem:

  ```sh
  sudo snap refresh go
  ```

### Kako mogu da proverim da li je Go ispravno instaliran

Možete da proverite da li je Go ispravno instaliran tako što ćete proveriti i verziju i lokaciju go binarne datoteke i tako što ćete potvrditi da ključne vrednosti Go okruženja izgledaju razumno.

Možete koristiti sledeću kontrolnu listu:

- Trebalo bi da potvrdite da Go pokreće i štampa verziju.

  ```sh
  go version
  ```

- Trebalo bi da potvrdite odakle vaša školjka rešava komandu go, posebno ako ste koristili više od jednog metoda instalacije.

  ```sh
  which go
  type -a go
  ```

- Trebalo bi da potvrdite koren instalacije Go i podrazumevanu lokaciju radnog prostora.

  ```sh
  go env GOROOT GOPATH
  ```

Ako `go version` ili `which go` ne uspe sa greškom "komanda nije pronađena", trebalo bi da ponovo posetite svoju PATH konfiguraciju i uverite se da je ispravan Go binarni direktorijum (kao što je `/usr/local/go/bin` za tarball metod) učitan od strane ljuske.

## Zaključak

Sada imate tri praktična načina da instalirate Go na Ubuntu, a najbolji izbor zavisi od toga koliko vam treba kontrole nad verzijom. Zvanični tarball metod obično najbolje odgovara kada želite najnovije stabilno izdanje i predvidljivo upravljanje verzijama (zbog čega je uobičajeno u CI/CD-u i produkciji). APT metoda je dobra opcija kada više volite pakete kojima upravlja OS i nadogradnje vezane za Ubuntu-ova spremišta. Metoda snap-a je često zgodna na radnim stanicama za programere jer se njime upravlja i može se automatski ažurirati.

Nakon instalacije, najvažniji sledeći korak je potvrda da vaš PATH bira go binarni program koji očekujete, a zatim pokreće jednostavan modul baziran na "Hello, Vorld!" da verifikujete vaše podešavanje od kraja do kraja. Odatle možete da koristite Go module (go.mod i go.sum) za upravljanje zavisnošću i da se vratite na odeljke za upravljanje verzijama i rešavanje problema ako treba da nadogradite, deinstalirate ili rešite PATH konflikte.

Za zvaničnu dokumentaciju i beleške o izdanju pogledajte <https://go.dev/doc/>.

[Nazad](./README.md)
