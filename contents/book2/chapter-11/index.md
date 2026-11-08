---
title: "Measuring Supply Chain Risk"
description: "A guide to measuring, assessing, and managing software supply chain risk through frameworks, metrics, and culture."
icon: "lucide/bar-chart-3"
---

# Chapter 11: Measuring Supply Chain Risk

Chapter 11 is a guide to measuring and managing software supply chain risk. It establishes that understanding threats is necessary but not sufficient; organizations need structured approaches to assess, prioritize, and respond to supply chain risks in their specific context.

The chapter begins by examining established risk frameworks including NIST C-SCRM, ISO 28000, and FAIR, alongside software-specific frameworks like SLSA, OpenSSF Scorecards, and S2C2F. It emphasizes that no single framework perfectly addresses all needs, and mature organizations typically combine multiple approaches.

A major focus is understanding dependency depth and transitive risk. When developers add a single package, they often inherit dozens or hundreds of transitive dependencies they never explicitly chose. The chapter provides practical techniques for measuring this exposure, analyzing risk concentration (where many packages depend on the same underlying component), and communicating these risks to stakeholders.

Project health evaluation receives detailed treatment, covering maintainer activity, community health, security practices, maturity indicators, and documentation quality. The chapter introduces the concept of project support archetypes ranging from "no support" to "promised support" backed by foundations or corporate sponsors.

Automated scoring systems including OpenSSF Scorecards, deps.dev, Ecosyste.ms, and commercial tools are examined with honest discussion of their limitations. The chapter warns that scores can be gamed and represent proxies for security rather than security itself.

Beyond technical measures, the chapter addresses building risk-aware organizational culture, distinguishing compliance-driven organizations from those with genuine security awareness. It covers developer education, incentive alignment, blameless postmortems, and effective communication strategies for different audiences.

Finally, the chapter addresses supply chain risk in mergers and acquisitions, where inherited dependencies become balance sheet liabilities requiring careful due diligence, technical debt quantification, and structured post-acquisition remediation planning.
