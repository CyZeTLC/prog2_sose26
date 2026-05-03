# Aufg. 1)

Die Ausgabe von `git status` in diesem Bsp. zeigt, dass die Datein `CONTRIBUTING.md` und `homework/b03.md` bereits in der Repo vorhanden sind, jedoch geändert wurden und noch per `git add ...` hingzugefügt werden müssen.
Es zeigt ebenfalls, dass die Datei `foo.java` noch nicht per `git add ..` hinzugefügt wurde und von git noch nicht bekannt ist. Man kann ebenfalls noch erkennen, dass man sich aktuell im branch `b03` befindet.

## Befehlssequenz

* `git add foo.java` (oder `git add .`)
* `git commit -m "commit message"`

---

# Aufg. 2.)

## Aufg 2.1.1)

* `git log --reverse --all`
  → Zeigt alle Commits (dort nach tag 1 gesucht & commit hash kopiert)

* `git show 4aa80149369d7412df0a9ff837c4bc9ec37c774d`
  → Zeigt den Inhalt und Infos zum Commit von Tag 1 an

---

### Questlog

> In einer düsteren und mysteriösen Welt wagte sich ein furchtloser Held namens Markus in einen gefährlichen Dungeon. Es wurde erzählt, dass in diesem Dungeon Monster lauerten, die die Kerkerebenen terrorisierten. Markus war jedoch fest entschlossen, die Monster zu besiegen und das Amulet zu finden.

Das ganze dann mit `git show ...` dann auch für alle commits bis `01.5`

### Verlauf

* **01.1:**
  Eine Ratte kommt auf ihn zu & greift ihn an. Er zieht sich zurück und besiegt sie mit einem Schwert als die Ratte nachsetzt. Dazu ist exp. von 1 auf 2 gegangen.

* **01.2:**
  Er riecht ein komischen Geruch (moderig & abgestanden). Dazu sieht er eine schwache Beleuchtung & sieht in der Ferne eine Tür als er die Gänge entlang geht.

* **01.3:**
  Er öffnet die Tür und findet eine schlafende Ratte. Dabei merkt er, dass sein Schwert gegen schlafende Feinde besser ist. Exp. geht von 2 auf 4 hoch.

* **01.4:**
  Er geht immer vorsichtig weiter im Dungeon & bekommt langsam Hunger. Hunger geht von 0 auf 4 hoch.

* **01.5:**
  Markus kämpfte gegen die "Ratten", besiegte sie und muss weiter ins Dungeon absteigen.

---

## Aufg 2.1.2)

Er hat bei Commit `01.3` das erste Mal 4 exp.

Das weiß man, indem man einfach die Aufg. davor gemacht hat oder indem man sich den Verlauf von der stats Datei anschaut:

```bash
git log -p --reverse -G "experience" -- stats.md
```

---

## Aufg 2.1.3)

Auch hier findet man es raus mit dem selben cmd raus, man muss nur aus `experience` → `hunger` machen:

```bash
git log -p --reverse -G "hunger" -- stats.md
```

Wenn man das macht findet man raus, dass der Held bei Commit tag `2` das erste Mal `10 Hunger` hat.

---

## Aufg 2.1.4)

Mit:

```bash
git log -p --reverse -- rucksack.md
```

kann man sich die Änderungen im Rucksack ansehen.

Dort stellt man fest, dass der Held insgesamt nur einen Heiltrank zur selben Zeit hatte.

Allerdings hatte er:

* bei Commit tag `03.14` einen bekommen,
* bei Commit tag `03.17` einen verloren,
* & bei Commit tag `04.4` wieder einen bekommen.

Also hatte er insgesamt zwei Stück.

---

## Aufg 2.1.5)

* Bei Commit tag `03.9` hat er für 5 Gold ein Brot gekauft.
* Bei Commit tag `03.14` hat er für 5 Gold einen Heiltrank gekauft.

---

## Aufg 2.1.6)

Mit:

```bash
git diff 2ffebcf 39568d5
```

kann man die Änderungen sehen.

### Änderungen

In der `questlog.md` wurde hinzugefügt:

```text
Markus zieht erfrischt und gestärkt weiter.
```

Ebenfalls wurden aus dem Rucksack 10 Gold entfernt.

Dazu wurden:

* seine Leben von 5 auf 10 erhöht
* sein Hunger von 10 auf 0 gesetzt

---

## Aufg 2.1.7)

Verlauf anschauen mit:

```bash
git log -p --reverse -G "hunger" -- stats.md
```

(bei tag `03.17` hat sich der Hunger auf 0 geändert)

Mit:

