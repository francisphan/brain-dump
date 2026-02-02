# OPERA Cloud Business Events - Examples

A comprehensive guide to OPERA Cloud Business Events with real payload examples from the event bus.

---

## Overview

**OPERA Cloud Business Events** are real-time notifications pushed from OPERA when specific actions occur in the system. These events enable event-driven architectures and real-time integrations.

### Event Delivery Methods

1. **Webhooks** - HTTP POST to your endpoint
2. **OHIP Event Bus** - Oracle Hospitality Integration Platform message queue
3. **Platform Events** - Streaming API subscriptions

### Event Structure

All OPERA business events follow a standard structure:

```json
{
  "eventType": "EventName",
  "eventId": "unique-event-id",
  "eventTime": "2026-02-02T10:30:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    // Event-specific payload
  }
}
```

---

## Guest Profile Events

### 1. ProfileCreated

Triggered when a new guest profile is created in OPERA.

**Event Type:** `ProfileCreated`

**Example Payload:**

```json
{
  "eventType": "ProfileCreated",
  "eventId": "evt_1234567890abcdef",
  "eventTime": "2026-02-02T10:30:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
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
        "emailType": "BUSINESS",
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
        "bedType": "KING",
        "language": "EN"
      },
      "membershipInfo": {
        "membershipNumber": "123456789",
        "membershipLevel": "GOLD",
        "membershipType": "LOYALTY",
        "points": 0,
        "enrollmentDate": "2026-02-02"
      },
      "vipStatus": "REGULAR",
      "dateOfBirth": "1985-03-20",
      "nationality": "USA",
      "createdBy": "frontdesk.user@hotel.com",
      "createdDateTime": "2026-02-02T10:30:00.000Z",
      "lastModifiedBy": "frontdesk.user@hotel.com",
      "lastModifiedDateTime": "2026-02-02T10:30:00.000Z"
    },
    "createdSource": {
      "sourceType": "WEB_BOOKING",
      "sourceId": "WEB-12345",
      "userId": "frontdesk.user@hotel.com"
    }
  }
}
```

### 2. ProfileUpdated

Triggered when an existing guest profile is modified.

**Event Type:** `ProfileUpdated`

**Example Payload:**

```json
{
  "eventType": "ProfileUpdated",
  "eventId": "evt_2345678901bcdefg",
  "eventTime": "2026-02-02T14:45:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "profile": {
      "profileId": "12345678",
      "profileType": "GUEST",
      "nameInfo": {
        "givenName": "John",
        "surname": "Smith"
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
      "lastModifiedBy": "manager.user@hotel.com",
      "lastModifiedDateTime": "2026-02-02T14:45:00.000Z"
    },
    "changedFields": [
      {
        "fieldName": "emailAddress",
        "oldValue": "john.smith@email.com",
        "newValue": "john.smith.new@email.com"
      },
      {
        "fieldName": "phoneNumber",
        "oldValue": "+1-555-123-4567",
        "newValue": "+1-555-999-8888"
      },
      {
        "fieldName": "vipStatus",
        "oldValue": "REGULAR",
        "newValue": "PLATINUM"
      }
    ],
    "updateSource": {
      "sourceType": "MANUAL",
      "userId": "manager.user@hotel.com",
      "updateReason": "VIP upgrade"
    }
  }
}
```

### 3. ProfileMerged

Triggered when duplicate profiles are merged.

**Event Type:** `ProfileMerged`

**Example Payload:**

```json
{
  "eventType": "ProfileMerged",
  "eventId": "evt_3456789012cdefgh",
  "eventTime": "2026-02-02T16:20:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "survivingProfile": {
      "profileId": "12345678",
      "nameInfo": {
        "givenName": "John",
        "surname": "Smith"
      },
      "emailAddress": "john.smith@email.com"
    },
    "mergedProfiles": [
      {
        "profileId": "87654321",
        "nameInfo": {
          "givenName": "J.",
          "surname": "Smith"
        },
        "emailAddress": "jsmith@email.com",
        "mergedDateTime": "2026-02-02T16:20:00.000Z"
      }
    ],
    "mergeReason": "DUPLICATE_PROFILE",
    "mergedBy": "admin.user@hotel.com",
    "dataRetained": [
      "All reservations from merged profile",
      "Stay history",
      "Preferences"
    ]
  }
}
```

---

## Reservation Events

### 4. ReservationCreated

Triggered when a new reservation is created.

**Event Type:** `ReservationCreated`

**Example Payload:**

