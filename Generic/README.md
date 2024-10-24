---
marp: true
---

# Generische Typen in C#

Generische Typen sind ein leistungsfähiges Feature in C#, das es Entwicklern ermöglicht, Klassen, Schnittstellen und Methoden zu definieren, die mit einem oder mehreren Platzhaltern für Datentypen arbeiten können. Dieses Konzept erhöht die Flexibilität und Wiederverwendbarkeit von Code und verbessert die Typensicherheit zur Compile-Zeit.

---

## Vorteile von Generischen Typen

- **Typensicherheit**: Generische Typen bieten zur Compile-Zeit eine starke Typprüfung. Dadurch werden Laufzeitfehler, die durch falsche Typumwandlungen entstehen können, minimiert.

- **Wiederverwendbarkeit**: Mit generischen Typen können Entwickler eine einzige Implementierung erstellen, die mit verschiedenen Datentypen funktioniert. Zum Beispiel kann eine generische Klasse Buffer<T> für verschiedene Typen wie int, string oder benutzerdefinierte Typen verwendet werden.

- **Leistung**: Generische Typen vermeiden die Kosten für Boxen und Unboxing, die auftreten, wenn Werttypen in Referenztypen umgewandelt werden. Dies führt zu einer besseren Performance, insbesondere bei der Arbeit mit Sammlungen von Werttypen.

---

## Probleme ohne generische Typen

Klassen mit unterschiedlichen Elementtypen

```csharp
class Buffer {
  private object[] data;
  public void Put(object x) {...}
  public object Get() {...}
}
```

**Probleme**
- Typumwandlungen nötig
  `buffer.Put(3); // Boxing kostet Zeit`
  `int x = (int)buffer.Get(); // Typumwandlung kostet Zeit`
- Homogenität kann nicht erzwungen werden
  `buffer.Put(3); buffer.Put(new Rectangle());`
  `Rectangle r = (Rectangle)buffer.Get(); // kann zu Laufzeitfehler führen!`
- Spezielle Typen IntBuffer, RectangleBuffer, ... führen zu Redundanz

---

## Generische Klasse Buffer

```csharp
class Buffer<Element> {         // Element ist der generische Typ (Platzhalter)
  private Element[] data;
  public Buffer(int size) {...}
  public void Put(Element x) {...}
  public Element Get() {...}
}
```

- geht auch für structs und interface
  
- Platzhaltertyp Element kann wie normaler Typ verwendet werden
  
---

## Benutzung

```csharp
Buffer<int> a = new Buffer<int>(100);
a.Put(3); // nur int-Parameter erlaubt; kein Boxing
int i = a.Get(); // keine Typumwandlung nötig
```

```csharp
Buffer<Rectangle> b = new Buffer<Rectangle>(100);
b.Put(new Rectangle()); // nur Rectangle-Parameter erlaubt
Rectangle r = b.Get(); // keine Typumwandlung nötig
```

**Vorteile**
- Homogene Datenstruktur mit Compilezeit-Typprüfung
- Effizient (kein Boxing, keine Typumwandlungen)

Generizität auch in Ada, Eiffel, C++ (Templates), Java 5.0

---

## Mehrere Platzhaltertypen

**Buffer mit Prioritäten**

```csharp
class Buffer <Element, Priority> {
  private Element[] data;
  private Priority[] prio;
  public void Put(Element x, Priority prio) {...}
  public void Get(out Element x, out Priority prio) {...}
}
```

---

## Verwendung

```csharp
Buffer<int, int> a = new Buffer<int, int>();
a.Put(100, 0);
int elem, prio;
a.Get(out elem, out prio);
```

```csharp
Buffer<Rectangle, double> b = new Buffer<Rectangle, double>();
b.Put(new Rectangle(), 0.5);
Rectangle r; double prio;
b.Get(out r, out prio);
```

C++ erlaubt auch die Angabe von Konstanten als Platzhalter, C# nicht

---

## Constraints

Annahmen über Platzhaltertypen werden als Basistypen ausgedrückt

```csharp
class OrderedBuffer <Element, Priority> where Priority: IComparable {
  Element[] data;
  Priority[] prio;
  int lastElem;
  ...
  public void Put(Element x, Priority p) {
    int i = lastElement;
    while (i >= 0 && p.CompareTo(prio[i]) > 0) {  
      data[i+1] = data[i]; prio[i+1] = prio[i]; i--;
    }
    data[i+1] = x; prio[i+1] = p;
  }
}
```

