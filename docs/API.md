# API Documentation - Fantasy App

## Base URL
```
Development: http://localhost:3000/api
Production: https://api.fantasy-app.com/api
```

## Authentication

All protected endpoints require a Bearer token in the Authorization header:

```
Authorization: Bearer {token}
```

---

## Authentication Endpoints

### Register User
Create a new user account.

```
POST /auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securePassword123",
  "firstName": "John",
  "lastName": "Doe"
}

Response 201:
{
  "id": "uuid",
  "email": "user@example.com",
  "token": "jwt_token",
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe"
  }
}
```

### Login
Authenticate user and receive JWT token.

```
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securePassword123"
}

Response 200:
{
  "token": "jwt_token",
  "refreshToken": "refresh_token",
  "user": {
    "id": "uuid",
    "email": "user@example.com"
  }
}
```

### Refresh Token
Get a new access token using refresh token.

```
POST /auth/refresh
Content-Type: application/json

{
  "refreshToken": "refresh_token"
}

Response 200:
{
  "token": "new_jwt_token"
}
```

### Logout
Invalidate current session.

```
POST /auth/logout
Authorization: Bearer {token}

Response 200:
{
  "message": "Logged out successfully"
}
```

---

## User Endpoints

### Get User Profile
```
GET /user/profile
Authorization: Bearer {token}

Response 200:
{
  "id": "uuid",
  "email": "user@example.com",
  "profile": {
    "firstName": "John",
    "lastName": "Doe",
    "profileImage": "url",
    "skinTone": "medium",
    "hairColor": "brown"
  },
  "preferences": {
    "favoriteStyles": ["casual", "minimalist"],
    "budgetRange": [50, 500],
    "shoppingPreferences": ["sustainable", "affordable"]
  }
}
```

### Update User Profile
```
PUT /user/profile
Authorization: Bearer {token}
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "skinTone": "medium",
  "hairColor": "brown",
  "preferences": {
    "favoriteStyles": ["casual"],
    "budgetRange": [50, 500]
  }
}

Response 200:
{
  "message": "Profile updated successfully",
  "user": { ... }
}
```

### Upload Profile Picture
```
POST /user/profile-picture
Authorization: Bearer {token}
Content-Type: multipart/form-data

Form Data:
- file: [image file]

Response 200:
{
  "url": "https://cdn.example.com/profile/uuid.jpg"
}
```

---

## Measurements Endpoints

### Create/Update Measurements
Record user's body measurements.

```
POST /measurements
Authorization: Bearer {token}
Content-Type: application/json

{
  "height": 175,
  "weight": 70,
  "shoulders": 45,
  "chest": 95,
  "waist": 80,
  "hips": 90,
  "thigh": 55,
  "inseam": 80,
  "armLength": 60,
  "neckSize": 38,
  "shoeSize": 42,
  "capSize": 57
}

Response 201:
{
  "id": "uuid",
  "userId": "uuid",
  "measurements": { ... },
  "recordedAt": "2026-06-12T10:00:00Z"
}
```

### Get Latest Measurements
```
GET /measurements/latest
Authorization: Bearer {token}

Response 200:
{
  "id": "uuid",
  "measurements": { ... },
  "recordedAt": "2026-06-12T10:00:00Z"
}
```

### Get Measurement History
```
GET /measurements/history?limit=10&offset=0
Authorization: Bearer {token}

Response 200:
{
  "total": 5,
  "measurements": [
    {
      "id": "uuid",
      "recordedAt": "2026-06-12T10:00:00Z",
      "measurements": { ... }
    }
  ]
}
```

### Delete Measurement Record
```
DELETE /measurements/:id
Authorization: Bearer {token}

Response 200:
{
  "message": "Measurement deleted successfully"
}
```

---

## Avatar Endpoints

### Create Avatar
Generate a 3D avatar from measurements.

```
POST /avatar/create
Authorization: Bearer {token}
Content-Type: application/json

{
  "measurementId": "uuid",
  "skinTone": "medium",
  "hairStyle": "long",
  "hairColor": "brown",
  "eyeColor": "blue"
}

Response 201:
{
  "id": "uuid",
  "userId": "uuid",
  "model3dUrl": "https://cdn.example.com/avatars/uuid.glb",
  "customizations": { ... },
  "createdAt": "2026-06-12T10:00:00Z"
}
```

