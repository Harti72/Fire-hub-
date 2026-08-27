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

## 🏠 Die Offline-Startseite

Die eigenständige Offline-Startseite liegt jetzt als [Erkunder-Kiosk.html](Erkunder-Kiosk.html) im Repository. Sie enthält die lokalen Einsatzdokumente, Online-Portale, Hardware-Werkzeuge und Kommunikationsfunktionen. Der Online-/Offline-Schalter blendet bei fehlender Verbindung nur externe Links aus.

### Autostart im Kiosk-Modus

Für den Erkunder-Arbeitsplatz wird empfohlen, den Browser nach der Anmeldung des lokalen Benutzers `erkunder` automatisch mit der Kiosk-Seite zu starten. Dadurch steht die Startoberfläche ohne manuelle Navigation bereit.

Der vorgesehene Ablauf ist:

1. Linux startet den Benutzer `erkunder`.
2. NetworkManager verbindet automatisch ein verfügbares, bekanntes WLAN-Profil.
3. Chromium startet im Kiosk-Modus mit `Erkunder-Kiosk.html`.
4. Die Seite wählt den Online- oder Offline-Modus anhand der Netzwerkverbindung vor.
5. Bei einem Browserabsturz wird der Neustart durch die lokale Systemkonfiguration sichergestellt.

Beispiel für einen Chromium-Teststart:

```bash
chromium --kiosk --noerrdialogs --disable-session-crashed-bubble file:///home/erkunder/Erkunder-Kiosk.html
```

Für Desktop-Umgebungen mit Autostart kann folgende Datei verwendet werden:

```ini
[Desktop Entry]
Type=Application
Name=Erkunder-Kiosk
Exec=chromium --kiosk --noerrdialogs --disable-session-crashed-bubble file:///home/erkunder/Erkunder-Kiosk.html
Terminal=false
X-GNOME-Autostart-enabled=true
```

Der vorgesehene Speicherort ist:

```text
/home/erkunder/.config/autostart/erkunder-kiosk.desktop
```

Die Autostart-Datei und die Kiosk-Seite können im Repository dokumentiert beziehungsweise versioniert werden. WLAN-Passwörter, NetworkManager-Profile, Zertifikate und lokale Einsatzdokumente werden dagegen ausschließlich auf dem Linux-Rechner oder über einen geschützten Provisioning-Prozess eingerichtet. Sie dürfen nicht in Git gespeichert werden.

Für einen produktiven Betrieb sind zusätzlich ein eigener Benutzer ohne Administratorrechte, LUKS-Festplattenverschlüsselung, regelmäßige Browser- und Systemupdates sowie ein definierter Wartungs- und Notausstieg erforderlich. Funktionen wie USB-Löschen oder Kameraimport müssen über lokale Linux-Verknüpfungen beziehungsweise geprüfte Skripte angebunden werden, da eine HTML-Seite keine Shell-Skripte ausführen darf.
