# Technik 9: Zwei Kulturmeinungsspalten, Progressbars und Tooltip-Aufschlüsselung

**Konzept:** [UI-Entwurf](../concept/06_kulturmeinung_im_kulturfenster.md). **Status:** Vanilla-UI-Struktur im 1.4.3-beta-Spiegel direkt nachgewiesen, dynamische Bindings/Runtime noch nicht verifiziert.

## 1. Richtige Vanilla-Datei und Einbauort

**Primärdatei:** [`in_game/gui/culture_lateral_view.gui`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/gui/culture_lateral_view.gui).

In `types CultureOverview` → `society_view_breakdown_subtab` → `filtered_sorted_list` (`name = "culture_list"`) ist das Listendatenmodell `[CountryCultureLateralView.GetImportantCultures]`. Die gerenderte Zeile ist **60 GUI-Einheiten** hoch, verwendet `[ImportantCultureItem.GetCulture]` als Datenkontext und setzt `datamodel_reuse_widgets = yes`.

Relevante Sortierspalten:

| Schlüssel | Spaltenbreite laut Vanilla | Daten |
|---|---:|---|
| `size` | 75 | Anteil der Kultur im Land |
| `our` | 45 | eigene Primärkultur → Zeilenkultur |
| `their` | 45 | Zeilenkultur → eigene Primärkultur |
| `status` | 70 | Kulturakzeptanz |

**Wichtig:** `in_game/gui/attribute_columns/culture.gui` ist **nicht** die Tabellenimplementierung dieser Ansicht; es enthält allgemeine Kultur-Auswahlspalten und sollte hier nicht bearbeitet werden.

## 2. Vorhandene Zeilen-Widgets

- **`our`:** `targetted_button_regular` mit Links-/Rechtsklick auf `improve_our_cultural_view` und `reduce_our_cultural_view`. Darin das Meinungsicon als `text_single` über `[ImportantCultureItem.GetOurOpinionIcon]`, vorhandener Tooltip über `[ImportantCultureItem.GetOurOpinionTooltip]`.
- **`their`:** eigenständiges `text_single` mit `[ImportantCultureItem.GetTheirOpinionIcon]` und `[ImportantCultureItem.GetTheirOpinionTooltip]`.
- **`status`:** davon unabhängig; die Vanilla-Akzeptanzbuttons sind **unverändert beizubehalten**.
- Die Primärkultur blendet die Meinungsfelder bereits aus (`[Not(ImportantCultureItem.IsPrimary)]`).

**Vorgesehene Änderung:** `our`-Button ersetzen durch passives Spaltenwidget, das Icon und Bar in einem **vertikalen Layout** enthält. `their` analog. Beide Richtungen behalten ihre eigenen Tooltip-Daten. Sortier-Highlights nach Möglichkeit erhalten.

## 3. Progressbar: Prinzip belegt

Vanilla verwendet `progressbar` bzw. `progressbar_framed` und Vorlagen unter [`main_menu/gui/shared/progressbars.gui`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/main_menu/gui/shared/progressbars.gui). Konkrete Vorbilder:

- [`in_game/gui/attribute_columns/rebel.gui`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/gui/attribute_columns/rebel.gui): `progressbar` mit einem an das Datenmodell gebundenen numerischen Füllstand.
- [`in_game/gui/diseases_lateralview.gui`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/gui/diseases_lateralview.gui): `progressbar_framed` mit `min`, `max`, `value`.

**Geplante Normalisierung:** Füllstand **(Meinungswert + 100) / 2** im Bereich 0–100. 0 Meinung ergibt 50 % Füllstand. Bei nur 35–45 GUI-Einheiten Breite muss der Balken kompakt bleiben (z. B. ca. 30–34 breit und 5–7 hoch). Sichtbare Nullmarkierung und korrekte Abstände **Ingame testen**.

## 4. Zugriff auf unsere Werte aus der GUI

Der ältere [`data_types_script.txt`-Dump](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/data_types_script.txt) enthält **`Culture.MakeScope`**, **`Scope.GetVariable`** und **`Scope.GetVariableFromVariableMap`**. In anderen [Community-Mod-GUIs](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_mods/3739393770/in_game/gui/shared/aaa_dmsd_stability_tooltip.gui) werden numerische Map-Werte aus einem Scope über `GetVariableFromVariableMap(...).GetValue` ausgegeben.

**Plausibler Datenweg:** Gerichtete Variable-Map auf **Quellkultur**, Zielkultur als Schlüssel. Für `our` die Primärkultur des Spielers als Quelle und die Zeilenkultur als Schlüssel; für `their` umgekehrt.

