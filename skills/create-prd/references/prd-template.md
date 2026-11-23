# PRD Template

Fill this when writing `prd.md`. Keep every section heading; write "N/A — <reason>" or "TBD" instead of deleting a section.

````markdown
# Product Requirements Document (PRD) Template

# 1. Overview / Purpose

## **Summary**

Provide a concise 2–3 sentence description of the feature or initiative.

## **Problem Statement**

What specific user or business problem does this solve?

## **Proposed Solution**

High-level overview of the intended approach or experience.

---

# 2. Objectives & Goals

## **Primary Objectives**

- [ ]  Objective 1
- [ ]  Objective 2

## **Success Metrics / KPIs**

- Metric 1 (target value or percentage change)
- Metric 2

## **Non-Goals**

- Explicitly state what is *not* part of this scope.

---

# 3. Target Users & Use Cases

## **User Personas / Roles**

- Example: Account Owner
- Example: Team Manager
- Example: Support Agent

## **Pain Points / Needs**

Brief bullets describing each user’s pain points this feature addresses.

## **User Stories**

- As a [role], I want to [action], so that [benefit].
- As a [role], I want to [action], so that [benefit].

---

# 4. Key Features & Requirements

## **Functional Requirements**

> **NOTE:** This can be a list instead of a table if more convenient.
> 

| Priority | Requirement | Description |
| --- | --- | --- |
| Must-Have | Example: User can upload documents | Supports PDF, DOCX, and image formats |
| Should-Have | Example: Real-time validation of required fields | Inline error messages |
| Nice-to-Have | Example: Bulk import from CSV | Future phase |

## **Non-Functional Requirements**

- Security: [Compliance requirements, e.g. SOC 2, GDPR, HIPAA]
- Performance: <300 ms response time on main queries
- Availability: 99.9% uptime SLA target

---

# 5. User Experience & Design

## **Key UX Notes:**

- Describe layout, navigation, and core interaction flow
- Accessibility (WCAG 2.1 AA compliance)

---

# 6. Technical Considerations

## **Architecture Overview**

Brief outline of relevant backend / frontend components (e.g., API service, database package, shared UI library).

## **Data Model Changes**

- New tables, columns, or relationships
- Example: Add `invoices` table linked to `customers`

## **Dependencies**

- Background jobs or workflows
- Database functions or triggers
- Deployment or infrastructure considerations

## **Integration Points**

- APIs, external systems, or services (e.g., search, realtime, email, payments providers)

---

# 7. Timeline & Milestones

| Milestone | Target Date | Owner | Notes |
| --- | --- | --- | --- |
| Draft Approval | YYYY-MM-DD |  |  |
| Design Finalization | YYYY-MM-DD |  |  |
| Dev Start | YYYY-MM-DD |  |  |
| Demo Launch | YYYY-MM-DD |  |  |
| Full Rollout | YYYY-MM-DD |  |  |

---

# 8. Metrics & Validation

## **KPIs**

- Engagement metric (e.g., % of users creating reports)
- Efficiency metric (e.g., avg. time saved per task)

## **Experimentation Plan**

- A/B testing strategy, if applicable
- Post-launch survey or usage analytics

## **Post-Launch Review**

- What data will determine success or need for iteration?

---

# 9. Risks & Assumptions

## Risks

- Delayed sync from a third-party system
    - Impact: High
    - Likelihood: Medium
    - Mitigation:
- Role-based visibility edge cases
    - Impact: High
    - Likelihood: Medium
    - Mitigation:

## **Assumptions**

- Data from connected systems is consistent and up to date.
- All users have valid email credentials for SSO.
````
