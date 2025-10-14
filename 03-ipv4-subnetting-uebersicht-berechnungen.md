# Subnetting - Komplette Berechnungsübersicht für IHK-Prüfungen

## 1. Grundlagen

### IP-Adressklassen
| Klasse | Erster Oktett | Standard-Subnetzmaske | CIDR | Netzanteil |
|--------|---------------|----------------------|------|------------|
| A | 1-126 | 255.0.0.0 | /8 | 8 Bit |
| B | 128-191 | 255.128.0.0 | /16 | 16 Bit |
| C | 192-223 | 255.255.255.0 | /24 | 24 Bit |


### Private IP-Bereiche
- Klasse A: 10.0.0.0 - 10.255.255.255 (/8)
- Klasse B: 172.16.0.0 - 172.31.255.255 (/12)
- Klasse C: 192.168.0.0 - 192.168.255.255 (/16)

**Hinweis:** Klassen sind mittlerweile veraltet, hier nur aus "historischen" Gründen erwähnt.

## 2. Wichtige Formeln

### Anzahl möglicher Subnetze
```
Anzahl Subnetze = 2^n
n = Anzahl geliehener Bits für Subnetz
```
### Anzahl nutzbarer Hosts pro Subnetz
```
Anzahl Hosts = 2^h - 2
h = Anzahl Host-Bits
-2 wegen Netzadresse und Broadcast-Adresse
```
### Subnetzgröße (Hosts + Netz + Broadcast)
```
Subnetzgröße = 2^h
```
## 3. Netz in mehrere Subnetze unterteilen

### Methode 1: Nach Anzahl benötigter Subnetze

**Gegeben:** 192.168.1.0/24, benötigt werden 5 Subnetze

**Schritt 1:** Berechne benötigte Subnetz-Bits
```
2^n ≥ 5
2^3 = 8 ≥ 5 → n = 3 Bits
```

**Schritt 2:** Neue Subnetzmaske bestimmen
```
Ursprünglich: /24
Neue Maske: /24 + 3 = /27
Dezimal: 255.255.255.224
```

**Schritt 3:** Anzahl Hosts pro Subnetz
```
Host-Bits: 32 - 27 = 5 Bits
Hosts: 2^5 - 2 = 30 nutzbare Hosts
```

**Schritt 4:** Subnetzgröße (Schrittweite)
```
2^5 = 32 Adressen pro Subnetz
```

**Schritt 5:** Subnetze auflisten
```
Subnetz 1: 192.168.1.0/27   (0-31)
Subnetz 2: 192.168.1.32/27  (32-63)
Subnetz 3: 192.168.1.64/27  (64-95)
Subnetz 4: 192.168.1.96/27  (96-127)
Subnetz 5: 192.168.1.128/27 (128-159)
...
```
Insgesamt wären acht Subnetze möglich.

### Methode 2: Nach Anzahl benötigter Hosts

**Gegeben:** 172.16.0.0/16, benötigt werden 500 Hosts pro Subnetz

**Schritt 1:** Berechne benötigte Host-Bits
```
2^h - 2 ≥ 500
2^9 - 2 = 510 ≥ 500 → h = 9 Bits
```

**Schritt 2:** Neue Subnetzmaske
```
32 - 9 = 23 Bit Netzanteil
Neue Maske: /23
Dezimal: 255.255.254.0
```

**Schritt 3:** Subnetzgröße
```
2^9 = 512 Adressen pro Subnetz
2^9 - 2 = 510 Hosts pro Subnetz
```

**Schritt 4:** Anzahl möglicher Subnetze

Wieviele Bits haben wir uns "geliehen"?
```
Geliehene Bits: 23 - 16 = 7 Bits
Subnetze: 2^7 = 128
```

Wir können also 128 Subnetze bilden.

**Schritt 5:** Subnetze auflisten (Beispiele)

512 = 2 × 256 (also 2 Adressen im 3. Oktett)

