## Appendix C: SBOM and AI-BOM Format Guide

This appendix provides practical guidance for implementing Software Bills of Materials (SBOMs) and AI Bills of Materials (AI-BOMs). We cover the two dominant SBOM formats, SPDX and CycloneDX, along with their extensions for machine learning components, and offer guidance on selecting the appropriate format for your use case.

---

### Understanding SBOM Formats

A **Software Bill of Materials (SBOM)** is a machine-readable inventory of software components and their relationships. SBOMs enable organizations to track dependencies, respond to vulnerability disclosures, and meet regulatory compliance requirements. Two formats have emerged as industry standards: SPDX (maintained by the Linux Foundation) and CycloneDX (maintained by OWASP).

Both formats can express the same fundamental information but differ in their origins, structure, and ecosystem support. SPDX originated from license compliance use cases and has been standardized as ISO/IEC 5962:2021. CycloneDX was designed specifically for security use cases and offers a more streamlined structure for vulnerability management workflows.

---

### SPDX Format

#### Overview

**SPDX (Software Package Data Exchange)** is the oldest and most mature SBOM standard, first released in 2010. SPDX provides comprehensive support for license information, making it particularly strong for compliance use cases. The format supports multiple serializations including JSON, XML, RDF, YAML, and a human-readable tag-value format.

SPDX 2.3 remains widely supported by tools, while SPDX 3.0 introduces significant enhancements including profiles for AI/ML, security, and build information.

#### Key Concepts

- **Document**: The top-level container holding metadata about the SBOM itself
- **Package**: A component in the software (library, application, container, etc.)
- **File**: Individual files within packages
- **Snippet**: Portions of files (useful for license identification)
- **Relationship**: How components relate to each other (DEPENDS_ON, CONTAINS, etc.)

#### Sample SPDX SBOM (JSON)

```json
{
  "spdxVersion": "SPDX-2.3",
  "dataLicense": "CC0-1.0",
  "SPDXID": "SPDXRef-DOCUMENT",
  "name": "example-webapp-sbom",
  "documentNamespace": "https://example.com/sbom/webapp-1.0.0",
  "creationInfo": {
    "created": "2024-11-15T10:30:00Z",
    "creators": [
      "Tool: syft-0.98.0",
      "Organization: Example Corp"
    ],
    "licenseListVersion": "3.22"
  },
  "packages": [
    {
      "SPDXID": "SPDXRef-Package-webapp",
      "name": "example-webapp",
      "versionInfo": "1.0.0",
      "supplier": "Organization: Example Corp",
      "downloadLocation": "https://github.com/example/webapp",
      "filesAnalyzed": false,
      "licenseConcluded": "Apache-2.0",
      "licenseDeclared": "Apache-2.0",
      "copyrightText": "Copyright 2024 Example Corp",
      "externalRefs": [
        {
          "referenceCategory": "PACKAGE-MANAGER",
          "referenceType": "purl",
          "referenceLocator": "pkg:npm/example-webapp@1.0.0"
        }
      ]
    },
    {
      "SPDXID": "SPDXRef-Package-lodash",
      "name": "lodash",
      "versionInfo": "4.17.21",
      "supplier": "Organization: Lodash",
      "downloadLocation": "https://registry.npmjs.org/lodash/-/lodash-4.17.21.tgz",
      "filesAnalyzed": false,
      "licenseConcluded": "MIT",
      "licenseDeclared": "MIT",
      "copyrightText": "Copyright OpenJS Foundation",
      "checksums": [
        {
          "algorithm": "SHA256",
          "checksumValue": "a0e27c..."
        }
      ],
      "externalRefs": [
        {
          "referenceCategory": "PACKAGE-MANAGER",
          "referenceType": "purl",
          "referenceLocator": "pkg:npm/lodash@4.17.21"
        },
        {
          "referenceCategory": "SECURITY",
          "referenceType": "cpe23Type",
          "referenceLocator": "cpe:2.3:a:lodash:lodash:4.17.21:*:*:*:*:*:*:*"
        }
      ]
    }
  ],
  "relationships": [
    {
      "spdxElementId": "SPDXRef-DOCUMENT",
      "relationshipType": "DESCRIBES",
      "relatedSpdxElement": "SPDXRef-Package-webapp"
    },
    {
      "spdxElementId": "SPDXRef-Package-webapp",
      "relationshipType": "DEPENDS_ON",
      "relatedSpdxElement": "SPDXRef-Package-lodash"
    }
  ]
}
```

#### SPDX Strengths

