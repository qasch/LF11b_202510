# HTTPS-Interception mit *mitmproxy*

In dieser Übung nutzen wir `mitmproxy` um einen Man-In-The-Middle Angriff zu simulieren und per HTTPS übertragene Daten zu entschlüsseln.

## 1. Lernziele

* Wir wollen verstehen, wie bzw. unter welchen Umständen ein Proxy mit eigener CA TLS-Verbindungen entschlüsseln kann.
* Zertifikatsfelder (Issuer, Subject, Validity) interpretieren.

## 2. Voraussetzungen

* Wir benötigen zwei VMs:
  * **Debian 13** — eine "Webserver-VM" (mit eigenem PHP Formular).
  * **Kali Linux** — die "Interception-VM" (mitmproxy, Wireshark).
* Beide VMs befinden sich im selben Netz, können miteinander kommunizieren

## 3. IP-Plan (Beispiele)

* Kali VM: `192.168.100.176/24`
* Debian VM: `192.168.100.215/24`

## 4. Snapshots

Da wir Änderungen am der Kali-VM durchführen, macht es Sinn, vorher einen Snapshot anzulegen, um nachher wieder in den Ausgangszustand zurückkehren zu können.

Dies könnt ihr über Hyper-V machen.

## 5. mitmproxy Setup (auf Kali)

### 5.1 Installation (falls nicht vorhanden)

>[!NOTE]
> Wenn Kali Linux mit den Standarpaketen installiert wurde, sind `mitmproxy` und `wireshark` bereits installiert.

```bash
sudo apt install -y mitmproxy wireshark
```

### 5.2 mitmproxy starten

Startet `mitmproxy` in der Shell oder die Web-GUI `mitmweb`. Das Webinterface ist komfortabler und wird für die Übung empfohlen.

Einige Beispiele, wie `mitmproxy` über die Shell gestartet werden kann:
```bash
# mitmproxy in der Shell starten 
mitmproxy
# oder:
# --listen-port braucht nur angegeben zu werden, falls mitmproxy 
# an einem anderen Port als 8080 lauschen soll
mitmproxy --listen-port 8080
# mitmweb (Webinterfache) starten
mitmweb 
# oder:
# s.o. bezüglich der Ports
mitmweb --listen-port 8080 --web-port 8081
```
## 6. Browser so konfigurieren, dass `mitmproxy` als Proxy genutzt wird

### 6.1 Proxy‑Einstellung im Browser (Kali)

Nutzt hier den Firefox Browser in Kali. Ihr habt doch einen Snapshot der VM angelet, oder? 

Setzt im Browser den Proxy auf `localhost` (`127.0.0.1`) Port `8080` (Standard mitmproxy‑Port):

* Firefox: `Einstellungen` → `Netzwerk‑Einstellungen` → `Manuelle Proxykonfiguration` → HTTP/HTTPS Proxy `127.0.0.1` Port `8080`.

>[!NOTE]
> Firefox verwendet einen internen Zertifikatsspeicher für die Root-Zertifikate und CAs. Chrome/Chromium (basierte Browser) verwenden den des Betriebssystems. Der Einfachheit halber nutzen wir hier also Firefox.

### 6.2 Funktionsweise von `mitmproxy`

Alle Anfragen des Browsers laufen jetzt über `mitmproxy`, welcher diese dann an die aufgerufenen Website weiterleitet. Soweit alles wie bei einem "regulären" Proxy. `mitmproxy` erzeugt aber zusätzlich ein Zertifikat für jede aufgerfufene Website. 

