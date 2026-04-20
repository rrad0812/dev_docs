Dinamički korisnički interfejs arhive

Contact.app je prešao dug put od tradicionalne veb aplikacije u stilu web 1.0: dodali smo aktivnu
pretragu, grupno brisanje, neke lepe animacije i mnoštvo drugih funkcija. Dostigli smo nivo
interaktivnosti za koji bi većina veb programera pretpostavila da zahteva neku vrstu okvira za
jednostranične JavaScript aplikacije, ali mi smo to umesto toga uradili koristeći hipermediju zasnovanu
na htmlx-u.

Pogledajmo kako možemo dodati poslednju značajnu funkciju aplikaciji Contact.app: preuzimanje arhive
svih kontakata.

Iz hipermedijske perspektive, preuzimanje datoteke nije baš raketna nauka: koristeći HTTP Content-
Dispositionzaglavlje odgovora, lako možemo reći pregledaču da preuzme i sačuva datoteku na lokalni
računar.

Međutim, učinimo ovaj problem zanimljivijim: dodajmo činjenicu da izvoz može potrajati malo vremena,
od pet do deset sekundi, ili ponekad čak i duže.

To znači da ako bismo implementirali preuzimanje kao "normalan" HTTP zahtev, vođen linkom ili
dugmetom, korisnik bi mogao da sedi sa vrlo malo vizuelne povratne informacije, pitajući se da li se
preuzimanje zaista dešava, dok se izvoz završava. Možda bi čak i odustao u frustraciji i ponovo kliknuo na
kontrolu za preuzimanje hipermedije, što bi izazvalo drugi zahtev za arhiviranje. Nije dobro.

Ovo se ispostavlja kao klasičan problem u razvoju veb aplikacija. Kada se suočimo sa potencijalno
dugotrajnim procesom poput ovog, na kraju imamo dve opcije:

- Kada korisnik pokrene akciju, blokirajte je dok se ne završi, a zatim odgovorite rezultatom.

- Započnite akciju i odmah se vratite, prikazujući neku vrstu korisničkog interfejsa koji ukazuje da su
stvari u toku.

Blokiranje i čekanje da se radnja završi je svakako jednostavniji način da se to reši, ali može biti loše
korisničko iskustvo, posebno ako je potrebno neko vreme da se radnja završi. Ako ste ikada kliknuli na
nešto u aplikaciji u stilu veb 1.0, a zatim morali da sedite tamo ono što izgleda kao večnost pre nego što se
bilo šta desi, videli ste praktične rezultate ovog izbora.

Druga opcija, pokretanje akcije asinhrono (recimo, kreiranjem niti ili njenim slanjem sistemu za
pokretanje zadataka) je mnogo lepša sa stanovišta korisničkog iskustva: server može odmah da odgovori i
korisnik ne mora da sedi tamo pitajući se šta se dešava.

Ali pitanje je, čime odgovarate ? Posao verovatno još nije završen, tako da ne možete dati vezu do
rezultata.

Videli smo nekoliko različitih "jednostavnih" pristupa u ovom scenariju u raznim veb aplikacijama:

- Obavestite korisnika da je proces počeo i da će mu biti poslat imejl sa linkom do rezultata završenog
procesa kada bude završen.

- Obavestite korisnika da je proces započet i preporučite mu da ručno osveži stranicu kako bi video
status procesa.

- Obavestite korisnika da je proces započet i automatski osvežite stranicu svakih nekoliko sekundi
koristeći JavaScript.

Sve ovo će funkcionisati, ali nijedno od toga nije odlično korisničko iskustvo.

Ono što bismo zaista želeli u ovom scenariju je nešto sličnije onome što vidite kada, na primer,



preuzmete veliku datoteku preko pregledača: lepu traku napretka koja pokazuje gde se u procesu nalazite
i, kada je proces završen, vezu na koju možete odmah kliknuti da biste videli rezultat procesa.

Ovo može zvučati kao nešto što je nemoguće implementirati pomoću hipermedija, i, iskreno, moraćemo
prilično snažno da pokrenemo htmlx da bi sve ovo funkcionisalo, ali kada se završi, neće biti toliko koda i
moći ćemo da postignemo korisničko iskustvo koje želimo za ovu funkciju arhiviranja.

Zahtevi za korisnički interfejs
Pre nego što se upustimo u implementaciju, hajde da u opštim crtama razmotrimo kako bi trebalo da
izgleda naš novi korisnički interfejs: želimo dugme u aplikaciji sa oznakom "Preuzmi arhivu kontakata".
Kada korisnik klikne na to dugme, želimo da ga zamenimo korisničkim interfejsom koji prikazuje
napredak procesa arhiviranja, idealno sa trakom napretka. Kako zadatak arhiviranja napreduje, želimo da
pomeramo traku napretka ka završetku. Zatim, kada je zadatak arhiviranja završen, želimo da korisniku
prikažemo vezu za preuzimanje datoteke arhive kontakata.

