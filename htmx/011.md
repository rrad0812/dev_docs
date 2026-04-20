JSON Data API-ji

Do sada smo se fokusirali na korišćenje hipermedija za izgradnju aplikacija vođenih
hipermedijom. Pritom pratimo i koristimo prednosti izvorne mrežne arhitekture veba i gradimo
RESTful sistem, u izvornom smislu tog termina.

Međutim, danas treba da priznamo da mnoge veb aplikacije često nisu izgrađene korišćenjem
ovog pristupa. Umesto toga, koriste biblioteku front-enda za jednostrane aplikacije kao što je
React za izgradnju svoje aplikacije i interaguju sa serverom putem JSON API-ja. Ovaj JSON API
skoro nikada ne koristi hipermedijske koncepte. Umesto toga, JSON API-ji su uglavnom API-ji
podataka, odnosno API-ji koji jednostavno vraćaju strukturirane podatke domena klijentu bez
ikakvih hipermedijskih kontrolnih informacija. Sam klijent mora znati kako da interpretira
JSON podatke: koje krajnje tačke su povezane sa podacima, kako treba interpretirati određena
polja i tako dalje.

Sada, verujte ili ne, kreiramo API za Contact.app.

Ovo vam može zvučati zbunjujuće: API? Upravo smo kreirali veb aplikaciju, sa obrađivačima koji
samo vraćaju HTML.

Kako je to API?

Ispostavlja se da Contact.app zaista pruža API. Samo što se desi da je to hipermedijalni API koji
hipermedijalni klijent, odnosno pregledač, razume. Pravimo API sa kojim pregledač može da
interaguje preko HTTP-a i, zahvaljujući magiji HTML-a i hipermedija, pregledač ne mora da zna
ništa o našem hipermedijalnom API-ju osim URL-a ulazne tačke: sve akcije i informacije o
prikazu dolaze, samostalno, unutar HTML odgovora.

Kreiranje RESTful veb aplikacija poput ove je toliko prirodno i jednostavno da možda uopšte
nećete pomisliti na to kao na API, ali vas uveravamo da jeste.

Dakle, imamo hipermedijalni API za Contact.app. Da li treba da uključimo i Data API za
Contact.app?

Naravno! Postojanje hipermedijalnog API-ja nikako ne znači da ne možete imati i Data API. U
stvari, ovo je uobičajena situacija u tradicionalnim veb aplikacijama: postoji "veb aplikacija"
kojoj se pristupa preko te URL adrese ulazne tačke, recimo https://mywebapp.example.com/. A
postoji i poseban JSON API koji je dostupan preko druge URL adrese, možda https://
api.mywebapp.example.com/v1.

Ovo je sasvim razuman način da se razdvoji hipermedijalni interfejs za vašu aplikaciju i Data API
koji pružate drugim klijentima koji nisu hipermedijalni.

Zašto biste želeli da uključite Data API zajedno sa hypermedia API-jem? Pa, zato što bi klijenti
koji nisu hipermedijski takođe mogli želeti da komuniciraju sa vašom aplikacijom.

Na primer:

- Možda imate mobilnu aplikaciju koja nije napravljena pomoću Hyperview-a. Ta aplikacija
će morati nekako da komunicira sa vašim serverom, a korišćenje postojećeg HTML API-ja
gotovo sigurno ne bi bilo dobro rešenje! Želite programski pristup vašem sistemu putem
Data API-ja, a JSON je prirodan izbor za to.



- Možda imate automatizovanu skriptu koja treba redovno da komunicira sa sistemom. Na
primer, možda imamo zadatak grupnog uvoza koji se pokreće svake noći i treba da uveze/
sinhronizuje hiljade kontakata. Iako bi bilo moguće napisati skriptu za ovo u odnosu na
HTML API, to bi takođe bilo dosadno: parsiranje HTML-a u skriptama je sklono greškama
i zamorno. Bilo bi bolje imati jednostavan JSON API za ovaj slučaj upotrebe.

