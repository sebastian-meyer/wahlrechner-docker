# Aufsetzen eines Wahlrechner-Servers für [Wahlkompass Dresden](https://wahlkompass-dresden.de)

Dieses Repository dient als Hifestellung, um eine [Wahlrechner](https://github.com/sebastian-meyer/wahlrechner)-Instanz für [DresdenZero](https://www.dresdenzero.de), [Zukunftsgestalten](https://zukunftsgestalten.org), [Lokale Agenda Dresden](https://la-dresden.de) und [BUND Dresden](https://www.bund-dresden.de) auf (m)einem Server aufzusetzen.
Die folgende Anleitung funktioniert nur auf Debian-basierten Systemen, und wurde ausschließlich mit `Debian 11 (Bullseye)` getestet.
Sie ist außerdem etwas auf die Umgebung des Servers von DresdenZero zugeschnitten und enthält deshalb nicht alle Schritte der [Originalanleitung](https://github.com/wahlrechner/server).

## Vorraussetzungen

### Repository klonen

```
mkdir wahl/
git clone --recurse-submodules https://github.com/sebastian-meyer/wahlrechner-docker wahl/
cd wahl
```

## Konfiguration des Wahlrechners

**Bevor du den Wahlrechner-Server das erste Mal startest,** musst du die Konfigurationsdatei `config.env` erstellen. Eine Vorlage ist unter `config/config.env.example` zu finden.

```
cp config/config.env.example config/config.env
```

**DJANGO_SECRET_KEY:** Ersetze `ChangeThisToRandomStringInProduction` durch eine [zufällig generierte](https://1password.com/de/password-generator/) (mind. 30 Zeichen lang, bestehend aus Zahlen, Buchstaben und Sonderzeichen) Zeichenkette. Teile den Secret Key niemals mit jemand anderem!

**DJANGO_DEFAULT_ADMIN_PASSWORD:** Beim erstmaligen Starten des Wahlrechners wird automatisch ein Admin-Account erstellt. Ersetze `adminpassword` durch ein erstes, sicheres Passwort. **Nach der erstmaligen Anmeldung in der Admin-Oberfläche solltest du dein Passwort zusätzlich nochmal ändern.**

**MYSQL_PASSWORD:** Ersetze `SetDatabaseUserPassword` durch ein zufällig generiertes Passwort. Du wirst es niemals von Hand eingeben müssen - also lass dir bitte ein sicheres Passwort mit einem [Passwortgenerator](https://1password.com/de/password-generator/) generieren.

## Installation eines SSL-Zertifikats

### Let's Encrypt

```
sudo certbot certonly --webroot -w /var/www/http/common/letsencryptauth -d wahlkompass-dresden.de
```

Erstelle anschließend einen Symlink, damit die Zertifikate automatisch aktualisiert werden können.

```
ln -s /etc/letsencrypt/live/wahlkompass-dresden.de/* web/cert/
```

## Starten, Stoppen und Aktualisieren des Wahlrechners

Du kannst den Server mit Docker **starten**:

```
sudo docker compose up -d
```

Du kannst den Server mit Docker wieder **stoppen**:

```
sudo docker compose down
```

Um die Wahlrechner-Instanz auf die neuste Version zu **aktualisieren**, lade diese von GitHub:

```
sudo docker compose down
git pull
sudo docker compose up -d
```
