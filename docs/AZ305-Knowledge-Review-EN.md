# AZ-305 Knowledge Review (English)

This review is organized from the supplied AZ305 source export. The complete source notes and all 39 original images are preserved in this repository.

- [Complete original notes](AZ305-Original-Notes.md)
- [Original image references (39)](AZ305-Original-Image-References.md)

## Exam-oriented service map

### Identity, governance, and monitoring
- **Azure Monitor** collects metrics, logs, traces, and resource changes. Use metrics for numeric near-real-time alerting; logs for investigation and event analysis; distributed traces to follow requests across application components.
- **Log Analytics workspace** stores Azure Monitor Logs. **Azure Monitor Agent (AMA)** and **Data Collection Rules (DCRs)** define collection. Use XPath filters for Windows event selection and KQL transformations/analysis. DCRs and DCEs are regional.
- **Microsoft Entra ID** provides cloud identity and access. Distinguish app registrations (application identity / user sign-in) from managed identities (Azure workload authentication to Azure resources).
- **Entra ID Governance** handles entitlement lifecycle and access reviews; **PIM** provides just-in-time privileged access; **Conditional Access** evaluates sign-in conditions.
- **Azure Lighthouse** delegates cross-tenant Azure resource management. **Entra Application Proxy** securely publishes internal web applications without requiring user VPN access.

### Storage and data
- Match storage to access pattern: Blob for object data, Files for managed file shares, Queue for messaging, Table for key/attribute NoSQL data.
- Prefer Microsoft Entra authorization and least-privilege RBAC where supported. A **SAS** grants scoped, time-limited access; protect account keys as broad credentials.
- For database migration, distinguish assessment/readiness from movement. **DMA** assesses compatibility and supports SQL migration scenarios; **SSMA** converts/migrates selected non-SQL databases to SQL Server; **Azure Migrate** assesses and coordinates broader infrastructure migration.

### Networking and security
- **Load Balancer** handles Layer 4 traffic; **Application Gateway** provides regional Layer 7 routing and can integrate WAF; **Front Door** provides global HTTP(S) entry and acceleration.
- **NAT Gateway** provides outbound connectivity for private workloads. **VPN Gateway** uses encrypted tunnels over the internet; **ExpressRoute** provides private connectivity. **Virtual WAN** centralizes large-scale branch and hub connectivity.
- **Private Link** gives private IP connectivity to supported services. **NSGs** filter traffic by source/destination, port, and protocol; **Azure Firewall** provides centralized network filtering; **DDoS Protection** protects public endpoints.
- Use **CDN/Front Door** to cache content near users; use **Azure Cache for Redis** for frequently accessed application data.

### Requirement → likely service

| Requirement | Likely choice |
| --- | --- |
| Centralized log queries and KQL | Log Analytics workspace |
| Collect guest OS logs with configurable rules | AMA + DCR |
| Just-in-time admin role activation | Entra PIM |
| Recurring certification of access | Entra Access Reviews |
| Azure workload accesses another Azure resource without stored credentials | Managed Identity |
| Private connectivity to a supported PaaS service | Private Link |
| Private workload needs controlled outbound internet | NAT Gateway |
| Encrypted site-to-site connectivity over public internet | VPN Gateway |
| Dedicated private on-premises-to-Azure connectivity | ExpressRoute |
| Cross-tenant delegated Azure operations | Azure Lighthouse |

Use the full original notes for the source-specific details, tables, and all 39 embedded images.
