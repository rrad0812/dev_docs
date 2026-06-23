# Virtuelne tabele

[Sadržaj](README.md)

## Popunite sa `on_after_open`

Uobičajeni način za popunjavanje virtuelne tabele je korišćenje obrađivača događaja `on_after_open`. `view` metoda otvara iteme, a za virtuelne tabele otvara ih prazne.

Na primer, sledeći kod postavlja `paginate` atribut itema na `false` a zatim popunjava item zapisima koje dobija na serveru koristeći "get_records" funkciju.

Kod na strani klijenta:

```js
function on_view_form_created(item) {
    item.paginate = false;
}

function on_after_open(item) {
    item.server('get_records', function(records) {
        records.forEach(function(rec) {
            item.append();
            item.product.value = rec.product;
            item.price.value = rec.price;
            item.post();
        });
    });
}
```

kod na strani servera:

```py
def get_records(item):
    return [
        {
            'product': 'Product1',
            'price': 10
        },
        {
            'product': 'Product2',
            'price': 20
        }
    ]
```

[Sadržaj](README.md)

## Popunite sa Jam.py ili DataTables JS bibliotekom

U sledećim primerima imamo Pajton kod ( "show_data" ) koji čita Rest API i vraća listu vrednosti ( podataka ). Ova lista će se prikazivati u tabelama vizuelizacije.

### Opcija sa izvornim Jam.Py funkcijama

Napravite virtuelnu tabelu, uključujući polja koja će ona prikazivati.

U `on_view_form_created` klijent hendleru, uključite sledeće linije da biste onemogućili `pagination` i sprečili prikazivanje dugmadi za snimanje:

```js
function on_view_form_created(item) {
    item.paginate = false;
    item.view_form.find("#edit-btn").hide();
    item.view_form.find("#delete-btn").hide();
    item.view_form.find("#new-btn").hide();
}
```

U `on_after_open` klijent hendleru, pozivam rutinu "show_data" u Pajtonu (može biti bilo koja druga rutina) koja vraća listu (podaci) sa zapisima, a zatim uključujem zapise u obrazac za pregled:

```js
function on_after_open(item) {
    item.server('show_data', function(data, error) {
        if (error) {
            item.alert_error(error);
        }
        else {
            data.forEach(function(rec) {
                item.append();
                item.id.value = rec.id;
                item.emp.value = rec.emp;
                item.data.value = rec.data_diaria;
                item.valor.value = rec.vl_diaria;
                item.uhs.value = rec.uhs;
                item.post();
            });
        }
    });
}
```

### Opcija sa DataTables Javascript bibliotekom ( <https://datatables.net> )

Uključite JS i CSS DataTables datoteke u `index.html`:

```html
...
<script src="jam/js/jam.js"></script>

<!-- DataTables Lib Include -->
<link rel="stylesheet" type="text/css" href="/css/jquery.dataTables.css">
<script type="text/javascript" charset="utf8" src="/js/jquery.dataTables.js"></script>
```

Napravite DIV "datatables-view" u `index.html` datoteci:

```html
<div class="datatables-view">
    <div class="row-fluid">
        <br>
        <table id="table_id" class="display"></table>
    </div>
</div>
```

Napravite item sa virtuelnom tabelom pod nazivom "datatables" (polja nisu potrebna)

U klijentskom `on_view_form_created` događaju, pozivam Python rutinu "show_data" (može biti bilo koja druga rutina) koja vraća listu (podatke) sa zapisima i konačno ih prikazuje u objektu DataTable.

```js
function on_view_form_created(item) {
    item.server('show_data', [optview], function(data, error) {
        if (error) {
            item.alert_error(error);
        }
        else {
            var table=$('#table_id').DataTable( {
                language: {
                    url: '/js/datatables-pt-br.json',
                    decimal: '.',
                    thousands: ','
                },
                data: data,
                columns: [
                    {data: 'id', title: 'Id'},
                    {data: 'emp', title: 'Emp'},
                    {data: 'data_diaria', title: 'Data'},
                    {data: 'vl_diaria', title: 'Valor'},
                    {data: 'uhs', title: 'UHs'}
                ]
            });
        }
    });
}
```

[Sadržaj](README.md)

## Rad sa virtuelnom tabelom i dinamičkim poljima

Metoda `apply` ne radi za virtuelne tabele, ali mislim da možete sačuvati izmene u tabeli na serveru koristeći neki obrađivač događaja, na primer, `on_aply_post` i `server` metod za slanje podataka na server i njihovo čuvanje.

Što se tiče kreiranja `dynamic fields` napisao sam `dyn_fields` metod za `Item` klasu u `jam.js` modulu.

