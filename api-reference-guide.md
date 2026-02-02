# Complete API Reference Guide
## OPERA, Salesforce, Pardot, and NetSuite APIs

A comprehensive guide to all relevant APIs with example requests and responses in JSON format.

---

## Table of Contents

1. [OPERA Cloud APIs](#opera-cloud-apis)
2. [Salesforce REST API](#salesforce-rest-api)
3. [Pardot API](#pardot-api)
4. [NetSuite REST APIs](#netsuite-rest-apis)

---

# OPERA Cloud APIs

## Overview

**Base URL:** `https://{{hostName}}/{{chainCode}}/{{version}}`
- Example: `https://api.oracle.com/hospitality/v1`

**Authentication:** OAuth 2.0
**Format:** JSON
**Protocol:** REST

## Authentication

### OAuth 2.0 Token Request

**Endpoint:** `POST /oauth/v1/tokens`

**Request:**
```http
POST /oauth/v1/tokens HTTP/1.1
Host: api.oracle.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id=YOUR_CLIENT_ID
&client_secret=YOUR_CLIENT_SECRET
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "profile.read profile.write reservation.read reservation.write"
}
```

## Guest Profile APIs

### 1. Get Guest Profile

**Endpoint:** `GET /crm/v1/hotels/{hotelId}/profiles/{profileId}`

**Request:**
```http
GET /crm/v1/hotels/HOTEL001/profiles/12345678 HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Response:**
```json
{
  "profile": {
    "profileId": "12345678",
    "profileType": "GUEST",
    "nameInfo": {
      "nameType": "PRIMARY",
      "givenName": "John",
      "middleName": "Michael",
      "surname": "Smith",
      "nameTitle": "Mr.",
      "nameSuffix": ""
    },
    "contactInfo": {
      "emailAddress": "john.smith@email.com",
      "phoneNumbers": [
        {
          "phoneType": "MOBILE",
          "phoneNumber": "+1-555-123-4567",
          "primary": true
        },
        {
          "phoneType": "HOME",
          "phoneNumber": "+1-555-987-6543",
          "primary": false
        }
      ]
    },
    "addressInfo": {
      "addressType": "HOME",
      "addressLine1": "123 Main Street",
      "addressLine2": "Apt 4B",
      "city": "Miami",
      "state": "FL",
      "postalCode": "33101",
      "country": "USA"
    },
    "preferences": {
      "roomType": "KING",
      "smokingPreference": "NON_SMOKING",
      "floor": "HIGH",
      "newspaper": "WSJ",
      "language": "EN"
    },
    "membershipInfo": {
      "membershipNumber": "123456789",
      "membershipLevel": "GOLD",
      "membershipType": "LOYALTY",
      "points": 15000,
      "enrollmentDate": "2020-01-15"
    },
    "vipStatus": "VIP",
    "dateOfBirth": "1985-03-20",
    "createdDateTime": "2020-01-15T10:30:00Z",
    "lastModifiedDateTime": "2026-01-30T14:22:00Z"
  },
  "links": {
    "self": {
      "href": "/crm/v1/hotels/HOTEL001/profiles/12345678"
    }
  }
}
```

### 2. Create Guest Profile

**Endpoint:** `POST /crm/v1/hotels/{hotelId}/profiles`

**Request:**
```http
POST /crm/v1/hotels/HOTEL001/profiles HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
```

**Request Body:**
```json
{
  "profile": {
    "profileType": "GUEST",
    "nameInfo": {
      "givenName": "Jane",
      "surname": "Doe",
      "nameTitle": "Ms."
    },
    "contactInfo": {
      "emailAddress": "jane.doe@email.com",
      "phoneNumbers": [
        {
          "phoneType": "MOBILE",
          "phoneNumber": "+1-555-234-5678",
          "primary": true
        }
      ]
    },
    "addressInfo": {
      "addressType": "HOME",
      "addressLine1": "456 Oak Avenue",
      "city": "Orlando",
      "state": "FL",
      "postalCode": "32801",
      "country": "USA"
    },
    "preferences": {
      "roomType": "QUEEN",
      "smokingPreference": "NON_SMOKING",
      "language": "EN"
    },
    "dateOfBirth": "1990-07-15"
  }
}
```

**Response:**
```json
{
  "profile": {
    "profileId": "87654321",
    "profileType": "GUEST",
    "nameInfo": {
      "givenName": "Jane",
      "surname": "Doe",
      "nameTitle": "Ms."
    },
    "contactInfo": {
      "emailAddress": "jane.doe@email.com",
      "phoneNumbers": [
        {
          "phoneType": "MOBILE",
          "phoneNumber": "+1-555-234-5678",
          "primary": true
        }
      ]
    },
    "addressInfo": {
      "addressType": "HOME",
      "addressLine1": "456 Oak Avenue",
      "city": "Orlando",
      "state": "FL",
      "postalCode": "32801",
      "country": "USA"
    },
    "preferences": {
      "roomType": "QUEEN",
      "smokingPreference": "NON_SMOKING",
      "language": "EN"
    },
    "vipStatus": "REGULAR",
    "dateOfBirth": "1990-07-15",
    "createdDateTime": "2026-02-02T15:30:00Z",
    "lastModifiedDateTime": "2026-02-02T15:30:00Z"
  },
  "links": {
    "self": {
      "href": "/crm/v1/hotels/HOTEL001/profiles/87654321"
    }
  }
}
```

### 3. Update Guest Profile

**Endpoint:** `PUT /crm/v1/hotels/{hotelId}/profiles/{profileId}`

**Request:**
```http
PUT /crm/v1/hotels/HOTEL001/profiles/12345678 HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
```

**Request Body:**
```json
{
  "profile": {
    "profileId": "12345678",
    "contactInfo": {
      "emailAddress": "john.smith.new@email.com",
      "phoneNumbers": [
        {
          "phoneType": "MOBILE",
          "phoneNumber": "+1-555-999-8888",
          "primary": true
        }
      ]
    },
    "vipStatus": "PLATINUM"
  }
}
```

**Response:**
```json
{
  "profile": {
    "profileId": "12345678",
    "profileType": "GUEST",
    "nameInfo": {
      "givenName": "John",
      "middleName": "Michael",
      "surname": "Smith",
      "nameTitle": "Mr."
    },
    "contactInfo": {
      "emailAddress": "john.smith.new@email.com",
      "phoneNumbers": [
        {
          "phoneType": "MOBILE",
          "phoneNumber": "+1-555-999-8888",
          "primary": true
        }
      ]
    },
    "vipStatus": "PLATINUM",
    "lastModifiedDateTime": "2026-02-02T16:45:00Z"
  },
  "links": {
    "self": {
      "href": "/crm/v1/hotels/HOTEL001/profiles/12345678"
    }
  }
}
```

### 4. Search Profiles

**Endpoint:** `GET /crm/v1/hotels/{hotelId}/profiles`

**Request:**
```http
GET /crm/v1/hotels/HOTEL001/profiles?email=john.smith@email.com&limit=10&offset=0 HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Query Parameters:**
- `email` - Email address to search
- `lastName` - Last name to search
- `phoneNumber` - Phone number to search
- `membershipNumber` - Loyalty number
- `limit` - Number of results (default: 100, max: 500)
- `offset` - Pagination offset

**Response:**
```json
{
  "profiles": [
    {
      "profileId": "12345678",
      "nameInfo": {
        "givenName": "John",
        "surname": "Smith"
      },
      "contactInfo": {
        "emailAddress": "john.smith@email.com",
        "phoneNumbers": [
          {
            "phoneType": "MOBILE",
            "phoneNumber": "+1-555-123-4567"
          }
        ]
      },
      "vipStatus": "PLATINUM"
    }
  ],
  "totalResults": 1,
  "hasMore": false,
  "links": {
    "self": {
      "href": "/crm/v1/hotels/HOTEL001/profiles?email=john.smith@email.com&limit=10&offset=0"
    }
  }
}
```

## Reservation APIs

### 1. Get Reservation

**Endpoint:** `GET /rsv/v1/hotels/{hotelId}/reservations/{reservationId}`

**Request:**
```http
GET /rsv/v1/hotels/HOTEL001/reservations/RSV123456 HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Response:**
```json
{
  "reservation": {
    "reservationId": "RSV123456",
    "confirmationNumber": "CONF789012",
    "hotelId": "HOTEL001",
    "hotelName": "Grand Beach Resort",
    "reservationStatus": "RESERVED",
    "guestInfo": {
      "profileId": "12345678",
      "guestName": "John Smith",
      "emailAddress": "john.smith@email.com",
      "phoneNumber": "+1-555-123-4567"
    },
    "stayInfo": {
      "arrivalDate": "2026-03-15",
      "departureDate": "2026-03-18",
      "numberOfNights": 3,
      "numberOfAdults": 2,
      "numberOfChildren": 0,
      "arrivalTime": "15:00"
    },
    "roomInfo": {
      "roomType": "KING",
      "roomNumber": "",
      "numberOfRooms": 1,
      "roomRate": {
        "rateCode": "BAR",
        "ratePlan": "Best Available Rate",
        "amountBeforeTax": 250.00,
        "taxAmount": 37.50,
        "totalAmount": 287.50,
        "currency": "USD"
      }
    },
    "bookingInfo": {
      "bookingDate": "2026-01-20T14:30:00Z",
      "bookingSource": "DIRECT",
      "bookingChannel": "WEBSITE",
      "marketCode": "LEISURE",
      "sourceCode": "WEB"
    },
    "guaranteeInfo": {
      "guaranteeType": "CREDIT_CARD",
      "creditCard": {
        "cardType": "VISA",
        "cardNumber": "************1234",
        "cardHolderName": "John Smith",
        "expirationDate": "12/28"
      }
    },
    "specialRequests": "High floor, king bed, late checkout if possible",
    "totalRevenue": {
      "roomRevenue": 750.00,
      "taxAmount": 112.50,
      "totalAmount": 862.50,
      "currency": "USD"
    },
    "createdDateTime": "2026-01-20T14:30:00Z",
    "lastModifiedDateTime": "2026-01-20T14:30:00Z"
  },
  "links": {
    "self": {
      "href": "/rsv/v1/hotels/HOTEL001/reservations/RSV123456"
    },
    "profile": {
      "href": "/crm/v1/hotels/HOTEL001/profiles/12345678"
    }
  }
}
```

### 2. Create Reservation

**Endpoint:** `POST /rsv/v1/hotels/{hotelId}/reservations`

**Request:**
```http
POST /rsv/v1/hotels/HOTEL001/reservations HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
```

**Request Body:**
```json
{
  "reservation": {
    "guestInfo": {
      "profileId": "12345678",
      "guestName": "John Smith",
      "emailAddress": "john.smith@email.com",
      "phoneNumber": "+1-555-123-4567"
    },
    "stayInfo": {
      "arrivalDate": "2026-03-15",
      "departureDate": "2026-03-18",
      "numberOfAdults": 2,
      "numberOfChildren": 0,
      "arrivalTime": "15:00"
    },
    "roomInfo": {
      "roomType": "KING",
      "numberOfRooms": 1,
      "roomRate": {
        "rateCode": "BAR"
      }
    },
    "guaranteeInfo": {
      "guaranteeType": "CREDIT_CARD",
      "creditCard": {
        "cardType": "VISA",
        "cardNumber": "4111111111111111",
        "cardHolderName": "John Smith",
        "expirationDate": "12/28",
        "cvv": "123"
      }
    },
    "specialRequests": "High floor, king bed"
  }
}
```

**Response:**
```json
{
  "reservation": {
    "reservationId": "RSV999888",
    "confirmationNumber": "CONF445566",
    "hotelId": "HOTEL001",
    "reservationStatus": "RESERVED",
    "guestInfo": {
      "profileId": "12345678",
      "guestName": "John Smith",
      "emailAddress": "john.smith@email.com"
    },
    "stayInfo": {
      "arrivalDate": "2026-03-15",
      "departureDate": "2026-03-18",
      "numberOfNights": 3
    },
    "roomInfo": {
      "roomType": "KING",
      "numberOfRooms": 1,
      "roomRate": {
        "rateCode": "BAR",
        "amountBeforeTax": 250.00,
        "taxAmount": 37.50,
        "totalAmount": 287.50,
        "currency": "USD"
      }
    },
    "totalRevenue": {
      "roomRevenue": 750.00,
      "taxAmount": 112.50,
      "totalAmount": 862.50,
      "currency": "USD"
    },
    "createdDateTime": "2026-02-02T17:15:00Z"
  },
  "links": {
    "self": {
      "href": "/rsv/v1/hotels/HOTEL001/reservations/RSV999888"
    }
  }
}
```

### 3. Search Reservations

**Endpoint:** `GET /rsv/v1/hotels/{hotelId}/reservations`

**Request:**
```http
GET /rsv/v1/hotels/HOTEL001/reservations?arrivalDate=2026-03-15&status=RESERVED&limit=50 HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Query Parameters:**
- `arrivalDate` - Filter by arrival date (YYYY-MM-DD)
- `departureDate` - Filter by departure date
- `status` - RESERVED, IN_HOUSE, CHECKED_OUT, CANCELLED
- `profileId` - Filter by guest profile
- `confirmationNumber` - Search by confirmation number
- `limit` - Results per page
- `offset` - Pagination offset

**Response:**
```json
{
  "reservations": [
    {
      "reservationId": "RSV123456",
      "confirmationNumber": "CONF789012",
      "reservationStatus": "RESERVED",
      "guestInfo": {
        "guestName": "John Smith"
      },
      "stayInfo": {
        "arrivalDate": "2026-03-15",
        "departureDate": "2026-03-18",
        "numberOfNights": 3
      },
      "roomInfo": {
        "roomType": "KING",
        "roomRate": {
          "totalAmount": 287.50
        }
      }
    },
    {
      "reservationId": "RSV123457",
      "confirmationNumber": "CONF789013",
      "reservationStatus": "RESERVED",
      "guestInfo": {
        "guestName": "Jane Doe"
      },
      "stayInfo": {
        "arrivalDate": "2026-03-15",
        "departureDate": "2026-03-17",
        "numberOfNights": 2
      },
      "roomInfo": {
        "roomType": "QUEEN",
        "roomRate": {
          "totalAmount": 230.00
        }
      }
    }
  ],
  "totalResults": 45,
  "hasMore": false,
  "links": {
    "self": {
      "href": "/rsv/v1/hotels/HOTEL001/reservations?arrivalDate=2026-03-15&limit=50"
    }
  }
}
```

### 4. Cancel Reservation

**Endpoint:** `PUT /rsv/v1/hotels/{hotelId}/reservations/{reservationId}/cancel`

**Request:**
```http
PUT /rsv/v1/hotels/HOTEL001/reservations/RSV123456/cancel HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
```

**Request Body:**
```json
{
  "cancellation": {
    "cancellationReason": "GUEST_REQUEST",
    "cancellationNotes": "Guest changed travel plans"
  }
}
```

**Response:**
```json
{
  "reservation": {
    "reservationId": "RSV123456",
    "confirmationNumber": "CONF789012",
    "reservationStatus": "CANCELLED",
    "cancellationInfo": {
      "cancellationDate": "2026-02-02T18:00:00Z",
      "cancellationReason": "GUEST_REQUEST",
      "cancellationNumber": "CXL98765"
    }
  },
  "links": {
    "self": {
      "href": "/rsv/v1/hotels/HOTEL001/reservations/RSV123456"
    }
  }
}
```

## Block (Group) APIs

### 1. Get Block Details

**Endpoint:** `GET /blk/v1/hotels/{hotelId}/blocks/{blockId}`

**Request:**
```http
GET /blk/v1/hotels/HOTEL001/blocks/BLK12345 HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Response:**
```json
{
  "block": {
    "blockId": "BLK12345",
    "blockCode": "CORP2026",
    "blockName": "Tech Conference 2026",
    "hotelId": "HOTEL001",
    "blockStatus": "DEFINITE",
    "blockType": "GROUP",
    "dateRange": {
      "startDate": "2026-06-10",
      "endDate": "2026-06-14"
    },
    "accountInfo": {
      "accountName": "Tech Corp Inc",
      "accountId": "ACC789",
      "primaryContact": {
        "name": "Mary Johnson",
        "email": "mary.johnson@techcorp.com",
        "phone": "+1-555-777-8888"
      }
    },
    "roomBlock": [
      {
        "date": "2026-06-10",
        "roomType": "KING",
        "roomsBlocked": 50,
        "roomsSold": 35,
        "roomsAvailable": 15,
        "rateAmount": 199.00
      },
      {
        "date": "2026-06-11",
        "roomType": "KING",
        "roomsBlocked": 50,
        "roomsSold": 48,
        "roomsAvailable": 2,
        "rateAmount": 199.00
      },
      {
        "date": "2026-06-12",
        "roomType": "KING",
        "roomsBlocked": 50,
        "roomsSold": 50,
        "roomsAvailable": 0,
        "rateAmount": 199.00
      }
    ],
    "totalRoomNights": {
      "blocked": 200,
      "sold": 133,
      "available": 67,
      "pickupPercentage": 66.5
    },
    "rateInfo": {
      "rateCode": "GROUP",
      "ratePlan": "Corporate Group Rate",
      "currency": "USD"
    },
    "cutoffDate": "2026-05-10",
    "createdDateTime": "2026-01-10T09:00:00Z",
    "lastModifiedDateTime": "2026-02-01T14:30:00Z"
  },
  "links": {
    "self": {
      "href": "/blk/v1/hotels/HOTEL001/blocks/BLK12345"
    }
  }
}
```

### 2. Create Block

**Endpoint:** `POST /blk/v1/hotels/{hotelId}/blocks`

**Request:**
```http
POST /blk/v1/hotels/HOTEL001/blocks HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
```

**Request Body:**
```json
{
  "block": {
    "blockCode": "WEDD2026",
    "blockName": "Smith Wedding",
    "blockStatus": "TENTATIVE",
    "blockType": "GROUP",
    "dateRange": {
      "startDate": "2026-09-15",
      "endDate": "2026-09-17"
    },
    "accountInfo": {
      "accountName": "Smith Family",
      "primaryContact": {
        "name": "Sarah Smith",
        "email": "sarah.smith@email.com",
        "phone": "+1-555-444-3333"
      }
    },
    "roomBlock": [
      {
        "date": "2026-09-15",
        "roomType": "KING",
        "roomsBlocked": 20,
        "rateAmount": 179.00
      },
      {
        "date": "2026-09-16",
        "roomType": "KING",
        "roomsBlocked": 20,
        "rateAmount": 179.00
      }
    ],
    "cutoffDate": "2026-08-15"
  }
}
```

**Response:**
```json
{
  "block": {
    "blockId": "BLK99888",
    "blockCode": "WEDD2026",
    "blockName": "Smith Wedding",
    "blockStatus": "TENTATIVE",
    "dateRange": {
      "startDate": "2026-09-15",
      "endDate": "2026-09-17"
    },
    "roomBlock": [
      {
        "date": "2026-09-15",
        "roomType": "KING",
        "roomsBlocked": 20,
        "roomsSold": 0,
        "roomsAvailable": 20,
        "rateAmount": 179.00
      },
      {
        "date": "2026-09-16",
        "roomType": "KING",
        "roomsBlocked": 20,
        "roomsSold": 0,
        "roomsAvailable": 20,
        "rateAmount": 179.00
      }
    ],
    "createdDateTime": "2026-02-02T19:00:00Z"
  },
  "links": {
    "self": {
      "href": "/blk/v1/hotels/HOTEL001/blocks/BLK99888"
    }
  }
}
```

## Rate and Availability APIs

### 1. Check Availability

**Endpoint:** `GET /rsv/v1/hotels/{hotelId}/availability`

**Request:**
```http
GET /rsv/v1/hotels/HOTEL001/availability?arrivalDate=2026-03-20&departureDate=2026-03-23&adults=2 HTTP/1.1
Host: api.oracle.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Response:**
```json
{
  "availability": {
    "hotelId": "HOTEL001",
    "checkInDate": "2026-03-20",
    "checkOutDate": "2026-03-23",
    "numberOfNights": 3,
    "roomTypes": [
      {
        "roomType": "KING",
        "roomTypeName": "Deluxe King",
        "available": true,
        "roomsAvailable": 15,
        "rates": [
          {
            "rateCode": "BAR",
            "ratePlan": "Best Available Rate",
            "amountPerNight": 250.00,
            "totalAmount": 750.00,
            "taxAmount": 112.50,
            "totalWithTax": 862.50,
            "currency": "USD"
          },
          {
            "rateCode": "ADV",
            "ratePlan": "Advance Purchase",
            "amountPerNight": 225.00,
            "totalAmount": 675.00,
            "taxAmount": 101.25,
            "totalWithTax": 776.25,
            "currency": "USD",
            "restrictions": ["NON_REFUNDABLE"]
          }
        ]
      },
      {
        "roomType": "QUEEN",
        "roomTypeName": "Standard Queen",
        "available": true,
        "roomsAvailable": 8,
        "rates": [
          {
            "rateCode": "BAR",
            "ratePlan": "Best Available Rate",
            "amountPerNight": 200.00,
            "totalAmount": 600.00,
            "taxAmount": 90.00,
            "totalWithTax": 690.00,
            "currency": "USD"
          }
        ]
      },
      {
        "roomType": "SUITE",
        "roomTypeName": "Executive Suite",
        "available": false,
        "roomsAvailable": 0,
        "rates": []
      }
    ]
  },
  "links": {
    "self": {
      "href": "/rsv/v1/hotels/HOTEL001/availability?arrivalDate=2026-03-20&departureDate=2026-03-23"
    }
  }
}
```

## OPERA Error Responses

**401 Unauthorized:**
```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or expired access token",
    "status": 401
  }
}
```

**404 Not Found:**
```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Profile with ID 99999999 not found",
    "status": 404,
    "details": {
      "resource": "Profile",
      "resourceId": "99999999"
    }
  }
}
```

**400 Bad Request:**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request data",
    "status": 400,
    "errors": [
      {
        "field": "arrivalDate",
        "message": "Arrival date must be in the future"
      },
      {
        "field": "emailAddress",
        "message": "Invalid email format"
      }
    ]
  }
}
```

---

# Salesforce REST API

## Overview

**Base URL:** `https://{{instance}}.salesforce.com/services/data/v{{version}}`
- Example: `https://your-instance.salesforce.com/services/data/v60.0`

**Authentication:** OAuth 2.0
**Format:** JSON
**Protocol:** REST

## Authentication

### OAuth 2.0 - Username/Password Flow

**Endpoint:** `POST /services/oauth2/token`

**Request:**
```http
POST /services/oauth2/token HTTP/1.1
Host: login.salesforce.com
Content-Type: application/x-www-form-urlencoded

grant_type=password
&client_id=YOUR_CONSUMER_KEY
&client_secret=YOUR_CONSUMER_SECRET
&username=your.email@company.com
&password=YourPasswordYourSecurityToken
```

**Response:**
```json
{
  "access_token": "00D5e000000abcd!AR8AQMy7fSj...",
  "instance_url": "https://your-instance.salesforce.com",
  "id": "https://login.salesforce.com/id/00D5e000000abcd/0055e000000xyz",
  "token_type": "Bearer",
  "issued_at": "1706889600000",
  "signature": "abc123def456..."
}
```

### OAuth 2.0 - JWT Bearer Flow (Server-to-Server)

**Request:**
```http
POST /services/oauth2/token HTTP/1.1
Host: login.salesforce.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response:**
```json
{
  "access_token": "00D5e000000abcd!AR8AQMy7fSj...",
  "scope": "api web full",
  "instance_url": "https://your-instance.salesforce.com",
  "id": "https://login.salesforce.com/id/00D5e000000abcd/0055e000000xyz",
  "token_type": "Bearer"
}
```

## Contact APIs

### 1. Get Contact

**Endpoint:** `GET /services/data/v60.0/sobjects/Contact/{ContactId}`

**Request:**
```http
GET /services/data/v60.0/sobjects/Contact/0035e000001AbCdEFG HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Accept: application/json
```

**Response:**
```json
{
  "attributes": {
    "type": "Contact",
    "url": "/services/data/v60.0/sobjects/Contact/0035e000001AbCdEFG"
  },
  "Id": "0035e000001AbCdEFG",
  "FirstName": "John",
  "LastName": "Smith",
  "Email": "john.smith@email.com",
  "Phone": "(555) 123-4567",
  "MobilePhone": "(555) 987-6543",
  "MailingStreet": "123 Main Street",
  "MailingCity": "Miami",
  "MailingState": "FL",
  "MailingPostalCode": "33101",
  "MailingCountry": "USA",
  "Birthdate": "1985-03-20",
  "AccountId": "0015e000001XyzABC",
  "OwnerId": "0055e000001OwnerDEF",
  "LeadSource": "OPERA PMS",
  "OPERA_Profile_ID__c": "12345678",
  "Guest_VIP_Status__c": "PLATINUM",
  "Guest_Membership_Level__c": "GOLD",
  "Last_Stay_Date__c": "2026-01-15",
  "Total_Stays__c": 15,
  "Total_Revenue__c": 12500.00,
  "CreatedDate": "2020-01-15T10:30:00.000+0000",
  "LastModifiedDate": "2026-02-01T14:22:00.000+0000"
}
```

### 2. Create Contact

**Endpoint:** `POST /services/data/v60.0/sobjects/Contact`

**Request:**
```http
POST /services/data/v60.0/sobjects/Contact HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "FirstName": "Jane",
  "LastName": "Doe",
  "Email": "jane.doe@email.com",
  "Phone": "(555) 234-5678",
  "MobilePhone": "(555) 345-6789",
  "MailingStreet": "456 Oak Avenue",
  "MailingCity": "Orlando",
  "MailingState": "FL",
  "MailingPostalCode": "32801",
  "MailingCountry": "USA",
  "Birthdate": "1990-07-15",
  "LeadSource": "OPERA PMS",
  "OPERA_Profile_ID__c": "87654321",
  "Guest_VIP_Status__c": "REGULAR"
}
```

**Response:**
```json
{
  "id": "0035e000002HijKLMN",
  "success": true,
  "errors": []
}
```

### 3. Update Contact

**Endpoint:** `PATCH /services/data/v60.0/sobjects/Contact/{ContactId}`

**Request:**
```http
PATCH /services/data/v60.0/sobjects/Contact/0035e000001AbCdEFG HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "Email": "john.smith.new@email.com",
  "MobilePhone": "(555) 999-8888",
  "Guest_VIP_Status__c": "PLATINUM",
  "Last_Stay_Date__c": "2026-02-01",
  "Total_Stays__c": 16
}
```

**Response:**
```http
HTTP/1.1 204 No Content
```

### 4. Upsert Contact (by External ID)

**Endpoint:** `PATCH /services/data/v60.0/sobjects/Contact/OPERA_Profile_ID__c/{externalId}`

**Request:**
```http
PATCH /services/data/v60.0/sobjects/Contact/OPERA_Profile_ID__c/12345678 HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "FirstName": "John",
  "LastName": "Smith",
  "Email": "john.smith@email.com",
  "Phone": "(555) 123-4567",
  "OPERA_Profile_ID__c": "12345678",
  "Guest_VIP_Status__c": "PLATINUM",
  "Last_Stay_Date__c": "2026-02-01"
}
```

**Response (Created):**
```json
{
  "id": "0035e000003NewRecABC",
  "success": true,
  "errors": [],
  "created": true
}
```

**Response (Updated):**
```json
{
  "id": "0035e000001AbCdEFG",
  "success": true,
  "errors": [],
  "created": false
}
```

### 5. Query Contacts

**Endpoint:** `GET /services/data/v60.0/query`

**Request:**
```http
GET /services/data/v60.0/query?q=SELECT+Id,FirstName,LastName,Email,OPERA_Profile_ID__c+FROM+Contact+WHERE+Email='john.smith@email.com'+LIMIT+1 HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Accept: application/json
```

**Response:**
```json
{
  "totalSize": 1,
  "done": true,
  "records": [
    {
      "attributes": {
        "type": "Contact",
        "url": "/services/data/v60.0/sobjects/Contact/0035e000001AbCdEFG"
      },
      "Id": "0035e000001AbCdEFG",
      "FirstName": "John",
      "LastName": "Smith",
      "Email": "john.smith@email.com",
      "OPERA_Profile_ID__c": "12345678"
    }
  ]
}
```

### 6. Delete Contact

**Endpoint:** `DELETE /services/data/v60.0/sobjects/Contact/{ContactId}`

**Request:**
```http
DELETE /services/data/v60.0/sobjects/Contact/0035e000001AbCdEFG HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
```

**Response:**
```http
HTTP/1.1 204 No Content
```

## Account APIs

### 1. Create Account

**Endpoint:** `POST /services/data/v60.0/sobjects/Account`

**Request:**
```http
POST /services/data/v60.0/sobjects/Account HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "Name": "Tech Corp Inc",
  "Type": "Corporate",
  "Industry": "Technology",
  "BillingStreet": "100 Business Plaza",
  "BillingCity": "San Francisco",
  "BillingState": "CA",
  "BillingPostalCode": "94105",
  "BillingCountry": "USA",
  "Phone": "(555) 777-8888",
  "Website": "www.techcorp.com",
  "NumberOfEmployees": 5000,
  "AnnualRevenue": 50000000,
  "NetSuite_Customer_ID__c": "CUST-1001"
}
```

**Response:**
```json
{
  "id": "0015e000003CompABC",
  "success": true,
  "errors": []
}
```

## Custom Object APIs (Reservation)

### 1. Create Reservation Record

**Endpoint:** `POST /services/data/v60.0/sobjects/Reservation__c`

**Request:**
```http
POST /services/data/v60.0/sobjects/Reservation__c HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "Name": "RSV123456",
  "Contact__c": "0035e000001AbCdEFG",
  "Reservation_Number__c": "RSV123456",
  "Confirmation_Number__c": "CONF789012",
  "Check_In_Date__c": "2026-03-15",
  "Check_Out_Date__c": "2026-03-18",
  "Number_of_Nights__c": 3,
  "Room_Type__c": "KING",
  "Rate_Plan__c": "BAR",
  "Room_Revenue__c": 750.00,
  "Total_Revenue__c": 862.50,
  "Status__c": "Confirmed",
  "Booking_Source__c": "Direct",
  "Property__c": "Grand Beach Resort"
}
```

**Response:**
```json
{
  "id": "a005e000001ResABC",
  "success": true,
  "errors": []
}
```

## Bulk API (Composite Requests)

### 1. Composite Request (Multiple Operations)

**Endpoint:** `POST /services/data/v60.0/composite`

**Request:**
```http
POST /services/data/v60.0/composite HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "allOrNone": false,
  "compositeRequest": [
    {
      "method": "POST",
      "url": "/services/data/v60.0/sobjects/Contact",
      "referenceId": "NewContact1",
      "body": {
        "FirstName": "Alice",
        "LastName": "Johnson",
        "Email": "alice.johnson@email.com",
        "OPERA_Profile_ID__c": "11111111"
      }
    },
    {
      "method": "POST",
      "url": "/services/data/v60.0/sobjects/Contact",
      "referenceId": "NewContact2",
      "body": {
        "FirstName": "Bob",
        "LastName": "Williams",
        "Email": "bob.williams@email.com",
        "OPERA_Profile_ID__c": "22222222"
      }
    },
    {
      "method": "GET",
      "url": "/services/data/v60.0/query?q=SELECT+Id+FROM+Contact+WHERE+Email='test@test.com'",
      "referenceId": "QueryContact"
    }
  ]
}
```

**Response:**
```json
{
  "compositeResponse": [
    {
      "body": {
        "id": "0035e000004AliceABC",
        "success": true,
        "errors": []
      },
      "httpHeaders": {},
      "httpStatusCode": 201,
      "referenceId": "NewContact1"
    },
    {
      "body": {
        "id": "0035e000005BobDEF",
        "success": true,
        "errors": []
      },
      "httpHeaders": {},
      "httpStatusCode": 201,
      "referenceId": "NewContact2"
    },
    {
      "body": {
        "totalSize": 1,
        "done": true,
        "records": [
          {
            "attributes": {
              "type": "Contact",
              "url": "/services/data/v60.0/sobjects/Contact/0035e000006TestGHI"
            },
            "Id": "0035e000006TestGHI"
          }
        ]
      },
      "httpHeaders": {},
      "httpStatusCode": 200,
      "referenceId": "QueryContact"
    }
  ]
}
```

### 2. Bulk Upsert (Collection)

**Endpoint:** `PATCH /services/data/v60.0/composite/sobjects`

**Request:**
```http
PATCH /services/data/v60.0/composite/sobjects HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "allOrNone": false,
  "records": [
    {
      "attributes": {
        "type": "Contact"
      },
      "OPERA_Profile_ID__c": "12345678",
      "FirstName": "John",
      "LastName": "Smith",
      "Email": "john.smith@email.com",
      "Last_Stay_Date__c": "2026-02-01"
    },
    {
      "attributes": {
        "type": "Contact"
      },
      "OPERA_Profile_ID__c": "87654321",
      "FirstName": "Jane",
      "LastName": "Doe",
      "Email": "jane.doe@email.com",
      "Last_Stay_Date__c": "2026-02-02"
    }
  ]
}
```

**Response:**
```json
[
  {
    "id": "0035e000001AbCdEFG",
    "success": true,
    "errors": [],
    "created": false
  },
  {
    "id": "0035e000002HijKLMN",
    "success": true,
    "errors": [],
    "created": true
  }
]
```

## Campaign APIs

### 1. Create Campaign

**Endpoint:** `POST /services/data/v60.0/sobjects/Campaign`

**Request:**
```http
POST /services/data/v60.0/sobjects/Campaign HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "Name": "Q1 2026 Email Campaign",
  "Type": "Email",
  "Status": "In Progress",
  "StartDate": "2026-01-01",
  "EndDate": "2026-03-31",
  "IsActive": true,
  "Description": "Quarterly promotional email campaign",
  "BudgetedCost": 5000.00,
  "ExpectedRevenue": 50000.00
}
```

**Response:**
```json
{
  "id": "7015e000001CampABC",
  "success": true,
  "errors": []
}
```

### 2. Add Campaign Member

**Endpoint:** `POST /services/data/v60.0/sobjects/CampaignMember`

**Request:**
```http
POST /services/data/v60.0/sobjects/CampaignMember HTTP/1.1
Host: your-instance.salesforce.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Content-Type: application/json
```

**Request Body:**
```json
{
  "CampaignId": "7015e000001CampABC",
  "ContactId": "0035e000001AbCdEFG",
  "Status": "Sent"
}
```

**Response:**
```json
{
  "id": "00v5e000001MembDEF",
  "success": true,
  "errors": []
}
```

## Salesforce Error Responses

**400 Bad Request:**
```json
[
  {
    "message": "Required fields are missing: [LastName]",
    "errorCode": "REQUIRED_FIELD_MISSING",
    "fields": ["LastName"]
  }
]
```

**401 Unauthorized:**
```json
[
  {
    "message": "Session expired or invalid",
    "errorCode": "INVALID_SESSION_ID"
  }
]
```

**404 Not Found:**
```json
[
  {
    "message": "The requested resource does not exist",
    "errorCode": "NOT_FOUND"
  }
]
```

**API Limit Exceeded:**
```json
[
  {
    "message": "TotalRequests Limit exceeded.",
    "errorCode": "REQUEST_LIMIT_EXCEEDED"
  }
]
```

---

# Pardot API

## Overview

**Base URL:** `https://pi.pardot.com/api`
**Current Version:** v5 (REST API)
**Authentication:** OAuth 2.0 (via Salesforce)
**Format:** JSON

