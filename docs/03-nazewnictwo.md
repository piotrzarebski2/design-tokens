# Nazewnictwo tokenów

> Używamy ustrukturyzowanego, wieloczęściowego podejścia do tworzenia nazw tokenów. Ten dokument opisuje konwencje dla wszystkich warstw.

---

## Przegląd struktur nazewnictwa

Każda warstwa ma swoją własną strukturę nazewnictwa:

| Warstwa | Struktura | Przykład |
|---------|-----------|----------|
| **Primitives** | `{category}.{color}.{scale}` | `color.blue.600` |
| **Semantic** | `{base}.{property}.{category}.{modifier}{State}` | `color.background.brand.solidHover` |
| **Module** | `{component}.{variant}.{element}.{property}.{state}` | `buttons.variant.primary.bg.default` |

---

## Warstwa Primitives (Core)

Najprostsza struktura — opisuje CO to jest.

```
{category}.{subcategory}.{scale}

Przykłady:
color.blue.600
color.grey.800
spacing.24
border.radius.full
```

---

## Warstwa Semantic

Struktura opisująca DO CZEGO token służy.

```
{base}.{property}.{category}.{modifier}{State}
```

### Części składowe

| Część | Wartości | Opis |
|-------|----------|------|
| **Base** | `color`, `spacing`, `typography`, `border` | Typ tokena |
| **Property** | `background`, `text`, `icon`, `border`, `focus` | Gdzie używany |
| **Category** | `base`, `brand`, `success`, `warning`, `danger`, `neutral`, `overlay`, `onSolid` | Kontekst znaczeniowy |
| **Modifier** | `default`, `subtle`, `muted`, `strong`, `solid`, `disabled` | Wariant intensywności |
| **State** | `Hover`, `Active`, `Disabled` | Stan interaktywny (suffix) |

### Przykłady

```
color.background.brand.solid         ← solidne tło marki
color.background.brand.solidHover    ← hover na solidnym tle marki
color.text.base.default              ← domyślny kolor tekstu
color.text.danger.default            ← kolor tekstu błędu
color.border.base.muted              ← wyciszone obramowanie
```

### Diagram kategorii

```
┌─────────────────────────────────────────────────────────────┐
│  KATEGORIE SEMANTYCZNE                                      │
│                                                             │
│  base     → Neutralne, podstawowe (szare)                  │
│  brand    → Elementy marki, akcje główne (niebieskie)      │
│  success  → Pozytywne stany (zielone)                      │
│  warning  → Ostrzeżenia (żółte)                            │
│  danger   → Błędy, destrukcja (czerwone)                   │
│  neutral  → Drugorzędne elementy (szare)                   │
│  overlay  → Warstwy nakładkowe (półprzezroczyste)          │
│  onSolid  → Tekst na solidnych tłach (biały/ciemny)        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Warstwa Module (Component)

> 📖 **Pełna dokumentacja:** [Tokeny modułowe](./05-tokeny-modulowe.md)

Struktura opisująca GDZIE token jest używany.

```
{component}.{variant|role|context}.{element}.{property}.{state}
```

### Części składowe

| Część | Przykłady | Opis |
|-------|-----------|------|
| **Component** | `buttons`, `avatar`, `chat`, `modal` | Nazwa komponentu |
| **Variant** | `primary`, `secondary`, `danger` | Wariant wizualny |
| **Element** | `bg`, `text`, `icon`, `border`, `label` | Element wewnętrzny |
| **Property** | `color`, `width`, `radius` | Właściwość CSS |
| **State** | `default`, `hover`, `focus`, `disabled` | Stan interaktywny |

### Przykłady

```
buttons.variant.primary.bg.default
buttons.variant.primary.bg.hover
avatar.bg.default
avatar.border.color.hover
status-pill.variant.success.text
chat.container.bg
chat.input.border.color.focus
```

---

## Zasady nazewnictwa

### Separator: kropka `.`

Używamy kropki jako separatora:
```
✅ buttons.variant.primary.bg.default
❌ buttons-variant-primary-bg-default
❌ buttons/variant/primary/bg/default
```

> Kropka tworzy hierarchię drzewa w narzędziach projektowych.

### Pomijaj `.color` dla `bg`

```
✅ avatar.bg.default
❌ avatar.bg.color.default
```

### Pomijaj `.default` gdy jeden stan

```
✅ avatar.borderRadius
❌ avatar.borderRadius.default
```

### Używaj semantycznych nazw elementów

```
✅ input.label.color
✅ input.placeholder.color
❌ input.text1.color
❌ input.text2.color
```

---

## Stosowanie tokenów — Do / Don't

### ✅ Do: Wybieraj tokeny według znaczenia

```
┌───────────────────────────────────────┐
│  TOKEN: buttons.variant.primary.bg    │
│                                       │
│  ┌─────────────────────────────────┐  │
│  │█████████████████████████████████│  │
│  │██        Zapisz              ██│  │
│  │█████████████████████████████████│  │
│  └─────────────────────────────────┘  │
│                                       │
│  ✅ Użyto tokena buttona dla buttona │
└───────────────────────────────────────┘
```

### ❌ Don't: Nie wybieraj tylko po kolorze

```
┌───────────────────────────────────────┐
│  TOKEN: color.background.brand.solid  │
│         (semantic, nie module!)       │
│                                       │
│  ┌─────────────────────────────────┐  │
│  │█████████████████████████████████│  │
│  │██        Zapisz              ██│  │
│  │█████████████████████████████████│  │
│  └─────────────────────────────────┘  │
│                                       │
│  ❌ Użyto tokena semantic bezpośrednio│
│     Gdy zmieni się semantic —         │
│     zmieni się WSZYSTKO!              │
└───────────────────────────────────────┘
```

---

## Transformacja między platformami

Token źródłowy jest automatycznie konwertowany do formatu odpowiedniego dla platformy:

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  ŹRÓDŁO (Token Studio)                                      │
│  buttons.variant.primary.bg.default                        │
│                     │                                       │
│        ┌───────────┼───────────┬───────────┐               │
│        │           │           │           │               │
│        ▼           ▼           ▼           ▼               │
│   ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐          │
│   │  Figma  │ │   CSS   │ │   iOS   │ │ Android │          │
│   └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘          │
│        │           │           │           │               │
│   buttons/    --buttons-  ButtonsVariant  buttonsVariant   │
│   variant/    variant-    PrimaryBg       PrimaryBg        │
│   primary/    primary-bg- Default         Default          │
│   bg/default  default                                      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Szybka ściągawka

### Primitives
```
color.{kolor}.{skala}           → color.blue.600
spacing.{wartość}               → spacing.24
```

### Semantic
```
color.{property}.{category}.{modifier}{State}
→ color.background.brand.solidHover
```

### Module
```
{component}.{variant}.{element}.{property}.{state}
→ buttons.variant.primary.bg.default
```

---

## Następne kroki

➡️ [Tokeny modułowe](./05-tokeny-modulowe.md) — pełna konwencja nazewnictwa komponentów

➡️ [Kolory](./04-kolory.md) — przegląd wszystkich tokenów kolorów
