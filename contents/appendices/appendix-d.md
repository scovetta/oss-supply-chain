## Appendix D: Security Checklist for Open Source Projects

This appendix provides a comprehensive security checklist for open source project maintainers. Use these checklists to assess your project's security posture, identify gaps, and prioritize improvements. Each section includes implementation guidance and links to relevant resources.

The checklist aligns with the OpenSSF Best Practices Badge[^openssf-badge] criteria and incorporates recommendations from the OpenSSF Scorecard project.

For step-by-step platform walkthroughs, see **Appendix J: Platform Security Configuration Guides**, which provides complete implementation guides for GitHub and GitLab. Each checklist item below includes a reference identifier (e.g., [D.A.1]) that maps to the corresponding walkthrough in Appendix J.

---

### Repository Security Settings

Configure your source code repository to prevent unauthorized changes and enforce security controls. These settings apply to GitHub; similar controls exist for GitLab, Bitbucket, and other platforms.

#### Branch Protection

- [ ] **Enable branch protection on default branch**: Prevent direct pushes to main branch [D.A.1]
- [ ] **Require pull request reviews before merging**: At least one approval from a maintainer [D.A.2]
- [ ] **Require review from code owners**: Ensure domain experts review relevant changes [D.A.3]
- [ ] **Dismiss stale pull request approvals**: Re-review required after new commits [D.A.4]
- [ ] **Require status checks to pass before merging**: CI must complete successfully [D.A.5]
- [ ] **Require branches to be up to date before merging**: Prevent merge conflicts introducing issues [D.A.6]
- [ ] **Require signed commits**: Verify commit author identity via GPG or SSH signatures [D.A.7]
- [ ] **Require linear history**: Simplify auditing by preventing merge commits [D.A.8]
- [ ] **Restrict who can push to matching branches**: Limit to trusted maintainers [D.A.9]
- [ ] **Do not allow bypassing branch protection**, Apply rules to administrators too [D.A.10]

#### Access Control

- [ ] **Enable two-factor authentication requirement**: Require 2FA for all organization members [D.A.11]
- [ ] **Review and minimize admin access**: Limit administrative privileges to essential personnel [D.A.12]
- [ ] **Audit collaborator permissions quarterly**: Remove inactive or unnecessary access [D.A.13]
- [ ] **Use teams for permission management**: Avoid individual permission grants [D.A.14]
- [ ] **Enable SSO/SAML if available**: Centralize identity management for organizations [D.A.15]
- [ ] **Review and rotate deploy keys annually**: Remove unused keys promptly [D.A.16]
- [ ] **Use fine-grained personal access tokens**: Avoid classic tokens with broad permissions [D.A.17]

#### Repository Settings

- [ ] **Enable Dependabot alerts**: Receive notifications for vulnerable dependencies [D.A.18]
- [ ] **Enable Dependabot security updates**: Automated pull requests for security fixes [D.A.19]
- [ ] **Enable secret scanning**: Detect accidentally committed credentials [D.A.20]
- [ ] **Enable secret scanning push protection**: Block commits containing secrets [D.A.21]
- [ ] **Enable code scanning (CodeQL or similar)**: Automated vulnerability detection [D.A.22]
- [ ] **Configure security advisories**: Enable private vulnerability reporting [D.A.23]
- [ ] **Set repository visibility appropriately**: Public for open source; private for sensitive components [D.A.24]
- [ ] **Disable unused features**: Turn off wikis, projects, discussions if not used [D.A.25]

**Implementation Guide**: GitHub Repository Security Settings[^github-repo-security]

**Platform Walkthroughs**: See Appendix J for step-by-step GitHub and GitLab configuration guides.

---

### Documentation Requirements

Security documentation communicates your project's security posture and provides guidance for users and researchers. Complete documentation builds trust and streamlines security processes.

#### Required Security Documentation

