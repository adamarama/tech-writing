# EmuTV Architecture Narrative
**Version 1.0**
**Audience:** Engineers, Architects, Platform Teams, Product Managers, Technical Leadership
**Document Type:** Architecture Narrative
**Owner:** Hector Adama

**Last Updated:** June 2026

*NOTE: EmuTV is a fictional streaming platform used as an example for this document.**

## 1. Purpose
This document is intended to provide a narrative overview of the EmuTV platform architecture.

Unlike reference documentation, this architecture narrative focuses on:

 - Why systems exist
 - How systems interact
 - The reasoning behind architectural boundaries
 - Common engineering workflows
 - Platform tradeoffs and constraints

The goal is to help engineers and stakeholders understand not only what components exist, but how the platform operates in an integrated system.

## 2. Business Context
EmuTV is a global streaming platform that delivers video content to millions of users across multiple device ecosystems.

Supported experiences include:

 - On-demand video
 - Live streaming events
 - Subscription services
 - Advertising-supported viewing
 - Personalised recommendations

The platform must support:

 - Global scale
 - High availability
 - Low-latency playback
 - Continuous content publishing
 - Rapid feature delivery

These requirements have shaped the architecture into a collection of domain-oriented services connected through APIs and event streams.

## 3. Architectural Principals
The EmuTV architecture is guided by five core principles.

### Principle 1: Domain ownership
Services are organised around business capabilities rather than technical layers.

Examples include:

 - Identity
 - Catalog
 - Playback
 - Billing
 - Advertising

Each domain owns its data, APIs, operational responsibilities, and lifecycle.

### Principle 2: API-first design
Capabilities are exposed through versioned APIs.

Benefits:

 - Clear contracts
 - Independent deployment
 - Easier integration

Tradeoff:

API management introduces governance overhead. 

*Governance overhead* refers to the time and resources spent enforcing the standards and policies that determine how APIs are designed, managed, and secured versus the actual value being delivered. It is the developmental and administrative burdens placed on engineering teams to ensure compliance. Too little governance can lead to sloppy APIs, technical debt, and security risks. Too much governance (or high overhead) slows down releases, causes friction, and stifles developer agility.

### Principle 3: Event-driven communication
Changes in one domain are communicated to other domains through business events.

Examples:

 - UserCreated
 - subscriptionActivated
 - PlaybackStarted
 - ContentPublished

Benefits:

 - Reduced coupling
 - Better scalability

Tradeoff:

Eventual consistency between platforms.

*Eventual Consistency* refers to a brief delay that may occur after data is modified before the latest value is reflected everywhere. This allows APIs to be highly scalable and available, but consumers may see slightly differing states across different systems in the short term.

### Principle 4: Cloud-native operations
All production services run within Kubernetes clusters.

Benefits:

 - Standardised deployments
 - Improved scalability
 - Platform consistency

Tradeoff:
Additional operational complexity.

### Principle 5: Global availability
Customer-facing services must remain available despite regional failure.

This requires:

 - Multi-region deployment
 - Automated failover
 - Global-content distribution

## 4. Platform Overview
The platform is divided into nine major domains.

 1. Identity
 2. Customer Experience
 3. Catalog
 4. Recommendations
 5. Playback
 6. Advertising
 7. Billing
 8. Analytics
 9. Platform Engineering

Each domain owns a specific business capability while collaborating through defined contracts.

## 5. User Journey Narrative
The most useful way to understand the platform is to follow a user interaction.

Scenario:

A user opens the application and watches a movie.

### Step 1: Application startup
The user launches EmuTV.

The client application performs:

 - Session validation
 - Configuration retrieval
 - Feature flag retrieval

Primary systems involved:

 - Identity API
 - Configuration Service

Goal:

Establish user context before rendering content.

### Step 2: Authentication
The Identity Domain validates the user's access token.

Successful validation returns:

 - User identifier
 - Entitlements
 - Subscription status

The client now has an authenticated session.

### Step 3: Homepage generation
The application requests personalised content.

The recommendation Domain retrieves:

 - Viewing history
 - User preferences
 - Engagement signals

Recommendations are generated and enriched with metadata from the Catalog Domain.

