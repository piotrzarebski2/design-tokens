# Wprowadzenie do Design Tokenów

> Design tokeny sprawiają, że decyzje projektowe stają się spójne, skalowalne i łatwe do ponownego użycia.

---

## Czym są design tokeny?

Design tokeny to **fundamentalne cegiełki design systemu**. Przechowują najmniejsze, wielokrotnie używane decyzje projektowe — takie jak kolory, typografia, odstępy czy animacje — i zamieniają surowe wartości (np. `#0b68ff`) w zrozumiałe dla człowieka etykiety.

Tokeny tworzą **wspólny język** między designerami a developerami, zapewniając spójność i skalowalność w całej organizacji.

---

## Kluczowa zasada: aplikujemy tokeny modułowe

> ⚠️ **Ważne:** Na produkt aplikujemy **wyłącznie tokeny modułowe** (komponentowe), nigdy primitives ani semantic bezpośrednio.

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   ❌ NIE APLIKUJ                    ✅ APLIKUJ              │
│                                                             │
│   color.blue.600                   buttons.variant.         │
│   (primitive)                      primary.bg.default       │
│                                    (module token)           │
│   color.background.brand.solid                              │
│   (semantic)                                                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Dlaczego tokeny modułowe?

### 🎯 Elastyczność lokalna

Każdy komponent ma swoje tokeny — możesz modyfikować wygląd modułu **bez wpływu na resztę platformy**.

```
Zmiana koloru przycisku:
buttons.variant.primary.bg.default = {color.background.brand.solid}
                                            ↓
                             Zmień tylko dla buttonów,
                             reszta systemu bez zmian
```

### 🔄 Globalne zmiany nadal możliwe

Tokeny modułowe czerpią z semantyki — zmiana na poziomie semantic **propaguje się do wszystkich modułów**.

```
color.background.brand.solid = #FF0000  (zmiana globalna)
        │
        ├── buttons.variant.primary.bg.default
        ├── links.text.default
        └── badges.variant.info.bg
            │
            Wszystkie się aktualizują
```

### 🧱 Zgodność z Atomic Design

Struktura tokenów odzwierciedla budowę warstwową komponentów:

```
ATOMS              MOLECULES           ORGANISMS
(primitives)       (semantic)          (module tokens)

color.blue.600  →  brand.solid      →  buttons.primary.bg
                                       avatar.bg
                                       status-pill.bg
```

### 📦 Grupowanie wspólnych cech

Komponenty o wspólnych cechach (np. wszystkie formularze) mogą współdzielić tokeny:

```
form.input.border.color.default     ← Wspólne dla wszystkich inputów
form.input.border.color.focus
form.input.border.color.error

input.text.border.color   = {form.input.border.color.default}
input.select.border.color = {form.input.border.color.default}
input.date.border.color   = {form.input.border.color.default}
```

---

## Jak to działa?

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  1. PRIMITIVES (Core)                                       │
│     color.blue.600 = #0b68ff                               │
│            │                                                │
│            ▼                                                │
│  2. SEMANTIC                                                │
│     color.background.brand.solid = {color.blue.600}        │
│            │                                                │
│            ▼                                                │
│  3. MODULE (Component)                                      │
│     buttons.variant.primary.bg.default =                   │
│            {color.background.brand.solid}                   │
│            │                                                │
│            ▼                                                │
│  4. PRODUKT                                                 │
│     ┌───────────────────────────────────────┐              │
│     │████████████████████████████████████████│              │
│     │██         Zapisz                    ██│              │
│     │████████████████████████████████████████│              │
│     └───────────────────────────────────────┘              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Podsumowanie zasad

| Warstwa | Przykład | Aplikować na produkt? |
|---------|----------|----------------------|
| **Primitives** | `color.blue.600` | ❌ Nigdy |
| **Semantic** | `color.background.brand.solid` | ❌ Nigdy |
| **Module** | `buttons.variant.primary.bg.default` | ✅ Tak! |

---

## Kto używa tokenów?

| Rola | Jak używa tokenów |
|------|-------------------|
| **Designer** | W Figma wybiera tokeny modułowe z Variables (np. `buttons/variant/primary/bg/default`) |
| **Developer** | Używa zmiennych CSS wygenerowanych z tokenów modułowych |
| **Product Owner** | Wie, że zmiany brandingu można wprowadzić centralnie na poziomie semantic |

---

## Następne kroki

➡️ [Warstwy tokenów](./02-warstwy.md) — poznaj strukturę Primitives → Semantic → Module

➡️ [Nazewnictwo tokenów](./03-nazewnictwo.md) — zrozum jak czytać i tworzyć nazwy tokenów

➡️ [Tokeny modułowe](./05-tokeny-modulowe.md) — szczegółowa konwencja nazewnictwa komponentów
