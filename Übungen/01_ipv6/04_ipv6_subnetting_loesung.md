# IPv6 Subnetting - Übungsaufgaben mit Lösungen

## Aufgabe 1: Grundlegendes Subnetting

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:cafe::/48` erhalten und sollst daraus 5 /64-Subnetze für folgende Bereiche erstellen:
- Management-VLAN
- Benutzer-VLAN
- Server-VLAN
- Gäste-VLAN
- DMZ

**Lösung:**

Bei einem /48-Präfix stehen dir die Bits 49-64 (16 Bit = 4 Hexadezimalstellen) für Subnetting zur Verfügung.

```
Basis: 2001:db8:cafe::/48

Management-VLAN:  2001:db8:cafe:0001::/64
Benutzer-VLAN:    2001:db8:cafe:0002::/64
Server-VLAN:      2001:db8:cafe:0003::/64
Gäste-VLAN:       2001:db8:cafe:0004::/64
DMZ:              2001:db8:cafe:0005::/64
```

**Erklärung:** Die vierte Hextet-Gruppe (nach dem dritten Doppelpunkt) wird für die Subnetz-Nummerierung verwendet. Jedes /64-Subnetz kann 2^64 Hosts aufnehmen.

---

## Aufgabe 2: Grundlegendes Subnetting - Variante 2

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:beef::/48` erhalten und sollst daraus 6 /64-Subnetze für folgende Bereiche erstellen:
- IoT-Geräte
- Drucker-VLAN
- Telefonie-VLAN
- Video-Konferenz
- Entwicklung
- Testing

**Lösung:**

Bei einem /48-Präfix stehen dir die Bits 49-64 (16 Bit = 4 Hexadezimalstellen) für Subnetting zur Verfügung.

```
Basis: 2001:db8:beef::/48

IoT-Geräte:        2001:db8:beef:000a::/64
Drucker-VLAN:      2001:db8:beef:000b::/64
Telefonie-VLAN:    2001:db8:beef:000c::/64
Video-Konferenz:   2001:db8:beef:000d::/64
Entwicklung:       2001:db8:beef:0010::/64
Testing:           2001:db8:beef:0011::/64
```

**Erklärung:** Die vierte Hextet-Gruppe wird für die Subnetz-Nummerierung verwendet. Ich habe bei 000a begonnen und sprechende Blöcke für Entwicklung/Testing gewählt (0010, 0011). Jedes /64-Subnetz bietet genug Adressen für alle Geräte.

---

## Aufgabe 3: Grundlegendes Subnetting - Variante 3

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:1234::/48` erhalten und sollst daraus 7 /64-Subnetze für folgende Bereiche erstellen:
- Produktions-Datenbank
- Staging-Datenbank
- Test-Datenbank
- Web-Frontend
- API-Server
- Cache-Server
- Monitoring

**Lösung:**

```
Basis: 2001:db8:1234::/48

Produktions-DB:    2001:db8:1234:0100::/64
Staging-DB:        2001:db8:1234:0101::/64
Test-DB:           2001:db8:1234:0102::/64
Web-Frontend:      2001:db8:1234:0200::/64
API-Server:        2001:db8:1234:0201::/64
Cache-Server:      2001:db8:1234:0202::/64
Monitoring:        2001:db8:1234:0300::/64
```

**Erklärung:** Hier habe ich eine logische Gruppierung verwendet: 
- 0100er-Bereich für Datenbanken
- 0200er-Bereich für Web-Services
- 0300er-Bereich für Infrastruktur
Diese Struktur erleichtert später die Firewall-Konfiguration und Dokumentation.

---

## Aufgabe 4: Grundlegendes Subnetting - Variante 4

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:abcd::/48` erhalten und sollst daraus 8 /64-Subnetze für folgende Bereiche erstellen:
- Geschäftsführung
- Buchhaltung
- Vertrieb
- Marketing
- IT-Abteilung
- Lager
- Produktion
- Empfang/Besucher

**Lösung:**

```
Basis: 2001:db8:abcd::/48

Geschäftsführung:  2001:db8:abcd:0001::/64
Buchhaltung:       2001:db8:abcd:0002::/64
Vertrieb:          2001:db8:abcd:0003::/64
Marketing:         2001:db8:abcd:0004::/64
IT-Abteilung:      2001:db8:abcd:0005::/64
Lager:             2001:db8:abcd:0006::/64
Produktion:        2001:db8:abcd:0007::/64
Empfang/Besucher:  2001:db8:abcd:0008::/64
```

