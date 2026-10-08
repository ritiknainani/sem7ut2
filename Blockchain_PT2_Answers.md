# Blockchain PT-2: Complete Exam Guide

**Mumbai University · Sem 7 Computer Engineering · Internal Assessment 2 · 20 Marks · 1 Hour**

Syllabus: Module 2 (Cryptocurrency) · Module 4 (Public Blockchain) · Module 5 (Private Blockchain) · Module 6 (Tools and Applications)

---

## Part A: Question Bank Analysis and Strategy

### Exam pattern

- Total: **20 marks in 60 minutes**.
- Typical split: 4 × 5 marks, or 2 × 10 marks with internal choice.
- Budget about **12 minutes per 5-mark answer**, roughly one page. Keep 5 minutes to read the paper and 5 to revise.

### Priority matrix

| Priority | Questions |
| --- | --- |
| 🔴 **HIGHEST** | Q2 Fabric v1 architecture · Q5 Ethereum architecture · Q11 PoW/PoS/PoET · Q6 Public/Private/Consortium · Q12 Bitcoin vs Ethereum · Q9 Double spending |
| 🟠 **HIGH** | Q3 RAFT · Q10 PAXOS · Q8 Gas and Ether · Q1 short notes (Fabric, EVM, Corda) |
| 🟡 **MEDIUM** | Q4 Types of cryptocurrencies · Q7 Hot vs Cold wallets · Q1 Ripple, DeFi |

### Core tips

- **Comparison questions (6, 7, 11, 12) are free marks.** Write a table with 6-8 rows, one parameter per row.
- **A diagram earns mandatory marks** in Q2, Q3, Q5, Q10, and in the EVM and DeFi notes.
- **Learn Q3 and Q10 together.** Raft is a simplified, easier-to-understand Paxos.
- **Short notes:** 6-8 crisp points plus one example. No essays.
- **To secure 20 marks:** prepare all 🔴 and 🟠 topics fully, and the 🟡 topics in 5-point form.

---

## Part B: Direct High-Yield Answers

## Q1. Short Notes (5 marks each)

### a) Corda

- Open-source **permissioned DLT by R3**, built for finance and regulated industries.
- **No global broadcast.** Data is shared only with the parties involved (point-to-point).
- **UTXO-style model.** *States* are immutable facts. *Transactions* consume input states and create output states.
- **Notary service** provides consensus: *uniqueness* (prevents double spend) and optionally *validity*.
- **CorDapps** (Kotlin/Java) contain states, contracts and flows.
- Each node keeps a **vault**, its own view of the ledger. Contracts can reference legal prose.
- **Use cases:** trade finance, insurance, settlements.

### b) Ripple

- **Real-time gross settlement and remittance network** (RippleNet) built on the **XRP Ledger**, with native token **XRP**.
- **Consensus: RPCA** (Ripple Protocol Consensus Algorithm). Each validator trusts a **UNL** (Unique Node List). A transaction is accepted when **at least 80%** of the UNL agrees.
- No mining, so it is fast (about 3-5 seconds) and cheap.
- XRP acts as a **bridge currency** for cross-border payments (Bank A → XRP → Bank B).
- **Components:** ledger, validators, gateways, XRP.

### c) Hyperledger Fabric

- Linux Foundation project: a **permissioned, modular** enterprise blockchain.
- **Channels** give private transactions between subsets of members.
- **Chaincode** (Go/Java/Node.js) is the smart contract.
- **Peers** (endorsing/committing), **orderer**, **MSP** and **CA** handle execution and identity.
- Uses the **Execute → Order → Validate** model, with pluggable consensus (Raft).
- **Ledger** = blockchain + world state (LevelDB/CouchDB).

### d) DeFi Architecture (4 layers)

```
+---------------------------------------------------+
| Aggregation Layer  (1inch, Zapper)                |  combines apps/services
+---------------------------------------------------+
| Application Layer  (Uniswap UI, wallets)          |  end-user dApps
+---------------------------------------------------+
| Protocol Layer     (Aave, MakerDAO, Compound)     |  standards / smart contracts
+---------------------------------------------------+
| Settlement Layer   (Ethereum, ETH, stablecoins)   |  base chain
+---------------------------------------------------+
```

