# Workflow: Dodanie nowych kategorii semantycznych (Supply, Service, Entity)

> Instrukcja dla Claude Code. Dodaje trzy nowe kategorie kolorów semantycznych analogicznie do istniejących `success`, `warning`, `danger`. Dotyczy warstwy semantic (Light.json + Dark.json) oraz aktualizacji dokumentacji.

---

## Kiedy używasz tego workflow?

Gdy trzeba dodać nową kategorię kolorystyczną do warstwy semantic — w tym przypadku:
- `supply` — zasilanie danymi / data feeds
- `service` — usługi / API
- `entity` — encje / rekordy danych

---

## Kolory źródłowe (primitives)

Każda kategoria mapuje na jedną rodzinę kolorów z primitives. Domyślne przypisanie (na podstawie użycia w `section-message`):

| Kategoria | Primitive family | Uzasadnienie |
|-----------|-----------------|--------------|
| `supply`  | `color.cyan.*`  | cyan używany w section-message dla supply |
| `service` | `color.purple.*`| purple używany w section-message dla service |
| `entity`  | `color.violet.*`| violet używany w section-message dla entity |

> ⚠️ **Przed wykonaniem: potwierdź z Piotrem które primitive family idzie do której kategorii.** Jeśli jest inaczej — podmień odpowiednio w krokach poniżej.

---

## Struktura do dodania

Wzorzec: analogicznie do `success` (sprawdź przed wykonaniem):

```
color.background.{kategoria}.subtle        = {color.{family}.50}
color.background.{kategoria}.subtleHover   = {color.{family}.100}
color.background.{kategoria}.solid         = {color.{family}.600}
color.background.{kategoria}.solidHover    = {color.{family}.500}
color.background.{kategoria}.solidActive   = {color.{family}.700}

color.text.{kategoria}.default             = {color.{family}.700}

color.border.{kategoria}.default           = {color.{family}.600}
color.border.{kategoria}.subtle            = {color.{family}.100}
color.border.{kategoria}.strong            = {color.{family}.200}

color.icon.{kategoria}.default             = {color.{family}.600}
```

---

## Krok 1 — Sprawdź istniejący wzorzec

Przeczytaj `tokens/semantic/Light.json` i znajdź strukturę dla `success`:

```
color → background → success → subtle, subtleHover, solid, solidHover, solidActive
color → text → success → default
color → border → success → default, subtle, strong
color → icon → success → default
```

Zapamiętaj dokładną strukturę JSON — użyjesz jej jako szablonu.

---

## Krok 2 — Przygotuj bloki JSON dla Light.json

Dla każdej z trzech kategorii przygotuj analogiczny blok. Przykład dla `supply` (cyan):

```json
"supply": {
  "subtle": {
    "$type": "color",
    "$value": "{color.cyan.50}",
    "$description": "Subtelne tło dla kategorii Supply — jasny odcień"
  },
  "subtleHover": {
    "$type": "color",
    "$value": "{color.cyan.100}",
    "$description": "Subtelne tło Supply — hover"
  },
  "solid": {
    "$type": "color",
    "$value": "{color.cyan.600}",
    "$description": "Solidne tło Supply — główny kolor akcentu"
  },
  "solidHover": {
    "$type": "color",
    "$value": "{color.cyan.500}",
    "$description": "Solidne tło Supply — hover"
  },
  "solidActive": {
    "$type": "color",
    "$value": "{color.cyan.700}",
    "$description": "Solidne tło Supply — active/pressed"
  }
}
```

Analogicznie dla `service` (purple) i `entity` (violet).

---

## Krok 3 — Dodaj do Light.json

Ścieżki w pliku gdzie dodajesz:

- `color → background` → dodaj `supply`, `service`, `entity` po `neutral`
- `color → text` → dodaj `supply`, `service`, `entity` po `neutral`
- `color → border` → dodaj `supply`, `service`, `entity` po `neutral`
- `color → icon` → dodaj `supply`, `service`, `entity` po `neutral`

**Sprawdź czy w primitives istnieją potrzebne odcienie.** Wywołaj:

```bash
cat tokens/primitives/core.json | python3 -c "
import json,sys; d=json.load(sys.stdin)
for family in ['cyan','purple','violet']:
    shades = d['color'].get(family,{})
    print(f'{family}: {list(shades.keys())}')
"
```

Jeśli któryś odcień nie istnieje w primitives — **zatrzymaj się i zgłoś to użytkownikowi** zamiast dodawać hardcoded hex.

---

## Krok 4 — Dodaj do Dark.json

Sprawdź jak Dark.json obsługuje istniejące kategorie (success, warning, danger) — zazwyczaj darker shades dla tła i lighter dla tekstu. Odwzoruj ten wzorzec dla nowych kategorii.

Przykład (zweryfikuj z istniejącą strukturą dark):
```json
"supply": {
  "subtle": { "$value": "{color.cyan.900}" },
  "subtleHover": { "$value": "{color.cyan.800}" },
  "solid": { "$value": "{color.cyan.600}" },
  "solidHover": { "$value": "{color.cyan.500}" },
  "solidActive": { "$value": "{color.cyan.700}" }
}
```

---

## Krok 5 — Zwaliduj JSON

```bash
python3 -c "import json; json.load(open('tokens/semantic/Light.json')); print('Light.json OK')"
python3 -c "import json; json.load(open('tokens/semantic/Dark.json')); print('Dark.json OK')"
```

Jeśli błąd — znajdź i napraw przed kontynuowaniem.

---

## Krok 6 — Zaktualizuj CONTEXT.md

W `instructions/CONTEXT.md` znajdź sekcję `## Matryca semantyczna — skrót` i dodaj nowe kategorie do każdej właściwości:

```markdown
### Background
`color.background.{base|brand|success|warning|danger|neutral|supply|service|entity}.*`

### Border
`color.border.{base|brand|success|warning|danger|neutral|supply|service|entity|...}.*`
```

Zaktualizuj też sekcję `**Categories:**` w opisie warstwy semantic.

---

## Krok 7 — Zaktualizuj docs/04-kolory.md

Znajdź sekcję opisującą kategorie semantyczne (success, warning, danger) i dodaj analogiczne opisy dla:

- **Supply** — kolory dla zasilania danymi, feedów, integracji
- **Service** — kolory dla usług, API, połączeń zewnętrznych
- **Entity** — kolory dla encji, rekordów, obiektów danych

Zachowaj styl i format istniejących opisów.

---

## Krok 8 — Przedstaw podsumowanie

Po zakończeniu wylistuj:
- Ile tokenów dodano (łącznie powinno być ~36: 3 kategorie × 4 właściwości × ~3 modifkatory)
- Czy wszystkie primitive references istniały
- Czy JSON jest poprawny

---

## Zasady

- Nie dodawaj hardcoded hex — tylko referencje do primitives
- Nie modyfikuj istniejących tokenów
- Zachowaj kolejność: background → text → border → icon
- Każdy token musi mieć `$description`
- Czekaj na potwierdzenie primitive family od użytkownika przed wykonaniem (krok 0)

---

*Powiązane: `instructions/CONTEXT.md` · `docs/04-kolory.md` · `tokens/semantic/Light.json`*
