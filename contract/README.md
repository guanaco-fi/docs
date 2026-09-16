# Guanaco contracts

This directory contains human-readable CashScript specifications of the
contracts used by [guanaco.fi](https://guanaco.fi).

## Guanaco V1

[`GuanacoV1.cash`](GuanacoV1.cash) documents the current Guanaco pool covenant
in high-level CashScript. [`GuanacoV1-CashAssembly.md`](GuanacoV1-CashAssembly.md)
documents the optimized CashAssembly emitted by the production V1 builder.
V1 supports four fee tiers:

| Level | Fee | Numerator | Denominator |
| --- | ---: | ---: | ---: |
| 0 | 0.01% | 1 | 10,000 |
| 1 | 0.05% | 5 | 10,000 |
| 2 | 0.3% | 3 | 1,000 |
| 3 | 1% | 1 | 100 |

For each trade, the covenant:

1. Preserves the pool's token category and locking bytecode.
2. Requires transaction version 2.
3. Calculates the selected fee from the absolute BCH reserve delta.
4. Allocates at least 10% of that fee to the fixed buyback-and-burn output.
5. Retains the other 90% for liquidity providers and enforces the adjusted
   constant-product invariant.

The withdrawal path remains controlled by the public-key hash committed when
the pool was created.

## Buyback-and-burn allocation

Every Guanaco V1 trade assigns 10% of the selected pool fee to the fixed V1
buyback-and-burn treasury:

- **Address:**
  [`bitcoincash:qp6gu00lmsdtwfhg6lvd8q09vq6gw8lxyvgnr3603l`](https://explorer.selene.cash/address/bitcoincash:qp6gu00lmsdtwfhg6lvd8q09vq6gw8lxyvgnr3603l)
- **Public-key hash:** `748e3dffdc1ab726e8d7d8d381e56034871fe623`
- **P2PKH locking bytecode:**
  `76a914748e3dffdc1ab726e8d7d8d381e56034871fe62388ac`

This allocation funds periodic open-market purchases of GUA followed by
permanent token burns. Buybacks and burns are performed separately from each
swap; the allocation output does not represent an immediate purchase or burn.
Permanent burns reduce the maximum possible supply of GUA, whose original
fixed maximum supply is 1,000,000 GUA. See the public
[GUA overview](https://guanaco.fi/gua) for the token identity, supply, and
protocol-utility information.

The V1 covenant enforces two facts about the allocation output: its BCH value
must be at least 10% of the pool fee, and its locking bytecode must be the fixed
P2PKH bytecode above. The covenant does not itself execute or cryptographically
verify the later market purchase and token burn.

## Specification status

The production V1 covenant is emitted directly as optimized Bitcoin Cash
Script. The CashScript source in this directory is an independently compilable,
high-level specification of the same rules. CashScript compiler output is not
byte-for-byte identical to the optimized production redeem script and must not
be used to calculate production contract addresses.

V1 contract identity is defined by the production redeem script together with
the pool creator's withdrawal public-key hash and selected fee tier. Any future
consensus change requires a new contract version.
