## Appendix A: Glossary of Terms

This glossary provides definitions for key terms used throughout this book. Terms are organized alphabetically, with cross-references to related concepts indicated in *italics*.

---

### A {.unlisted .unnumbered}

**Account hijacking**: An attack in which an adversary gains unauthorized access to a legitimate user's credentials on a source code repository, package registry, or other software development platform. Once compromised, attackers can publish malicious code, modify existing packages, or exfiltrate sensitive information. Account hijacking is frequently mitigated through multi-factor authentication and the use of scoped access tokens. *See also: Multi-factor authentication (MFA), Trusted publishing.*

**Address Space Layout Randomization (ASLR)**: A memory protection technique that randomizes the locations where system executables, libraries, and other components are loaded into memory. ASLR makes it more difficult for attackers to exploit memory corruption vulnerabilities by preventing them from reliably predicting target memory addresses. *See also: Binary hardening, Control Flow Integrity (CFI).*

**Artifact**: Any file or set of files produced during the software development and build process, including compiled binaries, container images, packages, and documentation. In supply chain security, verifying the integrity and provenance of artifacts is essential to ensuring that consumers receive authentic, untampered software.

**Attack surface**: The sum of all points where an unauthorized user could attempt to enter or extract data from a system. In software supply chain security, attack surface includes dependencies, build systems, distribution channels, and any external inputs the software accepts. Reducing attack surface is a fundamental security principle.

**Attestation**: A cryptographically signed statement that asserts specific claims about a software artifact, such as how it was built, what inputs were used, or what security checks it passed. Attestations provide verifiable evidence that certain procedures were followed during the software development lifecycle. In the SLSA framework, attestations are used to document build provenance and verify supply chain integrity. *See also: Build provenance, in-toto, SLSA.*

---

### B {.unlisted .unnumbered}

**Binary hardening**: A collection of techniques applied during compilation or post-compilation to make compiled software more resistant to exploitation. Common hardening measures include enabling stack canaries, position-independent executables (PIE), ASLR support, and Control Flow Integrity. Binary hardening represents a defense-in-depth approach that reduces the likelihood that vulnerabilities will be successfully exploited. *See also: Address Space Layout Randomization (ASLR), Control Flow Integrity (CFI).*

**Bug bounty**: A program offered by organizations that provides financial rewards to security researchers who responsibly disclose vulnerabilities. Bug bounty programs incentivize security research and help organizations identify and fix vulnerabilities before they can be exploited by malicious actors. Some programs include patch bounties that reward researchers for submitting working fixes alongside vulnerability reports.

**Build provenance**: Metadata that describes how a software artifact was produced, including the source code location, build system used, builder identity, build parameters, and input dependencies. Build provenance enables consumers to verify that an artifact was built from expected sources using expected processes. The SLSA framework defines specific requirements for build provenance at different security levels. *See also: Attestation, Hermetic build, SLSA.*

---

### C {.unlisted .unnumbered}

**CI/CD (Continuous Integration/Continuous Delivery)**: A software development practice that automates the building, testing, and deployment of code changes. CI/CD pipelines are critical infrastructure in modern software development but also represent a significant attack surface. Securing CI/CD systems involves protecting secrets, validating inputs, and ensuring the integrity of the build environment. *See also: Trusted publishing, Hermetic build.*

**CNA (CVE Numbering Authority)**: An organization authorized by the CVE Program to assign CVE identifiers to vulnerabilities within a defined scope. CNAs include software vendors, open source projects, bug bounty programs, and national and industry CERTs. As of 2024, there are over 300 CNAs worldwide. The CNA structure enables distributed vulnerability coordination while maintaining a centralized identification system. *See also: CVE, NVD.*

**Code signing**: The practice of digitally signing software artifacts to verify the identity of the publisher and ensure the code has not been modified since signing. Code signing uses public key cryptography to create signatures that can be verified by consumers. While code signing provides authenticity guarantees, it does not guarantee that the code is free of vulnerabilities or malicious functionality. *See also: Sigstore, Attestation.*

