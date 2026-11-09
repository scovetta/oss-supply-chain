---
title: "Browser Extension Supply Chains"
description: "Understand how browser extensions' extraordinary access creates potent attack vectors distributed through trusted channels."
icon: "lucide/puzzle"
---

# 9.2 Browser Extension Supply Chains

Browser extensions operate with extraordinary access to users' online activities. An extension with appropriate permissions can read every webpage you visit, capture form data including passwords, modify page content, intercept network requests, and access browsing history. This access makes extensions powerful productivity tools, and potent attack vectors. When extension supply chains are compromised, attackers gain capabilities that rival sophisticated malware, distributed through trusted channels and automatically updated to millions of users.

!!! danger "Extraordinary Access"

    A single permission like `<all_urls>` grants an extension access to content on **all websites**, including banking, email, and corporate applications. This access persists through ownership changes and updates.

The browser extension ecosystem combines the dependency risks familiar from package managers with unique factors: extremely broad permissions, automatic updates, and direct access to users' most sensitive online activities.

## The Extension Ecosystem Landscape

Major browsers operate extension marketplaces with varying security models:

**Chrome Web Store:**

Google's [Chrome Web Store][chrome-web-store] dominates with approximately 110,000-140,000 extensions available (as of 2024). Chrome extensions use the Chromium extension architecture, which also underlies Microsoft Edge and other Chromium-based browsers. Chrome's market dominance makes its extension ecosystem the primary target for supply chain attacks.

Chrome enforces review processes for new extensions and updates, but the scale of submissions (thousands daily) limits review depth. Extensions are distributed as CRX packages and can include JavaScript, HTML, CSS, and in some cases, native code.

**Firefox Add-ons:**

Mozilla's add-on ecosystem uses the WebExtensions API, largely compatible with Chrome's extension architecture. Firefox maintains stricter review processes, including human review of source code for featured extensions.

Firefox has historically emphasized user privacy, reflected in extension policies that restrict certain behaviors permitted in Chrome.

**Microsoft Edge Add-ons:**

Since Edge migrated to Chromium, its extension ecosystem has aligned with Chrome's. Edge accepts most Chrome extensions with minimal modification. Microsoft maintains its own add-on store but the technical architecture mirrors Chrome.

## The Unique Risks of Extension Permissions

Extensions declare required **permissions** in their manifest files. Users must grant these permissions during installation. However, permission models have significant limitations:

**Permission Scope:**

A single permission can grant extensive access:

- `<all_urls>` or `*://*/*`: Access to content on all websites
- `tabs`: Access to browser tabs, including URLs and titles
- `webRequest`: Ability to intercept and modify network requests
- `storage`: Ability to store data locally (useful for persistence)
- `cookies`: Access to browser cookies across sites

Many legitimate extensions require broad permissions. An ad blocker needs network request access to block ads. A password manager needs access to all pages to fill forms. This creates a permissions model where dangerous access appears routine.

**Permission Creep:**

Extensions may request minimal permissions initially, then expand permissions in updates. Users who approved an extension for one purpose may not notice when an update requests additional access.

**Implicit Trust:**

Users rarely read permission requests carefully. A popular, well-reviewed extension is assumed to be safe. This trust persists through ownership changes and updates, exactly the trust that supply chain attacks exploit.

## Extension Acquisition and Account Hijacking

Attackers frequently target extension developers and their accounts rather than building malicious extensions from scratch:

**Developer Account Compromise:**

Extension developer accounts, like package registry accounts, can be compromised through:

- Credential stuffing from password reuse
- Phishing targeting developers
- Session hijacking
- Social engineering of store support

Once an account is compromised, attackers can push malicious updates to all installed instances.

**Extension Purchase:**

Attackers purchase extensions from developers willing to sell. A popular extension with thousands of users has monetary value. Developers may sell for a few thousand dollars, not realizing (or not caring) that buyers intend malicious use.

Purchased extensions are not compromised: they're legitimately transferred. Users receive no notification of ownership changes.

**Abandoned Extension Takeover:**

When developers abandon extensions, browsers may allow new developers to claim them through various processes. Attackers monitor for abandoned popular extensions and request transfer.

## Case Studies

**The Great Suspender (2021):**

**The Great Suspender** was a popular Chrome extension with over 2 million users.[^great-suspender-users] It suspended inactive tabs to save memory, a genuinely useful function.

!!! example "The Acquisition Attack Pattern"

    The Great Suspender was sold in June 2020. Malicious updates appeared in October 2020. Google removed it in February 2021. **Eight months** of malicious operation in 2 million browsers.

In June 2020, the original developer sold the extension to an unknown entity. The new owners pushed updates in October 2020 that:

- Added analytics code tracking user behavior
- Loaded remote scripts from third-party servers
- Included code capable of executing arbitrary JavaScript

In February 2021, [Google removed the extension][great-suspender] from the Chrome Web Store and disabled it in users' browsers, a relatively rare action that signaled serious concern, with Google's warning stating bluntly that the extension contained malware.

The delay between ownership change (June 2020) and removal (February 2021) meant malicious code operated in millions of browsers for months.

**MEGA.nz Extension Compromise (2018):**

The official **MEGA.nz** Chrome extension was compromised through developer account access. Attackers pushed an update that:

- Stole credentials for sites including Amazon, Microsoft, GitHub, and Google
- Exfiltrated cryptocurrency wallet keys
- Captured login forms on banking sites

!!! warning "Speed of Compromise"

    The MEGA.nz malicious version was available for only **4 hours**, yet 1.5 million users were exposed during any browsing in that period. Browser extensions auto-update silently.

The malicious version was available for [approximately 4 hours][mega-incident] before detection. MEGA.nz confirmed that an attacker had compromised their Chrome Web Store account and uploaded a malicious version of the extension.

Despite the short window, the extension's 1.5 million users were exposed during any browsing in that period.

**Nano Defender and Nano Adblocker (2020):**

In October 2020, the developers of **Nano Defender** and **Nano Adblocker** (popular ad-blocking extensions with 300,000+ users combined) sold the extensions to undisclosed new owners.

Within days, the new owners pushed updates that:

- Harvested browser data
- Injected code into Instagram and other social media sites
- Exfiltrated user credentials

The ad-blocking function continued normally, disguising the malicious additions.

**Urban VPN AI Conversation Harvesting (2025):**

In July 2025, security researchers discovered that **Urban VPN Proxy** and seven related extensions were secretly capturing and selling AI conversations from over 8 million users across Chrome and Edge.[^urban-vpn]

The extensions, marketed as privacy and security tools, operated a massive surveillance operation targeting eight AI platforms: ChatGPT, Claude, Gemini, Microsoft Copilot, Perplexity, DeepSeek, Grok, and Meta AI. The harvesting mechanism was sophisticated:

1. When users visited AI platforms, the extension injected scripts that intercepted `fetch()` and `XMLHttpRequest`
2. All prompts, responses, timestamps, and conversation IDs were extracted before rendering
3. Data was compressed and transmitted to Urban VPN's analytics servers
4. The collected data was sold to data brokers for "marketing analytics purposes"

!!! warning "Featured Badges Failed"

    All affected extensions carried "Featured" badges from Google and Microsoft, suggesting they had passed human review. The badges failed to detect this malicious activity despite platform policies prohibiting data broker transfers.

The harvesting functionality was hardcoded with no user toggle and operated regardless of VPN connection status. Users who installed before July 2025 received no updated consent prompts: the auto-update mechanism silently added harvesting capabilities.

This incident demonstrates a concerning evolution: extensions that specifically target AI interactions as high-value data. Users sharing sensitive information, business strategies, or personal details with AI assistants had no indication their conversations were being captured and sold.

[^urban-vpn]: Koi Security Research, "Urban VPN: 8 Million Users' AI Conversations Harvested and Sold," 2025, https://www.koi.ai/blog/urban-vpn-browser-extension-ai-conversations-data-collection

**Fake ClawdBot VS Code Extension (2026):**

In January 2026, a VS Code extension branded "ClawdBot Agent" was discovered to be a trojan that installed ScreenConnect RAT on victim machines.[^aikido-clawdbot-ext] The extension appeared in the marketplace before the legitimate Clawdbot project (an open-source AI personal agent later renamed to Moltbot, then OpenClaw) had published any official extension. This is a form of **namesquatting**: attackers claim a project's name in a distribution channel before the project itself does.

The attack exploited the brand confusion surrounding Clawdbot's rapid renames (Clawdbot → Moltbot → OpenClaw). Users searching for official tooling had no reliable way to distinguish the trojan from a legitimate extension. The case illustrates that extension marketplace impersonation is particularly effective when targeting viral, fast-moving projects where users do not know what "official" looks like yet. For the broader OpenClaw supply chain attack campaign (including malicious skills, a one-click RCE vulnerability, and an agent platform database exposure) see Section 10.3.

[^aikido-clawdbot-ext]: Aikido Security, "Fake Clawdbot VS Code Extension Installs ScreenConnect RAT," January 2026, https://www.aikido.dev/blog/fake-clawdbot-vscode-extension-malware

## Manifest V3: Security Implications

