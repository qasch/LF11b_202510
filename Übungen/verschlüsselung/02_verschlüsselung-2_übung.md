# Fragen zu: Kryptographie, SSL/TLS, GPG, Zertifikate & CAs (mit Lösungen)


## 1) Symmetrische vs. asymmetrische Verschlüsselung

1. Worin liegt der Hauptunterschied zwischen symmetrischer und asymmetrischer Verschlüsselung?

Lösung: Symmetrisch nutzt denselben Schlüssel zum Ver- und Entschlüsseln; asymmetrisch nutzt Schlüsselpaare (privat/öffentlich).

2. Nenne zwei typische Algorithmen für symmetrische Verschlüsselung.

Lösung: AES, ChaCha20 (auch: 3DES – veraltet)

3. Nenne zwei typische Algorithmen für asymmetrische Kryptografie.

Lösung: RSA, ECDSA / ECDH

4. Warum ist asymmetrische Kryptografie bei großen Datenmengen in der Praxis oft nicht das Mittel der Wahl?

Lösung: Asymmetrische Verfahren sind deutlich langsamer und rechenintensiver; deshalb wird in der Regel asymmetrisch nur für Schlüsselaustausch und Signaturen genutzt, die eigentliche Datenverschlüsselung erfolgt symmetrisch.

5. Wofür wird asymmetrische Kryptografie in der Praxis meist eingesetzt? Nenne mindestens zwei Einsatzzwecke.

Lösung: Authentisierung (digitale Signaturen), Schlüsselaustausch (z. B. ECDHE), seltener direkte Nachrichtenverschlüsselung.

6. Welcher Vorteil ergibt sich aus Forward Secrecy?

Lösung: Selbst wenn der langfristige private Schlüssel kompromittiert wird, bleiben alte Sitzungen vertraulich, weil für jede Sitzung ein ephemerer Schlüssel verwendet wird.

---

## 2) SSL & TLS – Grundlagen

7. Wofür stehen die Abkürzungen SSL und TLS?

Lösung: SSL = Secure Sockets Layer; TLS = Transport Layer Security.

8. Warum ist SSL veraltet und welche TLS-Versionen gelten heute als Standard?

Lösung: SSL (z. B. SSL 2.0/3.0) hat bekannte Schwachstellen (z. B. POODLE) und wird nicht mehr verwendet. Heutiger Standard sind TLS 1.2 und TLS 1.3.

9. Welche TLS-Versionen sollten heute mindestens aktiviert sein – und warum ist die Auswahl nicht nur eine Frage der Rückwärtskompatibilität?

Lösung: TLS 1.2 und 1.3; ältere Versionen sind unsicher oder unterstützen keine modernen Cipher/Forward Secrecy; Kompatibilität mit alten Clients kann Sicherheitsrisiken verursachen.

10. Nenne zwei Standard-Ports für implicit TLS bei E‑Mail-Protokollen.

Lösung: SMTPS 465, IMAPS 993 (auch POP3S 995)

11. Beschreibe technisch korrekt, was HTTPS ist.

Lösung: HTTP über TLS (historisch: "über SSL") üblicherweise auf Port 443.

12. Wozu dient HSTS im Web und welche Einschränkungen hat es?

Lösung: HSTS weist Browser an, eine Website nur per HTTPS aufzurufen, verhindert HTTPS-Strip-Angriffe. Einschränkungen: wirkt erst nach erstem sicheren Besuch (außer bei Preload); erfordert saubere HTTPS-Konfiguration.

---

## 3) TLS-Handshake

13. Nenne zwei Hauptaufgaben des TLS-Handshakes.

Lösung: Authentisierung des Servers (optional auch des Clients) und Aushandlung/Erzeugung gemeinsamer Sitzungsschlüssel.

14. Welche Schlüsselaustausch-Verfahren bieten Forward Secrecy? Begründe kurz.

