# 📘 Microsoft Purview – **Expanded FAQ & How-To Guide**

*A practical, example-rich reference that touches every major angle of the platform.*

---

## Contents

1. Core Concepts
2. Governance & Glossary
3. Scanning, Classification & Lineage
4. Security, Labels & Policies
5. Insights, Cost & Optimization
6. Architecture, Integration & Automation
7. Organization & People (Collections, Data Councils, Roles)
8. Comparative & Strategic Questions
9. Road-map, Limits & Best Practices
10. “Quick Answers” cheat-sheet

---

## 1  |  Core Concepts

### **Q 1.1 What is Microsoft Purview?**

**Answer** Purview is Microsoft’s unified **data-governance platform** that:

* **Discovers** data anywhere (Azure, on-prem, AWS, SaaS).
* **Catalogs** technical + business metadata in a central **Data Map**.
* **Classifies & labels** sensitive data (PII, PHI, PCI…).
* **Tracks lineage** from raw source to dashboards.
* **Enforces policies** for secure, compliant use.

> **Example** A retailer with Azure SQL, SAP on-prem, and Power BI registers all sources in Purview. Analysts find “Sales Orders” once and reuse it everywhere; compliance sees where every credit-card number lives.

### **Q 1.2 Is Purview only for Azure-first companies?**

No. Native connectors exist for AWS S3, on-prem SQL/Oracle (via **Self-Hosted Integration Runtime**), Power BI, Microsoft 365, and preview support for Snowflake & Google BigQuery.

---

## 2  |  Governance & Glossary

### **Q 2.1 What is a Glossary Term & who is its “Owner”?**

A **Glossary Term** is the *authoritative* business definition of a concept (e.g., “Active Customer”). The **Owner** is accountable for its accuracy, updates, and dispute resolution.

| Term           | Definition                     | Owner                    | Example Usage          |
| -------------- | ------------------------------ | ------------------------ | ---------------------- |
| Customer Churn | % of customers lost in a month | Marketing Analytics Lead | KPI in churn dashboard |

### **Q 2.2 Can glossary links be automatic?**

Not entirely. Purview suggests matches (based on names / classifiers), but a steward must confirm the link for auditability.

### **Q 2.3 What is a Data Council?**

A cross-functional committee (CDO, Legal, Security, Domain Leads, Data Stewards) that:

1. Approves glossary terms & standards.
2. Sets classification & retention rules.
3. Monitors governance KPIs (via Purview Insights).
4. Arbitrates ownership conflicts.

> **Example** Finance wants “Gross Margin” re-defined; the council votes, updates the glossary, and notifies report owners.

---

## 3  |  Scanning, Classification & Lineage

### **Q 3.1 How is end-to-end lineage built (Blob → Synapse → Power BI)?**

Register **all** stages in Purview **and** use supported orchestration (ADF / Synapse Pipelines). If Synapse is missing, lineage shows a gap: *Blob → Unknown Process → Power BI*.

### **Q 3.2 Can I scan unstructured files?**

Yes: CSV, Parquet, JSON, Excel, PDF. Purview extracts schema where possible and applies classifiers. Deep lineage is limited unless the data flows through an ETL that Purview understands.

### **Q 3.3 Can I create custom classifiers?**

✅ Use regex/keyword rules (e.g., detect `EMP-\d{4}` for employee IDs). Add them to a scan rule set; results appear in Insights and metadata.

### **Q 3.4 Does Purview deduplicate assets?**

No automatic deduplication; use naming conventions, tags, or custom metadata to identify duplicates.

---

## 4  |  Security, Labels & Policies

### **Q 4.1 What’s the difference between “Classification” and “Sensitivity Label”?**

| Aspect           | Classification                      | Sensitivity Label                          |
| ---------------- | ----------------------------------- | ------------------------------------------ |
| Purpose          | Identify data type (PII, Financial) | **Protect** data (encrypt, restrict)       |
| Enforced?        | Informational                       | Yes – via Microsoft Information Protection |
| Shown in Office? | No                                  | Yes (Word, Excel, Outlook)                 |

### **Q 4.2 What is a Policy and why do I need it?**

A **Policy** automates *who* can do *what* with *which* data.
*Example*: *Allow HR analysts to SELECT from tables labeled “Confidential–HR” in Azure SQL.*
Policies prevent over-permissioning, ease audits, and centralize access management.

