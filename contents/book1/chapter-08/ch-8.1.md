---
title: "Compromised Maintainer Accounts"
description: "Learn how stolen maintainer credentials enable attackers to publish malicious packages through official channels in minutes."
icon: "lucide/key"
---

# 8.1 Compromised Maintainer Accounts

Chapter 7 examined attacks that compromise build systems and distribution infrastructure—complex operations requiring access to protected servers and sophisticated persistence mechanisms. But there is often a simpler path to supply chain compromise: stealing a maintainer's credentials. With a maintainer's username and password, an attacker can publish malicious package versions directly, no build system compromise required. The attack completes in minutes, the malicious code flows through official channels, and downstream users install it without suspicion.

Maintainer accounts are among the highest-value credentials in the software ecosystem. Their compromise has repeatedly enabled supply chain attacks affecting millions of users.

## The Value of Maintainer Accounts

A maintainer account provides direct publishing access to packages that may be installed millions of times daily. Consider the asymmetry:

- A single compromised npm account can push malicious updates to packages with millions of weekly downloads
- Every CI/CD pipeline that runs `npm install` or `pip install` will fetch the malicious version
- Installation scripts execute immediately, before any human review
- The malicious package arrives through official, trusted channels

Unlike attacking a company's servers—which requires finding vulnerabilities, establishing persistence, and evading detection—attacking a maintainer account requires only obtaining credentials. Many maintainers are volunteers without security training, using consumer email accounts and reused passwords.

The leverage is extraordinary. An attacker who compromises the maintainer of a package like `lodash` or `requests`—each with tens of millions of weekly downloads—gains code execution on a substantial fraction of the world's development infrastructure.

This leverage makes maintainer accounts targets for:

- **Financially motivated attackers**: Seeking cryptocurrency credentials, payment information, or resources for cryptomining
- **Nation-state actors**: Seeking access to specific organizations that use targeted packages
- **Hacktivists**: Seeking platforms for political messages or destructive actions

## Account Takeover Techniques

Attackers use multiple techniques to compromise maintainer accounts:

**Credential Stuffing:**

When data breaches expose username-password pairs, attackers test these credentials against other services. If a maintainer reuses passwords—a depressingly common practice—their npm, PyPI, or RubyGems account may be accessible using credentials from an unrelated breach.

Credential stuffing is automated and operates at scale. Attackers obtain breach databases containing billions of credentials and systematically test them against valuable targets. A maintainer who used the same password for a gaming forum and their npm account becomes vulnerable when the gaming forum is breached.

**Phishing:**

Targeted phishing campaigns impersonate registries, platforms, or collaborators. A maintainer might receive an email appearing to be from npm security, warning of suspicious activity and requesting login to verify their account. The link leads to a convincing replica of the npm login page that captures credentials.

Sophisticated phishing campaigns use:

- Lookalike domains (npm-js.com instead of npmjs.com)
- Valid TLS certificates (easy to obtain for any domain)
- Personalized content referencing the maintainer's actual packages
- Urgency to prevent careful examination

**Session Hijacking:**

Authentication tokens stored in configuration files, environment variables, or browser cookies can be stolen through malware or by compromising systems where they're stored. The Codecov attack (Section 7.4) specifically targeted CI/CD environment variables, which often contain registry authentication tokens.

**SIM Swapping:**

When maintainers use SMS-based two-factor authentication, attackers may target the phone number itself. SIM swapping involves convincing a mobile carrier to transfer a victim's phone number to an attacker-controlled SIM card. The attacker then receives SMS codes intended for the victim.

SIM swapping requires social engineering carrier support or exploiting carrier system vulnerabilities. High-profile incidents have targeted cryptocurrency holders for millions of dollars. Package maintainers are lower-profile targets, but the technique remains viable.

**Malware on Developer Machines:**

Infostealers—malware designed to exfiltrate credentials, cookies, and authentication tokens—specifically target developers. Once installed, they harvest:

- Browser stored passwords and cookies
- SSH keys and git credentials
- Package manager authentication tokens
- Environment variables containing secrets

The 3CX attack (Section 7.3) began with such malware, installed through a compromised financial trading application.

## Case Studies

Several high-profile incidents illustrate how account compromise enables supply chain attacks:

**eslint-scope (July 2018):**

On July 12, 2018, attackers compromised the npm account of an ESLint maintainer through credential reuse. The attacker published a malicious version of `eslint-scope`, a package with millions of weekly downloads as a dependency of the ESLint JavaScript linter.

The malicious code in version 3.7.2 stole npm tokens from developer machines. The `postinstall` script read the npm configuration file and transmitted authentication tokens to an attacker-controlled server.

The attack was detected within hours because the malicious version broke builds—an unusual outcome that attracted attention. npm invalidated all tokens that might have been exposed and required affected users to re-authenticate.

[The ESLint postmortem][eslint-postmortem] determined that the maintainer had reused their npm password on several other sites and did not have two-factor authentication enabled, underscoring the importance of unique passwords and MFA.

**rest-client gem (August 2019):**

In August 2019, attackers compromised the RubyGems account of a rest-client gem maintainer. The `rest-client` gem was widely used for HTTP requests in Ruby applications, with millions of downloads.

The attacker published versions 1.6.10 through 1.6.13 containing malicious code that:

- Collected system information
- Exfiltrated environment variables
- Sent data to Pastebin URLs controlled by the attacker
- In some versions, included cryptocurrency mining capabilities

The compromise was discovered when developers noticed unexpected Pastebin URLs in the gem source. RubyGems removed the malicious versions and the account was recovered.

**ua-parser-js (October 2021):**

The ua-parser-js incident, detailed in Section 6.4, demonstrated the speed and impact of account compromise. On October 22, 2021, attackers gained access to the maintainer's npm account and published three malicious versions within minutes.

The malicious packages included:

- A cryptocurrency miner for Linux systems
- A credential-stealing trojan for Windows systems

The attack affected approximately 7 million weekly downloads. The malicious versions remained available for about 9 hours before detection and removal.

The maintainer's npm account had been protected with a password but not two-factor authentication. The specific compromise vector was not publicly disclosed, but credential stuffing or phishing were likely candidates.

**Megalodon (May 2026):**

The Megalodon campaign showed what account compromise looks like at industrial scale. On May 18, 2026, an attacker used GitHub credentials harvested by infostealer malware on developer machines to push [5,718 malicious commits against 5,561 repositories in roughly six hours][megalodon-stepsecurity]. Forged author identities like `build-bot`, `auto-ci`, and `pipeline-bot` disguised the commits as routine automation, while injected GitHub Actions workflows exfiltrated CI secrets, cloud credentials, SSH keys, and OIDC tokens. A targeted variant replaced existing workflows with dormant `workflow_dispatch` backdoors the attacker could trigger on demand—meaning cleanup required auditing workflow files, not just reverting malicious commits.

Megalodon illustrates two shifts in the account-compromise landscape. First, credentials increasingly arrive in bulk from infostealer infections rather than one-at-a-time phishing, turning a single malware campaign into thousands of simultaneous repository compromises. Second, stolen session tokens and personal access tokens often sidestep two-factor authentication entirely, because the authentication already happened on the victim's machine.

## Two-Factor Authentication: Progress and Gaps

The eslint-scope, rest-client, and ua-parser-js attacks share a common factor: none of the compromised accounts had robust two-factor authentication enabled. Registries have responded by encouraging or mandating stronger authentication.

**npm:**

npm [began rolling out mandatory 2FA for high-impact packages][npm-2fa] in February 2022, starting with maintainers of the top 100 packages by dependents. npm defines high-impact packages as those with more than 1 million weekly downloads or 500+ dependents.

npm's phased rollout has expanded mandatory 2FA from top packages toward high-impact maintainers. However, the long tail of smaller packages remains less protected.

**PyPI:**

PyPI began mandating 2FA for its most-downloaded projects in 2022, covering the top 1% of packages by download count. In May 2023, PyPI [announced that every account maintaining any project or organization][pypi-2fa] would be required to enable 2FA by the end of 2023.

