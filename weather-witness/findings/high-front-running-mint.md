# 🔴 High — Front-running Mint Requests

### 📄 Contract: `WeatherNft.sol`

---

## 🧠 Summary

The function emits a predictable request ID (`_reqId`) before storing associated minting information, allowing bots or malicious actors to track and act on it.  
This creates an opening for front-running the asynchronous Chainlink fulfillment process.

---

## 🔍 Description

When a user initiates minting:

```solidity
_reqId = _sendFunctionsWeatherFetchRequest(_pincode, _isoCode);
s_funcReqIdToUserMintReq[_reqId] = UserMintRequest({...});
```

The request ID is **emitted before** it is stored in internal mappings, making it observable and exploitable by bots before the state is finalized.

---

## 🎯 Impact

- Malicious actors can spoof or hijack fulfillment requests.
- Users may receive NFTs with incorrect or manipulated metadata.
- Trust in the minting system is degraded.

---

## ⚠️ Risk Assessment

**Likelihood**: High — Request ID is publicly emitted and can be easily captured  
**Impact**: High — Broken minting logic, polluted metadata

---

## 🧪 Proof of Concept

```solidity
// Attacker listens for emitted requestId on-chain
// Before Chainlink fulfills, they attempt to spoof fulfillMintRequest

bytes32 leakedRequestId = sniffFromLogs();
weatherNft.fulfillMintRequest(leakedRequestId); // spoofed call
```

---

## ✅ Recommended Mitigation

Capture request ID **before emitting** or exposing:

```solidity
// BAD:
_reqId = _sendFunctionsWeatherFetchRequest(_pincode, _isoCode);
s_funcReqIdToUserMintReq[_reqId] = UserMintRequest({...});

// GOOD:
bytes32 tempReqId = _sendFunctionsWeatherFetchRequest(_pincode, _isoCode);
require(s_funcReqIdToUserMintReq[tempReqId].user == address(0), "Duplicate Request");
s_funcReqIdToUserMintReq[tempReqId] = UserMintRequest({...});
_reqId = tempReqId;
```

This ensures state is committed before external exposure.

---

## 🧑‍⚖️ Judge's Note

> Lack of ownership check in `fulfillMintRequest()` allows NFTs to be minted using stolen request IDs.
