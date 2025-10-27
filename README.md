<div align="center">

```
╔╦╗┬─┐┌─┐┬  ┬┌─┐┬  ╦ ╦┬ ┬┌┐
 ║ ├┬┘├─┤└┐┌┘├┤ │  ╠═╣│ │├┴┐
 ╩ ┴└─┴ ┴ └┘ └─┘┴─┘╩ ╩└─┘└─┘
```

**Own Your Journey, Share Your Story, Protect Your Privacy**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-2.0-green.svg)](https://github.com/ArtemioPadilla/TravelHub)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/ArtemioPadilla/TravelHub/pulls)
[![Made with Love](https://img.shields.io/badge/Made%20with-❤️-red.svg)](https://github.com/ArtemioPadilla/TravelHub)

[Features](#-key-features) • [Quick Start](#-quick-start) • [Tech Stack](#-tech-stack) • [Documentation](#-documentation) • [Roadmap](#-roadmap) • [Contributing](#-contributing)

</div>

---

## 🌍 About TravelHub

**TravelHub** is a **free, open-source, privacy-first** Progressive Web Application that revolutionizes how you plan, document, and share your travel experiences. Unlike existing solutions that address only fragments of the travel journey, TravelHub provides a **complete platform** covering the entire travel lifecycle—from initial planning through active documentation to long-term archival and beautiful video creation.

### Why TravelHub?

TravelHub is the **100% free alternative to mult.dev** ($49-299/month) with superior features:

- ✅ **Always Free** - No paywalls, no watermarks, no artificial limitations
- ✅ **Complete Lifecycle** - Only tool covering plan → travel → document → share → archive
- ✅ **Privacy-First** - Your data stays on your device or in your CyberEco Hub
- ✅ **Open Source** - Transparent, auditable, community-driven (MIT License)
- ✅ **Digital Sovereignty** - Integration with [CyberEco Hub](https://cybere.co) ecosystem
- ✅ **Zero Operational Cost** - Client-side architecture means unlimited scalability

### vs. mult.dev

| Feature | TravelHub | mult.dev |
|---------|-----------|----------|
| **Price** | $0 forever | $49-299/month |
| **Waypoints** | Unlimited | Limited by tier |
| **Watermarks** | None | Paid tiers only |
| **Commercial Use** | Always allowed | Premium only |
| **Trip Planning** | Full suite | No |
| **Active Travel Mode** | Yes | No |
| **Lifetime Repository** | Yes | No |
| **Open Source** | Yes | No |
| **Data Ownership** | 100% yours | Vendor lock-in |
| **CyberEco Integration** | Yes | N/A |

---

## ✨ Key Features

### 🗺️ **Trip Planning (Pre-Travel)**
- Interactive map-based route planning with **unlimited waypoints**
- Multi-stop itinerary builder with dates, activities, accommodations
- **Collaborative planning** with real-time synchronization
- Budget tracking with multi-currency support
- Smart recommendations (weather, Wikipedia, Wikivoyage)
- GPX/KML import from Google Maps and GPS devices

### 📱 **Active Travel Mode (During Travel)**
- Quick capture: Photo + location + note in **<10 seconds**
- Background GPS tracking with battery optimization
- Voice notes with automatic transcription
- **Complete offline functionality**
- One-tap check-in at waypoints
- Lock screen widget for instant notes

### 📸 **Travel Documentation (Post-Travel)**
- Batch photo upload with automatic geotagging
- Google Photos integration (import by date/album)
- Rich text notes with markdown support
- AI-powered photo tagging and organization
- Face recognition integration
- **Searchable full-text database** of all trip content

### 🎬 **Video Export System**
- Animated 3D globe with route visualization
- Support for **720p, 1080p, 4K at 30/60 FPS**
- Multiple aspect ratios (16:9, 9:16, 1:1, 4:5)
- Platform-optimized presets (Instagram, TikTok, YouTube)
- Customizable themes and animations
- **Zero watermarks, unlimited exports, commercial use allowed**

### 🌐 **Interactive Explorer**
- Instagram Stories-style navigation
- Auto-play mode with timeline scrubber
- Embeddable widgets for blogs/websites
- QR code generation for offline sharing
- Full keyboard and mobile gesture support

### 📚 **Personal Travel Repository**
- Global map showing **all visited locations**
- Statistics dashboard (countries, distance, days traveling)
- Advanced filtering and search
- Trip comparison and analytics
- "First time visited" badges and milestones
- Lifetime travel history visualization

### 🔐 **CyberEco Ecosystem Integration**
- Single sign-on (SSO) via [CyberEco Hub](https://cybere.co)
- Cloud storage in **your** CyberEco Hub account
- Cross-app data sharing (e.g., expenses with JustSplit)
- **Digital sovereignty** and data ownership
- Participation in CyberEco community governance

### 🤖 **AI-Powered Intelligence**
- Natural language route parsing ("Flight from Paris to Rome, then train to Florence...")
- Automatic photo tagging and categorization
- Smart destination recommendations
- Optimal route reordering
- Caption and itinerary suggestions

---

## 🚀 Quick Start

### Prerequisites

- **Node.js** 20+ (with npm or pnpm)
- Modern browser (Chrome, Edge, Firefox, Safari)

### Installation

```bash
# Clone the repository
git clone https://github.com/ArtemioPadilla/TravelHub.git
cd TravelHub

# Install dependencies
npm install
# or
pnpm install

# Start development server
npm run dev
# or
pnpm dev
```

The app will be available at `http://localhost:5173`

### Building for Production

```bash
# Build static files
npm run build

# Preview production build
npm run preview
```

### Deploy

TravelHub is a **static site** that can be deployed to:
- **Cloudflare Pages** (recommended, free tier)
- GitHub Pages
- Vercel
- Netlify
- Any static hosting service

---

## 🎨 Demo & Screenshots

> 🚧 **Coming Soon** - Screenshots and live demo will be added once Phase 1 MVP is complete.

**Planned Demo Sections:**
- [ ] Trip planning interface
- [ ] Interactive map with waypoints
- [ ] Video export preview
- [ ] Personal travel repository
- [ ] Mobile active travel mode

**Live Demo:** [travelhub.app](https://travelhub.app) _(Coming Soon)_

---

## 🛠️ Tech Stack

### Frontend
| Technology | Purpose |
|------------|---------|
| **React 18** + TypeScript | UI framework with type safety |
| **Vite** | Fast dev server and optimized builds |
| **Tailwind CSS** | Utility-first styling |
| **Zustand** | Lightweight state management |
| **MapLibre GL JS** | Open-source map rendering |

### Processing & Storage
| Technology | Purpose |
|------------|---------|
| **IndexedDB** + OPFS | Local browser storage (50GB+) |
| **WebCodecs API** | Hardware-accelerated video encoding |
| **ffmpeg.wasm** | Fallback video encoder |
| **Web Workers** | Background processing |
| **Service Worker** | PWA offline capabilities |

### Cloud (Optional)
| Technology | Purpose |
|------------|---------|
| **CyberEco Hub API** | Primary cloud storage & SSO |
| **Google Drive/Photos** | Alternative cloud storage |

### Deployment
| Technology | Purpose |
|------------|---------|
| **Cloudflare Pages** | Free static hosting with CDN |
| **GitHub Actions** | CI/CD automation |

### External APIs (All Free Tier)
- **OpenStreetMap** - Map tiles
- **Nominatim** - Geocoding
- **OSRM** - Routing
- **Open-Meteo** - Weather forecasts
- **Cohere** - LLM for AI features

---

## 📖 Documentation

Comprehensive documentation is available in the repository:

- **[Executive Summary](./EXECUTIVE_SUMMARY.md)** - Project overview, market analysis, business model
- **[Software Requirements Specification (SRS)](./SRS.md)** - Complete feature specifications and requirements
- **[Technical Architecture](./technical-architecture.md)** - System design, tech stack, and implementation details
- **[Development Guide (CLAUDE.md)](./CLAUDE.md)** - AI-assisted development guidelines and code patterns

---

## 🗺️ Roadmap

### Phase 1: MVP (Weeks 1-6) 🎯
**Status:** In Progress

- [ ] Trip creation and editing
- [ ] Map integration with waypoints
- [ ] Photo upload and geotagging
- [ ] 1080p30fps video export
- [ ] Local storage (IndexedDB)
- [ ] Basic PWA functionality

**Target:** Product Hunt launch with 1,000+ GitHub stars

### Phase 2: Enhanced Features (Weeks 7-14) 🚀
**Status:** Planned

- [ ] CyberEco Hub integration (SSO, cloud storage)
- [ ] Google Drive/Photos integration
- [ ] Interactive Explorer with story navigation
- [ ] Trip planning tools (itinerary, budget, weather)
- [ ] Advanced video customization (4K, themes, music)
- [ ] Personal travel map and statistics

**Target:** 5,000+ monthly active users

### Phase 3: AI & Community (Weeks 15-26) 🌍
**Status:** Planned

- [ ] AI route parser and photo tagging
- [ ] Active travel mode (mobile-optimized)
- [ ] Collaborative editing (real-time sync)
- [ ] Template marketplace
- [ ] Multi-format exports (PDF, HTML, GPX)
- [ ] 10+ language support

**Target:** 20,000+ MAU, 100+ community templates

### Phase 4: Scale & Enterprise (Ongoing) 📈
**Status:** Future

- [ ] Public trip discovery feed
- [ ] Mobile apps (React Native)
- [ ] Browser extension
- [ ] White-label deployments
- [ ] Public API for integrations
- [ ] Advanced analytics

**Target:** 50,000+ MAU, featured in major tech publications

---

## 🤝 Contributing

We welcome contributions from the community! TravelHub is built on the principles of **open source collaboration** and **digital sovereignty**.

### How to Contribute

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/amazing-feature`)
3. **Commit your changes** (`git commit -m 'Add amazing feature'`)
4. **Push to the branch** (`git push origin feature/amazing-feature`)
5. **Open a Pull Request**

### Development Setup

```bash
# Fork and clone your fork
git clone https://github.com/YOUR_USERNAME/TravelHub.git
cd TravelHub

# Add upstream remote
git remote add upstream https://github.com/ArtemioPadilla/TravelHub.git

# Install dependencies
pnpm install

# Start dev server
pnpm dev
```

### Contribution Guidelines

- Follow the code style (ESLint + Prettier configured)
- Write meaningful commit messages
- Add tests for new features
- Update documentation as needed
- Read [CLAUDE.md](./CLAUDE.md) for development patterns

### Areas We Need Help With

- 🎨 UI/UX design and improvements
- 🌍 Internationalization (translations)
- 🧪 Testing and quality assurance
- 📝 Documentation and tutorials
- 🎬 Video export optimizations
- 🤖 AI/ML features
- 📱 Mobile PWA enhancements

---

## 📄 License

TravelHub is licensed under the **MIT License** - see the [LICENSE](./LICENSE) file for details.

This means you can:
- ✅ Use commercially
- ✅ Modify and distribute
- ✅ Use privately
- ✅ Sublicense

**Attribution appreciated but not required.**

---

## 🙏 Acknowledgments

- **[CyberEco Hub](https://cybere.co)** - For providing the digital sovereignty infrastructure
- **OpenStreetMap** - For free, community-driven maps
- **MapLibre** - For the open-source mapping library
- **mult.dev** - For inspiration on travel visualization
- **Open Source Community** - For making projects like this possible

---

## 📬 Contact & Support

- **GitHub Issues:** [Report bugs or request features](https://github.com/ArtemioPadilla/TravelHub/issues)
- **Discussions:** [Ask questions or share ideas](https://github.com/ArtemioPadilla/TravelHub/discussions)
- **CyberEco Community:** [Join the ecosystem](https://cybere.co)

---

<div align="center">

**TravelHub** - Building the future of travel documentation, one trip at a time.

Made with ❤️ by the open source community

[⭐ Star us on GitHub](https://github.com/ArtemioPadilla/TravelHub) • [🐦 Follow updates](https://twitter.com/travelhub) • [📧 Subscribe to newsletter](https://travelhub.app/newsletter)

</div>
