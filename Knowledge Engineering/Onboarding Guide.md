# EmuTV Onboarding Guide
**Version 1.0**
**Audience:** New Developers

**Owner:** Hector Adama

**Last Updated:** June 2026

*NOTE: EmuTV is a fictional streaming platform used as an example for this document. As this is a sample, it is not exhaustive and has been kept condensed for length, so some sections may appear to be missing steps.*

## Welcome to EmuTV
Welcome to EmuTV! We are excited to have you joining the team.

This guide is designed to help new developers build a working mental model of the platform. For now, you can ignore specific implementation details. The goal is to answer four main questions:

 1. What does EmuTV do?
 2. How is the platform organised?
 3. How do systems communicate?
 4. Where should I go next?

By the end of this guide, you should understand how the major domains fit together and how users move through the platform.

## What is EmuTV?
EmuTV is a global streaming platform that delivers video content to millions of users across multiple device ecosystems.

The platform supports:

 - On-demand streaming
 - Live events
 - Subscription services
 - Advertising-supported viewing
 - Personalised recommendations

Users can access EmuTV through:

 - Web applications
 - Mobile applications
 - Smart TVs
 - Gaming consoles

At a high level, EmuTV exists to accomplish one goal:

> Deliver the right content to the right user at the right time.

Everything within the platform exists to support that goal.

## Start with Domains, not Services
One of the most common onboarding mistakes is attempting to learn individual services or jumping directly into reading source code before anything else. The EmuTV platform contains dozens of services and thousands of lines of code, so to make sense of it all, it is important to first examine the big picture. Instead of services, we will begin with domains. 

Domains represent business capabilities. Each domain owns:

 - Data
 - APIs
 - Events
 - Operational responsibility

The platform is organised into nine major domains that handle a specific area of the business:

 - Identity
 - Customer Experience
 - Catalog
 - Recommendations
 - Playback
 - Advertising
 - Billing
 - Analytics
 - Platform Engineering

Understanding these domains will help you to understand how services and events interact and who owns them.

## Follow the User Journey
The most useful way to understand the platform is to follow a user interaction.

**Scenario:**
> A user opens the application and watches a movie.

### Step 1: Application startup
 The user launches EmuTV. 

The client application performs:

 - Session validation
 - Configuration retrieval
 - Feature flag retrieval

Primary systems involved:

 - Identity API
 - Configuration Service

**Goal:**
Establish user context before rendering content.

<hr>

### Step 2: Authentication
The Identity Domain validates the user's access token.

Successful validation returns:

 - User identifier
 - Entitlements
 - Subscription status

The client now has an authenticated session.

<hr>

### Step 3: Homepage generation
The application requests personalised content.

The recommendation Domain retrieves:

 - Viewing history
 - User preferences
 - Engagement signals

Recommendations are generated and enriched with metadata from the Catalog Domain.

The homepage is assembled from multiple service reponses.

<hr>

### Step 4: Content selection
The user selects a movie.

The Catalog Domain returns:

 - Title metadata
 - Artwork
 - Runtime
 - Ratings
 - Available formats

The Playback Domain is not yet involved. At this stage, the experience remains informational.

<hr>

### Step 5: Playback authorisation
The user presses Play.

The Playback Domain performs:

 - Authentication verification
 - Entitlement validation
 - Geographic restrictions checks
 - DRM policy violation

If authorisation succeeds, a playback session is created.

<hr>

### Step 6: Stream delivery
Playback infrastructure generates:

 - Manifest files
 - Playback tokens
 - CDN routing information

Video content is delivered from the nearest edge location, meaning the majority of streaming traffic never reaches core application services and significantly reduces infrastructure load.

<hr>

### Step 7: Event generation
Playback generates business events including:

 - PlaybackStarted
 - PlaybackPaused
 - PlaybackCompleted

Events are published to the platform event bus.

<hr>

### Step 8: Analytics processing
Analytics systems consume playback events.

Data is used for:

 - Reporting
 - Recommendations
 - Advertising
 - Product insights

The same event supports multiple business capabilities.

