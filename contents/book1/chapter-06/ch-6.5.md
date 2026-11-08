---
title: "Advanced Package Attack Techniques"
description: "Explore sophisticated attacks like star-jacking that evade detection by exploiting subtle package ecosystem mechanics."
icon: "lucide/zap"
---

# 6.5 Advanced Package Attack Techniques

The attacks described in previous sections—typosquatting, dependency confusion, straightforward malicious packages—represent the most common supply chain threats. However, attackers continuously develop more sophisticated techniques that evade detection and exploit subtle aspects of package ecosystem mechanics. Understanding these advanced techniques is essential for security teams conducting threat assessments and for researchers working to improve ecosystem defenses.

These techniques often combine multiple deceptive elements, exploit trust signals that developers rely on, or leverage implementation details of package managers that create unexpected security implications.

## Star-Jacking: Manufacturing Credibility

When developers evaluate packages, they often check GitHub star counts as a rough indicator of popularity and community trust. A package with 10,000 stars appears more credible than one with 10 stars. **Star-jacking** exploits this heuristic by associating a malicious package with a legitimate repository's star count.

The technique works because package registries often allow publishers to specify an arbitrary repository URL in package metadata. The registry may then display that repository's star count alongside the package—even if the package has no actual relationship to that repository.

**Mechanism:**

1. Attacker creates a malicious package on npm, PyPI, or another registry
2. Attacker sets the package's repository field to point to a popular, legitimate project (e.g., `facebook/react`, `tensorflow/tensorflow`)
3. Registry displays the legitimate project's star count on the malicious package page
4. Developers seeing thousands of stars assume the package is widely trusted

[Research by Checkmarx in 2023][checkmarx-starjacking] documented widespread star-jacking on PyPI, finding hundreds of packages claiming association with repositories they had no connection to. Some malicious packages displayed star counts exceeding 100,000 by linking to extremely popular projects.

**Detection challenges:**

- Registries would need to verify that repository maintainers have authorized the package association
- Legitimate forks and derivative works may reasonably reference parent projects
- Verification adds friction to the publication process

**Defense:** Developers should verify package claims by visiting the linked repository and confirming it references the package. Discrepancies between repository content and package content indicate potential deception.

## Contribution Fraud: Building Fake Reputation

Beyond star-jacking, attackers can manufacture apparent legitimacy through **contribution fraud**—creating fake contributor activity to make a package or maintainer appear established and trustworthy.

**Techniques include:**

- **Fake commit history**: Generating commits with backdated timestamps to make a repository appear older and more actively maintained
- **Sock puppet contributors**: Creating multiple GitHub accounts that appear to contribute to a project, simulating community involvement
- **Imported contribution graphs**: Forking a repository, making superficial changes, and claiming the fork's history as evidence of long-term work
- **Purchased accounts**: Using aged GitHub accounts purchased from underground markets, with existing contribution history

The [XZ Utils attack][xz-utils-backdoor] demonstrated sophisticated identity construction: the "Jia Tan" persona built a credible-appearing identity over two years, contributing to multiple projects and engaging in normal open source behavior before introducing malicious code.

**Detection challenges:**

- Distinguishing genuine from fraudulent contribution history requires deep analysis
- Aged accounts with legitimate-appearing history are difficult to flag
- Behavioral analysis at scale is resource-intensive

**Defense:** For critical dependencies, examine not just quantity of contributions but quality and coherence. Investigate whether contributors have verifiable external identities. Consider whether the project's apparent history matches its functionality and complexity.

## Manifest Confusion: Metadata vs. Reality

Package managers rely on manifest files (`package.json`, `setup.py`, `Cargo.toml`) to describe package contents, dependencies, and scripts. **Manifest confusion** attacks exploit discrepancies between what manifests declare and what packages actually contain.

**Variants include:**

**Hidden dependencies**: The manifest declares one set of dependencies, but installation scripts fetch additional packages not listed. Security scanners examining only the manifest miss these hidden dependencies.

**Script discrepancies**: The manifest's `scripts` field may show benign installation hooks, while the actual script files contain different (malicious) code. Some registries validate manifest fields but not file contents.

**Version field manipulation**: A package might declare version `1.0.0` in its manifest but contain code from a different (compromised) version.

**Hidden files**: Including files in the published package that are not present in the source repository. Developers reviewing the GitHub repository see clean code, but the published package contains additional malicious files.

