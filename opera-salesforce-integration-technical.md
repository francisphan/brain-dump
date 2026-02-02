# OPERA Cloud to Salesforce Integration - Technical Implementation

## Overview

This document provides technical implementation details for integrating OPERA Cloud (Oracle Hospitality Property Management System) with Salesforce CRM to automatically sync guest/client data.

## OPERA Cloud Integration Capabilities

### 1. OPERA Cloud APIs

OPERA Cloud provides comprehensive REST APIs for data access and integration:

**Base API Structure:**
- **API Version:** OXI (OPERA Xchange Interface) v5 / REST API v1
- **Authentication:** OAuth 2.0
- **Format:** JSON
- **Rate Limits:** Varies by subscription tier (typically 100-1000 requests/minute)

**Key API Endpoints:**

```
GET    /rsv/v1/hotels/{hotelId}/reservations
GET    /crm/v1/hotels/{hotelId}/profiles
POST   /crm/v1/profiles
PUT    /crm/v1/profiles/{profileId}
GET    /crm/v1/profiles/{profileId}
```

**API Documentation:** https://docs.oracle.com/en/industries/hospitality/integration-platform/

### 2. OPERA Cloud Business Events (Event-Based Hooks)

OPERA Cloud supports **Business Events** - event-driven notifications that can trigger external integrations.

#### Available Business Events

**Profile/Guest Events:**
- `ProfileCreated` - New guest profile created
- `ProfileUpdated` - Guest profile information changed
- `ProfileMerged` - Duplicate profiles merged
- `GuestStayCreated` - New reservation created
- `GuestCheckIn` - Guest checked in
- `GuestCheckOut` - Guest checked out

**Reservation Events:**
- `ReservationCreated`
- `ReservationModified`
- `ReservationCancelled`
- `ReservationConfirmed`

**Membership Events:**
- `MembershipEnrolled`
- `MembershipUpgraded`
- `LoyaltyPointsEarned`

#### Event Payload Structure

When an event fires, OPERA Cloud sends a payload:

```json
{
  "eventType": "ProfileCreated",
  "hotelId": "HOTEL001",
  "timestamp": "2026-02-02T10:30:00Z",
  "profileId": "12345678",
  "profile": {
    "profileId": "12345678",
    "nameInfo": {
      "givenName": "John",
      "surname": "Smith",
      "middleName": "",
      "nameTitle": "Mr."
    },
    "contactInfo": {
      "emailAddress": "john.smith@email.com",
      "phoneNumber": "+1-555-123-4567",
      "mobileNumber": "+1-555-987-6543"
    },
    "addressInfo": {
      "addressLine1": "123 Main Street",
      "addressLine2": "Apt 4B",
      "city": "Miami",
      "state": "FL",
      "postalCode": "33101",
      "country": "USA"
    },
    "preferences": {
      "roomType": "King",
      "smokingPreference": "NonSmoking",
      "language": "EN"
    },
    "vipStatus": "Gold",
    "membershipLevel": "Elite",
    "createdDateTime": "2026-02-02T10:30:00Z",
    "lastModifiedDateTime": "2026-02-02T10:30:00Z"
  }
}
```

### 3. OHIP (Oracle Hospitality Integration Platform)

**OHIP** is Oracle's native integration platform specifically designed for hospitality integrations.

**Features:**
- Pre-built connectors for common integrations
- Event streaming capabilities
- Message queue management
- Transformation and mapping tools
- Error handling and retry logic

**Architecture:**
```
OPERA Cloud → OHIP Event Bus → External Endpoint (Webhook)
                              → Message Queue
                              → Integration Flow
```

## Technical Implementation Approaches

### Approach 1: OPERA Cloud Business Events → Webhook → Middleware → Salesforce

This is the **recommended approach** for real-time, event-driven integration.

#### Architecture

```
OPERA Cloud (Business Event Triggered)
    ↓
Webhook Endpoint (Your middleware or integration platform)
    ↓
Processing & Transformation
    ↓
Salesforce API (Create/Update Contact)
```

#### Implementation Steps

**Step 1: Configure OPERA Cloud Business Events**

1. **Access OPERA Cloud Console**
   - Navigate to Integration → Business Events
   - Select event types to subscribe to:
     - ProfileCreated
     - ProfileUpdated
     - GuestCheckIn (optional)

2. **Configure Webhook Endpoint**
   ```
   Webhook URL: https://your-middleware.com/opera-webhook
   Authentication: Bearer token or API key
   Content-Type: application/json
   Retry Policy: 3 attempts with exponential backoff
   ```

3. **Test Event Delivery**
   - OPERA Cloud provides test event functionality
   - Send test payload to verify connectivity
   - Check webhook logs for successful receipt

**Step 2: Build Webhook Receiver (Middleware)**

You can build this in several ways:

#### Option A: Using Integration Platform (Celigo/Boomi/Workato)

**Celigo Example:**

1. **Create Integration Flow**
   - Name: "OPERA to Salesforce Profile Sync"
   - Trigger: Webhook (expose endpoint)

2. **Configure Webhook Trigger**
   ```javascript
   // Celigo will generate webhook URL
   // Example: https://api.celigo.com/webhook/abc123

   // Authentication: API Key or Bearer Token
   // Configure in Celigo settings
   ```

