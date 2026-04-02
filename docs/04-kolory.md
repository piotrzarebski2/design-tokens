# Kolory

> Przegląd tokenów kolorów w Synerise Design System — od primitives przez semantic do module.

---

## Przepływ wartości kolorów

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  PRIMITIVES                                                 │
│  color.blue.600 = #0b68ff                                  │
│         │                                                   │
│         ▼                                                   │
│  SEMANTIC                                                   │
│  color.background.brand.solid = {color.blue.600}           │
│         │                                                   │
│         ▼                                                   │
│  MODULE                                                     │
│  buttons.variant.primary.bg.default =                      │
│         {color.background.brand.solid}                      │
│         │                                                   │
│         ▼                                                   │
│  RESOLVED: #0b68ff                                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

> ⚠️ **Pamiętaj:** Na produkt aplikujemy tylko tokeny **Module** — nigdy Primitives ani Semantic bezpośrednio.

---

## Paleta Primitives (Core)

Bazowe kolory — fundament dla warstwy semantic.

### Blue (Niebieski) — Brand

| Token | Wartość |
|-------|---------|
| `color.blue.50` | `#f4faff` |
| `color.blue.100` | `#d9eeff` |
| `color.blue.200` | `#bce1ff` |
| `color.blue.300` | `#8bcaff` |
| `color.blue.400` | `#4da7fe` |
| `color.blue.500` | `#238afe` |
| `color.blue.600` | `#0b68ff` ← główny brand |
| `color.blue.700` | `#0044d9` |
| `color.blue.800` | `#00039e` |
| `color.blue.900` | `#01026e` |

### Grey (Szary) — Base/Neutral

| Token | Wartość |
|-------|---------|
| `color.grey.50` | `#f9fafb` |
| `color.grey.100` | `#f3f5f6` |
| `color.grey.200` | `#e9edee` |
| `color.grey.300` | `#dbe0e3` |
| `color.grey.400` | `#b5bdc3` |
| `color.grey.500` | `#949ea6` |
| `color.grey.600` | `#6a7580` |
| `color.grey.700` | `#57616d` |
| `color.grey.800` | `#384350` ← główny tekst |
| `color.grey.900` | `#232936` ← dark mode bg |

### Green (Zielony) — Success

| Token | Wartość |
|-------|---------|
| `color.green.600` | `#54cb0b` ← success |
| `color.green.700` | `#399903` |

### Red (Czerwony) — Danger

| Token | Wartość |
|-------|---------|
| `color.red.600` | `#f52922` ← danger |
| `color.red.700` | `#cf1413` |

### Yellow (Żółty) — Warning

| Token | Wartość |
|-------|---------|
| `color.yellow.500` | `#ffc300` ← warning |
| `color.yellow.700` | `#eda600` |

---

## Tokeny Semantic — Background

Tokeny tła pogrupowane według kategorii semantycznych.

### Base

| Token Semantic | Wartość | Użycie |
|----------------|---------|--------|
| `color.background.base.default` | `#FFFFFF` | Główne tło |
| `color.background.base.subtle` | `{grey.50}` | Subtelne tło |
| `color.background.base.muted` | `{grey.100}` | Przytłumione |
| `color.background.base.strong` | `{grey.400}` | Mocne |
| `color.background.base.disabled` | `{grey.100}` | Wyłączone |

### Brand

| Token Semantic | Wartość | Użycie |
|----------------|---------|--------|
| `color.background.brand.subtle` | `{blue.50}` | Subtelne brandowe |
| `color.background.brand.solid` | `{blue.600}` | Solidne brandowe |
| `color.background.brand.solidHover` | `{blue.500}` | Hover |
| `color.background.brand.solidActive` | `{blue.700}` | Active |

### Success / Warning / Danger

| Kategoria | Subtle | Solid |
|-----------|--------|-------|
| **Success** | `{green.50}` | `{green.600}` |
| **Warning** | `{yellow.50}` | `{yellow.500}` |
| **Danger** | `{red.50}` | `{red.600}` |

