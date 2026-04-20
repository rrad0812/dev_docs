
Kreiranje aplikacije za kontakte pomoću Hyperview-a

U ranijim poglavljima ove knjige objašnjene su prednosti izgradnje aplikacija korišćenjem hipermedijalne
arhitekture. Ove prednosti su demonstrirane izgradnjom robusne veb aplikacije "Kontakti". Zatim, u 11.
poglavlju se tvrdilo da se hipermedijski koncepti mogu i treba primeniti na platforme koje nisu veb. Predstavili
smo Hyperview kao primer hipermedijalnog formata i klijenta posebno dizajniranog za izgradnju mobilnih
aplikacija. Ali možda se i dalje pitate: kako je kreirati potpuno funkcionalnu, produkciono spremnu mobilnu
aplikaciju koristeći Hyperview? Da li moramo da naučimo potpuno novi jezik i frejmvork? U ovom poglavlju
ćemo pokazati Hyperview u akciji prenoseći veb aplikaciju "Kontakti" na izvornu mobilnu aplikaciju. Videćete
da su mnoge tehnike veb razvoja (i zapravo, veliki deo koda) potpuno identične kada se razvija pomoću
Hyperview-a. Kako je to moguće?

1. Naša veb aplikacija Kontakti je napravljena po principu HATEOAS (Hipermedija kao mehanizam stanja
aplikacije). Sve funkcije aplikacije (preuzimanje, pretraživanje, uređivanje i kreiranje kontakata) su
implementirane u pozadini ( ContactsPython klasa). Naša mobilna aplikacija, napravljena pomoću
Hyperview-a, takođe koristi HATEOAS i oslanja se na pozadinu za svu logiku aplikacije. To znači da
Python Contactsklasa može da pokreće našu mobilnu aplikaciju na isti način na koji pokreće veb
aplikaciju, bez ikakvih potrebnih promena.

2. Komunikacija klijent-server u veb aplikaciji se odvija pomoću HTTP-a. HTTP server za našu veb
aplikaciju je napisan pomoću Flask frejmvorka. Hyperview takođe koristi HTTP za komunikaciju klijent-
server. Tako da možemo ponovo da koristimo Flask rute i prikaze iz veb aplikacije i za mobilnu aplikaciju.

3. Veb aplikacija koristi HTML za svoj hipermedijski format, a Hyperview koristi HXML. HTML i HXML su
različiti formati, ali je osnovna sintaksa slična (ugnežđene oznake sa atributima). To znači da možemo
koristiti istu biblioteku šablona (Jinja) za HTML i HXML. Pored toga, mnogi koncepti htmx-a su
ugrađeni u HXML. Možemo direktno preneti funkcije veb aplikacije implementirane pomoću htmx-a
(pretraga, beskonačno učitavanje) u HXML.

U suštini, možemo ponovo da koristimo skoro sve iz bekenda veb aplikacije, ali ćemo morati da zamenimo
HTML šablone sa HXML šablonima. Većina odeljaka u ovom poglavlju pretpostavljaće da aplikacija za veb
kontakte radi lokalno i da sluša na portu 5000. Spremni? Hajde da kreiramo nove HXML šablone za korisnički
interfejs naše mobilne aplikacije.

Kreiranje mobilne aplikacije
Da biste započeli sa HXML-om, postoji jedan dosadan zahtev: Hyperview klijent. Prilikom razvoja veb
aplikacija, potrebno je da brinete samo o serveru jer je klijent (veb pregledač) univerzalno dostupan. Ne postoji
ekvivalentan Hyperview klijent instaliran na svakom mobilnom uređaju. Umesto toga, kreiraćemo sopstveni
Hyperview klijent, prilagođen da komunicira samo sa našim serverom. Ovaj klijent može biti upakovan u
mobilnu aplikaciju za Android ili iOS i distribuiran preko odgovarajućih prodavnica aplikacija.

Srećom, ne moramo da počinjemo od nule da bismo implementirali Hyperview klijent. Hyperview
repozitorijum koda dolazi sa demo bekendom i demo klijentom napravljenim pomoću Expo-a. Koristićemo
ovaj demo klijent, ali ćemo ga usmeriti ka našem bekend-u aplikacije za kontakte kao početnoj tački.

git clone git@github.com:Instawork/hyperview.git
cd hyperview/demo
yarn <1>
yarn start <2>

1. Instalirajte zavisnosti za demo aplikaciju

2. Pokrenite Expo server da biste pokrenuli mobilnu aplikaciju u iOS simulatoru.

Nakon pokretanja yarn start, biće vam prikazan prompt koji traži da otvorite mobilnu aplikaciju pomoću
Android emulatora ili iOS simulatora. Izaberite opciju na osnovu toga koji programerski SDK imate instaliran.
(Snimci ekrana u ovom poglavlju biće preuzeti iz iOS simulatora.) Uz malo sreće, videćete da je mobilna
aplikacija Expo instalirana u simulatoru. Mobilna aplikacija će se automatski pokrenuti i prikazati ekran sa
porukom "Mrežni zahtev nije uspeo". To je zato što je ova aplikacija podrazumevano konfigurisana da upućuje



zahtev ka http://0.0.0.0:8085/index.xml, ali naš bekend sluša na portu 5000. Da bismo ovo popravili,
možemo napraviti jednostavnu promenu konfiguracije u datoteci demo/src/constants.js:

//export const ENTRY_POINT_URL = 'http://0.0.0.0:8085/index.xml'; <1>
export const ENTRY_POINT_URL = 'http://0.0.0.0:5000/'; <2>

1. Podrazumevana URL adresa ulazne tačke u demo aplikaciji

2. Postavljanje URL-a da vodi do naše aplikacije za kontakte

Još uvek nismo pokrenuti. Sada kada naš Hyperview klijent pokazuje na pravu krajnju tačku, vidimo drugu
grešku, "ParseError". To je zato što bekend odgovara na zahteve sa HTML sadržajem, ali Hyperview klijent
očekuje XML odgovor (tačnije, HXML). Zato je vreme da usmerimo pažnju na naš Flask bekend. Proći ćemo
kroz Flask prikaze i zameniti HTML šablone HXML šablonima. Konkretno, hajde da podržimo sledeće funkcije
u našoj mobilnoj aplikaciji:

- Lista kontakata koja se može pretraživati

- Pregled detalja kontakta

- Uređivanje kontakta

- Brisanje kontakta

- Dodavanje novog kontakta

Počećemo sa izradom naše Hyperview aplikacije sa ekranom za ulaznu tačku, listom kontakata. Za početnu
verziju ovog ekrana, hajde da podržimo sledeće funkcije iz veb aplikacije:

- prikažite listu kontakata koja se može skrolovati

- polje "pretraga tokom kucanja" iznad liste

- "beskonačno skrolovanje" da bi se učitalo više kontakata dok korisnik skroluje

Pored toga, dodaćemo interakciju "povuci za osvežavanje" na listu, jer korisnici to očekuju od korisničkih
interfejsa lista u mobilnim aplikacijama.

Ako se sećate, sve stranice u veb aplikaciji Kontakti su proširile zajednički osnovni šablon, layout.html.
Potreban nam je sličan osnovni šablon za ekrane mobilne aplikacije. Ovaj osnovni šablon će sadržati pravila
stila našeg korisničkog interfejsa i osnovnu strukturu zajedničku za sve ekrane. Nazovimo je layout.xml.

<doc xmlns="https://hyperview.org/hyperview">
  <screen>
    <styles><!-- omitted for brevity --></styles>
    <body style="body" safe-area="true">
      <header style="header">
        {% block header %} <1>
          <text style="header-title">Contact.app</text>
        {% endblock %}
      </header>

      <view style="main">
        {% block content %}{% endblock %} <2>
      </view>
    </body>
  </screen>
</doc>

Osnovni šablonhv/layout.xml

1. Zaglavlje šablona, sa podrazumevanim naslovom.

2. Odeljak sadržaja šablona, koji će obezbediti drugi šabloni.

Koristimo HXML oznake i atribute obrađene u prethodnom poglavlju. Ovaj šablon podešava osnovni raspored
ekrana koristeći oznake <doc>, <screen>, <body>, <header>, i <view>. Imajte na umu da se HXML sintaksa dobro
slaže sa bibliotekom šablona Jinja. Ovde koristimo Jinja blokove da definišemo dva odeljka ( headeri content)



koji će sadržati jedinstveni sadržaj ekrana. Kada je naš osnovni šablon završen, možemo da kreiramo šablon
posebno za ekran liste kontakata.

{% extends 'hv/layout.xml' %} <1>

{% block content %} <2>
  <form> <3>
    <text-field name="q" value="" placeholder="Search..."

style="search-field" />
    <list id="contacts-list"> <4>
      {% include 'hv/rows.xml' %}
    </list>
  </form>
{% endblock %}

Početakhv/index.xml

1. Proširite osnovni šablon rasporeda

2. Zameni contentblok šablona rasporeda

3. Napravite obrazac za pretragu koji će izdati HTTP GETporuku/contacts

4. Lista kontakata, koristeći DŽindža includeoznaku.

Ovaj šablon proširuje bazu layout.xmli zamenjuje contentblok sa <form>. U početku može izgledati čudno što
obrazac obuhvata i <text-field>elemente i <list>. Ali zapamtite: u Hiperprikazu, podaci obrasca se uključuju u
svaki zahtev koji potiče od podređenog elementa. Uskoro ćemo dodati interakcije na listu (povuci za
osvežavanje) koje će zahtevati podatke obrasca. Obratite pažnju na upotrebu oznake Jinja includeza
prikazivanje HXML-a za redove kontakata na listi ( hv/rows.xml). Baš kao i u HTML šablonima, možemo
koristiti includeda razbijemo naš HXML na manje delove. Takođe omogućava serveru da odgovori samo sa
rows.xmlšablonom za interakcije kao što su pretraživanje, beskonačno skrolovanje i povlačenje za osvežavanje.

