# Transactions
- An Ethereum transaction refers to an action initiated by an externally-owned account, in other words an account 
managed by a human, not a contract.
- Each transaction on Ethereum contains a value field, which specifies the amount of ether to be transferred, 
denominated in wei, to send from the sender's address to the recipient address.
- When the recipient address is a smart contract, this transferred ether may be used to pay for gas when the smart 
contract executes its code.
- While your transaction is being applied to the database, no other transaction can alter it.
- A transaction is always cryptographically signed by the sender (creator)
- If two transactions contradict each other, the one that ends up being second will be rejected and not become part 
of the block.
- A transaction is a message that is sent from one account to another account (which might be the same or empty, see 
below). It can include binary data (which is called “payload”) and Ether.
  - If the target account contains code, that code is executed and the payload is provided as input data.
  - If the target account is not set (the transaction does not have a recipient or the recipient is set to null), the 
  transaction creates a new contract
- Transaction includes the following fields: from, to, signature, nonce, gasLimit, value, input-data, maxPriorityFeePerGas, maxFeePerGas
- data field is used to specify the input data for the contract. It is limited to 32 bytes.
  - first four bytes of the data field are the function selector, which is the first four bytes of the Keccak-256 hash of the signature of the function.
  - The remaining bytes are the parameters of the function.
- Types of transactions:
  - Value transfer transactions
  - Contract creation transactions
  - Contract interaction transactions

# Accounts
- Ethereum has two account types:
  - Externally-owned account (EOA) – controlled by anyone with the private keys
  - Contract account – a smart contract deployed to the network, controlled by code. Learn about smart contracts
    Externally-owned

- Creating an account costs nothing
  - Can initiate transactions
  - Transactions between externally-owned accounts can only be ETH/token transfers
  - Made up of a cryptographic pair of keys: public and private keys that control account activities

- Contract
  - Creating a contract has a cost because you're using network storage
  - Can only send transactions in response to receiving a transaction
  - Transactions from an external account to a contract account can trigger code which can execute many different actions, such as transferring tokens or even creating a new contract
  - Contract accounts don't have private keys. Instead, they are controlled by the logic of the smart contract code

- Ethereum accounts have four fields: nonce, balance, storageRoot, and codeHash
  - Nonce: A counter used to ensure each transaction can only be processed once
  - Balance: The amount of ether in the account
  - StorageRoot: The hash of the root node of the account's storage trie
  - CodeHash: The hash of the EVM code of the account

- You never really hold cryptocurrency, you hold private keys – the funds are always on Ethereum's ledger.
- When you want to create an account, most libraries will generate you a random private key.
- The contract address comes(derived/based on) from the creator's address and the number of transactions sent from that 
address (the “nonce”).

# Blocks
- By spacing out commits, ethereum give all network participants enough time to come to consensus: even though 
transaction requests occur dozens of times per second, blocks are only created and committed on Ethereum once every 
twelve seconds.
-  blocks are strictly ordered (every new block created contains a reference to its parent block), and transactions 
within blocks are strictly ordered as well


# EVM - Storage, Memory and the Stack
- storage - Each account has a data area called storage. Its costly to read and write from storage.  A contract can neither read 
nor write to any storage apart from its own.
- memory - contract obtains a receive a freshly cleared instance of memory and has access to the call payload - which 
will be provided in a separate area called the calldata. Memory is linear and can be addressed at 
byte level, but reads are limited to a width of 256 bits, while writes can be either 8 bits or 256 bits wide.
- stack - all computations are performed on a data area called the stack. It has a maximum size of 1024 elements and 
contains words of 256 bits.

# Contracts 
- A contract in the sense of Solidity is a collection of code (its functions) and data (its state) that resides at a 
specific address on the Ethereum blockchain.
- The constructor is a special function that is executed during the creation of the contract and cannot be called afterwards.
- The address of a contract is determined at the time the contract is created (it is derived from the creator address and 
the number of transactions sent from that address, the so-called “nonce”)
- Each contract can contain declarations of State Variables, Functions, Function Modifiers, Events, Errors, Struct Types 
and Enum Types. Furthermore, contracts can inherit from other contracts

### Initialization Order
The place where you initialize each variable determines when the initialization code will run. During initialization the code is executed in the following order:

- Expressions used as initializers in state variable declarations (least to most derived).
- Base constructor arguments (most to the least derived).
- Constructor bodies (least to the most derived).
If the initialization of each variable is independent, this makes no practical difference. You might simply get bytecode 
- that is functionally equivalent but has some instructions ordered slightly differently.

