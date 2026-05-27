
# Uvod u AlpineJS

[Sadržaj](00_Sadržaj.md)

## 1 Počnimo

Napravite praznu HTML datoteku negde na računaru sa imenom kao što je: "i-love-alpine.html"

Koristeći uređivač teksta, popunite datoteku ovim sadržajem:

```html
<html>
<head>
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
</head>
<body>
    <h1 x-data="{ message: 'I ❤️ Alpine' }" x-text="message"></h1>
</body>
</html>
```

Otvorite datoteku u veb pregledaču, ako vidite "I ❤️ Alpine", spremni ste za igru!

Hajde da pogledamo tri praktična primera kao osnovu za učenje osnova AlpineJS. Do kraja ove vežbe, trebalo bi da budete više nego opremljeni da počnete sami da gradite stvari. Hajde da krenemo.

- Izgradnja brojača
- Kreiranje padajućeg menija
- Kreiranje unosa za pretragu

[Sadržaj](00_Sadržaj.md)

### 1.1 Izgradnja brojača

Počnimo sa jednostavnom komponentom "brojač" kako bismo demonstrirali osnove praćenja stanja i događaja u AlpineJS, dve osnovne karakteristike.

Umetnite sledeće u `<body>` oznaku:

```html
<div x-data="{ count: 0 }">
    <button x-on:click="count++">Increment</button>
    <span x-text="count"></span>
</div>
```

Sada, možete videti da smo, sa 2 linije AlpineJSkoda umetnuta u ovaj HTML, kreirali interaktivnu komponentu "brojač".

Hajde da ukratko prođemo kroz kod da vidimo šta se dešava:

**Deklarisanje podataka**:

```html
<div x-data="{ count: 0 }">
```

Sve u AlpineJS počinje direktivom `x-data`. Unutar `x-data`, u običnom JavaScript-u, deklarišete objekat podataka - "promenljiva stanja" koji će AlpineJSpratiti.

Svako svojstvo unutar ovog objekta biće dostupno drugim direktivama unutar ovog HTML elementa. Pored toga, kada se jedno od ovih svojstava promeni, promeniće se i sve što se na njega oslanja.

> [!Note]
> `x-data` je potreban na roditeljskom elementu da bi većina AlpineJS direktiva funkcionisale.

Hajde da pogledamo `x-on` kako može da pristupi i izmeni `count` svojstvo odozgo:

**Osluškivanje događaja**:

```html
<button x-on:click="count++">Increment</button>
```

`x-on` je direktiva koju možete koristiti za slušanje bilo kog događaja na elementu. U ovom slučaju slušamo `click` događaj, tako da naš kod izgleda kao `x-on:click`.

Možete osluškivati i druge događaje koje želite. Na primer, osluškivanje događaja `mouseenter` bi izgledalo ovako `x-on:mouseenter`.

Kada se desi `click` događaj, AlpineJS će pozvati pridruženi JavaScript izraz, `count++` u našem slučaju. Kao što vidite, imamo direktan pristup podacima deklarisanim u `x-data` izrazu.

> [!Note]
> Često ćete videti `@` umesto `x-on:`. Ovo je kraća, prijateljskija sintaksa koju mnogi preferiraju. Od sada će ova dokumentacija verovatno koristiti `@` umesto `x-on:`.

**Reagovanje na promene**:

```html
<span x-text="count"></span>
```

`x-text` je AlpineJS direktiva koju možete koristiti da podesite tekstualni sadržaj elementa kao rezultat JavaSkript izraza.

U ovom slučaju, kažemo AlpineJS da uvek osigura da sadržaj ove `span` oznake odražava vrednost objekta `count`.

U slučaju da nije jasno, `x-text`, kao i većina direktiva, prihvata običan JavaScript izraz kao argument. Na primer, umesto toga možete podesiti njegov sadržaj sa:

```html
<span x-text="count * 2">
```

i tekstualni sadržaj `span` će sada uvek biti 2 puta veći od vrednosti `count`.

[Sadržaj](00_Sadržaj.md)

### 1.2 Kreiranje padajućeg menija

Sada kada smo videli neke osnovne funkcionalnosti, hajde da nastavimo i pogledamo važnu direktivu u AlpineJS: `x-show`, izgradnjom izmišljene komponente "padajućeg menija".

