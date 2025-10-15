# StackVaultTrade

A Clarity smart contract that implements a simple STX vault, staking system, and peer-to-peer trade marketplace. Designed for local development with Clarinet and for integration with frontend code using the Stacks JS libraries.

## Features

- User vault (deposit / withdraw)
- Simple staking with a built-in 10% reward and single-claim flow
- P2P trade orders (create, accept, cancel)
- Basic access control for admin-only actions
- Deterministic on-chain storage using maps and data-vars

## Quick overview of on-chain storage & constants

- Maps:
  - `user-balances` { user: principal } -> { balance: uint }
  - `user-stakes` { user: principal } -> { amount: uint, reward: uint, active: bool }
  - `trade-orders` { id: uint } -> { seller: principal, buyer: (optional principal), price: uint, active: bool }
- Data variables:
  - `total-orders` uint
  - `total-staked` uint
  - `admin` principal
- Error constants:
  - `ERR_NOT_ENOUGH_FUNDS` (u100)
  - `ERR_UNAUTHORIZED` (u101)
  - `ERR_ALREADY_STAKED` (u102)
  - `ERR_NOT_STAKED` (u103)
  - `ERR_INVALID_AMOUNT` (u104)
  - `ERR_NO_SUCH_ORDER` (u105)

## Contract public functions

- deposit(amount uint)
  - Transfer STX from caller to contract and credit user vault.
- withdraw(amount uint)
  - Debit user vault and transfer STX back to caller.
- stake(amount uint)
  - Move balance into a stake record, set active flag, increment `total-staked`.
- claim-reward()
  - If an active stake exists, calculate a 10% reward (reward = amount / 10), mark stake inactive, transfer reward to user.
- create-trade(price uint)
  - Create an order with a sequential id.
- accept-trade(id uint)
  - Transfer price from buyer (tx-sender) to seller, mark order filled.
- cancel-trade(id uint)
  - Seller cancels their active order.

## Usage / Developer workflow

Requirements:
- Node.js (LTS)
- Clarinet (for local testing)
- Optional: @stacks/transactions and @stacks/network for integration

Install Clarinet (local or global):
PowerShell / CMD:
```powershell
npm install -g clarinet
# or dev dependency in repo
npm install --save-dev clarinet
```

Run tests:
```powershell
cd c:\Users\USER\Desktop\STACKS\OCTOMBER\stackvaulttrade
clarinet test
```

Open Clarinet console (local devnet):
```powershell
clarinet console
```
From the console you can interact with deployed contracts or run calls via your test accounts.

Example high-level calls (conceptual):
- Deposit 100 STX:
  - call `deposit(100)` using your tooling (Clarinet console or stacks.js)
- Stake 50 STX:
  - call `stake(50)`
- Claim reward:
  - call `claim-reward()`
- Create trade at price 25:
  - call `create-trade(25)`
- Accept trade id 1:
  - call `accept-trade(1)`

For production/testnet deployment and dApp integration, use @stacks/transactions to build and broadcast contract calls (provide contract address and proper fee/gas).

## Example: call pattern with @stacks/transactions (outline)
Use Stacks JS to construct and submit contract calls. Provide appropriate sender key management and network configuration.

(Pseudocode)
```js
// build a contract-call transaction to call `deposit`
// sign and broadcast using @stacks/transactions and @stacks/network
```

## Testing

- Add Clarinet unit tests under `tests/` to cover:
  - Deposit / withdraw happy-path and insufficient funds
  - Stake / double-stake rejection
  - Claim-reward success and double-claim rejection
  - Trade create / accept / cancel and error cases



Add an appropriate license file (e.g., MIT) and update repository metadata.
