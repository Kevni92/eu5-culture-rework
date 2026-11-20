# EU5 Culture Rework – konkreter Implementierungsplan V1

**Stand:** 20.11.2026 · **Status:** implementierungsbereit als *Arbeitsplan*, **noch nicht technisch/ingame freigegeben**. Keine Moddateien oder Runtime-Tests erstellt.

**Verbindliche Quellen:** [Gameplay-Konzept](../concept/README.md), [Technik 1 – Speicherung](01_meinungswert_und_engine_grenzen.md), [Technik 2 – on_actions](02_on_actions_und_kontakte.md), [Technik 8 – Vanilla-Synchronisierung](08_hybrid_synchronisierung_und_intervalle.md), [Technik 9 – GUI](09_gui_kulturmeinung.md). [Prüfmatrix mit belegten IDs und Blockern](11_v1_verifikationsmatrix.md).

> **Patch-Gate:** Die namentlich aufgeführten Vanilla-/Engine-Funktionen wurden im öffentlichen **1.4.3-beta**-Referenzspiegel bzw. dessen beigelegten Dumps gefunden. Vor Codegenerierung **Spielbuild, dazu passende lokale Vanilla-Dateien, `script_docs` und `dump_data_types` prüfen**. Es gibt keine implizite Bestätigung für eine spätere Spielversion.

## A. Umfang und Architekturentscheidung

### In V1 umzusetzen

1. **Gerichteter Kulturmeinungswert:** je A → B numerisch **−100 bis +100**, getrennt von B → A.
2. **Zielwertmodell:** je aktivem Paar einen gesellschaftlichen Zielwert ermitteln, Meinung nähert sich diesem langsam an. Für inaktive Paare gilt Ziel **0**, die Meinung verblasst weiter.
3. **Relevante Kulturpaare:** am Start und fortlaufend über konkrete Kontakte erfassen; keine vollständige Kultur-×-Kultur-Matrix.
4. **Historische Initialisierung:** bisherige fünf Vanilla-Meinungsstufen auf numerische Startwerte abbilden.
5. **Einstellungen:** Berechnung monatlich (Vorgabe) oder jährlich per Game Rule, zeitlich äquivalente Annäherung.
6. **Skriptbare Zielwertbeiträge:** kulturelle Nähe/Zusammenleben, staatliche Diplomatie, Religionspolitik/-zusammensetzung, vereinfachter Kriegsbeitrag und neue Kabinettsaktion; **Modul-Gates siehe unten**.
7. **Vanilla-Synchronisierung:** `set_cultural_view` **nur wenn die abgeleitete der tatsächlichen Stufe widerspricht**.
8. **GUI:** die zwei bestehenden Richtungsspalten, Icon über Balken, Tooltip mit Ist/Ziel/Änderung, Untertooltip mit Zielwertbeiträgen.
9. **Alte Sofort-Aktionen für Meinung sperren oder umleiten**, damit sie den kontinuierlichen Wert nicht umgehen; aktuelle Vanilla-Änderungsevents erfassen.
10. **Save/Load, neue Kulturen, Performance und Konsistenztests**.

### Nicht in V1

- Kein Ersatz von akzeptierten/tolerierten/ignorierten/diskriminierten Kulturen, **keine** neue Kulturkapazitätsformel, **keine** zusätzlichen Pop-/Kultur-Modifikatoren zur Umgehung der Vanilla-Effekte.
- Keine Opferzahlen/Plünderungen pro Pop-Kultur ohne belegbare Engine-Daten.
- Keine lokale Meinungsmatrix pro Land/Location; Beiträge sind **länderbezogen**, die gespeicherte Meinung bleibt **global je Kulturpaar**.
- Keine automatische Kontaktaufnahme allein aufgrund gleicher Sprache/Religion/Kulturgruppe; **kein vollständiger Kulturpaar-Vollscan pro Tick**.

### Lieferstufen und Blocker

