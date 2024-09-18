# Nodes and clients?
- A "node" is any instance of Ethereum client software that is connected to other computers also running Ethereum 
software, forming a network
- A client is an implementation of Ethereum that verifies data against the protocol rules and keeps the network secure. 
- A node has to run two clients: a consensus client and an execution client. 
    - Execution client: listens to new transactions broadcasted in the network, executes them in EVM, and holds the 
      latest state and database of all current Ethereum data.
    - Consensus client: implements the proof-of-stake consensus algorithm, which enables the network to achieve 
      agreement based on validated data from the execution client
- There is also a third piece of software, known as a 'validator' that can be added to the consensus client, allowing 
a node to participate in securing the network.
- Both execution clients and consensus clients exist in a variety of programming languages developed by different teams.
- Node Types:
    - Full Node: A full node stores the entire history of the blockchain and validates every transaction and block.
    - Light Node: A light node only stores the block headers and can verify the proof of work. It relies on full nodes 
      to provide block data.
    - Archive Node: An archive node stores the entire history of the blockchain and all states.

# Node Architecture
An Ethereum node is composed of two clients: an execution client and a consensus client.

## Execution Client
- It is responsible for listening to new transactions broadcasted in the network
- Executing transactions in the EVM
- Re-executing transactions in new blocks to ensure they are valid
- Holding the latest state and database of all current Ethereum data
- Providing an API for other software to interact with the Ethereum network

## Consensus Client
- It is responsible implementing the proof-of-stake consensus algorithm
- Deals with all the logic that enables a node to stay in sync with the Ethereum network. This includes receiving 
  blocks from peers and running a fork choice algorithm to ensure the node always follows the chain with the 
  greatest accumulation of attestations (weighted by validator effective balances).
- It is responsible for block building, block gossiping
- Achieving agreement based on validated data from the execution client
- The consensus client does not participate in attesting to or proposing blocks - this is done by a validator, an 
  optional add-on to a consensus client. A consensus client without a validator only keeps up with the head of the 
  chain, allowing the node to stay synced.Securing the network by participating in the consensus process
- Providing an API for other software to interact with the Ethereum network

## Validator
- Node operators can add a validator to their consensus clients by depositing 32 ETH in the deposit contract. 
- The validator client comes bundled with the consensus client and can be added to a node at any time. 
- The validator handles attestations and block proposals. 
- They enable a node to accrue rewards or lose ETH via penalties or slashing. 
- Running the validator software also makes a node eligible to be selected to propose a new block.

