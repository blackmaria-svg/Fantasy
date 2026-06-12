# Architecture Guide - Fantasy App

## System Overview

Fantasy is a full-stack application with the following major components:

```
┌─────────────────────────────────────────────────────────────────┐
│                        Frontend (React)                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │ Avatar View  │  │ Recommendations│ │ Virtual Try-On       │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │ Shopping Hub │  │ User Profile │  │ Outfit Manager       │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                    ┌─────────▼─────────┐
                    │   API Gateway     │
                    └─────────┬─────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌───────▼────────┐   ┌───────▼────────┐   ┌───────▼────────┐
│  Auth Service  │   │   Avatar API   │   │ Recommendation │
└────────────────┘   │                │   │  Engine (AI)   │
                     │ - Generation   │   └────────────────┘
                     │ - Scaling      │
                     │ - Customization│
                     └────────────────┘
        │
┌───────▼──────────────────────────────────────┐
│          Database (PostgreSQL)               │
│  ┌──────────────┐  ┌──────────────────────┐ │
│  │ Users        │  │ Measurements         │ │
│  │ Avatars      │  │ Recommendations      │ │
│  │ Outfits      │  │ Shopping History     │ │
│  └──────────────┘  └──────────────────────┘ │
└────────────────────────────────────────────────┘
        │
┌───────▼──────────────────────────────────────┐
│       External Services                      │
│  ┌──────────────┐  ┌──────────────────────┐ │
│  │ Shopee API   │  │ OpenAI / ML Services │ │
│  │ 3D Services  │  │ Storage (S3/CDN)     │ │
│  └──────────────┘  └──────────────────────┘ │
└────────────────────────────────────────────────┘
```

## Core Modules

### 1. Avatar Service
Handles 3D avatar generation and customization.

**Key Components:**
- `AvatarGenerator`: Creates 3D models from measurements
- `MeasurementProcessor`: Converts user input to avatar parameters
- `AvatarCustomizer`: Applies skin tone, hair, and facial features
- `AvatarRenderer`: Uses Three.js/Babylon.js for visualization

**Data Flow:**
```
User Measurements → Validation → Processing → 3D Generation → Rendering
```

### 2. Recommendation Engine
AI-powered outfit and style suggestions.

**Key Components:**
- `BodyShapeAnalyzer`: Determines body type from measurements
- `FaceShapeDetector`: Analyzes facial proportions
- `StyleMatcher`: Recommends styles based on analysis
- `ColorTheorist`: Suggests colors matching skin tone
- `NicheIdentifier`: Categorizes user's style niche

**Algorithm Flow:**
```
Avatar Data → Analysis → Style Profile → Product Matching → Ranking
```

### 3. Virtual Try-On Engine
Real-time outfit simulation on the 3D avatar.

**Key Components:**
- `ClothingLoader`: Imports 3D clothing models
- `PhysicsSimulator`: Simulates clothing behavior
- `PoseController`: Manages avatar animations (standing, walking, sitting)
- `LightingManager`: Realistic lighting in different scenarios
- `CameraController`: Multi-angle visualization

**Rendering Pipeline:**
```
Avatar + Outfit → Physics → Animation → Lighting → Rendering
```

### 4. Shopping Integration
Connects with Shopee and manages product recommendations.

**Key Components:**
- `ShopeeConnector`: API integration with Shopee
- `ProductMatcher`: Matches recommendations to actual products
- `PriceAggregator`: Tracks pricing and availability
- `WishlistManager`: Saves and organizes outfits
- `PurchaseTracker`: Records user's shopping history

**Integration Flow:**
```
Recommendation → Product Search → Details Fetch → Link Generation
```

### 5. User Management
Handles authentication and profile management.

**Key Components:**
- `AuthService`: JWT/OAuth authentication
- `UserProfileManager`: Profile data management
- `MeasurementStorage`: Secure measurement data storage
- `PreferenceManager`: User preferences and settings

## Technology Stack Details

### Frontend Stack
```
React 18+
├── TypeScript
├── Vite (Build tool)
├── Three.js or Babylon.js (3D rendering)
├── Tailwind CSS (Styling)
├── React Query (Data fetching)
├── Zustand (State management)
└── React Router (Navigation)
```

### Backend Stack
```
Node.js / Express.js
├── TypeScript
├── PostgreSQL (Primary database)
├── Redis (Caching)
├── JWT (Authentication)
├── Multer (File uploads)
├── Axios (HTTP client)
└── Socket.io (Real-time updates)
```

### 3D Libraries
```
Three.js / Babylon.js
├── Ready Player Me (Avatar platform - optional)
├── Cannon.js / Babylon Physics (Physics)
├── GLTF/GLB (3D model format)
└── Texture management libraries
```

