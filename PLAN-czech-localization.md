# Plán: Implementace českého jazyka rozhraní (OpenNOW)

## Cíl
Přidat češtinu (`cs`) jako plnohodnotný jazyk UI OpenNOW, konzistentně s existující lokalizační infrastrukturou (Crowdin-owned `locales/*.json`, zdrojový `locales/en.json`).

## Kontext zjištěný z repo
- Zdrojový jazyk: `locales/en.json` (jediný soubor, který se ručně edituje).
- Ostatní `locales/*.json` jsou generovány Crowdinem (dle AGENTS.md se ručně neupravují).
- `Localization.cpp` načítá dostupné locales dynamicky z `:/locales/*.json` → stačí přidat `locales/cs.json`.
- `Resources.cmake` používá `GLOB` nad `locales/*.json` → cs.json se automaticky zabalí do Qt resources.
- Validace: `npm run locales:check` (kontroluje JSON platnost a klíče).
- QML používá `qsTr` / `I18n.source` / `I18n.text` → klíče musí odpovídat en.json.

## Fáze

### Fáze 1 – Příprava cs.json (překlad)
- [ ] Vytvořit `locales/cs.json` jako strukturální kopii `locales/en.json`.
- [ ] Přeložit všechny hodnoty (řetězce) do češtiny, zachovat:
  - zástupné znaky `%1`, `%2`, `{{token}}`,
  - velikost písmen (ALL CAPS nadpisy),
  - interpunkci a formátování.
- [ ] Nepřekládat klíče (jen hodnoty).
- **Poznámka:** Pokud se použije strojový překlad, nutná ruční revize herní/technické terminologie (GFN, stream, codec, HDR, FSR, CloudMatch).

### Fáze 2 – Crowdin integrace (volitelné, doporučené)
- [ ] Přidat `cs` do Crowdin projektu OpenNOW, aby budoucí synchronizace přepsala ruční cs.json.
- [ ] Nebo ponechat cs.json jako „seed“ a nechat Crowdin přepsat při dalším syncu (dle AGENTS.md pravidla).

### Fáze 3 – Validace
- [ ] Spustit `npm run locales:check` → musí projít bez chyb.
- [ ] Ověřit, že `cs.json` je validní JSON a má stejnou strukturu klíčů jako `en.json` (skript kontroluje JSON platnost; strukturální shodu ověřit diffem klíčů).
- [ ] Build Qt: `cmake --build build/opennow-qt` (resources se přegenerují).
- [ ] Spustit Qt testy: `ctest --test-dir build/opennow-qt --output-on-failure` (zejména `tst_localization`).

### Fáze 4 – Ověření v UI
- [ ] Spustit aplikaci, v Settings → Language zvolit Čeština (nebo `cs`).
- [ ] Projít hlavní obrazovky: Home, Library, Store, Auth, Friends, Updates, Settings, Stream.
- [ ] Ověřit, že se zobrazují české řetězce, ne fallback na angličtinu (kromě chybějících klíčů).
- [ ] Ověřit pluralizaci a interpolaci (`%1`, `{{count}}`).

### Fáze 5 – Dokumentace
- [ ] Aktualizovat README/docs, pokud obsahují seznam podporovaných jazyků.
- [ ] Přidat poznámku do release notes.

## Rizika a omezení
- Velikost `en.json` je velká (tisíce klíčů) → ruční překlad je pracný; zvážit profesionální překlad nebo Crowdin komunitní překlad.
- AGENTS.md: Crowdin owns generated translations. Ruční cs.json může být přepsán při Crowdin syncu → nutná koordinace.
- Terminologie (GFN, streaming, codec) vyžaduje konzistentní překladový slovník.

## Doporučený další krok
Začít Fází 1: vygenerovat `locales/cs.json` jako kopii `en.json` a přeložit (strojově + revize). Chceš, abych to provedl automaticky (strojový překlad s revizí terminologie)?
