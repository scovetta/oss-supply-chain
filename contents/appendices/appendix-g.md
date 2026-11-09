## Appendix G: Ecosystem-Specific Security Guides

This appendix provides quick-reference security guides for major programming language ecosystems. Each section covers lockfile management, security scanning, signing and verification, registry features, and common vulnerabilities specific to that ecosystem.

---

### JavaScript/npm Security Best Practices

The JavaScript ecosystem, centered on npm (Node Package Manager), is the largest package ecosystem, with more than 4 million packages. Its size and the tendency toward many small dependencies create unique supply chain challenges.

#### Lockfile and Pinning

npm uses `package-lock.json` to lock dependency versions. Always commit this file to version control.

```shell
# Generate or update lockfile
npm install

# Install from lockfile only (CI/CD)
npm ci

# Audit lockfile for known vulnerabilities
npm audit

# Update a specific package
npm update lodash
```

**Best Practices**:

- Use `npm ci` in CI/CD pipelines instead of `npm install`
- Set `save-exact=true` in `.npmrc` to pin exact versions by default
- Review lockfile changes in pull requests: large diffs may indicate supply chain attacks

```ini
# .npmrc
save-exact=true
audit=true
```

#### Security Scanning Tools

| Tool | Purpose | Command/Integration |
|------|---------|---------------------|
| npm audit | Built-in vulnerability scanner | `npm audit` |
| npm audit fix | Auto-fix vulnerabilities | `npm audit fix` |
| Snyk | Comprehensive scanning | `snyk test` |
| Socket | Supply chain threat detection | GitHub App integration |
| npm-check-updates | Find outdated packages | `ncu` |

```shell
# Run npm audit with JSON output for CI
npm audit --json > audit-results.json

# Fix vulnerabilities automatically (use with caution)
npm audit fix

# Force fixes that may include breaking changes
npm audit fix --force
```

#### Signing and Verification

npm supports package provenance through Sigstore integration since 2023.

```shell
# Publish with provenance (requires npm 9.5.0+)
npm publish --provenance

# Check if a package has provenance
npm view <package> --json | jq '.dist.attestations'
```

**Verification**: Look for the "Provenance" badge on npmjs.com package pages, indicating the package was built from a verified source repository.

#### Registry Security Features

- **Two-factor authentication**: Enable on npmjs.com for all publishing accounts
- **Granular access tokens**: Create tokens with limited scope and expiration
- **npm Organizations**: Use teams and access controls for shared packages
- **Automated security advisories**: npm automatically creates advisories for reported vulnerabilities

```shell
# Create a read-only automation token
npm token create --read-only

# Create a token limited to specific packages
npm token create --cidr=192.168.1.0/24
```

#### Common Vulnerabilities

| Vulnerability Type | Example | Mitigation |
|--------------------|---------|------------|
| Prototype pollution | Manipulating `__proto__` | Use `Object.create(null)`, validate input |
| ReDoS | Catastrophic regex backtracking | Audit regex patterns, use safe-regex |
| Path traversal | `../` in file operations | Validate and sanitize paths |
| Command injection | Unsanitized shell commands | Use `execFile` instead of `exec` |
| Typosquatting | `loadsh` vs `lodash` | Verify package names carefully |

#### Quick-Reference Checklist

- [ ] `package-lock.json` committed to version control
- [ ] `npm ci` used in CI/CD pipelines
- [ ] `npm audit` runs in CI with failure threshold
- [ ] Two-factor authentication enabled on npm account
- [ ] Publish tokens use minimal required scope
- [ ] `.npmrc` configured with `save-exact=true`
- [ ] Provenance enabled for published packages
- [ ] Dependencies reviewed before updating lockfile
- [ ] `postinstall` scripts audited in dependencies

**Documentation**: npm Security Best Practices[^npm-security-appg]

---

### Python/PyPI Security Best Practices

Python's package ecosystem centers on PyPI (Python Package Index). The ecosystem has made significant security improvements including trusted publishing and mandatory 2FA for critical projects.

#### Lockfile and Pinning

Python has multiple dependency management approaches. Choose based on project needs.

**pip with requirements.txt**:

```shell
# Generate pinned requirements
pip freeze > requirements.txt

# Install with hash verification
pip install --require-hashes -r requirements.txt

# Generate requirements with hashes
pip-compile --generate-hashes requirements.in
```

**Poetry**:

```shell
# Install from lockfile
poetry install --no-root

# Update lockfile
poetry update

# Export to requirements.txt
poetry export -f requirements.txt --output requirements.txt
```

