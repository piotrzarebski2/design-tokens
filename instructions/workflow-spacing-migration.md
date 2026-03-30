# Workflow: Migracja tokenów odstępów (Spacing Migration)

> Instrukcja dla Claude Code — jednorazowa migracja systemu tokenów spacing.
> Wykonaj w całości na branchu `feature/spacing-tokens`.

---

## Kontekst i cel

Obecny system tokenów `space.*` w `tokens/primitives/core.json` zawiera 22 niejednorodne wartości — mix siatki ×4 i wartości off-grid. Zadaniem jest:

1. Zastąpić je nową skalą opartą o mnożnik (jednostka = 4px)
2. Dodać oddzielną skalę `size.*` dla wysokości komponentów
3. Stworzyć warstwę semantyczną (`inset` / `stack` / `inline`) w nowym pliku
4. Zaktualizować `tokens/modules/base.json` — każdy komponent używa tokenów semantycznych
5. Napisać dokumentację `docs/07-tokeny-odleglosci.md`
6. Zaktualizować `instructions/CONTEXT.md` o architekturę spacing

---

## Krok 1 — Stwórz branch

```bash
git checkout -b feature/spacing-tokens
```

---

## Krok 2 — Zaktualizuj `tokens/primitives/core.json`

### Usuń stare tokeny `space`
Usuń cały blok `"space"` z pliku.

### Wstaw nowe tokeny `space` i `size`

```json
"space": {
  "025": { "$type": "dimension", "$value": "1px",   "$description": "Hairline — separatory, obramowania. Nie spacing." },
  "05":  { "$type": "dimension", "$value": "2px",   "$description": "Micro offset — focus ring. Nie spacing." },
  "1":   { "$type": "dimension", "$value": "4px",   "$description": "1 × 4px" },
  "2":   { "$type": "dimension", "$value": "8px",   "$description": "2 × 4px" },
  "3":   { "$type": "dimension", "$value": "12px",  "$description": "3 × 4px" },
  "4":   { "$type": "dimension", "$value": "16px",  "$description": "4 × 4px" },
  "5":   { "$type": "dimension", "$value": "20px",  "$description": "5 × 4px" },
  "6":   { "$type": "dimension", "$value": "24px",  "$description": "6 × 4px" },
  "8":   { "$type": "dimension", "$value": "32px",  "$description": "8 × 4px" },
  "10":  { "$type": "dimension", "$value": "40px",  "$description": "10 × 4px" },
  "12":  { "$type": "dimension", "$value": "48px",  "$description": "12 × 4px" },
  "16":  { "$type": "dimension", "$value": "64px",  "$description": "16 × 4px" },
  "20":  { "$type": "dimension", "$value": "80px",  "$description": "20 × 4px" },
  "32":  { "$type": "dimension", "$value": "128px", "$description": "32 × 4px" }
},
"size": {
  "1": { "$type": "dimension", "$value": "24px",  "$description": "Badge, status pill" },
  "2": { "$type": "dimension", "$value": "32px",  "$description": "Compact list item, nav item" },
  "3": { "$type": "dimension", "$value": "40px",  "$description": "Standard input, button" },
  "4": { "$type": "dimension", "$value": "48px",  "$description": "Large button, dropdown footer" },
  "5": { "$type": "dimension", "$value": "56px",  "$description": "Navbar, large list item" },
  "6": { "$type": "dimension", "$value": "64px",  "$description": "App menu zone" },
  "7": { "$type": "dimension", "$value": "80px",  "$description": "Page header area" }
}
```

---

## Krok 3 — Stwórz `tokens/semantic/spacing.json`

Nowy plik. Zawiera tokeny `inset`, `stack`, `inline` z wartościami t-shirt.

