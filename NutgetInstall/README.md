---
marp: true
---

# NuGet-Pakete in C# installieren

Um NuGet-Pakete in einem C#-Projekt zu installieren, kannst du verschiedene Ansätze verwenden, abhängig von deinem Entwicklungswerkzeug. Hier sind die häufigsten Methoden:

---

## 1. Installation über die NuGet-Paket-Manager-Oberfläche (Visual Studio)

1. **Projekt öffnen**: Öffne dein Projekt in Visual Studio.
2. **NuGet-Paket-Manager öffnen**:
   - Rechtsklick auf das Projekt im **Solution Explorer**.
   - Wähle **Manage NuGet Packages...** (NuGet-Pakete verwalten).
3. **Paket suchen**:
   - Wechsel zur Registerkarte **Browse**.
   - Suche nach dem gewünschten Paket, z. B. `Newtonsoft.Json`.
4. **Paket installieren**:
   - Wähle das Paket aus der Liste.
   - Klicke auf **Install**.
5. **Bestätigen**: Akzeptiere die Lizenzbedingungen und schließe die Installation ab.

---

## 2. Installation über die NuGet-Konsole (Visual Studio)

1. **NuGet-Paket-Manager-Konsole öffnen**:
   - Gehe zu **Tools** → **NuGet Package Manager** → **Package Manager Console**.
2. **Befehl ausführen**:

   ```powershell
   Install-Package Paketname
   ```

   Beispiel:

   ```powershell
   Install-Package Newtonsoft.Json
   ```

3. Die Konsole zeigt den Fortschritt und bestätigt die Installation.

---

## 3. Installation über die .NET-CLI

Falls du mit der .NET-CLI (Command Line Interface) arbeitest:

1. **Befehl ausführen**:

   ```bash
   dotnet add package Paketname
   ```

   Beispiel:

   ```bash
   dotnet add package Newtonsoft.Json
   ```

2. Die CLI aktualisiert die **.csproj-Datei** automatisch mit der Paketreferenz.

---

## 4. Direkte Bearbeitung der `.csproj`-Datei

1. **Projektdatei öffnen**:
   - Öffne die `.csproj`-Datei deines Projekts in einem Texteditor.
2. **Paketreferenz hinzufügen**:

   ```xml
   <ItemGroup>
       <PackageReference Include="Newtonsoft.Json" Version="13.0.1" />
   </ItemGroup>
   ```

3. **Projekt neu laden**:
   - Speichere die Datei und lade das Projekt in Visual Studio neu.
4. **Paket wiederherstellen**:
   - Führe in der NuGet-Konsole oder der CLI den folgenden Befehl aus:

     ```bash
     dotnet restore
     ```

---

## 5. Installation über Visual Studio Code mit .NET-CLI

1. **Visual Studio Code öffnen**: Starte Visual Studio Code und öffne dein Projektverzeichnis.
2. **Terminal öffnen**:
   - Öffne das integrierte Terminal in VS Code (Tastenkombination: `Strg + ``).
3. **Paket installieren**:
   - Verwende den folgenden Befehl:

     ```bash
     dotnet add package Paketname
     ```

     Beispiel:

     ```bash
     dotnet add package Newtonsoft.Json
     ```

4. **Wiederherstellen**:
   - Stelle sicher, dass alle Abhängigkeiten mit folgendem Befehl wiederhergestellt werden:

     ```bash
     dotnet restore
     ```

---

## 6. Installation mit der NuGet Package Manager-Erweiterung in Visual Studio Code

1. **Erweiterung installieren:**
   - Öffne den Extensions Marketplace in VS Code (Tastenkombination: Strg + Umschalt + X).
   - Suche nach NuGet Package Manager und klicke auf Install.

2. **NuGet-Paket verwalten:**
   - Öffne die Command Palette (Strg + Umschalt + P) und wähle NuGet: Add Package.
   - Gib den Namen des Pakets ein (z. B. Newtonsoft.Json) und wähle es aus der Liste aus.

**Weitere Funktionen:**

Aktualisiere oder entferne Pakete direkt über die Benutzeroberfläche.

---

## Tipps

- Stelle sicher, dass du die richtige Version des Pakets für dein Projekt auswählst. Informationen dazu findest du normalerweise auf [NuGet.org](https://www.nuget.org).
- Prüfe nach der Installation, ob du das Paket erfolgreich referenzieren kannst, indem du den Namespace im Code importierst:

  ```csharp
  using Newtonsoft.Json;
  ```

Mit diesen Methoden kannst du NuGet-Pakete flexibel installieren und verwalten.
