````md
# [CLIENT NAME] — [ASSESSMENT TYPE]
> E.g., Application and API Assessment / Web Application Penetration Test / Internal Network Penetration Test / IoT Security Assessment

**Report ID:** [R-0000]  
**Classification:** Confidential  
**Issue Date:** [DD/MM/YYYY]  
**Version:** [1.0]  
**Author(s):** [Name(s)]  
**Reviewer(s):** [Name(s)]  
**Final Approver:** [Name(s)]  

---

## Confidentiality Notice
<!--
Insert confidentiality clause, copyright statement, distribution restrictions, and permitted use of this document.
It is also important to state that this report contains sensitive security information.
-->

---

# Table of Contents
<!--
Generate an automatic table of contents if the tool supports it.
-->

1. Revision History  
2. Introduction  
   2.1 Client Context  
   2.2 Assessment Objective  
   2.3 Scope and Duration  
   2.4 Included Scenarios  
   2.5 In-Scope Assets  
   2.6 Out-of-Scope Assets  
   2.7 Constraints and Assumptions  
3. Executive Summary  
   3.1 Overall Result Overview  
   3.2 Key Risks  
   3.3 Positive Observations  
   3.4 Next Steps  
   3.5 Caveats  
   3.6 Risk Categories  
   3.7 CVSS Equivalency  
   3.8 Visual Summary  
4. Methodology  
   4.1 Testing Approach  
   4.2 Assessment Phases  
   4.3 Techniques Used  
   4.4 References / Frameworks  
   4.5 Methodological Limitations  
5. Attack Surface Mapping  
6. Recommended Actions (Consolidated View)  
7. Technical Findings  
8. Additional Information  
9. Appendices  

---

# 1. Revision History

| Name | Date | Version | Comment |
|---|---:|---:|---|
| [Name] | [DD/MM/YYYY] | [0.1] | Initial draft |
| [Name] | [DD/MM/YYYY] | [0.2] | Technical review |
| [Name] | [DD/MM/YYYY] | [1.0] | Final version |

<!--
Record all relevant document changes.
Useful for audit trail, QA, and document governance.
-->

---

# 2. Introduction

## 2.1 Client Context
<!--
Briefly describe the organisation, the assessed system, and the business context.
Answer: what is the application/environment, and why is it important?
Avoid excessive marketing language; keep the focus on operational and risk context.
-->

**Summary of the assessed environment:**  
[Insert description of the system, application, API, infrastructure, or device.]

**Business importance:**  
[Insert the importance of the system to operations, revenue, compliance, customers, etc.]

---

## 2.2 Assessment Objective
<!--
Explain why the test was performed:
- identify vulnerabilities
- validate security controls
- support remediation
- satisfy contractual, regulatory, or internal requirements
-->

The objective of this assessment was to identify exploitable vulnerabilities, validate security controls, and provide practical recommendations to improve the security posture of the in-scope environment.

---

## 2.3 Scope and Duration
<!--
Clearly state:
- testing period
- effort (days/hours)
- work phases
- attack perspective (external, internal, authenticated, unauthenticated, grey box, white box, black box)
-->

**Assessment period:** [Start date] to [End date]  
**Total effort:** [X days / X hours]  
**Testing model:** [Black Box / Grey Box / White Box]  
**Perspective:** [External / Internal / Authenticated / Unauthenticated / Remote]

**Work phases performed:**
- Phase 1 — [e.g., reconnaissance and enumeration]
- Phase 2 — [e.g., web application and API testing]
- Phase 3 — [e.g., impact validation]
- Phase 4 — [e.g., reporting]

---

## 2.4 Included Scenarios
<!--
List the testing scenarios that were actually considered.
Examples:
- web application
- API
- authentication/authorisation
- file upload
- business logic
- exposed infrastructure
- IoT / mobile / internal network / social engineering
-->

The following scenarios were included:
- [Scenario 1]
- [Scenario 2]
- [Scenario 3]

---

## 2.5 In-Scope Assets
<!--
Explicitly list authorised assets.
May include:
- domains
- IPs
- ranges
- applications
- API endpoints
- environments
- devices
- test accounts
- supplied components (source code, configs, documentation)
-->

