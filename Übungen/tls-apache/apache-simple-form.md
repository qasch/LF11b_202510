# Installation Apache2 und simples Login Formular

> Ziel: Auf einem **Debian**-Server Apache2 mit PHP installieren, welcher ein einfaches **Login-Formular** bereitstellt. Anschliessend wird die Übertragung der Formulardaten mit Wireshark beobachtet.

>[!NOTE] Führt die gesamte Übung per SSH von eurem lokalen Rechner aus durch. So könnt ihr angenehmer arbeiten und habt vor allem die Möglichkeit, Copy und Paste zu benutzen.
>
> Die Public-Key-Authentifizierung braucht dafür nicht eingerichtet zu sein, Login per User/Passwort ist genauso möglich. Falls ihr möchtet, dürft ihr das aber natürlich auch nochmal wiederholen und einrichten.

## 0): Installation und Konfiguration von `sudo` für den Benutzer *student*

Falls der Benutzer `student` noch keine `sudo`-Rechte besitzt, kann er wie folgt eingerichtet werden:

### 1. Prüfen, ob `sudo` installiert ist

```bash
apt list --installed | grep sudo
```

Falls `sudo` nicht installiert ist:

```bash
su -   # Wechsel in den Root-Benutzer
apt update
apt install sudo
```

### 2. Benutzer `student` zur `sudo`-Gruppe hinzufügen

```bash
usermod -aG sudo student
```

Damit wird der Benutzer zur Grupps `sudo` hinzugefügt und kann künftig Befehle mit `sudo` ausführen.

Nun müsst ihr euch einmal vom System (z.B. per SSH) ab- und wieder anmelden, um die neue Gruppenzugehörigkeit gülitg zu machen.

### 3. Testen der Berechtigung

Wechsle zum Benutzer:

```bash
su - student
```

Führe anschließend einen Testbefehl aus:

```bash
sudo whoami
```

Wenn die Ausgabe lautet:

```
root
```

dann wurde `sudo` erfolgreich eingerichtet.

---

## 1) Apache2 und PHP installieren

1. Apache2 installieren:

   ```bash
   sudo apt -y install apache2
   ```
2. PHP für Apache installieren. Da Apache2 standardmässig nur HTML Seiten ausliefern kann, müssen wir zusätzlich PHP an sich und das Paket `libapache2-mod-php` installieren und aktivieren. Dies ist ein zusätzliches Modul für den Apache2:

   ```bash
   sudo apt -y install php libapache2-mod-php
   ```
3. Apache Module aktivieren:

   ```bash
   sudo a2enmod php # lädt das zum System passende PHP-Modul, ggf. ignorieren wenn bereits aktiv
   sudo systemctl reload apache2 # Webserverkonfiguration neu einlesen
   ```
4. Test ob Webserver läuft (ggf. muss das Paket `curl` installiert werden mit `apt install curl`). Auf dem Debian‑Server:

   ```bash
   curl -I http://127.0.0.1/
   # Erwartet: HTTP/1.1 200 OK
   ```

## 2) Formular anlegen