## Authentication

Pardot API now uses Salesforce OAuth 2.0 authentication.

### Get Access Token (via Salesforce)

**Endpoint:** `POST https://login.salesforce.com/services/oauth2/token`

**Request:**
```http
POST /services/oauth2/token HTTP/1.1
Host: login.salesforce.com
Content-Type: application/x-www-form-urlencoded

grant_type=password
&client_id=YOUR_SALESFORCE_CONSUMER_KEY
&client_secret=YOUR_SALESFORCE_CONSUMER_SECRET
&username=your.pardot@company.com
&password=YourPasswordYourSecurityToken
```

**Response:**
```json
{
  "access_token": "00D5e000000abcd!AR8AQMy7fSj...",
  "instance_url": "https://your-instance.salesforce.com",
  "id": "https://login.salesforce.com/id/00D5e000000abcd/0055e000000xyz",
  "token_type": "Bearer",
  "issued_at": "1706889600000",
  "signature": "abc123def456..."
}
```

**Note:** Use the `access_token` from Salesforce OAuth for Pardot API calls.

## Prospect APIs

### 1. Get Prospect by ID

**Endpoint:** `GET /api/v5/objects/prospects/{id}`

**Request:**
```http
GET /api/v5/objects/prospects/12345 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Accept: application/json
```