- **M0 – Runtime-Spike (P0):** Kultur-Map, zwei Richtungen, Lesen aus GUI, `set_cultural_view`, periodischer Scheduler. Bei Fehlschlag keine scheinbar fertige V1 bauen; stattdessen Alternativarchitektur dokumentieren.
- **M1 – vollständige Kernmechanik:** Game-Start-Register, Meinung+Ziel, Kontaktfilter, Aktiv/Ruhend, monatlich/jährlich, Vanilla-Synchronisierung, GUI und Tooltips. Ein reproduzierbarer, zunächst einfacher Zielwertbeitrag genügt für die technische Abnahme.
- **M2 – V1-Einflussmodule:** Zusammenleben/Kulturnähe, Diplomatie, Religion, Krieg (nur Länder-Primärkulturen), Kabinettsaktion. **Jedes Modul** bekommt eine separat testbare, begrenzte Zielwertkomponente. Religion und Kriegsverluste haben erhöhte Datenrisiken: ohne bestandenen Nachweis als **offene V1-Teilaufgabe**, nicht als implementierte Funktion deklarieren.
- **M3 – V1-Freigabe:** Tests, Performance, Lokalisierung, Mod-Kompatibilität. Erst danach „V1 umgesetzt“ melden.

## B. Verzeichnis- und Dateiplan

**Vorgeschlagene deploybare Mod-Root-Struktur im Repository:** `.metadata/`, `in_game/`, `main_menu/`. Exakten Launcher-Metadatenbedarf und Zielordner gegen den installierten Build prüfen. **Die folgenden `crw_`-Bezeichner sind neue, von uns zu definierende Modulnamen, nicht behauptete Vanilla-APIs.**

| Datei (neu / Override) | Auftrag | Meilenstein |
|---|---|---|
| `.metadata/metadata.json` *(genaue Metadatenform prüfen)* | Mod-ID, Version, Beschreibung | M0 |
| `in_game/common/on_action/crw_culture_on_actions.txt` | Initialisierung, periodische Läufe, Änderungen/Dirty-Markierung | M0–M1 |
| `in_game/common/scripted_effects/crw_registry_effects.txt` | Kulturpaar registrieren, Kontakt aktivieren/deaktivieren, Deduplizieren | M0–M1 |
| `in_game/common/scripted_effects/crw_init_effects.txt` | Startstufen lesen, Maps/Flags einmalig anlegen | M0–M1 |
| `in_game/common/scripted_effects/crw_update_effects.txt` | Zielwert bestimmen, Annäherung, Begrenzung und Stufensynchronisierung | M0–M1 |
| `in_game/common/scripted_effects/crw_target_effects.txt` | Beiträge sammeln, aggregieren und cachefähige Teilwerte speichern | M1–M2 |
| `in_game/common/scripted_effects/crw_contact_effects.txt` | Länderbevölkerung, Nachbarschaft, Diplomatie- und Kriegskontakte | M1 |
| `in_game/common/scripted_triggers/crw_relevance_triggers.txt` | Mindestanteil, Mindestgröße, Kontakt, Ausschluss A = B | M1 |
| `in_game/common/scripted_triggers/crw_state_triggers.txt` | Initialisiert? Paar registriert/aktiv? Stufe bereits korrekt? | M0–M1 |
| `in_game/common/script_values/crw_opinion_values.txt` | Balancing-Parameter, Grenzwerte, Pull-/Zeitschrittfaktoren, Clamps | M0–M1 |
| `in_game/common/script_values/crw_target_values.txt` | Richtungsspezifische Zielwertkomponenten und aggregierte Werte **falls Scope-Übergabe nachgewiesen** | M1–M2 |
| `in_game/common/scripted_effects/crw_diplomacy_effects.txt` | gewichtete Beiträge von Landesmeinung, Vertrauen, Bündnissen, Rivalität | M2 |
| `in_game/common/scripted_effects/crw_religion_effects.txt` | jährliche Kultur-Religion-Verteilung und Politikbeiträge, ggf. Cache | M2 / Prüf-Gate |
| `in_game/common/scripted_effects/crw_war_effects.txt` | vereinfachter Kriegseinfluss der Primärkulturen | M2 |
| `in_game/common/cabinet_actions/crw_cultural_rapprochement.txt` | neue, dauerhafte Kabinettsaktion für Kultur A → eigene Primärkultur | M2 |
| `main_menu/common/game_rules/crw_culture_game_rules.txt` | `crw_update_frequency`: `crw_monthly` (Vorgabe) oder `crw_yearly` | M1 |
| `in_game/gui/culture_lateral_view.gui` **Override** | `our` + `their` als Icon+Bar, unsere Sofortbuttons entfernen | M0–M1 |
| `in_game/gui/crw_culture_tooltips.gui` | Ist/Ziel/Änderung; verschachtelter Tooltip für Zielwertkomponenten | M1–M2 |
| `main_menu/localization/english/crw_culture_l_english.yml` | UI, Tooltips, Game Rule, Kabinettsaktion | M1–M2 |
| `main_menu/localization/german/crw_culture_l_german.yml` | deutschsprachige Texte, Pfad anhand aktueller Vanilla bestätigen | M1–M2 |
| **Vanilla-Overrides in `generic_actions/` und `country_interactions/`** | `improve/reduce` / `ask to improve` sperren oder umleiten; exakte Dateien/IDs erst nach Suche | M2 |
| `docs/technical/11_v1_verifikationsmatrix.md` | Existenz-/Scope-/Versionsstatus und Entscheidungen | laufend |
| `docs/technical/12_v1_abnahme_und_tests.md` | Testfälle, Performance-Metriken, Freigabekriterien | laufend |

