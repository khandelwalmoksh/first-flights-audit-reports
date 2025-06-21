# 🟢 Low — Unbounded Loop in `graduateAndUpgrade()` Leads to Potential DoS

### 📄 Contract: `LevelOne.sol`

---

## 🧠 Summary

The `graduateAndUpgrade()` function in the `LevelOne` contract contains an unbounded loop over `listOfTeachers`. If the teacher list grows too large, gas consumption will exceed the block gas limit, causing the transaction to fail. This leads to a **Denial of Service (DoS)** that blocks contract upgrade and teacher/principal payouts.

---

## 🔍 Vulnerability Details

```solidity
uint256 totalTeachers = listOfTeachers.length;

uint256 payPerTeacher = (bursary * TEACHER_WAGE) / PRECISION;
uint256 principalPay = (bursary * PRINCIPAL_WAGE) / PRECISION;

_authorizeUpgrade(_levelTwo);

for (uint256 n = 0; n < totalTeachers; n++) {
    usdc.safeTransfer(listOfTeachers[n], payPerTeacher);
}
```

- The loop iterates over all entries in `listOfTeachers`.
- As the list grows, gas costs increase linearly.
- Eventually, `graduateAndUpgrade()` will fail to complete, blocking core protocol logic.

---

## 🎯 Impact

- ❌ Teacher payments cannot be processed.
- ❌ Principal cannot receive wages.
- ❌ Contract cannot be upgraded to `levelTwo`.
- 🧊 Protocol lifecycle is halted, leading to governance paralysis.
- 🧱 Trust is eroded as teachers remain unpaid and upgrades are blocked.

---

## 🛠️ Tools Used

- Manual code review

---

## ✅ Recommendations

- **Pull Payments Pattern**: Accumulate balances and allow teachers to claim funds via `withdraw()` instead of pushing tokens.
- **Limit Teacher Count**: Cap `listOfTeachers.length` to a reasonable upper bound.
- **Batch Processing**: Introduce pagination logic (e.g., 10 teachers per call) for safe upgrades.
- **Gas Threshold Alerts**: Notify or revert if teacher count exceeds safe limits during enrollment.

---

## 📌 Judge’s Note

> *“Due to the use of a push system as regards payment of teacher wages, there is a risk of possible DoS as gas costs increase in direct proportion to the size of the teachers list.”*
