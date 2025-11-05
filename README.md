# PrivacyShield — súkromné stiahnutie

Tento repozitár obsahuje jednoduchú statickú stránku (index.html) ktorá umožní obmedziť prístup k stiahnutiu APK pomocou klientského SHA-256 porovnania hesla. Upozornenie: klientská kontrola NIE JE bezpečná pre citlivé súbory. Nižšie sú odporúčané bezpečné metódy nasadenia.

## Súbory
- index.html — statická stránka (upraviť `PASSWORD_HASH` a `REPLACE_WITH_APK_SHA256`).
- server.js — príklad Node/Express servera s HTTP Basic autentifikáciou (voliteľné).
- .gitignore — bežné položky.

## Rýchle kroky — inicializácia repozitára (lokálne)
1. Vytvor projektový adresár a skopíruj súbory.
2. Spusti:
```bash
git init
git add .
git commit -m "Initial commit: PrivacyShield static page"
git branch -M main
git remote add origin https://github.com/<tvoj_user>/<repo>.git
git push -u origin main
```

## Ako vypočítať SHA-256 APK pre overenie integrity
- Linux/macOS:
  - sha256sum files/privacyshield.apk
  - alebo: shasum -a 256 files/privacyshield.apk
- Windows (PowerShell):
  - Get-FileHash .\files\privacyshield.apk -Algorithm SHA256

Skopíruj výstup (len hex) do `index.html` (pole `REPLACE_WITH_APK_SHA256`) a do README pre používateľov.

## Ako vygenerovať PASSWORD_HASH (SHA-256 hex)
Príklad (Linux/macOS):
```bash
echo -n 'moje_tajne_heslo' | sha256sum | awk '{print $1}'
```
Windows PowerShell:
```powershell
-join ((Get-FileHash -Algorithm SHA256 -InputStream ([System.IO.MemoryStream]::new([System.Text.Encoding]::UTF8.GetBytes('moje_tajne_heslo')))).Hash | ForEach-Object ToString("x2"))
```
Vložiť výsledný hex reťazec do `index.html` pre `PASSWORD_HASH`.

## Bezpečné nasadenie (odporúčané možnosti)
1. HTTP Basic Auth + TLS (Express / nginx)
   - Option A: Node/Express (príklad `server.js` v repozitári). Spusti za environment premenných `BASIC_USER` a `BASIC_PASS`.
   - Option B: nginx + htpasswd — vhodné pre statické stránky (zabezpečenie na úrovni servera).

2. S3 (privátny bucket) + presigned URLs
   - Nahraj APK do súkromného bucketu a generuj presigned URL, ktorú dáš len oprávneným používateľom. Toto je robustnejšie než klientská autentifikácia.

3. GitHub Releases (privátne repo)
   - Ak máš privátne repo, môžeš využívať release assets. Priamo kontroluj prístup cez GitHub.

## Príklad: Node/Express s HTTP Basic (server.js)
- Pozri `server.js` príklad v repozitári.
- Spusti: `BASIC_USER=admin BASIC_PASS=secret node server.js`
- Server bude servovať `/` (index.html) a súbory z `public/` len po autentifikácii.

## Overenie po nahratí
- Po pushnutí repo sem daj vedieť (alebo pošli URL) — skontrolujem, či v index.html
