
# Kako koristiti klase u JSu

[Nazad](README.md)

Da li ste radoznali u vezi sa klasama u JSu, ali ste pomalo zbunjeni kako funkcionišu ili zašto biste ih uopšte koristili? Ako ste to vi, onda ste definitivno na pravom mestu. Mnogi programeri smatraju da su klase malo komplikovane u početku, i iskreno, i ja sam jednom bio u istoj situaciji.

Ovaj članak je za vas ako vam nešto od ovoga zvuči poznato:

- JS je vaš prvi programski jezik.
- Novi ste u svetu objektno orijentisanog programiranja (OOP) ili vam nisu sasvim jasni principi.
- Prvenstveno ste koristili funkcije za strukturiranje vašeg JavaScript koda.

Ako se slažete sa bilo čim od ovoga, onda nastavite da čitate.

U ovom članku, korak po korak ćemo vam pokazati kako se objektno orijentisano programiranje implementira u JSu pomoću objekata i konstruktorskih funkcija i jasno ćemo ilustrovati zašto će vas razumevanje i korišćenje klasa učiniti svestranijim i efikasnijim JS programerom, čak i ako ste navikli da sve pišete u funkcijama. Završićemo sve jednostavnim primerom aplikacije sa zadatakom kako biste mogli da vidite kako se koriste klase.

## 1 Funkcije

Ako ste počeli sa JSom, verovatno ste se već navikli na funkcije. One su vam kao gradivni blokovi svega, zar ne? Razmislite o tome: ako bih vas zamolio da napišete program koji pozdravlja nekoga po imenu, verovatno biste u trenutku napravili nešto ovako:

```js
function greetUser(userName) {
    alert("Hello, " + userName + "!");
}

greetUser("Alice"); // Like magic! It greets Alice.
```

U redu, hajde malo da podignemo nivo. Zamislite da vas zamolim da napišete program koji izračunava nečiju godinu rođenja samo znajući njihove godine. Ako imaju 25 godina, želeli biste da im kaže "2000" (pod pretpostavkom da je trenutna godina 2025).

Šta bi vam bila prva pomisao? Verovatno nešto poput: "Vreme je za funkciju!" Jesam li u pravu? Pomislili biste: "Napisaću funkciju; ona će uzeti godine i bum, izbaciti godinu rođenja." Vidite?

Razmišljanje usmereno ka funkcijama. Potpuno prirodno u JSu. A evo kako biste to mogli da kodirate:

```js
function getBirthYear(age) {
    const currentYear = 2025; //  For this example, let's say it's 2025
    const birthYear = currentYear - age;
    return birthYear;
}
console.log(getBirthYear(25)); // Yep, it logs 2000!
```

Sada, hajde da to malo usložimo. Šta ako želimo da budemo malo pametniji i da se uverimo da je starost zaista validna? Znate, ne neki ludi niz ili negativan broj. Držeći se naših mozgova koji vole funkcije, koji je prirodni sledeći korak? Naravno, još jedna funkcija. Verovatno bismo kreirali funkciju validateAge:

```js
function validateAge(age) {
    if (typeof age !== "number" || age <= 0 || age > 120) {
      return "Invalid age";
    } else {
      return age; //  Age is good to go!
    }
}

console.log(validateAge(25)); //  Output: 25 (valid!)
console.log(validateAge("twenty")); //  Output: Invalid age (not a number)
console.log(validateAge(-5)); //  Output: Invalid age (negative)
```

Vidite kako samo gomilamo funkcije? "getBirthYear" radi jedno, "validateAge" radi drugo. To su odvojene male kutijice koda.

Hajde da ovo malo proširimo. Šta ako bismo želeli da utvrdimo i nečiji horoskopski znak na osnovu godine rođenja? Da, pogodili ste – mozak kaže: "Još funkcija." Hajde da napišemo još jednu "getZodiacSign" funkciju:

```js
function getZodiacSign(birthYear) {
    const signs = [
        "Monkey",
        "Rooster",
        "Dog",
        "Pig",
        "Rat",
        "Ox",
        "Tiger",
        "Rabbit",
        "Dragon",
        "Snake",
        "Horse",
        "Sheep"
    ];
    return signs[birthYear % 12]; // Simple modulo trick!
}
```

Da li primećujete obrazac ovde? Za svaku novu stvar koju želimo da uradimo, samo dodajemo sve više i više odvojenih funkcija. Stvari počinju da deluju pomalo ... raštrkano, zar ne? A nismo čak ni završili sa dodavanjem funkcija.

U redu, recimo sada da želimo da sačuvamo još više informacija o osobi - njeno ime, zemlju, profesiju, pored samo godina. Kako bismo sve ovo upravljali našim pristupom usmerenim na funkcije? Pa, mogli bismo pokušati da napravimo veliku funkciju "Osoba" koja uzima sve ove informacije:

```js
function Person(name, age, country, profession) {
    const personName = name;
    const personAge = age;
    const personCountry = country;
    const personProfession = profession;
  
    const validatedAge = validateAge(personAge);
    const birthYear = getBirthYear(validatedAge);
    const zodiacSign = getZodiacSign(birthYear);
  
    alert(
        `${personName}, you're ${personAge} years old, born in ${birthYear}, zodiac sign: ${zodiacSign}!`
    );
}
```

Šta ako zatim želimo da koristimo ime osobe u našim drugim funkcijama, kao što su "getZodiacSign" ili "getBirthYear"? Morali bismo da se vratimo i ručno dodamo "name" kao argument svakoj od tih funkcija. Zamislite da morate da ažurirate svaku funkciju svaki put kada dodate novi podatak o osobi.

```js
//  Suddenly, we need 'name' everywhere!

function getZodiacSign(birthYear, name) {
    alert("Zodiac sign for " + name + " is...");
    //... rest of zodiac logic...
}