3. **Add Transformation Logic**
   ```javascript
   // Map OPERA fields to Salesforce fields
   function mapOperaToSalesforce(operaProfile) {
     return {
       FirstName: operaProfile.profile.nameInfo.givenName,
       LastName: operaProfile.profile.nameInfo.surname,
       Email: operaProfile.profile.contactInfo.emailAddress,
       Phone: operaProfile.profile.contactInfo.phoneNumber,
       MobilePhone: operaProfile.profile.contactInfo.mobileNumber,
       MailingStreet: operaProfile.profile.addressInfo.addressLine1,
       MailingCity: operaProfile.profile.addressInfo.city,
       MailingState: operaProfile.profile.addressInfo.state,
       MailingPostalCode: operaProfile.profile.addressInfo.postalCode,
       MailingCountry: operaProfile.profile.addressInfo.country,
       OPERA_Profile_ID__c: operaProfile.profileId,
       Guest_VIP_Status__c: operaProfile.profile.vipStatus,
       Guest_Membership_Level__c: operaProfile.profile.membershipLevel,
       Lead_Source: 'OPERA PMS'
     };
   }
   ```

4. **Add Salesforce Lookup/Upsert**
   - Lookup existing Contact by Email or OPERA_Profile_ID__c
   - If exists: Update
   - If not exists: Create new Contact

5. **Error Handling**
   - Log failed integrations
   - Send alerts for failures
   - Implement retry logic

#### Option B: Custom Webhook Receiver (Node.js Example)

```javascript
// server.js - Express.js webhook receiver
const express = require('express');
const jsforce = require('jsforce');
const app = express();

app.use(express.json());

// Salesforce connection
const conn = new jsforce.Connection({
  oauth2: {
    clientId: process.env.SF_CLIENT_ID,
    clientSecret: process.env.SF_CLIENT_SECRET,
    redirectUri: process.env.SF_REDIRECT_URI
  },
  instanceUrl: process.env.SF_INSTANCE_URL,
  accessToken: process.env.SF_ACCESS_TOKEN
});

// Webhook endpoint to receive OPERA events
app.post('/opera-webhook', async (req, res) => {
  try {
    const operaEvent = req.body;

    // Verify webhook authenticity (API key or signature)
    const apiKey = req.headers['x-api-key'];
    if (apiKey !== process.env.OPERA_WEBHOOK_SECRET) {
      return res.status(401).json({ error: 'Unauthorized' });
    }

    console.log('Received OPERA event:', operaEvent.eventType);

    // Process based on event type
    switch(operaEvent.eventType) {
      case 'ProfileCreated':
      case 'ProfileUpdated':
        await syncProfileToSalesforce(operaEvent);
        break;
      case 'GuestCheckIn':
        await handleGuestCheckIn(operaEvent);
        break;
      default:
        console.log('Unhandled event type:', operaEvent.eventType);
    }

    // Acknowledge receipt
    res.status(200).json({
      success: true,
      message: 'Event processed successfully'
    });

  } catch (error) {
    console.error('Error processing webhook:', error);
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
});

// Function to sync OPERA profile to Salesforce
async function syncProfileToSalesforce(operaEvent) {
  const profile = operaEvent.profile;

  // Map OPERA data to Salesforce Contact
  const salesforceContact = {
    FirstName: profile.nameInfo.givenName,
    LastName: profile.nameInfo.surname,
    Email: profile.contactInfo.emailAddress,
    Phone: profile.contactInfo.phoneNumber,
    MobilePhone: profile.contactInfo.mobileNumber,
    MailingStreet: profile.addressInfo.addressLine1,
    MailingCity: profile.addressInfo.addressInfo.city,
    MailingState: profile.addressInfo.state,
    MailingPostalCode: profile.addressInfo.postalCode,
    MailingCountry: profile.addressInfo.country,
    OPERA_Profile_ID__c: profile.profileId,
    Guest_VIP_Status__c: profile.vipStatus,
    Guest_Membership_Level__c: profile.membershipLevel,
    LeadSource: 'OPERA PMS',
    Last_Stay_Date__c: profile.lastStayDate
  };

  try {
    // Check if Contact already exists by OPERA Profile ID or Email
    const existingContacts = await conn.sobject('Contact')
      .find({
        $or: [
          { OPERA_Profile_ID__c: profile.profileId },
          { Email: profile.contactInfo.emailAddress }
        ]
      })
      .limit(1)
      .execute();

    if (existingContacts.length > 0) {
      // Update existing Contact
      const contactId = existingContacts[0].Id;
      await conn.sobject('Contact').update({
        Id: contactId,
        ...salesforceContact
      });
      console.log(`Updated Salesforce Contact: ${contactId}`);
    } else {
      // Create new Contact
      const result = await conn.sobject('Contact').create(salesforceContact);
      console.log(`Created Salesforce Contact: ${result.id}`);
    }

    // Log sync activity
    await logSyncActivity(profile.profileId, 'SUCCESS');

  } catch (error) {
    console.error('Salesforce sync error:', error);
    await logSyncActivity(profile.profileId, 'FAILED', error.message);
    throw error;
  }
}

// Function to handle guest check-in events
async function handleGuestCheckIn(operaEvent) {
  const profile = operaEvent.profile;

  // Update last stay date in Salesforce
  const result = await conn.sobject('Contact')
    .update({
      OPERA_Profile_ID__c: profile.profileId,
      Last_Stay_Date__c: new Date().toISOString(),
      Last_Property_Stayed__c: operaEvent.hotelId
    }, {
      extIdField: 'OPERA_Profile_ID__c'
    });

  console.log(`Updated check-in for Contact: ${result.id}`);
}

// Log sync activity to database or monitoring service
async function logSyncActivity(operaProfileId, status, errorMessage = null) {
  // Implement your logging logic here
  // Could write to database, CloudWatch, Datadog, etc.
  console.log(`Sync Log - Profile: ${operaProfileId}, Status: ${status}`);
}

// Start server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`OPERA webhook receiver listening on port ${PORT}`);
});
```

