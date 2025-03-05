---
marp: true
---

# Logging mit dem Entity Framework Core (EF Core)

Beim Entity Framework können die SQL-Statements protokollieren (Logging aktivieren), um die generierten SQL-Befehle zu überwachen. Dies ist hilfreich für Debugging und Performance-Optimierung. Die Vorgehensweise hängt davon ab, ob **Entity Framework Core** (EF Core) oder das klassische **Entity Framework (EF6)** verwendet wird. Im Unterricht verwenden wir ausschließlich die **DotNet Core** Versionen.

---

## **Logging in Entity Framework Core (EF Core)**

In **EF Core** können die integrierte Logging-Feature genutzen werden.

---

### Methode 1: Logging in der `DbContext`-Konfiguration

Das Logging kann direkt in der `DbContext`-Klasse konfigurieren werden:

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Logging;

public class MyDbContext : DbContext
{
    public static readonly ILoggerFactory MyLoggerFactory = LoggerFactory.Create(builder =>
    {
        builder.AddConsole(); // Ausgabe der Logs in die Konsole
    });

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder
            .UseLoggerFactory(MyLoggerFactory)  // Logger aktivieren
            .EnableSensitiveDataLogging()       // Zeigt auch Parameterwerte an (Vorsicht mit sensiblen Daten!)
            .EnableDetailedErrors();            // Zeigt detaillierte Fehlermeldungen
    }

    public DbSet<User> Users { get; set; }
}
```

---

**Wichtig:**

- `EnableSensitiveDataLogging()` zeigt Parameterwerte in SQL-Logs, aber es kann ein Sicherheitsrisiko darstellen.
- `EnableDetailedErrors()` gibt detaillierte Fehlermehldungen in die Konsole aus.

---

### Methode 2: Logging mit Dependency Injection (z.B. in ASP.NET Core)

In einer ASP.NET Core Anwendung kann das Logging über `ILogger<MyDbContext>` genutzt werden:

```csharp
services.AddDbContext<MyDbContext>(options =>
    options.UseSqlServer(connectionString)
           .LogTo(Console.WriteLine, LogLevel.Information) // SQL-Statements in die Konsole schreiben
           .EnableSensitiveDataLogging()
);
```

---

## **Logging der SQL-Statements in eine Logging-Datei**

Falls du die Logs in eine Datei schreiben möchtest, kannst du `LogTo()` verwenden:

### **EF Core:**

```csharp
services.AddDbContext<MyDbContext>(options =>
    options.UseSqlServer(connectionString)
           .LogTo(msg => File.AppendAllText("ef-log.txt", msg + Environment.NewLine), LogLevel.Information)
           .EnableSensitiveDataLogging()
);
```

---

**Fazit:**

- **EF Core:** Nutze `.UseLoggerFactory()`, `LogTo()` oder `EnableSensitiveDataLogging()`
- **EF6:** Verwende `Database.Log`
- **Logging kann entweder in die Konsole oder eine Datei erfolgen**
- **Achte bei `EnableSensitiveDataLogging()` darauf, keine sensiblen Daten zu protokollieren**
