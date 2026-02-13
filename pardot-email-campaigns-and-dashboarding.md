# Pardot Email Campaigns & Dashboarding Guide

A practical guide to understanding how Pardot email campaigns work and how to build dashboards that give visibility into marketing performance across Pardot and Salesforce.

---

## Part 1: Pardot Email Campaigns

### How Email Campaigns Work in Pardot

Pardot offers three ways to send emails, each serving a different purpose:

#### 1. List Emails (One-Time Sends)

A single email blast sent to a specific list of prospects at a scheduled time.

**When to use:** Newsletters, announcements, quarterly investor updates, event invitations.

**How it works:**
```
1. Create email content (template or custom)
2. Select recipient list (static or dynamic)
3. Set send date/time
4. Pardot sends to all list members
5. Opens, clicks, bounces tracked automatically
```

**Example in our context:**
- "Q1 2026 Property Performance Report" sent to "NetSuite Investors - All Active" list
- One-time send, scheduled for the first Monday after quarter close

#### 2. Engagement Studio Programs (Drip/Nurture Campaigns)

Multi-step automated sequences that respond to prospect behavior. This is where Pardot's real power lives.

**When to use:** Welcome series, lead nurturing, re-engagement, onboarding flows.

**How it works:**
```
Prospect enters program (trigger or list-based)
    ↓
Step 1: Send Email A
    ↓
Wait 3 days
    ↓
Decision: Did they open Email A?
   ├── YES → Send Email B (next topic)
   └── NO  → Resend Email A with different subject line
    ↓
Wait 5 days
    ↓
Action: Adjust prospect score
    ↓
Step 2: Send Email C
    ↓
... continues based on engagement
```

**Key concepts:**
- **Actions**: Things Pardot does (send email, adjust score, add to list, assign to user)
- **Triggers**: Conditions based on prospect behavior (opened email, clicked link, submitted form)
- **Rules**: Conditions based on prospect data (job title contains "Director", investor status = "Active")
- **Wait steps**: Time delays between steps (hours, days, or business days)

**Example: New Investor Welcome Series**
```
Entry: Investor_Status__c changes to "Active Investor"
    ↓
Day 1: Send "Welcome to Our Investment Community"
    ↓
Day 3: Send "Your Investor Portal and Resources"
    ↓
    Decision: Did they click the portal link?
    ├── YES → Send "Advanced Portal Features" (Day 5)
    └── NO  → Send "Quick Start Guide" (Day 5)
    ↓
Day 10: Send "Meet Your Property Management Team"
    ↓
Day 14: Send "Understanding Your Investment Reporting"
    ↓
End → Add to "Onboarded Investors" list
```

#### 3. Autoresponder Emails (Form Completion Triggers)

Automatically sent when a prospect completes a specific action (usually a form submission).

**When to use:** Form confirmations, content delivery, immediate follow-ups.

**How it works:**
```
Prospect fills out Pardot form (e.g., "Request Property Info")
    ↓
Completion Action triggers autoresponder
    ↓
Email sent immediately with requested content
    ↓
Prospect also added to nurture list for follow-up
```

### Email Building Blocks

#### Templates

Pardot provides two template systems:

| Feature | Classic Builder | Lightning Builder (Drag & Drop) |
|---------|----------------|-------------------------------|
| Ease of use | HTML knowledge needed | No code, visual editor |
| Flexibility | Full HTML control | Limited to pre-built blocks |
| Dynamic content | Supported | Supported |
| Responsive | Manual coding | Automatic |
| Best for | Custom designs | Quick, standard layouts |

#### Personalization (Variable Tags)

Insert prospect-specific data into emails:

```
Hi %%first_name%%,

Your %%primary_property%% investment update is ready.

Investment since: %%investor_since_date%%
Current value: %%total_investment_amount%%

[View Full Report]
```

**Common variable tags:**
- `%%first_name%%` / `%%last_name%%` - Prospect name
- `%%company%%` - Company name
- `%%primary_property%%` - Custom field (mapped from Salesforce)
- `%%property_location%%` - Custom field
- `%%investor_status%%` - Custom field

**Fallback values (important):**
```
Hi %%first_name|Valued Investor%%,
```
If `first_name` is empty, "Valued Investor" is used instead. Always set fallbacks to avoid blank personalization.

#### Dynamic Content

Show different content blocks based on prospect attributes. One email, multiple versions.