### Overlay

| Token | Zastosowanie |
|-------|--------------|
| `overlay.default` | Maska modali (`rgba(35,41,54,0.5)`) |
| `overlay.solid` | Tło tooltipu — `grey.800 @ 90%` *(tymczasowe)* |

### Translucent *(tymczasowe)*

Kategoria `color.background.translucent.*` dostarcza półprzezroczyste tła przeznaczone do użycia na kolorowych lub ciemnych powierzchniach (np. przyciski tertiary na ciemnym headerze). Używa składni alpha modifier zamiast notacji hex-alpha suffix.

| Token | Opacity | Zastosowanie |
|-------|---------|--------------|
| `translucent.none` | 0% | Brak tła (transparent) |
| `translucent.subtle` | 10% | Delikatna warstwa |
| `translucent.subtleHover` | 15% | Hover delikatny |
| `translucent.subtleActive` | 25% | Active delikatny |
| `translucent.muted` | 10% | Stonowana warstwa |
| `translucent.mutedHover` | 15% | Hover stonowany |
| `translucent.mutedActive` | 25% | Active stonowany |
| `translucent.strong` | 35% | Wyraźna warstwa |
| `translucent.strongHover` | 25% | Hover wyraźny |

> ⚠️ Kategoria tymczasowa — zostanie wycofana po pełnej tokenizacji warstwy modules.

### Supply / Service / Entity

Trzy dodatkowe kategorie dla kontekstów biznesowych systemu Synerise:

| Kategoria | Primitive family | Kontekst | Subtle | Solid |
|-----------|-----------------|----------|--------|-------|
| **Supply** | `cyan` | Zasilanie danymi / data feeds / integracje | `{cyan.50}` | `{cyan.600}` |
| **Service** | `purple` | Usługi / API / połączenia zewnętrzne | `{purple.50}` | `{purple.600}` |
| **Entity** | `violet` | Encje / rekordy / obiekty danych | `{violet.50}` | `{violet.600}` |

Każda kategoria ma pełen zestaw tokenów analogiczny do `success`:

```
color.background.{supply|service|entity}.subtle
color.background.{supply|service|entity}.subtleHover
color.background.{supply|service|entity}.solid
color.background.{supply|service|entity}.solidHover
color.background.{supply|service|entity}.solidActive

color.text.{supply|service|entity}.default
color.border.{supply|service|entity}.default
color.border.{supply|service|entity}.subtle
color.border.{supply|service|entity}.strong
color.icon.{supply|service|entity}.default
```

---

## Mapowanie: Semantic → Module

Jak tokeny semantic są używane w tokenach modułowych:

### Buttons

```
┌─────────────────────────────────────────────────────────────┐
│  SEMANTIC                      MODULE                       │
│                                                             │
│  color.background.brand.solid  →  buttons.variant.primary.  │
│                                   bg.default                │
│                                                             │
│  color.background.brand.       →  buttons.variant.primary.  │
│  solidHover                       bg.hover                  │
│                                                             │
│  color.text.onSolid.brand      →  buttons.variant.primary.  │
│                                   text.default              │
│                                                             │
│  color.background.danger.solid →  buttons.variant.          │
│                                   primary-danger.bg.default │
└─────────────────────────────────────────────────────────────┘
```

### Avatar

```
┌─────────────────────────────────────────────────────────────┐
│  SEMANTIC                      MODULE                       │
│                                                             │
│  color.background.base.muted   →  avatar.bg.default        │
│  color.background.base.        →  avatar.bg.hover          │
│  mutedHover                                                 │
│  color.text.base.muted         →  avatar.text.default      │
│  color.border.base.default     →  avatar.border.color.     │
│                                   default                   │
│  color.background.brand.solid  →  avatar.border.color.hover│
└─────────────────────────────────────────────────────────────┘
```

### Status Pill

