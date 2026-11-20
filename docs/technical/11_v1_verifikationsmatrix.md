# V1 – Engine-/Vanilla-Verifikationsmatrix und technische Risikogates

**Stand:** 20.11.2026 · **Quellengrundlage:** [EU5-Referenzspiegel Commit `9e941ced`](https://github.com/HLJSXK/eu5-modding-base/commit/9e941cedccf963921733810a041ba912b6b8ceb7), laut Maintainer 1.4.3 beta; generierte `reference_official_defines/docs/`-Dateien im Spiegel. **Keine Zielbuild-/Runtime-Prüfung durchgeführt.**

**Statusschlüssel:**
- **REFERENZ:** Name und (wo angegeben) Scope im alten Dump/Vanilla gefunden.
- **PROTOTYP:** konkretes Zusammenspiel, Syntax, Semantik oder Persistenz im Zielbuild offen.
- **BLOCKER:** ohne erfolgreichen Test kein sicherer weiterer Implementierungsschritt.

## 1. Pflicht-Fundstellen

- [Engine-Effekte / Iteratoren](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/effects.log)
- [Engine-Trigger](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/triggers.log)
- [Engine-on_actions](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/on_actions.log)
- [GUI-Datentypen](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/data_types_script.txt) und zum Build passende lokale `dump_data_types`
- [GUI: Kulturfenster](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/gui/culture_lateral_view.gui)
- [Vanilla: Game Rules](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/main_menu/common/game_rules/00_game_rules.txt)
- [Vanilla: Cabinet `promote_culture`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/cabinet_actions/promote_culture.txt)

## 2. Konkrete API-Prüfung

| Konstrukt | Nachgewiesener Eingangsscope bzw. Rolle | Status | Nächste Prüfung |
|---|---|---|---|
| `every_culture` | `none/any` → `culture` | REFERENZ | **Nicht** monatlich verschachtelt benutzen |
| `every_country` | `none/any` → `country` | REFERENZ | Nur Start-/Audit-Scan |
| `every_present_culture_in_country` | `country` → `culture` | REFERENZ | liefert tatsächlich relevante Minderheiten |
| `every_country_in_culture` | `culture` → `country` (nur Primärkultur!) | REFERENZ | deckt **nicht** alle Diaspora-Länder ab |
| `every_neighbor_country` | `country` → `country` | REFERENZ | Nachbarländer mit anderer Primärkultur |
| `every_country_at_war_with` | `country` → `country` | REFERENZ | mehrfache Kriegsparteien deduplizieren |
| `every_related_country` | `country` → `country` | REFERENZ | Was heißt „related“ konkret? Welche Arten sind enthalten? |
| `every_present_religion_in_country` | `country` → `religion` | REFERENZ | **Keine** Kultur×Religion-Kreuztabelle |
| `set_variable`, `change_variable` | `none/any` | REFERENZ | dauerhafte Speicherung **auf Culture-Scope** testen |
| `add_to_variable_map` / `remove_from_variable_map` | `none/any`, Event-Target-Schlüssel/Wert laut Dump | REFERENZ + PROTOTYP | numerische Werte, Kultur als Schlüssel, Save/Load |
| `every_key_in_variable_map` | `none/any` | REFERENZ + PROTOTYP | echte Schlüsseliteration aus Culture-Map, Mutation während Iteration |
| `has_variable_map` / `is_key_in_variable_map` | `none/any` | REFERENZ | schreibfreier Deduplikationscheck |
| `cultural_view` | `culture`, Ziel ist eine Kultur, nativer `relation_level` | REFERENZ | fünf Enum-Vergleiche, dynamischer Zielscope |
| `set_cultural_view` | `culture`, `target`, `value = relation_level` | REFERENZ + PROTOTYP | **P0:** A→B setzen, B→A unverändert |
| `culture_percentage_in_country` | `country` | REFERENZ | numeric trigger vs. numeric Script-Value-Zugriff unterscheiden |
| `culture_population_in_country` | `country` | REFERENZ | Anteil und absolute 1.000-Pop-Grenze |
| `world_culture_population` | `none/any`, akzeptiert Kulturparameter | REFERENZ | **Wichtig:** als numerischer Trigger gefunden; **als Script-Value-Zahl** für Quotienten erst testen |
| `has_shared_culture_group` | `culture` → `culture` | REFERENZ | beide gerichteten Basisteile identisch, Kontakt separat erforderlich |
| `opinion`, `get_opinion`, `trust` | `country` | REFERENZ | Wertebereiche und numerische Lesbarkeit |
| `is_rival_of` | `country` | REFERENZ | diplomatische Zielrichtung |
| `has_scripted_relation_of_type` | `country` → Relationstyp | REFERENZ | **nicht** automatisch bilaterale Relation mit bestimmtem Land |
| `has_game_rule` | `none/any` | REFERENZ | konkrete Game-Rule-ID und Option im Script-Kontext |
| `on_game_start` | `none/any` | REFERENZ | Verhalten bei Neuspiel, Load, Multiplayer |
| `on_game_started_after_lobby` | `none/any` | REFERENZ | **Alternative zum Start-Hook prüfen** (Community-Mod nutzt ihn für Initialisierung) |
| `monthly_country_pulse`, `yearly_country_pulse` | `country` | REFERENZ + BLOCKER | **kein belegter globaler Monatspuls**; globales Dedup und Perioden-ID |
| `on_war_declared`, `on_join_war`, `on_ending_war` | im On-Action-Dump vorhanden | REFERENZ | Eventtargets, Zeitpunkt, Mehrfachtrigger |
| `on_location_changed_owner`, `on_culture_changed` | im alten Referenzstand vorhanden | REFERENZ | markierte Länder/Kulturen, Pop-Änderung nicht vollständig abgedeckt |
| `on_scripted_relation_created` | `none/any` | REFERENZ | kein umfassender Relations-End-Hook nachgewiesen |
| `Culture.MakeScope`, `Scope.GetVariableFromVariableMap`, `Scope.GetValue` | GUI-Script-Datentypen | REFERENZ + PROTOTYP | im `ImportantCultureItem`-Kontext A/B als Kultur referenzieren |
| `progressbar` / `progressbar_framed` | GUI-Widgets | REFERENZ | dynamische Füllung von unseren Werten |
| `cabinet_actions` mit `on_activate`/`on_deactivate` | Vanilla `promote_culture` | REFERENZ | eigene Zielkultur, Laufzeit, Fähigkeit, mehrere Aktionen |

## 3. Die wichtigsten Architektur-Gates

**G1 – Persistenter gerichteter Speicher (BLOCKER):** Kultur als Map-Eigentümer *und* Culture-Key, negative feste Zahlen, Update/Remove/Add, unabhängig gerichtete Werte, Save/Load und GUI-Lesbarkeit nachweisen. Wenn unmöglich: alternative Speicherung (globales oder eigenes persistentes Registry-Objekt) gesondert evaluieren. Kein globales Kulturpaar-Kartesisches Produkt.

**G2 – Zuverlässiger Takt (BLOCKER):** Im alten Dump gibt es Monats- und Jahrespulse **pro Land**, keinen nachgewiesenen globalen Monatspuls. Eindeutige periodenbezogene Verarbeitung aller eingetragenen Kulturpaare sicherstellen: globaler Periodenmarker, zuverlässiger Kalender-/Zeitzugriff oder gleichwertiger Testmechanismus. Save/Load und tag-wechselnde Reiche berücksichtigen.

**G3 – Native Synchronisierung (BLOCKER):** `set_cultural_view` für ein reales A→B testweise ausführen und prüfen, dass das Ergebnis sofort in GUI/Gameplay wirksam wird, ohne B→A, Kulturakzeptanz oder Kulturkapazitätsregeln direkt umzuschreiben.

**G4 – Einflüsse und numerische Script-Values:** Numerische Vanilla-Trigger (`world_culture_population`, `opinion`, `trust`) lassen sich **nicht automatisch** als frei kombinierbare Script-Value-Ausdrücke behandeln. Für jeden Wert konkrete Syntax + Script-Scope + Vanilla-Beispiel nachweisen; sonst einmal pro Update mittels korrekt lesbarer Effects aggregieren.

**G5 – Religion:** Keine direkte `Kultur × Religion`-Matrix im gefundenen Dump nachgewiesen. Erst globales/nationales Pop-/Religion-Aggregationsschema auf Scope und Kosten testen; reine Länderreligionsquoten ersetzen die Kulturverteilung nicht. Keine stillschweigende semantische Vereinfachung.

**G6 – Kabinett:** Kulturwahl und persistente aktive Aktion im aktuellen Cabinet-Schema testen. Ein Cabinet-Effekt auf ein **gerichtetes Kulturpaar** ist nicht bereits durch `promote_culture` bewiesen. Beim Entfernen den Zielwertbeitrag sicher zurücknehmen.

**G7 – Religion/Krieg und Updatezeit:** Jährliche Cache-Daten + monatliche Meinungsaktualisierung sauber trennen. Kurzlebige Ereignisse im jährlichen Modus korrekt erfassen oder als abweichendes Verhalten dokumentieren.

**G8 – GUI und Tooltip:** Quelle A und Ziel B in beiden Zellen auflösen; keine Berechnung pro Hover; `our`-Sofortbutton entfernen, aber `status`-Buttons beibehalten. Ein Community-Tooltip zeigt verschachtelte Hover-Werte; unser konkretes Widget ist weiterhin Prototyp.

## 4. Modifikatoren versus Script-Values

### Belegt

- Vanilla definiert echte Modifier mit Scope-Kategorien (z. B. `country`, `religion`).
- `add_country_modifier` ist verfügbar, wirkt aber **landesweit**.
- `monthly_creditworthiness_change` ist ein **Country-Modifier**, **kein** Muster für einen dynamisch registrierbaren Culture-Pair-Modifier.
- Das echte Bonitätssystem hat `CREDITWORTHINESS_TARGET_BASE` und `CREDITWORTHINESS_MONTHLY_PULL_RATE`, die V1-Formel ist davon inspiriert.

### V1-Entscheidung

**Kein** `main_menu/common/modifier_type_definitions/crw_...` und **kein** `add_culture_modifier` erfinden.

Stattdessen `script_values/crw_target_values.txt` und `scripted_effects/crw_target_effects.txt` einsetzen: **numerische Zielwert-Beiträge** in begrenzten, gerichtet gespeicherten Caches. Die GUI zeigt einen **modifier-artigen Breakdown**, keinen nativen Modifier-Tooltip, dessen Engine-Funktion nicht belegt ist.

**Wichtig:** `script_values` speichern nicht automatisch Zustand und erzeugen nicht automatisch UI-Breakdowns. Script-Value-Berechnung, persistente Map-Caches und Tooltip-Darstellung sind **drei getrennt zu implementierende Aufgaben**.

## 5. Noch nötige genaue Spielbelege

1. **Version:** Spielversion, Checksum, Steam-Build; synchronisierte `script_docs`, `dump_data_types`.
2. **Mod-Root:** Launcher-Metadaten, Locale-Ordner/Encoding, Game-Rule-Form, On-Action-Override-Semantik.
3. **Datenträger:** Culture-Variable-Maps mit FixedPoint und Kultur-Key; globale Persistenz.
4. **Zeit:** zuverlässiger Monats-/Jahres-Periodenwert oder eine zentrale Engine-Eventquelle.
5. **Kulturstatus:** tatsächliche dynamische `set_cultural_view`-Syntax.
6. **Game Rules:** konkrete Option-Flags und Lesbarkeit durch `has_game_rule`.
7. **Numerik:** Welt-Kulturbevölkerung und Länder-Kulturbevölkerung als **Script-Values**, nicht nur Trigger.
8. **Diplomatie:** bilaterale Beziehungen (Bündnis, Meinung, Vertrauen), korrekte Einheiten und Richtung.
9. **Religion:** Kreuztabelle Kultur/Religion und staatliche Religionspolitik ohne globalen Pop-Vollscan je Paar.
10. **Kabinettsaktion:** Ziel-Scope, Dauer, Tick/On-Activate, GUI-Wahl, AI, Deaktivierung.
11. **GUI:** Datenbindings für `our`/`their`, Progressbar, Tooltip-Drilldown.
12. **Vanilla-Sofortaktionen:** [`generic_actions/improve_our_cultural_view.txt`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/generic_actions/improve_our_cultural_view.txt), [`country_interactions/improve_cultural_view.txt`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/country_interactions/improve_cultural_view.txt) und [`country_interactions/improve_cultural_view_subject.txt`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/country_interactions/improve_cultural_view_subject.txt) vollständig gegen aktuelle Vanilla prüfen.

## 6. Nachweisschema pro Funktion

| Feld | Nachzutragen |
|---|---|
| Feature/Task | T00–T16 aus [Plan](10_implementierungsplan_v1.md) |
| Zielbuild | Versionsnummer / Checksum / Mod-Version |
| Verwendete Identität | Effect, Trigger, Script-Value, GUI-Funktion, On Action |
| Nachweis | relativer Dateipfad + Zeile + Download-/Dump-Version |
| Eingangsscope / Zielscope | tatsächlich bestätigte Typen |
| Ergebnis | Screenshot, Save/Load, Log und ggf. Performance |
| Status | VERIFIZIERT_IM_ZIELBUILD / TEILWEISE / NICHT_MÖGLICH / OFFEN |
| Alternative | nur nach begründeter technischer Abweichung |

**Diesen Nachweis *vor* der ersten vollständigen Codex-Implementierung durchführen.**
