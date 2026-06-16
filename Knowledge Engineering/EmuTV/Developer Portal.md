# EmuTV Developer Portal
**Version 1.0**
**Owner:** Hector Adama

**Last Updated:** June 2026

*NOTE: EmuTV is a fictional streaming platform used as an example for this document. As this is a sample, it is not exhaustive and has been kept condensed for length, so some sections may appear to be missing steps.*

## Portal Home
### Platform overview
EmuTV consists of nine business domains and more than fifty independently deployable services. The Developer Portal helps developers answer questions such as:

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
### Purpose
This section defines the different domains within EmuTV. Each domain owns its data, APIs, operational responsibilities, and lifecycle.

|Criticality|Definition|
|--|--|
|Tier 1|Critical business impact|
|Tier 2|Moderate business impact|
|Tier 3|Minimal business impact| 

### Identity Domain
**Purpose:**
Authentication, authorisation, and customer identity management.

**Owned by:**
Identity Platform

**Key Services:**

 - Identity API
 - Session Service
 - Profile Service
 - Token Service

**Critical Events:**

 - UserCreated
 - UserUpdated
 - UserDeleted

**Dependencies:**

 - Billing
 - Recommendations
 - Playback

**Business Criticality:**
Tier 1

### Catalog Domain
**Purpose:**
Content metadata and discovery.

**Owned by:**
Content Platform

**Key Services:**

 - Catalog API
 - Metadata Service
 - Search Service

**Critical Events:**

 - ContentCreated
 - ContentPublished
 - ContentArchived

**Dependencies:**

 - Recommendations
 - Playback
 - Search

**Business Criticality:**
Tier 1

### Playback Domain
**Purpose:**
Video delivery and streaming authorisation.

**Owned by:**
Playback Engineering

**Key Services:**

 - Playback API
 - Manifest Service
 - DRM Service
 - CDN Service

**Critical Events:**

 - PlaybackStarted
 - PlaybackPaused
 - PlaybackCompleted

**Business Criticality:**
Tier 1

### Recommendation Domain
**Purpose:**
Personalised content discovery.

**Owned by:**
Discovery Engineering

**Key Services:**

 - Recommendations Engine
 - Feature Service
 - Ranking Service

**Critical Events:**

 - RecommendationGenerated

**Business Criticality:**
Tier 2

## Service Catalog
### Purpose
The Service Catalog is a reference that clearly defines all internal and external services, including their purpose, owners,  and technology stack.

|Support Tier|Definition|
|--|--|
|Tier 1|Critical issues requiring immediate action|
|Tier 2|Issues should be considered Medium to High priority|
|Tier 3|Issues should be considered Normal to Medium priority| 

### Identity API
**Service Type:**
User-facing API

**Purpose:**
Authenticate users and issue access tokens.

**Owned by:**
Identity Platform Team

**Repository:**
`identity-api`

**Technology Stack:**

 - TypeScript
 - Node.js
 - PostgreSQL
 - Kubernetes

**Consumes Events:**

 - SubscriptionExpired

**Publishes Events:**

 - UserAuthenticated

**Dependencies:**

 - Profile Service
 - Session Service

**Uptime Availability Target:**
99.95%

**Support Tier:**
Tier 1

### Catalog API
**Service Type:**
User-facing API

**Purpose:**
Provide content metadata.

**Owned by:**
Content Platform Team

**Repository:**
`content-api`

**Technology Stack:**

 - TypeScript
 - PostgreSQL
 - Elasticsearch

**Consumes Events:**

 - ContentPublished

**Publishes Events:**

 - MetadataUpdated

**Uptime Availability Target:**
99.95%

**Support Tier:**
Tier 1

### Recommendation Engine
**Service Type:**
Internal Service

**Purpose:**
Generate personalised recommendations.

**Owned by:**
Discovery Engineering

**Technology Stack:**

 - Python
 - Feature Store
 - Redis

**Consumes Events:**

 - PlaybackCompleted
 - SearchPerformed

**Publishes Events:**

 - RecommendationsGenerated

**Uptime Availability Target:**
99.95%

**Support Tier:**
Tier 2

### Playback API
**Service Type:**
User-facing API

**Purpose:**
Authorise streaming sessions.

**Owned by:**
Playback Engineering

**Technology Stack:**

 - Go
 - Redis
 - Kubernetes

**Consumes Events:**

 - SubscriptionActivated
 - SubscriptionExpired

**Publishes Events:**

 - PlaybackStarted

**Uptime Availability Target:**
99.99%

**Support Tier:**
Tier 1

`NOTE: Due to critical impacts on business and revenue, issues related to the Playback API always take highest priority over other issues.`

## Event Catalog
### Purpose
The Event Catalog is a reference that clearly defines all events, including what domain owns and produces them, what domains consume them, and their purpose.

### UserCreated
**Producer:**
Identity Domain

**Consumers:**

 - Recommendations
 - Analytics
 - Billing

**Purpose:**
Signals creation of a new user account.

### SubscriptionActivated
**Producer:**
Billing Domain

**Consumers:**

 - Playback
 - Advertising
 - Recommendations

**Purpose:**
Signals activation of a user entitlement.

### PlaybackCompleted
**Producer:**
Playback Domain

**Consumers:**

 - Analytics
 - Recommendations
 - Advertising

**Purpose:**
Signals successful completion of content playback.

## Dependency Maps
### Purpose
Dependency maps provide a visual representation that illustrates the different relationships between domains, services,  and other system components, making it easier to understand how the system architecture fits together at a high level.

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

## Development Standards
### Purpose
This section defines the formal guidelines defining how the EmuTV system is designed, documented, and scaled to ensure consistency, maintainability, and security standards across development teams. These standards establish a baseline for design, communication, and deployment.

### API Standards
**Requirements:**

 - OpenAPI specifications
 - Semantic versioning
 - Contract testing
 - Backward compatibility

### Event Standards
**Requirements:**

 - Event ownership
 - Event schemas
 - Versioned contracts
 - Consumer documentation

### Observability Standards
**Requirements:**

 - Structured logging
 - Distributed tracing
 - Service metrics
 - Service Level Objective (SLO) definitions