**Common Vulnerability Scoring System (CVSS)**: A standardized framework for rating the severity of security vulnerabilities. CVSS provides a numerical score (0.0 to 10.0) based on factors including attack vector, complexity, required privileges, and potential impact. CVSS scores are commonly used to prioritize vulnerability remediation, with scores of 9.0-10.0 considered "Critical." *See also: CVE, NVD.*

**Common Weakness Enumeration (CWE)**: A community-developed catalog of software and hardware weakness types. CWE provides a standardized language for describing security weaknesses and serves as a baseline for weakness identification, mitigation, and prevention. Each CWE entry includes a description, potential consequences, and recommended mitigations. *See also: CVE, Static analysis.*

**Control Flow Integrity (CFI)**: A security mechanism that prevents attackers from redirecting program execution to arbitrary code by enforcing that control flow transfers (such as function calls and returns) follow a predetermined control flow graph. CFI is particularly effective against return-oriented programming (ROP) and similar code-reuse attacks. *See also: Binary hardening, Address Space Layout Randomization (ASLR).*

**Coordinated vulnerability disclosure**: A process in which a security researcher privately reports a vulnerability to the affected vendor or maintainer, allowing them time to develop and release a fix before public disclosure. This approach balances the public's right to know about security issues with the need to protect users from exploitation during the remediation period. *See also: Responsible disclosure, Zero-day vulnerability.*

**CVE (Common Vulnerabilities and Exposures)**: A standardized identifier system for publicly known cybersecurity vulnerabilities. Each CVE ID (e.g., CVE-2021-44228) uniquely identifies a specific vulnerability, enabling consistent communication across security tools, databases, and organizations. CVEs are assigned by CNAs and cataloged in the National Vulnerability Database. *See also: CNA, CVSS, NVD.*

---

### D {.unlisted .unnumbered}

**DAST (Dynamic Application Security Testing)**: A security testing methodology that analyzes applications while they are running to identify vulnerabilities. DAST tools probe applications from the outside, simulating attacks to discover issues such as injection vulnerabilities, authentication flaws, and configuration errors. DAST complements static analysis by finding runtime-specific vulnerabilities. *See also: IAST, SAST, Fuzzing.*

**Dependency**: A software component that another component requires to function. Dependencies can be direct (explicitly declared by the developer) or transitive (required by direct dependencies). Managing dependencies securely is a fundamental challenge in software supply chain security. *See also: Transitive dependency, Dependency confusion, Lockfile.*

**Dependency confusion**: A supply chain attack that exploits package manager behavior to trick build systems into downloading malicious packages from public repositories instead of intended private packages. The attack works when an attacker publishes a package to a public registry with the same name as an internal package, often with a higher version number. First publicly demonstrated by Alex Birsan in 2021, dependency confusion attacks have affected major technology companies. *See also: Typosquatting, Malicious package.*

**Dependency management**: The practice of tracking, updating, and securing the external software components that a project relies upon. Effective dependency management includes maintaining accurate dependency manifests, regularly updating to patched versions, and monitoring for known vulnerabilities. *See also: Lockfile, Software Composition Analysis (SCA), Vendoring.*

**DevSecOps**: An approach to software development that integrates security practices throughout the development lifecycle rather than treating security as a separate phase. DevSecOps emphasizes automation, collaboration between development, security, and operations teams, and the principle of "shifting security left" to identify issues earlier in development.

---

### F {.unlisted .unnumbered}

**Fuzzing**: An automated software testing technique that provides invalid, unexpected, or random data as inputs to a program to discover bugs and security vulnerabilities. Fuzzers monitor program behavior for crashes, assertion failures, memory leaks, and other anomalies. Modern fuzzing approaches include coverage-guided fuzzing (e.g., AFL, libFuzzer) and structure-aware fuzzing. Google's OSS-Fuzz project provides continuous fuzzing for critical open source projects. *See also: DAST, OSS-Fuzz.*