**Erklärung:** Bei einer abteilungsbasierten Struktur bietet sich eine einfache, aufsteigende Nummerierung an. Die Reihenfolge kann nach Wichtigkeit oder alphabetisch gewählt werden. Jede Abteilung ist klar getrennt und kann unabhängig verwaltet werden.

---

## Aufgabe 5: /62-Subnetting für Server-Cluster

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:5000::/48` erhalten. Erstelle 4 /62-Subnetze für verschiedene Server-Cluster:
- Kubernetes-Cluster A
- Kubernetes-Cluster B
- Docker-Swarm-Cluster
- VMware-Cluster

Gib für jeden Cluster auch die enthaltenen /64-Subnetze an.

**Lösung:**

Ein /62-Subnetz enthält 4 /64-Subnetze (2^(64-62) = 2^2 = 4).

```
Basis: 2001:db8:5000::/48

Kubernetes-Cluster A: 2001:db8:5000:0000::/62
  └─ Subnetz 1: 2001:db8:5000:0000::/64 (Pods)
  └─ Subnetz 2: 2001:db8:5000:0001::/64 (Services)
  └─ Subnetz 3: 2001:db8:5000:0002::/64 (Storage)
  └─ Subnetz 4: 2001:db8:5000:0003::/64 (Management)

Kubernetes-Cluster B: 2001:db8:5000:0004::/62
  └─ Subnetz 1: 2001:db8:5000:0004::/64 (Pods)
  └─ Subnetz 2: 2001:db8:5000:0005::/64 (Services)
  └─ Subnetz 3: 2001:db8:5000:0006::/64 (Storage)
  └─ Subnetz 4: 2001:db8:5000:0007::/64 (Management)

Docker-Swarm-Cluster: 2001:db8:5000:0008::/62
  └─ Subnetz 1: 2001:db8:5000:0008::/64 (Containers)
  └─ Subnetz 2: 2001:db8:5000:0009::/64 (Overlay Network)
  └─ Subnetz 3: 2001:db8:5000:000a::/64 (Ingress)
  └─ Subnetz 4: 2001:db8:5000:000b::/64 (Management)

VMware-Cluster: 2001:db8:5000:000c::/62
  └─ Subnetz 1: 2001:db8:5000:000c::/64 (vMotion)
  └─ Subnetz 2: 2001:db8:5000:000d::/64 (Storage)
  └─ Subnetz 3: 2001:db8:5000:000e::/64 (Management)
  └─ Subnetz 4: 2001:db8:5000:000f::/64 (VMs)
```

**Erklärung:** 
- Ein /62 fasst immer 4 aufeinanderfolgende /64-Subnetze zusammen
- Die letzen 2 Bit der vierten Hextet-Gruppe variieren (00, 01, 10, 11 binär = 0-3 dezimal)
  - `00 00` -> `0`
  - `01 00` -> `4`
  - `10 00` -> `8`
  - `11 00` -> `C`
- /62 eignet sich ideal für logische Gruppierungen von verwandten Subnetzen
- Nächstes freies /62 beginnt bei 0010 (nach 000f)

```
2001:db8:c000:0000:: /62
│    │   │    │
16   32  48   64 Bits

Bit 1-16:   2001
Bit 17-32:  0db8
Bit 33-48:  c000
Bit 49-64:  0000  ← Hier liegt unser /62!
```
Die 4. Hextet-Gruppe (Bits 49-64) ist entscheidend. Bei `/62` schneiden wir bei **Bit 62**, das liegt **14 Bits innerhalb dieser Gruppe** (49 + 13 = 62, wir zählen ab Bit 49).

### Die Umrechnung: Hexadezimal zu Binär

Jede Hexadezimalziffer repräsentiert **4 Bits**. Die vierte Gruppe `0000` hat 4 Hex-Ziffern = 16 Bits.
```
Hexadezimal: 0    0    0    0
Binär:       0000 0000 0000 0000
Position:    |<-- 14 Bits -->|<-2->|
             └─ Netzwerk ─┘└Subnetz┘
```

Bei `/62` gehören die **ersten 14 Bits zum Netzwerk-Präfix** und die **letzten 2 Bits zum Subnetz-Teil**.

### Warum durch 4 teilbar?

Die letzten 2 Bits können 4 verschiedene Werte annehmen:
- `00` = 0
- `01` = 1
- `10` = 2
- `11` = 3

Diese 2 Bits befinden sich ganz rechts in der vierten Hextet-Gruppe:
```
Hexadezimal: 0000
Binär:       0000 0000 0000 00│00│ ← diese 2 Bits variieren
                            └──┘
                         /62-Subnetz-Bits
