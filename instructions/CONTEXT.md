# CONTEXT — Synerise Design System Tokens

> Ten plik jest fundamentem każdej rozmowy. Opisuje projekt, architekturę, zasady i sposób pracy z Claude.

---

## Projekt

**Produkt:** Synerise Design System v2  
**Repo tokenów:** https://github.com/piotrzarebski2/design-tokens  
**Source of truth:** Tokeny JSON w repo → Token Studio (Figma) konsumuje przez sync  
**Dokumentacja:** folder `docs/` w repo  
**Instrukcje dla Claude:** folder `instructions/` w repo

---

## Mój stack roboczy

| Narzędzie | Rola |
|-----------|------|
| **Token Studio** (Figma plugin) | Edytor tokenów, sync z GitHub |
| **GitHub repo** (`design-tokens`) | Source of truth dla tokenów i dokumentacji |
| **Claude.ai (Projects)** | Analiza, projektowanie, dokumentacja tokenów |
| **Claude Code** (terminal) | Aplikowanie gotowych tokenów na Figmę (późniejszy etap) |
| **Figma CLI / figma-ds-cli** | CDP-based Figma automation (późniejszy etap) |

---

## Architektura tokenów — model 3-warstwowy

```
PRIMITIVES  →  SEMANTIC  →  MODULE (aplikujemy na produkt)
```

### Warstwa 1: Primitives
Surowe wartości zorganizowane w skale. Opisują **CO to jest**.

```
color.{kolor}.{skala}
→ color.blue.600 = #0b68ff
```

**Paleta kolorów:**
- `color.blue.*` (50–900) — brand, główna akcja
- `color.grey.*` (50–900) — base, neutral, dark mode
- `color.green.*` (600, 700) — success
- `color.red.*` (600, 700) — danger
- `color.yellow.*` (500, 700) — warning
- Dodatkowe: pink, mars, orange, fern, cyan, purple, violet

**Kluczowe wartości:**
- `color.blue.600` = `#0b68ff` ← główny brand
- `color.grey.800` = `#384350` ← główny tekst (light)
- `color.grey.900` = `#232936` ← dark mode bg

---

### Warstwa 2: Semantic
Tokeny z **znaczeniem i przeznaczeniem**. Zmieniają się między light/dark mode.

```
color.{property}.{category}.{modifier}{State}
→ color.background.brand.solidHover
```

**Properties:** `background`, `text`, `icon`, `border`  
**Categories:** `base`, `brand`, `success`, `warning`, `danger`, `neutral`, `inverse`, `onSolid`  
**Modifiers:** `default`, `subtle`, `muted`, `strong`, `solid`, `inverted`  
**States (suffix):** `Hover`, `Active`, `Focus`, `Disabled`

> Stan w semantic jest **przyrostkiem CamelCase sklejonym z modyfikatorem**: `solidHover`, `defaultHover`. Nigdy nie jest osobnym segmentem.

**Zasada specjalna — tokeny achromatyczne:**
- `achromatic.max` — biały w light, czarny w dark (czyste odwrócenie)
- `achromatic.min` — czarny w light, biały w dark
- Nie używamy `neutral` dla tych przypadków (kolizja z kategorią semantyczną)

---

### Warstwa 3: Module (jedyna którą aplikujemy)

```
{component}.{variant|role|context}.{element}.{property}.{state}
→ buttons.variant.primary.bg.default
```

**Reguły nazewnictwa:**
- Separator: kropka `.` (nie myślnik, nie slash)
- Pomijaj `.color` dla `bg` → `avatar.bg.default` (nie `avatar.bg.color.default`)
- Pomijaj `.default` gdy element ma tylko jeden stan → `avatar.borderRadius`
- `container` dla głównego kontenera organizmu → `chat.container.bg`
- `text` tylko gdy brak lepszej nazwy semantycznej (preferuj `label`, `description`, `placeholder`)

**Elementy:** `bg`, `border`, `label`, `placeholder`, `description`, `text`, `icon`  
**Stany:** `default`, `hover`, `focus`, `active`, `selected`, `disabled`, `error`, `validated`

> Stan w module jest **osobnym segmentem lowercase na końcu nazwy**: `.bg.hover`, `.border.color.disabled`. Nigdy nie skleja się go z elementem ani nie stosuje CamelCase.

**Zakaz mieszania konwencji:**
```
❌ buttons.variant.primary.bg.defaultHover   ← styl semantic w module
❌ color.background.brand.solid.hover        ← styl module w semantic
✅ color.background.brand.solidHover         ← poprawnie: semantic
✅ buttons.variant.primary.bg.hover          ← poprawnie: module
```

---

## Kluczowa zasada — NIGDY nie aplikuj semantic/primitives bezpośrednio

```
❌ color.blue.600                        (primitive)
❌ color.background.brand.solid          (semantic)  
✅ buttons.variant.primary.bg.default    (module)
```

**Dlaczego?** Token modułowy pozwala zmienić jeden komponent bez wpływu na resztę systemu. Semantic zmienione = zmienia się wszystko co go używa.

---

## Komponenty z gotowymi tokenami

### Buttons
```
buttons.variant.primary.bg.{default|hover|focus|active|disabled}
buttons.variant.primary.text.{default|hover|disabled}
buttons.variant.primary.icon.default
buttons.variant.primary.border.{default|focus}
buttons.variant.primary-danger.bg.*
buttons.variant.secondary.*
buttons.variant.tertiary.*
```