Umetnite sledeći kod u `<body>` oznaku:

```html
<div x-data="{ open: false }">
    <button @click="open = !open">Toggle</button>
    <div x-show="open" @click.outside="open = false">Contents...</div>
</div>
```

Ako učitate ovu komponentu, trebalo bi da vidite da je "Contents..." podrazumevano skriven. Možete da ga uključite/isključite na stranici klikom na dugme "Toogle".

Direktive `x-data` and `x-on` bi vam trebalo da budu poznate iz prethodnog primera, pa ćemo preskočiti ta objašnjenja.

**Prebacivanje elemenata**:

```html
<div x-show="open" ...>Contents...</div>
```

`x-show` je izuzetno moćna direktiva u AlpineJS koja se može koristiti za prikazivanje i skrivanje bloka HTML-a na stranici na osnovu rezultata JavaScript izraza, u našem slučaju: `open`.

**Osluškivanje eksternog `click`**:

```html
<div ... @click.outside="open = false">Contents...</div>
```

Primetićete nešto novo u ovom primeru: `.outside`. Mnoge direktive u AlpineJS prihvataju "modifikatore" koji su lančano povezani na kraju direktive i odvojeni su tačkama.

U ovom slučaju, `.outside` govori AlpineJS da umesto da sluša klik unutar `<div>`, da sluša klik samo ako se desi van `<div>`.

Ovo je pomoćna funkcija ugrađena u AlpineJS jer je to uobičajena potreba i njena ručna implementacija je dosadna i složena.

[Sadržaj](00_Sadržaj.md)

### 1.3 Kreiranje unosa za pretragu

Hajde sada da napravimo složeniju komponentu i uvedemo nekoliko drugih direktiva i obrazaca.

Umetnite sledeći kod u `<body>` oznaku:

```html
<div
    x-data="{
        search: '',
        items: ['foo', 'bar', 'baz'],
        get filteredItems() {
            return this.items.filter(
                i => i.startsWith(this.search)
            )
        }
    }"
>
    <input x-model="search" placeholder="Search...">
    <ul>
        <template x-for="item in filteredItems" :key="item">
            <li x-text="item"></li>
        </template>
    </ul>
</div>
```

```html
fu
bar
baz
```

Podrazumevano, sve "stavke" (foo, bar i baz) biće prikazane na stranici, ali ih možete filtrirati kucanjem u polje za unos teksta. Dok kucate, lista stavki će se menjati kako bi odražavala ono što tražite.

Sada se ovde dosta toga dešava, pa hajde da prođemo kroz ovaj isečak koda deo po deo.

**Višeredno formatiranje `x-data` direktive**:

Prvo što bih želeo da istaknem jeste da `x-data` sada ima mnogo više toga u sebi nego ranije. Da bismo olakšali pisanje i čitanje, podelili smo ga u više redova u našem HTML-u. Ovo je potpuno opciono i malo kasnije ćemo više govoriti o tome kako da u potpunosti izbegnemo ovaj problem, ali za sada ćemo sav ovaj JavaScript zadržati direktno u HTML-u.

**Vezivanje sa ulaznim elementima**:

```html
<input x-model="search" placeholder="Search...">
```

Primetićete novu direktivu koju još nismo videli `x-model:`.

`x-model` se koristi za "povezivanje" vrednosti ulaznog elementa sa svojstvom podataka:

```html
<input `x-model="{ search: '', ... }">
```

u našem slučaju "search".

To znači da će se vrednost "search" promeniti svaki put kada se vrednost input elementa promeni.

`x-model` je sposoban za mnogo više od ovog jednostavnog primera.

**Izračunata svojstva korišćenjem gettera**:

Sledeće na šta bih želeo da skrenem vašu pažnju jesu svojstva `items` i `filteredItems` iz `x-data` direktive.

```js
{
    ...
    items: ['foo', 'bar', 'baz'],
    get filteredItems() {
        return this.items.filter(
            i => i.startsWith(this.search)
        )
    }
}
```

Svojstvo `items` bi trebalo da bude samo po sebi jasno. Ovde postavljamo vrednost `items` na JavaScript niz od 3 različite stavke ("foo", "bar" i "baz").

Zanimljiv deo ovog isečka je `filteredItems` svojstvo.

Prefiks `get` za ovo svojstvo `filteredItems` označio je svojstvo tipa "getter" u ovom objektu. To znači da možemo pristupiti `filteredItems` kao da je u pitanju normalno svojstvo u našem objektu podataka, ali kada to učinimo, JavaScript će proceniti datu funkciju "ispod haube" i vratiti rezultat.

Potpuno je prihvatljivo odustati od `get` i jednostavno napraviti `this` kao metodu koju možete pozvati iz šablona, ali neki preferiraju lepšu sintaksu `get` metode.

Sada da pogledamo unutar `filteredItems` gettera i uverimo se da razumemo šta se tamo dešava:

```js
return this.items.filter(
    i => i.startsWith(this.search)
)
```

Ovo je sve običan JavaScript. Prvo dobijamo niz elemenata (foo, bar i baz) i filtriramo ih koristeći dati povratni poziv:

```js
i => i.startsWith(this.search).
```

Prenošenjem ovog povratnog poziva na filter, govorimo JavaScript-u da vrati samo stavke koje počinju stringom: `this.search`, koji će, kao što smo videli sa `x-model` uvek odražavati vrednost unosa.

Možda ćete primetiti da do sada nismo morali da koristimo `this.` za referenciranje svojstava. Međutim, pošto radimo direktno unutar `x-data` objekta, moramo referencirati sva svojstva koristeći `this.[property]` umesto jednostavno `[property]`.

Pošto je AlpineJS "reaktivni" frejmvork, svaki put kada se vrednost `this.search` promeni, delovi šablona koji se koriste `filteredItems` će se automatski ažurirati.

**Elementi petlje**:

Sada kada razumemo deo sa podacima naše komponente, hajde da razumemo šta se dešava u šablonu što nam omogućava da se krećemo kroz `filteredItems` stranicu.

```html
<ul>
    <template x-for="item in filteredItems">
        <li x-text="item"></li>
    </template>
</ul>
```

Prvo što treba primetiti ovde je `x-for` AlpineJS direktiva. `x-for` izrazi imaju sledeći oblik:
`[item] in [items]` gde je [items] bilo koji niz podataka, a [item] je ime promenljive koja će biti dodeljena iteraciji unutar petlje.

Takođe primetite da je `x-for` deklarisano na `<template>` elementu, a ne direktno na `<li>`. Ovo je uslov korišćenja `x-for`. Omogućava AlpineJS da iskoristi postojeće ponašanje `<template>` oznaka u pregledaču u svoju korist.

Sada će se svaki element unutar `<template>` oznake ponoviti za svaku stavku unutra `filteredItems` i svi izrazi izračunati unutar petlje imaće direktan pristup promenljivoj iteracije ( `item` u ovom slučaju ).

[Sadržaj](00_Sadržaj.md)

### 1.4 Rezime

Ako ste stigli dovde, upoznali ste se sa sledećim direktivama u programu Alpine:

- `x-data`
- `x-on`
- `x-text`
- `x-show`
- `x-model`
- `x-for`

To je odličan početak, međutim, postoji još mnogo direktiva u koje treba zaroniti zube. Najbolji način da usvojite AlpineJS jeste da pročitate ovu dokumentaciju. Nema potrebe da češljate svaku reč, ali ako barem prelistate svaku stranicu, bićete mnogo efikasniji kada koristite AlpineJS.

[Sadržaj](00_Sadržaj.md)

## 2 Osnovne stvari

### 2.1 Instalacija

Postoje 2 načina da uključite Alpine u ​​svoj projekat:

- Uključivanje iz `<script>` oznake
- Uvoz kao modul

Oba su sasvim validna. Sve zavisi od potreba projekta i ukusa programera.

**Iz `<script>` oznake**:

Ovo je ubedljivo najjednostavniji način da počnete sa Alpine-om. Uključite sledeći `<script>` tag u zaglavlje vaše HTML stranice.

```html
<html>
    <head>
        ...
        <script defer src="<https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
    </head>
    ...
