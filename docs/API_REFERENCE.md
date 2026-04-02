# API Reference — Stella E-Commerce Backend

> Generated: April 2, 2026 | Derived entirely from static codebase analysis.

---

## Authentication

Stella uses **stateless JWT Bearer token authentication**.

### How it works

1. Call `POST /api/auth/login` with your credentials (email or phone number + password).
2. The response contains a `jwtToken` (valid for **24 hours**).
3. Include the token in the `Authorization` header of every subsequent request:
   ```
   Authorization: Bearer <jwtToken>
   ```

### Roles

| Role          | Assigned to                                              |
| ------------- | -------------------------------------------------------- |
| `ROLE_BUYER`  | All registered users (default)                           |
| `ROLE_SELLER` | Users who have completed seller registration or upgraded |

### Public Endpoints (no token required)

- `POST /api/auth/login`
- `POST /api/users/register`
- `POST /api/users/verify-user`
- `POST /api/users/verify-update`
- `POST /api/users/register/resend-token`
- `POST /api/sellers/register`
- `POST /api/sellers/verify-update`
- `GET /api/orders/track`

---

## Response Format

Successful responses return the primary payload directly (object or array). Error responses from the global exception handler follow one of two shapes:

**String error (400 Bad Request):**

```json
"Error: message describing the problem"
```

**Structured 404 Not Found:**

```json
{
  "status": 404,
  "message": "Error: User not found",
  "timestamp": 1712000000000
}
```

**Structured 400 Bad Request (IllegalArgumentException):**

```json
{
  "status": 400,
  "message": "Error: message",
  "timestamp": 1712000000000
}
```

---

## Auth Endpoints

### `POST /api/auth/login`

Authenticate and receive a JWT token.

**Request Body:**

```json
{
  "reference": "user@example.com",
  "password": "MyP@ssw0rd"
}
```

> `reference` accepts either the user's **email** or **phone number**.

**Success Response — `200 OK`:**

```json
{
  "jwtToken": "eyJhbGciOiJIUzI1NiJ9...",
  "reference": "user@example.com"
}
```

**Error Responses:**

- `400 Bad Request` — Invalid username or password.
- `401 Unauthorized` — Authentication failed.

---

## User Endpoints

Base path: `/api/users`

---

### `POST /api/users/register`

Register a new user account. Account is created inactive; a verification email is sent.

**Auth:** None

**Request Body:**

```json
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "number": "9876543210",
  "password": "MyP@ssw0rd1"
}
```

> Password rules: minimum 8 characters, at least one uppercase, one lowercase, one digit, one special character.

**Success Response — `200 OK`:** `"Success"`

**Error Responses:**

- `400 Bad Request` — Email or phone already registered; weak password; invalid email/phone format.

---

### `POST /api/users/verify-user?token={token}`

Verify email address using the token sent during registration.

**Auth:** None | **Query Param:** `token` (string)

**Success Response — `200 OK`:** `"Email verified successfully!"`
**Error Response — `400 Bad Request`:** `"Invalid or expired token"`

---

### `POST /api/users/register/resend-token`

Resend the registration verification email.

**Auth:** None

**Request Body:** `"user@example.com"` _(plain string)_

**Success Response — `200 OK`:** `"Success"`

---

### `POST /api/users/verify-update?token={token}`

Confirm a pending email or phone number update via the verification token sent by email.

**Auth:** None | **Query Param:** `token` (string)

**Success Response — `200 OK`:** `"update verified successfully!"`

---

### `GET /api/users`

Fetch the authenticated user's profile.

**Auth:** Bearer token (any role)

**Success Response — `200 OK`:**

```json
{
  "userId": 1,
  "name": "Jane Doe",
  "email": "jane@example.com",
  "number": "9876543210"
}
```

---

### `PUT /api/users/name`

Update the authenticated user's display name. Change is applied immediately.

**Auth:** Bearer token (any role)

**Request Body:** `"New Name"` _(plain string)_

**Success Response — `200 OK`:** Updated `UserViewModel` object (same shape as GET /api/users).

---

### `PUT /api/users/email`

Initiate an email address change. A verification link is sent to the **new** email; the change is only applied after clicking it.

**Auth:** Bearer token (any role)

**Request Body:** `"newemail@example.com"` _(plain string)_

**Success Response — `200 OK`:** `"Success"`

**Error Responses:** `400` — Invalid email format; email already in use.

---

### `PUT /api/users/number`

Initiate a phone number change. A verification link is sent; change applied after confirmation.