**Noch offen:** Ist der Kultur-Scope direkt beschreibbar und kann der `ImportantCultureItem`-Kontext diese beiden Culture-Objekte an die Map-Leseoperation liefern? Die angezeigten GUI-Formeln, Formatierung, Refresh und Fehlverhalten bei fehlenden Paaren müssen **im aktuellen Build** getestet werden. Wiederverwendete Listen-Widgets dürfen keine Werte der vorherigen Zeile anzeigen. Keine ungeprüften Methoden erfinden.

## 5. Tooltip / Subtooltip

Vanilla hat bereits `SimpleContextualStringPairTooltipType`, aber dessen aktuelle Texte sind allein **Engine-generierte native Meinungsbeschreibungen**. Die Tooltip-Erweiterung muss für die Mod eigene Daten ausgeben:

- **Richtung**, **kontinuierlicher Wert** (−100…+100), **aktuelle native Stufe**;
- **Änderung pro Berechnungsintervall**, korrekt als „pro Monat“ oder „pro Jahr“;
- optional Distanz zur nächsten Stufe.

Ein **Hover auf den Änderungswert innerhalb des Tooltip** soll eine separate Aufschlüsselung bieten. Das Muster **verschachtelter Tooltip-Widgets** ist in [Community-Mod: `aaa_dmsd_stability_tooltip.gui`](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_mods/3739393770/in_game/gui/shared/aaa_dmsd_stability_tooltip.gui) dokumentiert und implementiert; daraus folgt **Plausibilität**, aber noch keine Bestätigung für unseren spezifischen Widget-Kontext.

**Wichtige Designregel:** Die Aufschlüsselung stammt aus **den tatsächlich berechneten Beiträgen** (Religion, Diplomatie, Kabinettsarbeit, Krieg, Zusammenleben), nicht aus separaten GUI-Schätzungen.

## 6. „Eigener Modifier“ für die Änderungsrate – Engine-Grenze

Die gewünschte Änderungsrate soll als **eigenständige berechnete Größe** mit Faktoren-Aufschlüsselung geführt werden, **unabhängig vom gespeicherten Meinungswert**.

Im älteren [Modifier-Type-Katalog](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/main_menu/common/modifier_type_definitions/00_modifier_types.txt) sind als Kategorien u. a. `country`, `location`, `religion` genannt, **nicht `culture` oder `culture_pair`**. Im [Engine-Effekt-Dump](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/effects.log) ist auch kein `add_culture_modifier` belegt.

**Daher derzeit NICHT als nachgewiesen behaupten, dass ein neuer nativer Modifier je gerichtetes Kulturpaar möglich ist.**

Praktischer Forschungsansatz:
- Pro Kulturpaar einen **separaten numerischen Änderungswert** speichern.
- Beiträge als **einzeln erklärbare Komponenten** führen oder bei der regelmäßigen Aktualisierung berechnen.
- Die **Summe** ist der Änderungswert; ein **Modifier-artiger Tooltip** listet seine Einflüsse auf.
- Wenn der aktuelle Build doch einen echten Culture-/Pair-Modifier unterstützt, kann diese Berechnungsgröße später an einen echten Engine-Modifier gebunden werden.

**Testanforderung:** Hauptwert und Tooltip-Summen müssen beim monatlichen/jährlichen Lauf konsistent sein. Keine Pop- oder GUI-Volliteration zur bloßen Darstellung des Tooltips.

## 7. Performance, Update-Takt und Mod-Kompatibilität

- Alle teuren Einflussfaktoren **beim monatlichen oder jährlichen Simulationslauf aggregieren**, nicht pro gezeichneter Tabellenzelle / Tooltip-Hover neu berechnen.
- Gleiche **effektive Änderungsrate** bei Monats-/Jahresmodus; im Tooltip den tatsächlichen eingestellten Modus nennen.
- Nur registrierte aktive Paare aktualisieren; UI liest **Cache-Werte** der sichtbaren Zeilen.
- Eingriffe in `culture_lateral_view.gui` können **Whole-file-Overrides** erfordern. UI-Mod-Kompatibilität und Patch-Stand prüfen. Ein 1.4.3-beta-Layout darf nicht ungeprüft in aktuelle EU5-Versionen übernommen werden.
- Die bisherigen interaktiven `our`-Aktionen und ihre weiteren Einstiegspunkte müssen auf die beschlossene automatische Entwicklung umgestellt werden.

## Nächste Tests

1. `our`-Button durch passives Icon+Bar-Widget ersetzen und Layout in 60 Einheiten prüfen.
2. Dynamisches GUI-Lesen **beider Richtungen** via `Culture.MakeScope`/Map testen.
3. Tooltip mit Score und Intervallrate hinzufügen; Subtooltip für Änderungsrate testen.
4. Berechnungscache und Text beim Wechsel Monat/Jahr, Save/Load, Sortieren und Scrollen prüfen.
5. Keine Änderung der Vanilla-Akzeptanzbuttons oder -Kapazitätsanzeige.

**Referenzstatus:** **ALT_1.4.3_BETA**; keine Laufzeitfreigabe.
