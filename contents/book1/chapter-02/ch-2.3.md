---
title: "The Maintainer Crisis"
description: "Understand the human reality behind open source: unpaid maintainers facing burnout who represent a critical supply chain risk."
icon: "lucide/alert-triangle"
---

# 2.3 The Maintainer Crisis

Behind every open source package is a human being. Sometimes it is a team of humans, sometimes a well-funded corporate initiative, but remarkably often it is a single individual who created something useful and shared it with the world: never anticipating that their weekend project would become critical infrastructure for thousands of organizations. The sustainability challenges facing these maintainers constitute one of the most significant systemic risks to software supply chain security. Burned-out maintainers make mistakes, delay security patches, or abandon projects entirely. Overwhelmed maintainers become vulnerable to social engineering by attackers offering to "help." Understanding this crisis is essential for anyone seeking to secure the software supply chain.

## Who Maintains Open Source Software?

The popular image of open source development, vibrant communities collaborating to build software, obscures a more precarious reality. According to [Tidelift's 2023 State of the Open Source Maintainer report][tidelift-2023], 60% of maintainers describe themselves as unpaid hobbyists. Only 26% are paid for some or all of their maintenance work. The majority maintain projects in their spare time, alongside full-time employment in unrelated roles.

The demographics skew heavily: GitHub's 2023 survey found that 91% of open source contributors identify as male, and participation is concentrated in North America and Europe. Maintainers tend to be experienced developers (the same Tidelift survey found that 65% have more than 16 years of programming experience) who built tools to solve their own problems and shared them publicly.

Motivations for maintaining open source software vary. Some maintainers are driven by intellectual satisfaction: the joy of building something elegant and useful. Others value the reputation and community connections that come with maintaining popular projects. Some hope that open source work will lead to employment opportunities or establish thought leadership in their field. Very few started with the expectation of financial compensation, though that expectation is slowly changing.

This profile (experienced but unpaid, motivated by intrinsic rewards, working in spare time) shapes how maintenance actually happens. When a security vulnerability is reported, the maintainer must find time outside their day job to understand the issue, develop a fix, test it, and release an update. When dependencies break, the maintainer must troubleshoot compatibility issues. When users file bug reports or feature requests, the maintainer must triage and respond. All of this happens in time carved from evenings, weekends, and lunch breaks.

## The Sustainability Crisis

The gap between what maintainers provide and what they receive has reached crisis proportions. Projects that power billions of dollars in commercial software are maintained by individuals who receive nothing in return, not money, not recognition, often not even a thank-you.

The [2024 Tidelift survey][tidelift-2024] found that 60% of maintainers have quit or considered quitting their maintainer role. The primary reasons cited were lack of financial compensation (44%), lack of time (42%), and burnout or stress (36%). These factors compound: maintainers who aren't paid must fit maintenance into limited free time, creating stress that leads to burnout.

Nadia Eghbal, in her influential book *Working in Public: The Making and Maintenance of Open Source Software*, documented how the nature of maintenance has changed. Early open source projects often had active contributor communities who shared maintenance burden. Today's popular packages more often have passive user bases: millions of downloads, but contributions come from a tiny fraction of users. The maintainer becomes less a community organizer and more a service provider: expected to respond to issues, fix bugs, and support users, but without the distributed effort that early open source advocates imagined.

> "Being a core maintainer is interesting: people tend to look to you for help even though you usually feel like you don't know what's going on. There are plenty of technical challenges but because it's important to think more long term, a lot of it is around the sustainability of not really the project but the people behind it (regarding overwork, funding, burnout)."
> — [Henry Zhu][henry-zhu], Babel maintainer

This sentiment, expressed in various forms by maintainers across the ecosystem, reflects a fundamental imbalance. The value flows in one direction, from maintainer to user, while recognition and support rarely flow back.

## The Infrastructure Layer: Registry Sustainability

The sustainability crisis extends beyond individual maintainers to the infrastructure they depend on. Package registries (PyPI, Maven Central, crates.io, npm, RubyGems) serve as the distribution backbone of the software supply chain. These registries handle billions of downloads monthly, yet they face the same structural funding problems as the maintainers who publish to them.

In September 2025, eleven major organizations issued a joint statement titled "Open Infrastructure is Not Free" that laid bare the precariousness of this infrastructure.[^joint-statement] The signatories included the Python Software Foundation (which operates PyPI), the Rust Foundation (crates.io), Sonatype (Maven Central), Ruby Central, the OpenJS Foundation, the Eclipse Foundation, and the Open Source Security Foundation. Their collective warning was stark:

> "Billion-dollar ecosystems cannot stand on foundations built of goodwill and unpaid weekends."

The statement identified several compounding pressures. Modern package registries must provide global distribution with low latency, cryptographic signature verification, continuous integration support, zero-downtime availability, rapid security response, and increasingly, regulatory compliance. These are enterprise-grade expectations imposed on organizations operating largely on donations and volunteer effort.

The incentive misalignment mirrors the maintainer crisis but at institutional scale. As the statement noted, "a small number of organizations absorb the majority of infrastructure costs, while the overwhelming majority of large-scale users...consume these services without contributing." Commercial entities extract enormous value (their CI/CD pipelines pull packages continuously, their products depend on registry availability) yet few contribute proportionally to operational costs.

Two trends identified in the statement threaten to accelerate the crisis. First, **generative AI is driving explosive growth in automated package consumption**. AI coding assistants and automated dependency management tools issue machine-driven requests at scales that dwarf human usage patterns, often with redundant or wasteful access patterns. Second, **commercial vendors increasingly distribute proprietary software through public registries**, effectively using community infrastructure as free global content delivery networks for commercial products.

The security implications are significant. Underfunded registries struggle to implement security measures that well-resourced organizations take for granted: comprehensive malware scanning, typosquatting detection, account security enforcement, and incident response capabilities. When npm, PyPI, or Maven Central suffers a security incident, the blast radius encompasses virtually the entire software industry.

Alpha-Omega, a project of the Open Source Security Foundation, has funded security improvements across multiple package managers since 2022 but acknowledged that grants alone cannot solve the structural problem.[^alpha-omega-statement] The organization began hosting roundtable discussions with registry operators to explore sustainable funding models, approaches where revenue scales with usage, that might align costs with consumption without compromising open access for individual developers and small projects.

The joint statement represents a watershed moment: the organizations responsible for critical infrastructure publicly acknowledging that the current model is unsustainable. Whether the industry responds with meaningful investment or continues extracting value until systems fail remains an open question.

[^joint-statement]: Open Source Security Foundation et al., "Open Infrastructure is Not Free: A Joint Statement on Sustainable Stewardship" (September 2025). https://openssf.org/blog/2025/09/23/open-infrastructure-is-not-free-a-joint-statement-on-sustainable-stewardship/
[^alpha-omega-statement]: Alpha-Omega, "Alpha-Omega Endorses the Joint Statement on Sustainable Stewardship" (September 2025). https://alpha-omega.dev/blog/alpha-omega-endorses-the-joint-statement-on-sustainable-stewardship/

## The Bus Factor

The **bus factor** (sometimes called the "truck factor" or, more positively, the "lottery factor") measures how many team members would need to be incapacitated before a project could no longer be maintained. A bus factor of one means a single person's departure would leave the project unmaintained.

The [Linux Foundation's Census II study][census-ii], analyzing the most widely deployed open source components, found alarming concentration of maintenance responsibility. The study found that among the most critical packages, many had only one or two developers responsible for 90% or more of commits. For example, the study identified packages with hundreds of millions of downstream dependents where a single developer accounted for the vast majority of commits over the preceding year.

This concentration creates acute supply chain risk. When key maintainers become unavailable (whether due to illness, job changes, burnout, or death), dependent projects and organizations face difficult choices. They can attempt to fork and maintain the project themselves, search for alternative packages, or simply hope someone else steps up. None of these options is satisfactory for critical infrastructure.

The 2016 left-pad incident illustrated how even tiny packages can have outsize impact. When developer Azer Koçulu removed his packages from npm following a dispute, the 11-line left-pad package disappeared from the registry. Thousands of builds broke instantly because left-pad was a transitive dependency of popular packages like Babel and React. The incident revealed how dependent the JavaScript ecosystem had become on packages maintained by single individuals with no obligation to continue providing them.

## Case Study: core-js and the Limits of Volunteerism

The story of core-js illustrates the maintainer crisis in painful detail. Core-js is a JavaScript polyfill library that provides compatibility for modern JavaScript features in older browsers. According to npm download statistics (as of 2023), it is downloaded over 30 million times per week and is a dependency of projects used by virtually every major website and application. It is, by any measure, critical infrastructure for the web.

Denis Pushkarev has been the sole significant maintainer of core-js for years. According to Pushkarev, in 2019 he was sentenced to prison in Russia following a car accident that resulted in a fatality.[^pushkarev-sentence] He continued attempting to maintain the project from prison, but capacity was obviously limited. The project fell behind on compatibility updates and security review.

After his release, Pushkarev published a lengthy blog post describing the situation. Despite core-js being essential infrastructure, he had received minimal financial support. His attempts to fund development through Patreon and Open Collective generated modest income: nowhere near enough to sustain full-time work on a project of this complexity and importance. Meanwhile, the companies profiting from core-js (and the websites serving billions of users through code that depended on it) contributed nothing.

Pushkarev's post included pointed observations about the sustainability of open source:

> "I'm the only maintainer of core-js—a project used by most popular websites. I don't receive proper financial support. A few months ago, I was in prison. Now I'm returning to the project and I need your support."
> — [Denis Pushkarev][core-js-post], core-js maintainer

The response to Pushkarev's appeal was mixed. Some donations increased. Some users complained about the npm installation messages he added soliciting support. Few of the large companies depending on core-js established meaningful sponsorship. The project continues, maintained largely by one person whose life circumstances have been extraordinarily difficult, with minimal institutional support for work that underpins the modern web.

## Case Study: XZ Utils and the Social Engineering of Burnout

!!! danger "Weaponizing Maintainer Burnout"

    The XZ Utils compromise demonstrated how attackers exploit maintainer exhaustion. Sock puppet accounts pressured the overwhelmed maintainer for faster updates while a patient attacker spent two years building trust through helpful contributions. When granted co-maintainer access, the attacker introduced a sophisticated backdoor.

The XZ Utils compromise of 2024 demonstrated how attackers can exploit maintainer exhaustion. XZ Utils is a compression library included in essentially every Linux distribution. Its maintainer, Lasse Collin, had maintained the project for years as a solo effort alongside other responsibilities.

The attack began with social pressure. Accounts that appear to have been sock puppets began pressuring Collin for faster updates and more responsive maintenance. A new contributor, operating under the name "Jia Tan," appeared and began making helpful contributions. Over two years, Jia Tan built trust through sustained, legitimate contribution, exactly the kind of help an overwhelmed maintainer might welcome.

Other accounts continued pressuring Collin, complaining about slow responses and suggesting he hand over more responsibility. Collin, dealing with mental health challenges he had openly discussed, eventually granted Jia Tan co-maintainer status. This was a reasonable decision for a burned-out maintainer receiving genuine help.

With maintainer access secured, Jia Tan introduced a sophisticated backdoor through a series of obfuscated changes to the build process. The backdoor (CVE-2024-3094) would have provided unauthorized access to systems running SSH with the affected XZ library, potentially millions of servers.[^xz-cve] Only the chance observation of unusual latency by Microsoft engineer Andres Freund prevented the compromise from reaching stable Linux distributions.[^xz-timeline]

The XZ Utils attack was not primarily a technical exploitation. It was a social engineering attack that exploited the predictable vulnerability of solo maintainers: they are overwhelmed, they need help, and they have limited capacity to vet those who offer it. The attacker weaponized the maintainer crisis itself.

## Mental Health and Harassment

The burdens on maintainers extend beyond time and money. Maintaining popular open source software can be psychologically taxing in ways that receive insufficient attention.

Maintainers report experiencing harassment, entitlement from users, and unrelenting demands. The [Tidelift survey][tidelift-2024] found that 59% of maintainers have experienced burnout, and 35% have experienced harassment or toxic interactions related to their open source work. When a maintainer makes a decision users disagree with (changing a license, deprecating a feature, declining to add functionality), they may face abuse in issue trackers, on social media, and sometimes via direct communication.

The always-on nature of open source amplifies stress. Issues can be filed at any hour from any timezone. Security vulnerabilities may demand immediate response regardless of the maintainer's other commitments. The public nature of open source development means that every mistake, delayed response, or unpopular decision is visible to anyone who cares to look.

Several prominent maintainers have spoken publicly about the toll. André Staltz, creator of Cycle.js and contributor to other JavaScript projects, wrote about stepping back due to burnout and the emotional weight of feeling responsible for users' problems. Rich Hickey, creator of Clojure, published an essay titled ["Open Source is Not About You"][hickey-oss] pushing back against user entitlement, arguing that open-sourcing code is "a licensing and delivery mechanism, period", not an implicit social contract obligating maintainers to accept contributions or fulfill feature requests. The frequency of such statements suggests systemic rather than individual problems.

Lasse Collin's openness about mental health challenges before the XZ attack was notable. He had mentioned on mailing lists that mental health issues affected his capacity to maintain the project. This candor, admirable in itself, may have provided information that attackers used to target him.

## The Security Implications

Every dimension of the maintainer crisis has security implications.

**Burned-out maintainers make mistakes.** Security review requires attention, time, and care. A maintainer racing to process a backlog of pull requests may not scrutinize each change as thoroughly as ideal. Fatigue-induced errors in security-critical code can introduce vulnerabilities.

**Overwhelmed maintainers delay patches.** When a security vulnerability is reported, the maintainer must understand the issue, develop a fix, test it across supported versions, and coordinate disclosure. A maintainer with limited time may take weeks to address issues that a well-resourced team would fix in days.

**Desperate maintainers accept dangerous help.** As XZ Utils demonstrated, maintainers who need assistance may grant commit access or release privileges without the vetting that well-resourced organizations would apply. Attackers understand this and specifically target projects with overwhelmed maintainers.

**Abandoned projects remain in use.** When maintainers walk away from projects, the code doesn't disappear from applications that depend on it. Vulnerabilities discovered in abandoned packages may never be fixed, leaving dependent applications permanently exposed. The time between vulnerability disclosure and fix becomes infinite.

**Unmaintained projects lack security infrastructure.** Projects without active maintenance typically lack security reporting processes, vulnerability response plans, or the continuous review needed to detect malicious contributions. They become attractive targets for attackers seeking entry points into the supply chain.

## The Path Forward

The maintainer crisis will not be solved by exhorting maintainers to work harder or users to be more grateful. The scale mismatch between open source consumption and contribution is structural, rooted in economic incentives that favor extraction over investment.

Subsequent chapters explore approaches to this challenge: economic models that might fund sustainable maintenance (Book 3, Chapter 30), corporate programs that support dependencies (Book 3, Chapter 29), and foundation initiatives that provide infrastructure for under-resourced projects. Book 3, Chapter 24 provides direct guidance for maintainers seeking to protect themselves and their projects.

For now, the essential insight is that supply chain security is inseparable from maintainer sustainability. Organizations cannot secure software that no one is maintaining. They cannot expect volunteer maintainers, working nights and weekends without compensation, to provide enterprise-grade security responses. The humans behind the code are not inexhaustible resources to be consumed but essential participants in the ecosystem whose wellbeing directly affects everyone who depends on their work.

[henry-zhu]: https://jsnation.medium.com/henry-zhu-open-source-is-about-ones-soul-searching-and-moving-the-whole-community-6a087cf3d003
[tidelift-2023]: https://tidelift.com/open-source-maintainer-survey-2023
[tidelift-2024]: https://www.tidelift.com/open-source-maintainer-survey-2024
[census-ii]: https://www.linuxfoundation.org/research/census-ii-of-free-and-open-source-software-application-libraries
[core-js-post]: https://github.com/zloirock/core-js/blob/master/docs/2023-02-14-so-whats-next.md
[hickey-oss]: https://gist.github.com/richhickey/1563cddea1002958f96e7ba9519972d9

[^xz-cve]: NVD, "CVE-2024-3094: XZ Utils Backdoor." https://nvd.nist.gov/vuln/detail/CVE-2024-3094
[^xz-timeline]: Evan Boehs, "Everything I Know About the XZ Backdoor" (March 2024). https://boehs.org/node/everything-i-know-about-the-xz-backdoor; see also Sam James's comprehensive timeline: https://gist.github.com/thesamesam/223949d5a074ebc3dce9ee78baad9e27
[^pushkarev-sentence]: Denis Pushkarev, "So, what's next?" (February 2023). https://github.com/zloirock/core-js/blob/master/docs/2023-02-14-so-whats-next.md

![The open source maintainer crisis: statistics on burnout and security implications](img/ch-2-maintainer-crisis.svg)
