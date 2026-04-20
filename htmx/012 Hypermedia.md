Hiperpogled: Mobilni hipermedijalni sistem

Možda će vam se oprostiti ako pomislite da je hipermedijalna arhitektura sinonim za veb, veb
pregledače i HTML. Nema sumnje, veb je najveći hipermedijalni sistem, a veb pregledači su
najpopularniji hipermedijalni klijent. Dominacija veba u diskusijama o hipermedijalnim
tehnologijama olakšava zaboravljanje da je hipermedijalni koncept opšti koncept i da se može
primeniti na sve vrste platformi i aplikacija. U ovom poglavlju ćemo videti hipermedijalnu
arhitekturu primenjenu na platformu koja nije veb: izvorne mobilne aplikacije.

Mobilne platforme imaju drugačija ograničenja od veba. Zahtevaju drugačije kompromise i
dizajnerske odluke. Ipak, koncepti hipermedije, HATEOAS-a i REST-a mogu se direktno
primeniti za izgradnju zanimljivih mobilnih aplikacija.

U ovom poglavlju ćemo obraditi nedostatke trenutnog stanja razvoja mobilnih aplikacija i kako
hipermedijska arhitektura može da reši ove probleme. Zatim ćemo pogledati put ka hipermediji
na mobilnim uređajima: Hyperview, okvir za mobilne aplikacije koji koristi hipermedijsku
arhitekturu. Zaključićemo pregledom HXML-a, hipermedijskog formata koji koristi Hyperview.

Stanje razvoja mobilnih aplikacija
Pre nego što možemo da razgovaramo o tome kako primeniti hipermediju na mobilne platforme,
moramo da razumemo kako se obično grade nativne mobilne aplikacije. Koristim reč "nativni"
da označim kod napisan uz SDK koji obezbeđuje operativni sistem telefona (obično Android ili
iOS). Ovaj kod je upakovan u izvršnu binarnu datoteku i otpremljen je i odobren putem
prodavnica aplikacija koje kontrolišu Google i Apple. Kada korisnici instaliraju ili ažuriraju
aplikaciju, preuzimaju ovu izvršnu datoteku i pokreću kod direktno na operativnom sistemu
svog uređaja. Na ovaj način, mobilne aplikacije imaju mnogo toga zajedničkog sa desktop
aplikacijama stare škole za Mac, Windows ili Linux. Postoji jedna važna razlika između desktop
aplikacija za računare iz prošlosti i današnjih mobilnih aplikacija. Danas su skoro sve mobilne
aplikacije "umrežene". Pod umreženim mislimo da aplikacija treba da čita i piše podatke preko
interneta da bi pružila svoju osnovnu funkcionalnost. Drugim rečima, umrežena mobilna
aplikacija treba da implementira klijent-server arhitekturu.

Prilikom implementacije klijent-server arhitekture, programer mora da donese odluku: Da li
aplikacija treba da bude dizajnirana kao tanki ili debeli klijent? Trenutni mobilni ekosistemi
snažno guraju programere ka pristupu debelog klijenta. Zašto? Zapamtite, Android i iOS
zahtevaju da nativna mobilna aplikacija bude upakovana i distribuirana kao izvršna binarna
datoteka. Nema načina da se to zaobiđe. Pošto programer treba da napiše kod za pakovanje u
izvršnu datoteku, čini se logičnim da se deo logike aplikacije implementira u tom kodu. Kod
može takođe da pokrene HTTP pozive serveru radi preuzimanja podataka, a zatim da te podatke
prikaže koristeći biblioteke korisničkog interfejsa platforme. Stoga, programeri su prirodno
dovedeni u obrazac debelog klijenta koji izgleda otprilike ovako:

- Klijent sadrži kod za slanje API zahteva serveru i kod za prevođenje tih odgovora na
ažuriranja korisničkog interfejsa

- Server implementira HTTP API koji govori JSON i malo zna o stanju klijenta.

Baš kao i kod SPA-ova na vebu, ova arhitektura ima veliki nedostatak: logika aplikacije se širi
između klijenta i servera. Ponekad to znači da se logika duplira (kao kod validacije podataka



obrasca). U drugim slučajevima, klijent i server implementiraju nepovezane delove ukupne
logike aplikacije. Da bi razumeo šta aplikacija radi, programer mora da prati interakcije između
dve veoma različite baze koda.

Postoji još jedna mana koja više utiče na mobilne aplikacije nego na SPA: odliv API-ja.
Zapamtite, prodavnice aplikacija kontrolišu kako se vaša aplikacija distribuira i ažurira.
Korisnici čak mogu da kontrolišu da li i kada dobijaju ažurirane verzije vaše aplikacije. Kao
programer mobilnih aplikacija, ne možete pretpostaviti da će svaki korisnik biti na najnovijoj
verziji vaše aplikacije. Vaš frontend kod se fragmentira na mnoge verzije, a sada vaš bekend
mora da ih sve podržava.

Videli smo da hipermedijalna arhitektura može da reši nedostatke SPA na vebu. Ali da li
hipermedija može da funkcioniše i za mobilne aplikacije? Odgovor je da!

Baš kao i na vebu, možemo koristiti hipermedijske formate na mobilnim uređajima i dozvoliti im
da služe kao motor stanja aplikacije. Sva logika se kontroliše iz bekenda, umesto da se
raspoređuje između dve baze koda. Hipermedijska arhitektura takođe rešava dosadan problem
prenamene API-ja na mobilnim aplikacijama. Pošto bekend služi hipermedijski odgovor koji
sadrži i podatke i akcije, ne postoji način da podaci i korisnički interfejs budu nesinhronizovani.
Nema više brige o kompatibilnosti sa starijim verzijama ili održavanju više verzija API-ja.

Pa kako možete koristiti hipermediju za svoju mobilnu aplikaciju? Danas postoje dva pristupa
korišćenja hipermedije za kreiranje i isporuku nativnih mobilnih aplikacija:

- Veb prikazi, koji obavijaju pouzdanu veb platformu u ljusku mobilne aplikacije

- Hipervju, novi hipermedijalni sistem koji smo dizajnirali posebno za mobilne aplikacije

Veb pregledi

Najjednostavniji način za korišćenje hipermedijske arhitekture na mobilnim uređajima jeste
korišćenje veb tehnologija. I Android i iOS SDK-ovi pružaju "veb prikaze": veb pregledače bez
hroma koji se mogu ugraditi u izvorne aplikacije. Alati poput Apache Cordova olakšavaju
uzimanje URL-a veb stranice i kreiranje izvornih iOS i Android aplikacija na osnovu veb prikaza.
Ako već imate responzivnu veb aplikaciju, možete besplatno dobiti "nativnu" mobilnu HDA.
Zvuči previše dobro da bi bilo istinito, zar ne?

Naravno, postoji fundamentalno ograničenje kod ovog pristupa. Veb platforma i mobilne
platforme imaju različite mogućnosti i UX konvencije. HTML ne podržava izvorno uobičajene UI
obrasce mobilnih aplikacija. Jedna od najvećih razlika je u načinu na koji svaka platforma rukuje
navigacijom. Na vebu, navigacija je zasnovana na stranicama, pri čemu jedna stranica zamenjuje
drugu, a pregledač pruža dugmad za kretanje napred/nazad za navigaciju kroz istoriju stranica.
Na mobilnim uređajima, navigacija je složenija i podešena za fizički aspekt interakcija
zasnovanih na gestovima.

- Da bi se detaljnije analiziralo, ekrani se pomeraju jedan preko drugog, formirajući gomile
ekrana.

- Trake sa karticama na vrhu ili dnu aplikacije omogućavaju prebacivanje između različitih
grupa ekrana.

- Modalni prozori se pomeraju nagore od dna aplikacije, prekrivajući ostale stekove i traku
sa karticama.



- Za razliku od veb stranica, svi ovi ekrani su i dalje prisutni u memoriji, prikazuju se i
ažuriraju na osnovu stanja aplikacije.

Arhitektura navigacije je glavna razlika između funkcionisanja mobilnih i veb aplikacija. Ali nije
jedina. Mnogi drugi UX obrasci su prisutni u mobilnim aplikacijama, ali nisu izvorno podržani
na vebu:

- osvežavanje sadržaja na ekranu pomoću funkcije "povuci za osvežavanje"

- horizontalno prevlačenje preko elemenata korisničkog interfejsa da bi se otkrile radnje

- sekcijske liste sa lepljivim zaglavljima

Iako ove interakcije nisu izvorno podržane od strane veb pregledača, mogu se simulirati pomoću
JS biblioteka. Naravno, ove biblioteke nikada neće imati isti osećaj i performanse kao izvorni
gestovi. A njihovo korišćenje obično zahteva usvajanje JS-om pretežno zasnovane SPA
arhitekture kao što je React. Ovo nas vraća na početak! Da bismo izbegli korišćenje tipične
arhitekture debelog klijenta izvornih mobilnih aplikacija, okrenuli smo se veb prikazu. Veb
prikaz nam omogućava da koristimo dobri stari HTML zasnovan na hipermediji. Ali da bismo
dobili željeni izgled i osećaj mobilne aplikacije, na kraju gradimo SPA u JS-u, gubeći prednosti
hipermedije u tom procesu.

Da bismo napravili mobilnu HDA koja funkcioniše i deluje kao nativna aplikacija, HTML neće
biti dovoljan. Potreban nam je format dizajniran da predstavi interakcije i obrasce nativnih
mobilnih aplikacija. To je upravo ono što Hyperview radi.

Hiperpogled

Hipervju je hipermedijalni sistem otvorenog koda koji pruža:

- Hipermedijalni format za definisanje mobilnih aplikacija nazvan HXML

- Hipermedijalni klijent za HXML koji radi na iOS-u i Androidu

- Tačke proširenja u HXML-u i klijentu za prilagođavanje okvira za datu aplikaciju

Format

HXML je dizajniran tako da bude poznat veb programerima, naviklim na rad sa HTML-om.
Otuda izbor XML-a za osnovni format. Pored poznate ergonomije, XML je kompatibilan sa
bibliotekama za renderovanje na strani servera. Na primer, Jinja2 je savršeno pogodan kao
biblioteka šablona za renderovanje HXML-a. Poznatost XML-a i lakoća integracije na bekend-u
olakšavaju njegovo usvajanje i u novim i u postojećim bazama koda. Pogledajte aplikaciju
"Zdravo svete" napisanu u HXML-u. Sintaksa bi trebalo da bude poznata svima koji su radili sa
HTML-om:

