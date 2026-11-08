---
title: "Securing Build and Release Pipelines"
description: "A comprehensive framework for securing CI/CD infrastructure, from foundational principles to SLSA compliance and artifact signing."
icon: "lucide/shield-check"
---

# Chapter 17: Securing Build and Release Pipelines

CI/CD pipelines represent uniquely high-value targets in the software supply chain. They have privileged access to source code, secrets, signing keys, and deployment credentials, making their security critical to overall software integrity. This chapter lays out a framework for securing build infrastructure, from foundational principles to specific implementation guidance.

The chapter begins with core security principles including least privilege, build isolation, ephemeral environments, comprehensive audit logging, and network segmentation. It then explores zero trust architecture for CI/CD, demonstrating how to replace long-lived stored secrets with identity-based authentication using OIDC federation and short-lived credentials.

A significant focus is placed on non-human identity management, addressing the challenge that machine identities now vastly outnumber human identities in enterprise environments. The chapter covers workload identity frameworks like SPIFFE/SPIRE, just-in-time credential issuance, and the emerging considerations around AI agent identities in build systems.

The chapter examines hermetic and reproducible builds as foundational controls that enable independent verification of software artifacts. Build provenance and attestation receive detailed treatment, including the in-toto framework and practical implementation across ecosystems.

Two sections provide deep coverage of industry standards: the SLSA framework with its incrementally adoptable levels for measuring supply chain security, and Sigstore with its transparency logs for keyless artifact signing. These standards have achieved significant ecosystem adoption, with npm, PyPI, and container registries now supporting native provenance and signing capabilities.

Together, these practices transform CI/CD security from implicit trust to explicit verification, enabling organizations to detect tampering and establish accountability throughout the build process.
