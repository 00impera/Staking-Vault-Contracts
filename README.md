# 🏦 Staking Vault - Complete Documentation

A comprehensive staking platform deployed on Monad Mainnet that allows users to stake WMON tokens, earn rewards, and participate in a referral program. Built with upgradeable smart contracts using the UUPS proxy pattern.

## 📋 Deployed Contracts

### Staking Vault V2 (Recommended - Enhanced Version)
- **Proxy Address**: `0x2C18eb6708cb7c03C19c7C793345bd22E9d261b8`
- **Implementation**: `0x4B69B14c81327E9C70e5d95Ab11776425E51b721`
- **Network**: Monad Mainnet (Chain ID: 143)
- **Owner**: `0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e`

### Staking Vault V1 (Basic Version)
- **Proxy Address**: `0x12b3C66EE272B4FBC4fb935983C1BbB958e3f969`
- **Implementation**: `0x06b91F034a9013A0b3DF14435DAbf2eB71c781a9`

### Token Addresses
- **WMON**: `0x3bd359C1119dA7Da1D913D1C4D2B7c461115433A`
- **USDC**: `0x754704Bc059F8C67012fEd69BC8A327a5aafb603`

## 🎯 What Does This Contract Do?

### Core Functionality

**Token Staking**: Stake WMON tokens for a lock period (minimum 1 day, no maximum) with automatic share calculation for rewards.

**Time-Locked Staking**: Choose your lock duration. Longer locks earn better rewards. Early withdrawal incurs a 5% penalty.

### Fee Structure
- **Deposit Fee**: 1% (100 basis points)
- **Early Withdraw Fee**: 5% (500 basis points)
- **Performance Fee**: 10% (1000 basis points)

### Fee Distribution (V2 Only)
- 50% → Protocol/Platform
- 30% → Creator/Owner
- 20% → Referrer (if applicable)

### Referral System (V2 Only)
- Stake with a referrer address
- Referrers earn 20% of all fees from their referrals
- Rewards are tracked on-chain and claimable anytime

### Multi-Owner Governance (V2 Only)
- Support for up to 10 owners
- Owners can add/remove other owners, configure tokens, update fees, pause/unpause, and update metadata

## 🔧 Key Features

### V1 Features
- ✅ Basic token staking with lock periods
- ✅ Withdrawal after unlock time
- ✅ Fee collection system
- ✅ Uniswap V3 integration for liquidity
- ✅ Upgradeable (UUPS proxy)
- ✅ Pausable for emergencies
- ✅ Owner-controlled configuration

### V2 Enhanced Features
**All V1 features PLUS:**
- ✅ Multi-owner governance (up to 10 owners)
- ✅ Referral system with automatic rewards
- ✅ Gasless staking with EIP-2612 permit
- ✅ Dynamic metadata (changeable name/symbol)
- ✅ Advanced fee splits (protocol/creator/referrer)
- ✅ Per-user stake limits (max 100,000 WMON)
- ✅ Total vault cap (max 1,000,000 WMON)
- ✅ Enhanced token configuration

## 💡 How It Works

### For Users (Stakers)

**1. Approve WMON**
```javascript
await wmonContract.approve(VAULT_ADDRESS, amount);
```

**2. Stake Tokens**
```javascript
await vault.stake(
  WMON_ADDRESS,      // token to stake
  amount,            // amount in wei
  duration,          // lock time in seconds
  referrerAddress    // optional referrer (or 0x0)
);
```

**3. Wait for Unlock**
- Stake is locked for chosen duration
- Early withdrawal possible with 5% penalty

**4. Withdraw**
```javascript
await vault.withdraw(stakeId);
```

**5. View Stakes**
```javascript
const stakeIds = await vault.getUserStakes(userAddress);
const stakeInfo = await vault.getStakeInfo(userAddress, stakeIds[0]);
```

### For Referrers