<doc xmlns="https://hyperview.org/hyperview">
  <screen>
    <styles />
    <body>
      <header>
        <text>My first app</text>
      </header>
      <view>
        <text>Hello World!</text>
      </view>
    </body>



  </screen>
</doc>

Zdravo svete

Ali HXML nije samo direktna verzija HTML-a sa drugačije imenovanim oznakama. U
prethodnim poglavljima smo videli kako htmx poboljšava HTML sa nekoliko novih atributa. Ovi
dodaci održavaju deklarativnu prirodu HTML-a, dok programerima daju moć da kreiraju bogate
veb aplikacije. U HXML-u, koncepti htmx-a su ugrađeni u specifikaciju. Konkretno, HXML nije
ograničen na interakcije "klikni na link" i "pošalji obrazac" kao osnovni HTML. Podržava niz
okidača i akcija za modifikovanje sadržaja na ekranu. Ove interakcije su objedinjene u moćan
koncept "ponašanja". Programeri čak mogu definisati nove akcije ponašanja kako bi dodali nove
mogućnosti svojoj aplikaciji, bez potrebe za skriptovanjem. Više o ponašanjima ćemo saznati
kasnije u ovom poglavlju.

Klijent

Hyperview pruža HXML klijentsku biblioteku otvorenog koda napisanu u React Native-u. Uz
malo konfiguracije i nekoliko koraka u komandnoj liniji, ova biblioteka se kompajlira u nativne
binarne datoteke aplikacija za iOS ili Android. Korisnici instaliraju aplikaciju na svoj uređaj
putem prodavnice aplikacija. Prilikom pokretanja, aplikacija upućuje HTTP zahtev
konfigurisanom URL-u i prikazuje HXML odgovor kao prvi ekran.

Možda deluje pomalo čudno da razvoj HDA pomoću Hyperview-a zahteva jednonamenski
klijentski binarni fajl. Na kraju krajeva, ne tražimo od korisnika da prvo preuzmu i instaliraju
binarni fajl da bi videli veb aplikaciju. Ne, korisnici samo unose URL adresu u adresnu traku
opštenamenskog veb pregledača. Jedan HTML klijent prikazuje aplikacije sa bilo kog HTML
servera ( [fig-1clientmanyserver] ).

                  ┌────────────┐
                  │            │
┌──────────┬─┐    │   SERVER   │
├──────────┴─┤    │            │
│            │    └──▲─────────┘
│            │       │
│            │       │  
│            ├───────┘  ┌────────────┐
│            │          │            │
│   CLIENT   ├──────────▶   SERVER   │
│            │          │            │
│            ├─────┐    └────────────┘
│            │     │
│            │   ┌────────────┐
│            │   │            │
└────────────┘   │   SERVER   │
                 │            │
                 └────────────┘

Jedan HTML klijent, više HTML servera

Teoretski je moguće napraviti ekvivalentan opšti "Hyperview pregledač". Ovaj HXML klijent bi
prikazivao aplikacije sa bilo kog HXML servera, a korisnici bi unosili URL adresu da bi naveli
aplikaciju koju žele da koriste. Ali iOS i Android su izgrađeni oko koncepta aplikacija sa jednom
namenom. Korisnici očekuju da pronađu i instaliraju aplikacije iz prodavnice aplikacija i da ih
pokreću sa početnog ekrana svog uređaja. Hyperview prihvata ovu paradigmu usmerenu na
aplikacije današnjih popularnih mobilnih platformi. To znači da HXML klijent (binarni fajl
aplikacije) prikazuje svoj korisnički interfejs sa jednog unapred konfigurisanog HXML servera (
[fig-1client1server] ).



┌────────────┐
│            │       ┌────────────┐
│            │       │┌──────────┐│
│   SERVER   │       ││          ││
│            │       ││┌───┐┌───┐││
│            ◀─────────┤   ││   │││
│            │       ││└───┘└───┘││
│            │       ││ App  App ││
│            │       ││          ││
│            │       ││┌───┐┌───┐││
│            │       │└┴───┴┴───┴┘│
│            │       │   CLIENT   │
│            │       └────────────┘
└────────────┘

Jedan HXML klijent, jedan HXML server

Srećom, programeri ne moraju da pišu HXML klijent od nule; klijentska biblioteka otvorenog
koda obavlja 99% posla. I kao što ćemo videti u sledećem odeljku, postoje velike prednosti
kontrole i klijenta i servera u HDA.

Proširivost

Da bismo razumeli prednosti arhitekture Hyperview-a, prvo moramo da razgovaramo o
nedostacima veb arhitekture. Na vebu, svaki veb pregledač može da prikaže HTML sa bilo kog
veb servera. Ovaj nivo kompatibilnosti može se postići samo sa dobro definisanim standardima
kao što je HTML5. Ali definisanje i razvijanje standarda je naporan proces. Na primer, W3C-u je
trebalo preko 7 godina da pređe put od prvog nacrta do preporuke za HTML5 specifikaciju. To
nije iznenađujuće, s obzirom na nivo promišljenosti koji je potreban za promenu koja utiče na
toliko ljudi. Ali to znači da se napredak dešava sporo. Kao veb programer, možda ćete morati da
čekate godinama da pregledači steknu široku podršku za funkciju koja vam je potrebna.

Dakle, koje su prednosti Hyperview arhitekture? U Hyperview aplikaciji, vaša mobilna aplikacija
prikazuje HXML samo sa vašeg servera. Ne morate da brinete o kompatibilnosti između vašeg
servera i drugih mobilnih aplikacija, ili između vaše mobilne aplikacije i drugih servera. Ne
postoji telo za standardizaciju koje biste konsultovali. Ako želite da dodate funkciju treptanja
svojoj mobilnoj aplikaciji, samo napred i implementirajte element <blink>u klijentu i počnite da
vraćate <blink>elemente u HXML odgovorima sa vašeg servera. U stvari, Hyperview klijentska
biblioteka je napravljena imajući u vidu ovu vrstu proširivosti. Postoje tačke proširenja za
prilagođene elemente korisničkog interfejsa i prilagođene akcije ponašanja. Očekujemo i
podstičemo programere da koriste ova proširenja kako bi HXML učinili izražajnijim i
prilagođenijim funkcionalnosti njihove aplikacije.

A proširivanjem HXML formata i samog klijenta, nema potrebe da Hyperview uključuje sloj
skriptovanja u HXML. Funkcije koje zahtevaju logiku na strani klijenta se "ugrađuju" u binarni
fajl klijenta. HXML odgovori ostaju čisti, sa korisničkim interfejsom i interakcijama
predstavljenim u deklarativnom XML-u.

Koju hipermedijsku arhitekturu treba da koristite?

Razgovarali smo o dva pristupa za kreiranje mobilnih aplikacija korišćenjem hipermedijalnih
sistema:

- kreirajte bekend koji vraća HTML i prikazuje ga u mobilnoj aplikaciji putem veb prikaza

- kreirajte bekend koji vraća HXML i servirajte ga u mobilnoj aplikaciji pomoću Hyperview
klijenta



Namerno sam opisao ova dva pristupa na način koji istakne njihove sličnosti. Na kraju krajeva,
oba su zasnovana na hipermedijalnim sistemima, samo sa različitim formatima i klijentima. Oba
pristupa rešavaju fundamentalne probleme tradicionalnog razvoja mobilnih aplikacija sličnih
SPA:

- Bekend kontroliše kompletno stanje aplikacije.

- Logika naše aplikacije je na jednom mestu.

- Aplikacija uvek koristi najnoviju verziju, nema problema sa API-jem.

Dakle, koji pristup bi trebalo da koristite za mobilnu HDA? Na osnovu našeg iskustva u izradi
obe vrste aplikacija, verujemo da Hyperview pristup rezultira boljim korisničkim iskustvom.
Veb-prikaz će uvek delovati neumesno na iOS-u i Android-u; jednostavno ne postoji dobar način
da se repliciraju obrasci navigacije i interakcije koje mobilni korisnici očekuju. Hyperview je
kreiran posebno da bi se rešio problem ograničenja pristupa sa debelim klijentom i veb-
prikazom. Nakon početnog ulaganja u učenje Hyperview-a, dobićete sve prednosti Hypermedia
arhitekture, bez nedostataka degradiranog korisničkog iskustva.

Naravno, ako već imate jednostavnu veb aplikaciju prilagođenu mobilnim uređajima, onda je
korišćenje veb-prikaza razumno. Sigurno ćete uštedeti vreme jer nećete morati da prikazujete
svoju aplikaciju kao HXML pored HTML-a. Ali, kao što ćemo pokazati na kraju ovog poglavlja,
ne zahteva mnogo posla da se postojeća veb aplikacija vođena Hypermedia-om konvertuje u
Hyperview mobilnu aplikaciju. Ali pre nego što stignemo do toga, potrebno je da uvedemo
koncepte elemenata i ponašanja u Hyperview-u. Zatim ćemo ponovo izgraditi našu aplikaciju za
kontakte u Hyperview-u.

Kada ne bi trebalo da koristite hipermediju za izradu mobilne aplikacije?
Hipermedija nije uvek pravi izbor za izradu mobilne aplikacije. Baš kao i na vebu, aplikacije koje
zahtevaju veoma dinamične korisničke interfejse (kao što je aplikacija za tabelarne proračune)
bolje se implementiraju sa kodom na strani klijenta. Pored toga, neke aplikacije moraju da
funkcionišu dok su potpuno oflajn. Pošto HDA zahtevaju server za prikazivanje korisničkog
interfejsa, mobilne aplikacije koje su prvenstveno oflajn nisu dobar izbor za ovu arhitekturu.
Međutim, baš kao i na vebu, programeri mogu da koriste hibridni pristup za izradu svoje
mobilne aplikacije. Veoma dinamični ekrani mogu se izgraditi sa složenom logikom na strani
klijenta, dok se manje dinamični ekrani mogu izgraditi pomoću veb prikaza ili hiperpregleda. Na
ovaj način, programeri mogu da potroše svoj budžet za složenost na jezgro aplikacije i da
jednostavni ekrani ostanu jednostavni.

