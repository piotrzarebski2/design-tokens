# Workflow: Dodanie tokenów translucent + naprawa hex-alpha w modules

> Instrukcja dla Claude Code. Dodaje kategorię `color.background.translucent.*` do warstwy semantic (Light.json + Dark.json) oraz token `color.background.overlay.solid` (tooltip). Naprawia tokeny modułowe, które używają hex-suffix (`{token}26`) — zamienia je na składnię alpha modifier. Wszystkie nowe i zmodyfikowane tokeny są **oznaczone jako tymczasowe** do czasu pełnej tokenizacji warstwy modules.

---

## Kiedy używasz tego workflow?

- Trzeba dodać półprzezroczyste kolory do warstwy semantic
- Trzeba naprawić notację `{token}26` (hex-alpha suffix) w modules
- Phase 1: frontend używa semantic bezpośrednio — warstwa translucent jest pomostem
- Po pełnej tokenizacji modules — translucent zostanie wycofane

---

## ⚠️ Ważne zasady

- **Nie używaj `{token}26` (hex-alpha suffix)** — to notacja Token Studio, niekompatybilna z W3C DTCG. Używaj `$extensions.studio.tokens.modify`.
- **Tokeny `translucent` w semantic są tymczasowe** — każdy musi mieć `$description` z dopiskiem `[TYMCZASOWE — do wycofania po tokenizacji modules]`. Dotyczy tylko `color.background.translucent.*` i `overlay.solid` w Light/Dark.json.
- **Tokeny modułowe z alpha modifier są stałe** — przyciski tertiary i tertiary-white będą na zawsze wskazywać na semantic + alpha modifier. Nie oznaczaj ich jako tymczasowe. Zmiana polega wyłącznie na zamianie notacji hex-alpha na właściwą składnię.
- **Tokeny modułowe nie korzystają z `color.background.translucent.*`** — module wskazuje na zwykłe semantic tokeny + dodaje modifier alpha. Warstwa translucent w semantic służy do bezpośredniego użycia przez frontend (Phase 1).
- **Nie modyfikuj istniejących tokenów** poza wymienionymi w tym workflow.
- **Dark mode — prefix `color-dark`**: W `Dark.json` wszystkie referencje do primitywów używają prefiksu `color-dark.*` (nie `color.*`). Paleta `color-dark.grey.*` jest odwrócona — niskie numery (50–300) to ciemne kolory, wysokie (700–900) to jasne. To ta sama konwencja co reszta Dark.json.

---

## Składnia alpha modifier (Token Studio)

```json
{
  "$type": "color",
  "$value": "{color.grey.300}",
  "$description": "Opis tokena [TYMCZASOWE — do wycofania po tokenizacji modules]",
  "$extensions": {
    "studio.tokens": {
      "modify": {
        "type": "alpha",
        "value": "0.15",
        "space": "srgb"
      }
    }
  }
}
```

---

## Krok 1 — Sprawdź dostępność primitives

Przed dodaniem tokenów upewnij się, że wymagane odcienie istnieją w `tokens/primitives/core.json` — dla obu palet:

```bash
python3 -c "
import json
d = json.load(open('tokens/primitives/core.json'))
needed = ['300', '400', '500', '800', '900']
print('=== color.grey (Light) ===')
grey = d['color'].get('grey', {})
for shade in needed:
    val = grey.get(shade, {}).get('\$value', 'BRAK')
    print(f'  grey.{shade}: {val}')
print('=== color-dark.grey (Dark) ===')
grey_dark = d.get('color-dark', {}).get('grey', {})
for shade in needed:
    val = grey_dark.get(shade, {}).get('\$value', 'BRAK')
    print(f'  color-dark.grey.{shade}: {val}')
"
```

Wymagane odcienie — **obie palety**: `grey.300`, `grey.400`, `grey.500`, `grey.800`, `grey.900`.

**Jeśli któryś odcień nie istnieje — zatrzymaj się i zgłoś użytkownikowi.** Nie dodawaj hardcoded hex.

> **Kontekst:** `color-dark.grey.50` = `#292b2c` (ciemny), `color-dark.grey.900` = `#F4F5F6` (jasny) — paleta odwrócona względem `color.grey`.

---

## Krok 2 — Sprawdź istniejące overlay w Light.json

Przeczytaj `tokens/semantic/Light.json` i znajdź blok `color → background → overlay`. Powinien wyglądać tak:

```json
"overlay": {
  "default": {
    "$type": "color",
    "$value": "rgba(35,41,54,0.5)",
    "$description": "Overlay background for modals/dropdowns"
  }
}
```

> `overlay.default` pozostawiamy bez zmian (modal mask) — **nie modyfikuj tej wartości**.
> Dodamy tylko nowy token `overlay.solid` dla tooltipu.

