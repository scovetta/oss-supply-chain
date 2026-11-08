---
title: "Memory Safety and Language-Level Vulnerabilities"
description: "Discover why 70% of critical vulnerabilities are memory safety issues and how language choice can eliminate entire vulnerability classes."
icon: "lucide/memory-stick"
---

# 5.6 Memory Safety and Language-Level Vulnerabilities

Throughout the previous sections, we have examined vulnerabilities as individual flaws to be discovered, patched, and remediated. But a deeper pattern underlies many of the most severe vulnerabilities in the software supply chain: **memory safety**. The majority of critical vulnerabilities in foundational software (operating systems, browsers, cryptographic libraries, network stacks) result from a category of programming errors that certain languages permit and others prevent entirely. Understanding this pattern has profound implications for supply chain security, as it suggests that language choice can eliminate entire vulnerability classes rather than addressing them one CVE at a time.

## The Prevalence of Memory Safety Vulnerabilities

The data on memory safety vulnerabilities is remarkably consistent across major software projects:

**Microsoft** reported in 2019 that approximately 70% of all security vulnerabilities addressed in Microsoft products over the previous 12 years were memory safety issues.[^msrc-memory-safety] This figure has remained stable over time, despite decades of investment in secure coding practices, static analysis, and code review.

**Google Chrome** security data shows a similar pattern. Google's analysis of 912 high or critical severity security bugs since 2015 found that roughly 70% are memory safety vulnerabilities: buffer overflows, use-after-free, and related issues.[^chromium-memory-safety]

**Android** security bulletins reflect the same distribution. Memory safety issues dominate the high-severity vulnerabilities patched in Android releases.

**Apple** products, though the company discloses less detailed statistics, show the same pattern in security updates: memory corruption vulnerabilities appear consistently in iOS and macOS patches.

This convergence around 70% is not coincidental. It reflects the fundamental nature of C and C++, the languages in which most foundational software is written. These languages provide direct memory access and manual memory management: powerful capabilities that also enable classes of errors that safer languages prevent by design.

## Understanding Memory Safety Vulnerabilities

**Memory safety vulnerabilities** arise when programs access memory in unintended ways. To understand these, think of computer memory as a vast apartment building with numbered units. When your program runs, it rents specific apartments for its data: apartment 1000 for the username, apartments 1001-1100 for a file buffer, and so on. Memory safety vulnerabilities occur when programs accidentally read or write to apartments they have not rented, or continue accessing apartments after their lease has ended.

Several vulnerability classes fall under this umbrella:

**Buffer overflows** occur when a program writes data beyond the bounds of allocated memory. Imagine filling out a form with a 20-character field for your name, but writing 200 characters: the extra text spills over into adjacent fields, corrupting whatever was stored there. The classic stack buffer overflow can overwrite return addresses, enabling arbitrary code execution. Heap overflows corrupt adjacent data structures with similar potential for exploitation.

**Use-after-free** vulnerabilities occur when a program accesses memory after it has been deallocated. Continuing the apartment analogy: you move out of apartment 1000, but keep your key. A new tenant moves in. Later, you use your old key to enter and start rearranging furniture, or reading the new tenant's mail. If the memory has been reallocated for a different purpose, the access can corrupt unrelated data structures or leak sensitive information.

**Double-free** errors occur when memory is deallocated twice, potentially corrupting memory management structures and enabling exploitation. It is like returning your apartment key twice: the building manager's records become confused about who controls that unit.

**Null pointer dereferences** and **uninitialized memory access** can leak information or cause crashes, sometimes exploitable for denial of service or worse.

**Type confusion** occurs when memory is interpreted as a different type than intended, enabling attackers to manipulate program behavior.

These vulnerability classes share a common root: the languages permit operations that can corrupt program memory in ways the programmer did not intend. The compiler does not prevent these errors; they manifest at runtime, often exploitably.

For supply chain security, the implication is significant: dependencies written in C or C++ carry inherent risk of memory safety vulnerabilities. No amount of careful coding eliminates this risk entirely: the languages permit errors that even expert programmers make.