Lösung: (EC)DHE (z. B. ECDHE mit X25519) liefert ephemere Schlüssel für jede Sitzung, damit alte Sitzungen nicht bei Schlüsselkompromittierung entschlüsselbar sind.

15. Was wurde in TLS 1.3 in Bezug auf veraltete Ciphers und Key-Exchange geändert und warum?

Lösung: TLS 1.3 entfernt unsichere/obsolete Ciphers und veraltete Key-Exchange-Methoden (z. B. RSA-Key-Exchange) und vereinfacht den Handshake, um Sicherheit und Performance zu verbessern.

16. Welche Rolle spielt die digitale Signatur des Servers im Handshake?

Lösung: Sie beweist, dass der Server im Besitz des privaten Schlüssels zum Zertifikat ist, und schützt die ausgehandelten Parameter vor Man-in-the-Middle.

17. Was passiert nach Abschluss des Handshakes?

Lösung: Alle Anwendungsdaten werden symmetrisch mit abgeleiteten Sitzungsschlüsseln (AEAD) verschlüsselt und mit Integritätsprüfungen versehen.

---

## 4) Zertifikate & Certificate Authorities (CAs)

18. Was ist ein X.509-Zertifikat und welche Informationen enthält es mindestens?

Lösung: Ein digital signiertes Dokument, das einen Public Key mit Identitätsdaten (z. B. Domainname), Aussteller, Gültigkeitszeitraum und Signatur verbindet.

19. Welche Prüfungen führt ein Browser typischerweise bei einem Zertifikat durch? Nenne mehrere Prüfpunkte.

Lösung: Gültigkeitszeitraum, Zertifikatskette bis zu einer vertrauten Root-CA, Sperrstatus (OCSP/CRL), Hostname-Match, Ev. zusätzliche Extentions (SAN).

20. Was ist eine Root-CA und welche Risiken sind mit ihr verbunden?

Lösung: Eine Root-CA ist selbstsigniert und dient als Vertrauensanker; Risiken: Kompromittierung führt zu massiver Vertrauensverletzung, Missausstellungen durch eine Root-CA sind besonders kritisch.

21. Wozu dienen Zwischenzertifikate (Intermediate CAs)?

Lösung: Sie schichten Vertrauen unterhalb der Root-CA und erlauben es, Root-Schlüssel offline zu halten; reduzieren das Risiko direkter Root-Nutzung.

22. Was ist OCSP Stapling und welchen praktischen Vorteil bietet es?

Lösung: Der Server liefert dem Client den OCSP-Antwortstatus des Zertifikats mit; so werden zusätzliche Online-Anfragen des Clients an die CA vermieden und die Performance/Verfügbarkeit verbessert.

23. Nenne gängige Zertifikatstypen nach Validierungsgrad und beschreibe kurz den Unterschied.

Lösung: DV (Domain Validation) prüft nur Domainbesitz; OV (Organization Validation) prüft organisatorische Informationen; EV (Extended Validation) erfordert umfangreiche Prüfungen der Organisation.

24. Warum wird oft noch der Begriff "SSL-Zertifikat" verwendet, obwohl technisch TLS eingesetzt wird?

Lösung: Historische/marketingbedingte Namenskonvention; technisch ist das Zertifikat ein X.509-Zertifikat, das für TLS verwendet wird.

---

## 5) GPG / OpenPGP

25. Wofür steht GPG und wofür wird es genutzt?

Lösung: GNU Privacy Guard; Implementierung von OpenPGP zur E‑Mail-Verschlüsselung, Dateiverschlüsselung und digitalen Signaturen.

26. Wie wird Vertrauen bei OpenPGP typischerweise aufgebaut?

Lösung: Durch ein Web of Trust, also gegenseitiges Signieren von Schlüsseln; alternative Modelle gibt es, sind aber weniger verbreitet.

27. Was kann man mit GPG an Dateien durchführen?

Lösung: Signieren (Authentizität/Integrität) und Verschlüsseln (Vertraulichkeit).

28. Nenne kurz die semantischen Ziele der GPG-Kommandos für Signieren und Verschlüsseln.

