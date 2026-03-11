# Workflow: Analiza tokenów kolorystycznych vs Figma

> Instrukcja dla Claude jak analizować komponent z Figmy i walidować/tworzyć tokeny modułowe.
> Claude ma dostęp do Figmy przez MCP — nie potrzebuje danych z Inspect od użytkownika.

---

## Kiedy używasz tego workflow?

- Użytkownik podaje link do komponentu w Figmie
- Chcesz sprawdzić czy istniejące tokeny modułowe są poprawne
- Chcesz stworzyć tokeny dla nowego komponentu
- Chcesz zwalidować referencje (czy moduł wskazuje na właściwy semantic)

---

## Format linku Figma

```
https://www.figma.com/design/{file-id}/branch/{branch-id}/{name}?node-id={node-id}
```

**Zamiana node-id:** w URL jest `17814-54284` → w API używamy `17814:54284` (myślnik na dwukropek)

---

## Dostępne narzędzia MCP

| Narzędzie | Co zwraca |
|-----------|-----------|
| `Figma:get_screenshot` | Wizualny podgląd komponentu |
| `Figma:get_design_context` | Pełne dane: kolory, spacing, typografia, struktura |
| `Figma:get_variable_defs` | Zmienne/tokeny już przypisane do node w Figmie |

---

## Krok po kroku — co robi Claude

### Krok 1: Pobierz dane z Figmy automatycznie

Gdy użytkownik poda link — bez pytania wywołaj wszystkie trzy narzędzia:

1. `Figma:get_screenshot` → podgląd wizualny
2. `Figma:get_design_context` → wszystkie właściwości
3. `Figma:get_variable_defs` → już przypisane tokeny

---

### Krok 2: Przeanalizuj dane

Z `get_design_context` wyciągasz:
- Fill → bg token
- Stroke color + width → border tokens
- Text color → text/label token
- Border radius → border.radius token
- Opacity → opacity token (dla disabled)

Z `get_variable_defs` sprawdzasz:
- Jakie tokeny są już przypisane?
- Czy są zgodne z naszą architekturą?

---

### Krok 3: Zmapuj na tokeny

Dla każdej wartości: primitive → semantic (matryca z `docs/06-wyszukiwanie-i-tworzenie-tokenow.md`) → module

```
Figma fill: #0b68ff
→ color.blue.600
→ color.background.brand.solid
→ buttons.variant.primary.bg.default = {color.background.brand.solid}
```

---

### Krok 4: Porównaj z repo i przedstaw plan

```
✅ Token istnieje i jest poprawny
⚠️ Token istnieje ale wskazuje na zły semantic
❌ Token nie istnieje — trzeba stworzyć
🆕 Brakuje tokena semantycznego — uzgodnić przed dodaniem
```

**ZAWSZE czekaj na akceptację planu przed generowaniem JSON.**

---

### Krok 5: Po akceptacji — wygeneruj JSON

Format Token Studio:

```json
{
  "buttons": {
    "variant": {
      "primary": {
        "bg": {
          "default": {
            "$value": "{color.background.brand.solid}",
            "$type": "color",
            "$description": "Tło głównego przycisku — stan domyślny"
          }
        }
      }
    }
  }
}
```

---

### Krok 6: Instrukcja wgrania

Wszystkie tokeny modułowe trzymamy w **jednym pliku** `tokens/modules/base.json`.
Każdy komponent to osobny blok JSON (klucz najwyższego poziomu).

```
1. VS Code → tokens/modules/base.json
2. Znajdź blok komponentu (np. "buttons", "avatar") i edytuj go
   lub dodaj nowy blok jeśli komponent nie istnieje w pliku
3. Source Control → commit → Sync
4. Figma → Token Studio → Pull
```

---

## Zasady

- Nigdy primitive ani semantic jako wartość tokena modułowego
- Zawsze plan przed JSON — czekaj na akceptację
- Nie renameujesz istniejących tokenów
- Nowe tokeny semantyczne zawsze konsultujesz
- Generujesz tokeny dla wszystkich stanów naraz

---

*Powiązane: `instructions/CONTEXT.md` / `docs/05-tokeny-modulowe.md` / `docs/06-wyszukiwanie-i-tworzenie-tokenow.md`*
