---
marp: true
theme: default
paginate: false
size: 16:9
---

# .NET Framework 3.0

## C# 3.0 Features

---

## Neue Spracherweiterung C# 3.0

- LINQ
- Lambda-Ausdrücke
- Erweiterungsmethoden
- Automatische Eigenschaften
- Objekt- und Auflistungsinitialisierer
- Anonyme Typen
- Abfrage-Ausdrücke
- Partielle Klassen und Methoden

---

## LINQ (Language Integrated Query)

Weitere Informationen zu LINQ finden Sie im​ C# Programmierhandbuch von Microsoft​
​
[C# Programmierhandbuch/LINQ​](https://learn.microsoft.com/de-de/dotnet/csharp/linq/get-started/introduction-to-linq-queries​)

---

## LINQ

### Übersicht

- SQL-ähnliche Abfragen in C#:
  - LINQ to Objects
  - LINQ to XML
  - LINQ to SQL
- Vorteile:
  - Compiler-geprüfte Typzuweisungen
  - Vereinheitlichung von Datenbank- und Objektabfragen
  - Integration funktionaler Programmierkonzepte

---

## LINQ

```csharp
Namespaces: System.Linq, System.Xml.Linq, System.Data.Linq​
```

### Konzeptionelle Ziele von LINQ

- Bring Programmabfragen und Datenbankabfragen näher zusammen​
- Integration von funktionalen Programmierkonzepten in C# (Lambda Ausdrücke)​
- Integration von deklarativen Programmierelementen​ (Anonyme Typen, Objektinitialisierer)​

---

## LINQ: Beispiel

```csharp
string[] cities = { "London", "Vienna", "Paris", "Linz", "Brussels" };

// Abfrage
var result = from c in cities
             where c.StartsWith("L")
             orderby c
             select c.ToUpper();

foreach (var city in result)
    Console.WriteLine(city);
```

**Ergebnis:**

- LINZ
- LONDON

**LINQ Abfragen** werden übersetzt in Lambda Ausdrücke und Erweiterungsmethoden!​

---

## Lambda-Ausdrücke

Weitere Informationen zu Lambda Ausdrücken finden Sie im C# Programmierhandbuch von Microsoft​ 

[C# Programmierhandbuch/Lambda​](https://learn.microsoft.com/de-de/dotnet/csharp/language-reference/operators/lambda-operator​)

### Definition

- Kurzform für delegierte Werte
- Allgemeines Muster:
  
```csharp
  (Parameter) => Ausdruck
```

---

## Funktionale Programmierung

Bisher: Prozedurale (imperative) Programmierung und OOP​

- Methoden operieren auf dem Status (Fields) ihrer Objekte​

Ergänzung: Funktionale Elemente​

- Funktionen transformieren Daten durch ihre Operationen​
- Eigenschaften:​
  - Funktionen werden verkettet (Ergebnis der Operation ist Parameter der nächsten Operation)​
  - Funktionen werden als Parameter übergeben​
  - Funktionen haben keinen Status  keine Seiteneffekte ​

**=> Lambda Ausdrücke​**

---

## Lambda Ausdrücke - Entwicklung

Kurzform für delegierte Werte​

```csharp
delegate int Function(int x);	

int Square(int x) { return x * x; }​
int Inc(int x) { return x + 1; }​
```

**C# 1.0:**

```csharp
Function f;​
f = new Function(Square);	  ... f(3) ...	// 9​
f = new Function(Inc);	    ... f(3) ...	// 4​
```

---

## Lambda Ausdrücke - Entwicklung II

**C# 2.0:**

```csharp
Function f;​
f = delegate (int x) { return x * x; }	... f(3) ...	// 9​
f = delegate (int x) { return x + 1; }	... f(3) ...	// 4
```

**C# 3.0:**

```csharp
f = x => x * x; ... f(3) ...	// 9​
f = x => x + 1;	... f(3) ...	// 4​
```

---

## Beispiel für Lambda Ausdrücke

**Anwendung einer Funktion auf eine Folge von Zahlen:**

```csharp
delegate int Function (int x);​

static int[] Apply (Function f, int[] data) ​{​
  int[] result = new int[data.Length];​

  for (int i = 0; i < data.Length; i++) ​{​
    result[i] = f(data[i]);​
  }​
  return result;​
}
```

```csharp
int[] values = Apply ( i => i * i , new int[] {1, 2, 3, 4});​   // => 1, 4, 9, 16​
```

```csharp
int[] values = Apply ( x => x + 1 , new int[] {1, 2, 3, 4});​   // => 2, 3, 4, 5​
```

---

## Lambda Ausdrücke I

Generelle Form: `Parameters "=>" (Expr | Block)`

Lambdas können 0, 1 oder mehrere Parameter haben!

```csharp
() => ...         // no parameters
x => ...          // 1 parameter
(x, y) => ...     // 2 parameters
(x, y, z) => ...  // 3 parameters
```

Parameter können mit Typen und Modifizierer (ref/out) angegeben sein.

```csharp
(int x) => ...    // (…) muss angegeben werden, wenn ein Typ angegeben ist
(string s, int x) => ...
(ref int x ) => ...
(int x, out int y) => ...
```

---

## Lambda Ausdrücke II

Parametertypen werden üblicherweise nicht angegeben.
Auf diese kann der Compiler vom Delegate schließen!

```csharp
delegate bool Func(int x, int y);
Func f = (x, y) => x > y;
```

---

## Lambda Ausdrücke III

`Parameters "=>" (Expr | Block)`

Üblicherweise ist auf der rechten Seite ein Rückgabe-Ausdruck

```csharp
x => x * x          // returns x * x
(x, y) => x + y     // returns x + y
```

Auf der rechte Seite kann auch ein Block definiert werden

```csharp
n =>  {
  int sum = 0;
  for (int i = 1; i <= n; i++) {
    sum += i;
  }
  return sum;
}
```

---

## Lambda Ausdrücke IV

`Parameters "=>" (Expr | Block)`

Auf der rechten Seite muss nicht unbedingt ein Wert zurückgegeben werden

```csharp
delegate void Proc(int x);

Proc p = x => { Console.WriteLine(x); };
```

Auf der rechten Seite können auf lokalen Variable welche außerhalb definiert sind, zugegriffen werden

```csharp
int sum = 0;

Proc p = x => { sum += x; };
```

---

## Lambda Ausdrücke V - Beispiele

```csharp
delegate TRes Func<TRes> ();
delegate TRes Func<T1, TRes> (T1 a);
delegate TRes Func<T1, T2, TRes> (T1 a, T2 b);
delegate TRes Func<T1, T2, T3, TRes> (T1 a, T2 b, T3 c);
delegate TRes Func<T1, T2, T3, T4, TRes> (T1 a, T2 b, T3 c, T4 d);

delegate void Action ();
delegate void Action<T1> (T1 a);
delegate void Action<T1, T2> (T1 a, T2 b);
delegate void Action<T1, T2, T3> (T1 a, T2 b, T3 c);
delegate void Action<T1, T2, T3, T4> (T1 a, T2 b, T3 c, T4 d);
```

Beispiele:

```csharp
Func<int, int> f1 = x => 2 * x + 1;                             f1(3);                // 7
Func<int, int, bool> f2 = (x, y) => x > y;                      f2(5, 3);             // true
Func<string, int, string> f3 = (s, i) => s.Substring(i);        f3("Hello", 2);       // "llo"
Func<int[]> f4 = () => new int[] { 1, 2, 3, 4, 5 };             f4();                 // {1, 2, 3, 4, 5}
Action a1 = () => { Console.WriteLine("Hello"); };              a1();                 // Hello
Action<int, int> a2 = (x, y) => { Console.WriteLine(x + y); };  a2(1, 2);             // 3
```

---

## Erweiterungsmethoden

Weitere Informationen zu Erweiterungsmethoden finden Sie im C# Programmierhandbuch von Microsoft 

[C# Programmierhandbuch/Extension](https://learn.microsoft.com/de-de/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)

### Konzept

- Erweiterung bestehender Typen (class, struct, interface)
- Beispiel:

```csharp
  public static class FractionUtils
  {
      public static Fraction Inverse(this Fraction f)
      {
          return new Fraction(f.n, f.z);
      }
  }
```

---

## Erweiterungsmethoden - Definition

Wir benötigen eine zusätzliche statische Klasse für die Definition der Erweiterungen

Erweiterungsmethoden für die Klasse Fraction

```csharp
static class FractionUtils
{
  public static Fraction Inverse (this Fraction f)
  {
    return new Fraction(f.n, f.z);
  }
  public static void Add (this Fraction f, int x)
  {
    f.z += x * f.n;
  }
}
```

- müssen in eine statische Klasse deklariert werden
- und der erster Parameter muss mit this und dem Erweiterungstyp deklariert werden

---

## Erweiterungsmethoden - Verwendung

Nun können die Erweiterungsmethoden auf den Typ angewendet werden

Verwendung

```csharp
Fraction f = new Fraction(1, 2);

f = f.Inverse();
f = FractionUtils.Inverse(f);

f.Add(2);
FractionUtils.Add(f, 2);
```

- Die Erweiterung kann als Instanz Methode aufgerufen werden
- Die Erweiterung kann jedoch auch explizit aufgerufen werden
- Innerhalb der Erweiterung können nicht auf die privaten Mitglieder zugegriffen werden

---

## Erweiterungsmethoden - Vordefiniert

**Das Geheimnis von LINQ liegt in den vordefinierten Erweiterungsmethoden!**

`System.Linq.Enumerable hat vordefinierte Erweiterungsmethoden für IEnumerable<T>`

```csharp
namespace System.Linq
{
  public static class Enumerable
  {
    public static IEnumerable<T> Where<T> (this IEnumerable<T> source, Func<T, bool> f)
    {
      ... returns all values x from source, for which f(x) == true ...
    }
    ...
  }
}
```

---

## Erweiterungsmethoden - Vordefiniert II

Verwendung:

```csharp
using System.Linq;          // macht Where sichtbar
...
List<int> list = ... list of integer values   ...;
IEnumerable<int> result = list.Where(i => i > 0);   // Enumerable.Where(list, i => i > 0)
```

Die Typinferenz übernimmt der Compiler

```csharp
list ist deklariert als List<int>
==> T = int
==> i is of type int
```

Kann auf alle Sammlungen und Arrays angewendet werden!

```csharp
string[] a = {"Bob", "Ann", "Sue", "Bart"};

IEnumerable<string> result = a.Where(s => s.StartsWith("B"));
```

---

## Automatische Eigenschaften

Weitere Informationen zu Automatisch implementierten Eigenschaften finden Sie im C# Programmierhandbuch von Microsoft

[C# Programmierhandbuch/AutoProp](https://learn.microsoft.com/de-de/dotnet/csharp/programming-guide/classes-and-structs/auto-implemented-properties)

### Syntax

```csharp
public string Name { get; set; }
```

---

## Anonyme Typen

Weitere Informationen zu Anonyme Typen finden Sie im C# Programmierhandbuch von Microsoft 

[# Programmierhandbuch/Anonymous](https://learn.microsoft.com/de-de/dotnet/csharp/fundamentals/types/anonymous-types)

### Beispiel

```csharp
var obj = new { Name = "John", Id = 100 };

Console.WriteLine(obj.Name); // Ausgabe: John
```

---

## Anonyme Typen I

Type die während der Ausführung erzeugt werden (mit Angabe von Eigenschaften)

```csharp
var obj = new { Name = "John", Id = 100 };  // erzeugt einen neuen Typen mit den Eigenschaften Name und Id
// var Anonymer Typ auf der rechten Seite - Typinference!
```

Erzeugt =>

```csharp
class ??? {
  public int Id { get; private set; }
  public string Name { get; private set; }
}
```

```csharp
??? obj = new ???();
obj.Name = "John";
obj.Id = 100;
```

```csharp
var v1 = “Linda“;           // muss mit einem Wert initialisiert werden,
var v2 = default(string);   // oder mit dem Default-Wert eines Typs!
```

---

## Anonyme Typen II

`var obj = new { Id = x, student.Name };`

- Generiert Eigenschaften (Id, Name) welche schreibgeschützt sind!
- Generiert Eigenschaften mit expliziten Namen (Id = x) oder implizit (student.Name). Explizite und implizite Namen können abwechseln.
- Anonyme Typen sind kompatibel mit dem Typ Object
- Compiler generiert automatisch die ToString() Methode für alle anonyme Typen `Console.WriteLine(obj);` => `{ Id = 1234, Name = "John Doe" }`
- Anonyme Typen werden oft in Projektionen von Lambda Ausdrücken gebraucht

```csharp
List<Student> list = ...;
var result = list.Select(s => new { s.Name, s.Subject } );

// result ist IEnumerable<???>
```

---

## Typinference - var

`var x = ...;`

- var kann nur lokale Variable deklarieren (keine Parameter und Eigenschaften)
- Variable muss bei der Deklaration initialisiert werden.
- Der Typ der Variablen ist inferred vom Initialisierungsausdruck.

Typische Anwendungen

```csharp
var obj = new { Width = 100, Height = 50 };             // ??? obj = 
var res = list.Select(s => new { s.Name, s.Subject });  // IEnumerable<???> res = ...
var dict = new Dictionary<string, int>();               // Dictionary<string, int> dict = new Dictionary<string, int>();
```

Im Prinzip ist folgendes möglich (aber nicht empfehlenswert!)

```csharp
var x = 3;              // int x = 3;
var s = "John";         // string s = "John";
```

---

## Query Expression

Weitere Informationen zu Abfrage-Ausdrücke finden Sie im C# Programmierhandbuch von Microsoft

[C# Programmierhandbuch/LinqQueries](https://learn.microsoft.com/de-at/dotnet/csharp/linq/get-started/query-expression-basics)

---

## Partielle Klassen und Methoden

Weitere Informationen zu Partielle Klassen und Methoden finden Sie im C# Programmierhandbuch von Microsoft

[C# Programmierhandbuch/PartialMethods](https://learn.microsoft.com/de-de/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)

**Beispiel:**

```csharp
public partial class Accelerator
{
    partial void BeforeAccelerate();
}
```

---

## Klassen aus mehreren Teilen

```csharp
public partial class C {                // Datei Part1.cs
  int x;
  public void M1(...) {...}
  public int M2(...) {...}
}
```

```csharp
public partial class C {                // Datei Part2.cs
  string y;
  public void M3(...) {...}
  public void M4(...) {...}
  public void M5(...) {...}
}
```

**Zweck:**

- Teile können nach Funktionalitäten gruppiert werden
- verschiedene Entwickler können gleichzeitig an derselben Klasse arbeiten
- erster Teil kann maschinengeneriert sein, zweiter Teil handgeschrieben
