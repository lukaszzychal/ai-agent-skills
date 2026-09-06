---
name: security-guard
description: "Technology-agnostic audytor bezpieczeństwa i strażnik przed wyciekiem danych. Wykrywa sekrety, luki OWASP Top 10, podatności zależności (CVE) oraz błędy konfiguracji."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "security", "secrets", "owasp", "cve", "sast", "pre-commit", "audit"]
---

# Security Guard & Vulnerability Auditor
> **Autor / Twórca:** Łukasz / Lukasz Zychal

Ten skill służy do **audytu bezpieczeństwa aplikacji, detekcji wycieków sekretów oraz eliminacji podatności** na poziomie kodu źródłowego, konfiguracji i zależności zewnętrznych, niezależnie od stosu technologicznego (Python, Node/TypeScript, Rust, Go, PHP, Java itp.).

---

## 🎯 Kiedy aktywować ten skill?
- Użytkownik prosi o: "sprawdź bezpieczeństwo", "audyt security", "czy ten kod jest bezpieczny", "sprawdź czy nie ma wycieku kluczy / tokenów".
- Przed zatwierdzeniem commitów i wypchnięciem kodu do zdalnego repozytorium (Pre-commit / Pre-push check).
- Podczas przeglądu nowych endpointów API, obsługi autoryzacji, uploadu plików lub integracji zewnętrznych.

---

## 🛡️ Kluczowe Filary Audytu Bezpieczeństwa

### 1. Detekcja Sekretów i Danych Wrażliwych (Secrets Leak Prevention)
Zawsze przeszukaj kod pod kątem twardo zakodowanych poświadczeń (Hardcoded Credentials):
- **Wzorce do wykrycia:**
  - Klucze API (np. OpenAI `sk-...`, Anthropic, AWS `AKIA...`, Google API keys, Stripe `sk_live_...`).
  - Hasła baz danych, connection strings zawierające hasła (`postgres://user:password@host...`).
  - Prywatne klucze kryptograficzne (`-----BEGIN PRIVATE KEY-----`, RSA, ED25519).
  - Sekrety tokenów JWT (`JWT_SECRET = "supersecret123"`).
- **Zasada higieny:** Wszystkie sekrety MUSZĄ pochodzić wyłącznie ze zmiennych środowiskowych (`.env` w `.gitignore` lub menedżera sekretów np. HashiCorp Vault, AWS Secrets Manager).
- **Weryfikacja historii Gita:** Upewnij się, że `.env` nie został dodany do indeksu Gita (`git ls-files --stage | grep .env`).

---

### 2. OWASP Top 10 – Weryfikacja Logiki Kodu

#### A. Wstrzykiwanie Kodu i Poleceń (Injection Flaws)
- **SQL / NoSQL Injection:** Bezwzględny zakaz konkatenacji stringów w zapytaniach. Używaj wyłącznie zapytań sparametryzowanych (Prepared Statements) lub ORM/ODM.
- **Command Injection:** Unikaj `os.system()`, `eval()`, `exec()`, `shell=True` w `subprocess` (Python) lub `child_process.exec()` (Node.js). Zawsze używaj tablic argumentów bez powłoki shella.
- **Path Traversal / Arbitrary File Access:** Zabezpieczaj ścieżki do plików przed sekwencją `../`. Używaj funkcji walidujących bezpieczną bazową ścieżkę (np. `pathlib.Path.resolve()` i sprawdzanie `path.is_relative_to(base)`).

#### B. Bezpieczeństwo API i Sieci
- **CORS (Cross-Origin Resource Sharing):** Nigdy nie ustawiaj `AllowOrigins: ["*"]` w połączeniu z `allow_credentials=True`. Wskazuj jawnie dozwolone domeny z env.
- **SSRF (Server-Side Request Forgery):** Waliduj i blokuj adresy IP prywatne (loopback `127.0.0.1`, RFC1918 `10.0.0.0/8`, `192.168.0.0/16`, link-local `169.254.169.254` dla chmur), jeśli aplikacja wykonuje zapytania pod adresy podane przez użytkownika.
- **Rate Limiting & DoS:** Czy endpointy publiczne i WebSocket posiadają ochronę przed zalaniem zapytaniami (rate limiting, max message size, timeouty)?

#### C. Uwierzytelnianie i Autoryzacja
- **Broken Object-Level Authorization (BOLA / IDOR):** Czy użytkownik A może odczytać/zmodyfikować zasób użytkownika B przekazując jego `id` w URL? Zawsze weryfikuj własność zasobu po stronie backendu na podstawie tokenu sesyjnego.
- **Rotacja i Bezpieczeństwo Tokenów:** Tokeny sesyjne muszą mieć skończony czas życia (TTL) i bezpieczne flagi w ciasteczkach (`HttpOnly`, `Secure`, `SameSite=Lax/Strict`).

---

### 3. Bezpieczeństwo Zależności (SCA - Software Composition Analysis)
Rekomendowane narzędzia do skanowania zależności w różnych technologiach:

| Ekosystem | Komenda skanowania podatności (CVE) | Dobre praktyki |
| :--- | :--- | :--- |
| **Python** | `pip-audit` lub `safety check` | Zablokowane wersje w `poetry.lock` / `requirements.txt` |
| **Node.js / TS** | `npm audit` lub `pnpm audit` | Sprawdzanie przed każdym commitem; `audit-level=moderate` |
| **Rust** | `cargo audit` | Zależności przypięte w `Cargo.lock` |
| **PHP** | `composer audit` | Weryfikacja pakietów z Packagist |
| **Go** | `govulncheck ./...` | Oficjalny skaner podatności Go |

---

## 🛑 Szybka Checklista Bezpieczeństwa przed Commitem (Pre-Commit Guard)

Przed wypchnięciem zmian odpowiedz na pytania:
1. **[ ] Sekrety:** Czy żaden plik w `git status` / `git diff` nie zawiera prawdziwych haseł, tokenów ani kluczy API?
2. **[ ] Walidacja wejścia:** Czy dane od użytkownika są walidowane (np. Zod, Pydantic, Bean Validation) przed użyciem?
3. **[ ] Błędy i Stack Traces:** Czy aplikacja produkcyjna nie zwraca użytkownikowi pełnych stack trace'ów zawierających strukturę bazy danych lub ścieżki serwera?
4. **[ ] Bezpieczeństwo Pamięci / Buforów:** Czy w przypadku strumieniowania (np. pliki audio, video, websockets) zdefiniowano limit maksymalnego rozmiaru bufora w pamięci RAM?
5. **[ ] Prawa Dostępu:** Czy operacje modyfikacji wymagają uprawnień administratora / właściciela zasobu?
