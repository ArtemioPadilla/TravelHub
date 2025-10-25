# TravelVault - Arquitectura Técnica
## Stack React con Storage Híbrido y Costos Mínimos

**Versión:** 1.0  
**Fecha:** Octubre 2025

---

## Índice

1. [Resumen Ejecutivo](#resumen-ejecutivo)
2. [Stack Tecnológico](#stack-tecnológico)
3. [Arquitectura de Aplicación](#arquitectura-de-aplicación)
4. [Sistema de Almacenamiento Híbrido](#sistema-de-almacenamiento-híbrido)
5. [Procesamiento de Video Client-Side](#procesamiento-de-video-client-side)
6. [Integración Google Drive/Photos](#integración-google-drivephotos)
7. [Backend Opcional (Premium Features)](#backend-opcional-premium-features)
8. [Estrategia de Deployment](#estrategia-de-deployment)
9. [Consideraciones de Performance](#consideraciones-de-performance)
10. [Roadmap de Implementación](#roadmap-de-implementación)

---

## Resumen Ejecutivo

TravelVault será una **aplicación React moderna** (Next.js 14+ con App Router) que implementa un modelo de negocio híbrido innovador:

### Propuesta de Valor Central

**Para usuarios gratuitos:**
- 100% funcionalidad completa del ciclo de vida de viajes
- Storage en Google Drive del usuario (15GB gratis, expandible pagando a Google)
- Procesamiento completamente client-side (privacidad total)
- $0 de costos operacionales para nosotros

**Para usuarios premium ($5-20/mo):**
- Storage gestionado en nuestra infraestructura
- Features colaborativos en tiempo real
- Custom branding y dominios
- Priority support

### Ventajas de la Arquitectura

✅ **React sobre Svelte:**
- Ecosistema más maduro (más librerías, más developers)
- Mejor soporte corporativo (Meta)
- Más fácil contratar/onboard developers
- Mejor integración con tooling moderno

✅ **Client-side first:**
- Escala infinitamente (compute en dispositivos de usuarios)
- Zero server costs para free tier
- Privacy by design (data nunca sale del dispositivo sin consentimiento)

✅ **Progressive enhancement:**
- Funcionalidad base sin backend
- Premium features agregan valor sin limitar free tier

---

## Stack Tecnológico

### Frontend Core

```typescript
// package.json (simplified)
{
  "dependencies": {
    // Framework
    "next": "^14.2.0",              // React framework con SSR/SSG
    "react": "^18.3.0",
    "react-dom": "^18.3.0",
    
    // State Management
    "@reduxjs/toolkit": "^2.0.0",   // State management con RTK Query
    "zustand": "^4.5.0",            // Lightweight state para UI
    
    // Routing (Next.js App Router built-in)
    
    // UI Components
    "tailwindcss": "^3.4.0",        // Utility-first CSS
    "@headlessui/react": "^1.7.0",  // Accessible UI components
    "framer-motion": "^11.0.0",     // Animations
    "lucide-react": "^0.344.0",     // Icons
    
    // Maps
    "maplibre-gl": "^4.0.0",        // Map rendering
    "react-map-gl": "^7.1.0",       // React wrapper para MapLibre
    "@turf/turf": "^6.5.0",         // Geospatial calculations
    
    // File Processing
    "togeojson": "^0.16.2",         // GPX/KML parsing
    "papaparse": "^5.4.1",          // CSV parsing
    
    // Storage
    "idb": "^8.0.0",                // IndexedDB wrapper
    "dexie": "^4.0.0",              // Reactive IndexedDB
    
    // Date/Time
    "date-fns": "^3.3.0",           // Date manipulation
    
    // Forms
    "react-hook-form": "^7.50.0",   // Form handling
    "zod": "^3.22.0",               // Schema validation
    
    // Rich Text
    "@tiptap/react": "^2.2.0",      // Rich text editor (journal)
    
    // Video Processing
    "@ffmpeg/ffmpeg": "^0.12.10",   // Video encoding fallback
    
    // Google APIs
    "@react-oauth/google": "^0.12.0", // Google OAuth
    "gapi-script": "^1.2.0",        // Google API client
    
    // Utilities
    "nanoid": "^5.0.0",             // ID generation
    "clsx": "^2.1.0",               // Conditional classnames
    "hash-wasm": "^4.11.0"          // Hashing para checksums
  },
  
  "devDependencies": {
    "typescript": "^5.3.0",
    "@types/react": "^18.2.0",
    "@types/node": "^20.0.0",
    "eslint": "^8.56.0",
    "prettier": "^3.2.0",
    "@playwright/test": "^1.41.0",  // E2E testing
    "vitest": "^1.2.0",             // Unit testing
    "@testing-library/react": "^14.2.0"
  }
}
```

### Backend Opcional (Premium)

```typescript
// Cloudflare Workers + KV + D1
// Solo necesario para premium storage y features colaborativos

{
  "dependencies": {
    "@cloudflare/workers-types": "^4.0.0",
    "hono": "^4.0.0",              // Lightweight web framework
    "zod": "^3.22.0",              // Validation
    "jose": "^5.2.0",              // JWT handling
    "nanoid": "^5.0.0"
  }
}
```

### Justificación de Elecciones

**¿Por qué Next.js sobre Create React App?**
- App Router moderno con RSC (React Server Components)
- Image optimization built-in
- API routes para OAuth proxy
- SSG para landing pages (SEO)
- Mejor DX con Fast Refresh

**¿Por qué Redux Toolkit?**
- RTK Query elimina boilerplate de data fetching
- DevTools excelente para debugging
- Integración con middleware para IndexedDB sync
- Normalized state para complex data structures

**¿Por qué Tailwind?**
- Utility-first permite rapid development
- PurgeCSS elimina CSS no usado (bundle pequeño)
- Design system consistente
- No CSS-in-JS overhead

**¿Por qué MapLibre sobre Mapbox?**
- Open source (no vendor lock-in)
- Zero cost (Mapbox cobra después de 50k loads/mes)
- Compatible con OSM tiles
- Feature-equivalent para nuestro use case

---

## Arquitectura de Aplicación

### Estructura de Directorios

```
/travel-platform
├── /src
│   ├── /app                      # Next.js App Router
│   │   ├── layout.tsx            # Root layout
│   │   ├── page.tsx              # Home page
│   │   ├── /dashboard            # Dashboard routes
│   │   ├── /planner              # Trip planner
│   │   ├── /animator             # Video animator
│   │   ├── /explorer             # Interactive explorer
│   │   ├── /t/[shortCode]        # Public trip viewer
│   │   └── /api                  # API routes (OAuth proxy)
│   │
│   ├── /components               # React components
│   │   ├── /ui                   # Base UI components
│   │   ├── /map                  # Map-related components
│   │   ├── /planner              # Planning components
│   │   └── /shared               # Shared components
│   │
│   ├── /lib                      # Utilities
│   │   ├── /storage              # Storage abstraction
│   │   │   ├── indexeddb.ts     # IndexedDB operations
│   │   │   ├── opfs.ts          # OPFS operations
│   │   │   ├── google-drive.ts  # Google Drive integration
│   │   │   └── storage-manager.ts # Unified interface
│   │   │
│   │   ├── /video                # Video processing
│   │   │   ├── webcodecs.ts     # WebCodecs encoder
│   │   │   ├── ffmpeg.ts        # FFmpeg fallback
│   │   │   └── encoder.ts       # Unified interface
│   │   │
│   │   ├── /map                  # Map utilities
│   │   │   ├── route-calculator.ts
│   │   │   ├── geocoder.ts
│   │   │   └── tile-cache.ts
│   │   │
│   │   └── /utils                # General utilities
│   │
│   ├── /store                    # Redux store
│   │   ├── store.ts              # Store configuration
│   │   ├── /slices               # State slices
│   │   │   ├── trips.ts
│   │   │   ├── user.ts
│   │   │   └── ui.ts
│   │   └── /api                  # RTK Query APIs
│   │
│   ├── /hooks                    # Custom hooks
│   │   ├── useStorage.ts
│   │   ├── useMap.ts
│   │   ├── useVideoEncoder.ts
│   │   └── useAuth.ts
│   │
│   ├── /types                    # TypeScript types
│   │   ├── trip.ts
│   │   ├── storage.ts
│   │   └── video.ts
│   │
│   └── /workers                  # Web Workers
│       ├── video-encoder.worker.ts
│       ├── route-calculator.worker.ts
│       └── sync.worker.ts
│
├── /public                       # Static assets
│   ├── /icons
│   ├── /templates
│   └── manifest.json             # PWA manifest
│
├── /workers-backend              # Cloudflare Workers (optional)
│   ├── /src
│   │   ├── index.ts              # Main worker
│   │   ├── /routes
│   │   └── /middleware
│   └── wrangler.toml             # CF Workers config
│
└── /docs                         # Documentation
    ├── architecture.md
    ├── api.md
    └── contributing.md
```

### Component Architecture

```typescript
// Component hierarchy (simplified)

<App>
  <Providers>                       // Redux, Auth, Theme
    <Layout>
      <Header>
        <UserMenu />
        <SearchBar />
        <NotificationBell />
      </Header>
      
      <Sidebar />
      
      <MainContent>
        {/* Routes */}
        <Dashboard />
        <Planner>
          <ItineraryBuilder />
          <BudgetManager />
          <PackingList />
        </Planner>
        <Animator>
          <RouteEditor />
          <CustomizationPanel />
          <Timeline />
          <ExportDialog />
        </Animator>
        <Explorer>
          <InteractiveMap />
          <PhotoGallery />
          <JournalViewer />
        </Explorer>
      </MainContent>
      
      <Footer />
    </Layout>
  </Providers>
</App>
```

### State Management Strategy

**Redux Toolkit para:**
- Trip data (normalized)
- User settings
- Global UI state (modals, notifications)
- Sync status

```typescript
// Example slice
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import type { Trip } from '@/types/trip';
import { storageManager } from '@/lib/storage';

export const fetchTrips = createAsyncThunk(
  'trips/fetchTrips',
  async () => {
    const trips = await storageManager.getAllTrips();
    return trips;
  }
);

const tripsSlice = createSlice({
  name: 'trips',
  initialState: {
    items: {} as Record<string, Trip>,
    ids: [] as string[],
    status: 'idle',
    activeTrip: null as string | null
  },
  reducers: {
    setActiveTrip: (state, action) => {
      state.activeTrip = action.payload;
    }
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchTrips.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(fetchTrips.fulfilled, (state, action) => {
        state.status = 'succeeded';
        // Normalize data
        action.payload.forEach(trip => {
          state.items[trip.id] = trip;
          if (!state.ids.includes(trip.id)) {
            state.ids.push(trip.id);
          }
        });
      });
  }
});

export default tripsSlice.reducer;
```

**Zustand para:**
- Transient UI state (sidebar collapsed, theme)
- Map state (viewport, selected markers)
- Form state temporal

```typescript
// Example Zustand store
import { create } from 'zustand';

interface MapStore {
  viewport: {
    latitude: number;
    longitude: number;
    zoom: number;
  };
  selectedWaypoint: string | null;
  setViewport: (viewport: Partial<MapStore['viewport']>) => void;
  selectWaypoint: (id: string | null) => void;
}

export const useMapStore = create<MapStore>((set) => ({
  viewport: {
    latitude: 40.7128,
    longitude: -74.0060,
    zoom: 10
  },
  selectedWaypoint: null,
  setViewport: (viewport) => 
    set((state) => ({ 
      viewport: { ...state.viewport, ...viewport } 
    })),
  selectWaypoint: (id) => set({ selectedWaypoint: id })
}));
```

---

## Sistema de Almacenamiento Híbrido

### Storage Manager - Abstracción Unificada

```typescript
// /src/lib/storage/storage-manager.ts

export enum StorageProvider {
  LOCAL = 'local',
  GOOGLE_DRIVE = 'google-drive',
  PREMIUM = 'premium'
}

export interface StorageConfig {
  provider: StorageProvider;
  googleDrive?: {
    accessToken: string;
    refreshToken: string;
  };
  premium?: {
    apiUrl: string;
    authToken: string;
  };
}

export class StorageManager {
  private provider: StorageProvider;
  private localDB: LocalStorage;
  private driveClient?: GoogleDriveClient;
  private premiumClient?: PremiumStorageClient;
  
  constructor(config: StorageConfig) {
    this.provider = config.provider;
    this.localDB = new LocalStorage();
    
    if (config.provider === StorageProvider.GOOGLE_DRIVE) {
      this.driveClient = new GoogleDriveClient(config.googleDrive!);
    } else if (config.provider === StorageProvider.PREMIUM) {
      this.premiumClient = new PremiumStorageClient(config.premium!);
    }
  }
  
  async saveTrip(trip: Trip): Promise<void> {
    // Always save to local IndexedDB first (instant)
    await this.localDB.saveTrip(trip);
    
    // Then sync to remote if configured
    if (this.driveClient) {
      await this.driveClient.saveTrip(trip);
    } else if (this.premiumClient) {
      await this.premiumClient.saveTrip(trip);
    }
  }
  
  async getTrip(id: string): Promise<Trip> {
    // Try local first (fast)
    const local = await this.localDB.getTrip(id);
    if (local) {
      // Background sync to check for updates
      this.syncTrip(id);
      return local;
    }
    
    // Fallback to remote
    if (this.driveClient) {
      const remote = await this.driveClient.getTrip(id);
      await this.localDB.saveTrip(remote); // Cache locally
      return remote;
    } else if (this.premiumClient) {
      const remote = await this.premiumClient.getTrip(id);
      await this.localDB.saveTrip(remote);
      return remote;
    }
    
    throw new Error('Trip not found');
  }
  
  async uploadPhoto(tripId: string, photo: File): Promise<string> {
    // Generate thumbnail locally
    const thumbnail = await generateThumbnail(photo);
    
    // Save thumbnail to IndexedDB (instant access)
    const photoId = nanoid();
    await this.localDB.saveThumbnail(photoId, thumbnail);
    
    // Upload full-res to remote storage
    let photoUrl: string;
    if (this.driveClient) {
      photoUrl = await this.driveClient.uploadFile(tripId, photo);
    } else if (this.premiumClient) {
      photoUrl = await this.premiumClient.uploadFile(tripId, photo);
    } else {
      // Local only - save to OPFS
      photoUrl = await this.localDB.saveToOPFS(photo);
    }
    
    return photoId;
  }
  
  private async syncTrip(id: string): Promise<void> {
    // Background sync logic
    // Compare checksums, merge changes, resolve conflicts
  }
}
```

### IndexedDB Schema

```typescript
// /src/lib/storage/indexeddb.ts
import Dexie, { Table } from 'dexie';

interface Trip {
  id: string;
  title: string;
  data: object;  // Full trip data
  updatedAt: number;
  checksum: string;
}

interface Photo {
  id: string;
  tripId: string;
  thumbnail: Blob;
  url?: string;  // URL to full-res (Drive or Premium)
  metadata: object;
}

interface CachedTile {
  key: string;  // `${z}-${x}-${y}`
  data: Blob;
  cachedAt: number;
}

class TravelVaultDB extends Dexie {
  trips!: Table<Trip>;
  photos!: Table<Photo>;
  tiles!: Table<CachedTile>;
  settings!: Table<{ key: string; value: any }>;
  
  constructor() {
    super('TravelVaultDB');
    this.version(1).stores({
      trips: 'id, updatedAt',
      photos: 'id, tripId, *tags',
      tiles: 'key, cachedAt',
      settings: 'key'
    });
  }
}

export const db = new TravelVaultDB();

// Usage
await db.trips.add({
  id: nanoid(),
  title: 'Summer Europe Trip',
  data: { ... },
  updatedAt: Date.now(),
  checksum: await calculateChecksum(data)
});
```

### Google Drive Integration

```typescript
// /src/lib/storage/google-drive.ts

export class GoogleDriveClient {
  private accessToken: string;
  private folderStructure = {
    root: 'TravelVault',
    trips: 'trips',
    photos: 'photos',
    videos: 'videos'
  };
  
  constructor(auth: { accessToken: string; refreshToken: string }) {
    this.accessToken = auth.accessToken;
  }
  
  async ensureFolderStructure(): Promise<void> {
    // Create TravelVault folder if doesn't exist
    // Create subfolders for trips, photos, videos
  }
  
  async saveTrip(trip: Trip): Promise<void> {
    const folderId = await this.getTripFolder(trip.id);
    
    // Save metadata.json
    await this.uploadFile(
      folderId,
      'metadata.json',
      JSON.stringify(trip, null, 2)
    );
    
    // Save route.geojson
    if (trip.route) {
      await this.uploadFile(
        folderId,
        'route.geojson',
        JSON.stringify(trip.route)
      );
    }
  }
  
  async uploadFile(
    folderId: string, 
    name: string, 
    content: string | Blob
  ): Promise<string> {
    const metadata = {
      name,
      parents: [folderId]
    };
    
    const formData = new FormData();
    formData.append('metadata', 
      new Blob([JSON.stringify(metadata)], { type: 'application/json' })
    );
    formData.append('file', 
      content instanceof Blob ? content : new Blob([content])
    );
    
    const response = await fetch(
      'https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart',
      {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${this.accessToken}`
        },
        body: formData
      }
    );
    
    const result = await response.json();
    return result.id;
  }
  
  async downloadFile(fileId: string): Promise<string> {
    const response = await fetch(
      `https://www.googleapis.com/drive/v3/files/${fileId}?alt=media`,
      {
        headers: {
          'Authorization': `Bearer ${this.accessToken}`
        }
      }
    );
    
    return response.text();
  }
  
  private async getTripFolder(tripId: string): Promise<string> {
    // Search for folder named tripId
    // If doesn't exist, create it
    // Return folder ID
  }
}
```

---

## Procesamiento de Video Client-Side

### WebCodecs Encoder (Primario)

```typescript
// /src/lib/video/webcodecs.ts

export class WebCodecsEncoder {
  private encoder: VideoEncoder | null = null;
  private width: number;
  private height: number;
  private frameRate: number;
  private chunks: EncodedVideoChunk[] = [];
  
  constructor(config: {
    width: number;
    height: number;
    frameRate: number;
    bitrate?: number;
  }) {
    this.width = config.width;
    this.height = config.height;
    this.frameRate = config.frameRate;
    
    // Check support
    if (!('VideoEncoder' in window)) {
      throw new Error('WebCodecs not supported');
    }
  }
  
  async initialize(): Promise<void> {
    this.encoder = new VideoEncoder({
      output: (chunk, metadata) => {
        this.chunks.push(chunk);
      },
      error: (error) => {
        console.error('Encoding error:', error);
      }
    });
    
    const config: VideoEncoderConfig = {
      codec: 'avc1.42E01F', // H.264 baseline
      width: this.width,
      height: this.height,
      bitrate: 2_000_000,
      framerate: this.frameRate
    };
    
    // Verify config support
    const support = await VideoEncoder.isConfigSupported(config);
    if (!support.supported) {
      throw new Error('Config not supported');
    }
    
    this.encoder.configure(config);
  }
  
  async encodeFrame(
    canvas: HTMLCanvasElement, 
    timestamp: number
  ): Promise<void> {
    if (!this.encoder) throw new Error('Not initialized');
    
    // Create VideoFrame from canvas
    const frame = new VideoFrame(canvas, {
      timestamp: timestamp * 1_000_000, // microseconds
    });
    
    // Encode
    this.encoder.encode(frame, { keyFrame: timestamp === 0 });
    
    // Close frame to free memory
    frame.close();
  }
  
  async finalize(): Promise<Uint8Array> {
    if (!this.encoder) throw new Error('Not initialized');
    
    // Flush remaining frames
    await this.encoder.flush();
    
    // Mux chunks into MP4
    const mp4Data = await this.muxToMP4(this.chunks);
    
    this.encoder.close();
    return mp4Data;
  }
  
  private async muxToMP4(chunks: EncodedVideoChunk[]): Promise<Uint8Array> {
    // Use mp4box.js or similar for muxing
    // For simplicity, this is pseudocode
    const muxer = new MP4Muxer({
      width: this.width,
      height: this.height,
      frameRate: this.frameRate
    });
    
    for (const chunk of chunks) {
      muxer.addChunk(chunk);
    }
    
    return muxer.finalize();
  }
}
```

### FFmpeg.wasm Fallback

```typescript
// /src/lib/video/ffmpeg.ts
import { FFmpeg } from '@ffmpeg/ffmpeg';
import { fetchFile } from '@ffmpeg/util';

export class FFmpegEncoder {
  private ffmpeg: FFmpeg;
  
  constructor() {
    this.ffmpeg = new FFmpeg();
  }
  
  async initialize(): Promise<void> {
    await this.ffmpeg.load();
  }
  
  async encodeFrames(
    frames: ImageData[], 
    config: { width: number; height: number; frameRate: number }
  ): Promise<Uint8Array> {
    // Convert frames to PNG images
    for (let i = 0; i < frames.length; i++) {
      const blob = await this.imageToPNG(frames[i]);
      await this.ffmpeg.writeFile(`frame${i.toString().padStart(5, '0')}.png`, await fetchFile(blob));
    }
    
    // Run FFmpeg command
    await this.ffmpeg.exec([
      '-framerate', config.frameRate.toString(),
      '-i', 'frame%05d.png',
      '-c:v', 'libx264',
      '-preset', 'medium',
      '-crf', '23',
      '-pix_fmt', 'yuv420p',
      'output.mp4'
    ]);
    
    // Read output file
    const data = await this.ffmpeg.readFile('output.mp4');
    return data as Uint8Array;
  }
  
  private async imageToPNG(imageData: ImageData): Promise<Blob> {
    const canvas = document.createElement('canvas');
    canvas.width = imageData.width;
    canvas.height = imageData.height;
    const ctx = canvas.getContext('2d')!;
    ctx.putImageData(imageData, 0, 0);
    return new Promise(resolve => canvas.toBlob(blob => resolve(blob!), 'image/png'));
  }
}
```

### Video Encoder Manager (Unified Interface)

```typescript
// /src/lib/video/encoder.ts

export class VideoEncoderManager {
  private encoder: WebCodecsEncoder | FFmpegEncoder;
  
  constructor(config: VideoConfig) {
    // Feature detection
    if (this.supportsWebCodecs()) {
      this.encoder = new WebCodecsEncoder(config);
    } else {
      this.encoder = new FFmpegEncoder();
    }
  }
  
  private supportsWebCodecs(): boolean {
    return typeof VideoEncoder !== 'undefined' &&
           typeof VideoFrame !== 'undefined';
  }
  
  async encode(
    frames: HTMLCanvasElement[], 
    onProgress?: (progress: number) => void
  ): Promise<Blob> {
    await this.encoder.initialize();
    
    for (let i = 0; i < frames.length; i++) {
      await this.encoder.encodeFrame(frames[i], i / 30);
      onProgress?.((i + 1) / frames.length * 100);
    }
    
    const data = await this.encoder.finalize();
    return new Blob([data], { type: 'video/mp4' });
  }
}
```

### Web Worker para Encoding

```typescript
// /src/workers/video-encoder.worker.ts

import { VideoEncoderManager } from '@/lib/video/encoder';

self.addEventListener('message', async (event) => {
  const { type, data } = event.data;
  
  if (type === 'ENCODE_VIDEO') {
    const { frames, config } = data;
    
    const encoder = new VideoEncoderManager(config);
    
    try {
      const videoBlob = await encoder.encode(frames, (progress) => {
        self.postMessage({ type: 'PROGRESS', progress });
      });
      
      self.postMessage({ 
        type: 'COMPLETE', 
        videoBlob 
      });
    } catch (error) {
      self.postMessage({ 
        type: 'ERROR', 
        error: error.message 
      });
    }
  }
});
```

---

## Integración Google Drive/Photos

### OAuth Flow con PKCE

```typescript
// /src/lib/auth/google-oauth.ts

export class GoogleOAuthClient {
  private clientId = process.env.NEXT_PUBLIC_GOOGLE_CLIENT_ID!;
  private redirectUri = `${window.location.origin}/auth/callback`;
  
  async initiateAuth(scopes: string[]): Promise<void> {
    // Generate PKCE challenge
    const codeVerifier = this.generateRandomString(128);
    const codeChallenge = await this.generateCodeChallenge(codeVerifier);
    
    // Store verifier in sessionStorage
    sessionStorage.setItem('code_verifier', codeVerifier);
    
    // Build authorization URL
    const params = new URLSearchParams({
      client_id: this.clientId,
      redirect_uri: this.redirectUri,
      response_type: 'code',
      scope: scopes.join(' '),
      code_challenge: codeChallenge,
      code_challenge_method: 'S256',
      access_type: 'offline',
      prompt: 'consent'
    });
    
    // Redirect to Google
    window.location.href = `https://accounts.google.com/o/oauth2/v2/auth?${params}`;
  }
  
  async handleCallback(code: string): Promise<TokenResponse> {
    const codeVerifier = sessionStorage.getItem('code_verifier');
    if (!codeVerifier) throw new Error('No code verifier found');
    
    // Exchange code for tokens via our backend proxy
    // (never expose client secret in frontend)
    const response = await fetch('/api/auth/google/token', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        code,
        code_verifier: codeVerifier,
        redirect_uri: this.redirectUri
      })
    });
    
    const tokens = await response.json();
    
    // Store tokens securely
    await this.storeTokens(tokens);
    
    return tokens;
  }
  
  private async storeTokens(tokens: TokenResponse): Promise<void> {
    // Encrypt tokens before storing in IndexedDB
    const encrypted = await this.encryptTokens(tokens);
    await db.settings.put({ key: 'google_tokens', value: encrypted });
  }
  
  private generateRandomString(length: number): string {
    const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-._~';
    let result = '';
    const randomValues = crypto.getRandomValues(new Uint8Array(length));
    randomValues.forEach(v => result += chars[v % chars.length]);
    return result;
  }
  
  private async generateCodeChallenge(verifier: string): Promise<string> {
    const encoder = new TextEncoder();
    const data = encoder.encode(verifier);
    const hash = await crypto.subtle.digest('SHA-256', data);
    return this.base64URLEncode(hash);
  }
  
  private base64URLEncode(buffer: ArrayBuffer): string {
    const bytes = new Uint8Array(buffer);
    let binary = '';
    bytes.forEach(b => binary += String.fromCharCode(b));
    return btoa(binary)
      .replace(/\+/g, '-')
      .replace(/\//g, '_')
      .replace(/=/g, '');
  }
}
```

### Backend OAuth Proxy

```typescript
// /src/app/api/auth/google/token/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function POST(request: NextRequest) {
  const { code, code_verifier, redirect_uri } = await request.json();
  
  // Exchange code for tokens (server-side to protect client secret)
  const response = await fetch('https://oauth2.googleapis.com/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      code,
      client_id: process.env.GOOGLE_CLIENT_ID!,
      client_secret: process.env.GOOGLE_CLIENT_SECRET!,
      redirect_uri,
      grant_type: 'authorization_code',
      code_verifier
    })
  });
  
  const tokens = await response.json();
  
  return NextResponse.json(tokens);
}
```

### Google Photos Auto-Sync

```typescript
// /src/lib/integrations/google-photos.ts

