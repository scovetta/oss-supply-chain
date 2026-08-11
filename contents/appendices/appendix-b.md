## Appendix B: Resource Guide

This appendix provides curated resources for readers seeking deeper knowledge in open source security and software supply chain security. Resources are organized by category and annotated to help you identify the most relevant materials for your needs.

---

### Essential Reading

#### Books

**Building Secure and Reliable Systems** by Heather Adkins et al. (O'Reilly, 2020)[^building-secure-reliable]
Written by Google security and SRE professionals, this book bridges the gap between security and reliability engineering. Freely available online, it offers practical guidance on integrating security throughout the software lifecycle.

**Software Transparency: Supply Chain Security in an Era of a Software-Driven Society** by Chris Hughes and Tony Turner (Wiley, 2022)[^software-transparency]
A comprehensive treatment of software supply chain security with particular emphasis on SBOMs, policy frameworks, and organizational implementation strategies.

**Threat Modeling: Designing for Security** by Adam Shostack (Wiley, 2014)[^threat-modeling]
The definitive guide to threat modeling methodology. Essential reading for anyone designing secure systems or evaluating the security posture of software projects.

**The Art of Software Security Assessment** by Mark Dowd, John McDonald, and Justin Schuh (Addison-Wesley, 2006)[^art-security-assessment]
Though focused on vulnerability discovery, this comprehensive text provides deep understanding of how software vulnerabilities arise—essential context for supply chain security practitioners.

**Hacking Kubernetes** by Andrew Martin and Michael Hausenblas (O'Reilly, 2021)[^hacking-kubernetes]
Covers security considerations for containerized environments and Kubernetes, including supply chain concerns specific to cloud-native infrastructure.

**Alice and Bob Learn Application Security** by Tanya Janca (Wiley, 2020)[^alice-bob-security]
An accessible introduction to application security that covers secure development practices, making it suitable for developers new to security concepts.

**Practical Binary Analysis** by Dennis Andriesse (No Starch Press, 2018)[^practical-binary]
For readers interested in understanding binary-level security analysis, this book covers disassembly, instrumentation, and analysis techniques relevant to verifying software artifacts.

#### Foundational Papers

**"Backstabber's Knife Collection: A Review of Open Source Software Supply Chain Attacks"** by Marc Ohm et al. (2020)[^backstabbers-knife]
A systematic taxonomy of software supply chain attacks against open source ecosystems. Essential reading for understanding the threat landscape.

**"in-toto: Providing farm-to-table guarantees for bits and bytes"** by Santiago Torres-Arias et al. (USENIX Security 2019)[^in-toto-paper]
The foundational paper describing the in-toto framework for supply chain integrity, explaining its cryptographic attestation model.

**"Dependency Confusion: How I Hacked Into Apple, Microsoft and Dozens of Other Companies"** by Alex Birsan (2021)[^dependency-confusion]
The original disclosure of the dependency confusion attack vector. Required reading for understanding this critical vulnerability class.

**"An Empirical Study of Malicious Code in PyPI Ecosystem"** by Ruian Duan et al. (ASE 2020)[^malicious-pypi]
Research analyzing malicious packages in the Python ecosystem, providing data-driven insights into attack patterns and detection approaches.

**"A Look at the Security of npm"** by Markus Zimmermann et al. (2019)[^npm-security]
Comprehensive security analysis of the npm ecosystem examining maintainer practices, vulnerability propagation, and security risks.

**"Reproducible Builds: Increasing the Integrity of Software Supply Chains"** by Chris Lamb and Stefano Zacchiroli (IEEE Software 2022)[^reproducible-builds-paper]
Academic treatment of reproducible builds, explaining why they matter and the technical challenges involved in achieving them.

**"World of Code: An Infrastructure for Mining the Universe of Open Source VCS Data"** by Yuxing Ma et al. (MSR 2019)[^world-of-code]
Describes infrastructure for large-scale analysis of open source code, relevant for understanding ecosystem-wide security research methodologies.

**"Research Directions in Software Supply Chain Security"** by Hasan et al. (ACM TOSEM 2025)[^acm-tosem-ssc]
Peer-reviewed survey identifying research gaps and future directions in supply chain security, providing scholarly foundation for the discipline.

**"SoK: Analysis of Software Supply Chain Security by Establishing Secure Design Properties"** by Ladisa et al. (arXiv 2024)[^arxiv-sok-ssc]
Systematization of Knowledge paper proposing a framework of four attack stages and three security properties (transparency, validity, separation) for evaluating supply chain defenses.

#### Key Industry Reports

**Sonatype State of the Software Supply Chain Report** (Annual)[^sonatype-report]
Comprehensive annual report tracking supply chain attacks, open source consumption trends, and security metrics across major ecosystems.

**Snyk State of Open Source Security Report** (Annual)[^snyk-report]
Data-driven analysis of vulnerability trends, fixing times, and security practices across open source projects.

**OpenSSF Scorecard Report**[^openssf-blog]
Periodic reports analyzing security practices across open source projects using the Scorecard framework.

**CISA Secure Software Development Framework (SSDF)**[^cisa-ssdf]
NIST Special Publication 800-218 providing a core set of secure development practices that form the basis for many organizational policies.

**CISA Software Bill of Materials (SBOM) Resources**[^cisa-sbom]
Official U.S. government guidance on SBOM implementation, including minimum element requirements and sharing practices.

**Linux Foundation Census Reports**[^lf-research]
Research identifying the most critical open source packages, informing where security investments should be prioritized.

**CNCF Software Supply Chain Best Practices White Paper v2** (November 2024)[^cncf-sscp-v2]
Updated guidance from CNCF TAG Security on supply chain security best practices, including persona-based recommendations for developers, operators, and security teams. Referenced by NIST SSDF.

**CoSAI AI Supply Chain and Agentic Security Publications** (2025-2026)[^cosai-resources]
Coalition for Secure AI workstream publications covering AI supply chain risks and controls, signing ML artifacts, defending and responding to AI incidents, shared responsibility, Model Context Protocol security, agentic identity and access management, and secure design patterns for agentic systems.[^cosai-risks-controls][^cosai-signing-ml][^cosai-defenders][^cosai-ir][^cosai-shared-responsibility][^cosai-mcp][^cosai-agentic-iam][^cosai-agentic-future]

**Synopsys Open Source Security and Risk Analysis (OSSRA) Report** (Annual)[^synopsys-ossra]
Analysis based on audits of commercial codebases, revealing open source usage patterns and risk exposure.

---

### Key Organizations

#### Standards and Coordination Bodies

**Open Source Security Foundation (OpenSSF)**[^openssf]
The primary cross-industry initiative for improving open source security. Hosts working groups on vulnerability disclosure, supply chain integrity, security tooling, and education. Essential for anyone working in this space.

**Coalition for Secure AI (CoSAI)**[^cosai]
OASIS Open Project launched in 2024 to coordinate industry guidance for secure AI systems. Its workstreams cover AI software supply chain security, defender readiness, AI security risk governance, and secure design patterns for agentic systems.

**Cybersecurity and Infrastructure Security Agency (CISA)**[^cisa]
U.S. federal agency providing guidance, alerts, and coordination for software security. Key source for government policy and requirements.

**MITRE Corporation**[^mitre]
Operates CVE, CWE, ATT&CK, and other foundational security resources. Understanding MITRE's frameworks is essential for security practitioners.

**Forum of Incident Response and Security Teams (FIRST)**[^first]
Global forum for incident response teams that maintains CVSS and promotes coordinated vulnerability disclosure practices.

**Internet Engineering Task Force (IETF)**[^ietf]
Develops internet standards including security protocols relevant to software distribution and verification.

#### Open Source Foundations

**Linux Foundation**[^linux-foundation]
Hosts numerous critical projects including the Linux kernel, Kubernetes, and many supply chain security initiatives including Sigstore and SPDX.

**Apache Software Foundation**[^apache]
Stewards over 350 open source projects with established governance and security response processes. Their security model is worth studying.

**Cloud Native Computing Foundation (CNCF)**[^cncf]
Hosts cloud-native projects including Kubernetes, in-toto, and Notary. Maintains security guidelines for cloud-native supply chains.

**Open Web Application Security Project (OWASP)**[^owasp]
Produces security guidance, tools, and educational resources. Key projects include Dependency-Check, CycloneDX, and the Software Component Verification Standard.

**Python Software Foundation**[^psf]
Governs Python and PyPI, implementing security features like trusted publishing that serve as models for other ecosystems.

**Rust Foundation**[^rust-foundation]
Supports the Rust ecosystem, notable for its memory safety focus and crates.io security practices.

---

### Tooling Reference

#### Software Composition Analysis (SCA)

**OWASP Dependency-Check**[^dependency-check]
Open source tool that identifies project dependencies and checks for known vulnerabilities. Supports multiple languages and integrates with CI/CD systems.

**Grype**[^grype]
Fast, open source vulnerability scanner for container images and filesystems. Pairs well with Syft for SBOM generation.

**Snyk**[^snyk]
Commercial platform (with free tier) for vulnerability scanning, license compliance, and dependency management across multiple ecosystems.

**Dependabot**[^dependabot]
GitHub-integrated tool that automatically creates pull requests to update vulnerable dependencies. Now part of GitHub's native security features.

**Trivy**[^trivy]
Comprehensive scanner for vulnerabilities, misconfigurations, secrets, and SBOM generation in containers, filesystems, and repositories.

**Xygeni**[^xygeni]
Identify questionable dependencies and malicious code that may compromise software projects, with Remediation Risk analysis for safer, smarter fixes.

#### SBOM Generation and Management

**Syft**[^syft]
Powerful CLI tool for generating SBOMs from container images and filesystems. Supports SPDX, CycloneDX, and custom formats.

**CycloneDX Tools**[^cyclonedx-tools]
Collection of tools for generating, validating, and managing CycloneDX SBOMs across various programming languages.

**SPDX Tools**[^spdx-tools]
Official tools for working with SPDX format SBOMs, including validators, converters, and generators.

**SBOM Scorecard**[^sbom-scorecard]
Tool for evaluating the quality and completeness of SBOMs against best practices.

#### Signing and Verification

**Sigstore**[^sigstore]
Free, open infrastructure for signing and verifying software artifacts. Includes Cosign, Fulcio, and Rekor components.

**Cosign**[^cosign]
Tool for signing and verifying container images and other artifacts. Supports keyless signing via Sigstore.

**The Update Framework (TUF)**[^tuf]
Framework for securing software update systems against various attack types. Used by PyPI, RubyGems, and others.

**Notary**[^notary]
CNCF project implementing TUF for container image signing and verification.

#### Supply Chain Security Frameworks

**SLSA Tools**[^slsa-tools]
Generators and verifiers for SLSA provenance, with GitHub Actions integration.

**OpenSSF Scorecard**[^scorecard]
Automated tool that assesses open source project security practices against a defined set of checks.

**in-toto**[^in-toto]
Framework for generating and verifying supply chain metadata through cryptographic attestations.

**OSS Gadget**[^oss-gadget]
Microsoft's collection of tools for analyzing open source packages, including health metrics and security checks.

#### Static Analysis

**CodeQL**[^codeql]
Semantic code analysis engine from GitHub. Query language enables sophisticated vulnerability detection. Free for open source.

**Semgrep**[^semgrep]
Fast, open source static analysis tool with an extensive rule library. Supports custom rule creation.

**Capslock**[^capslock-appendix]
Capability analysis for Go, Rust, and Java. Maps what dependencies can access (files, network, exec) to enforce least privilege and detect supply chain anomalies.

**SonarQube**[^sonarqube]
Platform for continuous code quality and security inspection. Community edition is free and open source.

**Bandit**[^bandit]
Python-focused security linter that finds common security issues in Python code.

**Xygeni**[^xygeni-sast]
Advanced SAST powered by malware detection and AI AutoFix for private remediation, enabling developers to detect vulnerabilities and deliver secure code.

#### Fuzzing

**OSS-Fuzz**[^oss-fuzz]
Google's continuous fuzzing service for critical open source projects. Provides infrastructure and integration support.

**AFL++**[^aflplus]
Community-maintained fork of American Fuzzy Lop with improved performance and features.

**ClusterFuzz**[^clusterfuzz]
Scalable fuzzing infrastructure that powers OSS-Fuzz. Available for self-hosting.

#### Secret Detection

**Gitleaks**[^gitleaks]
Fast, open source tool for detecting secrets in git repositories.

**TruffleHog**[^trufflehog]
Scans repositories for high-entropy strings and known credential patterns.

**detect-secrets**[^detect-secrets]
Yelp's audited tool for preventing secrets from entering codebases.

**Xygeni**[^xygeni-secrets]
Identify secrets throughout the entire SDLC and prevent new leaks during coding, building, and delivery actions with automated prevention rules.

---

### Conferences and Community Events

#### Major Security Conferences

**Black Hat**[^blackhat]
Premier security conference featuring cutting-edge research presentations. Supply chain security tracks have grown significantly in recent years.

**DEF CON**[^defcon]
Largest hacker convention with villages dedicated to specific security domains. Excellent for hands-on learning and community engagement.

**RSA Conference**[^rsa]
Major enterprise security conference with significant vendor presence and policy discussions.

**USENIX Security Symposium**[^usenix]
Academic security conference publishing peer-reviewed research, including foundational supply chain security papers.

#### Open Source and DevSecOps Events

**Open Source Summit**[^oss-summit]
Linux Foundation's flagship event combining multiple conferences including Open Source Security Summit.

**KubeCon + CloudNativeCon**[^kubecon]
Premier cloud-native conference with extensive supply chain security content. Co-located events include SupplyChainSecurityCon.

**SupplyChainSecurityCon**[^scscon]
Dedicated conference focusing specifically on software supply chain security topics.

**OWASP Global AppSec**[^owasp-appsec]
Application security conference with strong focus on practical security implementation.

**PackagingCon**[^packagingcon]
Conference dedicated to software package management, relevant for understanding ecosystem security.

#### Community Meetups and Working Groups

**OpenSSF Working Groups**[^openssf-community]
Regular meetings of OpenSSF working groups are open to public participation. Excellent way to contribute to industry initiatives.

**CNCF Security TAG**[^cncf-tag-security]
Technical Advisory Group on security for cloud-native projects. Publishes guidance and reviews project security.

**Package Manager Security Summits**
Informal gatherings of package manager maintainers to discuss shared security challenges. Watch OpenSSF announcements for scheduling.

---

### Training and Certification Programs

#### Free Online Courses

**OpenSSF Secure Software Development Fundamentals**[^openssf-training]
Free, self-paced course covering secure development practices. Provides certificate upon completion.

**OpenSSF Developing Secure Software (LFD121)**[^lfd121]
Comprehensive course on secure software development fundamentals offered through Linux Foundation.

**OWASP Web Security Testing Guide**[^owasp-wstg]
While not a formal course, this comprehensive guide serves as an excellent self-study resource.

**Google's Secure Coding Practices**[^google-security]
Collection of security guides and best practices from Google covering various platforms and languages.

#### Professional Certifications

**Certified Secure Software Lifecycle Professional (CSSLP)**[^csslp]
ISC2 certification focused on incorporating security throughout the software lifecycle.

**GIAC Secure Software Programmer (GSSP)**[^gssp]
SANS certification demonstrating secure coding competency in specific languages.

**Certified Kubernetes Security Specialist (CKS)**[^cks]
Linux Foundation certification covering Kubernetes security including supply chain considerations.

#### Paid Training Programs

**SANS Secure Coding Courses**[^sans-coding]
Industry-recognized training covering secure development across multiple languages and platforms.

**Linux Foundation Security Training**[^lf-training]
Various courses on container security, Kubernetes security, and secure development practices.

---

### Newsletters, Blogs, and Ongoing Learning

#### Newsletters

**tl;dr sec**[^tldrsec]
Weekly newsletter curating security content with excellent coverage of supply chain security topics. Highly recommended.

**This Week in Security**[^this-week-security]
Weekly security news roundup covering vulnerabilities, incidents, and industry developments.

**Risky Business**[^risky-biz]
Security news podcast with excellent analysis of significant security events.

**Software Supply Chain Security Newsletter**[^scsc-news]
Focused specifically on supply chain security news and developments.

#### Blogs and Publications

**OpenSSF Blog**[^openssf-blog-main]
Official blog covering OpenSSF initiatives, research, and community updates.

**Trail of Bits Blog**[^trailofbits]
Technical security research from a leading security firm. Frequently covers supply chain topics.

**Google Security Blog**[^google-security-blog]
Official Google security blog with announcements about SLSA, Sigstore, and other initiatives.

**Chainguard Blog**[^chainguard]
Focused on supply chain security, container security, and Sigstore ecosystem.

**Socket.dev Blog**[^socket]
Analysis of supply chain attacks and package security across ecosystems.

**Snyk Blog**[^snyk-blog]
Regular vulnerability analyses, security research, and best practice guides.

#### Vulnerability Databases and Feeds

**National Vulnerability Database (NVD)**[^nvd]
Official U.S. government repository of CVE data with CVSS scores and analysis.

**GitHub Advisory Database**[^github-advisories]
Curated database of security advisories with direct links to affected packages.

**OSV (Open Source Vulnerabilities)**[^osv]
Google-maintained vulnerability database with API access and ecosystem coverage.

**VulnDB**[^vulndb]
Commercial vulnerability intelligence with broader coverage than NVD alone.

---

*Resources in this guide were verified as of the publication date. For the most current links and additional resources, visit the book's companion website or the OpenSSF resource collection.*

[^building-secure-reliable]: Google, "Building Secure and Reliable Systems," 2020, https://sre.google/books/building-secure-reliable-systems/

[^software-transparency]: Wiley, "Software Transparency: Supply Chain Security in an Era of a Software-Driven Society," 2022, https://www.wiley.com/en-us/Software+Transparency-p-9781119986362

[^threat-modeling]: Adam Shostack, "Threat Modeling: Designing for Security," 2014, https://shostack.org/books/threat-modeling-book

[^art-security-assessment]: Pearson, "The Art of Software Security Assessment," 2006, https://www.pearson.com/en-us/subject-catalog/p/art-of-software-security-assessment-the-identifying-and-preventing-software-vulnerabilities/P200000009486

[^hacking-kubernetes]: O'Reilly, "Hacking Kubernetes," 2021, https://www.oreilly.com/library/view/hacking-kubernetes/9781492081722/

[^alice-bob-security]: Wiley, "Alice and Bob Learn Application Security," 2020, https://www.wiley.com/en-us/Alice+and+Bob+Learn+Application+Security-p-9781119687405

[^practical-binary]: No Starch Press, "Practical Binary Analysis," 2018, https://nostarch.com/binaryanalysis

[^backstabbers-knife]: Marc Ohm et al., "Backstabber's Knife Collection: A Review of Open Source Software Supply Chain Attacks," 2020, https://arxiv.org/abs/2005.09535

[^in-toto-paper]: Santiago Torres-Arias et al., "in-toto: Providing farm-to-table guarantees for bits and bytes," USENIX Security 2019, https://www.usenix.org/conference/usenixsecurity19/presentation/torres-arias

[^dependency-confusion]: Alex Birsan, "Dependency Confusion: How I Hacked Into Apple, Microsoft and Dozens of Other Companies," 2021, https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610

[^malicious-pypi]: Ruian Duan et al., "An Empirical Study of Malicious Code in PyPI Ecosystem," ASE 2020, https://arxiv.org/abs/2309.11021

[^npm-security]: Markus Zimmermann et al., "A Look at the Security of npm," 2019, https://arxiv.org/abs/1902.09217

[^reproducible-builds-paper]: Chris Lamb and Stefano Zacchiroli, "Reproducible Builds: Increasing the Integrity of Software Supply Chains," IEEE Software 2022, https://arxiv.org/abs/2104.06020

[^world-of-code]: Yuxing Ma et al., "World of Code: An Infrastructure for Mining the Universe of Open Source VCS Data," MSR 2019, https://arxiv.org/abs/1906.07083

[^acm-tosem-ssc]: Hasan et al., "Research Directions in Software Supply Chain Security," ACM TOSEM, 2025, https://dl.acm.org/doi/abs/10.1145/3714464

[^arxiv-sok-ssc]: Ladisa et al., "SoK: Analysis of Software Supply Chain Security by Establishing Secure Design Properties," arXiv:2406.10109, 2024, https://arxiv.org/abs/2406.10109

[^sonatype-report]: Sonatype, "State of the Software Supply Chain Report," https://www.sonatype.com/state-of-the-software-supply-chain

[^snyk-report]: Snyk, "State of Open Source Security Report," https://snyk.io/reports/open-source-security/

[^openssf-blog]: OpenSSF, "OpenSSF Blog," https://openssf.org/blog/

[^cisa-ssdf]: NIST, "Secure Software Development Framework (SSDF)," https://csrc.nist.gov/Projects/ssdf

[^cisa-sbom]: CISA, "Software Bill of Materials (SBOM)," https://www.cisa.gov/sbom

[^lf-research]: Linux Foundation, "Research," https://www.linuxfoundation.org/research

[^synopsys-ossra]: Synopsys, "Open Source Security and Risk Analysis (OSSRA) Report," https://www.synopsys.com/software-integrity/resources/analyst-reports/open-source-security-risk-analysis.html

[^openssf]: Open Source Security Foundation, "OpenSSF," https://openssf.org

[^cosai]: Coalition for Secure AI, https://www.coalitionforsecureai.org/

[^cosai-resources]: Coalition for Secure AI, "Resources," https://www.coalitionforsecureai.org/resources/

[^cosai-risks-controls]: Coalition for Secure AI, "Establish Risks and Controls for the AI Supply Chain," V1.0, 2025, https://www.coalitionforsecureai.org/wp-content/uploads/2026/03/risks-and-controls-for-the-ai-supply-chain-v1.pdf

[^cosai-signing-ml]: Coalition for Secure AI, "Signing ML Artifacts," 2025, https://www.coalitionforsecureai.org/wp-content/uploads/2026/03/signing-ml-artifacts-1.pdf

[^cosai-defenders]: Coalition for Secure AI, "Preparing Defenders of AI Systems," V1.0, 2025, https://www.coalitionforsecureai.org/wp-content/uploads/2026/03/preparing-defenders-of-ai-systems.pdf

[^cosai-ir]: Coalition for Secure AI, "AI Incident Response Framework," V1.0, 2025, https://www.coalitionforsecureai.org/wp-content/uploads/2026/03/AI-Incident-Response-1.pdf

[^cosai-shared-responsibility]: Coalition for Secure AI, "AI Shared Responsibility Framework," V1.0, 2026, https://www.coalitionforsecureai.org/wp-content/uploads/2026/05/CoSAI-Shared-Responsibility-Framework.pdf

[^cosai-mcp]: Coalition for Secure AI, "Model Context Protocol (MCP) Security," 2026, https://www.coalitionforsecureai.org/wp-content/uploads/2026/03/model-context-protocol-security-1.pdf

[^cosai-agentic-iam]: Coalition for Secure AI, "Agentic Identity and Access Management," 2026, https://www.coalitionforsecureai.org/wp-content/uploads/2026/04/agentic-identity-and-access-control.pdf

[^cosai-agentic-future]: Coalition for Secure AI, "The Future of Agentic Security: From Chatbots to Autonomous Swarms," 2026, https://www.coalitionforsecureai.org/wp-content/uploads/2026/03/the-future-of-agentic-security.pdf

[^cisa]: CISA, "Cybersecurity and Infrastructure Security Agency," https://www.cisa.gov

[^mitre]: MITRE, "MITRE Corporation," https://www.mitre.org

[^first]: FIRST, "Forum of Incident Response and Security Teams," https://www.first.org

[^ietf]: IETF, "Internet Engineering Task Force," https://www.ietf.org

[^linux-foundation]: Linux Foundation, "Linux Foundation," https://www.linuxfoundation.org

[^apache]: Apache Software Foundation, "Apache Software Foundation," https://www.apache.org

[^cncf]: CNCF, "Cloud Native Computing Foundation," https://www.cncf.io

[^owasp]: OWASP, "Open Web Application Security Project," https://owasp.org

[^psf]: Python Software Foundation, "Python Software Foundation," https://www.python.org/psf/

[^rust-foundation]: Rust Foundation, "Rust Foundation," https://foundation.rust-lang.org

[^dependency-check]: OWASP, "OWASP Dependency-Check," https://owasp.org/www-project-dependency-check/

[^grype]: Anchore, "Grype," https://github.com/anchore/grype

[^snyk]: Snyk, "Snyk," https://snyk.io

[^dependabot]: GitHub, "Dependabot," https://github.com/dependabot

[^trivy]: Aqua Security, "Trivy," https://github.com/aquasecurity/trivy

[^syft]: Anchore, "Syft," https://github.com/anchore/syft

[^cyclonedx-tools]: CycloneDX, "CycloneDX Tool Center," https://cyclonedx.org/tool-center/

[^spdx-tools]: SPDX, "SPDX Tools," https://spdx.dev/tools/

[^sbom-scorecard]: eBay, "SBOM Scorecard," https://github.com/eBay/sbom-scorecard

[^sigstore]: Sigstore, "Sigstore," https://www.sigstore.dev

[^cosign]: Sigstore, "Cosign," https://github.com/sigstore/cosign

[^tuf]: TUF, "The Update Framework," https://theupdateframework.io

[^notary]: Notary Project, "Notary," https://github.com/notaryproject/notary

[^slsa-tools]: SLSA, "SLSA Get Started," https://slsa.dev/get-started

[^scorecard]: OpenSSF, "Security Scorecards," https://securityscorecards.dev

[^in-toto]: in-toto, "in-toto," https://in-toto.io

[^oss-gadget]: Microsoft, "OSS Gadget," https://github.com/microsoft/OSSGadget

[^codeql]: GitHub, "CodeQL," https://codeql.github.com

[^semgrep]: Semgrep, "Semgrep," https://semgrep.dev

[^capslock-appendix]: Capslock Project, https://capslock-project.github.io

[^sonarqube]: SonarSource, "SonarQube," https://www.sonarqube.org

[^bandit]: Bandit, "Bandit," https://bandit.readthedocs.io

[^oss-fuzz]: Google, "OSS-Fuzz," https://google.github.io/oss-fuzz/

[^aflplus]: AFL++, "AFL++," https://aflplus.plus

[^clusterfuzz]: Google, "ClusterFuzz," https://google.github.io/clusterfuzz/

[^gitleaks]: Gitleaks, "Gitleaks," https://github.com/gitleaks/gitleaks

[^trufflehog]: Truffle Security, "TruffleHog," https://github.com/trufflesecurity/trufflehog

[^detect-secrets]: Yelp, "detect-secrets," https://github.com/Yelp/detect-secrets

[^blackhat]: Black Hat, "Black Hat," https://www.blackhat.com

[^defcon]: DEF CON, "DEF CON," https://defcon.org

[^rsa]: RSA Conference, "RSA Conference," https://www.rsaconference.com

[^usenix]: USENIX, "USENIX Conferences," https://www.usenix.org/conferences

[^oss-summit]: Linux Foundation, "Linux Foundation Events," https://events.linuxfoundation.org

[^kubecon]: Linux Foundation, "KubeCon + CloudNativeCon," https://events.linuxfoundation.org/kubecon-cloudnativecon-north-america/

[^scscon]: Linux Foundation, "SupplyChainSecurityCon," https://events.linuxfoundation.org

[^owasp-appsec]: OWASP, "OWASP Events," https://owasp.org/events/

[^packagingcon]: PackagingCon, "PackagingCon," https://packaging-con.org

[^openssf-community]: OpenSSF, "OpenSSF Community," https://openssf.org/community/

[^cncf-tag-security]: CNCF, "CNCF Security TAG," https://github.com/cncf/tag-security

[^cncf-sscp-v2]: CNCF TAG Security, "Software Supply Chain Best Practices v2," November 2024, https://tag-security.cncf.io/blog/software-supply-chain-security-best-practices-v2/

[^openssf-training]: OpenSSF, "OpenSSF Training Courses," https://openssf.org/training/courses/

[^lfd121]: Linux Foundation, "Developing Secure Software (LFD121)," https://training.linuxfoundation.org/training/developing-secure-software-lfd121/

[^owasp-wstg]: OWASP, "Web Security Testing Guide," https://owasp.org/www-project-web-security-testing-guide/

[^google-security]: Google, "Google Developers Security," https://developers.google.com/security

[^csslp]: ISC2, "Certified Secure Software Lifecycle Professional (CSSLP)," https://www.isc2.org/Certifications/CSSLP

[^gssp]: GIAC, "GIAC Secure Software Programmer," https://www.giac.org/certifications/secure-software-programmer-java-gssp-java/

[^cks]: Linux Foundation, "Certified Kubernetes Security Specialist (CKS)," https://training.linuxfoundation.org/certification/certified-kubernetes-security-specialist/

[^sans-coding]: SANS, "Secure Software Development Courses," https://www.sans.org/cyber-security-courses/?focus-area=secure-software-development

[^lf-training]: Linux Foundation, "Linux Foundation Training," https://training.linuxfoundation.org/training/

[^tldrsec]: tl;dr sec, "tl;dr sec Newsletter," https://tldrsec.com

[^this-week-security]: Teleport, "This Week in Security," https://this.teleport.com/thisweekin/

[^risky-biz]: Risky Business, "Risky Business," https://risky.biz

[^scsc-news]: SCSC News, "Software Supply Chain Security Newsletter," https://scscnews.com

[^openssf-blog-main]: OpenSSF, "OpenSSF Blog," https://openssf.org/blog/

[^trailofbits]: Trail of Bits, "Trail of Bits Blog," https://blog.trailofbits.com

[^google-security-blog]: Google, "Google Security Blog," https://security.googleblog.com

[^chainguard]: Chainguard, "Chainguard Blog," https://www.chainguard.dev/unchained

[^socket]: Socket, "Socket.dev Blog," https://socket.dev/blog

[^snyk-blog]: Snyk, "Snyk Blog," https://snyk.io/blog/

[^nvd]: NIST, "National Vulnerability Database," https://nvd.nist.gov

[^github-advisories]: GitHub, "GitHub Advisory Database," https://github.com/advisories

[^osv]: Google, "Open Source Vulnerabilities (OSV)," https://osv.dev

[^vulndb]: Flashpoint, "VulnDB," https://vulndb.cyberriskanalytics.com