- Možda postoje klijenti treće strane koji žele da se na neki način integrišu sa podacima
vašeg sistema. Možda partner želi da sinhronizuje podatke svake noći. Kao i kod primera
sa grupnim uvozom, ovo nije sjajan slučaj upotrebe za HTML-bazirani API i imalo bi više
smisla da se obezbedi nešto što je lakše za skriptovanje.

Za sve ove slučajeve upotrebe, JSON Data API ima smisla: u svakom slučaju, API ne koristi
hipermedijalni klijent, tako da bi predstavljanje hipermedijalnog API-ja zasnovanog na HTML-u
bilo neefikasno i komplikovano za klijenta. Jednostavan JSON Data API ispunjava uslove za ono
što želimo i, kao i uvek, preporučujemo korišćenje pravog alata za taj posao.

Razlike između hipermedijalnih API-ja i API-ja za podatke

Prihvatimo na trenutak da ćemo imati Data API za našu aplikaciju, pored našeg hipermedijskog
API-ja. U ovom trenutku, neki programeri se možda pitaju: zašto imati oba? Zašto ne bismo
imali jedan API, JSON Data API, i zašto bi više klijenata koristilo ovaj jedan API za
komunikaciju sa njim?

Zar nije suvišno imati obe vrste API-ja za našu aplikaciju?

Ovo je razumna poenta: zalažemo se za korišćenje više API-ja za vašu veb aplikaciju ako je
potrebno i, da, to može dovesti do izvesne redundantnosti u kodu. Međutim, postoje jasne
prednosti obe vrste API-ja i, još više, različiti zahtevi za obe vrste API-ja.

Podržavanjem oba ova tipa API-ja odvojeno možete dobiti prednosti oba, dok istovremeno jasno
razdvajate njihove različite stilove koda i potrebe za infrastrukturom.

Hajde da uporedimo potrebe JSON API-ja sa potrebama Hypermedia API-ja:

Potrebe za JSON API-jem Hipermedijski API
Mora ostati stabilan tokom vremena: ne možete Nema potrebe da ostanete stabilni tokom
menjati API hteti-nehteti ili rizikujete da vremena: svi URL-ovi se otkrivaju putem
pokvarite klijente koji koriste API i očekuju da se HTML odgovora, tako da možete biti mnogo
određene krajnje tačke ponašaju na određeni agresivniji u promeni oblika hipermedijalnog
način. API-ja.
Mora biti verzionisan: u vezi sa prvom tačkom,
kada napravite veliku promenu, potrebno je da Verziranje nije problem, što je još jedna snaga
verzionišete API tako da klijenti koji koriste stari hipermedijalnog pristupa.
API nastave da rade.
Trebalo bi da bude ograničene brzine: pošto API-
je podataka često koriste i drugi klijenti, a ne

Ograničavanje brzine verovatno nije toliko
samo vaša interna veb aplikacija, zahtevi treba

važno osim sprečavanja distribuiranih
da budu ograničeni brzinom, često od strane

napada uskraćivanja usluge (DDoS).
korisnika, kako bi se izbeglo da jedan klijent
preoptereti sistem.
Trebalo bi da bude opšti API: pošto je API API može biti veoma specifičan za potrebe
namenjen svim klijentima, ne samo vašoj veb vaše aplikacije: pošto je dizajniran samo za
aplikaciji, trebalo bi da izbegavate vašu određenu veb aplikaciju i pošto se API



specijalizovane krajnje tačke koje su vođene
otkriva putem hipermedije, možete dodavati i

potrebama vaše sopstvene aplikacije. Umesto
uklanjati visoko podešene krajnje tačke za

toga, API treba da bude dovoljno opšti i
određene funkcije ili potrebe optimizacije u

ekspresivan da zadovolji što više potencijalnih
vašoj aplikaciji.

potreba klijenata.
Autentifikacija za ove vrste API-ja je obično Autentifikacija se obično upravlja putem
zasnovana na tokenima, o čemu ćemo detaljnije kolačića sesije koji je uspostavila stranica za
govoriti kasnije. prijavu.

