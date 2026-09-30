# Handoff: maganmasolasidij.hu – teljes webhely újratervezés

## Overview
Az Artisjus és további 4 közös jogkezelő (EJI, FILMJUS, HUNGART, MAHASZ) tájékoztató portáljának újratervezése. A jelenlegi egyoldalas (one-page, horgonyos) Drupal 10 oldal helyett önálló, indexelhető aloldalak, ügyfélportál és teljes visszajelzés-rendszer (siker / hiba / figyelmeztetés / töltés / üres állapot).

## About the Design Files
A csomagban lévő fájlok **HTML-ben készült design-referenciák** (működő prototípus), nem közvetlenül átvehető production kód. A feladat ezeknek a designoknak az **újraépítése a cél-kódbázis környezetében** (jelenleg Drupal 10 – Twig téma + Drupal Form API / webform; vagy ha újraírás történik, a csapat által választott keretrendszerben), annak bevett mintáival és könyvtáraival.

A `.dc.html` fájlok böngészőben közvetlenül megnyithatók (a `support.js` futtatja őket). Minden fájl egy sablont (HTML + inline style, `{{ }}` helyőrzők, `<sc-for>` / `<sc-if>` vezérlés) és egy `class Component` logikai osztályt tartalmaz – a logikában található a **teljes szöveges tartalom**, a validáció és az állapotgép.

## Fidelity
**High-fidelity.** Végleges színek, tipográfia, térközök, interakciók és szövegek. Pixelpontos újraépítés az elvárás.

## Oldaltérkép és fájlok

| Útvonal | Fájl | `page` prop |
|---|---|---|
| `/` | `PageHome.dc.html` | – |
| `/a-maganmasolasi-dijrol` | `PageDij.dc.html` | `/a-maganmasolasi-dijrol` |
| `/a-maganmasolasi-dijrol/mit-masolhatok-szabadon` | `PageDij.dc.html` | … |
| `/a-maganmasolasi-dijrol/gyik-kereskedoknek` | `PageDij.dc.html` | … (11 kérdés, kereső) |
| `/befizetett-dijak-sorsa` | `PageSorsa.dc.html` | áttekintés + felosztási arányok |
| `/befizetett-dijak-sorsa/jogosulti-csoportok` | `PageSorsa.dc.html` | arányok + 5 jogkezelő + szabályzat-linkek |
| `/befizetett-dijak-sorsa/kulturalis-celok` | `PageSorsa.dc.html` | NKA, Hangfoglaló, alprogramok, Kollégium |
| `/visszaterites` | `PageVissza.dc.html` | áttekintés, lépések |
| `/visszaterites/tajekoztato` | `PageVissza.dc.html` | teljes tájékoztató + PDF |
| `/visszaterites/ugyintezes` | `PageVissza.dc.html` | előzetes bejelentési űrlap (belépéshez kötött) |
| `/visszaterites/ellenorzok` | `PageVissza.dc.html` | IMEI / PCSN ellenőrző |
| `/visszaterites/gyik` | `PageVissza.dc.html` | 16 kérdés, kereső |
| `/ugyfelportal` | `PagePortal.dc.html` | belépés (belépve → `/fiok`) |
| `/ugyfelportal/regisztracio` | `PagePortal.dc.html` | regisztráció |
| `/ugyfelportal/jelszo` | `PagePortal.dc.html` | új jelszó igénylése |
| `/ugyfelportal/uj-jelszo` | `PagePortal.dc.html` | új jelszó beállítása (e-mailes linkről) |
| `/ugyfelportal/fiok` | `PagePortal.dc.html` | Igényléseim (belépés nélkül → `/ugyfelportal`) |
| `/kapcsolat` | `PageInfo.dc.html` | elérhetőség, üzemeltetők (`#uzemeltetok`), kapcsolati űrlap |
| `/adatvedelmi-tajekoztato` | `PageInfo.dc.html` | tartalomjegyzékes dokumentum |
| `/suti-tajekoztato` | `PageInfo.dc.html` | sütitáblázat + beállítás gomb |
| bármi más | `PageInfo.dc.html` | 404 |

- `Weboldal.dc.html` – **belépési pont / shell**: fejléc, kliensoldali router, lábléc, süti-hozzájárulás sáv, toast. Ezt nyisd meg a teljes prototípushoz.
- `Allapotok.dc.html` – az összes visszajelzés-állapot egy lapon + tesztadatok az előidézésükhöz.
- `Nyitóoldal v3.dc.html` – a jóváhagyott nyitóoldal önálló változata (referencia).