**Auth:** Bearer token (any role)

**Request Body:** `"9876543211"` _(plain string, 10 digits)_

**Success Response — `200 OK`:** `"Success"`

---

### `PUT /api/users/password`

Update the authenticated user's password.

**Auth:** Bearer token (any role)

**Request Body:**

```json
{
  "currentPassword": "OldP@ssw0rd",
  "newPassword": "NewP@ssw0rd1"
}
```

**Success Response — `200 OK`:** `"Success"`

**Error Responses:** `400` — Wrong current password; weak new password.

---

## Address Endpoints

Base path: `/api/users/address` | **Auth:** Bearer token (any role)

---

### `POST /api/users/address`

Save a new address for the authenticated user.

**Request Body:**

```json
{
  "streetAddress": "123 Main St",
  "city": "Mumbai",
  "state": "Maharashtra",
  "postalCode": "400001",
  "country": "India",
  "main": 1
}
```

> `main: 1` marks as default/primary address; `main: 0` for secondary.

**Success Response — `200 OK`:** Array of `AddressViewModel` (all user addresses).

---

### `GET /api/users/address`

Fetch all addresses for the authenticated user.

**Success Response — `200 OK`:**

```json
[
  {
    "id": 1,
    "streetAddress": "123 Main St",
    "city": "Mumbai",
    "state": "Maharashtra",
    "postalCode": "400001",
    "country": "India",
    "main": 1
  }
]
```

---

### `PUT /api/users/address`

Update an existing address.

**Request Body:** `AddressViewModel` (same shape as GET response, with `id` populated).

**Success Response — `200 OK`:** Updated array of all user addresses.

---

### `DELETE /api/users/address?id={id}`

Delete an address by ID.

**Query Param:** `id` (int)

**Success Response — `200 OK`:** Remaining array of user addresses.

---

## Seller Endpoints

Base path: `/api/sellers`

---

### `POST /api/sellers/register`

Register a brand-new seller account (creates a user + seller in one step).

**Auth:** None

**Request Body:**

```json
{
  "name": "John Store",
  "email": "john@store.com",
  "number": "9123456780",
  "password": "St0reP@ss",
  "storeName": "John's Electronics",
  "address": "456 Market Rd, Delhi"
}
```

**Success Response — `200 OK`:** `"Success"`

---

### `POST /api/sellers/upgrade`

Upgrade an existing `ROLE_BUYER` account to `ROLE_SELLER`.

**Auth:** Bearer token (`ROLE_BUYER`)

**Request Body:**

```json
{
  "storeName": "My New Store",
  "address": "789 Commerce Ave, Bangalore"
}
```

**Success Response — `200 OK`:** `"Success"`

---

### `POST /api/sellers/verify-update?token={token}`

Confirm a pending seller profile update via email token.

**Auth:** None | **Query Param:** `token` (string)

**Success Response — `200 OK`:** `"update verified successfully!"`

---

### `PUT /api/sellers/store-name`

Update the seller's store name.

**Auth:** Bearer token (`ROLE_SELLER`)

**Request Body:** `"New Store Name"` _(plain string)_

**Success Response — `200 OK`:** `"Success"`

---

### `PUT /api/sellers/address`

Update the seller's store address.

**Auth:** Bearer token (`ROLE_SELLER`)

**Request Body:** `"456 New Street, City"` _(plain string)_

**Success Response — `200 OK`:** `SellerViewModel` object:

```json
{
  "userId": 5,
  "storeName": "John's Electronics",
  "address": "456 New Street, City",
  "logo": "/path/to/logo.png",
  "createdAt": "2025-01-01T00:00:00.000+00:00"
}
```

---

### `POST /api/sellers/logo`

Upload an initial store logo image.

**Auth:** Bearer token (`ROLE_SELLER`) | **Content-Type:** `multipart/form-data`

**Form Fields:**
| Field | Type | Description |
|---|---|---|
| `logo` | `MultipartFile` | Image file (jpg, jpeg, png, gif, bmp) |

**Success Response — `200 OK`:** `SellerViewModel`

---

### `PUT /api/sellers/logo`

Replace the existing store logo.

**Auth:** Bearer token (`ROLE_SELLER`) | **Content-Type:** `multipart/form-data`

Same request/response as `POST /api/sellers/logo`.

---

## Seller Dashboard Endpoints

Base path: `/api/sellers/dashboard` | **Auth:** Bearer token (`ROLE_SELLER`)

---

### `GET /api/sellers/dashboard/products`

Fetch all products listed by the authenticated seller.

