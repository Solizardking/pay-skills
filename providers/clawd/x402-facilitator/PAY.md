---
name: x402-facilitator
title: "x402.wtf Omni Facilitator"
description: "Omni-chain x402 facilitator hub: verify and settle exact-scheme payments on Solana mainnet and devnet, Base, Bitcoin, Lightning, Cardano and NEAR Intents, with x402m batch settlement, receipts, prepaid vouchers and 215 pay-per-call tools."
use_case: "Use when a resource server or agent needs an x402 facilitator: read supported payment kinds, verify a signed payment payload before serving a paid resource, settle exact payments on Solana mainnet, or settle batched payments."
category: finance
service_url: https://x402.wtf
version: v2
openapi:
  path: openapi.json
---

The x402.wtf hub is a non-custodial x402 facilitator: payers sign, the
facilitator verifies and relays. It exposes the standard facilitator
surface — `GET /supported`, `POST /verify`, `POST /settle` — plus a
machine-readable discovery document at
`GET /.well-known/x402-facilitator` that lists every endpoint, scheme,
network and leg status. Facilitator calls are free; the resource server
sets the price of its own resources.

Live payment legs (as advertised by `/supported`, 17 kinds):

- **Solana mainnet and devnet** — `exact` scheme, x402 v1 and v2,
  settled through the production facilitator service. The hub also
  serves the Clawd Compute catalog of 215 pay-per-call tools from this
  host (`GET /v1/tools`, `POST /v1/tools/<name>`, flat $0.01 USDC) as a
  facade over the gateway tool engine: an unpaid call returns a 402
  challenge accepting USDC on Solana mainnet (or Base), and the
  payment settles through this facilitator.
- **Base (eip155:8453)** — `exact` verify and settle delegated to the
  Coinbase CDP Facilitator; settlements carrying a Bazaar discovery
  declaration are indexed in the CDP Bazaar catalog.
- **Bitcoin (bip122), Lightning, Cardano (cip34)** — payment-proof legs:
  the payer broadcasts a normal transaction (or reveals a Lightning
  preimage) and the hub verifies the proof.
- **NEAR Intents** — payout routing across 39 chains via 1Click
  (`GET /api/intents/tokens`, `POST /api/intents/quote`).
- **x402m meta protocol** — single-signature batch settlement
  (`x402m-batch-v0`), settlement receipts (`x402m-receipts-v0`) and
  referrals (`x402m-referral-v0`), served with the federated Musebook
  peer, plus z402 prepaid vouchers on Solana.

Stated plainly — legs that are not fully live:

- Robinhood Chain (eip155:4663) and HyperEVM (eip155:999) verify live at
  the hub; settlement activates when the hub's EVM gas relayer is
  funded, and `/settle` returns a 503 `evm_relayer_unfunded` error
  until then.
- The shielded Solana leg (Helius Privacy Rings) is dry-run only, and
  the Zcash leg's scan backend is not yet configured — neither is
  advertised as a supported kind in `/supported`.

Documentation, the omni-chain integration guide and the facilitator
skill live at https://x402.life (the x402.life well-known facilitator
document redirects here — x402.wtf is the facilitator, x402.life is its
docs hub).

## Spend-aware usage

- Read `/supported` once at startup and cache it; only advertise a
  payment option after the facilitator confirms that exact x402
  version, scheme and network.
- Call `/verify` before doing paid work and `/settle` once — a payment
  redeems exactly once, and replays are rejected.
- Prefer the batch endpoints (`/api/x402/batch/*`) when settling many
  payments from one payer signature window instead of one settle per
  payment.
