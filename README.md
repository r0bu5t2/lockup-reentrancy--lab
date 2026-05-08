🔥 Lockup Reentrancy Lab

A hands-on security research project demonstrating a real-world reentrancy vulnerability in a token lockup contract, along with a patched implementation.

---

📌 Overview

This repo contains:

- ❌ Vulnerable lockup contract
- 💥 Exploit using malicious token
- ✅ Patched contract (CEI pattern)
- 🧪 Foundry tests + invariant testing

---

🧨 Vulnerability

The vulnerable contract performs:

token.transfer(...)
balances[msg.sender] = 0;

This violates the Checks-Effects-Interactions pattern and enables reentrancy.

---

💥 Exploit Demo

Before exploit

"Before" (screenshots/balances.png)

Exploit execution

"Exploit" (screenshots/exploit.png)

After exploit

Attacker drains more than intended due to repeated withdrawals.

---

🛡️ Fix

The patched version applies:

balances[msg.sender] = 0;
token.transfer(...)

Result

"Fixed" (screenshots/fixed.png)

---

🚀 Run Locally

forge build
forge test -vv

Start local chain:

anvil

Run exploit:

./script.sh

---

🌿 Branches

- "main" → vulnerable version
- "patched" → fixed version

---

⚠️ Disclaimer

This project is for educational purposes only.
Do NOT use vulnerable code in production.

---

🧠 Key Takeaway

«Reentrancy is not about whether callbacks exist —
it’s about when state changes relative to external calls.»