- ISO standardization (ISO/IEC 5962:2021)
- Comprehensive license expression support
- Rich relationship types for complex dependency graphs
- File-level and snippet-level granularity
- Strong government and enterprise adoption

---

### CycloneDX Format

#### Overview

**CycloneDX** is a lightweight SBOM standard designed by OWASP specifically for security and supply chain use cases. First released in 2017, CycloneDX prioritizes ease of generation and consumption while providing robust support for vulnerability information, services, and machine learning components.

CycloneDX supports JSON and XML serializations and is currently at version 1.6, which includes enhanced support for attestations, cryptographic assets, and AI/ML components.

#### Key Concepts

- **BOM**: The top-level document containing metadata and component inventory
- **Component**: Any software component (library, application, framework, etc.)
- **Service**: External services the software depends on
- **Dependency**: Explicit dependency relationships between components
- **Vulnerability**: Known vulnerabilities affecting components (VEX integration)

#### Sample CycloneDX SBOM (JSON)

```json
{
  "$schema": "http://cyclonedx.org/schema/bom-1.6.schema.json",
  "bomFormat": "CycloneDX",
  "specVersion": "1.6",
  "serialNumber": "urn:uuid:3e671687-395b-41f5-a30f-a58921a69b79",
  "version": 1,
  "metadata": {
    "timestamp": "2024-11-15T10:30:00Z",
    "tools": [
      {
        "vendor": "Anchore",
        "name": "syft",
        "version": "0.98.0"
      }
    ],
    "component": {
      "type": "application",
      "name": "example-webapp",
      "version": "1.0.0",
      "purl": "pkg:npm/example-webapp@1.0.0"
    },
    "manufacturer": {
      "name": "Example Corp",
      "url": ["https://example.com"]
    }
  },
  "components": [
    {
      "type": "library",
      "name": "lodash",
      "version": "4.17.21",
      "purl": "pkg:npm/lodash@4.17.21",
      "cpe": "cpe:2.3:a:lodash:lodash:4.17.21:*:*:*:*:*:*:*",
      "licenses": [
        {
          "license": {
            "id": "MIT"
          }
        }
      ],
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "a0e27c..."
        }
      ],
      "externalReferences": [
        {
          "type": "website",
          "url": "https://lodash.com"
        },
        {
          "type": "vcs",
          "url": "https://github.com/lodash/lodash"
        }
      ]
    },
    {
      "type": "library",
      "name": "express",
      "version": "4.18.2",
      "purl": "pkg:npm/express@4.18.2",
      "licenses": [
        {
          "license": {
            "id": "MIT"
          }
        }
      ]
    }
  ],
  "dependencies": [
    {
      "ref": "pkg:npm/example-webapp@1.0.0",
      "dependsOn": [
        "pkg:npm/lodash@4.17.21",
        "pkg:npm/express@4.18.2"
      ]
    },
    {
      "ref": "pkg:npm/express@4.18.2",
      "dependsOn": []
    }
  ]
}
```

#### CycloneDX Strengths

- Designed for security workflows from the start
- Native VEX (Vulnerability Exploitability eXchange) support
- Simpler structure, easier to generate and parse
- Strong support for services and SaaSBOM
- Active development with regular releases
- Extensive machine learning component support

---

### AI-BOM Formats

As organizations deploy AI and machine learning systems, documenting model provenance, training data, and dependencies becomes critical. Both SPDX and CycloneDX have extended their specifications to address AI/ML transparency requirements.

CoSAI's AI supply chain guidance provides a useful completeness test for AI-BOMs. It frames the AI supply chain as data, model, application, and infrastructure, and recommends documentation that combines machine-readable provenance with human-readable review artifacts such as model cards.[^cosai-ai-supply-chain] An AI-BOM should therefore describe more than the final model file; it should connect the model to the data, training process, evaluation evidence, and serving context that shape its behavior.

| Documentation Area | Include in the AI-BOM |
|--------------------|-----------------------|
| **Model provenance and integrity** | Model name, version, source, registry location, base model or checkpoint lineage, hashes or signatures for model artifacts, and verification status before deployment or fine-tuning. |
| **Dataset documentation** | Training, validation, fine-tuning, and evaluation datasets; source identifiers such as URLs, repositories, or providers; dataset versions; checksums or content hashes; collection methods; preprocessing and transformation steps; and usage characteristics such as token counts or distribution metrics. |
| **Training and fine-tuning configuration** | Model architecture, ML framework and version, hyperparameters, mixture weights or sampling strategies, training code and container images, pipeline dependencies, and relevant compute environment details. |
| **Model card content** | Intended use, limitations, performance metrics, operational constraints, bias or safety considerations, evaluation methodology, and links to full model cards when the AI-BOM only carries a summary. |
| **Application and serving context** | RAG components, embedding models, retrieval algorithms, vector database providers and isolation controls, filters or guardrails, tools and plugins available during inference, serving infrastructure, authentication and authorization boundaries, and dependency trees for these components. |

