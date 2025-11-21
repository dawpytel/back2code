# Module 5: Observability - Metrics, Logging, Monitoring & Alerting

## 1. Module Overview

### Learning Objectives

By completing this module, you will:

- **Understand** the three pillars of observability: metrics, logs, and traces
- **Design** comprehensive monitoring strategy with SLIs, SLOs, and SLAs
- **Implement** Prometheus metrics collection across distributed services
- **Apply** Grafana dashboards for real-time system visualization
- **Evaluate** centralized logging strategies (ELK Stack or Loki)
- **Master** distributed tracing with Jaeger for request flow analysis
- **Implement** intelligent alerting with on-call best practices
- **Practice** incident response and postmortem procedures

### Prerequisites

- **Module 1 completed:** REST API with basic logging
- **Module 2 completed:** Multi-database with cache metrics
- **Module 3 completed:** Horizontal scaling with multiple instances
- **Module 4 completed:** Authentication with audit logging
- **Familiarity with:**
  - Structured logging concepts
  - Time-series data
  - Query languages (PromQL basics)
  - Alert fatigue challenges

### Estimated Time Commitment

- **Theory & Design:** 16-20 hours (4-5 days)
- **Implementation:** 32-40 hours (7-10 days of focused work)
- **Break & Observe:** 10-14 hours (2-3 days)
- **Documentation & Reflection:** 5-7 hours
- **Total:** 63-81 hours (approximately 3-5 weeks at moderate pace)

### Key Deliverables

1. **Design Artifacts:**
   - Observability architecture diagram
   - Metrics taxonomy (RED, USE, four golden signals)
   - SLI/SLO definitions for critical services
   - Alert runbook templates
   - On-call rotation policy
   - Incident severity classification

2. **Implementation:**
   - Prometheus server with service discovery
   - Custom metrics exporters for application
   - Grafana dashboards (system, application, business metrics)
   - Centralized logging with Loki or ELK
   - Jaeger distributed tracing integration
   - AlertManager configuration with routing
   - PagerDuty/Slack integration
   - Log aggregation queries
   - Trace sampling strategy

3. **Documentation:**
   - Metrics catalog (what each metric measures)
   - Dashboard usage guide
   - Alert response procedures (runbooks)
   - Incident response playbook
   - Postmortem with observability insights

---

## 2. Learning Area Coverage Analysis

### PRIMARY: Observability

**Concepts Covered:**

- **Metrics:**
  - Counter, Gauge, Histogram, Summary metric types
  - Prometheus data model and PromQL
  - RED metrics (Rate, Errors, Duration)
  - USE metrics (Utilization, Saturation, Errors)
  - Four Golden Signals (Latency, Traffic, Errors, Saturation)
  - Custom business metrics
  - Cardinality management
  - Metric aggregation and federation

- **Logging:**
  - Structured logging (JSON format)
  - Log levels and when to use each
  - Centralized log aggregation
  - Log retention and rotation
  - Log correlation with traces
  - Query languages (LogQL, Lucene)
  - Log parsing and indexing

- **Tracing:**
  - Distributed tracing concepts
  - Span and trace structure
  - OpenTelemetry standards
  - Context propagation across services
  - Trace sampling strategies
  - Service dependency mapping
  - Latency analysis

- **Alerting:**
  - Alert design principles
  - Alert routing and escalation
  - On-call best practices
  - Runbook automation
  - Alert fatigue prevention
  - SLA/SLO-based alerting

- **SRE Practices:**
  - Service Level Indicators (SLIs)
  - Service Level Objectives (SLOs)
  - Service Level Agreements (SLAs)
  - Error budgets
  - Toil reduction
  - Incident management
  - Postmortem culture

### PRIMARY: Reliability, Scalability, and Availability

**Concepts Covered:**