**Pipenv**:

```shell
# Install from Pipfile.lock
pipenv install --deploy

# Generate lockfile
pipenv lock
```

**uv**[^uv-docs] (recommended for new projects):

```shell
# Initialize project
uv init

# Add dependencies
uv add requests

# Generate/update lockfile
uv lock

# Install from lockfile (reproducible)
uv sync

# Install with frozen lockfile (CI/CD)
uv sync --frozen
```

uv is an extremely fast Python package manager written in Rust (10-100x faster than pip) that provides security-first defaults:

- Generates cross-platform lockfiles with SHA-256 hashes by default
- Replaces pip, pip-tools, pipx, poetry, pyenv, and virtualenv with a single tool
- Hermetic builds ensure reproducibility across environments
- Active development by Astral[^astral], creators of the Ruff linter

**Best Practices**:

- Always use a lockfile mechanism (`uv.lock`, `poetry.lock`, `Pipfile.lock`, or pinned `requirements.txt`)
- Use hash verification for production deployments
- Pin transitive dependencies, not just direct dependencies

#### Security Scanning Tools

| Tool | Purpose | Command |
|------|---------|---------|
| pip-audit | Vulnerability scanning | `pip-audit` |
| Safety | Vulnerability database check | `safety check` |
| uv-secure | Vulnerability scanning for uv.lock | `uv-secure` |
| Bandit | Static analysis for Python | `bandit -r src/` |
| Semgrep | Pattern-based scanning | `semgrep --config=p/python` |
| pyup.io | Dependency monitoring | SaaS integration |

```shell
# Scan installed packages for vulnerabilities
pip-audit

# Scan requirements file
pip-audit -r requirements.txt

# Run Bandit with baseline
bandit -r src/ -b bandit-baseline.json
```

#### Signing and Verification

PyPI supports **Trusted Publishing** via OpenID Connect, eliminating the need for API tokens.

```yaml
# GitHub Actions: Trusted Publishing
jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:

      - uses: pypa/gh-action-pypi-publish@release/v1

```

**Sigstore signing** is available for packages:

```shell
# Sign a distribution
python -m sigstore sign dist/*.whl

# Verify a signature
python -m sigstore verify dist/*.whl
```

#### Registry Security Features

- **Trusted Publishing**: Publish from GitHub Actions without API tokens
- **Mandatory 2FA**: Required for critical projects (top 1% by downloads)
- **Organization accounts**: Team-based access control
- **API tokens**: Scoped tokens for automation
- **Project quarantine**: New projects held briefly for malware scanning

```shell
# Configure trusted publishing on PyPI
# 1. Go to PyPI project settings
# 2. Add GitHub repository as trusted publisher
# 3. Remove API token from GitHub secrets
```

#### Common Vulnerabilities

| Vulnerability Type | Example | Mitigation |
|--------------------|---------|------------|
| Pickle deserialization | `pickle.loads(untrusted)` | Never unpickle untrusted data |
| YAML deserialization | `yaml.load()` unsafe by default | Use `yaml.safe_load()` |
| SQL injection | String formatting in queries | Use parameterized queries |
| Command injection | `os.system()` with user input | Use `subprocess` with lists |
| Path traversal | `open(user_path)` | Validate paths, use `pathlib` |

#### Quick-Reference Checklist

- [ ] Lockfile committed (`uv.lock`, `poetry.lock`, `Pipfile.lock`, or pinned requirements)
- [ ] Hash verification enabled for production installs
- [ ] `pip-audit`, Safety, or `uv-secure` runs in CI pipeline
- [ ] Bandit static analysis configured
- [ ] Two-factor authentication enabled on PyPI
- [ ] Trusted Publishing configured for releases
- [ ] Virtual environments used for isolation
- [ ] `setup.py` reviewed for code execution during install
- [ ] Private package index configured if using internal packages

**Documentation**: PyPI Security[^pypi-security]

---

### Java/Maven Security Best Practices

The Java ecosystem uses Maven Central as its primary repository. The ecosystem has mature security tooling but requires explicit configuration for dependency locking.

#### Lockfile and Pinning

Maven doesn't have a native lockfile. Use these approaches for reproducible builds:

**Dependency Management BOM**:

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>3.2.0</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

**Maven Enforcer Plugin**:

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-enforcer-plugin</artifactId>
  <version>3.4.1</version>
  <executions>
    <execution>
      <id>enforce-versions</id>
      <goals><goal>enforce</goal></goals>
      <configuration>
        <rules>
          <requireUpperBoundDeps/>
          <dependencyConvergence/>
        </rules>
      </configuration>
    </execution>
  </executions>
