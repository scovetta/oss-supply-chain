## Appendix J: Platform Security Configuration Guides

This appendix provides step-by-step instructions for implementing the security controls described in Appendix D: Security Checklist for Open Source Projects. While Appendix D tells you *what* to configure, this appendix shows you *how* to configure it on the two most widely used open source hosting platforms: GitHub and GitLab.

Each platform guide is self-contained. If your project is hosted on GitHub, you can read Part 1 without referencing Part 2, and vice versa. Every walkthrough references the corresponding Appendix D checklist item using bracketed identifiers (e.g., [D.A.1]) so you can cross-reference between the checklist and implementation instructions.

### How to Use This Guide

1. **Start with Appendix D** to assess which controls your project needs
2. **Navigate to your platform's section** (Part 1 for GitHub, Part 2 for GitLab)
3. **Follow the step-by-step instructions** for each control you want to implement
4. **Return to Appendix D** to mark completed items and track progress using the self-assessment template

Some Appendix D items describe organizational processes rather than platform settings (e.g., succession planning, threat modeling). For these items, we provide recommended approaches for documenting and managing these processes using your platform's features, marked with a **Process Guidance** label.

> **Note**: Platform interfaces evolve frequently. The settings and capabilities described here remain accurate even if menu layouts or visual styling change. When in doubt, consult the platform's current documentation linked at the end of each section.

### Quick-Reference Navigation

| Appendix D Section | GitHub (Part 1) | GitLab (Part 2) |
|---|---|---|
| Repository Security Settings [D.A] | Repository Security Settings on GitHub | Repository Security Settings on GitLab |
| Documentation Requirements [D.B] | Documentation Requirements on GitHub | Documentation Requirements on GitLab |
| Build and Release Security [D.C] | Build and Release Security on GitHub | Build and Release Security on GitLab |
| Vulnerability Management [D.D] | Vulnerability Management on GitHub | Vulnerability Management on GitLab |
| Community and Governance [D.E] | Community and Governance on GitHub | Community and Governance on GitLab |

---

## Part 1: GitHub Security Configuration Guide

GitHub is the most widely used platform for open source development, hosting over 200 million repositories. This section walks through configuring every Appendix D security control using GitHub's interface and features.

**Scope**: These instructions apply to GitHub.com (cloud). GitHub Enterprise Server (self-hosted) offers the same features but with slightly different navigation paths. Where GitHub Advanced Security (GHAS) is required for private repositories, we note the requirement explicitly.

**Permissions required**: Most settings require repository Administrator or Organization Owner access. We note specific permission requirements where they differ.

---

### Repository Security Settings on GitHub

#### Branch Protection Rules [D.A.1–10]

GitHub provides two mechanisms for protecting branches: **branch protection rules** (the established approach) and **repository rulesets** (the newer approach with more flexibility). We cover both, starting with branch protection rules.

##### Configuring Branch Protection Rules

**Navigation**: Repository > **Settings** > **Branches** (under "Code and automation") > **Add branch protection rule**

The Branch protection rules page lists all existing rules and provides an **Add branch protection rule** button. Clicking it opens a form with a branch name pattern field at the top followed by a series of checkboxes for each protection setting.

**Step 1**: In the **Branch name pattern** field, enter `main` (or your default branch name). You can use fnmatch patterns like `release/*` to protect multiple branches.

**Step 2**: Configure each protection setting:

**Enable branch protection on default branch** [D.A.1], Adding any rule targeting your default branch enables branch protection. The rule itself prevents direct pushes (force pushes and deletions are blocked automatically for protected branches).

**Require pull request reviews before merging** [D.A.2]: Check **Require a pull request before merging**. This expands a sub-section where you can set the number of **Required approving reviews** (a dropdown with values from 1 to 6). We recommend at least one approval for most projects, and two for critical security-sensitive repositories. Additional options in this sub-section control stale review dismissal, code owner reviews, and who can dismiss reviews.

**Require review from code owners** [D.A.3]: Under the pull request review section, check **Require review from Code Owners**. This requires you to have a `CODEOWNERS` file in your repository (`.github/CODEOWNERS`, `docs/CODEOWNERS`, or at the repository root). Example `CODEOWNERS` file:

```
# Default owners for everything
* @org/security-team

# Crypto-related code requires security team review
/src/crypto/ @org/security-team @crypto-lead

# CI/CD configuration requires platform team review
/.github/workflows/ @org/platform-team
```

**Dismiss stale pull request approvals** [D.A.4]: Check **Dismiss stale pull request approvals when new commits are pushed**. This ensures that if a contributor pushes new commits after receiving approval, the pull request must be re-reviewed. Without this setting, an attacker who gains commit access could add malicious code after approval.

**Require status checks to pass before merging** [D.A.5]: Check **Require status checks to pass before merging**. Search for and select the specific status checks that must pass (e.g., your CI build, test suite, linting). Only checks that have run recently on the repository will appear in the search.

**Require branches to be up to date before merging** [D.A.6]: Under status checks, check **Require branches to be up to date before merging**. This prevents merging a pull request that was approved against an older version of the base branch, which could introduce merge-related issues that were never tested.

**Require signed commits** [D.A.7]: Check **Require signed commits**. This requires all commits pushed to the branch to have a verified signature (GPG, SSH, or S/MIME). Contributors must configure commit signing before they can push to protected branches. See GitHub's documentation on signing commits[^gh-signing-commits] for setup instructions.

> **Practical consideration**: Requiring signed commits can create friction for new contributors. Consider enabling this for release branches while using a softer approach (displaying verification badges but not blocking) for the default development branch.

**Require linear history** [D.A.8]: Check **Require linear history**. This enforces squash or rebase merges, preventing merge commits. Linear history simplifies `git log` auditing and makes it easier to trace when specific changes were introduced.

**Restrict who can push to matching branches** [D.A.9]: Check **Restrict who can push to matching branches** and add the specific people, teams, or apps that should have push access. For most projects, this should be limited to the core maintainer team.

**Do not allow bypassing branch protection** [D.A.10]: Near the bottom of the branch protection form, check **Do not allow bypassing the above settings**. Without this, repository administrators can bypass all branch protection rules. Enabling this setting applies the rules universally, including to admins. This is particularly important for preventing compromised admin accounts from pushing directly.

**Step 3**: Click **Create** (or **Save changes** if editing an existing rule) at the bottom of the page.

##### Using Repository Rulesets (Alternative)

GitHub repository rulesets offer the same protections as branch protection rules with additional capabilities: organization-level rulesets that apply across multiple repositories, tag protection, and the ability to allow specific users to bypass rules without disabling protection entirely.

**Navigation**: Repository > **Settings** > **Rules** > **Rulesets** > **New ruleset** > **New branch ruleset**

Rulesets use the same underlying settings as branch protection rules. If your organization manages many repositories, rulesets at the organization level provide more consistent governance. For a single repository, either approach works.

#### Access Control [D.A.11–17]

##### Two-Factor Authentication [D.A.11]

**Navigation** (Organization): **Organization Settings** > **Authentication security** > **Two-factor authentication**

The Authentication security page displays a **Two-factor authentication** section with a single checkbox. Check **Require two-factor authentication for everyone in the [organization name] organization**. A confirmation dialog warns that members who have not enabled 2FA will be removed from the organization and must re-join after configuring 2FA.

> **Before enabling**: Communicate the requirement to all organization members in advance. Provide setup instructions and a deadline. GitHub supports authenticator apps, security keys (FIDO2/WebAuthn), and SMS (not recommended) as second factors.

For individual repositories not owned by an organization, you cannot enforce 2FA on collaborators. Encourage all collaborators to enable 2FA through their personal account settings (**Settings** > **Password and authentication** > **Two-factor authentication**).

##### Review and Minimize Admin Access [D.A.12]

**Navigation** (Organization): **Organization Settings** > **People** > filter by **Role: Owner**

Review the list of organization owners. Each owner has full administrative access to every repository and setting. We recommend:

- Maintain no more than 2-3 organization owners for most projects
- Use the **Member** role for regular contributors
- Use **Teams** with specific repository permissions rather than individual admin grants
- Document why each person has the access level they do

For individual repositories: **Settings** > **Collaborators and teams**. Review who has **Admin** access and reduce to **Maintain** or **Write** where full admin is not needed.

##### Audit Collaborator Permissions Quarterly [D.A.13]

**Navigation** (Organization): **Organization Settings** > **People**

GitHub does not provide automated quarterly access reviews. We recommend establishing a calendar reminder and following this process:

1. Navigate to **Organization Settings** > **People**
2. Sort by **Last active** to identify inactive members
3. Review each member's role and repository access
4. Remove access for members who no longer need it
5. For organizations with GitHub Enterprise, use the **Audit log** (**Organization Settings** > **Audit log**) to review recent permission changes

The People page displays a table of all organization members with columns for username, role (Owner, Member), 2FA status, and last active date. Use the role and 2FA filters at the top of the page to quickly identify members who need attention.

##### Use Teams for Permission Management [D.A.14]

**Navigation**: **Organization Settings** > **Teams** > **New team**

Teams provide structured, maintainable permission management instead of granting access to individuals:

1. Create teams reflecting your project structure (e.g., `core-maintainers`, `security-team`, `triage-team`)
2. Assign repository access at the team level: **Team page** > **Repositories** > **Add repository**
3. Set the appropriate permission level for each team-repository combination: **Read**, **Triage**, **Write**, **Maintain**, or **Admin**
4. Add members to teams rather than granting individual repository access

**Example team structure** for an open source project:

| Team | Repository Permission | Purpose |
|---|---|---|
| `core-maintainers` | Maintain | Merge PRs, manage issues |
| `release-managers` | Admin | Create releases, manage settings |
| `security-team` | Write | Handle security reports, develop fixes |
| `triage-team` | Triage | Label and assign issues |

##### Enable SSO/SAML [D.A.15]

!!! info "Requires GitHub Enterprise Cloud"

    SAML single sign-on is available only with GitHub Enterprise Cloud plans.

**Navigation**: **Organization Settings** > **Authentication security** > **SAML single sign-on**

If your organization uses an identity provider (Okta, Azure AD, OneLogin, etc.), enabling SAML SSO centralizes authentication and allows you to enforce organizational security policies. Configure per your identity provider's GitHub integration documentation.

##### Review and Rotate Deploy Keys [D.A.16]

**Navigation**: Repository > **Settings** > **Deploy keys**

Review the list of deploy keys:

1. Remove any keys that are no longer in use (click the **Delete** button next to each key)
2. For active keys, verify they have the minimum required access (**read-only** unless write access is specifically needed)
3. Rotate keys annually by generating a new key, adding it, testing, and then removing the old key
4. Document where each deploy key is used (e.g., which CI/CD system or deployment environment)

##### Fine-Grained Personal Access Tokens [D.A.17]

**Navigation** (Individual): **User Settings** > **Developer settings** > **Personal access tokens** > **Fine-grained tokens** > **Generate new token**

**Navigation** (Organization): **Organization Settings** > **Personal access tokens** (under "Third-party access") to manage token policies

Fine-grained tokens replace classic tokens with scoped access:

