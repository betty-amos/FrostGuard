# FrostGuard Protocol

**Enterprise-Grade Bitcoin Cold Storage with Multi-Signature Governance on Stacks**

---

## 🔐 Overview

**FrostGuard** is a battle-tested **multi-signature cold storage management protocol** purpose-built for **institutional Bitcoin custody**. Designed for maximum isolation and cryptographic integrity, FrostGuard enforces that all participating keys must undergo strict **cold storage verification** using **challenge-response cryptographic proofs** before being eligible for signing operations.

Built on the **Stacks blockchain**, FrostGuard allows decentralized, programmable custody of BTC via Stacks' Bitcoin Layer integration. Ideal for DAOs, treasuries, exchanges, and high-value custody solutions requiring air-gapped key management and granular signing governance.

---

## 🌐 System Overview

FrostGuard introduces **verifiable cold storage enforcement** by combining:

* **Challenge-Response Cryptographic Verification**: Ensures a key has signed a unique on-chain challenge without ever touching a hot/internet-connected environment.
* **Flexible Multi-Signature Wallets**: Configurable **M-of-N** multisig wallets with only verified cold keys.
* **Secure Transaction Lifecycle**: Transaction proposals require quorum approval from authorized cold signers before execution.
* **Emergency Controls**: Includes **contract-level pause**, **key revocation**, and **challenge expiry** cleanup mechanisms.

---

## 🏗️ Contract Architecture

The FrostGuard protocol is implemented as a **Clarity smart contract**, maintaining security, auditability, and deterministic behavior on-chain.

### Key Modules

| Module                      | Responsibility                                                   |
| --------------------------- | ---------------------------------------------------------------- |
| **Cold Storage Management** | Register, verify, and revoke cold keys via challenge-response    |
| **Challenge Lifecycle**     | Issue unique cryptographic challenges, validate signed responses |
| **Multisig Wallet Manager** | Create wallets with defined signer sets and thresholds           |
| **Transaction Processor**   | Propose, sign, and execute multisig transactions                 |
| **Emergency Ops**           | Pause/unpause the contract and revoke malicious keys             |

---

## 🧱 Data Structures

### 🔑 Cold Storage Keys (`cold-storage-keys`)

* Stores metadata for each registered key (public key, verification status, last verified height).
* Each key is identified by its `hash160` of the public key.

### 🧩 Active Challenges (`active-challenges`)

* Manages pending challenge-response verifications.
* Contains the `challenge-id`, challenge message, linked key, and block height.

### 🧾 Wallet Signers (`wallet-signers`)

* Represents a multisig wallet: list of signer key hashes, signing threshold, nonce, and creator.

### 📝 Pending Transactions (`pending-transactions`)

* Tracks multisig transaction proposals: target wallet, amount, recipient, signatures collected, and execution status.

---

## 🔄 Core Data Flow

### 1. **Key Registration & Verification**

```text
[Cold Signer Owner] → register-cold-key(pubkey) → [FrostGuard stores unverified key]
                   → create-cold-key-challenge(hash) → [Contract emits challenge]
                   → (offline) Sign challenge → verify-cold-key-challenge(challenge-id, signature)
                   → [Key marked as verified]
```

### 2. **Wallet Creation**

```text
[Admin] → create-multisig-wallet([verified_key_hashes], threshold)
        → [New wallet created with verified signers only]
```

### 3. **Transaction Lifecycle**

```text
[User] → propose-transaction(wallet-id, recipient, amount)
       → [Transaction stored in pending state]

[Cold Signers] → sign-transaction(tx-id, signer-hash, signature)
               → [Signature added if valid and unique]

[Anyone] → execute-transaction(tx-id)
         → [Executed only if required threshold is met]
```

### 4. **Key Revocation & Emergency Controls**

```text
[Owner] → pause-contract / unpause-contract → Toggle contract activity
        → revoke-cold-key(hash) → Remove a compromised/malicious key
```

---

## 🛠️ Key Features

* ✅ **Cold Storage Verification**: Ensures air-gapped key integrity before use
* 🔐 **Bitcoin-Compatible Multisig**: Support for `hash160(pubkey)` format, compatible with Bitcoin wallets
* 🧱 **Configurable M-of-N**: Fine-grained control over signer thresholds
* ⏱️ **Challenge Expiry**: Prevents reuse or replay of stale challenges
* 🚨 **Emergency Pausing & Revocation**: Rapid response to compromised keys or governance failure
* 📊 **On-chain Auditable State**: All actions, keys, and transactions are stored transparently

---

## 🔧 Constants

| Constant           | Value                 | Description                           |
| ------------------ | --------------------- | ------------------------------------- |
| `MAX-SIGNERS`      | `10`                  | Max number of signers per wallet      |
| `MIN-THRESHOLD`    | `2`                   | Minimum number of signatures required |
| `CHALLENGE-EXPIRY` | `144` blocks (~24h)   | Time before a challenge expires       |
| `CONTRACT-OWNER`   | `tx-sender` at deploy | Admin who can pause/revoke            |

---

## 📘 Example Usage

### Register and Verify a Cold Key

```lisp
(register-cold-key 0x02abc...xyz) ;; Returns key hash
(create-cold-key-challenge key-hash) ;; Returns challenge message
;; (Offline) Sign challenge with private key
(verify-cold-key-challenge challenge-id signature)
```

### Create a Multisig Wallet

```lisp
(create-multisig-wallet (list key-hash1 key-hash2 key-hash3) u2)
```

### Propose and Execute Transaction

```lisp
(propose-transaction wallet-id 'ST123...abc u1000000)
(sign-transaction tx-id key-hash signature)
(execute-transaction tx-id)
```

---

## 🔒 Security Considerations

* **Signature Verification** is abstracted (to be integrated with `secp256k1-verify` in practice).
* **Only Verified Cold Keys** may interact with signing functions.
* **Contract Pausing** allows emergency halting of all protocol activity.
* **Challenge Expiry Enforcement** prevents challenge reuse or delayed attacks.
* **All State Mutations** are access-controlled and error-guarded.

---

## 🧪 Future Improvements

* 🔍 **Bitcoin Signature Integration**: Use native `secp256k1` verification
* 🕸️ **P2SH/BIP-32 Compatibility**: Interop with Bitcoin multi-sig standards
* 📈 **Governance Extensions**: DAO-controlled wallet upgrades and signers
* 📬 **Off-chain Challenge Delivery APIs**: Encrypted challenge distribution systems

---

## 📄 License

MIT © 2025 FrostGuard Protocol Contributors

---

## ✍️ Author

**Lead Developer**: [Stacks Clarity Senior Engineer]
FrostGuard is maintained by a group of core Stacks contributors focused on secure custody systems.
