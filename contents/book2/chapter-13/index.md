# Chapter 13: Dependency Management Strategies

Chapter 13 provides a framework for managing software dependencies securely throughout their lifecycle. The chapter addresses the fundamental challenge that every dependency represents both a trust decision and an ongoing security commitment.

The chapter begins with dependency selection criteria, establishing a tiered evaluation process that assesses security, maintenance health, functionality fit, and licensing compatibility before adoption. Special attention is given to validating AI-suggested dependencies, which may be hallucinated or maliciously registered packages.

Version control strategies are examined in depth, covering the spectrum from exact version pinning to lockfiles to fully reproducible and hermetic builds. The chapter explains how lockfiles provide reproducibility and reduce supply chain exposure, while warning about potential attack vectors like lockfile injection.

A detailed comparison of vendoring versus dynamic dependency resolution helps organizations choose the right approach for their context. Vendoring offers complete control and build availability but increases maintenance burden, while dynamic resolution provides convenience at the cost of registry dependency. Hybrid approaches using artifact repository proxies offer a practical middle ground.

The chapter addresses the update paradox: updates are essential for security but can introduce instability. Automated tools like Dependabot and Renovate are compared, with guidance on update frequency, testing requirements, and rollback strategies.

Dependency minimalism is advocated as a security practice. The chapter critiques the micro-dependency anti-pattern and provides criteria for deciding when to build functionality in-house versus borrowing from external packages.

Finally, the chapter tackles legacy systems and technical debt, explaining how supply chain risk accumulates over time and offering strategies for managing end-of-life dependencies through replacement, forking, isolation, or risk acceptance with compensating controls.
