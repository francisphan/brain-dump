# OPERA Cloud OAuth Setup Guide

## Finding Your OAuth Credentials (client_id and client_secret)

### What Are These Credentials?

- **client_id** (Consumer Key): Public identifier for your application
- **client_secret** (Consumer Secret): Secret key used to authenticate your application
- These are created when you register an application with OPERA Cloud

### Where to Find/Create OAuth Credentials

## Step-by-Step Setup Process

### Step 1: Access OPERA Cloud Administration

1. **Log into OPERA Cloud**
   - URL: `https://opera-cloud.oracle.com`
   - Use your OPERA Cloud administrator credentials

2. **Navigate to Integration Settings**
   - Go to **Configuration** → **Integration** → **External Systems**
   - Or: **Settings** → **Property Settings** → **Integration**

   The exact path may vary by OPERA Cloud version and your permissions.

### Step 2: Register a New Application

1. **Create New External System/Application**
   - Click **"Add New External System"** or **"Register Application"**
   - Fill in the application details:

   ```
   Application Name: Your Integration Name (e.g., "Salesforce Integration")
   Description: Brief description of what this integration does
   Application Type: Server-to-Server / Machine-to-Machine
   Grant Type: Client Credentials
   ```

2. **Set Permissions/Scopes**
   - Select the API scopes your application needs:
     - `profile.read` - Read guest profiles
     - `profile.write` - Create/update guest profiles
     - `reservation.read` - Read reservations
     - `reservation.write` - Create/update reservations
     - `block.read` - Read group blocks
     - `block.write` - Create/update blocks
     - `availability.read` - Check availability
     - `rate.read` - Read rates

   **Best Practice:** Only request the minimum scopes needed for your integration.

3. **Save and Generate Credentials**
   - Click **"Save"** or **"Generate Credentials"**
   - OPERA Cloud will generate your OAuth credentials

### Step 3: Retrieve Your Credentials

After registration, you'll see:

```
Application Details
-------------------
Application Name: Salesforce Integration
Client ID: 8a7b6c5d-4e3f-2a1b-9c8d-7e6f5a4b3c2d
Client Secret: Ab12Cd34Ef56Gh78Ij90Kl12Mn34Op56Qr78St90Uv12
Callback URL: (if applicable for your integration type)
Created: 2026-02-02
Status: Active
```

**CRITICAL - SECURITY WARNING:**
- **Copy the client_secret immediately** - it may only be shown once
- If you lose it, you'll need to regenerate (which will invalidate the old one)
- Store these credentials securely (use a secrets manager, not plain text)

### Step 4: Additional Configuration

1. **Hotel/Property Association**
   - Link the application to specific properties/hotels
   - Navigate to property settings and enable the integration
   - Grant property-level access

2. **IP Whitelisting (Optional but Recommended)**
   - Add trusted IP addresses that can use these credentials
   - Provides additional security layer
   - Found in: **Security Settings** → **API Access Control**

3. **Environment Configuration**
   - Note your environment:
     - **Production:** `https://api.oracle.com/hospitality`
     - **Test/Sandbox:** `https://api-test.oracle.com/hospitality`
   - Chain code (property identifier)
   - Hotel ID(s)

## Finding Credentials for Existing Applications

If your application was already registered:

1. **Go to External Systems**
   - Configuration → Integration → External Systems

2. **Find Your Application**
   - Look for your application in the list
   - Click to view details

3. **View/Regenerate Credentials**
   - Click **"View Details"** or **"Manage"**
   - You'll see the `client_id` (always visible)
   - For `client_secret`:
     - **If visible:** Copy it now
     - **If hidden:** Click **"Regenerate Secret"** (this invalidates the old one)

## Alternative: OHIP (Oracle Hospitality Integration Platform)

If you're using **OHIP** (Oracle's integration middleware):

1. **OHIP Console Access**
   - URL provided by Oracle (varies by deployment)
   - Contact your Oracle Account Manager for access

2. **Navigate to Connections**
   - OHIP → Connections → OPERA Cloud Connection

3. **OAuth Configuration**
   - View or configure OAuth settings
   - Credentials may be pre-configured by Oracle

## Example OAuth Configuration

### Environment Details You'll Need

```yaml
OAuth Configuration:
  auth_url: https://api.oracle.com/oauth/v1/tokens
  base_url: https://api.oracle.com/hospitality/v1
  client_id: 8a7b6c5d-4e3f-2a1b-9c8d-7e6f5a4b3c2d
  client_secret: Ab12Cd34Ef56Gh78Ij90Kl12Mn34Op56Qr78St90Uv12
  grant_type: client_credentials
  scope: profile.read profile.write reservation.read reservation.write

Property Configuration:
  chain_code: GRAND
  hotel_id: HOTEL001
  property_name: Grand Beach Resort
```

## Testing Your Credentials

### Using cURL