<items xmlns="https://hyperview.org/hyperview"> <1>
  {% for contact in contacts %} <2>
    <item key="{{ contact.id }}" style="contact-item"> <3>
      <text style="contact-item-label">
        {% if contact.first %}
          {{ contact.first }} {{ contact.last }}
        {% elif contact.phone %}
          {{ contact.phone }}
        {% elif contact.email %}
          {{ contact.email }}
        {% endif %}
      </text>
    </item>
  {% endfor %}
</items>

hv/rows.xml

1. HXML element koji grupiše skup <item>elemenata u zajedničkog pretka.

2. Ponovite postupak kroz kontakte koji su prosleđeni šablonu.

3. Prikaži <item>za svaki kontakt, prikazujući ime, broj telefona ili imejl adresu.

U veb aplikaciji, svaki red na listi je prikazivao ime kontakta, broj telefona i adresu e-pošte. Ali u mobilnoj
aplikaciji imamo manje prostora. Bilo bi teško ugurati sve ove informacije u jedan red. Umesto toga, red
prikazuje samo ime i prezime kontakta, a vraća se na telefon ili adresu e-pošte ako ime nije podešeno. Da bismo
prikazali red, ponovo koristimo sintaksu šablona Jinja za prikazivanje dinamičkog teksta sa podacima
prosleđenim šablonu.

Sada imamo šablone za osnovni raspored, ekran kontakata i redove kontakata. Ali i dalje moramo da ažuriramo
Flask prikaze da bismo koristili ove šablone. Hajde da pogledamo prikaz contacts()u njegovom trenutnom
obliku, napisan za veb aplikaciju:

@app.route("/contacts")
def contacts():
    search = request.args.get("q")
    page = int(request.args.get("page", 1))



if search:
        contacts_set = Contact.search(search)

if request.headers.get('HX-Trigger') == 'search':
return render_template("rows.html",

              contacts=contacts_set, page=page)
else:

        contacts_set = Contact.all(page)
return render_template("index.html",

      contacts=contacts_set, page=page)

app.py

Ovaj prikaz podržava preuzimanje skupa kontakata na osnovu dva parametra upita, qi page. Takođe odlučuje da
li da prikaže celu stranicu ( index.html) ili samo redove kontakata ( rows.html) na osnovu HX-Triggerzaglavlja.
Ovo predstavlja manji problem. HX-TriggerZaglavlje podešava htmx biblioteka; ne postoji ekvivalentna funkcija
u Hyperview-u. Štaviše, postoji više scenarija u Hyperview-u koji zahtevaju da odgovorimo samo sa redovima
kontakata:

- pretraživanje

- osvežavanje povlačenjem

- učitava se sledeća stranica kontakata

Pošto ne možemo da se oslonimo na zaglavlje kao što je HX-Trigger, potreban nam je drugačiji način da
otkrijemo da li je klijentu potreban ceo ekran ili samo redovi u odgovoru. To možemo da uradimo uvođenjem
novog parametra upita, rows_only. Kada ovaj parametar ima vrednost true, prikaz će odgovoriti na zahtev
prikazivanjem rows.xmlšablona. U suprotnom, odgovoriće šablonom index.xml:

@app.route("/contacts")
def contacts():
    search = request.args.get("q")
    page = int(request.args.get("page", 1))
    rows_only = request.args.get("rows_only") == "true" <1>

if search:
        contacts_set = Contact.search(search)

else:
        contacts_set = Contact.all(page)

    template_name = "hv/rows.xml" if rows_only else "hv/index.xml" <1>
return render_template(template_name,

      contacts=contacts_set, page=page)

app.py

1. Proverite novi rows_onlyparametar upita.

2. Prikažite odgovarajući HXML šablon na osnovu rows_only.

Moramo napraviti još jednu promenu. Flask pretpostavlja da će većina prikaza odgovoriti HTML-om. Zato
Flask podrazumevano podešava Content-Typezaglavlje odgovora na vrednost text/html. Ali Hyperview klijent
očekuje da primi HXML sadržaj, što je naznačeno Content-Typezaglavljem odgovora sa vrednošću application/
vnd.hyperview+xml. Klijent će odbiti odgovore sa drugačijim tipom sadržaja. Da bismo ovo popravili, potrebno je
eksplicitno da podesimo Content-Typezaglavlje odgovora u našim Flask prikazima. To ćemo učiniti uvođenjem
nove pomoćne funkcije, render_to_response():

def render_to_response(template_name, *args, **kwargs):
    content = render_template(template_name, *args, **kwargs) <1>
    response = make_response(content) <2>
    response.headers['Content-Type'] =

'application/vnd.hyperview+xml' <3>
return response

app.py

1. Prikazuje dati šablon sa navedenim argumentima i ključnim rečima.

2. Napravite eksplicitni objekat odgovora sa renderovanim šablonom.

3. Postavlja zaglavlje odgovora Content-Typena XML.



Kao što vidite, ova pomoćna funkcija koristi " render_template()ispod haube". render_template()vraća string.
Ova pomoćna funkcija koristi taj string da bi kreirala eksplicitni Responseobjekat. Objekat odgovora ima
headersatribut, koji nam omogućava da podesimo i promenimo zaglavlja odgovora. Konkretno,
render_to_response()postavlja Content-Typena application/vnd.hyperview+xmltako da Hyperview klijent prepozna
sadržaj. Ovaj pomoćnik je zamena za render_templateu našim prikazima. Dakle, sve što treba da uradimo je da
ažuriramo poslednji red funkcije contacts().

return render_to_response(template_name,
  contacts=contacts_set, page=page) <1>

contacts() function

1. Prikažite HXML šablon u XML odgovor.

Sa ovim promenama u contacts()prikazu, konačno možemo videti plodove našeg rada. Nakon ponovnog
pokretanja bekenda i osvežavanja ekrana u našoj mobilnoj aplikaciji, možemo videti ekran sa kontaktima!






Ekran kontakata

Pretraga kontakata

Za sada imamo mobilnu aplikaciju koja prikazuje ekran sa listom kontakata. Ali naš korisnički interfejs ne
podržava nikakve interakcije. Unos upita u polje za pretragu ne filtrira listu kontakata. Hajde da dodamo
ponašanje polju za pretragu kako bismo implementirali interakciju pretrage tokom kucanja. Ovo zahteva
proširivanje <text-field>da bi se dodao <behavior>element.

<text-field name="q" value="" placeholder="Search..."
style="search-field">

  <behavior
trigger="change" <1>

    action="replace-inner" <2>
    target="contacts-list" <3>
    href="/contacts?rows_only=true" <4>
    verb="get" <5>
  />
</text-field>

Isečakhv/index.xml

1. Ovo ponašanje će se aktivirati kada se vrednost tekstualnog polja promeni.

2. Kada se ponašanje pokrene, akcija će zameniti sadržaj unutar ciljnog elementa.

3. Cilj akcije je element sa ID-jem contacts-list.

4. Zamenski sadržaj će biti preuzet sa ove URL putanje.

5. Zamenski sadržaj će biti preuzet GETHTTP metodom.

Prvo što ćete primetiti je da smo promenili tekstualno polje sa samozatvarajuće oznake ( <text-field />) na
korišćenje početnih i zatvarajućih oznaka ( <text-field>…</text-field>). Ovo nam omogućava da dodamo
podređeni <behavior>element za definisanje interakcije.

Atribut trigger="change"govori Hyperview-u da će promena vrednosti tekstualnog polja pokrenuti akciju. Svaki
put kada korisnik izmeni sadržaj tekstualnog polja dodavanjem ili brisanjem znakova, pokrenuće se akcija.

Preostali atributi elementa <behavior>definišu akciju. action="replace-inner"To znači da će akcija ažurirati
sadržaj na ekranu, zamenjujući HXML sadržaj elementa novim sadržajem. Da replace-innerbi to uradila,
potrebno je da znamo dve stvari: trenutni element na ekranu koji će biti ciljan akcijom i sadržaj koji će se
koristiti za zamenu. target="contacts-list"nam govori ID trenutnog elementa. Imajte na umu da smo postavili
id="contacts-list"na <list>element u index.xml. Dakle, kada korisnik unese upit za pretragu u tekstualno polje,
Hyperview će zameniti sadržaj <list>(grupe <item>elemenata) novim sadržajem ( <item>elementi koji
odgovaraju upitu za pretragu) primljenim u relativnom href odgovoru. Domen ovde se zaključuje iz domena
koji se koristi za preuzimanje ekrana. Imajte na umu da to hrefuključuje naš rows_onlyparametar upita; želimo
da odgovor uključuje samo redove, a ne ceo ekran.






Pretraga kontakata

