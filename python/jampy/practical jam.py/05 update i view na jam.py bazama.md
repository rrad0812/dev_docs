
# Update view-a na bazama podataka

## Kratak odgovor: ne, views nisu ažurabilni

Svi ovi DBMS-ovi podržavaju update nad view-ovima u određenoj meri, ali pravila se razlikuju

| DBMS | UPDATE/INSERT/DELETE nad view-om |
| ---- | -------------------------------- |
| SQLite | Da, ali praktično samo uz INSTEAD OF trigger |
| MySQL | Da, ako je view dovoljno jednostavan |
| PostgreSQL | Da, ako je view automatski update-bilan ili uz INSTEAD OF trigger/rules |
| SQL Server | Da, ako je view jednostavan; složeni preko INSTEAD OF triggera |
| Oracle | Da, ako je view jednostavan; složeni preko INSTEAD OF triggera |

### 1 SQLite

Ovo je najveći izuzetak.

Običan view:

```SQL
CREATE VIEW v_emp AS
SELECT id, name
FROM emp;

UPDATE v_emp SET name='Pera' WHERE id=1;
```

```sh
→ error:
cannot modify v_emp because it is a view
```

Da bi radio update, moraš da napraviš:

```SQL
CREATE TRIGGER ...
INSTEAD OF UPDATE ON v_emp
```

Dakle, bez triggera view nije update-bilan.

### 2 MySQL

Radi za jednostavne view-ove:

```SQL
CREATE VIEW v_emp AS
SELECT id, name
FROM emp;
```

Ovo radi:

```SQL
UPDATE v_emp
SET name='Pera'
WHERE id=1;
```

Ali ako imaš:

- `JOIN`
- `GROUP BY`
- agregatne funkcije (`SUM`, `COUNT`, ...)
- `DISTINCT`
- `UNION`

onda view uglavnom nije update-bilan.

### 3 PostgreSQL

PostgreSQL od verzije 9.3 ima automatski update-bilne view-ove.

Radi ako view:

- koristi jednu tabelu
- nema `GROUP BY`
- nema agregate
- nema `DISTINCT`
- nema `UNION`
- nema komplikovane `JOIN`-e

Primer:

```SQL
CREATE VIEW v_emp AS
SELECT id, name
FROM emp;

UPDATE v_emp
SET name='Pera'
WHERE id=1;
```

radi.

Za složenije view-ove možeš da koristiš:

- `INSTEAD OF` trigger
- ili stariji mehanizam `RULE`.

### 4 SQL Server

Isto:

Jednostavan view:

```SQL
CREATE VIEW v_emp AS
SELECT id, name
FROM emp;

UPDATE v_emp
SET name='Pera'
WHERE id=1;
```

radi.

Za view sa join-ima postoje ograničenja. Ako želiš punu kontrolu, koristiš:

```SQL
CREATE TRIGGER ...
ON v_emp

INSTEAD OF UPDATE
```

### 5 Oracle

Oracle podržava update nad jednostavnim view-ovima već veoma dugo.

Primer:

```SQL
CREATE VIEW v_emp AS
SELECT id, name
FROM emp;

UPDATE v_emp
SET name='Pera'
WHERE id=1;
```

radi.

Ako view sadrži:

- agregate
- `GROUP BY`
- `DISTINCT`
- `UNION`
- složene join-ove

onda obično nije update-bilan, osim ako ne definišeš:

```SQL
CREATE TRIGGER ...
INSTEAD OF UPDATE ON v_emp
```

Praktično pravilo koje važi skoro svuda:

- Ako je view projekcija jedne tabele bez agregacija i grupisanja, verovatno je update-bilan.
- Ako sadrži `JOIN`, `GROUP BY`, `DISTINCT`, agregate ili `UNION`, računaj da nije automatski update-bilan i proveri dokumentaciju ili koristi `INSTEAD OF` trigger.
- Od svih ovih baza, SQLite je jedina kod koje običan view nikada nije direktno update-bilan – uvek moraš da koristiš `INSTEAD OF` trigger.

`INSTEAD OF` triger, tu praktično rešiš sve u kodu, i tabelu koja se menja i primarne ključeve redova koji se menjaju ...?

Da, upravo tako.

`INSTEAD OF` trigger praktično kaže bazi:

"Nemoj ti da izvršiš `INSERT/UPDATE/DELETE` nad view-om, nego pozovi moj kod, a ja ću sam da odradim šta treba."

U triggeru ti:

- vidiš koja operacija je pozvana (`INSERT`, `UPDATE`, `DELETE`);
- imaš pristup starim i novim vrednostima reda:
  - u PostgreSQL: `OLD` i `NEW`
  - u SQLite: `OLD` i `NEW`
  - u SQL Serveru: pseudo-tabele `deleted` i `inserted`
  - u Oracle: `:OLD` i `:NEW`
- sam odlučuješ:
  - koju tabelu ili više njih menjaš,
  - koje redove menjaš,
  - kako koristiš primarne ključeve,
  - da li ćeš uopšte dozvoliti operaciju.

Na primer, zamisli view:

```SQL
CREATE VIEW v_emp_dept AS
SELECT e.id,
       e.name,
       d.name AS dept_name
FROM emp e
JOIN dept d ON d.id = e.dept_id;
```

Korisnik napiše:

```SQL
UPDATE v_emp_dept
SET name='Pera',
    dept_name='IT'
WHERE id=10;
```

Baza sama ne zna:

- da li `dept_name` treba da menja `dept.name`,
- da li treba da pronađe postojeći `department`,
- da li treba da napravi novi `department`,
- šta ako više zaposlenih deli isti `department`.

Zato `INSTEAD OF UPDATE` trigger dobija:

```SQL
OLD.id = 10
OLD.name = 'Marko'
OLD.dept_name = 'HR'
NEW.name = 'Pera'
NEW.dept_name = 'IT'
```

a ti napišeš:

```SQL
UPDATE emp
SET name = NEW.name
WHERE id = OLD.id;

UPDATE dept
SET name = NEW.dept_name
WHERE id = (
    SELECT dept_id
    FROM emp
    WHERE id = OLD.id
);
```

ili šta god smatraš ispravnim.

Zato se često kaže da je `INSTEAD OF` trigger neka vrsta mini ORM-a na bazi:

- aplikacija vidi jedan view,
- radi `INSERT/UPDATE/DELETE` nad njim,
- a trigger prevodi te operacije na jednu ili više fizičkih tabela.

To se dosta koristi u Oracle-u i SQL Serveru za pravljenje "updatable join view"-ova. U PostgreSQL-u je danas ređe jer su jednostavni view-ovi automatski update-bilni, ali za složene view-ove `INSTEAD OF` trigger je i dalje standardno rešenje.