## Globális elrendezés

- **Konténer:** `max-width: 1200px; margin: 0 auto; padding: 0 28px`.
- **Fejléc (sticky, z 40):** háttér navy, padding 16px 28px. Bal: logó (34px bakelit-kör + „Magánmásolási díj” Source Serif 4 700 22px). Jobb: fő navigáció (15px/500, gap 18px, aktív: mustár szín + 2px mustár alsó vonal) + „Ügyfélportál” pill gomb (mustár, belépve „Fiókom”). Skip link: „Ugrás a tartalomra”.
- **Aloldal-hero:** navy sáv, jobb felső sarokban barázdás kör dekor (`repeating-radial-gradient`, 6% fehér). Morzsamenü (14px), eyebrow (13px/700, uppercase, .07em, mustár), H1 (Source Serif 4 700, `clamp(34px,4.4vw,52px)`, lh 1.08), lead (19px, `oklch(0.88 0.03 262)`). Padding 40px 28px 56px.
- **Aloldal-törzs:** flex-wrap, gap 48px. Bal oldalsáv (`flex:1 1 220px; max-width:280px; position:sticky; top:96px`) – „Ebben a részben” almenü, aktív elem navy háttér + fehér szöveg, radius 10px. Tartalom: `flex:999 1 520px; max-width:780px`. Oldal végén „Tovább” kártyák (halványkék, radius 14px).
- **Lábléc:** sötét navy; 4 oszlop (Ügyfélszolgálat, Ügyfélfogadás, Tájékoztatás linkek, Visszatérítés linkek), GVH-szöveg + PDF, impresszum, Adatvédelmi / Süti-tájékoztató / Süti-beállítások.

## Nyitóoldal (`/`)
1. **Hero** (navy): bal – eyebrow, H1 „A szabad magáncélú másolás lehetőségéért fizetendő díj” (utolsó szó mustár), lead, 2 gomb. Jobb – **hanglemez-kompozíció**: mustár lemezborító (72% szélesség, aspect 1:1, radius 6px, árnyék `0 30px 60px rgba(0,0,0,.35)`) a hanghordozós felosztással (45/30/25%, navy sávok), mögötte jobbra kilógó bakelit (barázdák: `repeating-radial-gradient(circle,#12151e 0 2px,#252b3b 2px 3.2px)`), mustár címke körbefutó felirattal („ARTISJUS · EJI · FILMJUS · HUNGART · MAHASZ ·”, SVG textPath), statikus fényes conic-gradient réteg. **A lemez 7 s alatt fordul körbe, lineárisan, végtelenítve**; `prefers-reduced-motion` esetén álljon meg (production-ben add hozzá).
2. **„Megfizették a díjat?”** kártya a hero aljára csúsztatva (`margin-top:-44px`), fehér, radius 14px, árnyék `0 18px 40px rgba(30,35,70,.12)`; IMEI és PCSN link-kártya.
3. **Kinek szól?** 3 kártya (Fizetőknek / Visszatérítés / Kapcsolat), halványkék háttér, navy számozott kör, 3 link.
4. **A díjról**, **Díjak sorsa** (25% NKA navy blokk, 5 jogkezelő lista), **Visszatérítés** (halványkék szekció, 5 lépés), **GYIK** (fül: Kereskedőknek / Visszatérítés).

## Interakciók és viselkedés
- **Navigáció:** belső linkek valódi `href`-fel, a prototípusban kliensoldali router (`nav(path)`); horgony (`/kapcsolat#uzemeltetok`) esetén 90px offsettel görget (sticky fejléc). Oldalváltáskor lap tetejére ugrik.
- **GYIK harmonika:** egyszerre egy nyitott elem (első alapból nyitva); `+`/`−` kör ikon (nyitva navy). Keresés: kérdés + válasz szövegében, találatok automatikusan nyitva, számláló („3 találat”), üres állapot „Nincs találat” + „Keresés törlése”. Minden kérdésnek van `id`-je (`k1…k11`, `v1…v16`) mélylinkhez.
- **Belépés-függés:** `/visszaterites/ugyintezes` kijelentkezve tájékoztató sávot mutat, az űrlap 55% opacitású és nem interaktív.
- **Hover:** kártyák `translateY(-2px/-3px)` 200–250ms; linkek sötétebb kék + aláhúzás; pill gombok világosabb árnyalat.
- **Megjelenés:** visszajelzések `mmdin` animációval (opacity 0→1, translateY 8px→0, 250–300ms, `cubic-bezier(.22,.61,.36,1)`).
- **Toast:** jobb alul, navy, zöld pipás kör, 2,8 s után eltűnik (süti-mentés után).
- **Süti-sáv:** első látogatáskor alul középen (max 760px). „Összes elfogadása” / „Csak a szükségesek” / „Beállítások” (kinyitja: Szükséges – mindig aktív, Statisztikai – kapcsolható, „Kiválasztottak mentése”). A láblécből és a Süti-tájékoztatóból újranyitható. Tárolás: `mmd-cookie` = `all` | `needed`.

