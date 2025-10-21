# Übungen zu IPv6 - Teil 2

## ICMPv6

### **Übung 1: ICMPv6 Nachrichten**
1. Erkläre den Unterschied zwischen einer **ICMPv6 Echo Request** und einer **ICMPv6 Echo Reply** Nachricht.
2. Welchen Type haben diese beien Nachrichten?
3. Welche Informationen enthält eine **ICMPv6 Time Exceeded** Nachricht und unter welchen Umständen wird sie gesendet?

### **Übung 2: ICMPv6 in der Praxis**
1. Warum ist ICMPv6 wichtig für die Netzwerkinfrastruktur in IPv6-Netzwerken?
2. Was passiert, wenn eine **ICMPv6 Echo Request** gesendet wird und der Zielhost nicht erreichbar ist? Welche Fehlermeldung könnte zurückgegeben werden?
3. Wie unterscheiden sich ICMPv6 und ICMPv4 in Bezug auf ihre Funktionsweise und Bedeutung?

## Neighbor Discovery (ND)**

### **Übung 3: Neighbor Discovery-Protokoll**
1. Beschreibe die grundlegende Funktion des **Neighbor Discovery Protocols (NDP)** in IPv6.
2. Was sind die Unterschiede zwischen **Neighbor Solicitation (NS)** und **Neighbor Advertisement (NA)**? Wann werden diese Nachrichten gesendet?
3. Warum wird die **Link-Local-Adresse** für die Kommunikation zwischen benachbarten Geräten verwendet?

### **Übung 4: Router Advertisement**
1. Erkläre die Rolle des **Router Advertisement (RA)** im Neighbor Discovery-Protokoll. Welche Informationen werden durch Router Advertisements verbreitet?

2. Multiple Choice zu Router Advertisement

#### Frage 1  
Welche Aufgabe hat eine **Router Advertisement**-Nachricht im IPv6-Netzwerk?

- [ ] A) Sie weist einem Host automatisch eine IPv4-Adresse zu.  
- [ ] B) Sie informiert Hosts über Netzparameter wie Präfixe und Standard-Gateway.  
- [ ] C) Sie dient zur Ermittlung der MAC-Adresse eines Nachbarn.  
- [ ] D) Sie überprüft die Erreichbarkeit eines anderen Geräts im Netzwerk.  

#### Frage 2  
Welche **Informationen** kann eine Router Advertisement-Nachricht enthalten?

- [ ] A) Nur die IPv6-Adresse des Routers  
- [ ] B) Netzpräfixe, Router Lifetime, Flags (A/M/O), MTU, DNS-Server  
- [ ] C) Nur den Hostnamen und die MAC-Adresse des Routers  
- [ ] D) Nur die Default-Gateway-Adresse ohne weitere Parameter  

#### Frage 3  
Wann und von wem wird eine **Router Advertisement**-Nachricht versendet?

- [ ] A) Vom Host regelmäßig an alle Router, um ihre Erreichbarkeit zu prüfen  
- [ ] B) Vom Router regelmäßig oder als Antwort auf eine Router Solicitation  
- [ ] C) Vom DNS-Server bei jeder Namensauflösung  
- [ ] D) Vom Switch, wenn ein neuer Port aktiv wird  

#### Bonusfrage  
Welche **Multicast-Adresse** wird für Router Advertisement-Nachrichten typischerweise verwendet?

- [ ] A) ff02::1 (alle Nodes)  
- [ ] B) ff02::2 (alle Router)  
- [ ] C) ff02::5 (alle DHCP-Server)  
- [ ] D) ff02::1:ffXX:XXXX (Solicited-Node Multicast)  

## IPv6 Autokonfiguration**

### **Übung 5: Autokonfiguration und SLAAC**
1. Erkläre den Unterschied zwischen der **stateless autoconfiguration** (SLAAC) und einer **stateful autoconfiguration** in IPv6.
2. Wie wird die **IPv6-Adresse** eines Hosts, der **SLAAC** verwendet, gebildet? Beschreibe kurz die einzelnen Schritte.
3. Was ist der Zweck von **Router Advertisements** (RAs) im Zusammenhang mit der IPv6-Autokonfiguration? Wie beeinflussen diese RAs den Autokonfigurationsprozess?

### **Übung 6: Duplicate Address Detection (DAD)**
1. Erkläre den Zweck und den Ablauf der **Duplicate Address Detection (DAD)** in IPv6.
2. Was passiert, wenn ein Gerät eine IPv6-Adresse verwenden möchte, die bereits im Netzwerk existiert? Wie wird dies erkannt?
3. Warum ist DAD notwendig und welche Risiken werden ohne diesen Mechanismus vermieden?

## IPsec**

### **Übung 7: Grundlagen von IPsec**
1. Was sind die Hauptfunktionen von **IPsec** und welche Sicherheitsdienste bietet es?
2. Erkläre den Unterschied zwischen den **Transportmodus** und **Tunnelmodus** in IPsec. In welchem Szenario wird welcher Modus bevorzugt?
3. Was sind die grundlegenden Schritte bei der Einrichtung einer **IPsec-VPN-Verbindung** zwischen zwei Netzwerken?

### **Übung 8: IPsec Protokolle**
1. Erläutere die Unterschiede zwischen den beiden IPsec-Protokollen **AH (Authentication Header)** und **ESP (Encapsulating Security Payload)**.
2. Welche Funktion hat die **Schlüsselvereinbarung** im Rahmen von IPsec? Welche Protokolle werden zur Schlüsselvereinbarung verwendet?
3. Warum ist die **Authentifizierung** bei IPsec so wichtig, und wie wird sie gewährleistet?

### Übung 9: Kurzantworten
1) Wofür steht **DAD** und was prüft es?
2) Welche ICMPv6-Nachrichten sind am SLAAC-Prozess beteiligt?
3) Warum gibt es in IPv6 **kein ARP** mehr?
4) Welche Aufgabe hat das **M-Flag** in einem Router Advertisement?
5) Was beschreibt **RDNSS** im Kontext von IPv6?

## Multiple Choice

### **10.1** Welche Aussage zu ICMPv6 trifft zu?
- [ ] A): ICMPv6 wird nur für Ping genutzt.
- [ ] B): ICMPv6 ist optional; Routerfunktioniert auch ohne.
- [ ] C): ND basiert auf ICMPv6 und ist für IPv6-Betrieb essenziell.
- [ ] D): ICMPv6 ist identisch zu ICMPv4.

### **10.2** Welche Nachricht signalisiert einem Host Präfix- und Gateway-Informationen?
- [ ] A): Echo Reply  
- [ ] B): Router Advertisement  
- [ ] C): Neighbor Solicitation  
- [ ] D): Redirect

### **10.3** Wofür steht das **O-Flag** in RA?
- [ ] A): Only-Unicast  
- [ ] B): Other Configuration (z. B. DNS per DHCPv6)  
- [ ] C): Offload  
- [ ] D): Optional MTU

### **10.4** Welche Reihenfolge passt zu SLAAC?
- [ ] A): NA → RS → RA → NS  
- [ ] B): RS → RA → DAD (NS/NA) → Adresse bevorzugt  
- [ ] C): RA → RS → Echo  
- [ ] D): NS → NA → Router Redirect

### **10.5** Welche Aussage zu Privacy-Extensions ist richtig?
- [ ] A): Ersetzen die Link-Local-Adresse.  
- [ ] B): Sorgen für rotierende **zusätzliche** globale Adressen für ausgehend.  
- [ ] C): Sind nur auf Routern aktiv.  
- [ ] D): Deaktivieren DAD.