function getBirthYear(age, name) {
    alert("Birth year for " + name + " is...");
    // ... rest of birth year logic...
}
```

U ovom malom primeru, to je donekle upravljivo. Ali zamislite ogroman projekat sa mnoštvom funkcija raspoređenih po datotekama i fasciklama, kako biste pokušali da sve držite sinhronizovanim i ažurirate funkcije kad god se vaši "person" podaci promene. To zvuči kao recept za glavobolje, greške i mnogo frustracije. Može postati neverovatno neefikasno i, iskreno, prilično sklono greškama.

### 1.2 Da li da kažemo da su funkcije loše

Funkcije su neverovatne. Zamislite ovaj pristup fokusiran na funkcije kao "klasičan JS način" rada. Ako ste počeli sa JSom, ovo vam se verovatno čini potpuno prirodnim i prijatnim – i to je odlično. Čak su i super popularne moderne biblioteke poput React-a izgrađene pomoću funkcija za komponente. Funkcije su neverovatno moćne i fleksibilne.

Ali, čak i u React-u, ako promenite neke osnovne podatke (kao što je "prop" u React terminologiji) u glavnoj komponenti, možda ćete morati da pretražite mnoge druge komponente kako biste bili sigurni da sve i dalje radi glatko. Funkcije su fantastične, ali ponekad, za određene vrste problema, može postojati drugi način da organizujemo naš kod. Način koji se nekim ljudima čini intuitivnijim, posebno ako dolaze iz drugih programerskih sredina.

Zamislite da zamolite programera čiji je maternji jezik Java ili C++ da napravi naš "birth year" program. Možda bi mu se mozak zasvetleo, ali bi verovatno pomislio nešto malo drugačije. Možda nešto poput ovoga:

Potreban nam je "Person" (class). A "Person" ima "age" (proterty) i potreban nam je način da "calculateBirthYear" (action) dobijemo "Person".

Primećujete li nešto drugačije? Funkcije nisu prva stvar koja im padne na pamet. Više se radi o tome da "objects things" imaju "properties" i "actions". Zapanjujuće, zar ne? Mnogi programeri koji su počeli sa jezicima poput Jave ili C++ prirodno razmišljaju u ovom objektno orijentisanom (ili OOP) stilu. I hej, možda zato ovo čitate - možda ste radoznali da istražite i ovaj pristup objektnog razmišljanja, posebno u JavaSkriptu. Ne brinite, ne tražim od vas da iznenada pređete na Javu 😉.

Dakle, o ovim klasama u JavaSkriptu. Spremite se za mali JavaSkript preokret. Stvar je sledeća: JavaSkript tehnički nema klase na način na koji ih imaju jezici poput Jave ili C++. Znam, može biti malo zbunjujuće. Umesto klasičnih klasa kakve se nalaze u jezicima poput Jave ili C++, JavaSkript je izgrađen na nečemu što se zove **prototipovi**. Koristi ove fleksibilne prototipove i objekte da bi imitirao kako klase funkcionišu u drugim jezicima. Dakle, ako želite efikasno da koristite klase u JavaSkriptu, pravi ključ je prvo razumeti objekte i prototipove. Tu leži magija JavaSkript objektno-optičkog programiranja (OOP).

### 1.3 JS nema prave klase

Da li to znači da smo zauvek zaglavljeni samo sa funkcijama? Ne. Iako JS radi stvari na svoj način sa prototipovima (umesto klasičnih klasa), ona i dalje u potpunosti podržava "objektno orijentisano programiranje" (OOP).

Hajde da objasnimo OOP jednostavnim jezikom. Dve velike ideje u OOP-u su enkapsulacija i nasleđivanje. Zvuči otmeno, zar ne? Ali to su zapravo prilično jednostavni koncepti.

Enkapsulacija? Zamislite kapsulu, kao za lekove. Samo objedinjujete stvari koje pripadaju zajedno. U objektno-orientiranom programiranju, enkapsulacija znači grupisanje podataka (kao što su godine, ime) i radnji koje možete da uradite sa tim podacima (kao što je izračunavanje godine rođenja, pozdrav) unutar jednog "objekta". JS objekti su savršeni za ovo.

A nasleđivanje? Zamislite to kao nasleđivanje osobina od vaše porodice. U JavaScript objektno-optičkom programiranju, objekti mogu da "nasleđuju" svojstva i ponašanja od drugih objekata. JavaScript ovo naziva prototipskim nasleđivanjem, a objekat od kog nasleđujete naziva se prototip (uskoro ćemo detaljnije razmotriti prototip).

Vidite? Ovde nema zatvora funkcija. JS je potpuno spreman za objektno-optičko programiranje (OOP). Da bismo ovo videli u akciji, hajde da prepišemo naš program za godinu rođenja, ali ovaj put koristeći ovaj OOP stil u JSu.

Pogledajte ovo. Evo kako bismo mogli da prepišemo naš program za godinu rođenja koristeći OOP stil u JSu, koristeći samo dobri stari JS objekat:

```js
const Person = {
    //  --- Properties (Data) ---
    name: "Spruce",
    age: 25,
    country: "Nigeria",
    profession: "Engineer",
  
    //  --- Methods (Actions related to Person data) ---
    isValidAge: function () {
        return typeof this.age === "number" && this.age > 0;
    },
  
    getBirthYear: function () {
        if (!this.isValidAge()) {
            return "Invalid age!";
        }
        return new Date().getFullYear() - this.age;
    },
  
    getZodiacSign: function () {
        if (!this.isValidAge()) {
            return "Oops, can't get zodiac for an invalid age!";
        }
    
        const birthYear = this.getBirthYear();
        const zodiacSigns = [
            "Capricorn",
            "Aquarius",
            "Pisces",
            "Aries",
            "Taurus",
            "Gemini",
            "Cancer",
            "Leo",
            "Virgo",
            "Libra",
            "Scorpio",
            "Sagittarius",
        ];
        return zodiacSigns[birthYear % 12];
    },
  
    greet: function () {
        return (
            `Hello, I'm ${this.name}. I'm ${
                this.age
            } years old, born in ${this.getBirthYear()}, ` +
                `working as a ${this.profession} from ${
                this.country
            }.  My zodiac sign is ${this.getZodiacSign()}.`
        );
    },
};

//  --- Let's use our Person object! ---
console.log(Person.greet());

//  Output (might vary slightly depending on year):

// "Hello, I'm Spruce. I'm 25 years old, born in 2000, working as a Engineer from Nigeria.  My zodiac sign is Pig."
```

Vidite kako je to uredno? Sve o osobi "Person", njeni detalji (ime, godine itd.) i šta možete da uradite sa osobom (proverite godine, dobijete godinu rođenja, pozdravite) je sve objedinjeno i lepo organizovano unutar ovog jednog "Person" objekta. To je enkapsulacija u akciji. Prilično kul, zar ne?

Sada, želite da znate "Person" "ime"? Veoma jednostavno:

```js
console.log(Person.name); // Output: "Spruce"
```

Godina rođenja? Manja prašina:

```js
console.log(Person.getBirthYear()); // Output (if current year is 2025): 2000
```

A evo i prave magije enkapsulacije: ako promenimo nešto unutar "Person" objekta (na primer, odlučimo da promenimo starost), sve metode (akcije) unutra se automatski prilagođavaju. Ne moramo da tražimo po odvojenim funkcijama da bismo ažurirali stvari. Dozvolite mi da vam pokažem:

```js
//  Age is 25 initially...
console.log("Birth year when age is 25:", Person.getBirthYear()); // Output (if current year is 2025): 2000

//  Let's update the age directly in the Person object...
Person.age = 30;

//  Now, getBirthYear automatically uses the *new* age!
console.log("Birth year when age is 30:", Person.getBirthYear()); // Output (if current year is 2025): 1995
```

Dakle, JS koristi objekte — i, kao što ćemo videti, prototipove — da bi oživeo OOP, čak i ako nema klasične klase. Nadamo se da počinjete da uviđate privlačnost organizovanja koda na ovaj način. Pre nego što pređemo na klase, ima mnogo smisla da steknemo zaista dobro razumevanje objekata i prototipova u JSu, zar ne? To je ono u šta ćemo se sledeće pozabaviti.

## 2 Objekti u JSu

Ako ste već upoznati sa načinom funkcionisanja objekata, to je fantastično. To će vam olakšati razumevanje svega što obrađujemo u ovom članku. Da bismo bili sigurni da smo svi na istoj stranici, počnimo sa veoma osnovnim objektom:

```js
const Person = {};
```

Dakle, da li je "Person" objekat prazan? Na prvi pogled, svakako izgleda prazno. Ako ste pomislili "da", niste sami. To je uobičajena početna pomisao. Ali u JSu, objekti su malo zanimljiviji od onoga što eksplicitno stavljamo u njih. Hajde da istražimo kako objekti zaista funkcionišu "ispod haube".

### 2.1 Kako objekti funkcionišu u JSu

Hajde da to razložimo. U svojoj suštini, objekat je kolekcija svojstava. Zamislite svojstva kao imenovane kontejnere za vrednosti. Svako svojstvo ima ime (takođe se naziva "ključ").

```js
const Person = {
    firstName: "John",
    lastName: "Doe",
};
```

"firstName" i "lastName" su imena svojstava (ključevi), a "John" i "Doe" su njihove odgovarajuće vrednosti. Svojstvo u objektu je uvek par ključ-vrednost. Deo sa vrednošću može biti mnogo stvari.

Vrednost povezana sa svojstvom može biti primitivni tip podataka. U JSu, primitivi su stvari poput:

- stringova,
- brojeva,
- bulovih vrednosti ( `true` ili `false` ),
- null,
- undefined i
- simbola.

Pogledajmo neke primere:

```js
const exampleObject = {
    name: "Example", // String
    age: 30, // Number
    isStudent: false, // Boolean
    favoriteColor: null, // null
};
```

Ali sjajno je to što vrednosti svojstava mogu biti i složeniji tipovi podataka ili čak drugi objekti, funkcije i nizovi. Pogledajmo to:

```js
const anotherObject = {
    address: {
        // Value is another object
        street: "123 Main St",
        city: "Anytown",
    },
    hobbies: ["reading", "hiking"], // Value is an array
    greet: function () {
        // Value is a function (a method!)
        console.log("Hello!");
    },
};
```

Kada je funkcija svojstvo objekta, nazivamo je metodom. To je u suštini funkcija koja pripada objektu i obično operiše na podacima objekta.

```js
const calculator = {
    value: 0,
    add: function(number) {
        this.value += number; // 'this' refers to the calculator object
    },
    getValue: function() {
        return this.value;
    }
};

