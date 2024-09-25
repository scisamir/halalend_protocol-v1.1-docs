# Halalend Protocol V1.1 Specification


## 1. Overview

Halalend lending protocol mimics all the features of the currently existing lending protocols on
Cardano except the interest paid by borrowers on loan repayment. Loans are repaid at zero
interest rate. Initially, the protocol will be the sole lender. Loans will be issued out in stablecoins
from the protocol's treasury. This treasury will be seeded/bootstrapped by the community
through a token sale. This means that the protocol will maintain a finite amount of liquidity and
limited loan threshold. Ultimately, the protocol is aiming to be the de facto hub for quick, short
term loans.


## 2. Architecture

- ### Add Liquidty:

There is 1 contract in the add liquidity feature:

- Lending Protocol Validator: Returns the liquidity transaction input and reference script

- ### Borrow:

There are 4 contracts in the borrow feature:

- Lending protocol Validator: Returns the liquidity transaction input and reference script
- Collateral validator: Locks a collateral utxo that represent the collateral of the borrower
- NFT Minting Validator: Returns a reference script used to create an NFT that represents a loan position
- Protocol Parameter Validator: Returns reference transaction input with the protocol parameters

- ### Liquidation:

There are 3 contracts in the add liquidity feature:

- Lending protocol Validator: Returns the liquidity transaction input and reference script
- Collateral validator: Returns the collateral transaction input and reference script
- Protocol Parameter Validator: Returns reference transaction input with the protocol parameters

- ### Repayment:

There are 3 contracts in the add liquidity feature:

- Lending protocol Validator: Returns the liquidity transaction input and reference script
- Collateral validator: Returns the collateral transaction input and reference script
- Protocol Parameter Validator: Returns reference transaction input with the protocol parameters


## 3. Specification

### 3.1 Actors

- User: An entity who wants to interact with the liquidity pool to add liquidity or borrow/repay a loan

### 3.2 Tokens

- LP Token: Represents liquidity provider's share of of pool
    - CurrencySymbol: Lending Protocol Validator
    - TokeName: Defined in LendingProtocolValidator
- Loan NFT: Represents the amount of loan a user has taken
    - CurrencySymbol: NFT Minting Validator
    - TokeName: Defined in NFTMintingValidator

### 3.3 Smart Contract

#### 3.3.1 Lending Protocol Validator
-------------------
##### 3.3.1.1 Parameter
--------
##### 3.3.1.2 Datum
--------
##### 3.3.1.3 Redeemer
--------
##### 3.3.1.4 Validation
--------

#### 3.3.2 Collateral Validator
Collateral Validator is a lock script that locks a utxo containing the collateral that a user provides.
The locked collateral is provided as a transaction input and reference script during liquidation or when a user
wants to repay their borrowed loan
##### 3.3.2.1 Parameter
None
##### 3.3.2.2 Datum
- CollateralDatum:
    - owner: the lending protocol's verification key hash
    - beneficiary: the beneficiary's / locker's verification key hash
    - assets_locked: list of asset name and value locked
##### 3.3.2.3 Redeemer
- LiquidateCollateral
- RepayLoan
##### 3.3.2.4 Validation
- LiquidateCollateral: the redeemer will allow spending the UTxO and return change back to the user
    - validate that the UTxO can be spent if the liquidation threshold is reached
- RepayLoan: the redeemer will allow spending the UTxO by the user
    - validate that the UTxO can be spent if the user returns the exact amount of loan borrowed

#### 3.3.3 NFT Minting Validator
NFT Minting Validator is responsible for creating the loan NFT
##### 3.3.3.1 Parameter
- out_ref: is a reference of a UTxO which will only be spent on `NFTMintingRedeemer` redeemer to make sure this redeemer can only be called once
##### 3.3.3.2 Minting Purpose
###### 3.3.3.2.1 Redeemer
- CheckMint
- CheckBurn
###### 3.3.3.2.2 Validation
- CheckMint: the redeemer can be called once to mint the loan NFT
    - validate that the `out_ref` must be presented in the transaction inputs
    - validate that the redeemer only mint:
        - a single loan NFT
    - validate that there's only one loan UTxO in the transaction outputs. The loan UTxO must contain in it's value, 1 and it's datum:
        - beneficiary: `VerificationKeyHash`,
        - loan_amount: `Int`
- CheckBurn: the redeemer can be called once to burn the loan NFT
    - validate that the redeemer only burn:
        - a single loan NFT
    - validate that there's only one loan UTxO in the transaction input. The loan UTxO must contain in it's value, 1 and it's datum:
        - beneficiary: `VerificationKeyHash`,
        - loan_amount: `Int`
##### 3.3.3.3 Spend Purpose
###### 3.3.3.3.1 Datum
- beneficiary: `VerificationKeyHash`,
- loan_amount: `Int`
###### 3.3.3.3.2 Redeemer
- Nothing
###### 3.3.3.3.3 Validation
- validate that the transaction has to be executed by the beneficiary
- validate transaction won't mint anything

#### 3.3.4 Protocol Parameter Validator
Protocol Parameter Validator is responsible for providing the parameters of `Halalend protocol` like the MCR, and CR
##### 3.3.4.1 Parameter
None
##### 3.3.4.2 Datum
- ProtocolParameterDatum:
    - cr: the collateralization ratio
    - lq: liquidation threshold
    - mla: minimum loan amounts
    - lt: loan terms
    - lp: liquidation penalty
    - rt: reserve threshold
    - rr: reserve ratios
    - gtp: governance token parameters
    - ltv: maximum loan to value ratios
##### 3.3.4.3 Redeemer
None
##### 3.3.4.4 Validation
- validate that the collateralization ratio satisfies the requirement for the user to borrow
- validate that the user's collateral is not at or lower than the liquidation threshold
- validate that the loan amount a user wants to borrow is up to the minmum loan amount
- validate that the loan conforms to the loan terms
- validate that the user loan amount and collateral doesn't exceed the maximum loan to value ratio

### 3.4 Transaction

#### 3.4.1 `Transaction 1 Name`
--------------------------
Transaction structure:
- Inputs:
    - `The input`
- Mint:
    - `Minting Policy Name`:
        - Redeemer: ------
        - Value:
            - `values`
            - `values`
- Outputs:
    - `The utxos...`:
        - Address: ----
        - Datum: ------
        - Value:
            - `values`
            - `values`

#### 3.4.2 `Transaction 2 Name`
--------------------------
Transaction structure:
- Inputs:
    - `The input`
- Mint:
    - `Minting Policy Name`:
        - Redeemer: ------
        - Value:
            - `values`
            - `values`
- Outputs:
    - `The utxos...`:
        - Address: ----
        - Datum: ------
        - Value:
            - `values`
            - `values`