- **Traits:** permissionless, non-custodial, transparent, composable ("money legos").
- **Uses:** lending/borrowing, DEXs, stablecoins, yield farming.

### e) Ethereum Virtual Machine (EVM)

- **Sandboxed, deterministic, stack-based (256-bit word) runtime** that executes smart contract **bytecode** on every node.
- **Flow:** `Solidity → compiler → bytecode → deployed → EVM executes opcodes`.
- **Data areas:** **Stack** (temporary), **Memory** (volatile), **Storage** (persistent, costly), **Calldata** (read-only input).
- Every opcode costs **gas**, which prevents infinite loops.
- Turing-complete. Identical results on all nodes give consensus on state.

```
Transaction -> EVM -> [Stack | Memory | Storage] -> new state
                 |
            gas metering (halts at out-of-gas)
```

---

## Q2. Hyperledger Fabric v1 Architecture (10 marks)

**Components**

- **Client / SDK:** submits transaction proposals.
- **Peers:** hold the ledger and chaincode.
  - *Endorsing peers* simulate and sign.
  - *Committing peers* validate and write.
- **Orderer:** orders transactions into blocks (Solo/Kafka/Raft). It **does not** execute chaincode.
- **MSP + CA:** identity and membership.
- **Channel:** a separate ledger for a subset of members.
- **Ledger:** blockchain (immutable log) + world state (current key-value pairs).

**Transaction flow**

```
 Client          Endorsing Peers        Orderer          Committing Peers
   |--1.Proposal------->|                  |                    |
   |<-2.Simulate + Sign-|                  |                    |
   |   (read/write set + endorsement)      |                    |
   |--3.Submit endorsed Tx---------------->|                    |
   |                                4.Order, cut block          |
   |                                       |--5.Deliver block-->|
   |                                       |   6.Validate (endorsement policy, MVCC)
   |                                       |   7.Commit + update world state
   |<----------------8.Event notification---------------------|
```

**Keywords to write:** execute-order-validate · endorsement policy · read-write set · MVCC check · invalid transactions are still stored in the block but flagged.

---

## Q3. RAFT Consensus for Private Blockchain (5-10 marks)

- **Crash Fault Tolerant (CFT)**, not Byzantine. A simpler alternative to Paxos. Used in Fabric and Quorum.
- **Node states:** Follower → Candidate → Leader.
- **Term:** a logical clock. Each election starts a new term.

```
       timeout, no heartbeat            majority votes
Follower ------------------> Candidate ----------------> Leader
    ^                            |                          |
    |<---- higher term / leader -+                          |
    |<------------ discovers higher term ------------------+
```

**Steps**

1. **Leader election:** a follower whose randomized timeout (150-300 ms) expires becomes a candidate, increments the term and sends `RequestVote`. A **majority** of votes makes it leader.
2. **Heartbeats:** the leader sends periodic `AppendEntries` to stay in charge.
3. **Log replication:** the client sends a command to the leader, who appends it to its log and replicates it. When a **majority** acknowledges, the entry is **committed**, applied, and the client gets a response.
4. **Safety:** a node with a stale log cannot win an election. If the leader fails, a new election is held.

**Fault tolerance:** a cluster of 2f+1 nodes tolerates f failures (for example, 5 nodes tolerate 2).

---

## Q4. Types of Cryptocurrencies (5 marks)

| Type | Description | Examples |
| --- | --- | --- |
| **Payment / store of value** | First-generation digital cash | Bitcoin, Litecoin |
| **Platform / smart-contract coins** | Run dApps; the coin pays gas | Ether, Solana, Cardano |
| **Altcoins** | Any coin other than Bitcoin | Litecoin, Dogecoin |
| **Tokens** | Built on another blockchain (ERC-20/721). Sub-types: *utility, security, governance, NFT* | UNI, LINK, NFTs |
| **Stablecoins** | Pegged to fiat or assets, so low volatility | USDT, USDC, DAI |
| **Privacy coins** | Hide sender, receiver and amount | Monero, Zcash |
| **Exchange / meme coins** | Fee discounts, community-driven | BNB, Shiba Inu |