**Kein neues natives `modifier_type` für `culture_pair` einplanen.** Die Referenz nennt für Modifikatoren andere Kategorien (z. B. Country, Religion), aber keinen belegten paargebundenen Modifier. „Eigener Meinungsänderungs-Modifier“ wird in V1 als **Mod-eigener Zielwert mit aufgeschlüsselten Script-Values/Cache-Daten im Tooltip** umgesetzt. Script-Values sind **Berechnungen**, keine Engine-Modifier.

## C. Datenmodell und Zustandsautomat

**Pro gerichteter Quellkultur A, Zielkultur B** (vorgesehene Variable-Maps, der konkrete Kultur-Scope ist P0-Test):

| Name/Map | Inhalt | Persistenz |
|---|---|---|
| `crw_score` | numerische Ist-Meinung [−100, +100], B als Schlüssel | dauerhaft |
| `crw_target` | zuletzt berechneter Zielwert [−100, +100] | Cache; bei aktiver Berechnung aktualisiert |
| `crw_active` | Kontakt besteht ja/nein bzw. zuletzt bestätigte Registry-Generation | dauerhaft/aktuell |
| `crw_target_coexistence`, `crw_target_diplomacy`, `crw_target_religion`, `crw_target_war`, `crw_target_cabinet`, `crw_target_affinity` | Zielwertbeiträge, Tooltip-Breakdown | Cache; bei Update |
| `crw_last_update` *(nur bei Lazy-/Zeitsprung notwendig)* | zuletzt verarbeitete Periode/Datum | persistent |
| `crw_init_version` *(global)* | Schützt Initialisierung bei Save/Load, erlaubt spätere Migration | persistent |
| `crw_last_processed_period` *(global)* | schützt vor mehrfacher Verarbeitung durch Country-Pulse | persistent |

**Zu prüfen:** Negative Dezimalzahlen in Kultur-Maps, kulturtypisierte Schlüssel, Lesen aus GUI und Persistenz. Variable-Maps dürfen beim Wert-Update keine Schlüssel verlieren oder ihre Iteration korrumpieren; Community-Referenzen verwenden ggf. Entfernen+Wiedereinfügen. Bei fehlender Culture-Map-Unterstützung ist ein **alternativer globaler Schlüssel-/Wert-Speicher** zu testen – niemals stillschweigend annehmen.

**Aktiv:** Es existiert **mindestens ein** gültiger Kontakt irgendwo in der Spielwelt. Ziel aus Einflüssen berechnen.  
**Ruhend:** Es existiert **kein** gültiger Kontakt mehr. `target = 0`; Ist-Wert bleibt, driftet aber gegen Null.  
**Unbekannt:** Noch nie aktiviertes Kulturpaar, keine gespeicherte Modmeinung; auf keinen Fall jedes unbekannte Paar pauschal mit 0 synchronisieren.