The homepage is assembled from multiple service reponses.

### Step 4: Content selection
The user selects a movie.

The Catalog Domain returns:

 - Title metadata
 - Artwork
 - Runtime
 - Ratings
 - Available formats

The Playback Domain is not yet involved.

At this stage, the experience remains informational.

### Step 5: Playback authorisation
The user presses Play.

The Playback Domain performs:

 - Authentication verification
 - Entitlement validation
 - Geographic restrictions checks
 - DRM policy violation

If authorisation succeeds, a playback session is created.

### Step 6: Stream delivery
Playback infrastructure generates:

 - Manifest files
 - Playback tokens
 - CDN routing information

Video content is delivered from the nearest edge location.

The majority of streaming traffic never reaches core application services.

This significantly reduces infrastructure load.

### Step 7: Event generation
Playback generates business events including:

 - PlaybackStarted
 - PlaybackPaused
 - PlaybackCompleted

Events are published to the platform event bus.

### Step 8: Analytics processing
Analytics systems consume playback events.

Data is used for:

 - Reporting
 - Recommendations
 - Advertising
 - Product insights

The same event supports multiple business capabilities.

## 6. Identity Domain
### Purpose
The Identity Domain manages authentication, authorization, and user profiles.

Without the Identity Domain, no other user-facing capability can operate securely.

### Responsibilities
The Identity Domain owns:

 - User accounts
 - Authentication
 - Session management
 - Access tokens
 - User profiles

### Key components
#### Authentication Service
Validates credentials.

#### Token Service
Issues and validates access tokens.

#### Profile Service
Stores user profile information.

#### Session Service
Tracks active user sessions.

### Architectural tradeoffs
#### JSON Web Tokens
A **JSON Web Token** or **JWT** is a compact, stateless, and self-contained standard (RFC 7519) used to securely transmit information between parties as a JSON object.

Benefits:

 - Reduced latency
 - Stateless validation

Costs:

 - Revocation complexity.

Because of the stateless nature of JWTs, they are considered valid until their embedded time expires, making immediate revocation more challenging. Additional complexity must be added to ensure tokens are immediately invalidated regardless of their original expiration time when used for security events such as user logouts or password changes.

EmuTV accepts this tradeoff because scalability requirements outweigh the revocation challenges.

## 7. Catalog Domain
### Purpose
The Catalog Domain serves as the authoritative source of content metadata.

The Catalog Domain does not deliver video, it only contains the data that describes it.

### Responsibilities

 - Metadata management
 - Content discovery
 - Search indexing
 - Content relationships

### Key components
#### Catalog API
Primary metadate interface.

#### Search Index
Optimised content retrieval.

#### Metadata Store
Source of truth.

### Architectural tradeoffs
Search infrastructure intentionally duplicates catalog data to bypass the heavy compute costs of querying an external data catalog every time a user executes a search.

This increases storage costs but dramatically improves query performance.

## 8. Recommendation Domain
### Purpose
Help users discover relevant content.

Recommendation quality directly affects engagement.

### Inputs
Recommendations use:

 - Viewing history
 - Search behaviour
 - Device information
 - Regional preferences

### Components
#### Feature Store
Maintains machine-learning features.

#### Candidate Generator
Produces recommendation candidates.

#### Ranking Engine
Orders candidate results.

### Architectural tradeoffs
#### Batch recommendations
Benefits:

 - Lower cost
 - Predictable performance

Drawbacks:

 - Slower adaptation

#### Real-time recommendations
Benefits:

 - Higher personalisation

Drawbacks:

 - Greater operational complexity

The current architecture uses a hybrid approach.

## 9. Playback Domain
### Purpose
Deliver video content securely and reliably.

 Playback is the highest-scale domain in the platform.

### Responsibilities

 - Playback authorisation
 - DRM integration
 - Stream generation
 - CDN coordination

### Components
#### Playback APi
Creates playback sessions.

#### Manifest Service
Generates streaming manifests.

#### DRM Service
Protects licensed content.

#### CDN Gateway
Routes playback requests.

