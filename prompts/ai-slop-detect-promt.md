# 🔍 Audyt strony i aplikacji – Narzędzia i Prompty

## Narzędzia online

| Narzędzie | Cel | Link |
|---|---|---|
| **PageSpeed Insights** | Core Web Vitals, SEO techniczne, dostępność | https://pagespeed.web.dev/ |
| **Google Rich Results Test** | Czy Google rozumie strukturę treści (schema.org) | https://search.google.com/test/rich-results |
| **Schema Markup Validator** | Oficjalny walidator składni JSON-LD / Microdata (Schema.org) | https://validator.schema.org/ |
| **Google Search Console** | Indeksowanie, błędy crawlowania, hreflang | https://search.google.com/search-console |
| **Bing Webmaster Tools** | Indeksowanie w Bing, Copilot AI, protokół IndexNow | https://www.bing.com/webmasters |
| **Ahrefs Free SEO Checker** | Szybki audit SEO | https://ahrefs.com/seo-checker |
| **Screaming Frog (free)** | Crawl strony: broken links, meta, redirects | https://www.screamingfrog.co.uk/seo-spider/ |
| **llms.txt Validator** | Weryfikacja i standard pliku /llms.txt dla modeli AI i wyszukiwarek | https://llmstxt.org/ |
| **WAVE Web Accessibility** | Dostępność (a11y), kontrast, ARIA | https://wave.webaim.org/ |
| **axe DevTools** | Audyt WCAG 2.0–2.2 poziomy A/AA/AAA (rozszerzenie Chrome/Firefox, branżowy standard a11y) | https://www.deque.com/axe/devtools/ |
| **Accessibility Checker** | Szybki online audyt WCAG AA (powered by Deque axe-core) | https://www.accessibilitychecker.org/ |
| **ANDI (SSA.gov)** | Screen reader / nawigacja klawiaturą / Section 508 / WCAG tester (US Gov) | https://www.ssa.gov/accessibility/andi/help/install.html |
| **Lighthouse (Chrome DevTools)** | Performance, SEO, a11y, PWA (F12 → Lighthouse) | wbudowany w Chrome |
| **Open Graph Debugger** | Podgląd OG tags (jak wygląda na FB/LinkedIn) | https://www.opengraph.xyz/ |
| **Twitter Card Validator** | Sprawdzenie kart Twitter/X | https://cards-dev.twitter.com/validator |
| **robots.txt Tester** | Weryfikacja robots.txt | https://technicalseo.com/tools/robots-txt/ |
| **XML Sitemap Validator** | Walidacja sitemap.xml | https://www.xml-sitemaps.com/validate-xml-sitemap.html |
| **lightsite.ai** | AI slop detection, ogólna ocena strony | https://www.lightsite.ai/ |
| **urlvoid.com** | Czy domena nie jest na blacklistach | https://www.urlvoid.com/ |

---

## 💻 Jak audytować stronę na LOCALHOST przed wdrożeniem?

Zewnętrzne serwisy AI (ChatGPT, Perplexity, Claude w przeglądarce) nie mają bezpośredniego dostępu do adresu `http://localhost:3000`. Możesz to rozwiązać na **3 sposoby**:

### Sposób 1: Przez lokalnego Agenta w IDE (Antigravity / Cursor) – NAJSZYBSZY
Agent lokalny ma bezpośredni dostęp do Twojej maszyny i wbudowanego Playwrighta/Chrome DevTools.
* Wystarczy podać mu prompt z adresem `http://localhost:3000` lub `http://localhost:3001`.
* Agent potrafi samodzielnie emulować urządzenia mobilne, sprawdzać konsolę błędów, klikać elementy i robić zrzuty ekranu.

### Sposób 2: Darmowy tymczasowy tunel (Cloudflared / Localtunnel)
Udostępnia Twojego localhosta pod publicznym adresem HTTPS, który możesz wkleić do ChatGPT, PageSpeed Insights czy validatorów:
```bash
# Opcja A (Localtunnel - bez instalacji):
npx localtunnel --port 3000

# Opcja B (Cloudflare Tunnel - bardzo szybki i stabilny):
brew install cloudflare/cloudflare/cloudflared
cloudflared tunnel --url http://localhost:3000
```
Otrzymany link (np. `https://funny-cat-42.loca.lt`) wklejasz do promptu jako `[URL]`.

### Sposób 3: Zrzuty ekranu (Screenshots)
Robisz zrzut ekranu (Desktop, Tablet, Mobile) i załączasz pliki graficzne bezpośrednio do okna czatu z promptem (patrz: **Prompt 5**).

---

## 📁 Standard zapisu plików z audytem

Każdy wygenerowany audyt powinien zostać zapisany jako dokument Markdown w katalogu `audyt/` według schematu:
```
audyt/audyt-[nazwa-projektu]-[typ-audytu]-[RRRR-MM-DD]-[GGMM].md
```
**Przykłady:**
* `audyt/audyt-lukaszzychal.dev-multiperspektywowy-2026-09-16-1550.md`
* `audyt/audyt-lukaszzychal.dev-ai-slop-2026-09-16-1555.md`
* `audyt/audyt-extension-chrome-ui-2026-09-16-1600.md`

---

## 🤖 Prompty do audytu (AI: ChatGPT / Claude / Perplexity / Antigravity)

> **Instrukcja ogólna:** 
> - Podmień `[URL]` na adres strony (lub publiczny link tunelu localhost).
> - Jeśli audytujesz wersję lokalną lub zrzuty, załącz odpowiednie pliki / kontekst.
> - Wymuś na modelu sprawdzenie 3 widoków: **Desktop (1440px+), Tablet (768px–1024px) oraz Mobile (375px–414px)**.
> - **Zasada Actionable Backlog:** Model musi omówić w tekście **wszystkie wykryte problemy**, a na końcu raportu zestawić je w **zbiorczy backlog posortowany malejąco wg priorytetów (P1: Blokery/Krytyczne, P2: Istotne UX/SEO, P3: Szlif/Nice-to-have)** z unikalnymi identyfikatorami `[FIX-01]`, `[FIX-02]`, co umożliwia ich błyskawiczne, selektywne wdrożenie.
> - **🛠️ Hierarchia Narzędzi Inspekcji (Graceful Fallback):**
>   1. **Poziom 1 (Złoty Standard – Pełna emulacja):** Jeśli masz dostęp do Playwright, Puppeteer, Chrome DevTools MCP lub Browser Subagenta – uruchom przeglądarkę, zbadaj 3 viewporty (375x667, 768x1024, 1440x900), sprawdź błędy w konsoli JS i przetestuj interakcje (np. menu mobilne, tap targets min. 44x44px).
>   2. **Poziom 2 (Scraper treści – Firecrawl / Jina Reader / Web Fetch):** Jeśli brak silnika przeglądarki, ale masz dostęp do sieci/narzędzi scrapingowych – pobierz stronę (np. przez Firecrawl, r.jina.ai/[URL] lub curl) i zbadaj tekst, semantykę HTML, hierarchię nagłówków H1–H3 oraz metatagi SEO.
>   3. **Poziom 3 (Statyczny fallback – Kod / Zrzuty):** Jeśli strona lub localhost są niedostępne sieciowo – poproś o kod źródłowy komponentów lub zrzuty ekranu (Prompt 5) i przeprowadź statyczny audyt kodu oraz architektury layoutu.

---

### Prompt 1 – Audyt wieloperspektywowy (rekruter, dev, designer + Mobile/Tablet)

