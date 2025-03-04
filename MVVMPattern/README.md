---
marp: true
---

# MVVM-Pattern mit Avalonia

---

## Einführung

### Was ist das MVVM-Pattern?

- MVVM steht für **Model-View-ViewModel**
- Trennung der **UI-Logik** von der **Geschäftslogik**
- Verbesserung der Testbarkeit und Wartbarkeit


### Warum MVVM mit Avalonia?

- **Plattformübergreifend** (Windows, Linux, macOS)
- **Moderne UI-Technologie** mit XAML-Unterstützung
- **Datenbindung** und **Komponentenbasierte Entwicklung**

---

## MVVM-Architektur

### Komponenten

- **Model**
  - Repräsentiert die Daten und Geschäftslogik
  - Enthält keine UI-spezifischen Elemente
- **ViewModel**
  - Vermittler zwischen Model und View
  - Implementiert **INotifyPropertyChanged** für Datenbindung
- **View**
  - UI-Definition mit XAML oder Code
  - Bindet sich an das ViewModel (ohne direkte Logik)

---

## Objektmodel

![Objektmodel (OM)](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://github.com/leoggehrer/Slides/blob/main/MVVMPattern/diagrams/mvvm_pattern.puml)

---

## Vorteile und Nachteile des MVVM-Patterns

### Vorteile

- **Klare Trennung von Verantwortlichkeiten**
- **Verbesserte Testbarkeit** durch Unabhängigkeit von UI-Komponenten
- **Wiederverwendbarkeit** von ViewModels
- **Erleichterte Wartung und Erweiterbarkeit**

### Nachteile

- **Erhöhter initialer Aufwand** durch Trennung der Schichten
- **Komplexität** steigt bei größeren Projekten
- **Datenbindung kann fehleranfällig sein**, wenn nicht richtig umgesetzt
- **Nicht alle UI-Frameworks unterstützen MVVM optimal**, erfordert manchmal Workarounds

---

## MVVM mit Avalonia – Beispiel

### Model (Data.cs)

```csharp
public class Data
{
    public string Message { get; set; } = "Hallo, Avalonia!";
}
```

### ViewModel (MainViewModel.cs)

```csharp
using System.ComponentModel;

public class MainViewModel : INotifyPropertyChanged
{
    private string _message;
    
    public event PropertyChangedEventHandler PropertyChanged;
    
    public string Message
    {
        get => _message;
        set
        {
            _message = value;
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Message)));
        }
    }
    
    public MainViewModel()
    {
        Message = "Willkommen bei Avalonia mit MVVM!";
    }
}
```

### View (MainWindow.axaml)

```xml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MVVM mit Avalonia" Width="400" Height="200">
    <StackPanel>
        <TextBlock Text="{Binding Message}" FontSize="20" HorizontalAlignment="Center"/>
    </StackPanel>
</Window>
```

### View-Code-Behind (MainWindow.axaml.cs)

```csharp
using Avalonia.Controls;

public partial class MainWindow : Window
{
    public MainWindow()
    {
        DataContext = new MainViewModel();
        InitializeComponent();
    }
}
```

---

## Fazit

- **MVVM erleichtert die Entwicklung wartbarer und testbarer Anwendungen**
- **Avalonia bietet eine leistungsstarke Plattform für moderne UI-Entwicklung**
- **Datenbindung reduziert Boilerplate-Code und verbessert die Benutzererfahrung**

---

**Fragen? Diskussion?** 😊