</html>
```

Ne zaboravite atribut `defer` u `<script>` oznaci.

Obratite pažnju na @3.x.xu datom CDN linku. Ovo će preuzeti najnoviju verziju Alpine verzije 3. Radi stabilnosti u produkciji, preporučuje se da čvrsto kodirate najnoviju verziju u CDN linku.

```html
<script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.15.12/dist/cdn.min.js"></script>
```

To je to! AlpineJS je sada dostupan za upotrebu unutar vaše stranice.

Imajte na umu da ćete i dalje morati da definišete komponentu sa `x-data` da bi svi atributi Alpine.JS radili. Više informacija potražite na <https://github.com/alpinejs/alpine/discussions/3805>.

**Kao modul**:

Ako više volite robusniji pristup, možete instalirati Alpine putem NPM-a i uvesti ga u paket.

Pokrenite sledeću komandu da biste je instalirali.

```sh
npm install alpinejs
```

Sada uvezite AlpineJS u ​​vaš paket i inicijalizujte ga ovako:

```js
import Alpine from 'alpinejs'

window.Alpine = Alpine

Alpine.start()
```

Ovaj `window.Alpine = Alpine` deo je opcionalan, ali je lepo imati ga zbog slobode i fleksibilnosti. Na primer, kada se petljate sa AlpineJS iz devtools-a.

Ako ste uvezli Alpine u ​​paket, morate se uveriti da registrujete bilo koji kod proširenja između koda kada uvozite AlpineJS globalni objekat i kada inicijalizujete Alpine pozivanjem `Alpine.start()`. Uverite se da se `Alpine.start()` poziva samo jednom po stranici. Višestruko pozivanje će rezultirati time da se više "instanci" Alpine-a pokreće istovremeno.

[Sadržaj](00_Sadržaj.md)

### 2.2 Stanje

Stanje (JavaScript podaci čije promene AlpineJS prati) je u srži svega što radite u AlpineJS. Možete da obezbedite lokalne podatke delu HTML-a ili da ih učinite globalno dostupnim za upotrebu bilo gde na stranici koristeći `x-data` ili `Alpine.store()` respektivno.

**Lokalno stanje**

AlpineJS vam omogućava da deklarišete stanje HTML bloka u jednom `x-data` atributu bez ostavljanja sopstvenih oznaka.

Evo osnovnog primera:

```html
<div x-data="{ open: false }">
    ...
</div>
```

Sada će bilo koja druga AlpineJS sintaksa na ili unutar ovog elementa moći da pristupi `open`. I kao što biste pretpostavili, kada se `open` promeni iz bilo kog razloga, sve što zavisi od njega će automatski reagovati.

***Ugnežđene promenljive stanja***:

Podaci se mogu ugnježvati u AlpineJS. Na primer, ako imate dva elementa sa priključenim AlpineJS podacima (jedan unutar drugog), možete pristupiti podacima roditelja iznutra podređenog elementa.

```html
<div x-data="{ open: false }">
    <div x-data="{ label: 'Content:' }">
        <span x-text="label"></span>
        <span x-show="open"></span>
    </div>
</div>
```

Ovo je slično određivanju opsega (scoping) u samom JavaSkriptu (kod unutar funkcije može pristupiti promenljivim deklarisanim izvan te funkcije).

Kao što ste možda pretpostavili, ako dete ima svojstvo podataka koje se podudara sa imenom svojstva roditelja, svojstvo deteta će imati prednost.

***Podaci o jednom elementu***

Iako se ovo nekima može činiti očiglednim, vredi napomenuti da se podaci iz programa AlpineJS mogu koristiti unutar istog elementa. Na primer:

```html
<button x-data="{ label: 'Click Here' }" x-text="label"></button>
```

***AlpineJS bez podataka***

Ponekad možda želite da koristite Alpine funkcionalnost, ali vam nisu potrebni nikakvi reaktivni podaci. U tim slučajevima, možete se isključiti iz potpunog prosleđivanja izraza `x-data`. Na primer:

```html
<button x-data @click="alert('I\'ve been clicked!')">Click Me</button>
```

***Podaci koji se mogu ponovo koristiti***

Kada koristite AlpineJS, možda ćete imati potrebu da ponovo koristite deo podataka i/ili odgovarajući šablon.

Ako koristite bekend frejmvork kao što su Rails ili Laravel, AlpineJS prvo preporučuje da izdvojite ceo blok HTML-a u delimični ili uključeni šablon.

Ako iz nekog razloga to nije idealno za vas ili niste u okruženju za kreiranje šablona u pozadini, AlpineJS vam omogućava da globalno registrujete i ponovo koristite deo podataka komponente koristeći `Alpine.data(...)`.

```js
Alpine.data('dropdown', () => ({
    open: false,
    toggle() {
        this.open = ! this.open
    }
}))
```

Sada kada ste registrovali podatke „padajućeg menija“, možete ih koristiti unutar svog označavanja na onoliko mesta koliko želite:

```html
<div x-data="dropdown">
    <button @click="toggle">Expand</button>
    <span x-show="open">Content...</span>
