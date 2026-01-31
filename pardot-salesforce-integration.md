# Pardot & Salesforce: Technical Integration Guide

Understanding the two-way data sync between Pardot (Marketing Cloud Account Engagement) and Salesforce CRM.

## Overview

**Pardot** (now called **Marketing Cloud Account Engagement**) is Salesforce's B2B marketing automation platform.

### The Relationship

1. **Owned by Salesforce**
   - Salesforce acquired Pardot in 2012 for $95.5 million
   - In 2022, Salesforce rebranded it as "Marketing Cloud Account Engagement"
   - Still commonly called "Pardot" by many users

2. **Integration**
   - Pardot integrates natively with Salesforce CRM
   - Data flows between the two platforms:
     - **Leads & Contacts** sync from Salesforce to Pardot
     - **Prospect activities** (email opens, clicks, form submissions) sync from Pardot to Salesforce
     - **Campaigns** can be tracked in both systems

3. **Different Purposes**
   - **Salesforce CRM**: Sales team tool for managing customer relationships, deals, contacts
   - **Pardot/MCAE**: Marketing team tool for email campaigns, lead nurturing, lead scoring, landing pages

### Typical Workflow

```
Marketing (Pardot) → Generates & nurtures leads → Hands off to Sales (Salesforce)
                                                          ↓
                                              Sales closes deals, updates records
                                                          ↓
                                              Data syncs back to Pardot
```

---

## Technical Architecture

```
┌─────────────────┐         ┌─────────────────┐
│   Salesforce    │◄────────►│    Pardot       │
│      CRM        │  Native  │     (MCAE)      │
│                 │ Connector│                 │
└─────────────────┘         └─────────────────┘
```

## How the Two-Way Sync Works

### 1. Salesforce Connector (Core Component)

Pardot uses a **native Salesforce Connector** that establishes the integration:
- Not a third-party tool - built by Salesforce specifically for this integration
- Uses OAuth 2.0 for authentication
- Maintains persistent connection between the two systems

### 2. Sync Process

#### Initial Setup:
1. Admin installs Pardot package in Salesforce (adds custom fields/objects)
2. Configures the connector with field mappings
3. Sets sync frequency and rules

#### Ongoing Sync:

**Salesforce → Pardot (Lead/Contact sync):**
```
Salesforce detects change (trigger/platform event)
    ↓
Change logged to sync queue
    ↓
Pardot connector polls Salesforce API
    ↓
Retrieves changed records (getUpdated API)
    ↓
Creates/updates Prospect in Pardot
    ↓
Stores Salesforce ID as reference (FID)
```

**Pardot → Salesforce (Activity/Prospect data):**
```
Prospect takes action (email click, form fill, etc.)
    ↓
Pardot logs activity
    ↓
Sync process runs (scheduled or real-time)
    ↓
Pardot calls Salesforce REST/SOAP API
    ↓
Updates Lead/Contact record + creates Activity records
    ↓
Salesforce processes update
```

### 3. Technical Components

#### APIs Used:
- **Salesforce REST API** - Modern, JSON-based data operations
- **Salesforce SOAP API** - Legacy XML-based operations (still used for some features)
- **Bulk API** - For large data syncs (>2000 records)
- **Streaming API / Platform Events** - For near-real-time updates

#### Data Model:
```sql
-- Pardot side
Prospect {
  id: 12345,
  email: "user@example.com",
  salesforce_fid: "00Q5e000001AbCd",  -- Foreign ID to Salesforce
  ...
}

-- Salesforce side
Lead/Contact {
  Id: "00Q5e000001AbCd",
  Email: "user@example.com",
  pi__pardot_id__c: "12345",  -- Custom field linking to Pardot
  ...
}
```

### 4. Sync Frequency

**Configurable Intervals:**
- **Real-time** - Some high-priority updates (forms, conversions)
- **Every 2-4 minutes** - Standard sync interval for most data
- **Hourly/Daily** - Bulk operations, cleanup tasks

**Trigger-based:**
- Form submissions trigger immediate sync
- Lead conversions trigger immediate sync
- Batch operations run on schedule

### 5. Conflict Resolution

**"Salesforce is the source of truth" (default):**
```
If same field changed in both systems:
    Salesforce value wins
    Pardot value overwritten
```

**Field-level sync direction:**
- Some fields: Salesforce → Pardot only
- Some fields: Pardot → Salesforce only
- Some fields: Bi-directional (last write wins, or SF wins)

### 6. Field Mapping

Admins configure which fields sync:

```javascript
// Example mapping
{
  "Salesforce.FirstName" → "Pardot.first_name",
  "Salesforce.LastName" → "Pardot.last_name",
  "Salesforce.Email" → "Pardot.email",
  "Pardot.Score" → "Salesforce.pi__score__c",
  "Pardot.Grade" → "Salesforce.pi__grade__c"
}
```

### 7. Activity Sync

Pardot activities become Salesforce Tasks/Activities:

```
Pardot: Email opened
    ↓
Creates Task in Salesforce:
{
  Subject: "Email: [Campaign Name]",
  Type: "Email",
  Status: "Completed",
  WhoId: [Lead/Contact ID]
}
```

### 8. Performance Optimization

**Techniques used:**
- **Delta sync** - Only changed records since last sync
- **Batch processing** - Groups API calls to stay under limits
- **Caching** - Stores frequently accessed data
- **Queue management** - Prioritizes critical updates

**API Limits:**
- Salesforce has daily API call limits (based on edition)
- Pardot connector is optimized to minimize calls
- Uses bulk operations where possible

## Detailed Flow Example

### Example: User Fills Out Pardot Form

```
[User fills Pardot form]
    ↓
1. Form data saved to Pardot database
    ↓
2. Trigger: "New prospect activity"
    ↓
3. Sync job enqueued
    ↓
4. Pardot makes API call to Salesforce:
   POST /services/data/v60.0/sobjects/Lead
   {
     "FirstName": "John",
     "LastName": "Doe",
     "Email": "john@example.com",
     "pi__campaign__c": "12345"
   }
    ↓
5. Salesforce creates/updates Lead
    ↓
6. Salesforce returns Lead ID: "00Q..."
    ↓
7. Pardot stores FID reference
    ↓
8. Prospect and Lead are now linked
```

### Example: Sales Rep Updates Contact in Salesforce

```
[Sales rep updates phone number in Salesforce]
    ↓
1. Salesforce saves change
    ↓
2. Platform event/trigger fires
    ↓
3. Change logged to sync queue
    ↓
4. Pardot connector polls for changes (2-4 min later)
    ↓
5. Pardot retrieves updated Contact via API:
   GET /services/data/v60.0/sobjects/Contact/003...
    ↓
6. Pardot updates corresponding Prospect record
    ↓
7. Phone number now consistent in both systems
```

## Key Technical Takeaways

1. **Not real-time** - Usually 2-4 minute delay (except priority events)
2. **API-based** - Uses standard Salesforce APIs under the hood
3. **Bidirectional** - But with configurable rules and conflict resolution
4. **Record linking** - Uses foreign ID fields to maintain relationships
5. **Governed by limits** - Salesforce API limits apply
6. **Native integration** - Not a third-party middleware solution
7. **Selective sync** - Admins control which fields and records sync

## Common Sync Scenarios

### Scenario 1: New Lead from Pardot Form
1. Prospect fills form on Pardot landing page
2. Pardot creates Prospect record
3. Sync creates Lead in Salesforce (real-time or within 2-4 min)
4. Lead assigned to sales rep via Salesforce assignment rules
5. Lead activities (emails, page views) sync to Salesforce as Tasks

### Scenario 2: Lead Conversion in Salesforce
1. Sales rep converts Lead to Contact/Opportunity in Salesforce
2. Sync detects conversion
3. Pardot Prospect updated with new Contact FID
4. Historical activities remain linked to Prospect
5. Future activities sync to Contact record

### Scenario 3: Email Campaign Engagement
1. Pardot sends marketing email
2. Prospect opens email and clicks link
3. Activity logged in Pardot
4. Sync creates Task records in Salesforce
5. Sales rep sees engagement history on Lead/Contact record

## Troubleshooting Common Issues

### Sync Delays
- **Cause**: High volume, API limits, or connector issues
- **Solution**: Check sync queue, verify API limits not exceeded

### Duplicate Records
- **Cause**: Multiple prospects with same email or poor matching rules
- **Solution**: Configure duplicate matching rules, use email as unique identifier

### Missing Data
- **Cause**: Field mapping not configured or sync direction wrong
- **Solution**: Review field mappings in connector settings

### Sync Errors
- **Cause**: Validation rules, required fields, or permissions
- **Solution**: Check Salesforce validation rules, ensure Pardot user has proper permissions

## Best Practices

1. **Plan field mappings** before enabling sync
2. **Use consistent data standards** (naming, formatting)
3. **Monitor API usage** to avoid hitting limits
4. **Set up error notifications** for sync failures
5. **Document custom field purposes** for both teams
6. **Test in sandbox** before production changes
7. **Regular audits** of sync health and data quality

---

This architecture allows marketing teams to work in Pardot and sales teams to work in Salesforce CRM while maintaining data consistency and visibility across both platforms.