**Assets authorised for testing:**
- [domain / IP / application / API / device]
- [domain / IP / application / API / device]

**Credentials / profiles provided:**
- [profile 1]
- [profile 2]

---

## 2.6 Out-of-Scope Assets
<!--
Essential for legal protection and operational clarity.
Explicitly indicate what was NOT tested.
Examples:
- production
- third parties
- legacy systems
- specific IP ranges
- critical functions
- destructive testing
-->

**Out of scope:**
- [item 1]
- [item 2]
- [item 3]

**Prohibited activities:**
- [e.g., intentional DoS]
- [e.g., phishing real users]
- [e.g., persistent modification of data]

> **Note:** Do not test any asset without formal written authorisation.

---

## 2.7 Constraints and Assumptions
<!--
Document limitations that affect interpretation of the results.
Examples:
- short testing window
- dependence on supplied accounts
- WAF/CDN in place
- time restrictions
- no access to internal environment
-->

**Known constraints:**
- [constraint 1]
- [constraint 2]

**Assumptions made:**
- [assumption 1]
- [assumption 2]

---

# 3. Executive Summary
<!--
Section aimed at executives and managers.
It should be short, clear, and not overly technical.
Ideally around 1 page or readable in less than 5 minutes.
-->

## 3.1 Overall Result Overview
<!--
Summarise the general performance of the tested environment.
Answer:
- is the overall posture good, reasonable, or critical?
- were serious flaws found?
- is there evidence of good practice?
-->

The assessment identified **[number] findings**, distributed across **[risk categories]**.  
Overall, the environment demonstrated **[high-level assessment]**. Positive aspects were observed, such as **[existing controls]**, although relevant risks remain and should be addressed.

---

## 3.2 Key Risks
<!--
List the most important findings from a business perspective.
Describe impact in accessible language.
Avoid excessive technical detail here.
-->

The most significant risks identified were:

1. **[Most significant finding title] — [Severity]**  
   [Explain the risk and impact in executive language.]

2. **[Finding title] — [Severity]**  
   [Explain the impact.]

3. **[Finding title] — [Severity]**  
   [Explain the impact.]

---

## 3.3 Positive Observations
<!--
Important to balance the report. Showing mature controls improves credibility and helps contextualise risk.
Examples:
- use of WAF
- strong CSP
- security headers
- MFA
- privilege segregation
-->

The following positive observations were made:
- [positive control 1]
- [positive control 2]
- [positive control 3]

---

## 3.4 Next Steps
<!--
Guide prioritisation.
Can follow this logic:
- critical: immediate
- high: urgent
- medium: within 90 days
- low/info: backlog / risk register
-->

Recommended actions:
- **Critical:** address immediately.
- **High:** address as a priority after any critical issues.
- **Medium:** plan remediation within [timeframe].
- **Low / Informational:** record, track, and assess the cost-benefit of remediation.

---

## 3.5 Caveats  # optional in our case
<!--
A critical section from both technical and contractual perspectives.
Make it clear that:
- the test was time-limited
- scope was restricted
- absence of evidence is not evidence of absence
- a real attacker is not limited by time or scope
-->

This report reflects the results of a time-limited assessment restricted to the authorised in-scope assets. The absence of evidence of exploitation should not be interpreted as proof that no other vulnerabilities exist. A continuous cycle of assessment, remediation, and retesting is recommended.

---

## 3.6 Risk Categories  # optional in our case
<!--
Explain the taxonomy used in the report.
May be:
Critical / High / Medium / Low / Informational
Include a simple rationale for each category.
-->

| Category | Description |
|---|---|
| Critical | Severe risk, likely or simple exploitation, significant impact |
| High | Important risk with strong potential for compromise |
| Medium | Meaningful risk with moderate impact or conditional exploitation |
| Low | Limited risk, difficult to exploit, or low impact |
| Informational | Exposure, observation, or hardening improvement opportunity |

---

## 3.7 CVSS Equivalency
<!--
Map the internal classification to CVSS ranges.
Important to note that not every finding fits well into CVSS.
-->