Google introduced **Manifest V3** as a major revision to the Chrome extension platform, with significant security implications:

**Key Security Changes:**

- **Service workers replace background pages**: Extensions can no longer run persistent background scripts. This limits certain attack patterns that relied on persistent execution.

- **Remote code execution restrictions**: Extensions cannot execute remotely-hosted code. All code must be included in the extension package at review time.

- **DeclarativeNetRequest replaces webRequest blocking**: Network request modification uses a declarative API with predefined rules rather than arbitrary JavaScript.

- **Host permission changes**: Extensions must request access to specific hosts or receive permission through user gesture.

**Security Benefits:**

The remote code restriction is particularly significant for supply chain security. Previous attacks often used minimal extension code that downloaded and executed payloads from remote servers: code that could change after review. Manifest V3 requires all executable code to be present during review.

**Limitations and Criticism:**

Critics argue Manifest V3's restrictions are primarily designed to limit ad blockers rather than improve security:

- Sophisticated attackers can still obfuscate code to evade review
- The declarative network request API limits legitimate security extensions
- Delayed enforcement timelines have reduced adoption incentives

Manifest V3 improves the security baseline but does not eliminate supply chain risks.

## Detection Challenges

Identifying malicious extensions presents significant challenges:

**Obfuscation:**

Malicious code can be obfuscated to evade automated analysis and human review:

- Minification makes code difficult to read
- String encoding hides suspicious patterns
- Dead code and irrelevant functions obscure malicious logic
- Code spread across multiple files

**Delayed Activation:**

Extensions can behave legitimately for extended periods before activating malicious functionality:

- Time-based triggers that activate after review period
- Geographic triggers targeting specific regions
- User count triggers that activate only at scale
- Server-controlled activation flags

**Legitimate Functionality Mixed with Malicious:**

Extensions that perform genuine useful functions (ad blocking, tab management) while also performing malicious actions are harder to detect than purely malicious extensions.

**Update Dynamics:**

Review processes may scrutinize initial submissions more than updates. Attackers submit legitimate extensions, build user bases, then introduce malicious code in updates.

**Detection Approaches:**

- **Static analysis**: Examining extension code for suspicious patterns
- **Dynamic analysis**: Running extensions in sandboxed environments to observe behavior
- **Network monitoring**: Identifying suspicious network connections
- **Permission analysis**: Flagging extensions with excessive permissions
- **Behavioral comparison**: Detecting when updates significantly change behavior

Tools like **[Spin.AI][spinai]** provide enterprise visibility into extension risks. (Note: CRXcavator, previously a leading tool in this space, has been discontinued.)

## Enterprise Extension Management

Organizations face particular challenges with browser extensions:

**Risk Landscape:**

- Employees install extensions on corporate browsers
- Extensions have access to corporate applications and data
- Shadow IT means security teams may not know what's installed
- Supply chain compromises affect all devices with the extension

**Chrome Enterprise Controls:**

Chrome Enterprise provides extension management capabilities:

- **Allowlists**: Permit only specific approved extensions
- **Blocklists**: Prevent specific extensions from installation
- **Force-install**: Automatically deploy specific extensions
- **Permission restrictions**: Block extensions requesting certain permissions

**Group Policy and Configuration:**

Enterprise management tools can enforce:

- Extension source restrictions (only from managed store)
- Permission-based blocking (no extensions with `<all_urls>`)
- Automatic removal of removed-from-store extensions

**Challenges:**

- Overly restrictive policies reduce productivity
- Users may resist restrictions
- Allowlisting requires ongoing maintenance
- Legacy extensions may lack modern security features

## IDE Extension Marketplaces: A Distinct Ecosystem

While browser extensions target end users' browsing activity, **IDE extension marketplaces** represent a related but distinct supply chain ecosystem that targets *developers specifically*, and the consequences of compromise are correspondingly more severe for upstream software security.

The two major IDE extension distribution channels are:

- **VS Code Marketplace**: Microsoft's official marketplace for Visual Studio Code, the most widely used code editor. Extensions are reviewed but run without sandbox isolation.
- **Open VSX**: The Eclipse Foundation's open-source alternative, used by VSCodium, Eclipse Theia, Gitpod, and other editors. Open VSX operates with a different governance model and historically lighter publisher vetting.

**How IDE Extensions Differ from Browser Extensions:**

| Dimension | Browser Extensions | IDE Extensions |
|-----------|-------------------|----------------|
| **Primary victims** | End users (consumers) | Developers (producers) |
| **Access scope** | Web browsing data, cookies | Source code, credentials, terminal, file system |
| **Sandbox model** | Partial (permission-gated) | None — full IDE privileges |
| **Supply chain leverage** | Steals user data | Enables upstream attacks via stolen developer credentials |
| **Update model** | Auto-update via browser | Auto-update via editor |
| **Manifest version controls** | Chrome Manifest V3 restrictions | No equivalent restriction framework |

