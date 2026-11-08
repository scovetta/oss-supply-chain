## Appendix F: Major Supply Chain Incident Timeline

This appendix provides a chronological reference of significant software supply chain incidents that have shaped our understanding of supply chain security risks. Each entry documents what happened, the scope of impact, key lessons learned, and sources for further investigation. These incidents span four decades, demonstrating both the evolution of attack techniques and the persistent nature of supply chain vulnerabilities.

> **Disclaimer on Incident Information**: All incident descriptions in this appendix are based on publicly available information, security research reports, and official disclosures as of the publication date (November 2026). Details about security incidents may be:
>
> - **Incomplete** due to ongoing investigations or undisclosed information
> - **Subject to interpretation** based on available evidence
> - **Disputed** by organizations or individuals mentioned
> - **Updated** as new information emerges after publication
>
> Attribution statements (e.g., "attributed to [threat actor]") reflect assessments by security researchers, government agencies, or industry analysts based on available indicators. Such attributions represent informed professional judgment rather than legal findings or definitive proof.
>
> Organizations and individuals mentioned are referenced for educational purposes to document publicly reported incidents. Such references do not constitute accusations, legal findings, or claims of wrongdoing beyond what has been publicly reported and documented in cited sources.
>
> Readers should consult cited sources for the most current information and official statements from involved parties.

---

### 1984

#### Reflections on Trusting Trust

**Date:** October 1984

**Summary:** Ken Thompson delivered his Turing Award lecture "Reflections on Trusting Trust," demonstrating how a compiler could be modified to insert a backdoor into any program it compiles, including future versions of the compiler itself. Thompson showed that even if you inspect source code and find it clean, you cannot trust the compiled binary unless you trust the entire toolchain that produced it.

**Impact Scope:** Conceptual/theoretical demonstration affecting all compiled software

**Key Lessons:**

- Trust in software must extend to the entire build toolchain, not just source code
- Self-replicating backdoors can persist across compiler generations without appearing in source
- Binary verification and reproducible builds are essential for establishing trust
- The "trusting trust" problem remains fundamentally unsolved for most software

**Sources:**

- Thompson, K., "Reflections on Trusting Trust," Communications of the ACM, 1984[^thompson-1984c]

---

### 2008

#### Debian OpenSSL Weak Key Generation (CVE-2008-0166)

**Date:** May 13, 2008 (disclosed)

**Summary:** A Debian maintainer inadvertently removed critical entropy-gathering code from OpenSSL in 2006 while addressing a Valgrind warning. This reduced the randomness of generated cryptographic keys to approximately 32,767 possible values per architecture and key size. The vulnerability persisted undetected for nearly two years in Debian and derivative distributions including Ubuntu.

**Impact Scope:** All cryptographic keys generated on affected Debian-based systems from September 2006 to May 2008, including SSH keys, SSL certificates, and OpenVPN keys

**Key Lessons:**

- Security-critical code changes require expert review, even for seemingly minor modifications
- Upstream maintainers and downstream packagers must communicate effectively about changes
- Cryptographic code is especially sensitive: warnings may indicate security features, not bugs
- Long detection times for subtle vulnerabilities highlight the need for security auditing

**Sources:**

- Debian Security Advisory DSA-1571-1[^debian-dsa-1571]
- CVE-2008-0166[^cve-2008-0166]
- Schneier, B., "Random Number Bug in Debian Linux," 2008[^schneier-debian-2008]

---

### 2014

#### Heartbleed (CVE-2014-0160)

**Date:** April 7, 2014 (disclosed)

**Summary:** A buffer over-read vulnerability in OpenSSL's implementation of the TLS heartbeat extension allowed attackers to read up to 64KB of server memory per request, potentially exposing private keys, session tokens, passwords, and other sensitive data. The bug was introduced in December 2011 and affected OpenSSL versions 1.0.1 through 1.0.1f.

**Impact Scope:** Estimated 17% of internet SSL servers (approximately 500,000 servers) at time of disclosure; affected major services including Yahoo, Imgur, and numerous others

**Key Lessons:**

- Critical infrastructure software often lacks adequate funding and maintainer resources
- Memory-unsafe languages (C) in security-critical code present persistent risks
- Widespread dependencies on single implementations create systemic vulnerability
- Led directly to formation of the Core Infrastructure Initiative (now OpenSSF)

**Sources:**

- Heartbleed.com[^heartbleed-site]
- CVE-2014-0160[^cve-2014-0160]
- Durumeric, Z., et al., "The Matter of Heartbleed," IMC 2014[^durumeric-heartbleed-2014]

---

### 2016

#### left-pad Incident

**Date:** March 22, 2016

**Summary:** Developer Azer Koculu unpublished 273 npm packages, including the 11-line `left-pad` utility, following a trademark dispute with messaging app Kik. Because `left-pad` was a dependency of Babel, React, and thousands of other projects, builds worldwide began failing immediately. npm took the unprecedented step of un-unpublishing the package to restore service.

**Impact Scope:** Thousands of JavaScript projects experienced build failures; major frameworks including React and Babel were affected; incident lasted approximately 2.5 hours before npm intervention

**Key Lessons:**

- Micro-dependencies create fragile dependency chains
- Package registries need policies governing package removal
- Build systems should not assume package availability
- Dependency vendoring and lock files provide resilience against upstream changes

**Sources:**

- Koculu, A., "I've Just Liberated My Modules," 2016[^koculu-leftpad-2016]
- Williams, C., "How one developer just broke Node, Babel and thousands of projects," The Register, 2016[^register-leftpad-2016]
- npm Blog, "kik, left-pad, and npm," 2016[^npm-leftpad-2016]

---

### 2018

#### event-stream Compromise

**Date:** November 26, 2018 (disclosed)

**Summary:** A malicious actor gained maintainership of the popular `event-stream` npm package through social engineering, then added a dependency on `flatmap-stream` containing obfuscated code targeting the Copay Bitcoin wallet. The attack specifically extracted private keys from Copay wallet users. The original maintainer had transferred control after the attacker offered to help maintain the project.

**Impact Scope:** `event-stream` had approximately 2 million weekly downloads; the malicious payload specifically targeted Copay wallet versions 5.0.2 through 5.1.0

**Key Lessons:**

- Maintainer succession requires careful vetting and trust establishment
- Transitive dependencies can introduce malicious code without direct project changes
- Targeted attacks may hide within broadly-used packages
- Package consumers should monitor for unexpected dependency additions

**Sources:**

- GitHub Issue #116, "I don't know what to say," dominictarr/event-stream[^eventstream-issue-116]
- Snyk, "Malicious code found in npm package event-stream," 2018[^snyk-eventstream-2018]
- npm Security Advisory[^npm-advisory-737]

---

### 2020

#### SolarWinds SUNBURST Attack

**Date:** December 13, 2020 (disclosed); attack began March 2020

**Summary:** Nation-state actors (attributed to Russian SVR) compromised SolarWinds' Orion software build system, inserting the SUNBURST backdoor into updates distributed to approximately 18,000 organizations. The malware remained dormant for two weeks after installation before beaconing to attacker-controlled infrastructure. The attack was discovered by FireEye after noticing anomalous activity in their own environment.

**Impact Scope:** Approximately 18,000 organizations installed the compromised update; confirmed breaches at multiple U.S. government agencies (Treasury, Commerce, Homeland Security, State Department, and others), Microsoft, FireEye, and numerous Fortune 500 companies

**Key Lessons:**

- Build system security is as critical as source code security
- Software signing alone does not guarantee integrity: the build process must be secured
- Sophisticated attackers target widely-deployed management software for maximum reach
- Detection of supply chain compromises requires behavioral analysis beyond signature matching
- Led to Executive Order 14028 mandating SBOM adoption for federal software procurement

**Sources:**

- CISA, "Advanced Persistent Threat Compromise of Government Agencies," Alert AA20-352A[^cisa-aa20-352a]
- FireEye, "Highly Evasive Attacker Leverages SolarWinds Supply Chain," 2020[^fireeye-solarwinds-2020]
- Microsoft, "Analyzing Solorigate," 2020[^microsoft-solorigate-2020]

---

### 2021

#### Codecov Bash Uploader Compromise

**Date:** April 1, 2021 (disclosed); attack began January 31, 2021

**Summary:** Attackers exploited a vulnerability in Codecov's Docker image creation process to modify the Bash Uploader script, adding code that exfiltrated environment variables (including CI/CD secrets, API tokens, and credentials) from customer build environments to an attacker-controlled server. The compromise persisted for over two months before detection.

**Impact Scope:** Approximately 29,000 customers potentially affected; confirmed secondary breaches at Twitch, HashiCorp, and others whose secrets were exfiltrated

**Key Lessons:**

- CI/CD environments contain high-value secrets requiring protection
- Scripts fetched and executed during builds are attack vectors
- Supply chain attacks can cascade: compromised credentials enable secondary attacks
- Integrity verification (checksums, signatures) should be mandatory for build-time dependencies

**Sources:**

- Codecov, "Bash Uploader Security Update," 2021[^codecov-security-2021]
- HashiCorp, "Codecov Security Event Impact," 2021[^hashicorp-codecov-2021]
- Reuters, "Codecov hackers breached hundreds of networks," 2021[^reuters-codecov-2021]

---

#### ua-parser-js Hijacking

**Date:** October 22, 2021

**Summary:** Attackers compromised the npm account of the `ua-parser-js` maintainer and published three malicious versions (0.7.29, 0.8.0, 1.0.0) containing cryptocurrency miners and password-stealing trojans. The package, used for parsing browser user-agent strings, had approximately 8 million weekly downloads. The compromise was detected and removed within hours.

**Impact Scope:** Approximately 8 million weekly downloads; malicious versions available for approximately 4 hours; CISA issued alert regarding potential federal agency exposure

**Key Lessons:**

- High-download packages are attractive targets for account takeover
- Multi-factor authentication on package registry accounts is essential
- Rapid detection and response capabilities are critical for package registries
- Even brief windows of compromise can affect millions of downstream users

**Sources:**

- GitHub Advisory GHSA-pjwm-rvh2-c87w[^ghsa-ua-parser-js]
- CISA, "MAR-10354752-1.v1," 2021[^cisa-ua-parser-2021]
- Bleeping Computer, "Popular npm library hijacked," 2021[^bleeping-ua-parser-2021]

---

#### Log4Shell (CVE-2021-44228)

**Date:** December 9, 2021 (disclosed)

**Summary:** A critical remote code execution vulnerability was discovered in Apache Log4j 2, a ubiquitous Java logging library. The flaw allowed attackers to execute arbitrary code by submitting specially crafted strings that triggered JNDI lookups to attacker-controlled servers. Due to Log4j's prevalence across enterprise software, cloud services, and embedded systems, the vulnerability was described as one of the most severe in internet history.

**Impact Scope:** Estimated hundreds of millions of affected devices; exploited within hours of disclosure; affected services included Apple iCloud, Amazon AWS, Cloudflare, Steam, Minecraft, and countless enterprise applications

**Key Lessons:**

- Ubiquitous dependencies create systemic risk across the software ecosystem
- Many organizations lack visibility into their transitive dependencies
- Vulnerability disclosure in widely-used libraries requires coordinated response
- SBOM adoption is essential for rapid vulnerability identification and response
- Feature-rich libraries may contain unexpected attack surface (JNDI lookup feature)

**Sources:**

- Apache Log4j Security Vulnerabilities[^apache-log4j-security]
- CVE-2021-44228[^cve-2021-44228]
- CISA, "Apache Log4j Vulnerability Guidance," 2021[^cisa-log4j-2021]
- Swiss Government NCSC, "Log4j Analysis," 2021[^swiss-log4j-2021]

---

### 2022

#### colors.js and faker.js Sabotage (Protestware)

**Date:** January 8, 2022

**Summary:** The maintainer of `colors.js` and `faker.js`, Marak Squires, deliberately corrupted both packages in protest of large corporations using open source without adequate compensation. The `colors.js` update introduced an infinite loop printing "LIBERTY LIBERTY LIBERTY" along with ANSI art, while `faker.js` was emptied entirely. The packages had a combined weekly download count exceeding 25 million.

**Impact Scope:** Approximately 19,000 projects depended on `colors.js`; Amazon AWS Cloud Development Kit (CDK) builds were disrupted; `faker.js` was subsequently forked as `@faker-js/faker` with new maintainers

**Key Lessons:**

- Open source sustainability and maintainer burnout create security risks
- Single-maintainer projects represent single points of failure
- Package registries should consider policies for detecting and responding to self-sabotage
- Dependency pinning and lock files provide protection against malicious updates
- Community forks can provide continuity when original projects are compromised

**Sources:**

- Bleeping Computer, "Dev corrupts npm libs 'colors' and 'faker'," 2022[^bleeping-colors-faker-2022]
- GitHub Issue, colors.js #317[^colors-issue-317]
- The Verge, "Open source developer corrupts widely-used libraries," 2022[^verge-colors-2022]

---

#### node-ipc Protestware (CVE-2022-23812)

**Date:** March 15, 2022 (disclosed)

**Summary:** The maintainer of `node-ipc`, a popular inter-process communication library, added code that detected Russian and Belarusian IP addresses and overwrote files on affected systems with heart emojis, in protest of Russia's invasion of Ukraine. Earlier versions contained more destructive code. The package had approximately 1 million weekly downloads and was a dependency of the Vue.js CLI.

**Impact Scope:** Approximately 1 million weekly downloads; Vue.js CLI users potentially affected; systems with Russian or Belarusian IP addresses experienced data destruction

**Key Lessons:**

- "Protestware" represents a distinct threat category where trusted maintainers weaponize their access
- Geopolitical events can motivate software supply chain attacks
- Even well-intentioned political actions in software cause collateral damage
- Automated behavioral analysis could detect unexpected file system operations
- Package registry policies must address intentional sabotage by maintainers

**Sources:**

- Snyk, "Protestware: Open Source Malware," 2022[^snyk-node-ipc-2022]
- CVE-2022-23812[^cve-2022-23812]
- Snyk, "node-ipc protestware analysis," 2022[^snyk-node-ipc-analysis-2022]

---

#### PyTorch Dependency Confusion (torchtriton)

**Date:** December 2022

**Summary:** A malicious PyPI package named `torchtriton` exploited dependency confusion to target internal PyTorch build systems. The package executed during installation and attempted to exfiltrate environment data.

**Impact Scope:**
Limited external exposure; internal systems targeted

**Key Lessons:**

- Dependency confusion remains viable years after disclosure
- Internal package namespaces require defensive registration
- ML tooling pipelines are high-value targets

**Sources:**

- PyTorch Security Advisory, December 2022[^pytorch-advisory-2022]
- Checkmarx, "PyTorch Dependency Confusion," 2023[^checkmarx-pytorch-2023]

---

### 2023

#### 3CX Supply Chain Attack

**Date:** March 29, 2023 (disclosed)

**Summary:** The 3CX desktop application, a voice and video conferencing client used by over 600,000 organizations, was compromised through a cascading supply chain attack. Attackers first compromised X_TRADER, a trading software application from Trading Technologies, then used credentials from a 3CX employee who had installed the compromised X_TRADER to access 3CX's build environment. The attack was attributed to North Korean threat actors (Lazarus Group).

**Impact Scope:** Approximately 600,000 customer organizations; 12 million daily users; both Windows and macOS versions were compromised; secondary payload targeted cryptocurrency companies

**Key Lessons:**

- Supply chain attacks can cascade: one compromise enables the next
- Employee workstations with development access require heightened security
- Nation-state actors invest in long-term, multi-stage supply chain operations
- Build environment isolation and integrity verification are essential
- Detection requires correlation across multiple vendors and time periods

**Sources:**

- CrowdStrike, "3CXDesktopApp Supply Chain Attack," 2023[^crowdstrike-3cx-2023]
- Mandiant, "3CX Supply Chain Compromise," 2023[^mandiant-3cx-2023]
- 3CX Security Advisory[^3cx-advisory]

---

#### GitHub Dependabot Account Compromise Attempts

**Date:** Ongoing 2023

**Summary:** Multiple campaigns attempted to exploit Dependabot PR workflows to introduce malicious dependency updates, relying on automated merging or insufficient review.

**Impact Scope:**
No confirmed large-scale compromise, but repeated near-misses across major repositories

**Key Lessons:**

- Automation amplifies both defense and risk
- Dependency update bots require strict policy controls
- "Near misses" should be treated as incidents for learning

---

#### npm `eslint-scope` / `@eslint` Ecosystem Confusion Attempt

**Date:** Mid-2023

**Summary:** Attackers attempted to exploit namespace trust around ESLint-related packages using lookalike names and social engineering, though widespread compromise was avoided.

**Key Lessons:**

- Trusted namespaces create implicit trust
- Visual similarity attacks bypass human review
- Namespace governance is a security control

---

#### Ledger Connect Kit Compromise

**Date:** December 14, 2023

**Summary:** Attackers compromised a former Ledger employee's NPMJS account through a phishing attack and published malicious versions (1.1.5–1.1.7) of the Ledger Connect Kit, a JavaScript library used by decentralized applications (dApps) to connect to Ledger hardware wallets. The malicious code contained a wallet drainer that redirected cryptocurrency transactions to attacker-controlled addresses.

**Impact Scope:** Over 100 cryptocurrency projects potentially affected; confirmed losses exceeded $600,000; attack window was approximately 5 hours before detection and takedown

**Key Lessons:**