```

Wenn wir das in Hexadezimal betrachten:
```
Binär:       0000 0000 0000 0000  = 0000 (hex) = /62 Block 0
Binär:       0000 0000 0000 0001  = 0001 (hex)   └─ /64 Subnetz 0
Binär:       0000 0000 0000 0010  = 0002 (hex)   └─ /64 Subnetz 1
Binär:       0000 0000 0000 0011  = 0003 (hex)   └─ /64 Subnetz 2
Binär:       0000 0000 0000 0100  = 0004 (hex) = /62 Block 1
                                                  └─ /64 Subnetz 3
```

### Praktisches Beispiel
```
/62-Block startet bei: 0008

Binär: 0000 0000 0000 1000
                    └──┘└─ 00 = Subnetz 0 → 0008
                    └──┘└─ 01 = Subnetz 1 → 0009
                    └──┘└─ 10 = Subnetz 2 → 000a
                    └──┘└─ 11 = Subnetz 3 → 000b

Nächster /62-Block: 000c (die ersten 14 Bits ändern sich)
```

### Die Regel: /62-Blöcke starten immer bei Vielfachen von 4

Weil die letzten 2 Bits intern zum /62-Block gehören, müssen sie bei `00` starten. In Dezimal bedeutet das:
```
0, 4, 8, 12 (c), 16 (10), 20 (14), 24 (18), ...
```

In Hexadezimal:
```
0000, 0004, 0008, 000c, 0010, 0014, 0018, 001c, 0020, ...
```

---

## Aufgabe 6: /62-Subnetting für Multi-Site-Deployment

**Aufgabe:** Ein Unternehmen hat das Präfix `2001:db8:c000::/48` und möchte für 6 Zweigstellen jeweils ein /62-Subnetz bereitstellen. Jede Zweigstelle benötigt:
- Ein Benutzer-VLAN
- Ein Server-VLAN
- Ein VoIP-VLAN
- Ein Management-VLAN

Erstelle die Subnetz-Struktur für alle 6 Standorte.

**Lösung:**

```
Basis: 2001:db8:c000::/48

Zweigstelle Hamburg: 2001:db8:c000:0000::/62
  └─ Benutzer:    2001:db8:c000:0000::/64
  └─ Server:      2001:db8:c000:0001::/64
  └─ VoIP:        2001:db8:c000:0002::/64
  └─ Management:  2001:db8:c000:0003::/64

Zweigstelle Berlin: 2001:db8:c000:0004::/62
  └─ Benutzer:    2001:db8:c000:0004::/64
  └─ Server:      2001:db8:c000:0005::/64
  └─ VoIP:        2001:db8:c000:0006::/64
  └─ Management:  2001:db8:c000:0007::/64

Zweigstelle München: 2001:db8:c000:0008::/62
  └─ Benutzer:    2001:db8:c000:0008::/64
  └─ Server:      2001:db8:c000:0009::/64
  └─ VoIP:        2001:db8:c000:000a::/64
  └─ Management:  2001:db8:c000:000b::/64

Zweigstelle Köln: 2001:db8:c000:000c::/62
  └─ Benutzer:    2001:db8:c000:000c::/64
  └─ Server:      2001:db8:c000:000d::/64
  └─ VoIP:        2001:db8:c000:000e::/64
  └─ Management:  2001:db8:c000:000f::/64

Zweigstelle Frankfurt: 2001:db8:c000:0010::/62
  └─ Benutzer:    2001:db8:c000:0010::/64
  └─ Server:      2001:db8:c000:0011::/64
  └─ VoIP:        2001:db8:c000:0012::/64
  └─ Management:  2001:db8:c000:0013::/64

Zweigstelle Stuttgart: 2001:db8:c000:0014::/62
  └─ Benutzer:    2001:db8:c000:0014::/64
  └─ Server:      2001:db8:c000:0015::/64
  └─ VoIP:        2001:db8:c000:0016::/64
  └─ Management:  2001:db8:c000:0017::/64