</div>
<div x-data="dropdown">
    <button @click="toggle">Expand</button>
    <span x-show="open">Some Other Content...</span>
</div>
```

**Globalno stanje**:

Ako želite da neke podatke učinite dostupnim svakoj komponenti na stranici, to možete učiniti koristeći Alpine-ovu funkciju "globalnog skladištenja" - `store`.

Možete registrovati promenljivu stanja koristeći `Alpine.store(...)` i referencirati je magičnom `$store()` metodom.

Pogledajmo jednostavan primer. Prvo ćemo registrovati prodavnicu globalno:

```js
Alpine.store('tabs', {
    current: 'first',
    items: ['first', 'second', 'third'],
})
```

Sada možemo pristupiti ili izmeniti njegove podatke sa bilo kog mesta na našoj stranici:

```html
<div x-data>
    <template x-for="tab in $store.tabs.items">
        ...
    </template>
</div>

<div x-data>
    <button @click="$store.tabs.current = 'first'">First Tab</button>
    <button @click="$store.tabs.current = 'second'">Second Tab</button>
    <button @click="$store.tabs.current = 'third'">Third Tab</button>
</div>
```

[Sadržaj](00_Sadržaj.md)

### 2.3 Šabloniranje

Alpine nudi nekoliko korisnih direktiva za manipulisanje DOM-om na veb stranici.

Hajde da ovde pokrijemo nekoliko osnovnih direktiva za šablone, ali obavezno pogledajte dostupne direktive u bočnoj traci za iscrpan spisak.

**Tekstualni sadržaj**:

Alpine olakšava kontrolu tekstualnog sadržaja elementa pomoću x-textdirektive.

```html
<div x-data="{ title: 'Start Here' }">
    <h1 x-text="title"></h1>
</div>
```

Sada će Alpine podesiti tekstualni sadržaj `<h1>` na vrednost title("Start here"). Kada se `title` promeni, promeniće se i sadržaj `<h1>`.

Kao i sve direktive u Alpine-u, možete koristiti bilo koji JavaScript izraz koji želite. Na primer:

```html
<span x-text="1 + 2"></span>

3
```

`x-text` elementa `<span>` sada sadrži zbir brojeva „1“ i „2“.

**Prebacivanje elemenata unutar šablona**:

Prebacivanje elemenata je uobičajena potreba na veb stranicama i aplikacijama. Padajući meniji, modalni prozori, dijalozi, "prikaži još", itd... su svi dobri primeri.

AlpineJS nudi direktive `x-show` i `x-if` za prebacivanje elemenata na stranici.

***x-show***

Evo jednostavne komponente za prebacivanje koja koristi `x-show`.

```html
<div x-data="{ open: false }">
    <button @click="open = ! open">Expand</button>
    <div x-show="open">
        Content...
    </div>
</div>
```

Sada će ceo `<div>` sadržaj biti prikazan i skriven na osnovu vrednosti `open`.

Ispod haube, Alpine dodaje CSS svojstvo `display: none;` elementu kada bi trebalo da bude skriven.

Ovo dobro funkcioniše u većini slučajeva, ali ponekad ćete možda želeti da potpuno dodate i uklonite element iz DOM-a. To je ono čemu `x-if` služi.

***x-if***

Evo istog prekidača od ranije, ali ovaj put se koristi x-ifumesto x-show.

```html
<div x-data="{ open: false }">
    <button @click="open = ! open">Expand</button>
    <template x-if="open">
        <div>
            Content...
        </div>
    </template>
