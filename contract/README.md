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

## Specification status

The production V1 covenant is emitted directly as optimized Bitcoin Cash
Script. The CashScript source in this directory is an independently compilable,
high-level specification of the same rules. CashScript compiler output is not
byte-for-byte identical to the optimized production redeem script and must not
be used to calculate production contract addresses.

V1 contract identity is defined by the production redeem script together with
the pool creator's withdrawal public-key hash and selected fee tier. Any future
consensus change requires a new contract version.
