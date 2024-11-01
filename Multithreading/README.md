---
marp: true
---

# Einführung in Multithreading mit C#

Multithreading ist eine Technik, die es ermöglicht, mehrere Aufgaben gleichzeitig innerhalb einer Anwendung auszuführen. Dies wird durch die parallele Nutzung mehrerer Threads ermöglicht, wodurch Anwendungen reaktionsfähiger und effizienter arbeiten können.

---

## Was ist Multithreading?

**Definition:** Multithreading bezeichnet die parallele Ausführung mehrerer Threads in einer Anwendung. Ein Thread ist der kleinste Ausführungskontext in einem Betriebssystem.  
**Vorteile:** Erhöhte Geschwindigkeit, bessere Ressourcenverteilung und verbesserte Benutzerfreundlichkeit.  
**Anwendungsbeispiele:** Spiele, Server, Datenverarbeitung und jede Anwendung mit zeitintensiven Aufgaben.

---

## Warum Multithreading verwenden?

**Problemstellung:** Traditionelle Programme führen Operationen sequenziell aus, was bei rechenintensiven Aufgaben zu Verzögerungen führt.  
**Vorteil der Parallelität:** Zeitgleiches Abarbeiten von Aufgaben, z. B. Berechnungen, Datenzugriff und Netzwerkoperationen.  
**Multithreading-Anwendung:** Ideale Lösung für CPU-intensive Aufgaben und I/O-bound-Prozesse.

---

## Vorteile von Multithreading

**Bessere Reaktionsfähigkeit:** Multithreading ermöglicht es Anwendungen, reaktionsfähiger zu sein, besonders bei Aufgaben, die längere Zeit in Anspruch nehmen. Die Benutzeroberfläche kann weiterhin auf Eingaben reagieren, während im Hintergrund andere Threads laufen.

**Effiziente Nutzung von Ressourcen:** Multithreading kann die Nutzung von CPU-Kernen optimieren, indem mehrere Aufgaben parallel ausgeführt werden. Dies erhöht die Effizienz und verringert die Leerlaufzeiten der CPU.

**Erhöhte Performance bei parallelen Aufgaben:** Multithreading eignet sich besonders gut für Aufgaben, die unabhängig voneinander und parallel ausgeführt werden können, wie z. B. das Rendern von Grafiken, Datenverarbeitung oder das Durchführen mehrerer Abfragen.

---

## Vorteile von Multithreading 2

**Schnellere Verarbeitung von I/O-Operationen:** Threads können asynchrone Ein-/Ausgabe-Aufgaben (z. B. das Laden von Dateien oder das Senden von Netzwerk-Anfragen) parallel bearbeiten, ohne den Hauptprozess zu blockieren.

**Skalierbarkeit:** Auf modernen Mehrkern-Prozessoren kann Multithreading die Leistung erheblich steigern, da Threads parallel auf verschiedenen Kernen laufen können.

**Verbesserte Benutzerfreundlichkeit:** In Anwendungen, die intensive Berechnungen oder Datenzugriffe erfordern, sorgt Multithreading dafür, dass die Hauptoberfläche weiterhin interaktiv bleibt.

Diese **Vorteile** machen Multithreading in C# und anderen Programmiersprachen besonders attraktiv für Anwendungen, die schnelle und parallele Datenverarbeitung erfordern.

---

## Nachteile von Multihreading

**Komplexität:** Die Programmierung mit Multithreading ist komplexer als sequenzielles Programmieren. Entwickler müssen sich mit Synchronisation, Deadlocks, Race Conditions und anderen Problemen des parallelen Programmierens auseinandersetzen.

**Thread-Synchronisation:** Bei gleichzeitigen Zugriffen auf gemeinsame Ressourcen kann es zu Konflikten kommen. Diese zu lösen, erfordert Synchronisationstechniken (z. B. Locks, Mutex), die wiederum die Performance verringern können.

**Ressourcenverbrauch:** Jeder Thread verbraucht Speicher und CPU-Zeit. Zu viele Threads können die Performance verschlechtern und das System überlasten.

---

## Nachteile von Multihreading 2

**Deadlocks und Race Conditions:** Deadlocks entstehen, wenn Threads sich gegenseitig blockieren und aufeinander warten, was die gesamte Anwendung zum Stillstand bringt. Race Conditions können auftreten, wenn mehrere Threads gleichzeitig auf gemeinsame Ressourcen zugreifen und dadurch unerwartete Ergebnisse erzeugen.

**Fehlersuche und Debugging:** Fehler in multithreaded Anwendungen sind oft schwer zu reproduzieren und zu finden, da sie durch spezifische Timing- und Ressourcenbedingungen verursacht werden können.

Diese **Nachteile** zeigen, dass Multithreading zwar leistungsfähig, aber auch risikobehaftet ist und sorgfältige Planung und Debugging erfordert.

---

## Threads in C#: Einführung

**Was ist ein Thread in C#?**  
- Ein Thread repräsentiert eine parallele Ausführungseinheit innerhalb einer Anwendung.  
- C# bietet über die `System.Threading`-Bibliothek umfassende Unterstützung für Threads.

**Grundkomponenten:** `Thread`-Klasse, `Task`-Klasse und `ThreadPool`.

---

## Grundlegende Thread-Erstellung in C#

