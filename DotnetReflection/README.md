---
marp: true
---

# Reflection in C#

**Untertitel:** Einblicke in die Laufzeit-Introspektion

---

## Agenda

1. **Was ist Reflection?** (Einführung)
2. **Warum Reflection nutzen?** (Motivation & Anwendungsfälle)
3. **Grundlagen:** `System.Reflection` Namespace
    * `Assembly`, `Type`, `MemberInfo` & Co.
    * `BindingFlags`
4. **Praktische Beispiele:**
    * Typinformationen abrufen
    * Member auflisten und untersuchen
    * Instanzen erstellen
    * Methoden aufrufen & Eigenschaften/Felder lesen/schreiben
    * Attribute auslesen
5. **Vor- und Nachteile**
6. **Vertiefung:**
    * Performance-Überlegungen & Optimierung
    * Reflection Emit (Kurzer Ausblick)
    * Expression Trees als Alternative
    * Generics und Reflection
    * Sicherheitsaspekte
7. **Zusammenfassung & Best Practices**
8. **Fragen & Antworten**

---

## Was ist Reflection? (Einführung)

* **Definition:** Reflection ist die Fähigkeit eines Programms, seine eigene Struktur (Metadaten) zur Laufzeit zu untersuchen und zu manipulieren.
* **Metadaten:** Informationen über Typen, Methoden, Eigenschaften, Felder, Attribute etc., die beim Kompilieren in einer Assembly gespeichert werden.
* **Kernidee:** Anstatt zur Kompilierzeit festzulegen, was aufgerufen wird, kann dies zur Laufzeit dynamisch entschieden werden.
* Man kann quasi "zurückblicken" (reflect) auf den Code selbst.

---

## Warum Reflection nutzen? (Motivation & Anwendungsfälle)

* **Dynamisches Laden von Assemblies:** z.B. für Plugin-Systeme, bei denen Module zur Laufzeit geladen werden, ohne dass das Hauptprogramm sie zur Kompilierzeit kennen muss.
* **Objektinspektion:** Debugger, Serialisierungs-Frameworks (z.B. `Newtonsoft.Json`, `System.Text.Json`, XML Serializer), ORM-Mapper (z.B. Entity Framework), Property Grids in UI-Frameworks.
* **Späte Bindung (Late Binding):** Aufruf von Methoden oder Zugriff auf Member, deren Namen erst zur Laufzeit bekannt sind (z.B. aus Konfigurationsdateien oder Benutzereingaben).
* **Attribut-basierte Programmierung:** Auslesen von Custom Attributes, um Verhalten zu steuern (z.B. Validierungsregeln, Routing in Web-Frameworks, Test-Frameworks wie NUnit/xUnit).
* **Erstellung generischer Werkzeuge:** Tools, die mit beliebigen Typen arbeiten können, ohne sie spezifisch zu kennen (z.B. Dependency Injection Container).
* **(Selten/Fortgeschritten):** Dynamische Code-Generierung (`Reflection.Emit`).

---

## Grundlagen: `System.Reflection` Namespace

* Der zentrale Namespace für alle Reflection-Operationen.
* **Wichtige Klassen:**
  * `Assembly`: Repräsentiert eine geladene .NET Assembly (.dll oder .exe).
    * Laden: `Assembly.Load()`, `Assembly.LoadFrom()`, `Assembly.GetExecutingAssembly()`, `typeof(MyClass).Assembly`
    * `Module`: Ein Teil einer Assembly (meist gibt es nur ein Modul pro Assembly).
    * `Type`: **Die zentrale Klasse!** Repräsentiert Typdefinitionen (Klassen, Interfaces, Structs, Enums, Delegaten...).
    * Abrufen: `typeof(MyClass)`, `myObject.GetType()`, `Type.GetType("Namespace.MyClass, AssemblyName")`
    * `MemberInfo`: Abstrakte Basisklasse für alle Typ-Member.
      * `Name`, `DeclaringType`, `MemberType` (Field, Property, Method, Constructor, Event, TypeInfo, Custom, NestedType)
    * Spezifische Member-Klassen:
      * `FieldInfo`: Felder
      * `PropertyInfo`: Eigenschaften (Properties)
      * `MethodInfo`: Methoden
      * `ConstructorInfo`: Konstruktoren
      * `EventInfo`: Ereignisse
      * `ParameterInfo`: Parameter von Methoden/Konstruktoren

