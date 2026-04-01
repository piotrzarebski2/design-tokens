# Design Tokens — Onboarding dla Frontend Developerów

**Synerise Design System v2 · Kwiecień 2026**

> Ten dokument wyjaśnia cały pipeline tokenów: od źródła w Figmie/repo, przez transformer, aż po gotowe zmienne CSS których będziesz używać w kodzie.

---

## Spis treści

1. [Czym są design tokeny i po co to robimy](#1-czym-są-design-tokeny-i-po-co-to-robimy)
2. [Architektura 3-warstwowa](#2-architektura-3-warstwowa)
3. [Gdzie żyją tokeny — struktura repo](#3-gdzie-żyją-tokeny--struktura-repo)
4. [Token Studio — skąd się biorą JSONy](#4-token-studio--skąd-się-biorą-jsony)
5. [Pipeline transformacji: JSON → CSS](#5-pipeline-transformacji-json--css)
6. [Konfiguracja Style Dictionary (setup raz)](#6-konfiguracja-style-dictionary-setup-raz)
7. [Faza 1: kolory semantyczne (teraz)](#7-faza-1-kolory-semantyczne-teraz)
8. [Faza 2: kolory modułowe (wkrótce)](#8-faza-2-kolory-modułowe-wkrótce)
9. [Faza 3: spacing i typografia (w toku)](#9-faza-3-spacing-i-typografia-w-toku)
10. [Jak używać zmiennych CSS — zasady](#10-jak-używać-zmiennych-css--zasady)
11. [Light Mode / Dark Mode](#11-light-mode--dark-mode)
12. [Najczęstsze pytania (FAQ)](#12-najczęstsze-pytania-faq)

---

## 1. Czym są design tokeny i po co to robimy

Design token to **nazwana para: nazwa = wartość**. Zamiast pisać `background-color: #0b68ff` wszędzie w kodzie, piszesz `background-color: var(--buttons-variant-primary-bg-default)`. Wartość pod spodem to nadal `#0b68ff`, ale gdy designer zmieni kolor przycisku — zmiana propaguje się automatycznie wszędzie bez szukania po całym projekcie.

```
❌ background-color: #0b68ff;                              (hardkodowana wartość)
❌ background-color: var(--color-blue-600);                (primitive — nie stosujemy)
❌ background-color: var(--color-background-brand-solid);  (semantic — nie stosujemy)
✅ background-color: var(--buttons-variant-primary-bg-default);  (module token ✅)
```

Tokenizacja pozwala nam też obsługiwać **Light/Dark mode** i potencjalnie **white-labeling** przez jedną zmianę w pliku tokenów, bez dotykania kodu komponentów.

---

## 2. Architektura 3-warstwowa

System ma 3 warstwy. Ważne: **do kodu aplikujesz tylko warstwę 3 (Module)**. Warstwy 1 i 2 to wewnętrzna kuchnia systemu.

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  WARSTWA 1 — PRIMITIVES (core.json)                            │
│  Surowa paleta kolorów. Opisuje CO TO JEST.                    │
│                                                                 │
│  color.blue.600 = #0b68ff                                      │
│  color.grey.800 = #384350                                      │
│  color.green.600 = #54cb0b                                     │
│                                                                 │
│  ❌ Nigdy nie trafia do kodu                                    │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  WARSTWA 2 — SEMANTIC (Light.json / Dark.json)                 │
│  Znaczenie i przeznaczenie. Opisuje DO CZEGO SŁUŻY.            │
│                                                                 │
│  color.background.brand.solid = {color.blue.600}              │
│  color.text.base.default      = {color.grey.800}              │
│  color.text.onSolid.brand     = #FFFFFF                       │
│                                                                 │
│  Tu zaszyta jest różnica Light ↔ Dark.                        │
│  ❌ Nigdy nie trafia do kodu bezpośrednio                      │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  WARSTWA 3 — MODULE (modules/base.json)                        │
│  Konkretny komponent. Opisuje GDZIE I W CZYM jest użyte.      │
│                                                                 │
│  buttons.variant.primary.bg.default = {color.background.brand.solid}
│  avatar.bg.default                  = {color.background.base.muted}
│                                                                 │
│  ✅ TYLKO TO TRAFIA DO KODU                                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Dlaczego nie używamy Semantic bezpośrednio?

Wyobraź sobie, że 10 komponentów używa `color.background.brand.solid`. Chcesz zmienić kolor TYLKO dla przycisku primary — nie możesz, bo zmiana semantic uderzy we wszystkie 10. Token modułowy daje izolację: zmiana `buttons.variant.primary.bg.default` dotyka tylko buttonów.

```
color.background.brand.solid  →  buttons.variant.primary.bg.default
                              →  links.text.default
                              →  badges.variant.info.bg
                              →  ... (10+ komponentów)

Chcesz zmienić TYLKO button? Zmień token modułowy. Reszta bez zmian. ✅
```

---

## 3. Gdzie żyją tokeny — struktura repo

Repo: **`github.com/piotrzarebski2/design-tokens`**

```
design-tokens/
├── tokens/
│   ├── $metadata.json          ← kolejność ładowania tokenSetów (WAŻNE dla SD)
│   ├── $themes.json            ← definicje tematów Light/Dark + mapowanie Figma Variables
│   │
│   ├── primitives/
│   │   └── core.json           ← Warstwa 1: cała paleta (kolory, spacing raw, radius, shadows)
│   │
│   ├── semantic/
│   │   ├── Light.json          ← Warstwa 2: tokeny semantyczne — tryb jasny
│   │   ├── Dark.json           ← Warstwa 2: tokeny semantyczne — tryb ciemny
│   │   └── custom-color/       ← Semantic per kolor (blue, grey, red, green, yellow...)
│   │       └── *.json
│   │
│   ├── surface/
│   │   └── base.json           ← Wzorce surface (base, raised, floating — wkrótce)
│   │
│   └── modules/
│       └── base.json           ← Warstwa 3: WSZYSTKIE tokeny modułowe komponentów
│
└── docs/                       ← Dokumentacja (ten plik też tu żyje)
```

### Format JSON — DTCG

Tokeny są zapisane w formacie **DTCG (Design Token Community Group)** — to otwarty standard. Każdy token wygląda tak:

```json
{
  "buttons": {
    "variant": {
      "primary": {
        "bg": {
          "default": {
            "$type": "color",
            "$value": "{color.background.brand.solid}",
            "$description": "Primary button background — default state"
          }
        }
      }
    }
  }
}
```

Kluczowe pola:
- `$type` — typ tokena (`color`, `dimension`, `typography`, itd.)
- `$value` — wartość lub **referencja do innego tokena** (`{color.background.brand.solid}`)
- `$description` — opis (trafi jako komentarz do CSS)

Referencje (`{...}`) są rozwijane przez transformer — nie martwisz się tym w kodzie.

---

## 4. Token Studio — skąd się biorą JSONy

**Token Studio** to plugin do Figmy. Projektanci używają go, żeby:

1. Definiować tokeny bezpośrednio w Figmie
2. Synchronizować je z GitHubem przez wbudowany sync (push/pull)
3. Mapować tokeny na Figma Variables — designer stosuje token modułowy w komponencie

Przepływ wygląda tak:

```
Figma + Token Studio Plugin
        │
        │  push (gdy designer wprowadzi zmiany)
        ▼
GitHub repo (tokens/*.json)   ← SOURCE OF TRUTH
        │
        │  yarn build:tokens / CI
        ▼
CSS Variables (dist/tokens.css)
        │
        ▼
Aplikacja frontendowa
```

**Twoja rola jako frontend dev:** konsumować gotowy CSS z tokenami. Nie edytujesz JSONów ręcznie — to domena designerów i Design System teamu. Ty uruchamiasz transformer (raz przy setupie, potem automatycznie w CI) i używasz zmiennych CSS.

---

## 5. Pipeline transformacji: JSON → CSS

JSON z Token Studio → **Style Dictionary v4** + **@tokens-studio/sd-transforms** → CSS custom properties

```
tokens/*.json
      │
      │  Style Dictionary v4
      │  + @tokens-studio/sd-transforms
      ▼
dist/
  ├── light.css    ← CSS variables dla Light mode
  └── dark.css     ← CSS variables dla Dark mode
```

### Co robi transformer?

1. **Ładuje tokeny** zgodnie z kolejnością z `$metadata.json`
2. **Rozwiązuje referencje** — `{color.blue.600}` staje się `#0b68ff`
3. **Przetwarza specjalne wartości** — np. `alpha: 0.4` na rgba, wyrażenia matematyczne
4. **Zamienia nazwy** z notacji z kropką na format CSS:
   ```
   buttons.variant.primary.bg.default  →  --buttons-variant-primary-bg-default
   color.background.brand.solid        →  --color-background-brand-solid
   ```
5. **Generuje osobne pliki** dla Light i Dark mode z rozwiązanymi wartościami

---

## 6. Konfiguracja Style Dictionary (setup raz)

### Instalacja

```bash
npm install --save-dev style-dictionary @tokens-studio/sd-transforms
```

### Plik konfiguracyjny `build-tokens.mjs`

```javascript
import StyleDictionary from 'style-dictionary';
import { register, permutateThemes } from '@tokens-studio/sd-transforms';
import { readFileSync } from 'fs';

// Rejestracja transformów Token Studio
register(StyleDictionary);

// Wczytaj $themes.json
const $themes = JSON.parse(readFileSync('tokens/$themes.json', 'utf-8'));

// Generuj permutacje tematów (Light, Dark)
const themes = permutateThemes($themes, { separator: '_' });

// Dla każdego tematu wygeneruj osobny plik CSS
const configs = Object.entries(themes).map(([name, tokenSets]) => ({
  source: tokenSets.map((tokenSet) => `tokens/${tokenSet}.json`),
  preprocessors: ['tokens-studio'],   // wymagane od sd-transforms 0.16+
  platforms: {
    css: {
      transformGroup: 'tokens-studio',
      prefix: '',                     // brak prefixu — zmienne: --buttons-variant-primary-bg-default
      buildPath: 'dist/',
      files: [
        {
          destination: `${name.toLowerCase()}.css`,
          format: 'css/variables',
          options: {
            outputReferences: false,  // rozwiąż wszystkie referencje do hard values
            selector: name === 'Light' ? ':root' : '[data-theme="dark"]',
          },
        },
      ],
    },
  },
}));

// Build
for (const cfg of configs) {
  const sd = new StyleDictionary(cfg);
  await sd.buildAllPlatforms();
}
```

### Skrypt w `package.json`

```json
{
  "scripts": {
    "build:tokens": "node build-tokens.mjs"
  }
}
```

### Uruchomienie

```bash
npm run build:tokens
```

Efekt: pliki `dist/light.css` i `dist/dark.css` z gotowymi zmiennymi CSS.

---

## 7. Faza 1: kolory semantyczne (teraz)

**Co robimy teraz:** generujemy tylko kolory z warstwy Semantic (Light.json + Dark.json + custom-color). To daje nam jeden zestaw zmiennych CSS `--color-*` dla obu motywów.

### Jak wyglądają wynikowe zmienne CSS?

**`dist/light.css`**
```css
:root {
  /* color.background.base */
  --color-background-base-default: #ffffff;
  --color-background-base-subtle: #f4f7fa;
  --color-background-base-muted: #e8ecf0;

  /* color.background.brand */
  --color-background-brand-solid: #0b68ff;
  --color-background-brand-solidHover: #0057e0;
  --color-background-brand-solidActive: #0047be;

  /* color.text.base */
  --color-text-base-default: #384350;
  --color-text-base-subtle: #626d7a;
  --color-text-base-muted: #8c96a0;

  /* ... i kilkadziesiąt więcej */
}
```

**`dist/dark.css`**
```css
[data-theme="dark"] {
  --color-background-base-default: #232936;
  --color-background-brand-solid: #0b68ff;  /* brand może zostać ten sam */
  --color-text-base-default: #ffffff;

  /* ... */
}
```

### Jak użyć w komponencie? (Faza 1)

W tej fazie **wyjątkowo** możesz użyć tokenów semantic bezpośrednio — to punkt startowy zanim pojawią się tokeny modułowe dla wszystkich komponentów:

```css
/* Tło główne strony */
.page {
  background-color: var(--color-background-base-default);
  color: var(--color-text-base-default);
}

/* Komponent który jeszcze nie ma tokenu modułowego */
.my-component {
  background-color: var(--color-background-brand-solid);
  border-color: var(--color-border-base-default);
}
```

> ⚠️ **Pamiętaj:** tokeny semantic są tymczasowe w kodzie komponentów. Gdy komponent dostanie tokeny modułowe — migrujesz na nie.

---

## 8. Faza 2: kolory modułowe (wkrótce)

Gdy dla komponentu powstaną tokeny modułowe (warstwa 3), **migrujesz** z semantic na module:

### Przed (Faza 1 — semantic)
```css
.btn-primary {
  background-color: var(--color-background-brand-solid);
  color: var(--color-text-on-solid-brand);
}
.btn-primary:hover {
  background-color: var(--color-background-brand-solidHover);
}
```

### Po (Faza 2 — module tokens)
```css
.btn-primary {
  background-color: var(--buttons-variant-primary-bg-default);
  color: var(--buttons-variant-primary-text-default);
}
.btn-primary:hover {
  background-color: var(--buttons-variant-primary-bg-hover);
}
.btn-primary:focus {
  background-color: var(--buttons-variant-primary-bg-focus);
  border-color: var(--buttons-variant-primary-border-focus);
}
.btn-primary:disabled {
  background-color: var(--buttons-variant-primary-bg-disabled);
  color: var(--buttons-variant-primary-text-disabled);
}
```

### Jakie komponenty mają już tokeny modułowe?

| Komponent | Status | Przykładowe zmienne CSS |
|-----------|--------|------------------------|
| **Buttons** | ✅ gotowe | `--buttons-variant-primary-bg-default` |
| **Avatar** | ✅ gotowe | `--avatar-bg-default` |
| **Status Pill** | ✅ gotowe | `--status-pill-variant-success-text` |
| **Form / Radio** | ✅ gotowe | `--form-radio-bg-default` |
| Checkbox, Switch | 🚧 w toku | — |
| Inne komponenty | 📋 planowane | — |

### Wzorzec nazewnictwa zmiennych CSS

Token źródłowy (z kropkami) → zmienna CSS (z myślnikami):

```
buttons.variant.primary.bg.default   →  --buttons-variant-primary-bg-default
avatar.border.color.hover            →  --avatar-border-color-hover
status-pill.variant.success.text     →  --status-pill-variant-success-text
form.radio.bg.selected               →  --form-radio-bg-selected
```

---

## 9. Faza 3: spacing i typografia (w toku)

Tokeny spacing i typografia są w trakcie tworzenia. Kiedy będą gotowe, pojawią się w analogiczny sposób jako zmienne CSS.

### Spacing — zajawka

```css
/* Nie będziesz pisać: */
padding: 24px;

/* Tylko: */
padding: var(--modal-body-padding);        /* token modułowy */
gap: var(--form-radio-content-gap);
height: var(--list-item-height);
```

Skala bazuje na mnożniku ×4px — `space.6` = 24px. Na poziomie semantic tokeny mają nazwy t-shirt: `inset.xl` (padding uniformowy), `stack.md` (gap pionowy), `inline.sm` (gap poziomy). Do kodu zawsze trafia token modułowy.

### Typografia — zajawka

```css
/* Nie będziesz pisać: */
font-size: 12px;
font-weight: 600;
line-height: 1.4;

/* Tylko: */
font: var(--status-pill-typography);
```

---

## 10. Jak używać zmiennych CSS — zasady

### ✅ Rób tak

```css
/* Token modułowy dla komponentu który go ma */
.button--primary {
  background-color: var(--buttons-variant-primary-bg-default);
}
.button--primary:hover {
  background-color: var(--buttons-variant-primary-bg-hover);
}

/* Token semantyczny tymczasowo dla komponentu bez modułowego */
.card {
  background-color: var(--color-background-base-default);
  border-color: var(--color-border-base-default);
}
```

### ❌ Nie rób tak

```css
/* Hardkodowane wartości — zero tolerancji */
.button { background-color: #0b68ff; }

/* Primitive — nie stosujemy bezpośrednio */
.button { background-color: var(--color-blue-600); }

/* Semantic zamiast modułowego gdy moduł istnieje */
.button { background-color: var(--color-background-brand-solid); }
```

### Jak znaleźć właściwy token?

1. Sprawdź czy komponent ma tokeny modułowe w `tokens/modules/base.json`
2. Znajdź token dla odpowiedniego stanu (default, hover, focus, disabled...)
3. Zamień kropki na myślniki i dodaj `--` na początku

```
buttons.variant.primary.bg.hover  →  var(--buttons-variant-primary-bg-hover)
```

Możesz też przeszukać plik `dist/light.css` po nazwie komponentu.

---

## 11. Light Mode / Dark Mode

### Jak działa?

Transformer generuje dwa pliki CSS. Plik `light.css` ustawia wartości na `:root` (domyślnie), `dark.css` nadpisuje je dla `[data-theme="dark"]`.

### Integracja

```html
<!-- W <head> -->
<link rel="stylesheet" href="dist/light.css">
<link rel="stylesheet" href="dist/dark.css">
```

```javascript
// Przełączanie motywu
document.documentElement.setAttribute('data-theme', 'dark');
document.documentElement.removeAttribute('data-theme'); // lub: setAttribute('data-theme', 'light')
```

### Alternatywnie: media query

Jeśli wolisz automatyczne wykrywanie z preferencji systemu, możesz skonfigurować SD żeby generował:

```css
@media (prefers-color-scheme: dark) {
  :root { ... dark values ... }
}
```

Ustal z DS teamem który wariant preferujecie — `data-theme` daje większą kontrolę (toggle w UI), media query jest prostsze w implementacji.

### Ważne: tokeny modułowe też obsługują Dark mode automatycznie

Nie musisz pisać osobnych reguł dla dark mode dla tokenów modułowych — dziedziczą zmianę przez semantic:

```
Light: --buttons-variant-primary-bg-default = #0b68ff
Dark:  --buttons-variant-primary-bg-default = #0b68ff  (może być ten sam lub inny — zależy od semantic)

Twój kod:
.button { background-color: var(--buttons-variant-primary-bg-default); }
→ działa poprawnie w obu motywach bez żadnej zmiany w CSS komponentu ✅
```

---

## 12. Najczęstsze pytania (FAQ)

**Skąd mam wiedzieć które tokeny są dostępne?**
Po uruchomieniu `npm run build:tokens` przejrzyj `dist/light.css` — wszystkie dostępne zmienne są tam wymienione z komentarzami.

**Komponent nie ma tokenu modułowego — co robię?**
Użyj tymczasowo tokenu semantycznego i zgłoś DS teamowi, że komponent potrzebuje tokenizacji.

**Jak zgłosić że brakuje tokenu?**
Napisz do DS teamu (Piotr) z nazwą komponentu i opisem brakującej właściwości.

**Czy mogę użyć tokenu z innego komponentu dla swojego?**
Nie. Każdy komponent ma swoje tokeny. Jeśli brakuje — patrz punkt wyżej.

**Primitive vs Semantic vs Module — jak odróżnić?**

| Jak zaczyna się nazwa zmiennej | Warstwa | Używać? |
|-------------------------------|---------|---------|
| `--color-blue-*`, `--color-grey-*` | Primitive | ❌ |
| `--color-background-*`, `--color-text-*`, `--color-border-*` | Semantic | ⚠️ tymczasowo |
| `--buttons-*`, `--avatar-*`, `--status-pill-*`, `--form-*` | Module | ✅ tak! |

**Transformer się wysypuje — co robić?**
Sprawdź czy `$metadata.json` i `$themes.json` są poprawne, czy wszystkie pliki JSON istnieją. Najczęstszy błąd to uszkodzona referencja w tokenach (np. `{color.nieistniejacy.token}`).

**Co to jest `outputReferences: false` w konfiguracji SD?**
Opcja która każe SD rozwiązać wszystkie referencje do końcowych wartości hex/px. Bez tego w CSS byłoby `var(--color-blue-600)` zamiast `#0b68ff`. Dla produkcji zawsze `false`.

---

## Podsumowanie — co robisz jako frontend dev

```
1. SETUP (raz)
   npm install style-dictionary @tokens-studio/sd-transforms
   Skopiuj build-tokens.mjs do repo projektu
   Dodaj "build:tokens" do package.json
   npm run build:tokens → dist/light.css + dist/dark.css

2. UŻYCIE (codziennie)
   Importuj dist/light.css i dist/dark.css w aplikacji
   Używaj var(--nazwa-tokenu) zamiast hardkodowanych wartości

3. AKTUALIZACJA TOKENÓW
   git pull (w repo design-tokens)
   npm run build:tokens
   Commit nowych dist/*.css do projektu  ← lub automatyzacja przez CI

4. FAZA 1 (teraz): var(--color-background-*) dla komponentów bez tokenów modułowych
   FAZA 2 (wkrótce): var(--buttons-variant-primary-bg-default) gdy komponent tokenizowany
   FAZA 3 (później): spacing i typografia
```

---

*Dokumentacja: Synerise Design System Team · Repo: github.com/piotrzarebski2/design-tokens*