Da bismo zapravo izvršili arhiviranje, koristićemo Pajton klasu, Archiver, koja implementira sve potrebne
funkcionalnosti. Kao i kod Contactklase, nećemo ulaziti u detalje implementacije Archiver, jer je to van
okvira ove knjige. Za sada je potrebno samo da znate da ona pruža sve ponašanje na strani servera
neophodno za pokretanje procesa arhiviranja kontakata i dobijanje rezultata kada se taj proces završi.

Archiverdaje nam sledeće metode za rad:

- status()- Niz koji predstavlja status preuzimanja, bilo Waiting, RunningiliComplete

- progress()- Broj između 0 i 1, koji pokazuje koliko je napredova zadatak arhiviranja ostvario

- run()- Pokreće novi zadatak arhiviranja (ako je trenutni status Waiting)

- reset()- Otkazuje trenutni zadatak arhiviranja, ako postoji, i vraća se u stanje "Čekanje"

- archive_file()- Putanja do arhivske datoteke koja je kreirana na serveru, kako bismo je mogli
poslati klijentu

- get()- Metod klase koji nam omogućava da dobijemo Arhivator za trenutnog korisnika

Prilično nekomplikovan API.

Jedini donekle komplikovan aspekt celog API-ja je taj što metod run()nije blokirajući. To znači da ne
kreira odmah arhivsku datoteku, već pokreće pozadinski zadatak (kao nit) da bi izvršio stvarno
arhiviranje. Ovo može biti zbunjujuće ako niste navikli na višenitni rad u kodu: možda očekujete da
metoda run()"blokira", odnosno da zapravo izvrši ceo izvoz i vrati se tek kada se završi. Ali, ako bi to
uradila, ne bismo mogli da pokrenemo proces arhiviranja i odmah prikažemo željeni korisnički interfejs
koji prikazuje napredak arhiviranja.

Početak naše implementacije
Sada imamo sve što nam je potrebno da počnemo sa implementacijom našeg korisničkog interfejsa:
razumnu skicu kako će izgledati i logiku domena koja ga podržava.

Dakle, za početak, imajte na umu da je ovaj korisnički interfejs uglavnom samostalan: želimo da
zamenimo dugme trakom napretka preuzimanja, a zatim traku napretka linkom za preuzimanje rezultata
završenog procesa arhiviranja.

Činjenica da će naš arhivirani korisnički interfejs biti unutar određenog dela korisničkog interfejsa je jak
nagoveštaj da ćemo želeti da kreiramo novi šablon za njegovu obradu. Nazovimo ovaj šablon
archive_ui.html.

Takođe imajte na umu da ćemo želeti da zamenimo ceo korisnički interfejs za preuzimanje u više



slučajeva:

- Kada započnemo preuzimanje, želećemo da zamenimo dugme trakom napretka.

- Kako proces arhiviranja bude odvijao, želećemo da zamenimo/ažuriramo traku napretka.

- Kada se proces arhiviranja završi, želećemo da zamenimo traku napretka linkom za preuzimanje.

Da bismo ažurirali korisnički interfejs na ovaj način, potrebno je da postavimo dobru metu za ažuriranja.
Dakle, hajde da umotamo ceo korisnički interfejs u divoznaku, a zatim je koristimo divkao metu za sve
naše operacije.

Evo početka šablona za naš novi korisnički interfejs arhive:

<div id="archive-ui"
  hx-target="this" <1>
  hx-swap="outerHTML"> <2>
</div>

Naš početni šablon korisničkog interfejsa arhive

1. Ovaj div će biti cilj za sve elemente unutar njega.

2. Zamenite ceo div svaki put kada koristite outerHTML.

Zatim, dodajmo dugme "Preuzmi arhivu kontakata" koje divće pokrenuti proces arhiviranja, a zatim
preuzimanja. Koristićemo POSTputanju /contacts/archiveda bismo pokrenuli proces arhiviranja:

<div id="archive-ui" hx-target="this" hx-swap="outerHTML">
<button hx-post="/contacts/archive"> <1>

    Download Contact Archive
</button>

</div>

Dodavanje dugmeta za arhiviranje

1. Ovo dugme će izdati POSTdo /contacts/archive.

Konačno, hajde da uključimo ovaj novi šablon u naš glavni index.htmlšablon, iznad tabele kontakata:

{% block content %}
  {% include 'archive_ui.html' %} <1>

<form action="/contacts" method="get" class="tool-bar">

Naš početni šablon korisničkog interfejsa arhive

1. Ovaj šablon će sada biti uključen u glavni šablon.

Kada je to završeno, sada imamo dugme koje se prikazuje u našoj veb aplikaciji za pokretanje
preuzimanja. Pošto okruženje divima hx-target="this"na sebi, dugme će naslediti taj cilj i zameniti to
okruženje divbilo kojim HTML kodom koji se vrati iz POSTdo /contacts/archive.

