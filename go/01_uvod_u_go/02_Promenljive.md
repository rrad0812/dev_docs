
# Promenljive i konstante

- [[Uvod]][01]  
- [[Sadržaj]][00]  
- [[Tipovi podataka]][03]

## Promenljive

Počnimo sa deklarisanjem promenljive.

1. **Deklaracija bez inicijalizacije**:

   ```go
   var foo string
   ```

2. **Deklaracija sa inicijalizacijom**:

   ```go
   var foo string = "Go is awesome"
   ```

3. **Višestruke deklaracije**:

    ```go
    var foo, bar string = "Hello", "World"
    // ili
    var (
        foo string = "Hello"
        bar string  = "World"
    )
    ```

4. **Deklaracija sa zaključenim tipom**

   Tip je izostavljen, ali će biti zaključen:

   ```go
   var foo = "What's my type?"
   ```

5. **Skraćena deklaracija**

   Skraćena deklaracija, ovde izostavljamo i var ključnu reč, a tip je uvek implicitan. Ovako ćemo videti promenljive koje se deklarišu većinu vremena. Takođe koristimo walrus operator dodele `:=` za deklaraciju plus dodelu.

   ```go
   foo := "Shorthand!"
   ```

   **Napomena**: Skraćena deklaracija rade samo unutar tela funkcije.

## Konstante

Konstante možemo deklarisati pomoću `const` ključne reči, koja, kao što ime sugeriše, predstavljaju fiksne vrednosti koje se ne mogu ponovo dodeliti.

```go
const constant = "This is a constant"
```

Takođe je važno napomenuti da se samo konstante mogu dodeliti drugim konstantama.

```go
const a = 10
const b = a // ✅ Works

var a = 10
const b = a // ❌ a (variable of type int) is not constant (InvalidConstInit)
```

- [[Uvod]][01]  
- [[Sadržaj]][00]  
- [[Tipovi podataka]][03]

[01]: 01_uvod.md
[00]: toc.md
[03]: 03_Tipovi_podataka.md