## Government Guidance on Memory Safety

The prevalence and severity of memory safety vulnerabilities has prompted government agencies to issue formal guidance recommending transition to memory-safe languages.

The **[NSA][nsa-memory-safety]** published "Software Memory Safety" in November 2022, explicitly recommending that organizations shift development to memory-safe languages. The guidance stated:

> "NSA recommends that organizations use memory safe languages when possible and bolster protection through code-hardening defenses."

The NSA specifically identified C#, Go, Java, Ruby, Rust, and Swift as examples of memory-safe alternatives to C and C++.

**[CISA][cisa-memory-safety]**, jointly with the FBI, NSA, and international partners, published "The Case for Memory Safe Roadmaps" in December 2023. This guidance called on software manufacturers to develop plans for transitioning to memory-safe languages, particularly for new development and for security-critical components.

The CISA guidance acknowledged that immediate migration is impractical but emphasized that the status quo (continuously patching memory safety vulnerabilities without addressing their root cause) is unsustainable. It asked manufacturers to publish roadmaps for eliminating memory safety vulnerabilities in their products, starting with new code and the most exposed components.

This government attention elevates memory safety from a technical concern to a strategic priority, with potential implications for procurement requirements and regulatory expectations.

## The Rust Transition in Critical Infrastructure

**Rust** has emerged as the leading memory-safe alternative for systems programming. Unlike garbage-collected languages (Java, Go, Python), Rust provides memory safety without runtime overhead through compile-time ownership checking. This makes Rust suitable for the performance-sensitive, resource-constrained environments where C and C++ have traditionally dominated.

Major infrastructure projects have begun incorporating Rust:

**Linux kernel** accepted Rust as a supported language for driver development starting with kernel 6.1 (December 2022). While the core kernel remains in C, new drivers can be written in Rust, enabling memory-safe development for new functionality. This represents a significant philosophical shift for a project that has used C exclusively for over 30 years.

**Android** has progressively increased Rust usage. Google reported that for Android 13, roughly 21% of new native code was written in Rust.[^android-rust] Google's analysis showed that as Rust adoption increased, memory safety vulnerabilities in Android decreased, providing empirical evidence for the security benefits.

**Windows** includes Rust components, with Microsoft experimenting with and shipping Rust in low-level Windows code. The company has been public about its Rust investment and its motivation in reducing memory safety vulnerabilities.

**curl**, the ubiquitous data transfer library, experimented with HTTP backend support written in Rust (hyper). This work illustrated both the appeal of memory-safe components and the integration challenges of introducing Rust into long-lived C projects with enormous deployment footprints.[^curl-installs]

**Sudo and su** received a Rust reimplementation (sudo-rs) sponsored by Amazon Web Services' Prossimo project, targeting one of the most security-critical Unix utilities.

These adoption examples demonstrate that memory-safe systems programming is not theoretical: production infrastructure is actively transitioning.

## Practical Migration Considerations

For organizations evaluating their supply chain exposure to memory safety vulnerabilities, several practical considerations apply:

**Prioritize based on exposure and criticality.** Not all C/C++ dependencies carry equal risk. Network-facing code, input parsers, cryptographic libraries, and privilege-critical components warrant more attention than internal utilities. Focus migration pressure on dependencies where memory safety vulnerabilities would be most consequential.

**Assess dependency migration trajectory.** Some C/C++ dependencies are actively adding Rust alternatives or components. Others have no migration path. Prefer dependencies with memory-safe evolution underway.

**Consider alternative implementations.** For some dependency categories, memory-safe alternatives already exist. A Rust HTTP library might replace a C library. A Go network service might replace a C++ implementation. Evaluate whether such substitutions are feasible.

**Support memory-safe rewrites.** The Prossimo project (Internet Security Research Group) funds memory-safe rewrites of critical infrastructure. Corporate sponsorship and contribution to these efforts accelerates ecosystem-wide safety improvements.