## Understanding Event-Driven Architecture
One of the most important features of the platform is event-driven communication. Changes in one domain are communicated to other domains through events. System components communicate asynchronously by producing, detecting, and responding to *events*, which are defined as meaningful changes in state.

This reduces coupling and improves scalability by minimising direct dependencies between different system components, services, or modules. By forcing domains to interact only through well-defined, interfaces or asynchronous events, it prevents changes in one area from cascading into unintended failures elsewhere.

**Example:**
```
PlaybackCompleted
|
├── Analytics
├── Recommendations
└── Advertising
```

**Benefits:**

 - Better scalability
 - Improved resilience
 - Faster feature development

**Tradeoff:**

 - Systems become eventually consistent, meaning new or updated information may take a short period of time to propagate throughout the platform.

## Understanding Domain Ownership
When you encounter a new service, your first question should be:
> Which domain owns this?

Understanding domain ownership will help you find the appropriate documentation, teams, and support resources.

**Examples:**
|Capability|Domain|
|--|--|
|Authentication|Identity|
|Search|Catalog|
|Recommendations|Recommendations|
|Video Delivery|Playback|
|Subscription Management|Billing|
|Ad Serving|Advertising|
|Reporting|Analytics|

## Critical Systems
Not all systems have equal impacts on the business. The following domains are considered **Tier 1** business capabilities, meaning any failure or downtime will result in critical impacts on the business and a poor experience for users. 

Tier 1 issues must be escalated and investigated immediately.

### Identity
Without the Identity Domain, users cannot authenticate.

### Catalog
Without the Catalog Domain, users cannot discover content.

### Playback
Without the Playback Domain, users cannot watch content.

Playback receives the highest reliability investment because it directly affects revenue generations.

## Platform Engineering
Platform Engineering exists to make application teams more productive. This team builds and maintains internal tools, automated workflows, and infrastructure that other developers rely on. The primary goal of Platform Engineering is to provide a "paved road" of self-service systems that allow other teams to deply code and manage resources without operational friction. 

 Platform Engineering owns shared capabilities including:

 - Kubernetes
 - CI/CD
 - Service templates
 - Observability
 - Secrets management

## Common Development Standards
All services are expected to follow shared development standards that serve as formal guidelines defining how the EmuTV system is designed, documented, and scaled to ensure consistency, maintainability, and security across development teams. These standards establish a baseline for design, communication, and deployment and improve consistency across the platform.

**API Standards:**

 - OpenAPI specifications
 - Semantic versioning
 - Contract testing
 - Backward compatibility

**Event Standards:**

 - Event ownership
 - Event schemas
 - Versioned contracts
 - Consumer documentation

**Observability Standards:**

 - Structured logging
 - Distributed tracing
 - Service metrics
 - Service Level Objective (SLO) definitions

## Where to Find Information
The Developer Portal should be your primary starting point.

Use it to answer:

 - What does this service do?
 - Who owns it?
 - What events does it publish?
 - What systems depend on it?
 - What documentation exists?

The Developer Portal contains:

 - Service Catalog
 - Domain Directory
 - Event Catalog
 - Dependency Maps
 - Architecture Documentation

## Suggested Learning Path
We recommend you follow this learning path for the best possible experience:

**Week 1**

 - Platform Overview
 - Domain Boundaries
 - Development Standards

**Week 2**

 - Identity
 - Catalog
 - Playback

**Week 3**

 - Event Architecture
 - Analytics
 - Platform Tooling

**Week 4**

 - Team-Specific Systems
 - Production Operations

This progression follows how the platform is organised and will help you build understanding efficiently.

## Key Takeaways
Remember these principles:

 1. Learn domains before services.
 2. Follow the user journey.
 3. Understand event-driven communication.
 4. Focus on ownership and dependencies.
 5. Use the Developer Portal as your source of truth.

EmuTV is a platform of interconnected business capabilities. Developers become effective more quickly when they understand how systems relate to one another rather than focusing exclusively on individual implementations. The most valuable knowledge in a distributed system is often understanding the connections between components rather than the compontents themselves.