---
name: Creating ADRs
description: Create Architectural Decision Records
when_to_use: when design is complete and the changes will benefit from creation of an Architectual Decision Record (ADR). We ONLY require these when the changes being planned have significant architectural impact. For example, we expect them- when public API contracts are changed or added, not when private APIs change, when database schemas change in a way that could impact other consumers. When we add new caches, including local or in-memory caches that affect the state of the system. When we require new infrastructure or services. When we are storing PII in a new way. When we're significantly changing project structure. When we need to make breaking changes to a public API. 
version: 1.0.0
---

# Writing ADRs

## Overview

Write an Architectural Decision Record for key decisions made as part of a plan. To effectively capture and communicate architectural decisions, Architectural Decision Records (ADRs) should include specific fields that provide comprehensive information about the decision-making process. Each field serves a distinct purpose, contributing to the clarity, context, and traceability of the decision. Below are the fields we adopt for ADRs, along with their respective purposes:

You are writing these for the engineer, however the consumers are all technical stakeholders for the project. Consider the teams or specialist roles who would want to be involved in reviewing this ADR. For example, InfoSec, AppSec, Privacy, Infrastructure, DevOps / Deployments, SRE teams. 

**Announce at start:** "I'm going to create an ADR (or ADRs) to cover the key decisions made as part of this plan"

**Context:** This should be run in the current working tree

**Save plans to:** `docs/adr/XXXX-<adr-title-filename-safe>.md` - scan the `docs/adr/` folder to find the latest ADR numbers and add one. 

**Structure:**


Title
Purpose: Clearly defines the subject of the architectural decision. The title should be concise yet descriptive, making it easy for team members to understand the decision at a glance.

Date
Purpose: Records the date when the architectural decision was made. This field contributes to the historical aspect of ADRs, enabling team members to understand when a decision was implemented or updated.

Status
Purpose: Indicates the current state of the architectural decision, whether it's proposed, accepted, rejected, deprecated or superseded. This field provides visibility into the decision's lifecycle. (See below for more details on the statuses.)

If the ADR is marked as superseded, then the superseding ADR title should be mentioned here.

Context
Purpose: Describes the circumstances, background, and context leading to the need for the architectural decision. This field helps readers understand the problem or requirement that prompted the decision. Use diagrams whenever appropriate to help readers quickly understand the details.

If the proposal supersedes a previous ADR then this should be mentioned in the context.

Decision
Purpose: Articulates the actual choice made in response to the identified problem or requirement. This field should be explicit and concise, outlining the selected option or course of action.

Consequences
Purpose: Details the anticipated outcomes, both positive and negative, of implementing the architectural decision. This field helps stakeholders understand the impact of the decision on the system and its components.

Alternatives (optional)
Purpose: Lists alternative solutions or approaches considered during the decision-making process. This field provides insight into the deliberation process and helps document the rejected options, explaining why they were not chosen.

Notes (optional)
Purpose: Provides somewhere to include ad-hoc information that will be useful to people reviewing this in future. Of particular value is an indication of who should review it. For example, if the decision affects something related to PII or privacy, then the privacy team. If it involves a security aspect or a new library, then the appsec team, and if it relates to some new infrastructure requirements or networking changes, then the infra team.

Starter template:
```md
# 0001. <<Title>>
Date: <<YYYY-MM-dd>>

## Status:  

Proposed on <<YYYY-MM-dd>>


## Context



## Decision



## Consequences



## Alternatives



## Notes

```