```json
{
  "inset": {
    "xs":  { "$type": "dimension", "$value": "{space.1}", "$description": "4px — gęste UI: tagi, badges, tiny chips" },
    "sm":  { "$type": "dimension", "$value": "{space.2}", "$description": "8px — kompaktowe: badge padding, małe buttony" },
    "md":  { "$type": "dimension", "$value": "{space.3}", "$description": "12px — standard: inputy, nav items" },
    "lg":  { "$type": "dimension", "$value": "{space.4}", "$description": "16px — wygodne: card content, sidebar" },
    "xl":  { "$type": "dimension", "$value": "{space.6}", "$description": "24px — przestronne: modal body, card padding" },
    "2xl": { "$type": "dimension", "$value": "{space.8}", "$description": "32px — duże sekcje" }
  },
  "stack": {
    "xs":  { "$type": "dimension", "$value": "{space.1}", "$description": "4px — ikona + label, helper text" },
    "sm":  { "$type": "dimension", "$value": "{space.2}", "$description": "8px — pola formularza, elementy listy" },
    "md":  { "$type": "dimension", "$value": "{space.3}", "$description": "12px — standardowe sekcje w komponencie" },
    "lg":  { "$type": "dimension", "$value": "{space.4}", "$description": "16px — grupy fieldów, bloki" },
    "xl":  { "$type": "dimension", "$value": "{space.6}", "$description": "24px — sekcje strony" },
    "2xl": { "$type": "dimension", "$value": "{space.8}", "$description": "32px — duże sekcje layoutu" }
  },
  "inline": {
    "xs":  { "$type": "dimension", "$value": "{space.1}", "$description": "4px — ikona + tekst" },
    "sm":  { "$type": "dimension", "$value": "{space.2}", "$description": "8px — między tagami, chipami" },
    "md":  { "$type": "dimension", "$value": "{space.3}", "$description": "12px — między polami formularza" },
    "lg":  { "$type": "dimension", "$value": "{space.4}", "$description": "16px — między blokami UI" },
    "xl":  { "$type": "dimension", "$value": "{space.6}", "$description": "24px — między sekcjami layoutu" }
  }
}
```

---

## Krok 4 — Zaktualizuj `$metadata.json`

Dodaj `semantic/spacing` do listy tokenSetów, **przed** `modules/base`:

```json
"tokenSetOrder": [
  "primitives/core",
  "semantic/Light",
  "semantic/Dark",
  "semantic/spacing",
  "surface/base",
  "modules/base"
]
```

---

## Krok 5 — Zaktualizuj `tokens/modules/base.json`

Zaktualizuj istniejące komponenty aby używały nowych tokenów semantycznych.

### Zasada migracji wartości off-grid

**Nie zaokrąglaj twardo** wartości poza siatką — ryzyko niekontrolowanych zmian wyglądu.
Każdą wartość off-grid zapisz jako wyrażenie: **najbliższy token semantyczny ± korekta**.

```json
// 7px → {inset.sm} - 1
"input.padding.y": { "$value": "{inset.sm} - 1", "$description": "TODO: visual diff → docelowo {inset.sm}" }

// 14px → {inset.lg} - 2
"dropdown.group-title.padding.top": { "$value": "{inset.lg} - 2", "$description": "TODO: visual diff → docelowo {inset.lg}" }

// 3px → {inset.xs} - 1
"tabs.padding.left": { "$value": "{inset.xs} - 1", "$description": "TODO: visual diff → docelowo {inset.xs}" }
```

Zasada: **każdy token modułowy opisujący odstęp musi referencować semantic (`inset.*`, `stack.*`, `inline.*`) lub `space.*` / `size.*` — nigdy px wprost.**

#### Komponenty do zaktualizowania:

**buttons** — padding:
```json
"buttons": {
  "padding": {
    "y":  { "$value": "{inset.sm}", "$type": "dimension" },
    "x":  { "$value": "{inset.lg}", "$type": "dimension" }
  }
}
```

**form / switch**:
```json
"form": {
  "switch": {
    "size": {
      "handle": { "$value": "{space.3}", "$type": "dimension" }
    }
  }
}
```

**divider**:
```json
"divider": {
  "line": {
    "height": { "$value": "{space.4}", "$type": "dimension" }
  }
}
```

**avatar** — zamień stare `space.*` na nowe:
```json
"avatar": {
  "size": {
    "small":  { "$value": "{size.1}", "$type": "dimension" },
    "medium": { "$value": "{size.3}", "$type": "dimension" },
    "large":  { "$value": "{space.20}", "$type": "dimension" },
    "xlarge": { "$value": "{space.32}", "$type": "dimension" }
  }
}
```

**status-pill**:
```json
"status-pill": {
  "size": {
    "height":           { "$value": "{size.1}", "$type": "dimension" },
    "padding": {
      "horizontal":     { "$value": "{inset.sm}", "$type": "dimension" },
      "vertical":       { "$value": "{space.025}", "$type": "dimension" }
    }
  }
}
```