**Deployment Options:**
- AWS Lambda + API Gateway (serverless)
- Heroku (easy deployment)
- AWS ECS/Fargate (containerized)
- Azure Functions
- Google Cloud Run

**Environment Variables:**
```bash
# .env file
SF_CLIENT_ID=your_salesforce_connected_app_client_id
SF_CLIENT_SECRET=your_salesforce_connected_app_secret
SF_INSTANCE_URL=https://your-instance.salesforce.com
SF_ACCESS_TOKEN=your_access_token (or use OAuth flow)
OPERA_WEBHOOK_SECRET=your_webhook_secret_key
PORT=3000
```

#### Option C: AWS Lambda Webhook Handler

```javascript
// lambda-handler.js
const AWS = require('aws-sdk');
const axios = require('axios');

exports.handler = async (event) => {
  try {
    // Parse incoming webhook
    const operaEvent = JSON.parse(event.body);

    console.log('Received OPERA event:', operaEvent.eventType);

    // Verify webhook signature/API key
    const apiKey = event.headers['x-api-key'];
    if (apiKey !== process.env.OPERA_WEBHOOK_SECRET) {
      return {
        statusCode: 401,
        body: JSON.stringify({ error: 'Unauthorized' })
      };
    }

    // Get Salesforce access token from Secrets Manager
    const secretsManager = new AWS.SecretsManager();
    const secret = await secretsManager.getSecretValue({
      SecretId: 'salesforce-credentials'
    }).promise();

    const sfCredentials = JSON.parse(secret.SecretString);

    // Transform and send to Salesforce
    await syncToSalesforce(operaEvent, sfCredentials);

    return {
      statusCode: 200,
      body: JSON.stringify({
        success: true,
        message: 'Event processed'
      })
    };

  } catch (error) {
    console.error('Error:', error);

    // Send to DLQ for retry
    const sqs = new AWS.SQS();
    await sqs.sendMessage({
      QueueUrl: process.env.DLQ_URL,
      MessageBody: JSON.stringify({
        event: event,
        error: error.message,
        timestamp: new Date().toISOString()
      })
    }).promise();

    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};

async function syncToSalesforce(operaEvent, credentials) {
  const profile = operaEvent.profile;

  // Get Salesforce access token
  const authResponse = await axios.post(
    `${credentials.instanceUrl}/services/oauth2/token`,
    null,
    {
      params: {
        grant_type: 'password',
        client_id: credentials.clientId,
        client_secret: credentials.clientSecret,
        username: credentials.username,
        password: credentials.password
      }
    }
  );

  const accessToken = authResponse.data.access_token;
  const instanceUrl = authResponse.data.instance_url;

  // Check if Contact exists
  const queryResponse = await axios.get(
    `${instanceUrl}/services/data/v58.0/query`,
    {
      params: {
        q: `SELECT Id FROM Contact WHERE OPERA_Profile_ID__c = '${profile.profileId}' OR Email = '${profile.contactInfo.emailAddress}' LIMIT 1`
      },
      headers: {
        'Authorization': `Bearer ${accessToken}`
      }
    }
  );

  const contactData = {
    FirstName: profile.nameInfo.givenName,
    LastName: profile.nameInfo.surname,
    Email: profile.contactInfo.emailAddress,
    Phone: profile.contactInfo.phoneNumber,
    MobilePhone: profile.contactInfo.mobileNumber,
    MailingStreet: profile.addressInfo.addressLine1,
    MailingCity: profile.addressInfo.city,
    MailingState: profile.addressInfo.state,
    MailingPostalCode: profile.addressInfo.postalCode,
    MailingCountry: profile.addressInfo.country,
    OPERA_Profile_ID__c: profile.profileId,
    Guest_VIP_Status__c: profile.vipStatus,
    Guest_Membership_Level__c: profile.membershipLevel,
    LeadSource: 'OPERA PMS'
  };

  if (queryResponse.data.totalSize > 0) {
    // Update existing Contact
    const contactId = queryResponse.data.records[0].Id;
    await axios.patch(
      `${instanceUrl}/services/data/v58.0/sobjects/Contact/${contactId}`,
      contactData,
      {
        headers: {
          'Authorization': `Bearer ${accessToken}`,
          'Content-Type': 'application/json'
        }
      }
    );
    console.log(`Updated Contact: ${contactId}`);
  } else {
    // Create new Contact
    await axios.post(
      `${instanceUrl}/services/data/v58.0/sobjects/Contact`,
      contactData,
      {
        headers: {
          'Authorization': `Bearer ${accessToken}`,
          'Content-Type': 'application/json'
        }
      }
    );
    console.log('Created new Contact');
  }
}
```

