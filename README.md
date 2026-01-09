# Staking Vault — README

A comprehensive staking platform deployed on Monad Mainnet that allows users to stake WMON tokens, earn rewards, and participate in a referral program. Built with upgradeable smart contracts using the UUPS proxy pattern.

---

## Table of Contents

- [Overview](#overview)
- [Deployed Contracts & Addresses](#deployed-contracts--addresses)
- [Token Logos](#token-logos)
- [What the Contract Does](#what-the-contract-does)
- [Key Features](#key-features)
- [How It Works](#how-it-works)
  - [For Users (Stakers)](#for-users-stakers)
  - [For Referrers](#for-referrers)
  - [For Administrators](#for-administrators)
- [Fee Breakdown Example](#fee-breakdown-example)
- [Security Features](#security-features)
- [Contract Architecture](#contract-architecture)
- [Technical Details](#technical-details)
  - [Storage Layout](#storage-layout)
  - [Events](#events)
- [Integration Guide](#integration-guide)
  - [Ethers.js Example](#ethersjs-example)
  - [Web3.js Example](#web3js-example)
- [FAQ](#faq)
- [Deployment Information & Gas Costs](#deployment-information--gas-costs)
- [Support & Links](#support--links)
- [Disclaimer & License](#disclaimer--license)

---

## Overview

This project is a feature-rich staking vault for WMON on Monad Mainnet. It supports time-locked staking, fee distribution, referrals, multi-owner governance (V2), and more. V2 is recommended for production usage.

---

## Deployed Contracts & Addresses

### Staking Vault V2 (Recommended - Enhanced Version)
- Proxy Address: `0x2C18eb6708cb7c03C19c7C793345bd22E9d261b8`  
- Implementation: `0x4B69B14c81327E9C70e5d95Ab11776425E51b721`  
- Network: Monad Mainnet (Chain ID: 143)  
- Owner: `0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e`

### Staking Vault V1 (Basic Version)
- Proxy Address: `0x12b3C66EE272B4FBC4fb935983C1BbB958e3f969`  
- Implementation: `0x06b91F034a9013A0b3DF14435DAbf2eB71c781a9`

### Token Addresses
- WMON: `0x3bd359C1119dA7Da1D913D1C4D2B7c461115433A`  
- USDC: `0x754704Bc059F8C67012fEd69BC8A327a5aafb603`  
- BITCOINFLASH (BTCF): `0x7d7E0112d2763c98238aF7cebEAe33d53F3F76DD`

---

## Token Logos

Included below are token logo images used across the UI and documentation. You can embed these in the UI or README; if you want to add more token logos, add a URL and the symbol to the list.

| Logo | Symbol | Token | Address |
|---:|:---:|:---|:---|
| <img src="https://files.catbox.moe/hwlhih.png" alt="WMON" width="36" height="36"> | WMON | Wrapped MONAD (WMON) | `0x3bd359C1119dA7Da1D913D1C4D2B7c461115433A` |
| <img src="https://files.catbox.moe/ujzf30.gif" alt="USDC" width="36" height="36"> | USDC | USD Coin | `0x754704Bc059F8C67012fEd69BC8A327a5aafb603` |
| <img src="https://files.catbox.moe/lk7mrk.png" alt="BTCF" width="36" height="36"> | BTCF | BitcoinFlash | `0x7d7E0112d2763c98238aF7cebEAe33d53F3F76DD` |

How to add a new token logo (example for maintainers):
1. Upload a PNG/GIF/SVG to a public CDN.
2. Add the URL and symbol to the "Token Logos" table above.
3. In the UI, map the token address to the image URL (see token logo mapping in the frontend).

---

## What the Contract Does

Core functionality:
- Token staking (WMON) for a chosen lock period with share-based reward calculation.
- Time-locked staking — longer lock durations receive better rewards.
- Early withdrawal permitted with a penalty.
- Fee collection and distribution, including referral reward distribution (V2).
- Multi-owner governance (V2) and upgradeability via UUPS proxy.

Fees and referral distribution (V2):
- Deposit Fee: 1% (100 basis points)
- Early Withdraw Fee: 5% (500 basis points)
- Performance Fee: 10% (1000 basis points)
- Fee split (V2): 50% protocol, 30% creator, 20% referrer (when applicable)

---

## Key Features

### V1 (Base)
- Basic token staking with lock periods
- Withdraw after unlock time
- Fee collection system
- Uniswap V3 integration for liquidity (where applicable)
- Upgradeable via UUPS
- Pausable for emergencies
- Owner-controlled configuration

### V2 (Enhanced)
Includes all V1 features plus:
- Multi-owner governance (up to 10 owners)
- Referral system with on-chain rewards
- Gasless staking via EIP-2612 permit
- Dynamic metadata (name / symbol updatable)
- Advanced fee splits and per-recipient fee routing
- Per-user stake limits (e.g., max 100,000 WMON)
- Total vault cap (e.g., max 1,000,000 WMON)
- Enhanced token configuration options

---

## How It Works

### For Users (Stakers)
1. Approve WMON:
   ```js
   await wmonContract.approve(VAULT_ADDRESS, amount);
   ```
2. Stake tokens:
   ```js
   await vault.stake(
     WMON_ADDRESS,      // token to stake
     amount,            // amount in wei
     duration,          // lock time in seconds
     referrerAddress    // optional referrer (or 0x0)
   );
   ```
3. Wait for unlock (or withdraw early with penalty).
4. Withdraw when eligible:
   ```js
   await vault.withdraw(stakeId);
   ```
5. View stakes:
   ```js
   const stakeIds = await vault.getUserStakes(userAddress);
   const stakeInfo = await vault.getStakeInfo(userAddress, stakeIds[0]);
   ```

### For Referrers
- Share your address with potential stakers.
- Earn 20% of fees collected from referred users (V2).
- Referrer rewards are tracked on-chain and claimable.

### For Administrators
- Configure new tokens (per-token settings: min liquidity, fees, caps).
- Update fees, add/remove owners (V2), pause/unpause, update metadata.
- Example (configure token):
  ```js
  await vault.configureToken(
    tokenAddress,
    "Token Name",
    "SYMBOL",
    poolFee,          // 3000 = 0.3%
    pairedToken,      // USDC
    minLiquidity,
    maxPerUser,       // e.g., 100000 * 10**18
    totalCap          // e.g., 1000000 * 10**18
  );
  ```
- Update fees:
  ```js
  await vault.setFees(depositFeeBps, earlyWithdrawFeeBps, performanceFeeBps);
  ```
- Owner management (V2):
  ```js
  await vault.addOwner(newOwnerAddress);
  await vault.removeOwner(ownerAddress);
  ```

---

## Fee Breakdown Example

Stake 1000 WMON:
- 1% deposit fee = 10 WMON → 990 WMON staked
- Fee split: 5 WMON protocol, 3 WMON creator, 2 WMON referrer (if provided)

Early withdraw example:
- 5% penalty on staked amount: on 990 WMON → 49.5 WMON penalty
- Returned: 990 - 49.5 = 940.5 WMON (plus or minus other adjustments)

---

## Security Features

- Access control for owner-only operations
- Multi-owner governance support (V2)
- Emergency pause/unpause
- Upgradeable (UUPS) — only owner(s) can upgrade
- Use of OpenZeppelin libraries: reentrancy guards, SafeMath-like checks, zero-address protections

---

## Contract Architecture

A simplified view:

User Interface (Web3 / MetaMask)  
↓  
ERC1967 Proxy (user-facing) — `0x2C18eb...`  
↓  
Implementation (StakingVaultV2) — `0x4B69B1...`  
↓  
External Contracts (WMON, Uniswap V3, etc.)

---

## Technical Details

### Storage Layout (high level)
User stakes:
```solidity
struct Stake {
  address token;
  uint256 amount;
  uint256 stakedAt;
  uint256 unlockTime;
  uint256 shares;
  uint256 rewardDebt;
  address referrer;
  bool withdrawn;
  bool autoCompound;
}
```

Token configuration:
```solidity
struct TokenConfig {
  bool enabled;
  uint24 poolFee;
  address pairedToken;
  uint256 minLiquidity;
  uint256 maxStakePerUser;
  uint256 totalStakeCap;
  string name;
  string symbol;
}
```

### Events
- TokenStaked(address indexed user, uint256 indexed stakeId, address indexed token, uint256 amount, uint256 unlockTime, address referrer)  
- StakeWithdrawn(address indexed user, uint256 indexed stakeId, uint256 amount, uint256 reward)  
- TokenConfigured(address indexed token, string name, string symbol, uint24 fee, address pairedToken, uint256 minLiquidity)  
- ReferrerRewardPaid(address indexed referrer, address indexed token, uint256 amount)

---

## Integration Guide

### Ethers.js Example
```js
const { ethers } = require("ethers");

const provider = new ethers.providers.JsonRpcProvider("https://rpc.monad.xyz");
const wallet = new ethers.Wallet(PRIVATE_KEY, provider);
const vault = new ethers.Contract(VAULT_ADDRESS, VAULT_ABI, wallet);
const wmonContract = new ethers.Contract(WMON_ADDRESS, ERC20_ABI, wallet);

// Approve
await wmonContract.approve(VAULT_ADDRESS, amount);

// Stake
await vault.stake(WMON_ADDRESS, amount, duration, referrerAddress);

// Withdraw
await vault.withdraw(stakeId);

// Get stakes
const stakeIds = await vault.getUserStakes(userAddress);
const stakeInfo = await vault.getStakeInfo(userAddress, stakeIds[0]);
```

### Web3.js Example
```js
const Web3 = require("web3");
const web3 = new Web3("https://rpc.monad.xyz");
const vault = new web3.eth.Contract(VAULT_ABI, VAULT_ADDRESS);

// Approve (assumes wmonContract is instantiated)
await wmonContract.methods.approve(VAULT_ADDRESS, amount).send({ from: user });

// Stake
await vault.methods.stake(WMON_ADDRESS, amount, duration, referrer).send({ from: user });

// Withdraw
await vault.methods.withdraw(stakeId).send({ from: user });
```

---

## FAQ

Q: Can I withdraw my stake early?  
A: Yes — early withdrawal is allowed but incurs a 5% penalty.

Q: What happens to collected fees?  
A: Fees are split: 50% protocol, 30% creator, 20% referrer (V2).

Q: How do referrals work?  
A: Provide a referrer's address when staking. Referrer receives 20% of fees automatically (V2).

Q: Can I stake multiple times?  
A: Yes. Each stake is tracked separately with its own ID and unlock time.

Q: What are staking limits?  
A: Per-user (V2): 100,000 WMON. Vault total cap (V2): 1,000,000 WMON.

Q: Is my money safe?  
A: The contracts are auditable and use OpenZeppelin standards, but always test with small amounts and understand the risks.

Q: Can owners withdraw user funds?  
A: No. Owners can configure and manage the vault but cannot withdraw stake balances belonging to users.

---

## Deployment Information & Gas Costs

- V1 Deployed: Block `46313284`  
- V2 Deployed: Block `46317099`  
- WMON Configured: Block `46318921`

Approximate gas costs:
- Deploy V2: ~0.504 ETH
- Configure Token: ~0.030 ETH
- Stake: ~0.15–0.20 ETH
- Withdraw: ~0.10–0.15 ETH

Network:
- Name: Monad Mainnet  
- Chain ID: `143`  
- RPC: https://rpc.monad.xyz  
- Explorer: https://monadvision.com

---

## Support & Links

- V2 Proxy on MonadVision: (use explorer to view the proxy address)  
- V2 Implementation on MonadVision: (use explorer to view implementation address)  
- Monad Docs: https://monad.xyz (or project-specific docs)  
- Uniswap V3 Docs: https://docs.uniswap.org  
- OpenZeppelin Contracts: https://docs.openzeppelin.com

Owner: `0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e`

**Donate (UI + README):**  
The donate flow uses two links:

1. Preferred (MonadVision wallet/profile)
- Opens the owner's MonadVision wallet/profile page (useful if the user wants to open the owner's portfolio/wallet UI):  
  https://monadvision.com/myspace?feature=Wallet&type=Portfolio&address=0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e

2. Fallback (direct donation link)
- Direct donation page (works if a dedicated donation widget is hosted there):  
  https://moon.monad.xyz/donate/0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e

If the "donate" button in your UI wasn't working because it pointed only to the fallback or used a relative link, make it explicit and include both: open the MonadVision link first (for wallet UI), and show the fallback link if the user has trouble.

---

## Donate button: suggested front-end fix

Add a donate button (or update existing donate link) like this in your UI HTML:

```html
<!-- Example donate button -->
<a id="donateBtn" class="btn" target="_blank" rel="noopener noreferrer"
   href="https://monadvision.com/myspace?feature=Wallet&type=Portfolio&address=0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e">
  Donate
</a>
```

And include the small JS helper (place before </body>) to set both primary (MonadVision) and fallback (moon) links and show fallback to the user when needed:

```javascript name=donate-fix.js
// Donate button helper — try MonadVision as primary, show fallback link tooltip/label
(function () {
  const OWNER = '0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e';
  const MONADVISION_URL = `https://monadvision.com/myspace?feature=Wallet&type=Portfolio&address=${OWNER}`;
  const MOON_DONATE_URL = `https://moon.monad.xyz/donate/${OWNER}`;

  const btn = document.getElementById('donateBtn');
  if (!btn) return;

  // Primary target: MonadVision profile/wallet view
  btn.href = MONADVISION_URL;
  btn.target = '_blank';
  btn.rel = 'noopener noreferrer';

  // Provide a tooltip or small fallback action if MonacoVision doesn't open properly:
  btn.addEventListener('auxclick', () => { /* middle-click works as normal */ });

  // On long-press / right-click we can't detect, but provide a small fallback UI on normal click if needed:
  btn.addEventListener('click', (e) => {
    // give browser a moment to open external site; optionally show fallback if popup blocked
    setTimeout(async () => {
      // optional: for browsers with popup blocking, user will still be able to copy fallback link
      // We won't try to fetch cross-origin resources (CORS) — just provide the fallback to user.
      // Show a small confirmation offering the fallback link (non-blocking)
      if (!confirm('Open MonadVision wallet? Click "Cancel" to copy a direct donate link instead.')) {
        e.preventDefault();
        try {
          await navigator.clipboard.writeText(MOON_DONATE_URL);
          alert('Fallback donation link copied to clipboard:\n' + MOON_DONATE_URL);
        } catch (err) {
          // If clipboard fails, open fallback in new tab
          window.open(MOON_DONATE_URL, '_blank', 'noopener');
        }
      }
    }, 150);
    // Let the default navigation happen (opening MonadVision in new tab).
  });
})();
```

This approach:
- Opens MonadVision first (preferred wallet/profile UI).
- If user cancels the confirm prompt, copies the fallback donate link to clipboard or opens it in a new tab.
- Avoids CORS/fetch issues (no cross-origin fetch attempts).

---

## Disclaimer & License

This smart contract system is provided "as is" without warranty. Use at your own risk.

- Test with small amounts before production usage.
- Understand lock periods and the fee structure.
- Keep private keys secure; never share seed phrases.

License: **MIT** — see contract source for details.

---

_Last Updated: January 3, 2026 • Version: 2.0 • ✅ Production Ready_
