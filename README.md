# Klientský newsletter evisions — vlastní HTML pro SmartEmailing

Ručně kódovaný tabulkový e-mail, který se vkládá do SmartEmailingu jako vlastní HTML.
Šablona se needituje vizuálně — obsah se přepisuje přímo v kódu.

## Soubory

| Soubor | Co to je |
|---|---|
| `newsletter-01-26.html` | **e-mail k nasazení** — lednové vydání, 29 bloků |
| `bloky-knihovna.html` | knihovna 13 bloků, po jednom z každého — kopíruj odsud do nového vydání |
| `podklady/smartemailing/` | **20 obrázků k nahrání na CDN**, 403 kB — kompletní |
| `nahled-lokalni.html` | náhled skutečného kódu s vloženými obrázky — otevři dvojklikem, dá se i poslat |
| `nahled-emailing.html` | designový náhled light + dark vedle sebe (není pro nasazení) |
| `archiv/` | varianta jako smart šablona s `data-editable` + starší designové náhledy |

Oba HTML soubory mají nahoře v komentáři obsah — číslovaný seznam bloků v pořadí,
v jakém jsou v dokumentu. Každý blok je v kódu označený `<!-- NN | Název bloku -->`.

## Velikost — čím měřit

**Gmail seká zprávy nad ~102 kB a odstřihne přitom i patičku s odhlašovacím odkazem.
Ten limit se ale nevztahuje na surové bajty souboru, ale na zakódovanou zprávu.**
Česká diakritika i jakýkoli jiný ne-ASCII znak v quoted-printable naroste na trojnásobek.

| | raw | quoted-printable | base64 |
|---|---|---|---|
| `newsletter-01-26.html` | 60,4 kB | **67,4 kB** | 80,5 kB |
| `bloky-knihovna.html` | 31,6 kB | 36,4 kB | 42,1 kB |

Rezerva v nejhorším případě (QP) je **34,6 kB**. Než přidáš do vydání další sekci,
přepočítej to — nikoli velikost souboru na disku.

Praktický důsledek: **žádné ne-ASCII znaky v komentářích ani v dekoraci kódu.**
První verze měla komentáře orámované znakem `═` (3 bajty, v QP 9 znaků) a soubor kvůli
tomu vycházel na 108,5 kB, tedy nad limitem, aniž by to bylo na velikosti souboru vidět.
Diakritika v obsahu je samozřejmě v pořádku, ta do mailu patří.

## Nasazení

### 1. Nahrát obrázky na CDN

Do `cdn-smartemailing.cz/676845/media/klientsky NL/sablona-2026/`. **Šablona odkazuje
výhradně na tuhle jednu složku** — nic jiného na CDN nepotřebuje.

```
logo-white.png       632×120    8 kB   bílé logo (hlavička + patička)
hero-01-26.jpg      1200×840   66 kB   hero vydání (tmavý přechod zapečený v JPEGu)
portret-benda.png    144×144   32 kB
portret-kovalcik.png 144×144   30 kB
portret-kunesova.png 144×144   32 kB
portret-sikora.png   144×144   30 kB
ok-fsv.jpg           372×280   21 kB
ok-webtop.jpg        372×280   25 kB
ok-iea.jpg           372×280   26 kB
party-1.jpg          372×372   21 kB
party-2.jpg          372×372   20 kB
party-3.jpg          372×372   23 kB
tip-1.jpg            192×240   11 kB
tip-2.jpg            192×240   11 kB
tip-3.jpg            192×240   11 kB
tip-4.jpg            192×240   13 kB
tip-5.jpg            192×240   11 kB
ico-fb.png            44×44     1 kB
ico-ig.png            44×44     2 kB
ico-li.png            44×44     1 kB
```

**Portréty se nahrávají znovu v malé verzi.** Ty v `media/profilovky_evisions/` mají
350 × 350 px a 166 kB, ale zobrazují se na 72 px — čtyři z nich by znamenaly 664 kB,
které si příjemce stáhne zbytečně. Ve verzi 144 × 144 je to 125 kB, tedy o 539 kB méně
na každé otevření. Celková váha obrázků v mailu je teď 395 kB.

### 2. Logo