```

**Berechnung der /62-Grenzen:**
- /62 bedeutet: Die letzten 2 Bit der vierten Hextet-Gruppe gehören zum Subnetz-Teil
- Jedes /62 startet bei einem durch 4 teilbaren Wert (in Hex: 0, 4, 8, c, 10, 14, 18, ...)
- Schema: 0000, 0004, 0008, 000c, 0010, 0014

**Erklärung:** 
- Diese Struktur ist perfekt für standardisierte Zweigstellen mit gleichen Anforderungen
- Jede Zweigstelle hat genau 4 Subnetze in einem zusammenhängenden /62-Block
- Routing-Tabellen können pro Zweigstelle eine einzige /62-Route verwenden
- Die konsistente Struktur erleichtert die Automatisierung und das Troubleshooting
- Innerhalb jedes /62 ist die Reihenfolge identisch: Benutzer → Server → VoIP → Management

**Anzahl möglicher /62-Blöcke aus /48:**
- 64 - 48 = 16 Bit verfügbar
- Davon 2 Bit für /62 intern → 14 Bit für /62-Blöcke
- 2^14 = 16.384 mögliche /62-Blöcke aus einem /48-Präfix

---
## Aufgabe 7: Berechnung verfügbarer Subnetze

**Aufgabe:** Wie viele /64-Subnetze kannst du aus einem /56-Präfix erstellen? Wie viele aus einem /52-Präfix?

**Lösung:**

**Aus /56:**
- /64 - /56 = 8 Bit für Subnetting
- 2^8 = **256 /64-Subnetze**

**Aus /52:**
- /64 - /52 = 12 Bit für Subnetting
- 2^12 = **4096 /64-Subnetze**

**Erklärung:** Die Differenz zwischen der erhaltenen Präfixlänge und /64 (Standard-Subnetzgröße) gibt an, wie viele Bits für Subnetting zur Verfügung stehen.

---

## Aufgabe 8: Adressbereiche identifizieren

**Aufgabe:** Gegeben ist das Subnetz `2001:db8:ab00:0040::/64`. 
- Wie lautet die erste verwendbare Host-Adresse?
- Wie lautet die letzte verwendbare Host-Adresse?
- Welches ist das nächste Subnetz?

**Lösung:**

```
Subnetz:           2001:db8:ab00:0040::/64

Erste Adresse:     2001:db8:ab00:0040::1
(oder vollständig: 2001:db8:ab00:0040:0000:0000:0000:0001)

Letzte Adresse:    2001:db8:ab00:0040:ffff:ffff:ffff:ffff

Nächstes Subnetz:  2001:db8:ab00:0041::/64
```

**Erklärung:** 
- Bei IPv6 gibt es keine Broadcast-Adresse, daher ist theoretisch jede Adresse nutzbar
- Die ::0 Adresse wird oft für das Netzwerk selbst vermieden
- Das nächste Subnetz erhöht man durch Inkrementierung der vierten Hextet-Gruppe

---

## Aufgabe 9: Hierarchisches Subnetting

**Aufgabe:** Ein Unternehmen hat `2001:db8:1000::/48` erhalten und hat 3 Standorte:
- Hauptsitz (benötigt 100 Subnetze)
- Zweigstelle A (benötigt 20 Subnetze)
- Zweigstelle B (benötigt 10 Subnetze)

Erstelle eine hierarchische Subnetz-Struktur.

**Lösung:**

```
Basis: 2001:db8:1000::/48

Hauptsitz:
  Bereich: 2001:db8:1000:0000::/56 (256 Subnetze verfügbar)
  Subnetze: 2001:db8:1000:0000::/64 bis 2001:db8:1000:00ff::/64

Zweigstelle A:
  Bereich: 2001:db8:1000:0100::/56 (256 Subnetze verfügbar)
  Subnetze: 2001:db8:1000:0100::/64 bis 2001:db8:1000:01ff::/64

Zweigstelle B:
  Bereich: 2001:db8:1000:0200::/56 (256 Subnetze verfügbar)
  Subnetze: 2001:db8:1000:0200::/64 bis 2001:db8:1000:02ff::/64
```

**Erklärung:** Durch die Verwendung von /56-Blöcken für jeden Standort erhältst du eine klare hierarchische Struktur und jedem Standort steht ausreichend Platz für Wachstum zur Verfügung.

---

## Aufgabe 10: Variable Length Subnet Masks (VLSM)

**Aufgabe:** Du hast `2001:db8:5000::/48` und benötigst:
- 1 Subnetz mit /60 für Point-to-Point-Links (16 /64-Subnetze)
- 1 Subnetz mit /62 für Server (4 /64-Subnetze)
- Mehrere normale /64-Subnetze

Plane die Struktur.

**Lösung:**

```
Basis: 2001:db8:5000::/48

