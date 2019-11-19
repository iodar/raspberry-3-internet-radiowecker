# Raspberry Pi 3 - Internet Radiowecker

Dieses Repository enhält Anleitungen, Software & Links um einen Raspberry Pi
3 in einen Internet Radiowecker zu verwandeln. Für die Verwandlung wird unter
Anderem ein Display für den Raspberry benötigt. Die hier hinterlegten
Informationen stammen aus dem Artikel des Magazins `c´t`.

## Links

- [5 Zoll LCD Touch Display von Waveshare](https://www.waveshare.com/5inch-hdmi-lcd-with-bicolor-case.htm)
- [Anleitung zur Installation des Displays](https://www.waveshare.com/img/devkit/accessories/5inch-HDMI-LCD-Bicolor-Holder/5inch-HDMI-LCD-Bicolor-Holder-LCD-assemble.jpg)

## Schritte zum Bau

### 1. Zusammenbau Gehäuse

Als erstes empfiehlt sich das Gehäuse zusammen zu bauen. Dazu kann die verlinkte bebilderte Anleitung genutzt werden.
Falls die Anleitung nicht mehr im Internet zur Verfügung steht, dann kann diese auch
[hier im Repo gefunden werden](assets/installation-case/5inch-HDMI-LCD-Bicolor-Holder-LCD-assemble.jpg).

### 2. Verbindung Gehäuse und Raspberry

- Dislpay auf GPIO Leiste stecken, sodass die HDMI-Buchsen von Display und Raspberry übereinander liegen
- Raspberry einschalten
- Display zeigt Streifen ==> Ok, da noch kein Treiber für das Display installiert ist

### 3. Installation OS auf Raspberry

- [Raspbian Buster Lite herunterladen (raspberrypi.org)](https://www.raspberrypi.org/downloads/raspbian/)
  - Alternativer Download [Raspbian Buster Lite herunterladen (heise.de)](https://www.heise.de/download/product/raspbian-91329)
- Flash Software wie z.B. [Belena Etcher herunterladen](https://www.balena.io/etcher/)
- Raspbian Buster Lite auf SD-Karte flashen
- Karte auswerfen
- Karte wieder einstecken, damit Partitionstabelle erneut eingelesen wird
- Datei `ssh` auf Partition `boot` anlegen, damit Rapsberry per `ssh` eingerichtet werden kann
- Datei `wpa_supplicant.conf` auf Partition `boot` anlegen, damit Raspberry sich mit WLAN verbinden kann

Inhalt der Datei `wpa_supplicant.conf`

```bash
ctrl_interface=/run/wpa_supplicant #definiert das control interface
update_config=1 #switch der angibt, ob Konfiguration ueber 'raspi-config' geaendert werden kann
country=DE
network={
    ssid="<WLANNAME>"
    psk="<WLANPASSWORD>" #an dieser Stelle kann auch ein encrypteter Schlüssel angegeben werden
}
```

- In der [offiziellen Dokumentation von Raspberry Pi](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md) wird beschrieben, wie ein encrypteter Schlüssel erstellt werden kann


### 4. Installation Stream Player Mopidy

Für die Wiedergabe von Musik und Abrufen von Musik von Streamingdiensten ist die der Stream Player `Mopidy`
erforderlich. Die Version der Anwendung in der offiziellen Paketquelle von Raspbian Buster ist bereits sehr
alt. Daher wird empfohlen eine inoffizielle Paketquelle mit den aktuellen Versionen des Players hinzuzufügen.
Dieser Prozess wird in den folgenden Schritten beschrieben.

Signaturschlüssel für Pakequelle impotieren

```bash
curl -G https://apt.mopidy.com/mopidy.gpg | sudo apt-key add -
```

Paketquelle hinzufügen

```bash
sudo curl -G -o /etc/apt/sources.list.d/mopidy.list https://apt.mopidy.com/buster.list
```

Pakete installieren

```bash
sudo apt update
sudo apt upgrade -y #bestätigt Donwload und Installation der Pakete
```

Installation aller Abhängigkeiten

```bash
sudo apt install --no-install-recommends -y xserver-xorg xinit \
xterm xserver-xorg-input-evdev xserver-xorg-video-fbturbo \
lightdm gstreamer1.0-alsa python3-pip python3-pygame python3-venv \
python3-wheel python-pip python-setuptools python-wheel \
python-alsaaudio mopidy mopidy-tunein mopidy-podcast-itunes
```

## TODOs

- [x] Anleitung zur Montage des Gehäuses speichern
- [x] Erster Start Raspberry
- [x] Installation Stream Player `Mopidy` dokumentieren
- [ ] Installtion Treiber dokumentieren

## Wichtige Kommandos

Scannen des Wlan Netzes mit dem Interface `wlan0`

```bash
sudo iwlist wlan0 scan
```

Neukonfiguerien des Interfacesc `wlan0`

```bash
wpa_cli -i wlan0 reconfigure
```
