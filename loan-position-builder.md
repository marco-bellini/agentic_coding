# Loan Position Builder and Drawdown Calculator

## System Context

Our collateral lending system operates with a C# backend that exposes APIs and uses Kafka for event streaming. The frontend is built with Blazor. The new module should be designed with this architecture in mind to facilitate smooth integration.

## Core Requirements

### 1. Position Builder Component

Create a component that calculates the maximum loan amount a client can borrow based on their collateral, configured parameters, and current market rates.

#### Specific Functionalities

- Calculate maximum borrowable amount for a given set of collateral assets and loan currencies
- Display detailed breakdown per collateral asset showing:
  - Loan-to-Value (LTV) at Initial Margin (IM)
  - Lending value contribution of each asset
  - Selected Fiat currency equivalent (e.g., USD, CHF, EUR) amount for each contribution
- Support for multiple collateral assets simultaneously
- Recalculate values when any input parameters change

### 2. Drawdown Calculator Component

Create a component that helps clients understand the risk thresholds of their loan positions.

#### Specific Functionalities

Calculate and display:
- Collateralization levels triggering:
  - Initial Margin (IM) restoration
  - Soft Margin (SM) call
  - Hard Margin (HM) liquidation
- Percentage decrease in collateral value before triggering IM, SM, and HM thresholds
- Absolute amount (in USD, EUR, or CHF) the collateral can decrease before triggering each threshold
- Collateralization per asset per level (IM, SM, HM)
- Portfolio totals:
  - Total portfolio value
  - Collateral value at IM
  - Collateral value at SM
  - Collateral value at HM

## Input Parameters

### User-Provided Inputs

1. Collateral assets and their quantities (e.g., BTC, 10; SOL, 1000)
2. Loan currency (USD, EUR, CHF, etc.)
3. Desired loan duration/tenor (3M, 6M, 1Y, etc.)
4. Loan amount (specifically for the drawdown calculator)

### Configuration Parameters

1. LTV Values Table:
   - Asset (currency)
   - Initial Margin percentage per asset
   - Soft Margin percentage per asset
   - Hard Margin percentage per asset

2. CTV (Collateral-to-Value) Values Table:
   - Asset (currency)
   - Initial Margin percentage per asset
   - Soft Margin percentage per asset
   - Hard Margin percentage per asset

3. Interest Rate Curves:
   - Asset (currency)
   - Interest rate per tenor (3M, 6M, 1Y, etc.)

### External Data

- Market rates for all relevant assets (assume these are available in the server's memory cache). Generally, in USD (BTC/USD, etc.)

## Technical Requirements

### Code Structure

1. Create modular C# classes with clear separation of concerns:
   - Data models for collateral, margins, and calculation results
   - Service layer for calculations and business logic
   - API controllers for integration with the existing system
   - Kafka event producers/consumers if needed

2. Implement the following key classes:
   - LoanPositionBuilder - Core calculation engine for maximum loan amounts
   - DrawdownCalculator - Core calculation engine for margin thresholds
   - CollateralAsset - Model for collateral assets with their properties
   - MarginLevel - Model for different margin thresholds (IM, SM, HM)
   - LoanCalculationResult - Model to hold and transport calculation results

### Performance Considerations

1. Efficient calculations to ensure responsive UI
2. Proper caching of intermediary results to avoid redundant calculations
3. Asynchronous processing for potentially long-running calculations

### Integration Points

1. RESTful API endpoints for both calculator components
2. Kafka event definitions for relevant system events
3. Blazor UI components that consume the APIs

## Deliverables

1. Complete C# implementation of the Position Builder and Drawdown Calculator components
2. Unit tests covering the core calculation logic and edge cases
3. Integration tests demonstrating interaction with the existing system
4. API documentation for the new endpoints
5. Simple UI mockups or components for Blazor integration
6. Documentation explaining the calculation methodologies

## Example Scenario

### Scenario Details

A client has:
- 5 BTC (current price: $60,000)
- 2,000 SOL (current price: $100)

Configuration:
- BTC: IM at 70% LTV, SM at 75% LTV, HM at 80% LTV
- SOL: IM at 50% LTV, SM at 55% LTV, HM at 60% LTV
- Loan currency: USD
- Interest rate for 6M: 8% APR

### Position Builder Calculations

- Maximum loan amount (in USD)
- Contribution of BTC: $210,000 (5 × $60,000 × 70%)
- Contribution of SOL: $100,000 (2,000 × $100 × 50%)
- Total available: $310,000

### Drawdown Calculator Results

If the client takes a loan of $250,000:
- Current collateralization: 124% ($310,000 / $250,000)
- Portfolio can decrease by 19.4% before triggering IM restoration
- Portfolio can decrease by 24.2% before triggering SM call
- Portfolio can decrease by 29.0% before triggering HM liquidation

## Implementation Considerations

1. Consider implementing real-time recalculation as market prices change
2. Design the system to handle multiple currencies with appropriate conversion
3. Implement proper validation for user inputs
4. Consider adding visualization components to help users understand margin thresholds
5. Implement proper error handling and edge cases (e.g., insufficient collateral)

## Assessment Criteria

The implementation will be evaluated based on:

1. Correctness of calculations
2. Code quality and organization
3. Performance and efficiency
4. Testability and test coverage
5. Integration capabilities with the existing system
6. User experience considerations
7. Documentation quality
