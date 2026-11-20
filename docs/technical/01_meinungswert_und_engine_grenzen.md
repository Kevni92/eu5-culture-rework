# Technik 1: Gerichteter Kulturmeinungswert und Engine-Grenzen

**Gameplay-Konzept:** [Kontinuierliche Kulturbeziehungen](../concept/01_kulturbeziehungen.md) · [Gameplay-Folgen](../concept/05_auswirkungen_und_offene_fragen.md)

**Analyse:** 20.11.2026 · **Quellenstand:** öffentlicher Textspiegel `HLJSXK/eu5-modding-base`, Commit `9e941cedccf963921733810a041ba912b6b8ceb7` (**1.4.3 beta**). Enthaltene `reference_official_defines/docs/`-Dumps sind referenziert, aber ihr exakter Build gegenüber dem Gamefile-Sync ist nicht separat verifiziert. **Kein Nachweis für die aktuelle Spielversion oder einen Laufzeittest.**

## Kurzurteil

**Ein zusätzliches gerichtetes, kontinuierliches Meinungssystem erscheint skripttechnisch realistisch. Der vollständige Ersatz des eingebauten Kultursystems ist bislang nicht belegt und hat ein hohes Engine-Risiko.**

Wichtig sind **zwei verschiedene Vanilla-Systeme**, die getrennt ersetzt werden müssten:

1. **Kulturmeinung (culture A → culture B):** fünf diskrete Werte (`enemy`, `negative`, `neutral`, `positive`, `kindred`).
2. **Kulturstatus im Land:** Primärkultur bzw. akzeptiert, toleriert, ignoriert oder diskriminiert. Diese Zustände beeinflussen Kulturkapazität, Assimilation und Pop-Regeln.

Ein kontinuierlicher Wert allein ersetzt nicht automatisch das zweite System.

## 1. Native Kulturmeinung: vorhanden, aber stufenbasiert

**Befund: im Referenzstand belegt.**

- Kulturen definieren gerichtete Startmeinungen über `opinions`. Beispielsweise enthält die andalusische Kultur eine negative Startbeziehung zur kastilischen Kultur als `enemy`.
- Der Engine-Effekt `change_cultural_view` verändert die Meinung um eine diskrete Stufe; `set_cultural_view` setzt einen `relation_level` (in Vanilla z. B. `kindred`). Beide arbeiten im Kultur-Scope.
- `cultural_view` prüft eine bestimmte Meinungsstufe. Der numerisch auswertbare `culture_opinion_impact` ist ein **abgeleiteter Meinungs-Einfluss**, kein belegbarer frei schreibbarer −100…+100-Wert.
- Die UI- und diplomatischen Standardaktionen benutzen ausdrücklich die diskreten Zustände.

**Konsequenz:** Ein kontinuierlicher Wert kann nach heutiger Quellenlage **nicht direkt an die Stelle des nativen Relation-Enums geschrieben werden**. Eine Mod müsste einen eigenen Wert als neue Quelle der Wahrheit führen. Ob ein unbekannter aktueller Engine-Zugang doch numerische Beziehungen unterstützt, bleibt bis zum aktuellen Dump offen.

**Belege:**
- [Kulturdefinitionen: `cultures/iberian.txt`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/cultures/iberian.txt)
- [Engine-Effekte: `change_cultural_view`, `set_cultural_view`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/effects.log)
- [Engine-Trigger: `cultural_view`, `culture_opinion_impact`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/triggers.log)
- [Vanilla-Aktion: `improve_our_cultural_view.txt`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/generic_actions/improve_our_cultural_view.txt)
- [Diplomatie: `improve_cultural_view.txt`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/country_interactions/improve_cultural_view.txt)

## 2. Eigene gerichtete Zahlenwerte speichern

**Befund: grundlegende Bausteine belegt; Kulturpaar-Persistenz noch ungetestet.**

- Der Engine-Dump dokumentiert `set_variable` (u. a. numerische Werte) und `change_variable` (arithmetische Änderungen). Die Effekte werden für `none/any` aufgeführt.
- `add_to_variable_map`, `remove_from_variable_map` sowie `every_key_in_variable_map` und `is_key_in_variable_map` ermöglichen Schlüssel-Wert-Verwaltung und Iteration.
- In veröffentlichten Community-Mod-Dateien existieren **numerische** Map-Werte, Script-Value-Zugriffe über `variable_map(...)` und ein Update-Muster aus **Entfernen + erneutes Einfügen**. Ein weiteres Mod-Beispiel speichert Beziehungen Land → Land mit Zählwerten.
- Das macht ein Modell **„auf Kultur A eine Map mit Zielkultur B als Schlüssel und numerischem Meinungswert“** zum naheliegenden Prototyp. Die beiden Richtungen wären separate Einträge auf A und B.