**Success Response — `200 OK`:** Array of `ProductViewModel` (see Product section).

---

### `GET /api/sellers/dashboard/orders`

Fetch all orders that contain the seller's products.

**Success Response — `200 OK`:**

```json
[
  {
    "orderId": 12,
    "orderDate": "...",
    "status": "paid",
    "shippingAddress": "123 Main St, Mumbai...",
    "items": [
      {
        "orderItemId": 5,
        "productId": 3,
        "productName": "Laptop X",
        "status": "waiting",
        "quantity": 1,
        "price": 75000.0,
        "totalPrice": 75000.0
      }
    ]
  }
]
```

---

### `POST /api/sellers/dashboard/update-order-status`

Update the fulfillment status of all order items in a given order.

**Request Body:**

```json
{
  "order_id": 12,
  "status": "accepted"
}
```

> Allowed `status` values: `waiting`, `accepted`, `canceled`, `shipped`

**Success Response — `200 OK`:** Updated array of `SellerOrderViewModel`.

---

## Product Endpoints

Base path: `/api/products`

---

### `POST /api/products`

Create a new product listing with images.

**Auth:** Bearer token (`ROLE_SELLER`) | **Content-Type:** `multipart/form-data`

**Form Fields:**
| Field | Type | Required | Description |
|---|---|---|---|
| `images` | `List<MultipartFile>` | Yes | Up to 9 product images |
| `name` | `String` | Yes | Product name (must be unique per seller) |
| `description` | `String` | Yes | Product description |
| `price` | `double` | Yes | Price in INR |
| `stock` | `int` | Yes | Available quantity |
| `category` | `String` | Yes | Product category name |
| `active` | `Boolean` | Yes | Whether the listing is live |

**Success Response — `200 OK`:** `ProductViewModel`:

```json
{
  "id": 10,
  "userId": 5,
  "name": "Asus ROG 5",
  "description": "Gaming laptop",
  "price": 85000.0,
  "stock": 20,
  "category": "Laptops",
  "active": true,
  "images": ["path/to/0.jpg", "path/to/1.jpg"]
}
```

---

### `PUT /api/products`

Update an existing product. Only fields with non-empty/non-zero values are updated.

**Auth:** Bearer token (`ROLE_SELLER`) | **Content-Type:** `multipart/form-data`

**Form Fields:** Same as `POST /api/products` plus `id` (int, the product ID).
Send an empty file for `images` to skip image update.

**Success Response — `200 OK`:** Updated `ProductViewModel`.

---

### `DELETE /api/products/de-activate?id={id}`

Deactivate (soft-delete) a product. The product is hidden from searches but not removed.

**Auth:** Bearer token (`ROLE_SELLER`) | **Query Param:** `id` (int)

**Success Response — `200 OK`:** `ProductViewModel` with `active: false`.

---

### `POST /api/products/activate?id={id}`

Re-activate a previously deactivated product.

**Auth:** Bearer token (`ROLE_SELLER`) | **Query Param:** `id` (int)

**Success Response — `200 OK`:** `ProductViewModel` with `active: true`.

---

### `GET /api/products/store?store={storeName}`

Fetch all products from a specific seller store.

**Auth:** None required | **Query Param:** `store` (string)

**Success Response — `200 OK`:** Array of `ProductViewModel`.

**Error Response — `400`:** Store name not found.

---

### `GET /api/products/search?search={query}`

Search products by name (partial match).

**Auth:** None required | **Query Param:** `search` (string)

**Success Response — `200 OK`:** Array of `ProductViewModel`.

---

## Cart Endpoints

Base path: `/api/users/cart` | **Auth:** Bearer token (any role)

---

### `POST /api/users/cart`

Add an item to an existing cart, or create a new cart if `cartId` is `0`.

**Request Body:**

```json
{
  "cartId": 0,
  "request": {
    "productId": 3,
    "quantity": 2
  }
}
```

> Set `cartId: 0` to create a new cart. Set `cartId` to an existing cart ID to add to it.

**Success Response — `200 OK`:** Array of all `CartViewModel` for the user.

---

### `GET /api/users/cart`

Fetch all carts belonging to the authenticated user.

**Success Response — `200 OK`:**

```json
[
  {
    "cartId": 1,
    "totalAmount": 170000.0,
    "items": [
      {
        "id": 1,
        "productId": 3,
        "productName": "Asus ROG 5",
        "quantity": 2,
        "price": 85000.0,
        "totalPrice": 170000.0
      }
    ]
  }
]
```

---

