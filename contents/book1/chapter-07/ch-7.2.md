---
title: "Case Study: SolarWinds and the SUNBURST Attack"
description: "Analyze the watershed attack that reached 18,000 organizations through compromised software updates and reshaped supply chain security."
icon: "lucide/sun"
---

# 7.2 Case Study: SolarWinds and the SUNBURST Attack

In December 2020, the cybersecurity industry confronted an attack that would fundamentally reshape understanding of supply chain risk. The compromise of SolarWinds' Orion platform (subsequently named **SUNBURST** by FireEye/Mandiant (and **Solorigate** by Microsoft), with **SUNSPOT** used by CrowdStrike for the implant that modified the build) demonstrated that nation-state adversaries could infiltrate trusted software distribution channels with extraordinary sophistication. The attack [reached approximately 18,000 organizations][solarwinds-sec], including critical U.S. government agencies and Fortune 500 companies, through software updates that customers had every reason to trust.

SUNBURST became the watershed moment for software supply chain security, triggering government policy changes, industry investment, and a fundamental reassessment of how organizations evaluate trust in their software dependencies.

## Background: SolarWinds and the Orion Platform

SolarWinds, founded in 1999 and headquartered in Austin, Texas, developed IT management software used by organizations worldwide. Their flagship product, **Orion**, provided network monitoring, performance analysis, and IT infrastructure management capabilities.

Orion's market position made it an attractive target:

- **Ubiquitous deployment**: [Over 300,000 customers globally][solarwinds-sec], including most Fortune 500 companies and major government agencies
- **Deep access**: Network monitoring software necessarily has extensive visibility into the environments it monitors, seeing traffic, configurations, and system status
- **Trusted updates**: Customers routinely accepted Orion updates as coming from a trusted vendor
- **Privileged positioning**: Orion servers typically had network access and credentials necessary for monitoring infrastructure across the enterprise

This combination (wide deployment, deep access, and implicit trust) made Orion an ideal supply chain attack vector. Compromising Orion would provide access to thousands of high-value networks through a single attack.

## The Attack: Build System Compromise

The SUNBURST attack did not target SolarWinds' source code repository in a way that would be visible to developers or code reviewers. Instead, attackers compromised the build infrastructure itself, modifying the compiled output without changing the source files.

**Initial Access (October 2019):**

CrowdStrike's analysis revealed that attackers first accessed SolarWinds' environment in late 2019. They conducted reconnaissance, established persistence, and studied the build process for several months before deploying their attack mechanism.

**Build Modification (February 2020):**

Attackers deployed a tool [CrowdStrike named **SUNSPOT**][crowdstrike-sunspot] into the Orion build environment. This implant monitored the build process and, when it detected compilation of the `SolarWinds.Orion.Core.BusinessLayer` DLL, it replaced a source file with a malicious version just before compilation.

The replacement was surgical:

1. SUNSPOT monitored for `MsBuild.exe` processes
2. When it detected compilation of the target DLL, it replaced `InventoryManager.cs` with a modified version
3. The modified source included malicious code
4. After compilation, the original source file was restored
5. The resulting binary was malicious, but source code inspection would reveal nothing

This approach meant that:

- Source code in version control remained clean
- Code reviews would not detect the modification
- Build-time security scans of source code would miss the threat
- The malicious code only existed in the compiled binary

**Distribution (March - June 2020):**

The compromised DLL was distributed in Orion versions 2019.4 HF5 through 2020.2.1. These updates were digitally signed by SolarWinds, appearing completely legitimate to customers and security tools.

Over 18,000 organizations downloaded and installed the malicious updates. The attackers had successfully placed their implant in the heart of these organizations' networks through what appeared to be a routine software update.

## Technical Details: SUNBURST Capabilities

The malicious code inserted into the Orion DLL demonstrated sophisticated tradecraft designed to evade detection:

!!! info "SUNBURST Evasion Techniques"

    - **12-14 day dormancy** before any malicious activity
    - **Environment checks** to detect security analysis tools
    - **DNS-based C2** encoding victim info in subdomain queries
    - **Selective targeting**: Only ~100-200 of 18,000 victims received second-stage payloads

**Extended Dormancy:**

SUNBURST remained dormant for approximately 12-14 days after installation before executing any malicious activity. This delay helped evade sandbox-based security tools, which typically monitor software for only minutes or hours after installation.

**Environment Checks:**

Before activating, the malware checked for indicators that it might be running in a security analysis environment:

- Security tools and antivirus software
- Specific process names associated with analysis
- Domain names suggesting test environments
- Debugging tools or forensic software

If any indicated a security research context, the malware would remain dormant or disable itself.

**Domain Generation Algorithm (DGA):**

For command and control communication, SUNBURST used a sophisticated DGA that encoded victim information into DNS queries for subdomains of `avsvmcloud[.]com`. The subdomain encoded:

- A hash of the victim's domain name
- A unique machine identifier
- Status information about the implant