---

### H {.unlisted .unnumbered}

**Hermetic build**: A build process that is isolated from the host environment and produces the same output regardless of when or where it is executed. Hermetic builds achieve reproducibility by explicitly declaring all inputs (source code, dependencies, tools, and environment) and preventing access to external resources during the build. Hermetic builds support stronger SLSA-aligned build assurances. *See also: Reproducible build, Build provenance, SLSA.*

**Homoglyph attack**: An attack that uses visually similar characters from different character sets to create deceptive text. In the context of software supply chain security, homoglyph attacks can be used to create package names that appear identical to legitimate packages but contain different Unicode characters. For example, using the Cyrillic "а" (U+0430) instead of the Latin "a" (U+0061). *See also: Typosquatting, Malicious package.*

---

### I {.unlisted .unnumbered}

**IAST (Interactive Application Security Testing)**: A security testing approach that combines elements of static and dynamic analysis by instrumenting applications during testing to monitor internal behavior. IAST tools can identify vulnerabilities with lower false positive rates than traditional SAST or DAST by observing actual data flows during execution. *See also: DAST, SAST.*

**in-toto**: A framework for securing the integrity of software supply chains by generating and verifying metadata about each step in the development and deployment process. in-toto uses cryptographically signed attestations called "link metadata" to create an auditable record of the supply chain. The framework was developed at NYU and is now a Cloud Native Computing Foundation project. *See also: Attestation, Build provenance, SLSA.*

---

### L {.unlisted .unnumbered}

**Lockfile**: A file that records the exact versions (and often cryptographic hashes) of all dependencies resolved for a project at a specific point in time. Lockfiles ensure reproducible installations by preventing automatic upgrades to newer versions and enabling verification that downloaded packages match expected content. Examples include `package-lock.json` (npm), `Pipfile.lock` (Python), and `Cargo.lock` (Rust). *See also: Dependency management, Reproducible build.*

---

### M {.unlisted .unnumbered}

**Malicious package**: A software package that intentionally contains harmful functionality such as data exfiltration, cryptocurrency mining, backdoors, or destructive payloads. Malicious packages may be published under names designed to deceive (typosquatting), may compromise legitimate packages through account hijacking, or may be introduced by malicious maintainers. Package registries employ automated scanning and community reporting to detect and remove malicious packages. *See also: Typosquatting, Account hijacking, Protestware.*

**Memory safety**: A property of programming languages or runtime environments that prevents programs from accessing memory in unsafe ways, such as buffer overflows, use-after-free errors, and null pointer dereferences. Memory safety issues are a leading cause of security vulnerabilities in systems software. Languages like Rust, Go, and Java provide memory safety guarantees, while C and C++ require careful programming practices to avoid memory safety vulnerabilities.

**Multi-factor authentication (MFA)**: A security mechanism that requires users to provide two or more verification factors to gain access to a resource. MFA significantly reduces the risk of account hijacking by ensuring that compromised passwords alone are insufficient for unauthorized access. Hardware security keys (phishing-resistant) and time-based one-time passwords (TOTP) are preferred over SMS-based methods due to their resistance to SIM-swapping attacks. Often implemented as two-factor authentication (2FA). *See also: Account hijacking, Trusted publishing.*

---

### N {.unlisted .unnumbered}

**National Vulnerability Database (NVD)**: A U.S. government repository of standards-based vulnerability management data maintained by NIST. The NVD catalogs CVE entries with additional analysis including CVSS severity scores, CWE classifications, and affected product information (CPE). The NVD serves as a primary reference for vulnerability data used by security tools and organizations worldwide. *See also: CVE, CVSS, CWE.*

