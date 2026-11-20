# V1 – Testmatrix, Meilenstein-Abnahme und Performance-Audit

**Stand:** 20.11.2026 · **Implementierungs-/Teststatus:** noch nicht gestartet. Dieser Plan ist eine **Testvorgabe**, kein behauptetes erfolgreiches Ergebnis.

**Verknüpfung:** [Implementierungsplan](10_implementierungsplan_v1.md) · [API-Verifikationsmatrix](11_v1_verifikationsmatrix.md).

## A. Testumgebung (vor dem ersten Lauf ausfüllen)

- EU5-Spielversion: **OFFEN**
- Checksum / Steam-Build: **OFFEN**
- Vanilla-Gamefiles und Engine-Dumps derselben Version: **OFFEN**
- Mod-Commit und Launcher-Registrierung: **OFFEN**
- Testland, Startdatum, Save-ID: **OFFEN**
- Andere Mods / Ladereihenfolge: **OFFEN**
- Testhardware, durchschnittliche Scriptzeit, Durchlaufdauer: **OFFEN**
- Referenzspielstand ohne Mod, identische Einstellungen: **OFFEN**

## B. M0 – technische P0-Spikes

| ID | Prüfung | Erwartetes Ergebnis |
|---|---|---|
| **V01** | Culture A besitzt Map mit Culture B als Schlüssel und Wert −48,5 | korrekter Wert nach Save/Load und anderen Scripteinstiegen lesbar |
| **V02** | A→B = −48,5; B→A = +21,25 | Werte vollständig unabhängig; keine Doppel-/Inverseinträge |
| **V03** | A→B-Wert in Map verändern, Schlüssel iterieren | genau ein Schlüssel, korrekter Wert, keine Iterationskorruption |
| **V04** | `set_cultural_view` für A→B von `negative` auf `positive` | A→B geändert, B→A unverändert, sofort in UI vorhanden |
| **V05** | Zweite Updateanforderung innerhalb derselben Monatsperiode | **kein zweiter Meinungsfortschritt**, selbst bei vielen Länder-Pulsen |
| **V06** | Game Rule monatlich vs. jährlich, neuen Spielstand starten | nur gewählter Lauf aktiv, anderer bleibt inaktiv |
| **V07** | Laden / Mod-Re-Load ohne Neuspiel | keine zurückgesetzte Initialisierung, keine Duplikate |
| **V08** | Kultur-Map aus `ImportantCultureItem` im GUI lesen | beide Spalten, Zielrichtungen und Values korrekt |

**M0-Gate:** V01–V08 bestanden. Bei einem P0-Fehlschlag zuerst [API-Matrix](11_v1_verifikationsmatrix.md) aktualisieren, alternative Architektur testbar definieren und die Implementierung stoppen, statt mit Fantasie-Syntax fortzufahren.

## C. M1 – Kernspielmechanik

