# Chapter 18: Securing Distribution and Deployment

This chapter covers securing the distribution layer of the software supply chain, covering package registries, container images, policy enforcement, and runtime verification.

The chapter begins by examining security features across major package registries (npm, PyPI, Maven Central, NuGet, RubyGems), including authentication models, multi-factor authentication enforcement, namespace protection, package signing with Sigstore, and trusted publishing through OIDC integration. These controls address account takeover risks that have enabled high-profile supply chain attacks.

Container image security receives extensive treatment, explaining the layered image model and its inherited risk implications. The chapter evaluates base image selection criteria, advocates for minimal images (distroless and scratch-based) to reduce attack surface, and details scanning tools like Trivy for vulnerability detection. Image signing with Cosign and Notation ensures integrity, while admission controllers enforce deployment policies.

Policy enforcement through Kubernetes admission control forms a critical enforcement layer. The chapter compares OPA/Gatekeeper with Kyverno for implementing image source restrictions, signature verification, vulnerability thresholds, and security configuration requirements. Practical guidance covers policy development workflows, graduated enforcement rollouts, and structured exception handling.

The final section addresses runtime verification as the last line of defense. File integrity monitoring, RASP, and eBPF-based tools (Falco, Tetragon, Cilium) provide visibility into executing software behavior. Behavioral anomaly detection and container drift detection catch compromises that evade pre-deployment controls. The chapter concludes with guidance on alert workflows and minimizing false positives to maintain operational effectiveness.

Together, these controls create defense in depth from artifact publication through runtime execution.