```json
{
  "eventType": "ReservationCreated",
  "eventId": "evt_4567890123defghi",
  "eventTime": "2026-02-02T11:15:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
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
        "phoneNumber": "+1-555-123-4567",
        "vipStatus": "PLATINUM",
        "membershipNumber": "123456789",
        "membershipLevel": "GOLD"
      },
      "stayInfo": {
        "arrivalDate": "2026-03-15",
        "departureDate": "2026-03-18",
        "numberOfNights": 3,
        "numberOfAdults": 2,
        "numberOfChildren": 1,
        "childAges": [8],
        "arrivalTime": "15:00",
        "departureTime": "11:00"
      },
      "roomInfo": {
        "roomType": "KING",
        "roomTypeDescription": "Deluxe King Room",
        "roomNumber": "",
        "numberOfRooms": 1,
        "roomFeatures": ["OCEAN_VIEW", "BALCONY"],
        "roomRate": {
          "rateCode": "BAR",
          "ratePlan": "Best Available Rate",
          "rateType": "DAILY",
          "currency": "USD",
          "amountBeforeTax": 250.00,
          "taxAmount": 37.50,
          "totalAmount": 287.50,
          "averageDailyRate": 287.50
        }
      },
      "bookingInfo": {
        "bookingDate": "2026-02-02T11:15:00.000Z",
        "bookingSource": "DIRECT",
        "bookingChannel": "WEBSITE",
        "bookingChannelCode": "WEB",
        "marketCode": "LEISURE",
        "sourceCode": "WEB",
        "travelAgentId": "",
        "companyId": "",
        "groupCode": "",
        "bookedBy": "john.smith@email.com"
      },
      "guaranteeInfo": {
        "guaranteeType": "CREDIT_CARD",
        "guaranteeCode": "CC",
        "creditCard": {
          "cardType": "VISA",
          "cardNumber": "************1234",
          "cardHolderName": "John Smith",
          "expirationDate": "12/28",
          "billingAddress": {
            "street": "123 Main Street",
            "city": "Miami",
            "state": "FL",
            "postalCode": "33101",
            "country": "USA"
          }
        }
      },
      "specialRequests": [
        "High floor preferred",
        "King bed required",
        "Late checkout if possible"
      ],
      "packages": [
        {
          "packageCode": "BREAKFAST",
          "packageName": "Daily Breakfast for 2",
          "packageAmount": 50.00,
          "quantity": 3
        }
      ],
      "totalRevenue": {
        "roomRevenue": 750.00,
        "packageRevenue": 150.00,
        "taxAmount": 112.50,
        "totalAmount": 1012.50,
        "currency": "USD",
        "depositAmount": 287.50,
        "balanceDue": 725.00
      },
      "cancelPolicy": {
        "policyCode": "24HR",
        "policyDescription": "Cancel 24 hours before arrival",
        "cancellationDeadline": "2026-03-14T15:00:00.000Z",
        "penaltyAmount": 287.50
      },
      "createdBy": "system@booking.com",
      "createdDateTime": "2026-02-02T11:15:00.000Z",
      "lastModifiedDateTime": "2026-02-02T11:15:00.000Z"
    },
    "creationContext": {
      "createdVia": "WEBSITE",
      "userAgent": "Mozilla/5.0...",
      "ipAddress": "192.168.1.100",
      "referralSource": "GOOGLE"
    }
  }
}
```

### 5. ReservationModified

Triggered when a reservation is changed.

**Event Type:** `ReservationModified`

**Example Payload:**

```json
{
  "eventType": "ReservationModified",
  "eventId": "evt_5678901234efghij",
  "eventTime": "2026-02-05T09:30:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "reservation": {
      "reservationId": "RSV123456",
      "confirmationNumber": "CONF789012",
      "reservationStatus": "RESERVED",
      "guestInfo": {
        "profileId": "12345678",
        "guestName": "John Smith"
      },
      "stayInfo": {
        "arrivalDate": "2026-03-15",
        "departureDate": "2026-03-19",
        "numberOfNights": 4,
        "numberOfAdults": 2,
        "numberOfChildren": 1
      },
      "roomInfo": {
        "roomType": "SUITE",
        "roomRate": {
          "rateCode": "BAR",
          "amountBeforeTax": 350.00,
          "totalAmount": 402.50
        }
      },
      "totalRevenue": {
        "roomRevenue": 1400.00,
        "totalAmount": 1610.00
      },
      "lastModifiedBy": "reservations@hotel.com",
      "lastModifiedDateTime": "2026-02-05T09:30:00.000Z"
    },
    "modifications": [
      {
        "fieldName": "departureDate",
        "oldValue": "2026-03-18",
        "newValue": "2026-03-19",
        "changedBy": "reservations@hotel.com",
        "changeReason": "Guest requested extension"
      },
      {
        "fieldName": "roomType",
        "oldValue": "KING",
        "newValue": "SUITE",
        "changedBy": "reservations@hotel.com",
        "changeReason": "Upgrade requested"
      }
    ],
    "modificationSource": {
      "sourceType": "PHONE",
      "userId": "reservations@hotel.com",
      "notes": "Guest called to extend stay and upgrade room"
    }
  }
}
```

