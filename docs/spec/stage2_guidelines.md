# Stage 2 Master Architectural Guidelines & Adversarial Dialectic Protocol

## 1. Project Identity & Objective
* **Name:** JANKY (**J**SON-Isomorphic **A**cyclic **N**avigable **K**inetic **Y**arn)
* **Objective:** Establish the formal, bit-level mathematical specification, type system, IDL grammar, and memory safety model for JANKY before implementation.
* **Core Language:** **Rust**. Every architecture decision must maximize Rust's unique systems features: zero-cost abstractions, compile-time borrow/lifetime tracking (`&'a [u8]`), direct SIMD intrinsics (`core::arch`), const generics, zero-sized types (ZSTs) for typestates, `#[repr(C, align(64))]`, and `MaybeUninit<T>`.

---

## 2. The Adversarial Dialectic Protocol (Proposer vs. Challenger)
To ensure JANKY is truly unbreakable, no architectural claim is accepted without surviving rigorous adversarial peer review.

For every core engineering domain, work is divided into a **Proposer** and an **Adversarial Red Team Challenger**:
1. **The Proposer** drafts an exhaustive, bit-level, technically ambitious specification.
2. **The Challenger** independently dissects the proposal, conducting live research, running benchmark tests/scripts, probing for undefined behavior (UB), CPU pipeline stalls, cache line evictions, edge-case math overflows, and distributed failure modes.
3. Every critique must offer concrete counter-examples, failure scenarios, and constructive revisions.

---

## 3. The 4 Engineering Battlegrounds

### Battleground 1: Wire Layout, SIMD & Microarchitecture
* **Proposer:** Hardware-Accelerated Wire Architect
  * Scope: Frame envelope, 64-byte SIMD alignment, Directory Stream (64-bit Popcount Presence Bitmask + 16-bit offset jump table), StreamVByte integer packing, German StringView 16-byte slot, PAX micro-blocks.
  * Target Output: `/data/data/com.termux/files/home/serial/docs/spec/01_wire_format_proposal.md`
* **Challenger:** Microarchitectural Red Team & Performance Adversary
  * Scope: Attack the proposal! Probe:
    - Does 64-byte alignment cause unacceptably high padding bloat on 20-byte micro-payloads?
    - Can StreamVByte decode stall on unaligned vector loads?
    - Does Popcount `_mm_popcnt_u64` introduce pipeline dependencies on non-x86/non-ARM targets?
    - What happens when a German StringView prefix matches but the full string differs?
  * Target Output: `/data/data/com.termux/files/home/serial/docs/spec/01_wire_format_critique.md`

### Battleground 2: Acyclic Safety, Rust Memory Model & Zero-Copy Proofs
* **Proposer:** Acyclic Safety & Memory Model Architect
  * Scope: Mathematical proof of Forward-Monotone Offsets ($\text{Addr}(Target) > \text{Addr}(Current)$), eliminating pointer cycles by construction. Safe Rust zero-copy lifetime projection (`&'a [u8]`, `&'a str`), top-down typestate builder patching offsets at compile-time.
  * Target Output: `/data/data/com.termux/files/home/serial/docs/spec/02_safety_and_memory_model.md`
* **Challenger:** Exploit Vector & Formal Verification Adversary
  * Scope: Attack the safety claims! Probe:
    - Can 64-bit/32-bit integer addition overflow wraparound allow backward pointers in forward-only logic?
    - Can an attacker chain thousands of forward pointers into an exponential DAG that blows the stack on recursive traversal?
    - Does safe Rust slice casting violate aliasing rules or cause unaligned read UB?
    - Buffer-proportional allocation bounds under malicious payload truncation.
  * Target Output: `/data/data/com.termux/files/home/serial/docs/spec/02_safety_critique.md`

### Battleground 3: Dual-State Janus Grammar & Lossless Text Isomorphism
* **Proposer:** Janus Dual-State & Text Grammar Architect
  * Scope: JANKY-Text grammar (JSON-compatible superset, comments, trailing commas, unquoted keys, native hex/byte literals `b"..."`), 1:1 lossless bidirectional mapping with JANKY-Binary, length-prefixed string fastpaths for SIMD text parsers.
  * Target Output: `/data/data/com.termux/files/home/serial/docs/spec/03_textual_grammar_proposal.md`
* **Challenger:** Parser Ergonomics & Semantic Ambiguity Adversary
  * Scope: Attack the grammar! Probe:
    - Floating point string round-trip precision (IEEE 754 Ryu/Dragonbox edge cases, `NaN` bit patterns, `-0.0` vs `+0.0`).
    - Unicode normalization hazards (NFC vs NFD) across language runtimes.
    - Ambiguities between unquoted keys and reserved literals.
    - Does SIMD length-prefixed text parsing introduce syntax injection attack vectors?
  * Target Output: `/data/data/com.termux/files/home/serial/docs/spec/03_textual_grammar_critique.md`

### Battleground 4: Schema IDL, Evolution & Legacy Interop Bridges
* **Proposer:** Schema IDL & Evolution Architect
  * Scope: Minimalist JANKY IDL syntax, content-addressed 32-bit union tags (xxHash32), open enums backed by native ints, embedded 64-bit HighwayHash schema fingerprints, streaming zero-allocation JSON/Protobuf transcoder pipeline.
  * Target Output: `/data/data/com.termux/files/home/serial/docs/spec/04_schema_and_interop_proposal.md`
* **Challenger:** Distributed Systems & Evolution Adversary
  * Scope: Attack evolution guarantees! Probe:
    - Is a 32-bit hash on union variants vulnerable to birthday paradox collisions at scale?
    - What happens when default values mutate across microservice deploys?
    - How does the streaming transcoder handle malformed JSON without memory exhaustion?
    - Benchmark claims: is zero-allocation transcoding really possible across all JSON edge cases?
  * Target Output: `/data/data/com.termux/files/home/serial/docs/spec/04_schema_and_interop_critique.md`

---

## 4. Mandatory Standards for All Reports
1. **Concrete Bit/Byte Diagrams:** ASCII/byte-offset tables down to the bit level.
2. **Rust Code & Struct Signatures:** Exact Rust types, lifetimes, and safety invariants.
3. **Empirical Verification:** When claiming cycle counts, SIMD instructions, or memory footprints, run verification scripts or cite processor optimization manuals.
4. **Actionable Resolutions:** Challengers must not merely complain; they must propose mathematically sound solutions to close every vulnerability.
