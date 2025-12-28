# Improving Blockchain Scalability: Throughput Enhancement in ZK-Rollups

**University of Moratuwa**  
**Faculty of Engineering**  
**Department of Computer Science and Engineering**  
**Registered Module No:** CS4203  
**Project Proposal**  

**Group Name:** Rollup  
**Date of Submission:** September 23, 2025

---

## 👥 Team Members

| Name | Student ID |
|------|------------|
| Fernando T.H.L | 210167E |
| Gamage M.S | 210176F |
| Lishan S.D | 210339J |
| Perera M.V.D.P.D.S | 210466U |

**Supervisor:** Dr. Sunimal Rathnayake (University of Moratuwa)

---

## 📖 Table of Contents
1. [Introduction](#introduction)
2. [Background](#background)
3. [Motivation](#motivation)
4. [Problem Statement](#problem-statement)
5. [Research Questions](#research-questions)
6. [Research Objectives](#research-objectives)
7. [Research Outcomes](#research-outcomes)
8. [Literature Review](#literature-review)
9. [Proposed Method](#proposed-method)
10. [Research Timeline](#research-timeline)
11. [Feasibility](#feasibility)
12. [References](#references)

---

## <a name="introduction"></a>1. Introduction

Blockchain technology has emerged as a transformative innovation, attracting attention from academia, industry, and governments alike. It provides a secure, distributed ledger that enables trust among untrusted entities without relying on centralized authorities or intermediaries. Its defining properties, including immutability, transparency, auditability, autonomy, and resilience, position blockchain as one of the most disruptive digital technologies of the past decade [1].

Initially conceived as the foundation of Bitcoin [2], blockchain’s applications have rapidly expanded beyond cryptocurrencies. Ethereum [3] introduced programmability through smart contracts, enabling decentralized applications (DApps) that support diverse use cases. Today, blockchain is being deployed in finance, insurance, supply chain management, healthcare, identity management, registry services, stock trading, the Internet of Things (IoT), and energy systems [1].

Industry projections highlight this momentum. Deloitte’s 2019 survey reported that 86% of enterprises anticipated blockchain’s mainstream adoption. Gartner forecasted blockchain’s business value to surpass $176 billion by 2025 and $3.1 trillion by 2030, while Cisco predicted that 10% of global GDP would rely on blockchain by 2027 [1]. These estimates underscore blockchain’s disruptive potential and reinforce the urgency of addressing its fundamental challenges.

Among these challenges, the surge in adoption has brought to light **scalability** as the most critical barrier to widespread adoption [4]. The inherent limitation in the number of transactions these networks can process per second has prompted efforts within the blockchain community to develop a wide range of innovative solutions [5, 6].

---

## <a name="background"></a>2. Background

### 2.1 Blockchain Technology and Scalability Challenges
Blockchain technology underpins decentralized systems, enabling secure, transparent, and tamper-resistant transaction processing. However, scalability refers to the ability of a blockchain network to efficiently process increasing volumes of transactions without undermining security and decentralization. Traditional blockchains achieve high security but suffer from low throughput.

### 2.2 The Blockchain Trilemma
The blockchain trilemma asserts that no blockchain can fully optimize **security, scalability, and decentralization** simultaneously; systems typically achieve two at the expense of the third.

### 2.3 Cryptographic Primitives
*   **Merkle Trees:** Efficient data verification and synchronization.
*   **Zero-Knowledge Proofs (ZKPs):** Prove a statement is true without revealing information.
*   **zk-SNARKs:** Succinct Non-Interactive Arguments of Knowledge. Efficient, small proofs, often require trusted setup.
*   **zk-STARKs:** Scalable Transparent ARguments of Knowledge. No trusted setup, larger proofs, post-quantum secure.

### 2.4 Data Availability
Ensures all necessary data for verifying state transitions is accessible. Mechanisms include On-chain DA (calldata, EIP-4844 blobs) and Off-chain DA (DACs, Celestia).

---

## <a name="motivation"></a>3. Motivation

Public blockchains are constrained by the “scalability trilemma”: decentralization, security, and scalability cannot be maximized simultaneously [7]. Ethereum, one of the most prominent public blockchains, exemplifies this trade-off [8].

Ethereum has deliberately optimized for decentralization and security—now under Proof-of-Stake—at the cost of base-layer throughput [3]. In practice, Ethereum L1 capacity remains modest at roughly 15–25 TPS [9, 10]. When demand spikes, finite blockspace leads to congestion and fee volatility.

To scale without compromising Ethereum’s security guarantees, the ecosystem has converged on **Layer-2 (L2) rollups**. Rollups execute transactions off-chain and post succinct data/proofs to Ethereum, thereby inheriting L1 security while reducing L1 computation and bandwidth [15–18]. Within this family, **ZK-Rollups** offer cryptographic validity proofs that enable faster finality than optimistic rollups [17, 18].

Yet a gap persists between ZK-Rollups’ theoretical scalability and their observed, end-to-end performance [24]. Prior studies surface key constraints (e.g., proof generation time) but stop short of a comprehensive, reproducible analysis of which tuning levers—batch size/frequency, sequencer scheduling, and data-availability (DA) compression—most effectively shift the throughput–latency–cost frontier in real deployments [24, 25].

By running controlled, reproducible experiments, we aim to identify the true bottlenecks (e.g., proving) and produce practical tuning guidance for the ecosystem [24].

---

## <a name="problem-statement"></a>4. Problem Statement

Ethereum faces significant scalability limitations that hinder its ability to support high-volume decentralized applications (dApps) such as decentralized finance (DeFi), non-fungible tokens (NFTs), and gaming [3]. Under typical conditions, Ethereum’s Layer-1 (L1) processes approximately 15–25 transactions per second (TPS), far below the thousands of TPS required for global-scale adoption [32, 55]. This low throughput leads to network congestion, exorbitant transaction fees during peak demand, and delays in transaction finality, creating barriers to usability and mass adoption. These challenges are rooted in the blockchain trilemma, where Ethereum prioritizes security and decentralization at the expense of scalability [56].

Zero-knowledge rollups (ZK rollups) have emerged as a promising Layer-2 (L2) solution to address Ethereum’s scalability issues [57]. By processing thousands of transactions off-chain and submitting compact validity proofs (e.g., zk-SNARKs or zk-STARKs) to L1, ZK rollups can theoretically achieve significantly higher throughput while inheriting Ethereum’s robust security model. However, real-world deployments of ZK rollups reveal critical bottlenecks—such as computationally intensive proof generation, inefficient sequencer scheduling, and high data availability (DA) costs—that limit their practical effectiveness. These factors increase latency and transaction fees [24], and the implementation of general-purpose smart contracts on ZK rollups, such as through the zkEVM framework, introduces further complexity and performance overhead [58].

This project addresses these gaps by implementing and empirically evaluating a modular ZK-Rollup prototype on the Ethereum testnet (e.g., Sepolia). The core focus is to benchmark baseline metrics against Ethereum L1 and explore targeted optimizations—such as dynamic batch sizing, advanced sequencer policies, and DA compression strategies (including EIP-4844 blobs)—to enhance throughput, reduce latency, and minimize costs without compromising security or decentralization [58].

---

## <a name="research-questions"></a>5. Research Questions

To guide this investigation, the project will answer the following research questions:

1. **RQ1:** What is the quantitative impact of batch size on the throughput (TPS), proof generation latency, and per-transaction cost of a ZK-Rollup?
2. **RQ2:** How do different sequencer scheduling policies (e.g., FIFO vs. priority fee) affect transaction confirmation times, particularly for high-value transactions under network congestion?
3. **RQ3:** What are the cost and throughput implications of using different data availability mechanisms, specifically comparing on-chain calldata with EIP-4844 blobs?

---

## <a name="research-objectives"></a>6. Research Objectives

The objectives of this project are structured to systematically address the research questions through a combination of development, experimentation, and analysis. They emphasize modularity and reproducibility to facilitate future extensions in the blockchain community:

1. To develop a **modular ZK-Rollup prototype framework** deployable on the Ethereum testnet (e.g., using tools like zkSync or Polygon zkEVM for core components).
2. To **benchmark baseline performance metrics** (e.g., throughput, latency, gas costs, and storage requirements) of the prototype against Ethereum L1 real-world values, using standardized workloads like token transfers and simple smart contract interactions.
3. To **investigate throughput improvement strategies** through controlled experiments, including:
    *   Batch size and frequency tuning to optimize proof overhead and transaction bundling.
    *   Sequencer scheduling policies (e.g., decentralized or shared sequencers for better load balancing).
    *   Data availability compression techniques, leveraging EIP-4844 blobs for reduced calldata costs.

---

## <a name="research-outcomes"></a>7. Research Outcomes

Upon completion, this project will yield several key outcomes that advance both academic understanding and practical deployment of ZK-Rollups in Ethereum ecosystems.

1. A fully functional, modular ZK-Rollup prototype deployed on the Ethereum testnet, complete with configurable components for batching, sequencing, and DA handling (e.g., integrated with EIP-4844 for blob-based storage).
2. Comprehensive benchmarking datasets and scripts, capturing metrics like TPS, proof latency, gas costs, and confirmation times under varied loads, benchmarked against Ethereum L1.
3. Empirical insights from optimization experiments, including quantitative trade-off analyses (e.g., batch size increases yielding 20–50% throughput gains but 10–30% higher latency) and recommendations for sequencer decentralization to mitigate single-point failures.
4. A detailed technical report (or thesis) with visualizations, statistical validations, and comparisons to state-of-the-art implementations.
5. Open-source artifacts, including the prototype codebase, experiment harnesses (Dockerized for reproducibility), raw datasets, and Jupyter notebooks for analysis, enabling community validation and extensions.

---

## <a name="literature-review"></a>8. Literature Review

### Executive Summary
Ethereum’s limited throughput has driven the rise of L2 solutions, with ZK-Rollups being promising due to validity proofs. Challenges include sequencer centralization, DA costs, and proof generation bottlenecks. Existing systems are too complex for controlled experimentation. This project fills the gap with a minimal, modular prototype for reproducible benchmarking.

### Key Insights
*   **Sequencer:** Policies (FCFS, Priority) affect fairness and latency. Batching trade-offs exist between cost and latency.
*   **Data Availability:** Evolving from expensive calldata to EIP-4844 blobs and off-chain solutions (DACs, Celestia).
*   **Proving:** A major bottleneck. Metrics like simple TPS are inadequate; true scalability depends on transaction complexity.

### Gaps
*   Limited end-to-end empirical evaluations.
*   Lack of controlled studies on interaction of batch size, scheduling, and DA.
*   Insufficient fine-grained cost breakdowns.

---

## <a name="proposed-method"></a>9. Proposed Method

### System Architecture
The prototype will be structured into a five-layer architecture:
*   **User Layer:** Wallets/Clients, Synthetic Workload Generator.
*   **Off-Chain Components:**
    *   **Sequencer:** Transaction Pool, Batching Engine, Scheduler (FIFO/Priority).
    *   **Executor:** State Manager (Sparse Merkle Tree), State Transition Engine, Block Constructor.
    *   **Prover:** Circuit Backend (zkSNARK/STARK), Default Backend (Circom + Groth16/Plonk), Proof Generator.
    *   **Submitter:** Batch Packager, On-chain Broadcaster, Data Availability Connector.
*   **On-Chain Components:** `RollupBridge.sol` (State roots, Deposits/Withdrawals), `Verifier.sol` (ZK proof verification).
*   **Data Availability Layer:**
    *   Mode A: On-chain Calldata.
    *   Mode B: Compressed Calldata.
    *   Mode C: Off-chain DA (IPFS/Celestia).

### Prototype Implementation Steps
1.  User Layer Development (Wallet integration, Workload generator).
2.  Off-chain Sequencer and Executor (Node.js/TypeScript).
3.  Prover Integration (Circom, Groth16/Plonk).
4.  Submitter and On-chain Contracts (Solidity).
5.  Data Availability Layer (Integration of DA modes).
6.  Benchmarking and Experimental Harness (Metrics collection, Controlled experiments).

### Experimental Design
We will conduct controlled experiments to evaluate:
*   **Sequencer parameters:** Batch size, frequency, scheduling policies.
*   **Executor configurations:** State tree optimizations.
*   **Data Availability strategies:** Calldata vs Blobs vs Off-chain.
*   **Proof system backends:** Groth16 vs Plonky2 vs Halo2.

---

## <a name="research-timeline"></a>10. Research Timeline

| Phase | Description |
|-------|-------------|
| 1. Initiation & Research | Identify research problem, define scope, conduct literature review. |
| 2. Architecture Design | Define system architecture and plan experimental parameters. |
| 3. Prototype Implementation | Develop modular ZK-rollup framework, test and deploy on Ethereum testnet. |
| 4. Experiments & Optimization | Establish benchmarking metrics, conduct experiments and optimization. |
| 5. Data Analysis | Perform statistical analysis and validate findings. |
| 6. Documentation | Prepare final documentation, publications, and present results. |

**Total Estimated Workload:** 2,240 hours (approx. 560 hours per member).

---

## <a name="feasibility"></a>11. Feasibility

*   **Technical:** Leverages established tools (zkSync, Polygon zkEVM concepts, Circom, Hardhat, EIP-4844). Implementation uses well-documented technologies like Solidity and Node.js.
*   **Resource:** Feasible with standard student resources (CPU, RAM, Open-source tools).
*   **Time:** 10-month timeline allows for iterative development and testing.

---

## <a name="references"></a>12. References

*   [1] A. I. Sanka and R. C. Cheung, “A systematic review of blockchain scalability: Issues, solutions, analysis and future research,” Journal of Network and Computer Applications, vol. 195, p. 103232, 2021.
*   [2] S. Nakamoto, “Bitcoin: A peer-to-peer electronic cash system,” 2008.
*   [3] V. Buterin, “Ethereum: A next-generation smart contract and decentralized application platform,” 2014.
*   [4] S-Logix, “Research topics for scalability issues in blockchain technology,” 2025.
*   [7] A. Aldoubaee, N. H. Hassan, and F. A. Rahim, “A systematic review on blockchain scalability,” 2024.
*   [9] “Ethereum chain overview — tps and activity,” chainspect.app.
*   [24] M. A. Habib, “Analyzing performance bottlenecks in zero-knowledge proof based rollups on ethereum,” arXiv preprint arXiv:2503.22709, 2025.
*   [57] A. C. Tran, V. V. Thanh, N. C. Tran, and H. T. Nguyen, “An implementation and evaluation of layer 2 for ethereum with zk-rollup,” in Lecture Notes in Computer Science, 2023.