---

## Grundlagen: Member abrufen & `BindingFlags`

* Die `Type`-Klasse bietet Methoden zum Abrufen von Membern:
  * `GetMembers()`, `GetFields()`, `GetProperties()`, `GetMethods()`, `GetConstructors()`, `GetEvents()` (holen alle passenden Member)
  * `GetMember()`, `GetField()`, `GetProperty()`, `GetMethod()`, `GetConstructor()`, `GetEvent()` (holen einen spezifischen Member anhand des Namens und ggf. Parameter)
* **`BindingFlags` (Enum):** **Sehr wichtig!** Steuert, *welche* Member gesucht werden. Wird als Bitmaske kombiniert.
  * `Instance` vs. `Static`: Instanz- oder statische Member?
  * `Public` vs. `NonPublic`: Öffentliche oder private/interne/protected Member?
  * `DeclaredOnly`: Nur Member, die direkt in diesem Typ deklariert sind (nicht geerbt)?
  * `FlattenHierarchy`: Schließt `public static` und `protected static` Member von Basisklassen ein. Standard für `GetMembers()`.
* **Beispiel:** Einen privaten Instanzmember namens "MyPrivateMethod" finden:

```csharp
    MethodInfo method = type.GetMethod(
        "MyPrivateMethod",
        BindingFlags.Instance | BindingFlags.NonPublic
    );
```

---

## Praktisches Beispiel: Typinformationen abrufen

```csharp
// Typ über typeof Operator (Kompilierzeit)
Type stringType = typeof(string);

// Typ über Instanz (Laufzeit)
string myString = "Hello Reflection!";
Type instanceType = myString.GetType();

// Typ über Namen (Laufzeit, Assembly muss bekannt sein/geladen werden)
// Vorsicht: Fehleranfällig bei Refactoring! Assembly-qualifizierter Name nötig.
// Der genaue Name kann je nach .NET Version/Assembly variieren.
Type listType = Type.GetType("System.Collections.Generic.List`1[[System.String]], System.Private.CoreLib");
// Falls listType null ist, ist der Name oder die Assembly falsch.

// Informationen ausgeben
Console.WriteLine($"Name: {stringType.Name}"); // String
Console.WriteLine($"Full Name: {stringType.FullName}"); // System.String
Console.WriteLine($"Is Class: {stringType.IsClass}"); // True
Console.WriteLine($"Assembly: {stringType.Assembly.GetName().Name}"); // z.B. System.Private.CoreLib

if (listType != null) {
    Console.WriteLine($"List Type Full Name: {listType.FullName}");
} else {
    Console.WriteLine("List<string> konnte nicht per Name geladen werden.");
}
```

---

## Praktisches Beispiel: Member auflisten

```csharp
using System.Reflection;
using System.Linq; // Für Take()

// Beispieltyp
Type type = typeof(System.DateTime);

Console.WriteLine($"--- Properties von {type.Name} (Public Instance) ---");
PropertyInfo[] properties = type.GetProperties(BindingFlags.Public | BindingFlags.Instance);
foreach (PropertyInfo prop in properties)
{
    // Zeigt Typ und Name der Eigenschaft
    Console.WriteLine($"{prop.PropertyType.Name} {prop.Name}");
}

