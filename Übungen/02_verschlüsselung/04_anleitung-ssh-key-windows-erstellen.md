# Anleitung: SSH‑Schlüsselpaar auf Windows erstellen und PubKey‑Authentifizierung auf Debian einrichten

## Ziel
1. SSH‑Schlüsselpaar auf einem Windows‑System erstellen
2. Den öffentlichen Schlüssel auf einen Debian‑Server übertragen
3. Anmeldung mittels Public‑Key‑Authentifizierung
4. Häufige Fehler erkennen und beheben

## Voraussetzungen
- Ein Windows‑System mit installiertem **OpenSSH‑Client** (`ssh`, `ssh-keygen`, `scp`) – ist bei Windows 10/11 i. d. R. schon dabei.
- Ein Debian‑Server mit installiertem **SSH‑Server** (`openssh-server`), Dienstname unter systemd: **`ssh`**.
- Zugriff auf den Debian‑Server per Benutzername/Passwort (für den Erstzugang).

## Schritt 1: SSH‑Schlüsselpaar auf Windows erstellen

### 1.1 Prüfen, ob OpenSSH vorhanden ist
1. Öffne die **PowerShell**.
2. Gib folgendes Kommando ein:
   ```powershell
   ssh -V
   ```
3. Wird etwas wie `OpenSSH_for_Windows_8.xp1` angezeigt, ist OpenSSH installiert. Falls nicht: **Einstellungen → Apps → Optionale Features** öffnen, **OpenSSH-Client** installieren.

### 1.2 Schlüsselpaar erzeugen
Erzeuge explizit einen Ed25519‑Schlüssel mit dem Kommando `ssh-keygen`. Die Option `-t ed25569` bewirkt, dass der Ed25519 Verschlüsselungsalgorithmus verwendet wird.
```powershell
ssh-keygen -t ed25519
```
- Standardspeicherort bestätigen (Enter): `C:\Users\<DeinBenutzer>\.ssh\id_ed25519`
- **Passphrase** setzen: Eine optionale Sicherheitsstufe. Sie verschlüsselt den privaten Schlüssel zusätzlich mit einem Passwort. Sollte der Schlüssel in fremde Hände gelangen, könnte er so also nicht ohne weiteres genutzt werden.
- Drücke hier `Enter`, um keine Passphrase zu verwenden, gib aber besser ein "sicheres" Passwort ein.
- Für diese Übung braucht nicht unbedingt eine Passphrase verwendet zu werden, **in der Praxis** aber bitte **immer**!

Ergebnis:
- Privater Schlüssel: `C:\Users\<DeinBenutzer>\.ssh\id_ed25519`
- Öffentlicher Schlüssel: `C:\Users\<DeinBenutzer>\.ssh\id_ed25519.pub`

### 1.3 Optional: Windows ssh-agent aktivieren um Passwort zu speichern
Windows 10/11 bringt den **OpenSSH Authentication Agent** mit. So musst du die Passphrase nicht jedes Mal eingeben:
```powershell
Get-Service ssh-agent
# Falls gestoppt:
Start-Service ssh-agent
Set-Service -Name ssh-agent -StartupType Automatic
# Schlüssel dem SSH Agent hinzufügen
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```
## Schritt 2: Öffentlichen Schlüssel auf den Debian‑Server übertragen

Es gibt mehrere Wege, den öffentlichen Schlüssel auf den Server zu übertragen. **Methode 2 (SCP)** ist robust und funktioniert in jeder Umgebung. **Methode 1** eignet sich, wenn du per Terminal/Clipboard arbeiten kannst.

### Methode 1: Manuell per Zwischenablage

#### 1.1 Öffentlichen Schlüssel in die Zwischenablage kopieren
> Funktioniert nicht, wenn deine Umgebung (z. B. bestimmte VMs/Hyper‑V‑Konsolen) keine gemeinsame Zwischenablage hat.

Öffne den Public Key mit einem Editor wie z.B. `notepad`:
```powershell
notepad $env:USERPROFILE\.ssh\id_ed25519.pub
```
Kopiere den **gesamten** Inhalt der Datei in die Zwischenablage.

#### 1.2 `.ssh` auf dem Debian‑Server vorbereiten
**Wichtig:** Führe alle Befehle als `<benutzer>` also z.B. `student` aus, **nicht** als `root` oder mit `sudo`.
```bash
# Login auf dem Server
ssh <benutzer>@<server-ip>
# Verzeichnis für SSH erstellen und Berechtigungen setzen
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```
> Hintergrund: Das Verzeichnis `~/.ssh` muss für andere Benutzer unzugänglich sein, so dass kein anderer Benutzer die sich in diesem Verzeichnis befindlichen Private-Keys (falls erstellt) auslesen oder kopieren kann.

