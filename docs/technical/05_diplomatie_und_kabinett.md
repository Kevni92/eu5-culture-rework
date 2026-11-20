# Technik 5: Diplomatie und Kabinettsaktion

**Konzept:** [Staatlicher Einfluss, Abschnitte Diplomatie und Kabinett](../concept/03_staatlicher_einfluss.md).

## Diplomatie

**Gewünschtes Verhalten:** Das Verhältnis von Land A zu Land B (z. B. Meinung, Vertrauen, Bündnis) verändert **Primärkultur A → Primärkultur B**. Der Beitrag wird über den Anteil von Kultur A im Land A an allen Angehörigen von Kultur A gewichtet. Umgekehrte Beziehungen werden separat betrachtet.

**Belege:** `on_scripted_relation_created` wird in Vanilla genutzt; `on_war_declared`, `on_ending_war`, `on_culture_changed` sind weitere sinnvolle Aktualisierungspunkte.

**Offen:** Lesbarkeit und Wertebereich für bilaterale Meinung/Vertrauen in der aktuellen Script-API, Ereignis bei Vertrauensänderung, allgemeiner Hook für Diplomatieende, sowie Kosten regelmäßiger Aktualisierung. Diplomatieeinfluss kann ggf. monatlich aus einem jährlichen relevanten Länderpaar-Register berechnet werden, wenn kein Änderungs-Hook besteht.

## Kabinettsaktion „Kulturelle Annäherung“

**Gewünschtes Verhalten:** Ein Land beeinflusst eine **eigene, im Land ansässige Zielkultur** in ihrer Meinung zur eigenen Primärkultur. Stärke abhängig von Bevölkerung, Kabinettsmitglied und laufendem Aufwand. Keine globale Sofortaktion und kein künstlicher 50-Jahre-Cooldown.

**Beleg:** `in_game/common/cabinet_actions/promote_culture.txt` zeigt ein bestehendes Skriptmuster mit `ability`, Zielen, `on_activate`, `on_deactivate`, `progress` und kontinuierlichem Standortmodifikator. Das ist ein **brauchbarer Ausgangspunkt**, nicht der Beweis für einen monatlichen frei wählbaren Culture-Pair-Effect.

## Nächste Prüfungen

1. Aktuelles Cabinet-Action-Schema, Updatehäufigkeit und Ziel-Scope aus `script_docs` / Vanilla ermitteln.
2. Sicherstellen, dass **Zielkultur** und **eigene Primärkultur** im Effekt gemeinsam referenzierbar sind.
3. Prüfen, wie eine aktive Aktion einen **monatlichen Einflussbeitrag** registriert und nach Ende korrekt entfernt.
4. Spieler-, AI- und Save/Load-Verhalten testen; Doppelzählung vermeiden, wenn mehrere Länder dieselbe Kultur fördern.

**Status:** Hook-/Cabinet-Muster **im älteren Stand belegt**, konkreter Kulturmeinungseffekt **offen**.

## Referenzen

- [Vanilla: on_action/diplomatic_reactions.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/on_action/diplomatic_reactions.txt)
- [Vanilla: cabinet_actions/promote_culture.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/cabinet_actions/promote_culture.txt)
- [Vanilla: on_action/_hardcoded.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/on_action/_hardcoded.txt)
