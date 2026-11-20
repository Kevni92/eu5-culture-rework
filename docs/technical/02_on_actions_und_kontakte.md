# Technik 2: Kontakte über on_actions aktivieren und deaktivieren

**Konzept:** [Berührungspunkte und Relevanz](../concept/04_beruehrungspunkte_und_relevanz.md).

## Gefundene Hooks (EU5 1.4.3-beta-Spiegel)

| Anlass | In Vanilla nachgewiesener Hook | Verwendungszweck |
|---|---|---|
| Spielstart | `on_game_start` | Einmaliger Initialabgleich der vorhandenen Kontakte |
| Kriegserklärung | `on_war_declared` | Potenziell neues Kontaktpaar |
| Kriegsteilnahme | `on_join_war` | Zusätzliche Beteiligte erfassen |
| Kriegsende | `on_ending_war` | Kriegs-Kontaktbeitrag entfernen; historische Meinung behalten |
| Gebietswechsel | `on_location_changed_owner` | Betroffene Länder/Kulturen neu bewerten |
| Staatskulturwechsel | `on_culture_changed` | Länderbeiträge der alten/neuen Primärkultur aktualisieren |
| Entlassung eines Staates | `on_released_country` | Neue staatliche Kontaktbeiträge prüfen |
| Politischer Kurs | `on_policy_changed` | Relevante staatliche Einflussfaktoren aktualisieren |
| Neue bestimmte Diplomatiebeziehung | `on_scripted_relation_created` | Bündnisse/andere Beziehungen berücksichtigen |
| Wiederkehrende Prüfungen | `monthly_country_pulse`, `yearly_country_pulse` | Wählbare Meinungskalkulation und periodischen Abgleich ausführen; Mehrfachberechnung pro Kulturpaar vermeiden |

`on_scripted_relation_created` wird konkret in `diplomatic_reactions.txt` verwendet. `on_actions.log` führt die genannten jährlichen und monatlichen Länder-Pulse auf.

## Fehlende Belege

- Im geprüften Referenzmaterial wurde **kein `on_pop_migration`-Hook** nachgewiesen. Das ist **kein Beweis**, dass er in der aktuellen Engine nicht existiert.
- Ein komplementärer, unmittelbar passender **Hook für das Ende jeder Diplomatiebeziehung** ist bisher ebenfalls nicht belegt.
- Für jeden Hook müssen tatsächliche Scopes, Zeitpunkt, Mehrfachausführung und Mod-Erweiterungsverhalten im **aktuellen** Build überprüft werden.

## Geplanter Kontakt-Lebenszyklus

1. **Initialisieren:** beim Spielstart relevante bestehende Kulturkontakte ermitteln.
2. **Ereignisgetrieben aktualisieren:** ein passender Hook markiert betroffene Länder/Kulturpaare als neu zu bewerten.
3. **Aktiv halten:** Entwicklung **monatlich oder jährlich**, je nach Einstellung, ausschließlich für relevante Beziehungen; `set_cultural_view` nur beim Wechsel der abgeleiteten nativen Meinungsstufe.
4. **Ruhend stellen:** Wenn **keinerlei relevante Kontakte** mehr bestehen, wird der **Zielwert 0**. Der bestehende Meinungswert bleibt gespeichert, läuft aber **zeitabhängig langsam in Richtung 0**. Ein bloßes Aussetzen ohne Meinungsveränderung ist nicht mehr vorgesehen.
5. **Jährlich abgleichen:** veränderte Bevölkerungszusammensetzung, Migration, Diplomatieende oder fehlende Hooks auffangen.

**Performance-Frage:** Bei inaktiven Paaren ist keine Neuberechnung von Religion, Bevölkerung und Diplomatie erforderlich. Denkbar sind zeitbasierte Berechnung seit der letzten Aktualisierung („lazy decay“) und/oder seltene synchronisierende Updates. **Offen:** Wenn wir nicht regelmäßig `set_cultural_view` ausführen, bleibt die native Stufe einer inaktiven Beziehung zwischenzeitlich veraltet. Beim Reaktivieren muss deshalb vor der nächsten Spielwirkung nachgezogen werden; wie viel ältere native Stufen außerhalb aktiver Kontakte tolerierbar sind, braucht einen Runtime-Test.

**Wichtige Einschränkung:** `on_actions` ersetzen keinen Mechanismus zur effizienten Auflistung tatsächlich betroffener Kulturpaare. Ob sich diese Paare persistent registrieren und ohne globale Vollsuche iterieren lassen, ist offen.

**Zusatzprüfung:** Monatliche oder jährliche Pulse treten pro Land auf. Eine globale gerichtete Kulturbeziehung darf **nicht bei jedem betroffenen Land erneut fortgeschrieben werden**. Zu testen sind eine einmalige zentrale Ausführung, die gewählte Frequenz sowie eine getrennte jährliche Kontakt-/Pop-Neubewertung.

**Status:** Geeignete Ereignis-Anknüpfungspunkte sind **belegt im alten Stand**; lückenlose Aktivierung/Deaktivierung und einmalige Ausführung pro Kulturpaar **nicht belegt**. [Synchronisierungsplan](08_hybrid_synchronisierung_und_intervalle.md).

## Referenzen

- [Vanilla: on_action/_hardcoded.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/on_action/_hardcoded.txt)
- [Vanilla: on_action/diplomatic_reactions.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/on_action/diplomatic_reactions.txt)
- [Engine-Referenz im Repository: on_actions.log](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/on_actions.log)
