# Pet Management Platform - API Specifications

## API Overview

The Pet Management Platform API follows RESTful principles with comprehensive OpenAPI 3.0 documentation. All endpoints require authentication unless otherwise specified.

## Base Configuration

- **Base URL**: `https://api.petmanager.com/v1`
- **Authentication**: Bearer token (JWT)
- **Content Type**: `application/json`
- **Rate Limiting**: 1000 requests/hour for free tier, 10000/hour for premium

## Authentication Endpoints

### POST /auth/register
Register a new user account.

```json
// Request
{
  "email": "user@example.com",
  "password": "SecurePassword123!",
  "firstName": "John",
  "lastName": "Doe",
  "timezone": "America/New_York"
}

// Response (201)
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "subscriptionTier": "free"
    },
    "accessToken": "jwt_token",
    "refreshToken": "refresh_token",
    "expiresIn": 3600
  }
}
```

### POST /auth/login
Authenticate user and receive tokens.

```json
// Request
{
  "email": "user@example.com",
  "password": "SecurePassword123!"
}

// Response (200)
{
  "success": true,
  "data": {
    "user": { /* user object */ },
    "accessToken": "jwt_token",
    "refreshToken": "refresh_token",
    "expiresIn": 3600
  }
}
```

### POST /auth/refresh-token
Refresh access token using refresh token.

```json
// Request
{
  "refreshToken": "refresh_token"
}

// Response (200)
{
  "success": true,
  "data": {
    "accessToken": "new_jwt_token",
    "expiresIn": 3600
  }
}
```

## User Management Endpoints

### GET /users/profile
Get current user's profile information.

```json
// Response (200)
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "phone": "+1234567890",
    "timezone": "America/New_York",
    "subscriptionTier": "premium",
    "profilePhotoUrl": "https://cdn.example.com/profile.jpg",
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-15T10:30:00Z"
  }
}
```

### PUT /users/profile
Update current user's profile information.

```json
// Request
{
  "firstName": "John",
  "lastName": "Smith",
  "phone": "+1234567890",
  "timezone": "America/Los_Angeles"
}

// Response (200)
{
  "success": true,
  "data": {
    /* updated user object */
  }
}
```

## Pet Management Endpoints

### GET /pets
Get all pets for the authenticated user.

```json
// Query Parameters
// ?page=1&limit=20&search=fluffy&species=dog

// Response (200)
{
  "success": true,
  "data": {
    "pets": [
      {
        "id": "uuid",
        "name": "Fluffy",
        "species": "dog",
        "breed": "Golden Retriever",
        "dateOfBirth": "2020-05-15",
        "weightKg": 25.5,
        "microchipId": "123456789012345",
        "profilePhotoUrl": "https://cdn.example.com/pet.jpg",
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-15T10:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

### POST /pets
Create a new pet profile.

```json
// Request
{
  "name": "Buddy",
  "species": "dog",
  "breed": "Labrador",
  "dateOfBirth": "2021-03-20",
  "weightKg": 30.0,
  "microchipId": "987654321098765"
}

// Response (201)
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Buddy",
    "species": "dog",
    "breed": "Labrador",
    "dateOfBirth": "2021-03-20",
    "weightKg": 30.0,
    "microchipId": "987654321098765",
    "profilePhotoUrl": null,
    "createdAt": "2024-01-20T15:00:00Z",
    "updatedAt": "2024-01-20T15:00:00Z"
  }
}
```

### GET /pets/:id
Get detailed information for a specific pet.

```json
// Response (200)
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Fluffy",
    "species": "dog",
    "breed": "Golden Retriever",
    "dateOfBirth": "2020-05-15",
    "weightKg": 25.5,
    "microchipId": "123456789012345",
    "profilePhotoUrl": "https://cdn.example.com/pet.jpg",
    "healthRecords": {
      "total": 15,
      "recent": [
        {
          "id": "uuid",
          "recordType": "vaccination",
          "title": "Annual Vaccination",
          "dateAdministered": "2024-01-15",
          "veterinarian": "Dr. Smith"
        }
      ]
    },
    "upcomingReminders": [
      {
        "id": "uuid",
        "title": "Flea Treatment",
        "reminderDate": "2024-02-01T09:00:00Z"
      }
    ],
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-15T10:30:00Z"
  }
}
```

## Health Records Endpoints

### GET /pets/:petId/health-records
Get health records for a specific pet.

```json
// Query Parameters
// ?page=1&limit=20&type=vaccination&from=2024-01-01&to=2024-12-31

