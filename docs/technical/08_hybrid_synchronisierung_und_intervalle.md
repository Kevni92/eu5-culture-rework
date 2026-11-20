# Technik 8: Kontinuierliche Meinung → native Kulturmeinungsstufen (beschlossener Plan)

**Konzept:** [Kontinuierliche Kulturbeziehungen](../concept/01_kulturbeziehungen.md) und [Meinungsentwicklung](../concept/02_meinungsentwicklung.md).

**Status:** Zielarchitektur beschlossen (20.11.2026), nicht implementiert. Referenzquellen aus EU5 1.4.3 beta; aktueller Spielbuild noch nicht bestätigt.

## Architekturentscheidung

- Die **einzige neu berechnete Kulturmechanik** ist ein **gerichteter Zahlenwert je Kulturpaar** im Bereich **−100 bis +100**.
- Die **native Kulturmeinung** bleibt im Spiel wirksam und wird aus dem Zahlenwert abgeleitet.
- **Kulturakzeptanz, Tolerierung, Diskriminierung, Kulturkapazität, Assimilation und weitere statusabhängige Vanilla-Effekte bleiben unverändert**. Die Mod erzeugt dafür keine alternativen Modifier oder eigene Berechnung.
- Lediglich die schon vorhandenen Auswirkungen der **fünf nativen Kulturmeinungsstufen** ändern sich, wenn eine Grenze überschritten wird.
- Die bisherigen direkten Vanilla-Meinungsaktionen müssen noch auf Abschaltung/Umleitung geprüft werden, damit sie den Modwert nicht umgehen.

## Eindeutige Übersetzung (Grenzen links inklusive)

| Modwert | Native Stufe |
|---|---|
| −100 ≤ x < −60 | `enemy` |
| −60 ≤ x < −20 | `negative` |
| −20 ≤ x < 20 | `neutral` |
| 20 ≤ x < 60 | `positive` |
| 60 ≤ x ≤ 100 | `kindred` |

Damit gilt z. B. **−60 = negative**, **−20 = neutral**, **20 = positive**, **60 = kindred**. Der Wert wird auf [−100, +100] begrenzt. **Keine Hysterese beschlossen**; kleine Schwankungen an einer Grenze können daher Stufenwechsel in aufeinanderfolgenden Berechnungen auslösen.

## Synchronisierung

1. Nur **aktiv registrierte, gerichtete Kulturpaare** entsprechend dem gewählten Berechnungsintervall auswerten.
2. Numerischen Wert aus gespeicherter Meinung und gewichteten gesellschaftlichen Einflüssen fortschreiben, auf [−100, +100] begrenzen.
3. Zielstufe eindeutig anhand der Tabelle bestimmen.
4. Tatsächliche native Stufe für exakt **Kultur A → Kultur B** prüfen.
5. **Nur bei unterschiedlicher Stufe** `set_cultural_view` im Scope von Kultur A mit Kultur B als Ziel ausführen; andernfalls **kein Set-Effekt**.
6. Die umgekehrte Richtung bei Bedarf unabhängig behandeln.

`set_cultural_view` und der Trigger `cultural_view` sind in den referenzierten Engine-Dumps nachgewiesen. **Funktionsfähige konkrete Syntax, Scope-Lebensdauer und korrekte Reaktion der Engine auf einen Set-Aufruf** sind im Zielbuild noch zu testen.

**Wichtig:** Dieses Schema kann mehrere Vanilla-Stufen mit einem einzigen `set_cultural_view`-Aufruf überspringen. Bei jährlichem Update ist das ausdrücklich möglich.

## Konfigurierbares Berechnungsintervall

- **Monatlich:** genauerer Verlauf; potenziell mehr Rechenaufwand und häufigere Grenzübertritte.
- **Jährlich:** weniger Rechenläufe; größere Veränderungen pro Update und potenziell verzögerte sichtbare Reaktionen.
- Die **zeitliche Änderungsrate** muss auf das verstrichene Intervall normiert werden. Ein jährlicher Lauf darf nicht nur eine Monatsänderung anwenden. Konkrete Aggregation von Ereignissen (z. B. Kriegsverluste) ist noch offen.
- **Jährlicher Bevölkerungs-/Religions-Cache** ist von der wählbaren Frequenz der eigentlichen Meinungskalkulation zu unterscheiden.
- Ob die Frequenz durch eine **Game Rule**, Mod-Einstellung oder andere benutzerseitige Konfiguration ohne Reload gewählt werden kann, ist zu validieren. Vorläufig ist nur das Ziel „monatlich oder jährlich auswählbar“ beschlossen.
- Ein berechneter Zustand darf nicht durch mehrere Länder-Puls-Ereignisse **mehrfach pro Periode** aktualisiert werden. Einen eindeutigen Update-Verantwortlichen bzw. ein Periodenkennzeichen prüfen.

## Startwert, Ruhen und externe Änderungen

- Der initiale numerische Wert muss aus den **vorhandenen Vanilla-Kulturmeinungen** abgeleitet werden, damit ein neues Spiel nicht sämtliche historischen Feindschaften verliert. **Vorschlag (noch offen):** Stufenmitten −80/−40/0/+40/+80.
- Ruhende Kulturpaare behalten ihren Zahlenwert. Bei erneutem Kontakt kann Synchronisierung und Berechnung wieder anlaufen.
- Andere Vanilla-Ereignisse können die native Stufe zwischen zwei Mod-Updates ändern. Zu entscheiden ist, ob diese Ereignisse **abgeschaltet, in den numerischen Wert überführt oder beim nächsten Update überschrieben** werden. Ohne Entscheidung entstehen widersprüchliche Meinungen.
- Save/Load, Spieldatenmigration und neu entstandene Kulturen benötigen eine klare Initialisierungsregel.

## P0-Prüfungen

1. Numerische Map **auf Kultur-Scope**, Kultur als Schlüssel, negative/dezimale Werte, Lesbarkeit, Save/Load.
2. Eigenständige gerichtete **A→B und B→A**-Werte.
3. `cultural_view` korrekt prüfen und `set_cultural_view` nur bei veränderter Zielstufe aufrufen.
4. Grenzfälle **−100, −60, −20, 20, 60, 100** und Überspringen mehrerer Stufen testen.
5. Monats-/Jahrestakt mit **zeitlich vergleichbarer Änderungsrate** prüfen, insbesondere Aktivierung während einer Periode, Save/Load und erneute Änderung.
6. Spielseitige UI-/Kulturkostenänderungen nach Stufenwechsel kontrollieren. **Kein direkter Eingriff in Kulturakzeptanz oder -kapazitätsformel.**
7. Bestand der alten Änderungsaktionen/Events und **Synchronisierungs-Konflikte** untersuchen.

## Fundstellen

- [Engine-Dump: `set_cultural_view`, `change_cultural_view`, Variable-Maps](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/effects.log)
- [Engine-Dump: `cultural_view` und kulturbezogene Trigger](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_official_defines/docs/triggers.log)
- [Vanilla: Kulturmeinungen als Stufen](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/cultures/iberian.txt)
- [Vanilla: Meinung durch Aktion ändern](https://github.com/HLJSXK/eu5-modding-base/blob/9e941cedccf963921733810a041ba912b6b8ceb7/reference_game_files/game/in_game/common/generic_actions/improve_our_cultural_view.txt)
