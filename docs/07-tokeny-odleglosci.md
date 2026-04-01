# Tokeny odległości (Spacing)

> Przegląd tokenów odstępów w Synerise Design System — od primitives przez semantic do module.

---

## Przepływ wartości odstępów

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  PRIMITIVES                                                 │
│  space.6 = 24px                                            │
│         │                                                   │
│         ▼                                                   │
│  SEMANTIC                                                   │
│  inset.xl = {space.6}                                      │
│         │                                                   │
│         ▼                                                   │
│  MODULE                                                     │
│  modal.header.padding.x = {inset.xl}                       │
│         │                                                   │
│         ▼                                                   │
│  RESOLVED: 24px                                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

> ⚠️ **Pamiętaj:** Na produkt aplikujemy tylko tokeny **Module** — nigdy Primitives ani Semantic bezpośrednio.

---

## Primitives — skala space

Bazowe wartości odstępów. Jednostka = 4px. Nazwa tokenu = mnożnik.

### Skala space (mnożnik × 4px)

| Token | Mnożnik | Wartość | Zastosowanie |
|-------|---------|---------|-------------|
| `space.025` | 0.25× | 1px | Hairline: separatory, obramowania |
| `space.05` | 0.5× | 2px | Micro offset: focus ring |
| `space.1` | 1× | 4px | XS: gęste UI, offsety ikon |
| `space.2` | 2× | 8px | SM: kompaktowe paddingi |
| `space.3` | 3× | 12px | MD-S: standardowe inputy, nav items |
| `space.4` | 4× | 16px | MD: standardowy padding |
| `space.5` | 5× | 20px | MD-L: modal header padding |
| `space.6` | 6× | 24px | LG: card padding, modal body |
| `space.8` | 8× | 32px | XL: sekcje, duże karty |
| `space.10` | 10× | 40px | 2XL: layout gap |
| `space.12` | 12× | 48px | 3XL: duże sekcje |
| `space.16` | 16× | 64px | 4XL: layout |
| `space.20` | 20× | 80px | 5XL: avatar large |
| `space.32` | 32× | 128px | 7XL: panel width base |

> `space.025` i `space.05` to wartości **micro** — używane wyłącznie dla separatorów i focus ringów, nie dla spacingu UI.

### Skala size (wysokości komponentów)

Komponenty o stałej wysokości. Oddzielona od `space` bo opisuje **rozmiar elementu**, nie odstęp.

| Token | Wartość | Komponenty |
|-------|---------|-----------|
| `size.1` | 24px | Badge, status pill, small indicator |
| `size.2` | 32px | Compact list item, nav item, small input |
| `size.3` | 40px | Standard input, button, icon area |
| `size.4` | 48px | Large button, dropdown footer, card tab |
| `size.5` | 56px | Navbar, large list item |
| `size.6` | 64px | App menu zone, large structural |
| `size.7` | 80px | Page header area |

---

## Semantic — tokeny z intencją

Tokeny semantyczne opisują **po co** jest odstęp, nie ile wynosi. Używają nazewnictwa t-shirt.

### inset — padding kontenera (wszystkie 4 strony równe)

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   inset.xl                                                 │
│   ┌───────────────────────────────────────────────────┐    │
│   │ ◄──────────────── 24px ───────────────────────►  │    │
│   │                                                   │    │
│   │  ▲                                           ▲   │    │
│   │  │ 24px                                  24px│   │    │
│   │  ▼                                           ▼   │    │
│   │                                                   │    │
│   └───────────────────────────────────────────────────┘    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

| Token | Wartość primitywu | px | Kiedy używać |
|-------|-------------------|-----|-------------|
| `inset.xs` | `{space.1}` | 4px | Gęste UI: tagi, badges, tiny chips |
| `inset.sm` | `{space.2}` | 8px | Kompaktowe: badge padding, małe buttony |
| `inset.md` | `{space.3}` | 12px | Standard: inputy, nav items, list cells |
| `inset.lg` | `{space.4}` | 16px | Wygodne: card content, sidebar items |
| `inset.xl` | `{space.6}` | 24px | Przestronne: modal body, card padding duże |
| `inset.2xl` | `{space.8}` | 32px | Bardzo duże sekcje |

