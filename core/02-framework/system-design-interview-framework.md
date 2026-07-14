# A Framework For System Design Interviews (IMP)

## 1. Gather and Define Requirements

Ask Clarifying Questions
- Understand a similar system.
- Identify the key features to build.
- Understand the expected scale.

Define Functional Requirements
- List the main user flows.
- Map the required features to each flow.

Define Non-Functional Requirements
- List the non-functional requirements.
- Define the expected SLAs.

Do Back-of-the-Envelope Calculations
- Estimate storage requirements.
- Estimate read and write traffic.

---

## 2. Propose a High-Level Design

1. Design a system that satisfies all functional requirements. Ignore scale and non-functional requirements at this stage.
2. Walk through each user flow and verify that all functional requirements are covered.
3. Identify the databases to use.
4. Define the APIs and events.
5. Define the database schema.
6. Refine the design to handle scale.

---

## 3. Deep Dive

Pick the important non-functional requirements and discuss them in detail.

Availability
- Higher availability targets (for example, p999 to p9999).
- Multi-region and multi-AZ deployment.
- Technologies such as EKS and Aurora.

Reliability
- Handling software failures.
- Handling hardware failures.
- Handling human errors.

Scalability
- Horizontal scaling.

Observability
- Monitoring.
- Alerting.

Security
- DDos : Rate limiter
- Evesdropping and Man in middle attack : HTTPS and mTLS
- e2e encrytion