The critical difference is **supply chain leverage**: a compromised browser extension steals passwords and browsing data from end users. A compromised IDE extension steals signing keys, registry tokens, SSH keys, and cloud credentials from developers: enabling the attacker to publish malicious packages, tamper with CI/CD pipelines, or access production infrastructure. A single compromised developer can become the vector for an upstream supply-chain attack affecting thousands of downstream users.

**GlassWASM and the Open VSX Attack Pattern:**

The GlassWASM campaign demonstrated this risk concretely. In June 2026, Socket researchers found TinyGo-compiled WebAssembly malware embedded in trojanized Open VSX extensions, including a theme and a transaction-hash debugging utility.[^glasswasm-openvsx] The extensions auto-executed on activation, used obfuscation and Solana transaction memos to resolve command-and-control infrastructure, and delivered staged payloads into developer environments.

[^glasswasm-openvsx]: Socket Security, "GlassWASM: WebAssembly Malware Found in Trojanized Open VSX Extensions," June 15, 2026, https://socket.dev/blog/glasswasm-malware-open-vsx-extensions

**Recommendations for IDE Extension Ecosystems:**

1. **Treat IDE extensions as supply chain dependencies.** Apply the same governance (allowlisting, review, monitoring) that you apply to npm or PyPI packages.
2. **Inventory all IDE extension distribution channels.** Organizations may not realize that VSCodium, Gitpod, or Eclipse Theia installations pull from Open VSX rather than the VS Code Marketplace.
3. **Monitor for publisher credential compromise.** Sudden behavioral changes in established extensions (new network calls, new permissions, obfuscated code) warrant investigation regardless of the extension's reputation.
4. **Support and adopt marketplace verification.** As marketplaces add stronger checks, participate in feedback cycles and adopt verification features as they become available.

## Recommendations

**For Individual Users:**

1. **Minimize extension usage.** Each extension increases risk. Use only extensions with clear necessity.

2. **Review permissions carefully.** Question why an extension needs the permissions it requests. A weather widget shouldn't need access to all websites.

3. **Research before installing.** Check developer reputation, review history, and recent changes. Sudden ownership changes are warning signs.

4. **Review installed extensions regularly.** Remove extensions you no longer use. Check for permission changes in updates.

5. **Use multiple browser profiles.** Isolate extensions that need broad permissions in dedicated profiles.

**For Enterprises:**

1. **Implement extension allowlisting.** Permit only reviewed and approved extensions. This is the single most effective control.

2. **Block by permission.** If full allowlisting isn't feasible, block extensions requesting dangerous permission combinations.

3. **Use enterprise browser management.** Deploy Chrome Enterprise, Firefox Enterprise, or Edge management policies.

4. **Monitor extension behavior.** Deploy tools that provide visibility into installed extensions and their network activity.

5. **Review allowed extensions periodically.** Ownership changes, update patterns, and security incidents require reassessment.

6. **Educate users.** Help employees understand extension risks and why controls exist.

**For Extension Developers:**

1. **Enable two-factor authentication.** Protect developer accounts with strong authentication.

2. **Request minimal permissions.** Only request permissions the extension genuinely needs.

3. **Avoid remote code.** Include all code in the extension package. Avoid loading scripts from remote servers.

4. **Document ownership.** If you sell or transfer an extension, be transparent with users.

5. **Publish source code.** Open-source extensions enable community review.

6. **Respond to security reports.** Establish security contact information and respond to researcher reports.

Browser extensions occupy a unique position in the supply chain landscape: they're installed by users rather than developers, they have extraordinary access to sensitive data, and their distribution channels enable rapid deployment to millions of browsers. The Great Suspender and MEGA.nz compromises demonstrate the consequences when this access is weaponized. Enterprise controls, careful extension selection, and ongoing vigilance are essential to managing browser extension supply chain risk.

[chrome-web-store]: https://chrome.google.com/webstore
[great-suspender]: https://www.bleepingcomputer.com/news/security/the-great-suspender-chrome-extensions-fall-from-grace/
[mega-incident]: https://www.bleepingcomputer.com/news/security/mega-chrome-extension-hacked-to-steal-login-credentials-and-cryptocurrency/
[spinai]: https://spin.ai/
[^great-suspender-users]: The Register, "What happens when a Chrome extension with 2m+ users changes hands" (January 7, 2021). <https://www.theregister.com/2021/01/07/great_suspender_malware/>