### 6. ReservationCancelled

Triggered when a reservation is cancelled.

**Event Type:** `ReservationCancelled`

**Example Payload:**

```json
{
  "eventType": "ReservationCancelled",
  "eventId": "evt_6789012345fghijk",
  "eventTime": "2026-02-10T13:45:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "reservation": {
      "reservationId": "RSV123457",
      "confirmationNumber": "CONF789013",
      "reservationStatus": "CANCELLED",
      "guestInfo": {
        "profileId": "87654321",
        "guestName": "Jane Doe",
        "emailAddress": "jane.doe@email.com"
      },
      "stayInfo": {
        "arrivalDate": "2026-03-20",
        "departureDate": "2026-03-23",
        "numberOfNights": 3
      },
      "roomInfo": {
        "roomType": "QUEEN",
        "roomRate": {
          "totalAmount": 230.00
        }
      },
      "cancellationInfo": {
        "cancellationNumber": "CXL98765",
        "cancellationDate": "2026-02-10T13:45:00.000Z",
        "cancellationReason": "GUEST_REQUEST",
        "cancellationReasonText": "Guest changed travel plans",
        "cancelledBy": "jane.doe@email.com",
        "cancellationSource": "WEBSITE",
        "refundAmount": 0.00,
        "penaltyAmount": 230.00,
        "penaltyApplied": true,
        "penaltyReason": "Cancelled within 24 hours of arrival"
      },
      "originalBookingDate": "2026-01-25T14:20:00.000Z"
    },
    "cancellationContext": {
      "daysBeforeArrival": 38,
      "withinCancellationPolicy": false,
      "refundable": false
    }
  }
}
```

---

## Guest Stay Events

### 7. GuestCheckIn

Triggered when a guest checks in.

**Event Type:** `GuestCheckIn`

**Example Payload:**

```json
{
  "eventType": "GuestCheckIn",
  "eventId": "evt_7890123456ghijkl",
  "eventTime": "2026-03-15T15:30:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "reservation": {
      "reservationId": "RSV123456",
      "confirmationNumber": "CONF789012",
      "reservationStatus": "IN_HOUSE",
      "guestInfo": {
        "profileId": "12345678",
        "guestName": "John Smith",
        "emailAddress": "john.smith@email.com",
        "phoneNumber": "+1-555-123-4567",
        "vipStatus": "PLATINUM",
        "membershipNumber": "123456789"
      },
      "stayInfo": {
        "arrivalDate": "2026-03-15",
        "departureDate": "2026-03-19",
        "numberOfNights": 4,
        "numberOfAdults": 2,
        "numberOfChildren": 1,
        "actualArrivalTime": "2026-03-15T15:30:00.000Z"
      },
      "roomInfo": {
        "roomType": "SUITE",
        "roomNumber": "1205",
        "roomDescription": "Ocean View Suite",
        "floor": 12,
        "building": "Tower A",
        "roomRate": {
          "rateCode": "BAR",
          "totalAmount": 402.50
        }
      },
      "checkInInfo": {
        "checkInDateTime": "2026-03-15T15:30:00.000Z",
        "checkInType": "FRONT_DESK",
        "checkInMethod": "STANDARD",
        "checkedInBy": "frontdesk@hotel.com",
        "registrationCardSigned": true,
        "idVerified": true,
        "idType": "DRIVERS_LICENSE",
        "creditCardOnFile": true,
        "keyCardsIssued": 2,
        "keyCardNumbers": ["KEY-12050001", "KEY-12050002"]
      },
      "guestRequests": [
        {
          "requestType": "EXTRA_PILLOWS",
          "requestStatus": "COMPLETED",
          "requestedAt": "2026-03-15T15:35:00.000Z",
          "completedAt": "2026-03-15T15:50:00.000Z"
        }
      ],
      "welcomeGiftProvided": true,
      "totalRevenue": {
        "roomRevenue": 1400.00,
        "totalAmount": 1610.00,
        "depositAmount": 402.50,
        "balanceDue": 1207.50
      }
    },
    "checkInContext": {
      "earlyCheckIn": false,
      "preCheckedIn": false,
      "mobileCheckIn": false,
      "queueWaitTime": "5 minutes"
    }
  }
}
```