**Lambda Configuration:**
- Runtime: Node.js 18.x
- Memory: 512 MB
- Timeout: 30 seconds
- Trigger: API Gateway REST API
- Environment variables: OPERA_WEBHOOK_SECRET, DLQ_URL
- Permissions: SecretsManager read, SQS write, CloudWatch Logs

### Approach 2: Scheduled Polling (API-Based Sync)

If OPERA Cloud Business Events are not available or you need additional control, use scheduled API polling.

#### Architecture

```
Scheduler (Cron/Lambda/Celigo)
    ↓
Query OPERA Cloud API for new/updated profiles
    ↓
Transform data
    ↓
Upsert to Salesforce
```

#### Implementation

**Using Celigo Scheduled Integration:**

1. **Create Scheduled Flow**
   - Schedule: Every 15 minutes (or as needed)
   - Source: OPERA Cloud REST API
   - Destination: Salesforce

2. **OPERA Cloud API Query**
   ```
   GET /crm/v1/hotels/{hotelId}/profiles

   Query Parameters:
   - modifiedSince: 2026-02-02T10:00:00Z (last sync timestamp)
   - limit: 100
   - offset: 0

   Headers:
   - Authorization: Bearer {oauth_token}
   - Content-Type: application/json
   ```

3. **Pagination Logic**
   ```javascript
   let offset = 0;
   const limit = 100;
   let hasMore = true;

   while (hasMore) {
     const response = await fetch(
       `${operaApiUrl}/crm/v1/hotels/${hotelId}/profiles?limit=${limit}&offset=${offset}&modifiedSince=${lastSyncTime}`,
       {
         headers: {
           'Authorization': `Bearer ${accessToken}`
         }
       }
     );

     const data = await response.json();

     // Process profiles
     await processProfiles(data.profiles);

     // Check if more pages exist
     hasMore = data.profiles.length === limit;
     offset += limit;
   }

   // Update last sync timestamp
   await updateLastSyncTime(new Date().toISOString());
   ```

4. **Track Last Sync Time**
   - Store in Celigo state variable
   - Or in Salesforce custom setting
   - Or in external database

**Custom Polling Script (Node.js):**

