---
name: feature-audit
description: "Precyzyjny audyt pojedynczej funkcji, metody, komponentu, pliku lub bieżącego kodu pod kątem czystego kodu, SOLID, obsługi błędów, bezpieczeństwa, testów (TDD, Detroit/London, AAA/GWT) i unikania overengineeringu."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "code-review", "feature-audit", "solid", "kiss", "tdd", "clean-code"]
---

# Audytor Funkcjonalności i Bieżącego Kodu (Feature Audit Skill)
> **Autor / Twórca:** Łukasz / Lukasz Zychal

Ten skill służy do przeprowadzania **precyzyjnego, szybkiego audytu konkretnego wycinka kodu** (poziom MIKRO):
- pojedynczej funkcji lub metody
- konkretnego pliku lub komponentu UI
- hooka, endpointu lub serwisu
- bieżącego taska lub diffa w trakcie prac deweloperskich

Stosuj go, gdy użytkownik prosi o:
- "zrób audyt tej funkcji / tego pliku"
- "sprawdź ten kod pod kątem dobrych praktyk"
- "przeanalizuj to co właśnie napisałem"
- "audyt funkcjonalności / feature audit"

---

## 🧭 Złota Zasada Poziomu Mikro (KISS & Anti-Overengineering)

1. **Prostota ponad formę:** Najlepszy kod to ten, którego nie trzeba pisać. Zwracaj uwagę na nadmierną komplikację — jeśli funkcja 20-liniowa rozwiązuje problem czysto i czytelnie, odradzaj tworzenie dla niej fabryk, strategii czy dodatkowych 3 warstw abstrakcji.
2. **Odporność na błędy (Edge Cases):** Kod produkcyjny różni się od prototypu obsługą sytuacji nieprzewidzianych (`null`, `undefined`, zerwanie połączenia, timeout, niepoprawny format danych).
3. **Praktyczny diff:** Zamiast ogólnych uwag, zawsze przedstaw gotowy, ulepszony kod z wyjaśnieniem dlaczego ta zmiana jest korzystna.

---

## 🔍 Kryteria Oceny Fragmentu Kodu

Przeanalizuj badany fragment pod kątem 5 filarów:

### 1. Czystość Kodu i SOLID/CUPID (Poziom Funkcji/Klasy)
- **SRP (Single Responsibility):** Czy funkcja robi dokładnie jedną rzecz na jednym poziomie abstrakcji?
- **Sygnatura i parametry:**
  - Czy lista parametrów nie jest zbyt długa (maks. 3-4 argumenty lub czytelny obiekt konfiguracyjny)?
  - Czy nie występują tzw. *Flag Arguments* (parametry boolean sterujące dwiema zupełnie różnymi ścieżkami wewnątrz funkcji)?
- **CUPID:**
  - *Composable:* Czy funkcję można łatwo łączyć z innymi?
  - *Predictable:* Czy funkcja jest deterministyczna? Czy nie modyfikuje niejawnie obiektów przekazanych przez referencję (brak niepożądanych side-effects)?
  - *Idiomatic:* Czy używa idiomów typowych dla danego języka (np. Pattern Matching w Rust, Destructuring w TypeScript, List Comprehensions/Generators w Pythonie)?

### 2. Obsługa Błędów i Stany Skrajne (Robustness)
- **Przypadki brzegowe (Edge Cases):**
  - Co się stanie przy pustej liście, `0`, ujemnej liczbie, bardzo długim stringu, znakach specjalnych?
  - Jak kod reaguje na brak danych (`None`, `null`, `undefined`)?
- **Zarządzanie wyjątkami:**
  - Czy błędy nie są połykane po cichu (`except: pass`, `catch (e) {}` bez logu i reakcji)?
  - Czy błędy są rzucane z precyzyjnym typem i kontekstem ułatwiającym debugging na produkcji?
  - Czy w operacjach asynchronicznych przewidziano timeouty (`asyncio.wait_for`, `AbortController`)?

