# Dataset Inventory

## Overview

The Executive KPI Command Center will use eight fictional CSV datasets that simulate exports from Northstar Analytics' business systems.

Each dataset has a defined grain, expected row count, date range, business owner, primary key, and relationship to the broader analytics model.

The portfolio version will use enough data to support realistic trend analysis without making the project difficult to manage.

---

## 1. Customer Master Dataset

### File Name

`customers.csv`

### Source System

Customer Master File

### Business Purpose

Provides the central reference record for each customer account.

### Grain

One row per customer account.

### Primary Key

`account_id`

### Expected Row Count

5,000 customer accounts

### Proposed Date Range

January 1, 2022 through December 31, 2025

### Business Owner

Revenue Operations

### Key Fields

- account_id
- account_name
- industry
- region
- country
- customer_segment
- employee_band
- annual_revenue_band
- account_start_date
- customer_status
- sales_rep_id
- customer_success_manager_id

### Relationships

- One customer may have multiple subscriptions.
- One customer may have multiple sales opportunities.
- One customer may have multiple product usage events.
- One customer may have multiple support tickets.
- One customer may have one current customer success record.
- One customer may have multiple finance transactions.

### KPIs Supported

- Active customers
- Customer growth
- Customer segmentation
- Revenue by segment
- Churn by industry
- Customer lifetime value
- Product adoption by segment
- Customer health analysis

### Expected Data Quality Issues

- Duplicate account names
- Missing customer segments
- Inconsistent industry labels
- Missing account owners
- Invalid account status values

---

## 2. Subscription Dataset

### File Name

`subscriptions.csv`

### Source System

Stripe Billing

### Business Purpose

Tracks recurring subscription contracts, plan details, monthly revenue, renewal status, and cancellations.

### Grain

One row per subscription contract.

### Primary Key

`subscription_id`

### Foreign Key

`account_id`

### Expected Row Count

Approximately 6,500 subscriptions

Some customers may have more than one active or historical subscription.

### Proposed Date Range

January 1, 2022 through December 31, 2025

### Business Owner

Finance and Billing Operations

### Key Fields

- subscription_id
- account_id
- plan_id
- subscription_start_date
- subscription_end_date
- billing_frequency
- monthly_recurring_revenue
- subscription_status
- cancellation_date
- cancellation_reason
- payment_status
- auto_renewal_flag

### Relationships

- Many subscriptions may belong to one customer.
- One subscription may have multiple finance transactions.
- Subscription status supports customer retention analysis.

### KPIs Supported

- Monthly recurring revenue
- Annual recurring revenue
- Active subscriptions
- Customer churn rate
- Renewal rate
- Churned revenue
- Gross revenue retention
- Net revenue retention
- Average revenue per account

### Expected Data Quality Issues

- Active subscriptions with expired end dates
- Missing cancellation dates
- Duplicate subscription IDs
- Negative recurring revenue
- Invalid payment status values

---

## 3. Finance Transaction Dataset

### File Name

`finance_transactions.csv`

### Source System

NetSuite

### Business Purpose

Tracks invoices, payments, recurring revenue movements, adjustments, and financial transaction activity.

### Grain

One row per financial transaction.

### Primary Key

`transaction_id`

### Foreign Keys

- account_id
- subscription_id

### Expected Row Count

Approximately 60,000 transactions

### Proposed Date Range

January 1, 2022 through December 31, 2025

### Business Owner

Finance

### Key Fields

- transaction_id
- account_id
- subscription_id
- transaction_date
- transaction_type
- invoice_amount
- payment_amount
- recurring_revenue_amount
- adjustment_amount
- currency
- payment_status

### Proposed Transaction Types

- New Revenue
- Expansion
- Contraction
- Renewal
- Churn
- Refund
- Adjustment

### Relationships

- Many finance transactions may belong to one customer.
- Many finance transactions may belong to one subscription.
- Transactions will support monthly revenue movement calculations.

### KPIs Supported

- Monthly recurring revenue
- Annual recurring revenue
- New recurring revenue
- Expansion revenue
- Contraction revenue
- Churned revenue
- Revenue growth
- Average revenue per account

### Expected Data Quality Issues

- Duplicate transactions
- Missing subscription IDs
- Negative amounts without valid transaction types
- Incorrect transaction dates
- Unsupported currency values
- Missing payment statuses

---

## 4. Sales Opportunity Dataset

### File Name

`sales_opportunities.csv`

### Source System

Salesforce

### Business Purpose

Tracks sales opportunities from creation through closed-won or closed-lost outcomes.

### Grain

One row per sales opportunity.

### Primary Key

`opportunity_id`

### Foreign Key

`account_id`

### Expected Row Count

Approximately 12,000 sales opportunities

### Proposed Date Range