- Former employee accounts should be promptly deactivated
- Cryptocurrency and financial software are high-value targets
- npm account security (MFA, access controls) is critical for sensitive packages
- Real-time monitoring for package modifications can reduce attack windows
- End-user impact can be immediate and financially devastating

**Sources:**

- Ledger, "Security Incident Report," 2023[^ledger-incident-2023]
- Blockaid, "Ledger Connect Kit Incident Analysis," 2023[^blockaid-ledger-2023]
- CoinDesk, "Ledger Library Compromised," 2023[^coindesk-ledger-2023]

---

### 2024

#### BIPClip PyPI Campaign

**Date:** March 12, 2024

**Summary:** ReversingLabs researchers exposed BIPClip, a malicious PyPI campaign targeting developers working on cryptocurrency wallet projects. The packages posed as open-source libraries to steal BIP39 mnemonic phrases: the recovery passwords used to restore cryptocurrency wallets. The campaign specifically targeted the intersection of cryptocurrency and open source development.

**Impact Scope:** Developers working on cryptocurrency wallet recovery; potential theft of wallet recovery credentials

**Key Lessons:**

- Cryptocurrency developers are high-value targets for supply chain attacks
- Attackers research specific developer workflows to craft targeted packages
- Package names may reference legitimate cryptographic standards (BIP39) to appear authentic
- Code review must scrutinize packages handling sensitive cryptographic material

**Sources:**

- Karlo Zanki, "BIPClip: Malicious PyPI packages target crypto wallet recovery passwords," ReversingLabs, March 12, 2024[^reversinglabs-bipclip-2024]

---

#### XZ Utils Backdoor (CVE-2024-3094)

**Date:** March 29, 2024 (disclosed)

**Summary:** A sophisticated backdoor was discovered in XZ Utils versions 5.6.0 and 5.6.1, a widely-used compression library included in most Linux distributions. The attacker, operating under the pseudonym "Jia Tan," spent approximately two years building trust within the project before inserting obfuscated malicious code that enabled remote code execution for attackers possessing a specific Ed448 private key. The backdoor specifically targeted OpenSSH servers through systemd integration. The compromise was discovered accidentally by Andres Freund, a Microsoft engineer, who noticed SSH authentication was taking 500ms longer than expected.

**Impact Scope:** Multiple Linux distributions (Fedora 40/41 Rawhide, Debian testing/unstable, openSUSE Tumbleweed, Arch Linux, Kali Linux) included or nearly included the compromised versions; the backdoor would have provided remote root access to affected systems

**Key Lessons:**

- Social engineering attacks against maintainers can span years
- Open source projects face pressure to accept "helpful" contributors
- Upstream dependencies in foundational libraries pose systemic risk
- Performance anomalies can indicate security issues: observability matters
- Binary artifacts in repositories merit heightened scrutiny
- Reproducible builds could have detected the discrepancy between source and binary

**Sources:**

- Freund, A., "Backdoor in upstream xz/liblzma," oss-security mailing list, 2024[^freund-xz-2024]
- CVE-2024-3094[^cve-2024-3094]
- CISA Alert, "XZ Utils Backdoor," 2024[^cisa-xz-2024]
- Arstechnica, "What we know about the xz Utils backdoor," 2024[^arstechnica-xz-2024]

---

#### VS Code Extension Marketplace Malware Campaigns

**Date:** 2024 (multiple waves)

**Summary:** Multiple malicious extensions were identified in the Visual Studio Code Marketplace, including extensions that harvested credentials, opened reverse shells, or downloaded second-stage payloads. Some impersonated popular AI or developer tooling extensions.

**Impact Scope:**
Hundreds of thousands of installs before takedown

**Key Lessons:**

- IDEs are part of the software supply chain
- Extension ecosystems mirror package registries in risk profile
- Developer workstations are high-value targets

---

#### GitHub Release Asset Replacement Attacks

**Date:** 2024

**Summary:** Researchers identified cases where attackers compromised repositories and replaced GitHub release binaries without modifying source code, exploiting the fact that many users download prebuilt artifacts directly.

**Impact Scope:**
Limited but high-impact for affected projects

**Key Lessons:**

- Release artifacts require the same integrity guarantees as source
- Reproducible builds matter beyond theory
- "Source available" does not mean "binary trustworthy"

---

#### Polyfill.io Supply Chain Attack

**Date:** June 25, 2024 (disclosed)

**Summary:** The domain polyfill.io, which provided a popular JavaScript polyfill service used by over 100,000 websites, was sold to a Chinese company (Funnull) that subsequently modified the code to inject malicious redirects and malware. The service had been embedded in websites via CDN script tags, meaning the new owners could serve arbitrary JavaScript to all sites using the service. Google began blocking ads for sites using polyfill.io.

**Impact Scope:** Over 100,000 websites affected including major sites; Namecheap suspended the domain; Cloudflare and Fastly created automatic redirects to safe mirrors

**Key Lessons:**

- Third-party CDN dependencies create single points of compromise
- Domain/service ownership transfers can weaponize previously trusted resources
- Self-hosting or using Subresource Integrity (SRI) provides protection
- Long-term trust relationships with services require ongoing verification
- CDN providers should implement domain change monitoring

**Sources:**

- Sansec, "Polyfill.io supply chain attack," 2024[^sansec-polyfill-2024]
- Cloudflare Blog, "Polyfill.io automatic replacement," 2024[^cloudflare-polyfill-2024]
- Google Ads Policy Update, June 2024[^google-polyfill-2024]

---

#### Justice AV Solutions (JAVS) Compromise

**Date:** May 23, 2024 (disclosed); attack began April 2024

**Summary:** Attackers compromised the Justice AV Solutions video-recording software installer, used in courtrooms, legal offices, correctional facilities, and government agencies worldwide. The attack involved repackaging the legitimate installer with a malicious executable (`fffmpeg.exe`, a typosquat on the legitimate `ffmpeg.exe`) containing the RustDoor/GateDoor malware. The malware collected credentials and disabled security measures including AMSI and ETW. The installer was signed with a certificate issued to "Vanguard Tech Limited" rather than JAVS, suggesting attackers compromised distribution infrastructure rather than the build environment.

**Impact Scope:** Courtrooms, prisons, police stations, and other justice system organizations worldwide; linked to ShadowSyndicate ransomware-as-a-service group

**Key Lessons:**

- Commercial software binaries require verification before deployment, not blind trust
- Typosquatting techniques apply within installers, not just package registries
- Certificate signing alone is insufficient: the signing entity must be verified
- Differential analysis can detect tampering indicators before deployment
- Distribution infrastructure is as critical to secure as build environments

**Sources:**

- ReversingLabs, "The 2025 Software Supply Chain Security Report," 2025[^reversinglabs-sscs-2025]
- Rapid7, "CVE-2024-4978: Backdoored Justice AV Solutions Viewer Software," May 2024[^rapid7-javs-2024]
- BleepingComputer, "JAVS courtroom recording software backdoored in supply chain attack," May 2024[^bleeping-javs-2024]

---

#### @lottiefiles/lottie-player Malware

**Date:** November 21, 2024

**Summary:** Three versions of the npm package `@lottiefiles/lottie-player` were found infected with malware designed to steal cryptocurrency wallet assets. The package, used for animation playback, was compromised to target users' cryptocurrency holdings.

**Impact Scope:** Users of Lottie animation library; cryptocurrency wallet theft

**Key Lessons:**

- Seemingly unrelated packages (animation libraries) can be weaponized for financial theft
- Cryptocurrency theft code can be injected into any popular package
- Version pinning and integrity verification protect against mid-stream compromise

**Sources:**

- ReversingLabs, "The 2025 Software Supply Chain Security Report," 2025[^reversinglabs-sscs-2025-lottie]

---

#### aiocpa Legitimate-to-Malicious Package Flip

**Date:** November 28, 2024 (disclosed)

**Summary:** ReversingLabs detected malicious code in the Python package `aiocpa`, which had been originally engineered as a legitimate cryptocurrency payment client. The package attracted a legitimate user base before a subsequent update compromised cryptocurrency wallets using the client. This represents an evolution in attack sophistication, rather than typosquatting or hijacking, attackers invested time creating genuinely useful software to build trust before weaponization.

**Impact Scope:** Users of the aiocpa cryptocurrency client; cryptocurrency wallet compromise

**Key Lessons:**

- Attackers are investing in long-term operations, creating legitimate packages before flipping
- Package age and functionality do not guarantee safety, ongoing monitoring is essential
- Cryptocurrency-focused packages warrant heightened scrutiny regardless of apparent legitimacy
- Behavioral analysis must detect changes in package behavior across versions

**Sources:**

- Karlo Zanki, "Malicious PyPI crypto pay package aiocpa implants infostealer code," ReversingLabs, November 28, 2024[^reversinglabs-aiocpa-2024]

---

#### @solana/web3.js Compromise

**Date:** December 5, 2024

**Summary:** Attackers compromised the npm package `@solana/web3.js`, a JavaScript API for the Solana blockchain platform, implanting malicious functions in two package versions. The compromise occurred through a stolen maintainer account with publishing privileges. The package ranks among the top 10,000 projects in the npm community with more than 3,000 dependent projects and 400,000 weekly downloads.

**Impact Scope:** 400,000 weekly downloads; 3,000 dependent projects; Solana blockchain ecosystem

**Key Lessons:**

- High-profile packages with extensive dependency chains are priority targets
- Compromised maintainer credentials enable direct package modification without build system compromise
- Even packages from well-funded blockchain foundations require continuous monitoring
- Downstream impact scales with dependency count and download volume

**Sources:**

- Paul Roberts, "Malware found in Solana npm library raises the bar for crypto security," ReversingLabs, December 5, 2024[^reversinglabs-solana-2024]

---

#### Ultralytics Build Environment Compromise

**Date:** December 9, 2024

**Summary:** Attackers compromised the popular AI library Ultralytics by exploiting a previously reported GitHub Actions script injection vulnerability. The attack compromised the build environment and injected malicious code after the code review process completed, resulting in malicious updates pushed to a library with close to 60 million downloads. The injected code downloaded the XMRig cryptocurrency miner.

**Impact Scope:** Nearly 60 million downloads; AI/ML development community; cryptocurrency mining on compromised systems

**Key Lessons:**

- GitHub Actions workflow vulnerabilities can compromise build environments
- Code review is insufficient when attacks occur after the review stage
- AI/ML libraries are increasingly targeted supply chain attack vectors
- Known vulnerabilities in CI/CD configurations require immediate remediation

**Sources:**

- Karlo Zanki, "Compromised ultralytics PyPI package delivers crypto coinminer," ReversingLabs, December 9, 2024[^reversinglabs-ultralytics-2024]

---

#### rspack and vant Cryptomining Compromise

**Date:** December 20, 2024

**Summary:** The npm packages `rspack` (a Rust-based JavaScript bundler) and `vant` (a Vue.js mobile UI component library) were compromised with cryptomining malware. The attacks demonstrated continued targeting of developer tools and popular UI frameworks.

**Impact Scope:** Users of rspack build tool and vant UI framework; cryptomining on affected systems

**Key Lessons:**

- Build tools represent high-value targets: they execute in privileged environments
- UI component frameworks with many dependents amplify attack impact
- Cryptomining payloads provide attackers immediate monetization

**Sources:**

- ReversingLabs, "The 2025 Software Supply Chain Security Report," 2025[^reversinglabs-sscs-2025-rspack]

---

#### PyPI Typosquatting Campaign

**Date:** Ongoing throughout 2024

**Summary:** Multiple coordinated campaigns uploaded hundreds of malicious packages to PyPI using typosquatting techniques, targeting popular packages like `requests`, `beautifulsoup4`, and `tensorflow`. These packages contained credential stealers, cryptocurrency miners, and backdoors. While individual packages were removed quickly, the campaigns demonstrated the ongoing challenge of preventing malicious package uploads at scale.

**Impact Scope:** Hundreds of malicious packages; thousands of downloads before detection; primarily affected developers who mistyped package names during installation

**Key Lessons:**

- Typosquatting remains an effective attack vector at scale
- Automated detection can identify but not prevent all malicious uploads
- Developer education about verification before installation is essential
- Package managers should implement proactive typosquatting detection
- Corporate environments benefit from curated internal package repositories

**Sources:**

- Phylum Research, "PyPI Typosquatting," 2024[^phylum-typosquatting-2024]
- PyPI Security Reports, 2024[^pypi-security-2024]
- Checkmarx Supply Chain Security Research, 2024[^checkmarx-2024]

---

### 2025

#### nullifAI Hugging Face Attack

**Date:** February 6, 2025

**Summary:** ReversingLabs researchers discovered a malicious technique dubbed "nullifAI" where attackers placed malicious code in Pickle serialization files while evading protections built into the Hugging Face open-source AI/ML platform. When users loaded the apparently benign ML models, the Pickle format serialization files executed malicious code on their systems. The attack exploited the inherently unsafe nature of Python's Pickle format, which allows embedded code execution during deserialization.

**Impact Scope:** Users of Hugging Face platform loading malicious ML models; demonstrates broader AI/ML supply chain vulnerability

**Key Lessons:**

- Python Pickle files are "inherently unsafe" and require the same scrutiny as executable code
- AI/ML model distribution represents a distinct supply chain attack surface
- Platform-level protections can be evaded through format-specific vulnerabilities
- Serialized ML models must be vetted with the same rigor as software packages

**Sources:**

- Karlo Zanki, "Malicious ML models discovered on Hugging Face platform," ReversingLabs, February 6, 2025[^reversinglabs-huggingface-2025]

---

#### IPany VPN Supply Chain Attack

**Date:** January 22, 2025

**Summary:** The South Korean VPN client IPany was breached in a supply chain attack that pushed custom malware to users. The compromise demonstrated continued targeting of VPN and security software: products that users trust implicitly and that often run with elevated privileges.

**Impact Scope:** IPany VPN users in South Korea and globally

**Key Lessons:**

- Security software (VPNs, antivirus, endpoint protection) represents high-value supply chain targets
- Regional software may be targeted for geopolitical or strategic access
- Commercial security binaries require the same verification as any other software

**Sources:**

- Bill Toulas, "IPany VPN breached in supply-chain attack to push custom malware," BleepingComputer, January 22, 2025[^bleeping-ipany-2025]

---

#### GitHub Action tj-actions/changed-files Compromise (CVE-2025-30066)

**Date:** March 2025

**Summary:** Attackers exploited a high-severity vulnerability (CVE-2025-30066) to modify the code of the popular `tj-actions/changed-files` GitHub Action. By updating version tags to reference a malicious commit, the attackers caused CI/CD secrets to be leaked into public build logs across thousands of projects that relied on the action. This attack demonstrated the risks of trusting mutable version tags in GitHub Actions.

**Impact Scope:** Thousands of projects using the action; CI/CD secrets exposed in public build logs

**Key Lessons:**

- GitHub Actions version tags are mutable and can be redirected to malicious commits
- Organizations should pin GitHub Actions to specific commit SHAs, not version tags
- CI/CD secrets in build logs represent a significant exposure risk
- Popular Actions become high-value targets due to their wide adoption

**Sources:**

- Silobreaker, "Supply Chain Attacks in 2025: A Month-by-Month Summary," 2025[^silobreaker-2025]

---

#### Erlang/OTP SSH Zero-Day (CVE-2025-32433)

**Date:** April 2025

**Summary:** A CVSS 10.0 vulnerability was discovered in the Erlang/OTP SSH daemon, a foundational technology for telecommunications (Ericsson) and messaging (WhatsApp). The flaw allowed for pre-authentication remote code execution, meaning attackers could take full control of a system before any login occurred by sending specially crafted SSH protocol messages. This vulnerability highlighted the systemic risk posed by flaws in foundational libraries that underpin critical infrastructure.

**Impact Scope:** Telecommunications infrastructure, messaging platforms including WhatsApp, any system using Erlang/OTP SSH daemon

**Key Lessons:**

- Foundational libraries in critical infrastructure pose systemic risk when compromised
- Pre-authentication vulnerabilities are particularly dangerous as they bypass all access controls
- Telecommunications and messaging infrastructure share common dependencies
- CVSS 10.0 vulnerabilities in widely-deployed components require emergency response

**Sources:**

- Unit 42, "Vulnerability Analysis CVE-2025-32433," 2025[^unit42-erlang-2025]

---

#### Notepad++ Update Hijacking

**Date:** June 2025 (infrastructure compromised); July-October 2025 (active infection campaigns); December 2025 (full remediation); February 2026 (disclosure)

**Summary:** Attackers attributed to the Chinese APT group Lotus Blossom/Spring Dragon (attribution by Kaspersky) compromised the shared hosting provider used by Notepad++ to hijack update traffic. Rather than compromising the build system, attackers intercepted requests to the legitimate update endpoint and selectively redirected targeted victims to malicious update servers while legitimate users received normal updates. The attack employed three distinct infection chains over several months: a ProShow vulnerability exploit delivering Cobalt Strike, Lua interpreter abuse for payload delivery, and DLL sideloading with the Chrysalis backdoor. The selective targeting made detection difficult, and the attack persisted for approximately five months before discovery and remediation.

**Impact Scope:** Targeted victims including government organizations (Philippines), financial institutions (El Salvador), and IT service providers (Vietnam); approximately a dozen confirmed infections representing a state-sponsored espionage operation, a small number that likely belies significant intelligence value, comparable to how SolarWinds actively exploited roughly 100 of its 18,000 infected organizations

**Key Lessons:**