**Noch nicht belegt:**

1. Akzeptiert die Map eine **Kultur als Schlüssel**, und lassen sich Maps sicher direkt auf einem **Culture-Scope** halten?
2. Funktionieren numerische Werte, negative Zahlen, Nachkommastellen, Lesen und Aktualisieren **in genau diesem Scope**?
3. Werden die Einträge zuverlässig in **Savegames** gespeichert und geladen? Was geschieht bei neu erzeugten oder zusammengeführten Kulturen?
4. Ist die Iteration über tatsächlich registrierte Kulturpaare kostengünstig genug, um diese **monatlich** zu aktualisieren?
5. Können GUI und andere Scriptbereiche den neu gespeicherten Wert ohne aufwendige Kontextwechsel lesen?

**Einschränkung:** `none/any` im Dump belegt die allgemeine Script-Oberfläche, **nicht automatisch** das Laufzeit- und Persistenzverhalten für alle Objekttypen. Außerdem beschreibt der Map-Effekt „event targets“, während die Community numerische Werte benutzt; maßgeblich ist ein Runtime-Test im passenden Build.

**Belege:**
- [Engine-Effekte und Iteratoren](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/effects.log)
- [Engine-Trigger für Maps](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/triggers.log)
- [Community-Mod: numerische Maps und Remove/Add-Update](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_mods/3739393770/in_game/common/scripted_effects/dmsd_utils.txt)
- [Community-Mod: numerische Länderbeziehungs-Map](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_mods/3696243603/in_game/common/scripted_effects/atd_effects.txt)

## 3. Kulturstatus und Kulturkapazität

**Befund: starke Hinweise auf engineverwaltete Mechanik; freier per-Kultur-Kosten-Hook nicht belegt.**

- Vanilla verwaltet akzeptierte, tolerierte und diskriminierte Kulturen über eigene Engine-Effekte. `demote_accepted_culture` wird sogar als Statuswechsel mit **Pop-Status-Update** dokumentiert.
- `used_cultures_capacity` ist ein lesbarer Länder-Trigger und beschreibt ausdrücklich die Kosten akzeptierter und tolerierter Kulturen.
- Die `NCulture`-Defines enthalten globale Basiskosten `ACCEPTED_CULTURE_BASE_COST` und `TOLERATED_CULTURE_BASE_COST` sowie feste Faktoren für Sprache, Kulturmeinungsstufen und Bevölkerungsgrößen.
- Die Modifikatoren `accepted_culture_maintenance_cost_modifier` und `tolerated_culture_maintenance_cost_modifier` sind **länderweit** definiert. Sie belegen keinen einzelnen, dynamisch berechenbaren Unterhaltswert je Kulturpaar.
- Das Überschreiben der normalen Kulturaktionen kann neue manuelle Statuswechsel verhindern; es **ersetzt weder die interne Kapazitätsberechnung noch automatisch die vorhandenen AI-, UI- und Pop-Abhängigkeiten**.

**Konsequenz:** Ein exakter, jeden Monat fließend aus der neuen Meinung berechneter **Kulturkapazitätsverbrauch pro Kultur** ist mit den bisher identifizierten Script-Einstiegspunkten **nicht nachgewiesen**. Nationale Modifier oder veränderte globale Defines sind mögliche Annäherungen, aber kein vollständiger Ersatz.

**Belege:**
- [Vanilla-Kulturaktionen](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/generic_actions/culture.txt)
- [`NCulture`-Defines](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/loading_screen/common/defines/00_defines.txt)
- [Engine-Effekte: Kulturstatus](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/effects.log)
- [Engine-Trigger: `used_cultures_capacity`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/triggers.log)
- [Modifier-Katalog](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/modifiers.log)

## 4. Pop-Zufriedenheit, Assimilation und Rechte

**Befund: einzelne Effekte änderbar; vollständige kontinuierliche Kopplung offen.**