January 1, 2022 through December 31, 2025

### Business Owner

Revenue Operations

### Key Fields

- opportunity_id
- account_id
- sales_rep_id
- opportunity_name
- opportunity_stage
- opportunity_value
- created_date
- expected_close_date
- actual_close_date
- opportunity_status
- lead_source
- loss_reason

### Proposed Opportunity Stages

- Prospecting
- Qualification
- Discovery
- Proposal
- Negotiation
- Closed Won
- Closed Lost

### Relationships

- Many opportunities may relate to one customer account.
- Each opportunity is assigned to one sales representative.
- Closed-won opportunities may lead to new or expanded subscriptions.

### KPIs Supported

- Open pipeline value
- Pipeline coverage
- Win rate
- Average deal size
- Sales cycle length
- Funnel conversion
- Closed-won revenue
- Sales performance by representative

### Expected Data Quality Issues

- Closed opportunities missing actual close dates
- Duplicate opportunities
- Invalid stage and status combinations
- Missing lead source values
- Negative opportunity amounts
- Missing account IDs

---

## 5. Marketing Campaign Dataset

### File Name

`marketing_campaigns.csv`

### Source System

HubSpot

### Business Purpose

Tracks campaign spending, lead generation, pipeline contribution, customer acquisition, and attributed revenue.

### Grain

One row per marketing campaign.

### Primary Key

`campaign_id`

### Expected Row Count

Approximately 250 campaigns

### Proposed Date Range

January 1, 2022 through December 31, 2025

### Business Owner

Marketing Operations

### Key Fields

- campaign_id
- campaign_name
- campaign_type
- start_date
- end_date
- marketing_spend
- leads_generated
- marketing_qualified_leads
- sales_qualified_leads
- opportunities_created
- customers_acquired
- attributed_revenue

### Proposed Campaign Types

- Paid Search
- Paid Social
- Webinar
- Industry Event
- Email
- Content Marketing
- Partner Campaign

### Relationships

- Marketing campaigns may create multiple sales opportunities.
- Campaign performance will be analyzed by period and campaign type.
- The initial version will use summarized campaign-level data.

### KPIs Supported

- Cost per lead
- Marketing-qualified lead conversion
- Customer acquisition cost
- Campaign return on investment
- Marketing-sourced pipeline
- Attributed revenue

### Expected Data Quality Issues

- End dates before start dates
- Missing marketing spend
- Conversion totals exceeding lead totals
- Negative attributed revenue
- Duplicate campaign names
- Inconsistent campaign types

---

## 6. Customer Success Dataset

### File Name

`customer_success.csv`

### Source System

Gainsight

### Business Purpose

Tracks customer health, account risk, renewals, success activity, and expansion opportunities.

### Grain

One row per customer account representing the latest customer success status.

### Primary Key

`account_id`

### Expected Row Count

Approximately 5,000 records

### Proposed Snapshot Date

December 31, 2025

### Business Owner

Customer Success

### Key Fields

- account_id
- customer_success_manager_id
- health_score
- risk_status
- lifecycle_stage
- renewal_date
- last_customer_contact_date
- open_success_tasks
- expansion_opportunity_flag
- customer_notes_category

### Proposed Health Score Range

0 to 100

### Proposed Risk Status Values

- Low Risk
- Moderate Risk
- High Risk
- Critical Risk

### Relationships

- Each customer account should have one current customer success record.
- Customer success records connect with subscriptions, product usage, and support activity.

### KPIs Supported

- Customer health score
- At-risk customers
- Upcoming renewals
- Expansion candidates
- Customer engagement
- Account coverage
- Retention risk

### Expected Data Quality Issues

- Missing health scores
- Health scores outside the 0 to 100 range
- Past renewal dates for active customers
- Missing assigned managers
- Inconsistent risk labels
- Future last-contact dates

---

## 7. Product Usage Dataset

### File Name

`product_usage.csv`

### Source System

Mixpanel

### Business Purpose

Tracks product engagement, feature usage, active users, sessions, and adoption patterns.

### Grain

One row per product usage event.

### Primary Key

`usage_event_id`

### Foreign Key

`account_id`

### Expected Row Count

Approximately 500,000 usage events

### Proposed Date Range

January 1, 2024 through December 31, 2025

### Business Owner

Product Analytics

### Key Fields

- usage_event_id
- account_id
- user_id
- event_date
- event_name
- feature_name
- session_id
- usage_count
- active_user_flag
- core_feature_flag

### Proposed Event Names

- User Login
- Dashboard Viewed
- Report Created
- Report Exported
- Integration Connected
- Alert Configured
- Team Member Invited

### Relationships

- Many usage events may belong to one customer.
- Many usage events may belong to one user.
- Product usage supports adoption and customer health analysis.