```csharp
using System;
using System.Threading;

class Program { 
    // Code-Beispiel: Einfache Thread-Erstellung in C#
    static void Main() {
        Thread myThread = new Thread(new ThreadStart(MyMethod));
        myThread.Start();
        Console.WriteLine("Hauptthread läuft weiter...");
    }

    static void MyMethod() {
        Console.WriteLine("Zusätzlicher Thread läuft!");
    }
}
```

---

## Grundlegende Thread-Erstellung in C# 2

**Erklärung des Codes:**

- Erstellen eines neuen Threads durch Instanziierung der `Thread`-Klasse.  
- Starten des Threads mit `.Start()` und paralleles Ausführen der Methode `MyMethod`.

---

## Arbeiten mit Tasks für parallele Ausführung

**Tasks vs. Threads:** Tasks sind eine höhere Abstraktion und eignen sich gut für komplexere, parallele Workflows.  

```csharp
using System;
using System.Threading.Tasks;

class Program { 
    // Code-Beispiel: Verwendung von Tasks
    static async Task Main() {
        Task task = Task.Run(() => DoWork());
        Console.WriteLine("Doing something in main...");
        await task;
    }

    static void DoWork() {
        Console.WriteLine("Doing work in a task.");
    }
}
```

---

## Arbeiten mit Tasks für parallele Ausführung 2

**Erklärung des Codes:**

- Der Task wird parallel zur Hauptmethode ausgeführt. `await` sorgt dafür, dass der Hauptthread auf das Ende des Tasks wartet, bevor er fortfährt.

---

## Synchronisation und Thread-Sicherheit

**Problem:** Gleichzeitiger Zugriff auf Ressourcen kann zu Konflikten und unvorhersehbarem Verhalten führen.  

**Lösung:** Verwendung von Synchronisationstechniken wie `lock`, `Monitor`, `Mutex`, `Semaphore`.  

---

## Synchronisation und Thread-Sicherheit - Beispiel

```csharp
using System;
using System.Threading;

class Program { 
    // Code-Beispiel: `lock`-Anweisung
    static readonly object locker = new object();

    static void Main() {
        Thread thread1 = new Thread(SafeMethod);
        Thread thread2 = new Thread(SafeMethod);
        thread1.Start();
        thread2.Start();
    }
    static void SafeMethod() {
        lock(locker) {
            Console.WriteLine("Thread-sicherer Zugriff auf Ressource");
        }
    }
}
```

---

## Synchronisation und Thread-Sicherheit - Beispiel 2

**Erklärung des Codes:**

- `lock` stellt sicher, dass **nur** ein Thread den `lock`-Block in der `SafeMethod`-Methode ausführen kann. Das bedeuted, dass alle kritischen Operationen im `lock`-Block enthalten sind.

---

## Beispiel: Berechnung von Fibonacci-Zahlen mit Threads

```csharp
using System;
using System.Threading;

class Program {
    static void Main() {
        for (int i = 1; i <= 5; i++) {
            int num = i;
            Thread thread = new Thread(() => PrintFibonacci(num));
            thread.Start();
        }
    }
    static void PrintFibonacci(int n) {
        int a = 0, b = 1;
        for (int i = 0; i < n; i++) {
            int temp = a;
            a = b;
            b = temp + b;
        }
        Console.WriteLine($"Fibonacci({n}) = {a}");
    }
}
```

---

## Beispiel: Berechnung von Fibonacci-Zahlen mit Threads 2

**Erklärung des Codes:** 

- Jeder Thread berechnet die Fibonacci-Zahl unabhängig, und das Ergebnis wird parallel ausgegeben.

---

## Fortgeschrittene Synchronisationsmechanismen: Semaphore und Mutex

**Semaphore:** Kontrolliert mehrere Threads, die eine Ressource gleichzeitig nutzen dürfen.  
**Mutex:** Exklusiver Zugriff auf eine Ressource; gut geeignet für systemübergreifende Synchronisation.  

---

## Kurzes Code-Beispiel für `SemaphoreSlim` in C#

```csharp
using System;
using System.Threading;

class Program {
    static SemaphoreSlim semaphore = new SemaphoreSlim(2);  // Max. 2 Threads gleichzeitig

    static void Main() {
        for (int i = 0; i < 5; i++) {
            int num = i;
            Thread thread = new Thread(() => AccessResource(num));
            thread.Start();
        }
    }

    static void AccessResource(int id) {
        semaphore.Wait();
        Console.WriteLine($"Thread {id} hat Zugriff auf die Ressource");
        Thread.Sleep(2000); // Simuliert Arbeit
        Console.WriteLine($"Thread {id} gibt die Ressource frei");
        semaphore.Release();
    }
}
```

---

## Kurzes Code-Beispiel für `SemaphoreSlim` in C# 2

**Erklärung:** `SemaphoreSlim` limitiert die Anzahl der gleichzeitig aktiven Threads.

---

## Zusammenfassung und Best Practices

**Best Practices:**  
- **Vermeide Overhead:** Nur Multithreading verwenden, wenn nötig.  
- **Thread-Sicherheit sicherstellen:** Immer Synchronisationsmechanismen nutzen.  
- **Bevorzugung von Tasks:** Für komplexe und dynamische Workflows.

**Zusammenfassung:** Multithreading ist eine leistungsstarke Technik in C#, die jedoch Sorgfalt und Synchronisation erfordert.