**Deduplikation:** Das Registrieren von A↔B ist idempotent, erzeugt A→B und B→A getrennt und überschreibt **keine bereits gespeicherten Meinungswerte**. Nur *ein* Pfad darf in einer Periode die dynamische Meinung fortschreiben.

## D. Start-Initialisierung

**Trigger:** `on_game_start` aus dem alten Referenzstand; tatsächlicher Zeitpunkt bei neuem Spiel, Laden und Mod-Aktivierung ist im Zielbuild zu testen.

1. Globale Initialisierungs-/Versionsmarkierung prüfen. **Wenn bereits initialisiert: kein Reset**, nur erforderliche Migration und Wiederherstellung.
2. **Einmalig `every_country`:**
   - **Kulturkontakt im Inland:** Primärkultur des Landes ↔ jede mit `every_present_culture_in_country` gefundene andere Kultur, **wenn Landesquote ≥ 0,1 % UND Kulturbevölkerung ≥ 1.000** (V1-Balancingvorschlag). Ist A=B, ignorieren.
   - **Nachbarländer:** `every_neighbor_country` und jeweils deren Primärkultur; bei A≠B Kontakt A↔B.
   - **Diplomatische Berührung:** passende `every_related_country` / bilaterale Relation (nur nach Typprüfung) → Primärkultur A↔B.
   - **Laufende Kriege:** `every_country_at_war_with` → Primärkultur A↔B.
3. **Bei erstmaliger Registrierung** jede Richtung von der **vorhandenen nativen Meinung** initialisieren: `enemy = −80`, `negative = −40`, `neutral = 0`, `positive = +40`, `kindred = +80`. Das sind **vorläufige beschlossene Implementierungsdefaults**, kein zusätzliches Vanilla-Wissen.
4. Für registrierte Paare **Aktivstatus setzen**, zunächst Ziel/Beiträge initialisieren; **noch keine spontane Kulturmeinungsstufenänderung beim Spielstart erzwingen**.
5. Initialisierungsmarker setzen. **Fehlertoleranz:** Wenn Setup vor vollständiger Bevölkerungsinitialisierung läuft, einen nachgelagerten kontrollierten Erstabgleich planen; keinen globalen Doppelstart zulassen.

**Beispiel:** Kastilisch ↔ Portugiesisch wegen Nachbarschaft. Kastilisch ↔ Chinesisch ohne Kontakt **nicht** anlegen. Französisch ↔ Chinesisch bei 50.000 von angenommen 10 Mio. Landesbevölkerung **anlegen**, auch wenn der Anteil an allen Chinesen weltweit klein ist.

## E. Kontakt-Relevanz und fortlaufende Registry-Pflege

**Registrierungskriterien V1:** (a) nennenswerte Minderheit im selben Land wie dessen Primärkultur (Quote + absolute Größe), (b) Nachbarstaaten mit verschiedenen Primärkulturen, (c) konkrete diplomatische Beziehung, (d) Krieg zwischen Ländern verschiedener Primärkultur, (e) laufende kulturelle Kabinettsaktion.

**Kein Kontakt allein wegen:** gleicher Sprache, gleicher Kulturgruppe, gleicher Religion oder beliebiger diplomatischer Reichweite. Andere Kontakte, z. B. Handel oder benachbarte *Locations*, sind Erweiterungen, nicht V1-Blocker.

**Dynamik:**
- `on_war_declared`, `on_join_war`, `on_ending_war`, `on_location_changed_owner`, `on_culture_changed`, `on_released_country`, `on_scripted_relation_created` und Cabinet-Aktivierung/Deaktivierung markieren betroffene Kulturen/Länder **dirty**; Hooks und Scopes vor Verwendung prüfen.
- Migration/Pop-Veränderungen und Diplomatie-Abbau sind nicht lückenlos eventseitig belegt → **einmal jährlich** Kontakt-Scan auf Länderbasis.
- Jährlicher Sweep: vorher registrierte Paare zunächst als „nicht bestätigt“ kennzeichnen; im Kontaktscan wieder markieren; nur am Ende vollständig unbestätigte Paare auf ruhend/Target 0 setzen. **Score nie löschen.**
- Liegt **anderswo** weiterhin Kontakt vor, bleibt das globale Paar aktiv. Kein voreiliges Deaktivieren nach Wegzug nur aus einem Land.
- Bei erneutem Kontakt bestehende Meinung **nicht** auf den ursprünglichen Vanilla-Mittelpunkt zurücksetzen.

