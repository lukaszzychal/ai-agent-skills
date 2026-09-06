---
name: tauri-troubleshooter
description: "Ekspert aplikacji desktopowych Tauri (v1 i v2) z backendem Rust i nowoczesnym frontendem (Next.js/React/Vue). Rozwiązuje problemy z IPC, uprawnieniami systemowymi, budowaniem i bundlingiem cross-platform."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "tauri", "rust", "desktop", "cross-platform", "ipc", "nextjs", "bundling"]
---

# Tauri & Rust Desktop Troubleshooter
> **Autor / Twórca:** Łukasz / Lukasz Zychal

Ten skill służy do **projektowania, debugowania, optymalizacji i przygotowywania dystrybucji (bundlingu)** wieloplatformowych aplikacji desktopowych opartych o **Tauri (v1 oraz v2)** z warstwą natywną w Rust oraz nowoczesnym frontendem webowym (Next.js, React, Vue, Svelte).

---

## 🎯 Kiedy aktywować ten skill?
- Użytkownik prosi o: "napraw błąd w Tauri", "jak spiąć Rust z frontendem przez invoke / emit", "aplikacja Tauri nie buduje się", "uprawnienia do mikrofonu / plików w macOS / Windows", "jak przygotować paczkę .dmg / .exe / .deb", "integracja Next.js z Tauri".
- Błędy kompilacji `cargo build` w podkatalogu `src-tauri`.
- Problemy z architekturą IPC (Inter-Process Communication) lub synchronizacją stanu pomiędzy Rustem a JavaScript/TypeScript.

---

## 🌉 Architektura Komunikacji IPC (Rust <-> Frontend)

### 1. Wzorzec Poleceń (Request-Response z `invoke`)

#### Po stronie Rust (`src-tauri/src/main.rs` lub `lib.rs`):
```rust
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize, Debug)]
pub struct TranslationRequest {
    pub text: String,
    pub target_lang: String,
}

#[derive(Serialize, Deserialize, Debug)]
pub struct TranslationResponse {
    pub translated_text: String,
    pub confidence: f32,
}

#[tauri::command]
pub async fn translate_payload(payload: TranslationRequest) -> Result<TranslationResponse, String> {
    if payload.text.trim().is_empty() {
        return Err("Pusty tekst wejściowy".into());
    }

    Ok(TranslationResponse {
        translated_text: format!("[Translated to {}]: {}", payload.target_lang, payload.text),
        confidence: 0.98,
    })
}
```

#### Po stronie TypeScript / React:
```typescript
import { invoke } from '@tauri-apps/api/core'; // Tauri v2 lub '@tauri-apps/api/tauri' w v1

interface TranslationResponse {
  translated_text: string;
  confidence: number;
}

export async function sendTranslation(text: string, target_lang: string): Promise<TranslationResponse> {
  try {
    return await invoke<TranslationResponse>('translate_payload', {
      payload: { text, target_lang }
    });
  } catch (error) {
    console.error('Błąd wywołania Tauri IPC:', error);
    throw error;
  }
}
```

---

### 2. Zdarzenia Dwukierunkowe i Strumienie (Events & Streaming)
Gdy aplikacja odbiera dane w czasie rzeczywistym (np. chunk audio, status transkrypcji):
- **Z Rusta do Okna UI:**
  `app_handle.emit("transcription-chunk", json!({ "text": "...", "is_final": false }))?;`
- **W Frontendzie:**
  ```typescript
  import { listen } from '@tauri-apps/api/event';
  
  useEffect(() => {
    const unlistenPromise = listen('transcription-chunk', (event) => {
      console.log('Nowy chunk:', event.payload);
    });
    return () => {
      unlistenPromise.then(unlisten => unlisten());
    };
  }, []);
  ```

---

## 🔐 Uprawnienia Systemowe i Bezpieczeństwo

### macOS (Uprawnienia do Mikrofonu, Kamery, Dostępności)
Dla aplikacji wymagających urządzeń audio (jak VoxBridgeAI) kluczowa jest konfiguracja `Info.plist` w `src-tauri/`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>NSMicrophoneUsageDescription</key>
    <string>Aplikacja wymaga dostępu do mikrofonu, aby rejestrować i tłumaczyć mowę w czasie rzeczywistym.</string>
</dict>
</plist>
```

### Tauri v2: System Capabilities i Permissions
W Tauri v2 uprawnienia definiuje się deklaratywnie w plikach JSON w `src-tauri/capabilities/default.json`:
```json
{
  "$schema": "../gen/schemas/desktop-schema.json",
  "identifier": "default",
  "description": "Domyślne uprawnienia aplikacji",
  "windows": ["main"],
  "permissions": [
    "core:default",
    "shell:allow-open",
    "dialog:default",
    "fs:default"
  ]
}
```

---

## ⚙️ Integracja z Next.js / Static Export

Aplikacja desktopowa Tauri wymaga statycznych zasobów HTML/CSS/JS (`devUrl` w trybie deweloperskim, `frontendDist` w produkcji).

W `next.config.js` / `next.config.mjs`:
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Wymóg Tauri: Czysty statyczny eksport HTML/JS/CSS (brak serwera Node.js na produkcji)
  output: 'export',
  images: {
    unoptimized: true, // Wymagane przy braku serwera Next.js
  },
  // Wyłączenie trailing slash dla zgodności z WebView
  trailingSlash: true,
};

export default nextConfig;
```

---

## 📦 Szybka Checklista przed Przygotowaniem Wydania (Tauri Release)
- [ ] Czy `next build` generuje folder `out/` bez błędów kompilacji SSR?
- [ ] Czy pole `frontendDist` (v2) lub `distDir` (v1) w `tauri.conf.json` wskazuje precyzyjnie na folder wyjściowy frontendu (`../out`)?
- [ ] Czy wersja w `tauri.conf.json`, `Cargo.toml` i `package.json` jest spójna i zgodna z SemVer?
- [ ] Czy na macOS zdefiniowano wymagane klucze `NSMicrophoneUsageDescription` w `Info.plist`?
- [ ] Czy komendy Rusta zarejestrowano w handlerze `invoke_handler(tauri::generate_handler![...])`?
- [ ] Czy budowanie binarne `cargo tauri build` kończy się sukcesem dla docelowej architektury?
