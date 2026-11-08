---
title: "Infrastructure as Supply Chain"
description: "Examine the hidden infrastructure dependencies—DNS, cloud platforms, CDNs—that underpin every software supply chain."
icon: "lucide/server"
---

# 3.5 Infrastructure as Supply Chain

The previous sections have focused on software dependencies—the packages, libraries, and components that applications incorporate. But modern software depends on far more than code. A hidden layer of infrastructure services underpins every software supply chain: DNS resolution that translates package registry names to IP addresses, cloud platforms that host build systems and registries, CDNs that distribute assets globally, certificate authorities that enable secure connections, and time synchronization services that coordinate distributed systems. Compromise or failure at this infrastructure layer can undermine software supply chain security regardless of how carefully individual packages are vetted.

## DNS: The Foundation Beneath the Foundation

The **Domain Name System (DNS)** translates human-readable domain names into IP addresses that computers use to communicate. Every time a developer runs `npm install` or `pip install`, DNS resolution determines which servers receive those requests. This makes DNS a critical, largely invisible supply chain dependency.

DNS compromise enables powerful attacks:

**DNS hijacking** redirects traffic intended for legitimate services to attacker-controlled servers. If an attacker can control DNS resolution for `registry.npmjs.org`, they can disrupt package installation, harvest metadata, or—if TLS or certificate validation is also undermined—serve malicious packages to developers whose DNS queries they intercept. The **DNSpionage** attacks (2018-2019), attributed to Iranian threat actors, hijacked DNS records for government and private organizations across the Middle East and North Africa, demonstrating nation-state capability and interest in DNS-based attacks.[^dnspionage]

**DNS cache poisoning** inserts malicious records into DNS resolvers, affecting all users of those resolvers. While modern DNS implementations include protections against classic poisoning attacks, vulnerabilities continue to emerge. The **SAD DNS** attack (2020) demonstrated that cache poisoning remained viable against significant portions of DNS infrastructure.[^saddns]

**Registrar compromise** provides control over authoritative DNS records. Attackers who compromise accounts at domain registrars can redirect any domain those accounts control. The **Perl.com hijacking** (January 2021) saw the perl.com domain redirected after an unauthorized registrar transfer, temporarily disrupting access to Perl resources.[^perl-hijack]

**Availability attacks** on DNS infrastructure can prevent access to package registries entirely. A DDoS attack against the DNS infrastructure serving npm or PyPI would prevent developers from installing packages, potentially breaking CI/CD pipelines and deployments globally. The **Dyn DDoS attack** (October 2016), executed using the Mirai botnet, demonstrated this risk by disrupting DNS for major internet services including GitHub, Twitter, Netflix, and Reddit, affecting developer workflows worldwide.[^dyn-ddos]

Organizations rarely consider DNS in supply chain risk assessments, yet DNS compromise could enable attacks against any package installation that relies on network resolution.

## BGP Hijacking: Routing the Internet Astray

While DNS translates names to IP addresses, the **Border Gateway Protocol (BGP)** determines how traffic actually reaches those addresses across the internet. BGP is the routing protocol that enables autonomous systems (networks operated by ISPs, cloud providers, enterprises, and other organizations) to exchange routing information and direct traffic to its destination. BGP hijacking—the malicious or accidental announcement of false routing information—can redirect internet traffic through attacker-controlled networks, enabling interception or manipulation of software supply chain communications.

!!! danger "BGP: The Internet's Unverified Routing"

    BGP was designed in an era of implicit trust between network operators. Route announcements are not cryptographically verified by default, allowing any network to claim ownership of IP address space. Attackers who control a network—or compromise one—can redirect traffic destined for package registries, build systems, or any internet service.

**How BGP Hijacking Works:**

BGP operates on announcements: networks tell their neighbors which IP address ranges (prefixes) they can reach. When a malicious network announces a more specific prefix than the legitimate owner, traffic flows to the attacker:

1. Legitimate owner announces: "I own 192.0.2.0/24"
2. Attacker announces: "I own 192.0.2.0/25" (more specific)
3. BGP routers prefer more specific routes
4. Traffic intended for 192.0.2.0-127 flows to attacker instead

The attacker can then:

- **Intercept and forward**: Capture traffic, inspect or modify it, and forward to the real destination (man-in-the-middle)
- **Blackhole**: Simply drop the traffic, creating a denial of service
- **Impersonate**: Respond to requests as if they were the legitimate service

**Supply Chain Attack Scenarios:**

BGP hijacking could target software supply chains in several ways:

**Package registry hijacking**: An attacker announcing routes for npm, PyPI, or Maven Central IP space could intercept package download requests. If TLS is absent, misconfigured, or undermined through certificate compromise, attackers could modify those requests; even when TLS holds, they could still disrupt availability or observe connection metadata. Potential effects include:

- Serve cached older (vulnerable) package versions when integrity checks are absent
- Present fraudulent certificates if they have also compromised a CA
- Harvest metadata about which organizations use which packages
- Cause availability disruptions that force developers to seek alternative sources

**Build infrastructure targeting**: CI/CD systems that fetch dependencies during builds could have their traffic redirected. Attackers could serve modified packages to build pipelines while legitimate users see normal packages—making detection difficult.

**Certificate validation bypass**: Some BGP hijacking attacks have targeted certificate authorities or OCSP responders, potentially undermining the certificate validation that protects TLS connections.

**Notable BGP Incidents:**

Several BGP incidents have demonstrated the protocol's vulnerability:

- **YouTube hijacking (2008)**: Pakistan Telecom accidentally hijacked YouTube's IP space while attempting to block the site domestically, causing a global outage. The incident demonstrated how easily BGP misconfigurations propagate.[^youtube-bgp]

- **Cryptocurrency exchange attacks (2018)**: Attackers used BGP hijacking to redirect traffic from Amazon's Route 53 DNS service, specifically targeting MyEtherWallet to steal cryptocurrency. The attack combined BGP and DNS manipulation.[^myetherwallet-bgp]

- **Russian traffic interception (2017)**: Large portions of internet traffic were routed through Russian networks for brief periods, raising concerns about traffic surveillance or manipulation capabilities.[^russia-bgp]

- **China Telecom routing anomalies**: Researchers have documented numerous instances of traffic being routed through Chinese networks unexpectedly, though intent remains debated.[^china-bgp]

**Why BGP Remains Vulnerable:**

Despite decades of awareness, BGP security improvements have been slow:

- **No authentication by default**: BGP was designed assuming trust between network operators
- **Global coordination required**: Securing BGP requires cooperation across thousands of independent networks
- **Backwards compatibility**: Security mechanisms must work alongside networks that haven't upgraded
- **Detection difficulty**: Short-lived hijacks may complete before detection and response

**Mitigation Approaches:**

Several technologies aim to improve BGP security:

**Resource Public Key Infrastructure (RPKI)** allows network operators to cryptographically sign route origin authorizations, enabling verification that announcements come from legitimate sources. Adoption has grown significantly but remains incomplete—networks that haven't deployed RPKI cannot validate routes, and attackers can target paths through non-validating networks.

**BGPsec** extends RPKI to validate the entire path of a route, not just its origin. However, BGPsec requires universal deployment to be fully effective and has seen limited adoption due to performance and complexity concerns.

**Route filtering** by ISPs can prevent obviously invalid announcements (e.g., small networks claiming large IP blocks), but requires manual configuration and doesn't address sophisticated attacks.

**Implications for Supply Chain Security:**

BGP hijacking is a sophisticated attack requiring network-level access, making it primarily a concern for nation-state adversaries or attackers who have compromised network infrastructure. However, the potential for such attacks means that:

- TLS alone is insufficient if certificate validation can be undermined
- Geographic diversity in package mirror selection provides some resilience
- Monitoring for routing anomalies should be part of critical infrastructure defense
- Organizations with high-value supply chains should understand their BGP exposure

BGP represents the deepest infrastructure layer of supply chain risk—one that most organizations cannot directly mitigate but should understand when assessing their overall threat model.

[^youtube-bgp]: RIPE NCC, "YouTube Hijacking: A RIPE NCC RIS case study" (2008). https://www.ripe.net/publications/news/industry-developments/youtube-hijacking-a-ripe-ncc-ris-case-study
[^myetherwallet-bgp]: Cloudflare, "BGP leaks and cryptocurrencies" (April 2018). https://blog.cloudflare.com/bgp-leaks-and-crypto-currencies/
[^russia-bgp]: Oracle, "Russian BGP Hijacks" (November 2017). https://blogs.oracle.com/internetintelligence/post/russian-bgp-hijacks
[^china-bgp]: Naval War College & Tel Aviv University, "China's Maxim - Leave No Access Point Unexploited: The Hidden Story of China Telecom's BGP Hijacking" (2018). https://scholarcommons.usf.edu/mca/vol3/iss1/7/

## Cloud Provider Dependencies