1. Datei erstellen und Inhalt einfügen:

   ```bash
   sudo nano /var/www/html/simple-login-form.php
   ```

   **Inhalt:**

   ```php
   <?php

   $received = false;
   $username = '';
   $password = '';

   if ($_SERVER['REQUEST_METHOD'] === 'POST') {
       $username = trim($_POST['username'] ?? '');
       $password = trim($_POST['password'] ?? '');
       $time = date('Y-m-d H:i:s');
       $entry = "[$time] username={$username} password={$password}\n";
       @file_put_contents(__DIR__ . '/lab_submissions.log', $entry, FILE_APPEND | LOCK_EX);
       $received = true;
   }

   function h($s) { return htmlspecialchars($s, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8'); }
   ?>

   <!doctype html>
   <html lang="de">
   <head>
     <meta charset="utf-8">
     <meta name="viewport" content="width=device-width,initial-scale=1">
     <title>Lab Login (Bootstrap)</title>
     <!-- Bootstrap 5 (CDN). For offline use, download and host locally. -->
     <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
   </head>
   <body class="bg-light">
     <div class="container py-5">
       <div class="row justify-content-center">
         <div class="col-12 col-md-8 col-lg-5">
           <div class="card shadow-sm">
             <div class="card-body">
               <h1 class="h4 mb-1">Simple Login Form</h1>
               <p class="text-muted mb-4">This page posts username &amp; password to a server-side log file for testing purposes.</p>

               <?php if ($received): ?>
                 <div class="alert alert-info" role="alert">
                   <strong>Received</strong> — submission logged to <code>form_submissions.log</code>.
                 </div>
                 <pre class="bg-dark text-light p-3 rounded mb-3"><code><?= "username: " . h($username) . "\npassword: " . h($password) ?></code></pre>
               <?php endif; ?>

               <form method="post" autocomplete="off" novalidate>
                 <div class="mb-3">
                   <label for="username" class="form-label">Username</label>
                   <input id="username" name="username" type="text" maxlength="256" required class="form-control" />
                 </div>

                 <div class="mb-3">
                   <label for="password" class="form-label">Password</label>
                   <div class="input-group">
                     <input id="password" name="password" type="password" maxlength="256" required class="form-control" />
                     <button class="btn btn-outline-secondary" type="button" id="togglePw" aria-label="Show password">👁</button>
                   </div>
                 </div>

                 <div class="d-flex gap-2">
                   <button type="submit" class="btn btn-primary">Submit</button>
                   <button type="reset" class="btn btn-outline-secondary">Reset</button>
                 </div>

                 <p class="text-muted mt-3 mb-0">
                   Logfile: <code><?= h(__DIR__ . '/form_submissions.log') ?></code>
                 </p>
               </form>
             </div>
           </div>
         </div>
       </div>
     </div>

     <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
     <script>
       // Tiny helper: show/hide password (no external deps)
       (function(){
         const btn = document.getElementById('togglePw');
         const input = document.getElementById('password');
         if (btn && input) {
           btn.addEventListener('click', function(){
             const isPw = input.getAttribute('type') === 'password';
             input.setAttribute('type', isPw ? 'text' : 'password');
             btn.setAttribute('aria-label', isPw ? 'Hide password' : 'Show password');
           });
         }
       })();
     </script>
   </body>
   </html>
   ```
2. Eigentümer & Rechte sicherstellen (Webserver kann die Datei lesen):

   ```bash
   sudo chown root:www-data /var/www/html/simple-login-form.php
   sudo chmod 0640 /var/www/html/simple-login-form.php
   ```
3. **Lokaler Test** (auf Debian):

   ```bash
   curl -I http://127.0.0.1/simple-login-form.php
   # 200 OK erwartet
   ```
**Logdatei:** Das Script schreibt nach `/var/www/html/form_submissions.log`. 

Datei erstellen und Schreibrechte des Apache‑Users (`www-data`) sicherstellen:

```bash
sudo touch /var/www/html/form.log
sudo chown www-data:www-data /var/www/html/form.log
sudo chmod 0644 /var/www/html/form.log
```

## 3) Vom Windows‑Rechner im selben LAN zugreifen

1. **IP des Debian‑Servers** ermitteln (falls noch nicht notiert):

   ```bash
   ip a
   # oder
   hostname -I
   # z. B. 192.168.1.50
   ```

2. **Windows** → **PowerShell**: Erreichbarkeit prüfen

   ```powershell
   ping 192.168.1.50
   ```

3. **Browser** auf Windows öffnen und URL aufrufen:

   * `http://192.168.1.50/simple-login-form.php`

4. **Fehlerbehebung** (wenn kein Zugriff):

5. **Log prüfen** (auf Debian), nachdem du das Formular abgeschickt hast:

   ```bash
   tail /var/www/html/form_submissions.log
   ```

## 4) Übermittelte Formulardaten mit Wireshark prüfen

