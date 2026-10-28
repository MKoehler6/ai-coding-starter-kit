
**Datum:** 27.10.2026
**Status:** Question Storming abgeschlossen – nächster Schritt: Mind Mapping

---

## Projektidee

Ein persönliches Tool, das täglich Stellenanzeigen nach Stichworten (z.B. Informatiker, Python) sucht. Dabei kann eine Region vorgegeben werden oder deutschlandweit. 
Die Stellen sollen gespeichert werden mit Firma, Überschrift, Link und benötigte Skills. Jeden Tag soll automatisch ein Agent diese Suche übernehmen. Duplikate werden herausgefiltert. Mit der Zeit soll ein Trend errechnet werden, ob die Anzahl der Stellen mit diesem Stichwort zunehmen oder abnehmen

## Rahmenbedingungen

- **Nutzung:** Persönliches Tool, kein kommerzielles Produkt
- **Stack:** Python Backend, React Frontend, LLM-gestützte Analyse, PostgresDB
- **Deployment:** Linux Server
- **Energie:** Breit explorativ → Projektkonzept, dann technischer Aufbau

---

## Geplante Brainstorming-Phasen

### Phase 1 – Konzept
1. Question Storming ✅
2. Mind Mapping ← *hier sind wir gerade*
3. SCAMPER

### Phase 2 – Technik
4. Morphologische Analyse
5. Failure Analysis

---

## Question Storming – bisherige Fragen

### ✅ Bereits diskutiert

**F1: Woher kommen die Stellenanzeigen?**
- Mehrere Quellen (z.B. Indeed, LinkedIn, StepStone, Arbeitsagentur, Klimajobs.de, greenjobs.de, nachhaltigejobs.de, jobverde.de, Glassdoor – Liste wird gemeinsam erstellt)

Probleme bei Quellen:
- ab Seite 2 oder 3 Suchergebnisse kommt Überprüfung: Ich bin ein Mensch
- brauche ich einen Account auf der jeweiligen Seite?
- Offene Frage: Wie reagiert das Tool auf Strukturänderungen bei den Quellen?

**F2: Was ist ein "Skill"?**
- ✅ Nur **Hard Skills**: Programmiersprachen, Informatik-Tools
- ✅ **Fachspezifische Fähigkeiten**: z.B. GIS, Biologie, Geografie
- ❌ Keine Soft Skills (z.B. Teamfähigkeit)
- Grenzen werden iterativ zusammen mit dem LLM geschärft

**F3: Was gilt als Duplikat?**
- ✅ Gleiche Anzeige auf verschiedenen Portalen
- ✅ Eine Stelle, mehrere Orte → zählt als **eine** Stelle
- ❌ Neu eingestellte Anzeige (Repost) gilt **nicht** als Duplikat → wird als neue Stelle gezählt
- ❌ Personalvermittler-Anzeigen werden nicht gesondert abgeglichen
- Hinweis: Reposts können den Trend leicht nach oben verzerren (bewusst in Kauf genommen)

**F4: Umgang mit Duplikaten**
- ✅ Verwerfen: der zuerst gefundene Eintrag bleibt, weitere Treffer werden ignoriert

**F5: Lebenszyklus einer Stelle**
- ✅ Nur Funddatum, kein "zuletzt gesehen"
- Folge: Trend = Anzahl **neuer** Stellen pro Zeitraum

**F6: Wie wird eine Region angegeben?**
- ✅ Stadt + Umkreis in km (z.B. "Leipzig, 50 km")

**F7: Wie wird Remote behandelt?**
- ✅ Remote-Stellen werden immer eingeschlossen, unabhängig von der Region

**F8: Trend & Region**
- ✅ Suche läuft deutschlandweit pro Stichwort, Region ist nur ein Anzeigefilter
- Folge: Jeder Stellenort muss in Koordinaten umgerechnet werden (Geocoding), damit sich der Umkreis nachträglich filtern lässt
- Folge: Es fallen mehr Daten pro Lauf an (relevant für Captcha/Rate-Limits aus F1)

**F9: Was zeigt das Dashboard?** (alles gewünscht)
- ✅ Trend-Diagramm (neue Stellen pro Zeitraum je Stichwort)
- ✅ Stellenliste (filter- und durchsuchbar)
- ✅ Skill-Ranking (häufigste Skills und deren Entwicklung)
- ✅ Karte (nutzt die Geocoding-Koordinaten)

**F10: Welches LLM?**
- ✅ Cloud-API (z.B. Claude), Kosten bei geringem Volumen überschaubar

**F11: Wie oft läuft der Agent?**
- ✅ 1x täglich nachts (Cron/systemd-Timer)

**F12: Wird der Volltext gespeichert?**
- ✅ Ja, damit Anzeigen neu ausgewertet werden können, wenn sich die Skill-Definition ändert (siehe F2)

**F13: Wie lange werden Daten aufbewahrt?**
- ✅ Unbegrenzt

**F14: Benachrichtigungen?**
- ✅ Keine, nur das Dashboard

---

## Offene Fragen (für Phase 2)

- Wie reagiert das Tool auf Strukturänderungen bei den Quellen? (aus F1)
- Wie geht das Tool mit Captchas und Account-Pflicht um? (aus F1)
- Woran wird ein Cross-Portal-Duplikat konkret erkannt? (z.B. normalisierte Firma + Titel, oder Ähnlichkeitsprüfung per LLM)
- Welcher Geocoding-Dienst wird genutzt? (z.B. Nominatim/OpenStreetMap)
- Wie wird "Remote" zuverlässig erkannt? (LLM-Extraktion)

---

## Nächste Schritte (beim Fortsetzen)

1. Mind Mapping der Kernkonzepte (Quellen, Scraping, Duplikate, LLM-Extraktion, Geocoding, Speicherung, Trend, Dashboard)
2. SCAMPER
3. Phase 2: Morphologische Analyse (offene Fragen hier klären)