```bash
# Request access token
curl -X POST https://api.oracle.com/oauth/v1/tokens \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=8a7b6c5d-4e3f-2a1b-9c8d-7e6f5a4b3c2d" \
  -d "client_secret=Ab12Cd34Ef56Gh78Ij90Kl12Mn34Op56Qr78St90Uv12" \
  -d "scope=profile.read"
```

**Expected Response:**
```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "profile.read"
}
```

### Using Postman

1. **Create New Request**
   - Method: POST
   - URL: `https://api.oracle.com/oauth/v1/tokens`

2. **Set Headers**
   ```
   Content-Type: application/x-www-form-urlencoded
   ```

3. **Set Body (x-www-form-urlencoded)**
   ```
   grant_type: client_credentials
   client_id: YOUR_CLIENT_ID
   client_secret: YOUR_CLIENT_SECRET
   scope: profile.read
   ```

4. **Send Request**
   - You should receive an access_token
   - Save this token for subsequent API calls

5. **Test API Call**
   - Create new request: GET
   - URL: `https://api.oracle.com/hospitality/v1/crm/v1/hotels/HOTEL001/profiles/{profileId}`
   - Headers:
     ```
     Authorization: Bearer YOUR_ACCESS_TOKEN
     Accept: application/json
     ```

## Common Issues and Solutions

### Issue 1: "Invalid client credentials"

**Cause:** Wrong client_id or client_secret

**Solutions:**
- Double-check for typos (copy-paste to avoid errors)
- Verify credentials haven't been regenerated
- Confirm you're using the correct environment (production vs. test)
- Check if the application is still active

### Issue 2: "Insufficient scope"

**Cause:** Requesting access to APIs without proper scope

**Solutions:**
- Check which scopes are assigned to your application
- Request additional scopes from OPERA Cloud administrator
- Update application permissions in OPERA Cloud settings

### Issue 3: "Application not found"

**Cause:** Application may be deactivated or deleted

**Solutions:**
- Verify application status in OPERA Cloud
- Check if application is enabled for your property
- Confirm you have the correct client_id

### Issue 4: "IP address not allowed"

**Cause:** IP whitelisting is enabled and your IP isn't allowed

**Solutions:**
- Add your server's IP address to the whitelist
- Contact OPERA Cloud administrator to update IP whitelist
- Verify you're calling from the correct server/environment

### Issue 5: "Token expired"

**Cause:** Access tokens typically expire after 1 hour

**Solutions:**
- Implement token refresh logic in your application
- Request new token when you receive 401 Unauthorized
- Don't hardcode tokens - generate them dynamically

## Security Best Practices

### 1. Secure Storage

**DO:**
- Store credentials in environment variables
- Use secrets management services:
  - AWS Secrets Manager
  - Azure Key Vault
  - HashiCorp Vault
  - Google Cloud Secret Manager
- Encrypt credentials at rest

**DON'T:**
- Hardcode in source code
- Commit to version control (Git)
- Store in plain text files
- Share via email or chat

### 2. Access Control

**DO:**
- Limit scope to minimum required permissions
- Use separate credentials for different integrations
- Regularly audit which applications have access
- Implement IP whitelisting

**DON'T:**
- Use admin credentials for integrations
- Share credentials across multiple applications
- Grant broader permissions than needed

### 3. Credential Rotation

**DO:**
- Rotate credentials regularly (every 90-180 days)
- Have a credential rotation procedure
- Test new credentials before revoking old ones
- Document which systems use which credentials

**DON'T:**
- Use the same credentials forever
- Rotate without testing
- Forget to update all systems using the credentials

### 4. Monitoring

**DO:**
- Log all authentication attempts
- Monitor for unusual API usage patterns
- Set up alerts for authentication failures
- Track which IPs are accessing your credentials

**DON'T:**
- Ignore authentication errors
- Skip logging
- Assume credentials are always secure

## Code Examples

### Node.js Example

```javascript
// config.js - Store credentials securely
module.exports = {
  opera: {
    authUrl: 'https://api.oracle.com/oauth/v1/tokens',
    baseUrl: 'https://api.oracle.com/hospitality/v1',
    clientId: process.env.OPERA_CLIENT_ID,
    clientSecret: process.env.OPERA_CLIENT_SECRET,
    hotelId: process.env.OPERA_HOTEL_ID
  }
};

// auth.js - Get OAuth token
const axios = require('axios');
const qs = require('querystring');
const config = require('./config');

let cachedToken = null;
let tokenExpiry = null;

async function getAccessToken() {
  // Return cached token if still valid
  if (cachedToken && tokenExpiry && new Date() < tokenExpiry) {
    return cachedToken;
  }

  try {
    const response = await axios.post(
      config.opera.authUrl,
      qs.stringify({
        grant_type: 'client_credentials',
        client_id: config.opera.clientId,
        client_secret: config.opera.clientSecret,
        scope: 'profile.read profile.write reservation.read reservation.write'
      }),
      {
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }
    );

    cachedToken = response.data.access_token;

    // Set expiry to 5 minutes before actual expiry
    const expiresIn = response.data.expires_in - 300;
    tokenExpiry = new Date(Date.now() + expiresIn * 1000);

    console.log('Successfully obtained access token');
    return cachedToken;

  } catch (error) {
    console.error('Failed to obtain access token:', error.response?.data || error.message);
    throw error;
  }
}

module.exports = { getAccessToken };
```

