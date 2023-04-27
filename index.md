---
layout: default.liquid
title: Cranelift
---

<style>
section pre {
  padding: 1em;
}
</style>

<section class="section-hero">
<div class="container w-container">

# Cranelift

## An optimizing compiler backend

A <a href="https://bytecodealliance.org/">Bytecode Alliance</a> project

Cranelift is a *fast*, *secure*, *relatively simple* and *innovative* compiler
backend. It takes an [intermediate representation][IR] of a program generated
by some frontend and compiles it to executable machine code. Cranelift is meant
to be used as a library within an "embedder". It is in successful use by the
[Wasmtime] WebAssembly virtual machine, for just-in-time (JIT) and
ahead-of-time (AOT) compilation, and also as an [experimental backend][cg-clif]
for the [Rust] compiler. Its most common use is as a WebAssembly compiler, but
it is fully general and usable for most code-generation needs. Cranelift itself
is written in Rust.

Cranelift currently supports x86-64, aarch64 (ARM64), s390x (IBM Z), and
riscv64 platforms. It is retargetable and contributions of further ISA support
are welcome.

Cranelift is actively maintained and used in production to run sandboxed
untrusted code with close-to-native performance. We continue to develop it to
add feature support, improve performance, and further verify its correctness.
We follow [Wasmtime]'s [release policy] and [security policy].

### Attributes

