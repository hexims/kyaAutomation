# ZLocker API Documentation

## Base URL
```
Production: https://api.zlocker.com/v1
Development: http://localhost:3000/v1
```

## Authentication

All API endpoints require authentication using JWT tokens.

### Getting a Token

**Request:**
```bash
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "token_type": "Bearer",
  "expires_in": 86400,
  "user": {
    "id": "user-123",
    "email": "user@example.com",
    "name": "John Doe"
  }
}
```

### Using the Token

Include the token in the `Authorization` header:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

## Endpoints

### Authentication

#### POST /auth/register
Create a new user account.

**Request:**
```json
{
  "email": "newuser@example.com",
  "password": "SecurePass123!",
  "name": "Jane Doe"
}
```

**Response (201):**
```json
{
  "id": "user-456",
  "email": "newuser@example.com",
  "name": "Jane Doe",
  "created_at": "2024-01-15T10:30:00Z"
}
```

#### POST /auth/logout
Revoke authentication token.

**Request:**
```
Authorization: Bearer <token>
POST /auth/logout
```

**Response (200):**
```json
{
  "message": "Successfully logged out"
}
```

### Documents

#### POST /documents/upload
Upload a new document.

**Request:**
```
Authorization: Bearer <token>
POST /documents/upload
Content-Type: multipart/form-data

File: [binary]
document_type: "national_id"
description: "My National ID"
```

**Response (201):**
```json
{
  "id": "doc-789",
  "user_id": "user-123",
  "document_type": "national_id",
  "file_url": "https://s3.amazonaws.com/zlocker/doc-789.pdf",
  "status": "processing",
  "created_at": "2024-01-15T10:30:00Z"
}
```

#### GET /documents
List all documents for the authenticated user.

**Request:**
```
Authorization: Bearer <token>
GET /documents?limit=10&offset=0
```

**Response (200):**
```json
{
  "total": 5,
  "limit": 10,
  "offset": 0,
  "documents": [
    {
      "id": "doc-789",
      "document_type": "national_id",
      "status": "verified",
      "created_at": "2024-01-15T10:30:00Z"
    }
  ]
}
```

#### GET /documents/{id}
Get details of a specific document.

**Request:**
```
Authorization: Bearer <token>
GET /documents/doc-789
```

**Response (200):**
```json
{
  "id": "doc-789",
  "user_id": "user-123",
  "document_type": "national_id",
  "file_url": "https://s3.amazonaws.com/zlocker/doc-789.pdf",
  "status": "verified",
  "metadata": {
    "ocr_data": {
      "document_number": "123456789",
      "issue_date": "2020-01-01",
      "expiry_date": "2030-01-01"
    }
  },
  "created_at": "2024-01-15T10:30:00Z",
  "verified_at": "2024-01-15T10:35:00Z"
}
```

#### DELETE /documents/{id}
Delete a document.

**Request:**
```
Authorization: Bearer <token>
DELETE /documents/doc-789
```

**Response (204):**
No content

### KYC Verification

#### POST /kyc/verify
Initiate KYC verification process.

**Request:**
```
Authorization: Bearer <token>
POST /kyc/verify
Content-Type: application/json

{
  "first_name": "John",
  "last_name": "Doe",
  "date_of_birth": "1990-01-15",
  "nationality": "US",
  "address": "123 Main St, City, State 12345"
}
```

**Response (201):**
```json
{
  "kyc_id": "kyc-123",
  "user_id": "user-123",
  "status": "pending",
  "created_at": "2024-01-15T10:30:00Z"
}
```

#### GET /kyc/{kyc_id}
Get KYC verification status.

**Request:**
```
Authorization: Bearer <token>
GET /kyc/kyc-123
```

**Response (200):**
```json
{
  "kyc_id": "kyc-123",
  "status": "verified",
  "risk_score": 15,
  "flags": [],
  "verified_at": "2024-01-15T10:35:00Z"
}
```

