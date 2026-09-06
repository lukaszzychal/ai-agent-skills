---
name: tdd-scaffolder
description: "Technology-agnostic generator i architekt testów (TDD, Detroit vs London, AAA/GWT). Tworzy odporne, czyste testy jednostkowe, integracyjne i kontraktowe w dowolnym języku."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "tdd", "testing", "detroit-school", "london-school", "aaa", "gwt", "unit-tests", "clean-code"]
---

# TDD Scaffolder & Test Architect
> **Autor / Twórca:** Łukasz / Lukasz Zychal

Ten skill służy do **projektowania, rusztowania (scaffoldingu) i wdrażania testów** w dowolnym projekcie, niezależnie od języka i technologii (Python, TypeScript/JavaScript, Rust, Go, PHP, Java, C#). Pomaga dobrać odpowiednią szkołę testowania, strukturę testów i uniknąć kruchych, pozornych testów (brittle tests).

---

## 🎯 Kiedy aktywować ten skill?
- Użytkownik prosi o: "napisz testy do tego kodu", "zaprojektuj testy TDD", "jak przetestować ten serwis/komponent", "napisz testy w stylu Detroit / London".
- Tworzenie nowej funkcjonalności od podstaw w podejściu TDD (Red -> Green -> Refactor).
- Refaktoryzacja istniejących testów, które są kruche (łamią się przy byle zmianie wewnętrznej implementacji).

---

## 🧭 Wybór Szkoły Testowania: Detroit vs Londyn

| Kryterium | Szkoła Klasyczna (Detroit / Chicago / Bottom-Up) | Szkoła Makietowa (Londyńska / Mockist / Top-Down) |
| :--- | :--- | :--- |
| **Główny cel** | Weryfikacja stanu i rzeczywistego rezultatu działania | Weryfikacja ról, kontraktów i interakcji między obiektami |
| **Podejście** | Czarnoskrzynkowe (Black-box) | Biało/szaroskrzynkowe (Interaction testing) |
| **Użycie Mocków** | **Minimalne** – tylko dla zewnętrznych granic systemu (I/O, sieć, czas, baza) | **Intensywne** – mockowane są wszystkie zależności bezpośrednie (collaborators) |
| **Odporność na refaktoring** | **Bardzo wysoka** – zmiana wnętrza klasy nie psuje testu, dopóki wynik jest poprawny | **Umiarkowana/Niska** – refaktoring metod wewnętrznych może złamać oczekiwania mocka |
| **Kiedy stosować? (Rekomendacja)** | - Logika domenowa, reguły biznesowe, silniki obliczeniowe<br>- Czyste funkcje, parsery, transformacje danych<br>- Value Objects, Agregaty w DDD | - Koordynatory procesów, Orchestratory, Fasady<br>- Warstwa integracji (np. czy wysłano e-mail / event do brokera)<br>- Projektowanie architektury z góry do dołu (Outside-In) |

> [!IMPORTANT]
> **Złota reguła Pragmatyzmu:** Domyślnie preferuj **Styl Klasyczny (Detroit)** dla logiki biznesowej. Stosuj **Styl Londyński** tylko na granicach integracyjnych i w orchestratorach. Nigdy nie twórz "testów pozornych", w których mockujesz wszystko i sprawdzasz jedynie, czy mock został wywołany bez asercji stanu końcowego!

---

## 📐 Żelazna Struktura Testu: AAA / GWT

Każdy test bez wyjątku MUSI posiadać czytelnie wydzielone 3 fazy:

### Wariant 1: AAA (Arrange - Act - Assert)
```
// 1. Arrange: Przygotowanie danych wejściowych, stanu początkowego i zależności
// 2. Act: Wykonanie pojedynczej, testowanej operacji
// 3. Assert: Weryfikacja oczekiwanych rezultatów i stanu końcowego
```

### Wariant 2: GWT (Given - When - Then) – preferowany dla BDD / specyfikacji domenowych
```
// Given: Warunki początkowe kontekstu biznesowego
// When: Wystąpienie zdarzenia / wywołanie akcji
// Then: Oczekiwany skutek biznesowy i zmiana stanu
```

---

## 🚫 Antywzorce Testowania (Czego BEZWZGLĘDNIE unikać)

1. **Mockowanie danych zamiast logiki:** Mockowanie modeli domenowych lub DTO zamiast użycia prawdziwych instancji.
2. **Brittle Mocks (Kruche mocki):** Weryfikowanie dokładnej kolejności i parametrów prywatnych wywołań, które są detalem implementacyjnym.
3. **Puste asercje:** Testy z asercją typu `expect(result).toBeDefined()` lub `assert service is not None` zamiast sprawdzenia konkretnych wartości.
4. **Logika warunkowa w testach:** Instrukcje `if/else`, pętle `for` czy bloki `try/catch` w testach zaciemniają intencję. Test ma być sekwencyjny i liniowy.
5. **Współdzielony stan (Shared Mutable State):** Stan wyciekający między testami przez zmienne globalne lub singletony. Każdy test musi być w 100% izolowany.

---

## 🛠️ Szablony Implementacji w Różnych Językach

### 1. TypeScript / JavaScript (Vitest / Jest)

#### Styl Klasyczny (Detroit) – Test Logiki Domenowej
```typescript
import { describe, it, expect } from 'vitest';
import { ShoppingCart } from './ShoppingCart';

describe('ShoppingCart (Detroit Style)', () => {
  it('should calculate total with applied volume discount', () => {
    // Arrange (Given)
    const cart = new ShoppingCart();
    cart.addItem({ id: 'prod-1', price: 100, quantity: 5 });

    // Act (When)
    const total = cart.calculateTotal();

    // Assert (Then)
    // 5 * 100 = 500, rabat 10% przy >= 5 sztukach = 450
    expect(total).toBe(450);
  });
});
```

#### Styl Londyński (London) – Test Orchestratora z Mockami
```typescript
import { describe, it, expect, vi } from 'vitest';
import { CheckoutService } from './CheckoutService';
import type { PaymentGateway, NotificationService } from './ports';

describe('CheckoutService (London Style)', () => {
  it('should charge payment gateway and send receipt upon order confirmation', async () => {
    // Arrange (Given)
    const paymentGatewayMock: PaymentGateway = {
      charge: vi.fn().mockResolvedValue({ success: true, transactionId: 'tx-123' })
    };
    const notificationMock: NotificationService = {
      sendReceipt: vi.fn().mockResolvedValue(undefined)
    };
    const checkout = new CheckoutService(paymentGatewayMock, notificationMock);

    // Act (When)
    const result = await checkout.processOrder({ orderId: 'ord-1', amount: 450 });

    // Assert (Then)
    expect(paymentGatewayMock.charge).toHaveBeenCalledWith('ord-1', 450);
    expect(notificationMock.sendReceipt).toHaveBeenCalledWith('ord-1', 'tx-123');
    expect(result.status).toBe('COMPLETED');
  });
});
```

---

### 2. Python (pytest)

#### Styl Klasyczny (Detroit)
```python
import pytest
from core.circuit_breaker import CircuitBreaker

def test_circuit_breaker_opens_after_reaching_failure_threshold():
    # Arrange (Given)
    cb = CircuitBreaker(failure_threshold=3, cooldown_seconds=60)

    # Act (When)
    cb.record_failure()
    cb.record_failure()
    cb.record_failure()

    # Assert (Then)
    assert cb.can_execute() is False
    assert cb.state == "OPEN"
```

#### Styl Londyński (London)
```python
from unittest.mock import AsyncMock
import pytest
from services.order_service import OrderNotifier

@pytest.mark.asyncio
async def test_order_notifier_publishes_event_to_message_bus():
    # Arrange (Given)
    mock_bus = AsyncMock()
    notifier = OrderNotifier(message_bus=mock_bus)

    # Act (When)
    await notifier.notify_order_created(order_id="ord-99")

    # Assert (Then)
    mock_bus.publish.assert_awaited_once_with(
        topic="orders.created", 
        payload={"order_id": "ord-99"}
    )
```

---

### 3. Rust (`cargo test`)
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn should_calculate_session_duration_correctly() {
        // Arrange
        let mut session = Session::new("user-1");
        session.record_activity(Timestamp::from_secs(10));
        session.record_activity(Timestamp::from_secs(25));

        // Act
        let duration = session.total_active_seconds();

        // Assert
        assert_eq!(duration, 15);
    }
}
```

---

## 📋 Checklista Jakościowa Nowego Testu
Przed zaakceptowaniem testu zweryfikuj:
- [ ] Czy nazwa testu czyta się jak specyfikacja (`should_..._when_...` lub `test_...`)?
- [ ] Czy sekcje AAA / GWT są wizualnie odseparowane komentarzami lub pustą linią?
- [ ] Czy test jest niezależny i może być uruchomiony w losowej kolejności z innymi?
- [ ] Czy test sprawdza sytuacje brzegowe (pusta lista, błąd sieci, timeout, wartości skrajne)?
- [ ] Czy w przypadku niepowodzenia komunikat błędu precyzyjnie wskaże, co i dlaczego zawiodło?