* **Fast**: Cranelift is developed with the speed of the compiler itself in
  mind. We aim for Cranelift to be usable as a just-in-time (JIT) compiler. It
  has been [measured](https://arxiv.org/abs/2011.13127) to perform the
  code-generation process about an order of magnitude faster than an equivalent
  LLVM-based system.

  Cranelift aims to generate fast-enough code, as well. While it does not have
  the aggressive optimization stance (or complexity) of larger compilers such
  as [LLVM] or [gcc], its output is often not too far off: the [same
  paper](https://arxiv.org/abs/2011.13127) showed Cranelift generating code
  that ran ~2% slower than V8 (TurboFan) and ~14% slower than an LLVM-based
  system.

* **Secure**: Cranelift has a strong focus on correctness and verification. In
  addition to developing the compiler itself in a memory-safe language (Rust),
  we continually work to test and verify the compiler in new ways with
  [fuzzing], [symbolic translation validation] of some stages, correctness
  proofs for critical pieces where appropriate, and more recently, static
  [formal verification] as well.  We have designed the compiler with
  abstractions to prevent bugs and enforce invariants wherever possible. We
  consciously navigate complexity tradeoffs and choose to avoid some riskier
  and more error-prone optimization techniques. For example, unlike most
  optimizing compilers, our IR does not have undefined behavior, by design.
  Finally, we keep up to date with mitigation techniques, both for side-channel
  attacks like [Spectre], and for general defense-in-depth with [hardware CFI
  features] and other hardening.

  Cranelift is also, like browser JIT engines' compilers but unlike LLVM or
  gcc, specifically hardened against malicious compiler input: it is written to
  guard against pathological runtimes, for example by avoiding
  input-length-bounded recursion, and quadratic or higher algorithmic
  complexity wherever possible. It is not a linear-time single-pass compiler,
  but it is still suitable for compiling untrusted code (for example, Wasm
  modules). We fuzz while monitoring for out-of-memory and timeout conditions
  in compilation to verify this.

  Cranelift follows the [Bytecode Alliance security policy][security policy].
  We have had two sandbox-escape-capable miscompilation CVEs in Cranelift's
  7-year history. We welcome responsible disclosure of vulnerabilities and we
  follow best practices in disseminating notices and patches.

* **Relatively simple**: Cranelift is an optimizing compiler, but it aims to
  take a fresh look at which optimizations are necessary. We have explicitly
  avoided features -- such as advanced alias analysis or use of undefined
  behavior -- that have historically led to subtle miscompilations in other
  compilers. Cranelift consists of about 200 thousand lines of code; in
  contrast, e.g. LLVM consists of over 20 million lines of code, a hundred
  times larger. This difference also allows Cranelift to be relatively
  approachable to developers, researchers, auditors and others who wish to
  understand how it works.

* **Innovative**: In order to compete as a viable alternative to larger
  compilers, and in order to maximize correctness and security in particular,
  Cranelift is open to innovative approaches, and aims to be an actively
  welcoming home to research collaborations. We are (to our knoweldge) the
  first production compiler to use [e-graphs] to build a unified optimization
  framework. We have developed new fuzzing techniques, such as a
  [semantics-preserving fuzzing mutator] and a [symbolic translation
  validator][symbolic translation validation] for our register allocator. We
  developed a [custom pattern-matching DSL] for optimization rewrite rules and
  instruction lowering rules in order to ensure correctness and allow
  high-level integration with verification tools. We strongly leverage fuzzing
  with custom oracles, such as differential comparison of execution results or
  validation of invariants (in practice more similar to [property-based
  testing]) in order to efficiently find bugs and develop with high confidence.
  We actively collaborate with several academic groups to [formally
  verify][formal verification] parts of the compiler.

[Wasmtime]: https://github.com/bytecodealliance/wasmtime
[cg-clif]: https://github.com/bjorn3/rustc_codegen_cranelift
[fuzzing]: https://bytecodealliance.org/articles/1-year-update#improving-testing-with-fuzzing
[symbolic translation validation]: https://cfallin.org/blog/2021/03/15/cranelift-isel-3/
[formal verification]: https://www.cs.cornell.edu/~avh/veri-isle-preprint.pdf
[Spectre]: https://en.wikipedia.org/wiki/Spectre_(security_vulnerability)
[hardware CFI features]: https://github.com/bytecodealliance/rfcs/blob/main/accepted/cfi-improvements-with-pauth-and-bti.md
[security policy]: https://bytecodealliance.org/security
[release policy]: https://docs.wasmtime.dev/stability-release.html
[e-graphs]: https://github.com/bytecodealliance/rfcs/pull/27
[semantics-preserving fuzzing mutator]: https://www.jacarte.me/assets/pdf/wasm_mutate.pdf
[custom pattern-matching DSL]: https://github.com/bytecodealliance/wasmtime/blob/main/cranelift/isle/docs/language-reference.md
[property-based testing]: https://en.wikipedia.org/wiki/Software_testing#Property_testing

</div>
</section>

<section>
<div class="container w-container">

## Documentation

Cranelift has [general documentation][docs] describing aspects of its design,
such as its [intermediate representation][IR], CLIF. Many of the [Wasmtime
documents][wasmtime-docs] are also applicable: Cranelift is developed in-tree
in Wasmtime and generally follows its development, testing and release
processes, for example. The [contributing] documentation may be especially
useful.

For users of Cranelift, the [API] documentation for the main crate
[`cranelift-codegen`](https://docs.rs/cranelift-codegen) and its auxiliary
helpers for IR producers and Wasm-centric embedders,
[`cranelift-frontend`](https://docs.rs/cranelift-frontend) and
[`cranelift-wasm`](https://docs.rs/cranelift-wasm) respectively, may be useful
to reference. Developers of Cranelift backends will find the above API
references useful, as well as the [ISLE DSL reference][isle].

Finally, please do feel free to stop by our [Zulip chat instance] and ask
questions -- we are happy to help!

[docs]: https://github.com/bytecodealliance/wasmtime/tree/main/cranelift/docs/
[IR]: https://github.com/bytecodealliance/wasmtime/blob/main/cranelift/docs/ir.md
[wasmtime-docs]: https://docs.wasmtime.dev/
[isle]: https://github.com/bytecodealliance/wasmtime/blob/main/cranelift/isle/docs/language-reference.md
[contributing]: https://bytecodealliance.github.io/wasmtime/contributing.html

</div>
</section>
