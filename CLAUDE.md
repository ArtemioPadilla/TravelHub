# CLAUDE.md - TravelHub Development Guide
## Complete Guide for AI-Assisted Development

---

## 📋 Document Purpose

This document provides Claude (and other AI assistants) with comprehensive context, architectural decisions, code patterns, and guidelines necessary to effectively assist in TravelHub development. It is designed to be read at the beginning of each development session to maintain consistency and alignment with the project vision.

---

## 🎯 Project Overview

**Name**: TravelHub
**Description**: Open source web platform for managing the complete travel lifecycle - planning, documentation, visualization, and sharing
**Goal**: Create a free and superior alternative to mult.dev with additional features for personal repository and trip planning
**Target Users**: Travelers, content creators, travel bloggers, outdoor enthusiasts
**License**: MIT
**Repo**: https://github.com/username/travelhub

---

## 🏗️ Architecture Overview

### High-Level Architecture

TravelHub is a **Progressive Web App (PWA)** with hybrid architecture: client-side first with optional CyberEco Hub integration for cloud storage and digital sovereignty:

```
User Browser
└── TravelHub PWA (React + TypeScript)
    ├── UI Layer (React Components)
    ├── State Management (Zustand/Jotai)
    ├── Services Layer
    │   ├── Map Engine (MapLibre GL JS)
    │   ├── Storage Manager (IndexedDB + CyberEco Hub)
    │   ├── Video Encoder (Web Worker + WebCodecs)
    │   ├── CyberEco Hub Client (SSO + Data Sync)
    │   ├── Google API Client (Drive + Photos - fallback)
    │   └── Route Calculator (OSRM API)
    ├── Browser APIs
    │   ├── IndexedDB (50GB+ storage)
    │   ├── OPFS (temp files)
    │   ├── WebCodecs (video encoding)
    │   ├── Service Worker (offline + cache)
    │   └── Web Workers (background tasks)
    └── External Services
        ├── CyberEco Hub (primary - identity + storage)
        ├── OpenStreetMap (map tiles)
        ├── Nominatim (geocoding)
        ├── OSRM (routing)
        ├── Google Drive (optional fallback)
        └── Google Photos (optional integration)
```

### Key Architectural Decisions

#### 1. **Hybrid Storage Architecture**
**Decision**: Local-first with optional CyberEco Hub cloud sync
**Rationale**:
- Privacy-first: Data stays on device by default
- User owns their data completely (digital sovereignty)
- Optional cloud backup via CyberEco Hub for multi-device sync
- Zero vendor lock-in (can export all data anytime)
- Works offline from first load

**Implications**:
- Implement robust IndexedDB wrapper with error handling
- CyberEco Hub sync with conflict resolution
- Fallback to local-only mode if CyberEco Hub unavailable
- End-to-end encryption for cloud storage

#### 2. **CyberEco Ecosystem Integration**
**Decision**: Deep integration with CyberEco Hub for identity and data
**Rationale**:
- Single sign-on across all CyberEco applications
- Cross-app data sharing (e.g., expenses with JustSplit)
- Unified digital identity and user profile
- Aligned with digital sovereignty mission
- Enhanced user experience through ecosystem benefits

**Implications**:
- OAuth 2.0/OpenID Connect for authentication
- CyberEco Hub API client for all data operations
- Graceful degradation when CyberEco Hub unavailable
- Privacy by design: end-to-end encryption

#### 3. **Static Hosting**
**Decision**: Deploy as static files on Cloudflare Pages
**Rationale**:
- Free hosting with unlimited bandwidth
- Global edge network (low latency worldwide)
- Auto-deploy from GitHub
- Zero server management
- Infinite scalability via CDN

**Implications**:
- No server-side rendering (use pre-rendering for SEO)
- No backend API routes (all via external APIs)
- Environment variables via build-time injection

#### 4. **Client-Side First**
**Decision**: All processing occurs in user's browser
**Rationale**:
- Zero operational costs
- Infinite scalability via CDN
- Privacy-first (no data leaves device unless user opts in)
- No backend to maintain

**Implications**:
- Use Web Workers to avoid blocking UI
- Implement progressive enhancement (works without JS for basic content)
- Critical memory management (monitor usage, aggressive cleanup)

#### 5. **React Framework**
**Decision**: React 18+ with TypeScript
**Rationale**:
- Mature and well-documented ecosystem
- Large hiring pool (more widely known than Svelte/Vue)
- Excellent tooling (React DevTools, testing libraries)
- Massive community support

**Implications**:
- Use function components + hooks exclusively
- Implement code splitting by routes
- Memoization strategies for performance

---

## 🛠️ Tech Stack

### Core Technologies

| Category | Technology | Version | Purpose |
|----------|-----------|---------|---------|
| **Framework** | React | 18.3+ | UI library |
| **Language** | TypeScript | 5.0+ | Type safety |
| **Build Tool** | Vite | 5.0+ | Fast dev server, optimized builds |
| **Styling** | Tailwind CSS | 3.4+ | Utility-first CSS |
| **State** | Zustand | 4.5+ | Lightweight state management |
| **Routing** | React Router | 6.20+ | Client-side routing |
| **Maps** | MapLibre GL JS | 4.0+ | Map rendering |
| **Storage** | IndexedDB | Native | Local persistence |
| **Cloud Storage** | CyberEco Hub | API | Primary cloud storage + identity |
| **Video** | WebCodecs API | Native | Hardware-accelerated encoding |
| **Offline** | Service Worker | Native | PWA offline support |

### Additional Libraries

| Library | Purpose | Notes |
|---------|---------|-------|
| `idb` | IndexedDB wrapper | Promise-based API |
| `@turf/turf` | Geospatial calculations | Line simplification, distances |
| `date-fns` | Date manipulation | Lightweight alternative to moment.js |
| `react-dropzone` | File uploads | Drag & drop support |
| `react-map-gl` | MapLibre React wrapper | Declarative map components |
| `sharp` (via wasm) | Image processing | Resize, compress client-side |
| `ffmpeg.wasm` | Video encoding fallback | When WebCodecs unavailable |
| `exifreader` | EXIF parsing | Extract GPS from photos |
| `@headlessui/react` | Unstyled components | Modals, dropdowns, tabs |
| `lucide-react` | Icon library | Lightweight, tree-shakeable |
| `react-hook-form` | Form management | Performance, validation |
| `zod` | Schema validation | TypeScript-first validation |

### External APIs

| API | Purpose | Free Tier | Role |
|-----|---------|-----------|------|
| **CyberEco Hub** | Identity + Cloud storage | Free tier TBD | Primary data layer |
| **OpenStreetMap** | Map tiles | Unlimited | Required |
| **Nominatim** | Geocoding | 1 req/sec | Required |
| **OSRM** | Routing | Unlimited | Required |
| **Google Drive** | Cloud storage | 15GB free | Optional fallback |
| **Google Photos** | Photo management | Unlimited | Optional |
| **Open-Meteo** | Weather data | Unlimited | Optional |
| **Cohere** | LLM (route parsing) | 1M tokens/mo | Optional |

---

## 📁 Project Structure

