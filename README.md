# Handoff: maganmasolasidij.hu – teljes webhely újratervezés

## Overview
Az Artisjus és további 4 közös jogkezelő (EJI, FILMJUS, HUNGART, MAHASZ) tájékoztató portáljának újratervezése. A jelenlegi egyoldalas (one-page, horgonyos) Drupal 10 oldal helyett önálló, indexelhető aloldalak, belépés nélküli, lépésenkénti visszatérítés-igénylés, közös GYIK, önálló díjfizetés-ellenőrző és teljes visszajelzés-rendszer (siker / hiba / figyelmeztetés / töltés / üres állapot).

**A célplatform WordPress** – a megvalósítás részleteit a [WordPress-megvalósítás](#wordpress-megvalósítás) fejezet írja le.

## About the Design Files
A csomagban lévő fájlok **HTML-ben készült design-referenciák** (működő prototípus), nem közvetlenül átvehető production kód. A feladat ezeknek a designoknak az **újraépítése WordPressben** (egyedi blokktéma vagy klasszikus téma + ACF-blokkok, Gravity Forms), annak bevett mintáival és bővítményeivel.

A `.dc.html` fájlok helyi webszerverről nyithatók meg (a `support.js` futtatja őket, `file://`-ról nem töltenek be). Minden fájl egy sablont (HTML + inline style, `{{ }}` helyőrzők, `<sc-for>` / `<sc-if>` vezérlés) és egy `class Component` logikai osztályt tartalmaz – a logikában található a **teljes szöveges tartalom**, a validáció és az állapotgép.

## Fidelity
**High-fidelity.** Végleges színek, tipográfia, térközök, interakciók és szövegek. Pixelpontos újraépítés az elvárás – kivéve, ahol a WordPress-fejezet bővítményre (Gravity Forms, süti-bővítmény) bízza a markupot: ott a bővítmény kimenetét kell a designhoz stílusozni.

## Oldaltérkép és fájlok

| Útvonal | Fájl | Tartalom |
|---|---|---|
| `/` | `PageHome.dc.html` | nyitóoldal |
| `/a-maganmasolasi-dijrol` | `PageDij.dc.html` | egy oldal, sticky tartalomjegyzékkel: A díj lényege · Kik a jogosultak? · Díjszabás és igazolás (2 PDF) · Mit másolhatok szabadon? (`#mit-masolhatok`) |
| `/befizetett-dijak-sorsa` | `PageSorsa.dc.html` | áttekintés + NKA 25% |
| `/befizetett-dijak-sorsa/jogosulti-csoportok` | `PageSorsa.dc.html` | felosztási arányok + 5 jogkezelő + szabályzat-linkek |
| `/befizetett-dijak-sorsa/kulturalis-celok` | `PageSorsa.dc.html` | NKA, Hangfoglaló, alprogramok, Kollégium |
| `/visszaterites` | `PageVissza.dc.html` | egy oldal, oldalsáv nélkül: feltételek + PDF + az ügyintézés 4 lépése + „Igénylés indítása” |
| `/visszaterites/igenyles` | `PageIgenyles.dc.html` | lépésenkénti igénylés (belépés nélkül), oldalsáv nélkül |
| `/gyik` | `PageGyik.dc.html` | közös GYIK: Díjfizetés (15) / Visszatérítés (11) fül + kereső mindkettőben |
| `/ellenorzo` | `PageEllenorzo.dc.html` | IMEI / PCSN ellenőrző (`#imei`, `#pcsn`) – független az igényléstől |
| `/kapcsolat` | `PageInfo.dc.html` | kapcsolati űrlap (elsődleges) + elérhetőség, ügyfélfogadás, üzemeltetők (`#uzemeltetok`) |
| `/adatvedelmi-tajekoztato` | `PageInfo.dc.html` | tartalomjegyzékes dokumentum |
| `/suti-tajekoztato` | `PageInfo.dc.html` | sütitáblázat + beállítás gomb |
| bármi más | `PageInfo.dc.html` | 404 |

**Az oldalon nincs belépés** – nincs fiók, jelszó, „Ügyfélportál”. Az elnevezés mindenhol **„Igénylés”** (nem „Regisztráció”, mert az fiókot sugall).

**Megszűnt útvonalak** (a prototípusban átirányítanak, élesben 301-es átirányítás kell):

| Régi | Új |
|---|---|
| `/ugyfelportal…`, `/visszaterites/ugyintezes`, `/visszaterites/regisztracio` | `/visszaterites/igenyles` |
| `/visszaterites/tajekoztato` | `/visszaterites` |
| `/visszaterites/ellenorzok` | `/ellenorzo` |
| `/visszaterites/gyik` | `/gyik#visszaterites` |
| `/a-maganmasolasi-dijrol/gyik-kereskedoknek` | `/gyik#dijfizetes` |
| `/a-maganmasolasi-dijrol/mit-masolhatok-szabadon` | `/a-maganmasolasi-dijrol#mit-masolhatok` |

**Szerkezeti elvek:**
- **Nincs ismétlődés, nincs körbe mutató link.** Egy szöveg csak egy oldalon szerepel teljes terjedelmében; az oldalak alján nincsenek testvéroldalakra visszamutató „Tovább” kártyák. A nyitóoldalon minden célra egy belépési pont mutat (a fejléc- és lábléc-menü kivételével).
- **Kattintható vs. információ – vizuálisan elkülönítve:**
  - **Kattintható felület: halványkék** (`oklch(0.95 0.02 262)`) kártya/csempe, hover-re `translateY(-2px)`; a jobb szélen nyíl.
  - **Nem kattintható információ: fehér** doboz 1px hairline kerettel (`oklch(0.9 0.01 262)`), hover nélkül.
  - Kivétel a navy kiemelő blokk (pl. 25% NKA) – ez sem kattintható, statisztikai kiemelés.
- **Egységes info-ikon** (minden nem kattintható listaelemen, pipa / X / i): 24px kör, halványkék háttér, 12px navy monoline ikon (2.6px). Szándékosan visszafogott, hogy ne versenyezzen a kattintható elemekkel.
- **Link-ikonok:** belső oldalra `→`, külső oldalra vagy PDF-re `↗`. **Minden PDF és külső oldal új lapon nyílik** (`target="_blank" rel="noopener"`).
- **Linklisták** (pl. „Kinek szól?” kártyák): elválasztó vonal csak az elemek között – az első elem felett és az utolsó alatt nincs.
- **Oldalsáv csak ott, ahol több testvéroldal vagy hosszú, tagolt tartalom van** (Díjak sorsa, A magánmásolási díjról, adatvédelmi tájékoztató); „Ebben a részben” felirat nélkül. A Visszatérítés, az Igénylés, a GYIK és az Ellenőrző egyoszlopos (max. 860px).

- `Weboldal.dc.html` – **belépési pont / shell**: fejléc, kliensoldali router, lábléc, süti-hozzájárulás sáv, toast. Ezt nyisd meg a teljes prototípushoz.
- `Allapotok.dc.html` – az összes visszajelzés-állapot egy lapon + tesztadatok az előidézésükhöz.

## Globális elrendezés

- **Konténer:** `max-width: 1200px; margin: 0 auto; padding: 0 28px`; egyoszlopos oldalakon `max-width: 860px`.
- **Fejléc (sticky, z 40):** háttér navy, padding 16px 28px. Bal: logó (34px bakelit-kör + „Magánmásolási díj” Source Serif 4 700 22px). Jobb: fő navigáció – A magánmásolási díjról · A befizetett díjak sorsa · Visszatérítés · Gyakori kérdések · Kapcsolat (15px/500, gap 18px, aktív: mustár szín + 2px mustár alsó vonal; az Igénylés oldalon a „Visszatérítés” aktív). **Nincs külön „Igénylés indítása” gomb a fejlécben** – az a Visszatérítés oldal végén van, hogy a két belépési pont ne ismételje egymást. Skip link: „Ugrás a tartalomra”.
- **Aloldal-hero:** navy sáv, jobb felső sarokban barázdás kör dekor (`repeating-radial-gradient`, 6% fehér). Morzsamenü (14px), H1 (Source Serif 4 700, `clamp(34px,4.4vw,52px)`, lh 1.08), lead (19px, `oklch(0.88 0.03 262)`). Padding 40px 28px 56px.
- **Oldalsávos törzs:** flex-wrap, gap 48px. Bal oldalsáv (`flex:1 1 220px; max-width:280px; position:sticky; top:96px`), aktív elem navy háttér + fehér szöveg, radius 10px. Tartalom: `flex:999 1 520px; max-width:780px`.
- **Lábléc:** sötét navy; 4 oszlop – Ügyfélszolgálat · Telefonos ügyfélfogadás · Tájékoztatás (A díjról, Mit másolhatok szabadon?, Jogosulti csoportok, Kulturális célok, Gyakori kérdések) · Ügyintézés (Visszatérítés feltételei, Igénylés indítása, Díjfizetés ellenőrzése, Kapcsolat); GVH-szöveg + PDF (új lapon), impresszum, Adatvédelmi / Süti-tájékoztató / Süti-beállítások.

## Nyitóoldal (`/`)
1. **Hero** (navy): bal – eyebrow, H1 „A szabad magáncélú másolás lehetőségéért fizetendő díj” (utolsó szó mustár), lead, 1 gomb („Mi a magánmásolási díj?”). Jobb – **hanglemez-kompozíció**: mustár lemezborító (72% szélesség, aspect 1:1, radius 6px, árnyék `0 30px 60px rgba(0,0,0,.35)`) a hanghordozós felosztással (45/30/25%, navy sávok), mögötte jobbra kilógó bakelit (barázdák: `repeating-radial-gradient(circle,#12151e 0 2px,#252b3b 2px 3.2px)`), mustár címke körbefutó felirattal („ARTISJUS · EJI · FILMJUS · HUNGART · MAHASZ ·”, SVG textPath), statikus fényes conic-gradient réteg. **A lemez 7 s alatt fordul körbe, lineárisan, végtelenítve**; `prefers-reduced-motion` esetén álljon meg (production-ben add hozzá).
2. **„Megfizették a díjat?”** kártya a hero aljára csúsztatva (`margin-top:-44px`), fehér, radius 14px, árnyék `0 18px 40px rgba(30,35,70,.12)`; IMEI és PCSN link-csempe (halványkék) → `/ellenorzo#imei`, `/ellenorzo#pcsn`.
3. **Kinek szól?** 3 halványkék kártya (a kártya maga nem kattintható, nincs hover-emelés; a benne lévő linklista az): cím, leírás, linklista.
   - Fizetőknek: Díjszabás (`/a-maganmasolasi-dijrol#dijszabas`), Gyakori kérdések a díjfizetésről (`/gyik#dijfizetes`)
   - Visszatérítés: Feltételek és menete, Igénylés indítása, Gyakori kérdések a visszatérítésről (`/gyik#visszaterites`)
   - Kapcsolat: Írj nekünk, A portál üzemeltetői
4. **A díjról** (fehér szekció): rövid magyarázat; „szabadon másolható” lista és a kivétel-doboz **fehér info-dobozként** (egységes info-ikon); alatta egy link: „A kivételek részletesen →”.
5. **Díjak sorsa:** bal – magyarázat + 25% NKA navy blokk; jobb – „Hová kerül a díj? Nézd meg részletesen:” + két halványkék, leírással ellátott csempe (Jogosulti csoportok, Kulturális célok). A jogkezelők külső linkjei nem a nyitóoldalon, hanem a Jogosulti csoportok oldalon vannak.

## Interakciók és viselkedés
- **Navigáció:** belső linkek valódi `href`-fel, a prototípusban kliensoldali router (`nav(path)`); horgony esetén 90px offsettel görget (sticky fejléc). Oldalváltáskor lap tetejére ugrik.
- **Oldalankénti cím a prototípusban:** minden oldalnak saját, megosztható címe van hash-útvonalként – pl. `Weboldal.dc.html#/visszaterites`, `#/gyik#visszaterites`, `#/kapcsolat#uzemeltetok`. A böngésző Vissza/Előre gombja működik, a fül címe oldalanként változik („Visszatérítés – Magánmásolási díj”), a megszűnt címek az újra cserélődnek. WordPressben ugyanezek valódi URL-ek lesznek (`/visszaterites`, `/gyik#visszaterites`), az oldalcím pedig a SEO-bővítményből jön.
- **Tartalomjegyzék scrollspy** (`/a-maganmasolasi-dijrol`): a sticky bal oldali listában mindig az a szakasz aktív (navy), amelynek a címe már a fejléc alá ért; kattintásra 90px offsettel görget.
- **GYIK** (`/gyik`): két fül (Díjfizetés / Visszatérítés), a horgony választja ki a fület (`#dijfizetes`, `#visszaterites`) vagy nyit ki egy kérdést (`#d1…d15`, `#v1…v11`). Harmonika: egyszerre egy nyitott elem (első alapból nyitva); `+`/`−` kör ikon (nyitva navy). A kereső **mindkét kategóriában** keres, találatnál a kérdés fölött a kategória neve; számláló, üres állapot „Nincs találat” + „Keresés törlése”.
- **Hover:** csak kattintható felületen – csempe `translateY(-2px)` 200ms; linkek sötétebb kék + aláhúzás; pill gombok világosabb árnyalat.
- **Megjelenés:** visszajelzések `mmdin` animációval (opacity 0→1, translateY 8px→0, 250–300ms, `cubic-bezier(.22,.61,.36,1)`).
- **Toast:** jobb alul, navy, zöld pipás kör, 2,8 s után eltűnik (süti-mentés után). WordPressben elhagyható, ha a süti-bővítmény nem ad visszajelzést.
- **Süti-sáv:** első látogatáskor alul középen (max 760px). „Összes elfogadása” / „Csak a szükségesek” / „Beállítások” (kinyitja: Szükséges – mindig aktív, Statisztikai – kapcsolható, „Kiválasztottak mentése”). A láblécből és a Süti-tájékoztatóból újranyitható. Tárolás: `mmd-cookie` = `all` | `needed`. WordPressben a süti-bővítmény sávját kell erre a designra stílusozni.

## Űrlapok, validáció, állapotok
Beküldéskor (illetve lépésváltáskor) validál, nem gépelés közben; gépeléskor az adott mező hibája törlődik. Hibás mező: 1.5px piros keret, halvány piros háttér, `aria-invalid`, alatta félkövér piros üzenet. Az űrlap felett összesítő riasztás („N mezőt kell javítanod”).

**Igénylés** (`/visszaterites/igenyles`, belépés nélkül) – **többlépéses űrlap**, a Gravity Forms többoldalas űrlapjának mintájára, oldalsáv nélkül:
- Az 1. lépés felett **„Mire lesz szükséged a kitöltéshez?”** fehér info-doboz (4 tétel, egységes info-ikon).
- **Lépésjelző** (fehér kártya az űrlap felett): „N. lépés / 5” eyebrow + „X% kész”, 6px-es navy folyamatsáv, alatta a lépések listája. Kész lépés: navy kör fehér pipával; aktuális: mustár kör navy számmal, halvány mustár gyűrű, félkövér címke; hátralévő: halványkék kör, szürke címke. `aria-current="step"` az aktuálison.
- **Lépések:**
  1. Igénylő adatai: Teljes név*, E-mail*, Telefonszám, Lakcím*
  2. Alkotói tevékenység: Alkotói tevékenység* (select)
  3. A hordozó adatai: Hordozó típusa* (select), Darabszám* (≥1), Vásárlás dátuma* (**csak aktuális év**), Eladó neve*, Hologramos címke sorszáma / IMEI* (6–20 karakter, `[A-Za-z0-9-]`)
  4. Tárolt tartalom: leírás* (textarea)
  5. Nyilatkozatok: saját professzionális tartalom*, nem használom magánmásolásra*, társszerzői nyilatkozat, adatvédelem*
- **Gombsor** (az űrlapkártya alján, hairline felett): „← Vissza” (másodlagos, 2. lépéstől; nem validál, az adatok megmaradnak) · „Tovább →” / az utolsó lépésen „Igénylés elküldése” (elsődleges) · jobbra igazítva „Mentés és folytatás később” szöveges gomb (2. lépéstől, amikor már van e-mail-cím).
- **Validáció:** a „Tovább →” csak az aktuális lépés mezőit ellenőrzi; hiba esetén a lépésen marad, összesítő riasztás + mezőhibák.
- **Állapotok:** idle → sending (gomb letiltva, spinner, „Küldés…”) → **ok** („Igénylésedet rögzítettük”, azonosító `MMD-ÉÉÉÉ-NNNNN`, „Hogyan tovább?” 3 lépés, PDF-összesítő, Új igénylés) | **fail** (riasztás „Vissza a hordozó adataihoz →” linkkel, ami a 3. lépésre ugrik és a hibás mezőt jelöli) | **mentve** (halványkék riasztás: „Kitöltés elmentve – a folytatáshoz szükséges linket elküldtük a(z) … címre. A link 30 napig érvényes”).
- **Megjegyzés:** a kérdéslista egyelőre az előzetes bejelentőlap ismert mezőiből áll; a teljes kérdéssort az ügyféltől kell bekérni. A lépésszerkezet tetszőleges számú lépésre és mezőre skálázódik; egy lépésben lehetőleg legfeljebb 6–8 kérdés legyen.

**Ellenőrző** (`/ellenorzo`): **független az igényléstől** – arra szolgál, hogy bárki (vásárló, kereskedő) meggyőződjön a díj megfizetéséről; ezért nem a Visszatérítés alatt van, hanem saját oldalon, a nyitóoldali „Megfizették a díjat?” kártyáról, a lábléc „Ügyintézés” oszlopából és a díj oldal „Díjszabás és igazolás” szakaszából érhető el. Fül IMEI / PCSN. IMEI = pontosan 15 számjegy; SN = 6–24 karakter. Eredmény: **Regisztrált státuszú** (zöld) / **Nem található** (mustár, link az ügyfélszolgálathoz) / **Rendszer nem érhető el** (piros, Újrapróbálás). Élesben az Artisjus IMEI (imei.artisjus.com) és PCSN (artisjus.hu) rendszerének API-ját kell bekötni – egyeztetendő; ha nincs API, maradjon külső link.

**Kapcsolat** (`/kapcsolat`): az **üzenetküldő űrlap az elsődleges elem** (bal, szélesebb oszlop) – név, e-mail, téma (select), üzenet (≥20 karakter) → „Köszönjük, üzenetedet megkaptuk” | hálózati hiba riasztás. Jobb oldalon fehér info-dobozok, egységes 17px/700 címsorral: Ügyfélszolgálat (cím, telefon, e-mail) · Telefonos ügyfélfogadás (alatta kis infósor: személyes ügyintézés időpontfoglalás után, telefonon vagy e-mailben egyeztetve) · A portál üzemeltetői (az öt szervezet neve **link nélkül**, a GVH-döntés PDF új lapon).

A prototípusbeli tesztbemeneteket (pl. „0000” címke, „999” ellenőrző-kód) az `Allapotok.dc.html` 6. szakasza sorolja fel – ezek csak a demo miatt vannak, élesben a backend válaszai vezérlik.

## State Management
- Shell: `route`, `hash`, `cookie` (consent), `cookieOpen`, `cookieDetail`, `analytics`, `toast`. Prototípusban localStorage: `mmd-route`, `mmd-cookie`.
- Igénylés: `step`, `v` (értékek), `c` (nyilatkozatok), `errors`, `cerr`, `status` (`idle | sending | ok | fail`), `saved`.
- Kapcsolati űrlap: `values`, `errors`, `status`.
- GYIK: `tab` (`d | v`), `open` index, `query`.
- Ellenőrző: `tab`, `code`, `codeErr`, `checking`, `res` (`ok | no | err`).
- Díj oldal: `active` (scrollspy).

## Design Tokens
Színek (oklch, zárójelben közelítő hex – a WordPress színpalettájába a hex értékek kerüljenek):
- Navy (elsődleges, fejléc, gombok): `oklch(0.28 0.08 262)` (~#1B2A55)
- Sötét navy (lábléc): `oklch(0.22 0.07 262)` (~#111D40)
- Mustár (kiemelés, CTA): `oklch(0.85 0.14 85)` (~#EBC04A); hover `oklch(0.9 0.12 85)`; halvány `oklch(0.96 0.045 85)`
- Link kék: `oklch(0.42 0.12 255)` (~#2856A0); hover `oklch(0.32 0.12 255)`
- Halványkék felület – **kattintható elemek**: `oklch(0.95 0.02 262)` (~#E9EDF6); még halványabb `oklch(0.97 0.012 262)`
- Oldalháttér: `#F6F5F1`; kártya / **info-doboz**: `#FFFFFF` + hairline keret
- Szöveg: `oklch(0.24 0.04 262)`; másodlagos `oklch(0.36–0.45 0.03 262)`; hairline `oklch(0.9 0.01 262)`, input keret `oklch(0.86 0.015 262)`
- Siker: háttér `oklch(0.95 0.04 150)`, ikon `oklch(0.5 0.12 150)`, szöveg `oklch(0.35 0.1 150)`
- Hiba: háttér `oklch(0.95 0.035 27)`, ikon/keret `oklch(0.55 0.2 27)`, szöveg `oklch(0.42–0.5 0.16–0.19 27)`
- Figyelmeztetés: háttér `oklch(0.96 0.05 85)`, ikon `oklch(0.72 0.14 75)`, szöveg `oklch(0.4 0.08 70)`

Tipográfia:
- Címsor: **Source Serif 4** 700 (H1 `clamp(34px,4.4vw,52px)`, nyitó H1 `clamp(40px,5.2vw,64px)`, H2 `clamp(28px,3.2vw,40px)` / 26–30px, kártyacím 21–26px)
- Szöveg/UI: **Source Sans 3** 400/500/600/700 – törzs 17px/1.6, lead 19px, kicsi 15px, info-doboz címe 17px/700, címke 14–15px/600, eyebrow 13px/700 uppercase .07em
- Kód/azonosítók: `ui-monospace`

Térköz: szekciók 80–88px függőleges padding, kártyák 22–32px, rács gap 10–20px, oldalsáv–tartalom 48px.
Radius: 6px (lemezborító), 8–12px (input, kis kártya), 14–16px (nagy kártya), 999px (gombok, pillek, fülek).
Árnyék: kártya `0 1px 2px rgba(30,35,70,.06)`; lebegő `0 18px 40px rgba(30,35,70,.12)`; süti-sáv `0 20px 50px rgba(20,25,60,.25)`.

## Assets
- Nincs raszterkép. A bakelit, barázda-dekor és logó CSS-gradiensekből készül; ikonok inline SVG (monoline, 2px, round cap).
- A jogkezelők logói (a jelenlegi oldalon: `/sites/default/files/media/images/*-logo.*`) a Jogosulti csoportok oldalra beemelhetők.
- PDF-ek: `/media/6/download` (visszatérítési tájékoztató), `/media/7/download` (GVH végzés) – WordPressben a Médiatárba kerülnek, a régi URL-ekről átirányítás kell.
- Díjszabás (Artisjus, külső): `https://www.artisjus.hu/wp-content/uploads/2025/12/U26.pdf` (Ü) és `https://www.artisjus.hu/wp-content/uploads/2025/12/U_PC_26.pdf` (Ü-PC), 2026. január 1-től. **Évente frissíteni kell** – a link szerkeszthető mező legyen.

## WordPress-megvalósítás

### Téma és design tokenek
- Egyedi téma (blokktéma vagy klasszikus téma + ACF-blokkok – a fejlesztő döntése). A színek, betűk, betűméretek és térközök a `theme.json`-ba kerüljenek (`settings.color.palette`, `typography.fontFamilies` / `fontSizes`, `spacing.spacingSizes`), hex értékekkel; a szerkesztőben csak ez a paletta legyen elérhető (`custom: false`).
- **Betűtípusok helyben**: a Source Serif 4 és a Source Sans 3 a témából töltődjön be (`theme.json` `fontFace`, woff2), **ne a Google Fonts CDN-ről** – az EU-ban a távoli betöltés GDPR-kockázat.
- A prototípus inline style-jai helyett komponens-szintű CSS (blokkonként), BEM vagy blokk-osztályokkal. A „kattintható = halványkék, információ = fehér” szabály két CSS-osztály legyen (pl. `.is-link-surface`, `.is-info-surface`), hogy a szerkesztők ne keverhessék.

### Oldalszerkezet és menük
- Minden útvonal valódi WP-oldal, a hierarchia a szülő–gyermek oldalakból jön (pl. Visszatérítés → Igénylés). A kliensoldali router megszűnik.
- **Fő menü**, **lábléc „Tájékoztatás”** és **lábléc „Ügyintézés”** oszlop: WP-menük (Megjelenés → Menük / navigációs blokk). Aktív állapot a menü `current-menu-item` / `current-menu-ancestor` osztályaiból.
- **Oldalsáv:** a Díjak sorsa szekcióban a szülőoldal gyermekoldalaiból; a díj oldalon és az adatvédelmi tájékoztatón a tartalom H2 címsoraiból (tartalomjegyzék + scrollspy). Felirat nélkül, sticky.
- **Morzsamenü**: Yoast SEO vagy Rank Math breadcrumbs, a design szerinti markuppal.
- **Mobil**: ~900px alatt hamburgermenü (a prototípusban még nincs megtervezve – a fejlesztés előtt pótolni kell); az oldalsáv mobilon a tartalom fölé kerül, összecsukható menüként.

### Blokk-leltár (ACF- vagy Gutenberg-blokkok)
Minden ismétlődő elem legyen szerkeszthető blokk, **tetszőleges elemszámmal** (a 3 kártya, 4 lépés stb. csak a mostani tartalom):

| Blokk | Hol látszik | Mezők |
|---|---|---|
| Aloldal-hero | minden aloldal | H1, lead (morzsamenü automatikus) |
| Nyitóoldali hero + bakelit | `/` | H1 (kiemelt szóval), lead, 1 gomb; a bakelit **fix sablonelem**, nem szerkeszthető |
| Link-kártya rács | „Kinek szól?” | ismétlő: cím, leírás, linklista (belső/külső → automatikus ikon és `target`) |
| Link-csempe | „Megfizették a díjat?”, „Hová kerül a díj?” | ismétlő: cím, leírás, (ikon), link |
| Lépéssor | Visszatérítés | ismétlő: cím, leírás |
| Info-lista | „Szabadon másolható”, kivételek, „Mire lesz szükséged?” | ismétlő: szöveg, ikon (pipa / X / i) – mindig az egységes info-ikonnal |
| PDF-kártya | Visszatérítés, Díjszabás | cím, alcím, fájl vagy URL (új lapon nyílik) |
| CTA-sáv (navy) | Visszatérítés vége | cím, alcím, gomb |
| Riasztás / infó doboz | bárhol | típus (siker / hiba / figyelmeztetés / infó), cím, szöveg |
| GYIK harmonika + kereső | `/gyik` | a GYIK tartalomtípusból, kategória-fülekkel |
| Felosztási arány-sáv | Jogosulti csoportok | ismétlő: címke, százalék, szín |
| Jogkezelő lista | Jogosulti csoportok | a Jogkezelők tartalomtípusból |
| Ügyfélszolgálat-adatok | Kapcsolat, lábléc | témabeállításokból (telefon, e-mail, nyitvatartás – egy helyen szerkeszthető) |

- **Kiemelt szó a címben** (mustár „díj”): egyedi formázás a blokkszerkesztőben (pl. „Kiemelés” formátum → `<mark class="is-accent">`).

### Tartalomtípusok
- **GYIK** (egyedi tartalomtípus): kérdés, válasz (rich text), kategória (`dijfizetes` / `visszaterites`), sorrend; a mélylink `id` a slugból. FAQ schema a SEO-bővítményből. Egy kérdés csak egy kategóriában szerepelhet.
- **Jogkezelők**: név, kinek a jogait kezeli, honlap, szabályzat URL, logó, szín.
- Az **Adatvédelmi tájékoztató** sima oldal; a tartalomjegyzék a címsorokból generálódjon (tartalomjegyzék-blokk).

### Űrlapok – Gravity Forms
- **Igénylés**: Gravity Forms többoldalas űrlap (Page Break mezők), lépésjelző stílusa: „Steps” – a design szerinti lépésjelzőre stílusozva. Validáció: oldalanként, beküldéskor (a GF alapviselkedése). A „Validation Summary” beállítás adja az összesítő riasztást.
- **Mentés és folytatás később**: GF „Save and Continue” funkció; a folytató link e-mailben, 30 napos lejárattal (GF alapérték).
- **Egyedi fejlesztés kell:**
  - a vásárlás dátumának aktuális évre korlátozása és a címkesorszám formátuma (`gform_field_validation` hook);
  - az `MMD-ÉÉÉÉ-NNNNN` formátumú azonosító (egyedi merge tag vagy rejtett mező, a beküldéskor generálva) a visszaigazoló oldalon és az e-mailben;
  - a címkesorszám ellenőrzése az Artisjus nyilvántartásában, ha erre lesz API (hiba esetén a 3. lépésre visszavezető üzenet).
- **PDF-összesítő**: Gravity PDF bővítmény, a design színeivel.
- **Visszaigazoló képernyő**: GF Confirmation (szöveg típus) a design „Igénylésedet rögzítettük” blokkjának markupjával.
- Hibás mező, összesítő riasztás, gombok: a GF kimenetét (`.gfield_error`, `.gform_validation_errors`, `.gform_next_button` stb.) kell a design szerint stílusozni – ne saját markupot építsetek.
- **Kapcsolati űrlap**: szintén Gravity Forms (egyoldalas).
- Adatkezelés: a beküldött bejegyzések tárolási ideje és törlése (GF „Personal Data” beállítás) – egyeztetendő az adatvédelmi tájékoztatóval.

### Süti-hozzájárulás
- Bővítmény (pl. Complianz vagy CookieYes): kategóriák Szükséges + Statisztikai; a Google Analytics csak hozzájárulás után töltődjön be (Consent Mode v2). A sáv megjelenését a design szerint kell stílusozni; a „Süti-beállítások” link a láblécben és a Süti-tájékoztatón a bővítmény újranyitó függvényét hívja. A sütitáblázat a bővítményből generálható.

### Ellenőrző (IMEI / PCSN)
- Ha van API: kis egyedi bővítmény (REST végpont + a design szerinti űrlap blokk), a három eredményállapottal. Ha nincs: a blokk külső linkként jelenjen meg (ugyanebben a kártyában, új lapon).

### Migráció, SEO, akadálymentesség
- 301-es átirányítások a régi Drupal-útvonalakról, horgonyokról és PDF-linkekről, valamint a fenti „Megszűnt útvonalak” táblázat szerint (Redirection bővítmény vagy szerveroldali szabályok).
- SEO-bővítmény (Yoast / Rank Math): meta, breadcrumbs, FAQ schema, XML sitemap.
- Akadálymentesség: WCAG 2.1 AA (kontrasztok, fókuszállapotok, `aria-current` a menüben, a tartalomjegyzékben és a lépésjelzőn, űrlaphibák `aria-describedby`-jal). Új lapon nyíló linkeknél jelezni kell ezt a képernyőolvasónak (pl. vizuálisan rejtett „(új lapon nyílik)” szöveg).
- A forgó bakelit `prefers-reduced-motion` esetén álljon meg.

## Nyitott pontok
- **Az igénylés teljes kérdéslistája** – az ügyféltől kell bekérni; ennek alapján véglegesíthető a lépések száma és tartalma.
- **Mobil hamburgermenü** megtervezése.
- **Személyes ügyintézés:** ha az ügyfél online időpontfoglalást szeretne, a Kapcsolat oldal infósora helyére foglalási modul kerül (pl. Amelia / Bookly) – most csak infó.
- Adatvédelmi tájékoztató: a prototípus csak a szerkezetet és rövid összefoglalót mutatja; a teljes jogi szöveget a jelenlegi oldalról kell átemelni.
- Sütitáblázat nevei/időtartamai – a WordPress + süti-bővítmény + GA beállítás alapján újra kell gyűjteni.
- Az eredeti oldal „kilenc alprogramról” ír, de csak nyolcat sorol fel – tisztázandó.
- IMEI/PCSN API elérhetősége – egyeztetendő az Artisjusszal.
- Angol nyelvű verzió (WPML / Polylang): ügyféldöntés.

## Files
- `Weboldal.dc.html` – shell, router (belépési pont)
- `PageHome.dc.html`, `PageDij.dc.html`, `PageSorsa.dc.html`, `PageVissza.dc.html`, `PageIgenyles.dc.html`, `PageGyik.dc.html`, `PageEllenorzo.dc.html`, `PageInfo.dc.html`
- `Allapotok.dc.html` – állapotgaléria
- `support.js` – a `.dc.html` fájlok futtatókörnyezete (csak a prototípus megnyitásához)
