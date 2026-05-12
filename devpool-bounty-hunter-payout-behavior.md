# DevPool Bounty Hunter Payout Behavior

## Request

Hunt down 10 bounty hunters on chain to see what they do with their money.

Issue: https://github.com/ubiquity/research/issues/33

## Summary

I reviewed public Ubiquity assignment comments and Gnosis Chain token transfers for 10 bounty-hunter payout wallets that have matched incoming transfers from known Ubiquity funding wallets. Data was collected on 2026-05-13.

The strongest pattern is that bounty payouts are usually not held in the original payout token. Most wallets either sweep the full XDAI or UUSD amount onward, route it through swap/bridge-style contracts, or mix it with broader DeFi activity. Only a few wallets look inactive after assignment, and those were not counted in the 10-wallet sample unless a Ubiquity funding transfer could be matched.

## Method

1. Search public Ubiquity GitHub issues for bot comments containing `Beneficiary`, `Registered Wallet`, or `/wallet`.
2. For each candidate address, query Gnosis Chain token transfers through Blockscout's account token transfer API.
3. Count Ubiquity funding only when the token transfer sender matched one of the known funding wallets referenced in the historical permit audit discussion.
4. Classify follow-on behavior from outbound token transfers and top outbound recipients.

Known funding wallets used for this pass:

- `0x44ca15db101fd1c194467db6af0c67c6bbf4ab51`
- `0x9051eda96db419c967189f4ac303a290f3327680`
- `0xf87ca4583c792212e52720d127e7e0a38b818ad1`
- `0x054ec26398549588f3c958719bd17cc1e6e97c3c`
- `0xefc0e701a824943b469a694ac564aa1eff7ab7dd`

Source for the funding-wallet set: https://github.com/ubiquity/supabase-payments-audit/issues/4

The query is intentionally conservative. If an address was registered in GitHub comments but no incoming transfer from the known funding-wallet set was visible, I did not treat it as a confirmed payout wallet.

The wallet is the primary unit of analysis because the public bot comments expose the payout address consistently, while historical GitHub user mapping can change or be missing from old comments.

## Wallet Findings