### 3. Strategia Testów dla Badanej Funkcjonalności
Oceń istniejące testy dla badanego fragmentu (lub wskaż brakujące):
- **Podejście TDD:** Czy testy powstały przed kodem, czy funkcja jest łatwo testowalna (testable design)?
- **Styl testowania:**
  - **Styl Klasyczny (Detroit / Chicago / Bottom-Up / State-based):**
    - Czy test weryfikuje wejście -> wyjście (stan/rezultat) bez zaglądania do wnętrza funkcji?
    - Jest to preferowany styl dla czystej logiki biznesowej, algorytmów i transformacji danych.
  - **Styl Makietowy (Londyński / London / Interaction-based):**
    - Czy test sprawdza wywołania współpracowników (`verify(mock).send(...)`)?
    - Zwróć uwagę na **over-mocking**: jeśli test wymaga 15 linii konfiguracji mocków, aby przetestować 5 linii logiki, oznacza to zły design badanej funkcji (zbyt duże sprzężenie).
- **Struktura AAA / GWT:**
  - Czy test jest ustrukturyzowany w blokach **Arrange / Act / Assert** (lub **Given / When / Then**)?
  - Czy asercje sprawdzają konkretny rezultat biznesowy, a nie przypadkowe wartości?
- **Pokrycie przypadków brzegowych:**
  - Czy istnieją testy negatywne (błędne dane, rzucenie wyjątku, brak autoryzacji), czy tylko "happy path"?

### 4. Bezpieczeństwo i Wydajność (Poziom Mikro)
- **Zasoby i wycieki pamięci:**
  - Czy otwarte deskryptory plików, strumienie, połączenia socketowe są zamykane w blokach `finally` / `with` / `cleanup`?
  - W React: czy hooki (`useEffect`) czyszczą timery (`clearTimeout`), interwały i event listenery?
  - Czy `useCallback` / `useMemo` mają poprawne tablice zależności (brak pętli i brak stale closures)?
- **Bezpieczeństwo danych:**
  - Czy dane wejściowe od użytkownika są walidowane i sanityzowane (ochrona przed XSS, SQLi, Path Traversal)?
  - Czy w logach nie są wypisywane dane wrażliwe (hasła, tokeny, PII)?

### 5. Filtr Overengineeringu (YAGNI & KISS)
Zadaj pytanie: **"Czy da się to napisać prościej bez utraty elastyczności?"**
- Wytknij tworzenie niepotrzebnych klas bazowych, generyków "na zapas" czy wzorców tam, gdzie zwykła funkcja lub prosty if-else w zupełności wystarczą.

---

## 📋 Wymagany Format Raportu Audytu Funkcjonalności

Wygenerowany raport musi mieć zwięzłą, bezpośrednią formę:

```markdown
# Audyt Kodu — [Nazwa Funkcji / Pliku]

## 1. Werdykt i Podsumowanie
- **Stan:** 🟢 Gotowy do wdrożenia / 🟡 Wymaga drobnych poprawek / 🔴 Wymaga pilnej poprawy
- **Ocena ogólna (1-10):** X/10
- **Główny wniosek:** (1-2 zdania)

---

## 2. Analiza Jakościowa

| Kryterium | Ocena | Uwagi |
|---|:---:|---|
| 🧱 Czystość i SOLID/CUPID | OK / Uwagi | ... |
| 🛡️ Obsługa Błędów & Edge Cases | OK / Uwagi | ... |
| 🧪 Testy (Styl Detroit vs London, AAA) | OK / Uwagi | ... |
| ⚡ Bezpieczeństwo & Zasoby | OK / Uwagi | ... |
| ✂️ Balans (Brak Overengineeringu) | OK / Uwagi | ... |

---

## 3. Zidentyfikowane Kwestie
- 🚨 **Błędy / Ryzyka:** [Opis konkretnego problemu i linia]
- ⚠️ **Słabe punkty (Code Smells / Brak testów):** [Opis]
- 💡 **Uproszczenia (KISS/YAGNI):** [Gdzie kod jest przekombinowany]

---

## 4. Rekomendowane Testy Jednostkowe (AAA / Detroit Style)
```[język]
// Propozycja testów pokrywających przypadki brzegowe
```

---

## 5. Zrefaktoryzowany Kod (Gotowy Drop-In Replacement)
```[język]
// Poprawiona, zoptymalizowana wersja funkcji/pliku
```
```