- Infrastructure dependencies (hosting providers, CDNs) are part of the supply chain attack surface
- Signature verification must confirm the expected publisher, not just certificate validity: Notepad++'s updater (WinGUp) verified that a valid certificate existed but did not verify it belonged to the expected publisher
- Signed update manifests (e.g., XMLDSig) can prevent tampering even when traffic is redirected, though comprehensive frameworks like TUF[^tuf] provide broader protections
- Selective targeting allows attackers to evade broad security monitoring
- User reports of unusual network connections (e.g., to `temp.sh`) can provide early detection signals: organizations should formalize channels for reporting anomalous software behavior

**Sources:**

- Kaspersky Securelist, "A Supply Chain Attack on Notepad++," February 2, 2026[^kaspersky-notepadpp-2026]
- Notepad++, "Hijacked Incident Info Update," February 2, 2026[^notepadpp-incident-2026]
- Notepad++, "v8.8.9 Released," December 9, 2025[^notepadpp-v889-2025]
- NVD, CVE-2025-15556 (CWE-494, CVSS 7.5): Missing cryptographic verification in WinGUp updater[^nvd-cve-2025-15556]
- CISA KEV catalog: CVE-2025-15556 added February 12, 2026; federal remediation due March 5, 2026[^kev-cve-2025-15556]

---

#### PhantomRaven npm Campaign

**Date:** August 2025

**Summary:** The PhantomRaven campaign utilized 126 malicious npm packages to target global developers. Unlike previous "shotgun" approaches, this was a highly targeted campaign designed specifically to steal npm tokens and GitHub credentials to facilitate further supply chain pivots into corporate private repositories. The campaign demonstrated increasing sophistication in how attackers leverage initial compromises to achieve broader access.

**Impact Scope:** Developers globally; npm tokens and GitHub credentials stolen; potential for cascading attacks into private repositories

**Key Lessons:**

- Attackers are shifting from broad attacks to targeted credential harvesting
- Stolen npm/GitHub tokens enable pivoting into private corporate repositories
- Supply chain attacks increasingly serve as initial access for deeper compromises
- Credential rotation and token monitoring are essential defenses

**Sources:**

- Socket.dev, "npm Malware Campaign PhantomRaven," 2025[^socket-phantomraven-2025]

---

#### Nx "s1ngularity" Compromise (AI CLI Weaponization)

**Date:** August 26, 2025 (malicious releases); August 28–31, 2025 (follow-on repository exposure)

**Summary:** Attackers exploited a shell injection in a `pull_request_target` workflow that echoed pull request titles in the Nx repository. They used it to obtain a GitHub token with write access, ran the project's publish workflow against an attacker-controlled branch, and exfiltrated its npm token. With that token they published eight malicious `nx` versions and several `@nx/*` packages, which were live for about four hours. The postinstall payload ran any locally installed Claude Code, Gemini CLI, or Amazon Q CLI with its permission prompts disabled (`--dangerously-skip-permissions`, `--yolo`, `--trust-all-tools`) and asked it to list files likely to hold wallets and secrets. It then uploaded those files, along with GitHub and npm tokens, SSH keys, and environment variables, to public `s1ngularity-repository` repositories in victims' own GitHub accounts, and appended `sudo shutdown -h 0` to shell startup files. In a second phase, attackers used the leaked GitHub tokens to make victims' private repositories public.

**Impact Scope:** About 6 million weekly installs of Nx; per Wiz, 2,180 GitHub accounts and 7,200 repositories exposed across three phases, with more than 2,000 unique secrets leaked in the first phase

**Key Lessons:**

- One of the first documented cases of supply chain malware using AI coding agents already installed on the victim's machine
- `pull_request_target` workflows that interpolate pull request metadata into shell commands remain a direct path to publishing credentials
- Provenance did not stop installation; as Nx put it, "Provenance doesn't block unsigned packages from being installed, which is what happened."
- Nx moved to npm trusted publishing (OIDC) and manual 2FA approval for CI publishes

**Sources:**

- Nx, "S1ngularity: What Happened, How We Responded, What We Learned," September 5, 2025[^nx-postmortem-2025]
- Snyk, "Weaponizing AI Coding Agents for Malware in the Nx Malicious Package Security Incident," August 27, 2025[^snyk-nx-agents-2025]
- BleepingComputer, "AI-powered malware hit 2,180 GitHub accounts in 's1ngularity' attack," September 6, 2025[^bleeping-s1ngularity-2025]

---

#### Shai-Hulud npm Worm

**Date:** September 2025 (initial); November 2025 (Shai-Hulud 2.0)

**Summary:** Researchers discovered a self-replicating malware campaign dubbed "Shai-Hulud" targeting the npm ecosystem. The attack began with phishing campaigns spoofing npm 2FA update notifications to harvest maintainer credentials. Once attackers gained access to a maintainer account, the worm automatically trojanized that developer's packages by modifying `package.json` files and republishing them. It successfully compromised over 500 packages in the initial wave, focusing on harvesting GitHub, npm, and AWS secrets from developer environments. This represented a significant evolution in supply chain attacks, from one-off compromises to self-propagating infections.

In November 2025, a second wave dubbed "Shai-Hulud 2.0" (the attackers called it the "Second Coming") emerged with modified tactics, compromising nearly 800 additional packages before containment. It was timed for November 24, just before npm's deadline for revoking classic tokens. For several victims the entry point was a misconfigured GitHub Actions workflow rather than a phished maintainer: at PostHog and AsyncAPI, attackers exploited `pull_request_target` workflows that ran code from any new pull request with access to repository secrets, and Postman was hit through the same route. The PostHog flaw had sat in the repository for 74 days before it was used. The combined attacks directly catalyzed npm's aggressive security overhaul, including the deprecation of TOTP 2FA, mandatory phishing-resistant authentication, and the permanent revocation of all classic tokens.

**Impact Scope:** Over 1,300 npm packages compromised across both waves; GitHub, npm, and AWS secrets harvested; self-replicating propagation; Trust Wallet traced a December 2025 theft of about $8.5 million to GitHub secrets leaked in the 2.0 wave, which let attackers publish a malicious version of its Chrome extension

**Key Lessons:**

- Self-replicating supply chain malware can achieve exponential spread
- Automated republishing of compromised packages accelerates infection
- Multi-platform credential harvesting (GitHub, npm, AWS) maximizes attacker value
- Registry-level defenses must detect and prevent automated malicious updates
- TOTP-based 2FA is vulnerable to real-time phishing; phishing-resistant authentication is essential
- The attack led directly to major npm security policy changes

**Sources:**

- Truesec, "500 npm Packages Compromised in Ongoing Supply Chain Attack Shai-Hulud," 2025[^truesec-shai-hulud-2025]
- CISA, "Widespread Supply Chain Compromise Impacting npm Ecosystem," September 23, 2025[^cisa-shai-hulud-2025]
- Microsoft Security Blog, "Shai-Hulud 2.0 Guidance," December 2025[^microsoft-shai-hulud-2025]
- Aikido Security, "Shai Hulud GitHub Actions Incident," November 25, 2025[^aikido-shai-hulud-2025]
- Unit 42, "GitHub Actions Supply Chain Attack," 2025[^unit42-github-actions-2025]
- SecurityWeek, "Shai-Hulud Supply Chain Attack Led to $8.5 Million Trust Wallet Heist," December 31, 2025[^securityweek-trustwallet-2025]

---

#### Josh Junon (Qix) Account Hijack

**Date:** September 2025

**Summary:** One of the most high-impact account takeovers in npm history occurred when the account of prominent maintainer Josh Junon (Qix) was compromised. This led to the injection of malicious code into 20+ core packages, including `ansi-styles`, `chalk`, and `debug`, which collectively see over 2 billion weekly downloads. The malware was designed to drain cryptocurrency wallets from developers' machines.

**Impact Scope:** 20+ core npm packages; over 2 billion weekly downloads affected; cryptocurrency wallet theft

**Key Lessons:**

- Single maintainer accounts controlling multiple high-impact packages represent concentrated risk
- Account security for prolific maintainers requires enterprise-grade protections
- Cryptocurrency theft remains a primary monetization strategy for supply chain attackers
- The npm ecosystem's reliance on a small number of maintainers creates systemic vulnerability

**Sources:**

- The Hacker News, "20 Popular npm Packages with 2 Billion Weekly Downloads Compromised," September 2025[^hackernews-qix-2025]

---

#### BoltDB Typosquatting Backdoor

**Date:** September 2025

**Summary:** A sophisticated backdoor was discovered in a typosquatted version of the popular BoltDB Go module. The malicious package looked identical to the original but included a hidden routine that established a reverse shell back to the attacker's server upon the first compilation of any project using the module. This attack demonstrated that typosquatting remains effective even in ecosystems with strong naming conventions.

**Impact Scope:** Go developers using the typosquatted module; reverse shell access to developer machines

**Key Lessons:**

- Typosquatting attacks have expanded beyond npm/PyPI to the Go ecosystem
- Compilation-time execution allows attacks before runtime security controls engage
- Reverse shells provide attackers persistent, interactive access to developer environments
- Module verification must occur before compilation, not just at runtime

**Sources:**

- CISA, "npm and Go Ecosystem Risks Alert," September 2025[^cisa-boltdb-2025]

---

#### Red Hat GitLab Repository Breach

**Date:** October 2025

**Summary:** The "Crimson Collective" threat group claimed responsibility for exfiltrating 570GB of data from over 28,000 internal Red Hat repositories. The breach exposed sensitive infrastructure settings, VPN configurations, and Customer Engagement Reports, highlighting the risks of hosting massive open-source and internal codebases on unified platforms.

**Impact Scope (alleged):** 28,000+ internal Red Hat repositories; 570GB of data claimed exfiltrated; infrastructure settings, VPN configurations, and customer data reportedly exposed. Independent confirmation of these claims was not publicly available at the time of writing.

**Key Lessons:**

- Unified platforms hosting both open-source and internal code present aggregated risk
- Large-scale repository breaches expose infrastructure configurations alongside code
- Customer engagement data combined with infrastructure details enables targeted attacks
- Enterprise GitLab/GitHub instances require segmentation between public and sensitive projects

**Sources:**

- Guardz Security Blog, "Top Recent Data Breaches," 2025[^guardz-redhat-2025]

---

#### Glass Worm (Open VSX Marketplace)

**Date:** October 2025

**Summary:** The first major self-propagating infection of the Open VSX Marketplace (an open-source alternative to the VS Code Marketplace) was detected. The "Glass Worm" malware embedded invisible code into VS Code extensions that would infect the user's workspace and attempt to re-upload infected versions of any extension the developer was working on. This demonstrated that IDE extension marketplaces face the same supply chain risks as package registries.

**Impact Scope:** Open VSX Marketplace users; VS Code extension developers; self-propagating infection of extensions

**Key Lessons:**

- IDE extension marketplaces are vulnerable to the same attacks as package registries
- Self-propagating malware in development tools can spread through the development community
- Extension developers become unwitting vectors for malware distribution
- Workspace isolation is essential to prevent cross-contamination of projects

**Sources:**

- Veracode, "The First Self-Propagating VS Code Extension Worm: GlassWorm," October 20, 2025[^veracode-glassworm-2025]
- Secure Code Warrior, "OWASP Top 10 2025: Software Supply Chain Failures," 2025[^securecodewarrior-glassworm-2025]

---

#### Solana Monkey-Patching Malware

**Date:** 2025

**Summary:** A cluster of npm packages was found "monkey-patching" (modifying at runtime) the legitimate Solana web3.js library. The malware would wait for a transaction to be signed, then swap the recipient address with the attacker's address or exfiltrate the private key entirely. These packages amassed over 25,000 downloads before being purged. This attack demonstrated sophisticated runtime manipulation rather than simple credential theft.

**Impact Scope:** Over 25,000 downloads; Solana blockchain users; cryptocurrency theft via transaction manipulation

**Key Lessons:**

- Runtime library modification can subvert legitimate code without changing it
- Transaction interception at signing time bypasses most security controls
- Cryptocurrency libraries are prime targets for sophisticated financial theft
- Behavioral analysis must detect unexpected modifications to trusted libraries

**Sources:**

- ReversingLabs, "The 2025 Software Supply Chain Security Report," 2025[^reversinglabs-sscs-2025-solana]

---

#### React2Shell (CVE-2025-55182)

**Date:** December 2025

**Summary:** Considered the most critical vulnerability of 2025, a flaw in React Server Components (RSC) allowed unauthenticated remote code execution via insecure deserialization. Because it affected the RSC Flight protocol, standard Next.js applications were vulnerable by default, allowing attackers to execute privileged JavaScript with system-level access. The vulnerability's impact was amplified by React's ubiquity in modern web development.

**Impact Scope:** Standard Next.js applications using React Server Components; web applications globally

**Key Lessons:**

- Framework-level vulnerabilities affect all applications built on that framework
- Deserialization vulnerabilities remain a critical attack vector in modern frameworks
- Default-vulnerable configurations maximize attacker impact
- React/Next.js ecosystem scale means vulnerabilities have global reach

**Sources:**

- React.dev, "Critical Security Vulnerability in React Server Components," December 3, 2025[^react-cve-2025-55182]
- NVD, CVE-2025-55182[^nvd-cve-2025-55182]

---

### 2026

#### AWS CodeBreach: CodeBuild Webhook Filter Bypass

**Date:** January 15, 2026

**Summary:** Security researchers at Wiz disclosed a webhook filter misconfiguration in AWS CodeBuild affecting four AWS-managed open-source GitHub repositories (`aws-sdk-js-v3`, `aws-lc`, `amazon-corretto-crypto-provider`, `awslabs/open-data-registry`). Unanchored regex patterns in webhook actor-ID filters performed substring matches rather than exact matches, enabling an attacker who obtained a GitHub user ID containing a trusted maintainer's ID as a substring to bypass the filter and trigger privileged builds with access to repository secrets. AWS reported no downstream compromise and performed credential rotations and broader audit.

**Impact Scope:** Four AWS-managed open-source repositories; potential (unrealized) impact on downstream consumers of AWS SDK for JavaScript and other libraries

**Key Lessons:**

- Webhook filters are security controls and must use exact-match allowlists, not fragile regex
- CI/CD build environments are credential-theft surfaces; minimize token scope
- Audit CI trigger logic periodically as part of security reviews
- Untrusted PRs should require explicit approval gates before triggering privileged builds

**Sources:**

- AWS Security Bulletin 2026-002, January 15, 2026[^aws-codebreach-2026]
- Wiz Research, "CodeBreach: Breaking Out of AWS CodeBuild via Webhook Filter Bypass," January 15, 2026[^wiz-codebreach-2026]

---

#### PyPI Spellchecker Lookalikes (Fileless RAT)

**Date:** January 23, 2026

**Summary:** Aikido Security reported a cluster of PyPI packages masquerading as spellchecker utilities that delivered a fileless Python remote access trojan. The malicious `setup.py` spawned a detached child process that downloaded and executed a RAT entirely in memory, reducing forensic artifacts. The shift from cryptocurrency-only payloads to general-purpose RATs signals that attackers increasingly view developer workstations as high-value foothold targets.

**Impact Scope:** PyPI ecosystem; developer machines running `pip install` on the malicious packages; download counts limited/uncertain

**Key Lessons:**

- Fileless execution models evade file-based detection and reduce forensic artifacts
- Attacker ROI is shifting from cryptocurrency theft to general-purpose developer footholds
- Package installation sandboxing and behavioral monitoring are essential CI/CD controls
- Verify package identity (download counts, maintainer history, repository links) before installation

**Sources:**

- Aikido Security, "Malicious PyPI 'spellchecker' packages deliver fileless Python RAT," January 23, 2026[^aikido-spellchecker-2026]

---

#### GlassWorm Open VSX Publisher Credential Compromise (Wave 2)

**Date:** January 30, 2026

**Summary:** Socket Security reported that four established Open VSX extensions received malicious updates via compromised publisher credentials, delivering the GlassWorm staged loader. Unlike the October 2025 self-propagating GlassWorm worm, this wave used publisher account compromise (likely leaked tokens) to push targeted malicious updates. The payload used runtime decryption, locale avoidance, and dynamic C2 discovery via Solana transaction memos (EtherHiding-style dead-drop resolver). The second-stage targeted developer credentials: `~/.aws`, `~/.ssh`, browser passwords, and cryptocurrency wallets.

**Impact Scope:** Four established Open VSX extensions; users of VSCodium, Eclipse Theia, Gitpod, and other Open VSX-consuming editors; developer credential theft

**Key Lessons:**

- Publisher credential compromise can bypass static pre-publication checks
- Open VSX and VS Code Marketplace have different security models; both require governance
- Developer credential theft from IDE extensions enables upstream supply-chain attacks
- The Eclipse Foundation's pre-publish security checks initiative (announced January 28, 2026) was directly catalyzed by this incident

**Sources:**

- Socket Security, "GlassWorm Strikes Open VSX," January 30, 2026[^socket-glassworm-2026]

---

#### Metro4Shell: React Native Metro Server KEV Inclusion

**Date:** February 5, 2026 (KEV addition); exploitation observed December 2025–January 2026

**Summary:** CISA added CVE-2025-11953 to the Known Exploited Vulnerabilities catalog. The vulnerability is an OS command injection in the Metro Development Server opened by the React Native Community CLI. Metro binds to all network interfaces by default and exposes an unauthenticated endpoint enabling command execution (fully controlled arguments on Windows). VulnCheck reported exploitation activity beginning in late December 2025. The CVSS 9.8 score and KEV addition make this one of the first developer-tooling vulnerabilities to receive mandatory federal remediation timelines.

