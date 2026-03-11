# Tokeny modułowe — konwencja nazewnictwa

> Tokeny modułowe to jedyna warstwa, którą aplikujemy na produkt. Ten dokument opisuje szczegółową konwencję ich nazewnictwa.

---

## Struktura nazwy tokena modułowego

```
{component}.{variant|role|context}.{element}.{property}.{state}
```

Używamy separatora **kropki `.`** ponieważ:
- Pomaga uporządkować tokeny w strukturze drzewa w narzędziach projektowych
- Działa dobrze w plikach JSON
- Można łatwo tłumaczyć na zmienne CSS (`--component-variant-element-state`)

---

## Części składowe nazwy

### 1. Component (Komponent)

Nazwa komponentu lub modułu, do którego należy token.

**Przykłady:**
```
buttons.variant.primary.bg.default
avatar.bg.default
status-pill.variant.success.text
modal.header.bg
chat.input.border.color.focus
```

---

### 2. Variant / Role / Context

Opcjonalna część określająca wariant, rolę lub kontekst użycia.

#### Warianty wizualne

```
buttons.variant.primary...
buttons.variant.secondary...
buttons.variant.tertiary...
buttons.variant.primary-danger...
```

> 💡 **Uwaga:** Nie ma jeszcze wiążącej decyzji czy używamy `variant.` jako prefix czy wychodzi z kontekstu (`buttons.primary...`).

#### Role / Tryby

```
list-item.role.delete...
button.role.destructive...
```

#### Części komponentu (dla organizmów)

```
modal.header...
modal.body...
modal.footer...
chat.input...
chat.message...
```

---

### 3. Element

Stałe, powtarzalne nazwy elementów:

| Element | Użycie | Przykład |
|---------|--------|----------|
| `bg` | Tło elementu | `avatar.bg.default` |
| `border` | Obramowanie | `avatar.border.color.default` |
| `label` | Etykieta | `input.label.color` |
| `placeholder` | Placeholder | `input.placeholder.color` |
| `description` | Tekst opisowy | `input.description.color` |
| `text` | Tekst główny* | `card.text.color` |
| `icon` | Ikona | `buttons.variant.primary.icon.default` |

> ⚠️ **`text`** używamy tylko gdy:
> - To główny tekst komponentu
> - Nie da się go lepiej nazwać semantycznie (np. `label`, `description`)

---

### 4. Property (Właściwość)

Określa konkretną właściwość CSS.

```
*.border.color    → border-color
*.border.width    → border-width
*.border.radius   → border-radius
```

#### Kiedy pomijamy `.color`?

Gdy element może mieć **tylko właściwość kolor** (jak `bg`), pomijamy `.color`:

```
✅ avatar.bg.default
❌ avatar.bg.color.default

✅ buttons.variant.primary.bg.hover
❌ buttons.variant.primary.bg.color.hover
```

Ale dla `border` zawsze używamy pełnej ścieżki:

```
✅ avatar.border.color.default
✅ avatar.border.width
✅ avatar.border.radius
```

---

### 5. State (Stan)

Stany interaktywne dodajemy **tylko gdy jest ich więcej niż jeden**.

**Dostępne stany:**
```
default | hover | focus | active | selected | disabled | error | validated
```

#### Kiedy używamy stanów?

```
✅ Element ma wiele stanów:
buttons.variant.primary.bg.default
buttons.variant.primary.bg.hover
buttons.variant.primary.bg.focus
buttons.variant.primary.bg.active
buttons.variant.primary.bg.disabled
```

```
✅ Element ma tylko jeden stan — nie używamy .default:
avatar.borderRadius          (nie: avatar.borderRadius.default)
avatar.borderWidth           (nie: avatar.borderWidth.default)
status-pill.typography       (nie: status-pill.typography.default)
```

---

## Container — dla złożonych organizmów

Gdy komponent jest złożony w organizm i zawiera wiele elementów, używamy `container` dla głównego kontenera:

```
chat.container.bg
chat.container.border.color
chat.container.border.width
chat.container.border.radius
chat.container.shadow
```

**Przykład struktury chata:**

```
---- Kontener główny ----
chat.container.bg
chat.container.border.radius
chat.container.border.color
chat.container.border.width

---- Header ----
chat.header.bg
chat.header.text.color
chat.header.border.color
chat.header.border.width

---- Wiadomości ----
chat.message.bg
chat.message.text.color
chat.message.highlighted.bg
chat.message.highlighted.border.color

---- Input ----
chat.input.bg
chat.input.text.color
chat.input.placeholder.color
chat.input.border.color.default
chat.input.border.color.focus
chat.input.border.color.filled

---- Linki ----
chat.link.text.color.default
chat.link.text.color.hover
chat.link.text.color.disabled
```

---

## Przykłady z rzeczywistych komponentów

### Buttons

```json
buttons.variant.primary.bg.default      = {color.background.brand.solid}
buttons.variant.primary.bg.hover        = {color.background.brand.solidHover}
buttons.variant.primary.bg.focus        = {color.background.brand.solid}
buttons.variant.primary.bg.active       = {color.background.brand.solidActive}
buttons.variant.primary.bg.disabled     = {color.background.brand.solid}

buttons.variant.primary.text.default    = {color.text.onSolid.brand}
buttons.variant.primary.text.hover      = {color.text.onSolid.brand}
buttons.variant.primary.text.disabled   = {color.text.onSolid.brand}

buttons.variant.primary.icon.default    = {color.text.onSolid.brand}
buttons.variant.primary.border.default  = {color.transparent}
buttons.variant.primary.border.focus    = {color.focus.base.strong}
```

