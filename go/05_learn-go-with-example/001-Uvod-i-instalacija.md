# Go tutorijal 001

[Sadržaj](000-Sadržaj.md)

## 1 Uvod i instalacija

Ovo je prvi tutorial u našoj seriji golang tutorijala. Ovaj tutorial pruža Uvod za Go i takođe raspravlja o prednostima izbora prelazeći druge programiramske jezike.Takođe ćemo naučiti kako instalirati Go na Mac OS, Windows i Linux.

### 1.1 Uvod

Poznat i kao Golang to je jezik otvorenog izvora, kompajliran i jako statički tipiziran programski jezik koji je razvio Google. Ključni ljudi koji stoje iza stvaranja
Goa su Rob Pike, Ken Thompson i Robert Griesemer. Go je postao open source u novembru 2009. godine.

Go je jezik za programiranje opšte namene sa jednostavnom sintaksom i podržan robusnom standardnom bibliotekom. Jedna od ključnih područja u kojoj Go sjaji je kreairanje visoko dostupnih i skalabilnih web aplikacija. Go se takođe može koristiti za kreiranje aplikacija komandne linije, desktop aplikacija i čak i mobilnih aplikacija.

#### 1.1.1 Prednosti Goa

Zašto biste izabrali Go kao jezik na strani servera kada postoje tone drugih jezika kao što su Python, Ruby, Nodejs ... koji rade isti posao.

Evo nekih od prednosti:

- Jednostavna sintaksa  
  Sintaksa je jednostavna i sažeta i jezik se ne omamljuje sa nepotrebnim karakteristikama. To olakšava pisanje koda koji je čitljiv i održiv.

- Lako pisanje konkurentnih programa
  Konkurencija je svojstveni deo jezika. Kao rezultat, pisanje multithreaded programa je samo komad torte. To se postiže gorutinama i kanalima o kojima ćemo razgovarati u predstojećim tutorijalima.

- Kompajlirani jezik  
  Go je kompajlirani jezik. Izvorni kod je kompajliran na izvršni binarni. Ovo je karika koja nedostaje u interpretiranim jezicima kao što je JavaScript.

- Brza kompilacija  
  Kompajler Goa je dizajniran je da bude brz od samog početaka.

- Statičko linkovanje  
  Go kompajler podržava statičko povezivanje. Ceo Go projekat može biti statički povezan u jednu veliku binarnu datoteku i lako se može rasporediti na serverima oblaka bez brige o zavisnosti.

- Go alati  
  Alati zaslužuje posebno spominje u Go. Go dolazi u paketu sa moćnim alatima koji pomažu programerima da napišu bolji kod.  
  Evo najčešće korišćenih alata:
  - gofmt - gofmt se koristi za automatsko formatiranje izvornog koda. Koristi tabove za uvlačenje i praznine za poravnanje.
  - vet - vet analizira kod i izveštava o mogućem sumnjivomm kodu. Sve što vet prijavi možda nije istinski problem, ali ima mogućnost da uhvati greške koje kompajler uvek ne prijavljuje.
  - staticcheck - staticcheck se koristi za sprovođenje stalnih provera u kodu.

- Prikupljanje smeća  
  Go koristi skeniranje radi prikupljanja smeća i otuda menadžment memorije je prilično automatski i programer ne treba da brine o upravljanju memorijom. Ovo takođe pomaže da se lako napiše konkurentne programe.

- Jednostavna specifikacija jezika  
  Jezičke specifikacije su prilično jednostavne.Celokupni Go je dobro dokumentovan i možete ga koristiti da napišete sopstveni kompajler :)

- OpenSource  
  Poslednje, ali ne najmanje bitno, Go je projekat otvorenog koda. Možete učestvovati i doprineti projektu.

#### 1.1.2 Popularni proizvodi sagrađeni koristeći Go

- Google je razvio "Kubernetes" koristeći Go.

- "Docker", svetski poznata platforma za kontejnerizaciju razvijena je na Gou.

- "Dropbox" je prebacio po performansama sve svoje kritične komponente iz
  Python-a u Go.

- Infoblox-ovi "Next Generation Networking Products" se razvijaju koristeći Go.

### 1.2 Instalacija

Go se može instalirati na sve tri platforme Mac, Windows i Linux. Možete preuzeti binarne instalacije za odgovarajuću platformu sa <https://go.dev/dl/>.

#### 1.2.1 Mac OS

Preuzmite Mac OS Installer sa <https://go.dev/dl/>. Dva puta dodirnite da biste započeli instalaciju.Sledite uputstva i to će za rezultat imati instaliran Go u "/usr/local/go" i dodaće direktorijum "/usr/local/go/bin" u PATH varijablu okruženja. Na vama ostaje da dodate GOROOT=/usr/local/go varijablu okruženja.

#### 1.2.2 Windows

Preuzmite MSI Installer sa <https://go.dev/dl/>. Dva puta dodirnite da biste
započeli instalaciju i sledite uputstva. Ovo će instalirati Go na lokaciju
"C:\go" i dodati direktorijum "C:\go\bin" u PATH varijablu okruženja. Na vama je da dodate "GOROOT=c:\go" varijablu okruženja.

#### 1.2.3 Linux

Preuzmite tar datoteku sa <https://go.dev/dl/> i raspakujte je na "/usr/local/go". Dodajte "/usr/local/go/bin" u vašu PATH promenljivu okruženja i dodajte
"GOROOT=/usr/local/go" varijablu okruženja.

#### 1.2.4 Provera instalacije

Da biste proverili da li je uspešno instaliran, unesite komandu "go version" u
Terminal i to će ispisati instaliranu go verziju.

```sh
go version
```

[Sadržaj](000-Sadržaj.md)
