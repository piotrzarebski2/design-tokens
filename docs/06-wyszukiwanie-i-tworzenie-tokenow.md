# Wyszukiwanie i tworzenie tokenów semantycznych

> Ten dokument pomoże Ci znaleźć odpowiedni token semantyczny do użycia w tokenie modułowym, a jeśli takiego nie ma — stworzyć go zgodnie z konwencją.

---

## Spis treści

1. [Kiedy potrzebujesz tego dokumentu?](#kiedy-potrzebujesz-tego-dokumentu)
2. [Jak działa matryca tokenów?](#jak-działa-matryca-tokenów)
3. [Workflow krok po kroku](#workflow-krok-po-kroku)
4. [Matryca: Border](#matryca-border)
5. [Matryca: Background](#matryca-background)
6. [Matryca: Text](#matryca-text)
7. [Matryca: Icon](#matryca-icon)
8. [Tworzenie brakującego tokena](#tworzenie-brakującego-tokena)
9. [Słownik pojęć](#słownik-pojęć)

---

## Kiedy potrzebujesz tego dokumentu?

Używasz tego dokumentu gdy:

1. **Tworzysz nowy token modułowy** i musisz wskazać jego wartość (referencję do tokena semantycznego)
2. **Szukasz istniejącego tokena** semantycznego do przypisania
3. **Brakuje tokena** semantycznego i musisz go stworzyć

```
Przykład sytuacji:

Projektujesz komponent "Card" z niebieskim obramowaniem.
Potrzebujesz tokena modułowego: card.border.color.default

Pytanie: Jaki token semantyczny przypisać jako wartość?
→ Używasz matrycy aby znaleźć: color.border.brand.solid
→ Wynik: card.border.color.default = {color.border.brand.solid}
```

---

## Jak działa matryca tokenów?

Matryca to tabela, która pokazuje **wszystkie możliwe kombinacje** tokenów semantycznych dla danego property.

### Struktura nazwy tokena semantycznego

```
color.{property}.{category}.{modifier}{State}
```

| Element | Opis | Przykłady |
|---------|------|-----------|
| **property** | Co stylujemy? | `border`, `background`, `text`, `icon` |
| **category** | Jaki kontekst/znaczenie? | `base`, `brand`, `success`, `warning`, `danger`, `neutral` |
| **modifier** | Jaka intensywność/wariant? | `default`, `subtle`, `muted`, `strong`, `solid`, `inverted` |
| **State** | Jaki stan interakcji? | (brak), `Hover`, `Active`, `Focus`, `Disabled` |

### Czytanie matrycy

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│              MODIFIERS (kolumny)                            │
│         ┌──────┬──────┬──────┬──────┬──────┐               │
│         │default│subtle│muted │strong│solid │               │
│    ┌────┼──────┼──────┼──────┼──────┼──────┤               │
│  C │base│  ■   │  ■   │  ■   │      │      │               │
│  A ├────┼──────┼──────┼──────┼──────┼──────┤               │
│  T │brand│     │  ■   │      │      │  ■   │               │
│  E ├────┼──────┼──────┼──────┼──────┼──────┤               │
│  G │success│   │  ■   │      │      │  ■   │               │
│  O ├────┼──────┼──────┼──────┼──────┼──────┤               │
│  R │danger│    │  ■   │      │      │  ■   │               │
│  Y └────┴──────┴──────┴──────┴──────┴──────┘               │
│                                                             │
│    ■ = token istnieje                                       │
│    (puste) = token nie istnieje, ale można go stworzyć     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Jak czytać?**
- Znajdź **wiersz** (category) odpowiadający znaczeniu koloru
- Znajdź **kolumnę** (modifier) odpowiadającą intensywności
- Na przecięciu znajdziesz token (lub pustą komórkę = brak tokena)

---

## Workflow krok po kroku

### Krok 1: Zidentyfikuj potrzebę

Odpowiedz na pytania:

| Pytanie | Twoja odpowiedź | Mapowanie |
|---------|-----------------|-----------|
| **Co stylujemy?** | Obramowanie? Tło? Tekst? Ikonę? | → `property` |
| **Jakie znaczenie?** | Bazowy? Marki? Sukces? Ostrzeżenie? Błąd? | → `category` |
| **Jaka intensywność?** | Domyślna? Subtelna? Wyciszona? Mocna? Pełna? | → `modifier` |
| **Jaki stan?** | Normalny? Hover? Active? Disabled? | → `State` |

### Krok 2: Zbuduj nazwę tokena

```
color.{property}.{category}.{modifier}{State}
```

**Przykład:**
- Co stylujemy? → **tło** → `background`
- Jakie znaczenie? → **marka/brand** → `brand`
- Jaka intensywność? → **pełna/solidna** → `solid`
- Jaki stan? → **hover** → `Hover`

**Wynik:** `color.background.brand.solidHover`

### Krok 3: Sprawdź czy token istnieje

1. Otwórz odpowiednią matrycę (border/background/text/icon)
2. Znajdź wiersz z category (np. `brand`)
3. Znajdź kolumnę z modifier (np. `solid`)
4. Sprawdź czy na przecięciu jest ■ (istnieje) czy jest pusto (brak)

### Krok 4a: Token istnieje → Użyj go

```json
{
  "buttons.variant.primary.bg.default": {
    "$value": "{color.background.brand.solid}",
    "$type": "color"
  }
}
```

### Krok 4b: Token NIE istnieje → Stwórz go

Przejdź do sekcji [Tworzenie brakującego tokena](#tworzenie-brakującego-tokena)

---

## Matryca: Border

Tokeny dla **obramowań** (`color.border.*`)

### Legenda stanów
- Bez sufiksu = stan domyślny
- `Hover` = stan najechania
- `Active` = stan aktywny/wciśnięty

|  | default | defaultHover | subtle | subtleHover | subtleActive | muted | mutedHover | strong | strongHover | inverted | solid | solidHover | solidActive |
|--|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| **base** | ■ | ■ | ■ |  |  | ■ |  |  |  |  |  |  |  |
| **brand** |  |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ | ■ |
| **success** |  |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ | ■ |
| **warning** |  |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ | ■ |
| **danger** |  |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ | ■ |
| **neutral** |  |  | ■ |  |  |  |  |  |  |  | ■ | ■ | ■ |
| **inverse** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |
| **onSolid** | ■ |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ | ■ |

### Przykłady użycia border

| Przypadek użycia | Token semantyczny |
|------------------|-------------------|
| Domyślne obramowanie inputa | `color.border.base.default` |
| Obramowanie inputa po najechaniu | `color.border.base.defaultHover` |
| Obramowanie aktywnego inputa (brand) | `color.border.brand.solid` |
| Obramowanie błędu walidacji | `color.border.danger.solid` |
| Subtelne obramowanie sukcesu | `color.border.success.subtle` |
| Obramowanie na ciemnym tle | `color.border.inverse.default` |

---

## Matryca: Background

Tokeny dla **teł** (`color.background.*`)

|  | default | defaultHover | subtle | subtleHover | subtleActive | muted | mutedHover | strong | strongHover | inverted | solid | solidHover | solidActive | canvas | overlay | scrim |
|--|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| **base** | ■ | ■ | ■ | ■ | ■ | ■ | ■ |  |  |  |  |  |  | ■ | ■ | ■ |
| **brand** | ■ |  | ■ | ■ | ■ |  |  |  |  |  | ■ | ■ | ■ |  |  |  |
| **success** | ■ |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ |  |  |  |  |
| **warning** | ■ |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ |  |  |  |  |
| **danger** | ■ |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ |  |  |  |  |
| **neutral** | ■ |  | ■ | ■ |  |  |  |  |  |  | ■ | ■ |  |  |  |  |
| **onSolid** |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

### Przykłady użycia background

| Przypadek użycia | Token semantyczny |
|------------------|-------------------|
| Tło strony/karty | `color.background.base.default` |
| Tło sekcji wyróżnionej | `color.background.base.subtle` |
| Tło wyciszone (np. disabled) | `color.background.base.muted` |
| Tło głównego przycisku | `color.background.brand.solid` |
| Tło przycisku po najechaniu | `color.background.brand.solidHover` |
| Tło alertu sukcesu | `color.background.success.subtle` |
| Tło alertu błędu | `color.background.danger.subtle` |
| Tło overlay/modal | `color.background.base.overlay` |

---

## Matryca: Text

Tokeny dla **tekstu** (`color.text.*`)

|  | default | defaultHover | subtle | subtleHover | subtleActive | muted | mutedHover | strong | strongHover | inverted | solid | solidHover | solidActive | onMuted | onSolid | badge |
|--|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| **base** | ■ | ■ | ■ | ■ |  | ■ |  |  |  |  |  |  |  |  |  |  |
| **brand** | ■ | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **success** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **warning** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **danger** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **neutral** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **onSolid** | ■ |  |  |  |  |  |  | ■ |  |  | ■ |  |  | ■ | ■ | ■ |

### Przykłady użycia text

| Przypadek użycia | Token semantyczny |
|------------------|-------------------|
| Główny tekst treści | `color.text.base.default` |
| Tekst drugorzędny | `color.text.base.subtle` |
| Tekst wyciszony/placeholder | `color.text.base.muted` |
| Tekst linku | `color.text.brand.default` |
| Tekst linku po najechaniu | `color.text.brand.defaultHover` |
| Tekst komunikatu sukcesu | `color.text.success.default` |
| Tekst komunikatu błędu | `color.text.danger.default` |
| Tekst na solidnym tle (np. przycisk) | `color.text.onSolid.default` |
| Tekst na kolorowym badge | `color.text.onSolid.badge` |

---

## Matryca: Icon

Tokeny dla **ikon** (`color.icon.*`)

|  | default | defaultHover | subtle | subtleHover | subtleActive | muted | mutedHover | strong | strongHover | inverted | solid | solidHover | solidActive | onMuted | onSolid |
|--|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| **base** | ■ | ■ | ■ | ■ |  | ■ |  |  |  |  |  |  |  |  |  |
| **brand** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **success** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **warning** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **danger** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **neutral** | ■ |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **onSolid** | ■ |  |  |  |  |  |  |  |  |  |  |  |  | ■ | ■ |

### Przykłady użycia icon

| Przypadek użycia | Token semantyczny |
|------------------|-------------------|
| Domyślna ikona | `color.icon.base.default` |
| Ikona drugorzędna | `color.icon.base.subtle` |
| Ikona wyciszona/disabled | `color.icon.base.muted` |
| Ikona akcji brand | `color.icon.brand.default` |
| Ikona sukcesu (checkmark) | `color.icon.success.default` |
| Ikona błędu (X) | `color.icon.danger.default` |
| Ikona na solidnym tle | `color.icon.onSolid.default` |

---

## Tworzenie brakującego tokena

Jeśli w matrycy nie ma tokena, którego potrzebujesz — możesz go stworzyć.

### Kiedy tworzyć nowy token semantyczny?

✅ **TAK, stwórz** gdy:
- Potrzebujesz tokena dla istniejącej kombinacji category + modifier, ale z nowym stanem
- Widzisz lukę w matrycy, która ma sens funkcjonalny
- Token będzie używany przez wiele komponentów

❌ **NIE twórz** gdy:
- Token byłby używany tylko przez jeden komponent (użyj tokena modułowego bezpośrednio z primitive)
- Kombinacja nie ma sensu funkcjonalnego (np. `text.base.solid` — tekst nie ma "solidnego" wariantu)

### Proces tworzenia

#### Krok 1: Zbuduj nazwę zgodnie z konwencją

```
color.{property}.{category}.{modifier}{State}
```

#### Krok 2: Określ wartość (referencję do primitive)

Sprawdź istniejące tokeny w tej samej category — użyj podobnej logiki.

**Przykład:**
```
Chcę stworzyć: color.border.brand.muted

Sprawdzam istniejące brand:
- color.border.brand.subtle = {color.blue.200}
- color.border.brand.solid = {color.blue.600}

Logika: muted powinien być między subtle a solid
Wynik: color.border.brand.muted = {color.blue.300}
```

#### Krok 3: Dodaj token do pliku tokenów

```json
{
  "color": {
    "border": {
      "brand": {
        "muted": {
          "$value": "{color.blue.300}",
          "$type": "color",
          "$description": "Wyciszone obramowanie marki, używane dla elementów nieaktywnych"
        }
      }
    }
  }
}
```

#### Krok 4: Dodaj wersję Dark Mode (jeśli dotyczy)

```json
// W pliku dark.json lub sekcji dark
{
  "color": {
    "border": {
      "brand": {
        "muted": {
          "$value": "{color.blue.400}",
          "$type": "color"
        }
      }
    }
  }
}
```

#### Krok 5: Zaktualizuj matrycę

Dodaj nowy token do matrycy w dokumentacji, aby inni wiedzieli, że istnieje.

### Checklist przed stworzeniem tokena

- [ ] Nazwa zgodna z konwencją `color.{property}.{category}.{modifier}{State}`
- [ ] Wartość (primitive) dobrana logicznie względem innych tokenów w category
- [ ] Dodana wersja Light Mode
- [ ] Dodana wersja Dark Mode
- [ ] Token ma opis (`$description`)
- [ ] Matryca zaktualizowana

---

## Słownik pojęć

### Properties (Co stylujemy?)

| Property | Opis | Przykład użycia |
|----------|------|-----------------|
| `border` | Obramowanie elementu | Ramka inputa, krawędź karty |
| `background` | Tło elementu | Tło przycisku, tło alertu |
| `text` | Kolor tekstu | Nagłówki, akapity, etykiety |
| `icon` | Kolor ikony | Ikony w przyciskach, ikony statusu |

### Categories (Jakie znaczenie?)

| Category | Opis | Kiedy używać |
|----------|------|--------------|
| `base` | Neutralny, podstawowy | Domyślne elementy UI bez specjalnego znaczenia |
| `brand` | Kolor marki (niebieski) | Akcje główne, elementy wyróżnione, linki |
| `success` | Sukces (zielony) | Potwierdzenia, walidacja pozytywna |
| `warning` | Ostrzeżenie (żółty/pomarańczowy) | Uwagi, informacje wymagające uwagi |
| `danger` | Błąd/niebezpieczeństwo (czerwony) | Błędy, usuwanie, akcje destrukcyjne |
| `neutral` | Neutralny szary | Elementy drugoplanowe, disabled |
| `inverse` | Odwrócony (dla ciemnych teł) | Tekst/ikony na ciemnym tle |
| `onSolid` | Na solidnym kolorowym tle | Tekst/ikona na kolorowym przycisku |

### Modifiers (Jaka intensywność?)

| Modifier | Opis | Intensywność | Przykład użycia |
|----------|------|:------------:|-----------------|
| `default` | Domyślny | ●●●○○ | Standardowy element |
| `subtle` | Subtelny | ●●○○○ | Lekkie wyróżnienie |
| `muted` | Wyciszony | ●○○○○ | Disabled, placeholder |
| `strong` | Mocny | ●●●●○ | Wyróżnienie ważnych elementów |
| `solid` | Pełny/solidny | ●●●●● | Główne akcje, przyciski CTA |
| `inverted` | Odwrócony | — | Na kontrastowym tle |

### States (Jaki stan?)

| State | Opis | Kiedy dodawać |
|-------|------|---------------|
| (brak) | Stan domyślny | Zawsze podstawowy |
| `Hover` | Najechanie myszką | Elementy interaktywne |
| `Active` | Wciśnięty/aktywny | Podczas kliknięcia |
| `Focus` | Skupiony (focus) | Nawigacja klawiaturą |
| `Disabled` | Wyłączony | Elementy nieaktywne |

---

## Szybka ściągawka

### Formuła nazwy

```
color.{border|background|text|icon}.{base|brand|success|warning|danger|neutral|inverse|onSolid}.{default|subtle|muted|strong|solid|inverted}{Hover|Active|Focus|Disabled}
```

### Najczęściej używane tokeny

| Użycie | Token |
|--------|-------|
| Tło strony | `color.background.base.default` |
| Główny tekst | `color.text.base.default` |
| Tekst pomocniczy | `color.text.base.subtle` |
| Placeholder | `color.text.base.muted` |
| Link | `color.text.brand.default` |
| Tło przycisku głównego | `color.background.brand.solid` |
| Tekst na przycisku | `color.text.onSolid.default` |
| Obramowanie inputa | `color.border.base.default` |
| Obramowanie focus | `color.border.brand.solid` |
| Alert sukcesu (tło) | `color.background.success.subtle` |
| Alert błędu (tło) | `color.background.danger.subtle` |

---

## Następne kroki

➡️ [Tokeny modułowe](./05-tokeny-modulowe.md) — jak używać tokenów semantycznych w komponentach

➡️ [Nazewnictwo](./03-nazewnictwo.md) — pełna konwencja nazewnictwa
