## **Synthetix Protocol**

[Synthetix](https://www.gemini.com/cryptopedia/glossary#synthetix-snx) is an [Ethereum-based](https://www.gemini.com/cryptopedia/ethereum-smart-contracts-tokens-use-cases) protocol for issuing synthetic assets. Analogous to [derivatives](https://www.gemini.com/cryptopedia/glossary#derivative) in legacy finance, [synthetic assets](https://www.gemini.com/cryptopedia/glossary#synthetic-asset) are financial instruments in the form of [ERC-20](https://www.gemini.com/cryptopedia/erc20-token-standard-ethereum) [smart contracts](https://www.gemini.com/cryptopedia/smart-contract-examples-smart-contract-use-cases) known as “Synths,” which track and provide the returns of another asset without requiring you to hold that asset. You can trade Synths — which range from cryptocurrencies, indexes, inverses, and real-world assets like gold — on Kwenta, Synthetix’s [decentralized exchange (DEX)](https://www.gemini.com/cryptopedia/decentralized-exchange-dex-crypto). Synthetix’s native token, the Synthetix Network Token (SNX), is used to provide collateral against Synths that are issued.

To understand Synthetix, it is necessary to understand the utility of Synths and their role in the [decentralized finance (DeFi)](https://www.gemini.com/cryptopedia/open-vs-decentralized-finance-defi) ecosystem.

**Imports**

1. Import “./owned.sol”;

   In this contract, we have two state variables address owner and address nominatedOwner. In the contract’s constructor, we assign the owner of the contract moving forward in case we want to change the owner of the contract we assign a new address to the address nominatedOwner using the function nominateNewOwner and function acceptOwnership.

   In summary, this contract assigns ownership and also changes ownership if desired.

2. Import “./Pausable.sol”;

   The Pausable contract has two state variables known as uint public lastPauseTime and bool public paused. Also, the Pausable contract inherits the Owned contract to determine onlyOwner can pause the contract. The Pausable contract has a constructor where the owner address is being assigned and a function to set the pause state to either true or false.

3. Import “MixinResolver.sol”;

   The contract has one import import "./AddressResolver.sol" which is used to look up the contract addresses in this contract and used to cache contract addresses.

   It has a mapping of bytes32 to address mapping(bytes32 => address) private addressCache which is used to store the cached contract addresses. Also, there is a constructor which receives the address of the AddressResolver contract as input.

   The next line after the constructor is an internal function combineArrays which receives two arrays of type bytes as inputs and combines these two arrays to become one array.

   Next, function resolverAdressesRequired returns an array of bytes32 that specifies the contract addresses required by the contract.

   Next, there is a public function rebuildCache which rebuilds the cache of contract addresses by iterating over the list of required contract addresses that are gotten from function resolverAddressesRequired, and for each required address, it calls the function requireAndGetAddress from the imported AddressResolver contract to get the target address. The target address is then stored in the addressCache mapping.

   Next is an external function isResolverCached, it is used to check the validity of the cached addresses in the addressCache by comparing it to the addresses retrieved from the AddressResolver contract.

   Lastly, the function requireAndGetAddress. It takes in one parameter bytes32 name which is used as a key to get a corresponding address from the mapping of addressCache. The function is used internally by the contract MixinResolver to retrieve addresses from the cache instead of calling requireAndGetAddress directly every time from the AddressResolver contract.

4. Import “./interfaces/ICollateralManager.sol”;

   This is the interface for the collateralManager contract, which defines the functions that can be called by other contracts.

5. Import “./AddressSetLib.sol”;

   This import is a library called AddressSetLib, which provides functionality to manage a set of Ethereum addresses. It has a struct called AddressSet which stores the set of addresses as an array called elements and a mapping called indices which maps each address to its index in the array. The library has for functions:

- contains: checks if a given address exits in the set or not. It returns true if it does contain and false if it does not.
- getPage: it retrieves a page of addresses from the set, given an index and a page size as parameters.
- add: it is used to add an address to the set.
- remove: it is used to remove an address from the set.

6. Import “./Bytes32SetLib.sol”;

   This import is a library called Bytes32SetLib, which provides several functionalities to work with the set of bytes32 elements. It has a struct called Bytes32Set which stores the set of bytes32 elements as an array called elements and a mapping called indices which maps each bytes32 element to its index in the array. The library has for functions:

- contains: this function takes in Bytes32Set and a bytes32 element as parameters and returns true if the element is in the set and false if it is not.
- getPage: this function takes a Bytes32Set, an index, and a pageSize. It returns a bytes32 array containing the elements from the set starting at the given index and up to pageSize elements.
- add: the function takes Bytes32Set and a bytes32 element and adds the element to the set if it is not already there.
- remove: the function takes Bytes32Set and a bytes32 element, and removes the element from the set if it is in the set.

7. Import “./SafeDecimalMath.sol”;

   This is a library that provides functions for safely performing mathematical operations with fixed-point decimals. The library is designed to work with decimals represented as uint values, where the decimal point is fixed at a certain position. The library made use of OpenZeppelin SafeMath library for performing safe arithmetic operations with unsigned integers, ensuring that no integer overflow or underflow occurs.

   The library is designed to be used as a utility library by other smart contracts that need to perform arithmetic operations with fixed-point decimals.

8. Import “./CollateralManagerState.sol”;

   This contract CollateralManagerState is a smart contract used to manage the state of the collateral system in a synthetic asset exchange. It inherits two other contracts Owned and State. It includes some state variables and mapping;

- The struct Balance is defined to represent the total amount of a synth that has been issued in the system. It has two fields: long and short, both of type uint.
- The uint public totalLoans represent the total number of loans in the system.
- The uint[] public borrowRates is a dynamic array that stores the current borrowing interest rates for all synths. and uint public borrowRatesLastUpdated stores the timestamp of the last time the borrowing rates were updated.
- The mapping(bytes32 => uint[]) public shortRates is a mapping of dynamic arrays, where each key corresponds to a synth and its associated shorting interest rates and mapping(bytes32 => uint) public shortRatesLastUpdated stores the timestamp of the last time the shorting interest rates for each synth were updated.
- The mapping(bytes32 => Balance) public totalIssuedSynths represents the total amount of long and short for each synth in the system.

The contract has a constructor that sets the first element of borrowRates to 0 and sets borrowRatesLastUpdated to the current block timestamp.

The contract also has several external functions that can only be called by the associated contract which is specified during contract instantiation;

- Function incrementTotalLoans increments totalLoans by 1 and returns the new value(the number of loans present in the system).
- Function long takes a synth (specified as bytes32) as input and returns the long value of the corresponding Balance struct in the totalIssuedSynths.
- Function short takes a synth (specified as bytes32) as input and returns the short value of the corresponding Balance struct in totalIssuedSynths.
- Function incrementLongs takes a synth (specified as bytes32) and an amount as inputs, increment the long value of the corresponding Balance struct in totalIssuedSynths by the given amount.
- Function decrementLongs takes a synth (specified as bytes32) and an amount as inputs, decrements the long value of the corresponding Balance struct in totalIssuedSynths by given amount.
- Function incrementShorts takes a synth and an amount as inputs, increments the short value of the corresponding Balance struct in totalIssuedSynths by the given amount.
- Function decrementShorts takes a synth and an amount as inputs, decrements the short value of the corresponding Balance struct in totalIssuedsynths by the given amount.
- Function updateBorrowRates takes a rate as input adds it to the end of borrowRates and updates borrowRatesLastUpdated to the current block timestamp.
- Function ratesLastUpdated returns the value of borrowRatesLastUpdated.
- Function getRateAt takes an index as input and returns the borrow rate at that index in borrowRates.
- getRatesLength reuters the length of borrowRates.
- Function getRatesAndTime takes an index as input and returns four values: the borrow rate at that index in borrowRates(entryRate), the last borrow rate in borrowRates(lastRate), the timestamp when the last borrow rate was added t borrowRates(lastUpdated), and the new length of borrowRates after adding the new rate to the end.
- Function addShortCurrency adds a new currency to the list of currencies that have short rates.
- Function removeShortCurrency removes a currency from the list of currencies that have short rates.
- Function getShortRateAt returns the short rate at the specified index for the specified currency.
- Function getShortRatesLength returns the number of short rates for the specified currency.
- Function updateShortRates adds a new short rate for the specified currency.
- Function shortRateLastUpdated returns the timestamp of when the short rates for the specified currency were last updated.
- Function getShortRatesAndTime returns the short rate at the specified index for the specified currency ("entryRate"), the last short rate for the specified currency ("lastRate"), the timestamp when the last short rate was added for the specified currency ("lastUpdated"), and the new length of short rates for the specified currency after adding the new rate to the end.

Import “./interfaces/IIssuer”;

This is the interface for the Synthetix Issuer contract. The Issuer is responsible for issuing and burning Synths (synthetic assets that track the price of real-world assets), as well as managing the collateralization of these synths.

The interface defines both view and restricted functions. The view functions allow users to query information about the state of the system, such as the amount of debt outstanding, the total collateralization ratio, and the available synths. The restricted functions are used internally to the Synthetix system and are used to issue and burn synths, as well as manage the system's debt and collateralization.

Some notable functions in the interface include:

- issueSynths: Used to issue synths to a particular address, either from the issuer's own address or on behalf of another address.
- burnSynths: Used to burn synths from a particular address.
- availableSynths: Returns the list of available Synths.
- collateralisationRatio: Returns the collateralization ratio for a given issuer.
- debtBalanceOf: Returns the debt balance of a given issuer for a particular synth.
- totalIssuedSynths: Returns the total amount of synths issued for a given synth, optionally excluding synths that are collateralized by assets other than the SNX token.
- liquidateAccount: Liquidates an account, transferring their collateral to pay off their outstanding debt.
- addSynths: Adds new synths to the system.
- setCurrentPeriodId: Sets the current period ID for the system.

This is the interface for the Synthetix Issuer contract. The Issuer is responsible for issuing and burning Synths (synthetic assets that track the price of real-world assets), as well as managing the collateralization of these synths.

The interface defines both view and restricted functions. The view functions allow users to query information about the state of the system, such as the amount of debt outstanding, the total collateralization ratio, and the available synths. The restricted functions are used internally to the Synthetix system and are used to issue and burn synths, as well as manage the system's debt and collateralization.

Some notable functions in the interface include:

- issueSynths: Used to issue synths to a particular address, either from the issuer's own address or on behalf of another address.
- burnSynths: Used to burn synths from a particular address.
- availableSynths: Returns the list of available Synths.
- collateralisationRatio: Returns the collateralization ratio for a given issuer.
- debtBalanceOf: Returns the debt balance of a given issuer for a particular synth.
- totalIssuedSynths: Returns the total amount of synths issued for a given synth, optionally excluding synths that are collateralized by assets other than the SNX token.
- liquidateAccount: Liquidates an account, transferring their collateral to pay off their outstanding debt.
- addSynths: Adds new synths to the system.
- setCurrentPeriodId: Sets the current period ID for the system.

This is a contract called CollateralManager that implements the ICollateralManager interface, and inherits from the Owned, Pausable, and MixinResolver contracts. It includes several state variables, constants, mappings, and sets, along with the constructor function.

The using statements import various libraries to use throughout the contract, such as SafeMath for safe arithmetic operations, AddressSetLib for managing sets of addresses, and Bytes32SetLib for managing sets of bytes32 values.

The state variables include:

- state: an instance of the CollateralManagerState struct that stores debt balances and borrow rates.
- \_collaterals: an address set that stores all collateral contracts.
- \_currencyKeys: a bytes32 set that stores all available currency keys.
- \_synths: a bytes32 set that stores all synths issuable by the various collateral contracts.
- synthsByKey: a mapping from currency key to synth contract name.
- \_shortableSynths: a bytes32 set that stores all synths that are shortable.
- shortableSynthsByKey: a mapping from currency key to shortable synth contract name.
- utilisationMultiplier: a uint that scales the utilisation ratio.
- maxDebt: a uint that specifies the maximum amount of debt in sUSD that can be issued by non-SNX collateral.
- maxSkewRate: a uint that determines the skew limit maximum.
- baseBorrowRate: a uint that specifies the base interest rate applied to all borrows.
- baseShortRate: a uint that specifies the base interest rate applied to all shorts.

The constructor function takes in several parameters and sets the initial values for the state variables, as well as initializes the Owned, Pausable, and MixinResolver contracts. It also sets the owner variable twice, once in the Owned contract and once in the constructor itself.

This is a contract called CollateralManager that implements the ICollateralManager interface, and inherits from the Owned, Pausable, and MixinResolver contracts. It includes several state variables, constants, mappings, and sets, along with the constructor function.

The using statements import various libraries to use throughout the contract, such as SafeMath for safe arithmetic operations, AddressSetLib for managing sets of addresses, and Bytes32SetLib for managing sets of bytes32 values.

The state variables include:

- state: an instance of the CollateralManagerState struct that stores debt balances and borrow rates.
- \_collaterals: an address set that stores all collateral contracts.
- \_currencyKeys: a bytes32 set that stores all available currency keys.
- \_synths: a bytes32 set that stores all synths issuable by the various collateral contracts.
- synthsByKey: a mapping from currency key to synth contract name.
- \_shortableSynths: a bytes32 set that stores all synths that are shortable.
- shortableSynthsByKey: a mapping from currency key to shortable synth contract name.
- utilisationMultiplier: a uint that scales the utilisation ratio.
- maxDebt: a uint that specifies the maximum amount of debt in sUSD that can be issued by non-SNX collateral.
- maxSkewRate: a uint that determines the skew limit maximum.
- baseBorrowRate: a uint that specifies the base interest rate applied to all borrows.
- baseShortRate: a uint that specifies the base interest rate applied to all shorts.

The constructor function takes in several parameters and sets the initial values for the state variables, as well as initializes the Owned, Pausable, and MixinResolver contracts. It also sets the owner variable twice, once in the Owned contract and once in the constructor itself.

This is a contract called CollateralManager that implements the ICollateralManager interface, and inherits from the Owned, Pausable, and MixinResolver contracts. It includes several state variables, constants, mappings, and sets, along with the constructor function.

The using statements import various libraries to use throughout the contract, such as SafeMath for safe arithmetic operations, AddressSetLib for managing sets of addresses, and Bytes32SetLib for managing sets of bytes32 values.

The state variables include:

- state: an instance of the CollateralManagerState struct that stores debt balances and borrow rates.
- \_collaterals: an address set that stores all collateral contracts.
- \_currencyKeys: a bytes32 set that stores all available currency keys.
- \_synths: a bytes32 set that stores all synths issuable by the various collateral contracts.
- synthsByKey: a mapping from currency key to synth contract name.
- \_shortableSynths: a bytes32 set that stores all synths that are shortable.
- shortableSynthsByKey: a mapping from currency key to shortable synth contract name.
- utilisationMultiplier: a uint that scales the utilisation ratio.
- maxDebt: a uint that specifies the maximum amount of debt in sUSD that can be issued by non-SNX collateral.
- maxSkewRate: a uint that determines the skew limit maximum.
- baseBorrowRate: a uint that specifies the base interest rate applied to all borrows.
- baseShortRate: a uint that specifies the base interest rate applied to all shorts.

The constructor function takes in several parameters and sets the initial values for the state variables, as well as initializes the Owned, Pausable, and MixinResolver contracts. It also sets the owner variable twice, once in the Owned contract and once in the constructor itself.

The resolverAddressesRequired() function returns an array of addresses required by the contract's resolver. The function first defines an array staticAddresses containing two contract addresses, CONTRACT_ISSUER and CONTRACT_EXRATES. It then creates an empty array shortAddresses and populates it with the elements of \_shortableSynths if there are any. It then combines shortAddresses with the elements of \_synths to create an array synthAddresses. Finally, if synthAddresses contains any elements, the function combines it with staticAddresses to create the addresses array that is returned.

The isSynthManaged() function checks whether a given currencyKey is a synth managed by the contract by checking if synthsByKey[currencyKey] is not equal to bytes32(0).

The \_issuer() and \_exchangeRates() functions return instances of the IIssuer and IExchangeRates interfaces, respectively, by calling the requireAndGetAddress() function with the corresponding contract addresses.

- resolverAddressesRequired(): returns the addresses of the contracts that this contract depends on.
- isSynthManaged(bytes32 currencyKey): checks whether a synthetic asset is managed by the exchange.
- \_issuer(): returns an instance of the IIssuer contract that this contract depends on.
- \_exchangeRates(): returns an instance of the IExchangeRates contract that this contract depends on.
- \_synth(bytes32 synthName): returns an instance of a synthetic asset contract.
- hasCollateral(address collateral): checks whether a collateral address is registered with the exchange.
- hasAllCollaterals(address[] memory collaterals): checks whether all of the specified collateral addresses are registered with the exchange.
- long(bytes32 synth): returns the long balance of a synthetic asset.
- short(bytes32 synth): returns the short balance of a synthetic asset.
- totalLong(): returns the total long balance of all synthetic assets held in the exchange.
- totalShort(): returns the total short balance of all shortable synthetic assets held in the exchange.
- totalLongAndShort(): returns the total long and short balance of all synthetic assets held in the exchange.

The getBorrowRate() function calculates the borrowing rate for the platform by taking into account the total debt on the platform, the utilization ratio, and a multiplier and adding it to a base borrow rate.

The getShortRate() function calculates the shorting rate for a specific synthetic asset by taking into account the long and short supply of the asset, the skew towards the short side, and a maximum skew limit. This is then added to a base short rate.

The getRatesAndTime() and getShortRatesAndTime() functions retrieve the historical rates and timestamps for a specific index of rates.

Finally, the exceedsDebtLimit() function checks whether a new borrow or short order will exceed the maximum debt limit set for the platform. It does this by converting the amount to US dollars, adding it to the current long and short value on the platform, and checking whether it exceeds the maximum debt limit.

- setUtilisationMultiplier: Sets a variable utilisationMultiplier to a new value and emits an event UtilisationMultiplierUpdated.
- setMaxDebt: Sets a variable maxDebt to a new value and emits an event MaxDebtUpdated.
- setMaxSkewRate: Sets a variable maxSkewRate to a new value and emits an event MaxSkewRateUpdated.
- setBaseBorrowRate: Sets a variable baseBorrowRate to a new value and emits an event BaseBorrowRateUpdated.
- setBaseShortRate: Sets a variable baseShortRate to a new value and emits an event BaseShortRateUpdated.
- getNewLoanId: Returns a new unique ID for a loan.
- addCollaterals: Adds an array of addresses to a set \_collaterals if they are not already in the set and emits an event CollateralAdded for each address added.
- removeCollaterals: Removes an array of addresses from a set \_collaterals if they are in the set and emits an event CollateralRemoved for each address removed.
- addSynths: Adds an array of synth names and keys to two sets \_synths and \_currencyKeys if they are not already in the sets, sets the mapping synthsByKey to link synth keys to synth names, and emits an event SynthAdded for each synth added. Finally, it calls the function rebuildCache.
- areSynthsAndCurrenciesSet: Checks whether an array of synth names and keys are in the sets \_synths and synthsByKey respectively, and returns a boolean value.
- removeSynths: Removes an array of synth names and keys from the sets \_synths and \_currencyKeys respectively, deletes the corresponding entries in the synthsByKey mapping, and emits an event SynthRemoved for each synth removed.

The addShortableSynths function adds shortable synths to the contract. It takes two arrays as input - requiredSynthNamesInResolver which is an array of byte32 names of synths that should be added, and synthKeys which is an array of byte32 keys associated with the synths. The function checks if each synth is already in the \_shortableSynths set, and if not, adds it to the set using an external library \_addressSetLib. It also adds the associated synth key to the CollateralManagerState. Finally, it emits a ShortableSynthAdded event and rebuilds the cache.

The areShortableSynthsSet function checks if all the synths added to the \_shortableSynths set are also added to the external state contract. It takes the same input arrays as addShortableSynths. It returns true if all synths are set, otherwise false.

The removeShortableSynths function removes shortable synths from the contract. It takes an array of byte32 synths as input, and removes each synth that is in the \_shortableSynths set. It also removes the associated synth key from the CollateralManagerState, deletes the mapping between the synth key and synth name, and emits a ShortableSynthRemoved event.

The updateBorrowRates and updateShortRates functions are internal functions that update the borrow and short rates respectively, by calling the corresponding functions in the CollateralManagerState contract.

The updateBorrowRatesCollateral and updateShortRatesCollateral functions are external functions that update the borrow and short rates respectively, but they can only be called by the CollateralManager contract.

<table>
  <tr>
   <td>
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
  </tr>
</table>
