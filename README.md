# Sudoku

Einfaches Sudoku zum Einbetten per iframe. Läuft als statische Seite auf
Cloudflare Workers (nur Static Assets, kein Worker-Code, keine Datenbank).

Basiert auf [0xPacman/Sudoku](https://github.com/0xPacman/Sudoku)
(Commit `147b85c`, MIT-Lizenz, siehe `LICENSE`, die Datei muss im Repo bleiben).
Das Original-README liegt als `UPSTREAM-README.md` bei.

```
public/index.html   Markup, Overlays
public/app.js       Spielzustand, Bedienung, Animationen
public/sudoku.js    Engine: Generator (eindeutige Lösung), Prüfung
public/style.css    Design
public/fonts/       Space Grotesk (lokal, OFL)
public/_headers     HTTP-Header (wer per iframe einbetten darf)
wrangler.toml       Cloudflare-Konfiguration
embed-test.html     lokale Testseite fürs iframe
```

## Lokal starten

Node liegt projektlokal in `.tools/node` (arm64, nicht im Repo).

```bash
export PATH="$PWD/.tools/node/bin:$PATH"
npm install
npm run dev        # http://127.0.0.1:8789
```

## Deployen

```bash
export PATH="$PWD/.tools/node/bin:$PATH"
npx wrangler login     # einmalig
npm run deploy         # -> https://sudoku.<account>.workers.dev
```

Eigene Domain (z.B. `sudoku.tsri.ch`): im Cloudflare-Dashboard beim Worker
unter *Settings → Domains & Routes*.

## Einbetten

```html
<iframe src="https://sudoku.<account>.workers.dev/" title="Sudoku" loading="lazy"
        style="width:100%;max-width:480px;height:740px;border:0;display:block;margin:0 auto"></iframe>
```

Die App ist max. 460px breit und braucht ca. 730px Höhe (Handy ca. 610px).

Wer einbetten darf, steuert `public/_headers` (`frame-ancestors`). Aktuell
alle; zum Einschränken die eigene Domain eintragen.

## Anpassungen gegenüber dem Original

- Texte auf Deutsch (Schweizer Schreibweise, «ss»).
- Design ans Worträtsel angelehnt (`../Raetsel/public/static/game.css`):
  weiss, Space Grotesk, Akzent `#2f80ed`, graue Tasten, runde Dialoge.
  Standard ist hell, Umschalter auf dunkel bleibt.
- Keine externen Anfragen: Space Grotesk liegt lokal in `public/fonts/`
  (SIL Open Font License, `OFL-LICENSE.txt` muss dabei bleiben).
- Laufendes Spiel wird im Browser gespeichert (`localStorage`, Schlüssel
  `sudoku-game-v1`) und beim Neuladen fortgesetzt; nach dem Lösen gelöscht.
- «Neues Spiel» pausiert das laufende Spiel und hat «Abbrechen».

Nach Änderungen an CSS/JS die `?v=`-Nummer in `index.html` hochzählen,
damit Browser nicht die alte Version aus dem Cache nehmen.
