# Aufg. 1

**Link zur Datei (AstTest):**  
https://github.com/CyZeTLC/prog2_ybel_filterdsl/blob/master/src/test/java/filter/ast/AstTest.java

Wie bei den letzten Abgaben auch, habe ich hier wieder einmal das Muster **Given–When–Then** hinzugefügt.

```java
    @Test
    void parseComplexQueryWithPrecedenceAndInListToValidAst() {
        // Given
        String query = "genre in (\"power metal\", \"punk rock\") or year <= 1995 and not artist == \"Nirvana\"";

        Expr expectedAst =
            new Expr.Or(
                new Expr.InList("genre", List.of(new Value.Str("power metal"), new Value.Str("punk rock"))),
                new Expr.And(
                    new Expr.Comparison("year", CompOp.LE, new Value.Num(1995)),
                    new Expr.Not(new Expr.Comparison("artist", CompOp.EQ, new Value.Str("Nirvana")))));

        // When
        Expr actualAst = evaluatePatternBuilder(query);

        // Then
        assertEquals(expectedAst, actualAst);
    }

    @Test
    void parseChainedAndExpressionsWithLeftAssociativeOrder() {
        // Given
        String query = "year > 1980 and artist == \"Blind Guardian\" and genre == \"power metal\"";

        Expr expectedAst =
            new Expr.And(
                new Expr.And(
                    new Expr.Comparison("year", CompOp.GT, new Value.Num(1980)),
                    new Expr.Comparison("artist", CompOp.EQ, new Value.Str("Blind Guardian"))),
                new Expr.Comparison("genre", CompOp.EQ, new Value.Str("power metal")));

        // When
        Expr actualAst = evaluateVisitorBuilder(query);

        // Then
        assertEquals(expectedAst, actualAst);
    }

    @Test
    void parseParenthesesToOverrideDefaultPrecedenceRules() {
        // Given
        String query = "(year <= 1995 or artist == \"Die Toten Hosen\") and year > 1985";

        Expr expectedAst =
            new Expr.And(
                new Expr.Or(
                    new Expr.Comparison("year", CompOp.LE, new Value.Num(1995)),
                    new Expr.Comparison("artist", CompOp.EQ, new Value.Str("Die Toten Hosen"))),
                new Expr.Comparison("year", CompOp.GT, new Value.Num(1985)));

        // When
        Expr patternAst = evaluatePatternBuilder(query);
        Expr visitorAst = evaluateVisitorBuilder(query);

        // Then
        assertAll(
            () -> assertEquals(expectedAst, patternAst),
            () -> assertEquals(expectedAst, visitorAst)
        );
    }

    @ParameterizedTest
    @ValueSource(
        strings = {
            "artist == \"Subway to Sally\"",
            "year == 1991",
            "not artist != \"Queen\"",
            "artist == \"Blind Guardian\" and year == 1992",
            "genre in (\"folk metal\", \"folk rock\")",
            "not (genre in (\"electronic\", \"rock\") or year >= 2010)"
        })
    void assertBothBuildersProduceIdenticalAstForStandardQueries(String query) {
        // Given & When
        Expr patternAst = evaluatePatternBuilder(query);
        Expr visitorAst = evaluateVisitorBuilder(query);

        // Then
        assertEquals(patternAst, visitorAst);
    }

    @Test
    void parseStringLiteralsWithInternalEscapedQuotesCorrectly() {
        // Given
        Expr expectedAst = new Expr.Comparison("title", CompOp.EQ, new Value.Str("Bataillon d\"Amour"));

        // When
        Expr patternAst = evaluatePatternBuilder("title == \"Bataillon d\\\"Amour\"");
        Expr visitorAst = evaluateVisitorBuilder("title == \"Bataillon d\\\"Amour\"");

        // Then
        assertAll(
            () -> assertEquals(expectedAst, patternAst),
            () -> assertEquals(expectedAst, visitorAst)
        );
    }

    @Test
    void simplifyExpressionTreeByEliminatingDeepDoubleNegations() {
        // Given
        Expr blindGuardianComparison = new Expr.Comparison("artist", CompOp.EQ, new Value.Str("Blind Guardian"));
        Expr metalComparison = new Expr.Comparison("genre", CompOp.EQ, new Value.Str("power metal"));

        Expr originalAst =
            new Expr.And(
                new Expr.Not(new Expr.Not(blindGuardianComparison)),
                new Expr.Not(new Expr.Not(new Expr.Not(metalComparison)))
            );

        Expr expectedAst = new Expr.And(blindGuardianComparison, new Expr.Not(metalComparison));

        // When
        Expr simplifiedAst = AstBuilders.simplify(originalAst);

        // Then
        assertEquals(expectedAst, simplifiedAst);
    }

    @Test
    void parseAndAutomaticallyNormalizeDoubleNegationsFromQueryString() {
        // Given
        Expr expectedAst = new Expr.Comparison("artist", CompOp.EQ, new Value.Str("Die Toten Hosen"));

        // When
        Expr patternExpression =
            AstBuilders.fromQuery("not not artist == \"Die Toten Hosen\"", new AstBuilderPattern()::translate);
        Expr visitorExpression =
            AstBuilders.fromQuery("not not artist == \"Die Toten Hosen\"", new AstBuilderVisitor()::translate);

        // Then
        assertAll(
            () -> assertEquals(expectedAst, patternExpression),
            () -> assertEquals(expectedAst, visitorExpression)
        );
    }

    @Test
    void evaluateMediaItemAgainstAstProducedByPatternBuilder() {
        // Given
        String query = "artist == \"Subway to Sally\" and year == 1998";
        Expr expression = AstBuilders.fromQuery(query, new AstBuilderPattern()::translate);
        MediaItem matchingItem = new MediaItem("Veitstanz", "Subway to Sally", Genre.FOLK_METAL, 1998);
        MediaItem nonMatchingItem = new MediaItem("Mirror Mirror", "Blind Guardian", Genre.POWER_METAL, 1998);

        // When
        boolean matchesExpected = Evaluator.matches(matchingItem, expression);
        boolean matchesUnexpected = Evaluator.matches(nonMatchingItem, expression);

        // Then
        assertAll(
            () -> assertTrue(matchesExpected),
            () -> assertFalse(matchesUnexpected)
        );
    }

    private static Expr evaluatePatternBuilder(String query) {
        return new AstBuilderPattern().translate(AstBuilders.parse(query));
    }

    private static Expr evaluateVisitorBuilder(String query) {
        return new AstBuilderVisitor().translate(AstBuilders.parse(query));
    }
```

