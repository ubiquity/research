# DevPool Bounty Hunter Payout Transaction Audit

Issue: https://github.com/ubiquity/research/issues/33

## Request

Hunt down 10 bounty hunters on chain to see what they do with their money.

## Summary

I reviewed 10 public bounty-hunter payout wallets that can be connected to Ubiquity GitHub assignment comments and Gnosis Chain token-transfer history. This pass is transaction-level rather than only aggregate-level: for every wallet I recorded the matched funding window, the latest matched funding transaction, the first post-funding movement when available, and the observed movement within 7 and 30 days after the latest matched payout.

Using the funding-wallet allowlist below, Blockscout returned 890 matched incoming Ubiquity transfers across the 10 wallets:

- 78,321.4 XDAI
- 34,256.8 UUSD

The main behavioral pattern is that most bounty-hunter wallets do not leave the payout untouched. Seven of the ten wallets moved funds within 7 days after their latest matched payout, one moved within 30 days, one moved later than 30 days, and one had no post-latest-payout movement visible in the token-transfer data. The highest-volume wallets also show broad DeFi-style activity, so the payout often becomes part of a larger stablecoin/router flow rather than remaining as a clean, isolated reward balance.

## Method

1. Start from public GitHub assignment or wallet comments that expose a bounty hunter wallet.
2. Query Gnosis Chain Blockscout token transfers for each wallet:

```text
https://gnosis.blockscout.com/api?module=account&action=tokentx&address=<wallet>&sort=asc
```

3. Treat an incoming transfer as matched Ubiquity funding only when:
   - `to` equals the bounty-hunter wallet,
   - `from` is in the known Ubiquity funding-wallet set below,
   - token symbol is one of the expected value tokens: XDAI, UUSD, USDC, WXDAI, SDAI, EURE, or GNO.
4. Treat `from == bounty-hunter wallet` as post-payout movement.
5. Ignore spam and promotional tokens by default.

Known funding wallets used for this audit:

- `0x44ca15db101fd1c194467db6af0c67c6bbf4ab51`
- `0x9051eda96db419c967189f4ac303a290f3327680`
- `0xf87ca4583c792212e52720d127e7e0a38b818ad1`
- `0x054ec26398549588f3c958719bd17cc1e6e97c3c`
- `0xefc0e701a824943b469a694ac564aa1eff7ab7dd`

Funding-wallet source: https://github.com/ubiquity/supabase-payments-audit/issues/4

## Findings