```
travelhub/
├── public/
│   ├── manifest.json          # PWA manifest
│   ├── sw.js                  # Service Worker
│   └── icons/                 # App icons (various sizes)
├── src/
│   ├── components/            # React components
│   │   ├── common/            # Shared components (Button, Modal, etc.)
│   │   ├── map/               # Map-related components
│   │   ├── trip/              # Trip-specific components
│   │   ├── video/             # Video export components
│   │   └── layout/            # Layout components (Header, Sidebar)
│   ├── hooks/                 # Custom React hooks
│   │   ├── useTrips.ts
│   │   ├── useStorage.ts
│   │   ├── useCyberEcoHub.ts
│   │   ├── useGoogleDrive.ts
│   │   └── useVideoExport.ts
│   ├── services/              # Business logic services
│   │   ├── storage/           # IndexedDB wrapper
│   │   ├── cybereco/          # CyberEco Hub API client
│   │   ├── video/             # Video encoding logic
│   │   ├── maps/              # Map utilities
│   │   ├── google/            # Google API clients
│   │   └── routing/           # Route calculation
│   ├── workers/               # Web Workers
│   │   ├── video-encoder.worker.ts
│   │   └── image-processor.worker.ts
│   ├── store/                 # Zustand stores
│   │   ├── tripStore.ts
│   │   ├── userStore.ts
│   │   ├── uiStore.ts
│   │   └── settingsStore.ts
│   ├── types/                 # TypeScript types
│   │   ├── trip.ts
│   │   ├── user.ts
│   │   ├── waypoint.ts
│   │   └── video.ts
│   ├── utils/                 # Utility functions
│   │   ├── gpx-parser.ts
│   │   ├── geo-utils.ts
│   │   └── date-utils.ts
│   ├── pages/                 # Page components (routes)
│   │   ├── Home.tsx
│   │   ├── TripEditor.tsx
│   │   ├── Gallery.tsx
│   │   ├── Explorer.tsx
│   │   └── Settings.tsx
│   ├── App.tsx                # Root component
│   ├── main.tsx               # Entry point
│   └── index.css              # Global styles
├── tests/                     # Test files
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docs/                      # Documentation
│   ├── API.md
│   ├── ARCHITECTURE.md
│   └── CONTRIBUTING.md
├── .github/                   # GitHub specific
│   └── workflows/
│       └── deploy.yml         # CI/CD pipeline
├── package.json
├── tsconfig.json
├── vite.config.ts
├── tailwind.config.js
├── README.md
├── LICENSE
├── CLAUDE.md                  # This file
└── .env.example               # Example environment variables
```

---

## 🎨 Design System

### Color Palette

```typescript
// colors.ts
export const colors = {
  primary: {
    50: '#eff6ff',
    100: '#dbeafe',
    500: '#3b82f6',  // Main brand color
    600: '#2563eb',
    900: '#1e3a8a',
  },
  secondary: {
    500: '#8b5cf6',  // Accent color
  },
  success: '#10b981',
  warning: '#f59e0b',
  error: '#ef4444',
  neutral: {
    50: '#fafafa',
    100: '#f5f5f5',
    200: '#e5e5e5',
    500: '#737373',
    900: '#171717',
  }
}
```

### Typography

```typescript
// Typography scale (Tailwind classes)
h1: 'text-4xl font-bold'      // 36px
h2: 'text-3xl font-semibold'  // 30px
h3: 'text-2xl font-semibold'  // 24px
h4: 'text-xl font-medium'     // 20px
body: 'text-base'             // 16px
small: 'text-sm'              // 14px
tiny: 'text-xs'               // 12px
```

### Spacing

Use Tailwind spacing scale (4px increments):
- `p-2` = 8px
- `p-4` = 16px
- `p-6` = 24px
- `p-8` = 32px

### Components

All components should follow this structure:

```typescript
// Example: Button component
interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  loading?: boolean;
  onClick?: () => void;
}

export const Button: React.FC<ButtonProps> = ({
  children,
  variant = 'primary',
  size = 'md',
  disabled = false,
  loading = false,
  onClick,
}) => {
  // Implementation
}
```

---

## 📊 Data Models

### User

```typescript
interface User {
  // CyberEco Hub Identity
  id: string;                    // CyberEco Hub user ID
  email: string;                 // Primary email
  username?: string;             // Unique username

  // Profile Information
  displayName: string;           // Full name or nickname
  avatar?: string;               // Profile picture URL
  bio?: string;                  // Short biography

  // Account Status
  createdAt: Date;
  lastLoginAt: Date;
  isPremium: boolean;            // Premium/paid tier status

  // Privacy
  isPublic: boolean;             // Profile visibility
  showEmail: boolean;            // Email visible to others

  // CyberEco Hub Integration
  cyberEcoHubId: string;         // CyberEco Hub account ID
  syncEnabled: boolean;          // Auto-sync to CyberEco Hub
  lastSyncedAt?: Date;
}

interface UserPreferences {
  // Display
  theme: 'light' | 'dark' | 'auto';
  language: string;              // ISO 639-1 code
  units: 'metric' | 'imperial';

  // Synchronization
  autoSync: boolean;             // Auto-sync to CyberEco Hub
  syncInterval: number;          // Minutes between syncs
  googleDriveSync: boolean;      // Enable Google Drive backup (fallback)

  // Privacy
  defaultTripPrivacy: 'private' | 'public' | 'unlisted';
  shareLocation: boolean;
  allowAnalytics: boolean;

  // Notifications
  emailNotifications: boolean;
  pushNotifications: boolean;

  // Map Preferences
  defaultMapStyle: 'streets' | 'satellite' | 'terrain' | 'dark';
  showTraffic: boolean;

  // Video Export
  defaultResolution: '720p' | '1080p' | '4k';
  defaultFPS: 30 | 60;
  defaultAspectRatio: '16:9' | '9:16' | '1:1' | '4:5';
}
```

### Trip

```typescript
interface Trip {
  id: string;                    // UUID
  userId: string;                // Owner user ID
  title: string;
  description?: string;
  startDate: Date;
  endDate?: Date;
  coverPhoto?: string;           // URL or blob URL
  waypoints: Waypoint[];
  photos: Photo[];
  notes: Note[];
  budget?: Budget;
  collaborators?: Collaborator[];
  tags: string[];
  isPublic: boolean;
  shareSlug?: string;            // For public sharing
  createdAt: Date;
  updatedAt: Date;

  // CyberEco Hub Integration
  syncedToCyberEco?: boolean;
  cyberEcoHubFileId?: string;

  // Google Drive (fallback)
  syncedToGoogleDrive?: boolean;
  googleDriveFileId?: string;
}
```

### Waypoint

```typescript
interface Waypoint {
  id: string;
  tripId: string;
  order: number;                 // Sequence in trip
  lat: number;
  lng: number;
  name: string;
  address?: string;
  arrivalDate?: Date;
  departureDate?: Date;
  transportMode: TransportMode;  // 'plane' | 'car' | 'train' | 'boat' | 'bike' | 'walk'
  duration?: number;             // Minutes at location
  notes?: string;
  photos: string[];              // Photo IDs
  createdAt: Date;
}

type TransportMode = 'plane' | 'car' | 'train' | 'boat' | 'bike' | 'walk' | 'bus' | 'ferry';
```

### Photo