Console.WriteLine($"\n--- Methoden von {type.Name} (Public Instance, nur in DateTime deklariert, Auszug) ---");
MethodInfo[] methods = type.GetMethods(BindingFlags.Public | BindingFlags.Instance | BindingFlags.DeclaredOnly);
// Nur die ersten 5 zur Demo ausgeben
foreach (MethodInfo method in methods.Take(5))
{
    // Zeigt Rückgabetyp und Name der Methode
    Console.WriteLine($"{method.ReturnType.Name} {method.Name}(...)");
}
```

---

## Praktisches Beispiel: Instanzen erstellen

```csharp
using System;
using System.Text;
using System.Collections.Generic;
using System.Reflection;

// Beispielklasse für Konstruktor mit Parameter
public class MyClass
{
    public int Value { get; set; }
    public MyClass() { Console.WriteLine("Parameterloser Konstruktor aufgerufen."); }
    public MyClass(int value) {
        this.Value = value;
        Console.WriteLine($"Konstruktor mit Wert {value} aufgerufen.");
    }
}

// --- Instanziierung ---

// 1. Über Activator (einfach, braucht parameterlosen Konstruktor oder Argumente passen)
Type typeToCreate = typeof(System.Text.StringBuilder);
object instance1 = Activator.CreateInstance(typeToCreate);
Console.WriteLine($"Typ erstellt via Activator: {instance1?.GetType().Name}");

// Activator kann auch Konstruktoren mit Parametern finden (weniger explizit als ConstructorInfo)
object instanceWithParamViaActivator = Activator.CreateInstance(typeof(MyClass), 456);


// 2. Über ConstructorInfo (expliziter, volle Kontrolle)
Type type = typeof(MyClass);
// Finde Konstruktor mit einem int-Parameter
ConstructorInfo ctor = type.GetConstructor(new Type[] { typeof(int) });
if (ctor != null)
{
    // Rufe den gefundenen Konstruktor mit Argumenten auf
    object instance2 = ctor.Invoke(new object[] { 123 });
    Console.WriteLine($"Instanz mit Parameter via ConstructorInfo erstellt. Value: {((MyClass)instance2).Value}");
}

// 3. Für Generische Typen:
// Hole den offenen generischen Typ Definition
Type openListType = typeof(List<>);
// Erstelle einen geschlossenen Typ zur Laufzeit (z.B. List<int>)
Type listIntType = openListType.MakeGenericType(typeof(int));
// Instanziiere den geschlossenen Typ
object genericInstance = Activator.CreateInstance(listIntType);
Console.WriteLine($"Generische Instanz erstellt: {genericInstance?.GetType().FullName}"); // System.Collections.Generic.List`1[[System.Int32]]
```

---

## Praktisches Beispiel: Methoden aufrufen & Memberzugriff

```csharp
using System;
using System.Reflection;

// Beispielklasse
public class MyClass
{
    public string Name { get; set; } = "Initial";
    private int _counter = 0;

    public string DoSomething(string prefix)
    {
       _counter++;
       return $"{prefix}: {Name} (Count: {_counter})";
    }

    private void ResetCounter()
    {
        _counter = 0;
        Console.WriteLine("Counter wurde zurückgesetzt (private Methode).");
    }
}

// --- Zugriff ---
MyClass myObj = new MyClass();
Type myType = typeof(MyClass);

// Eigenschaft lesen: myObj.Name
PropertyInfo nameProp = myType.GetProperty("Name");
object nameValue = nameProp.GetValue(myObj);
Console.WriteLine($"Initial Name Property: {nameValue}"); // Initial

// Eigenschaft schreiben: myObj.Name = "Reflected Value"
nameProp.SetValue(myObj, "Reflected Value");
nameValue = nameProp.GetValue(myObj); // Erneut lesen
Console.WriteLine($"Updated Name Property: {nameValue}"); // Reflected Value

// Methode aufrufen: myObj.DoSomething("Test")
MethodInfo doSomethingMethod = myType.GetMethod("DoSomething", new Type[] { typeof(string) });
object result = doSomethingMethod.Invoke(myObj, new object[] { "Test" });
Console.WriteLine($"DoSomething Result 1: {result}"); // Test: Reflected Value (Count: 1)
result = doSomethingMethod.Invoke(myObj, new object[] { "Again" });
Console.WriteLine($"DoSomething Result 2: {result}"); // Again: Reflected Value (Count: 2)