Uvod u HXML

Zdravo svete!

HXML je dizajniran tako da veb programerima koji dolaze sa HTML-a deluje prirodno. Hajde da
detaljnije pogledamo aplikaciju "Zdravo svete" definisanu u HXML-u:

<doc xmlns="https://hyperview.org/hyperview"> <1>
  <screen> <2>
    <styles />
    <body> <3>
      <header> <4>
        <text>My first app</text>
      </header>
      <view> <5>
        <text>Hello World!</text> <6>
      </view>



    </body>
  </screen>
</doc>

Zdravo svete, ponovo posećen

1. Korenski element HXML aplikacije

2. Element koji predstavlja ekran aplikacije

3. Element koji predstavlja korisnički interfejs ekrana

4. Element koji predstavlja gornji zaglavlje ekrana

5. Omotački element oko sadržaja prikazanog na ekranu

6. Tekstualni sadržaj prikazan na ekranu

Ništa previše čudno ovde, zar ne? Baš kao i HTML, sintaksa definiše stablo elemenata koristeći
početne oznake ( <screen>) i završne oznake ( </screen>). Elementi mogu da sadrže druge
elemente ( <view>) ili tekst ( Hello World!). Elementi takođe mogu biti prazni, predstavljeni
praznom oznakom ( <styles />). Međutim, primetićete da se imena HXML elementa razlikuju
od onih u HTML-u. Hajde da detaljnije pogledamo svaki od tih elemenata da bismo razumeli šta
rade.

<doc>je koren HXML aplikacije. Zamislite to kao ekvivalent <html>elementa u HTML-u. Imajte na
umu da <doc>element sadrži atribut xmlns="https://hyperview.org/hyperview". Ovo definiše
podrazumevani imenski prostor za dokument. Imenski prostori su karakteristika XML-a koja
omogućava da jedan dokument sadrži elemente koje su definisali različiti programeri. Da bi se
sprečili sukobi kada dva programera koriste isto ime za svoj element, svaki programer definiše
jedinstveni imenski prostor. Više ćemo govoriti o imenskim prostorima kada kasnije u ovom
poglavlju budemo razgovarali o prilagođenim elementima i ponašanjima. Za sada je dovoljno
znati da se elementi u HXML dokumentu bez eksplicitnog imenskog prostora smatraju delom
imenskog https://hyperview.org/hyperviewprostora.

<screen>predstavlja korisnički interfejs koji se prikazuje na jednom ekranu mobilne aplikacije.
Moguće je da jedan element <doc>sadrži više <screen>elemenata, ali sada nećemo ulaziti u to.
Tipično, <screen>element će sadržati elemente koji definišu sadržaj i stil ekrana.

<styles>definiše stilove korisničkog interfejsa na ekranu. U ovom poglavlju nećemo se previše
baviti stilizovanjem u Hyperview-u. Dovoljno je reći da, za razliku od HTML-a, Hyperview ne
koristi poseban jezik (CSS) za definisanje stilova. Umesto toga, pravila stilizovanja kao što su
boje, razmak, raspored i fontovi definišu se u HXML-u. Na ova pravila se zatim eksplicitno
pozivaju elementi korisničkog interfejsa, slično kao što se koriste klase u CSS-u.

<body>definiše stvarni korisnički interfejs ekrana. Telo sadrži sav tekst, slike, dugmad, obrasce
itd. koji će biti prikazani korisniku. Ovo je ekvivalentno elementu <body>u HTML-u.

<header>definiše zaglavlje ekrana. Tipično u mobilnim aplikacijama, zaglavlje uključuje
navigaciju (kao što je dugme za nazad) i naslov ekrana. Korisno je definisati zaglavlje odvojeno
od ostatka tela. Neki mobilni operativni sistemi će koristiti drugačiji prelaz za zaglavlje nego za
ostatak sadržaja ekrana.

<view>je osnovni gradivni blok za rasporede i strukturu unutar tela ekrana. Zamislite to kao
<div>u HTML-u. Imajte na umu da za razliku od HTML <div>elemenata, <view>ne može direktno
da sadrži tekst.



<text>Elementi su jedini način za prikazivanje teksta u korisničkom interfejsu. U ovom primeru,
"Zdravo svete" se nalazi unutar <text>elementa.

To je sve što je potrebno da se definiše osnovna aplikacija "Zdravo svete" u HXML-u. Naravno,
ovo nije baš uzbudljivo. Hajde da pokrijemo neke druge ugrađene elemente prikaza.

Elementi korisničkog interfejsa

Liste

Veoma uobičajen obrazac u mobilnim aplikacijama je listanje kroz listu stavki. Fizička svojstva
ekrana telefona (uzdužni i vertikalni) i intuitivni gest prevlačenja palcem gore i dole čine ovo
dobrim izborom za mnoge ekrane.

HXML ima namenske elemente za predstavljanje lista i stavki.

<list> <1>
  <item key="item1"> <2>
    <text>My first item</text> <3>
  </item>
  <item key="item2">
    <text>My second item</text>
  </item>
</list>

Element liste

1. Element koji predstavlja listu

2. Element koji predstavlja stavku na listi, sa jedinstvenim ključem

3. Sadržaj stavke na listi.

Liste su predstavljene sa dva nova elementa. obuhvata <list>sve stavke na listi. Može se
stilizovati kao generički element <view>(širina, visina, itd.). <list>Element sadrži samo
<item>elemente. Naravno, oni predstavljaju svaku jedinstvenu stavku na listi. Imajte na umu da
<item>je potrebno imati keyatribut koji je jedinstven među svim stavkama na listi.

Možda se pitate: "Zašto nam je potrebna prilagođena sintaksa za liste stavki? Zar ne možemo
jednostavno da koristimo gomilu <view>elemenata?". Da, za liste sa malim brojem stavki,
korišćenje ugnežđenih elemenata <views>će funkcionisati prilično dobro. Međutim, često broj
stavki na listi može biti dovoljno dugačak da zahteva optimizacije kako bi se podržale glatke
interakcije skrolovanja. Razmislite o pregledanju fida objava u aplikaciji društvenih medija. Dok
stalno skrolujete kroz fid, nije neuobičajeno da aplikacija prikazuje stotine, ako ne i hiljade
objava. U bilo kom trenutku možete da pomerite prst da biste se pomerili do skoro bilo kog dela
fida. Mobilni uređaji imaju tendenciju da budu ograničeni memorijom. Čuvanje potpuno
prikazane liste stavki u memoriji može da potroši više resursa nego što je dostupno. Zato i iOS i
Android pružaju API-je za optimizovane korisničke interfejse lista. Ovi API-ji znaju koji deo liste
je trenutno na ekranu. Da bi uštedeli memoriju, oni brišu nevidljive stavke liste i recikliraju
objekte korisničkog interfejsa stavki da bi uštedeli memoriju. Korišćenjem eksplicitnih
elemenata ` <list>and` <item>u HXML-u, Hyperview klijent zna da koristi ove optimizovane
API-je lista kako bi vaša aplikacija bila efikasnija.

Takođe je vredno pomenuti da HXML podržava liste odeljaka. Liste odeljaka su korisne za
kreiranje korisničkih interfejsa zasnovanih na listama, gde se stavke na listi mogu grupisati radi
lakšeg korišćenja. Na primer, korisnički interfejs koji prikazuje meni restorana mogao bi



grupisati ponude po tipu jela:

<section-list> <1>
  <section> <2>
    <section-title> <3>
      <text>Appetizers</text>
    </section-title>
    <item key="1"> <4>
      <text>French Fries</text>
    </item>
    <item key="2">
      <text>Onion Rings</text>
    </item>
  </section>

  <section> <5>
    <section-title>
      <text>Entrees</text>
    </section-title>
    <item key="3">
      <text>Burger</text>
    </item>
  </section>
</section-list>

Element liste odeljaka

1. Element koji predstavlja listu sa odeljcima

2. Prvi deo ponude predjela

3. Element za naslov odeljka, koji prikazuje tekst "Predelo"

4. Predmet koji predstavlja predjelo

5. Odeljak za ponudu glavnih jela

Primetićete nekoliko razlika između <list>i <section-list>. Element liste odeljka sadrži samo
<section>elemente, koji predstavljaju grupu stavki. Odeljak može da sadrži <section-
title>element. Ovo se koristi za prikazivanje korisničkog interfejsa koji deluje kao zaglavlje
odeljka. Ovo zaglavlje je "lepljivo", što znači da ostaje na ekranu dok se skroluje kroz stavke koje
pripadaju odgovarajućem odeljku. Konačno, <item>elementi se ponašaju isto kao u redovnoj listi,
ali se mogu pojaviti samo unutar <section>.

Slike

Prikazivanje slika u Hyperview-u je prilično slično HTML-u, ali postoji nekoliko razlika.

<image source="/profiles/1.jpg" style="avatar" />

Element slike

Atribut sourceodređuje kako se učitava slika. Kao u HTML-u, izvor može biti apsolutni ili
relativni URL. Pored toga, izvor može biti URI kodiranih podataka, na primer data:image/
png;base64,iVBORw. Međutim, izvor može biti i "lokalni" URL, koji se odnosi na sliku koja je u
paketu kao element u mobilnoj aplikaciji. Lokalni URL ima prefiks./:

<image source="./logo.png" style="logo" />

Element slike, koji ukazuje na lokalni izvor



Korišćenje lokalnih URL-ova je optimizacija. Pošto se slike nalaze na mobilnom uređaju, ne
zahtevaju mrežni zahtev i brzo će se pojaviti. Međutim, povezivanje slike sa binarnom datotekom
mobilne aplikacije povećava veličinu binarne datoteke. Korišćenje lokalnih slika je dobar
kompromis za slike kojima se često pristupa, ali se retko menjaju. Dobri primeri uključuju
logotip aplikacije ili uobičajene ikone dugmadi.