```typescript
interface Photo {
  id: string;
  tripId: string;
  waypointId?: string;           // If associated with waypoint
  url: string;                   // Blob URL or Google Photos URL
  thumbnailUrl?: string;
  fileName: string;
  fileSize: number;
  mimeType: string;
  lat?: number;                  // From EXIF
  lng?: number;
  capturedAt?: Date;             // From EXIF
  uploadedAt: Date;
  caption?: string;
  tags: string[];
  googlePhotosId?: string;
}
```

### VideoExport

```typescript
interface VideoExportSettings {
  tripId: string;
  resolution: '720p' | '1080p' | '4k';
  fps: 30 | 60;
  duration: number;              // Seconds
  aspectRatio: '16:9' | '9:16' | '1:1' | '4:5';
  theme: VideoTheme;
  showPhotos: boolean;
  musicUrl?: string;
  animationSpeed: number;        // 0.5 to 2.0
}

interface VideoTheme {
  globeStyle: 'realistic' | 'minimal' | 'dark';
  routeColor: string;
  routeWidth: number;
  iconSet: 'modern' | 'classic' | 'minimal';
  showLabels: boolean;
  backgroundColor: string;
}
```

---

## 🔧 Core Services Specifications

### 1. Storage Service

**File**: `src/services/storage/storage.service.ts`

```typescript
/**
 * Storage Service - Hybrid storage with IndexedDB + CyberEco Hub
 *
 * Strategy:
 * 1. All data saved to local IndexedDB immediately (fast, offline-capable)
 * 2. Auto-sync to CyberEco Hub every 5 minutes if changes detected
 * 3. Conflict resolution: server wins for metadata, merge for content
 * 4. Fallback to Google Drive if CyberEco Hub unavailable
 *
 * Stores: trips, photos, settings, cache
 * Local Quota: 50GB+ (monitor usage, warn at 80%)
 */

class StorageService {
  private db: IDBDatabase;
  private cyberEcoClient: CyberEcoHubClient;

  async init(): Promise<void>
  async getTrips(): Promise<Trip[]>
  async getTrip(id: string): Promise<Trip | null>
  async saveTrip(trip: Trip): Promise<void>
  async deleteTrip(id: string): Promise<void>

  async savePhoto(photo: Photo, blob: Blob): Promise<void>
  async getPhoto(id: string): Promise<Photo | null>
  async getPhotoBlob(id: string): Promise<Blob | null>

  async getStorageUsage(): Promise<StorageEstimate>
  async clearCache(): Promise<void>

  // CyberEco Hub sync (primary)
  async syncToCyberEcoHub(tripId: string): Promise<void>
  async syncFromCyberEcoHub(tripId: string): Promise<void>
  async enableAutoSync(enabled: boolean): Promise<void>

  // Google Drive sync (fallback)
  async syncToGoogleDrive(tripId: string): Promise<void>
}
```

**Key Features**:
- Auto-versioning with migrations
- Automatic backup every 5 minutes if changes detected
- Photo compression before saving
- Automatic cleanup of temporary files
- End-to-end encryption for cloud storage
- Graceful fallback when CyberEco Hub unavailable

### 2. CyberEco Hub API Service

**File**: `src/services/cybereco/cybereco-hub.service.ts`

```typescript
/**
 * CyberEco Hub API Service
 *
 * Handles authentication, data storage, and cross-app integration
 * with CyberEco Hub ecosystem
 *
 * OAuth 2.0/OpenID Connect for SSO
 * End-to-end encryption for all data
 */

class CyberEcoHubService {
  // Authentication
  async authenticate(): Promise<void>
  async isAuthenticated(): Promise<boolean>
  async logout(): Promise<void>
  async refreshToken(): Promise<void>

  // User Profile
  async getUserProfile(): Promise<User>
  async updateProfile(updates: Partial<User>): Promise<void>

  // Data Storage
  async uploadFile(blob: Blob, filename: string, encrypt: boolean): Promise<string>
  async downloadFile(fileId: string, decrypt: boolean): Promise<Blob>
  async listFiles(folderId?: string): Promise<CyberEcoFile[]>
  async deleteFile(fileId: string): Promise<void>

  // Trip Sync
  async uploadTrip(trip: Trip): Promise<string>
  async downloadTrip(tripId: string): Promise<Trip>
  async syncTrip(trip: Trip): Promise<SyncResult>

  // Cross-App Data Sharing
  async shareDataWithApp(appId: string, data: any): Promise<void>
  async requestDataFromApp(appId: string, dataType: string): Promise<any>
  async revokeAppAccess(appId: string): Promise<void>

  // Digital Sovereignty
  async exportAllData(): Promise<Blob>  // Complete data export
  async deleteAllData(): Promise<void>   // Delete all user data

  // Health Check
  async healthCheck(): Promise<boolean>
  async getStorageQuota(): Promise<StorageQuota>
}
```

**OAuth Flow**:

```typescript
// 1. User clicks "Sign in with CyberEco Hub"
// 2. Generate PKCE code challenge
const codeVerifier = generateRandomString(128);
const codeChallenge = await sha256(codeVerifier);

// 3. Redirect to CyberEco Hub OAuth
const authUrl = `https://hub.cybere.co/oauth/authorize?
  client_id=${CLIENT_ID}&
  redirect_uri=${REDIRECT_URI}&
  response_type=code&
  scope=profile email storage&
  code_challenge=${codeChallenge}&
  code_challenge_method=S256`;

// 4. Handle callback
const urlParams = new URLSearchParams(window.location.search);
const code = urlParams.get('code');

// 5. Exchange code for tokens
const response = await fetch('https://hub.cybere.co/oauth/token', {
  method: 'POST',
  body: JSON.stringify({
    code,
    client_id: CLIENT_ID,
    redirect_uri: REDIRECT_URI,
    grant_type: 'authorization_code',
    code_verifier: codeVerifier,
  })
});

const { access_token, refresh_token } = await response.json();
// Store encrypted in IndexedDB
```

**Sync Strategy**:

```typescript
async function syncTripToCyberEcoHub(trip: Trip): Promise<void> {
  // 1. Encrypt trip data
  const tripData = JSON.stringify(trip);
  const encryptedData = await encryptData(tripData, userKey);
  const blob = new Blob([encryptedData], { type: 'application/json' });

  // 2. Check if file exists
  if (trip.cyberEcoHubFileId) {
    // Update existing file
    await cyberEcoHub.updateFile(trip.cyberEcoHubFileId, blob);
  } else {
    // Create new file
    const fileId = await cyberEcoHub.uploadFile(blob, `${trip.title}.json.enc`, true);
    trip.cyberEcoHubFileId = fileId;
  }

  // 3. Update local record
  trip.syncedToCyberEco = true;
  trip.updatedAt = new Date();
  await storageService.saveTrip(trip);
}

// Auto-sync hook
function useAutoSync(trip: Trip) {
  const { syncEnabled, syncInterval } = useUserPreferences();

  useEffect(() => {
    if (!syncEnabled) return;

    const timer = setInterval(async () => {
      if (trip.updatedAt > trip.lastSyncedAt) {
        await syncTripToCyberEcoHub(trip);
      }
    }, syncInterval * 60 * 1000);

    return () => clearInterval(timer);
  }, [trip, syncEnabled, syncInterval]);
}
```

