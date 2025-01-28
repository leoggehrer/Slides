---
marp: true
---

# Anleitung: Entity Framework Code First

Hier ist eine Beschreibung der Schritte, um eine Datenbank mit **Entity Framework Code First** zu erstellen, sowohl für **Visual Studio 2022 (VS2022)** als auch für **Visual Studio Code (VSCode)**:

---

## **In Visual Studio 2022**

1. **Projekt erstellen:**
   - Öffne VS2022 und erstelle ein neues Projekt.
   - Wähle `ASP.NET Core Web Application` oder `Console App (.NET)` als Projekttyp.
   - Gib dem Projekt einen Namen und wähle `.NET 6` oder `.NET 7` (oder die gewünschte Version).

2. **Entity Framework Core installieren:**
   - Öffne die **NuGet-Paketverwaltung**:
     - Rechtsklick auf das Projekt im **Solution Explorer** → `NuGet-Pakete verwalten`.
     - Suche nach `Microsoft.EntityFrameworkCore` und installiere es.
     - Optional: Installiere auch weitere Pakete wie:
       - `Microsoft.EntityFrameworkCore.SqlServer` (für SQL Server)
       - `Microsoft.EntityFrameworkCore.Tools` (für Migrationsbefehle)

---

3. **Datenbankkontext und Modelle erstellen:**
   - Erstelle eine neue Klasse für den **DbContext**:

    ```csharp
    using Microsoft.EntityFrameworkCore;

    public class AppDbContext : DbContext
    {
        public DbSet<User> Users { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer("YourConnectionStringHere");
        }
    }

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
    ```

- Ersetze `YourConnectionStringHere` mit der tatsächlichen Verbindungszeichenfolge.

---

4. **Migration erstellen:**
   - Das ausfühbare Programm muss als Start-Projekt festgelegt sein.
   - Öffne die **Package Manager Console** (Tools → NuGet-Paket-Manager → Package Manager Console).
   - Die Auswahl des `Default project` muss das Projekt sein, welches den `DbContext` beinhaltet. 
   - Führe die folgenden Befehle aus:

     ```shell
     Add-Migration InitialCreate
     Update-Database
     ```

   - Der erste Befehl erstellt die Migration (`InitialCreate` ist der Name der Migration).
   - Der zweite Befehl wendet die Migration an und erstellt die Datenbank.

---

1. **Datenbank testen:**
   - Füge einen Eintrag in der Datenbank hinzu, um sicherzustellen, dass alles funktioniert:

     ```csharp
     using (var context = new AppDbContext())
     {
         context.Users.Add(new User { Name = "John Doe", Email = "john.doe@example.com" });
         context.SaveChanges();
     }
     ```

---

## **In Visual Studio Code**

1. **Projekt einrichten:**
   - Öffne das Terminal in VSCode und erstelle ein neues Projekt:

     ```bash
     dotnet new console -n MyApp
     cd MyApp
     ```

   - Installiere die benötigten EF Core-Pakete:

     ```bash
     dotnet add package Microsoft.EntityFrameworkCore
     dotnet add package Microsoft.EntityFrameworkCore.SqlServer
     dotnet add package Microsoft.EntityFrameworkCore.Tools
     ```

---

2. **Datenbankkontext und Modelle erstellen:**
   - Erstelle eine Datei `AppDbContext.cs` und füge folgenden Code hinzu:

     ```csharp
     using Microsoft.EntityFrameworkCore;

     public class AppDbContext : DbContext
     {
         public DbSet<User> Users { get; set; }

         protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
         {
             optionsBuilder.UseSqlServer("YourConnectionStringHere");
         }
     }

     public class User
     {
         public int Id { get; set; }
         public string Name { get; set; }
         public string Email { get; set; }
     }
     ```

---

3. **Migration erstellen:**
   - Führe die folgenden Befehle im Terminal aus:

     ```bash
     dotnet ef migrations add InitialCreate
     dotnet ef database update
     ```

   - Dadurch wird die Datenbank basierend auf dem Modell erstellt.

---

4. **Datenbank testen:**
   - Füge in der `Program.cs` folgenden Code hinzu, um einen Testeintrag zu erstellen:

     ```csharp
     using (var context = new AppDbContext())
     {
         context.Users.Add(new User { Name = "John Doe", Email = "john.doe@example.com" });
         context.SaveChanges();
     }
     ```

---

5. **Erweiterungen für VSCode installieren:**
   - Installiere die Erweiterung **C#** von Microsoft, um IntelliSense und Debugging zu aktivieren.
   - Optional: Installiere **SQL Server**-Erweiterungen, um direkt auf die Datenbank zuzugreifen.

---

Mit diesen Anleitungen kannst du sowohl in **VS2022** als auch in **VSCode** eine Datenbank mit **Entity Framework Code First** erstellen und verwalten.
