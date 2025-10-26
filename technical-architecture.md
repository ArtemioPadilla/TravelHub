# TravelHub - Technical Architecture
## React + Vite with Hybrid Storage and Zero Operational Costs

**Version:** 2.0
**Date:** October 2025

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Technology Stack](#technology-stack)
3. [Application Architecture](#application-architecture)
4. [Hybrid Storage System](#hybrid-storage-system)
5. [Client-Side Video Processing](#client-side-video-processing)
6. [CyberEco Hub Integration](#cybereco-hub-integration)
7. [Google Drive/Photos Integration](#google-drivephotos-integration-fallback)
8. [Deployment Strategy](#deployment-strategy)
9. [Performance Considerations](#performance-considerations)
10. [Implementation Roadmap](#implementation-roadmap)

---

## Executive Summary

TravelHub is a **modern React Progressive Web Application** (PWA) built with Vite that implements an innovative hybrid architecture combining client-side processing with optional CyberEco Hub cloud integration.

### Core Value Proposition

**For Free Users:**
- 100% complete travel lifecycle functionality
- Storage in local browser (IndexedDB) or optional CyberEco Hub account
- Complete privacy with client-side processing
- Digital sovereignty through CyberEco Hub integration
- $0 operational costs for us, $0 for users

**For Premium Users ($5-20/month):**
- Managed cloud storage on our infrastructure
- Real-time collaborative features
- Custom branding and domains
- Priority support
- Advanced analytics

### Architectural Advantages

✅ **Client-Side First:**
- Infinite scalability (compute on user devices)
- Zero server costs for free tier
- Privacy by design (data never leaves device without consent)
- Works completely offline after initial load

✅ **CyberEco Hub Integration:**
- Single sign-on across all CyberEco applications
- Digital sovereignty (users own their data completely)
- Cross-app data sharing (e.g., expenses with JustSplit)
- Unified digital identity and profile
- Optional cloud backup with end-to-end encryption

✅ **Vite + React Over Next.js:**
- Simpler static deployment (no server needed)
- Faster development with instant HMR
- Smaller bundle sizes with better tree-shaking
- Perfect for client-side first architecture
- No hydration complexity

✅ **Zustand Over Redux:**
- 10x less boilerplate code
- Better TypeScript inference
- Simpler mental model
- Smaller bundle size (~1KB vs ~10KB)
- Perfect for our use case

---

## Technology Stack

### Frontend Core

```typescript
// package.json dependencies
{
  "name": "travelhub",
  "version": "2.0.0",
  "type": "module",

  "dependencies": {
    // Framework
    "react": "^18.3.0",
    "react-dom": "^18.3.0",

    // Build Tool
    "vite": "^5.0.0",
    "@vitejs/plugin-react": "^4.2.0",

    // Routing
    "react-router-dom": "^6.20.0",

    // State Management
    "zustand": "^4.5.0",              // Lightweight state
    "immer": "^10.0.0",               // Immutable updates

    // Styling
    "tailwindcss": "^3.4.0",
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0",
    "@headlessui/react": "^2.0.0",    // Accessible components
    "framer-motion": "^11.0.0",       // Animations
    "lucide-react": "^0.344.0",       // Icons
    "clsx": "^2.1.0",                 // Conditional classes

    // Maps
    "maplibre-gl": "^4.0.0",          // Map rendering
    "react-map-gl": "^7.1.0",         // React wrapper
    "@turf/turf": "^6.5.0",           // Geospatial calculations

    // Storage
    "idb": "^8.0.0",                  // IndexedDB wrapper
    "localforage": "^1.10.0",         // Storage abstraction

    // File Processing
    "togeojson": "^0.16.2",           // GPX/KML parsing
    "exifreader": "^4.0.0",           // EXIF parsing

    // Date/Time
    "date-fns": "^3.3.0",             // Date manipulation

    // Forms & Validation
    "react-hook-form": "^7.50.0",
    "zod": "^3.22.0",

    // Rich Text Editor
    "@tiptap/react": "^2.2.0",
    "@tiptap/starter-kit": "^2.2.0",

    // Video Processing
    "ffmpeg-wasm": "^0.12.0",         // Fallback video encoder

    // CyberEco Hub Integration
    "@cybereco/hub-client": "^1.0.0", // CyberEco Hub SDK (hypothetical)

    // Google APIs (Fallback)
    "@react-oauth/google": "^0.12.0",
    "gapi-script": "^1.2.0",

    // Utilities
    "nanoid": "^5.0.0",               // ID generation
    "hash-wasm": "^4.11.0",           // Hashing
    "sharp-wasm": "^0.1.0"            // Image processing
  },

  "devDependencies": {
    "typescript": "^5.3.0",
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@types/maplibre-gl": "^4.0.0",
    "eslint": "^8.56.0",
    "eslint-plugin-react": "^7.33.0",
    "prettier": "^3.2.0",
    "vitest": "^1.2.0",
    "@testing-library/react": "^14.2.0",
    "@playwright/test": "^1.41.0",
    "vite-plugin-pwa": "^0.19.0"
  }
}
```

### Architecture Comparison

| Aspect | TravelHub Choice | Alternative | Rationale |
|--------|-----------------|-------------|-----------|
| **Framework** | Vite + React | Next.js | Static deployment, simpler, faster dev |
| **State** | Zustand | Redux Toolkit | Less boilerplate, smaller bundle |
| **Styling** | Tailwind CSS | CSS-in-JS | Faster, smaller bundle, better DX |
| **Maps** | MapLibre GL | Mapbox GL | Open source, zero cost |
| **Storage** | CyberEco Hub + IndexedDB | Supabase/Firebase | Digital sovereignty, privacy |
| **Video** | WebCodecs API | Canvas + ffmpeg.js | Hardware acceleration |
| **Hosting** | Cloudflare Pages | Vercel | Free tier, edge network |

---

## Application Architecture

### Directory Structure

```
/travelhub
├── /public
│   ├── manifest.json           # PWA manifest
│   ├── sw.js                   # Service Worker
│   └── /icons                  # App icons
│
├── /src
│   ├── /assets                 # Static assets
│   │   ├── /images
│   │   └── /fonts
│   │
│   ├── /components             # React components
│   │   ├── /common             # Shared components
│   │   │   ├── Button.tsx
│   │   │   ├── Modal.tsx
│   │   │   ├── Input.tsx
│   │   │   └── Card.tsx
│   │   ├── /map                # Map components
│   │   │   ├── MapCanvas.tsx
│   │   │   ├── Marker.tsx
│   │   │   └── RouteLayer.tsx
│   │   ├── /trip               # Trip components
│   │   │   ├── TripCard.tsx
│   │   │   ├── TripEditor.tsx
│   │   │   └── WaypointList.tsx
│   │   ├── /video              # Video export
│   │   │   ├── VideoExporter.tsx
│   │   │   ├── SettingsPanel.tsx
│   │   │   └── PreviewPlayer.tsx
│   │   └── /layout             # Layout components
│   │       ├── Header.tsx
│   │       ├── Sidebar.tsx
│   │       └── Footer.tsx
│   │
│   ├── /hooks                  # Custom React hooks
│   │   ├── useTrips.ts
│   │   ├── useAuth.ts
│   │   ├── useCyberEcoHub.ts
│   │   ├── useStorage.ts
│   │   ├── useGoogleDrive.ts
│   │   └── useVideoExport.ts
│   │
│   ├── /services               # Business logic
│   │   ├── /storage
│   │   │   ├── storage.service.ts
│   │   │   ├── indexeddb.ts
│   │   │   └── opfs.ts
│   │   ├── /cybereco
│   │   │   ├── cybereco-hub.service.ts
│   │   │   ├── auth.ts
│   │   │   ├── storage.ts
│   │   │   └── sharing.ts
│   │   ├── /google
│   │   │   ├── drive.service.ts
│   │   │   └── photos.service.ts
│   │   ├── /video
│   │   │   ├── video-encoder.service.ts
│   │   │   ├── frame-generator.ts
│   │   │   └── webcodecs-encoder.ts
│   │   ├── /maps
│   │   │   ├── map.service.ts
│   │   │   └── routing.ts
│   │   └── /api
│   │       ├── osm.ts
│   │       ├── nominatim.ts
│   │       └── osrm.ts
│   │
│   ├── /workers                # Web Workers
│   │   ├── video-encoder.worker.ts
│   │   └── image-processor.worker.ts
│   │
│   ├── /store                  # Zustand stores
│   │   ├── tripStore.ts
│   │   ├── userStore.ts
│   │   ├── uiStore.ts
│   │   └── settingsStore.ts
│   │
│   ├── /types                  # TypeScript types
│   │   ├── trip.ts
│   │   ├── user.ts
│   │   ├── waypoint.ts
│   │   ├── photo.ts
│   │   └── video.ts
│   │
│   ├── /utils                  # Utility functions
│   │   ├── gpx-parser.ts
│   │   ├── geo-utils.ts
│   │   ├── date-utils.ts
│   │   └── crypto.ts
│   │
│   ├── /pages                  # Route pages
│   │   ├── Home.tsx
│   │   ├── Dashboard.tsx
│   │   ├── TripEditor.tsx
│   │   ├── Explorer.tsx
│   │   ├── Gallery.tsx
│   │   └── Settings.tsx
│   │
│   ├── App.tsx                 # Root component
│   ├── main.tsx                # Entry point
│   ├── router.tsx              # Route configuration
│   └── index.css               # Global styles
│
├── /tests                      # Tests
│   ├── /unit
│   ├── /integration
│   └── /e2e
│
├── index.html                  # HTML entry point
├── vite.config.ts              # Vite configuration
├── tsconfig.json               # TypeScript config
├── tailwind.config.js          # Tailwind config
├── postcss.config.js           # PostCSS config
├── .env.example                # Environment variables
└── package.json
```

### Component Architecture Patterns

```typescript
// Example: Trip Store with Zustand
import { create } from 'zustand';
import { immer } from 'zustand/middleware/immer';
import { Trip } from '@/types/trip';

interface TripStore {
  trips: Trip[];
  currentTrip: Trip | null;
  loading: boolean;
  error: string | null;

  // Actions
  loadTrips: () => Promise<void>;
  addTrip: (trip: Trip) => void;
  updateTrip: (id: string, updates: Partial<Trip>) => void;
  deleteTrip: (id: string) => void;
  setCurrentTrip: (trip: Trip | null) => void;
}

export const useTripStore = create<TripStore>()(
  immer((set, get) => ({
    trips: [],
    currentTrip: null,
    loading: false,
    error: null,

    loadTrips: async () => {
      set({ loading: true, error: null });
      try {
        const trips = await storageService.getTrips();
        set({ trips, loading: false });
      } catch (error) {
        set({ error: error.message, loading: false });
      }
    },

    addTrip: (trip) => set((state) => {
      state.trips.push(trip);
    }),

    updateTrip: (id, updates) => set((state) => {
      const index = state.trips.findIndex(t => t.id === id);
      if (index !== -1) {
        Object.assign(state.trips[index], updates);
      }
    }),

    deleteTrip: (id) => set((state) => {
      state.trips = state.trips.filter(t => t.id !== id);
    }),

    setCurrentTrip: (trip) => set({ currentTrip: trip })
  }))
);
```

---

## Hybrid Storage System

TravelHub implements a three-tier storage architecture that prioritizes user privacy, digital sovereignty, and resilience.

### Storage Architecture Diagram

```
┌──────────────────────────────────────────────────────────┐
│                    User's Browser                        │
│  ┌────────────────────────────────────────────────────┐ │
│  │         TravelHub Application                      │ │
│  │                                                     │ │
│  │  ┌──────────────────────────────────────────────┐ │ │
│  │  │       Storage Manager (Hybrid)               │ │ │
│  │  │                                               │ │ │
│  │  │  ┌─────────┐  ┌─────────┐  ┌─────────────┐  │ │ │
│  │  │  │ Tier 1  │  │ Tier 2  │  │   Tier 3    │  │ │ │
│  │  │  │ Local   │→ │CyberEco │→ │Google Drive │  │ │ │
│  │  │  │IndexedDB│  │  Hub    │  │  (Fallback) │  │ │ │
│  │  │  └─────────┘  └─────────┘  └─────────────┘  │ │ │
│  │  │                                               │ │ │
│  │  │  Save Flow:                                   │ │ │
│  │  │  1. Write to IndexedDB (immediate)            │ │ │
│  │  │  2. Sync to CyberEco Hub (every 5 min)       │ │ │
│  │  │  3. Fallback to Google Drive (if opted in)   │ │ │
│  │  └───────────────────────────────────────────────┘ │ │
│  └─────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘
```

### Tier 1: Local Storage (IndexedDB + OPFS)

**Purpose:** Immediate, offline-first storage

**Technology:**
- IndexedDB for structured data (trips, photos metadata, settings)
- OPFS (Origin Private File System) for large files (video exports, cached images)

**Capacity:**
- Typical quota: 50GB+ (varies by browser)
- OPFS: Additional storage for temporary files

**Implementation:**

```typescript
// Storage Service with IndexedDB
import { openDB, DBSchema, IDBPDatabase } from 'idb';

interface TravelHubDB extends DBSchema {
  trips: {
    key: string;
    value: Trip;
    indexes: { 'by-date': Date; 'by-user': string };
  };
  photos: {
    key: string;
    value: Photo;
    indexes: { 'by-trip': string };
  };
  settings: {
    key: string;
    value: any;
  };
  cache: {
    key: string;
    value: { data: any; timestamp: number };
  };
}

class StorageService {
  private db: IDBPDatabase<TravelHubDB>;

  async init() {
    this.db = await openDB<TravelHubDB>('travelhub-db', 2, {
      upgrade(db, oldVersion, newVersion) {
        // Create object stores
        if (!db.objectStoreNames.contains('trips')) {
          const tripStore = db.createObjectStore('trips', { keyPath: 'id' });
          tripStore.createIndex('by-date', 'startDate');
          tripStore.createIndex('by-user', 'userId');
        }

        if (!db.objectStoreNames.contains('photos')) {
          const photoStore = db.createObjectStore('photos', { keyPath: 'id' });
          photoStore.createIndex('by-trip', 'tripId');
        }

        if (!db.objectStoreNames.contains('settings')) {
          db.createObjectStore('settings');
        }

        if (!db.objectStoreNames.contains('cache')) {
          db.createObjectStore('cache');
        }
      }
    });
  }

  // Trip operations
  async saveTrip(trip: Trip): Promise<void> {
    await this.db.put('trips', trip);
    // Trigger sync to CyberEco Hub
    await this.syncToCyberEcoHub(trip);
  }

  async getTrip(id: string): Promise<Trip | undefined> {
    return await this.db.get('trips', id);
  }

  async getAllTrips(): Promise<Trip[]> {
    return await this.db.getAll('trips');
  }

  async deleteTrip(id: string): Promise<void> {
    await this.db.delete('trips', id);
    // Sync deletion to CyberEco Hub
    await this.syncDeletionToCyberEcoHub(id);
  }

  // Storage quota management
  async getStorageEstimate(): Promise<StorageEstimate> {
    if ('storage' in navigator && 'estimate' in navigator.storage) {
      return await navigator.storage.estimate();
    }
    return { usage: 0, quota: 0 };
  }

  async checkStorageQuota(): Promise<void> {
    const estimate = await this.getStorageEstimate();
    const usagePercent = (estimate.usage! / estimate.quota!) * 100;

    if (usagePercent > 80) {
      console.warn('Storage quota at 80%+, consider cloud sync');
      // Notify user to enable CyberEco Hub sync
    }
  }
}
```

### Tier 2: CyberEco Hub (Primary Cloud Storage)

**Purpose:** Cloud backup, multi-device sync, digital sovereignty, cross-app integration

**Features:**
- Single sign-on (SSO) across CyberEco ecosystem
- End-to-end encrypted storage
- Automatic sync every 5 minutes
- Cross-app data sharing (e.g., expenses with JustSplit)
- User owns and controls all data
- Export/delete all data at any time

**Implementation:**

```typescript
// CyberEco Hub Service
import { CyberEcoHubClient, AuthConfig } from '@cybereco/hub-client';

interface CyberEcoConfig {
  baseUrl: string;
  clientId: string;
  redirectUri: string;
  scopes: string[];
}

class CyberEcoHubService {
  private client: CyberEcoHubClient;
  private isAuthenticated = false;

  constructor(config: CyberEcoConfig) {
    this.client = new CyberEcoHubClient({
      baseUrl: config.baseUrl,
      clientId: config.clientId,
      redirectUri: config.redirectUri,
      scopes: ['profile', 'email', 'storage', 'data-sharing']
    });
  }

  // Authentication
  async authenticate(): Promise<void> {
    await this.client.authenticate();
    this.isAuthenticated = true;
  }

  async isAuth(): Promise<boolean> {
    return this.client.isAuthenticated();
  }

  async getUserProfile(): Promise<User> {
    return await this.client.getProfile();
  }

  // Trip Storage
  async uploadTrip(trip: Trip): Promise<string> {
    // Encrypt trip data
    const encrypted = await this.encryptData(trip);

    // Upload to CyberEco Hub
    const result = await this.client.storage.upload({
      path: `/trips/${trip.id}.json`,
      data: encrypted,
      encrypted: true,
      metadata: {
        title: trip.title,
        startDate: trip.startDate,
        endDate: trip.endDate
      }
    });

    return result.fileId;
  }

  async downloadTrip(tripId: string): Promise<Trip> {
    const encrypted = await this.client.storage.download(`/trips/${tripId}.json`);
    const decrypted = await this.decryptData(encrypted);
    return decrypted as Trip;
  }

  // Sync Strategy
  async syncTrip(trip: Trip): Promise<SyncResult> {
    try {
      // Check if trip exists in cloud
      const cloudTrip = await this.downloadTrip(trip.id);

      // Compare versions
      if (cloudTrip.updatedAt > trip.updatedAt) {
        // Cloud is newer - conflict resolution
        return { status: 'conflict', cloudVersion: cloudTrip, localVersion: trip };
      } else if (trip.updatedAt > cloudTrip.updatedAt) {
        // Local is newer - upload
        await this.uploadTrip(trip);
        return { status: 'uploaded' };
      } else {
        // Same version
        return { status: 'synced' };
      }
    } catch (error) {
      if (error.code === 'NOT_FOUND') {
        // Trip doesn't exist in cloud - first upload
        await this.uploadTrip(trip);
        return { status: 'uploaded' };
      }
      throw error;
    }
  }

  // Cross-App Data Sharing
  async shareExpensesWithJustSplit(tripId: string): Promise<void> {
    const trip = await storageService.getTrip(tripId);
    if (!trip) throw new Error('Trip not found');

    const expenses = this.extractExpenses(trip);

    await this.client.dataSharing.share({
      targetApp: 'justsplit',
      dataType: 'trip-expenses',
      data: expenses,
      permissions: ['read', 'write']
    });
  }

  // Digital Sovereignty
  async exportAllData(): Promise<Blob> {
    const allData = await this.client.user.exportData();
    return new Blob([JSON.stringify(allData, null, 2)], { type: 'application/json' });
  }

  async deleteAllData(): Promise<void> {
    await this.client.user.deleteAllData();
  }

  // Health Check
  async healthCheck(): Promise<boolean> {
    try {
      await this.client.health.check();
      return true;
    } catch {
      return false;
    }
  }

  // Encryption (client-side)
  private async encryptData(data: any): Promise<ArrayBuffer> {
    const jsonString = JSON.stringify(data);
    const encoder = new TextEncoder();
    const dataBuffer = encoder.encode(jsonString);

    // Get encryption key from user's CyberEco Hub profile
    const key = await this.getUserEncryptionKey();

    // Encrypt using Web Crypto API
    const iv = crypto.getRandomValues(new Uint8Array(12));
    const encrypted = await crypto.subtle.encrypt(
      { name: 'AES-GCM', iv },
      key,
      dataBuffer
    );

    // Combine IV and encrypted data
    const combined = new Uint8Array(iv.length + encrypted.byteLength);
    combined.set(iv, 0);
    combined.set(new Uint8Array(encrypted), iv.length);

    return combined.buffer;
  }

  private async decryptData(encrypted: ArrayBuffer): Promise<any> {
    const combined = new Uint8Array(encrypted);
    const iv = combined.slice(0, 12);
    const data = combined.slice(12);

    const key = await this.getUserEncryptionKey();

    const decrypted = await crypto.subtle.decrypt(
      { name: 'AES-GCM', iv },
      key,
      data
    );

    const decoder = new TextDecoder();
    const jsonString = decoder.decode(decrypted);
    return JSON.parse(jsonString);
  }
}
```

### Tier 3: Google Drive/Photos (Fallback Option)

**Purpose:** Alternative cloud storage for users who don't use CyberEco Hub

**When to Use:**
- User doesn't have CyberEco Hub account
- User prefers Google ecosystem
- Backup redundancy
- Photo library integration

**Features:**
- OAuth 2.0 with PKCE for secure authentication
- 15GB free storage
- Google Photos integration for photo imports
- Standard Google Drive file management

**Implementation:**

```typescript
// Google Drive Service (Fallback)
import { GoogleAuthProvider } from '@react-oauth/google';

class GoogleDriveService {
  private accessToken: string | null = null;

  async authenticate(): Promise<void> {
    const codeVerifier = this.generateCodeVerifier();
    const codeChallenge = await this.generateCodeChallenge(codeVerifier);

    // OAuth 2.0 with PKCE flow
    const authUrl = `https://accounts.google.com/o/oauth2/v2/auth?` +
      `client_id=${import.meta.env.VITE_GOOGLE_CLIENT_ID}&` +
      `redirect_uri=${encodeURIComponent(import.meta.env.VITE_GOOGLE_REDIRECT_URI)}&` +
      `response_type=code&` +
      `scope=https://www.googleapis.com/auth/drive.file&` +
      `code_challenge=${codeChallenge}&` +
      `code_challenge_method=S256`;

    window.location.href = authUrl;
  }

  async uploadTrip(trip: Trip): Promise<string> {
    const tripData = JSON.stringify(trip, null, 2);
    const blob = new Blob([tripData], { type: 'application/json' });

    const metadata = {
      name: `${trip.title}.json`,
      mimeType: 'application/json',
      parents: ['travelhub-folder-id']
    };

    const form = new FormData();
    form.append('metadata', new Blob([JSON.stringify(metadata)], { type: 'application/json' }));
    form.append('file', blob);

    const response = await fetch('https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.accessToken}`
      },
      body: form
    });

    const result = await response.json();
    return result.id;
  }
}
```

### Storage Selection Strategy

```typescript
// Storage Manager - Coordinates all three tiers
class StorageManager {
  private local: StorageService;
  private cybereco: CyberEcoHubService;
  private google: GoogleDriveService;

  async saveTrip(trip: Trip): Promise<void> {
    // 1. Always save to local IndexedDB first (immediate)
    await this.local.saveTrip(trip);

    // 2. Sync to CyberEco Hub if authenticated
    if (await this.cybereco.isAuth()) {
      await this.cybereco.syncTrip(trip);
    }
    // 3. Fallback to Google Drive if user opted in
    else if (await this.google.isAuth() && userSettings.googleDriveSync) {
      await this.google.uploadTrip(trip);
    }
  }

  async loadTrips(): Promise<Trip[]> {
    // Try to load from local first (fastest)
    let trips = await this.local.getAllTrips();

    // If empty and user is authenticated, try cloud
    if (trips.length === 0) {
      if (await this.cybereco.isAuth()) {
        trips = await this.cybereco.getAllTrips();
        // Cache locally
        for (const trip of trips) {
          await this.local.saveTrip(trip);
        }
      }
    }

    return trips;
  }
}
```

---

## Client-Side Video Processing

TravelHub uses cutting-edge browser APIs to encode high-quality videos entirely client-side, eliminating server costs and ensuring user privacy.

### Video Processing Architecture

```
┌─────────────────────────────────────────────────┐
│          Main Thread (React UI)                 │
│  ┌───────────────────────────────────────────┐ │
│  │  User clicks "Export Video"               │ │
│  │  ↓                                         │ │
│  │  Configure settings (resolution, fps)     │ │
│  │  ↓                                         │ │
│  │  Send to Web Worker                       │ │
│  └───────────────────────────────────────────┘ │
└─────────────────────────────────────────────────┘
                    │
                    ▼ postMessage
┌─────────────────────────────────────────────────┐
│       Web Worker (Background Thread)            │
│  ┌───────────────────────────────────────────┐ │
│  │  1. Generate frames from map              │ │
│  │     ↓                                      │ │
│  │  2. Check WebCodecs support               │ │
│  │     ├─ Yes: Use VideoEncoder (fast)       │ │
│  │     └─ No: Use ffmpeg.wasm (fallback)     │ │
│  │     ↓                                      │ │
│  │  3. Encode frames to video                │ │
│  │     ↓                                      │ │
│  │  4. Return video blob                     │ │
│  └───────────────────────────────────────────┘ │
└─────────────────────────────────────────────────┘
                    │
                    ▼ postMessage
┌─────────────────────────────────────────────────┐
│          Main Thread                            │
│  ┌───────────────────────────────────────────┐ │
│  │  Download video file                      │ │
│  └───────────────────────────────────────────┘ │
└─────────────────────────────────────────────────┘
```

### WebCodecs API (Primary, Chrome/Edge/Safari)

```typescript
// video-encoder.worker.ts
import type { VideoEncoderConfig, EncodedVideoChunk } from '@types/webcodecs';

interface VideoExportSettings {
  width: number;
  height: number;
  fps: number;
  bitrate: number;
  codec: string;
}

class VideoEncoderWorker {
  private encoder: VideoEncoder | null = null;
  private chunks: EncodedVideoChunk[] = [];

  async encodeVideo(frames: ImageBitmap[], settings: VideoExportSettings): Promise<Blob> {
    this.chunks = [];

    // Configure encoder
    const config: VideoEncoderConfig = {
      codec: 'vp09.00.10.08', // VP9 for web
      width: settings.width,
      height: settings.height,
      bitrate: settings.bitrate,
      framerate: settings.fps,
      hardwareAcceleration: 'prefer-hardware'
    };

    this.encoder = new VideoEncoder({
      output: (chunk, metadata) => {
        this.chunks.push(chunk);
        // Report progress
        self.postMessage({
          type: 'progress',
          percent: (this.chunks.length / frames.length) * 100
        });
      },
      error: (error) => {
        self.postMessage({ type: 'error', message: error.message });
      }
    });

    this.encoder.configure(config);

    // Encode frames
    for (let i = 0; i < frames.length; i++) {
      const frame = new VideoFrame(frames[i], {
        timestamp: (i * 1_000_000) / settings.fps, // microseconds
      });

      const keyFrame = i % 150 === 0; // Keyframe every 5 seconds at 30fps
      this.encoder.encode(frame, { keyFrame });
      frame.close();
    }

    // Finish encoding
    await this.encoder.flush();
    this.encoder.close();

    // Mux chunks into WebM container
    return this.muxToWebM(this.chunks, settings);
  }

  private async muxToWebM(chunks: EncodedVideoChunk[], settings: VideoExportSettings): Promise<Blob> {
    // Simple WebM muxing (in production, use a library like webm-writer)
    // This is a simplified version
    const buffers: ArrayBuffer[] = [];
    for (const chunk of chunks) {
      const buffer = new ArrayBuffer(chunk.byteLength);
      chunk.copyTo(buffer);
      buffers.push(buffer);
    }

    return new Blob(buffers, { type: 'video/webm' });
  }
}

// Worker message handler
self.onmessage = async (event) => {
  const { type, data } = event.data;

  if (type === 'encode') {
    const worker = new VideoEncoderWorker();
    try {
      const videoBlob = await worker.encodeVideo(data.frames, data.settings);
      self.postMessage({ type: 'complete', blob: videoBlob });
    } catch (error) {
      self.postMessage({ type: 'error', message: error.message });
    }
  }
};
```

### ffmpeg.wasm (Fallback, Firefox and older browsers)

```typescript
// ffmpeg-encoder.worker.ts
import { FFmpeg } from '@ffmpeg/ffmpeg';
import { fetchFile } from '@ffmpeg/util';

class FFmpegEncoder {
  private ffmpeg: FFmpeg;

  async init() {
    this.ffmpeg = new FFmpeg();
    await this.ffmpeg.load();
  }

  async encodeVideo(frames: ImageBitmap[], settings: VideoExportSettings): Promise<Blob> {
    // Convert frames to images
    for (let i = 0; i < frames.length; i++) {
      const canvas = new OffscreenCanvas(settings.width, settings.height);
      const ctx = canvas.getContext('2d')!;
      ctx.drawImage(frames[i], 0, 0);

      const blob = await canvas.convertToBlob({ type: 'image/png' });
      const filename = `frame_${String(i).padStart(5, '0')}.png`;
      await this.ffmpeg.writeFile(filename, await fetchFile(blob));

      // Report progress
      self.postMessage({
        type: 'progress',
        percent: (i / frames.length) * 50 // First 50% for frame generation
      });
    }

    // Encode with ffmpeg
    await this.ffmpeg.exec([
      '-framerate', String(settings.fps),
      '-i', 'frame_%05d.png',
      '-c:v', 'libvpx-vp9',
      '-b:v', String(settings.bitrate),
      '-pix_fmt', 'yuv420p',
      'output.webm'
    ]);

    // Read output
    const data = await this.ffmpeg.readFile('output.webm');
    return new Blob([data], { type: 'video/webm' });
  }
}
```

### Performance Targets

| Resolution | FPS | Target Encode Time | Hardware |
|------------|-----|-------------------|----------|
| 720p | 30 | <1x realtime | 2018+ laptop |
| 1080p | 30 | <2x realtime | 2020+ laptop |
| 1080p | 60 | <3x realtime | 2020+ laptop |
| 4K | 30 | <4x realtime | 2022+ laptop |
| 4K | 60 | <6x realtime | 2023+ desktop |

---

## CyberEco Hub Integration

Complete integration guide continues in next section...

_[Document continues with sections 6-10: Google Drive Integration, Deployment Strategy, Performance, and Roadmap]_