Ova dva različita tipa API-ja imaju različite prednosti i potrebe, tako da je logično koristiti oba.
Hipermedijalni pristup može se koristiti za vašu veb aplikaciju, što vam omogućava da
specijalizujete API za "oblik" vaše aplikacije. Pristup Data API-ja može se koristiti za druge
klijente koji nisu hipermedijalni, kao što su mobilni uređaji, integracioni partneri itd.

Imajte na umu da razdvajanjem ova dva API-ja smanjujete pritisak da stalno menjate opšti Data
API kako biste zadovoljili potrebe aplikacije. Vaš Data API može se fokusirati na stabilnost i
pouzdanost, umesto da zahteva novu verziju sa svakom dodatnom funkcijom.

Ovo je ključna prednost odvajanja vašeg Data API-ja od vašeg Hypermedia API-ja.

U redu, kako ćemo dodati JSON Data API našoj aplikaciji? Jedan pristup, popularizovan od
strane Ruby on Rails veb frejmvorka, jeste da koristite iste URL krajnje tačke kao i vašu
hipermedijalnu aplikaciju, ali koristite HTTP Acceptzaglavlje da biste utvrdili da li klijent želi
JSON ili HTML reprezentaciju. HTTP Acceptzaglavlje omogućava klijentu da navede koje MIME
(Multipurpose Internet Mail Extensions) tipove, odnosno tipove datoteka, želi nazad od servera:
JSON, HTML, tekst i tako dalje.

Dakle, ako klijent želi JSON reprezentaciju svih kontakata, može izdati GETzahtev koji izgleda
ovako:

Accept: application/json

GET /contacts

Zahtev za JSON reprezentaciju svih kontakata

Ako bismo usvojili ovaj obrazac, onda /contacts/bi naš obrađivač zahteva za morao biti ažuriran
da bi pregledao ovaj zaglavlje i, u zavisnosti od vrednosti, vratio JSON umesto HTML
reprezentaciju za kontakte. Ruby on Rails ima podršku za ovaj obrazac ugrađenu u okvir, što
olakšava uključivanje traženog MIME tipa.

Nažalost, naše iskustvo sa ovim obrascem nije bilo sjajno, iz razloga koji bi trebalo da budu jasni
s obzirom na razlike koje smo naveli između API-ja za podatke i hipermedijalnih API-ja: oni
imaju različite potrebe i često poprimaju veoma različite "oblike", a pokušaj da se uguraju u isti
skup URL-ova na kraju stvara veliku napetost u kodu aplikacije.

S obzirom na različite potrebe ova dva API-ja i naše iskustvo u upravljanju višestrukim API-jima
poput ovog, smatramo da je njihovo razdvajanje, i stoga, razdvajanje JSON Data API-ja na
njegov zaseban skup URL-ova, pravi izbor. Ovo će nam omogućiti da razvijamo ova dva API-ja
odvojeno jedan od drugog i dati nam prostora da svaki unapredimo nezavisno, na način koji je u
skladu sa njihovim individualnim snagama.

Izbor korenskog URL-a za naš API

S obzirom na to da ćemo odvojiti naše JSON Data API rute od naših redovnih hipermedijalnih



ruta, gde bi trebalo da ih postavimo? Jedno važno razmatranje ovde je da želimo da se uverimo
da možemo da verzionišemo naš API na neki način, bez obzira na obrazac koji izaberemo.

Ako pogledate okolo, mnoga mesta koriste poddomen za svoje API-je, nešto poput https://
api.mywebapp.example.comi, zapravo, često kodiraju verzije u poddomenu: https://
v1.api.mywebapp.example.com.

Iako ovo ima smisla za velike kompanije, deluje pomalo preterano za naš skromni mali
Contact.app. Umesto korišćenja poddomena, koji predstavljaju problem za lokalni razvoj,
koristićemo podputaje unutar postojeće aplikacije:

- Koristićemo /apikao koren za našu funkcionalnost Data API-ja

- Koristićemo /api/v1kao ulaznu tačku za verziju 1 našeg Data API-ja

Ako i kada odlučimo da nadogradimo verziju API-ja, možemo preći na /api/v2i tako dalje.

Ovaj pristup, naravno, nije savršen, ali će funkcionisati za našu jednostavnu aplikaciju i može se
prilagoditi pristupu poddomena ili raznim drugim metodama u kasnijem trenutku, kada naš
Contact.app preuzme internet i budemo mogli da priuštimo veliki tim API programera.:)

Naša prva JSON krajnja tačka: Lista svih kontakata

Dodajmo našu prvu krajnju tačku Data API-ja. Ona će obrađivati HTTP GETzahtev ka /api/v1/
contactsi vraćati JSON listu svih kontakata u sistemu. U nekim aspektima će izgledati prilično
slično našem početnom kodu za hipermedijsku rutu /contacts: učitaćemo sve kontakte iz baze
podataka kontakata, a zatim ćemo prikazati neki tekst kao odgovor.

Takođe ćemo iskoristiti jednu lepu karakteristiku Flaska: ako jednostavno vratite objekat iz
rukovaoca, on će serijalizovati (to jest, konvertovati) taj objekat u JSON odgovor. Ovo čini
veoma lakim za pravljenje jednostavnih JSON API-ja u Flasku!

@app.route("/api/v1/contacts", methods=["GET"]) <1>
def json_contacts():
    contacts_set = Contact.all()
    contacts_dicts = [c.__dict__ for c in contacts_set] <2>

return {"contacts": contacts_dicts} <3>

JSON API za podatke za vraćanje svih kontakata

1. JSON API dobija svoju putanju, počevši od /api.

2. Konvertujte niz kontakata u niz jednostavnih objekata rečnika (mape).

3. Vrati rečnik koji sadrži contactssvojstvo svih kontakata.

Ovaj Pajton kod vam može izgledati pomalo strano ako niste Pajton programer, ali sve što
radimo jeste da konvertujemo naše kontakte u niz jednostavnih parova ime/vrednost i vraćamo
taj niz u objektu koji ga obuhvata kao contactssvojstvo. Flask će automatski serijalizovati ovaj
objekat u JSON odgovor.

Sa ovim na mestu, ako uputimo HTTP GETzahtev ka /api/v1/contacts, videćemo odgovor koji
izgleda otprilike ovako:

{
"contacts": [
{



"email": "carson@example.com",
"errors": {},
"first": "Carson",
"id": 2,
"last": "Gross",
"phone": "123-456-7890"

},
{
"email": "joe@example2.com",
"errors": {},
"first": "",
"id": 3,
"last": "",
"phone": ""

},
...

]
}

Neki primeri podataka iz našeg API-ja

Dakle, vidite, sada imamo način da dobijemo relativno jednostavnu JSON reprezentaciju naših
kontakata putem HTTP zahteva. Nije savršeno, ali je dobar početak. Svakako je dovoljno dobro
da se napišu neki osnovni automatizovani skriptovi. Na primer, možete koristiti ovaj Data API
za:

- Premeštajte kontakte na drugi sistem svake noći

- Napravite rezervnu kopiju kontakata u lokalnu datoteku

- Automatizujte slanje imejlova svojim kontaktima

Ovaj mali JSON Data API otvara mnogo mogućnosti za automatizaciju koje bi bilo teže postići sa
našim postojećim hipermedijskim API-jem.

Dodavanje kontakata

Pređimo na sledeći deo funkcionalnosti: mogućnost dodavanja novog kontakta. Još jednom, naš
kod će u nekim aspektima izgledati slično kodu koji smo napisali za našu normalnu veb
aplikaciju. Međutim, ovde ćemo takođe videti da se JSON API i hypermedia API za našu veb
aplikaciju očigledno razlikuju.