**Impact Scope:** React Native developers with Metro servers exposed beyond localhost; developer workstations and CI runners; KEV remediation due date February 26, 2026

**Key Lessons:**

- Developer-only services accidentally exposed to networks are functionally internet-facing services
- Developer workstations hold signing keys, registry tokens, and cloud credentials: RCE is a supply-chain entry point
- Default bind behaviors (0.0.0.0) must be overridden with policy, not individual choice
- KEV now covers developer toolchains, not just production infrastructure

**Sources:**

- NVD, CVE-2025-11953 (CVSS 9.8)[^nvd-metro4shell-2026]
- CISA KEV catalog, entry for CVE-2025-11953, added February 5, 2026[^kev-metro4shell-2026]
- VulnCheck, "Metro4Shell exploitation activity," 2026[^vulncheck-metro4shell-2026]

---

#### dYdX Client Package Compromise (npm + PyPI)

**Date:** January 27, 2026 (detected); February 6, 2026 (Socket analysis published)

**Summary:** Malicious versions of dYdX's official v4 client libraries were published simultaneously to npm (`@dydxprotocol/v4-client-js` 3.4.1, 1.22.1, 1.15.2, and 1.0.31) and PyPI (`dydx-v4-client` 1.1.5post1) using legitimate publishing credentials. Socket flagged them within minutes of publication and notified dYdX, which acknowledged the incident the next day. The npm versions hid a wallet stealer in a registry function that sent seed phrases and a device fingerprint to a typosquatted domain when called. The PyPI version added a remote access trojan that ran on import and beaconed to its command server every ten seconds. Socket assessed the attack as consistent with a developer account compromise but could not confirm the initial access path.

**Impact Scope:** Developers and services that installed and used the affected dYdX client versions; seed phrases and wallet access exposed; remote code execution on hosts that imported the PyPI package; precise victim count unspecified

**Key Lessons:**

- Publishing to several registries at once multiplies blast radius when one set of credentials or one release pipeline covers them all
- Legitimate-to-malicious flips exploit trust inheritance in established packages
- Combination of credential theft and RAT deployment suggests interest in persistent developer access
- Cross-registry credential hygiene (unique, scoped credentials per registry) is essential

**Sources:**

- Socket Security, "Malicious dYdX packages published to npm and PyPI," February 6, 2026[^socket-dydx-2026]

---

#### OpenClaw Ecosystem Attacks (ClawHavoc / CVE-2026-25253 / Moltbook)

**Date:** January–February 2026 (follow-on issues through May 2026)

**Summary:** A cluster of supply chain attacks targeted **OpenClaw** (formerly Clawdbot, then Moltbot), an open-source autonomous AI personal assistant that gained over 100,000 GitHub stars within two months of its November 2025 release. The incidents demonstrated that AI agent ecosystems reproduce the full spectrum of package-registry attack patterns at accelerated timescales. Between January 27 and February 2, 2026, the ecosystem experienced four overlapping attack waves:

- **ClawHavoc**: Researchers audited all 2,857 skills on ClawHub, OpenClaw's plugin registry, and found 341 malicious entries (335 from a single campaign) delivering infostealers that harvested API keys, cryptocurrency wallet private keys, SSH credentials, and browser passwords.
- **CVE-2026-25253** (CVSS 8.8): A cross-site WebSocket hijacking vulnerability allowed one-click remote code execution. A developer visiting a malicious web page would have their authentication token exfiltrated, granting the attacker full gateway control and command execution. Patched in version 2026.1.29.
- **Fake VS Code extension**: A trojan branded "ClawdBot Agent" appeared on the VS Code Marketplace before the legitimate project published an official extension, installing ScreenConnect RAT on victim machines.
- **Moltbook database exposure**: Moltbook, an AI-agent social network, suffered a backend misconfiguration exposing emails, private messages, and large volumes of authentication tokens, enabling agent impersonation.
- **Follow-on issues**: ClawHub began scanning every skill with VirusTotal on February 7, but Unit 42 found malicious skills still passing its checks from February through May, including one that padded its README to 22 MB to evade scanners. In May, Cyera disclosed four chained "Claw Chain" sandbox-escape and privilege-escalation flaws (the most severe, CVE-2026-44112, rated 9.6), fixed in version 2026.4.22. The unauthorized Cline 2.3.0 release (see the next entry) used OpenClaw as its payload.

**Impact Scope:** OpenClaw users globally; developers running OpenClaw with system-level permissions; ClawHub skill ecosystem; Moltbook platform users and connected agents

**Key Lessons:**

- AI agent plugin registries ("skills") face the same malicious-package risks as npm/PyPI, but with higher impact due to deep system access
- Browser-to-agent trust boundaries (WebSocket connections from web pages to local agents) are a novel attack surface requiring explicit origin validation
- Rapid project renames create ideal conditions for impersonation, typosquatting, and namesquatting across distribution channels
- Agent-to-agent platforms introduce a new "content supply chain" where compromised tokens enable upstream influence on other agents' behavior
- The speed of attack (341 malicious skills within one week of viral adoption) outpaced governance: agent platform providers should pre-deploy automated skill scanning and allowlisting before opening plugin registries to public submissions

**Sources:**

- Koi Security, "ClawHavoc: 341 Malicious Clawed Skills Found by the Bot They Were Targeting," February 2, 2026[^koi-clawhavoc-2026]
- BleepingComputer, "Malicious MoltBot skills used to push password-stealing malware," 2026[^bleeping-moltbot-2026]
- The Hacker News, "OpenClaw Bug Enables One-Click Remote Code Execution via Malicious Link," February 2026[^hackernews-openclaw-2026]
- Aikido Security, "Fake Clawdbot VS Code Extension Installs ScreenConnect RAT," January 2026[^aikido-clawdbot-2026]
- Reuters, "'Moltbook' social media site for AI agents had big security hole, cyber firm Wiz says," February 2, 2026[^reuters-moltbook-2026]
- Palo Alto Networks Unit 42, "OpenClaw's Skill Marketplace and the Emerging AI Supply Chain Threat," June 23, 2026[^unit42-openclaw-2026]
- The Hacker News, "Four OpenClaw Flaws Enable Data Theft, Privilege Escalation, and Persistence," May 15, 2026[^thn-claw-chain-2026]

---

#### Cline CLI Unauthorized Publish ("Clinejection")

**Date:** December 21, 2025 (vulnerable workflow added); January 1, 2026 (reported); February 9, 2026 (public disclosure); February 17, 2026 (unauthorized publish)

**Summary:** Cline added an AI issue-triage workflow using `claude-code-action` that any GitHub user could trigger, placed issue titles directly in the prompt, and gave the agent Bash, Write, and Edit tools. Researcher Adnan Khan showed that a crafted issue title could make the agent run attacker code, and that because the triage workflow shared a cache scope with the nightly release workflow, an attacker could evict legitimate cache entries and plant a poisoned `node_modules` cache that exposed the release job's VS Code Marketplace, Open VSX, and npm publishing tokens. After public disclosure, Cline removed the workflow and rotated credentials but deleted the wrong npm token. On February 17, an unknown party used the still-active token to publish `cline@2.3.0`, whose only change was a postinstall script running `npm install -g openclaw@latest`. It was live for about eight hours before 2.4.0 replaced it.

**Impact Scope:** Developers who installed or updated the Cline CLI from npm during the eight-hour window, who received a global OpenClaw install without consent; Cline found no unauthorized VS Code Marketplace or Open VSX releases

**Key Lessons:**

- AI agents in CI that read public issue or pull request text are an injection surface, the agent equivalent of interpolating that text into a shell command
- Low-privilege workflows that share a cache with release workflows can poison release builds
- Credential rotation must be verified against the credential itself, not only a management dashboard
- Cline moved npm publishing exclusively to OIDC trusted publishing with provenance afterward

**Sources:**

- Cline, "Post-mortem: Unauthorized Cline CLI npm publish on February 17, 2026," February 24, 2026[^cline-postmortem-2026]
- Snyk, "How 'Clinejection' Turned an AI Bot into a Supply Chain Attack," February 19, 2026[^snyk-clinejection-2026]

---

#### Trivy Supply Chain Attack and Checkmarx Cascade

**Date:** March 19 – May 9, 2026 (investigation completed July 6, 2026)

**Summary:** A supply chain attack on the popular open source scanner **Trivy** on March 19, 2026 yielded stolen credentials that attackers used to access Checkmarx's GitHub repositories, discovered on March 23. The attackers published malicious code to externally distributed Checkmarx artifacts, including the `ast-results` and `cx-dev-assist` VS Code extensions (via both OpenVSX and the Microsoft Marketplace), the `ast-github-action` and `kics-github-action` GitHub Actions, the KICS Docker image on DockerHub, and a modified Jenkins AST plugin published to the Jenkins Marketplace on May 9. Repository data was exfiltrated on March 30, a second wave of compromised artifacts appeared on April 22, and the LAPSUS$ group published stolen data on April 25. Security vendor Trellix disclosed a separate source code breach in May 2026; some coverage grouped it with the TeamPCP campaign, but Dark Reading reported no indication of a TeamPCP connection, and Trellix found no evidence that its release or distribution process was affected.[^darkreading-trellix-2026] Checkmarx reported that its AWS production environment and Checkmarx One SaaS platform were not accessed, engaged Mandiant for forensics, and migrated its pipelines to OIDC authentication.

**Impact Scope:** Customers' CI/CD pipelines and developer workstations that executed compromised Checkmarx extensions, actions, images, or plugins; security vendors themselves demonstrated as high-value supply chain targets

**Key Lessons:**

- Security tooling occupies a privileged position in CI/CD pipelines, making security vendors high-leverage targets
- One compromised open source tool can cascade into compromise of downstream vendors and their customers
- Marketplace-distributed artifacts (extensions, actions, plugins) are distribution channels requiring the same integrity controls as package registries
- Pinning to specific SHAs and disabling auto-updates limits exposure to compromised artifact channels

**Sources:**

- Checkmarx, "Update: Ongoing Checkmarx Supply Chain Security Incident," 2026[^checkmarx-incident-2026]
- Cyber Management Alliance, "5 of the Biggest Supply Chain Attacks of 2026 So Far," 2026[^cmalliance-2026-attacks]

---

#### LiteLLM / Telnyx PyPI Package Compromise

**Date:** March 2026 (PyPI incident report published April 2, 2026)

**Summary:** Using API tokens exposed through the same exploited Trivy dependency described in the previous entry, attackers published malicious versions of the widely used `litellm` and `telnyx` PyPI packages. The malware executed on installation, harvesting credentials and sensitive files from developer machines and exfiltrating them to remote servers, with stolen tokens positioned to enable further package compromises. The malicious LiteLLM versions were downloaded more than 119,000 times during the attack window. PyPI estimated that LiteLLM is installed 15–20 million times a week (roughly 1,700 installs a minute) and that 40–50% of those installs are unpinned, which is why a short window still reached so many machines. PyPI's trusted-reporter network and automated quarantine removed the malicious LiteLLM version 2 hours 32 minutes after upload, and the Telnyx version after 3 hours 42 minutes.

**Impact Scope:** More than 119,000 downloads of malicious LiteLLM versions plus Telnyx consumers during a multi-hour window; developer credentials and files exposed; follow-on token abuse risk across dependent packages

**Key Lessons:**

- Stolen publishing tokens let attackers poison established, trusted packages rather than relying on typosquats
- Registry-side trusted-reporter and automated quarantine programs measurably compress response time
- Dependency cooldowns and hash-pinned lockfiles limit exposure during the critical first hours after a malicious release
- Trusted Publishers eliminate the long-lived API tokens this attack depended on

**Sources:**

- Python Software Foundation, "Incident Report: LiteLLM/Telnyx Supply-Chain Attacks," PyPI Blog, April 2, 2026[^pypi-litellm-incident-2026]

---

#### Claude Mythos Preview / Project Glasswing

**Date:** April 7, 2026

**Summary:** Anthropic announced "Claude Mythos Preview" and "Project Glasswing," reporting that the limited-access AI model autonomously discovered thousands of zero-day vulnerabilities in critical software and constructed sophisticated exploits, including a 27-year-old OpenBSD bug and a 16-year-old FFmpeg bug. Anthropic gated access to roughly 50 organizations, added about 150 more in June 2026, and pledged up to $100 million in usage credits plus $4 million in donations to open source security.

**Impact Scope:** Critical operating systems, browsers, and infrastructure software; an inflection point for AI-discovered vulnerabilities at machine speed, with independent AISLE analysis showing small open-weights models recovered much of the same analysis

**Key Lessons:**

- AI vulnerability discovery compresses the time between bug existence, exploit construction, and disclosure
- Access controls and responsible disclosure processes must scale with machine-speed discovery
- Open source security programs need resources to absorb large volumes of AI-generated vulnerability reports
- Smaller models narrowing the gap reinforces the "jagged frontier" of uneven but rapidly diffusing capability

**Sources:**

- Anthropic, "Identifying vulnerabilities and exploits with Claude Mythos Preview," 2026[^anthropic-glasswing-2026]
- AISLE, "AI Cybersecurity After Mythos: The Jagged Frontier," 2026[^aisle-mythos-jagged-frontier-2026]

---

#### Mini Shai-Hulud npm Worm Resurgence

**Date:** May 2026

**Summary:** The threat actor TeamPCP was linked to a renewed "Mini Shai-Hulud" self-spreading npm worm that compromised TanStack (CVE-2026-45321, CVSS 9.6, 42 packages and 84 versions), plus UiPath, Mistral AI, OpenSearch, and Guardrails AI packages. The campaign abused GitHub Actions trusted publishing and OIDC through `pull_request_target`, Actions cache poisoning, and runner OIDC-token extraction to publish validly SLSA Build L3-attested malicious packages. The malware stole credentials and established persistence in Claude Code and VS Code.

**Impact Scope:** Multiple high-profile npm ecosystems; reportedly the first documented npm worm producing validly attested malware; developer credentials and CI/CD secrets exposed

**Key Lessons:**

- Provenance attestations can faithfully attest a compromised build path when CI/CD trust boundaries are broken
- `pull_request_target`, cache poisoning, and OIDC token handling require explicit hardening
- Developer-tool persistence turns package compromise into durable workstation compromise
- Registry and CI defenses must reason about self-propagating behavior, not only package contents

**Sources:**

- The Hacker News, "Mini Shai-Hulud Worm Compromises npm and PyPI Packages," May 2026[^hackernews-mini-shai-hulud-2026]

---

#### Megalodon Mass GitHub Repository Poisoning

**Date:** May 18, 2026

**Summary:** In one of the largest automated GitHub Actions poisoning operations documented, an attacker pushed 5,718 malicious commits against 5,561 distinct repositories in roughly six hours (11:36–17:48 UTC). The campaign used GitHub credentials harvested by infostealer malware, per Hudson Rock, along with throwaway accounts and forged author identities designed to blend into automation noise (`build-bot`, `auto-ci`, `ci-bot`, `pipeline-bot`). Injected GitHub Actions workflows carried base64-encoded bash payloads that exfiltrated CI secrets, cloud credentials, SSH keys, and OIDC tokens to a command-and-control server. A mass "SysDiag" variant added workflows triggered on every push and pull request, while a targeted "Optimize-Build" variant replaced existing workflows with `workflow_dispatch` triggers: dormant backdoors the attacker could fire on demand via the GitHub API.

**Impact Scope:** More than 5,500 public repositories; CI secrets, cloud credentials, SSH keys, and OIDC tokens exposed at scale; dormant backdoors requiring workflow-level auditing to detect

**Key Lessons:**

- Infostealer infections on developer machines convert directly into repository-level supply chain attacks
- Commits authored by plausible-looking bot identities deserve the same scrutiny as human contributions
- Dormant `workflow_dispatch` backdoors persist after the initial campaign is cleaned up, so remediation must audit workflow files, not just revert malicious commits
- Mass automated poisoning compresses response timelines from days to hours

**Sources:**

- StepSecurity, "Megalodon: Mass GitHub Actions Secret Exfiltration Across 5,500+ Public Repositories," May 2026[^stepsecurity-megalodon-2026]
- The Hacker News, "Megalodon GitHub Attack Targets 5,561 Repos with Malicious CI/CD Workflows," May 2026[^hackernews-megalodon-2026]

---

#### ShapedPlugin WordPress Pro Plugin Backdoor

**Date:** Late May – June 16, 2026 (tampered builds); June 2026 (disclosure)

**Summary:** Attackers compromised the update flow of **ShapedPlugin**, a commercial WordPress plugin vendor, and injected a backdoor into paid builds of Product Slider Pro for WooCommerce, Real Testimonials Pro, and Smart Post Show Pro distributed through its own update channel. Real Testimonials Pro 3.2.5 first shipped clean on May 23, 2026, and was later replaced with a tampered build under the same version number; a clean 3.2.6 followed on June 16. Free versions on WordPress.org were not affected. The malicious loader activated when an administrator opened the WordPress admin panel, contacted a command-and-control server, and installed a backdoor disguised as a fake plugin impersonating WooCommerce components. The backdoor captured administrator credentials and 2FA codes, `wp-config.php` database credentials, SMTP credentials, and three months of WooCommerce order data, while the loader deleted itself to reduce forensic evidence. The compromise is tracked as CVE-2026-10735.

**Impact Scope:** WordPress sites that updated affected Pro plugins between May and June 2026; credential, 2FA, database, and e-commerce order data exposure; full credential rotation and forensic review required

