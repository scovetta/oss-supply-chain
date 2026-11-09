# Chapter 15: Red Team and Adversarial Testing

This chapter covers proactively testing software supply chain security through penetration testing, red teaming, tabletop exercises, and adversarial thinking. Unlike traditional application security testing, supply chain security assessment requires targeting build systems, CI/CD pipelines, artifact repositories, and dependency management infrastructure.

The chapter begins with supply chain penetration testing, which differs fundamentally from conventional pen tests by focusing on development infrastructure rather than deployed applications. Key considerations include scoping build systems and registries, testing for dependency confusion vulnerabilities, scanning for exposed secrets, and establishing strict safety rules to prevent unintended downstream impact.

Red team exercises simulate real adversary behavior against build infrastructure, drawing from threat scenarios like compromised developer credentials, CI/CD exploitation, and artifact repository poisoning. The chapter emphasizes purple teaming, where attackers and defenders collaborate in real-time to accelerate detection capability improvements.

Tabletop exercises prepare organizations for supply chain incidents through discussion-based simulations. Using scenarios modeled on real attacks like Log4Shell and compromised npm packages, teams practice incident response coordination, decision-making under uncertainty, and cross-functional communication. Regular exercises with proper follow-through on action items build the muscle memory needed when real incidents occur.

The final section develops adversarial thinking skills, teaching defenders to analyze attack paths, identify weak links, understand attacker economics, and translate insights into prioritized defenses. The key insight is that defenders must think in graphs (connected attack paths) rather than checklists, focusing resources on high-value targets that offer attackers the best return on investment.

Together, these approaches reveal vulnerabilities that scanning and compliance cannot detect, testing whether organizations can prevent, detect, and respond to sophisticated supply chain attacks.