calculator.add(5);
console.log(calculator.getValue()); // Output: 5
```

E, tu stvari postaju zaista zanimljive. Objekti u JSu nemaju samo svojstva koja eksplicitno definišemo. Oni takođe mogu da referenciraju svojstva drugih objekata. Ovo je osnovni koncept koji se naziva "prototipsko nasleđivanje" (ponekad se naziva samo "prototipska delegacija").

Sećate se našeg naizgled praznog "Person = {}" objekta? Rekli smo da izgleda prazno, zar ne? Pa, vreme je za malo JS magije. Iako mu nismo sami dodali nikakva svojstva, nije potpuno prazan. Svaki objekat u JSu, podrazumevano, ima skrivenu vezu (često interno nazivanu njegovim svojstvom "Prototype") do drugog objekta koji se naziva njegov prototip.

Za objekte kreirane korišćenjem jednostavne `{}` sintakse (kao što je naš "Person" objekat), njihov podrazumevani prototip je ugrađeni `Object.prototype`. Zamislite `Object.prototype` kao neku vrstu roditeljskog objekta koji pruža neke osnovne, ugrađene funkcionalnosti svim objektima.

Zato možete da radite ovakve stvari, čak i sa našim "praznim" "Person" objektom:

```js
console.log(Person.toString()); // Output: [object Object]
```

Čekajte malo. Nikada nismo definisali `toString()` metodu u našem "Person" objektu. Pa odakle dolazi? Dolazi iz njegovog prototipa, `Object.prototype`. `toString()` je metoda koja je ugrađena u `Object.prototype`, i pošto je Person prototip `Object.prototype`, "Person" može da pristupi i koristi `toString()` metodu.

Dakle, dobar način da se razmisli o tome je: "Prototip objekta je drugi objekat iz kojeg može da pretraži i koristi svojstva i metode ako ih sam nema."

Zašto je razumevanje prototipova toliko važno? Zato što otključava moć ponovne upotrebe koda i kreiranja specijalizovanih objekata zasnovanih na opštijim. Ovde stvari postaju zaista moćne, posebno kako vaši JavaScript projekti rastu.

Zamislite da želimo da kreiramo specifičniji tip "Person" — recimo, "Developer". Objekat "Developer" je i dalje Person, ali može imati neka dodatna svojstva ili ponašanja specifična za programere. U osnovi, želimo da "Developer" objekat bude Person, ali takođe da ima svoje jedinstvene stvari.

Ovde možemo eksplicitno podesiti prototipove. Umesto da se oslanjamo na podrazumevani `Object.prototype`, možemo reći JavaScript-u: "Hej, želim da prototip mog "Developer" objekta bude P"erson" objekat koji smo već definisali." To možemo uraditi koristeći `Object.create()`:

```js
const Person = {
    firstName: "John",
    lastName: "Doe",
    sayHello: function () {
      console.log(`Hello, my name is ${this.firstName} ${this.lastName}`);
    },
};

const developer = Object.create(Person); // developer's prototype is now 'Person'
developer.firstName = "Spruce"; // Add a *specific* firstName for developer
developer.programmingLanguage = "JavaScript"; // Developer's own property

developer.sayHello(); // Output: Hello, my name is Spruce Person (still accesses sayHello from 'person' prototype!)
console.log(developer.programmingLanguage); // Output: JavaScript (developer's own property)
console.log(developer.lastName); // Output: Doe (inherited from 'Person' prototype!)
```

Hajde da analiziramo šta se dešava kada pristupamo svojstvima na Developer:

```js
console.log(developer.firstName); // Output: Spruce (developer's *own* property)
console.log(developer.programmingLanguage); // Output: JavaScript (developer's *own* property)
console.log(developer.lastName); // Output: Doe (found on the *prototype* 'Person')
console.log(developer.sayHello()); // Output: Hello, my name is Spruce Person (method from *prototype*)
console.log(developer.job); // Output: undefined (not on 'Developer' OR 'Person' prototype)
```

Kada pokušate da pristupite svojstvu kao što je Developer.lastName, JS radi sledeće:

- Prvo, proverava: Da li "Developer" ima svojstvo imenovano "lastName" direktno na sebi? U našem primeru, "Developer" ima samo "firstName" i "programmingLanguage" kao svoja svojstva. "lastName" nije tamo.

- Ako ga ne pronađe na samom objektu, JS zatim pretražuje prototip objekta (koji postavljamo na "Person" korišćenjem `Object.create()`).

- Proverava: Da li "Person" objekat (prototip) ima svojstvo pod nazivom "lastName"? Da, "Person" ima "lastName": "Doe". Dakle, JS koristi ovu vrednost.

- Ako svojstvo nije pronađeno ni na prototipu, JS bi zatim pogledao "Person" prototipu (što je `Object.prototype` podrazumevano) i tako dalje, uz lanac prototipa. Ako ide sve do gore po lancu i još uvek ne pronađe svojstvo, konačno vraća `undefined` (kao kada smo pokušali da pristupimo "developer.jobs").

Sopstvena svojstva su jednostavno svojstva koja su definisana direktno na samom objektu kada ga kreirate (kao što su "firstName" i "programmingLanguage" na Developer objektu). Svojstvima prototipa se pristupa kroz lanac prototipa.

Možete čak kreirati i duže lance prototipova. Na primer, recimo da želimo da kreiramo "JavaScript Developer" objekat, koji je tipa "Developer":

```js
const JavaScriptDeveloper = Object.create(Developer); // javaScriptDeveloper's prototype is 'Developer'

JavaScriptDeveloper.framework = "React"; // JavaScriptDeveloper's own property

