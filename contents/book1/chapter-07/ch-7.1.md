---
title: "Compromising Build Infrastructure"
description: "Understand why build systems are high-value targets and how attackers inject malicious code that never appears in source repositories."
icon: "lucide/server-crash"
---

# 7.1 Compromising Build Infrastructure

The journey from source code to deployed software passes through build systems—infrastructure that compiles, packages, signs, and prepares code for distribution. This transformation is invisible to most users, who reasonably assume that the software they install corresponds to the source code they can inspect. Attackers have recognized that this assumption creates opportunity. By compromising build infrastructure, adversaries can inject malicious code that never appears in any source repository, evading the code review, static analysis, and community oversight that open source development provides.

Build infrastructure compromise represents one of the highest-leverage attack vectors in the software supply chain. A single successful attack on a widely-used project's build system can affect millions of downstream users, as the SolarWinds, 3CX, and Codecov incidents—examined in subsequent sections—demonstrate.

## The Build System as a High-Value Target

Build systems occupy a uniquely privileged position in the software supply chain. They have access to:

- **Source code**: All code passing through the build, including proprietary and sensitive components
- **Credentials**: Signing keys, publishing tokens, cloud access credentials, and API secrets needed for deployment
- **Network access**: Often with elevated permissions to reach internal systems, registries, and distribution infrastructure
- **Trust**: Output from official build systems is assumed to be authentic

This combination makes build infrastructure extraordinarily attractive to attackers. A compromised build system can:

1. **Inject code into artifacts**: Add malicious functionality that never exists in version control
2. **Exfiltrate secrets**: Steal signing keys, deployment credentials, and other sensitive material
3. **Pivot to downstream systems**: Use build system access to compromise deployment infrastructure
4. **Maintain persistence**: Build-time modifications can be repeated with each build, surviving code updates

!!! danger "Exceptional Attack Leverage"

    Rather than compromising individual developer machines or attempting to sneak malicious commits past code review, an attacker who controls the build system affects every release produced by that system. The SolarWinds attack distributed malicious updates to [approximately 18,000 organizations][solarwinds-sec-filing].

The leverage is exceptional. Rather than compromising individual developer machines or attempting to sneak malicious commits past code review, an attacker who controls the build system affects every release produced by that system.

## The Gap Between Source and Binary

When you examine source code on GitHub or GitLab, you see what developers wrote and reviewers approved. When you install a binary package, you receive something different—the output of a build process that transformed source into executable form.

This transformation involves many steps:

- **Compilation**: Converting source code to machine code or bytecode
- **Transpilation**: Transforming modern language features to compatible formats
- **Bundling**: Combining modules into distributable packages
- **Minification**: Optimizing code size, often making it unreadable
- **Dependency resolution**: Fetching and incorporating external packages
- **Code generation**: Creating code from schemas, templates, or other sources
- **Signing**: Applying cryptographic signatures for integrity verification

Each step represents an opportunity for malicious modification. An attacker who controls any part of this pipeline can insert code that:

- Does not appear in any source repository
- Cannot be detected through source code review
- May be difficult to discover through binary analysis
- Appears legitimate because it carries official signatures

**Phantom dependencies** exemplify this risk. These are dependencies introduced during the build process that do not appear in declared dependency manifests. A malicious build script might:

```shell
# Legitimate-looking build script that fetches undeclared dependency
npm install --save-dev legitimate-looking-helper
```

The installed package executes its installation hooks, potentially compromising the build environment, but never appears in `package.json` or lockfiles that developers review.

## Attack Vectors Against Build Systems

Attackers compromise build infrastructure through multiple vectors:

**Compromised build servers** represent direct infrastructure attacks. Attackers gain access to build machines through:

- Exploiting vulnerabilities in CI/CD platform software
- Compromising credentials of accounts with CI/CD access
- Attacking the underlying infrastructure (cloud accounts, container registries)
- Social engineering operators responsible for build systems

Once inside, attackers can modify build scripts, inject code during compilation, or exfiltrate secrets. The persistence can be subtle—modifications that only activate under specific conditions, avoiding detection during testing.

