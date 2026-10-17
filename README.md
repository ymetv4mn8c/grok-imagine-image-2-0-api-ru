# Grok Imagine Image 2.0 API (`grok-imagine-image-2.0`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **from $0.032 per image** (1K@low $0.032 · 1K@medium $0.048 · 2K@low $0.048) — одна OpenAI-совместимая конечная точка на `https://api.apimart.ai/v1`, ежемесячный тариф не нужен. *(данные на 2026-10-12)*

**[Получить API-ключ](https://go.apimart.ai/k-f418f5)** · **[Актуальные цены](https://go.apimart.ai/k-eef693)** · **[Страница модели](https://go.apimart.ai/k-1f8886)** · [⚡ Быстрый старт за 60 секунд](#quickstart)

**Почему команды используют Grok Imagine Image 2.0 (`grok-imagine-image-2.0`) через APIMart**

- **Один ключ — весь каталог.** Тот же base URL `https://api.apimart.ai/v1` и тот же заголовок `Authorization` дают доступ к Grok Imagine Image 2.0 (`grok-imagine-image-2.0`) и ещё 300+ моделям для изображений, видео и языка — менять нужно поле `model`, а не клиент.
- **Минимум $1, оплата по факту.** Ни подписки, ни предоплаченного тарифа, который нужно заранее рассчитать: пополняйте от $1 и расходуйте на вызовы.
- **Сумма списания возвращается в ответе.** Каждая завершённая задача сообщает списанную сумму (`cost` / `credits_cost`); задача со статусом `failed` возвращается на баланс.
- **Асинхронность по умолчанию.** Отправьте задачу, получите `task_id`, опрашивайте `GET /v1/tasks/{id}` — пакетная обработка и повторы это обычная работа с очередью, а не отдельная интеграция.

<!-- /conv-kit:v1 -->

**Также известен как:** Грок Имаджин

Grok Imagine Image 2.0 is xAI's higher-fidelity image route, priced per image by resolution and quality.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Per-image | `grok-imagine-image-2.0` | per delivered image, by resolution | see the [model page](https://go.apimart.ai/k-1f8886) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/images/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| 1K@low | 0.04 | 0.032 |
| 1K@medium | 0.06 | 0.048 |
| 2K@low | 0.06 | 0.048 |
| 2K@medium | 0.08 | 0.064 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-eef693) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | what to generate, or what to change when editing |
| `size` | string | `auto` / `1:1` | aspect ratio, or `auto` where the route supports it |
| `resolution` | string | `1K` | output resolution tier — cost scales with it |
| `n` | integer | `1` | number of images per task, where the route allows more than one |
| `image_urls` | string[] | — | reference images as public HTTP(S) URLs or base64 data URLs |
| `nsfw_check` | boolean | `false` | runs `omni-moderation-latest` before submitting |

The table lists the fields accepted by the APIMart `images/generations` endpoint. Check the [model page](https://go.apimart.ai/k-1f8886) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"grok-imagine-image-2.0","prompt":"A bamboo forest at sunrise, slow push-in","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "grok-imagine-image-2.0", "prompt": "A bamboo forest at sunrise, slow push-in",
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["images"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Какой id у модели Grok Imagine Image 2.0?**

`grok-imagine-image-2.0`. Смотрите таблицу маршрутов выше.

**Как тарифицируется?**

**from $0.032 per image** (1K@low $0.032 · 1K@medium $0.048 · 2K@low $0.048). Завершённая задача указывает точную сумму в поле `cost`; неудачная задача возвращается на баланс.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-f418f5) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [grok-imagine-image-2-0-prompts-ru](https://github.com/ymetv4mn8c/grok-imagine-image-2-0-prompts-ru)
- **Same model, other languages** — 10 more language builds: English `grok-imagine-image-2-0-api` · 中文 `grok-imagine-image-2-0-api-cn` · Deutsch [grok-imagine-image-2-0-api-de](https://github.com/lrj-zpge/grok-imagine-image-2-0-api-de) · Español [grok-imagine-image-2-0-api-es](https://github.com/f6w6tvp/grok-imagine-image-2-0-api-es) · Français `grok-imagine-image-2-0-api-fr` · Relay/gateway [grok-imagine-image-2-0-api-gateway](https://github.com/voj2iwdw0e/grok-imagine-image-2-0-api-gateway) · Bahasa [grok-imagine-image-2-0-api-id](https://github.com/hhcq3trx-6/grok-imagine-image-2-0-api-id) · 日本語 `grok-imagine-image-2-0-api-ja` · 한국어 `grok-imagine-image-2-0-api-ko` · Português [grok-imagine-image-2-0-api-pt](https://github.com/jkk4bhqwsq/grok-imagine-image-2-0-api-pt)
- **More models in this language** — [midjourney-api-ru](https://github.com/sfpxvicw/midjourney-api-ru) · [gemini-2-5-flash-image-preview-api-ru](https://github.com/zwtdva8/gemini-2-5-flash-image-preview-api-ru) · [nano-banana-api-ru](https://github.com/ggpbi40ss/nano-banana-api-ru) · [gemini-3-1-flash-image-preview-api-ru](https://github.com/lu60av/gemini-3-1-flash-image-preview-api-ru) · [nano-banana-2-api-ru](https://github.com/jnp3hlry/nano-banana-2-api-ru) · [gemini-3-pro-image-preview-api-ru](https://github.com/dbxz24/gemini-3-pro-image-preview-api-ru)
- **Topic hubs** — every build of this model, all languages: [grok-imagine-image-2-0](https://github.com/topics/grok-imagine-image-2-0) · [grok-imagine-image-2-0-api](https://github.com/topics/grok-imagine-image-2-0-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-1f8886> | `apimart.ai/ru/model/grok-imagine-2-0` |
| Live pricing | <https://go.apimart.ai/k-eef693> | `apimart.ai/ru/pricing` |
| Get an API key | <https://go.apimart.ai/k-f418f5> | `apimart.ai/ru/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Grok Imagine Image 2.0 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