Modern software supply chains are deeply entangled with cloud providers. Package registries run on cloud infrastructure. CI/CD systems operate as cloud services. Container images are stored in cloud registries. This concentration creates **shared fate**—the security and availability of your supply chain depends on your cloud provider's security and availability.

**Infrastructure compromise** at cloud providers would have cascading effects throughout the software ecosystem. If an attacker compromised AWS infrastructure hosting npm's registry backend, they could potentially modify packages served to millions of developers. While major cloud providers invest heavily in security, their central position makes them attractive targets for sophisticated adversaries.

**Cloud service vulnerabilities** periodically affect supply chains. The **Codecov breach** (2021) exploited a vulnerability in Codecov's Docker image creation process that exposed credentials, enabling attackers to modify the Bash Uploader script and harvest secrets from customer CI environments for more than two months before detection.[^codecov] Cloud-based CI/CD services—GitHub Actions, GitLab CI, CircleCI, Travis CI—execute customer code with access to credentials and secrets, making them high-value targets.

**Multi-tenancy risks** arise because cloud services serve many customers from shared infrastructure. Security boundaries between tenants are logical rather than physical, and vulnerabilities that break these boundaries enable cross-tenant attacks. The **ChaosDB vulnerability** (2021), discovered by Wiz researchers, allowed unauthorized access to other customers' Azure Cosmos DB instances through a flaw in the Jupyter Notebook feature, illustrating how cloud multi-tenancy risks can affect data and potentially supply chain assets.[^chaosdb]

**Availability dependencies** mean that cloud provider outages disrupt supply chains. When AWS us-east-1 experiences degradation, npm availability may be affected. When GitHub experiences downtime, millions of CI/CD pipelines fail. Organizations building on cloud infrastructure inherit both the provider's security investments and their single points of failure.

The concentration of supply chain infrastructure in a small number of cloud providers creates systemic risk. If AWS, Azure, and GCP account for the majority of supply chain hosting, the software ecosystem's resilience depends on those three organizations' security postures.

## Content Delivery Networks

**Content Delivery Networks (CDNs)** distribute static assets—JavaScript files, fonts, images—from servers geographically close to users. Many websites load JavaScript libraries directly from CDNs rather than bundling them locally. This creates a supply chain dependency where the CDN becomes a trust point: compromise of the CDN enables modifying assets served to website visitors.

The **Polyfill.io incident** (June 2024) demonstrated CDN supply chain risk vividly and deserves detailed examination as a case study in third-party JavaScript risks.

!!! danger "Case Study: Polyfill.io (2024)"

    After a Chinese company acquired the polyfill.io domain, they modified JavaScript to redirect mobile users to betting/adult sites. Censys found over **380,000 hosts** embedding the script, including 182 `.gov` hosts and properties from Warner Bros., Hulu, Mercedes-Benz, and the World Economic Forum. Site operators had delegated trust by including `<script src="https://cdn.polyfill.io/...">`—with no control over what code that URL would serve in the future.

**Background**: Polyfill.io was a popular CDN service providing JavaScript polyfills—code that implements modern JavaScript features in older browsers. The service was created in 2014 by the Financial Times as an open source project to help developers support older browsers without bundling unnecessary code for modern browsers. The service dynamically detected browser capabilities and served only the polyfills needed, making it an elegant solution adopted by hundreds of thousands of websites.

**The Acquisition**: In February 2024, a Chinese company named Funnull acquired the polyfill.io domain and the associated GitHub organization from the original maintainers. Shortly after acquisition, the original project creator, Andrew Betts, warned website operators via social media that he no longer controlled the service and recommended removing any references to polyfill.io from their sites. He noted that modern browsers no longer require most polyfills, making the service largely unnecessary for contemporary development.

**The Attack**: Beginning in June 2024, the new operators modified the JavaScript served by cdn.polyfill.io to include malicious code. The malicious payload:

- Redirected mobile users to sports betting and adult content sites
- Targeted specific referring domains to avoid detection during analysis
- Used obfuscation techniques to evade automated security scanning
- Included anti-debugging measures to frustrate investigation
- Executed only under certain conditions (mobile browsers, specific referrers) to reduce detection probability

**Scale and Impact**: Security researchers at Sansec initially identified the attack affecting over 100,000 websites.[^sansec-polyfill] Subsequent analysis by Censys found over 380,000 hosts embedding the malicious polyfill script, including 182 government websites.[^censys-polyfill] High-profile properties operated by major corporations were found to be embedding the script, including Warner Bros., Hulu, Mercedes-Benz, JSTOR, Intuit, and the World Economic Forum.