1. Share your address with potential stakers
2. Earn automatically when they stake using your address (20% of fees)
3. Track rewards on-chain anytime
4. Claim rewards whenever you want

### For Administrators

**Configure New Tokens**
```javascript
await vault.configureToken(
  tokenAddress,
  "Token Name",
  "SYMBOL",
  poolFee,          // 3000 = 0.3%
  pairedToken,      // USDC
  minLiquidity,     // minimum amount
  maxPerUser,       // 100000 * 10^18
  totalCap          // 1000000 * 10^18
);
```

**Update Fees**
```javascript
await vault.setFees(
  depositFee,         // basis points (100 = 1%)
  earlyWithdrawFee,   // basis points
  performanceFee      // basis points
);
```

**Add/Remove Owner (V2 only)**
```javascript
await vault.addOwner(newOwnerAddress);
await vault.removeOwner(ownerAddress);
```

**Emergency Pause**
```javascript
await vault.pause();      // Stop all operations
await vault.unpause();    // Resume operations
```

**Update Metadata**
```javascript
await vault.setMetadata(
  "https://your-metadata-uri",
  "New Vault Name",
  "NEW"
);
```

**Upgrade Contract (Owner Only)**
```javascript
await vault.upgradeToAndCall(newImplementationAddress, "0x");
```

## 📊 Fee Breakdown Example

**Stake 1000 WMON:**
- 1% deposit fee = 10 WMON
- 990 WMON staked
- Fee split: 5 WMON protocol, 3 WMON creator, 2 WMON referrer

**Early withdraw:**
- 5% penalty on 990 = 49.5 WMON
- 940.5 WMON returned

## 🔐 Security Features

- **Access Control**: Only owners can configure contracts, only stake owner can withdraw
- **Multi-signature governance**: V2 supports up to 10 owners
- **Emergency pause**: For security incidents
- **Upgradeable**: UUPS proxy pattern, only owner can upgrade
- **OpenZeppelin libraries**: Reentrancy guards, overflow protection, zero address checks, balance validation

## 📈 Use Cases

- **Liquidity Mining**: Stake WMON, provide liquidity, earn Uniswap V3 fees
- **Token Holding Incentive**: Lock tokens to reduce circulating supply and reward long-term holders
- **Referral Marketing**: Grow community, incentivize promotion, track performance on-chain
- **Governance Participation**: Multi-owner system for DAOs and decentralized decision making

## 🔍 Contract Architecture

```
┌─────────────────────────────────────┐
│         User Interface              │
│       (Web3 / MetaMask)             │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│         ERC1967 Proxy               │
│      (User-facing address)          │
│   0x2C18eb6708cb7c03...             │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│   StakingVaultV2 Implementation     │
│        (Logic contract)             │
│   0x4B69B14c81327E9C70e5...         │
│   • Staking Logic                   │
│   • Fee Distribution                │
│   • Referral System                 │
│   • Multi-owner Management          │
└─────────────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│       External Contracts            │
│   • WMON Token                      │
│   • Uniswap V3 Position Manager     │
│   • Uniswap V3 Swap Router          │
└─────────────────────────────────────┘
```

## 🛠️ Technical Details

### Storage Layout

**User Stakes**
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

**Token Configuration**
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

### Events Emitted

```solidity
event TokenStaked(
    address indexed user,
    uint256 indexed stakeId,
    address indexed token,
    uint256 amount,
    uint256 unlockTime,
    address referrer
);

event StakeWithdrawn(
    address indexed user,
    uint256 indexed stakeId,
    uint256 amount,
    uint256 reward
);

event TokenConfigured(
    address indexed token,
    string name,
    string symbol,
    uint24 fee,
    address pairedToken,
    uint256 minLiquidity
);

event ReferrerRewardPaid(
    address indexed referrer,
    address indexed token,
    uint256 amount
);
```

## 📱 Integration Guide

### Ethers.js Example

