# Warstwy tokenów

> Tokeny w Synerise Design System są zorganizowane w **3-warstwowy model**, który zapewnia strukturę, skalowalność i łatwość zarządzania decyzjami projektowymi.

---

## Model warstwowy

Każda warstwa buduje na poprzedniej, przechodząc od surowych wartości do konkretnych zastosowań w komponentach:

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│     0. Surowe wartości                                      │
│     ┌─────────────────┐                                     │
│     │    #0b68ff      │  ← Nie używaj bezpośrednio         │
│     └────────┬────────┘                                     │
│              │                                              │
│              ▼                                              │
│     1. PRIMITIVES (Core)                                    │
│     ┌─────────────────┐                                     │
│     │  color.blue.600 │  ← ❌ Nie aplikuj na produkt       │
│     └────────┬────────┘                                     │
│              │                                              │
│              ▼                                              │
│     2. SEMANTIC                                             │
│     ┌──────────────────────────────┐                        │
│     │ color.background.brand.solid │  ← ❌ Nie aplikuj     │
│     └────────┬─────────────────────┘                        │
│              │                                              │
│              ▼                                              │
│     3. MODULE (Component)                                   │
│     ┌──────────────────────────────────────┐                │
│     │ buttons.variant.primary.bg.default   │  ← ✅ APLIKUJ │
│     └────────┬─────────────────────────────┘                │
│              │                                              │
│              ▼                                              │
│     ┌─────────────────┐                                     │
│     │     Button      │                                     │
│     └─────────────────┘                                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Opis warstw

### 0. Surowe wartości (Raw Values)

To nieprzetworzone wartości, takie jak `#0b68ff` dla koloru czy `16px` dla odstępu. Są niezależne od platformy i czysto funkcjonalne.

**Przykłady:**
- `#0b68ff` — kolor niebieski
- `16px` — odstęp
- `Inter` — nazwa fontu

> ⚠️ **Nigdy nie używaj surowych wartości w designie ani kodzie.** Zawsze używaj tokenów modułowych.

---

### 1. Primitives (Core Tokens)

Tokeny Primitives organizują surowe wartości w **neutralne, wielokrotnie używane zmienne**. Stanowią fundament dla wszystkich decyzji projektowych, ale pozostają **wolne od kontekstu**.

**Charakterystyka:**
- Opisują **CO to jest** (np. "niebieski 600")
- Nie mówią DO CZEGO służy
- Są bazą dla tokenów semantycznych

**Przykłady z Synerise:**

| Token | Wartość |
|-------|---------|
| `color.blue.600` | `#0b68ff` |
| `color.grey.800` | `#384350` |
| `color.green.600` | `#54cb0b` |
| `color.red.600` | `#f52922` |

> ❌ **Nie aplikuj na produkt** — Primitives są źródłem dla warstwy semantycznej.

---

### 2. Semantic Tokens

Warstwa semantyczna definiuje **przeznaczenie tokenów** w sposób ogólny i znaczeniowy. To stosunkowo **mała grupa tokenów**, która jest łatwa do dostosowania pod dark mode lub stylistykę klienta.

**Charakterystyka:**
- Opisują **DO CZEGO służy** (np. "tło dla elementów marki")
- Zmieniają wartość w zależności od motywu (Light/Dark)
- Są **bazą źródłową** dla tokenów modułowych
- Relatywnie niewiele tokenów — łatwe w utrzymaniu

**Struktura nazwy semantic:**
```
color.{property}.{category}.{modifier}{State}

Przykład: color.background.brand.solidHover
```

**Przykłady z Synerise:**

| Token | Light | Dark | Przeznaczenie |
|-------|-------|------|---------------|
| `color.background.brand.solid` | `#0b68ff` | `#0b68ff` | Solidne tło marki |
| `color.background.base.default` | `#FFFFFF` | `#232936` | Domyślne tło |
| `color.text.base.default` | `#384350` | `#FFFFFF` | Domyślny tekst |

> ❌ **Nie aplikuj na produkt** — Semantic są źródłem dla tokenów modułowych.

---

### 3. Module Tokens (Component Tokens)

To **warstwa, którą aplikujemy na produkt**. Tokeny modułowe są przypisane do konkretnych komponentów i czerpią wartości z warstwy semantycznej.

**Charakterystyka:**
- Opisują **GDZIE jest używane** (np. "tło głównego przycisku")
- Każdy komponent ma swoje dedykowane tokeny
- Umożliwiają **lokalne decyzje projektowe** bez wpływu na cały system
- Wspierają budowę warstwową: atoms → molecules → organisms