### 3. Video Encoder Service

**File**: `src/services/video/video-encoder.service.ts`

```typescript
/**
 * Video Encoder Service
 *
 * Strategy:
 * 1. Try WebCodecs API (Chrome/Edge) - fastest, hardware-accelerated
 * 2. Fallback to ffmpeg.wasm (Firefox/Safari) - slower but compatible
 *
 * Uses Web Worker to avoid blocking UI
 */

class VideoEncoderService {
  async exportVideo(
    trip: Trip,
    settings: VideoExportSettings,
    onProgress: (percent: number) => void
  ): Promise<Blob>

  async generatePreview(
    trip: Trip,
    settings: VideoExportSettings
  ): Promise<Blob>  // Low-quality quick preview

  async cancel(): Promise<void>

  isWebCodecsSupported(): boolean
}
```

**Implementation Details**:

```typescript
// video-encoder.worker.ts
async function encodeWithWebCodecs(frames: ImageBitmap[], settings: VideoExportSettings) {
  const encoder = new VideoEncoder({
    output: (chunk, metadata) => {
      // Stream encoded chunks to main thread
      self.postMessage({ type: 'chunk', data: chunk })
    },
    error: (error) => {
      self.postMessage({ type: 'error', error: error.message })
    }
  });

  encoder.configure({
    codec: 'vp09.00.10.08',  // VP9 for web
    width: settings.width,
    height: settings.height,
    bitrate: settings.bitrate,
    framerate: settings.fps,
  });

  for (const [index, frame] of frames.entries()) {
    encoder.encode(frame, { keyFrame: index % 150 === 0 });
    self.postMessage({ type: 'progress', percent: (index / frames.length) * 100 });
  }

  await encoder.flush();
}
```

### 4. Map Service

**File**: `src/services/maps/map.service.ts`

```typescript
/**
 * Map Service - MapLibre GL JS wrapper
 */

class MapService {
  private map: maplibregl.Map;

  init(container: HTMLElement): void

  addMarker(waypoint: Waypoint): maplibregl.Marker
  addRoute(waypoints: Waypoint[], mode: TransportMode): void

  fitBounds(waypoints: Waypoint[]): void
  flyTo(waypoint: Waypoint, zoom?: number): void

  // For video generation
  async captureFrame(width: number, height: number): Promise<ImageBitmap>

  // Animate camera for video
  async animatePath(
    waypoints: Waypoint[],
    onFrame: (frame: ImageBitmap) => void,
    settings: AnimationSettings
  ): Promise<void>
}
```

### 5. Google API Service (Fallback)

**File**: `src/services/google/google-api.service.ts`

```typescript
/**
 * Google API Service (Fallback Option)
 *
 * Used as fallback when CyberEco Hub unavailable
 * or as alternative sync option
 *
 * OAuth 2.0 with PKCE
 * Scopes: drive.file, photos.readonly
 */

class GoogleAPIService {
  async authenticate(): Promise<void>
  async isAuthenticated(): Promise<boolean>
  async logout(): Promise<void>

  // Drive
  async uploadFile(blob: Blob, filename: string, folderId?: string): Promise<string>
  async downloadFile(fileId: string): Promise<Blob>
  async listFiles(folderId?: string): Promise<DriveFile[]>

  // Photos
  async listAlbums(): Promise<Album[]>
  async getPhotosInAlbum(albumId: string): Promise<Photo[]>
  async getPhotosByDateRange(start: Date, end: Date): Promise<Photo[]>
}
```

---

## 🎯 Key Features Implementation

### Feature 1: CyberEco Hub Authentication

**User Flow**:
1. User visits TravelHub landing page
2. Clicks "Sign in with CyberEco Hub"
3. Redirected to CyberEco Hub SSO
4. Authenticates with email/password, passkey, or social login
5. Returns to TravelHub with auth token
6. Profile synced automatically

**Components**:
- `LoginPage.tsx` - Landing page with sign-in options
- `AuthCallback.tsx` - Handles OAuth callback
- `ProfileMenu.tsx` - User profile dropdown
- `SyncIndicator.tsx` - Shows sync status

**Implementation Tips**:
```typescript
// useAuth hook
function useAuth() {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    checkAuthStatus();
  }, []);

  const checkAuthStatus = async () => {
    try {
      const isAuth = await cyberEcoHub.isAuthenticated();
      if (isAuth) {
        const profile = await cyberEcoHub.getUserProfile();
        setUser(profile);
      }
    } catch (error) {
      console.error('Auth check failed:', error);
    } finally {
      setLoading(false);
    }
  };

  const login = async () => {
    await cyberEcoHub.authenticate();
  };

  const logout = async () => {
    await cyberEcoHub.logout();
    setUser(null);
  };

  return { user, loading, login, logout };
}

// Show sync status
function SyncIndicator() {
  const { lastSyncedAt, syncStatus } = useSyncStatus();

  return (
    <div className="flex items-center gap-2">
      {syncStatus === 'syncing' && <Spinner />}
      {syncStatus === 'synced' && <CheckIcon className="text-green-500" />}
      {syncStatus === 'error' && <ErrorIcon className="text-red-500" />}
      <span className="text-sm text-gray-600">
        {lastSyncedAt ? `Synced ${formatRelative(lastSyncedAt)}` : 'Not synced'}
      </span>
    </div>
  );
}
```

### Feature 2: Trip Creation

**User Flow**:
1. Click "New Trip"
2. Enter title, dates
3. Add waypoints (click map, search, or import GPX)
4. Customize transport modes between waypoints
5. Add photos (upload or import from Google Photos)
6. Save trip (auto-syncs to CyberEco Hub)

**Components**:
- `TripEditor.tsx` - Main editor page
- `MapCanvas.tsx` - Interactive map
- `WaypointList.tsx` - Sidebar with waypoint list
- `PhotoUploader.tsx` - Photo upload/import
- `TransportModeSelector.tsx` - Choose transport between waypoints

**Implementation Tips**:
```typescript
// Auto-save every 30 seconds
useEffect(() => {
  const timer = setInterval(() => {
    if (hasUnsavedChanges) {
      saveTrip(currentTrip);
    }
  }, 30000);
  return () => clearInterval(timer);
}, [currentTrip, hasUnsavedChanges]);

// Undo/Redo stack
const [history, setHistory] = useState<Trip[]>([]);
const [historyIndex, setHistoryIndex] = useState(0);

const undo = () => {
  if (historyIndex > 0) {
    setHistoryIndex(i => i - 1);
    setCurrentTrip(history[historyIndex - 1]);
  }
};
```

### Feature 3: Video Export

**User Flow**:
1. Open trip
2. Click "Export Video"
3. Configure settings (resolution, theme, music)
4. Preview low-quality version
5. Export full quality
6. Download MP4 file

**Implementation**:

```typescript
// Export button click handler
const handleExport = async () => {
  setIsExporting(true);
  setProgress(0);

  try {
    // 1. Generate frames
    const frames = await generateFrames(trip, settings, (p) => setProgress(p * 0.5));

    // 2. Encode video
    const videoBlob = await videoEncoder.exportVideo(trip, settings, (p) => {
      setProgress(0.5 + p * 0.5);
    });

    // 3. Trigger download
    const url = URL.createObjectURL(videoBlob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `${trip.title}-${Date.now()}.mp4`;
    a.click();
    URL.revokeObjectURL(url);

    toast.success('Video exported successfully!');
  } catch (error) {
    toast.error('Export failed: ' + error.message);
  } finally {
    setIsExporting(false);
  }
};

// Frame generation
async function generateFrames(trip: Trip, settings: VideoExportSettings): Promise<ImageBitmap[]> {
  const frames: ImageBitmap[] = [];
  const totalFrames = settings.duration * settings.fps;
  const waypoints = trip.waypoints;

  for (let i = 0; i < totalFrames; i++) {
    // Calculate camera position for this frame
    const progress = i / totalFrames;
    const position = interpolateAlongPath(waypoints, progress);

    // Position map camera
    map.flyTo({
      center: [position.lng, position.lat],
      zoom: position.zoom,
      bearing: position.bearing,
      pitch: position.pitch,
      essential: false
    });

    // Wait for map to render
    await waitForMapRender();

    // Capture frame
    const frame = await map.captureFrame(settings.width, settings.height);
    frames.push(frame);

    onProgress((i / totalFrames) * 100);
  }

  return frames;
}
```

### Feature 4: Cross-App Data Sharing

**User Flow**:
1. User creates trip with expenses in TravelHub
2. Clicks "Share expenses with JustSplit"
3. System exports expense data to CyberEco Hub shared layer
4. JustSplit app can now access and import expenses
5. User maintains single source of truth

**Implementation**:

```typescript
async function shareExpensesWithJustSplit(trip: Trip) {
  // 1. Extract expense data
  const expenseData = {
    tripId: trip.id,
    tripName: trip.title,
    expenses: trip.waypoints.flatMap(w => w.expenses || []),
    totalCost: calculateTotalCost(trip),
    currency: trip.budget?.currency || 'USD',
    participants: trip.collaborators?.map(c => c.email) || [],
  };

  // 2. Share via CyberEco Hub
  await cyberEcoHub.shareDataWithApp('justsplit', {
    dataType: 'trip-expenses',
    data: expenseData,
    permissions: ['read', 'write'],
  });

  // 3. Notify user
  toast.success('Expenses shared with JustSplit');
}

// Listen for data requests from other apps
cyberEcoHub.on('data-request', async (event) => {
  const { appId, dataType } = event;

  // Prompt user for permission
  const granted = await promptUserPermission(appId, dataType);

  if (granted) {
    const data = await fetchRequestedData(dataType);
    await cyberEcoHub.shareDataWithApp(appId, data);
  }
});
```

### Feature 5: Interactive Explorer

**User Flow**:
1. Open trip
2. Click "Explore"
3. See story-style view with map + photos
4. Swipe to navigate through journey
5. Click photo to view full-screen
6. Share link with friends

**Components**:
- `Explorer.tsx` - Main explorer page
- `StoryView.tsx` - Instagram-style story navigation
- `Timeline.tsx` - Horizontal timeline scrubber
- `PhotoViewer.tsx` - Full-screen photo viewer
- `ShareDialog.tsx` - Generate public share link

**Implementation**:

```typescript
// Story navigation
const StoryView: React.FC<{ trip: Trip }> = ({ trip }) => {
  const [currentIndex, setCurrentIndex] = useState(0);
  const waypoint = trip.waypoints[currentIndex];

  const handleNext = () => {
    if (currentIndex < trip.waypoints.length - 1) {
      setCurrentIndex(i => i + 1);
    }
  };

  const handlePrev = () => {
    if (currentIndex > 0) {
      setCurrentIndex(i => i - 1);
    }
  };

  return (
    <div className="relative h-screen w-screen">
      {/* Background map */}
      <Map center={[waypoint.lng, waypoint.lat]} zoom={12} />

      {/* Overlay content */}
      <div className="absolute inset-0 pointer-events-none">
        {/* Timeline at top */}
        <Timeline
          waypoints={trip.waypoints}
          currentIndex={currentIndex}
          onSeek={setCurrentIndex}
        />

        {/* Photos */}
        <div className="flex items-center justify-center h-full">
          {waypoint.photos.map(photoId => (
            <Photo key={photoId} id={photoId} />
          ))}
        </div>

        {/* Navigation */}
        <button
          onClick={handlePrev}
          className="absolute left-4 top-1/2 pointer-events-auto"
        >
          Previous
        </button>
        <button
          onClick={handleNext}
          className="absolute right-4 top-1/2 pointer-events-auto"
        >
          Next
        </button>
      </div>
    </div>
  );
};
```

---

## 🧪 Testing Strategy

### Unit Tests

Use Vitest + React Testing Library:

```typescript
// Example: TripStore test
import { describe, it, expect, beforeEach } from 'vitest';
import { useTripStore } from './tripStore';

describe('TripStore', () => {
  beforeEach(() => {
    useTripStore.setState({ trips: [] });
  });

  it('should add a new trip', () => {
    const newTrip = createMockTrip();
    useTripStore.getState().addTrip(newTrip);

    expect(useTripStore.getState().trips).toHaveLength(1);
    expect(useTripStore.getState().trips[0].id).toBe(newTrip.id);
  });

  it('should delete a trip', () => {
    const trip = createMockTrip();
    useTripStore.getState().addTrip(trip);
    useTripStore.getState().deleteTrip(trip.id);

    expect(useTripStore.getState().trips).toHaveLength(0);
  });
});
```

### Integration Tests

Test user flows:

```typescript
// Example: Trip creation flow
import { render, screen, userEvent } from '@testing-library/react';

it('should create a new trip', async () => {
  render(<App />);

  // Click new trip button
  await userEvent.click(screen.getByText('New Trip'));

  // Fill form
  await userEvent.type(screen.getByLabelText('Title'), 'Summer Vacation');
  await userEvent.type(screen.getByLabelText('Start Date'), '2025-06-01');

  // Add waypoint
  await userEvent.click(screen.getByText('Add Waypoint'));
  await userEvent.type(screen.getByLabelText('Location'), 'Paris, France');

  // Save
  await userEvent.click(screen.getByText('Save Trip'));

  // Verify trip appears in gallery
  expect(await screen.findByText('Summer Vacation')).toBeInTheDocument();
});
```

### E2E Tests

Use Playwright for critical flows:

```typescript
// tests/e2e/video-export.spec.ts
import { test, expect } from '@playwright/test';

test('should export video', async ({ page }) => {
  await page.goto('/');

  // Create trip
  await page.click('text=New Trip');
  await page.fill('input[name="title"]', 'Test Trip');
  await page.click('text=Save');

  // Export video
  await page.click('text=Export Video');
  await page.selectOption('select[name="resolution"]', '1080p');
  await page.click('text=Export');

  // Wait for export
  await expect(page.locator('text=Exporting')).toBeVisible();
  await expect(page.locator('text=Export Complete')).toBeVisible({ timeout: 60000 });

  // Verify download
  const download = await page.waitForEvent('download');
  expect(download.suggestedFilename()).toMatch(/\.mp4$/);
});
```

### Performance Tests

Monitor key metrics:

```typescript
// Lighthouse CI in GitHub Actions
module.exports = {
  ci: {
    collect: {
      startServerCommand: 'npm run preview',
      url: ['http://localhost:4173'],
      numberOfRuns: 3,
    },
    assert: {
      assertions: {
        'categories:performance': ['error', { minScore: 0.9 }],
        'categories:accessibility': ['error', { minScore: 0.9 }],
        'categories:best-practices': ['error', { minScore: 0.9 }],
        'categories:seo': ['error', { minScore: 0.9 }],
      },
    },
  },
};
```

---

## 🚀 Development Workflow

### 1. Local Development

```bash
# Clone repo
git clone https://github.com/username/travelhub.git
cd travelhub

# Install dependencies
npm install

# Start dev server
npm run dev

# Open browser
# http://localhost:5173
```

### 2. Code Style

Use ESLint + Prettier:

```javascript
// .eslintrc.js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'prettier',
  ],
  rules: {
    'react/react-in-jsx-scope': 'off',  // Not needed in React 18
    '@typescript-eslint/explicit-module-boundary-types': 'off',
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
  },
};
```

### 3. Git Workflow

```bash
# Feature branch
git checkout -b feature/add-gpx-import

# Make changes, commit often
git commit -m "feat: add GPX file parser"
git commit -m "feat: integrate GPX import with trip editor"

# Push and create PR
git push origin feature/add-gpx-import

# PR description should reference issue:
# "Closes #42"
```

**Commit Message Convention**:
- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation only
- `style:` Formatting, missing semi colons, etc.
- `refactor:` Code change that neither fixes a bug nor adds a feature
- `perf:` Performance improvement
- `test:` Adding tests
- `chore:` Maintenance

### 4. CI/CD Pipeline

GitHub Actions workflow:

```yaml
# .github/workflows/deploy.yml
name: Deploy to Cloudflare Pages

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Run linter
        run: npm run lint

      - name: Build
        run: npm run build
        env:
          VITE_CYBERECO_CLIENT_ID: ${{ secrets.CYBERECO_CLIENT_ID }}
          VITE_GOOGLE_CLIENT_ID: ${{ secrets.GOOGLE_CLIENT_ID }}

      - name: Deploy to Cloudflare Pages
        uses: cloudflare/pages-action@v1
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          accountId: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
          projectName: travelhub
          directory: dist
          gitHubToken: ${{ secrets.GITHUB_TOKEN }}
```

---

## 📝 Code Patterns & Best Practices

### 1. Component Pattern

```typescript
/**
 * Component file structure:
 * 1. Imports
 * 2. Types/Interfaces
 * 3. Component
 * 4. Exports
 */

// Good example
import React, { useState, useEffect } from 'react';
import { useTripStore } from '@/store/tripStore';
import { Button } from '@/components/common/Button';

interface TripCardProps {
  trip: Trip;
  onEdit: (id: string) => void;
  onDelete: (id: string) => void;
}

export const TripCard: React.FC<TripCardProps> = ({ trip, onEdit, onDelete }) => {
  const [isHovered, setIsHovered] = useState(false);

  return (
    <div
      className="rounded-lg shadow-md p-4 hover:shadow-lg transition-shadow"
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <img src={trip.coverPhoto} alt={trip.title} className="w-full h-48 object-cover" />
      <h3 className="text-xl font-semibold mt-2">{trip.title}</h3>
      <p className="text-gray-600">{formatDateRange(trip.startDate, trip.endDate)}</p>

      {isHovered && (
        <div className="flex gap-2 mt-2">
          <Button onClick={() => onEdit(trip.id)}>Edit</Button>
          <Button variant="secondary" onClick={() => onDelete(trip.id)}>Delete</Button>
        </div>
      )}
    </div>
  );
};
```

### 2. Custom Hooks Pattern

```typescript
/**
 * Custom hooks should:
 * - Start with "use"
 * - Return state and actions
 * - Handle side effects internally
 */

// Good example
function useTrips() {
  const [trips, setTrips] = useState<Trip[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    loadTrips();
  }, []);

  const loadTrips = async () => {
    try {
      setLoading(true);
      const data = await storageService.getTrips();
      setTrips(data);
    } catch (err) {
      setError(err as Error);
    } finally {
      setLoading(false);
    }
  };

  const addTrip = async (trip: Trip) => {
    await storageService.saveTrip(trip);
    setTrips(prev => [...prev, trip]);
  };

  const deleteTrip = async (id: string) => {
    await storageService.deleteTrip(id);
    setTrips(prev => prev.filter(t => t.id !== id));
  };

  return {
    trips,
    loading,
    error,
    addTrip,
    deleteTrip,
    refreshTrips: loadTrips,
  };
}
```

### 3. Error Handling Pattern

```typescript
/**
 * All async operations should:
 * - Use try/catch
 * - Show user-friendly error messages
 * - Log errors for debugging
 */

// Good example
async function handleExport() {
  try {
    setIsExporting(true);
    const video = await videoEncoder.exportVideo(trip, settings, setProgress);
    downloadVideo(video);
    toast.success('Video exported successfully!');
  } catch (error) {
    console.error('Export failed:', error);

    if (error instanceof OutOfMemoryError) {
      toast.error('Not enough memory. Try lowering the resolution.');
    } else if (error instanceof NetworkError) {
      toast.error('Network error. Check your connection.');
    } else {
      toast.error('Export failed. Please try again.');
    }
  } finally {
    setIsExporting(false);
  }
}
```

### 4. Performance Optimization

```typescript
/**
 * Optimization techniques:
 * - Memoization for expensive calculations
 * - Virtualization for long lists
 * - Code splitting for routes
 * - Image optimization
 */

// Memoization
const sortedTrips = useMemo(() => {
  return trips.sort((a, b) => b.startDate.getTime() - a.startDate.getTime());
}, [trips]);

// Virtual scrolling for photo grid
import { FixedSizeGrid } from 'react-window';

<FixedSizeGrid
  columnCount={3}
  columnWidth={200}
  height={600}
  rowCount={Math.ceil(photos.length / 3)}
  rowHeight={200}
  width={620}
>
  {({ columnIndex, rowIndex, style }) => {
    const index = rowIndex * 3 + columnIndex;
    const photo = photos[index];
    return photo ? <PhotoCard photo={photo} style={style} /> : null;
  }}
</FixedSizeGrid>

// Code splitting
const TripEditor = lazy(() => import('./pages/TripEditor'));
const Explorer = lazy(() => import('./pages/Explorer'));

<Suspense fallback={<LoadingSpinner />}>
  <Routes>
    <Route path="/editor/:id" element={<TripEditor />} />
    <Route path="/explore/:id" element={<Explorer />} />
  </Routes>
</Suspense>
```

---

## 🐛 Common Issues & Solutions

### Issue 1: IndexedDB Quota Exceeded

**Symptoms**: "QuotaExceededError" when saving large photos