#### SPDX 3.0 AI Profile

SPDX 3.0 introduces an **AI and Dataset Profile** that extends the core specification to capture machine learning-specific metadata. This profile enables organizations to document:

- Model architecture and parameters
- Training and evaluation datasets
- Hyperparameters and training configuration
- Performance metrics and limitations
- Intended use and ethical considerations

```json
{
  "spdxVersion": "SPDX-3.0",
  "creationInfo": {
    "created": "2024-11-15T10:30:00Z",
    "createdBy": ["https://example.com/agents/ml-pipeline"]
  },
  "elements": [
    {
      "type": "ai_Package",
      "spdxId": "urn:spdx:example:sentiment-model-v2",
      "name": "sentiment-classifier",
      "ai_modelType": "transformer",
      "ai_autonomyType": "assistive",
      "ai_domain": ["nlp", "sentiment-analysis"],
      "ai_energyConsumption": {
        "trainingEnergyConsumption": "450 kWh",
        "inferenceEnergyConsumption": "0.002 kWh per request"
      },
      "ai_hyperparameters": {
        "learningRate": 0.0001,
        "batchSize": 32,
        "epochs": 10
      },
      "ai_limitations": [
        "May exhibit bias on underrepresented languages",
        "Not suitable for regulated financial decisions"
      ],
      "ai_metric": [
        {
          "metricType": "accuracy",
          "metricValue": "0.94",
          "metricDataset": "internal-test-set-v3"
        },
        {
          "metricType": "f1Score",
          "metricValue": "0.91"
        }
      ],
      "ai_safetyRisk": "low"
    },
    {
      "type": "Dataset",
      "spdxId": "urn:spdx:example:training-data-v3",
      "name": "sentiment-training-corpus",
      "dataset_size": "2.3GB",
      "dataset_intendedUse": "Training sentiment classification models",
      "dataset_dataCollectionProcess": "Web scraping with human annotation",
      "dataset_knownBias": ["English language bias", "Social media tone"]
    }
  ],
  "relationships": [
    {
      "type": "Relationship",
      "from": "urn:spdx:example:sentiment-model-v2",
      "relationshipType": "trainedOn",
      "to": "urn:spdx:example:training-data-v3"
    }
  ]
}
```

#### CycloneDX ML-BOM

CycloneDX 1.6+ includes native support for **Machine Learning Bill of Materials (ML-BOM)** through its `modelCard` extension. This approach aligns with Google's Model Cards framework and captures:

- Model details and versioning
- Training data characteristics
- Performance metrics across different populations
- Ethical considerations and intended use

```json
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.6",
  "serialNumber": "urn:uuid:ml-bom-example",
  "version": 1,
  "metadata": {
    "timestamp": "2024-11-15T10:30:00Z",
    "component": {
      "type": "machine-learning-model",
      "name": "fraud-detection-model",
      "version": "2.1.0"
    }
  },
  "components": [
    {
      "type": "machine-learning-model",
      "name": "fraud-detection-model",
      "version": "2.1.0",
      "bom-ref": "fraud-model-v2.1",
      "modelCard": {
        "modelParameters": {
          "approach": {
            "type": "supervised"
          },
          "task": "binary-classification",
          "architectureFamily": "gradient-boosting",
          "modelArchitecture": "XGBoost",
          "datasets": [
            {
              "type": "dataset",
              "name": "transaction-history-2023",
              "contents": {
                "type": "tabular",
                "properties": [
                  {"name": "records", "value": "50000000"},
                  {"name": "features", "value": "127"}
                ]
              },
              "governance": {
                "owners": [{"organization": {"name": "Data Team"}}]
              }
            }
          ],
          "inputs": [
            {
              "format": "JSON"
            }
          ],
          "outputs": [
            {
              "format": "JSON"
            }
          ]
        },
        "quantitativeAnalysis": {
          "performanceMetrics": [
            {
              "type": "precision",
              "value": "0.96",
              "confidenceInterval": {"lowerBound": "0.94", "upperBound": "0.98"}
            },
            {
              "type": "recall",
              "value": "0.89"
            },
            {
              "type": "auc-roc",
              "value": "0.97"
            }
          ]
        },
        "considerations": {
          "users": ["Fraud analysis team", "Automated transaction systems"],
          "useCases": ["Real-time fraud detection for transactions > $100"],
          "technicalLimitations": [
            "Latency increases significantly for batch sizes > 1000",
            "Requires feature engineering pipeline version 3.x"
          ],
          "ethicalConsiderations": [
            {
              "name": "Demographic bias",
              "mitigationStrategy": "Regular fairness audits across protected classes"
            }
          ]
        }
      },
      "licenses": [
        {"license": {"name": "Proprietary"}}
      ]
    }
  ],
  "dependencies": [
    {
      "ref": "fraud-model-v2.1",
      "dependsOn": [
        "pkg:pypi/xgboost@1.7.6",
        "pkg:pypi/scikit-learn@1.3.0",
        "pkg:pypi/pandas@2.0.3"
      ]
    }
  ]
}
```