**Malicious build scripts** introduce risk through the build configuration itself. Build definitions (`Jenkinsfile`, `.github/workflows/*.yml`, `.gitlab-ci.yml`) are code that executes with high privilege. An attacker who can modify these files—through compromised credentials, social engineering, or malicious pull requests—controls what happens during builds.

Build scripts can:

- Download and execute arbitrary code
- Modify source before compilation
- Replace dependencies with malicious versions
- Exfiltrate environment variables containing secrets
- Establish persistent access to build infrastructure

**Poisoned caches** exploit build optimization mechanisms. Modern CI/CD systems cache dependencies, build artifacts, and intermediate results to accelerate builds. If attackers can poison these caches—by compromising cache storage or exploiting cache key collisions—subsequent builds will incorporate malicious content.

The Codecov attack (detailed in Section 7.4) demonstrated this vector: attackers modified a script that was executed in thousands of CI environments, exfiltrating secrets from each.

**Compromised build dependencies** introduce malicious code through the tools used to build software rather than the software itself. Compilers, transpilers, bundlers, and other build tools are software too—software that runs with full access to everything being built.

Ken Thompson's classic paper ["Reflections on Trusting Trust"][thompson-trust] described this threat decades ago: a compromised compiler could insert backdoors into programs it compiles while appearing innocent when its own source code is examined. Modern build toolchains are far more complex than 1984-era compilers, with many more opportunities for supply chain attacks.

## Build Reproducibility and Security

!!! info "Reproducible Builds"

    Reproducible builds ensure anyone can independently verify that a binary was produced from its claimed source code. If builds are reproducible, rebuild from source and compare the result to the distributed binary.

**Reproducible builds** address the source-to-binary gap by ensuring that anyone can independently verify that a binary was produced from its claimed source code.

The concept is straightforward; implementation is challenging:

- Builds must eliminate non-deterministic elements (timestamps, file ordering, random numbers)
- Build environments must be precisely specified and recreatable
- All inputs (source, dependencies, tools) must be identified and fixed
- Verification infrastructure must be available and used

The [**Reproducible Builds project**][reproducible-builds] has made significant progress, particularly in the Debian ecosystem where [over 95% of packages are now reproducible][debian-reproducible]. However, adoption elsewhere remains limited:

- Most npm packages are not reproducibly built
- Many PyPI packages include non-reproducible elements
- Container images often incorporate non-deterministic operations
- Commercial software rarely prioritizes reproducibility

Without reproducibility, users cannot independently verify that binaries match source. They must trust build infrastructure—trust that sophisticated attackers target precisely because it is granted implicitly.

## CI/CD Platform Security Considerations

The choice of CI/CD platform and its configuration significantly affects build security.

**Cloud-hosted platforms** (GitHub Actions, GitLab CI, CircleCI, Azure Pipelines) offer:

*Advantages:*

- Managed security by specialized teams
- Isolation between customer builds
- Regular updates and security patching
- No infrastructure maintenance burden

*Risks:*

- Shared infrastructure with other customers
- Limited visibility into platform security
- Potential for platform-wide compromises
- Third-party action/orb/integration risks

**Self-hosted platforms** (Jenkins, self-hosted GitLab, BuildKite agents) offer:

*Advantages:*

- Complete control over infrastructure
- No shared-tenancy risks
- Custom security controls possible
- Full visibility into build environment

*Risks:*

- Security responsibility falls on the organization
- Maintenance and patching burden
- Often inadequate security investment
- May lack security expertise

Neither approach is inherently superior. Cloud platforms provide good default security but introduce platform trust. Self-hosted systems offer control but require expertise to secure properly.

**Common CI/CD security failures:**

- **Excessive secrets exposure**: Providing builds with more credentials than necessary
- **Insufficient isolation**: Allowing builds to affect each other or persist changes
- **Uncontrolled third-party integrations**: Using community actions without review
- **Missing audit logging**: Inability to detect or investigate compromises
- **Inadequate access controls**: Too many accounts with administrative privileges

## The SLSA Framework and Build Integrity

