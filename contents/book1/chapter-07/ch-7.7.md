---
title: "Code Signing and Its Limitations"
description: "Learn why code signing is necessary but not sufficient—malicious software often arrives with valid signatures from compromised builds."
icon: "lucide/pen-tool"
---

# 7.7 Code Signing and Its Limitations

Throughout the preceding case studies, a pattern recurs: malicious software arrived signed by trusted entities. SolarWinds' backdoored updates bore valid SolarWinds signatures. The 3CX trojanized installer (see Section 7.3) carried authentic 3CX certificates. These attacks succeeded not despite code signing but alongside it—the signatures were genuine, produced by compromised build systems using legitimate keys.

This pattern reveals a critical truth about code signing: it is necessary but not sufficient for supply chain security. Understanding what signing does and does not guarantee is essential for building effective defenses.

## How Code Signing Works

**Code signing** uses public-key cryptography to bind an identity to a piece of software. The mechanics are straightforward:

1. **Key generation**: A signer creates a public-private key pair. The private key is kept secret; the public key is distributed.

2. **Signature creation**: The signer computes a cryptographic hash of the software, then uses their private key to create a digital signature over that hash.

3. **Signature verification**: Anyone can use the signer's public key to verify the signature. They compute the hash of the software independently; if it matches the signed value, the signature is valid.

Valid signatures prove two things:

- **Integrity**: The software has not been modified since signing (any change would alter the hash)
- **Attribution**: The signature was created by whoever controls the private key

Crucially, signing does not prove the software is safe, correct, or free of vulnerabilities. It proves only that the signer's key was used to sign this specific content.

## The Trust Model: Certificate Authorities and Key Management

For signature verification to be meaningful, verifiers must know which public keys to trust. This leads to **Public Key Infrastructure (PKI)**, a system of **Certificate Authorities (CAs)** that vouch for key ownership.

The chain works as follows:

1. **Root CAs** are pre-trusted. Operating systems and browsers ship with lists of trusted root CA certificates.

2. **Intermediate CAs** are certified by root CAs. They issue certificates to end entities.

3. **End entity certificates** bind public keys to identities (organizations, individuals). These are what signers use.

4. **Certificate chain verification** traces from an end entity certificate through intermediates to a trusted root.

When you verify a signed binary, your system checks:

- Is the signature cryptographically valid?
- Does the certificate chain lead to a trusted root?
- Is the certificate unexpired and unrevoked?

**Key Management Challenges:**

The security of this system depends on keeping private keys secret. This is harder than it sounds:

- Keys must be accessible to build systems for automated signing
- Build systems are attractive targets (as SolarWinds demonstrated)
- Long-lived keys accumulate risk over time
- Key rotation requires coordinating with CAs and updating verifier trust

For commercial software vendors, code signing certificates cost money and require identity verification—a vendor like SolarWinds undergoes vetting before receiving a certificate. But this vetting confirms identity, not security practices. A compromised vendor signs with a valid certificate.

**Timestamping:**

Code signing timestamps record when a signature was created, using a trusted Time Stamping Authority (TSA). Without timestamping, signatures become invalid when certificates expire—software signed with an expired certificate cannot be verified. With timestamps, signatures remain valid indefinitely if created while the certificate was valid. However, this creates a complication for revocation: software signed and timestamped before a certificate was revoked may still be accepted, even if the certificate was later compromised. Organizations must balance long-term signature validity against the security implications of honoring pre-revocation signatures.

## Signing in Open Source: The Adoption Gap

Open source has historically struggled with code signing adoption. The traditional PKI model presents barriers:

**Cost**: Code signing certificates from commercial CAs cost hundreds to thousands of dollars annually. Volunteer maintainers often cannot afford them.

**Identity verification**: CAs require identity documentation that some maintainers cannot or prefer not to provide. Pseudonymous contributors face particular challenges.

**Key management complexity**: Secure key storage, rotation, and protection require infrastructure and expertise that small projects lack.

**Distributed development**: Open source projects with multiple maintainers must decide who holds signing keys, how keys are shared, and what happens when maintainers leave.

As a result, much open source software was distributed unsigned, or signed with keys that verifiers could not meaningfully validate.

## Sigstore: Democratizing Open Source Signing

**[Sigstore][sigstore]** emerged in 2021 to address these barriers, providing free, easy code signing for open source projects. The Linux Foundation project combines several components:

**[Fulcio][fulcio]** is a certificate authority that issues short-lived certificates based on OIDC (OpenID Connect) authentication. Instead of long-lived certificates requiring identity vetting, Fulcio issues certificates valid for only 10 minutes, tied to developer identity through providers like GitHub, Google, or GitLab.