-> Schrittweite im 3. Oktett: +2
```
172.16.0.0/23   (0-511)
172.16.2.0/23   (512-1023)
172.16.4.0/23   (1024-1535)
...
```
bzw. detaillierter:
```
Subnetz 1:  172.16.0.0/23
  - Netzadresse: 172.16.0.0
  - Erste IP:    172.16.0.1
  - Letzte IP:   172.16.1.254
  - Broadcast:   172.16.1.255
  - Hosts:       510

Subnetz 2:  172.16.2.0/23
  - Netzadresse: 172.16.2.0
  - Erste IP:    172.16.2.1
  - Letzte IP:   172.16.3.254
  - Broadcast:   172.16.3.255
  - Hosts:       510

Subnetz 3:  172.16.4.0/23
  - Netzadresse: 172.16.4.0
  - Erste IP:    172.16.4.1
  - Letzte IP:   172.16.5.254
  - Broadcast:   172.16.5.255
  - Hosts:       510

... und so weiter bis:

Subnetz 128: 172.16.254.0/23
  - Netzadresse: 172.16.254.0
  - Erste IP:    172.16.254.1
  - Letzte IP:   172.16.255.254
  - Broadcast:   172.16.255.255
  - Hosts:       5
```
## 4. Berechnung wichtiger Adressen

### Für eine gegebene IP-Adresse alle Adressen bestimmen

**Beispiel:** 192.168.50.45/26

**Schritt 1:** Subnetzmaske in Dezimal umrechnen
```
/26 = 255.255.255.192
```

**Schritt 2:** Subnetzgröße berechnen
```
Host-Bits: 32 - 26 = 6
Subnetzgröße: 2^6 = 64 Adressen
Schrittweite im 4. Oktett: 64
```

**Schritt 3:** Netzadresse bestimmen
```
IP: 192.168.50.45
Schrittweite: 64 (0, 64, 128, 192)
45 liegt zwischen 0 und 64
→ Netzadresse: 192.168.50.0
```

**Alternative:** Binär AND-Verknüpfung
```
IP:    11000000.10101000.00110010.00101101
Maske: 11111111.11111111.11111111.11000000
       ────────────────────────────────────
Netz:  11000000.10101000.00110010.00000000
       = 192.168.50.0
```

**Schritt 4:** Broadcast-Adresse
```
Netzadresse: 192.168.50.0
+ (Subnetzgröße - 1): 0 + 63
→ Broadcast: 192.168.50.63
```

**Schritt 5:** Erste nutzbare IP
```
Netzadresse + 1 = 192.168.50.1
```

**Schritt 6:** Letzte nutzbare IP
```
Broadcast - 1 = 192.168.50.62
```

**Schritt 7:** Anzahl nutzbarer Hosts
```
2^6 - 2 = 62 Hosts
```

**Zusammenfassung:**
```
Netzadresse:      192.168.50.0/26
Erste nutzbare:   192.168.50.1
Letzte nutzbare:  192.168.50.62
Broadcast:        192.168.50.63
Nutzbare Hosts:   62
Subnetzmaske:     255.255.255.192
```

## 5. Bestimmung: Zu welchem Netz gehört eine IP?

### Methode 1: Mit Schrittweite

**Gegeben:** IP 10.50.200.75/21 - Welches Netz?

**Schritt 1:** Host-Bits bestimmen
```
32 - 21 = 11 Host-Bits
```

**Schritt 2:** Subnetzgröße
```
2^11 = 2048 Adressen
```

**Schritt 3:** Schrittweite im relevanten Oktett
```
/21 = erste 21 Bits sind Netz
= 2 volle Oktette + 5 Bits im 3. Oktett
Schrittweite im 3. Oktett: 2^(8-5) = 2^3 = 8
```

**Schrittweite bestimmen (generell):**

In einem Oktett mit `r` Einsen von links gilt:
```
2^(8-r)
```
Wobei r die Anzahl der Einsen aus dem relevanten Oktett meint, hier also die Einsen aus dem 3. Oktett:
```
2 volle Oktette (16) + 5 Bits im 3. Oktett = 21
```
**Schritt 4:** Netzadresse finden
```
3. Oktett: 200
Schrittweiten: 0, 8, 16, 24, ..., 192, 200, 208
200 liegt zwischen 200 und 208
→ Netzadresse: 10.50.200.0/21
```
### Methode 2: Binäre AND-Verknüpfung

**Gegeben:** 172.30.145.88/20

```
IP:    172.30.145.88
       10101100.00011110.10010001.01011000

Maske: /20 = 255.255.240.0
       11111111.11111111.11110000.00000000

AND:   10101100.00011110.10010000.00000000
       = 172.30.144.0

→ Netzadresse: 172.30.144.0/20
```