```
Wejdź na stronę: [URL] (użyj najwyższego dostępnego narzędzia: Playwright / Chrome DevTools / Firecrawl / fetch wg hierarchii narzędzi)

[OPCJONALNIE – KONTEKST I INTENCJA AUTORA]:
(Jeśli pominiesz ten blok, samodzielnie wywnioskuj cel na podstawie pierwszego wrażenia)
– Cel strony / Główna akcja (CTA): [np. Portfolio inżynierskie B2B / Prezentacja projektu open-source / SaaS / Edukacja]
– Grupa docelowa: [np. Rekruterzy techniczni, CTO, klienci biznesowi]
– Zamierzony styl / Archetyp marki: [np. Industrial Dark Terminal / Nowoczesny minimalizm / Corporate Clean]
– Świadome decyzje (czego NIE krytykować): [np. Celowo brak cennika; techniczny font mono i brak stockowych zdjęć są zamierzone]

Przeprowadź kompleksowy audyt z 5 różnych perspektyw. 
Dla KAŻDEJ perspektywy uwzględnij analizę 3 widoków ekranu:
- DESKTOP (1440px+)
- TABLET (768px – 1024px, np. iPad w pionie i poziomie)
- MOBILE (375px – 414px, smartfon)

Oceń każdą perspektywę w skali 1-10 i podaj konkretne uwagi:

1. 🧑‍💼 REKRUTER IT / TECH HIRING MANAGER (lub Klient B2B)
   – Czy profil, poziom seniorski i specjalizacja technologiczna są czytelne w 30 sekund na telefonie i desktopie?
   – Czy widać konkretne efekty biznesowe, architekturę i skalę projektów, a nie tylko suchą listę technologii?
   – Czy copy nie zawiera AI slopu i pustego korpo-coachingu („dostarczam synergię 360”, zmyślone metryki), z poszanowaniem autorskiego charakteru i manifestu twórcy?
   – Co by Cię zatrzymało, a co by odrzuciło przed zaproszeniem na rozmowę techniczną?

2. 🎨 UI/UX DESIGNER (Desktop + Tablet + Mobile)
   – Spójność wizualna, hierarchia treści, typografia, kontrast.
   – Responsywność: czy elementy nie wyjeżdżają poza ekran (poziomy scroll)?
   – Wersja Tablet: jak zachowują się gridy (2 kolumny) i marginesy boczne?
   – Wersja Mobile: czytelność menu hamburger, rozmiar przycisków dotykowych (tap targets min. 44x44px), spacing.
   – Czy design jest unikalny czy wygląda jak AI-default (v0/shadcn template)?

3. 👨‍💻 FRONTEND DEVELOPER
   – Struktura semantyczna HTML (H1-H3, article, section, nav, main, header, footer).
   – Dostępność (a11y) – weryfikuj wg WCAG 2.1 AA (minimalny standard wymagany prawnie w UE/PL):
     • Kontrast tekstu normalnego ≥ 4.5:1, tekstu dużego (≥18pt) ≥ 3:1 [WCAG 1.4.3 AA]
     • Kontrast elementów UI (przyciski, inputy, ikony aktywne) ≥ 3:1 [WCAG 1.4.11 AA – nowe w 2.1]
     • Tekst alternatywny: znaczące obrazy `alt="opis"`, dekoracyjne `alt=""` [WCAG 1.1.1 A]
     • ARIA labels, role i `aria-describedby` na formularzach i ikonach [WCAG 1.3.1 A]
     • Focus-visible: wyraźny outline przy nawigacji klawiaturą Tab [WCAG 2.4.7 AA / 2.4.11 AA w 2.2]
     • Brak pułapek klawiatury (focus trap dozwolony tylko w modalach z Escape) [WCAG 2.1.2 A]
     • Obsługa `prefers-reduced-motion` dla animacji i przejść [WCAG 2.3.3 / dobra praktyka]
     • Skalowanie tekstu do 200% bez utraty treści i poziomego scrolla [WCAG 1.4.4 AA]
     • Reflow przy 320px szerokości (brak poziomego scrolla) [WCAG 1.4.10 AA – nowe w 2.1]
   – Etykiety formularzy: każdy input musi mieć powiązany `<label>` lub `aria-label` [WCAG 3.3.2 AA].
   – Zachowanie layoutu przy zwężaniu okna, brak przesunięć layoutu (CLS).
   – Performance mobilny: optymalizacja zasobów, LCP.

4. 🌐 WEB DEVELOPER / FULLSTACK
   – Działanie nawigacji i płynnego przewijania (anchors) na mobile i desktopie.
   – Działanie formularza kontaktowego na telefonie (klawiatura wirtualna, walidacja).
   – Czy wszystkie linki zewnętrzne otwierają się poprawnie?
   – Obsługa stanów błędów i brakujące funkcje UX.

5. 🔍 SEO SPECIALIST
   – Mobile-First Indexing: czy wersja mobilna zawiera te same kluczowe treści co desktop?
   – Meta tagi, Open Graph, canonical, robots.txt, sitemap.xml.
   – Czytelność dla botów i crawlerów AI (Google AI Overviews, Perplexity).

Wymóg formatowania wyniku:
1. Szczegółowo omów WSZYSTKIE wykryte problemy w ramach każdej z 5 perspektyw (nie pomijaj drobnych błędów).
2. Przygotuj podsumowanie z ogólną oceną (1-10) oraz:
   ### 🎯 ZBIORCZY BACKLOG POPRAWEK (WSZYSTKIE WYKRYTE PROBLEMY):
   Przedstaw WSZYSTKIE wykryte problemy posortowane malejąco według priorytetu z unikalnymi ID:
   - 🔴 [PRIORYTET P1 - KRYTYCZNE / BLOKERY] (np. błędy mobilne, poziomy scroll, broken links, tap targets):
     * [FIX-01] [Perspektywa / Widok] Konkretny problem i rekomendowane rozwiązanie
   - 🟡 [PRIORYTET P2 - ISTOTNE / UX & SEO] (np. hierarchia H1-H3, brak zamykania menu mobilnego, puste frazy):
     * [FIX-02] ...
   - 🟢 [PRIORYTET P3 - SZLIF / NICE-TO-HAVE] (np. mikro-animacje, kosmetyka spacingu):
     * [FIX-03] ...
3. Raport przygotuj do zapisu w pliku:
   `audyt/audyt-[nazwa-projektu]-multiperspektywowy-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 2 – Zaawansowany Detektor "AI slop", Autentyczności i Human-First Copy (Desktop & Mobile)

> **Zgodność ze standardami:** Standard *WikiProject AI Cleanup* (skill `/humanizer`), Zasady Human-First Copywriting, Wytyczne Google Search Essentials (wartość merytoryczna vs wypełniacze generatywne).