- Monitoring for reliability
- Capacity planning with metrics
- Proactive vs reactive monitoring
- Mean Time To Detect (MTTD)
- Mean Time To Resolve (MTTR)
- Availability calculations (nine's)
- Error budget tracking

### SECONDARY: System Design

**Concepts Covered:**

- Observability architecture patterns
- Metrics pipeline design
- Log aggregation architecture
- Trace collection topology
- Pull vs push models
- Service mesh observability

### SECONDARY: API & Integration Design

**Concepts Covered:**

- Instrumentation best practices
- Webhook integrations (Slack, PagerDuty)
- Metrics export formats
- OpenTelemetry SDK integration

### SECONDARY: Security and Compliance

**Concepts Covered:**

- Sensitive data redaction in logs
- Access control for dashboards
- Audit trail with logs
- Security monitoring metrics
- Compliance logging requirements

### NOT_COVERED: Data and Storage

**Justification:**
Data storage patterns covered in Modules 1 & 2. This module focuses on observability data (time-series, logs) which has different characteristics than application data.

---

## 3. Theory Foundation

### Core Concepts and Principles

#### Three Pillars of Observability

**1. Metrics (What's happening)**
- Numerical measurements over time
- Low cardinality, highly aggregatable
- Examples: request count, CPU usage, error rate
- Best for: Dashboards, alerts, capacity planning

**2. Logs (What happened)**
- Discrete events with context
- High cardinality, detailed
- Examples: error messages, audit trails, debug info
- Best for: Debugging, audit trails, root cause analysis

**3. Traces (Where is it happening)**
- Request flow across services
- Shows relationships and latency
- Examples: API call chain, database query timing
- Best for: Performance optimization, dependency mapping

**The Synergy:**
- Metrics tell you WHAT is wrong
- Logs tell you WHY it's wrong
- Traces tell you WHERE it's wrong

#### Prometheus Fundamentals

**Data Model:**
```
metric_name{label1="value1", label2="value2"} 42 timestamp
```

**Metric Types:**

1. **Counter:** Monotonically increasing value
   ```
   http_requests_total{method="GET", endpoint="/api/notes"} 1543
   ```
   - Use for: counts of events (requests, errors)
   - Query with: `rate()` or `increase()`

2. **Gauge:** Value that can go up or down
   ```
   memory_usage_bytes 2147483648
   ```
   - Use for: current state (memory, connections)
   - Query directly

3. **Histogram:** Distribution of values in buckets
   ```
   http_request_duration_seconds_bucket{le="0.1"} 95
   http_request_duration_seconds_bucket{le="0.5"} 120
   ```
   - Use for: latency, response sizes
   - Query with: `histogram_quantile()`

4. **Summary:** Pre-calculated quantiles
   - Similar to histogram but calculated client-side
   - Use when histogram buckets unknown upfront

**PromQL Basics:**
```promql
# Request rate (requests per second)
rate(http_requests_total[5m])

# Error rate percentage
rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) * 100

# 95th percentile latency
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

#### Metrics Methodologies

**RED Method (for services):**
- **R**ate: Requests per second
- **E**rrors: Failed requests per second
- **D**uration: Latency distribution (p50, p95, p99)

**USE Method (for resources):**
- **U**tilization: % of resource used (CPU, memory, disk)
- **S**aturation: Work that can't be serviced (queue depth)
- **E**rrors: Error count

**Four Golden Signals (Google SRE):**
1. **Latency:** Time to serve request
2. **Traffic:** Demand on system (requests/sec)
3. **Errors:** Rate of failed requests
4. **Saturation:** How "full" the service is

#### Service Level Objectives

**SLI (Service Level Indicator):**
- Quantitative measure of service level
- Examples:
  - Request latency: 95% of requests < 200ms
  - Availability: 99.9% of requests succeed
  - Throughput: 1000 requests/sec

**SLO (Service Level Objective):**
- Target value for SLI
- Example: "99.9% availability over 30 days"
- Sets expectations internally

**SLA (Service Level Agreement):**
- Contract with consequences
- Example: "99.95% availability or refund"
- Sets expectations externally

**Error Budget:**
```
Error Budget = 100% - SLO
If SLO = 99.9%, Error Budget = 0.1% = 43.2 minutes/month

Remaining Budget = Error Budget - Actual Errors
```

- If budget exhausted → freeze feature releases, focus on reliability
- If budget healthy → can take risks, ship faster

#### Distributed Tracing

**Trace Structure:**
```
Trace ID: abc123
  Span 1: API Gateway (20ms)
    Span 2: Auth Service (5ms)
    Span 3: Notes Service (12ms)
      Span 4: PostgreSQL Query (8ms)
      Span 5: Redis Cache Check (1ms)
```

**Context Propagation:**
```
HTTP Header: X-Trace-Id: abc123
             X-Span-Id: span456
             X-Parent-Span-Id: parent789
```

**Sampling Strategies:**
- **Head-based:** Decide at trace start (e.g., sample 1%)
- **Tail-based:** Decide after trace completes (keep errors, slow requests)
- **Adaptive:** Adjust sample rate based on volume

#### Logging Best Practices

**Structured Logging:**
```json
{
  "timestamp": "2024-01-15T10:30:00Z",
  "level": "error",
  "message": "Failed to fetch note",
  "service": "notes-api",
  "instance": "api-2",
  "trace_id": "abc123",
  "user_id": "user456",
  "note_id": "note789",
  "error": "DatabaseConnectionError",
  "duration_ms": 3000
}
```

**Log Levels:**
- **DEBUG:** Detailed diagnostic info (disabled in production)
- **INFO:** General informational messages
- **WARN:** Warning, but application continues
- **ERROR:** Error that impacts functionality
- **FATAL:** Critical error, application cannot continue

**What to Log:**
✅ Request/response (method, path, status, duration)
✅ Authentication events (login, logout, failures)
✅ Business events (note created, user registered)
✅ Errors with stack traces
✅ External API calls with latency
✅ Database queries (slow queries)

**What NOT to Log:**
❌ Passwords, tokens, API keys
❌ Credit card numbers, SSNs
❌ Full request bodies (may contain sensitive data)
❌ Excessive debug logs in production

#### Alerting Principles

**Good Alerts:**
- **Actionable:** Require human response
- **Urgent:** Require immediate attention
- **Real:** Indicate actual problems
- **Specific:** Clear what's wrong

**Alert Fatigue Prevention:**
- Set meaningful thresholds (not arbitrary)
- Use SLO-based alerts (error budget burn rate)
- Aggregate related alerts
- Implement alert dependencies
- Regular alert review and tuning

**Runbooks:**
Every alert needs a runbook:
```markdown
# Alert: High Error Rate

## Severity: Critical

## Description
Error rate exceeded 5% for 5 minutes

## Impact
Users experiencing failed requests

## Diagnosis
1. Check Grafana dashboard: link
2. Search logs for errors: query
3. Check dependent services: list

## Resolution
1. If database down: promote replica
2. If API instance down: restart
3. If bug: rollback deployment

## Escalation
Contact: @oncall-backend
```

### Recommended Reading Materials

#### Essential Reading (Before Implementation)

1. **Google SRE Book - Selected Chapters** (8-10 hours)
   - Chapter 4: Service Level Objectives
   - Chapter 6: Monitoring Distributed Systems
   - Chapter 10: Practical Alerting
   - **Focus:** SLO/SLI/SLA, monitoring philosophy

2. **"Distributed Systems Observability" by Cindy Sridharan** (4-5 hours)
   - Full book (it's short)
   - **Focus:** Three pillars, instrumentation

3. **Prometheus Documentation** (4-6 hours)
   - https://prometheus.io/docs/introduction/overview/
   - https://prometheus.io/docs/prometheus/latest/querying/basics/
   - **Focus:** Data model, PromQL, best practices

4. **OpenTelemetry Documentation** (3-4 hours)
   - https://opentelemetry.io/docs/concepts/
   - **Focus:** Tracing concepts, instrumentation

#### Supplementary Reading (During Implementation)

5. **Grafana Documentation** (2-3 hours)
   - https://grafana.com/docs/grafana/latest/

6. **Jaeger Documentation** (2 hours)
   - https://www.jaegertracing.io/docs/

7. **"Observability Engineering" by Majors, Fong-Jones, Miranda** (reference)
   - Chapters on instrumentation and debugging

### Key Terminology and Definitions

- **SLI:** Service Level Indicator - quantitative measure
- **SLO:** Service Level Objective - target for SLI
- **SLA:** Service Level Agreement - contract with consequences
- **Error Budget:** Allowed downtime before SLO violation
- **Cardinality:** Number of unique time series
- **PromQL:** Prometheus Query Language
- **Exporter:** Service that exposes metrics in Prometheus format
- **Scrape:** Prometheus pulling metrics from target
- **Alerting Rule:** Condition that triggers alert
- **Runbook:** Step-by-step guide for responding to alert
- **MTTD:** Mean Time To Detect - how long to notice problem
- **MTTR:** Mean Time To Resolve - how long to fix problem
- **Span:** Single unit of work in trace
- **Trace:** Collection of spans representing request flow
- **Context Propagation:** Passing trace IDs across service boundaries
- **Sampling:** Collecting subset of traces (not all)
- **Structured Logging:** Machine-parseable log format (JSON)
- **Log Aggregation:** Collecting logs from multiple sources

### Common Patterns and Anti-Patterns

#### Patterns to Follow

✅ **Instrument Everything:** Metrics, logs, traces from start
✅ **Use Labels Wisely:** Low cardinality (method, status, endpoint)
✅ **RED/USE Methods:** Consistent metric naming
✅ **Structured Logging:** JSON format with correlation IDs
✅ **Trace Critical Paths:** At minimum, user-facing requests
✅ **SLO-Based Alerts:** Alert on SLO budget burn rate
✅ **Runbook for Every Alert:** Document response procedures
✅ **Dashboard Hierarchy:** Overview → service → component
✅ **Monitor Monitoring:** Alert if Prometheus down

#### Anti-Patterns to Avoid

❌ **High Cardinality Labels:** User IDs, trace IDs in labels
❌ **Alert on Everything:** Creates alert fatigue
❌ **No Context:** Logs without request ID or trace ID
❌ **Vanity Metrics:** Metrics that look good but don't help
❌ **Logging Everything:** Fills disk, obscures important logs
❌ **Complex PromQL:** Hard to understand and maintain
❌ **No Retention Policy:** Metrics/logs forever = expensive
❌ **Single Dashboard:** One massive dashboard is unusable

### Best Practices Specific to This Module

**Prometheus:**
1. Use consistent metric naming: `<namespace>_<subsystem>_<name>_<unit>`
2. Keep cardinality low (< 10 label values per label)
3. Use histogram for latency (not gauge)
4. Set appropriate scrape intervals (15s default)
5. Monitor Prometheus itself

**Grafana:**
1. Create dashboard hierarchy (overview → details)
2. Use templates for repeatable panels
3. Set appropriate time ranges
4. Add annotations for deployments
5. Share dashboards as code (JSON)

**Logging:**
1. Always include timestamp, level, message
2. Add correlation ID to every log
3. Log at appropriate levels (don't spam INFO)
4. Redact sensitive data automatically
5. Set retention based on compliance needs

**Tracing:**
1. Trace user-facing endpoints
2. Sample appropriately (1-10% often sufficient)
3. Add meaningful span names and tags
4. Keep traces under 100 spans
5. Use tail-based sampling for errors

**Alerting:**
1. Page only for urgent, actionable issues
2. Use severity levels (critical, warning, info)
3. Include runbook link in alert
4. Set cooldown periods
5. Review and tune alerts regularly

---

## 4. Practical Implementation Plan

### Phase 1: Design (16-20 hours)

#### Architecture Decisions to Make

**1. Metrics Strategy:**
- What metrics to collect? (RED for APIs, USE for resources)
- How to expose metrics? (Pull vs push)
- Metric retention? (15 days default, longer for aggregates)
- Federation strategy? (Single Prometheus or hierarchical)

**2. Logging Strategy:**
- Centralized logging: Loki (simpler) vs ELK (more features)?
- Log retention: How long to keep logs?
- Log volume: What to log, what to omit?
- Log parsing: Structure vs unstructured?

**3. Tracing Strategy:**
- Trace all requests or sample? (Sample 1-10%)
- Sampling strategy: Head-based or tail-based?
- Trace retention: 7 days typical
- What to trace: External APIs, database calls?

**4. Alerting Strategy:**
- Which SLIs to define?
- What SLOs to commit to?
- Alert routing: Who gets paged for what?
- On-call rotation: How many people?

#### Design Artifacts to Create

**1. Observability Architecture Diagram:**

```
┌─────────────────────────────────────────────────────────┐
│                 Grafana (Visualization)                  │
│  ┌──────────────┬──────────────┬──────────────────────┐ │
│  │   Metrics    │     Logs     │       Traces         │ │
│  │  Dashboards  │  Dashboards  │     Dashboards       │ │
│  └──────┬───────┴──────┬───────┴──────┬───────────────┘ │
└─────────┼──────────────┼──────────────┼──────────────────┘
          │              │              │
     ┌────▼─────┐   ┌────▼────┐    ┌───▼──────┐
     │Prometheus│   │  Loki   │    │  Jaeger  │
     │          │   │         │    │          │
     └────▲─────┘   └────▲────┘    └───▲──────┘
          │              │              │
          │  scrape      │  push        │  push
          │              │              │
┌─────────┴──────────────┴──────────────┴─────────────┐
│            API Instances (instrumented)              │
│  ┌────────────────────────────────────────────────┐  │
│  │  - Prometheus client (metrics)                 │  │
│  │  - Winston/Pino logger (structured logs)       │  │
│  │  - OpenTelemetry SDK (traces)                  │  │
│  └────────────────────────────────────────────────┘  │
│                                                       │
│  api-1         api-2         api-3                   │
└───────────────────────────────────────────────────────┘
          │              │              │
          └──────────────┴──────────────┘
                         │
                  ┌──────▼──────┐
                  │   Databases │
                  │   (monitored)│
                  └─────────────┘
```

**2. Metrics Taxonomy:**

**Application Metrics (RED Method):**
```
# Rate
http_requests_total{method, endpoint, status}
http_requests_per_second{method, endpoint}

# Errors
http_request_errors_total{method, endpoint, error_type}
http_error_rate_percent{method, endpoint}

# Duration
http_request_duration_seconds{method, endpoint, quantile}
http_request_duration_seconds_bucket{method, endpoint, le}
```

**System Metrics (USE Method):**
```
# Utilization
process_cpu_percent
process_memory_bytes
nodejs_heap_used_bytes

# Saturation
nodejs_eventloop_lag_seconds
http_connections_active
database_connections_waiting

# Errors
process_crashes_total
nodejs_gc_pause_seconds_total
```

**Business Metrics:**
```
notes_created_total{user_role}
notes_deleted_total{user_role}
user_registrations_total
authentication_attempts_total{success}
```

**3. SLI/SLO Definitions:**

| Service | SLI | Target (SLO) | Error Budget | Alert Threshold |
|---------|-----|--------------|--------------|-----------------|
| Notes API | Availability (success rate) | 99.9% | 43.2 min/month | Budget < 10% remaining |
| Notes API | Latency (p95) | < 200ms | N/A | p95 > 300ms for 5 min |
| Authentication | Availability | 99.95% | 21.6 min/month | Budget < 10% remaining |
| Database | Query latency (p99) | < 100ms | N/A | p99 > 200ms for 5 min |

**4. Alert Severity Matrix:**

| Severity | Response Time | Examples | Notification |
|----------|--------------|----------|--------------|
| **Critical** | Immediate (page) | SLO violation, system down | PagerDuty + Phone |
| **High** | 30 minutes | High error rate, degraded performance | PagerDuty |
| **Medium** | 4 hours | Warning signs, approaching limits | Slack |
| **Low** | Next business day | Info, capacity planning | Email |

**5. Dashboard Hierarchy:**

```
Level 1: Executive Dashboard
  - Overall availability
  - Active users
  - Error budget status
  - Key business metrics

Level 2: Service Dashboards
  - Notes API Dashboard (RED metrics)
  - Auth Service Dashboard
  - Database Dashboard (USE metrics)

Level 3: Component Dashboards
  - Individual instance metrics
  - Circuit breaker states
  - Cache hit rates
  - Detailed traces

Level 4: Debug Dashboards
  - Log search interface
  - Trace details
  - Anomaly detection
```

### Phase 2: Core Implementation (32-40 hours)

#### Step 1: Prometheus Setup (4-6 hours)

**1. Add Prometheus to Docker Compose:**

```yaml
prometheus:
  image: prom/prometheus:latest
  container_name: back2code-prometheus
  ports:
    - "9090:9090"
  volumes:
    - ./monitoring/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    - prometheus_data:/prometheus
  command:
    - '--config.file=/etc/prometheus/prometheus.yml'
    - '--storage.tsdb.path=/prometheus'
    - '--storage.tsdb.retention.time=15d'
    - '--web.console.libraries=/usr/share/prometheus/console_libraries'
    - '--web.console.templates=/usr/share/prometheus/consoles'
  networks:
    - back2code

volumes:
  prometheus_data:
```

**2. Create Prometheus Configuration:**

```yaml
# monitoring/prometheus/prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s
  external_labels:
    cluster: 'back2code-dev'
    environment: 'development'

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']

# Load rules
rule_files:
  - 'alerts/*.yml'

# Scrape configurations
scrape_configs:
  # Prometheus itself
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  # API instances
  - job_name: 'api'
    static_configs:
      - targets:
          - 'api-1:3000'
          - 'api-2:3000'
          - 'api-3:3000'
    metric_relabel_configs:
      - source_labels: [__address__]
        target_label: instance

  # PostgreSQL
  - job_name: 'postgres'
    static_configs:
      - targets: ['postgres-exporter:9187']

  # Redis
  - job_name: 'redis'
    static_configs:
      - targets: ['redis-exporter:9121']

  # Node Exporter (system metrics)
  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
```

**3. Install Prometheus Client in NestJS:**

```bash
npm install prom-client @willsoto/nestjs-prometheus
```

**4. Configure Metrics Module:**

```typescript
// metrics/metrics.module.ts
import { Module } from '@nestjs/common';
import { PrometheusModule } from '@willsoto/nestjs-prometheus';

@Module({
  imports: [
    PrometheusModule.register({
      path: '/metrics',
      defaultMetrics: {
        enabled: true,
        config: {
          prefix: 'back2code_',
        },
      },
    }),
  ],
})
export class MetricsModule {}
```

#### Step 2: Custom Application Metrics (6-8 hours)

**1. Create Metrics Service:**

```typescript
// metrics/metrics.service.ts
import { Injectable } from '@nestjs/common';
import { Counter, Histogram, Gauge, Registry } from 'prom-client';
import { InjectMetric } from '@willsoto/nestjs-prometheus';

@Injectable()
export class MetricsService {
  constructor(
    @InjectMetric('http_requests_total')
    public httpRequestsTotal: Counter<string>,
    
    @InjectMetric('http_request_duration_seconds')
    public httpRequestDuration: Histogram<string>,
    
    @InjectMetric('http_requests_in_progress')
    public httpRequestsInProgress: Gauge<string>,
  ) {}

  recordRequest(method: string, endpoint: string, status: number, duration: number) {
    this.httpRequestsTotal.inc({
      method,
      endpoint,
      status: status.toString(),
    });

    this.httpRequestDuration.observe(
      {
        method,
        endpoint,
      },
      duration / 1000 // Convert to seconds
    );
  }

  startRequest(method: string, endpoint: string) {
    this.httpRequestsInProgress.inc({ method, endpoint });
  }

  endRequest(method: string, endpoint: string) {
    this.httpRequestsInProgress.dec({ method, endpoint });
  }
}
```

**2. Create Metrics Interceptor:**

```typescript
// common/interceptors/metrics.interceptor.ts
import { Injectable, NestInterceptor, ExecutionContext, CallHandler } from '@nestjs/common';
import { Observable } from 'rxjs';
import { tap, finalize } from 'rxjs/operators';
import { MetricsService } from '../metrics/metrics.service';

@Injectable()
export class MetricsInterceptor implements NestInterceptor {
  constructor(private metricsService: MetricsService) {}

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const response = context.switchToHttp().getResponse();

    const method = request.method;
    const endpoint = this.normalizeEndpoint(request.route?.path || request.url);
    const startTime = Date.now();

    this.metricsService.startRequest(method, endpoint);

    return next.handle().pipe(
      tap(() => {
        const duration = Date.now() - startTime;
        const status = response.statusCode;
        this.metricsService.recordRequest(method, endpoint, status, duration);
      }),
      finalize(() => {
        this.metricsService.endRequest(method, endpoint);
      })
    );
  }

  private normalizeEndpoint(path: string): string {
    // Replace IDs with placeholders to reduce cardinality
    return path
      .replace(/\/[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}/g, '/:id')
      .replace(/\/\d+/g, '/:id');
  }
}
```

**3. Register Custom Metrics:**

```typescript
// app.module.ts
import { makeCounterProvider, makeHistogramProvider, makeGaugeProvider } from '@willsoto/nestjs-prometheus';

@Module({
  providers: [
    // HTTP metrics
    makeCounterProvider({
      name: 'http_requests_total',
      help: 'Total number of HTTP requests',
      labelNames: ['method', 'endpoint', 'status'],
    }),
    makeHistogramProvider({
      name: 'http_request_duration_seconds',
      help: 'HTTP request latency in seconds',
      labelNames: ['method', 'endpoint'],
      buckets: [0.001, 0.005, 0.01, 0.05, 0.1, 0.5, 1, 5],
    }),
    makeGaugeProvider({
      name: 'http_requests_in_progress',
      help: 'Number of HTTP requests currently in progress',
      labelNames: ['method', 'endpoint'],
    }),
    
    // Business metrics
    makeCounterProvider({
      name: 'notes_created_total',
      help: 'Total number of notes created',
      labelNames: ['user_role'],
    }),
    makeCounterProvider({
      name: 'auth_attempts_total',
      help: 'Total authentication attempts',
      labelNames: ['success', 'method'],
    }),
  ],
})
export class AppModule {}
```

#### Step 3: Grafana Setup and Dashboards (8-10 hours)

**1. Add Grafana to Docker Compose:**

```yaml
grafana:
  image: grafana/grafana:latest
  container_name: back2code-grafana
  ports:
    - "3001:3000"
  environment:
    - GF_SECURITY_ADMIN_USER=admin
    - GF_SECURITY_ADMIN_PASSWORD=admin
    - GF_USERS_ALLOW_SIGN_UP=false
  volumes:
    - grafana_data:/var/lib/grafana
    - ./monitoring/grafana/provisioning:/etc/grafana/provisioning
  depends_on:
    - prometheus
  networks:
    - back2code

volumes:
  grafana_data:
```

**2. Provision Prometheus Data Source:**

```yaml
# monitoring/grafana/provisioning/datasources/prometheus.yml
apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
    editable: true
```

**3. Create API Overview Dashboard (JSON):**

```json
{
  "dashboard": {
    "title": "API Overview",
    "tags": ["api", "overview"],
    "timezone": "browser",
    "panels": [
      {
        "title": "Request Rate (requests/sec)",
        "targets": [
          {
            "expr": "sum(rate(http_requests_total[5m])) by (method)",
            "legendFormat": "{{method}}"
          }
        ],
        "type": "graph"
      },
      {
        "title": "Error Rate (%)",
        "targets": [
          {
            "expr": "sum(rate(http_requests_total{status=~\"5..\"}[5m])) / sum(rate(http_requests_total[5m])) * 100",
            "legendFormat": "Error Rate"
          }
        ],
        "type": "graph",
        "alert": {
          "conditions": [
            {
              "evaluator": {
                "type": "gt",
                "params": [5]
              }
            }
          ]
        }
      },
      {
        "title": "Latency (p50, p95, p99)",
        "targets": [
          {
            "expr": "histogram_quantile(0.50, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "p50"
          },
          {
            "expr": "histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "p95"
          },
          {
            "expr": "histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "p99"
          }
        ],
        "type": "graph"
      }
    ]
  }
}
```

**4. Create System Metrics Dashboard:**

Key panels:
- CPU usage per instance
- Memory usage per instance
- Event loop lag
- Heap size and used
- Active connections
- Database connection pool utilization

#### Step 4: Centralized Logging with Loki (6-8 hours)

**1. Add Loki to Docker Compose:**

```yaml
loki:
  image: grafana/loki:latest
  container_name: back2code-loki
  ports:
    - "3100:3100"
  command: -config.file=/etc/loki/local-config.yaml
  volumes:
    - ./monitoring/loki/loki-config.yml:/etc/loki/local-config.yaml
    - loki_data:/loki
  networks:
    - back2code

promtail:
  image: grafana/promtail:latest
  container_name: back2code-promtail
  volumes:
    - /var/log:/var/log
    - ./monitoring/promtail/promtail-config.yml:/etc/promtail/config.yml
  command: -config.file=/etc/promtail/config.yml
  depends_on:
    - loki
  networks:
    - back2code

volumes:
  loki_data:
```

**2. Configure Loki:**

```yaml
# monitoring/loki/loki-config.yml
auth_enabled: false

server:
  http_listen_port: 3100

ingester:
  lifecycler:
    address: 127.0.0.1
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1
  chunk_idle_period: 5m
  chunk_retain_period: 30s

schema_config:
  configs:
    - from: 2020-05-15
      store: boltdb
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 168h

storage_config:
  boltdb:
    directory: /loki/index
  filesystem:
    directory: /loki/chunks

limits_config:
  enforce_metric_name: false
  reject_old_samples: true
  reject_old_samples_max_age: 168h

chunk_store_config:
  max_look_back_period: 0s

table_manager:
  retention_deletes_enabled: true
  retention_period: 168h
```

**3. Integrate Winston with Loki:**

```bash
npm install winston winston-loki
```

```typescript
// logging/winston-logger.service.ts
import { Injectable, LoggerService } from '@nestjs/common';
import * as winston from 'winston';
import LokiTransport from 'winston-loki';

@Injectable()
export class WinstonLoggerService implements LoggerService {
  private logger: winston.Logger;

  constructor() {
    this.logger = winston.createLogger({
      level: process.env.LOG_LEVEL || 'info',
      format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.errors({ stack: true }),
        winston.format.json()
      ),
      defaultMeta: {
        service: 'back2code-api',
        instance: process.env.INSTANCE_ID || 'unknown',
      },
      transports: [
        new winston.transports.Console({
          format: winston.format.combine(
            winston.format.colorize(),
            winston.format.simple()
          ),
        }),
        new LokiTransport({
          host: 'http://loki:3100',
          labels: {
            app: 'back2code',
            environment: process.env.NODE_ENV || 'development',
          },
          json: true,
          format: winston.format.json(),
          replaceTimestamp: true,
          onConnectionError: (err) => console.error(err),
        }),
      ],
    });
  }

  log(message: string, context?: string) {
    this.logger.info(message, { context });
  }

  error(message: string, trace?: string, context?: string) {
    this.logger.error(message, { trace, context });
  }

  warn(message: string, context?: string) {
    this.logger.warn(message, { context });
  }

  debug(message: string, context?: string) {
    this.logger.debug(message, { context });
  }
}
```

**4. Add Request Logging Middleware:**

```typescript
// common/middleware/request-logger.middleware.ts
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';
import { WinstonLoggerService } from '../logging/winston-logger.service';