> **Add this line:** *coins* have their own blockchain, while *tokens* run on another chain.

---

## Q5. Ethereum Architecture (10 marks)

```
+-------------------------------------------------------+
|  dApps / Wallets / Web3 libraries  (JSON-RPC)         |
+-------------------------------------------------------+
|  Smart Contracts  (Solidity -> bytecode)              |
+-------------------------------------------------------+
|  EVM  (executes bytecode, gas metering)               |
+-------------------------------------------------------+
|  State: Accounts (EOA + Contract) [Merkle Patricia Trie]|
|  Blockchain: Blocks (header, txs, state root)         |
+-------------------------------------------------------+
|  Consensus (PoS; earlier PoW) + P2P Network           |
+-------------------------------------------------------+
```

**Components**

- **Accounts:**
  - *EOA* is controlled by a private key.
  - *Contract account* is controlled by code.
  - Fields: `nonce`, `balance`, `storageRoot`, `codeHash`.
- **Transactions:** transfer ETH, deploy contracts, call contracts. They carry a gas limit and gas price.
- **Block:** header (parent hash, state root, transaction root, receipts root, timestamp) + transactions.
- **State:** world state stored in a **Merkle Patricia Trie**. Ethereum is **account-based**.
- **EVM:** the runtime for contracts.
- **Consensus:** PoS (Gasper) since **the Merge (2022)**. Earlier it was PoW (Ethash).
- **Network:** P2P nodes (full, light, archive).
- **Gas** is the execution fee mechanism. **Ether** is the native currency.

---

## Q6. Public vs Private vs Consortium Blockchain (5 marks)

| Parameter | Public | Private | Consortium |
| --- | --- | --- | --- |
| Access | Open to all | Single organization | Group of organizations |
| Permission | Permissionless | Permissioned | Permissioned |
| Control | Decentralized | Centralized | Semi-decentralized |
| Consensus | PoW / PoS | Raft / PBFT | PBFT / Raft / PoA |
| Speed / scalability | Slow, low TPS | Fast | Fast |
| Transparency | Full | Restricted | Partial |
| Immutability | Very high | Owner can alter | Moderate |
| Trust | Trustless | Trusted owner | Trust among members |
| Examples | Bitcoin, Ethereum | Internal Hyperledger, Multichain | Corda, Quorum, Fabric consortium |

---

## Q7. Hot vs Cold Wallets (5 marks)

| Parameter | Hot Wallet | Cold Wallet |
| --- | --- | --- |
| Connectivity | Online | Offline |
| Security | Vulnerable to hacking and malware | Very secure |
| Convenience | Easy, fast transactions | Slower, manual signing |
| Use | Daily / small trading | Long-term storage (HODL) |
| Cost | Mostly free | Hardware costs money |
| Private key | On an internet-connected device | Stored offline |
| Examples | MetaMask, Trust Wallet, exchange wallets | Ledger, Trezor, paper wallet |

---

## Q8. Gas and Ether in Detail (5-10 marks)

**Ether (ETH):** the native cryptocurrency of Ethereum. It pays fees, rewards validators, and is staked in PoS.

$$1\ \text{ETH} = 10^{9}\ \text{Gwei} = 10^{18}\ \text{Wei}$$

**Gas:** a unit measuring the **computational effort** of an operation. It prevents spam and infinite loops, and separates computation cost from ETH's price volatility.

- **Gas limit:** the maximum gas the user will spend. A simple ETH transfer needs **21,000**.
- **Gas price:** price per gas unit, in Gwei.
- **After EIP-1559:** *base fee* (burned) + *priority fee / tip* (paid to the validator).

$$\text{Tx Fee} = \text{Gas Used} \times \text{Gas Price}$$

$$\text{Tx Fee (EIP-1559)} = \text{Gas Used} \times (\text{Base Fee} + \text{Priority Fee})$$

**Worked example:** a transfer uses 21,000 gas at 50 Gwei.

