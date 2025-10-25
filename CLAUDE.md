# CLAUDE.md - TravelHub Development Guide
## Guía Completa para Desarrollo Asistido por IA

---

## 📋 Document Purpose

Este documento proporciona a Claude (y otros AI assistants) el contexto completo, decisiones de arquitectura, patrones de código, y guidelines necesarios para asistir efectivamente en el desarrollo de TravelHub. Está diseñado para ser leído al inicio de cada sesión de desarrollo para mantener consistencia y alineación con la visión del proyecto.

---

## 🎯 Project Overview

**Nombre**: TravelHub  
**Descripción**: Plataforma web open source para gestionar el ciclo completo de viajes - planeación, documentación, visualización y sharing  
**Goal**: Crear alternativa gratuita y superior a mult.dev con features adicionales de repositorio personal y planeación de viajes  
**Target Users**: Viajeros, content creators, bloggers de viaje, outdoor enthusiasts  
**License**: MIT  
**Repo**: (TBD - crear en GitHub)  

---

## 🏗️ Architecture Overview

### High-Level Architecture

TravelHub es una **Progressive Web App (PWA)** completamente client-side que opera con costo $0:

```
User Browser
└── TravelHub PWA (React + TypeScript)
    ├── UI Layer (React Components)
    ├── State Management (Zustand/Jotai)
    ├── Services Layer
    │   ├── Map Engine (MapLibre GL JS)
    │   ├── Storage Manager (IndexedDB + OPFS)
    │   ├── Video Encoder (Web Worker + WebCodecs)
    │   ├── Google API Client (Drive + Photos)
    │   └── Route Calculator (OSRM API)
    ├── Browser APIs
    │   ├── IndexedDB (50GB+ storage)
    │   ├── OPFS (temp files)
    │   ├── WebCodecs (video encoding)
    │   ├── Service Worker (offline + cache)
    │   └── Web Workers (background tasks)
    └── External Services
        ├── OpenStreetMap (map tiles)
        ├── Nominatim (geocoding)
        ├── OSRM (routing)
        ├── Google Drive (optional sync)
        └── Google Photos (optional integration)
```

### Key Architectural Decisions

