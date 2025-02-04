---
marp: true
---

# Einführung in die AppSettings in .NET

In modernen .NET-Anwendungen werden Konfigurationswerte üblicherweise in einer `appsettings.json`-Datei gespeichert. Diese Methode ersetzt weitgehend die frühere `web.config` oder `app.config`-Datei und bietet eine flexible, strukturierte und leicht lesbare Möglichkeit zur Konfiguration von Anwendungen.

---

## Struktur der `appsettings.json`

Die `appsettings.json`-Datei verwendet das JSON-Format und kann hierarchische Daten enthalten. Eine typische Datei könnte wie folgt aussehen:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning"
    }
  },
  "ConnectionStrings": {
    "SqlServerDefaultConnection": "Data Source=127.0.0.1,1433; Database=DefaultDb;User Id=sa; Password=...;TrustServerCertificate=true"
  },
  "ApplicationSettings": {
    "ApiKey": "12345-abcde",
    "MaxItems": 100
  }
}
```

---

## Konfiguration für verschiedene Umgebungen

In .NET kann die Anwendung unterschiedliche Konfigurationsdateien für verschiedene Umgebungen verwenden, z. B. für Entwicklung (`Development`) und Produktion (`Production`). Dies geschieht durch zusätzliche Dateien wie `appsettings.Development.json` und `appsettings.Production.json`.

Beispiel für `appsettings.Development.json`:

```json
{
  "ConnectionStrings": {
    "SqlServerDefaultConnection": "Data Source=DevelopmentServer; Database=DevelopmentDb;User Id=sa; Password=...;TrustServerCertificate=true"
  },
  "ApplicationSettings": {
    "ApiKey": "dev-12345",
    "MaxItems": 50
  }
}
```

---

Beispiel für `appsettings.Production.json`:

```json
{
  "ConnectionStrings": {
    "SqlServerDefaultConnection": "Data Source=ProductionServer; Database=ProductionDb;User Id=sa; Password=...;TrustServerCertificate=true"
  },
  "ApplicationSettings": {
    "ApiKey": "prod-67890",
    "MaxItems": 200
  }
}
```

---

### Automatische Erkennung der Umgebung

.NET erkennt die Umgebung anhand der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT`. Standardwerte sind `Development`, `Staging` und `Production`.

Der Zugriff auf die aktuelle Umgebung erfolgt über:

```csharp
var environment = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
Console.WriteLine($"Aktuelle Umgebung: {environment}");
```

---

### Laden der passenden Konfigurationsdatei

Die `WebApplicationBuilder`-Klasse lädt automatisch die korrekte Konfigurationsdatei basierend auf der Umgebung:

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Configuration
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
    .AddJsonFile($"appsettings.{builder.Environment.EnvironmentName}.json", optional: true)
    .AddEnvironmentVariables();
```

---

## Zugriff auf AppSettings in C#

### 1. Nutzung von Options Pattern

Eine strukturierte Möglichkeit, mit Konfigurationen zu arbeiten, bietet das **Options Pattern** mit stark typisierten Klassen.

1. Erstellen einer Konfigurationsklasse:

```csharp
public class ApplicationSettings
{
    public string ApiKey { get; set; }
    public int MaxItems { get; set; }
}
```

---

2. Registrieren der Konfiguration in `Program.cs`:

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;

var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<ApplicationSettings>(builder.Configuration.GetSection("ApplicationSettings"));
var app = builder.Build();
```

---

3. Zugriff über Dependency Injection (DI):

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Options;

[ApiController]
[Route("api/settings")]
public class SettingsController : ControllerBase
{
    private readonly ApplicationSettings _settings;

    public SettingsController(IOptions<ApplicationSettings> options)
    {
        _settings = options.Value;
    }

    [HttpGet]
    public IActionResult GetSettings()
    {
        return Ok(_settings);
    }
}
```

---

### Zugriff auf ConnectionStrings

Datenbankverbindungszeichenfolgen können direkt über `IConfiguration` abgerufen werden:

```csharp
string connectionString = configuration.GetConnectionString("SqlServerDefaultConnection");
Console.WriteLine($"Connection String: {connectionString}");
```

---

## Nutzung von Umgebungsvariablen

Zusätzlich zur `appsettings.json` können Konfigurationswerte über Umgebungsvariablen gesteuert werden. Diese haben eine höhere Priorität als Werte aus der JSON-Datei und können beispielsweise im Betriebssystem oder in Docker-Containern gesetzt werden.

Ein Beispiel für das Setzen einer Umgebungsvariablen unter Windows (PowerShell):

```powershell
$env:ApplicationSettings__ApiKey="env-override-key"
```

Unter Linux/macOS (Bash):

```bash
export ApplicationSettings__ApiKey="env-override-key"
```

---

## Nutzung von Umgebungsvariablen II

.NET verwendet das `IConfiguration`-Interface, um Umgebungsvariablen zu berücksichtigen:

```csharp
builder.Configuration.AddEnvironmentVariables();
```

---

## Fazit

Die `appsettings.json`-Datei bietet eine leistungsfähige und flexible Möglichkeit zur Konfigurationsverwaltung in .NET-Anwendungen. Durch **Umgebungsabhängige Konfigurationsdateien** (`appsettings.Development.json`, `appsettings.Production.json`) und **Umgebungsvariablen** kann sichergestellt werden, dass die Anwendung je nach Umgebung die passenden Einstellungen verwendet. Das **Options Pattern** ermöglicht eine typsichere und strukturierte Nutzung der Konfigurationen, während **Dependency Injection** diese effizient in der gesamten Anwendung bereitstellt.

---

Durch den Einsatz dieser Methoden wird die Verwaltung von App-Einstellungen deutlich vereinfacht und verbessert die Wartbarkeit und Skalierbarkeit der Anwendung.