</div>
```

Obratite pažnju da x-ifmora biti deklarisano na `<template>` oznaci. Ovo je da bi Alpine mogao da iskoristi postojeće ponašanje elementa u pregledaču `<template>` i da ga koristi kao izvor cilja `<div>` koji će biti dodat i uklonjen sa stranice.

Kada je `open` vrednost "true", Alpine će dodati `<div>` oznaci `<template>`, a ukloniti je kada je `open` vrednost "false".

**Prebacivanje sa prelazima**:

Alpine olakšava glatki prelazak između „prikazanog“ i „skrivenog“ stanja koristeći x-transitiondirektivu.

x-transition Radi samo sa x-show, ne sa x-if.

Evo, ponovo, jednostavnog primera prebacivanja, ali ovaj put sa primenjenim prelazima:

```html
<div x-data="{ open: false }">
    <button @click="open = ! open">Expands</button>
    <div x-show="open" x-transition>
        Content...
    </div>
</div>
```

Hajde da zumiramo deo šablona koji se bavi prelazima:

```html
<div x-show="open" x-transition>
```

x-transition samo po sebi će primeniti razumne podrazumevane prelaze (zatamnjenje i skaliranje) na prekidač.

Postoje dva načina za prilagođavanje ovih prelaza:

- Pomoćnici u tranziciji
- Prelazne CSS klase.

Hajde da pogledamo svaki od ovih pristupa:

***Pomoćnici u tranziciji***

Recimo da želite da produžite trajanje prelaza, to možete ručno odrediti koristeći .durationmodifikator na sledeći način:

```html
<div x-show="open" x-transition.duration.500ms>
```

Sada će prelaz trajati 500 milisekundi.

Ako želite da odredite različite vrednosti za prelaze unutra i napolje, možete koristiti x-transition:enter`and` x-transition:leave:

```html
<div
    x-show="open"
    x-transition:enter.duration.500ms
    x-transition:leave.duration.1000ms
>
```

Pored toga, možete dodati ili .opacityili .scaleda biste samo prešli na to svojstvo. Na primer:

```html
<div x-show="open" x-transition.opacity>
```

***Prelazne klase***

Ako vam je potrebna preciznija kontrola nad prelazima u vašoj aplikaciji, možete primeniti određene CSS klase u određenim fazama prelaza koristeći sledeću sintaksu (ovaj primer koristi Tailwind CSS ):

```html
<div
    x-show="open"
    x-transition:enter="transition ease-out duration-300"
    x-transition:enter-start="opacity-0 transform scale-90"
    x-transition:enter-end="opacity-100 transform scale-100"
    x-transition:leave="transition ease-in duration-300"
    x-transition:leave-start="opacity-100 transform scale-100"
    x-transition:leave-end="opacity-0 transform scale-90"
>...</div>
```

**Atribut `bind`**

Možete dodati HTML atribute kao što su `class`, `style`, `disabled`, itd... elementima u AlpineJS koristeći `x-bind` direktivu.

Evo primera dinamički vezanog `class` atributa:

```html
<button
    x-data="{ red: false }"
    x-bind:class="red ? 'bg-red' : ''"
    @click="red = ! red"
>
    Toggle Red
</button>
```

Kao prečicu, možete izostaviti i direktno x-bindkoristiti skraćenu sintaksu::

```html
<button ... :class="red ? 'bg-red' : ''">
```

Uključivanje i isključivanje klasa na osnovu podataka unutar Alpine-a je uobičajena potreba. Evo primera uključivanja/isključivanja klase koristeći Alpine-ovu classsintaksu povezivanja objekata:

(Napomena: ova sintaksa je dostupna samo za classatribute)

```html
<div x-data="{ open: true }">
    <span :class="{ 'hidden': ! open }">...</span>
</div>
```

Sada će `hidden` klasa biti dodata elementu ako je `open` `false`, i uklonjena ako je `open` `true`.

**Elementi petlje u šablonima**:

Alpine omogućava ponavljanje delova vašeg šablona na osnovu JavaSkript podataka koristeći x-fordirektivu. Evo jednostavnog primera:

```html
<div x-data="{ statuses: ['open', 'closed', 'archived'] }">
    <template x-for="status in statuses">
        <div x-text="status"></div>
    </template>