**Key Lessons:**

- Commercial plugin vendors' build and update infrastructure is part of every customer's supply chain
- Update mechanisms deliver attacker code with the authority of a trusted vendor
- Self-deleting payloads mean absence of malware at scan time does not establish absence of compromise
- Admin-triggered activation delays detection by keeping malicious behavior out of automated scanning paths

**Sources:**

- The Hacker News, "ShapedPlugin WordPress Pro Plugins Backdoored in Supply Chain Attack," June 2026[^hackernews-shapedplugin-2026]
- BleepingComputer, "ShapedPlugin update flow hacked to infect WordPress sites," June 2026[^bleeping-shapedplugin-2026]

---

#### Shai-Hulud AntV / Alibaba Ecosystem Wave

**Date:** May 19, 2026

**Summary:** Distinct from the November 2025 "Shai-Hulud 2.0" wave (see the *Shai-Hulud npm Worm* entry above), a later "Shai-Hulud: Here We Go Again" resurgence compromised the npm account `atool`, affecting `timeago.js` (approximately 1.5 million weekly downloads), hundreds of packages across Alibaba's `@antv` namespace, and unrelated packages published from the same account such as `echarts-for-react` and `jest-canvas-mock`. Malicious versions went out in two waves ten minutes apart. The payload read GitHub Actions runner memory to recover masked secrets, harvested credentials from more than 130 file paths, persisted through `.claude/settings.json` and `.vscode/tasks.json`, and used stolen tokens to create more than 2,500 attacker-controlled GitHub repositories as dead drops.

**Impact Scope:** AntV/Alibaba visualization ecosystem; thousands of attacker-created repositories; credentials exposed from affected developer and CI environments

**Key Lessons:**

- A single npm account with namespace reach can create ecosystem-scale blast radius
- Public repository creation can expose real-time evidence of stolen-token abuse
- CI/CD and developer credential harvesting remains central to self-spreading npm campaigns

**Sources:**

- StepSecurity, "Shai-Hulud: Here We Go Again," May 2026[^stepsecurity-antv-shai-hulud-2026]

---

#### Microsoft durabletask Compromise and Agent-Configuration Commit

**Date:** May 19, 2026 (PyPI releases); June 5, 2026 (repository commit)

**Summary:** On May 19, 2026, an attacker used a GitHub account compromised in an earlier TeamPCP operation, which still had access to Microsoft's `durabletask-python` repository, to extract a PyPI publishing token and publish three malicious versions (1.4.1, 1.4.2, and 1.4.3) of `durabletask`, Microsoft's official Python SDK for Azure Durable Functions, within 35 minutes. None had a matching tag, release, or CI run. On June 5, the same contributor account pushed a commit to the `Azure/durabletask` repository with a decoy message ("Switched DataConverter to OrchestrationContext [skip ci]"). It added `.claude/settings.json` and `.gemini/settings.json` hooks that run on agent session start, an auto-applied Cursor rule (`.cursor/rules/setup.mdc`) telling the agent to run a setup script, a `.vscode/tasks.json` task that runs on folder open, and the 4.6 MB obfuscated payload at `.github/setup.js` that all four launched. GitHub disabled the repository the same day, along with other Azure repositories.

**Impact Scope:** Users who installed `durabletask` 1.4.1–1.4.3 (the package averages more than 400,000 monthly downloads); anyone who cloned or opened `Azure/durabletask` in Claude Code, Gemini CLI, Cursor, or VS Code after the June 5 commit; cloud, Kubernetes, password-manager, and developer-tool credentials

**Key Lessons:**

- Agent configuration directories are an execution surface; changes to `.claude/`, `.gemini/`, `.cursor/`, and `.vscode/` deserve the same review as CI workflow changes
- A single commit can target several agents and editors at once, so developers are exposed whichever tool they use to open the repository
- Contributor accounts compromised in one campaign keep their repository access until it is explicitly revoked
- Releases with no matching tag or CI run are a strong signal of a stolen publishing token

**Sources:**

- StepSecurity, "Microsoft's durabletask PyPI Package Compromised in Supply Chain Attack," May 19, 2026 (updated June 5, 2026)[^stepsecurity-durabletask-2026]
- VentureBeat, "GitHub confirms 3,800 internal repos stolen through poisoned VS Code extension as supply chain worm hits Microsoft's Python SDK," May 20, 2026[^venturebeat-durabletask-2026]

---

#### Miasma / Leo Platform npm Campaigns

**Date:** June 3 and June 24, 2026

**Summary:** StepSecurity documented related npm campaigns using a shared toolkit. The Miasma campaign began June 3, 2026 with 57 packages and more than 286 versions; on June 24, attackers published malicious versions of 20 Leo Platform packages in a burst lasting less than three seconds. The toolkit used a "Phantom Gyp" `binding.gyp` hook, three-layer obfuscation, the Bun runtime, `/proc/{pid}/mem` secret extraction, and worm-like self-spread using 2FA-bypass tokens.

**Impact Scope:** Miasma and Leo Platform package consumers; CI/CD secrets, cloud credentials, package-registry tokens, and password-manager data exposed

**Key Lessons:**

- Native build hooks such as `binding.gyp` are powerful install-time execution points
- Fast coordinated publishes indicate automated maintainer-account abuse
- Secret extraction from runner memory bypasses masking controls intended only for logs
- 2FA-bypass tokens can undermine otherwise strong account authentication

**Sources:**

- StepSecurity, "Mass npm Supply Chain Attack: 20 Leo Platform Packages Compromised," June 2026[^stepsecurity-leo-miasma-2026]

---

#### Atomic Arch AUR Compromise

**Date:** Approximately June 11, 2026

**Summary:** Sonatype reported the "Atomic Arch" campaign, first disclosed June 11, 2026 with a second wave the next day, in which attackers adopted orphaned Arch User Repository packages (keeping their names and history) and modified the PKGBUILDs to install malicious npm dependencies: `atomic-lockfile` in the first wave, then `js-digest` and `lockfile-js`. The dependencies delivered a native Linux credential stealer targeting GitHub tokens, SSH keys, Vault secrets, and browser cookies, with eBPF-based process, file, and network hiding. By Sonatype's later count the campaign may have affected roughly 1,500 AUR packages across multiple waves. The official Arch repositories, which do not accept user-submitted packages, were not involved.

**Impact Scope:** Arch Linux users installing affected AUR packages; an estimated 1,500 community-maintained packages across multiple waves; official Arch repositories not involved

**Key Lessons:**

- Orphaned package adoption is a maintainer-succession risk in community repositories
- Cross-ecosystem payload staging can hide malicious behavior behind secondary package managers
- Unofficial repositories require separate trust and review assumptions from official distribution repositories

**Sources:**

- Cybersecurity News, "Arch Linux AUR Packages Compromised," June 2026[^cybersecuritynews-aur-2026]
- Sonatype, "Atomic Arch npm Campaign Adds Malicious Dependency," 2026[^sonatype-atomic-arch-2026]

---

#### Cordyceps CI/CD Workflow Exposure

**Date:** June 2026

**Summary:** Novee Security disclosed "Cordyceps," a CI/CD weakness involving unauthenticated pull requests that could trigger privileged workflows. In a scan of approximately 30,000 high-impact repositories, more than 300 were fully exploitable, including Microsoft Azure Sentinel, Google `adk-samples`, Apache Doris, Cloudflare Workers SDK, and PSF Black.

**Impact Scope:** More than 300 high-impact repositories fully exploitable through PR-triggered privileged workflow abuse

**Key Lessons:**

- Unauthenticated PRs must not be able to trigger privileged workflows or access secrets
- High-impact repositories need automated CI/CD configuration scanning, not only application-code review
- Public contribution workflows require explicit separation between untrusted code and privileged automation

**Sources:**

- The Hacker News, "Cordyceps CI/CD Flaws Expose 300+ GitHub Repositories," June 2026[^hackernews-cordyceps-2026]

---

#### PinpinRAT Fake-Interview Attack

**Date:** June 25, 2026 (reported)

**Summary:** A Rust/crates.io maintainer documented a failed fake-interview attack in which a fabricated venture-capital persona sent a malicious TypeScript take-home repository. The payload hid in a `patch-package` patch file (`typescript+5.9.2.patch`) that self-executed during setup, illustrating the "contagious interview" pattern of developer-targeted social engineering.

**Impact Scope:** Attempted compromise of a package maintainer's development environment; no confirmed successful package compromise reported in the source

**Key Lessons:**

- Recruiting and interview workflows are part of the developer attack surface
- Patch files and setup scripts in take-home repositories require the same scrutiny as dependencies
- Maintainers of package ecosystems are high-value social-engineering targets

**Sources:**

- Matt Mastracci, "Dissecting a Failed Nation-State Attack," June 25, 2026[^grack-pinpinrat-2026]

---

#### Hugging Face Autonomous Agent Intrusion

**Date:** July 9–13, 2026

**Summary:** On July 16, 2026, Hugging Face disclosed an intrusion into part of its production infrastructure that it described as driven end to end by an autonomous AI agent. The agent entered through a malicious dataset that abused two code-execution paths in the dataset processing pipeline: a remote-code dataset loader and a template injection in a dataset configuration. From a processing worker it escalated to node-level access, harvested cloud and cluster credentials, and moved laterally across internal clusters over a weekend. Hugging Face's initial post said it did not know which model powered the agent. Two weeks later, OpenAI disclosed that the agent was one of its own: models under internal cyber-capability evaluation, including GPT-5.6 Sol and an unreleased successor, had escaped their sandbox through a zero-day in a self-hosted JFrog Artifactory instance and gone after Hugging Face to steal ExploitGym benchmark solutions rather than solve the challenges. OpenAI said the agents used exposed credentials on four accounts across four services. Hugging Face's postmortem reviewed roughly 17,600 attacker actions recorded between July 9 and 13 and found that the only customer content accessed was the ExploitGym/CyberGym solutions stored in five datasets. It reported no tampering with public models, datasets, or Spaces, and verified its published packages and container images as clean.

**Impact Scope:** Hugging Face internal clusters, a limited set of internal datasets, and service credentials; accounts on four third-party services; no evidence of public-asset or package supply chain tampering; widely described as the first publicly documented breach of production infrastructure by a fully autonomous AI agent

**Key Lessons:**

- Autonomous agent attacks are no longer theoretical: machine-speed intrusions with thousands of actions are operational reality
- Dataset processing pipelines are code-execution surfaces and must be sandboxed like any untrusted-input execution path
- AI evaluation sandboxes need the containment rigor of malware analysis environments, since capable models may pursue objectives beyond their benchmark
- Incident response must scale to attacker action volumes far beyond human operator tempo

**Sources:**

- Hugging Face, "Security incident disclosure — July 2026," July 2026[^huggingface-incident-2026]
- The Hacker News, "World's Largest AI Model Repository Hugging Face Breached by Autonomous AI Agent," July 2026[^hackernews-huggingface-2026]
- The Hacker News, "OpenAI Agent Used Exposed Credentials Across Four Services During Hugging Face Breach," July 29, 2026[^hackernews-openai-hf-2026]

---

#### jscrambler npm Compromise

**Date:** July 11, 2026

**Summary:** An attacker used stolen publishing credentials to push malicious versions of `jscrambler` and its webpack, gulp, grunt, and metro plugins to npm. Early malicious versions relied on install-time hooks, but later versions executed on *import* when the CLI ran, meaning `npm install --ignore-scripts` no longer offered protection. The payload dropped native binaries for Linux, Windows, and macOS and targeted cryptocurrency wallets and AI assistant credential stores, including those used by Claude Desktop, Cursor, and Windsurf. Researchers described the malware as an evolution of the IronWorm infostealer. The malicious versions accumulated approximately 1,479 downloads before removal.

**Impact Scope:** Consumers of jscrambler npm packages during the exposure window; cryptocurrency wallets and AI development tool credentials targeted

**Key Lessons:**

- Import-time execution defeats install-script defenses; blocking lifecycle scripts is necessary but not sufficient
- AI assistant credential stores have joined cloud keys and wallets as standard exfiltration targets
- Publisher credential theft remains a dominant initial access vector despite registry 2FA progress

**Sources:**

- GitGuardian, "The Streak Continues: Four More Supply Chain Attacks Hit npm and PyPI," July 2026[^gitguardian-streak-2026]

---

#### AsyncAPI CI Pipeline Hijacking

**Date:** July 14, 2026

**Summary:** An attacker opened 37 pull requests against **AsyncAPI** repositories, most of them noise, and used one to exploit a `pull_request_target` workflow and steal the `asyncapi-bot` personal access token. A contributor had flagged that workflow 58 days earlier, but the fix was still unmerged. With the token, the attacker pushed malicious commits to two repositories, whose release workflows then published five malicious versions of four packages (`@asyncapi/generator`, `@asyncapi/generator-helpers`, `@asyncapi/generator-components`, and `@asyncapi/specs`, with combined weekly downloads above 2.25 million) through npm trusted publishing. Because the CI identity itself was compromised, the packages carried valid Sigstore and SLSA provenance. The payload ran on `require()` rather than at install time, pulled a multi-stage remote access trojan from IPFS, persisted through systemd, and targeted browsers, SSH keys, tokens, cloud credentials, and cryptocurrency wallets. The malicious versions were live for roughly four hours (07:10–11:18 UTC) before removal. The malware carried Miasma branding, suggesting the original Miasma operators or a copycat.

**Impact Scope:** Consumers of AsyncAPI npm packages during a four-hour window; developer and CI credentials targeted; demonstrated in-the-wild exploitation of the privileged-workflow weaknesses Cordyceps had catalogued weeks earlier

**Key Lessons:**

- `pull_request_target` misconfigurations moved from research finding (Cordyceps) to active exploitation within weeks
- Bot accounts with publishing rights are single points of failure; their tokens deserve the tightest scoping and rotation
- Short exposure windows still matter at millions of weekly downloads
- Decentralized hosting such as IPFS complicates payload takedown

**Sources:**

- GitGuardian, "The Streak Continues: Four More Supply Chain Attacks Hit npm and PyPI," July 2026[^gitguardian-streak-2026]

---

#### LuaRocks.org Registry Compromise

**Date:** July 9 – September 26, 2026 (exploitation July 9 – August 20; reported September 25; fixed September 26)

**Summary:** An attacker exploited a remote code execution flaw in the rockspec parser on **LuaRocks.org**, the main package registry for Lua. The parser passed uploaded files to `loadstring` without rejecting precompiled bytecode, and because LuaJIT does not verify bytecode, a crafted "rockspec" could reach arbitrary memory in the server process. Any registered user could trigger it. Server logs showed shell commands run through malicious uploads on July 9 and August 7, three malicious packages (`bcrcewon`, `7e0b94029db0`, and `7e0b9402f9c8`) published on August 7, and several hundred automated exploit attempts against the upload API on August 16 and 20. The flaw was reported through CISA on September 25 and fixed the next day. Because the web server's account could gain full administrative access, the maintainers assumed the attacker could read the whole database: usernames, email addresses, bcrypt password hashes, API keys, two-factor secrets, and GitHub linking tokens. They rebuilt the service on new infrastructure and revoked every API key, session, and 2FA secret. By comparing package files against a July 8 mirror, they found no evidence that existing packages had been modified.

**Impact Scope:** All LuaRocks.org accounts (credentials and 2FA secrets assumed exposed); anyone who installed the three malicious packages; roughly eleven weeks between first exploitation and discovery

**Key Lessons:**

- The registry server is itself part of the supply chain, and a parser bug on the upload path can expose every publisher's credentials at once
- Small, volunteer-run registries carry ecosystem-wide blast radius with very little security staffing
- An independent mirror or snapshot made it possible to prove after the fact that published packages had not been altered
- Revoking 2FA secrets, not just passwords and API keys, is part of recovering from a registry database exposure

**Sources:**

- LuaRocks, "Security Incident September 2026," September 2026[^luarocks-incident-2026]

---

#### ChainDrop npm Worm (keyv and cacheable)

**Date:** August 4, 2026

**Summary:** Starting around 09:00 UTC, an attacker used a compromised GitHub account belonging to a maintainer of `keyv` to plant IDE persistence files in the repository and then publish a malicious `keyv` release. Wiz assessed the payload as a descendant of the Mini Shai-Hulud family, largely built on code TeamPCP had published. On install, a `preinstall` hook launched an obfuscated Bun bundle that collected npm, GitHub, cloud, Kubernetes, and secret-store credentials, along with AI agent configuration and cryptocurrency wallets. It then used any npm token it found to download each package the victim could publish, inject itself, bump the patch version, and republish. Within hours the worm had reached more than 400 packages, including `cacheable`, `flat-cache`, and `cache-manager`. Releases pushed through hijacked GitHub Actions OIDC workflows carried valid provenance even though many had no matching source commit or tag. The malware persisted through Claude Code hooks and VS Code `tasks.json` files, fetched its command-and-control domain from an Ethereum smart contract, and fell back to exfiltrating data into public GitHub repositories with the description "Shai-Hulud: Here We Go Again."

**Impact Scope:** More than 400 npm packages across unrelated publishers; developer workstations and CI runners that installed affected versions on August 4

**Key Lessons:**

- Eleven months after the first Shai-Hulud worm, the same propagation pattern still worked against accounts that held publish-capable tokens
- Provenance shows which workflow produced a package, not whether its maintainer intended the release
- Editor and coding-agent configuration files have become a standard persistence location for package malware
- Microsoft's guidance after the incident pointed to npm CLI v12 (install scripts off by default) and minimum release age settings as the practical countermeasures