---

## Krok 3 — Przygotuj bloki JSON dla Light.json

### 3a. Blok `translucent` (nowa kategoria)

Translucent to półprzezroczyste tła do użytku na ciemnych i kolorowych powierzchniach (przyciski tertiary, ghost-white itp.). Wartości dobrane optycznie pod kątem luminancji na białym tle.

```json
"translucent": {
  "none": {
    "$type": "color",
    "$value": "{color.transparent}",
    "$description": "Przezroczyste tło (brak wypełnienia). [TYMCZASOWE — do wycofania po tokenizacji modules]"
  },
  "subtle": {
    "$type": "color",
    "$value": "{color.grey.300}",
    "$description": "Subtelne półprzezroczyste tło — 10% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.10", "space": "srgb" }
      }
    }
  },
  "subtleHover": {
    "$type": "color",
    "$value": "{color.grey.300}",
    "$description": "Subtelne półprzezroczyste tło — hover — 15% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.15", "space": "srgb" }
      }
    }
  },
  "subtleActive": {
    "$type": "color",
    "$value": "{color.grey.300}",
    "$description": "Subtelne półprzezroczyste tło — active — 25% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.25", "space": "srgb" }
      }
    }
  },
  "muted": {
    "$type": "color",
    "$value": "{color.grey.500}",
    "$description": "Stonowane półprzezroczyste tło — 10% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.10", "space": "srgb" }
      }
    }
  },
  "mutedHover": {
    "$type": "color",
    "$value": "{color.grey.400}",
    "$description": "Stonowane półprzezroczyste tło — hover — 15% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.15", "space": "srgb" }
      }
    }
  },
  "mutedActive": {
    "$type": "color",
    "$value": "{color.grey.400}",
    "$description": "Stonowane półprzezroczyste tło — active — 25% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.25", "space": "srgb" }
      }
    }
  },
  "strong": {
    "$type": "color",
    "$value": "{color.grey.400}",
    "$description": "Mocne półprzezroczyste tło — 35% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.35", "space": "srgb" }
      }
    }
  },
  "strongHover": {
    "$type": "color",
    "$value": "{color.grey.500}",
    "$description": "Mocne półprzezroczyste tło — hover — 25% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.25", "space": "srgb" }
      }
    }
  }
}
```

### 3b. Token `overlay.solid` (nowy, do tooltipów)

Dodaj do istniejącego bloku `overlay` (obok `overlay.default`):

```json
"solid": {
  "$type": "color",
  "$value": "{color.grey.800}",
  "$description": "Ciemne tło tooltipu — grey.800 @ 90% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
  "$extensions": {
    "studio.tokens": {
      "modify": { "type": "alpha", "value": "0.90", "space": "srgb" }
    }
  }
}
```

---

## Krok 4 — Dodaj do Light.json

Ścieżka: `color → background`

- Dodaj blok `"translucent": { ... }` **po `"overlay"`** (przed `"custom"`)
- Dodaj `"solid": { ... }` **wewnątrz istniejącego bloku `"overlay"`**

Kolejność bloków w `color.background` po edycji:
```
base → brand → success → warning → danger → neutral → overlay (+ solid) → translucent → custom
```

---

## Krok 5 — Dodaj do Dark.json

W Dark.json **wszystkie referencje do primitywów muszą używać prefiksu `color-dark.*`** (nie `color.*`). To obowiązująca konwencja — sprawdź ją w istniejących tokenach Dark.json np. `base.strong = {color-dark.grey.400}`.

Paleta `color-dark.grey` jest odwrócona względem `color.grey`:
- `color-dark.grey.300` = `#5C6063` (ciemny szary)
- `color-dark.grey.400` = `#7D8286` (ciemny szary medium)
- `color-dark.grey.500` = `#9DA4A9` (szary medium)
- `color-dark.grey.700` = `#DEE2E3` (jasny szary)
- `color-dark.grey.800` = `#ECEEEF` (bardzo jasny)

Sprawdź istniejący blok `overlay` w Dark.json — analogicznie do Light.

Dodaj bloki:

### `translucent` w Dark.json

W dark mode efekt szkła na ciemnym tle uzyskujemy przez jasne kolory z niskim kryciem. Używamy `color-dark.grey.700–800` (jasne odcienie w ciemnej palecie) dla efektu brightening na ciemnych powierzchniach.

> ⚠️ **WYMAGANA weryfikacja designera** — wartości odcieni dla Dark.json są propozycją techniczną. Efekt wizualny jest odwrotny do Light: jasny overlay na ciemnym tle. Zatwierdź z designerem przed mergeowaniem.