export class GooglePhotosSync {
  constructor(private accessToken: string) {}
  
  async syncPhotosForTrip(trip: Trip): Promise<void> {
    const { startDate, endDate } = trip;
    
    // Search photos in date range
    const photos = await this.searchPhotos(startDate, endDate);
    
    // Filter by geolocation (within trip bounds)
    const geotaggedPhotos = photos.filter(photo => {
      if (!photo.metadata.location) return false;
      return this.isWithinTripBounds(photo.metadata.location, trip);
    });
    
    // Associate photos with trip
    for (const photo of geotaggedPhotos) {
      await this.linkPhotoToTrip(photo, trip);
    }
  }
  
  private async searchPhotos(
    startDate: Date, 
    endDate: Date
  ): Promise<GooglePhoto[]> {
    const response = await fetch(
      'https://photoslibrary.googleapis.com/v1/mediaItems:search',
      {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${this.accessToken}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          filters: {
            dateFilter: {
              ranges: [{
                startDate: this.formatDate(startDate),
                endDate: this.formatDate(endDate)
              }]
            },
            mediaTypeFilter: {
              mediaTypes: ['PHOTO']
            }
          }
        })
      }
    );
    
    const data = await response.json();
    return data.mediaItems || [];
  }
  
  private isWithinTripBounds(
    location: { latitude: number; longitude: number },
    trip: Trip
  ): boolean {
    // Get bounding box of all waypoints
    const bounds = this.calculateBounds(trip.waypoints);
    
    // Add buffer (e.g., 50km)
    const buffer = 0.5; // degrees (~50km)
    
    return (
      location.latitude >= bounds.south - buffer &&
      location.latitude <= bounds.north + buffer &&
      location.longitude >= bounds.west - buffer &&
      location.longitude <= bounds.east + buffer
    );
  }
}
```

---

## Backend Opcional (Premium Features)

### Cloudflare Workers Setup

```typescript
// /workers-backend/src/index.ts
import { Hono } from 'hono';
import { cors } from 'hono/cors';
import { jwt } from 'hono/jwt';

