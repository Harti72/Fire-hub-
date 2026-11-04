---
name: erkunder-checklisten
description: "Use when turning a provided PDF, Word, spreadsheet, image, or existing list into a new Markdown (MD) checklist or inspection plan compatible with the Erkunder Materialkontrolle app. Triggers: Checkliste, Prüfliste, Prüfplan, Materialliste, PDF nach MD, app-kompatibles Markdown."
argument-hint: "Quellliste oder PDF und gewünschten Namen für die neue MD-Datei"
---

# Erkunder-Checklisten erstellen

Erstelle aus einer vom Nutzer bereitgestellten Liste eine **neue, app-kompatible MD-Datei**. Bewahre die Quelle unverändert und gib nur eine knappe Fertigmeldung statt des vollständigen Listeninhalts aus.

## Ablauf

1. **Quelle verwenden:** Nimm den Anhang oder die Datei, auf die der Nutzer im aktuellen Auftrag verweist. Bei mehreren möglichen Quellen kurz klären, welche gemeint ist. Nicht eigenständig private Ordner durchsuchen. Wenn die Quelle nicht verfügbar oder unlesbar ist, dies ehrlich sagen.
2. **Inhalt treu übernehmen:** Reihenfolge, Kapitel, Materialnamen und Soll-Mengen beibehalten. Lager-/Artikelnummern in Material oder Bemerkung erhalten. Werte wie `div.`, `n.n.` und Einheiten nicht erraten oder stillschweigend ändern. Unleserliches als `Prüfen` markieren und in Bemerkung kennzeichnen.
3. **Neue Datei anlegen:** Quelle nicht überschreiben. Die MD-Datei neben der Quellliste speichern, mit einem kurzen, aussagekräftigen Dateinamen. Private Einsatz- oder Fahrzeuglisten nicht ohne ausdrücklichen Auftrag in ein öffentliches Repository committen.
4. **App-Format verwenden:** Kapitel als `##`, optionale Unterkapitel als `###`. Für jede Gruppe die folgende Tabelle und Trennzeile verwenden. Jede Materialzeile hat genau sieben Zellen:

   `|Vorh.|Fehl.|Pos.|Material|Soll|Ist|Bemerkung|`

   `|:-:|:-:|---|---|--:|--:|---|`

   Beispielzeile: `|□|□|1.1.1|Materialname|2|||`
5. **Positionen vergeben:** Jede Position braucht eine eindeutige, nur numerische Punktnummer wie `1.1.1`, `1.1.2`, `2.1.1`. Nummern müssen in der ganzen Datei eindeutig sein. Originalnummern, die diesem Muster nicht entsprechen, zusätzlich im Materialtext oder in Bemerkung bewahren. IDs nach Kapiteln gruppieren.
6. **Prüfen, dann fertigmelden:** Kontrolliere, dass jede Datenzeile sieben Zellen hat, alle Positionsnummern eindeutig und im App-Muster sind, keine Quellposition fehlt und Mengen unverändert sind. Ergebnis direkt als Datei schreiben; in der Antwort nur Dateilink und kurze Bestätigung nennen.

## Hinweise zum App-Verhalten

- Die App liest die Spalten anhand ihrer Reihenfolge: Position = 3. Zelle, Material = 4., Soll = 5., Ist = 6., Bemerkung = 7.
- Kapitelwahl und Fortschritt basieren auf `##`-Überschriften. Daher für jeden sinnvoll direkt anwählbaren Hauptbereich ein eigenes `##` verwenden; Detailbereiche darunter mit `###` gruppieren.
- Keine senkrechten Striche `|` in Feldinhalten verwenden: der App-Parser behandelt sie als Spaltentrenner.
- Verwende [die MD-Vorlage](./assets/checklist-template.md), wenn ein Startgerüst benötigt wird.
