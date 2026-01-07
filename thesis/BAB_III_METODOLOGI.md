# BAB III: METODOLOGI PENELITIAN

## 3.1 Jenis Penelitian

Penelitian ini menggunakan pendekatan **Design Science Research (DSR)** yang merupakan metodologi penelitian untuk menciptakan dan mengevaluasi IT artifacts yang dimaksudkan untuk menyelesaikan masalah organisasi yang teridentifikasi.

### 3.1.1 Karakteristik Design Science Research

| Aspek | Penjelasan |
|:---|:---|
| **Tujuan** | Menciptakan solusi inovatif (artifacts) untuk masalah yang relevan |
| **Output** | IT Artifacts: Constructs, Models, Methods, Instantiations |
| **Evaluasi** | Rigorous evaluation terhadap utility dan efficacy |
| **Kontribusi** | Kontribusi pada knowledge base dan practical applications |

### 3.1.2 DSR Guidelines (Hevner et al., 2004)

| No | Guideline | Implementasi dalam Penelitian |
|:---:|:---|:---|
| 1 | Design as an Artifact | Sistem e-Procurement sebagai IT artifact |
| 2 | Problem Relevance | Masalah fraud & compliance di perbankan |
| 3 | Design Evaluation | Functional testing, performance testing |
| 4 | Research Contributions | Novel architecture combining microservices + event-driven + compliance |
| 5 | Research Rigor | Penerapan standar ISO 27001 |
| 6 | Design as Search Process | Iterative development dengan user feedback |
| 7 | Communication | Dokumentasi thesis dan publikasi |

---

## 3.2 Tahapan Penelitian