To je sve što je potrebno da dodamo funkcionalnost pretrage dok kucate u našu mobilnu aplikaciju! Kako
korisnik kuca upit za pretragu, klijent će slati zahteve bekendu i zamenjivati listu rezultatima pretrage. Možda
se pitate, kako bekend zna koji upit treba da koristi? Atribut hrefu ponašanju ne uključuje qparametar koji
očekuje naš bekend. Ali zapamtite, u index.xml, obmotali smo elemente <text-field>i <list>sa <form>elementom
. <form>Element definiše grupu unosa koji će biti serijalizovani i uključeni u sve HTTP zahteve koje pokreću
njegovi podređeni elementi. U ovom slučaju, element <form>okružuje ponašanje pretrage i tekstualno polje.
Dakle, vrednost <text-field>će biti uključena u naš HTTP zahtev za rezultate pretrage. Pošto šaljemo GETzahtev,
ime i vrednost tekstualnog polja će biti serijalizovani kao parametar upita. Svi postojeći parametri upita na
hrefće biti sačuvani. To znači da stvarni HTTP zahtev našem bekendu izgleda kao GET /contacts?
rows_only=true&q=Car. Naš bekend već podržava qparametar za pretragu, tako da će odgovor uključivati redove
koji se podudaraju sa nizom "Automobil".

Beskonačno skrolovanje

Ako korisnik ima stotine ili hiljade kontakata, njihovo istovremeno učitavanje može dovesti do loših
performansi aplikacije. Zato većina mobilnih aplikacija sa dugim listama implementira interakciju poznatu kao
"beskonačno skrolovanje". Aplikacija učitava fiksni broj početnih stavki na listi, recimo 100 stavki. Ako korisnik
skroluje do dna liste, vidi rotirajući alat koji ukazuje da se učitava još sadržaja. Kada je sadržaj dostupan,
rotirajući alat se zamenjuje sledećom stranicom od 100 stavki. Ovi artikli se dodaju na listu, a ne zamenjuju
prvi skup stavki. Dakle, lista sada sadrži 200 stavki. Ako korisnik ponovo skroluje do dna liste, videće drugi
rotirajući alat i aplikacija će učitati sledeći skup sadržaja. Beskonačno skrolovanje poboljšava performanse
aplikacije na dva načina:

- Početni zahtev za 100 artikala biće obrađen brzo, sa predvidljivim kašnjenjem.

- Naknadni zahtevi takođe mogu biti brzi i predvidljivi.

- Ako korisnik ne skroluje do dna liste, aplikacija neće morati da šalje naknadne zahteve.

Naš Flask bekend već podržava paginaciju na /contactskrajnjoj tački putem pageparametra upita. Samo treba
da izmenimo naše HXML šablone da bismo koristili ovaj parametar. Da bismo to uradili, hajde rows.xmlda
dodamo novi <item>ispod Jinja for-loop-a:

<items xmlns="https://hyperview.org/hyperview">
  {% for contact in contacts %}
    <item key="{{ contact.id }}" style="contact-item">

<!-- omitted for brevity -->
    </item>
  {% endfor %}
  {% if contacts|length > 0 %}
    <item key="load-more" id="load-more" style="load-more-item"> <1>
      <behavior

trigger="visible" <2>
        action="replace" <3>
        target="load-more" <4>
        href="/contacts?rows_only=true&page={{ page + 1 }}" <5>
        verb="get"
      />
      <spinner /> <6>
    </item>
  {% endif %}
</items>

Isečakhv/rows.xml

1. Uključite dodatni element <item>u listu da biste prikazali spiner.



2. Ponašanje stavke se aktivira kada je vidljiva u prikaznom prozoru.

3. Kada se pokrene, ponašanje će zameniti element na ekranu.

4. Element koji treba zameniti je sama stavka (ID load-more).

5. Zamenite stavku sledećom stranicom sadržaja.

6. Element spinera.

Ako je trenutna lista kontakata prosleđenih šablonu prazna, možemo pretpostaviti da nema više kontakata za
preuzimanje iz bekenda. Zato koristimo DŽindža uslov da bismo uključili ovaj novi element samo <item>ako
lista kontakata nije prazna. Ovaj novi <item>element dobija ID i ponašanje. Ponašanje definiše interakciju
beskonačnog skrolovanja.

Do sada smo videli triggervrednosti changei refresh. Ali da bismo implementirali beskonačno skrolovanje,
potreban nam je način da pokrenemo akciju kada korisnik skroluje do dna liste. Okidač visiblese može koristiti
upravo u ovu svrhu. Pokrenuće akciju kada je element sa tim ponašanjem vidljiv u prikaznom prozoru uređaja.
U ovom slučaju, novi <item>element je poslednja stavka na listi, tako da će se akcija pokrenuti kada korisnik
skroluje nadole dovoljno daleko da stavka uđe u prikazni prozor. Čim stavka postane vidljiva, akcija će poslati
HTTP GET zahtev i zameniti <item>element za učitavanje sadržajem odgovora.

Imajte na umu da naš href mora da sadrži rows_only=trueparametar upita, tako da će naš odgovor sadržati samo
HXML za stavke kontakata, a ne ceo ekran. Takođe, prosleđujemo pageparametar upita, povećavajući broj
trenutne stranice kako bismo osigurali učitavanje sledeće stranice.

Šta se dešava kada postoji više od jedne stranice sa stavke? Početni ekran će sadržati prvih 100 stavki, plus
stavku "učitaj još" na dnu. Kada korisnik pomeri do dna ekrana, Hiperpogled će zahtevati drugu stranicu stavki
( &page=2) i zameniti stavku "učitaj još" novim stavkama. Ali ova druga stranica stavki će sadržati novu stavku
"učitaj još". Dakle, kada korisnik pomeri kroz sve stavke sa druge stranice, Hiperpogled će ponovo zahtevati još
stavki ( &page=3). I još jednom, stavka "učitaj još" će biti zamenjena novim stavkama. Ovo će se nastaviti sve dok
se sve stavke ne učitaju na ekran. U tom trenutku, više neće biti kontakata za vraćanje, odgovor neće sadržati
drugu stavku "učitaj još" i naše paginiranje je završeno.

Osvežavanje povlačenjem

Osvežavanje povlačenjem je uobičajena interakcija u mobilnim aplikacijama, posebno na ekranima sa
dinamičkim sadržajem. Funkcioniše ovako: Na vrhu prikaza za skrolovanje, korisnik povlači sadržaj koji se
skroluje nadole gestom prevlačenja nadole. Ovo otkriva rotirajući alat "ispod" sadržaja. Povlačenje sadržaja
dovoljno nadole pokrenuće osvežavanje. Dok se sadržaj osvežava, rotirajući alat ostaje vidljiv na ekranu, što
ukazuje korisniku da se radnja još uvek odvija. Kada se sadržaj osveži, sadržaj se vraća u podrazumevani
položaj, skrivajući rotirajući alat i obaveštavajući korisnika da je interakcija završena.



Osvežavanje povlačenjem

Ovaj obrazac je toliko čest i koristan da je ugrađen u Hyperview putem refreshakcije. Dodajmo opciju "povuci
za osvežavanje" našoj listi kontakata da bismo je videli u akciji.

<list id="contacts-list"
trigger="refresh" <1>

  action="replace-inner" <2>
  target="contacts-list" <3>
  href="/contacts?rows_only=true" <4>
  verb="get" <5>
>
  {% include 'hv/rows.xml' %}
</list>

Isečakhv/index.xml

1. Ovo ponašanje će se aktivirati kada korisnik izvrši gest "povuci za osvežavanje".

2. Kada se ponašanje pokrene, ova radnja će zameniti sadržaj unutar ciljnog elementa.

3. Cilj akcije je <list>sam element.

4. Zamenski sadržaj će biti preuzet sa ove URL putanje.

5. Zamenski sadržaj će biti preuzet GETHTTP metodom.

Primetićete nešto neobično u gornjem isečku: umesto da dodamo <behavior>element u <list>, dodali smo
atribute ponašanja direktno elementu <list>. Ovo je skraćena notacija koja je ponekad korisna za određivanje
pojedinačnih ponašanja elementa. To je ekvivalentno dodavanju <behavior>elementa u <list>sa istim
atributima.

Pa zašto smo ovde koristili skraćenu sintaksu? Ima veze sa akcijom, replace-inner. Zapamtite, ova akcija
zamenjuje sve podređene elemente cilja novim sadržajem. Ovo uključuje <behavior>i elemente! Recimo da
<list>je naš sadržao <behavior>. Ako je korisnik izvršio pretragu ili povlačenje radi osvežavanja, zamenili bismo
sadržaj <list>sa sadržajem iz rows.xml. <behavior>više ne bi bio definisan na <list>, a naknadni pokušaji



povlačenja radi osvežavanja ne bi funkcionisali. Definisanjem ponašanja kao atributa <list>, ponašanje će se
zadržati čak i kada se zamene stavke na listi. Generalno, više volimo da koristimo eksplicitne
<behavior>elemente u HXML-u. To olakšava definisanje višestrukih ponašanja i pomeranje ponašanja tokom
refaktorisanja. Ali skraćena sintaksa je dobra za primenu u situacijama poput ove.

Pregled detalja kontakta

Sada kada je naš ekran sa listom kontakata u dobrom stanju, možemo početi sa dodavanjem drugih ekrana u
našu aplikaciju. Sledeći prirodni korak je kreiranje ekrana sa detaljima, koji se pojavljuje kada korisnik dodirne
stavku na listi kontakata. Hajde da ažuriramo šablon koji prikazuje elemente kontakta <item>i dodamo
ponašanje za prikazivanje ekrana sa detaljima.

<items xmlns="https://hyperview.org/hyperview">
  {% for contact in contacts %}
    <item key="{{ contact.id }}" style="contact-item">
      <behavior trigger="press" action="push"

href="/contacts/{{ contact.id }}" /> <1>
      <text style="contact-item-label">