### Avatar

```json
avatar.size.small           = {space.24}
avatar.size.medium          = {space.40}
avatar.size.large           = {space.84}
avatar.size.xlarge          = {space.120}

avatar.borderRadius         = {border.radius.full}
avatar.borderWidth          = {border.default}

avatar.bg.default           = {color.background.base.muted}
avatar.bg.hover             = {color.background.base.mutedHover}
avatar.bg.disabled          = {color.background.base.muted}

avatar.text.default         = {color.text.base.muted}
avatar.text.hover           = {color.text.base.muted}
avatar.text.disabled        = {color.text.disabled.default}

avatar.border.color.default = {color.border.base.default}
avatar.border.color.hover   = {color.background.brand.solid}
avatar.border.color.disabled= {color.border.base.disabled}

avatar.disabled.opacity     = {opacity.disabled}
```

### Status Pill

```json
status-pill.size.height             = {space.16}
status-pill.size.padding.horizontal = {space.8}
status-pill.size.padding.vertical   = {space.1}
status-pill.borderRadius            = {border.radius.full}
status-pill.borderWidth             = {border.default}
status-pill.typography              = {typography.title.level7}

status-pill.variant.neutral.text         = {color.text.neutral.default}
status-pill.variant.neutral.border.solid = {color.border.neutral.subtle}
status-pill.variant.neutral.border.dashed= {color.border.neutral.subtle}

status-pill.variant.success.text         = {color.text.success.default}
status-pill.variant.success.border.solid = {color.border.success.default}

status-pill.variant.warning.text         = {color.text.warning.default}
status-pill.variant.warning.border.solid = {color.border.warning.default}

status-pill.variant.error.text           = {color.text.danger.default}
status-pill.variant.error.border.solid   = {color.border.danger.default}
```

---

## Zasada: Applying Deltas for Variant Overrides

Aby zachować jasną i skalowalną strukturę, **oddzielamy wspólne właściwości od nadpisań wariantów**.

**Przykład dla chip:**

Jeśli domyślny chip ma ten sam kolor tekstu co wariant `filled`, definiujemy tekst raz, a tło nadpisujemy dla wariantu:

```
chip.text                    ← wspólny tekst
chip.bg                      ← domyślne tło
chip.filled.bg               ← nadpisanie dla wariantu filled
```

**Nie duplikujemy:**
```
❌ chip.default.text
❌ chip.filled.text          ← niepotrzebne jeśli takie same
```

---

## Dobre praktyki — Do / Don't

### ✅ Do: Używaj semantycznych nazw elementów

```
✅ input.label.color
✅ input.description.color
✅ input.placeholder.color
```

### ❌ Don't: Nie używaj `text` gdy masz lepszą nazwę

```
❌ input.text.label.color        ← nadmiarowe
❌ input.text.description.color  ← nadmiarowe
```

---

### ✅ Do: Pomijaj `.default` gdy element ma tylko jeden stan

```
✅ avatar.borderRadius
✅ status-pill.typography
```

### ❌ Don't: Nie dodawaj `.default` bez potrzeby

```
❌ avatar.borderRadius.default
❌ status-pill.typography.default
```

---

### ✅ Do: Pomijaj `.color` dla elementów jednowłaściwościowych

```
✅ avatar.bg.hover
✅ buttons.variant.primary.bg.default
```

### ❌ Don't: Nie dodawaj `.color` do `bg`

```
❌ avatar.bg.color.hover
❌ buttons.variant.primary.bg.color.default
```

---

### ✅ Do: Używaj `container` dla głównego kontenera organizmu

```
✅ chat.container.bg
✅ modal.container.border.radius
```

### ❌ Don't: Nie nazywaj kontenera po komponencie

```
❌ chat.chat.bg
❌ modal.modal.border.radius
```

---

## Grupowanie wspólnych cech

Komponenty o wspólnych cechach mogą współdzielić tokeny grupowe:

```
---- Wspólne tokeny formularzy ----
form.field.border.color.default
form.field.border.color.focus
form.field.border.color.error
form.field.border.color.disabled
form.field.border.width
form.field.border.radius
form.field.bg.default
form.field.bg.disabled

---- Użycie w komponentach ----
input.text.border.color.default    = {form.field.border.color.default}
input.select.border.color.default  = {form.field.border.color.default}
input.textarea.border.color.default= {form.field.border.color.default}
datepicker.border.color.default    = {form.field.border.color.default}
```

---

## Transformacja do CSS

Tokeny modułowe są automatycznie transformowane do zmiennych CSS:

```
Token:   buttons.variant.primary.bg.default
CSS:     --buttons-variant-primary-bg-default: #0b68ff;

Token:   avatar.border.color.hover
CSS:     --avatar-border-color-hover: #0b68ff;
```

---

## Szybka ściągawka

```
STRUKTURA:
{component}.{variant|role|context}.{element}.{property}.{state}

ELEMENTY:
bg | border | label | placeholder | description | text | icon

PROPERTIES:
border.color | border.width | border.radius

STANY:
default | hover | focus | active | selected | disabled | error | validated

ZASADY:
• Pomijaj .color dla bg
• Pomijaj .default gdy jeden stan
• Używaj container dla organizmów
• Grupuj wspólne cechy (form.field.*)
```

---

## Następne kroki

➡️ [Kolory](./04-kolory.md) — jak semantic mapuje się na module tokens

➡️ [Warstwy](./02-warstwy.md) — dlaczego używamy tokenów modułowych