```
Wejdź na stronę: [URL] (priorytet inspekcji: Playwright / Chrome DevTools / Firecrawl / fetch wg hierarchii narzędzi)

[OPCJONALNIE – KONTEKST I TONE OF VOICE]:
– Kim jest autor / Czym jest projekt: [np. Senior Backend Developer / Narzędzie CLI / SaaS B2B / Portal narzędziowy]
– Oczekiwany styl komunikacji: [np. Rygor inżynierski, zwięzłość, konkretne metryki, zero korporacyjnego żargonu]
– Świadome decyzje (czego NIE krytykować): [np. Autorskie motto „Ja nie tworzę aplikacji, ja rozwiązuję problemy” jest zamierzone i ma pozostać; minimalistyczny terminal look jest świadomym wyborem]

Przeprowadź rygorystyczny audyt anty-slopowy, weryfikując czy strona wygląda i brzmi jak generyczny wytwór sztucznej inteligencji pozbawiony autorskiej edycji (tzw. "AI slop").

Zbadaj stronę w 3 wymiarach (Mobile 375px, Tablet 768px, Desktop 1440px):

===================================================================
1. WARSTWA TEKSTOWA I TONE OF VOICE (Standard WikiProject AI Cleanup & /humanizer):
===================================================================
A) ❌ NEGATIVE PARALLELISMS & CLICHÉ CONSTRASTS (§9 humanizer):
   – Czy występują szablonowe konstrukcje typu: „To nie tylko X, to Y”, „Nie chodzi o X, lecz o Y”?
   – Czy w profilu/hero widnieją oklepane formułki generowane maszynowo: „Nie piszę po prostu kodu – dostarczam synergiczną wartość biznesową w skali 360”? (Uwaga: respektuj świadome manifesty i hasła osobiste zadeklarowane przez autora w bloku kontekstu!).
B) ❌ INFLATED SYMBOLISM & PRZYMIOTNIKI PRZECHWAŁKOWE (§1, §4 humanizer):
   – Czy projekt lub polecane źródła opisane są nadętym patosem: „ikona architektury”, „kopalnia wiedzy”, „kultowy przewodnik”, „absolutny lider”, „niekwestionowany złoty standard”, „genialne analizy”, „super-wydajny”?
   – Czy autor przypisuje sobie samonadane etykiety (Ego Badges): „ekspert”, „ninja”, „guru”, „pasjonat zorientowany na sukces”?
C) ❌ COPULA AVOIDANCE & PSEUDOGŁĘBIA (§3, §8 humanizer):
   – Czy zamiast prostego „jest/są/posiada” tekst używa ceremonii: „służy jako świadectwo”, „pełni kluczową rolę w ekosystemie”, „reprezentuje zmianę paradygmatu”?
   – Czy występują sztuczne zdania imiesłowowe na końcach zdań (-ing drag): „...zapewniając maksymalną wydajność i odzwierciedlając bezwzględne zaangażowanie”?
D) ❌ CZARNA LISTA SŁÓW-WYTRYCHÓW AI (§7 humanizer):
   – Wyszukaj i wskaż wystąpienia słów demaskujących model: „kompleksowy”, „innowacyjny”, „asystent decyzyjny”, „dla Twojej wygody”, „kluczowy”, „fundamentalny”, „holistyczny”, „synergia”, „ekosystem”, „wachlarz możliwości”, „w mgnieniu oka”, „w dzisiejszym dynamicznym świecie”, „nie ulega wątpliwości”.
E) ❌ SZTUCZNE ZAKRESY I WYMUSZONE TRIADY (§10, §12 humanizer):
   – Czy autor stosuje schemat False Ranges: „Od finansów osobistych, przez wskaźniki zdrowotne, aż po domowe obliczenia”?
   – Czy myśli są na siłę grupowane w trójki (Rule of Three) dla pozornej kompletności?
F) ❌ EM DASH ABUSE (§13) I BRAK LUDZKIEGO RYTMU (Burstiness & Pulse):
   – Czy tekst nadużywa myślników (—) w stylu agresywnego copywritingu sprzedażowego?
   – Czy wszystkie akapity mają identyczną, mechaniczną długość 3 zdań, czy słychać autentyczny głos inżyniera (mieszanka zdań krótkich, konkretnych faktów i technicznych detali)?

===================================================================
2. WARSTWA WIZUALNA, LAYOUT I KOMPONENTY (Design Slop):
===================================================================
A) SZABLONOWOŚĆ UI (Default Generator Look):
   – Czy strona wygląda jak niemodyfikowany szablon z v0.dev / standardowy boilerplate bez dopracowania tokenów barwnych i typografii?
   – Czy występują generyczne plamy kolorowego rozmycia (AI glow blobs / radial blur) rzucone losowo pod tekst?
B) EMOJI SPAM I LISTY WYPUNKTOWANE:
   – Czy każdy nagłówek lub punkt zaczyna się od emotikony (🚀, 💡, 🔥, ✨, 📌)?
   – Czy każda sekcja to identyczna wyliczanka: `* **Cecha:** Opis`?
C) INTERAKCJA I REALNOŚĆ KOMPONENTÓW:
   – Czy przyciski, karty i linki to działające elementy, czy „ślepe atrapy” (`href="#"`, `onClick={() => {}}`)?
   – Czy tap targets na mobile spełniają min. 44x44px?

===================================================================
WYNIK KOŃCOWY I FORMATOWANIE RAPORTU:
===================================================================
1. METRYKA AI-SLOPU:
   - Wskaźnik „AI-wości” (0% = w pełni autentyczny, rzemieślniczy projekt; 100% = surowy, nieedytowany slop z generatora).
   - Werdykt w 2-3 zdaniach.
2. TABELA WYKRYTYCH WZORCÓW PRZED VS PO:
   | Element / Cytat ze strony | Wykryty wzorzec / Naruszona reguła humanizer | Propozycja Human-First (Rzeczowo & Konkretnie) |
   |---|---|---|
   | ... | ... | ... |
3. 🎯 ZBIORCZY BACKLOG POPRAWEK ANTY-SLOP (z unikalnymi ID):
   - 🔴 [PRIORYTET P1 – RAŻĄCY SLOP / PSEUDOCOACHING / FAKE METRYKI]
     * [SLOP-01] [Sekcja/Tekst] Opis problemu i gotowy zamiennik Human-First
   - 🟡 [PRIORYTET P2 – SZABLONOWE KOMPONENTY, EMOJI SPAM, BUZZWORDS]
     * [SLOP-02] ...
   - 🟢 [PRIORYTET P3 – SZLIF STYLISTYCZNY, RYTM ZDAŃ, POLISH]
     * [SLOP-03] ...
4. Raport zapisz w pliku: `audyt/audyt-[nazwa-projektu]-ai-slop-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 3 – Audyt działania, funkcjonalności i a11y na telefonie i tablecie