---

### Format Comparison

The following table summarizes key differences between SPDX and CycloneDX to help you select the appropriate format:

| Aspect | SPDX 2.3 | SPDX 3.0 | CycloneDX 1.6+ |
|--------|----------|----------|----------------|
| **Primary Use Case** | License compliance | Multi-purpose (modular profiles) | Security & supply chain |
| **ISO Standardization** | Yes (ISO/IEC 5962:2021) | In progress | No (ECMA pending) |
| **Serialization Formats** | JSON, XML, RDF, YAML, Tag-Value | JSON-LD, JSON, XML | JSON, XML |
| **File-Level Detail** | Full support | Full support | Limited |
| **License Expression** | Comprehensive (SPDX license list) | Comprehensive | Good |
| **Vulnerability Integration** | External refs only | Security profile | Native VEX support |
| **Service Dependencies** | Limited | Services profile | Native (SaaSBOM) |
| **AI/ML Support** | No | AI/Dataset profile | Native ML-BOM |
| **Learning Curve** | Moderate | Higher (profiles) | Lower |
| **Tool Ecosystem** | Mature | Emerging | Mature |

#### When to Use SPDX

- License compliance is a primary concern
- You need file-level or snippet-level granularity
- Government contracts require ISO-standardized formats
- Interoperability with legal/compliance tooling is needed
- You're working with the Linux Foundation ecosystem

#### When to Use CycloneDX

- Security and vulnerability management are primary concerns
- You need native VEX support for vulnerability status
- You're documenting services and APIs (SaaSBOM)
- You want simpler tooling integration
- You need ML-BOM capabilities without SPDX 3.0 migration

---

### Tooling Reference

#### SBOM Generation Tools

| Tool | Languages/Ecosystems | Output Formats | Notes |
|------|---------------------|----------------|-------|
| **Syft** | Multi-ecosystem (20+) | SPDX, CycloneDX | Fast, accurate, widely adopted |
| **Trivy** | Containers, filesystems | SPDX, CycloneDX | Integrated vulnerability scanning |
| **cdxgen** | Multi-ecosystem | CycloneDX | CycloneDX-native, ML-BOM support |
| **SPDX SBOM Generator** | Multi-ecosystem | SPDX | Official SPDX tool |
| **Microsoft SBOM Tool** | .NET, npm, pip, Maven | SPDX | Enterprise-focused |
| **Tern** | Containers | SPDX | Container layer analysis |
| **Kubernetes BOM** | Kubernetes | SPDX | K8s-specific metadata |
| **CycloneDX plugins** | Maven, Gradle, npm, etc. | CycloneDX | Build-system integrated |

#### SBOM Consumption and Analysis Tools

| Tool | Purpose | Formats Supported |
|------|---------|-------------------|
| **Grype** | Vulnerability scanning | SPDX, CycloneDX |
| **SBOM Scorecard** | Quality assessment | SPDX, CycloneDX |
| **DependencyTrack** | Continuous monitoring | CycloneDX (primary), SPDX |
| **Bomber** | Vulnerability scanning | SPDX, CycloneDX |
| **SPDX Online Tools** | Validation, conversion | SPDX |
| **CycloneDX CLI** | Validation, merging, diffing | CycloneDX |
| **GUAC** | Graph analysis | SPDX, CycloneDX |

---

### Common Pitfalls

**1. Incomplete Package URLs (purls)**
Package URLs are essential for correlating components with vulnerability databases. Ensure every component includes a properly formatted purl.

```
❌ "name": "lodash", "version": "4.17.21"
✅ "purl": "pkg:npm/lodash@4.17.21"
```

**2. Missing Checksums**
SBOMs without cryptographic hashes cannot verify component integrity. Include SHA-256 hashes at minimum.