| Wallet | Public assignment source | Matched Ubiquity funding in | Observed outbound behavior | Classification |
| --- | --- | ---: | --- | --- |
| `0xbb689fdabbfc0ae9102863e011d3f897b079c80f` | [`pay.ubq.fi#386`](https://github.com/ubiquity/pay.ubq.fi/issues/386#issuecomment-2708672579) | 4,979.8649 XDAI; 1,902.8435 UUSD | Sent out 8,487.8649 XDAI and 3,034.5025 UUSD. Top recipients include zero-address burns/invalidations, router-style contracts, and one CoW Protocol settlement transfer. | Mostly swept or routed onward; not a long-term holder. |
| `0x01ae8d6d0f137cf946e354ea707b698e8cae6485` | [`ubiquity-dollar#926`](https://github.com/ubiquity/ubiquity-dollar/issues/926#issuecomment-2386277724) | 300.78 XDAI | Sent out 300.78 XDAI, mostly to one high-frequency recipient plus some zero-address activity. | Full cash-out/onward transfer. |
| `0x0fc1b909ba9265a846b82cf4ce352fc3e7eeb2ed` | [`stake.ubq.fi#8`](https://github.com/ubiquity/stake.ubq.fi/issues/8#issuecomment-3515109888) | 38,721.2098 XDAI; 29,615.3173 UUSD | Very active wallet: large SDAI, XDAI, EURE, UUSD, USDC, and GNO flows. Top outbound recipients are repeated router/protocol addresses. | Active DeFi treasury-style wallet. |
| `0xd1a8dd23e356b9fae27df5def9ea025a602ec81e` | [`ubiquibot#913`](https://github.com/ubiquity/ubiquibot/issues/913#issuecomment-2359008805) | 325.4915 UUSD | Ubiquity payout is small relative to broader token activity. Top outbound recipients include 12 CoW Protocol settlement transfers and frequent zero-address activity. | Mixed active wallet; payout was likely absorbed into broader DeFi activity. |
| `0x2f05fd58023b0a95d1866aa0a3b672cef05945c5` | [`command-start-stop#106`](https://github.com/ubiquity-os-marketplace/command-start-stop/issues/106#issuecomment-2541544862) | 798.502 XDAI | Sent out 793.977 XDAI, mainly to one high-frequency recipient. | Near full cash-out/onward transfer. |
| `0x0bed00438d57d07e3667b85fa8eb86af147c7025` | [`ubiquibot#913`](https://github.com/ubiquity/ubiquibot/issues/913#issuecomment-2023088897) | 206.85 XDAI | Sent out 326.85 XDAI. Top recipients are the zero address and one self-transfer. | Cleanup/invalidation-heavy wallet; no clear swap target. |
| `0x226aae6db84dca652818400750b8bf96a077349e` | [`arbitrage-bot#3`](https://github.com/ubiquity/arbitrage-bot/issues/3#issuecomment-2541665078) | 1.31 XDAI | Sent out exactly 1.31 XDAI to one recipient. | Dust payout swept completely. |
| `0xdaba6e01d15db560b88c8f426b016801f79e1f69` | [`.github#123`](https://github.com/ubiquity/.github/issues/123#issuecomment-2702190994) | 7,360.123 XDAI; 1,809.5525 UUSD | Sent out 10,357.298 XDAI and 1,809.5525 UUSD. Top recipients are router-style contracts and zero-address activity. | Cash-out and routing; not a long-term holder. |
| `0x3623338046b101ecec741de9c3594cc2176f39e5` | [`ubiquibot#787`](https://github.com/ubiquity/ubiquibot/issues/787#issuecomment-1728871520) | 4,001.1 XDAI | Sent out 3,962.5 XDAI, mainly to one high-frequency recipient. | Near full cash-out/onward transfer. |
| `0xae5d1f192013db889b1e2115a370ab133f359765` | [`arbitrage-bot#3`](https://github.com/ubiquity/arbitrage-bot/issues/3#issuecomment-3591331999), [`supabase-payments-audit#4`](https://github.com/ubiquity/supabase-payments-audit/issues/4) | 21,951.6604 XDAI; 603.62 UUSD | Sent out 23,954.5295 XDAI and 603.62 UUSD. Top recipients include one high-frequency recipient and router-style contracts. | High-volume earner that sweeps and routes most payout value. |

Blockscout address links:

- https://gnosis.blockscout.com/address/0xbb689fdabbfc0ae9102863e011d3f897b079c80f
- https://gnosis.blockscout.com/address/0x01ae8d6d0f137cf946e354ea707b698e8cae6485
- https://gnosis.blockscout.com/address/0x0fc1b909ba9265a846b82cf4ce352fc3e7eeb2ed
- https://gnosis.blockscout.com/address/0xd1a8dd23e356b9fae27df5def9ea025a602ec81e
- https://gnosis.blockscout.com/address/0x2f05fd58023b0a95d1866aa0a3b672cef05945c5
- https://gnosis.blockscout.com/address/0x0bed00438d57d07e3667b85fa8eb86af147c7025
- https://gnosis.blockscout.com/address/0x226aae6db84dca652818400750b8bf96a077349e
- https://gnosis.blockscout.com/address/0xdaba6e01d15db560b88c8f426b016801f79e1f69
- https://gnosis.blockscout.com/address/0x3623338046b101ecec741de9c3594cc2176f39e5
- https://gnosis.blockscout.com/address/0xae5d1f192013db889b1e2115a370ab133f359765

## Non-Confirmed Samples

These addresses appeared in public assignment comments, but I did not find incoming transfers from the known funding-wallet set in the Blockscout token-transfer data used for this pass:

| Wallet | Public source | Observation |
| --- | --- | --- |
| `0x608afcd86a82b62b062c7cfa4d95ef96cc74a7fe` | [`stake.ubq.fi#9`](https://github.com/ubiquity/stake.ubq.fi/issues/9#issuecomment-3864014548) | No Gnosis token transfers returned by the token-transfer API. |
| `0xba82a1b39499b578f8056d296b914b13c99f4d98` | [`stake.ubq.fi#8`](https://github.com/ubiquity/stake.ubq.fi/issues/8#issuecomment-3864018063) | Only spam-like or unrelated incoming tokens visible; no matched Ubiquity funding transfer. |

Possible explanations include unclaimed assignments, changed wallets, incomplete funding-wallet coverage, permits settled from other senders, or payment on another network.

## Patterns

### Payouts are usually liquidated or rerouted

Small and medium earners commonly move almost the whole received XDAI amount out of the payout wallet. Examples include `0x01ae...`, `0x2f05...`, `0x3623...`, and `0x226a...`.

### Larger earners behave like active DeFi operators

The largest confirmed wallets do not simply hold UUSD or XDAI. `0x0fc1...`, `0xae5d...`, `0xdaba...`, and `0xbb68...` show repeated routing through protocol contracts, stablecoin flows, and larger outbound totals than the matched funding totals.

### Spam-token noise is high

Many wallets received large amounts of spam-like tokens with promotional token symbols. Those should be ignored for reward analysis. Filtering by known funding sender and expected payout tokens is necessary before drawing conclusions.

### GitHub assignment comments are useful but insufficient alone

The bot comments expose candidate wallets, but registration does not prove payout. A reliable report needs to join:

- the GitHub issue or comment URL,
- the beneficiary wallet at assignment time,
- known funding wallets,
- token transfer history,
- permit or claim transaction hashes when available.

## Recommendations

1. Store the beneficiary wallet, issue URL, label amount, token, permit nonce, and settlement transaction hash in one audit table at permit generation or claim time.
2. Classify post-payout behavior with a small taxonomy: held, swept, swapped, bridged/routed, DeFi-active, unmatched.
3. Maintain an official funding-wallet registry by chain and token so historical analysis does not depend on manual extraction from comments.
4. Ignore spam tokens by default and only include tokens from an allowlist such as XDAI, WXDAI, UUSD, USDC, SDAI, EURE, and GNO.
5. Preserve both current wallet mapping and historical wallet mapping. Contributors can change wallets, so current `/wallet` state alone is not enough for old payouts.
6. For high-value contributors, review exact transaction hashes rather than aggregate totals. Aggregate transfer sums are good for behavior classification but not enough for accounting.

## Reproducible Query Shape

Blockscout token transfer API pattern:

```text
https://gnosis.blockscout.com/api?module=account&action=tokentx&address=<wallet>&sort=asc
```

For each transfer:

- `to == <wallet>` and `from in <known funding wallets>` gives matched Ubiquity funding in.
- `from == <wallet>` gives post-payout movement.
- token amounts should be normalized using `tokenDecimal`.

This can be automated as a small periodic audit job that starts from GitHub assignment comments and ends in a wallet-level payout behavior report.
