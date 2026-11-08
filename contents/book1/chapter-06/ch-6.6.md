---
title: "Slopsquatting: AI-Hallucinated Package Attacks"
description: "Discover how attackers register package names hallucinated by AI coding assistants, creating a novel supply chain threat."
icon: "lucide/bot"
---

# 6.6 Slopsquatting: AI-Hallucinated Package Attacks

The emergence of AI coding assistants has transformed software development, enabling developers to generate code faster than ever before. Tools like GitHub Copilot, ChatGPT, Claude, and others have become integral to modern development workflows. However, this productivity comes with a novel security risk: AI models sometimes recommend packages that do not exist. Attackers have recognized this vulnerability and begun registering these hallucinated package names, creating a new attack vector that security researcher Seth Larson termed **slopsquatting** in April 2025.

This attack represents a fundamental shift in supply chain threats. Traditional attacks exploit human error or trust relationships. Slopsquatting exploits the behavior of AI systems that developers increasingly rely on, creating risk that scales with AI adoption across the software industry.

## Definition and Origin

The term **slopsquatting** combines "slop", internet slang for low-quality AI-generated content, with "squatting," the practice of claiming valuable names for malicious purposes. Seth Larson, the Python Software Foundation's Security Developer-in-Residence, coined the name, and Andrew Nesbitt, creator of Ecosyste.ms, [popularized it in an April 2025 Mastodon post][nesbitt-slopsquatting] that credited Larson for it, defining it as:

> "Slopsquatting -- when an LLM hallucinates a non-existent package name, and a bad actor registers it maliciously. The AI brother of typosquatting."

The attack leverages a well-documented phenomenon: large language models (LLMs) generate plausible-sounding but non-existent package names when asked to solve programming problems. If attackers can predict which non-existent names AI models will recommend, they can register those names with malicious packages and wait for victims to install them.

## How AI Coding Assistants Hallucinate Packages

AI coding assistants are trained on vast corpora of code, documentation, and technical discussions. When generating code recommendations, they predict likely tokens based on patterns in their training data. This prediction can produce package names that:

- **Sound plausible**: The names follow ecosystem naming conventions and seem like reasonable package names
- **Fit the context**: The hallucinated name relates to the problem being solved
- **Do not exist**: The package was never published to any registry

For example, when asked to write code for a specific task, an AI might suggest:

```python
from flask_security_utils import generate_token
```

If `flask-security-utils` does not exist on PyPI, this is a hallucination. The name is plausible, it sounds like something that might exist, but the AI has invented it based on patterns rather than referencing an actual package.

Several factors contribute to hallucination:

- **Training data age**: Models trained on older data may reference packages that were removed, renamed, or never existed
- **Pattern matching over recall**: Models generate names that fit patterns rather than retrieving exact names from memory
- **Context extrapolation**: Models invent names that seem like they should exist given the programming context
- **Frequency bias**: Common words and patterns in training data influence generation even when they produce non-existent combinations

## Research Findings: The Scale of the Problem

!!! note "AI Hallucination Rates"

    Research found commercial AI models hallucinated about 5.2% of recommended packages, while open-source models reached 21.7%. Of hallucinated names, 58% were repeated more than once across 10 runs, with 43% appearing every time.

Academic research has begun quantifying the slopsquatting risk. A [study by Spracklen et al.][spracklen-study] titled "We Have a Package for You!" systematically tested AI models' tendency to hallucinate package names.

**Key findings:**

- Commercial AI models hallucinated approximately **5.2% of recommended packages**, while open-source models reached **21.7%**
- Hallucination rates varied by model and ecosystem, with some model-ecosystem combinations exceeding 25%
- **Python (PyPI)** and **JavaScript (npm)** were particularly affected due to their large package ecosystems and prominence in training data
- Newer models did not consistently perform better than older ones on this dimension

The research tested multiple commercial and open-source models across thousands of code generation prompts, providing statistically robust evidence that hallucination is not a rare edge case but a consistent behavior affecting a significant portion of AI-generated code recommendations.

