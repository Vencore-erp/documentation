# Use Case Diagram: Supervisor Actor

## Overview
The Supervisor actor is responsible for approving requests, monitoring budgets, and strategic oversight.

## Use Case Diagram

```mermaid
graph LR
    Supervisor((Supervisor))

    subgraph "Approval Workflows"
        UC001[UC-SUP-001<br/>View Pending Approvals]
        UC002[UC-SUP-002<br/>Approve PR]
        UC003[UC-SUP-003<br/>Reject PR]
        UC004[UC-SUP-004<br/>Request More Info]
        UC005[UC-SUP-005<br/>Approve PO]
        UC006[UC-SUP-006<br/>Approve Contract Award]
        UC007[UC-SUP-007<br/>Approve Vendor Blacklisting]
        UC008[UC-SUP-008<br/>Approve Budget Override]
        UC009[UC-SUP-009<br/>Delegate Approval]
        UC010[UC-SUP-010<br/>Approve Change Orders]
    end

    subgraph "Budget & Analytics"
        UC011[UC-SUP-011<br/>View Budget Dashboard]
        UC012[UC-SUP-012<br/>Approve Budget Reallocation]
        UC013[UC-SUP-013<br/>View Spending by Category]
        UC014[UC-SUP-014<br/>View Saving Reports]
        UC015[UC-SUP-015<br/>Analyze Vendor Performance]
        UC016[UC-SUP-016<br/>Monitor SLA Breaches]
        UC017[UC-SUP-017<br/>View Cycle Time]
        UC018[UC-SUP-018<br/>Export Executive Summary]
        UC019[UC-SUP-019<br/>View Price Trends]
        UC020[UC-SUP-020<br/>Manage Team Workload]
    end

    Supervisor --> UC001
    Supervisor --> UC002
    Supervisor --> UC003
    Supervisor --> UC004
    Supervisor --> UC005
    Supervisor --> UC006
    Supervisor --> UC007
    Supervisor --> UC008
    Supervisor --> UC009
    Supervisor --> UC010
    Supervisor --> UC011
    Supervisor --> UC012
    Supervisor --> UC013
    Supervisor --> UC014
    Supervisor --> UC015
    Supervisor --> UC016
    Supervisor --> UC017
    Supervisor --> UC018
    Supervisor --> UC019
    Supervisor --> UC020
```

## Use Case Summary Table

| ID | Use Case Name | Category |
|:---|:---|:---|
| UC-SUP-001 | View Pending Approvals | Approval |
| UC-SUP-002 | Approve Purchase Requisition | Approval |
| UC-SUP-003 | Reject PR with Reason | Approval |
| UC-SUP-004 | Request Info (Return to Submitter) | Approval |
| UC-SUP-005 | Approve Purchase Order | Approval |
| UC-SUP-006 | Approve Contract Award | Approval |
| UC-SUP-007 | Approve Vendor Blacklisting | Approval |
| UC-SUP-008 | Approve Budget Override | Approval |
| UC-SUP-009 | Delegate Approval Authority | Config |
| UC-SUP-010 | Approve Change Orders | Approval |
| UC-SUP-011 | View Budget Utilization Dashboard | Analytics |
| UC-SUP-012 | Approve Budget Reallocation | Budget |
| UC-SUP-013 | View Spending by Category | Analytics |
| UC-SUP-014 | View Saving Reports | Analytics |
| UC-SUP-015 | Analyze Vendor Performance | Analytics |
| UC-SUP-016 | Monitor SLA Breaches | Monitoring |
| UC-SUP-017 | View Procurement Cycle Time | Analytics |
| UC-SUP-018 | Export Executive Summary | Reporting |
| UC-SUP-019 | View Historical Price Trends | Analytics |
| UC-SUP-020 | Manage Team Workload | Monitoring |