**Performance-Regel:** Landes-/Primärkultur-Kandidaten aus realen Kontakten erzeugen. Keine periodische verschachtelte `every_culture`-Iteration. Ein einmaliger Register-Aufbau und jährlicher Relevanzabgleich sind erlaubt. Eine vollständige Pop-Iteration je *Kulturpaar* ist verboten.

## F. Zielwert, Änderung, Monats-/Jahrestakt

**Konzept:** `score` folgt exponentiell dem `target` mit Trägheit; der Updateeffekt schreibt die Zahl und synchronisiert nachher die native Stufe.

| Parametervorschlag (eigene Script-Value-/Balancing-Definitionen) | V1-Startwert |
|---|---:|
| Mindestquote im Land | **0,001 = 0,1 %** |
| Mindestpopulation | **1.000** |
| Aktive Meinungs-Halbwertszeit zum Ziel | **10 Jahre** |
| Ruhende Meinungs-Halbwertszeit zu 0 | **15 Jahre** |
| Meinung und Ziel | **−100 bis +100** |
| Berechnung | **monatlich** (Default), **jährlich** als Game Rule |
| Startwert pro Vanilla-Stufe | **−80 / −40 / 0 / +40 / +80** |

**Definition:** Nach `n` vergangenen Monaten und Halbwertszeit `H` Monaten:
- `a(n) = 1 − 2^(−n/H)` (Anteil des noch vorhandenen Abstands, der im Intervall geschlossen wird).
- `score_neu = clamp(score_alt + (target − score_alt) × a(n), −100, +100)`.
- Für H=120 aktiv und H=180 ruhend sind Monats- und Jahresschritte ohne Einflusswechsel mathematisch gleichwertig. Für Jomini ggf. **vorab berechnete konstante Faktoren** hinterlegen, falls Potenzen nicht verfügbar sind.
- Beim Betrag unter einer Darstellungs-/Rundungsschwelle darf der interne Wert **nicht** vorzeitig auf 0 springen, außer dafür wird ausdrücklich eine Konvergenzgrenze beschlossen.

**Zielbildung (einziger finaler Datentyp):**
1. Aktiv: `target = clamp(affinity + coexistence + diplomacy + religion + war + cabinet, −100, +100)`.
2. Ruhend: `target = 0`, die aufwendigen Faktoren entfallen.
3. Beiträge bleiben **erklärbar**, additive oder anders gewichtete Kombinationen in V1 benötigen identische Scriptberechnung und Tooltip-Ausgabe.
4. Bei mehreren Ländern: Beiträge **gerichtet** gewichten und aggregieren. **Landesrelevanz entscheidet über Berechnung**, die **Anteile der Quellkultur des beitragenden Landes an ihrer Weltbevölkerung** gewichten die globale Auswirkung. Die Gegenrichtung erhält eine eigene Gewichtung.
5. Die **kleine weltweite Quote** ist **kein zweiter Filter**. Rundung sehr kleiner Beiträge prüfen; 50.000 Chinesen werden nicht allein wegen 0,05 % Weltquote ignoriert.

**Balancing:** Einzelbeiträge sollen eigene Parameter mit Bereichsbegrenzungen bekommen; genaue Größen (z. B. Diplomatie −30…+30, Krieg −20, Verwandtschaft +10) sind **Arbeitsannahmen**, noch nicht durch Gameplaytests beschlossen. Keine versteckten magischen Zahlen in der Update-Logik.