```javascript
// scheduled-sync.js
const cron = require('node-cron');
const axios = require('axios');
const jsforce = require('jsforce');

// Salesforce connection
const sfConn = new jsforce.Connection({
  loginUrl: process.env.SF_LOGIN_URL,
  username: process.env.SF_USERNAME,
  password: process.env.SF_PASSWORD
});

// OPERA Cloud API config
const operaConfig = {
  baseUrl: process.env.OPERA_API_URL,
  hotelId: process.env.OPERA_HOTEL_ID,
  clientId: process.env.OPERA_CLIENT_ID,
  clientSecret: process.env.OPERA_CLIENT_SECRET
};

let operaAccessToken = null;

// Authenticate with OPERA Cloud
async function getOperaAccessToken() {
  if (operaAccessToken) return operaAccessToken;

  const response = await axios.post(
    `${operaConfig.baseUrl}/oauth/v1/tokens`,
    {
      grant_type: 'client_credentials',
      client_id: operaConfig.clientId,
      client_secret: operaConfig.clientSecret
    }
  );

  operaAccessToken = response.data.access_token;

  // Refresh token before expiry
  setTimeout(() => {
    operaAccessToken = null;
  }, (response.data.expires_in - 300) * 1000); // Refresh 5 min before expiry

  return operaAccessToken;
}

// Get last sync timestamp from Salesforce custom setting
async function getLastSyncTime() {
  const result = await sfConn.query(
    "SELECT Last_OPERA_Sync__c FROM Integration_Settings__c LIMIT 1"
  );

  if (result.totalSize > 0) {
    return result.records[0].Last_OPERA_Sync__c;
  }

  // Default to 7 days ago if no previous sync
  const sevenDaysAgo = new Date();
  sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7);
  return sevenDaysAgo.toISOString();
}

// Update last sync timestamp
async function updateLastSyncTime(timestamp) {
  await sfConn.sobject('Integration_Settings__c').update({
    Id: process.env.SF_INTEGRATION_SETTINGS_ID,
    Last_OPERA_Sync__c: timestamp
  });
}

// Fetch profiles from OPERA Cloud
async function fetchOperaProfiles(modifiedSince) {
  const accessToken = await getOperaAccessToken();
  const allProfiles = [];
  let offset = 0;
  const limit = 100;
  let hasMore = true;

  while (hasMore) {
    const response = await axios.get(
      `${operaConfig.baseUrl}/crm/v1/hotels/${operaConfig.hotelId}/profiles`,
      {
        params: {
          modifiedSince: modifiedSince,
          limit: limit,
          offset: offset
        },
        headers: {
          'Authorization': `Bearer ${accessToken}`,
          'Content-Type': 'application/json'
        }
      }
    );

    const profiles = response.data.profiles || [];
    allProfiles.push(...profiles);

    hasMore = profiles.length === limit;
    offset += limit;

    console.log(`Fetched ${profiles.length} profiles (offset: ${offset})`);
  }

  return allProfiles;
}

// Sync profiles to Salesforce
async function syncProfilesToSalesforce(profiles) {
  console.log(`Syncing ${profiles.length} profiles to Salesforce`);

  const contacts = profiles.map(profile => ({
    OPERA_Profile_ID__c: profile.profileId,
    FirstName: profile.nameInfo.givenName,
    LastName: profile.nameInfo.surname,
    Email: profile.contactInfo.emailAddress,
    Phone: profile.contactInfo.phoneNumber,
    MobilePhone: profile.contactInfo.mobileNumber,
    MailingStreet: profile.addressInfo.addressLine1,
    MailingCity: profile.addressInfo.city,
    MailingState: profile.addressInfo.state,
    MailingPostalCode: profile.addressInfo.postalCode,
    MailingCountry: profile.addressInfo.country,
    Guest_VIP_Status__c: profile.vipStatus,
    Guest_Membership_Level__c: profile.membershipLevel,
    LeadSource: 'OPERA PMS'
  }));

  // Use Salesforce Bulk API for better performance
  const results = await sfConn.sobject('Contact').upsert(
    contacts,
    'OPERA_Profile_ID__c', // External ID field for upsert
    { allowNoOp: true }
  );

  // Log results
  const successful = results.filter(r => r.success).length;
  const failed = results.filter(r => !r.success).length;

  console.log(`Sync complete: ${successful} successful, ${failed} failed`);

  // Log failures
  results.filter(r => !r.success).forEach(result => {
    console.error('Sync failed:', result.errors);
  });

  return { successful, failed };
}

// Main sync function
async function performSync() {
  console.log('Starting OPERA to Salesforce sync...');

  try {
    // Get last sync time
    const lastSyncTime = await getLastSyncTime();
    console.log(`Last sync: ${lastSyncTime}`);

    // Fetch updated profiles from OPERA
    const profiles = await fetchOperaProfiles(lastSyncTime);
    console.log(`Found ${profiles.length} updated profiles`);

    if (profiles.length === 0) {
      console.log('No new profiles to sync');
      return;
    }

    // Sync to Salesforce
    const results = await syncProfilesToSalesforce(profiles);

    // Update last sync time
    await updateLastSyncTime(new Date().toISOString());

    console.log('Sync completed successfully');

  } catch (error) {
    console.error('Sync error:', error);
    // Send alert (email, Slack, PagerDuty, etc.)
    await sendErrorAlert(error);
  }
}

// Send error alerts
async function sendErrorAlert(error) {
  // Implement your alerting logic
  // Examples: Email, Slack webhook, PagerDuty, etc.
  console.error('ALERT: OPERA sync failed', error);
}

// Schedule sync to run every 15 minutes
cron.schedule('*/15 * * * *', () => {
  console.log('Running scheduled OPERA sync');
  performSync();
});

// Run immediately on startup
performSync();

console.log('OPERA to Salesforce sync scheduler started');
```

**Deployment:**
- Run as background service (PM2, systemd)
- Or deploy as Lambda with EventBridge schedule
- Or use Heroku Scheduler

### Approach 3: OHIP (Oracle Hospitality Integration Platform)

If you're an Oracle Hospitality enterprise customer, OHIP provides the most robust integration option.

#### Features

- Native OPERA Cloud integration
- Pre-built Salesforce connector
- Event streaming and queuing
- Built-in transformation and mapping
- Enterprise-grade monitoring and logging

#### Setup

1. **Enable OHIP in OPERA Cloud**
   - Contact Oracle support to enable OHIP
   - Configure event subscriptions

2. **Create Integration Flow**
   - Source: OPERA Cloud (via events or API)
   - Transformation: Map OPERA fields to Salesforce
   - Destination: Salesforce (via pre-built connector)

3. **Configure Salesforce Connector**
   - Authenticate with Salesforce
   - Map object relationships
   - Set sync frequency and error handling

4. **Monitor Integration**
   - OHIP dashboard shows real-time stats
   - Alerts for failures
   - Reprocess failed messages

**Pricing:** OHIP is typically included with Oracle Hospitality Cloud subscriptions but may require specific licensing tiers.

## Salesforce Configuration

### Required Custom Fields on Contact Object

Create these custom fields in Salesforce to store OPERA-specific data:

```
Field Name                    API Name                      Type        Description
────────────────────────────────────────────────────────────────────────────────────
OPERA Profile ID              OPERA_Profile_ID__c           Text(50)    External ID, Unique
Guest VIP Status              Guest_VIP_Status__c           Picklist    Gold, Platinum, Regular
Guest Membership Level        Guest_Membership_Level__c     Picklist    Elite, Premium, Standard
Last Stay Date                Last_Stay_Date__c             Date        Most recent check-out
Last Property Stayed          Last_Property_Stayed__c       Text(100)   Hotel/property name
Total Stays                   Total_Stays__c                Number      Lifetime stay count
Total Revenue                 Total_Revenue__c              Currency    Lifetime value
Preferred Room Type           Preferred_Room_Type__c        Picklist    King, Queen, Suite
Smoking Preference            Smoking_Preference__c         Picklist    Smoking, Non-Smoking
Language Preference           Language_Preference__c        Text(10)    EN, ES, FR, etc.
Special Requests              Special_Requests__c           Long Text   Guest notes/preferences
```