### 8. GuestCheckOut

Triggered when a guest checks out.

**Event Type:** `GuestCheckOut`

**Example Payload:**

```json
{
  "eventType": "GuestCheckOut",
  "eventId": "evt_8901234567hijklm",
  "eventTime": "2026-03-19T11:15:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "reservation": {
      "reservationId": "RSV123456",
      "confirmationNumber": "CONF789012",
      "reservationStatus": "CHECKED_OUT",
      "guestInfo": {
        "profileId": "12345678",
        "guestName": "John Smith",
        "emailAddress": "john.smith@email.com",
        "vipStatus": "PLATINUM",
        "membershipNumber": "123456789"
      },
      "stayInfo": {
        "arrivalDate": "2026-03-15",
        "departureDate": "2026-03-19",
        "numberOfNights": 4,
        "actualCheckInTime": "2026-03-15T15:30:00.000Z",
        "actualCheckOutTime": "2026-03-19T11:15:00.000Z"
      },
      "roomInfo": {
        "roomNumber": "1205",
        "roomType": "SUITE"
      },
      "checkOutInfo": {
        "checkOutDateTime": "2026-03-19T11:15:00.000Z",
        "checkOutType": "FRONT_DESK",
        "checkedOutBy": "frontdesk@hotel.com",
        "lateCheckOut": true,
        "lateCheckOutCharge": 50.00,
        "lateCheckOutTime": "11:15",
        "standardCheckOutTime": "11:00",
        "keyCardsReturned": 2,
        "minibarCharges": true,
        "outstandingBalance": 0.00
      },
      "finalBill": {
        "roomRevenue": 1400.00,
        "foodAndBeverage": 250.00,
        "minibar": 45.00,
        "spa": 150.00,
        "parking": 80.00,
        "lateCheckOutCharge": 50.00,
        "otherCharges": 25.00,
        "subtotal": 2000.00,
        "tax": 300.00,
        "totalAmount": 2300.00,
        "paymentsReceived": 2300.00,
        "balanceDue": 0.00,
        "paymentMethod": "CREDIT_CARD",
        "currency": "USD"
      },
      "loyaltyPoints": {
        "pointsEarned": 2300,
        "pointsRedeemed": 0,
        "newBalance": 17300,
        "tierStatus": "GOLD",
        "tierMaintained": true
      },
      "feedback": {
        "surveyRequested": true,
        "surveySentTo": "john.smith@email.com",
        "surveySentAt": "2026-03-19T11:20:00.000Z"
      }
    },
    "checkOutContext": {
      "expressSCheckOut": false,
      "mobileCheckOut": false,
      "stayDuration": "4 nights",
      "totalSpend": 2300.00,
      "repeatGuest": true,
      "totalStays": 16
    }
  }
}
```

---

## Housekeeping Events

### 9. RoomStatusChanged

Triggered when room status changes.

**Event Type:** `RoomStatusChanged`

**Example Payload:**

```json
{
  "eventType": "RoomStatusChanged",
  "eventId": "evt_9012345678ijklmn",
  "eventTime": "2026-03-19T14:30:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "room": {
      "roomNumber": "1205",
      "roomType": "SUITE",
      "floor": 12,
      "building": "Tower A",
      "roomFeatures": ["OCEAN_VIEW", "BALCONY", "KING_BED"],
      "previousStatus": "DIRTY",
      "currentStatus": "CLEAN",
      "housekeepingStatus": "INSPECTED",
      "frontOfficeStatus": "VACANT_CLEAN",
      "occupancyStatus": "VACANT",
      "condition": "GOOD",
      "outOfOrder": false,
      "outOfService": false
    },
    "statusChange": {
      "changedBy": "housekeeper.mary@hotel.com",
      "changedAt": "2026-03-19T14:30:00.000Z",
      "changeReason": "CHECKOUT_CLEANING_COMPLETE",
      "inspectedBy": "supervisor.jane@hotel.com",
      "inspectedAt": "2026-03-19T14:25:00.000Z",
      "cleaningDuration": "45 minutes",
      "inspectionPassed": true
    },
    "maintenance": {
      "maintenanceRequired": false,
      "maintenanceNotes": ""
    },
    "availability": {
      "availableForAssignment": true,
      "blockedReason": "",
      "nextArrival": "2026-03-20",
      "nextReservation": "RSV123460"
    }
  }
}
```

