# Aufg. 1

**Link zur Repository mit dem Code:** https://github.com/CyZeTLC/prog2_zoo

---

# Aufg. 2

**Siehe Code:** https://github.com/CyZeTLC/prog2_zoo/blob/master/src/main/java/de/cyzetlc/hsbi/zoo/Zoo.java

**Ausgabe in der Konsole:**

```text
Log-Level: WARNING
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo findEnclosureByName
WARNUNG: findEnclosureByName: kein Gehege mit dem Namen 'Vogelhaus' gefunden

Log-Level: INFO
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo getAllAnimals
INFORMATION: getAllAnimals aufgerufen
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo countAnimalsByType
INFORMATION: countAnimalsByType aufgerufen
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo getAllAnimals
INFORMATION: getAllAnimals aufgerufen

Log-Level: FINE
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo summary
INFORMATION: summary aufgerufen
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo getAllAnimals
INFORMATION: getAllAnimals aufgerufen
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo getAllAnimals
FEIN: getAllAnimals liefert 4 Tiere zurück
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo summary
FEIN: summary erzeugt: Zoo mit 2 Gehegen und 4 Tieren: 2 Fish, 2 Mammals
Zoo mit 2 Gehegen und 4 Tieren: 2 Fish, 2 Mammals
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo getOvercrowdedEnclosures
INFORMATION: getOvercrowdedEnclosures aufgerufen mit maxAnimals=1
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo getOvercrowdedEnclosures
FEIN: getOvercrowdedEnclosures liefert 2 überfüllte Gehege zurück

Log-Level: SEVERE
Juli 01, 2026 11:44:59 PM de.cyzetlc.hsbi.zoo.Zoo addEnclosure
SCHWERWIEGEND: addEnclosure: Gehege-Name 'Haifischbecken' ist bereits vergeben - Namenseindeutigkeit verletzt, Gehege wird NICHT hinzugefügt
```

Ich finde log4j mit slf4j2 trotzdem noch deutlich praktischer und besser. 😉

---

# Aufg. 3

## Generics

### Wo helfen Generics zur Compile-Zeit?

* `Enclosure<T extends Animal>` sorgt dafür, dass man gar nicht erst ein Gehege für etwas anlegen kann, das kein Tier ist. `new Enclosure<String>(...)` würde also gar nicht kompilieren.
* Die spezialisierten Gehege (`Aquarium extends Enclosure<Fish>`, `MammalHouse extends Enclosure<Mammal>`, `CatHouse extends Enclosure<Cat>`) legen jeweils genau fest, welcher Tiertyp reinpasst. Der Compiler prüft das bei jedem `add(...)` automatisch, ganz ohne Casts oder Laufzeit-Checks.
* In `Zoo` sorgt `List<Enclosure<? extends Animal>>` dafür, dass man Gehege unterschiedlichster Tiertypen gemeinsam verwalten kann, ohne dass man sie versehentlich mischen oder falsch befüllen kann.

### Beispiel

```java
Aquarium aquarium = new Aquarium("Becken 1");
aquarium.add(new Shark("Bruce"));   // OK, Shark ist ein Fish
aquarium.add(new Cat("Felix"));     // Compile-Fehler, Cat ist kein Fish
```

Die zweite Zeile kompiliert nicht, weil `Aquarium` nur `Fish`-Objekte akzeptiert. Ohne Generics würde man diesen Fehler erst zur Laufzeit merken (oder gar nicht).

---

## Logging

Also anstelle von dem java.util.logging.Logger würde ich immer zu log4j mit slf4j2 raten. Es ist halt nicht nur
Standard, sondern bringt halt auch direkt viele Funktionen und configs mit. Aber is ja nicht so wichtig hier für die Abgabe :)

### Warum Logger statt `System.out.println`?

* Man kann die Ausgabemenge über Log-Level steuern, ohne den Code anzufassen – im Betrieb reichen z. B. `WARNING`/`SEVERE`, bei der Fehlersuche schaltet man auf `FINE`.
* Logging lässt sich zentral konfigurieren (wohin geschrieben wird, wie formatiert wird etc.). `println` schreibt dagegen immer stur auf die Konsole.
* Jede Log-Zeile hat automatisch Zeitstempel und Kontext. Das macht die Fehlersuche später viel einfacher.
* `println` vermischt normale Ausgaben mit Debug-Infos, ein Logger trennt das sauber.

### Wann welches Level?

* **INFO:** Eine öffentliche Zoo-Methode wird aufgerufen, zusammen mit ihren Parametern – zeigt, was gerade passiert.
* **FINE:** Kurze Detail-Info danach, z. B. wie viele Tiere oder Gehege betroffen sind – nützlich zum Debuggen, im Normalbetrieb aber nicht wichtig.
* **WARNING:** Etwas Angefragtes wurde nicht gefunden (z. B. Gehege-Name existiert nicht) – kein echter Fehler, aber ein Hinweis, dass die Anfrage ins Leere lief.
* **SEVERE:** Es stimmt etwas Grundsätzliches nicht, z. B. ein doppelter Gehege-Name, `null` statt eines Geheges oder ein Tier ohne erkennbare Tiergruppe.

---

## Streams

### Wo haben Streams geholfen?

* Bei Sachen wie `getAllMammals()` oder `countAnimalsByType()` machen Streams den Code sehr kurz und gut lesbar – Filtern, Umwandeln und Zählen liest sich fast wie eine Beschreibung in Worten, statt mit Schleifen und Zählvariablen zu hantieren.
* `flatMap` in `getAllAnimals()` holt mit einer Zeile alle Tiere aus allen Gehegen. Mit einer klassischen Schleife bräuchte man eine verschachtelte Doppelschleife plus eine Hilfsliste zum Sammeln.
* `groupingBy` + `counting()` erledigt Gruppieren und Zählen in einem Rutsch (z. B. bei `countAnimalsByType()`), das wäre klassisch eine Map mit manuellem Hochzählen gewesen.

### Wo wurde es unübersichtlich?

* In `summary()` wird die Kette recht lang (gruppieren, sortieren, formatieren, zusammenfügen). Da blickt man nicht mehr auf den ersten Blick durch, eine klassische Schleife oder kleinere Hilfsmethoden wären hier eventuell lesbarer gewesen.
* Bei Wildcard-Typen wie `Enclosure<? extends Animal>` im Stream muss man kurz genauer nachdenken, was der Compiler hier eigentlich erlaubt. Bei einer einfachen Schleife mit konkretem Typ ist das intuitiver.