**Response:**
```json
{
  "id": 12345,
  "email": "john.smith@email.com",
  "firstName": "John",
  "lastName": "Smith",
  "company": "Tech Corp",
  "jobTitle": "Marketing Manager",
  "phone": "555-123-4567",
  "website": "www.techcorp.com",
  "addressOne": "123 Main Street",
  "addressTwo": "Suite 100",
  "city": "Miami",
  "state": "FL",
  "territory": "",
  "zip": "33101",
  "country": "USA",
  "score": 87,
  "grade": "B",
  "lastActivityAt": "2026-02-01T18:30:00.000Z",
  "createdAt": "2025-01-15T10:00:00.000Z",
  "updatedAt": "2026-02-01T18:30:00.000Z",
  "campaign": {
    "id": 5678,
    "name": "Q1 2026 Campaign"
  },
  "assignedTo": {
    "id": 9876,
    "email": "sales.rep@company.com"
  },
  "salesforceId": "00Q5e000001LeadABC",
  "crmLeadFid": "00Q5e000001LeadABC",
  "crmContactFid": null,
  "isDoNotEmail": false,
  "isDoNotCall": false,
  "optedOut": false,
  "isReviewed": true,
  "isStarred": false
}
```

### 2. Create or Update Prospect

**Endpoint:** `POST /api/v5/objects/prospects`