- [ ] **SECURITY.md in repository root**: Clear instructions for reporting vulnerabilities [D.B.1]
  - [ ] Preferred contact method (email, security advisory, bug bounty platform)
  - [ ] Expected response timeline
  - [ ] Disclosure policy and timeline
  - [ ] PGP key for encrypted communications (if applicable)
  - [ ] Scope of security policy (which versions are supported)

- [ ] **Security policy published and discoverable**: Linked from README and repository settings [D.B.2]

- [ ] **Supported versions documented**: Clear statement of which versions receive security updates [D.B.3]

#### General Documentation

- [ ] **README.md with project overview**: Clear description of what the project does [D.B.4]
- [ ] **Installation instructions**: Secure installation methods documented [D.B.5]
- [ ] **LICENSE file present**: Clear licensing terms using SPDX identifier [D.B.6]
- [ ] **CONTRIBUTING.md with guidelines**: Include security considerations for contributors [D.B.7]
- [ ] **CODE_OF_CONDUCT.md**: Community standards and enforcement procedures [D.B.8]
- [ ] **CHANGELOG.md or releases page**: Document changes including security fixes [D.B.9]

#### Security-Specific Documentation

- [ ] **Threat model documented**: Key assets, threats, and mitigations described [D.B.10]
- [ ] **Security architecture overview**: How security controls are implemented [D.B.11]
- [ ] **Authentication/authorization model**, If applicable, document access control design [D.B.12]
- [ ] **Cryptography usage documented**: Algorithms, key management, and rationale [D.B.13]
- [ ] **Known limitations documented**: Security boundaries and out-of-scope threats [D.B.14]
- [ ] **Hardening guide**: Secure configuration recommendations for users [D.B.15]
- [ ] **Dependency policy**: How dependencies are selected and evaluated [D.B.16]

**Template**: GitHub SECURITY.md Template[^github-security-template]

**Tool**: OpenSSF Disclosure Check[^openssf-disclosure-check] — Verify vulnerability disclosure mechanisms

**Platform Walkthroughs**: See Appendix J for step-by-step GitHub and GitLab configuration guides.

---

### Build and Release Security

Secure build processes prevent tampering and ensure users receive authentic artifacts. These controls form the foundation of supply chain integrity.

#### Build Environment Security

- [ ] **Use CI/CD for all builds**: No builds from local developer machines for releases [D.C.1]
- [ ] **Pin CI/CD action versions by hash**: Prevent malicious action updates [D.C.2]

  ```yaml
  # Good: Pinned by SHA
  uses: actions/checkout@8ade135a41bc03ea155e62e844d188df1ea18608
  # Risky: Floating tag
  uses: actions/checkout@v4
  ```

- [ ] **Minimize build dependencies**: Only include necessary tools and packages [D.C.3]
- [ ] **Use ephemeral build environments**: Fresh environment for each build [D.C.4]
- [ ] **Isolate build environments**: Prevent network access during build where possible [D.C.5]
- [ ] **Pin all dependency versions**: Use lockfiles for reproducible builds [D.C.6]
- [ ] **Verify dependency integrity**: Check hashes during dependency resolution [D.C.7]
- [ ] **Scan dependencies before build**: Fail builds with known vulnerable dependencies [D.C.8]

#### Build Process

- [ ] **Document build process completely**: Anyone should be able to reproduce the build [D.C.9]
- [ ] **Enable compiler security flags**: Stack canaries, ASLR, PIE, FORTIFY_SOURCE [D.C.10]
- [ ] **Run SAST during build**: Static analysis integrated into CI pipeline [D.C.11]
- [ ] **Run tests including security tests**: Unit tests, integration tests, security-specific tests [D.C.12]
- [ ] **Generate SBOM during build**: Automated bill of materials creation [D.C.13]
- [ ] **Build from verified source only**: Clone from official repository, verify tags [D.C.14]

#### Release Signing