Druga stvar koju treba napomenuti je prisustvo atributa stylena <image>elementu. U HXML-u,
slike moraju imati stil koji ima pravila za slike widthi height. Ovo se razlikuje od HTML-a, gde
<img>elementi ne moraju eksplicitno da podešavaju širinu i visinu. Veb pregledači će preurediti
sadržaj veb stranice kada se slika preuzme i dimenzije su poznate. Iako je preuređenje sadržaja
razumno ponašanje za veb dokumente, korisnici ne očekuju da će mobilne aplikacije preurediti
raspored dok se sadržaj učitava. Da bi se održao statički raspored, HXML zahteva da dimenzije
budu poznate pre nego što se slika učita.

Ulazi

Mnogo toga se može pokriti o unosima u Hyperview-u. Pošto je ovo zamišljeno kao uvod, a ne
kao iscrpan resurs, istaći ću samo nekoliko vrsta unosa. Počnimo sa primerom najjednostavnije
vrste unosa, tekstualnog polja.

<text-field
name="first_name" <1>

  style="input" <2>
  value="Adam" <3>
  placeholder="First name" <4>
/>

Element tekstualnog polja

1. Ime koje se koristi prilikom serijalizacije podataka sa ovog ulaza

2. Stil primenjen na element korisničkog interfejsa

3. Trenutna vrednost podešena u polju

4. Prikazni znak koji se prikazuje kada je vrednost prazna

Ovaj element bi trebalo da bude poznat svakome ko je kreirao tekstualno polje u HTML-u. Jedna
razlika je u tome što većina unosa u HTML-u koristi element <input>sa typeatributom, npr <input
type="text">. U Hyperview-u, svaki unos ima jedinstveno ime, u ovom slučaju <text-field>.
Korišćenjem različitih imena, možemo koristiti izražajniji XML za predstavljanje unosa.

Na primer, razmotrimo slučaj gde želimo da prikažemo korisnički interfejs koji omogućava
korisniku da izabere jednu od nekoliko opcija. U HTML-u bismo koristili radio dugme za unos,
nešto poput <input type="radio" name="choice" value="option1" />. Svaki izbor je predstavljen
kao jedinstveni element unosa. Ovo mi se nikada nije činilo idealnim. Većinu vremena, radio
dugmad su grupisana zajedno da bi uticala na isto ime. HTML pristup dovodi do mnogo šablona
(dupliranje type="radio"i name="choice"za svaki izbor). Takođe, za razliku od radio dugmadi na
desktop računarima, mobilni operativni sistemi ne pružaju jak standardni korisnički interfejs za
izbor jedne opcije. Većina mobilnih aplikacija koristi bogatije, prilagođene korisničke interfejse
za ove interakcije. Dakle, u HXML-u implementiramo ovaj korisnički interfejs koristeći element
pod nazivom <select-single>:

<select-single name="choice"> <1>
  <option value="option1"> <2>
    <text>Option 1</text> <3>
  </option>



  <option value="option2">
    <text>Option 2</text>
  </option>
</select-single>

Izaberite jedan element

1. Element koji predstavlja unos gde je izabran jedan izbor. Naziv izbora je ovde definisan
jednom.

2. Element koji predstavlja jedan od izbora. Vrednost izbora je ovde definisana.

3. Korisnički interfejs selekcije. U ovom primeru koristimo tekst, ali možemo koristiti bilo
koje elemente korisničkog interfejsa.

Element <select-single>je roditelj ulaza za izbor jedne opcije od mnogih. Ovaj element sadrži
nameatribut koji se koristi prilikom serijalizacije izabrane opcije. <option>Elementi unutar
<select-single>predstavljaju dostupne opcije. Imajte na umu da svaki <option>element ima
valueatribut. Kada se pritisne, ovo će biti izabrana vrednost ulaza. Element <option>može da
sadrži bilo koje druge elemente korisničkog interfejsa unutar sebe. To znači da nismo ograničeni
prikazivanjem ulaza kao liste radio dugmadi sa oznakama. Možemo da prikažemo opcije kao
radio dugmad, oznake, slike ili bilo šta drugo što bi bilo intuitivno za naš interfejs. HXML
stilizovanje podržava modifikatore za pritisnuta i izabrana stanja, omogućavajući nam da
prilagodimo korisnički interfejs kako bismo istakli izabranu opciju.

Opisivanje svih karakteristika ulaza u HXML-u bi zauzelo celo poglavlje. Umesto toga, sumiraću
nekoliko drugih ulaznih elemenata i njihove karakteristike.

- <select-multiple>radi kao <select-single>, ali podržava uključivanje i isključivanje više opcija.
Ovo zamenjuje unose polja za potvrdu u HTML-u. - Element <switch>prikazuje prekidač za
uključivanje/isključivanje koji je uobičajen u mobilnim korisničkim interfejsima - Element
<date-field>podržava unos određenih datuma i dolazi sa širokim spektrom prilagođavanja za
formatiranje, raspone podešavanja itd.

Još dve stvari koje treba pomenuti o ulazima. Prva je <form>element. <form>Element se koristi za
grupisanje ulaza radi serijalizacije. Kada korisnik preduzme akciju koja pokreće bekend zahtev,
Hyperview klijent će serijalizovati sve ulaze u okruženju <form>i uključiti ih u zahtev. Ovo važi i
za i GETza POSTzahteve. Ovo ćemo detaljnije obraditi kada budemo govorili o ponašanjima kasnije
u ovom poglavlju. Takođe, kasnije u ovom poglavlju, govoriću o podršci za prilagođene elemente
u HXML-u. Sa prilagođenim elementima, možete kreirati i sopstvene ulazne elemente.
Prilagođeni ulazni elementi vam omogućavaju da izgradite neverovatno moćne interakcije sa
jednostavnom XML sintaksom koja se dobro integriše sa ostatkom HXML-a.

Stilizovanje

Do sada nismo pomenuli kako primeniti stilizovanje na sve HXML elemente. Videli smo iz
aplikacije Hello World da svaki <screen>može da sadrži <styles>element. Hajde da ponovo
posetimo aplikaciju Hello World i popunimo <styles>element.

<doc xmlns="https://hyperview.org/hyperview">
  <screen>
    <styles> <1>
      <style id="body" flex="1" flexDirection="column" /> <2>
      <style id="header"

borderBottomWidth="1" borderBottomColor="#ccc" />
      <style id="main" margin="24" />
      <style id="h1" fontSize="32" />



      <style id="info" color="blue" />
    </styles>

    <body style="body"> <3>
      <header style="header">
        <text style="info">My first app</text>
      </header>
      <view style="main">
        <text style="h1 info">Hello World!</text> <4>
      </view>
    </body>
  </screen>
</doc>

Primer stilizovanja korisničkog interfejsa

1. Element koji obuhvata sav stil za ekran

2. Primer definicije stila za "telo"

3. Primena stila "telo" na element korisničkog interfejsa

4. Primer primene više stilova (h1 i info) na element

Primetićete da je u HXML-u stilizovanje deo XML formata, umesto da se koristi poseban jezik
poput CSS-a. Međutim, možemo povući neke paralele između CSS pravila i <style>elementa.
CSS pravilo se sastoji od selektora i deklaracija. U trenutnoj verziji HXML-a, jedini dostupni
selektor je ime, označeno atributom id. Ostali atributi elementa <style>su deklaracije, koje se
sastoje od svojstava i vrednosti svojstava.

Elementi korisničkog interfejsa unutar <screen>mogu da referenciraju <style>pravila
dodavanjem imena stilova svom styleatributu. Obratite pažnju na <text>element oko "Zdravo
svete!" koji referencira dva stila: h1i info. Odgovarajući stilovi se spajaju redosledom kojim se
pojavljuju na elementu. Vredi napomenuti da su svojstva stilizovanja slična onima u CSS-u
(boja, margine/podmetanje, okviri itd.). Trenutno je jedini dostupni mehanizam za raspored
zasnovan na fleksboksu.

Pravila stila mogu biti prilično opširna. Radi kratkoće, nećemo uključivati <styles>element u
ostale primere u ovom poglavlju osim ako nije neophodno.

Prilagođeni elementi

Osnovni elementi korisničkog interfejsa koji se isporučuju sa Hyperview-om su prilično osnovni.
Većina mobilnih aplikacija zahteva bogatije elemente da bi pružila odlično korisničko iskustvo.
Srećom, HXML može lako da primi prilagođene elemente u svojoj sintaksi. To je zato što je
HXML zapravo samo XML, poznat kao "proširivi jezik za označavanje". Proširivost je već
ugrađena u format! Programeri mogu slobodno da definišu nove elemente i atribute koji
predstavljaju prilagođene elemente.

Pogledajmo ovo u akciji na konkretnom primeru. Pretpostavimo da želimo da dodamo element
mape našoj aplikaciji "Hello World". Želimo da mapa prikazuje definisanu oblast i jedan ili više
markera na određenim koordinatama u toj oblasti. Hajde da prevedemo ove zahteve u XML:

- Element <area>će predstavljati područje prikazano na mapi. Da bi se odredilo područje,
element će sadržati atribute za latitudei longitudeza centar područja, i atribute za latitude-
deltai longitude-deltakoji označavaju +/- područje prikaza oko centra.

- Element <marker>će predstavljati marker u oblasti. Koordinate markera će biti definisane



pomoću latitudei longitudeatributa na markeru.

Koristeći ove prilagođene XML elemente, instanca mape u našoj aplikaciji može izgledati ovako:

<doc xmlns="https://hyperview.org/hyperview">
  <screen>
    <body>
      <view>
        <text>Hello World!</text>
        <area latitude="37.8270" longitude="122.4230"

latitude-delta="0.1" longitude-delta="0.1"> <1>
          <marker latitude="37.8118" longitude="-122.4177" /> <2>
        </area>
      </view>
    </body>
  </screen>
</doc>

Prilagođeni elementi u HXML-u

1. Prilagođeni element koji predstavlja područje koje prikazuje mapa

2. Prilagođeni element koji predstavlja marker prikazan na određenim koordinatama na
mapi

Sintaksa se savršeno uklapa među osnovne HXML elemente. Međutim, postoji potencijalni
problem. "Područje" i "marker" su prilično generički nazivi. Mogao bih da vidim elemente
<area>i <marker>kako ih koristi prilagođavanje za prikazivanje dijagrama i grafikona. Ako naša
aplikacija prikazuje i mape i grafikone, HXML oznake bi bile dvosmislene. Šta bi klijent trebalo
da prikaže kada vidi <area>ili <marker>?

