# Improving Blockchain Scalability: Throughput Enhancement in ZK-Rollups

**University of Moratuwa**  
**Faculty of Engineering**  
**Department of Computer Science and Engineering**

**Module No:** CS4203  
**Project Proposal Submission:** September 23, 2025

## Team Members - Group "Rollup"

- **Fernando T.H.L** (210167E)
- **Gamage M.S** (210176F)
- **Lishan S.D** (210339J)
- **Perera M.V.D.P.D.S** (210466U)

**Supervisor:** Dr. Sunimal Rathnayake

---

## 1. Introduction

Blockchain technology has emerged as a transformative innovation, attracting attention from academia, industry, and governments alike. It provides a secure, distributed ledger that enables trust among untrusted entities without relying on centralized authorities or intermediaries. Its defining properties, including immutability, transparency, auditability, autonomy, and resilience, position blockchain as one of the most disruptive digital technologies of the past decade.

Initially conceived as the foundation of Bitcoin, blockchain’s applications have rapidly expanded beyond cryptocurrencies. Ethereum introduced programmability through smart contracts, enabling decentralized applications (DApps) that support diverse use cases. Today, blockchain is being deployed in finance, insurance, supply chain management, healthcare, identity management, registry services, stock trading, the Internet of Things (IoT), and energy systems.

Among the challenges brought by the surge in adoption, **scalability** remains the most critical barrier. The inherent limitation in the number of transactions these networks can process per second has prompted efforts within the blockchain community to develop a wide range of innovative solutions.

### 1.1 Motivation

Public blockchains are constrained by the “scalability trilemma”: decentralization, security, and scalability cannot be maximized simultaneously. Ethereum, one of the most prominent public blockchains, exemplifies this trade-off. Ethereum has deliberately optimized for decentralization and security—now under Proof-of-Stake—at the cost of base-layer throughput. In practice, Ethereum L1 capacity remains modest at roughly 15–25 TPS. When demand spikes, finite blockspace leads to congestion and fee volatility.

To scale without compromising Ethereum’s security guarantees, the ecosystem has converged on **Layer-2 (L2) rollups**. Rollups execute transactions off-chain and post succinct data/proofs to Ethereum, thereby inheriting L1 security while reducing L1 computation and bandwidth. Within this family, **ZK-Rollups** offer cryptographic validity proofs that enable faster finality than optimistic rollups.

Yet a gap persists between ZK-Rollups’ theoretical scalability and their observed, end-to-end performance. Prior studies surface key constraints (e.g., proof generation time) but stop short of a comprehensive, reproducible analysis of which tuning levers—batch size/frequency, sequencer scheduling, and data-availability (DA) compression—most effectively shift the throughput–latency–cost frontier in real deployments.

By running controlled, reproducible experiments, we aim to identify the true bottlenecks (e.g., proving) and produce practical tuning guidance for the ecosystem. We will implement a simple, educational proof-of-concept (PoC) ZK-rollup rather than a fully EVM-compatible system. The novel contribution of this work is the creation of an open-source, modular framework specifically designed for controlled, empirical analysis of ZK-Rollup performance trade-offs.

### 1.2 Background

#### 1.2.1 Blockchain Technology and Scalability Challenges

Blockchain technology underpins decentralized systems, enabling secure, transparent, and tamper-resistant transaction processing. However, scalability refers to the ability of a blockchain network to efficiently process increasing volumes of transactions without undermining security and decentralization. Traditional blockchains achieve high security but suffer from low throughput.

#### 1.2.2 Scalability of Ethereum

Ethereum processes approximately 15–25 TPS under typical conditions. Two primary strategies have emerged to address this:

1.  **Base-layer (L1) scaling:** e.g., Sharding (complex).
2.  **Layer-2 (L2) scaling:** Rollups have emerged as the preferred mechanism.
    - **Optimistic Rollups:** Assume validity, fraud proofs.
    - **ZK-Rollups:** Validity proofs, immediate verification.

#### 1.2.3 The Blockchain Trilemma

The blockchain trilemma asserts that no blockchain can fully optimize **security, scalability, and decentralization** simultaneously; systems typically achieve two at the expense of the third.

#### 1.2.4 Cryptographic Primitives

- **Merkle Trees:** Efficient data verification and synchronization.
- **Zero-Knowledge Proofs (ZKPs):** Prove a statement is true without revealing information.
- **zk-SNARKs:** Succinct Non-Interactive Arguments of Knowledge. Efficient, small proofs, often require trusted setup.
- **zk-STARKs:** Scalable Transparent ARguments of Knowledge. No trusted setup, larger proofs, post-quantum secure.

#### 1.2.5 Data Availability

Ensures all necessary data for verifying state transitions is accessible. Mechanisms include On-chain DA (calldata, EIP-4844 blobs) and Off-chain DA (DACs, Celestia).

### 1.3 Problem Statement

Ethereum faces significant scalability limitations (approx. 15–25 TPS), leading to congestion and high fees. ZK-rollups are a promising L2 solution but real-world deployments reveal bottlenecks:

- Computationally intensive proof generation.
- Inefficient sequencer scheduling.
- High Data Availability (DA) costs.

This project addresses these gaps by implementing and empirically evaluating a modular ZK-Rollup prototype on the Ethereum testnet (e.g., Sepolia). We focus on benchmarking baseline metrics and exploring targeted optimizations like dynamic batch sizing, advanced sequencer policies, and DA compression strategies.

### 1.4 Research Questions