Erlaubt Operationen auf Elemente von Platzhaltertypen

---

## Verwendung

```csharp
OrderedBuffer<int, int> a = new OrderedBuffer<int, int>();
a.Put(100, 3);
```

**Parameter muss `IComparable` unterstützen**

---

## Mehrere Constraints möglich

```csharp
class OrderedBuffer <Element, Priority>
  where Element: MyClass
  where Priority: IComparable
  where Priority: ISerializable {
  ...
  public void Put(Element x, Priority p) {...}
  public void Get(out Element x, out Priority p) {...}
}
```

**Verwendung**

```csharp
OrderedBuffer<MySubclass, MyPrio> a = new OrderedBuffer<MySubclass, MyPrio>();
...
a.Put(new MySubclass(), new MyPrio(100));
```

Platzhalter `MySubclass` muss Unterklasse von MyClass sein
Platzhalter `MyPrio` muss IComparable und ISerializable unterstützen

---

## Konstruktor-Constraints

Zum Erzeugen neuer Objekte in einem generischen Typ

```csharp
class Stack<T, E> where E: Exception, new() {
  T[] data = ...;
  int top = -1;
  public void Push(T x) {
    if (top >= data.Length)
      throw new E();
    else
      data[++top] = x;
  }
}
```

`new()`... spezifiziert, dass der Platzhalter E einen parameterlosen Konstruktor haben muss.

---

## Verwendung

```csharp
class MyException: Exception {
  public MyException(): base("stack overflow or underflow") {}
}
```

```csharp
Stack<int, MyException> stack = new stack.Push(3); Stack<int, MyException>();
...
```

## Generizität und Vererbung

```csharp
class Buffer <Element>: List<Element> {
  ...
  public void Put(Element x) {
  this.Add(x); // Add wurde von List geerbt
  }
}
```

---

Von welchen Klassen darf eine generische Klasse erben?

- von einer gewöhnlichen Klasse                         
  
  `class T<X>: B {...}`

- von einer konkretisierten generischen Klasse          
  
  `class T<X>: B<int> {...}`

- von einer generischen Klasse mit gleichem Platzhalter 
    
  `class T<X>: B<X> {...}`

---

## Kompatibilität in Zuweisungen

Zuweisung von `T<x>` an gewöhnliche Oberklasse

```csharp
class A {...}
class B<X>: A {...}
class C<X,Y>: A {...}
```

```csharp
A a1 = new B<int>();
A a2 = new C<int, float>();
```

---

Zuweisung von `T<x>` an generische Oberklasse

```csharp
class A<X> {...}
class B<X>: A<X> {...}
class C<X,Y>: A<X> {...}
```

```csharp
A<int> a1 = new B<int>();       // erlaubt, wenn korrespondierende Platzhalter
A<int> a2 = new C<int, float>();// durch denselben Typ ersetzt wurden
```

```csharp
A<int> a3 = new B<short>();     // Verboten!
```

---

## Überschreiben von Methoden

```csharp
class Buffer<Element> {
  ...
  public virtual void Put(Element x) {...}
}
```

Wenn von konkretisierter Klasse geerbt

```csharp
class MyBuffer : Buffer<int> {
  ...
  public override void Put(int x) {...}
}
```
...Element wird durch konkreten Typ int ersetzt

---

Wenn von generischer Klasse geerbt

```csharp
class MyBuffer<Element> : Buffer<Element> {
  ...
  public override void Put(Element x) {...}
}
```
...Element bleibt als Platzhalter

Folgendes geht nicht (man kann keinen Platzhalter erben)

```csharp
class MyBuffer : Buffer<Element> {
  ...
  public override void Put(Element x) {...}
}
```

---

## Laufzeittypprüfungen

Konkretisierter generischer Typ kann wie normaler Typ verwendet werden

```csharp
Buffer<int> buf = new Buffer<int>(20);
object obj = buf;
if (obj is Buffer<int>)
  buf = (Buffer<int>) obj;

Type t = typeof(Buffer<int>);
Console.WriteLine(t.Name); // => Buffer[System.Int32]
```

Reflection liefert auch die konkreten Platzhaltertypen!

---