```json
"translucent": {
  "none": {
    "$type": "color",
    "$value": "{color-dark.transparent}",
    "$description": "Przezroczyste tło (brak wypełnienia). [TYMCZASOWE — do wycofania po tokenizacji modules]"
  },
  "subtle": {
    "$type": "color",
    "$value": "{color-dark.grey.700}",
    "$description": "Subtelne półprzezroczyste tło (dark) — jasny kolor 10% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.10", "space": "srgb" }
      }
    }
  },
  "subtleHover": {
    "$type": "color",
    "$value": "{color-dark.grey.700}",
    "$description": "Subtelne półprzezroczyste tło (dark) — hover — 15% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.15", "space": "srgb" }
      }
    }
  },
  "subtleActive": {
    "$type": "color",
    "$value": "{color-dark.grey.700}",
    "$description": "Subtelne półprzezroczyste tło (dark) — active — 25% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.25", "space": "srgb" }
      }
    }
  },
  "muted": {
    "$type": "color",
    "$value": "{color-dark.grey.600}",
    "$description": "Stonowane półprzezroczyste tło (dark) — 10% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.10", "space": "srgb" }
      }
    }
  },
  "mutedHover": {
    "$type": "color",
    "$value": "{color-dark.grey.700}",
    "$description": "Stonowane półprzezroczyste tło (dark) — hover — 15% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.15", "space": "srgb" }
      }
    }
  },
  "mutedActive": {
    "$type": "color",
    "$value": "{color-dark.grey.700}",
    "$description": "Stonowane półprzezroczyste tło (dark) — active — 25% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.25", "space": "srgb" }
      }
    }
  },
  "strong": {
    "$type": "color",
    "$value": "{color-dark.grey.700}",
    "$description": "Mocne półprzezroczyste tło (dark) — 35% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.35", "space": "srgb" }
      }
    }
  },
  "strongHover": {
    "$type": "color",
    "$value": "{color-dark.grey.800}",
    "$description": "Mocne półprzezroczyste tło (dark) — hover — 25% krycia. [TYMCZASOWE — do wycofania po tokenizacji modules]",
    "$extensions": {
      "studio.tokens": {
        "modify": { "type": "alpha", "value": "0.25", "space": "srgb" }
      }
    }
  }
}
```

### `overlay.solid` w Dark.json

W dark mode tooltip może być jasny (standard inwersji). `color-dark.grey.800` = `#ECEEEF` (bardzo jasny szary) @ 90% tworzy białawy tooltip na ciemnym tle.

> ⚠️ Weryfikacja designera: czy tooltip w dark mode ma być jasny (inwersja) czy ciemny (taki sam jak w light)?

```json
"solid": {
  "$type": "color",
  "$value": "{color-dark.grey.800}",
  "$description": "Tło tooltipu (dark mode) — color-dark.grey.800 @ 90%. [TYMCZASOWE — do wycofania po tokenizacji modules]",
  "$extensions": {
    "studio.tokens": {
      "modify": { "type": "alpha", "value": "0.90", "space": "srgb" }
    }
  }
}
```

---

## Krok 6 — Napraw tokeny modułowe w base.json

Znajdź i zamień **tylko poniższe konkretne tokeny** w `tokens/modules/base.json`. Nie modyfikuj nic poza wymienionymi.

### 6a. `buttons.variant.tertiary.bg`

Obecny stan (3 tokeny do zmiany):
```json
"default":  { "$value": "{color.background.base.strong}26" },
"focus":    { "$value": "{color.background.base.strong}26" },
"disabled": { "$value": "{color.background.base.strong}26" }
```

Nowy stan — zamień KAŻDY z trzech na:
```json
{
  "$type": "color",
  "$value": "{color.background.base.strong}",
  "$description": "Tertiary button background — semi-transparent grey @ 15% (alpha modifier replaces legacy hex-alpha suffix)",
  "$extensions": {
    "studio.tokens": {
      "modify": { "type": "alpha", "value": "0.15", "space": "srgb" }
    }
  }
}
```

> Token `hover` i `active` dla tertiary — **nie zmieniaj**, używają pełnych opaque kolorów.

### 6b. `buttons.variant.tertiary-white.bg.disabled`

Obecny stan:
```json
"disabled": { "$value": "{color.background.neutral.solid}26" }
```

Nowy stan:
```json
"disabled": {
  "$type": "color",
  "$value": "{color.background.neutral.solid}",
  "$description": "Tertiary-white button background disabled — semi-transparent dark grey @ 15% (alpha modifier replaces legacy hex-alpha suffix)",
  "$extensions": {
    "studio.tokens": {
      "modify": { "type": "alpha", "value": "0.15", "space": "srgb" }
    }
  }
}
```

> Tokeny `default`, `hover`, `focus` dla tertiary-white — **nie zmieniaj**.

---

## Krok 7 — Zwaliduj JSON