```
┌─────────────────────────────────────────────────────────────────┐
│                    TAHAPAN PENELITIAN DSR                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  PHASE 1: PROBLEM IDENTIFICATION                                │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │  1.1 Literature Review                                     │ │
│  │  1.2 Industry Analysis                                     │ │
│  │  1.3 Problem Definition                                    │ │
│  │  OUTPUT: Problem Statement, Research Questions             │ │
│  └───────────────────────────────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  PHASE 2: OBJECTIVES DEFINITION                                 │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │  2.1 Define Solution Objectives                            │ │
│  │  2.2 Requirements Gathering                                │ │
│  │  2.3 Feasibility Assessment                                │ │
│  │  OUTPUT: Functional & Non-Functional Requirements          │ │
│  └───────────────────────────────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  PHASE 3: DESIGN & DEVELOPMENT                                  │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │  3.1 Architecture Design                                   │ │
│  │  3.2 Database Design                                       │ │
│  │  3.3 API Design                                            │ │
│  │  3.4 UI/UX Design                                          │ │
│  │  3.5 Implementation                                        │ │
│  │  OUTPUT: Working Prototype                                 │ │
│  └───────────────────────────────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  PHASE 4: DEMONSTRATION                                         │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │  4.1 Feature Demonstration                                 │ │
│  │  4.2 Use Case Walkthrough                                  │ │
│  │  OUTPUT: Demo Environment, User Scenarios                  │ │
│  └───────────────────────────────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  PHASE 5: EVALUATION                                            │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │  5.1 Functional Testing                                    │ │
│  │  5.2 Non-Functional Testing                                │ │
│  │  5.3 Compliance Verification                               │ │
│  │  5.4 User Acceptance Testing                               │ │
│  │  OUTPUT: Test Reports, Evaluation Results                  │ │
│  └───────────────────────────────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  PHASE 6: COMMUNICATION                                         │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │  6.1 Documentation                                         │ │
│  │  6.2 Thesis Writing                                        │ │
│  │  6.3 Publication Preparation                               │ │
│  │  OUTPUT: Thesis Document, Technical Documentation          │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3.3 Software Development Methodology

### 3.3.1 Agile Development dengan Scrum

Pengembangan sistem menggunakan metodologi Agile dengan framework Scrum:

| Komponen Scrum | Implementasi |
|:---|:---|
| **Sprint Duration** | 2 minggu |
| **Sprint Planning** | Setiap awal sprint |
| **Daily Standup** | Daily 15 menit |
| **Sprint Review** | Demo di akhir sprint |
| **Sprint Retrospective** | Evaluasi proses |

### 3.3.2 Sprint Planning

| Sprint | Fokus | Deliverables |
|:---:|:---|:---|
| 1-2 | Infrastructure Setup | Docker, Kubernetes, CI/CD pipeline |
| 3-4 | Auth & User Service | Login, RBAC, User CRUD |
| 5-6 | Procurement Service (PR, RFQ) | PR workflow, RFQ management |
| 7-8 | Procurement Service (PO, GRN) | PO workflow, Goods Receipt |
| 9-10 | Vendor Service | Vendor registration, KYC, Scoring |
| 11-12 | Finance Service | Invoice, 3-way match, Payment |
| 13-14 | Notification & Audit | Email notifications, Audit logging |
| 15-16 | Frontend Development | Dashboard, Core modules |
| 17-18 | Integration & Testing | End-to-end testing, Bug fixes |
| 19-20 | Documentation & UAT | Documentation, User acceptance |

---

## 3.4 Technology Stack

### 3.4.1 Backend Technologies

| Category | Technology | Version | Justification |
|:---|:---|:---:|:---|
| **Language** | Java | 21 LTS | Enterprise standard, Virtual threads |
| **Framework** | Spring Boot | 3.2.x | Production-ready, extensive ecosystem |
| **API** | Spring WebFlux | 3.2.x | Reactive, high throughput |
| **Security** | Spring Security | 6.x | Comprehensive security framework |
| **Database ORM** | Spring Data JPA | 3.2.x | Hibernate 6, efficient data access |
| **Message Broker** | Apache Kafka | 3.6.x | Event streaming, high durability |
| **API Documentation** | SpringDoc OpenAPI | 2.x | Auto-generated Swagger docs |
| **Testing** | JUnit 5 + Mockito | 5.x | Unit & integration testing |
| **Build Tool** | Gradle | 8.x | Faster builds, Kotlin DSL |

### 3.4.2 Frontend Technologies

| Category | Technology | Version | Justification |
|:---|:---|:---:|:---|
| **Framework** | Next.js | 14.x | SSR, App Router, Performance |
| **UI Components** | shadcn/ui | Latest | Customizable, Accessible |
| **Styling** | Tailwind CSS | 3.x | Utility-first, Rapid development |
| **State Management** | Zustand | 4.x | Simple, lightweight |
| **Server State** | TanStack Query | 5.x | Caching, Optimistic updates |
| **Forms** | React Hook Form | 7.x | Performance, Validation |
| **Charts** | Recharts | 2.x | React-native charts |
| **Testing** | Jest + RTL | Latest | Component testing |

### 3.4.3 Infrastructure Technologies

| Category | Technology | Version | Justification |
|:---|:---|:---:|:---|
| **Container** | Docker | 24.x | Containerization standard |
| **Orchestration** | Kubernetes | 1.29.x | Production orchestration |
| **Database** | PostgreSQL | 16 | ACID compliance, JSON support |
| **Cache** | Redis | 7.x | High-performance caching |
| **Object Storage** | MinIO | Latest | S3-compatible, Self-hosted |
| **Search Engine** | Elasticsearch | 8.x | Audit log search |
| **Monitoring** | Prometheus + Grafana | Latest | Metrics & visualization |
| **Logging** | ELK Stack | 8.x | Centralized logging |
| **Tracing** | Jaeger | Latest | Distributed tracing |
| **CI/CD** | GitHub Actions | Latest | Automated pipelines |

---

## 3.5 Development Environment

### 3.5.1 Hardware Requirements

| Component | Minimum | Recommended |
|:---|:---|:---|
| **CPU** | 4 cores | 8+ cores |
| **RAM** | 16 GB | 32 GB |
| **Storage** | 100 GB SSD | 256 GB NVMe |
| **Network** | 100 Mbps | 1 Gbps |

### 3.5.2 Software Requirements

```yaml
Development Tools:
  IDE:
    - IntelliJ IDEA Ultimate (Backend)
    - VS Code (Frontend)
  
  Version Control:
    - Git 2.40+
    - GitHub
  
  Container:
    - Docker Desktop 4.x
    - kubectl CLI
  
  Database Tools:
    - DBeaver / pgAdmin
    - Redis Insight
  
  API Testing:
    - Postman / Insomnia
    - Bruno (OpenAPI)
