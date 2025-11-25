# API Documentation - טופס הרשמה לחופשת סקי

## Overview

This document describes the API endpoint for receiving ski vacation registration form data.

## Base URL

```
POST https://script.google.com/macros/s/AKfycbyqn2I4Q7P4Z_1k_qUVNQhc3aD6Cl1VYZ6DAEsZg3mBfK1_iGLO4X4zGIJphv9qz-Ll/exec
```

---

## Endpoints

### 1. Get Vacation Dates

Retrieves available vacation dates.

**Request:**
```
GET {BASE_URL}?action=getVacationDates
```

**Response:**
```json
["תאריך 1", "תאריך 2", "תאריך 3"]
```

---

### 2. Submit Registration Form

Submit the complete registration form with passenger details and files.

**Request:**
```
POST {BASE_URL}
Content-Type: application/json
```

**Request Body:**

```json
{
  "vacationDate": "string",
  "passengers": [
    {
      "lastName": "string",
      "firstName": "string",
      "birthDate": "YYYY-MM-DD",
      "passportNumber": "string",
      "gender": "male | female",
      "noFlight": "boolean",
      "frequentFlyer": {
        "hasNumber": "boolean",
        "number": "string | null"
      },
      "skiPass": "boolean",
      "skiLevel": "string"
    }
  ],
  "files": [
    {
      "name": "string",
      "type": "string",
      "data": "base64-encoded-string"
    }
  ],
  "roomType": "standard | deluxe",
  "deluxeFor": "all | some | null",
  "deluxePassengers": ["string"] | null,
  "skiLessons": {
    "kids": {
      "selected": "boolean",
      "participants": ["string"]
    },
    "adults": {
      "selected": "boolean",
      "participants": ["string"]
    },
    "private": {
      "selected": "boolean",
      "participants": ["string"]
    }
  },
  "contactInfo": {
    "address": "string",
    "phone": "string",
    "email": "string",
    "notes": "string | null"
  },
  "paymentMethod": "bank | card",
  "bankTransfer": {
    "date": "YYYY-MM-DD | null",
    "reference": "string | null",
    "amount": "number | null",
    "receiptFiles": [
      {
        "name": "string",
        "type": "string",
        "data": "base64-encoded-string"
      }
    ]
  } | null,
  "creditCard": {
    "cardNumber": "string",
    "cardType": "ויזה | מאסטרקארד | אמריקן אקספרס | דיינרס",
    "expiryDate": "MM/YY",
    "cvv": "string",
    "cardHolderId": "string",
    "cardHolderName": "string"
  } | null,
  "termsAccepted": "boolean",
  "signature": "base64-encoded-png-string",
  "submissionDate": "DD/MM/YYYY"
}
```

---

## Data Models

### Passenger Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `lastName` | string | Yes | Last name in English (as in passport) - English letters only |
| `firstName` | string | Yes | First name in English (as in passport) - English letters only |
| `birthDate` | string | Yes | Date of birth in YYYY-MM-DD format |
| `passportNumber` | string | Yes | Passport number |
| `gender` | string | Yes | Gender: "male" or "female" |
| `noFlight` | boolean | No | True if passenger opts out of flight (350€ credit) |
| `frequentFlyer` | object | No | Frequent flyer information |
| `frequentFlyer.hasNumber` | boolean | Yes | Whether passenger has frequent flyer number |
| `frequentFlyer.number` | string | Conditional | Frequent flyer number (required if hasNumber is true) |
| `skiPass` | boolean | Yes | Whether passenger needs ski pass |
| `skiLevel` | string | Conditional | Ski level (required if skiPass is true): "1" (beginner), "2" (beginner+), "3" (intermediate), "4" (advanced), "SB" (snowboard) |

### File Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Original file name |
| `type` | string | Yes | MIME type (e.g., "image/jpeg", "application/pdf") |
| `data` | string | Yes | Base64-encoded file content |

### Room Type

