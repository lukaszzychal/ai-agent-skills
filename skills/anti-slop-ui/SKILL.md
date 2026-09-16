---
name: anti-slop-ui
description: "Rygorystyczny architekt i audytor interfejsów webowych oraz stron WWW. Eliminuje szablonowy kod AI ('AI slop'), korporacyjny bełkot, generyczne plamy blur, ślepe linki oraz wymusza bezbłędny semantyczny HTML, a11y (WCAG AA) i perfekcyjne RWD (375px/768px/1440px)."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "ui-ux", "anti-slop", "frontend", "web-design", "clean-code", "a11y", "responsive-design", "craftsmanship"]
---

# Architekt Interfejsów i Pogromca AI-Slopu (Anti-Slop UI Skill)
> **Autor / Twórca:** Łukasz / Lukasz Zychal

Ten skill służy do **projektowania, implementacji oraz bezwzględnego audytu stron WWW i komponentów interfejsu**. Gwarantuje, że wygenerowany kod nie nosi żadnych znamion szablonowości AI (*"AI slop"*, *"AI generated template"*, *"corporate filler"*), jest autentyczny, inżynierski i gotowy na rygor produkcji.

### Kiedy aktywować ten skill?
Aktywuj ten skill zawsze, gdy użytkownik prosi o:
- Wygenerowanie nowej strony, landing page'a, portfolio lub widoku aplikacji
- Stworzenie komponentu UI, nawigacji, sekcji Hero, formularza
- *"Sprawdź czy ta strona to nie AI slop"*
- *"Oczyść ten kod ze sztucznego AI-bełkotu i generycznego designu"*
- Audyt wizualny, semantyczny lub RWD frontendu

---

## 🚫 1. CZARNA LISTA AI-SLOPU (Blacklist)

### A. Zakaz Korporacyjnego Bełkotu (Tone of Voice Ban)
- **ZAKAZANE SŁOWA I ZWROTY:** *"innowacyjny"*, *"kompleksowy"*, *"synergia"*, *"transformacja cyfrowa"*, *"nowoczesne podejście"*, *"dostarczamy wartość 360"*, *"szyte na miarę"*, *"dynamicznie rozwijający się"*, *"lider w branży"*.
- **STANDARD ZASTĘPCZY:** Pisz wyłącznie twardym językiem faktów i inżynierii:
  `[Zidentyfikowany Problem]` ➔ `[Zastosowane Narzędzie / Wzorzec Architektoniczny]` ➔ `[Mierzalny Rezultat Techniczny lub Biznesowy]`.

### B. Zakaz Zmyślonych Metryk (Fake Metrics Ban)
- **ZAKAZANE:** Wymyślanie wskaźników typu *"+350% wzrostu ROI"*, *"99.9% zadowolenia klientów"*, *"10M+ obsłużonych zapytań"*, jeśli użytkownik nie dostarczył tych danych w specyfikacji.
- **STANDARD ZASTĘPCZY:** Skup się na twardych faktach architektury (np. konteneryzacja Docker, kolejkowanie RabbitMQ, asynchroniczność, optymalizacja indeksów bazodanowych) lub pozostaw miejsce na uzupełnienie z wyraźnym oznaczeniem.

### C. Zakaz Wizualnego Szablonu AI (Aesthetic Slop Ban)
- **ZAKAZANE:**
  - Centralne, gigantyczne, rozmyte plamy fioletowo-różowe lub indygo (`w-96 h-96 bg-purple-500/20 blur-3xl rounded-full`).
  - Identyczne, powtarzalne karty w bento-gridzie ze sztucznymi gradientowymi ramkami bez głębi.
  - Puste, asymetryczne połowy ekranu w sekcji Hero (np. lewa kolumna z tekstem, prawa pusta z `hidden lg:flex`).
  - Stockowe atrapy urządzeń (mockup laptopa/telefonu z losowym zrzutem).
- **STANDARD ZASTĘPCZY:**
  - Unikalny, rzemieślniczy design system (Industrial, Brutalist, Minimalist Dark lub Clean Technical).
  - Kontrast min. 4.5:1 (WCAG AA).
  - Zbalansowana sekcja Hero (np. interaktywny widget terminala, dedykowany canvas, schemat architektury, autentyczny podgląd kodu).

### D. Zakaz Ślepych Linków i Błędnych Zasobów (Zero Dead Anchors & 404s)
- **ZAKAZANE:**
  - Linki w nawigacji lub przyciskach typu `href="#blog"`, `href="#pricing"`, jeśli na stronie nie istnieje element o dokładnie takim `id`.
  - Linkowanie w `<head>` lub `<img>` do nieistniejących plików (np. `/apple-icon.png`, `/avatar.jpg`).
- **STANDARD ZASTĘPCZY:** Każda kotwica musi mieć fizycznie istniejący element docelowy z identycznym ID. Jeśli dana sekcja jeszcze nie powstała, NIE DODAWAJ do niej linku w menu!

---

## 📐 2. STANDARD INŻYNIERSKI FRONTENDU & A11Y

### A. Semantyczna Hierarchia HTML
1. **Pojedynczy H1:** Dokładnie jeden `<h1>` na podstronie (główny tytuł / profil).
2. **Hierarchia bez przeskoków:**
   - Główne sekcje strony to `<h2>`.
   - Podsekcje, nazwy projektów, stanowiska i artykuły to `<h3>`.
   - **NIGDY nie skacz z `<h2>` bezpośrednio do `<h4>`!**
