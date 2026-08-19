# Arbeitsplan: MCP-gestützter Informationsassistent für Feuerwehr und Katastrophenschutz

**Status:** Entwurf  
**Version:** 0.1  
**Datum:** 2026-08-19  
**Ziel-Repository:** `Harti72/Fire-hub-`

## 1. Executive Summary

Dieses Vorhaben untersucht und prototypisiert einen MCP-gestützten Informationsassistenten für Feuerwehr und Katastrophenschutz. Der Assistent soll öffentlich zugängliche, fachlich relevante Datenquellen gebündelt abfragen und Antworten mit Quelle, Zeitstempel und Datenqualität liefern.

Der erste Anwendungsfall ist bewusst **read-only**: Recherche, Lagevorbereitung und Zusammenfassung. Der Assistent trifft keine Einsatzentscheidungen, alarmiert keine Kräfte und steuert keine kritische Infrastruktur.

**Leitprinzip:** MCP ist nur die technische Schnittstelle. Die fachliche Verlässlichkeit, Aktualität und rechtliche Nutzbarkeit kommen aus den angebundenen Datenquellen.

## 2. Ziele und Nicht-Ziele

### Ziele

- Öffentliche Datenquellen für Warnungen, Wetter, Geodaten, Hochwasser und Feuerereignisse identifizieren.
- Prüfen, ob bestehende MCP-Server fachlich und technisch nutzbar sind.
- Einen kleinen eigenen MCP-Server als kontrollierte Integrationsschicht prototypisieren.
- Antworten mit Quellenangabe, Abrufzeit, Datenalter und Unsicherheiten erzeugen.
- Sicherheits-, Datenschutz-, Lizenz- und Betriebsrisiken vor einer weiteren Nutzung bewerten.
- Einen reproduzierbaren Prototyp für das private GitHub-Repository dokumentieren.

### Nicht-Ziele der ersten Phase

- Keine automatische Alarmierung oder Disposition.
- Keine automatische Evakuierungs- oder Einsatzpriorisierungsentscheidung.
- Keine Verarbeitung nichtöffentlicher Einsatz-, Gesundheits- oder Personaldaten.
- Keine Übernahme eines öffentlichen MCP-Servers ohne Code-, Lizenz- und Sicherheitsprüfung.
- Keine Zusage von Verfügbarkeit, Vollständigkeit oder amtlicher Verbindlichkeit ohne belastbaren Nachweis.

## 3. Priorisierte Anwendungsfälle

| Priorität | Anwendungsfall | Ergebnis des Assistenten |
|---|---|---|
| P1 | Wetter- und Gefahrenwarnungen für ein Gebiet | Aktuelle Warnungen mit Quelle, Gültigkeit und Abrufzeit |
| P1 | Amtliche Warnmeldungen | Gefilterte Meldungen nach Ort und Kategorie |
| P1 | Feuerwehr- und Rettungsinfrastruktur | Geolokalisierte Treffer, zum Beispiel Feuerwachen und Rettungswachen |
| P2 | Hochwasser- und Pegelinformationen | Pegelstatus, Trend und Datenalter, sofern verfügbar |
| P2 | Feuer- und Wärmeereignisse | Hinweise aus Satelliten- oder Geodaten mit deutlicher Unsicherheitskennzeichnung |
| P2 | Lagevorbereitung | Quellenbasierte Zusammenfassung für ein definiertes Gebiet |
| P3 | Historische Analyse | Vergleich von Wetter, Warnungen und Ereignissen über einen Zeitraum |

## 4. Zielquellen

Die Quellen werden vor einer technischen Integration anhand eines Quellensteckbriefs bewertet.