console.log(JavaScriptDeveloper.firstName); // Output: Spruce (from 'Developer' prototype)
console.log(JavaScriptDeveloper.lastName); // Output: Doe (from 'Person' prototype)
console.log(JavaScriptDeveloper.programmingLanguage); // Output: JavaScript (from 'Developer' prototype)
console.log(JavaScriptDeveloper.framework); // Output: React (JavaScriptDeveloper's own property)
console.log(JavaScriptDeveloper.job); // Output: undefined (not found anywhere in the chain)
```

( Opciono istraživanje: Ako ste radoznali, pratite pretragu za "JavaScriptDeveloper.lastName". Ide: "JavaScriptDeveloper" -> "Developer" -> "Person" -> "Object.prototype" ).

U redu, prototipovi su moćni. Možemo kreirati objekte koji dele svojstva i ponašanja i specijalizovati ih za različite potrebe. Ali zamislite da želimo da kreiramo stotine "Person" objekata, stotine "Developer" objekata i stotine "JavaScriptDeveloper" objekata.

Korišćenje `Object.create()` svaki put bi i dalje bilo prilično ponavljajuće, posebno ako želimo da osiguramo da "svaki" "Person" počinje istim osnovnim svojstvima (kao što su "firstName" i "lastName").

Potreban nam je bolji način za kreiranje više objekata koji prate isti obrazac, poput plana koji možemo ponovo i iznova koristiti za kreiranje objekata. Zato služe klase, one su samo planovi koje možemo koristiti za kreiranje više objekata, a JS koristi konstruktorske funkcije za kreiranje klasa (planova).

U sledećem odeljku ćemo se pozabaviti kako JS koristi "Konstruktorske funkcije" za implementaciju klasa.

## 3. Funkcije konstruktori

U redu, prototipovi su prilično kul za ponovnu upotrebu koda i pravljenje specijalizovanih objekata. Videli smo kako nam `Object.create()` omogućava da kreiramo objekte koji nasleđuju od drugih. Ali zamislite da želimo da napravimo tone "Person" objekata, recimo, stotine njih za veb lokaciju. Kucanje `Object.create(person)` za svaki pojedinačni objekat bi postalo previše repetitivno, posebno ako uvek želimo da svaki "Person" počinje sa istim osnovnim svojstvima, kao što su "firstName" i "lastName".

Potreban nam je efikasniji način da napravimo mnogo objekata koji prate isti obrazac. Ono što nam zaista treba je nešto poput nacrta — nešto što možemo koristiti iznova i iznova da bismo iscrtali nove objekte, koji svi izgledaju i rade na sličan način. I znate šta? Upravo za to služe konstruktorske funkcije.

Zamislite konstruktorske funkcije kao način na koji JavaScript kreira "nacrte za objekte". One su kao fabrike objekata. A u JavaScript-u koristimo konstruktorske funkcije, koje su specijalizovane funkcije koje se koriste na određeni način, da bismo kreirali ove nacrte. Da, opet funkcije. Ali ih koristimo na poseban način.

### 3.1 Konstruktorska funkcija

Pa, kao što sam rekao, to je funkcija koja kreira objekte. Pogledajte ovaj primer:

```js
function PersonConstructor(name, age) {
    this.name = name;
    this.age = age;
    this.greet = function () {
        console.log(`Hello, I'm ${this.name}`);
    };
}
```

To izgleda kao regularna funkcija. Apsolutno si u pravu. Izgleda baš kao i svaka druga funkcija koju si verovatno napisao u JSu. U stvari, hajde da to dokažemo. Ako samo zabeležimo "PersonConstructor" samu sebe u logu, videćemo:

```js
console.log(PersonConstructor);

// output
function PersonConstructor(name, age) {
    this.name = name;
    this.age = age;
    this.greet = function () {
        console.log(`Hello, I'm ${this.name}`);
    };
}
```

Vidite? Samo obična funkcija. Pa, šta je čini konstruktorskom funkcijom?
Magični sastojak: Ključna reč `new`.

Ono što običnu funkciju pretvara u konstruktor — nešto što gradi objekte — jeste `new` ključna reč. To je kao da kažete JSu: "Hej, tretiraj ovu funkciju kao nacrt i koristi je da mi napraviš novi objekat."

Da vidimo to u akciji:

```js
const person1 = new PersonConstructor("Alice", 25);

console.log(person1);

// output

// PersonConstructor { name: 'Alice', age: 25, greet: [Function] }
```

U sadašnjem izlazu, umesto samo koda funkcije, vidimo objekat "PersonConstructor". Ključna reč `new` nije samo pozvala funkciju, već je zapravo kreirala potpuno novi objekat na osnovu "PersonConstructor" nacrta.

Sada možemo koristiti ovaj plan, "PersonConstructor", da kreiramo onoliko "Person" objekata koliko želimo, svi sa istom osnovnom strukturom:

```js
const person1 = new PersonConstructor("Alice", 25);
const person2 = new PersonConstructor("Bob", 30);
const person3 = new PersonConstructor("Charlie", 28);

console.log(person1);
console.log(person2);
console.log(person3);

// output
PersonConstructor { name: 'Alice', age: 25, greet: [Function] }
PersonConstructor { name: 'Bob', age: 30, greet: [Function] }
PersonConstructor { name: 'Charlie', age: 28, greet: [Function] }
```

Super, zar ne? Imamo tri različita "Person" objekta, svi napravljeni po istom "PersonConstructor" nacrtu.

Čekaj... Koja je ovo `this` ključna reč koju stalno vidim?

Verovatno ste primetili da se reč `this` često pojavljuje u ovim primerima koda, kao u `this.name`, `this.age` i `this.greet()`. I možda mislite: "Šta je, u svetu JavaSkripta `this`?"

Ne brinite, `this` u početku može biti malo zbunjujuće, ali je zapravo prilično jednostavno kada savladate. Hajde da to razložimo jednostavnom analogijom.

Zamislite da opisujete sebe. Mogli biste reći: "Moje ime je [Tvoje ime]." U ovoj rečenici, "moje" se odnosi na vas, osobu koja govori.

U JavaScript objektima, `this` je kao "my" ili "me". To je način na koji objekat referencira samog sebe.

Prvo ćemo ovo videti na primeru običnog objekta:

```js
const PersonObject = {
    name: "Spruce",
    greet: function () {
        console.log("Hello, my name is " + PersonObject.name); // Using PersonObject.name directly
    },
};

PersonObject.greet(); // Output: Hello, my name is Spruce
```

U ovom "PersonObject", unutar "greet" funkcije, koristili smo "PersonObject.name" za pristup "name" svojstvu. Ovo funkcioniše sasvim dobro. Direktno govorimo JavaScript-u da dobije "name" svojstvo iz "PersonObject". Mogli bismo ovde koristiti i `this`, ali da vidimo zašto `this` postaje veoma korisno, posebno u konstruktorskim funkcijama.

Sada, razmotrite ovu malo drugačiju verziju koristeći `this`:

```js
const PersonObjectThis = {
    name: "Spruce",
    greet: function () {
        console.log("Hello, my name is " + this.name); // Using 'this.name'
    },
};

PersonObjectThis.greet(); // Output: Hello, my name is Spruce
```

Vidite? I dalje radi na isti način. Kada se "greet" pozove na "PersonObjectThis", unutar "greet" funkcije, ona se automatski poziva na "PersonObjectThis". Dakle, "this.name" je samo dinamičniji način da se kaže "name svojstvo ovog trenutnog objekta".

### 3.2 this vs direktnog imenovanja objekta

Zato što je `this` dinamičan i svestan konteksta. Uvek ukazuje na objekat koji trenutno poziva metodu. Ovo postaje neophodno u konstruktorskim funkcijama jer su konstruktorske funkcije dizajnirane da kreiraju mnogo različitih objekata.

Nazad na konstruktorske funkcije: Šta `this` znači?

Hajde da ponovo pogledamo naš "PersonConstructor":

```js
function PersonConstructor(name, age) {
    this.name = name;
    this.age = age;
    this.greet = function () {
        console.log(`Hello, I'm ${this.name}`);
    };
}

const person1 = new PersonConstructor("Alice", 25);
const person2 = new PersonConstructor("Bob", 30);
```

Kada uradimo

```js
const person1 = new PersonConstructor("Alice", 25);
```

unutar PersonConstructorfunkcije `this` postaje "person1". Kao da JavaScript radi:

```js
person1.name = "Alice";
person1.age = 25;
person1.greet = function() { ... };
```

A kada ponovo radimo

```js
const person2 = new PersonConstructor("Bob", 30);
```

unutra PersonConstructor this postaje person2. Kao daJavaScript radi:

```js
person2.name = "Bob";
person2.age = 30;
person2.greet = function() { ... };
```

Dakle, `this` u konstruktoru funkcija je kao rezervisano mesto koje se popunjava određenim objektom koji se kreira kada koristite `new`. To nam omogućava da kreiramo mnogo različitih objekata iz istog nacrta.

### 3.3 Velika potrošnja memorije konstruktorskih funkcija

U redu, sada kada znate kako da kreirate nacrte objekata koristeći konstruktorske funkcije i razumete šta `this` radi, možemo da napravimo mnogo "Person" objekata.

Ali postoji mali problem koji se krije u nama PersonConstructor:

```js
function PersonConstructor(name, age) {
    this.name = name;
    this.age = age;
    this.greet = function () {
        // 😬 Look at this greet function!
        console.log(`Hello, I'm ${this.name}`);
    };
}

