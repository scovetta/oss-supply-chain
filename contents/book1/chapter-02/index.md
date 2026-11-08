# Chapter 2: The Open Source Landscape

## Summary

This chapter examines the open source ecosystem and its implications for software supply chain security. It traces the historical roots of open source from Richard Stallman's Free Software Movement in 1983 through the emergence of the "open source" term in 1998, explaining how founding principles like transparency, collaboration, and community governance continue to shape security dynamics today.

The chapter explores how open source projects are governed, from single-maintainer hobby projects to enterprise-critical infrastructure managed by well-funded foundations like Apache, Linux Foundation, and CNCF. Different governance models create distinct security profiles: concentrated governance enables rapid response but creates single points of failure, while distributed governance provides resilience but may slow emergency decisions.

A central theme is the maintainer crisis. Surveys reveal that 60% of maintainers are unpaid volunteers, many experiencing burnout, with 60% having quit or considered quitting. The chapter uses case studies including core-js (maintained by a single developer through imprisonment) and XZ Utils (where attackers exploited maintainer exhaustion through social engineering) to illustrate how this crisis directly threatens supply chain security.

The chapter surveys major package ecosystems including npm, PyPI, Maven Central, RubyGems, crates.io, Go modules, Packagist, NuGet, CocoaPods, and Swift Package Manager, comparing their governance, security features, and attack histories. It also examines operating system package managers and when to prefer them over language-specific packages.

Finally, the chapter analyzes open source economics, explaining why open source constitutes a public good subject to the free-rider problem. Despite creating trillions in value, maintenance receives minimal investment, with security work particularly underfunded because it is invisible when successful and competes with feature development for limited maintainer time.

## Sections

- 2.1 A Brief History of Open Source and Its Philosophy
- 2.2 How Open Source Projects Are Governed and Maintained
- 2.3 The Maintainer Crisis
- 2.4 Major Package Ecosystems: A Comparative Survey
- 2.5 Operating System Package Managers
- 2.6 The Economics of Open Source