```
Wejdź na stronę: [URL] (priorytet: Playwright / Chrome DevTools do emulacji dotyku, viewportów 375px/768px i odczytu konsoli JS; fallback: fetch/kod)

[OPCJONALNIE – SPECYFIKA I ZNANE ZACHOWANIA]:
– Kluczowe interakcje do przetestowania: [np. Menu mobilne hamburger, przełącznik języków PL/EN, formularz kontaktowy]
– Świadome zachowania: [np. Formularz jest w trybie offline/fallback i kopiuje e-mail do schowka]

Sprawdź działanie strony na 3 urządzeniach (Desktop, Tablet 768px, Mobile 375px) BEZ wysyłania wrażliwych danych:

1. WERSJA MOBILNA I TABLET (UX & Dotyk):
   – Czy menu mobilne (hamburger) otwiera się, zamyka i przewija do właściwej sekcji?
   – Czy po kliknięciu linku w menu mobilnym drawer/menu samo się zamyka?
   – Czy przyciski i ikony są wystarczająco duże do kliknięcia kciukiem (min. 44x44px)?
   – Czy nie występuje błąd poziomego przewijania (tzw. horizontal overflow / rozjechany viewport)?

2. LINKI I NAWIGACJA:
   – Czy kotwice (#about, #services, #experience, #projects, #blog, #contact) trafiają precyzyjnie w nagłówki?
   – Czy linki zewnętrzne otwierają się w nowej karcie z rel="noopener noreferrer"?
   – Czy przełącznik języków (PL/EN) zachowuje kontekst podstrony?

3. FORMULARZ I BEZPIECZEŃSTWO:
   – Czy formularz ma widoczne pola i prawidłowe typy inputów (email, tel)?
   – Czy formularz posiada zabezpieczenie przed botami (np. honeypot _gotcha)?
   – Czy kod źródłowy nie ujawnia prywatnych kluczy API ani wrażliwych tokenów?

4. KONSOLA I BŁĘDY:
   – Błędy JS w konsoli przeglądarki?
   – 404 na zasobach (obrazki, ikony, manifesty)?

5. ♿ DOSTĘPNOŚĆ (WCAG 2.1 AA – minimalny standard wymagany prawnie w UE/PL):
   – Kontrast: tekst ≥ 4.5:1 (normalny) / ≥ 3:1 (duży ≥18pt lub bold) [WCAG 1.4.3 AA]; elementy UI ≥ 3:1 [WCAG 1.4.11 AA, dodane w 2.1].
   – Tekst alternatywny: znaczące obrazy `alt="opis"`, ikony mają `aria-label`, dekoracyjne `alt=""`. [WCAG 1.1.1 A]
   – Nawigacja klawiaturą: tab order logiczny, focus-visible widoczny, brak pułapek klawiatury. [WCAG 2.1.1 A, 2.4.7 AA]
   – Formularze: `<label>` powiązany z każdym inputem, komunikaty błędów dostępne tekstowo. [WCAG 1.3.1 A, 3.3.1 A, 3.3.2 AA]
   – Semantyczne landmarki: `<nav>`, `<main>`, `<header>`, `<footer>`, `<aside>` użyte poprawnie. [WCAG 1.3.1 A]
   – Animacje: czy `prefers-reduced-motion` jest obsługiwane? [WCAG 2.3.3 / dobra praktyka]
   – Zoom 200%: treść czytelna bez poziomego scrolla (reflow przy 320px). [WCAG 1.4.4 AA, 1.4.10 AA]
   – Sprawdź przez axe DevTools (rozszerzenie Chrome): odnotuj liczbę błędów krytycznych i ostrzeżeń WCAG AA.

Wynik końcowy i formatowanie:
- Szczegółowy rejestr: Co działa ✅, co nie działa ❌, co wymaga uwagi ⚠️ (szczegółowe omówienie wszystkich wykrytych problemów).
- 🎯 ZBIORCZY BACKLOG POPRAWEK: Wszystkie wykryte usterki ułożone malejąco wg priorytetu (P1: błędy blokujące działanie, overflow viewportu, 404; P2: obsługa stanów formularza, tap targets < 44px; P3: drobne usprawnienia a11y) z identyfikatorami [FIX-01], [FIX-02]...
- Zapisz wynik jako: `audyt/audyt-[nazwa-projektu]-funkcjonalnosc-mobile-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 4 – Ocena estetyki, responsywności i typografii (Desktop + Tablet + Mobile)

```
Przeanalizuj wizualnie stronę: [URL] (priorytet: Playwright / Chrome DevTools do zbadania widoków 375px, 768px i 1440px; fallback: zrzuty ekranu / fetch)

[OPCJONALNIE – ZAMIERZONY STYL I VIBE]:
– Docelowy archetyp wizualny: [np. Industrial Dark / Terminal Geek / Brutalist / Clean Modern]
– Odbiorca docelowy: [np. Inżynierowie, designerzy, klienci B2B]
– Świadome decyzje wizualne: [np. Ograniczona paleta barw, wysoki kontrast, brak miękkich cieni i zaokrągleń]

Kategorie oceny (każda w skali 1-10):

A) PIERWSZE WRAŻENIE (3 sekundy na mobile i desktopie)
   – Czy natychmiast wiadomo kim jest autor i jaką wartość oferuje?
   – Czy główne CTA jest jednoznaczne i widoczne bez przewijania (above the fold)?

B) TYPOGRAFIA I HIERARCHIA
   – Czytelność fontów na małym ekranie telefonu.
   – Kontrast tekstu do tła (zgodność z normą WCAG AA min. 4.5:1).
   – Interlinia i długość linii (czy tekst nie zlewa się w blok).

C) KOLORYSTYKA I WARSTWA GRAFICZNA
   – Czy paleta kolorów jest elegancka i technologiczna?
   – Czy tło i micro-animacje wzbogacają odbiór, czy rozpraszają uwagę?

D) RESPONSYWNOŚĆ (Desktop vs Tablet vs Mobile)
   – Widok Tablet (768px–1024px): czy układ kolumn (2 kolumny) jest czytelny?
   – Widok Mobile (375px): czy karty składają się w 1 kolumnę bez ucinania tekstu?
   – Czy nie ma pustych przestrzeni lub nakładających się warstw?

E) UNIKALNOŚĆ vs SZABLONOWOŚĆ
   – 1 (gotowy szablon z generatora) → 10 (rzemieślniczy, przemyślany design).
   – Co konkretnie nadaje charakter, a co wygląda generycznie?

Wynik końcowy i formatowanie:
- Ocena łączna (1-10) oraz szczegółowa analiza każdego z 5 kryteriów A-E.
- 🎯 ZBIORCZY BACKLOG POPRAWEK: Wszystkie wykryte problemy wizualne i RWD posortowane wg priorytetu (P1: rozjazdy RWD na 375px/768px, P2: kontrast i typografia WCAG AA, P3: detale mikro-animacji i unikalny charakter) z identyfikatorami [FIX-01], [FIX-02]...
- Zapisz w: `audyt/audyt-[nazwa-projektu]-wizualny-rwd-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 5 – Audyt ze ZRZUTÓW EKRANU (dla rozszerzeń przeglądarki, aplikacji desktopowych lub widoków localhost)

> **Zastosowanie:** Użyj tego promptu, gdy audytujesz aplikację, która nie ma publicznego URL (np. **rozszerzenie Chrome**, panel administracyjny za logowaniem, aplikacja Tauri/Electron, widok z `localhost`).
> **Instrukcja:** Załącz do czatu 1–4 zrzuty ekranu (np. popup rozszerzenia, panel opcji, wersję jasną/ciemną, stan błędu).

