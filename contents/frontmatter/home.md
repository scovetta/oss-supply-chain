---
title: Home
description: A practical guide to understanding, protecting, and governing the open source software supply chain.
hide:
    - navigation
    - path
    - toc
---

# Open Source Software Supply Chain Security

A practical guide to understanding, protecting, and governing the open source software supply chain.

<div class="equal-table" markdown="1">

| Understanding the Software Supply Chain | Protecting the Software Supply Chain | Governing the Software Supply Chain |
|:-:|:-:|:-:|
| [![Book 1 Cover - Understanding the Software Supply Chain](../book1/cover-front.png)](book1) | [![Book 2 - Protecting the Software Supply Chain](../book2/cover-front.png)](book2) | [![Book 3 - Governing the Software Supply Chain](../book3/cover-front-640.png)](book3) |
| [ [Read Online](book1) ]<br/>[ [Download PDF](https://github.com/scovetta/oss-supply-chain/releases/download/v2026.08.02/book-1-understanding-the-software-supply-chain.pdf) ] | [ [Read Online](book2) ]<br/>[ [Download PDF](https://github.com/scovetta/oss-supply-chain/releases/download/v2026.08.02/book-2-protecting-the-software-supply-chain.pdf) ] | [ [Read Online](book3) ]<br/>[ [Download PDF](https://github.com/scovetta/oss-supply-chain/releases/download/v2026.08.02/book-3-governing-the-software-supply-chain.pdf) ]|

</div>

---

## About This Book Series

Most of the code in a modern application was written by someone else: open source maintainers, registry operators, CI providers, and vendors you may never have heard of. That arrangement is why software gets built so quickly, and it is also why a single compromised package, build server, or maintainer account can reach thousands of organizations at once.

This three-book series is for the people who have to deal with that. Book 1 explains how the supply chain works and how it gets attacked, Book 2 covers the defenses, and Book 3 covers the organizational, legal, and economic questions that decide whether those defenses last.

---

## The Books

### Book 1: Understanding the Software Supply Chain

**Chapters 1-10** | Foundations and Attack Patterns

How software is actually built today, who maintains it, and how attackers exploit it, with each attack pattern grounded in real incidents.

- **Part I - Foundations**: How software is built, supply chain threats, historical attacks
- **Part II - Attack Patterns**: Malicious packages, dependency confusion, typosquatting, build system attacks, insider threats, social engineering

[Start Reading Book 1](book1/)

[Download PDF](https://github.com/scovetta/oss-supply-chain/releases/download/v2026.08.02/book-1-understanding-the-software-supply-chain.pdf)

---

### Book 2: Protecting the Software Supply Chain

**Chapters 11-22** | Practical Defenses and Incident Response

Defenses across the development lifecycle, from choosing a dependency to running code in production, plus what to do when prevention fails.

- **Part III - Risk Assessment & Testing**: Risk measurement, SBOMs, dependency management, security testing, red teaming
- **Part IV - Defense & Response**: Securing development environments, CI/CD pipelines, distribution, incident response, crisis communication
- **Part V - Operationalizing Defense**: Building security programs, platform engineering

[Start Reading Book 2](book2/)

[Download PDF](https://github.com/scovetta/oss-supply-chain/releases/download/v2026.08.02/book-2-protecting-the-software-supply-chain.pdf)

---

### Book 3: Governing the Software Supply Chain

**Chapters 23-33** | Policy, Compliance, and Organizational Strategy

The people, money, and rules behind supply chain security: training, maintainers, vendor risk, regulation, economics, and where the field is heading.

- **Part V - People & Organizations**: Training and security culture, open source maintainers, vendor risk management
- **Part VI - Regulatory & Legal**: Regulatory landscape, compliance frameworks, legal considerations, industry initiatives
- **Part VII - Context & Future**: Economics, geopolitics, lessons from other industries, future directions

[Start Reading Book 3](book3/)

[Download PDF](https://github.com/scovetta/oss-supply-chain/releases/download/v2026.08.02/book-3-governing-the-software-supply-chain.pdf)

---

## Appendices

- [Appendix A: Glossary](appendices/appendix-a.md) - Key terms and definitions
- [Appendix B: Resource Guide](appendices/appendix-b.md) - Curated resources for further learning
- [Appendix C: SBOM and AI-BOM Guide](appendices/appendix-c.md) - Practical guidance on software bills of materials
- [Appendix D: Security Checklist](appendices/appendix-d.md) - Actionable security checklists
- [Appendix E: Sample Policies and Templates](appendices/appendix-e.md) - Ready-to-use policy templates
- [Appendix F: Incident Timeline](appendices/appendix-f.md) - Historical supply chain incidents
- [Appendix G: Ecosystem Guides](appendices/appendix-g.md) - Language and ecosystem-specific guidance
- [Appendix H: Compliance Mapping](appendices/appendix-h.md) - Mapping to compliance frameworks
- [Appendix I: Legal Disclaimers](appendices/appendix-i.md) - Legal disclaimers and limitations
- [Appendix J: Platform Security Configuration Guides](appendices/appendix-j.md) - Step-by-step GitHub and GitLab security setup

---

## How to Use This Resource

**For security practitioners**: Start with Book 2 for immediate, actionable defenses, then reference Book 1 for deeper threat understanding.

**For developers**: Begin with Book 1, Chapter 1 to understand the landscape, then jump to Book 2's chapters on dependency management and secure development.

**For executives and managers**: Book 3 covers organizational programs, regulatory requirements, and strategic considerations.

**For open source maintainers**: See Chapter 24 in Book 3 for guidance specific to your role, and Appendices D and J for reference material and walk-throughs.

Use the **search** feature to find specific topics across all three books.

---

## License

This work is licensed under the MIT License[^mit-license]. You can copy, modify, distribute, and use the work, even for commercial purposes, with attribution.

[^mit-license]: Open Source Initiative, "MIT License," https://opensource.org/licenses/MIT
