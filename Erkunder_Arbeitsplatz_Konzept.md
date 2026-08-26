# Einsatzkonzept: Erkunder-Arbeitsplatz (Linux)

Dieses Dokument beschreibt die technische Umsetzung und das Konfigurationskonzept für den IT-Arbeitsplatz im CBRN-Erkundungsfahrzeug (Feuerwehr). Es dient als Vorlage für die Systemeinrichtung, um Ausfallsicherheit im Einsatz, Unabhängigkeit von privaten Mobiltelefonen und schnelle Offline-Verfügbarkeit zu garantieren.

---

## ⚙️ Systemsicherheit & Benutzerkonzept (Ersatz für 2. Faktor)

Da private Smartphones im Einsatz- und Übungsdienst verboten sind, entfällt die klassische Zwei-Faktor-Authentifizierung (2FA). Die Absicherung erfolgt stattdessen über physische Barrieren und lokale Schutzmechanismen:

* **Der „Messrechner“-Ansatz:** Automatischer Login in einen lokalen Standard-Nutzer (z. B. `erkunder`). Dieser besitzt keine Administrator-Rechte (`root`), um Fehlbedienungen im Stress zu verhindern.
* **Hardware-Sperre (Yubikey / Nitrokey):** Statt eines Smartphones wird ein robuster USB-Sicherheits-Token am Fahrzeugschlüssel oder im Erkunder-Koffer befestigt. Der Login am PC oder bei Clouddiensten klappt nur, wenn dieser USB-Token steckt (Ein-Faktor-Besitz).
* **Zentrale Datenverschlüsselung (LUKS):** Die gesamte Festplatte wird verschlüsselt. Wird das Gerät aus dem Fahrzeug entwendet oder geht im Einsatz verloren, sind alle internen Daten (SharePoint-Daten, Dokumente) absolut sicher vor unbefugtem Zugriff geschützt.

---

## 📂 Datenmanagement & Hardware-Integration

### 1. Offline-Daten & SharePoint (OneDrive)
* **Technik:** Nutzung des quelloffenen Linux-Tools `Rclone`.
* **Funktion:** Synchronisiert das SharePoint/OneDrive der Feuerwehr im Gerätehaus automatisch im Hintergrund, sobald das Fahrzeug Netzempfang hat (z. B. über den LTE-Router im Fahrzeug). Im Einsatz stehen alle Ordner komplett offline als lokale Kopie bereit:
  * Übersicht Prüfröhrchen Dräger
  * Einsatzkonzepte
  * Bedienungsanleitungen Messgeräte
  * Gefahrstoff-Beständigkeitslisten

### 2. USB-Stick einlesen & löschen
* **Technik:** Lokales Bash-Skript mit einer grafischen Verknüpfung auf dem Desktop.
* **Funktion:** Ein Klick auf das Icon *"USB-Stick sicher löschen (Überschreiben)"* formatiert das Medium ohne Kommandozeilen-Kenntnisse automatisch passend für den Erkunder neu.

### 3. Kamera-Import
* **Funktion:** Ein lokaler Ordner wird so konfiguriert, dass er importierte Fotos von Digitalkameras oder Erkunder-Messsystemen via Dateimanager automatisch nach Datum sortiert ablegt.

---

## 🌐 Kommunikation & Online-Dienste

* **Webseiten-Direktzugriff:** Die wichtigsten Portale sind als Kacheln direkt auf der Offline-Startseite hinterlegt:
  * **BayernAtlas** (Geoportal Bayern)
  * **Einsatzleiter Wiki**
  * **OKU Koordinaten-Tool** (Staatliche Feuerwehrschule Bayern - oku.sfs-bayern.de)
  * **Divera**
* **E-Mail & Video-Calls:** Ein nativer Linux-Client wie **Thunderbird** verwaltet das feste Erkunder-Postfach für den schnellen Mail-Versand/-Empfang. Für Video-Calls (z. B. LfU-Lagebesprechung zur Reaktorsicherheit) wird ein Chromium-Browser genutzt, der Kamera und Headset direkt via USB-Plug-and-Play anspricht.
* **Wartungspläne:** Diese werden über webbasierte Tools gepflegt (z. B. im SharePoint-System oder einem lokalen Wiki), sodass sie sich remote synchronisieren, sobald das Fahrzeug online ist.

---

## 📺 Hardware, Verlastung & Monitor-Splitting

* **Mobiles Docking-Konzept:** Als Hardware eignet sich ein extrem robustes, militärisch zertifiziertes Notebook (z. B. **Panasonic Toughbook** oder **Dell Latitude Rugged**). Dieses sitzt in einer fahrzeuggebundenen Dockingstation (Stromversorgung, Antennenkopplung) und kann mit einem Handgriff für den mobilen Einsatz entnommen werden. Das Gerät lässt sich somit flexibel neben dem Hauptmessrechner bedienen.
* **Großer Monitor (HDMI Splitter vs. Beamer):** Ein **HDMI-Splitter (1-in-2-out)** mit robuster 12V/24V-Fahrzeugstromversorgung ist im Funkarbeitsplatz die stabilste Lösung, um das Bild des Laptops parallel auf einem großen Lagebild-Monitor im Fahrzeug anzuzeigen. Ein Mini-Beamer ist für den schnellen Außeneinsatz (z. B. im Zelt der Dekon-Stelle) eine gute Ergänzung.
* **Drucken in der Fahrzeughalle:** Der Linux-Druckdienst (CUPS) wird so eingerichtet, dass er den Netzwerkdrucker der Fahrzeughalle automatisch anspricht, sobald sich das Fahrzeug per WLAN am Gerätehaus anmeldet.

