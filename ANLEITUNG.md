# Universal Links mit GitHub Pages einrichten

Domain: `morsemessage.github.io`

## 1. Team-ID und Bundle-ID herausfinden

- **Team-ID:** developer.apple.com/account ▸ Membership details ▸ *Team ID* (10 Zeichen, bei dir: `UKB7B6QZ9W`).
- **Bundle-ID:** Xcode ▸ Target ▸ General ▸ *Bundle Identifier* (vorgeschlagen: `de.roland.MorseMessage`).

In `.well-known/apple-app-site-association` ist bereits eingetragen (bei anderer Bundle-ID dort anpassen):

    "appIDs": [ "UKB7B6QZ9W.de.roland.MorseMessage" ]

## 2. GitHub-Repository anlegen

1. Leg auf github.com **in der Organisation `morsemessage`** ein **öffentliches** Repository an, das **exakt** `morsemessage.github.io` heißt.
   Wichtig: Ein Repo mit anderem Namen (z. B. `morse`) funktioniert nicht, weil Apple die Datei unter der Domain-Wurzel sucht.
2. Lade den **gesamten Inhalt** dieses Ordners hoch, auch die versteckten Einträge `.nojekyll` und `.well-known/`.
   Finder blendet Punkt-Dateien aus. Mit **⌘ + ⇧ + .** machst du sie sichtbar. Alternativ im Terminal:

       cd github-pages
       git init && git add -A && git commit -m "Morse Universal Links"
       git branch -M main
       git remote add origin https://github.com/morsemessage/morsemessage.github.io.git
       git push -u origin main

3. Repo ▸ Settings ▸ Pages ▸ *Deploy from a branch* ▸ `main` / `(root)` ▸ Save.

`.nojekyll` ist nötig, weil GitHub Pages Ordner mit Punkt am Anfang (`.well-known`) sonst nicht ausliefert.

## 3. Prüfen

Nach 1–2 Minuten sollte im Browser Folgendes klappen:

- `https://morsemessage.github.io/.well-known/apple-app-site-association` → zeigt das JSON (oder lädt es herunter)
- `https://morsemessage.github.io/play/?c=...%20---%20...` → zeigt die Morse-Seite mit „SOS“

Apple holt die Datei über ein eigenes CDN. Ob es sie schon kennt, siehst du hier (kann anfangs einige Stunden dauern):

- `https://app-site-association.cdn-apple.com/a/v1/morsemessage.github.io`

## 4. Xcode

1. Target ▸ **Signing & Capabilities** ▸ **+ Capability** ▸ **Associated Domains**
2. Eintrag: `applinks:morsemessage.github.io`
3. In `MorseCode.swift` ▸ `universalHost = "morsemessage.github.io"` setzen.
4. Die App **vom iPhone löschen und neu installieren.** iOS lädt die Zuordnung nur bei der Installation.

**Tipp zum Testen ohne CDN-Wartezeit:** Als Eintrag `applinks:morsemessage.github.io?mode=developer` verwenden. Außerdem auf dem iPhone unter Einstellungen ▸ Entwickler ▸ **Associated Domains Development** aktivieren. Vor der App-Store-Veröffentlichung `?mode=developer` wieder entfernen.

## 5. Testen

- In der App Text eingeben ▸ Teilen ▸ WhatsApp an dich selbst ▸ auf den Link tippen → die App öffnet sich und spielt ab.
- **Nicht** in die Safari-Adresszeile tippen. Dort löst iOS Universal Links absichtlich nie aus.
- Hast du einmal „In Safari öffnen“ gewählt, merkt sich iOS das. Dann den Link lange drücken ▸ **„In Morse Message öffnen“**. Danach klappt es wieder direkt.
- Empfänger ohne App landen auf der Webseite und können die Nachricht dort im Browser anhören.