## The Repeatability Problem

Perhaps the most concerning finding from slopsquatting research is that hallucinations are **predictable and repeatable**. If an AI model hallucinates a particular package name once, it is likely to hallucinate the same name again when given similar prompts.

The Spracklen study found that hallucinated package names showed a bimodal distribution: **58% were repeated more than once** across 10 runs of the same prompt, with **43% appearing every time**. This repeatability transforms hallucination from a random error into an exploitable vulnerability:

1. Attackers can systematically query AI models with common programming prompts
2. They collect hallucinated package names that appear repeatedly
3. They register these names on public registries
4. When other developers ask similar questions and receive the same hallucinated recommendations, they install the attacker's packages

This repeatability exists because hallucinations are not truly random: they result from deterministic patterns in model behavior. The same training data biases that cause one developer to receive a hallucinated recommendation will cause other developers with similar prompts to receive the same recommendation.

[Socket.dev research][socket-slopsquatting] highlighted this pattern, identifying specific hallucinated package names that appeared consistently across repeated model runs. Some hallucinated names are therefore more valuable to attackers because they can be elicited predictably.

## Attack Mechanics

A slopsquatting attack proceeds as follows:

1. **Reconnaissance**: Attacker queries AI coding assistants with common programming questions across various domains (web development, data science, security, etc.)

2. **Harvesting**: Attacker collects package names mentioned in AI responses and checks whether they exist on public registries

3. **Validation**: Attacker re-queries with variations to confirm which hallucinated names appear consistently

4. **Registration**: Attacker registers confirmed hallucinated names on appropriate registries (PyPI, npm, RubyGems, etc.)

5. **Payload deployment**: Attacker publishes packages with malicious functionality—credential theft, cryptomining, backdoors, or other payloads

6. **Waiting**: Attacker waits for developers to receive the same hallucinated recommendations and install the now-registered malicious packages

The attack is particularly efficient because it requires no direct interaction with victims. The AI system serves as an unwitting accomplice, directing developers toward the attacker's packages through its recommendations.

## Comparison to Traditional Typosquatting

While slopsquatting shares surface similarities with typosquatting, the underlying dynamics differ significantly:

| Dimension | Typosquatting | Slopsquatting |
|-----------|---------------|---------------|
| **Error source** | Human typing mistakes | AI hallucination |
| **Target** | Variations of existing packages | Entirely invented names |
| **Predictability** | Based on keyboard layout, common typos | Based on AI model behavior patterns |
| **Scale** | Limited by typing error probability | Scales with AI usage across industry |
| **Evolution** | Relatively static attack surface | Evolves with AI model updates |

Typosquatting exploits the gap between what a developer intends to type and what they actually type. Slopsquatting exploits the gap between what developers need and what AI systems recommend.

The distinction matters for defense. Typosquatting detection focuses on edit distance from known legitimate packages. Slopsquatting involves names that are not close to any existing package: they are novel inventions by the AI. Traditional typosquatting detection will not catch them.

## Vibe Coding and Reduced Verification

!!! warning "Vibe Coding Can Reduce Verification"

    The convenience that makes AI assistants valuable can also reduce the friction that previously served as a security check. Treat AI-recommended packages as suggestions that still require independent verification.

The slopsquatting risk is amplified by changing developer practices around AI-generated code. The phenomenon of **"vibe coding"** (accepting AI-generated code with minimal review because it "vibes" correctly) reduces the verification that might catch non-existent packages.

In traditional development, a developer would:

1. Identify a need for external functionality
2. Search registries for suitable packages
3. Evaluate options based on popularity, maintenance, documentation
4. Select and install a package
5. Integrate it into their code

With AI-assisted development, the flow often becomes:

1. Describe the problem to an AI assistant
2. Receive code that includes import statements for packages
3. Copy the code into the project
4. Run install commands to fetch dependencies
5. Proceed when the code appears to work

If the AI hallucinates a package name that an attacker has registered, the developer may install it without ever verifying that the package is what they expected. The AI's authoritative presentation of the code discourages questioning.

