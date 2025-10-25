# Airbnb Clone Backend — Requirements & API Reference

This document defines the functional and non-functional requirements for the Airbnb Clone Backend, as well as a complete reference for the primary API endpoints.

---

## Table of Contents
1. [Feature Requirements](#feature-requirements)
   - [1. User Authentication & Profile Management](#1-user-authentication--profile-management)
   - [2. Property Listing Management](#2-property-listing-management)
   - [3. Booking System](#3-booking-system)
2. [API Reference](#api-reference)
   - [User Authentication & Profile](#1️⃣-user-authentication--profile)
   - [Property Listing Management](#2️⃣-property-listing-management)
   - [Booking System](#3️⃣-booking-system)

---

# Feature Requirements

## 1. User Authentication & Profile Management

This feature handles user registration, secure login, session management, and role-based profile interactions.

###  1.1 Functional Requirements

| ID | Description | Role(s) |
|----|-------------|---------|
| AUTH-01 | System must allow user registration via email/password. | Guest, Host |
| AUTH-02 | System must issue a JSON Web Token (JWT) upon successful login. | All |
| AUTH-03 | System must validate the role of the authenticated user (RBAC). | All |
| AUTH-04 | Users must be able to update name, email, and profile picture URL. | All |

###  1.2 Validation Rules & Constraints
- **Email:** Must be unique and valid format
- **Password Policy:** Min 8 chars, includes uppercase, number, special char
- **JWT:** Required in `Authorization: Bearer <token>` for protected endpoints

###  1.3 Performance & Security
- Password hashing using **bcrypt**
- Auth endpoints must respond in **<150 ms** (95% of the time)

---

## 2. Property Listing Management

Allows Hosts to create, update, and manage rental properties.

###  2.1 Functional Requirements

| ID | Description | Role |
|----|-------------|------|
| LIST-01 | Hosts can create a new listing (required fields: title, desc, location, price, capacity). | Host |
| LIST-02 | Max **10 images** per listing. | Host |
| LIST-03 | Hosts can update their own listings. | Host |
| LIST-04 | Hosts can soft-delete (deactivate) listings. | Host |
| LIST-05 | Stores image URLs from external storage (AWS S3, Cloudinary). | Host |

### 2.2 Input/Output Example (POST /listings)

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| title | String | Yes | ≤ 100 chars |
| location | Object | Yes | Must include valid geo-coordinates |
| base_price | Integer | Yes | > 0, stored in cents |
| amenities | Array | No | Must match predefined list |
| image_urls | Array<String> | Yes | Max 10 |

### 2.3 Performance & Data Rules
- Indexed search by **location** and **price**
- Strong validation before committing updates

---

## 3. Booking System

Handles property reservation, pricing, and secure payment flow.

### 3.1 Functional Requirements

| ID | Description | Role |
|----|-------------|------|
| BOOK-01 | Check availability against existing bookings. | Guest |
| BOOK-02 | System calculates total price + fees. | Guest |
| BOOK-03 | Creates a Pending booking after availability check. | Backend |
| BOOK-04 | Integrates with Payment Gateway for processing. | Backend |
| BOOK-05 | On success → update booking to Confirmed + log payment record. | Backend |
| BOOK-06 | On failure → delete/mark Pending record as Failed. | Backend |

### 3.2 Validation for Booking Creation

| Field | Type | Required | Rules |
|------|------|----------|------|
| listing_id | UUID | ✅ | Must reference active listing |
| check_in_date | Date | ✅ | Future date only |
| check_out_date | Date | ✅ | Must be after check-in |
| payment_token | String | ✅ | From frontend/Stripe |

### 3.3 Process Performance
- Uses **optimistic locking / transaction isolation** to avoid double-booking
- Entire POST `/bookings` must complete in **<500 ms** including payment

---

# API Reference

Documented based on the above functional requirements.

---

## 1️⃣ User Authentication & Profile
Base: `/api/v1/auth` & `/api/v1/users`

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | /api/v1/auth/register | Register new user. | No |
| POST | /api/v1/auth/login | Authenticate and return JWT. | No |
| GET | /api/v1/users/me | Get current user profile. | Yes |
| PATCH | /api/v1/users/me | Update current user profile. | Yes |

---

## 2️⃣ Property Listing Management
Base: `/api/v1/listings`  
Auth: Host-only for modifications

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | /api/v1/listings | Create new listing | Host Role |
| GET | /api/v1/listings/{id} | Get listing details | No |
| PUT | /api/v1/listings/{id} | Update owned listing | Host Role |
| DELETE | /api/v1/listings/{id} | Soft delete listing | Host Role |

---

## 3️⃣ Booking System
Base: `/api/v1/bookings`  
Auth: Guest-only for creation

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | /api/v1/bookings | Create booking + payment process | Guest Role |
| GET | /api/v1/bookings/me | Get booking history | Yes |
| POST | /api/v1/bookings/{id}/cancel | Cancel an existing booking | Yes |

---

## RBAC Notes
- JWT must be sent using: Authorization: Bearer <token>