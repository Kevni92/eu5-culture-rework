# 7. Verifikation und Umsetzungstests

Diese Datei sammelt **Ergebnisse**, nicht nur Annahmen. Aktuell wurden noch keine Mods erstellt oder Ingame-Tests durchgeführt.

Für die geplante erste Version gelten jetzt verbindlich der [V1-Implementierungsplan](10_implementierungsplan_v1.md), die [Verifikationsmatrix](11_v1_verifikationsmatrix.md) und die [detaillierten Abnahmetests](12_v1_abnahme_und_tests.md).

## Vor jedem technischen Beschluss

1. **Zielbuild festhalten** (Spielversion, Checksum, Steam-Build).
2. Passende lokale Vanilla-Dateien und Engine-Dumps (`script_docs`, `dump_data_types`) aus **demselben** Build prüfen.
3. Für jeden Trigger, Effect und jedes Datenfeld **Existenz, Scope, Werttyp und ein funktionierendes Vanilla-Beispiel** nachweisen.
4. Nur danach minimale Test-Mod bauen; Errorlog, tatsächliches Spielverhalten und Performance vergleichen.
5. Status und Fundstelle für jede Mechanik in den zugehörigen Dateien aktualisieren.

## Beschlossener Plan – zuerst zu prüfende Integrationspunkte

1. Gerichteten kontinuierlichen Wert auf Kultur-Scope persistent speichern und bei Save/Load stabil halten.
2. Konkrete `set_cultural_view`-Ausführung gegen den aktuellen Spielbuild testen und **nur beim Stufenwechsel** ausführen.
3. Grenzfälle **−100, −60, −20, +20, +60, +100** testen; auch Überspringen mehrerer Stufen in einem Jahreslauf.
4. **Monatliche / jährliche** Berechnung auswählbar machen; jährlichen Schritt zeitlich korrekt skalieren und doppelte Ausführung durch Länder-Pulse verhindern.
5. Startwerte aus den historischen Vanilla-Stufen ableiten; neue, ruhende und wieder aktive Kulturpaare konsistent behandeln.
6. Unveränderte **Kulturakzeptanz, Tolerierung, Diskriminierung und Kulturkapazität** nach Stufenwechsel testen. Keine eigenen Kapazitäts- oder Pop-Modifikatoren erforderlich.
7. Direkte Vanilla-Meinungsaktionen und Ereignisse auf Umgehungen des neuen numerischen Werts prüfen.

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
| −60 / −20 / +20 / +60 überschritten | Nur bei abweichender nativer Stufe `set_cultural_view`, korrekte Richtung, nur ein Set pro Änderung |
| Monatlicher vs. jährlicher Modus | Vergleichbare langfristige Werteentwicklung; keine zwölfmal zu kleine Jahresänderung |
| Bestehende Kultur akzeptieren | Vanilla-Aktion, Kulturkapazitätskosten und statusabhängige Pop-Folgen unverändert |
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
