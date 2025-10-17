# Praktische Einführung in symmetrische und asymmetrische Verschlüsselung mit GPG

## Ziel
- Verstehen der Funktionsweise von symmetrischer und asymmetrischer Verschlüsselung.
- Praktische Demonstration der Verschlüsselungsarten mit GPG (GNU Privacy Guard).
- Zusammenarbeit in Gruppen: Public Keys werden ausgetauscht und importiert.

## Voraussetzungen

1. **Software:**
   - GPG installiert: Prüfen mit `gpg --version`. Installieren bei Bedarf:
     ```bash
     sudo apt update
     sudo apt install gnupg
     ```

## Schritt-für-Schritt-Anleitung

### Teil 1: Symmetrische Verschlüsselung

#### 1.1. Datei erstellen
Erstellt eine Beispieltextdatei mit beliebigem Inhalt:
```bash
echo "Dies ist eine geheime Nachricht." > geheim.txt
```
**Erklärung des Kommandos:** Ihr nutzt hier einen *Redirect* (`>`), um den *Standardausgabekanal* des Kommandos `echo` in eine Datei umzuleiten. Vorsicht: Eine bereits bestehende Datei würde zuerst geleert, also überschrieben werden.

#### 1.2. Symmetrische Verschlüsselung der Datei
Verschlüsselt die Datei mit einem Passwort:
```bash
gpg --symmetric geheim.txt
```
Falls die Passwortabfrage immer wieder erscheint, gebt ihr ein nicht ausreichend sicheres Passwort ein. GPG möchte hier eine Kombination aus Klein- und Großbuchstaben, Ziffern und Sonderzeichen haben.

- GPG fragt nach einem Passwort.
- Die Datei `geheim.txt` wird verschlüsselt und als `geheim.txt.gpg` gespeichert.
- Wir könnten aber auch einen anderen Dateinamen über die Option `-o` angeben:
```bash
gpg -o geheim-verschluesselt.gpg --symmetric geheim.txt
```

#### 1.3. Verschlüsselte Datei analysieren
Prüft den Unterschied zwischen der ursprünglichen und der verschlüsselten Datei:
```bash
file geheim.txt geheim.txt.gpg
ls -lh geheim.txt geheim.txt.gpg
```

#### 1.4. Entschlüsselung
Entschlüsselt die Datei mit dem verwendeten Passwort:
```bash
gpg --decrypt geheim.txt.gpg > entschluesselt.txt
```
Prüft den Inhalt:
```bash
cat entschluesselt.txt
```
Hinweis: Es erscheint keine Passwortabfrage. Das liegt daran, dass GPG bzw. der GPG-Agent das eingegebene Passwort für eine bestimmte Zeit speichert.

Du kannst folgendes tun, um eine Passwortabfrage zu erzwingen (zur besseren Nachvollziehbarkeit, dass wirklich ein Passwort abgefragt wird): 