```
Przeanalizuj załączone zrzuty ekranu interfejsu (UI/UX) aplikacji / rozszerzenia przeglądarki.

[OPCJONALNIE – KONTEKST I CEL APLIKACJI]:
– Typ i cel aplikacji: [np. Rozszerzenie Chrome do zarządzania promptami / panel admina Tauri]
– Grupa użytkowników i środowisko pracy: [np. Deweloperzy w codziennej pracy, używane na małym ekranie bocznym]
– Zamierzony styl: [np. Dark Mode spójny z Chrome SidePanel, kompaktowy interfejs]

Oceń interfejs w 5 kategoriach (każda 1-10):

1. ERGONOMIA I OGRANICZENIA PRZESTRZENI (szczególnie dla popupów Chrome: 350-400px szerokości):
   – Czy interfejs nie jest przeładowany informacjami (information overload)?
   – Czy najważniejsze akcje są dostępne w 1 kliknięciu bez przewijania?
   – Czy odstępy (paddingi) dają oddech elementom?

2. HIERARCHIA WIZUALNA I TYPOGRAFIA:
   – Czy od razu widać stan główny (np. aktywne/nieaktywne, sukces, błąd)?
   – Czy rozmiary fontów (12px-16px) są czytelne w małym oknie?
   – Czy ikony są intuicyjne i jednoznaczne?

3. SYSTEM DESIGN I SPÓJNOŚĆ (Design System):
   – Czy stylistyka pasuje do nowoczesnych standardów (np. Chrome SidePanel / Modern Web UI)?
   – Czy kolor akcentowy jest używany konsekwentnie tylko dla kluczowych akcji?
   – Jak oceniasz kontrast i dostępność (WCAG)?

4. STANY INTERFEJSU (Edge Cases):
   – Jak wygląda stan pusty (Empty State – gdy nie ma danych)?
   – Jak prezentowane są błędy i komunikaty powodzenia (Toasty / Alert bary)?
   – Czy przyciski mają czytelne stany hover/active?

5. POTENCJAŁ DO POPRAWY (UX Quick Wins & Polish):
   – Co sprawia wrażenie "amatorskiego" lub "niedokończonego"?
   – Wymień wszystkie zidentyfikowane niedociągnięcia UI/UX.

Wynik końcowy i formatowanie:
- Szczegółowe omówienie kategorii 1-5.
- 🎯 ZBIORCZY BACKLOG POPRAWEK: Wszystkie wykryte problemy ułożone malejąco wg priorytetu (P1: ergonomia i błędy widoku w małym oknie, P2: spójność Design Systemu i kontrast, P3: szlif mikro-interakcji i quick wins) z identyfikatorami [FIX-01], [FIX-02]...
- Raport sformatuj w Markdown gotowy do zapisu w:
  `audyt/audyt-[nazwa-aplikacji]-screenshot-ui-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 6 – Dedykowany audyt dostępności WCAG (Accessibility / a11y)

> **Zastosowanie:** Użyj tego promptu do pełnego audytu dostępności dla osób z niepełnosprawnościami (wzrokowymi, motorycznymi, słuchowymi, poznawczymi). Weryfikacja wg standardów WCAG:
> - **WCAG 2.0** (2008) – fundament: poziomy **A** (absolutne minimum), **AA** (standard), **AAA** (zaawansowany, opcjonalny)
> - **WCAG 2.1** (2018) – 17 nowych kryteriów: mobile, low vision, kognitywne (poziomy A i AA)
> - **WCAG 2.2** (2023) – 9 nowych kryteriów AA: focus appearance, drag-and-drop, auth, redundant entry
> - **WCAG 3.0** (Working Draft, ~2026+) – nowy model APCA kontrastu; NIE jest jeszcze standardem ISO. Status: https://www.w3.org/TR/wcag-3.0/
> - **Poziom wymagany prawnie:** WCAG 2.1 AA (EAA 2025 w UE, Ustawa o Dostępności Cyfrowej w Polsce, ADA/Section 508 w USA)

```
Wejdź na stronę: [URL] (użyj najwyższego dostępnego narzędzia: Playwright + axe-core / Chrome DevTools / WAVE / Firecrawl)

[OPCJONALNIE – KONTEKST]:
– Typ strony: [np. Portfolio / E-commerce / Blog / SaaS / Strona instytucji publicznej]
– Wymagania prawne: [np. Ustawa o dostępności cyfrowej (PL), EAA 2025 (UE), Section 508 (US)]
– Znane wyjątki: [np. Wideo bez napisów pochodzi z zewnętrznego źródła / CAPTCHA jest niezbędna]

Przeprowadź audyt dostępności wg WCAG 2.1 AA (z uwagami dla 2.2 tam gdzie zaznaczono):

ZASADA 1 – POSTRZEGALNOŚĆ (Perceivable) [WCAG 1.x]:

1.1 TEKST ALTERNATYWNY [1.1.1 – Poziom A]:
   – Czy znaczące obrazy mają opisowy alt="..." (nie: "image" / "foto" / pusta nazwa pliku)?
   – Czy obrazy dekoracyjne mają alt="" i nie są czytane przez screen readery?
   – Czy ikony SVG i ikony-fonty mają aria-label lub aria-hidden="true" gdy są ozdobne?
   – Czy przyciski-ikony (burger menu, X, strzałki) mają czytelną etykietę dla AT?

1.2 MULTIMEDIA [1.2.x – Poziom A/AA]:
   – Czy wideo z dźwiękiem ma napisy (closed captions)? [1.2.2 A]
   – Czy treść tylko-audio (podcast, nagranie) ma transkrypcję tekstową? [1.2.1 A]
   – Czy wideo informacyjne ma audiodeskrypcję lub transkrypt z opisem wizualnym? [1.2.5 AA]

1.3 ADAPTOWALNOŚĆ [1.3.x – Poziom A]:
   – Czy informacje nie są przekazywane WYŁĄCZNIE przez kolor (błąd = tylko czerwona obwódka)? [1.4.1 A]
   – Czy semantyczne landmarki HTML5 są użyte: <nav>, <main>, <header>, <footer>, <aside>? [1.3.1 A]
   – Czy hierarchia nagłówków H1→H2→H3 jest logiczna i nie pomija poziomów? [1.3.1 A]
   – Czy kolejność treści przy nawigacji klawiaturą odpowiada kolejności wizualnej? [1.3.2 A]
   – Czy tabele danych mają <caption> i <th scope> zamiast layoutowych <div>? [1.3.1 A]

1.4 ROZRÓŻNIALNOŚĆ – Kontrast i czytelność [1.4.x – Poziom AA]:
   – Kontrast tekstu normalnego (<18pt / 14pt bold): min. 4.5:1 [1.4.3 AA]
   – Kontrast tekstu dużego (≥18pt lub ≥14pt bold): min. 3:1 [1.4.3 AA]
   – Kontrast elementów UI (przyciski, inputy, checkbox, ikony aktywne): min. 3:1 [1.4.11 AA – WCAG 2.1]
   – Kontrast stanu focus (outline): min. 3:1 względem sąsiedniego koloru [2.4.11 AA – WCAG 2.2]
   – Czy tekst można powiększyć do 200% bez utraty treści / nakładania elementów? [1.4.4 AA]
   – Czy strona nie ma poziomego przewijania przy 320px (reflow)? [1.4.10 AA – WCAG 2.1]
   – Czy odstępy tekstu można zwiększyć (line-height, letter-spacing) bez utraty treści? [1.4.12 AA – WCAG 2.1]
   – Czy treść pojawiająca się na hover/focus można zamknąć (Esc), najechać i jest trwała? [1.4.13 AA – WCAG 2.1]

ZASADA 2 – FUNKCJONALNOŚĆ (Operable) [WCAG 2.x]:

2.1 NAWIGACJA KLAWIATURĄ [2.1.x – Poziom A]:
   – Czy WSZYSTKIE interaktywne elementy (linki, przyciski, inputy, modalne) są dostępne przez Tab? [2.1.1 A]
   – Czy nie ma pułapek klawiatury (focus trap dozwolony tylko w dialogach z Escape)? [2.1.2 A]
   – Czy skróty klawiszowe (1 znak) nie kolidują z czytnikami ekranu NVDA/JAWS/VoiceOver? [2.1.4 A – WCAG 2.1]

2.4 NAWIGACJA I FOCUS [2.4.x – Poziom AA]:
   – Czy istnieje skip link „Przejdź do treści" widoczny przy focus jako pierwsza opcja Tab? [2.4.1 A]
   – Czy <title> strony jest opisowy i unikalny dla każdej podstrony? [2.4.2 A]
   – Czy focus-visible jest wyraźny (nie usunięto outline: none bez zamiennika)? [2.4.7 AA / 2.4.11 AA WCAG 2.2]
   – Czy focus nie znika pod sticky headerem (scroll-margin-top lub focus offset)? [2.4.12 AA – WCAG 2.2]

2.5 MODALNOŚCI WEJŚCIA [2.5.x – WCAG 2.1/2.2]:
   – Czy gesty wielodotykowe (pinch, swipe) mają alternatywę jednopalcową? [2.5.1 A – WCAG 2.1]
   – Czy akcje drag-and-drop mają alternatywę bez przeciągania? [2.5.7 AA – WCAG 2.2]

ZASADA 3 – ZROZUMIAŁOŚĆ (Understandable) [WCAG 3.x]:

3.1 JĘZYK [3.1.x – Poziom A]:
   – Czy atrybut lang na <html> jest ustawiony poprawnie (np. lang="pl")? [3.1.1 A]
   – Czy fragmenty w innym języku mają lang na elemencie (np. <span lang="en">)? [3.1.2 AA]

3.3 FORMULARZE I BŁĘDY [3.3.x – Poziom A/AA]:
   – Czy każdy input ma powiązany <label for> lub aria-label / aria-labelledby? [3.3.2 AA]
   – Czy błędy walidacji opisane są tekstem (nie tylko kolorem/ikoną)? [3.3.1 A]
   – Czy przy błędzie sugerowana jest korekta? [3.3.3 AA]
   – Czy strona nie wymaga CAPTCHA jako jedynej opcji? [3.3.7 AA – WCAG 2.2]
   – Czy dane formularza nie są kasowane po nieudanym kroku? [3.3.7 AA – WCAG 2.2]

ZASADA 4 – SOLIDNOŚĆ (Robust) [WCAG 4.x]:

4.1 KOMPATYBILNOŚĆ Z AT [4.1.x – Poziom A/AA]:
   – Czy HTML jest poprawny: brak duplikatów id, brak niezamkniętych tagów, poprawne zagnieżdżenia? [4.1.1 A]
   – Czy wszystkie interaktywne komponenty mają dostępną role, name i value dla AT? [4.1.2 A]
   – Czy dynamiczne powiadomienia (toasty, alerty) używają aria-live="polite" lub role="alert"? [4.1.3 AA – WCAG 2.1]

POZIOMY ZGODNOŚCI (A / AA / AAA) – podsumowanie:
   – POZIOM A: absolutne minimum; niespełnienie = strona NIEDOSTĘPNA dla wielu użytkowników AT.
   – POZIOM AA: standard wymagany prawnie (EAA 2025, Ustawa o Dostępności Cyfrowej, ADA). Cel tego audytu.
   – POZIOM AAA: zaawansowany (kontrast 7:1, pełne transkrypcje, język migowy); wdrażaj tam gdzie możliwe.

WZMIANKA O WCAG 3.0 (Working Draft – NIE jest jeszcze standardem):
   – Wprowadza APCA (Accessible Perceptual Contrast Algorithm) – nowy model kontrastu zamiast 4.5:1.
   – Nowy system oceny: scoring zamiast binarnego pass/fail.
   – Nie wymagaj pełnej zgodności z WCAG 3.0 w audytach produkcyjnych.
   – Aktualny status: https://www.w3.org/TR/wcag-3.0/

Wynik końcowy i formatowanie:
- Tabela zgodności:
  | Kryterium WCAG | Wersja | Poziom | Status | Uwagi |
  |---|---|---|---|---|
  | 1.1.1 Tekst alternatywny | 2.0 | A | ✅/❌/⚠️/N/A | ... |
  | 1.4.3 Kontrast tekstu | 2.0 | AA | ✅/❌/⚠️/N/A | ... |
  | 1.4.11 Kontrast UI | 2.1 | AA | ✅/❌/⚠️/N/A | ... |
  | 2.4.11 Focus Appearance | 2.2 | AA | ✅/❌/⚠️/N/A | ... |
- Wynik z axe DevTools: [X] błędów krytycznych, [Y] poważnych, [Z] umiarkowanych, [W] informacyjnych.
- Szczegółowe omówienie WSZYSTKICH wykrytych naruszeń z opisem problemu i sugestią naprawy.
- 🎯 ZBIORCZY BACKLOG POPRAWEK DOSTĘPNOŚCI – posortowany wg priorytetu:
  🔴 [P1 – KRYTYCZNE / Poziom A]: naruszenia blokujące dostęp dla AT
    * [A11Y-01] [Kryterium WCAG] Opis problemu i rekomendacja naprawy
  🟡 [P2 – ISTOTNE / Poziom AA]: naruszenia wymogów prawnych
    * [A11Y-02] ...
  🟢 [P3 – SZLIF / Poziom AAA + WCAG 3.0-ready]: rekomendacje zaawansowane
    * [A11Y-03] ...
- Zapisz raport w: `audyt/audyt-[nazwa-projektu]-accessibility-wcag-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 7 – Dedykowany audyt SEO technicznego, On-Page i GEO (Generative Engine Optimization & AI Search)