DNS queries to randomly-appearing subdomains are notoriously difficult to distinguish from legitimate traffic, particularly when the parent domain appears innocuous.

**Selective Targeting:**

The attackers did not exploit all 18,000 victims. Instead, they reviewed the information returned via DNS beacons and selected high-value targets for further exploitation. Only an estimated 100-200 organizations received second-stage payloads.

This selectivity served two purposes:

1. It limited exposure: fewer active intrusions meant lower detection probability
2. It focused resources on valuable targets rather than attempting to exploit everyone

**Legitimate Appearance:**

The malicious code was written to resemble legitimate Orion code in style, naming conventions, and architecture. Security researchers noted that the code appeared to be written by experienced developers familiar with the Orion codebase, making detection through code quality anomalies essentially impossible.

In its investigation of the SolarWinds supply chain attack and the concurrent compromise of FireEye's own systems, [FireEye stated][fireeye] that the intrusion was carried out by a highly sophisticated threat actor whose operational security and techniques suggested a nation-state campaign, and that the level of discipline and clandestine operations exceeded typical cyber incidents. Detailed technical analyses from [Microsoft][microsoft-sunburst] and [Mandiant][fireeye-sunburst] documented the malware's command-and-control protocols, anti-analysis techniques, and the surgical precision of the code injection.

## Discovery: FireEye Uncovers the Breach

The attack was discovered not through detection of SUNBURST itself, but through its consequences.

**December 8, 2020:**

FireEye, a leading cybersecurity firm, disclosed that it had been breached and that attackers had stolen red team tools: the same types of tools FireEye used to test clients' security. This was concerning but, initially, appeared to be an isolated incident.

**December 13, 2020:**

FireEye's investigation revealed that the breach had occurred through a compromised SolarWinds Orion update. FireEye publicly disclosed SUNBURST, alerting the broader community to the supply chain compromise.

**December 13-14, 2020:**

CISA issued Emergency Directive 21-01, requiring federal civilian agencies to immediately disconnect SolarWinds Orion products from their networks. Microsoft, CrowdStrike, and other security firms began analyzing the malware and identifying victims.

**Subsequent weeks:**

The scope of the attack became clear. Victims included:

- **U.S. Treasury Department**: Emails monitored for months
- **U.S. Commerce Department (NTIA)**: Networks compromised
- **Department of Homeland Security**: Including CISA, ironically the agency responsible for federal cybersecurity
- **U.S. State Department**: Long-term access established
- **U.S. Department of Energy**: Including NNSA (National Nuclear Security Administration)
- **FireEye**: Security firm's red team tools stolen
- **Microsoft**: Internal systems accessed, source code repositories viewed
- **Numerous Fortune 500 companies**: Technology, telecommunications, and professional services firms

The investigation revealed that attackers had maintained access to some victims for 6-9 months before discovery. During this time, they had exfiltrated data, monitored communications, and established persistence mechanisms beyond the initial SUNBURST implant.

## Attribution: Russian Intelligence Services

The U.S. government formally attributed the attack to Russia's Foreign Intelligence Service (SVR), also tracked by security researchers as **APT29** or **Cozy Bear**.

On April 15, 2021, [the White House issued a statement][whitehouse-attribution]:

> "The U.S. Intelligence Community has high confidence that Russia's SVR was behind the broad-scope cyber espionage campaign that exploited the SolarWinds Orion platform and other information technology infrastructures."

The statement accompanied sanctions against Russian entities and individuals, as well as the expulsion of Russian diplomats.

The attribution aligned with the attack's characteristics:

- Targeting of government agencies and major corporations aligned with intelligence collection priorities
- Sophistication and patience consistent with well-resourced nation-state actors
- Selective exploitation focused on high-value targets rather than financial gain
- Prior APT29 activity demonstrated similar tradecraft

## Why Traditional Security Failed

SUNBURST evaded security tools that organizations reasonably believed would detect supply chain compromises:

**Signed by Trusted Vendor:**

The malicious DLL was signed with SolarWinds' legitimate code signing certificate. Security tools that verify signatures would see a validly signed binary from a known vendor, exactly what should be allowed.

**Delivered Through Official Channels:**

The malware arrived through SolarWinds' official update mechanism. Organizations that restrict software installation to approved sources had Orion approved. The update came from where it was supposed to come from.

**Dormancy Evaded Sandboxes:**

Security sandboxes that analyze software behavior typically monitor for minutes or hours. SUNBURST's 12-14 day dormancy period far exceeded these windows.

**Anti-Analysis Techniques:**

The malware checked for security tools before activating. In security research environments, it would appear inert.

**Legitimate-Looking Code:**

The malicious code was written to match Orion's coding style. Automated tools looking for anomalous code patterns found nothing unusual.

**Selective Activation:**

By only exploiting selected targets, the attackers minimized unusual network behavior that might trigger detection.

This combination of techniques represented a fundamental challenge: the attack succeeded because it precisely mimicked legitimate behavior at every level where security controls operate.

## Response: Industry and Government Action