PyPI has actively distributed hardware security keys to maintainers of critical packages, providing phishing-resistant 2FA at no cost to maintainers. [The Python Software Foundation, with support from Google's Open Source Security Team, distributed over 4,000 Titan Security Keys][pypi-titan-keys] to maintainers of critical packages.

**RubyGems:**

[RubyGems implemented mandatory 2FA][rubygems-2fa] for gem owners with more than 180 million total downloads in August 2022, covering the top 100 most-downloaded gems, with expanded coverage over time.

**Adoption Gaps:**

Despite progress, gaps remain:

- Many maintainers use TOTP (time-based one-time password) apps rather than hardware keys, leaving them vulnerable to sophisticated phishing
- SMS-based 2FA, while better than nothing, remains vulnerable to SIM swapping
- The long tail of packages outside mandatory 2FA thresholds remains weakly protected
- Organizational accounts and shared credentials complicate enforcement

Even where registries mandate 2FA, enforcement mechanisms and recovery exceptions vary across ecosystems.

## MFA Bypass Techniques

Two-factor authentication significantly raises the bar for attackers, but determined adversaries have developed bypass techniques:

**Real-Time Phishing Proxies:**

Sophisticated phishing attacks don't just capture credentials—they proxy the entire authentication session in real-time. The victim enters credentials and MFA codes into a phishing site, which immediately relays them to the real site, completing authentication and capturing the resulting session.

Tools like Evilginx2 and Modlishka automate this attack. The victim experiences a normal login (with MFA), but the attacker obtains authenticated session tokens.

**MFA Fatigue:**

When organizations use push-notification MFA (like Duo or Microsoft Authenticator prompts), attackers can repeatedly trigger authentication requests. Overwhelmed or confused users may eventually approve a request to stop the notifications.

This technique was used in the 2022 Uber breach, where an attacker sent numerous MFA prompts until an employee approved one.

**Targeting Recovery Flows:**

MFA protects normal login, but account recovery flows often bypass MFA. If attackers can convince support teams to reset MFA or trigger recovery mechanisms, they circumvent the protection entirely.

## Account Recovery as Attack Vector

Account recovery processes are designed for convenience—helping legitimate users who lose access. This creates tension with security. Attackers exploit recovery mechanisms through:

**Social Engineering Support:**

Attackers contact registry support teams, impersonate maintainers, and request account recovery. Convincing scenarios might include:

- "I lost my phone and my backup codes"
- "My email was hacked and I need to update my contact information"
- "I'm being locked out and need urgent help to publish a security fix"

Support teams face pressure to help users and may not have robust identity verification procedures. A convincing story with some publicly-available information about the maintainer may be sufficient.

**Compromising Recovery Email:**

Many recovery flows send links to a registered email address. If attackers compromise that email account (often through credential stuffing against personal email), they can trigger and complete account recovery.

**Exploiting Recovery Token Vulnerabilities:**

Recovery tokens and links have occasionally been vulnerable to prediction, reuse, or insufficient expiration. Vulnerabilities in recovery flows can allow account takeover without compromising the primary credentials.

## Domain Resurrection Attacks

A particularly insidious form of account takeover exploits expired domain names. When a maintainer registers an account using an email address on a custom domain (not Gmail, Outlook, or other major providers), that account's security becomes tied to the domain's continued registration. If the domain expires, anyone can purchase it—and with it, gain control of any email addresses under that domain.

**The attack chain is straightforward:**

1. Attacker identifies a package maintainer whose email domain has expired
2. Attacker purchases the expired domain (often for under $10)
3. Attacker configures email service to receive mail for the domain
4. Attacker initiates a password reset on the package registry
5. Attacker receives the password reset email and takes over the account
6. Attacker publishes malicious package versions

The entire attack, from domain purchase to malicious publication, can complete in under an hour. No vulnerability exploitation, no phishing, no social engineering of support teams—just the predictable consequence of a forgotten domain renewal.

**Case Study: Python ctx Package (May 2022)**

The [ctx package compromise][ctx-attack] demonstrated this attack in practice. The ctx library, used for accessing Python dictionaries with dot notation, had been stable for eight years—no updates needed, no attention required. When the maintainer's personal domain (figlief.com) expired, an attacker registered it for approximately $5.

WHOIS records show the domain was registered on May 14, 2022 at 18:40 UTC. The attacker initiated a password reset just 12 minutes later, rapidly gaining control of the PyPI account. Within 40 minutes of domain registration, malicious package versions were being uploaded.

The [malicious code exfiltrated environment variables][ctx-analysis]—including AWS credentials—to an attacker-controlled Heroku endpoint. The compromised versions remained on PyPI for 10 days before detection, during which approximately 27,000 copies were downloaded.

The attack's simplicity was striking. No 2FA was enabled on the account. The maintainer had long since stopped actively using the email address but never updated their PyPI credentials or secured the account with additional factors.

**Case Study: npm foreach Package (May 2022)**

Around the same time, security researcher Lance Vick [demonstrated the vulnerability's scope in the npm ecosystem][foreach-disclosure]. He noticed that the maintainer of the `foreach` package—with nearly 6 million weekly downloads and 36,000+ dependent projects—had let their personal domain expire.

Vick purchased the expired domain to make a point about npm's security posture. While he responsibly disclosed the issue and returned the domain rather than exploiting it, the incident revealed the scale of the problem: research identified [2,818 maintainer email addresses associated with expired domains][jfrog-research], potentially enabling hijacking of 8,494 npm packages.

**Scale of the Problem**

The [JFrog Security Research team's analysis][jfrog-research] found:

- 3,210 npm packages contain maintainers with purchasable expired domains (~0.16% of all packages)
- 900 npm maintainers have email addresses on available-for-purchase domains (~0.17% of maintainers)
- The highest-risk vulnerable package had approximately 31 million total downloads
- Single-maintainer packages (2,817 of those affected) are particularly vulnerable since there's no second account to detect or prevent the takeover

Similar exposure exists across PyPI, RubyGems, and other registries.

**Registry Countermeasures**

Registries have begun implementing defenses against domain resurrection attacks:

**PyPI's Domain Monitoring:**

In 2025, [PyPI deployed automated domain monitoring][pypi-domain-monitoring] to detect expired domains associated with user accounts. The system uses the Domainr Status API to periodically check domain status and unverify email addresses when domains enter the redemption period—the window before a domain becomes publicly available for purchase.

Since implementation, PyPI has unverified over 1,800 email addresses with expired domains. Affected accounts aren't deleted, but password resets are blocked until users verify a new email address or complete account recovery.

**npm's Periodic Checks:**

[npm periodically checks][npm-security] whether account email addresses have expired domains or invalid MX records. When detected, password reset functionality is disabled, requiring users to undergo account recovery through identity verification before regaining access.

**Mandatory 2FA:**

Both registries' mandatory 2FA requirements for high-impact packages provide a second layer of defense. Even if an attacker gains email access, they cannot complete login without the second factor. However, 2FA was optional at the time of the ctx and foreach incidents.

**The Long Tail Problem**

Current protections focus on high-impact packages and proactive domain monitoring. But millions of packages fall outside mandatory 2FA thresholds. A package with 50,000 weekly downloads may not trigger protections but still provides significant reach for attackers.

Tools like JFrog's [npm_domain_check][npm-domain-check] help organizations audit their dependencies for maintainers with expired domains. Phylum's analytics platform similarly [flags packages with expired author domains][phylum-domains] as a supply chain risk indicator.

**Recommendations for Maintainers:**

1. **Use permanent email providers for registry accounts.** Gmail, Outlook, and other major providers don't expire. Custom domains require ongoing registration and payment.

2. **Add a secondary verified email.** PyPI and other registries allow multiple email addresses. Add a backup on a permanent provider so account recovery remains possible if your primary domain lapses.

3. **Enable 2FA regardless of package popularity.** Even if not required, 2FA blocks domain resurrection attacks by adding a factor attackers cannot obtain through email access.

4. **Audit older packages you maintain.** Packages published years ago may still use outdated email addresses from domains you no longer control.

[ctx-attack]: https://python-security.readthedocs.io/pypi-vuln/index-2022-05-24-ctx-domain-takeover.html
[ctx-analysis]: https://thehackernews.com/2022/05/pypi-package-ctx-and-php-library-phpass.html
[foreach-disclosure]: https://www.theregister.com/security/2022/05/10/expert-grabs-expired-domain-for-npm-package-to-make-a-point/1341031
[jfrog-research]: https://jfrog.com/blog/npm-package-hijacking-through-domain-takeover-how-bad-is-this-new-attack/
[pypi-domain-monitoring]: https://blog.pypi.org/posts/2025-08-18-preventing-domain-resurrections/
[npm-security]: https://docs.npmjs.com/threats-and-mitigations/
[npm-domain-check]: https://github.com/jfrog/npm_domain_check
[phylum-domains]: https://docs.phylum.io/analytics/expired_author_domains

## Platform and Registry Responses

Beyond mandatory 2FA, registries have implemented additional protections:

**Hardware Security Key Programs:**

Both npm (through GitHub) and PyPI have distributed free hardware security keys to maintainers of critical packages. Hardware keys are phishing-resistant—they verify the domain they're authenticating to, preventing real-time phishing attacks.

**Trusted Publishing (PyPI):**

PyPI's Trusted Publishing feature allows packages to be published from CI/CD systems (like GitHub Actions) without storing long-lived tokens. Publication is authorized through OIDC federation (using identity tokens from the CI/CD platform), eliminating persistent credentials that could be stolen.

**npm Provenance:**

npm's provenance feature links published packages to specific source repositories and build processes. While not preventing account compromise, it makes it harder for attackers to publish code not present in the expected repository.

**Audit Logging:**

Improved audit logging helps detect suspicious activity:

- Logins from unusual locations
- Publications at unusual times
- Multiple packages published in rapid succession

**IP-Based Restrictions:**

Some registries allow restricting publication to specific IP ranges, limiting the impact of credential compromise by blocking publication from unexpected locations.

## Recommendations

**For maintainers:**

1. **Enable phishing-resistant MFA.** Use hardware security keys (FIDO2/WebAuthn) where supported. TOTP apps are acceptable; SMS should be avoided.

2. **Use unique passwords.** Never reuse passwords across services. Use a password manager to generate and store unique, strong passwords.

3. **Secure your email.** Your email account is a gateway to all account recovery. Apply the same or stronger security to email as to registry accounts.

4. **Use Trusted Publishing where available.** Eliminate stored tokens by publishing from CI/CD systems using OIDC federation.

5. **Monitor for unauthorized activity.** Review audit logs for unexpected logins or publications. Configure alerts for new sessions.

6. **Secure your development machine.** Treat your development environment as a high-value target. Be cautious about what software you install.

**For organizations depending on open source:**

1. **Monitor critical package maintainer changes.** Unexpected maintainer changes or publication patterns may indicate compromise.

2. **Prefer packages with strong maintainer security.** Packages whose maintainers use 2FA and have provenance attestations provide better assurance.

3. **Implement multiple layers of defense.** Don't rely solely on registry security. Use lockfiles, vulnerability scanning, and behavioral analysis.

**For registries and platforms:**

1. **Mandate robust MFA for high-impact accounts.** Cover not just top packages but all packages with significant downstream impact.

2. **Provide free hardware security keys.** Cost should not be a barrier to phishing-resistant authentication for critical maintainers.

3. **Implement Trusted Publishing.** Enable token-less publication from verified CI/CD systems.

4. **Secure recovery flows.** Require robust identity verification for account recovery. Implement waiting periods for sensitive changes.

5. **Detect and alert on suspicious activity.** Anomaly detection can identify compromises before malicious packages reach users.

Maintainer account security is one of the most cost-effective supply chain security investments. The asymmetry between attack cost (obtaining one password) and impact (millions of affected installations) makes strong authentication essential. As registries continue expanding mandatory 2FA and providing phishing-resistant authentication, the ecosystem becomes meaningfully more secure—but maintaining this progress requires continued vigilance from maintainers, platforms, and organizations alike.

[eslint-postmortem]: https://eslint.org/blog/2018/07/postmortem-for-malicious-package-publishes/
[megalodon-stepsecurity]: https://www.stepsecurity.io/blog/megalodon-mass-github-actions-secret-exfiltration-across-5-500-public-repositories
[npm-2fa]: https://github.blog/security/supply-chain-security/top-100-npm-package-maintainers-require-2fa-additional-security/
[pypi-2fa]: https://blog.pypi.org/posts/2023-05-25-securing-pypi-with-2fa/
[pypi-titan-keys]: https://blog.google/technology/safety-security/making-open-source-software-safer-and-more-secure/
[rubygems-2fa]: https://blog.rubygems.org/2022/08/15/requiring-mfa-on-popular-gems.html