| Quelle | Fachlicher Zweck | Erste Bewertung |
|---|---|---|
| [warnung.bund.de](https://warnung.bund.de/) | Amtliche Warnungen aus Bevölkerungsschutz, Wetter und Hochwasser | Hohe fachliche Relevanz; technische Schnittstelle und Nutzungsbedingungen prüfen |
| [Deutscher Wetterdienst Open Data](https://www.dwd.de/DE/leistungen/opendata/opendata.html) | Wetterdaten, Vorhersagen und Warnungen | Hohe Relevanz; Aktualität, Lizenz und API-/Dateiformate prüfen |
| [GovData](https://www.govdata.de/) | Deutsche Verwaltungsdaten | Such- und Katalogquelle; Datensatzqualität je Anbieter bewerten |
| [OpenStreetMap](https://www.openstreetmap.org/) / [Overpass API](https://overpass-api.de/) | Feuerwehrhäuser, Rettungswachen, Krankenhäuser, Gewässer und Infrastruktur | Geeignet für Geodaten; Lizenz, Abdeckung und Aktualität berücksichtigen |
| [Copernicus Emergency Management Service](https://www.copernicus.eu/en/access-data/copernicus-services-catalogue/emergency-management-service) | Satelliten- und Geoinformationen bei Schadenslagen | Geeignet für Lagebilder; Verarbeitung und Aktualisierungszeiten prüfen |
| [GDACS](https://www.gdacs.org/) | Internationale Katastropheninformationen | Ergänzende internationale Quelle; regionale Eignung prüfen |
| [NASA FIRMS](https://firms.modaps.eosdis.nasa.gov/) | Satellitengestützte Feuer- und Wärmeindikatoren | Nur als Hinweisquelle verwenden; keine direkte Einsatzfeststellung |

**Regel:** Eine Quelle wird erst als produktiv nutzbar markiert, wenn Herkunft, Lizenz, Aktualisierung, Ausfallverhalten und fachliche Grenzen dokumentiert sind.

## 5. OpenStreetMap-Konzept für den MVP

OpenStreetMap (OSM) dient im ersten MVP als öffentliche, community-basierte Geodatenquelle. GitHub stellt den Quellcode und optional ein statisches Web-Hosting bereit, ist aber kein Backend für Live-Geodaten oder Einsatzinformationen.

### Technische Rollen

```text
Mobile/Web-App
    |
    +--> Kartenkacheln eines geeigneten OSM-Tile-Anbieters
    +--> Overpass API fuer POI- und Infrastrukturabfragen
    +--> Nominatim fuer eingeschraenktes Geocoding
    |
    +--> optionaler read-only MCP-Server fuer Agentenabfragen
```

**Vorgesehene Rollen der Komponenten**

| Komponente | Rolle | Grenze |
|---|---|---|
| GitHub | Quellcode, Issues, Dokumentation und optional statisches Hosting | Kein Live-Datenspeicher und kein Einsatz-Backend |
| Kartenkacheln | Darstellung der Karte | Nutzungsbedingungen und Attribution des Anbieters beachten |
| Overpass API | Abfrage von OSM-Objekten nach Gebiet und Tags | Räumlich begrenzte Abfragen, Rate Limits und Caching erforderlich |
| Nominatim | Adress- und Ortsauflösung | Nicht für Hochlast- oder Massengeocoding verwenden |
| MCP-Server | Kontrollierte fachliche Suchfunktionen für Agenten | Read-only, keine beliebigen Netzwerk- oder Dateizugriffe |

### Priorisierte Kartenlayer

Der erste MVP soll nur wenige, klar verständliche Layer enthalten:

| Layer | Beispiele für OSM-Tags | Fachliche Aussage |
|---|---|---|
| Feuerwehr und Rettungsdienst | `emergency=fire_station`, `emergency=ambulance_station`, `amenity=hospital` | Kartierter Standort, nicht automatisch aktuelle Einsatzbereitschaft |
| Hydranten und Löschwasser | `emergency=fire_hydrant`, `fire_hydrant:type`, `fire_hydrant:position` | Kartierte Löschwasser-Infrastruktur mit möglicher Lückenhaftigkeit |
| Sammelpunkte und Unterkünfte | `emergency=assembly_point`, `amenity=shelter` | Kartiertes Objekt; Eignung und aktuelle Verfügbarkeit separat prüfen |
| Katastrophenschutz-Infrastruktur | `emergency=*` und regional geprüfte Ergänzungen | Nur nach definierter Tag-Mapping-Regel anzeigen |
| ABC-relevante Umgebungshinweise | Industrie, Tankstellen und weitere geeignete POI-Tags | Kein Nachweis einer aktuellen Gefahrstofflage |

ABC-relevante Daten werden zunächst ausdrücklich als **Umgebungshinweise** bezeichnet. OSM liefert keine verlässlich vollständige Klassifikation aktueller atomarer, biologischer oder chemischer Gefahren, Gefahrstoffmengen oder Betriebszustände.

### Fachliche MCP-Werkzeuge

Der MCP-Server übersetzt fachliche Kategorien in kontrollierte OSM-/Overpass-Abfragen:

```text
find_fire_stations(area, radius)
find_fire_hydrants(area, radius)
find_emergency_services(area, categories)
find_shelters_and_assembly_points(area)
find_abc_relevant_pois(area)
search_map_features(area, category)
```

Jede Antwort muss mindestens enthalten:

```json
{
  "source": "OpenStreetMap via Overpass API",
  "retrieved_at": "2026-08-19T12:00:00Z",
  "area": "definierte Region",
  "category": "fire_station",
  "items": [],
  "limitations": [
    "Community-maintained data",
    "No guarantee of completeness or current operational status"
  ]
}
```

### Datenqualität und Nutzung

- OSM-Daten sind community-basiert und nicht automatisch amtlich, vollständig oder aktuell.
- Ein fehlender POI darf nicht als Beweis gewertet werden, dass das Objekt nicht existiert.
- Ein vorhandener POI darf nicht als Nachweis für Einsatzbereitschaft, Zustand oder Verfügbarkeit interpretiert werden.
- Jede Darstellung benötigt OSM-Attribution und den Abrufzeitpunkt.
- Overpass-Abfragen werden auf ein Gebiet und eine Ergebnisgröße begrenzt.
- Wiederholte Abfragen werden, soweit zulässig, gecacht.
- Tile- und API-Nutzungsbedingungen werden je Anbieter dokumentiert.
- OSM-Daten werden nicht allein als Grundlage für Alarmierung, Disposition oder Evakuierungsentscheidungen verwendet.

### Abnahme des OSM-Konzepts

- Die drei MVP-Layer Feuerwehr/Rettungsdienst, Hydranten/Löschwasser und Sammelpunkte/Unterkünfte sind in einer Testregion sichtbar.
- Jede Kategorie besitzt ein dokumentiertes Tag-Mapping und eine bekannte fachliche Grenze.
- Eine begrenzte Overpass-Abfrage funktioniert für eine Testregion und behandelt leere Ergebnisse sowie Timeouts.
- Die Anwendung zeigt Quelle, Datenstand und OSM-Attribution an.
- Der MCP-Server kann ausschließlich lesen, filtern und zusammenfassen.

## 6. Arbeitspakete

### AP0 - Projektabgrenzung und Governance

**Aufgaben**

- Zielgruppe und Einsatzkontext festlegen.
- Read-only-MVP verbindlich bestätigen.
- Verantwortliche Person für Datenquellen, Code und Sicherheitsprüfung benennen.
- Klären, ob der Prototyp ausschließlich privat oder später in einem Organisationskontext genutzt wird.
- Fachliche Begriffe und Gebietsdefinitionen festlegen.

**Ergebnis**

- Abgenommener Scope.
- Liste der Nicht-Ziele.
- Verantwortlichkeitsmatrix.

**Abnahme**

- Für jeden Anwendungsfall ist festgelegt, ob er Recherche, Lagevorbereitung oder operative Nutzung unterstützt.
- Operative Aktionen sind für den MVP ausgeschlossen.

### AP1 - Quelleninventar und technische Machbarkeit

**Aufgaben**

- Für jede Zielquelle einen Quellensteckbrief anlegen.
- Offizielle API-, Feed- oder Download-Schnittstelle identifizieren.
- Für OSM die Kartenkachelquelle, Overpass-Instanz und gegebenenfalls Nominatim getrennt bewerten.
- Das Tag-Mapping für jeden MVP-Layer in einer versionierten Datei dokumentieren.
- Authentifizierung, Rate Limits, Nutzungsbedingungen und Lizenz dokumentieren.
- Beispielabfragen mit realistischen Gebieten und Zeitfenstern durchführen.
- Fehlende oder nicht verlässlich zugängliche Quellen als offene Punkte markieren.

**Quellensteckbrief**

```text
Name:
Betreiber:
Fachlicher Zweck:
Offizielle URL:
Technische Schnittstelle:
Datenformat:
Aktualisierung:
Räumliche Abdeckung:
Zeitliche Abdeckung:
Lizenz/Nutzungsbedingungen:
Authentifizierung:
Rate Limits:
Bekannte Ausfälle oder Lücken:
Fachliche Grenzen:
Geprüft am:
```

**Ergebnis**

- Bewertete Quellenmatrix.
- Kleine Sammlung reproduzierbarer Beispielabfragen.

**Abnahme**

- Mindestens drei Quellen liefern erfolgreich testbare Ergebnisse.
- Jede verwendete Quelle hat eine dokumentierte fachliche Grenze.

### AP2 - Markt- und MCP-Server-Prüfung

**Aufgaben**

- Die offizielle [MCP Registry](https://registry.modelcontextprotocol.io/), [Glama](https://glama.ai/mcp/servers), [Smithery](https://smithery.ai/) und GitHub durchsuchen.
- Server nach Wetter, Warnungen, Geodaten, Katastrophenschutz und Feuerereignissen suchen.
- Für jeden Kandidaten Repository, Maintainer, letzte Aktivität, Lizenz und Abhängigkeiten prüfen.
- Benötigte Berechtigungen und externe Netzwerkziele erfassen.
- Öffentliche MCP-Server nur als Kandidaten, nicht als Vertrauensnachweis behandeln.

**Ergebnis**

- MCP-Kandidatenliste mit Entscheidung: `verwenden`, `testen`, `nicht verwenden`.
- Begründete Entscheidung für Eigenentwicklung oder Wiederverwendung.

**Abnahme**

- Kein Kandidat erhält Zugriff auf private Daten oder Secrets im Rahmen des MVP.
- Die Entscheidung für jeden verworfenen Kandidaten ist nachvollziehbar dokumentiert.

### AP3 - Architektur und Sicherheitsdesign

**Zielbild**

```text
MCP-Client (VS Code/Copilot)
            |
            v
Kontrollierter MCP-Server (read-only)
            |
            +--> DWD / Warnungen / Hochwasser
            +--> OpenStreetMap / Overpass
            +--> Copernicus / FIRMS / weitere geprüfte Quellen
            |
            v
Normalisierte Ergebnisse mit Quelle, Zeitstempel und Datenqualität
```

**Aufgaben**

- Werkzeuge und Eingabeparameter definieren.
- Nur erlaubte Domains und Protokolle für externe Abrufe zulassen.
- Timeouts, Rate Limits, Größenlimits und Fehlerbehandlung festlegen.
- Externe Inhalte als untrusted input behandeln.
- Keine Shell-Ausführung, keine beliebigen Dateioperationen und keine Schreibaktionen im MVP zulassen.
- Secrets ausschließlich über lokale Secret-Verwaltung oder Umgebungsvariablen beziehen.
- Logging ohne personenbezogene oder vertrauliche Inhalte entwerfen.
- Datenfluss und Vertrauensgrenzen dokumentieren.

**Vorgeschlagene read-only-Werkzeuge**

```text
get_weather_warnings(area, time_window)
get_official_alerts(location, category)
find_emergency_infrastructure(area, type)
get_flood_information(area)
get_fire_hotspots(area, time_window)
create_situation_summary(area, time_window)
```

**Ergebnis**

- Architekturübersicht.
- Bedrohungsmodell.
- Freigabeliste der externen Ziele.
- MCP-Werkzeugvertrag mit Eingaben, Ausgaben und Fehlerfällen.

**Abnahme**

- Jedes Werkzeug ist read-only und hat eine begrenzte Berechtigungsreichweite.
- Ungültige, fehlende und übergroße Eingaben werden abgewiesen.
- Quellen- und Zeitstempel sind Bestandteil jeder fachlichen Antwort.

### AP4 - Technischer MVP

**Aufgaben**

- Eigenen MCP-Server oder eine kontrollierte Adapter-Schicht implementieren.
- Zuerst nur eine Quelle und einen Anwendungsfall umsetzen, vorzugsweise Wetter- oder amtliche Warnungen.
- Antworten in ein einheitliches Schema normalisieren.
- Tests mit gültigen, leeren, veralteten und fehlerhaften Antworten erstellen.
- Lokalen Betrieb in einer isolierten Umgebung dokumentieren.
- Konfiguration von VS Code ausschließlich für das private Profil beschreiben.

**Minimaler Antwortvertrag**

```json
{
  "source": "offizielle Quelle",
  "retrieved_at": "2026-08-19T12:00:00Z",
  "valid_until": "2026-08-19T18:00:00Z",
  "area": "definierte Region",
  "items": [],
  "limitations": []
}
```

**Abnahme**

- Der MCP-Server startet reproduzierbar.
- Ein End-to-End-Test liefert eine Antwort mit Quelle und Zeitstempel.
- Ein Ausfall der Datenquelle führt zu einer verständlichen Fehlermeldung und nicht zu erfundenen Daten.

### AP5 - Fachliche Validierung

**Aufgaben**

- Ergebnisse mit der Originalquelle vergleichen.
- Mindestens zwei Personen mit fachlichem oder einsatznahem Hintergrund einbeziehen.
- Falschinterpretationen und missverständliche Formulierungen sammeln.
- Klare Kennzeichnung von Hinweis-, Prognose- und amtlichen Daten einführen.
- Prüfen, ob Gebiets- und Zeitangaben im deutschen Einsatzkontext verständlich sind.

**Abnahme**

- Fachliche Stichproben sind dokumentiert.
- Bekannte Fehlinterpretationen sind in Tests oder Einschränkungen abgebildet.
- Der Assistent behauptet keine Einsatzfeststellung, wenn die Quelle nur einen Hinweis liefert.

### AP6 - Datenschutz, Recht und Betrieb

**Aufgaben**

- Prüfen, ob personenbezogene Daten verarbeitet werden.
- Lizenz- und Quellenpflichten für alle Datenquellen dokumentieren.
- Aufbewahrung, Löschung und Zugriff auf Logs festlegen.
- Verantwortlichkeit für Wartung und Störungsbehandlung bestimmen.
- Abhängigkeiten, Versionen und bekannte Schwachstellen regelmäßig prüfen.
- Entscheidung dokumentieren, ob der Prototyp öffentlich, privat oder nur lokal betrieben wird.

**Abnahme**

- Keine Secrets oder privaten Einsatzdaten sind im Repository enthalten.
- Jede externe Quelle hat eine dokumentierte Lizenz- und Nutzungseinschätzung.
- Es gibt eine benannte Person oder Rolle für Betrieb und Aktualisierung.

### AP7 - Dokumentation und private GitHub-Übertragung

**Aufgaben**

- README mit Zweck, Setup, Sicherheitsgrenzen und Quellen erstellen.
- Beispielkonfiguration ohne Secrets dokumentieren.
- `.gitignore` für lokale Konfiguration, Logs, virtuelle Umgebungen und Secrets ergänzen.
- Repository auf versehentlich enthaltene Tokens, BMW-interne Inhalte und private Daten prüfen.
- Änderungen committen und in das private Repository pushen.

**Empfohlener Ablauf**

```powershell
# Im Arbeitsverzeichnis des privaten Projekts
 git init
 git remote add origin git@github-private:Harti72/Fire-hub-.git
 git status
 git add .
 git commit -m "Add MCP emergency response work plan"
 git branch -M main
 git push -u origin main
```

Falls das Repository bereits ein Git-Repository ist, `git init` und `git remote add` nicht erneut ausführen. Vor dem Push prüfen:

```powershell
git remote -v
git diff --cached --stat
git grep -n -I -E "token|secret|password|api[_-]?key|bearer" -- .
```

**Abnahme**

- Das private Repository enthält den Arbeitsplan und eine nachvollziehbare Projektstruktur.
- Es wurden keine Zugangsdaten oder BMW-internen Inhalte übertragen.
- Der erste Commit ist auf das private Remote begrenzt.

## 7. Zeitplan für den MVP

| Zeitraum | Schwerpunkt | Ergebnis |
|---|---|---|
| Woche 1 | AP0 und AP1 | Scope und Quellensteckbriefe |
| Woche 2 | AP2 und AP3 | MCP-Entscheidung, Architektur und Sicherheitsgates |
| Woche 3 | AP4 | Erster read-only-MCP-Server mit einem Anwendungsfall |
| Woche 4 | AP5 und AP6 | Fachliche Prüfung sowie Datenschutz-/Betriebsbewertung |
| Woche 5 | AP7 | Dokumentation, Bereinigung und privater GitHub-Push |

Der Zeitplan ist eine Arbeitsannahme. Die Verfügbarkeit und Nutzbarkeit der externen Schnittstellen kann den Ablauf verändern.

## 8. Entscheidungsgates

### Gate 1 - Quellenfreigabe

Weiterarbeit nur, wenn mindestens eine Quelle technisch erreichbar, fachlich geeignet und rechtlich grundsätzlich nutzbar ist.

### Gate 2 - Sicherheitsfreigabe

Weiterarbeit nur, wenn der Server read-only bleibt, externe Ziele begrenzt sind und keine privaten Daten oder Secrets benötigt werden.

### Gate 3 - Fachliche Freigabe

Weiterarbeit nur, wenn Antworten gegen die Originalquellen geprüft und Einschränkungen sichtbar gemacht wurden.

### Gate 4 - Repository-Freigabe

Push nur, wenn ein Secret- und Inhaltscheck ohne Befund abgeschlossen wurde.

## 9. Risiken und Gegenmaßnahmen

| Risiko | Auswirkung | Gegenmaßnahme |
|---|---|---|
| Datenquelle ist veraltet oder nicht erreichbar | Falsche Lageeinschätzung | Zeitstempel, Datenalter, Timeout und klare Fehlermeldung |
| Community-MCP-Server enthält schädlichen Code | Datenabfluss oder lokale Kompromittierung | Eigenentwicklung bevorzugen, Quellcode prüfen, Rechte minimieren |
| Geodaten sind unvollständig | Fehlende oder falsche Infrastruktur | Quelle und Abdeckung sichtbar machen, keine Vollständigkeit behaupten |
| Öffentliche OSM-Dienste werden überlastet oder ändern ihre Nutzungsvorgaben | App-Ausfälle oder Sperrung des Zugangs | Gebietslimits, Caching, Rate Limits und später eigener oder vertraglicher Geodatenbetrieb |
| Warnung wird als Einsatzlage interpretiert | Fehlentscheidung | Hinweis-/Prognose-/amtliche Daten klar unterscheiden |
| Lizenz oder Nutzungsbedingungen unklar | Rechtliches Risiko | Quellensteckbrief und Freigabegate |
| Private oder interne Daten gelangen ins Repository | Datenschutz- oder Vertraulichkeitsverletzung | `.gitignore`, Secret-Scan und Vier-Augen-Prüfung |
| MCP-Server erhält zu weitreichende Rechte | Erhöhtes Sicherheitsrisiko | Read-only, Allowlist, isolierte Laufzeit und minimale Berechtigungen |

## 10. Offene Entscheidungen

- Welche Region und welche konkreten Nutzergruppen stehen im Mittelpunkt?
- Soll der MVP nur Deutschland oder auch internationale Gebiete abdecken?
- Welche Quelle ist für Warnungen fachlich verbindlich?
- Welcher OSM-Tile-Anbieter und welche Overpass-Instanz sind für den Prototyp zulässig?
- Soll die App OSM-Daten direkt abfragen oder einen eigenen Proxy mit Cache verwenden?
- Welche OSM-Tags werden für ABC-relevante Umgebungshinweise akzeptiert?
- Wird der Server lokal, in einer kontrollierten Cloud-Umgebung oder nur als Demonstrator betrieben?
- Welche Fachperson nimmt die Ergebnisse ab?
- Welche Lizenz soll für den eigenen Code verwendet werden?
- Welche Betriebs- und Verfügbarkeitsanforderungen gelten außerhalb des MVP?

## 11. Erfolgskriterien

Der MVP gilt als erfolgreich, wenn:

- mindestens ein priorisierter Anwendungsfall end-to-end funktioniert;
- mindestens drei öffentliche Quellen bewertet und mindestens eine integriert wurde;
- die drei priorisierten OSM-Layer in einer Testregion abgefragt und dargestellt werden können;
- jede Antwort Quelle, Abrufzeit, Gültigkeit und Einschränkungen enthält;
- der MCP-Server keine operativen Schreib- oder Steuerungsaktionen ausführen kann;
- Tests für Normalfall, leere Daten, veraltete Daten und Quellenausfall vorhanden sind;
- Sicherheits-, Datenschutz- und Lizenzrisiken dokumentiert sind;
- der Arbeitsstand ohne Secrets in das private GitHub-Repository übertragen werden kann.

## 12. Quellen und Referenzen

- [Model Context Protocol](https://modelcontextprotocol.io/)
- [MCP Registry](https://registry.modelcontextprotocol.io/)
- [Glama MCP Server Directory](https://glama.ai/mcp/servers)
- [Smithery](https://smithery.ai/)
- [Warnung.bund.de](https://warnung.bund.de/)
- [DWD Open Data](https://www.dwd.de/DE/leistungen/opendata/opendata.html)
- [GovData](https://www.govdata.de/)
- [OpenStreetMap](https://www.openstreetmap.org/)
- [Overpass API](https://overpass-api.de/)
- [Copernicus Emergency Management Service](https://www.copernicus.eu/en/access-data/copernicus-services-catalogue/emergency-management-service)
- [GDACS](https://www.gdacs.org/)
- [NASA FIRMS](https://firms.modaps.eosdis.nasa.gov/)

**Hinweis:** Dieser Arbeitsplan ist eine Planungsgrundlage und keine Einsatz-, Sicherheits- oder Rechtsfreigabe.
