# Technik 6: Krieg und kulturell zugeordnete Verluste

**Konzept:** [Meinungsentwicklung, Kriegsbeispiel](../concept/02_meinungsentwicklung.md).

## Belegte Einstiegspunkte im älteren Vanilla-Spiegel

- `on_war_declared`, `on_join_war`: Kriegsbeteiligung und potenzielle Kulturkontakte.
- `on_battle_won`, `on_battle_lost`, `on_great_battle_won`, `on_great_battle_lost`: Kampfereignisse.
- `on_siege_won`, `on_location_occupied`, `on_location_changed_owner`: Belagerung, Besetzung und territoriale Folgen.
- `on_ending_war`: Ende des laufenden Konflikts und möglicher Übergang zu Nachkriegsfolgen.

## Kritische Datenlücke

Ein vorhandener Schlacht-Hook belegt **nicht**, dass dessen Scope die Anzahl der getöteten Soldaten **aufgeschlüsselt nach Pop-Kultur** bereitstellt. EU5-Armeen und Verluste können Mischungen mehrerer Kulturgruppen sein.

### Umsetzungsstufen

1. **Minimal:** Krieg zwischen Ländern beeinflusst nur ihre Primärkulturen. Unvollständig, aber wahrscheinlich leichter umzusetzen.
2. **Erweitert:** Tatsächlich betroffene Kulturgruppen nach Rekrutierungsgebiet/Armeeherkunft gewichten, **falls** verlässlich zugänglich.
3. **Vollständig:** Gefallene, zivile Verluste, Plünderung und Vertreibung kulturgenau berücksichtigen – nur bei gesicherten Engine-Daten.

Kein Schritt darf Opferzahlen oder Kulturzuordnungen erfinden. Besonders bei Aufständen, Söldnern und multiethnischen Armeen besteht Fehlergefahr.

## Prüfauftrag

Aktuelle Event-Scopes, Battle-/Unit-/Pop-Zugriff, Verluste und Doppelaufrufe überprüfen; dann die Laufzeitkosten im großen Krieg messen. Aktivierung eines Kulturpaares bei Kriegserklärung ist getrennt vom späteren tatsächlichen Verlusteffekt zu behandeln.

**Status:** Kriegs-/Schlacht-Hooks **im älteren Stand belegt**, kulturgenaue Opferberechnung **offen**.

## Referenz

- [Vanilla: on_action/_hardcoded.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/on_action/_hardcoded.txt)
