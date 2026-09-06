---
name: project-audit
description: "Kompleksowy, globalny audyt całego projektu pod kątem architektury, SOLID/CUPID/GRASP, strategii testów (TDD, Detroit/London, AAA/GWT), DevOps/CI/CD, bezpieczeństwa i wydajności z zachowaniem inżynierskiego balansu bez overengineeringu."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "architecture", "audit", "solid", "tdd", "ci-cd", "security", "quality"]
---

# Globalny Audytor Projektu (Project Audit Skill)
> **Autor / Twórca:** Łukasz / Lukasz Zychal

Ten skill służy do przeprowadzania **pełnego, całościowego audytu architektonicznego i jakościowego projektu** (poziom MAKRO). Stosuj go, gdy użytkownik prosi o:
- "zrób audyt projektu"
- "sprawdź projekt pod kątem dobrych praktyk"
- "przeanalizuj architekturę i jakość kodu"
- "audyt całościowy / globalny"

---

## 🧭 Filozofia i Złota Zasada Audytu (Pragmatyzm & Balans)

1. **Unikaj overengineeringu i przedwczesnej optymalizacji:** Każda uwaga musi mieć uzasadnienie biznesowe lub operacyjne. Nie zalecaj wprowadzania wzorców projektowych (np. fabryk, strategii, warstw abstrakcji) tam, gdzie prosta funkcja spełnia swoje zadanie (KISS, YAGNI).
2. **Odróżniaj krytyczne ryzyka od preferencji stylistycznych:** Błędy bezpieczeństwa, wycieki zasobów, brak obsługi błędów i brak testów logiki krytycznej to priorytet 🚨 **Krytyczny**. Kosmetyka i drobne refaktoryzacje to priorytet 💡 **Niski / Opcjonalny**.
3. **Konkret zamiast teorii:** Zawsze podawaj ścieżki do plików, numery linii i fragmenty kodu (Przed / Po).

---

## 🔍 Obszary Oceny i Metodologia

Podczas audytu przeanalizuj projekt w następujących 6 wymiarach:

### 1. Architektura i Zgodność z Domeną (Domain Alignment & DDD)
- **Styl architektoniczny:** Czy projekt ma czytelny styl (Modular Monolith, Warstwowa, Hexagonal / Ports & Adapters, Clean Architecture, Event-Driven)? Czy granice modułów są szczelne?
- **Zgodność z domeną (Domain Alignment):** Czy struktura katalogów odzwierciedla logikę biznesową (Package by Feature / Domain), czy tylko warstwy techniczne (Package by Layer: controllers, services, utils)?
- **Ubiquitous Language (Język Wszechobecny):** Czy nazewnictwo klas, metod i typów odpowiada pojęciom domenowym i biznesowym, czy używa technicznego żargonu ("DataProcessor", "Manager", "Helper")?
- **Spójność (Cohesion) i Sprzężenie (Coupling):** Czy moduły mają wysoką spójność wewnętrzną i luźne powiązania zewnętrzne? Czy brak cyklicznych zależności?

### 2. Jakość Kodu i Teoria Obiektowa/Funkcyjna
- **SOLID & GRASP:**
  - *SRP:* Czy klasy/moduły nie są "God Objects" (np. pliki > 800 linii łączące sieć, logikę i UI)?
  - *OCP / LSP / ISP / DIP:* Czy zależności są odwrócone (wstrzykiwanie zależności zamiast hardkodowanych instancji)?
  - *GRASP:* Low Coupling, High Cohesion, Information Expert, Controller.
- **CUPID (dla nowoczesnego kodu):**
  - *Composable* (łatwy do łączenia), *Unix-like* (robi jedną rzecz dobrze), *Predictable* (deterministyczny, bez ukrytych efektów ubocznych), *Idiomatic* (zgodny z duchem danego języka), *Domain-based*.
- **Code Smells & Złożoność:**
  - Primitive Obsession, Feature Envy, Long Parameter Lists, Duplicate Code, Shotgun Surgery.
  - Złożoność cyklomatyczna i poznawcza (Cognitive Complexity) w pętlach i warunkach zagnieżdżonych.

### 3. Strategia Testów i Kultura QA
Zbadaj stan testów według następujących podpunktów:
- **Metodyka TDD:**
  - Czy testy są obecne i czy widać ślady TDD (test-first), czy testy pisane były post-factum jako "obowiązek", czy w ogóle brak testów?
- **Styl i szkoła testowania:**
  - **Styl Klasyczny (Detroit / Chicago / Bottom-Up / Black-Box):**
    - Skupia się na weryfikacji stanu i rezultatów (State Verification) rzeczywistego kodu.
    - Minimalne użycie mocków (tylko na zewnętrznych granicach I/O, sieci, baz danych).
    - Zapewnia wysoką odporność na refaktoryzację wnętrza modułu.
  - **Styl Makietowy (Londyński / London School / Mockist / Top-Down):**
    - Skupia się na weryfikacji interakcji i kontraktów między obiektami (Behavior Verification).
    - Intensywne mockowanie wszystkich współpracowników.
    - Przydatny do projektowania ról od góry do dołu, ale grozi kruchymi testami (*fragile tests*) przy refaktoryzacji.
  - *Ocena:* Czy styl został dobrany odpowiednio? Czy w projekcie nie ma problemu nadmiernego mockowania (over-mocking), gdzie testy badają jedynie to, jak kod został napisany, a nie czy działa?
- **Struktura testów (AAA / GWT):**
  - Czy testy stosują czytelny podział **Arrange-Act-Assert** lub **Given-When-Then**?
  - Czy intencja testu jest jednoznaczna, a asercje precyzyjne (jeden logiczny koncept na test)?