| ID | Szenario | Erwartetes Ergebnis |
|---|---|---|
| **V09** | Neuer Spielstart Kastilien / Portugal | Paar A↔B durch Grenzkontakt registriert, separate Richtungen vorhanden |
| **V10** | Kastilien / Chinesisch ohne Kontakt | **kein aktives Paar** nur wegen kultureller Existenz |
| **V11** | Nation mit 300 Chinesen und angenommen 10 Mio. Einwohnern | unter vorgeschlagener Landesquote/Popgrenze; kein Minderheitenkontakt |
| **V12** | Nation mit 50.000 Chinesen / 10 Mio. Einwohnern | unter Berücksichtigung der lokalen Quote **aktiviert**, selbst bei weltweit 100 Mio. Chinesen |
| **V13** | Eine Beziehung existiert durch Kontakt in drei Ländern; Kontakt fällt in einem weg | globales Kulturpaar bleibt aktiv |
| **V14** | Letzter Kontakt fällt weg, aktuelle Meinung −50 | aktives Register deaktiviert, Ziel wird **0**, Meinung wandert langsam auf 0 zu |
| **V15** | Kontakt kehrt nach mehreren Jahren zurück | gealterter Wert wird weiterverwendet; kein Reset zum Vanilla-Mittelpunkt |
| **V16** | Historisch `enemy` beim ersten Registereintrag | Mod-Startwert −80; native Stufe bleibt `enemy` |
| **V17** | Score genau −100, −60, −20, 20, 60, +100 | exakt `enemy`, `negative`, `neutral`, `positive`, `kindred` an den richtigen Grenzen |
| **V18** | Score −61 → −59,8; +59,9 → +60,1 | genau je **ein** nötiger `set_cultural_view`-Aufruf |
| **V19** | Score bleibt im selben Intervall | **kein** unnötiger `set_cultural_view`-Aufruf |
| **V20** | Jährlicher Sprung von −80 nach +65 | korrekter Wechsel unmittelbar auf `kindred` |
| **V21** | identischer Ausgangswert, konstantes Ziel und 12 Monate Simulationszeit | monatlicher vs. jährlicher Modus erreichen denselben Wert innerhalb Fixed-Point-Toleranz |
| **V22** | Kontaktloses Paar und 10–30 Jahre Laufzeit | score geht exponentiell auf 0 zu, native Meinung wird bei jeder überschrittenen Grenze synchronisiert |
| **V23** | Kulturakzeptanzstatus ändern | unveränderte Vanilla-Akzeptanzaktion, Kapazitäts-/Pop-Berechnung ohne eigenen Mod-Effekt |
| **V24** | Save/Load, danach 12 Monate und erneuter Save/Load | keine doppelte Aktualisierung, kein Kontaktverlust, kein Initialisierungsreset |

## D. M2 – Zielwertbeiträge

| ID | Szenario | Erwartetes Ergebnis |
|---|---|---|
| **V25** | Gleiche Sprache/Kulturgruppe, aber **kein** Kontakt | kein registriertes Paar allein aufgrund dieser Gemeinsamkeiten |
| **V26** | Aktiviertes Paar mit verwandter Kulturgruppe | positiver **Zielwertbeitrag**, keine unmittelbare Score-Umschaltung |
| **V27** | Gutes französisches Verhältnis zu Kastilien | Gewichtung **Französisch → Kastilisch**, nicht automatisch umgekehrt |
| **V28** | Vertrauen/Bündnis sinkt bzw. wird aufgehoben | Beitrag verschwindet oder sinkt nach zugelassenem Aktualisierungsintervall |
| **V29** | Kastilien/Granada mit unterschiedlicher Religionszusammensetzung | nur **nach nachgewiesener Kultur×Religion-Aggregation** korrekte Religionsgewichte; nicht allein Religionsnamen vergleichen |
| **V30** | Krieg zwischen Ländern mit verschiedenen Primärkulturen, später Frieden | begrenzter Kriegs-Zielbeitrag in richtiger Richtung; nach Frieden entfällt er; Meinung bleibt träge |
| **V31** | Kultur im Land wird durch neue Kabinettsaktion gefördert | Zielkultur → Primärkultur gewinnt **Zielwert** solange Aktion aktiv ist; nach Abbruch Beitrag weg |
| **V32** | Zwei Länder fördern dieselbe Zielkultur | gerichtete, bevölkerungsgewichtete Aggregation ohne unabsichtliche Doppelzählung |
| **V33** | Kleine Weltquote 0,05 % trotz relevanter lokaler Quote | nicht vorzeitig gefiltert; Rundung/Genauigkeit nachvollziehbar |
| **V34** | Jahresmodus, Krieg beginnt und endet innerhalb eines Jahres | kein stillschweigend völlig verlorener Kurzeffekt; entweder erfasst oder ausdrücklich bekannte V1-Einschränkung |
| **V35** | Sofort-Meinungsaktionen und Events | keine Aktion verändert ohne Synchronisierung den im Spiel gültigen Mod-Zustand dauerhaft |

## E. GUI und Tooltip

