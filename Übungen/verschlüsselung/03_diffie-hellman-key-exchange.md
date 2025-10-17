# Arbeitsblatt: Diffie-Hellman-Schlüsselaustausch (vereinfacht)

## Ziel

Alice und Bob möchten **denselben geheimen Schlüssel** berechnen, ohne ihn über das Netzwerk zu übertragen. Dazu verwenden sie das **Diffie-Hellman-Verfahren**.

Das Prinzip:  

- Beide einigen sich auf **öffentliche Werte**: eine Primzahl `p` und eine Basis `g`.  
- Jede Seite wählt eine **geheime Zahl** (privater Schlüssel).  
- Aus dieser berechnet sie einen **öffentlichen Wert**, den sie austauschen.  
- Beide können daraus **unabhängig denselben geheimen Schlüssel** berechnen.

---

## Beispiel

| Symbol | Bedeutung | Wert |
|---------|------------|------|
| `p` | öffentliche Primzahl | 23 |
| `g` | öffentlicher Generator | 5 |

### Schritt 1: Alice wählt ihr Geheimnis
Alice wählt `a = 6`  
und berechnet ihren öffentlichen Wert:
```
A = g^a mod p = 5^6 mod 23 = 15625 mod 23 = 8
```

### Schritt 2: Bob wählt sein Geheimnis
Bob wählt `b = 15`  
und berechnet seinen öffentlichen Wert:
```
B = g^b mod p = 5^15 mod 23 = 19
```

### Schritt 3: Austausch
Beide schicken ihre öffentlichen Werte:
- Alice → Bob: `A = 8`
- Bob → Alice: `B = 19`

### Schritt 4: Gemeinsamer geheimer Schlüssel

Alice berechnet:
```
K = B^a mod p = 19^6 mod 23 = 2
```

Bob berechnet:
```
K = A^b mod p = 8^15 mod 23 = 2
```
Beide haben denselben geheimen Schlüssel: **2**

---

## Warum ist das sicher?

Ein Angreifer kennt nur:
```
p = 23, g = 5, A = 8, B = 19
```

Aber nicht `a` oder `b`.  

Um `a` zu finden, müsste er das **diskrete Logarithmusproblem** lösen:

> Welche Zahl a ergibt 5^a mod 23 = 8?

Das ist bei kleinen Zahlen vielleicht noch möglich - bei echten Schlüsseln (z. B. 256 Bit) aber **praktisch unmöglich**.

---

## Aufgaben

### Aufgabe 1
Gegeben:
```
p = 17, g = 3
Alice: a = 5
Bob: b = 9
```
  
Berechne:
1. Alices öffentlichen Wert `A = g^a mod p`
2. Bobs öffentlichen Wert `B = g^b mod p`
3. Den gemeinsamen geheimen Schlüssel

---

### Aufgabe 2
Erfinde eigene Werte:
1. Wähle eine kleine Primzahl `p` (z. B. zwischen 11 und 31)  
2. Wähle eine Basis `g` (kleine Zahl, z. B. 2–10)  
3. Wähle zwei geheime Zahlen `a` und `b`  
4. Berechne:
   - `A = g^a mod p`
   - `B = g^b mod p`
   - gemeinsamen Schlüssel: `A^b mod p` bzw. `B^a mod p`

Vergleiche das Ergebnis mit einer weiteren Person aus dem Kurs - ihr beide solltet denselben Schlüssel haben.

---

## Bonus (optional)

Überlege:
- Warum kann ein Angreifer, der A und B kennt, **nicht** den geheimen Schlüssel berechnen?
- Was bedeutet das Wort *„ephemeral“* in **ECDHE**?

---

**Hinweis:**  
Dieses vereinfachte Beispiel nutzt kleine Zahlen. In der echten Kryptografie sind `p` und die geheimen Schlüssel viele hundert Bits lang - dadurch ist die Rückrechnung praktisch unmöglich.