// Private Methode aufrufen: myObj.ResetCounter()
MethodInfo resetMethod = myType.GetMethod("ResetCounter", BindingFlags.Instance | BindingFlags.NonPublic);
if (resetMethod != null)
{
    resetMethod.Invoke(myObj, null); // Keine Parameter
}
result = doSomethingMethod.Invoke(myObj, new object[] { "After Reset" });
Console.WriteLine($"DoSomething Result 3: {result}"); // After Reset: Reflected Value (Count: 1)

```

---

## Praktisches Beispiel: Attribute auslesen

```csharp
using System;
using System.Reflection;
using System.ComponentModel; // Für DescriptionAttribute

// Beispielklasse mit Attributen
public class MyService
{
    [Obsolete("Diese Methode ist veraltet. Benutze NewMethod stattdessen.", false)] // isWarning = false -> Fehler
    [Description("Eine alte, nicht mehr zu verwendende Methode.")]
    public void OldMethod() { /* ... */ }

    public void NewMethod() { /* ... */ }
}

// --- Attribute auslesen ---
Type serviceType = typeof(MyService);
MethodInfo oldMethod = serviceType.GetMethod("OldMethod");

if (oldMethod != null)
{
    Console.WriteLine($"--- Attribute für Methode: {oldMethod.Name} ---");

    // Alle Attribute holen (generische Variante ist typsicherer)
    var obsoleteAttributes = oldMethod.GetCustomAttributes<ObsoleteAttribute>(true); // true = geerbte Attribute auch suchen
    foreach (var attr in obsoleteAttributes)
    {
        Console.WriteLine($"[Obsolete] Message: {attr.Message}, IsError: {attr.IsError}");
    }

    // Einzelnes Attribut holen (oder null, wenn nicht vorhanden)
    var descriptionAttr = oldMethod.GetCustomAttribute<DescriptionAttribute>(true);
    if (descriptionAttr != null)
    {
        Console.WriteLine($"[Description] Text: {descriptionAttr.Description}");
    }

    // Prüfen, ob ein Attribut vorhanden ist (effizienter, wenn man nur die Existenz prüfen will)
    bool hasObsolete = oldMethod.IsDefined(typeof(ObsoleteAttribute), true);
    Console.WriteLine($"Hat [Obsolete] Attribut? {hasObsolete}"); // True
}

