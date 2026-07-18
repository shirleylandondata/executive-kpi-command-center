# Star Schema Design

## Overview

The Executive KPI Command Center will use a dimensional star schema designed for executive reporting, KPI calculation, trend analysis, and Power BI performance.

The model separates descriptive business information into dimension tables and measurable business activity into fact tables.

The customer account will serve as the central business entity connecting revenue, subscriptions, sales, customer success, product usage, and support activity.

The model will include conformed dimensions that can be reused across multiple fact tables, including:

- Customer
- Date
- Subscription Plan
- Sales Representative
- Customer Success Manager
- Product Feature
- Marketing Campaign

The primary reporting model will contain multiple fact tables rather than combining all business processes into one large table. This approach preserves the natural grain of each dataset and reduces duplication.

# Dimension Tables

## 1. dim_customer

### Business Purpose

Stores descriptive information about each B2B customer account.

### Grain

One row per customer account.

### Surrogate Primary Key

`customer_key`

### Natural Business Key

`account_id`

### Proposed Fields

- customer_key
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
- effective_start_date
- effective_end_date
- current_record_flag

### Source Dataset

`customers.csv`

### Used By

- fact_subscription
- fact_revenue_transaction
- fact_sales_opportunity
- fact_customer_success_snapshot
- fact_product_usage
- fact_support_ticket

### Design Notes

The warehouse will use `customer_key` as the reporting key rather than relying directly on `account_id`.

The `account_id` field remains in the dimension as the source-system business identifier.

The effective-date fields allow the dimension to support historical changes in customer segment, region, account ownership, or customer status.

## 2. dim_date

### Business Purpose

Provides a shared calendar structure for historical, monthly, quarterly, and annual analysis.

### Grain

One row per calendar date.

### Primary Key

`date_key`

### Proposed Key Format

YYYYMMDD

Example:

January 15, 2025 becomes `20250115`.

### Proposed Fields

- date_key
- full_date
- day_number
- day_name
- day_of_week
- week_number
- month_number
- month_name
- month_short_name
- year_month
- quarter_number
- quarter_name
- calendar_year
- fiscal_month
- fiscal_quarter
- fiscal_year
- month_start_date
- month_end_date
- quarter_start_date
- quarter_end_date
- year_start_date
- year_end_date
- weekend_flag

### Source

Generated using Python or SQL.

### Used By

All fact tables containing date-based activity.

### Design Notes

Several fact tables contain more than one date.

For example, the sales opportunity fact includes:

- Created date
- Expected close date
- Actual close date

The model will use role-playing relationships in Power BI so the same date dimension can represent different business dates.

## 3. dim_plan

### Business Purpose

Stores descriptive information about subscription products and pricing plans.

### Grain

One row per subscription plan.

### Primary Key

`plan_key`

### Natural Business Key

`plan_id`

### Proposed Fields

- plan_key
- plan_id
- plan_name
- plan_tier
- billing_frequency
- standard_monthly_price
- included_users
- target_customer_segment
- active_plan_flag

### Source

Derived from `subscriptions.csv` or created as a separate reference file.

### Used By

- fact_subscription
- fact_revenue_transaction

### Proposed Plan Tiers

- Starter
- Growth
- Professional
- Enterprise

## 4. dim_sales_rep

### Business Purpose

Stores descriptive information about sales representatives and sales teams.

### Grain

One row per sales representative.

### Primary Key

`sales_rep_key`

### Natural Business Key

`sales_rep_id`

### Proposed Fields

- sales_rep_key
- sales_rep_id
- sales_rep_name
- sales_team
- sales_region
- manager_name
- hire_date
- annual_quota
- active_rep_flag

### Source

A proposed reference dataset named:

`sales_representatives.csv`

### Used By

- fact_sales_opportunity
- dim_customer

## 5. dim_customer_success_manager

### Business Purpose

Stores descriptive information about customer success managers and assigned account teams.

### Grain

One row per customer success manager.

### Primary Key

`customer_success_manager_key`

### Natural Business Key

`customer_success_manager_id`

### Proposed Fields

- customer_success_manager_key
- customer_success_manager_id
- manager_name
- customer_success_team
- region
- manager_level
- hire_date
- active_manager_flag

### Source

A proposed reference dataset named:

`customer_success_managers.csv`

### Used By

- fact_customer_success_snapshot
- dim_customer

## 6. dim_product_feature

### Business Purpose

Standardizes product events, product features, and core-adoption classifications.

### Grain

One row per defined product feature.

### Primary Key

`product_feature_key`

### Natural Business Key

`feature_id`

### Proposed Fields

- product_feature_key
- feature_id
- feature_name
- feature_category
- event_name
- core_feature_flag
- adoption_weight
- active_feature_flag

### Source

Derived from `product_usage.csv` or created as a separate feature reference file.

### Used By

- fact_product_usage

## 7. dim_marketing_campaign

### Business Purpose

Stores descriptive information about marketing campaigns.

### Grain

One row per marketing campaign.

### Primary Key

`marketing_campaign_key`

### Natural Business Key

`campaign_id`

### Proposed Fields

- marketing_campaign_key
- campaign_id
- campaign_name
- campaign_type
- campaign_channel
- campaign_owner
- campaign_start_date
- campaign_end_date
- campaign_status

### Source Dataset

`marketing_campaigns.csv`

### Used By

- fact_marketing_campaign
- fact_sales_opportunity, where campaign attribution is available

## 8. dim_sales_stage

### Business Purpose

Provides a standardized sales funnel hierarchy.

### Grain

One row per sales opportunity stage.

### Primary Key

`sales_stage_key`

### Proposed Fields

- sales_stage_key
- stage_name
- stage_order
- stage_category
- open_pipeline_flag
- closed_won_flag
- closed_lost_flag
- default_probability

### Proposed Stage Values

| Stage Order | Stage Name | Stage Category |
|---:|---|---|
| 1 | Prospecting | Open |
| 2 | Qualification | Open |
| 3 | Discovery | Open |
| 4 | Proposal | Open |
| 5 | Negotiation | Open |
| 6 | Closed Won | Closed Won |
| 7 | Closed Lost | Closed Lost |

# Fact Tables

## 1. fact_subscription

### Business Purpose

Tracks subscription contracts, recurring revenue, renewals, cancellations, and subscription status.

### Grain

One row per subscription contract.

### Primary Key

`subscription_fact_key`

### Degenerate Business Key

`subscription_id`

### Foreign Keys

- customer_key
- plan_key
- subscription_start_date_key
- subscription_end_date_key
- cancellation_date_key

### Proposed Measures and Attributes

- subscription_fact_key
- subscription_id
- customer_key
- plan_key
- subscription_start_date_key
- subscription_end_date_key
- cancellation_date_key
- monthly_recurring_revenue
- annual_recurring_revenue
- subscription_status
- payment_status
- auto_renewal_flag
- active_subscription_flag
- canceled_subscription_flag

### Source Dataset

`subscriptions.csv`

### KPIs Supported

- MRR
- ARR
- Active subscriptions
- Renewal rate
- Customer churn rate
- Churned revenue
- Average revenue per account

## 2. fact_revenue_transaction

### Business Purpose

Tracks individual financial and recurring revenue movements.

### Grain

One row per financial transaction.

### Primary Key

`revenue_transaction_key`

### Degenerate Business Key

`transaction_id`

### Foreign Keys

- customer_key
- plan_key
- transaction_date_key

### Proposed Measures and Attributes

- revenue_transaction_key
- transaction_id
- subscription_id
- customer_key
- plan_key
- transaction_date_key
- transaction_type
- invoice_amount
- payment_amount
- recurring_revenue_amount
- adjustment_amount
- net_transaction_amount
- currency
- payment_status

### Source Dataset

`finance_transactions.csv`

### KPIs Supported

- New MRR
- Expansion MRR
- Contraction MRR
- Churned MRR
- Revenue growth
- Revenue movements
- Payments received
- Outstanding invoice value

The subscription fact represents the current or contractual subscription state.

The revenue transaction fact represents activity and revenue movement over time.

These tables must remain separate because they have different grains and answer different business questions.

## 3. fact_sales_opportunity

### Business Purpose

Tracks sales opportunities across the sales pipeline.

### Grain

One row per sales opportunity.

### Primary Key

`sales_opportunity_key`

### Degenerate Business Key

`opportunity_id`

### Foreign Keys

