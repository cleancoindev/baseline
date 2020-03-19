# Components

The purpose of the Baseline Protocol initiative is to construct a design-pattern and set of reference implementations and tools that can help developers build _baselined_ offerings, as discussed elsewhere.

During the bootstrapping phase of the initiative \(March - June 2020\), this section will be used to articulate the generalized abstractions the community can glean from the Radish34 POC implementation.  

**The section below articulates a few of the known components, but this is a work in progress.**

![Components of a Baseline Protocol within a Radish34 POC Context](../.gitbook/assets/radish34-components%20%281%29.png)

## Messenger Service

The messenger service fulfills the requirement of decentralized private messaging between Parties. Each Party that runs a baselined system needs to send private data to Counter-parties. In the Radish34 supply chain use case, this private data is in the form of requests for proposals \(RFPs\), Service Contracts \(MSAs\), Purchase Orders, and secret keys/hashes used in the ZKP generation or verification processes. Also, this service adds message delivery receipts and durability to the messages by storing them locally.

### Radish34 Implementation

The messenger service used in the Radish34 demo was [Whisper](https://github.com/ethereum/go-ethereum/wiki/Whisper). This was convenient, as it is supported in Geth and provided a fast way to demonstrate the concept. 

Here's the [**link**](https://github.com/ethereum-oasis/baseline/tree/master/radish34/messenger) to the component as implemented in the Radish34 demo.

Convenient as it is, Whisper is not well supported at this time, and it has a number of drawbacks. Chief among these is that data is stored and forwarded by nodes on the way to the destination, potentially breaking prohibitions by GDPR and other regulations that stipulate that personally identifiable information not be stored, even in encrypted form, on machines not controlled by the responsible Parties or authorized Counter-parties. 

### Suggested Implementation

The ideal messaging service, which the Baseline  Protocol community will endeavor to specify and promote, would:

* Send data point-to-point, stored only by specified counter-parties with no intermediary storage; 
* Able to specify different counter-parties on a message-by-message \(or at least Workflow Step by Step\) basis; 
* Balance appropriate liveness and safety guarantees optimally for baselining; 
* Handle long session management without blocking and without "frankenstein" code.

It has been suggested that the Baseline Protocol community consider looking into the [Corda Flows](https://github.com/corda/corda) open source repository as an example of a potential design pattern that would suit baselining. If so, an effort to implement such a pattern in an Ethereum client such as Hyperledger Besu could be a way forward.

## ZK Service

zk-SNARK stands for "Zero Knowledge Succinct Non-Interactive Argument of Knowledge", and is a family of privacy tools called zero knowledge proofs \(ZKP\). While other privacy techniques like bulletproofs, ring signatures and stealth addresses, etc. mask the identities of the entities involved in a business transaction, ZKP techniques allow to prove logical statements without divulging any information and yet proving the validity of such proofs. Particularly, zk-SNARKs are mathematical concepts and tools to establish zero knowlege verification of succinct proofs, which convert logical statements to arithmetic circuits, that are then leveraged to generate proofs.

> Normally zk-SNARKs are used as a way to say "I have a secret" to someone and prove that you indeed have the secret without telling them what the secret is.   What the Baseline Protocol uses it for is a little different. It's more like saying to a specific set of counterparties, "We have a secret" and using a machine that we all can access to tell us that we all have the same secret \(or that we do not\) _without_ telling that machine anything that would let someone else with access to it discover anything about the secret...or even that we have one.

There is a good backgrounder on ZK and how it is used in the Radish34 demo in the repo here:

{% embed url="https://github.com/ethereum-oasis/baseline/blob/master/radish34/zkp/README.md" %}

### ZK in the Baseline Protocol

The Zero Knowledge \(ZK\) service in the Baseline Protocol does one essential thing and one optional thing:

**Essential**: Receives EdDSA signatures back from the Messenger service, validates that all signatures required by the Workflow Step are received and show consistency, and sends the Proof \(a hash\) and other verification/validation material to the Mainnet -- specifically into a Shield Contract that checks a Verifier contract  before depositing the Proof into a Merkle leaf inside the Shield Contract.

**Optional**: In cases where it is important that the business logic followed by all counterparties conforms to a set of specifications, a special ZK "Circuit" can be constructed in a domain-specific language \(DSL\) like [Zokrates](https://github.com/Zokrates/ZoKrates) to enforce "correctness." For example, in the Radish34 demo, the Master Service Agreement Circuit enforces, among other things,  that the rate table has no gaps between tiers. So a rate table where `0 - 10 units` is followed by   `12-22 units` would be rejected by the Circuit.

### Why Use a Special Circuit?

The core of the baseline approach is the notion of a _consistency machine_.  Consistency here indicates consistency of data and logical transformations therewith of the data. You want to know that the states of different machines are the same and then use that as flow control for enforcing the integrity of subsequent Workflow Steps. This can be done with a simple, standardized, repeatable ZK Circuit. Essentially, this lends to a standardized approach to plug a circuit for custom business logic that can be extensible and reusable depending on custom business scenarios and cases. 

It costs more in Mainnet $gas fees and in time to set up specialized circuits. But with advances in ZK, the cost has come down to where an initial test of the Radish34 MSA was in the low tens of cents, depending on the price of Ether. 

**An Opportunity for Standards and Regulation:** Typically, a small group of counterparties may be fine relying on a code package shared between them to be correct. All they need know is that everyone executed it correctly based on the same inputs, which generated the same outputs. But consider a set of companies in a highly regulated industry. An industry standards body could publish a set of Circuits/packages/libraries and require their use when regulated companies _baseline_ their Workflows. This would allow the regulated companies, potentially, to reduce the cost of oversight by ensuring the business logic they used to conduct business with their counterparties was done correctly and in accordance with the regulation...or at least with the enforcement Circuits provided. 

## UI Integration

In the Radish34 Demo, the team built a simple web-based UI to simulate the user experience of systems of record that were enabled with the Baseline Protocol. You can find that here:

{% embed url="https://github.com/ethereum-oasis/baseline/tree/master/radish34/ui" %}

In the real world, the user experience is the experience of any number of products.

If you are are in charge of the feature/benefit mix of a product in ERP, CRM, SCM, Core Banking or any other type of system of record, you can use the example in the Radish34 UI section to get ideas for how to integrate with baseline components \(whether they have added to your product's internal services or whether they have been deployed as services in your wider IT environment\) and present baselining benefits to users.