Dodavanje krajnje tačke arhiviranja
Naš sledeći korak je obrada POSTkoju naše dugme pravi. Želimo da dobijemo Archiverza trenutnog
korisnika i da pozovemo run()metodu na njemu. Ovo će pokrenuti proces arhiviranja. Zatim ćemo
prikazati novi sadržaj koji ukazuje da je proces u toku.

Da bismo to uradili, želimo ponovo da koristimo archive_uišablon za obradu renderovanja korisničkog
interfejsa arhive za oba stanja, kada arhivator "Čeka" i kada "Radi". (Obradićemo stanje "Završeno"
uskoro).



Ovo je veoma uobičajen obrazac: sve različite potencijalne korisničke interfejse za dati deo korisničkog
interfejsa stavljamo u jedan šablon i uslovno prikazujemo odgovarajući interfejs. Čuvanjem svega u
jednoj datoteci, drugim programerima (ili nama, ako se vratimo posle nekog vremena!) je mnogo lakše da
razumemo kako tačno korisnički interfejs funkcioniše na strani klijenta.

Pošto ćemo uslovno prikazivati različite korisničke interfejse na osnovu stanja arhivatora, moraćemo da
prosledimo arhivator šablonu kao parametar. Dakle, ponovo: potrebno je da pozovemo run()arhivator u
našem kontroleru, a zatim da ga prosledimo šablonu, kako bi mogao da prikaže korisnički interfejs na
način koji odgovara trenutnom statusu procesa arhiviranja.

Evo kako izgleda kod:

@app.route("/contacts/archive", methods=["POST"]) <1>
def start_archive():
    archiver = Archiver.get() <2>
    archiver.run() <3>

return render_template("archive_ui.html", archiver=archiver) <4>

Kod na strani servera za pokretanje procesa arhiviranja

1. Ručka POSTza /contacts/archive.

2. Potražite Arhivator.

3. Pozovite neblokirajuću run()metodu na njoj.

4. Renderujte archive_ui.htmlšablon, prosledivši ga arhivatoru.

Uslovno prikazivanje korisničkog interfejsa napretka
Sada ćemo usmeriti pažnju na ažuriranje našeg korisničkog interfejsa za arhiviranje podešavanjem
archive_ui.htmluslovnog prikazivanja različitog sadržaja u zavisnosti od stanja procesa arhiviranja.

Podsetimo se da arhivator ima status()metodu. Kada prosledimo arhivator kao promenljivu šablonu,
možemo konsultovati ovu status()metodu da bismo videli status procesa arhiviranja.

Ako arhivator ima status Waiting, želimo da prikažemo dugme "Preuzmi arhivu kontakata". Ako je status
Running, želimo da prikažemo poruku koja ukazuje da je izrada u toku. Hajde da ažuriramo naš kod
šablona da bi to uradio:

<div id="archive-ui" hx-target="this" hx-swap="outerHTML">
  {% if archiver.status() == "Waiting" %} <1>

<button hx-post="/contacts/archive">
      Download Contact Archive

</button>
  {% elif archiver.status() == "Running" %} <2>
    Running... <3>
  {% endif %}
</div>

Dodavanje uslovnog renderovanja

1. Prikaži dugme za arhiviranje samo ako je status "Čekanje".

2. Prikaži drugačiji sadržaj kada je status "Pokreće se".

3. Za sada, samo tekst koji kaže da je proces u toku.

U redu, odlično, imamo uslovnu logiku u našem prikazu šablona i logiku na strani servera koja podržava
pokretanje procesa arhiviranja. Još uvek nemamo traku napretka, ali stići ćemo do nje! Da vidimo kako
ovo funkcioniše u sadašnjem stanju i osvežimo glavnu stranicu naše aplikacije…

UndefinedError
jinja2.exceptions.UndefinedError: 'archiver' is undefined



Nešto je pošlo po zlu

Auč!

Odmah dobijamo poruku o grešci. Zašto? Ah, uključujemo archive_ui.htmlu index.htmlšablon, ali sada
archive_ui.htmlšablon očekuje da mu se prosledi arhivator, tako da može uslovno da prikaže ispravan
korisnički interfejs.

To je jednostavno rešenje: samo treba da propustimo arhivator kada renderujemo šablon index.html:

@app.route("/contacts")
def contacts():
    search = request.args.get("q")

if search is not None:
        contacts_set = Contact.search(search)

if request.headers.get('HX-Trigger') == 'search':
return render_template("rows.html", contacts=contacts_set)

else:
        contacts_set = Contact.all()

return render_template("index.html",
      contacts=contacts_set, archiver=Archiver.get()) <1>

Uključivanje arhivera kada prikazujemo index.html

1. Prođite kroz arhivator do glavnog šablona