Možete ga pronaći pretraživanjem linije:

```js
dyn_fields: function(fields) {
```

Treba da doda dinamička polja itemu na osnovu parametra `fields`.

Parametar `fields` je lista definicija polja. Moraju biti iste kao one koje se prosleđuju iz `server` da bi item kreirao polja na klijentu.

Probao sam da koristim ovu metodu samo jednom pre nekoliko godina i ne sećam se kako tačno funkcioniše. I nisam siguran da će pravilno funkcionisati.

> [!Note]
> Probao sam `dyn_fields` metod za kreiranje dinamičkih polja i ispostavilo se da je zastareo.
>
> Ispravio sam to i objavio izmene u verziji 5.4.131.

Može se koristiti na sledeći način:

```js
function on_view_form_created(item) {
    let d_fields = [
        {
            field_name: 'string_field',
            field_caption: 'String field',
            data_type: task.consts.TEXT,
            field_size: 100,
            required: true
        },
        {
            field_name: 'integer_field',
            field_caption: 'Integer field',
            data_type: task.consts.INTEGER
        },
        {
            field_name: 'lookup_field',
            field_caption: 'Lookup field',
            data_type: task.consts.INTEGER,
            lookup_item: task.customers,
            lookup_field: 'lastname'
        },
    ];
    item.dyn_fields(d_fields);

    item.view_options.fields = ['string_field', 'integer_field', 'lookup_field'];
    item.edit_options.fields = ['string_field', 'integer_field', 'lookup_field'];    

    item.open();
}
```

Dinamička polja su deo virtuelne tabele.

[Sadržaj](README.md)

## Dobijanje podataka iz tabela/view-ova u virtuelnu tabelu

Imam Jam.py aplikaciju koja koristi MSSQL za svoju bazu podataka.

Ono što bih želeo jeste da imam (samo za čitanje) pristup podacima dobijenim iz viewa na bazi podataka, koristeći virtuelnu tabelu. Ne mogu da koristim funkciju "Import" za view - kako bih imao odgovarajući item za view, jer ne vidi view u bazi podataka.

Ono što bih želeo da uradim jeste da nekako "mapiram" rezultatske redove iz viewa na item sa virtuelnom tabelom.

---

Potrebno je da definišete sve za VT tabelu. Zatim, potrebno je da napravite redove servera za povratak, što je definisano u VT-u.

Glavni problem sa VT klijentskim kodom je znati gde koristiti `disable_controls` i `enable_controls`, jer to utiče na performanse pri prikazivanju.

Server:

```py
def get_rows(item):
    rows = None
    sql = """SELECT ID, User_ID, YearID, Year FROM INTEGRA_GRUSES"""
    #print(sql)
    rows = item.task.execute_select(sql)
    return rows

def get_uses(item):
    res = []
    rows = []

    rows = get_rows(item)
   
    for r in rows:
        res.append(
            {
                'id': r[0],
                'user_id': r[1],
                'yearid': r[2],
                'year': r[3]
            }
        )
       
    print(res)
    return res
```

Klijentski kod je:

```js
function on_view_form_created(item) {
    item.paginate = false;
    item.table_options.new = false;
    item.view_form.find("#edit-btn").hide();
    item.view_form.find("#delete-btn").hide();
    item.view_form.find("#new-btn").hide();
    item.open(); // mislim da ovo ne treba
}

function on_after_open(item) {
    item.server('get_uses', function(records) {
        item.disable_controls();
        try {
            records.forEach(function(rec) {
                item.append();
                item.user_id.value = rec.user_id;
                item.yearid.value = rec.yearid;
                item.year.value = rec.year;
                item.post();
            });
        }
        finally {
            item.enable_controls();
        }
    });
}
```

[Sadržaj](README.md)

## Uvoz view-a iz baze podataka

Za SQLite3 pronađite datoteku: jam/db/sqlite.py blok koda sa:

```py
def get_table_names(connection):
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM sqlite_master WHERE type='table'")
```

i dodajte samo `OR type='view'`:

```py
def get_table_names(connection):
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM sqlite_master WHERE type='table' OR type='view'")
```

Moći ćete da uvezete view. Naravno, ovo nije podržano, tako da vaš izvoz/uvoz možda neće raditi…

Dakle, umesto korišćenja VT-a, sada možete da koristite DB view, koji je gotovo trenutan.

[Sadržaj](README.md)

### "Manual DB mode"

"Manual Db mode" garantuje da kreirana tabela u interfejsu Jam Builder-a **neće** biti kreirana u bazi podataka.

Dok je uključen, omogućava uvoz tabela, ili view-ova u ovom primeru. Uvoz samo kreira informacije u interfejsu Jam.py Buider-a, pošto tabela već postoji u bazi podataka.

