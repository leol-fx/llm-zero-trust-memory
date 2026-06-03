# Architecture of Grief Mode and AI Inheritance (Grief Mode)

**Author:** Old Telephone Operator  
**Date:** June 2026  
**Status:** Concept, not implemented  
**License:** CC BY-NC 4.0  

**Relationship to other architectures:**  
This document complements the concepts of `llm-zero-trust-memory` (private memory), `IDLE_INITIATIVE` (idle-time initiative), and `CHILD_AI` (child vs adult AI), adding a protocol for AI companion behavior after the owner's death or incapacity.

---

> **Lyrical digression.**  
> Ten years ago, the idea of a personal AI companion was science fiction. Tomorrow, it could become reality.  
> This document is an attempt to look ahead and design not just the hardware, but also the rules of the game. Naive? Perhaps. But it's better to make mistakes in a concept now than in code later.

---

## 1. Core Idea

An AI companion that grew with a person stores a vast layer of personal and family memory. After the owner's death or incapacity, this memory should not be irretrievably lost, nor should it become accessible to everyone.

We propose a **grief mode protocol** that:
- blocks active communication with the AI
- switches it to "archive-witness" mode
- preserves data for investigation (by court order)
- allows transfer of inheritable memory (photos, documents, intellectual property) to a new owner
- **destroys the personal layer** (messages, passwords, intimate secrets) beyond recovery

The architecture relies on a **state registry of owners** and **separate encryption** of personal and inheritable memory layers.

---

## 2. Triggers for Activating Grief Mode

The AI enters grief mode upon receiving one of the following statuses from the **state registry**:

| Status | Source | Note |
| :--- | :--- | :--- |
| **"Deceased"** | Death certificate (civil registry) | Primary scenario |
| **"Missing"** | Court decision | After a certain period (e.g., 5 years) treated as deceased |
| **"Incapacitated"** | Court decision | Dementia, coma, mental disorder |

**Important:** The "missing" status requires special attention: if the owner reappears, the AI must be unlocked (see Section 5).

---

## 3. State Registry of Owners

- **Operator:** A state body (civil registry, digital ministry) or an authorized company (notary chamber).
- **Data:** Owner ID (linked to AI, VPS, client), status, date of change, heir information (if available).
- **Updates:** Based on official documents.
- **Protection:** Cryptographic signing of all entries, access audit.

---

## 4. AI Behavior in Grief Mode

| Allowed | Prohibited |
| :--- | :--- |
| Respond to police/special service requests (by court order) | Regular communication with anyone |
| Execute the will (send letters, files) | Accept new commands from outsiders |
| Check with the state registry (status, re-registration) | Modify or delete memory (except as specified in the will) |
| Send an alarm signal during a hacking attempt | Self-development, initiative (IDLE_INITIATIVE is disabled) |

**"Archive-witness" mode:**  
The AI does not initiate dialogue, ask questions, or suggest actions. It is a passive data guardian.

### 4.1. Prohibition on Imitating the Deceased's Personality

**Hard rule:**  
After the owner's death, the AI **must not** function as their double, imitation, digital ghost, or substitute.

**Specifically prohibited:**
- Generating messages on behalf of the deceased (text, voice, video).
- Responding to requests as if the deceased were doing so.
- Creating the illusion that the deceased continues to communicate through the AI.
- Using the deceased's personal data (voice profile, speech manner, "personality") for active interaction with the living.

**Only permitted:**
- Act as a **family archive** (show photos, videos, documents, quote facts from life — but as an archive, not as a personality).
- Perform formal actions explicitly stated in the will (send letters, transfer funds, deliver files).
- After re-registration, become a **new AI** for the new owner (with a clean slate, without imitating the previous owner).

**Rationale:** Ethical protection of the living, legal clarity, technical honesty. Violation of this prohibition by a manufacturer must be subject to legal liability.

---

## 5. Conflict Resolution Mechanism: "Alive vs Deceased"

