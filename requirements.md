# Backend Features Technical Requirements

## 1. User Authentication System

### 1.1 Functional Requirements

#### User Registration
- Support registration for guests and hosts
- Collect essential user information:
  - Email (required)
  - Password (required)
  - Full name (required)
  - Phone number (optional)
  - Profile photo (optional)
- Implement email verification workflow
- Support OAuth integration (Google, Facebook)

#### Authentication
- JWT-based authentication system
- Role-based access control (RBAC) for:
  - Guests
  - Hosts
  - Admins
- Session management with token refresh mechanism

### 1.2 API Endpoints

```plaintext
Authentication Endpoints:
POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/logout
POST /api/v1/auth/verify-email
POST /api/v1/auth/forgot-password
POST /api/v1/auth/reset-password
POST /api/v1/auth/refresh-token
GET  /api/v1/auth/me

OAuth Endpoints:
GET  /api/v1/auth/google
GET  /api/v1/auth/facebook
POST /api/v1/auth/google/callback
POST /api/v1/auth/facebook/callback
```

### 1.3 Request/Response Specifications

```json
// Registration Request
{
  "email": "string",
  "password": "string",
  "fullName": "string",
  "phoneNumber": "string",
  "userType": "enum(GUEST, HOST)",
  "profilePhoto": "file"
}

// Registration Response
{
  "status": "success",
  "message": "Registration successful",
  "data": {
    "userId": "string",
    "email": "string",
    "fullName": "string",
    "userType": "string",
    "verificationToken": "string"
  }
}

// Login Response
{
  "status": "success",
  "data": {
    "accessToken": "string",
    "refreshToken": "string",
    "expiresIn": "number",
    "user": {
      "id": "string",
      "email": "string",
      "fullName": "string",
      "userType": "string",
      "isVerified": "boolean"
    }
  }
}
```

### 1.4 Validation Rules
- Email: Valid format, unique in system
- Password: 
  - Minimum 8 characters
  - Include uppercase, lowercase, number, special character
- Phone: Valid international format
- Profile Photo: Max 5MB, formats: JPG, PNG

### 1.5 Performance Requirements
- Registration response time: < 2s
- Login response time: < 1s
- Token refresh: < 500ms
- OAuth integration response: < 3s

## 2. Property Management

### 2.1 Functional Requirements

#### Property Creation and Management
- Create new property listings
- Update existing properties
- Delete properties
- Manage property availability
- Handle property media (photos)
- Set pricing and rules

### 2.2 API Endpoints

```plaintext
Property Management:
POST   /api/v1/properties
GET    /api/v1/properties
GET    /api/v1/properties/{id}
PUT    /api/v1/properties/{id}
DELETE /api/v1/properties/{id}

Media Management:
POST   /api/v1/properties/{id}/photos
DELETE /api/v1/properties/{id}/photos/{photoId}

Availability Management:
PUT    /api/v1/properties/{id}/availability
GET    /api/v1/properties/{id}/availability
```

### 2.3 Request/Response Specifications

```json
// Property Creation Request
{
  "title": "string",
  "description": "string",
  "propertyType": "enum(APARTMENT, HOUSE, etc)",
  "location": {
    "address": "string",
    "city": "string",
    "state": "string",
    "country": "string",
    "postalCode": "string",
    "coordinates": {
      "latitude": "number",
      "longitude": "number"
    }
  },
  "amenities": ["string"],
  "basePrice": "number",
  "maxGuests": "number",
  "bedrooms": "number",
  "bathrooms": "number"
}
```

### 2.4 Validation Rules
- Title: 5-100 characters
- Description: 20-2000 characters
- Price: Positive number, max 2 decimal places
- Photos: 
  - Max 15 per property
  - Max size: 5MB each
  - Required formats: JPG, PNG
- Location: Valid coordinates and address

### 2.5 Performance Requirements
- Property creation: < 3s
- Photo upload: < 5s per photo
- Property search: < 1s
- Property update: < 2s

## 3. Booking System

### 3.1 Functional Requirements

#### Booking Management
- Create new bookings
- Update booking status
- Cancel bookings
- Handle payment processing
- Manage booking communications
- Prevent double bookings

### 3.2 API Endpoints

```plaintext
Booking Management:
POST   /api/v1/bookings
GET    /api/v1/bookings
GET    /api/v1/bookings/{id}
PUT    /api/v1/bookings/{id}
DELETE /api/v1/bookings/{id}

Booking Operations:
POST   /api/v1/bookings/{id}/cancel
POST   /api/v1/bookings/{id}/confirm
GET    /api/v1/bookings/{id}/receipt

Payment Integration:
POST   /api/v1/bookings/{id}/pay
POST   /api/v1/bookings/{id}/refund
```

### 3.3 Request/Response Specifications

```json
// Booking Creation Request
{
  "propertyId": "string",
  "checkIn": "date",
  "checkOut": "date",
  "guests": {
    "adults": "number",
    "children": "number"
  },
  "totalPrice": "number",
  "paymentMethod": "enum(CREDIT_CARD, PAYPAL)",
  "specialRequests": "string"
}

// Booking Response
{
  "bookingId": "string",
  "status": "enum(PENDING, CONFIRMED, CANCELLED)",
  "propertyDetails": {
    "id": "string",
    "title": "string"
  },
  "bookingDetails": {
    "checkIn": "date",
    "checkOut": "date",
    "guests": {
      "adults": "number",
      "children": "number"
    }
  },
  "paymentStatus": "enum(PENDING, PAID, REFUNDED)",
  "totalPrice": "number"
}
```

### 3.4 Validation Rules
- Check-in date: Future date only
- Check-out date: After check-in date
- Maximum stay: 30 days
- Guest count: Within property limits
- Payment: Valid payment method
- Cancellation: According to property policy

### 3.5 Performance Requirements
- Booking creation: < 3s
- Payment processing: < 5s
- Availability check: < 500ms
- Booking modification: < 2s
- Concurrent booking handling: 50/second
