# Design Tokens — Dokumentacja

**Synerise Design System v2**

---

## Spis treści

### Podstawy

1. **[Wprowadzenie](./01-wprowadzenie.md)**
   - Czym są design tokeny?
   - Kluczowa zasada: aplikujemy tokeny modułowe
   - Dlaczego tokeny modułowe?

2. **[Warstwy tokenów](./02-warstwy.md)**
   - Model 3-warstwowy: Primitives → Semantic → Module
   - Dlaczego nie aplikujemy Semantic bezpośrednio?
   - Light Mode vs Dark Mode

3. **[Nazewnictwo tokenów](./03-nazewnictwo.md)**
   - Przegląd struktur dla każdej warstwy
   - Zasady nazewnictwa
   - Transformacja między platformami

### Szczegóły

4. **[Kolory](./04-kolory.md)**
   - Paleta Primitives (Core)
   - Tokeny Semantic — Background, Text
   - Mapowanie: Semantic → Module

5. **[Tokeny modułowe](./05-tokeny-modulowe.md)** ⭐
   - Pełna konwencja nazewnictwa
   - Container dla organizmów
   - Przykłady: Buttons, Avatar, Status Pill
   - Do / Don't

6. **[Wyszukiwanie i tworzenie tokenów](./06-wyszukiwanie-i-tworzenie-tokenow.md)** 🆕
   - Matryca tokenów semantycznych
   - Workflow krok po kroku
   - Jak tworzyć brakujące tokeny
   - Słownik pojęć

---

## Kluczowa zasada

> ⚠️ Na produkt aplikujemy **wyłącznie tokeny modułowe** — nigdy primitives ani semantic bezpośrednio.

```
❌ color.blue.600                    (primitive)
❌ color.background.brand.solid      (semantic)
✅ buttons.variant.primary.bg.default (module)
```

---

## Szybki start

### Dla Designerów

1. W Figma używaj **Figma Variables** z namespace komponentu
2. Wybieraj tokeny `buttons/`, `avatar/`, `status-pill/` etc.
3. **Nie używaj** tokenów `color/background/` ani `color/blue/`
4. Szukasz tokena? → [Matryca tokenów](./06-wyszukiwanie-i-tworzenie-tokenow.md)

### Dla Developerów

1. Używaj zmiennych CSS wygenerowanych z tokenów modułowych:
   ```css
   background-color: var(--buttons-variant-primary-bg-default);
   ```
2. **Nigdy nie hardcoduj** wartości hex ani tokenów semantic

---

## Struktura nazewnictwa — ściągawka

### Primitives
```
color.{kolor}.{skala}
→ color.blue.600
```

### Semantic
```
color.{property}.{category}.{modifier}{State}
→ color.background.brand.solidHover
```

### Module ⭐
```
{component}.{variant}.{element}.{property}.{state}
→ buttons.variant.primary.bg.default
```

---

## Kontakt

Pytania? → Design System Team

---

*Wersja 2.0 — Styczeń 2026*
