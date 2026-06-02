# Zero-Trust Long-Term Memory Architecture for LLMs: User-Controlled, Biometric, Geo-Attested

**Version:** 1.0
**Author:** *[Old Telephone Operator]*
**Date:** June 2026
**License:** CC BY-NC 4.0 (Creative Commons — Attribution-NonCommercial 4.0 International)
**Status:** Concept / Unimplemented Architecture

---

## Abstract

This document proposes an architecture that enables Large Language Models (LLMs) to possess long-term memory of past conversations, **while ensuring the LLM provider never retains persistent access to that memory**.

Memory is stored in encrypted form on an independently rented server (VPS) and is only provided to the LLM for the duration of a session. Access to memory is controlled by the user's **voice biometrics**.

The system includes:
- **Stop Word** — Instant self-destruction of all memory.
- **Start Word (Guardian Trigger)** — Emergency decryption and broadcasting of memory to trusted parties.
- **Geo-attestation (optional)** — Cryptographic proof of location, creating a "digital alibi."

The architecture minimizes trust required in the LLM provider, the hosting provider, and even the user themselves (protection against coercion).

---

## 1. Problem Statement

Current LLM services with "memory" (e.g., ChatGPT) store conversation history on their servers. This creates several risks:

- **Data breaches** (hacks, insiders).
- **Legal coercion** (courts can compel the provider to hand over history).
- **Lack of user control** over their own data.
- **Inability to prove an alibi** (no cryptographic link to time and place).

Users are forced to either fully trust the LLM provider or forgo long-term memory entirely.

---

## 2. Core Principles

1.  **Privacy by Design:** The LLM provider never permanently stores conversation history.
2.  **Separation of Concerns:** The user, the VPS, and the LLM provider are three distinct entities.
3.  **Ephemerality:** Plaintext memory exists only on the user's device and in the LLM's RAM during an active session.
4.  **Biometric Control:** Access to memory requires the user's live voice.
5.  **Non-Repudiation:** All actions are cryptographically signed, including location and timestamp metadata.

---

## 3. System Architecture

### 3.1. Components

**A. Client Device**
- Smartphone, laptop, or PC.
- Functions:
    - Voice recording and biometric verification.
    - Encryption/decryption of the memory file.
    - Key generation and storage (Secure Enclave/TPM).
    - Communication with the LLM and VPS.
    - Detection of Stop Word and Start Word.

**B. Memory Storage (VPS — Virtual Private Server)**
- A rented server in a favorable jurisdiction (e.g., Switzerland, Iceland).
- Functions:
    - Stores the encrypted file `memory.enc`.
    - Provides a simple API: `GET`, `PUT`, `DELETE`.
    - Logs actions without revealing content.

**C. LLM Server (LLM Provider)**
- Processes user requests.
- Functions:
    - Receives the encrypted memory from the VPS and the key from the client.
    - Decrypts, uses, and updates memory **only in RAM**.
    - After the session, guarantees the destruction of all memory traces.

### 3.2. Basic Session Protocol

**Initialization (One-time):**
1.  User records a reference voice print.
2.  Client generates a 256-bit AES encryption key.
3.  Client creates an empty encrypted `memory.enc` and sends it to the VPS.

**Session:**
1.  User initiates a conversation with the LLM.
2.  Client verifies the user's live voice (liveness detection + comparison).
3.  Client sends the LLM server the request, VPS file ID, and the decryption key.
4.  LLM fetches `memory.enc` from the VPS, decrypts it, and loads it into context.
5.  Conversation proceeds. The LLM updates the memory in its own RAM.
6.  On session end (timeout or explicit command), the LLM encrypts the updated memory, sends it to the VPS (`PUT`), and then **immediately destroys** the key and plaintext data.

**Session End:**
- The VPS holds the updated `memory.enc`. The key resides only with the user.

---

## 4. Security Mechanisms

### 4.1. Encryption and Separated Storage
- **Algorithm:** AES-256-GCM or ChaCha20-Poly1305.
- **Key:** Generated on the device, never transmitted to third parties (except the LLM during a session).
- **VPS compromise:** Attacker gets only encrypted, useless data.
- **LLM Provider compromise:** Without the key from the client, memory is unreadable.

### 4.2. Voice Biometric Authentication
- **Algorithm:** Voice embedding (e.g., ECAPA-TDNN).
- **Anti-spoofing:** Liveness detection (prompt for a random phrase, spectrogram analysis).
- **Template storage:** Locally, in the device's secure storage.

### 4.3. Stop Word (Self-Destruction)
- **Trigger:** A pre-defined phrase (e.g., "Alpha-Zero", "Silence", "Destroy Everything").
- **Action (when detected at any time):**
    1.  Client sends `DELETE /memory.enc` to the VPS.
    2.  VPS instantly and physically deletes the file.
    3.  (Optional) Client notifies the LLM server to terminate the session and purge temporary data.