**Response**: Cloudflare and Fastly responded by creating mirrors of the legitimate polyfill.io library code; Cloudflare also automatically rewrote polyfill.io links for sites proxied through its network. Google began warning advertisers that their ads would be blocked if served on pages including polyfill.io scripts. Domain registrars eventually suspended the polyfill.io domain, though the attackers registered alternative domains in attempts to continue operations.

**Why It Succeeded**: The attack was effective because website operators had delegated trust to a third party by including `<script src="https://cdn.polyfill.io/...">` in their pages. They had no control over what code that URL would serve in the future. When the service changed hands, so did control over code executing on their visitors' browsers. Many site operators were unaware they included this dependency—it had been added years earlier, perhaps by developers who had since left, or included transitively through other libraries and frameworks.

**Lessons Learned**:

1. **Third-party JavaScript is a supply chain risk**: Every external script is a dependency you don't control. The polyfill.io incident was not a technical vulnerability: it was a trust chain failure.

2. **Domain and project ownership can change**: Unlike package registries where maintainer changes may be visible, domain ownership changes are opaque to downstream consumers. The previous owner's reputation provides no guarantee about future operators.

3. **Subresource Integrity (SRI) provides partial protection**: Sites that had implemented SRI—a browser feature that verifies loaded resources against expected cryptographic hashes—would have blocked the modified scripts, as the hash would no longer match. However, SRI adoption remains limited, and it doesn't work for dynamically generated scripts like polyfill.io's browser-specific responses.

4. **Self-hosting eliminates third-party risks**: Bundling dependencies locally rather than loading from CDNs ensures you control what code executes. The performance benefits of CDN loading rarely outweigh the security risks.

5. **Audit third-party dependencies regularly**: Many affected sites were loading polyfill.io scripts added years ago that were no longer needed. Regular review of external dependencies—including third-party JavaScript—should be part of security hygiene.

The Polyfill.io incident represents a new category of supply chain attack—not compromising a package registry, but acquiring a widely-trusted distribution service and weaponizing it. As more services and domains change hands, similar attacks become increasingly likely.

Similar risks exist whenever websites load resources from external domains:

- JavaScript CDNs like cdnjs, jsDelivr, and unpkg serve libraries to millions of websites
- Font services like Google Fonts serve typography resources with potential for tracking or malicious modification
- Analytics scripts execute on pages with access to page content and user interactions

As noted above, SRI provides partial mitigation by specifying expected cryptographic hashes for externally loaded resources. Browsers verify loaded content against these hashes and refuse to execute content that doesn't match. However, SRI adoption remains limited, and it requires knowing the expected hash in advance, which is problematic for services that update JavaScript content regularly and doesn't cover dynamically-loaded dependencies, images, or other types of content.

## Certificate Authorities and Trust

**Certificate Authorities (CAs)** issue the TLS certificates that enable secure connections between clients and servers. When you install packages over HTTPS, your trust that the connection is secure ultimately derives from trust in CAs. CA compromise enables **man-in-the-middle attacks** against any connection the attacker can intercept.

The **DigiNotar compromise** (2011) demonstrated CA risks dramatically. Attackers compromised the Dutch certificate authority and issued over 500 fraudulent certificates for high-profile domains including google.com. Investigators identified over 300,000 Iranian Gmail users as the primary targets of subsequent man-in-the-middle attacks. DigiNotar's root certificates were removed from all major browsers, and the company declared bankruptcy within weeks—demonstrating how CA compromise can undermine internet security broadly.[^diginotar]

More recently, **certificate misissuance**—certificates issued incorrectly, whether through error or malice—has affected supply chain-relevant domains. While certificate transparency logs now provide visibility into certificate issuance, detection requires active monitoring.

For software supply chains, CA trust has specific implications:

- Package managers verify registry connections using TLS, depending on CA-issued certificates
- Code signing certificates from CAs authenticate software publishers
- Compromised CAs could enable man-in-the-middle attacks during package installation
- Revocation mechanisms (CRL, OCSP) may not propagate quickly enough to prevent exploitation

## Time Synchronization

**Network Time Protocol (NTP)** synchronization may seem distant from supply chain security, but accurate time underpins many security mechanisms:

- **Certificate validation** checks that certificates are within their validity period. Systems with incorrect time may accept expired or not-yet-valid certificates.
- **Signature verification** for some schemes depends on timestamp validation. Incorrect time can cause valid signatures to be rejected or invalid signatures to be accepted.
- **Log correlation** during incident investigation requires accurate timestamps. Systems with time skew produce logs that are difficult to correlate.
- **Rate limiting and timeout mechanisms** behave incorrectly when system time is wrong.

