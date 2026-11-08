---
title: "Fake Security Researchers and Malicious Fixes"
description: "Recognize attackers posing as researchers who pressure maintainers into merging 'fixes' that introduce vulnerabilities."
icon: "lucide/venetian-mask"
---

# 8.6 Fake Security Researchers and Malicious Fixes

Security researchers play a vital role in open source security, identifying vulnerabilities and helping projects fix them before exploitation. Legitimate researchers follow **responsible disclosure** practices, providing detailed reports and often contributing patches. Attackers have recognized this trusted relationship and begun exploiting it—posing as security researchers to pressure maintainers into merging malicious "fixes" that introduce vulnerabilities rather than remediate them.

This attack vector weaponizes the urgency and authority that legitimate security reports carry. Maintainers, already stretched thin, face pressure to act quickly on potential vulnerabilities. That pressure creates opportunity for manipulation.

## The Rise of Fake Vulnerability Reports

Security teams have documented attackers building [fake security researcher personas and malicious proof-of-concept repositories][fake-researchers]. Maintainers face a related pattern: fake vulnerability reports and "fixes" that use the authority of security research to manipulate project decisions. These campaigns share common characteristics:

**Mass-Submitted "Vulnerability" Reports:**

Attackers submit nearly identical reports to hundreds of projects, claiming to have discovered security vulnerabilities. The reports often:

- Reference legitimate vulnerability classes (XSS, SQL injection, SSRF)
- Include generic descriptions applicable to many projects
- Claim high severity requiring urgent action
- Offer to provide patches or request maintainer contact

**Plausible Authority Claims:**

Fake researchers present credentials that appear legitimate:

- Professional-looking email domains
- LinkedIn profiles with security job titles
- References to CVEs (sometimes real CVEs unrelated to the report)
- Claims of affiliation with security firms or bug bounty platforms

**Automated or Semi-Automated Submission:**

The volume of fake reports suggests automation:

- Identical text across projects suggests templates
- Reports may target specific project characteristics (certain languages, frameworks)
- Timing patterns indicate coordinated campaigns

Maintainers of popular packages have documented receiving dozens of fake vulnerability reports in short periods—each from different apparent researchers, each requiring investigation that consumes maintenance time.

## Malicious Pull Requests Disguised as Security Fixes

Beyond simple harassment or time-wasting, some campaigns have more sinister goals: submitting patches that claim to fix vulnerabilities but actually introduce them.

**The Attack Pattern:**

1. Attacker identifies a target project
2. Attacker reports a plausible vulnerability (real or fabricated)
3. Attacker offers to provide a fix, or submits a PR claiming to address the issue
4. The "fix" contains subtle changes that introduce actual vulnerabilities
5. Maintainer, believing they're addressing a security issue, merges the malicious code

**Technical Approaches:**

Malicious "fixes" use techniques to hide their true nature:

- **Unnecessary changes**: The PR includes many changes beyond the stated fix, hiding malicious modifications in the noise
- **Subtle logic changes**: Conditions are altered, bounds checks are weakened, or error handling is modified in ways that introduce exploitable flaws
- **Comments that mislead**: Code comments explain what the code "should" do, not what it actually does
- **Build or test modifications**: Changes to CI configuration, test cases, or build scripts that mask the vulnerability or enable future attacks

**Example Pattern:**

A fake report claims: "Your URL parsing library is vulnerable to SSRF because it doesn't validate protocols."

The submitted "fix":

```python
def parse_url(url):
    # Security fix: validate protocol to prevent SSRF
    if url.startswith(('http://', 'https://', 'file://')):  # Added file:// "by accident"
        return urllib.parse.urlparse(url)
    raise ValueError("Invalid protocol")
```

The "fix" adds `file://` support, actually introducing local file access vulnerability while claiming to restrict protocols.

## Social Pressure Tactics

Attackers exploit the dynamics of security disclosure to pressure rapid action:

**Artificial Urgency:**

- Claims that attackers are "already exploiting" the vulnerability
- Threats of public disclosure within hours
- Assertions that users are at immediate risk
- References to active incident response situations

**Authority and Expertise:**

- Technical language and jargon suggesting deep expertise
- References to past disclosures or CVEs
- Claims of working for well-known security firms
- Professional presentation (formatted reports, logos)

**Guilt and Responsibility:**

- Implications that delay puts users at risk
- Suggestions that proper security practice demands immediate action
- Comparisons to how "other projects" responded faster

**Persistence:**

- Follow-up emails escalating urgency
- Requests for status updates applying pressure
- Implied criticism of slow response

Legitimate security researchers also apply some pressure—they have genuine concerns about vulnerability timelines. But legitimate researchers generally:

- Provide reasonable disclosure timelines ([typically 90 days][project-zero-policy])
- Engage in technical dialogue about the vulnerability
- Accept that verification takes time
- Have verifiable track records

## Vetting Researchers and Validating Reports

!!! tip "Verifying Security Reports"

    1. Check the sender's history for legitimate prior disclosures
    2. Verify claimed affiliations through official channels
    3. Legitimate researchers provide reasonable timelines (typically 90 days)
    4. They engage in technical dialogue and accept that verification takes time

Maintainers receiving security reports must balance speed with verification:

**Researcher Verification:**

1. **Check the sender's history**: Do they have a track record of legitimate disclosures? Can you find prior CVEs they've reported?

2. **Verify claimed affiliations**: If they claim to work for a security firm, verify through the firm's official channels, not contact information they provide.

3. **Assess communication patterns**: Legitimate researchers typically engage technically. Generic reports or refusal to provide details are red flags.

4. **Check researcher databases**: Platforms like [HackerOne][hackerone] and [Bugcrowd][bugcrowd] maintain researcher profiles with history.

