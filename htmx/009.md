Trikovi Htmx majstora

U ovom poglavlju ćemo detaljnije razmotriti htmx alate. Dosta smo postigli sa onim što smo do sada naučili. Ipak, kada razvijate aplikacije zasnovane na hipermediji, biće trenutaka
kada ćete morati da posegnete za dodatnim opcijama i tehnikama.

Proći ćemo kroz naprednije atribute u htmx-u, kao i proširiti napredne detalje atributa koje smo već koristili.

Pored toga, pogledaćemo funkcionalnosti koje htmx nudi izvan jednostavnih HTML atributa: kako htmx proširuje standardne HTTP zahteve i odgovore, kako htmx radi sa (i proizvodi)
događajima i kako pristupiti situacijama gde ne postoji jednostavan, jedinstveni cilj na stranici koji treba ažurirati.

Na kraju, pogledaćemo praktična razmatranja prilikom razvoja htmx-a: kako efikasno otklanjati greške u aplikacijama zasnovanim na htmx-u, bezbednosna razmatranja koja treba
uzeti u obzir prilikom rada sa htmx-om i kako konfigurisati ponašanje htmx-a.

Sa karakteristikama i tehnikama u ovom poglavlju, moći ćete da kreirate izuzetno sofisticirane korisničke interfejse koristeći samo htmlx i možda malo hipermedijskog skriptovanja na
strani klijenta.

Htmx atributi
Do sada smo u našoj aplikaciji koristili oko petnaest različitih atributa iz htmlx-a. Najvažniji su bili:

hx-get, hx-post, itd.

Da bi se odredio AJAX zahtev koji element treba da napravi

hx-trigger

Da biste naveli događaj koji pokreće zahtev

hx-swap

Da biste odredili kako da zamenite vraćeni HTML sadržaj u DOM

hx-target

Da biste naveli gde u DOM-u treba zameniti vraćeni HTML sadržaj

Dva od ovih atributa, hx-swapi hx-trigger, podržavaju brojne korisne opcije za kreiranje naprednijih aplikacija vođenih hipermedijom.

hx-swap

Počećemo sa hx-swapatributom. Ovaj atribut često nije uključen u elemente koji izdaju zahteve vođene htmlx-om jer njegovo podrazumevano ponašanje — innerHTML, koje zamenjuje
unutrašnji HTML elementa — obično pokriva većinu slučajeva upotrebe.

Ranije smo videli situacije u kojima smo želeli da poništimo podrazumevano ponašanje i koristimo outerHTML, na primer. A u 2. poglavlju smo razgovarali o nekim drugim opcijama
zamene pored ove dve, beforebegin, afterend, itd.

U 5. poglavlju smo takođe razmotrili swapmodifikator kašnjenja za hx-swap, koji nam je omogućio da postepeno sakrijemo neki sadržaj pre nego što bude uklonjen iz DOM-a.

Pored ovoga, hx-swapnudi dodatnu kontrolu sa sledećim modifikatorima:

settle

Kao i swap, ovo vam omogućava da primenite određeno kašnjenje između trenutka kada je sadržaj zamenjen u DOM i trenutka kada su njegovi atributi "postavljeni", odnosno
ažurirani sa svojih starih vrednosti (ako ih ima) na svoje nove vrednosti. Ovo vam može dati preciznu kontrolu nad CSS prelazima.

show

Omogućava vam da odredite element koji treba da se prikaže — to jest, da se pomeri u prikaz pregledača ako je potrebno — kada se zahtev završi.

scroll

Omogućava vam da odredite element koji se može skrolovati (tj. element sa trakama za skrolovanje), koji treba da se skroluje na vrh ili dno kada se zahtev završi.

focus-scroll

Omogućava vam da navedete da htmx treba da se pomeri do fokusiranog elementa kada se zahtev završi. Podrazumevana vrednost za ovaj modifikator je "false".

Na primer, ako bismo imali dugme koje izdaje GETzahtev i želeli bismo da skrolujemo do vrha elementa bodykada se zahtev završi, napisali bismo sledeći HTML kod:

<button hx-get="/contacts" hx-target="#content-div"
  hx-swap="innerHTML show:body:top"> <1>
  Get Contacts
</button>

Pomeranje do vrha stranice

1. Ovo govori htmx-u da prikaže vrh tela nakon što se zamena desi.

Više detalja i primera možete pronaći na mreži u hx-swap dokumentaciji.

hx-triger

Kao hx-swap, hx-triggerčesto se može izostaviti kada koristite htmx, jer je podrazumevano ponašanje obično ono što želite. Podsetimo se da su podrazumevani okidači određeni tipom
elementa:

- Zahtevi za elemente input, textarea& selectse pokreću događajem change.

- Zahtevi za formelemente se pokreću na submitdogađaju.

- Zahtevi za sve ostale elemente se pokreću događajem click.

Međutim, postoje slučajevi kada želite detaljniju specifikaciju okidača. Klasičan primer je primer aktivne pretrage koji smo implementirali u Contact.app:

<input id="search" type="search" name="q"
  value="{{ request.args.get('q') or '' }}"
  hx-get="/contacts"
  hx-trigger="search, keyup delay:200ms changed"/> <1>

Aktivni unos za pretragu

1. Razrađena specifikacija okidača.

Ovaj primer je iskoristio dva modifikatora dostupna za hx-triggeratribut:

delay