**Zeitsteuerung:**
- Game Rule `crw_update_frequency` mit `crw_monthly` / `crw_yearly`. Vanilla zeigt `main_menu/common/game_rules/`; der Trigger `has_game_rule` ist im alten Dump dokumentiert. Konkrete Flag-Auflösung prüfen.
- In `on_actions` **einen global wirksamen, deduplizierten Update-Pfad** für die registrierten Paare verwenden, obwohl `monthly_country_pulse`/`yearly_country_pulse` pro Land aufgerufen werden. Die Ermittlung einer zuverlässigen Perioden-ID/Global-Tick-Garantie ist **P0-Blocker**, kein angenommener vorhandener Global-Monatspuls.
- **Jahresmodus:** Jahresfaktor aus derselben Halbwertszeit; Zielwert kann während des Jahres wechseln. Bei kurzen Ereignissen muss der entsprechende Beitrag gespeichert und beim Jahreslauf angemessen berücksichtigt oder der Zielwert bei Schlüsselereignissen zusätzlich aktualisiert werden. Bis diese Semantik geklärt ist: *gleichwertiges Verhalten nur bei konstantem Zielwert zusichern*.
- **Ruhende Paare weiterhin leichtgewichtig aktualisieren und Stufen synchronisieren**, zumindest in der gewählten monatlichen/jährlichen Periode. Kein ungeprüftes Lazy-Verfahren, das jahrelang eine veraltete native Stufe zurücklässt.

## G. Vanilla-Meinungsstufen: einzige Engine-Wirkung

| Scorebereich | Zielsymbol/Vanilla-Zustand |
|---|---|
| −100 ≤ x < −60 | `enemy` |
| −60 ≤ x < −20 | `negative` |
| −20 ≤ x < +20 | `neutral` |
| +20 ≤ x < +60 | `positive` |
| +60 ≤ x ≤ +100 | `kindred` |

Nach **jeder** numerischen Aktualisierung der A→B-Richtung:
1. Zielstufe aus dem neuen Score ablesen.
2. Mit `cultural_view` den **realen** nativen Zustand von A→B prüfen.
3. Ausschließlich **bei Abweichung** `set_cultural_view` ausführen. Mehrstufige Sprünge in einem Jahres-Update sind erlaubt.
4. Keine eigenständigen Kulturkapazitäts-/Pop-Effekte ausführen.
5. B→A getrennt behandeln.

**Konfliktregel:** Für registrierte Paare ist der kontinuierliche Wert die **Quelle der Wahrheit**. Direkte Vanilla-`change_cultural_view`-Aktionen müssen deaktiviert oder in Mod-Einflüsse übersetzt werden. Vanilla-Ereignisse mit direkter Veränderung müssen auditiert werden; ein unerkannter externer Wechsel wird beim nächsten Update korrigiert, könnte dazwischen aber sichtbar sein. Das ist ein **offen zu testender Kompatibilitätsfall**.

## H. Zielwert-Module: konkreter Umsetzungsumfang

| Modul | Daten und Berechnung | Script-/Engine-Einstieg | P1-/P2-Risiko |
|---|---|---|---|
| **Kulturnähe** | Gleiche Kulturgruppe/Sprache als **positiver Zielbeitrag nur bei bereits aktivem Kontakt** | `has_shared_culture_group`, Sprache-Trigger im aktuellen Dump nachweisen | Sprach-API und Gewichtung |
| **Zusammenleben** | Für Land A: Primärkultur → Minderheit und Minderheit → Primärkultur, Gewichtung nach Landesrelevanz und jeweiliger globaler Quellkulturpopulation | `every_present_culture_in_country`, `culture_percentage_in_country`, `culture_population_in_country`, `world_culture_population` | globaler Zahlenwert-Zugriff und Rundung |
| **Diplomatie** | Nur Primärkulturen beteiligter Länder, Richtung A → B aus Meinung/Vertrauen A über B; Bündnis/Rivalität/Krieg wirken zusätzlich | `opinion`, `trust`, `get_opinion`, `is_rival_of`, geprüfte Relation-Trigger | tatsächlicher Wertebereich, Land-zu-Land-Objektzugriff |
| **Religion** | Einmal pro Jahr Kultur×Religion-Verteilung, danach Zielwert aus religiösem Verhältnis und der konkreten Religionspolitik des betroffenen Landes, gerichtet und gewichtet | `every_present_religion_in_country` **reicht allein nicht für Kultur×Religion**; Pop-/Aggregator-Zugriff als P1 beweisen | **hohes Risiko**: teure Aggregation; kein stiller Mehrheitsreligions-Proxy |
| **Krieg V1** | Kriegsbeziehung der **Primärkulturen**, temporär negativer Zielbeitrag; keine kulturgenauen Verlustzahlen behaupten | `on_war_declared`, `on_join_war`, `on_ending_war`, `every_country_at_war_with` | asymmetrische Verantwortung / Drittkriegsparteien |
| **Kabinettsaktion** | Spieler beauftragt Integration einer im Land vertretenen Kultur → eigene Primärkultur. Während der Aktivierung eigener positiver Zielwertbeitrag, durch Bevölkerung und Fähigkeit gewichtet | neues `cabinet_actions/`-Objekt analog `promote_culture`, `on_activate` / `on_deactivate` | aktuell gültiger Task-/Culture-Scope und laufender Einfluss |

