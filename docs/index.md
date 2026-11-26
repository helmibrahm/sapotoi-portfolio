# Sapotoi software engineering documentation

[Portfolio home](../README.md) · [Screenshot placeholders](screenshots.md) · [Standalone edition proposal](portfolio-edition-plan.md)

I built Sapotoi independently to make repetitive support work easier. This documentation applies the practices I learned in my Software Engineering degree: define the problem and scope, describe requirements, model the system from several perspectives, and trace requirements to design and verification evidence. The requirements and diagrams are retrospective; they are not presented as artifacts signed off during the original project.

## Where to start

- **Recruiters and general readers:** [Project background](requirements.md#1-project-introduction-and-background), [problem and scope](requirements.md#2-problem-statement), and [personal contributions](case-study.md#17-personal-contributions-and-ai-assisted-development).
- **Software engineers:** [Requirements traceability](requirements.md#requirements-traceability), [components](architecture.md#8-software-architecture-and-component-diagram), [data model](architecture.md#11-entity-relationship-diagram), and [integration boundaries](architecture.md#12-api-and-external-service-integration).
- **Technical interviewers:** [Activity and sequence views](architecture.md#9-activity-diagrams), [verification](case-study.md#14-testing-and-verification), and [engineering decisions](case-study.md#15-technical-challenges-and-engineering-decisions).

## Contents

| Section | Read |
| --- | --- |
| 1. Project introduction and background | [Project background](requirements.md#1-project-introduction-and-background) |
| 2. Problem statement | [Operational problem](requirements.md#2-problem-statement) |
| 3. Project objectives and scope | [Objectives and scope](requirements.md#3-project-objectives-and-scope) |
| 4. Functional requirements | [Requirements and traceability](requirements.md#4-functional-requirements) |
| 5. Non-functional requirements and quality considerations | [Quality considerations](requirements.md#5-non-functional-requirements-and-quality-considerations) |
| 6. Actors and UML use case diagram | [Actors and use cases](architecture.md#6-actors-and-uml-use-case-diagram) |
| 7. System context diagram | [Context and system boundary](architecture.md#7-system-context-diagram) |
| 8. Software architecture and component diagram | [Components and responsibilities](architecture.md#8-software-architecture-and-component-diagram) |
| 9. Activity diagrams | [Provisioning and registration activities](architecture.md#9-activity-diagrams) |
| 10. Sequence diagrams | [Provisioning and API sequences](architecture.md#10-sequence-diagrams) |
| 11. Entity relationship diagram | [Logical data model](architecture.md#11-entity-relationship-diagram) |
| 12. API and external service integration | [Interfaces and external dependencies](architecture.md#12-api-and-external-service-integration) |
| 13. Deployment architecture | [Configured release and runtime design](architecture.md#13-deployment-architecture) |
| 14. Testing and verification | [Offline checks and runtime gaps](case-study.md#14-testing-and-verification) |
| 15. Technical challenges and engineering decisions | [Decisions and trade-offs](case-study.md#15-technical-challenges-and-engineering-decisions) |
| 16. Limitations and future improvements | [Limitations and proposal](case-study.md#16-limitations-and-future-improvements) |
| 17. Personal contributions and AI-assisted development | [My role and use of development tools](case-study.md#17-personal-contributions-and-ai-assisted-development) |

## Evidence labels

| Label | Meaning |
| --- | --- |
| Historical use | My colleagues and I used the original application. This is firsthand history, not feature-by-feature verification or quantified impact. |
| Original design | Behavior supported by the reviewed source or configuration. The diagrams simplify it and omit private operational details. |
| Offline verification | The checks actually run against a local source snapshot. A passing build does not establish a working integration. |
| Proposed | An improvement or standalone portfolio design, not an implemented capability. |

The architecture chapter integrates the supplied diagram pack. Its UML use case image has an [editable PlantUML source](diagrams/use-cases.puml); the SVG is the GitHub-readable visual. Mermaid covers the other model views. Figure IDs are stable cross-references for the requirements matrix.