> `inset.*` zawsze oznacza **jednakowy padding na wszystkich 4 stronach**. Dla paddingu nieuniformowego (X ≠ Y) definiujesz osobne tokeny w warstwie modułowej: `component.padding.x` i `component.padding.y`.

---

### stack — gap pionowy (column flex, margin-bottom)

```
┌───────────────────┐
│    Element A      │
└───────────────────┘
         │
    stack.md (12px)
         │
┌───────────────────┐
│    Element B      │
└───────────────────┘
```

| Token | Wartość | px | Kiedy używać |
|-------|---------|-----|-------------|
| `stack.xs` | `{space.1}` | 4px | Ikona + label, helper text pod inputem |
| `stack.sm` | `{space.2}` | 8px | Pola formularza, elementy listy |
| `stack.md` | `{space.3}` | 12px | Standardowe sekcje w komponencie |
| `stack.lg` | `{space.4}` | 16px | Większe bloki, grupy fieldów |
| `stack.xl` | `{space.6}` | 24px | Sekcje strony, bloki layoutu |
| `stack.2xl` | `{space.8}` | 32px | Duże sekcje layoutu |

---

### inline — gap poziomy (row flex, margin-right)

```
┌──────┐   inline.sm   ┌──────┐   inline.sm   ┌──────┐
│ Tag  │ ◄── 8px ────► │ Tag  │ ◄── 8px ────► │ Tag  │
└──────┘               └──────┘               └──────┘
```

| Token | Wartość | px | Kiedy używać |
|-------|---------|-----|-------------|
| `inline.xs` | `{space.1}` | 4px | Ikona + tekst, badge + label |
| `inline.sm` | `{space.2}` | 8px | Między tagami, chipami, małymi elementami |
| `inline.md` | `{space.3}` | 12px | Między polami formularza w wierszu |
| `inline.lg` | `{space.4}` | 16px | Między głównymi blokami UI |
| `inline.xl` | `{space.6}` | 24px | Między sekcjami layoutu |

---

## Mapowanie: Semantic → Module

### Navbar

```
┌─────────────────────────────────────────────────────────────┐
│  height: {size.5} = 56px                                   │
│                                                             │
│  SEMANTIC → MODULE                                         │
│                                                             │
│  inset.xl    →  navbar.padding.x  (24px, lewa i prawa)    │
│  (padding-y jest zbędny — treść centrowana przez flexbox)  │
└─────────────────────────────────────────────────────────────┘
```

### Modal

```
┌─────────────────────────────────────────────────────────────┐
│  HEADER (height wypływa z treści + padding)                │
│                                                             │
│  {space.5}   →  modal.header.padding.y  (20px góra/dół)   │
│  {inset.xl}  →  modal.header.padding.x  (24px lewa/prawa) │
│                                                             │
│  BODY                                                       │
│  {inset.xl}  →  modal.body.padding      (24px, wszystkie) │
│                                                             │
│  FOOTER                                                     │
│  {inset.xl}  →  modal.footer.padding.x                    │
│  {inset.sm}  →  modal.footer.padding.y                    │
└─────────────────────────────────────────────────────────────┘
```

### List item / Nav item

```
┌─────────────────────────────────────────────────────────────┐
│  height: {size.2} = 32px                                   │
│                                                             │
│  SEMANTIC → MODULE                                         │
│                                                             │
│  inset.md   →  list-item.padding.x  (12px, lewa i prawa)  │
│  (padding-y zbędny — treść centrowana przez align-items)   │
└─────────────────────────────────────────────────────────────┘
```

### Badge / Status Pill