Sada kada je to završeno, možemo da učitamo stranicu. I, naravno, možemo videti dugme "Preuzmi
arhivu kontakata".

Kada kliknemo na njega, dugme se zamenjuje sadržajem "Pokreće se…" i možemo videti u našoj razvojnoj
konzoli na strani servera da se zadatak zaista pravilno pokreće.

Ankete
To je definitivno napredak, ali ovde nemamo baš najbolji indikator napretka: samo neki statički tekst koji
korisniku govori da je proces u toku.

Želimo da se sadržaj ažurira kako proces napreduje i, idealno, da se prikaže traka napretka koja pokazuje
koliko je proces odmakao. Kako to možemo da uradimo u htmlx-u koristeći običan hipermedijski format?

Tehnika koju ovde želimo da koristimo naziva se "anketiranje", gde izdajemo zahtev u određenom
intervalu i ažuriramo korisnički interfejs na osnovu novog stanja servera.

Htmx nudi dve vrste anketiranja. Prvo je "anketiranje sa fiksnom brzinom", koje koristi posebnu hx-
triggersintaksu da bi naznačilo da nešto treba da se anketira u fiksnom intervalu.

Evo jednog primera:

<div hx-get="/messages" hx-trigger="every 3s"> <1>
</div>

Fiksni interval anketiranja

1. Pokreće se GETsvake /messagestri sekunde.

Ovo odlično funkcioniše u situacijama kada želite da anketirate neograničeno, na primer ako želite da
stalno anketirate za nove poruke koje ćete prikazati korisniku. Međutim, anketiranje sa fiksnom brzinom
nije idealno kada imate definitivan proces nakon kojeg želite da zaustavite anketiranje: anketiranje se
nastavlja zauvek, sve dok se element na kojem se nalazi ne ukloni iz DOM-a.

U našem slučaju, imamo definitivan proces sa završetkom. Dakle, biće bolje koristiti drugu tehniku
anketiranja, poznatu kao "anketiranje učitavanja". Kod anketiranja učitavanja, koristimo činjenicu da
htmx pokreće događaj loadkada se sadržaj učita u DOM. Možemo kreirati okidač za ovaj loaddogađaj i



dodati malo kašnjenja tako da se zahtev ne pokrene odmah.

Ovim možemo uslovno renderovati hx-triggerna svaki zahtev: kada se proces završi, jednostavno ne
uključujemo loadokidač i ispitivanje učitavanja se zaustavlja. Ovo nudi lep i jednostavan način za
ispitivanje dok se određeni proces ne završi.

Korišćenje anketiranja za ažuriranje korisničkog interfejsa arhive

Koristićemo "load polling" da bismo ažurirali naš korisnički interfejs kako arhivar napreduje. Da bismo
prikazali napredak, koristićemo traku napretka zasnovanu na CSS-u, koristeći metodu progress()koja
vraća broj između 0 i 1 koji pokazuje koliko je proces arhiviranja blizu završetka.

Evo isečka HTML koda koji ćemo koristiti:

<div class="progress">
<div class="progress-bar"

         style="width:{{ archiver.progress() * 100 }}%"></div> <1>
</div>

Traka napretka zasnovana na CSS-u

1. Širina unutrašnjeg elementa odgovara napretku.

Ova traka napretka zasnovana na CSS-u ima dve komponente: spoljašnju divkoja pruža žičani okvir za
traku napretka i unutrašnju divkoja je stvarni indikator trake napretka. Širinu unutrašnje trake napretka
postavljamo na neki procenat (imajte na umu da progress()rezultat treba da pomnožimo sa 100 da bismo
dobili procenat) i to će učiniti da indikator napretka bude odgovarajuće širine unutar roditeljskog div-a.

Hajde da ažuriramo našu traku napretka kako bismo imali odgovarajuće ARIA uloge i vrednosti:

<div class="progress">
<div class="progress-bar"

    role="progressbar" <1>
    aria-valuenow="{{ archiver.progress() * 100 }}" <2>
    style="width:{{ archiver.progress() * 100 }}%"></div>
</div>

Traka napretka zasnovana na CSS-u

1. Ovaj element će služiti kao traka napretka

2. Napredak će biti procenat potpunosti arhivara, gde 100 označava potpuno završeno

Konačno, radi potpunosti, evo CSS-a koji ćemo koristiti za ovu traku napretka:

.progress {
height: 20px;
margin-bottom: 20px;
overflow: hidden;
background-color: #f5f5f5;
border-radius: 4px;
box-shadow: inset 0 1px 2px rgba(0,0,0,.1);

}

.progress-bar {
float: left;
width: 0%;
height: 100%;
font-size: 12px;
line-height: 20px;
color: #fff;
text-align: center;
background-color: #337ab7;
box-shadow: inset 0 -1px 0 rgba(0,0,0,.15);
transition: width.6s ease;

}



CSS za našu traku napretka