```solidity
contract A {
    string[] order;

    constructor(uint _x) {
        log("constructor of A");
    }

    function log(string memory _word) internal returns (uint) {
        order.push(_word);
        return 42;
    }
}

contract B is A {
    uint b = log("declaration of b");

    constructor(uint _x) A(log("args of A")) {
        log("constructor of B");
    }
}

contract C is B {
    uint c = log("declaration of c");

    constructor() B(log("args of B")) {
        log("constructor of C");
    }

    function get() public view returns (string[] memory) {
        return order;
    }
}

```
If you deploy the code above and run C.get(), you'll see the following values:

* `declaration of b`
* `declaration of c`
* `args of B`
* `args of A`
* `constructor of A`
* `constructor of B`
* `constructor of C`

# Method Calls
- Contracts can call other contracts or send Ether to non-contract accounts by the means of message calls. Message
  calls are similar to transactions, in that they have a source, a target, data payload, Ether, gas and return data.
  In fact, every transaction consists of a top-level message call which in turn can create further message calls.
- `delegatecall` which is identical to a message call apart from the fact that the code at the target address is executed
  in the context (i.e. at the address) of the calling contract and msg.sender and msg.value do not change their values.
- This means that a contract can dynamically load code from a different address at runtime. Storage, current 
address and balance still refer to the calling contract, only the code is taken from the called address.
- This makes it possible to implement the “library” feature in Solidity: Reusable library code that can be applied to 
a contract’s storage, e.g. in order to implement a complex data structure.

# Solidity Files
- Pragmas - `version pragma`, `abi pragma`, `experimental pragma`
- import - `import "filename";`, `import * as symbolName from "filename";`, `import {symbol1 as alias, symbol2} from "filename";`


# Logs
- Logs is used by Solidity in order to implement events. Contracts cannot access log data after it has been created, 
but they can be efficiently accessed from outside the blockchain.


# Types
- Mappings can be seen as hash tables which are virtually initialized such that every possible key exists from the 
start and is mapped to a value whose byte-representation is all zeros. However, it is neither possible to obtain a 
list of all keys of a mapping, nor a list of all values.


# Access Modifiers
- `public` - The keyword public automatically generates a function that allows you to access the current value of the state variable from outside of the contract. Without this keyword, other contracts have no way to access the variable. The compiler automatically creates getter functions for all public state variables. 

# Events
- Ethereum clients such as web applications can listen for these events emitted on the blockchain without much cost.
- 

# Validation
- Errors allow you to provide more information to the caller about why a condition or operation failed. Errors are used together with the revert statement. The revert statement unconditionally aborts and reverts all changes, much like the require function. Both approaches allow you to provide the name of an error and additional data which will be supplied to the caller (and eventually to the front-end application or block explorer) so that a failure can more easily be debugged or reacted upon.
- The require function call defines conditions that reverts all changes if not met

# Functions
`function (<parameter types>) {internal|external} [pure|view|payable] [returns (<return types>)]`

- The return types cannot be empty - if the function type should not return anything, the whole returns (<return types>) part has to be omitted
- By default, function types are internal, so the internal keyword can be omitted
- Visibility has to be specified explicitly for functions defined in contracts, they do not have a default
- `internal` type can be assigned to private, internal and public functions of both contracts and libraries as well as free functions
- `external` are only compatible with public and external contract functions.

### Memory
Values that are only stored for the lifetime of a contract function's execution are called memory variables. Since
these are not stored permanently on the blockchain, they are much cheaper to use.

### Two types of functions
There are two types of function calls:
1. `internal` – these don't create an EVM call . Internal functions and state variables can only be accessed internally
   (i.e. from within the current contract or contracts deriving from it)
2. `external` – these do create an EVM call. External functions are part of the contract interface, which means they
   can be called from other contracts and via transactions. An external function f cannot be called internally (i.e. f()

### They can also be public or private
- `public` functions can be called internally from within the contract or externally via messages
- `private` functions are only visible for the contract they are defined in and not in derived contracts
  Both functions and state variables can be made public or privatedoes not work, but this.f() works).

### View and Pure functions
- `view` – declares that no state will be changed
- `pure` – declares that no state variable will be changed or read
  `view` and `pure` functions do not cost gas when called externally by a contract or transaction.

### Modifying state
What is considered modifying state:
- Writing to state variables.
- Emitting events.
- Creating other contracts.
- Using selfdestruct.
- Sending ether via calls.
- Calling any function not marked view or pure.
- Using low-level calls.
- Using inline assembly that contains certain opcodes.

### Constructor
constructor functions are only executed once when the contract is first deployed. They are optional and can be used to
initialize contract state.

The Constructor Caveat

### Fallback function
A contract can have a fallback function that is executed if a function is called that does not exist or if no data is
sent with the transaction. The fallback function is declared using the `fallback` keyword.