const person1 = new PersonConstructor("Alice", 25);
const person2 = new PersonConstructor("Bob", 30);

console.log(person1, person2);

// output

PersonConstructor {name: "Alice", age: 25, greet: function}
PersonConstructor {name: "Bob", age: 30, greet: function}
```

Obratite pažnju na "greet" funkciju unutar "PersonConstructor"? Svaki put kada kreiramo novi "Person" objekat koristeći `new PersonConstructor()`, mi zapravo kopiramo celu "greet" funkciju na svaki objekat.

Zamislite da kreiramo hiljadu "Person" objekata. Imali bismo hiljadu identičnih "greet" funkcija u memoriji. Za jednostavnu "greet()" funkciju, uticaj na memoriju može izgledati mali. Međutim, ako imate složenije metode sa puno koda ili ako kreirate hiljade ili čak milione objekata, dupliranje ovih funkcija za svaki pojedinačni objekat može postati značajno gubljenje memorije.

Takođe utiče na performanse jer JS mora da upravlja svim ovim dupliranim funkcijama. To je mnogo dupliranog koda i nije baš efikasno u smislu memorije, posebno ako je "greet" funkcija (ili druge metode) složenija.

### 3.4 Prototipovi - efikasno deljenje metoda

Sećate se prototipova? Naučili smo da objekti mogu nasleđivati svojstva i metode od svojih prototipova. Pa, konstruktorske funkcije imaju ugrađeni način da koriste prototipove za rešavanje ovog problema gubljenja memorije.

Umesto da definišemo "greet" funkciju unutar konstruktora i tako je kopiramo u svaku instancu, možemo je dodati u instancu `prototype` funkcije "PersonConstructor".

Ovako:

```js
function PersonConstructor(name, age) {
    this.name = name;
    this.age = age;
}

//  --- Add the greet method to the PROTOTYPE of PersonConstructor! ---
PersonConstructor.prototype.greet = function () {
    console.log(`Hello, I'm ${this.name}`);
};
```

Sada "greet" je metoda definisana samo jednom na `PersonConstructor.prototype`. Ali svi objekti kreirani sa PersonConstructor i dalje mogu da je koriste. Oni je nasleđuju od prototipa.

Hajde da to testiramo:

```js
const person1 = new PersonConstructor("Alice", 25);
const person2 = new PersonConstructor("Bob", 30);

person1.greet(); // Output: Hello, I'm Alice  - Still works!
person2.greet(); // Output: Hello, I'm Bob    - Still works!

console.log(person1.greet === person2.greet); // Output: false - They are NOT the same function object in memory

console.log(person1.__proto__.greet === person2.__proto__.greet); // Output: true - But they share the same prototype method!
```

"person1.greet()" i "person2.greet()" i dalje rade savršeno. Ali sada se greet funkcija ne kopira za svaki objekat. Deli se kroz prototip. Ovo je mnogo efikasnije, posebno kada imamo posla sa mnogo objekata i metoda.

### 3.5 Funkcije konstruktori + prototipovi

Sada smo videli kako konstruktorske funkcije deluju kao planovi za kreiranje objekata i kako nam korišćenje prototipa konstruktorske funkcije omogućava efikasno deljenje metoda među svim objektima kreiranim iz tog plana.

Ovo je ključni obrazac u JSu za kreiranje struktura objekata za višekratnu upotrebu.

U redu, pokrili smo kreiranje objekata i efikasne metode... Ali šta je sa nasleđivanjem pomoću konstruktorskih funkcija?

Šta ako želimo da napravimo "DeveloperPerson" plan koji nasleđuje naš "PersonConstructor" plan?
Tako da "DeveloperPerson" objekti automatski imaju "name", "age" i "greet", ali mogu imati i svoja posebna svojstva i metode vezane za programere?

Tu stvari postaju malo složenije sa konstruktorskim funkcijama, i moraćemo da koristimo poseban trik koji se zove `call()` da bi nasleđivanje funkcionisalo. Hajde da se sledeće pozabavimo time.

### 3.6 Nasleđivanje pomoću konstruktorskih funkcija

U redu, dobro napredujemo. Imamo konstruktorske funkcije za kreiranje nacrta objekata i prototipove za efikasno deljenje metoda. Ali jedan od glavnih razloga zašto ljudi koriste OOP je nasleđivanje – ideja kreiranja specijalizovanih objekata koji se nadovezuju na opštije.

Razmislite ponovo o našem primeru "Person" i "Developer". "Developer" je "Person", zar ne? Imaju ime, godine, možda pozdravljaju ljude, ali takođe imaju svojstva specifična za programere, poput omiljenog programskog jezika i sposobnosti kodiranja.

Kako možemo da napravimo "DeveloperPersonConstructor" plan koji nasleđuje sve osnovne "PersonConstructor" stvari, a zatim dodaje sopstvene funkcije specifične za programere?

Sa konstruktorskim funkcijama možete koristiti nešto što se zove `call()`.

#### 3.6.1 call() - Rukovanje tajnim nasledstvom

`call()` je metod funkcije koji vam omogućava da uradite nešto pomalo neobično: možete pozajmiti funkciju iz jednog objekta i pokrenuti je u kontekstu drugog objekta. Zvuči zbunjujuće? Hajde da pojednostavimo.

Da bismo ilustrovali `call()`, razmotrimo naš "PersonConstructor". Želimo da kreiramo "DeveloperPersonConstructor" koji se takođe podešava name i age na isti način kako "PersonConstruc" to radi, pre dodavanja svojstava specifičnih za programera.

Tu `call()` dolazi do izražaja. Možemo koristiti `call()` da u suštini kažemo: "Hej "PersonConstructor", pokreni svoj kod, ali ga pokreni kao da si unutar "DeveloperPersonConstructor", i podesi "name" i "age" za ovaj "DeveloperPerson" objekat koji trenutno kreiramo."

Pogledajmo ovo u kodu da bi bilo jasnije:

```js
function PersonConstructor(name, age) {
    this.name = name;
    this.age = age;
}

PersonConstructor.prototype.greet = function () {
  console.log(`Hello, I'm ${this.name}`);
};

function DeveloperPersonConstructor(name, age, programmingLanguage) {
    //  --- "Borrow" the PersonConstructor to set up name and age! ---
    PersonConstructor.call(this, name, age); //  <--  The magic of 'call()'
  
    // --- Now, add developer-specific properties ---
    this.programmingLanguage = programmingLanguage;
    this.code = function () {
      console.log(`${this.name} is coding in ${this.programmingLanguage}`);
    };
}
```

Vidite li taj red sa call? To je ključ nasleđivanja ovde. Hajde da ga razložimo:

```js
PersonConstructor.call(this, name, age);
```

- `PersonConstructor.call(...)` - Pozivamo funkciju "PersonConstructor", ali ne na uobičajen način. Koristimo `.call()`.
- `this` - Prvi argument `call()` je ključan. On određuje šta `this` treba da bude unutar "PersonConstructor" funkcije kada se pokrene. Ovde prosleđujemo `this` sa "DeveloperPersonConstructor". Zašto? Zato što želimo da "PersonConstructor" podesi "name" i "age" na "DeveloperPerson" objektu koji se trenutno kreira.
- "name", "age" - Ovo su argumenti koje prosleđujemo "PersonConstructor" funkciji. Dakle, kada "PersonConstructor" se pokrene (zahvaljujući `.call()`), primiće "name" i "age" i uradiće ono što obično radi: podesiće `this.name = name` i `this.age = age`. Ali ovdeje `this` zapravo "DeveloperPerson" objekat, on postavlja ova svojstva na "DeveloperPerson" objekat.

Spajanje svega zajedno:

#### 3.6.2 Kreiranje objekata

Sada, hajde da kreiramo "DeveloperPerson" objekat i vidimo šta se dešava:

```js
const devPerson1 = new DeveloperPersonConstructor("Eve", 30, "JavaScript");