To counter this tendency, teams should make package verification explicit rather than assuming AI-generated imports refer to legitimate packages.

## Detection and Defense

Defending against slopsquatting requires adjustments to development workflows and tooling:

**For individual developers:**

1. **Verify package existence independently**: Before installing any AI-recommended package, search for it on the official registry. Confirm it exists, has reasonable download counts, and appears legitimate.

2. **Check package age and history**: A package that was registered recently but is recommended for common tasks may be suspicious. Legitimate utility packages typically have history.

3. **Cross-reference with documentation**: If an AI recommends a package for a specific framework or library, check that library's official documentation to see if the recommended package is mentioned.

4. **Use lockfiles and review changes**: When adding new dependencies, review the lockfile additions. New packages warrant verification.

5. **Be skeptical of novel-sounding names**: AI hallucinations often combine plausible terms in ways that sound reasonable but have not been used. Unusual names warrant extra scrutiny.

**For organizations:**

1. **Implement package allowlists**: Require new dependencies to be approved against a list of vetted packages. AI-recommended packages must pass the same evaluation as manually selected ones.

2. **Deploy security scanning**: Tools like [Socket][socket], [Snyk][snyk], and npm audit can flag suspicious packages based on behavioral analysis, age, download patterns, and other signals.

3. **Establish AI usage guidelines**: Train developers on the risks of uncritically accepting AI-generated code. Require verification steps for new dependencies regardless of how they were identified.

4. **Monitor for emerging threats**: Track security research on AI hallucinations and slopsquatting. The threat landscape is evolving rapidly.

**Validation tools and approaches:**

- **Registry verification scripts**: Simple scripts that verify packages exist and have minimum thresholds for downloads and age before allowing installation
- **[Socket.dev][socket]**: Analyzes package behavior and flags suspicious characteristics including recently-registered packages with unusual patterns
- **Package manager plugins**: Extensions that prompt for verification when adding new dependencies
- **CI/CD gates**: Pipeline checks that reject builds adding unvetted dependencies

## Registry Responses and Proactive Defense

Package registries face challenges defending against slopsquatting:

**Proactive registration is difficult**: Registries could theoretically identify likely hallucinated names and reserve them, but this would require:

- Continuous AI querying to identify hallucination patterns
- Registration of potentially millions of names
- Policy decisions about name reservation without packages

**Reactive removal is the current approach**: Registries currently respond to slopsquatting as they do to other malicious packages, removing them when reported and applying behavioral analysis to detect suspicious publications.

**Potential future defenses:**

- **AI-aware detection**: Scanning new package registrations against known hallucination patterns from AI models
- **Publication friction for suspicious names**: Requiring additional verification for packages with names matching hallucination patterns
- **Namespace policies**: Encouraging use of organizational namespaces that AI systems are less likely to hallucinate
- **Collaboration with AI providers**: Sharing intelligence about hallucinated names so AI models can be updated to avoid recommending them

Some registries have begun implementing enhanced scrutiny for newly-registered packages that receive immediate downloads, a pattern consistent with slopsquatting attacks where the attacker promotes their malicious package immediately after registration.

## Recommendations

Slopsquatting represents a novel threat category that will grow as AI coding assistant adoption increases. We recommend:

1. **Treat AI-generated import statements as untrusted input.** Verify every package an AI recommends before installation, just as you would verify any code from an untrusted source.

2. **Implement verification workflows.** Establish team practices requiring package verification regardless of how dependencies are identified. AI assistance does not reduce the need for due diligence.

3. **Use security tooling that analyzes package behavior.** Traditional vulnerability scanning focuses on known CVEs. Slopsquatting introduces packages with no vulnerability history. Behavioral analysis and provenance checking are essential.

4. **Educate developers about AI limitations.** Ensure teams understand that AI assistants can and do recommend non-existent packages. Confidence in AI output should not override verification practices.

5. **Monitor the evolving threat landscape.** Slopsquatting techniques and defenses are actively developing. Stay current with security research on AI-related supply chain risks.