### **Q 4.3 Alerting – can I get notified on scan failures or new sensitive data?**

Purview logs to Azure Monitor / Log Analytics. Wire alerts via **Azure Alerts**, **Logic Apps**, or **Event Grid** to email, Teams, or ticketing.

---

## 5  |  Insights, Cost & Optimization

### **Q 5.1 Are Insights free to view?**

**Yes.** Costs arise from:

1. **Capacity Units** consumed while scanning & classifying.
2. **Metadata storage** in the Data Map.

### **Q 5.2 How does classification impact cost?**

It lengthens scans ⇒ more Capacity Units; adds more metadata ⇒ more storage.
**Tip** Classify only high-risk domains (Finance, HR) and sample low-risk stores.

### **Q 5.3 Cost-saving checklist**

* Scope scans to needed containers / schemas.
* Use incremental scans.
* Schedule off-peak.
* Purge obsolete metadata.
* Monitor with Azure Cost Management.

---

## 6  |  Architecture, Integration & Automation

### **Q 6.1 Can I automate Purview (CI/CD)?**

Yes: REST API & Azure SDK allow scripting source registration, scan kicks, metadata tagging. Integrate into Azure DevOps / GitHub Actions.

### **Q 6.2 Does Purview support Microsoft Fabric & OneLake?**

Integration is rolling out (2024–25). Fabric workspaces, Lakehouses, and OneLake data appear in Purview lineage and catalog.

### **Q 6.3 Data residency – where is metadata stored?**

In the Azure region you pick when creating the Purview account; complies with data-sovereignty rules.

### **Q 6.4 Disaster Recovery**

Purview is a PaaS service; Microsoft handles replication within the region. Cross-region DR (metadata copy) can be scripted via API export/import.

---

## 7  |  Organization & People

### **Q 7.1 What are Collections?**

Hierarchical containers for assets + permissions (e.g., *Root → Finance → Payroll*). Roles are **scoped** to collections.

### **Q 7.2 Which Azure RBAC roles exist?**

| Role                  | Key Rights                        |
| --------------------- | --------------------------------- |
| **Collection Admin**  | Add users, manage sub-collections |
| **Data Source Admin** | Register / scan sources           |
| **Data Curator**      | Edit metadata, glossary links     |
| **Data Reader**       | View assets                       |

### **Q 7.3 Can small/medium businesses use Purview?**

Yes—pay-as-you-go, start with a handful of sources, grow later.

---

## 8  |  Comparative & Strategic Questions

### **Q 8.1 Purview vs Collibra / Alation?**

| Factor            | Purview                                    | Collibra / Alation            |
| ----------------- | ------------------------------------------ | ----------------------------- |
| Azure Integration | Native (RBAC, MIP, ADF, Synapse, Power BI) | Connector-based               |
| Pricing           | Usage (CUs + metadata)                     | Subscription                  |
| On-prem Connect   | SHIR                                       | Native agents/lineage servers |
| MIP Labels        | **Built-in**                               | External tools                |

Choose Purview for Azure-centric estates or Microsoft 365 alignment; choose others for deep multi-cloud heterogeneity or existing investment.

---

## 9  |  Road-map, Limits & Best Practices

* **Road-map (2025)** – deeper Fabric lineage, GA for AWS Glue & Snowflake scanners, UI alerting, policy insights.
* **Current limits** – 10 M assets per account (soft limit), 50 custom classifiers, 1000 collections.
* **Best practices** – domain-oriented collections, auto-scan high-risk sources weekly, large-file sampling, enforce glossary ownership, track KPIs quarterly with the Data Council.

---

## 10  |  “Quick Answers” Cheat-Sheet

| Question              | 3-Word Answer             |
| --------------------- | ------------------------- |
| View Insights Cost?   | **Viewing is free**       |
| End-to-End Lineage?   | **Scan everything**       |
| Sensitive Data Label? | **MIP sensitivity label** |
| Reduce Scan Cost?     | **Scope + Incremental**   |
| Glossary Owner Role?  | **Definition gatekeeper** |

---

### Need something else?

Ask for 👉 role-specific FAQs, step-by-step labs, or architecture diagrams, and I’ll add them inline!