Lösung: sign/verify für Integrität und Authentizität; encrypt/decrypt für Vertraulichkeit.

---

## 6) Praxis & Sicherheit

29. Welche Kombination ist für Webserver heute Best Practice? Begründe kurz.

Lösung: TLS 1.2/1.3, ECDHE, AEAD-Ciphers wie AES-GCM oder ChaCha20-Poly1305, HSTS, OCSP Stapling.

30. Beschreibe, wie ein Browser typischerweise auf ein fehlerhaftes oder gefälschtes Zertifikat reagiert, und welche Rolle Nutzerverhalten dabei spielt.

Lösung: Browser zeigen Zertifikatswarnungen und blockieren in der Regel die Verbindung. Wenn Nutzer Warnungen ignorieren oder Admins bösartige Root-CAs installieren, können MITM-Angriffe unbemerkt bleiben.

31. Was unterscheidet STARTTLS von implicit TLS?

Lösung: STARTTLS startet mit einer unverschlüsselten Verbindung, die per Upgrade-Befehl auf TLS gehoben wird; implicit TLS verwendet von Anfang an TLS auf einem separaten Port.

32. Welche Vor- und Nachteile hat Zertifikats-Pinning?

Lösung: Vorteil: reduziert Risiko falscher Zertifikate; Nachteil: Betriebsaufwand, mögliche Ausfälle bei nicht rechtzeitiger Pin-Aktualisierung.

33. Nenne Indikatoren, die auf ein problematisches Zertifikat hindeuten können (mehrere Beispiele).

Lösung: Falscher Hostname, unbekannte Aussteller-CA, abgelaufener Zeitraum, fehlerhafte Kette, OCSP-Fehler.

34. Was sollte getan werden, wenn ein privater Schlüssel kompromittiert ist?

Lösung: Zertifikat unverzüglich widerrufen, neuen Schlüssel erzeugen und Zertifikat ersetzen; betroffene Sessions neu ausstellen; Ursache analysieren.

35. Warum werden Anwendungsdaten nach dem TLS-Handshake symmetrisch verschlüsselt?

Lösung: Symmetrische Verfahren sind deutlich effizienter für große Datenmengen; asymmetrische Kryptografie wird primär für Schlüsselaustausch und Signaturen verwendet.

---

## 7) Bonus-Aufgaben (optional, mit Lösungsskizze)

36. Dein Unternehmen betreibt eine öffentliche Website. Nenne drei konkrete Maßnahmen zur Absicherung gegen HTTPS-Strip/MITM.

Lösungsskizze: HSTS (+ Preload), Erzwingen von HTTPS-Redirects, nur TLS 1.2/1.3 mit ECDHE und AEAD-Ciphers, OCSP Stapling, CT-Monitoring, sichere Schlüsselablage (HSM), Monitoring.

37. Ein Kunde fordert "höchste Sicherheit, aber auch Kompatibilität mit sehr alten Geräten". Welche Vorgehensweise empfiehlst du?

Lösung: TLS 1.2/1.3 aktivieren; ältere Protokolle nicht aktivieren. Falls unbedingt erforderlich, Legacy-Endpunkte separat betreiben und isolieren, aber klar kommunizieren, dass dies ein erhöhtes Risiko darstellt.

38. Erkläre Certificate Transparency (CT) in einem Satz.

Lösung: Öffentliche, überprüfbare Logbücher für ausgestellte Zertifikate, die Missausstellungen sichtbar machen.

39. Ein EV-Zertifikat garantiert, dass eine Website keine Malware verteilt. Diskutiere kurz.

Lösung: Nein — EV erhöht Identitätsprüfung, garantiert aber nicht, dass der Betreiber sicher oder malwarefrei ist.

40. Welche Rolle hat ein CSR (Certificate Signing Request)?

Lösung: Enthält den öffentlichen Schlüssel und Identitätsdaten; wird bei einer CA eingereicht, um ein signiertes Zertifikat zu erhalten.
