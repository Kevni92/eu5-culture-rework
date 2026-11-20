# Codex-Arbeitsanweisung – EU5 Culture Rework

## Aufgabe

Implementiere **ausschließlich** den dokumentierten V1-Hybridansatz:

- Eigene gerichtete Meinungswerte je Kulturpaar (−100…+100) und gesellschaftliche Zielwerte.
- Relevante Kulturpaare über realen Kontakt registrieren; bei Kontaktverlust Zielwert 0 und langsame Drift.
- Monatliche oder jährliche, **global deduplizierte** Berechnung.
- Auf die fünf nativen Meinungsstufen synchronisieren; `set_cultural_view` nur bei tatsächlichem Zustandswechsel.
- **Vanilla-Kulturakzeptanz, Tolerierung, Diskriminierung, Kulturkapazität, Assimilation, Rechte und statusabhängige Funktionen unverändert lassen.**

Die verbindliche Aufgabenreihenfolge und Dateistruktur stehen in [docs/technical/10_implementierungsplan_v1.md](docs/technical/10_implementierungsplan_v1.md). Dessen Verifikationskriterien stehen in [11_v1_verifikationsmatrix.md](docs/technical/11_v1_verifikationsmatrix.md); die Abnahme in [12_v1_abnahme_und_tests.md](docs/technical/12_v1_abnahme_und_tests.md).

## Quellenhierarchie

1. Lokal installierte, **versionsgleiche** EU5-Spielskripte und mit **derselben** Version generierte `script_docs`/`dump_data_types`.
2. Vanilla-Anwendungsbeispiele aus demselben Build.
3. Versionsgleiche, getestete Mods.
4. Älterer Referenzspiegel als Recherche, **nicht** als Patch-Freigabe: `HLJSXK/eu5-modding-base`, Commit `9e941ced` (1.4.3 beta).
5. Wiki/Guides als ergänzende Hinweise.

**Keine erfundenen Scopes, Trigger, Effects, GUI-Data-Getter oder Modifier-Kategorien.** Für jede neue Schnittstelle Existenz, Eingangsscope, Zielskope, Werttyp, Semantik und funktionierendes Beispiel nachweisen.

## Reihenfolge – harte Gates

1. Zuerst Spielversion, Metadaten-/Modpfade, aktuelle Engine-Dumps und Vanilla-Layouts feststellen.
2. **T02:** kulturtypisierte persistente Variable-Map inklusive A→B/B→A und Save/Load beweisen.
3. **T03:** `cultural_view` und `set_cultural_view` mit dynamischer Zielkultur validieren.
4. **T04:** tatsächlich genau **einen** globalen Tick je Periode aus Country-Pulses schaffen oder eine verifizierte Alternative finden.
5. Erst nach den Gates Registry, Zielwert, UI, Einflussmodule implementieren. Keine Teilfunktion als „fertig“ markieren, die einen P0-Gate nicht bestanden hat.

## Projektorganisation und Qualität

- Script-IDs mit `crw_`, Localization mit `CRW_` präfixen; keine zufälligen Vanilla-Overrides.
- Gameplay-Grundsätze ausschließlich unter `docs/concept/`, technische Tests, Quellen, Umsetzungsentscheidungen unter `docs/technical/`.
- Änderungen an `culture_lateral_view.gui` **nur** auf Basis der zum Zielbuild passenden Vanilla-Datei. Die `our`-`their`-Spalten umbauen, die Akzeptanzbuttons unverändert lassen.
- „Änderungsmodifier“ bedeutet zunächst ein **erklärbarer Mod-Zielwert plus Anpassungsrate** und gespeicherte Beiträge – **kein** nativer Culture-Pair-Modifier ohne Engine-Nachweis.
- Zielwertfaktoren nicht bei jedem GUI-Hover neu ermitteln. Kein vollständiger Culture×Culture-Scan bei Monats-/Jahrestick.
- Gemeinsame, getestete Zahlenberechnung für Script-Effekte, Script-Values und Tooltips; keine unterschiedliche Formeln.
- Nach jedem Task Tests, Fehlerlogs, Save/Load und Laufzeitbefunde mit genauer Version in die Dokumentation eintragen.
- Bei fehlender Engine-Funktion: **BLOCKER** und nachweisbare Alternativen dokumentieren; nicht stillschweigend Semantik verändern.

## Status

Dokumentations-/Planungsstand vom 20.11.2026. **Noch kein fertiger Mod-Prototyp nachgewiesen.**
