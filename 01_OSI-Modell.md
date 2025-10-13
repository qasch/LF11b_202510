# OSI-Modell

Das **OSI-Referenzmodell** (Open Systems Interconnection Model) ist ein theoretisches Schichtenmodell, das beschreibt, wie Daten in einem Netzwerk übertragen werden. Es teilt die Netzwerkkommunikation in **sieben Schichten** auf, wobei jede Schicht spezifische Aufgaben hat und mit der darüber- oder darunterliegenden Schicht interagiert. Ziel ist es, die Netzwerkkommunikation besser zu verstehen, zu standardisieren und zu modularisieren.

Hilfreich ist das Verständnis unter anderem bei der Fehlersuche bzw. erlangen wir dadurch ein grundlegendes Verständnis über den Ablauf der Datenübertragung.

## Die sieben Schichten des OSI-Modells

![OSI-Modell](img/osi-modell.png)

### 1. Schicht 7: Anwendungsschicht (Application Layer)
- **Funktion**: Schnittstelle für Benutzer und Anwendungen, die auf Netzwerke zugreifen.
- **Aufgabe**: Bereitstellen von Diensten wie Dateiübertragung, E-Mail, Webzugriff.
- **Beispiele**: HTTP, FTP, SMTP, DNS.

### 2. Schicht 6: Darstellungsschicht (Presentation Layer)
- **Funktion**: Übersetzung, Verschlüsselung und Kompression von Daten.
- **Aufgabe**: Daten in ein Format bringen, das vom Empfänger verstanden werden kann.
- **Beispiele**: SSL/TLS (für Verschlüsselung), Textcodierung (z. B. UTF-8, ASCII).

### 3. Schicht 5: Sitzungsschicht (Session Layer)
- **Funktion**: Steuerung und Synchronisierung von Kommunikationssitzungen.
- **Aufgabe**: Aufbau, Verwaltung und Beendigung von Sitzungen zwischen Endsystemen.
- **Beispiele**: Sitzungssteuerung in RPCs (Remote Procedure Calls).

### 4. Schicht 4: Transportschicht (Transport Layer)
- **Funktion**: Zuverlässige oder unzuverlässige Datenübertragung.
- **Aufgabe**: Segmentierung, Fehlerkorrektur und Flusskontrolle.
- **Protokolle**:
  - **TCP** (Transmission Control Protocol): Zuverlässig, verbindungsorientiert.
  - **UDP** (User Datagram Protocol): Unzuverlässig, verbindungslos.

### 5. Schicht 3: Netzwerkschicht (Network Layer)
- **Funktion**: Routing und Adressierung von Datenpaketen.
- **Aufgabe**: Daten von der Quelle zum Ziel transportieren, unabhängig von der physischen Verbindung.
- **Protokolle**: IP (IPv4, IPv6), ICMP, OSPF.

### 6. Schicht 2: Sicherungsschicht (Data Link Layer)
- **Funktion**: Bereitstellung einer fehlerfreien Übertragung über ein einzelnes physisches Netzwerk.
- **Aufgabe**: Fehlererkennung, Fehlerkorrektur und Steuerung des Datenflusses.
- **Protokolle**: Ethernet, Wi-Fi (802.11), PPP.

### 7. Schicht 1: Physische Schicht (Physical Layer)
- **Funktion**: Übertragung der Rohdaten (Bits) über ein physisches Medium.
- **Aufgabe**: Definition von Kabeln, Signalen, Frequenzen und physischer Verbindung.
- **Beispiele**: Netzwerkkabel, elektrische Signale, Lichtimpulse (bei Glasfaser).

## Encapsulation

**Encapsulation** (Einbettung) beschreibt den Prozess, bei dem Daten in einem Netzwerkprotokoll in eine **Hülle aus Steuerinformationen** (Header) eingepackt werden, bevor sie zur Übertragung an die nächste Schicht weitergeleitet werden. Jede Schicht des **OSI-Referenzmodells** fügt dabei ihre eigenen spezifischen Informationen hinzu. Diese zusätzlichen Informationen ermöglichen es, dass die Daten korrekt übertragen, adressiert, geroutet und interpretiert werden können.

![Encapsulaiont](img/encapsulation.png)

### Ablauf der Encapsulation

1. **Start in der Anwendungsschicht (Schicht 7)**:  
   Die Rohdaten (z. B. ein HTTP-Request) werden generiert und an die nächste Schicht weitergegeben.

2. **Hinzufügen von Steuerinformationen**:  
   Jede Schicht fügt ihrer Funktion entsprechende Steuerinformationen in Form eines **Headers** hinzu. In einigen Fällen (z. B. Schicht 2) kann auch ein **Trailer** hinzugefügt werden.

3. **Weitergabe an die nächste Schicht**:  
   Die eingekapselten Daten werden als "Payload" (Nutzlast) an die darunterliegende Schicht weitergegeben, die wiederum eigene Informationen hinzufügt.

4. **Schicht 1: Physikalische Übertragung**:  
   Auf der untersten Schicht (Schicht 1) werden die vollständig enkapsulierten Daten als elektrische Signale, Lichtimpulse oder Funkwellen über das physische Medium übertragen.

### Beispiel für Encapsulation (HTTP über TCP/IP)

Ein HTTP-Request, der über das Internet gesendet wird, durchläuft folgende Schichten:

1. **Anwendungsschicht (HTTP)**:  
   Der Request wird als Datenstrom erzeugt (z. B. `GET /index.html HTTP/1.1`).

   Wir fassen hierbei die Schichten 5 bis 7 zusammen.

2. **Transportschicht (TCP)**:  
   Ein **TCP-Header** wird hinzugefügt, der Informationen wie Quell- und Zielport enthält. Die Daten heißen nun **TCP-Segment**.

3. **Netzwerkschicht (IP)**:  
   Ein **IP-Header** wird hinzugefügt, der Quell- und Ziel-IP-Adressen enthält. Die Daten heißen jetzt **IP-Paket**.

4. **Sicherungsschicht (Ethernet)**:  
   Ein **Ethernet-Header** (z. B. MAC-Adressen) und ein **Trailer** (z. B. für Fehlererkennung) werden hinzugefügt. Die Daten heißen nun **Ethernet-Frame**.

5. **Physische Schicht**:  
   Die Frames werden in Bits umgewandelt und über das physische Medium übertragen.

### Dekapsulation

Auf der Empfängerseite wird der Prozess umgekehrt (**Decapsulation**):
1. Die physische Schicht empfängt die Bits und wandelt sie zurück in Frames.
2. Jede Schicht entfernt ihre Header und interpretiert die enthaltenen Informationen.
3. Schließlich erhält die Anwendungsschicht die ursprünglichen Daten.

### Vorteile der Encapsulation

1. **Modularität**:  
   Jede Schicht kümmert sich nur um ihre spezifischen Aufgaben, unabhängig von anderen Schichten.

2. **Flexibilität**:  
   Daten können über verschiedene Netzwerke und Protokolle hinweg übertragen werden.

3. **Fehlertoleranz**:  
   Fehler in einer Schicht haben keinen direkten Einfluss auf andere Schichten.

4. **Standardisierung**:  
   Encapsulation ermöglicht die Interoperabilität zwischen Geräten und Anwendungen verschiedener Hersteller.

## PDU