**Apply hardening to remaining C/C++ dependencies.** Where migration is not feasible, mitigation helps. Platform and compiler hardening (ASLR/PIE, stack canaries, control flow integrity), memory-safe standard library usage, and fuzzing reduce exploitation likelihood without eliminating underlying vulnerability risk.

## Challenges and Realistic Timelines

Despite compelling security benefits, memory-safe transition faces significant challenges:

**Expertise scarcity**: Rust, while growing rapidly, has a smaller developer community than C/C++. Finding developers with Rust expertise (particularly Rust expertise combined with domain knowledge in areas like operating systems, embedded systems, or cryptography) is difficult. Training existing developers takes time.

**Ecosystem maturity**: The Rust ecosystem, though developing rapidly, lacks equivalent libraries for every C/C++ use case. Some specialized domains have limited Rust tooling. Interoperability between Rust and existing C/C++ code adds complexity.

**Performance considerations**: While Rust matches C/C++ performance for most use cases, some highly optimized code may require careful porting. Certain low-level operations may be awkward to express in Rust's ownership model.

**Existing codebase scale**: Billions of lines of C/C++ code power global infrastructure. Complete replacement is a generational project. Even with aggressive investment, critical C/C++ code will remain in production for decades.

**Interoperability complexity**: Transitioning incrementally, rewriting modules while maintaining interfaces with existing code, introduces FFI (Foreign Function Interface) boundaries that can be error-prone and that can reduce some safety benefits.

These challenges argue against expecting rapid transformation. Memory-safe transition is a multi-decade endeavor, not a quick fix.

## Balanced Recommendations

Given both the security imperative and practical constraints, we recommend a balanced approach:

**1. Favor memory-safe dependencies for new development.** When selecting new dependencies, prefer memory-safe implementations where quality options exist. A Rust TLS library may be preferable to another OpenSSL binding.

**2. Track memory-safe evolution in critical dependencies.** Monitor whether key dependencies are adding Rust components or alternatives. Factor this into long-term dependency strategy.

**3. Support ecosystem transition.** Contribute to memory-safe rewrite efforts, whether through funding, code contribution, or adoption that validates new implementations.

**4. Apply defense in depth to remaining C/C++ code.** While waiting for memory-safe alternatives, use available hardening: modern compilers with security features enabled, fuzzing, static analysis, and careful code review.

**5. Set realistic expectations.** Memory-safe transition will take years to decades. Communicate timeline realistically to stakeholders. This is not a quick fix but a generational improvement.

**6. Evaluate supply chain exposure.** Understand which of your critical dependencies are written in memory-unsafe languages and assess the risk implications. This awareness informs both patching priority and long-term strategy.

The memory safety challenge illustrates a broader supply chain security principle: some risks cannot be eliminated through patching and monitoring alone. Fundamental improvements to the software development ecosystem (in this case, language evolution) can eliminate entire vulnerability classes in ways that addressing individual CVEs cannot. Supply chain security strategy should include awareness of and support for these foundational improvements alongside operational security measures.

![Memory safety and the 70% problem](img/ch-5-memory-safety.svg)

[nsa-memory-safety]: https://media.defense.gov/2022/Nov/10/2003112742/-1/-1/0/CSI_SOFTWARE_MEMORY_SAFETY.PDF
[cisa-memory-safety]: https://www.cisa.gov/resources-tools/resources/case-memory-safe-roadmaps
[^android-rust]: Google Security Blog, "Memory Safe Languages in Android 13" (December 1, 2022). <https://security.googleblog.com/2022/12/memory-safe-languages-in-android-13.html>
[^curl-installs]: Daniel Stenberg, "The world's biggest curl installations" (September 17, 2018). <https://daniel.haxx.se/blog/2018/09/17/the-worlds-biggest-curl-installations/>
[^msrc-memory-safety]: Microsoft Security Response Center, "A Proactive Approach to More Secure Code" (July 2019). <https://msrc.microsoft.com/blog/2019/07/a-proactive-approach-to-more-secure-code/>
[^chromium-memory-safety]: Chromium Project, "Memory Safety." <https://www.chromium.org/Home/chromium-security/memory-safety/>