NTP attacks can manipulate time on target systems. Researchers have demonstrated attacks that shift victim system clocks hours or days from actual time, potentially affecting certificate validation and other security mechanisms. **Network Time Security (NTS)**, standardized in RFC 8915, addresses these weaknesses by using TLS for initial authentication and authenticated encryption for subsequent time synchronization packets—providing cryptographic assurance that time data hasn't been tampered with.[^nts-rfc] However, NTS adoption remains limited, leaving most systems vulnerable to time-based attacks.

## The Hidden Infrastructure

Modern software depends on a complex web of infrastructure services that developers rarely consider:

**Public key infrastructure** beyond CAs includes systems like keyservers that distribute GPG keys for package signing, update-framework implementations that manage key rotation, and certificate transparency logs that provide auditability.

**Package registry infrastructure** includes not just the registry servers themselves but the mirrors, caches, and CDNs that improve availability and performance. Many organizations use caching proxies (Verdaccio, Nexus, Artifactory) that introduce additional trust points.

**Build infrastructure** beyond CI/CD includes the base images used for container builds, the compilers and toolchains that transform source to binaries, and the orchestration systems that coordinate distributed builds.

Each infrastructure component represents a supply chain dependency. Comprehensive supply chain security requires considering these infrastructure dependencies alongside the more visible software dependencies.

## Implications for Security Strategy

Infrastructure dependencies create supply chain risks that traditional software composition analysis does not address. Effective security strategy must consider:

**Identify infrastructure dependencies**: Map the infrastructure services your supply chain depends on—DNS providers, cloud platforms, CDNs, certificate authorities. Understand that compromise of these services affects your security regardless of your own security practices.

**Reduce unnecessary dependencies**: Self-host where practical. Bundle assets rather than loading from CDNs. Use private DNS where appropriate. Each external dependency is a trust point outside your control.

**Implement integrity verification**: Use Subresource Integrity for externally loaded assets. Verify package signatures rather than relying solely on TLS. Defense in depth reduces reliance on any single infrastructure component.

**Plan for infrastructure failure**: Assume infrastructure dependencies will occasionally fail or be compromised. Caching, fallback mechanisms, and incident response plans provide resilience.

Chapter 7 examines attacks targeting distribution infrastructure in detail, and Book 2 discusses defensive measures for securing delivery and deployment. The infrastructure layer explored here provides context for those discussions—a reminder that software supply chain security extends far beyond the packages that appear in dependency manifests.

[^dnspionage]: CISA, "DNS Infrastructure Hijacking Campaign" (January 2019). https://www.cisa.gov/news-events/cybersecurity-advisories/aa19-024a
[^saddns]: Man, K., et al., "DNS Cache Poisoning Attack Reloaded: Revolutions with Side Channels," ACM CCS 2020. https://www.saddns.net/
[^perl-hijack]: Perl.com, "The Hijacking of Perl.com" (March 2021). https://www.perl.com/article/the-hijacking-of-perl-com/
[^dyn-ddos]: Cloudflare, "What is the Mirai Botnet?" https://www.cloudflare.com/learning/ddos/glossary/mirai-botnet/
[^codecov]: Codecov, "Bash Uploader Security Update" (April 2021). https://about.codecov.io/security-update/
[^chaosdb]: Wiz, "ChaosDB: How we hacked thousands of Azure customers' databases" (August 2021). https://www.wiz.io/blog/chaosdb-how-we-hacked-thousands-of-azure-customers-databases
[^sansec-polyfill]: Sansec, "Polyfill supply chain attack hits 100K+ sites" (June 2024). https://sansec.io/research/polyfill-supply-chain-attack
[^censys-polyfill]: Censys, "Polyfill.io Supply Chain Attack - Digging into the Web of Compromised Domains" (July 2024). https://www.censys.com/blog/july-2-polyfill-io-supply-chain-attack-digging-into-the-web-of-compromised-domains
[^diginotar]: Fox-IT, "Black Tulip: Report of the investigation into the DigiNotar Certificate Authority breach" (August 2012). https://www.researchgate.net/publication/269333601_Black_Tulip_Report_of_the_investigation_into_the_DigiNotar_Certificate_Authority_breach
[^nts-rfc]: IETF, RFC 8915: "Network Time Security for the Network Time Protocol" (September 2020). https://datatracker.ietf.org/doc/html/rfc8915
