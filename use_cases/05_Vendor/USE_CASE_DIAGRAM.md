# Use Case Diagram: Vendor Actor

## Overview
The Vendor actor is an external user responsible for registration, bidding, order fulfillment, and invoice submission.

## Use Case Diagram

```mermaid
graph LR
    Vendor((Vendor))

    subgraph "Registration & Profile"
        UC001[UC-VEN-001<br/>Register Company]
        UC002[UC-VEN-002<br/>Update Profile]
        UC003[UC-VEN-003<br/>Upload Legal Docs]
        UC004[UC-VEN-004<br/>Manage Bank Account]
        UC005[UC-VEN-005<br/>Reset Password]
    end

    subgraph "Bidding & Orders"
        UC006[UC-VEN-006<br/>View Open RFQs]
        UC007[UC-VEN-007<br/>Submit Commercial Bid]
        UC008[UC-VEN-008<br/>Submit Technical Proposal]
        UC009[UC-VEN-009<br/>Respond to Clarification]
        UC010[UC-VEN-010<br/>Acknowledge PO]
        UC011[UC-VEN-011<br/>Reject PO]
        UC012[UC-VEN-012<br/>Request Delivery Extension]
    end

    subgraph "Fulfillment & Billing"
        UC013[UC-VEN-013<br/>Create ASN]
        UC014[UC-VEN-014<br/>Submit Digital Invoice]
        UC015[UC-VEN-015<br/>Check Payment Status]
        UC016[UC-VEN-016<br/>View Vendor Scorecard]
        UC017[UC-VEN-017<br/>Respond to Dispute]
        UC018[UC-VEN-018<br/>Update Catalog Items]
        UC019[UC-VEN-019<br/>Chat with Buyer]
        UC020[UC-VEN-020<br/>Download PO PDF]
    end

    Vendor --> UC001
    Vendor --> UC002
    Vendor --> UC003
    Vendor --> UC004
    Vendor --> UC005
    Vendor --> UC006
    Vendor --> UC007
    Vendor --> UC008
    Vendor --> UC009
    Vendor --> UC010
    Vendor --> UC011
    Vendor --> UC012
    Vendor --> UC013
    Vendor --> UC014
    Vendor --> UC015
    Vendor --> UC016
    Vendor --> UC017
    Vendor --> UC018
    Vendor --> UC019
    Vendor --> UC020
```

## Use Case Summary Table

| ID | Use Case Name | Category |
|:---|:---|:---|
| UC-VEN-001 | Register Company Account | Onboarding |
| UC-VEN-002 | Update Company Profile | Profile |
| UC-VEN-003 | Upload Legal Documents | Compliance |
| UC-VEN-004 | Manage Bank Account Details | Profile |
| UC-VEN-005 | Reset Password | Security |
| UC-VEN-006 | View Open RFQs | Bidding |
| UC-VEN-007 | Submit Commercial Bid | Bidding |
| UC-VEN-008 | Submit Technical Proposal | Bidding |
| UC-VEN-009 | Respond to Clarification | Bidding |
| UC-VEN-010 | Acknowledge Purchase Order | Orders |
| UC-VEN-011 | Reject Purchase Order | Orders |
| UC-VEN-012 | Request Delivery Extension | Orders |
| UC-VEN-013 | Create Advance Shipping Notice | Fulfillment |
| UC-VEN-014 | Submit Digital Invoice | Billing |
| UC-VEN-015 | Check Payment Status | Billing |
| UC-VEN-016 | View Vendor Scorecard | Performance |
| UC-VEN-017 | Respond to Dispute | Billing |
| UC-VEN-018 | Update Catalog Items | Catalog |
| UC-VEN-019 | Chat with Buyer | Communication |
| UC-VEN-020 | Download PO PDF | Utility |
