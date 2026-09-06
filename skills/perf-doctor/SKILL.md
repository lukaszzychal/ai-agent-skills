---
name: perf-doctor
description: "Technology-agnostic lekarz wydajności i detektor wycieków zasobów. Eliminuje wąskie gardła, wycieki pamięci, problem N+1, blokowanie pętli zdarzeń i zbędne re-rendery UI."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "performance", "memory-leaks", "optimization", "n-plus-1", "event-loop", "profiling", "clean-code"]
---

# Performance Doctor & Resource Leak Analyzer
> **Autor / Twórca:** Łukasz / Lukasz Zychal

Ten skill służy do **diagnozowania, profilowania i rozwiązywania problemów z wydajnością, wyciekami pamięci i zatorami I/O** zarówno w warstwie backendowej (Python, Node.js, Go, Rust, Java, PHP), jak i frontendowej (React, Next.js, Vue, Vanilla JS).

---

## 🎯 Kiedy aktywować ten skill?
- Użytkownik zgłasza: "aplikacja muli / zwalnia z czasem", "rośnie zużycie RAM-u", "wyciek pamięci (memory leak)", "zoptymalizuj to pod kątem wydajności", "problem N+1", "lagi w interfejsie".
- Przegląd kodu pod kątem operacji o wysokiej złożoności obliczeniowej ($O(n^2)$, $O(2^n)$) lub kosztownych operacji wejścia/wyjścia (I/O).
- Weryfikacja cyklu życia zasobów (zarządzanie deskryptorami, połączeniami, timerami i listenerami).

---

## 🔍 Główne Wektory Diagnozy Wydajnościowej

### 1. Wycieki Pamięci i Zasobów (Memory & Resource Leaks)

#### Frontend (JavaScript / TypeScript / React)
- **Wiszące Event Listenery:** Dodanie listenera do `window`, `document` lub elementu DOM bez jego usunięcia w funkcji czyszczącej (`return () => window.removeEventListener(...)`).
- **Nieanulowane Timery / Interwały:** `setInterval` lub `setTimeout` działające po odmontowaniu komponentu, trzymające w domknięciu referencje do stanu.
- **Subskrypcje i Strumienie:** Nieodsubskrybowane instancje `WebSocket`, `EventSource` (SSE), `IntersectionObserver` czy strumienie RxJS.
- **Niekontrolowany Wzrost DOM:** Renderowanie tysięcy elementów listy naraz. **Rozwiązanie:** Wirtualizacja list (Virtual Scrolling, np. `react-virtual` / `@tanstack/react-virtual`).

#### Backend (Python, Node.js, Go, Rust)
- **Globalne Kolekcje Bez Limitu (Unbounded Cache):** Słowniki lub listy na poziomie modułu (`cache = {}`), do których dodawane są elementy bez polityki usuwania (brak LRU, brak TTL).
- **Niezamknięte Deskryptory:** Otwarte pliki, sockety lub sesje HTTP bez context managera (`with open(...)`, `using`, `try-with-resources`).
- **Niekontrolowane Bufory w Pamięci:** Zapisywanie nieskończonych strumieni danych (np. audio, video) do pojedynczego bufora RAM (`BytesIO`) bez podziału na porcje (chunking) lub limitu rozmiaru.

---

### 2. Blokowanie Pętli Zdarzeń (Event Loop Starvation)
Dotyczy asynchronicznych środowisk jednowątkowych (Python asyncio/FastAPI, Node.js):
- ❌ **Antywzorzec:** Wywoływanie operacji synchronicznych I/O (np. `time.sleep()`, synchroniczny `requests.get()`, synchroniczne czytanie dużego pliku dyskowego) wewnątrz funkcji `async def`.
- 💥 **Skutek:** Cały serwer zamarza dla WSZYSTKICH podłączonych użytkowników na czas trwania operacji.
- ✅ **Rozwiązanie:**
  - W Pythonie: Używaj bibliotek asynchronicznych (`asyncio.sleep()`, `httpx.AsyncClient`, `aiofiles`) LUB deleguj synchroniczne zadania do puli wątków: `asyncio.to_thread(func, *args)`.
  - W Node.js: Unikaj synchronicznych metod `fs.*Sync()`, a ciężkie obliczenia CPU (np. hashowanie, kryptografia, parsowanie gigantycznych JSONów) przenoś do `Worker Threads`.

---

### 3. Bazy Danych i Operacje I/O (Problem N+1 & Indeksy)
- **Problem N+1 zapytań:**
  - ❌ Pobranie listy 100 użytkowników (1 zapytanie), a następnie w pętli pobieranie profilu każdego użytkownika (100 zapytań) = 101 zapytań do bazy.
  - ✅ **Rozwiązanie:** Eager loading za pomocą `JOIN` / `select_related` / `prefetch_related` (np. w SQLAlchemy, Prisma, Django, TypeORM) lub `DataLoader` dla GraphQL.
- **Brakujące Indeksy:**
  - Każde zapytanie filtrujące (`WHERE`), łączące (`JOIN`) lub sortujące (`ORDER BY`) na tabelach powyżej kilkunastu tysięcy wierszy musi trafiać w indeks, aby uniknąć pełnego skanu tabeli (Sequential / Full Table Scan).
- **Zasada Paginacji:** Żadne zapytanie zwracające listę nie może działać w trybie `SELECT * FROM table` bez klauzuli `LIMIT` / `OFFSET` lub paginacji kursorowej (Keyset Pagination).

---

### 4. Optymalizacja UI i Renderowania (Frontend Perf)
- **Niepotrzebne Re-rendery:**
  - Rozbijanie wielkich komponentów na mniejsze, atomowe jednostki.
  - Prawidłowe użycie `useMemo` i `useCallback` tam, gdzie przekazywane są referencje do komponentów opakowanych w `React.memo` lub koszt obliczeń jest mierzalny.
- **Leniwa Inicjalizacja Stanu:**
  - ❌ `const [val, setVal] = useState(expensiveComputation());` (obliczenie wykonuje się przy KAŻDYM renderze).
  - ✅ `const [val, setVal] = useState(() => expensiveComputation());` (obliczenie tylko przy montowaniu).
- **Code-Splitting i Dynamic Imports:**
  - Ciężkie moduły (np. edytory WYSIWYG, wykresy, parsery PDF) importuj dynamicznie przez `lazy()` / `next/dynamic` tylko wtedy, gdy użytkownik faktycznie z nich korzysta.

---

## ⚡ Checklista Audytu Wydajnościowego
- [ ] Czy żadna operacja blokująca CPU/dysk nie wykonuje się w głównym wątku / pętli asynchronicznej?
- [ ] Czy wszystkie zasoby (pliki, sockety, transakcje bazodanowe) są zamykane deterministycznie (context manager / try-finally)?
- [ ] Czy w kodzie frontendu każdy `useEffect` posiadający subskrypcję/timer ma funkcję czyszczącą (`cleanup`)?
- [ ] Czy zapytania do bazy danych eliminują problem N+1 i posiadają indeksy dla kluczy obcych i filtrów?
- [ ] Czy rozmiary przesyłanych i trzymanych w pamięci danych są ograniczone stałymi limitami (Safety Buffers)?