**Request:**
```http
POST /api/v5/objects/prospects HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Content-Type: application/json
```

**Request Body:**
```json
{
  "email": "alice.johnson@email.com",
  "firstName": "Alice",
  "lastName": "Johnson",
  "company": "Widget Inc",
  "jobTitle": "Director of Sales",
  "phone": "555-444-3333",
  "city": "Orlando",
  "state": "FL",
  "country": "USA",
  "campaign": {
    "id": 5678
  }
}
```

**Response:**
```json
{
  "id": 54321,
  "email": "alice.johnson@email.com",
  "firstName": "Alice",
  "lastName": "Johnson",
  "company": "Widget Inc",
  "jobTitle": "Director of Sales",
  "phone": "555-444-3333",
  "city": "Orlando",
  "state": "FL",
  "country": "USA",
  "score": 0,
  "grade": "D",
  "createdAt": "2026-02-02T20:00:00.000Z",
  "updatedAt": "2026-02-02T20:00:00.000Z",
  "campaign": {
    "id": 5678,
    "name": "Q1 2026 Campaign"
  },
  "isDoNotEmail": false,
  "optedOut": false
}
```

### 3. Update Prospect

**Endpoint:** `PATCH /api/v5/objects/prospects/{id}`

**Request:**
```http
PATCH /api/v5/objects/prospects/12345 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Content-Type: application/json
```

