# EmuTV Engineering Portal
**Version 1.0**
**Owner:** Hector Adama

**Last Updated:** June 2026

*NOTE: EmuTV is a fictional streaming platform used as an example for this document.*

## Portal Home
### Platform overview
EmuTV consists of nine business domains and more than fifty independently deployable services.

The Engineering Portal helps engineers answer questions such as:

 - Which service owns this capability?
 - What APIs are available?
 - Which systems depend on this service?
 - Who owns this component?
 - What events are published?
 - What standards apply?

## Knowledge Map
### Business capability map

```
Customer Experience
|
├── Web Platform
├── Mobile Platform
├── Smart TV Platform
└── Gaming Platform

Content Management
|
├── Content Ingestion
├── Metadata Management
├── Publishing
└── Rights Management

Discovery
|
├── Search
├── Recommendations
├── Personalisation
└── Trending Content

Streaming
|
├── Playback Authorisation
├── Manifest Generation
├── CDN Delivery
└── DRM Protection

Commerce
|
├── Billing
├── Subscription Management
├── Entitlements
└── Payment Processing

Advertising
|
├── Campaign Management
├── Ad Decisioning
├── Measurement
└── Attribution

Analytics
|
├── Event Collection
├── Reporting
├── Experimentation
└── Data Warehouse

Platform Engineering
|
├── Kubernetes
├── CI/CD
├── Observability
├── Security
└── Developer Experience
```

## Domain Directory
### Identity Domain
Purpose:
Authentication, authorisation, and customer identity management.

Owned by:
Identity Platform Team

Key Services:

 - Identity API
 - Session Service
 - Profile Service
 - Token Service

Critical Events:

 - UserCreated
 - UserUpdated
 - UserDeleted

Dependencies:

 - Billing
 - Recommendations
 - Playback

Business Criticality:
Tier 1

### Catalog Domain
Purpose:
Content metadata and discovery.

Owned by:
Content Platform Team

Key Services:

 - Catalog API
 - Metadata Service
 - Search Service

Critical Events:

 - ContentCreated
 - ContentPublished
 - ContentArchived

Dependencies:

 - Recommendations
 - Playback
 - Search

Business Criticality:
Tier 1

### Playback Domain
Purpose:
Video delivery and streaming authorisation.

Owned by:
Playback Engineering

Key Services:

 - Playback API
 - Manifest Service
 - DRM Service
 - CDN Service

Critical Events:

 - PlaybackStarted
 - PlaybackPaused
 - PlaybackCompleted

Business Criticality:
Tier 1

### Recommendation Domain
Purpose:
Personalised content discovery.

Owned by:
Discovery Engineering

Key Services:

 - Recommendations Engine
 - Feature Service
 - Ranking Service

Critical Events:

 - RecommendationGenerated

Business Criticality:
Tier 2

## Service Catalog
### Identity API
Service Type:
User-facing API

Purpose:
Authenticate users and issue access tokens.

Owned by:
Identity Platform Team

Repository:
identity-api

Technology Stack:

 - TypeScript
 - Node.js
 - PostgreSQL
 - Kubernetes

Consumes Events:

 - SubscriptionExpired

Publishes Events:

 - UserAuthenticated

Dependencies:

 - Profile Service
 - Session Service

Availability Target:
99.95%

Support Tier:
Tier 1

### Catalog API
Service Type:
User-facing API

Purpose:
Provide content metadata.

Owned by:
Content Platform Team

Repository:
content-api

Technology Stack:

 - TypeScript
 - PostgreSQL
 - Elasticsearch

Consumes Events:

 - ContentPublished

Publishes Events:

 - MetadataUpdated

Availability Target:
99.95%

Support Tier:

Tier 1

### Recommendation Engine
Service Type:
Internal Service

Purpose:
Generate personalised recommendations.

Owned by:
Discovery Engineering

Technology Stack:

 - Python
 - Feature Store
 - Redis

Consumes Events:

 - PlaybackCompleted
 - SearchPerformed

Publishes Events:

 - RecommendationsGenerated

Availability Target:
99.95%

Support Tier:
Tier 2

### Playback API
Service Type:
User-facing API

Purpose:
Authorise streaming sessions.

Owned by:
Playback Engineering

Technology Stack:

 - Go
 - Redis
 - Kubernetes

Consumes Events:

 - SubscriptionActivated
 - SubscriptionExpired

Publishes Events:

 - PlaybackStarted

Availability Target:
99.99%

Support Tier:
Tier 1

## Event Catalog
### UserCreated
Producer:
Identity Domain

Consumers:

 - Recommendations
 - Analytics
 - Billing

Purpose:
Signals creation of a new user account.

### SubscriptionActivated
Producer:
Billing Domain

Consumers:

 - Playback
 - Advertising
 - Recommendations

Purpose:
Signals activation of a user entitlement.

### PlaybackCompleted
Producer:
Playback Domain

Consumers:

 - Analytics
 - Recommendations
 - Advertising

Purpose:
Signals successful completion of content playback.

## Dependency Maps
### Playback Domain
```
Playback API
|
├── Identity API
├── Entitlement Service
├── DRM Service
└── CDN Gateway
```

### Recommendation Domain
```
Recommendation Engine
|
├── Feature Store
├── Catalog API
├── Analytics Pipeline
└── User Profile Service
```

## Engineering Standards
### API Standards
Requirements:

 - OpenAPI specifications
 - Semantic versioning
 - Contract testing
 - Backward compatibility

### Event Standards
Requirements:

 - Event ownership
 - Event schemas
 - Versioned contracts
 - Consumer documentation

### Observability Standards
Requirements:

 - Structured logging
 - Distributed tracing
 - Service metrics
 - Service Level Objective (SLO) definitions

## New Engineer Navigation Guide
If you are trying to understand:

Authentication
→ Identity Domain

Content Discovery
→ Catalog + Recommendation Domains

Video Playback
→ Playback Domain

Subscription Logic
→ Billing Domain

Advertising
→ Advertising Domain

Operational Infrastructure
→ Platform Engineering Domain

## AI Knowledge Structure
Example Service Record
```yaml
service_id: playback-api

domain: playback

owner_team: playback-engineering

purpose:
Authorise and manage video playback sessions.

dependencies:

 - identity-api
 - drm-service
 - entitlement-service

published_events:

 - PlaybackStarted
 - PlaybackCompleted

criticality:
tier1

runbooks:

 - playback-outage
 - drm-failure

documentation:

 - architecture-narrative
 - onboarding-guide
 - api-reference

status:
production