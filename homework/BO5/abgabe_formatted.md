# Aufgabe 1.1

Link: https://github.com/CyZeTLC/prog2_ybel_syntaxhighlighting/

## Kernunterschied

- **Regex (Blatt 04):**
  Sucht nach Mustern im gesamten Text. Dabei entsteht schnell Durcheinander, weil ein Regex nicht weiß, in welchem Kontext er sich befindet. Er findet beispielsweise Schlüsselwörter mitten in Kommentaren oder Strings. Diese Konflikte müssen anschließend im Java-Code aufwendig aussortiert werden.

- **ANTLR (Lexer):**
  Liest den Quellcode von vorne nach hinten. Dabei kennt der Lexer den aktuellen Kontext. Beginnt beispielsweise ein Kommentar, werden alle anderen Regeln ignoriert, bis dieser endet. Dadurch entstehen solche Konflikte gar nicht erst.

---

## Vergleich

| Eigenschaft | Regex-Highlighter | ANTLR-Lexer |
|-------------|-------------------|-------------|
| **Arbeitsweise** | Sucht unabhängig voneinander nach Mustern im Text. | Liest den Text einmal von links nach rechts. |
| **Konflikte & Überlappungen** | Treten häufig auf (z. B. Schlüsselwörter innerhalb von Kommentaren). | Treten nicht auf. Jedes Zeichen gehört genau zu einem Token. |
| **Sortierung der Ergebnisse** | Treffer müssen anschließend sortiert werden. | Tokens liegen automatisch in der richtigen Reihenfolge vor. |
| **Aufwand im Java-Code** | Hoch, da Konflikte manuell gelöst werden müssen. | Gering, da ANTLR die Konfliktlösung übernimmt. |
| **Aufwand im Hintergrund** | Gering (nur Standard-Java). | Höher (Grammatikdatei `.g4` und Codegenerierung). |

### Zusammenfassung

Der Java-Code ist bei der Regex-Variante deutlich aufwendiger, da die Fehler der ungenauen Suche nachträglich korrigiert werden müssen. ANTLR ist zwar im Hintergrund komplexer, übernimmt dafür aber die komplette Konfliktlösung.

---

# Aufgabe 1.2.1

Hierfür habe ich eine neue Klasse `highlighting.antlr.PrettyPrinter` erstellt und die Methode `PrettyPrinter#parse()` implementiert.

```java
public static MiniJavaParser.CompilationUnitContext parse(String text) {
    CharStream charStream = CharStreams.fromString(text);
    MiniJavaLexer lexer = new MiniJavaLexer(charStream);
    CommonTokenStream tokenStream = new CommonTokenStream(lexer);
    MiniJavaParser parser = new MiniJavaParser(tokenStream);

    MiniJavaParser.CompilationUnitContext tree = parser.compilationUnit();
    return tree;
}
```

---

# Aufgabe 1.2.2

Die vier geforderten Methoden befinden sich bereits im Fork bzw. Repository:

<https://github.com/CyZeTLC/prog2_ybel_syntaxhighlighting/blob/master/src/main/java/highlighting/antlr/PrettyPrinterVisitor.java>

---

# Aufgabe 1.2.3

## Abfrage der Einrückung

Der Benutzer wird nach der Anzahl der Leerzeichen pro Einrückungsstufe gefragt.

```java
private static int requestSpacesPerIndentLevel() {
    Scanner inputScanner = new Scanner(System.in);

    while (true) {
        System.out.print("Anzahl an Leerzeichen pro Einrueckstufe (z.B. 2, 4 oder 8): ");

        try {
            if (!inputScanner.hasNextLine()) {
                continue;
            }

            int userInput = Integer.parseInt(inputScanner.nextLine().trim());

            if (userInput > 0) {
                return userInput;
            }
        } catch (NumberFormatException ignored) {
        }
    }
}
```

## Ausgabe des formatierten Codes

```java
System.out.println();
System.out.println("=== Pretty-Printer Ausgabe ===");
System.out.println(
    PrettyPrinter.format(MINI_JAVA_FORMAT_TEST_CODE, spacesPerIndentLevel)
);
System.out.println("==============================");
```

## Beispiel für unformatierten Code

Der folgende Beispielcode enthält nahezu alle in der Aufgabenstellung genannten Sprachkonstrukte.

```java
package demo;import java.util.List;@TypeAnnotation class Calculator{private int result;public void execute(){while(isRunning){if(hasError){return null;}else{{return result;}}}}}
```

---

# Aufgabe 2.1

Link: https://github.com/CyZeTLC/prog2_ybel_cyclechronicles

## 1. Äquivalenzklassen und Grenzwerte

| Merkmal | Gültige Klasse | Ungültige Klasse | Wichtige Grenzwerte |
|----------|----------------|------------------|---------------------|
| **Fahrradtyp** | `RACE`, `SINGLE_SPEED`, `FIXIE` | `GRAVEL`, `EBIKE` | – |
| **Kunde** | Kein offener Auftrag vorhanden | Bereits ein offener Auftrag vorhanden | Wechsel von **0** auf **1** Auftrag |
| **Queue-Größe** | 0–4 offene Aufträge | 5 oder mehr offene Aufträge | **4** (letzter freier Platz), **5** (Warteschlange voll) |

---

## 2. Testfälle

### Testfall 1: Erfolgreiche Annahme

**Ausgangslage**

- Der Shop ist leer.
- Es existieren keine offenen Aufträge.

**Aktion**

- Ein neuer Kunde gibt einen Auftrag für ein `RACE`-Bike ab.

**Erwartung**

- Die Methode gibt `true` zurück.
- Der Auftrag wird in die Warteschlange aufgenommen.

---

### Testfall 2: Ablehnung wegen falschem Fahrradtyp

**Ausgangslage**

- Der Shop ist leer.

**Aktion**

- Ein Kunde bestellt ein `GRAVEL`-Bike.

**Erwartung**

- Die Methode gibt `false` zurück.
- Die Warteschlange bleibt unverändert.

---

## 3. JUnit-Implementierung mit Mockito

Die vollständige Klasse `ShopTest` befindet sich im Repository. Die beiden Testmethoden wurden zusätzlich nach dem **Given-When-Then**-Muster kommentiert, um den Ablauf der Tests übersichtlicher darzustellen.

```java
@Test
void testAcceptValidOrderShouldReturnTrue() {
    // Given: Ein leerer Fahrradladen und ein Kunde mit einem gültigen Rennrad
    Order validOrder = mock(Order.class);
    when(validOrder.getCustomer()).thenReturn("Kunde A");
    when(validOrder.getBicycleType()).thenReturn(Type.RACE);

    // When: Der Auftrag wird eingereicht
    boolean result = shop.accept(validOrder);

    // Then: Der Auftrag wird angenommen
    assertTrue(result);
}

@Test
void testAcceptGravelBikeShouldReturnFalse() {
    // Given: Ein leerer Fahrradladen und ein Kunde mit einem nicht erlaubten Fahrradtyp
    Order gravelOrder = mock(Order.class);
    when(gravelOrder.getCustomer()).thenReturn("Kunde B");
    when(gravelOrder.getBicycleType()).thenReturn(Type.GRAVEL);

    // When: Der Auftrag wird eingereicht
    boolean result = shop.accept(gravelOrder);

    // Then: Der Auftrag wird abgelehnt
    assertFalse(result);
}
```