**Request Body:**
```json
{
  "score": 95,
  "grade": "A",
  "phone": "555-999-8888",
  "isReviewed": true
}
```

**Response:**
```json
{
  "id": 12345,
  "email": "john.smith@email.com",
  "firstName": "John",
  "lastName": "Smith",
  "score": 95,
  "grade": "A",
  "phone": "555-999-8888",
  "isReviewed": true,
  "updatedAt": "2026-02-02T20:15:00.000Z"
}
```

### 4. Query Prospects

**Endpoint:** `GET /api/v5/objects/prospects`

**Request:**
```http
GET /api/v5/objects/prospects?orderBy=score&limit=50&offset=0 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Accept: application/json
```

**Query Parameters:**
- `orderBy` - Field to sort by (score, grade, createdAt, updatedAt)
- `limit` - Number of results (max 200)
- `offset` - Pagination offset
- `createdAfter` - Filter by creation date
- `updatedAfter` - Filter by update date
- `grade` - Filter by grade
- `score_greater_than` - Filter by minimum score

**Response:**
```json
{
  "values": [
    {
      "id": 12345,
      "email": "john.smith@email.com",
      "firstName": "John",
      "lastName": "Smith",
      "score": 95,
      "grade": "A"
    },
    {
      "id": 54321,
      "email": "alice.johnson@email.com",
      "firstName": "Alice",
      "lastName": "Johnson",
      "score": 87,
      "grade": "B"
    }
  ],
  "totalResults": 150,
  "hasMore": true
}
```