@Injectable()
export class RequestLoggerMiddleware implements NestMiddleware {
  constructor(private logger: WinstonLoggerService) {}

  use(req: Request, res: Response, next: NextFunction) {
    const startTime = Date.now();
    const { method, originalUrl, ip } = req;
    const userAgent = req.get('user-agent') || '';

    res.on('finish', () => {
      const { statusCode } = res;
      const duration = Date.now() - startTime;
      const user = req['user'];

      this.logger.log('HTTP Request', {
        method,
        url: originalUrl,
        statusCode,
        duration,
        ip,
        userAgent,
        userId: user?.id,
        traceId: req['traceId'],
      });
    });

    next();
  }
}
```

#### Step 5: Distributed Tracing with Jaeger (6-8 hours)

**1. Add Jaeger to Docker Compose:**

```yaml
jaeger:
  image: jaegertracing/all-in-one:latest
  container_name: back2code-jaeger
  ports:
    - "5775:5775/udp"
    - "6831:6831/udp"
    - "6832:6832/udp"
    - "5778:5778"
    - "16686:16686"  # UI
    - "14268:14268"  # Collector
    - "14250:14250"
    - "9411:9411"
  environment:
    - COLLECTOR_ZIPKIN_HTTP_PORT=9411
  networks:
    - back2code
