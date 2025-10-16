# Fragen zu: Kryptographie, SSL/TLS, GPG, Zertifikate & CAs


## 1) Symmetrische vs. asymmetrische Verschlüsselung

1. Nenne zwei typische Algorithmen für symmetrische Verschlüsselung.

2. Nenne zwei typische Algorithmen für asymmetrische Kryptografie.

3. Warum ist asymmetrische Kryptografie bei großen Datenmengen in der Praxis oft nicht das Mittel der Wahl?

4. Wofür wird asymmetrische Kryptografie in der Praxis meist eingesetzt? Nenne mindestens zwei Einsatzzwecke.

5. Welcher Vorteil ergibt sich aus Forward Secrecy?

## 2) SSL & TLS – Grundlagen

6. Wofür stehen die Abkürzungen SSL und TLS?

7. Warum ist SSL veraltet und welche TLS-Versionen gelten heute als Standard?

8. Welche TLS-Versionen sollten heute mindestens aktiviert sein – und sollten von einem Server generell noch alle älteren Versionen unterstützt werden?

9. Nenne zwei Standard-Ports für *implicit* TLS bei E‑Mail-Protokollen. Welches "andere" TLS gibt es hier noch?

10. Beschreibe technisch korrekt, was HTTPS ist. Ist es *HTTP over SSL*?

11. Wozu dient HSTS im Web und welche Einschränkungen hat es?

## 3) TLS-Handshake

12. Nenne zwei Hauptaufgaben des TLS-Handshakes.

13. Welche Schlüsselaustausch-Verfahren bieten Forward Secrecy?

14. Was wurde in TLS 1.3 in Bezug auf veraltete Ciphers und Key-Exchange geändert und warum?

15. Welche Rolle spielt die digitale Signatur des Servers im Handshake?

16. Welche Rolle spielen die *Finished - Nachrichten* zum Abschluss des TLS-Handshakes von Client und Server?

## 4) Zertifikate & Certificate Authorities (CAs)

17. Was ist ein X.509-Zertifikat und welche Informationen enthält es mindestens?

18. Was ist eine Root-CA und welche Risiken sind mit ihr verbunden?

19. Wozu dienen Zwischenzertifikate (Intermediate CAs)?

20. Was ist OCSP Stapling und welchen praktischen Vorteil bietet es?

## 5) GPG / OpenPGP

21. Wofür steht GPG und wofür wird es genutzt?

22. Wie wird Vertrauen bei OpenPGP typischerweise aufgebaut?

23. Was kann man mit GPG an Dateien durchführen?

## 6) Praxis & Sicherheit

24. Beschreibe, wie ein Browser typischerweise auf ein fehlerhaftes oder gefälschtes Zertifikat reagiert, und welche Rolle Nutzerverhalten dabei spielt.

25. Was unterscheidet STARTTLS von implicit TLS?

26. Nenne Indikatoren, die auf ein problematisches Zertifikat hindeuten können (mehrere Beispiele).

27. Dein Unternehmen betreibt eine öffentliche Website. Nenne drei konkrete Maßnahmen zur Absicherung gegen HTTPS-Strip/MITM.
