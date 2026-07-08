# Mirka Meal Tracker

Osobný, jednosúborový nutričný tracker pre konkrétnu používateľku (Miroslava Bajtošová). Žiadny backend, žiadny build — všetko je v jednom `index.html` (~180 KB, ~2600 riadkov: CSS v `<style>`, markup, vanilla JS v `<script>`). Dáta žijú v `localStorage` prehliadača.

## Používateľka (kontext pre AI funkcie aj UX rozhodnutia)

- Miroslava, 48 rokov, 177 cm, ~79 kg, cieľ 70 kg (chudnutie)
- **Perimenopauza** — zvýšené nároky na Ca/Mg/Fe, riziko osteoporózy
- **Reflux** — večera max 550 kcal (natvrdo vynútené alertom, pozri nižšie), bez citrusov a mäty
- **Migrény + LS svalový spazmus** — neurologická indikácia na denné horčíkové doplnky (nesmie sa vynechať), odporúčaný max interval medzi jedlami 5h
- **Zvýšené CV riziko** (rodinná anamnéza) — dôraz na omega-3, K2, CoQ10, cholesterol pod kontrolou
- Laboratórne poznámky v Nastaveniach: FSH 57.86, LDL 3.19 mmol/l, HGB 132 g/l, feritín 54.8 µg/l (04/2026, v norme)

Tieto zdravotné toggly v Nastaveniach (`#s-restrict`, `#s-notes`, checkboxy diagnóz) sú momentálne **len informačné/statické** — jediná časť, ktorá je skutočne vynútená v logike, je refluxný alert (viď nižšie). Pri rozširovaní funkcionality zvážiť, či majú diagnózy začať reálne ovplyvňovať výpočty/alerty.

## Architektúra

- Jeden súbor `index.html`, žiadne závislosti, žiadny bundler
- Obrazovky (tab bar): **Dnes / Log / Produkty / Suplementy / História / Nastavenia** — prepínané cez `openScreen(name)`, každá má vlastný `render*()`
- Dáta v `localStorage` pod kľúčmi: `day_<date>` (jedlá + aktivity), `chk_<date>` (odškrtnuté suplementy), `supps`, `products`, `profile`, `targets`, `micros`, `wlog` (váha)
- Kalorické/makro ciele: Mifflin-St Jeor BMR × aktivita (TDEE), `MACRO_SPLITS` podľa cieľa (lose/muscle/recomp/maintain) v `calcBMR()`
- `emptyTotals()` / `calcTotals()` / `calcSuppTotals()` / `calcDayTotals()` — jednotný súčet makro + mikro živín naprieč jedlami a odškrtnutými suplementami

## AI integrácia

- Volá sa priamo z prehliadača na `https://api.anthropic.com/v1/messages` (model `claude-sonnet-4-5`), API kľúč zadáva používateľka v Nastaveniach a ukladá sa len lokálne (`localStorage`)
- Použitia: odhad výživových hodnôt z textového popisu jedla, AI foto jedla, AI foto etikety produktu/suplementu, denné zhodnotenie (`genDayReview`), týždenné odporúčania (`genRecommendations`)
- Do promptov sa vždy vkladá profil používateľky (vek, výška, váha, diagnózy) a `SUPP_PROFILE` — textový blok s presným zoznamom suplementov, dávok a synergií (definovaný v JS, riadky ~992-1030)
- Barcode/EAN lookup ide cez Open Food Facts (nie Anthropic)

## Suplementačný profil

Zdroj pravdy pre suplementy je [supplement-profile.md](../Downloads/supplement-profile%20(2).md) (mimo repa, referenčný dokument) + `DEFAULT_SUPPS`/`SUPP_PROFILE` v `index.html`. Kľúčové body:

- **Denne ráno:** iProbio probiotiká, Omega-3 TG Strong 3×softgel (990mg EPA + 660mg DHA), B-komplex Dr.Max Forte (**chýba B9 aj B12**), D3 2000 IU, K2 MK-7 100mcg, Ca citrát 120mg (štítok odporúča 3×/deň, užíva sa 1×), CoQ10, MSM 4g (2 odmerky)
- **Denne kedykoľvek:** Kreatín monohydrát 6g
- **Denne večer (striedanie, nesmie sa vynechať):** Magnosolv (~342mg Mg) ALEBO Chelated Mg kapsula (100mg) — neurologická indikácia, migréna/spazmus profylaxia
- **Príležitostne (nie denne):** Vitamín C 1000mg, morský kolagén + kyselina hyalurónová, Inosifol (myo-inozitol 4200mg + aktívny folát/Quatrefolic 400mcg)
- **Medzery:** B12 úplne chýba (kryť stravou), B9 len príležitostne cez Inosifol, železo sa nesuplementuje (feritín v norme)
- **Synergie dôležité pre AI odporúčania:** D3+K2+Ca = "kostná trojica"; CoQ10 s tukovým jedlom; kolagén+vitC = kĺbový protokol; MSM+kolagén+vitC = kĺbový protokol
- Zvažovaný, zatiaľ neaktívny doplnok: Psyllium plus (vláknina) — neužíva sa, nezarátavať

Pri zmene dávkovania/produktov **aktualizovať na troch miestach súčasne**: `supplement-profile.md`, `DEFAULT_SUPPS` pole a `SUPP_PROFILE` textový blok v `index.html` — inak sa UI, dáta a AI prompty rozídu.

## Vynútená logika (alerts v `renderToday`)

- Omega-3 < 50 % cieľa a nezaškrtnuté → kritický alert
- Horčík večer neodškrtnutý → warning alert
- Večera > 550 kcal → refluxný alert (jediné miesto, kde je zdravotné obmedzenie skutočne vynútené v kóde)
- Jedenie po 20:00 → warning alert

## Vývoj

- Žiadny build krok — edituje sa priamo `index.html`, testuje sa otvorením v prehliadači (najlepšie mobile viewport, appka je mobile-first/dark-mode, myslená na "Add to Home Screen" na iPhone Safari)
- Nasadenie: GitHub Pages z `main` vetvy / root
- Pri úprave dát/schémy (nové polia v jedlách/suplementoch/produktoch) pozor na `migrateSupp()` a podobné migrácie — staré dáta v `localStorage` používateliek musia zostať kompatibilné
