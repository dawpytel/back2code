# Technical Learning Context and Plan

## Purpose of this app

This “learning app” is a personal sandbox and teaching simulator where I can add new modules (tasks, simulations, small project) over time. ach module demonstrates one or more of learning areas related to software engnieering and system design. It is about learning in practice to design, code, monitor, and break real systems.

---

## Profile

- 13+ years of engineering leadership experience (Manager, Director, VP)
- Former software engineer with Java (backend) background; no front-end experience
- Goal: stay up to date with technology and system-level understanding
- Objective: regain hands-on technical depth to understand how modern software systems work at code and architecture level

---

## Learning Areas

1. **System Design** – scalable, resilient distributed systems, APIs, service boundaries, load balancing, caching, data partitioning
2. **Reliability, Scalability, and Availability** – fault tolerance, redundancy, graceful degradation, horizontal scaling, monitoring, alerting, incident response
3. **Data and Storage** – relational vs NoSQL, indexing, transactions, data modeling, caching layers (Redis, Memcached)
4. **Security and Compliance** – authentication, authorization, encryption, data privacy, secure SDLC practices
5. **Observability** – metrics, logs, monitoring dashboards
6. **API & Integration Design** – REST, GraphQL, event-driven systems, message queues, versioning

---

## Learning Approach

### Core Concept

Learn through **active, practical construction** by building a modular “learning platform” — a small distributed system that evolves over time and demonstrates key software engineering concepts.

The learning method will be based on the following approach:

1. **Theory First:** Read core concepts, understand the "why"
2. **Design Phase:** Create architecture diagrams, document design decisions
3. **Implementation:** Build the solution iteratively
4. **Break & Observe:** Introduce failures, measure behavior
5. **Reflection:** Write postmortem, document trade-offs

### Progressive Integration vs Independent Modules

Should each stage build incrementally on the previous one, or should modules be relatively independent?
This project adopts a **hybrid approach**:

- **Core Services (Stages 1-2):** Build sequentially as foundation
- **Cross-cutting Concerns (Stages 4-5):** Integrate into existing services
- **Advanced Features (Stages 6-7):** Can be explored independently or integrated

Document dependencies explicitly in each module. This allows to:

- Skip or reorder modules if interests change
- Rebuild components if better approaches are discovered
- Focus on specific areas without breaking the entire system

### Project Theme

**“Back2Code”** – a backend system offering APIs for user-generated data (e.g., notes, messages) that includes:

- Authentication and authorization
- Multiple storage layers
- Event-driven communication
- Observability and reliability tooling

---

## Technology Stack

- **Backend:** Node.js (NestJS)
- **Database:** PostgreSQL + Redis
- **Messaging:** RabbitMQ or Kafka
- **Containerization:** Docker + Docker Compose (extend later to Kubernetes)
- **Monitoring:** Prometheus + Grafana
- **Auth:** JWT + OAuth2 (Keycloak or Auth0 mock)

---

## Project Modules and Learning Goals

1. System Design - REST API, DB persistence, Dockerization, API design, service boundaries
2. Data & Storage - PostgreSQL + MongoDB, Redis caching Data modeling, indexing, cache invalidation
3. Reliability & Scalability - Fault tolerance, graceful degradation, scaling
4. Security - JWT login, password encryption, audit logs, authN/authZ, data privacy
5. Observability - Prometheus + Grafana dashboards, metrics, logging, alerting
6. API & Integration - GraphQL endpoint, event-driven notifications, REST vs GraphQL, queues, async systems, API versioning strategies, Rate limiting and throttling, Circuit breaker patterns
7. Reliability Ops - Chaos simulation (kill containers), recovery practice, incident response, redundancy, failover

Scope will be adjusted during planning phase for each module.

---

## Learning Methodology

- **Essential theory:** understanding the issue, learning about recommended strategies and best practices
- **Active Constructionism:** learn by building and explaining
- **Progressive Complexity:** expand modules gradually
- **Documentation-Driven Development:** write short design docs/diagrams before coding
- **Break to Learn:** simulate outages and measure observability
- **Postmortem Practice:** summarize learnings and design trade-offs after each module

---

## Repository Structure

/back2code
├── /core-api # REST and Graphql service
├── /auth-service # Authentication + authorization
├── /storage-service # DB + cache
├── /event-bus # Messaging system
├── /monitoring # Prometheus + Grafana setup
├── /ai # md files - the result of working with AI or input data for working with AI
└── docker-compose.yml

Each module includes:

- Learning materials: articles, descriptions, diagrams, best practices
- Source code
- Simulation or testing scripts
- Observability dashboards
- Learning documentation

---

## Recommended Resources

- **System Design:** “System Design Interview” (Alex Xu); GitHub: `donnemartin/system-design-primer`
- **Reliability:** Google SRE Book; “Building Microservices” (Sam Newman)
- **Data:** “Designing Data-Intensive Applications” (Martin Kleppmann)
- **Security:** OWASP Top 10; “Web Security for Developers”
- **Observability:** “Distributed Systems Observability” (Cindy Sridharan)
- **APIs:** “API Design Patterns” (JJ Geewax); Postman & GraphQL tutorials

---

## Expansion Opportunities

- Add a lightweight **React/Next.js dashboard** for visualization
- Deploy to **cloud (AWS/GCP/Azure)** using Terraform/Pulumi
- Convert into a **self-paced engineering bootcamp** for mentoring or team training

---

## Summary

This plan defines a practical, project-based path to mastering modern software system principles.  
The “Back2Code” acts as a sandbox to:

- Build and connect distributed components
- Learn reliability, scalability, and observability by experimentation
- Regain deep technical understanding through active creation, documentation, and iteration