console.log(devPerson1.name); // Output: Eve (Inherited from PersonConstructor!)
console.log(devPerson1.age); // Output: 30 (Inherited from PersonConstructor!)
devPerson1.greet(); // Output: (Oops! Error!)
console.log(devPerson1.programmingLanguage); // Output: JavaScript (Developer-specific)
devPerson1.code(); // Output: Eve is coding in JavaScript (Developer-specific)
```

Primetite da su "devPerson1.name" i "devPerson1.age" tu. "DeveloperPersonConstructor" pozajmljeni su deo koji "PersonConstructor" podešava ta osnovna svojstva. Takođe imamo "devPerson1.programmingLanguage" i "devPerson1.code()" koji su specifični za programere.

Gde je greet()?

Ali čekajte, "devPerson1.greet()" baca grešku. Zašto? Zato što iako smo pozajmili logiku konstruktora iz PersonConstructor, još uvek nismo podesili lanac prototipova za nasleđivanje metoda prototipova kao što je "greet()".

Trenutno, "devPerson1" prototip je samo podrazumevani prototip objekta ( `Object.prototype` ). Ne nasleđuje od "PersonConstructor.prototype". Moramo to da popravimo.

#### 3.6.3 Podešavanje lanca prototipa za nasleđivanje

Da bi "DeveloperPersonConstructor" objekti takođe nasleđivali prototipske metode iz "PersonConstructor", potrebno je ručno da podesimo lanac prototipova. To možemo da uradimo koristeći "Object.create()" ponovo.

Želimo da prototip "DeveloperPersonConstructor" bude objekat koji nasleđuje od "PersonConstructor.prototype".

Evo koda:

```js
function PersonConstructor(name, age) {
    this.name = name;
    this.age = age;
}

PersonConstructor.prototype.greet = function () {
    console.log(`Hello, I'm ${this.name}`);
};

function DeveloperPersonConstructor(name, age, programmingLanguage) {
    PersonConstructor.call(this, name, age);
    this.programmingLanguage = programmingLanguage;
    this.code = function () {
        console.log(`${this.name} is coding in ${this.programmingLanguage}`);
    };
}

// ---  Set up the Prototype Chain for Inheritance! ---
DeveloperPersonConstructor.prototype = Object.create(
    PersonConstructor.prototype
);
```

Ta linija:

```js
`DeveloperPersonConstructor.prototype = Object.create(PersonConstructor.prototype);
```

čini magiju. Ona kaže: "Hej JavaScript, postavi prototip DeveloperPersonConstructor da bude novi objekat koji nasleđuje od PersonConstructor.prototype."

Sada, pokušajmo devPerson1.greet()ponovo:

```js
const devPerson1 = new DeveloperPersonConstructor("Eve", 30, "JavaScript");

devPerson1.greet(); // Output: Hello, I'm Eve  - 🎉 It works now!
```

"devPerson1.greet()" sada radi. "devPerson1" nasleđuje "greet()" metodu od "PersonConstructor.prototype" kroz lanac prototipova koji smo upravo postavili.

#### 3.6.4 Praćenje lanca prototipa

Hajde da zaista shvatimo šta se dešava kada to uradimo "devPerson1.greet()":

- JavaScript provere: Da li "devPerson1" sam po sebi ima "greet" svojstvo? Ne.
- JavaScript pregleda "devPerson1" prototip: "DeveloperPersonConstructor.prototype". Da li ima "greet" svojstvo? Ne, u našem primeru smo samo direktno dodali metode ili svojstva specifične za programere "DeveloperPersonConstructor", a ne njegovom prototipu. (Kasnije bismo mogli dodati metode prototipa specifične za programere).
- JavaScript ide uz lanac prototipova do "DeveloperPersonConstructor.prototype" prototipa "PersonConstructor.prototype". Da li ima "greet" svojstvo? Da. Definisali smo:

  ```js
  PersonConstructor.prototype.greet = function() { ... };
  ```

- JavaScript pronalazi "greet()" na "PersonConstructor.prototype" i izvršava ga u kontekstu "devPerson1" ( dakle, "this.name" u unutrašnjosti greet() se odnosi na "devPerson1.name" ).

Prototipski lanac u akciji:

```js
devPerson1->DeveloperPersonConstructor.prototype->PersonConstructor.prototype->Object.prototype.

JS programer

Možemo čak kreirati i duže lance nasleđivanja. Recimo da želimo da kreiramo "JavaScriptDeveloperPersonConstructor" poseban tip "DeveloperPersonConstructor", možda sa određenim preferencijama JavaScript frejmvorka.

Možemo da uradimo isti obrazac:

function JavaScriptDeveloperPersonConstructor(name, age, framework) {
  //  "Borrow" from DeveloperPersonConstructor first!
  DeveloperPersonConstructor.call(this, name, age, "JavaScript"); // Hardcoded "JavaScript"
  this.framework = framework;
  this.codeJavaScript = function () {
    // Specific to JavaScript developers
    console.log(`${this.name} is coding in JavaScript with ${this.framework}`);
  };
}

// Set up prototype chain: JavaScriptDeveloperPerson -> DeveloperPerson -> Person
JavaScriptDeveloperPersonConstructor.prototype = Object.create(
  DeveloperPersonConstructor.prototype
);
```

Sada imamo lanac nasleđivanja na tri nivoa.

#### 3.6.5 Konstruktorske funkcije - moćne, ali malo preopširne

Konstruktorske funkcije i prototipovi su zaista moćni. Oni su osnovni način na koji JavaScript postiže ponašanje slično objektno-orientiranom programiranju (OOP). Međutim, kao što vidite, podešavanje nasleđivanja pomoću `call()` i `Object.create()` može postati malo opširno i teško za čitanje, posebno kako lanci nasleđivanja postaju duži.

I znate šta? LJudi koji su stručnjaci za JavaScript su takođe ovo primetili. 2015. godine, u JavaScript je uvedena nova, čistija sintaksa za kreiranje planova objekata.

## 4 ES6 klase - Sintaksički šećer za prototipove

Vidite, 2015. godine, programeri JSa su prepoznali da korišćenje prototipova i konstruktorskih funkcija direktno za postizanje obrazaca sličnih klasama može postati opširno i manje jednostavno za upravljanje kako aplikacije rastu. Stoga su uveli sintaksu `class` u ECMAScript 2015 (ES6).

Klase u JSu pružaju mnogo čistiji i poznatiji način za kreiranje planova objekata i podešavanje nasleđivanja. Ali evo jedne izuzetno važne stvari koju treba zapamtiti: JS klase se i dalje grade na prototipovima. One fundamentalno ne menjaju način na koji JS OOP funkcioniše. One su samo sintaksički šećer – lepši i lakši način za pisanje koda koji i dalje koristi prototipove iza kulisa.

U sledećem odeljku ćemo videti kako da prepišemo naše primere "Person", "DeveloperPerson", i "JavaScriptDeveloperPerson" koristeći novu `class` sintaksu, i videćete koliko to izgleda čistije i sličnije klasi (namerna igra reči), a da pritom iskoristimo moć JavaScript prototipova.

### 4.1 ES6 klase: Sintaksa klase – maskirani prototipovi

U redu, videli smo kako sa konstruktorskim funkcijama, `call()` pozivima i `Object.create()` bismo naterali nasleđivanje da radi sa prototipovima. Moćno je, ali budimo iskreni, može delovati malo preopširno i indirektno, posebno ako ste navikli na jezike zasnovane na klasama.