<!-- omitted for brevity -->
      </text>
    </item>
  {% endfor %}
</items>

hv/rows.xml

1. Ponašanje koje pomera ekran sa detaljima kontakta na stek kada se pritisne.

Naš Flask bekend već ima rutu za prikazivanje kontakt podataka na /contacts/<contact_id>. U našem šablonu
koristimo Jinja promenljivu za dinamičko generisanje URL putanje za trenutni kontakt u for-petlji. Takođe
smo koristili akciju "push" da bismo prikazali detalje dodavanjem novog ekrana na stek. Ako ponovo učitate
aplikaciju, sada možete dodirnuti bilo koji kontakt na listi, a Hyperview će otvoriti novi ekran. Međutim, novi
ekran će prikazati poruku o grešci. To je zato što naš bekend i dalje vraća HTML u odgovoru, a Hyperview
klijent očekuje HXML. Hajde da ažuriramo bekend da odgovori sa HXML-om i odgovarajućim zaglavljima.

@app.route("/contacts/<contact_id>")
def contacts_view(contact_id=0):
    contact = Contact.find(contact_id)

return render_to_response("hv/show.xml", contact=contact) <1>

app.py

1. Generišite XML odgovor iz nove datoteke šablona.

Baš kao i contacts()prikaz, contacts_view()koristi se render_to_response()za podešavanje Content-Typezaglavlja
odgovora. Takođe generišemo odgovor iz novog HXML šablona, koji sada možemo da kreiramo:

{% extends 'hv/layout.xml' %} <1>

{% block header %} <2>
  <text style="header-button">
    <behavior trigger="press" action="back" /> <3>
    Back
  </text>
{% endblock %}

{% block content %} <4>
<view style="details">
  <text style="contact-name">
    {{ contact.first }} {{ contact.last }}
  </text>

  <view style="contact-section">
    <text style="contact-section-label">Phone</text>
    <text style="contact-section-info">{{contact.phone}}</text>
  </view>

  <view style="contact-section">
    <text style="contact-section-label">Email</text>
    <text style="contact-section-info">{{contact.email}}</text>
  </view>
</view>



{% endblock %}

hv/show.xml

1. Proširite osnovni šablon rasporeda.

2. Preimenite headerblok šablona rasporeda da biste uključili dugme "Nazad".

3. Ponašanje za prelazak na prethodni ekran kada se pritisne.

4. Zamenite contentblok da biste prikazali sve detalje izabranog kontakta.

Ekran sa detaljima kontakata proširuje osnovni layout.xmlšablon, baš kao što smo uradili u index.xml. Ovog
puta, zamenjujemo sadržaj i u headerbloku i u contentbloku. Zamenjivanje bloka zaglavlja nam omogućava da
dodamo dugme "Nazad" sa određenim ponašanjem. Kada se pritisne, Hyperview klijent će vratiti korisnika na
listu kontakata.

Imajte na umu da pokretanje ovog ponašanja nije jedini način za navigaciju unazad. Hyperview klijent poštuje
konvencije navigacije na različitim platformama. Na iOS-u, korisnici takođe mogu da se vrate na prethodni
ekran prevlačenjem prstom udesno sa leve ivice uređaja. Na Android-u, korisnici takođe mogu da se vrate na
prethodni ekran pritiskom na dugme za nazad na hardveru. Ne moramo ništa dodatno da navodimo u HXML-u
da bismo dobili ove interakcije.

Ekran sa detaljima kontakta

Sa samo nekoliko jednostavnih promena, prešli smo sa aplikacije za jedan ekran na aplikaciju za više ekrana.
Imajte na umu da nismo morali ništa da menjamo u samom kodu mobilne aplikacije da bismo podržali naš
novi ekran. Ovo je velika stvar. U tradicionalnom razvoju mobilnih aplikacija, dodavanje ekrana može biti
značajan zadatak. Programeri treba da kreiraju novi ekran, ubace ga na odgovarajuće mesto u hijerarhiji



navigacije i napišu kod za otvaranje novog ekrana sa postojećih ekrana. U Hipervjuu smo samo dodali
ponašanje sa action="push".

Za sada, naša aplikacija nam omogućava da pregledamo listu kontakata i vidimo detalje određenog kontakta.
Zar ne bi bilo lepo ažurirati ime, broj telefona ili imejl adresu kontakta? Dodajmo korisnički interfejs za
uređivanje kontakata kao sledeće poboljšanje.

Prvo moramo da shvatimo kako želimo da prikažemo korisnički interfejs za uređivanje. Mogli bismo da
dodamo novi ekran za uređivanje na stek, na isti način na koji smo dodali ekran sa detaljima kontakta. Ali to
nije najbolji dizajn sa stanovišta korisničkog iskustva. Dodavanje novih ekrana ima smisla kada se podaci
detaljnije analiziraju, kao što je prelazak sa liste na jednu stavku. Ali uređivanje nije interakcija "detaljnije
analize", već prebacivanje između pregleda i uređivanja. Dakle, umesto dodavanja novog ekrana, hajde da
zamenimo trenutni ekran korisničkim interfejsom za uređivanje. To znači da treba da dodamo dugme i
ponašanje koje koristi tu reloadradnju. Ovo dugme se može dodati u zaglavlje ekrana sa detaljima kontakta.

{% block header %}
  <text style="header-button">
    <behavior trigger="press" action="back" />
    Back
  </text>

  <text style="header-button"> <1>
    <behavior trigger="press" action="reload"

href="/contacts/{{contact.id}}/edit" /> <2>
    Edit
  </text>
{% endblock %}

Isečakhv/show.xml

1. Novo dugme "Izmeni".

2. Ponašanje koje ponovo učitava trenutni ekran sa ekranom za uređivanje kada se pritisne.

Još jednom, ponovo koristimo postojeću Flask rutu ( /contacts/<contact_id>/edit) za korisnički interfejs za
uređivanje i popunjavamo ID kontakta koristeći podatke prosleđene šablonu Jinja. Takođe treba da ažuriramo
prikaz contacts_edit_get()da bi vraćao XML odgovor zasnovan na HXML šablonu ( hv/edit.xml). Preskočićemo
primer koda jer su potrebne izmene identične onima koje smo primenili contacts_view()u prethodnom odeljku.
Umesto toga, fokusirajmo se na šablon za ekran za uređivanje.

{% extends 'hv/layout.xml' %}

{% block header %}
  <text style="header-button">
    <behavior trigger="press" action="back" href="#" />
    Back
  </text>
{% endblock %}

{% block content %}
<form> <1>
  <view id="form-fields"> <2>
    {% include 'hv/form_fields.xml' %} <3>
  </view>

  <view style="button"> <4>
    <behavior

trigger="press"
action="replace-inner"
target="form-fields"
href="/contacts/{{contact.id}}/edit"
verb="post"

    />
    <text style="button-label">Save</text>
  </view>
</form>
{% endblock %}

hv/edit.xml

1. Forma koja obuhvata polja za unos i dugmad.



2. Kontejner sa ID-om, koji sadrži polja za unos.

3. Šablon uključuje za prikazivanje polja za unos.

4. Dugme za slanje podataka obrasca i ažuriranje kontejnera polja za unos.

Pošto ekran za uređivanje treba da šalje podatke bekendu, ceo odeljak sadržaja obuhvatamo elementom <form>.
Ovo osigurava da će podaci iz polja obrasca biti uključeni u HTTP zahteve ka našem bekendu. Unutar elementa
<form>, naš korisnički interfejs je podeljen na dva dela: polja obrasca i dugme "Sačuvaj". Stvarna polja obrasca
su definisana u posebnom šablonu ( form_fields.xml) i dodaju se na ekran za uređivanje pomoću oznake
"inkluziv" u DŽindži.

<view style="edit-group">
  <view style="edit-field">
    <text-field name="first_name" placeholder="First name"

value="{{ contact.first }}" /> <1>
    <text style="edit-field-error">{{ contact.errors.first }}</text> <2>
  </view>

  <view style="edit-field"> <3>
    <text-field name="last_name" placeholder="Last name"

value="{{ contact.last }}" />
    <text style="edit-field-error">{{ contact.errors.last }}</text>
  </view>

<!-- same markup for contact.email and contact.phone -->
</view>

hv/form_fields.xml

1. Unos teksta koji sadrži trenutnu vrednost za ime kontakta.

2. Tekstualni element koji bi mogao da prikaže greške iz modela kontakta.

3. Još jedno tekstualno polje, ovaj put za prezime kontakta.

Izostavili smo kod za broj telefona i adresu e-pošte kontakta, jer prate isti obrazac kao ime i prezime. Svako
polje kontakta ima svoj <text-field>i <text>element ispod njega za prikaz mogućih grešaka. <text-field>ima
dva važna atributa:

- namedefiniše ime koje se koristi prilikom serijalizacije vrednosti tekstualnog polja u podatke forme za
HTTP zahteve. Koristimo ista imena kao i veb aplikacija iz prethodnih poglavlja ( first_name, last_name,
phone, email). Na taj način, ne moramo da pravimo izmene u našem bekendu da bismo analizirali podatke
forme.

- valuedefiniše unapred popunjene podatke u tekstualnom polju. Pošto uređujemo postojeći kontakt, ima
smisla unapred popuniti tekstualno polje trenutnim imenom, telefonom ili imejl adresom.

