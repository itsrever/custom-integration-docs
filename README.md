# Custom integrations with REVER

This repository documents the HTTP contract that an ecommerce platform must implement so that the REVER returns & exchanges portal can integrate with it.

## Why this contract exists

REVER's portal handles the full returns/exchanges flow on behalf of an ecommerce: lookup of an order, validation of returnable items, generation of return labels, refunds (including via the original payment method), gift cards, promocodes and variant exchanges. To do that, REVER needs to read from and write to the ecommerce's platform.

Off-the-shelf platforms (Shopify, Magento, WooCommerce, Salesforce Commerce Cloud, …) are integrated through purpose-built connectors maintained by REVER. **Custom platforms** integrate by exposing a small set of HTTP endpoints described in this repository. The ecommerce implements the endpoints; REVER calls them.

## Versions

| Version | Status | Path prefix | Spec |
| --- | --- | --- | --- |
| **v1** | Stable. Maintained for already-integrated ecommerces. No new features. | `/rever/...` | [docs/v1/README.md](docs/v1/README.md) |
| **v2** | Current. Recommended for new integrations. | `/rever/v2/...` | [docs/v2/README.md](docs/v2/README.md) — OpenAPI: [docs/v2/openapi.yaml](docs/v2/openapi.yaml) |

### What v2 adds over v1

- **Full catalog** — `GET /rever/v2/products`, `GET /rever/v2/products/{id}`, `GET /rever/v2/menu`. Lets REVER pull the ecommerce's catalog periodically to power recommendations and exchange suggestions.
- **Categories and tags** on orders and products, used by dynamic warehouses and segmentation.
- **Promocodes vs gift cards** — clearly separated. Promocodes are single-use discount codes (CRUD). Gift cards are a stored-value payment method with partial redemption and balance (CRUD).
- **Mixed-refund support** — a new `GET /rever/v2/orders/{order_id}/transactions` endpoint exposes the individual payment legs (with their payment-processor IDs) so REVER can refund across multiple payment methods (e.g. credit card + gift card).
- **Get transactions** — `GET /rever/v2/orders/{order_id}/transactions` so REVER can know exactly what was already refunded.
- **Two-currency model** — `shop_currency` (ecommerce currency for product/catalog prices) and `customer_currency` (shopper currency for everything else on the order) are paired at the top of the order, with `exchange_rate` for the cross-currency case. Per-field `currency` siblings are gone.
- **Product physical attributes** — optional `weight` and `dimensions` (physical bounding box) on line items, variants, and products. When a product has no variants, the product-level values are used; when variants are present, each variant carries its own and the product-level copies are ignored. Variant-defining options (Size, Color, …) live in a separate field called `options`.

> **Event notifications (webhooks) are out of scope for this spec.** Return-completed, refund-executed, exchange-order-created and return-cancelled events are delivered through REVER's standard webhook system — see <https://docs.itsrever.com/apis/webhooks>.

## Versioning policy

Path prefixes follow `vMAJOR` for breaking changes and `vMAJOR_MINOR` for additive revisions:

| Prefix | Meaning |
| --- | --- |
| `/rever/v2/` | Initial v2 release. |
| `/rever/v2_1/`, `/rever/v2_2/`, … | Additive revisions of v2. Only new optional fields or new endpoints. Backwards-compatible with `/rever/v2/`. Ecommerces may stay on `/rever/v2/` indefinitely. |
| `/rever/v3/` | Future breaking version. Required only when an existing field's type or semantics must change. |

An ecommerce that has integrated against `/rever/v2/` does not need to do anything when `/rever/v2_1/` ships. Opting in to a minor revision is opt-in: the ecommerce can start exposing the newer prefix when ready, and REVER will switch to it.

## Authentication

All requests carry the header:

```http
X-rever-api-key: <api_key>
```

The value is agreed with REVER at the start of the integration and shared via a one-time secret link. Both v1 and v2 use the same scheme.

## Repository layout

```text
.
├── README.md             # this file
└── docs/
    ├── v1/
    │   └── README.md     # v1 specification
    └── v2/
        ├── README.md     # v2 specification
        ├── openapi.yaml  # OpenAPI 3.1 (ecommerce-implemented endpoints)
        └── examples/     # copy-paste-ready JSON bodies
```
