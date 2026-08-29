# Six Inline

A fork of [quickjs-ng](https://github.com/quickjs-ng/quickjs), forked at
`v0.16.2` (`1ab8676`). Upstream's README is preserved as
[README-quickjs-ng.md](README-quickjs-ng.md).

## What this is for

Making **framework-shaped JavaScript** viable in a memory-constrained browser
engine, without spending the memory advantage that motivated the host project.

The target is two workloads, measured against V8:

| workload | today | goal |
|---|---|---|
| closure-dispatch (framework machinery) | 119.5 ms — 14.2× V8 | ≤ 60 ms |
| object-churn (VDOM-shaped allocation) | 210.5 ms — 9.6× V8 | ≤ 105 ms |

The first bet is **inline caches** — quickjs-ng has shapes (`JSShape`) but no
inline caches at all. That is the largest available win that requires no
machine-code generation.

## What this is NOT for

**Beating V8 on numeric hot loops.**

The `01-hot-loop` benchmark runs at 21.8× V8 and that is *fine*. It is kept as a
diagnostic — it is where a JIT's presence shows up first — but it is not a goal,
and **improving it is not worth a single byte of footprint.**

A change that wins the hot loop and costs baseline memory has failed, however
good the number looks. V8 was refused on memory, not on speed: its marginal heap
is 6.6–36.2 MB *per workload*, while the host project renders an entire page in
9–16 MB. A fork that repeats that mistake more slowly is worse than no fork.

If you are about to optimise `01-hot-loop`: don't. Read the gate first.

## The gate

Work here is gated on pre-registered thresholds — performance *and* memory *and*
conformance, all of which must hold together. Nothing merges on a speed number
alone.

- **test262 baseline: 38 errors / 43,034 tests (99.91%)**, measured against the
  test262 commit this tag pins (`5ef1e57`), never against `main`. This number
  must never rise.
- **Baseline process PSS: 1.42 MB.** The G1 ceiling is 2.0 MB.

The full charter — thresholds, kill conditions, and the reasoning behind the
fork — lives in the host project at `thesis/six-inline/README.md`.

## Licence

MIT, inherited from quickjs-ng and Bellard's original QuickJS. See
[LICENSE](LICENSE).