**Hotovo.** `logo-white.png` je v podkladech: 632 × 120 px, RGBA, bílá kresba
s oranžovými akcenty `#E5712C`, 8 kB. Zobrazuje se na 125 × 24 px v hlavičce
a 110 × 21 px v patičce, tedy asi pětinásobná hustota — ostré i na retině.
Poměr stran se od nativního loga liší o 0,9 %, což je pod hranicí vnímatelnosti.

### 3. Vložit do SmartEmailingu

Kampaně → **E-maily** → *Vytvořit nový e-mail* → vložit vlastní HTML.
(Není to v sekci Šablony e-mailů, ale v E-mailech.)

### 4. Otestovat — v tomto pořadí

**První test je nejdůležitější: přežily import podmíněné komentáře pro Outlook?**
Hero má fotku jako `background` na buňce a Outlook pro Windows ji vykreslí jen díky
`<!--[if gte mso 9]>` a VML (`<v:image>`, `<v:rect>`, `<v:textbox>`). V souboru jsou
**3 podmíněné komentáře a 3 VML tagy**. Pokud je importér SmartEmailingu strhne,
v Outlooku zůstane z hera jen tmavá plocha s textem — funkční, ale bez fotky.
Ověřit hned po prvním vložení, ne až před odesláním.

Pak:

- odeslat na testovací seznam, ne jen náhled (odhlašovací stránka v náhledu nefunguje)
- ověřit, že se zpráva nesekla — na konci musí být vidět patička s odhlášením
- Outlook pro Windows: hero, gradientní lišta (spadne na plnou `#ED692C`), hranaté CTA
- Gmail app: dělá si vlastní inverzi v dark mode a naše CSS ignoruje
- Apple Mail / iOS: webfonty a `prefers-color-scheme`
- stav s vypnutými obrázky: logo má nastylovaný alt, hero má `bgcolor="#202020"`
- zkontrolovat, že SmartEmailing přepsal odkazy pro měření kliků a nerozbil UTM

## Knihovna bloků

| # | Blok | Poznámka |
|---|---|---|
| 1 | Hlavička s logem | tmavý pás, logo + řada služeb (na mobilu se skryje) |
| 2 | Hero – fotka s textem | přechod zapečený v JPEGu, VML pro Outlook |
| 3 | Gradientní lišta | CSS gradient, v Outlooku plná `#ED692C` |
| 4 | Nadpis sekce | eyebrow + vlasová linka + nadpis |
| 5 | Text s odkazem | 2 odstavce Lora + odkaz s oranžovým podtržením |
| 6 | Citace s portrétem | kruhový portrét 72 px, na mobilu se stohuje |
| 7 | CTA tlačítko | **v jednom vydání použij právě jedno** |
| 8 | Tři obrázky | s popiskami, tmavá plocha, na mobilu zůstávají vedle sebe |
| 9 | Tři obrázky bez popisků | fotostrip, tmavá plocha |
| 10 | Tip pod čarou | náhled 96 × 120 + kategorie + text |
| 11 | Byline s portrétem | „Vybral … " — portrét 44 px + jméno a role |
| 12 | Oddělovač | vlasová linka — **nedávat nad Nadpis sekce**, ten má vlastní linku, vznikly by dvě |
| 13 | Patička | **nikdy nemazat** — povinné náležitosti a odhlášení |

Každý blok je samostatná tabulka na 100 % šířky s vnořenou tabulkou na 600 px.
Bloky se navzájem nezanořují, takže se dají přehazovat, duplikovat i mazat po celých
celcích, aniž by se rozpadl layout.

## Co je v šabloně vědomě zabudované

- **600 px kolona.** Stará šablona měla 680 px, což působí vodorovný scroll ve čtecím
  panelu Outlooku a na části Android klientů.
- **Živé nadpisy sekcí.** „SEO", „UX", „Predikce" už nejsou PNG. Stará šablona
  s vypnutými obrázky neukázala nic.
- **Kontrasty.** Oranžová `#E5712C` má na krémové jen 3,1 : 1, proto jsou odkazy tmavé
  s oranžovým podtržením a šipky ve ztmavené `#C25A1E` (4,6 : 1). Na fotce má oranžový
  text 1,1 : 1 — proto je eyebrow v heru bílý na oranžovém chipu. Lime `#BDF16D` má na
  bílé 1,3 : 1 a patří výhradně na tmavé plochy.
- **Fallback bez webfontu.** Gmail a Outlook pro Windows Inter Tight ignorují. Nadpisy
  mají ve fallbacku menší velikost a tracking, jinak se zalomí o řádek dřív a vytlačí
  CTA pod zlom.
