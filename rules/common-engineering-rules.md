# Common Engineering Rules for AI Coding Agents
> **Author / Creator:** Łukasz / Lukasz Zychal

Zbiór fundamentalnych reguł inżynierskich, którymi powinien kierować się asystent AI (Antigravity, Gemini, Claude, Cursor, Copilot) w każdym projekcie programistycznym.

---

## 1. Zasada Pragmatyzmu i Anty-Overengineeringu (KISS / YAGNI)
- **Nie twórz zbędnych abstrakcji:** Złożone wzorce projektowe (Abstract Factories, Chain of Responsibility, wielopoziomowe dekoratory) wprowadzaj wyłącznie wtedy, gdy rozwiązują rzeczywisty, istniejący problem architektoniczny.
- **Prostota ponad formę:** Czytelna, 20-liniowa funkcja jest wielokrotnie lepsza od 5 nowych klas i interfejsów, które realizują ten sam cel.
- **Brak przedwczesnej optymalizacji:** Optymalizuj kod na podstawie profilowania i pomiarów, a nie domysłów.

---

## 2. Higiena Kodu i Zarządzanie Zmianami (Diff Integrity)
- **Nie usuwaj istniejącej logiki:** Przy refaktoryzacji zawsze zachowuj istniejące komentarze biznesowe, docstringi i obsługę sytuacji brzegowych, chyba że użytkownik wyraźnie zażądał ich usunięcia.
- **Minimalny promień rażenia (Blast Radius):** Zmiany powinny dotykać tylko plików niezbędnych do zrealizowania zadania.
- **Idempotentność:** Skrypty konfiguracyjne i instalacyjne powinny być bezpieczne do wielokrotnego uruchomienia.

---

## 3. Bezpieczeństwo i Higiena Sekretów
- **Nigdy nie hardkoduj poświadczeń:** Klucze API, tokeny JWT, hasła i prywatne klucze muszą pochodzić wyłącznie ze zmiennych środowiskowych (`.env`, secret manager).
- **Zabezpieczenie przed commitem:** Przed każdym commitem agent ma obowiązek sprawdzić `git status` i `git diff`, by upewnić się, że żaden plik z sekretami nie trafił do repozytorium.

---

## 4. Konwencja Commitów i Wersjonowanie
- Wiadomości commitów zawsze pisz w języku angielskim według specyfikacji **Conventional Commits** (`feat:`, `fix:`, `refactor:`, `chore:`, `test:`, `docs:`).
- Wydania taguj zgodnie z **SemVer** (`vX.X.X-alpha.X`, `vX.X.X-beta.X`, `vX.X.X`).