Naša traka napretka zasnovana na CSS-u, kao što je implementirano u [lst-progress-bar-css]

Dodavanje korisničkog interfejsa trake napretka

Dodajmo kod za našu traku napretka u naš archive_ui.htmlšablon za slučaj kada je arhivator pokrenut i
ažurirajmo kopiju da piše "Kreiranje arhive…":

<div id="archive-ui" hx-target="this" hx-swap="outerHTML">
  {% if archiver.status() == "Waiting" %}

<button hx-post="/contacts/archive">
      Download Contact Archive

</button>
  {% elif archiver.status() == "Running" %}

<div>
      Creating Archive...

<div class="progress"> <1>
<div class="progress-bar" role="progressbar"

          aria-valuenow="{{ archiver.progress() * 100}}"
          style="width:{{ archiver.progress() * 100 }}%"></div>

</div>
</div>

  {% endif %}
</div>

Dodavanje trake napretka

1. Naša nova sjajna traka napretka

Sada kada kliknemo na dugme "Preuzmi arhivu kontakata", dobijamo traku napretka. Ali ona se i dalje ne
ažurira jer još nismo implementirali anketiranje učitavanja: samo stoji tu, na nuli.

Da bi se traka napretka dinamički ažurirala, moraćemo da implementiramo anketiranje učitavanja
pomoću hx-trigger. Možemo ovo dodati skoro svakom elementu unutar uslovnog bloka za vreme kada je
arhivator pokrenut, pa hajde da ga dodamo onome divšto se obavija oko teksta "Kreiranje arhive…" i
trake napretka.

Hajde da izvršimo anketiranje izdavanjem HTTP zahteva GETna istu putanju kao i POST: /contacts/archive.

<div id="archive-ui" hx-target="this" hx-swap="outerHTML">
  {% if archiver.status() == "Waiting" %}

<button hx-post="/contacts/archive">
      Download Contact Archive

</button>
  {% elif archiver.status() == "Running" %}

<div hx-get="/contacts/archive" hx-trigger="load delay:500ms"> <1>
      Creating Archive...

<div class="progress" >
<div class="progress-bar" role="progressbar"

          aria-valuenow="{{ archiver.progress() * 100}}"
          style="width:{{ archiver.progress() * 100 }}%"></div>

</div>
</div>

  {% endif %}
</div>

Implementacija anketiranja opterećenja



1. Izdaj GETdo /contacts/archive500 milisekundi nakon što se sadržaj učita.

Kada GETse ovo izda za /contacts/archive, zameniće divsa id-jem archive-ui, ne samo sebe. hx-
targetAtribut na divsa id-jem archive-uinasleđuju svi podređeni elementi unutar tog, tako da ćediv sva
podređena elementa ciljati taj najudaljeniji divu archive_ui.htmldatoteci.

Sada treba da obradimo to GETna /contacts/archiveserveru. Srećom, ovo je prilično jednostavno: sve što
želimo da uradimo je da ponovo renderujemo archive_ui.htmlpomoću arhivera:

@app.route("/contacts/archive", methods=["GET"]) <1>
def archive_status():
    archiver = Archiver.get()

return render_template("archive_ui.html", archiver=archiver) <2>

Rukovanje ažuriranjima napretka

1. ručka GETza /contacts/archiveputanju

2. samo ponovo renderujte archive_ui.htmlšablon

Kao i mnogo toga drugog kod hipermedija, kod je veoma čitljiv i nije komplikovan.

Sada, kada kliknemo na "Preuzmi arhivu kontakata", dobijamo traku napretka koja se ažurira svakih 500
milisekundi. Kao rezultat poziva funkcije " archiver.progress()incrementally updates" od 0 do 1, traka
napretka se pomera po ekranu. Veoma kul!

Preuzimanje rezultata

Imamo još jedno poslednje stanje koje treba da obradimo, slučaj kada archiver.status()je podešeno na
"Završeno" i postoji JSON arhiva podataka spremna za preuzimanje. Kada je arhivator završen, možemo
da dobijemo lokalnu JSON datoteku na serveru od arhivatora putem poziva archive_file().

Dodajmo još jedan slučaj našoj if naredbi da bismo obrađivali stanje "Završeno" i, kada se zadatak
arhiviranja završi, prikažimo vezu do nove putanje, /contacts/archive/file, koja će odgovoriti
arhiviranom JSON datotekom. Evo novog koda:

<div id="archive-ui" hx-target="this" hx-swap="outerHTML">
  {% if archiver.status() == "Waiting" %}

<button hx-post="/contacts/archive">
      Download Contact Archive

</button>
  {% elif archiver.status() == "Running" %}

<div hx-get="/contacts/archive" hx-trigger="load delay:500ms">
      Creating Archive...

<div class="progress" >
<div class="progress-bar" role="progressbar"

          aria-valuenow="{{ archiver.progress() * 100}}"
          style="width:{{ archiver.progress() * 100 }}%"></div>