- [ ] **Sign all release artifacts**: Use GPG, Sigstore, or platform-native signing [D.C.15]
- [ ] **Publish signing keys/certificates**: Make verification possible for users [D.C.16]
- [ ] **Sign git tags for releases**: `git tag -s` for signed tags [D.C.17]
- [ ] **Document verification process**: Instructions for users to verify signatures [D.C.18]
- [ ] **Use Sigstore for keyless signing**: Reduce key management burden [D.C.19]
- [ ] **Generate provenance attestations**: SLSA provenance for build transparency [D.C.20]
- [ ] **Publish attestations with releases**: Make provenance verifiable [D.C.21]

#### Release Process

- [ ] **Use protected release branches/tags**: Prevent unauthorized release modifications [D.C.22]
- [ ] **Require multiple approvals for releases**: No single person can release alone [D.C.23]
- [ ] **Automate release process**: Reduce human error and intervention points [D.C.24]
- [ ] **Publish to official registries only**: npm, PyPI, Maven Central, etc. [D.C.25]
- [ ] **Use trusted publishing where available**: OIDC-based publishing without long-lived secrets [D.C.26]
- [ ] **Include SBOM with release**: Ship bill of materials with artifacts [D.C.27]
- [ ] **Announce releases through official channels**: Prevent impersonation [D.C.28]

**Implementation Guide**: SLSA Requirements[^slsa-requirements]

**Tool**: Sigstore[^sigstore] — Free signing infrastructure for open source

**Platform Walkthroughs**: See Appendix J for step-by-step GitHub and GitLab configuration guides.

---

### Vulnerability Management

Effective vulnerability management protects your users and maintains trust in your project. Establish clear processes before vulnerabilities are discovered.

#### Vulnerability Reporting

- [ ] **Private reporting channel established**: Security advisories, email, or bug bounty platform [D.D.1]
- [ ] **Designated security contact(s)**: Named individuals responsible for security issues [D.D.2]
- [ ] **Acknowledgment timeline defined**: Respond within 48-72 hours [D.D.3]
- [ ] **Assessment timeline defined**: Initial severity assessment within 1 week [D.D.4]
- [ ] **Fix timeline expectations set** — Critical: days, High: weeks, Medium: months [D.D.5]
- [ ] **Disclosure timeline defined**, Typically 90 days, coordinated with reporter [D.D.6]
- [ ] **Safe harbor statement**: Protect good-faith security researchers [D.D.7]

#### Vulnerability Response Process

- [ ] **Triage process documented**: How reports are evaluated and prioritized [D.D.8]
- [ ] **Severity rating methodology**: CVSS or equivalent for consistent assessment [D.D.9]
- [ ] **Fix development process**: Private branch/fork for developing fixes [D.D.10]
- [ ] **Testing requirements for fixes**: Verify fix addresses vulnerability without regression [D.D.11]
- [ ] **Backporting policy defined**, Which older versions receive security fixes [D.D.12]
- [ ] **Communication plan**: How users are notified of vulnerabilities and fixes [D.D.13]

#### Vulnerability Disclosure

- [ ] **Request CVE for confirmed vulnerabilities**: Use CNA or MITRE process [D.D.14]
- [ ] **Publish security advisory**: GitHub Security Advisories or equivalent [D.D.15]
- [ ] **Credit reporters appropriately**: Acknowledge researchers per their preference [D.D.16]
- [ ] **Provide clear upgrade guidance**: Tell users exactly what to do [D.D.17]
- [ ] **Document workarounds if available**: Temporary mitigations before patching [D.D.18]
- [ ] **Coordinate with downstream projects**: Notify known significant consumers [D.D.19]

#### Ongoing Vulnerability Management

- [ ] **Monitor for dependency vulnerabilities**: Dependabot, Snyk, or similar [D.D.20]
- [ ] **Regular dependency updates**: Keep dependencies current, not just security fixes [D.D.21]
- [ ] **Periodic security assessments**: Self-assessment or external audit annually [D.D.22]
- [ ] **Track security debt**: Known issues and their remediation timeline [D.D.23]
- [ ] **Review past vulnerabilities**: Learn from patterns to prevent recurrence [D.D.24]

**Implementation Guide**: CERT Guide to Coordinated Vulnerability Disclosure[^cert-cvd-guide]