</plugin>
```

**Gradle Lockfiles**:

```shell
# Generate lockfile
./gradlew dependencies --write-locks

# Verify against lockfile
./gradlew dependencies --update-locks
```

#### Security Scanning Tools

| Tool | Purpose | Integration |
|------|---------|-------------|
| OWASP Dependency-Check | Vulnerability scanning | Maven/Gradle plugin |
| Snyk | Comprehensive scanning | CLI and plugins |
| SpotBugs + Find Security Bugs | Static analysis | Maven plugin |
| JCapsLock | Capability analysis | Maven plugin |
| Checkmarx | Enterprise SAST | CI integration |

```xml
<!-- OWASP Dependency-Check Maven Plugin -->
<plugin>
  <groupId>org.owasp</groupId>
  <artifactId>dependency-check-maven</artifactId>
  <version>9.0.0</version>
  <executions>
    <execution>
      <goals><goal>check</goal></goals>
    </execution>
  </executions>
  <configuration>
    <failBuildOnCVSS>7</failBuildOnCVSS>
  </configuration>
</plugin>
```

```shell
# Run dependency check
mvn dependency-check:check

# Generate SBOM
mvn org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom

# Run JCapsLock capability analysis
mvn capslock:analyze
```

**JCapsLock** performs capability analysis on Java dependencies, showing which packages can access files, network, or execute code. This helps detect unexpected behavior in dependencies: useful for identifying supply chain compromises that don't yet have CVEs.

#### Signing and Verification

Maven Central requires GPG signing for all published artifacts.

```shell
# Generate GPG key
gpg --gen-key

# Sign artifact
mvn gpg:sign

# Verify signatures
mvn org.simplify4u.plugins:pgpverify-maven-plugin:check
```

```xml
<!-- PGP Verification Plugin -->
<plugin>
  <groupId>org.simplify4u.plugins</groupId>
  <artifactId>pgpverify-maven-plugin</artifactId>
  <version>1.17.0</version>
  <executions>
    <execution>
      <goals><goal>check</goal></goals>
    </execution>
  </executions>
</plugin>
```

#### Registry Security Features

- **GPG signing mandatory**: All artifacts on Maven Central must be signed
- **Namespace validation**: Group IDs verified against domain ownership
- **Immutable releases**: Published versions cannot be modified
- **Sonatype scans**: Automated malware scanning on upload

#### Common Vulnerabilities

| Vulnerability Type | Example | Mitigation |
|--------------------|---------|------------|
| Deserialization | `ObjectInputStream` attacks | Avoid Java serialization, use allowlists |
| XML External Entity (XXE) | Processing untrusted XML | Disable external entities |
| Log injection | Log4Shell (CVE-2021-44228) | Update Log4j, disable JNDI |
| SQL injection | String concatenation | Use PreparedStatement |
| Expression Language injection | Spring EL, OGNL | Sanitize inputs |

#### Quick-Reference Checklist

- [ ] Dependency versions explicitly declared
- [ ] Maven Enforcer Plugin configured
- [ ] OWASP Dependency-Check in build pipeline
- [ ] GPG signing configured for releases
- [ ] PGP verification enabled for dependencies
- [ ] SpotBugs with security rules enabled
- [ ] Sonatype account has 2FA enabled
- [ ] SBOM generated with CycloneDX plugin
- [ ] Transitive dependencies reviewed with `mvn dependency:tree`

**Documentation**: Maven Central Security[^maven-security]

---

### Go Modules Security Best Practices

Go has built-in module support with strong supply chain security features, including the module checksum database and module proxy.

#### Lockfile and Pinning

Go uses `go.sum` as its lockfile, containing cryptographic checksums of all dependencies.

```shell
# Initialize module
go mod init example.com/myproject

# Add dependencies and update go.sum
go mod tidy

# Verify checksums
go mod verify

# Download dependencies
go mod download
```

**Best Practices**:

- Always commit both `go.mod` and `go.sum`
- Run `go mod verify` in CI to detect tampering
- Use `go mod tidy` to remove unused dependencies

```shell
# Vendor dependencies for hermetic builds
go mod vendor