6. **Consider AI model selection.** Different AI models have different hallucination rates. Models with better grounding in package registries may reduce (though not eliminate) hallucination risk.

## AI-Generated Vulnerability Reports: From Slop to Flood

While slopsquatting targets developers consuming AI-generated package recommendations, a parallel problem hits the other side of the ecosystem: the open source maintainers who receive security vulnerability reports. In 2024 and 2025 that problem was **AI slop reports**, invalid reports generated by language models that describe security issues that do not exist. By 2026 it had turned into something harder to deal with: a flood of AI-assisted reports that are mostly *correct*, arriving faster than small security teams can process them. Both phases matter for supply chain security, because the vulnerability reporting channel is how upstream bugs get fixed before downstream users are exposed.

### The Slop Phase (2024–2025)

Daniel Stenberg,[^stenberg] creator and lead maintainer of curl,[^curl] became the most visible critic of AI-generated reports. In July 2025 he wrote that about 20% of all submissions to curl's bug bounty that year were AI slop, and that only about 5% of 2025 submissions had turned out to be genuine vulnerabilities.[^stenberg-ai-slop] Each bogus report still had to be handled properly. Stenberg estimated that every report engaged three or four members of curl's seven-person security team, "perhaps for 30 minutes, sometimes up to an hour or three. Each."

[^stenberg-ai-slop]: Daniel Stenberg, "Death by a Thousand Slops," daniel.haxx.se, July 14, 2025, https://daniel.haxx.se/blog/2025/07/14/death-by-a-thousand-slops/

Bug bounty platforms saw the same trend. In July 2025, HackerOne's Michiel Prins told TechCrunch the platform had seen a rise in false positives, "vulnerabilities that appear real but are generated by LLMs and lack real-world impact," while Bugcrowd founder Casey Ellis reported an overall increase of about 500 submissions per week and said AI use was widespread but had not yet produced a major spike in slop on that platform.[^techcrunch-slop]

[^techcrunch-slop]: Lorenzo Franceschi-Bicchierai, "AI slop and fake reports are exhausting some security bug bounties," TechCrunch, July 24, 2025, https://techcrunch.com/2025/07/24/ai-slop-and-fake-reports-are-exhausting-some-security-bug-bounties

Slop reports were hard to dismiss quickly because they looked plausible. They tended to share a few traits:

- Polished formatting and correct security vocabulary (CWE identifiers, CVSS vectors, OWASP categories)
- References to functions, files, or code paths that do not exist in the project
- Reproduction steps that cannot reproduce anything, and patches that do not apply to the real code
- Occasionally, fabricated or misapplied CVE identifiers

HackerOne report #3125832 to curl in 2025 is a typical example. It described an exploit in curl's HTTP/3 handling that relied on functions not present in the codebase, with a suggested patch that did not correspond to the actual code.[^curl-slop-report] It took the curl team real effort to confirm that the report described nothing.

The incentives were straightforward. Bounties paid for findings, generating a plausible report required no security skill, and submitting dozens of them cost almost nothing. Stenberg called banning offenders "a rather toothless threat."

### Bug Bounties Pull Back (Late 2025 – 2026)

Paid bounties were the first casualty. Stenberg announced the end of the curl bug bounty on January 26, 2026, effective January 31. Over its lifetime the program had confirmed 87 vulnerabilities and paid out more than $100,000, but the confirmed rate had fallen from somewhere above 15% in earlier years to below 5% in 2025. Removing the money was meant to "remove the incentives for submitting made up lies."[^curl-bounty-end] GNOME's bug bounty, funded by Germany's Sovereign Tech Agency and run on YesWeHack, paid €183,900 for 71 vulnerabilities before it accepted its last report on February 23, 2026, after its coordinator was overwhelmed by AI-generated submissions.[^catanzaro-gnome] HackerOne paused new submissions to the Internet Bug Bounty, which had funded rewards for many open source projects, on March 27, 2026, citing a growing imbalance between discovery and maintainers' ability to remediate, and Node.js suspended its own bounty after losing that funding.[^ibb-pause] On October 1, 2026, Google stopped accepting product vulnerability reports to its Open Source Software VRP, citing "a significant rise in automated submissions, the vast majority of which are not valid," and promised an update in early 2027.[^google-ossvrp-pause]

