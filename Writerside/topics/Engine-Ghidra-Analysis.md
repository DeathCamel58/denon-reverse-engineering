# Engine Ghidra Analysis

This page documents a repeatable method for getting a stripped Engine OS application binary into a
navigable state in Ghidra, and the symbol naming conventions used across this documentation.

Everything here was derived from `/usr/Engine/Engine` out of the **SC6000 5.0.4** firmware, but the
technique applies unchanged to [](Reporter.md), [](Offline-Analyzer.md),
[](MIDI-Device-Scanner.md), [](Firmware-Updater.md) and [](SoundSwitch.md), which are built the same
way.

## Why the default analysis is not enough

Ghidra's auto-analysis finds only about half of the code in `Engine`. On the 5.0.4 SC6000 build:

| Measure                                      | Value          |
|----------------------------------------------|----------------|
| Functions after stock auto-analysis          | 24,252         |
| `.ARM.exidx` entries (one per real function) | 46,552         |
| Of those, already a function start           | 12,904 (27.7%) |
| Inside a wrongly-bounded existing function   | 160            |
| **No function at all; undiscovered code**    | **33,488**     |

The surplus of 24,252 over 12,904 is PLT thunks and external stubs. The practical consequence is that
roughly 58% of the real code is invisible, so anything built on top of it (call graphs, cross-references,
vtable walking) silently under-reports.

> Fix function boundaries **before** doing any naming work. Every naming technique below depends on
> `getFunctionAt()` succeeding, and each one fails quietly rather than loudly when it doesn't.
>
{style="warning"}

## Step 1: recover function starts from `.ARM.exidx`

`.ARM.exidx` is the ARM exception unwind index. The toolchain emits one 8-byte entry per function,
sorted by address, and it survives stripping. Word 0 is a **prel31** self-relative offset to the
function start; word 1 is unwind data (or `EXIDX_CANTUNWIND`).

Decode word 0 by sign-extending bit 30 into bit 31 and adding it to the entry's own address:

```C
long off = (long)((word0 << 1) >> 1);   /* sign-extend the 31-bit value */
long target = entry_address + off;
```

On the 5.0.4 build, all 46,552 decoded targets land inside `.text` (`0016d840`-`01d7a667`), which is
a good sanity check that the decoding is right.

For each target with no function: `disassemble(addr)` then `createFunction(addr, null)`. Ghidra's
disassembler follows flow, so one call often recovers several neighboring functions at once.

> **Do not set the Thumb bit from `.ARM.exidx`.** Unlike a function pointer stored in a vtable, an
> exidx target does not use bit 0 as an ARM/Thumb selector. On this build all 46,552 targets are
> 4-byte aligned with bit 0 clear, and `TMode` is already correct at each one. The recovered bytes
> decode as textbook ARM prologues, e.g. `30 48 2d e9` = `push {r4, r5, r11, lr}`.
>
{style="note"}

Result on 5.0.4:

| Stage                                  | Functions |
|----------------------------------------|-----------|
| Before                                 | 24,252    |
| After exidx recovery                   | 57,771    |
| After Ghidra's follow-up auto-analysis | 58,457    |

Coverage afterward is **46,295 / 46,552 = 99.45%** of exidx targets as function starts, leaving 212
inside an existing function and 45 with no function. Only 14 `createFunction` calls failed outright.

### `.ARM.exidx` is not exhaustive

That 99.45% is coverage of the *exidx table*, which is different from coverage of `.text`. Measured
afterward on 5.0.4:

| Measure of `.text` (29,412,904 bytes) | Share      |
|---------------------------------------|------------|
| Instruction bytes                     | 84.73%     |
| Defined data                          | 4.11%      |
| **Still undefined**                   | **11.17%** |
| Covered by a function body            | 82.86%     |

There are **1,104 gaps larger than 4 KB between consecutive exidx targets, totaling 8.79 MB:
29.9% of `.text`**. Some of that is the tails of large functions, but a meaningful amount is code the
unwind table simply never describes (handwritten assembly, and functions built without unwind info).

This is not academic. While chasing a hardcoded TCP port, a genuine
`movw r7,#0xa1b9` instruction was found at `01a95054` sitting in undefined bytes, inside a ~10 KB
exidx gap between the targets `01a94b94` and `01a971e4`. A scalar-operand scan across all 6.2 M
disassembled instructions missed it entirely, because Ghidra had never turned those bytes into
instructions.

> **When hunting for a constant, do not rely on an instruction/operand scan alone.** Search raw bytes
> for the encoding too. For ARM `movw Rd,#imm16` the little-endian pattern is
> `[imm12 & 0xFF] [(Rd << 4) | (imm12 >> 8)] [imm4] [0xE3]`, where `imm4 = imm >> 12`. Note that a
> conditional form such as `movweq` has `0x03` in the top byte instead of `0xE3`, and check 4-byte
> alignment to discard coincidental matches in data.
>
{style="warning"}

Leave **ARM Aggressive Instruction Finder** disabled throughout. `.ARM.exidx` is authoritative, and
AIF invents code that is not there.

## Step 2: name from C++ RTTI vtables

`Engine` is built with RTTI and exceptions intact, and ELF relocations are applied in the Ghidra
loader, so vtables in `.data.rel.ro` contain real pointers.

The Itanium C++ ABI layout this relies on:

- **typeinfo**: word 0 is a vptr, word 1 points to the mangled type-name string.
- **vtable**: `[offset-to-top][typeinfo ptr][virtual fn 0][virtual fn 1]...`

So the walk is:

1. Collect mangled type-name strings from `.rodata` (e.g. `32RecordingNormalisationController`,
   `N6Planck9DeckStateE`). Demangle by prefixing `_ZTS` and calling Ghidra's
   `DemanglerUtil.demangle()`, which yields `RecordingNormalisationController` and
   `Planck::DeckState`.