**Example:**
```
IF property_location = "Miami":
    Show Miami property photo + local updates

IF property_location = "Orlando":
    Show Orlando property photo + local updates

DEFAULT:
    Show general portfolio update
```

**Setup:** Create dynamic content in Pardot (Marketing > Content > Dynamic Content), then embed in email template using the dynamic content tag.

#### Segmentation (Who Gets What)

**Static Lists:**
- Manually curated or imported
- Don't change unless you add/remove prospects
- Use for: One-off event invitations, imported contact lists

**Dynamic Lists:**
- Auto-populate based on rules (like a saved search)
- Update in real-time as prospect data changes
- Use for: All ongoing campaigns

**Key dynamic lists for our context:**
| List Name | Criteria | Use Case |
|-----------|----------|----------|
| All Active Investors | `NetSuite_Customer__c = TRUE` | Broad investor communications |
| Recent Investors (90 days) | `Investor_Since__c > 90 days ago` AND `NetSuite_Customer__c = TRUE` | Welcome/onboarding campaigns |
| High-Value Investors | `Total_Investment_Amount__c > $500K` AND `NetSuite_Customer__c = TRUE` | VIP offers, early access |
| Miami Investors | `Property_Location__c = "Miami"` AND `NetSuite_Customer__c = TRUE` | Location-specific updates |
| Lapsed Guests (No Stay 12mo) | `Last_Stay_Date__c < 12 months ago` AND no future reservation | Win-back campaigns |
| Engaged Prospects | `Pardot Score > 50` | Sales-ready handoff |

### Email Metrics That Matter

When Pardot sends an email, it tracks:

| Metric | What It Measures | Healthy Benchmark |
|--------|-----------------|-------------------|
| **Delivery Rate** | Emails that reached inbox (not bounced) | > 95% |
| **Open Rate** | Recipients who opened the email | 20-30% (B2B average) |
| **Click-Through Rate (CTR)** | Recipients who clicked a link | 2-5% |
| **Click-to-Open Rate (CTOR)** | Openers who then clicked | 10-15% |
| **Bounce Rate** | Emails that failed to deliver | < 2% (hard), < 1% (soft) |
| **Unsubscribe Rate** | Recipients who opted out | < 0.5% |
| **Spam Complaint Rate** | Marked as spam | < 0.1% |

