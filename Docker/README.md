---
marp: true
---
# Einführung in Docker

Docker ist eine Open-Source-Plattform zur Containerisierung von Anwendungen. Sie ermöglicht es Entwicklern, Software mit allen benötigten Abhängigkeiten in Containern zu verpacken, sodass sie unabhängig von der Umgebung konsistent ausgeführt werden können.

---

## Warum Docker?

- **Plattformunabhängigkeit:** Anwendungen laufen überall gleich (lokal, in der Cloud, auf Servern).

- **Leichtgewichtige Container:** Im Vergleich zu virtuellen Maschinen benötigen Docker-Container weniger Ressourcen.

- **Schnelles Deployment:** Anwendungen können schnell gestartet, gestoppt und skaliert werden.
- **Einfache Verwaltung:** Mit Docker Compose können mehrere Container-Anwendungen orchestriert werden.

---

## Wichtige Begriffe

- **Image:** Eine Vorlage, die alle benötigten Dateien für eine Anwendung enthält.
- **Container:** Eine laufende Instanz eines Images.
- **Dockerfile:** Ein Skript, das die Erstellung eines Images beschreibt.
- **Docker Hub:** Eine Plattform zum Speichern und Teilen von Docker-Images.

---

## Erste Schritte

- **Installation:** Lade Docker von [docker.com](https://www.docker.com/) herunter und installiere es.
- **Container starten:**

```bash
sh

docker run hello-world
```

---

## Grundlegende Docker-Befehle

| Befehl | Beschreibung |
|--------|--------------|
| `docker version` | Zeigt die installierte Docker-Version an. |
| `docker info`    | Gibt eine Übersicht über die Docker-Installation. |
| `docker help`    | Zeigt Hilfe zu Docker-Befehlen an. |

---

### Container-Verwaltung

| Befehl | Beschreibung |
|--------|--------------|
| `docker run IMAGE` | Startet einen Container aus einem Image. |
| `docker run -d IMAGE` | Startet einen Container im Hintergrund (detached mode). |
| `docker run -p HOST_PORT:CONTAINER_PORT IMAGE` | Startet einen Container und bindet Ports. |
| `docker ps` | Zeigt laufende Container an. |
| `docker ps -a` | Zeigt alle Container (auch gestoppte). |

---

### Container-Verwaltung II

| Befehl | Beschreibung |
|--------|--------------|
| `docker stop CONTAINER_ID` | Stoppt einen laufenden Container. |
| `docker start CONTAINER_ID` | Startet einen gestoppten Container neu. |
| `docker restart CONTAINER_ID` | Startet einen Container neu. |
| `docker rm CONTAINER_ID` | Löscht einen Container. |
| `docker logs -lq` | Gibt die ID des zuletzt erstellten Containers zurück. |
| `docker logs CONTAINER_ID` | Zeigt Logs eines Containers an. |
| `docker exec -it CONTAINER_ID bash` | Öffnet eine interaktive Shell im Container. |

---

### Image-Verwaltung

| Befehl | Beschreibung |
|--------|--------------|
| `docker images` | Zeigt alle lokal gespeicherten Images an. |
| `docker pull IMAGE` | Lädt ein Image aus Docker Hub herunter. |
| `docker build -t IMAGE_NAME .` | Erstellt ein Image aus einer `Dockerfile`. |
| `docker rmi IMAGE_ID` | Löscht ein Image. |
| `docker tag IMAGE_NAME NEW_TAG` | Erstellt eine neue Tag-Version eines Images. |
| `docker push IMAGE_NAME` | Lädt ein Image in ein Repository (Docker Hub). |

---

### Netzwerk und Volumes

| Befehl | Beschreibung |
|--------|--------------|
| `docker network ls` | Listet verfügbare Netzwerke auf. |
| `docker network create NETWORK_NAME` | Erstellt ein neues Netzwerk. |
| `docker volume ls` | Zeigt existierende Volumes an. |
| `docker volume create VOLUME_NAME` | Erstellt ein neues Volume. |

---

### Docker-Compose (Mehrere Container verwalten)

| Befehl | Beschreibung |
|--------|--------------|
| `docker-compose up`   | Startet alle Container aus einer `docker-compose.yml`. |
| `docker-compose down` | Stoppt und entfernt alle Container. |
| `docker-compose ps`   | Zeigt laufende Container in Docker Compose. |

Diese Befehle helfen dir, Docker effizient zu nutzen!

---

## Eigenes Image erstellen

- Erstelle eine Dockerfile
- Baue ein Image mit docker build -t mein-image .
- Starte es mit docker run mein-image

Mit Docker lassen sich Anwendungen effizient, portabel und skalierbar betreiben.

---

## MSSQL Server in einem Docker-Container starten

In diesem Abschnitt wollen wir einen `Container` für eine MSSQL Datenbank erstellen. Dazu sind folgende Schritte notwendig:

1. Vorbereitung des Speicherortes
2. Starten des MSSQL Servers mit den Zugangsdaten

---

### Vorbereitung

- Erstellen Sie ein Verzeichnis `Share/docker/mssql`.

In diesem lokalen Verzeichnis werden alle Dateien des MSSQL Servers gespeichert. Diese ist erforderlich, damit nach einem Neustart des `Containers` die Datenbank[en] erhalten bleiben. 

Falls Sie keinen Speicherort angeben, werden die Dateien nach dem Beenden des `Containers` gelöscht und Sie haben die Datenbank[en] verloren.

---

### Starten des MSSQL Servers

Um einen Microsoft SQL Server in Docker zu starten, verwende folgenden Befehl:

> docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=passme!1234' -p 1433:1433 --name mssql-container -v C:\Share\docker\mssql:/var/opt/mssql/data -d mcr.microsoft.com/mssql/server:latest

---

#### Erklärung

* `-e 'ACCEPT_EULA=Y'` → Akzeptiert die Microsoft-Lizenzvereinbarung.
* `-e 'SA_PASSWORD=passme!1234'` → Setzt ein sicheres Passwort für den SQL Server.
* `-p 1433:1433` → Bindet den SQL-Server-Port für externe Verbindungen.
* `-v C:\Share\docker\mssql:/var/opt/mssql/data` → Speichert die Datenbank-Dateien auf dem lokalen Speicher.
* `--name mssql-container` → Gibt dem Container einen Namen.
* `-d` → Führt den Container im Hintergrund aus.
* `mcr.microsoft.com/mssql/server:latest` → Nutzt das neueste MSSQL-Image von Microsoft.

---

#### Verbindung zum SQL Server testen

> docker exec -it mssql-container /opt/mssql-tools/bin/sqlcmd -S localhost -U SA

Anschließend werden Sie aufgefordert das `Password:` einzugeben. Nach der erfolgreichen Authentifizierung befinden Sie sich in der `SqlCmd`-Umgebung. 

---

#### Einfache SQLCmd-Befehle

| Befehl | Beschreibung |
|--------|--------------|
| `SELECT @@VERSION;`| Zeigt die SQL Server Version an. |
| `CREATE DATABASE TestDB;` | Erstellt eine neue Datenbank. |
| `USE TestDB;` | Wechselt zu einer bestimmten Datenbank. |
| `CREATE TABLE Users (ID INT, Name NVARCHAR(50));` | Erstellt eine Tabelle. |
| `INSERT INTO Users (ID, Name) VALUES (1, 'Alice'); ` | Fügt eine Zeile in die Tabelle ein. |
| `SELECT * FROM Users;` | Zeigt alle Daten aus der Tabelle an. |
| `DROP DATABASE TestDB;`| Löscht die Datenbank. |

---

#### Einfachen Befehl absetzen


---

### Container stoppen und entfernen

Sie können den `Container` mit folgendem Befehl stoppen:

> docker stop mssql-container

Mit dem folgendem Befehl können Sie den `Container`  entfernen:

> docker rm mssql-container