### Document Sharing

#### POST /sharing/create
Create a shareable link for a document.

**Request:**
```
Authorization: Bearer <token>
POST /sharing/create
Content-Type: application/json

{
  "document_id": "doc-789",
  "expires_in": "7d",
  "permission": "view"
}
```

**Response (201):**
```json
{
  "share_id": "share-456",
  "document_id": "doc-789",
  "share_link": "https://zlocker.com/s/share-456",
  "expires_at": "2024-01-22T10:30:00Z"
}
```

#### GET /sharing/{share_id}
Access a shared document (public endpoint).

**Request:**
```
GET /sharing/share-456
```

**Response (200):**
```json
{
  "document": {
    "id": "doc-789",
    "type": "national_id",
    "shared_by": "John Doe"
  },
  "expires_at": "2024-01-22T10:30:00Z"
}
```

### OCR & Data Extraction

#### POST /ocr/extract
Extract data from an uploaded document.

**Request:**
```
Authorization: Bearer <token>
POST /ocr/extract
Content-Type: multipart/form-data

File: [binary]
document_type: "national_id"
```

**Response (200):**
```json
{
  "data": {
    "full_name": "John Doe",
    "date_of_birth": "1990-01-15",
    "document_number": "123456789",
    "issue_date": "2020-01-01",
    "expiry_date": "2030-01-01"
  },
  "confidence_score": 0.95,
  "processing_time_ms": 1234
}
```

### Face Recognition

#### POST /face/verify
Verify user's face using liveness detection.

**Request:**
```
Authorization: Bearer <token>
POST /face/verify
Content-Type: multipart/form-data

File: [video_or_image]
```

**Response (200):**
```json
{
  "status": "verified",
  "liveness_score": 0.98,
  "matching_score": 0.92,
  "message": "Face verified successfully"
}
```

### Admin API

#### GET /admin/users
List all users (admin only).

**Request:**
```
Authorization: Bearer <admin_token>
GET /admin/users?limit=50&offset=0
```

**Response (200):**
```json
{
  "total": 1500,
  "users": [
    {
      "id": "user-123",
      "email": "user@example.com",
      "created_at": "2024-01-15T10:30:00Z",
      "kyc_status": "verified"
    }
  ]
}
```

## Error Handling

### Error Response Format
```json
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "The request is invalid",
    "details": {
      "field": "email",
      "reason": "Invalid email format"
    }
  }
}
```

### Common Error Codes

| Code | Status | Description |
|------|--------|-------------|
| UNAUTHORIZED | 401 | Invalid or missing authentication token |
| FORBIDDEN | 403 | User does not have permission |
| NOT_FOUND | 404 | Resource not found |
| VALIDATION_ERROR | 400 | Request validation failed |
| RATE_LIMITED | 429 | Too many requests |
| INTERNAL_ERROR | 500 | Server error |

## Rate Limiting

API requests are rate-limited to 100 requests per 15 minutes per user.

**Response Headers:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1705329000
```

## SDK & Libraries

### JavaScript/TypeScript
```bash
npm install @zlocker/sdk
```

```javascript
import ZLocker from '@zlocker/sdk';

const client = new ZLocker({
  apiKey: 'your_api_key',
  baseUrl: 'https://api.zlocker.com/v1'
});

// Upload document
const document = await client.documents.upload(file, 'national_id');

// Verify KYC
const kyc = await client.kyc.verify({
  firstName: 'John',
  lastName: 'Doe'
});
```

### Python
```bash
pip install zlocker
```

```python
from zlocker import ZLocker

client = ZLocker(api_key='your_api_key')

# Upload document
document = client.documents.upload(file, 'national_id')

# Verify face
result = client.face.verify(video_file)
```

## Support

For API questions and support:
- Email: api-support@zlocker.com
- Documentation: https://docs.zlocker.com
- GitHub Issues: https://github.com/hexims/kyaAutomation/issues