```

**2. Install OpenTelemetry:**

```bash
npm install @opentelemetry/api @opentelemetry/sdk-node
npm install @opentelemetry/auto-instrumentations-node
npm install @opentelemetry/exporter-jaeger
```

**3. Configure Tracing:**

```typescript
// tracing.ts
import { NodeSDK } from '@opentelemetry/sdk-node';
import { getNodeAutoInstrumentations } from '@opentelemetry/auto-instrumentations-node';
import { JaegerExporter } from '@opentelemetry/exporter-jaeger';
import { Resource } from '@opentelemetry/resources';
import { SemanticResourceAttributes } from '@opentelemetry/semantic-conventions';

const traceExporter = new JaegerExporter({
  endpoint: 'http://jaeger:14268/api/traces',
});

const sdk = new NodeSDK({
  resource: new Resource({
    [SemanticResourceAttributes.SERVICE_NAME]: 'back2code-api',
    [SemanticResourceAttributes.SERVICE_VERSION]: '1.0.0',
    [SemanticResourceAttributes.DEPLOYMENT_ENVIRONMENT]: process.env.NODE_ENV || 'development',
  }),
  traceExporter,
  instrumentations: [
    getNodeAutoInstrumentations({
      '@opentelemetry/instrumentation-http': {
        enabled: true,
        ignoreIncomingPaths: ['/health', '/metrics'],
      },
      '@opentelemetry/instrumentation-express': {
        enabled: true,
      },
      '@opentelemetry/instrumentation-pg': {
        enabled: true,
      },
      '@opentelemetry/instrumentation-redis': {
        enabled: true,
      },
    }),
  ],
});

sdk.start();

process.on('SIGTERM', () => {
  sdk.shutdown()
    .then(() => console.log('Tracing terminated'))
    .catch((error) => console.log('Error terminating tracing', error))
    .finally(() => process.exit(0));
});
```

**4. Add Tracing to Main:**

```typescript
// main.ts
import './tracing'; // Must be first import

import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  // ...
  await app.listen(3000);
}

bootstrap();
```

#### Step 6: AlertManager Configuration (4-5 hours)

**1. Add AlertManager to Docker Compose:**

```yaml
alertmanager:
  image: prom/alertmanager:latest
  container_name: back2code-alertmanager
  ports:
    - "9093:9093"
  volumes:
    - ./monitoring/alertmanager/config.yml:/etc/alertmanager/config.yml
    - alertmanager_data:/alertmanager
  command:
    - '--config.file=/etc/alertmanager/config.yml'
    - '--storage.path=/alertmanager'
  networks:
    - back2code

volumes:
  alertmanager_data:
```

**2. Configure AlertManager:**

```yaml
# monitoring/alertmanager/config.yml
global:
  resolve_timeout: 5m
  slack_api_url: '${SLACK_WEBHOOK_URL}'

route:
  group_by: ['alertname', 'cluster', 'service']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 12h
  receiver: 'slack-notifications'
  
  routes:
    - match:
        severity: critical
      receiver: 'pagerduty-critical'
      continue: true
    
    - match:
        severity: warning
      receiver: 'slack-warnings'

receivers:
  - name: 'slack-notifications'
    slack_configs:
      - channel: '#alerts'
        title: 'Alert: {{ .GroupLabels.alertname }}'
        text: '{{ range .Alerts }}{{ .Annotations.description }}{{ end }}'
        send_resolved: true

  - name: 'pagerduty-critical'
    pagerduty_configs:
      - service_key: '${PAGERDUTY_SERVICE_KEY}'
        description: '{{ .GroupLabels.alertname }}'

  - name: 'slack-warnings'
    slack_configs:
      - channel: '#warnings'
        send_resolved: true

inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'cluster', 'service']
```

**3. Define Alert Rules:**

```yaml
# monitoring/prometheus/alerts/api-alerts.yml
groups:
  - name: api_alerts
    interval: 30s
    rules:
      # High Error Rate
      - alert: HighErrorRate
        expr: |
          (sum(rate(http_requests_total{status=~"5.."}[5m])) 
          / sum(rate(http_requests_total[5m]))) * 100 > 5
        for: 5m
        labels:
          severity: critical
          service: api
        annotations:
          summary: "High error rate detected"
          description: "Error rate is {{ $value }}% (threshold: 5%)"
          runbook: "https://wiki.company.com/runbooks/high-error-rate"

      # High Latency
      - alert: HighLatency
        expr: |
          histogram_quantile(0.95, 
            sum(rate(http_request_duration_seconds_bucket[5m])) by (le)
          ) > 0.3
        for: 5m
        labels:
          severity: warning
          service: api
        annotations:
          summary: "High API latency detected"
          description: "p95 latency is {{ $value }}s (threshold: 0.3s)"

      # SLO Budget Burn Rate
      - alert: SLOBudgetBurnRateFast
        expr: |
          (1 - (sum(rate(http_requests_total{status!~"5.."}[1h])) 
          / sum(rate(http_requests_total[1h])))) > 0.001
        for: 2m
        labels:
          severity: critical
          service: api
        annotations:
          summary: "SLO error budget burning too fast"
          description: "At this rate, monthly error budget will be exhausted"

      # Service Down
      - alert: ServiceDown
        expr: up{job="api"} == 0
        for: 1m
        labels:
          severity: critical
          service: api
        annotations:
          summary: "API instance {{ $labels.instance }} is down"
          description: "Instance has been down for more than 1 minute"

      # Database Connection Pool Saturation
      - alert: DatabasePoolSaturation
        expr: |
          database_connections_active 
          / database_connections_max > 0.8
        for: 5m
        labels:
          severity: warning
          service: database
        annotations:
          summary: "Database connection pool near capacity"
          description: "Pool utilization is {{ $value }}% (threshold: 80%)"
```

### Phase 3: Enhancement (8-10 hours)

#### 1. Advanced Dashboards (3-4 hours)

**SLO Dashboard:**
- Error budget tracking
- Burn rate visualization
- Time to exhaustion projections
- Historical SLO compliance

**Business Metrics Dashboard:**
- User activity metrics
- Feature usage statistics
- Conversion funnels
- Revenue-related metrics

**Debugging Dashboard:**
- Correlated metrics, logs, and traces
- Anomaly detection
- Comparative analysis (this week vs last week)

#### 2. Log Correlation (2 hours)

**Link traces to logs:**
```typescript
this.logger.log('Database query completed', {
  traceId: span.context().traceId,
  spanId: span.context().spanId,
  duration: queryDuration,
  query: sanitizedQuery,
});
```

**Grafana explore:**
- Filter logs by trace ID
- Jump from metrics to logs
- Navigate from logs to traces

#### 3. On-Call Runbooks (2-3 hours)

Create detailed runbooks for common alerts:

**Template:**
```markdown
# Runbook: High Error Rate

## Alert Details
- **Severity:** Critical
- **Threshold:** Error rate > 5% for 5 minutes
- **SLO Impact:** High - affects availability SLO

## Initial Assessment (2 minutes)
1. Check Grafana API Overview dashboard
2. Identify which endpoint(s) have errors
3. Check recent deployments (annotation in Grafana)

## Investigation (5 minutes)
1. Query logs: `{app="back2code"} |= "error" | json`
2. Find common error pattern
3. Check Jaeger traces for failed requests
4. Verify dependent services (database, Redis, external APIs)

## Common Causes
1. **Database down:** Check database dashboard, connection errors in logs
2. **Recent deployment bug:** Check deployment annotations, rollback if needed
3. **External API failure:** Check circuit breaker metrics
4. **Memory leak:** Check heap usage trends

## Resolution Steps

### If Database Issue:
1. Check database health: `curl localhost/health`
2. Promote replica if primary down: `docker exec postgres-replica-1 pg_ctl promote`
3. Update app config to point to new primary
4. Restart API instances

### If Deployment Issue:
1. Verify error started after deployment
2. Rollback: `./scripts/rollback.sh`
3. Monitor error rate drop
4. Create incident for investigation

### If External Dependency:
1. Check circuit breaker state
2. Verify fallback behavior working
3. Contact external service provider
4. Consider temporary disable of feature

## Escalation
- If not resolved in 15 minutes: Page @oncall-backend-lead
- If customer-impacting: Notify @incident-commander

## Post-Incident
1. Update status page
2. Start postmortem document
3. Schedule postmortem meeting within 24 hours
```

#### 4. Custom Alerts (1 hour)

**Business metric alerts:**
```yaml
- alert: NoUserRegistrations
  expr: |
    increase(user_registrations_total[1h]) == 0
  for: 1h
  labels:
    severity: warning
  annotations:
    summary: "No new user registrations in the last hour"
    description: "This could indicate a problem with the registration flow"

- alert: UnusuallyHighNoteCreation
  expr: |
    rate(notes_created_total[5m]) > 
    avg_over_time(rate(notes_created_total[5m])[7d:]) * 3
  for: 10m
  labels:
    severity: info
  annotations:
    summary: "Unusually high note creation rate"
    description: "Could be viral content or potential abuse"
```

---

## 5. Complexity Validation

### Too Basic Indicators (Not Present)

- ❌ Simple console logging only
- ❌ No metrics collection
- ❌ Manual log file inspection
- ❌ No alerting
- ❌ No centralized monitoring

### Appropriate Indicators (Present) ✅

- ✅ **Full observability stack:** Prometheus, Grafana, Loki, Jaeger
- ✅ **Production patterns:** RED/USE metrics, SLO-based alerts
- ✅ **Distributed tracing:** OpenTelemetry with automatic instrumentation
- ✅ **Centralized logging:** Structured logs with correlation
- ✅ **Intelligent alerting:** Severity-based routing, runbooks
- ✅ **Dashboard hierarchy:** From executive to debug level
- ✅ **SRE practices:** SLIs, SLOs, error budgets
- ✅ **Incident response:** Runbooks, escalation, postmortems

### Too Advanced Indicators (Not Present)

- ❌ AIOps / ML-based anomaly detection
- ❌ Multi-cluster federation
- ❌ Long-term storage (Thanos, Cortex)
- ❌ Advanced tracing (exemplars, trace sampling service)
- ❌ Service mesh observability (Istio, Linkerd)

### Complexity Rating: **ADVANCED**

### Justification

This module is **advanced** because:

**1. Complete Observability Stack:**
- Four major tools (Prometheus, Grafana, Loki, Jaeger)
- Integration between all pillars (metrics, logs, traces)
- Production-grade configuration
- Multiple exporters and integrations

**2. SRE Discipline:**
- SLI/SLO/SLA understanding and implementation
- Error budget calculations
- Proactive vs reactive monitoring
- Incident response procedures

**3. Distributed System Challenges:**
- Monitoring across multiple instances
- Correlation between services
- Context propagation in traces
- Aggregating logs from distributed sources

**4. Operational Maturity:**
- Alert design and routing
- Runbook creation
- On-call practices
- Postmortem culture
- Dashboard hierarchy

**5. Query Languages:**
- PromQL for metrics
- LogQL for logs
- Understanding histogram quantiles
- Alert rule expressions

**Why Appropriate for Your Profile:**
- Observability is critical at scale
- SRE practices expected at senior+ levels
- Production operations knowledge
- Incident response experience valuable in leadership

⚠️ **CONCERN:** This is a data-heavy module with steep learning curve for query languages.

**Mitigation:**
- Start with pre-built dashboards
- Progressive complexity (simple metrics → complex queries)
- Extensive examples and templates
- Break & Observe exercises reinforce patterns

---

## 6. Best Practices Integration

### Instrumentation Best Practices

**1. Consistent Naming:**
```
<namespace>_<subsystem>_<name>_<unit>
Example: back2code_http_requests_total
```

**2. Label Hygiene:**
- Keep cardinality low (< 10 values per label)
- Use consistent label names across metrics
- Don't use unbounded values (user IDs, trace IDs) as labels

**3. Metric Types:**
- Counter for cumulative values (requests, errors)
- Gauge for current state (memory, connections)
- Histogram for distributions (latency, request size)

### Dashboard Best Practices

**1. Dashboard Organization:**
- One primary purpose per dashboard
- Left-to-right, top-to-bottom reading flow
- Most important metrics at top
- Group related panels

**2. Panel Design:**
- Clear titles and descriptions
- Appropriate visualization type
- Consistent color schemes
- Meaningful thresholds and alerts

**3. Variables and Templates:**
```yaml
# Instance selector variable
variables:
  - name: instance
    type: query
    query: label_values(up{job="api"}, instance)
    