[^curl-bounty-end]: Daniel Stenberg, "The end of the curl bug-bounty," daniel.haxx.se, January 26, 2026, https://daniel.haxx.se/blog/2026/01/26/the-end-of-the-curl-bug-bounty/

[^catanzaro-gnome]: Michael Catanzaro, "The Era of Software Quality, or the Era of Ostriches?," October 2, 2026, https://blogs.gnome.org/mcatanzaro/2026/10/02/the-era-of-software-quality-or-the-era-of-ostriches/

[^ibb-pause]: CSO Online, "Internet Bug Bounty program hits pause on payouts," April 2026, https://www.csoonline.com/article/4154216/internet-bug-bounty-program-hits-pause-on-payouts-2.html

[^google-ossvrp-pause]: Help Net Security, "AI slop submissions force Google to freeze its open-source bug bounty," October 5, 2026, https://www.helpnetsecurity.com/2026/10/05/google-ai-generated-vulnerability-reports-pause/

### The Flood Phase (2026)

Ending the bounty did not reduce curl's workload. After a brief move to GitHub's private reporting, curl returned to HackerOne in March 2026 without paying rewards. In April, Stenberg reported that "the slop situation is not a problem anymore." Report frequency was roughly double the 2025 rate, and the share of confirmed vulnerabilities was back to about 15–16%, at or above the pre-AI level of 2024.[^stenberg-hqchaos] By late May the rate was four to five times higher than in 2024, and Stenberg described spending almost all of his working days on HackerOne.[^stenberg-pressure] In October he summed up the change: "Since spring 2026 the slop reports are practically gone," yet every one of the hundreds of reports received in recent months "had AI involved to some extent." He refused to call them slop, "because the reports are typically accurate and correct, even if much too wordy."[^stenberg-ignore]

[^stenberg-hqchaos]: Daniel Stenberg, "High-Quality Chaos," daniel.haxx.se, April 22, 2026, https://daniel.haxx.se/blog/2026/04/22/high-quality-chaos/

[^stenberg-pressure]: Daniel Stenberg, "The pressure," daniel.haxx.se, May 26, 2026, https://daniel.haxx.se/blog/2026/05/26/the-pressure/

[^stenberg-ignore]: Daniel Stenberg, "“ignore the slop”," daniel.haxx.se, October 22, 2026, https://daniel.haxx.se/blog/2026/10/22/ignore-the-slop/

The numbers in curl's releases show what accurate volume looks like. curl 8.21.0 in June 2026 published 18 vulnerabilities, a project record for a single release. curl 8.23.0 in October fixed 24, including a HIGH-severity HTTP/2 use-after-free, only the third HIGH-rated curl CVE since 2021.[^curl-8-21][^curl-8-23] Much of the volume came from AI-assisted auditing. Aisle, which pairs AI tooling with human validation, was credited with 35 published curl CVEs over roughly a year, more than any other group or individual had ever found in the project.[^curl-aisle]

[^curl-8-21]: Daniel Stenberg, "curl 8.21.0," daniel.haxx.se, June 24, 2026, https://daniel.haxx.se/blog/2026/06/24/curl-8-21-0/

[^curl-8-23]: Daniel Stenberg, "curl 8.23.0," daniel.haxx.se, October 14, 2026, https://daniel.haxx.se/blog/2026/10/14/curl-8-23-0/

[^curl-aisle]: Daniel Stenberg, "A curl audit by Aisle," daniel.haxx.se, October 14, 2026, https://daniel.haxx.se/blog/2026/10/14/a-curl-audit-by-aisle/