| Category | CVSS v3.x Range |
|---|---|
| Critical | 9.0 – 10.0 |
| High | 7.0 – 8.9 |
| Medium | 4.0 – 6.9 |
| Low | 0.1 – 3.9 |
| Informational | N/A |

> **Note:** Not all findings are well represented by CVSS. Architectural, exposure-related, or governance issues may require contextual assessment.

---

## 3.8 Visual Summary
<!--
INSERT CHARTS HERE.
This was emphasised in the material provided.
Suggestions:
1. bar chart showing number of findings by severity
2. pie chart showing percentage distribution of findings
3. risk heatmap (impact vs likelihood)
4. remediation timeline
-->

### Chart 1 — Distribution of findings by severity
<!-- Insert bar chart -->

### Chart 2 — Percentage of findings by category
<!-- Insert pie or donut chart -->

### Chart 3 — Risk matrix
<!-- Insert impact vs likelihood heatmap -->

---

# 4. Methodology

## 4.1 Testing Approach
<!--
Describe how the test was conducted.
It should support understanding and reproducibility.
-->

The assessment was conducted using manual techniques supported by tools, with emphasis on practical validation of identified findings.

---

## 4.2 Assessment Phases
<!--
Adapt depending on the assessment type.
-->

1. Reconnaissance and enumeration  
2. Attack surface mapping  
3. Vulnerability identification  
4. Controlled exploitation  
5. Impact validation  
6. Evidence consolidation  
7. Technical and executive reporting  

---

## 4.3 Techniques Used
<!--
Examples:
- port and service enumeration
- authentication analysis
- authorisation testing
- parameter fuzzing
- header analysis
- upload testing
- sanitisation validation
- manual exploitation
-->

**Examples of techniques used:**
- Service enumeration
- Authentication and session management analysis
- Horizontal and vertical authorisation testing
- Input and output validation
- Testing for SSRF, XSS, SQLi, path traversal, IDOR, etc.
- TLS/SSL configuration review
- Inspection of outdated components

---

## 4.4 References / Frameworks
<!--
Cite frameworks used in the engagement.
Examples:
- OWASP Top 10
- OWASP API Security Top 10
- OWASP IoT Top 10
- PTES
- NIST
- STRIDE
- CWE/CVE/CVSS
-->

The assessment was informed by the following, where applicable:
- OWASP Top 10
- OWASP API Security Top 10
- OWASP IoT Top 10
- STRIDE
- CWE / CVE / CVSS
- [others]

---

## 4.5 Methodological Limitations   #optional to our case
<!--
State where depth was limited by time, access, scope, or operational impact concerns.
-->

Methodological limitations included:
- [limitation 1]
- [limitation 2]

---

# 5. Attack Surface Mapping
<!--
Section strongly recommended based on the lecture material.
Especially valuable for web, API, infrastructure, and IoT assessments.
-->

## 5.1 Attack Surface Overview
<!--
Describe relevant channels, interfaces, components, and dependencies.
Examples:
- web app
- API
- mobile app
- gateway
- database
- message queue
- CDN/WAF
- third-party integrations
-->

**Mapped components:**
- [Component 1]
- [Component 2]
- [Component 3]

---

## 5.2 Architecture / Flow Diagram
<!--
INSERT DIAGRAM HERE.
This is one of the best sections for visual value.
Suggestion:
client -> WAF/CDN -> application -> API -> database -> internal services -> third parties
For IoT:
device -> gateway -> mobile app -> cloud -> admin panel
-->

### Diagram 1 — Assessed environment architecture
<!-- Insert architecture diagram -->

---

## 5.3 Identified Attack Vectors
<!--
List potential attack paths observed during the assessment.
-->

| Vector | Surface | Possible Impact | Notes |
|---|---|---|---|
| [e.g., reflected input] | Web application | XSS / session theft | [notes] |
| [e.g., URL fetch functionality] | API / internal tool | SSRF | [notes] |
| [e.g., exposed ports] | Infrastructure | enumeration / service attack | [notes] |

---

# 6. Recommended Actions (Consolidated View)
<!--
Management-friendly summary table to support triage and planning.
Very important for managers.
-->

