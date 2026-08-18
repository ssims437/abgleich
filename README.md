# Abgleich

**Wie ähnlich zwei Folgen sind.** Zwei Erbgutstücke, zwei Fassungen einer Datei, zwei Wörter mit
Tippfehler — dieselbe Matrix beantwortet alle drei Fragen. Sie füllt sich Feld für Feld, und der
Rückweg durch sie ist die beste Ausrichtung, von der sich **beweisen** lässt, dass es keine bessere
gibt.

→ **[Blatt öffnen](https://ssims437.github.io/abgleich/)**

- **Die Matrix als Bild** — jedes Feld eingefärbt nach seiner Punktzahl, der Rückweg eingezeichnet
- **Durchgehend oder stückweise** — Needleman-Wunsch (ganze Folgen) und Smith-Waterman (bestes Stück)
- **Punkte am Regler** — Treffer, Fehlpaarung, Lücke; die Ausrichtung ändert sich sofort
- **Fünf Beispiele** — verwandtes und nicht verwandtes Erbgut, ein Tippfehler, und zwei Textfassungen,
  bei denen dieselbe Matrix genau das liefert, was `diff` anzeigt
- **Änderungsabstand** (Levenshtein) auf einem zweiten, unabhängigen Weg gerechnet
- **Prüflauf** — sieben Zeilen, darunter der Vergleich gegen **alle** möglichen Ausrichtungen

## Was der Prüflauf zeigt

| Behauptung | Ergebnis |
|---|---|
| **die Matrix findet wirklich das Beste** | 120 Folgenpaare · **161 850 Ausrichtungen einzeln aufgezählt** (bis 8989 je Paar) · kein Unterschied |
| der Rückweg stimmt mit der Matrix überein | 400 Ausrichtungen ausgeschrieben und Spalte für Spalte nachgerechnet |
| symmetrisch, und mit sich selbst maximal | A gegen B = B gegen A · A mit sich selbst = Länge × Trefferpunkte |
| Matrix und Änderungsabstand sagen dasselbe | 500 Paare, zwei unabhängige Rechenwege |
| **Dreiecksungleichung hält** | alle **29 791 Tripel** aus Folgen bis Länge 4 · 0 Verletzungen |
| **stückweise findet wirklich das beste Stück** | 60 Paare · **22 347 Teilstück-Paare** stumpf durchgerechnet |
| teurere Lücken senken die Punktzahl | 1500 Läufe über fünf Lückenstrafen, 0 Ausreißer |

Die erste und die sechste Zeile sind der Kern: Dort steht die Matrix gegen stumpfes Durchprobieren —
einmal über alle Ausrichtungen, einmal über alle Teilstücke. Beides ist nur für kurze Folgen
bezahlbar, und genau deshalb ist es der richtige Prüfstein: Wo man noch zählen kann, muss die
Abkürzung dasselbe liefern.

## Warum das Zählen so schnell unbezahlbar wird

Die Zahl aller Ausrichtungen zweier Folgen sind die **Delannoy-Zahlen**. Sie wachsen brutal:

| Länge | Ausrichtungen |
|---|---|
| 3 × 3 | 63 |
| 6 × 6 | 8 989 |
| 9 × 9 | 1 462 563 |
| 12 × 12 | 265 470 617 |

Die Matrix braucht dagegen nur `(n+1)·(m+1)` Felder — bei 12 × 12 sind das **169**. Das ist der
ganze Gewinn der dynamischen Programmierung: Sie ersetzt exponentielles Zählen durch quadratisches
Ausfüllen, ohne dabei etwas zu übersehen.

## Was mich das gekostet hat

**Der Rückweg ist die Stelle, an der man sich belügt.** Die Matrix zu füllen ist die einfache
Hälfte — der Weg zurück durch sie ist die, bei der man Fehler nicht bemerkt: eine Ausrichtung, die
plausibel aussieht, aber nicht zu der Punktzahl gehört, die oben steht. Deshalb rechnet die zweite
Prüfzeile die fertige Ausrichtung **Spalte für Spalte** noch einmal nach und vergleicht mit dem
Wert aus der Matrix. Ohne diese Probe hätte ich nie erfahren, ob die schöne blaue Linie im Bild
tatsächlich das ist, was der Wert daneben behauptet.

**Stückweise ist nicht durchgehend mit anderem Vorzeichen.** Smith-Waterman unterscheidet sich von
Needleman-Wunsch durch drei Kleinigkeiten, die man alle einzeln vergessen kann: negative Werte
werden auf **null** gesetzt, der Rand wird **nicht** mit Lückenstrafen gefüllt, und der Rückweg
startet nicht in der Ecke, sondern beim **größten Feld überhaupt** — und endet, sobald er auf eine
Null trifft. Die sechste Prüfzeile deckt genau das ab, indem sie stumpf alle 22 347 Teilstück-Paare
durchrechnet und das Maximum bildet.

**Die Dreiecksungleichung war nicht selbstverständlich.** Dass der Änderungsabstand ein echtes Maß
ist — dass also der Umweg über ein drittes Wort nie kürzer sein kann —, klingt offensichtlich und
ist der Punkt, an dem eine falsche Kostenwahl auffliegt. Mit den Standardkosten (Einfügen,
Löschen, Ersetzen je 1) hält sie über alle 29 791 Tripel. Mit einer Ersetzung, die teurer wäre als
Löschen plus Einfügen, wäre sie sofort verletzt — dann würde „ersetzen" nie mehr gewählt, und das
Maß würde etwas anderes messen, als es verspricht.

**Was das Blatt nicht kann:** keine affinen Lückenkosten (in der Biologie kostet das *Öffnen* einer
Lücke viel und das *Verlängern* wenig — das braucht drei Matrizen statt einer, Gotoh 1982), keine
Substitutionsmatrizen wie BLOSUM oder PAM, keine Heuristiken wie BLAST, mit denen man tatsächlich
in Genomen sucht. Für zwei Folgen von je 3 Milliarden Zeichen hätte diese Matrix 9·10¹⁸ Felder —
deshalb benutzt sie niemand für ganze Genome, sondern nur für Ausschnitte.

## Technik

Eine einzelne HTML-Datei. Kein Build, keine Bibliothek, nichts verlässt den Browser.
Dynamische Programmierung, Canvas 2D, hell und dunkel.

## Die ganze Sammlung

Alle Blätter nach Feld geordnet, jedes mit eigenem Repo:
**[ssims437.github.io](https://ssims437.github.io/)**

## Lizenz

MIT
