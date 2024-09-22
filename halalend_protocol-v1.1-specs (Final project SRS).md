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
-------------------
##### 3.3.2.1 Parameter
--------
##### 3.3.2.2 Datum
--------
##### 3.3.2.3 Redeemer
--------
##### 3.3.2.4 Validation
--------

#### 3.3.3 NFT Minting Validator
-------------------
##### 3.3.3.1 Parameter
--------------
##### 3.3.3.2 Minting Purpose
--------------
###### 3.3.3.2.1 Redeemer
--------
###### 3.3.3.2.2 Validation
--------
##### 3.3.3.3 Spend Purpose
--------------
###### 3.3.3.3.1 Datum
--------
###### 3.3.3.3.2 Redeemer
--------
###### 3.3.3.3.3 Validation
--------

#### 3.3.4 Protocol Parameter Validator
-------------------
##### 3.3.4.1 Parameter
--------
##### 3.3.4.2 Datum
--------
##### 3.3.4.3 Redeemer
--------
##### 3.3.4.4 Validation
--------

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