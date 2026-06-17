# Technical Playbook: Investigating a Playback Failure
**Version 1.0**

**Audience:** Developers, Site Reliability Engineers (SREs), Incident Responders, Platform Teams

**Owner:** Hector Adama

**Last Updated:** June 2026

*NOTE: EmuTV is a fictional streaming platform used as an example for this document. As this is a sample, it is not exhaustive and has been kept condensed for length, so some sections may appear to be missing steps.*

## Purpose
This technical playbook provides a structured approach for investigating playback failures within the EmuTV platform. The objective is not to provide exhaustive troubleshooting procedures for every failure mode. Instead, this guide helps developers:

 - Identify likely failure domains
 - Reduce time-to-diagnosis
 - Escalate issues appropriately
 - Understand system dependencies
 - Restore service quickly

## Scope
You should use this technical playbook when users report:

 - Videos fail to start
 - Playback sessions cannot be created
 - Infinite loading indicators
 - Playback authorisation errors
 - Streaming failures after playback begins

Do not use this playbook for:

 - Account login issues
 - Subscription purchase failures
 - Content publishing problems
 - Recommendation issues

## Understanding the Playback Path
Before troubleshooting, it is important to understand the request path as a failure anywhere on this path can prevent successful playback.

```mermaid
flowchart TD
    A[User] --> B[Customer Application]
    B --> C[Identity Domain]
    C --> D[Playback API]
    D --> E[Entitlement Validation]
    E --> F[DRM Service]
    F --> G[Manifest Service]
    G --> H[CDN Gateway]
    H --> I[Video Delivery]
```

## Incident Classification
Start by defining the scope of the impact. Consider the symptoms and the impact to business capabilities and end users in determining the severity of the incident.

### Severity Assessment
#### SEV-1
**Symptoms:**

 - Playback unavailable globally
 - All titles affected
 - Revenue-impacting outage

**Examples:***

 - Playback API unavailable
 - CDN outage
 - Authentication failure preventing playback authorisation

In the event of a SEV-1 incident, **immediate escalation is required**.

<hr>

#### SEV-2
**Symptoms:**

 - Significant customer impact
 - Regional failures
 - Specific device ecosystems affected

**Examples:**

 - Smart TV playback failures
 - Regional CDN routing issues

<hr>

#### SEV-3
***Symptoms:**

 - Limited impact
 - Isolated title failures
 - Single workflow degradation

**Examples:**

 - Incorrect content configuration
 - Metadata inconsistencies

## Initial Triage Checklist
Before investigating specific systems, you must follow a triage checklist. This checklist establishes a consisent and repeatable framework that will assist you in isolating root causes, reduce system downtime, and prevent critical issues from being overlooked.

To begin, answer the following questions:

### 1. What is failing?
Can users:

 - open the application?
 - browse content?
 - select content?
 - start playback?

### 2. Who is affected?
Determine:

 - Geographic region
 - Device platform
 - Subscription tier
 - Content type

Examples:

 - Only Android users
 - Only live content
 - Only premium subscribers

### 3. When did it start?
Identify:

 - Deployment timing
 - Configuration changes
 - Infrastructure incidents

Correlate failures with recent changes whenever possible.

## Failure Isolation Workflow
A failure isolation workflow confines the scope of an incident to a specific area, ideally preventing it from cascading into a widespread outage. It acts as a targeted blueprint for troubleshooting and debugging, allowing you to convert vague system errors into actionable diagnoses for faster resolution.

Never start with assumptions. Instead, use the following sequence to begin working through the issue:

### Step 1: Verify Identity Domain
Playback depends on successful user authentication.

Relevant Services:

 - Identity API
 - Session Service
 - Token Service

Questions:

 - Are tokens being validated?
 - Are sessions being created?
 - Has authentication latency increased?

Indicators:

 - Large increase in authentication failures
 - Token validation errors
 - Expired session anomalies

If authentication fails, stop. Playback investigation should shift to the Identity Platform team instead.

<hr>

### Step 2: Verify Entitlement Validation
Users must possess valid viewing entitlements to stream video content.

Relevant Domain:
Billing

Relevant Events:

 - SubscriptionActivated
 - SubscriptionExpired

Questions:

 - Are subscriptions being recognised?
 - Are entitlements resolving correctly?
 - Has Billing published recent events successfully?