MethodInfo newMethod = serviceType.GetMethod("NewMethod");
if(newMethod != null)
{
    bool hasObsoleteOnNew = newMethod.IsDefined(typeof(ObsoleteAttribute), true);
    Console.WriteLine($"\nHat NewMethod [Obsolete] Attribut? {hasObsoleteOnNew}"); // False
}
```

---

## Vor- und Nachteile

* **Vorteile:**
  * **Flexibilität:** Ermöglicht hochdynamische und anpassbare Systeme (Plugins, Konfiguration zur Laufzeit).
  * **Erweiterbarkeit:** Einfaches Hinzufügen neuer Funktionalität durch dynamisches Laden und Untersuchen von Assemblies/Typen.
  * **Generische Werkzeuge:** Erstellung von Frameworks und Bibliotheken, die mit zur Kompilierzeit unbekannten Typen arbeiten können (Serialisierung, ORM, DI).
  * **Introspektion:** Nützlich für Debugging, Diagnose und Tools, die Code analysieren.

* **Nachteile:**
  * **Performance:** Reflection ist *deutlich* langsamer als direkter Code-Aufruf. Der Overhead für Metadaten-Suche, Typüberprüfungen, Sicherheitschecks und Parameter-Marshalling ist signifikant.
  * **Komplexität:** Code, der Reflection verwendet, ist oft schwerer zu lesen, zu verstehen und zu debuggen. Magische Strings für Membernamen sind fehleranfällig.
  * **Verringerte Typsicherheit:** Fehler (z.B. Tippfehler in Methodennamen, falsche Parametertypen, Member nicht vorhanden) werden erst zur **Laufzeit** erkannt, nicht zur Kompilierzeit. Das führt zu unerwarteten Exceptions im Betrieb.
  * **Refactoring-Probleme:** Standard-Refactoring-Tools (z.B. Umbenennen von Methoden/Properties in einer IDE) erkennen oft keine Reflection-Aufrufe über String-Literale. Das kann leicht zu Laufzeitfehlern nach einem Refactoring führen.
  * **Sicherheit:** Kann Zugriffsmodifikatoren (`private`/`internal`) umgehen. Dies kann Kapselung brechen und Sicherheitsrisiken bergen, wenn unbedacht eingesetzt (erfordert i.d.R. volle Vertrauensstellung bzw. spezifische Berechtigungen).

---

## Vertiefung: Performance & Optimierung

* **Problem:** Wiederholte Aufrufe von `MethodInfo.Invoke()`, `PropertyInfo.GetValue/SetValue()`, `Activator.CreateInstance()` und das wiederholte Abrufen von `Type`/`MemberInfo`-Objekten können Engpässe verursachen.
* **Optimierungsstrategien:**
  * **Caching:** `Type`, `MethodInfo`, `PropertyInfo`, `ConstructorInfo` etc. nicht bei jedem Aufruf neu per `Get...()` holen. Ermitteln Sie sie einmal und speichern Sie sie in einem `static Dictionary` oder einem anderen geeigneten Cache. Der Key könnte der Typ oder ein Membername sein.
  * **Delegaten erstellen (Stark empfohlen für wiederholte Aufrufe):**
    * `Delegate.CreateDelegate()`: Erstellt einen stark typisierten Delegaten aus einer `MethodInfo`. Der Aufruf des Delegaten ist *fast* so schnell wie ein direkter Methodenaufruf. Benötigt aber den genauen Delegattyp.
    * **`Expression Trees`** (siehe nächste Folie): Noch flexibler. Können zur Laufzeit zu Delegaten kompiliert werden (`Expression.Compile()`). Oft die performanteste dynamische Option, wenn Caching allein nicht reicht. Erzeugt optimierten Code.
    * **`dynamic` Keyword:** Nutzt intern die DLR (Dynamic Language Runtime), die Caching-Mechanismen verwendet. Kann einfacher sein als manuelle Reflection für späte Bindung, hat aber immer noch Performance-Overhead gegenüber statischem Code.
    * **Source Generators (.NET 5+):** Eine moderne Alternative. Code wird zur **Kompilierzeit** generiert, basierend auf Attributen oder anderen Code-Strukturen. Ergebnis ist normaler, schneller C#-Code, keine Laufzeit-Reflection nötig. Ideal für Serialisierung, DI, INotifyPropertyChanged etc.
    * **Vermeiden:** Reflection nur nutzen, wenn es wirklich nötig ist. Prüfen Sie, ob Design-Patterns wie Interfaces, Basisklassen, generische Programmierung oder Strategie-Pattern eine typsichere und performantere Alternative bieten.

---

### Vertiefung: Reflection Emit (Kurzer Ausblick)

* Namespace: `System.Reflection.Emit`
* Ermöglicht das **dynamische Erzeugen von MSIL** (Microsoft Intermediate Language) Code zur Laufzeit innerhalb des laufenden Prozesses.
* Man kann komplette dynamische Assemblies, Module, Typen (Klassen), Methoden, Eigenschaften etc. "on the fly" generieren.
* **Anwendungsfälle:**
  * Hochleistungs-Serialisierer, die spezialisierten Code für jeden Typ generieren.
  * Proxy-Generatoren (z.B. für ORM-Lazy-Loading oder AOP-Frameworks wie Castle DynamicProxy).
  * Implementierung dynamischer Sprachen auf der .NET Plattform (z.B. IronPython, IronRuby nutzten dies).
  * Erstellung spezialisierter Methoden zur Laufzeit für Performance-Optimierung.
* **Nachteile:**
  * **Extrem komplex:** Erfordert tiefes Verständnis von IL-Code, der CLR-Typstruktur und Speicherverwaltung.
  * Sehr fehleranfällig und extrem schwer zu debuggen, da der generierte Code nicht direkt im Source sichtbar ist (außer mit speziellen Tools wie ILSpy).
  * Wartungsaufwand ist hoch.
* **Alternative:** Oft sind kompilierte `Expression Trees` ausreichend und deutlich einfacher zu handhaben, wenn keine vollständige IL-Kontrolle benötigt wird. `Reflection.Emit` ist das "Schwergewicht" für absolute Low-Level-Kontrolle.

---

### Vertiefung: Expression Trees als Alternative

* Eingeführt mit LINQ (`System.Linq.Expressions`).
* Repräsentieren Code als **Datenstruktur** (einen abstrakten Syntaxbaum - AST). `Expression<Func<T, TResult>>` statt nur `Func<T, TResult>`.
* Können zur Laufzeit analysiert, modifiziert und – das Wichtigste – **kompiliert** werden (`Expression.Compile()`).
* **Vorteile gegenüber reiner Reflection (Invoke/GetValue/SetValue):**
  * **Performance:** Kompilierte Expression Trees erzeugen optimierte Delegaten, deren Ausführung nahezu die Geschwindigkeit von direkt geschriebenem C#-Code erreicht. *Deutlich* schneller als `MethodInfo.Invoke()` oder `PropertyInfo.GetValue/SetValue()`.
  * **Typsicherheit:** Können oft typsicherer konstruiert werden, insbesondere wenn man mit generischen Methoden und Lambdas (`() => obj.Property`) arbeitet, um den Baum zu erstellen.
* **Beispiel:** Dynamischer, aber schneller Property-Getter:

```csharp
using System.Linq.Expressions;
using System.Reflection;

