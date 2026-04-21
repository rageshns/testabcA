elaborate bit more 1. Users & Endpoints (left column)
   - Claim Agents: access via AD SSO with MFA; primary day-to-day adjudication users.
   - SMEs / SOP Authors: HITL reviewers who author and curate Standard Operating Procedures used by the GenAI pipeline.
   - Platform Admins: restricted access via jump host only; no direct path to production workloads.

2. DMZ / Edge (Perimeter Zone)
   - Perimeter Firewall: default-deny posture; all ingress inspected.
   - F5 BIG-IP + ASM WAF: TLS 1.3 termination and L7 web application firewall.
   - NGINX Reverse Proxy: 2+ nodes, keepalived for HA; routes authenticated traffic inward.

3. Identity & Key Management
   - Active Directory (Forest root): central identity for users and service accounts.
   - Vault / Conjur: application secrets, API keys, DB credentials; short-lived tokens.

4. Application Tier - Kubernetes Cluster (Containers as Apps)
   - Kubernetes Cluster: hosts the containerized microservices below.
   - API Gateway: mTLS + JWT enforced for service-to-service and external calls.
   - Workflow Backend: orchestrates claim lifecycle states.
   - GenAI Backend: LLM-driven reasoning/adjudication service; calls the AI Inference Tier.
   - SOP Processor: ingests and indexes SOPs for retrieval.
   - Claim Data Processor: normalizes and enriches incoming claim payloads.
   - Rule Engine: deterministic execution for policy/edit rules (authoritative for compliance-critical decisions).

5. Windows Application VMs
   - Workflow Manager UI: IIS on Windows Server 2022; agent-facing console.
   - Agent UI: workbench surface for claim agents.
   - RPA Orchestrator: drives automation against legacy systems (e.g., Facets/QNXT).

6. AI Inference Tier (GPU Bare-metal)
   - H100: 2 nodes active/active for high-throughput inference.
   - vLLM / sglang: serving runtime for LLMs.
   - Prompt Guardrails: PII/PHI filter, OWASP LLM controls applied before and after model calls.

7. Data Tier
   - SQL Server AlwaysOn AG: workflow + GenAI database, synchronous replica.
   - SQL Read Replica (DR): async, DR-site replication.
   - Redis Sentinel: rule cache and session/low-latency lookups.
   - MinIO Object Store: SOP blobs, versioned; immutable retention where required.

8. Integration & Claims Platform
   - Facets / QNXT: system of record for claims; integrated via controlled APIs and RPA where APIs are unavailable.

9. Observability, Security & Backup (bottom)
   - Splunk ES (SIEM): security event correlation and alerting.
   - ELK: application logs and operational analytics.
   - Prometheus + Grafana: metrics, SLOs, dashboards.
   - CrowdStrike EDR: endpoint detection and response across VMs and nodes.
