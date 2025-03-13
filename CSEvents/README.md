---
marp: true
theme: default
paginate: false
---

# Events in C\#

## Einführung

Events sind ein zentrales Konzept in C#, das es ermöglicht, eine lose Kopplung zwischen Komponenten herzustellen.

---

### Analogie aus dem echten Leben

Ein Event in der realen Welt ist eine **Türklingel**: Wenn jemand die Klingel drückt (**Event-Trigger**), wird der Bewohner benachrichtigt (**Event-Handler**) und kann reagieren.

---

## Delegates als Grundlage

### Was sind Delegates?

Ein **Delegate** ist ein Typ, der eine Methode mit einer bestimmten Signatur referenziert.

---

### Signatur eines Delegates

- **Rückgabewert**: Der Typ, den die Methode zurückgibt (z. B. `void`).
- **Parameterliste**: Die Anzahl und Typen der erwarteten Parameter.

```csharp
public delegate void MessageDelegate(string message);
```

---

### Beispiel für einen Delegate:

```csharp
using System;
public delegate void MessageDelegate(string message);
class Program
{
    static void Main()
    {
        MessageDelegate msgDelegate = PrintMessage;
        msgDelegate("Hallo, Delegates!");
    }
    static void PrintMessage(string msg)
    {
        Console.WriteLine(msg);
    }
}
```

---

## Multicast-Delegates

Ein Delegate kann mehrere Methoden referenzieren und diese ausführen.

---

### Beispiel für Multicast-Delegates:

```csharp
using System;
public delegate void MultiDelegate();
class Program
{
    static void Main()
    {
        MultiDelegate multiDelegate = MethodOne;
        multiDelegate += MethodTwo;
        multiDelegate(); // Beide Methoden werden aufgerufen
    }
    static void MethodOne() => Console.WriteLine("Methode Eins ausgeführt");
    static void MethodTwo() => Console.WriteLine("Methode Zwei ausgeführt");
}
```

---

## Events in C\#

### Was sind Events?

Ein **Event** ist eine spezielle Form eines Delegates, die von einer Klasse definiert wird und von anderen Klassen abonniert werden kann.

---

### Beispiel für ein Event:

```csharp
using System;
public delegate void Notify();
class EventPublisher
{
    public event Notify OnChange;
    public void TriggerEvent() => OnChange?.Invoke();
}
class Program
{
    static void Main()
    {
        EventPublisher publisher = new EventPublisher();
        publisher.OnChange += EventHandler;
        publisher.TriggerEvent();
    }
    static void EventHandler() => Console.WriteLine("Event wurde ausgelöst!");
}
```

---

## EventHandler und EventArgs

C# verwendet `EventHandler`-Delegates mit `EventArgs` für standardisierte Events.

---

### Beispiel mit `EventHandler`:

```csharp
using System;
class EventPublisher
{
    public event EventHandler<EventArgs> OnChange;
    public void TriggerEvent() => OnChange?.Invoke(this, EventArgs.Empty);
}
class Program
{
    static void Main()
    {
        EventPublisher publisher = new EventPublisher();
        publisher.OnChange += EventHandlerMethod;
        publisher.TriggerEvent();
    }
    static void EventHandlerMethod(object sender, EventArgs e)
    {
        Console.WriteLine("Standard-Event wurde ausgelöst!");
    }
}
```

---

## Delegate vs. Event

- **Delegates** sind flexibel und ermöglichen eine direkte Zuweisung von Methoden.
- **Events** sind sicherer, da sie nur durch \`+=\` und \`-=\` verändert werden können.
- **Events** können nur vom Besitzer ausgeführt werden.
- **Delegates** können direkt aufgerufen werden\*\*, während Events nur von innerhalb der Klasse ausgelöst werden können.
- **Events** fördern eine **lose Kopplung** zwischen Komponenten.

---

### Übersichtstabelle

| Feature               | Delegate       | Event                           |
| --------------------- | -------------- | ------------------------------- |
| Direkt aufrufbar?     | Ja             | Nein (nur innerhalb der Klasse) |
| Multicast?            | Ja             | Ja                              |
| Externe Manipulation? | Ja             | Nein (`+=`, `-=` erlaubt)       |
| Sicherheit            | Weniger sicher | Sicherer                        |

---

### Beispiel:

```csharp
using System;
public delegate void Notify();
class Example
{
    public Notify DelegateInstance;
    public event Notify EventInstance;
}
class Program
{
    static void Main()
    {
        Example example = new Example();
        example.DelegateInstance = () => Console.WriteLine("Delegate aufgerufen");
        example.DelegateInstance();
        example.EventInstance += () => Console.WriteLine("Event aufgerufen");
        example.EventInstance?.Invoke();
    }
}
```

---

## Fazit

- **Delegates** sind die Grundlage für Events.
- **Multicast-Delegates** ermöglichen das Verketten mehrerer Methoden.
- **Events** fördern lose Kopplung zwischen Komponenten.
- **EventHandler und EventArgs** sind empfohlene Standards.
- **Events sind sicherer als Delegates**.

Events sind ein leistungsstarkes Konzept, das in vielen .NET-Anwendungen verwendet wird.