## Űrlapok, validáció, állapotok
Beküldéskor validál (nem gépelés közben); gépeléskor az adott mező hibája törlődik. Hibás mező: 1.5px piros keret, halvány piros háttér, `aria-invalid`, alatta félkövér piros üzenet. Az előzetes bejelentésnél összesítő riasztás a lap tetején („N mezőt kell javítanod”).

**Előzetes bejelentési űrlap** (`/visszaterites/ugyintezes`) – csoportok:
- Igénylő adatai: Teljes név*, E-mail*, Telefonszám, Lakcím*, Alkotói tevékenység* (select)
- A hordozó adatai: Hordozó típusa* (select), Darabszám* (≥1), Vásárlás dátuma* (**csak aktuális év**), Eladó neve*, Hologramos címke sorszáma / IMEI* (6–20 karakter, `[A-Za-z0-9-]`)
- Tárolt tartalom: leírás* (textarea)
- Nyilatkozatok: saját professzionális tartalom*, nem használom magánmásolásra*, társszerzői nyilatkozat (15.4 pont), adatvédelem*
- Állapotok: idle → sending (gomb letiltva, spinner, „Küldés…”) → **ok** (azonosító `MMD-ÉÉÉÉ-NNNNN`, „Hogyan tovább?” 3 lépés, PDF-összesítő, Igényléseim, Új bejelentés) | **fail** (riasztás, űrlap megmarad).
- **Megjegyzés:** a mezőlistát az éles előzetes bejelentőlap alapján pontosítani kell (a jelenlegi megvalósítás belépés mögött van, nem látható).

**Ellenőrzők** (`/visszaterites/ellenorzok`): fül IMEI / PCSN. IMEI = pontosan 15 számjegy; SN = 6–24 karakter. Eredmény: **Regisztrált státuszú** (zöld) / **Nem található** (mustár, link az ügyfélszolgálathoz) / **Rendszer nem érhető el** (piros, Újrapróbálás). Élesben az Artisjus IMEI (imei.artisjus.com) és PCSN (artisjus.hu) rendszerének API-ját kell bekötni – egyeztetendő; ha nincs API, maradjon külső link.

**Ügyfélportál:**
- Belépés: hibás adat → piros riasztás; 3. hibás próbálkozás → figyelmeztetés, 15 perc tiltás. Siker → `/ugyfelportal/fiok`.
- Regisztráció: név, e-mail, jelszó (min. 8 + szám, 4 szegmenses erősségjelző), jelszó újra, adatvédelmi checkbox. Foglalt e-mail → hiba. Siker → „Ellenőrizd a postafiókodat” + Újraküldés.
- Új jelszó igénylése: siker üzenete szándékosan semleges (nem árulja el, létezik-e a fiók).
- Új jelszó beállítása: siker „Jelszó módosítva”; lejárt link → figyelmeztetés.
- Igényléseim: lista (azonosító, dátum, hordozó, státusz pill, megjegyzés); státuszok: Elbírálás alatt / Hiánypótlás szükséges / Jóváhagyva / Elutasítva; üres állapot bakelit ikonnal. A belépés utáni valós funkciókat az ügyféltől kell bekérni.

**Kapcsolati űrlap** (új elem): név, e-mail, téma (select), üzenet (≥20 karakter) → „Köszönjük, üzenetedet megkaptuk” | hálózati hiba riasztás.

A prototípusbeli tesztbemeneteket (pl. „0000” címke, „foglalt” e-mail) az `Allapotok.dc.html` 6. szakasza sorolja fel – ezek csak a demo miatt vannak, élesben a backend válaszai vezérlik.

## State Management
- Shell: `route`, `loggedIn`, `cookie` (consent), `cookieOpen`, `cookieDetail`, `analytics`, `toast`. Prototípusban localStorage: `mmd-route`, `mmd-login`, `mmd-cookie`.
- Űrlapok: `values`, `errors`, `status` (`idle | sending/busy | ok | fail | badcred | locked | taken | regdone | sent | pwdone | expired`), `fails` (belépési kísérletek).
- GYIK: `open` index, `query`.
- Ellenőrző: `tab`, `code`, `codeErr`, `checking`, `res` (`ok | no | err`).

