
# Kako implementirati obrazac posmatrača u Pajtonu

[Nazad](../misc/README.md)

Da li ste se ikada zapitali kako vas YouTube obaveštava kada vaš omiljeni kanal postavi novi video? Ili kako vas vaš imejl klijent obaveštava kada stignu nove poruke? Ovo su savršeni primeri obrasca posmatranja u akciji.

Posmatrački obrazac je obrazac dizajna gde objekat (nazvan subjekat) održava listu zavisnih elemenata (nazvanih posmatrači) i automatski ih obaveštava kada se njegovo stanje promeni. To je kao da imate pretplatu na bilten: kada se objavi novi sadržaj, svi pretplatnici dobijaju obaveštenje.

U ovom tutorijalu ćete saznati šta je šablon posmatrača, zašto je koristan i kako ga implementirati u Pajtonu uz praktične primere.

Kod možete pronaći na GitHabu.

## Sadržaj

- Preduslovi
- Šta je obrazac posmatrača?
- Jednostavan primer obrasca posmatrača
- Rukovanje odjavama
- Različite vrste posmatrača
- Korišćenje apstraktnih osnovnih klasa
- Kada koristiti obrazac posmatrača

### 1 Preduslovi

Pre nego što počnemo, uverite se da imate:

- Instaliran je Pajton 3.10 ili noviji
- Razumevanje kako funkcionišu klase i metode u Pajtonu
- Poznavanje koncepata objektno orijentisanog programiranja (OOP)

Hajde da počnemo!

### 2 Šta je obrazac posmatrača?

Šablon posmatrača definiše odnos jedan-prema-više između objekata. Kada jedan objekat promeni stanje, svi njegovi zavisni objekti se obaveštavaju i automatski ažuriraju.

Zamislite to kao novinsku agenciju i reportere. Kada se dogodi vest (o kojoj se radi), agencija odmah obaveštava sve pretplaćene reportere (posmatrače). Svaki reporter tada može da se nosi sa vešću na svoj način – neki bi je mogli tvitovati, drugi bi mogli pisati članke, a neki bi je mogli emitovati na televiziji.

Šablon je koristan kada:

- Potrebno je da obavestite više objekata o promenama stanja
- Želite labavu vezu između objekata
- Ne znate koliko objekata treba unapred obavestiti
- Objekti bi trebalo da budu u mogućnosti da se dinamički pretplate i odjave

### 3 Jednostavan primer obrasca posmatrača

Počnimo sa osnovnim primerom: blog koji obaveštava čitaoce kada se objavi novi članak.

Napravićemo blog (naslov) i pretplatnike imejla (posmatrače) koji će automatski biti obavešteni kada se objavi novi sadržaj.

Prvo, hajde da napravimo Blog klasu koja će upravljati pretplatnicima i slati obaveštenja:

```py
class Blog:
    def __init__(self, name):
        self.name = name
        self._subscribers = []
        self._latest_post = None

    def subscribe(self, subscriber):
        """Add a subscriber to the blog"""
        if subscriber not in self._subscribers:
            self._subscribers.append(subscriber)
            print(f"✓ {subscriber.email} subscribed to {self.name}")

    def unsubscribe(self, subscriber):
        """Remove a subscriber from the blog"""
        if subscriber in self._subscribers:
            self._subscribers.remove(subscriber)
            print(f"✗ {subscriber.email} unsubscribed from {self.name}")

    def notify_all(self):
        """Send notifications to all subscribers"""
        print(f"\nNotifying {len(self._subscribers)} subscribers...")
        for subscriber in self._subscribers:
            subscriber.receive_notification(self.name, self._latest_post)

    def publish_post(self, title):
        """Publish a new post and notify subscribers"""
        print(f"\n📝 {self.name} published: '{title}'")
        self._latest_post = title
        self.notify_all()
```

