# Chapter 10: Emerging Attack Surfaces

## Summary

This chapter examines the newest and rapidly evolving attack surfaces in software supply chains, focusing on how artificial intelligence, containers, hardware, and cryptographic transitions are reshaping security risks.

AI-assisted development has fundamentally changed how code is written. AI coding assistants influence dependency choices, often suggesting packages without human verification. This creates opportunities for "slopsquatting"---attackers registering package names that AI models commonly hallucinate. Research shows approximately 20% of AI-generated code samples reference non-existent packages, with many hallucinations being highly repeatable and therefore exploitable.

Beyond assistants, autonomous AI coding agents represent a shift from AI as a tool to AI as a supply chain participant. These "digital insiders" can clone repositories, write code, and potentially deploy changes with minimal human oversight, introducing risks around goal hijacking, tool misuse, and memory poisoning. Attackers have also started using agents already installed on developer machines to hunt for secrets, and planting agent configuration files in repositories so that opening the project runs their code. The Model Context Protocol (MCP) further extends AI capabilities by connecting models to external tools and data sources, creating new dependency relationships with their own supply chain considerations.

The chapter also addresses ML model supply chains, where pre-trained models from registries like Hugging Face carry risks including serialization vulnerabilities (pickle files enabling arbitrary code execution) and model poisoning attacks. Shadow AI---unauthorized use of AI tools---compounds these risks by creating governance gaps and data leakage. A newly emerging threat, prompt worms, demonstrates how adversarial self-replicating prompts can propagate through connected AI systems---turning email assistants, RAG pipelines, and multi-agent architectures into vectors for automated attacks.

Container supply chains aggregate multiple risk layers, from base images through application dependencies. Hardware and firmware form the often-overlooked foundation of all software security, while post-quantum cryptography represents a looming transition that will eventually require replacing the cryptographic primitives underlying code signing, TLS, and certificate authorities.

## Sections

- 10.1 AI Coding Assistants and Supply Chain Risk
- 10.2 Package Hallucination and Slopsquatting
- 10.3 AI Coding Agents and Autonomous Development
- 10.4 Model Context Protocol (MCP) and Tool Integration
- 10.5 AI/ML Model Supply Chains
- 10.6 Shadow AI and Ungoverned Tool Usage
- 10.7 Container and Image Supply Chains
- 10.8 Hardware and Firmware Considerations
- 10.9 Post-Quantum Cryptography Transition
- 10.10 CI/CD Pipeline Supply Chain Attacks
- 10.11 Ecosystem-Scale Package Malware Campaigns
- 10.12 Identity and Ownership Hijacking
- 10.13 Prompt Worms: Self-Replicating AI Malware