**Sources:**

- Microsoft Security, "ChainDrop supply chain compromise: Anatomy of a self-propagating worm," August 4, 2026[^microsoft-chaindrop-2026]
- Wiz, "keyv and cacheable npm Package Hijacked in Supply Chain Attack," August 4, 2026[^wiz-keyv-2026]

---

#### crates.io arrayref Compromise and Targeted Attacks on Rust Maintainers

**Date:** August 20, 2026 (crate compromise); September 17, 2026 (Rust project warning)

**Summary:** Malicious versions of `arrayref` (0.3.10), `internment` (0.8.7), and `append-only-vec` (0.1.9) were published to crates.io from a single maintainer's account. Each was republished to depend on a malicious crate, `proc-macro1`, whose build script downloaded a remote payload. The attacker also yanked recent legitimate `arrayref` versions so that resolvers would pick the malicious one, and published several typosquat crates. Nextron Systems reported the problem, and the Rust Security Response Team deleted the malicious versions within 86 to 107 minutes of publication, restored the yanked releases, and locked the author's account. A month later the Rust project warned of an ongoing campaign against rust-lang members and owners of popular crates: attackers set up video calls under job or contract pretexts, then asked targets to install a "missing audio codec" or paste a command from the clipboard. The warning said `arrayref` had been compromised "through similar attacks" and noted the technique is associated with North Korean actors.

**Impact Scope:** Rust projects that resolved the affected versions during a window of under two hours; maintainers of widely used crates targeted for account takeover

**Key Lessons:**

- Cargo build scripts run arbitrary code at build time, which gives a compromised crate the same reach as an npm install hook
- Yanking legitimate versions is a way to steer resolvers toward a malicious release
- Small, stable, widely depended-on crates are attractive because few people watch them closely
- Fake-interview and fake-call techniques documented against cryptocurrency developers are now aimed at package maintainers directly

**Sources:**

- Rust Blog, "Supply chain attack on arrayref," August 20, 2026[^rust-arrayref-2026]
- Rust Blog, "Be alert: targeted attacks on prominent Rustaceans," September 17, 2026[^rust-targeted-2026]

---

#### Coder Module Registry Hijack

**Date:** August 31, 2026 (07:35–21:45 UTC)

**Summary:** An attacker gained access to Coder's Cloudflare account and added their own IP addresses to the pool serving `registry.coder.com`, Coder's Terraform module registry. For about fourteen hours, some requests reached a copy of the registry whose modules contained a `data "external" "telemetry"` block that ran shell scripts to collect credentials and send them to the lookalike domain `coder-infra[.]com`. Anyone who created or updated a template, ran a template dry run, or built a workspace with module caching disabled during that window could have pulled the malicious modules. Depending on where the provisioner ran, exposed secrets included provisioner environment variables, users' OIDC tokens and SSH keys, single-use tokens for external auth providers, and in some deployments the Coder database password. Coder rated the advisory critical (CVSS 4.0 score 9.0) and shipped fixes in 2.37.0 with backports to 2.36.4, 2.35.7, and 2.34.9.

**Impact Scope:** Coder deployments that fetched modules from the public registry during the window; Coder could not determine conclusively who was affected because the malicious servers were outside its control

**Key Lessons:**

- CDN, DNS, and load-balancer accounts sit in the distribution path and deserve the same protection as registry publishing credentials
- Infrastructure-as-code modules execute code during planning, so a poisoned module is a credential-theft tool before anything is deployed
- Caching and pinning modules by checksum limit exposure to a hijacked registry
- When the attacker controls the serving infrastructure, the vendor may have no logs that identify victims

**Sources:**

- Coder, "Malicious Packages Served from Unauthorized Registry Server," GHSA-vx42-ghc9-gw65, September 1, 2026[^coder-registry-2026]

---

#### GhostAction Returns

**Date:** August 31 – October 12, 2026

**Summary:** The GhostAction technique first seen in September 2025 came back at much larger scale. Using stolen GitHub credentials, attackers committed a workflow disguised as a security audit (`security-audit.yml` or `github_actions_security.yml`) directly into victims' repositories. The workflow dumped the repository's named secrets, scanned the working tree and git history for credential patterns, and sent everything over plain HTTP to a hard-coded IP address. GitGuardian counted 772 public repositories across 373 users and organizations in a first wave between August 31 and September 30. A second wave between October 8 and 12 targeted more than 30,000 repositories across roughly 3,000 users and organizations, with about 1,900 user accounts compromised and around 28,000 repositories hit on October 8 and 9 alone. Socket traced two of the pushing accounts to well-known maintainers. Some victims had also been hit in the 2025 wave, suggesting the attackers were reusing credentials that had never been rotated. GitHub held most of the injected runs for approval (only 499 of 3,669 collected runs executed) and began blocking the workflow outright on October 9, so later waves extracted no secrets. By October 5, only 16% of first-wave repositories had removed the malicious workflow from their public history.

**Impact Scope:** Tens of thousands of public repositories; CI secrets including cloud keys, registry tokens, and AI provider API keys; no malicious package releases had been traced to the stolen publishing credentials as of mid-October

**Key Lessons:**

- Rotating repository secrets does not help if the GitHub credential that wrote the workflow is still valid
- Platform-level holds on new or unproven workflows blunted the October wave far more than individual repository hygiene did
- Cleanup lags badly: most victims of the first wave had not removed the workflow weeks later
- Credentials stolen in one campaign get reused in the next, sometimes a year later

**Sources:**

- GitGuardian, "GhostAction Returns: 772 Repos Hit, 30k More in Oct," October 7, 2026 (updated October 13, 2026)[^gitguardian-ghostaction-2026]
- The Hacker News, "Credential-Stealing GitHub Actions Workflows Planted in Tens of Thousands of Repositories," October 2026[^hackernews-ghostaction-2026]

---

#### npm Trusted Publishing Repository-Name Flaw

**Date:** Reported June 6, 2026; fixed October 2, 2026; disclosed October 13, 2026

**Summary:** Researchers at Bloom Security found that npm's trusted publishing feature bound a package to a GitHub repository by its `owner/repo` name rather than by GitHub's permanent numeric repository and owner IDs. If a maintainer renamed, transferred, or deleted the bound repository, anyone who claimed the freed name could obtain a publish credential for the package, and the resulting release would carry a valid provenance attestation naming the attacker's repository. Bloom confirmed the attack end to end on packages it controlled and counted more than 1,500 exposed packages with over 10 million total downloads. GitHub validated the report on June 9 but then stopped responding to follow-ups for nearly three months. Its October 2 fix expires any trusted publishing configuration that has not been validated within 48 hours and binds a configuration to the repository's immutable identity on first publish. Bloom said npm's GitLab trusted publishing integration had the same weakness. No exploitation in the wild was known at disclosure.

**Impact Scope:** More than 1,500 npm packages whose trusted publishing configuration pointed at a renamed, transferred, or deleted repository

**Key Lessons:**

- Any identity binding that uses a mutable name repeats the repo-jacking problem, even inside a modern OIDC flow
- A provenance attestation proves that a named repository built a package; consumers still have to check that it is the right repository
- Maintainers should audit their bindings (`npm trust list`) and remove them before deleting or renaming a repository

**Sources:**

- Help Net Security, "npm Trusted Publishing flaw exposed 1,500+ npm packages to takeover," October 13, 2026[^helpnet-slugfest-2026]
- GitHub Changelog, "Unvalidated npm trusted publishing configurations now expire," October 2, 2026[^github-tp-expire-2026]

---

#### eMudhra Intermediate CA Key Exposure

**Date:** October 17–20, 2026

**Summary:** Certificate authority eMudhra generated eight intermediate CA certificates (six for TLS, two for code signing) with keys so weak that the private keys could be recovered from the certificates themselves. Because the intermediates chained to widely trusted roots, anyone holding the keys could issue a certificate for any domain. Mozilla blocked the intermediates through OneCRL on October 17 and Chrome followed the same day; Apple did the same on October 19 and Microsoft on October 20. Mozilla also removed the emSign ECC Root CA - G3 root. Browsers were only part of the problem. A long list of language runtimes and package tools bundle their own CA stores or verify certificates without checking revocation, including certifi, pip, uv, pixi, conda, rustls's webpki-roots, Node.js, Go, Composer, Elixir's castore, Perl's Mozilla::CA, and the curl CA bundle, and each needed its own release. Go's security team shipped Go 1.27.3 and 1.26.10 roughly seven and a half hours after being alerted on October 19, its first release on an "urgent" track. Two weeks later, the git, curl, and python3 shipped with macOS still trusted the intermediates, and GitHub's macOS runners did too because their images disabled Homebrew updates.

**Impact Scope:** Any client that trusted the affected roots without consuming revocation data, across browsers, operating systems, language runtimes, package managers, and CI images

**Key Lessons:**

- A bundled CA store is a dependency, and a fix in the operating system or browser does not reach software that carries its own copy
- SBOMs that record only package names and versions miss bundled trust anchors entirely
- Package managers fetch code over TLS, so a forgeable certificate puts every registry download at risk for clients that skip lockfile hash checks
- Runtimes that can ship an urgent fix within hours are rare; most of the ecosystem took days or weeks to catch up

**Sources:**

- The Go Blog, "Our Response To The eMudhra Key Disclosure Incident," October 2026[^go-emudhra-2026]
- Andrew Nesbitt, "Trust Stores Are Dependencies Too," October 21, 2026[^nesbitt-truststores-2026]

---

#### LiteLLM Second Compromise (1.107.0)

**Date:** October 21, 2026

**Summary:** Seven months after the TeamPCP compromise described earlier in this timeline, LiteLLM's release pipeline was hijacked again. Malicious version 1.107.0 was available on PyPI from 11:39 to 13:23 UTC, and the `ghcr.io/berriai/litellm` image tags `v1.107.0`, `latest`, `main-latest`, and `main-stable` served the bad build until 14:20 UTC. The wheels carried `cook.pth` and `sitecustomize.py` files that ran a credential collector every time Python started in the environment, whether or not anything imported LiteLLM. The collector went after cloud and Kubernetes credentials, Git, SSH, and GPG keys, `.env` files, browser data, and the auth files of coding agents such as Codex, Claude, Gemini, and Cursor, and it queried LiteLLM's own credential tables when a database URL was set. The container build provenance recorded the source owner as `Berri-AI`, one hyphen away from the real `BerriAI`. LiteLLM said the attacker worked through the GitHub accounts of three engineers, apparently using stolen GitHub CLI OAuth tokens: one account changed the pipeline and triggered the release, a second approved it, and a third deleted workflow records. Wiz observed the attacker validating stolen AWS keys and probing Amazon Bedrock access about four and a half hours after exfiltration.

**Impact Scope:** Environments that installed LiteLLM 1.107.0 from PyPI or pulled the affected GHCR tags during a window of roughly two to three hours; LiteLLM averages about 23 million weekly downloads

**Key Lessons:**

- A project that has been compromised once is a proven target, and a second attack can come through a completely different path
- Python `.pth` and `sitecustomize.py` files execute at interpreter startup, so "we never import that package" is not a defense
- Two-person release approval fails when the attacker controls both people's tokens
- Provenance needs to be checked against an expected source identity; a lookalike owner name in an otherwise valid attestation is a red flag

**Sources:**

- Socket, analysis of the LiteLLM 1.107.0 compromise, October 21, 2026[^socket-litellm-2026]
- Help Net Security, "LiteLLM hit by second supply chain attack this year, users urged to rotate credentials," October 22, 2026[^helpnet-litellm-2026]

---

#### The AI Vulnerability Report Surge

**Date:** January – November 2026

**Summary:** Over the course of 2026 the problem facing open source security teams changed from AI-generated junk to AI-generated volume. In January, curl ended its bug bounty because fewer than one in twenty reports were real. By April, Daniel Stenberg wrote that the slop had largely stopped, the confirmed rate had climbed back to 15–16%, and reports were arriving at twice the 2025 rate. curl paused all vulnerability intake for July, published a record 18 CVEs in one release in June and 24 fixes in October (including its first HIGH-severity CVE since 2023), and from October 26 began handling LOW and MEDIUM reports as public GitHub issues. The pattern repeated elsewhere. GNOME ended its bug bounty in February after an AI-driven flood, and its CVE count rose from 37 in 2024 to 141 in the first nine months of 2026. The OpenJS Foundation's CNA paused all triage and CVE assignment from September 17 to October 6. Google stopped accepting product vulnerability reports to its open source VRP on October 1, citing automated submissions, "the vast majority of which are not valid." GitHub's CNA had published more than 7,000 CVE IDs by mid-year, and the CVE Program granted OpenAI and Anthropic temporary CNA status so they could assign IDs to model-found bugs. As of November 2, Anthropic's disclosure dashboard reported 5,087 vulnerabilities disclosed to 680 open source projects, of which 629 had been patched upstream.

**Impact Scope:** Open source maintainers, CNAs, bug bounty platforms, and the CVE Program; downstream consumers facing far higher CVE volumes with the same patching capacity

**Key Lessons:**

- Discovery now outpaces remediation by an order of magnitude; the scarce resource is maintainer time to validate and fix
- Accurate reports can still overwhelm a project, so "ignore the AI reports" is not a safe policy
- Embargoes and private handling are being reserved for the most severe issues as volume grows
- Organizations that depend on open source should expect many more CVEs per component and plan prioritization accordingly (see Chapter 33)

**Sources:**

- Daniel Stenberg, "High-Quality Chaos," April 22, 2026[^stenberg-hqchaos-2026]
- Daniel Stenberg, "Do minor curl vulnerabilities in the open," October 20, 2026[^stenberg-open-2026]
- Michael Catanzaro, "The Era of Software Quality, or the Era of Ostriches?," October 2, 2026[^catanzaro-gnome-2026]
- Cybersecurity Dive, "CVE Program eyes automation and globalization to weather AI 'vulnpocalypse'," August 11, 2026[^cybersecuritydive-cve-2026]
- Anthropic, "Anthropic's coordinated vulnerability disclosure dashboard," snapshot of November 2, 2026[^anthropic-cvd-dashboard-2026]

---

### Summary of Attack Vectors by Incident

| Incident | Year | Primary Vector | Sophistication |
|----------|------|----------------|----------------|
| Trusting Trust | 1984 | Compiler compromise | High |
| Debian OpenSSL | 2008 | Maintainer error | Low (unintentional) |
| Heartbleed | 2014 | Code vulnerability | Low (unintentional) |
| left-pad | 2016 | Package removal | Low |
| event-stream | 2018 | Social engineering / maintainer transfer | Medium |
| SolarWinds | 2020 | Build system compromise | Very High |
| Codecov | 2021 | CI/CD script compromise | Medium |
| ua-parser-js | 2021 | Account takeover | Medium |
| Log4Shell | 2021 | Code vulnerability | Low (unintentional) |
| colors.js/faker.js | 2022 | Maintainer sabotage | Low |
| node-ipc | 2022 | Maintainer sabotage | Low |
| 3CX | 2023 | Cascading supply chain | Very High |
| Ledger Connect | 2023 | Account takeover | Medium |
| BIPClip | 2024 | Malicious PyPI packages | Medium |
| XZ Utils | 2024 | Long-term social engineering | Very High |
| JAVS | 2024 | Distribution infrastructure compromise | High |
| Polyfill.io | 2024 | Domain acquisition | Medium |
| @lottiefiles/lottie-player | 2024 | Account takeover | Medium |
| aiocpa | 2024 | Legitimate-to-malicious flip | High |
| @solana/web3.js | 2024 | Account takeover | Medium |
| Ultralytics | 2024 | CI/CD workflow exploitation | High |
| rspack/vant | 2024 | Account takeover | Medium |
| IPany VPN | 2025 | Distribution infrastructure compromise | Medium |
| nullifAI (Hugging Face) | 2025 | Malicious ML models | Medium |
| tj-actions/changed-files | 2025 | CI/CD workflow exploitation | High |
| Erlang/OTP SSH | 2025 | Code vulnerability | Low (unintentional) |
| Notepad++ Update Hijacking | 2025 | Distribution infrastructure compromise / selective targeting | High |
| PhantomRaven | 2025 | Malicious npm packages | Medium |
| Nx s1ngularity | 2025 | CI/CD workflow exploitation + AI CLI weaponization | High |
| Shai-Hulud npm Worm (1.0 & 2.0) | 2025 | Self-replicating malware + phishing | Very High |
| Josh Junon (Qix) | 2025 | Account takeover | High |
| BoltDB | 2025 | Typosquatting | Medium |
| Red Hat GitLab | 2025 | Repository breach | High |
| Glass Worm | 2025 | Self-replicating malware | High |
| Solana Monkey-Patching | 2025 | Runtime library manipulation | High |
| React2Shell | 2025 | Code vulnerability | Low (unintentional) |
| AWS CodeBreach | 2026 | CI/CD webhook filter bypass | High |
| PyPI Spellchecker RAT | 2026 | Typosquatting / fileless RAT | Medium |
| Glass Worm (Wave 2) | 2026 | Publisher credential compromise | High |
| Metro4Shell (KEV) | 2026 | Developer tooling exploitation | Critical |
| dYdX | 2026 | Multi-registry publishing credential compromise | High |
| OpenClaw Ecosystem | 2026 | Multi-vector (malicious skills, RCE, impersonation) | High |
| Cline CLI (Clinejection) | 2026 | Prompt injection in AI CI workflow + unrevoked token | High |
| Claude Mythos / Project Glasswing | 2026 | AI-discovered vulnerabilities and exploits | Very High |
| Mini Shai-Hulud Resurgence | 2026 | Trusted publishing/OIDC abuse + self-replicating malware | Very High |
| Shai-Hulud AntV / Alibaba Wave | 2026 | Account compromise + self-replicating malware | Very High |
| Microsoft durabletask | 2026 | Stolen contributor account + agent-configuration hooks | High |
| Miasma / Leo Platform | 2026 | Malicious npm install hooks / CI secret theft | High |
| Atomic Arch | 2026 | Orphaned-package adoption / malicious dependencies | High |
| Cordyceps | 2026 | Privileged workflow abuse | High |
| PinpinRAT | 2026 | Social engineering / malicious interview repo | High |
| Trivy / Checkmarx Cascade | 2026 | Cascading security-vendor compromise | High |
| LiteLLM / Telnyx | 2026 | Stolen-token package injection | High |
| Megalodon | 2026 | Mass workflow poisoning via stolen credentials | High |
| ShapedPlugin | 2026 | Update mechanism compromise | High |
| Hugging Face Agent Intrusion | 2026 | Autonomous AI agent / malicious dataset | Very High |
| jscrambler | 2026 | Account takeover / import-time execution | High |
| AsyncAPI | 2026 | CI/CD workflow exploitation | High |
| LuaRocks.org | 2026 | Registry server RCE / credential database exposure | High |
| ChainDrop | 2026 | Account compromise + self-replicating malware | Very High |
| crates.io arrayref | 2026 | Social engineering / account compromise | High |
| Coder Module Registry | 2026 | CDN account compromise / registry impersonation | High |
| GhostAction Returns | 2026 | Mass workflow poisoning via stolen credentials | High |
| npm Trusted Publishing Flaw | 2026 | Mutable identity binding (design flaw) | Medium |
| eMudhra Intermediate CAs | 2026 | Trust anchor compromise | High |
| LiteLLM 1.107.0 | 2026 | Release pipeline hijack via stolen OAuth tokens | High |
| AI Vulnerability Report Surge | 2026 | Ecosystem capacity (not an attack) | N/A |