Omogućava vam da odredite odlaganje pre nego što se zahtev izda. Ako se događaj ponovo desi, prvi događaj se odbacuje i tajmer se resetuje. Ovo vam omogućava da "odbijete"
zahteve.

changed

Omogućava vam da navedete da zahtev treba izdati samo kada valuese promeni svojstvo datog elementa.

hx-triggerima nekoliko dodatnih modifikatora. To je logično, jer su događaji prilično složeni i želimo da budemo u mogućnosti da iskoristimo svu moć koju nude. O događajima ćemo
detaljnije govoriti u nastavku.

Evo ostalih modifikatora dostupnih na hx-trigger:

once

Dati događaj će pokrenuti zahtev samo jednom.

throttle

Omogućava vam da ograničite događaje, izdavajući ih samo jednom u određenom intervalu. Ovo se razlikuje po delaytome što će se prvi događaj pokrenuti odmah, ali se svi
naredni događaji neće pokrenuti dok ne istekne vremenski period ograničenja.

from

CSS selektor koji vam omogućava da izaberete drugi element za praćenje događaja. Primer ove upotrebe videćemo kasnije u poglavlju.

target

CSS selektor koji vam omogućava da filtrirate događaje samo na one koji se dešavaju direktno na datom elementu. U DOM-u, događaji se "prenose" na svoje prethodne elemente,
tako da clickće događaj na dugmetu takođe pokrenuti clickdogađaj na okružujućem elementu div, sve do elementa body. Ponekad želite da odredite događaj direktno na datom
elementu, a ovaj atribut vam to omogućava.

consume

Ako je ova opcija podešena na true, okidajući događaj će biti otkazan i neće se proširiti na elemente pretka.

queue

Ova opcija vam omogućava da odredite kako se događaji stavljaju u red čekanja u htmx-u. Podrazumevano, kada htmx primi okidajući događaj, izdaće zahtev i pokrenuti red
čekanja za događaje. Ako je zahtev još uvek u toku kada se primi drugi događaj, staviće događaj u red čekanja i, kada se zahtev završi, pokrenuće novi zahtev. Podrazumevano,
zadržava samo poslednji događaj koji primi, ali možete izmeniti to ponašanje pomoću ove opcije: na primer, možete je podesiti na nonei ignorisati sve okidajuće događaje koji se
dogode tokom zahteva.

Filteri okidača

Atribut hx-triggervam takođe omogućava da odredite filter za događaje korišćenjem uglastih zagrada koje okružuju JavaScript izraz nakon imena događaja.

Recimo da imate složenu situaciju gde kontakti treba da budu dostupni samo u određenim situacijama. Imate JavaScript funkciju contactRetrievalEnabled()koja vraća bulovsku
vrednost, trueako se kontakti mogu preuzeti, a falseako se ne mogu. Kako biste mogli da koristite ovu funkciju da postavite kapiju na dugme koje izdaje zahtev za /contacts?

Da biste ovo uradili koristeći filter događaja u htmx-u, napisali biste sledeći HTML:

<script>
function contactRetrievalEnabled() {
// code to test if contact retrieval is enabled
...

  }
</script>
<button hx-get="/contacts"
  hx-trigger="click[contactRetrievalEnabled()]"> <1>
  Get Contacts
</button>

Aktivni unos za pretragu

1. Zahtev se izdaje klikom samo kada contactRetrievalEnabled()se vrati true.

Dugme neće izdati zahtev ako contactRetrievalEnabled()vrati vrednost "false", što vam omogućava da dinamički kontrolišete kada će zahtev biti podnet. Postoje uobičajene situacije koje
zahtevaju okidač događaja, kada želite da izdate zahtev samo pod određenim okolnostima:

- ako određeni element ima fokus

- ako je dati obrazac validan

- ako skup ulaza ima određene vrednosti

Koristeći filtere događaja, možete koristiti bilo koju logiku koju želite da filtrirate zahteve po htmlx-u.

Sintetički događaji

Pored ovih modifikatora, hx-triggernudi nekoliko "sintetičkih" događaja, odnosno događaja koji nisu deo redovnog DOM API-ja. Već smo videli loadi revealedu našim primerima lenjeg
učitavanja i beskonačnog skrolovanja, ali htmx vam takođe daje intersectdogađaj koji se pokreće kada element preseče svoj prikazni okvir.

Ovaj sintetički događaj koristi moderni Intersection Observer API, o kome možete više pročitati na MDN-u.

Presek vam daje preciznu kontrolu nad tačnim vremenom kada zahtev treba da se pokrene. Na primer, možete postaviti prag i navesti da se zahtev izda samo kada je element vidljiv
50%.

Atribut hx-triggerje svakako najsloženiji u htmx-u. Više detalja i primera možete pronaći u njegovoj dokumentaciji.

Ostali atributi

Htmx nudi mnoge druge manje uobičajene atribute za fino podešavanje ponašanja vaše aplikacije vođene hipermedijom.

Evo nekih od najkorisnijih:

hx-push-url

"Gura" URL zahteva (ili neku drugu vrednost) u navigacionu traku.

hx-preserve

Čuva deo DOM-a između zahteva; originalni sadržaj će biti sačuvan, bez obzira na to šta se vraća.

hx-sinhronizacija

Sinhronizovani zahtevi između dva ili više elemenata.

hx-disable



Onemogućava ponašanje htmx-a na ovom elementu i svim njegovim podređenim elementima. Vratićemo se na ovo kada budemo razgovarali o temi bezbednosti.