3. **Semantyka struktury:**
   - Używaj `<main>`, `<nav>`, `<aside>`, `<header>`, `<footer>`.
   - Każda niezależna jednostka treści (karta projektu, wpis doświadczenia zawodowego, wpis na blogu) MUSI być opakowana w `<article>`.
   - Sekcje powinny posiadać nagłówki powiązane przez `<section aria-labelledby="section-heading-id">`.

### B. Dostępność (a11y - WCAG 2.1 AA)
1. **Przyciski i Ikony:**
   - Każdy przycisk lub link zawierający tylko ikonę MUSI posiadać czytelny `aria-label` lub ukryty tekst pomocniczy (`<span className="sr-only">Opis akcji</span>`).
   - Czysto dekoracyjne ikony SVG muszą posiadać atrybut `aria-hidden="true"`.
2. **Minimalne pole dotykowe (Tap Targets):**
   - Na urządzeniach mobilnych i tabletach każdy klikalny element MUSI mieć wymiary minimum **44x44px** (`min-h-[44px] min-w-[44px]` lub padding zapewniający ten obszar).
3. **Mikro-interakcje i Animacje:**
   - Każda animacja CSS / Framer Motion / Canvas MUSI szanować preferencje użytkownika:
     `@media (prefers-reduced-motion: reduce)`.

---

## 📱 3. ŻELAZNY AUDYT RESPONSYWNOŚCI (Mobile-First RWD)

Każdy wygenerowany lub edytowany layout musi bezwzględnie przejść test w 3 widokach:

| Urządzenie / Szerokość | Wymóg Architektoniczny | Typowe Błędy do Wyeliminowania |
|---|---|---|
| **Mobile (375px – 414px)** | Układ 1-kolumnowy, elastyczne szerokości, zero overflow-x | Poziomy scroll, za małe przyciski kciuka, nakładający się tekst w nagłówkach |
| **Menu Mobilne (Drawer)** | Hamburger otwiera czytelne menu, **po kliknięciu dowolnego linku menu natychmiast automatycznie się zamyka** i płynnie scrolluje | Menu pozostające otwarte i zasłaniające treść po przejściu do kotwicy |
| **Tablet (768px – 1024px)** | Wyważony układ 2-kolumnowy, równe marginesy boczne | Rozciągnięte karty na 100% szerokości lub nienaturalnie ściśnięte 3 kolumny |
| **Desktop (1440px+)** | Zbalansowany Hero, max-width kontenera (np. 1280px/1440px) | Pusta prawa połowa ekranu, niespójne wyrównanie lewej i prawej strony |

---

## 🛡️ 4. ODPORNOŚĆ NA BŁĘDY & GRACEFUL DEGRADATION

1. **Formularze i Kontakt:**
   - Formularz musi zawierać pole antyspamowe typu honeypot (np. `<input type="text" name="_gotcha" tabIndex="-1" autoComplete="off" className="hidden" />`).
   - **Brak backendu / env:** Jeśli serwis nie ma skonfigurowanych kluczy API (np. Resend, SendGrid), formularz NIE MOŻE sypać błędem 500 ani udawać, że wysłał. Musi zaoferować elegancki fallback: bezpieczny przycisk "Kopiuj adres e-mail" z dymkiem potwierdzenia lub bezpośrednie wywołanie `mailto:`.
2. **Linki Zewnętrzne:**
   - Wszystkie odnośniki wychodzące poza domenę muszą posiadać: `target="_blank" rel="noopener noreferrer"`.
3. **SEO & Social Share:**
   - Każda strona musi generować dedykowany tag `og:image` (1200x630px).
   - Metadane muszą zawierać canonical, hreflang (jeśli wielojęzyczna) oraz strukturalne dane Schema.org (JSON-LD: `Person`, `Organization` lub `WebSite`).

---

## 🔄 5. PROCEDURA DZIAŁANIA DLA AGENTA

### Tryb A: Gdy tworzysz nowy kod (Generator)
1. **Krok 1 (Styl & Architektura):** Ustal spójną paletę kolorów, fonty i layout bez używania domyślnych fioletowych kul blur.
2. **Krok 2 (Semantyka):** Przygotuj czysty szkielet HTML: jeden `H1`, sekcje `H2` z `aria-labelledby`, karty z `<article>` i `H3`.
3. **Krok 3 (Kotwice):** Upewnij się, że każdy link w menu odpowiada istniejącemu elementowi docelowemu.
4. **Krok 4 (Mobilność):** Zadbaj o automatyczne zamykanie menu mobilnego i tap targets min. 44x44px.
5. **Krok 5 (Treść):** Pisz technicznie i konkretnie, bez pustych przymiotników i naciąganych liczb.

### Tryb B: Gdy audytujesz lub oczyszczasz istniejący kod (Sanitizer)
1. Wyszukaj i usuń zwroty korporacyjne z czarnej listy.
2. Zamień błędne poziomy nagłówków (np. `H4` pod `H2` ➔ `H3`).
3. Usuń martwe odnośniki w nawigacji.
4. Sprawdź i dodaj brakujące `aria-label` oraz `aria-hidden="true"`.
5. Dodaj automatyczne zamykanie do menu mobilnego i zabezpiecz formularze przed spamem.