Tu dolaze do izražaja XML imenski prostori. XML imenski prostori eliminišu dvosmislenost i
kolizije između elemenata i atributa koji se koriste za predstavljanje različitih stvari. Zapamtite
da <doc>element deklariše da https://hyperview.org/hyperviewje to podrazumevani imenski
prostor za ceo dokument. Pošto nijedan drugi element ne definiše imenske prostore, svaki
element u gornjem primeru je deo imenskog https://hyperview.org/hyperviewprostora.

Hajde da definišemo novi imenski prostor za naše elemente mape. Pošto ovaj imenski prostor
neće biti podrazumevani za dokument, takođe moramo dodeliti imenski prostor prefiksu koji
ćemo dodati našim elementima:

<doc xmlns="https://hyperview.org/hyperview"
xmlns:map="https://mycompany.com/hyperview-map">

Ovaj novi atribut deklariše da map:je prefiks povezan sa imenskim prostorom "https://
mycompany.com/hyperview-map". Ovaj imenski prostor može biti bilo šta, ali zapamtite da je
cilj koristiti nešto jedinstveno što neće imati kolizije. Korišćenje domena vaše kompanije/
aplikacije je dobar način da se garantuje jedinstvenost. Sada kada imamo imenski prostor i
prefiks, potrebno je da ga koristimo za naše elemente:

<doc xmlns="https://hyperview.org/hyperview"
xmlns:map="https://mycompany.com/hyperview-map"> <1>

  <screen>
    <body>
      <view>
        <text>Hello World!</text>
        <map:area latitude="37.8270" longitude="122.4230"

latitude-delta="0.1" longitude=delta="0.1"> <2>
          <map:marker latitude="37.8118" longitude="-122.4177" /> <3>
        </map:area> <4>
      </view>



    </body>
  </screen>
</doc>

Imenski prostori prilagođenih elemenata

1. Definicija imenskog prostora sa pseudonimom "map"

2. Dodavanje imenskog prostora početnoj oznaci "area"

3. Dodavanje imenskog prostora samozatvarajućoj oznaci "marker"

4. Dodavanje imenskog prostora završnoj oznaci "area"

To je to! Ako bismo uveli prilagođenu biblioteku za crtanje grafikona sa elementima "area" i
"marker", kreirali bismo jedinstveni imenski prostor i za te elemente. U HXML dokumentaciji,
mogli bismo lako da <map:area>razlikujemo <chart:area>.

U ovom trenutku se možda pitate: "Kako Hyperview klijent zna da prikaže mapu kada moj
dokument sadrži <map:area>?" Tačno je, do sada smo definisali samo format prilagođenog
elementa, ali nismo implementirali element kao funkciju u našoj aplikaciji. Detaljnije ćemo se
baviti implementacijom prilagođenih elemenata u sledećem poglavlju.

Ponašanja

Kao što je već rečeno u prethodnim poglavljima, HTML podržava dva osnovna tipa interakcija:

- Klikom na hiperlink: klijent će poslati GET zahtev i prikazati odgovor kao novu veb
stranicu.

- Slanje formulara: klijent će (obično) napraviti POST zahtev sa serijalizovanim sadržajem
formulara i prikazati odgovor kao novu veb stranicu.

Klik na hiperlinkove i slanje obrazaca je dovoljno za kreiranje jednostavnih veb aplikacija. Ali
oslanjanje samo na ove dve interakcije ograničava našu mogućnost kreiranja bogatijih
korisničkih interfejsa. Šta ako želimo da se nešto desi kada korisnik pređe mišem preko
određenog elementa ili možda kada skroluje neki sadržaj u prikazni prozor? To ne možemo da
uradimo sa osnovnim HTML-om. Pored toga, i klikovi i slanje obrazaca rezultiraju učitavanjem
potpuno nove veb stranice. Šta ako želimo da ažuriramo samo mali deo trenutne stranice? Ovo je
veoma čest scenario u bogatim veb aplikacijama, gde korisnici očekuju da preuzmu i ažuriraju
sadržaj bez prelaska na novu stranicu.

Dakle, sa osnovnim HTML-om, interakcije (klikovi i slanja) su ograničene i čvrsto povezane sa
jednom radnjom (učitavanje nove stranice). Naravno, korišćenjem JavaScript-a možemo
proširiti HTML i dodati neku novu sintaksu kako bismo podržali željene interakcije. Htmx radi
upravo to sa novim skupom atributa:

- Interakcije se mogu dodati bilo kom elementu, ne samo linkovima i obrascima.

- Interakcija se može pokrenuti klikom, slanjem, prelaskom miša ili bilo kojim drugim
JavaScript događajem.

- Radnje koje proizilaze iz okidača mogu izmeniti trenutnu stranicu, ne samo zahtevati novu
stranicu.

Razdvajanjem elemenata, okidača i akcija, htmx nam omogućava da izgradimo bogate aplikacije



vođene hipermedijom na način koji je veoma kompatibilan sa HTML sintaksom i veb razvojem
na strani servera.

HXML preuzima ideju definisanja interakcija putem okidača i akcija i ugrađuje ih u
specifikaciju. Ove interakcije nazivamo "ponašanjima". Koristimo poseban <behavior>element da
bismo ih definisali. Evo primera jednostavnog ponašanja koje pomera novi mobilni ekran na
navigacioni stek:

<text>
  <behavior <1>
    trigger="press" <2>
    action="push" <3>
    href="/next-screen" <4>
  />
  Press me!
</text>

Osnovno ponašanje

1. Element koji obuhvata interakciju na roditeljskom <text>elementu.

2. Okidač koji će izvršiti interakciju, u ovom slučaju pritiskanje <text>elementa.

3. Radnja koja će se izvršiti kada se pokrene, u ovom slučaju postavljanje novog ekrana na
trenutni stek.

4. href koji se učitava na novom ekranu.

Hajde da analiziramo šta se dešava u ovom primeru. Prvo, imamo <text>element sa sadržajem
"Pritisni me!". Već smo <text>ranije prikazivali elemente u primerima HXML-a, tako da ovo nije
ništa novo. Ali sada, <text>element sadrži novi podređeni element, <behavior>. Ovaj
<behavior>element definiše interakciju na roditeljskom <text>elementu. Sadrži dva atributa koja
su potrebna za bilo koje ponašanje:

- trigger: definiše korisničku akciju koja pokreće ponašanje

- action: definiše šta se dešava kada se aktivira

U ovom primeru, triggerje podešen na press, što znači da će se ova interakcija dogoditi kada
korisnik pritisne <text>element. actionAtribut je podešen na push. pushje radnja koja će pomeriti
novi ekran na navigacioni stek. Konačno, Hyperview treba da zna koji sadržaj da učita na
novopostavljenom ekranu. Tu hrefdolazi do izražaja atribut. Obratite pažnju da ne moramo da
definišemo punu URL adresu. Baš kao u HTML-u, može hrefbiti apsolutna ili relativna URL
adresa.

Dakle, to je prvi primer ponašanja u HXML-u. Možda mislite da ova sintaksa deluje prilično
opširno. Zaista, pritiskanje elemenata za navigaciju do novog ekrana jedna je od najčešćih
interakcija u mobilnoj aplikaciji. Bilo bi lepo imati jednostavniju sintaksu za uobičajeni slučaj.
Srećom, atributi triggeri actionimaju podrazumevane vrednosti pressi push, respektivno. Stoga
se mogu izostaviti da bi se sintaksa očistila:

<text>
  <behavior href="/next-screen" /> <1>
  Press me!
</text>

Osnovno ponašanje sa podrazumevanim podešavanjima



1. Kada se pritisne, ovo ponašanje će otvoriti novi ekran sa datom URL adresom.

Ova oznaka za <behavior>će proizvesti istu interakciju kao i prethodni primer. Sa
podrazumevanim atributima, <behavior>element izgleda slično sidru <a>u HTML-u. Ali puna
sintaksa postiže naše ciljeve razdvajanja elemenata, okidača i akcija:

- Ponašanja se mogu dodati bilo kom elementu, nisu ograničena samo na linkove i forme.

- Ponašanja mogu da navedu eksplicitni trigger, ne samo klikove ili slanja obrazaca.

- Ponašanja mogu da navedu eksplicitni action, ne samo zahtev za novu stranicu.

- Dodatni atributi poput hrefpružaju više konteksta za radnju.

Pored toga, korišćenje namenskog <behavior>elementa znači da jedan element može definisati
više ponašanja. Ovo nam omogućava da izvršimo nekoliko akcija iz istog okidača. Ili, možemo
izvršiti različite akcije za različite okidače na istom elementu. Na kraju ovog poglavlja ćemo
pokazati primere moći višestrukih ponašanja. Prvo moramo pokazati raznolikost podržanih
akcija i okidača.

Akcije

Radnje ponašanja u Hyperview-u se dele u četiri opšte kategorije:

- Akcije navigacije, koje učitavaju nove ekrane i kreću se između njih

- Akcije ažuriranja, koje menjaju HXML trenutnog ekrana

- Sistemske akcije, koje interaguju sa mogućnostima na nivou OS-a.

- Prilagođene akcije, koje mogu da izvršavaju bilo koji kod koji dodate klijentu.

Radnje navigacije

Već smo videli najjednostavniji tip akcije, push. Klasifikujemo je pushkao "akciju navigacije", jer je
povezana sa navigacijom po ekranima u mobilnoj aplikaciji. Pomeranje ekrana na stek navigacije
je samo jedna od nekoliko akcija navigacije koje podrži Hyperview. Korisnici takođe moraju biti
u mogućnosti da se vrate na prethodne ekrane, otvore i zatvore modalne prozore, prebace
između kartica ili pređu na proizvoljne ekrane. Svaka od ovih vrsta navigacije je podržana kroz
različitu vrednost za actionatribut:

- push: Ubacite novi ekran u trenutni stek za navigaciju. Ovo izgleda kao ekran koji se
pomera zdesna, preko trenutnog ekrana.

- newOtvorite novi stek za navigaciju kao modalni prozor. Ovo izgleda kao ekran koji se
pomera odozdo, preko trenutnog ekrana.

- backOvo je dopuna akciji push. Izbacuje trenutni ekran iz navigacionog steka (pomeranjem
udesno).

