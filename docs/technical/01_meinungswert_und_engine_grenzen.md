# Technik 1: Gerichteter Kulturmeinungswert und Engine-Grenzen

**Konzept:** [Kontinuierliche Kulturbeziehungen](../concept/01_kulturbeziehungen.md) und [Auswirkungen](../concept/05_auswirkungen_und_offene_fragen.md).

## Bestätigte Beobachtungen im alten Referenzstand

- `in_game/common/generic_actions/culture.txt` enthält Aktionen zum Akzeptieren, Tolerieren, Diskriminieren und Entfernen dieser Zustände. Die Statusverwaltung ist also mindestens teilweise in Skriptaktionen sichtbar.
- `in_game/common/cabinet_actions/promote_culture.txt` fragt bestehende Kulturstatus ab und nutzt einen Kulturmeinungs-Einfluss. Das zeigt Abhängigkeiten zu anderen Mechaniken.
- Der Kulturstatus und die kulturbezogene Kosten-/Pop-Logik können zusätzlich **fest in der Engine** verankert sein. Die bloße Änderbarkeit einer Aktion beweist **nicht**, dass sich native Stufen und ihre Folgen durch einen Ersatzwert ablösen lassen.

## Zu untersuchen

1. Kann ein **persistenter, gerichteter Wert je Kulturpaar** angelegt, gelesen und effizient verändert werden?
2. Sind Variable-Maps mit Kultur-Objekten als Schlüsseln und einer zugehörigen Zahl möglich? Können Werte vorhandener Schlüssel zuverlässig aktualisiert und aus Saves geladen werden?
3. Lässt sich die **native kulturelle Meinung** selbst numerisch setzen oder existieren nur diskrete Engine-Enums? Falls nur Enum: Ist ein paralleler Modwert mit eigener Anzeige notwendig?
4. Können Kulturkapazitätskosten, Pop-Zufriedenheit, Assimilation und politische Rechte **kontinuierlich** aus dem neuen Wert berechnet werden, oder werden sie engineintern ausschließlich aus Akzeptanzstufen abgeleitet?
5. Was geschieht bei Änderungen der Primärkultur, Kulturentstehung, Assimilation und Auflösung bestehender Staaten?

## Vorläufiges Urteil

**Offen / hohes Risiko** für vollständigen Ersatz der Engine-Kulturstatus. Ein gerichteter Modwert ist als Forschungsansatz plausibel, aber noch nicht implementierbar belegt. Eventuell ist zuerst nur ein zusätzlicher Simulationswert mit teilweiser Übertragung auf Vanilla-Boni möglich; das wäre eine bewusste Designabweichung und kein stillschweigender Ersatz.

## Referenzen

- [Vanilla: generic_actions/culture.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/generic_actions/culture.txt)
- [Vanilla: cabinet_actions/promote_culture.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/cabinet_actions/promote_culture.txt)
- [Referenz-Docs: on_actions.log](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/on_actions.log)