**3. Incorrect Relationship Types**
Confusing DEPENDS_ON with CONTAINS is common. DEPENDS_ON indicates a runtime or build dependency; CONTAINS indicates a component is physically included within another.

**4. Stale SBOMs**
SBOMs generated once and never updated become liability rather than asset. Integrate SBOM generation into CI/CD pipelines.

**5. Omitting Transitive Dependencies**
SBOMs must include the complete dependency tree. An SBOM showing only direct dependencies provides false confidence.

**6. License Expression Errors**
Use SPDX license identifiers exactly as specified. `Apache 2.0` is incorrect; `Apache-2.0` is correct.

**7. Missing Supplier Information**
Vulnerability response requires knowing who maintains components. Always populate supplier/manufacturer fields.

**8. Format Mixing in Pipelines**
Converting between formats can lose information. Standardize on one format throughout your toolchain where possible.

---

### Best Practices Checklist

Use this checklist when implementing SBOM processes:

#### Generation

- [ ] Integrate SBOM generation into CI/CD pipeline
- [ ] Generate SBOMs at build time, not retrospectively
- [ ] Include complete transitive dependency trees
- [ ] Populate package URLs (purls) for all components
- [ ] Include cryptographic hashes (SHA-256 minimum)
- [ ] Capture supplier/manufacturer information
- [ ] Use accurate SPDX license identifiers
- [ ] Version and timestamp all SBOMs
- [ ] Include the primary component being described
- [ ] Document the tool and version used for generation

#### Quality

- [ ] Validate SBOMs against schema before distribution
- [ ] Use SBOM Scorecard or similar to assess completeness
- [ ] Verify purls resolve to correct packages
- [ ] Confirm transitive dependencies are accurate
- [ ] Test that vulnerability scanners can process your SBOMs
- [ ] Review for sensitive information before sharing

#### Distribution

- [ ] Establish consistent naming conventions
- [ ] Store SBOMs alongside artifacts they describe
- [ ] Sign SBOMs using Sigstore or similar
- [ ] Define retention policies aligned with software lifecycle
- [ ] Provide SBOMs in formats your consumers need
- [ ] Document how to obtain SBOMs for your products

#### AI-BOM Specific

- [ ] Document model architecture and parameters
- [ ] Include training, validation, fine-tuning, and evaluation data lineage and characteristics
- [ ] Record dataset source identifiers, versions, hashes, collection methods, preprocessing steps, and transformation logs
- [ ] Capture base model, checkpoint, and model-weight provenance with hashes or signatures
- [ ] Document training configuration, including hyperparameters, mixture weights, sampling strategies, and framework versions
- [ ] Capture performance metrics with confidence intervals
- [ ] Document known limitations and biases
- [ ] Specify intended use cases and constraints
- [ ] Include ethical considerations and mitigation strategies
- [ ] Link to full model cards where applicable
- [ ] Capture software dependencies of ML pipelines
- [ ] Document serving context, including RAG components, embedding models, vector databases, filters, tools, plugins, and trust boundaries

---

### Additional Resources

- **SPDX Specification**[^spdx-spec]
- **CycloneDX Specification**[^cyclonedx-spec]
- **CycloneDX Guides**[^cyclonedx-guides]
- **NTIA SBOM Minimum Elements**[^ntia-sbom-appc]
- **CISA SBOM Resources**[^cisa-sbom]
- **SPDX License List**[^spdx-licenses]
- **Package URL Specification**[^purl-spec]
- **CoSAI AI Supply Chain Risks and Controls**[^cosai-ai-supply-chain]

[^spdx-spec]: Linux Foundation, "SPDX Specification," https://spdx.github.io/spdx-spec/

[^cyclonedx-spec]: OWASP, "CycloneDX Specification Overview," https://cyclonedx.org/specification/overview/

[^cyclonedx-guides]: OWASP, "CycloneDX Guides," https://cyclonedx.org/guides/

[^ntia-sbom-appc]: NTIA, "Software Bill of Materials," https://www.ntia.gov/sbom

[^cisa-sbom]: CISA, "Software Bill of Materials," https://www.cisa.gov/sbom

[^spdx-licenses]: Linux Foundation, "SPDX License List," https://spdx.org/licenses/

[^purl-spec]: GitHub, "Package URL Specification," https://github.com/package-url/purl-spec

[^cosai-ai-supply-chain]: CoSAI, "Establish Risks and Controls for the AI Supply Chain," V1.0, Workstream 1, 2025, https://www.coalitionforsecureai.org/wp-content/uploads/2026/03/risks-and-controls-for-the-ai-supply-chain-v1.pdf
