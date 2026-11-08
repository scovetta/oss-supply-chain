---
title: "Case Studies in Package Attacks"
description: "Study five notable package attacks including event-stream and ua-parser-js, revealing attack vectors and lessons for defenders."
icon: "lucide/file-search"
---

# 6.4 Case Studies in Package Attacks

Abstract analysis of attack techniques provides valuable understanding, but detailed examination of actual incidents reveals nuances that generalized discussions miss. This section presents five case studies of notable package attacks, each illustrating different attack vectors, motivations, and discovery mechanisms. Together, these incidents provide a representative sample of the threats facing package ecosystems and the lessons defenders should extract from them.

## event-stream: The Long Game of Trust (2018)

!!! example "The Most Significant Social Engineering Attack"

    The event-stream incident remains the most significant example of social engineering in open source: an attacker ("right9ctrl") patiently built trust through helpful contributions, then took over maintenance from an overwhelmed maintainer to inject a targeted Bitcoin wallet theft payload.

The **event-stream incident** remains the most significant example of social engineering in the open source ecosystem, demonstrating how attackers can patiently build trust to gain access to widely-used packages.

**Background and Timeline:**

`event-stream` was a popular npm package for working with Node.js streams, created by Dominic Tarr. By 2018, it had approximately 2 million weekly downloads and was a dependency of numerous other packages.

- **Early 2018**: A GitHub user named "right9ctrl" began contributing to event-stream, submitting helpful pull requests and engaging constructively in issues.

- **September 2018**: Tarr, who had moved on to other projects, accepted right9ctrl's offer to take over maintenance. This transfer was conducted openly through GitHub, appearing to be normal open source succession.

- **September 9, 2018**: right9ctrl published version 3.3.6, adding a new dependency: `flatmap-stream`. This package appeared to be a simple functional programming utility.

- **October 5, 2018**: right9ctrl published version 4.0.0 of event-stream, removing the flatmap-stream dependency. This version bump made the malicious 3.x versions appear to be "old" versions that security-conscious users might avoid.

- **November 20, 2018**: A GitHub user noticed unusual code in flatmap-stream while investigating build issues and reported it.

- **November 26, 2018**: Full analysis revealed the attack's sophistication and target.

**Technical Details:**

The malicious code in flatmap-stream was carefully designed to evade detection:

1. The package included a minified and encrypted payload
2. Decryption required a key derived from the package `description` field of a specific dependent package: Copay, a Bitcoin wallet
3. The malicious code would only execute in the Copay build environment
4. When executed, it would steal Bitcoin wallet credentials and private keys

This conditional execution meant that the malicious code:

- Would not trigger during npm's security scanning (which wouldn't have the Copay context)
- Would not affect the millions of event-stream users who didn't use Copay
- Would specifically target Copay users' cryptocurrency

**Impact:**