**Field Setup:**

1. Navigate to Setup → Object Manager → Contact
2. Create each field with specifications above
3. Set `OPERA_Profile_ID__c` as External ID (for upsert operations)
4. Make `OPERA_Profile_ID__c` unique
5. Add fields to Contact page layouts
6. Set field-level security

### Salesforce Duplicate Management

Since OPERA profiles sync to Salesforce, implement duplicate prevention:

**Duplicate Rules:**

1. **Rule:** Match on OPERA_Profile_ID__c
   - If two contacts have same OPERA Profile ID, block or alert

2. **Rule:** Match on Email
   - If email exists, update existing record instead of creating duplicate
   - Use fuzzy matching for name + email combination

**Matching Rules:**

Create matching rules for Contact object:
- Exact match on OPERA_Profile_ID__c
- Fuzzy match on Email + LastName
- Alert on potential duplicates

## Data Mapping Reference

### OPERA Cloud to Salesforce Field Mapping

| OPERA Field | OPERA API Path | Salesforce Field | Transformation |
|-------------|----------------|------------------|----------------|
| Profile ID | `profileId` | `OPERA_Profile_ID__c` | Direct |
| First Name | `nameInfo.givenName` | `FirstName` | Direct |
| Last Name | `nameInfo.surname` | `LastName` | Direct |
| Middle Name | `nameInfo.middleName` | `MiddleName` | Direct |
| Title | `nameInfo.nameTitle` | `Salutation` | Map: Mr. → Mr, Mrs. → Ms, etc. |
| Email | `contactInfo.emailAddress` | `Email` | Direct |
| Phone | `contactInfo.phoneNumber` | `Phone` | Format to E.164 |
| Mobile | `contactInfo.mobileNumber` | `MobilePhone` | Format to E.164 |
| Address Line 1 | `addressInfo.addressLine1` | `MailingStreet` | Concatenate line1 + line2 |
| Address Line 2 | `addressInfo.addressLine2` | `MailingStreet` | Concatenate line1 + line2 |
| City | `addressInfo.city` | `MailingCity` | Direct |
| State | `addressInfo.state` | `MailingState` | Direct |
| Postal Code | `addressInfo.postalCode` | `MailingPostalCode` | Direct |
| Country | `addressInfo.country` | `MailingCountry` | Map ISO codes to full names |
| VIP Status | `vipStatus` | `Guest_VIP_Status__c` | Direct |
| Membership Level | `membershipLevel` | `Guest_Membership_Level__c` | Direct |
| Preferred Room | `preferences.roomType` | `Preferred_Room_Type__c` | Direct |
| Smoking Pref | `preferences.smokingPreference` | `Smoking_Preference__c` | Direct |
| Language | `preferences.language` | `Language_Preference__c` | ISO code |
| Created Date | `createdDateTime` | N/A | Used for sync timestamp |
| Modified Date | `lastModifiedDateTime` | `LastModifiedDate` | For change tracking |

## Error Handling and Monitoring

### Common Integration Errors

**Error: 401 Unauthorized (OPERA API)**
- Cause: OAuth token expired
- Resolution: Refresh access token
- Prevention: Implement token refresh before expiry

**Error: 404 Not Found (OPERA API)**
- Cause: Profile ID doesn't exist
- Resolution: Skip record, log for review
- Prevention: Validate profile exists before querying details

**Error: DUPLICATE_VALUE (Salesforce)**
- Cause: OPERA Profile ID already exists
- Resolution: Use upsert instead of insert
- Prevention: Always use upsert with External ID

**Error: REQUIRED_FIELD_MISSING (Salesforce)**
- Cause: LastName is empty from OPERA
- Resolution: Set default value ("Unknown") or skip sync
- Prevention: Validate required fields before sync

**Error: INVALID_EMAIL_ADDRESS (Salesforce)**
- Cause: OPERA email format invalid
- Resolution: Validate and sanitize email format
- Prevention: Regex validation before sync

### Monitoring Strategies

**Metrics to Track:**

1. **Sync Performance:**
   - Total profiles synced per hour/day
   - Average sync latency
   - Success rate percentage

2. **Error Rates:**
   - Failed syncs by error type
   - Retry success rate
   - Data quality issues

3. **Data Freshness:**
   - Time since last successful sync
   - Oldest unsynced profile
   - Sync lag (OPERA modification to SF update)

**Monitoring Tools:**

- **CloudWatch** (AWS): Logs, metrics, alarms
- **Datadog**: Application performance monitoring
- **New Relic**: Real-time monitoring
- **Splunk**: Log aggregation and analysis
- **Salesforce Event Monitoring**: Track API usage

**Alerting Rules:**

