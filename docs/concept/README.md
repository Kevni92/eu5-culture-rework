# Gameplay-Konzept: EU5 Culture Rework

Hier steht ausschließlich **was im Spiel geschehen soll und warum**. Keine Script-Syntax, Dateipfade, Hooks oder Implementierungspläne. Technische Machbarkeit wird getrennt unter [../technical/](../technical/README.md) dokumentiert.

## Leitbild

Kulturelle Beziehungen sind historische, gesellschaftliche Prozesse. Ein **gerichteter kontinuierlicher Wert von −100 bis +100** entwickelt sich durch Politik, Diplomatie und gesellschaftlichen Kontakt. Dieser Wert steuert **die fünf bestehenden Kulturmeinungsstufen**. **Kulturakzeptanz und alle damit verbundenen Vanilla-Regeln bleiben unverändert.**

## Mechaniken

1. [Kontinuierliche Kulturbeziehungen](01_kulturbeziehungen.md)
2. [Einflüsse und wahlweise monatliche/jährliche Entwicklung](02_meinungsentwicklung.md)
3. [Staatlicher Einfluss: Religion, Diplomatie und Kabinett](03_staatlicher_einfluss.md)
4. [Berührungspunkte und Relevanz](04_beruehrungspunkte_und_relevanz.md)
5. [Gameplay-Folgen und offene Designfragen](05_auswirkungen_und_offene_fragen.md)
6. [Kulturmeinung im Kulturfenster: Icon, Fortschrittsbalken und Tooltip](06_kulturmeinung_im_kulturfenster.md)

**Stand:** Architekturentscheidung vom 20.11.2026: Hybrid aus kontinuierlichem Meinungsscore und bestehenden fünf Stufen. Die Grenzen −60/−20/+20/+60 sind beschlossen; weitere Einflussgewichte bleiben Balancing-Entwürfe.