Možda se pitate zašto smo odlučili da definišemo polja forme u posebnom šablonu ( form_fields.xml)? Da bismo
razumeli tu odluku, prvo moramo da razgovaramo o dugmetu "Sačuvaj". Kada se pritisne, Hyperview klijent će
poslati HTTP POSTzahtev ka contacts/<contact_id>/edit, sa podacima forme serijalizovanim iz <text-field>ulaza.
HXML odgovor će zameniti sadržaj kontejnera polja forme (ID form-fields). Ali kakav bi trebalo da bude taj
odgovor? To zavisi od validnosti podataka forme:

1. Ako su podaci nevažeći (npr. duplirana imejl adresa), naš korisnički interfejs će ostati u režimu
uređivanja i prikazivati poruke o greškama u nevažećim poljima. Ovo omogućava korisniku da ispravi
greške i ponovo pokuša da sačuva podatke.

2. Ako su podaci validni, naš bekend će sačuvati izmene, a naš korisnički interfejs će se vratiti u režim
prikaza (korisnički interfejs za kontakt podatke).

Dakle, naš bekend mora da razlikuje validnu i nevažeću izmenu. Da bismo podržali ova dva scenarija,
napravićemo neke izmene u postojećem contacts_edit_post()prikazu u Flask aplikaciji.

@app.route("/contacts/<contact_id>/edit", methods=["POST"])
def contacts_edit_post(contact_id=0):
    c = Contact.find(contact_id)
    c.update(



      request.form['first_name'],
      request.form['last_name'],
      request.form['phone'],
      request.form['email']) <1>

if c.save(): <2>
        flash("Updated Contact!")

return render_to_response("hv/form_fields.xml",
          contact=c, saved=True) <3>

else:
return render_to_response("hv/form_fields.xml", contact=c) <4>

app.py

1. Ažurirajte objekat kontakta iz podataka obrasca zahteva.

2. Pokušaj da se ažuriranja sačuvaju. Ovo vraća grešku Falseza nevažeće podatke.

3. U slučaju uspeha, prikaži šablon polja obrasca i prosledi savedzastavicu šablonu

4. U slučaju neuspeha, prikaži šablon polja obrasca. Poruke o grešci su prisutne na objektu kontakta.

Ovaj prikaz već sadrži uslovnu logiku zasnovanu na tome da li je model kontakta save()uspešan. Ako save()ne
uspe, prikazujemo form_fields.xmlšablon. contact.errorsSadržaće poruke o greškama za nevažeća polja, koja će
biti prikazana u <text style="edit-field-error">elemente. Ako save()uspe, takođe ćemo prikazivati
form_fields.xmlšablon. Ali ovog puta, šablon će dobiti savedzastavicu, što ukazuje na uspeh. Ažuriraćemo šablon
da koristi ovu zastavicu za implementaciju našeg željenog korisničkog interfejsa: vraćanje korisničkog interfejsa
u režim prikaza.

<view style="edit-group">
  {% if saved %} <1>
    <behavior

trigger="load" <2>
      action="reload" <3>
      href="/contacts/{{contact.id}}" <4>
    />
  {% endif %}

  <view style="edit-field">
    <text-field name="first_name" placeholder="First name"

value="{{ contact.first }}" />
    <text style="edit-field-error">{{ contact.errors.first }}</text>
  </view>

<!-- same markup for the other fields -->
</view>

hv/form_fields.xml

1. Uključite ovo ponašanje tek nakon uspešnog čuvanja kontakta.

2. Odmah pokrenite ponašanje.

3. Ponašanje će ponovo učitati ceo ekran.

4. Ekran će se ponovo učitati ekranom sa podacima o kontaktu.

Uslov šablona Jinja osigurava da se naše ponašanje prikazuje samo nakon uspešnih čuvanja, a ne kada se ekran
prvi put otvori (ili kada korisnik pošalje nevažeće podatke). U slučaju uspeha, šablon uključuje ponašanje koje
se odmah pokreće zahvaljujući trigger="load". Akcija ponovo učitava trenutni ekran sa ekranom sa podacima o
kontaktu (iz /contacts/<contact_id>rute).

Rezultat? Kada korisnik klikne na "Sačuvaj", naš bekend čuva nove kontakt podatke, a ekran se vraća na ekran
sa detaljima. Pošto će aplikacija napraviti novi HTTP zahtev za dobijanje kontakt podataka, garantovano je da
će prikazati sveže sačuvane izmene.

Sada imamo funkcionalan korisnički interfejs za uređivanje u našoj aplikaciji za kontakte. Korisnici mogu da
uđu u režim uređivanja pritiskom na dugme na ekranu sa detaljima kontakta. U režimu uređivanja mogu da
ažuriraju podatke kontakta i sačuvaju ih u serverskoj službi. Ako serverska služba odbije izmene kao nevažeće,
aplikacija ostaje u režimu uređivanja i prikazuje greške validacije. Ako serverska služba prihvati i zadrži
izmene, aplikacija će se vratiti u režim detalja, prikazujući ažurirane podatke kontakta.



Dodajmo još jedno poboljšanje korisničkom interfejsu za uređivanje. Bilo bi lepo kada bismo omogućili
korisniku da pređe iz režima uređivanja bez potrebe za čuvanjem kontakta. To se obično radi pružanjem akcije
"Otkaži". Možemo je dodati kao novo dugme ispod dugmeta "Sačuvaj".

<view style="button">
  <behavior trigger="press" action="replace-inner"target="form-fields"

href="/contacts/{{contact.id}}/edit" verb="post" />
  <text style="button-label">Save</text>
</view>
<view style="button"> <1>
  <behavior

trigger="press"
action="reload" <2>

    href="/contacts/{{contact.id}}" <3>
  />
  <text style="button-label">Cancel</text>
</view>

Isečakhv/edit.xml

1. Novo dugme Otkaži na ekranu za uređivanje.

2. Kada se pritisne, ponovo učitava ceo ekran.

3. Ekran će se ponovo učitati ekranom sa podacima o kontaktu.

Ovo je ista tehnika koju smo koristili za prelazak sa korisničkog interfejsa za uređivanje na korisnički interfejs
sa detaljima nakon uspešnog uređivanja kontakta. Ali pritiskom na "Otkaži" korisnički interfejs će se ažurirati
brže nego pritiskom na "Sačuvaj". Prilikom čuvanja, aplikacija će prvo podneti zahtev POSTza čuvanje podataka,
a zatim GETzahtev za ekran sa detaljima. Otkazivanjem se preskače POSTi odmah se podnosi GETzahtev.






Ekran za uređivanje kontakata

Ažuriranje liste kontakata

U ovom trenutku, možemo tvrditi da smo u potpunosti implementirali korisnički interfejs za uređivanje. Ali
postoji problem. U stvari, ako bismo se ovde zaustavili, korisnici bi čak mogli smatrati da aplikacija ima greške!
Zašto? To ima veze sa sinhronizacijom stanja aplikacije na više ekrana. Hajde da prođemo kroz ovaj niz
interakcija:

1. Pokrenite aplikaciju na listi kontakata.

2. Pritisnite na kontakt "DŽo Blou" da biste učitali njegove kontakt podatke.

3. Pritisnite Uredi da biste prešli u režim uređivanja i promenite ime kontakta u "DŽozef".

4. Pritisnite Sačuvaj da biste se vratili u režim pregleda. Ime kontakta je sada "DŽozef Blou".

5. Pritisnite dugme za nazad da biste se vratili na listu kontakata.

Da li ste primetili problem? Naša lista kontakata i dalje prikazuje istu listu imena kao kada smo pokrenuli
aplikaciju. Kontakt koji smo upravo preimenovali u "DŽozef" se i dalje prikazuje na listi kao "DŽo". Ovo je opšti
problem u hipermedijalnim aplikacijama. Klijent nema predstavu o deljenim podacima između različitih delova
korisničkog interfejsa. Ažuriranja u jednom delu aplikacije neće automatski ažurirati druge delove aplikacije.

Srećom, postoji rešenje za ovaj problem u Hyperview-u: događaji. Događaji su ugrađeni u sistem ponašanja i
omogućavaju jednostavnu komunikaciju između različitih delova korisničkog interfejsa.

Sada znamo dovoljno o sistemu događaja Hyperview-a da bismo rešili grešku u našoj aplikaciji. Kada korisnik
sačuva promenu kontakta, potrebno je da pokrenemo događaj sa ekrana sa detaljima. A ekran sa kontaktima
mora da osluškuje taj događaj i da se ponovo učita kako bi odražavao izmene. Pošto šablon form_fields.xmlveć
dobija savedzastavicu kada bekend uspešno sačuva kontakt, to je dobro mesto za pokretanje događaja:

{% if saved %}
  <behavior

trigger="load" <1>
    action="dispatch-event" <2>
    event-name="contact-updated" <2>
  />
  <behavior <4>
    trigger="load"
    action="reload"
    href="/contacts/{{contact.id}}"
  />
{% endif %}

Isečak izhv/form_fields.xml

1. Odmah pokrenite ponašanje.

2. Ponašanje će poslati događaj.

3. Naziv događaja je "ažuriran kontakt".

4. Postojeće ponašanje za prikaz korisničkog interfejsa sa detaljima.

Sada nam je samo potrebno da lista kontakata osluškuje događaj contact-updatedi ponovo se učita:

<form>
  <behavior



trigger="on-event" <1>
    event-name="contact-updated" <2>
    action="replace-inner" <3>
    target="contacts-list"
    href="/contacts?rows_only=true"
    verb="get"
  />
<!-- text-field omitted -->

  <list id="contacts-list">
    {% include 'hv/rows.xml' %}
  </list>
</form>

Isečak izhv/index.xml

1. Pokrenite ponašanje pri otpremi događaja.