```bash
git show 13834cd
```

kann man sehen, dass er an Tag `03.17` ein Brot gegesen & ein Heiltrank getrunken hat.

---

## Aufg. 2.2)

Per vim oder irgendeinen Editor die Exp. auf `42` setzen.

Dann:

```bash
git add .
git commit --amend --no-edit
```

Die Commit-Message wird dabei nicht geändert.

---

## Aufg. 2.3)

### Änderung

```text
Er merkte nach kurzer, dass das Essen verschimmelt war.
```

### CMD

```bash
git add .
git commit -m "tag 04.6"
```

---

## Aufg 2.4)

### Änderung

#### `questlog.md`

```text
..Er bekommt wieder sehr großen Hunger.
```

#### `stats.md`

Hunger auf `10` gesetzt.

### CMD

```bash
git add .
```

oder

```bash
git add stats.md questlog.md
```

danach:

```bash
git commit -m "tag 04.7"
```

---

## Aufg 2.5)

### `gear.md`

```md
| Property   | Value         |
|------------|---------------|
| weapon     | sword (3 dmg) |
| armor      | light (2 dmg) |
```

### Änderung in `stats.md`

Die beiden Zeilen `weapon` & `armor` entfernt.

### CMD

```bash
git add .
git commit -m "tag 04.8"
```

---

# Aufg. 3)

Vorarb möchte ich sagen, dass ich die Arbeit von niemanden hier schlecht reden möchte. Es könnte sein, dass das ein oder andere von mir etwas hart klingt, aber so hab ich das vor Jahren auf der gelernt und habe vieles bis jetzt so beibehalten und halte daran fest.

Kurzgesagt finde ich beide Commit-Messages nicht gut.

Besonders die erste Meldung ist sehr schlecht, denn:

* der Titel sagt überhaupt nichts über den Commit aus
* die Beschreibung des Commits ist super ungenau
* sie lässt sehr viel aus

Beim zweiten Commit ist es schon deutlich genauer, denn:

* der Titel beschreibt bereits kurz worum es geht
* die Beschreibung ist deutlich genauer
* sie geht darauf ein, was eigentlich genau geändert/gemacht wurde

Bei beiden Commits wurde jedoch:

* kein schönes Format
* und die falsche Zeitform

gewählt.

Ein Commit sollte immer in der Zeitform:

> "This Commit will ..." (Befehlsform)

geschrieben sein.

Mir gefällt auch nicht, dass soviel verschiedene Sachen in einem Commit gemacht wurden (ist zwar nicht direkt schlimm, aber habe ich anders gelernt und finde es auch anders besser).

Beim zweiten Bsp. schaut es aus, als wäre das dort nicht der Fall, da dieser Commit aus einer PR entstanden ist.

---

## Vorschläge

Ich persönlich bin ein großer Fan der Conventional Commits:

```text
feat(scope): ...
chore(scope): ...
```

Commits sollten mehr von einander abgetrennt sein (also mehrere einzelne).

---

# Aufg. 4)

Zuerst musste ich die Datei `gradle.properties` erstellen und dort den Java Home-Path festlegen.

Das ganze musste ich daher machen, da ich nicht die Java Version im gesamten System ändern wollte.

Ich habe auf meinem PC ca. 10 verschiedene Java-Versionen installiert & meine Standart System Version ist Java-8. Daher kann Gradle damit nicht starten.

Da viele meiner Projekte noch auf Java 8 o.Ä. laufen, ist es so einfacher, als es für das System zu ändern.

---

## Projektanlegen

### CMD

```bash
gradle init
```

Danach:

* application auswählen
* java auswählen
* groovy auswählen
* JUnit Jupiter auswählen
* name festlegen

---

## Erstelle Verzeichnisse

```text
app/                 -> eigentlicher Anwendungscode
gradle/              -> Dateien für den Gradle Wrapper
gradlew              -> Wrapper-Skript für Linux/macOS
gradlew.bat          -> Wrapper-Skript für Windows
settings.gradle      -> Projekteinstellungen
gradle.properties    -> Gradle-Konfigurationen
```

---

## Welche Tasks gibt es?

Man kann sehen welche Tasks es gibt mit:

```bash
gradle tasks
```

Dort findet man z.B.:

| Task       | Bedeutung                 |
| ---------- | ------------------------- |
| `build`    | Baut das gesamte Projekt  |
| `run`      | Startet die Anwendung     |
| `test`     | Führt JUnit-Tests aus     |
| `clean`    | Löscht Build-Dateien      |
| `jar`      | Erstellt ein JAR          |
| `assemble` | Baut Artefakte ohne Tests |
| `check`    | Führt Prüfungen/Tests aus |