**Template**: GitHub Security Advisory Process[^github-security-advisory]

**Platform Walkthroughs**: See Appendix J for step-by-step GitHub and GitLab configuration guides.

---

### Community and Governance

Clear governance and community practices ensure security decisions are made transparently and that the project can sustain security efforts long-term.

#### Project Governance

- [ ] **Governance model documented**: Decision-making process is clear [D.E.1]
- [ ] **Maintainer roles defined**, Who can merge, release, handle security [D.E.2]
- [ ] **Succession plan exists**: Project continuity if maintainers leave [D.E.3]
- [ ] **Multiple active maintainers**: No single point of failure (bus factor > 1) [D.E.4]
- [ ] **Foundation or organizational backing**: For critical projects, formal support structure [D.E.5]
- [ ] **Funding model transparent**: How the project sustains development [D.E.6]

#### Contributor Security

- [ ] **Contributor verification process**: Validate significant new contributors [D.E.7]
- [ ] **CLA or DCO requirement**: Legal clarity for contributions [D.E.8]
- [ ] **New contributor review requirements**: Enhanced scrutiny for first-time contributors [D.E.9]
- [ ] **Commit access progression**: Clear path from contributor to committer [D.E.10]
- [ ] **Periodic access review**: Remove inactive maintainer access [D.E.11]

#### Security Culture

- [ ] **Security discussed in project communications**: Regular presence in meetings/updates [D.E.12]
- [ ] **Security champion identified**: Point person for security matters [D.E.13]
- [ ] **Security training for maintainers**: At least basic secure development training [D.E.14]
- [ ] **Incident response plan**: Steps to take if project is compromised [D.E.15]
- [ ] **Post-incident review process**: Learn from security incidents [D.E.16]

#### Transparency

- [ ] **Public issue tracker**: Visible development activity (excluding security issues) [D.E.17]
- [ ] **Public roadmap**: Development direction is visible [D.E.18]
- [ ] **Meeting notes published**: For projects with governance meetings [D.E.19]
- [ ] **Security improvements communicated**: Share security enhancements publicly [D.E.20]
- [ ] **Annual security report**: Summary of security activities and status [D.E.21]

**Implementation Guide**: OpenSSF Best Practices Badge[^openssf-badge-criteria]

**Platform Walkthroughs**: See Appendix J for step-by-step GitHub and GitLab configuration guides.

---

### Self-Assessment Template

Use this scoring template to assess your project's security maturity. Score each category and identify priority improvements.

#### Scoring Guide

| Score | Meaning |
|-------|---------|
| 0 | Not implemented |
| 1 | Partially implemented or planned |
| 2 | Fully implemented |
| N/A | Not applicable to this project |

#### Assessment Worksheet

**Repository Security** (Max: 20 points)

| Control | Score (0-2) | Notes |
|---------|-------------|-------|
| Branch protection enabled | | |
| Required reviews configured | | |
| 2FA required for org members | | |
| Signed commits required/encouraged | | |
| Dependabot/security scanning enabled | | |
| Secret scanning enabled | | |
| Code scanning enabled | | |
| Access permissions reviewed | | |
| Security advisories enabled | | |
| Deploy keys/tokens minimized | | |

**Documentation** (Max: 14 points)

| Control | Score (0-2) | Notes |
|---------|-------------|-------|
| SECURITY.md present and complete | | |
| Supported versions documented | | |
| README with security considerations | | |
| CONTRIBUTING.md with security guidance | | |
| Threat model documented | | |
| Hardening guide available | | |
| Dependency policy documented | | |

**Build and Release** (Max: 16 points)

| Control | Score (0-2) | Notes |
|---------|-------------|-------|
| CI/CD used for releases | | |
| Dependencies pinned with lockfiles | | |
| Build process documented | | |
| Release artifacts signed | | |
| SBOM generated | | |
| Provenance attestations created | | |
| Trusted publishing enabled | | |
| Multiple approvals for release | | |

**Vulnerability Management** (Max: 14 points)