**Weiteres berechnen:**
```
Host-Bits: 12
Subnetzgröße: 2^12 = 4096
Broadcast: 172.30.144.0 + 4095 = 172.30.159.255
Erste IP: 172.30.144.1
Letzte IP: 172.30.159.254
```

## 6. CIDR-Notation und Subnetzmasken

### Magische Zahl (Schrittweite berechnen)
```
Magische Zahl = 256 - Wert des relevanten Oktetts

Beispiel /26: 256 - 192 = 64 (Schrittweite)
Beispiel /20: 256 - 240 = 16 (Schrittweite im 3. Oktett)
```
oder **2^(Anzahl Host-Bits)**:

**Beispiel /26:** 
```
3 volle Oktette, also 24, Rest 2 Netz-Bits im 4. Oktett

Host-Bits gesamt: 32 - 26 = 6 Host-Bits

Schrittweite im interessanten (hier vierten) Oktett:
  2^(8-Netz-Bits in diesem Oktett) = 2^(8-2) = 2^6 = 64

Subnetzmaske: 26 = 24 + 2 -> 256 - 2^(8-2) = 256 - 2^6 = 256 - 64 = 192 -> 255.255.255.192

Beispiel: 192.168.10.199/26 -> 199 liegt im Block 192-255 -> Netz-ID 192.168.10.192, Broadcast 192.168.10.255, Subnetzmaske 255.255.255.192
```
**Beispiel /20:** 
```
2 volle Oktette, also 16, Rest 4 Netz-Bits im 3. Oktett

Host-Bits gesamt: 32 - 20 = 12 Host-Bits

Schrittweite im interessanten (hier dritten) Oktett:
  2^(8-Netz-Bits in diesem Oktett) = 2^(8-4) = 2^4 = 16

Subnetzmaske: 20 = 16 + 4 -> 256 - 2^(8-4) = 256 - 2^4 = 256 - 16 = 240 -> 255.255.240.0

Beispiel: 10.1.37.5/20 ⇒ 37 liegt im Block 32–47 ⇒ Netz-ID 10.1.32.0, Broadcast 10.1.47.255, Subnetzmaske 255.255.240.0
```
**Beispiel /11:** 
```
1 volles Oktette, also 8, Rest 3 Netz-Bits im 2. Oktett

Host-Bits gesamt: 32 - 11 = 21 Host-Bits

Schrittweite im interessanten (hier zweiten) Oktett:
  2^(8-Netz-Bits in diesem Oktett) = 2^(8-3) = 2^5 = 32

Subnetzmaske: 11 = 8 + 3 -> 256 - 2^(8-3) = 256 - 2^5 = 256 - 32 = 224 -> 255.224.0.0

Beispiel: 145.25.59.47/11 -> 25 liegt im Block 0-31 -> Netz-ID 145.0.0.0, Broadcast 145.31.255.255
```
## 7. VLSM (Variable Length Subnet Masking)

### Verschiedene Subnetzgrößen aus einem Netz

**Aufgabe:** Aus 192.168.10.0/24 sollen folgende Subnetze erstellt werden:
- Subnetz A: 50 Hosts
- Subnetz B: 25 Hosts
- Subnetz C: 10 Hosts
- Subnetz D: 5 Hosts

**Lösung (größtes zuerst):**

**Subnetz A: 50 Hosts**
```
Benötigt: 2^h - 2 ≥ 50 → h = 6 (2^6 - 2 = 62)
Maske: /26 (32 - 6 = 26)
Netz: 192.168.10.0/26
Bereich: 192.168.10.0 - 192.168.10.63
```

**Subnetz B: 25 Hosts**
```
Benötigt: 2^h - 2 ≥ 25 → h = 5 (2^5 - 2 = 30)
Maske: /27
Netz: 192.168.10.64/27
Bereich: 192.168.10.64 - 192.168.10.95
```

**Subnetz C: 10 Hosts**
```
Benötigt: 2^h - 2 ≥ 10 → h = 4 (2^4 - 2 = 14)
Maske: /28
Netz: 192.168.10.96/28
Bereich: 192.168.10.96 - 192.168.10.111
```