```yaml
Alerts:
  - name: "Sync Failure Rate High"
    condition: error_rate > 5%
    action: Send email + Slack notification

  - name: "Sync Not Running"
    condition: no_sync_in_last_30_minutes
    action: Page on-call engineer

  - name: "API Rate Limit Approaching"
    condition: api_usage > 80%
    action: Send warning notification

  - name: "Data Quality Issue"
    condition: missing_required_fields > 10
    action: Alert data team
```

## Performance Optimization

### Batch Processing

For high-volume syncs, use batch processing:

**Salesforce Bulk API:**
```javascript
// Instead of individual updates
await sfConn.sobject('Contact').upsert(contacts, 'OPERA_Profile_ID__c');

// Processes up to 10,000 records per batch
// Much faster than individual REST API calls
```

**Batch Size Recommendations:**
- OPERA API: 100-200 profiles per request
- Salesforce Bulk API: 10,000 records per batch
- Webhook processing: 1 event at a time (real-time)

### Caching

Implement caching to reduce API calls:

**Cache Salesforce Contact IDs:**
```javascript
const contactIdCache = new Map();

async function getCachedContactId(operaProfileId) {
  if (contactIdCache.has(operaProfileId)) {
    return contactIdCache.get(operaProfileId);
  }

  // Query Salesforce
  const result = await sfConn.query(
    `SELECT Id FROM Contact WHERE OPERA_Profile_ID__c = '${operaProfileId}'`
  );

  if (result.totalSize > 0) {
    const contactId = result.records[0].Id;
    contactIdCache.set(operaProfileId, contactId);
    return contactId;
  }

  return null;
}

// Clear cache periodically
setInterval(() => {
  contactIdCache.clear();
}, 3600000); // Clear every hour
```

### Rate Limiting

Respect API rate limits:

**OPERA Cloud:** Typically 100-1000 requests/minute
**Salesforce:** 15,000-100,000 API calls/day (depends on edition)

**Implement Rate Limiting:**
```javascript
const rateLimit = require('express-rate-limit');

const operaLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 minute
  max: 100 // 100 requests per minute
});

app.use('/opera-webhook', operaLimiter);
```

## Security Best Practices

### 1. Secure Webhook Endpoints

**Validate Webhook Authenticity:**
```javascript
// Verify API key
const apiKey = req.headers['x-api-key'];
if (apiKey !== process.env.OPERA_WEBHOOK_SECRET) {
  return res.status(401).json({ error: 'Unauthorized' });
}

// Or verify signature (if OPERA provides HMAC)
const signature = req.headers['x-opera-signature'];
const expectedSignature = crypto
  .createHmac('sha256', process.env.WEBHOOK_SECRET)
  .update(JSON.stringify(req.body))
  .digest('hex');

if (signature !== expectedSignature) {
  return res.status(401).json({ error: 'Invalid signature' });
}
```

**Use HTTPS Only:**
- Always use HTTPS for webhook endpoints
- Configure SSL/TLS certificates
- Reject non-HTTPS requests

### 2. Credential Management

**Never hardcode credentials:**
```javascript
// ❌ BAD
const apiKey = 'hardcoded-api-key-123';

// ✅ GOOD
const apiKey = process.env.OPERA_API_KEY;
```

**Use Secret Management:**
- AWS Secrets Manager
- Azure Key Vault
- HashiCorp Vault
- Environment variables (for development)

### 3. API Authentication

**OAuth 2.0 for OPERA Cloud:**
```javascript
// Use client credentials flow
const tokenResponse = await axios.post(
  'https://opera-cloud.com/oauth/v1/tokens',
  {
    grant_type: 'client_credentials',
    client_id: process.env.OPERA_CLIENT_ID,
    client_secret: process.env.OPERA_CLIENT_SECRET
  }
);

const accessToken = tokenResponse.data.access_token;
```

**OAuth 2.0 for Salesforce:**
```javascript
// Use JWT bearer flow for server-to-server
const jwt = require('jsonwebtoken');

const token = jwt.sign(
  {
    iss: process.env.SF_CLIENT_ID,
    sub: process.env.SF_USERNAME,
    aud: 'https://login.salesforce.com',
    exp: Math.floor(Date.now() / 1000) + (5 * 60)
  },
  privateKey,
  { algorithm: 'RS256' }
);

const authResponse = await axios.post(
  'https://login.salesforce.com/services/oauth2/token',
  {
    grant_type: 'urn:ietf:params:oauth:grant-type:jwt-bearer',
    assertion: token
  }
);
```

### 4. Data Encryption

**Encrypt sensitive data in transit and at rest:**
- Use TLS 1.2+ for all API calls
- Encrypt PII data in databases
- Implement field-level encryption in Salesforce if needed

### 5. Logging and Audit

**Log integration activity but scrub PII:**
```javascript
// ❌ BAD - Logs PII
console.log('Processing profile:', profile);

// ✅ GOOD - Logs without PII
console.log('Processing profile:', {
  profileId: profile.profileId,
  hasEmail: !!profile.contactInfo.emailAddress,
  timestamp: new Date().toISOString()
});
```

## Testing Strategy

### 1. Unit Tests

Test individual transformation functions:

```javascript
// transformation.test.js
const { mapOperaToSalesforce } = require('./transformation');

describe('OPERA to Salesforce Mapping', () => {
  test('maps basic profile fields correctly', () => {
    const operaProfile = {
      profileId: '12345',
      nameInfo: {
        givenName: 'John',
        surname: 'Smith'
      },
      contactInfo: {
        emailAddress: 'john@example.com'
      }
    };

    const salesforceContact = mapOperaToSalesforce(operaProfile);

    expect(salesforceContact.FirstName).toBe('John');
    expect(salesforceContact.LastName).toBe('Smith');
    expect(salesforceContact.Email).toBe('john@example.com');
    expect(salesforceContact.OPERA_Profile_ID__c).toBe('12345');
  });

  test('handles missing optional fields', () => {
    const operaProfile = {
      profileId: '12345',
      nameInfo: {
        givenName: 'John',
        surname: 'Smith'
      },
      contactInfo: {} // No email
    };

    const salesforceContact = mapOperaToSalesforce(operaProfile);

    expect(salesforceContact.Email).toBeUndefined();
  });
});
```

### 2. Integration Tests

Test end-to-end flow in sandbox:

```javascript
// integration.test.js
describe('OPERA to Salesforce Integration', () => {
  test('syncs new profile to Salesforce', async () => {
    // Create test profile in OPERA sandbox
    const operaProfile = await createTestOperaProfile();

    // Trigger webhook or sync
    await triggerSync(operaProfile.profileId);

    // Wait for async processing
    await sleep(5000);

    // Verify Contact created in Salesforce sandbox
    const sfContact = await querySalesforceContact(operaProfile.profileId);

    expect(sfContact).toBeDefined();
    expect(sfContact.Email).toBe(operaProfile.contactInfo.emailAddress);
  });
});
```

### 3. Load Testing

Test performance with high volume:

```javascript
// load-test.js
const { performance } = require('perf_hooks');

async function loadTest() {
  const startTime = performance.now();
  const promises = [];

  // Simulate 1000 concurrent webhook requests
  for (let i = 0; i < 1000; i++) {
    promises.push(sendWebhook({
      eventType: 'ProfileCreated',
      profileId: `TEST-${i}`,
      profile: generateTestProfile()
    }));
  }

  await Promise.all(promises);

  const endTime = performance.now();
  const duration = endTime - startTime;

  console.log(`Processed 1000 profiles in ${duration}ms`);
  console.log(`Average: ${duration / 1000}ms per profile`);
}
```

## Implementation Checklist

- [ ] **OPERA Cloud Configuration**
  - [ ] Enable API access
  - [ ] Create OAuth application
  - [ ] Configure Business Events (if using webhooks)
  - [ ] Set webhook endpoint URL
  - [ ] Test event delivery

- [ ] **Salesforce Configuration**
  - [ ] Create custom fields on Contact object
  - [ ] Set OPERA_Profile_ID__c as External ID
  - [ ] Configure field-level security
  - [ ] Add fields to page layouts
  - [ ] Create duplicate rules
  - [ ] Set up matching rules

- [ ] **Integration Development**
  - [ ] Choose integration approach (webhook/polling/OHIP)
  - [ ] Build/configure middleware
  - [ ] Implement field mapping
  - [ ] Add error handling
  - [ ] Implement retry logic
  - [ ] Add logging and monitoring

- [ ] **Security**
  - [ ] Secure webhook endpoints (HTTPS, API key validation)
  - [ ] Store credentials in secrets manager
  - [ ] Implement OAuth flows
  - [ ] Enable audit logging

- [ ] **Testing**
  - [ ] Unit test transformations
  - [ ] Integration test in sandbox
  - [ ] Load test with realistic volume
  - [ ] Test error scenarios

- [ ] **Deployment**
  - [ ] Deploy to production environment
  - [ ] Configure monitoring and alerts
  - [ ] Document integration
  - [ ] Train support team

- [ ] **Go-Live**
  - [ ] Historical data migration (if needed)
  - [ ] Enable real-time sync
  - [ ] Monitor closely for first week
  - [ ] Gather feedback and optimize

## Estimated Timeline

**Week 1-2:** Planning and design
- Finalize requirements
- Choose integration approach
- Design data flow and field mapping

**Week 3-4:** OPERA and Salesforce configuration
- Set up custom fields
- Configure OPERA webhooks/API access
- Create test environments

**Week 5-7:** Integration development
- Build webhook receiver or polling service
- Implement transformation logic
- Add error handling and logging

**Week 8:** Testing
- Unit and integration testing
- Load testing
- User acceptance testing

**Week 9:** Deployment and go-live
- Deploy to production
- Historical data sync
- Enable real-time integration
- Monitor and support

**Total: 9 weeks (2-3 months)**

## Conclusion

OPERA Cloud provides robust integration capabilities through Business Events (webhooks) and REST APIs. The recommended approach is:

1. **Real-time syncs:** Use OPERA Business Events with webhooks for immediate profile synchronization
2. **Batch syncs:** Use scheduled API polling for bulk updates and historical data
3. **Platform choice:** Celigo or similar iPaaS for faster implementation, or custom development for full control
4. **Salesforce setup:** Create custom fields with External ID for efficient upsert operations

This integration enables seamless flow of guest data from OPERA PMS to Salesforce CRM, maintaining a single source of truth for client information and enabling effective marketing and sales operations.