### 10. HousekeepingTaskCompleted

Triggered when a housekeeping task is completed.

**Event Type:** `HousekeepingTaskCompleted`

**Example Payload:**

```json
{
  "eventType": "HousekeepingTaskCompleted",
  "eventId": "evt_0123456789jklmno",
  "eventTime": "2026-03-19T14:25:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "task": {
      "taskId": "HK-20260319-1205",
      "taskType": "CHECKOUT_CLEAN",
      "taskStatus": "COMPLETED",
      "priority": "HIGH",
      "roomNumber": "1205",
      "assignedTo": {
        "employeeId": "EMP-4567",
        "employeeName": "Mary Johnson",
        "employeeEmail": "housekeeper.mary@hotel.com"
      },
      "assignedAt": "2026-03-19T13:00:00.000Z",
      "startedAt": "2026-03-19T13:40:00.000Z",
      "completedAt": "2026-03-19T14:25:00.000Z",
      "duration": "45 minutes",
      "notes": "Deep cleaned all areas. Restocked amenities.",
      "itemsUsed": [
        {
          "item": "Shampoo",
          "quantity": 2
        },
        {
          "item": "Towels",
          "quantity": 6
        },
        {
          "item": "Bed Linens",
          "quantity": 1
        }
      ]
    },
    "inspection": {
      "inspected": true,
      "inspectedBy": {
        "employeeId": "EMP-7890",
        "employeeName": "Jane Supervisor",
        "employeeEmail": "supervisor.jane@hotel.com"
      },
      "inspectionResult": "PASSED",
      "inspectionNotes": "Excellent work. Room ready for next guest.",
      "inspectionDateTime": "2026-03-19T14:25:00.000Z"
    },
    "nextReservation": {
      "reservationId": "RSV123460",
      "arrivalDate": "2026-03-20",
      "arrivalTime": "15:00"
    }
  }
}
```

---

## Payment Events

### 11. PaymentReceived

Triggered when a payment is received.

**Event Type:** `PaymentReceived`

**Example Payload:**

```json
{
  "eventType": "PaymentReceived",
  "eventId": "evt_1234567890klmnop",
  "eventTime": "2026-03-19T11:18:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "payment": {
      "paymentId": "PAY-789012",
      "reservationId": "RSV123456",
      "confirmationNumber": "CONF789012",
      "guestInfo": {
        "profileId": "12345678",
        "guestName": "John Smith"
      },
      "paymentDetails": {
        "paymentMethod": "CREDIT_CARD",
        "paymentType": "FINAL_SETTLEMENT",
        "amount": 2300.00,
        "currency": "USD",
        "paymentDate": "2026-03-19T11:18:00.000Z",
        "transactionId": "TXN-ABC123DEF456",
        "authorizationCode": "AUTH789012",
        "cardType": "VISA",
        "cardNumber": "************1234",
        "cardHolderName": "John Smith",
        "expirationDate": "12/28"
      },
      "billDetails": {
        "folioNumber": "FOLIO-123456",
        "totalBill": 2300.00,
        "previousPayments": 0.00,
        "thisPayment": 2300.00,
        "remainingBalance": 0.00
      },
      "paymentBreakdown": [
        {
          "description": "Room charges",
          "amount": 1400.00
        },
        {
          "description": "Food & Beverage",
          "amount": 250.00
        },
        {
          "description": "Minibar",
          "amount": 45.00
        },
        {
          "description": "Spa",
          "amount": 150.00
        },
        {
          "description": "Parking",
          "amount": 80.00
        },
        {
          "description": "Late checkout",
          "amount": 50.00
        },
        {
          "description": "Other charges",
          "amount": 25.00
        },
        {
          "description": "Tax",
          "amount": 300.00
        }
      ],
      "processedBy": "frontdesk@hotel.com",
      "receiptNumber": "RCPT-789012",
      "receiptSent": true,
      "receiptSentTo": "john.smith@email.com"
    },
    "paymentContext": {
      "paymentAtCheckOut": true,
      "autoSettlement": false,
      "manualEntry": false,
      "paymentTerminal": "TERMINAL-03"
    }
  }
}
```

### 12. RefundProcessed

Triggered when a refund is processed.

**Event Type:** `RefundProcessed`

**Example Payload:**

