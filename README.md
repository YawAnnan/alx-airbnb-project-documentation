# Backend Requirement Specifications
---
## 1. User Authentication
Objective: To provide a secure and efficient way for users to register, log in, and manage their identities on the platform.

Endpoint: POST /api/auth/register
Description: Creates a new user account.

Input:

email: string (required, unique, valid email format)

password: string (required, min. 8 characters)

role: enum ("guest" or "host", required)

Output (Success 201 Created):

message: string ("User registered successfully")

token: string (JWT for authenticated session)

Output (Error 409 Conflict):

error: string ("Email already registered")

Output (Error 400 Bad Request):

error: string ("Invalid input data")

Validation Rules: Password must be hashed before storage. Email must be unique and validated for format.

## 2. Property Management
Objective: To allow hosts to create, read, update, and delete property listings and to enable guests to view them.

Endpoint: POST /api/listings
Description: Creates a new property listing.

Authentication: Requires a valid JWT with a "host" role.

Input:

title: string (required, min. 5 chars)

description: string (required, min. 20 chars)

location: string (required)

pricePerNight: number (required, must be greater than 0)

amenities: array of strings

imageUrls: array of strings (required)

Output (Success 201 Created):

propertyId: UUID

message: string ("Property created successfully")

Output (Error 401 Unauthorized):

error: string ("Invalid or missing token")

Output (Error 403 Forbidden):

error: string ("User does not have host permissions")

Validation Rules: All required fields must be present. pricePerNight must be a positive number.

Endpoint: GET /api/listings
Description: Retrieves a list of all properties with optional filtering and pagination.

Input (Query Parameters):

location: string

minPrice: number

maxPrice: number

amenities: array of strings

page: number (default 1)

limit: number (default 10)

Output (Success 200 OK):

properties: array of property objects

currentPage: number

totalPages: number

Performance Criteria: The API should respond within 500ms for a search query.

## 3. Booking System
Objective: To allow guests to reserve properties for specific dates and to manage the booking lifecycle.

Endpoint: POST /api/bookings
Description: Creates a new booking for a property.

Authentication: Requires a valid JWT with a "guest" role.

Input:

propertyId: UUID (required)

startDate: date (required, format YYYY-MM-DD)

endDate: date (required, format YYYY-MM-DD)

Output (Success 201 Created):

bookingId: UUID

message: string ("Booking initiated successfully")

Output (Error 400 Bad Request):

error: string ("Start date must be before end date")

Output (Error 409 Conflict):

error: string ("Property is not available for the selected dates")

Output (Error 401 Unauthorized):

error: string ("Invalid or missing token")

Validation Rules: startDate must be before endDate. The backend must perform a real-time check to ensure the property is not already booked for the specified dates.
