# Source Systems

## Overview

Northstar Analytics uses several business applications to manage revenue, customers, sales, marketing, product activity, and support operations.

The Executive KPI Command Center will consolidate data from these systems into one analytics environment.

This portfolio project uses fictional datasets that simulate exports from each source system.

---

## 1. Salesforce

### Business Function

Sales and opportunity management

### Primary Users

- Chief Revenue Officer
- Sales managers
- Account executives
- Revenue operations team

### Data Captured

- Accounts
- Contacts
- Opportunities
- Sales stages
- Opportunity values
- Expected close dates
- Closed-won and closed-lost outcomes
- Sales representatives
- Lead sources
- Loss reasons

### Proposed Dataset

```text
sales_opportunities.csv

# Source System Summary

| Source System | Business Area | Proposed Dataset | Refresh Frequency | Primary Executive Use |
|---|---|---|---|---|
| Salesforce | Sales | sales_opportunities.csv | Daily | Pipeline and sales performance |
| NetSuite | Finance | finance_transactions.csv | Daily | Revenue and financial reporting |
| Stripe Billing | Subscriptions | subscriptions.csv | Daily | Recurring revenue and retention |
| HubSpot | Marketing | marketing_campaigns.csv | Weekly | Marketing performance and attribution |
| Gainsight | Customer Success | customer_success.csv | Daily | Customer health and renewals |
| Mixpanel | Product | product_usage.csv | Daily | Product adoption and engagement |
| Zendesk | Support | support_tickets.csv | Daily | Service performance and customer risk |
| Customer Master File | Shared Reference Data | customers.csv | Daily | Customer segmentation and account analysis |

# Proposed Data Flow

The portfolio solution will simulate scheduled exports from each source system.

The data will move through the following process:

1. Source systems generate CSV exports.
2. Python validates and loads the files.
3. SQL staging tables store the raw source data.
4. SQL transformation models standardize and combine the data.
5. Fact and dimension tables support analytics.
6. Power BI connects to the curated reporting layer.
7. An AI-assisted workflow generates an executive performance briefing.
8. Data quality results are displayed in the command center.