```json
{
  "eventType": "RefundProcessed",
  "eventId": "evt_2345678901lmnopq",
  "eventTime": "2026-03-20T10:45:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "refund": {
      "refundId": "REF-345678",
      "originalPaymentId": "PAY-789011",
      "reservationId": "RSV123458",
      "confirmationNumber": "CONF789014",
      "cancellationNumber": "CXL98766",
      "guestInfo": {
        "profileId": "11111111",
        "guestName": "Alice Williams",
        "emailAddress": "alice.williams@email.com"
      },
      "refundDetails": {
        "refundMethod": "CREDIT_CARD",
        "refundAmount": 200.00,
        "currency": "USD",
        "refundDate": "2026-03-20T10:45:00.000Z",
        "refundReason": "CANCELLATION_REFUND",
        "originalChargeAmount": 250.00,
        "cancellationFee": 50.00,
        "netRefund": 200.00,
        "transactionId": "REF-TXN-XYZ789",
        "cardType": "MASTERCARD",
        "cardNumber": "************5678"
      },
      "cancellationInfo": {
        "cancellationDate": "2026-03-18T14:30:00.000Z",
        "originalArrivalDate": "2026-03-22",
        "daysBeforeArrival": 4,
        "withinPolicy": true
      },
      "processedBy": "finance@hotel.com",
      "approvedBy": "manager@hotel.com",
      "refundReceiptSent": true,
      "refundReceiptSentTo": "alice.williams@email.com"
    },
    "refundContext": {
      "autoRefund": false,
      "manualApprovalRequired": true,
      "processingTime": "3-5 business days"
    }
  }
}
```

---

## Block/Group Events

### 13. BlockCreated

Triggered when a group block is created.

**Event Type:** `BlockCreated`

**Example Payload:**

```json
{
  "eventType": "BlockCreated",
  "eventId": "evt_3456789012mnopqr",
  "eventTime": "2026-02-15T09:00:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "block": {
      "blockId": "BLK12345",
      "blockCode": "CORP2026",
      "blockName": "Tech Conference 2026",
      "blockType": "GROUP",
      "blockStatus": "DEFINITE",
      "dateRange": {
        "startDate": "2026-06-10",
        "endDate": "2026-06-14",
        "numberOfNights": 4
      },
      "accountInfo": {
        "accountName": "Tech Corp Inc",
        "accountId": "ACC789",
        "primaryContact": {
          "contactId": "CONT456",
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
          "roomsSold": 0,
          "roomsAvailable": 50,
          "rateAmount": 199.00
        },
        {
          "date": "2026-06-11",
          "roomType": "KING",
          "roomsBlocked": 50,
          "roomsSold": 0,
          "roomsAvailable": 50,
          "rateAmount": 199.00
        },
        {
          "date": "2026-06-12",
          "roomType": "KING",
          "roomsBlocked": 50,
          "roomsSold": 0,
          "roomsAvailable": 50,
          "rateAmount": 199.00
        },
        {
          "date": "2026-06-13",
          "roomType": "KING",
          "roomsBlocked": 50,
          "roomsSold": 0,
          "roomsAvailable": 50,
          "rateAmount": 199.00
        }
      ],
      "totalRoomNights": {
        "blocked": 200,
        "sold": 0,
        "available": 200
      },
      "rateInfo": {
        "rateCode": "GROUP",
        "ratePlan": "Corporate Group Rate",
        "currency": "USD",
        "rateType": "FLAT"
      },
      "cutoffDate": "2026-05-10",
      "attritionRate": 10,
      "minimumStay": 1,
      "maximumStay": 5,
      "contractInfo": {
        "contractSigned": false,
        "contractDate": "",
        "depositRequired": true,
        "depositAmount": 10000.00,
        "depositDueDate": "2026-03-15"
      },
      "createdBy": "sales@hotel.com",
      "createdDateTime": "2026-02-15T09:00:00.000Z"
    },
    "salesInfo": {
      "salesManager": "sales.manager@hotel.com",
      "salesforceOpportunityId": "0065e000001OppXYZ",
      "expectedRevenue": 39800.00,
      "commissionable": false
    }
  }
}
```

### 14. BlockPickup

Triggered when reservations are made against a block (pickup).

**Event Type:** `BlockPickup`

**Example Payload:**