**Hard bounce vs Soft bounce:**
- **Hard bounce**: Permanent failure (bad email address, domain doesn't exist). Pardot auto-removes after 1 hard bounce.
- **Soft bounce**: Temporary failure (mailbox full, server down). Pardot retries, removes after 5 consecutive soft bounces.

### Completion Actions

Actions that automatically fire when a prospect interacts with an email:

**On email open:**
- Adjust score (+5)
- Add to "Engaged" list

**On link click:**
- Adjust score (+10)
- Notify assigned user (sales rep)
- Add to specific nurture campaign
- Change prospect field value

**On unsubscribe:**
- Remove from all active campaigns
- Sync opt-out to Salesforce
- Notify marketing ops

### A/B Testing

Test two versions of an email to find what works:

**What you can test:**
- Subject line (most common and impactful)
- From name/address
- Email content/layout
- Call-to-action text
- Send time

**How it works:**
```
Total list: 1,000 prospects
    ↓
Test group A (15%): 150 prospects get Version A
Test group B (15%): 150 prospects get Version B
    ↓
Wait 24-48 hours
    ↓
Winning version (by open rate or CTR) sent to remaining 700
```

---

## Part 2: Dashboarding

### The Problem: Data Lives in Two Places

This is one of the biggest pain points (covered in detail in `pardot-salesforce-pain-points.md`):

```
Marketing metrics     → Pardot    (email opens, clicks, form fills, scores)
Sales/Revenue metrics → Salesforce (opportunities, deals, revenue, pipeline)

Executive asks: "What's the ROI of our email campaigns?"

Answer requires data from BOTH systems.
```

### Where to Build Dashboards

#### Option 1: Pardot Native Reports

**What's available:**
- Email performance reports (per email, per list)
- Engagement Studio reports (program performance)
- Landing page conversion reports
- Form submission reports
- Lifecycle reports (funnel stages)

**Strengths:**
- No setup required
- Real-time marketing data
- Granular email metrics

**Limitations:**
- Can't show Salesforce revenue/pipeline data
- Limited customization
- Can't combine with sales metrics
- Export to CSV for further analysis

**Key Pardot reports to use:**

| Report | Where to Find | What It Shows |
|--------|--------------|---------------|
| Email Performance | Marketing > Emails > [email] > Report | Opens, clicks, bounces per email |
| List Email Report | Marketing > Emails > Sent | All sent emails with metrics |
| Engagement Studio | Automation > Engagement Studio > [program] | Step-by-step funnel performance |
| Lifecycle Report | Reports > Lifecycle | Prospects by funnel stage |
| Campaign Report | Marketing > Campaigns > [campaign] | All activities tied to a campaign |

#### Option 2: Salesforce Reports & Dashboards (Recommended Starting Point)

**What's available:**
- Standard report builder with drag-and-drop
- Dashboard component types: charts, gauges, tables, metrics
- Scheduled email delivery of dashboards
- Cross-object reporting (Contacts + Campaigns + Opportunities)

**Strengths:**
- Combines Pardot engagement data with Salesforce revenue data
- Shareable with entire organization
- Scheduled delivery
- Familiar Salesforce UI

**Limitations:**
- Some Pardot data requires B2B Marketing Analytics (additional license)
- Real-time refresh requires manual click
- Complex reports can hit governor limits

**How Pardot data gets into Salesforce for reporting:**
```
Pardot tracks email open
    ↓
Sync creates Activity/Task in Salesforce
    ↓
Pardot score updates via custom field (pi__score__c)
    ↓
Campaign Member status updates
    ↓
Salesforce reports can now query this data
```

#### Option 3: B2B Marketing Analytics (Pardot Lightning Dashboards)

**What it is:** Pre-built analytics dashboards that combine Pardot and Salesforce data natively. Available in the Pardot Lightning app within Salesforce.

**Key dashboards included:**
- Marketing Manager Dashboard
- Pipeline Dashboard
- Engagement Dashboard
- Account-Based Marketing Dashboard
- Lifecycle Dashboard

**Strengths:**
- True unified view of marketing + sales data
- Pre-built, minimal configuration
- Einstein AI insights
- Multi-touch attribution

**Limitations:**
- Requires additional Pardot license tier (Plus or Advanced)
- Pre-built templates may not match your exact needs
- Customization requires CRM Analytics knowledge

#### Option 4: External BI Tools (Tableau, Power BI, Looker)

**When to use:** When you need advanced analytics, custom visualizations, or data from more than two systems (e.g., OPERA + Salesforce + Pardot + NetSuite all in one view).

**How it works:**
```
Pardot API → Extract email metrics
Salesforce API → Extract CRM data
NetSuite API → Extract financial data
OPERA API → Extract stay data
    ↓
Data warehouse (Snowflake, BigQuery, etc.)
    ↓
BI tool connects and visualizes
```

**Strengths:**
- Complete cross-system visibility
- Advanced visualizations
- Historical trending
- Custom calculations

**Limitations:**
- Significant setup cost and time
- Requires data engineering resources
- Data freshness depends on ETL frequency
- Additional licensing costs

### Building Your Dashboards

#### Dashboard 1: Email Campaign Performance (Pardot-Focused)

**Audience:** Marketing team, weekly review

**Components:**

| Component | Type | Data Source | Metric |
|-----------|------|------------|--------|
| Emails Sent (30 days) | Metric | Pardot | Total sends |
| Average Open Rate | Gauge | Pardot | % opens / sends |
| Average CTR | Gauge | Pardot | % clicks / sends |
| Top 5 Emails by Opens | Horizontal Bar | Pardot | Open count per email |
| Bounce Rate Trend | Line Chart | Pardot | Bounce % over time |
| Unsubscribe Trend | Line Chart | Pardot | Unsub % over time |
| Email Performance Table | Table | Pardot | All emails with key metrics |
| List Growth Over Time | Line Chart | Pardot | Prospect count by list |

**How to build in Pardot:**
1. Go to Reports in Pardot
2. Most of these are available as standard reports
3. Export to CSV and combine in Google Sheets/Excel for a unified view
4. Or use B2B Marketing Analytics if licensed

#### Dashboard 2: Lead Generation & Funnel (Salesforce + Pardot)

**Audience:** Marketing leadership, sales leadership

**Components:**

| Component | Type | Data Source | Metric |
|-----------|------|------------|--------|
| New Prospects This Month | Metric | Salesforce | New Lead/Contact count |
| MQL Count | Metric | Salesforce | Leads reaching score threshold |
| MQL → SQL Conversion | Gauge | Salesforce | % of MQLs accepted by sales |
| Lead Source Breakdown | Pie Chart | Salesforce | Leads by source |
| Funnel by Stage | Funnel Chart | Salesforce | Prospects at each lifecycle stage |
| Campaign Influence on Pipeline | Bar Chart | Salesforce | Pipeline $ by campaign |
| Avg Days to MQL | Metric | Salesforce | Time from creation to MQL |
| Top Performing Campaigns | Table | Salesforce | Campaigns ranked by influenced pipeline |

**How to build in Salesforce:**
1. Go to Reports tab
2. Create individual reports for each component
3. Go to Dashboards tab > New Dashboard
4. Add components, linking each to a report
5. Set refresh schedule (daily recommended)
6. Share with relevant users/groups

**Key report types to use:**
- "Campaigns with Campaign Members" - for campaign performance
- "Contacts & Accounts" with filters - for investor segmentation
- "Leads with Campaign History" - for lead gen attribution

#### Dashboard 3: Investor Marketing Performance (Cross-System)

**Audience:** Executive team, investor relations

**Components:**

| Component | Type | Data Source | Metric |
|-----------|------|------------|--------|
| Total Active Investors | Metric | Salesforce | Count where NetSuite_Customer__c = TRUE |
| Investor Email Engagement | Gauge | Pardot | Avg open rate for investor lists |
| Investor Campaigns Sent (QTD) | Metric | Pardot | Emails sent to investor segments |
| Engagement by Property Location | Bar Chart | Salesforce + Pardot | Open/click rates by location |
| New Investors This Quarter | Metric | Salesforce | Recent investor count |
| Investor Satisfaction Score | Gauge | Survey data | NPS or CSAT |
| Top Engaged Investors | Table | Pardot | Highest scoring investor prospects |
| Campaign ROI | Table | Salesforce | Revenue influenced vs. campaign cost |

**Building this requires:** Either B2B Marketing Analytics (native) or exporting data from both systems into a spreadsheet/BI tool.

#### Dashboard 4: Integration Health (Ops/Admin)

**Audience:** Marketing ops, Salesforce admin

**Components:**

| Component | Type | Data Source | Metric |
|-----------|------|------------|--------|
| Sync Status | Indicator | Salesforce | Connector health check |
| Records Synced (24h) | Metric | Integration logs | Count of synced records |
| Sync Errors (24h) | Metric | Integration logs | Error count |
| API Usage | Gauge | Salesforce | % of daily API limit consumed |
| Unmatched Records | Metric | Integration logs | NetSuite records with no SF match |
| Data Quality Score | Gauge | Salesforce | % of records with complete fields |
| Bounce Rate Trend | Line Chart | Pardot | Email deliverability health |
| List Hygiene Score | Gauge | Pardot | % of active, valid prospects |

### Pardot Scoring & Grading on Dashboards

Understanding what the numbers mean is critical for dashboarding:

**Score (Engagement):**
```
Activity                    Points
──────────────────────────────────
Email open                  +1
Email click                 +3
Form submission             +50
Landing page view           +5
Website page view           +1
File download               +5
Custom redirect click       +3
Webinar registration        +10
Webinar attended            +25
Unsubscribe                 -10 to -100
Inactive 30 days            Decay -10%
```

**Grade (Fit):**
```
Criteria                    Impact
──────────────────────────────────
Job title match             +1/3 grade
Company size match          +1/3 grade
Industry match              +1/3 grade
Location match              +1/3 grade
Missing email               -1/3 grade
Personal email domain       -2/3 grade
```

**Dashboard tip:** Don't just show the raw score. Show score distribution (histogram) and score trends over time. A rising average score means your campaigns are driving engagement. A flat or declining score means content isn't resonating.

### Connected Campaigns (Important for Reporting)

Pardot campaigns and Salesforce campaigns can be linked via "Connected Campaigns." This is critical for unified reporting.

**Without Connected Campaigns:**
```
Pardot: "Q1 Webinar" campaign tracks email sends, opens, clicks
Salesforce: Separate "Q1 Webinar" campaign tracks leads, opportunities, revenue
No connection between the two → Manual data merging for ROI
```

**With Connected Campaigns:**
```
One campaign object shared between both systems
Pardot engagement data + Salesforce revenue data = unified view
Campaign Influence reports show pipeline and revenue impact
Multi-touch attribution becomes possible
```

**How to enable:**
1. Salesforce Setup > Pardot > Enable "Connected Campaigns"
2. Align campaign structures in both systems
3. Link existing campaigns or create new connected ones
4. Campaign Member statuses bridge both systems

### Multi-Touch Attribution

Once Connected Campaigns are enabled, you can answer: "Which campaigns contributed to this deal?"

**Attribution Models Available:**

| Model | How It Works | Best For |
|-------|-------------|----------|
| First Touch | 100% credit to first campaign | Understanding lead generation sources |
| Last Touch | 100% credit to last campaign before conversion | Understanding what closes deals |
| Even Distribution | Equal credit to all campaigns touched | Fair overview of all touchpoints |
| Custom Weighted | You define weights per stage | Matching your specific sales process |

**Example:**
```
Prospect journey:
1. Clicked Google Ad → Landing Page (First Touch)
2. Attended Webinar
3. Opened 3 nurture emails
4. Downloaded Property Brochure
5. Requested Consultation → Became Opportunity (Last Touch)
6. Attended Property Tour
7. Signed Contract → Closed Won

First Touch attribution: Google Ad gets 100% credit
Last Touch attribution: Consultation Request gets 100% credit
Even Distribution: Each of 7 touchpoints gets ~14% credit
```

### Practical Tips for Dashboarding

1. **Start with 3-5 metrics, not 30.** More components = more noise. Focus on what drives decisions.

2. **Match dashboards to audiences.** Executives want ROI and pipeline. Marketing wants engagement rates. Ops wants system health. Don't mix these.

3. **Set up scheduled dashboard delivery.** In Salesforce: Dashboard > Subscribe > Choose frequency and recipients. Stakeholders get a snapshot in their inbox without logging in.

4. **Use conditional highlighting.** Green/yellow/red indicators based on thresholds (e.g., open rate > 25% = green, 15-25% = yellow, < 15% = red).

5. **Always include a date range filter.** "Last 30 days" vs "This Quarter" vs "Year to Date" changes the story completely.

6. **Document your metric definitions.** "MQL" means different things to different people. Write it down and put it on the dashboard.

7. **Review and prune monthly.** Dashboards grow stale. If no one looks at a component, remove it.

---

## Quick Reference: Campaign Setup Checklist

```
[ ] Define campaign goal and target audience
[ ] Create or verify dynamic list with correct criteria
[ ] Build email content with personalization and fallbacks
[ ] Set up A/B test (at minimum, test subject lines)
[ ] Configure completion actions (score adjustments, notifications)
[ ] Set up Connected Campaign in Salesforce
[ ] Test with internal seed list first
[ ] Schedule send (consider time zones)
[ ] Monitor delivery in first 1-2 hours (watch for bounce spikes)
[ ] Review results after 48 hours
[ ] Update dashboard with campaign results
[ ] Document learnings for next campaign
```

## Quick Reference: Dashboard Planning Template

```
Dashboard Name: _________________________
Audience: _______________________________
Refresh Frequency: ______________________
Data Sources: ___________________________

Components:
1. Metric: _____________ | Source: _______ | Threshold: _______
2. Metric: _____________ | Source: _______ | Threshold: _______
3. Metric: _____________ | Source: _______ | Threshold: _______
4. Metric: _____________ | Source: _______ | Threshold: _______
5. Metric: _____________ | Source: _______ | Threshold: _______

Distribution: ___________________________
Review Cycle: ___________________________
```

---

## Related Documents

- [Salesforce and Pardot Overview](salesforce-and-pardot-overview.md) - Foundational overview of both platforms
- [Pardot-Salesforce Integration](pardot-salesforce-integration.md) - Technical sync details
- [Pardot-Salesforce Pain Points](pardot-salesforce-pain-points.md) - Known issues and workarounds
- [Pardot-Salesforce Monitoring](pardot-salesforce-monitoring.md) - Operational monitoring strategy
- [NetSuite-Salesforce-Pardot Investor Targeting](netsuite-salesforce-pardot-investor-targeting.md) - Investor email campaign integration
- [OPERA-Salesforce-Pardot Integration](opera-salesforce-pardot-integration.md) - Hospitality use cases and workflows
