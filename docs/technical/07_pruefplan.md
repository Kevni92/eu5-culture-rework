# 7. Verifikation und Umsetzungstests

Diese Datei sammelt **Ergebnisse**, nicht nur Annahmen. Aktuell wurden noch keine Mods erstellt oder Ingame-Tests durchgeführt.

## Vor jedem technischen Beschluss

1. **Zielbuild festhalten** (Spielversion, Checksum, Steam-Build).
2. Passende lokale Vanilla-Dateien und Engine-Dumps (`script_docs`, `dump_data_types`) aus **demselben** Build prüfen.
3. Für jeden Trigger, Effect und jedes Datenfeld **Existenz, Scope, Werttyp und ein funktionierendes Vanilla-Beispiel** nachweisen.
4. Nur danach minimale Test-Mod bauen; Errorlog, tatsächliches Spielverhalten und Performance vergleichen.
5. Status und Fundstelle für jede Mechanik in den zugehörigen Dateien aktualisieren.

## Testszenarien

| Szenario | Erwartete Prüfung |
|---|---|
| Kastilien / Portugal | Gerichtete Entwicklung über Kabinettsaktion, Bevölkerungsgewichtung |
| Kastilien / Granada | Religiöse Zusammensetzung und Staatspolitik getrennt nachvollziehbar |
| Frankreich / Kastilien | Bündnis, Meinung, Vertrauen verändern nur korrekte Meinungsrichtung |
| Frankreich / 300 Chinesen | Vernachlässigbarer staatlicher Einfluss |
| Frankreich / 50.000 Chinesen | Nicht durch globale 0,05-%-Quote ausgeschlossen |
| Friedensschluss | Kriegseinfluss endet, historischer Meinungswert bleibt erhalten |
| Migration ohne Event-Hook | Jährlicher Abgleich aktiviert bzw. deaktiviert relevante Kontakte |
| Save/Load und neuer Spielstart | Persistenz, Doppelzählungs- und Initialisierungsfehler ausschließen |
| Große multiethnische Reiche | Monatliche/jährliche Scriptzeit messen, keine Kultur-Vollmatrix |

## Ergebnisprotokoll (pro getesteter Mechanik)

- Datum / EU5-Version / Build
- Quellenpfad und Engine-Dump-Auszug
- Prüfung: Syntax, Scope, Semantik
- Testkonstellation und erwartetes Verhalten
- Beobachtetes Ingame-Verhalten und Logbefund
- Performance: Häufigkeit, Kosten und Vergleich mit Vanilla
- Entscheidung: **verifiziert / teilweise / widerlegt / offen**
- Offene Folgeaufgaben

**Aktueller Stand:** Alle Fälle offen. Der alte öffentliche 1.4.3-beta-Spiegel ist eine Vorrecherche, kein Testnachweis für die aktuelle Installation.