| ID | Prüfung | Erwartetes Ergebnis |
|---|---|---|
| **U01** | Kulturfenster öffnen | beide Spalten `our` und `their`: natives Icon oben, Bar unten |
| **U02** | Balken bei −100, 0, +100 | 0 %, 50 %, 100 % Füllstand, keine UI-Überläufe |
| **U03** | unterschiedliche Richtungen | beide Balken und Icons unabhängig |
| **U04** | Maus auf Balken/Icon | Richtung, Score, Ziel, periodische Änderung und native Stufe |
| **U05** | Maus auf Änderung/Ziel im Tooltip | Breakdown des **Zielwerts**, Abstand und Trägheit nachvollziehbar |
| **U06** | Zielwert und angezeigte Faktoren | Summe entspricht dem Zielwert (inkl. dokumentierter Caps) |
| **U07** | Ruhende Kultur | Tooltip zeigt Ziel **0** und fortlaufende Drift |
| **U08** | Liste sortieren/scrollen/filtern | keine veralteten Daten aus wiederverwendeten Listen-Widgets |
| **U09** | Spalte `our` klicken | keine alte Sofort-Aktion; Spalte `status` weiter benutzbar |
| **U10** | Standardauflösungen und UI-Skalierung | 60er Zeilen, Icons, Bars und Tooltips nicht abgeschnitten |
| **U11** | Monatlich/Jährlich umschalten (vor Spielstart) | Tooltip-Einheit passt zur gewählten Regel; kein falsches Δ |
| **U12** | Spiel starten ohne Daten für Kulturpaar | weder kaputtes Widget noch inkorrekt als 0 gesetzte historische Beziehung |

## F. Performance-Messung

**Pflichtmessungen** (gleiche Hardware/Settings/Save, Vergleich mit Vanilla):

1. **Start:** Zeit für Registry + Initialwerte; Anzahl Länder, Kulturen, neu angelegter **gerichteter** Paare.
2. **Jährlicher Kontakt-Sweep:** Zeit, geprüfte Länder/Kontakte, wieder aktivierte/ruhende Paare.
3. **Periodischer Lauf:** Dauer, Anzahl aktiver/ruhender Paare, Anzahl tatsächlicher `set_cultural_view`-Aufrufe, Script-Value-Zugriffe, verwendete Aufrufquelle.
4. **GUI:** Öffnungszeit, Reaktionszeit beim Sortieren und Tooltip-Aufruf; kein Pop-Vollscan bei Hover.
5. **Skalierung:** frühere Startwelt, großes Vielvölkerreich, weit verstreute Diaspora, großer Krieg und spätes Spiel.
6. **Vergleich:** Monatsmodus und Jahresmodus über identische historische Spielzeit; Scriptzeiten, Savegröße und eventuelle Abweichungen.

**Freigaberegel:** Keine Behauptung „performant“ ohne protokollierte Messungen. Vor dem V1-Release ist ein numerisches Budget im Projekt festzulegen; solange keine Basiswerte existieren, gelten **„keine signifikanten wiederholten Spikes, kein Kultur-Vollmatrix-Scan pro Tick, kein Pop-Vollscan pro Tooltip“** als vorläufige Mindestanforderungen.

## G. Fehler- und Kompatibilitätsaudit

- Scope-/Script-/GUI-Fehler im Log, inklusive dynamischer Modifier/Script-Value-Ausdrücke.
- Vollständige Liste eigener `crw_`-IDs und Vermeidung von Vanilla-Name-Konflikten.
- Wechsel der Primärkultur, neu entstandene oder zusammengeführte Kulturen, aufgelöste Länder, Dynastie-/Kriegswechsel.
- Reaktionen auf Diplomatie-Events und direkte Vanilla-`change_cultural_view`-Aufrufe.
- GUI-Whole-File-Override mit aktuellen Patchdateien vergleichen.
- Savegame-Kompatibilität beim Mod-Update und beim Entfernen der Mod **nicht als garantiert ausgeben**.
- Fehlen einer echten Kulturpaar-Modifier-Kategorie und mögliche verbleibende UI-Rundungsfehler explizit prüfen.

## H. Ergebnisprotokoll (je Test)

- **Test-ID:** …
- **Build/Mod-Commit:** …
- **Quelle und Nachweis des Script-Konstrukts:** …
- **Testaufbau und Ausgangswert:** …
- **Erwartet:** …
- **Beobachtet / Log / Screenshot:** …
- **Performance, sofern relevant:** …
- **Ergebnis:** OFFEN / BESTANDEN / FEHLGESCHLAGEN / BLOCKIERT
- **Notwendige Maßnahme:** …

**Gesamtstatus am 20.11.2026: alle Tests offen.**
