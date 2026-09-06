---
name: api-contract-sync
description: "Technology-agnostic projektant i synchronizator kontraktów API (REST, WebSocket, OpenAPI, gRPC). Gwarantuje spójność typów, wsteczną kompatybilność i brak breaking changes."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "api", "openapi", "websocket", "contract-testing", "typescript", "pydantic", "architecture"]
---

# API Contract & Schema Synchronizer
> **Autor / Twórca:** Łukasz / Lukasz Zychal

Ten skill służy do **projektowania, weryfikacji i synchronizacji kontraktów komunikacyjnych** pomiędzy backendem, frontendem i serwisami zewnętrznymi. Zapewnia Single Source of Truth dla typów danych, eliminuje ciche błędy niezgodności modeli oraz chroni przed zrywaniem kompatybilności wstecznej (Breaking Changes).

---

## 🎯 Kiedy aktywować ten skill?
- Użytkownik prosi o: "zaprojektuj kontrakt API", "zsynchronizuj typy backendu i frontendu", "jak zaprojektować komunikację WebSocket", "czy ta zmiana w API zepsuje frontend".
- Wprowadzanie zmian w DTO / schematach odpowiedzi lub żądań HTTP / WebSocket.
- Generowanie typów klienckich z OpenAPI / Swagger / Protobuf.

---

## 💎 Złote Zasady Projektowania Kontraktów API

### 1. Single Source of Truth (Jedno Źródło Prawdy)
- Modele danych nie powinny być pisane ręcznie w dwóch niezależnych miejscach (np. oddzielnie w Pythonie i oddzielnie w TypeScript), co prowadzi do dryfu schematów (Schema Drift).
- **Zalecany wzorzec:** Backend definiuje schemat (np. Pydantic w FastAPI, DTO w NestJS/C#/Go), eksportuje specyfikację OpenAPI (JSON/YAML), a typy frontendowe są generowane automatycznie (np. `openapi-typescript`, `orval`) LUB oba systemy współdzielą schemat JSON Schema / Protobuf.

### 2. Zasada Postela (Robustness Principle)
> *"Bądź konserwatywny w tym, co wysyłasz, i liberalny w tym, co przyjmujesz."*
- Klient powinien ignorować nieznane, dodatkowe pola zwracane przez serwer (umożliwia to bezpieczne dodawanie nowych właściwości bez psucia starych klientów).
- Serwer powinien jasno walidować wymagane pola i nadawać sensowne wartości domyślne dla pól opcjonalnych.

### 3. Ochrona Przed Breaking Changes (Wsteczna Kompatybilność)
Nigdy nie łam działających klientów:
- ❌ **Zabronione:** Zmiana typu istniejącego pola (np. z `int` na `string`).
- ❌ **Zabronione:** Zmiana nazwy pola (np. `user_name` -> `username`) bez okresu przejściowego.
- ❌ **Zabronione:** Dodanie nowego wymaganego pola w żądaniu bez wartości domyślnej.
- ❌ **Zabronione:** Usunięcie istniejącego endpointu lub pola z odpowiedzi.
- ✅ **Dozwolone:** Dodanie nowego opcjonalnego pola z domyślną wartością.
- ✅ **Dozwolone:** Wprowadzenie nowej wersji endpointu (np. `/api/v2/...`), zachowując `/api/v1/...` jako zdeprecjonowane przez ustalony okres (Deprecation Window).

---

## 🔄 Wzorzec Kontraktu dla WebSocket i Strumieniowania

Protokół WebSocket wymaga ścisłego modelowania zdarzeń z dyskryminatorem typu (Tagged Union / Discriminated Union):

### Standardowy format ramki (JSON):
```json
{
  "type": "TRANSLATION_CHUNK",
  "payload": {
    "sessionId": "sess-456",
    "sequenceNumber": 12,
    "text": "Hello world",
    "isFinal": false
  },
  "metadata": {
    "timestamp": 1725633600000,
    "version": "1.0"
  }
}
```

### Reguły dla połączeń asynchronicznych:
1. **Dyskryminator (`type`):** Każda wiadomość serwer <-> klient musi posiadać unikalne pole identyfikujące typ komunikatu.
2. **Sekwencyjność (`sequenceNumber`):** Jeśli kolejność ma znaczenie (np. audio, transkrypcja), ramki muszą zawierać numer sekwencyjny.
3. **Heartbeat / Ping-Pong:** Niezależny mechanizm kontroli życia połączenia co N sekund dla wykrywania cichych rozłączeń (Dead Connections).
4. **Błąd jako zdarzenie (`ERROR`):** Zamiast nagłego zerwania socketu, serwer powinien wysłać sformatowaną ramkę błędu z kodem i komunikatem czytelnym dla klienta.

---

## 📐 Mapowanie Typów: Backend -> Frontend

| Typ Danych | Backend (np. Python / Pydantic) | Frontend (TypeScript) | Uwagi |
| :--- | :--- | :--- | :--- |
| Identyfikator | `str` (UUID) | `string` | Unikaj generowania ID jako surowych liczb |
| Data i czas | `datetime` (ISO 8601) | `string` (ISO 8601) | Zawsze przesyłaj czas w UTC ze strefą `Z` |
| Kwoty pieniężne | `Decimal` lub `int` (w groszach/centach) | `number` (centy) lub `string` | **NIGDY** nie przesyłaj kwot jako `float` |
| Wartości wyliczeniowe | `Enum` (np. `class Status(str, Enum)`) | `type Status = "ACTIVE" \| "PAUSED"` | Używaj String Enums, nie intów |
| Opcjonalność | `Optional[T]` lub `T \| None = None` | `T \| null` lub `field?: T` | Jawnie odróżniaj brak pola od wartości `null` |

---

## 📋 Checklista Przeglądu Zmian w Kontrakcie API
- [ ] Czy nowe pola w DTO żądania mają zdefiniowane wartości domyślne?
- [ ] Czy usunięcie lub zmiana pól została poprzedzona wersjonowaniem API (`v1` -> `v2`)?
- [ ] Czy kody odpowiedzi HTTP odpowiadają standardom (200 OK, 201 Created, 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict, 422 Unprocessable Entity, 500 Internal Error)?
- [ ] Czy błędy API zwracają ustandaryzowany schemat (np. RFC 7807 Problem Details: `type`, `title`, `status`, `detail`)?
- [ ] Czy typy po stronie klienta (frontend/mobile) zostały zaktualizowane i skompilowane bez błędów?