U veb aplikaciji, bila nam je potrebna posebna putanja /contacts/newza hostovanje HTML
formulara za kreiranje novog kontakta. U veb aplikaciji smo doneli odluku da izdamo POSTka
istoj toj putanji kako bismo održali doslednost.

U slučaju JSON API-ja, takva putanja nije potrebna: JSON API "jednostavno postoji": ne mora
da pruža nikakvu hipermedijsku reprezentaciju za kreiranje novog kontakta. Jednostavno znate
gde da izdate komandu POSTza kreiranje kontakta — verovatno kroz neku dokumentaciju koja je
data o API-ju — i to je to.

Zbog te činjenice, možemo postaviti obrađivač "create" na istu putanju kao i obrađivač "list": /
api/v1/contacts, ali da on odgovara samo na HTTP POSTzahteve.

Kod ovde je relativno jednostavan: popunite novi kontakt informacijama iz zahteva POST,
pokušajte da ga sačuvate i — ako ne uspete — prikažite poruke o grešci. Evo koda:

@app.route("/api/v1/contacts", methods=["POST"]) <1>
def json_contacts_new():



    c = Contact(None,
      request.form.get('first_name'),
      request.form.get('last_name'),
      request.form.get('phone'),
      request.form.get('email')) <2>

if c.save(): <3>
return c.__dict__

else:
return {"errors": c.errors}, 400 <4>

Dodavanje kontakata pomoću našeg JSON API-ja

1. Ovaj obrađivač je na istoj putanji kao i prvi za naš JSON API, ali obrađuje POSTzahteve.

2. Kreiramo novi kontakt na osnovu vrednosti dostavljenih uz zahtev.

3. Pokušavamo da sačuvamo kontakt i, ako uspemo, prikažemo ga kao JSON objekat.

4. Ako čuvanje nije uspešno, prikazujemo objekat koji prikazuje greške, sa kodom odgovora
400 (Bad Request).

U nekim aspektima, ovo je slično našem contacts_new()obrađivaču iz naše veb aplikacije;
kreiramo kontakt i pokušavamo da ga sačuvamo. U drugim aspektima je veoma drugačije:

- Ovde se ne dešava preusmeravanje nakon uspešnog kreiranja, jer nemamo posla sa
hipermedijalnim klijentom kao što je pregledač.

- U slučaju lošeg zahteva, jednostavno vraćamo kod odgovora na grešku, 400 (Bad Request).
Ovo je u suprotnosti sa veb aplikacijom, gde ponovo prikazujemo obrazac sa porukama o
greškama u njemu.

Ove vrste razlika se vremenom nagomilavaju i čine ideju o čuvanju JSON i hipermedijalnih API-
ja na istom skupu URL-ova sve manje privlačnom.

Pregledanje kontakt podataka

Zatim, omogućićemo JSON API klijentu da preuzme detalje za jedan kontakt. Naravno,
koristićemo HTTP GETza ovu funkcionalnost i pratićemo konvenciju koju smo uspostavili za našu
redovnu veb aplikaciju i postavićemo putanju na /api/v1/contacts/<contact id>. Dakle, na
primer, ako želite da vidite detalje kontakta sa id-jem 42, izdali biste HTTP GETka /api/v1/
contacts/42.

Ovaj kod je prilično jednostavan:

@app.route("/api/v1/contacts/<contact_id>", methods=["GET"]) <1>
def json_contacts_view(contact_id=0):
    contact = Contact.find(contact_id) <2>

return contact.__dict__ <3>

Dobijanje detalja kontakta u JSON formatu

1. Dodajte novu GETrutu na putanji koju želimo da koristimo za pregled kontaktnih podataka

2. Potražite kontakt preko ID-a koji je prosleđen kroz putanju

3. Konvertujte kontakt u rečnik, tako da se može prikazati kao JSON odgovor

Ništa previše komplikovano: tražimo kontakt po ID-u koji je naveden u putanji do kontrolera.



Zatim ga prikazujemo kao JSON. Morate ceniti jednostavnost ovog koda!