</div>
</div>

  {% elif archiver.status() == "Complete" %} <1>
<a hx-boost="false" href="/contacts/archive/file">

      Archive Ready! Click here to download. &downarrow;
</a> <2>

  {% endif %}
</div>

Prikazivanje linka za preuzimanje kada se arhiviranje završi

1. Ako je status "Završeno", prikaži link za preuzimanje.

2. Link će izdati poruku GETza /contacts/archive/file.

Imajte na umu da je link hx-boostpodešen na false. Ovo je tako da link neće naslediti ponašanje boost-a
koje je prisutno za druge linkove i stoga neće biti izdato putem AJAX-a. Želimo ovo "normalno"



ponašanje linka jer AJAX zahtev ne može direktno da preuzme datoteku, dok obična oznaka sidra može.

Preuzimanje završene arhive

Poslednji korak je obrada GETzahteva za /contacts/archive/file. Želimo da pošaljemo datoteku koju je
arhivator kreirao klijentu. Imamo sreće: Flask ima mehanizam za slanje datoteke kao preuzetog
odgovora, metodu send_file().

Kao što vidite u kodu koji sledi, prosleđujemo tri argumenta send_file(): putanju do arhivske datoteke
koju je arhivator kreirao, ime datoteke koju želimo da pregledač kreira i da li želimo da se pošalje "kao
prilog". Ovaj poslednji argument govori Flasku da postavi HTTP zaglavlje odgovora Content-Dispositionna
attachmentdato ime datoteke; to je ono što pokreće ponašanje pregledača pri preuzimanju datoteka.

@app.route("/contacts/archive/file", methods=["GET"])
def archive_content():
    manager = Archiver.get()

return send_file(
      manager.archive_file(), "archive.json", as_attachment=True) <1>

Slanje datoteke klijentu

1. Pošaljite datoteku klijentu putem Flask-ove send_file()metode.

Savršeno. Sada imamo veoma elegantan korisnički interfejs za arhiviranje. Kliknite na dugme "Preuzmi
arhivu kontakata" i pojavljuje se traka napretka. Kada traka napretka dostigne 100%, ona nestaje i
pojavljuje se veza za preuzimanje arhivske datoteke. Korisnik zatim može kliknuti na tu vezu i preuzeti
svoju arhivu.

Nudimo korisničko iskustvo koje je mnogo jednostavnije za korišćenje od uobičajenog iskustva "klikni i
čekaj" na mnogim veb-sajtovima.

Uglađivanje stvari: Animacije u Htmx-u
Koliko god da je ovaj korisnički interfejs lep, postoji jedna mala smetnja: kako se traka napretka ažurira,
ona "skače" sa jedne pozicije na drugu. Ovo se pomalo oseća kao potpuno osvežavanje stranice u
aplikacijama stila web 1.0. Da li postoji način da ovo popravimo? (Očigledno postoji, zato smo se odlučili
za divumesto progresselementa !)

Hajde da prođemo kroz uzrok ovog vizuelnog problema i kako bismo ga mogli rešiti. (Ako žurite da
pronađete odgovor, slobodno pređite na "naše rešenje".)

Ispostavlja se da postoji nativna HTML tehnologija za ublažavanje promena na elementu iz jednog stanja
u drugo: CSS Transitions API, isti onaj o kome smo govorili u 4. poglavlju. Koristeći CSS Transitions,
možete glatko animirati element između različitih stilova koristeći svojstvo transition.

Ako pogledate našu CSS definiciju klase.progress-bar, videćete sledeću definiciju prelaza:. transition:
width.6s ease;To znači da kada se širina trake napretka promeni sa, recimo, 20% na 30%, pregledač će
animirati tokom perioda od 0,6 sekundi koristeći funkciju "ease" (koja ima lep efekat ubrzavanja/
usporavanja).

Pa zašto se ta tranzicija ne primenjuje u našem trenutnom korisničkom interfejsu? Razlog je taj što u
našem primeru htmx zamenjuje traku napretka novom svaki put kada vrši anketu. Ne ažurira širinu
postojećeg elementa. CSS tranzicije, nažalost, primenjuju se samo kada se svojstva postojećeg elementa
promene u tekstu, a ne kada se element zameni.

To je razlog zašto aplikacije zasnovane na čistom HTML-u mogu delovati trzavo i neuglađeno u poređenju
sa svojim SPA pandanima: teško je koristiti CSS prelaze bez JavaScript-a.

Ali postoje i dobre vesti: htmx ima način da koristi CSS prelaze čak i kada zamenjuje sadržaj u DOM-u.



Korak "smirivanja" u Htmx-u

