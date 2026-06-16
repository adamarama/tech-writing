# EmuTV New Engineer Onboarding Guide

**Version 1.0**
**Audience:** New Engineers

**Owner:** Hector Adama

**Last Updated:** June 2026

*NOTE: EmuTV is a fictional streaming platform used as an example for this document.*

## Welcome to EmuTV
Welcome to EmuTV! We're excited to have you joining the team. This guide is designed to help new engineers build a working mental model of the platform. For now, you can ignore specific implementation details. The goal is to answer four main questions:

 1. What does EmuTV do?
 2. How is the platform organised?
 3. How do systems communicate?
 4. Where should I go next?

By the end of this guide, you should understand how the major domains fit together and how users move through the platform.

## What is EmuTV?
EmuTV is a global video streaming platform.

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
One of the most common onboarding mistakes is attempting to learn individual services or jumping directly into reading source code before anything else. EmuTV contains dozens of services and thousands of lines of code, so to make sense of it all, it is important to first examine the big picture. Instead of services, we will begin with domains. 

Domains represent business capabilities.

Each domain owns:

 - Data
 - APIs
 - Events
 - Operational responsibility

The platform is organised into nine major domains:

 - Identity
 - Customer Experience
 - Catalog
 - Recommendations
 - Playback
 - Advertising
 - Billing
 - Analytics
 - Platform Engineering

Understanding these domains will help you to understand where services belong who owns them.

## Follow the User Journey
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

## Understanding Event-Driven Architecture
One of the most important features of the platform is event-driven communication.

Domains publish business events to communicate instead of using direct synchronous calls. This reduces coupling between systems.

Example:
```
PlaybackCompleted
|
├── Analytics
├── Recommendations
└── Advertising
```

Benefits:

 - Better scalability
 - Improved resilience
 - Faster feature development

Tradeoff:

 - Systems become eventually consistent, meaning new or updated information may take a short period of time to propagate throughout the platform.

## Understanding Domain Ownership
When you encounter a new service, your first question should be:
> Which domain owns this?

Understanding domain ownership will help you find the appropriate documentation, teams, and support resources.

Examples:
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
Not all systems have equal impacts on the business. The following domains are considered Tier 1 business capabilities, meaning any failure or downtime will result in critical impacts on the business and a poor experience for users.

### Identity
Without the Identity Domain, users cannot authenticate.

### Catalog
Without the Catalog Domain, users cannot discover content.

### Playback
Without the Playback Domain, users cannot watch content.

Playback receives the highest reliability investment because it directly affects revenue generations.

## Platform Engineering
Platform Engineering exists to make application teams more productive. The team owns shared capabilities including:

 - Kubernetes
 - CI/CD
 - Service templates
 - Observability
 - Secrets management

## Common Engineering Standards
All services are expected to follow shared engineering standards. These standards improve consistency across the platform.

API Standards:

 - OpenAPI specifications
 - Semantic versioning
 - Contract testing
 - Backward compatibility

Event Standards:

 - Event ownership
 - Event schemas
 - Versioned contracts
 - Consumer documentation

Observability Standards:

 - Structured logging
 - Distributed tracing
 - Service metrics
 - Service Level Objective (SLO) definitions

## Where to Find Information
The Engineering Portal should be your primary starting point.

Use it to answer:

 - What does this service do?
 - Who owns it?
 - What events does it publish?
 - What systems depend on it?
 - What documentation exists?

The Engineering Portal contains:

 - Service Catalog
 - Domain Directory
 - Event Catalog
 - Dependency Maps
 - Architecture Documentation

## Suggested Learning Path
If you are new to the organisation, we recommend you follow this path:

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

This progression follows how the platform is organised and will help you build understanding efficiently.

## Key Takeaways
Remember these principles:

 1. Learn domains before services.
 2. Follow the user journey.
 3. Understand event-driven communication.
 4. Focus on ownership and dependencies.
 5. Use the Engineering Portal as your source of truth.

EmuTV is a platform of interconnected business capabilities. 

Engineers become effective more quickly when they understand how systems relate to one another rather than focusing exclusively on individual implementations. The most valuable knowledge in a distributed system is often understanding the connections between components rather than the compontents themselves.