public static class ExpressionUtils
{
    // Erstellt einen schnellen Delegaten Func<TObject, TProperty> zum Lesen einer Property
    public static Func<TObject, TProperty> CreateGetter<TObject, TProperty>(PropertyInfo pi)
    {
        // Parameter für das Objekt (z.B. 'obj') vom Typ TObject
        ParameterExpression instanceParam = Expression.Parameter(typeof(TObject), "obj");

        // Zugriff auf die Property (z.B. obj.PropertyName)
        MemberExpression memberAccess = Expression.Property(instanceParam, pi);

        // Konvertierung, falls TProperty nicht exakt der PropertyType ist (optional, aber oft nötig)
        UnaryExpression castMemberAccess = Expression.Convert(memberAccess, typeof(TProperty));

        // Erstelle Lambda: obj => (TProperty)obj.PropertyName
        Expression<Func<TObject, TProperty>> lambda =
            Expression.Lambda<Func<TObject, TProperty>>(castMemberAccess, instanceParam);

        // Kompiliere den Baum zu einem ausführbaren Delegaten
        return lambda.Compile(); // Dieser Delegat ist schnell!
    }
}

// Verwendung:
// PropertyInfo propInfo = typeof(MyClass).GetProperty("Name");
// Func<MyClass, string> getName = ExpressionUtils.CreateGetter<MyClass, string>(propInfo);
// string name = getName(myObjInstance); // Schneller Aufruf!
```

* Ideal für Szenarien, die dynamisch sein müssen, aber Performance kritisch ist (z.B. Kernlogik in ORMs, Serialisierern, Mapping-Tools).

---

### Vertiefung: Generics und Reflection

* Reflection kann auch mit generischen Typen und Methoden umgehen, sowohl offenen als auch geschlossenen.
* **Generischen Typ untersuchen/instanziieren:**
  * `typeof(List<>)` gibt den **offenen** generischen Typ zurück (`IsGenericTypeDefinition == true`).
  * `typeof(List<int>)` gibt den **geschlossenen** generischen Typ zurück (`IsGenericTypeDefinition == false`).
  * `Type.MakeGenericType(Type...)`: Erstellt einen geschlossenen generischen Typ zur Laufzeit aus einem offenen Typ.

```csharp
// Offenen Typ holen
Type openListType = typeof(List<>);
// Typargumente festlegen (z.B. string)
Type[] typeArgs = { typeof(string) };
// Geschlossenen Typ erstellen (List<string>)
Type closedListType = openListType.MakeGenericType(typeArgs);
// Instanziieren
object listInstance = Activator.CreateInstance(closedListType);
Console.WriteLine($"Instanz von {closedListType} erstellt.");
```

* **Generische Methode untersuchen/aufrufen:**
* `MethodInfo.IsGenericMethodDefinition` prüft, ob es sich um eine offene generische Methode handelt.
* `MethodInfo.MakeGenericMethod(Type...)`: Spezialisiert eine generische Methode mit konkreten Typargumenten.

```csharp
// Angenommen: public T Echo<T>(T item) { return item; } in MyGenericClass
Type myGenClassType = typeof(MyGenericClass);
object instance = Activator.CreateInstance(myGenClassType);

