# BMC1i — Fixed-Fairness Investment & Buying Tool

## Project Overview

BMC1i is a company-internal investment and purchasing mechanism designed for Polygon Chain 137.

Its purpose is to take an investment amount in USDC, mathematically divide that amount into six equal portions, and use each portion to independently purchase one of six approved underlying Polygon tokens.

BMC1i is NOT a price-weighted index.

The market price of an underlying token does not determine how much of the investment that token receives. The market price is used only to determine how many units of that token can be purchased with its fixed allocation.

## Core Mathematical Rule

BMC1i supports exactly six approved underlying tokens.

Every investment is divided equally:

Investment Amount / 6 = Allocation Per Token

Therefore:

Token 1 = 1/6
Token 2 = 1/6
Token 3 = 1/6
Token 4 = 1/6
Token 5 = 1/6
Token 6 = 1/6

Each token receives approximately 16.666666...% of the investment.

The allocation is based on the investment amount and is independent of the market price of the six tokens.

## Example

For a $100 USDC investment:

$100 / 6 = $16.666666...

Each of the six tokens receives approximately $16.666666... of the investment allocation.

The six tokens may have completely different market prices.

For example:

Token A = $0.10
Token B = $2.00
Token C = $25.00
Token D = $100.00
Token E = $0.005
Token F = $500.00

Their prices do not change the allocation.

Each still receives the same investment portion.

The price only determines the quantity purchased.

Quantity Purchased = Allocation / Execution Price

Therefore:

Money determines allocation.
Price determines quantity.

## Rounding

Blockchain assets use indivisible smallest units, so some investment amounts cannot be divided perfectly into six integer portions.

BMC1i will use deterministic integer mathematics.

The first five allocations receive:

portion = investmentAmount / 6

The sixth allocation receives the remainder:

sixthAllocation = investmentAmount - (portion * 5)

This guarantees that:

allocation1 + allocation2 + allocation3 + allocation4 + allocation5 + allocation6 = investmentAmount

No investment amount should be silently lost because of rounding.

This behavior must be thoroughly tested before production deployment.

## Initial Input Asset

V1 will use USDC on Polygon Chain 137 as the investment/input asset.

Additional input assets may be considered in a future version, but V1 should remain USDC-focused to keep accounting and security simple.

## Six Underlying Assets

BMC1i will support exactly six approved Polygon tokens.

The six token contract addresses will be explicitly defined by the contract.

Users must not be able to select arbitrary tokens.

The final six token addresses will be recorded and verified before deployment.

The initial design should avoid giving an administrator unrestricted ability to replace the six underlying assets after deployment.

## Purchase Process

The intended investment process is:

User/Company
    |
    | USDC
    v
BMC1i
    |
    | Divide investment by 6
    v
Six equal allocations
    |
    +----> Token 1 purchase
    +----> Token 2 purchase
    +----> Token 3 purchase
    +----> Token 4 purchase
    +----> Token 5 purchase
    +----> Token 6 purchase
              |
              v
        Secure Vault

Each allocation is treated as an independent purchase.

The system must not calculate one token's allocation based on another token's market price.

## Market Price

Market price is used to determine the quantity of an underlying token purchased.

Example:

Allocation = $16.66
Execution price = $2.00

Quantity purchased is approximately 8.33 tokens.

If another token costs $100, the same $16.66 allocation purchases approximately 0.1666 tokens.

The investment allocation remains approximately $16.66 for both.

Price affects quantity, not allocation.

## DEX Purchases

The six allocations will ultimately be executed as six independent swaps using an approved Polygon DEX/router mechanism.

Every swap must include protection against unacceptable execution.

Each swap should have a minimum acceptable output amount:

minimumAmountOut

If the swap cannot provide at least that amount, the transaction should revert.

This is intended to protect the investment from excessive slippage and unfavorable execution.

## Vault

The purchased underlying tokens will be maintained by the BMC1i vault/system.

The vault is intended to provide secure custody and transparent on-chain accounting.

There will be no:

- Staking requirement
- Yield lock
- Maturity period
- Artificial holding period

The underlying assets remain available for the supported redemption, swap, or trading mechanisms.

## Redemption and Conversion

The design is intended to allow the underlying assets to eventually be redeemed, swapped, or traded.

Supported destination assets should initially be limited to approved Polygon Chain 137 assets and controlled swap mechanisms.

The exact redemption mechanism will be finalized before implementation.

BMC1i must not create a false promise that an investment position has a guaranteed market value.

Actual redemption value depends on the underlying assets, execution prices, fees, and market conditions.

## Security Principles

Security is a primary requirement.

The implementation should use established and well-tested security patterns and audited libraries where practical.

The intended protections include:

- Reentrancy protection
- Safe ERC-20 transfers
- Restricted administrative functions
- Emergency pause capability
- Fixed approved asset list
- Zero-address validation
- Slippage protection
- Deterministic integer accounting
- Explicit token decimal handling
- Event logging
- Controlled external contract interaction
- Failure/revert protection
- Clear separation between allocation, execution, and valuation

## Administrative Security

Administrative privileges must be minimized.

An administrator must not have unrestricted ability to:

- Arbitrarily mint investment positions
- Arbitrarily modify balances
- Manipulate the fixed six-way allocation
- Set arbitrary token prices
- Remove normal user/investment assets
- Replace approved assets without controlled procedures

For production deployment, administrative authority should preferably use a secure multisignature arrangement rather than a single private key.

## Price Manipulation Protection

BMC1i must not allow an administrator to simply declare an arbitrary token price and use that value to manufacture an investment valuation.

Price information used for reporting or execution must come from an appropriate external source or actual DEX execution data and must include appropriate validation.

Most importantly, price information must never change the fixed 1/6 allocation rule.

## Accounting Principles

For every investment:

Total Allocations = Original Investment

The fundamental accounting invariant is:

Allocation 1
+ Allocation 2
+ Allocation 3
+ Allocation 4
+ Allocation 5
+ Allocation 6
= Investment Amount

The system must clearly distinguish between three different concepts:

1. Allocation — how much USDC is assigned to each purchase.
2. Execution — how many underlying tokens the DEX actually provides.
3. Valuation — what those tokens may currently be worth according to an accepted price source.

These concepts must not be incorrectly combined.

## Fees

The system must distinguish between:

1. Investment allocation
2. DEX trading costs
3. Polygon network gas

The six-way mathematical allocation must remain deterministic.

The final implementation must explicitly define whether trading costs are deducted from each allocation or handled separately.

Polygon gas should be treated separately from the investment allocation whenever practical.

## Company/Internal Use

BMC1i is initially intended as a controlled company/internal system.

The implementation should prioritize:

- Predictable behavior
- Controlled access
- Transparent accounting
- Verifiable transactions
- Limited administrative powers
- Security
- Simplicity
- Auditability

The contract should not contain unnecessary features simply because they are technically possible.

## V1 Scope

BMC1i V1 should intentionally remain simple.

V1 should NOT initially include:

- Staking
- Yield farming
- Lending
- Leverage
- Derivatives
- Arbitrary asset creation
- Unrestricted token listings
- Unnecessary governance
- Unnecessary upgrade systems
- Unnecessary financial complexity

Every additional feature increases complexity and attack surface.

The first version should establish the fixed mathematical purchasing mechanism correctly and securely.

## Development Environment

Primary development environment:

Remix IDE

Target blockchain:

Polygon

Chain ID:

137

Development should begin in a controlled test environment before production deployment.

No real funds should be used during initial development and testing.

## Project Structure

The initial project is intentionally small:

BMC1i-Index/
|
|-- README.md
|-- .gitignore
|
|-- contracts/
|   |-- BMC1i.sol
|
|-- docs/
|   |-- ECONOMICS.md
|   |-- SECURITY.md
|
|-- test/
    |-- BMC1i.t.sol
    |-- BMC1iSecurity.t.sol
    |-- BMC1iInvariants.t.sol

Additional files should only be added when they are required by the finalized implementation.

## Security Testing

Before production deployment, the system should be tested for:

- Six-way allocation accuracy
- Deterministic rounding
- Zero investment
- Very small investments
- Large investments
- Failed USDC transfers
- Failed swaps
- Insufficient swap output
- Excessive slippage
- Unauthorized access
- Reentrancy
- Paused-state behavior
- Invalid addresses
- Incorrect token decimals
- Duplicate underlying assets
- Unexpected token behavior
- Accounting discrepancies
- Vault balance discrepancies
- Emergency procedures
- External contract failures

The six-way allocation invariant should also be tested with many different investment amounts.

## Development Sequence

The project should follow this sequence:

1. Finalize the six underlying token addresses.
2. Finalize the USDC contract address.
3. Finalize the DEX/router mechanism.
4. Finalize the exact rounding rule.
5. Finalize fee treatment.
6. Finalize redemption behavior.
7. Write the mathematical specification.
8. Write the Solidity contract.
9. Compile in Remix.
10. Perform unit and security testing.
11. Test on a controlled test network/environment.
12. Review the complete contract and attack surface.
13. Verify deployment configuration.
14. Deploy to production only after testing and review.

A successful compilation does not mean the contract is safe for production.

## Core Design Principle

The central principle of BMC1i is:

Equal investment portions are determined by fixed mathematics, not by the market price of the assets being purchased.

In simple terms:

Money determines allocation.

Price determines quantity.

The six underlying assets therefore receive equal investment portions regardless of whether one token costs fractions of a cent and another costs hundreds of dollars.

## Current Project Status

Project: BMC1i

Purpose: Fixed-fairness investment and purchasing mechanism

Company: BMC1 BeMyCrypto1 LLC

Network: Polygon Chain 137

Input: USDC

Underlying assets: Exactly six approved Polygon tokens

Allocation: 1/6 per token

Purchasing: Six independent purchases

Custody: Secure vault

Locking: None

Primary development environment: Remix

Production status: Not deployed

## Development Rule

The core economics must not be changed casually during implementation.

If a proposed Solidity feature changes the mathematical allocation, custody model, asset selection, or security assumptions, the specification must be reviewed before that feature is added.

BMC1i should remain:

Simple
Predictable
Secure
Verifiable
Auditable

The objective is to build the mathematical purchasing tool first, then add only the functionality necessary to operate it safely.