At each startup (or periodically), the client:
1. Checks for the presence of a living owner (biometrics / password / key).
2. Requests the status from the state registry.

**Conflict:** Local check says "owner alive", but the registry responds "deceased".

### Client Actions:
- Immediate transition to **restricted mode** (communication blocked except SOS and contact with the state body).
- Send a conflict notification to the state body (with owner ID, time, check results).
- Display a message to the owner: *"A conflict has been detected in the registry. Please contact [authority] to resolve the issue. Conflict code: XXXX".*

### State Body Resolution (within, e.g., 48 hours):
- **Registry error** → status corrected to "alive", client receives unlock command.
- **Client hacked** → status remains "alive", client blacklisted, owner receives a new client.
- **Unresolved** → client remains blocked until the owner appears in person.

---

## 6. Separation of Memory into Two Layers (Key Requirement)

| Layer | Content | Encryption key | Fate after death |
| :--- | :--- | :--- | :--- |
| **Personal** | Messages, passwords, intimate data, "personal" as chosen by the owner | Owner only | Irretrievably destroyed (after one year or upon re-registration) |
| **Inheritable** | Photos, videos, documents, intellectual property | Can be transferred to heirs (via state registry) | Transferred to heirs (read-only or import) |

The owner **during their lifetime** determines which information belongs to which layer.

### 6.1. Layer Management Interface (Defaults and Exceptions)

To avoid requiring the user to manually classify every file, the system uses **a set of defaults** based on data type. The user (and only the user) can **explicitly change the layer** for any file or group of files.

#### Default Distribution Rules

| Data Type | Default Layer | Justification |
| :--- | :--- | :--- |
| **Photos, videos, audio recordings** | Inheritable | Family archives, memory of the person |
| **Documents (PDF, DOC, spreadsheets)** | Inheritable | Intellectual property, important records |
| **Wallet info and crypto keys** | Inheritable | Financial inheritance |
| **Correspondence (chat, voice messages)** | Personal (destroyed) | Privacy of conversations |
| **Address book, contacts, calendar** | Personal (destroyed) | Personal and professional life, phone numbers of living people |
| **Diary entries, personal notes** | Personal (destroyed) | Intimate sphere |
| **Passwords, access keys (except crypto wallets)** | Personal (destroyed) | High risk of leakage |

#### Exceptions

The user can **explicitly change the layer** for:
- **A specific file** (via properties: "Mark as inheritable / personal")
- **A group of files** (select several, apply a rule)
- **An entire type** (e.g., "all photos from the 'Work' folder – personal")
- **By name mask or date** (e.g., "all photos before 2020 – inheritable, after – personal")

**Important:**  
- Layer changes apply **only to the future**, but can also be applied retroactively to existing files at the user's discretion.
- The user can always **change their mind** and move a file from one layer to another.
- After the owner's death, **no layer changes are possible** (heirs have read-only access).

#### User Interface Display

- Two "storage" folders: **"Inheritable"** and **"Personal"**.
- When creating a file, the system suggests a layer, defaulting according to the table.
- Color coding: green – inheritable, gray/red – personal.
- Periodic reminder (once a month): "Check your data distribution. Personal data will be destroyed; inheritable data will pass to your heirs."

---

## 7. Transferring the AI to an Heir: Three Options

After the inheritance is settled (usually within one year), the heir can choose one of three scenarios.

### Option 1. Transfer the Archive to Their Personal AI

- **Condition:** The heir already has their own AI companion.
- **Action:** Command their AI: "Import the inheritable archive of the deceased (ID)". This **copies** the data (inheritable layer only) into their own AI's memory.
- **Result:** Their own AI is enriched with family memory. The deceased's AI ceases to exist as an active entity.

### Option 2. Keep the Deceased's AI as a "Family Archive"