Attackers can exploit this gap by publishing tarballs that contain files absent from the linked source repository. Without comparing repository contents to published package contents, these additions remain invisible to reviewers who inspect only the source repository.

**Detection challenges:**

- Comparing published packages to source repositories requires infrastructure and defined processes
- Manifest parsing must be implemented identically to how package managers interpret them
- Differences may be subtle and require detailed analysis

**Defense:** Tools like [Socket][socket] analyze published package contents rather than relying solely on manifest declarations. Reproducible builds enable verification that published artifacts match source repositories.

## Lockfile Injection Attacks

!!! danger "The Security Feature Turned Attack Vector"

    Lockfiles are designed to ensure reproducible installations, but attackers can inject malicious package references via pull requests. Reviewers focus on code changes, overlooking lockfile modifications—and CI/CD pipelines strictly follow lockfile specifications.

**Lockfiles** (`package-lock.json`, `yarn.lock`, `poetry.lock`, `Gemfile.lock`) are designed to ensure reproducible installations by specifying exact versions and sources for all dependencies. Ironically, this security feature can be turned into an attack vector through **lockfile injection**.

**Attack mechanism:**

1. Attacker submits a pull request to a target repository
2. The pull request includes modifications to the lockfile
3. These modifications point to attacker-controlled packages or altered versions
4. Reviewers focus on code changes, overlooking lockfile modifications
5. When the PR is merged, subsequent installations fetch malicious packages

The attack is particularly effective because:

- Lockfiles are often large and difficult to review manually
- Developers may assume lockfiles are auto-generated and trustworthy
- CI/CD pipelines commonly run `npm ci` or equivalent, which strictly follows lockfile specifications
- Code review tools may collapse or minimize lockfile changes

[Research by Snyk][snyk-lockfile-injection] demonstrated lockfile injection against npm, showing how modified `package-lock.json` files could redirect dependency resolution to malicious packages while leaving `package.json` untouched.

**Variants:**

- **Integrity hash manipulation**: Changing the expected hash of a package to match a malicious version
- **Registry URL injection**: Changing the resolved registry URL to point to an attacker-controlled server
- **Transitive dependency substitution**: Modifying deep transitive dependency entries that reviewers are unlikely to examine

**Detection challenges:**

- Lockfile diffs can be thousands of lines long
- Legitimate dependency updates also modify lockfiles extensively
- Subtle changes (single character in a hash) are easy to miss

**Defense:** Treat lockfile changes with the same scrutiny as code changes. Use tooling that validates lockfile integrity against expected sources. Consider policies requiring lockfile regeneration rather than manual editing.

## Namespace Shadowing

Namespace shadowing extends beyond basic dependency confusion to exploit complex namespace resolution across registries, scopes, and organizations.

**Techniques include:**

**Cross-registry shadowing**: A package name exists on multiple registries (npm and GitHub Package Registry, for example). Build systems configured to check multiple registries may resolve to an unintended source depending on configuration order or availability.

**Organization scope confusion**: npm scopes like `@angular/core` associate packages with organizations. Attackers may register similar-appearing scopes (`@angu1ar`, `@angular-dev`) to exploit visual confusion or typos.

**Nested namespace exploitation**: Some ecosystems allow complex namespacing. Attackers may claim names that appear to be subpackages or plugins of legitimate projects.

**Private scope leakage**: Organizations may use internal scopes that are not registered on public registries. Attackers can register those scopes publicly, potentially capturing packages if configuration errors cause public registry checks.

**Detection challenges:**

- Namespace rules vary across ecosystems
- Visual similarity detection must account for Unicode and typography
- Private namespace discovery requires intelligence about target organizations

**Defense:** Register your organization's namespaces on public registries defensively. Use scoped packages consistently and configure package managers to route scopes to specific registries.

## Optional Dependency Claiming

Package managers support **optional dependencies**—packages that enhance functionality but are not required for core operation. If an optional dependency is unavailable, installation continues without error. This graceful degradation creates an attack vector: **optional dependency claiming**, where attackers register packages that are declared as optional dependencies but do not actually exist on public registries.

This attack differs from standard dependency confusion. Traditional dependency confusion exploits private/public namespace collisions requiring misconfigured registries. Optional dependency claiming requires only that a legitimate package references a nonexistent optional dependency—no private registry involvement needed.

**Ecosystem mechanics:**

