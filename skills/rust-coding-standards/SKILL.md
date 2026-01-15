---
name: rust-coding-standards
description: Enforce high-quality Rust coding standards including toolchain usage, core semantics, API design, and linting rules. This skill should be used when writing, reviewing, or refactoring Rust code to ensure it meets production-grade quality.
---

# **Modern Rust & Effective Rust – Skills Matrix**

**Scope**
This document defines what it means to develop, review, and maintain production-grade Rust in this codebase.

It establishes **capability requirements**, not self-reported skill claims.

Compliance is enforced by:

* `rustfmt`
* `clippy`
* CI gates
* API and module design rules

---

# **L0 — Toolchain & Workflow Discipline**

> Rust must be used as an industrial toolchain, not a scripting language.

### Required abilities

| Area            | Standard                                                                |
| --------------- | ----------------------------------------------------------------------- |
| Tooling         | `rustup`, `cargo`, `rustfmt`, `clippy`, `rustdoc`                       |
| Layout          | workspace, crate, lib/bin, features                                     |
| Cargo           | correct use of `dependencies`, `dev-dependencies`, `build-dependencies` |
| Editions        | understand 2018 / 2021 / 2024                                           |
| Reproducibility | `Cargo.lock`, `rust-toolchain.toml`                                     |

### Mandatory local checks

```bash
cargo fmt --all
cargo clippy --all-targets --all-features -D warnings
cargo test --all
cargo doc --no-deps
```

No PR is valid unless these pass locally and in CI.

---

# **L1 — Core Rust Semantics**

> Code must be *correct by construction*, not by convention.

You must be fluent in:

* Ownership, moves, borrows, reborrows, and drop order
* Lifetime design in APIs
* Trait system: associated types vs generic parameters
* `enum` for state modeling
* `Result<T, E>` for error semantics
* Newtypes for illegal-state elimination

### Forbidden patterns

* `Rc<RefCell<T>>` as a default escape hatch
* `Arc<Mutex<T>>` replacing real design
* `Option<T>` encoding multi-state logic
* `unwrap()` / `expect()` in production code

---

# **L2 — Effective Rust API Design**

> This repository builds **libraries**, not scripts.

### Required API standards

| Area         | Standard                                         |
| ------------ | ------------------------------------------------ |
| Construction | `new()`, `try_new()`, or builder patterns        |
| Invariants   | Enforced by types, not comments                  |
| Visibility   | `pub`, `pub(crate)`, and `mod` used deliberately |
| Errors       | Public APIs do not expose `anyhow`               |
| Stability    | APIs must be forward-compatible                  |

### Canonical pattern

```rust
pub struct Config { ... }

impl Config {
    pub fn builder() -> ConfigBuilder { ... }
}

pub struct Client { ... }

impl Client {
    pub fn new(cfg: Config) -> Result<Self, InitError>
}
```

---

# **L3 — Lint-Driven Engineering**

> Clippy is not advisory. It is a design constraint.

### Baseline lint policy

```toml
warns = [
    "clippy::pedantic",
    "clippy::nursery",
    "clippy::unwrap_used",
    "clippy::expect_used",
    "clippy::panic",
]
```

### Expectations

* Every lint must be understood, not ignored
* `#[allow]` requires justification
* Refactoring is preferred over suppression

### Formatting

`rustfmt` defines the canonical code form.
Any format diff is a CI failure.

---

# **L4 — Large Codebase & Workspace Hygiene**

> This is a system, not a crate.

| Area          | Requirement                     |
| ------------- | ------------------------------- |
| Workspace     | split core / api / impl / tools |
| Features      | avoid `cfg` pollution           |
| Dependencies  | minimal, no default features    |
| MSRV          | explicitly defined              |
| Encapsulation | aggressive use of `pub(crate)`  |

---

# **L5 — CI and Policy Enforcement**

> If CI does not enforce it, it is not real.

CI must run:

* `cargo fmt --check`
* `cargo clippy -D warnings`
* `cargo test`
* `cargo doc`
* `cargo deny` or `cargo audit`

PR rules:

* Lint changes require review
* Any `allow` requires rationale

---

# **L6 — Unsafe, FFI & Performance Boundaries**

> Unsafe is a **capability boundary**, not an escape hatch.

Every `unsafe` must define:

* Which invariant it upholds
* Why it is required
* Why it is safe

Standard:

```rust
/// # Safety
/// Caller must ensure ...
pub unsafe fn ...
```

`clippy::undocumented_unsafe_blocks` is mandatory.

---

# **Final Definition: Modern Rust Engineer**

A Modern Rust Engineer does not rely on discipline.
They rely on:

> **Types + Lints + Format + CI + Policy**

to make correctness structural rather than aspirational.

This document defines the minimum bar for contributing to this codebase.