### 5. Assign Prospect

**Endpoint:** `POST /api/v5/objects/prospects/{id}/assign`

**Request:**
```http
POST /api/v5/objects/prospects/12345/assign HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Content-Type: application/json
```

**Request Body:**
```json
{
  "user": {
    "id": 9876
  }
}
```

**Response:**
```json
{
  "id": 12345,
  "email": "john.smith@email.com",
  "assignedTo": {
    "id": 9876,
    "email": "sales.rep@company.com",
    "firstName": "Sales",
    "lastName": "Rep"
  },
  "updatedAt": "2026-02-02T20:30:00.000Z"
}
```

## Campaign APIs

### 1. Get Campaign

**Endpoint:** `GET /api/v5/objects/campaigns/{id}`

**Request:**
```http
GET /api/v5/objects/campaigns/5678 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Accept: application/json
```

**Response:**
```json
{
  "id": 5678,
  "name": "Q1 2026 Email Campaign",
  "cost": 5000,
  "createdAt": "2026-01-01T08:00:00.000Z",
  "updatedAt": "2026-02-01T12:00:00.000Z"
}
```

### 2. List Campaigns

**Endpoint:** `GET /api/v5/objects/campaigns`

**Request:**
```http
GET /api/v5/objects/campaigns?limit=50&offset=0 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Accept: application/json
```

**Response:**
```json
{
  "values": [
    {
      "id": 5678,
      "name": "Q1 2026 Email Campaign",
      "cost": 5000
    },
    {
      "id": 5679,
      "name": "Webinar Series 2026",
      "cost": 3000
    }
  ],
  "totalResults": 25,
  "hasMore": false
}
```

## List (Segmentation) APIs

### 1. Get List

**Endpoint:** `GET /api/v5/objects/lists/{id}`

**Request:**
```http
GET /api/v5/objects/lists/1111 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Accept: application/json
```

**Response:**
```json
{
  "id": 1111,
  "name": "NetSuite Investors",
  "isPublic": false,
  "isDynamic": true,
  "title": "Active Investors",
  "description": "All active property investors in NetSuite",
  "isCrmVisible": true,
  "createdAt": "2026-01-15T09:00:00.000Z",
  "updatedAt": "2026-02-01T14:00:00.000Z"
}
```

### 2. List Membership - Add Prospect to List

**Endpoint:** `POST /api/v5/objects/listMemberships`

**Request:**
```http
POST /api/v5/objects/listMemberships HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Content-Type: application/json
```

**Request Body:**
```json
{
  "listId": 1111,
  "prospectId": 12345
}
```

**Response:**
```json
{
  "id": 99999,
  "listId": 1111,
  "prospectId": 12345,
  "createdAt": "2026-02-02T21:00:00.000Z"
}
```

## Email APIs

### 1. Get Email Template

**Endpoint:** `GET /api/v5/objects/emailTemplates/{id}`

**Request:**
```http
GET /api/v5/objects/emailTemplates/7777 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Accept: application/json
```

**Response:**
```json
{
  "id": 7777,
  "name": "Welcome Email Template",
  "subject": "Welcome to Our Community, %%first_name%%!",
  "htmlMessage": "<html><body><h1>Welcome!</h1><p>Hi %%first_name%%,</p><p>Thank you for joining us.</p></body></html>",
  "textMessage": "Hi %%first_name%%,\n\nWelcome to our community!",
  "isArchived": false,
  "createdAt": "2025-12-01T10:00:00.000Z",
  "updatedAt": "2026-01-15T14:00:00.000Z"
}
```

### 2. Send One-to-One Email

**Endpoint:** `POST /api/v5/objects/emails`

**Request:**
```http
POST /api/v5/objects/emails HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Content-Type: application/json
```

**Request Body:**
```json
{
  "prospectIds": [12345],
  "campaignId": 5678,
  "emailTemplateId": 7777,
  "replyToAddress": "sales@company.com",
  "sendOptions": {
    "sendImmediately": true
  }
}
```

**Response:**
```json
{
  "queued": 1,
  "sent": 0,
  "errors": []
}
```

## Visitor Activity APIs

### 1. Get Visitor Activities

**Endpoint:** `GET /api/v5/objects/visitorActivities`

**Request:**
```http
GET /api/v5/objects/visitorActivities?prospectId=12345&limit=50 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Accept: application/json
```

**Response:**
```json
{
  "values": [
    {
      "id": 88888,
      "prospectId": 12345,
      "type": "Email",
      "typeId": 7777,
      "details": "Opened email: Welcome to Our Community",
      "emailId": 7777,
      "campaignId": 5678,
      "createdAt": "2026-02-02T10:30:00.000Z"
    },
    {
      "id": 88889,
      "prospectId": 12345,
      "type": "Email",
      "typeId": 7777,
      "details": "Clicked link in email: Welcome to Our Community",
      "emailId": 7777,
      "campaignId": 5678,
      "createdAt": "2026-02-02T10:32:00.000Z"
    },
    {
      "id": 88890,
      "prospectId": 12345,
      "type": "Visit",
      "details": "Visited page: /pricing",
      "createdAt": "2026-02-02T10:35:00.000Z"
    },
    {
      "id": 88891,
      "prospectId": 12345,
      "type": "Form",
      "typeId": 3333,
      "details": "Submitted form: Contact Us",
      "formId": 3333,
      "campaignId": 5678,
      "createdAt": "2026-02-02T10:40:00.000Z"
    }
  ],
  "totalResults": 45,
  "hasMore": false
}
```

## Form APIs

### 1. Get Form

**Endpoint:** `GET /api/v5/objects/forms/{id}`

**Request:**
```http
GET /api/v5/objects/forms/3333 HTTP/1.1
Host: pi.pardot.com
Authorization: Bearer 00D5e000000abcd!AR8AQMy7fSj...
Pardot-Business-Unit-Id: 0Uv5e000000abcd
Accept: application/json
```

**Response:**
```json
{
  "id": 3333,
  "name": "Contact Us Form",
  "embedCode": "<iframe src=\"https://go.pardot.com/l/123456/2026-01-01/abc123\" width=\"100%\" height=\"500\"></iframe>",
  "campaignId": 5678,
  "layout": "above",
  "createdAt": "2026-01-01T08:00:00.000Z",
  "updatedAt": "2026-01-15T12:00:00.000Z"
}
```

## Pardot Error Responses

**400 Bad Request:**
```json
{
  "errors": [
    {
      "message": "Invalid request: email is required",
      "code": "INVALID_REQUEST"
    }
  ]
}
```

**401 Unauthorized:**
```json
{
  "errors": [
    {
      "message": "Invalid access token",
      "code": "UNAUTHORIZED"
    }
  ]
}
```

**404 Not Found:**
```json
{
  "errors": [
    {
      "message": "Prospect not found",
      "code": "NOT_FOUND"
    }
  ]
}
```

**429 Rate Limit:**
```json
{
  "errors": [
    {
      "message": "API rate limit exceeded",
      "code": "RATE_LIMIT_EXCEEDED"
    }
  ]
}
```

---

# NetSuite REST APIs

## Overview

**Base URL:** `https://{{account_id}}.suitetalk.api.netsuite.com/services/rest`
- Example: `https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1`

**Authentication:** OAuth 1.0a or Token-Based Authentication (TBA)
**Format:** JSON
**Protocol:** REST

## Authentication

### OAuth 1.0a (Token-Based Authentication)

