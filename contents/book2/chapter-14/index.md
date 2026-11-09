# Chapter 14: Security Testing for Supply Chains

This chapter is a practical guide to security testing techniques specifically designed for evaluating and monitoring third-party dependencies in software supply chains. Unlike traditional application security testing, dependency testing requires specialized approaches because organizations must assess code they did not write and cannot directly modify.

The chapter begins with fuzz testing as a dependency evaluation technique, explaining how coverage-guided fuzzing can reveal vulnerabilities that human reviewers and traditional testing miss. Tools like OSS-Fuzz, AFL++, libFuzzer, and language-specific fuzzers enable organizations to assess code robustness before adoption.

Static analysis applied to dependencies extends beyond CVE scanning to detect zero-day vulnerabilities, malicious code patterns, and quality issues. The chapter covers tools like Semgrep and CodeQL with custom rules for supply chain attack indicators such as obfuscated code, suspicious network calls, and install-time execution.

Dynamic analysis addresses the limitations of static approaches by observing actual runtime behavior. Install-time monitoring, network call detection, file system access tracking, and sandboxing techniques help identify malicious packages that hide their true behavior until execution.

Security regression testing ensures that dependency updates do not reintroduce vulnerabilities or break security controls. Integration with automated update tools like Dependabot and Renovate enables continuous validation as dependencies evolve.

For high-assurance systems, the chapter introduces symbolic execution and formal methods, explaining when mathematical verification is justified and which verified components (HACL*, EverCrypt, s2n-tls) provide stronger guarantees than testing alone.

Finally, chaos engineering principles applied to supply chains help organizations test resilience against dependency failures, registry outages, and infrastructure disruptions before real incidents occur.