```

### 3.5.3 Development Environment Setup

```
┌─────────────────────────────────────────────────────────────────┐
│                  LOCAL DEVELOPMENT STACK                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │                   Docker Compose                           │ │
│  │                                                            │ │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │ │
│  │  │PostgreSQL│ │  Redis  │ │  Kafka  │ │Zookeeper│        │ │
│  │  │  :5432  │ │  :6379  │ │  :9092  │ │  :2181  │        │ │
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘        │ │
│  │                                                            │ │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐                     │ │
│  │  │  MinIO  │ │Elastic- │ │ Jaeger  │                     │ │
│  │  │  :9000  │ │ search  │ │ :16686  │                     │ │
│  │  └─────────┘ └─────────┘ └─────────┘                     │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │                 Application Services                       │ │
│  │                                                            │ │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │ │
│  │  │  Auth   │ │  User   │ │Procure- │ │ Vendor  │        │ │
│  │  │  :8081  │ │  :8082  │ │ment:8083│ │ :8084   │        │ │
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘        │ │
│  │                                                            │ │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐                     │ │
│  │  │ Finance │ │ Notif   │ │  Audit  │                     │ │
│  │  │  :8085  │ │  :8086  │ │  :8087  │                     │ │
│  │  └─────────┘ └─────────┘ └─────────┘                     │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │                      Frontend                              │ │
│  │         Next.js Development Server (:3000)                │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3.6 Testing Strategy

### 3.6.1 Testing Pyramid

```
                    ┌───────────────┐
                    │     E2E       │  ← Cypress/Playwright
                   ─┴───────────────┴─
                  ┌───────────────────┐
                  │   Integration     │  ← Spring Integration Test
                 ─┴───────────────────┴─
                ┌───────────────────────┐
                │        Unit          │  ← JUnit 5 + Mockito
               ─┴───────────────────────┴─
              ┌─────────────────────────────┐
              │         Static            │  ← SonarQube
             ─┴─────────────────────────────┴─
```

### 3.6.2 Test Categories

| Category | Coverage Target | Tools | Focus |
|:---|:---:|:---|:---|
| **Unit Tests** | > 80% | JUnit 5, Mockito | Business logic |
| **Integration Tests** | > 60% | Spring Test, Testcontainers | Service interaction |
| **API Tests** | 100% endpoints | REST Assured | Contract verification |
| **E2E Tests** | Critical paths | Cypress/Playwright | User workflows |
| **Performance Tests** | Baseline | JMeter, Gatling | Load & stress |
| **Security Tests** | OWASP Top 10 | OWASP ZAP | Vulnerability scan |

### 3.6.3 Non-Functional Testing

| NFR | Metric | Target | Tool |
|:---|:---|:---:|:---|
| **Performance** | Response time | < 500ms (P95) | JMeter |
| **Throughput** | Requests/sec | > 1000 RPS | Gatling |
| **Availability** | Uptime | 99.9% | Monitoring |
| **Scalability** | Concurrent users | > 5000 | Load test |
| **Security** | Vulnerabilities | Zero critical | OWASP ZAP |

---

## 3.7 Data Collection Methods

### 3.7.1 Functional Requirements

| Method | Source | Output |
|:---|:---|:---|
| Literature Review | Academic papers, Standards | Compliance requirements |
| Document Analysis | OJK/BI regulations | Regulatory requirements |
| Best Practice Review | Industry frameworks | Feature requirements |

### 3.7.2 Validation Data

| Method | Purpose | Metrics |
|:---|:---|:---|
| Functional Testing | Feature verification | Test pass rate |
| Performance Testing | Response time, throughput | Latency, TPS |
| Compliance Checklist | ISO 27001 mapping | Control coverage |
| Code Analysis | Code quality | SonarQube metrics |

---

## 3.8 Analysis Methods

### 3.8.1 Requirements Analysis
- **Technique**: Use Case Analysis
- **Tool**: PlantUML, Draw.io
- **Output**: Use Case Diagrams, Use Case Specifications

### 3.8.2 Architecture Analysis
- **Technique**: C4 Model
- **Tool**: Structurizr, Mermaid
- **Output**: Context, Container, Component diagrams

### 3.8.3 Data Analysis
- **Technique**: Entity-Relationship Modeling
- **Tool**: dbdiagram.io, DBeaver
- **Output**: ERD, Database Schema

### 3.8.4 Evaluation Analysis
- **Technique**: Descriptive Statistics
- **Tool**: Custom scripts, Grafana
- **Output**: Test reports, Performance metrics