**Struktura nazwy module:**
```
{component}.{variant|role|context}.{element}.{property}.{state}

Przykład: buttons.variant.primary.bg.default
```

**Przykłady z Synerise:**

| Token | Wartość (referencja) |
|-------|----------------------|
| `buttons.variant.primary.bg.default` | `{color.background.brand.solid}` |
| `buttons.variant.primary.text.default` | `{color.text.onSolid.brand}` |
| `avatar.bg.default` | `{color.background.base.muted}` |
| `status-pill.variant.success.text` | `{color.text.success.default}` |

> ✅ **Aplikuj na produkt** — to jedyna warstwa do użycia w designie i kodzie.

---

## Dlaczego nie aplikujemy Semantic bezpośrednio?

### Problem z bezpośrednim użyciem semantic:

```
❌ Bezpośrednie użycie semantic:

Button A:  color.background.brand.solid
Button B:  color.background.brand.solid
Card:      color.background.brand.solid
Badge:     color.background.brand.solid

Chcesz zmienić TYLKO kolor buttona?
→ Musisz zmienić semantic
→ Zmienia się WSZYSTKO co używa brand.solid 😱
```

### Rozwiązanie z tokenami modułowymi:

```
✅ Użycie tokenów modułowych:

buttons.variant.primary.bg.default  = {brand.solid}
card.header.bg                      = {brand.solid}
badge.variant.info.bg               = {brand.solid}

Chcesz zmienić TYLKO kolor buttona?
→ Zmień buttons.variant.primary.bg.default = {brand.solidHover}
→ Tylko buttony się zmieniają ✅
→ Card i Badge bez zmian ✅
```

---

## Przepływ wartości

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  PRIMITIVE                                                  │
│  color.blue.600 = #0b68ff                                  │
│         │                                                   │
│         │ referencja                                        │
│         ▼                                                   │
│  SEMANTIC                                                   │
│  color.background.brand.solid = {color.blue.600}           │
│         │                                                   │
│         │ referencja                                        │
│         ▼                                                   │
│  MODULE                                                     │
│  buttons.variant.primary.bg.default =                      │
│         {color.background.brand.solid}                      │
│         │                                                   │
│         │ aplikacja                                         │
│         ▼                                                   │
│  ┌───────────────────┐                                      │
│  │████████████████████│                                      │
│  │██    Button     ██│  Resolved: #0b68ff                  │
│  │████████████████████│                                      │
│  └───────────────────┘                                      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Light Mode vs Dark Mode

Ten sam token semantyczny może mieć różne wartości w zależności od aktywnego motywu. Tokeny modułowe automatycznie dziedziczą te zmiany:

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  LIGHT MODE                      DARK MODE                  │
│                                                             │
│  color.background.base.default   color.background.base.default
│  = #FFFFFF                       = #232936                  │
│         │                               │                   │
│         └───────────┬───────────────────┘                   │
│                     │                                       │
│                     ▼                                       │
│         avatar.bg.default = {color.background.base.muted}  │
│                     │                                       │
│                     ▼                                       │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  Avatar wygląda inaczej w Light i Dark,             │    │
│  │  ale używa tego samego tokena modułowego!           │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Porównanie warstw

| Aspekt | Primitives | Semantic | Module |
|--------|------------|----------|--------|
| **Kontekst** | Brak | Ogólny | Specyficzny |
| **Opisuje** | CO to jest | DO CZEGO | GDZIE użyte |
| **Przykład** | `color.blue.600` | `background.brand.solid` | `buttons.primary.bg` |
| **Zmienia się z motywem?** | Nie | Tak | Tak (przez semantic) |
| **Aplikować na produkt?** | ❌ | ❌ | ✅ |
| **Ile tokenów?** | ~200 | ~100 | ~500+ |

---

## Podsumowanie

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  1. PRIMITIVES    →   Surowe wartości, paleta bazowa       │
│                       (nie aplikuj)                         │
│                                                             │
│  2. SEMANTIC      →   Znaczenie, kontekst, theming         │
│                       (nie aplikuj)                         │
│                                                             │
│  3. MODULE        →   Konkretne komponenty                 │
│                       (APLIKUJ NA PRODUKT!)                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Następne kroki

➡️ [Nazewnictwo tokenów](./03-nazewnictwo.md) — jak czytać i tworzyć nazwy tokenów

➡️ [Tokeny modułowe](./05-tokeny-modulowe.md) — szczegółowa konwencja komponentów
