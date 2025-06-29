Backend Requirement Specifications
This document outlines the technical and functional requirements for the core backend features of the Airbnb Clone project.

1. User Authentication & Management
This feature covers user registration, login, and profile management. It is the foundation for identifying and authorizing users within the system.

Functional Requirements
Users must be able to register for a new account with an email and password.

Users must be able to log in to receive a secure authentication token (JWT).

Authenticated users must be able to view and update their own profile information.

The system must support role differentiation between "Guest" and "Host".

API Endpoints
Method

Endpoint

Description

POST

/api/v1/auth/register

Creates a new user account.

POST

/api/v1/auth/login

Authenticates a user and returns a JWT.

GET

/api/v1/users/profile

Retrieves the profile of the logged-in user.

PUT

/api/v1/users/profile

Updates the profile of the logged-in user.

Endpoint Specifications
A. Register User
Endpoint: POST /api/v1/auth/register

Description: Registers a new user.

Input (Request Body):

{
  "username": "john_doe",
  "email": "john.doe@example.com",
  "password": "strongpassword123",
  "role": "Guest"
}

Validation Rules:

username: Required, string, unique, 3-20 characters.

email: Required, valid email format, unique.

password: Required, string, minimum 8 characters, must contain at least one number and one letter.

role: Required, must be either "Guest" or "Host".

Output (Success 201 Created):

{
  "message": "User registered successfully",
  "userId": "user_id_12345"
}

Output (Error 400 Bad Request):

{
  "error": "Validation failed",
  "details": {
    "email": "Email already in use"
  }
}

B. Login User
Endpoint: POST /api/v1/auth/login

Description: Authenticates a user.

Input (Request Body):

{
  "email": "john.doe@example.com",
  "password": "strongpassword123"
}

Validation Rules:

email: Required, must be a valid email.

password: Required, string.

Output (Success 200 OK):

{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}

Output (Error 401 Unauthorized):

{
  "error": "Invalid credentials"
}

Performance & Security
Security: Passwords must be hashed using a strong algorithm (e.g., bcrypt). All endpoints except /register and /login must be protected and require a valid JWT.

Performance: Login and registration responses should be under 200ms.

2. Property Listings Management
This feature allows hosts to create, manage, and display their property listings.

Functional Requirements
Authenticated hosts must be able to create new property listings.

Hosts must be able to update or delete their own listings.

Any user (authenticated or not) must be able to view a list of properties and search for them.

Any user must be able to view the detailed information for a single property.

API Endpoints
Method

Endpoint

Description

POST

/api/v1/properties

Creates a new property listing (Host only).

GET

/api/v1/properties

Retrieves a list of all properties.

GET

/api/v1/properties/{id}

Retrieves details for a single property.

PUT

/api/v1/properties/{id}

Updates a property listing (Owner only).

DELETE

/api/v1/properties/{id}

Deletes a property listing (Owner only).

Endpoint Specifications
A. Create Property Listing
Endpoint: POST /api/v1/properties

Authentication: Required (Host role).

Input (Request Body):

{
  "title": "Cozy Downtown Apartment",
  "description": "A beautiful apartment in the heart of the city.",
  "location": "City Center",
  "price_per_night": 150.00,
  "guest_capacity": 2,
  "amenities": ["Wi-Fi", "Kitchen", "Air Conditioning"]
}

Validation Rules:

title: Required, string, 5-100 characters.

price_per_night: Required, number, greater than 0.

guest_capacity: Required, integer, greater than 0.

Output (Success 201 Created):

{
  "message": "Property created successfully",
  "propertyId": "prop_id_67890"
}

B. Get All Properties
Endpoint: GET /api/v1/properties

Description: Supports query parameters for filtering (e.g., ?location=City%20Center&max_price=200).

Output (Success 200 OK):

{
  "data": [
    {
      "propertyId": "prop_id_67890",
      "title": "Cozy Downtown Apartment",
      "location": "City Center",
      "price_per_night": 150.00
    }
  ]
}

Performance & Security
Security: A user must be the owner of a property to update or delete it.

Performance: The endpoint to get all properties should support pagination and respond in under 300ms, even with thousands of listings.

3. Booking System
This feature enables guests to book properties for specific dates.

Functional Requirements
An authenticated guest must be able to create a booking for a property.

The system must prevent double-bookings for the same dates.

A guest must be able to view their own booking history.

A host must be able to view bookings for their properties.

API Endpoints
Method

Endpoint

Description

POST

/api/v1/bookings

Creates a new booking (Guest only).

GET

/api/v1/bookings/my-bookings

Retrieves bookings for the logged-in user.

GET

/api/v1/properties/{id}/bookings

Retrieves bookings for a specific property (Owner only).

Endpoint Specifications
A. Create Booking
Endpoint: POST /api/v1/bookings

Authentication: Required (Guest role).

Input (Request Body):

{
  "propertyId": "prop_id_67890",
  "start_date": "2025-08-10",
  "end_date": "2025-08-15"
}

Validation Rules:

propertyId: Required, must correspond to an existing property.

start_date: Required, valid date format, must be in the future.

end_date: Required, valid date format, must be after start_date.

Logic: The system must check for booking conflicts before creating the new booking.

Output (Success 201 Created):

{
  "message": "Booking created successfully",
  "bookingId": "booking_id_abcde",
  "total_price": 750.00
}

Output (Error 409 Conflict):

{
  "error": "Booking conflict",
  "message": "The property is unavailable for the selected dates."
}

Performance & Security
Security: A user can only view their own bookings. A host can only view bookings for properties they own.

Performance: The conflict-checking logic for bookings must be highly optimized to respond in under 500ms, even for properties with many existing bookings.