- **Dark mode** přes `prefers-color-scheme` plus `[data-ogsc]` pro Outlook.com. Gmail
  app si invertuje po svém — světlá verze je proto navržená tak, aby cizí inverzi přežila.
- **UTM na všech 11 obsahových odkazech.** Stará šablona neměla ani jeden, provoz
  z newsletteru padal v GA4 do direct.
- **Jedno oranžové tlačítko v celém vydání.** Ostatní akce jsou textové odkazy. Šest
  tlačítek pod sebou znamená, že žádné nefunguje. **V heru CTA není** — celý e-mail
  je to vydání, takže tlačítko „Přečíst celé vydání" tam nemělo co dělat.
- **Jedna dělící linka mezi sekcemi.** Nadpis sekce nese vlasovou linku vedle eyebrow,
  takže samostatný blok Oddělovač se nad něj nedává. Ve vydání se nepoužívá vůbec.
- **Výška hera 420 px desktop / 360 px mobil** je odměřená, ne odhadnutá, a musí se
  rovnat rozměru VML rectu pro Outlook. Když se změní text v heru, změní se i potřebný
  `hero-spacer` (dnes 229 px / 158 px) — jinak v Outlooku vznikne ořez nebo mezera.
- **Tmavá sekce „Zpětné zrcátko"** v druhé polovině rozděluje mail na dvě části a dává
  fotkám z ocenění i večírku lepší vyznění než krémová plocha.

## Ověřeno automaticky

Tagy párované · 21 obrázků, všechny s `alt` i `width` · žádný flexbox, grid, CSS
proměnná, inline SVG, `object-fit` ani base64 obrázek · 29 bloků, žádný zanořený ·
jedno CTA tlačítko v celém vydání · hero 420/360 px odměřeno v prohlížeči ·
merge tagy `{{unsubscribe}}`, `{{df_emailaddress}}`, `{{cl_sendername}}`,
`{{cl_senderemail}}`, `{{webversion}}` · velikost v QP 67,4 kB.

**Neověřeno:** vizuální kontrola v reálných klientech. Ta musí proběhnout po vložení.

## Proč se v `newsletter-01-26.html` nezobrazují obrázky

Odkazuje na CDN cestu `klientsky NL/sablona-2026/`, která zatím **neexistuje** (vrací 404).
Je to produkční e-mail, takže musí odkazovat na veřejně hostované obrázky — base64 Gmail
strhává a na lokální soubory se v e-mailu odkázat nedá. Až složku nahraješ, zobrazí se.

Do té doby je na prohlížení `nahled-lokalni.html`, kde jsou všechny obrázky včetně
loga vložené přímo v souboru. Ten se dá otevřít dvojklikem i poslat dál, ale **do SmartEmailingu nikdy
nevkládej jeho** — vložené obrázky by Gmail strhal a soubor je desetkrát větší.

## Otevřené k rozhodnutí

- **Dark mode textu.** Serifový text (Lora) v bílé na tmavé opticky rozkvétá. Nabízená
  úprava: v dark režimu Lora 500 místo 400, bílá 80 % místo 74 %, 16,5 px místo 17.
  Zatím neaplikováno.
- **Řada `SEO · PPC · UX · Social`** v hlavičce — na mobilu se skrývá, takže je otázka,
  jestli tam má být.
- **Utility strip** nad hlavičkou byl vyřazen. Odkaz na webovou verzi je zatím jen
  v patičce přes `{{webversion}}`.
- **Nepoužitý obsah z lednového vydání:** TikTok „Dej Vendy vlasy do skřipce", fotka
  studentů FSV je použitá v mřížce ocenění, UX ilustrace `discover-opportunity`
  vynechaná (v koloně byla skoro kvadratická, 536 × 522 px).

## GitHub Pages

V repu je prázdný soubor `.nojekyll`. **Nemazat.** Bez něj GitHub Pages prohánějí
obsah Jekyllem, který v HTML interpretuje `{{ ... }}` jako Liquid proměnné — a šablona
je plná merge tagů SmartEmailingu (`{{unsubscribe}}`, `{{df_emailaddress}}` a další).
S `.nojekyll` se soubory jen zkopírují, což je pro statické HTML správné a rychlejší.

Live náhled: https://ondrej-broz.github.io/newsletter/
