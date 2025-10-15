# Übungen zu IPv6 - Teil 1

## Übung 1: Grundlagen von IPv6
1. Was ist der Hauptunterschied zwischen IPv4 und IPv6?
2. Warum wurde IPv6 eingeführt, und welche Vorteile bietet es gegenüber IPv4?
3. Eine IPv6-Adresse besteht aus 128 Bit. Wie viele mögliche Adressen können damit erstellt werden?
4. Wie ist eine typische IPv6-Adresse aufgebaut? Nenne die Anzahl der Blöcke und die Anzahl der Bits pro Block.

## Übung 2: Darstellung von IPv6-Adressen
### Gegeben: IPv6-Adresse

2001:0db8:0000:0000:0000:ff00:0042:8329

1. Kürze die Adresse so weit wie möglich nach den IPv6-Darstellungsregeln.
2. Wie lautet die Adresse, wenn sie in Dualform (binär) dargestellt wird? Schreibe nur die ersten zwei Blöcke vollständig aus.

## Übung 3: Präfix
### Gegeben: IPv6-Adresse
2001:db8:abcd:0012::/64

1. Was bedeutet das Präfix `/64` bzw. Wie viele Bits werden für das Netzwerk verwendet, und wie viele für Hosts?
2. Gibt es auch andere Präfixe? 
3. Informiere dich generell (kurz, nicht detailliert/intensiv) über *Subnetting* in IPv6 und was das mit dem Präfix zu tun hat.

## Übung 4: Adresstypen
1. Welche Arten von IPv6-Adressen gibt es? Beschreibe kurz die Eigenschaften/Unterschiede zwischen:
   - Global Unicast
   - Multicast
   - Anycast
   - Link Local
   - Unique Local
2. Welche der folgenden Adressen ist eine **Link-Local-Adresse**, und wie erkennst du das?
   - fe80::1  
   - 2001:db8::1  
   - ff02::1  
   - ::1  

## Übung 5: IPv6 Header
1. Beschreibe kurz, was ein *Extension Header* ist.
2. Was ist das Äquivalent zum *TTL Feld* des IPv4 Headers im IPv6 Header?
3. Welche Länge hat ein IPv4 Header und welche ein IPv6 Header?

## Übung 6: Autokonfiguration
1. Was versteht man generell unter *Autokonfiguration* in IPv6 und welche Verfahren gibt es hier?
2. Beschreibe kurz den Ablauf der *Autokonfiguration* mit SLAAC unter IPv6.

## Übung 7: IPv6 im Alltag
1. Welche IPv6-Adresse wird verwendet, wenn du lokal mit deinem Gerät kommunizieren möchtest? (Hinweis: Äquivalent zu `127.0.0.1` in IPv4)
2. Dein Router hat die Adresse `2001:db8:abcd:1::1`. Dein PC hat die Adresse `2001:db8:abcd:1::2`.  
   Kann dein PC direkt mit dem Router kommunizieren? Begründe deine Antwort.
3. Ein PC mit der Adresse `fe80::1` sendet ein Paket an die Multicast-Adresse `ff02::1`.  
   Wer empfängt dieses Paket, und warum?