**Subnetz D: 5 Hosts**
```
Benötigt: 2^h - 2 ≥ 5 → h = 3 (2^3 - 2 = 6)
Maske: /29
Netz: 192.168.10.112/29
Bereich: 192.168.10.112 - 192.168.10.119
```

## 8. Prüfung: Liegen zwei IPs im selben Netz?

**Gegeben:** IP1: 192.168.100.50/26 und IP2: 192.168.100.75/26

**Methode 1: Beide Netzadressen berechnen**
```
IP1: 192.168.100.50/26
Schrittweite: 64
50 liegt zwischen 0 und 64 → Netz: 192.168.100.0/26

IP2: 192.168.100.75/26
75 liegt zwischen 64 und 128 → Netz: 192.168.100.64/26

→ NICHT im selben Netz!
```

**Methode 2: Binär vergleichen**
```
Nur die ersten 26 Bits vergleichen (Netzanteil)

IP1: 11000000.10101000.01100100.00110010
IP2: 11000000.10101000.01100100.01001011
     ↑ gleich bis Bit 25 ↑ unterschiedlich ab Bit 26

→ NICHT im selben Netz!
```

## 9. Schnellübersicht für häufige Aufgaben

### Aufgabe: Netzadresse finden
1. Subnetzmaske in Binär umwandeln oder Schrittweite berechnen (`2^(8 - Netz-Bits-im-relevanten-Oktett)`)
2. IP AND Maske ODER mit Schrittweite arbeiten
3. Ergebnis ist Netzadresse

### Aufgabe: Schrittweite berechnen
Schrittweite im interessanten (hier zweiten) Oktett:
  2^(8-Netz-Bits in diesem Oktett) = 2^(8-3) = 2^5 = 32

### Aufgabe: Broadcast finden
1. Netzadresse bestimmen
2. Subnetzgröße berechnen (2^Host-Bits)
3. Netzadresse + Subnetzgröße - 1

### Aufgabe: Anzahl Subnetze
1. Neue Maske - alte Maske = geliehene Bits
2. 2^geliehene Bits = Anzahl Subnetze

### Aufgabe: Hosts pro Subnetz
1. 32 - Präfixlänge = Host-Bits
2. 2^Host-Bits - 2 = nutzbare Hosts

### Aufgabe: Schrittweite ermitteln
Schrittweite im relevanten Oktett (da wo die Einsen zu Nullen kippen):
```
2^(8 - Netz-Bits-im-relevanten-Oktett)
```
### Aufgabe: Subnetzmaske aus CIDR ermitteln
1. Masken-Oktett ermitteln:
```
256 - 2^(8 - Netz-Bits-im-relevanten-Oktett)
```
2. Zusammen bauen: Vor dem relevanten Oktett `255`, danach `0`

## 10. Übungsbeispiele mit Lösungen

### Beispiel 1
**Aufgabe:** Teile 10.0.0.0/8 in Subnetze mit je 1000 Hosts

**Lösung:**
```
Host-Bits: 2^h - 2 ≥ 1000 → h = 10 (2^10 - 2 = 1022)
Neue Maske: 32 - 10 = /22
Dezimal: 255.255.252.0
Erste Subnetze:
- 10.0.0.0/22
- 10.0.4.0/22
- 10.0.8.0/22
```

### Beispiel 2
**Aufgabe:** Zu welchem Netz gehört 172.20.95.200/19?

**Lösung:**
```
Host-Bits: 32 - 19 = 13
Schrittweite im 3. Oktett: 2^(8-3) = 32
95 ÷ 32 = 2 Rest 31
→ 2 × 32 = 64
Netzadresse: 172.20.64.0/19
Broadcast: 172.20.95.255
Erste IP: 172.20.64.1
Letzte IP: 172.20.95.254
```

### Beispiel 3
**Aufgabe:** Erstelle 4 Subnetze aus 192.168.5.0/24

**Lösung:**
```
Benötigte Bits: 2^n ≥ 4 → n = 2
Neue Maske: /26
Subnetze:
1. 192.168.5.0/26   (Hosts: .1 - .62)
2. 192.168.5.64/26  (Hosts: .65 - .126)
3. 192.168.5.128/26 (Hosts: .129 - .190)
4. 192.168.5.192/26 (Hosts: .193 - .254)
```