Zatim, dodajmo i ažuriranje i brisanje kontakta.

Ažuriranje i brisanje kontakata

Kao i kod krajnje tačke API-ja za kreiranje kontakta, pošto ne postoji HTML korisnički interfejs
za kreiranje za njih, možemo ponovo koristiti putanju /api/v1/contacts/<contact id>.
Koristićemo PUTHTTP metod za ažuriranje kontakta i DELETEmetod za brisanje kontakta.

Naš kod za ažuriranje će izgledati gotovo identično kao i kod za kreiranje, osim što ćemo, umesto
kreiranja novog kontakta, pretraživati kontakt po ID-u i ažurirati njegova polja. U tom smislu,
samo kombinujemo kod za kreiranje i kod za detaljan prikaz.

@app.route("/api/v1/contacts/<contact_id>", methods=["PUT"]) <1>
def json_contacts_edit(contact_id):
    c = Contact.find(contact_id) <2>
    c.update(
        request.form['first_name'],
        request.form['last_name'],
        request.form['phone'],
        request.form['email']) <3>

if c.save(): <4>
return c.__dict__

else:
return {"errors": c.errors}, 400

Ažuriranje kontakta pomoću našeg JSON API-ja

1. Obrađujemo PUTzahteve upućene URL adresi za dati kontakt.

2. Potražite kontakt preko ID-a koji je prosleđen kroz putanju.

3. Ažuriramo podatke kontakta na osnovu vrednosti navedenih u zahtevu.

4. Odavde pa nadalje logika je identična onoj kod json_contacts_create()rukovaoca.

Još jednom, zahvaljujući ugrađenoj funkcionalnosti u Flasku, jednostavno je implementirati.

Hajde sada da pogledamo brisanje kontakta. Ovo se ispostavlja još jednostavnijim: kao i kod
obrada ažuriranja, potražićemo kontakt po ID-u, a zatim ćemo ga obrisati. U tom trenutku
možemo vratiti jednostavan JSON objekat koji ukazuje na uspeh.

@app.route("/api/v1/contacts/<contact_id>", methods=["DELETE"]) <1>
def json_contacts_delete(contact_id=0):
    contact = Contact.find(contact_id)
    contact.delete() <2>

return jsonify({"success": True}) <3>

Brisanje kontakta pomoću našeg JSON API-ja

1. Obrađujemo DELETEzahteve upućene URL adresi za dati kontakt.

2. Potražite kontakt i pozovite delete()metodu na njemu.

3. Vrati jednostavan JSON objekat koji ukazuje da je kontakt uspešno obrisan.

I, uz to, imamo naš jednostavan mali JSON Data API koji radi uz našu redovnu veb aplikaciju,
lepo odvojen od glavne veb aplikacije, tako da se može zasebno razvijati po potrebi.



Dodatna razmatranja o API-ju podataka

Sada bismo imali mnogo više posla ako bismo želeli da ovo bude JSON API spreman za
produkciju. Morali bismo bar da dodamo:

- Ograničavanje brzine, važno za svaki javno dostupan API za podatke kako bi se izbegli
zlonamerni klijenti.

- Mehanizam za autentifikaciju. (Ni mi ga nemamo za našu veb aplikaciju!)

- Podrška za paginaciju naših kontakt podataka.

- Nekoliko sitnica, kao što je prikazivanje ispravnog 404 (Not Found)odgovora ako neko
podnese zahtev sa ID-om kontakta koji ne postoji.

Ove teme prevazilaze obim ove knjige, ali bismo želeli da se fokusiramo na jednu posebno,
autentifikaciju, kako bismo pokazali razliku između našeg hipermedijskog i JSON API-ja. Da
bismo obezbedili našu aplikaciju, potrebno je da dodamo autentifikaciju, neki mehanizam za
određivanje od koga dolazi zahtev, kao i autorizaciju, koja određuje da li imaju pravo da izvrše
zahtev.