MethodInfo openMethod = myGenClassType.GetMethod("Echo"); // Finde die Methode "Echo"
// Spezialisiere die Methode für <int>
MethodInfo closedMethod = openMethod.MakeGenericMethod(typeof(int));
// Rufe Echo<int>(123) auf
object result = closedMethod.Invoke(instance, new object[] { 123 });
Console.WriteLine($"Echo<int>(123) ergab: {result} (Typ: {result.GetType()})"); // 123 (Typ: System.Int32)
```

* Umgang mit generischen Constraints und komplexeren Szenarien erfordert sorgfältige Prüfung der Metadaten (`GetGenericArguments()`, `GetGenericParameterConstraints()`).

---

### Vertiefung: Sicherheitsaspekte

* Reflection kann standardmäßig auf `public` Member zugreifen.
* Mit den richtigen `BindingFlags` (`NonPublic`) kann Reflection auch `private`, `protected` und `internal` Member untersuchen und darauf zugreifen (`Invoke`, `GetValue`, `SetValue`).
* **Potenzielle Risiken:**
  * **Umgehung von Kapselung:** Das absichtliche Design (Verstecken von Implementierungsdetails, Invarianten durch private Setter sicherstellen) kann untergraben werden.
  * **Zugriff auf sensible interne Zustände:** Interne Daten oder Methoden, die nicht für den externen Gebrauch bestimmt sind, können manipuliert werden, was zu inkonsistenten Objektzuständen führen kann.
  * **Sicherheitslücken:** Wenn eine Anwendung Code aus nicht vertrauenswürdigen Quellen lädt (z.B. Plugins) und diesem erlaubt, uneingeschränkt Reflection zu nutzen, könnte dieser bösartigen Code ausführen oder interne Sicherheitsmechanismen umgehen.
* **Berechtigungen (Permissions):**
  * In traditionellen .NET Framework Szenarien mit Code Access Security (CAS) war `ReflectionPermission` erforderlich, um über öffentliche Member hinauszugehen oder `Reflection.Emit` zu nutzen. CAS ist in .NET Core/.NET 5+ weitgehend veraltet.
  * In modernen .NET-Anwendungen wird Sicherheit eher durch Prozessgrenzen, Containerisierung und Betriebssystem-Berechtigungen gehandhabt. Innerhalb eines vertrauenswürdigen Prozesses gibt es meist keine technischen Hürden für Reflection.
  * **Vorsicht bei `private` Zugriff:** Tun Sie dies nur, wenn es absolut notwendig ist (z.B. für Unit-Testing von Legacy-Code oder tiefgreifende Framework-Integration) und Sie die Konsequenzen verstehen. Dokumentieren Sie solche Zugriffe gut.
* **Alternativen für Assembly-übergreifenden Zugriff:** Das `[InternalsVisibleTo]` Attribut ist eine sauberere, kompilierzeitgeprüfte Methode, um `internal` Member für befreundete Assemblies (z.B. Testprojekte) sichtbar zu machen, ohne Reflection zu benötigen.

---

### Zusammenfassung & Best Practices

* **Zusammenfassung:** Reflection ist ein mächtiges Feature von .NET, das die Introspektion (Untersuchung) und Manipulation von Code-Metadaten zur Laufzeit ermöglicht. Es ist der Schlüssel für viele fortgeschrittene Szenarien wie Plugins, Serialisierung, ORM, DI und dynamische Codegenerierung.
* **Best Practices:**
  * **Nur bei Bedarf:** Nutzen Sie Reflection als letztes Mittel, wenn statische, typsichere Alternativen (Interfaces, Vererbung, Generics, `dynamic`, Source Generators) nicht ausreichen oder unpraktikabel sind.
  * **Performance beachten:** Vermeiden Sie Reflection in Performance-kritischen Pfaden (z.B. in engen Schleifen, bei der Verarbeitung großer Datenmengen).
  * **Caching:** Speichern Sie Reflection-Objekte (`Type`, `MemberInfo`) aggressiv zwischen, wenn sie wiederholt benötigt werden. Der Look-up ist teuer.
  * **Expression Trees für Performance:** Bei wiederholten dynamischen Aufrufen kompilieren Sie `Expression Trees` zu Delegaten, anstatt `Invoke`/`GetValue`/`SetValue` direkt zu verwenden.
  * **Fehlerbehandlung:** Reflection-Code ist anfällig für Laufzeitfehler (`TypeLoadException`, `MissingMethodException`, `TargetInvocationException` etc.). Implementieren Sie robuste Fehlerbehandlung (`try-catch`) und aussagekräftige Fehlermeldungen.
  * **Vermeiden Sie "Magic Strings":** Wenn möglich, verwenden Sie `nameof()` (für Member im gleichen Kontext) oder Konstanten, um die Fehleranfälligkeit von String-basierten Member-Namen zu reduzieren.
  * **Sicherheit:** Seien Sie extrem vorsichtig beim Zugriff auf nicht-öffentliche Member. Verstehen Sie die Implikationen und nutzen Sie es nur, wenn es keine saubere Alternative gibt. Ziehen Sie `[InternalsVisibleTo]` für Test-Szenarien vor.
  * **Source Generators prüfen:** Für viele gängige Anwendungsfälle (Serialisierung, DI-Wiring, INPC) bieten Source Generators in modernen .NET-Versionen eine performantere und typsicherere Alternative zur Laufzeit-Reflection.

---

### Fragen & Antworten

* Jetzt ist Zeit für Ihre Fragen!

* *(Mögliche Diskussionspunkte)*
  * Wann genau sollte ich Reflection statt Interfaces verwenden? *(Antwort: Wenn die Typen zur Kompilierzeit nicht bekannt sind oder ein gemeinsames Interface unpraktisch ist, z.B. Plugin-Systeme)*
  * Wie unterscheidet sich `dynamic` von Reflection? *(Antwort: `dynamic` nutzt intern die DLR mit Caching, vereinfacht die Syntax für späte Bindung, aber die Typüberprüfung findet immer noch zur Laufzeit statt. Reflection gibt mehr Kontrolle über Metadaten.)*
  * Ist Reflection in .NET Core/.NET 5+ anders als im .NET Framework? *(Antwort: Die API ist größtenteils gleich. Performance wurde oft verbessert. CAS ist weg. Einige Details bei Assembly-Ladekontexten können abweichen.)*
  * Wann sind Source Generators besser als Reflection? *(Antwort: Wenn die benötigten Informationen zur Kompilierzeit verfügbar sind. Erzeugt statischen Code, daher schneller und typsicherer zur Laufzeit.)*