Indicators:

 - Users can browse but cannot watch content.
 - Authorisation failures occur despite active subscriptions.

If entitlement validation fails, escalate to the Billing Engineering team.

<hr>

### Step 3: Verify Playback API
The Playback API must be healthy and available to create playback sessions.
 
Relevant Domain:
Billing

Questions:

 - Is the API healthy?
 - Are requests timing out?
 - Has latency increased?
 - Has there been any recent updates to the API?

Indicators:

 - Playback never starts.
 - Session creation fails.

**NOTE: If you verify a problem with the Playback API, it must be escalated to a Critical issue and further investigation must begin immediately.**

Key Metrics:

 - Request volume
 - Error rate
 - Latency
 - Availability

<hr>
 
### Step 4: Verify DRM Service
The Digital Rights Management (DRM) Service protects licensed content.

Questions:

 - Are license requirements succeeding?
 - Have DRM certificates expired?
 - Are failures platform-specific?

Indicators:

 - Playback starts then immediately fails.
 - Device-specific playback errors.

Examples:

 - Only Smart TV devices affected.
 - Only iOS devices affected.

These patterns often indicate DRM issues.

<hr>

### Step 5: Verify Manifest Generation
The Manifest Service generates streaming manifests.

Questions:

 - Are manifests being generated?
 - Are playback tokens valid?
 - Are generated URLs accessible?

Indicators:

 - Video remains in a loading state.
 - Playback initialisation fails.

Check:

 - Manifest generation logs
 - Playback session records
 - URL signing workflows

<hr>

### Step 6: Verify CDN Delivery
Most video traffic bypasses core application services.

Questions:

 - Are edge nodes healthy?
 - Are cache invalidation events succeeding?
 - Is regional routing functioning?

Indicators:

 - Playback succeeds in one region, but not in another
 - Intermittent buffering
 - Regional outages

Check:

 - CDN health dashboards
 - Edge node metrics
 - Regional availability reports

## Event Validation
When troubleshooting an incident, event validation can help you pinpoint the exact source of corrupted data, preventing bad inputs from crashing services downstream, and significantly reducing debugging time.

Relevant Events:

 - SubscriptionActivated
 - PlaybackStarted
 - PlaybackCompleted

Verify:

 - Events are being published
 - Events are being consumed
 - Lag remains within acceptable values

## Dependency Analysis
A dependency analysis maps out how code and services interact within an application. When there is a system failure, this analysis critically reduces downtime, prevents cascading issues, and allows you to isolate the root cause more quickly. 

Keep in mind that failure in any dependency can appear as a playback problem. Avoid treating playback failures as isolated playback issues and always investigate dependencies.
```
Playback API
|
├── Identity API
├── Entitlement Service
├── DRM Service
└── CDN Gateway
```

## Communications Guidance
When reporting findings, it is important to be as thorough, clear, and detailed as possible. Poorly communicating your findings can cause delays in incident response.

Avoid saying:
>"Playback is broken."

Instead, provide useful details:
>"The Playback API is healthy and identity validation is succeeding. Manifest generation is also succeeding. Regional CDN edge nodes in APAC are returning elevated error rates."

This clearly communicates:

 - Scope
 - Evidence
 - Suspected root cause

## Post-Incident Activities
Keeping a consistent and regularly updated record of all incidents, details, findings, and resolutions is crucial to the overall health of the platform. It will make it easier to identify and resolve problems in the future, especially if a problem is recurring and may indicate larger problems. 

Knowledge should continually evolve alongside the platform and documentation should be seen as an essential part of the process.

After resolving an issue:

### Capture the Timeline
You must document:

 - Detection
 - Escalation
 - Mitigation
 - Resolution

<hr>

### Update Runbooks
If new information was discovered:

 - Update troubleshooting procedures
 - Update dependency maps
 - Update known failure modes

<hr>

### Update Knowledge Assets
Review:

 - Architecture Narrative
 - Developer Portal
 - Service Catalog
 - Related Runbooks

<hr>

## Quick Reference
Playback Troubleshooting Sequence

 1. Identity
 2. Entitlements
 3. Playback API
 4. DRM Service
 5. Manifest Service
 6. CDN Delivery
 7. Event Validation

Remember: most playback incidents are often dependency failures rather than playback failures themselves. Always investigate upstream dependencies before assuming the Playback Domain is the root cause.

Understanding relationships between systems is the fastest path to identifying the actual source of failure.