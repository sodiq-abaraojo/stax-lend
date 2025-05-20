# StaxLend - Decentralized Lending Protocol for Bitcoin & Stacks

A trustless, over-collateralized lending protocol enabling STX holders to unlock liquidity while maintaining Bitcoin ecosystem exposure.

## Key Features

- 🛡️ **150% Minimum Collateral Ratio**
- ⚠️ **130% Liquidation Threshold**
- 📈 **Dynamic Interest Rates** (5% base APY with protocol fee)
- 🔒 **Non-Custodial Design**
- ⚡ **Partial Repayments & Collateral Management**
- 🚨 **Liquidation Incentives with 5% Bonus**
- 📊 **Real-Time Loan Health Monitoring**

## Architecture Overview

### Components

1. **Core Smart Contract**  
   Manages all protocol operations including:
   - Collateral deposits/withdrawals
   - Loan origination and repayment
   - Interest accrual calculations
   - Liquidation engine
   - Protocol fee collection

2. **Data Structures**
   ```clarity
   ;; Loan record structure
   (define-map loans { loan-id: uint } {
     borrower: principal,
     collateral-amount: uint,
     loan-amount: uint,
     interest-accumulated: uint,
     creation-height: uint,
     last-interest-height: uint,
     status: (string-ascii 20)
   })
   ```

3. **Economic Model**
   - Interest Formula:  
     `Interest = Principal × (5% / 52560 blocks) × Blocks Elapsed`
   - Protocol Fee: 20% of interest generated
   - Liquidation Incentive: 5% of collateral

4. **Security Mechanisms**
   - Over-collateralization requirements
   - Time-based interest accrual
   - Pause functionality for emergencies
   - Reentrancy protection through STX-native transfers

## Core Workflows

### Loan Lifecycle

1. **Deposit Collateral**  
   Users lock STX into smart contract:
   ```clarity
   (deposit (amount uint))  // Returns new deposit balance
   ```

2. **Borrow Funds**  
   Create loan against collateral:
   ```clarity
   (borrow (collateral-amount uint) (loan-amount uint))  // Returns loan ID
   ```

3. **Interest Accrual**  
   Automatically calculated per-block:
   ```clarity
   Interest = Principal × 0.000000951 blocks⁻¹  // ~5% APY
   ```

4. **Repayment Options**  
   Flexible repayment strategies:
   ```clarity
   (repay-loan (loan-id uint) (repay-amount uint))  // Partial or full
   ```

5. **Liquidation Process**  
   Under-collateralized positions get liquidated:
   ```clarity
   (liquidate (loan-id uint))  // Liquidator receives collateral bonus
   ```

## Key Metrics

| Metric                | Formula                          | Example Value |
|-----------------------|----------------------------------|---------------|
| Collateral Ratio      | (Collateral × 1000) / Debt      | 1500 (150%)   |
| Liquidation Threshold | 130% of loan value              | $130 on $100  |
| Protocol Fee          | 20% of interest generated       | 1% APY        |
| Liquidation Bonus     | 5% of collateral value          | $5 on $100    |

## Security Considerations

### Risk Mitigation Strategies

1. **Collateral Buffers**
   - Minimum 150% initial ratio
   - 130% liquidation threshold

2. **Circuit Breakers**
   ```clarity
   (define-data-var paused bool false)
   (define-public (set-paused (paused-state bool))  // Admin-only
   ```

3. **Mathematical Safeguards**
   - All calculations use safe unsigned integers
   - Explicit overflow checks
   - Block-based time calculations

4. **Access Controls**
   - Critical functions restricted to contract owner
   - Borrower-only repayment enforcement

## Getting Started

### Prerequisites

- [Clarinet SDK](https://docs.hiro.so/clarinet)
- [Stacks.js](https://stacks.js.org/)
- Testnet STX tokens

### Sample Interaction Flow

1. Deposit collateral:
   ```bash
   clarinet contract call stax-lend deposit 5000000 --sender user1
   ```

2. Open loan position:
   ```bash
   clarinet contract call stax-lend borrow 3000000 2000000 --sender user1
   ```

3. Monitor loan health:
   ```bash
   clarinet contract call stax-lend get-loan-health 1 --sender user1
   ```

## Contributing

1. Fork repository
2. Create feature branch (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -am 'Add new feature'`)
4. Push branch (`git push origin feature/improvement`)
5. Create Pull Request