2. A **typeinfo struct** is any address `T` where the word at `T+4` is one of those strings.
3. A **vtable** is any word equal to some `T` where the word at `A-4` is a plausible offset-to-top:
   `0` for a primary vtable, a small negative like `0xfffffffc` for a secondary one. This filter
   matters; it rejected 3,720 false positives on 5.0.4, mostly `__si_class_type_info` base-class
   lists, which also contain a pointer to a typeinfo struct but are not vtables.
4. Virtual function pointers follow from `A+4`. **Mask bit 0** (`ptr & ~1`). Here the Thumb bit
   *is* used, and 463 functions in this binary really are Thumb. Tolerate a slot or two that doesn't
   resolve (pure-virtual, PLT) before deciding the vtable has ended.

The payoff is entirely dependent on Step 1:

| Measure                             | Stock analysis | After exidx recovery |
|-------------------------------------|----------------|----------------------|
| Vtables accepted                    | 6,528          | 6,528                |
| Vtable slots resolved to a function | 2,253          | **38,532**           |
| Distinct functions attributed       | 613            | **16,738**           |

### Do not name shared virtuals

Of the 16,738 attributed functions, 15,141 are claimed by exactly one class and 1,597 appear in two
or more vtables. The latter are inherited-but-not-overridden base methods, or thunks; naming them
after one arbitrary claimant is simply wrong. Annotate them with the candidate list instead and
leave the name alone.

## Step 3: name from strings the compiler left behind

Release builds of `Engine` still carry assertion machinery, which is unusually generous:

- **`__PRETTY_FUNCTION__` signatures**: full source signatures such as
  `bool Hardware::updateTouchDevicePathsInConfig(const QString&, const QString&)`. The function that
  references the string *is* the function it names, so these are authoritative. 316 exist on 5.0.4,
  resolving to 289 functions.
- **Assertion strings with build paths**: e.g.
  `it != _deckStates.end()\nPrecondition failed\n/usr/src/debug/planck/0.0.0+git/App/src/Planck/gui/Views/BeatGridEditView/BeatGridEditViewController.cpp:253`.
  214 exist. These give a source file and line for the containing function even when they don't give
  a name, and their directory distribution is a useful map of the codebase.
- **Logger name literals**: the logging framework passes the method name as a plain literal, e.g.
  `std::__ostream_insert(..., "createGRPCStub", 0xe)`. There are 10,617 bare-identifier string
  candidates. Only trust one where the string has a single reference from a single function.
- **gRPC method-path strings**: `/networktrust.v1.NetworkTrustService/CreateTrust` and friends.
  A function referencing exactly one is that method's stub; a function referencing many is the Stub
  constructor that registers them all.

> Watch for inlining. 30 functions on 5.0.4 reference more than one `__PRETTY_FUNCTION__` string
> because a small method was inlined into a larger one. A naive one-pass renamer processes the
> strings in address order and the last write silently wins. Collect all signatures per function
> first, pick one deterministically, and record the rest as inlined in the plate comment.
>
{style="warning"}

## Naming conventions used in this documentation

| Form                            | Meaning                                                                    | Ghidra source type |
|---------------------------------|----------------------------------------------------------------------------|--------------------|
| `Class::method`                 | Exact source name, from a `__PRETTY_FUNCTION__` signature or a log literal | `USER_DEFINED`     |
| `Class::vfunc_N`                | Virtual function at **vtable slot N** of `Class`. A position, not a name   | `ANALYSIS`         |
| `Service::Method_rpc`           | gRPC stub for one method path                                              | `USER_DEFINED`     |
| `Service::Stub_registerMethods` | gRPC Stub constructor registering several method paths                     | `USER_DEFINED`     |
| `FUN_xxxxxxxx`                  | Untouched. No anchor and no vtable slot                                    | default            |

The two source types are the useful part: `USER_DEFINED` means a name traceable to a string in the
binary, `ANALYSIS` means it was inferred from the vtable position. Never promote the second to the first.

`Class::vfunc_N` deserves emphasis. It says *where* the function sits, not *what* it does. It is
accurate and good enough to navigate by, but does not read intent into the number.

Every renamed function also gets a plate comment recording where the name came from: the vtable
address and slot, or the source signature and the string's address.

## Result on 5.0.4

| Category                                  | Count  |
|-------------------------------------------|--------|
| Total functions (non-external)            | 54,832 |
| `Class::vfunc_N` from RTTI                | 14,971 |
| Exact names from source anchors           | 294    |
| Other (imports, thunks, demangled)        | 6,761  |
| Still `FUN_`                              | 32,806 |
| Functions carrying a plate comment        | 17,014 |

About 40% of functions carry a meaningful name. The remaining `FUN_` entries are overwhelmingly
non-virtual internal helpers with no anchor of any kind. Expected, and not a problem for
navigation, since the named 40% includes essentially every class boundary.

This work surfaced subsystem namespaces that were previously undocumented, including
`airNetworkDiscoverer`, `airNetworkExchange`, `airNfsFileAdapter`, `grpcutils`, `librarian`,
`eaas`, `engineSync` and `PlanckScriptRoot`. See [](Engine-Networking.md).

## A note on `.gnu_debuglink`

The ELF contains a `.gnu_debuglink` section, so a separate unstripped debug file exists somewhere in
inMusic's build pipeline. If a copy ever becomes available, it would supersede this entire page. Until
then, `.ARM.exidx` plus RTTI is the best available substitute.

## Related Files

- [](Engine.md) covers the binary itself.
- [](Engine-Networking.md) covers the network services this analysis exposed.
- [](Engine-Library.md) covers the database format.