**Solution**:
```typescript
// Monitor storage usage
async function checkStorageQuota() {
  if ('storage' in navigator && 'estimate' in navigator.storage) {
    const estimate = await navigator.storage.estimate();
    const usagePercent = (estimate.usage / estimate.quota) * 100;

    if (usagePercent > 80) {
      toast.warning('Storage almost full. Consider enabling CyberEco Hub sync.');
    }
  }
}

// Compress images before saving
async function compressImage(blob: Blob, maxSize: number = 1920): Promise<Blob> {
  const img = await createImageBitmap(blob);

  let width = img.width;
  let height = img.height;

  if (width > maxSize || height > maxSize) {
    if (width > height) {
      height = (height / width) * maxSize;
      width = maxSize;
    } else {
      width = (width / height) * maxSize;
      height = maxSize;
    }
  }

  const canvas = new OffscreenCanvas(width, height);
  const ctx = canvas.getContext('2d');
  ctx.drawImage(img, 0, 0, width, height);

  return canvas.convertToBlob({ type: 'image/jpeg', quality: 0.8 });
}
```

### Issue 2: CyberEco Hub Unavailable

**Symptoms**: Sync fails, user cannot authenticate

**Solution**:
```typescript
// Health check and fallback
async function ensureStorageAvailable() {
  try {
    const isAvailable = await cyberEcoHub.healthCheck();

    if (!isAvailable) {
      // Fallback to local-only mode
      toast.warning('CyberEco Hub temporarily unavailable. Working in local-only mode.');
      return { mode: 'local', provider: null };
    }

    return { mode: 'hybrid', provider: 'cybereco' };
  } catch (error) {
    // Offer Google Drive as alternative
    const useGoogleDrive = await promptUserFallback();

    if (useGoogleDrive) {
      await googleAPI.authenticate();
      return { mode: 'hybrid', provider: 'google' };
    }

    return { mode: 'local', provider: null };
  }
}

// Queue sync requests for retry
class SyncQueue {
  private queue: SyncRequest[] = [];

  async add(request: SyncRequest) {
    this.queue.push(request);
    await this.process();
  }

  async process() {
    while (this.queue.length > 0) {
      const request = this.queue[0];

      try {
        await request.execute();
        this.queue.shift(); // Remove successful request
      } catch (error) {
        // Exponential backoff
        const delay = Math.min(1000 * Math.pow(2, request.retries), 60000);
        await sleep(delay);
        request.retries++;

        if (request.retries > 5) {
          toast.error('Sync failed after multiple retries. Please check your connection.');
          this.queue.shift(); // Give up after 5 retries
        }
      }
    }
  }
}
```

### Issue 3: WebCodecs Not Available

**Symptoms**: Video export fails in Firefox/Safari

**Solution**:
```typescript
// Detect and fallback
async function exportVideo(trip: Trip, settings: VideoExportSettings) {
  if ('VideoEncoder' in window) {
    console.log('Using WebCodecs (fast)');
    return exportWithWebCodecs(trip, settings);
  } else {
    console.log('Using ffmpeg.wasm (slower but compatible)');
    toast.info('Using compatibility mode. Export may take longer.');
    return exportWithFFmpeg(trip, settings);
  }
}
```

### Issue 4: Map Not Rendering

**Symptoms**: Blank map or "Error loading tiles"

**Solutions**:
```typescript
// 1. Check internet connection
if (!navigator.onLine) {
  toast.error('No internet connection. Some features may not work.');
}

// 2. Use cached tiles
map.on('styleimagemissing', (e) => {
  // Fallback to default icon
  map.addImage(e.id, defaultMarkerImage);
});

// 3. Verify MapLibre token/style
const map = new maplibregl.Map({
  container: 'map',
  style: 'https://api.maptiler.com/maps/streets/style.json?key=YOUR_KEY',
  center: [0, 0],
  zoom: 2,
});
```

### Issue 5: Cross-App Data Sharing Conflicts

**Symptoms**: Data inconsistencies between TravelHub and other CyberEco apps

**Solution**:
```typescript
// Implement conflict resolution
async function resolveDataConflict(local: any, remote: any, field: string) {
  // Strategy depends on data type
  if (field === 'metadata') {
    // Server wins for metadata
    return remote;
  } else if (field === 'content') {
    // Merge changes for content
    return mergeContent(local, remote);
  } else if (field === 'expenses') {
    // Combine unique entries
    return [...new Set([...local, ...remote])];
  }

  // Default: Last write wins
  return remote.updatedAt > local.updatedAt ? remote : local;
}

// Version control for shared data
interface SharedData {
  id: string;
  version: number;
  data: any;
  lastModifiedBy: string;
  lastModifiedAt: Date;
}

async function updateSharedData(update: Partial<SharedData>) {
  const current = await cyberEcoHub.getSharedData(update.id);

  if (update.version !== current.version) {
    // Version conflict - resolve before saving
    const resolved = await resolveDataConflict(update, current, 'data');
    update.data = resolved;
    update.version = current.version + 1;
  }

  await cyberEcoHub.updateSharedData(update);
}
```

---

## 📚 Resources & References

### Documentation
- **React**: https://react.dev
- **TypeScript**: https://www.typescriptlang.org/docs/
- **Vite**: https://vitejs.dev/guide/
- **Tailwind CSS**: https://tailwindcss.com/docs
- **MapLibre GL JS**: https://maplibre.org/maplibre-gl-js/docs/
- **WebCodecs API**: https://developer.mozilla.org/en-US/docs/Web/API/WebCodecs_API
- **IndexedDB**: https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API

### APIs
- **CyberEco Hub**: https://cybere.co/documentation/
- **Google Drive API**: https://developers.google.com/drive/api/guides/about-sdk
- **Google Photos API**: https://developers.google.com/photos/library/guides/overview
- **Nominatim API**: https://nominatim.org/release-docs/latest/api/Overview/
- **OSRM API**: http://project-osrm.org/docs/v5.24.0/api/

### Tools
- **ffmpeg.wasm**: https://github.com/ffmpegwasm/ffmpeg.wasm
- **Turf.js**: https://turfjs.org/
- **exifreader**: https://github.com/mattiasw/ExifReader
- **Zustand**: https://github.com/pmndrs/zustand

### Inspiration
- **mult.dev**: https://mult.dev (competitor to study)
- **Polarsteps**: https://www.polarsteps.com/ (travel app)
- **Wanderlog**: https://wanderlog.com/ (trip planning)

---

## 🎯 Development Priorities

### Phase 1 - MVP (Current Focus)

**Week 1-2: Core Infrastructure**
- [ ] Setup Vite + React + TypeScript project
- [ ] Configure Tailwind CSS
- [ ] Setup IndexedDB wrapper
- [ ] Implement basic routing
- [ ] Create design system (colors, typography, components)
- [ ] Integrate CyberEco Hub SDK

**Week 3-4: CyberEco Hub Integration**
- [ ] Implement OAuth 2.0/OpenID Connect authentication
- [ ] Build user profile management
- [ ] Setup hybrid storage (IndexedDB + CyberEco Hub)
- [ ] Implement sync strategy with conflict resolution
- [ ] Add sync status indicators
- [ ] Test fallback to local-only mode

**Week 5-6: Trip Management**
- [ ] Create Trip data model
- [ ] Build trip creation UI
- [ ] Implement map integration (MapLibre)
- [ ] Add waypoint management (add, edit, delete, reorder)
- [ ] Build trip gallery view

**Week 7-8: Video Export**
- [ ] Research WebCodecs API
- [ ] Implement frame generation from map
- [ ] Create video encoder worker
- [ ] Build export settings UI
- [ ] Test video quality and performance