| Control | Score (0-2) | Notes |
|---------|-------------|-------|
| Private reporting channel exists | | |
| Response timeline defined | | |
| Severity rating process defined | | |
| CVE process established | | |
| Security advisory process defined | | |
| Dependency monitoring active | | |
| Backporting policy defined | | |

**Governance** (Max: 12 points)

| Control | Score (0-2) | Notes |
|---------|-------------|-------|
| Governance model documented | | |
| Multiple active maintainers | | |
| Contributor verification process | | |
| Security champion identified | | |
| Incident response plan exists | | |
| Access review performed periodically | | |

#### Maturity Levels

| Total Score | Maturity Level | Description |
|-------------|----------------|-------------|
| 0-19 | **Initial** | Basic security awareness; significant gaps |
| 20-39 | **Developing** | Some controls in place; key areas need attention |
| 40-59 | **Defined** | Most controls implemented; refinement needed |
| 60-69 | **Managed** | Strong security posture; continuous improvement |
| 70-76 | **Optimizing** | Excellent security practices; industry leader |

#### Priority Matrix

After scoring, prioritize improvements using this matrix:

| Impact | Low Effort | High Effort |
|--------|-----------|-------------|
| **High** | Do immediately | Plan for next quarter |
| **Low** | Quick wins when time permits | Evaluate necessity |

**High-impact, low-effort items typically include:**

- Enabling Dependabot and secret scanning
- Creating SECURITY.md
- Enabling branch protection
- Setting up 2FA requirements

---

### Additional Resources

- **OpenSSF Best Practices Badge**: https://www.bestpractices.dev/[^openssf-badge-resource]
- **OpenSSF Scorecard**: https://securityscorecards.dev/[^openssf-scorecard]
- **CISA Secure Software Development Attestation**: https://www.cisa.gov/secure-software-attestation-form[^cisa-attestation]
- **GitHub Security Hardening Guide**: https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions[^github-hardening]
- **AllStar (Automated Security Policy Enforcement)**: https://github.com/ossf/allstar[^allstar]
- **Scorecard Action**: https://github.com/ossf/scorecard-action[^scorecard-action]

[^openssf-badge]: Open Source Security Foundation, "Best Practices Badge," https://www.bestpractices.dev/

[^github-repo-security]: GitHub, "Managing security and analysis settings for your repository," https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-security-and-analysis-settings-for-your-repository

[^github-security-template]: GitHub, "Adding a security policy to your repository," https://docs.github.com/en/code-security/getting-started/adding-a-security-policy-to-your-repository

[^openssf-disclosure-check]: Open Source Security Foundation, "Disclosure Check," https://github.com/ossf/disclosure-check

[^slsa-requirements]: SLSA, "Requirements," https://slsa.dev/spec/v1.0/requirements

[^sigstore]: Sigstore, "Sigstore," https://www.sigstore.dev/

[^cert-cvd-guide]: CERT, "Guide to Coordinated Vulnerability Disclosure," https://vuls.cert.org/confluence/display/CVD

[^github-security-advisory]: GitHub, "About repository security advisories," https://docs.github.com/en/code-security/security-advisories/repository-security-advisories/about-repository-security-advisories

[^openssf-badge-criteria]: Open Source Security Foundation, "Best Practices Badge Criteria," https://www.bestpractices.dev/en/criteria

[^openssf-badge-resource]: Open Source Security Foundation, "Best Practices Badge," https://www.bestpractices.dev/

[^openssf-scorecard]: Open Source Security Foundation, "Scorecard," https://securityscorecards.dev/

[^cisa-attestation]: CISA, "Secure Software Development Attestation Form," https://www.cisa.gov/secure-software-attestation-form

[^github-hardening]: GitHub, "Security hardening for GitHub Actions," https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions

[^allstar]: Open Source Security Foundation, "AllStar," https://github.com/ossf/allstar

[^scorecard-action]: Open Source Security Foundation, "Scorecard Action," https://github.com/ossf/scorecard-action