Za sada ćemo ostaviti autorizaciju po strani i razmotriti samo autentifikaciju.

Autentifikacija u veb aplikacijama

U svetu HTML veb aplikacija, autentifikacija se tradicionalno vršila putem stranice za prijavu
koja od korisnika traži korisničko ime (često imejl) i lozinku. Ova lozinka se zatim proverava u
bazi podataka (heširanih) lozinki kako bi se utvrdilo da je korisnik onaj ko se predstavlja. Ako je
lozinka ispravna, onda se uspostavlja kolačić sesije, koji ukazuje na to ko je korisnik. Ovaj
kolačić se zatim šalje sa svakim zahtevom koji korisnik uputi veb aplikaciji, omogućavajući
aplikaciji da zna koji korisnik upućuje dati zahtev.

HTTP kolačići HTTP kolačići su pomalo čudna karakteristika HTTP-a. U nekim aspektima oni
krše cilj ostanka bez stanja, glavnu komponentu RESTful arhitekture: server će često koristiti
kolačić sesije kao indeks stanja koji se čuva na serveru "sa strane", kao što je keš poslednje
radnje koju je izvršio korisnik.

Ipak, kolačići su se pokazali izuzetno korisnim i ljudi se obično ne žale previše na ovaj njihov
aspekt (Nismo sigurni koje bi nam bile druge opcije ovde!) Zanimljiv primer pragmatizma koji je
(relativno) dobro urađen u veb razvoju.

U poređenju sa standardnim pristupom veb aplikacija za autentifikaciju, JSON API će obično
koristiti neku vrstu autentifikacije zasnovane na tokenima: token za autentifikaciju će biti
uspostavljen putem mehanizma kao što je OAuth, a taj token za autentifikaciju će zatim biti
prosleđen, često kao HTTP zaglavlje, sa svakim zahtevom koji klijent napravi.

Na višem nivou, ovo je slično onome što se dešava kod normalne autentifikacije veb aplikacija:
token se nekako uspostavlja, a zatim je taj token deo svakog zahteva. Međutim, u praksi,
mehanika je obično potpuno drugačija:

- Kolačići su deo HTTP specifikacije i HTTP server ih može lako podesiti.

- JSON tokeni za autentifikaciju, nasuprot tome, često zahtevaju uspostavljanje složenih
mehanizama razmene poput OAuth-a.



Ovi različiti mehanizmi za uspostavljanje autentifikacije su još jedan dobar razlog za podelu
naših JSON i hipermedijalnih API-ja.

"Oblik" naša dva API-ja

Kada smo razvijali naš API, primetili smo da u mnogim slučajevima JSON API nije zahtevao
toliko krajnjih tačaka kao naš hipermedijalni API: nije nam bio potreban /contacts/new, na
primer, rukovalac da bismo obezbedili hipermedijalnu reprezentaciju za kreiranje kontakata.

Još jedan aspekt našeg hipermedijalnog API-ja koji treba razmotriti bilo je poboljšanje
performansi koje smo napravili: izvukli smo ukupan broj kontakata na zasebnu krajnju tačku i
implementirali obrazac "Lenjeg učitavanja" kako bismo poboljšali percipirane performanse naše
aplikacije.

Sada, ako bismo imali i hipermedijski i JSON API koji dele iste putanje, da li bismo želeli da
objavimo ovaj API i kao JSON krajnju tačku?

Možda, ali možda i ne. Ovo je bila prilično specifična potreba za našu veb aplikaciju i, bez
zahteva korisnika našeg JSON API-ja, nema smisla uključivati ga za JSON potrošače.

A šta ako, nekim čudom, problemi sa performansama Contact.count()koje smo rešavali
šablonom "lenjeg učitavanja" nestanu? Pa, u našoj aplikaciji vođenoj hipermedijom možemo
jednostavno da se vratimo na stari kod i da uključimo brojanje direktno u zahtev za /contacts.
Možemo da uklonimo contacts/countkrajnju tačku i svu logiku povezanu sa njom. Zbog
jedinstvenog interfejsa hipermedije, sistem će nastaviti da radi sasvim dobro.