**NHI (Non-Human Identity)**: A digital identity used by software systems, services, or automated processes rather than human users. NHIs include service accounts, API keys, OAuth tokens, CI/CD credentials, and machine identities. Securing NHIs is critical in software supply chains because compromised NHIs can enable attackers to publish malicious packages, access source code repositories, or manipulate build systems. *See also: Secret management, Trusted publishing.*

---

### O {.unlisted .unnumbered}

**OSS-Fuzz**: Google's continuous fuzzing service for open source software. OSS-Fuzz runs fuzzing tests against critical open source projects 24/7, automatically reporting discovered bugs to maintainers. As of 2024, OSS-Fuzz has found over 10,000 vulnerabilities and 36,000 bugs across 1,000+ open source projects. *See also: Fuzzing.*

---

### P {.unlisted .unnumbered}

**Package management system**: A collection of software tools that automates the process of installing, upgrading, configuring, and removing software packages. Package managers maintain databases of available packages, resolve dependencies, and handle versioning. Major package managers include npm (JavaScript), PyPI/pip (Python), Maven Central (Java), NuGet (.NET), and RubyGems (Ruby). *See also: Package registry, Dependency management.*

**Package registry**: A centralized repository that hosts and distributes software packages. Package registries provide discovery, download, and often authentication and access control services. Examples include npmjs.com, PyPI.org, crates.io, and Docker Hub. Registries are critical infrastructure in software supply chains and high-value targets for attackers. *See also: Package management system, Trusted publishing.*

**Patch bounty**: A bug bounty program that provides rewards for security researchers who submit working patches to fix vulnerabilities, not just vulnerability reports. Patch bounties incentivize researchers to contribute remediation work and can accelerate the time to fix, particularly for open source projects with limited maintainer resources. Google's Patch Rewards program pioneered this approach.

**Protestware**: Software that has been intentionally modified by its maintainer to include functionality that protests or makes a political statement, often in ways that harm users. Unlike traditional malware, protestware originates from legitimate maintainers rather than external attackers. Notable examples include the `node-ipc` incident in March 2022, where the maintainer added code targeting users with Russian or Belarusian IP addresses. Protestware represents a unique supply chain threat because it exploits the trust relationship between maintainers and users. *See also: Malicious package.*

**Provenance**: Information about the origin and history of a software artifact, including its source, how it was built, and its chain of custody. Provenance information enables consumers to make trust decisions about software and detect tampering or substitution. *See also: Build provenance, Attestation, SLSA.*

---

### R {.unlisted .unnumbered}

**RASP (Runtime Application Self-Protection)**: A security technology that runs within an application to detect and prevent attacks in real-time. RASP solutions instrument applications to monitor behavior and can block malicious activities such as SQL injection, command injection, and authentication bypass attempts. *See also: DAST, IAST.*

**Reproducible build**: A build process that produces bit-for-bit identical outputs when given the same inputs, regardless of the build environment or time. Reproducible builds enable independent verification that a distributed binary corresponds to its purported source code, helping detect tampering or compromise in the build process. The Reproducible Builds project maintains tools and best practices for achieving reproducibility across different platforms. *See also: Hermetic build, Build provenance.*

**Responsible disclosure**: The ethical practice of privately reporting security vulnerabilities to affected parties before public disclosure, allowing time for remediation. Responsible disclosure timelines typically range from 30 to 90 days, after which researchers may publish details regardless of patch availability. *See also: Coordinated vulnerability disclosure, Zero-day vulnerability.*

---

### S {.unlisted .unnumbered}

**SAST (Static Application Security Testing)**: Security testing that analyzes source code, bytecode, or binary code without executing the program. SAST tools identify potential vulnerabilities by examining code structure, data flows, and patterns associated with known vulnerability types. SAST can find issues early in development but may produce false positives and cannot detect runtime-specific vulnerabilities. Also referred to as static analysis. *See also: DAST, IAST, Software Composition Analysis (SCA).*

