# Guanaco V1 CashAssembly

This document describes the optimized Bitcoin Cash Script emitted by the
production Guanaco V1 contract builder. It is the low-level counterpart of the
human-readable [`GuanacoV1.cash`](GuanacoV1.cash) specification.

The listing uses symbolic placeholders so the structure can be read without
tying it to a particular pool:

- `<withdrawalPkh>` is the pool creator's 20-byte withdrawal public-key hash.
- `<feeCalculation>` is replaced by the selected V1 fee-tier fragment.
- `<allocationP2pkh>` is the fixed V1 buyback-and-burn P2PKH locking bytecode:
  `76a914748e3dffdc1ab726e8d7d8d381e56034871fe62388ac`.
  It pays
  [`bitcoincash:qp6gu00lmsdtwfhg6lvd8q09vq6gw8lxyvgnr3603l`](https://explorer.selene.cash/address/bitcoincash:qp6gu00lmsdtwfhg6lvd8q09vq6gw8lxyvgnr3603l).

Opcode names follow Bitcoin Cash VM terminology. Integer literals are encoded
using their minimal Script-number representation.

## Fee-tier specialization

The fee fraction is compiled into each pool's redeem script. Multiplication by
one is omitted for levels 0 and 3.

| Level | Fee | `<feeCalculation>` | Redeem-script size |
| --- | ---: | --- | ---: |
| 0 | 0.01% | `<10000> OP_DIV` | 109 bytes |
| 1 | 0.05% | `OP_5 OP_MUL <10000> OP_DIV` | 111 bytes |
| 2 | 0.3% | `OP_3 OP_MUL <1000> OP_DIV` | 111 bytes |
| 3 | 1% | `<100> OP_DIV` | 108 bytes |

## Annotated assembly

```text
OP_DEPTH
OP_IF
    # Withdrawal path
    # Unlocking stack: <signature> <publicKey>
    OP_DUP
    OP_HASH160
    <withdrawalPkh>
    OP_EQUALVERIFY
    OP_CHECKSIG
OP_ELSE
    # Trade path
    # The successor pool at the active input index must retain the token
    # category of the spent pool.
    OP_INPUTINDEX
    OP_OUTPUTTOKENCATEGORY
    OP_INPUTINDEX
    OP_UTXOTOKENCATEGORY
    OP_EQUALVERIFY

    # Guanaco swaps use transaction version 2.
    OP_TXVERSION
    OP_2
    OP_EQUALVERIFY

    # The successor output must retain this exact covenant bytecode.
    OP_INPUTINDEX
    OP_OUTPUTBYTECODE
    OP_INPUTINDEX
    OP_UTXOBYTECODE
    OP_EQUALVERIFY

    # totalFee = abs(inputBch - successorBch) * numerator / denominator
    OP_INPUTINDEX
    OP_UTXOVALUE
    OP_INPUTINDEX
    OP_OUTPUTVALUE
    OP_SUB
    OP_ABS
    <feeCalculation>

    # Keep totalFee and calculate the nominal 10% buyback-and-burn allocation.
    # Stack: totalFee nominalAllocation
    OP_DUP
    OP_10
    OP_DIV

    # allocationIndex = outputCount - 1 - activeInputIndex
    # Allocation outputs are arranged in reverse pool-input order.
    OP_TXOUTPUTCOUNT
    OP_1SUB
    OP_INPUTINDEX
    OP_SUB

    # The allocation output must pay at least the nominal 10% share.
    # A relay-policy dust top-up may make the actual output larger.
    OP_DUP
    OP_OUTPUTVALUE
    OP_2
    OP_PICK
    OP_GREATERTHANOREQUAL
    OP_VERIFY

    # The allocation must use the fixed V1 P2PKH destination.
    OP_OUTPUTBYTECODE
    <allocationP2pkh>
    OP_EQUALVERIFY

    # lpFee = totalFee - nominalAllocation
    OP_SUB

    # kBefore = inputBch * inputTokenAmount
    OP_INPUTINDEX
    OP_UTXOVALUE
    OP_INPUTINDEX
    OP_UTXOTOKENAMOUNT
    OP_MUL

    # kAfter = (successorBch - lpFee) * successorTokenAmount
    OP_INPUTINDEX
    OP_OUTPUTVALUE
    OP_2
    OP_PICK
    OP_SUB
    OP_INPUTINDEX
    OP_OUTPUTTOKENAMOUNT
    OP_MUL

    # Require kAfter >= kBefore and remove the saved lpFee.
    OP_LESSTHANOREQUAL
    OP_NIP
OP_ENDIF
```

## Execution paths

### Withdrawal

The withdrawal branch is selected when unlocking data is present. It performs a
standard public-key-hash ownership check: the supplied public key must hash to
`<withdrawalPkh>`, and the transaction signature must be valid for that key.

### Trade

The trade branch is selected with an empty unlocking stack. It requires the pool
to be recreated at the same index, with the same token category and locking
bytecode. The covenant calculates the configured swap fee from the absolute BCH
reserve delta, reserves 10% for GUA buybacks and permanent burns, and treats the
remaining 90% as the liquidity-provider fee when checking the constant-product
invariant.

The covenant enforces the nominal allocation as a minimum. Any additional BCH
needed to keep the allocation output relayable is transaction-building policy,
not part of the V1 consensus calculation.

The destination accumulates BCH reserved for periodic open-market GUA purchases
and permanent token burns. Those operations happen separately from the swap.
V1 guarantees the amount and destination of each allocation, but it does not
execute or verify the subsequent purchase and burn. More information about GUA
is available at [guanaco.fi/gua](https://guanaco.fi/gua).

## Version identity

The optimized redeem script, `<withdrawalPkh>`, and selected fee tier together
define a Guanaco V1 pool's P2SH32 identity. Changing an opcode, embedded value,
fee fraction, or destination produces a different contract and therefore
requires a new version.