| ID | Vulnerability Title | Recommended Action Summary | Risk Category | CVSS | Suggested Owner | Suggested Timeline |
|---|---|---|---|---:|---|---|
| 1 | [Title] | [Short action] | [Medium] | [5.4] | [Team] | [30 days] |
| 2 | [Title] | [Short action] | [Low] | [3.7] | [Team] | [90 days] |

<!--
Best practice: order by severity.
Optional: add a status column in tracking versions.
-->

---

# 7. Technical Findings
<!--
Order by priority/risk.
Each finding should stand alone and be reproducible.
-->

---

## 7.[N]. [FINDING TITLE]

### 7.[N].1 Finding Summary
<!--
Short, direct, objective description.
Explain the issue in one concise paragraph.
-->

[Describe the finding briefly.]

---

### 7.[N].2 Risk Classification
<!--
Present the internal rating and CVSS.
If CVSS does not apply, explain why.
-->

| Metric | Value |
|---|---|
| Risk Category | [Critical / High / Medium / Low / Informational] |
| CVSS v3.x | [X.X or N/A] |
| CVSS Vector | [vector string or N/A] |

**Classification rationale:**  
[Explain why the risk was rated this way in the client’s context.]

---

### 7.[N].3 Background
<!--
Explain the vulnerability type for readers who may not be familiar with it.
A standard internal finding library may be used here.
Do not copy third-party text without permission.
-->

[Insert conceptual explanation of the vulnerability.]

---

### 7.[N].4 Evidence / Technical Details
<!--
The most important section from a technical perspective.
It must prove the flaw exists and allow revalidation.
Include:
- endpoint / URL / host / port
- parameters
- payload
- observed response
- expected vs observed behaviour
-->

**Affected item(s):**
- [hostname / URL / IP / service / endpoint / component]

**Preconditions:**
- [authenticated? required role? prior access?]

**Steps to reproduce:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