**Abnahme je Modul:** (a) ein Testpaar reagiert richtig; (b) umgekehrte Meinung bleibt unabhängig; (c) Beitrag verschwindet nach Ende der Ursache; (d) Tooltip stimmt mit gespeicherten Zahlen überein; (e) kein zusätzlicher Scan aller Pops pro Paar. Nicht bestandene Module sind **nicht implementiert**; sie blockieren das jeweilige M2-Ziel, aber nicht M1-Kernprüfung.

## I. GUI und Lokalisierung

**Quelllayout:** `in_game/gui/culture_lateral_view.gui` (verifizierter Dateiort im alten Spiegel), `society_view_breakdown_subtab`, Liste `CountryCultureLateralView.GetImportantCultures`.

- Spalten **`our`** (eigene Primärkultur → Zeilenkultur) und **`their`** (umgekehrt) auf vertikale Container umstellen.
- Oben die **bestehenden fünf Icons** über `ImportantCultureItem.GetOurOpinionIcon` bzw. `GetTheirOpinionIcon`. Darunter je eine schmale **`progressbar`** aus bestehenden Vanilla-Templates.
- Balken normiert **(Score + 100) / 2** (0–100 % Füllung). Ziel: 30–34 Einheiten breit, 5–7 hoch; Screenshot/Tooltip-Kollisionen in 60er Zeilen testen.
- **`our`-Button** (Links-/Rechtsklick Sofortmeinung) **in eine passive Anzeige** umwandeln. Statusbuttons daneben, Sortierfunktion und Tooltips anderer Zellen erhalten.
- Eigener Tooltip **pro Richtung**: Kultur A→B, **Ist**, **Vanilla-Stufe**, **Zielwert**, **Δ pro Monat/Jahr**, Abstand zu nächster Stufe.
- Hover/Untertooltip auf **Zielwert bzw. Änderungsrate** zeigt **Zielwertbeiträge**, Halbwertszeit/Annäherungsfaktor und daraus abgeleitete periodische Veränderung.
- Keine aufwendige Pop-/Diplomatieberechnung bei Maus-Hover: GUI liest ausschließlich **bereits berechnete Werte**. Weitere Eingaben/GUI-Bindings nur nach Nachweis im aktuellen `data_types_gui`.
- Primärkultur: eigene Meinungsspalten wie Vanilla ausblenden; **Kulturstatus** bleibt unverändert.
- Localization-Schlüssel `CRW_...` für beide Sprachen. Andere Sprachen benötigen Fallback oder spätere Übersetzung.

**Kompatibilität:** `.gui` möglicherweise **Whole-File-Override**. Aktuellen Vanilla-Stand als Basis wählen und Konflikte mit UI-Mods explizit benennen. Keine ungeprüfte Übernahme der alten 1.4.3-beta-Datei.

## J. Reihenfolge der Umsetzung und Definition of Done