- Wechsel in den Root Account (**Dieses Mal** mit dem Kommando `su`, nicht mit `su -l`. Der Unterschied ist, dass wir so nicht in das Heimatverzeichnis von `root` wechseln, sondern im aktuellen Verzeichnis bleiben.
- Versuche nun, die Datei zu entschlüsseln. Dieses Mal erfolgt die Abfrage des Passworts.
- Alternativ könntest du auch eine weitere Terminal Sitzung starten oder dich als ein anderer Benutzer anmelden.

### Teil 2: Asymmetrische Verschlüsselung

#### 2.1. Schlüsselpaar erstellen
Jeder Schüler generiert ein Schlüsselpaar:
```bash
gpg --full-generate-key
```
- **Wichtige Eingaben:**
  1. Wähle den Typ: `1` (RSA und RSA).
  2. Schlüssellänge: `3072` (Standard), `4096` ginge auch.
  3. Ablaufdatum: `0` (kein Ablauf). Hier könnte auch z.B. `1y` oder `2y` angegeben werden.
  4. Name, E-Mail und Kommentar eingeben.
  5. Passwort für den privaten Schlüssel festlegen.

Überprüfe den erstellten Schlüssel:
```bash
gpg --list-keys
```

#### 2.2. Öffentlichen Schlüssel exportieren und teilen

Wir wollen nun eine Datei für eine andere Person verschlüsseln. Dazu benötigen wir den öffentlichen Schlüssel dieser Person.

Tut euch dazu jeweils zu zweit zusammen und macht folgendes:

Exportiere deinen öffentlichen Schlüssel:
```bash
gpg --export --armor "<Name oder E-Mail>" > public.key
# besser, da theoretisch mehrere Schlüssel für die gleich E-Mail/Name existieren könnten:
gpg --export --armor "<Key-ID>" > public.key
```
Die Option `--armor` sorgt dafür, dass der Public-Key im ASCII Format ausgegeben wird und nicht als Binärdatei.
 
Gib die Datei `public.key` (diese enthält deinen öffentlichen Schlüssel) an die andere Person weiter. Du kannst z.B. den Inhalt der Datei mit `cat` ausgeben, in die Zwischenablage kopieren und in den Teams-Chat stellen. 

Die andere Person öffnet nun einen Editor (`nano public.key`), fügt den Inhalt dort ein und speichert die Datei.

Achtet darauf, den gesamten Inhalt der Datei zu übernehmen, also inklusive `-----BEGIN PGP PUBLIC KEY BLOCK-----
` und `-----END PGP PUBLIC KEY BLOCK-----`

Macht dies beide, denn dann könnt ihr euch gegenseitig Dateien verschlüsseln. 

#### 2.3. Öffentlichen Schlüssel importieren
Importiere nun den öffentlichen Schlüssel der anderen Person in euren Keyring:
```bash
gpg --import public.key
```
Prüfe, ob der importierte Schlüssel vorhanden ist:
```bash
gpg --list-keys
```
Hier solltest du nun zwei Schlüssel sehen: Deinen eigenen öffentlichen Schlüssel und den soeben importierten der anderen Person.

#### 2.4. Datei asymmetrisch verschlüsseln
Verwende nun den öffentlichen Schlüssel der anderen Person, um die Datei zu verschlüsseln:
```bash
echo Geheime Nachricht von <dein Name> > name.txt
gpg --encrypt --recipient "<Name oder E-Mail des Empfängers/der anderen Person>" name.txt
```
- Die verschlüsselte Datei wird als `name.txt.gpg` gespeichert.
- Wählt hier einen sinnvollen Dateinamen wie z.B. `tux.txt.gpg` oder `anton.txt.gpg` usw.

#### 2.5. Datei entschlüsseln
Die verschlüsselte Datei wird nun auch zur anderen Person übertragen. Dazu könnt ihr z.B. `scp` nutzen. `scp` funktioniert prinzipiell genauso wie `cp`, damit können Dateien aber auch über eine SSH-Verbindung kopiert werden. 

Startet dazu eine neue CMD oder Powershell auf eurem **lokalen** Windows Rechner, auf dem Teams läuft. Verbindet euch **nicht** per SSH mit dem Debian Server.

Die Syntax ist folgende:
```
scp <quelle> <ziel>
# konkret z.B.:
scp student@10.100.20.30:name.txt.gpg .
```
So wird die Datei `name.txt.gpg` im Heimatverzeichnis von `student` auf `10.100.20.30` in das aktuelle Verzeichnis, z.B. `C:\User\Student` kopiert (achte hierbei auf den Punkt `.`, der für das aktuelle Verzeichnis steht.

Übertrage nun die Datei über den Teams-Chat an die andere Person.

Die andere Person lädt die Datei nun auf ihren Windows Rechner herunter und überträgt sie wiederum mit `scp` auf ihren Linux Server:
```
scp name.txt.gpg student@10.100.30.40:
```
Passt den Pfad zur Datei `name.txt.gpg` entsprechend an und achtet auf den Doppelpunkt hinter der IP Adresse. Ansonsten erzeugt `scp` eine lokale Kopie von `name.txt.gpg` mit dem Namen `student@10.100.30.40`.

Jetzt kann die Datei mit dem privaten Schlüssel entschlüsseln werden:
```bash
gpg --decrypt name.txt.gpg > entschluesselt_asym.txt
```
Prüfe den Inhalt:
```bash
cat entschluesselt_asym.txt
```
#### 2.6. Datei entschlüsseln 2

Versuche nun, die Datei welche du **für die andere Person** verschlüsselt hast, selbst zu entschlüsseln. Dies sollte nicht funktionieren. Mache dir klar, warum nicht.

Fällt dir eine Möglichkeit ein, wie das doch gehen könnte? Wirf dazu einen Blick in die Manpage von GPG (`man gpg`) oder recherchiere dazu im Internet.

### Teil 3: Signatur und Verifikation

#### 3.1. Datei signieren
Signiere die Datei, um ihre Authentizität sicherzustellen:
```bash
gpg --clearsign geheim.txt
```
- Die signierte Datei wird als `geheim.txt.asc` erstellt.
- Lass dir den Inhalt der Datei mit `cat` ausgeben.
- Die Signatur erfolgt mit dem **privaten** Schlüssel, nicht wie beim Verschlüsseln mit dem öffentlichen. Mach dir klar, warum...

#### 3.2. Signatur überprüfen
Ein Gruppenmitglied überprüft die Signatur:
```bash
gpg --verify geheim.txt.gpg
```
- GPG zeigt an, ob die Signatur gültig ist und von welchem Schlüssel sie stammt.
- Die Überprüfung erfolgt jetzt natürlich mit dem jeweiligen **öffentlichen** Schlüssel.

Damit das Signieren bzw. Überprüfen der Signatur funktioniert, müsst ihr ggf. das *Trust Level* des jeweiligen Keys bearbeiten. Nutzt dazu das Kommando `gpg --edit-key <Key-ID / E-Mail des importierten PubKeys>`. Es startet eine interaktive Shell von GPG. Das Kommando `help` zeigt euch alle verfügbaren Kommandos an. Gebt also `trust` ein und setzt das Trust-Level auf `5 = I trust ultimately`.

Was tut ihr da eigentlich? Informiert euch selbstständig über *Trust-Level* bzw. das Konzept des *Web of Trust*.