```
┌─────────────────────────────────────────────────────────────┐
│  SEMANTIC                      MODULE                       │
│                                                             │
│  color.text.success.default    →  status-pill.variant.     │
│                                   success.text              │
│  color.border.success.default  →  status-pill.variant.     │
│                                   success.border.solid      │
│                                                             │
│  color.text.danger.default     →  status-pill.variant.     │
│                                   error.text                │
│  color.border.danger.default   →  status-pill.variant.     │
│                                   error.border.solid        │
└─────────────────────────────────────────────────────────────┘
```

---

## Przykłady użycia tokenów modułowych

### Przycisk Primary

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │█████████████████████████████████████████████████████████│  │
│  │██                    Zapisz                          ██│  │
│  │█████████████████████████████████████████████████████████│  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
│  TOKENY MODUŁOWE:                                          │
│  • buttons.variant.primary.bg.default     → #0b68ff        │
│  • buttons.variant.primary.text.default   → #FFFFFF        │
│  • buttons.variant.primary.border.default → transparent    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Avatar

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│     ┌─────────┐                                             │
│     │         │                                             │
│     │   AB    │  ← tekst: avatar.text.default              │
│     │         │     bg: avatar.bg.default                  │
│     └─────────┘     border: avatar.border.color.default    │
│                                                             │
│  TOKENY MODUŁOWE:                                          │
│  • avatar.bg.default           → {grey.100} → #f3f5f6     │
│  • avatar.text.default         → {grey.600} → #6a7580     │
│  • avatar.border.color.default → {grey.300} → #dbe0e3     │
│  • avatar.borderRadius         → 9999px (full)             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Status Pill (Success)

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│     ┌──────────────────┐                                    │
│     │  ✓ Completed     │ ← text: status-pill.variant.      │
│     └──────────────────┘        success.text               │
│                            border: status-pill.variant.     │
│                                   success.border.solid      │
│                                                             │
│  TOKENY MODUŁOWE:                                          │
│  • status-pill.variant.success.text         → #399903      │
│  • status-pill.variant.success.border.solid → #54cb0b      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Light vs Dark Mode

Tokeny semantic zmieniają wartości w zależności od motywu. Tokeny module dziedziczą te zmiany automatycznie:

| Token Semantic | Light | Dark |
|----------------|-------|------|
| `color.background.base.default` | `#FFFFFF` | `#232936` |
| `color.text.base.default` | `#384350` | `#FFFFFF` |
| `color.background.brand.solid` | `#0b68ff` | `#0b68ff` |

**Efekt na tokeny modułowe:**

```
                    LIGHT MODE           DARK MODE

avatar.bg.default   #f3f5f6              (ciemniejszy)
                        ↑                     ↑
                        │                     │
                    ┌───┴─────────────────────┴───┐
                    │   color.background.base.muted   │
                    │   {grey.100}        {grey.800}  │
                    └─────────────────────────────────┘
```

---

## Dodatkowe palety

System zawiera również dodatkowe palety dla specyficznych zastosowań:

| Paleta | Przeznaczenie |
|--------|---------------|
| `color.pink.*` | Akcenty, tagi |
| `color.mars.*` | Alternatywne czerwone |
| `color.orange.*` | Ciepłe akcenty |
| `color.fern.*` | Alternatywne zielone |
| `color.cyan.*` | Chłodne akcenty |
| `color.purple.*` | Kategorie, akcenty |
| `color.violet.*` | Promocje |

---

## Zasady stosowania

### ✅ Rób tak

```
// Użyj tokena modułowego
background-color: var(--buttons-variant-primary-bg-default);
```

### ❌ Nie rób tak

```
// Nie używaj semantic bezpośrednio
background-color: var(--color-background-brand-solid);

// Nie używaj primitives
background-color: var(--color-blue-600);

// Nie używaj wartości hex
background-color: #0b68ff;
```

---

## Następne kroki

➡️ [Tokeny modułowe](./05-tokeny-modulowe.md) — pełna konwencja nazewnictwa komponentów

➡️ [Warstwy](./02-warstwy.md) — dlaczego używamy tokenów modułowych