Point-to-Point (/60):
  2001:db8:5000:0000::/60
  Enthält: 2001:db8:5000:0000::/64 bis 2001:db8:5000:000f::/64

Server-Block (/62):
  2001:db8:5000:0010::/62
  Enthält: 2001:db8:5000:0010::/64 bis 2001:db8:5000:0013::/64

Normale /64-Subnetze ab:
  2001:db8:5000:0014::/64
  2001:db8:5000:0015::/64
  ... und so weiter
```

**Erklärung:** 
- Ein /60 fasst 16 /64-Subnetze zusammen (60 zu 64 = 4 Bit Unterschied = 2^4)
- Ein /62 fasst 4 /64-Subnetze zusammen (62 zu 64 = 2 Bit Unterschied = 2^2)
- VLSM ermöglicht effiziente Aggregation und Routing

---

## Aufgabe 11: Subnetz-Zugehörigkeit prüfen

**Aufgabe:** Gehört die Adresse `2001:db8:a:b:c:d:e:f` zum Subnetz `2001:db8:a::/48`?

**Lösung:**

```
Adresse:  2001:db8:000a:000b:000c:000d:000e:000f
Subnetz:  2001:db8:000a::/48

Die ersten 48 Bit (= erste 3 Hextets) müssen übereinstimmen:
2001:db8:000a ✓

Antwort: JA, die Adresse gehört zum Subnetz.
```

**Weitere Beispiele:**

```
2001:db8:b:1::5     → NEIN (drittes Hextet ist 'b' statt 'a')
2001:db8:a:ffff::1  → JA (erste 3 Hextets stimmen überein)
2001:db9:a:1::1     → NEIN (zweites Hextet ist 'db9' statt 'db8')
```

**Erklärung:** Bei /48 müssen die ersten 48 Bit übereinstimmen, das entspricht den ersten drei 16-Bit-Hextets.

---

## Aufgabe 12: ISP-Zuteilung verstehen

**Aufgabe:** Ein ISP hat `2001:db8::/32` erhalten und möchte jedem Kunden ein /56-Präfix zuteilen. Wie viele Kunden kann er bedienen?

**Lösung:**

```
ISP-Block: /32
Kunden-Block: /56

Differenz: 56 - 32 = 24 Bit für Kunden-Zuteilungen

Anzahl Kunden: 2^24 = 16.777.216 Kunden
```

**Zusatz:** Jeder Kunde mit /56 kann dann:
- 2^(64-56) = 2^8 = 256 /64-Subnetze erstellen

**Erklärung:** Die massive Größe des IPv6-Adressraums erlaubt großzügige Zuteilungen ohne Adressknappheit.

---

## Aufgabe 13: Praktische Subnetz-Planung

**Aufgabe:** Plane die Subnetze für ein kleines Rechenzentrum mit dem Präfix `2001:db8:dc00::/48`:
- 10 Web-Server
- 5 Datenbank-Server
- 3 Load Balancer
- Management-Netzwerk
- Backup-Netzwerk

**Lösung:**

```
Basis: 2001:db8:dc00::/48

Web-Server:        2001:db8:dc00:0010::/64
Datenbank-Server:  2001:db8:dc00:0020::/64
Load Balancer:     2001:db8:dc00:0030::/64
Management:        2001:db8:dc00:0100::/64
Backup:            2001:db8:dc00:0200::/64

Beispiel-Adressen für Web-Server:
  WEB-01: 2001:db8:dc00:0010::1
  WEB-02: 2001:db8:dc00:0010::2
  WEB-03: 2001:db8:dc00:0010::3
```

**Erklärung:** 
- Jeder Servertyp bekommt sein eigenes /64-Subnetz
- Selbst mit nur 10 Servern verwendest du ein /64, weil das der Standard ist
- Die großzügige Nummerierung (0010, 0020, etc.) lässt Raum für zukünftige Erweiterungen
- Firewall-Regeln lassen sich einfach pro Subnetz definieren

---

## Tipps für IPv6-Subnetting:

1. **Bleib bei /64**: Verwende fast immer /64 für End-Subnetze (wegen SLAAC)
2. **Plane hierarchisch**: Nutze /48 für Organisationen, /56 für Standorte
3. **Dokumentiere klar**: Verwende sprechende Hexadezimal-Werte (z.B. 0010, 0020)
4. **Sei großzügig**: Der Adressraum ist riesig, keine Notwendigkeit zu sparen