---

## 🏠 Die Offline-Startseite (index.html)

Als zentraler Einstiegspunkt dient eine lokal im Browser geladene HTML-Seite. Sie benötigt kein Internet und startet automatisch im Vollbild-Kioskmodus. Nachfolgend finden Sie den Quellcode, den Sie lokal als `index.html` speichern können.

```html
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Erkunder Workplace</title>
    <style>
        :root {
            --bg-color: #121212;
            --card-bg: #1e1e1e;
            --text-color: #e0e0e0;
            --accent-color: #e63946;
            --accent-hover: #ff4d5a;
            --border-color: #333;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 20px;
        }
        .header {
            text-align: center;
            border-bottom: 3px solid var(--accent-color);
            padding-bottom: 15px;
            margin-bottom: 30px;
        }
        .header h1 {
            margin: 0;
            color: #fff;
            letter-spacing: 1px;
        }
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
        }
        .card {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 20px;
        }
        .card h2 {
            margin-top: 0;
            color: var(--accent-color);
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 8px;
        }
        .link-list {
            list-style: none;
            padding: 0;
            margin: 0;
        }
        .link-list li {
            margin-bottom: 12px;
        }
        .link-list a {
            color: var(--text-color);
            text-decoration: none;
            display: flex;
            align-items: center;
            padding: 8px;
            border-radius: 4px;
            background-color: #252525;
            transition: background 0.2s;
        }
        .link-list a:hover {
            background-color: var(--accent-color);
            color: #fff;
        }
        .status-bar {
            margin-top: 4px;
            font-size: 0.85em;
            color: #888;
        }
    </style>
</head>
<body>

    <div class="header">
        <h1>EINSATZPLATZ ERKUNDER</h1>
        <div style="color: #aaa; margin-top: 5px;">Zentrale Startoberfläche | Feuerwehr</div>
    </div>

    <div class="grid">
        <!-- Offline-Daten -->
        <div class="card">
            <h2>📂 Offline-Daten (Lokal)</h2>
            <ul class="link-list">
                <li><a href="file:///home/erkunder/Feuerwehr/Dräger_Prüfröhrchen_Übersicht.pdf" target="_blank">📄 Übersicht Dräger Prüfröhrchen</a></li>
                <li><a href="file:///home/erkunder/Feuerwehr/Einsatzkonzepte/" target="_blank">📁 Einsatzkonzepte & Handbücher</a></li>
                <li><a href="file:///home/erkunder/Feuerwehr/Bedienungsanleitungen/" target="_blank">📁 Bedienungsanleitungen Messgeräte</a></li>
                <li><a href="file:///home/erkunder/Feuerwehr/Beständigkeitslisten/" target="_blank">📁 Beständigkeitslisten Gefahrstoffe</a></li>
                <li><a href="file:///home/erkunder/Feuerwehr/Sharepoint_FF/" target="_blank">☁️ Lokale SharePoint/OneDrive Kopie</a></li>
            </ul>
        </div>

        <!-- Online-Portale -->
        <div class="card">
            <h2>🌐 Online-Portale (Einsatz)</h2>
            <ul class="link-list">
                <li><a href="https://geoportal.bayern.de/bayernatlas/" target="_blank">🗺️ BayernAtlas</a></li>
                <li><a href="https://www.einsatzleiter-wiki.de/" target="_blank">📖 Einsatzleiter Wiki</a></li>
                <li><a href="https://oku.sfs-bayern.de/" target="_blank">📍 OKU Koordinaten-Tool (SFS-B)</a></li>
                <li><a href="https://www.divera247.com/" target="_blank">🚨 Divera 24/7</a></li>
                <li><a href="https://www.lfu.bayern.de/" target="_blank">☢️ Bayern Cloud (LfU Reaktorsicherheit)</a></li>
            </ul>
        </div>

        <!-- Hardware & Werkzeuge -->
        <div class="card">
            <h2>🛠️ Hardware & Werkzeuge</h2>
            <ul class="link-list">
                <li><a href="file:///home/erkunder/Skripte/usb-clear.sh">🧹 USB-Stick einlesen & löschen</a></li>
                <li><a href="file:///home/erkunder/Bilder/Kamera_Import/" target="_blank">📸 Foto-Zentrale (Kamera-Import)</a></li>
                <li><a href="file:///home/erkunder/Feuerwehr/Wartungspläne/" target="_blank">🗓️ Wartungsplaner & Dokumentation</a></li>
            </ul>
        </div>

        <!-- Kommunikation -->
        <div class="card">
            <h2>✉️ Kommunikation</h2>
            <ul class="link-list">
                <li><a href="mailto:erkunder@feuerwehr.de">📧 E-Mail Postfach (Erkunder)</a></li>
                <li><a href="https://teams.microsoft.com/" target="_blank">📹 Video-Call (MS Teams / Webex)</a></li>
                <li><a href="javascript:window.print()">🖨️ Aktuelle Ansicht drucken</a></li>
            </ul>
        </div>
    </div>

    <div style="text-align: center; margin-top: 4px; padding: 10px; color: #555; font-size: 0.8em;">
        Erkunder-Arbeitsplatz-System | Autologin-Modus aktiv | Physische Absicherung über Hardware-Token
    </div>

</body>
</html>
```