- closeOvo je dopuna radnje new. Zatvara trenutni stek za navigaciju (pomeranjem nadole).

- reloadSlično dugmetu "osveži" u pregledaču, ovo će ponovo zahtevati sadržaj trenutnog
ekrana.



- navigateOva radnja će pokušati da pronađe ekran sa datim podacima koji hrefsu već
učitani u aplikaciji. Ako ekran postoji, aplikacija će preći na taj ekran. Ako ne postoji,
ponašaće se isto kao push.

push, new, i navigatesvi učitavaju novi ekran. Stoga, oni zahtevaju hrefatribut kako bi Hyperview
znao koji sadržaj da zahteva za novi ekran. backi closene učitavaju nove ekrane, pa samim tim ne
zahtevaju hrefatribut. reloadje zanimljiv slučaj. Podrazumevano, koristiće URL adresu ekrana
kada ponovo zahteva sadržaj za ekran. Međutim, ako želite da zamenite ekran drugim, možete
da obezbedite atribut hrefsa reloadna elementu ponašanja.

Pogledajmo primer aplikacije "vidžeti" koja koristi nekoliko akcija navigacije na jednom ekranu:

<screen>
  <body>
    <header>
      <text>
        <behavior action="back" /> <1>
        Back
      </text>

      <text>
        <behavior action="new" href="/widgets/new" /> <2>
        New Widget
      </text>
    </header>
    <text>
      <behavior action="reload" /> <3>
      Check for new widgets
    </text>
    <list>
      <item key="widget1">
        <behavior action="push" href="/widgets/1" /> <4>
      </item>
    </list>
  </body>
</screen>

Primeri radnji navigacije

1. Vraća korisnika na prethodni ekran

2. Otvara novi modalni prozor za dodavanje vidžeta

3. Ponovo učitava sadržaj ekrana, prikazujući nove vidžete iz pozadine

4. Otvara novi ekran sa detaljima za određeni vidžet

Većini ekrana u vašoj aplikaciji biće potreban način da se korisnik vrati na prethodni ekran. To
se obično radi pomoću dugmeta u zaglavlju koje koristi akciju "nazad" ili "zatvori", u zavisnosti
od toga kako je ekran otvoren. U ovom primeru, pretpostavljamo da je ekran vidžeta pomeren na
navigacioni stek, tako da je akcija "nazad" odgovarajuća. Zaglavlje sadrži drugo dugme koje
omogućava korisniku da unese podatke za novi vidžet. Pritiskom na ovo dugme otvoriće se
modalni prozor sa ekranom "Novi vidžet". Pošto će se ovaj ekran "Novi vidžet" otvoriti kao
modalni prozor, biće mu potrebna odgovarajuća akcija "zatvori" da bi se sam zatvorio i ponovo
prikazao naš ekran "vidžeti". Konačno, da biste videli više detalja o određenom vidžetu, svaki
<item>element sadrži ponašanje sa akcijom "gurni". Ova akcija će pomeriti ekran "Detalji
vidžeta" na trenutni navigacioni stek. Kao i na ekranu "Vidžeti", "Detalji vidžeta" će biti potreban
dugme u zaglavlju koje koristi akciju "nazad" da bi omogućilo korisniku da se vrati.

Na vebu, pregledač obavlja osnovne potrebe za navigacijom kao što su kretanje unazad/napred,



ponovno učitavanje trenutne stranice ili prelazak na obeleživač. iOS i Android ne pružaju ovu
vrstu univerzalne navigacije za izvorne mobilne aplikacije. Na programerima aplikacija je da se
sami pobrinu za ovo. Akcije navigacije u HXML-u pružaju jednostavan, ali moćan način
programerima da izgrade arhitekturu koja ima smisla za njihovu aplikaciju.

Ažuriranje akcija

Akcije ponašanja nisu ograničene samo na navigaciju između ekrana. One se takođe mogu
koristiti za promenu sadržaja na trenutnom ekranu. Ove akcije nazivamo "akcijama ažuriranja".
Slično akcijama navigacije, akcije ažuriranja upućuju zahtev serverskom delu. Međutim, odgovor
nije ceo HXML dokument, već fragment HXML-a. Ovaj fragment se dodaje HXML-u trenutnog
ekrana, što rezultira ažuriranjem korisničkog interfejsa. Atribut actionodređuje <behavior>kako
se fragment uključuje u HXML. Takođe treba da uvedemo novi targetatribut <behavior>da bismo
definisali gde se fragment uključuje u postojeći dokument. targetAtribut je ID referenca na
postojeći element na ekranu.

Hipervju trenutno podržava ove akcije ažuriranja, koje predstavljaju različite načine za
uključivanje fragmenta u ekran:

- replace: zamenjuje ceo ciljni element fragmentom

- replace-inner: zamenjuje potomke ciljnog elementa fragmentom

- append: dodaje fragment nakon poslednjeg deteta ciljnog elementa

- prepend: dodaje fragment ispred prvog deteta ciljnog elementa.

Pogledajmo nekoliko primera da bismo ovo učinili konkretnijim. Za ove primere, pretpostavimo
da naš bekend prihvata GETzahteve za /fragment, a odgovor je fragment HXML-a koji izgleda kao
<text>My fragment</text>.

<screen>
  <body>
    <text>
      <behavior action="replace" href="/fragment" target="area1" /> <1>
      Replace
    </text>
    <view id="area1">
      <text>Existing content</text>
    </view>

    <text>
      <behavior action="replace-inner"

href="/fragment" target="area2" /> <2>
      Replace-inner
    </text>
    <view id="area2">
      <text>Existing content</text>
    </view>

    <text>
      <behavior action="append" href="/fragment" target="area3" /> <3>
      Append
    </text>
    <view id="area3">
      <text>Existing content</text>
    </view>

    <text>
      <behavior action="prepend" href="/fragment" target="area4" /> <4>
      Prepend



    </text>
    <view id="area4">
      <text>Existing content</text>
    </view>

  </body>
</screen>

Primeri akcija ažuriranja

1. Zamenjuje element area1 preuzetim fragmentom

2. Zamenjuje podređene elemente area2 sa preuzetim fragmentom

3. Dodaje preuzeti fragment u oblast3

4. Dodaje preuzeti fragment na početak oblasti4

U ovom primeru, imamo ekran sa četiri dugmeta koja odgovaraju četirima radnjama ažuriranja:
replace, replace-inner, append, prepend. Ispod svakog dugmeta nalazi se odgovarajuće dugme
<view>koje sadrži tekst. Imajte na umu da se ponašanje idsvakog prikaza podudara targetsa
ponašanjem odgovarajućeg dugmeta.

Kada korisnik pritisne prvo dugme, Hyperview klijent šalje zahtev za /fragment. Zatim, traži cilj,
tj. element sa ID-om "area1". Na kraju, zamenjuje element <view id="area1">preuzetim
fragmentom, <text>My fragment</text>. Postojeći prikaz i tekst sadržan u tom prikazu biće
zamenjeni. Korisniku će izgledati kao da je "Postojeći sadržaj" promenjen u "Moj fragment". U
HXML-u, element <view id="area1">će takođe nestati.

Drugo dugme se ponaša slično kao i prvo. Međutim, radnja replace-innerne uklanja ciljni
element sa ekrana, već samo zamenjuje podređene elemente. To znači da će rezultujuća oznaka
izgledati ovako <view id="area2"><text>My fragment</text></view>.

Treće i četvrto dugme ne uklanjaju sadržaj sa ekrana. Umesto toga, fragment će biti dodat ili
posle (u slučaju append) ili pre ( prepend) potomaka ciljnog elementa.

Radi potpunosti, pogledajmo stanje ekrana nakon što korisnik pritisne sva četiri dugmeta:

<screen>
  <body>
    <text>
      <behavior action="replace" href="/fragment" target="area1" /> <1>
      Replace
    </text>
    <text>My fragment</text>

    <text>
      <behavior action="replace-inner"

href="/fragment" target="area2" /> <2>
      Replace-inner
    </text>
    <view id="area2">
      <text>My fragment</text>
    </view>

    <text>
      <behavior action="append" href="/fragment" target="area3" /> <3>
      Append
    </text>
    <view id="area3">
      <text>Existing content</text>
      <text>My fragment</text>



    </view>

    <text>
      <behavior action="prepend" href="/fragment" target="area4" /> <4>
      Prepend
    </text>
    <view id="area4">
      <text>My fragment</text>
      <text>Existing content</text>
    </view>

  </body>
</screen>

Ažuriranje akcija, nakon pritiskanja dugmadi

1. Fragment je potpuno zamenio metu koristeći replaceakciju

2. Fragment je zamenio decu cilja koristeći replace-innerakciju

3. Fragment je dodat kao poslednji potomak cilja korišćenjem appendakcije

4. fragment dodat kao prvo dete cilja korišćenjem prependakcije

Gore navedeni primeri prikazuju akcije koje upućuju GETzahteve bekendu. Ali ove akcije takođe
mogu da upućuju POSTzahteve postavljanjem verb="post"na <behavior>element. Za oba, GETi za
zahteve i POSTza zahteve, podaci iz roditeljskog <form>elementa biće serijalizovani i uključeni u
zahtev. Za GETzahteve, sadržaj će biti kodiran URL-om i dodat kao parametri upita. Za
POSTzahteve, sadržaj će biti kodiran URL-om forme i postavljen na telu zahteva. Pošto
podržavaju POSTpodatke obrasca i, akcije ažuriranja se često koriste za slanje podataka bekendu.

Do sada, naš primer akcija ažuriranja zahteva dobijanje novog sadržaja iz pozadinskog sistema i
njegovo dodavanje na ekran. Ali ponekad samo želimo da promenimo stanje postojećih
elemenata. Najčešće stanje koje se menja za element je njegova vidljivost. Hiperprikaz ima akcije
hide, showi togglekoje rade upravo to. Kao i druge akcije ažuriranja, hide, showi togglekoriste
targetatribut da bi primenili akciju na element na trenutnom ekranu.

<screen>
  <body>
    <text>
      <behavior action="hide" target="area" /> <1>
      Hide
    </text>

    <text>
      <behavior action="show" target="area" /> <2>
      Show
    </text>

    <text>
      <behavior action="toggle" target="area" /> <3>
      Toggle
    </text>

    <view id="area"> <4>
      <text>My fragment</text>
    </view>
  </body>