</div>
```

```sh
open
closed
archived
```

Slično kao `x-if`, `x-for` mora se primeniti na `<template>` oznaku. Interno, AlpineJS će dodati sadržaj `<template>` oznake za svaku iteraciju u petlji.

Kao što vidite, nova `status` promenljiva je dostupna u opsegu iterativnih šablona.

**Unutrašnji HTML**:

AlpineJS olakšava kontrolu HTML sadržaja elementa pomoću `x-html` direktive.

```html
<div x-data="{ title: '<h1>Start Here</h1>' }">
    <div x-html="title"></div>
</div>
```

Sada će Alpine podesiti tekstualni sadržaj elementa `<div>` pomoću `<h1>Start Here</h1>`. Kada se `title` promeni, promeniće se i sadržaj elementa `<h1>`.

> [!Note]
> Koristite samo na pouzdanom sadržaju, a nikada na sadržaju koji pružaju korisnici. Dinamičko prikazivanje HTML-a od trećih strana može lako dovesti do XSS ranjivosti.

[Sadržaj](00_Sadržaj.md)

### 2.4 Događaji

Alpine olakšava praćenje događaja u pregledaču i reagovanje na njih.

**Osluškivanje jednostavnih događaja**:

Korišćenjem `x-on`, možete slušati događaje pregledača koji se šalju na ili unutar elementa.

Evo osnovnog primera slušanja klika na dugme:

```html
<button x-on:click="console.log('clicked')">...</button>
```

Kao alternativu, možete koristiti skraćenu sintaksu događaja ako želite:. @Evo istog primera kao i pre, ali koristeći skraćenu sintaksu (koju ćemo koristiti od sada):

```html
<button @click="...">...</button>
```

Pored click, možete da slušate bilo koji događaj pregledača po imenu. Na primer: @mouseenter, @keyup, itd... su sve validne sintakse.

**Slušanje određenih tastera**:

Recimo da želite da slušate da li enterse taster pritiska unutar `<input>` elementa. AlpineJS to olakšava dodavanjem `.enter`:

```html
<input @keyup.enter="...">
```

Možete čak kombinovati modifikatore tastera da biste slušali kombinacije tastera kao što je pritiskanje `enter` dok držite `shift`:

```html
<input @keyup.shift.enter="...">
```

**Sprečavanje neizvršenja obaveza**:

Prilikom reagovanja na događaje u pregledaču, često je potrebno "sprečiti podrazumevano ponašanje" (sprečiti podrazumevano ponašanje događaja u pregledaču).

Na primer, ako želite da slušate slanje obrasca, ali da sprečite pregledač da pošalje zahtev za obrazac, možete koristiti `.prevent`:

```html
<form @submit.prevent="...">...</form>
```

Takođe možete da se prijavite `.stop` da biste postigli ekvivalent od `event.stopPropagation()`.

**Pristup objektu događaja**:

Ponekad ćete možda želeti da pristupite objektu događaja izvornog pregledača unutar sopstvenog koda. Da bi ovo olakšao, AlpineJS automatski ubrizgava `$event` magičnu promenljivu:

```html
<button @click="$event.target.remove()">Remove Me</button>
```

**Slanje prilagođenih događaja**:

Pored slušanja događaja pregledača, možete ih i otpremiti. Ovo je izuzetno korisno za komunikaciju sa drugim AlpineJS komponentama ili pokretanje događaja u alatima van samog AlpineJS.

AlpineJS otkriva magičnog pomoćnika `$dispatch` koji je za ovo pozvan:

```html
<div @foo="console.log('foo was dispatched')">
    <button @click="$dispatch('foo')"></button>
</div>
```

Kao što vidite, kada se klikne na dugme, Alpine će poslati događaj pregledača pod nazivom "foo", a naš @fooslušalac na `<div>` će ga preuzeti i reagovati na njega.

**Osluškivanje događaja na prozoru**:

Zbog prirode događaja u pregledaču, ponekad je korisno slušati događaje na objektu prozora najvišeg nivoa.

Ovo vam omogućava potpunu komunikaciju između komponenti kao u sledećem primeru:

```html
<div x-data>
    <button @click="$dispatch('foo')"></button>
