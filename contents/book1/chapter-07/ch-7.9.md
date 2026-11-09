---
title: "Case Study: Notepad++ Update Hijacking (2025)"
description: "How state-sponsored attackers compromised hosting infrastructure to selectively serve malicious updates to targeted victims."
icon: "lucide/file-edit"
---

# 7.9 Case Study: Notepad++ Update Hijacking (2025)

Section 7.8 examined distribution channel attacks in general terms, from compromised mirrors and CDNs to update mechanism hijacking and domain takeovers. The Notepad++ incident in 2025 illustrates several of these risks in a specific, instructive case that also introduces a distinct tactical pattern worth understanding.

In mid-2025, attackers attributed to the Chinese APT group Lotus Blossom/Spring Dragon (attribution by Kaspersky[^kaspersky-notepadpp]) compromised the shared hosting provider used by Notepad++ to hijack update traffic.[^notepadpp-incident] Unlike build system compromises where attackers inject malware into the official build, this attack intercepted traffic to the legitimate update endpoint (`notepad-plus-plus.org/update/getDownloadUrl.php`) and served trojanized installers only to targeted victims. The source code, build process, and signing infrastructure were never compromised: the attack existed entirely at the distribution layer.

## A Precision Supply Chain Attack

The Notepad++ attack represents what we might call a **precision supply chain attack**: leveraging infrastructure-level access to the update pipeline combined with network-level victim selection to minimize the attack's detection footprint. Unlike SolarWinds, where all approximately 18,000 recipients received the same backdoored binary and targeting was implemented in the payload itself, here non-targeted users never received malicious content at all. This creates a fundamentally harder detection problem: there is no malicious artifact to discover through broad scanning because most users simply never encounter one.

## Three Infection Chains

The attack employed three distinct infection chains over several months, each reflecting an evolution in operational tradecraft:

1. **Chain #1 (July-August 2025):** NSIS installer exploiting a ProShow vulnerability to deliver Cobalt Strike beacons
2. **Chain #2 (September-October 2025):** Lua interpreter abuse as a **living-off-the-land** technique (using legitimate tools already present on the system rather than deploying custom malware) for payload delivery
3. **Chain #3 (October 2025):** DLL sideloading through a malicious `log.dll` delivering the Chrysalis backdoor

The progression across these chains is itself instructive. The shift from Cobalt Strike, a well-known and broadly detected offensive framework, to Lua interpreter abuse (a living-off-the-land technique that blends with legitimate development tooling) and finally to the custom Chrysalis backdoor suggests an adversary actively adapting its tooling to reduce detection probability. For defenders, this pattern underscores that initial detection of one toolchain variant does not mean the campaign has been contained: attackers may have already pivoted to different delivery mechanisms.

The selective targeting (redirecting only specific users while serving legitimate updates to others) made detection particularly difficult. Confirmed victims included government organizations in the Philippines, financial institutions in El Salvador, and IT service providers in Vietnam.[^kaspersky-notepadpp]

## Key Defensive Lessons

**Infrastructure dependencies are supply chain dependencies.** The attack exploited shared hosting infrastructure, not Notepad++'s source code or build system. Organizations must evaluate the security of their hosting providers, CDNs, and DNS services as critically as their own code. "Shared hosting" in this context means multi-tenant web hosting where multiple customers share the same server infrastructure, a compromise of the hosting provider's systems can affect all tenants.

**The vulnerability has formal recognition, and a remediation deadline.** The underlying weakness in WinGUp was assigned **CVE-2025-15556** (CWE-494: Download of Code Without Integrity Check, CVSS v3.x base score 7.5).[^cve-2025-15556] On February 12, 2026, CISA added it to the **Known Exploited Vulnerabilities (KEV) catalog** with a federal remediation due date of March 5, 2026, reflecting evidence of exploitation in the wild.[^kev-notepadpp] The KEV entry notes "known ransomware use: unknown," meaning the exploitation evidence is tied to the APT campaign described here rather than ransomware. For organizations using vulnerability prioritization frameworks (Section 5.3), KEV inclusion for a *developer tool updater* is a notable signal: it demonstrates that CISA treats developer-facing software as infrastructure worthy of the same remediation urgency as production systems.

[^cve-2025-15556]: NIST, "CVE-2025-15556," NVD, https://nvd.nist.gov/vuln/detail/CVE-2025-15556
[^kev-notepadpp]: CISA, Known Exploited Vulnerabilities Catalog, entry for CVE-2025-15556, added February 12, 2026, https://www.cisa.gov/known-exploited-vulnerabilities-catalog

**Signature verification must confirm the expected publisher, not just signature validity.** Notepad++'s updater (WinGUp) confirmed that downloaded installers carried a valid code-signing certificate from a trusted certificate authority, but it did not verify that the certificate belonged to the expected publisher (Notepad++ / Don Ho). Attackers served installers signed with legitimately issued but *different* certificates. Post-incident, the updater was hardened to verify both the digital signature and the expected certificate identity for downloaded installers.[^notepadpp-incident] This same principle applies broadly: any verification system that checks "is this signed?" without checking "is this signed by whom I expect?" provides a false sense of security.

**Signed update manifests prevent tampering.** The attack redirected requests to a PHP endpoint that returned update metadata. Following the incident, Notepad++ implemented XMLDSig signatures on update manifests and enforced manifest-signature verification in v8.9.2, ensuring that even if traffic is redirected, tampered manifests will be rejected.[^notepadpp-v892] The defenses Notepad++ implemented post-incident, signed update manifests and certificate verification, are a subset of the protections provided by The Update Framework (TUF, discussed in Section 7.8). TUF's comprehensive model, including key rotation, threshold signing, and metadata freshness guarantees, would have prevented not only this specific attack but also rollback attacks and other update manipulation techniques. For software publishers maintaining their own update mechanisms, TUF provides a battle-tested framework rather than requiring ad hoc solutions.

**Selective targeting evades broad monitoring.** Because most users received legitimate updates, the attack evaded detection for months. Behavioral monitoring and user reports ultimately contributed to discovery: users noticed connections to unusual domains like `temp.sh` (a file-sharing service) from Notepad++'s update process. This highlights an underexplored detection strategy: user reports of anomalous network behavior from trusted software represent a valuable signal. Organizations should establish formal channels for users to report unusual software behavior, and SOC teams should monitor for connections to file-sharing services and other unexpected domains from update processes.

The Notepad++ incident demonstrates that securing the software supply chain requires extending trust verification beyond source code and build systems to encompass the entire distribution infrastructure. As attackers increasingly combine infrastructure-level access with precision targeting, the detection burden shifts from artifact analysis to behavioral monitoring and infrastructure integrity verification.

[^kaspersky-notepadpp]: Kaspersky Securelist, "A Supply Chain Attack on Notepad++," February 2, 2026, https://securelist.com/notepad-supply-chain-attack/118708/

[^notepadpp-incident]: Notepad++, "Hijacked Incident Info Update," February 2, 2026, https://notepad-plus-plus.org/news/hijacked-incident-info-update/

[^notepadpp-v892]: Notepad++, "v8.9.2 Released," February 16, 2026, https://notepad-plus-plus.org/news/v892-released/