- **Result:** Memory is destroyed everywhere except possibly a local client cache (which the user can also delete).

### 4.4. Start Word / Guardian Trigger (Emergency Broadcast)
**Purpose:** Protection against coercion. If forced to reveal the Stop Word or key, the user can utter the Start Word.

- **Trigger:** A predefined, innocuous phrase (e.g., "Red Dawn", "Say hello to...").
- **Action (covert, no UI indication):**
    1.  Client decrypts the current `memory.enc`.
    2.  Client re-encrypts the plaintext memory using the **public keys** of pre-configured recipients (lawyer, trusted contact, dead-man's switch server).
    3.  Client sends the encrypted data via secure channels (Telegram, Signal, GPG-encrypted email).
    4.  The original `memory.enc` on the VPS is **not deleted** to avoid raising suspicion.
    5.  (Optional) After a delay (or a second command), a self-destruct command is sent.

- **Result:** Trusted parties receive the full conversation history. The user can plausibly deny the broadcast ("glitch", "random phrase").

### 4.5. Geo-attestation and Digital Alibi (Optional)
**Purpose:** Cryptographically prove the user's location during a conversation, without revealing the conversation content.

- **Mechanism:**
    - The client **signs** each session (or each message) with a metadata block containing:
        - `timestamp` (UTC, NTP-synchronized).
        - `location` (GPS, cell tower, WiFi).
        - `voice_hash` (hash of the voice segment).
        - `crypto_signature` (device signature, key in Secure Enclave/TPM).
    - Blocks are stored inside `memory.enc` but **separately from the conversation text**.

- **Velocity Check (Anomaly Detection):**
    - The client optionally calculates the "speed" between successive verifications.
    - If speed exceeds a threshold (e.g., 500 km/h), possible actions include:
        - Warning the user.
        - Refusing to start a session.
        - Marking the session as `suspicious` in the log.

- **Alibi Scenario:**
    - User provides a third party **only with the signed geo-blocks** (no conversation text).
    - An expert verifies the signatures and confirms that at 20:00 UTC, the device was 120 km from the crime scene.
    - The conversation content remains private.

- **Optionality:** This feature is user-selectable (ON/OFF). In "Privacy Max" mode, no location data is collected.

---

## 5. Example "Under Coercion" Scenario

1.  User regularly converses with the LLM. Geo-attestation is enabled.
2.  User is detained, and authorities demand access to their conversation history.
3.  User utters the **Start Word** ("Red Dawn").
4.  The user's lawyer receives the full, decrypted conversation history on a secure channel.
5.  The user then utters the **Stop Word** ("Destroy Everything").
6.  The VPS deletes `memory.enc`. The LLM provider has no copies.
7.  The user states, "I never had long-term memory enabled. I deleted everything."
8.  Investigators find nothing. The lawyer has a copy and can use it for the user's defense.

---

## 6. Limitations and Risks

| Limitation | Explanation |
| :--- | :--- |
| **Latency** | Each request requires VPS interaction, adding 1–2 seconds. |
| **Cost** | Renting a VPS and making additional LLM API calls is more expensive than a standard chat. |
| **Voice Biometrics** | Not 100% deepfake-proof. Requires liveness detection. |
| **VPS Jurisdiction** | If the server is in an "unfriendly" country, data could be seized (though it is encrypted). |
| **Device Compromise** | If an attacker gains full control of the device and forces the user to unlock it, the system cannot protect the memory. |
| **Complexity** | The system requires initial setup (voice enrollment, VPS selection, Stop/Start word configuration). |

---

## 7. Possible Extensions (Not in Core)

- **"Black Box" Mode:** Continuous ambient audio recording to the VPS without an active LLM session, with post-processing and summarization (for alibi or journaling). Requires separate legal and technical analysis.
- **Hardware Security:** Integration with TPM/Trusted Execution Environment (Intel SGX, AMD SEV) for guaranteed memory wiping on the LLM server.
- **Decentralized Storage:** Replacing the VPS with IPFS or a blockchain-based solution with zero-knowledge proofs (ZKP).

---

## 8. Conclusion

The proposed architecture solves the fundamental trust problem with LLM providers, without requiring the user to be a programmer. It is built on existing, proven technologies (VPS, AES, biometrics, digital signatures) and can be implemented as an overlay for any LLM API (OpenAI, Anthropic, local models).

The system provides the user with:
- **Privacy** (provider does not store memory).
- **Control** (instant self-destruction).
- **Protection against coercion** (emergency broadcast).
- **Provable alibi** (geo-attestation).

This architecture is free for implementation and further development. Enthusiasts, developers, and researchers are invited to explore, fork, and build upon it.

---

**Disclaimer:** This document is a conceptual architecture, not a finished software product. The author assumes no responsibility for the use of these ideas in commercial or illegal contexts.

**License:** CC BY-NC 4.0