- Approximately 8 million downloads were reported during the 2.5 months the malicious versions were available[^event-stream-downloads]
- Copay released updates to address the compromise
- Unknown number of Copay users potentially had wallets compromised
- BitPay (Copay's parent company) warned users to move funds from potentially affected wallets
- [npm's detailed incident report][npm-event-stream] and [Snyk's post-mortem analysis][snyk-event-stream] documented the attack's sophistication

**Lessons:**

1. **Maintainer transitions are high-risk moments.** The attack succeeded because an overwhelmed maintainer handed off control without ability to vet the new maintainer's intentions.

2. **Targeted attacks can hide in widely-used packages.** The attack only activated for Copay users, demonstrating that malicious code can be surgical rather than broadly destructive.

3. **Deep dependency inspection is necessary.** The malicious code was not in event-stream but in a newly-added dependency. Reviewing only direct code changes would have missed it.

4. **Community vigilance matters.** The attack was discovered through community member investigation, not automated scanning.

## ua-parser-js: Credential Compromise at Scale (2021)

!!! danger "Four Hours of Exposure"

    Malicious versions of ua-parser-js (~7 million weekly downloads) were available for approximately 4 hours after attackers compromised the maintainer's npm account. Thousands of installations occurred during this window, with CI/CD pipelines particularly exposed.

The **ua-parser-js compromise** demonstrated how a single credential compromise could immediately affect millions of users.

**Background and Timeline:**

`ua-parser-js` parses User-Agent strings to detect browser, engine, operating system, and device information. With approximately 7 million weekly downloads, it was among npm's most widely-used packages.

- **October 22, 2021 (approximately 12:15 UTC)**: Attackers gained access to the maintainer's npm account through credential compromise and published versions 0.7.29, 0.8.0, and 1.0.0, containing malicious code.

- **October 22, 2021 (multiple hours)**: Malicious versions were downloaded and installed by users and CI systems worldwide.

- **October 22, 2021 (afternoon UTC)**: The legitimate maintainer discovered the compromise and reported it to npm.

- **October 22, 2021 (16:16-16:26 UTC)**: Safe versions were published and malicious versions were unpublished.

**Technical Details:**

The malicious code included two payloads:

1. **A cryptocurrency miner**: Installed and executed on Linux systems to mine cryptocurrency using victims' computational resources.

2. **A credential stealer**: Harvested cookies and passwords from Windows systems, transmitting them to attacker-controlled servers.

The attack exploited installation hooks: the malicious code executed immediately when `npm install` ran, before any code review could occur.

**Impact:**

- Malicious versions were available for approximately 4 hours
- The package's download velocity meant thousands of installations during this window
- Organizations running CI/CD pipelines during this period were particularly exposed: automated builds repeatedly installed the malicious version
- [CISA issued an alert][cisa-ua-parser] reporting the incident affected an unknown number of organizations

**Discovery:**

The attack was discovered when the legitimate maintainer received notifications about package publications they had not made. This relatively quick detection limited the damage but did not prevent significant exposure.

**Lessons:**

1. **MFA is essential for high-impact packages.** The attack succeeded through credential compromise; MFA would have prevented publication under the maintainer's identity.

2. **Response time matters enormously.** Even a 4-hour window exposed thousands of systems. Faster detection and response capabilities are critical.

3. **CI/CD systems are high-exposure targets.** Automated builds that repeatedly install packages amplify the impact of malicious versions.

4. **Monitoring for unexpected publications is valuable.** The maintainer's quick discovery of unauthorized publications enabled rapid response.

## colors.js and faker.js: Maintainer Protest (2022)

!!! warning "When Maintainers Become the Threat"

    The colors.js and faker.js incident demonstrated that the threat model must include maintainers themselves. A single maintainer's deliberate sabotage broke thousands of projects including Amazon AWS CDK and Facebook Create React App.

The **colors.js and faker.js incident** raised fundamental questions about maintainer trust, demonstrating that the threat model must include maintainers themselves.

**Background and Timeline:**

Marak Squires created and maintained `colors.js` (terminal string styling, ~20 million weekly downloads) and `faker.js` (fake data generation, ~2.5 million weekly downloads), both widely-used npm packages.

- **November 2020**: Squires tweeted frustration about Fortune 500 companies using his open source work without compensation, stating he would no longer support them for free.

- **Early January 2022 (colors.js)**: Squires published version 1.4.1 with new code that printed an infinite loop of garbage characters ("LIBERTY LIBERTY LIBERTY") and included a new `am I Not Faisal?` comment referencing historical events.

- **Early January 2022 (faker.js)**: Squires deleted the faker.js repository content and replaced it with "What really happened with Aaron Swartz?" references.

- **January 6-7, 2022**: Thousands of projects using these dependencies experienced broken builds as the corrupted code propagated through dependency updates.

- **January 8, 2022**: npm reverted colors.js to the last non-malicious version and transferred control. GitHub temporarily suspended Squires' account.

**Technical Details:**

The colors.js modification was simple but effective:

```javascript
let am = 'a]b.c" | ".d" | ".e" | ".f" | ".g" | ".h" | ".i';
var n = new Uint8Array(100);
// ... code that prints gibberish infinitely
```

The code introduced an infinite loop that would hang any application using the library. For CLI applications and build tools that depended on colors.js, this meant complete operational failure.

**Impact:**

- Major projects including Amazon AWS CDK, Facebook Create React App, and thousands of others were affected
- CI/CD pipelines across the industry failed
- Developers scrambled to pin versions and find workarounds
- The incident sparked intense debate about open source sustainability and maintainer rights

**Community Response:**

Reactions were divided:

- Some viewed Squires' actions as legitimate protest against exploitation of open source labor
- Others condemned the sabotage as violating user trust and potentially harming innocent developers
- The incident intensified discussions about funding open source maintainers

**Lessons:**

1. **Maintainers themselves are a trust point.** Even well-known, long-term maintainers can take actions that harm users.

2. **Version pinning and lockfiles provide protection.** Organizations that pinned specific versions rather than allowing automatic updates were not immediately affected.

3. **Open source sustainability is a security issue.** Maintainer frustration with exploitation can manifest in destructive actions.

4. **Registry oversight has limits.** Registries struggle to distinguish intentional sabotage from legitimate (if poorly tested) changes.

## node-ipc: Geopolitical Protestware (2022)

The **node-ipc incident** introduced the term **protestware** into security discussions, demonstrating how geopolitical events could manifest in the software supply chain.

**Background and Timeline:**

`node-ipc` is a popular npm package for inter-process communication, with approximately 1 million weekly downloads. Its maintainer, Brandon Nozaki Miller (RIAEvangelist), modified it in response to Russia's invasion of Ukraine.

- **March 7, 2022**: Miller published node-ipc versions 10.1.1 and 10.1.2, adding a dependency on a new package called `peacenotwar` and code that detected systems with Russian or Belarusian IP addresses and overwrote files with heart emojis.

- **March 15, 2022**: The destructive payload was discovered and publicly reported.

- **March 15-16, 2022**: Security researchers analyzed the code and published warnings. The malicious versions were flagged in vulnerability databases.

**Technical Details:**

The payload in versions 10.1.1 and 10.1.2 included:

1. IP geolocation check using public APIs
2. If the IP geolocated to Russia or Belarus, the code would recursively overwrite files on the system with "❤️"
3. The `peacenotwar` dependency would create files named `WITH-LOVE-FROM-AMERICA.txt` on affected systems

Later versions removed the destructive payload but retained `peacenotwar` for displaying protest messages.

**Impact:**

- An American company reported that their network infrastructure was affected due to Belarus-based development contractors
- Unknown number of systems in Russia and Belarus were affected
- Security teams worldwide scrambled to assess exposure
- The incident received significant media coverage

**Community and Legal Response:**

The incident sparked intense debate:

- Some viewed targeting Russian/Belarusian systems as legitimate protest against the invasion
- Security researchers emphasized that this was malware by any reasonable definition
- GitHub did not remove the repository, citing that the code was disclosed in the repository
- The incident raised questions about the legal liability of intentional supply chain sabotage

**Lessons:**

1. **Geopolitical events create new threat vectors.** Maintainer actions can be motivated by political beliefs, not just financial gain.

2. **Protestware is still malware.** Regardless of motivation, unauthorized data destruction is malicious behavior.

3. **Targeting by geography sets dangerous precedent.** If political targeting is accepted, any package could become a vector for discrimination.

4. **All dependencies require evaluation.** The `peacenotwar` package was new and had no legitimate purpose but was automatically installed as a dependency.

## PyPI Malware Campaigns: Patterns and Trends

While npm has dominated package attack news, **Python Package Index (PyPI)** has experienced increasingly sophisticated malware campaigns demonstrating evolving attacker techniques.

**Notable Campaigns:**

**2022 PyPI Campaign (Phylum discovery)**: Researchers discovered a coordinated campaign publishing over 1,000 malicious packages in a short period. Packages used typosquatting, dependency confusion patterns, and bundled cryptominers.

**2023 W4SP Stealer Campaign**: Security researchers identified packages containing "W4SP Stealer" malware targeting Discord tokens, browser passwords, and cryptocurrency wallets. Packages used obfuscation and legitimate-appearing code.

**2024 Ultralytics Incident** (December 2024): The popular machine learning library was compromised after an attacker exploited a vulnerable GitHub Actions workflow using a malicious branch name.[^ultralytics-yossarian] [Malicious versions were published to PyPI][pypi-ultralytics], containing cryptocurrency-mining code. The incident affected a mainstream package with legitimate users.

**Common Patterns:**

PyPI malware campaigns demonstrate recurring techniques:

- **Typosquatting at scale**: Automated registration of hundreds of typosquat variants
- **`setup.py` exploitation**: Malicious code in installation scripts executing during `pip install`
- **Obfuscation libraries**: Use of tools like PyArmor or custom encoders to hide malicious code
- **Credential targeting**: Discord tokens, browser passwords, and cloud credentials as primary targets
- **Rapid publication**: Publishing many packages quickly before detection can catch up

**Detection Challenges:**

PyPI's historically more limited security infrastructure compared to npm meant:

- Longer dwell times before malicious package discovery
- Less sophisticated automated detection
- Greater reliance on community reporting

Recent improvements including malware scanning, Trusted Publishers, and enhanced authentication have improved PyPI's security posture, but the ecosystem remains an active attacker target.

## dYdX: Maintainer Compromise Across Two Registries (2026)

The **dYdX package compromise** in February 2026 demonstrated a multi-registry maintainer account takeover that used trust in a legitimate project's name to distribute credential-stealing malware across both npm and PyPI simultaneously.[^dydx-compromise]

[^dydx-compromise]: Socket Security research and public reporting on dYdX package compromise, https://socket.dev/blog/malicious-dydx-packages-published-to-npm-and-pypi, February 6, 2026.

**Background and Timeline:**

dYdX is a decentralized derivatives exchange with significant usage in the cryptocurrency ecosystem. Legitimate dYdX-related packages on npm and PyPI were used by developers building trading bots, portfolio managers, and DeFi integrations. On February 6, 2026, Socket Security reported that malicious versions had been published to both registries.

**Technical Details:**

Attackers compromised the maintainer's accounts on both registries and published malicious versions of existing, trusted packages. Because the packages were already established with legitimate histories and download counts, the malicious updates were pulled automatically by CI/CD pipelines and developers running routine dependency updates.

The malicious versions included two complementary capabilities:

1. **Cryptocurrency wallet stealers**: Targeted wallet credentials, private keys, and seed phrases stored on developer machines
2. **Remote access tooling**: Established persistent backdoor access enabling follow-on operations, including exfiltration of source code and additional credentials

**Impact:**

The dYdX incident illustrates several trends in package attack evolution:

- **Multi-registry attacks**: Rather than targeting a single ecosystem, attackers compromised the same project across npm and PyPI, doubling the victim surface
- **Legitimate-to-malicious flip**: Unlike typosquatting, the packages were genuine projects with established trust — the "supply-chain trust inheritance" problem
- **Follow-on capability**: The combination of credential theft and remote access suggests interest in persistent access to developer environments as a staging ground for further supply-chain attacks

**Lessons:**

1. **Cross-registry credential hygiene matters.** Maintainers using the same credentials or tokens across registries create correlated compromise risk. Use unique, scoped credentials per registry.
2. **Maintainer account compromise remains the highest-leverage attack on registries.** Trusted publishing (OIDC) eliminates stored publishing tokens; phishing-resistant MFA (WebAuthn) protects account login.
3. **Monitor for unexpected version publications of established packages.** Anomaly detection on publication cadence, payload size changes, and new dependency additions is more effective than static scanning alone.

## PyPI Spellchecker Lookalikes: Fileless RAT via Typosquatting (2026)

!!! warning "Fileless Execution Evades Detection"

    Malicious PyPI packages masquerading as spellchecker utilities delivered a fileless Python RAT, executing entirely in memory with no files written to disk. The shift from cryptocurrency-only payloads to general-purpose RATs signals that attackers increasingly view developer machines as high-value foothold targets.

In January 2026, Aikido Security reported a cluster of PyPI packages masquerading as spellchecker utilities that delivered a fileless Python remote access trojan (RAT).[^aikido-spellchecker] The incident demonstrates attacker ROI shifting from pure cryptocurrency theft to general-purpose developer foothold acquisition.

[^aikido-spellchecker]: Aikido Security, "Malicious PyPI 'spellchecker' packages deliver fileless Python RAT," https://www.aikido.dev/blog/malicious-pypi-packages-spellcheckpy-and-spellcheckerpy-deliver-python-rat, January 23, 2026.

**Technical Details:**

The packages used names designed to appear as legitimate spellchecking libraries, a common pattern targeting developers who search for common functionality and install the first plausible result. The malicious `setup.py` executed during `pip install` and:

1. Spawned a detached child process (not dependent on the parent installer)
2. Downloaded and executed a Python RAT entirely in memory: no files written to disk
3. Established persistence-like behavior through process respawning

The fileless execution model represents an evolution beyond the typical "drop a file and execute" pattern common in earlier malware campaigns. By keeping the payload in memory and spawning detached processes, the malware reduces forensic artifacts and evades file-based detection.

**Impact:**

The shift from cryptocurrency-only payloads to **general-purpose RATs** signals that attackers increasingly view developer workstations as high-value *foothold* targets with follow-on options, not merely as sources of cryptocurrency credentials. A developer machine compromised with a RAT enables:

- Source code theft and modification
- CI/CD credential harvesting
- Lateral movement to internal systems
- Long-term persistent access for future supply-chain attacks

**Lessons:**

1. **Verify package identity before installation.** Check the package's PyPI page for download counts, maintainer history, and repository links before running `pip install`.
2. **Use `--no-deps` and `--dry-run` for unfamiliar packages.** Inspect what will be installed before executing install scripts.
3. **Monitor for detached processes spawned during package installation** in CI/CD environments: this is anomalous behavior that sandboxed install environments can detect.

## Synthesis: Common Patterns Across Incidents

These case studies reveal recurring patterns that inform defensive strategy:

**Attack Vectors:**

1. **Credential compromise** (ua-parser-js): Attackers target maintainer accounts directly
2. **Social engineering** (event-stream): Attackers build trust over time to gain access
3. **Maintainer action** (colors.js, node-ipc): The trusted maintainer themselves takes harmful action
4. **Volume attacks** (PyPI campaigns): Overwhelming detection with many malicious packages

**Timing Patterns:**

- **Quick exploitation**: When credentials are compromised, attackers act immediately
- **Long-game patience**: Social engineering attacks may take months or years
- **Protest timing**: Maintainer protests often coincide with personal frustration peaks or external events

**Detection Mechanisms:**

- **Community vigilance**: Most incidents were discovered by developers noticing anomalies
- **Build failures**: Destructive code (colors.js) was discovered through broken builds
- **Suspicious behavior**: Unexpected publications or dependency additions triggered investigation

**Impact Amplifiers:**

- **CI/CD automation**: Automated builds rapidly installed malicious versions
- **Transitive dependencies**: Malicious code flowed through dependency chains
- **Delayed detection**: Hours or days of exposure before discovery

**Lessons for Defenders:**

1. **Enforce MFA for package publishing.** Credential compromise enables immediate, high-impact attacks.

2. **Monitor for unexpected dependency changes.** New dependencies, especially from unknown sources, warrant scrutiny.

3. **Use lockfiles and pin versions.** Preventing automatic updates to latest versions provides time for community detection.

4. **Evaluate maintainer risk.** Consider project governance, maintainer succession, and single-maintainer risk.

5. **Implement detection layers.** Combine automated scanning, anomaly detection, and community reporting.

6. **Prepare for incident response.** When malicious packages are discovered, rapid response limits damage.

7. **Recognize that trust is multidimensional.** Even trusted maintainers can act destructively; trust models must account for this.

These case studies demonstrate that package attacks are not theoretical risks but recurring incidents affecting real organizations and developers. The patterns they reveal should inform security strategy, dependency selection criteria, and incident response preparation.

[npm-event-stream]: https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident
[snyk-event-stream]: https://snyk.io/blog/a-post-mortem-of-the-malicious-event-stream-backdoor/
[cisa-ua-parser]: https://www.cisa.gov/news-events/alerts/2021/10/22/malware-discovered-popular-npm-package-ua-parser-js
[pypi-ultralytics]: https://blog.pypi.org/posts/2024-12-11-ultralytics-attack-analysis/
[^event-stream-downloads]: Security Boulevard, "Malicious code in npm 'event-stream' package targets a bitcoin wallet and causes 8 million downloads in two months" (November 27, 2018). <https://securityboulevard.com/2018/11/malicious-code-in-npm-event-stream-package-targets-a-bitcoin-wallet-and-causes-8-million-downloads-in-two-months/>
[^ultralytics-yossarian]: William Woodruff, "zizmor would have caught the Ultralytics workflow vulnerability," December 6, 2024, https://blog.yossarian.net/2024/12/06/zizmor-ultralytics-injection
