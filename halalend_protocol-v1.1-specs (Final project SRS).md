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
Lending Protocol Validator is a validator that holds the liquidity of the protocol. It returns a UTxO containing the liquidty that can be spent from
##### 3.3.1.1 Parameter
None
##### 3.3.1.2 Datum
- LendingProtocolDatum:
    - spender: the entity who can spend from the liquidity
##### 3.3.1.3 Redeemer
None
##### 3.3.1.4 Validation
- validate that enough liquidity is available to be spent
- validate that `spender` can spend from the liquidity
- validate that a transaction adheres to the protocol rules

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
        - beneficiary: `VerificationKeyHash`
        - loan_amount: `Int`
- CheckBurn: the redeemer can be called once to burn the loan NFT
    - validate that the `out_ref` must presented in the transaction inputs (which is the NFT)
    - validate that the redeemer only burn:
        - a single loan NFT
    - validate that there's only one loan UTxO in the transaction input. The loan UTxO must contain in it's value, 1 and it's datum:
        - beneficiary: `VerificationKeyHash`
        - loan_amount: `Int`
##### 3.3.3.3 Spend Purpose
###### 3.3.3.3.1 Datum
- beneficiary: `VerificationKeyHash`
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

#### 3.4.1 Add Liquidity
Adds liquidity to the protocol.<br /><br />Transaction requires spending an UTxO provided by a user intending to add liquidity
Transaction structure:
- Inputs:
    - Liquidity UTxO from the user
    - Liquidity transaction input and reference script from the `Lending Protocol Validator`
- Outputs:
    - Updated Liquidity UTxO:
        - Address: Liquidity Address
        - Datum:
            - spender: the entity who can spend from liquidity
        - Value:
            - Stable coin balance left
    - LP (Liquidity Pool) Tokens:
        - Address: User's wallet address

#### 3.4.2 Borrow
User borrows from the protocol.<br /><br />Transaction requires spending an UTxO provided by a user intending to borrow, input of liquidity, nft script, reference to the protocol parameters, and reference to dex exchange rates
Transaction structure:
- Inputs:
    - Borrow UTxO from user
    - Liquidity transaction input and reference script from the `Lending Protocol Validator`
    - Reference script from the `NFT Minting Validator`
    - Reference transaction input from the `Protocol Parameter Validator`
    - Reference transaction input from the dex for rates
- Mint:
    - Script NFT Minting Policy:
        - Redeemer: CheckMint
        - Value:
            - 1 Loan NFT Asset
- Outputs:
    - Loan NFT UTxO:
        - Address: User wallet address
        - Datum:
            - beneficiary: `VerificationKeyHash`
            - loan_amount: `Int`
        - Value:
            - Loan Amount
            - 1 Loan NFT Asset
    - Collateral UTxO:
        - Address: Collateral Validator address
        - Datum:
            - owner: the lending protocol's verification key hash
            - beneficiary: the beneficiary's / locker's verification key hash
            - assets_locked: list of asset name and value locked
        - Value:
            - Assets locked
    - Updated Liquidity UTxO:
        - Address: Liquidity Address
        - Datum:
            - spender: the entity who can spend from liquidity
        - Value:
            - Stable coin balance left
    - Fees UTxO

#### 3.4.3 Liquidation
Liquidates the users collateral if it reaches the `collateral threshold`<br /><br />Transaction requires input of collateral validator, input of liquidity, reference to the protocol parameters, and reference to dex exchange rates
Transaction structure:
- Inputs:
    - Liquidity transaction input and reference script from the `Lending Protocol Validator`
    - Collateral transaction input and reference script from the `Collateral Validator`
    - Reference transaction input from the `Protocol Parameter Validator`
    - Reference transaction input from the dex for rates
- Outputs:
    - Remaining collateral:
        - Address: User wallet address
        - Value:
            - remaining collateral
    - Fees and collateral equivalent to loan value created

#### 3.4.4 Repayment
User repays borrowed funds
Transaction structure:
- Inputs:
    - Repayment UTxO from User
- Mint:
    - Script NFT Minting Policy:
        - Redeemer: CheckMint
        - Value:
            - 1 Loan NFT Asset
- Outputs:
    - Collateral amount UTxO:
        - Address: User's wallet address
        - Datum:
            - owner: the lending protocol's verification key hash
            - beneficiary: the beneficiary's / locker's verification key hash
            - assets_locked: list of asset name and value locked
        - Value:
            - minus 1 Loan NFT Asset
            - Loan Amount
    - Updated Liquidity UTxO:
        - Address: Liquidity Address
        - Datum:
            - spender: the entity who can spend from liquidity
        - Value:
            - Stable coin balance left
    - Fees UTxO
