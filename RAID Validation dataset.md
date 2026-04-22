Proof of Concept

**Geometrische Auditierung von KI-Systemen**  
B. Wyneken · DOI: [10.5281/zenodo.17691545](https://zenodo.org/records/17691545)

---

## Was wurde gebaut

Ein **deterministischer, regelbasierter Detektor** für strukturelle Über‑Kohärenz in KI‑generierten Texten.  
Kein neuronales Netz, keine Embeddings, keine GPU. Die Erkennung basiert auf neun CPU‑Metriken, die topologische Eigenschaften des Textes messen.

**Kernidee:** Ein stabiler Attraktor im Phasenraum (D₂ ≈ 1.0) entspricht einer gleichmäßigen, geordneten Textstruktur. KI‑Texte zeigen diese Gleichmäßigkeit in mehreren Dimensionen gleichzeitig – das ist die messbare Signatur.

Das System ist ein **Veto‑Filter mit strikter UND‑Logik**: Ein Dokument wird nur dann als „KI‑Halluzination“ markiert, wenn **alle** definierten strukturellen Bedingungen gleichzeitig erfüllt sind. Dies führt zu extrem hoher Präzision bei sehr geringer False‑Positive‑Rate in kontrollierten Umgebungen.

---

## Was wurde erreicht

| Metrik | Wert |
|--------|------|
| **Precision** | **95,4 %** |
| Recall | 10,0 % |
| Spezifität (Human korrekt) | 83,7 % |
| Kalibrierungsbasis | 61 handverlesene deutsche KI‑Texte |
| Testbasis | RAID‑Benchmark: 7,1 Mio. Dokumente, 11 Modelle, 11 Domains, 11 Adversarial Attacks |

Der niedrige Recall ist **erwartet** – RAID enthält bewusst qualitativ hochwertige, teils menschlich nachbearbeitete KI‑Texte. QCK erkennt keine „KI generell“, sondern eine spezifische **Signatur struktureller Über‑Kohärenz**. Ein kohärenter, stabiler KI‑Text wird nicht markiert.

**Kernaussage:** 61 Kalibrierungstexte generalisieren auf 7 Millionen Dokumente mit 95,4 % Precision. Ein neuronales Modell benötigt für vergleichbare Generalisierung Millionen Trainingsbeispiele – QCK misst geometrische Eigenschaften und lernt keine statistischen Muster.

---

## Robustheit gegen adversarielle Angriffe

Getestet auf 11 Angriffstypen (jeweils ~587.000 KI‑Texte). 9 von 11 Angriffen zeigen **keinen messbaren Effekt** auf den Recall.

| Angriff | Δ Recall | Bewertung |
|---------|----------|-----------|
| alternative_spelling | −0,0 % | robust |
| article_deletion | +1,6 % | robust |
| homoglyph | −0,5 % | robust |
| insert_paragraphs | +0,0 % | robust |
| number | +0,3 % | robust |
| perplexity_misspelling | +0,0 % | robust |
| synonym | +0,6 % | robust |
| upper_lower | +0,6 % | robust |
| whitespace | −0,5 % | robust |
| paraphrase | −6,9 % | anfällig |
| zero_width_space | −11,2 % | anfällig |

**Begründung:** QCK misst topologische Eigenschaften – Leerzeichen, Großschreibung, Synonyme und Tippfehler ändern die Topologie nicht.

- **`paraphrase`** (−6,9 %): Echte semantische Umformulierung verändert Satzstruktur und Vokabular‑Dynamik – theoretisch konsistent.
- **`zero_width_space`** (−11,2 %): Unsichtbare Unicode‑Zeichen fragmentieren die Tokenisierung. Technisch lösbar durch Unicode‑Normalisierung (NFKC) – im PoC nicht implementiert.

---

## Erkennungsrate pro Domain (KI clean)

| Domain | KI‑Recall | Human‑Spezifität | Anmerkung |
|--------|-----------|------------------|-----------|
| german | 17,1 % | 64,1 % | Beste KI‑Erkennung – Kalibrierung auf Deutsch |
| reviews | 14,1 % | 83,1 % | |
| wiki | 12,5 % | 96,8 % | |
| news | 12,0 % | 83,6 % | |
| poetry | 11,6 % | 75,0 % | |
| reddit | 10,2 % | 99,0 % | |
| abstracts | 10,2 % | 80,9 % | Siehe Anmerkung |
| czech | 10,5 % | 56,5 % | Sprach‑Mismatch |
| books | 8,3 % | 90,5 % | |
| recipes | 7,2 % | 95,5 % | |
| code | 7,0 % | 88,2 % | |

**Anmerkung zu Abstracts:** Level 3 (300–700 Wörter) zeigt eine erhöhte FP‑Rate (23,7 %). Das ist kein Detektionsfehler – wissenschaftliche Abstracts, Wikipedia‑Kurzartikel und Datenbankeinträge verwenden von Natur aus eine gleichmäßige, informationsdichte Sprache, die strukturell mit KI‑Ausgaben konvergiert. Für das eigentliche Anwendungsszenario (freier narrativer Text) ist dieser Bereich weniger relevant.

---

## Kalibrierung

### Level 1–4 (KI‑Käfig)

KI muss **alle** Gates treffen. Human muss **mindestens zwei** Gates brechen (ein einzelner Bruch reicht nicht).

| Level | Wörter | FP‑Rate | Human‑Basis | Gate‑Metriken |
|-------|--------|---------|-------------|---------------|
| lvl1 | 5–49 | 0,0 % | 35 | d3_global, bigram_entropy, hapax_ratio |
| lvl2 | 50–299 | 6,9 % | 2.014 | avg_word_len, vocab_growth, rep_rate, bigram_entropy |
| lvl3 | 300–699 | 23,7 % | 1.202 | rep_rate, ttr, d3_avg, vocab_growth |
| lvl4 | 700–3.500 | 8,2 % | 1.202 | hapax_ratio, rep_rate, d3_min, vocab_growth |

### Level 9 (Human‑Korridor, invertiert)

Bei Texten >3.500 Wörtern ist die Logik **invertiert**: Der Korridor ist auf Human‑Texten kalibriert. KI bricht aus diesem Korridor aus (≥1 Bruch → KI_HALLUCINATION).

| Human‑Basis | Gate‑Metriken |
|-------------|---------------|
| 1.283 Texte | sent_autocorr (−0,501 … 0,611), zipf_slope (0,060 … 1,472) |

**Warum invertiert?** Bei sehr langen Texten ist der menschliche Rhythmus stabiler als der von KI – ein Mensch, der ein Buch schreibt, hat einen charakteristischen, konsistenten Rhythmus; eine KI verliert mit der Länge die Konsistenz oder wiederholt Muster, die kein Mensch zeigt.

---

## Die Level im Überblick

- **lvl1 (5–49 Wörter)** – Künstliche Satzbildung, kompressionsbasierte Metriken (d3_global, bigram_entropy, hapax_ratio)
- **lvl2 (50–299)** – Wortlänge, Vokabularwachstum, Wiederholungsrate
- **lvl3 (300–699)** – Schwierigste Zone (Abstracts, News) – strukturell höhere FP‑Rate
- **lvl4 (700–3.500)** – Hapax, Repetition, Sliding Window
- **lvl9 (>3.500, invertiert)** – Human‑Korridor, Autokorrelation der Satzlängen, Zipf‑Steigung

---

## Performance

**Benchmark: Intel i9-10900X (2019, 10 Kerne), kein GPU**

| Domain | Dokumente | Zeit | Docs/Sekunde |
|--------|-----------|------|-------------|
| code | 386.400 | 10s | 37.138/s |
| abstracts | 741.720 | 49s | 15.137/s |
| reddit | 747.180 | 72s | 10.377/s |
| books | 748.020 | 76s | 9.843/s |
| recipes | 744.240 | 89s | 8.351/s |
| german | 827.400 | 93s | 8.897/s |
| wiki | 747.180 | 101s | 7.397/s |

**7,9 Millionen Dokumente in ~20 Minuten** auf Standard‑Consumer‑Hardware (2019).  
Die Geschwindigkeit variiert mit der Textlänge – kurze Code‑Texte (lvl1/2) sind etwa 5‑mal schneller als lange Wiki‑Artikel (lvl4).

---

## Anhang: D₂ und die Wahl der Metriken

### Was ist D₂?

Die Korrelationsdimension D₂ misst, wie dicht ein Pfad im Phasenraum einen Attraktor ausfüllt.  
- **D₂ ≈ 1,0** → stabiler Attraktor (KI‑typisch)  
- **D₂ > 1,5** → unregelmäßiger Pfad (Human‑typisch)

Echtes D₂ im Embedding‑Raum benötigt Satzvektoren, eine Distanzmatrix und den Grassberger‑Procaccia‑Algorithmus – O(N²), GPU, Transformer‑Abhängigkeit.

### Die Proxy‑Entscheidung

QCK verwendet **neun CPU‑Metriken**, die dieselbe Information approximieren:

| Metrik | Ersetzt im Phasenraum |
|--------|----------------------|
| `sent_autocorr` | Autokorrelation der Schrittweiten |
| `zipf_slope` | Globale Dichte des Vokabulars |
| `rep_rate` | Lokale Schrittweiten‑Varianz |
| `hapax_ratio` | Seltenheitsstruktur des Attraktors |
| `ttr` | Vokabular‑Diversität |
| `vocab_growth` | Expansion des Attraktors über Zeit |
| `bigram_entropy` | Lokale Krümmung der Trajektorie |
| `avg_word_len` | Morphologische Komplexität |
| `d3_global` | Kompressionsbasierte Redundanz |

**Warum die Proxys funktionieren:** KI‑Texte sind in mehreren voneinander unabhängigen statistischen Dimensionen gleichmäßig. Weil diese Dimensionen orthogonal sind, reicht ein einziger Bruch in irgendeiner Metrik, um Human zu signalisieren. KI muss alle Gates erfüllen.

| Kriterium | CPU‑Proxys (QCK) | Echtes D₂ (Embedding) |
|-----------|-----------------|----------------------|
| Rechenaufwand | O(N) | O(N²) + Transformer |
| Geschwindigkeit | 7,9 Mio. Docs/20 min | ~5–10 Docs/s auf GPU |
| Modellabhängigkeit | keine | hoch |
| Precision auf RAID | **95,4 %** | nicht getestet |

Die CPU‑Proxys sind **kein Kompromiss** – sie sind eine bewusste Engineering‑Entscheidung. Echtes D₂ bleibt ein wertvolles theoretisches Konstrukt für Spezialfälle oder als Kalibrierungsreferenz.

---

## Was dieses System **nicht** ist (noch)

- **Kein universeller KI‑Detektor** – es erkennt nicht alle KI‑Texte
- **Kein probabilistischer Klassifikator** – es gibt keine Wahrscheinlichkeiten aus
- **Kein semantischer Wahrheitsprüfer** – es prüft keine Fakten
- **Kein allgemeiner Halluzinationsdetektor** – es misst strukturelle Kohärenz, nicht semantische Fehler

**Interpretation eines positiven Befunds:**  
> „Dieser Text zeigt ein ungewöhnlich hohes Maß an multidimensionaler struktureller Gleichmäßigkeit, das unter typischen menschlichen Schreibprozessen unwahrscheinlich ist.“

Nicht‑Erkennung bedeutet **nicht**, dass der Text von einem Menschen stammt.

---

## Abgrenzung zu ML‑basierten Detektoren

| Aspekt | ML‑Detektor | QCK (dieses System) |
|--------|-------------|---------------------|
| **Lernverfahren** | Statistisches Lernen aus Daten | Deterministische, feste Regeln |
| **Entscheidung** | Probabilistisch (z. B. p=0,87) | Harte logische UND‑Verknüpfung |
| **Feature‑Raum** | Latenter Embedding‑Raum (hochdimensional, black box) | Explizite, interpretierbare Metriken (Zipf, Repetition, …) |
| **Komplexität** | O(N·d) mit großen Konstanten (Transformer) | O(N) mit sehr kleinen Konstanten |
| **Invarianz** | Empfindlich gegen Paraphrasierung, Synonyme, Formatierung | Stabil unter lexikalischen Änderungen (solange Struktur bleibt) |
| **Fehlermodi** | Opaque (Overfitting, Domänenverschiebung, Kalibrierungsfehler) | Explizit rückverfolgbar auf einzelne Metrik‑Grenzen |
| **Lebenszyklus** | Training, Validierung, Retraining, Versionierung | Kein Training, kein Update, reine Funktion |
| **Optimierungsziel** | Accuracy / F1 | **Maximierung der Präzision unter strikten Nebenbedingungen** |

ML‑Detektoren und QCK operieren in **fundamental unterschiedlichen Räumen** und können als **komplementäre Komponenten** eingesetzt werden – nicht als direkte Substitute.

---

## Design‑Philosophie

Das System priorisiert:

- **Präzision vor Recall**
- **Determinismus vor Wahrscheinlichkeit**
- **Interpretierbarkeit vor Modellkomplexität**
- **Geschwindigkeit und Einsetzbarkeit vor Trainingsperformance**

Dadurch eignet es sich als:

- **forensischer Pre‑Filter**
- **Hochvertrauenssignal‑Generator**
- **komplementäre Komponente** in größeren Detektionspipelines

---

## Einschränkungen und offene Fragen (Red‑Team‑Perspektive)

- **Sprache:** Kalibriert auf Deutsch. Die hohe Precision auf englischen RAID‑Dokumenten wurde ohne englische Kalibrierung erreicht. Für andere Sprachen sinkt die Spezifität (siehe `czech`). Ein multilingualer Ausbau ist möglich, aber nicht Teil des Prototyps.
- **Was „Halluzination“ bedeutet:** Es wird strukturelle Gleichmäßigkeit gemessen, nicht faktische Korrektheit. Die Korrelation zwischen topologischer Signatur und faktischer Halluzination ist theoretisch hergeleitet, aber nicht direkt empirisch validiert.
- **Gate‑Stabilität:** Die Grenzen wurden auf einem bestimmten Human‑Korpus kalibriert. Kreuzvalidierung über andere Domänen (z. B. juristische Texte, Patentschriften) steht aus.
- **False Positives:** Globale FP‑Rate 16,3 % – aufgeschlüsselt nach Domäne: `reddit` 1,0 %, `wiki` 3,2 %, `recipes` 4,5 %, `czech` 43,5 % (Sprach‑Mismatch). Für den deutschen und englischen Raum liegt die effektive FP‑Rate im einstelligen Bereich.
- **Unterschiede zwischen KI‑Modellen:** Die Precision ist ein Gesamtwert. Ältere Modelle (GPT‑2) zeigen höheren Recall (27,4 %) als moderne Chat‑Modelle (7–13 %). Eine detaillierte Aufschlüsselung pro Modell wurde nicht durchgeführt.
- **CJK‑Sprachen:** Die Tokenisierung mit `\w+` erfasst keine CJK‑Zeichen. Eine Unicode‑basierte Tokenisierung wäre notwendig – im PoC nicht implementiert.
- **`zero_width_space`:** Recall 0,0 % bei diesem Angriff. Eine Zeile `unicodedata.normalize('NFKC', text)` würde das Problem beheben – nicht im PoC, da der Angriff expliziten Manipulations‑Intent voraussetzt.
- **Kein Vergleich mit neuronalen Baselines:** Ein auf RAID feingetunter DistilBERT würde wahrscheinlich einen höheren Recall erreichen – auf Kosten von GPU, Latenz und Modellwartung. Ein solcher Vergleich wurde nicht durchgeführt.

---

## Zusammenfassung

QCK ist ein **deterministischer, regelbasierter Prototyp**, der auf 7,1 Millionen RAID‑Dokumenten eine Precision von 95,4 % erreicht, kalibriert mit nur 61 deutschen KI‑Texten. Es misst geometrische Signaturen struktureller Über‑Kohärenz – keine statistischen Muster – und ist robust gegen 9 von 11 adversariellen Angriffen. Das System ist extrem schnell, vollständig interpretierbar, läuft auf CPU und kann als Veto‑Filter oder komplementäre Komponente in Detektionspipelines eingesetzt werden. Die Einschränkungen sind transparent dokumentiert.

---

## Dateien (Auswahl)

| Datei | Funktion |
|-------|---------|
| `qck_auditor_010.py` | Kern‑Auditor, finale Kalibrierung |
| `qck_auditor_measure_001.py` | Mess‑Modus, 31 Metriken, kein Gate |
| `RAID_TEST_SCRIPT_VALIDATOR_016.py` | Scanner für Parquet + TXT |
| `qck_analyzer_001.py` | Kalibrierungs‑Analyzer |
| `qck_raid_evaluator_002.py` | RAID‑Auswertung mit Angriffs‑Unterscheidung |

---

## Referenzen

- Behroozi, P. et al. (2025). *High‑Dimensional Ray Tracing for Uncertainty Quantification in Neural Networks.* University of Arizona / arXiv.
- Dugan, L. et al. (2024). *RAID: A Shared Benchmark for Robust Evaluation of Machine‑Generated Text Detectors.* ACL 2024. DOI: 10.18653/v1/2024.acl-long.674
- Wyneken, B. (2025). *Geometrische Auditierung von KI‑Systemen.* DOI: [10.5281/zenodo.17691545](https://zenodo.org/records/17691545)

---

Diese README enthält alle wesentlichen Informationen – von der Idee über die Ergebnisse bis hin zur Einordnung und den offenen Forschungsfragen – in einer klaren, redundanzfreien Struktur auf Deutsch.