---

# Aufg. 2

**Link zur Datei (AstBuilderVisitor):**  
https://github.com/CyZeTLC/prog2_ybel_filterdsl/blob/master/src/main/java/filter/ast/builder/AstBuilderVisitor.java

---

# Aufg. 3

**Link zur Datei (AstBuilderPattern):**  
https://github.com/CyZeTLC/prog2_ybel_filterdsl/blob/master/src/main/java/filter/ast/builder/AstBuilderPattern.java

---

# Aufg. 4

## 1. Visitor-Pattern

### Vorteile

- **Automatische Traversierung:** Der von ANTLR generierte Basis-Visitor steuert den Durchlauf des Baums; nicht explizit überschriebene Knoten werden automatisch weitergereicht.
- **Erweiterbarkeit:** Neue Operationen auf der Baumstruktur können durch zusätzliche Visitor-Klassen implementiert werden, ohne die bestehenden Knotanklassen zu modifizieren.

### Nachteile

- **Zustandsbehaftet:** Die Verwendung von Stacks erfordert eine genaue Verwaltung der Reihenfolge von Einfügen und Löschen (`push`/`pop`), was die Anfälligkeit für Laufzeitfehler erhöht.
- **Komplexeres Debugging:** Logische Fehler bei der Stack-Zuweisung spiegeln sich oft erst verzögert im Gesamtergebnis wider und sind schwerer zu lokalisieren.

---

## 2. Manuelle Traversierung mit Pattern Matching

### Vorteile

- **Zustandslosigkeit:** Daten werden direkt entgegengenommen und transformiert zurückgegeben. Der Verzicht auf temporäre Speicherstrukturen (Stacks) erhöht die Vorhersagbarkeit des Kontrollflusses.
- **Compiler-Unterstützung:** Bei der Verwendung von *Sealed Interfaces* prüft der Compiler, ob alle möglichen Subtypen im `switch`-Block abgedeckt sind.
- **Direkte Lesbarkeit:** Die Struktur der Methoden spiegelt die Hierarchie der Grammatikregeln eins zu eins wider.

### Nachteile

- **Erhöhter Implementierungsaufwand:** Jede Verzweigung und Weiterleitung im Baum muss explizit ausprogrammiert werden.
- **Wartungsaufwand bei Grammatikänderungen:** Strukturänderungen in der `.g4`-Datei erfordern manuelle Anpassungen an den entsprechenden Stellen im Code.

---

## Fazit

Das **Visitor-Pattern** eignet sich für komplexe Systeme, bei denen eine stabile Baumstruktur im Nachhinein für viele unterschiedliche, unabhängige Analysen genutzt werden soll.

Das **Pattern Matching** ist eine zielgerichtete Alternative für lineare Transformationen (z. B. Parse-Tree zu AST), da es Typsicherheit mit einem zustandslosen Kontrollfluss verbindet.

---

# Aufg. 5

**Link zur Datei (AstBuilders):**  
https://github.com/CyZeTLC/prog2_ybel_filterdsl/blob/master/src/main/java/filter/ast/builder/AstBuilders.java

---

# Aufg. 6

**Link zur Datei (ApprovalTest):**  
https://github.com/CyZeTLC/prog2_ybel_filterdsl/blob/master/src/test/java/filter/ast/ApprovalTest.java

---

# Aufg. 7

**Link zur Datei (RoundtripPropertiesTest):**  
https://github.com/CyZeTLC/prog2_ybel_filterdsl/blob/master/src/test/java/filter/ast/RoundtripPropertiesTest.java