- **RQ1:** What is the quantitative impact of batch size on the throughput (TPS), proof generation latency, and per-transaction cost of a ZK-Rollup?
- **RQ2:** How do different sequencer scheduling policies (e.g., FIFO vs. priority fee) affect transaction confirmation times, particularly for high-value transactions under network congestion?
- **RQ3:** What are the cost and throughput implications of using different data availability mechanisms, specifically comparing on-chain calldata with EIP-4844 blobs?

### 1.5 Research Objectives

1.  **Develop a modular ZK-Rollup prototype framework** deployable on the Ethereum testnet.
2.  **Benchmark baseline performance metrics** (throughput, latency, gas costs, storage) against Ethereum L1.
3.  **Investigate throughput improvement strategies** through controlled experiments:
    - Batch size and frequency tuning.
    - Sequencer scheduling policies.
    - Data availability compression techniques (EIP-4844).

### 1.6 Research Outcomes

- A fully functional, modular ZK-Rollup prototype framework.
- Comprehensive benchmarking datasets and scripts.
- Empirical insights from optimization experiments (quantitative trade-off analyses).
- A detailed technical report/thesis.
- Open-source artifacts (codebase, experiment harnesses, notebooks).

---

## 2. Literature Review

### Executive Summary

Ethereum’s limited throughput has driven the rise of L2 solutions, with ZK-Rollups being promising due to validity proofs. Challenges include sequencer centralization, DA costs, and proof generation bottlenecks. Existing systems are too complex for controlled experimentation. This project fills the gap with a minimal, modular prototype for reproducible benchmarking.

### Key Insights

- **Sequencer:** Policies (FCFS, Priority) affect fairness and latency. Batching trade-offs exist between cost and latency.
- **Data Availability:** Evolving from expensive calldata to EIP-4844 blobs and off-chain solutions (DACs, Celestia).
- **Proving:** A major bottleneck. Metrics like simple TPS are inadequate; true scalability depends on transaction complexity.

### Gaps

- Limited end-to-end empirical evaluations.
- Lack of controlled studies on interaction of batch size, scheduling, and DA.
- Insufficient fine-grained cost breakdowns.

---

## 3. Proposed Method

We will design, implement, and benchmark a modular ZK-Rollup framework on an Ethereum testnet.

### 3.1 Prototype Design

**System Architecture Layers:**

1.  **User Layer:** Wallets, Synthetic Workload Generator.
2.  **Off-Chain Components:**
    - **Sequencer:** Transaction pool, Batching engine, Scheduler.
    - **Executor:** State Manager (Sparse Merkle Tree), State Transition Engine.
    - **Prover:** Circuit Backend (Circom+Groth16/Plonk), Proof Generator.
    - **Submitter:** Batch Packager, On-chain Broadcaster, DA Connector.
3.  **On-Chain Components:** `RollupBridge.sol` (State roots, Deposits/Withdrawals), `Verifier.sol` (ZK proof verification).
4.  **Data Availability Layer:** Mode A (Calldata), Mode B (Compressed), Mode C (Off-chain/IPFS).

### 3.2 Experimental Design

**Objectives:**

- Evaluate sequencer parameters (batch size, frequency).
- Assess executor performance.
- Investigate DA strategies.
- Compare proof system backends.

**Variables:**

- **Independent:** Batch size, Batch frequency, Sequencer policy, DA mode, Proof system.
- **Dependent:** Throughput (TPS), Latency, Gas cost, Proof generation time, Verification time.

**Procedure:**

1.  **Setup:** Deploy on Sepolia and Local testnet (Hardhat). Establish L1 baseline.
2.  **Controlled Experiments:** Vary independent variables systematically.
3.  **Data Collection:** Capture metrics for each batch.
4.  **Analysis:** Plot trade-off curves (e.g., throughput vs. latency).

---

## 4. Research Timeline

- **Project Phases:** Initiation, Architecture Design, Implementation, Experiments/Benchmarking, Data Analysis, Documentation.
- **Milestones:** Proposal Submission, Prototype Deployment, Complete Experiments, Final Report.
- **Workload:** Estimated 2,240 hours total (approx. 560 hours per member).

---

## 5. Feasibility and Conclusion

### Feasibility

- **Technical:** Uses established tools (Solidity, Node.js, Circom, EIP-4844). Modular design allows flexibility.
- **Resource:** Feasible with standard computational resources and open-source tools.
- **Time:** 10-month timeline with buffers is realistic.

### Conclusion

This project aims to bridge the gap between theoretical ZK-Rollup scalability and practical performance. By building a modular prototype and conducting rigorous experiments, we will provide actionable insights into optimizing batching, sequencing, and data availability. The open-source artifacts will support future research and education in the blockchain domain.

### Future Work

- Integration of zkEVM for general-purpose smart contracts.
- Decentralized sequencer designs.
- Economic analysis of fee mechanisms.

---

## References

_(Selected references from proposal)_

- [1] A. I. Sanka and R. C. Cheung, “A systematic review of blockchain scalability...”
- [2] S. Nakamoto, “Bitcoin: A peer-to-peer electronic cash system,” 2008.
- [3] V. Buterin, “Ethereum: A next-generation smart contract...”
- [58] S. Chaliasos et al., “Analyzing and benchmarking zk-rollups,” 2024.
- [62] Matter Labs, “ZKsync Docs,” 2025.
- [63] StarkWare Industries, “Starknet Documentation,” 2025.
- [66] Celestia, “How Celestia works,” 2025.