</screen>

Prikaži, sakrij i uključi/isključi radnje

1. Sakriva element sa ID-om "area".



2. Prikazuje element sa ID-om "area".

3. Uključuje/isključuje vidljivost elementa sa identifikatorom "area".

4. Element na koji su akcije ciljane.

U ovom primeru, tri dugmeta označena sa "Sakrij", "Prikaži" i "Uključi/isključi" će izmeniti
stanje prikaza oblasti <view>sa ID-om "područje". Višestruki pritisak na "Sakrij" neće imati
efekta kada je prikaz sakriven. Slično tome, višestruki pritisak na "Prikaži" neće imati efekta
kada se prikaz ponovo pojavi. Pritiskom na "Uključi/isključi" ćete stalno menjati status
vidljivosti elementa između prikazanog i skrivenog.

Hipervju dolazi sa drugim akcijama koje menjaju postojeći HXML. Nećemo ih detaljno
obrađivati, ali ću ih ovde ukratko pomenuti:

- set-value: ova radnja može da podesi vrednost ulaznog elementa kao što je <text-field>,
<switch>, <select-single>, itd.

- select-alli unselect-allradite sa <select-multiple>elementom da biste izabrali/poništili
izbor svih opcija.

Sistemske akcije

Neke standardne akcije Hyperview-a uopšte ne interaguju sa HXML-om. Umesto toga, one
otkrivaju funkcionalnosti koje pruža mobilni operativni sistem. Na primer, i Android i iOS
podržavaju korisnički interfejs "Deli" na nivou sistema. Ovaj korisnički interfejs omogućava
deljenje URL-ova i poruka iz jedne aplikacije u drugu. Hyperview ima akciju sharekoja podržava
ovu interakciju. Ona uključuje prilagođeni imenski prostor i atribute specifične za deljenje.

<behavior
xmlns:share="https://instawork.com/hyperview-share" <1>

  trigger="press"
  action="share" <2>
  share:url="https://www.instawork.com" <3>
  share:message="Check out this website!" <4>
/>

Akcija deljenja sistema

1. Definiše imenski prostor za akciju deljenja.

2. Radnja ovog ponašanja će otvoriti listu deljenja.

3. URL adresa za deljenje.

4. Poruka za deljenje.

Videli smo XML imenske prostore kada smo govorili o prilagođenim elementima. Ovde
koristimo imenski prostor za atribute urli messagena <behavior>. Ova imena atributa su generička
i verovatno ih koriste druge komponente i ponašanja, tako da imenski prostor osigurava da neće
biti dvosmislenosti. Kada se pritisne, pokrenuće se akcija "deljenje". Vrednosti atributa urli
messagebiće prosleđene sistemskom korisničkom interfejsu za deljenje. Odatle će korisnik moći
da deli URL i poruku putem SMS-a, e-pošte ili drugih aplikacija za komunikaciju.

Akcija sharepokazuje kako akcija ponašanja može koristiti prilagođene atribute za prosleđivanje
dodatnih podataka potrebnih za interakcije. Ali neke akcije zahtevaju još više strukturiranih
podataka. Ovo se može obezbediti putem podređenih elemenata na <behavior>. Hiperprikaz



koristi ovo za implementaciju alertakcije. alertAkcija prikazuje prilagođeni dijaloški prozor na
nivou sistema. Ovaj dijalog zahteva konfiguraciju za naslov i poruku, ali i za prilagođena
dugmad. Svako dugme zatim mora da pokrene drugo ponašanje kada se pritisne. Ovaj nivo
konfiguracije se ne može uraditi samo sa atributima, pa koristimo prilagođene podređene
elemente da bismo predstavili ponašanje svakog dugmeta.

<behavior
xmlns:alert="https://hyperview.org/hyperview-alert" <1>

  trigger="press"
  action="alert" <2>
  alert:title="Continue to next screen?" <3>
  alert:message=
    "Are you sure you want to navigate to the next screen?" <4>
>
  <alert:option alert:label="Continue"> <5>
    <behavior action="push" href="/next" /> <6>
  </alert:option>
  <alert:option alert:label="Cancel" /> <7>
</behavior>

Akcija sistemskog upozorenja

1. Definiše imenski prostor za akciju upozorenja.

2. Radnja ovog ponašanja će otvoriti sistemski dijaloški prozor.

3. Naslov dijaloškog okvira.

4. Sadržaj dijaloškog okvira.

5. Opcija "nastavi" u dijaloškom okviru

6. Kada se pritisne "nastavi", otvori se novi ekran na navigacionom steku.

7. Opcija "otkaži" koja zatvara dijaloški prozor.

Kao i shareponašanje, alertkoristi imenski prostor za definisanje nekih atributa i elemenata.
<behavior>Sam element sadrži atribute titlei messageza dijaloški okvir. Opcije dugmeta za dijalog
su definisane korišćenjem novog <option>elementa ugnežđenog u <behavior>. Obratite pažnju da
svaki <option>element ima oznaku, a zatim opciono sadrži i <behavior>samog sebe! Ova struktura
HXML-a omogućava sistemskom dijalogu da pokrene bilo koju interakciju koja se može
definisati kao <behavior>. U gornjem primeru, pritiskom na dugme "Nastavi" otvoriće se novi
ekran. Ali bismo podjednako lako mogli da pokrenemo akciju ažuriranja da bismo promenili
trenutni ekran. Mogli bismo čak da otvorimo i tabelu za deljenje ili drugi dijaloški okvir. Ali
molim vas, nemojte to raditi u pravoj aplikaciji! Sa velikom moći dolazi i velika odgovornost.

Prilagođene radnje

Možete da napravite mnogo mobilnih korisničkih interfejsa pomoću standardnih akcija za
navigaciju, ažuriranje i sistemske akcije Hyperview-a. Ali standardni set možda ne pokriva sve
interakcije koje će vam biti potrebne za vašu mobilnu aplikaciju. Srećom, sistem akcija je
proširiv. Na isti način na koji možete da dodate prilagođene elemente u Hyperview, možete da
dodate i prilagođene akcije ponašanja. Prilagođene akcije imaju sličnu sintaksu kao sharei
alertakcije, koristeći imenske prostore za atribute koji prosleđuju dodatne podatke. Prilagođene
akcije takođe imaju potpun pristup HXML-u trenutnog ekrana, tako da mogu da menjaju stanje
ili dodaju/uklanjaju elemente sa trenutnog ekrana. U sledećem poglavlju ćemo kreirati
prilagođenu akciju ponašanja kako bismo poboljšali našu aplikaciju za mobilne kontakte.



Okidači

Već smo videli najjednostavniji tip okidača, okidač pressna elementu. Hipervju podržava mnoge
druge uobičajene okidače koji se koriste u mobilnim aplikacijama.

Dugo pritisnite

Usko povezano sa pritiskom je dugi pritisak. Ponašanje koje trigger="longPress"se aktivira kada
korisnik pritisne i drži element. Interakcije "dugog pritiska" se često koriste za prečice i funkcije
napajanja. Ponekad, elementi će podržavati različite radnje i za a pressi za longPress. To se radi
korišćenjem više <behavior>elemenata na istom elementu korisničkog interfejsa.

<text>
  <behavior trigger="press" action="push" href="/next-screen" /> <1>
  <behavior trigger="longPress" <2>
    action="push" href="/secret-screen" />
  Press (or long-press) me!
</text>

Primer dugog pritiska okidača

1. Normalnim pritiskom otvoriće se sledeći ekran.

2. Dugim pritiskom otvoriće se drugi ekran.

U ovom primeru, običan pritisak će otvoriti novi ekran i zatražiti sadržaj od /next-screen.
Međutim, dug pritisak će otvoriti novi ekran sa sadržajem od /secret-screen. Ovo je izmišljen
primer radi kratkoće. Bolje korisničko iskustvo bi bilo da dugi pritisak otvori kontekstualni meni
sa prečicama i naprednim opcijama. To se može postići korišćenjem action="alert"i otvaranjem
sistemskog dijaloškog prozora sa prečicama.

Učitaj

Ponekad želimo da se akcija pokrene čim se ekran učita. trigger="load"Radi upravo to. Jedan od
slučajeva upotrebe je brzo učitavanje ljuske ekrana, a zatim popunjavanje glavnog sadržaja na
ekranu drugom akcijom ažuriranja.

<body>
  <view>
    <text>My app</text>
    <view id="container"> <1>
      <behavior trigger="load" action="replace" href="/content"

target="container"> <2>
      <text>Loading...</text> <3>
    </view>
  </view>
</body>

Primer okidača za učitavanje

1. Element kontejnera bez stvarnog sadržaja

2. Ponašanje koje odmah pokreće zahtev za /content kako bi se zamenio kontejner

3. Učitavanje korisničkog interfejsa koji se pojavljuje dok se sadržaj ne preuzme i zameni.

U ovom primeru, učitavamo ekran sa naslovom ("Moja aplikacija"), ali bez sadržaja. Umesto
toga, prikazujemo <view>sa ID-om "kontejner" i tekstom "Učitavanje…". Čim se ovaj ekran učita,



ponašanje trigger=loadpokreće replaceakciju. Zahteva sadržaj iz /contentputanje i zamenjuje
prikaz kontejnera odgovorom.

Vidljivo

Za razliku od load, visibleokidač će izvršiti ponašanje samo kada se element sa ponašanjem
skroluje u prikazni prozor na mobilnom uređaju. Akcija visiblese obično koristi za
implementaciju interakcije beskonačnog skrolovanja na <list>elemente <item>. Poslednja stavka
na listi uključuje ponašanje sa trigger="visible". appendAkcija će preuzeti sledeću stranicu stavki
i dodati ih na listu.

Osveži

Ovaj okidač beleži akciju "povuci za osvežavanje" na <list>stavkama <view>. Ova interakcija je
povezana sa preuzimanjem ažuriranog sadržaja sa bekenda. Stoga je obično uparena sa akcijom
ažuriranja ili ponovnog učitavanja kako bi se prikazali najnoviji podaci na ekranu.

<body>
  <view scroll="true">
    <behavior trigger="refresh" action="reload" /> <1>
    <text>No items yet</text>
  </view>
</body>

Primer okidača za osvežavanje povlačenjem