type Bindings = {
  KV: KVNamespace;
  DB: D1Database;
  R2: R2Bucket;
  JWT_SECRET: string;
};

const app = new Hono<{ Bindings: Bindings }>();

// CORS
app.use('/*', cors({
  origin: ['https://travelplatform.com', 'http://localhost:3000'],
  credentials: true
}));

// JWT middleware para rutas protected
app.use('/api/*', async (c, next) => {
  const authHeader = c.req.header('Authorization');
  if (!authHeader?.startsWith('Bearer ')) {
    return c.json({ error: 'Unauthorized' }, 401);
  }
  
  try {
    const token = authHeader.substring(7);
    const payload = await jwt.verify(token, c.env.JWT_SECRET);
    c.set('userId', payload.sub);
    await next();
  } catch {
    return c.json({ error: 'Invalid token' }, 401);
  }
});

// Trip endpoints
app.post('/api/trips', async (c) => {
  const userId = c.get('userId');
  const trip = await c.req.json();
  
  // Save to D1 database
  await c.env.DB.prepare(
    'INSERT INTO trips (id, user_id, data) VALUES (?, ?, ?)'
  ).bind(trip.id, userId, JSON.stringify(trip)).run();
  
  return c.json({ success: true });
});

app.get('/api/trips/:id', async (c) => {
  const tripId = c.req.param('id');
  const userId = c.get('userId');
  
  const result = await c.env.DB.prepare(
    'SELECT * FROM trips WHERE id = ? AND user_id = ?'
  ).bind(tripId, userId).first();
  
  if (!result) {
    return c.json({ error: 'Not found' }, 404);
  }
  
  return c.json(JSON.parse(result.data));
});

