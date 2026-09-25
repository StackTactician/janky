# Security Policy & Formally Verified Safety Model

## Supported Versions

| Version | Status | Supported |
| :--- | :--- | :--- |
| **v1.0-draft** | Stage 2 Design Complete & Formally Verified | :white_check_mark: |

---

## Reporting a Vulnerability

If you discover a security vulnerability or specification defect within JANKY:

1. **Do not** disclose the vulnerability publicly in issues or discussions.
2. Open a private security advisory on GitHub under **Security > Advisories > Report a vulnerability**.
3. Include:
   * A reproduction test case or byte payload vector.
   * Target architecture (x86-64, AArch64, WASM).
   * Impact analysis (memory corruption, DoS loop, out-of-bounds read, etc.).

We acknowledge reports within 48 hours and coordinate a coordinated disclosure timeline.

---

## Formally Verified Threat Model & Safety Invariants

JANKY eliminates the historical **Verifier Paradox** of zero-copy formats through mathematical guarantees built directly into the wire framing:

### 1. Acyclic Graph Invariant (No Pointer Cycles)
* **Theorem:** In untrusted zero-copy buffers, wire offsets are **strictly forward-monotone relative offsets**.
* **Verification Arithmetic:** An offset $\delta$ referencing target size $S_{\min}$ at slot position $s$ in buffer $L$ must satisfy:
  $$\delta \le (L - S_{\min}) - s$$
* Evaluated via **checked subtraction**, eliminating integer wraparound vulnerabilities and preventing any backward or self-referential pointer cycles.

### 2. Algorithmic Complexity Bounds (No Diamond DAG Freezes)
* **Attack:** A malicious adversary crafts an acyclic Diamond DAG of depth 60, where nodes fan in and out exponentially, forcing naive traversers into $2^{60}$ operations on a 2 KiB payload.
* **Invariant:** Every zero-copy traverser operates under an active `TraversalLimiter`. Total dereferenced payload words are capped at:
  $$\text{Budget} = 2 \times \left\lfloor \frac{L}{8} \right\rfloor$$
* Guarantees that traversal execution time is strictly $\mathcal{O}(L)$, preventing CPU exhaustion denial-of-service.

### 3. Stack Exhaustion Ceiling
* Recursion is capped at a strict upper limit:
  $$D_{\max} \le 64$$
* Deserialization traversers execute using a flat, stack-allocated array `[usize; 64]` ($\le 512\text{ bytes}$ of stack space). This eliminates stack overflow crashes on resource-constrained platforms (such as 64 KiB WebAssembly call stacks and 128 KiB musl libc threads).

### 4. Buffer-Proportional Allocation (CWE-789 Mitigation)
* Array lengths $N$ decoded from wire buffers must satisfy:
  $$N \le \left\lfloor \frac{B_{\text{rem}}}{S_{\min}} \right\rfloor$$
* Prevents decompression and allocation bombs from claiming gigabytes of heap memory based on untrusted integer headers.
