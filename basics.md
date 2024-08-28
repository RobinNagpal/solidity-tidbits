# Contracts 
- A contract in the sense of Solidity is a collection of code (its functions) and data (its state) that resides at a specific address on the Ethereum blockchain.
- The constructor is a special function that is executed during the creation of the contract and cannot be called afterwards.

# Types
- Mappings can be seen as hash tables which are virtually initialized such that every possible key exists from the start and is mapped to a value whose byte-representation is all zeros. However, it is neither possible to obtain a list of all keys of a mapping, nor a list of all values.

# Access Modifiers
- `public` - The keyword public automatically generates a function that allows you to access the current value of the state variable from outside of the contract. Without this keyword, other contracts have no way to access the variable. The compiler automatically creates getter functions for all public state variables. 
# Events
- Ethereum clients such as web applications can listen for these events emitted on the blockchain without much cost.
- 

# Validation
- Errors allow you to provide more information to the caller about why a condition or operation failed. Errors are used together with the revert statement. The revert statement unconditionally aborts and reverts all changes, much like the require function. Both approaches allow you to provide the name of an error and additional data which will be supplied to the caller (and eventually to the front-end application or block explorer) so that a failure can more easily be debugged or reacted upon.
- The require function call defines conditions that reverts all changes if not met