NetSuite uses OAuth 1.0a with Token-Based Authentication.

**Required Headers:**
```http
Authorization: OAuth realm="{{account_id}}",
oauth_consumer_key="{{consumer_key}}",
oauth_token="{{token_id}}",
oauth_signature_method="HMAC-SHA256",
oauth_timestamp="{{timestamp}}",
oauth_nonce="{{nonce}}",
oauth_version="1.0",
oauth_signature="{{signature}}"
```

**Note:** NetSuite OAuth signature generation is complex. Use NetSuite SDK or libraries for proper implementation.

## Customer APIs

### 1. Get Customer

**Endpoint:** `GET /record/v1/customer/{internalId}`

**Request:**
```http
GET /record/v1/customer/1001 HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Accept: application/json
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/customer/1001"
    }
  ],
  "internalId": "1001",
  "entityId": "CUST-1001",
  "companyName": "Smith Investment LLC",
  "isPerson": false,
  "firstName": "John",
  "lastName": "Smith",
  "email": "john.smith@email.com",
  "phone": "555-123-4567",
  "mobilePhone": "555-987-6543",
  "defaultAddress": {
    "addressee": "John Smith",
    "addr1": "123 Main Street",
    "addr2": "Apt 4B",
    "city": "Miami",
    "state": "FL",
    "zip": "33101",
    "country": "US"
  },
  "subsidiary": {
    "id": "1",
    "refName": "Parent Company"
  },
  "currency": {
    "id": "1",
    "refName": "USD"
  },
  "salesRep": {
    "id": "500",
    "refName": "Sales Rep Name"
  },
  "customFieldList": {
    "customField": [
      {
        "scriptId": "custentity_salesforce_id",
        "value": "0015e000003CompABC"
      },
      {
        "scriptId": "custentity_total_investments",
        "value": "500000.00"
      },
      {
        "scriptId": "custentity_investor_status",
        "value": {
          "id": "1",
          "refName": "Active Investor"
        }
      }
    ]
  },
  "dateCreated": "2025-01-15T10:00:00Z",
  "lastModifiedDate": "2026-02-01T14:30:00Z"
}
```

### 2. Create Customer

**Endpoint:** `POST /record/v1/customer`

**Request:**
```http
POST /record/v1/customer HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Content-Type: application/json
```

**Request Body:**
```json
{
  "entityId": "CUST-2001",
  "companyName": "Doe Investment Group",
  "isPerson": false,
  "firstName": "Jane",
  "lastName": "Doe",
  "email": "jane.doe@email.com",
  "phone": "555-234-5678",
  "defaultAddress": {
    "addressee": "Jane Doe",
    "addr1": "456 Oak Avenue",
    "city": "Orlando",
    "state": "FL",
    "zip": "32801",
    "country": "US"
  },
  "subsidiary": {
    "id": "1"
  },
  "currency": {
    "id": "1"
  },
  "customFieldList": {
    "customField": [
      {
        "scriptId": "custentity_salesforce_id",
        "value": "0015e000004NewCust"
      },
      {
        "scriptId": "custentity_investor_status",
        "value": "2"
      }
    ]
  }
}
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/customer/2001"
    }
  ],
  "internalId": "2001",
  "entityId": "CUST-2001",
  "companyName": "Doe Investment Group",
  "email": "jane.doe@email.com"
}
```

### 3. Update Customer

**Endpoint:** `PATCH /record/v1/customer/{internalId}`

**Request:**
```http
PATCH /record/v1/customer/1001 HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Content-Type: application/json
```

**Request Body:**
```json
{
  "email": "john.smith.new@email.com",
  "mobilePhone": "555-999-8888",
  "customFieldList": {
    "customField": [
      {
        "scriptId": "custentity_total_investments",
        "value": "750000.00"
      },
      {
        "scriptId": "custentity_investor_status",
        "value": "1"
      }
    ]
  }
}
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/customer/1001"
    }
  ],
  "internalId": "1001",
  "email": "john.smith.new@email.com",
  "mobilePhone": "555-999-8888",
  "lastModifiedDate": "2026-02-02T22:00:00Z"
}
```

### 4. Search Customers (SuiteQL)

**Endpoint:** `POST /query/v1/suiteql`

**Request:**
```http
POST /query/v1/suiteql HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Content-Type: application/json
Prefer: transient
```

**Request Body:**
```json
{
  "q": "SELECT id, entityid, companyname, email, custentity_salesforce_id FROM customer WHERE email = 'john.smith@email.com' FETCH FIRST 10 ROWS ONLY"
}
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/query/v1/suiteql"
    }
  ],
  "count": 1,
  "hasMore": false,
  "items": [
    {
      "links": [
        {
          "rel": "self",
          "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/customer/1001"
        }
      ],
      "id": "1001",
      "entityid": "CUST-1001",
      "companyname": "Smith Investment LLC",
      "email": "john.smith@email.com",
      "custentity_salesforce_id": "0015e000003CompABC"
    }
  ],
  "totalResults": 1
}
```

## Item (Inventory/Product) APIs

### 1. Get Item

**Endpoint:** `GET /record/v1/inventoryItem/{internalId}`

**Request:**
```http
GET /record/v1/inventoryItem/5001 HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Accept: application/json
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/inventoryItem/5001"
    }
  ],
  "internalId": "5001",
  "itemId": "PROP-MIA-101",
  "displayName": "Miami Beach Condo - Unit 101",
  "description": "Luxury 2BR/2BA oceanfront condo",
  "basePrice": 500000.00,
  "costEstimate": 350000.00,
  "currency": {
    "id": "1",
    "refName": "USD"
  },
  "subsidiary": {
    "id": "1",
    "refName": "Parent Company"
  },
  "location": {
    "id": "10",
    "refName": "Miami Property"
  },
  "quantityAvailable": 1,
  "quantityOnHand": 1,
  "quantityCommitted": 0,
  "customFieldList": {
    "customField": [
      {
        "scriptId": "custitem_property_address",
        "value": "123 Ocean Drive, Miami Beach, FL 33139"
      },
      {
        "scriptId": "custitem_property_type",
        "value": {
          "id": "1",
          "refName": "Condo"
        }
      },
      {
        "scriptId": "custitem_square_footage",
        "value": "1500"
      }
    ]
  },
  "dateCreated": "2025-06-01T09:00:00Z",
  "lastModifiedDate": "2026-01-20T11:00:00Z"
}
```

### 2. Create Item

**Endpoint:** `POST /record/v1/inventoryItem`

**Request:**
```http
POST /record/v1/inventoryItem HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Content-Type: application/json
```

**Request Body:**
```json
{
  "itemId": "PROP-ORL-205",
  "displayName": "Orlando Villa - Unit 205",
  "description": "3BR/2BA villa with pool access",
  "basePrice": 400000.00,
  "costEstimate": 280000.00,
  "subsidiary": {
    "id": "1"
  },
  "location": {
    "id": "20"
  },
  "quantityOnHand": 1,
  "customFieldList": {
    "customField": [
      {
        "scriptId": "custitem_property_address",
        "value": "789 Resort Blvd, Orlando, FL 32819"
      },
      {
        "scriptId": "custitem_property_type",
        "value": "2"
      },
      {
        "scriptId": "custitem_square_footage",
        "value": "2000"
      }
    ]
  }
}
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/inventoryItem/5002"
    }
  ],
  "internalId": "5002",
  "itemId": "PROP-ORL-205",
  "displayName": "Orlando Villa - Unit 205"
}
```

## Sales Order APIs

### 1. Get Sales Order

**Endpoint:** `GET /record/v1/salesOrder/{internalId}`