Tu u pomoć priskaču ES6 klase. One nude mnogo pojednostavljeniju i klasi sličnu sintaksu za kreiranje planova objekata u JavaScript-u.

Hajde da prepišemo naš "PersonConstructor" primer koristeći `class` sintaksu. Spremite se za dašak svežeg vazduha.

**PersonClass** - Funkcija konstruktora redizajnirana kao klasa

Evo kako možemo definisati naš "Person" plan kao klasu:

```js
class PersonClass { // Using the 'class' keyword!
    constructor(name, age) { // 'constructor' method - like our old constructor function
        this.name = name; // Still using 'this' in the constructor
        this.age = age;
    }
  
    greet() {
        console.log(`Hello, I'm ${this.name}`);
    }
}
```

Zar to ne izgleda mnogo čistije i organizovanije? Hajde da analiziramo sintaksu klase:

- `class PersonClass { ... }`  
  Počinjemo sa `class` ključnom reči, nakon čega sledi ime klase ( "PersonClass" u ovom slučaju). Imena klasa se konvencionalno pišu velikim slovom.
- `constructor(name, age) { ... }`  
  Unutar klase imamo posebnu metodu pod nazivom `constructor`. Ovo je kao naša stara "PersonConstructor" funkcija. Tu stavljamo kod za inicijalizaciju svojstava novog "PersonClass" objekta kada se kreira pomoću `new`. I dalje koristimo `this` unutar metode klase `constructor` da bismo referencirali novi objekat koji se kreira.
- `greet() { ... }`  
  Ovako definišemo metode u klasi. Jednostavno pišemo ime metode ( "greet" ), nakon čega slede zagrade za parametre (u ovom slučaju nema), a zatim telo metode u vitičastim zagradama. Obratite pažnju da ovde ne koristimo `function` ključnu reč. To je samo "greet() { ... }".

### 4.2 Kreiranje objekata iz klase

Da bismo kreirali objekte iz našeg "PersonClass" nacrta, i dalje koristimo `new` ključnu reč, baš kao što smo to radili sa konstruktorskim funkcijama:

```js
const classPerson1 = new PersonClass("Charlie", 28);
const classPerson2 = new PersonClass("Diana", 32);

console.log(classPerson1.name); // Output: Charlie
classPerson1.greet(); // Output: Hello, I'm Charlie
```

Da, funkcioniše potpuno isto kao i naš primer konstruktorske funkcije, ali je sintaksa klase mnogo čitljivija i manje pretrpana.

**DeveloperPersonClass - Nasleđivanje je lako uz `extends`**:

Sada, hajde da se pozabavimo nasleđivanjem pomoću klasa. Sećate se kako smo morali da koristimo `call()` i `Object.create()` da bismo "DeveloperPersonConstructor" nasledili od "PersonConstructor"? Sa klasama, nasleđivanje postaje izuzetno jednostavno korišćenjem ključne reči `extends`.

Evo kako možemo prepisati "DeveloperPersonConstructor" kao "DeveloperPersonClass" što nasleđuje od "PersonClass":

```js
class DeveloperPersonClass extends PersonClass {
    //  'extends' for inheritance!
    constructor(name, age, programmingLanguage) {
        super(name, age); // 'super()' calls the parent class constructor!
        this.programmingLanguage = programmingLanguage;
    }
  
    code() {
        // Developer-specific method
        console.log(`${this.name} is coding in ${this.programmingLanguage}`);
    }
}
```

Pogledajte to. Nasleđivanje u klasama se deklariše pomoću `extends` ključne reči:

```js
class DeveloperPersonClass extends PersonClass {...}. 
```

Ova linija kaže: 'Hej JavaScript, "DeveloperPersonClass" trebalo bi da nasledi od "PersonClass".'

Unutar "DeveloperPersonClass" konstruktora imamo ovu liniju:

```js
super(name, age);. 
```

`super()` je ključna za nasleđivanje klasa. Tako pozivamo konstruktor roditeljske klase ( "PersonClass" u ovom slučaju). Kada pozovemo `super(name, age)`, ona u suštini radi istu stvar kao `PersonConstructor.call(this, name, age)` u našem primeru funkcije konstruktora — pokreće "PersonClass" konstruktor da bi podesila nasleđena svojstva ( "name" i "age") na "DeveloperPersonClass" objektu.

Nakon pozivanja `super()`, možemo dodati bilo koja svojstva ili metode specifične za programera našem "DeveloperPersonClass", kao što su `this.programmingLanguage = programmingLanguage;` i `code()` metoda.

### 4.3 Korišćenje klasa

Hajde da kreiramo "DeveloperPersonClass" objekat i vidimo nasleđivanje u akciji sa ovom čistijom sintaksom:

```js
const classDevPerson1 = new DeveloperPersonClass("Eve", 35, "JavaScript");

console.log(classDevPerson1.name); // Output: Eve (Inherited from PersonClass!)
console.log(classDevPerson1.age); // Output: 35 (Inherited from PersonClass!)
classDevPerson1.greet(); // Output: Hello, I'm Eve (Inherited from PersonClass!)
console.log(classDevPerson1.programmingLanguage); // Output: JavaScript (Developer-specific)
classDevPerson1.code(); // Output: Eve is coding in JavaScript (Developer-specific)
```

Radi tačno kako se očekuje. Obnjekat DevPerson1 nasleđuje "name", "age" i "greet()" od "PersonClass", a takođe ima i svoje "code()" i "programmingLanguage". Ali sintaksa klase čini nasleđivanje mnogo očiglednijim i lakšim za rad.

### 4.4 Klase - sintaksički šećer, prototipska snaga ispod

Budimo ponovo kristalno jasni: JavaScript klase su sintaksički šećer u odnosu na prototipove. One su lakši način pisanja koda koji je i dalje zasnovan na prototipovima i konstruktorskim funkcijama iza kulisa.

Kada definišete klasu, JS zapravo radi sledeće stvari za vas "ispod haube":

- Kreira funkciju konstruktora (kao što je naša PersonConstructor).
- Podešava `.prototype` svojstvo te konstruktorske funkcije.
- Kada koristite `extends`, on koristi `Object.create()` i `call()` za podešavanje lanca prototipa za nasleđivanje.

Klase ne menjaju fundamentalnu prirodu JavaScript objektno-optimizovanog programiranja zasnovanu na prototipovima. One nam samo daju poznatiju i manje opširnu sintaksu za rad sa njim.

### 4.5 Da li su klase samo "lažne" klase

Neki ljudi tvrde da su JavaScript klase "lažne" jer su samo sintaksički šećer. Ali iskreno, to uopšte nije poenta. Sintaksički šećer je sjajan – olakšava čitanje, pisanje i održavanje našeg koda. Za one koji dolaze iz jezika zasnovanog na klasama, klase čine objektno orijentisano programiranje u JavaScript-u mnogo pristupačnijim i razumljivijim.

Ključna stvar je da, iako vam klase daju urednu, poznatu sintaksu, i dalje morate da razumete osnovni mehanizam: "prototipove". Klase su samo prijateljski sloj na vrhu JSovog sistema prototipova.

## 5 Primeri iz stvarnog sveta

U redu, sada kada vam je ideja klasa bila prijatna, vreme je da ih vidite u akciji. Razumevanje teorije je samo pola bitke – potrebni su nam neki praktični primeri.

I da bismo učvrstili vaše razumevanje, hajde da prođemo kroz izradu klasičnog primera: osnovne aplikacije za listu obaveza. Iako je aplikacija za obaveze i dalje relativno jednostavna po konceptu, ona uvodi dovoljno interakcije na front-endu da bismo videli kako klase mogu da organizuju front-end JavaScript kod za interaktivne elemente na način koji je lak za učenje.

Zamislite da želite da napravite zaista osnovnu aplikaciju sa obavezama. Šta treba da upravljate?

- Zadaci: Svaka stavka obaveze ima:
  - opis,
  - status (završeno ili ne).
- Akcije:
  - Želećete da dodate nove zadatke,
  - označite ih kao završene,
  - obrišete ih i napravite listu.

Ovo nas prirodno navodi da razmišljamo o stavki "ToDo" kao o objektu, a ako kreirate mnogo stavki to-do, ToDo klasa je savršen plan.

### 5.1 Podešavanje datoteka

Pre nego što napišete bilo koji kod, kreirajte dve datoteke u istom diru:

- "index.html" - Ovo je struktura veb stranice.
- "script.js" - Ovde će se nalaziti vaš JavaScript kod sa klasama.

Možete koristiti bilo koji uređivač teksta (kao što su VS Code, Sublime Text ili čak Notepad) da biste kreirali ove datoteke.

### 5.2 Kreiranje ToDo klase

Počnimo sa izgradnjom naše "ToDo" klase. Kopirajte i nalepite sledeći kod u vašu "script.js" datoteku:

```js
class ToDo {