| Wallet | GitHub source | Matched funding totals | Matched funding window | Latest matched funding tx | Post-latest movement | Classification |
| --- | --- | ---: | --- | --- | --- | --- |
| [`0xbb68...c80f`](https://gnosis.blockscout.com/address/0xbb689fdabbfc0ae9102863e011d3f897b079c80f) | [`pay.ubq.fi#386`](https://github.com/ubiquity/pay.ubq.fi/issues/386#issuecomment-2708672579) | 1,902.8 UUSD; 4,979.9 XDAI | 2024-10-01 to 2025-08-26 | [0.404 UUSD](https://gnosis.blockscout.com/tx/0xd22123e1425ed360f72faa39c4e296299eaef645acc605e9dd0ca56e6fe8b551) | 30d: 1.000 USDC; 129.2 XDAI, first tx [0x5827...bc20](https://gnosis.blockscout.com/tx/0x58271cfc9f34809adb15ce0a46b62b8c45e66ff14d2efb7fc575f19ab74fbc20) | Moved within 30 days; not a static holder. |
| [`0x01ae...6485`](https://gnosis.blockscout.com/address/0x01ae8d6d0f137cf946e354ea707b698e8cae6485) | [`ubiquity-dollar#926`](https://github.com/ubiquity/ubiquity-dollar/issues/926#issuecomment-2386277724) | 300.8 XDAI | 2024-09-24 | [300.8 XDAI](https://gnosis.blockscout.com/tx/0xb60d3e0d4470a70225cb960d89db668da0fec1fd2a802f13cd90dcf26c268ae0) | 7d: 260.0 XDAI; 30d: 300.8 XDAI, first tx [0x86c8...b761](https://gnosis.blockscout.com/tx/0x86c8988893c7d1298b497c0cd8a57eb7d007bd685dbf569761b9eb2b3436b761) | Full or near-full cash-out/onward transfer. |
| [`0x0fc1...b2ed`](https://gnosis.blockscout.com/address/0x0fc1b909ba9265a846b82cf4ce352fc3e7eeb2ed) | [`stake.ubq.fi#8`](https://github.com/ubiquity/stake.ubq.fi/issues/8#issuecomment-3515109888) | 29,615.3 UUSD; 38,721.2 XDAI | 2024-02-13 to 2026-04-13 | [1.565 UUSD](https://gnosis.blockscout.com/tx/0xd4a80087eb6ec1c5cc3a9c9def53ad0b5ac1c124552186780ad1a49436f064dc) | 7d: 3,628.0 UUSD; 3,622.0 XDAI, first tx [0x8cc7...1200](https://gnosis.blockscout.com/tx/0x8cc74b556b0b26470766510b9c9f3bf9c4cf12b01feeee11d8d5bb38c85a1200) | DeFi-active wallet; payout is mixed into larger stablecoin/router activity. |
| [`0xd1a8...c81e`](https://gnosis.blockscout.com/address/0xd1a8dd23e356b9fae27df5def9ea025a602ec81e) | [`ubiquibot#913`](https://github.com/ubiquity/ubiquibot/issues/913#issuecomment-2359008805) | 325.5 UUSD | 2025-11-11 to 2025-12-18 | [3.736 UUSD](https://gnosis.blockscout.com/tx/0x9fa21c74108f118c8bca563a337467be8f0ce85bc70e4b06e57373ae9ed6c1b6) | 7d: 3.736 UUSD, first tx [0xcc4b...31a](https://gnosis.blockscout.com/tx/0xcc4bddc81994a40fe4b2a77419ac3bf6452795d4d10a6d03c12caaca3bc2a31a) | Latest small payout moved quickly. |
| [`0x2f05...45c5`](https://gnosis.blockscout.com/address/0x2f05fd58023b0a95d1866aa0a3b672cef05945c5) | [`command-start-stop#106`](https://github.com/ubiquity-os-marketplace/command-start-stop/issues/106#issuecomment-2541544862) | 798.5 XDAI | 2024-04-19 to 2024-09-14 | [201.0 XDAI](https://gnosis.blockscout.com/tx/0xf7f6ee2ff35347159a69cbee052a2592c6c8a74505fc4a30dc0f074b572e421a) | 7d: 198.0 XDAI, first tx [0x6c39...cb91](https://gnosis.blockscout.com/tx/0x6c39fa0244da4fab10773ca8c0e1f76e1c876deb52c75e6ccaa642a7e0bacb91) | Near full cash-out/onward transfer. |
| [`0x0bed...7025`](https://gnosis.blockscout.com/address/0x0bed00438d57d07e3667b85fa8eb86af147c7025) | [`ubiquibot#913`](https://github.com/ubiquity/ubiquibot/issues/913#issuecomment-2023088897) | 206.8 XDAI | 2023-08-26 to 2024-07-20 | [25.000 XDAI](https://gnosis.blockscout.com/tx/0xb39d8211d9608bc361ff13fd713b8170bafc1868712415e212711cbe52b70868) | 7d: 135.0 XDAI, first tx [0x3c77...7dc4](https://gnosis.blockscout.com/tx/0x3c77eabdb63197ab67f928340216d2af446eee32bce485d07729dad5b2947dc4) | Moved more than latest payout amount within 7 days, suggesting combined balance cleanup. |
| [`0x226a...349e`](https://gnosis.blockscout.com/address/0x226aae6db84dca652818400750b8bf96a077349e) | [`arbitrage-bot#3`](https://github.com/ubiquity/arbitrage-bot/issues/3#issuecomment-2541665078) | 1.310 XDAI | 2024-12-09 | [1.310 XDAI](https://gnosis.blockscout.com/tx/0x33599fb5bc0edf17fd4994be540eee0dd40af5c0f7d36e872f54b274afca9594) | Later than 30d, first tx [0x9b38...2d07](https://gnosis.blockscout.com/tx/0x9b38f5c9be7b334b05063756cbbb7e723ad3a86fbc7825669ea21ce7c1342d07) | Dust payout eventually swept. |
| [`0xdaba...1f69`](https://gnosis.blockscout.com/address/0xdaba6e01d15db560b88c8f426b016801f79e1f69) | [`.github#123`](https://github.com/ubiquity/.github/issues/123#issuecomment-2702190994) | 1,809.6 UUSD; 7,360.1 XDAI | 2024-08-31 to 2025-09-02 | [6.212 UUSD](https://gnosis.blockscout.com/tx/0x08c38176058d26cc92b692a86a063c83a58972f921002d506840a026847243b2) | 7d: 607.4 UUSD, first tx [0x0afc...2b08](https://gnosis.blockscout.com/tx/0x0afceb0b1a23a4a027631fceaac6bef86ed908c7de2be89f62c69ecf19502b08) | Moved funds within 7 days; larger balance likely batched. |
| [`0x3623...39e5`](https://gnosis.blockscout.com/address/0x3623338046b101ecec741de9c3594cc2176f39e5) | [`ubiquibot#787`](https://github.com/ubiquity/ubiquibot/issues/787#issuecomment-1728871520) | 4,001.1 XDAI | 2023-06-06 to 2024-02-21 | [21.450 XDAI](https://gnosis.blockscout.com/tx/0x27dad9320478ff2d68bacd21c722d16a9d1d08f1a9e836d2acf9e6d143208ae3) | No token-transfer movement after latest matched payout in this data. | Held or inactive after latest matched payout. |
| [`0xae5d...9765`](https://gnosis.blockscout.com/address/0xae5d1f192013db889b1e2115a370ab133f359765) | [`arbitrage-bot#3`](https://github.com/ubiquity/arbitrage-bot/issues/3#issuecomment-3591331999) | 603.6 UUSD; 21,951.7 XDAI | 2023-08-30 to 2026-01-16 | [73.000 XDAI](https://gnosis.blockscout.com/tx/0xf945e6d44d1a5600fd56a151520574dcfa8d927fe76b99ac9fe1e419cd02604e) | 7d: 467.9 UUSD; 560.0 XDAI, first tx [0x5872...703](https://gnosis.blockscout.com/tx/0x5872c2386b73e7cfc403767a1d77609b44338dc3c2dcdaf82ca9dd49bf3ef703) | DeFi-active wallet; payouts are quickly rerouted with larger stablecoin activity. |

## Patterns

### Most wallets move rewards quickly

Seven wallets moved value within 7 days after their latest matched funding event. Several moved more than the latest single payout amount, which suggests the payout was combined with existing balances or batched with other earned amounts before being sent onward.

### Small payouts are often swept

The lower-volume wallets show simple movement patterns: a payout arrives, then nearly the same amount leaves for one or a few recipients. Examples include `0x01ae...6485`, `0x2f05...45c5`, and `0x226a...349e`.

### High-volume earners look like DeFi operators

The largest wallets have many inbound funding transfers and broad stablecoin activity. `0x0fc1...b2ed` and `0xae5d...9765` both moved thousands of XDAI/UUSD shortly after the latest matched funding events and also interacted with a wider token set. For these wallets, the bounty payout is not economically isolated after receipt.

### GitHub wallet comments are necessary but not sufficient

The GitHub comments provide candidate wallets, but wallet registration does not prove payment. A reliable payout-behavior audit needs transaction hashes from an allowlisted funding-wallet set or an official payment table. This is especially important because spam tokens are common on Gnosis Chain and can otherwise distort wallet activity.

## Recommendations

1. Store issue URL, beneficiary wallet, token, amount, permit nonce, settlement hash, and chain in one exportable payout table at claim or settlement time.
2. Maintain an official funding-wallet registry by chain and token so later audits do not depend on manual extraction from comments.
3. Classify post-payout behavior with a stable taxonomy: held, swept, batched, swapped/routed, bridged, DeFi-active, unmatched.
4. Ignore spam tokens by default; only include allowlisted value tokens unless the audit specifically studies spam exposure.
5. For high-volume contributors, use exact transaction hashes and time windows instead of aggregate wallet totals alone.

## Limitations

- This pass uses the Blockscout token-transfer API, not internal Ubiquity accounting data.
- The analysis is wallet-level. It does not prove that the same human controlled the wallet during every historical transfer.
- Native gas movements and non-token traces are outside this pass.
- A transfer from the allowlisted funding wallets is treated as strong evidence of Ubiquity funding, but an official payout export would be stronger.
