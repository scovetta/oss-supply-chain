---
title: "Threat Modeling as a Continuous Practice"
description: "Integrate threat modeling into development workflows so security analysis stays current as dependencies and threats evolve."
icon: "lucide/refresh-cw"
---

# 4.5 Threat Modeling as a Continuous Practice

!!! note "Threat Models Become Obsolete"

    A threat model created once and never revisited quickly becomes obsolete, providing false confidence rather than genuine security insight. Dependencies update continuously. New packages are adopted. Vulnerabilities are discovered. The threat landscape shifts.

Effective supply chain threat modeling must be **continuous**: integrated into development workflows, updated as systems change, and refined as understanding deepens. This section provides practical guidance for making threat modeling an ongoing practice rather than a one-time exercise.

## Integrating Threat Modeling into Development Workflows

Threat modeling should occur at natural decision points in development workflows, not as a separate activity that competes for time and attention.

**Design reviews** are natural integration points for comprehensive threat modeling. When designing new systems or significant features, include supply chain considerations: What new dependencies will this require? What privilege level will those dependencies have? How does this change the attack surface? Design review checklists should include supply chain questions alongside traditional security considerations.

**Dependency addition** warrants threat modeling whenever new packages enter your codebase. Before approving a new dependency, evaluate its criticality (using the framework from Section 4.3), assess maintainer security practices, and consider how it changes your threat profile. This need not be exhaustive for every package, but Tier 1 dependencies deserve meaningful analysis.

**Dependency updates** should trigger review proportional to change scope. Minor version updates of well-established packages may require minimal review. Major version updates, new maintainers, or updates following security incidents warrant closer examination. Automated dependency update tools (Dependabot, Renovate) should be configured to flag updates requiring human attention.

**Release milestones** provide opportunities for periodic review. Before major releases, validate that the threat model reflects current reality. Have new dependencies been added without review? Have previously identified risks been addressed? Release checklists should include threat model currency.

**Incident response** generates threat model updates. When security incidents occur (whether affecting your organization directly or reported in the broader ecosystem), evaluate whether your threat model anticipated the attack pattern. Incidents reveal blind spots; updating models to address them prevents recurrence.

## Lightweight Threat Modeling for Routine Use

Comprehensive threat modeling (constructing detailed attack trees, applying STRIDE to every component, producing extensive documentation) is valuable but resource-intensive. For routine decisions, **lightweight threat modeling** provides security benefit without excessive overhead.

Lightweight approaches include:

!!! tip "Five-Minute Dependency Evaluation"

    1. What does this dependency do, and do we actually need it?
    2. Who maintains it, and what's their security track record?
    3. What privilege level does it require (network, filesystem, build-time execution)?
    4. What happens if it's compromised or becomes unavailable?
    5. Are there alternatives with better security properties?

**Structured questions** rather than formal methodology. For dependency adoption decisions, a simple checklist provides guidance without methodology overhead. Five minutes answering these questions provides meaningful risk awareness without elaborate process.

**Timeboxed analysis** constrains effort while ensuring consideration. Allocate 15-30 minutes to threat model a new dependency or feature. Document what you considered, what concerns emerged, and what you decided. Time constraints prevent analysis paralysis while ensuring security receives attention.

**Abuse cases** focus on how features could be misused rather than comprehensive threat enumeration. For each new capability, ask: "How could an attacker abuse this?" This question-driven approach often surfaces the most relevant threats quickly.

**Threat storming** adapts brainstorming to security. Gather the team for a brief session focused on a specific component or change. What could go wrong? How might attackers exploit this? Five people spending 20 minutes together often generate insights that individual analysis misses.

## When to Apply Comprehensive vs. Lightweight Approaches

Not every situation warrants the same level of analysis. Matching approach intensity to risk level ensures security investment goes where it matters.

!!! info "Match Analysis Intensity to Risk Level"

    **Comprehensive**: Initial architecture design, Tier 1 dependencies, build infrastructure changes, major incidents, periodic reviews
    
    **Lightweight**: Routine dependency updates, Tier 2-3 dependencies, minor features, regular development activities
    
    **Minimal**: Tier 4 dependencies (dev tools), patch-level updates, dependencies covered by organizational standards

**Minimal review** may suffice for:

- Tier 4 dependencies (development tools, test utilities)
- Patch-level updates to stable dependencies
- Dependencies already covered by organizational standards

The key is proportionality: invest analysis effort where risk is highest, and accept that not everything can receive deep scrutiny.

## Maintaining and Versioning Threat Models

Threat models are documents that describe understanding at a point in time. As systems and threats evolve, models must evolve with them.

**Version control** threat models alongside code. Store threat model documents, attack trees, and analysis artifacts in your repository. This enables tracking changes over time, connecting model updates to code changes, and reviewing model evolution alongside technical evolution.

**Tag models to releases** when producing threat model documentation. A threat model tagged to version 2.0 describes the threat landscape at that release. When security questions arise later, having historical models provides context.

