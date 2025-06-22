# 🔴 High — Missing Access Control in `fulfillMintRequest`

### 📄 Contract: `WeatherNft.sol`

---

## 🧠 Summary

The `fulfillMintRequest()` function can be called by **any external address**, not just Chainlink.  
This enables anyone to spoof weather data callbacks and mint NFTs illegitimately.

---

## 🔍 Description

```solidity
@> function fulfillMintRequest(bytes32 requestId) external {
    ...
    _safeMint(to, newTokenId);
    ...
}
```

There is **no access control** ensuring that only the Chainlink oracle can call this function.

---

## 🎯 Impact

- Unauthorized minting of NFTs
- Pollution of the collection with incorrect weather metadata
- Denial of service or misuse of LINK and resources

---

## ⚠️ Risk Assessment

**Likelihood**: Medium — Anyone monitoring emitted request IDs can exploit  
**Impact**: High — Entire NFT logic compromised

---

## 🧪 Proof of Concept

```solidity
// Attacker observes a valid requestId in logs
bytes32 leakedRequestId = sniffFromLogs();

// Calls the fulfill function manually
weatherNft.fulfillMintRequest(leakedRequestId); // No checks!
```

---

## ✅ Recommended Mitigation

Add an access control condition to ensure only Chainlink can call:

```solidity
- function fulfillMintRequest(bytes32 requestId) external {
+ function fulfillMintRequest(bytes32 requestId) external {
+     require(msg.sender == address(chainlinkOracle), "Only Chainlink can fulfill");
```

---

## 🧑‍⚖️ Judge's Note

> Arbitrary users can mint NFTs using leaked or forged data. Oracle identity checks are essential.
