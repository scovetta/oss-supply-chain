---
title: "Git-Specific Attack Vectors"
description: "Discover how Git features like hooks, submodules, and symbolic references can be weaponized in supply chain attacks."
icon: "lucide/git-branch"
---

# 8.5 Git-Specific Attack Vectors

Git has become the dominant version control system, underlying virtually all modern software development. Its distributed architecture, powerful features, and integration with platforms like GitHub and GitLab have made it essential infrastructure. But Git's flexibility also creates attack surface. Features designed for legitimate workflows—hooks, submodules, symbolic references—can be weaponized. Understanding Git-specific vulnerabilities helps practitioners harden their environments and recognize suspicious repository configurations.

## The `.git` Directory as Attack Target

Every Git repository contains a `.git` directory storing configuration, hooks, object database, and references. This directory is both essential and sensitive:

- **Configuration files** (`.git/config`) can specify arbitrary commands to run on certain operations
- **Hooks** are executable scripts triggered by Git operations
- **References** point to commit objects and can be manipulated
- **Object database** stores all content and history

Attackers who can modify the `.git` directory—through direct access, malicious commits, or exploitation of other vulnerabilities—gain significant control over the repository and potentially the systems interacting with it.

Several CVEs have targeted `.git` directory exposure:

**[CVE-2018-11235][cve-2018-11235]** allowed arbitrary code execution through crafted `.gitmodules` files where submodule names contained path traversal sequences like `../`. This allowed attackers to place hooks in unexpected locations that would execute during clone operations.

**Defense**: Web servers should block access to `.git` directories. Never expose repositories directly through web servers without explicit `.git` exclusion. Regularly audit deployed applications for `.git` exposure.

## Malicious Git Hooks

!!! danger "Git Hooks Execute Automatically"

    Hooks are scripts triggered by Git operations (commit, checkout, merge, push). An attacker who can modify committed hook scripts or git configuration gains code execution on any developer who runs setup or any CI system processing the repository.

**Git hooks** are scripts that execute automatically during Git operations. Standard hooks include:

- `pre-commit`: Runs before committing
- `post-checkout`: Runs after checking out a branch
- `post-merge`: Runs after merging
- `pre-push`: Runs before pushing

Hooks reside in `.git/hooks/` and are not transferred during clone or fetch operations—this is a deliberate security design. However, several attack patterns exploit hooks:

**Hook Installation Through Other Means:**

Projects sometimes include hook scripts in the repository (often in a `hooks/` or `.githooks/` directory) with setup scripts that symlink them into `.git/hooks/`. If an attacker can modify these committed scripts, they achieve code execution when contributors run the setup.

**Example pattern**:

```shell
# setup-hooks.sh - legitimate but exploitable pattern
ln -sf ../../hooks/pre-commit .git/hooks/pre-commit
```

An attacker who modifies `hooks/pre-commit` in the repository gains code execution on any developer who re-runs setup.

**Core.hooksPath Exploitation:**

Git's `core.hooksPath` configuration allows specifying an alternative hooks directory. An attacker who can modify `.git/config` can point hooks to a directory containing malicious scripts:

```ini
[core]
    hooksPath = /path/to/malicious/hooks
```

This has implications for shared development environments or CI/CD systems where configuration might be persisted.

**Connection to CI/CD:**

CI/CD systems often run Git operations that trigger hooks. A `post-checkout` hook in a CI environment executes with the CI runner's privileges—potentially accessing secrets, deployment credentials, and other sensitive resources.

**Defense:**

- Audit any scripts that install hooks from repository content
- In CI/CD, consider running with `core.hooksPath` set to an empty directory
- Monitor for unexpected configuration changes

## Submodule Hijacking and Redirection

**Git submodules** embed one repository within another, specified in `.gitmodules` file and `.git/config`. Submodules reference external repositories by URL—creating dependency on external resources.

**Submodule URL Redirection:**

If an attacker controls the URL a submodule points to, they control what code is fetched:

- A domain expires and is re-registered by an attacker
- A repository is deleted and recreated with malicious content
- URL is modified to point to a different repository

When developers or CI systems update submodules, they fetch attacker-controlled content.

**[CVE-2018-17456][cve-2018-17456]** demonstrated remote code execution through malicious submodule URLs. A URL could be crafted to execute commands during `git clone --recurse-submodules`:

```
-u./payload
```

This exploited how Git parsed certain URL formats, allowing command injection through option injection attacks where `-u` was interpreted as a git clone option.

**Submodule Commit Mismatch:**

