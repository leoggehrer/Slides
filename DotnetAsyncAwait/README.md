---
marp: true
---

# Async und Await in C#

[C# Programmierhandbuch/AsyncAwait](https://learn.microsoft.com/de-de/dotnet/csharp/asynchronous-programming/)

---

## Einführung

**Agenda:**  

- Warum Asynchronität?  
- Grundlagen von `async` und `await`  
- Beispiele  
- Best Practices  
- Fazit  

---

## Warum Asynchronität?

- Erhöht die Performance von Anwendungen
- Verhindert Blockierungen im UI-Thread
- Skalierbarkeit in Serveranwendungen
- Nutzt Ressourcen effizienter

---

**Beispiel:**
Synchroner vs. asynchroner Code:

```csharp
// Synchroner Code (Blockierend)
Thread.Sleep(5000);
Console.WriteLine("Fertig");

// Asynchroner Code (Nicht blockierend)
await Task.Delay(5000);
Console.WriteLine("Fertig");
```

---

## Grundlagen von `async` und `await`

- `async` markiert eine Methode als asynchron.
- `await` wartet auf das Ergebnis einer asynchronen Methode.
- Rückgabewerte:
  - `Task` für Methoden ohne Rückgabewert.
  - `Task<T>` für Methoden mit Rückgabewert.

**Beispiel:**

```csharp
public async Task<int> LadeDatenAsync()
{
    await Task.Delay(2000); // Simuliert eine Verzögerung
    return 42;
}
```

---

## Beispiel – Webanfrage mit `HttpClient`

```csharp
public async Task<string> HoleDatenAsync()
{
    using HttpClient client = new HttpClient();
    string daten = await client.GetStringAsync("https://example.com");
    return daten;
}
```

- `GetStringAsync` ist eine asynchrone Methode.
- `await` stellt sicher, dass das Programm erst nach Abschluss fortfährt.

---

## Fehlerbehandlung

- Fehler werden mit `try-catch` abgefangen.

```csharp
public async Task LadeDateiAsync()
{
    try
    {
        string inhalt = await File.ReadAllTextAsync("datei.txt");
        Console.WriteLine(inhalt);
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Fehler: {ex.Message}");
    }
}
```

---

## Best Practices

✅ Vermeide `async void`, außer für Event-Handler.  
✅ Nutze `ConfigureAwait(false)` in Bibliotheken für bessere Performance.  
✅ Vermeide `Task.Result` oder `Task.Wait()` (Deadlocks möglich).  
✅ Ketten mehrere `await`-Aufrufe, anstatt `Task.Run()` zu nutzen.  

---

## Fazit

- `async` und `await` ermöglichen nicht blockierende Codeausführung.
- Richtige Anwendung verbessert Performance und Responsiveness.
- Fehlerbehandlung und Best Practices sind essenziell für sauberen Code.