Mit:

```bash
gradle help --task <task>
```

kann man auch noch mehr Details zu den einzelnen Tasks finden.

---

## Wo kommen die Java-Datein hin?

Die Datein kommen in den Ordner:

```text
./app/src/main/java/
```

---

## Projektlayout

| Ordner               | Inhalt                         |
| -------------------- | ------------------------------ |
| `src/main/java`      | Java-Quellcode der Anwendung   |
| `src/main/resources` | Ressourcen der Anwendung       |
| `src/test/java`      | JUnit-Testklassen              |
| `src/test/resources` | Testressourcen                 |
| `build/`             | automatisch generierte Dateien |

---

## Abschnitte im Build-Skript

Im Ordner `app` befindet sich die `build.gradle` Datei.

---

### 1)

```groovy
plugins {
    id 'application'
}
```

Dort werden die Gradle Plugins hinzugefügt.

### Das Plugin `application`

* macht das Projekt ausführbar
* erzeugt Startskripte
* stellt Tasks wie `run` bereit
* aktiviert intern auch das `java`-Plugin

---

### 2)

```groovy
repositories {
    mavenCentral()
}
```

Defeniert einfach nur welche Bibliotheken geladen werden.

Maven Central ist einfach die zentrale Maven Repo:
[https://mvnrepository.com/repos/central](https://mvnrepository.com/repos/central)

Dort habe ich z.B. auch lombok für mein InteliJ-Projekt her.

---

### 3)

`dependencies` legt einfach nur fest, welche Deps. ins das Projekt eingebaut werden.

Die müssen in diesem Fall alle im Maven Central (oder local) liegen wenn man keine weitere Repo festlegt.

---

### 4)

```groovy
application {
    mainClass = 'org.example.App'
}
```

Legt einfach nur die Main Klasse des Projektes fest (package & Klassenname halt).

---

### 5)

```groovy
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(25)
    }
}
```

Setzt die Java Version. In diesem Fall Java 25.

---

### 6)

```groovy
testing {
    suites {
        test {
            useJUnitJupiter('6.0.1')
        }
    }
}
```

Legt JUnit `v6.0.1` als Testingframework fest.

---

## Die Tasks in der IDE ausführen

Ich bin ein großer Fan davon die rechte Sidebar in InteliJ für einzelne CMDs zu nutzen.

Für Abläufe zum Bauen des Projekts lege ich einfach Run/Debug Configs an.

Für das Bauen z.B.:

* clean
* build

Alles geht natürlich in der IDE auch einfach über das Terminal mit z.B.:

```bash
./gradlew build
```

(die IDE macht ja mit der Sidebar auch nichts anderes als den CMD ausführen)

---

# Aufg. 5)

## Config für spotless

```groovy
spotless {
    java {
        target 'src/*/java/**/*.java'
        removeUnusedImports()
        trimTrailingWhitespace()
        endWithNewline()
        eclipse().configFile('spotless/codestyle.xml')
    }
}
```

Für die Formatierung habe ich bewusst für meine eigene Version entschieden, da ich damit seit Jahren arbeite & diese am schönsten finde.

Die Datei dafür befindet sich auch im Git.

Ansich würde ich aber erstmal sagen, dass spotless für mich keinen großen Vorteil hat, da bereits alles was Formatierung etc. angeht automatisch beim Speichern einer Datei von meiner IDE übernommen wird.

Für ältere Projekte von mir finde ich spotless jedoch echt super, denn bei vielen Projekte die schon ein paar Jahre älter sind, hatte ich meine IDE noch nicht so eingestellt und dort finde ich oft Datein wo z.B. nicht verwendete Imports o.Ä. drin sind, welche dann von spotless entfernt werden könnten.

---

## Erklärung der Settings

### `removeUnusedImports()`

Entfernt alle nicht benutzten Imports.

Beispiel:

Wenn ich einmal eine `HashMap` an einer Stelle im Code hatte, diese aber entfernt habe, bleibt oft der Import vorhanden bei schlecht eigestellter IDE.

Spotless würde den Import für `HashMap` dann entfernen.

---

### `trimTrailingWhitespace()`

Entfernt einfach nur nachgestelle Leerzeichen.

---

### `endWithNewline()`

Stellt sicher, dass jede Datei mit einer neuen Zeile endet.

---

### `eclipse().configFile('spotless/codestyle.xml')`

Legt fest, dass der Code nach den in der Datei:

```text
spotless/codestyle.xml
```

gegebenen Formatierungen formatiert wird.