Ali šta ako bismo povezali naš JSON API i hipermedijski API i objavili ih /contacts/countkao
podržanu krajnju tačku za naš JSON API? U tom slučaju ne bismo mogli jednostavno ukloniti
krajnju tačku: (nehipermedijski) klijent bi mogao da se oslanja na nju.

Još jednom možete videti fleksibilnost hipermedijalnog pristupa i zašto odvajanje vašeg JSON
API-ja od vašeg hipermedijalnog API-ja vam omogućava da maksimalno iskoristite tu
fleksibilnost.

Paradigma kontrolera prikaza modela (MVC)

Jedna stvar koju ste možda primetili kod obrađivača za naš JSON API jeste da su relativno
jednostavni i uobičajeni. Većina teškog posla ažuriranja podataka i tako dalje obavlja se unutar
samog modela kontakta: obrađivači deluju kao jednostavni konektori koji obezbeđuju posrednik
između HTTP zahteva i modela.

Ovo je idealan kontroler paradigme Model-View-Controller (MVC) koja je bila toliko popularna
u ranom vebu: kontroler bi trebalo da bude "tanak", pri čemu model sadrži većinu logike u
sistemu.

Tanki kontroleri olakšavaju razdvajanje vaših JSON i hipermedijalnih API-ja, jer se sva važna
logika nalazi u modelu domena koji dele oba. Ovo vam omogućava da razvijate oba odvojeno, a
da pritom logika ostane sinhronizovana jedna sa drugom.

Sa pravilno izgrađenim "tankim" kontrolerima i "debelim" modelima, održavanje dva odvojena
API-ja sinhronizovana, a ipak evoluiraju odvojeno, nije toliko teško ili ludo koliko možda zvuči.

HTML napomene: Mikroformati



Mikroformati su standard za ugrađivanje mašinski čitljivih strukturiranih podataka u HTML.
Koristi klase za označavanje određenih elemenata kao onih koji sadrže informacije koje treba
izdvojiti, sa konvencijama za izdvajanje uobičajenih svojstava kao što su ime, URL i fotografija
bez klasa. Dodavanjem ovih klasa u HTML reprezentaciju objekta, omogućavamo da se svojstva
objekta oporave iz HTML-a. Na primer, ovaj jednostavan HTML:

<a class="h-card" href="https://john.example">
<img src="john.jpg" alt=""> John Doe

</a>

može se analizirati u ovu JSON-sličnu strukturu pomoću mikroformata:

{
"type": ["h-card"],
"properties": {
"name": ["John Doe"],
"photo": ["john.jpg"],
"url": ["https://john.example"]

}
}

Koristeći razna svojstva i ugnežđene objekte, mogli bismo da označimo svaki deo informacije o
kontaktu, na primer, na način koji mašinski čita.

Kao što je objašnjeno u prethodnom poglavlju, pokušaj korišćenja istog mehanizma za
interakciju ljudi i mašina nije dobra ideja. Vaši interfejsi okrenuti ka ljudima i mašinama mogu
na kraju biti međusobno ograničeni. Ako želite da korisnicima i programerima izložite podatke i
radnje specifične za domen, JSON API je odlična opcija.

Međutim, mikroformate je mnogo lakše usvojiti. Protokol ili standard koji zahteva od veb
lokacija da implementiraju JSON API ima visoku tehničku barijeru. U poređenju sa tim, bilo
koja veb lokacija može biti proširena mikroformatima jednostavnim dodavanjem nekoliko klasa.
Drugi formati podataka ugrađeni u HTML, poput mikropodataka, Open Graph-a, slično se lako
usvajaju. Ovo čini mikroformate korisnim za sisteme koji prelaze sa veb lokacija na veb lokacije
(usuđujemo se reći veb-skalirane ) poput IndieWeb-a, koji ga sveprisutno koristi.
