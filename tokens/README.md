# Token metadata and support in Guanaco

## Quick start

To request support for a token that is not listed in OpenTokenRegistry:

1. Contact the Guanaco team through the official
   [Guanaco Telegram channel](https://t.me/guanacofi).
2. Copy the form below, complete every field, and send it to us:

   ```text
   - Token category:
   - Token name:
   - Token symbol:
   - Decimal precision:
   - Token icon URL:
   - BCMR metadata URL:
   - Official website:
   - Official contact channels:
   - Public page or announcement displaying the full token category:
   - Brief description of the token's purpose:
   - OpenTokenRegistry submission or entry (if any):
   ```

3. Maintain at least **0.1 BCH in combined live liquidity** across Guanaco and
   Cauldron pools that Guanaco can discover.

Approval is specific to Guanaco. If the required liquidity is withdrawn or the
token's identity metadata changes, Guanaco may suspend the approval and request
a new review.

The sections below explain the verification model, requirements, and limits in
more detail.

---

## How token verification works

CashTokens are identified on-chain by an immutable token category. Human-readable
details such as a name, symbol, icon, website, and decimal precision come from
separate metadata.

Guanaco treats these two layers differently:

- A token category proves which on-chain asset is being used.
- Verified metadata tells Guanaco how that asset should be presented and how
  human-readable amounts map to its atomic units.

This distinction matters. Incorrect metadata can make one token look like
another, and incorrect decimal precision can produce a transaction for an
unexpected amount.

## Token status

### Unverified

An unverified token may be discovered on-chain and shown in Guanaco. Its presence
does not mean that Guanaco endorses the token or trusts its metadata.

Guanaco may restrict an unverified token from actions that construct or sign
transactions. This protects users from misleading identities and unsafe amount
conversion.

### Community verified

The preferred path is inclusion in
[OpenTokenRegistry](https://otr.cash/). OpenTokenRegistry provides public,
community-reviewed token metadata that can be reused across Bitcoin Cash
wallets and applications.

Guanaco can use a supported registry entry as evidence that a token category
and its public metadata have been reviewed. Registry inclusion is not an
endorsement of the token's market value, issuer, financial condition, or future
performance.

### Guanaco reviewed

If OpenTokenRegistry verification is not available, a token issuer may ask
Guanaco to review the token for local support.

A Guanaco review is specific to Guanaco. It does not add the token to
OpenTokenRegistry and must not be represented as ecosystem-wide verification.
Approval means only that Guanaco has accepted a particular mapping between the
token category and the metadata used by the application.

## What to provide

A review request should include:

- the complete CashToken category;
- the token name, symbol, and decimal precision;
- a square token icon with a stable public source;
- a BCMR metadata source, when available;
- the project's official website and public contact channels;
- a page or public announcement on an official channel that displays the full
  token category;
- a brief explanation of the token's purpose; and
- any existing OpenTokenRegistry submission or review.

The token must also have at least **0.1 BCH in live liquidity** across supported
pools that Guanaco can discover. Liquidity may be distributed between Guanaco
and Cauldron pools; the requirement applies to their combined discoverable BCH
liquidity, not separately to each protocol. Spent, inactive, or undiscoverable
pools do not count toward the minimum.

The liquidity requirement continues after approval. If liquidity is withdrawn
and the token no longer meets the minimum, Guanaco may suspend or revoke its
local reviewed status. This does not remove the token or its pools from the
Bitcoin Cash network; it only affects how Guanaco presents the token and
whether Guanaco enables it for value-changing operations. Restoring local
reviewed status may require a new review.

The information must be internally consistent and must not imitate or create
confusion with another token or project. Guanaco may ask for additional evidence
before enabling value-changing operations.

## What a review does not mean

Community verification or Guanaco review does **not** certify that a token is:

- an investment;
- safe from market, contract, or issuer risk;
- redeemable for another asset;
- legally compliant in every jurisdiction; or
- guaranteed to retain any value.

Users remain responsible for checking the token category before transacting.

## Metadata changes

Token identity metadata should be stable. Changes to the category, symbol,
decimal precision, official website, or other identity information may require
a new review. Guanaco may temporarily return a token to unverified status while
conflicting or changed metadata is investigated.

## Requesting support

Issuers should first pursue
[OpenTokenRegistry's listing process](https://otr.cash/docs/list/). When that is
not practical, contact the Guanaco team through the official
[Guanaco Telegram channel](https://t.me/guanacofi) with the information listed
above and request a Guanaco-specific metadata review.

Guanaco may approve, reject, defer, or revoke local token support when necessary
to protect users or resolve inconsistent metadata.