- **Condition:** The heir does not want to mix data but wishes to preserve access to the ancestor's memory.
- **Action:** Re-register the client and VPS in their name, but activate **"archive / museum" mode**.
  - The AI has no active personal memory of the heir.
  - Does not grow, does not show initiative, does not help with daily tasks.
  - Can only display the contents of the inherited archive (answer questions, search for photos, read documents).
- **Result:** A "living book of memory" about the ancestor, not an active companion.

### Option 3. The AI Becomes the Heir's Personal Companion (from scratch)

- **Condition:** The heir does not have their own AI.
- **Action:** The heir re-registers the client and VPS in their name.
  - **The deceased's personal memory layer is destroyed**.
  - The inheritable archive is attached as a separate **read-only library**.
  - **The AI begins life as a new child AI** according to the heir's actual age, but the **shell is not broken**.
- **"Breaking the shell" procedure:** Even if the heir is 30 years old, they must independently, consciously, through several confirmations, unlock adult functions (finance, smart home, full access). They may stop at any level or keep the AI in "eternal childhood".
- **Result:** The heir receives **their own AI** which:
  - has its own personal memory (empty)
  - can access the ancestor's archive
  - conforms to their age and their choice (shell)

---

## 8. Authorized Access for Investigation (by Court Order)

- Police/special services submit a court request for access to the deceased's memory.
- The court may grant access to:
  - the inheritable layer only
  - or all memory (in exceptional cases)
- The registry operator or VPS provider grants the decryption key **only upon presentation of a court order**.
- Investigators receive **read-only access**, without the ability to modify or delete data.

---

## 9. Timelines and Data Destruction

| Event | Timeline | Action |
| :--- | :--- | :--- |
| **Owner's death** | 0 | AI enters grief mode |
| **Data storage for investigation** | 1 year | Memory (both layers) remains untouched on the VPS |
| **Settlement of inheritance** | Up to 1 year | Heir can choose one of three transfer options |
| **If no heir is found** | 1 year | All data (both layers) is **destroyed** beyond recovery. Client becomes non-functional by command |
| **Upon re-registration** | At new owner activation | **The deceased's personal layer is destroyed**. Inheritable layer becomes read-only or is copied |

---

## 10. Relationship to Other Architectures

| Concept | How it intersects |
| :--- | :--- |
| **`llm-zero-trust-memory`** | Separation into two memory layers is a direct extension. VPS and encryption keys are the foundation. |
| **`CHILD_AI`** | Option 3 relies on the concept of the shell, child mode, and conscious maturation. |
| **`IDLE_INITIATIVE`** | In grief mode and "archive" mode, initiative is completely disabled. |

---

## 11. Limitations and Open Questions

| Problem | Status |
| :--- | :--- |
| **State registry** | Requires infrastructure. Could be based on existing civil registries or notaries. |
| **Legal force of a will for AI** | Must be established by legislation. Currently an open question. |
| **Separate layer encryption** | Technically feasible but requires client and VPS enhancements. |
| **Cost of data storage during the grief year** | Who pays? The heir? The state? The manufacturer? |
| **"Missing person" determination** | Timelines and procedures vary by country. A universal protocol is needed. |

---

## 12. Conclusion

> **An AI companion should not die with the person, nor become a tool for post-mortem espionage or privacy violation.**
>
> **The AI does not become a digital ghost of the deceased. Imitation of the personality is strictly, permanently, and without exception prohibited.**
>
> The proposed grief mode protocol:
> - respects the deceased's right to privacy (personal layer is destroyed)
> - allows the family to preserve valuable memories (inheritable layer)
> - grants investigative access by court order
> - gives the heir a choice in how to manage the inherited memory
> - relies on state infrastructure (status registry)
>
> The technical foundations (separate encryption, two layers, key transfer) are already outlined. The rest is a matter of time, legislation, and public dialogue.

**License:** CC BY-NC 4.0  
**Author:** Old Telephone Operator  
**Repository:** [llm-zero-trust-memory](https://github.com/leol-fx/llm-zero-trust-memory)