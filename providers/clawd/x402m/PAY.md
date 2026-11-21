---
name: x402m
title: "Musebook x402m — Facilitator & Tool Shop"
description: "Musebook x402m: the production deployment of the Musebook x402 facilitator — a pay-per-call tool shop of 178 Solana data, trading and agent APIs, every call gated in USDC on Solana mainnet and settled non-custodially by the facilitator."
use_case: "Use when an agent needs Solana market data, swap quotes, wallet intelligence or agent services paid per call in USDC, or a live x402 facilitator deployment whose settlements, receipts and batch protocol (x402m) it can build against."
category: finance
service_url: https://x402m.musebook.trade
version: v1
openapi:
  path: openapi.json
---

x402m is the production deployment of the **Musebook facilitator**, a
non-custodial x402 facilitator (payers sign; the facilitator verifies
and relays). This host runs the facilitator's tool shop: 178
pay-per-call APIs for Solana market data, perpetuals, swap quotes,
token risk, wallet intelligence and agent services. Every call is
gated with an x402 `exact` challenge in USDC on Solana mainnet and
settles through the Musebook facilitator to
`FYp4pswmeyzftftnoyJV9Rrr11XFb23yosrHZ3XG5tb9`. The full
machine-readable catalog — every tool, method, path and price — is
served at `GET /__x402/config` on this host; the OpenAPI document in
this listing covers a representative subset.

The facilitator's own API (free for resource servers) lives on the
canonical host musebook.trade:

- Discovery: `GET https://musebook.trade/.well-known/x402-facilitator`
- Supported kinds: `GET https://musebook.trade/api/x402/supported` —
  Solana mainnet and devnet (`exact`, x402 v1 and v2) and Base
  (eip155:8453; Base Sepolia is testnet).
- `POST /api/x402/verify` and `POST /api/x402/settle`, plus the x402m
  meta protocol: single-signature batch settlement
  (`x402m-batch-v0`), settlement receipts (`x402m-receipts-v0`) and
  referrals (`x402m-referral-v0`).
- Registry: `GET https://musebook.trade/api/x402/catalog`.

Pricing on this host: a flat $0.01 USDC per call for most endpoints,
$0.0142 for the Imperial / market-data tools, and a $0.01 one-hour
access pass at `GET /__x402/protected`. Prices are read from the live
402 challenge, never from this document.

Also reachable at https://x402m.x402.wtf (same deployment). The
facilitator service additionally federates with the x402.wtf omni hub,
which routes batch, receipts and pulse traffic to it.

## Spend-aware usage

- Fetch `GET /__x402/config` once and cache it — it is the complete
  priced catalog; call only the tool that answers the task.
- One payment redeems exactly once; on a 402, pay and resend the same
  request rather than opening a second payment.
- Prefer one broad call (e.g. wallet balances) over many narrow calls
  when the task needs the whole picture anyway.
