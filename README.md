# CSHAPPY — Provably Fair Verifier

**[English](#english) · [Русский](#русский)**

A single-file, fully client-side verifier for CSHAPPY Provably Fair rolls (cases, upgrades, contracts). Paste the JSON of your roll and every step of the fairness math is recomputed **in your browser** — no server, no tracking, no libraries.

**Live:** https://cshappy-com.github.io/provably_fair/

---

## English

### What this is
`index.html` is a standalone page. Everything runs locally via the browser's built-in **WebCrypto** — the only network requests are for the skin icons (Steam CDN). You can also save the file and open it, or paste it into any online sandbox (JSFiddle / CodePen). It must be served over **https** (or `http://localhost`), which is what `crypto.subtle` requires.

### How to use
1. On the roll page at **cshappy.com** press **“Пересчитать в браузере”** (Recompute in browser) to copy the roll JSON.
2. Paste it into the field and press **Проверить** (Verify).
3. The page recomputes each step and shows whether it matches what the server committed.

Prefer to explore first? The **example buttons** (case / upgrade / contract / not-revealed / mismatch) load ready-made data.

### How the roll is computed (exact recipe, bit-for-bit as on the server)
1. **Public Hash** — `SHA-256(server_seed)` must equal the `server_seed_hash` published **before** the game.
2. **Roll** — `HMAC-SHA256(server_seed, "client_seed:nonce:cursor")` → read the 32 bytes as big-endian uint32 quads → reject any quad ≥ 4 294 000 000 (rejection sampling to stay uniform) → first accepted quad `% 1 000 000` is the **Roll** (0…999 999).
3. **Outcome** — each mechanic reads the Roll differently:
   - **Case**: the Roll lands in an item’s half-open interval `roll_from ≤ roll < roll_to`.
   - **Upgrade**: win if `roll < shown_chance_ppm`.
   - **Contract**: `cell = floor(roll × K / 1 000 000)` picks a value-multiplier from the committed table; payout = `all_price × ratio`.

The `server_seed` becomes public only **after** the version/epoch rotates. Until then the roll can’t be recomputed — that’s the commitment, not a bug.

### Trust model
Nothing here talks to CSHAPPY servers to “ask” if a roll was fair — it **proves** it from public values you already hold. Open source, no build step, no dependencies.

---

## Русский

### Что это
`index.html` — автономная страница. Вся криптография считается локально через встроенный в браузер **WebCrypto**; единственные внешние запросы — за иконками скинов (Steam-CDN). Файл можно сохранить и открыть, или вставить в любую онлайн-песочницу (JSFiddle / CodePen). Открывать нужно по **https** (или `http://localhost`) — этого требует `crypto.subtle`.

### Как пользоваться
1. На странице ролла на **cshappy.com** нажмите **«Пересчитать в браузере»** — JSON ролла скопируется в буфер.
2. Вставьте его в поле и нажмите **«Проверить»**.
3. Страница пересчитает каждый шаг и покажет, сходится ли он с тем, что сервер зафиксировал.

Хотите просто посмотреть? **Кнопки-примеры** (кейс / апгрейд / контракт / секрет не раскрыт / расхождение) подставят готовые данные.

### Как считается бросок (точный рецепт, бит-в-бит как на сервере)
1. **Public Hash** — `SHA-256(server_seed)` обязан совпасть с `server_seed_hash`, опубликованным **до** игры.
2. **Roll** — `HMAC-SHA256(server_seed, «client_seed:nonce:cursor»)` → 32 байта читаются четвёрками как big-endian uint32 → четвёрка ≥ 4 294 000 000 отбрасывается (rejection sampling ради равномерности) → первая принятая `% 1 000 000` и есть **Roll** (0…999 999).
3. **Исход** — механики читают Roll по-разному:
   - **Кейс**: Roll попадает в полуоткрытый интервал предмета `roll_from ≤ roll < roll_to`.
   - **Апгрейд**: победа, если `roll < shown_chance_ppm`.
   - **Контракт**: `ячейка = floor(roll × K / 1 000 000)` выбирает множитель ценности из зафиксированной таблицы; выплата = `сумма × множитель`.

`server_seed` становится публичным только **после** ротации версии/эпохи. До этого Roll пересчитать нельзя — это и есть коммитмент, а не ошибка.

### Модель доверия
Страница ничего не «спрашивает» у серверов CSHAPPY — она **доказывает** честность из публичных значений, которые у вас уже есть. Открытый исходник, без сборки и зависимостей.