**Testing & Polish**
- [ ] Write unit tests for core services
- [ ] E2E tests for critical flows
- [ ] Performance optimization
- [ ] Bug fixes and refinements

### Phase 2 - Enhanced Features

**Priority Features**:
1. Cross-app data sharing (JustSplit integration)
2. Interactive Explorer view
3. Trip planning features (itinerary, budget)
4. Advanced video customization
5. Public sharing system
6. Google Drive/Photos integration (fallback)

### Phase 3 - Community & Scale

**Priority Features**:
1. Collaborative editing
2. Template marketplace
3. Mobile app (React Native)
4. AI-powered features
5. Public trip gallery
6. Advanced CyberEco ecosystem features

---

## 💬 Communication Guidelines

### When to Ask for Help

Claude (AI assistant) is here to help! Ask when:

- ✅ You're stuck on a technical problem for >30 minutes
- ✅ You need code review or suggestions
- ✅ You want to explore alternative approaches
- ✅ You need help with TypeScript types
- ✅ You're unsure about best practices
- ✅ You want to discuss architecture decisions

### How to Ask

**Good question format**:
```
I'm working on [feature/component].
I'm trying to [goal].
I've tried [attempted solutions].
I'm getting [error/unexpected behavior].
Here's the relevant code: [code snippet]

Questions:
1. What's causing this issue?
2. Is there a better approach?
3. Any performance concerns?
```

**Example**:
```
I'm working on the CyberEco Hub sync feature.
I'm trying to implement conflict resolution for trip data.
I've set up basic version comparison.
I'm getting inconsistent merge results when both local and remote have changes.

Code:
[paste relevant code]

Questions:
1. What's the best strategy for merging conflicting trip data?
2. Should I use operational transformation or CRDT?
3. How do I handle photo conflicts (different photos at same waypoint)?
```

### Code Review Checklist

Before requesting review:
- [ ] Code follows project style guide
- [ ] All tests pass
- [ ] No console.errors or warnings
- [ ] TypeScript has no errors
- [ ] Performance tested (no lag, memory leaks)
- [ ] Works in Chrome, Firefox, Safari
- [ ] Mobile responsive (if applicable)
- [ ] Accessibility checked (keyboard nav, screen readers)
- [ ] Comments added for complex logic
- [ ] No hardcoded values (use constants)
- [ ] CyberEco Hub integration tested
- [ ] Fallback modes work correctly

---

## 🎓 Learning Path

If you're new to any of these technologies, here's the recommended learning order:

### Week 1: Fundamentals
1. **React Basics** (if rusty)
   - Components, Props, State
   - Hooks (useState, useEffect, useMemo, useCallback)
   - Context API

2. **TypeScript Essentials**
   - Basic types (string, number, boolean, array)
   - Interfaces and Types
   - Generics basics
   - Union types

### Week 2: TravelHub-Specific
1. **MapLibre GL JS**
   - Initialize map
   - Add markers and routes
   - Handle user interactions
   - Capture screenshots

2. **IndexedDB**
   - Create database
   - Add/retrieve/update/delete records
   - Handle versioning and migrations

3. **CyberEco Hub Integration**
   - OAuth 2.0/OpenID Connect flow
   - API authentication and authorization
   - Data encryption and security
   - Cross-app data sharing

4. **WebCodecs API** (or ffmpeg.wasm)
   - Encode simple video
   - Configure encoder settings
   - Handle frames and chunks

### Week 3: Advanced Topics
1. **Web Workers**
   - Offload heavy computations
   - Message passing
   - Handle errors

2. **PWA**
   - Service Worker basics
   - Offline functionality
   - Cache strategies

3. **Performance Optimization**
   - React.memo and useMemo
   - Code splitting
   - Image optimization

---

## 🚨 Important Reminders

### Security
- ⚠️ **Never commit secrets** (API keys, tokens) to Git
- ⚠️ Use environment variables for sensitive data
- ⚠️ Sanitize all user inputs before rendering
- ⚠️ Validate file uploads (type, size, content)
- ⚠️ Implement end-to-end encryption for CyberEco Hub data
- ⚠️ Use secure token storage (encrypted IndexedDB)
- ⚠️ Implement CSRF protection for API calls

### Privacy
- ⚠️ Data must stay local by default
- ⚠️ CyberEco Hub sync requires explicit user consent
- ⚠️ No analytics tracking without user permission
- ⚠️ Respect GDPR/CCPA regulations
- ⚠️ Zero-knowledge architecture for cloud storage
- ⚠️ Clear user communication about data handling

### Performance
- ⚠️ Monitor memory usage (especially during video export)
- ⚠️ Compress images before storing
- ⚠️ Use Web Workers for CPU-intensive tasks
- ⚠️ Implement virtual scrolling for large lists
- ⚠️ Optimize sync frequency to avoid excessive API calls

### User Experience
- ⚠️ Always show loading states
- ⚠️ Provide clear error messages
- ⚠️ Auto-save work frequently
- ⚠️ Support keyboard navigation
- ⚠️ Test on slow connections (3G simulation)
- ⚠️ Graceful fallback when CyberEco Hub unavailable
- ⚠️ Clear sync status indicators

---

## 📞 Getting Help

### GitHub Issues
- **Bug Report**: Use "bug" template
- **Feature Request**: Use "feature" template
- **Question**: Use "question" label

### Discord Community
- **#general**: General discussion
- **#dev-help**: Technical questions
- **#feature-requests**: Suggest new features
- **#showcase**: Share your trips!
- **#cybereco-integration**: CyberEco Hub specific questions

### Documentation
- Check `/docs` folder first
- Search existing issues before creating new ones
- Update docs when you fix issues

---

## ✅ Pre-Development Checklist

Before starting a new feature:
- [ ] Read relevant SRS sections
- [ ] Review this CLAUDE.md file
- [ ] Check existing similar implementations
- [ ] Create GitHub issue for feature
- [ ] Design data models (TypeScript interfaces)
- [ ] Sketch UI mockups (if applicable)
- [ ] Plan testing strategy
- [ ] Estimate completion time
- [ ] Consider CyberEco Hub integration implications
- [ ] Plan fallback strategy if cloud services unavailable

---

## 🎉 Success Metrics

We know we're building the right thing when:

- ✅ Users can create a trip and export video in <10 minutes
- ✅ Video quality matches or exceeds mult.dev
- ✅ App loads in <2 seconds on 3G
- ✅ Zero crashes in production
- ✅ 80%+ Lighthouse scores
- ✅ Seamless CyberEco Hub integration
- ✅ Works fully offline after initial load
- ✅ Positive user feedback
- ✅ Growing GitHub stars
- ✅ Active community contributions
- ✅ Strong ecosystem integration with other CyberEco apps

---

## 📄 Document Maintenance

This CLAUDE.md file should be updated when:
- New features are added to roadmap
- Architecture decisions change
- APIs or libraries are updated
- Best practices evolve
- Common issues are discovered
- CyberEco Hub integration changes

**Last Updated**: October 2025
**Version**: 2.0
**Maintainer**: Development Team

---

**Remember**: The goal is to build an amazing travel visualization tool that's free, private, and better than mult.dev, while championing digital sovereignty through deep CyberEco Hub integration. Focus on user experience, performance, reliability, and privacy. Happy coding!