**Request:**
```http
GET /record/v1/salesOrder/3001 HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Accept: application/json
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/salesOrder/3001"
    }
  ],
  "internalId": "3001",
  "tranId": "SO-2026-001",
  "entity": {
    "id": "1001",
    "refName": "Smith Investment LLC"
  },
  "tranDate": "2026-02-01",
  "status": {
    "id": "PENDING_FULFILLMENT",
    "refName": "Pending Fulfillment"
  },
  "currency": {
    "id": "1",
    "refName": "USD"
  },
  "subsidiary": {
    "id": "1",
    "refName": "Parent Company"
  },
  "total": 500000.00,
  "subtotal": 500000.00,
  "taxtotal": 0.00,
  "itemList": {
    "item": [
      {
        "item": {
          "id": "5001",
          "refName": "Miami Beach Condo - Unit 101"
        },
        "quantity": 1,
        "rate": 500000.00,
        "amount": 500000.00,
        "description": "Purchase of Miami Beach Condo Unit 101"
      }
    ]
  },
  "customFieldList": {
    "customField": [
      {
        "scriptId": "custbody_salesforce_opportunity",
        "value": "0065e000001OppABC"
      },
      {
        "scriptId": "custbody_contract_date",
        "value": "2026-02-01"
      }
    ]
  },
  "createdDate": "2026-02-01T15:00:00Z",
  "lastModifiedDate": "2026-02-01T15:00:00Z"
}
```

### 2. Create Sales Order

**Endpoint:** `POST /record/v1/salesOrder`

**Request:**
```http
POST /record/v1/salesOrder HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Content-Type: application/json
```

**Request Body:**
```json
{
  "entity": {
    "id": "2001"
  },
  "tranDate": "2026-02-02",
  "subsidiary": {
    "id": "1"
  },
  "currency": {
    "id": "1"
  },
  "itemList": {
    "item": [
      {
        "item": {
          "id": "5002"
        },
        "quantity": 1,
        "rate": 400000.00,
        "amount": 400000.00,
        "description": "Purchase of Orlando Villa Unit 205"
      }
    ]
  },
  "customFieldList": {
    "customField": [
      {
        "scriptId": "custbody_salesforce_opportunity",
        "value": "0065e000002NewOpp"
      }
    ]
  }
}
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/salesOrder/3002"
    }
  ],
  "internalId": "3002",
  "tranId": "SO-2026-002",
  "entity": {
    "id": "2001",
    "refName": "Doe Investment Group"
  },
  "total": 400000.00
}
```

## Invoice APIs

### 1. Get Invoice

**Endpoint:** `GET /record/v1/invoice/{internalId}`

**Request:**
```http
GET /record/v1/invoice/4001 HTTP/1.1
Host: 1234567.suitetalk.api.netsuite.com
Authorization: OAuth realm="1234567",...
Accept: application/json
```

**Response:**
```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://1234567.suitetalk.api.netsuite.com/services/rest/record/v1/invoice/4001"
    }
  ],
  "internalId": "4001",
  "tranId": "INV-2026-001",
  "entity": {
    "id": "1001",
    "refName": "Smith Investment LLC"
  },
  "tranDate": "2026-02-05",
  "dueDate": "2026-03-05",
  "status": {
    "id": "OPEN",
    "refName": "Open"
  },
  "currency": {
    "id": "1",
    "refName": "USD"
  },
  "total": 500000.00,
  "amountPaid": 100000.00,
  "amountRemaining": 400000.00,
  "itemList": {
    "item": [
      {
        "item": {
          "id": "5001",
          "refName": "Miami Beach Condo - Unit 101"
        },
        "quantity": 1,
        "rate": 500000.00,
        "amount": 500000.00
      }
    ]
  },
  "createdFrom": {
    "id": "3001",
    "refName": "SO-2026-001"
  },
  "createdDate": "2026-02-05T10:00:00Z",
  "lastModifiedDate": "2026-02-05T10:00:00Z"
}
```

## NetSuite RESTlet (Custom Endpoints)

RESTlets allow you to create custom REST endpoints in NetSuite.

### Example: Custom Customer Lookup RESTlet

**Endpoint:** `GET /app/site/hosting/restlet.nl?script={scriptId}&deploy={deployId}`

**Request:**
```http
GET /app/site/hosting/restlet.nl?script=123&deploy=1&email=john.smith@email.com HTTP/1.1
Host: 1234567.restlets.api.netsuite.com
Authorization: OAuth realm="1234567",...
Accept: application/json
```

**Custom RESTlet Response:**
```json
{
  "success": true,
  "customer": {
    "internalId": "1001",
    "entityId": "CUST-1001",
    "companyName": "Smith Investment LLC",
    "email": "john.smith@email.com",
    "salesforceId": "0015e000003CompABC",
    "totalInvestments": 500000.00,
    "investorStatus": "Active",
    "properties": [
      {
        "itemId": "PROP-MIA-101",
        "propertyName": "Miami Beach Condo - Unit 101",
        "purchaseDate": "2026-02-01",
        "purchaseAmount": 500000.00
      }
    ],
    "openInvoices": [
      {
        "invoiceNumber": "INV-2026-001",
        "dueDate": "2026-03-05",
        "amountDue": 400000.00
      }
    ]
  }
}
```

## NetSuite Error Responses

**400 Bad Request:**
```json
{
  "type": "https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.1",
  "title": "Bad Request",
  "status": 400,
  "o:errorCode": "INVALID_KEY_OR_REF",
  "detail": "Invalid customer reference key 99999",
  "o:errorDetails": [
    {
      "detail": "Invalid customer reference key 99999",
      "o:errorCode": "INVALID_KEY_OR_REF"
    }
  ]
}
```

**401 Unauthorized:**
```json
{
  "type": "https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.2",
  "title": "Unauthorized",
  "status": 401,
  "o:errorCode": "INVALID_LOGIN_CREDENTIALS",
  "detail": "Invalid login credentials"
}
```

**404 Not Found:**
```json
{
  "type": "https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.5",
  "title": "Not Found",
  "status": 404,
  "o:errorCode": "RCRD_DSNT_EXIST",
  "detail": "That record does not exist."
}
```

**429 Too Many Requests:**
```json
{
  "type": "https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.30",
  "title": "Too Many Requests",
  "status": 429,
  "o:errorCode": "SSS_REQUEST_LIMIT_EXCEEDED",
  "detail": "You have exceeded the request rate limit"
}
```

---

## API Rate Limits Summary

| System | Rate Limit | Notes |
|--------|-----------|-------|
| **OPERA Cloud** | 100-1000 requests/minute | Varies by subscription tier |
| **Salesforce** | 15,000-100,000 calls/day | Based on edition and licenses |
| **Pardot** | 25,000-100,000 calls/day | Based on edition |
| **NetSuite** | 10-1000 requests/minute | Based on concurrency governance |

---

## Best Practices

### 1. Error Handling
- Always check HTTP status codes
- Implement retry logic with exponential backoff
- Log errors for debugging
- Handle rate limit errors gracefully

### 2. Authentication
- Securely store credentials and tokens
- Implement token refresh logic
- Use service accounts for integrations
- Rotate credentials regularly

### 3. Performance
- Use bulk/batch APIs when processing multiple records
- Implement pagination for large datasets
- Cache frequently accessed data
- Use query filters to minimize data transfer

### 4. Data Management
- Use external IDs for reliable record matching
- Validate data before API calls
- Implement idempotent operations
- Handle duplicates appropriately

### 5. Monitoring
- Track API usage against limits
- Monitor response times
- Set up alerts for failures
- Log all API interactions

---

## Quick Reference: Common Operations

### Create a Contact/Customer
1. **OPERA:** `POST /crm/v1/hotels/{hotelId}/profiles`
2. **Salesforce:** `POST /services/data/v60.0/sobjects/Contact`
3. **Pardot:** `POST /api/v5/objects/prospects`
4. **NetSuite:** `POST /record/v1/customer`

### Update a Contact/Customer
1. **OPERA:** `PUT /crm/v1/hotels/{hotelId}/profiles/{profileId}`
2. **Salesforce:** `PATCH /services/data/v60.0/sobjects/Contact/{Id}`
3. **Pardot:** `PATCH /api/v5/objects/prospects/{id}`
4. **NetSuite:** `PATCH /record/v1/customer/{internalId}`

### Search/Query Records
1. **OPERA:** `GET /crm/v1/hotels/{hotelId}/profiles?email=...`
2. **Salesforce:** `GET /services/data/v60.0/query?q=SELECT...`
3. **Pardot:** `GET /api/v5/objects/prospects?...`
4. **NetSuite:** `POST /query/v1/suiteql` (SuiteQL)

---

*This API reference guide provides practical examples for integrating OPERA, Salesforce, Pardot, and NetSuite systems.*