- Die `NCulture`-Defines enthalten **statusabhängige Assimilationsfaktoren** sowie bereits Meinungsfaktoren für die Assimilation in beide Richtungen.
- `add_pop_satisfaction` ist als Pop-Effekt dokumentiert; ein gezielter Pop-Zufriedenheitsschub wäre also prinzipiell scriptbar.
- Solche Impulse sind **nicht dasselbe** wie eine laufende, automatisch berechnete Zufriedenheitsbasis je Kulturpaar.
- Statusabhängige Rechte, Levy-/Ämterregeln und weitere Systeme können weiterhin auf den nativen Kulturstatus zugreifen. Für sie ist kein einheitlicher frei parametrisierbarer Ersatz-Hook nachgewiesen.
- Denkbare Ersatzlösungen über periodische Pop-Updates riskieren erhebliche **Performancekosten** und unbeabsichtigte Wechselwirkungen.

**Konsequenz:** Teilintegration ist plausibel; eine vollständig konsistente Neuberechnung **aller** kulturellen Pop-Auswirkungen bleibt ein eigener Engine-Prüfblock.

**Belege:**
- [`NCulture`: Assimilation](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/loading_screen/common/defines/00_defines.txt)
- [Engine-Effekt: `add_pop_satisfaction`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/effects.log)

## 5. Umsetzungsvarianten (keine Implementierungsfreigabe)

| Variante | Erwartete Machbarkeit | Kompromiss |
|---|---|---|
| **A. Eigenes Meinungssystem („Sidecar“)** | **Plausibel**, nach Kultur-Map-Test | −100…+100 und monatliche Entwicklung als eigene Simulation; native Status-/Kostenlogik läuft zunächst weiter |
| **B. Hybrid mit Übersetzung in Vanilla-Stufen** | **Technisch naheliegend**, nicht getestet | Neuer Wert entscheidet gelegentlich über die fünf nativen Meinungsstufen und ggf. Kulturstatus; **harte Sprünge und Vanilla-Abhängigkeiten bleiben**, widerspricht dem Ideal teilweise |
| **C. Vollständiger Ersatz der nativen Mechanik** | **Hohes Risiko / offen** | Erfordert nachweisbar ersetzbare per-Kultur-Kosten, Pop-Effekte, Kulturstatuslogik, AI und GUI; derzeit kein vollständiger Scriptweg belegt |

**Empfehlung für die Machbarkeitsprüfung:** A zuerst als **kleinen isolierten Prototyp**, C als gewünschte Zielarchitektur beibehalten. Nicht vorschnell auf B als endgültiges Gameplay festlegen.

## 6. Präzise nächste Experimente (in Reihenfolge)

| Priorität | Test | Bestanden, wenn … |
|---|---|---|
| **P0** | Kultur A besitzt numerische Map mit Kultur B als Schlüssel | Wert −100…+100 kann aus beliebigem Scriptkontext gelesen, geändert und gespeichert/geladen werden; die Gegenrichtung ist unabhängig |
| **P0** | Registrierte Paare iterieren | Monatliches Update benötigt **keinen vollständigen Culture×Culture-Scan** und verarbeitet korrekt nur vorhandene Schlüssel |
| **P1** | Per-Kultur-Kapazitätskosten im aktuellen Dump prüfen | Konkreter Hook für Berechnung/Override **pro Land und Zielkultur** gefunden; falls nicht, als **nicht nachgewiesen** dokumentieren |
| **P1** | Verhalten bei Statuswechseln und Berechnungsupdates | Kein erzwungener Statuswechsel und keine fehlerhafte Neuberechnung der Kulturkosten |
| **P2** | Zufriedenheit, Assimilation und Rechte durch Meinung beeinflussen | Nachweislich **kulturspezifische** Wirkung ohne Pop-Vollscan oder Doppelzählung |
| **P2** | AI und GUI prüfen | Angezeigte Werte, AI-Entscheidungen und interne Kosten stimmen überein |
| **P3** | Stress-Test | Speichern/Laden, Kulturwechsel, verschwundene Kulturen und große multiethnische Reiche funktionieren ohne relevante Scriptzeit-Spikes |

Für alle Tests: **Spielbuild und `script_docs`/Vanilla müssen versionsgleich sein**; jeweils Verhalten, Log und Performance messen.

## Entscheidung

**GO für einen gezielten Speicher-/Iteration-Prototyp eines zusätzlichen kontinuierlichen Kulturmeinungswerts. NO-GO für die Behauptung, der vollständige Vanilla-Ersatz sei bereits machbar.** Besonders die dynamischen Kosten je Kultur und die automatische kulturspezifische Pop-Wirkung sind offene Kernfragen.