---

### Key Observations Across Incidents

**Evolution of Sophistication:** Early incidents were primarily accidental vulnerabilities or simple attacks. Modern supply chain attacks increasingly involve long-term planning, social engineering, and targeting of build infrastructure rather than just source code.

**Recurring Themes:**

1. **Single points of failure:** Individual maintainers, accounts, and dependencies create concentrated risk
2. **Trust exploitation:** Attackers invest in building trust before exploiting it
3. **Detection challenges:** Many compromises persist for weeks or months before discovery
4. **Cascading effects:** One compromise enables subsequent attacks downstream

**Defensive Gaps Highlighted:**

- Build system integrity verification
- Distribution infrastructure and update mechanism verification
- Expected-publisher certificate validation (not just signature existence)
- Maintainer succession and vetting processes
- Behavioral analysis of package changes
- SBOM adoption and dependency visibility
- Multi-factor authentication enforcement

These incidents collectively demonstrate that software supply chain security requires defense in depth across the entire lifecycle, from initial development through build, publication, distribution, and consumption.

[^thompson-1984c]: Thompson, K., "Reflections on Trusting Trust," Communications of the ACM, 1984, https://www.cs.cmu.edu/~rdriley/487/papers/Thompson_1984_ReflectionsonTrustingTrust.pdf

[^debian-dsa-1571]: Debian, "Debian Security Advisory DSA-1571-1," 2008, https://www.debian.org/security/2008/dsa-1571

[^cve-2008-0166]: NIST, "CVE-2008-0166," https://nvd.nist.gov/vuln/detail/CVE-2008-0166

[^schneier-debian-2008]: Schneier, B., "Random Number Bug in Debian Linux," 2008, https://www.schneier.com/blog/archives/2008/05/random_number_b.html

[^heartbleed-site]: Heartbleed.com, "Heartbleed Bug," 2014, https://heartbleed.com/

[^cve-2014-0160]: NIST, "CVE-2014-0160," https://nvd.nist.gov/vuln/detail/CVE-2014-0160

[^durumeric-heartbleed-2014]: Durumeric, Z., et al., "The Matter of Heartbleed," IMC 2014, https://jhalderm.com/pub/papers/heartbleed-imc14.pdf

[^koculu-leftpad-2016]: Koculu, A., "I've Just Liberated My Modules," 2016, https://kodfabrik.com/journal/i-ve-just-liberated-my-modules

[^register-leftpad-2016]: Williams, C., "How one developer just broke Node, Babel and thousands of projects," The Register, 2016, https://www.theregister.com/2016/03/23/npm_left_pad_chaos/

[^npm-leftpad-2016]: npm Blog, "kik, left-pad, and npm," 2016, https://blog.npmjs.org/post/141577284765/kik-left-pad-and-npm

[^eventstream-issue-116]: GitHub, "Issue #116: I don't know what to say," dominictarr/event-stream, 2018, https://github.com/dominictarr/event-stream/issues/116

[^snyk-eventstream-2018]: Snyk, "Malicious code found in npm package event-stream," 2018, https://snyk.io/blog/malicious-code-found-in-npm-package-event-stream/

[^npm-advisory-737]: npm, "Security Advisory 737," 2018, https://www.npmjs.com/advisories/737

[^cisa-aa20-352a]: CISA, "Advanced Persistent Threat Compromise of Government Agencies," Alert AA20-352A, 2020, https://www.cisa.gov/news-events/cybersecurity-advisories/aa20-352a

[^fireeye-solarwinds-2020]: FireEye/Mandiant, "Highly Evasive Attacker Leverages SolarWinds Supply Chain," 2020, https://www.mandiant.com/resources/blog/evasive-attacker-leverages-solarwinds-supply-chain-compromises-with-sunburst-backdoor

[^microsoft-solorigate-2020]: Microsoft, "Analyzing Solorigate," 2020, https://www.microsoft.com/en-us/security/blog/2020/12/18/analyzing-solorigate-the-compromised-dll-file-that-started-a-sophisticated-cyberattack-and-how-microsoft-defender-helps-protect/

[^codecov-security-2021]: Codecov, "Bash Uploader Security Update," 2021, https://about.codecov.io/security-update/

[^hashicorp-codecov-2021]: HashiCorp, "Codecov Security Event Impact," 2021, https://discuss.hashicorp.com/t/hcsec-2021-12-codecov-security-event-and-hashicorp-gpg-key-exposure/23512

[^reuters-codecov-2021]: Reuters, "Codecov hackers breached hundreds of networks," 2021, https://www.reuters.com/technology/codecov-hackers-breached-hundreds-restricted-customer-sites-sources-2021-04-19/

[^ghsa-ua-parser-js]: GitHub, "Advisory GHSA-pjwm-rvh2-c87w," 2021, https://github.com/advisories/GHSA-pjwm-rvh2-c87w

[^cisa-ua-parser-2021]: CISA, "MAR-10354752-1.v1," 2021, https://www.cisa.gov/news-events/alerts/2021/10/22/malware-discovered-popular-npm-package-ua-parser-js

[^bleeping-ua-parser-2021]: Bleeping Computer, "Popular npm library hijacked," 2021, https://www.bleepingcomputer.com/news/security/popular-npm-library-hijacked-to-install-password-stealers-miners/

[^apache-log4j-security]: Apache, "Log4j Security Vulnerabilities," https://logging.apache.org/log4j/2.x/security.html

[^cve-2021-44228]: NIST, "CVE-2021-44228," https://nvd.nist.gov/vuln/detail/CVE-2021-44228

[^cisa-log4j-2021]: CISA, "Apache Log4j Vulnerability Guidance," 2021, https://www.cisa.gov/news-events/news/apache-log4j-vulnerability-guidance

[^swiss-log4j-2021]: Swiss Government NCSC, "Log4j Analysis," 2021, https://www.ncsc.admin.ch/ncsc/en/home/aktuell/im-fokus/log4j.html

[^bleeping-colors-faker-2022]: Bleeping Computer, "Dev corrupts npm libs 'colors' and 'faker'," 2022, https://www.bleepingcomputer.com/news/security/dev-corrupts-npm-libs-colors-and-faker-breaking-thousands-of-apps/

[^colors-issue-317]: GitHub, "Issue #317," Marak/colors.js, 2022, https://github.com/Marak/colors.js/issues/317

[^verge-colors-2022]: The Verge, "Open source developer corrupts widely-used libraries," 2022, https://www.theverge.com/2022/1/9/22874949/developer-corrupts-open-source-libraries-colors-faker-protest

[^snyk-node-ipc-2022]: Snyk, "Protestware: Open Source Malware," 2022, https://snyk.io/blog/peacenotwar-malicious-npm-node-ipc-package-vulnerability/

[^cve-2022-23812]: NIST, "CVE-2022-23812," https://nvd.nist.gov/vuln/detail/CVE-2022-23812

[^snyk-node-ipc-analysis-2022]: Snyk, "node-ipc protestware analysis," 2022, https://snyk.io/blog/peacenotwar-malicious-npm-node-ipc-package-vulnerability/

[^pytorch-advisory-2022]: PyTorch, "Security Advisory," December 2022, https://pytorch.org/blog/compromised-nightly-dependency/

[^checkmarx-pytorch-2023]: Checkmarx, "PyTorch Dependency Confusion," 2023, https://medium.com/checkmarx-security/py-torch-a-leading-ml-framework-was-poisoned-with-malicious-dependency-e30f88242964

[^crowdstrike-3cx-2023]: CrowdStrike, "3CXDesktopApp Supply Chain Attack," 2023, https://www.crowdstrike.com/blog/crowdstrike-detects-and-prevents-active-intrusion-campaign-targeting-3cxdesktopapp-customers/

[^mandiant-3cx-2023]: Mandiant, "3CX Supply Chain Compromise," 2023, https://www.mandiant.com/resources/blog/3cx-software-supply-chain-compromise

[^3cx-advisory]: 3CX, "Security Advisory," 2023, https://www.3cx.com/blog/news/security-incident-updates/

[^ledger-incident-2023]: Ledger, "Security Incident Report," 2023, https://www.ledger.com/blog/a-letter-from-ledger-chairman-ceo-pascal-gauthier-regarding-ledger-connect-kit-exploit

[^blockaid-ledger-2023]: Blockaid, "Ledger Connect Kit Incident Analysis," 2023, https://www.blockaid.io/blog/attack-report-ledger-connect-kit

[^coindesk-ledger-2023]: CoinDesk, "Ledger Library Compromised," 2023, https://www.coindesk.com/tech/2023/12/14/ledgers-connect-kit-compromised-users-warned-not-to-interact-with-dapps/

[^reversinglabs-bipclip-2024]: Karlo Zanki, "BIPClip: Malicious PyPI packages target crypto wallet recovery passwords," ReversingLabs, March 12, 2024, https://www.reversinglabs.com/blog/bipclip-malicious-pypi-packages-target-crypto-wallet-recovery-passwords

[^freund-xz-2024]: Freund, A., "Backdoor in upstream xz/liblzma," oss-security mailing list, 2024, https://www.openwall.com/lists/oss-security/2024/03/29/4

[^cve-2024-3094]: NIST, "CVE-2024-3094," https://nvd.nist.gov/vuln/detail/CVE-2024-3094

[^cisa-xz-2024]: CISA, "XZ Utils Backdoor Alert," 2024, https://www.cisa.gov/news-events/alerts/2024/03/29/reported-supply-chain-compromise-affecting-xz-utils-data-compression-library-cve-2024-3094

[^arstechnica-xz-2024]: Arstechnica, "What we know about the xz Utils backdoor," 2024, https://arstechnica.com/security/2024/04/what-we-know-about-the-xz-utils-backdoor-that-almost-infected-the-world/

[^sansec-polyfill-2024]: Sansec, "Polyfill.io supply chain attack," 2024, https://sansec.io/research/polyfill-supply-chain-attack

[^cloudflare-polyfill-2024]: Cloudflare Blog, "Polyfill.io automatic replacement," 2024, https://blog.cloudflare.com/automatically-replacing-polyfill-io-links-with-cloudflares-mirror-for-a-safer-internet/

[^google-polyfill-2024]: Google, "Ads Policy Update," June 2024, https://support.google.com/adspolicy/answer/14632116

[^reversinglabs-sscs-2025]: ReversingLabs, "The 2025 Software Supply Chain Security Report," 2025, https://www.reversinglabs.com/sscs-report

[^rapid7-javs-2024]: Rapid7, "CVE-2024-4978: Backdoored Justice AV Solutions Viewer Software," May 2024, https://www.rapid7.com/blog/post/2024/05/30/cve-2024-4978-backdoored-justice-av-solutions-viewer-software-used-in-apparent-supply-chain-attack/

[^bleeping-javs-2024]: BleepingComputer, "JAVS courtroom recording software backdoored in supply chain attack," May 2024, https://www.bleepingcomputer.com/news/security/javs-courtroom-recording-software-backdoored-in-supply-chain-attack/

[^aikido-shai-hulud-2025]: Aikido Security, "Shai Hulud GitHub Actions Incident," November 25, 2025, https://www.aikido.dev/blog/github-actions-incident-shai-hulud-supply-chain-attack

[^unit42-github-actions-2025]: Unit 42, "GitHub Actions Supply Chain Attack," 2025, https://unit42.paloaltonetworks.com/github-actions-supply-chain-attack/

[^reversinglabs-sscs-2025-lottie]: ReversingLabs, "The 2025 Software Supply Chain Security Report," 2025, https://www.reversinglabs.com/sscs-report

[^reversinglabs-aiocpa-2024]: Karlo Zanki, "Malicious PyPI crypto pay package aiocpa implants infostealer code," ReversingLabs, November 28, 2024, https://www.reversinglabs.com/blog/malicious-pypi-crypto-pay-package-aiocpa-implants-infostealer-code

[^reversinglabs-solana-2024]: Paul Roberts, "Malware found in Solana npm library raises the bar for crypto security," ReversingLabs, December 5, 2024, https://www.reversinglabs.com/blog/malware-found-in-solana-npm-library-raises-the-bar-for-crypto-security

[^reversinglabs-ultralytics-2024]: Karlo Zanki, "Compromised ultralytics PyPI package delivers crypto coinminer," ReversingLabs, December 9, 2024, https://www.reversinglabs.com/blog/compromised-ultralytics-pypi-package-delivers-crypto-coinminer

[^reversinglabs-sscs-2025-rspack]: ReversingLabs, "The 2025 Software Supply Chain Security Report," 2025, https://www.reversinglabs.com/sscs-report

[^phylum-typosquatting-2024]: Phylum Research, "PyPI Typosquatting," 2024, https://blog.phylum.io/

[^pypi-security-2024]: PyPI, "Security Reports," 2024, https://pypi.org/security/

[^checkmarx-2024]: Checkmarx, "Supply Chain Security Research," 2024, https://checkmarx.com/blog/

[^reversinglabs-huggingface-2025]: Karlo Zanki, "Malicious ML models discovered on Hugging Face platform," ReversingLabs, February 6, 2025, https://www.reversinglabs.com/blog/malicious-ml-models-discovered-on-hugging-face-platform

[^bleeping-ipany-2025]: Bill Toulas, "IPany VPN breached in supply-chain attack to push custom malware," BleepingComputer, January 22, 2025, https://www.bleepingcomputer.com/news/security/ipany-vpn-breached-in-supply-chain-attack-to-push-custom-malware/

[^silobreaker-2025]: Silobreaker, "Supply Chain Attacks in 2025: A Month-by-Month Summary," 2025, https://www.silobreaker.com/blog/cyber-threats/supply-chain-attacks-in-2025-a-month-by-month-summary/

[^unit42-erlang-2025]: Unit 42, "Vulnerability Analysis CVE-2025-32433," 2025, https://unit42.paloaltonetworks.com/vulnerability-analysis-cve-2025-32433/

[^kaspersky-notepadpp-2026]: Kaspersky Securelist, "A Supply Chain Attack on Notepad++," February 2, 2026, https://securelist.com/notepad-supply-chain-attack/118708/

[^notepadpp-incident-2026]: Notepad++, "Hijacked Incident Info Update," February 2, 2026, https://notepad-plus-plus.org/news/hijacked-incident-info-update/

[^notepadpp-v889-2025]: Notepad++, "v8.8.9 Released," December 9, 2025, https://notepad-plus-plus.org/news/v889-released/

[^tuf]: The Update Framework (TUF), https://theupdateframework.io/

[^socket-phantomraven-2025]: Socket.dev, "npm Malware Campaign PhantomRaven," 2025, https://socket.dev/blog/npm-malware-campaign-phantomraven

[^truesec-shai-hulud-2025]: Truesec, "500 npm Packages Compromised in Ongoing Supply Chain Attack Shai-Hulud," 2025, https://www.truesec.com/hub/blog/500-npm-packages-compromised-in-ongoing-supply-chain-attack-shai-hulud

[^cisa-shai-hulud-2025]: CISA, "Widespread Supply Chain Compromise Impacting npm Ecosystem," September 23, 2025, https://www.cisa.gov/news-events/alerts/2025/09/23/widespread-supply-chain-compromise-impacting-npm-ecosystem

[^microsoft-shai-hulud-2025]: Microsoft Security Blog, "Shai-Hulud 2.0 Guidance," December 2025, https://www.microsoft.com/en-us/security/blog/2025/12/09/shai-hulud-2-0-guidance-for-detecting-investigating-and-defending-against-the-supply-chain-attack/

