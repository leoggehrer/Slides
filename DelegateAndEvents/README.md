---
marp: true
theme: default
paginate: false
---

# Delegates und Events in C#

- **Delegates**: Delegierte Methodenzeiger in C#.
- **Events**: Ereignisbasierte Programmierung mit Delegaten.

---

# Delegate

Weitere Informationen zum Thema Delegates finden Sie im​ C# Programmierhandbuch von Microsoft​
​
[C# Programmierhandbuch/Delegate](https://learn.microsoft.com/de-de/dotnet/csharp/programming-guide/delegates/)

**Was ist ein Delegete?**

Ein **Delegate** ist ein Typ, der eine Methode mit einer bestimmten Signatur repräsentiert.

```csharp
public delegate void MyDelegate(string message);
```

- Ein Delegate ist ein Objekt, das auf eine Methode verweist.
- Ein Delegate kann eine oder mehrere Methoden aufrufen.

---

## Delegate Beispiel

```csharp
public delegate void MyDelegate(string message);

public class Program
{
    public static void Main()
    {
        MyDelegate del = new MyDelegate(ShowMessage);
        del("Hello from Delegate!");
    }

    public static void ShowMessage(string message)
    {
        Console.WriteLine(message);
    }
}
```

Ausgabe:

`Hello from Delegate`

---

## Multicast Delegates

Ein Delegate kann mehrere Methoden aufrufen:

```csharp
public delegate void MyDelegate(string message);

public class Program
{
    public static void Main()
    {
        MyDelegate del = ShowMessage;
        del += ShowAnotherMessage;

        del("Hello from Multicast Delegate!");
    }
    public static void ShowMessage(string message)
    {
        Console.WriteLine("Message 1: " + message);
    }
    public static void ShowAnotherMessage(string message)
    {
        Console.WriteLine("Message 2: " + message);
    }
}
```

---

## Multicast Delegates II

Ausgabe:

```csharp
Message 1: Hello from Multicast Delegate!
Message 2: Hello from Multicast Delegate!
```

---

## Events in C#

Ein Event ist eine spezielle Art von Delegate, das von einer Klasse oder Struktur verwendet wird, um Ereignisse zu signalisieren.

- Ein Event kann nur innerhalb der Klasse, die es definiert, ausgelöst werden.
- Andere Klassen können sich auf das Event abonnieren.

Weitere Informationen zum Thema Events finden Sie im​ C# Programmierhandbuch von Microsoft​
​
[C# Programmierhandbuch/Events](https://learn.microsoft.com/de-de/dotnet/csharp/programming-guide/events/)

---

## Event Beispiel

```csharp
public class Button
{
    public event EventHandler ButtonClicked;

    public void Click()
    {
        // Event auslösen
        ButtonClicked?.Invoke(this, EventArgs.Empty);
    }
}
public class Program
{
    public static void Main()
    {
        Button button = new Button();
        button.ButtonClicked += ButtonClickedHandler;
        button.Click();
    }

    public static void ButtonClickedHandler(object sender, EventArgs e)
    {
        Console.WriteLine("Button was clicked!");
    }
}
```
Ausgabe: `Button was clicked`

---

## Event mit Parametern

Events können auch Parameter übergeben, die zusätzliche Informationen liefern.

```csharp
public class Button 
{
    public event EventHandler<ButtonEventArgs> ButtonClicked;
    public void Click(string message) 
    {
        // Event auslösen mit Parameter
        ButtonClicked?.Invoke(this, new ButtonEventArgs(message));
    }
}

public class ButtonEventArgs : EventArgs 
{
    public string Message { get; }
    public ButtonEventArgs(string message) 
    {
        Message = message;
    }
}
```

---

## Event mit Parametern II

```csharp
public class Program {
    public static void Main() {
        Button button = new Button();
        button.ButtonClicked += ButtonClickedHandler;
        button.Click("Hello World");
    }
    public static void ButtonClickedHandler(object sender, ButtonEventArgs e) {
        Console.WriteLine($"Button clicked with message: {e.Message}");
    }
}
```
Ausgabe: `Button clicked with message: Hello World`

---

## Entfernen von Event-Handlern

Ein Event-Handler kann mit -= von einem Event entfernt werden:

```csharp
button.ButtonClicked -= ButtonClickedHandler;
```

---

## Zusammenfassung

- Delegates ermöglichen das dynamische Binden von Methoden.
- Multicast-Delegates rufen mehrere Methoden gleichzeitig auf.
- Events sind Delegates, die Ereignisse signalisieren und den Empfang von Benachrichtigungen ermöglichen.
- Delegates und Events sind wichtig für die ereignisgesteuerte Programmierung in C#.
  