```
┌─────────────────────────────────────────────────────────────┐
│  height: {size.1} = 24px                                   │
│                                                             │
│  inset.sm   →  badge.padding.x       (8px lewa i prawa)   │
│  (padding-y zbędny — height + align-items: center)         │
└─────────────────────────────────────────────────────────────┘
```

---

## Strategia migracji — tokeny off-grid

Istniejące komponenty zawierają wartości poza siatką ×4 (np. 3px, 7px, 14px). Migrujemy je etapowo **bez twardego zaokrąglania** — żeby uniknąć niekontrolowanych zmian wyglądu.

### Zasada: wyrażenie matematyczne zamiast zaokrąglenia

Każda wartość off-grid jest zapisywana jako **najbliższy token semantyczny ± korekta**. Zachowujemy oryginalną wartość, ale wyraźnie komunikujemy intencję i docelowy token.

```json
// 7px → najbliżej inset.sm (8px), różnica 1px
"input.padding.y": {
  "$value": "{inset.sm} - 1",
  "$description": "TODO: zweryfikuj visual diff → docelowo {inset.sm}"
}

// 14px → najbliżej inset.lg (16px), różnica 2px
"app-menu.group-title.padding.top": {
  "$value": "{inset.lg} - 2",
  "$description": "TODO: zweryfikuj visual diff → docelowo {inset.lg}"
}

// 3px → najbliżej inset.xs (4px), różnica 1px
"tabs.padding.left": {
  "$value": "{inset.xs} - 1",
  "$description": "TODO: zweryfikuj visual diff → docelowo {inset.xs}"
}
```

> Wyrażenia matematyczne (`{token} - 1`, `{token} + 2`) obsługują zarówno Token Studio jak i Style Dictionary.

### Fazy migracji

**Faza 1 — tokenizacja z wyrażeniami (teraz)**
Każda hardkodowana wartość w `modules/base.json` zastępowana wyrażeniem. Wizualnie nic się nie zmienia.

**Faza 2 — weryfikacja per komponent (stopniowo)**
Dla każdego tokenu z `TODO` wykonaj visual diff test. Jeśli wygląd jest akceptowalny po zaokrągleniu — usuń korektę i zostaw sam token semantyczny.

**Faza 3 — czysty stan docelowy**
Wszystkie tokeny modułowe wskazują wyłącznie na tokeny semantyczne. Brak wyrażeń matematycznych.

### Uwaga na szczególne przypadki

Niektóre wartości off-grid mogą być **intencjonalne** — oparte o optyczną kalibrację:

| Wartość | Komponent | Ryzyko zmiany |
|---------|-----------|---------------|
| 7px | `date-range-picker` grid-gap kalendarza | Wysoki — może zmienić układ dni |
| 14px | `SubMenuGroupTitle` padding-top (asymetria 14/8) | Średni — optyczna kompensacja |

Dla tych przypadków zostaw wyrażenie matematyczne do momentu decyzji projektowej.

---

## Zasady stosowania

### ✅ Rób tak

```
// Komponent z uniformowym paddingiem
padding: var(--modal-body-padding);          // module token

// Komponent z fixed height — padding tylko na osi X
height: var(--list-item-height);             // size token
padding-left: var(--list-item-padding-x);   // spacing token
align-items: center;                         // centrowanie vertikalne
```

### ❌ Nie rób tak

```
// Nie używaj semantic bezpośrednio w komponencie
padding: var(--inset-xl);

// Nie używaj primitives bezpośrednio
padding: var(--space-6);

// Nie hardkoduj px
padding: 24px;

// Nie dodawaj padding-y do elementów z fixed height
height: 32px;
padding: 8px 12px;  ← padding-y jest niewidoczny!
```

---

## Następne kroki

➡️ [Tokeny modułowe](./05-tokeny-modulowe.md) — konwencja nazewnictwa komponentów

➡️ [Warstwy](./02-warstwy.md) — architektura 3-warstwowa
