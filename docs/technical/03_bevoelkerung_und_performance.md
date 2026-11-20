# Technik 3: Bevölkerungsgewichtung, Relevanzfilter und Performance

**Konzept:** [Berührungspunkte und Relevanz](../concept/04_beruehrungspunkte_und_relevanz.md).

## Zwei getrennte Fragen

**Relevanz für den Staat:** Wie groß ist die Zielkultur **im Verhältnis zur Gesamtbevölkerung des Landes**? Diese Quote sollte zentral darüber entscheiden, ob der Staat einen laufenden Einflussfaktor erhält.

**Reichweite für die globale Kultur:** Wie viele Angehörige der Zielkultur leben **in diesem Staat relativ zur Weltbevölkerung dieser Kultur**? Dieser Anteil gewichtet seinen Beitrag zum weltweiten Kulturmeinungswert.

Die **Weltquote allein darf kein Ausschlusskriterium** sein. Beispiel: 50.000 Chinesen bei 100 Mio. Chinesen weltweit = 0,05 % Reichweite; bei einer angenommenen französischen Bevölkerung von 10 Mio. = 0,5 % lokaler Anteil. Ein kleiner, aber tatsächlich vorhandener Einfluss muss möglich bleiben. Bei nur 300 Personen kann eine Berechnung ggf. entfallen.

## Vorschlag für effiziente Prüfung

- Relevanzfilter zunächst anhand der **lokalen Bevölkerungsquote**; eine **absolute Mindestgröße** kann sehr kleine Gruppen zusätzlich ausklammern. Konkrete Grenzen bleiben Balancing-/Benchmark-Parameter.
- Globale Quote als **Gewichtung nach dem Relevanztest**, nicht als zweite zwingende Eintrittsschwelle.
- Kulturbevölkerung pro Land und weltweit **jährlich aggregieren**; nicht jeden Monat alle Pops iterieren.
- Monatlich lediglich **aktive gerichtete Kulturpaare** und ihre bereits bekannten Länderbeiträge bearbeiten.
- Relevante Änderungen über On Actions als **Neubewertungsbedarf** markieren; jährlicher Audit findet verpasste Veränderungen.
- Keine unbedachte Berechnung aller Kulturkombinationen oder wiederholtes Traversieren aller Locations/Pops je Länderpaar.

## Vorhandene Script-Belege

`culture_percentage_in_country` und `culture_population_in_country` sind in den Vanilla-Kulturaktionen verwendet. Damit sind **länderspezifische** Quoten und Größen grundsätzlich als Script-Ausdrücke nachgewiesen. **Ergänzung 20.11.2026:** Im älteren Engine-Dump ist auch der numerische **Trigger** `world_culture_population` (`none/any`, Kulturparameter) nachgewiesen. Seine Verwendung als direkt auslesbarer **Script-Value** zur Quotientenberechnung ist dagegen weiterhin zu testen. Persistente globale Aggregate/Iteration und Performance sind noch nicht verifiziert.

## Zu prüfen

1. Ausdrücke für weltweite Kulturbevölkerung und Landes-Gesamtbevölkerung in aktuellen `script_docs` / Vanilla finden.
2. Kosten einer Abfrage, globalen Kulturiteration und möglicher Variable-Maps messen.
3. Persistent gespeicherte aktive Kulturpaare, Umstellung auf „ruhend“ und Ladekonsistenz testen.
4. Zwei Szenarien benchmarken: kleine Kulturen in wenigen Ländern und weit verbreitete Diasporas.
5. Schwellenwerte auf **falsche Ausschlüsse** testen, besonders bei 50.000 vs. 300 Personen.

**Status:** Lokale Quoten **belegt im alten Stand**; globaler Cache, Paar-Register und akzeptable Performance **offen**.

## Referenzen

- [Vanilla: generic_actions/culture.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/generic_actions/culture.txt)
- [Vanilla: on_action/_hardcoded.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/on_action/_hardcoded.txt)