#### 1.3 Öffentlichen Schlüssel einfügen
```bash
nano ~/.ssh/authorized_keys
```
- Füge den kopierten öffentlichen Schlüssel aus der Zwischenablage ein (Rechtsklick zum Einfügen in nano).
- Wichtig ist hier, dass jeder öffentlche Schlüssel genau **eine** Zeile lang ist.
- Es können auch mehrere öffentlche Schlüssel (z.B. von verschiedenen Systemen) enthalten sein, jeweils einer pro Zeile.
- Speichere und schließe die Datei (`CTRL+O`, `Enter`, `CTRL+X`).

Setze die korrekten Rechte:
```bash
chmod 600 ~/.ssh/authorized_keys
```
### Methode 2: Schlüssel mit `scp` übertragen (empfohlen)
Diese Methode funktioniert **immer**, wenn wir SSH-Zugang haben. Das Kommando `scp` ist Teil des SSH-Clients und auch auf Windows Systemen verfügbar, es muss nicht zusätzlich installiert werden. Mit `scp` lassen sich Dateien sowohl lokal (genau wie mit `cp`) als auch verschlüsselt über eine bestehende SSH-Verbindung kopieren.

#### 2.1 Key kopieren
Auf dem Windows Rechner: Kopiere den öffentlchen Schlüssel in das Heimatverzeichnis des Benutzers auf dem Zielsystem:
```powershell
scp "$env:USERPROFILE\.ssh\id_ed25519.pub" <benutzer>@<server-ip>:
```
> Der Doppelpunkt `:` am Ende ist wichtig – ohne ihn würdest du lokal auf dem Windows System eine Datei namens `<benutzer>@<server-ip>` erstellen.

#### 2.2 Auf dem Server einrichten
Auf dem Zielsystem anmelden und Schlüssel an den richtigen Ort kopieren:
```bash
ssh <benutzer>@<server-ip>
mkdir -p ~/.ssh
chmod 700 ~/.ssh
cat ~/id_ed25519.pub >> ~/.ssh/authorized_keys
rm ~/id_ed25519.pub
chmod 600 ~/.ssh/authorized_keys
```
Wir verwenden hier einen doppelten *Redirect*, da dieser evtl. in der Datei `authorized_keys` enthaltene Daten nicht überschreibt, sondern den neuen Key an den bestehenden Inhalt anhängt.

## Schritt 3: Anmeldung mit Public‑Key testen
1. Bestehende SSH‑Session beenden:
   ```bash
   exit
   ```
2. Neu anmelden (von Windows):
   ```powershell
   ssh <benutzer>@<server-ip>
   ```
Wenn eine Passphrase für den Private Key gesetzt ist, fragt der Client danach (oder der `ssh-agent` übernimmt). Achte darauf, dass es sich hierbei **nicht** um das Login Passwort des Users auf dem Linux System handelt, sondern um die Passphrase, mit der der Private Key verschlüsselt ist. 

Was passiert bzw. dass der SSH Key zur Anmeldung genutzt wird kannst du auch in der Ausgabe mit `ssh -v` sehen:
```powershell
ssh -v <benutzer>@<server-ip>
```
Suche nach `Offering public key` und `Authentication succeeded (publickey)`.

## Häufige Fehler & Behebung

### Fehler 1: „Permission denied (publickey)“
- **Ursache**: Falsche Berechtigungen oder Besitzer für `.ssh` oder `authorized_keys`.
- **Lösung:**
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
# Eigentümer nur falls nötig, dann mit Root-Rechten:
sudo chown "$USER":"$USER" ~/.ssh ~/.ssh/authorized_keys
```
### Fehler 2: Öffentlicher Schlüssel falsch/fehlerhaft kopiert
- **Ursache**: Der Schlüssel wurde unvollständig oder fehlerhaft übertragen.
- **Lösung:** Öffne `~/.ssh/authorized_keys` und prüfe, ob der Key **vollständig in einer einzigen Zeile** steht (keine Zeilenumbrüche inmitten des Keys).

### Fehler 3: SSH‑Dienst akzeptiert keine Keys
- **Ursache**: Der SSH-Dienst ist falsch konfiguriert.
- **Prüfen/Anpassen:** Öffnen der Konfigurationsdatei `/etc/ssh/sshd_config` des SSH Servers auf dem Debian System:
```bash
sudo nano /etc/ssh/sshd_config
```
Stelle sicher, dass folgende Zeilen enthalten sind:
```text
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```
SSH Daemon neu laden/neustarten um die geänderte Konfigurationsdatei einzulesen:
```bash
sudo systemctl restart ssh
```

### Logs & Diagnose
- Client‑Verbose:
  ```powershell
  ssh -vvv <benutzer>@<server-ip>
  ```
- Server‑Logs (Debian):
  ```bash
  sudo journalctl -u ssh -f
  ```
Das `-f` bewirkt, dass wir die Log Datei 'live` beobachten können. Wir können also von einem zusätzlichen Terminal aus die Verbindung starten und beobachten, was passiert.

### Sicherheitshinweis
Wenn Public‑Key‑Login funktioniert, kannst du **Passwort‑Login deaktivieren** (optional):
```text
PasswordAuthentication no
```
Danach:
```bash
sudo systemctl restart ssh
```