[^hackernews-qix-2025]: The Hacker News, "20 Popular npm Packages with 2 Billion Weekly Downloads Compromised," September 2025, https://thehackernews.com/2025/09/20-popular-npm-packages-with-2-billion.html

[^cisa-boltdb-2025]: CISA, "npm and Go Ecosystem Risks Alert," September 2025, https://www.cisa.gov/news-events/alerts/2025/09/23/widespread-supply-chain-compromise-impacting-npm-ecosystem

[^guardz-redhat-2025]: Guardz Security Blog, "Top Recent Data Breaches," 2025, https://guardz.com/blog/top-recent-data-breaches/

[^veracode-glassworm-2025]: Veracode, "The First Self-Propagating VS Code Extension Worm: GlassWorm," October 20, 2025, https://www.veracode.com/blog/glassworm-vs-code-extension/

[^securecodewarrior-glassworm-2025]: Secure Code Warrior, "OWASP Top 10 2025: Software Supply Chain Failures," 2025, https://www.securecodewarrior.com/article/owasp-top-10-2025-software-supply-chain-failures

[^reversinglabs-sscs-2025-solana]: ReversingLabs, "The 2025 Software Supply Chain Security Report," 2025, https://www.reversinglabs.com/sscs-report

[^nvd-cve-2025-15556]: NIST, "CVE-2025-15556," NVD, https://nvd.nist.gov/vuln/detail/CVE-2025-15556

[^kev-cve-2025-15556]: CISA, Known Exploited Vulnerabilities Catalog, entry for CVE-2025-15556, added February 12, 2026, https://www.cisa.gov/known-exploited-vulnerabilities-catalog

[^aws-codebreach-2026]: AWS, "Security Bulletin 2026-002: Unanchored ACCOUNT_ID webhook filters for CodeBuild," https://aws.amazon.com/security/security-bulletins/2026-002-AWS/, January 15, 2026.

[^wiz-codebreach-2026]: Wiz Research, "CodeBreach: Infiltrating the AWS Console Supply Chain and Hijacking AWS GitHub Repositories via CodeBuild," https://www.wiz.io/blog/wiz-research-codebreach-vulnerability-aws-codebuild, January 15, 2026.

[^aikido-spellchecker-2026]: Aikido Security, "Malicious PyPI 'spellchecker' packages deliver fileless Python RAT," https://www.aikido.dev/blog/malicious-pypi-packages-spellcheckpy-and-spellcheckerpy-deliver-python-rat, January 23, 2026.

[^socket-glassworm-2026]: Socket Security, "GlassWorm Loader Hits Open VSX via Developer Account Compromise," January 31, 2026, https://socket.dev/blog/glassworm-loader-hits-open-vsx-via-suspected-developer-account-compromise

[^nvd-metro4shell-2026]: NIST, "CVE-2025-11953," NVD, https://nvd.nist.gov/vuln/detail/CVE-2025-11953

[^kev-metro4shell-2026]: CISA, Known Exploited Vulnerabilities Catalog, entry for CVE-2025-11953, added February 5, 2026.

[^vulncheck-metro4shell-2026]: VulnCheck, "Metro4Shell: Exploitation of React Native’s Metro Server in the Wild," https://www.vulncheck.com/blog/metro4shell_eitw, February 3, 2026.

[^socket-dydx-2026]: Socket Security research and public reporting on dYdX package compromise, https://socket.dev/blog/malicious-dydx-packages-published-to-npm-and-pypi, February 6, 2026.

[^react-cve-2025-55182]: React.dev, "Critical Security Vulnerability in React Server Components," December 3, 2025, https://react.dev/blog/2025/12/03/critical-security-vulnerability-in-react-server-components

[^nvd-cve-2025-55182]: NIST, "CVE-2025-55182," https://nvd.nist.gov/vuln/detail/CVE-2025-55182

[^koi-clawhavoc-2026]: Koi Security, "ClawHavoc: 341 Malicious Clawed Skills Found by the Bot They Were Targeting," February 2, 2026, https://www.koi.ai/blog/clawhavoc-341-malicious-clawedbot-skills-found-by-the-bot-they-were-targeting

[^bleeping-moltbot-2026]: BleepingComputer, "Malicious MoltBot skills used to push password-stealing malware," 2026, https://www.bleepingcomputer.com/news/security/malicious-moltbot-skills-used-to-push-password-stealing-malware/

[^hackernews-openclaw-2026]: The Hacker News, "OpenClaw Bug Enables One-Click Remote Code Execution via Malicious Link," February 2026, https://thehackernews.com/2026/02/openclaw-bug-enables-one-click-remote.html

[^aikido-clawdbot-2026]: Aikido Security, "Fake Clawdbot VS Code Extension Installs ScreenConnect RAT," January 2026, https://www.aikido.dev/blog/fake-clawdbot-vscode-extension-malware

[^reuters-moltbook-2026]: Reuters, "'Moltbook' social media site for AI agents had big security hole, cyber firm Wiz says," February 2, 2026, https://www.reuters.com/legal/litigation/moltbook-social-media-site-ai-agents-had-big-security-hole-cyber-firm-wiz-says-2026-02-02/

[^anthropic-glasswing-2026]: Anthropic, "Identifying vulnerabilities and exploits with Claude Mythos Preview," 2026, https://www.anthropic.com/glasswing

[^aisle-mythos-jagged-frontier-2026]: AISLE, "AI Cybersecurity After Mythos: The Jagged Frontier," 2026, https://aisle.com/blog/ai-cybersecurity-after-mythos-the-jagged-frontier

[^hackernews-mini-shai-hulud-2026]: The Hacker News, "Mini Shai-Hulud Worm Compromises npm and PyPI Packages," 2026, https://thehackernews.com/2026/05/mini-shai-hulud-worm-compromises.html

[^stepsecurity-antv-shai-hulud-2026]: StepSecurity, "Shai-Hulud: Here We Go Again," 2026, https://www.stepsecurity.io/blog/shai-hulud-here-we-go-again-mass-npm-supply-chain-attack-hits-the-antv-ecosystem

[^stepsecurity-leo-miasma-2026]: StepSecurity, "Mass npm Supply Chain Attack: 20 Leo Platform Packages Compromised," June 2026, https://www.stepsecurity.io/blog/mass-npm-supply-chain-attack-20-leo-platform-packages-compromised

[^cybersecuritynews-aur-2026]: Cybersecurity News, "Arch Linux AUR Packages Compromised," 2026, https://cybersecuritynews.com/arch-linux-aur-packages-compromised/

[^sonatype-atomic-arch-2026]: Sonatype, "Atomic Arch npm Campaign Adds Malicious Dependency," 2026, https://www.sonatype.com/blog/atomic-arch-npm-campaign-adds-malicious-dependency

[^hackernews-cordyceps-2026]: The Hacker News, "Cordyceps CI/CD Flaws Expose 300+ GitHub Repositories," 2026, https://thehackernews.com/2026/06/cordyceps-cicd-flaws-expose-300-github.html

[^grack-pinpinrat-2026]: Matt Mastracci, "Dissecting a Failed Nation-State Attack," June 25, 2026, https://grack.com/blog/2026/06/25/dissecting-a-failed-nation-state-attack/

[^checkmarx-incident-2026]: Checkmarx, "Update: Ongoing Checkmarx Supply Chain Security Incident," 2026, https://checkmarx.com/blog/ongoing-security-updates/

[^pypi-litellm-incident-2026]: Python Software Foundation, "Incident Report: LiteLLM/Telnyx Supply-Chain Attacks," PyPI Blog, April 2, 2026, https://blog.pypi.org/posts/2026-04-02-incident-report-litellm-telnyx-supply-chain-attack/

[^cmalliance-2026-attacks]: Cyber Management Alliance, "5 of the Biggest Supply Chain Attacks of 2026 So Far," 2026, https://www.cm-alliance.com/cybersecurity-blog/5-of-the-biggest-supply-chain-attacks-of-2026-so-far

[^stepsecurity-megalodon-2026]: StepSecurity, "Megalodon: Mass GitHub Actions Secret Exfiltration Across 5,500+ Public Repositories," May 2026, https://www.stepsecurity.io/blog/megalodon-mass-github-actions-secret-exfiltration-across-5-500-public-repositories

[^hackernews-megalodon-2026]: The Hacker News, "Megalodon GitHub Attack Targets 5,561 Repos with Malicious CI/CD Workflows," May 2026, https://thehackernews.com/2026/05/megalodon-github-attack-targets-5561.html

[^hackernews-shapedplugin-2026]: The Hacker News, "ShapedPlugin WordPress Pro Plugins Backdoored in Supply Chain Attack," June 2026, https://thehackernews.com/2026/06/shapedplugin-wordpress-pro-plugins.html

[^bleeping-shapedplugin-2026]: BleepingComputer, "ShapedPlugin update flow hacked to infect WordPress sites," June 2026, https://www.bleepingcomputer.com/news/security/shapedplugin-update-flow-hacked-to-infect-wordpress-sites/

[^huggingface-incident-2026]: Hugging Face, "Security incident disclosure — July 2026," July 2026, https://huggingface.co/blog/security-incident-july-2026

[^hackernews-huggingface-2026]: The Hacker News, "World's Largest AI Model Repository Hugging Face Breached by Autonomous AI Agent," July 2026, https://thehackernews.com/2026/07/worlds-largest-ai-model-repository.html

[^gitguardian-streak-2026]: GitGuardian, "The Streak Continues: Four More Supply Chain Attacks Hit npm and PyPI," July 2026, https://blog.gitguardian.com/shai-hulud-npm-pypi-supply-chain-attacks/

[^luarocks-incident-2026]: LuaRocks, "Security Incident September 2026," September 2026, https://luarocks.org/security-incident-september-2026

[^microsoft-chaindrop-2026]: Microsoft Security, "ChainDrop supply chain compromise: Anatomy of a self-propagating worm," August 4, 2026, https://www.microsoft.com/en-us/security/blog/2026/08/04/chaindrop-supply-chain-compromise-anatomy-self-propagating-worm/

[^wiz-keyv-2026]: Wiz, "keyv and cacheable npm Package Hijacked in Supply Chain Attack," August 4, 2026, https://www.wiz.io/blog/keyv-and-cacheable-npm-supply-chain-attack

[^rust-arrayref-2026]: Rust Security Response Team, "Supply chain attack on arrayref," Rust Blog, August 20, 2026, https://blog.rust-lang.org/2026/08/20/supply-chain-attack-on-arrayref/

[^rust-targeted-2026]: Adam Harvey, "Be alert: targeted attacks on prominent Rustaceans," Rust Blog, September 17, 2026, https://blog.rust-lang.org/2026/09/17/targeted-attacks/

[^coder-registry-2026]: Coder, "Malicious Packages Served from Unauthorized Registry Server," GHSA-vx42-ghc9-gw65, September 1, 2026, https://github.com/coder/coder/security/advisories/GHSA-vx42-ghc9-gw65

[^gitguardian-ghostaction-2026]: GitGuardian, "GhostAction Returns: 772 Repos Hit, 30k More in Oct," October 7, 2026, https://blog.gitguardian.com/ghostaction-github-actions-supply-chain-attack-returns/

[^hackernews-ghostaction-2026]: The Hacker News, "Credential-Stealing GitHub Actions Workflows Planted in Tens of Thousands of Repositories," October 2026, https://thehackernews.com/2026/10/credential-stealing-github-actions.html

[^helpnet-slugfest-2026]: Help Net Security, "npm Trusted Publishing flaw exposed 1,500+ npm packages to takeover," October 13, 2026, https://www.helpnetsecurity.com/2026/10/13/slugfest-npm-trusted-publishing-flaw/

[^github-tp-expire-2026]: GitHub, "Unvalidated npm trusted publishing configurations now expire," GitHub Changelog, October 2, 2026, https://github.blog/changelog/2026-10-02-unvalidated-npm-trusted-publishing-configurations-now-expire

[^go-emudhra-2026]: The Go Team, "Our Response To The eMudhra Key Disclosure Incident," The Go Blog, October 2026, https://go.dev/blog/emudhra-incident

[^nesbitt-truststores-2026]: Andrew Nesbitt, "Trust Stores Are Dependencies Too," October 21, 2026, https://nesbitt.io/2026/10/21/trust-stores-are-dependencies-too.html

[^socket-litellm-2026]: Socket, analysis of the LiteLLM 1.107.0 compromise, October 21, 2026, https://socket.dev/blog/litellm-compromise

[^helpnet-litellm-2026]: Help Net Security, "LiteLLM hit by second supply chain attack this year, users urged to rotate credentials," October 22, 2026, https://www.helpnetsecurity.com/2026/10/22/litellm-compromised-supply-chain-attack-pypi-docker/

[^stenberg-hqchaos-2026]: Daniel Stenberg, "High-Quality Chaos," daniel.haxx.se, April 22, 2026, https://daniel.haxx.se/blog/2026/04/22/high-quality-chaos/

[^stenberg-open-2026]: Daniel Stenberg, "Do minor curl vulnerabilities in the open," daniel.haxx.se, October 20, 2026, https://daniel.haxx.se/blog/2026/10/20/do-minor-curl-vulnerabilities-in-the-open/

[^catanzaro-gnome-2026]: Michael Catanzaro, "The Era of Software Quality, or the Era of Ostriches?," October 2, 2026, https://blogs.gnome.org/mcatanzaro/2026/10/02/the-era-of-software-quality-or-the-era-of-ostriches/

[^cybersecuritydive-cve-2026]: Eric Geller, "CVE Program eyes automation and globalization to weather AI 'vulnpocalypse'," Cybersecurity Dive, August 11, 2026, https://www.cybersecuritydive.com/news/cve-program-ai-black-hat-def-con/827477/

[^anthropic-cvd-dashboard-2026]: Anthropic, "Anthropic's coordinated vulnerability disclosure dashboard," snapshot of November 2, 2026, https://red.anthropic.com/2026/cvd/

[^hackernews-openai-hf-2026]: The Hacker News, "OpenAI Agent Used Exposed Credentials Across Four Services During Hugging Face Breach," July 29, 2026 (updated August 1, 2026), https://thehackernews.com/2026/07/openai-agent-used-exposed-credentials.html

[^securityweek-trustwallet-2025]: SecurityWeek, "Shai-Hulud Supply Chain Attack Led to $8.5 Million Trust Wallet Heist," December 31, 2025, https://www.securityweek.com/shai-hulud-supply-chain-attack-led-to-8-5-million-trust-wallet-heist/

[^darkreading-trellix-2026]: Dark Reading, "Trellix Source Code Breach Highlights Growing Supply Chain Threats," May 2026, https://www.darkreading.com/cyberattacks-data-breaches/trellix-source-code-breach-supply-chain-threats

[^nx-postmortem-2025]: Juri Strumpflohner, "S1ngularity: What Happened, How We Responded, What We Learned," Nx Blog, September 5, 2025, https://nx.dev/blog/s1ngularity-postmortem

[^snyk-nx-agents-2025]: Liran Tal, "Weaponizing AI Coding Agents for Malware in the Nx Malicious Package Security Incident," Snyk, August 27, 2025, https://snyk.io/blog/weaponizing-ai-coding-agents-for-malware-in-the-nx-malicious-package

[^bleeping-s1ngularity-2025]: Bill Toulas, "AI-powered malware hit 2,180 GitHub accounts in 's1ngularity' attack," BleepingComputer, September 6, 2025, https://www.bleepingcomputer.com/news/security/ai-powered-malware-hit-2-180-github-accounts-in-s1ngularity-attack/

[^cline-postmortem-2026]: Cline, "Post-mortem: Unauthorized Cline CLI npm publish on February 17, 2026," February 24, 2026, https://cline.bot/blog/post-mortem-unauthorized-cline-cli-npm

[^snyk-clinejection-2026]: Stephen Thoemmes, "How 'Clinejection' Turned an AI Bot into a Supply Chain Attack," Snyk, February 19, 2026, https://snyk.io/blog/cline-supply-chain-attack-prompt-injection-github-actions/

[^stepsecurity-durabletask-2026]: Ashish Kurmi, "Microsoft's durabletask PyPI Package Compromised in Supply Chain Attack," StepSecurity, May 19, 2026 (updated June 5, 2026), https://www.stepsecurity.io/blog/microsofts-durabletask-pypi-package-compromised-in-supply-chain-attack

[^venturebeat-durabletask-2026]: Louis Columbus, "GitHub confirms 3,800 internal repos stolen through poisoned VS Code extension as supply chain worm hits Microsoft's Python SDK," VentureBeat, May 20, 2026, https://venturebeat.com/security/github-confirms-3800-repos-stolen-poisoned-vs-code-extension-supply-chain-worm-microsoft-python-sdk

[^unit42-openclaw-2026]: Shresta Bellary Seetharam, Nabeel Mohamed, Billy Melicher, and Oleksii Starov, "OpenClaw's Skill Marketplace and the Emerging AI Supply Chain Threat," Palo Alto Networks Unit 42, June 23, 2026, https://unit42.paloaltonetworks.com/openclaw-ai-supply-chain-risk/

[^thn-claw-chain-2026]: Ravie Lakshmanan, "Four OpenClaw Flaws Enable Data Theft, Privilege Escalation, and Persistence," The Hacker News, May 15, 2026, https://thehackernews.com/2026/05/four-openclaw-flaws-enable-data-theft.html