**npm** supports `optionalDependencies` in `package.json`. If these packages cannot be found or fail to install, npm continues without error. Similarly, `peerDependencies` may reference packages the consuming project is expected to provide—if those packages do not exist, nothing prevents an attacker from registering them.

**Python** uses optional extras through `extras_require` in `setup.py` or `[project.optional-dependencies]` in `pyproject.toml`. When users install with syntax like `pip install package[extra]`, pip attempts to fetch the extra dependencies. If an extra references a nonexistent package, an attacker who registers that name captures installations from anyone using that extra.

**Attack mechanism:**

1. Attacker scans published packages for optional dependency declarations
2. Attacker checks whether declared optional packages exist on the public registry
3. For nonexistent optional dependencies, attacker registers the package name
4. Users installing with the optional extra receive the attacker's package, which executes installation hooks or provides malicious functionality

[Research by Orca Security][orca-dependency-confusion] found that 49% of organizations have at least one dependency on a package that does not exist on public repositories, creating substantial attack surface for this technique.

**Detection challenges:**

- Optional dependencies are intentionally allowed to be missing—package managers do not warn about nonexistent optionals
- Scanning tools may not check whether all declared optional dependencies actually exist
- The attack surface is diffuse—any package with optional dependencies is potentially vulnerable

**Defense:** Audit your dependencies' optional dependency declarations for nonexistent packages. Tools like [Phantom Guard][phantom-guard] specifically detect references to nonexistent packages before installation. For packages you maintain, ensure all optional dependencies either exist or are removed from declarations. Consider proactively registering placeholder packages for optional dependencies during the gap between declaration and publication.

[orca-dependency-confusion]: https://orca.security/resources/blog/dependency-confusion-supply-chain-attacks/
[phantom-guard]: https://github.com/matte1782/phantom_guard

## Revival Hijack: Claiming Removed Packages

!!! danger "22,000 PyPI Packages Vulnerable"

    JFrog research identified over 22,000 PyPI packages vulnerable to Revival Hijack—where attackers register package names that were removed by their original owners. These names remain valid targets for re-registration.

**Revival Hijack** exploits a subtle aspect of package registry mechanics: when maintainers remove packages from a registry, the package names often become available for re-registration by anyone. Unlike typosquatting (which targets misspellings) or dependency confusion (which targets private/public namespace collisions), Revival Hijack targets legitimate package names that previously existed.

**Attack mechanism:**

1. A legitimate maintainer removes their package from a registry (for various reasons: deprecation, abandonment, rebranding)
2. Existing projects that depended on the removed package still have it listed in their dependencies
3. Attacker registers the now-available package name with malicious code
4. When dependent projects reinstall dependencies (new environments, CI/CD rebuilds), they receive the attacker's version
5. Malicious code executes with the trust previously granted to the legitimate package

JFrog security researchers discovered this attack vector and documented its scale: **over 22,000 existing PyPI packages were vulnerable to hijacking**.[^jfrog-revival-hijack] To protect the ecosystem, JFrog proactively created a "security_holding" account to safely claim susceptible package names, registering them with version 0.0.0.1 placeholders to prevent malicious registration.

**Why packages get removed:**

- Maintainer abandonment or loss of interest
- Project rebranding or consolidation
- Legal or trademark disputes
- Security concerns about continued maintenance
- Organizational changes (acquired companies, project transfers)

**What makes this attack effective:**

- **Legitimate name**: The package name has real history and may appear in production lockfiles
- **Silent hijack**: Unlike typosquatting, no spelling error is required
- **Cached dependencies**: Organizations may have old lockfiles referencing removed packages
- **Build reproducibility gaps**: Rebuilds from source may fetch the hijacked version
- **No registry warning**: Registries don't typically alert users that a previously-installed package was removed and re-registered

**Detection challenges:**

- Distinguishing legitimate package transfers from malicious re-registration
- Monitoring for removal and re-registration across thousands of packages
- Alerting users who previously depended on removed packages

**Defense:**

1. **Pin exact versions and use lockfiles**: Ensures reproducible installations from known-good versions
2. **Monitor for package removal**: Track when dependencies are removed from registries
3. **Use package caching/mirroring**: Internal mirrors can retain packages even after upstream removal
4. **Registry hygiene**: Registries should consider "cooling off" periods before removed names can be re-registered
5. **Dependency freshness checks**: Flag dependencies on packages that no longer exist in their original form