```json
{
  "eventType": "BlockPickup",
  "eventId": "evt_4567890123nopqrs",
  "eventTime": "2026-04-15T14:20:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "block": {
      "blockId": "BLK12345",
      "blockCode": "CORP2026",
      "blockName": "Tech Conference 2026"
    },
    "reservation": {
      "reservationId": "RSV456789",
      "confirmationNumber": "CONF987654",
      "guestName": "Tom Anderson",
      "arrivalDate": "2026-06-10",
      "departureDate": "2026-06-14",
      "numberOfNights": 4,
      "roomType": "KING",
      "numberOfRooms": 1
    },
    "pickupSummary": {
      "totalRoomsBlocked": 200,
      "totalRoomsSold": 35,
      "totalRoomsAvailable": 165,
      "pickupPercentage": 17.5,
      "pickupByDate": [
        {
          "date": "2026-06-10",
          "blocked": 50,
          "sold": 10,
          "available": 40
        },
        {
          "date": "2026-06-11",
          "blocked": 50,
          "sold": 9,
          "available": 41
        },
        {
          "date": "2026-06-12",
          "blocked": 50,
          "sold": 8,
          "available": 42
        },
        {
          "date": "2026-06-13",
          "blocked": 50,
          "sold": 8,
          "available": 42
        }
      ]
    },
    "daysUntilCutoff": 25,
    "onPace": true,
    "forecastedFinalPickup": 180
  }
}
```

---

## Rate and Inventory Events

### 15. RateCodeUpdated

Triggered when a rate code is modified.

**Event Type:** `RateCodeUpdated`

**Example Payload:**

```json
{
  "eventType": "RateCodeUpdated",
  "eventId": "evt_5678901234opqrst",
  "eventTime": "2026-02-20T11:00:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "rateCode": {
      "rateCode": "BAR",
      "ratePlan": "Best Available Rate",
      "rateCategory": "STANDARD",
      "currency": "USD",
      "effectiveDateRange": {
        "startDate": "2026-03-01",
        "endDate": "2026-03-31"
      },
      "rateByRoomType": [
        {
          "roomType": "KING",
          "previousRate": 250.00,
          "newRate": 275.00,
          "percentageChange": 10.0
        },
        {
          "roomType": "QUEEN",
          "previousRate": 200.00,
          "newRate": 220.00,
          "percentageChange": 10.0
        },
        {
          "roomType": "SUITE",
          "previousRate": 400.00,
          "newRate": 440.00,
          "percentageChange": 10.0
        }
      ],
      "restrictions": {
        "minimumLengthOfStay": 1,
        "maximumLengthOfStay": 14,
        "closedToArrival": false,
        "closedToDeparture": false,
        "stopSell": false
      },
      "lastModifiedBy": "revenue@hotel.com",
      "lastModifiedDateTime": "2026-02-20T11:00:00.000Z"
    },
    "changeReason": "Seasonal rate adjustment for high season",
    "impactAnalysis": {
      "affectedReservations": 0,
      "futureBookingsImpacted": "Rate change applies to new bookings only"
    }
  }
}
```

### 16. InventoryUpdated

Triggered when room inventory availability changes.

**Event Type:** `InventoryUpdated`

**Example Payload:**

```json
{
  "eventType": "InventoryUpdated",
  "eventId": "evt_6789012345pqrstu",
  "eventTime": "2026-02-20T16:30:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "inventory": {
      "date": "2026-04-15",
      "roomTypes": [
        {
          "roomType": "KING",
          "totalInventory": 100,
          "availableRooms": 5,
          "occupiedRooms": 85,
          "blockedRooms": 10,
          "outOfOrderRooms": 0,
          "occupancyPercentage": 85.0,
          "availableForSale": 5,
          "sellStatus": "AVAILABLE"
        },
        {
          "roomType": "QUEEN",
          "totalInventory": 50,
          "availableRooms": 0,
          "occupiedRooms": 48,
          "blockedRooms": 2,
          "outOfOrderRooms": 0,
          "occupancyPercentage": 96.0,
          "availableForSale": 0,
          "sellStatus": "SOLD_OUT"
        },
        {
          "roomType": "SUITE",
          "totalInventory": 20,
          "availableRooms": 8,
          "occupiedRooms": 10,
          "blockedRooms": 2,
          "outOfOrderRooms": 0,
          "occupancyPercentage": 50.0,
          "availableForSale": 8,
          "sellStatus": "AVAILABLE"
        }
      ],
      "hotelTotals": {
        "totalInventory": 170,
        "totalOccupied": 143,
        "totalAvailable": 13,
        "overallOccupancyPercentage": 84.1
      }
    },
    "updateReason": "New reservation created",
    "updatedBy": "system"
  }
}
```