Wir wollen nun die Verbindung bzw. Datenübertragung mit **Wireshark** prüfen. Dazu installieren wir Wireshark auf unserem Windows Rechner.

### Wireshark herunterladen & installieren

1. Lade den Windows‑Installer von **wireshark.org** (aktuelle Windows‑Build) herunter.
2. Starte den Installer (Rechtsklick → Als Administrator ausführen, falls nötig).
3. Folge dem Installer‑Dialog. Wähle die Standardoptionen (typischerweise passen sie für Lab‑Einsatz).

> **Wichtig:** Wireshark verlangt die Installation von **Npcap** (Treiber für Packet‑Capture). Der Installer bietet an, Npcap mitzuinstallieren — das ist normalerweise erforderlich.

---

### Npcap Optionen (während der Installation)

Während der Npcap‑Installationsseite wirst du Optionen sehen. Empfohlen:

* **Install Npcap** — angehakt (erforderlich für Capture)
* **Support raw 802.11 traffic (and monitor mode) for wireless adapters** — optional; für typische Ethernet/LAN‑Setups nicht nötig
* **WinPcap API-compatible Mode** — aktiviere dies, falls du ältere Tools brauchst; nicht zwingend für Wireshark
* **Install Npcap in WinPcap API-compatible Mode** — ok zu aktivieren
* **Allow Npcap to be installed in WinPcap-compatible mode** — default ist ok

Klicke auf "Install" und warte auf Abschluss.

### Vorbereitung auf dem Windows‑Client vor dem Mitschnitt

1. **Netzwerk prüfen:** Stelle sicher, dass dein Windows‑PC die Webserver‑VM erreichen kann:

     ```powershell
     ping 192.168.1.50
     ```

     Ersetze `192.168.1.50` durch die IP deiner Debian‑VM.
2. **Browser öffnen:** Bereite die Formularseite vor (z. B. `http://192.168.1.50/simple-login-form.php`) — aber sende das Formular noch nicht.
3. **Adminrechte:** Falls du Capture‑Rechte brauchst, starte Wireshark als Administrator, oder gewähre Npcap während Installation die nötigen Zugriffsrechte.

---

### Capture starten

1. Starte **Wireshark**.

2. In der Startansicht siehst du eine Liste von Netzwerkinterfaces mit Live‑Traffic‑Minivorschau. Wähle das Interface, das-Netzwerkverkehr zwischen deinem Windows‑PC und dem Debian‑Server trägt. Das ist meist die Ethernet‑(LAN) oder Wi‑Fi‑Schnittstelle.

   * Tipp: Wenn du nicht sicher bist, öffne ein Terminal und generiere Ping‑Traffic (`ping 192.168.1.50`) — in der Wireshark‑Interface‑Liste sollte die Paketanzahl für das richtige Interface steigen.

3. Optional — Capture‑Filter setzen (empfohlen, damit die Mitschrift klein bleibt):

   * Capture‑Filter benutzt die **libpcap**-Syntax und wird **vor** dem Mitschnitt angewendet. Beispiel, nur Verkehr zu/von der Server‑IP auf Port 80 mitschneiden:

     ```
     host 192.168.1.50 and tcp port 80
     ```
   * Wenn du den Server‑IP noch nicht kennst, kannst `tcp port 80` verwenden (alles HTTP auf Port 80).

4. Klick auf **Start** (grüner Hai‑Button) um das Mitschneiden zu beginnen.

---

### Formular im Browser abschicken

1. Nachdem Capture läuft, wechsle zum Browserfenster mit deinem Formular.
2. Fülle Test‑Daten ein (z. B. `username=testuser`, `password=secret123`). 
3. Klicke **Submit**.
4. Beobachte Wireshark: neue Pakete erscheinen.
5. Sobald die Übertragung abgeschlossen ist (Seite lädt/Antwort kommt), kehre zu Wireshark zurück und klicke **Stop** (rotes Quadrat).

---

### POST‑Pakete filtern und Formulardaten prüfen

Jetzt hast du eine Capture‑Datei im Arbeitsspeicher. Wir filtern nach der POST‑Anfrage.

