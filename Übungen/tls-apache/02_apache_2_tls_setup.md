# Apache2 absichern mit selbst erstelltem TLS-Zertifikat

> Anleitung zur Absicherung des lokalen Apache2-Webserver mit einem selbstsignierten TLS-Zertifikat.

>[!NOTE]
> Auch diese Übung bitte per SSH vom lokalen Windows Rechner aus durchführen.

## Schritt 1: OpenSSL installieren

Wir benötigen das Paket OpenSSL, um Zertifikate erstellen zu können:

```bash
sudo apt install openssl
```
## Schritt 2: Eigenes selbstsigniertes TLS-Zertifikat erstellen

Da wir lokal arbeiten und kein "offizielles" von einer CA signiertes Zertifikat installieren können, erstellen wir uns ein *selbstsigniertes Zertifikat*.

Wir benötigen ein Verzeichnis, in dem wir das Zertifikat und den privaten Schlüssel speichern können:

```bash
sudo mkdir /etc/ssl/localcerts
```
Folgendes Kommando erzeugt einen **privaten Schlüssel (.key)** und ein **selbstsignierte Zertifikat (.crt)**. Wie in der Praxis auch beschränken wir die Gültigkeit auf 365 Tage:

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/localcerts/server.key \
-out /etc/ssl/localcerts/server.crt
```
Bei der Erstellung des Zertifikats werden zusätzliche Informationen abgefragt. Diese Informationen sind nachher im Zertifikat gespeichert und können angeschaut/ausgelesen werden:

- Country Name (2 letter code): z.B. `DE`
- State or Province Name: z.B. das Bundesland
- Locality Name: die Stadt in der ihr euch befindet
- Organization Name: z.B. `GFN`
- Common Name: hier wird eigentlich der FQDN bzw. die genaue URL der Webseite angegeben
- Email Address: hier würde eine E-Mail des Website-Betreibenden stehen

Die Angaben die ihr hier macht sind für unsere Testzwecke nicht wichtig, macht hier trotzdem welche und schaut sie euch nachher in eurem Browser bei der Überprüfung des Zertifikats an.

### Erklärung des OpenSSL-Kommandos

| Teil | Bedeutung |
|------|------------|
| `req` | Startet den Zertifikats-Request-Modus von OpenSSL |
| `-x509` | Erstellt ein selbstsigniertes Zertifikat (kein externer CA nötig) |
| `-nodes` | Speichert den privaten Schlüssel unverschlüsselt (ohne Passwortabfrage für Apache) |
| `-days 365` | Gültigkeitsdauer des Zertifikats in Tagen |
| `-newkey rsa:2048` | Erstellt gleichzeitig einen neuen RSA-Schlüssel mit 2048 Bit Länge |
| `-keyout` | Speicherort der privaten Schlüsseldatei |
| `-out` | Speicherort der Zertifikatsdatei |

### Zweck der Dateien

- **`server.key`** – Enthält den **privaten Schlüssel**. Dieser bleibt **streng vertraulich** auf dem Server. Er wird zur Signierung von Zertifikaten genutzt.
- **`server.crt`** – Enthält das **öffentliche Zertifikat**, das an Clients (Browser) gesendet wird. Es bestätigt die Identität des Servers und wird zur **Verschlüsselung des Datenverkehrs** verwendet.

**Zusammenhang (Teil des TLS-Handshake):**

Beim Aufbau einer HTTPS-Verbindung sendet der Server das Zertifikat (`.crt`) an den Client. Mit dem öffentlichen Schlüssel im Zertifikat kann der Client die vom Server signierten Daten verifizieren. Der private Schlüssel (`.key`) bleibt geheim und wird nur auf dem Server verwendet.

## Schritt 3: Apache für HTTPS konfigurieren

### SSL-Modul aktivieren

Der Funktionsumfang des Webservers Apache2 kann über Module erweitert werden. Momentan kann euer Webserver nur Anfragen auf Port 80 entgegennehmen. Prüft das doch einmal auf dem Server über die Eingabe von

    sudo ss -tulpn | grep apache2

Mit dem Kommando `ss` kann man unter anderem die offenen Ports auf dem eigenen System prüfen. Hier sollte nur Port 80 erscheinen.

Aktiviert das SSL-Modul mit folgendem Kommando:

```bash
sudo a2enmod ssl
```
Sorgt dafür, dass die Konfigurationsdatei des Apache2 neu eingelesen wird:

    sudo systemctl restart apache2

Prüft nun erneut, auf welchen Ports der Webserver lauscht:

    sudo ss -tulpn | grep apache2

Hier sollte nun zusätzlich Port 443 erscheinen.

### Apache2 für TLS konfigurieren

Wir müssen nun noch die Konfigurationsdatei für eingegende Verbindungen auf Port 443 bearbeiten:

```bash
sudo nano /etc/apache2/sites-available/default-ssl.conf
```
In der ersten Zeile ist folgender Eintrag:

    <VirtualHost *:443>

Die Konfiguration gilt also für alle Anfragen an diesen Server (`*`) die auf Port 443 hereinkommen, sprich TLS.

Passt folgende Zeilen an:

```apache
SSLEngine on
SSLCertificateFile      /etc/ssl/localcerts/server.crt
SSLCertificateKeyFile   /etc/ssl/localcerts/server.key
```
Achtet darauf, dass ihr andere Zeilen, die Zertifikate angeben wie 
```
SSLCertificateFile      /etc/ssl/certs/ssl-cert-snakeoil.crt
SSLCertificateKeyFile   /etc/ssl/private/ssl-cert-snakeoil.key
```
auskommentiert.

Dies sind Standard-Zertifikate, die von Debian mitgeliefert werden. Wir wollen aber unser selbst erstelltes Zertifikat nutzen.

## Schritt 4: Apache neu starten

```bash
sudo systemctl restart apache2
```

## Schritt 5: Zugriff testen

Öffne im Browser:
```
https://ip-adresse-debian
# z.B.
https://192.168.0.50
```

Es erscheint eine Sicherheitswarnung, da das Zertifikat selbstsigniert ist. Ihr könnt bzw. müsst die Ausnahme hinzufügen/bestätigen, um fortzufahren. 

Schaut euch aber vorher das Zertifikat einmal an. Ihr solltet hier die Informationen sehen, die ihr bei der Erstellung angegeben habt. In der Realität könnte soetwas natürlich auch vorkommen, z.B. wenn ein Zertifikat abgelaufen ist. Aber auch, wenn jemand versucht, euch auf eine gefälschte Seite umzuleiten. 

Seid in der realität also sehr wachsahm, wenn euch solch eine Warunung angezeigt wird.

## Mit Wireshark prüfen, ob die Formulardaten immer noch auszulesen sind

Nutzt Wireshark und prüft, ob die Formulardaten nun immer noch wie beim Protokoll HTTP mitzulesen sind. Setze dazu den ensprechenden Filter in Wireshark. Hinweis: Die Verbindung läuft jetzt nicht mehr über Port 80 bzw. das Protokoll HTTP. Wie hiess das nochmal? SSL? Oder war es TLS? Probiert doch mal `tls`...

Ihr könnt auch einmal auf das erste `Client Hello` Paket auswählen und euch den Beginn des TLS-Handshakes anschauen. 

Oder anschliessend einen Rechtsklick darauf und 'Follow' -> 'TCP Stream' auswählen. Hier dürfte nur noch der verschlüsselte Dialog (rot -> blau) zu erkennen sein.

Vergleicht das doch nochmal mit der unverschlüsselten Übertragung per HTTP. Ruft die Seite nochmal über HTTP auf, zeichnet die Pakete auf und macht wieder einen Rechtsklick auf das erste 'TCP [SYN]' Paket und dann 'Follow' -> 'TCP Stream'...

## Optional: HTTP → HTTPS umleiten

Um sicherzustellen, dass unser Formular **immer** über HTTPS aufgerufen wird, auch wenn in der Adresszeile  des Browsers `http://` angegeben wird, können wir eine Umleitung von HTTP auf HTTPS serverseitig erzwingen. Dazu gehen wir wie folgt vor:

Bearbeitet die Datei `/etc/apache2/sites-available/000-default.conf`:

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```
Fügt im `<VirtualHost *:80>`-Block hinzu:

```apache
Redirect "/" "ip-adresse-debian"
# z.B.:
Redirect "/" "https://192.168.0.50/"
```
Damit sagen wir dem Webserver, dass alles was ankommt auf HTTPS umgeleitet werden soll.

>[!NOTE] 
> Auf einem "echten" Server würden wir zusätzlich *HSTS (HTTP Strict Transport Security)* nutzen. Ein HSTS-Header teilt dem Browser z.B. mit: "Bitte verwende für die nächsten 31536000 Sekunden (1 Jahr) immer HTTPS für diese Domain – auch wenn der Benutzer http:// eingibt."

Den könnten wir wie folgt in der `/etc/apache2/sites-available/default-ssl.conf` einfügen:
```
<VirtualHost *:443>
    ServerName 192.168.0.50
    SSLEngine on
    SSLCertificateFile      /etc/ssl/localcerts/server.crt
    SSLCertificateKeyFile   /etc/ssl/localcerts/server.key
    
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
    
    # weitere Konfiguration...
</VirtualHost>```

Konfiguration neu einlesen:

```bash
sudo systemctl reload apache2
```