1. Set a descriptive **Token name** and **Expiration date** (we recommend 90 days or less)
2. Under **Repository access**, select **Only select repositories** and choose only the repositories this token needs
3. Under **Permissions**, grant only the specific permissions needed (e.g., only **Contents: Read** for a token used to clone)

**Organization policy**: Organization owners can restrict which token types are allowed. Navigate to **Organization Settings** > **Personal access tokens** and configure:

- Whether fine-grained tokens are allowed
- Whether classic tokens are allowed (we recommend disabling classic tokens)
- Whether tokens require admin approval before use

#### Repository Security Features [D.A.18–25]

**Navigation**: Repository > **Settings** > **Code security and analysis** (under "Security")

This settings page contains the majority of the repository-level security features. It displays a vertical list of security features, each with a short description, an **Enable** or **Disable** button, and in some cases a **Set up** link for more complex configuration. Work through each feature from top to bottom:

##### Dependabot Alerts [D.A.18]

Click **Enable** next to **Dependabot alerts**. GitHub will begin monitoring your dependency manifests (`package.json`, `requirements.txt`, `pom.xml`, etc.) and notifying you when known vulnerabilities affect your dependencies.

**View alerts**: Repository > **Security** tab > **Dependabot alerts**

##### Dependabot Security Updates [D.A.19]

Click **Enable** next to **Dependabot security updates**. Dependabot will automatically create pull requests to update vulnerable dependencies to the minimum fixed version.

For more control, create a `.github/dependabot.yml` configuration file:

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

##### Secret Scanning [D.A.20]

Click **Enable** next to **Secret scanning**. GitHub will scan your repository for known secret patterns (API keys, tokens, passwords) across all branches and the full commit history.

**View alerts**: Repository > **Security** tab > **Secret scanning alerts**

##### Secret Scanning Push Protection [D.A.21]

!!! info "Requires GitHub Advanced Security for private repositories"

    Push protection is available for free on all public repositories. For private repositories, it requires a GitHub Advanced Security license.

Click **Enable** next to **Push protection** (under Secret scanning). When enabled, GitHub blocks pushes that contain detected secrets. Contributors receive a message explaining why their push was blocked and can either remove the secret or, if it is a false positive, bypass the block with a justification that is logged.

##### Code Scanning / CodeQL [D.A.22]

Click **Set up** next to **Code scanning**. GitHub offers two approaches:

**Default setup** (recommended for most projects): Click **Set up** > **Default**. GitHub automatically detects your project's languages and configures CodeQL analysis. Analysis runs on every push and pull request.

**Advanced setup**: Click **Set up** > **Advanced** to customize the CodeQL workflow. This generates a `.github/workflows/codeql-analysis.yml` file you can modify. Use this if you need to:

- Analyze additional languages not auto-detected
- Configure custom queries
- Adjust the analysis schedule

**View results**: Repository > **Security** tab > **Code scanning alerts**

##### Security Advisories [D.A.23]

**Navigation**: Repository > **Settings** > **Code security and analysis** > **Private vulnerability reporting**

Click **Enable** next to **Private vulnerability reporting**. This allows security researchers to privately report vulnerabilities directly through GitHub rather than opening a public issue. You receive the report in the **Security** tab > **Advisories** section.

##### Repository Visibility [D.A.24]

**Navigation**: Repository > **Settings** > **General** > scroll to **Danger Zone** > **Change repository visibility**

For open source projects, the repository should be **Public**. For sensitive components, private forks, or security-related tooling, set the repository to **Private** or **Internal** (for organizations on GitHub Enterprise).

##### Disable Unused Features [D.A.25]

**Navigation**: Repository > **Settings** > **General**

Scroll through the **Features** section and uncheck any features your project does not use:

- **Wikis**: Disable if documentation lives in the repository or an external site
- **Projects**: Disable if you use an external project management tool
- **Discussions**: Disable if community discussions happen elsewhere

Disabling unused features reduces the attack surface and prevents confusion about where project information is maintained.

---

### Documentation Requirements on GitHub

#### Security Documentation Setup [D.B.1–3]

##### Creating SECURITY.md [D.B.1]

GitHub provides a built-in mechanism for security policies:

**Navigation**: Repository > **Security** tab > **Security policy** > **Start setup**

If your repository does not yet have a security policy, the Security tab displays a prominent **Start setup** button under the "Security policy" heading. Clicking it creates a `SECURITY.md` file with a template and opens the GitHub file editor. Edit the template to include:

1. **Preferred contact method**: Specify an email address, link to your private vulnerability reporting form (if enabled), or bug bounty platform URL
2. **Expected response timeline**: State how quickly reporters can expect acknowledgment (e.g., "We will acknowledge receipt within 48 hours")
3. **Disclosure policy and timeline**: Describe your coordinated disclosure process (e.g., "We aim to release a fix within 90 days of a confirmed report")
4. **PGP key** (if applicable): Link to or include your security team's PGP public key for encrypted communications
5. **Scope**: State which versions are covered by this security policy

**Example SECURITY.md**:

```markdown
# Security Policy

## Supported Versions


| Version | Supported          |
| ------- | ------------------ |
| 5.1.x   | :white_check_mark: |
| 5.0.x   | :x:                |
| 4.0.x   | :white_check_mark: (security fixes only) |
| < 4.0   | :x:                |

## Reporting a Vulnerability

Please report security vulnerabilities through GitHub's private
vulnerability reporting feature on this repository, or by emailing
security@example.com.

**Do not open a public issue for security vulnerabilities.**

### What to Expect

- **Acknowledgment**: Within 48 hours
- **Initial assessment**: Within 1 week
- **Fix timeline**: Critical issues within 7 days; high severity
  within 30 days
- **Disclosure**: Coordinated with reporter, typically 90 days

### Scope

This policy covers the core library. Third-party plugins and
integrations maintained by other parties are out of scope.
```

##### Security Policy Discoverability [D.B.2]

Once you create `SECURITY.md`, GitHub automatically:

- Displays a **Security policy** link in the repository's **Security** tab
- Shows a notice in the sidebar when someone opens a new issue, directing security reports to the proper channel

To increase discoverability, add a link to your security policy in your `README.md`:

```markdown
## Security

See SECURITY.md for our security policy and vulnerability reporting instructions.
```

##### Supported Versions Documentation [D.B.3]

Include a supported versions table in your `SECURITY.md` (as shown in the example above). This tells researchers and users which versions receive security patches, preventing wasted effort on reporting issues in unsupported versions.

#### General Documentation Files [D.B.4–9]

GitHub provides a **Community Standards** page that tracks which community health files are present:

**Navigation**: Repository > **Insights** > **Community Standards**

This page displays a checklist of community health files with a green checkmark next to each file that exists and a **Proposed** or **Add** link next to missing files. Use it as a quick audit of which documentation your project has and what is still needed:

- **README.md** [D.B.4]: Create at the repository root. Include a project overview, security considerations for users, and a link to `SECURITY.md`.

- **Installation instructions** [D.B.5]: Include in your README or a dedicated `INSTALL.md`. Document the recommended installation method and any security-relevant configuration steps.

- **LICENSE** [D.B.6]: Click **Add** on the Community Standards page or create a `LICENSE` file at the repository root. GitHub provides license templates; select one using a valid SPDX identifier.

- **CONTRIBUTING.md** [D.B.7]: Create at the repository root or in `.github/`. Include security considerations for contributors (e.g., "Do not commit secrets," "All dependencies must be reviewed before adding," "Security-sensitive changes require review from @security-team").

- **CODE_OF_CONDUCT.md** [D.B.8]: Click **Add** on the Community Standards page. GitHub provides templates based on the Contributor Covenant and Citizen Code of Conduct.

- **CHANGELOG.md** [D.B.9]: Create at the repository root, or use GitHub Releases to document changes. Security fixes should be clearly labeled (e.g., **Security**: Fixed XSS vulnerability in input parser).

#### Security-Specific Documentation [D.B.10–16]

**Process Guidance**: The following items describe documentation that your project should maintain. These are not GitHub settings but rather files and processes you create within your repository.

**Threat model** [D.B.10]: Create a `docs/security/THREAT-MODEL.md` or equivalent file. Document your project's key assets, trust boundaries, threat actors, and mitigations. For projects that don't warrant a formal threat model, a "Security Considerations" section in your README explaining what the project does and does not protect against is a good starting point.

**Security architecture overview** [D.B.11]: Document how your project implements security controls in `docs/security/ARCHITECTURE.md`. Include authentication flows, authorization models, data protection mechanisms, and how secrets are handled.

**Authentication/authorization model** [D.B.12], If your project handles user authentication or authorization, document the design in your security architecture or a dedicated file. Explain the authentication methods supported, session management, and permission model.

**Cryptography usage** [D.B.13]: Document all cryptographic algorithms used, key management practices, and the rationale for cryptographic choices in `docs/security/CRYPTOGRAPHY.md`. This helps security reviewers quickly assess the cryptographic posture.

**Known limitations** [D.B.14]: Document security boundaries and known limitations in your README or security documentation. Be explicit about what the project is *not* designed to protect against.

**Hardening guide** [D.B.15]: Provide a `docs/HARDENING.md` with secure deployment and configuration recommendations for users. Cover topics like recommended TLS versions, secure headers, restrictive file permissions, and disabling debug modes in production.

**Dependency policy** [D.B.16]: Document how your project selects and evaluates dependencies. Consider including criteria such as: minimum maintenance activity, license compatibility, vulnerability history, and the review process for adding new dependencies.

---

### Build and Release Security on GitHub

#### GitHub Actions Security [D.C.1–8]

##### CI/CD for All Builds [D.C.1]

Create GitHub Actions workflows to build your project automatically. No release should be built on a developer's local machine.

**Navigation**: Repository > **Actions** tab > **New workflow** (or create `.github/workflows/` files directly)

**Example minimal build workflow** (`.github/workflows/build.yml`):

```yaml
name: Build
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
      - name: Build
        run: make build
      - name: Test
        run: make test
```

> **Key practice**: Set `permissions: contents: read` at the workflow or job level to apply the principle of least privilege. Only grant additional permissions where explicitly needed.

##### Pin CI/CD Action Versions by Hash [D.C.2]

Replace tag-based action references with full SHA commit hashes:

```yaml
# Risky: tag can be moved to point to different code
- uses: actions/checkout@v4

# Secure: immutable reference to exact code
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
```

To find the SHA for an action version:

1. Navigate to the action's repository on GitHub (e.g., `github.com/actions/checkout`)
2. Click **Tags** and find the version you want (e.g., `v4.2.2`)
3. Click the tag name to view the commit
4. Copy the full 40-character commit SHA from the URL or the commit header

Tools like **StepSecurity Secure Workflows**[^stepsecurity] and **pinact**[^pinact] can automate the process of pinning action references across your workflows.

##### Minimize Build Dependencies [D.C.3]

Review your workflow steps and remove unnecessary tools or dependencies:

1. Use minimal runner images where possible (`ubuntu-latest` is sufficient for most projects)
2. Only install tools that the build actually needs
3. Prefer official GitHub Actions from verified publishers over third-party actions
4. Audit all third-party actions before adding them (check the action's source code, maintainer, and usage)

##### Ephemeral Build Environments [D.C.4]

GitHub-hosted runners are ephemeral by default: each job runs in a fresh virtual machine that is destroyed after the job completes. If you use self-hosted runners, configure them to:

1. Use a fresh container or VM for each job
2. Clean the workspace between runs
3. Avoid persisting sensitive data between jobs

##### Isolate Build Environments [D.C.5]

To limit network access during builds:

**Workflow-level permissions**: Restrict the `GITHUB_TOKEN` permissions to the minimum needed:

```yaml
permissions:
  contents: read
  # Only add additional permissions when needed
```

**Action-level isolation**: GitHub does not natively support blocking network access during workflow execution. For stricter isolation, consider:

- Self-hosted runners with network policies
- Container-based workflows with network restrictions
- Build tools that support offline/hermetic builds (e.g., Bazel)

##### Pin Dependency Versions [D.C.6]

Ensure your build uses lockfiles and installs exact versions. In your workflow:

```yaml
# Node.js: use npm ci (not npm install)
- run: npm ci

# Python: use frozen lockfile
- run: uv sync --frozen

# Go: verify checksums
- run: go mod verify

# Rust: use locked flag
- run: cargo build --locked
```

##### Verify Dependency Integrity [D.C.7]

Enable hash verification in your build process:

```yaml
# Python with pip: require hashes
- run: pip install --require-hashes -r requirements.txt

# Node.js: npm ci verifies package-lock.json integrity hashes
- run: npm ci

# Go: go mod verify checks go.sum hashes
- run: go mod verify
```

##### Scan Dependencies Before Build [D.C.8]

Add vulnerability scanning as a required step before building:

```yaml
- name: Scan dependencies
  run: |
    npm audit --audit-level=high
    # Or: pip-audit, cargo audit, govulncheck, etc.

- name: Build (only runs if scan passes)
  run: make build
```

Or use a dedicated scanning action:

```yaml
- name: Run Trivy vulnerability scanner
  uses: aquasecurity/trivy-action@915b19bbe73b92a6cf82a1bc12b087c9a19a5fe2 # v0.28.0
  with:
    scan-type: 'fs'
    severity: 'HIGH,CRITICAL'
    exit-code: '1'
```

#### Build Process Configuration [D.C.9–14]

##### Document Build Process [D.C.9]

**Process Guidance**: Create a `BUILD.md` or `CONTRIBUTING.md` section that documents:

1. Prerequisites (required tools and versions)
2. Build commands (from clone to artifact)
3. Test commands
4. How to verify the build locally matches CI

This helps contributors reproduce the build and supports verifying that released artifacts match the source code.

##### Compiler Security Flags [D.C.10]

**Process Guidance**: For compiled languages, enable security-hardening flags in your build configuration. Document these in your `BUILD.md`. Common flags:

- **C/C++**: `-fstack-protector-strong`, `-D_FORTIFY_SOURCE=2`, `-fPIE`, `-Wformat-security`
- **Go**: Memory-safety protections are provided by the Go toolchain; verify PIE and linker hardening for your target platform
- **Rust**: Memory safety is built in; enable release overflow checks if required and verify linker hardening for native targets

In your workflow, verify flags are applied:

```yaml
- name: Build with security flags
  run: |
    export CFLAGS="-fstack-protector-strong -D_FORTIFY_SOURCE=2 -fPIE"
    export LDFLAGS="-pie -z relro -z now"
    make build
```

##### SAST During Build [D.C.11]

Configure CodeQL or another SAST tool to run as part of your CI pipeline. The default CodeQL setup (configured under **Code security and analysis**, see [D.A.22]) runs analysis on pushes and PRs automatically.

For additional SAST tools, add workflow steps:

```yaml
- name: Run Semgrep
  uses: semgrep/semgrep-action@713efdd345f3035192eaa63f56867b88e63e4e5d # v1
  with:
    config: p/security-audit
```

##### Security Tests [D.C.12]

**Process Guidance**: Include security-specific tests alongside your unit and integration tests:

```yaml
- name: Run tests including security tests
  run: |
    make test
    make test-security  # Dedicated security test suite
```

Security tests might include: input validation edge cases, authentication bypass attempts, authorization boundary tests, and injection payload tests.

##### Generate SBOM During Build [D.C.13]

Use GitHub's built-in dependency submission API or a dedicated SBOM tool:

```yaml
- name: Generate SBOM
  uses: anchore/sbom-action@f325610c9f50a54015d37c8d16cb3b0e2c8f4de0 # v0.18.0
  with:
    format: spdx-json
    output-file: sbom.spdx.json

- name: Upload SBOM as artifact
  uses: actions/upload-artifact@ea165f8d65b6e75b540449e92b4886f43607fa02 # v4.6.2
  with:
    name: sbom
    path: sbom.spdx.json
```

##### Build from Verified Source [D.C.14]

Ensure your CI/CD only builds from the official repository:

```yaml
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
  with:
    # Verify the checkout is from the expected repository
    repository: ${{ github.repository }}
    # For tagged releases, verify the tag
    ref: ${{ github.ref }}
```

For release builds, verify the git tag signature:

```yaml
- name: Verify tag signature
  if: startsWith(github.ref, 'refs/tags/')
  run: git verify-tag "${GITHUB_REF#refs/tags/}" || exit 1
```

#### Release Signing and Provenance [D.C.15–21]

##### Sign Release Artifacts [D.C.15]

Use Sigstore Cosign to sign release artifacts in your GitHub Actions workflow:

```yaml
- name: Install Cosign
  uses: sigstore/cosign-installer@dc72c7d5c4d10cd6bcb8cf6e3fd625a9e5e537da # v3.7.0

- name: Sign artifact
  run: cosign sign-blob --yes artifact.tar.gz --bundle artifact.tar.gz.bundle
```

For container images:

```yaml
- name: Sign container image
  run: cosign sign --yes ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}@${{ steps.build.outputs.digest }}
```

##### Publish Signing Keys/Certificates [D.C.16]

**Process Guidance**: If using GPG signing, publish your public key:

1. Add your GPG public key to your `SECURITY.md` or a dedicated `VERIFICATION.md`
2. Upload to public keyservers (`keys.openpgp.org`)
3. Reference the key fingerprint in your release notes

If using Sigstore (recommended), verification is keyless: the signing certificate is recorded in the Rekor transparency log, so users can verify signatures without managing keys.

##### Sign Git Tags [D.C.17]

Create signed tags for releases:

```shell
# Create a signed tag
git tag -s v1.0.0 -m "Release v1.0.0"

# Push the signed tag
git push origin v1.0.0
```

In your release workflow, verify the tag is signed before building:

```yaml
- name: Verify signed tag
  run: git verify-tag "${GITHUB_REF#refs/tags/}"
```

##### Document Verification Process [D.C.18]

**Process Guidance**: Include verification instructions in your release notes or `VERIFICATION.md`:

```markdown
## Verifying Release Artifacts

### Sigstore Verification
cosign verify-blob --bundle artifact.tar.gz.bundle artifact.tar.gz \
  --certificate-identity=https://github.com/org/repo/.github/workflows/release.yml@refs/tags/v1.0.0 \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com

### GPG Verification
gpg --verify artifact.tar.gz.sig artifact.tar.gz
```

##### Sigstore for Keyless Signing [D.C.19]

Sigstore provides **keyless signing** using short-lived certificates tied to your CI/CD identity. When signing from GitHub Actions, the certificate records:

- The GitHub Actions workflow that performed the signing
- The repository, branch, and commit
- The OIDC issuer (GitHub)

This eliminates key management while providing strong provenance guarantees. See the Sigstore documentation[^sigstore-docs] for details.

##### Generate Provenance Attestations [D.C.20]

Use the SLSA GitHub generator or GitHub's built-in attestation feature.

The SLSA generator is a **reusable workflow** and must be called at the job level (not as a step). Add a separate job in your release workflow:

```yaml
jobs:
  build:
    # ... your build job that outputs artifact digests ...

  provenance:
    needs: [build]
    permissions:
      actions: read
      id-token: write
      contents: write
    uses: slsa-framework/slsa-github-generator/.github/workflows/generator_generic_slsa3.yml@v2.1.0
    with:
      base64-subjects: "${{ needs.build.outputs.digest }}"
```

Or use GitHub's native attestation (available for GitHub Actions):

```yaml
- name: Attest build provenance
  uses: actions/attest-build-provenance@7668571508540a607bdfd90a87a560489fe372eb # v2.1.0
  with:
    subject-path: 'dist/*'
```

##### Publish Attestations with Releases [D.C.21]

Include provenance and SBOM attestations as release assets:

```yaml
- name: Create GitHub Release
  uses: softprops/action-gh-release@da05d552573ad5aba039eaac05058a918a7bf631 # v2.2.2
  with:
    files: |
      dist/artifact.tar.gz
      dist/artifact.tar.gz.bundle
      sbom.spdx.json
      provenance.intoto.jsonl
```

#### Release Process [D.C.22–28]

##### Protected Release Branches and Tags [D.C.22]

**Navigation**: Repository > **Settings** > **Tags** (under "Code and automation")

Create a tag protection rule to prevent unauthorized tag creation:

1. Click **New rule**
2. Enter a pattern (e.g., `v*`) to protect all version tags
3. Only users with **Maintain** or **Admin** access can create matching tags

For release branches, use branch protection rules (see [D.A.1–10]) on branches like `release/*`.

##### Multiple Approvals for Releases [D.C.23]

Use **GitHub Environments** with required reviewers:

**Navigation**: Repository > **Settings** > **Environments** > **New environment**

1. Create a `production` environment
2. Under **Environment protection rules**, check **Required reviewers**
3. Add the reviewers who must approve deployments/releases
4. In your release workflow, reference this environment:

```yaml
jobs:
  release:
    runs-on: ubuntu-latest
    environment: production  # Requires approval
    steps:
      - name: Publish release
        run: make release
```

The environment settings page shows the environment name, a list of protection rules (including required reviewers, wait timers, and deployment branch restrictions), and a list of environment secrets and variables. When a workflow job references this environment, GitHub pauses execution and notifies the listed reviewers, who must approve before the job proceeds.

##### Automate Release Process [D.C.24]

Create a dedicated release workflow triggered by tag pushes or manual dispatch:

```yaml
name: Release
on:
  push:
    tags:
      - 'v*'

permissions:
  contents: write
  id-token: write

jobs:
  release:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
      - name: Build
        run: make build
      - name: Sign
        run: cosign sign-blob --yes dist/artifact.tar.gz --bundle dist/artifact.tar.gz.bundle
      - name: Create Release
        uses: softprops/action-gh-release@da05d552573ad5aba039eaac05058a918a7bf631 # v2.2.2
        with:
          files: dist/*
          generate_release_notes: true
```

##### Publish to Official Registries [D.C.25]

Configure your workflow to publish to the appropriate package registry:

```yaml
# npm
- run: npm publish
  env:
    NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

For PyPI, use trusted publishing with OIDC. Note that `permissions` must be set at the **job level**, not the step level:

```yaml
jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      id-token: write  # Required for trusted publishing
    steps:
      - uses: pypa/gh-action-pypi-publish@release/v1
```

##### Trusted Publishing [D.C.26]

Configure OIDC-based publishing to eliminate long-lived secrets:

**PyPI**: Configure on pypi.org under your project's **Publishing** settings. Add your GitHub repository and workflow file path as a trusted publisher. Then use the `pypa/gh-action-pypi-publish` action without API tokens.

**npm**: Use npm's provenance feature with `npm publish --provenance` in GitHub Actions. This requires `id-token: write` permission.

**RubyGems**: Configure trusted publishing on rubygems.org under your gem's settings.

##### Include SBOM with Release [D.C.27]

Attach the SBOM generated during your build (see [D.C.13]) as a release asset alongside your build artifacts.

##### Announce Releases Through Official Channels [D.C.28]

**Process Guidance**: Use GitHub Releases as your primary announcement channel. Configure your release workflow to generate release notes automatically:

```yaml
- uses: softprops/action-gh-release@da05d552573ad5aba039eaac05058a918a7bf631 # v2.2.2
  with:
    generate_release_notes: true
```

Additionally, consider posting release announcements to your project's mailing list, blog, or social media from verified accounts.

---

### Vulnerability Management on GitHub

#### Private Vulnerability Reporting [D.D.1–7]

##### Private Reporting Channel [D.D.1]

**Navigation**: Repository > **Settings** > **Code security and analysis** > **Private vulnerability reporting**

Click **Enable**. Once enabled, a **Report a vulnerability** button appears on your repository's **Security** tab > **Advisories** page. Researchers who click this button see a form with fields for vulnerability type, severity, affected versions, and a description. Submitting the form creates a private advisory visible only to repository maintainers, providing a secure channel for disclosure without requiring the researcher to find an email address or open a public issue.

##### Designated Security Contacts [D.D.2]

**Process Guidance**: Document your security contacts in `SECURITY.md`. GitHub does not have a dedicated "security contact" field, but the private vulnerability reporting feature routes reports to all repository administrators and organization owners.

To ensure the right people receive reports:

1. Add your security team members as repository collaborators with at least **Triage** access
2. List security contacts explicitly in your `SECURITY.md`
3. Consider creating a `security@your-project.org` email alias

##### Acknowledgment, Assessment, Fix, and Disclosure Timelines [D.D.3–6]

**Process Guidance**: Document these timelines in your `SECURITY.md`:

- **Acknowledgment** [D.D.3]: Respond within 48-72 hours
- **Assessment** [D.D.4]: Complete initial severity assessment within 1 week
- **Fix timeline** [D.D.5]: Set expectations by severity (critical: days, high: weeks, medium: months)
- **Disclosure** [D.D.6]: Coordinate with the reporter, typically targeting 90 days

When you receive a report through GitHub's private vulnerability reporting, respond using the advisory's private discussion thread to maintain confidentiality.

##### Safe Harbor Statement [D.D.7]

**Process Guidance**: Include a safe harbor statement in your `SECURITY.md`:

```markdown
## Safe Harbor

We consider security research conducted in good faith under this
policy to be authorized. We will not pursue legal action against
researchers who discover and report vulnerabilities responsibly in
accordance with this policy.
```

#### Security Advisories [D.D.8–19]

##### Creating and Managing Security Advisories [D.D.8–13]

**Navigation**: Repository > **Security** tab > **Advisories** > **New draft security advisory**

The advisory creation form is divided into sections: **Affected products** (ecosystem, package, versions), **Severity** (CVSS calculator and severity dropdown), and **Details** (description, credits, references). Fill in each section:

**Step 1**: Fill in the advisory details:

- **Ecosystem**: Select the package ecosystem (npm, pip, Maven, Go, etc.)
- **Package name**: Your package's name in the ecosystem
- **Affected versions**: Specify the version range affected
- **Patched versions**: Specify the version containing the fix
- **Severity**: Use the CVSS calculator or select a severity level [D.D.9]
- **Description**: Describe the vulnerability, its impact, and the fix

**Step 2**: Develop the fix using a **temporary private fork**:

1. From the advisory page, click **Start a temporary private fork**
2. Develop and test the fix in this private fork [D.D.10, D.D.11]
3. Review the fix with your security team
4. The fix remains private until you publish the advisory

**Step 3**: Request a CVE [D.D.14]:

GitHub is a CVE Numbering Authority (CNA). Click **Request CVE** on the advisory page. GitHub will assign a CVE identifier if the request is approved.

**Step 4**: Publish the advisory [D.D.15]:

1. Click **Publish advisory** when the fix is ready
2. The advisory, CVE, and associated Dependabot alerts become public
3. Dependabot automatically creates pull requests for affected downstream projects

**Step 5**: Credit the reporter [D.D.16] — Use the **Credits** section of the advisory to acknowledge the security researcher by their GitHub username.

**Step 6**: Provide upgrade guidance [D.D.17] — Include clear upgrade instructions in the advisory description and release notes.

**Step 7**: Document workarounds [D.D.18], If a workaround exists before a patched version is available, describe it in the advisory.

**Step 8**: Coordinate with downstream projects [D.D.19] — For widely-used libraries, consider privately notifying major downstream consumers before public disclosure. Use the advisory's private discussion thread for coordination.

**Backporting policy** [D.D.12] and **communication plan** [D.D.13]: Document these processes in your `SECURITY.md` or an internal security runbook.

#### Dependency Monitoring [D.D.20–24]

##### Dependabot Alerts Dashboard [D.D.20]

**Navigation**: Repository > **Security** tab > **Dependabot alerts**

Review alerts regularly:

1. **Triage**: Assess each alert's relevance to your project (some vulnerabilities may not be exploitable in your usage context)
2. **Remediate**: Click **Create Dependabot security update** to generate an automatic fix PR, or manually update the dependency
3. **Dismiss**: For false positives or accepted risks, dismiss with a reason

For organizations: **Organization Settings** > **Security** > **Security overview** provides a cross-repository view of all Dependabot alerts.

##### Regular Dependency Updates [D.D.21]

Configure Dependabot version updates (separate from security updates) to keep dependencies current:

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    reviewers:
      - "org/maintainers"
```

##### Periodic Security Assessments [D.D.22]

**Process Guidance**: Schedule annual security assessments. GitHub's **Security Overview** (available for organizations) provides a starting point by showing the status of security features across all repositories.

For formal assessments, use the self-assessment template in Appendix D, Section F.

##### Track Security Debt [D.D.23]

**Process Guidance**: Use GitHub Issues with a `security-debt` label to track known security issues and their remediation timelines. Create a project board or use GitHub Projects to visualize security debt alongside feature work.

##### Review Past Vulnerabilities [D.D.24]

**Process Guidance**: After resolving a security advisory, conduct a post-incident review. Document:

- What went wrong
- How the vulnerability was introduced
- What controls could have prevented it
- Action items to prevent recurrence

Store post-incident reviews in your repository's `docs/security/` directory or an internal wiki.

---

### Community and Governance on GitHub

#### GitHub Governance Tools

GitHub provides several features that support governance and community practices, though most governance items in Appendix D are organizational processes rather than platform settings.

##### CODEOWNERS File

Create a `CODEOWNERS` file (in `.github/`, `docs/`, or the repository root) to define who is responsible for reviewing specific parts of the codebase:

```
# Default owners for everything
* @org/core-maintainers

# Security-sensitive areas
/src/auth/ @org/security-team
/src/crypto/ @org/security-team
/.github/workflows/ @org/platform-team
```

When combined with branch protection rules requiring code owner reviews [D.A.3], this ensures the right experts review changes to their areas.

##### Organization Teams and Roles

**Navigation**: **Organization Settings** > **Teams**

Create teams that reflect your governance structure [D.E.2]:

- `maintainers`: Core decision-makers with merge access
- `security-team`: Security-focused members
- `release-managers`: Members authorized to create releases
- `triage`: Community members who can label and manage issues

##### Custom Repository Roles

!!! info "Requires GitHub Enterprise Cloud"

    Custom repository roles are available on GitHub Enterprise Cloud plans.

**Navigation**: **Organization Settings** > **Repository roles** > **Create a role**

Define custom roles that match your governance model (e.g., a "Security Reviewer" role with read access plus the ability to manage security advisories).

##### Audit Log

**Navigation** (Organization): **Organization Settings** > **Audit log**

The audit log records administrative actions including:

- Permission changes
- Repository creation/deletion
- Branch protection rule changes
- Team membership changes

Use the audit log for periodic access reviews [D.A.13] and to investigate suspicious activity.

#### Process and Policy Guidance [D.E.1–21]

> **Note**: The following items from Appendix D describe organizational practices rather than platform settings. We provide recommended approaches for implementing these practices with GitHub as your project's home.

##### Governance Model [D.E.1]

Create a `GOVERNANCE.md` file documenting your decision-making process. Include:

- How decisions are made (consensus, voting, maintainer approval)
- How new maintainers are nominated and approved
- How disputes are resolved
- Meeting cadence and format (if applicable)

See Appendix E for governance policy templates.

##### Maintainer Roles [D.E.2]

Document roles in `GOVERNANCE.md`. Map each role to GitHub permission levels:

| Role | GitHub Permission | Responsibilities |
|---|---|---|
| Contributor | Read/Triage | Submit PRs, report issues |
| Committer | Write | Merge PRs, manage issues |
| Maintainer | Maintain | Manage settings, handle releases |
| Admin | Admin | Organization settings, security |

##### Succession Plan [D.E.3]

**Process Guidance**: Document in `GOVERNANCE.md` or an internal document:

- How maintainer status is transferred
- Who takes over if the lead maintainer becomes unavailable
- Where credentials and secrets are stored (use a shared password manager)
- How to contact backup maintainers

##### Multiple Active Maintainers [D.E.4]

**Navigation**: Repository > **Insights** > **Contributors**

Monitor contributor activity to ensure your project does not depend on a single person. The Contributors page shows commit frequency by author. Actively recruit and mentor new maintainers.

##### Foundation or Organizational Backing [D.E.5]

**Process Guidance**: For critical projects, consider joining a foundation (Apache, Linux Foundation, OpenSSF). Document any organizational backing in your `README.md` or `GOVERNANCE.md`.

##### Funding Model [D.E.6]

**Navigation**: Repository > **Settings** > **General** > **Sponsorship** (or create a `.github/FUNDING.yml`)

Configure GitHub Sponsors or link to other funding platforms:

```yaml
# .github/FUNDING.yml
github: [maintainer-username]
open_collective: project-name
```

##### Contributor Verification [D.E.7]

Enable required pull request reviews [D.A.2] and consider the first-time contributor label that GitHub automatically applies. When a first-time contributor submits a PR, GitHub shows a banner indicating this is their first contribution.

##### CLA or DCO Requirement [D.E.8]

Configure a CLA or DCO bot in your workflows:

```yaml
# Developer Certificate of Origin
- name: DCO Check
  uses: dcoapp/app@v1
```

Or use a CLA assistant like `cla-assistant/cla-assistant`.

##### New Contributor Review Requirements [D.E.9]

**Process Guidance**: Document in `CONTRIBUTING.md` that:

- All first-time contributions receive additional review
- Contributions touching security-sensitive areas require security team review
- Large changes should be discussed in an issue before submitting a PR

##### Commit Access Progression [D.E.10]

**Process Guidance**: Document the path from contributor to committer in `GOVERNANCE.md`:

1. External contributor (opens PRs, reviewed by maintainers)
2. Regular contributor (recognized for consistent quality contributions)
3. Committer (granted Write access after nomination by existing maintainers)
4. Maintainer (granted Maintain access after sustained involvement)

##### Periodic Access Review [D.E.11]

Establish a quarterly process using the organization's People page (see [D.A.13]). Review and remove:

- Inactive maintainers (no activity for 6+ months)
- Contributors who no longer participate
- Bot accounts that are no longer needed

##### Security in Communications [D.E.12]

Use **GitHub Discussions** to create a security category for non-sensitive security topics. Enable Discussions under **Repository Settings** > **General** > **Features**.

##### Security Champion [D.E.13]

**Process Guidance**: Identify a security champion and document their role in `SECURITY.md` and `GOVERNANCE.md`. The security champion:

- Triages incoming security reports
- Coordinates vulnerability response
- Advocates for security improvements
- Keeps the team aware of relevant security developments

##### Security Training [D.E.14]

**Process Guidance**: Document recommended training resources in your `CONTRIBUTING.md` or internal wiki. Link to resources like the OpenSSF Secure Development Fundamentals course, OWASP training materials, and your project-specific security documentation.

##### Incident Response Plan [D.E.15]

**Process Guidance**: Create a `docs/security/INCIDENT-RESPONSE.md` documenting:

1. How to detect a potential compromise
2. Who to contact immediately
3. Steps to contain the incident
4. How to assess impact
5. Communication plan (internal and external)
6. Recovery procedures
7. Post-incident review process

See Appendix E for incident response plan templates.

##### Post-Incident Review [D.E.16]

**Process Guidance**: After any security incident, conduct a blameless post-incident review. Document findings in `docs/security/incidents/` with a standardized format:

- Date and duration
- What happened
- Timeline of events
- Root cause analysis
- Action items and their owners

##### Public Issue Tracker [D.E.17]

GitHub Issues are public by default for public repositories. Ensure security-sensitive issues are filed through the private vulnerability reporting mechanism [D.D.1] rather than as public issues.

##### Public Roadmap [D.E.18]

Use **GitHub Projects** (new) to create a public roadmap board. Navigate to your organization or repository's **Projects** tab to create a board visible to the public.

##### Meeting Notes [D.E.19]

**Process Guidance**: For projects with governance meetings, publish notes in your repository (e.g., `docs/meetings/`) or use GitHub Discussions.

##### Security Improvements Communication [D.E.20]

Include security improvements in your release notes and `CHANGELOG.md`. Use a dedicated **Security** section in release notes:

```markdown
## Security
- Upgraded dependency X to fix CVE-2025-XXXX
- Added rate limiting to API endpoints
- Enabled secret scanning push protection
```

##### Annual Security Report [D.E.21]

**Process Guidance**: Publish an annual summary covering:

- Security incidents and their resolution
- Vulnerabilities reported and fixed
- Security improvements implemented
- Third-party audit results (if applicable)
- Goals for the coming year

Publish as a blog post, GitHub Discussion, or document in `docs/security/`.

#### GitHub Configuration Summary Checklist

Use this checklist to verify you have completed all platform-specific configurations:

- [ ] Branch protection rules configured on default branch [D.A.1–10]
- [ ] 2FA required for organization members [D.A.11]
- [ ] Team-based permission management configured [D.A.14]
- [ ] Fine-grained tokens policy configured [D.A.17]
- [ ] Dependabot alerts and security updates enabled [D.A.18–19]
- [ ] Secret scanning and push protection enabled [D.A.20–21]
- [ ] Code scanning (CodeQL) configured [D.A.22]
- [ ] Private vulnerability reporting enabled [D.A.23]
- [ ] SECURITY.md created and linked [D.B.1–2]
- [ ] Community health files present (README, LICENSE, CONTRIBUTING, CODE_OF_CONDUCT) [D.B.4–8]
- [ ] GitHub Actions workflows configured with pinned action versions [D.C.1–2]
- [ ] Least-privilege permissions set on workflows [D.C.5]
- [ ] SBOM generation integrated into build [D.C.13]
- [ ] Release signing with Sigstore configured [D.C.15, D.C.19]
- [ ] Provenance attestations enabled [D.C.20]
- [ ] Environment protection rules for releases [D.C.23]
- [ ] CODEOWNERS file configured
- [ ] Governance documentation in place (GOVERNANCE.md) [D.E.1]
- [ ] Incident response plan documented [D.E.15]

---

## Part 2: GitLab Security Configuration Guide

GitLab provides an integrated DevSecOps platform with built-in security scanning, compliance frameworks, and vulnerability management. This section walks through configuring every Appendix D security control using GitLab's interface and CI/CD pipeline features.

**Scope**: These instructions apply to GitLab.com (SaaS). GitLab self-managed instances offer the same features with the same navigation paths. Security features vary by tier:

| Feature Area | Free | Premium | Ultimate |
|---|---|---|---|
| Protected branches, merge request approvals | Yes | Yes | Yes |
| SAST, Secret Detection (CI templates) | Yes | Yes | Yes |
| Dependency Scanning | — | — | Yes |
| Container Scanning | — | — | Yes |
| Security Dashboard | — | — | Yes |
| Compliance Frameworks | — | Yes | Yes |

**Permissions required**: Most settings require **Maintainer** or **Owner** role. Group-level settings require **Group Owner**. We note specific requirements where they differ.

---

### Repository Security Settings on GitLab

#### Protected Branches [D.A.1–10]

GitLab protects branches through **Protected Branches** settings and **Merge Request Approval Rules**.

##### Configuring Protected Branches

**Navigation**: Project > **Settings** > **Repository** > expand **Protected branches**

The Protected branches section shows a table of existing protected branches with columns for the branch name, **Allowed to merge**, **Allowed to push and merge**, and **Allowed to force push**. Below the table, a form allows you to add new protected branches.

**Step 1**: Select or enter the branch name (e.g., `main`) in the **Branch** dropdown.

**Step 2**: Configure protections:

**Enable branch protection on default branch** [D.A.1]: Select the branch and choose who is **Allowed to merge** and **Allowed to push and merge**. Setting push access to **No one** prevents direct pushes and forces all changes through merge requests.

**Require pull request reviews before merging** [D.A.2] — Navigate to **Settings** > **Merge requests** > **Merge request approvals**:

1. Click **Add approval rule**
2. Set the **Approvals required** count (we recommend at least 1)
3. Select approvers (specific users, groups, or predefined groups like "All project members")

The approval rules section displays a table of rules, each showing the rule name, number of approvals required, and the list of eligible approvers. You can create multiple rules (e.g., one requiring a maintainer and another requiring a security team member).

**Require review from code owners** [D.A.3]: In the **Protected branches** section, check **Require approval from code owners**. Create a `CODEOWNERS` file in your repository root:

```
# Default owners
* @group/maintainers

# Security-sensitive paths
/src/auth/ @group/security-team
/.gitlab-ci.yml @group/platform-team
```

**Dismiss stale pull request approvals** [D.A.4]: Navigate to **Settings** > **Merge requests** > **Merge request approvals** > **Approval settings**. Check **Remove all approvals when commits are added to the source branch**. This ensures new commits invalidate previous approvals.

**Require status checks to pass before merging** [D.A.5]: Navigate to **Settings** > **Merge requests** > **Merge checks**. Check **Pipelines must succeed**. This prevents merging when the CI pipeline fails.

**Require branches to be up to date before merging** [D.A.6]: In the same **Merge checks** section, check **Skipped pipelines are considered successful** (uncheck this to require pipelines to actually run). Also configure **Merge method** to **Merge commit with semi-linear history** or **Fast-forward merge** to prevent merging stale branches.

**Require signed commits** [D.A.7]: Navigate to **Settings** > **Repository** > **Push rules** (under "Push rules"). Check **Reject unsigned commits**. This requires all pushed commits to have GPG or SSH signatures.

!!! info "Requires Premium tier or higher"

    Push rules including signed commit requirements are available on GitLab Premium and Ultimate plans, and on GitLab.com Free for public projects.

**Require linear history** [D.A.8]: Navigate to **Settings** > **Merge requests** > **Merge method**. Select **Fast-forward merge**. This prevents merge commits and enforces linear history.

**Restrict who can push to matching branches** [D.A.9]: In the **Protected branches** section, set **Allowed to push and merge** to **No one** or restrict to specific roles (Maintainers only). For more granular control, set **Allowed to push and merge** to specific users or groups.

**Do not allow bypassing branch protection** [D.A.10]: In the **Protected branches** section, ensure **Allowed to push and merge** does not include owners or administrators who should also be subject to the merge request process. Additionally, under **Merge request approvals** > **Approval settings**, check **Prevent approval by author** and **Prevent editing approval rules in merge requests**.

#### Access Control [D.A.11–17]

##### Two-Factor Authentication [D.A.11]

**Navigation** (Group): **Group Settings** > **General** > expand **Permissions and group features**

The Permissions section includes a **Two-factor authentication** area with a checkbox labeled **Require all users in this group to set up two-factor authentication** and a **Grace period** field where you set how long members have to comply (in hours). Check the box and set a grace period (e.g., 48 hours) for existing members to configure 2FA. Members who fail to enable 2FA within the grace period are removed from the group.

For project-level enforcement on GitLab.com, 2FA is enforced at the group level rather than the project level.

##### Review and Minimize Admin Access [D.A.12]

**Navigation** (Group): **Group information** > **Members**

Review members with the **Owner** role:

1. Filter by **Owner** role
2. Reduce Owner access to essential personnel (2-3 people)
3. Downgrade others to **Maintainer** or **Developer** as appropriate

For projects: **Project information** > **Members**. Review members with **Maintainer** or **Owner** roles.

##### Audit Collaborator Permissions Quarterly [D.A.13]

**Navigation** (Group): **Group information** > **Members**

GitLab shows the last activity date for each member. Review quarterly:

1. Sort by activity to identify inactive members
2. Remove members who no longer need access
3. For GitLab Ultimate: Use **Group Settings** > **Audit events** to review permission changes

The Members page displays a table with columns for username, access level (Guest through Owner), source of membership (direct or inherited), and expiration date. Use the role dropdown filter at the top to quickly view all Owners or all members of a specific access level.

##### Use Teams for Permission Management [D.A.14]

GitLab uses **Groups** and **Subgroups** instead of teams:

1. Create a group for your project (e.g., `myproject`)
2. Create subgroups for different roles (e.g., `myproject/maintainers`, `myproject/security-team`)
3. Add members to subgroups rather than individual projects
4. Subgroup members inherit access to projects within the subgroup

**Navigation**: **Group Settings** > **Subgroups** > **New subgroup**

##### Enable SSO/SAML [D.A.15]

!!! info "Requires Premium tier or higher"

    SAML SSO is available on GitLab Premium and Ultimate plans.

**Navigation**: **Group Settings** > **SAML SSO**

Configure your identity provider (Okta, Azure AD, etc.) using GitLab's SAML integration. Once enabled, group members authenticate through your IdP.

##### Review and Rotate Deploy Keys and Tokens [D.A.16]

**Navigation**: Project > **Settings** > **Repository** > expand **Deploy keys**

And: Project > **Settings** > **Access tokens**

1. Review all deploy keys and remove unused ones
2. For deploy tokens, check expiration dates and rotate as needed
3. Use **project access tokens** (scoped tokens) instead of personal access tokens where possible
4. Set expiration dates on all tokens

##### Fine-Grained Access Tokens [D.A.17]

**Navigation**: Project > **Settings** > **Access tokens** > **Add new token**

GitLab project access tokens are scoped to a single project:

1. Set a descriptive **Token name**
2. Set an **Expiration date** (we recommend 90 days or less)
3. Select the minimum **Role** needed (Guest, Reporter, Developer, Maintainer)
4. Select only the **Scopes** required (e.g., `read_repository` for read-only access)

For group-level tokens: **Group Settings** > **Access tokens**

#### Security and Compliance Features [D.A.18–25]

GitLab integrates security scanning directly into the CI/CD pipeline using predefined CI templates. Unlike GitHub's settings-page approach, most GitLab security features are configured in `.gitlab-ci.yml`.

##### Dependency Scanning [D.A.18–19]

!!! info "Requires Ultimate tier for full Dependency Scanning"

    Basic vulnerability alerts are available on all tiers via CI templates. The full dependency scanning dashboard and auto-remediation require Ultimate.

Add dependency scanning to your `.gitlab-ci.yml`:

```yaml
include:
  - template: Security/Dependency-Scanning.gitlab-ci.yml
```

This automatically scans your project's dependencies for known vulnerabilities. Results appear in the **Security** tab > **Vulnerability report**.

For auto-remediation merge requests (similar to GitHub's Dependabot security updates), GitLab Ultimate provides automatic merge requests for vulnerable dependencies.

##### Secret Detection [D.A.20–21]

Add secret detection to your `.gitlab-ci.yml`:

```yaml
include:
  - template: Security/Secret-Detection.gitlab-ci.yml
```

**Pre-receive secret detection** (equivalent to GitHub's push protection):

!!! info "Requires Ultimate tier"

    Pre-receive secret detection is available on GitLab Ultimate.

**Navigation**: Project > **Settings** > **Security & Compliance** > **Secret detection**

Enable **Pre-receive secret detection** to block pushes containing detected secrets before they reach the repository.

##### Code Scanning / SAST [D.A.22]

Add SAST to your `.gitlab-ci.yml`:

```yaml
include:
  - template: Security/SAST.gitlab-ci.yml
```

GitLab automatically detects your project's languages and runs appropriate SAST analyzers. Results appear in merge request widgets and the vulnerability report.

For additional configuration:

```yaml
include:
  - template: Security/SAST.gitlab-ci.yml

variables:
  SAST_EXCLUDED_PATHS: "spec,test,tests"
  SEARCH_MAX_DEPTH: 4
```

##### Security Advisories [D.A.23]

GitLab does not have a built-in "private vulnerability reporting" form equivalent to GitHub's. Instead:

1. Direct researchers to email your security contact (documented in `SECURITY.md`)
2. Use **confidential issues** for tracking: when creating an issue, check **This issue is confidential** to restrict visibility to project members
3. For GitLab Ultimate: The **Security Dashboard** provides vulnerability tracking and management

##### Repository Visibility [D.A.24]

**Navigation**: Project > **Settings** > **General** > expand **Visibility, project features, permissions**

Set **Project visibility** to:

- **Public** for open source projects
- **Internal** for organization-internal projects (visible to all authenticated users)
- **Private** for restricted projects

##### Disable Unused Features [D.A.25]

**Navigation**: Project > **Settings** > **General** > expand **Visibility, project features, permissions**

Toggle off any features your project does not use:

- **Wiki**
- **Snippets**
- **Container Registry**
- **Package Registry**
- **Analytics**

---

### Documentation Requirements on GitLab

#### Security Documentation Setup [D.B.1–3]

##### Creating SECURITY.md [D.B.1]

Unlike GitHub, GitLab does not have a built-in SECURITY.md template. Create the file manually:

1. Navigate to your project's root directory
2. Create a new file named `SECURITY.md`
3. Follow the same content guidelines as described in the GitHub section (contact method, response timeline, disclosure policy, PGP key, scope)
4. Link to `SECURITY.md` from your project's README

GitLab does not automatically surface `SECURITY.md` in the project sidebar. Linking to it from your README is the primary way to make it discoverable.

##### Security Policy Discoverability [D.B.2]

Add a link to your security policy in your `README.md`:

```markdown
## Security

See SECURITY.md for our security policy and
vulnerability reporting instructions.
```

For GitLab Ultimate: You can also configure a **Security Policy** project that enforces security scanning requirements across multiple projects.

##### Supported Versions Documentation [D.B.3]

Include a supported versions table in your `SECURITY.md` (same format as the GitHub section).

#### General Documentation Files [D.B.4–9]

GitLab does not have a "Community Standards" page like GitHub. However, it recognizes standard files:

- **README.md** [D.B.4]: Displayed on the project's main page
- **Installation instructions** [D.B.5]: Include in README or a dedicated file
- **LICENSE** [D.B.6]: GitLab provides a license template when creating a new project. For existing projects, create a `LICENSE` file at the repository root. GitLab detects the license type and displays it in the project sidebar.
- **CONTRIBUTING.md** [D.B.7]: Create at the repository root. GitLab displays a link to this file when someone opens a new merge request.
- **CODE_OF_CONDUCT.md** [D.B.8]: Create at the repository root
- **CHANGELOG.md** [D.B.9]: Create at the repository root, or use GitLab Releases to document changes

#### Security-Specific Documentation [D.B.10–16]

**Process Guidance**: The same recommendations apply as in the GitHub section. Create documentation files for:

- **Threat model** [D.B.10]: `docs/security/THREAT-MODEL.md`
- **Security architecture** [D.B.11]: `docs/security/ARCHITECTURE.md`
- **Authentication/authorization model** [D.B.12]: Include in security architecture or standalone
- **Cryptography usage** [D.B.13]: `docs/security/CRYPTOGRAPHY.md`
- **Known limitations** [D.B.14]: Include in README or security docs
- **Hardening guide** [D.B.15]: `docs/HARDENING.md`
- **Dependency policy** [D.B.16]: `docs/security/DEPENDENCY-POLICY.md`

GitLab's **Wiki** feature can also host these documents if your project prefers wikis over in-repository documentation.

---

### Build and Release Security on GitLab

#### GitLab CI/CD Security [D.C.1–8]

##### CI/CD for All Builds [D.C.1]

Create a `.gitlab-ci.yml` file in your repository root:

```yaml
stages:
  - test
  - build
  - release

test:
  stage: test
  script:
    - make test

build:
  stage: build
  script:
    - make build
  artifacts:
    paths:
      - dist/
```

**Navigation**: Project > **Build** > **Pipelines** to view pipeline runs.

##### Pin CI/CD Component and Image Versions [D.C.2]

Pin Docker images by digest and use specific versions for CI/CD components:

```yaml
image: node:24.13.0-alpine@sha256:931d7d...  # Pinned by digest

test:
  image: python:3.12.0@sha256:abc123...  # Pinned by digest
  script:
    - python -m pytest
```

For GitLab CI/CD components:

```yaml
include:
  - component: gitlab.com/components/sast@1.0.0  # Pinned version
```

##### Minimize Build Dependencies [D.C.3]

Use minimal Docker images for CI jobs:

```yaml
build:
  image: alpine:3.19
  before_script:
    - apk add --no-cache build-base  # Only install what's needed
  script:
    - make build
```

Review `.gitlab-ci.yml` regularly to remove unused stages, scripts, and dependencies.

##### Ephemeral Build Environments [D.C.4]

GitLab SaaS runners (shared runners) are ephemeral by default. For self-managed runners, configure them to use Docker or Kubernetes executors with fresh containers for each job:

```toml
# GitLab Runner config.toml
[[runners]]
  executor = "docker"
  [runners.docker]
    privileged = false
    volumes = ["/cache"]
```

##### Isolate Build Environments [D.C.5]

Restrict CI/CD job permissions:

```yaml
build:
  variables:
    # Restrict the CI_JOB_TOKEN permissions
    GIT_STRATEGY: clone  # Fresh clone each time
  script:
    - make build
```

**Navigation**: Project > **Settings** > **CI/CD** > expand **Token Access** to restrict which projects can access your project's CI/CD job token.

For self-managed runners, use network policies to restrict outbound access during builds.

##### Pin Dependency Versions [D.C.6]

Use the same lockfile commands as described in the GitHub section:

```yaml
install:
  script:
    - npm ci                    # Node.js
    - uv sync --frozen          # Python
    - go mod verify             # Go
    - cargo build --locked      # Rust
```

##### Verify Dependency Integrity [D.C.7]

```yaml
verify:
  script:
    - npm ci                                      # Verifies package-lock.json hashes
    - pip install --require-hashes -r requirements.txt  # Python hash verification
    - go mod verify                                # Go checksum verification
```

##### Scan Dependencies Before Build [D.C.8]

```yaml
include:
  - template: Security/Dependency-Scanning.gitlab-ci.yml

dependency_scan:
  stage: test
  script:
    - npm audit --audit-level=high
  allow_failure: false  # Block pipeline on vulnerabilities
```

#### Build Process Configuration [D.C.9–14]

##### Document Build Process [D.C.9]

**Process Guidance**: Same as GitHub section — create `BUILD.md` documenting prerequisites, build commands, test commands, and local reproduction steps.

##### Compiler Security Flags [D.C.10]

**Process Guidance**: Same as GitHub section — configure security flags in your build system and document in `BUILD.md`.

```yaml
build:
  variables:
    CFLAGS: "-fstack-protector-strong -D_FORTIFY_SOURCE=2 -fPIE"
    LDFLAGS: "-pie -z relro -z now"
  script:
    - make build
```

##### SAST During Build [D.C.11]

```yaml
include:
  - template: Security/SAST.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml
```

GitLab SAST results appear in merge request widgets, allowing reviewers to see security findings before merging.

##### Security Tests [D.C.12]

```yaml
security_tests:
  stage: test
  script:
    - make test-security
  allow_failure: false
```

##### Generate SBOM During Build [D.C.13]

GitLab Ultimate includes built-in SBOM generation via dependency scanning. For all tiers, use CycloneDX tools:

```yaml
sbom:
  stage: build
  script:
    - npx @cyclonedx/cyclonedx-npm --output-file sbom.json
    # Or: cyclonedx-py, cyclonedx-gomod, etc.
  artifacts:
    paths:
      - sbom.json
```

##### Build from Verified Source [D.C.14]

GitLab CI/CD automatically checks out from the project repository. For additional verification:

```yaml
verify_source:
  script:
    - git verify-tag "$CI_COMMIT_TAG" || exit 1
  rules:
    - if: $CI_COMMIT_TAG
```

#### Release Signing and Provenance [D.C.15–21]

##### Sign Release Artifacts [D.C.15]

Use Cosign in your GitLab CI pipeline:

```yaml
sign:
  stage: release
  image: bitnami/cosign:2
  script:
    - cosign sign-blob --yes dist/artifact.tar.gz --bundle dist/artifact.tar.gz.bundle
  artifacts:
    paths:
      - dist/artifact.tar.gz.bundle
```

For container images:

```yaml
sign_image:
  stage: release
  image: bitnami/cosign:2
  script:
    - cosign sign --yes $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG
```

##### Publish Signing Keys/Certificates [D.C.16]

**Process Guidance**: Same as GitHub section — publish GPG keys or rely on Sigstore's keyless verification through the Rekor transparency log.

##### Sign Git Tags [D.C.17]

**Navigation**: Project > **Settings** > **Repository** > expand **Push rules**

Enable **Reject unsigned commits** to require signed commits and tags. Contributors must configure GPG or SSH signing locally.

##### Document Verification Process [D.C.18]

**Process Guidance**: Same as GitHub section — include verification instructions in release notes or `VERIFICATION.md`.

##### Sigstore for Keyless Signing [D.C.19]

GitLab CI supports OIDC tokens for Sigstore keyless signing:

```yaml
sign:
  id_tokens:
    SIGSTORE_ID_TOKEN:
      aud: sigstore
  script:
    - cosign sign-blob --yes --identity-token=$SIGSTORE_ID_TOKEN dist/artifact.tar.gz
```

##### Generate Provenance Attestations [D.C.20]

Use SLSA provenance generation in your pipeline:

```yaml
provenance:
  stage: release
  script:
    - slsa-provenance generate --artifact dist/artifact.tar.gz --output provenance.intoto.jsonl
  artifacts:
    paths:
      - provenance.intoto.jsonl
```

##### Publish Attestations with Releases [D.C.21]

Include attestations as release assets (see Release Process below).

#### Release Process [D.C.22–28]

##### Protected Tags [D.C.22]

**Navigation**: Project > **Settings** > **Repository** > expand **Protected tags**

1. Enter a tag pattern (e.g., `v*`)
2. Set **Allowed to create** to **Maintainers** or specific users
3. Click **Protect**

This prevents unauthorized users from creating release tags.

##### Multiple Approvals for Releases [D.C.23]

Use **protected environments** with approval rules:

**Navigation**: Project > **Settings** > **CI/CD** > expand **Protected environments** (or **Deployments**)

1. Add an environment named `production`
2. Set **Required approvals** to the number of required approvers
3. Add the approvers

In your `.gitlab-ci.yml`:

```yaml
release:
  stage: release
  environment:
    name: production
  script:
    - make release
  rules:
    - if: $CI_COMMIT_TAG
```

The protected environment settings page shows the environment name, a list of users or groups authorized to deploy, and the required number of approvals. When a pipeline job targets this environment, it pauses and requires the specified number of approvals before proceeding.

##### Automate Release Process [D.C.24]

Use GitLab's `release-cli` in your pipeline:

```yaml
release:
  stage: release
  image: registry.gitlab.com/gitlab-org/release-cli:latest
  script:
    - echo "Creating release"
  release:
    tag_name: $CI_COMMIT_TAG
    name: "Release $CI_COMMIT_TAG"
    description: "Release notes for $CI_COMMIT_TAG"
    assets:
      links:
        - name: "Binary"
          url: "https://example.com/dist/artifact.tar.gz"
        - name: "SBOM"
          url: "https://example.com/dist/sbom.json"
  rules:
    - if: $CI_COMMIT_TAG
```

##### Publish to Official Registries [D.C.25]

Configure registry publishing in your pipeline:

```yaml
publish_npm:
  stage: release
  script:
    - npm publish
  rules:
    - if: $CI_COMMIT_TAG

publish_pypi:
  stage: release
  script:
    - python -m build
    - twine upload dist/*
  rules:
    - if: $CI_COMMIT_TAG
```

Use GitLab CI/CD variables (**Settings** > **CI/CD** > **Variables**) to store registry tokens securely. Mark them as **Protected** (only available in pipelines on protected branches/tags) and **Masked** (hidden in job logs).

##### Trusted Publishing [D.C.26]

GitLab supports OIDC tokens for trusted publishing:

```yaml
publish_pypi:
  stage: release
  id_tokens:
    PYPI_ID_TOKEN:
      aud: pypi
  script:
    - python -m build
    - twine upload --repository-url https://upload.pypi.org/legacy/ dist/*
```

Configure the OIDC trust on the registry side (e.g., PyPI trusted publishers).

##### Include SBOM with Release [D.C.27]

Attach the SBOM as a release asset in your release job (see [D.C.24]).

##### Announce Releases Through Official Channels [D.C.28]

**Process Guidance**: Use GitLab Releases as your primary announcement channel. Configure release notes in your CI/CD pipeline. Additionally, post to mailing lists, blogs, or social media from verified accounts.

---

### Vulnerability Management on GitLab

#### Vulnerability Reporting [D.D.1–7]

##### Private Reporting Channel [D.D.1]

GitLab does not have a built-in "private vulnerability reporting" form like GitHub. Set up private reporting by:

1. **Confidential issues**: Instruct reporters to create a confidential issue (check **This issue is confidential** when creating)
2. **Email**: Provide a security email address in your `SECURITY.md`
3. **External platforms**: Link to a bug bounty platform if applicable

Document the preferred reporting method in `SECURITY.md`.

##### Security Contacts and Timelines [D.D.2–7]

**Process Guidance**: Same as GitHub section — document security contacts [D.D.2], acknowledgment timeline [D.D.3], assessment timeline [D.D.4], fix timeline [D.D.5], disclosure timeline [D.D.6], and safe harbor statement [D.D.7] in your `SECURITY.md`.

#### Security Dashboard and Advisory Management [D.D.8–19]

##### Vulnerability Report

!!! info "Requires Ultimate tier"

    The full Vulnerability Report and Security Dashboard are available on GitLab Ultimate.

**Navigation**: Project > **Security & Compliance** > **Vulnerability report**

The Vulnerability Report displays a filterable table of all findings from your security scanners (SAST, Dependency Scanning, Container Scanning, Secret Detection). Each row shows the vulnerability name, severity (Critical, High, Medium, Low), scanner that detected it, current status, and the date detected. Filter controls at the top let you narrow results by severity, scanner, status, and project. Use this dashboard to:

1. **Triage** [D.D.8]: Review each vulnerability, assess severity, and set status (Detected, Confirmed, Dismissed, Resolved)
2. **Severity rating** [D.D.9]: GitLab assigns severity based on the scanner's findings; you can adjust manually
3. **Create issue** [D.D.10]: Click **Create issue** on a vulnerability to create a confidential issue for developing a fix
4. **Test fixes** [D.D.11]: Develop fixes in a merge request; GitLab re-runs security scans to verify the fix

##### Advisory Management [D.D.12–19]

GitLab does not have a built-in security advisory publication mechanism equivalent to GitHub Security Advisories. For advisory management:

1. **Request a CVE** [D.D.14]: Submit CVE requests through MITRE's web form or your CNA
2. **Publish advisories** [D.D.15]: Create a security advisory as a GitLab Release note, a confidential-then-public issue, or a dedicated page in your documentation
3. **Credit reporters** [D.D.16]: Acknowledge researchers in your advisory text
4. **Upgrade guidance** [D.D.17]: Include in release notes and advisory
5. **Workarounds** [D.D.18]: Document in the advisory
6. **Downstream coordination** [D.D.19]: Use confidential issues for private coordination

**Backporting policy** [D.D.12] and **communication plan** [D.D.13]: Document in `SECURITY.md`.

#### Dependency and Container Scanning [D.D.20–24]

##### Dependency Monitoring [D.D.20]

For GitLab Ultimate: Dependency Scanning runs automatically when included in `.gitlab-ci.yml`:

```yaml
include:
  - template: Security/Dependency-Scanning.gitlab-ci.yml
```

For all tiers: Use `npm audit`, `pip-audit`, `cargo audit`, or similar tools in your CI pipeline:

```yaml
dependency_check:
  stage: test
  script:
    - npm audit --audit-level=high
  allow_failure: false
```

##### Regular Dependency Updates [D.D.21]

GitLab does not have a built-in Dependabot equivalent. Options:

**Renovate Bot**: Self-hosted or use the Mend Renovate GitLab app. Configure with `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["config:recommended"]
}
```

**Scheduled pipelines**: Create a scheduled pipeline that checks for updates:

**Navigation**: Project > **Build** > **Pipeline schedules** > **New schedule**

##### Periodic Security Assessments [D.D.22]

**Process Guidance**: Schedule periodic reviews using GitLab's Security Dashboard (Ultimate) or the self-assessment template in Appendix D, Section F.

##### Track Security Debt [D.D.23]

Use GitLab issues with a `security-debt` label. Create a **Security Debt** issue board:

**Navigation**: Project > **Plan** > **Issue boards** > **Create new board**

##### Review Past Vulnerabilities [D.D.24]

**Process Guidance**: Same as GitHub section — conduct post-incident reviews and document in `docs/security/incidents/`.

---

### Community and Governance on GitLab

#### GitLab Governance Tools

##### CODEOWNERS File

Create a `CODEOWNERS` file at the repository root (same format as GitHub):

```
* @group/maintainers
/src/auth/ @group/security-team
/.gitlab-ci.yml @group/platform-team
```

When combined with approval rules requiring code owner approval [D.A.3], this ensures the right reviewers are involved.

##### Groups, Subgroups, and Roles

GitLab uses a hierarchical group structure:

**Navigation**: **Group Settings** > **Members** or **Subgroups**

| Role | GitLab Access Level | Typical Use |
|---|---|---|
| Guest | Guest | View project, create issues |
| Reporter | Reporter | View code, create issues, create merge requests from forks |
| Developer | Developer | Push code, create branches |
| Maintainer | Maintainer | Manage project settings, merge to protected branches |
| Owner | Owner | Full administrative control |

##### Compliance Frameworks

!!! info "Requires Premium tier or higher"

    Compliance frameworks are available on GitLab Premium and Ultimate plans.

**Navigation**: **Group Settings** > **General** > expand **Compliance frameworks**

Create compliance frameworks (e.g., "Security-Critical Project") and assign them to projects. Compliance frameworks can enforce:

- Required CI/CD pipeline configurations
- Merge request approval requirements
- Separation of duties

##### Audit Events

**Navigation**: **Group Settings** > **Audit events** (or Project > **Settings** > **Audit events**)

!!! info "Requires Premium tier or higher"

    Audit events with full detail are available on GitLab Premium and Ultimate.

Review audit events for permission changes, project settings modifications, and merge request approvals. Use for periodic access reviews [D.A.13].

#### Process and Policy Guidance [D.E.1–21]

> **Note**: The following items from Appendix D describe organizational practices rather than platform settings. We provide recommended approaches for implementing these practices with GitLab as your project's home.

##### Governance Model [D.E.1]

Create a `GOVERNANCE.md` file documenting your decision-making process. Same content as described in the GitHub section. See Appendix E for templates.

##### Maintainer Roles [D.E.2]

Document roles in `GOVERNANCE.md`. Map each role to GitLab access levels:

| Role | GitLab Access Level | Responsibilities |
|---|---|---|
| Contributor | Developer | Submit merge requests |
| Committer | Developer | Push to non-protected branches |
| Maintainer | Maintainer | Merge to protected branches, manage settings |
| Admin | Owner | Group/project administration |

##### Succession Plan [D.E.3]

**Process Guidance**: Same as GitHub section — document in `GOVERNANCE.md`.

##### Multiple Active Maintainers [D.E.4]

**Navigation**: Project > **Analyze** > **Contributor analytics** (or **Repository** > **Contributors**)

Monitor contributor activity to ensure the project does not depend on a single person.

##### Foundation or Organizational Backing [D.E.5]

**Process Guidance**: Same as GitHub section.

##### Funding Model [D.E.6]

**Process Guidance**: Document funding in `README.md` or `GOVERNANCE.md`. GitLab does not have a built-in sponsorship feature like GitHub Sponsors; link to external funding platforms (Open Collective, Patreon, etc.).

##### Contributor Verification [D.E.7]

Configure merge request approval rules to require reviews for all contributions. GitLab shows a first-time contributor badge on merge requests from new contributors.

##### CLA or DCO Requirement [D.E.8]

Configure a DCO check in your `.gitlab-ci.yml`:

```yaml
dco_check:
  stage: test
  script:
    - |
      for sha in $(git log --format='%H' origin/main..HEAD); do
        if ! git log -1 --format='%B' "$sha" | grep -q 'Signed-off-by:'; then
          echo "Commit $sha is missing Signed-off-by line"
          exit 1
        fi
      done
  rules:
    - if: $CI_MERGE_REQUEST_IID
```

Or use a dedicated DCO enforcement tool.

##### New Contributor Review Requirements [D.E.9]

**Process Guidance**: Same as GitHub section — document in `CONTRIBUTING.md`.

##### Commit Access Progression [D.E.10]

**Process Guidance**: Document the path from contributor to maintainer in `GOVERNANCE.md`. Map to GitLab access levels (Developer → Maintainer → Owner).

##### Periodic Access Review [D.E.11]

Establish a quarterly process using the group/project members page (see [D.A.13]).

##### Security in Communications [D.E.12]

GitLab does not have a Discussions feature like GitHub. Use:

- Project issues with a `security` label for non-sensitive discussions
- Confidential issues for sensitive topics
- Project wiki for persistent security documentation

##### Security Champion [D.E.13]

**Process Guidance**: Same as GitHub section — document in `SECURITY.md` and `GOVERNANCE.md`.

##### Security Training [D.E.14]

**Process Guidance**: Same as GitHub section.

##### Incident Response Plan [D.E.15]

**Process Guidance**: Same as GitHub section — create `docs/security/INCIDENT-RESPONSE.md`. See Appendix E for templates.

GitLab also offers an **Incident Management** feature (Project > **Monitor** > **Incidents**) for tracking active incidents.

##### Post-Incident Review [D.E.16]

**Process Guidance**: Same as GitHub section.

##### Public Issue Tracker [D.E.17]

GitLab issues are visible to everyone for public projects. Use confidential issues for security-sensitive reports.

##### Public Roadmap [D.E.18]

Use **GitLab Milestones** and **Issue Boards** to create a public roadmap:

**Navigation**: Project > **Plan** > **Milestones** or **Issue boards**

##### Meeting Notes [D.E.19]

**Process Guidance**: Publish meeting notes in the project wiki or repository (e.g., `docs/meetings/`).

##### Security Improvements Communication [D.E.20]

Include security improvements in release notes and `CHANGELOG.md`. Same guidance as GitHub section.

##### Annual Security Report [D.E.21]

**Process Guidance**: Same as GitHub section.

#### GitLab Configuration Summary Checklist

Use this checklist to verify you have completed all platform-specific configurations:

- [ ] Protected branches configured with merge request requirements [D.A.1–10]
- [ ] 2FA required at group level [D.A.11]
- [ ] Group/subgroup-based access management configured [D.A.14]
- [ ] Project access tokens with scoped permissions [D.A.17]
- [ ] Dependency Scanning included in CI pipeline [D.A.18–19]
- [ ] Secret Detection and pre-receive detection enabled [D.A.20–21]
- [ ] SAST included in CI pipeline [D.A.22]
- [ ] SECURITY.md created and linked from README [D.B.1–2]
- [ ] Standard documentation files present (README, LICENSE, CONTRIBUTING) [D.B.4–8]
- [ ] CI/CD images pinned by digest [D.C.2]
- [ ] CI/CD job token permissions restricted [D.C.5]
- [ ] SBOM generation integrated into pipeline [D.C.13]
- [ ] Release signing with Cosign configured [D.C.15, D.C.19]
- [ ] Protected tags configured [D.C.22]
- [ ] Protected environment with required approvals [D.C.23]
- [ ] Renovate or scheduled update pipeline configured [D.D.21]
- [ ] CODEOWNERS file configured [D.A.3]
- [ ] Governance documentation in place (GOVERNANCE.md) [D.E.1]
- [ ] Incident response plan documented [D.E.15]

---

### Platform Comparison Quick Reference

This table maps each Appendix D section to the equivalent configuration location on each platform. Use it to quickly find the right settings when working across platforms.

| Appendix D Item | GitHub | GitLab |
|---|---|---|
| **Repository Security Settings** | | |
| Branch protection [D.A.1–10] | Settings > Branches > Branch protection rules | Settings > Repository > Protected branches |
| Merge/PR reviews [D.A.2] | Branch protection: Require PR reviews | Settings > Merge requests > Approvals |
| Code owners [D.A.3] | `.github/CODEOWNERS` + branch protection | `CODEOWNERS` + approval rules |
| Signed commits [D.A.7] | Branch protection: Require signed commits | Settings > Repository > Push rules |
| 2FA enforcement [D.A.11] | Organization > Authentication security | Group > General > Permissions |
| Team permissions [D.A.14] | Organization > Teams | Groups and Subgroups |
| SSO/SAML [D.A.15] | Organization > Authentication security (Enterprise) | Group > SAML SSO (Premium+) |
| Dependabot / Dep scanning [D.A.18–19] | Settings > Code security: Dependabot | CI template: Dependency-Scanning.gitlab-ci.yml |
| Secret scanning [D.A.20–21] | Settings > Code security: Secret scanning | CI template: Secret-Detection.gitlab-ci.yml |
| Code scanning / SAST [D.A.22] | Settings > Code security: Code scanning | CI template: SAST.gitlab-ci.yml |
| Private vuln reporting [D.A.23] | Settings > Code security: Private reporting | Confidential issues + SECURITY.md email |
| **Documentation** | | |
| SECURITY.md [D.B.1] | Security tab > Security policy > Start setup | Manual file creation |
| Community health files [D.B.4–9] | Insights > Community Standards | Manual file creation |
| **Build and Release** | | |
| CI/CD configuration | `.github/workflows/*.yml` | `.gitlab-ci.yml` |
| Action/component pinning [D.C.2] | Pin by SHA: `uses: action@sha` | Pin image by digest; pin components by version |
| Workflow permissions [D.C.5] | `permissions:` key in workflow YAML | CI/CD > Token Access settings |
| SBOM generation [D.C.13] | `anchore/sbom-action` or `actions/attest` | CycloneDX tools or built-in (Ultimate) |
| Release signing [D.C.15] | Sigstore Cosign in Actions | Sigstore Cosign in CI pipeline |
| Provenance [D.C.20] | `slsa-github-generator` or `actions/attest` | SLSA tools in CI pipeline |
| Protected tags [D.C.22] | Settings > Tags > Tag protection rules | Settings > Repository > Protected tags |
| Release approvals [D.C.23] | Environments with required reviewers | Protected environments with approvals |
| Trusted publishing [D.C.26] | OIDC via `id-token: write` permission | OIDC via `id_tokens` CI keyword |
| **Vulnerability Management** | | |
| Private reporting [D.D.1] | Settings > Code security: Private reporting | Confidential issues + email |
| Security advisories [D.D.15] | Security tab > Advisories | Release notes + confidential issues |
| CVE requests [D.D.14] | Request CVE via advisory (GitHub is a CNA) | Request via MITRE or your CNA |
| Dependency monitoring [D.D.20] | Dependabot alerts dashboard | Vulnerability Report (Ultimate) or CI scanning |
| Automated updates [D.D.21] | Dependabot version updates | Renovate Bot or scheduled pipelines |
| **Governance** | | |
| Audit log [D.A.13] | Organization > Audit log | Group > Audit events (Premium+) |
| Compliance frameworks | N/A | Group > Compliance frameworks (Premium+) |
| Sponsorship/funding [D.E.6] | `.github/FUNDING.yml` + GitHub Sponsors | External platforms (Open Collective, etc.) |

---

### Additional Resources

**GitHub Documentation**:

- GitHub Security Hardening Guide: https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions[^gh-actions-hardening]
- GitHub Code Security: https://docs.github.com/en/code-security[^gh-code-security]
- GitHub Repository Security Settings: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-security-and-analysis-settings-for-your-repository[^gh-repo-security]

**GitLab Documentation**:

- GitLab Security Configuration: https://docs.gitlab.com/ee/user/application_security/[^gl-security]
- GitLab CI/CD Security: https://docs.gitlab.com/ee/ci/pipelines/settings.html[^gl-ci-security]
- GitLab Protected Branches: https://docs.gitlab.com/ee/user/project/protected_branches.html[^gl-protected-branches]

**Cross-Platform Tools**:

- Sigstore: https://docs.sigstore.dev/[^sigstore-xplat]
- SLSA Framework: https://slsa.dev/[^slsa-xplat]
- OpenSSF Scorecard: https://securityscorecards.dev/[^scorecard-xplat]
- Renovate Bot: https://docs.renovatebot.com/[^renovate]

[^gh-signing-commits]: GitHub, "Signing commits," https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits

[^stepsecurity]: StepSecurity, "Secure Workflows," https://app.stepsecurity.io/

[^pinact]: Suzuki Shunsuke, "pinact," https://github.com/suzuki-shunsuke/pinact

[^sigstore-docs]: Sigstore, "Sigstore Documentation," https://docs.sigstore.dev/

[^gh-actions-hardening]: GitHub, "Security hardening for GitHub Actions," https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions

[^gh-code-security]: GitHub, "Code security," https://docs.github.com/en/code-security

[^gh-repo-security]: GitHub, "Managing security and analysis settings for your repository," https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-security-and-analysis-settings-for-your-repository

[^gl-security]: GitLab, "Application security," https://docs.gitlab.com/ee/user/application_security/

[^gl-ci-security]: GitLab, "CI/CD pipeline settings," https://docs.gitlab.com/ee/ci/pipelines/settings.html

[^gl-protected-branches]: GitLab, "Protected branches," https://docs.gitlab.com/ee/user/project/protected_branches.html

[^sigstore-xplat]: Sigstore, "Sigstore Documentation," https://docs.sigstore.dev/

[^slsa-xplat]: SLSA, "Supply-chain Levels for Software Artifacts," https://slsa.dev/

[^scorecard-xplat]: Open Source Security Foundation, "Scorecard," https://securityscorecards.dev/

[^renovate]: Mend, "Renovate Documentation," https://docs.renovatebot.com/