// File upload (presigned URL)
app.post('/api/upload/presign', async (c) => {
  const { tripId, fileName, fileType } = await c.req.json();
  const userId = c.get('userId');
  
  // Generate unique key
  const key = `${userId}/${tripId}/${nanoid()}-${fileName}`;
  
  // Generate presigned URL for R2
  const url = await c.env.R2.createPresignedUrl(key, {
    method: 'PUT',
    expiresIn: 900 // 15 minutes
  });
  
  return c.json({ uploadUrl: url, key });
});

// Public trip sharing
app.get('/t/:shortCode', async (c) => {
  const shortCode = c.req.param('shortCode');
  
  // Lookup shortCode in KV
  const tripId = await c.env.KV.get(`share:${shortCode}`);
  if (!tripId) {
    return c.json({ error: 'Not found' }, 404);
  }
  
  // Increment view count
  await c.env.DB.prepare(
    'UPDATE shared_trips SET views = views + 1 WHERE trip_id = ?'
  ).bind(tripId).run();
  
  // Get trip data
  const trip = await c.env.DB.prepare(
    'SELECT data FROM trips WHERE id = ?'
  ).bind(tripId).first();
  
  return c.json(JSON.parse(trip.data));
});

export default app;
```

### Database Schema (D1)

```sql
-- /workers-backend/schema.sql