Prüft das, in dem ihr eine beliebige Website (per HTTPS) aufruft. Der Browser sollte (noch, da kümmern wir uns gleich drum) eine Sicherheitswarnung anzeigen. Schaut euch hier einmal das Zertifikat an (z.B. über `Advanced` -> `View Certificate`. Achtet hier auf die Einträge `Suject Name` ->  `Common Name`: hier steht der Name der aufgerufenen Website. Soweit alles korrekt - das Zertifikat passt zur Seite. 

Spannend wird es, wenn ihr eine Seite aufruft, die auch unter anderen URLs erreichbar ist. Schaut mal in den Abschnitt `Subject Alt Names`. Auch hier passen die Einträge.

Recherchiert falls nötig, was unter den einzelnen Einträgen des Zertifikats zu verstehen ist.

Was hier aber nicht passt, ist die CA: Unter `Issuer Name` -> `Common Name` steht `mitmproxy`. Hm...

Was passiert hier? `mitmproxy` stellt bei jedem Aufruf einer Seite selbst ein Zertifikat aus und signiert es. Da unser Browser aber dieser CA (`mitmproxy`) (noch) nicht vertraut, erhalten wir die Sicherheitswarnung. Darum kümmern wir uns jetzt.

## 7. CA-Authority importieren und vertrauen

Wir erinnern uns daran, dass unser Browser Websites vertraut, die ein Zertifikat von einer vertrauenswürdigen CA ausliefern. Dies geschieht über die im Browser oder OS hinterlegten Root-Zertifikate. Diese werden bei der Installation mitgeliefert, über Updates verwaltet, können aber auch manuell hinzugefühgt werden.

Wir werden nun ein Root-Zertifikat bzw. die CA-Authority von `mitmproxy` importieren und den Browser anweisen, diesem zu vertrauen.

Dazu öffnen wir (mit aktivem `mitmproxy`) folgenden URL und laden die CA-Authority für unser Betriebssystem (in diesem Fall Linux) herunter und speichern es (im Verzeichnis `$HOME/Downloads/`) der Kali-VM.

[mitmproxy](http://mitm.it)

### 7.1 CA-Import (Firefox)

1. Öffnet Firefox in Kali.
2. `Settings` → `Privacy & Security` → `Certificates` → `View Certificates...` → `Authorities` → `Import...`.
3. Wählt die Datei `mitmproxy-ca-cert.pem` und aktiviert: "This certificate can identify websites.".

## 8. Erneuter Aufruf einer Website über HTTPS

Ruft nun die gleiche Website erneut über HTTPS auf - ihr erhaltet keine Sicherheitswarnung mehr und der Browser aktzeptiert das Zertifikat als gültig.

Prüft das Zertifikat erneut. Es ist dasselbe wie vorher. Der Unterschied ist nur, dass der Browser jetzt dem Aussteller, der CA vertraut.

## 9. HTTPS Traffic entschlüsseln

Wenn ihr euch jetzt die "Flows" in `mitmproxy` oder im Webinterface (`127.0.0.1:8081`) anschaut, werdet ihr sehen, dass unser Proxy als Man-In-The-Middle den gesamten Traffic unverschlüsselt mitlesen kann.

Um das ganz deutlich zu machen, ruft einmal das Login Formular auf dem Debian Server auf, z.B. über `https://192.168.100.215/simple-login-form.php`.

- Der Aufruf klappt nicht, ihr erhaltet einen `502 Bad Gateway`. 
- Aktiviert jetzt im Webinterface von `mitmproxy` unter `Options` `Don't verify server certificates`. 
- Ruft das Formular erneut auf, z.B. mit `STRG F5`, da ansonsten die gecachte Version verwendet wird.
- Das Formular sollte ohne Warnung angezeigt werden.

**Frage:**

Wäre es auch möglich, diesen Schritt nicht machen zu müssen? Was wäre die Alternative bzw. wie könnten wir unseren Browser dazu bringen, unserem selbst-signierten Zertifikat zu vertrauen?

### 9.1 Formulardaten auslesen

- Tragt nun Username und Passwort in das Login Formular ein und sendet das Formular ab. 
- Sucht den `POST` Request in den Flows von `mitmproxy`.
- Unten unter `Request` sollten nun die Formulardaten im Klartext zu lesen sein.

## 10. Traffic in Wireshark analysieren

Führt den letzten Schritt (Formular) erneut durch und beobachtet den Traffic in Wireshark. Ihr solltet hier eine "reguläre" TLS Verbindung sehen, keinen entschlüsselten Datenverkehr.

Sucht hier nach dem TLS handshake. Findet der zwischen euerm Browser und dem Login Formular der Debian VM statt? Oder zwischen wem?

Spielt etwas mit Wireshark hermum und versucht mehr über den Ablauf heraus zu bekommen.

Ihr könnt auch einmal die anderen Schritte in Wireshark beobachten:

Entzieht z.B. der CA im Browser das Vertrauen (`Edit Trust`) usw.

## 11. Weiterführende Hinweise & Übungen (optional)

* mitmproxy Dokumentation: [https://mitmproxy.org](https://mitmproxy.org)
* [Burp Suite](https://en.wikipedia.org/wiki/Burp_Suite) (als Alternative für Fortgeschrittene)
* Recherchiert nach einer anderen Möglichkeit, den HTTPS Traffic nachträglich in *Wireshark* zu entschlüsseln. Es gibt da z.B. eine Methode, bei der der Session Key abgefangen und nachträglich genutzt werden kann...

## 12. Realität und Gegenmaßnahmen

1. Als wie realistisch schätzt ihr einen solchen Angriff ein? Wir mussten hier ja schon etwas "Hand anlegen" bzw. vorbereiten, damit das so funktioniert. Wie könnte soetwas dennoch passieren?
2. Welche Gegenmaßnahmen könnte man ergreifen bzw. was lernen wir daraus bezüglich des Umgangs mit Zertifikaten, Browsern, Websites etc.?
3. Würde *HSTS* hier helfen?
4. Was ist *OSCP Stapling*?
5. Was versteht man unter *Certificate Pinning*?

## 13. Hinweis

Wir haben hier expliziet unser selbst erstelltes Login Formular zu Demo Zwecken genutzt. Obiges Vorgehen funktionert aber auch in der "Realität" bzw. mit offiziellen Websites/Formularen.

**Frage:**

Wozu könnte dieses Setup denn noch genutzt werden? Also nicht, um Formulardaten abzufangen, die wir selbst eingeben. Alle Websites bzw. auch alle Apps auf Mobilgeräten nutzen doch TLS. Der Datenverkehr ist für uns also nicht nachvollziehbar. Was wäre also ein weiterer Anwendungsfall für dieses Setup?
