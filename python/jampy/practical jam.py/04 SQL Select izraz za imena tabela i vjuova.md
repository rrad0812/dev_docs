
# SQL Select izraz za imena tabela i vjuova na bazama podataka

[Nazad](README.md)

## Pregleda SELECT izraza za imena tabela i view-ova u najčešćim bazama

| DBMS | Upit |
| ---- | ---- |
| SQLite | sqlite_master |
| MySQL | information_schema.tables |
| PostgreSQL | information_schema.tables ili pg_catalog |
| SQL Server | information_schema.tables ili sys.objects |
| Oracle | ALL_OBJECTS / USER_OBJECTS |

### 1 SQLite

```sql
SELECT 
    name, 
    type
FROM 
    sqlite_master
WHERE 
    type IN (
        'table', 
        'view'
    )
ORDER BY 
    type, 
    name;
```

Ako ne želiš interne SQLite tabele:

```sql
SELECT 
    name, 
    type
FROM 
    sqlite_master
WHERE 
    type IN (
        'table', 
        'view'
    ) AND 
    name NOT LIKE 
        'sqlite_%'
ORDER BY 
    type, 
    name;
```

## 2 MySQL

```sql
SELECT 
    table_schema,
    table_name,
    table_type
FROM 
    information_schema.tables
WHERE 
    table_schema NOT IN (
        'information_schema',      
        'mysql',                     
        'performance_schema',
        'sys'
    ) AND 
    table_type IN (
        'BASE TABLE', 
        'VIEW'
    )
ORDER BY 
    table_schema, 
    table_name;
```

### 3 PostgreSQL

```sql
SELECT 
    table_schema,
    table_name,
    table_type
FROM 
    information_schema.tables
WHERE 
    table_schema NOT IN (
        'pg_catalog', 
        'information_schema'
    ) AND 
    table_type IN (
        'BASE TABLE', 
        'VIEW'
    )
ORDER BY 
    table_schema, 
    table_name;
```

Ili preko pg_catalog:

```sql
SELECT 
    n.nspname AS schema_name,
    c.relname AS object_name,
    CASE c.relkind
        WHEN 'r' THEN 'TABLE'
        WHEN 'v' THEN 'VIEW'
        WHEN 'm' THEN 'MATERIALIZED VIEW'
    END AS object_type
FROM 
    pg_class c
JOIN 
    pg_namespace n ON 
        n.oid = c.relnamespace
WHERE 
    c.relkind IN (
        'r',
        'v',
        'm'
    ) AND 
    n.nspname NOT IN (
        'pg_catalog',
        'information_schema'
    )
ORDER BY 
    n.nspname, 
    c.relname;
```

### 4 Microsoft SQL Server

Preko INFORMATION_SCHEMA:

```sql
SELECT 
    TABLE_SCHEMA,
    TABLE_NAME,
    TABLE_TYPE
FROM 
    INFORMATION_SCHEMA.TABLES
WHERE 
    TABLE_TYPE IN (
        'BASE TABLE', 
        'VIEW'
    )
ORDER BY 
    TABLE_SCHEMA, 
    TABLE_NAME;
```

Ili preko sistemskih tabela:

```sql
SELECT 
    s.name AS schema_name,
    o.name AS object_name,
    o.type_desc AS object_type
FROM 
    sys.objects o
JOIN 
    sys.schemas s ON 
        o.schema_id = s.schema_id
WHERE 
    o.type IN (
        'U', 
        'V'
    )
ORDER BY 
    s.name, 
    o.name;
```

Gde je:

- U = User Table
- V = View

### 5 Oracle

Samo objekti trenutnog korisnika:

```sql
SELECT 
    object_name,
    object_type
FROM 
    user_objects
WHERE 
    object_type IN (
        'TABLE', 
        'VIEW'
    )
ORDER BY 
    object_type, 
    object_name;
```

Svi objekti kojima korisnik ima pristup:

```sql
SELECT 
    owner,
    object_name,
    object_type
FROM 
    all_objects
WHERE 
    object_type IN (
        'TABLE', 
        'VIEW'
    )
ORDER BY 
    owner, 
    object_name;
```

Ako imaš DBA privilegije:

```sql
SELECT 
    owner,
    object_name,
    object_type
FROM 
    dba_objects
WHERE 
    object_type IN (
        'TABLE', 
        'VIEW'
    )
ORDER BY 
    owner, 
    object_name;
```

## Najprenosivije rešenje

Ako želiš što sličniji SQL između baza, onda važi:

| DBMS | Sistem pogled |
| ---- | ------------- |
| MySQL | information_schema.tables |
| PostgreSQL | information_schema.tables |
| SQL Server | information_schema.tables |
| Oracle | all_objects ili user_objects |
| SQLite | sqlite_master |

Praktično, SQLite je jedini "izuzetak". Za MySQL, PostgreSQL i SQL Server možeš koristiti gotovo identičan upit nad `information_schema.tables`.

[Nazad](README.md)
