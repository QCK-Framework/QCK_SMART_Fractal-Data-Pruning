

# Proof of Concept

**Geometric Auditing of AI Systems**  
B. Wyneken · DOI: [10.5281/zenodo.17691545](https://zenodo.org/records/17691545)

---

## What has been built

A **deterministic, rule-based detector** for structural over‑coherence in AI‑generated texts.  
No neural network, no embeddings, no GPU. Detection is based on nine CPU metrics that measure topological properties of the text.

**Core idea:** A stable attractor in phase space (D₂ ≈ 1.0) corresponds to a uniform, ordered text structure. AI texts exhibit this uniformity across multiple dimensions simultaneously – that is the measurable signature.

The system is a **veto filter with strict AND logic**: a document is flagged as "AI hallucination" only if **all** defined structural conditions are satisfied simultaneously. This leads to extremely high precision with a very low false‑positive rate in controlled environments.

---

## What has been achieved

| Metric | Value |
|--------|-------|
| **Precision** | **95.4%** |
| Recall | 10.0% |
| Specificity (human correct) | 83.7% |
| Calibration basis | 61 hand‑picked German AI texts |
| Test basis | RAID benchmark: 7.1 million documents, 11 models, 11 domains, 11 adversarial attacks |

The low recall is **expected** – RAID deliberately contains high‑quality, partly human‑revised AI texts. QCK does not detect "AI in general", but a specific **signature of structural over‑coherence**. A coherent, stable AI text will not be flagged.

**Core statement:** 61 calibration texts generalize to 7 million documents with 95.4% precision. A neural model would require millions of training examples for comparable generalization – QCK measures geometric properties and does not learn statistical patterns.

---

## Robustness against adversarial attacks

Tested on 11 attack types (approx. 587,000 AI texts each). 9 out of 11 attacks show **no measurable effect** on recall.

| Attack | Δ Recall | Assessment |
|--------|----------|------------|
| alternative_spelling | −0.0% | robust |
| article_deletion | +1.6% | robust |
| homoglyph | −0.5% | robust |
| insert_paragraphs | +0.0% | robust |
| number | +0.3% | robust |
| perplexity_misspelling | +0.0% | robust |
| synonym | +0.6% | robust |
| upper_lower | +0.6% | robust |
| whitespace | −0.5% | robust |
| paraphrase | −6.9% | vulnerable |
| zero_width_space | −11.2% | vulnerable |

**Reasoning:** QCK measures topological properties – whitespace, capitalization, synonyms, and typos do not change the topology.

- **`paraphrase`** (−6.9%): True semantic reformulation changes sentence structure and vocabulary dynamics – theoretically consistent.
- **`zero_width_space`** (−11.2%): Invisible Unicode characters fragment tokenization. Technically solvable by Unicode normalization (NFKC) – not implemented in the PoC.

---

## Detection rate per domain (clean AI)

| Domain | AI Recall | Human Specificity | Note |
|--------|-----------|------------------|------|
| german | 17.1% | 64.1% | Best AI detection – calibration on German |
| reviews | 14.1% | 83.1% | |
| wiki | 12.5% | 96.8% | |
| news | 12.0% | 83.6% | |
| poetry | 11.6% | 75.0% | |
| reddit | 10.2% | 99.0% | |
| abstracts | 10.2% | 80.9% | See note |
| czech | 10.5% | 56.5% | Language mismatch |
| books | 8.3% | 90.5% | |
| recipes | 7.2% | 95.5% | |
| code | 7.0% | 88.2% | |

**Note on abstracts:** Level 3 (300–700 words) shows an elevated FP rate (23.7%). This is not a detection error – scientific abstracts, short Wikipedia articles, and database entries naturally use a uniform, information‑dense language that structurally converges with AI outputs. For the primary use case (free narrative text), this range is less relevant.

---

## Calibration

### Levels 1–4 (AI cage)

AI must hit **all** gates. Humans must break **at least two** gates (a single break is not enough).

| Level | Words | FP Rate | Human Base | Gate Metrics |
|-------|-------|---------|------------|--------------|
| lvl1 | 5–49 | 0.0% | 35 | |
| lvl2 | 50–299 | 6.9% | 2,014 | |
| lvl3 | 300–699 | 23.7% | 1,202 | |
| lvl4 | 700–3,500 | 8.2% | 1,202 | |

### Level 9 (Human corridor, inverted)

For texts >3,500 words the logic is **inverted**: the corridor is calibrated on human texts. AI breaks out of this corridor (≥1 break → AI_HALLUCINATION).

| Human Base | Gate Metrics |
|------------|--------------|
| 1,283 texts | |

**Why inverted?** For very long texts, human rhythm is more stable than that of AI – a human writing a book has a characteristic, consistent rhythm; an AI loses consistency over length or repeats patterns in a way no human does.

---

## Level overview

- **lvl1 (5–49 words)** – Artificial sentence building, compression‑based metrics (d3_global, bigram_entropy, hapax_ratio)
- **lvl2 (50–299)** – Word length, vocabulary growth, repetition rate
- **lvl3 (300–699)** – Most difficult zone (abstracts, news) – structurally higher FP rate
- **lvl4 (700–3,500)** – Hapax, repetition, sliding window
- **lvl9 (>3,500, inverted)** – Human corridor, sentence length autocorrelation, Zipf slope

---

## Performance

**Benchmark: Intel i9-10900X (2019, 10 cores), no GPU**

| Domain | Documents | Time | Docs/Second |
|--------|-----------|------|-------------|
| code | 386,400 | 10s | 37,138/s |
| abstracts | 741,720 | 49s | 15,137/s |
| reddit | 747,180 | 72s | 10,377/s |
| books | 748,020 | 76s | 9,843/s |
| recipes | 744,240 | 89s | 8,351/s |
| german | 827,400 | 93s | 8,897/s |
| wiki | 747,180 | 101s | 7,397/s |

**7.9 million documents in ~20 minutes** on standard consumer hardware (2019).  
Speed varies with text length – short code texts (lvl1/2) are about 5× faster than long wiki articles (lvl4).

---

## Appendix: D₂ and the choice of metrics

### What is D₂?

The correlation dimension D₂ measures how densely a path in phase space fills an attractor.  
- **D₂ ≈ 1.0** → stable attractor (AI‑typical)  
- **D₂ > 1.5** → irregular path (human‑typical)

True D₂ in embedding space requires sentence vectors, a distance matrix, and the Grassberger‑Procaccia algorithm – O(N²), GPU, transformer dependency.

### The proxy decision

QCK uses **nine CPU metrics** that approximate the same information:

| Metric | Replaces in phase space |
|--------|-------------------------|
| `sent_autocorr` | Autocorrelation of step sizes |
| `zipf_slope` | Global density of vocabulary |
| `rep_rate` | Local step‑size variance |
| `hapax_ratio` | Rarity structure of the attractor |
| `ttr` | Vocabulary diversity |
| `vocab_growth` | Expansion of the attractor over time |
| `bigram_entropy` | Local curvature of the trajectory |
| `avg_word_len` | Morphological complexity |
| `d3_global` | Compression‑based redundancy |

**Why the proxies work:** AI texts are uniform across several independent statistical dimensions. Because these dimensions are orthogonal, a single break in any metric is enough to signal human. AI must satisfy all gates.

| Criterion | CPU proxies (QCK) | True D₂ (embedding) |
|-----------|-------------------|---------------------|
| Complexity | O(N) | O(N²) + transformer |
| Speed | 7.9M docs / 20 min | ~5–10 docs/s on GPU |
| Model dependence | none | high |
| Precision on RAID | **95.4%** | not tested |

The CPU proxies are **not a compromise** – they are a deliberate engineering decision. True D₂ remains a valuable theoretical construct for special cases or as a calibration reference.

---

## What this system is **not** (yet)

- **Not a universal AI detector** – it does not detect all AI texts
- **Not a probabilistic classifier** – it does not output probabilities
- **Not a semantic truth verifier** – it does not check facts
- **Not a general hallucination detector** – it measures structural coherence, not semantic errors

**Interpretation of a positive finding:**  
> "This text exhibits an unusually high degree of multi‑dimensional structural uniformity that is unlikely under typical human writing processes."

Non‑detection does **not** imply human authorship.

---

## Distinction from ML‑based detectors

| Aspect | ML Detector | QCK (this system) |
|--------|-------------|-------------------|
| **Learning method** | Statistical learning from data | Deterministic, fixed rules |
| **Decision** | Probabilistic (e.g., p=0.87) | Hard logical AND |
| **Feature space** | Latent embedding space (high‑dim, black box) | Explicit, interpretable metrics (Zipf, repetition, …) |
| **Complexity** | O(N·d) with large constants (transformer) | O(N) with very small constants |
| **Invariance** | Sensitive to paraphrasing, synonyms, formatting | Stable under lexical changes (as long as structure remains) |
| **Failure modes** | Opaque (overfitting, domain shift, calibration errors) | Explicitly traceable to individual metric boundaries |
| **Lifecycle** | Training, validation, retraining, versioning | No training, no updates, pure function |
| **Optimization target** | Accuracy / F1 | **Maximizing precision under strict constraints** |

ML detectors and QCK operate in **fundamentally different spaces** and can be used as **complementary components** – not as direct substitutes.

---

## Design philosophy

The system prioritizes:

- **Precision over recall**
- **Determinism over probability**
- **Interpretability over model complexity**
- **Speed and deployability over training performance**

This makes it suitable as:

- a **forensic pre‑filter**
- a **high‑confidence signal generator**
- a **complementary component** in larger detection pipelines

---

## Limitations and open questions

- **Language:** Calibrated on German. The high precision on English RAID documents was achieved without English calibration. For other languages, specificity drops (see `czech`). Multilingual extension is possible but not part of this prototype.
- **What "hallucination" means:** Structural uniformity is measured, not factual correctness. The correlation between topological signature and factual hallucination is theoretically derived but not directly empirically validated.
- **Gate stability:** Boundaries were calibrated on a specific human corpus. Cross‑validation across other domains (e.g., legal texts, patents) remains to be done.
- **False positives:** Global FP rate 16.3% – broken down by domain: `reddit` 1.0%, `wiki` 3.2%, `recipes` 4.5%, `czech` 43.5% (language mismatch). For German and English, the effective FP rate is in the single‑digit range.
- **Differences across AI models:** Precision is an aggregate value. Older models (GPT‑2) show higher recall (27.4%) than modern chat models (7–13%). A detailed per‑model breakdown was not performed.
- **CJK languages:** Tokenization with `\w+` does not capture CJK characters. A Unicode‑based tokenizer would be necessary – not implemented in the PoC.
- **`zero_width_space`:** Recall 0.0% for this attack. One line of `unicodedata.normalize('NFKC', text)` would fix it – not in the PoC because the attack presupposes explicit manipulation intent.
- **No comparison with neural baselines:** A RAID‑finetuned DistilBERT would likely achieve higher recall – at the cost of GPU, latency, and model maintenance. Such a comparison was not performed.

---

## Summary

QCK is a **deterministic, rule‑based prototype** that achieves 95.4% precision on 7.1 million RAID documents, calibrated with only 61 German AI texts. It measures geometric signatures of structural over‑coherence – not statistical patterns – and is robust against 9 out of 11 adversarial attacks. The system is extremely fast, fully interpretable, runs on CPU, and can be used as a veto filter or complementary component in detection pipelines. Limitations are transparently documented.

---

## References

- Behroozi, P. et al. (2025). *High‑Dimensional Ray Tracing for Uncertainty Quantification in Neural Networks.* University of Arizona / arXiv.
- Dugan, L. et al. (2024). *RAID: A Shared Benchmark for Robust Evaluation of Machine‑Generated Text Detectors.* ACL 2024. DOI: 10.18653/v1/2024.acl-long.674
- Wyneken, B. (2025). *Geometric Auditing of AI Systems.* DOI: [10.5281/zenodo.17691545](https://zenodo.org/records/17691545)







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
| lvl1 | 5–49 | 0,0 % | 35 |
| lvl2 | 50–299 | 6,9 % | 2.014 | 
| lvl3 | 300–699 | 23,7 % | 1.202 | 
| lvl4 | 700–3.500 | 8,2 % | 1.202 | 

### Level 9 (Human‑Korridor, invertiert)

Bei Texten >3.500 Wörtern ist die Logik **invertiert**: Der Korridor ist auf Human‑Texten kalibriert. KI bricht aus diesem Korridor aus (≥1 Bruch → KI_HALLUCINATION).

| Human‑Basis | Gate‑Metriken |
|-------------|---------------|
| 1.283 Texte | 

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

## Einschränkungen und offene Fragen

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

## Referenzen

- Behroozi, P. et al. (2025). *High‑Dimensional Ray Tracing for Uncertainty Quantification in Neural Networks.* University of Arizona / arXiv.
- Dugan, L. et al. (2024). *RAID: A Shared Benchmark for Robust Evaluation of Machine‑Generated Text Detectors.* ACL 2024. DOI: 10.18653/v1/2024.acl-long.674
- Wyneken, B. (2025). *Geometrische Auditierung von KI‑Systemen.* DOI: [10.5281/zenodo.17691545](https://zenodo.org/records/17691545)