**[Rekor][rekor]** is a transparency log that records all signing events. Once a signature is recorded in Rekor, it cannot be deleted or altered. This provides an immutable audit trail of what was signed, when, and by whom.

**[Cosign][cosign]** is a tool for signing and verifying container images and other artifacts using the Sigstore infrastructure.

The Sigstore model differs fundamentally from traditional PKI:

| Aspect | Traditional PKI | Sigstore |
|--------|-----------------|----------|
| Certificate lifetime | 1-3 years | 10 minutes |
| Identity verification | Manual, documentation-based | Automated, OIDC-based |
| Cost | Hundreds-thousands of dollars | Free |
| Key management | User responsibility | Ephemeral, no long-term keys |
| Audit trail | Limited | Transparency log (Rekor) |

Short-lived certificates eliminate long-term key management—an approach sometimes called **keyless signing** because developers never manage persistent cryptographic keys. Developers authenticate with existing identities (GitHub accounts) rather than obtaining separate certificates. The transparency log provides visibility into signing activity that traditional PKI lacks.

Sigstore adoption has grown rapidly. Major package ecosystems including npm, PyPI, and container registries have integrated Sigstore-based signing. GitHub's Artifact Attestations use Sigstore infrastructure.

However, OIDC-based identity introduces its own risks. If an attacker compromises a maintainer's GitHub account (through credential theft, session hijacking, or social engineering), they can generate valid Sigstore signatures tied to that identity. The transparency log captures this activity—useful for forensic investigation—but does not prevent the initial abuse. Organizations relying on Sigstore signatures should implement additional controls: monitoring for unexpected signing activity, requiring multi-factor authentication on identity provider accounts, and verifying provenance claims beyond just signature validity.

## Attacks on Signing: When Signatures Don't Help

Signing provides integrity and attribution, but attacks can work around both:

**Compromised Signing Keys:**

If attackers obtain a signing key, they can sign malicious software with a valid certificate. The SolarWinds attack succeeded partly because the attackers could use SolarWinds' legitimate signing infrastructure.

In 2012, [Adobe discovered][adobe-2012] that attackers had compromised build servers and used Adobe's code signing certificates to sign malware. Adobe revoked the affected certificates, but malware signed before revocation remained valid on systems that hadn't received revocation updates.

**Compromised Signers:**

Even without stealing keys, attackers who compromise build systems can cause legitimate signing processes to sign malicious code. The build system faithfully signs whatever it produces—including backdoors injected by attackers.

This is what happened in SolarWinds, 3CX, and other build compromise attacks. The signing infrastructure worked correctly; it just signed malicious software.

**Certificate Authority Compromise:**

Attackers who compromise CAs can issue fraudulent certificates. [The 2011 DigiNotar breach][diginotar-2011] resulted in fraudulent certificates for major websites including Google, enabling man-in-the-middle attacks. While primarily a web security incident, CA compromise can equally affect code signing.

**Insufficient Verification:**

Many systems do not verify signatures rigorously:

- Users may dismiss certificate warnings
- Systems may be configured to accept unsigned code
- Verification may check signature validity but not certificate properties
- Revocation checking may be disabled or unreliable

In 2013, [security firm Bluebox discovered the "Master Key" vulnerability][android-2013] (CVE-2013-4787, affecting Android 1.6 Donut through 4.2 Jelly Bean—nearly 900 million devices at the time), demonstrating that the signature verification process could be bypassed, allowing attackers to modify signed APKs while maintaining valid signatures.

## What Signing Does Not Prove

Given these attack vectors, it is essential to understand signing's limits:

**Signing does not prove software is safe.**

A developer can sign malware. An attacker who gains commit access can merge malicious code, which is then signed through normal release processes. The XZ Utils backdoor would have been signed if it had reached stable releases—the signing infrastructure had no way to detect malicious code.

**Signing does not prove software matches source code.**

Signing proves that specific binary content was signed by a specific key holder. It does not prove that binary was built from specific source code. The SolarWinds build modification injected code not present in source; the resulting binary was validly signed.

**Signing does not prove build process integrity.**

A signed artifact reveals nothing about how it was built. Were dependencies verified? Was the build environment secure? Were multiple parties involved? Signing answers none of these questions.

**Signing does not prove who wrote the code.**

Signing proves who signed, not who authored. An open source project might sign releases with a project key, but the signature doesn't indicate which contributors' code is included.

As [the SLSA framework][slsa] emphasizes, signatures indicate who claims responsibility for an artifact but do not establish whether that entity should be trusted.

## Beyond Signing: Attestation and Provenance

Recognizing signing's limitations, the security community has developed complementary mechanisms:

**Attestation** is a signed statement about an artifact's properties or provenance. While a signature says "I signed this artifact," an attestation says "I claim this artifact has these properties."

Attestations can assert:

- The artifact was built from specific source code (commit hash)
- The build used specific tools and configurations
- The build occurred in a specific environment
- Specific security checks passed
- Multiple parties reviewed or approved

**[in-toto][in-toto]** is a framework for generating and verifying attestations about software supply chains. It defines a standard format for attestations (the "link" format) and policies for verifying them.

**[SLSA][slsa] (Supply chain Levels for Software Artifacts)** defines requirements for software supply chain integrity, with attestations as a core mechanism:

- **SLSA Provenance** attestations describe how an artifact was built: what source it came from, what builder produced it, what commands were run
- **SLSA verification** checks that provenance attestations meet specific integrity levels

SLSA defines four levels (L0-L3), each building on the previous:

- **Level 0**: No provenance guarantees
- **Level 1**: Provenance exists and follows the SLSA format (may be unsigned)
- **Level 2**: Signed provenance generated by a hosted build service
- **Level 3**: Hardened build platform with isolated builds and non-falsifiable provenance

A SLSA Level 3 provenance attestation might state:

- This container image was built from commit `abc123` of `github.com/org/repo`
- The build ran on GitHub Actions
- The build definition came from the same repository
- The build was hermetic (no network access during build)

This provides information that signing alone cannot:

| Question | Signature | SLSA Provenance |
|----------|-----------|-----------------|
| Who signed this? | ✓ | ✓ |
| Was it modified after signing? | ✓ | ✓ |
| What source was it built from? | ✗ | ✓ |
| What build system was used? | ✗ | ✓ |
| Was the build reproducible? | ✗ | ✓ |
| What dependencies were used? | ✗ | ✓ (with SBOM) |

GitHub, npm, and PyPI have implemented provenance attestations using these standards. npm packages can include SLSA provenance attestations indicating they were built from specific repositories using GitHub Actions.

## Practical Recommendations

Given signing's importance and limitations:

**For software consumers:**

1. **Verify signatures, but don't stop there.** Signature verification is necessary but not sufficient. A valid signature should be table stakes, not the end of evaluation.

2. **Check provenance when available.** SLSA provenance attestations provide additional assurance about build integrity. Prefer packages with verified provenance.

3. **Understand what signatures mean.** A signature from a project key indicates project approval. A signature from a maintainer's personal key indicates individual approval. Neither guarantees the code is safe.

**For software producers:**

1. **Sign your releases.** Despite limitations, signing provides basic integrity and attribution. Unsigned releases lack even this baseline assurance.

2. **Use Sigstore for open source.** The cost and key management barriers no longer apply. There is little reason not to sign.

3. **Implement SLSA provenance.** Go beyond signing to provide attestations about your build process. GitHub Actions can generate provenance automatically.

4. **Protect signing keys.** If using long-lived keys, store them in HSMs (Hardware Security Modules) or secure key management systems. Minimize access. Monitor for unauthorized use.

5. **Don't treat signing as security.** Signing is one control among many. It does not replace code review, dependency scanning, or build integrity measures.

**For verifiers and platforms:**

1. **Require signatures.** Platforms should encourage or require signed artifacts.

2. **Display provenance information.** When provenance attestations are available, make them visible to users.

3. **Support revocation.** Ensure revoked certificates and compromised keys are promptly rejected.

4. **Implement SLSA verification.** Check provenance attestations against SLSA level requirements.

Code signing remains a foundational supply chain control—but one that must be understood in context. It provides integrity and attribution, not safety guarantees. The emerging ecosystem of attestation and provenance mechanisms—Sigstore, SLSA, in-toto—addresses signing's limitations by providing verifiable claims about how software was built. Book 2 explores these mechanisms in detail: Chapter 12 examines SBOMs and software inventory practices, while Chapter 17 covers SLSA provenance implementation, Sigstore adoption across ecosystems, and practical guidance for achieving higher SLSA levels.

[sigstore]: https://sigstore.dev
[fulcio]: https://docs.sigstore.dev/certificate_authority/overview/
[rekor]: https://docs.sigstore.dev/logging/overview/
[cosign]: https://docs.sigstore.dev/cosign/signing/overview/
[slsa]: https://slsa.dev
[in-toto]: https://in-toto.io
[adobe-2012]: https://web.archive.org/web/20121018205507/http://blogs.adobe.com/asset/2012/09/inappropriate-use-of-adobe-code-signing-certificate.html
[diginotar-2011]: https://en.wikipedia.org/wiki/DigiNotar
[android-2013]: https://nvd.nist.gov/vuln/detail/CVE-2013-4787
