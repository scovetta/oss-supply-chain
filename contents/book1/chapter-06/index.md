# Chapter 6: Dependency and Package Attacks

## Summary

Chapter 6 examines how attackers exploit package registries and dependency management systems to compromise software supply chains. These attacks target the trust developers place in package ecosystems, transforming the convenience of modern dependency management into a security liability.

The chapter begins with typosquatting and namesquatting, where attackers register package names similar to popular libraries to catch developer typing errors. A single keystroke mistake can lead to installing malicious code that executes immediately during installation.

Dependency confusion attacks exploit how package managers resolve names when both public and private registries are configured. Alex Birsan's 2021 research demonstrated this by gaining code execution at Apple, Microsoft, and other major companies simply by publishing public packages with names matching internal packages, earning over $130,000 in bug bounties.

The chapter documents the malicious package ecosystem, where more than 1.2 million malicious packages have been discovered across registries. Attackers use installation hooks, obfuscation, and conditional execution to evade detection while stealing credentials, mining cryptocurrency, or installing backdoors.

Detailed case studies illustrate attack patterns: the event-stream compromise showed how attackers patiently build trust before striking; ua-parser-js demonstrated credential compromise impact; colors.js and node-ipc raised questions about maintainer trust and protestware. PyPI campaigns reveal ongoing threats across ecosystems.

Advanced techniques including star-jacking, contribution fraud, manifest confusion, and lockfile injection show how attackers manufacture credibility and exploit ecosystem mechanics. Multi-stage attacks like XZ Utils demonstrate nation-state-level patience in compromising critical infrastructure.

Finally, the chapter introduces slopsquatting, in which attackers register package names that AI coding assistants hallucinate, and follows AI's effect on the other side of the ecosystem: the vulnerability reports maintainers receive. That story moved fast. Fake "slop" reports drove curl, GNOME, and others to end their bug bounties in early 2026, and by mid-year the problem had become a flood of mostly accurate AI-assisted reports that small security teams could not keep up with.

## Sections

- 6.1 Typosquatting and Namesquatting
- 6.2 Dependency Confusion Attacks
- 6.3 Malicious Packages
- 6.4 Case Studies in Package Attacks
- 6.5 Advanced Package Attack Techniques
- 6.6 Slopsquatting: AI-Hallucinated Package Attacks