</div>
<div x-data @foo.window="console.log('foo was dispatched')">...</div>
```

U gornjem primeru, ako kliknemo na dugme u prvoj komponenti, AlpineJS će poslati događaj "foo". Zbog načina na koji događaji funkcionišu u pregledaču, oni se "proširuju" kroz nadređene elemente sve do "prozora" najvišeg nivoa.

Sada, pošto u našoj drugoj komponenti slušamo "foo" na prozoru (sa `.window`), kada se klikne na dugme, ovaj slušalac će to prikupiti i zabeležiti poruku "foo was dispatched".

[Sadržaj](00_Sadržaj.md)

### 2.5 Životni ciklus

AlpineJS ima nekoliko različitih tehnika za povezivanje sa različitim delovima svog životnog ciklusa. Hajde da prođemo kroz najkorisnije da biste se upoznali sa:

**Inicijalizacija elementa**:

Još jedna izuzetno korisna karakteristika životnog ciklusa u Alpine-u je `x-init` direktiva.

`x-init` može se dodati bilo kom elementu na stranici i izvršiće bilo koji JavaScript koji pozovete unutar njega kada AlpineJS počne sa inicijalizacijom tog elementa.

```html
<button x-init="console.log('Im initing')">
```

Pored direktive, Alpine će automatski pozivati sve init() metode sačuvane na objektu podataka. Na primer:

```js
Alpine.data('dropdown', () => ({

    init() {

        // I get called before the element using this data initializes.

    }

}))
```

**Nakon promene stanja**:

AlpineJS vam omogućava da izvršite kod kada se deo podataka (stanje) promeni. Nudi dva različita API-ja za takav zadatak: `$watch` i `x-effect`.

***$watch***

```html
<div x-data="{ open: false }" x-init="$watch('open', value => console.log(value))">
```

Kao što vidite gore, `$watch` omogućava vam da se povežete sa promenama podataka pomoću tačkaste notacije. Kada se taj deo podataka promeni, AlpineJS će pozvati prosleđeni povratni poziv i proslediti mu novu vrednost, zajedno sa starom vrednošću pre promene.

***x-effect***

`x-effect` koristi isti mehanizam ispod haube kao `$watch` ali ima veoma drugačiju upotrebu.

Umesto da navedete koji ključ podataka želite da pratite, `x-effect` poziva se dati kod i inteligentno traži sve Alpine podatke koji se koriste u njemu. Sada, kada se jedan od tih podataka promeni, `x-effect` izraz će se ponovo pokrenuti.

Evo istog dela koda iz $watchprimera prepisanog pomoću `x-effect`:

```html
<div x-data="{ open: false }" x-effect="console.log(open)">
```

Sada će ovaj izraz biti pozvan odmah i ponovo pozvan svaki put kada se `open` ažurira.

Dve glavne razlike u ponašanju kod ovog pristupa su:

- Navedeni kod će se pokrenuti odmah I kada se podaci promene ( `$watch` je „lenj“ -- neće se pokrenuti dok se podaci ne promene )
- Nema saznanja o prethodnoj vrednosti. (Povratni poziv je dat za `$watch` prijem i nove I stare vrednosti )

**AlpsineJS inicijalizacija**:

***alpine:init***

Obezbeđivanje da se deo koda izvrši nakon što se Alpine učita, ali pre nego što se inicijalizuje na stranici je neophodan zadatak.

Ova udica vam omogućava da registrujete prilagođene podatke, direktive, magije itd. pre nego što AlpineJS uradi svoje na stranici.

Možete se povezati sa ovom tačkom u životnom ciklusu tako što ćete osluškivati događaj koji je AlpineJS pozvao: `alpine:init`

```js
document.addEventListener('alpine:init', () => {
    Alpine.data(...)
})
```

***alpine:initialized***

Alpine takođe nudi udicu koju možete koristiti za izvršavanje koda nakon što se završi inicijalizacija, a koja se zove `alpine:initialized`:

```js
document.addEventListener('alpine:initialized', () => {
    //
})
```

[Sadržaj](00_Sadržaj.md)