2. Pokrenite ponašanje za otpremljene događaje sa nazivom "contact-updated".

3. Kada se pokrene, zameni sadržaj elementa <list>redovima iz bekenda.

Svaki put kada korisnik izmeni kontakt, ekran sa listom kontakata će se ažurirati kako bi odražavao izmene.
Dodavanje ova dva <behavior>elementa ispravlja grešku: ekran sa listom kontakata će ispravno prikazivati
"DŽozef Blou" na listi. Imajte na umu da smo namerno dodali novo ponašanje unutar elementa <form>. Ovo
osigurava da će pokrenuti zahtev sačuvati svaki upit za pretragu.

Da bismo pokazali šta mislimo, ponovo ćemo se pozabaviti skupom koraka koji su demonstrirali ponašanje sa
greškama. Pretpostavimo da je pre nego što je pritisnuo "DŽo Blou", korisnik pretražio kontakte tako što je u
polje za pretragu ukucao "DŽo". Kada korisnik kasnije ažurira kontakt na "DŽozef Blou", naš šablon šalje
događaj "contact-updated", koji pokreće ponašanje replace-innerna ekranu liste kontakata. Zbog
<form>elementa, upit za pretragu "DŽo" će biti serijalizovan sa zahtevom: GET /contacts?rows_only=true&q=Joe.
Pošto se ime "DŽozef" ne podudara sa upitom "DŽo", kontakt koji smo izmenili neće se pojaviti na listi (dok
korisnik ne obriše upit). Stanje naše aplikacije ostaje konzistentno na našem bekendu i svim aktivnim
ekranima.

Događaji uvode nivo apstrakcije u ponašanja. Do sada smo videli da uređivanje kontakta dovodi do osvežavanja
liste kontakata. Ali lista kontakata bi trebalo da se osveži i nakon drugih radnji, kao što je brisanje kontakta ili
dodavanje novog kontakta. Sve dok naši HXML odgovori za brisanje ili kreiranje uključuju ponašanje za slanje
contact-updateddogađaja, dobićemo željeno ponašanje osvežavanja na ekranu liste kontakata.

Ekran ne mari šta uzrokuje contact-updatedpokretanje događaja. On samo zna šta treba da uradi kada se to desi.

Kad smo već kod brisanja kontakta, ovo je dobra sledeća funkcija koju treba implementirati. Omogućićemo
korisnicima da obrišu kontakt iz korisničkog interfejsa za uređivanje. Dakle, hajde da dodamo novo dugme u
edit.xml.

<view style="button">
  <behavior trigger="press" action="replace-inner" target="form-fields"

href="/contacts/{{contact.id}}/edit" verb="post" />
  <text style="button-label">Save</text>
</view>
<view style="button">
  <behavior trigger="press" action="reload"

href="/contacts/{{contact.id}}" />
  <text style="button-label">Cancel</text>
</view>
<view style="button"> <1>
  <behavior

trigger="press"
action="append" <2>

    target="form-fields"
    href="/contacts/{{contact.id}}/delete" <3>
    verb="post"
  />
  <text style="button-label button-label-delete">Delete Contact</text>
</view>

Isečakhv/edit.xml

1. Novo dugme za brisanje kontakta na ekranu za uređivanje.



2. Kada se pritisne, dodaje HXML kontejneru na ekranu.

3. HXML će biti preuzet slanjem POST /contacts/<contact_id>/deletezahteva.

HXML za dugme "Izbriši" je prilično sličan dugmetu "Sačuvaj", ali postoji nekoliko suptilnih razlika. Zapamtite,
pritiskom na dugme "Sačuvaj" rezultira jedan od dva očekivana ishoda: neuspeh i prikazivanje grešaka
validacije na obrascu ili uspeh i prelazak na ekran sa detaljima kontakta. Da bi se podržao prvi ishod (neuspeh i
prikazivanje grešaka validacije), ponašanje čuvanja zamenjuje sadržaj kontejnera <view id="form-
fields">ponovo prikazanom verzijom form_fields.xml. Stoga, korišćenje replace-innerakcije ima smisla.

Brisanje ne uključuje korak validacije, tako da postoji samo jedan očekivani ishod: uspešno brisanje kontakta.
Kada brisanje uspe, kontakt više ne postoji. Nema smisla prikazivati korisnički interfejs za uređivanje ili
kontakt detalje za nepostojeći kontakt. Umesto toga, naša aplikacija će se vratiti na prethodni ekran (listu
kontakata). Naš odgovor će uključivati samo ponašanja koja se odmah pokreću, nema korisničkog interfejsa za
promenu. Stoga, korišćenje akcije appendće sačuvati trenutni korisnički interfejs dok Hyperview pokreće akcije.

<view>
  <behavior trigger="load" action="dispatch-event"

event-name="contact-updated" /> <1>
  <behavior trigger="load" action="back" /> <2>
</view>

Isečakhv/deleted.xml

1. Prilikom učitavanja, pošaljite contact-updateddogađaj da biste ažurirali ekran sa listama kontakata.

2. Vratite se na ekran sa listom kontakata.

Imajte na umu da pored ponašanja za vraćanje unazad, ovaj šablon takođe uključuje ponašanje za slanje
contact-updateddogađaja. U prethodnom odeljku poglavlja, dodali smo ponašanje za index.xmlosvežavanje liste
kada se taj događaj pošalje. Slanjem događaja nakon brisanja, osiguraćemo da se obrisani kontakt ukloni sa
liste.

Još jednom, preskočićemo izmene u Flask backend-u. Dovoljno je reći da ćemo morati da ažuriramo prikaz
contacts_delete()da bi odgovorio šablonom hv/deleted.xml. I moramo da ažuriramo rutu da bi podržavala
POSTpored DELETE, pošto Hyperview klijent razume samo GETi POST.

Sada imamo potpuno funkcionalnu funkciju brisanja! Ali nije baš najjednostavnija za korišćenje: potreban je
jedan slučajni dodir da bi se kontakt trajno izbrisao. Za destruktivne radnje poput brisanja kontakta, uvek je
dobra ideja pitati korisnika za potvrdu.

Možemo dodati potvrdu ponašanju brisanja korišćenjem alertsistemske akcije opisane u prethodnom
poglavlju. Kao što se sećate, akcija alertće prikazati sistemski dijaloški prozor sa dugmadima koja mogu
pokrenuti druga ponašanja. Sve što treba da uradimo je da brisanje umotamo <behavior>u ponašanje koje
koristi action="alert".

<view style="button">
  <behavior <1>
    xmlns:alert="https://hyperview.org/hyperview-alert"
    trigger="press"
    action="alert"
    alert:title="Confirm delete"
    alert:message="Are you sure you want to delete {{ contact.first }}?"
  >
    <alert:option alert:label="Confirm"> <2>
      <behavior <3>
        trigger="press"
        action="append"
        target="form-fields"
        href="/contacts/{{contact.id}}/delete"
        verb="post"
      />
    </alert:option>
    <alert:option alert:label="Cancel" /> <4>
  </behavior>
  <text style="button-label button-label-delete">Delete Contact</text>
</view>

Dugme za brisanje uhv/edit.xml



1. Pritiskom na dugme "Izbriši" pokreće se radnja za prikazivanje sistemskog dijaloga sa datim naslovom i
porukom.

2. Prva opcija koju možete pritisnuti u sistemskom dijalogu.

3. Pritiskom na prvu opciju pokrenućete brisanje kontakta.

4. Druga opcija koju je moguće pritisnuti nema nikakvo ponašanje, tako da samo zatvara dijalog.

Za razliku od ranije, pritiskanje dugmeta za brisanje neće imati trenutni efekat. Umesto toga, korisniku će biti
prikazan dijaloški prozor i biće zatraženo da potvrdi ili otkaže. Naše osnovno ponašanje brisanja se nije
promenilo, samo smo ga povezali sa drugim ponašanjem.

Potvrda brisanja kontakta

Dodavanje novog kontakta je poslednja funkcija koju želimo da podržimo u našoj mobilnoj aplikaciji. I srećom,
to je takođe i najlakše. Možemo ponovo koristiti koncepte (pa čak i neke šablone) iz funkcija koje smo već
implementirali. Konkretno, dodavanje novog kontakta je veoma slično uređivanju postojećeg kontakta. Obe
funkcije moraju:

- Prikažite obrazac za prikupljanje informacija o kontaktu.

- Imajte način da sačuvate unete podatke.

- Prikaži greške validacije na obrascu.

- Održavajte kontakt kada nema grešaka u validaciji.

Pošto je funkcionalnost toliko slična, ovde ćemo sumirati promene bez prikazivanja koda.

1. Ažuriranje index.xml.

◦ Preimenite headerblok da biste dodali novo dugme "Dodaj".

◦ Uključite ponašanje u dugme. Kada se pritisne, otvorite novi ekran kao modalni prozor koristeći
action="new"i zatražite sadržaj ekrana od /contacts/new.

2. Napravite šablon hv/new.xml.

◦ Preimenovati blok zaglavlja da biste uključili dugme koje zatvara modalni prozor, koristeći
action="close".



◦ Uključi hv/form_fields.xmlšablon za prikazivanje praznih polja obrasca

◦ Dodajte dugme "Dodaj kontakt" ispod polja obrasca.

◦ Uključite ponašanje u dugme. Kada se pritisne, pošaljite zahtev POSTi /contacts/newkoristite
action="replace-inner"za ažuriranje polja obrasca.

3. Ažurirajte prikaz Flask-a.