### KPIs Supported

- Monthly active users
- Monthly active accounts
- Product adoption rate
- Feature adoption
- Usage frequency
- Engagement score
- Dormant accounts

### Expected Data Quality Issues

- Duplicate event IDs
- Missing account IDs
- Test users mixed with customers
- Future event dates
- Inconsistent event names
- Missing feature names

---

## 8. Support Ticket Dataset

### File Name

`support_tickets.csv`

### Source System

Zendesk

### Business Purpose

Tracks customer support demand, resolution performance, escalations, and satisfaction.

### Grain

One row per support ticket.

### Primary Key

`ticket_id`

### Foreign Key

`account_id`

### Expected Row Count

Approximately 40,000 support tickets

### Proposed Date Range

January 1, 2024 through December 31, 2025

### Business Owner

Customer Support

### Key Fields

- ticket_id
- account_id
- created_date
- resolved_date
- ticket_status
- ticket_priority
- ticket_category
- escalation_flag
- satisfaction_score
- resolution_hours

### Proposed Ticket Status Values

- Open
- Pending
- Solved
- Closed

### Proposed Ticket Priorities

- Low
- Normal
- High
- Urgent

### Relationships

- Many support tickets may belong to one customer account.
- Support activity contributes to customer health scoring.
- Ticket categories may provide product feedback.

### KPIs Supported

- Open support tickets
- Average resolution time
- Escalation rate
- Customer satisfaction
- Support tickets per customer
- Customer health score

### Expected Data Quality Issues

- Resolved tickets without resolved dates
- Resolution dates before created dates
- Invalid satisfaction scores
- Duplicate ticket IDs
- Missing account IDs
- Negative resolution hours

# Dataset Summary

| Dataset | Grain | Primary Key | Expected Rows | Date Range | Main Business Area |
|---|---|---|---:|---|---|
| customers.csv | One row per customer account | account_id | 5,000 | 2022–2025 | Customer master data |
| subscriptions.csv | One row per subscription | subscription_id | 6,500 | 2022–2025 | Billing and retention |
| finance_transactions.csv | One row per transaction | transaction_id | 60,000 | 2022–2025 | Revenue and finance |
| sales_opportunities.csv | One row per opportunity | opportunity_id | 12,000 | 2022–2025 | Sales |
| marketing_campaigns.csv | One row per campaign | campaign_id | 250 | 2022–2025 | Marketing |
| customer_success.csv | One row per current customer status | account_id | 5,000 | Snapshot as of 2025-12-31 | Customer success |
| product_usage.csv | One row per usage event | usage_event_id | 500,000 | 2024–2025 | Product engagement |
| support_tickets.csv | One row per support ticket | ticket_id | 40,000 | 2024–2025 | Customer support |

# Dataset Relationship Summary

The `customers.csv` dataset serves as the central account reference table.

The primary relationship key across the project is:

`account_id`

Primary relationships include:

- One customer to many subscriptions
- One customer to many finance transactions
- One customer to many sales opportunities
- One customer to many product usage events
- One customer to many support tickets
- One customer to one current customer success record

The `subscription_id` field connects:

- subscriptions.csv
- finance_transactions.csv

The `sales_rep_id` field will later connect sales opportunities to a sales representative dimension.

The `customer_success_manager_id` field will later connect customer accounts to a customer success manager dimension.

Date fields will connect each applicable dataset to a shared calendar dimension.

# Proposed Data Volume

The estimated total project size is approximately:

- 5,000 customer records
- 6,500 subscription records
- 60,000 finance transactions
- 12,000 sales opportunities
- 250 marketing campaigns
- 5,000 customer success records
- 500,000 product usage events
- 40,000 support tickets

Estimated total:

Approximately 628,750 records

This volume is large enough to demonstrate data modeling, SQL transformations, data quality checks, KPI calculations, and Power BI performance considerations while remaining manageable for a portfolio project.

# Dataset Design Decisions

## Shared Account Identifier

All customer-related datasets will use `account_id` to support cross-functional analysis.

## Historical and Snapshot Data

Most datasets contain historical transaction or event records.

The customer success dataset will initially represent a current-state snapshot. A historical snapshot table may be added later if trend analysis is needed.

## Fictional Data

All records will be synthetically generated.

No confidential, personal, or proprietary business information will be used.

## Date Coverage

Finance, sales, subscription, customer, and marketing data will cover four calendar years.

Product usage and support data will cover two years because event-level datasets contain significantly more records.

## Currency

The first version will use United States dollars only.

This prevents currency conversion from distracting from the core business intelligence requirements.

## Customer Accounts

The project will represent B2B customer accounts rather than individual consumers.

One account may have multiple users, subscriptions, support tickets, opportunities, and product events.