# Build using vendored dependencies
go build -mod=vendor
```

#### Security Scanning Tools

| Tool | Purpose | Command |
|------|---------|---------|
| govulncheck | Official vulnerability scanner | `govulncheck ./...` |
| Capslock | Capability analysis (files, network, exec) | `capslock -packages=./...` |
| gosec | Static analysis | `gosec ./...` |
| Staticcheck | Comprehensive linter | `staticcheck ./...` |
| Trivy | Container and filesystem scan | `trivy fs .` |

```shell
# Install and run govulncheck
go install golang.org/x/vuln/cmd/govulncheck@latest
govulncheck ./...

# Run Capslock to analyze package capabilities
go install github.com/google/capslock/cmd/capslock@latest
capslock -packages=./...

# Run gosec
gosec -fmt=json -out=results.json ./...
```

**Capslock** analyzes what your Go packages and dependencies can actually *do*: file access, network calls, process execution. This is particularly valuable for auditing dependencies, as unexpected capabilities (like a data parser with network access) may indicate supply chain compromise. For Go packages, results are also available on deps.dev without local installation.

#### Signing and Verification

Go modules use the **checksum database** (sum.golang.org) for verification.

```shell
# Environment variables for checksum verification
export GOSUMDB=sum.golang.org
export GOFLAGS="-mod=readonly"

