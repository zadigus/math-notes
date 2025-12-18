Great question. If your priorities are **(a) formal verification of real software** and **(b) turning formally proved programs into runnable code**, the short answer (today) is:

> **Pick Coq** (aka _Rocq_) for production‑grade pipelines.  
> Consider **Lean 4** when you want a highly ergonomic prover/programming language with excellent metaprogramming and math libraries, and you’re OK compiling Lean itself rather than “extracting” to another language. [[1]](https://www.cs.princeton.edu/courses/archive/spring24/cos510/sf/lf/Extraction.html)[[2]](https://dl.acm.org/doi/epdf/10.1145/3656379)

Below is a practical comparison from a software‑engineer’s perspective.

---

## What “code generation from proofs” looks like in each ecosystem

### Coq: mature _extraction_ + verified toolchains

- **Built‑in extraction** turns proven Gallina functions into **OCaml/Haskell/Scheme**; widely taught and used (e.g., Software Foundations). You can control how types map to target types (e.g., map `bool`/`list` to native types). [[1]](https://www.cs.princeton.edu/courses/archive/spring24/cos510/sf/lf/Extraction.html)[[3]](https://softwarefoundations.cis.upenn.edu/vfa-current/Extract.html)
- **Verified extraction to OCaml**: MetaCoq’s newer, _formally verified_ pipeline reduces your TCB; it proves a correctness theorem for the extraction and clarifies interop guarantees. (PLDI 2024.) [[2]](https://dl.acm.org/doi/epdf/10.1145/3656379)
- **To C with proofs**: **CertiCoq** compiles Gallina to C (_Clight_), designed to pair with the **CompCert** verified C compiler for an end‑to‑end verified path to assembly. This is actively developed; there’s a verified FFI and publications showing the codegen step from CPS to C. [[4]](https://certicoq.org/)[[5]](https://www.cs.princeton.edu/~appel/papers/CPStoC.pdf)
- **Verified C compilation**: **CompCert** is the gold‑standard verified C compiler (ARM/x86/RISC‑V/PowerPC). Many industrial users rely on its semantic preservation guarantees. [[6]](https://compcert.org/)[[7]](https://inria.hal.science/hal-01091802v9/document)

**Typical Coq pipeline options** 1) _Gallina algorithm + proof_ → **extraction** → OCaml/Haskell → (native compiler) ✔️ [[1]](https://www.cs.princeton.edu/courses/archive/spring24/cos510/sf/lf/Extraction.html)  
2) _Gallina algorithm + proof_ → **CertiCoq** → C → **CompCert** → assembly ✔️ [[4]](https://certicoq.org/)[[6]](https://compcert.org/)  
3) _Existing C code_ → **specify & verify** with **VST** (Verifiable C) or **Frama‑C/WP**, then compile with CompCert ✔️ [[8]](https://softwarefoundations.cis.upenn.edu/vc-current/Verif_sumarray.html)[[9]](https://www.frama-c.com/fc-plugins/wp.html)

These give you multiple “formally justified” routes from proof to executable.

---

### Lean 4: compile Lean programs (with proofs) to C, but not “extraction”

- **Lean 4 is both a prover and a programming language**. The Lean compiler translates Lean code to **C** and then to native binaries; this is great for writing tactics, DSLs, and general programs in Lean itself with high performance. (Lean 4 is self‑hosted and emphasizes efficient code generation.) [[10]](https://pp.ipd.kit.edu/uploads/publikationen/demoura21lean4.pdf)[[11]](https://en.wikipedia.org/wiki/Lean_%28proof_assistant%29)
- **However**, Lean doesn’t currently offer a _mature, standard “extraction” pipeline_ that erases proofs and emits idiomatic OCaml/Haskell/C code the way Coq does. Research exploring Lean–style extraction exists, but it’s not an established, production pipeline today. [[12]](https://www.normalesup.org/~sdima/2025_extraction_report.pdf)
- **Verification frameworks**: the ecosystem is growing (e.g., a **Lean 4 port of the Iris** separation‑logic framework exists), but most heavyweight, field‑tested verification stacks have historically been Coq‑centric. [[13]](https://github.com/leanprover-community/iris-lean)

**Lean 4 practical upshot**  
You can **prove properties inside Lean** and then **compile the Lean program** to C and a native binary. That’s perfectly viable if your deliverable can be a Lean‑compiled artifact and you trust Lean’s compiler/runtime. It is _not_ “proof‑erasure extraction to another ecosystem” in the Coq sense. [[10]](https://pp.ipd.kit.edu/uploads/publikationen/demoura21lean4.pdf)

---

## Ecosystem depth for _software_ verification

- **Coq** has long‑standing, production‑oriented frameworks:  
    - **VST (Verified Software Toolchain)**: Hoare‑logic/separation‑logic proofs for C; composes with CompCert. Used in substantial case studies and taught widely. [[8]](https://softwarefoundations.cis.upenn.edu/vc-current/Verif_sumarray.html)[[14]](https://github.com/PrincetonUniversity/VST)
    - **Iris** (higher‑order concurrent separation logic) and many large program‑logic developments are Coq‑native. [[15]](https://iris-project.org/)[[16]](https://www.cambridge.org/core/journals/journal-of-functional-programming/article/iris-from-the-ground-up-a-modular-foundation-for-higherorder-concurrent-separation-logic/26301B518CE2C52796BFA12B8BAB5B5F)
    - **Frama‑C/WP** integrates with Coq to discharge proof obligations for annotated C. (Industrialized plugin; used for high‑assurance code.) [[9]](https://www.frama-c.com/fc-plugins/wp.html)
- **Lean** shines for formal mathematics (massive **mathlib** library) and powerful **metaprogramming**. For _software_ verification, the story is improving (e.g., Iris‑Lean), but depth and tooling maturity for end‑to‑end “prove‑then‑extract/compile elsewhere” pipelines lag Coq’s today. [[17]](https://github.com/leanprover-community/mathlib4)[[18]](https://github.com/leanprover-community/mathlib4/wiki/Metaprogramming-for-dummies)

---

## Recommendation by goal

- **You need a proven, end‑to‑end story “proof → code”**  
    → **Coq**. Use built‑in extraction for OCaml/Haskell/Scheme, or **CertiCoq + CompCert** for C/assembly with minimized TCB. This is the path with the most precedent in real projects. [[1]](https://www.cs.princeton.edu/courses/archive/spring24/cos510/sf/lf/Extraction.html)[[4]](https://certicoq.org/)[[6]](https://compcert.org/)
    
- **You want to verify C code that already exists**  
    → **Coq** + **VST** or **Frama‑C/WP**, then build with CompCert for maximum assurance. [[8]](https://softwarefoundations.cis.upenn.edu/vc-current/Verif_sumarray.html)[[9]](https://www.frama-c.com/fc-plugins/wp.html)
    
- **You prefer to program _in the prover_ with excellent ergonomics, metaprogramming, and math libraries, and are fine producing a Lean‑compiled binary**  
    → **Lean 4**. You’ll compile Lean to C and link it, not extract to (say) OCaml. Keep in mind the extraction gap if your org standardizes on another runtime. [[10]](https://pp.ipd.kit.edu/uploads/publikationen/demoura21lean4.pdf)
    

---

## A couple of concrete “starter” pipelines

**Coq → OCaml (fastest path to a running artifact)**  
1) Specify & prove an algorithm in Gallina.  
2) `Extraction Language OCaml.` + `Extraction "foo.ml" your_fn.`  
3) Build with `ocamlopt` or integrate into your service. (You can map Coq types to OCaml natives.) [[1]](https://www.cs.princeton.edu/courses/archive/spring24/cos510/sf/lf/Extraction.html)

**Coq → verified C/ASM (max assurance)**  
1) Prove algorithm in Coq → **CertiCoq** → C.  
2) Compile C with **CompCert** to assembly/binary.  
3) Optionally use the **CertiCoq FFI** to interop with C code. [[4]](https://certicoq.org/)

**C code → proof → trustworthy binary**  
1) Annotate with ACSL; run **Frama‑C/WP**; discharge hard VCs in **Coq**.  
2) Compile with **CompCert**. [[9]](https://www.frama-c.com/fc-plugins/wp.html)

---

## Bottom line

- For **formal verification of software** with a **credible, production‑ready code‑generation story**, **Coq wins today** thanks to mature extraction, verified extraction work (MetaCoq), the CertiCoq→CompCert path to C/assembly, and rich verification frameworks (VST, Iris, Frama‑C/WP). [[2]](https://dl.acm.org/doi/epdf/10.1145/3656379)[[4]](https://certicoq.org/)[[6]](https://compcert.org/)[[8]](https://softwarefoundations.cis.upenn.edu/vc-current/Verif_sumarray.html)
- **Lean 4** is excellent for _proving while programming in Lean_, with strong tooling and performance, but **doesn’t yet replace Coq’s extraction/verified‑compiler ecosystem** when your deliverable must be OCaml/Haskell/C produced outside Lean’s compiler. [[10]](https://pp.ipd.kit.edu/uploads/publikationen/demoura21lean4.pdf)[[12]](https://www.normalesup.org/~sdima/2025_extraction_report.pdf)

---

If you share a bit more about your target stack, I can tailor the choice:

- **Embedded C/RTOS** with strict assurance?
- **Services in OCaml/Haskell** where extracted code plugs in?
- **Greenfield code where compiling Lean → C is acceptable?**

Given your background, Michel, do you lean (no pun intended) toward verifying existing C/C++ bits, or building proved components and integrating them into an OCaml/Haskell or C codebase?

  
References

[1] [www.cs.princeton.edu](https://www.cs.princeton.edu/courses/archive/spring24/cos510/sf/lf/Extraction.html)

[2] [dl.acm.org](https://dl.acm.org/doi/epdf/10.1145/3656379)

[3] [softwarefoundations.cis.upenn.edu](https://softwarefoundations.cis.upenn.edu/vfa-current/Extract.html)

[4] [certicoq.org](https://certicoq.org/)

[5] [www.cs.princeton.edu](https://www.cs.princeton.edu/~appel/papers/CPStoC.pdf)

[6] [compcert.org](https://compcert.org/)

[7] [inria.hal.science](https://inria.hal.science/hal-01091802v9/document)

[8] [softwarefoundations.cis.upenn.edu](https://softwarefoundations.cis.upenn.edu/vc-current/Verif_sumarray.html)

[9] [www.frama-c.com](https://www.frama-c.com/fc-plugins/wp.html)

[10] [pp.ipd.kit.edu](https://pp.ipd.kit.edu/uploads/publikationen/demoura21lean4.pdf)

[11] [en.wikipedia.org](https://en.wikipedia.org/wiki/Lean_%28proof_assistant%29)

[12] [www.normalesup.org](https://www.normalesup.org/~sdima/2025_extraction_report.pdf)

[13] [github.com](https://github.com/leanprover-community/iris-lean)

[14] [github.com](https://github.com/PrincetonUniversity/VST)

[15] [iris-project.org](https://iris-project.org/)

[16] [www.cambridge.org](https://www.cambridge.org/core/journals/journal-of-functional-programming/article/iris-from-the-ground-up-a-modular-foundation-for-higherorder-concurrent-separation-logic/26301B518CE2C52796BFA12B8BAB5B5F)

[17] [github.com](https://github.com/leanprover-community/mathlib4)

[18] [github.com](https://github.com/leanprover-community/mathlib4/wiki/Metaprogramming-for-dummies)