**SBOM (Software Bill of Materials)**: A formal, machine-readable inventory of all components, libraries, and dependencies that comprise a piece of software. SBOMs enable organizations to track what software they are using, identify affected systems when vulnerabilities are disclosed, and comply with regulatory requirements. Major SBOM formats include SPDX (ISO/IEC 5962:2021) and CycloneDX. The U.S. Executive Order 14028 (2021) mandated SBOM requirements for software sold to the federal government. *See also: Software Composition Analysis (SCA), Dependency management.*

**Secret management**: The practice of securely storing, accessing, rotating, and auditing credentials, API keys, certificates, and other sensitive data used in software systems. Proper secret management prevents accidental exposure of credentials in source code repositories, logs, or published packages. Secret management solutions include HashiCorp Vault, AWS Secrets Manager, and Azure Key Vault. *See also: NHI (Non-Human Identity).*

**Security audit**: A systematic examination of software to identify security vulnerabilities, typically performed by independent security professionals. Security audits may include source code review, penetration testing, and architecture analysis. Audit reports provide documented evidence of security evaluation and often include remediation recommendations. Organizations such as OSTIF coordinate security audits for critical open source projects. *See also: Threat modeling.*

**Security Engineer in Residence (SEIR)**: A grant-funded security engineer placed inside an open source ecosystem organization, such as a language foundation or registry operator, to validate vulnerability findings before they reach maintainers, help develop and coordinate fixes, and absorb surges of AI-generated reports. Alpha-Omega began funding SEIRs in May 2026. Unlike an auditor, a SEIR works for the ecosystem and does not take over maintainer decisions. *See also: Security audit.*

**Sigstore (Fulcio/Rekor)**: The industry-standard project that provides free code signing and verification infrastructure for the open source community through keyless signing and transparency logs. Sigstore includes Fulcio (a certificate authority for issuing short-lived code signing certificates), Rekor (an immutable transparency log that records signing events), and Cosign (a signing tool). By eliminating the need for developers to manage their own long-lived signing keys, Sigstore significantly reduces barriers to code signing adoption. Sigstore is a Linux Foundation project with support from Google, Red Hat, and other organizations. *See also: Code signing, Attestation, Trusted publishing.*

**Slopsquatting**: A supply chain attack that exploits AI code assistants' tendency to hallucinate non-existent package names. When an AI assistant suggests a dependency that doesn't exist, attackers can register that package name and publish malicious code, which will then be installed by developers following the AI's recommendation. The term combines "slop" (AI-generated content) with "squatting." This attack vector emerged as a significant concern with the widespread adoption of AI coding assistants in 2023-2024. *See also: Typosquatting, Dependency confusion, Malicious package.*

**SLSA (Supply-chain Levels for Software Artifacts)**: A security framework that defines graduated requirements for increasing supply chain integrity guarantees. Pronounced "salsa," SLSA provides standards and controls to prevent tampering, improve artifact integrity, and secure the build process; the current specification organizes requirements into tracks such as the Build Track. SLSA is a project of the Open Source Security Foundation (OpenSSF). *See also: Build provenance, Attestation, Hermetic build.*

**Software Composition Analysis (SCA)**: Tools and processes that identify open source and third-party components in a codebase, catalog their versions, and detect known vulnerabilities. SCA tools typically maintain databases of component-vulnerability mappings and integrate with development workflows to alert teams when vulnerable components are detected. *See also: SBOM, Dependency management.*

**Software supply chain**: The complete set of components, processes, tools, and people involved in developing, building, and distributing software. This includes source code repositories, dependencies, build systems, CI/CD pipelines, package registries, and distribution channels. Supply chain security focuses on protecting all these elements from compromise.

**Static analysis**: See SAST (Static Application Security Testing).

**Subresource Integrity (SRI)**: A web security feature that enables browsers to verify that resources fetched from CDNs or other external sources have not been manipulated. SRI uses cryptographic hashes in HTML attributes to ensure that scripts, stylesheets, and other resources match expected content. SRI prevents attackers who compromise CDNs from injecting malicious code into websites.