- customer_key
- sales_rep_key
- sales_stage_key
- marketing_campaign_key
- created_date_key
- expected_close_date_key
- actual_close_date_key

### Proposed Measures and Attributes

- sales_opportunity_key
- opportunity_id
- customer_key
- sales_rep_key
- sales_stage_key
- marketing_campaign_key
- created_date_key
- expected_close_date_key
- actual_close_date_key
- opportunity_value
- opportunity_status
- lead_source
- loss_reason
- days_in_sales_cycle
- open_opportunity_flag
- won_opportunity_flag
- lost_opportunity_flag

### Source Dataset

`sales_opportunities.csv`

### KPIs Supported

- Open pipeline value
- Pipeline coverage
- Win rate
- Average deal size
- Sales cycle length
- Funnel conversion
- Closed-won revenue
- Quota attainment

## 4. fact_marketing_campaign

### Business Purpose

Tracks summarized marketing campaign performance and financial results.

### Grain

One row per marketing campaign.

### Primary Key

`marketing_campaign_fact_key`

### Foreign Keys

- marketing_campaign_key
- campaign_start_date_key
- campaign_end_date_key

### Proposed Measures

- marketing_campaign_fact_key
- marketing_campaign_key
- campaign_start_date_key
- campaign_end_date_key
- marketing_spend
- leads_generated
- marketing_qualified_leads
- sales_qualified_leads
- opportunities_created
- customers_acquired
- attributed_revenue

### Source Dataset

`marketing_campaigns.csv`

### KPIs Supported

- Cost per lead
- Cost per acquisition
- Campaign ROI
- Lead conversion rate
- Marketing-qualified lead conversion
- Marketing-sourced pipeline
- Attributed revenue

## 5. fact_customer_success_snapshot

### Business Purpose

Tracks customer health, account risk, renewal status, and customer success activity.

### Grain

One row per customer account per snapshot date.

### Primary Key

`customer_success_snapshot_key`

### Foreign Keys

- customer_key
- customer_success_manager_key
- snapshot_date_key
- renewal_date_key
- last_contact_date_key

### Proposed Measures and Attributes

- customer_success_snapshot_key
- customer_key
- customer_success_manager_key
- snapshot_date_key
- renewal_date_key
- last_contact_date_key
- health_score
- risk_status
- lifecycle_stage
- open_success_tasks
- expansion_opportunity_flag
- days_until_renewal
- days_since_last_contact
- at_risk_flag

### Source Dataset

`customer_success.csv`

### KPIs Supported

- Average customer health score
- At-risk customers
- Critical-risk customers
- Upcoming renewals
- Expansion candidates
- Customer coverage
- Days since last customer contact

## 6. fact_product_usage

### Business Purpose

Tracks product events, user activity, feature usage, and customer engagement.

### Grain

One row per product usage event.

### Primary Key

`product_usage_key`

### Degenerate Business Keys

- usage_event_id
- user_id
- session_id

### Foreign Keys

- customer_key
- product_feature_key
- event_date_key

### Proposed Measures and Attributes

- product_usage_key
- usage_event_id
- customer_key
- product_feature_key
- event_date_key
- user_id
- session_id
- usage_count
- active_user_flag
- core_feature_flag
- valid_customer_event_flag

### Source Dataset

`product_usage.csv`

### KPIs Supported

- Monthly active users
- Monthly active accounts
- Product adoption rate
- Core feature adoption
- Feature usage
- Usage frequency
- Engagement score
- Dormant accounts

## 7. fact_support_ticket

### Business Purpose

Tracks customer support demand, resolution performance, escalation activity, and satisfaction.

### Grain

One row per support ticket.

### Primary Key

`support_ticket_key`

### Degenerate Business Key

`ticket_id`

### Foreign Keys

- customer_key
- created_date_key
- resolved_date_key

### Proposed Measures and Attributes

- support_ticket_key
- ticket_id
- customer_key
- created_date_key
- resolved_date_key
- ticket_status
- ticket_priority
- ticket_category
- escalation_flag
- satisfaction_score
- resolution_hours
- open_ticket_flag
- resolved_ticket_flag

### Source Dataset

`support_tickets.csv`

### KPIs Supported