CREATE TABLE users (
  id TEXT PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  name TEXT,
  storage_tier TEXT DEFAULT 'free',
  storage_used_bytes INTEGER DEFAULT 0,
  storage_limit_bytes INTEGER,
  created_at INTEGER DEFAULT (unixepoch())
);

CREATE TABLE trips (
  id TEXT PRIMARY KEY,
  user_id TEXT NOT NULL,
  data TEXT NOT NULL,
  created_at INTEGER DEFAULT (unixepoch()),
  updated_at INTEGER DEFAULT (unixepoch()),
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE INDEX idx_trips_user ON trips(user_id);

CREATE TABLE shared_trips (
  short_code TEXT PRIMARY KEY,
  trip_id TEXT NOT NULL,
  created_by TEXT NOT NULL,
  created_at INTEGER DEFAULT (unixepoch()),
  expires_at INTEGER,
  views INTEGER DEFAULT 0,
  permissions TEXT, -- JSON
  FOREIGN KEY (trip_id) REFERENCES trips(id),
  FOREIGN KEY (created_by) REFERENCES users(id)
);

CREATE TABLE assets (
  id TEXT PRIMARY KEY,
  trip_id TEXT NOT NULL,
  user_id TEXT NOT NULL,
  type TEXT NOT NULL, -- 'photo', 'video', 'document'
  r2_key TEXT NOT NULL,
  size_bytes INTEGER NOT NULL,
  mime_type TEXT,
  uploaded_at INTEGER DEFAULT (unixepoch()),
  FOREIGN KEY (trip_id) REFERENCES trips(id),
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE INDEX idx_assets_trip ON assets(trip_id);
CREATE INDEX idx_assets_user ON assets(user_id);
```

---

## Estrategia de Deployment

### Cloudflare Pages (Frontend)

```yaml
# wrangler.toml (Cloudflare Pages config)

name = "travel-platform"
compatibility_date = "2024-01-01"

[build]
command = "npm run build"
cwd = "."
watch_paths = ["src/**/*.{ts,tsx}"]

[[pages_routes]]
pattern = "*"
zone_id = "your-zone-id"

[env.production]
vars = { ENVIRONMENT = "production" }

[env.preview]
vars = { ENVIRONMENT = "preview" }
```

### Deployment Workflow

```yaml
# .github/workflows/deploy.yml

name: Deploy to Cloudflare Pages

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Build
        run: npm run build
        env:
          NEXT_PUBLIC_GOOGLE_CLIENT_ID: ${{ secrets.GOOGLE_CLIENT_ID }}
      
      - name: Deploy to Cloudflare Pages
        uses: cloudflare/pages-action@v1
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          accountId: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
          projectName: travel-platform
          directory: out
          gitHubToken: ${{ secrets.GITHUB_TOKEN }}
```

### Environment Variables

```bash
# .env.local (never commit this)

# Google OAuth
NEXT_PUBLIC_GOOGLE_CLIENT_ID=xxx.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=xxx

# Backend (if premium features enabled)
NEXT_PUBLIC_API_URL=https://api.travelplatform.com

# Analytics (optional)
NEXT_PUBLIC_PLAUSIBLE_DOMAIN=travelplatform.com
```

---

## Consideraciones de Performance

### Code Splitting Strategy

```typescript
// next.config.js
module.exports = {
  webpack: (config) => {
    config.optimization.splitChunks = {
      chunks: 'all',
      cacheGroups: {
        // Map libraries (large)
        map: {
          test: /[\\/]node_modules[\\/](maplibre-gl|react-map-gl)[\\/]/,
          name: 'map',
          priority: 10
        },
        // Video processing (very large)
        video: {
          test: /[\\/]node_modules[\\/](@ffmpeg)[\\/]/,
          name: 'video',
          priority: 10
        },
        // UI libraries
        ui: {
          test: /[\\/]node_modules[\\/](framer-motion|@headlessui)[\\/]/,
          name: 'ui',
          priority: 5
        }
      }
    };
    return config;
  }
};
```

### Lazy Loading Components

```typescript
// Lazy load heavy components
import dynamic from 'next/dynamic';

const MapComponent = dynamic(() => import('@/components/map/Map'), {
  ssr: false,
  loading: () => <MapSkeleton />
});

const VideoEditor = dynamic(() => import('@/components/animator/VideoEditor'), {
  ssr: false,
  loading: () => <EditorSkeleton />
});
```

### Image Optimization

```typescript
// Use Next.js Image component
import Image from 'next/image';

<Image
  src={photo.url}
  alt={photo.caption}
  width={400}
  height={300}
  placeholder="blur"
  blurDataURL={photo.thumbnail}
  loading="lazy"
/>
```

### Performance Budget

```javascript
// lighthouse-ci.config.js
module.exports = {
  ci: {
    collect: {
      numberOfRuns: 3
    },
    assert: {
      assertions: {
        'categories:performance': ['error', { minScore: 0.9 }],
        'categories:accessibility': ['error', { minScore: 0.95 }],
        'first-contentful-paint': ['error', { maxNumericValue: 2000 }],
        'largest-contentful-paint': ['error', { maxNumericValue: 2500 }],
        'cumulative-layout-shift': ['error', { maxNumericValue: 0.1 }],
        'total-blocking-time': ['error', { maxNumericValue: 300 }]
      }
    }
  }
};
```

---

## Roadmap de Implementación

### Fase 1: MVP Core (8-10 semanas)

**Semanas 1-2: Setup y Fundaciones**
- ✅ Proyecto Next.js 14 con TypeScript
- ✅ Tailwind CSS + componentes UI base
- ✅ Redux Toolkit setup
- ✅ IndexedDB con Dexie
- ✅ Routing y layout principal

**Semanas 3-4: Gestión de Trips Básica**
- ✅ CRUD de trips (local storage)
- ✅ Biblioteca de trips con filtros
- ✅ Trip detail page
- ✅ Estados de trip (Planning/Ongoing/Completed)

**Semanas 5-6: Map Integration**
- ✅ MapLibre GL integration
- ✅ Waypoint management (add/edit/delete)
- ✅ Route drawing
- ✅ Geocoding search

**Semanas 7-8: Video Animation MVP**
- ✅ Timeline component
- ✅ Basic customization (colors, icons)
- ✅ Canvas rendering de animación
- ✅ WebCodecs encoding (1080p30fps)
- ✅ FFmpeg.wasm fallback

**Semanas 9-10: Polish y Testing**
- ✅ PWA setup (service worker, manifest)
- ✅ Offline functionality
- ✅ E2E tests críticos
- ✅ Performance optimization
- ✅ Bug fixes

**Deliverable:** Aplicación funcional que permite crear trips, agregar rutas manualmente, y exportar videos animados básicos.

### Fase 2: Google Drive Integration (4-6 semanas)

**Semanas 11-12: OAuth y Storage**
- ✅ Google OAuth con PKCE
- ✅ Google Drive API integration
- ✅ Folder structure en Drive
- ✅ Upload/download de trip data

**Semanas 13-14: Sync Engine**
- ✅ Background sync logic
- ✅ Conflict resolution
- ✅ Offline queue
- ✅ Sync status indicators

**Semanas 15-16: Google Photos Sync**
- ✅ Photos API integration
- ✅ EXIF parsing
- ✅ Auto-linking de fotos a trips
- ✅ Thumbnail generation

**Deliverable:** Free tier completamente funcional con storage en Google Drive del usuario.

### Fase 3: Planning Features (6-8 semanas)

**Semanas 17-18: Itinerary Builder**
- ✅ Day-by-day planning
- ✅ Activity/accommodation items
- ✅ Drag & drop reordering
- ✅ Time validation

**Semanas 19-20: Budget Tracking**
- ✅ Budget por categoría
- ✅ Expense logging
- ✅ Currency conversion
- ✅ Split expenses

**Semanas 21-22: Research Board**
- ✅ Save links/photos/notes
- ✅ Organization system
- ✅ Web clipper extension

**Semanas 23-24: Packing List & Polish**
- ✅ Template-based packing lists
- ✅ Smart suggestions
- ✅ Collaborative features básicas

**Deliverable:** Herramienta completa de planeación de viajes.

### Fase 4: Interactive Explorer (4-5 semanas)

**Semanas 25-26: Explorer UI**
- ✅ Fullscreen map view
- ✅ Animated playback
- ✅ Photo/journal sync con timeline

**Semanas 27-28: Sharing & Public Views**
- ✅ Generate public links
- ✅ Public trip viewer
- ✅ Embed codes
- ✅ SEO optimization

**Semana 29: Analytics & Polish**
- ✅ View tracking (privacy-respecting)
- ✅ Share analytics dashboard
- ✅ Refinements

**Deliverable:** Capacidad de compartir trips públicamente con experiencia interactiva.

### Fase 5: Premium Storage Backend (6-8 semanas)

**Semanas 30-32: Backend Infrastructure**
- ✅ Cloudflare Workers setup
- ✅ D1 database schema
- ✅ R2 object storage
- ✅ Authentication system

**Semanas 33-35: Premium Features**
- ✅ Managed storage
- ✅ Real-time collaboration
- ✅ Custom branding
- ✅ Analytics dashboard

**Semanas 36-37: Billing Integration**
- ✅ Stripe integration
- ✅ Subscription management
- ✅ Usage tracking
- ✅ Quota enforcement

**Deliverable:** Premium tier completamente funcional con monetización.

### Total: 37 semanas (~9 meses) para producto completo

**Milestones principales:**
- **Semana 10:** MVP lanzable (Fase 1)
- **Semana 16:** Free tier completo (Fase 2)
- **Semana 24:** Plataforma integral (Fase 3)
- **Semana 29:** Sharing público (Fase 4)
- **Semana 37:** Premium features (Fase 5)

---

## Conclusión

Esta arquitectura React proporciona:

✅ **Escalabilidad ilimitada** (client-side compute)  
✅ **$0 costos operacionales** para free tier  
✅ **Privacy by design** (data local o en Drive del usuario)  
✅ **Monetización flexible** (premium storage opcional)  
✅ **Stack moderno y mantenible** (React, Next.js, TypeScript)  
✅ **Progressive enhancement** (funciona sin backend)  

El producto resultante será una plataforma **única en el mercado** que combina planeación, documentación, animación y exploración de viajes en una experiencia cohesiva, con un modelo de negocio sostenible que respeta la privacidad del usuario mientras ofrece path claro hacia monetización premium.

**Próximos pasos:**
1. Setup inicial del proyecto
2. Definir design system detallado
3. Comenzar implementación de Fase 1
4. Iterar basado en feedback de early adopters