Submodule references include both the URL and a specific commit hash. Attackers who control the referenced repository can:

- Remove the referenced commit or make the submodule unavailable, disrupting builds
- More practically, compromise the repository so that future submodule pointer updates reference legitimate-looking commits containing malicious code

**Defense**:

- Pin submodules to specific commit hashes (default behavior, but verify)
- Audit `.gitmodules` for suspicious URLs
- Consider vendoring dependencies instead of using submodules for critical code
- Use `git config --global protocol.file.allow always` carefully; restrict protocol handlers

## Case Sensitivity Exploits

Git was designed on Linux, where filesystems are case-sensitive. macOS and Windows use case-insensitive filesystems by default, creating exploitable inconsistencies.

**The Classic Attack:**

A repository contains two files that differ only in case:

- `Makefile` (legitimate)
- `MAKEFILE` (malicious)

On Linux (case-sensitive), these are distinct files. On macOS or Windows (case-insensitive), they collide—and which file "wins" during checkout can be exploited.

**[CVE-2021-21300][cve-2021-21300]** exploited this pattern for arbitrary code execution on macOS and Windows. A malicious repository could include case-colliding files that, when checked out on case-insensitive systems, would overwrite sensitive files or place executables in unexpected locations.

**Symlink and Case Collision Combinations:**

More sophisticated attacks combine case collisions with symbolic links:

1. Repository contains `dir/file` as a regular file
2. Repository also contains `DIR` as a symlink to a sensitive location
3. On case-insensitive systems, checking out `dir/file` follows the `DIR` symlink

This could allow writing to locations outside the repository.

**`.git` Directory Collision:**

Particularly dangerous is case collision with the `.git` directory:

- A file or directory named `.GIT/config` might not be recognized as part of the Git metadata on Linux
- On Windows or macOS, it could be treated as equivalent to `.git/config`
- Malicious configuration could be injected through this mismatch

**Defense**:

- Keep Git updated; recent versions include case-collision detection
- Use `git config core.protectHFS true` on macOS
- Use `git config core.protectNTFS true` on Windows
- Consider CI validation that rejects repositories with case-colliding paths

## Signed Commits: Verification Gaps and Limitations

**Commit signing** uses GPG, SSH, or S/MIME keys to cryptographically bind committer identity to commits. While valuable, signing has limitations often misunderstood:

**What Signing Proves:**

- The commit content (tree, parent, message) was signed by the key holder
- The content has not been modified since signing

**What Signing Does Not Prove:**

- That the key holder is who they claim to be (key verification is separate)
- That the code is safe, reviewed, or high quality
- That the committer had authorization to make the commit
- That automated processes validated the content

**Verification Configuration Gaps:**

Most Git operations do not require signature verification by default:

```shell
# Pulling unsigned commits is allowed by default
git pull  # No signature requirement

# Explicit verification required
git verify-commit HEAD
```

Organizations may assume signing is enforced when it is not actually verified during critical operations.

**Signature Forgery Concerns:**

Git identifies commit authors through configuration, not authentication:

```shell
git config user.name "Linus Torvalds"
git config user.email "torvalds@linux-foundation.org"
```

Without signature verification, anyone can create commits claiming to be from any identity. Platforms like GitHub mark verified signatures, but users must actively check.

**Key Management Issues:**

- Developer keys may be stored insecurely
- Key revocation may not propagate to all verifiers
- Old commits remain signed by compromised keys
- Organizations may lack key verification infrastructure

**Defense**:

- Implement signature verification for releases and merges to protected branches
- Use GitHub's vigilant mode to flag unsigned commits
- Establish key verification procedures for maintainers
- Consider SSH signing (simpler key management than GPG)

## Git Protocol Vulnerabilities

Git communicates using several protocols, each with distinct security properties:

**Protocol Options:**

- **HTTPS**: Encrypted, server authenticated, widely supported
- **SSH**: Encrypted, mutual authentication possible, requires key management
- **Git Protocol** (`git://`): Unauthenticated, unencrypted, fast but insecure
- **File Protocol**: Local access, follows filesystem permissions

**[CVE-2022-41903][cve-2022-41903] and [CVE-2022-23521][cve-2022-23521]** demonstrated critical vulnerabilities in Git's handling of repository data. The former affected commit formatting, while the latter affected `.gitattributes` parsing; both could lead to arbitrary code execution when affected Git operations processed malicious repository content.

**The Danger of `git://` Protocol:**

The unauthenticated `git://` protocol allows man-in-the-middle attacks:

- Network attackers can modify content in transit
- DNS hijacking can redirect to malicious servers
- Git object hashes validate internal consistency, but the protocol does not authenticate that the server is the one you intended to reach

Despite its risks, some repositories still offer `git://` URLs.

**Defense**:

- Use HTTPS or SSH exclusively; avoid `git://` protocol
- Configure `git config --global protocol.file.allow user` to require explicit consent for file protocol
- Keep Git client updated; repository parser vulnerabilities are regularly discovered
- Consider `git config --global url."https://".insteadOf git://` to rewrite URLs

## Repository History Manipulation

Git's distributed nature means history can be rewritten—intentionally or maliciously:

**Force Push Attacks:**

If an attacker gains push access, force-pushing can:

- Remove commits that reveal attack preparation
- Replace commits with malicious versions having the same message
- Obscure the timeline of when changes were introduced

Protected branches on GitHub/GitLab mitigate this but must be configured.

**Phantom Commits:**

Commits can exist in a repository without being reachable from any branch. These "phantom" commits:

- Remain in the object database until garbage collection
- Can contain malicious code not visible in normal browsing
- May be fetched by systems that request specific hashes

Attackers could push commits, create a reference to a malicious commit hash, then delete the visible reference—leaving the commit accessible but hidden.

**Shallow Clone Limitations:**

Shallow clones (`git clone --depth 1`) fetch limited history. This:

- May miss vulnerability-introducing commits during historical analysis
- Can be exploited if security scanning only examines recent history
- Limits forensic analysis after incidents

**Defense**:

- Enable branch protection on critical branches
- Require signed commits for protected branches
- Implement audit logging for force pushes and reference deletions
- Perform security analysis on full repository history, not shallow clones

## Clone-Time Code Execution Risks

The act of cloning a repository can execute code through several mechanisms:

**Submodule Initialization:**

```shell
git clone --recurse-submodules <malicious-repo>
```

This fetches and checks out submodules, potentially triggering:

- Vulnerabilities in recursive clone or submodule handling
- Case-collision exploits
- Path traversal through submodule configuration

**Large File Storage (LFS) Smudge Filters:**

Git LFS uses **smudge filters** that process files after checkout. A malicious `.gitattributes` file can request a named filter:

```
*.bin filter=malicious
```

If the `malicious` filter is defined in configuration, it executes during checkout.

**Configuration Trust Boundaries:**

Git does not normally trust repository content to set arbitrary `.git/config` directives. The risk appears when surrounding tooling copies configuration from the repository, uses templates, or includes configuration paths that point into the worktree:

```ini
[include]
    path = ../malicious-config
```

That pattern can import attacker-controlled settings if the include path resolves to repository content.

**Defense**:

- Clone untrusted repositories with `--no-checkout` initially
- Audit `.gitmodules` and `.gitattributes` before full checkout
- Avoid `--recurse-submodules` for untrusted repositories
- Run initial analysis in isolated environments

## Hardening Recommendations

**For Developers:**

1. Keep Git updated to receive security fixes
2. Use HTTPS or SSH; never use `git://` protocol
3. Enable `core.protectHFS` and `core.protectNTFS` for cross-platform work
4. Verify signatures on important commits and tags
5. Audit repositories before cloning with `--recurse-submodules`

**For Organizations:**

1. Implement branch protection requiring signed commits
2. Configure push rules rejecting suspicious patterns
3. Audit `.gitmodules` changes in code review
4. Use shallow clones carefully; maintain full mirrors for security analysis
5. Enable audit logging for repository administration

**For CI/CD Systems:**

1. Clone with minimal depth when full history isn't needed
2. Disable hooks during CI clone operations
3. Validate repository structure before running build commands
4. Isolate clone operations in containers with limited capabilities
5. Consider using bare clones and explicit checkout for maximum control

Git's power comes from flexibility that also enables attacks. The version control system that tracks every change becomes an attack vector when those changes include malicious configuration, exploit cross-platform differences, or leverage powerful features like hooks and submodules. Defensive configuration, current versions, and careful handling of untrusted repositories mitigate these Git-specific risks.

[cve-2018-11235]: https://nvd.nist.gov/vuln/detail/CVE-2018-11235
[cve-2018-17456]: https://nvd.nist.gov/vuln/detail/CVE-2018-17456
[cve-2021-21300]: https://nvd.nist.gov/vuln/detail/CVE-2021-21300
[cve-2022-23521]: https://nvd.nist.gov/vuln/detail/CVE-2022-23521
[cve-2022-41903]: https://nvd.nist.gov/vuln/detail/CVE-2022-41903