> **Zastosowanie:** Użyj tego promptu do całościowego audytu widoczności strony zarówno w tradycyjnych wyszukiwarkach (Google, Bing), jak i wyszukiwarkach generatywnych nowej ery (Perplexity, ChatGPT Search / SearchGPT, Google AI Overviews, Claude, Microsoft Copilot).
> **Zgodność ze standardami:** Google Search Essentials, Schema.org (JSON-LD), llms.txt standard (llmstxt.org), Core Web Vitals (LCP, INP, CLS).

```
Wejdź na stronę: [URL] (użyj najwyższego dostępnego narzędzia: Playwright / Chrome DevTools / Firecrawl / fetch do analizy kodu HTML, nagłówków HTTP, metatagów i renderowania)

[OPCJONALNIE – KONTEKST STRONY I POZYCJONOWANIA]:
– Typ serwisu i cel: [np. Portfolio inżynierskie B2B / SaaS dla deweloperów / Blog techniczny / Strona firmowa]
– Główne frazy kluczowe / encje: [np. "Senior Backend Engineer Go/Python", "Audyt architektury chmurowej AWS"]
– Rynki i wersje językowe: [np. Polska (PL) oraz Globalny (EN)]
– Preferencja indeksowania AI: [np. Pełne otwarcie na boty AI i cytowania w Perplexity / SearchGPT]

Przeprowadź dogłębny audyt w 5 kluczowych filarach nowoczesnego SEO i GEO:

1. ⚙️ TECHNICZNE SEO & CRAWLABILITY (Fundament indeksowania):
   – Indeksowanie i dyrektywy: czy w `<head>` nie występuje omyłkowy tag `noindex`, `nofollow` lub dyrektywa blokująca w nagłówkach HTTP (X-Robots-Tag)?
   – Kanonizacja (Canonical): czy tag `<link rel="canonical" href="...">` jest zdefiniowany, bezwzględny (HTTPS), samoodnoszący i spójny z docelową strukturą URL (brak mixed trailing-slash `/`)?
   – Wielojęzyczność (hreflang): jeśli strona posiada wersje językowe (np. /pl i /en), czy tagi `<link rel="alternate" hreflang="..." href="...">` są wzajemne, kompletne i zawierają fallback `hreflang="x-default"`?
   – Pliki indeksujące:
     • Czy `robots.txt` jest dostępny pod `/robots.txt`, nie blokuje arkuszy CSS/JS i zawiera poprawny link do mapy witryny (`Sitemap: https://...`)?
     • Czy `sitemap.xml` jest dostępna pod `/sitemap.xml`, zawiera poprawne adresy kanoniczne, właściwe znaczniki `<lastmod>` i nie zawiera stron z błędami 404/301?
   – Architektura renderowania (SSR / SSG vs CSR):
     • Czy kluczowa treść, nagłówki i linki znajdują się w surowym kodzie HTML (widocznym dla crawlerów bez silnika JS), czy wymagają hydratacji klienta?
     • Czy serwis nie powoduje tzw. soft 404 (zwracanie kodu 200 OK dla nieistniejących podstron)?
   – Przekierowania i protokoły: wymuszenie HTTPS, brak pętli przekierowań (redirect loops) i brak zbędnych łańcuchów 301/302.

