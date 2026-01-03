# 🏦 Staking Vault - Complete Documentation

## Overview

A comprehensive staking platform deployed on **Monad Mainnet** that allows users to stake WMON tokens, earn rewards, and participate in a referral program. Built with upgradeable smart contracts using the UUPS proxy pattern.

---

## 📋 Deployed Contracts

### **Staking Vault V2** (Recommended - Enhanced Version)
- **Proxy Address**: `0x2C18eb6708cb7c03C19c7C793345bd22E9d261b8`
- **Implementation**: `0x4B69B14c81327E9C70e5d95Ab11776425E51b721`
- **Network**: Monad Mainnet (Chain ID: 143)
- **Owner**: `0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e`

### **Staking Vault V1** (Basic Version)
- **Proxy Address**: `0x12b3C66EE272B4FBC4fb935983C1BbB958e3f969`
- **Implementation**: `0x06b91F034a9013A0b3DF14435DAbf2eB71c781a9`

### **Token Addresses**
- **WMON**: `0x3bd359C1119dA7Da1D913D1C4D2B7c461115433A`
- **USDC**: `0x754704Bc059F8C67012fEd69BC8A327a5aafb603`

---

## 🎯 What Does This Contract Do?

### **Core Functionality**

#### 1. **Token Staking**
Users can stake WMON tokens for a specified lock period:
- Minimum duration: 1 day
- Maximum duration: Unlimited
- Lock periods prevent early withdrawal without penalty
- Automatic share calculation for rewards

#### 2. **Time-Locked Staking**
- Users choose lock duration when staking
- Longer locks may receive better rewards (configurable)
- Early withdrawal incurs a 5% penalty fee
- Normal withdrawal (after unlock) has no penalty

#### 3. **Fee Structure**
```
Deposit Fee: 1% (100 basis points)
Early Withdraw Fee: 5% (500 basis points)
Performance Fee: 10% (1000 basis points)
```

#### 4. **Fee Distribution** (V2 Only)
Fees are automatically split:
- **50%** → Protocol/Platform
- **30%** → Creator/Owner
- **20%** → Referrer (if applicable)

#### 5. **Referral System** (V2 Only)
- Users can stake with a referrer address
- Referrers earn 20% of all fees from their referrals
- Rewards are tracked and claimable
- No limit on number of referrals

#### 6. **Multi-Owner Governance** (V2 Only)
- Up to 10 owners can manage the contract
- Owners can:
  - Add/remove other owners
  - Configure tokens
  - Update fees
  - Pause/unpause contract
  - Update metadata

---

## 🔧 Key Features

### **V1 Features**
✅ Basic token staking with lock periods  
✅ Withdrawal after unlock time  
✅ Fee collection system  
✅ Uniswap V3 integration (for liquidity)  
✅ Upgradeable (UUPS proxy)  
✅ Pausable for emergencies  
✅ Owner-controlled configuration  

### **V2 Enhanced Features**
✅ **All V1 features PLUS:**  
✅ Multi-owner governance (up to 10 owners)  
✅ Referral system with automatic rewards  
✅ Gasless staking with EIP-2612 permit  
✅ Dynamic metadata (changeable name/symbol)  
✅ Advanced fee splits (protocol/creator/referrer)  
✅ Per-user stake limits (max 100,000 WMON)  
✅ Total vault cap (max 1,000,000 WMON)  
✅ Enhanced token configuration  

---

## 💡 How It Works

### **For Users (Stakers)**

#### **Step 1: Approve WMON**
```javascript
// Approve the staking contract to spend your WMON
await wmonContract.approve(VAULT_ADDRESS, amount);
```

#### **Step 2: Stake Tokens**
```javascript
// Stake with optional referrer
await vault.stake(
    WMON_ADDRESS,      // token to stake
    amount,            // amount in wei
    duration,          // lock time in seconds
    referrerAddress    // optional referrer (or 0x0)
);
```

#### **Step 3: Wait for Unlock**
- Your stake is locked for the chosen duration
- You can view your stakes and unlock times
- Early withdrawal possible but with 5% penalty

#### **Step 4: Withdraw**
```javascript
// After unlock time
await vault.withdraw(stakeId);
```

### **For Referrers**

1. **Share Your Address**: Give your wallet address to potential stakers
2. **Earn Automatically**: When they stake using your address, you get 20% of fees
3. **Track Rewards**: Check your accumulated rewards anytime
4. **Claim Anytime**: Rewards are tracked on-chain

### **For Administrators**

#### **Configure New Tokens**
```javascript
await vault.configureToken(
    tokenAddress,
    "Token Name",
    "SYMBOL",
    poolFee,           // 3000 = 0.3%
    pairedToken,       // USDC
    minLiquidity,      // minimum amount
    maxPerUser,        // 100000 * 10^18
    totalCap           // 1000000 * 10^18
);
```

#### **Update Fees**
```javascript
await vault.setFees(
    depositFee,        // basis points (100 = 1%)
    earlyWithdrawFee,  // basis points
    performanceFee     // basis points
);
```

