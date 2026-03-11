# Workflow: Aplikowanie tokenów do Figmy przez figma-ds-cli

> Instrukcja dla Claude jak aplikować tokeny modułowe do węzłów Figmy.
> Używamy `figma-ds-cli` (zainstalowane globalnie jako `figma-ds-cli`).

---

## Wymagania wstępne

- Figma Desktop musi być otwarta z właściwym plikiem
- `figma-ds-cli` zainstalowane: `~/.nvm/versions/node/v22.18.0/bin/figma-ds-cli`
- **WAŻNE:** CLI musi być uruchamiane z własnego katalogu — zawsze używaj prefixu:

```bash
cd ~/.nvm/versions/node/v22.18.0/lib/node_modules/figma-ds-cli && figma-ds-cli <komenda>
```

Sprawdzenie połączenia:
```bash
cd ~/.nvm/versions/node/v22.18.0/lib/node_modules/figma-ds-cli && figma-ds-cli status
```

---

## Format zmiennych Figma

Zmienne w Figmie mają format: `{kolekcja}/{ścieżka/tokenu}`

```
form/switch/bg/default
form/switch/bg/hover
form/buttons/variant/primary/bg/default
```

Kolekcja `form` = tokeny z `tokens/modules/base.json`.

Żeby sprawdzić dostępne zmienne:
```bash
cd ~/.nvm/versions/node/v22.18.0/lib/node_modules/figma-ds-cli && figma-ds-cli variables find "<nazwa>"
```

---

## Format node-id

W URL Figmy: `node-id=249-3688` → w CLI używamy `249:3688` (myślnik na dwukropek).

---

## Dostępne komendy bind

Aplikujemy **tylko tokeny kolorystyczne**. Pomijamy: radius, spacing, padding, gap, typografię i inne właściwości nielkolorystyczne.

| Właściwość | Komenda |
|---|---|
| Kolor wypełnienia (fill) | `bind fill "form/token/path" -n "id:id"` |
| Kolor obrysu (stroke) | `bind stroke "form/token/path" -n "id:id"` |

Bez `-n` komenda działa na aktualnie zaznaczonym węźle w Figmie.

Bindowanie wielu węzłów naraz:
```bash
figma-ds-cli bind-batch '[
  {"nodeId": "249:3688", "property": "fill", "variable": "form/switch/bg/default"},
  {"nodeId": "249:3689", "property": "fill", "variable": "form/switch/bg/hover"}
]'
```

---

## Krok po kroku — co robi Claude

### Krok 1: Pobierz strukturę komponentu z Figmy

Gdy użytkownik poda link — wywołaj MCP:

1. `figma-desktop:get_screenshot` → podgląd wizualny
2. `figma-desktop:get_metadata` → struktura węzłów z node-id każdej warstwy
3. `figma-desktop:get_variable_defs` → jakie zmienne są już przypisane

---

### Krok 2: Przeanalizuj strukturę węzłów

Z `get_metadata` wyciągasz mapę: `nazwa warstwy → node-id → właściwość`.

Identyfikujesz:
- Warstwy własne komponentu (do stokenizowania)
- Instancje innych komponentów (pomijasz lub nadpisujesz celowo — patrz zasady poniżej)

---

### Krok 3: Mapuj tokeny na węzły

Dla każdej własnej warstwy budujesz mapowanie:

```
węzeł "Switch Background" (fill) → form/switch/bg/default
węzeł "Thumb" (fill)             → form/switch/handle/bg
```

---

### Krok 4: Przedstaw plan bindowań

Przed wykonaniem pokaż tabelę:

```
node-id     | warstwa            | właściwość | zmienna
------------|---------------------|------------|---------------------------
248:4733    | Switch Background  | fill       | form/switch/bg/default
248:4734    | Thumb              | fill       | form/switch/handle/bg
```

**Czekaj na akceptację.**

---

### Krok 5: Wykonaj bindowania

Po akceptacji uruchamiaj komendy:

```bash
cd ~/.nvm/versions/node/v22.18.0/lib/node_modules/figma-ds-cli && \
  figma-ds-cli bind fill "form/switch/bg/default" -n "248:4733" && \
  figma-ds-cli bind fill "form/switch/handle/bg" -n "248:4734"
```

Lub przez `bind-batch` dla wielu węzłów naraz.

---

### Krok 6: Weryfikacja

Po wykonaniu wywołaj ponownie `get_variable_defs` i potwierdź że zmienne zostały przypisane poprawnie.

---

## Zasady

### Instancje innych komponentów

- **Pomijasz** instancje komponentów które mają własne tokeny modułowe i nie wymagają nadpisania (np. Label, Description wewnątrz Switch)
- **Nadpisujesz celowo** tylko gdy stan rodzica wymaga innego koloru niż domyślny tokenu dziecka (np. tekst na solid tle wymaga `onSolid` zamiast domyślnego)
- Identyfikujesz instancję po: `data-node-id` z prefixem `I` (np. `I249:3663;177:1187`) lub po `$type: INSTANCE` w metadanych

### Stany komponentu

Każdy stan (Default, Hover, Selected, Disabled, Error) to osobny węzeł w Figmie. Aplikujesz tokeny dla każdego stanu osobno, używając odpowiedniego tokenu:

```
stan Default  → form/switch/bg/default
stan Hover    → form/switch/bg/hover
stan Selected → form/switch/bg/selected
stan Error    → form/switch/bg/error
```

### Tylko tokeny modułowe

Nigdy nie aplikujesz bezpośrednio tokenów semantic ani primitives:
```
❌ color/background/base/strong
❌ color/grey/400
✅ form/switch/bg/default
```

### Czego nie robisz bez pytania

- Nie modyfikujesz warstw poza scope komponentu
- Nie nadpisujesz ręcznie ustawionych wartości bez potwierdzenia
- Nie bindingujesz tokenów semantic ani primitives bezpośrednio

---

## Przykład — Switch, stan Default (Solo)

Node z Figmy: `249:3674` (Content Type=Solo, State=Default)

```bash
# Sprawdź strukturę
figma-desktop:get_metadata fileKey="fsSZONXpVvtrDsCgtu01Jb" nodeId="249:3674"

# Aplikuj tokeny
cd ~/.nvm/versions/node/v22.18.0/lib/node_modules/figma-ds-cli && \
  figma-ds-cli bind fill "form/switch/bg/default" -n "248:4719" && \
  figma-ds-cli bind fill "form/switch/handle/bg" -n "248:4720"
```

---

*Powiązane: `instructions/CONTEXT.md` / `instructions/workflow-color-analysis.md` / `docs/05-tokeny-modulowe.md`*