Kada smo u 4. poglavlju razmatrali model zamene htmx, fokusirali smo se na klase koje htmx dodaje i
uklanja, ali smo preskočili proces "postavljanja". U htmx-u, postavljanje uključuje nekoliko koraka: kada
htmx treba da zameni deo sadržaja, on pregleda novi sadržaj i pronalazi sve elemente sa id. Zatim u
postojećem sadržaju traži elemente sa istim id.

Ako postoji jedan, on vrši sledeće, donekle složeno mešanje:

- Novi sadržaj privremeno dobija atribute starog sadržaja.

- Novi sadržaj je umetnut.

- Nakon kratkog kašnjenja, atributi novog sadržaja su vraćeni na stvarne vrednosti.

Pa, šta se ovim čudnim malim plesom treba postići?

Pa, ako element ima stabilan identifikator između zamena, sada možete pisati CSS prelaze između
različitih stanja. Pošto novi sadržaj nakratko ima stare atribute, normalan CSS mehanizam prelaska će se
aktivirati kada se stvarne vrednosti vrate.

Naše rešenje za izglađivanje

Dakle, stižemo do našeg rešenja.

Sve što treba da uradimo je da dodamo stabilan ID našem progress-barelementu.

<div class="progress" >
<div id="archive-progress" class="progress-bar" role="progressbar"

         aria-valuenow="{{ archiver.progress() * 100 }}"
         style="width:{{ archiver.progress() * 100 }}%"></div> <1>
</div>

Sređivanje stvari

1. Div trake napretka sada ima stabilan ID u svim zahtevima.

Uprkos komplikovanim mehanizmima koji se odvijaju iza kulisa u htmx-u, rešenje je jednostavno kao
dodavanje idatributa stable elementu koji želimo da animiramo.

Sada, umesto da skače na svako ažuriranje, traka napretka bi trebalo glatko da se kreće po ekranu dok se
ažurira, koristeći CSS tranziciju definisanu u našem stilskom listu. Model zamene htmlx-a nam
omogućava da ovo postignemo čak i ako zamenjujemo sadržaj novim HTML-om.

I vuala: imamo lepu, glatko animiranu traku napretka za našu funkciju arhiviranja kontakata. Rezultat
ima izgled i osećaj rešenja zasnovanog na JavaSkriptu, ali smo to uradili jednostavnošću pristupa
zasnovanog na HTML-u.

E to, dragi čitaoče, zaista budi radost.

Zatvaranje korisničkog interfejsa za preuzimanje
Neki korisnici mogu promeniti mišljenje i odlučiti da ne preuzimaju arhivu. Možda nikada neće videti
našu slavnu traku napretka, ali to je u redu. Daćemo ovim korisnicima dugme za odbacivanje veze za
preuzimanje i vraćanje u originalno stanje korisničkog interfejsa za izvoz.

Da bismo to uradili, dodaćemo dugme koje izdaje oznaku DELETEputanji /contacts/archive, što ukazuje da
se trenutna arhiva može ukloniti ili očistiti.

Dodaćemo ga nakon linka za preuzimanje, ovako:



<a hx-boost="false" href="/contacts/archive/file">
 Archive Ready! Click here to download. &downarrow;
</a>
<button hx-delete="/contacts/archive">Clear Download</button> <1>

Brisanje preuzimanja

1. Jednostavno dugme koje izdaje odredbu DELETEza /contacts/archive.

Sada korisnik ima dugme na koje može da klikne da bi zatvorio link za preuzimanje arhive. Ali moraćemo
da ga povežemo na strani servera. Kao i obično, ovo je prilično jednostavno: kreiramo novi rukovalac za
DELETEHTTP akciju, pozivamo reset()metodu na arhivatoru i ponovo prikazujemo archive_ui.htmlšablon.

Pošto ovo dugme prima istu hx-targetkonfiguraciju hx-swapkao i sve ostalo, ono "jednostavno radi".

Evo koda na strani servera:

@app.route("/contacts/archive", methods=["DELETE"])
def reset_archive():
    archiver = Archiver.get()
    archiver.reset() <1>

return render_template("archive_ui.html", archiver=archiver)

Rukovalac za resetovanje preuzimanja

1. Pozovite reset()arhivara

Ovo izgleda prilično slično našim drugim rukovaocima, zar ne?

Naravno! To je ideja!

Alternativno korisničko iskustvo: Automatsko preuzimanje
Iako preferiramo trenutno korisničko iskustvo za arhiviranje kontakata, postoje i druge alternative.
Trenutno, traka napretka prikazuje napredak procesa i, kada se završi, korisniku se prikazuje link za
preuzimanje datoteke. Još jedan obrazac koji vidimo na vebu je "automatsko preuzimanje", gde se
datoteka preuzima odmah bez potrebe da korisnik klikne na link.

Ovu funkcionalnost možemo prilično lako dodati našoj aplikaciji uz samo malo skriptovanja. Detaljnije
ćemo razmotriti skriptovanje u aplikaciji vođenoj hipermedijom u 9. poglavlju, ali, ukratko: skriptovanje
je sasvim prihvatljivo u HDA, sve dok ne zamenjuje osnovnu mehaniku hipermedije aplikacije.