The SolarWinds attack triggered significant responses:

**Immediate Response:**

- [CISA Emergency Directive 21-01][cisa-ed-21-01] required federal agencies to disconnect affected systems
- SolarWinds released hotfixes for affected versions
- Microsoft, GoDaddy, and others took action to disable the `avsvmcloud[.]com` domain used for command and control
- Victims began intensive forensic investigations

**Policy Response - Executive Order 14028:**

The SolarWinds attack catalyzed the most comprehensive U.S. government intervention into software supply chain security. On May 12, 2021, President Biden signed [Executive Order 14028][eo-14028], "Improving the Nation's Cybersecurity," which directly addressed supply chain lessons from SUNBURST. Key provisions include requirements for Software Bills of Materials (SBOMs), secure software development practice attestations, build integrity improvements, and Zero Trust adoption: all establishing market-access requirements that extended the government's influence across the commercial software industry.

For detailed regulatory requirements, compliance timelines, implementation guidance, and the full impact of Executive Order 14028 on federal contractors and the broader software industry, see Book 3, Section 26.1, "U.S. Executive Order 14028 and Federal Requirements."

**Industry Response:**

- Major technology companies increased investment in supply chain security
- Cloud providers enhanced build integrity features
- The OpenSSF SLSA framework gained prominence as organizations sought structured approaches to build security
- Security firms developed capabilities specifically targeting supply chain threats

## Lessons Learned

The SolarWinds attack taught, or reinforced, critical lessons for software security:

**1. Build systems require security investment proportional to their risk.**

Build infrastructure is not just "plumbing." It is security-critical infrastructure with access to signing keys, code, and distribution channels. Organizations must secure build systems with the same rigor applied to production systems.

**2. Code signing alone is insufficient.**

SUNBURST was signed with a legitimate certificate. Signature verification confirms that code was signed by the stated entity, not that the entity's systems were secure. Signing is necessary but not sufficient.

**3. Supply chain attacks can operate within trust boundaries.**

Traditional security models assume that trusted vendors provide trustworthy software. SUNBURST demonstrated that adversaries can compromise trusted vendors, requiring organizations to reconsider trust assumptions even for approved software.

**4. Dormancy and selectivity evade behavioral detection.**

Security tools designed to detect malicious behavior struggle when that behavior is delayed and selective. Detection strategies must account for patient adversaries.

**5. Source code review does not guarantee binary integrity.**

SUNBURST modified compiled output without changing source code. Reviewing source provides no assurance about the build process. Reproducible builds and build provenance verification address this gap.

**6. Visibility into the software supply chain is essential.**

Organizations discovered they lacked basic visibility into what software they ran and what that software contained. SBOM initiatives directly address this gap.

**7. Nation-state adversaries will invest in sophisticated supply chain attacks.**

The level of investment and patience demonstrated in SUNBURST (months of preparation, careful coding, selective exploitation) showed that well-resourced adversaries view supply chain compromise as worth significant investment.

## SolarWinds as Turning Point

The SolarWinds attack marked a turning point in supply chain security awareness. Prior incidents like the 2015 XcodeGhost malware or the 2017 CCleaner compromise had demonstrated supply chain attack feasibility, but SUNBURST's combination of sophistication, scope, and high-profile victims brought supply chain security to boardrooms, congressional hearings, and government policy.

The incident established expectations that supply chain attacks would become more common and that organizations must implement defenses beyond trusting their vendors. The case studies that follow, 3CX (Section 7.3) and Codecov (Section 7.4), demonstrate that these expectations were warranted: adversaries continued to target build and distribution infrastructure, applying lessons from both SolarWinds' success and its eventual detection.

[fireeye]: https://cloud.google.com/blog/topics/threat-intelligence/unauthorized-access-of-fireeye-red-team-tools
[solarwinds-sec]: https://www.sec.gov/ix?doc=/Archives/edgar/data/1739942/000173994220000075/swi-20201231.htm
[crowdstrike-sunspot]: https://www.crowdstrike.com/blog/sunspot-malware-technical-analysis/
[microsoft-sunburst]: https://www.microsoft.com/en-us/security/blog/2020/12/18/analyzing-solorigate-the-compromised-dll-file-that-started-a-sophisticated-cyberattack-and-how-microsoft-defender-helps-protect/
[fireeye-sunburst]: https://cloud.google.com/blog/topics/threat-intelligence/evasive-attacker-leverages-solarwinds-supply-chain-compromises-with-sunburst-backdoor
[cisa-ed-21-01]: https://www.cisa.gov/news-events/directives/ed-21-01-mitigate-solarwinds-orion-code-compromise
[whitehouse-attribution]: https://bidenwhitehouse.archives.gov/briefing-room/statements-releases/2021/04/15/fact-sheet-imposing-costs-for-harmful-foreign-activities-by-the-russian-government/
[eo-14028]: https://www.federalregister.gov/documents/2021/05/17/2021-10460/improving-the-nations-cybersecurity