**list-item** — wysokości jako `size.*`, bez padding-y:
```json
"list-item": {
  "container": {
    "height": {
      "standard":          { "$value": "{size.2}", "$type": "dimension" },
      "withAvatar":        { "$value": "{size.3}", "$type": "dimension" },
      "withAvatarLarge":   { "$value": "{size.4}", "$type": "dimension" },
      "withDescription":   { "$value": "{space.12}", "$type": "dimension" }
    },
    "padding": {
      "x": { "$value": "{inset.md}", "$type": "dimension" }
    }
  }
}
```

**navbar**:
```json
"navbar": {
  "height":  { "$value": "{size.5}", "$type": "dimension" },
  "padding": {
    "x": { "$value": "{inset.xl}", "$type": "dimension" }
  },
  "left": {
    "logo":      { "height": { "$value": "{space.3}", "$type": "dimension" } },
    "separator": {
      "width":  { "$value": "{space.025}", "$type": "dimension" },
      "height": { "$value": "{space.6}", "$type": "dimension" }
    }
  },
  "right": {
    "separator": {
      "width":  { "$value": "{space.025}", "$type": "dimension" },
      "height": { "$value": "{space.6}", "$type": "dimension" }
    },
    "userButton": {
      "padding": {
        "horizontal": { "$value": "{inset.sm}", "$type": "dimension" },
        "vertical":   { "$value": "{inset.xs}", "$type": "dimension" }
      }
    }
  }
}
```

**modal**:
```json
"modal": {
  "header": {
    "separator": { "height": { "$value": "{space.025}", "$type": "dimension" } },
    "padding": {
      "y": { "$value": "{space.5}", "$type": "dimension" },
      "x": { "$value": "{inset.xl}", "$type": "dimension" }
    }
  },
  "body": {
    "padding": { "$value": "{inset.xl}", "$type": "dimension" }
  }
}
```

**broadcast-bar**:
```json
"broadcast-bar": {
  "content": {
    "padding": {
      "x": {
        "left":  { "$value": "{space.16}", "$type": "dimension" },
        "right": { "$value": "{inset.lg}", "$type": "dimension" }
      }
    }
  }
}
```

**page-header**:
```json
"page-header": {
  "navigation": {
    "back": {
      "separator": {
        "width":  { "$value": "{space.025}", "$type": "dimension" },
        "height": { "$value": "{size.3}", "$type": "dimension" }
      }
    },
    "avatar": { "size": { "$value": "{size.3}", "$type": "dimension" } }
  },
  "tabs": {
    "container": { "padding": { "vertical": { "$value": "{space.025}", "$type": "dimension" } } },
    "item": {
      "padding": {
        "vertical": { "$value": "{space.05}", "$type": "dimension" },
        "bottom":   { "$value": "{inset.md}", "$type": "dimension" }
      }
    }
  }
}
```

**app-menu**:
```json
"app-menu": {
  "container": { "border": { "width": { "$value": "{space.025}", "$type": "dimension" } } },
  "expanded":  { "width": { "$value": "{space.32}", "$type": "dimension" } },
  "icons":     { "size": { "$value": "{size.3}", "$type": "dimension" } },
  "separator": { "height": { "$value": "{space.025}", "$type": "dimension" } },
  "header":    { "padding": { "vertical": { "$value": "{inset.xl}", "$type": "dimension" } } }
}
```

**stepper**:
```json
"stepper": {
  "padding": { "$value": "{inset.lg}", "$type": "dimension" }
}
```

**card**:
```json
"card": {
  "padding": { "$value": "{inset.xl}", "$type": "dimension" }
}
```

**toast / section-message**:
```json
"toast": {
  "padding": {
    "y": { "$value": "{inset.md}", "$type": "dimension" },
    "x": { "$value": "{inset.lg}", "$type": "dimension" }
  }
},
"section-message": {
  "padding": {
    "y": { "$value": "{inset.md}", "$type": "dimension" },
    "x": { "$value": "{inset.lg}", "$type": "dimension" }
  }
}
```

**pagination**:
```json
"pagination": {
  "padding": { "$value": "{inset.md}", "$type": "dimension" }
}
```

**progressbar**:
```json
"progressbar": {
  "height": { "$value": "{size.1}", "$type": "dimension" }
}
```

---

## Krok 6 — Weryfikacja