## Design Tokens
Színek (oklch, zárójelben közelítő hex):
- Navy (elsődleges, fejléc, gombok): `oklch(0.28 0.08 262)` (~#1B2A55)
- Sötét navy (lábléc): `oklch(0.22 0.07 262)` (~#111D40)
- Mustár (kiemelés, CTA): `oklch(0.85 0.14 85)` (~#EBC04A); hover `oklch(0.9 0.12 85)`; halvány `oklch(0.96 0.045 85)`
- Link kék: `oklch(0.42 0.12 255)` (~#2856A0); hover `oklch(0.32 0.12 255)`
- Halványkék felület: `oklch(0.95 0.02 262)` (~#E9EDF6); még halványabb `oklch(0.97 0.012 262)`
- Oldalháttér: `#F6F5F1`; kártya: `#FFFFFF`
- Szöveg: `oklch(0.24 0.04 262)`; másodlagos `oklch(0.36–0.45 0.03 262)`; hairline `oklch(0.9 0.01 262)`, input keret `oklch(0.86 0.015 262)`
- Siker: háttér `oklch(0.95 0.04 150)`, ikon `oklch(0.5 0.12 150)`, szöveg `oklch(0.35 0.1 150)`
- Hiba: háttér `oklch(0.95 0.035 27)`, ikon/keret `oklch(0.55 0.2 27)`, szöveg `oklch(0.42–0.5 0.16–0.19 27)`
- Figyelmeztetés: háttér `oklch(0.96 0.05 85)`, ikon `oklch(0.72 0.14 75)`, szöveg `oklch(0.4 0.08 70)`

Tipográfia:
- Címsor: **Source Serif 4** 700 (H1 `clamp(34px,4.4vw,52px)`, nyitó H1 `clamp(40px,5.2vw,64px)`, H2 `clamp(28px,3.2vw,40px)` / 26–28px, kártyacím 21–26px)
- Szöveg/UI: **Source Sans 3** 400/500/600/700 – törzs 17px/1.6, lead 19px, kicsi 15px, címke 14–15px/600, eyebrow 13px/700 uppercase .07em
- Kód/azonosítók: `ui-monospace`

Térköz: szekciók 80–88px függőleges padding, kártyák 22–32px, rács gap 10–20px, oldalsáv–tartalom 48px.
Radius: 6px (lemezborító), 8–12px (input, kis kártya), 14–16px (nagy kártya), 999px (gombok, pillek, fülek).
Árnyék: kártya `0 1px 2px rgba(30,35,70,.06)`; lebegő `0 18px 40px rgba(30,35,70,.12)`; süti-sáv `0 20px 50px rgba(20,25,60,.25)`.

## Assets
- Nincs raszterkép. A bakelit, barázda-dekor és logó CSS-gradiensekből készül; ikonok inline SVG (monoline, 2px, round cap).
- A jogkezelők logói (a jelenlegi oldalon: `/sites/default/files/media/images/*-logo.*`) a Jogosulti csoportok oldalra beemelhetők.
- PDF-ek: `/media/6/download` (visszatérítési tájékoztató), `/media/7/download` (GVH végzés).

## Nyitott pontok
- Adatvédelmi tájékoztató: a prototípus csak a szerkezetet és rövid összefoglalót mutatja; a teljes jogi szöveget a jelenlegi oldalról kell átemelni.
- Sütitáblázat nevei/időtartamai (SESS…, cookie-agreed 100 nap, _ga 2 év) a Drupal/GA alapértékei – ellenőrizendők.
- Az eredeti oldal „kilenc alprogramról” ír, de csak nyolcat sorol fel – tisztázandó.
- Mobil: a fő navigáció most tördelődik; ~900px alatt hamburger menü javasolt.
- Angol nyelvű verzió, tarifatáblázat beemelése: ügyféldöntés.

## Files
- `Weboldal.dc.html` – shell, router (belépési pont)
- `PageHome.dc.html`, `PageDij.dc.html`, `PageSorsa.dc.html`, `PageVissza.dc.html`, `PagePortal.dc.html`, `PageInfo.dc.html`
- `Allapotok.dc.html` – állapotgaléria
- `Nyitóoldal v3.dc.html` – nyitóoldal referencia
- `support.js` – a `.dc.html` fájlok futtatókörnyezete (csak a prototípus megnyitásához)