### Get Avatar
```
GET /avatar/:id
Authorization: Bearer {token}

Response 200:
{
  "id": "uuid",
  "model3dUrl": "https://cdn.example.com/avatars/uuid.glb",
  "customizations": { ... },
  "createdAt": "2026-06-12T10:00:00Z"
}
```

### Update Avatar Customization
```
PUT /avatar/:id/customize
Authorization: Bearer {token}
Content-Type: application/json

{
  "skinTone": "light",
  "hairColor": "blonde",
  "facialFeatures": {
    "browShape": 0.5,
    "cheekbones": 0.7,
    "jawLine": 0.4
  }
}

Response 200:
{
  "id": "uuid",
  "customizations": { ... }
}
```

### Get Avatar Render
Render avatar in a specific pose.

```
GET /avatar/:id/render?pose=standing&quality=high
Authorization: Bearer {token}

Response 200:
{
  "imageUrl": "https://cdn.example.com/renders/uuid_standing.png",
  "pose": "standing"
}
```

### Set Avatar Pose
Change avatar pose (for try-on).

```
POST /avatar/:id/pose
Authorization: Bearer {token}
Content-Type: application/json

{
  "pose": "walking",
  "animation": true
}

Response 200:
{
  "pose": "walking",
  "animationUrl": "https://cdn.example.com/animations/uuid_walking.webm"
}
```

### Delete Avatar
```
DELETE /avatar/:id
Authorization: Bearer {token}

Response 200:
{
  "message": "Avatar deleted successfully"
}
```

---

## Recommendations Endpoints

### Generate Recommendations
Get AI-powered style recommendations.

```
POST /recommendations/generate
Authorization: Bearer {token}
Content-Type: application/json

{
  "avatarId": "uuid",
  "measurementId": "uuid",
  "includeNiche": true
}

Response 200:
{
  "id": "uuid",
  "userId": "uuid",
  "analysis": {
    "bodyShape": "hourglass",
    "faceShape": "oval",
    "skinTone": "medium"
  },
  "recommendations": {
    "styles": ["minimalist", "casual"],
    "colors": ["navy", "cream", "rust"],
    "patterns": ["solid", "striped"],
    "niche": "minimalist-chic",
    "brands": ["brand1", "brand2"]
  },
  "reasoning": "Based on your body shape and preferences...",
  "createdAt": "2026-06-12T10:00:00Z"
}
```

### Get Recommendation by ID
```
GET /recommendations/:id
Authorization: Bearer {token}

Response 200:
{
  "id": "uuid",
  "recommendations": { ... }
}
```

### Get User Recommendations
```
GET /recommendations?limit=10&offset=0
Authorization: Bearer {token}

Response 200:
{
  "total": 15,
  "recommendations": [ ... ]
}
```

### Save Recommendation
```
POST /recommendations/:id/save
Authorization: Bearer {token}

Response 200:
{
  "message": "Recommendation saved"
}
```

---

## Shopping Endpoints

### Search Products
Search Shopee products based on recommendations.

```
GET /shopping/search?category=tops&color=navy&budget_max=100
Authorization: Bearer {token}

Response 200:
{
  "total": 45,
  "products": [
    {
      "id": "prod_uuid",
      "name": "Navy Minimalist T-shirt",
      "price": 35.99,
      "shopeeUrl": "https://shopee.com/...",
      "image": "url",
      "rating": 4.8,
      "reviews": 120
    }
  ]
}
```

### Get Product Details
```
GET /shopping/product/:productId
Authorization: Bearer {token}

Response 200:
{
  "id": "prod_uuid",
  "name": "Navy Minimalist T-shirt",
  "description": "High-quality cotton...",
  "price": 35.99,
  "images": ["url1", "url2"],
  "sizes": ["XS", "S", "M", "L", "XL"],
  "colors": ["navy", "black"],
  "shopeeUrl": "https://shopee.com/...",
  "rating": 4.8,
  "reviews": [...]
}
```

### Create Outfit
Create and save an outfit with multiple items.

