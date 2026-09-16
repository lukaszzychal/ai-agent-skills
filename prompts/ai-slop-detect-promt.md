# 🔍 Audyt strony i aplikacji – Narzędzia i Prompty

## Narzędzia online

| Narzędzie | Cel | Link |
|---|---|---|
| **PageSpeed Insights** | Core Web Vitals, SEO techniczne, dostępność | https://pagespeed.web.dev/ |
| **Google Rich Results Test** | Czy Google rozumie strukturę treści (schema.org) | https://search.google.com/test/rich-results |
| **Google Search Console** | Indeksowanie, błędy crawlowania, hreflang | https://search.google.com/search-console |
| **Ahrefs Free SEO Checker** | Szybki audit SEO | https://ahrefs.com/seo-checker |
| **Screaming Frog (free)** | Crawl strony: broken links, meta, redirects | https://www.screamingfrog.co.uk/seo-spider/ |
| **WAVE Web Accessibility** | Dostępność (a11y), kontrast, ARIA | https://wave.webaim.org/ |
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

---

### Prompt 1 – Audyt wieloperspektywowy (rekruter, dev, designer + Mobile/Tablet)

```
Wejdź na stronę: [URL]

Przeprowadź kompleksowy audyt z 5 różnych perspektyw. 
Dla KAŻDEJ perspektywy uwzględnij analizę 3 widoków ekranu:
- DESKTOP (1440px+)
- TABLET (768px – 1024px, np. iPad w pionie i poziomie)
- MOBILE (375px – 414px, smartfon)

Oceń każdą perspektywę w skali 1-10 i podaj konkretne uwagi:

1. 🧑‍💼 REKRUTER IT (PHP/Backend)
   – Czy profil i specjalizacja są czytelne w 30 sekund na telefonie i desktopie?
   – Czy widać seniorski poziom i konkretne efekty biznesowe?
   – Co by Cię zatrzymało, a co by odrzuciło na mobile i na dużym ekranie?

2. 🎨 UI/UX DESIGNER (Desktop + Tablet + Mobile)
   – Spójność wizualna, hierarchia treści, typografia, kontrast.
   – Responsywność: czy elementy nie wyjeżdżają poza ekran (poziomy scroll)?
   – Wersja Tablet: jak zachowują się gridy (2 kolumny) i marginesy boczne?
   – Wersja Mobile: czytelność menu hamburger, rozmiar przycisków dotykowych (tap targets min. 44x44px), spacing.
   – Czy design jest unikalny czy wygląda jak AI-default (v0/shadcn template)?

3. 👨‍💻 FRONTEND DEVELOPER
   – Struktura semantyczna HTML (H1-H3, article, section, nav).
   – Dostępność (a11y): ARIA labels, kontrast tekstu, focus states.
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
Przygotuj raport w formacie Markdown z nagłówkiem i podsumowaniem TOP 3 priorytetów do poprawy oraz ogólną oceną 1-10.
Raport przygotuj do zapisu w pliku:
`audyt/audyt-[nazwa-projektu]-multiperspektywowy-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 2 – Detektor "AI slop" / autentyczności (Desktop & Mobile)

```
Wejdź na stronę: [URL]

Oceń, czy ta strona wygląda jak "AI slop" (generyczna, bez charakteru, wygenerowana przez AI bez autorskiej edycji i dopracowania).

Przeanalizuj treść i wygląd na komputerze, tablecie i telefonie:
1. WARSTWA TEKSTOWA I TONE OF VOICE:
   – Czy teksty brzmią autentycznie, po inżyniersku, z charakterem?
   – Czy występują puste frazesy z "korpo-szczurowni" (np. "dostarczam innowacyjne synergie 360")?
   – Czy treści są spójne wewnętrznie i poparte faktami z kariery?
   – Czy za stroną stoi wyrazisty, konkretny człowiek?

2. WARSTWA WIZUALNA I RESPONSYWNOŚĆ (Desktop vs Mobile/Tablet):
   – Czy design wygląda jak domyślny template z v0.dev / podstawowy Tailwind bez dopracowania?
   – Czy na smartfonie czcionki, paddery i marginesy są zbalansowane, czy wyglądają na "sklejkę na szybko"?
   – Czy elementy interaktywne (karty, widgety, animacje) mają duszę i pasują do tematyki strony?

Wynik końcowy:
- Procent "AI-wości" (0% = w pełni autentyczna i unikalna, 100% = czysty AI slop)
- Lista konkretnych fraz lub elementów wymagających natychmiastowej poprawy
- Raport sformatowany do zapisu w: `audyt/audyt-[nazwa-projektu]-ai-slop-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 3 – Audyt działania, funkcjonalności i a11y na telefonie i tablecie