| Schritt | Änderung | Abnahme |
|---|---|---|
| **T00** | Zielbuild, lokale Dumps, Vanilla-GUI, Repo-Metadaten prüfen | Patch-Matrix mit konkreten Dateipfaden und Scope-Belegen |
| **T01** | Minimale Mod-/Spielstartstruktur | Log sauber; Mod wird geladen; Lauf nur einmal |
| **T02** | Numerische Culture-Map und zwei gerichtete Werte | A→B ≠ B→A, Dezimal/negativ, Save/Load |
| **T03** | `cultural_view`/ `set_cultural_view`-Mini-Test | nur geänderte Stufe gesetzt, korrekte Richtung |
| **T04** | Globale deduplizierte Scheduler-Logik + Game Rule | genau 1 Update pro Kulturpaar/Periode |
| **T05** | Startkontakt-Register + historische Stufenmitten | Kastilisch↔Portugiesisch ja, irrelevantes China nein |
| **T06** | Aktiv/Ruhend + jährlicher Relevanzabgleich | Auswanderung→Ziel 0; andere Kontakte verhindern falsches Ruhen |
| **T07** | Zielwert + exponentielle Annäherung (10/15 Jahre) | Vorzeichen, Grenzwerte, Monats-/Jahresgleichwert bei konstantem Ziel |
| **T08** | Eigenständiger Fünfstufen-Synchronisierer | alle Grenzwerte; keine überflüssigen Set-Aufrufe |
| **T09** | Balken + Getter im Kulturfenster | GUI zeigt A→B und B→A korrekt nach Sortieren/Scrollen |
| **T10** | Basis-Tooltips + verschachtelte Erklärung | keine nicht belegten Zahlen oder Mouse-Hover-Scans |
| **T11** | Kontakt-/Kulturnähe + Zusammenleben | gerichtete gewichtete Zielbeiträge |
| **T12** | Diplomatie + vereinfachter Krieg | Änderungen und Enden wirken auf richtigen Zielwert |
| **T13** | Kabinettsaktion | aktiv: Ziel steigt; deaktiviert: Beitrag entfällt |
| **T14** | Religion (jährlicher Cache) | Kultur×Religion-Daten wirklich vorhanden, Performance bestanden |
| **T15** | Alte Meinungsaktionen, Events, KI und UI auditieren | keine unerwünschte globale Sofortänderung |
| **T16** | Stress-/Regressionstests, Lokalisierung, Release | [Abnahmeplan](12_v1_abnahme_und_tests.md) bestanden |

**DoD für M1:** T00–T10 erfolgreich; abweichungsfreier Vanilla-Status und Save/Load, registrierte Paare werden korrekt aktualisiert. **DoD für vollständige V1:** zusätzlich T11–T16 oder die offenen Module im Release ausdrücklich als nicht enthalten kennzeichnen und V1-Scope formal anpassen.

## K. Betriebs- und Qualitätsregeln für Codex

1. **Keinen Code ohne aktuelle Versionsfreigabe.** Für jeden neuen Trigger/Effect/GUI-Aufruf Fundstelle, eingehenden Scope, Zielscope und beobachtete Semantik nachweisen.
2. **Keine erfundenen Script-APIs** wie `every_relevant_culture`; dieses Verhalten ist eine eigene Registry mit vorhandenem `every_key_in_variable_map`.
3. **Keine Änderung der Vanilla-Akzeptanz**. Bestehende Kulturstatus-Buttons und Kapazitätsberechnung sind Regressionstest.
4. Jede Speicherung oder Iteration muss auch **Save/Load, Reload und bereits registrierte Paare** vertragen.
5. Alle relevanten Verzeichnisse/Overrides gegen den tatsächlich installierten Build kontrollieren.
6. **Script-Values und Tooltip nicht als voneinander unabhängige Simulationen** implementieren. Einer gemeinsamen, nachvollziehbaren Berechnung entnehmen.
7. Performance getrennt messen: Initialisierung, jährlicher Scan, monatliche Paarupdates, GUI-Rendering. Einfache Dashboard-/Profiling-Werte dokumentieren.
8. Jede abgeschlossene Teilaufgabe mit Status, Quelle, reproduzierbarem Test und ggf. Patchhinweis in [11](11_v1_verifikationsmatrix.md) und [12](12_v1_abnahme_und_tests.md) nachtragen.

**Nächster ausführbarer Schritt:** T00 → T02 → T03 → T04. Erst nach diesen P0-Gates weitere Gameplay-Module entwickeln.
