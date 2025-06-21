# 🏫 Hawk High Audit — First Flights Round

## 📝 Summary

Welcome to Hawk High, enroll, avoid bad reviews, and graduate!!!  
You have been contracted to review the upgradeable contracts for the Hawk High School which will be launched very soon. These contracts utilize the UUPSUpgradeable library from OpenZeppelin. At the end of the school session (4 weeks), the system is upgraded to a new one.

---

## 📘 About

Welcome to **Hawk High**, enroll, avoid bad reviews, and graduate!!!  
You have been contracted to review the upgradeable contracts for the Hawk High School which will be launched very soon.  
These contracts utilize the `UUPSUpgradeable` library from OpenZeppelin.  
At the end of the school session (4 weeks), the system is upgraded to a new one.

---

## 🎭 Actors

- **Principal**:  
  In charge of hiring/firing teachers, starting the school session, and upgrading the system at the end of the school session.  
  Will receive 5% of all school fees paid as wages. Can also expel students who break rules.

- **Teachers**:  
  In charge of giving reviews to students at the end of each week.  
  Will share in 35% of all school fees paid as their wages.

- **Student**:  
  Will pay a school fee when enrolling in Hawk High School.  
  Will get a review each week.  
  If they fail to meet the cutoff score at the end of the school session, they will not graduate to the next level when the `Principal` upgrades the system.

---

## ✅ Invariants

- A school session lasts **4 weeks**
- USDC used in the contract has **18 decimals**
- Wages are to be paid **only** when the `graduateAndUpgrade()` function is called by the `Principal`
- Payment structure:
  - `Principal` receives **5%** of `bursary`
  - `Teachers` share **35%** of `bursary`
  - Remaining **60%** stays in the bursary after upgrade
- Students can be reviewed **only once per week**
- All students **must receive 4 reviews** before upgrade
- Students **below cutOffScore do not graduate**
- Upgrade can only occur **after sessionEnd timestamp**

---

## 📚 Resources

To learn more about Upgradeable Contracts:  
🔗 [Updraft — Upgradeable Smart Contracts](https://updraft.cyfrin.io/courses/advanced-foundry/upgradeable-smart-contracts/introduction-to-upgradeable-smart-contracts)
