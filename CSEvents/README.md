---
marp: true
theme: default
paginate: false
---

# Events in C\#

## 1. Einführung

Events sind ein zentrales Konzept in C#, das es ermöglicht, eine lose Kopplung zwischen Komponenten herzustellen. Sie basieren auf Delegates und ermöglichen es einem Objekt, andere Objekte zu benachrichtigen, wenn ein bestimmtes Ereignis eintritt.

---

## 2. Delegates als Grundlage

### Was sind Delegates?

Ein **Delegate** ist ein Typ, der eine Methode mit einer bestimmten Signatur referenziert. Delegates sind notwendig, um Events zu implementieren.

### Beispiel für einen Delegate:

```csharp
using System;

// Definition eines Delegates
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

## 3. Multicast-Delegates

Ein Delegate kann mehrere Methoden referenzieren. Dies ist die Grundlage für Events.

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

## 4. Delegate vs. Event

### Unterschiede zwischen Delegates und Events

| Feature                       | Delegate       | Event                                                          |
| ----------------------------- | -------------- | -------------------------------------------------------------- |
| Direkt aufrufbar?             | Ja             | Nein (nur innerhalb der Klasse)                                |
| Multicast-Unterstützung?      | Ja             | Ja                                                             |
| Externe Manipulation möglich? | Ja             | Nein (nur `+=` und `-=` erlaubt)                               |
| Sicherheit                    | Weniger sicher | Sicherer, da nur eingeschränkte Zugriffsmöglichkeiten bestehen |

### Beispiel zur Verdeutlichung:

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

        // Delegate kann direkt zugewiesen und aufgerufen werden
        example.DelegateInstance = () => Console.WriteLine("Delegate aufgerufen");
        example.DelegateInstance();

        // Event kann nur mit += oder -= verändert werden
        example.EventInstance += () => Console.WriteLine("Event aufgerufen");
        example.EventInstance?.Invoke();
    }
}
```

---

## 5. Events in C\#

### Was sind Events?

Ein **Event** ist eine spezielle Form eines Delegates, die von einer Klasse definiert wird und von anderen Klassen abonniert werden kann. Events sorgen für eine lose Kopplung zwischen Event-Quelle und Event-Hörer.

### Beispiel für ein Event:

```csharp
using System;

// Definition eines Delegates
public delegate void Notify();

class EventPublisher
{
    // Definition eines Events basierend auf dem Delegate
    public event Notify OnChange;

    public void TriggerEvent()
    {
        OnChange?.Invoke();
    }
}

class Program
{
    static void Main()
    {
        EventPublisher publisher = new EventPublisher();
        publisher.OnChange += EventHandler;

        publisher.TriggerEvent(); // Ruft den Event-Handler auf
    }

    static void EventHandler()
    {
        Console.WriteLine("Event wurde ausgelöst!");
    }
}
```

---

## 6. EventHandler und EventArgs

Um eine standardisierte Event-Implementierung zu nutzen, verwendet C# die `EventHandler`-Delegates mit `EventArgs`.

### Beispiel mit `EventHandler`:

```csharp
using System;

class EventPublisher
{
    public event EventHandler<EventArgs> OnChange;

    public void TriggerEvent()
    {
        OnChange?.Invoke(this, EventArgs.Empty);
    }
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

## 7. Delegate vs. Event (Zusammenfassung)

- **Delegates** sind flexibel und ermöglichen eine direkte Zuweisung von Methoden.
- **Events** sind sicherer, da sie nur durch `+=` und `-=` verändert werden können.
- **Events** können nur vom Besitzer ausgeführt werden.
- **Delegates können direkt aufgerufen werden**, während Events nur von innerhalb der Klasse ausgelöst werden können.
- **Events** fördern eine **lose Kopplung** zwischen Komponenten.

---

## 8. Fazit

- **Delegates** sind die Grundlage für Events.
- **Multicast-Delegates** ermöglichen das Verketten mehrerer Methoden.
- **Events** ermögichen eine lose Kopplung zwischen Publisher und Subscriber.
- **Events sind sicherer als Delegates**, da sie nur mit `+=` oder `-=` verändert werden können.
- **EventHandler und EventArgs** sind empfohlene Standards für Events in C#.

Events sind ein leistungsstarkes Konzept, das in vielen .NET-Anwendungen verwendet wird, insbesondere in GUI-Anwendungen (z. B. WPF, WinForms) oder in asynchronen Operationen.

---