curl was not unusual. GNOME's CVE count went from 37 in 2024 to 97 in 2025 and 141 in the first nine months of 2026, and WebKitGTK reached 305 CVEs in 2026, most of them from AI analysis of the Skia and ANGLE code it bundles.[^catanzaro-gnome] The PHP security team reported publishing 15 CVEs and closing 106 other reports as invalid, duplicate, or not reproducible between July 1 and mid-October 2026.[^php-reports] Pillow's maintainers received 82 reports totaling more than 226,000 words in a single day.[^pillow-reports] The OpenJS Foundation's CNA paused all triage and CVE assignment from September 17 to October 6, 2026, explaining that "AI has lowered the barrier to generating security reports, but not the cost of handling them."[^openjs-cna-break]

[^php-reports]: Alexandre Daubois, "How the PHP Security Team Handles Your Reports," The PHP Foundation, October 14, 2026, https://thephp.foundation/blog/2026/10/14/how-the-php-security-team-handles-your-reports/

[^pillow-reports]: Python Pillow, "Update security policy" (pull request #10018), September 2026, https://github.com/python-pillow/Pillow/pull/10018

[^openjs-cna-break]: OpenJS Foundation CNA, "The OpenJS Foundation CNA is taking a coordinated break," September 11, 2026, https://openjsf.org/blog/the-openjs-foundation-cna-is-taking-a-coordinated-break

### How Projects Are Adapting

Projects have responded by changing their processes rather than trying to filter AI out.

**Pausing intake.** curl stopped accepting vulnerability reports for the entire month of July 2026, the "summer of bliss." Stenberg later called it "possibly our best project decision in a long while"; only one report arrived through a side channel, and no emergency CVE was needed.[^curl-bliss]

**Handling minor issues in public.** From October 26, 2026, curl asks reporters to file anything it would rate LOW or MEDIUM as a public GitHub issue. Only HIGH and CRITICAL reports stay private on HackerOne. Stenberg's reasoning was that over 90% of submissions in recent years were assessed below HIGH, that open discussion lets more people check fixes, and that AI-assisted researchers already pick apart fixes as soon as CVEs are announced. The project accepted the tradeoff of a longer public exposure window for issues that, in Stenberg's words, "are not terrible."[^curl-open] Anil Madhavapeddy made a related observation about OCaml's `cohttp`: probes for a path traversal pattern appeared in his logs within minutes of a public fix, and an agent given only a rough description produced a working exploit in under a minute. For open source, he argued, the embargo window has effectively closed.[^rumour-exploit]

[^curl-bliss]: Daniel Stenberg, "What the bliss taught us," daniel.haxx.se, August 3, 2026, https://daniel.haxx.se/blog/2026/08/03/what-the-bliss-taught-us/

[^curl-open]: Daniel Stenberg, "Do minor curl vulnerabilities in the open," daniel.haxx.se, October 20, 2026, https://daniel.haxx.se/blog/2026/10/20/do-minor-curl-vulnerabilities-in-the-open/

[^rumour-exploit]: Anil Madhavapeddy, "Rumour is the exploit," August 2026, https://anil.recoil.org/notes/rumour-is-the-exploit

**Asking for better reports instead of no AI.** curl, PHP, and Pillow all accept AI-assisted reports if the bug is real. PHP's rule is that "AI-written reports are accepted if the issue reproduces on a PHP build."[^php-reports] Pillow asks reporters to verify LLM findings, deduplicate batches, and check the project's threat model first.[^pillow-reports] Stenberg's guidance asks for a short, human-written first paragraph explaining the problem, a standalone reproducer, a proposed patch where possible, and a reporter who stays available for follow-up.[^curl-excellent] GNOME's Michael Catanzaro went further, urging GNOME maintainers not to ban AI-generated reports: "There is zero hope of maintaining quality software in 2026 without AI vulnerability scanning."[^catanzaro-gnome]

[^curl-excellent]: Daniel Stenberg, "Do excellent vulnerability reports," daniel.haxx.se, June 29, 2026, https://daniel.haxx.se/blog/2026/06/29/do-excellent-vulnerability-reports/

**Rate limits and structure.** In October 2026 GitHub added per-user daily rate limits, a repository-level trusted-reporter allowlist, and structured report forms (configured through `.github/VULNERABILITY_REPORT.yml`) to private vulnerability reporting.[^gh-pvr-limits] Tools such as Scrutineer, which Andrew Nesbitt built for Alpha-Omega, take the opposite approach on the reporter side: every model finding passes through verification and a human review gate before anything reaches a maintainer.[^scrutineer]

[^gh-pvr-limits]: Andrew Nesbitt, "This Week in Package Management: 3 October 2026," https://nesbitt.io/2026/10/03/this-week-in-package-management.html

[^scrutineer]: Andrew Nesbitt, "Scrutineer: scanning open source without flooding maintainers," June 25, 2026, https://nesbitt.io/2026/06/25/scrutineer.html

Chapter 33 covers the broader consequences of AI-driven discovery, including the coalitions and clearinghouses formed in 2026 to absorb it.

### Recommendations

**For maintainers:**

1. **Publish a threat model and a severity policy.** Most invalid or low-value AI reports describe behavior the project already considers out of scope. Writing that down lets you close them by reference.
2. **Decide what you will handle privately.** curl's split (public for LOW and MEDIUM, private for HIGH and CRITICAL) is one workable model. Embargoes protect little when a public fix can be reverse-engineered in minutes.
3. **Require reproducers, not AI disclosure alone.** Disclosure is useful context, but a standalone reproducer is what separates a real bug from a fabricated one.
4. **Use platform controls.** Rate limits, trusted-reporter lists, and structured forms reduce the cost of each report.
5. **Plan for breaks.** A scheduled pause, announced in advance with an emergency channel for actively exploited issues, is better than burning out mid-year.

**For security researchers and AI tool operators:**

1. **Verify before you file.** Run the reproducer yourself. Check the project's threat model and existing issues for duplicates.
2. **Write for one human reader.** Lead with a short explanation in your own words, then the reproducer, then the patch. Long, bullet-heavy reports cost the maintainer time you could have saved.
3. **Bring a fix.** A proposed patch, even an imperfect one, moves the work from "find" to "fix," which is where projects are short of capacity.
4. **Coordinate batches.** If a tool finds dozens of issues in one project, talk to the maintainers about pacing before sending them.

**For platforms and funders:**

1. **Measure verified fixes, not findings.** A program that rewards report volume will get volume.
2. **Fund remediation alongside discovery.** The bottleneck in 2026 was not finding bugs.

### The Broader Implications

The slop problem looked like a filtering problem: separate the fake reports from the real ones. The 2026 flood showed that the deeper issue is capacity. Projects like curl and GNOME are now receiving more valid reports than their volunteer security teams can process, and "ignore the AI reports" is not a safe answer when most of them are right. As Stenberg put it, "we really cannot ignore vulnerability reports."[^stenberg-ignore] For organizations that depend on open source, this means more CVEs per component, more frequent security releases, and more pressure on the patching processes covered in Book 2.

The emergence of slopsquatting and AI slop reports illustrates how new technologies create new attack surfaces. AI coding assistants and AI-powered content generation provide genuine productivity benefits, but they also introduce trust relationships and noise pollution that attackers can exploit. Responsible AI adoption requires acknowledging these risks and implementing appropriate controls. Chapter 10 explores AI-assisted development security in greater depth, including additional considerations beyond slopsquatting.

[nesbitt-slopsquatting]: https://simonwillison.net/2025/Apr/12/andrew-nesbitt/
[spracklen-study]: https://arxiv.org/abs/2406.10279
[socket-slopsquatting]: https://socket.dev/blog/slopsquatting-how-ai-hallucinations-are-fueling-a-new-class-of-supply-chain-attacks
[^stenberg]: Daniel Stenberg, https://daniel.haxx.se/

[^curl]: curl, https://curl.se/

[^curl-slop-report]: HackerOne, "Report #3125832," 2025, https://hackerone.com/reports/3125832

[socket]: https://socket.dev/
[snyk]: https://snyk.io/
![Slopsquatting: AI hallucination exploitation](img/ch-6-slopsquatting-attack.svg)
