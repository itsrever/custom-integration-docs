# REVER Custom Integration — v2

This is the v2 specification of the HTTP contract between a custom ecommerce platform and the REVER returns & exchanges portal.

The ecommerce implements the endpoints below. REVER calls them.

> **Event notifications (webhooks) are out of scope for this spec.** REVER ships event notifications (return completed, refund executed, exchange order created, return cancelled, …) as standard webhooks documented separately at <https://docs.itsrever.com/apis/webhooks>. Integrators who need those events configure them through the webhook system.
>
> Already integrated against v1? See [../v1/README.md](../v1/README.md). v1 stays supported indefinitely. v2 is required only for the new capabilities listed in the [top-level README](../../README.md).
>
> New! The v2 specification is now public. It adds support for multi-currency, mixed refunds, product catalogs, and more. The contract is stable and ready for implementation.

---

## Table of contents

- [Conventions](#conventions)
- [Authentication](#authentication)
- [Versioning](#versioning)
- [Money](#money)
  - [Two currencies](#two-currencies)
  - [Taxes](#taxes)
- [Required endpoints](#required-endpoints)
  - [`GET /rever/v2/orders/{order_id}`](#get-reverv2ordersorder_id)
  - [`GET /rever/v2/orders?order_number={order_number}`](#get-reverv2ordersorder_numberorder_number)
- [Full catalog](#full-catalog)
  - [`GET /rever/v2/products`](#get-reverv2products)
  - [`GET /rever/v2/products/{product_id}`](#get-reverv2productsproduct_id)
  - [`GET /rever/v2/menu`](#get-reverv2menu)
- [Transactions](#transactions)
  - [`GET /rever/v2/orders/{order_id}/transactions`](#get-reverv2ordersorder_idtransactions)
- [Promocodes](#promocodes)
  - [`POST /rever/v2/promocodes`](#post-reverv2promocodes)
  - [`GET /rever/v2/promocodes/{code}`](#get-reverv2promocodescode)
  - [`DELETE /rever/v2/promocodes/{code}`](#delete-reverv2promocodescode)
- [Gift cards](#gift-cards)
  - [`POST /rever/v2/giftcards`](#post-reverv2giftcards)
  - [`GET /rever/v2/giftcards/{platform_id}`](#get-reverv2giftcardsplatform_id)
  - [`DELETE /rever/v2/giftcards/{platform_id}`](#delete-reverv2giftcardsplatform_id)
- [Exchanges](#exchanges)
  - [`POST /rever/v2/orders`](#post-reverv2orders)
- [Supported refund platforms](#supported-refund-platforms)

---

## Conventions

- **Content type**: `application/json; charset=utf-8` for all requests and responses.
- **Encoding**: UTF-8.
- **Casing**: snake_case for all JSON field names.
- **Dates**: ISO 8601 with timezone offset, e.g. `2026-03-21T10:15:30-04:00`.
- **IDs**: opaque strings — never assume numeric, even when they look numeric.
- **Optional fields**: may be omitted or sent as `null`. REVER treats both as "absent".
- **Required column in field tables** carries one of three values:
  - `yes` — the field must always be present and non-null.
  - `no` — the field is optional; sending it or omitting it is equally valid.
  - `conditional` — required in some contexts and optional in others. The exact rule is in the row's description (e.g. *"Required when `shop_currency ≠ customer_currency`"*, *"Required in catalog responses; optional under `line_item.product`"*). The OpenAPI schema marks these fields as plain optional — REVER enforces the conditional rule at runtime and rejects requests that violate it.
- **Enum casing** is split into two intentional buckets:
  - **`UPPER_SNAKE_CASE`** for abstract category enums — values that represent a state, kind, or category REVER's own code branches on. Examples: `Transaction.kind` (`SALE`, `CAPTURE`, …), `Transaction.status` (`SUCCESS`, `PENDING`, …), `LineFee.type` (`SHIPPING_FEE`, `HANDLING_FEE`, …), `non_returnable_reason` codes.
  - **`lowercase`** for platform / brand identifiers — values that mirror an external vendor name. Examples: `Transaction.platform_id` (`paypal`, `redsys`, `sequra`, `stripe`, `giftcard`, `manual`). These are open strings — new platforms can be added without a spec change.

  When you add a new value, follow the bucket: a new payment platform stays lowercase; a new fee type stays `UPPER_SNAKE_CASE`.
- **Unit symbols**: each measurement carries its unit. Allowed values are restricted — anything outside the lists below must be rejected or normalized ecommerce-side before being returned:
  - `weight.unit` ∈ [`kg`, `gr`, `lb`]
  - `dimensions.unit` ∈ [`m`, `cm`, `in`] (applies to all three lengths)
- **Localization (`lang` query param)**: REVER passes an optional `lang` query parameter on every `GET` endpoint that can return shopper-visible strings — `GET /orders/{order_id}`, `GET /orders?order_number=…`, `GET /products`, `GET /products/{product_id}`, and `GET /menu`. The value is a plain **ISO 639-1 two-letter language code** — `es`, `en`, `fr`...

## Authentication

All requests include this header:

```http
X-rever-api-key: <api_key>
```

The value is agreed with REVER at the start of the integration and shared via a one-time secret link. Endpoints reject any request missing or carrying a wrong key with `401 Unauthorized`.

## Versioning

All v2 endpoints live under `/rever/v2/...`.

Additive revisions are exposed under a separate prefix: `/rever/v2_1/...`, `/rever/v2_2/...`, etc. An ecommerce on `/rever/v2/` never has to upgrade — REVER continues calling that prefix as long as it is exposed. To opt in to a minor revision, the ecommerce exposes the newer prefix and notifies REVER, which then switches.

The contract for a minor revision (e.g. `v2_1`) is **strictly additive**:

- New endpoints may be introduced.
- New optional fields may be added to existing responses.
- No existing field may change type, name, or required-status.

Breaking changes are reserved for a new major version (`/rever/v3/`).

## Money

Every monetary value is a **decimal string** with `.` as decimal separator, no thousands separator, no currency symbol — e.g. `"19.99"`.

**Empty string means zero.** Any monetary field (`unit_discount`, `total_taxes`, any `line_fees[].*`, refund amounts, …) sent as `""` is treated as `"0.00"`. Either form is accepted on input; REVER's downstream calculations always use the numeric zero. The canonical form is `"0.00"` — prefer it when you can — but legacy systems that emit `""` for "not applicable" stay compatible.

### Two currencies

An order has two currencies, and every monetary field on the order body is implicitly in one of them — never both:

| Currency | Source field | Used by |
| --- | --- | --- |
| **Ecommerce currency** (shop currency) | `order.shop_currency` | Catalog & product prices: `Product.unit_price` (catalog responses) and every `Variant.unit_price` (whether nested under a catalog response or under an order line item's `product.variants[]`). Identical to `Product.currency` when set. |
| **Shopper currency** | `order.customer_currency` | Everything the customer was charged in: `line_items[]` (`unit_price`, `unit_discount`, `subtotal`, `total_taxes`, `total`), `line_fees[]` (`amount`, `discount`, `taxes`), the transactions returned by `GET /rever/v2/orders/{order_id}/transactions`, and every refund amount the ecommerce reports back. |

**The two currencies often coincide.** For domestic orders where the customer shops in the merchant's home currency, `shop_currency == customer_currency` and `exchange_rate` is `1.0` (or may be omitted — see below). Both currency fields are still sent — REVER never infers the shopper currency from the shop currency.

#### `order.exchange_rate`

The conversion factor between the order's two currencies, captured at purchase time. It applies to the whole order — independent of any line item.

**Formula** (this is the single authoritative definition; the OpenAPI uses the same one):

```text
customer_amount = shop_amount × exchange_rate
```

In other words, multiply a shop-currency amount by `exchange_rate` to get the equivalent amount in customer currency.

**Worked example.** Merchant books in EUR (`shop_currency = "EUR"`), customer paid in USD (`customer_currency = "USD"`). At purchase time `1 EUR = 1.10 USD`, so `exchange_rate = 1.10`. A product whose `unit_price` is `"20.00"` (EUR, shop-side) was charged to the customer at `"22.00"` USD = `20.00 × 1.10`.

**Required-ness:**

- When `shop_currency ≠ customer_currency`: **required, non-zero**.
- When `shop_currency == customer_currency`: optional. Omitted ⇒ REVER treats it as `1.0`. Sending `1.0` explicitly is also valid.

REVER will reject orders where `shop_currency ≠ customer_currency` and `exchange_rate` is missing or `0`.

### Taxes

**Taxes are always added on top of `subtotal` to produce `total`.** `subtotal` is net of discounts and pre-tax — never tax-inclusive. The per-line invariant below must always hold; if the ecommerce's internal prices already include tax, extract the tax and report it explicitly on `total_taxes` before sending.

```text
subtotal = (unit_price - unit_discount) * quantity
total    = subtotal + total_taxes
```

`total_taxes` appears at two levels — both in **shopper currency**:

- Each `line_item.total_taxes` (taxes on the line, on top of `subtotal`).
- Each `line_fees[].taxes` (taxes on the corresponding fee).

The order itself has no aggregated `total_taxes` field — sum the line items and every entry in `line_fees` when you need the total.

---

## Required endpoints

These endpoints are required for any ecommerce integrating against v2.

### `GET /rever/v2/orders/{order_id}`

Retrieves an order by its internal id. Called at multiple points in the return flow (validation, label generation, refund execution).

**Path parameters**

| Name | Type | Description |
| --- | --- | --- |
| `order_id` | string | The internal order id, as previously communicated by the ecommerce. |

**Query parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `lang` | string | no | Shopper locale hint — ISO 639-1 two-letter code (`es`, `en`, …). See [Conventions](#conventions). |

**Response 200** — the order body:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `order_id` | string | yes | Internal id of the order. |
| `order_number` | string | yes | The order identifier shown to the customer (without prefix or suffix — see v1 note). |
| `shop_currency` | string | yes | **Ecommerce currency** (ISO 4217). The currency the merchant keeps its books and prices its catalog in. |
| `customer_currency` | string | yes | **Shopper currency** (ISO 4217). The currency the customer was charged in. Source of truth for all monetary values across line items and line fees. |
| `exchange_rate` | number | conditional | `customer_amount = shop_amount × exchange_rate`. Single number for the whole order. **Required and non-zero when `shop_currency ≠ customer_currency`**; otherwise optional (omission ⇒ `1.0`). See [Money › `order.exchange_rate`](#orderexchange_rate). |
| `tags` | string[] | no | Order tags (used by REVER for dynamic warehouses and segmentation). |
| `categories` | string[] | no | Order categories. |
| `customer_info` | object | yes | See [Customer info](#customer-info). |
| `shipping_address` | object | yes | See [Address](#address). |
| `billing_address` | object | no | See [Address](#address). When omitted, REVER falls back to `shipping_address` for invoicing. |
| `line_items` | object[] | yes | See [Line item](#line-item). |
| `line_fees` | object[] | yes | Array of order-level fees (shipping, handling, insurance, …). May be empty. See [Line fee](#line-fee). |
| `purchased_at` | string | yes | ISO 8601 with timezone. |
| `fulfilled_at` | string | yes | ISO 8601 with timezone. May equal `purchased_at` if the ecommerce doesn't distinguish. |

#### Customer info

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `email` | string | yes | Customer email. |
| `first_name` | string | yes | |
| `last_name` | string | yes | |
| `phone` | string | yes | E.164 with country code preferred. |

#### Address

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `first_name` | string | no | Defaults to `customer_info.first_name` if absent. |
| `last_name` | string | no | Defaults to `customer_info.last_name`. |
| `email` | string | no | Defaults to `customer_info.email`. |
| `phone` | string | no | Defaults to `customer_info.phone`. |
| `address_line_1` | string | yes | Up to and including house number. |
| `address_line_2` | string | no | Flat/door number, etc. |
| `postal_code` | string | yes | |
| `city` | string | yes | |
| `province_state` | string | no | Display name of the province / state / region, e.g. `Barcelona`, `Ohio`, `Île-de-France`. Free-form text. Omit when the country has no meaningful subdivision (or when the ecommerce does not capture one). |
| `country_code` | string | yes | ISO 3166-1 alpha-2. |
| `country` | string | yes | Country name. |

#### Line item

`line_item.unit_price` is always authoritative — it's the price the customer was actually charged. The variant/product prices nested under `line_item.product` (for example `line_item.product.variants[].unit_price`) are for context only — REVER uses them to rank exchange suggestions, never to compute what was paid for this line.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | yes | Unique line item id within the order. **Must be stable across calls** — REVER uses it to correlate the same line item between repeated `GET /orders` lookups (validation, label generation, refund execution), so the same line item must always come back with the same `id`. |
| `variant_id` | string | no | Variant identifier. Omit when the parent product has no variants (single-SKU product). REVER resolves both the variant's display name and SKU from `product.variants[]` using this id. |
| `quantity` | integer | yes | Units of this product in the order. |
| `unit_price` | string | yes | Price of one unit, pre-discount and pre-tax. Decimal string. |
| `unit_discount` | string | no | Discount applied per unit, in the same currency as `unit_price` — an **absolute amount**, not a percentage. Subtracted from `unit_price` before tax. |
| `subtotal` | string | yes | Net of discount, pre-tax: `(unit_price - unit_discount) * quantity`. |
| `total_taxes` | string | yes | Taxes on this line, added on top of `subtotal`. |
| `total` | string | yes | Final amount paid for this line: `subtotal + total_taxes`. |
| `weight` | object | no | `{ "value": "240", "unit": "gr" }`. `unit` ∈ [`kg`, `gr`, `lb`]. |
| `dimensions` | object | no | `{ "length": "30", "width": "20", "height": "10", "unit": "cm" }`. `unit` ∈ [`m`, `cm`, `in`]. |
| `is_returnable` | boolean | yes | If `false`, all units in this line item are non-returnable. |
| `non_returnable_reason` | integer | no | `2` already returned · `3` policy · `4` not yet fulfilled · `5` out of return window (REVER also checks this internally). |
| `product` | object | yes | See [Product](#product). |

#### Product

The same `Product` schema is used in two places: nested under `line_item.product` when reading an order, and as the entries of `GET /products` / `GET /products/{product_id}` responses.

**Physical attributes precedence.** `unit_price`, `weight`, and `dimensions` at the product level apply **only when the product has no variants** (single-SKU). When variants are present, each variant's copy of these fields is authoritative and the product-level ones are ignored. They may still be sent at the product level for convenience, but REVER will consult the variants.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | yes | Product id. |
| `name` | string | yes | Localized — return in the language requested by the `lang` query parameter when supported, falling back to the ecommerce's canonical language otherwise. |
| `description` | string | yes | Localized — same rule as `name`. |
| `short_description` | string | no | Localized — same rule as `name`. |
| `sku` | string | no | Product-level SKU. A merchant identifier that groups all variants of the product under one reference. Variants additionally carry their own per-variant `sku`. |
| `unit_price` | string | conditional | Minimum unit price across variants when there are multiple; otherwise the single price of the product. Decimal string in the ecommerce currency declared by `currency`. **Required in catalog responses** (`GET /products`, `GET /products/{product_id}`); optional when the product is nested under `line_item.product` — the variants on the order already carry that information. **Ignored when variants are present** (see precedence rule above). |
| `currency` | string | conditional | ISO 4217. The ecommerce currency every price under this product (`unit_price` and every `variants[].unit_price`) is denominated in. **Must match `order.shop_currency`** — products and variants are always priced in the merchant's home currency. Required-ness mirrors `unit_price`: required in catalog responses, optional under `line_item.product`. |
| `images` | object[] | yes | At least one. See [Image](#image). |
| `categories` | string[] | no | Taxonomy ids the product belongs to. Used by dynamic warehouses. |
| `tags` | string[] | no | |
| `collections` | string[] | no | Collection ids (typically used for menu sections). |
| `variants` | object[] | no | Used for exchanges. If products have no variants, the exchange 1:1 is not available. Full Catalog exchanges feature is enabled if you implement the GET products call. See [Variant](#variant). |
| `stock` | object \| null | no | Tracked inventory for **single-SKU products** (no variants). When the product has variants, stock lives on each entry in `variants[]` instead and this field must be omitted. See [Stock](#stock). |
| `weight` | object | no | Physical weight — `{ value, unit }`, `unit` ∈ [`kg`, `gr`, `lb`]. **Ignored when variants are present** (each variant carries its own `weight`). |
| `dimensions` | object | no | Physical bounding box — `{ length, width, height, unit }`, `unit` ∈ [`m`, `cm`, `in`]. **Ignored when variants are present** (each variant carries its own `dimensions`). |
| `updated_at` | string | no | ISO 8601. Last time the product was modified. Optional — REVER uses it to know how fresh the data is but does not require it. |

#### Variant

A specific purchasable combination of the product's options — e.g. `XS / Green` is the variant where `Size = XS` and `Color = Green`. Only include variants the customer may choose for the exchange; filter out discontinued, region-blocked, compliance-restricted, or otherwise unavailable variants server-side rather than emitting them with a disabled flag.

`unit_price` is in the **ecommerce currency** declared by the enclosing [Product](#product)'s `currency`, which always matches `order.shop_currency`. The field name matches `LineItem.unit_price` so the same vocabulary is used everywhere a unit price appears.

**Physical attributes override the parent product.** When the product has variants, each variant's `unit_price`, `weight`, and `dimensions` are authoritative — the matching fields on the parent [Product](#product) are ignored. When the product has **no** variants, the parent's copies of those fields are used instead. (`dimensions` here is the **physical bounding box** — the same shape used on `line_item.dimensions` and `Product.dimensions`. The variant-defining options like Size/Color live in the separate `options` field below.)

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | yes | |
| `name` | string | yes | Human-readable label. Convention: join the option values, e.g. `XS / Green`. |
| `description` | string | no | |
| `unit_price` | string | yes | Decimal string in ecommerce currency. Same field name as on order line items. |
| `sku` | string | no | Per-variant SKU. |
| `options` | object[] | no | One entry **per option dimension** along which the parent product is sold (Size, Color, Material, …). Empty (or omitted) for single-SKU products. See [Variant option](#variant-option). |
| `stock` | object \| null | no | Tracked inventory for this variant. When the parent product has variants, stock lives here (one per variant) and `Product.stock` must be omitted. See [Stock](#stock). |
| `weight` | object | no | Same shape as on the line item — `{ value, unit }`, `unit` ∈ [`kg`, `gr`, `lb`]. |
| `dimensions` | object | no | Physical bounding box of the shipped variant — `{ length, width, height, unit }`, `unit` ∈ [`m`, `cm`, `in`]. Same shape as on the line item and on `Product.dimensions`. |

#### Variant option

A `VariantOption` ties this variant to a specific value along one of the product's option dimensions. A variant whose product is sold along two options (Size + Color) carries two entries — e.g. `[{id:"Size", name:"Size", value:"XS"}, {id:"Color", name:"Color", value:"Green"}]` represents `XS / Green`.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | yes | Stable identifier for the option, e.g. `Size`. Stays stable across languages while `name` may be localized. |
| `name` | string | yes | Display name of the option in the requested language, e.g. `Size`, `Talla`. |
| `value` | string | yes | This variant's value for the option, e.g. `XS`. |

#### Image

A single image reference, used inside `product.images[]`.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `url` | string | yes | Publicly reachable URL of the image. |
| `alt_text` | string | no | Accessibility text describing the image (rendered as `alt` on `<img>` tags). |

#### Stock

Tracked inventory. Lives in **exactly one place** per product, depending on whether the product has variants:

- **Product with variants** → stock goes on each `Variant.stock`. `Product.stock` must be omitted.
- **Product without variants** (single-SKU) → stock goes on `Product.stock`. There are no variants to attach it to.

The whole object is opt-in — omit it (or send `null`) when the ecommerce does not track inventory for that product/variant. When the object is present, `quantity` is authoritative and `quantity: 0` unambiguously means "out of stock" (distinct from "untracked / unknown").

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `quantity` | integer | yes (when `stock` is present) | Sellable units available right now. Must be `>= 0`. |

> **Stock contract.** When `stock` is omitted, REVER treats the product/variant as having unlimited inventory (e.g. made-to-order, print-on-demand, digital goods). When `stock` is present with `quantity > 0`, it is in stock; when `quantity = 0`, it is out of stock and REVER will not propose it as an exchange. `stock.quantity` is also used to rank exchange suggestions and to render "X left" hints in the portal.

#### Line fee

`order.line_fees` is an **array** of `LineFee`. Each entry is one order-level fee on top of the line items. All amounts in **shopper currency** (`order.customer_currency`).

Net contribution per fee = `amount - discount + taxes`. REVER sums every entry into the order total, **regardless of `type`**. The array may be empty when an order has no fees at all.

Today REVER only interprets `SHIPPING_FEE` specifically (for return-label generation, free-shipping policies, …). Other types still belong in the array because they contribute to the total, but REVER does not act on them individually. `SHIPPING_FEE` is **optional** — orders with no shipping cost (digital goods, free delivery promotion absorbed elsewhere, …) simply do not include a `SHIPPING_FEE` entry.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | string | yes | Fee type. Open string so new categories can appear without a spec change. Common values: `SHIPPING_FEE` (the only type REVER acts on today), `HANDLING_FEE`, `INSURANCE_FEE`, `OTHER`. |
| `amount` | string | yes | Gross fee charged to the customer. |
| `discount` | string | yes | Discount applied to this fee. `"0.00"` when none. |
| `taxes` | string | yes | Taxes on this fee, on top of `amount - discount`. |

#### Example

See [examples/get-order.json](examples/get-order.json).

#### Error handling

| Status | When |
| --- | --- |
| `200` | Order found, body returned. |
| `401` | `X-rever-api-key` missing or invalid. |
| `404` | The `order_id` does not exist. Body should describe the failure. |
| `5xx` | Ecommerce platform unavailable / internal error. |

### `GET /rever/v2/orders?order_number={order_number}`

Returns **one** order (the same response body as `GET /rever/v2/orders/{order_id}`), looked up by the customer-facing `order_number`. Called when a shopper enters their order number in the REVER portal. Returns `404` if no order matches — there is no list / search behavior, even though the path lacks a trailing segment.

**Query parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `order_number` | string | yes | Customer-facing order number, **without** any prefix or suffix. If the customer typed `#12345`, REVER sends `12345`. |
| `lang` | string | no | Shopper locale hint — ISO 639-1 two-letter code (`es`, `en`, …). See [Conventions](#conventions). |

> **Heads up:** Notifications about returns, refunds, exchanges, and cancellations are delivered as **webhooks** rather than calls into the ecommerce. See <https://docs.itsrever.com/apis/webhooks>.

---

## Full catalog

REVER pulls the ecommerce's catalog periodically (default: daily, between 03:00 and 05:00 UTC). The catalog feeds product recommendations and exchange suggestions.

These endpoints are optional but strongly recommended. Without them, REVER can only suggest exchanges among the variants of the product the customer already purchased.

### `GET /rever/v2/products`

Paginated listing of every active product in the catalog. REVER walks `offset` from `0` upward in steps of `page_size` until `page_info.has_next_page` is `false`, then keeps the resulting product set as its mirror of the catalog. **Products that appear in an earlier sync but not in a subsequent one are treated as removed.** There is no separate "deleted" signal — omission is the signal.

**Query parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `offset` | integer | no | Zero-based index of the first product to return. Default `0`. REVER pages by adding `page_size` each call. |
| `page_size` | integer | no | Requested page size. Default `50`, max `100`. The ecommerce may serve a smaller page. |
| `lang` | string | no | Shopper locale hint — ISO 639-1 two-letter code (`es`, `en`, …). See [Conventions](#conventions). |

**Response 200**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `products` | object[] | yes | Up to `page_size` products. Each entry follows the unified [Product](#product) schema; `unit_price` and `currency` are required in this context. |
| `page_info` | object | yes | `{ "has_next_page": boolean, "total"?: integer }`. When `has_next_page` is `true`, the next call uses `offset = previous_offset + page_size`. |

Variants returned inside each product's `variants[]` follow the [Variant](#variant) schema. As in the order context, only surface-able variants belong in the response — filter the rest out server-side. See [Stock](#stock) for how inventory is signaled (on each variant when the product has variants, on `Product.stock` for single-SKU products).

#### Example

See [examples/get-products.json](examples/get-products.json).

### `GET /rever/v2/products/{product_id}`

Returns a single product with full variant detail. Used when REVER needs to refresh stock before showing it as an exchange option.

**Query parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `lang` | string | no | Shopper locale hint — ISO 639-1 two-letter code (`es`, `en`, …). See [Conventions](#conventions). |

**Response 200**

A single `product` body shaped like [Product](#product), with `unit_price` and `currency` populated and (when set) `updated_at`.

| Status | When |
| --- | --- |
| `200` | Product found. |
| `404` | Unknown `product_id`. |

### `GET /rever/v2/menu`

Returns the ecommerce's category hierarchy so REVER can render top-level menus and sub-menus during exchange selection.

**Query parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `country_code` | string | no | ISO 3166-1 alpha-2. Filters availability for the country when the ecommerce maintains country-specific menus. |
| `lang` | string | no | Shopper locale hint — ISO 639-1 two-letter code (`es`, `en`, …). See [Conventions](#conventions). |

**Response 200**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | yes | Menu id. |
| `items` | object[] | yes | Top-level menu items. Each item: see below. |

Per-item fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | yes | Menu node id. |
| `title` | string | yes | Display title in the requested language. |
| `resource_type` | string | yes | `COLLECTION` (a category / collection of products) · `CATALOG` (the full catalog) · `SEARCH` (a search query). |
| `resource_id` | string | yes | The id REVER uses to filter products by this category. |
| `children` | object[] | no | Sub-menu nodes — same shape, recursive. |

---

## Transactions

### `GET /rever/v2/orders/{order_id}/transactions`

Returns the list of payment transactions that funded the order. REVER calls this:

- Before starting a return, to know how much is refundable and via which payment method.
- Before issuing a refund, to confirm which `transaction_id` to target through the corresponding `platform_id`.

**Response 200**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `transactions` | object[] | yes | One entry per transaction. |

Per-transaction fields. `amount` values are normally in **shopper currency** (`order.customer_currency`), but each transaction carries an explicit `currency` so cross-currency payment processors (paying out in a currency that differs from what the customer was charged in) stay unambiguous.

Each transaction carries the two fields REVER needs to issue a refund through the same processor: `platform_id` (which platform) and `transaction_id` (the id REVER passes back to that platform). See [Supported refund platforms](#supported-refund-platforms) for the exact `transaction_id` format per platform.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | yes | Ecommerce-side transaction id — the merchant's own identifier for this transaction. |
| `kind` | string | yes | `SALE` · `CAPTURE` · `AUTHORIZATION` · `VOID` · `REFUND`. |
| `status` | string | yes | `SUCCESS` · `PENDING` · `FAILURE` · `ERROR`. |
| `platform_id` | string | yes | Payment platform that processed this leg. Common values: `paypal`, `redsys`, `sequra`, `stripe`, `giftcard`, `manual`. See [Supported refund platforms](#supported-refund-platforms). |
| `transaction_id` | string | yes | Platform-specific identifier REVER will use to refund this leg through `platform_id`. Format depends on the platform — see [Supported refund platforms](#supported-refund-platforms). |
| `amount` | string | yes | Decimal string in the currency declared by the sibling `currency`. |
| `currency` | string | yes | ISO 4217 currency of this transaction. Normally matches `order.customer_currency`. |
| `parent_id` | string | no | For `REFUND` transactions, the id of the `SALE`/`CAPTURE` being refunded. |
| `executed_at` | string | yes | ISO 8601. |

#### Example

See [examples/get-transactions.json](examples/get-transactions.json).

---

## Promocodes

> **Promocodes are not gift cards.** A promocode is a one-shot discount code that can be redeemed against a future purchase. Gift cards are a separate stored-value payment method with balance and partial redemption — see [Gift cards](#gift-cards).
>
> **Currency rule.** Promocodes are always issued in the **shopper currency** (`order.customer_currency`) of the source order. There is no shop-vs-customer split at issuance time — REVER sends a single `amount` + `currency` pair, and the ecommerce stores the promocode at that face value.

### `POST /rever/v2/promocodes`

REVER calls this when a customer has chosen "Promocode" as the refund method. The ecommerce creates and stores a promocode and returns the code that will be sent to the customer.

**Request body**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `ecommerce_id` | string | yes | |
| `idempotency_key` | string | yes | Deterministic key for this promocode issuance. REVER derives it as `"{ecommerce_id}:{return_id}:promocode"` and sends the same value on every retry (network error, timeout, `5xx`). The ecommerce **must** dedupe on it: a second call with the same key returns the originally-issued promocode (HTTP `200` with the existing `code` value) without creating a duplicate. |
| `order_number` | string | yes | Source order number — the order whose return is producing this promocode. |
| `amount` | string | yes | Face value of the promocode (maximum discount it can apply). Decimal string in the **shopper currency** of the source order (`order.customer_currency`). |
| `currency` | string | yes | ISO 4217. Must equal the source order's `customer_currency`. REVER will reject the request if the two do not match. |

**Response 200**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `code` | string | yes | The promocode value. Format `RV-{order_number}-{random}`, total length 15 characters. Same value used as the `{code}` path segment on `GET`/`DELETE /promocodes/{code}`. Mirrors `GiftCard.code` for naming symmetry. |

### `GET /rever/v2/promocodes/{code}`

Returns the current state of a promocode. Used by REVER to verify a code is still valid before applying it.

**Response 200**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `code` | string | yes | Echo of the promocode value (same as the path segment). |
| `amount` | string | yes | Face value of the promocode. Echoes the `amount` originally sent on `POST /promocodes`. |
| `currency` | string | yes | ISO 4217. Echoes the `currency` originally sent on `POST /promocodes` — same as the source order's `customer_currency`. |
| `enabled` | boolean | yes | `true` if the code can still be redeemed. |
| `used` | boolean | yes | `true` once the code has been redeemed (fully or partially). |
| `order_ids` | string[] | no | Orders against which this promocode has been redeemed. |

| Status | When |
| --- | --- |
| `200` | Promocode found. |
| `404` | Unknown code. |

### `DELETE /rever/v2/promocodes/{code}`

Disables / cancels a promocode. Used by REVER to invalidate codes when a return is reversed.

| Status | When |
| --- | --- |
| `204` | Cancellation accepted. No body. |
| `404` | Unknown code. |
| `409` | Already redeemed — cannot be cancelled. |

---

## Gift cards

> **Gift cards are stored-value payment methods.** Unlike promocodes, they:
>
> - Carry a **balance**.
> - Can be redeemed **partially** across multiple orders.
> - Are treated as a payment method (a gift-card top-up is a transaction that appears in [`GET /rever/v2/orders/{order_id}/transactions`](#get-reverv2ordersorder_idtransactions)).
> - May carry **taxes** at issuance, depending on jurisdiction.
>
> **Currency rule.** Gift cards are always issued in the **shopper currency** (`order.customer_currency`) of the source order. There is no shop-vs-customer split at issuance time — REVER sends a single `amount` + `currency` pair, and the ecommerce stores the gift card at that face value. The same currency is returned later on `GET /giftcards/{platform_id}`.

### `POST /rever/v2/giftcards`

Creates a gift card.

**Request body**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `ecommerce_id` | string | yes | |
| `idempotency_key` | string | yes | Deterministic key for this gift-card issuance. REVER derives it as `"{ecommerce_id}:{return_id}:giftcard"` and sends the same value on every retry (network error, timeout, `5xx`). The ecommerce **must** dedupe on it: a second call with the same key returns the originally-issued gift card (HTTP `200` with the existing `code` + `platform_id`) without creating a duplicate. |
| `order_number` | string | yes | Source order number — the order whose return is producing this gift card. |
| `amount` | string | yes | Initial face value. Decimal string in the **shopper currency** of the source order (`order.customer_currency`). |
| `currency` | string | yes | ISO 4217. Must equal the source order's `customer_currency`. REVER will reject the request if the two do not match. |
| `taxes` | string | no | Taxes already included in `amount`, in the same currency. `"0.00"` (or omit) when none. Empty string `""` is treated as `"0.00"`. |

**Response 200**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `code` | string | yes | Code given to the customer. |
| `platform_id` | string | yes | Ecommerce-side identifier — same value used as the path segment on `GET`/`DELETE /giftcards/{platform_id}`. |
| `expires_on` | string | no | ISO 8601. Absent when the gift card never expires. |

### `GET /rever/v2/giftcards/{platform_id}`

Returns the current state of a gift card.

**Response 200**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `platform_id` | string | yes | Ecommerce-side identifier for this gift card. Same value returned by `POST /giftcards` and used as the path segment on `GET`/`DELETE /giftcards/{platform_id}`. |
| `code` | string | yes | |
| `amount` | string | yes | Initial value. |
| `balance` | string | yes | Remaining balance. |
| `currency` | string | yes | Gift card currency (ISO 4217). A gift card has its own currency, set at issue time and independent of any order it later helps pay for. |
| `expires_on` | string | no | |

| Status | When |
| --- | --- |
| `200` | Found. |
| `404` | Unknown `platform_id`. |

### `DELETE /rever/v2/giftcards/{platform_id}`

Cancels a gift card. Remaining balance becomes zero.

| Status | When |
| --- | --- |
| `204` | Cancelled. |
| `404` | Unknown `platform_id`. |
| `409` | Already fully redeemed. |

---

## Exchanges

**Optional.** Implement this section only if the ecommerce wants to support exchanges through the REVER portal. Ecommerces that only handle refunds can skip the endpoint — REVER will simply not offer the exchange flow to their shoppers.

### `POST /rever/v2/orders`

REVER calls this when a customer completes an exchange in the portal. The ecommerce creates a new order on its platform with the variants the customer chose and returns its own internal identifiers.

**Idempotency**

The request carries an `idempotency_key`. REVER derives it deterministically:

```text
idempotency_key = "{ecommerce_id}:{return_id}:exchange"
```

If REVER must retry (network error, timeout, ecommerce returning `5xx`), it sends the **same** key. The ecommerce must:

1. Look up any existing order created with this key.
2. If found, return that order's response body **verbatim** — same `order_id`, same `order_number`, same `total`/`currency`, same `line_items`. Do not create a duplicate.
3. If not found, create the order, persist the key, and return the response body for the newly-created order.

REVER detects whether a call was a fresh create or a replay by matching `order_id` against the value it received on an earlier successful call — no separate flag is needed in the response body.

> **Deduplication is the ecommerce's responsibility.** REVER does not validate `idempotency_key` itself — it sends the same key on retries and trusts the ecommerce to recognize it. **If the ecommerce fails to deduplicate, REVER will receive a new `order_id`, treat it as a fresh order, and silently end up with duplicate exchange orders on the platform.** Persist the key alongside the order and look it up on every call.

**Request body**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `ecommerce_id` | string | yes | |
| `idempotency_key` | string | yes | See above. |
| `return_id` | string | yes | The return process this exchange settles. |
| `order` | object | yes | The exchange order. See below. |
| `original_order` | object | yes | Reference to the order being exchanged from. |

`order` fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `customer_info` | object | yes | Same shape as [Customer info](#customer-info). |
| `shipping_address` | object | yes | Same shape as [Address](#address). |
| `line_items` | object[] | yes | `[ { "variant_id", "product_id", "quantity" } ]`. Each entry picks a specific variant of a specific product. |
| `payment` | object | no | Present only when the variant the customer chose is more expensive than the original — `{ "amount", "currency" }`. `amount` is a decimal string in the customer's currency; `currency` is ISO 4217 and is **explicit here** because the request stands alone with no parent order to derive it from. |

`original_order` fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `order_id` | string | yes | |
| `order_number` | string | yes | |
| `total` | string | yes | Decimal string in the original order's shopper currency. |
| `currency` | string | yes | Shopper currency (ISO 4217) of the original order. |

**Response 200**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `order_id` | string | yes | Internal id of the new order. **Must be stable across re-deliveries** of the same `idempotency_key` — receiving the same value on a retry is how REVER knows the order was already created. |
| `order_number` | string | yes | Customer-facing order number. |
| `total` | string | yes | Decimal string in the new order's shopper currency. |
| `currency` | string | yes | Shopper currency (ISO 4217) of the new order. |
| `line_items` | object[] | yes | What was actually fulfilled. May be a strict subset of the request when partial fulfillment is needed. Each: `{ "quantity", "variant_id", "product_id" }`. |

**Error handling**

| Status | When |
| --- | --- |
| `200` | Created (or replayed). |
| `400` | Bad request (missing fields, malformed body). |
| `409` | Order cannot be created (no stock, product no longer available, …). REVER does **not** retry on `409` — the response is final. |
| `5xx` | Ecommerce platform error — REVER will retry with the same `idempotency_key`. |

**Example**

See [examples/post-orders-exchange.json](examples/post-orders-exchange.json).

---

## Supported refund platforms

When REVER issues a refund, it does so per [Transaction](#get-reverv2ordersorder_idtransactions) — looking up each leg's `platform_id` and passing the corresponding `transaction_id` to that platform's refund API. Mixed refunds (card + gift card, etc.) are first-class: REVER picks the legs it needs to refund and drives each one through its own platform.

`platform_id` is an open string — new platforms can be added without a spec change — but the canonical set REVER understands today is below, together with the exact value the ecommerce must put in `transaction_id` for each.

| `platform_id` | What `transaction_id` must contain | Notes |
| --- | --- | --- |
| `paypal` | The Paypal **capture id** (`capture_id`) for the captured payment. | |
| `redsys` | The Redsys **`DS_MERCHANT_ORDER`** value for the original authorization. | |
| `sequra` | The Sequra **`order_ref`**. | REVER tracks cumulative refund state per `order_ref`, so successive partial refunds work without extra work on the ecommerce side. |
| `stripe` | The Stripe **Charge id** (`ch_…`) or **PaymentIntent id** (`pi_…`) — either is accepted. | |
| `giftcard` | The gift card's **`platform_id`** as returned by [`POST /rever/v2/giftcards`](#post-reverv2giftcards) / [`GET /rever/v2/giftcards/{platform_id}`](#get-reverv2giftcardsplatform_id). REVER refunds back to the same gift card using the same CRUD endpoints documented in this spec. | |
| `manual` | Free-form merchant reference (or empty). | Marks a leg refunded by hand on the ecommerce side; REVER does not call any external API. |

### Adding new platforms

Need a platform that isn't in the list above (Klarna, Adyen, Mollie, …)? Reach out — REVER ships connectors per platform. Until a connector exists, the ecommerce can still surface those legs with a stable `platform_id` of its own choosing, but REVER will not be able to refund them automatically: those legs should typically use `platform_id: "manual"` so REVER's portal lets a human operator settle them outside the API.