Hajde da pogledamo hx-sync, što nam omogućava da sinhronizujemo AJAX zahteve između dva ili više elemenata. Razmotrimo jednostavan slučaj gde imamo dva dugmeta koja oba
ciljaju isti element na ekranu:

<button hx-get="/contacts" hx-target="body">
  Get Contacts
</button>
<button hx-get="/settings" hx-target="body">
  Get Settings
</button>

Dva konkurentska dugmeta

Ovo je u redu i funkcionisaće, ali šta ako korisnik klikne na dugme "Preuzmi kontakte", a zatim je potrebno neko vreme da odgovori na zahtev? A u međuvremenu korisnik klikne na
dugme "Preuzmi podešavanja"? U ovom slučaju bismo imali dva zahteva u isto vreme.

Ako /settingsse zahtev prvo završi i prikaže informacije o podešavanjima korisnika, korisnik bi mogao biti veoma iznenađen ako počne da pravi izmene, a onda se, iznenada, /
contactszahtev završi i zameni celo telo kontaktima!

Da bismo se nosili sa ovom situacijom, mogli bismo razmotriti korišćenje `` hx-indicatorda bismo upozorili korisnika da se nešto dešava, čime bismo smanjili verovatnoću da će
kliknuti na drugo dugme. Ali ako zaista želimo da garantujemo da će se između ova dva dugmeta istovremeno izdati samo jedan zahtev, ispravno je koristiti hx-syncatribut ``. Hajde da
zatvorimo oba dugmeta u `` divi eliminišemo suvišnu hx-targetspecifikaciju podizanjem atributa `` do `` div. Zatim možemo koristiti hx-sync``on` taj div da bismo koordinirali
zahteve između dva dugmeta.

Evo našeg ažuriranog koda:

<div hx-target="body" <1>
  hx-sync="this"> <2>
<button hx-get="/contacts">

    Get Contacts
</button>
<button hx-get="/settings">

    Get Settings
</button>

</div>

Sinhronizacija dva dugmeta

1. Prebacite duplikate hx-targetatributa na roditelja div.

2. Sinhronizuj na roditeljskom uređaju div.

Postavljanjem hx-syncatributa na divsa vrednošću this, kažemo "Sinhronizujte sve htmlx zahteve koji se javljaju unutar ovog divelementa jedni sa drugima." To znači da ako jedno
dugme već ima zahtev u obradi, druga dugmad unutar divneće izdavati zahteve dok se to ne završi.

Atribut hx-syncpodržava nekoliko različitih strategija koje vam omogućavaju, na primer, da zamenite postojeći zahtev koji je u toku ili da stavite zahteve u red čekanja određenom
strategijom čekanja. Kompletnu dokumentaciju, kao i primere, možete pronaći na stranici htmx.org za hx-sync.

Kao što vidite, htmx nudi mnogo funkcionalnosti zasnovanih na atributima za naprednije aplikacije zasnovane na hipermediji. Kompletan pregled svih htmx atributa možete pronaći na
veb-sajtu htmx.

Događaji
Do sada smo radili sa JavaScript događajima u htmx-u prvenstveno preko hx-triggeratributa. Ovaj atribut se pokazao kao moćan mehanizam za pokretanje naše aplikacije koristeći
deklarativnu, HTML-prijateljsku sintaksu.

Međutim, postoji mnogo više što možemo da uradimo sa događajima. Događaji igraju ključnu ulogu kako u proširenju HTML-a kao hipermedije, tako i, kao što ćemo videti, u
skriptovanju prilagođenom hipermedijima. Događaji su "lepak" koji povezuje DOM, HTML, htmlx i skriptovanje. Možda biste DOM smatrali sofisticiranom "magistralom događaja" za
aplikacije.

Ne možemo dovoljno naglasiti: da bi se izgradile napredne aplikacije vođene hipermedijom, vredi se truditi da se događaji detaljno prouče.

Događaji generisani pomoću HTML-a

Pored toga što olakšava reagovanje na događaje, htmx takođe emituje mnoge korisne događaje. Možete koristiti ove događaje da dodate više funkcionalnosti svojoj aplikaciji, bilo
putem samog htmx-a ili putem skriptovanja.

Evo nekih od najčešće korišćenih događaja koje pokreće htmx:

htmx:load

Pokreće se kada se novi sadržaj učita u DOM pomoću htmx-a.

htmx:configRequest

Pokreće se pre nego što se zahtev izda, što vam omogućava da programski konfigurišete zahtev ili ga potpuno otkažete.

htmx:afterRequest

Pokreće se nakon odgovora na zahtev.

htmx:abort

Prilagođeni događaj koji se može poslati elementu koji pokreće htmlx da bi se prekinuo zahtev za otvaranje.

Korišćenje događaja htmx:configRequest

Pogledajmo primer kako se radi sa događajima koje emituje htmx. Koristićemo htmx:configRequestdogađaj za konfigurisanje HTTP zahteva.

Razmotrite sledeći scenario: vaš tim na strani servera je odlučio da žele da uključite token generisan od strane servera radi dodatne bezbednosti u svaki zahtev. Token će biti sačuvan u
localStoragepregledaču, u slotu special-token.

Token se podešava putem nekog Javaskripta (ne brinite još o detaljima) kada se korisnik prvi put prijavi:

let response = await fetch("/token"); <1>
localStorage['special-token'] = await response.text();

Dobijanje tokena u JavaSkriptu

1. Dobijte vrednost tokena, a zatim je postavite u localStorage

Tim na strani servera želi da uključite ovaj poseban token u svaki zahtev koji napravi htmx, kao X-SPECIAL-TOKENzaglavlje. Kako biste to mogli postići? Jedan od načina bi bio da uhvatite
htmx:configRequestdogađaj i ažurirate detail.headersobjekat ovim tokenom iz localStorage.

U VanillaJS-u, to bi izgledalo otprilike ovako, postavljeno u <script>oznaku u <head>našem HTML dokumentu:

document.body.addEventListener("htmx:configRequest", configEvent => {
  configEvent.detail.headers['X-SPECIAL-TOKEN'] = <1>



    localStorage['special-token'];
})

Dodavanje X-SPECIAL-TOKENzaglavlja

1. Preuzmite vrednost iz lokalne memorije i postavite je u zaglavlje.

Kao što vidite, dodajemo novu vrednost svojstvu headersdetail događaja. Nakon što se izvrši obrađivač događaja, headershtmx čita ovo svojstvo i koristi ga za konstruisanje zaglavlja
zahteva za AJAX zahtev koji pravi.

Svojstvo detaildogađaja htmx:configRequestsadrži mnoštvo korisnih svojstava koja možete ažurirati da biste promenili "oblik" zahteva, uključujući:

detail.parameters

Omogućava vam da dodate ili uklonite parametre zahteva

detail.target

Omogućava vam da ažurirate cilj zahteva

detail.verb

Omogućava vam da ažurirate HTTP "glagol" zahteva (npr. GET)

Na primer, ako je tim na strani servera odlučio da želi da token bude uključen kao parametar, a ne kao zaglavlje zahteva, mogli biste ažurirati svoj kod da izgleda ovako:

document.body.addEventListener("htmx:configRequest", configEvent => {
    configEvent.detail.parameters['token'] = <1>
      localStorage['special-token'];
})

Dodavanje tokenparametra

1. Preuzmite vrednost iz lokalne memorije i postavite je u parametar.

Kao što vidite, ovo vam daje veliku fleksibilnost u ažuriranju AJAX zahteva koji htmx pravi.

Kompletna dokumentacija za htmx:configRequestdogađaj (i druge događaje koji bi vas mogli zanimati) može se naći na veb stranici htmx.

Otkazivanje zahteva pomoću komande htmx:abort

Možemo da osluškujemo bilo koji od mnogih korisnih događaja iz htmx-a i možemo da odgovorimo na te događaje koristeći hx-trigger. Šta još možemo da uradimo sa događajima?

Ispostavlja se da sam htmx osluškuje jedan poseban događaj, htmx:abort. Kada htmx primi ovaj događaj na elementu koji ima zahtev u obradi, prekinuće zahtev.

Razmotrimo situaciju u kojoj imamo potencijalno dugotrajan zahtev ka /contactsi želimo da ponudimo način korisnicima da otkažu zahtev. Ono što želimo je dugme koje izdaje zahtev,
naravno vođeno pomoću htmlx-a, a zatim još jedno dugme koje će poslati događaj htmx:abortprvom.

Evo kako bi kod mogao da izgleda:

<button id="contacts-btn" hx-get="/contacts" hx-target="body"> <1>
  Get Contacts
</button>
<button
  onclick="
    document.getElementById('contacts-btn')
     .dispatchEvent(new Event('htmx:abort')) <2>
  ">
  Cancel
</button>

Dugme sa prekidom

1. Normalan GETzahtev vođen htmx-om za/contacts

2. Javaskript za pretraživanje dugmeta i slanje htmx:abortdogađaja

Dakle, ako korisnik klikne na dugme "Preuzmi kontakte" i zahtev potraje neko vreme, može da klikne na dugme "Otkaži" i završi zahtev. Naravno, u sofisticiranijem korisničkom
interfejsu, možda ćete želeti da onemogućite dugme "Otkaži" osim ako nije u toku HTTP zahtev, ali to bi bilo mučno implementirati u čistom JavaSkriptu.

Srećom, ovo nije tako loše implementirati u hiperskriptu, pa hajde da pogledamo kako bi to izgledalo:

<button id="contacts-btn" hx-get="/contacts" hx-target="body">
  Get Contacts
</button>
<button
  _="on click send htmx:abort to #contacts-btn
    on htmx:beforeRequest from #contacts-btn remove @disabled from me
    on htmx:afterRequest from #contacts-btn add @disabled to me">
  Cancel
</button>

Dugme sa hiperskriptom i prekidom

Sada imamo dugme "Otkaži" koje je omogućeno samo kada je zahtev sa contacts-btndugmeta u toku. I koristimo prednosti događaja generisanih i obrađenih u htmlx-u, kao i sintaksu
hiperskripta prilagođenu događajima, da bismo to omogućili. Odlično!

Događaji generisani serverom

U sledećem odeljku ćemo više govoriti o različitim načinima na koje htmx poboljšava redovne HTTP zahteve i odgovore, ali, pošto uključuje događaje, razmotrićemo jedan HTTP
zaglavlje odgovora koje htmx podržava:. HX-TriggerVeć smo ranije razgovarali o tome kako HTTP zahtevi i odgovori podržavaju zaglavlja, parove ime-vrednost koji sadrže metapodatke
o datom zahtevu ili odgovoru. Iskoristili smo HX-Triggerzaglavlje zahteva, koje uključuje ID elementa koji je pokrenuo dati zahtev.

Pored ovog zaglavlja zahteva, htmx takođe podržava zaglavlje odgovora, takođe pod nazivom HX-Trigger. Ovo zaglavlje odgovora vam omogućava da pokrenete događaj na elementu
koji je poslao AJAX zahtev. Ovo se ispostavlja kao moćan način za koordinisanje elemenata u DOM-u na razdvojeni način.

Da bismo videli kako bi ovo moglo da funkcioniše, razmotrimo sledeću situaciju: imamo dugme koje preuzima nove kontakte sa nekog udaljenog sistema na serveru. Zanemarićemo
detalje implementacije na strani servera, ali znamo da ako izdamo POSTputanji /sync, to će pokrenuti sinhronizaciju sa sistemom.

Sada, ova sinhronizacija može, ali i ne mora dovesti do kreiranja novih kontakata. U slučaju da se kreiraju novi kontakti, želimo da osvežimo našu tabelu kontakata. U slučaju da se ne
kreiraju kontakti, ne želimo da osvežavamo tabelu.

Da bismo ovo implementirali, mogli bismo uslovno dodati HX-Triggerzaglavlje odgovora sa vrednošću contacts-updated:

@app.route('/sync', methods=["POST"])
def sync_with_server():
    contacts_updated = RemoteServer.sync() <1>
    resp = make_response(render_template('sync.html'))

if contacts_updated <2>
      resp.headers['HX-Trigger'] = 'contacts-updated'

return resp

Uslovno pokretanje contacts-updateddogađaja



1. Poziv udaljenom sistemu koji je sinhronizovao našu bazu podataka kontakata sa njim

2. Ako su neki kontakti ažurirani, uslovno pokrećemo contacts-updateddogađaj na klijentu.

Ova vrednost bi pokrenula contacts-updateddogađaj na dugmetu koje je poslalo AJAX zahtev ka /sync. Zatim možemo iskoristiti modifikator from:atributa hx-triggerda bismo slušali taj
događaj. Ovim obrascem možemo efikasno pokrenuti htmx zahteve sa strane servera.

Evo kako bi mogao da izgleda kod na strani klijenta:

<button hx-post="/integrations/1"> <1>
  Pull Contacts From Integration
</button>

 ...

<table hx-get="/contacts/table"
  hx-trigger="contacts-updated from:body"> <2>
 ...
</table>

Tabela kontakata

1. Odgovor na ovaj zahtev može uslovno pokrenuti contacts-updateddogađaj

2. Ova tabela prati događaj i osvežava se kada se desi

Tabela osluškuje događaj contacts-updated, i to radi na bodyelementu. Osluškuje element bodyjer će se događaj pojaviti iz dugmeta, a to nam omogućava da ne povezujemo dugme i tabelu
zajedno: možemo pomerati dugme i tabelu kako želimo i, putem događaja, ponašanje koje želimo će nastaviti da funkcioniše ispravno. Pored toga, možda ćemo želeti da drugi elementi
ili zahtevi pokrenu contacts-updateddogađaj, tako da ovo pruža opšti mehanizam za osvežavanje tabele kontakata u našoj aplikaciji.

HTTP zahtevi i odgovori
Upravo smo videli naprednu funkciju HTTP odgovora koju podržava htmx, HX-Triggerzaglavlje odgovora, ali htmx podržava još dosta zaglavlja i za zahteve i za odgovore. U poglavlju 4
smo razgovarali o zaglavljima prisutnim u HTTP zahtevima. Evo nekih od važnijih zaglavlja koje možete koristiti da promenite ponašanje htmx-a sa HTTP odgovorima:

HX-Location

Izaziva preusmeravanje klijenta na novu lokaciju

HX-Push-Url

Unosi novi URL u traku lokacije

HX-Refresh

Osvežava trenutnu stranicu

HX-Retarget

Omogućava vam da navedete novi cilj u koji ćete zameniti sadržaj odgovora na strani klijenta

Referencu za sve zahteve i zaglavlja odgovora možete pronaći u htmx dokumentaciji.

HTTP kodovi odgovora

Još važnije od zaglavlja odgovora, u smislu informacija koje se prenose klijentu, jeste HTTP kod odgovora. HTTP kodove odgovora smo razmatrali u 3. poglavlju. Uglavnom, htmx
obrađuje različite kodove odgovora na način koji biste očekivali: zamenjuje sadržaj za sve kodove odgovora nivoa 200 i ne radi ništa za ostale. Međutim, postoje dva "posebna" koda
odgovora nivoa 200:

- 204 No Content- Kada htmx primi ovaj kod odgovora, neće zameniti nikakav sadržaj u DOM (čak i ako odgovor ima telo)

- 286- Kada htmx primi ovaj kod odgovora na zahtev koji se ispituje, zaustaviće ispitivanje

Možete poništiti ponašanje htmx-a u odnosu na kodove odgovora tako što ćete, pogodili ste, reagovati na događaj! Događaj htmx:beforeSwapvam omogućava da promenite ponašanje
htmx-a u odnosu na različite kodove statusa.

Recimo da, umesto da ne preduzmete ništa kada 404se dogodi, želite da upozorite korisnika da je došlo do greške. Da biste to uradili, želite da pozovete JavaScript metod,
showNotFoundError(). Dodajmo kod koji će koristiti htmx:beforeSwapdogađaj da bi se ovo desilo:

document.body.addEventListener('htmx:beforeSwap', evt => { <1>
if (evt.detail.xhr.status === 404) { <2>
showNotFoundError();

  }
});

Prikazuje se dijalog 404

1. Uključite se u htmx:beforeSwapdogađaj.

2. Ako je kod odgovora 404, prikažite korisniku dijalog.

Takođe možete koristiti htmx:beforeSwapdogađaj da konfigurišete da li odgovor treba da se zameni u DOM i koji element treba da cilja odgovor. Ovo vam daje dosta fleksibilnosti u izboru
načina na koji želite da koristite HTTP kodove odgovora u vašoj aplikaciji. Kompletna dokumentacija o htmx:beforeSwapdogađaju može se naći na htmx.org.

Ažuriranje ostalog sadržaja
Iznad smo videli kako se koristi događaj koji pokreće server, putem HX-TriggerHTTP zaglavlja odgovora, za ažuriranje dela DOM-a na osnovu odgovora na drugi deo DOM-a. Ova
tehnika se bavi opštim problemom koji se javlja u aplikacijama vođenim hipermedijom: "Kako da ažuriram drugi sadržaj?" Na kraju krajeva, u normalnim HTTP zahtevima postoji
samo jedan "cilj", ceo ekran, i, slično, u zahtevima zasnovanim na htmx-u postoji samo jedan cilj: ili eksplicitni ili implicitni cilj elementa.

Ako želite da ažurirate drugi sadržaj u htmx-u, imate nekoliko opcija:

Proširivanje vašeg izbora

Prva opcija, i najjednostavnija, jeste "proširivanje cilja". To jest, umesto jednostavne zamene malog dela ekrana, proširite cilj vašeg htmlx-vođenog zahteva dok ne bude dovoljno velik
da obuhvati sve elemente koje je potrebno ažurirati na ekranu. Ovo ima ogromnu prednost jer je jednostavno i pouzdano. Mana je što možda neće pružiti korisničko iskustvo koje želite
i možda neće dobro funkcionisati sa određenim rasporedom šablona na strani servera. Bez obzira na to, uvek preporučujemo da prvo razmislite o ovom pristupu.

Vanpojasne zamene

Druga opcija, malo složenija, jeste da se iskoristi podrška za sadržaj "Out Of Band" u htmx-u. Kada htmx primi odgovor, pregledaće ga za sadržaj najvišeg nivoa koji uključuje hx-swap-
oobatribut. Taj sadržaj će biti uklonjen iz odgovora, tako da neće biti zamenjen u DOM na uobičajen način. Umesto toga, biće zamenjen sadržajem koji se podudara po id-u.

Pogledajmo primer. Razmotrimo situaciju koju smo ranije imali, gde tabelu kontakata treba ažurirati ako integracija povuče bilo koje nove kontakte. Ranije smo ovo rešili korišćenjem
događaja i događaja koji pokreće server putem HX-Triggerzaglavlja odgovora.

Ovog puta, koristićemo hx-swap-oobatribut u odgovoru na " POSTto" /integrations/1. Novi sadržaj tabele kontakata će se "nadovezati" na odgovor.



<button hx-post="/integrations/1"> <1>
  Pull Contacts From Integration
</button>

 ...

<table id="contacts-table"> <2>
 ...
</table>

Ažurirana tabela kontakata

1. Dugme i dalje izdaje odlomak POSTod /integrations/1.

2. Tabela više ne čeka događaj, ali sada ima identifikator.

Zatim, odgovor na " POSTto" /integrations/1će sadržati sadržaj koji treba zameniti u dugme, prema uobičajenom htmlx mehanizmu. Ali će takođe sadržati novu, ažuriranu verziju tabele
kontakata, koja će biti označena kao hx-swap-oob="true". Ovaj sadržaj će biti uklonjen iz odgovora tako da se ne ubacuje u dugme. Umesto toga, zamenjuje se u DOM umesto postojeće
tabele, pošto ima odgovarajući identifikator.

HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
...

Pull Contacts From Integration <1>

<table id="contacts-table" hx-swap-oob="true"> <2>
 ...
</table>

Odgovor sa sadržajem van opsega

1. Ovaj sadržaj će biti smešten u dugme.

2. Ovaj sadržaj će biti uklonjen iz odgovora i zamenjen identifikatorom.

Koristeći ovu tehniku "pigibekinga", možete ažurirati sadržaj gde god je potrebno na stranici. hx-swap-oobAtribut podržava i druge dodatne funkcije, a sve su dokumentovane.

U zavisnosti od toga kako tačno funkcioniše vaša tehnologija šablona na strani servera i koji nivo interaktivnosti zahteva vaša aplikacija, zamena van opsega može biti moćan
mehanizam za ažuriranje sadržaja.

Događaji

Konačno, najsloženiji mehanizam za ažuriranje sadržaja je onaj koji smo videli u odeljku o događajima: korišćenje događaja koje pokreće server za ažuriranje elemenata. Ovaj pristup
može biti veoma čist, ali takođe zahteva dublje konceptualno znanje HTML-a i događaja, kao i posvećenost pristupu vođenom događajima. Iako nam se sviđa ovaj stil razvoja, on nije za
svakoga. Obično preporučujemo ovaj obrazac samo ako vam se htmx filozofija hipermedije vođene događajima zaista dopada.

Međutim, ako vam se dopadne, kažemo: samo napred. Napravili smo neke veoma složene i fleksibilne korisničke interfejse koristeći ovaj pristup i veoma nam se sviđa.

Biti pragmatičan

Svi ovi pristupi problemu "Ažuriranja drugog sadržaja" će funkcionisati, i često će dobro funkcionisati. Međutim, može doći tačka kada bi bilo jednostavnije koristiti drugačiji pristup za
vaš korisnički interfejs, poput reaktivnog. Koliko god nam se sviđa hipermedijalni pristup, stvarnost je da postoje neki UX obrasci koji se jednostavno ne mogu lako implementirati
pomoću njega. Kanonski primer ove vrste obrasca, koji smo ranije pomenuli, je nešto poput žive onlajn tabele: to je jednostavno previše složen korisnički interfejs, sa previše
međuzavisnosti, da bi se dobro uradio putem razmene hipermedija sa serverom.

U ovakvim slučajevima, i kad god smatrate da je rešenje zasnovano na htmx-u složenije od drugog pristupa, preporučujemo vam da razmotrite drugu tehnologiju. Budite pragmatični i
koristite pravi alat za posao. Uvek možete koristiti htmx za delove vaše aplikacije koji nisu toliko složeni i kojima je potrebna puna složenost reaktivnog okvira, a taj budžet za složenost
sačuvajte za delove kojima je potrebna.

Podstičemo vas da naučite mnogo različitih veb tehnologija, vodeći računa o prednostima i slabostima svake od njih. Ovo će vam pružiti bogat alat koji možete koristiti kada se pojave
problemi. Naše iskustvo je da je, sa htmlx-om, hipermedija alat koji možete često koristiti.

Otklanjanje grešaka
Ne stidimo se da priznamo: veliki smo ljubitelji događaja. Oni su osnovna tehnologija skoro svakog zanimljivog korisničkog interfejsa i posebno su korisni u DOM-u kada se otključaju
za opštu upotrebu u HTML-u. Omogućavaju vam da napravite lepo razdvojeni softver, a često čuvaju lokalnost ponašanja koju toliko volimo.

Međutim, događaji nisu savršeni. Jedno područje gde događaji mogu biti posebno teški za rešavanje jeste debagovanje: često želite da znate zašto se neki događaj ne dešava. Ali gde
možete postaviti tačku prekida za nešto što se ne dešava? Odgovor, trenutno, je: ne možete.

Postoje dve tehnike koje mogu pomoći u tom pogledu, jednu pruža htmx, a drugu Chrome, pregledač kompanije Google.

Evidentiranje Htmx događaja

Prva tehnika, koju pruža sam htmx, jeste pozivanje htmx.logAll()metode. Kada to uradite, htmx će evidentirati sve interne događaje koji se dešavaju dok obavlja svoje poslove, učitava
sadržaj, reaguje na događaje i tako dalje.

Ovo može biti zastrašujuće, ali uz pažljivo filtriranje može vam pomoći da se usredsredite na problem. Evo kako (delimično) izgledaju logovi kada kliknete na link "dokumentacija" na
https://htmx.org, kada logAll()je omogućeno:

htmx:configRequest
<a href="/docs/">
Object { parameters: {}, unfilteredParameters: {}, headers: {…}, target: body, verb: "get", errors: [], withCredentials: false, timeout: 0, path: "/docs/", triggeringEvent: a
, … }
htmx.js:439:29
htmx:beforeRequest
<a href="/docs/">
Object { xhr: XMLHttpRequest, target: body, requestConfig: {…}, etc: {}, pathInfo: {…}, elt: a
 }
htmx.js:439:29
htmx:beforeSend
<a class="htmx-request" href="/docs/">
Object { xhr: XMLHttpRequest, target: body, requestConfig: {…}, etc: {}, pathInfo: {…}, elt: a.htmx-request
 }
htmx.js:439:29
htmx:xhr:loadstart
<a class="htmx-request" href="/docs/">
Object { lengthComputable: false, loaded: 0, total: 0, elt: a.htmx-request
 }
htmx.js:439:29
htmx:xhr:progress
<a class="htmx-request" href="/docs/">
Object { lengthComputable: true, loaded: 4096, total: 19915, elt: a.htmx-request
 }
htmx.js:439:29
htmx:xhr:progress
<a class="htmx-request" href="/docs/">
Object { lengthComputable: true, loaded: 19915, total: 19915, elt: a.htmx-request
 }
htmx.js:439:29
htmx:beforeOnLoad
<a class="htmx-request" href="/docs/">
Object { xhr: XMLHttpRequest, target: body, requestConfig: {…}, etc: {}, pathInfo: {…}, elt: a.htmx-request



 }
htmx.js:439:29
htmx:beforeSwap
<body hx-ext="class-tools, preload">

Htmx logovi

Nije baš prijatno za oči, zar ne?

Ali, ako duboko udahnete i zažmirite, videćete da nije tako loše: niz htmx događaja, od kojih smo neke već videli (postoji htmx:configRequest!), beleži se u konzolu, zajedno sa elementom
na koji su pokrenuti.

Nakon malo čitanja i filtriranja, moći ćete da razumete tok događaja, a to vam može pomoći u otklanjanju problema vezanih za htmlx.

Praćenje događaja u Chrome-u

Prethodna tehnika je korisna ako se problem javlja negde unutar htmx-a, ali šta ako se htmx uopšte ne pokreće? Ovo se ponekad dešava, na primer kada ste slučajno pogrešno uneli
naziv događaja negde.

U ovakvim slučajevima biće vam potreban alat dostupan u samom pregledaču. Srećom, pregledač Chrome kompanije Google pruža veoma korisnu funkciju, monitorEvents(), koja vam
omogućava da pratite sve događaje koji se pokreću na elementu.

Ova funkcija je dostupna samo u konzoli, tako da je ne možete koristiti u kodu na vašoj stranici. Ali, ako radite sa htmlx-om u Chrome-u i radoznali ste zašto se događaj ne pokreće na
elementu, možete otvoriti konzolu za programere i otkucati sledeće:

monitorEvents(document.getElementById("some-element"));

Htmx logovi

Ovo će zatim ispisati sve događaje koji se pokreću na elementu sa tim identifikatorom some-elementu konzoli. Ovo može biti veoma korisno za razumevanje tačno na koje događaje želite
da odgovorite pomoću htmlx-a ili za rešavanje problema zašto se očekivani događaj ne dešava.

Korišćenje ove dve tehnike će vam pomoći dok (nadamo se retko) rešavate probleme vezane za događaje prilikom razvoja sa htmx-om.

Bezbednosna razmatranja
Generalno, htmlx i hypermedia su obično bezbedniji od pristupa izgradnji veb aplikacija koji koriste JavaScript. To je zato što, premeštanjem velikog dela obrade na bekend,
hipermedijski pristup obično ne izlaže toliko površine vašeg sistema krajnjim korisnicima za manipulacije i prevare.

Međutim, čak i sa hipermedijom, i dalje postoje situacije koje zahtevaju oprez prilikom razvoja. Posebno su zabrinjavajuće situacije u kojima se sadržaj koji generišu korisnici prikazuje
drugim korisnicima: pametan korisnik može pokušati da ubaci htmlx kod koji vara druge korisnike da kliknu na sadržaj koji pokreće radnje koje ne žele da preduzmu.

Generalno, sav sadržaj koji generišu korisnici treba da bude eskejpiran na strani servera, a većina frejmvorka za renderovanje na strani servera pruža funkcionalnost za rešavanje ove
situacije. Ali uvek postoji rizik da nešto promakne.

Da bi vam pomogao da bolje spavate noću, htmx pruža hx-disableatribut. Kada se ovaj atribut postavi na element, svi htmx atributi unutar tog elementa biće ignorisani.

Politike bezbednosti sadržaja i HTML

Politika bezbednosti sadržaja (CSP) je tehnologija pregledača koja vam omogućava da otkrijete i sprečite određene vrste napada zasnovanih na ubrizgavanju sadržaja. Potpuna diskusija
o CSP-ovima prevazilazi okvire ove knjige, ali vas upućujemo na članak Mozilla Developer Network-a na tu temu za više informacija.

Uobičajena funkcija koja se onemogućava korišćenjem CSP-a je eval()funkcija JavaScript-a, koja vam omogućava da procenite proizvoljni JavaScript kod iz stringa. Ovo se pokazalo
kao bezbednosni problem i mnogi timovi su odlučili da se ne isplati rizikovati da je drže omogućenom u svojim veb aplikacijama.

Htmx ne koristi mnogo eval(), pa će CSP sa ovim ograničenjem biti u redu. Jedina funkcija na koju se oslanja eval()su filteri događaja, o kojima je gore bilo reči. Ako odlučite da ih
onemogućite eval()za svoju veb aplikaciju, nećete moći da koristite sintaksu filtriranja događaja.

Konfigurisanje
Postoji veliki broj opcija za konfiguraciju dostupnih za htmx. Neki primeri stvari koje možete konfigurisati su:

- Podrazumevani stil zamene

- Podrazumevano kašnjenje zamene

- Podrazumevano vreme čekanja za AJAX zahteve

Kompletna lista opcija konfiguracije može se naći u odeljku konfiguracije glavne htmx dokumentacije.

Htmx se obično konfiguriše pomoću metaoznake, koja se nalazi u zaglavlju stranice. Naziv meta oznake treba da bude htmx-config, a atribut sadržaja treba da sadrži zamene
konfiguracije, formatirane kao JSON. Evo primera:

<meta name="htmx-config" content='{"defaultSwapStyle":"outerHTML"}'>

htmx konfiguracija putem metaoznake

U ovom slučaju, menjamo podrazumevani stil zamene sa uobičajenog innerHTMLna. Ovo može biti korisno ako češće outerHTMLkoristite i želite da izbegnete eksplicitno podešavanje te
vrednosti zamene u celoj aplikaciji.outerHTMLinnerHTML

HTML napomene: Semantički HTML
Govorenje ljudima da "koriste semantički HTML" umesto da "pročitaju specifikaciju" dovelo je do toga da mnogi ljudi pogađaju značenje oznaka — "meni izgleda prilično semantički!"
— umesto da se bave specifikacijom.

Mislim da zahtev za pisanje smislenog HTML-a bolje osvetljava put ka shvatanju da nije bitno šta tekst znači ljudima - već korišćenje oznaka u svrhu navedenu u
specifikacijama kako bi se zadovoljile potrebe softvera poput pregledača, pomoćnih tehnologija i pretraživača.

—  https://t-ravis.com/post/doc/semantic_the_8_letter_s-word/

Preporučujemo razgovor o i pisanje HTML-a koji je u skladu sa standardima. (Uvek možemo dalje da se bavimo biciklizmom). Koristite elemente u punom obimu koji je predviđen
HTML specifikacijom i pustite softver da iz njih preuzme bilo koje značenje koje može.