1. Fee = 21,000 × 50 = 1,050,000 Gwei
2. 1 Gwei = 10^-9 ETH, so Fee = 1,050,000 × 10^-9 ETH
3. **Final answer: 0.00105 ETH**

If gas used is below the gas limit, the unused gas is refunded. If gas runs out, the transaction fails and the fee is still lost.

---

## Q9. Double Spending Problem (5-10 marks)

**Definition:** spending the same digital coin more than once. It is possible because digital data can be copied.

**Example:** Alice has 1 BTC and sends it to both Bob and Carol using the same input.

**Attack types:** race attack, Finney attack, 51% attack.

**How algorithms solve it**

1. **Proof of Work:** miners validate transactions into blocks and the **longest chain** wins. Altering a confirmed block means redoing the PoW of every later block.
2. **Confirmations:** wait about 6 blocks for safety.
3. **UTXO tracking:** once an output is spent it is marked spent, and every node rejects a second spend of the same input.
4. **PoS:** an attacker risks **slashing** of their stake.
5. **Notary (Corda) / ordering service (Fabric):** check uniqueness of inputs.

```
Alice --Tx1 (UTXO#5)--> Bob   ---> mined in Block 101   (valid)
Alice --Tx2 (UTXO#5)--> Carol ---> REJECTED (input already spent)
```

---

## Q10. PAXOS (5-10 marks)

- Consensus protocol for **crash-fault-tolerant** distributed systems, by Lamport. Used in Google Chubby.
- **Roles:** **Proposer** (suggests a value), **Acceptor** (votes), **Learner** (learns the decided value).
- **Quorum:** a majority of acceptors.

**Phase 1: Prepare / Promise**

1. The proposer sends `Prepare(n)` with a unique proposal number n.
2. An acceptor promises not to accept proposals numbered below n, and returns any value it has already accepted.

**Phase 2: Accept / Accepted**

3. If a majority promised, the proposer sends `Accept(n, v)`. It uses the highest-numbered previously accepted value if one was returned, else its own.
4. Acceptors accept unless they promised a higher number, then notify learners. The value is **chosen** when a majority accepts.

```
Proposer         Acceptors            Learners
   |--Prepare(n)-->|                     |
   |<--Promise-----|                     |
   |--Accept(n,v)->|                     |
   |<--Accepted----|--Accepted(v)------->|
```

**Drawbacks:** complex to implement, and dueling proposers can cause livelock. **Raft** was designed to be simpler.

---

## Q11. Proof of Work vs Proof of Stake vs Proof of Elapsed Time (5-10 marks)

| Parameter | Proof of Work | Proof of Stake | Proof of Elapsed Time |
| --- | --- | --- | --- |
| Selection basis | Computational power (hash puzzle) | Amount of coins staked | Random wait time (shortest wins) |
| Resource | Electricity, ASICs | Staked coins | Trusted hardware (**Intel SGX**) |
| Energy use | Very high | Low | Very low |
| Speed | Slow | Faster | Fast |
| Attack risk | 51% hash power | 51% stake | Hardware / TEE compromise |
| Reward | Block reward + fees | Fees (+ issuance) | Mostly fees |
| Network type | Public | Public | Permissioned / consortium |
| Centralization risk | Mining pools | Wealthy validators | Hardware vendor |
| Examples | Bitcoin, Litecoin | Ethereum, Cardano | Hyperledger Sawtooth |

**One-liners**

- **PoW:** find a nonce such that `Hash(block) < Target`.
- **PoS:** a validator is chosen in proportion to stake, and a bad actor is *slashed*.
- **PoET:** each node gets a random wait time from a TEE, and the first to finish creates the block.

---

## Q12. Compare Bitcoin and Ethereum (5-10 marks)

| Parameter | Bitcoin | Ethereum |
| --- | --- | --- |
| Creator | Satoshi Nakamoto (2009) | Vitalik Buterin (2015) |
| Purpose | Digital currency / store of value | Decentralized application platform |
| Currency | BTC | ETH (Ether) |
| Smart contracts | Limited (Script) | Full (Solidity, Turing-complete) |
| Consensus | PoW | PoS (since 2022) |
| Block time | About 10 min | About 12 s |
| Supply | Capped at 21 million | No hard cap |
| Transaction model | UTXO | Account-based |
| Virtual machine | None | EVM |
| Fees | Per byte (sat/vB) | Gas |
| Hash function | SHA-256 | Keccak-256 |
| Throughput | About 7 TPS | About 15-30 TPS (higher with L2) |