◦ Promeni contacts_new_get()da bi se koristio render_to_response()sa hv/new.xmlšablonom.

◦ Promena contacts_new()za upotrebu render_to_response()sa hv/form_fields.xmlšablonom. Prosledi
saved=Trueprilikom renderovanja šablona nakon uspešnog čuvanja novog kontakta.






Dodaj kontakt modalni prozor

Ponovnom upotrebom form_fields.xmlza uređivanje i dodavanje kontakta, ponovo koristimo deo koda i
osiguravamo da dve funkcije imaju konzistentan korisnički interfejs. Takođe, naš ekran "Dodaj kontakt" će
imati koristi od logike "sačuvano" koja je već deo form_fields.xml. Nakon uspešnog dodavanja novog kontakta,
ekran će poslati contact-updateddogađaj, koji će osvežiti listu kontakata i prikazati novododati kontakt. Ekran će
se ponovo učitati da bi prikazao detalje kontakta.

Implementacija aplikacije
Sa završetkom korisničkog interfejsa za kreiranje kontakata, imamo potpuno implementiranu mobilnu
aplikaciju. Ona podržava pretragu liste kontakata, pregled detalja kontakta, uređivanje i brisanje kontakta i
dodavanje novog kontakta. Ali do sada smo razvijali aplikaciju koristeći simulator na našem desktop računaru.
Kako možemo da je vidimo kako radi na mobilnom uređaju? I kako je možemo predstaviti našim korisnicima?

Da bismo videli kako aplikacija radi na fizičkom uređaju, iskoristimo funkcionalnost pregleda aplikacije na
Expo platformi.

1. Preuzmite aplikaciju Expo Go na Android ili iOS uređaj.

2. Ponovo pokrenite aplikaciju Flask, povezujući se sa interfejsom dostupnim na vašoj mreži. Ovo može
izgledati otprilike ovako flask run --host 192.168.7.229, gde je host IP adresa vašeg računara na mreži.

3. Ažurirajte Hyperview klijentski kod tako da ENTRY_POINT_URL(in demo/src/constants.js) ukazuje na IP
adresu i port na koji je Flask server povezan.

4. Nakon pokretanja yarn startdemo aplikacije Hyperview, videćete QR kod odštampan u konzoli, sa
uputstvima kako da ga skenirate na Androidu i iOS-u.

Kada skenirate QR kod, cela aplikacija će se pokrenuti na uređaju. Dok koristite aplikaciju, videćete HTTP
zahteve upućene Flask serveru. Možete čak koristiti i fizički uređaj tokom razvoja. Svaki put kada napravite
promenu u HXML-u, samo ponovo učitajte ekran da biste videli ažuriranja korisničkog interfejsa.

Dakle, aplikacija nam radi na fizičkom uređaju, ali još uvek nije spremna za produkciju. Da bismo aplikaciju
stavili u ruke naših korisnika, potrebno je da uradimo nekoliko stvari:

1. Rasporedimo naš bekend u produkciji. Potrebno nam je da koristimo veb server produkcijskog nivoa kao
što je Gunicorn umesto Flask razvojnog servera. Takođe, trebalo bi da pokrenemo našu aplikaciju na
mašini dostupnoj na internetu, najverovatnije koristeći cloud provajdera kao što su AWS ili Heroku.

2. Napravite samostalne binarne aplikacije. Prateći uputstva iz Expo projekta, možemo kreirati `.ipaor`
.apkdatoteku za iOS i Android platforme. Ne zaboravite da ažurirate ENTRY_POINT_URLHyperview klijent
tako da vodi ka produkcijskom bekendu.

3. Pošaljite naše binarne datoteke u iOS App Store ili Google Play Store i sačekajte odobrenje aplikacije.

Kada aplikacija bude odobrena, čestitamo! Našu mobilnu aplikaciju mogu preuzeti korisnici Androida i iOS-a.
A evo najboljeg dela: Pošto naša aplikacija koristi hipermedijalnu arhitekturu, možemo joj dodati funkcije
jednostavnim ažuriranjem bekenda. Korisnički interfejs i interakcije su u potpunosti definisani pomoću
HXML-a generisanog iz šablona na strani servera. Želite da dodate novi odeljak ekranu? Samo ažurirajte
postojeći HXML šablon. Želite da dodate novi tip ekrana aplikaciji? Napravite novi šablon za rutu, prikaz i
HXML. Zatim, postojećem ekranu dodajte ponašanje koje će otvoriti novi ekran. Da biste ove promene
prosledili svojim korisnicima, samo treba da ponovo instalirate bekend. Naša aplikacija zna kako da
interpretira HXML, i to je dovoljno da razume kako da rukuje novim funkcijama.



Da bismo kreirali mobilnu aplikaciju koristeći hipermedijalnu arhitekturu, počeli smo sa veb-baziranom
aplikacijom za kontakte i napravili nekoliko izmena, prvenstveno zamenjujući HTML šablone HXML
šablonima. Ali u procesu portiranja bekenda za našu mobilnu aplikaciju, izgubili smo funkcionalnost veb
aplikacije. Zaista, ako biste pokušali da posetite stranicu http://0.0.0.0:5000u veb pregledaču, videli biste
gomilu teksta i XML oznaka. To je zato što veb pregledači ne znaju kako da prikažu običan XML, a svakako ne
znaju kako da interpretiraju oznake i atribute HXML-a da bi prikazali aplikaciju. Šteta je, jer je Flask kod za veb
aplikaciju i mobilnu aplikaciju skoro identičan. Logika baze podataka i modela se dele, a većina prikaza je
takođe nepromenjena.

U ovom trenutku se sigurno pitate: da li je moguće koristiti isti bekend za opsluživanje i veb aplikacije i mobilne
aplikacije? Odgovor je da! U stvari, ovo je jedna od prednosti korišćenja hipermedijske arhitekture na više
platformi. Ne moramo da prenosimo nikakvu logiku na strani klijenta sa jedne platforme na drugu, samo treba
da odgovorimo na zahteve odgovarajućim hipermedijskim formatom. Da bismo to uradili, koristićemo
pregovaranje o sadržaju ugrađeno u HTTP.

Šta je pregovaranje o sadržaju?

Zamislite da neko ko govori nemački i neko ko govori japanski jezik posećuju sajt https://google.comu svom veb
pregledaču. Videće početnu stranicu Gugla lokalizovanu na nemački i japanski jezik, respektivno. Kako Gugl
zna da vrati drugačiju verziju početne stranice na osnovu željenog jezika korisnika? Odgovor leži u REST
arhitekturi i načinu na koji ona razdvaja koncepte resursa i reprezentacija.

U REST arhitekturi, početna stranica Google-a se smatra jednim "resursom", predstavljenim jedinstvenim
URL-om. Međutim, taj jedan resurs može imati više "reprezentacija". Reprezentacije su varijacije u načinu na
koji se sadržaj resursa predstavlja klijentu. Nemačka i japanska verzija početne stranice Google-a su dve
reprezentacije istog resursa. Da bi odredili najbolju reprezentaciju resursa za vraćanje, HTTP klijenti i serveri
učestvuju u procesu koji se naziva "pregovaranje sadržaja". To funkcioniše ovako:

- Klijenti određuju preferiranu reprezentaciju putem Accept-*zaglavlja zahteva.

- Server pokušava da što bolje pronađe preferiranu reprezentaciju i vraća izabranu reprezentaciju koristeći
Content-*.

U primeru početne stranice Gugla, govornik nemačkog jezika koristi pregledač koji je podešen da preferira
sadržaj lokalizovan za nemački jezik. Svaki HTTP zahtev koji napravi veb pregledač sadržaće zaglavlje Accept-
Language: de-DE. Server vidi zaglavlje zahteva i vratiće odgovor lokalizovan za nemački jezik (ako može). HTTP
odgovor će sadržati Content-Language: de-DEzaglavlje koje obaveštava klijenta o jeziku sadržaja odgovora.

Jezik je samo jedan faktor za predstavljanje resursa. Još važnije za nas, resursi se mogu predstaviti korišćenjem
različitih tipova sadržaja, kao što su HTML ili HXML. Pregovaranje o sadržaju o tipu sadržaja se vrši
korišćenjem Acceptzaglavlja zahteva i Content-Typezaglavlja odgovora. Veb pregledači se podešavaju text/
htmlkao preferirani tip sadržaja u Acceptzaglavlju. Hipervju klijent se podešava application/
vnd.hyperview+xmlkao preferirani tip sadržaja. Ovo našem bekendu daje način da razlikuje zahteve koji dolaze iz
veb pregledača ili Hipervju klijenta i da svakom od njih prikaže odgovarajući sadržaj.

Postoje dva glavna pristupa pregovaranju o sadržaju: finozrnasti i globalni.

Pristup 1: Prebacivanje šablona

Kada smo preneli aplikaciju Kontakti sa veba na mobilne uređaje, zadržali smo sve Flask prikaze, ali smo
napravili neke manje izmene. Konkretno, uveli smo novu funkciju render_to_response()i pozvali je u return
naredbi svakog prikaza. Evo funkcije ponovo da vas podsetimo:

def render_to_response(template_name, *args, **kwargs):
    content = render_template(template_name, *args, **kwargs)
    response = make_response(content)
    response.headers['Content-Type'] = 'application/vnd.hyperview+xml'

return response

app.py

render_to_response()prikazuje šablon sa datim kontekstom i pretvara ga u Flask objekat odgovora sa
odgovarajućim Hyperview Content-Typezaglavljem. Očigledno je da je implementacija veoma specifična za
serviranje naše Hyperview mobilne aplikacije. Ali možemo da izmenimo funkciju da vrši pregovaranje o