### Architectural tradeoffs
Keeping video traffic at the CDN edge reduces application load but introduces cache invalidation complexity because you must ensure that globally distributed nodes always serve the correct, updated media files rather than outdated content.

## 10. Advertising Domain
### Purpose
Serve advertisements during ad-supported viewing experiences.

### Responsibilities

 - Ad selection
 - Campaign targeting
 - Impression tracking
 - Revenue attribution

### Components
#### Ad Decision Engine
Determines which advertisement should be shown.

#### Campaign Service
Manages active campaigns.

#### Measurement Service
Tracks outcomes.

### Architectural tradeoffs
Advertising relevance improves revenue but increases privacy and governance requirements.

## 11. Billing Domain
### Purpose
Manage subscription lifecycle events.

### Responsibilities

 - Payments
 - Plan management
 - Renewals
 - Entitlements

### Key events

 - SubscriptionCreated
 - SubscriptionRenewed
 - SubscriptionExpired

### Architectural tradeoffs
Billing remains highly centralised.

While this creates a dependency, consistency requirements outweigh the benefits of decentralisation.

## 12. Analytics Domain
### Purpose
Transform platform events into actionable business insights.

### Responsibilities

 - Event collection
 - Data processing
 - Reporting
 - Experiment analysis

### Data flow
Event producers
→ Event Bus
→ Stream Processing
→ Data Warehouse
→ Reporting Systems

### Architectural tradeoffs
Analytics prioritises completeness over immediacy.

Most reporting is eventually consistent, meaning a brief delay may occur after data is modified before the latest value is reflected everywhere.

## 13. Platform Engineering Domain
### Purpose
Provide shared infrastructure and engineering capabilities.

Platform Engineering enables teams to focus on business functionality.

### Responsibilities

 - Kubernetes
 - CI/CD
 - Service templates
 - Observability
 - Secrets management

### Key principle
Platform teams provide paved roads rather than enforcing rigid frameworks.

This improves adoption while preserving team autonomy.

## 14. Failure Scenarios
### Recommendation Service failure
Expected behaviour:

 - Homepage remains available
 - Fallback content displayed

Business impact:

Reduced personalisation.

User impact:

Minimal.

### Catalog Service failure
Expected behaviour:

 - Existing playback continues
 - New content discovery degraded

Business impact:

Moderate.

### Playback Service failure
Expected behaviour:

 - Playback unavailable

Business impact:

Critical

This domain receives the highest reliability investment.

## 15. Architecture Decision Records
### ADR-001
Decision:

Adopt event-driven integration. *Event-driven integration* replaces rigid, point-to-point API calls with asynchronous messaging. 

Reason:

Reduce service coupling by eliminating direct dependencies.

Result:

Improved scalability through non-blocking parallel processing and increases resilience by isolating component failures.

### ADR-002
Decision:

Use Content Delivery Network (CDN)-first video delivery by intercepting viewer requests at global edge servers rather than routing them to the origin server. 
Reason:

Global performance requirements.

Result:

Significant reduction in backend load. Instead of processing continuous data streams, the backend is limited to a one-time upload of media chunks, saving massive amounts of compute and bandwidth.

### ADR-003
Decision:

Use Kubernetes as the deployment platform.

Reason:

Standardised operations and scaling.

Result:

Improved platform consistency.

## 16. Engineering Onboarding Guide
New engineers should understand the platform in the following order:

Week 1

 - Platform Overview
 - Domain Boundaries
 - Engineering Standards

Week 2

 - Identity
 - Catalog
 - Playback

Week 3

 - Event Architecture
 - Analytics
 - Platform Tooling

Week 4

 - Team-Specific Systems
 - Production Operations

Engineers should focus first on understanding domain relationships before learning implementation details.

## 17. Key Takeaways
EmuTV is a network of business capabilities connected through APIs, events, and shared operational practices. It is not a collection of isolated services.

The architecture emphasises:

 - Domain ownership
 - Event-driven implementation
 - Platform standardisation
 - Global scalability
 - Discoverability of knowledge

Engineers who understand the domain boundaries and information flow can navigate the platform more effectively than engineers who focus solely on individual service implementations.

Understanding the relationships between systems is therefore as important as understanding the systems themselves.