---

## System Events

### 17. DataSyncRequired

Triggered when a sync is needed (for integrations).

**Event Type:** `DataSyncRequired`

**Example Payload:**

```json
{
  "eventType": "DataSyncRequired",
  "eventId": "evt_7890123456qrstuv",
  "eventTime": "2026-02-20T00:00:00.000Z",
  "hotelId": "HOTEL001",
  "chainCode": "GRAND",
  "source": "OPERA Cloud",
  "version": "1.0",
  "data": {
    "syncInfo": {
      "syncType": "FULL_SYNC",
      "syncReason": "NIGHTLY_AUDIT_COMPLETE",
      "syncTrigger": "SCHEDULED",
      "dataTypes": [
        "GUEST_PROFILES",
        "RESERVATIONS",
        "FOLIOS",
        "ROOM_STATUS"
      ],
      "dateRange": {
        "startDate": "2026-02-19T00:00:00.000Z",
        "endDate": "2026-02-20T00:00:00.000Z"
      },
      "estimatedRecords": {
        "profiles": 150,
        "reservations": 45,
        "folios": 30,
        "roomStatus": 170
      }
    },
    "syncPriority": "NORMAL",
    "syncDeadline": "2026-02-20T06:00:00.000Z"
  }
}
```

---

## Event Consumption Guide

### Webhook Configuration

To receive these events via webhook:

1. **Register Webhook Endpoint** in OPERA Cloud:
   ```
   URL: https://your-server.com/opera-webhook
   Method: POST
   Authentication: Bearer Token or API Key
   ```

2. **Validate Webhook Signature:**
   ```javascript
   const crypto = require('crypto');

   function validateWebhook(payload, signature, secret) {
     const expectedSignature = crypto
       .createHmac('sha256', secret)
       .update(JSON.stringify(payload))
       .digest('hex');

     return signature === expectedSignature;
   }
   ```

3. **Handle Event:**
   ```javascript
   app.post('/opera-webhook', (req, res) => {
     const event = req.body;

     // Validate signature
     const signature = req.headers['x-opera-signature'];
     if (!validateWebhook(event, signature, WEBHOOK_SECRET)) {
       return res.status(401).send('Invalid signature');
     }

     // Process event
     switch(event.eventType) {
       case 'ProfileCreated':
         handleProfileCreated(event.data);
         break;
       case 'ReservationCreated':
         handleReservationCreated(event.data);
         break;
       case 'GuestCheckIn':
         handleGuestCheckIn(event.data);
         break;
       case 'GuestCheckOut':
         handleGuestCheckOut(event.data);
         break;
       default:
         console.log('Unhandled event type:', event.eventType);
     }

     // Acknowledge receipt
     res.status(200).send('OK');
   });
   ```

### Event Filtering

You can filter which events you receive:

```javascript
const SUBSCRIBED_EVENTS = [
  'ProfileCreated',
  'ProfileUpdated',
  'ReservationCreated',
  'GuestCheckIn',
  'GuestCheckOut'
];

function shouldProcessEvent(eventType) {
  return SUBSCRIBED_EVENTS.includes(eventType);
}
```

### Retry Logic

Implement retry logic for failed processing:

```javascript
async function processEventWithRetry(event, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      await processEvent(event);
      return { success: true };
    } catch (error) {
      console.error(`Attempt ${attempt} failed:`, error);

      if (attempt === maxRetries) {
        // Send to dead letter queue
        await sendToDeadLetterQueue(event, error);
        return { success: false, error };
      }

      // Exponential backoff
      await sleep(Math.pow(2, attempt) * 1000);
    }
  }
}
```

---

## Summary

OPERA Cloud Business Events provide real-time notifications for:

- **Profile Events**: ProfileCreated, ProfileUpdated, ProfileMerged
- **Reservation Events**: ReservationCreated, ReservationModified, ReservationCancelled
- **Stay Events**: GuestCheckIn, GuestCheckOut
- **Housekeeping Events**: RoomStatusChanged, HousekeepingTaskCompleted
- **Payment Events**: PaymentReceived, RefundProcessed
- **Block Events**: BlockCreated, BlockPickup
- **Rate/Inventory Events**: RateCodeUpdated, InventoryUpdated
- **System Events**: DataSyncRequired

Use these events to build real-time integrations with Salesforce, Pardot, NetSuite, and other systems without polling APIs.

---

*For access to OPERA Cloud Business Events, contact your Oracle Account Manager or OPERA Cloud administrator.*