**Vulnerability Validation:**

1. **Reproduce the issue**: Before accepting any fix, independently verify the vulnerability exists. If the reporter cannot provide reproduction steps, be skeptical.

2. **Understand the impact**: A legitimate vulnerability should have a clear explanation of how it could be exploited and what the consequences would be.

3. **Evaluate the proposed fix**: Review any submitted patch as carefully as any other contribution—more carefully, given the security-sensitive context.

4. **Cross-reference CVEs**: If a CVE is mentioned, verify it exists and actually relates to the claimed issue. Attackers sometimes reference unrelated CVEs for authority.

5. **Consult other maintainers**: If your project has multiple maintainers, involve them in evaluating reports. Fresh perspectives catch issues.

**Red Flags in Reports:**

- Generic descriptions applicable to any project
- Inability to provide specific reproduction steps
- Pressure targeting timeline rather than technical discussion
- "Fixes" that make changes beyond what's needed
- Reports from newly-created accounts or emails
- Unusual contact patterns (odd hours, formal tone inconsistent with claimed expertise)

## The Tension Between Speed and Verification

Security response genuinely requires urgency. Vulnerabilities that are being actively exploited must be addressed quickly. This creates tension:

**When Speed Matters:**

- Active exploitation is confirmed
- The vulnerability is easily discovered and exploitation is trivial
- The vulnerability affects critical functionality
- Public disclosure has already occurred

**When Verification Matters More:**

- The report comes from an unknown source
- The vulnerability is complex and hard to verify
- The proposed fix is substantial or touches sensitive code
- Something about the report feels "off"

**Balancing Approach:**

1. **Acknowledge quickly, act carefully**: Respond to reports promptly (within days), but explain that verification is part of responsible handling.

2. **Implement minimal fixes first**: If urgent, address the immediate issue with the smallest possible change rather than accepting complex submitted patches.

3. **Develop fixes independently**: Rather than accepting attacker-supplied code, develop your own fix based on understanding the vulnerability.

4. **Stage the response**: Release an initial mitigation quickly if needed, then a comprehensive fix after proper review.

5. **Accept that some urgency is manufactured**: Attackers create false urgency. Legitimate researchers understand that responsible handling takes time.

## Impact on Legitimate Research Relationships

Fake security researcher campaigns impose costs beyond immediate time consumption:

**Maintainer Skepticism:**

Maintainers who have experienced fake reports may become skeptical of legitimate researchers. This skepticism can manifest as:

- Slow or no response to vulnerability reports
- Demands for excessive proof before engaging
- Hostility toward unsolicited security contact
- Reluctance to acknowledge vulnerabilities

**Researcher Frustration:**

Legitimate researchers encountering skeptical or unresponsive maintainers may:

- Give up on coordinated disclosure
- Resort to public disclosure without coordination
- Avoid reporting vulnerabilities to certain projects
- Feel their work is undervalued

**Community Trust Erosion:**

The relationship between security researchers and open source maintainers is essential for ecosystem security. Attacks that exploit this relationship damage it for everyone. Maintainers report that dealing with fake security reports makes them slower to respond to legitimate researchers—precisely the outcome attackers may be seeking.

## Recommendations

**For Maintainers:**

1. **Establish a security contact and policy.** A clear security policy (`SECURITY.md`) sets expectations for both reporters and maintainers.

2. **Verify before acting.** Always reproduce vulnerabilities independently. Never merge fixes you don't understand.

3. **Develop fixes independently.** Use reporter information to understand the issue, but write your own fix rather than accepting supplied code.

4. **Take time for verification.** Legitimate researchers will accept reasonable timelines. Excessive pressure is a warning sign.

5. **Document patterns.** Keep records of suspicious reports. Patterns across projects help the community identify campaigns.

6. **Consult others.** Security teams at foundations ([Apache][apache-security], [Linux Foundation][linux-foundation]) and platforms ([GitHub Security Lab][github-securitylab]) can help evaluate suspicious reports.

**For Organizations:**

1. **Support maintainers with security resources.** Provide access to security expertise for evaluating reports.

2. **Establish verification procedures.** Document how security reports should be handled, including verification steps.

3. **Create communication channels.** Enable maintainers to consult security teams quickly when suspicious reports arrive.

**For the Security Research Community:**

1. **Build and maintain reputation.** Consistent, professional disclosure builds trust that protects against impersonation.

2. **Provide reproducible reports.** Detailed, reproducible reports distinguish legitimate research from fake campaigns.

3. **Respect verification timelines.** Accept that maintainers must verify reports; patience demonstrates legitimacy.

4. **Use established channels.** Platform-based disclosure ([GitHub Security Advisories][github-advisories], [HackerOne][hackerone]) provides verification that email cannot.

Fake security researchers exploit the trust that legitimate researchers have earned. Defending against this attack requires balancing the urgency that real vulnerabilities demand against the verification that sophisticated attacks require. The goal is maintaining productive relationships with legitimate researchers while developing the skepticism necessary to resist manipulation. When in doubt, verify independently, develop fixes yourself, and remember that genuine urgency can coexist with reasonable caution.

[fake-researchers]: https://www.vulncheck.com/blog/fake-repos-deliver-malicious-implant
[project-zero-policy]: https://googleprojectzero.blogspot.com/p/vulnerability-disclosure-policy.html
[hackerone]: https://www.hackerone.com/
[bugcrowd]: https://www.bugcrowd.com/
[apache-security]: https://www.apache.org/security/
[linux-foundation]: https://www.linuxfoundation.org/
[github-securitylab]: https://securitylab.github.com/
[github-advisories]: https://docs.github.com/en/code-security/security-advisories
