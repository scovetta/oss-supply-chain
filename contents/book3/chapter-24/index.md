# Chapter 24: Guidance for Open Source Maintainers

This chapter gives guidance for open source maintainers on securing their projects and protecting themselves while doing so. It addresses both technical security practices and the human dimensions of maintainership.

The chapter begins with foundational security practices, including creating security policies (SECURITY.md), enabling platform security features like Dependabot and secret scanning, establishing vulnerability disclosure processes, and signing releases cryptographically. It emphasizes the importance of transparent communication about project maintenance status through standards like SECURITY-INSIGHTS.yml.

A detailed workflow for handling vulnerability reports covers the entire lifecycle from initial receipt through CVE assignment, coordinated disclosure, and communicating with downstream users. The chapter stresses treating security researchers as partners rather than adversaries.

Following the XZ Utils compromise of 2024, the chapter dedicates significant attention to managing security-sensitive contributions. It provides guidance on identifying high-risk code areas, implementing graduated trust models for contributors, and recognizing social engineering patterns. Key lessons include requiring multi-person review for sensitive changes and remaining vigilant about build system modifications.

The chapter also addresses maintainer wellbeing as a security concern. Burnout creates vulnerability to social engineering attacks. Practical advice covers setting boundaries with demanding users and companies, recognizing when to step back, and accessing mental health resources.

Finally, the chapter catalogs available resources including OpenSSF programs (Alpha-Omega, Scorecard), government funding through sovereign tech funds, corporate sponsorship mechanisms, foundation support, and community mentorship networks. Maintainers need not bear the burden of securing critical infrastructure alone.
