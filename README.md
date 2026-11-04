# Fire-hub-

Digitale Startoberfläche für Feuerwehr, Erkunder-Arbeitsplatz und CBRN-/ABC-Einsatz.

## Starten

- [Feuerwehr HUB](index.html) - zentrale Startseite für Alarm, Verwaltung, Kontakte, Einsatz-Tools und Warnungen
- [Erkunder-Kiosk](Erkunder-Kiosk.html) - Offline-Startseite für den Erkunder-Arbeitsplatz
- [Materialkontrolle Probenahme](Erkunder-Materialkontrolle.html) - prüft Positionen aus einer lokal ausgewählten Markdown-Checkliste per Ein-Klick-Bestätigung
- [GitHub Pages](https://harti72.github.io/Fire-hub-/) - öffentlich erreichbare Startseite

Der Erkunder-Kiosk bietet oben einen Online-/Offline-Schalter. Der Online-Modus zeigt alle Funktionen. Im Offline-Modus werden nur externe Web- und Mail-Links ausgeblendet; lokale Dateien, Ordner, Skripte und die Druckfunktion bleiben verfügbar.

Die Materialkontrolle läuft ohne Server und externe Bibliotheken. Die MD-Datei wird lokal im Browser eingelesen; passende Positionen werden mit einem Klick bestätigt und die App springt automatisch weiter. Abweichungen lassen sich mit Ist-Menge und Bemerkung erfassen. Prüfergebnisse werden lokal gespeichert und können als Markdown oder CSV exportiert werden.

## Copilot-Skill

- [Erkunder-Checklisten](.github/skills/erkunder-checklisten/SKILL.md) - erstellt aus einer bereitgestellten Liste eine neue, app-kompatible MD-Checkliste und prüft Positionen und Tabellenformat.

## Konzept und Planung

- [Erkunder-Arbeitsplatz-Konzept](Erkunder_Arbeitsplatz_Konzept.md) - technische und organisatorische Grundlage für den Linux-Arbeitsplatz
- [Arbeitsplan Feuerwehr und Katastrophenschutz](arbeitsplan-feuerwehr-katastrophenschutz-mcp.md) - MCP-, Quellen-, Sicherheits- und MVP-Planung

## Hinweis

Die lokalen `file:///home/erkunder/...`-Pfade im Kiosk sind Platzhalter für die vorgesehene Linux-Struktur. Vor einem praktischen Einsatz müssen sie an die tatsächlichen Ablageorte angepasst und fachlich freigegeben werden.