#### 1. **Client-Side First**
**Decision**: Todo processing ocurre en el navegador del usuario  
**Rationale**: 
- Zero operational costs
- Infinite scalability via CDN
- Privacy-first (no data leaves user's device)
- No backend to maintain

**Implications**:
- Usar Web Workers para evitar bloquear UI
- Implementar progressive enhancement (funciona sin JS para contenido básico)
- Memory management crítico (monitor usage, cleanup aggressive)

#### 2. **Static Hosting**
**Decision**: Deploy como archivos estáticos en Cloudflare Pages  
**Rationale**:
- Free hosting con bandwidth ilimitado
- Edge network global (baja latencia worldwide)
- Auto-deploy desde GitHub
- Zero server management

**Implications**:
- No server-side rendering (usar pre-rendering para SEO)
- No backend API routes (todo via external APIs)
- Environment variables via build-time injection

#### 3. **Local-First Storage**
**Decision**: IndexedDB como primary storage, Google Drive opcional  
**Rationale**:
- Funciona offline desde primera carga
- No depende de servicios externos
- User owns data completamente
- Sync opcional con Google Drive para backup/multi-device

**Implications**:
- Implementar robust IndexedDB wrapper con error handling
- Versioning de schema para migrations
- Sync strategy con conflict resolution

#### 4. **React Framework**
**Decision**: React 18+ con TypeScript  
**Rationale**:
- Ecosistema maduro y bien documentado
- Hiring pool grande (más conocido que Svelte/Vue)
- Excelente tooling (React DevTools, testing libraries)
- Community support masivo

**Implications**:
- Usar function components + hooks exclusively
- Implementar code splitting por routes
- Memoization strategies para performance

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

| API | Purpose | Free Tier | Fallback |
|-----|---------|-----------|----------|
| **OpenStreetMap** | Map tiles | Unlimited | None needed |
| **Nominatim** | Geocoding | 1 req/sec | Local cache |
| **OSRM** | Routing | Unlimited | Straight lines |
| **Google Drive** | Cloud storage | 15GB free | Local only |
| **Google Photos** | Photo management | Unlimited | Local upload |
| **Open-Meteo** | Weather data | Unlimited | Omit weather |
| **Cohere** | LLM (route parsing) | 1M tokens/mo | Manual input |

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
│   │   ├── useGoogleDrive.ts
│   │   └── useVideoExport.ts
│   ├── services/              # Business logic services
│   │   ├── storage/           # IndexedDB wrapper
│   │   ├── video/             # Video encoding logic
│   │   ├── maps/              # Map utilities
│   │   ├── google/            # Google API clients
│   │   └── routing/           # Route calculation
│   ├── workers/               # Web Workers
│   │   ├── video-encoder.worker.ts
│   │   └── image-processor.worker.ts
│   ├── store/                 # Zustand stores
│   │   ├── tripStore.ts
│   │   ├── uiStore.ts
│   │   └── settingsStore.ts
│   ├── types/                 # TypeScript types
│   │   ├── trip.ts
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

### Trip

```typescript
interface Trip {
  id: string;                    // UUID
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
 * Storage Service - Wrapper around IndexedDB
 * 
 * Stores: trips, photos, settings, cache
 * Quota: 50GB+ (monitor usage, warn at 80%)
 */

class StorageService {
  private db: IDBDatabase;

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
  
  // Auto-backup to Google Drive
  async syncToGoogleDrive(tripId: string): Promise<void>
}
```

**Key Features**:
- Auto-versioning con migrations
- Automatic backup every 5 minutes si hay cambios
- Compression de fotos antes de guardar
- Cleanup automático de archivos temporales

### 2. Video Encoder Service

**File**: `src/services/video/video-encoder.service.ts`

```typescript
/**
 * Video Encoder Service
 * 
 * Strategy:
 * 1. Try WebCodecs API (Chrome/Edge) - fastest
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

### 3. Map Service

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

### 4. Google API Service

**File**: `src/services/google/google-api.service.ts`

```typescript
/**
 * Google API Service
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

**OAuth Flow**:

```typescript
// 1. User clicks "Connect Google Drive"
// 2. Generate PKCE code challenge
const codeVerifier = generateRandomString(128);
const codeChallenge = await sha256(codeVerifier);

// 3. Redirect to Google OAuth
const authUrl = `https://accounts.google.com/o/oauth2/v2/auth?
  client_id=${CLIENT_ID}&
  redirect_uri=${REDIRECT_URI}&
  response_type=code&
  scope=https://www.googleapis.com/auth/drive.file&
  code_challenge=${codeChallenge}&
  code_challenge_method=S256`;

// 4. Handle callback
const urlParams = new URLSearchParams(window.location.search);
const code = urlParams.get('code');

// 5. Exchange code for tokens
const response = await fetch('https://oauth2.googleapis.com/token', {
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

---

## 🎯 Key Features Implementation

### Feature 1: Trip Creation

**User Flow**:
1. Click "New Trip"
2. Enter title, dates
3. Add waypoints (click map, search, or import GPX)
4. Customize transport modes between waypoints
5. Add photos (upload or import from Google Photos)
6. Save trip

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

### Feature 2: Video Export

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

### Feature 3: Google Drive Sync

**Strategy**:
- Save trip as JSON file in "TravelHub" folder
- Photos stored as references (don't duplicate from Google Photos)
- Sync every 5 minutes if changes detected
- Conflict resolution: last-write-wins with backup of both versions

**Implementation**:

```typescript
async function syncTripToGoogleDrive(trip: Trip): Promise<void> {
  // 1. Serialize trip to JSON
  const tripData = JSON.stringify(trip, null, 2);
  const blob = new Blob([tripData], { type: 'application/json' });
  
  // 2. Check if file exists
  if (trip.googleDriveFileId) {
    // Update existing file
    await googleAPI.updateFile(trip.googleDriveFileId, blob);
  } else {
    // Create new file
    const fileId = await googleAPI.uploadFile(blob, `${trip.title}.json`, travelhubFolderId);
    trip.googleDriveFileId = fileId;
  }
  
  // 3. Update local record
  trip.syncedToGoogleDrive = true;
  trip.updatedAt = new Date();
  await storageService.saveTrip(trip);
}

// Auto-sync hook
function useAutoSync(trip: Trip) {
  useEffect(() => {
    const timer = setInterval(async () => {
      if (trip.updatedAt > trip.lastSyncedAt) {
        await syncTripToGoogleDrive(trip);
      }
    }, 5 * 60 * 1000); // 5 minutes
    
    return () => clearInterval(timer);
  }, [trip]);
}
```

### Feature 4: Interactive Explorer

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
          ← Prev
        </button>
        <button 
          onClick={handleNext}
          className="absolute right-4 top-1/2 pointer-events-auto"
        >
          Next →
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
      toast.warning('Storage almost full. Consider enabling Google Drive sync.');
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

### Issue 2: WebCodecs Not Available

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
    return exportWithFFmpeg(trip, settings);
  }
}
```

### Issue 3: Map Not Rendering

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

### Issue 4: Google API Rate Limit

**Symptoms**: "Too many requests" error from Google APIs

**Solution**:
```typescript
// Implement exponential backoff
async function fetchWithRetry(url: string, options: RequestInit, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await fetch(url, options);
      
      if (response.status === 429) {
        // Rate limited, wait and retry
        const delay = Math.pow(2, i) * 1000; // 1s, 2s, 4s
        console.log(`Rate limited. Retrying in ${delay}ms...`);
        await sleep(delay);
        continue;
      }
      
      return response;
    } catch (error) {
      if (i === maxRetries - 1) throw error;
    }
  }
}

// Cache API responses
const cache = new Map();

async function cachedFetch(url: string) {
  if (cache.has(url)) {
    return cache.get(url);
  }
  
  const response = await fetch(url);
  const data = await response.json();
  cache.set(url, data);
  
  return data;
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

**Week 3-4: Trip Management**
- [ ] Create Trip data model
- [ ] Build trip creation UI
- [ ] Implement map integration (MapLibre)
- [ ] Add waypoint management (add, edit, delete, reorder)
- [ ] Build trip gallery view

**Week 5-6: Video Export**
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
1. Google Drive/Photos integration
2. Interactive Explorer view
3. Trip planning features (itinerary, budget)
4. Advanced video customization
5. Sharing system

### Phase 3 - Community & Scale

**Priority Features**:
1. Collaborative editing
2. Template marketplace
3. Mobile app (React Native)
4. AI-powered features
5. Public trip gallery

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
I'm working on the video export feature.
I'm trying to encode frames using WebCodecs API.
I've set up a VideoEncoder with VP9 codec.
I'm getting "EncodingError: Frame encode failed" after 100 frames.

Code:
[paste relevant code]

Questions:
1. Why is encoding failing after 100 frames?
2. Should I flush the encoder periodically?
3. Are there memory management best practices I'm missing?
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

3. **WebCodecs API** (or ffmpeg.wasm)
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

### Privacy
- ⚠️ Data must stay local by default
- ⚠️ Google Drive sync requires explicit consent
- ⚠️ No analytics tracking without user permission
- ⚠️ Respect GDPR/CCPA regulations

### Performance
- ⚠️ Monitor memory usage (especially during video export)
- ⚠️ Compress images before storing
- ⚠️ Use Web Workers for CPU-intensive tasks
- ⚠️ Implement virtual scrolling for large lists

### User Experience
- ⚠️ Always show loading states
- ⚠️ Provide clear error messages
- ⚠️ Auto-save work frequently
- ⚠️ Support keyboard navigation
- ⚠️ Test on slow connections (3G simulation)

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

---

## 🎉 Success Metrics

We know we're building the right thing when:

- ✅ Users can create a trip and export video in <10 minutes
- ✅ Video quality matches or exceeds mult.dev
- ✅ App loads in <2 seconds on 3G
- ✅ Zero crashes in production
- ✅ 80%+ Lighthouse scores
- ✅ Positive user feedback
- ✅ Growing GitHub stars
- ✅ Active community contributions

---

## 📄 Document Maintenance

This CLAUDE.md file should be updated when:
- New features are added to roadmap
- Architecture decisions change
- APIs or libraries are updated
- Best practices evolve
- Common issues are discovered

**Last Updated**: October 2025  
**Version**: 1.0  
**Maintainer**: Development Team

---

**Remember**: The goal is to build an amazing travel visualization tool that's free, private, and better than mult.dev. Focus on user experience, performance, and reliability. Happy coding! 🚀