- Open support tickets
- Average resolution time
- Escalation rate
- Customer satisfaction
- Support volume
- Tickets per customer
- Support-related customer risk

# Star Schema Summary

## Conformed Dimensions

The following dimensions are shared across multiple fact tables:

- dim_customer
- dim_date
- dim_plan
- dim_sales_rep
- dim_customer_success_manager
- dim_product_feature
- dim_marketing_campaign
- dim_sales_stage

## Fact Tables

The model contains the following fact tables:

- fact_subscription
- fact_revenue_transaction
- fact_sales_opportunity
- fact_marketing_campaign
- fact_customer_success_snapshot
- fact_product_usage
- fact_support_ticket

## Central Business Entity

The central business entity is the customer account.

`dim_customer` connects the primary customer-facing business processes:

- Subscriptions
- Revenue
- Sales
- Customer success
- Product engagement
- Customer support

This structure enables cross-functional analysis without combining business processes that have different grains.

# Relationship Matrix

| Fact Table | Customer | Date | Plan | Sales Rep | CSM | Product Feature | Marketing Campaign | Sales Stage |
|---|---|---|---|---|---|---|---|---|
| fact_subscription | Yes | Yes | Yes | No | No | No | No | No |
| fact_revenue_transaction | Yes | Yes | Yes | No | No | No | No | No |
| fact_sales_opportunity | Yes | Yes | No | Yes | No | No | Yes | Yes |
| fact_marketing_campaign | No | Yes | No | No | No | No | Yes | No |
| fact_customer_success_snapshot | Yes | Yes | No | No | Yes | No | No | No |
| fact_product_usage | Yes | Yes | No | No | No | Yes | No | No |
| fact_support_ticket | Yes | Yes | No | No | No | No | No | No |

# Conceptual Star Schema

```text
                             dim_plan
                                |
                                |
dim_date ---- fact_subscription ---- dim_customer
                                |
                                |
                  fact_revenue_transaction


dim_date ---- fact_sales_opportunity ---- dim_customer
                         |           |
                         |           |
                  dim_sales_rep   dim_sales_stage
                         |
                         |
               dim_marketing_campaign


dim_date ---- fact_customer_success_snapshot ---- dim_customer
                              |
                              |
              dim_customer_success_manager


dim_date ---- fact_product_usage ---- dim_customer
                         |
                         |
               dim_product_feature


dim_date ---- fact_support_ticket ---- dim_customer


dim_date ---- fact_marketing_campaign ---- dim_marketing_campaign

# Dashboard-to-Fact Mapping

| Dashboard Page | Primary Fact Tables |
|---|---|
| Executive Overview | All fact tables through shared dimensions |
| Revenue Performance | fact_subscription, fact_revenue_transaction |
| Customer Health | fact_customer_success_snapshot, fact_subscription, fact_product_usage, fact_support_ticket |
| Sales Performance | fact_sales_opportunity |
| Product Adoption | fact_product_usage |
| Marketing Performance | fact_marketing_campaign, fact_sales_opportunity |
| Data Quality | Data quality audit tables created during Phase 3 |

# Key Design Decisions

## Multiple Fact Tables

The project uses multiple fact tables because revenue transactions, subscriptions, sales opportunities, product events, support tickets, and customer health records occur at different levels of detail.

Combining them into one table would create duplicated values and inaccurate KPI calculations.

## Customer as a Conformed Dimension

Customer information will be standardized in `dim_customer` and reused across all customer-related fact tables.

This makes it possible to compare revenue, retention, product engagement, sales activity, and support demand by customer segment, region, and industry.

## Shared Date Dimension

A single `dim_date` table will support consistent month, quarter, year, and fiscal-period analysis.

The same date table may be used multiple times within one fact table through role-playing relationships.

## Customer Success Snapshot Design

Customer success data will be modeled as a periodic snapshot fact table.

The first generated dataset may contain only one snapshot, but the schema will support additional monthly snapshots later.

## Marketing Campaign Grain

The initial marketing dataset is aggregated at the campaign level.

A future version could introduce lead-level or campaign-touch attribution data, but that complexity is outside the first project scope.

## User Dimension Exclusion

The first version will retain `user_id` inside the product usage fact as a degenerate identifier.

A separate user dimension will not be created unless user-level segmentation becomes a reporting requirement.