2. 📝 ON-PAGE SEO & ARCHITEKTURA TREŚCI:
   – Meta Title: unikalny dla każdej podstrony, długość 50–60 znaków (lub ~580px), zawiera markę oraz kluczowe słowo/rolę na początku, brak keyword stuffingu.
   – Meta Description: unikalny, zachęcający do kliknięcia (CTR), długość 120–155 znaków, adekwatny do intencji użytkownika.
   – Hierarchia nagłówków (H1–H3):
     • Dokładnie jeden nagłówek `<h1>` na podstronie, jasno definiujący tożsamość/wartość serwisu.
     • Logiczna struktura `<h2>` dla sekcji głównych i `<h3>` dla podsekcji/kart projektów (brak skoków z H2 do H4).
   – Linkowanie wewnętrzne i kotwice (Anchor Text):
     • Czy linki wewnętrzne posiadają deskryptywne teksty kotwic (np. "Zobacz architekturę projektu X" zamiast generycznego "kliknij tutaj" lub "więcej")?
     • Czy nie ma uszkodzonych odnośników wewnętrznych (broken anchors / 404)?
   – Optymalizacja grafik pod SEO:
     • Czy wszystkie znaczące obrazy posiadają wartościowe atrybuty `alt` w kontekście strony?
     • Czy obrazy wykorzystują nowoczesne formaty (WebP/AVIF) i mają zadeklarowane `width`/`height` zapobiegające przesunięciom layoutu (CLS)?

3. 🏷️ DANE STRUKTURALNE (Schema.org / JSON-LD & Rich Results):
   – Obecność i poprawność JSON-LD: czy kod danych strukturalnych jest poprawnie osadzony w `<script type="application/ld+json">` i nie rzuca błędów walidacji?
   – Dobór schematów do profilu witryny:
     • Portfolio / strona personalna: schemat `Person` powiązany z `ProfilePage` lub `WebSite` (właściwości: `name`, `jobTitle`, `knowsAbout`, `sameAs` linkujące do GitHub, LinkedIn, Twitter/X).
     • Firma / SaaS: schemat `Organization` lub `SoftwareApplication` (`name`, `description`, `applicationCategory`, `operatingSystem`).
     • Artykuły / Blog: schemat `TechArticle` lub `BlogPosting` (`headline`, `author`, `datePublished`, `dateModified`).
     • Nawigacja: schemat `BreadcrumbList` dla hierarchii podstron.
   – Weryfikacja Rich Results: czy dane spełniają wytyczne Google Rich Results i umożliwiają wyświetlanie rozszerzonych wyników wyszukiwania?

4. 🤖 GEO (Generative Engine Optimization) & WYSZUKIWARKI AI (Perplexity, SearchGPT, AI Overviews):
   – Dostępność w `robots.txt` dla crawlerów generatywnych:
     • Czy boty AI (np. `GPTBot`, `OAI-SearchBot`, `PerplexityBot`, `ClaudeBot`, `Google-Extended`, `Bingbot`) nie są zablokowane, jeśli celem jest widoczność w wyszukiwarkach AI?
   – Standard `llms.txt` i `llms-full.txt` (llmstxt.org):
     • Czy w roocie domeny istnieje plik `/llms.txt` przygotowany specjalnie pod modele językowe, zawierający zwięzłe podsumowanie profilu, specjalizacji, kluczowych projektów oraz odnośniki do pełnej dokumentacji?
   – Optymalizacja cytowań (Information Gain & Fact Density):
     • Czy w kluczowych sekcjach występują zwięzłe bloki podsumowujące (Direct Answer format – 2-3 zdania wprost odpowiadające na pytania: kim jest autor, co robi, jakie problemy rozwiązuje)?
     • Czy dane techniczne i osiągnięcia podane są w tabelach lub punktach (dane ustrukturyzowane, które roboty AI łatwo parsują i cytują w odpowiedziach)?
     • Unikanie wieloznaczności (Entity-based SEO): czy pojęcia technologiczne i powiązania branżowe są jednoznacznie zdefiniowane, co zapobiega halucynacjom modeli AI?

5. ⚡ MOBILE-FIRST & CORE WEB VITALS (Wpływ wydajności na ranking):
   – Parytet treści Mobile vs Desktop: czy robot Google Smartphone widzi dokładnie tę samą treść i linki co użytkownik na komputerze?
   – LCP (Largest Contentful Paint) < 2.5s: czy główny element widoku (hero text / grafika) ładuje się natychmiast bez opóźnień fontów?
   – CLS (Cumulative Layout Shift) < 0.1: czy elementy strony nie skaczą podczas doładowywania stylów, czcionek lub obrazków?
   – INP (Interaction to Next Paint) < 200ms: czy interakcje (otwarcie menu, kliknięcie zakładek) reagują płynnie?

Wynik końcowy i formatowanie:
- Tabela zbiorcza zgodności SEO & GEO:
  | Obszar audytu | Badany parametr | Status | Wpływ na pozycjonowanie / AI | Uwagi |
  |---|---|---|---|---|
  | ⚙️ Techniczne | Indeksowanie (noindex / canonical / robots / sitemap) | ✅/❌/⚠️ | Krytyczny | ... |
  | ⚙️ Wielojęzyczność | Tagi hreflang + x-default | ✅/❌/⚠️/N/A | Wysoki | ... |
  | 📝 On-Page | Meta title, description, H1-H3, alt | ✅/❌/⚠️ | Wysoki | ... |
  | 🏷️ Dane strukturalne | Schema.org JSON-LD (Person/Org/Article) | ✅/❌/⚠️ | Średni/Wysoki | ... |
  | 🤖 GEO / AI Search | robots.txt boty AI, /llms.txt, Fact Density | ✅/❌/⚠️ | Strategiczny (AI) | ... |
  | ⚡ Core Web Vitals | LCP, CLS, INP, Mobile Parity | ✅/❌/⚠️ | Wysoki (Ranking) | ... |

- Szczegółowe omówienie WSZYSTKICH wykrytych problemów, braków i podatności SEO.
- 🎯 ZBIORCZY BACKLOG POPRAWEK SEO & GEO – posortowany wg priorytetu:
  🔴 [PRIORYTET P1 – KRYTYCZNE / BLOKERY INDEKSACJI] (np. omyłkowy noindex, błędy canonical, zablokowane boty w robots.txt, brak lub wielokrotne H1, błędy 404 w sitemapie):
    * [SEO-01] [Obszar] Konkretny problem i rekomendowany kod / rozwiązanie
  🟡 [PRIORYTET P2 – ISTOTNE / ON-PAGE, SCHEMA & HREFLANG] (np. brak JSON-LD Schema.org, brakujące tagi hreflang, za długi/ucięty title, brak alt na obrazach):
    * [SEO-02] ...
  🟢 [PRIORYTET P3 – SZLIF / GEO, LLMS.TXT & AI CITATIONS] (np. wdrożenie pliku /llms.txt, optymalizacja Direct Answer dla SearchGPT/Perplexity, breadcrumbs JSON-LD):
    * [SEO-03] ...
