# 6. Kulturmeinungen im Kulturfenster

**Status:** UI-Entwurf, 20.11.2026. Weitere visuelle Entscheidungen und Gameplay-Details sind noch offen.

## Ziel

Die beiden vorhandenen Spalten zur **gerichteten Kulturmeinung** in der Tabelle **Culture Breakdown** bleiben an ihrem bisherigen Platz. Statt des alleinstehenden Meinungsicons erscheint in jeder Spalte:

1. **Oben:** das bisherige Symbol für `enemy`, `negative`, `neutral`, `positive` oder `kindred`.
2. **Darunter:** ein schmaler **horizontaler Fortschrittsbalken** für den internen kontinuierlichen Meinungswert von **−100 bis +100**.

Kulturanteil, Kulturstatus, Icons für akzeptierte Kulturen und sonstige Funktionen dieser Ansicht bleiben wie im Vanilla-Spiel.

## Bedeutung der beiden Spalten

- **Unsere Meinung:** Primärkultur unseres Landes → Kultur der Tabellenzeile.
- **Ihre Meinung:** Kultur der Tabellenzeile → Primärkultur unseres Landes.

Die Werte dürfen verschieden sein. Der Balken repräsentiert jeweils ausschließlich **seine eigene Richtung**.

## Darstellung

- Das bestehende Zustandsicon bleibt unverändert und oberhalb des Balkens zentriert.
- Bar-Füllung verläuft von **−100 links (0 %)** über **0 in der Mitte (50 %)** bis **+100 rechts (100 %)**. Ein dezenter Mittelpunktmarker wäre hilfreich.
- Optional Farbcode: negativ rot, um 0 neutral, positiv grün. Keine neuen Meinungsicons erforderlich.
- Eine numerische Beschriftung **in der engen Tabellenzelle** ist vorerst nicht vorgesehen; der exakte Wert steht im Tooltip.
- Die aktuellen interaktiven Klicks für unmittelbare Kulturmeinungsänderung sollen **aus dieser Spalte verschwinden**. Die Darstellung ist passiv.

## Tooltips pro Richtung

Beim Überfahren von Icon oder Balken wird ein **gemeinsamer Tooltip für die betreffende Richtung** angezeigt.

**Beispiel (fiktive Werte):**

> Kastilisch → Portugiesisch
>
> Kulturmeinung: **+43,5 / +100** · Positiv
>
> Entwicklung: **+0,32 pro Monat**
>
> Nächste Meinungsstufe: **Kindred ab +60**

Im **Jahresmodus** soll die Entwicklung entsprechend auf das gewählte Intervall bezogen sein, beispielsweise „+3,8 pro Jahr“. Die angegebene Änderungsrate muss aus der gleichen Berechnung stammen, die den Meinungswert fortschreibt.

## Tooltip der Änderungsrate

Der **Änderungswert** ist ein **eigener erklärbarer Einflusswert**. Auf ihn soll ein separater Hover / verschachtelter Tooltip die konkreten Beiträge anzeigen.

Beispiel (rein illustrativ):

| Einfluss | Beitrag pro Monat |
|---|---:|
| Gemeinsames Zusammenleben | +0,18 |
| Gute Diplomatie | +0,12 |
| Kabinettsaktion | +0,10 |
| Religiöse Spannungen | −0,08 |
| **Gesamtänderung** | **+0,32** |

Jede Zeile muss nach Möglichkeit auf die jeweils **gerichtete Kulturbeziehung** bezogen sein; die UI darf keine Veränderung suggerieren, die in der Simulation nicht tatsächlich berechnet wurde.

## Offene visuelle Entscheidungen

- Balken mit Füllstand von links nach rechts oder divergierender Balken ab der Mitte? Vorläufig links-nach-rechts mit sichtbar markierter Null.
- Soll auf Wunsch die **Differenz zur nächsten Stufe** erscheinen?
- Soll die Farbe der Leiste die aktuelle native Stufe oder den exakten numerischen Wert widerspiegeln?
- Bei der Primärkultur: wie im Vanilla-Spiel Meinungsindikatoren ausblenden; kein künstlicher Eigenschaftswert für Kultur → dieselbe Kultur.

## Unverändert

**Kulturakzeptanz, Tolerierung, Diskriminierung, Kulturkapazität, Assimilation und deren Vanilla-Aktionen werden in dieser Mod nicht ersetzt.** Der Fortschrittsbalken visualisiert allein unsere neue **kontinuierliche Meinung**.