### `PUT /api/users/cart`

Update item quantities in a cart.

**Request Body:**

```json
{
  "cartId": 1,
  "updates": [{ "productId": 3, "quantity": 1 }]
}
```

**Success Response — `200 OK`:** Updated array of all user `CartViewModel` objects.

---

### `DELETE /api/users/cart`

Delete an entire cart and all its items.

**Request Body:** `1` _(plain integer — the cartId)_

**Success Response — `200 OK`:** Remaining carts for the user.

---

## Order Endpoints

Base path: `/api/orders`

---

### `POST /api/orders`

Create a new order from an explicit product list. Returns Razorpay payment info to initiate checkout on the client.

**Auth:** Bearer token (any role)

**Request Body:**

```json
{
  "addressId": 1,
  "orders": [
    { "productId": 3, "quantity": 1 },
    { "productId": 7, "quantity": 2 }
  ]
}
```

**Success Response — `200 OK`:**

```json
{
  "orderId": 42,
  "orderDate": "Wed Apr 02 10:00:00 IST 2026",
  "totalAmount": 95000.0,
  "status": "unpaid",
  "shippingAddress": "123 Main St, Mumbai, Maharashtra 400001, India",
  "razorpayId": "order_Abc123XYZ",
  "apiKey": "rzp_test_..."
}
```

---

### `POST /api/orders/order-by-cart`

Create an order from an existing cart (all items in the cart become order items).

**Auth:** Bearer token (any role)

**Request Body:**

```json
{
  "addressId": 1,
  "cartId": 1
}
```

**Success Response — `200 OK`:** Same `OrderPaymentRequest` shape as above.

---

### `POST /api/orders/payment-callback`

Called by the client after Razorpay payment is completed. Verifies the payment signature, marks the order as paid, and decrements product stock.

**Auth:** None (public endpoint — signature verification provides security)

**Request Body:**

```json
{
  "razorpay_payment_id": "pay_Abc123",
  "razorpay_order_id": "order_Abc123XYZ",
  "razorpay_signature": "computed_hmac_signature"
}
```

**Success Response — `200 OK`:** _(empty body)_

**Error Response — `400`:** Invalid or unauthorized payment callback (signature mismatch).

---

### `GET /api/orders`

Fetch all orders placed by the authenticated user.

**Auth:** Bearer token (any role)

**Success Response — `200 OK`:**

```json
[
  {
    "orderId": 42,
    "orderDate": "...",
    "totalAmount": 95000.0,
    "status": "paid",
    "shippingAddress": "...",
    "items": [
      {
        "orderItemId": 5,
        "productId": 3,
        "quantity": 1,
        "price": 85000.0,
        "totalPrice": 85000.0,
        "status": "accepted"
      }
    ]
  }
]
```

---

### `GET /api/orders/track?order_id={id}`

Public order status lookup by order ID.

**Auth:** None | **Query Param:** `order_id` (int)

**Success Response — `200 OK`:** `"paid"` _(plain string — order status)_

---

## Review & Rating Endpoints

---

### `POST /api/review-rating`

Submit a review and rating for a product.

**Auth:** Bearer token (any role)

**Request Body:**

```json
{
  "productId": 3,
  "rating": 4,
  "comment": "Great laptop, runs very smoothly."
}
```

> `rating` must be 1–5. `comment` has a character limit enforced by the service.

**Success Response — `200 OK`:** _(empty body)_

---

### `PUT /api/review-rating/{reviewAndRatingId}`

Update an existing review.

**Auth:** Bearer token (any role) | **Path Param:** `reviewAndRatingId` (int)

**Request Body:** Same as POST.

**Success Response — `200 OK`:** _(empty body)_

---

### `GET /api/products/review-rating?productId={id}`

Fetch all reviews for a product.

**Auth:** None | **Query Param:** `productId` (int)

**Success Response — `200 OK`:**

```json
[
  {
    "id": 1,
    "userId": 2,
    "productId": 3,
    "rating": 4,
    "comment": "Great laptop.",
    "date": "2026-03-01T10:00:00.000+00:00"
  }
]
```

---

### `GET /api/users/review-rating`

Fetch all reviews written by the authenticated user.

**Auth:** Bearer token (any role)

**Success Response — `200 OK`:** Array of `ReviewAndRatingViewModel` (same shape as above).

---

### `GET /api/products/average-rating?productId={id}`

Get average star rating for a product.

**Auth:** None | **Query Param:** `productId` (int)

**Success Response — `200 OK`:** `4.2` _(plain double)_