#### **Add New Owner** (V2 only)
```javascript
await vault.addOwner(newOwnerAddress);
```

#### **Emergency Pause**
```javascript
await vault.pause();    // Stop all operations
await vault.unpause();  // Resume operations
```

---

## 📊 Fee Breakdown Example

Let's say Alice stakes **1000 WMON**:

### **At Deposit (1% fee)**
- Alice pays: 10 WMON fee
- Alice stakes: 990 WMON
- Fee distribution:
  - Protocol: 5 WMON (50%)
  - Creator: 3 WMON (30%)
  - Referrer: 2 WMON (20%)

### **Early Withdrawal (5% penalty)**
If Alice withdraws early before unlock:
- Penalty: 49.5 WMON (5% of 990)
- Alice receives: 940.5 WMON
- Penalty distributed same as above

### **Normal Withdrawal (No penalty)**
If Alice waits for unlock:
- Alice receives: 990 WMON (full amount)
- No additional fees

---

## 🔐 Security Features

### **Access Control**
- Only owners can configure contracts
- Only stake owner can withdraw their stakes
- Multi-signature governance (V2)
- Emergency pause functionality

### **Upgradeability**
- UUPS proxy pattern
- Only owner can upgrade
- Upgrades preserve all staked funds
- Can fix bugs or add features

### **Safety Checks**
- Reentrancy protection
- Integer overflow protection
- Zero address checks
- Sufficient balance checks
- Lock time validation

---

## 📈 Use Cases

### **1. Liquidity Mining**
- Users stake WMON to provide liquidity
- Earn fees from Uniswap V3 trading
- Long-term holders rewarded

### **2. Token Holding Incentive**
- Lock tokens to reduce circulating supply
- Create price stability
- Reward long-term believers

### **3. Referral Marketing**
- Grow community organically
- Incentivize promotion
- Track performance on-chain

### **4. Governance Participation**
- Multi-owner system for DAOs
- Decentralized decision making
- Transparent operations

---

## 🔍 Contract Architecture

```
┌─────────────────────────────────────┐
│                                     │
│          User Interface             │
│        (Web3 / MetaMask)            │
│                                     │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│                                     │
│        ERC1967 Proxy                │
│   (User-facing address)             │
│   0x2C18eb6708cb7c03...             │
│                                     │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│                                     │
│   StakingVaultV2 Implementation     │
│   (Logic contract)                  │
│   0x4B69B14c81327E9C70e5...         │
│                                     │
│   • Staking Logic                   │
│   • Fee Distribution                │
│   • Referral System                 │
│   • Multi-owner Management          │
│                                     │
└─────────────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│                                     │
│      External Contracts             │
│                                     │
│  • WMON Token                       │
│  • Uniswap V3 Position Manager      │
│  • Uniswap V3 Swap Router           │
│                                     │
└─────────────────────────────────────┘
```

---

## 🛠️ Technical Details

### **Storage Layout**

#### **User Stakes**
```solidity
struct Stake {
    address token;          // Token being staked
    uint256 amount;         // Amount staked (after fees)
    uint256 stakedAt;       // Timestamp of stake
    uint256 unlockTime;     // When can withdraw
    uint256 shares;         // Share calculation for rewards
    uint256 rewardDebt;     // For reward tracking
    address referrer;       // Who referred this user
    bool withdrawn;         // Has been withdrawn
    bool autoCompound;      // Auto-reinvest rewards
}
```

#### **Token Configuration**
```solidity
struct TokenConfig {
    bool enabled;           // Is staking enabled
    uint24 poolFee;         // Uniswap pool fee
    address pairedToken;    // Paired with (USDC)
    uint256 minLiquidity;   // Minimum to add liquidity
    uint256 maxStakePerUser;// Per-user limit
    uint256 totalStakeCap;  // Total vault limit
    string name;            // Token name
    string symbol;          // Token symbol
}
```

### **Events Emitted**

```solidity
// When user stakes
event TokenStaked(
    address indexed user,
    uint256 indexed stakeId,
    address indexed token,
    uint256 amount,
    uint256 unlockTime,
    address referrer
);

// When user withdraws
event StakeWithdrawn(
    address indexed user,
    uint256 indexed stakeId,
    uint256 amount,
    uint256 reward
);

// When token is configured
event TokenConfigured(
    address indexed token,
    string name,
    string symbol,
    uint24 fee,
    address pairedToken,
    uint256 minLiquidity
);

// When referrer earns reward
event ReferrerRewardPaid(
    address indexed referrer,
    address indexed token,
    uint256 amount
);
```

---

## 📱 Integration Guide

### **Web3.js Example**

