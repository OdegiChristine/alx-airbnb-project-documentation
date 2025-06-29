# Technical and Functional Requirements

# 1. User Authentication

## Functional Requirements

- Users must be able to register as a guest or host.
- Users can log in using email/password or via OAuth (Google/Facebook).
- Tokens should be used for authenticated access (JWT).
- Role-based access control (Guest, Host, Admin) must be enforced.

## 📡 API Endpoints

**➤ POST /api/auth/register**
**Description**: Register a new user
**Input**:

```
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "securePass123",
  "role": "guest"  // or "host"
}
```

**Validation**:

- Email must be unique and valid.
- Password must be at least 8 characters.
- Role must be either "guest" or "host".

**Output**:

```
{
  "message": "User registered successfully",
  "user": { "id": "uuid", "role": "guest", "token": "jwt-token" }
}
```

**➤ POST /api/auth/login**

- Input:

```
{
  "email": "john@example.com",
  "password": "securePass123"
}
```

- Output:

```
{
  "token": "jwt-token",
  "user": { "id": "uuid", "role": "guest" }
}
```

## Performance Criteria

- Response time: ≤ 300ms
- Token expiration: 1 hour with refresh option
- Brute force protection: 5 failed attempts → lockout for 15 minutes

# 2. Property Management

## Functional Requirements

- Hosts can create, update, or delete property listings.
- Listings include title, description, location, price, availability, and amenities.
- Guests can view properties.

## API Endpoints

**➤ POST /api/properties/**

- Input:

```
{
  "title": "Modern Studio in Nairobi",
  "description": "Comfortable space in CBD",
  "location": "Nairobi",
  "price_per_night": 50,
  "availability": ["2025-07-01", "2025-07-02"],
  "amenities": ["Wi-Fi", "Parking"],
  "images": ["url1", "url2"]
}
```

-**Validation**:

- - price_per_night must be numeric and > 0
- - Title, description, and location are required

- **Output**:

```
{
  "message": "Listing created successfully",
  "property_id": "uuid"
}
```

**➤ GET /api/properties/?location=Nairobi&guests=2**

- **Output**: Array of matching properties

**➤ PUT /api/properties/:id**

- **Role Restriction**: Only the listing owner (host) can update

- **Validation**: Same as create

## ⚙️ Performance Criteria

- Pagination for results (default 10 per page)
- Full-text search on title/description for efficiency
- Indexed on location and price for fast filtering

# 3. Booking System

## Functional Requirements

- Guests can book available properties for specific dates.
- Prevent double bookings.
- Booking status: pending, confirmed, cancelled, completed.

## API Endpoints

**➤ POST /api/bookings/**

- Input:

```
{
  "property_id": "uuid",
  "guest_id": "uuid",
  "check_in": "2025-07-05",
  "check_out": "2025-07-08"
}
```

- Validation:

- - Dates must not overlap with existing bookings

- - Check-out must be after check-in

- - Property must be available

- Output:

```
{
  "message": "Booking created successfully",
  "booking_id": "uuid",
  "status": "pending"
}
```

**➤ GET /api/bookings/:id**

- Fetch details of a specific booking

**➤ PATCH /api/bookings/:id/cancel**

- Only the guest or host can cancel, based on cancellation rules

## Performance Criteria

- Overlap check must use efficient date range filtering
- Booking creation latency: ≤ 500ms
- Ensure concurrency safety to prevent race conditions