**Example payload / request:**
```http
[Insert HTTP request, command, query, payload, or technical snippet]
````

**Observed response / evidence:**

```text
[Insert relevant response, error, log, output, code excerpt, etc.]
```

<!--
INSERT SCREENSHOTS HERE.
Whenever possible, include visual evidence.
-->

### Figure — Evidence of the finding

<!-- Insert screenshot with caption -->

### Diagram — Exploitation flow (optional)

<!--
Insert when the finding is complex, multi-stage, or involves several components.
E.g., SSRF -> internal service -> enumeration -> indirect impact
-->

---

### 7.[N].5 Impact Analysis

<!--
Contextualise the impact in the client’s real environment.
Answer:
- what could an attacker achieve?
- what would the consequence be?
- are there existing mitigations?
-->

[Explain the technical and business impact.]

---

### 7.[N].6 Exploitability / Likelihood Analysis

<!--
Separate impact from likelihood.
May include:
- authentication requirement
- user interaction
- dependence on legacy browser
- WAF/CDN protection
- need for botnet
-->

[Explain difficulty, prerequisites, and mitigating factors.]

---

### 7.[N].7 Recommendation  #optional on our case

<!--
Provide concrete, actionable remediation guidance.
Ideally layered:
- immediate fix
- complementary hardening
- process improvement
-->

**Primary recommendation:**
[Describe the most direct fix.]

**Additional recommendations:**

* [complementary action 1]
* [complementary action 2]
* [complementary action 3]

**Suggested priority:** [Immediate / High / Planned]

---

### 7.[N].8 References

<!--
Links, standards, cheatsheets, CVE/CWE, vendor documentation.
-->

1. [Reference 1]
2. [Reference 2]
3. [Reference 3]

---

### 7.[N].9 Retest Notes

<!--
Useful to facilitate later verification.
-->

Retesting should confirm:

* [item 1]
* [item 2]
* [item 3]

---

> **Repeat the structure above for each finding.**

---

# 8. Additional Information  #optional on our case

<!--
Section for contextual supporting data that adds value but is not itself a finding.
Highly aligned with the Pentest Ltd example.
-->

## 8.1 WHOIS / Domain Registration  #optional on our case

<!--
Insert relevant domain and ownership information only where useful.
Avoid dumping raw data without analysis.
-->

**Domain:** [domain.com]
**Summary:** [relevant observations]

```text
[Insert concise and useful output]
```

---

## 8.2 Port Scan Results

<!--
Include only what is relevant.
Ideally summarise and explain significance.
-->

| Target | Port | State | Service | Notes   |
| ------ | ---: | ----- | ------- | ------- |
| [host] |  443 | open  | https   | [notes] |
| [host] |   80 | open  | http    | [notes] |

<!--
INSERT CHART OR DIAGRAM IF HELPFUL.
E.g., chart showing exposed services by host.
-->

### Chart — Exposed services by target

<!-- Insert optional chart -->

---

## 8.3 SSL/TLS Assessment

<!--
A useful section for web/API engagements.
Include:
- supported protocols
- cipher suites
- security headers
- tool results
- concise observations
-->

### 8.3.1 Executive TLS Summary

<!--
Do not just paste raw output. Summarise what matters.
-->

* Supported protocols: [TLS 1.2 / TLS 1.3]
* Insecure protocols: [not supported / supported]
* Weak suites: [yes / no]
* HSTS: [yes / no]
* Notes: [summary]

### 8.3.2 Technical Evidence

```text
[Insert summarised sslscan / testssl / nmap output]
```

---

## 8.4 Components / Libraries / Versions

<!--
Useful for documenting outdated software or relevant dependencies.
-->

| Component | Current Version | Latest Known Version | Risk  | Notes   |
| --------- | --------------- | -------------------- | ----- | ------- |
| [jQuery]  | [3.4.1]         | [3.5.1+]             | [Low] | [notes] |

---

## 8.5 Assessment Timeline

<!--
Useful in formal projects.
-->

| Date    | Activity                 |
| ------- | ------------------------ |
| [DD/MM] | Kick-off / authorisation |
| [DD/MM] | Testing                  |
| [DD/MM] | Consolidation            |
| [DD/MM] | Delivery                 |

<!--
INSERT TIMELINE GRAPHIC if desired.
-->

---

# 9. Appendices

## 9.1 Tools Used

<!--
List the main tools and their purpose.
No need to list everything without context.
-->

| Tool            | Purpose                              |
| --------------- | ------------------------------------ |
| [Burp Suite]    | Web testing and request manipulation |
| [Nmap]          | Service enumeration                  |
| [sslscan]       | TLS analysis                         |
| [Custom script] | Specific validation                  |

---

## 9.2 Detailed Methodology

<!--
Expand procedural detail if the main body is intentionally concise.
-->

[Insert additional details.]

---

## 9.3 Raw Data / Outputs

<!--
Insert lengthy outputs that would clutter the main body.
E.g., full scans, full headers, extensive listings.
-->

```text
[Insert raw output]
```

---

## 9.4 Scripts / Proofs of Concept

<!--
Include PoC only if contractually appropriate and without introducing unnecessary risk.
Prefer controlled versions and clearly identify their purpose.
-->

### [Script / PoC Name]

```python
# Insert script or pseudocode
```

**Usage notes:**

<!--
Explain the purpose of the script, its limitations, and context.
-->

---

## 9.5 Scoring References

<!--
Document the risk methodology used.
-->

* CVSS v3.x
* CWE
* [other references]

---

# Optional Annex — Remediation Plan

<!--
Very useful for turning the report into an action plan.
-->

| ID | Finding   | Action   | Owner  | Priority | Due Date | Status |
| -- | --------- | -------- | ------ | -------- | -------- | ------ |
| 1  | [Finding] | [Action] | [Team] | [High]   | [DD/MM]  | [Open] |

---

# Optional Annex — Presentation Summary

<!--
A shortened version for executive committee or closing meeting.
Can later be turned into slides.
-->

## Core Message

[Summary in 3 to 5 lines]

## Top 3 Risks

1. [Risk 1]
2. [Risk 2]
3. [Risk 3]

## Top 3 Actions

1. [Action 1]
2. [Action 2]
3. [Action 3]

```
```