### Avatar
```
avatar.size.{small|medium|large|xlarge}
avatar.borderRadius  → {border.radius.full}
avatar.borderWidth   → {border.default}
avatar.bg.{default|hover|disabled}
avatar.text.{default|hover|disabled}
avatar.border.color.{default|hover|disabled}
avatar.disabled.opacity
```

### Status Pill
```
status-pill.size.height
status-pill.size.padding.{horizontal|vertical}
status-pill.borderRadius
status-pill.borderWidth
status-pill.typography
status-pill.variant.{neutral|success|warning|error}.text
status-pill.variant.{neutral|success|warning|error}.border.{solid|dashed}
```

---

## Struktura plików w repo

```
design-tokens/
├── tokens/                          ← JSON tokenów (Token Studio sync)
│   ├── $metadata.json               ← kolejność tokenSetów
│   ├── $themes.json                 ← mapowanie tematów + referencje Figma Variables
│   ├── primitives/
│   │   └── core.json                ← surowe wartości (color, space, typography, radius, shadows…)
│   ├── semantic/
│   │   ├── Light.json               ← tokeny semantyczne — tryb jasny
│   │   ├── Dark.json                ← tokeny semantyczne — tryb ciemny
│   │   └── custom-color/            ← semantic per kolor (blue, grey, red, green, yellow…)
│   │       └── *.json
│   ├── surface/
│   │   └── base.json                ← tokeny surface (wzorce powierzchni: base, raised, floating…)
│   └── modules/
│       └── base.json                ← tokeny modułowe wszystkich komponentów
├── docs/                            ← dokumentacja tokenów (MD)
│   ├── 00-spis-tresci.md
│   ├── 01-wprowadzenie.md
│   ├── 02-warstwy.md
│   ├── 03-nazewnictwo.md
│   ├── 04-kolory.md
│   ├── 05-tokeny-modulowe.md
│   └── 06-wyszukiwanie-i-tworzenie-tokenow.md
└── instructions/                    ← instrukcje dla Claude
    ├── CONTEXT.md                   ← ten plik
    └── workflow-color-analysis.md
```

---

## Aktywne wątki do pracy

### ✅ Gotowe / stabilne
- Architektura 3-warstwowa (primitives → semantic → module)
- Konwencja nazewnictwa tokenów kolorystycznych
- Tokeny modułowe: Buttons, Avatar, Status Pill
- Dokumentacja (docs 01–06)
- Tokeny achromatyczne (`achromatic.max/min`)

### 🚧 W toku / do zrobienia
1. **Analiza tokenów kolorystycznych vs Figma** — komponent po komponencie, walidacja poprawności wartości i referencji
2. **Tokeny surface** — powtarzające się schematy kolorystyczne dla surface zebrać w jeden blok tokenów (zamiast każdy moduł aplikuje od zera)
3. **Tokeny odległościowe** (spacing, padding) — wymagają przeprojektowania od podstaw; poprzednie podejście przerwane; nowe podejście do ustalenia na podstawie Figmy

---

## Jak pracować ze mną (zasady dla Claude)

### Zawsze przed zmianą tokenów
1. Przedstaw plan zmian (co dodajesz, co modyfikujesz, dlaczego)
2. Poczekaj na akceptację
3. Dopiero potem proponuj konkretny JSON

### Przy analizie komponentu z Figmy
1. Przeanalizuj wszystkie właściwości wizualne (kolory, borderRadius, spacing)
2. Sprawdź czy istnieje już odpowiedni token semantyczny (użyj matrycy z doc 06)
3. Jeśli brakuje tokena semantycznego — zaproponuj go i zapytaj o akceptację
4. Zaproponuj kompletny zestaw tokenów modułowych dla komponentu
5. Sprawdź spójność z już istniejącymi tokenami w repo

### Przy tworzeniu nowych tokenów semantycznych
- Nazwa musi pasować do konwencji: `color.{property}.{category}.{modifier}{State}`
- Wartość musi być referencją do primitive (nie hardcoded hex)
- Wymagana wersja light i dark
- Wymagany `$description`
- Zaktualizuj matrycę w doc 06

### Przy dokumentacji
- Styl: techniczny, po polsku
- Używaj ASCII diagramów do ilustrowania przepływów
- Tabele dla mapowań semantic → module
- Zachowaj spójność z istniejącymi plikami docs

### Czego nie robisz bez pytania
- Nie zmieniasz wartości primitives
- Nie renameujesz istniejących tokenów (breaking change!)
- Nie dodajesz tokenów semantic bez uzgodnienia
- Nie modyfikujesz struktury folderów repo

---

## Matryca semantyczna — skrót

### Background
`color.background.{base|brand|success|warning|danger|neutral}.{default|subtle|muted|solid|solidHover|solidActive|...}`

### Border  
`color.border.{base|brand|success|warning|danger|neutral|inverse|onSolid}.{default|subtle|solid|...}`

### Text  
`color.text.{base|brand|success|warning|danger|neutral|onSolid}.{default|subtle|muted|...}`

### Icon  
`color.icon.{base|brand|success|warning|danger|neutral|onSolid}.{default|subtle|muted|...}`

---

*Wersja: 1.0 — Marzec 2026*  
*Repo: https://github.com/piotrzarebski2/design-tokens*
