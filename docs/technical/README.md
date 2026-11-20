# Technische Umsetzungsanalyse

In diesem Ordner stehen **ausschließlich** Implementierungsfragen, konkrete Fundstellen, Verifikationsstatus, Risiken und Ergebnisse späterer Tests. Das gewünschte Spielverhalten liegt unter [../concept/](../concept/README.md).

## Evidenzstand

- **Untersuchter Textspiegel:** `HLJSXK/eu5-modding-base`, Commit `9e941cedccf963921733810a041ba912b6b8ceb7`, vom Maintainer als **EU5 1.4.3 beta** bezeichnet.
- Im selben Repository liegen generierte `on_actions.log`-Referenzen; deren Buildgleichheit mit dem Textspiegel ist **nicht separat gesichert**.
- **Nicht verifiziert:** Vollständige aktuelle Originaldateien, Engine-Dumps und Laufzeittests für die installierte EU5-Version. Aussagen aus 1.4.3 beta sind **keine Freigabe für 1.4.6/1.4.9 oder spätere Patches**.
- Ergebnisstatus: **Belegt im alten Referenzstand / Plausibel / Offen / Blockiert**. „Belegt“ bedeutet ausdrücklich **nicht** „in aktueller Version getestet“.

## Mechanismen und Prüfergebnis

| Mechanismus | Erste Einschätzung | Details |
|---|---|---|
| Gerichteter Zahlenwert −100…+100 | **Plausibel**: numerische Variable-Maps belegt; Kulturpaar-Persistenz offen | [01](01_meinungswert_und_engine_grenzen.md) |
| Kontakt aktivieren/deaktivieren über Ereignisse | **Teilweise belegt**: mehrere passende `on_actions`, keine lückenlose Pop-Hook-Kette | [02](02_on_actions_und_kontakte.md) |
| Relevanzschwellen und Bevölkerungsgewichtung | **Plausibel**: länderspezifische Kulturwerte sind im Vanilla-Script lesbar; globale Aggregation offen | [03](03_bevoelkerung_und_performance.md) |
| Jährliche Religionszusammensetzung | **Offen**: Datenzugriff und Aggregationskosten zu prüfen | [04](04_religion.md) |
| Diplomatie und Kabinettsaktion | **Teilweise belegt**: Diplomatie-Hook und Cabinet-Action-Muster vorhanden; gewünschte Effekte offen | [05](05_diplomatie_und_kabinett.md) |
| Kriege und kulturell zugeordnete Opfer | **Teilweise belegt**: Kriegs-/Schlachthooks; kulturbezogene Verlustdaten offen | [06](06_krieg.md) |
| Synchronisierung auf fünf Vanilla-Meinungsstufen | **Beschlossener Ansatz**, `set_cultural_view` belegt im alten Stand; Grenzwerte und Intervall technisch zu testen | [08](08_hybrid_synchronisierung_und_intervalle.md) |
| Kulturfenster: Icon + Balken + Tooltip pro Richtung | **GUI-Struktur und Progressbar-Vorbild im 1.4.3-beta-Spiegel belegt;** Bindung eigener Kulturpaarwerte und echter Modifier ungetestet | [09](09_gui_kulturmeinung.md) |
| Konkreter V1-Implementierungsplan | **Dokumentiert, Umsetzung noch nicht gestartet** | [10](10_implementierungsplan_v1.md) |
| Engine-Verifikationsmatrix und V1-Abnahme | **Quellenfundstellen geprüft, aktuelle Runtime-Tests offen** | [11](11_v1_verifikationsmatrix.md), [12](12_v1_abnahme_und_tests.md) |
| Konkreter Implementierungs- und Testnachweis | **Noch ausstehend** | [07](07_pruefplan.md) |

**Verbindliche Architekturentscheidung (20.11.2026):** Die neue kontinuierliche Meinung steuert **ausschließlich die bestehende fünfstufige Kulturmeinung**. Bei einem Grenzwechsel wird die native Stufe mit `set_cultural_view` synchronisiert. **Kulturakzeptanz und deren Vanilla-Effekte bleiben unverändert.** Berechnung wahlweise monatlich oder jährlich; technische Konfiguration noch zu validieren.

**Arbeitsprinzip:** Erst Mechanik gegen passende Engine-/Vanilla-Version nachweisen, dann implementieren. Keine imaginären Trigger oder Effects annehmen.