// Response (200)
{
  "success": true,
  "data": {
    "records": [
      {
        "id": "uuid",
        "recordType": "vaccination",
        "title": "Rabies Vaccine",
        "description": "Annual rabies vaccination",
        "dateAdministered": "2024-01-15",
        "veterinarian": "Dr. Smith - City Vet Clinic",
        "cost": 75.00,
        "attachments": [
          {
            "id": "uuid",
            "filename": "vaccination_certificate.pdf",
            "url": "https://cdn.example.com/documents/cert.pdf",
            "type": "application/pdf",
            "size": 245760
          }
        ],
        "createdAt": "2024-01-15T14:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 15,
      "totalPages": 1
    },
    "summary": {
      "totalRecords": 15,
      "recordTypes": {
        "vaccination": 8,
        "medication": 4,
        "visit": 3
      },
      "totalCost": 1250.00
    }
  }
}
```

### POST /pets/:petId/health-records
Create a new health record for a pet.

```json
// Request
{
  "recordType": "vaccination",
  "title": "Distemper Vaccine",
  "description": "DHPP vaccination booster",
  "dateAdministered": "2024-01-20",
  "veterinarian": "Dr. Johnson - Animal Hospital",
  "cost": 65.00,
  "attachments": [
    {
      "filename": "vaccine_record.pdf",
      "url": "https://cdn.example.com/temp/upload123.pdf"
    }
  ]
}

// Response (201)
{
  "success": true,
  "data": {
    "id": "uuid",
    "petId": "pet_uuid",
    "recordType": "vaccination",
    "title": "Distemper Vaccine",
    "description": "DHPP vaccination booster",
    "dateAdministered": "2024-01-20",
    "veterinarian": "Dr. Johnson - Animal Hospital",
    "cost": 65.00,
    "attachments": [
      {
        "id": "uuid",
        "filename": "vaccine_record.pdf",
        "url": "https://cdn.example.com/documents/record456.pdf",
        "type": "application/pdf",
        "size": 156789
      }
    ],
    "createdAt": "2024-01-20T16:45:00Z"
  }
}
```

## Reminders Endpoints

### GET /reminders
Get reminders for the authenticated user.

```json
// Query Parameters
// ?page=1&limit=20&status=pending&petId=uuid&from=2024-01-01

// Response (200)
{
  "success": true,
  "data": {
    "reminders": [
      {
        "id": "uuid",
        "petId": "pet_uuid",
        "petName": "Fluffy",
        "title": "Heartworm Prevention",
        "description": "Monthly heartworm medication",
        "reminderDate": "2024-02-01T09:00:00Z",
        "repeatInterval": "monthly",
        "notificationMethods": ["email", "push"],
        "isCompleted": false,
        "createdAt": "2024-01-01T00:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 5,
      "totalPages": 1
    }
  }
}
```

### POST /reminders
Create a new reminder.

```json
// Request
{
  "petId": "pet_uuid",
  "title": "Flea Treatment",
  "description": "Apply monthly flea treatment",
  "reminderDate": "2024-02-15T10:00:00Z",
  "repeatInterval": "monthly",
  "notificationMethods": ["email", "push", "sms"]
}

// Response (201)
{
  "success": true,
  "data": {
    "id": "uuid",
    "petId": "pet_uuid",
    "title": "Flea Treatment",
    "description": "Apply monthly flea treatment",
    "reminderDate": "2024-02-15T10:00:00Z",
    "repeatInterval": "monthly",
    "notificationMethods": ["email", "push", "sms"],
    "isCompleted": false,
    "createdAt": "2024-01-20T17:30:00Z"
  }
}
```

## File Upload Endpoints

### POST /uploads/presigned-url
Get a presigned URL for direct S3 upload.

```json
// Request
{
  "filename": "pet_photo.jpg",
  "contentType": "image/jpeg",
  "purpose": "pet_profile_photo"
}

// Response (200)
{
  "success": true,
  "data": {
    "uploadUrl": "https://s3.amazonaws.com/bucket/presigned-url",
    "fileKey": "uploads/uuid/pet_photo.jpg",
    "expiresIn": 300,
    "maxFileSize": 10485760,
    "fields": {
      "key": "uploads/uuid/pet_photo.jpg",
      "policy": "encoded_policy",
      "x-amz-algorithm": "AWS4-HMAC-SHA256",
      "x-amz-credential": "credentials",
      "x-amz-date": "20240120T173000Z",
      "x-amz-signature": "signature"
    }
  }
}
```

### POST /uploads/confirm
Confirm successful upload and process the file.

```json
// Request
{
  "fileKey": "uploads/uuid/pet_photo.jpg",
  "purpose": "pet_profile_photo",
  "petId": "pet_uuid"
}

// Response (200)
{
  "success": true,
  "data": {
    "fileId": "uuid",
    "url": "https://cdn.example.com/optimized/pet_photo.jpg",
    "thumbnailUrl": "https://cdn.example.com/thumbnails/pet_photo_thumb.jpg",
    "processed": true
  }
}
```

## Calendar Integration Endpoints

### GET /calendar/events
Get synchronized calendar events.

```json
// Query Parameters
// ?from=2024-01-01&to=2024-01-31&calendar=google

// Response (200)
{
  "success": true,
  "data": {
    "events": [
      {
        "id": "uuid",
        "title": "Vet Appointment - Fluffy",
        "description": "Annual checkup",
        "startTime": "2024-01-25T14:00:00Z",
        "endTime": "2024-01-25T15:00:00Z",
        "location": "City Vet Clinic",
        "petId": "pet_uuid",
        "reminderId": "reminder_uuid",
        "calendarSource": "google",
        "externalId": "google_event_id"
      }
    ]
  }
}
```

### POST /calendar/sync
Trigger calendar synchronization.

```json
// Request
{
  "calendarProvider": "google",
  "syncDirection": "bidirectional"
}

// Response (200)
{
  "success": true,
  "data": {
    "syncId": "uuid",
    "status": "completed",
    "eventsImported": 5,
    "eventsExported": 3,
    "lastSyncTime": "2024-01-20T18:00:00Z"
  }
}
```

## Subscription & Payment Endpoints

### GET /subscription/status
Get current subscription status.

```json
// Response (200)
{
  "success": true,
  "data": {
    "subscriptionTier": "premium",
    "status": "active",
    "currentPeriodStart": "2024-01-01T00:00:00Z",
    "currentPeriodEnd": "2024-02-01T00:00:00Z",
    "cancelAtPeriodEnd": false,
    "features": {
      "maxPets": 10,
      "maxHealthRecords": "unlimited",
      "calendarSync": true,
      "prioritySupport": true,
      "advancedReports": true
    },
    "usage": {
      "petsCount": 3,
      "healthRecordsCount": 45,
      "storageUsedMB": 156.7
    }
  }
}
```

### POST /subscription/upgrade
Upgrade subscription tier.

```json
// Request
{
  "tier": "premium",
  "billingPeriod": "monthly",
  "paymentMethodId": "stripe_payment_method_id"
}

// Response (200)
{
  "success": true,
  "data": {
    "subscriptionId": "stripe_subscription_id",
    "status": "active",
    "tier": "premium",
    "nextBillingDate": "2024-02-20T00:00:00Z",
    "amount": 9.99,
    "currency": "USD"
  }
}
```

## Error Responses

All endpoints return standardized error responses:

```json
// 400 Bad Request
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Valid email address is required"
      }
    ]
  }
}