```
Wejdź na stronę: [URL]

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

Raport: Co działa ✅, co nie działa ❌, co wymaga uwagi ⚠️
Zapisz wynik jako: `audyt/audyt-[nazwa-projektu]-funkcjonalnosc-mobile-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 4 – Ocena estetyki, responsywności i typografii (Desktop + Tablet + Mobile)

```
Przeanalizuj wizualnie stronę: [URL]

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

Wynik końcowy: Ocena łączna + TOP 5 rekomendacji projektowych.
Zapisz w: `audyt/audyt-[nazwa-projektu]-wizualny-rwd-[RRRR-MM-DD]-[GGMM].md`
```

---

### Prompt 5 – Audyt ze ZRZUTÓW EKRANU (dla rozszerzeń przeglądarki, aplikacji desktopowych lub widoków localhost)

> **Zastosowanie:** Użyj tego promptu, gdy audytujesz aplikację, która nie ma publicznego URL (np. **rozszerzenie Chrome**, panel administracyjny za logowaniem, aplikacja Tauri/Electron, widok z `localhost`).
> **Instrukcja:** Załącz do czatu 1–4 zrzuty ekranu (np. popup rozszerzenia, panel opcji, wersję jasną/ciemną, stan błędu).

```
Przeanalizuj załączone zrzuty ekranu interfejsu (UI/UX) aplikacji / rozszerzenia przeglądarki.

Kontekst aplikacji: [Krótki opis: np. Rozszerzenie Chrome do zarządzania promptami / monitorowania API]

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

5. POTENCJAŁ DO POPRAWY (UX Quick Wins):
   – Co sprawia wrażenie "amatorskiego" lub "niedokończonego"?
   – Jakie 3 drobne zmiany natychmiast podniosą wrażenie jakości (premium feel)?

Raport sformatuj w Markdown gotowy do zapisu w:
`audyt/audyt-[nazwa-aplikacji]-screenshot-ui-[RRRR-MM-DD]-[GGMM].md`
```

---

## 📋 Checklist przed audytem produkcyjnym

- [ ] Czy strona jest wdrożona na VPS/serwerze (lub czy uruchomiono tunel dla localhost)?
- [ ] Czy przetestowano widok na telefonie (375px), tablecie (768px) i desktopie (1440px)?
- [ ] Czy menu mobilne działa poprawnie i zamyka się po kliknięciu linku?
- [ ] Czy Google Search Console widzi stronę?
- [ ] Czy `sitemap.xml` jest dostępny (np. `twojadomena.pl/sitemap.xml`)?
- [ ] Czy `robots.txt` jest prawidłowy (`twojadomena.pl/robots.txt`)?
- [ ] Czy HTTPS jest aktywny (zielona kłódka)?

---

## 🛡️ Prompty zapobiegające AI-Slop podczas GENEROWANIA stron
Szczegółowy podręcznik oraz gotowe moduły "Anti-AI-Slop Master Directive" (wersja skrócona do promptów oraz pełna wersja dla `.cursorrules` / Claude Projects) znajdziesz w pliku:
👉 [docs/anti-ai-slop-generator-prompt.md](file:///Users/lukaszzychal/PhpstormProjects/lukasz.zychal.dev-wp/docs/anti-ai-slop-generator-prompt.md)