```javascript
const ethers = require("ethers");

const provider = new ethers.providers.JsonRpcProvider("https://rpc.monad.xyz");
const wallet = new ethers.Wallet(PRIVATE_KEY, provider);
const vault = new ethers.Contract(VAULT_ADDRESS, VAULT_ABI, wallet);

// Approve WMON
await wmonContract.approve(VAULT_ADDRESS, amount);

// Stake
await vault.stake(WMON_ADDRESS, amount, duration, referrerAddress);

// Withdraw
await vault.withdraw(stakeId);

// Get user stakes
const stakeIds = await vault.getUserStakes(userAddress);
const stakeInfo = await vault.getStakeInfo(userAddress, stakeIds[0]);
```

### Web3.js Example

```javascript
const Web3 = require("web3");

const web3 = new Web3("https://rpc.monad.xyz");
const vault = new web3.eth.Contract(VAULT_ABI, VAULT_ADDRESS);

// Approve WMON
await wmonContract.methods.approve(VAULT_ADDRESS, amount).send({ from: user });

// Stake
await vault.methods
    .stake(WMON_ADDRESS, amount, duration, referrer)
    .send({ from: user });

// Withdraw
await vault.methods.withdraw(stakeId).send({ from: user });
```

## 🎓 FAQ

**Q: Can I withdraw my stake early?**  
A: Yes, but you'll pay a 5% early withdrawal penalty. It's better to wait for the unlock time.

**Q: What happens to the fees?**  
A: Fees are split: 50% to protocol, 30% to creator, 20% to referrer (if any).

**Q: How do referrals work?**  
A: When staking, enter a referrer's address. They automatically get 20% of your fees.

**Q: Can I stake multiple times?**  
A: Yes! Each stake is tracked separately with its own ID and unlock time.

**Q: What's the maximum I can stake?**  
A: Per user: 100,000 WMON. Total vault: 1,000,000 WMON.

**Q: Is my money safe?**  
A: The contract is auditable (open source), upgradeable (but only by owner), pausable (for emergencies), and built with battle-tested OpenZeppelin libraries.

**Q: What if I lose my referrer rewards?**  
A: Referrer rewards are tracked on-chain. They never expire and can be claimed anytime.

**Q: Can owners steal my funds?**  
A: No. Owners can only configure, pause/unpause, and add/remove other owners. They cannot withdraw user stakes.

## 🚀 Deployment Information

- **V1 Deployed**: Block 46313284
- **V2 Deployed**: Block 46317099
- **WMON Configured**: Block 46318921

### Gas Costs (Approximate)
- Deploy V2: ~0.504 ETH
- Configure Token: ~0.030 ETH
- Stake: ~0.15-0.20 ETH
- Withdraw: ~0.10-0.15 ETH

### Network Details
- **Network**: Monad Mainnet
- **Chain ID**: 143
- **RPC**: https://rpc.monad.xyz
- **Explorer**: https://monadvision.com

## 📞 Support & Links

- [V2 Proxy on MonadVision](https://monadvision.com/address/0x2C18eb6708cb7c03C19c7C793345bd22E9d261b8)
- [V2 Implementation on MonadVision](https://monadvision.com/address/0x4B69B14c81327E9C70e5d95Ab11776425E51b721)
- [Monad Docs](https://docs.monad.xyz)
- [Uniswap V3 Docs](https://docs.uniswap.org/protocol/V3)
- [OpenZeppelin Contracts](https://docs.openzeppelin.com)
- **Owner**: `0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e`
- **Donate**: [moon.monad.xyz/donate/0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e](https://moon.monad.xyz/donate/0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e)

## ⚠️ Disclaimer

This smart contract system is provided "as is" without warranty of any kind. Use at your own risk.

- Test with small amounts first
- Understand the lock periods
- Know the fee structure
- Keep your private keys safe
- Never share your seed phrase

## 📄 License

MIT License - See contract source code for details.

---

**Last Updated**: January 3, 2026 • **Version**: 2.0 • ✅ Production Ready
