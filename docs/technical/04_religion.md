# Technik 4: Religion und religiöse Kulturzusammensetzung

**Konzept:** [Staatlicher Einfluss, Abschnitt Religion](../concept/03_staatlicher_einfluss.md).

## Zielrechnung (noch kein nachgewiesener Scriptpfad)

Einmal jährlich wird die **Religionsverteilung je Kultur** ermittelt. Religiöse Beziehungen und die staatliche Behandlung dieser Religionen ergeben einen Einfluss auf die monatliche Änderung einer **gerichteten** Kulturmeinung.

Beispielannahme: 99 % Kastilier christlich, 90 % Andalusier muslimisch. Das soll **nicht automatisch** negative Meinung bedeuten: Ausschlaggebend sind das Verhältnis der konkreten Religionen und Kastiliens religionspolitisches Verhalten.

## Technisch zu klären

1. Gibt es in der aktuellen Script-API einen effizienten Zugriff auf die **weltweite Bevölkerung je Kultur und Religion**, möglichst ohne Pop-Vollscan?
2. Können länderspezifische Verteilungen ermittelt werden, wenn staatliche Religionspolitik einfließt?
3. Welche zugänglichen Werte repräsentieren **gerichtete Religionsmeinungen** oder die religiöse Toleranz eines Landes?
4. Wie werden mehrere Religionen je Kultur **gewichtet**? Eine Mehrheitsreligion darf Minderheiten nicht vollständig unsichtbar machen.
5. Wie werden die **jährlichen Snapshots** für 12 Monate wiederverwendet und nach größeren Religionswechseln erneuert?

## Einschätzung

**Offen**: Keine belastbare aktuelle API für die globale Verteilung `Kultur × Religion` geprüft. Nicht vorwegnehmen, dass die Engine diese Aggregation direkt anbietet. Eine vollständige Pop-Iteration über alle Kulturen wäre die risikoärmste semantische, aber möglicherweise die teuerste technische Lösung; ihr Einsatz bedarf eines Benchmarks.

## Quellen für die nächste Prüfung

- [Vanilla-Spiegel: on_action/_hardcoded.txt (on_religion_changed)](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/on_action/_hardcoded.txt)
- [Vanilla-Spiegel: generic_actions/culture.txt](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/generic_actions/culture.txt)
- Aktuell installierte `script_docs` und Vanilla-Dateien der **gleichen Buildnummer** sind noch erforderlich.