sadržaju na osnovu zaglavlja zahteva Accept:

HTML_MIME = 'text/html'
HXML_MIME = 'application/vnd.hyperview+xml'

def render_to_response(
    html_template_name, hxml_template_name, *args, **kwargs <1>
):
    response_type = request.accept_mimetypes.best_match(
      [HTML_MIME, HXML_MIME], default=HTML_MIME) <2>
    template_name =
      hxml_template_name

if response_type == HXML_MIME
else html_template_name <3>

    content = render_template(template_name, *args, **kwargs)
    response = make_response(content)
    response.headers['Content-Type'] = response_type <4>

return response

app.py

1. Potpis funkcije prihvata dva šablona, jedan za HTML i jedan za HXML.

2. Utvrdite da li klijent želi HTML ili HXML.

3. Izaberite šablon na osnovu najboljeg podudaranja sa klijentom.

4. Postavite Content-Typezaglavlje na osnovu najboljeg podudaranja za klijenta.

Flask-ov objekat zahteva otkriva accept_mimetypessvojstvo koje pomaže u pregovaranju o sadržaju.
Prosleđujemo naša dva MIME tipa sadržaja request.accept_mimetypes.best_match()i dobijamo nazad MIME tip
koji odgovara našem klijentu. Na osnovu najboljeg podudaranja MIME tipa, biramo da li ćemo prikazati HTML
šablon ili HXML šablon. Takođe se pobrinemo da zaglavlje podesimo Content-Typena odgovarajući MIME tip.
Jedina razlika u našim Flask prikazima je u tome što moramo da obezbedimo i HTML i HXML šablon:

@app.route("/contacts/<contact_id>")
def contacts_view(contact_id=0):
    contact = Contact.find(contact_id)

return render_to_response("show.html", "hv/show.xml",
      contact=contact)

app.py

- Prebacivanje šablona između HTML i HXML šablona, na osnovu klijenta.

Nakon ažuriranja svih Flask prikaza da bi podržali oba šablona, naš bekend će podržavati i veb pregledače i
našu mobilnu aplikaciju! Ova tehnika dobro funkcioniše za aplikaciju Kontakti jer se ekrani u mobilnoj
aplikaciji direktno mapiraju na stranice veb aplikacije. Svaka aplikacija ima posebnu stranicu (ili ekran) za
navođenje kontakata, prikazivanje i uređivanje detalja i kreiranje novog kontakta. To je značilo da se Flask
prikazi mogu zadržati kakvi jesu bez većih promena.

Ali šta ako želimo da redizajniramo korisnički interfejs aplikacije Kontakti za našu mobilnu aplikaciju? Možda
želimo da mobilna aplikacija koristi jedan ekran, sa redovima koji se proširuju u skladu sa tekstom kako bi
podržali pregled i uređivanje informacija? U situacijama kada se korisnički interfejs razlikuje između platformi,
prebacivanje šablona postaje glomazno ili nemoguće. Potreban nam je drugačiji pristup kako bismo imali jedan
bekend koji služi oba hipermedijska formata.

Pristup 2: Preusmeravanje viljuške

Ako se sećate, veb aplikacija Kontakti ima indexprikaz, usmeren iz korenske putanje /:

@app.route("/")
def index():

return redirect("/contacts") <1>

app.py

1. Preusmeri zahteve sa "/" na "/contacts"

Kada neko zatraži ka korenskoj putanji veb aplikacije, Flask ga preusmerava na tu /contactsputanju. Ovo



preusmeravanje takođe funkcioniše u našoj mobilnoj aplikaciji Hyperview. Hyperview klijent
ENTRY_POINT_URLukazuje na http://0.0.0.0:5000/, a server ga preusmerava na http://0.0.0.0:5000/contacts. Ali
ne postoji zakon koji kaže da moramo da preusmeravamo na istu putanju u našoj veb aplikaciji i mobilnoj
aplikaciji. Šta ako bismo koristili Acceptzaglavlje za preusmeravanje da bismo odlučili o putanji
preusmeravanja?

HTML_MIME = 'text/html'
HXML_MIME = 'application/vnd.hyperview+xml'

@app.route("/")
def index():
    response_type = request.accept_mimetypes.best_match(
      [HTML_MIME, HXML_MIME], default=HTML_MIME) <1>

if response_type == HXML_MIME:
return redirect("/mobile/contacts") <2>

else:
return redirect("/web/contacts") <3>

app.py

1. Utvrdite da li klijent želi HTML ili HXML.

2. Ako klijent želi HXML, preusmerite ga na /mobile/contacts.

3. Ako klijent želi HTML, preusmerite ga na /web/contacts.

Ulazna tačka je raskrsnica: ako klijent želi HTML, preusmeravamo ga na jednu putanju. Ako klijent želi HXML,
preusmeravamo ga na drugu putanju. Ova preusmeravanja bi se obrađivala različitim Flask prikazima:

@app.route("/mobile/contacts")
def mobile_contacts():


Render an HXML response

@app.route("/web/contacts")
def web_contacts():

Render an HTML response

app.py

Prikaz mobile_contacts()bi prikazao HXML šablon sa listom kontakata. Dodirom na stavku kontakta otvorio bi
se ekran zahtevan od /mobile/contacts/1, koji obrađuje prikaz mobile_contacts_view. Nakon početnog forka, svi
naredni zahtevi iz naše mobilne aplikacije idu na putanje sa prefiksom /mobile/, i obrađuju se mobilnim Flask
prikazima. Slično tome, svi naredni zahtevi iz veb aplikacije idu na putanje sa prefiksom /web/, i obrađuju se
veb Flask prikazima specifičnim za veb. (Imajte na umu da bismo u praksi želeli da razdvojimo veb i mobilne
prikaze u odvojene delove naše kodne baze: web_app.pyi mobile_app.py. Takođe možemo da izaberemo da ne
stavljamo prefiks ispred veb putanja /web/, ako želimo da se elegantniji URL-ovi prikazuju u adresnoj traci
pregledača.)

Možda mislite da Redirect Fork dovodi do velikog dupliranja koda. Na kraju krajeva, potrebno je da napišemo
dvostruki broj prikaza: jedan skup za veb aplikaciju i jedan skup za mobilnu aplikaciju. To je tačno, zbog čega je
Redirect Fork poželjniji samo ako dve platforme zahtevaju nepovezani skup logike prikaza. Ako su aplikacije
slične na obe platforme, prebacivanje šablona će uštedeti mnogo vremena i održati aplikacije konzistentnim.
Čak i ako treba da koristimo Redirect Fork, veći deo logike u našim modelima mogu deliti oba skupa prikaza.

U praksi, možete početi sa korišćenjem prebacivanja šablona, ali ćete zatim shvatiti da morate da
implementirate fork za funkcije specifične za platformu. U stvari, mi to već radimo u aplikaciji Kontakti.
Prilikom prenosa aplikacije sa veba na mobilni uređaj, nismo preneli određene funkcije poput funkcionalnosti
arhiviranja. Dinamički korisnički interfejs arhiviranja je moćna funkcija koja ne bi imala smisla na mobilnom
uređaju. Pošto naši HXML šabloni ne otkrivaju nikakve ulazne tačke funkcionalnosti arhiviranja, možemo je
tretirati kao "samo za veb" i ne brinuti o njenoj podršci u Hyperview-u.

U ovom poglavlju smo obradili dosta toga. Udahnite i napravite pregled koliko smo daleko stigli: preneli smo
osnovnu funkcionalnost veb aplikacije Contact.app na mobilne uređaje. I to smo uradili ponovnim korišćenjem
većeg dela našeg Flask bekenda i držeći se Jinja šablona. Ponovo smo videli korisnost događaja za povezivanje
različitih aspekata aplikacije.

Još nismo završili. U sledećem poglavlju ćemo implementirati prilagođena ponašanja i elemente korisničkog
interfejsa kako bismo završili našu mobilnu aplikaciju Contact.app.



Hipermedijske napomene: API krajnje tačke
Za razliku od JSON API-ja, hipermedijalni API koji proizvodite za svoju aplikaciju vođenu hipermedijom
trebalo bi da sadrži krajnje tačke specijalizovane za potrebe korisničkog interfejsa vaše određene aplikacije.

Pošto hipermedijalni API-ji nisu dizajnirani da ih koriste klijenti opšte namene, možete da ostavite po strani
pritisak da ih držite generalizovanim i da proizvodite sadržaj posebno potreban za vašu aplikaciju. Vaše krajnje
tačke treba da budu optimizovane da podrže potrebe korisničkog interfejsa/učešća vaše određene aplikacije, a
ne za opšti model pristupa podacima za vaš model domena.

Povezani savet je da, kada imate API zasnovan na hipermediji, možete agresivno refaktorisati svoj API na način
koji se snažno ne preporučuje prilikom pisanja SPA-ova ili mobilnih klijenata zasnovanih na JSON API-ju.
Pošto aplikacije zasnovane na hipermediji koriste hipermediju kao mehanizam stanja aplikacije, možete, i
zapravo, se podstičete, da menjate njihov oblik kako se vaši programeri aplikacija i kako se menjaju slučajevi
upotrebe.

Velika snaga hipermedijalnog pristupa je to što možete potpuno preraditi svoj API kako biste se prilagodili
novim potrebama tokom vremena, bez potrebe za verzijom API-ja ili čak njegovim dokumentovanjem.
Iskoristite to!
