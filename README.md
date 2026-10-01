# Handoff: maganmasolasidij.hu – teljes webhely újratervezés

## Overview
Az Artisjus és további 4 közös jogkezelő (EJI, FILMJUS, HUNGART, MAHASZ) tájékoztató portáljának újratervezése. A jelenlegi egyoldalas (one-page, horgonyos) Drupal 10 oldal helyett önálló, indexelhető aloldalak, belépés nélküli, lépésenkénti regisztrációs kérdőív és teljes visszajelzés-rendszer (siker / hiba / figyelmeztetés / töltés / üres állapot).

**A célplatform WordPress** – a megvalósítás részleteit a [WordPress-megvalósítás](#wordpress-megvalósítás) fejezet írja le.

## About the Design Files
A csomagban lévő fájlok **HTML-ben készült design-referenciák** (működő prototípus), nem közvetlenül átvehető production kód. A feladat ezeknek a designoknak az **újraépítése WordPressben** (egyedi blokktéma vagy klasszikus téma + ACF-blokkok, Gravity Forms), annak bevett mintáival és bővítményeivel.

A `.dc.html` fájlok helyi webszerverről nyithatók meg (a `support.js` futtatja őket, `file://`-ról nem töltenek be). Minden fájl egy sablont (HTML + inline style, `{{ }}` helyőrzők, `<sc-for>` / `<sc-if>` vezérlés) és egy `class Component` logikai osztályt tartalmaz – a logikában található a **teljes szöveges tartalom**, a validáció és az állapotgép.

## Fidelity
**High-fidelity.** Végleges színek, tipográfia, térközök, interakciók és szövegek. Pixelpontos újraépítés az elvárás – kivéve, ahol a WordPress-fejezet bővítményre (Gravity Forms, süti-bővítmény) bízza a markupot: ott a bővítmény kimenetét kell a designhoz stílusozni.

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
| `/visszaterites` | `PageVissza.dc.html` | Feltételek: teljes tájékoztató szöveg + PDF + az ügyintézés 4 lépése + CTA |
| `/visszaterites/regisztracio` | `PageRegisztracio.dc.html` | lépésenkénti regisztrációs kérdőív (belépés nélkül), az 1. lépés felett „Mire lesz szükséged?” |
| `/visszaterites/ellenorzok` | `PageVissza.dc.html` | IMEI / PCSN ellenőrző |
| `/visszaterites/gyik` | `PageVissza.dc.html` | 16 kérdés, kereső |
| `/kapcsolat` | `PageInfo.dc.html` | elérhetőség, üzemeltetők (`#uzemeltetok`), kapcsolati űrlap |
| `/adatvedelmi-tajekoztato` | `PageInfo.dc.html` | tartalomjegyzékes dokumentum |
| `/suti-tajekoztato` | `PageInfo.dc.html` | sütitáblázat + beállítás gomb |
| bármi más | `PageInfo.dc.html` | 404 |

**Az oldalon nincs belépés.** Nincs fiók, jelszó, bejelentkezés, „Igényléseim” vagy kijelentkezés, és nincs külön „Ügyfélportál” sem. A regisztráció a Visszatérítés szekció része: belépés nélkül kitölthető kérdőív, amelynek beküldése után az igénylő azonosítót és visszaigazoló e-mailt kap. A megszűnt útvonalak a prototípusban átirányítanak – élesben 301-es átirányítás kell: `/ugyfelportal…` és `/visszaterites/ugyintezes` → `/visszaterites/regisztracio`; `/visszaterites/tajekoztato` → `/visszaterites`.

**Szerkezeti elv – nincs ismétlődés, nincs körbe mutató link:**
- Egy szekción belül az **oldalsáv az egyetlen navigáció**; az oldalak alján nincsenek „Tovább” kártyák, amelyek a testvéroldalakra visszamutatnának.
- Egy szöveg (bekezdés, lista, lépéssor) **csak egy oldalon** szerepel teljes terjedelmében; máshol legfeljebb egymondatos összefoglaló + link.
- A nyitóoldalon minden célra **egy belépési pont** mutat (a fejléc- és lábléc-menü kivételével): ellenőrzők → „Megfizették a díjat?” kártya; díj, visszatérítés, kapcsolat → „Kinek szól?” kártyák; jogosultak, NKA → Díjak sorsa szekció.
- A lépéssor egyetlen helye a `/visszaterites` oldal; a regisztráció utáni teendők a sikeres beküldés képernyőjén jelennek meg.

- `Weboldal.dc.html` – **belépési pont / shell**: fejléc, kliensoldali router, lábléc, süti-hozzájárulás sáv, toast. Ezt nyisd meg a teljes prototípushoz.
- `Allapotok.dc.html` – az összes visszajelzés-állapot egy lapon + tesztadatok az előidézésükhöz.

## Globális elrendezés

- **Konténer:** `max-width: 1200px; margin: 0 auto; padding: 0 28px`.
- **Fejléc (sticky, z 40):** háttér navy, padding 16px 28px. Bal: logó (34px bakelit-kör + „Magánmásolási díj” Source Serif 4 700 22px). Jobb: fő navigáció (15px/500, gap 18px, aktív: mustár szín + 2px mustár alsó vonal; a Regisztráció oldalon a „Visszatérítés” aktív) + **„Igénylés indítása”** pill gomb (mustár, dokumentum ikon) → `/visszaterites/regisztracio`. Skip link: „Ugrás a tartalomra”.
- **Aloldal-hero:** navy sáv, jobb felső sarokban barázdás kör dekor (`repeating-radial-gradient`, 6% fehér). Morzsamenü (14px), eyebrow (13px/700, uppercase, .07em, mustár), H1 (Source Serif 4 700, `clamp(34px,4.4vw,52px)`, lh 1.08), lead (19px, `oklch(0.88 0.03 262)`). Padding 40px 28px 56px.
- **Aloldal-törzs:** flex-wrap, gap 48px. Bal oldalsáv (`flex:1 1 220px; max-width:280px; position:sticky; top:96px`) – „Ebben a részben” almenü, aktív elem navy háttér + fehér szöveg, radius 10px. Tartalom: `flex:999 1 520px; max-width:780px`. Az oldalak alján nincs „Tovább” kártya – a szekción belüli navigáció kizárólag az oldalsáv.
- **Lábléc:** sötét navy; 4 oszlop (Ügyfélszolgálat, Ügyfélfogadás, Tájékoztatás linkek, Visszatérítés linkek), GVH-szöveg + PDF, impresszum, Adatvédelmi / Süti-tájékoztató / Süti-beállítások.

## Nyitóoldal (`/`)
1. **Hero** (navy): bal – eyebrow, H1 „A szabad magáncélú másolás lehetőségéért fizetendő díj” (utolsó szó mustár), lead, 1 gomb („Mi a magánmásolási díj?”). Jobb – **hanglemez-kompozíció**: mustár lemezborító (72% szélesség, aspect 1:1, radius 6px, árnyék `0 30px 60px rgba(0,0,0,.35)`) a hanghordozós felosztással (45/30/25%, navy sávok), mögötte jobbra kilógó bakelit (barázdák: `repeating-radial-gradient(circle,#12151e 0 2px,#252b3b 2px 3.2px)`), mustár címke körbefutó felirattal („ARTISJUS · EJI · FILMJUS · HUNGART · MAHASZ ·”, SVG textPath), statikus fényes conic-gradient réteg. **A lemez 7 s alatt fordul körbe, lineárisan, végtelenítve**; `prefers-reduced-motion` esetén álljon meg (production-ben add hozzá).
2. **„Megfizették a díjat?”** kártya a hero aljára csúsztatva (`margin-top:-44px`), fehér, radius 14px, árnyék `0 18px 40px rgba(30,35,70,.12)`; IMEI és PCSN link-kártya.
3. **Kinek szól?** 3 kártya, halványkék háttér, számozás nélkül: cím, leírás, linklista.
   - Fizetőknek: Díjszabás (Artisjus), Gyakori kérdések kereskedőknek
   - Visszatérítés: Feltételek, Regisztráció, Gyakori kérdések
   - Kapcsolat: Ügyfélszolgálat, A portál üzemeltetői
4. **A díjról** (rövid magyarázat + „szabadon másolható” kártyák + kivételek doboz „Mit másolhatok szabadon? →” linkkel), **Díjak sorsa** (25% NKA navy blokk, 5 jogkezelő lista, linkek a Jogosulti csoportok és a Kulturális célok oldalra).
5. A korábbi Visszatérítés és GYIK szekció megszűnt – mindkettőt a „Kinek szól?” kártyák vezetik be.

## Interakciók és viselkedés
- **Navigáció:** belső linkek valódi `href`-fel, a prototípusban kliensoldali router (`nav(path)`); horgony (`/kapcsolat#uzemeltetok`) esetén 90px offsettel görget (sticky fejléc). Oldalváltáskor lap tetejére ugrik.
- **GYIK harmonika:** egyszerre egy nyitott elem (első alapból nyitva); `+`/`−` kör ikon (nyitva navy). Keresés: kérdés + válasz szövegében, találatok automatikusan nyitva, számláló („3 találat”), üres állapot „Nincs találat” + „Keresés törlése”. Minden kérdésnek van `id`-je (`k1…k11`, `v1…v16`) mélylinkhez.
- **Hover:** kártyák `translateY(-2px/-3px)` 200–250ms; linkek sötétebb kék + aláhúzás; pill gombok világosabb árnyalat.
- **Megjelenés:** visszajelzések `mmdin` animációval (opacity 0→1, translateY 8px→0, 250–300ms, `cubic-bezier(.22,.61,.36,1)`).
- **Toast:** jobb alul, navy, zöld pipás kör, 2,8 s után eltűnik (süti-mentés után). WordPressben elhagyható, ha a süti-bővítmény nem ad visszajelzést.
- **Süti-sáv:** első látogatáskor alul középen (max 760px). „Összes elfogadása” / „Csak a szükségesek” / „Beállítások” (kinyitja: Szükséges – mindig aktív, Statisztikai – kapcsolható, „Kiválasztottak mentése”). A láblécből és a Süti-tájékoztatóból újranyitható. Tárolás: `mmd-cookie` = `all` | `needed`. WordPressben a süti-bővítmény sávját kell erre a designra stílusozni.

## Űrlapok, validáció, állapotok
Beküldéskor (illetve lépésváltáskor) validál, nem gépelés közben; gépeléskor az adott mező hibája törlődik. Hibás mező: 1.5px piros keret, halvány piros háttér, `aria-invalid`, alatta félkövér piros üzenet. Az űrlap felett összesítő riasztás („N mezőt kell javítanod”).

**Regisztrációs kérdőív** (`/visszaterites/regisztracio`, belépés nélkül) – **többlépéses űrlap**, a Gravity Forms többoldalas űrlapjának mintájára:
- **Lépésjelző** (fehér kártya az űrlap felett): „N. lépés / 5” eyebrow + „X% kész”, 6px-es navy folyamatsáv, alatta a lépések listája. Kész lépés: navy kör fehér pipával; aktuális: mustár kör navy számmal, halvány mustár gyűrű, félkövér címke; hátralévő: halványkék kör, szürke címke. `aria-current="step"` az aktuálison.
- **Lépések:**
  1. Igénylő adatai: Teljes név*, E-mail*, Telefonszám, Lakcím*
  2. Alkotói tevékenység: Alkotói tevékenység* (select)
  3. A hordozó adatai: Hordozó típusa* (select), Darabszám* (≥1), Vásárlás dátuma* (**csak aktuális év**), Eladó neve*, Hologramos címke sorszáma / IMEI* (6–20 karakter, `[A-Za-z0-9-]`)
  4. Tárolt tartalom: leírás* (textarea)
  5. Nyilatkozatok: saját professzionális tartalom*, nem használom magánmásolásra*, társszerzői nyilatkozat (15.4 pont), adatvédelem*
- **Gombsor** (az űrlapkártya alján, hairline felett): „← Vissza” (másodlagos, 2. lépéstől; nem validál, az adatok megmaradnak) · „Tovább →” / az utolsó lépésen „Regisztráció elküldése” (elsődleges) · jobbra igazítva „Mentés és folytatás később” szöveges gomb (2. lépéstől, amikor már van e-mail-cím).
- **Validáció:** a „Tovább →” csak az aktuális lépés mezőit ellenőrzi; hiba esetén a lépésen marad, összesítő riasztás + mezőhibák.
- **Állapotok:** idle → sending (gomb letiltva, spinner, „Küldés…”) → **ok** (azonosító `MMD-ÉÉÉÉ-NNNNN`, „Hogyan tovább?” 3 lépés, PDF-összesítő, Új regisztráció) | **fail** (riasztás „Vissza a hordozó adataihoz →” linkkel, ami a 3. lépésre ugrik és a hibás mezőt jelöli) | **mentve** (halványkék riasztás: „Kitöltés elmentve – a folytatáshoz szükséges linket elküldtük a(z) … címre. A link 30 napig érvényes”).
- **Megjegyzés:** a kérdéslista egyelőre az előzetes bejelentőlap ismert mezőiből áll; a teljes kérdéssort az ügyféltől kell bekérni. A lépésszerkezet tetszőleges számú lépésre és mezőre skálázódik; egy lépésben lehetőleg legfeljebb 6–8 kérdés legyen.

**Ellenőrzők** (`/visszaterites/ellenorzok`): fül IMEI / PCSN. IMEI = pontosan 15 számjegy; SN = 6–24 karakter. Eredmény: **Regisztrált státuszú** (zöld) / **Nem található** (mustár, link az ügyfélszolgálathoz) / **Rendszer nem érhető el** (piros, Újrapróbálás). Élesben az Artisjus IMEI (imei.artisjus.com) és PCSN (artisjus.hu) rendszerének API-ját kell bekötni – egyeztetendő; ha nincs API, maradjon külső link.

**Kapcsolati űrlap** (új elem): név, e-mail, téma (select), üzenet (≥20 karakter) → „Köszönjük, üzenetedet megkaptuk” | hálózati hiba riasztás.

A prototípusbeli tesztbemeneteket (pl. „0000” címke, „999” ellenőrző-kód) az `Allapotok.dc.html` 6. szakasza sorolja fel – ezek csak a demo miatt vannak, élesben a backend válaszai vezérlik.

## State Management
- Shell: `route`, `cookie` (consent), `cookieOpen`, `cookieDetail`, `analytics`, `toast`. Prototípusban localStorage: `mmd-route`, `mmd-cookie`.
- Regisztráció: `step`, `v` (értékek), `c` (nyilatkozatok), `errors`, `cerr`, `status` (`idle | sending | ok | fail`), `saved`.
- Kapcsolati űrlap: `values`, `errors`, `status`.
- GYIK: `open` index, `query`.
- Ellenőrző: `tab`, `code`, `codeErr`, `checking`, `res` (`ok | no | err`).

## Design Tokens
Színek (oklch, zárójelben közelítő hex – a WordPress színpalettájába a hex értékek kerüljenek):
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
- PDF-ek: `/media/6/download` (visszatérítési tájékoztató), `/media/7/download` (GVH végzés) – WordPressben a Médiatárba kerülnek, a régi URL-ekről átirányítás kell.

## WordPress-megvalósítás

### Téma és design tokenek
- Egyedi téma (blokktéma vagy klasszikus téma + ACF-blokkok – a fejlesztő döntése). A színek, betűk, betűméretek és térközök a `theme.json`-ba kerüljenek (`settings.color.palette`, `typography.fontFamilies` / `fontSizes`, `spacing.spacingSizes`), hex értékekkel; a szerkesztőben csak ez a paletta legyen elérhető (`custom: false`).
- **Betűtípusok helyben**: a Source Serif 4 és a Source Sans 3 a témából töltődjön be (`theme.json` `fontFace`, woff2), **ne a Google Fonts CDN-ről** – az EU-ban a távoli betöltés GDPR-kockázat.
- A prototípus inline style-jai helyett komponens-szintű CSS (blokkonként), BEM vagy blokk-osztályokkal.

### Oldalszerkezet és menük
- Minden útvonal valódi WP-oldal, a hierarchia a szülő–gyermek oldalakból jön (pl. Visszatérítés → Regisztráció). A kliensoldali router megszűnik.
- **Fő menü**, **lábléc „Tájékoztatás”** és **lábléc „Visszatérítés”** oszlop: WP-menük (Megjelenés → Menük / navigációs blokk). Aktív állapot a menü `current-menu-item` / `current-menu-ancestor` osztályaiból.
- **„Ebben a részben” oldalsáv**: a szekció szülőoldalának gyermekoldalaiból automatikusan (vagy külön menüből), sticky.
- **Fejléc gomb** („Igénylés indítása”): a témabeállításokból / menüből szerkeszthető felirat és cél.
- **Morzsamenü**: Yoast SEO vagy Rank Math breadcrumbs, a design szerinti markuppal.
- **Mobil**: ~900px alatt hamburgermenü (a prototípusban még nincs megtervezve – a fejlesztés előtt pótolni kell); az oldalsáv mobilon a tartalom fölé kerül, összecsukható „Ebben a részben” menüként.

### Blokk-leltár (ACF- vagy Gutenberg-blokkok)
Minden ismétlődő elem legyen szerkeszthető blokk, **tetszőleges elemszámmal** (a 3 kártya, 5 lépés stb. csak a mostani tartalom):

| Blokk | Hol látszik | Mezők |
|---|---|---|
| Aloldal-hero | minden aloldal | eyebrow, H1, lead (morzsamenü automatikus) |
| Nyitóoldali hero + bakelit | `/` | H1 (kiemelt szóval), lead, 2 gomb; a bakelit **fix sablonelem**, nem szerkeszthető |
| Link-kártya rács | „Kinek szól?” | ismétlő: cím, leírás, linklista |
| Lépéssor | Visszatérítés (Feltételek) | ismétlő: cím, leírás |
| Pipás lista | „Mire lesz szükséged?” (Regisztráció) | ismétlő: cím, leírás |
| PDF letöltő kártya | Visszatérítés (Feltételek) | cím, alcím, fájl (Médiatár) |
| Riasztás / infó doboz | bárhol | típus (siker / hiba / figyelmeztetés / infó), cím, szöveg |
| GYIK harmonika + kereső | GYIK oldalak | GYIK-kategória választó (ld. lent) |
| Felosztási arány-sáv | Díjak sorsa | ismétlő: címke, százalék, szín |
| Jogkezelő lista | Díjak sorsa | a Jogkezelők tartalomtípusból |
| Kapcsolati blokk / ügyfélszolgálat | lábléc, oldalsáv | témabeállításokból (telefon, e-mail, nyitvatartás – egy helyen szerkeszthető) |

- **Kiemelt szó a címben** (mustár „díj”): egyedi formázás a blokkszerkesztőben (pl. „Kiemelés” formátum → `<mark class="is-accent">`).

### Tartalomtípusok
- **GYIK** (egyedi tartalomtípus vagy ACF-ismétlő): kérdés, válasz (rich text), kategória (`kereskedoknek` / `visszaterites`), sorrend; a mélylink `id` a slugból. FAQ schema a SEO-bővítményből.
- **Jogkezelők**: név, kinek a jogait kezeli, honlap, szabályzat URL, logó, szín.
- Az **Adatvédelmi tájékoztató** sima oldal; a tartalomjegyzék a címsorokból generálódjon (tartalomjegyzék-blokk).

### Űrlapok – Gravity Forms
- **Regisztrációs kérdőív**: Gravity Forms többoldalas űrlap (Page Break mezők), lépésjelző stílusa: „Steps” – a design szerinti lépésjelzőre stílusozva. Validáció: oldalanként, beküldéskor (a GF alapviselkedése). A „Validation Summary” beállítás adja az összesítő riasztást.
- **Mentés és folytatás később**: GF „Save and Continue” funkció; a folytató link e-mailben, 30 napos lejárattal (GF alapérték).
- **Egyedi fejlesztés kell:**
  - a vásárlás dátumának aktuális évre korlátozása és a címkesorszám formátuma (`gform_field_validation` hook);
  - az `MMD-ÉÉÉÉ-NNNNN` formátumú azonosító (egyedi merge tag vagy rejtett mező, a beküldéskor generálva) a visszaigazoló oldalon és az e-mailben;
  - a címkesorszám ellenőrzése az Artisjus nyilvántartásában, ha erre lesz API (hiba esetén a 3. lépésre visszavezető üzenet).
- **PDF-összesítő**: Gravity PDF bővítmény, a design színeivel.
- **Visszaigazoló képernyő**: GF Confirmation (szöveg típus) a design „Regisztrációdat rögzítettük” blokkjának markupjával.
- Hibás mező, összesítő riasztás, gombok: a GF kimenetét (`.gfield_error`, `.gform_validation_errors`, `.gform_next_button` stb.) kell a design szerint stílusozni – ne saját markupot építsetek.
- **Kapcsolati űrlap**: szintén Gravity Forms (egyoldalas).
- Adatkezelés: a beküldött bejegyzések tárolási ideje és törlése (GF „Personal Data” beállítás) – egyeztetendő az adatvédelmi tájékoztatóval.

### Süti-hozzájárulás
- Bővítmény (pl. Complianz vagy CookieYes): kategóriák Szükséges + Statisztikai; a Google Analytics csak hozzájárulás után töltődjön be (Consent Mode v2). A sáv megjelenését a design szerint kell stílusozni; a „Süti-beállítások” link a láblécben és a Süti-tájékoztatón a bővítmény újranyitó függvényét hívja. A sütitáblázat a bővítményből generálható.

### Ellenőrzők (IMEI / PCSN)
- Ha van API: kis egyedi bővítmény (REST végpont + a design szerinti űrlap blokk), a három eredményállapottal. Ha nincs: a blokk külső linkként jelenjen meg (ugyanebben a kártyában).

### Migráció, SEO, akadálymentesség
- 301-es átirányítások a régi Drupal-útvonalakról, horgonyokról és PDF-linkekről (Redirection bővítmény vagy szerveroldali szabályok); a régi `/ugyfelportal…` útvonalak → `/visszaterites/regisztracio`.
- SEO-bővítmény (Yoast / Rank Math): meta, breadcrumbs, FAQ schema, XML sitemap.
- Akadálymentesség: WCAG 2.1 AA (kontrasztok, fókuszállapotok, `aria-current` a menüben és a lépésjelzőn, űrlaphibák `aria-describedby`-jal).
- A forgó bakelit `prefers-reduced-motion` esetén álljon meg.

## Nyitott pontok
- **A regisztrációs kérdőív teljes kérdéslistája** – az ügyféltől kell bekérni; ennek alapján véglegesíthető a lépések száma és tartalma.
- **Mobil hamburgermenü** megtervezése.
- Adatvédelmi tájékoztató: a prototípus csak a szerkezetet és rövid összefoglalót mutatja; a teljes jogi szöveget a jelenlegi oldalról kell átemelni.
- Sütitáblázat nevei/időtartamai – a WordPress + süti-bővítmény + GA beállítás alapján újra kell gyűjteni.
- Az eredeti oldal „kilenc alprogramról” ír, de csak nyolcat sorol fel – tisztázandó.
- IMEI/PCSN API elérhetősége – egyeztetendő az Artisjusszal.
- Angol nyelvű verzió (WPML / Polylang), tarifatáblázat beemelése: ügyféldöntés.

## Files
- `Weboldal.dc.html` – shell, router (belépési pont)
- `PageHome.dc.html`, `PageDij.dc.html`, `PageSorsa.dc.html`, `PageVissza.dc.html`, `PageRegisztracio.dc.html`, `PageInfo.dc.html`
- `Allapotok.dc.html` – állapotgaléria
- `support.js` – a `.dc.html` fájlok futtatókörnyezete (csak a prototípus megnyitásához)