Klasa Blog je naša tema. Ona održava listu pretplatnika u `_subscribers` i čuva naslov najnovije objave u `_latest_post`. Metoda `subscribe` dodaje pretplatnike na listu, proveravajući duplikate. Metoda `notify_all` prolazi kroz sve pretplatnike i poziva njihovu `receive_notification` metodu. Kada pozovemo `publish_post`, ona ažurira najnoviju objavu i automatski obaveštava sve pretplatnike.

Sada hajde da kreiramo klasu posmatrača koja prima obaveštenja:

```py
class EmailSubscriber:
    def __init__(self, email):
        self.email = email

    def receive_notification(self, blog_name, post_title):
        print(f"Email sent to {self.email}: New post on {blog_name} - '{post_title}'")
```

Klasa `EmailSubscriber` je naš posmatrač. Ima jednu metodu, `receive_notification`, koja obrađuje dolazna obaveštenja sa bloga.

Sada hajde da koristimo ove klase zajedno:

```py
# Create a blog
tech_blog = Blog("DevDaily")

# Create subscribers
reader1 = EmailSubscriber("anna@example.com")
reader2 = EmailSubscriber("betty@example.com")
reader3 = EmailSubscriber("cathy@example.com")

# Subscribe to the blog
tech_blog.subscribe(reader1)
tech_blog.subscribe(reader2)
tech_blog.subscribe(reader3)

# Publish posts
tech_blog.publish_post("10 Python Tips for Beginners")
tech_blog.publish_post("Understanding Design Patterns")
```

Izlaz:

```sh
✓ anna@example.com subscribed to DevDaily
✓ betty@example.com subscribed to DevDaily
✓ cathy@example.com subscribed to DevDaily

📝 DevDaily published: '10 Python Tips for Beginners'

Notifying 3 subscribers...
📧 Email sent to anna@example.com: New post on DevDaily - '10 Python Tips for Beginners'
📧 Email sent to betty@example.com: New post on DevDaily - '10 Python Tips for Beginners'
📧 Email sent to cathy@example.com: New post on DevDaily - '10 Python Tips for Beginners'

📝 DevDaily published: 'Understanding Design Patterns'

Notifying 3 subscribers...
📧 Email sent to anna@example.com: New post on DevDaily - 'Understanding Design Patterns'
📧 Email sent to betty@example.com: New post on DevDaily - 'Understanding Design Patterns'
📧 Email sent to cathy@example.com: New post on DevDaily - 'Understanding Design Patterns'
```

Primetite kako Blog klasa ne mora da zna detalje o tome kako svaki pretplatnik obrađuje obaveštenje. Ona samo poziva njihovu `receive_notification` metodu.

> [!Note]
> Zamislite sve primere ovde kao funkcije čuvara mesta koje objašnjavaju kako funkcioniše obrazac posmatrača. U vašim projektima ćete imati funkcije koje se povezuju sa imejlom i drugim servisima.

### 4 Rukovanje odjavama

U stvarnim aplikacijama, korisnici moraju biti u mogućnosti da se odjave. Evo kako to funkcioniše:

```py
blog = Blog("CodeMaster")

user1 = EmailSubscriber("john@example.com")
user2 = EmailSubscriber("jane@example.com")

# Subscribe users
blog.subscribe(user1)
blog.subscribe(user2)

# Publish a post
blog.publish_post("Getting Started with Python")

# User1 unsubscribes
blog.unsubscribe(user1)

# Publish another post - only user2 gets notified
blog.publish_post("Advanced Python Techniques")
```

Izlaz:

```sh
✓ john@example.com subscribed to CodeMaster
✓ jane@example.com subscribed to CodeMaster

📝 CodeMaster published: 'Getting Started with Python'

Notifying 2 subscribers...
📧 Email sent to john@example.com: New post on CodeMaster - 'Getting Started with Python'
📧 Email sent to jane@example.com: New post on CodeMaster - 'Getting Started with Python'

✗ john@example.com unsubscribed from CodeMaster

📝 CodeMaster published: 'Advanced Python Techniques'

Notifying 1 subscribers...
📧 Email sent to jane@example.com: New post on CodeMaster - 'Advanced Python Techniques'
```