1. Kada se prikaz povuče nadole da bi se osvežio, ponovo učitajte ekran.

Imajte na umu da će dodavanje ponašanja sa trigger="refresh"u <view>ili <list>dodati
interakciju "povuci za osvežavanje" elementu, uključujući prikazivanje rotirajućeg dugmeta dok
se element povuče nadole.

Fokus, zamućenje i promena

Ovi okidači su povezani sa interakcijama sa ulaznim elementima. Stoga će pokrenuti samo
ponašanja vezana za elemente kao što su <text-field>. focusi blurpokrenuće se kada korisnik
fokusira i zamuti ulazni element, respektivno. changepokrenuće se kada se vrednost ulaznog
elementa promeni, kao kada korisnik ukuca slovo u tekstualno polje. Ovi okidači se često koriste
sa ponašanjima koja zahtevaju validaciju na strani servera na poljima obrasca. Na primer, kada
korisnik ukuca korisničko ime, a zatim zamuti polje, ponašanje bi moglo da pokrene blurzahtev
bekendu i proveri jedinstvenost korisničkog imena. Ako uneto korisničko ime nije jedinstveno,
odgovor bi mogao da sadrži poruku o grešci koja obaveštava korisnika da treba da izabere drugo
korisničko ime.

Korišćenje višestrukih ponašanja

Većina gore prikazanih primera pridaje singl <behavior>elementu. Ali ne postoji takvo
ograničenje u Hipervjuu; elementi mogu definisati višestruka ponašanja. Već smo videli primer
gde je jedan element imao različite akcije pokrenute na pressi longPress. Ali takođe možemo
pokrenuti više akcija na istom okidaču.

U ovom, priznajem, izmišljenom primeru, želimo da sakrijemo dva elementa na ekranu kada
pritisnemo dugme "Sakrij". Dva elementa su daleko jedan od drugog u HXML-u i ne mogu se
sakriti sakrivanjem zajedničkog elementa pretka. Međutim, možemo pokrenuti dva ponašanja



istovremeno, svako izvršava akciju "sakrivanja", ali cilja različite elemente.

<screen>
  <body>
    <text id="area1">Area 1</text>

    <text>
      <behavior trigger="press" action="hide" target="area1" /> <1>
      <behavior trigger="press" action="hide" target="area2" /> <2>
      Hide
    </text>

    <text id="area2">Area 2</text>
  </body>
</screen>

Višestruka ponašanja koja se pokreću pritiskom

1. Sakrij element sa ID-om "area1" kada se pritisne.

2. Sakrij element sa ID-om "area2" kada se pritisne.

Hipervju obrađuje ponašanja redosledom kojim se pojavljuju u oznakama. U ovom slučaju,
element sa ID-om "area1" će prvo biti skriven, a zatim element sa ID-om "area2". Pošto je "hide"
trenutna akcija (tj. ne upućuje HTTP zahtev), oba elementa će izgledati kao da se skrivaju
istovremeno. Ali šta ako pokrenemo dve akcije koje zavise od odgovora HTTP zahteva (kao što je
"replace-inner")? U tom slučaju, svaka pojedinačna akcija se obrađuje čim Hipervju primi HTTP
odgovor. U zavisnosti od latencije mreže, dve akcije mogu stupiti na snagu bilo kojim
redosledom i nije garantovano da će se primeniti istovremeno.

Videli smo elemente sa višestrukim ponašanjima i različitim okidačima. I videli smo elemente sa
višestrukim ponašanjima sa istim okidačem. Ovi koncepti se takođe mogu kombinovati. Nije
neuobičajeno da produkcijska Hyperview aplikacija sadrži nekoliko ponašanja, neka se pokreću
zajedno, a druga se pokreću pri različitim interakcijama. Korišćenje višestrukih ponašanja sa
prilagođenim akcijama održava HXML deklarativnim, bez žrtvovanja funkcionalnosti.

Rezime

Ovde obrađujemo mnogo novih koncepata, a ovaj uvod u HXML je samo početni korak. Da biste
saznali više o HXML-u, preporučujemo da konsultujete zvaničnu referentnu dokumentaciju. Za
sada se nadamo da ćete naučiti nekoliko ključnih stvari.

Prvo, HXML izgleda i deluje slično HTML-u. Veb programeri koji su upoznati sa frejmvorcima
za renderovanje na strani servera mogu da koriste iste tehnike za pisanje HXML-a. Pored
osnovnih elemenata korisničkog interfejsa ( <view>, <text>, <image>), HXML specificira elemente
za implementaciju korisničkih interfejsa specifičnih za mobilne uređaje. To uključuje obrasce
rasporeda ( <screen>, <list>, <section-list>) i elemente unosa ( <switch>, <select-single>,
<select-multiple>).

Drugo, interakcije u HXML-u su definisane korišćenjem ponašanja. Inspirisani htmx-om,
<behavior>elementi odvajaju interakcije korisnika (okidače) od rezultujućih akcija. Postoje tri
široke kategorije akcija ponašanja:

- Radnje navigacije ( push, back) omogućavaju navigaciju između ekrana mobilne aplikacije

- Akcije ažuriranja ( replace, append) omogućavaju ažuriranje ekrana novim fragmentima
HXML-a koji su zatraženi sa servera.



- Sistemske akcije ( alert, share) omogućavaju interakciju sa funkcijama na sistemskom
nivou na iOS i Android uređajima.

Konačno, sam HXML je dizajniran za prilagođavanje. Programeri mogu definisati prilagođene
elemente i prilagođene akcije ponašanja kako bi proširili moguće interakcije korisnika sa svojim
aplikacijama.

Postoje jaki razlozi za aplikacije vođene hipermedijom na mobilnim uređajima. Platforme
mobilnih aplikacija guraju programere ka arhitekturi debelog klijenta. Ali aplikacije koje koriste
debeli klijent pate od istih problema kao i SPA na vebu. Korišćenje hipermedijske arhitekture za
mobilne aplikacije može rešiti ove probleme.

Hipervju, zasnovan na novom formatu pod nazivom HXML, nudi put ovde. On pruža mobilni
tanki klijent otvorenog koda za renderovanje HXML-a. A HXML otvara skup elemenata i
obrazaca koji odgovaraju mobilnim korisničkim interfejsima. Programeri mogu da razvijaju
Hipervju kako bi odgovarao zahtevima svojih aplikacija, dok u potpunosti prihvataju
hipermedijsku arhitekturu. To je pobeda.

Da, hipermedija može da funkcioniše i za mobilne aplikacije. U naredna dva poglavlja
pokazaćemo kako tako što ćemo veb aplikaciju Contact.app pretvoriti u izvornu mobilnu
aplikaciju koristeći Hyperview.

Hipermedijske beleške: Maksimizirajte svoje prednosti na strani servera

U odeljcima knjige o hiperpregledu, pošto ne koristimo HTML, napravićemo šira zapažanja o
hipermedijima umesto da nudimo savete i razmišljanja specifična za HTML.

Velika prednost pristupa zasnovanog na hipermediji je to što čini serversko okruženje mnogo
važnijim prilikom izrade vaše veb aplikacije. Umesto da samo proizvodi JSON, vaš bekend je
sastavni deo korisničkog iskustva vaše hipermedijske aplikacije.

Zbog toga, ima smisla detaljno ispitati funkcionalnosti koje su tamo dostupne. Mnogi stariji veb
okviri, na primer, imaju neverovatno duboku funkcionalnost dostupnu za kreiranje HTML-a.
Funkcije poput keširanja na strani servera mogu napraviti razliku između neverovatno brze veb
aplikacije i sporog korisničkog iskustva.

Odvojite vreme da naučite sve alate koji su vam dostupni.

Dobro pravilo je da se trudite da odgovori servera u vašoj aplikaciji vođenoj hipermedijom traju
manje od 100 ms, a zreli serverski frejmvorci imaju alate koji pomažu da se to desi.

Serverska okruženja često imaju izuzetno razvijene mehanizme za pravilno faktorisanje (ili
organizovanje) vašeg koda. Šablon Model/Prikaz/Kontroler je dobro razvijen u većini okruženja,
a alati poput modula, paketa itd. pružaju odličan način za organizovanje vašeg koda.

Dok su današnji SPA i mobilni korisnički interfejsi obično organizovani putem komponenti,
aplikacije vođene hipermedijom su obično organizovane putem uključivanja šablona, gde se
šabloni na strani servera razlažu prema potrebama aplikacije za prikazivanje hipermedija, a
zatim se uključuju jedan u drugi po potrebi. Ovo obično dovodi do manjeg broja, krupnijih
datoteka nego što biste pronašli u aplikaciji zasnovanoj na komponentama.

Još jedna tehnologija koju treba potražiti su fragmenti šablona, koji vam omogućavaju da
prikažete samo deo datoteke šablona. Ovo može dodatno smanjiti broj datoteka šablona
potrebnih za vašu serversku aplikaciju.

Povezani savet je da iskoristite direktan pristup skladištu podataka. Kada je aplikacija izgrađena
korišćenjem pristupa "debelog" klijenta, skladište podataka se obično nalazi iza API-ja podataka
(npr. JSON). Ovaj nivo indirektnosti često sprečava programere front-enda da u potpunosti
iskoriste alate dostupne u skladištu podataka. GraphQL, na primer, može pomoći u rešavanju
ovog problema, ali dolazi sa problemima vezanim za bezbednost koje mnogi programeri izgleda
ne razumeju dobro.

S druge strane, kada proizvodite hipermediju na strani servera, programer koji kreira tu
hipermediju može imati potpun pristup skladištu podataka i iskoristiti prednosti, na primer,
funkcija spajanja i agregacije u SQL skladištima.

Ovo daje daleko veću izražajnu moć direktno u ruke programera koji proizvodi finalni
hipermedijski sadržaj. Pošto vaš hipermedijski API može biti strukturiran prema potrebama
vašeg korisničkog interfejsa, možete podesiti svaku krajnju tačku da izda što je moguće manje
zahteva za skladištenje podataka.

Dobro pravilo je da svaki zahtev vašem serveru treba da ima tri ili manje pristupa skladištu
podataka. Ako pratite ovo pravilo, vaša aplikacija zasnovana na hipermediji trebalo bi da bude
izuzetno brza.