The [**Supply chain Levels for Software Artifacts (SLSA)**][slsa] framework, developed by Google and now maintained by the [OpenSSF][openssf], provides a graduated approach to build integrity.

SLSA (v1.0) defines four levels of increasing assurance (L0-L3):

**Level 0**: No provenance guarantees. The baseline state for most software today.

**Level 1**: Provenance exists and follows the SLSA format. Builds produce metadata about how software was built, but provenance may be unsigned.

**Level 2**: Hosted build platform with signed provenance. Builds run on managed infrastructure rather than developer machines, and provenance is cryptographically signed.

**Level 3**: Hardened build platform with non-falsifiable provenance. Build definitions come from version control, builds are isolated, and provenance cannot be forged by the project maintainers.

Each level addresses specific threats:

| Threat | SLSA 1 | SLSA 2 | SLSA 3 |
|--------|--------|--------|--------|
| Build compromise | ✗ | ✓ | ✓ |
| Provenance forgery | ✗ | ✗ | ✓ |
| Modified source (build script) | ✗ | ✗ | ✓ |

Currently, most software achieves SLSA Level 0 (no provenance) or Level 1 (basic provenance). Reaching Level 3 requires significant investment but provides meaningful protection against build infrastructure attacks. Best practices like reproducible builds and hermetic builds, while not required for any specific SLSA level, strengthen Level 3 compliance and enable independent verification.

GitHub, npm, and PyPI have implemented provenance features aligned with SLSA, enabling packages to include verifiable build provenance. Adoption is growing but remains a minority practice.

## Supply Chain Attacks on Build Tools

Build tools themselves are software, subject to the same supply chain risks as any other software. Attackers have targeted:

**Compilers and language toolchains**: A compromised compiler can inject vulnerabilities into everything it compiles. While Thompson's theoretical attack has not been widely observed in practice, the principle remains valid. The Rust compiler, for instance, is built from a chain of prior Rust compilers—a bootstrap process that must be trusted.

**Package managers**: npm, pip, cargo, and other package managers execute during builds with significant privileges. Compromising these tools affects every build that uses them.

**Bundlers and build systems**: Webpack, Rollup, Gradle, Maven, and similar tools process code and dependencies. Malicious plugins or compromised core tools can inject arbitrary modifications.

**Transpilers and code generators**: TypeScript, Babel, protobuf compilers, and other code generators transform source before compilation. Malicious transformations would be difficult to detect in output.

**Container base images**: Builds using containers inherit whatever exists in base images. Compromised base images affect all builds that use them. The use of unverified or outdated base images is common in CI/CD configurations.

Organizations should inventory the tools involved in their build processes and apply supply chain security practices to these tools, not just to the code being built.

## Connection to Case Studies

The following sections examine specific incidents that illustrate build infrastructure compromise:

- **SolarWinds (Section 7.2)**: Demonstrated nation-state compromise of a commercial build system, injecting malware that reached 18,000 organizations
- **3CX (Section 7.3)**: Showed cascading supply chain compromise where one attacked build system was used to compromise another
- **Codecov (Section 7.4)**: Illustrated how a single compromised script executed in thousands of CI environments could exfiltrate secrets at scale
- **XZ Utils (Section 7.5)**: Revealed sophisticated build-time manipulation where malicious code was hidden in test files and activated only during specific build conditions

Each case study reinforces the central lesson: build infrastructure is a high-value target that requires security investment proportional to its risk. Organizations that treat CI/CD as "just plumbing" rather than security-critical infrastructure leave themselves vulnerable to attacks that bypass all their source code security efforts.

[solarwinds-sec-filing]: https://www.sec.gov/ix?doc=/Archives/edgar/data/1739942/000173994220000075/swi-20201231.htm
[thompson-trust]: https://www.cs.cmu.edu/~rdriley/487/papers/Thompson_1984_ReflectionsonTrustingTrust.pdf
[reproducible-builds]: https://reproducible-builds.org/
[debian-reproducible]: https://tests.reproducible-builds.org/debian/reproducible.html
[slsa]: https://slsa.dev/
[openssf]: https://openssf.org/
