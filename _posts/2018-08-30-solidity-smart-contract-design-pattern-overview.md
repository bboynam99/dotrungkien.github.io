---
layout: post
title: "Design Pattern In Solidity Smart Contract - Overview"
mathjax: true
---

Đây là chuỗi bài mình tổng hợp & dịch lại về các design pattern trong solidity smart contract.

# Design pattern list:

- [**Contract Self Destruction**](https://medium.com/@i6mi6/solidty-smart-contracts-design-patterns-ecfa3b1e9784)
- [**Factory Contract**](https://medium.com/@i6mi6/solidty-smart-contracts-design-patterns-ecfa3b1e9784)
- [**Name Registry**](https://medium.com/@i6mi6/solidty-smart-contracts-design-patterns-ecfa3b1e9784)
- [**Mapping Iterator**](https://medium.com/@i6mi6/solidty-smart-contracts-design-patterns-ecfa3b1e9784)
- [**Sending ether from contract: Withdrawal pattern**](https://medium.com/@i6mi6/solidty-smart-contracts-design-patterns-ecfa3b1e9784)

- **Behavioral Patterns**
  - [**Guard Check**](docs/guard_check.md): Ensure that the behavior of a smart contract and its input parameters are as expected.
  - [**State Machine**](docs/state_machine.md): Enable a contract to go through different stages with different corresponding functionality exposed.
  - [**Oracle**](docs/oracle.md): Gain access to data stored outside of the blockchain.
  - [**Randomness**](docs/randomness.md): Generate a random number of a predefined interval in the deterministic environment of a blockchain.
- **Security Patterns**
  - [**Access Restriction**](docs/access_restriction.md): Restrict the access to contract functionality according to suitable criteria.
  - [**Checks Effects Interactions**](docs/checks_effects_interactions.md): Reduce the attack surface for malicious contracts trying to hijack control flow after an external call.
  - [**Secure Ether Transfer**](docs/secure_ether_transfer.md): Secure transfer of ether from a contract to another address.
  - [**Pull over Push**](docs/pull_over_push.md): Shift the risk associated with transferring ether to the user.
  - [**Emergency Stop**](docs/emergency_stop.md): Add an option to disable critical contract functionality in case of an emergency.
- **Upgradeability Patterns**
  - [**Proxy Delegate**](docs/proxy_delegate.md): Introduce the possibility to upgrade smart contracts without breaking any dependencies.
  - [**Eternal Storage**](docs/eternal_storage.md): Keep contract storage after a smart contract upgrade.
- **Economic Patterns**
  - [**String Equality Comparison**](docs/string_equality_comparison.md): Check for the equality of two provided strings in a way that minimizes average gas consumption for a large -umber of different inputs.
  - [**Tight Variable Packing**](docs/tight_variable_packing.md): Optimize gas consumption when storing or loading statically-sized variables.
  - [**Memory Array Building**](docs/memory_array_building.md): Aggregate and retrieve data from contract storage in a gas efficient way.