Po zapisaniu plików sprawdź:

```bash
# 1. Brak starych tokenów space.* z wartościami off-grid
grep -r '"6px"\|"14px"\|"26px"\|"84px"' tokens/

# 2. Brak hardkodowanych px w modules (poza wartościami zaakceptowanymi)
grep -r '"[0-9]*px"' tokens/modules/base.json | grep -v "space\|size\|inset\|stack\|inline"

# 3. Wszystkie referencje w modules wskazują na istniejące tokeny
# (sprawdź ręcznie kilka kluczowych)
```

---

## Krok 7 — Napisz dokumentację

Plik `docs/07-tokeny-odleglosci.md` już istnieje — sprawdź czy jest aktualny z wartościami z tego workflowu. Jeśli cokolwiek się różni, zaktualizuj dokumentację.

---

## Krok 8 — Zaktualizuj `instructions/CONTEXT.md`

Dodaj do sekcji `Architektura tokenów — model 3-warstwowy` nową podsekcję `Spacing`:

```markdown
### Spacing (odległości)

**Primitives:**
- `space.{mnożnik}` — jednostka 4px, np. `space.6 = 24px` (6 × 4)
- `space.025` = 1px, `space.05` = 2px — micro, wyłącznie do separatorów/borderów
- `size.{n}` — wysokości komponentów: `size.1`=24px … `size.7`=80px

**Semantic (t-shirt):**
- `inset.{xs|sm|md|lg|xl|2xl}` — padding kontenera (uniform, wszystkie strony)
- `stack.{xs|sm|md|lg|xl|2xl}` — gap pionowy (flex column / grid row)
- `inline.{xs|sm|md|lg|xl}` — gap poziomy (flex row / grid column)

**Zasady:**
- Komponenty z `height: {size.*}` → **nie używają padding-y**, centrują przez `align-items: center`
- Padding nieuniformowy (X ≠ Y) → dwa osobne tokeny w module: `padding.x` + `padding.y`
- Token `{inset.*} - 1` dozwolony tylko jako strategia przejściowa (faza migracji)
```

Zaktualizuj też sekcję `Aktywne wątki do pracy`:
- Przenieś `Tokeny odległościowe` z `🚧 W toku` do `✅ Gotowe / stabilne`
- Dodaj nowy punkt w `🚧 W toku`: `Tokenizacja komponentów (spacing) — aplikowanie tokenów modułowych spacing komponent po komponencie`

---

## Krok 9 — Commit i PR

```bash
git add tokens/primitives/core.json
git add tokens/semantic/spacing.json
git add tokens/modules/base.json
git add tokens/$metadata.json
git add docs/07-tokeny-odleglosci.md
git add instructions/CONTEXT.md

git commit -m "feat: introduce spacing token architecture (primitives + semantic + modules)

- Replace 22 non-uniform space.* primitives with multiplier-based scale (unit=4px)
- Add size.* primitive scale for component heights (separated from spacing)
- Add semantic/spacing.json with inset/stack/inline t-shirt tokens
- Update modules/base.json: all spacing values now reference semantic tokens
- Add docs/07-tokeny-odleglosci.md
- Update CONTEXT.md with spacing architecture"

gh pr create --title "feat: spacing token architecture" \
  --body "Full spacing token system: primitives (multiplier scale) + semantic (inset/stack/inline) + modules migration. See docs/07-tokeny-odleglosci.md for details."
```

---

## Zasady ogólne przy pracy z tokenami spacing (na przyszłość)

Gdy tokenizujesz nowy komponent i napotkasz wartość spacingu:

1. **Sprawdź** czy wartość jest na siatce ×4
2. **Jeśli tak** → znajdź odpowiedni token semantyczny (`inset.*`, `stack.*`, `inline.*`) lub `size.*` dla wysokości
3. **Jeśli nie** (wartość off-grid) → zastosuj strategię `{token} - N` i zostaw komentarz `$description` z adnotacją "TODO: verify visual diff, round to {token}"
4. **Dla high-density widoków** (np. builder, canvas) — off-grid wartości mogą być intencjonalne; konsultuj przed zmianą
5. **Dla elementów z fixed height** → padding-y jest zbędny; użyj `align-items: center`

---

*Powiązane: `instructions/CONTEXT.md` / `docs/07-tokeny-odleglosci.md` / `workflow-color-analysis.md`*