    constructor(description) {
        this.description = description; // Every to-do needs a description
        this.completed = false; // By default, it's not completed
    }

    markComplete() {
        this.completed = true;
        console.log("${this.description}" marked as complete!);
    }

    // More methods (e.g., for editing the to-do) can be added later.
}
```

Obratite pažnju koliko je to čisto. `constructor` podešava "description" i "status" završetka za svaku novu stavku obaveze. Metoda "markComplete()" ažurira "status" i evidentira poruku potvrde.

### 5.3 Izgradnja klase ToDoList

Zatim ćemo napraviti "ToDoList" klasu za upravljanje našom kolekcijom zadataka. Dodajte sledeći kod u vašu "script.js" datoteku, ispod "ToDo" klase:

```js
class ToDoList {

    constructor() {
        this.todos = []; // Start with an empty array of to-dos
    }
    
    addTodo(description) {
        const newTodo = new ToDo(description); // Create a new ToDo object
        this.todos.push(newTodo); // Add it to our list
        this.renderTodoList(); // Update the webpage display
    }

    listTodos() {
        return this.todos; // Return the array of todos (for further processing or rendering)
    }

    markTodoComplete(index) {
        if (index >= 0 && index < this.todos.length) {
            this.todos[index].markComplete();
            this.renderTodoList(); // Update the display after marking complete
        }
    }

    renderTodoList() {
        const todoListElement = document.getElementById('todoList');
        todoListElement.innerHTML = ''; // Clear the current list in HTML
        this.todos.forEach((todo, index) => {
            const listItem = document.createElement('li');
            listItem.textContent = todo.description;
            if (todo.completed) {
                listItem.classList.add('completed'); // Add CSS class for styling completed items
            }
            // Create a "Complete" button for each to-do
            const completeButton = document.createElement('button');
            completeButton.textContent = 'Complete';
            completeButton.onclick = () => this.markTodoComplete(index);
            listItem.appendChild(completeButton);
            todoListElement.appendChild(listItem);
        });
    }
}
```

U ovoj klasi:

- `Constructor` - inicijalizuje prazan niz za čuvanje naših stavki obaveza.
- `addTodo(description)` - kreira novi ToDoo bjekat i dodaje ga u niz, a zatim poziva funkciju `renderTodoList()` da ažurira prikaz.
- `listTodos()` - vraća listu obaveza.
- `markTodoComplete(index)` - označava određeni zadatak kao završen i osvežava prikaz.
- `renderTodoList()` - pronalazi HTML element sa ID-jem todoList, briše njegov sadržaj, a zatim kreira stavke liste za svaki zadatak, uključujući dugme "Završi".
Kreiranje HTML strukture

Zatim, otvorite "index.html" datoteku i nalepite sledeći HTML kod:

```html
<!DOCTYPE html>

<html>
<head>
    <title>My Simple To-Do App</title>
    <style>
        /* Simple CSS to style completed items */
        completed {
            text-decoration: line-through;
            color: gray;
        }
    </style>
</head>

<body>
    <h1>My To-Do List</h1>
    <input type="text" id="todoInput" placeholder="Enter new to-do...">
    <button id="addButton">Add To-Do</button>
    <ul id="todoList"></ul>
    <script src="script.js"></script>
</body>
</html>
```

Ova HTML datoteka podešava:

- Naslov za vašu listu obaveza.
- Polje za unos (sa id="todoInput") za unos novih zadataka.
- Dugme "Dodaj zadatak" (sa id="addButton").
- Prazna neuređena lista (sa id="todoList") gde će se pojaviti vaši zadaci.
- Veza do "script.js" datoteke koja sadrži vaš JavaScript kod.

### 5.4 Učiniti da sve funkcioniše zajedno

Konačno, povežimo naše HTML elemente sa našim JavaScript-om. Na dnu datoteke "script.js" dodajte ovaj kod:

```js
const myTodoList = new ToDoList(); // Create an instance of ToDoList

// Get references to the HTML elements
const addButton = document.getElementById("addButton");
const todoInput = document.getElementById("todoInput");

// Listen for clicks on the "Add To-Do" button
addButton.addEventListener("click", () => {
    const todoText = todoInput.value.trim(); // Get the text from the input box
  
    if (todoText) {
        // Only add if the input is not empty
        myTodoList.addTodo(todoText); // Add the new to-do

        todoInput.value = ""; // Clear the input box
    }
});

// Render the to-do list initially (it will be empty to start)
myTodoList.renderTodoList();
```

Ovaj kod radi sledeće:

- Kreira instancu klase ToDoList.
- Pronalazi HTML elemente za input i button.
- Ovaj kod dodaje slušač događaja HTML elementu dugmeta koji ima ID "addButton". Ovaj slušač je podešen da reaguje na događaje "click" na ovom dugmetu. Kada se klikne na dugme "Dodaj zadatak", kod unutar funkcije slušača događaja će se izvršiti. Ovaj kod uzima tekst koji je korisnik uneo u HTML polje za unos sa ID-om "todoInput" i dodaje ga kao novu stavku obaveze na našu listu.
- U početku prikazuje listu obaveza na veb stranici.

### 5.5 Vaš izazov: Idite u proto-stil

Sada kada ste videli kako klase mogu učiniti izgradnju ove aplikacije sa zadacima strukturiranijom, evo izazova: pokušajte da napravite istu aplikaciju sa zadacima bez korišćenja `class` ključne reči . Umesto toga koristite literale objekata i prototipove. Razmislite o:

- Kako biste kreirali "ToDo" "nacrt" koristeći konstruktorsku funkciju i prototipove?
- Kako biste dodali "markComplete()" metodu prototipu "ToDo"?
- Kako biste slično strukturirali "plan" za "ToDoList"?

Izgradnjom iste aplikacije koristeći oba pristupa, zaista ćete shvatiti da su klase samo lepši, poznatiji način pisanja koda zasnovanog na prototipovima.

### 6 Zaključak

Čestitamo! Napravili ste osnovnu, interaktivnu aplikaciju sa obavezama koristeći JavaScript klase i HTML. Sada vidite kako vam klase pomažu da organizujete kod i obuhvatite povezane funkcionalnosti. Iako su klase samo sintaksički šećer u odnosu na prototipove, one znatno olakšavaju pisanje, čitanje i održavanje koda — posebno kako vaše aplikacije rastu.

Vaš sledeći korak? Eksperimentišite sa prototipskim pristupom i uporedite ga sa pristupom zasnovanim na klasama. Što više kodirate, ovi koncepti će postajati prirodniji. Srećno kodiranje i nastavite da pravite sjajne stvari.

Ako imate bilo kakvih pitanja, slobodno me pronađite na Tviteru na @sprucekhalifa i ne zaboravite da me pratite za još saveta i novosti. Srećno kodiranje!

[Nazad](README.md)