Za našu funkciju automatskog preuzimanja koristićemo _hyperscript, našu preferiranu opciju
skriptovanja. JavaScript bi takođe ovde funkcionisao i bio bi gotovo jednako jednostavan; ponovo ćemo
detaljno razmotriti opcije skriptovanja u 9. poglavlju.

Sve što treba da uradimo da bismo implementirali funkciju automatskog preuzimanja je sledeće: kada se
link za preuzimanje prikaže, automatski kliknemo na link za korisnika.

Kod _hyperscript se čita gotovo isto kao i prethodna rečenica (što je glavni razlog zašto volimo
hiperskript):

<a hx-boost="false" href="/contacts/archive/file"
  _="on load click() me"> <1>
  Archive Downloading! Click here if the download does not start.
</a>

Automatsko preuzimanje

1. Malo _hiperskripta da bi se datoteka automatski preuzela.

Ključno je da skriptovanje ovde jednostavno poboljšava postojeću hipermediju, umesto da je zamenjuje
zahtevom koji nije hipermedijski. Ovo je skriptovanje prilagođeno hipermediji, što ćemo detaljnije



objasniti uskoro.

Dinamički arhivski korisnički interfejs: Završeno
U ovom poglavlju smo uspeli da kreiramo dinamički korisnički interfejs za našu funkcionalnost
arhiviranja kontakata, sa trakom napretka i automatskim preuzimanjem, i skoro sve smo uradili — sa
izuzetkom malog dela skriptovanja za automatsko preuzimanje — u čistoj hipermediji. Bilo je potrebno
oko 16 linija prednjeg koda i 16 linija bekend koda da bi se cela stvar izgradila.

HTML, uz malo pomoći hipermedijske JavaScript biblioteke kao što je htmx, zapravo može biti izuzetno
moćan i ekspresivan.

HTML napomene: Markdown supa
Markdaun sup je manje poznati brat jezika <div>sup. To je rezultat toga što se veb programeri
ograničavaju na skup elemenata za koje Markdaun jezik pruža skraćenice, čak i kada su ti elementi
netačni. Još ozbiljnije, važno je biti svestan pune moći naših alata, uključujući HTML. Razmotrite sledeći
primer citata u IEEE stilu:

[1] C.H. Gross, A. Stepinski, and D. Akşimşek, <1>
  _Hypermedia Systems_, <2>
  Bozeman, MT, USA: Big Sky Software.
  Available: <https://hypermedia.systems/>

1. Referentni broj je napisan u zagradama.

2. Podvlake oko naslova knjige kreiraju element <em>.

Ovde se koristi <em> jer je to jedini Markdown element koji je podrazumevano prikazan kurzivom. Ovo
ukazuje da je naslov knjige naglašen, ali svrha je da se označi kao naslov dela. HTML ima element
<cite>koji je namenjen upravo za ovu svrhu.

Štaviše, iako je ovo numerisana lista savršena za <ol>element, što Markdown podržava, umesto toga se za
referentne brojeve koristi običan tekst. Zašto bi to moglo biti tako? IEEE stil citiranja zahteva da se ovi
brojevi prikažu u uglastim zagradama. Ovo bi se moglo postići pomoću <ol>CSS-a, ali Markdown nema
način da doda klasu elementima, što znači da bi se uglaste zagrade primenjivale na sve uređene liste.

Ne ustručavajte se da koristite ugrađeni HTML u Markdown-u. Za veće sajtove, razmotrite i Markdown
ekstenzije.

{.ieee-reference-list} <1>
1. C.H. Gross, A. Stepinski, and D. Akşimşek, <2>
<cite>Hypermedia Systems</cite>, <3>

  Bozeman, MT, USA: Big Sky Software.
  Available: <https://hypermedia.systems/>

1. Mnogi Markdaun dijalekti nam omogućavaju da dodamo identifikatore, klase i atribute koristeći
vitičaste zagrade.

2. Sada možemo koristiti element <ol> i kreirati zagrade u CSS-u.

3. Koristimo <cite>da označimo naslov dela koje se citira (ne ceo citat!).

Takođe možete koristiti prilagođene procesore za kreiranje dodatno detaljnog HTML-a umesto da ga
pišete ručno:

{% reference_list %} <1>
[hypers2023]: <2>
  C.H. Gross, A. Stepinski, and D. Akşimşek, _Hypermedia Systems_,
  Bozeman, MT, USA: Big Sky Software, 2023.
  Available: <https://hypermedia.systems/>
{% end %}



1. reference_listje makro koji će transformisati običan tekst u veoma detaljan HTML.

2. Procesor takođe može da razreši identifikatore, tako da ne moramo ručno da održavamo redosled
liste referenci i sinhronizaciju citata u tekstu.