---

## Part C: Last-Minute Revision Sheet

### UTXO vs Account-Based

| Parameter | UTXO (Bitcoin, Corda) | Account (Ethereum) |
| --- | --- | --- |
| State | Set of unspent outputs | Balance per account |
| Transfer | Consumes inputs, creates outputs (+ change) | Debit sender, credit receiver |
| Double-spend check | Is the input already spent? | Nonce + balance |
| Privacy | Better (fresh addresses) | Lower |
| Parallelism | Easy | Harder |
| Smart contracts | Limited | Easy |

### Key formulas and terms

| Item | Formula / Fact |
| --- | --- |
| Transaction fee | `Gas Used x Gas Price` |
| Ether units | `1 ETH = 10^9 Gwei = 10^18 Wei` |
| Simple transfer gas | 21,000 |
| PoW condition | `Hash(Header + nonce) < Target` |
| Bitcoin difficulty retarget | Every 2016 blocks: `New Diff = Old Diff x (2016 x 10 min) / Actual time` |
| Merkle root | Hash pairs upward: `H(H_A + H_B)`. An odd leaf is duplicated |
| Raft / Paxos tolerance | `N >= 2f + 1` (crash faults) |
| PBFT tolerance | `N >= 3f + 1` (Byzantine faults) |
| Ripple RPCA | At least 80% UNL agreement |
| Bitcoin supply / block time | 21M / 10 min |
| Ethereum block time | About 12 s |

**Quick tags:** Fabric = *execute-order-validate* · Corda = *notary, no broadcast* · Ripple = *RPCA, XRP* · EVM = *stack, bytecode, gas* · PoET = *Intel SGX*.

### Mini worked examples (for numericals)

**Merkle root, 4 transactions A, B, C, D**

1. Leaves: `H_A, H_B, H_C, H_D`
2. Level 1: `H_AB = H(H_A + H_B)` and `H_CD = H(H_C + H_D)`
3. **Root:** `H_ABCD = H(H_AB + H_CD)`

With 3 transactions (A, B, C), duplicate C: `H_CC = H(H_C + H_C)`, then Root = `H(H_AB + H_CC)`.

**Difficulty retarget:** the last 2016 blocks took 18,144 minutes instead of the expected 20,160.

1. Ratio = 20,160 / 18,144 = 1.111
2. **New difficulty = Old difficulty × 1.111** (mining gets harder, because blocks came too fast)

**UTXO transfer:** Alice holds one UTXO of 5 BTC and pays Bob 2 BTC with a 0.1 BTC fee.

1. Input: 5 BTC (consumed fully)
2. Output 1: 2 BTC to Bob
3. Output 2 (change): 5 - 2 - 0.1 = **2.9 BTC** back to Alice
4. The 0.1 BTC difference goes to the miner as the fee

### Common pitfalls that lose marks

1. **No diagram** in Q2, Q3, Q5, Q10 (lost mandatory marks).
2. **Comparison without a table**, or with fewer than 6 points.
3. Confusing **Raft (CFT)** with **PBFT (BFT)**.
4. Writing Ethereum as PoW only. **Mention the Merge (PoS, 2022).**
5. Forgetting **units** in gas calculations (Gwei vs ETH).
6. Mixing up Fabric roles. **Orderer ≠ endorser**: the orderer never runs chaincode.
7. Mixing up *coins vs tokens* in Q4.
8. No **real-world examples** (Bitcoin, Corda, MetaMask, Ledger, etc.).
9. Long paragraphs. MU examiners prefer **headings + bullets + a diagram**.
10. Leaving the **final answer unmarked** in numericals. Underline or box it.

**Time plan (60 min):** 5 min to read and choose · 12 min per 5-mark answer · 5 min to revise. Attempt your strongest 🔴 questions first.

Good luck with PT-2! 🎯