| Value | Description | Price |
|-------|-------------|-------|
| `standard` | Standard room | Included |
| `deluxe` | Deluxe room with view | +300€ per week |

### Deluxe Room Options

| Field | Type | Description |
|-------|------|-------------|
| `deluxeFor` | string | "all" (all passengers) or "some" (selected passengers) |
| `deluxePassengers` | array | List of passenger names for deluxe room (if deluxeFor is "some") |

### Ski Lessons

| Lesson Type | Description | Price |
|-------------|-------------|-------|
| `kids` | Kids/Youth group - 5 full days (10:00-16:00) | 430€ |
| `adults` | Adults group - 3 half days (Sun-Tue 10:00-13:00) + 1 full day (Thu 10:00-16:00) | 260€ |
| `private` | Private lessons - minimum 3 hours | 65€/hour (+15€ per additional person) |

Each ski lesson object contains:
| Field | Type | Description |
|-------|------|-------------|
| `selected` | boolean | Whether this lesson type is selected |
| `participants` | array | List of participant names from registered passengers |

### Contact Info Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `address` | string | Yes | Full address (street, city, postal code) |
| `phone` | string | Yes | Mobile phone number (format: 05x-xxxxxxx) |
| `email` | string | Yes | Email address |
| `notes` | string | No | Special requests and notes |

### Payment Method

| Field | Type | Description |
|-------|------|-------------|
| `paymentMethod` | string | "bank" for bank transfer, "card" for credit card |

### Bank Transfer Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `date` | string | No | Date of bank transfer (YYYY-MM-DD) |
| `reference` | string | No | Bank transfer reference number |
| `amount` | number | No | Amount transferred in EUR |
| `receiptFiles` | array | No | Array of receipt file objects (images/PDFs of transfer confirmation) |

### Credit Card Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `cardNumber` | string | Yes | Credit card number |
| `cardType` | string | Yes | Card type (ויזה, מאסטרקארד, אמריקן אקספרס, דיינרס) |
| `expiryDate` | string | Yes | Card expiry date (MM/YY format) |
| `cvv` | string | Yes | Card CVV code |
| `cardHolderId` | string | Yes | ID number of card holder |
| `cardHolderName` | string | Yes | Name of card holder |

**Note:** American Express and Diners cards have an additional 1.5% fee.

---

## Response Format

### Success Response

```json
{
  "success": true,
  "message": "הטופס נשלח בהצלחה",
  "registrationId": "string"
}
```

### Error Response

```json
{
  "success": false,
  "error": {
    "code": "string",
    "message": "string",
    "details": "string | null"
  }
}
```

---

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `VALIDATION_ERROR` | 400 | Required fields are missing or invalid |
| `INVALID_DATE` | 400 | Selected vacation date is not available |
| `INVALID_EMAIL` | 400 | Email format is invalid |
| `INVALID_PHONE` | 400 | Phone number format is invalid |
| `INVALID_NAME` | 400 | Name contains non-English characters |
| `MISSING_SIGNATURE` | 400 | Signature is required but not provided |
| `MISSING_PASSENGERS` | 400 | At least one passenger is required |
| `MISSING_GENDER` | 400 | Gender is required for all passengers |
| `MISSING_SKI_LEVEL` | 400 | Ski level is required when ski pass is selected |
| `MISSING_PAYMENT_METHOD` | 400 | Payment method must be selected |
| `MISSING_LESSON_PARTICIPANTS` | 400 | Participants must be selected for ski lessons |
| `INVALID_PASSPORT` | 400 | Passport information is invalid |
| `FILE_TOO_LARGE` | 400 | Uploaded file exceeds maximum size (10MB) |
| `INVALID_FILE_TYPE` | 400 | File type not supported (only images and PDFs) |
| `SERVER_ERROR` | 500 | Internal server error |

---

## Validation Rules

### Required Fields

