# Contributing to JANKY

Thank you for your interest in contributing to **JANKY** (**J**SON-Isomorphic **A**cyclic **N**avigable **K**inetic **Y**arn)!

JANKY is engineered to push serialization performance and formal safety to microarchitectural limits. To preserve these invariants, all contributions follow a **dialectic verification model** and a **cloud-first development workflow**.

---

## 1. Cloud-First Development Workflow (Zero Local Rust Required)

You do **not** need a local Rust compiler, Android NDK, or complex cross-compilation toolchains installed on your machine (whether working on Android Termux, Linux, macOS, or Windows).

All compilation, linting, test suites, and Miri memory verification run automatically in the cloud on **GitHub Actions**.

### Recommended Workflow:
1. **Fork and Clone:**
   ```bash
   git clone https://github.com/StackTactician/janky.git
   cd janky
   ```
2. **Make Edits & Push Branch:**
   ```bash
   git checkout -b feature/my-enhancement
   # Edit documentation, specifications, or code
   git commit -m "docs(spec): clarify PAX micro-block vector alignment"
   git push origin feature/my-enhancement
   ```
3. **Automated Verification:**
   GitHub Actions will automatically run:
   * **Matrix Compilation:** `x86_64-unknown-linux-gnu`, `aarch64-linux-android`, `wasm32-unknown-unknown`
   * **Linting & Formatting:** `cargo fmt --check`, `cargo clippy --all-targets -- -D warnings`
   * **Formal Memory Safety:** `cargo miri test` with Tree Borrows checking for undefined behavior.
4. **Download & Test Pre-Compiled Binaries Locally:**
   Using the GitHub CLI (`gh`), you can immediately fetch the compiled multi-arch binaries directly to your local terminal:
   ```bash
   # Download the latest build artifact from the active run
   gh run download -n janky-aarch64-linux-android
   chmod +x ./janky-cli
   ./janky-cli --version
   ```

---

## 2. Dialectic Engineering & Verification Standards

JANKY uses an adversarial dialectic engineering framework. Any architectural change, wire layout modification, or type opcode addition must satisfy three strict verification criteria:

1. **Adversarial Critique:** Every proposal must be accompanied by an independent critique identifying worst-case computational complexity, collision probability, or memory boundary failures.
2. **Mathematical Bounds:** Any dynamic indexing or hash-based discriminant must prove its collision bounds (e.g., $P(\text{collision}) < 10^{-6}$) and algorithmic complexity ($O(1)$ lookup, $O(L)$ traversal limit).
3. **Miri & Tree Borrows Compliance:** Zero-copy operations must never construct unaligned references (`&T`), must preserve pointer provenance, and must adhere to Rust's Stacked Borrows / Tree Borrows semantics.

---

## 3. Specification Governance & Directory Structure

* `docs/spec/JANKY_V1_MASTER_SPECIFICATION.md`: The normative master specification.
* `docs/spec/verification_*.md`: Formal proofs for memory safety, math bounds, protocol invariants, and CI/CD operations.
* `docs/research/`: Domain research dossiers covering the serialization landscape.
* `.github/workflows/`: Cloud matrix build and automated release pipelines.

---

## 4. Commit Convention

We adhere to the [Conventional Commits](https://www.conventionalcommits.org/) specification:

* `feat(...)`: A new feature or opcode addition.
* `fix(...)`: A bug fix or specification ambiguity resolution.
* `docs(...)`: Documentation or specification improvements.
* `spec(...)`: Normative specification changes or reconciliations.
* `test(...)`: Adding or updating test suites, fuzz harnesses, or test vectors.
* `ci(...)`: GitHub Actions workflow updates.

---

## 5. Community & Discussions

* For architectural discussions and questions, please use [GitHub Discussions](https://github.com/StackTactician/janky/discussions).
* For verifiable bugs or specification flaws, submit an issue via [GitHub Issues](https://github.com/StackTactician/janky/issues).