**Maintain living documents** for ongoing systems. Rather than producing new documents for each review, update existing models to reflect current state. Track changes through version control, but keep the canonical model current.

**Record decisions and rationale**, not just conclusions. When you decide to accept a risk, document why. When you choose one dependency over alternatives, record the security considerations that informed the choice. This rationale becomes valuable when revisiting decisions later or when team members change.

**Schedule periodic reviews** to ensure models don't drift too far from reality. Even without specific triggers, reviewing threat models quarterly or semi-annually catches gradual drift that individual changes might not surface.

## Training Developers in Supply Chain Threat Thinking

Threat modeling works best when distributed across the team rather than concentrated in a security group. Developers who understand supply chain threats make better decisions daily (about dependencies, about configurations, about trust assumptions) than developers who defer all security thinking to specialists.

Effective training approaches include:

**Scenario-based learning** teaches through realistic examples. Walk through actual supply chain incidents (event-stream, SolarWinds, XZ Utils) and discuss how threat modeling might have identified risks. Concrete examples are more memorable than abstract principles.

**Hands-on exercises** build practical skills. Have developers threat model a familiar system, then compare results and discuss differences. Practice builds competence that training alone cannot provide.

**Integration with existing training** embeds supply chain awareness into developer onboarding and ongoing education. Security training should include supply chain material alongside traditional application security topics.

**Champion programs** develop deep expertise in interested team members. Security champions receive additional training and serve as resources for their teams, scaling security knowledge without requiring every developer to become a security specialist.

**Just-in-time guidance** provides resources when needed. Checklists for dependency adoption, decision trees for update review, and templates for threat analysis support developers making security decisions in context.

The goal is not making every developer a threat modeling expert but building sufficient awareness that security considerations enter routine decisions naturally.

## Documentation and Knowledge Sharing

Threat modeling produces insights that should inform decisions beyond the immediate analysis. Effective documentation and sharing amplify the value of threat modeling investment.

**Standardize formats** to enable comparison and aggregation. Consistent templates for dependency evaluation, attack trees, and risk decisions allow patterns to emerge across analyses and simplify review.

**Create searchable repositories** of past analyses. When adopting a dependency similar to one previously evaluated, prior analysis provides starting points. When incidents occur, searching for related analysis reveals whether risks were anticipated.

**Share findings across teams** to prevent duplicated effort and propagate learning. A threat model created by one team may inform decisions for other teams using similar technologies.

**Connect models to action tracking** by linking identified risks to remediation work. Threat models that produce findings but no action provide limited value. Integration with issue tracking ensures identified risks receive appropriate follow-up.

**Tools for collaboration** should match organizational practices. Options include:

- Wiki platforms (Confluence, Notion) for narrative documentation
- Diagramming tools (Threat Dragon, diagrams.net) for visual models
- Issue trackers (Jira, GitHub Issues) for action item management
- Code repositories for version-controlled documents
- Specialized tools (IriusRisk, ThreatModeler) for organizations with extensive threat modeling programs

Simple tools used consistently outperform sophisticated tools used sporadically. Choose tools your team will actually use.

## Recommendations for Getting Started

For organizations beginning to integrate continuous threat modeling:

1. **Start with high-risk decisions.** Apply lightweight threat modeling to new Tier 1 dependency adoptions and significant infrastructure changes. Build capability before expanding scope.

2. **Create simple checklists.** Develop team-specific questions for dependency adoption and update review. Five good questions consistently asked provide more value than comprehensive methodologies inconsistently applied.

3. **Integrate with existing ceremonies.** Add supply chain considerations to design reviews, sprint planning, or release processes you already conduct. Leverage existing meetings rather than creating new ones.

4. **Document decisions, not just analyses.** Record what you decided and why. This documentation proves valuable when revisiting decisions and when onboarding new team members.

5. **Learn from incidents.** When supply chain incidents occur, whether affecting your organization or reported publicly, review your threat model. Would it have anticipated this attack? Update models to address blind spots.

6. **Train incrementally.** Start with awareness training for all developers, deeper training for security champions, and specialist training for security team members. Build capability progressively.

7. **Review periodically.** Schedule quarterly or semi-annual reviews of threat models for critical systems. Regular review catches drift and ensures models remain useful.

Continuous threat modeling is not about perfection but about consistently applying security thinking to supply chain decisions. Organizations that integrate lightweight analysis into routine workflows develop better security intuition and make better decisions than those that rely solely on periodic comprehensive exercises. The goal is building a practice that improves security sustainably, not creating a burden that teams abandon under delivery pressure.

Chapters 5-10 examine specific attack patterns in detail, providing concrete threats that threat models should address. Book 2 presents risk management frameworks that connect threat model findings to organizational decision-making. The threat modeling practices established in this chapter provide the analytical foundation for both defensive prioritization and risk-based resource allocation.