- **Jakość asercji i przypadki brzegowe:**
  - Czy testy badają wartości skrajne (edge-cases, null, błędy sieci, rate-limits), czy są jedynie "pustymi sprawdzaczami" (*happy path*) podbijającymi wskaźnik pokrycia?
- **Piramida testów i Code Coverage:**
  - Balans między testami jednostkowymi, integracyjnymi i E2E.
  - Rzeczywisty procent pokrycia krytycznej logiki biznesowej.

### 4. Konfiguracja Środowiskowa i Bezpieczeństwo
- **Zarządzanie sekretami i `.env`:**
  - Czy w kodzie nie ma zahardkodowanych kluczy API, tokenów ani haseł?
  - Czy plik `.env` jest w `.gitignore` i czy **nie wyciekł do historii Gita** (`git log --all -- ...`)?
  - Czy istnieje kompletny `.env.example` z dokumentacją zmiennych?
- **Bezpieczeństwo aplikacji (OWASP Top 10):**
  - CORS (czy nie ma `allow_origins=["*"]` z credentials)?
  - Walidacja wejścia (sanitizacja, ochrona przed Injection, ograniczenia rozmiaru payloadu i rate-limiting).
  - Autoryzacja i ochrona socketów/endpointów (tokeny, CSRF, bezpieczne nagłówki).
  - Szyfrowanie wrażliwych danych w pamięci lokalnej (localStorage / cookies).

### 5. Infrastruktura i Procesy (DevOps & CI/CD)
- **Automatyzacja potoków (Pipelines):**
  - Czy istnieją zautomatyzowane pipeline'y (np. GitHub Actions, GitLab CI) uruchamiające lintery i testy przy każdym PR/pushu?
- **Narzędzia jakości i lintery:**
  - Czy skonfigurowano lintery i formatowanie (Ruff, ESLint, Prettier, Clippy, Black)?
  - Czy istnieją skanery statyczne (SonarQube, mypy, tsc w trybie strict)?
- **Zarządzanie zależnościami i podatności (Vulnerability Checking):**
  - Czy zależności są przypięte (lockfiles: `package-lock.json`, `poetry.lock`, przypięte wersje w `requirements.txt`)?
  - Czy zależności zawierają znane podatności CVE (`npm audit`, `pip-audit`, Dependabot)?

### 6. Zarządzanie Stanem, Błędami i Observability
- **Error Handling:**
  - Czy błędy są obsługiwane hierarchicznie i typowane, czy połykane po cichu (`except: pass`, `catch (e) {}`)?
  - Czy system potrafi działać w trybie awaryjnym (Graceful Degradation, Fallback chain, Circuit Breaker)?
- **Logowanie i Observability:**
  - Czy stosowane jest ustrukturyzowane logowanie (`logging` z poziomami INFO/WARNING/ERROR) zamiast `print()` / `console.log()`?
  - Czy logi zawierają kontekst pozwalający na łatwe zdiagnozowanie incydentu na produkcji bez ujawniania danych wrażliwych (PII, klucze API)?

---

## 📋 Wymagany Format Raportu Audytu

Wygenerowany raport musi mieć poniższą strukturę:

```markdown
# Raport z Audytu Architektury i Jakości Projektu — [Nazwa Projektu]

## 1. Podsumowanie i Karta Ocen

| Kategoria | Ocena (1-10) | Podsumowanie stanu |
|---|:---:|---|
| 🏛️ Architektura & DDD | X/10 | ... |
| 🧱 Czystość kodu & SOLID/CUPID | X/10 | ... |
| 🧪 Strategia Testów & QA | X/10 | ... |
| 🔐 Bezpieczeństwo & Sekrety | X/10 | ... |
| 🚀 DevOps & CI/CD | X/10 | ... |
| ⚡ Wydajność & Observability | X/10 | ... |
| **OGÓLNA OCENA** | **X/10** | ... |

---

## 2. Strategia Testów — Szczegółowa Diagnoza
- **Metodyka:** [TDD / Testy post-factum / Brak testów]
- **Styl testowania:** [Styl Klasyczny (Detroit/Chicago) / Styl Makietowy (Londyński/Mockist) / Mieszany]
- **Ocena adekwatności stylu:** [Czy występuje over-mocking? Czy testy są odporne na refaktoring?]
- **Struktura (AAA / GWT):** [Tak/Nie/Częściowo + przykłady]
- **Jakość asercji i przypadki brzegowe:** [Analiza czy testy sprawdzają logikę, czy są "wydmuszkami"]

---

## 3. Zidentyfikowane Problemy (z podziałem na priorytety)

### 🚨 Priorytet 1: Krytyczne (Bezpieczeństwo, Wycieki, Crashe)
- **[PLIK:LINIA]** Opis problemu -> Skutek -> Rekomendacja naprawy

### ⚠️ Priorytet 2: Wysoki (Architektura, Poważne Code Smells, Odporność na awarie)
- **[PLIK:LINIA]** Opis problemu -> Rekomendacja refaktoryzacji

### 🟡 Priorytet 3: Średni (Maintainability, Lintery, Spójność)
- **[PLIK:LINIA]** Opis problemu -> Rekomendacja

### 💡 Priorytet 4: Sugestie z filtrem YAGNI (Opcjonalne — unikaj overengineeringu)
- *Zalecenie:* Rzeczy, które można usprawnić w przyszłości, ale **ich brak nie szkodzi obecnemu etapowi projektu**. Wyraźna adnotacja: *"Nie wdrażaj teraz, jeśli nie ma takiej potrzeby biznesowej"*.

---

## 4. Konkretny Plan Naprawczy (Action Plan)
Uporządkowana chronologicznie lista kroków do wdrożenia poprawek.
```
