# ✅ Voraussetzungen

Interesse am Thema Smart Home ist eine gute Grundlage – reicht allein aber nicht aus. Auch die Tatsache, dass im Studium ein **Pflichtpraktikum** absolviert werden muss, ist für sich noch keine Qualifikation. Damit du dich im Labor schnell zurechtfindest und von Anfang an Freude an der Arbeit hast, setzen wir **grundlegende Vorkenntnisse** voraus.

Wir unterscheiden dabei zwischen

* **MUSS-Kriterien:** Kenntnisse, die du **bereits mitbringen** solltest. Ohne sie ist die Einarbeitung sehr mühsam – für dich und für uns.
* **KANN-Kriterien:** Kenntnisse, die **ein Plus** sind. Niemand erfüllt alle davon; vieles lernt man im Labor.

Zu jedem Punkt erklären wir kurz, **wofür** wir ihn im Labor brauchen, und verlinken passende Kapitel aus dem Kompendium **[Informatik](https://github.com/Michdo93/Informatik)**.

<!-- TOC -->
## Inhaltsverzeichnis

- [MUSS-Kriterien](#muss-kriterien)
  - [Linux](#linux)
  - [Netzwerk](#netzwerk)
  - [Programmiersprachen](#programmiersprachen)
- [KANN-Kriterien](#kann-kriterien)
  - [Netzwerk und Funkstandards](#netzwerk-und-funkstandards)
  - [Weitere Programmiersprachen](#weitere-programmiersprachen)
  - [openHAB](#openhab)
  - [Weitere Smart-Home-Kenntnisse](#weitere-smart-home-kenntnisse)
  - [Virtualisierung](#virtualisierung)
  - [Hardware und angrenzende Themen](#hardware-und-angrenzende-themen)
  - [Sonstiges](#sonstiges)
- [Selbsteinschätzung](#selbsteinschätzung)
<!-- /TOC -->

## MUSS-Kriterien

### Linux

Fast alle Systeme im Labor – Server, virtuelle Maschinen, Container, Raspberry Pis – laufen unter **Linux**, und das meist **ohne grafische Oberfläche**. Gute bis sehr gute Linux-Kenntnisse auf der Kommandozeile sind deshalb die wichtigste Voraussetzung.

#### systemd-Services

Programme im Labor sollen nach einem Neustart **automatisch** und **zuverlässig** laufen. Dafür nutzen wir systemd. Es reicht nicht, `systemctl start` und `systemctl enable` zu kennen – du solltest **eigene Service-Units schreiben** können. Dazu gehört:

| Thema | Was du können solltest |
| --- | --- |
| **Service-Typen** | Unterschiede zwischen `simple`, `exec`, `forking`, `oneshot` und `notify` kennen und den passenden wählen |
| **Ablauf** | `ExecStartPre`, `ExecStart`, `ExecStartPost`, `ExecStop` und `ExecReload` einsetzen |
| **Abhängigkeiten** | Einen Dienst erst starten, **nachdem** ein anderer läuft (`After=`, `Requires=`, `Wants=`) – und ihn automatisch beenden, **wenn** der andere endet (`BindsTo=`, `PartOf=`) |
| **Verzögerter Start** | Den Start einer Anwendung verzögern, z. B. bis das Netzwerk oder ein Broker bereit ist |
| **Umgebung** | `WorkingDirectory`, `Environment`/`EnvironmentFile` und einen eigenen `User`/`Group` festlegen |
| **Fehlerbehandlung** | `Restart=on-failure`, `RestartSec` und eine **maximale Anzahl an Neustarts** (`StartLimitBurst`, `StartLimitIntervalSec`) konfigurieren |
| **Logging** | Ausgaben mit `journalctl -u <dienst>` auswerten |

📖 [systemd-Services](https://github.com/Michdo93/Informatik/blob/main/Linux%20%26%20Werkzeuge/systemd-Services.md)

#### Cron

Wiederkehrende Aufgaben wie Backups oder Aufräumarbeiten laufen zeitgesteuert. Du solltest **Cron-Jobs** anlegen können, die **Cron-Syntax** verstehen (`0 3 * * 0` – wann läuft das?) und typische Fallen kennen (anderer `PATH`, keine Ausgabe sichtbar).

📖 [Cron & systemd-Timer](https://github.com/Michdo93/Informatik/blob/main/Linux%20%26%20Werkzeuge/Cron%20%26%20systemd-Timer.md)

#### Bash- und Shell-Skripte

Viele kleine Aufgaben im Labor erledigen wir mit Skripten: Geräte abfragen, Backups anstoßen, Konfigurationen verteilen. Du solltest **eigenständig Skripte schreiben** und ausführen können – mit Variablen, Bedingungen, Schleifen, Parametern und sauberer Fehlerbehandlung.

📖 [Bash-Skripte](https://github.com/Michdo93/Informatik/blob/main/Linux%20%26%20Werkzeuge/Bash-Skripte.md)

#### Benutzer, Gruppen und Rechte

Dienste sollen nicht als `root` laufen, und nicht jeder soll jede Datei lesen können. Du solltest **Benutzer und Gruppen** anlegen und **Lese-, Schreib- und Ausführungsrechte** setzen können (`chmod`, `chown`, `usermod`, `sudo`).

📖 [Benutzer, Gruppen & Rechte](https://github.com/Michdo93/Informatik/blob/main/Linux%20%26%20Werkzeuge/Benutzer%2C%20Gruppen%20%26%20Rechte.md)

#### SSH

Auf nahezu alle Geräte greifen wir **per SSH** zu. Du solltest

* den SSH-Server konfigurieren können (z. B. eigener Port, Anmeldung per Schlüssel),
* **Schlüsselpaare** (Private/Public Key) erzeugen und auf Zielsysteme kopieren können (`ssh-keygen`, `ssh-copy-id`),
* Dateien mit **`scp`** (oder `rsync`) übertragen können,
* **`sshpass`** kennen und wissen, wofür wir es einsetzen (z. B. für Ansible und das openHAB Exec-Binding).

📖 [Best Practice SSH](https://github.com/Michdo93/Informatik/blob/main/Best%20Practices/SSH.md)

#### Zertifikate

Im Labor gilt: **HTTPS und TLS von Anfang an** – auch für MQTT. Du solltest wissen, wie man Zertifikate **erzeugt** und **erneuert**, und **Certbot** (Let's Encrypt) sollte kein Fremdwort sein.

📖 [Best Practice Zertifikate](https://github.com/Michdo93/Informatik/blob/main/Best%20Practices/Zertifikate.md)

#### Web-Server und Deployment

Viele Dienste im Labor haben eine Weboberfläche oder eine API. Du solltest wissen, wie man einen Web-Server **konfiguriert und startet** – und zwar so, wie man es auch im Produktivbetrieb macht:

| Situation | So machen wir es | Nicht so |
| --- | --- | --- |
| Web-Server / Reverse Proxy | **Nginx** | Apache2 (nur, wenn es einen guten Grund gibt) |
| Node.js-Anwendung | Mit **pm2** betreiben | `node app.js` in einer offenen Konsole |
| Flask-Anwendung | Mit **Gunicorn** (hinter Nginx) betreiben | `python3 app.py` oder `flask run` – das ist nur der Entwicklungsserver |

📖 [Web-Server & Deployment](https://github.com/Michdo93/Informatik/blob/main/Best%20Practices/Web-Server%20%26%20Deployment.md)

---

### Netzwerk

Ein Smart Home ist vor allem eines: ein **Netzwerk** aus vielen Geräten. Gute Netzwerkkenntnisse sind deshalb unverzichtbar.

| Thema | Was du können oder verstehen solltest |
| --- | --- |
| **HTTP/HTTPS** | Aufbau von Anfrage und Antwort (**Header** und **Body**), wichtige **Header** (z. B. `Content-Type`, `Authorization`), **Statuscodes** und die Methoden **GET, POST, PUT, DELETE** und **HEAD** |
| **REST-APIs** | Ressourcen, Endpunkte und Methoden verstehen und APIs mit `curl` oder Code ansprechen – z. B. die openHAB REST API |
| **MQTT** | Broker, Topics, Publish/Subscribe, QoS, Retained Messages |
| **DHCP** | Wie Geräte ihre IP-Adresse bekommen – und warum wir sie **sofort fest reservieren** |
| **Diagnose** | Erreichbarkeit mit `ping` prüfen, IP-Konfiguration auslesen, offene Ports testen |
| **Hostname** | Den Hostnamen eines Geräts ändern |
| **Verkabelung** | LAN-Kabel verlegen und an Switches anschließen – **ohne Schleifen (Loops)** zu erzeugen |

📖 [Netzwerk](https://github.com/Michdo93/Informatik/blob/main/Netzwerk/README.md) · [HTTP & REST](https://github.com/Michdo93/Informatik/blob/main/Netzwerk/HTTP%20%26%20REST.md) · [Best Practice MQTT](https://github.com/Michdo93/Informatik/blob/main/Best%20Practices/MQTT.md) · [Best Practice DHCP](https://github.com/Michdo93/Informatik/blob/main/Best%20Practices/DHCP.md)

---

### Programmiersprachen

| Sprache | Wofür wir sie einsetzen |
| --- | --- |
| **Python 3** | Gerätesteuerungen, Skripte, MQTT-Clients, Anbindung an die openHAB REST API, openHAB-Regeln (Python Scripting) |
| **JavaScript** | Weboberflächen und Dashboards, Node.js-Dienste, openHAB-Regeln (JavaScript Scripting) |

Du musst kein Profi sein, solltest aber **eigenständig kleine Programme** schreiben, fremden Code lesen und mit Bibliotheken umgehen können.

📖 [Code-Formatierung und Styleguides](https://github.com/Michdo93/Informatik/blob/main/Code-Formatierung/README.md)

> **Learning by Doing:** Vieles lernt man erst im Labor richtig. Mit Tipps, Tricks und der Unterstützung der Labormitarbeitenden wirst du in alle Themen noch deutlich tiefer einsteigen. Die MUSS-Kriterien sorgen dafür, dass du dabei von Anfang an mitreden und selbstständig arbeiten kannst.

---

## KANN-Kriterien

Die folgenden Kenntnisse sind **nicht erforderlich**, erleichtern den Einstieg aber deutlich – und sie zeigen uns, wo deine Interessen liegen. Bei der Wahl deiner Aufgaben und Projekte berücksichtigen wir sie gerne.

### Netzwerk und Funkstandards

| Thema | Wofür |
| --- | --- |
| **Z-Wave, Zigbee, Matter/Thread** | Viele Smart-Home-Geräte funken nicht über WLAN, sondern über eigene Funkstandards |
| **nmap** | Geräte und offene Ports im Netzwerk finden |
| **arp** | Zuordnung von IP- zu MAC-Adressen nachvollziehen – z. B. um ein neues Gerät zu identifizieren |

📖 [Netzwerk-Grundlagen](https://github.com/Michdo93/Informatik/blob/main/Netzwerk/Netzwerk-Grundlagen.md) · [Smart-Home-Funkstandards](https://github.com/Michdo93/Informatik/blob/main/Netzwerk/Smart-Home-Funkstandards.md)

### Weitere Programmiersprachen

* **Java** (openHAB selbst ist in Java geschrieben, ebenso die Bindings)
* **C#**
* **C++** (z. B. für Mikrocontroller und Robotik)
* Weitere Sprachen sind für die Arbeit im Labor eher zweitrangig.

### openHAB

Wer openHAB schon kennt, kann sofort loslegen. Wichtige Begriffe sind:

| Begriff | Bedeutung |
| --- | --- |
| **Binding** | Erweiterung, die openHAB mit einer Geräteklasse oder einem Protokoll verbindet |
| **Thing** | Ein konkretes Gerät oder ein Dienst |
| **Channel** | Eine einzelne Funktion eines Things (z. B. Helligkeit, Temperatur) |
| **Item** | Der Zustand bzw. die steuerbare Größe, mit der openHAB intern arbeitet |
| **Sitemap / UI** | Benutzeroberflächen |
| **Rule** | Automatisierungsregel (Rules DSL, JavaScript, Python, Blockly) |
| **Persistence** | Speicherung von Zustandsverläufen |
| **Semantisches Modell** | Ordnung der Items nach Orten, Geräten und Eigenschaften |

### Weitere Smart-Home-Kenntnisse

* **Design Pattern**, etwa das Proxy-Pattern oder die Kombination aus State und Command, wie sie in openHAB-Regeln häufig vorkommt
  📖 [Design Pattern](https://github.com/Michdo93/Informatik/blob/main/Design%20Pattern/README.md)
* **Smart Config** und andere Verfahren, mit denen Geräte ins WLAN gebracht werden
* **Andere Smart-Home-Systeme** wie Home Assistant, ioBroker, FHEM oder Node-RED

### Virtualisierung

* **Proxmox** (virtuelle Maschinen und LXC-Container)
* **Docker** und Docker Compose

📖 [Proxmox: VMs und Container](https://github.com/Michdo93/Informatik/blob/main/Virtualisierung/Proxmox.md) · [Docker & Compose betreiben](https://github.com/Michdo93/Informatik/blob/main/Virtualisierung/Docker%20%26%20Compose.md) · [Orchestrierung & Choreografie](https://github.com/Michdo93/Informatik/blob/main/Software-Konzepte/Orchestrierung%20%26%20Choreografie.md)

### Hardware und angrenzende Themen

* Kenntnisse zu **IoT-Geräten**
* **Einplatinencomputer** wie der Raspberry Pi
* **Mikrocontroller** wie Arduino oder ESP32/ESP8266
* **Robotik**
* **VR/AR**
* **Mobile Development**

### Sonstiges

* **USB/IP** (USB-Geräte über das Netzwerk bereitstellen)
* **NFC/RFID**
* Erfahrungen mit **Kinect-Kameras** oder dem **Leap Motion Controller**
* Bibliotheken wie **keyboard** oder **pygame**
* **Sniffer** und **Wireshark** (Netzwerkverkehr und Funkprotokolle analysieren) – z. B. für das [Reverse Engineering](https://github.com/Michdo93/Informatik/blob/main/Workarounds%20%26%20Hacks/Reverse%20Engineering.md) von Geräten ohne offene Schnittstelle
* **Löten** und Aufbau einfacher Schaltungen (Sensoren, Mikrocontroller, DIY-Geräte)

---

## Selbsteinschätzung

Nimm dir zehn Minuten und beantworte die folgenden Fragen ehrlich. Kannst du die meisten davon **ohne Nachschlagen** zumindest grob beantworten, bist du gut vorbereitet.

**Linux**

* [ ] Was ist der Unterschied zwischen `Type=simple` und `Type=oneshot`?
* [ ] Wie sorgst du dafür, dass ein Dienst nach einem Absturz neu startet – aber nicht endlos?
* [ ] Wann läuft der Cron-Job `30 2 * * 1-5`?
* [ ] Was bedeutet `chmod 750 skript.sh`?
* [ ] Wie richtest du die Anmeldung per SSH-Schlüssel auf einem Raspberry Pi ein?
* [ ] Warum startet man eine Flask-Anwendung im Betrieb nicht mit `flask run`?

**Netzwerk**

* [ ] Was ist der Unterschied zwischen `PUT` und `POST`?
* [ ] Was bedeutet der Statuscode `401`, was `403`?
* [ ] Was ist ein MQTT-Broker, und was ist ein Topic?
* [ ] Warum bekommt jedes Gerät im Labor eine feste IP-Adresse?
* [ ] Was passiert, wenn man zwei Ports desselben Switches mit einem Kabel verbindet?

**Programmierung**

* [ ] Kannst du in Python eine JSON-Antwort einer REST-API abrufen und auswerten?
* [ ] Kannst du in JavaScript auf ein Ereignis (z. B. einen Klick) reagieren und eine Anfrage an einen Server senden?

> Bei einigen Fragen unsicher? Kein Problem – die verlinkten Kapitel im Kompendium [Informatik](https://github.com/Michdo93/Informatik) helfen dir, die Lücken vor dem Start zu schließen. Genau dafür sind sie gedacht.

---
