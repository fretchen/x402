---
"@x402/evm": patch
---

Fixed `batch-settlement` throwing `No default asset configured for network …` on EVM networks outside `DEFAULT_STABLECOINS` when the caller supplies an explicit `amount` + `asset`. Asset metadata now flows through `parsePrice`/the caller instead of being re-derived from the registry in `enhancePaymentRequirements`, `createChannelManager` accepts an optional explicit token, and `defaultMoneyConversion` sets `assetTransferMethod` for permit2 tokens (matching the `exact` scheme).