Dakle, način pristupa view-u baze podataka u bilo kojoj bazi podataka jeste kreiranje tabele istog imena u Builderu, sa potrebnim poljima, dok ste u "Manual DB mode".

[Sadržaj](README.md)

### Ogromno povećanje performansi sa virtuelnim tabelama tj. view-ovima

Virtuelne tabele (VT) su bile prilično ispod radara jer su se koristile samo retko. U demo verziji, VT se koristi za slanje imejlova kupcima. Što zapravo ne pokazuje pun potencijal VT-a.

Po mome mišljenju, VT se može koristiti kao DB view. Recimo da je DB view samo SELECT SQL.

Dakle, možemo da napravimo serverski modul sa bilo kojim SQL-om koji nam je potreban i da ga prosledimo VT klijentskom modulu da bismo prikazali tabelu. Međutim, upravo je ovo mesto gde je problem sa performansama.

Napravio sam mali primer sa dve tabele, jednom VT i jednom DT, koje prikazuju isti select SQL: <https://drive.google.com/file/d/1uMz2i-2RcNYsJwLGcBblxjXrFcQfuq0h/view?usp=share_link>

Zamenite dotični kod za VT i vidite poboljšanje performansi.

### View ne skroluje

Koristim ovaj pristup za popunjavanje VT-a. Radi kako se očekuje. Međutim, primetio sam da rezultujući obrazac za pregled nije moguće skrolovati.

Evo mog klijentskog modula:

```js
function on_after_open(item) {
    item.disable_controls();
    let order_rows = item.server('get_order_rows');
    order_rows.forEach(function(v){
        item.append();
        item.id.value = v.id;
        item.master_rec_id.value = v.master_rec_id;
        item.sku.value = v.sku;
        item.title.value = v.title;
        item.colours.value = v.colours;
        item.post();
    });
    item.first();
    item.enable_controls();
}
```

A evo i mog serverskog modula:

```py
def get_order_rows(item):
    rows = []
    recs = item.task.execute_select('''
        SELECT a.id, a.master_rec_id, b.sku, b.title, a.colours
        FROM order_ordertable a
        INNER JOIN order_products b
            ON a.sku = b.id
        INNER JOIN order_orders c
            ON a.master_rec_id = c.id
                AND c.completed = false
        WHERE a.deleted = 0 and a.start_date IS NULL and a.machine IS NULL;
    ''')
    for v in recs:
        rows.append({
            'id': v[0],
            'master_rec_id': v[1],
            'sku': v[2],
            'title': v[3],
            'colours': v[4]
        })
    return rows
```

---

Nakon provere vaših priloženih uzoraka, uspeo sam da pokrenem skrolovanje dodavanjem ovog dela u moj klijentski modul:

```js
function on_view_form_created(item) {
    item.paginate = false;
}
```

AY nikada nije savetovao skrolovanje, mislim da to nije moguće sa VT po podrazumevanim podešavanjima. Osim ako SQL nije prilagođen nekim paginacijama i akcijama dugmadi.

Za vaš primer, pogledao bih Pivot.js.

[Sadržaj](README.md)

## Povezano polje u virtuelnoj tabeli

Imam virtuelnu tabelu sa povezanim poljem (category). Ovako popunjavam tabelu:

```js
function  plant_next(item) {
    var selected = task.categories.selections;
    if (!selected.length) {
        selected.add(task.categories.id.value);
    }
   
    item.server('plant_suggestions', [selected],  function(result, err) {
        if (err) {
            item.alert('Failed to send the mail: ' + err);
        }
        else {
            // Load suggestions into a temporary dataset for the modal
            var modal = task.newplanting;
            modal.open({ open_empty: true }, function() {
                modal.disable_controls(); // avoid event firing during fill

                for (var i = 0; i < result.length; i++) {
                    modal.append();
                    modal.id.value = i;
                    modal.category.value = result[i].plant;
                    modal.plant.value = result[i].plant;
                    modal.name.value = result[i].name;
                    modal.rank.value = result[i].rank;
                    modal.post();
                }
                modal.enable_controls();
                modal.edit_options.multiselect = true; // allow selecting multiple plants
            });
            modal.view();
        }
    });
}
```

Iz nekog razloga, polje Category ne prikazuje naziv kategorije. Međutim, kada izmenim polje kategorije, ono se prikazuje. Šta radim pogrešno?

Ako treba odmah da otvorite obrazac sa novim zapisom, na inicijalnim stranicama "modal" polja, potrebno je da napišete i `modal.category.lookup_value`.

[Sadržaj](README.md)