```
POST /shopping/outfit
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "Summer Minimalist",
  "avatarId": "uuid",
  "items": [
    {
      "productId": "prod_uuid",
      "size": "M",
      "color": "navy"
    },
    {
      "productId": "prod_uuid2",
      "size": "S",
      "color": "cream"
    }
  ],
  "pose": "standing"
}

Response 201:
{
  "id": "uuid",
  "name": "Summer Minimalist",
  "items": [ ... ],
  "totalPrice": 89.98,
  "previewUrl": "https://cdn.example.com/outfits/uuid.png",
  "createdAt": "2026-06-12T10:00:00Z"
}
```

### Get User Outfits
```
GET /shopping/outfits?limit=20&offset=0
Authorization: Bearer {token}

Response 200:
{
  "total": 5,
  "outfits": [ ... ]
}
```

### Update Outfit
```
PUT /shopping/outfit/:outfitId
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "Updated name",
  "items": [ ... ]
}

Response 200:
{
  "id": "uuid",
  "outfit": { ... }
}
```

### Delete Outfit
```
DELETE /shopping/outfit/:outfitId
Authorization: Bearer {token}

Response 200:
{
  "message": "Outfit deleted successfully"
}
```

### Add to Wishlist
```
POST /shopping/wishlist
Authorization: Bearer {token}
Content-Type: application/json

{
  "productId": "prod_uuid",
  "outfitId": "uuid"
}

Response 201:
{
  "id": "wishlist_uuid",
  "productId": "prod_uuid",
  "addedAt": "2026-06-12T10:00:00Z"
}
```

### Get Wishlist
```
GET /shopping/wishlist?limit=20&offset=0
Authorization: Bearer {token}

Response 200:
{
  "total": 12,
  "items": [ ... ]
}
```

### Remove from Wishlist
```
DELETE /shopping/wishlist/:itemId
Authorization: Bearer {token}

Response 200:
{
  "message": "Item removed from wishlist"
}
```

---

## Virtual Try-On Endpoints

### Start Try-On Session
```
POST /try-on/session
Authorization: Bearer {token}
Content-Type: application/json

{
  "avatarId": "uuid",
  "outfitId": "uuid"
}

Response 201:
{
  "sessionId": "uuid",
  "avatarUrl": "url",
  "outfitUrl": "url"
}
```

### Get Try-On Preview
```
GET /try-on/preview/:sessionId?pose=walking&angle=45
Authorization: Bearer {token}

Response 200:
{
  "imageUrl": "https://cdn.example.com/preview.png",
  "pose": "walking",
  "angle": 45
}
```

### End Try-On Session
```
POST /try-on/session/:sessionId/end
Authorization: Bearer {token}

Response 200:
{
  "message": "Session ended"
}
```

---

## Error Responses

### 400 Bad Request
```json
{
  "error": "Bad Request",
  "message": "Invalid input parameters",
  "details": {
    "field": "email",
    "message": "Invalid email format"
  }
}
```

### 401 Unauthorized
```json
{
  "error": "Unauthorized",
  "message": "Authentication required or invalid token"
}
```

### 403 Forbidden
```json
{
  "error": "Forbidden",
  "message": "You don't have permission to access this resource"
}
```

### 404 Not Found
```json
{
  "error": "Not Found",
  "message": "Resource not found"
}
```

### 429 Too Many Requests
```json
{
  "error": "Rate Limited",
  "message": "Too many requests. Please try again later.",
  "retryAfter": 60
}
```

### 500 Internal Server Error
```json
{
  "error": "Internal Server Error",
  "message": "An unexpected error occurred"
}
```

---

## Rate Limiting

- Standard: 100 requests per minute per user
- Premium: 500 requests per minute per user
- Burst: 1000 requests per hour per user

Headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1623456789
```

---

## Webhooks

### Avatar Generated
```
POST {your_webhook_url}
{
  "event": "avatar.generated",
  "timestamp": "2026-06-12T10:00:00Z",
  "data": {
    "avatarId": "uuid",
    "userId": "uuid"
  }
}
```

### Recommendation Ready
```
POST {your_webhook_url}
{
  "event": "recommendation.ready",
  "timestamp": "2026-06-12T10:00:00Z",
  "data": {
    "recommendationId": "uuid",
    "userId": "uuid"
  }
}
```

---

For SDKs and code examples, visit: https://docs.fantasy-app.com