# Use in panel:
sum(rate(http_requests_total{instance="$instance"}[5m]))
```

### Alerting Best Practices

**1. Alert Quality:**
- Every alert should be actionable
- Include runbook link
- Set appropriate severity
- Test alert rules

**2. Alert Grouping:**
```yaml
route:
  group_by: ['alertname', 'cluster']
  group_wait: 10s
  group_interval: 5m
```

**3. Alert Hygiene:**
- Review alerts quarterly
- Remove noisy alerts
- Adjust thresholds based on data
- Document why each alert exists

### Operational Excellence

**1. Monitoring the Monitors:**
```yaml
- alert: PrometheusDown
  expr: up{job="prometheus"} == 0
  for: 2m
  labels:
    severity: critical
```

**2. Capacity Planning:**
- Track metrics trends over time
- Forecast resource needs
- Alert on approaching capacity limits
- Plan for seasonal variations

**3. Cost Management:**
- Set retention policies
- Sample traces appropriately
- Aggregate old metrics
- Monitor storage usage

---

## 7. "Break & Observe" Exercises

### Exercise 1: Simulating High Latency

**What to Break:**
```bash
# Add 500ms latency to database queries
# Modify one API instance to have slow queries
docker exec -it back2code-api-1 bash
# Inside container: Add artificial delay to database queries
```

**Expected Behavior:**
- Grafana shows p95/p99 latency spike
- Load balancer routes more traffic to healthy instances
- Traces show slow database spans
- Alerts fire if latency threshold exceeded

**What to Observe:**
1. **Metrics:**
   - Check latency histogram in Grafana
   - Compare instances: one slow, others normal
   - Request rate changes (LB routing away)

2. **Logs:**
   - Query Loki: `{instance="api-1"} |= "query" | json | duration_ms > 400`
   - Find slow query logs
   - Correlation with error logs

3. **Traces:**
   - Open Jaeger, filter by slow traces
   - Identify database span taking 500ms+
   - See cascade effect on overall request

**Key Learnings:**
- How latency appears in each observability pillar
- Correlation between metrics, logs, and traces
- Load balancer's role in isolating issues
- p95/p99 more useful than average for latency

---

### Exercise 2: Simulating Service Outage

**What to Break:**
```bash
# Stop one API instance
docker stop back2code-api-2

# Generate load
ab -n 1000 -c 50 http://localhost/api/v1/notes
```

**Expected Behavior:**
- Alert fires: "ServiceDown - api-2"
- Prometheus shows `up{instance="api-2"} = 0`
- Grafana panels for api-2 show no data
- Request rate increases on other instances
- Overall success rate remains high (no user impact)

**What to Observe:**
1. **Prometheus:**
   - `up` metric = 0 for api-2
   - Scrape target shows as "DOWN"
   
2. **Grafana:**
   - API Overview dashboard shows 2/3 instances healthy
   - Request distribution shifts
   - No increase in error rate (good!)

3. **Alerts:**
   - Alert fires after 1 minute (as configured)
   - Notification sent to Slack/PagerDuty
   - Alert includes instance name and duration

4. **Jaeger:**
   - No traces from api-2
   - All traces route through api-1 and api-3

**Key Learnings:**
- Redundancy prevents user-facing impact
- Alerts notify before users affected
- Monitoring helps verify failover works
- Importance of `up` metric

---

### Exercise 3: SLO Budget Burn Rate

**What to Break:**
```bash
# Introduce bug causing 10% error rate
# Modify code to fail 1 in 10 requests randomly
```

**Expected Behavior:**
- Error rate spikes to 10%
- SLO budget burns rapidly
- Alert fires: "SLOBudgetBurnRateFast"
- If sustained, monthly SLO violated

**What to Observe:**
1. **Calculate Burn Rate:**
   ```
   SLO: 99.9% success rate
   Error Budget: 0.1% = 43.2 minutes/month
   
   Current Error Rate: 10%
   Burn Rate: 10% / 0.1% = 100x
   Time to Exhaustion: 43.2 min / 100 = ~26 seconds
   ```

2. **Grafana SLO Dashboard:**
   - Error budget depletion chart
   - Burn rate visualization
   - Time remaining projection

3. **Decision Point:**
   - If budget < 50%: escalate to critical
   - If budget exhausted: halt feature releases
   - Focus team on reliability

**Key Learnings:**
- SLO provides objective reliability measure
- Error budget quantifies acceptable risk
- Fast burn rate triggers immediate response
- Balancing features vs reliability

---

### Exercise 4: Log Volume and Filtering

**What to Break:**
```bash
# Enable debug logging on all instances
# Increase log verbosity to create noise
LOG_LEVEL=debug docker-compose restart
```

**Expected Behavior:**
- Log volume increases 10-100x
- Loki storage fills rapidly
- Important logs harder to find
- Performance impact on logging pipeline

**What to Observe:**
1. **Loki Storage:**
   ```bash
   du -sh /var/lib/loki/
   # Compare before and after
   ```

2. **Query Performance:**
   ```
   # Time how long queries take:
   {app="back2code"} |= "error"  # Slow with debug logs
   ```

3. **Finding Signal in Noise:**
   ```
   # Effective queries:
   {app="back2code", level="error"}
   {app="back2code"} | json | severity = "critical"
   ```

**Key Learnings:**
- Log level management crucial
- Structured logs enable better filtering
- Cost of excessive logging
- Balance detail vs noise

---

### Exercise 5: Trace Sampling Impact

**What to Break:**
```bash
# Change trace sampling rate from 100% to 1%
# Modify OTEL_TRACES_SAMPLER=parentbased_traceidratio
# OTEL_TRACES_SAMPLER_ARG=0.01
```

**Expected Behavior:**
- 99% of traces not collected
- Jaeger shows far fewer traces
- Some issues might be missed
- Storage and performance improve

**What to Observe:**
1. **Jaeger UI:**
   - Trace count drops dramatically
   - Still see some errors (if tail-based sampling)
   - Service graph less complete

2. **Find Rare Issues:**
   - Simulate rare error (1 in 1000 requests)
   - With 1% sampling, might not capture
   - Need tail-based sampling or higher rate

3. **Cost vs Coverage:**
   ```
   100% sampling: Complete data, high cost
   10% sampling: 90% reduction, still good coverage
   1% sampling: 99% reduction, might miss issues
   ```

**Key Learnings:**
- Sampling necessary at scale
- Tail-based sampling captures important traces
- Balance cost vs debuggability
- Errors and slow requests should always be sampled

---

### Exercise 6: Alert Fatigue Simulation

**What to Break:**
```bash
# Create intentionally noisy alert
# Alert on every request > 100ms

- alert: TooSensitive
  expr: http_request_duration_seconds > 0.1
  for: 0s
  labels:
    severity: critical
```

**Expected Behavior:**
- Alerts fire constantly
- Slack/PagerDuty flooded with notifications
- Important alerts lost in noise
- On-call engineer overwhelmed

**What to Observe:**
1. **Alert Volume:**
   - Count alerts in 1 hour period
   - Calculate notification fatigue

2. **Impact:**
   - Important alerts missed
   - Slow response to real issues
   - Team starts ignoring alerts

3. **Fix:**
   ```yaml
   # Improved alert:
   - alert: HighLatencySustained
     expr: |
       histogram_quantile(0.95, 
         rate(http_request_duration_seconds_bucket[5m])
       ) > 0.5
     for: 10m  # Sustained, not transient
     labels:
       severity: warning  # Not critical
   ```

**Key Learnings:**
- Alert quality > quantity
- Every alert should be actionable
- Group and aggregate alerts
- Regular alert review necessary
- Alert fatigue is real problem

---

### Exercise 7: Incident Response Drill

**What to Break:**
```bash
# Simulate production incident
# 1. Kill database primary
docker stop back2code-postgres-primary