Nakon `user1` odjave, samo `user2` prima obaveštenje za drugu objavu. Obrazac posmatrača olakšava dinamičko dodavanje i uklanjanje posmatrača.

### 5 Različite vrste posmatrača

Jedan izuzetno koristan aspekt obrasca posmatrača je to što različiti posmatrači mogu različito reagovati na isti događaj. Hajde da napravimo praćenje cena akcija gde više tipova posmatrača reaguje na promene cena.

Prvo, kreirajmo Stock klasu koja će obavestiti posmatrače kada se cena promeni:

```py
class Stock:
    def __init__(self, symbol, price):
        self.symbol = symbol
        self._price = price
        self._observers = []

    def add_observer(self, observer):
        self._observers.append(observer)
        print(f"Observer added: {observer.__class__.__name__}")

    def remove_observer(self, observer):
        self._observers.remove(observer)

    def notify_observers(self):
        for observer in self._observers:
            observer.update(self.symbol, self._price)

    def set_price(self, price):
        print(f"\n {self.symbol} price changed: ${self._price} → ${price}")
        self._price = price
        self.notify_observers()
```

Klasa Stock održava trenutnu cenu i obaveštava sve posmatrače kad god `set_price` se pozove.

Sada hajde da kreiramo tri različita tipa posmatrača koji različito reaguju na ažuriranja cena:

```py
class EmailAlert:
    def __init__(self, email):
        self.email = email

    def update(self, symbol, price):
        print(f"📧 Sending email to {self.email}: {symbol} is now ${price}")

class SMSAlert:
    def __init__(self, phone):
        self.phone = phone

    def update(self, symbol, price):
        print(f"📱 Sending SMS to {self.phone}: {symbol} price update ${price}")

class Logger:
    def update(self, symbol, price):
        print(f"📝 Logging: {symbol} = ${price} at system time")
```

Svaki posmatrač ima drugačiju implementaciju metode ažuriranja, `EmailAlert` šalje imejlove, `SMSAlert` šalje tekstualne poruke i `Logger` beleži promenu.

Sada hajde da ih koristimo zajedno:

```py
# Create a stock
apple_stock = Stock("AAPL", 150.00)

# Create different types of observers
email_notifier = EmailAlert("investor@example.com")
sms_notifier = SMSAlert("+1234567890")
price_logger = Logger()

# Add all observers
apple_stock.add_observer(email_notifier)
apple_stock.add_observer(sms_notifier)
apple_stock.add_observer(price_logger)

# Update the stock price
apple_stock.set_price(155.50)
apple_stock.set_price(152.25)
```

Izlaz:

```sh
Observer added: EmailAlert
Observer added: SMSAlert
Observer added: Logger

 AAPL price changed: $150.0 → $155.5
📧 Sending email to investor@example.com: AAPL is now $155.5
📱 Sending SMS to +1234567890: AAPL price update $155.5
📝 Logging: AAPL = $155.5 at system time

 AAPL price changed: $155.5 → $152.25
📧 Sending email to investor@example.com: AAPL is now $152.25
📱 Sending SMS to +1234567890: AAPL price update $152.25
📝 Logging: AAPL = $152.25 at system time
```

Klasu Stock nije briga šta svaki posmatrač radi. Ona jednostavno poziva `update` svakog od njih i prosleđuje potrebne podatke. Možete kombinovati posmatrače kako god želite.

### 6 Korišćenje apstraktnih osnovnih klasa

Da bismo sprovodili konzistentan interfejs za sve posmatrače, možemo koristiti Pajtonove apstraktne osnovne klase. Ovo garantuje bezbednost tipova.

Prvo, hajde da kreiramo osnovne klase koje definišu naš interfejs:

```py
from abc import ABC, abstractmethod

class Subject(ABC):
    def __init__(self):
        self._observers = []

    def attach(self, observer):
        if observer not in self._observers:
            self._observers.append(observer)

    def detach(self, observer):
        self._observers.remove(observer)

    def notify(self, data):
        for observer in self._observers:
            observer.update(data)

class Observer(ABC):
    @abstractmethod
    def update(self, data):
        pass
```

Klasa Subject pruža standardne metode za upravljanje posmatračima. Observer klasa definiše interfejs sa `@abstractmethod` dekoratorom, osiguravajući da svi posmatrači implementiraju `update`.

Sada hajde da kreiramo sistem naručivanja koji koristi ove osnovne klase:

```py
class OrderSystem(Subject):
    def __init__(self):
        super().__init__()
        self._order_id = None

    def place_order(self, order_id, items):
        print(f"\n🛒 Order #{order_id} placed with {len(items)} items")
        self._order_id = order_id
        self.notify({"order_id": order_id, "items": items})
```

OrderSystem nasleđuje od Subject posmatrača i može da upravlja njima bez same implementacije te logike.

Zatim, hajde da kreiramo konkretne posmatrače za različita odeljenja:

```py
class InventoryObserver(Observer):
    def update(self, data):
        print(f"📦 Inventory: Updating stock for order #{data['order_id']}")

class ShippingObserver(Observer):
    def update(self, data):
        print(f"🚚 Shipping: Preparing shipment for order #{data['order_id']}")

class BillingObserver(Observer):
    def update(self, data):
        print(f"💳 Billing: Processing payment for order #{data['order_id']}")
```

Svaki posmatrač mora da implementira `update` metod. Sada hajde da sve to spojimo:

```sh
# Create the order system
order_system = OrderSystem()

# Create observers
inventory = InventoryObserver()
shipping = ShippingObserver()
billing = BillingObserver()

# Attach observers
order_system.attach(inventory)
order_system.attach(shipping)
order_system.attach(billing)

# Place an order
order_system.place_order("ORD-12345", ["Laptop", "Mouse", "Keyboard"])
```

Izlaz:

```sh
🛒 Order #ORD-12345 placed with 3 items
📦 Inventory: Updating stock for order #ORD-12345
🚚 Shipping: Preparing shipment for order #ORD-12345
💳 Billing: Processing payment for order #ORD-12345
```

Korišćenje apstraktnih osnovnih klasa obezbeđuje bezbednost tipa i osigurava da svi posmatrači prate isti interfejs.

### 7 Kada koristiti obrazac posmatrača

Obrazac posmatrača je pogodan za:

- Sistemi vođeni događajima – grafički korisnički interfejsi, gejm endžini ili bilo koji sistem gde akcije pokreću ažuriranja negde drugde.
- Obaveštenja u realnom vremenu – aplikacije za ćaskanje, fidovi društvenih medija, berzanske vesti ili sistemi za push obaveštenja.
- Odvojena arhitektura – ​​Kada želite da subjekt bude nezavisan od svojih posmatrača radi fleksibilnosti.
- Višestruki slušaoci – Kada više objekata treba da reaguje na isti događaj različito.

Izbegavajte obrazac posmatrača kada imate jednostavne odnose jedan-na-jedan ili kada su performanse kritične sa mnogo posmatrača (jer dodatni troškovi obaveštenja mogu biti značajni).

## Zaključak

Obrazac posmatrača stvara jasno razdvajanje između objekata koji proizvode događaje i objekata koji reaguju na njih. On promoviše labavo sprezanje – subjekt ne mora ništa da zna o svojim posmatračima osim da imaju metod ažuriranja.

Obradili smo osnovnu implementaciju, rukovanje pretplatama, korišćenje različitih tipova posmatrača i apstraktne osnovne klase. Počnite jednostavno sa osnovnim odnosom subjekt-posmatrač i dodajte složenost samo kada je potrebno.

Srećno kodiranje!

[Nazad](../misc/README.md)