# Verify module checksums
go mod verify
```

**Private modules**: Configure `GOPRIVATE` for internal modules:

```shell
export GOPRIVATE=github.com/mycompany/*,gitlab.mycompany.com/*
```

#### Registry Security Features

- **Checksum Database**: Immutable log of module checksums
- **Module Proxy**: Caching proxy (proxy.golang.org) provides availability
- **Module Mirror**: Ensures modules remain available even if source disappears
- **Transparency Log**: Append-only log prevents tampering

```shell
# Configure module proxy
export GOPROXY=https://proxy.golang.org,direct

# For private modules
export GOPROXY=https://proxy.golang.org,https://private.proxy.mycompany.com,direct
```

#### Common Vulnerabilities

| Vulnerability Type | Example | Mitigation |
|--------------------|---------|------------|
| Command injection | `exec.Command` with user input | Validate inputs, avoid shell |
| Path traversal | Unsanitized file paths | Use `filepath.Clean`, validate |
| Integer overflow | Unchecked arithmetic | Use `math` package checks |
| Race conditions | Concurrent map access | Use `sync.Map` or mutexes |
| Slice bounds | Index out of range | Validate indices |

#### Quick-Reference Checklist

- [ ] `go.mod` and `go.sum` committed to version control
- [ ] `go mod verify` runs in CI pipeline
- [ ] `govulncheck` runs in CI pipeline
- [ ] `gosec` static analysis configured
- [ ] `GOSUMDB` not disabled (default is secure)
- [ ] `GOPRIVATE` configured for internal modules
- [ ] Vendoring used for reproducible builds (optional)
- [ ] Module versions use semantic versioning
- [ ] Major version changes use `/v2`, `/v3` paths

**Documentation**: Go Module Security[^go-security]

---

### Rust/crates.io Security Best Practices

Rust's package ecosystem (crates.io) benefits from memory safety guarantees and strong security practices.

#### Lockfile and Pinning

Rust uses `Cargo.lock` for dependency locking.

```shell
# Generate/update lockfile
cargo build

# Update specific dependency
cargo update -p serde

# Install exact versions from lockfile
cargo install --locked
```

**Best Practices**:

- Always commit `Cargo.lock` for applications
- Libraries may omit `Cargo.lock` from version control
- Use `--locked` flag in CI to ensure lockfile is respected

```toml
# Cargo.toml - pin specific version
[dependencies]
serde = "=1.0.193"  # Exact version

# Or use caret (default, allows patch updates)
serde = "1.0"  # Equivalent to ^1.0
```

#### Security Scanning Tools

| Tool | Purpose | Command |
|------|---------|---------|
| cargo-audit | Vulnerability scanning | `cargo audit` |
| cargo-deny | License and vulnerability checks | `cargo deny check` |
| cargo-crev | Code review system | `cargo crev verify` |
| cargo-capslock | Capability analysis (experimental) | `cargo capslock` |
| clippy | Linting including security | `cargo clippy` |

```shell
# Install and run cargo-audit
cargo install cargo-audit
cargo audit

# Install and configure cargo-deny
cargo install cargo-deny
cargo deny init
cargo deny check
```

**cargo-capslock** is an experimental Rust Foundation project (Alpha-Omega funded) that performs capability analysis similar to the Go implementation, analyzing what dependencies can access at the LLVM IR level.

**cargo-deny configuration** (`deny.toml`):

```toml
[advisories]
vulnerability = "deny"
unmaintained = "warn"
yanked = "deny"

[licenses]
unlicensed = "deny"
allow = ["MIT", "Apache-2.0", "BSD-3-Clause"]

[bans]
multiple-versions = "warn"
deny = [
  { name = "openssl" }  # Prefer rustls
]
```

#### Signing and Verification

crates.io does not currently require signing, but provides other integrity measures:

```shell
# Verify checksums (automatic with cargo)
cargo fetch

# Use cargo-crev for community code reviews
cargo install cargo-crev
cargo crev id new
cargo crev verify
```

#### Registry Security Features

- **Immutable crates**: Published versions cannot be modified
- **Yanking**: Problematic versions can be yanked (not deleted)
- **Mandatory 2FA**: Required for publishing
- **API tokens**: Scoped tokens for automation
- **Rate limiting**: Prevents abuse

```shell
# Create scoped token
# Via crates.io web interface

# Publish with token
cargo publish --token $CRATES_IO_TOKEN
```

#### Common Vulnerabilities

| Vulnerability Type | Example | Mitigation |
|--------------------|---------|------------|
| Unsafe code | `unsafe` blocks with bugs | Minimize unsafe, audit carefully |
| FFI issues | C interop vulnerabilities | Wrap unsafe FFI in safe APIs |
| Integer overflow | Debug vs release behavior | Use `checked_*` methods |
| Dependency confusion | Internal crate names | Use organizations/namespacing |
| Build scripts | Malicious `build.rs` | Review build scripts |

#### Quick-Reference Checklist

- [ ] `Cargo.lock` committed for applications
- [ ] `cargo audit` runs in CI pipeline
- [ ] `cargo deny` configured and runs in CI
- [ ] Two-factor authentication enabled on crates.io
- [ ] API tokens use minimal required scope
- [ ] `#![forbid(unsafe_code)]` where applicable
- [ ] Clippy warnings addressed
- [ ] Build scripts audited for dependencies
- [ ] `--locked` flag used in CI builds

**Documentation**: crates.io Security[^crates-security]

---

### Container Security Best Practices

Container security spans multiple ecosystems and requires attention to base images, build processes, and runtime configuration.

#### Base Image Selection and Pinning

```dockerfile
# Pin to specific digest (most secure)
FROM node:24.13.0-alpine3.23@sha256:931d7d...

# Pin to specific version (good)
FROM node:24.13.0-alpine3.23

# Avoid (mutable tags)
FROM node:latest
FROM node:24
```

**Distroless and minimal images**:

```dockerfile
# Google Distroless (no shell, minimal attack surface)
FROM gcr.io/distroless/nodejs24-debian12

# Chainguard Images (SLSA, signatures, SBOMs)
FROM cgr.dev/chainguard/node:latest

# Docker Hardened Images (minimal images, SLSA, signatures, SBOMs, version tags)
FROM dhi.io/node:24
```

#### Security Scanning Tools

| Tool | Purpose | Command |
|------|---------|---------|
| Trivy | Vulnerability and misconfiguration | `trivy image myimage:tag` |
| Grype | Vulnerability scanning | `grype myimage:tag` |
| Syft | SBOM generation | `syft myimage:tag` |
| Docker Scout | Docker-integrated scanning | `docker scout cves myimage:tag` |
| Snyk Container | Container scanning | `snyk container test myimage:tag` |

```shell
# Scan image for vulnerabilities
trivy image --severity HIGH,CRITICAL myapp:latest

# Generate SBOM for container
syft myapp:latest -o spdx-json > sbom.json

# Scan filesystem during build
trivy fs --scanners vuln,secret,misconfig .
```

#### Signing and Verification

Use **Sigstore Cosign** for container signing:

```shell
# Install cosign
brew install cosign

# Sign container image (keyless)
cosign sign myregistry/myimage:tag

# Verify signature
cosign verify myregistry/myimage:tag

# Sign with SBOM attestation
cosign attest --predicate sbom.json myregistry/myimage:tag
```

**Policy enforcement with Kyverno or OPA Gatekeeper**:

```yaml
# Kyverno policy: require signed images
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-image-signature
spec:
  validationFailureAction: Enforce
  rules:

    - name: verify-signature

      match:
        resources:
          kinds:

            - Pod

      verifyImages:

        - imageReferences:
            - "myregistry/*"

          attestors:

            - entries:
                - keyless:

                    issuer: https://accounts.google.com
                    subject: build@mycompany.com
```

#### Registry Security Features

| Registry | Security Features |
|----------|-------------------|
| Docker Hub | Content trust, vulnerability scanning, access tokens |
| GitHub Container Registry | OIDC publishing, vulnerability alerts, visibility controls |
| Amazon ECR | Image scanning, lifecycle policies, IAM integration |
| Google Artifact Registry | Vulnerability scanning, Binary Authorization |
| Azure Container Registry | Content trust, Defender scanning, managed identities |

```shell
# Enable Docker Content Trust
export DOCKER_CONTENT_TRUST=1

# Configure registry credentials securely
docker login --username $USER --password-stdin myregistry.com < token.txt
```

#### Common Vulnerabilities

| Vulnerability Type | Example | Mitigation |
|--------------------|---------|------------|
| Vulnerable base images | Outdated OS packages | Use updated, minimal images |
| Secrets in images | Credentials in layers | Multi-stage builds, secrets management |
| Excessive privileges | Running as root | Use non-root user, drop capabilities |
| Mutable tags | `latest` tag changes | Pin by digest |
| Exposed ports | Unnecessary services | Minimize exposed ports |

**Dockerfile security best practices**:

```dockerfile
# Use non-root user
FROM node:20-alpine
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Multi-stage build (don't include build tools in final image)
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER node
CMD ["node", "dist/index.js"]
```

#### Quick-Reference Checklist

- [ ] Base images pinned by digest
- [ ] Minimal/distroless base images used where possible
- [ ] Multi-stage builds eliminate build dependencies
- [ ] Container runs as non-root user
- [ ] No secrets embedded in image layers
- [ ] Trivy or equivalent scans in CI pipeline
- [ ] Images signed with Cosign
- [ ] SBOM generated and attested
- [ ] Registry uses access controls and scanning
- [ ] Signature verification enforced at deployment
- [ ] Image pull policies prevent mutable tags in production

**Documentation**: Docker Security[^docker-security], Sigstore[^sigstore-docs]

---

### Quick-Reference Security Checklists by Ecosystem

Use these condensed checklists for rapid security assessment across ecosystems.

| Security Control | npm | PyPI | Maven | Go | Rust | Containers |
|------------------|-----|------|-------|-----|------|------------|
| **Lockfile committed** | `package-lock.json` | `uv.lock` / `poetry.lock` | Enforcer plugin | `go.sum` | `Cargo.lock` | Digest pinning |
| **CI install command** | `npm ci` | `uv sync --frozen` | `mvn verify` | `go mod verify` | `cargo build --locked` | `docker pull @sha256:` |
| **Vulnerability scanner** | `npm audit` | `pip-audit` / `uv-secure` | OWASP Dep-Check | `govulncheck` | `cargo audit` | Trivy/Grype |
| **Static analysis** | ESLint security | Bandit | SpotBugs | gosec | Clippy | Dockerfile lint |
| **2FA on registry** | Required | Required (critical) | Required | N/A | Required | Registry-dependent |
| **Signing available** | Provenance | Sigstore | GPG (required) | Checksum DB | Not yet | Cosign |
| **SBOM tool** | Syft | Syft/CycloneDX | CycloneDX plugin | Syft | cargo-sbom | Syft |

**Universal Security Principles**:

1. **Pin dependencies**: Use lockfiles and verify checksums
2. **Scan continuously**: Run vulnerability scanners in every CI build
3. **Update regularly**: Keep dependencies current, not just when vulnerabilities appear
4. **Verify integrity**: Enable signature verification where available
5. **Minimize surface**: Use minimal dependencies and base images
6. **Automate updates**: Use Dependabot, Renovate, or equivalent
7. **Monitor advisories**: Subscribe to security notifications for critical dependencies

[^npm-security-appg]: npm, "Securing your code," https://docs.npmjs.com/packages-and-modules/securing-your-code

[^uv-docs]: Astral, "uv Documentation," https://docs.astral.sh/uv/

[^astral]: Astral, https://astral.sh/

[^pypi-security]: Python Software Foundation, "PyPI Security," https://pypi.org/security/

[^maven-security]: Sonatype, "Maven Central Requirements," https://central.sonatype.org/publish/requirements/

[^go-security]: Go Team, "How Go Mitigates Supply Chain Attacks," https://go.dev/blog/supply-chain

[^crates-security]: Rust Foundation, "crates.io Policies," https://crates.io/policies

[^docker-security]: Docker, "Docker Security," https://docs.docker.com/engine/security/

[^sigstore-docs]: Sigstore, "Sigstore Documentation," https://docs.sigstore.dev/