### AI/ML Services
```
OpenAI API (GPT-4 for recommendations)
├── Image recognition (face & body analysis)
├── Custom ML models (style classification)
└── Claude API (alternative option)
```

## Data Models

### User
```typescript
interface User {
  id: string;
  email: string;
  password: string;
  profile: {
    firstName: string;
    lastName: string;
    profileImage: string;
    skinTone: string;
    hairColor: string;
  };
  preferences: {
    favoriteStyles: string[];
    budgetRange: [number, number];
    shoppingPreferences: string[];
  };
  createdAt: Date;
  updatedAt: Date;
}
```

### Measurements
```typescript
interface Measurements {
  userId: string;
  height: number;
  weight: number;
  shoulders: number;
  chest: number;
  waist: number;
  hips: number;
  thigh: number;
  inseam: number;
  armLength: number;
  neckSize: number;
  shoeSize: number;
  capSize: number;
  recordedAt: Date;
}
```

### Avatar
```typescript
interface Avatar {
  id: string;
  userId: string;
  model3dUrl: string;
  customizations: {
    skinTone: string;
    hairStyle: string;
    hairColor: string;
    eyeColor: string;
    facialFeatures: Record<string, number>;
  };
  createdAt: Date;
  updatedAt: Date;
}
```

### Recommendation
```typescript
interface Recommendation {
  id: string;
  userId: string;
  bodyShape: string;
  faceShape: string;
  styleCategories: string[];
  colors: string[];
  niche: string;
  reasoning: string;
  createdAt: Date;
}
```

### Outfit
```typescript
interface Outfit {
  id: string;
  userId: string;
  name: string;
  items: OutfitItem[];
  avatarPose: 'standing' | 'walking' | 'sitting';
  recommendationId: string;
  shopeeLinks: string[];
  totalPrice: number;
  savedAt: Date;
}
```

## API Endpoints

### Authentication
```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/refresh
POST   /api/auth/logout
GET    /api/auth/profile
```

### Avatar
```
POST   /api/avatar/create
GET    /api/avatar/:id
PUT    /api/avatar/:id/customize
GET    /api/avatar/:id/render
POST   /api/avatar/:id/pose
```

### Measurements
```
POST   /api/measurements/create
GET    /api/measurements/latest
PUT    /api/measurements/:id
DELETE /api/measurements/:id
```

### Recommendations
```
POST   /api/recommendations/generate
GET    /api/recommendations/:id
GET    /api/user/recommendations
```

### Shopping
```
GET    /api/shopping/search
GET    /api/shopping/product/:id
POST   /api/shopping/wishlist
GET    /api/shopping/wishlist
POST   /api/shopping/outfit/create
```

## Deployment Architecture

```
┌─────────────────────────────────────────┐
│         CI/CD Pipeline (GitHub Actions) │
└──────────────┬──────────────────────────┘
               │
        ┌──────▼──────┐
        │ Test & Build│
        └──────┬──────┘
               │
   ┌───────────┼───────────┐
   │           │           │
┌──▼──┐    ┌──▼──┐    ┌──▼──┐
│ Dev │    │Stage│    │Prod │
└─────┘    └─────┘    └─────┘
   │           │           │
   └───────────┼───────────┘
               │
        ┌──────▼──────────┐
        │ Docker Registry │
        └─────────────────┘
               │
   ┌───────────┼───────────┐
   │           │           │
┌──▼──────────────────────────────┐
│ Kubernetes Cluster / Cloud Run  │
│ ├── Frontend Pod               │
│ ├── API Pod                    │
│ ├── Worker Pod                 │
│ └── Database Pod               │
└────────────────────────────────┘
```

## Security Considerations

1. **Authentication**: JWT tokens with refresh rotation
2. **Data Encryption**: TLS/SSL for all communications
3. **Measurement Privacy**: Encrypted storage of sensitive measurements
4. **API Rate Limiting**: Prevent abuse and DDoS
5. **Input Validation**: Strict validation on all inputs
6. **CORS Configuration**: Whitelist allowed origins
7. **Secure File Upload**: Virus scanning and size limits

## Performance Optimization

1. **Caching Strategy**: Redis for frequently accessed data
2. **CDN**: Serve static assets and 3D models from CDN
3. **Code Splitting**: Lazy load components
4. **Database Indexing**: Optimize query performance
5. **Image Optimization**: WebP and responsive images
6. **3D Model Optimization**: LOD (Level of Detail) for avatars
7. **API Response Compression**: GZIP compression

## Scalability Strategy

1. **Horizontal Scaling**: Load-balanced API servers
2. **Database Replication**: Master-slave setup for read scaling
3. **Message Queues**: Async processing with RabbitMQ/Redis
4. **Microservices**: Optional separation of AI/3D services
5. **CDN**: Global distribution of assets
6. **Auto-scaling**: Based on CPU/memory metrics

---

For detailed implementation guides, see the respective service documentation.