// 401 Unauthorized
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or expired token"
  }
}

// 403 Forbidden
{
  "success": false,
  "error": {
    "code": "INSUFFICIENT_PERMISSIONS",
    "message": "Premium subscription required for this feature"
  }
}

// 404 Not Found
{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Pet not found"
  }
}

// 429 Too Many Requests
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "API rate limit exceeded",
    "retryAfter": 3600
  }
}

// 500 Internal Server Error
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "An unexpected error occurred",
    "requestId": "uuid"
  }
}
```

## Rate Limiting

Rate limits are applied per API key/user:

- **Free Tier**: 1,000 requests/hour
- **Premium Tier**: 10,000 requests/hour
- **Enterprise Tier**: 100,000 requests/hour

Rate limit information is included in response headers:
- `X-RateLimit-Limit`: Request limit per window
- `X-RateLimit-Remaining`: Remaining requests in current window
- `X-RateLimit-Reset`: Time when the current window resets (Unix timestamp)

## Webhooks

For real-time updates, the API supports webhooks for the following events:

### Pet Events
- `pet.created`
- `pet.updated`
- `pet.deleted`

### Health Record Events
- `health_record.created`
- `health_record.updated`

### Reminder Events
- `reminder.created`
- `reminder.due`
- `reminder.completed`

### Subscription Events
- `subscription.updated`
- `subscription.cancelled`
- `payment.succeeded`
- `payment.failed`

### Webhook Payload Example

```json
{
  "id": "webhook_uuid",
  "event": "reminder.due",
  "timestamp": "2024-01-20T18:30:00Z",
  "data": {
    "reminderId": "uuid",
    "petId": "pet_uuid",
    "userId": "user_uuid",
    "title": "Heartworm Prevention",
    "reminderDate": "2024-01-20T18:30:00Z"
  },
  "signature": "webhook_signature"
}
```

## SDK & Libraries

Official SDKs are available for:
- JavaScript/TypeScript (Node.js & Browser)
- Python
- PHP
- Ruby
- Java
- Swift (iOS)
- Kotlin (Android)

Example usage with JavaScript SDK:

```javascript
import { PetManagerAPI } from '@petmanager/sdk';

const api = new PetManagerAPI({
  apiKey: 'your_api_key',
  baseUrl: 'https://api.petmanager.com/v1'
});

// Get user's pets
const pets = await api.pets.list();

// Create a health record
const healthRecord = await api.healthRecords.create(petId, {
  recordType: 'vaccination',
  title: 'Rabies Vaccine',
  dateAdministered: '2024-01-20',
  veterinarian: 'Dr. Smith'
});
```