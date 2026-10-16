# Seedream 4.5 API (`seedream-4-5`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **from $0.026 per image** — um único endpoint compatível com OpenAI em `https://api.apimart.ai/v1`, sem plano mensal. *(observado em 2026-10-12)*

**[Obter uma chave de API](https://go.apimart.ai/k-bcbbdf)** · **[Preços ao vivo](https://go.apimart.ai/k-33c925)** · **[Página do modelo](https://go.apimart.ai/k-2162ae)** · [⚡ Início rápido em 60 segundos](#quickstart)

**Por que os times usam Seedream 4.5 (`seedream-4-5`) via APIMart**

- **Uma chave, o catálogo inteiro.** A mesma base URL `https://api.apimart.ai/v1` e o mesmo cabeçalho `Authorization` alcançam Seedream 4.5 (`seedream-4-5`) e mais de 300 modelos de imagem, vídeo e linguagem — troque o campo `model`, não o seu cliente.
- **Mínimo de $1, pague conforme o uso.** Sem assinatura e sem plano pré-pago para dimensionar antes: recarregue a partir de $1 e gaste em chamadas.
- **A cobrança volta na resposta.** Toda tarefa concluída informa o valor cobrado (`cost` / `credits_cost`); uma tarefa marcada como `failed` é reembolsada.
- **Assíncrono por design.** Envie, pegue o `task_id`, consulte `GET /v1/tasks/{id}` — batching e retries são trabalho comum de fila, não uma integração sob medida.

<!-- /conv-kit:v1 -->

**Também conhecido como:** Dreamina

Seedream 4.5 delivers ByteDance's 4K text-to-image generation with precise text rendering, multi-reference style fusion and unified editing.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Per-image | `seedream-4-5` | per delivered image, by resolution | see the [model page](https://go.apimart.ai/k-2162ae) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/images/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| default | 0.0325 | 0.026 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-33c925) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

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

The table lists the fields accepted by the APIMart `images/generations` endpoint. Check the [model page](https://go.apimart.ai/k-2162ae) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"seedream-4-5","prompt":"A bamboo forest at sunrise, slow push-in","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "seedream-4-5", "prompt": "A bamboo forest at sunrise, slow push-in",
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

**Qual é o id do modelo Seedream 4.5?**

`seedream-4-5`. Veja a tabela de rotas acima.

**Como é a cobrança?**

**from $0.026 per image**. A tarefa concluída informa o valor exato no campo `cost`; uma tarefa com falha é reembolsada.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-bcbbdf) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [seedream-4-5-prompts-pt](https://github.com/d4qb-s4y087m/seedream-4-5-prompts-pt)
- **Same model, other languages** — 9 more language builds: 中文 [seedream-4-5-api-cn](https://github.com/etcg3hzbrw4d/seedream-4-5-api-cn) · Deutsch [seedream-4-5-api-de](https://github.com/q3zkzqq3f/seedream-4-5-api-de) · Español [seedream-4-5-api-es](https://github.com/kxoboq/seedream-4-5-api-es) · Français [seedream-4-5-api-fr](https://github.com/nhpm6vg83se/seedream-4-5-api-fr) · Relay/gateway `seedream-4-5-api-gateway` · Bahasa `seedream-4-5-api-id` · 日本語 `seedream-4-5-api-ja` · 한국어 [seedream-4-5-api-ko](https://github.com/ucy7hfyqm/seedream-4-5-api-ko) · Русский [seedream-4-5-api-ru](https://github.com/f3i936/seedream-4-5-api-ru)
- **More models in this language** — [midjourney-api-pt](https://github.com/l-ca0/midjourney-api-pt) · [gemini-2-5-flash-image-preview-api-pt](https://github.com/ycx8l0/gemini-2-5-flash-image-preview-api-pt) · [nano-banana-api-pt](https://github.com/erujt71i1lx/nano-banana-api-pt) · [gemini-3-1-flash-image-preview-api-pt](https://github.com/hutjwux0nt9h/gemini-3-1-flash-image-preview-api-pt) · [nano-banana-2-api-pt](https://github.com/ydkd8kdkl8n/nano-banana-2-api-pt) · [gemini-3-pro-image-preview-api-pt](https://github.com/qss2d-66qb2n7/gemini-3-pro-image-preview-api-pt)
- **Topic hubs** — every build of this model, all languages: [seedream-4-5](https://github.com/topics/seedream-4-5) · [seedream-4-5-api](https://github.com/topics/seedream-4-5-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-2162ae> | `apimart.ai/pt/model/seedream-4-5` |
| Live pricing | <https://go.apimart.ai/k-33c925> | `apimart.ai/pt/pricing` |
| Get an API key | <https://go.apimart.ai/k-bcbbdf> | `apimart.ai/pt/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedream 4.5 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