```javascript
const Web3 = require('web3');
const web3 = new Web3('https://rpc.monad.xyz');

const VAULT_ADDRESS = '0x2C18eb6708cb7c03C19c7C793345bd22E9d261b8';
const VAULT_ABI = [...]; // See full ABI below

const vault = new web3.eth.Contract(VAULT_ABI, VAULT_ADDRESS);

// Stake tokens
async function stakeTokens(amount, duration, referrer) {
    const accounts = await web3.eth.getAccounts();
    
    // 1. Approve WMON
    await wmonContract.methods.approve(
        VAULT_ADDRESS, 
        amount
    ).send({ from: accounts[0] });
    
    // 2. Stake
    await vault.methods.stake(
        WMON_ADDRESS,
        amount,
        duration,
        referrer || '0x0000000000000000000000000000000000000000'
    ).send({ from: accounts[0] });
}

// Get user's stakes
async function getUserStakes(userAddress) {
    const stakeIds = await vault.methods.getUserStakes(userAddress).call();
    
    const stakes = [];
    for (let id of stakeIds) {
        const info = await vault.methods.getStakeInfo(userAddress, id).call();
        stakes.push({
            id: id,
            token: info[0],
            amount: info[1],
            stakedAt: info[2],
            unlockTime: info[3],
            withdrawn: info[4],
            canWithdraw: info[5],
            referrer: info[6]
        });
    }
    
    return stakes;
}
```

### **Ethers.js Example**

```javascript
const ethers = require('ethers');

const provider = new ethers.providers.JsonRpcProvider('https://rpc.monad.xyz');
const wallet = new ethers.Wallet(PRIVATE_KEY, provider);

const vault = new ethers.Contract(VAULT_ADDRESS, VAULT_ABI, wallet);

// Stake with referrer
async function stake(amount, days, referrerAddress) {
    const duration = days * 24 * 60 * 60; // Convert days to seconds
    const amountWei = ethers.utils.parseEther(amount.toString());
    
    const tx = await vault.stake(
        WMON_ADDRESS,
        amountWei,
        duration,
        referrerAddress || ethers.constants.AddressZero
    );
    
    await tx.wait();
    console.log('Staked successfully!');
}

// Withdraw stake
async function withdraw(stakeId) {
    const tx = await vault.withdraw(stakeId);
    await tx.wait();
    console.log('Withdrawn successfully!');
}
```

---

## 🎓 FAQ

### **Q: Can I withdraw my stake early?**
A: Yes, but you'll pay a 5% early withdrawal penalty. It's better to wait for the unlock time.

### **Q: What happens to the fees?**
A: Fees are split: 50% to protocol, 30% to creator, 20% to referrer (if any).

### **Q: How do referrals work?**
A: When staking, enter a referrer's address. They automatically get 20% of your fees.

### **Q: Can I stake multiple times?**
A: Yes! Each stake is tracked separately with its own ID and unlock time.

### **Q: What's the maximum I can stake?**
A: Per user: 100,000 WMON. Total vault: 1,000,000 WMON.

### **Q: Is my money safe?**
A: Yes. The contract is:
- Auditable (open source)
- Upgradeable (but only by owner)
- Pausable (for emergencies)
- Battle-tested OpenZeppelin libraries

### **Q: What if I lose my referrer rewards?**
A: Referrer rewards are tracked on-chain. They never expire and can be claimed anytime.

### **Q: Can owners steal my funds?**
A: No. Owners can only:
- Configure settings
- Pause/unpause
- Add/remove other owners
They cannot withdraw user stakes.

---

## 🚀 Deployment Information

### **Deployment Dates**
- V1 Deployed: Block 46313284
- V2 Deployed: Block 46317099
- WMON Configured: Block 46318921

### **Gas Costs**
- Deploy V2: ~0.504 ETH
- Configure Token: ~0.030 ETH
- Stake: ~0.15-0.20 ETH
- Withdraw: ~0.10-0.15 ETH

### **Network Details**
- Chain: Monad Mainnet
- Chain ID: 143
- RPC: https://rpc.monad.xyz
- Explorer: https://monadvision.com

---

## 📞 Support & Links

### **Smart Contracts**
- V2 Proxy: [View on MonadVision](https://monadvision.com/address/0x2C18eb6708cb7c03C19c7C793345bd22E9d261b8)
- V2 Implementation: [View on MonadVision](https://monadvision.com/address/0x4B69B14c81327E9C70e5d95Ab11776425E51b721)

### **Useful Resources**
- Monad Docs: https://docs.monad.xyz
- Uniswap V3 Docs: https://docs.uniswap.org/protocol/V3
- OpenZeppelin Contracts: https://docs.openzeppelin.com

### **Owner Contact**
- Owner Address: `0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e`

---

## ⚠️ Disclaimer

This smart contract system is provided "as is" without warranty of any kind. Use at your own risk. Always:
- Test with small amounts first
- Understand the lock periods
- Know the fee structure
- Keep your private keys safe
- Never share your seed phrase

---

## 📄 License

MIT License - See contract source code for details.

---

**Last Updated**: January 3, 2026  
**Version**: 2.0  
**Status**: ✅ Production Ready
"external_url": "https://moon.monad.xyz/donate/0x592B35c8917eD36c39Ef73D0F5e92B0173560b2e"