# 2. High load
ab -n 10000 -c 100 http://localhost/api/v1/notes
```

**Expected Behavior:**
- Multiple alerts fire
- Error rate spikes
- User-facing impact
- Need coordinated response

**What to Observe and Practice:**

1. **Detection (Target: < 2 minutes)**
   - When did you first notice?
   - Which alert fired first?
   - How clear was the alert?

2. **Diagnosis (Target: < 5 minutes)**
   - Check Grafana dashboards
   - Query logs for errors
   - Trace failed requests
   - Identify: database is down

3. **Mitigation (Target: < 10 minutes)**
   - Follow runbook
   - Promote replica to primary
   - Update configuration
   - Verify recovery

4. **Communication:**
   - Update status page
   - Notify stakeholders
   - Set expectations

5. **Resolution:**
   - Confirm all systems green
   - Monitor for aftereffects
   - Declare incident resolved

6. **Postmortem (Within 24 hours):**
   ```markdown
   # Incident Postmortem: Database Primary Failure
   
   ## Timeline
   - 10:00 - Primary database crashed
   - 10:02 - Alert fired
   - 10:03 - On-call engineer paged
   - 10:05 - Diagnosis complete
   - 10:08 - Replica promoted
   - 10:12 - Services recovered
   
   ## Impact
   - 12 minutes of degraded service
   - ~1000 failed requests
   - SLO budget: -2.8% (exhausted 12 min of 43.2 min)
   
   ## Root Cause
   - Database primary ran out of disk space
   - No alert for disk usage
   
   ## Action Items
   1. Add disk usage alert [Owner: @alice] [Due: 2 days]
   2. Automate replica promotion [Owner: @bob] [Due: 1 week]
   3. Test failover quarterly [Owner: @team] [Recurring]
   
   ## What Went Well
   - Alerts fired quickly
   - Runbook was helpful
   - Team responded effectively
   
   ## What Could Improve
   - Faster diagnosis (took 3 min to identify cause)
   - Better disk monitoring
   - Automated failover
   ```

**Key Learnings:**
- Incident response is a skill
- Observability enables fast diagnosis
- Runbooks speed up resolution
- Postmortems prevent recurrence
- Practice makes perfect

---

## 8. Success Criteria

### Knowledge: Can Explain Concepts and Trade-offs

**You should be able to explain:**

1. **Three Pillars:**
   - ✅ When to use metrics vs logs vs traces
   - ✅ How they complement each other
   - ✅ Trade-offs of each

2. **Prometheus:**
   - ✅ Data model and metric types
   - ✅ Pull vs push model
   - ✅ PromQL query patterns
   - ✅ Cardinality and its impact

3. **SLO/SLI/SLA:**
   - ✅ Difference between them
   - ✅ How to define good SLIs
   - ✅ Error budget calculations
   - ✅ Burn rate alerts

4. **Distributed Tracing:**
   - ✅ Span and trace structure
   - ✅ Context propagation
   - ✅ Sampling strategies
   - ✅ When tracing is essential

5. **Alerting:**
   - ✅ Good alert characteristics
   - ✅ Alert fatigue causes and prevention
   - ✅ Severity classification
   - ✅ Runbook structure

### Skills: Can Implement Without Assistance

**You should be able to:**

1. **Metrics:**
   - ✅ Instrument application with Prometheus
   - ✅ Create custom metrics
   - ✅ Write PromQL queries
   - ✅ Configure scrape targets

2. **Dashboards:**
   - ✅ Create Grafana dashboards
   - ✅ Use variables and templates
   - ✅ Design panel layouts
   - ✅ Set up alerts in Grafana

3. **Logging:**
   - ✅ Configure structured logging
   - ✅ Set up centralized logging (Loki/ELK)
   - ✅ Query logs effectively
   - ✅ Correlate logs with traces

4. **Tracing:**
   - ✅ Instrument with OpenTelemetry
   - ✅ Configure Jaeger
   - ✅ Analyze traces
   - ✅ Set sampling rates

5. **Alerting:**
   - ✅ Write alert rules
   - ✅ Configure AlertManager
   - ✅ Set up notification channels
   - ✅ Create runbooks

### Application: Can Diagnose and Fix Issues

**You should be able to:**

1. **Use Observability for Debugging:**
   - ✅ Start with metrics to identify problem
   - ✅ Dive into logs for details
   - ✅ Use traces to find bottlenecks
   - ✅ Correlate across all three pillars

2. **Performance Analysis:**
   - ✅ Identify slow endpoints
   - ✅ Find database bottlenecks
   - ✅ Optimize based on metrics
   - ✅ Verify improvements

3. **Incident Response:**
   - ✅ Detect issues quickly (MTTD)
   - ✅ Diagnose root cause
   - ✅ Follow runbooks
   - ✅ Communicate status
   - ✅ Write postmortems

4. **Capacity Planning:**
   - ✅ Analyze trends
   - ✅ Forecast resource needs
   - ✅ Identify saturation points
   - ✅ Plan for growth

---

## 9. Postmortem Template

**Module:** Module 5: Observability - Metrics, Logging, Monitoring & Alerting
**Completed Date:** ___________  
**Actual Time Spent:** _____ hours (vs estimated 63-81 hours)

### What Worked Well?

**Technical Implementation:**
- Which observability tool was easiest to set up?
- Did Prometheus scraping work smoothly?
- Were Grafana dashboards intuitive?
- How effective was distributed tracing?

**Learning Process:**
- Which observability pillar made most sense?
- Did hands-on exercises deepen understanding?
- Were PromQL and LogQL learnable?
- Did incident drills prepare you for real incidents?

**Tools and Technologies:**
- How was Prometheus vs other monitoring tools?
- Was Grafana powerful enough?
- Did Loki meet logging needs?
- How was Jaeger for tracing?

### What Was Challenging?

**Conceptual Challenges:**
- Understanding when to use each pillar?
- Grasping SLO/SLI/SLA differences?
- PromQL query language?
- Trace sampling strategies?

**Technical Difficulties:**
- Prometheus configuration issues?
- Grafana dashboard design?
- Log parsing and querying?
- OpenTelemetry instrumentation?
- AlertManager routing?

**Operational Complexity:**
- Managing multiple observability tools?
- Correlating data across pillars?
- Defining meaningful SLOs?
- Preventing alert fatigue?

### Key Technical Insights Gained

**Observability:**
- What did you learn about metrics vs logs vs traces?
- When is each pillar most valuable?
- How do they work together?
- What surprised you about observability?

**SRE Practices:**
- How do SLOs change how you think about reliability?
- What is appropriate error budget?
- How to balance reliability vs features?
- Value of blameless postmortems?

**Incident Response:**
- How does observability accelerate diagnosis?
- What makes good runbooks?
- How to prevent alert fatigue?
- Value of practice drills?

**Query Languages:**
- How intuitive is PromQL?
- What PromQL patterns are most useful?
- How does LogQL compare?
- Tips for writing queries?

### Design Trade-offs Made and Rationale

**Decision 1: _____________**
(e.g., "Loki vs ELK Stack")

**Decision 2: _____________**
(e.g., "Trace sampling rate of 10%")

**Decision 3: _____________**
(e.g., "15 day metric retention")

### What Would You Do Differently Next Time?

**Architecture:**
- Different observability stack?
- More/fewer tools?
- Different retention policies?

**Implementation:**
- Start with different component?
- More comprehensive instrumentation?
- Different dashboard organization?

**Alerting:**
- Different SLO targets?
- More/fewer alerts?
- Different severity thresholds?

### How Does This Connect to Real-World Production Systems?

**Patterns You've Seen:**
- Observability stacks in production?
- SLO-based development?
- Incident response procedures?
- On-call rotations?

**Gaps vs Production Reality:**
- Multi-region observability?
- Advanced anomaly detection?
- Cost management at scale?
- Compliance and audit requirements?

**Operational Considerations:**
- How to manage observability costs?
- Retention and compliance?
- Access control for dashboards?
- Training team on observability tools?

### Module-Specific Questions

**Observability:**
- How confident instrumenting new services?
- Which pillar do you find most valuable?
- How would you expand observability?

**SRE:**
- Comfortable defining SLOs?
- How to handle SLO violations?
- Balance reliability vs velocity?

**Incident Response:**
- Ready to be on-call?
- How to improve MTTD and MTTR?
- What makes good postmortems?

### Quantitative Results

**Observability Metrics:**
- Metrics collected: _____ time series
- Log volume: _____ GB/day
- Traces sampled: _____%
- Dashboard count: _____

**Performance:**
- MTTD (Mean Time To Detect): _____ minutes
- MTTR (Mean Time To Resolve): _____ minutes
- Alert response time: _____ minutes
- False positive rate: _____%

**SLO Status:**
- API availability: _____%
- API latency (p95): _____ms
- Error budget remaining: _____%

### Questions to Explore Further

1. ___________________________________
2. ___________________________________
3. ___________________________________
4. ___________________________________
5. ___________________________________

### Preparation for Next Module

**Foundation Built:**
- Full observability for API & Integration (Module 6)
- Metrics for chaos experiments (Module 7)
- Incident response readiness

**Skills to Strengthen:**
- Advanced PromQL?
- Dashboard design?
- On-call practices?

**Mindset:**
- Confidence level going into Module 6: ___/10
- Excitement level for API integration: ___/10
- Energy level: ___/10

### Overall Reflection

**Most Valuable Learning:**
_What single observability insight will most impact your work?_

**Biggest Surprise:**
_What was most unexpected about observability implementation?_

**Pride Moment:**
_What observability achievement are you most proud of?_

**Advice to Your Past Self:**
_What would you tell yourself before starting Module 5?_

---

## 10. Coverage Gap Analysis

### ✅ Excellently Covered Areas

**Observability (PRIMARY):**
- Comprehensive coverage of all three pillars
- Prometheus, Grafana, Loki, Jaeger implementation
- SRE practices (SLIs, SLOs, error budgets)
- Incident response procedures
- **Verdict:** Excellent depth, core module strength

**Reliability, Scalability, and Availability (PRIMARY):**
- Monitoring for reliability
- Capacity planning
- Proactive monitoring
- Error budget tracking
- **Verdict:** Strong integration with observability

### ⚠️ Adequately Covered Areas

**System Design (SECONDARY):**
- Observability architecture patterns
- Monitoring pipeline design
- **Gap:** Service mesh observability (too advanced)
- **Verdict:** Sufficient for current scope

**Security and Compliance (SECONDARY):**
- Sensitive data redaction
- Audit trail with logs
- **Gap:** Deep compliance (SOC 2, ISO 27001)
- **Verdict:** Adequate foundation

### Critical Concepts Missing

**1. Cost Management:**
- **Gap:** No detailed cost analysis of observability stack
- **Recommendation:** Add to Phase 3
- **Implementation:**
  - Calculate metric cardinality cost
  - Log volume and retention costs
  - Trace storage costs
  - Cost optimization strategies

**2. Advanced Sampling:**
- **Gap:** Only basic head-based sampling covered
- **Recommendation:** Mention tail-based sampling more
- **Already in:** Theory foundation mentions it

**3. Long-term Storage:**
- **Gap:** No discussion of Thanos or Cortex
- **Recommendation:** Acceptable - too advanced for this scope
- **Note:** Mention as next step in advanced observability

### Adjustments to Ensure Comprehensive Coverage

**Add to Phase 3:**

1. **Cost Analysis Dashboard:**
   - Metric cardinality by label
   - Log volume trends
   - Storage usage forecasts
   - Cost per service

2. **Capacity Planning:**
   - Resource usage trends
   - Growth projections
   - Saturation predictions

**Add to Theory:**

1. **Observability Maturity Model:**
   - Level 1: Basic metrics and logs
   - Level 2: Structured logging, dashboards
   - Level 3: Distributed tracing, SLOs
   - Level 4: AIOps, predictive alerting
   - Level 5: Self-healing systems

### Final Coverage Assessment

| Learning Area | Coverage Level | Depth | Breadth | Module Fit | Change from Module 4 |
|---------------|----------------|-------|---------|------------|---------------------|
| Observability | PRIMARY        | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Perfect | ↑↑↑ Massive expansion |
| Reliability   | PRIMARY        | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Excellent | ↑↑ Monitoring integration |
| System Design | SECONDARY      | ⭐⭐⭐ | ⭐⭐⭐ | Good | ↑ Obs architecture |
| Security      | SECONDARY      | ⭐⭐⭐ | ⭐⭐⭐ | Good | = Log audit trails |
| API & Integration | SECONDARY  | ⭐⭐ | ⭐⭐⭐ | Adequate | = Webhook integrations |
| Data & Storage | NOT_COVERED    | - | - | Appropriate | = No change |

---

## 11. Next Module Recommendations

### Module 6: API & Integration Design - GraphQL, Events & Message Queues

**Primary Focus:**
- GraphQL endpoint implementation
- Event-driven architecture with RabbitMQ/Kafka
- Message queues and pub/sub patterns
- API versioning strategies
- Rate limiting and circuit breakers (extended)
- Webhooks and event notifications
- REST vs GraphQL trade-offs

**Why This Sequence:**

1. **Natural Progression:**
   - Module 5: Can observe system behavior
   - Module 6: Add complex integrations with monitoring
   - Can observe impact of async patterns

2. **Observability Enables:**
   - Monitor message queue metrics
   - Trace events across services
   - Debug async workflows
   - Alert on queue depth

3. **Leverages Existing Stack:**
   - Metrics for GraphQL queries
   - Traces for event propagation
   - Logs for message processing
   - Dashboards for queue health

**Key Learning Objectives:**
- Implement GraphQL API with schema-first design
- Set up message queue (RabbitMQ or Kafka)
- Build event-driven notification system
- Compare REST, GraphQL, and event-driven patterns
- Monitor async workflows

**Dependencies Satisfied by Module 5:**
✅ Can monitor GraphQL performance
✅ Can trace async event flows
✅ Can alert on message queue issues
✅ Can measure API integration success rates

---

## Validation Checklist

- [x] **All PRIMARY learning objectives are clear and measurable**
- [x] **Theory foundation covers WHY, not just HOW**
- [x] **Implementation is broken into digestible phases**
- [x] **Complexity is appropriate for the learner profile**
- [x] **At least 5 best practices are explicitly integrated**
- [x] **Break & Observe exercises are practical and insightful**
- [x] **Success criteria are specific and testable**
- [x] **Postmortem prompts encourage deep reflection**
- [x] **Module fits within the progressive learning path**
- [x] **No critical learning gaps exist**

---

## ✅ VALIDATED: This module provides appropriate depth and breadth for the stated learning objectives.

### Strengths of This Module Plan:

**1. Complete Observability Stack:**
- All three pillars: metrics, logs, traces
- Production-grade tools: Prometheus, Grafana, Loki, Jaeger
- Integration and correlation between pillars
- Real-world observability patterns

**2. SRE Discipline:**
- SLI/SLO/SLA framework
- Error budget calculations
- Incident response procedures
- Postmortem culture
- Runbook creation

**3. Practical Incident Response:**
- 7 Break & Observe exercises
- Simulated outages and incidents
- Hands-on with diagnosis and resolution
- Practice with real scenarios

**4. Query Languages:**
- PromQL for metrics
- LogQL for logs
- Practical examples and patterns
- Progressive complexity

**5. Production Operations:**
- Alert design and routing
- On-call best practices
- Dashboard hierarchy
- Cost awareness

### Why This Works for Your Profile:

**Engineering Leadership:**
- Observability critical for informed decisions
- SLO framework for setting reliability targets
- Incident response procedures for team processes
- Cost management for budget discussions

**Senior Engineer Skills:**
- Production observability implementation
- Debugging complex distributed systems
- Performance analysis and optimization
- Operational excellence

**Career Relevance:**
- Observability is foundational for modern systems
- SRE practices industry standard
- On-call experience valuable
- Incident response essential skill

### Success Probability: 85%

**Enablers:**
✅ Built on strong foundation (Modules 1-4)
✅ Progressive complexity (simple → advanced)
✅ Extensive examples and templates
✅ Hands-on exercises reinforce learning
✅ Production-ready patterns

**Risks:**
⚠️ Query language learning curve (PromQL, LogQL)
⚠️ Many tools to learn simultaneously
⚠️ Configuration complexity across stack
⚠️ Time estimation may be optimistic

**Mitigation:**
- Start with pre-built dashboards
- Progressive instrumentation (don't instrument everything at once)
- Use provided templates
- Break & Observe exercises build intuition
- Extensive documentation and examples

---

**Ready to begin Module 5.**

**Next Steps:**
1. **Theory reading (16-20 hours):** Google SRE Book, "Distributed Systems Observability", Prometheus docs
2. **Design phase (4-5 days):** Architecture, metrics taxonomy, SLI/SLO definitions
3. **Implementation:** Prometheus → Grafana → Loki → Jaeger → AlertManager
4. **Dashboards:** Create hierarchy from overview to debug
5. **Alerting:** Configure rules, routing, runbooks
6. **Break & Observe:** 7 exercises including incident drill
7. **Postmortem:** Reflect on observability value

**Key Success Factors:**
- Start simple, add complexity gradually
- Focus on actionable metrics and alerts
- Correlate data across pillars (metrics → logs → traces)
- Practice incident response (don't just read about it)
- Review and tune alerts regularly (prevent fatigue)

**Remember:** Observability is not just about tools - it's about understanding your system deeply enough to debug any issue, predict failures, and make informed decisions. This module gives you production-grade observability that transforms how you operate systems.