1. **Vacation Date** - Must be selected from available dates
2. **At least one passenger** - With all required fields filled
3. **Passport documents** - At least one passport image/PDF per passenger
4. **Contact information** - Address, phone, and email are required
5. **Payment method** - Bank transfer or credit card must be selected
6. **Terms acceptance** - Must be checked
7. **Signature** - Must be provided

### Field Validations

| Field | Validation |
|-------|------------|
| First/Last Name | English letters only (A-Z, a-z), spaces and hyphens allowed |
| Email | Must be valid email format |
| Phone | Must match Israeli mobile format (05x-xxxxxxx or 05xxxxxxxx) |
| Passport Number | Alphanumeric, 5-20 characters |
| Birth Date | Cannot be in the future, passenger must be under 120 years old |
| Gender | Must be "male" or "female" |
| Ski Level | Must be one of: 1, 2, 3, 4, SB (only if ski pass selected) |
| Files | Max 10MB each, only images (jpg, png, gif) or PDF |

---

## Example Request

```javascript
const formData = {
  vacationDate: "15/02/2026 - 22/02/2026",
  passengers: [
    {
      lastName: "COHEN",
      firstName: "DAVID",
      birthDate: "1985-03-15",
      passportNumber: "12345678",
      gender: "male",
      noFlight: false,
      frequentFlyer: {
        hasNumber: true,
        number: "FF123456"
      },
      skiPass: true,
      skiLevel: "3"
    },
    {
      lastName: "COHEN",
      firstName: "SARAH",
      birthDate: "1987-07-22",
      passportNumber: "87654321",
      gender: "female",
      noFlight: false,
      frequentFlyer: {
        hasNumber: false,
        number: null
      },
      skiPass: true,
      skiLevel: "2"
    }
  ],
  files: [
    {
      name: "passport_david.jpg",
      type: "image/jpeg",
      data: "base64encodeddata..."
    },
    {
      name: "passport_sarah.pdf",
      type: "application/pdf",
      data: "base64encodeddata..."
    }
  ],
  roomType: "deluxe",
  deluxeFor: "all",
  deluxePassengers: null,
  skiLessons: {
    kids: {
      selected: false,
      participants: []
    },
    adults: {
      selected: true,
      participants: ["DAVID COHEN", "SARAH COHEN"]
    },
    private: {
      selected: false,
      participants: []
    }
  },
  contactInfo: {
    address: "רחוב הרצל 123, ירושלים, 9123456",
    phone: "058-5396011",
    email: "david@example.com",
    notes: "מבקשים חדר קרוב למעלית"
  },
  paymentMethod: "bank",
  bankTransfer: {
    date: "2025-11-25",
    reference: "TRF123456",
    amount: 1000,
    receiptFiles: [
      {
        name: "transfer_receipt.pdf",
        type: "application/pdf",
        data: "base64encodeddata..."
      }
    ]
  },
  creditCard: null,
  termsAccepted: true,
  signature: "data:image/png;base64,iVBORw0KGgo...",
  submissionDate: "25/11/2025"
};

fetch(BASE_URL, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify(formData)
})
.then(response => response.json())
.then(data => {
  if (data.success) {
    console.log('Registration successful:', data.registrationId);
  } else {
    console.error('Error:', data.error.message);
  }
});
```

---

## Notes for API Developer

1. **File Handling**: Files are sent as Base64-encoded strings. The API should decode and store them appropriately.

2. **Receipt Files**: Bank transfer receipts are separate from passport files and should be stored with the payment information.

3. **Signature**: The signature is a PNG image encoded as a Base64 data URL. It should be stored with the registration.

4. **Participant Selection**: Ski lesson participants and deluxe room passengers are selected from the registered passengers list and identified by name.

5. **Validation**: All validations should be performed server-side. Client-side validation is only for user convenience.

6. **Response Time**: The API should respond within 30 seconds to avoid timeout.

7. **CORS**: Enable CORS for the form's origin domain.

8. **Security**: 
   - Credit card data should be handled according to PCI-DSS standards
   - All data should be transmitted over HTTPS
   - Consider tokenization for sensitive payment data