```bash
python3 -c "import json; json.load(open('tokens/semantic/Light.json')); print('Light.json OK')"
python3 -c "import json; json.load(open('tokens/semantic/Dark.json')); print('Dark.json OK')"
python3 -c "import json; json.load(open('tokens/modules/base.json')); print('base.json OK')"
```

Jeśli błąd — znajdź i napraw przed kontynuowaniem.

---

## Krok 8 — Zaktualizuj CONTEXT.md

W `instructions/CONTEXT.md` znajdź sekcję `## Matryca semantyczna — skrót` → `### Background` i zaktualizuj listę kategorii:

```markdown
### Background
`color.background.{base|brand|success|warning|danger|neutral|overlay|translucent|supply|service|entity}.*`
```

Dodaj też adnotację o translucent w sekcji opisującej warstwę semantic:

```markdown
> ⚠️ **Tokeny tymczasowe:** Kategoria `translucent` oraz `overlay.solid` są pomostem dla Phase 1 (direct semantic usage). Zostaną wycofane po pełnej tokenizacji warstwy modules.
```

Jeśli w CONTEXT.md jest tabela `**Hex-alpha suffix (`{token}26`)**` lub wzmianka o notacji hex-alpha — zaktualizuj ją, zaznaczając że alpha modifier jest właściwą składnią.

---

## Krok 9 — Zaktualizuj docs/04-kolory.md

Znajdź sekcję opisującą kategorie tła (background categories) lub matrycę semantyczną. Dodaj opis nowych kategorii:

### Fragment do dodania:

```markdown
#### Translucent *(tymczasowe)*

Kategoria `color.background.translucent.*` dostarcza półprzezroczyste tła przeznaczone do użycia na kolorowych lub ciemnych powierzchniach — np. przyciski tertiary na ciemnym headerze. Używa składni alpha modifier zamiast hex-alpha suffix.

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

> ⚠️ Kategoria tymczasowa — zostanie wycofana po tokenizacji modules.

#### Overlay

| Token | Zastosowanie |
|-------|--------------|
| `overlay.default` | Maska modali (`grey.900 @ 50%`) |
| `overlay.solid` | Tło tooltipu (`grey.800 @ 90%`) *(tymczasowe)* |
```

Upewnij się, że sekcja wyjaśnia różnicę między `translucent` (wielokrotne stany) a `overlay` (jednorazowe zastosowanie).

---

## Krok 10 — Przedstaw podsumowanie

Po zakończeniu wylistuj:

1. **Semantic (Light.json)**: ile tokenów dodano w `translucent` + ile w `overlay`
2. **Semantic (Dark.json)**: analogicznie
3. **Modules (base.json)**: ile tokenów zmieniono (powinno być 4: tertiary ×3 + tertiary-white ×1)
4. Czy JSON przeszedł walidację
5. Przypomnienie: Dark.json translucent wymaga weryfikacji designera

---

## Mapa zmian — quick reference

| Plik | Zmiana | Tokeny |
|------|--------|--------|
| `tokens/semantic/Light.json` | Nowa kategoria `translucent` | 9 |
| `tokens/semantic/Light.json` | Nowy token `overlay.solid` | 1 |
| `tokens/semantic/Dark.json` | Nowa kategoria `translucent` | 9 |
| `tokens/semantic/Dark.json` | Nowy token `overlay.solid` | 1 |
| `tokens/modules/base.json` | Fix hex-alpha → modifier: tertiary.bg.default/focus/disabled | 3 |
| `tokens/modules/base.json` | Fix hex-alpha → modifier: tertiary-white.bg.disabled | 1 |
| `instructions/CONTEXT.md` | Aktualizacja matrycy + notatka o tymczasowości | — |
| `docs/04-kolory.md` | Opis kategorii translucent + overlay | — |

---

## Zasady

- Nie dodawaj hardcoded hex ani rgba — tylko referencje do primitives + modifier
- Tokeny `color.background.translucent.*` i `overlay.solid` w semantic → `$description` z `[TYMCZASOWE]`
- Tokeny modułowe (tertiary, tertiary-white) → **bez** `[TYMCZASOWE]` — zmiana notacji jest stała
- Nie modyfikuj `overlay.default` (modal mask — zostaje bez zmian)
- Nie modyfikuj innych tokenów modułowych poza wymienionymi w Kroku 6
- **Light.json** → referencje `{color.grey.*}` i `{color.transparent}`
- **Dark.json** → referencje `{color-dark.grey.*}` i `{color-dark.transparent}` (bez wyjątku)
- Dark.json translucent = propozycja wymagająca zatwierdzenia designera — zaznacz to w commit message

---

*Powiązane: `instructions/CONTEXT.md` · `docs/04-kolory.md` · `tokens/semantic/Light.json` · `tokens/semantic/Dark.json` · `tokens/modules/base.json`*
