# IPv6 Subnetting - Übungsaufgaben

## Aufgabe 1: Grundlegendes Subnetting

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:cafe::/48` erhalten und sollst daraus 5 /64-Subnetze für folgende Bereiche erstellen:
- Management-VLAN
- Benutzer-VLAN
- Server-VLAN
- Gäste-VLAN
- DMZ

## Aufgabe 2: Grundlegendes Subnetting - Variante 2

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:beef::/48` erhalten und sollst daraus 6 /64-Subnetze für folgende Bereiche erstellen:
- IoT-Geräte
- Drucker-VLAN
- Telefonie-VLAN
- Video-Konferenz
- Entwicklung
- Testing

## Aufgabe 3: Grundlegendes Subnetting - Variante 3

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:1234::/48` erhalten und sollst daraus 7 /64-Subnetze für folgende Bereiche erstellen:
- Produktions-Datenbank
- Staging-Datenbank
- Test-Datenbank
- Web-Frontend
- API-Server
- Cache-Server
- Monitoring

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

## Aufgabe 5: /62-Subnetting für Server-Cluster

**Aufgabe:** Du hast das IPv6-Präfix `2001:db8:5000::/48` erhalten. Erstelle 4 /62-Subnetze für verschiedene Server-Cluster:
- Kubernetes-Cluster A
- Kubernetes-Cluster B
- Docker-Swarm-Cluster
- VMware-Cluster

Gib für jeden Cluster auch die enthaltenen /64-Subnetze an.

## Aufgabe 6: /62-Subnetting für Multi-Site-Deployment

**Aufgabe:** Ein Unternehmen hat das Präfix `2001:db8:c000::/48` und möchte für 6 Zweigstellen jeweils ein /62-Subnetz bereitstellen. Jede Zweigstelle benötigt:
- Ein Benutzer-VLAN
- Ein Server-VLAN
- Ein VoIP-VLAN
- Ein Management-VLAN

Erstelle die Subnetz-Struktur für alle 6 Standorte.

## Aufgabe 7: Berechnung verfügbarer Subnetze

**Aufgabe:** Wie viele /64-Subnetze kannst du aus einem /56-Präfix erstellen? Wie viele aus einem /52-Präfix?

## Aufgabe 8: Adressbereiche identifizieren

**Aufgabe:** Gegeben ist das Subnetz `2001:db8:ab00:0040::/64`. 
- Wie lautet die erste verwendbare Host-Adresse?
- Wie lautet die letzte verwendbare Host-Adresse?
- Welches ist das nächste Subnetz?

## Aufgabe 9: Hierarchisches Subnetting

**Aufgabe:** Ein Unternehmen hat `2001:db8:1000::/48` erhalten und hat 3 Standorte:
- Hauptsitz (benötigt 100 Subnetze)
- Zweigstelle A (benötigt 20 Subnetze)
- Zweigstelle B (benötigt 10 Subnetze)

Erstelle eine hierarchische Subnetz-Struktur.

## Aufgabe 10: Variable Length Subnet Masks (VLSM)

**Aufgabe:** Du hast `2001:db8:5000::/48` und benötigst:
- 1 Subnetz mit /60 für Point-to-Point-Links (16 /64-Subnetze)
- 1 Subnetz mit /62 für Server (4 /64-Subnetze)
- Mehrere normale /64-Subnetze

Plane die Struktur.

## Aufgabe 11: Subnetz-Zugehörigkeit prüfen

**Aufgabe:** Gehört die Adresse `2001:db8:a:b:c:d:e:f` zum Subnetz `2001:db8:a::/48`?

## Aufgabe 12: ISP-Zuteilung verstehen

**Aufgabe:** Ein ISP hat `2001:db8::/32` erhalten und möchte jedem Kunden ein /56-Präfix zuteilen. Wie viele Kunden kann er bedienen?

## Aufgabe 13: Praktische Subnetz-Planung

**Aufgabe:** Plane die Subnetze für ein kleines Rechenzentrum mit dem Präfix `2001:db8:dc00::/48`:
- 10 Web-Server
- 5 Datenbank-Server
- 3 Load Balancer
- Management-Netzwerk
- Backup-Netzwerk