### Python Example

```python
# config.py
import os

OPERA_CONFIG = {
    'auth_url': 'https://api.oracle.com/oauth/v1/tokens',
    'base_url': 'https://api.oracle.com/hospitality/v1',
    'client_id': os.getenv('OPERA_CLIENT_ID'),
    'client_secret': os.getenv('OPERA_CLIENT_SECRET'),
    'hotel_id': os.getenv('OPERA_HOTEL_ID')
}

# auth.py
import requests
from datetime import datetime, timedelta
from config import OPERA_CONFIG

class OperaAuth:
    def __init__(self):
        self.access_token = None
        self.token_expiry = None

    def get_access_token(self):
        # Return cached token if still valid
        if self.access_token and self.token_expiry and datetime.now() < self.token_expiry:
            return self.access_token

        # Request new token
        payload = {
            'grant_type': 'client_credentials',
            'client_id': OPERA_CONFIG['client_id'],
            'client_secret': OPERA_CONFIG['client_secret'],
            'scope': 'profile.read profile.write reservation.read reservation.write'
        }

        headers = {
            'Content-Type': 'application/x-www-form-urlencoded'
        }

        try:
            response = requests.post(
                OPERA_CONFIG['auth_url'],
                data=payload,
                headers=headers
            )
            response.raise_for_status()

            data = response.json()
            self.access_token = data['access_token']

            # Set expiry to 5 minutes before actual expiry
            expires_in = data['expires_in'] - 300
            self.token_expiry = datetime.now() + timedelta(seconds=expires_in)

            print('Successfully obtained access token')
            return self.access_token

        except requests.exceptions.RequestException as e:
            print(f'Failed to obtain access token: {e}')
            raise
```

### .env File Example

```bash
# OPERA Cloud OAuth Credentials
OPERA_CLIENT_ID=8a7b6c5d-4e3f-2a1b-9c8d-7e6f5a4b3c2d
OPERA_CLIENT_SECRET=Ab12Cd34Ef56Gh78Ij90Kl12Mn34Op56Qr78St90Uv12
OPERA_HOTEL_ID=HOTEL001
OPERA_CHAIN_CODE=GRAND
OPERA_BASE_URL=https://api.oracle.com/hospitality/v1

# Never commit .env file to version control!
# Add .env to your .gitignore file
```

### .gitignore Entry

```
# Environment variables
.env
.env.local
.env.production

# Credentials
config/credentials.json
secrets/
*.key
*.pem
```

## Who Can Help?

If you can't find or don't have access to create OAuth credentials:

1. **Your OPERA Cloud Administrator**
   - Usually someone in IT or Operations
   - Has admin access to OPERA Cloud
   - Can create and manage integrations

2. **Oracle Account Manager**
   - Your dedicated Oracle contact
   - Can provide access to OPERA Cloud admin console
   - Can help with OHIP setup if applicable

3. **Oracle Support**
   - Open a ticket through My Oracle Support
   - Reference: OPERA Cloud API Integration
   - Ticket Type: Technical - Integration

4. **Property IT Department**
   - May have existing credentials
   - Can grant access to integration settings
   - Should have documentation of existing integrations

## Documentation Resources

- **OPERA Cloud API Documentation:** Ask your Oracle Account Manager for access
- **Oracle Hospitality Developer Portal:** Check if your organization has access
- **OPERA Cloud User Guide:** Configuration → Integration section
- **Oracle Support:** My Oracle Support (support.oracle.com)

## Quick Checklist

Before you start development, make sure you have:

- [ ] OPERA Cloud admin access or contact person
- [ ] Registered application in OPERA Cloud
- [ ] Client ID (consumer key)
- [ ] Client Secret (consumer secret) - SAVED SECURELY
- [ ] Hotel ID(s) and Chain Code
- [ ] Required API scopes/permissions
- [ ] Environment URL (production or test)
- [ ] IP whitelist configured (if required)
- [ ] Test credentials work (successful token request)
- [ ] Credentials stored securely (not in code)
- [ ] Documentation of what this integration does

## Summary

**Where to Find OAuth Credentials:**
1. Log into OPERA Cloud admin console
2. Navigate to Configuration → Integration → External Systems
3. Register a new application or find existing one
4. Copy `client_id` and `client_secret`
5. **Save the secret immediately** - you may not see it again!

**If You're Not an Admin:**
- Contact your OPERA Cloud administrator
- Or reach out to your Oracle Account Manager
- Provide details about your integration needs

**Security is Critical:**
- Never hardcode credentials
- Use environment variables or secrets managers
- Rotate regularly
- Monitor usage

---

*Need help? Contact your Oracle Account Manager or OPERA Cloud administrator for access to the admin console and API credentials.*