#### a) Einfache Display‑Filter (Wireshark‑Syntax)

* Alle HTTP POST Requests anzeigen:
  ```
  http.request.method == "POST"
  ```
* HTTP Verkehr zu oder von einem Host (Server‑IP):
  ```
  ip.addr == 192.168.1.50 && tcp.port == 80
  ```
* Alle HTTP Pakettypen anzeigen (Requests + Responses):
  ```
  http
  ```

> **Hinweis:** Display‑Filter sind **fall‑sensitiv** in der Syntax (Feldnamen), aber Stringwerte werden in Anführungszeichen geschrieben.

#### b) Paket‑Details ansehen

1. Wähle ein Paket aus, das als `POST` in der Spalte `Info` angezeigt wird (z. B. `POST /simple-login-form.php HTTP/1.1`).
2. Unten im Fenster siehst du drei Teile: **Packet List** (oben), **Packet Details** (Mitte), **Packet Bytes** (unten).
3. In **Packet Details** erweitere die Sektion **Hypertext Transfer Protocol**. Dort findest du:

   * Request‑Line: `POST /... HTTP/1.1`
   * Header‑Felder (Host, User‑Agent, Content‑Type, Content‑Length …)
   * **Line‑based text data** oder **Entity Body**: Hier steht der POST‑Body, z. B. `username=testuser&password=secret123`.
4. Schau dir ansonsten den kompletten Mitschnitt auf der rechten Seite an. Dort solltest du die Formulardaten ganz am Ende sehen.

---

### TCP‑Stream / HTTP‑Body anschauen

Manchmal ist es einfacher, den gesamten Conversation‑Stream zu sehen.

1. Rechtsklick auf das HTTP‑Request‑Paket (POST) → **Follow** → **TCP Stream**.
2. Es öffnet sich ein neues Fenster mit der gesamten Unterhaltung zwischen Client und Server (Request + Response), im Klartext. Du findest dort den POST‑Body als rohe Zeichenkette.
3. Unter dem Stream‑Fenster kannst du den Stream in verschiedenen Encodings anzeigen; meist ist `Auto`/`Raw` passend.
4. Schließe das Fenster und Wireshark setzt automatisch einen Display‑Filter, der nur die Pakete dieses TCP‑Streams anzeigt.

> Alternativ kannst du **Follow → HTTP Stream** nutzen (manche Wireshark‑Versionen bieten direkt HTTP Follow), oder in Packet Details den Body expandieren.

### Troubleshooting & typische Fallen

* **Kein Traffic sichtbar**

  * Falsches Interface ausgewählt — überprüfe welches Interface Traffic zeigt (siehe Ping‑Trick).
  * VPN läuft auf Windows und tunnelt den Verkehr; schalte ihn aus oder wähle das VPN‑Interface.
  * Windows‑Firewall blockiert nicht das Capture, aber sie kann den Verkehr verändern — prüfe die Netzwerk‑Topologie.

* **VM in Hyper‑V ist nicht im selben LAN**

  * Falls die VM über einen internen or private switch läuft, ist sie nicht direkt erreichbar vom Windows‑Client. Stelle in Hyper‑V sicher, dass die VM eine **External Virtual Switch** verwendet, damit sie eine IP aus demselben LAN bekommt.

* **Windows und VM auf unterschiedlichem Subnetz**

  * Stelle sicher, dass Router/Firewall-Regeln den Traffic zwischen beiden erlauben.

* **Wireshark zeigt sehr viel Traffic**

  * Nutze einen Capture‑Filter (z. B. `host 192.168.1.50 and tcp port 80`) um nur relevanten Traffic zu sammeln.

---

### Rechtliches, Ethik & Sicherheit

* Mitschnitte dürfen **nur** in einer autorisierten, kontrollierten Umgebung mit ausdrücklicher Zustimmung stattfinden.
* Vermeide das Mitschneiden oder Dekodieren von personenbezogenen oder fremden Produktionsdaten.