**Supply chain attack**: An attack that targets the software supply chain rather than the end product directly. Supply chain attacks may compromise source code repositories, build systems, package registries, or update mechanisms to inject malicious code that is then distributed to downstream consumers. Notable supply chain attacks include the SolarWinds compromise (2020) and the Codecov breach (2021).

---

### T {.unlisted .unnumbered}

**Threat modeling**: A structured process for identifying potential security threats, vulnerabilities, and attack vectors affecting a system. Threat modeling typically involves creating diagrams of system architecture, identifying trust boundaries, enumerating potential threats using frameworks like STRIDE, and prioritizing risks for mitigation. Threat models should be updated as systems evolve. *See also: Attack surface, Security audit.*

**Transitive dependency**: A dependency that is not directly declared by a project but is required by one of its direct dependencies. Transitive dependencies can create deep dependency trees, and vulnerabilities in transitive dependencies affect all downstream projects. Managing transitive dependency security requires tools that can traverse the full dependency graph. *See also: Dependency, Dependency management, Lockfile.*

**Trusted publishing**: A mechanism that allows package maintainers to publish packages without using long-lived API tokens or passwords. Instead, packages are published directly from CI/CD workflows using short-lived, automatically provisioned credentials tied to verified identities. Trusted publishing implementations are available on PyPI (via OpenID Connect with GitHub Actions) and other registries. *See also: Sigstore, NHI (Non-Human Identity), CI/CD.*

**Typosquatting**: An attack that registers package names similar to popular packages, relying on developers making typographical errors when installing dependencies. Typosquatting exploits character substitutions (e.g., `requets` for `requests`), transpositions (e.g., `lodahs` for `lodash`), and omissions or additions (e.g., `colros` for `colors`). Package registries increasingly implement typosquatting detection, but the attack remains prevalent. *See also: Homoglyph attack, Dependency confusion, Slopsquatting.*

---

### V {.unlisted .unnumbered}

**Vendoring**: The practice of copying dependency source code directly into a project's repository rather than fetching it from a package registry at build time. Vendoring provides protection against dependency availability issues and supply chain attacks but increases maintenance burden and may complicate license compliance. Vendoring is common in Go projects and is sometimes used for critical dependencies in other ecosystems. *See also: Dependency management, Lockfile.*

**Vibe Coding**: A development paradigm enabled by AI coding assistants where developers provide broad, high-level prompts to AI models to generate code, prioritizing rapid development speed over deep technical understanding or rigorous security verification. Vibe coding practitioners may accept AI-generated code with minimal review, trusting the AI's recommendations without verifying dependencies, security implications, or correctness. This approach introduces supply chain risks, particularly vulnerability to slopsquatting attacks when AI assistants hallucinate non-existent package names. *See also: Slopsquatting.*

**Vulnerability**: A weakness in software that can be exploited to compromise confidentiality, integrity, or availability. Vulnerabilities may result from design flaws, implementation errors, or configuration mistakes. The severity of a vulnerability depends on factors including ease of exploitation and potential impact. *See also: CVE, CVSS, Zero-day vulnerability.*

---

### Z {.unlisted .unnumbered}

**Zero-day vulnerability**: A vulnerability that is unknown to the parties responsible for patching or otherwise fixing the flaw. The term "zero-day" refers to the fact that developers have had zero days to address the vulnerability before it may be exploited. Zero-day vulnerabilities are particularly dangerous because no patches or mitigations are available. Once a zero-day is publicly disclosed, it becomes an "n-day" vulnerability. *See also: CVE, Responsible disclosure, Coordinated vulnerability disclosure.*

---

*This glossary covers primary terms used in this book. For emerging terminology and updates, consult the OpenSSF Glossary (https://openssf.org) and NIST Computer Security Resource Center (https://csrc.nist.gov).*