[^jfrog-revival-hijack]: JFrog Security Research, "Revival Hijack: PyPI Hijack Technique Exploits Removed Package Names," September 2024, https://research.jfrog.com/malicious-packages/

## Package Aliasing and Redirection

Package managers support various mechanisms for aliasing, redirecting, or substituting packages. **Aliasing attacks** exploit these features to redirect package resolution to attacker-controlled code.

**npm aliasing**: npm supports installing packages under different names using the alias syntax: `npm install my-alias@npm:actual-package`. If an attacker can influence the alias target (through configuration injection or social engineering), they can redirect installations.

**Git URL substitution**: Many package managers support installing directly from Git URLs. Attackers who can inject or modify Git URLs in configuration or dependency declarations can redirect to malicious repositories.

**Registry redirect attacks**: If attackers can modify environment variables or configuration files that specify registry URLs, they can redirect all package installations to attacker-controlled infrastructure.

**Post-install script redirection**: Packages can include scripts that modify the local npm configuration, potentially affecting subsequent installations of other packages.

**Detection challenges:**

- Aliasing is a legitimate feature with valid use cases
- Configuration can be modified at multiple levels (project, user, system)
- Git URLs and registry settings may be set through environment variables not visible in repository files

**Defense:** Audit package manager configuration files and environment variables. Restrict who can modify build configuration. Use configuration validation to ensure expected registry settings.

## Multi-Stage Attacks: The Slow Compromise

The most sophisticated package attacks unfold in stages, with benign initial versions that later become malicious. This pattern, exemplified by the event-stream attack (discussed in Section 6.4), defeats point-in-time analysis.

**Stage 1: Establishment**

The attacker publishes a genuinely useful package or takes over an existing legitimate package. This version contains no malicious code and may be actively maintained with real functionality.

**Stage 2: Trust Building**

The package gains users, downloads, and potentially dependent packages. The attacker may contribute legitimately to build credibility. This phase can last months or years.

**Stage 3: Preparation**

The attacker introduces seemingly innocuous changes that prepare for the attack:

- Adding new dependencies that will later contain malicious code
- Introducing code patterns that enable future obfuscation
- Establishing legitimate-appearing infrastructure

**Stage 4: Payload Delivery**

The malicious payload is introduced, either directly or through a dependency. The attack may target specific victims (as in event-stream targeting Copay) to reduce detection likelihood.

**Stage 5: Cleanup (optional)**

The attacker may publish new versions that remove the malicious code, making forensic analysis more difficult. Users updating to the "clean" version may not realize they were previously compromised.

**Detection challenges:**

- Point-in-time analysis misses the temporal dimension
- Each stage individually may appear benign
- Targeted attacks may never trigger broadly-deployed detection

**Defense:** Track dependency change history, not just current state. Flag sudden maintainer changes, new dependencies in established packages, and unusual version patterns. Implement behavioral monitoring in runtime environments.

## Defense Recommendations for Advanced Techniques

Defending against these advanced techniques requires layered approaches:

1. **Source verification**: Validate that published packages match expected source repositories. Require reproducible builds for critical dependencies.

2. **Temporal analysis**: Track package history over time. Flag packages with significant changes in maintainer, dependencies, or behavior.

3. **Deep manifest inspection**: Analyze actual package contents, not just manifest declarations. Compare published artifacts to source.

4. **Lockfile governance**: Treat lockfile changes as security-sensitive. Implement tooling that validates lockfile modifications.

5. **Namespace hygiene**: Defensively register organizational namespaces. Configure explicit registry routing for all scopes.

6. **Behavioral monitoring**: Detect anomalous runtime behavior that may indicate compromise regardless of how it was introduced.

7. **Multi-signal evaluation**: Combine automated scanning with manual review for critical dependencies. Do not rely solely on social signals like stars or contributor counts.

These advanced techniques represent the current frontier of package attacks. As detection improves for simpler attacks, sophisticated adversaries increasingly employ these methods. Security teams must anticipate this evolution and implement defenses that address not just current threats but emerging techniques.

[checkmarx-starjacking]: https://checkmarx.com/blog/starjacking-making-your-new-open-source-package-popular-in-a-snap/
[socket]: https://socket.dev/
[snyk-lockfile-injection]: https://snyk.io/blog/why-npm-lockfiles-can-be-a-security-blindspot-for-injecting-malicious-modules/
[xz-utils-backdoor]: https://www.openwall.com/lists/oss-security/2024/03/29/4