- Zapisz gotowy raport w: `audyt/audyt-[nazwa-projektu]-seo-geo-[RRRR-MM-DD]-[GGMM].md`
```

---

## 🛠️ Szablony promptów do wdrażania poprawek po audycie

Po wygenerowaniu raportu audytu masz w nim **wszystkie wykryte problemy ponumerowane jako `[FIX-01]`, `[A11Y-01]`, `[SEO-01]`... i posortowane wg priorytetów (P1, P2, P3)**.
Dzięki temu nie musisz wdrażać wszystkiego naraz ani przepisywać kodu ręcznie. Wybierz odpowiedni szablon poniżej:

### ⚡ WARIANT A: Błyskawiczny (w tym samym oknie czatu z audytem)
*Zastosowanie: Gdy rozmawiasz z ChatGPT, Claude lub Antigravity i chcesz natychmiast przejść do poprawek.*

```markdown
Przeanalizowałem Twój raport audytu. Chcę wdrożyć WYBRANE poprawki według poniższego planu:

1. 🚀 WDRÓŻ BEZ ZMIAN (dokładnie tak jak zaproponowałeś):
   - Cały pakiet [P1] (wszystkie krytyczne blokery)
   - [FIX-04]

2. ✏️ WDRÓŻ Z MOJĄ MODYFIKACJĄ:
   - [FIX-02]: Zamiast usuwać ten element, zmień tylko jego kolor na stonowany szary i zmniejsz padding na mobile (375px).
   - [FIX-05]: Nie instaluj żadnej nowej biblioteki npm – rozwiąż to za pomocą czystego CSS/JS.

3. 🛑 ODRZUĆ (nie dotykaj tego kodu):
   - [FIX-06] (to celowy zabieg projektowy / zamierzona decyzja biznesowa)

Zadanie dla Ciebie:
Wygeneruj precyzyjny plan wdrożenia / kod diff dla wybranych punktów. Przestrzegaj zasad Anti-AI-Slop (brak ślepych linków, brak zbędnych bibliotek, przetestowane na 375px).
```

---

### 🚀 WARIANT B: Samodzielny prompt wdrożeniowy (do nowego czatu / Cursor / Antigravity)
*Zastosowanie: Gdy otwierasz nowy wątek lub pracujesz w edytorze kodu z plikiem audytu (np. `audyt/audyt-lukaszzychal.dev-multiperspektywowy-....md`).*

```markdown
Działasz jako Senior Frontend & Software Architect.
Twoim zadaniem jest wdrożenie ściśle wyselekcjonowanych poprawek z raportu audytu.

### 📄 KONTEKST AUDYTU:
Raport z audytu znajduje się w pliku: [audyt/audyt-lukaszzychal.dev-multiperspektywowy-[DATA].md]

### 🎯 WYBRANE ZADANIA DO REALIZACJI:
Wdróż wyłącznie poniższe punkty z raportu:
- [FIX-01]: [Krótki opis, np. Naprawa menu mobilnego - brakujące zamykanie po kliknięciu kotwicy]
- [FIX-03]: [Krótki opis, np. Poprawa rozmiaru tap targets do min. 44x44px na ekranie 375px]
- [FIX-07]: [Krótki opis, np. Usunięcie korpo-frazesów z sekcji Hero i dodanie konkretnych technologii]

### 📐 WYTYCZNE TECHNICZNE:
1. Nie wprowadzaj żadnych innych zmian poza wymienionymi punktami.
2. Zadbaj o brak regresji RWD (375px, 768px, 1440px).
3. Żadnego AI-slopu: brak ślepych linków, zachowana hierarchia H1->H3, czysty semantyczny kod.
4. Przedstaw zmiany w formie konkretnych diffów w istniejących plikach.
```

---

### 🪄 WARIANT C: Generator promptu dla zewnętrznego narzędzia (v0 / Bolt.new / Lovable)
*Zastosowanie: Gdy audyt zrobiłeś w Claude/ChatGPT, ale kod strony generuje dla Ciebie zewnętrzne narzędzie (v0.dev, Bolt, Lovable).*

```markdown
Na podstawie powyższego audytu chcę wdrożyć wyłącznie punkty: [FIX-01], [FIX-02], [FIX-05].

Napisz dla mnie precyzyjny, zwarty prompt gotowy do wklejenia w [v0.dev / Bolt.new / Lovable], który:
1. Skupi się WYŁĄCZNIE na tych 3 wybranych poprawkach (nie wspominaj o punktach odrzuconych).
2. Będzie zawierał ścisłe wytyczne inżynierskie (Mobile-First 375px, tap targets 44px, semantyka HTML, brak zbędnych bibliotek).
3. Zostanie sformatowany w zwięzły sposób wymuszający natychmiastowe naniesienie poprawek w kodzie.
```

---

## 📋 Checklist przed audytem produkcyjnym

- [ ] Czy strona jest wdrożona na VPS/serwerze (lub czy uruchomiono tunel dla localhost)?
- [ ] Czy przetestowano widok na telefonie (375px), tablecie (768px) i desktopie (1440px)?
- [ ] Czy menu mobilne działa poprawnie i zamyka się po kliknięciu linku?
- [ ] Czy Google Search Console widzi stronę i nie zgłasza błędów indeksowania?
- [ ] Czy w kodzie produkcyjnym nie pozostał przypadkowy tag `<meta name="robots" content="noindex">`?
- [ ] Czy tag `<link rel="canonical">` wskazuje na właściwy, ostateczny URL (HTTPS, bez pętli przekierowań)?
- [ ] Czy wersje językowe (PL/EN) mają dwukierunkowe tagi `hreflang` oraz fallback `hreflang="x-default"`?
- [ ] Czy dane strukturalne JSON-LD (Schema.org: Person / WebSite / Organization) przechodzą test w Google Rich Results Test?
- [ ] Czy `sitemap.xml` jest dostępny (np. `twojadomena.pl/sitemap.xml`) i zawiera tylko adresy 200 OK?
- [ ] Czy `robots.txt` jest prawidłowy (`twojadomena.pl/robots.txt`), linkuje do sitemapy i nie blokuje botów AI (GPTBot, PerplexityBot), jeśli zależy Ci na widoczności w SearchGPT/Perplexity?
- [ ] Czy w katalogu głównym domeny wdrożono plik `/llms.txt` ze zwięzłą specyfikacją witryny dla modeli AI?
- [ ] Czy HTTPS jest aktywny (zielona kłódka i brak mixed-content)?
- [ ] Czy strona przeszła audyt dostępności (axe DevTools / WAVE) bez błędów krytycznych WCAG 2.1 AA?
- [ ] Czy nawigacja klawiaturą (Tab, Shift+Tab, Enter, Escape) działa bez pułapek fokusa?
- [ ] Czy wszystkie obrazy mają poprawne `alt` (znaczące: opisowy tekst ze słowami kluczowymi; dekoracyjne: `alt=""`)?
- [ ] Czy atrybut `lang` jest ustawiony na `<html lang="pl">` (lub inny właściwy język)?

---

## 🛡️ Prompty zapobiegające AI-Slop podczas GENEROWANIA stron
Szczegółowy podręcznik oraz gotowe moduły "Anti-AI-Slop Master Directive" (wersja skrócona do promptów oraz pełna wersja dla `.cursorrules` / Claude Projects) znajdziesz w pliku:
👉 [docs/anti-ai-slop-generator-prompt.md](file:///Users/lukaszzychal/PhpstormProjects/lukasz.zychal.dev-wp/docs/anti-ai-slop-generator-prompt.md)

