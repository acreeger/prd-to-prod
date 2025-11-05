# GitHub Issue Breakdown for Autonomous Agent Execution

> **Purpose**: Structured GitHub issues enabling autonomous AI agent execution
> **Based on**: GEMINI_MILESTONE_TOOLS.md
> **Audience**: AI coding agents (Claude Code, Gemini, GitHub Copilot)
> **Last Updated**: 2025-11-05

---

## Issue Template Structure

Each issue follows this format for autonomous execution:

```markdown
**Milestone**: MS-XX.XX
**Dependencies**: [List of blocking issues]
**Estimated Complexity**: [Low/Medium/High]

### Objective
[Clear, single-sentence goal]

### Required Tools & Packages
- ✅ Tool Name (why it's required)
- ❌ Forbidden Tool (why to avoid)

### Implementation Steps
1. [Atomic, testable step]
2. [Next step with verification]

### Success Criteria
- [ ] Criterion 1 (how to verify)
- [ ] Criterion 2 (how to verify)

### Anti-Patterns to Avoid
- ❌ Pattern to avoid (why)

### Environment Variables
```bash
KEY=value
```

### Testing Commands
```bash
# Command to verify success
```

### Reference Files
- Path to relevant file
```

---

## Phase 1: Core Application (No Database)

---

### Issue #1: MS-01.01 - Project Setup & Scaffolding

**Milestone**: MS-01.01
**Dependencies**: None
**Estimated Complexity**: Low
**Labels**: `setup`, `milestone-01`, `phase-1`

#### Objective
Bootstrap Next.js 16 project with TypeScript, Tailwind CSS, and core dependencies using pnpm.

#### Required Tools & Packages
- ✅ **Next.js 16** (latest stable with App Router)
- ✅ **TypeScript** (type safety)
- ✅ **Tailwind CSS** (styling framework)
- ✅ **pnpm** (package manager - faster than npm/yarn)
- ✅ **@vis.gl/react-google-maps** (Google Maps integration)
- ✅ **swr** (data fetching with auto-refresh)
- ✅ **framer-motion** (smooth animations)
- ❌ **NO react-spring** (conflicts with Google Maps)
- ❌ **NO database packages** (Phase 1 is database-free)
- ❌ **NO Redux/MobX** (unnecessary complexity)

#### Implementation Steps

1. **Create Next.js project**:
```bash
pnpx create-next-app@latest fifa-navigator \
  --typescript \
  --tailwind \
  --app \
  --src-dir \
  --import-alias "@/*" \
  --use-pnpm \
  --eslint
```

2. **Navigate to project directory**:
```bash
cd fifa-navigator
```

3. **Install additional dependencies**:
```bash
pnpm add @vis.gl/react-google-maps swr framer-motion
pnpm add -D @types/node @types/react @types/react-dom
```

4. **Create base directory structure**:
```bash
mkdir -p src/components src/lib src/types src/data
```

5. **Create .env.local file**:
```bash
cat > .env.local << EOF
NEXT_PUBLIC_GMAK=placeholder_google_maps_key
MTAK=placeholder_marta_bus_key
MARTA_TRAIN_API_KEY=placeholder_marta_train_key
EOF
```

6. **Verify build works**:
```bash
pnpm build
```

#### Success Criteria
- [ ] `pnpm dev` starts development server successfully on port 3000
- [ ] `pnpm build` completes without errors
- [ ] TypeScript compilation succeeds with no errors
- [ ] Tailwind CSS is working (verify with test utility class)
- [ ] All three core packages installed: @vis.gl/react-google-maps, swr, framer-motion
- [ ] Directory structure created: src/components, src/lib, src/types, src/data
- [ ] .env.local exists with placeholder API keys

#### Anti-Patterns to Avoid
- ❌ **Installing react-spring** - Use framer-motion instead
- ❌ **Installing database packages (Prisma, @vercel/postgres)** - Not needed in Phase 1
- ❌ **Using npm or yarn** - Project requires pnpm for consistency
- ❌ **Installing next-i18next or react-intl** - Use Next.js built-in i18n
- ❌ **Creating custom webpack config** - Keep default Next.js config

#### Environment Variables
```bash
# Google Maps API (public - used in browser)
NEXT_PUBLIC_GMAK=your_google_maps_api_key

# MARTA APIs (server-side only)
MTAK=your_marta_bus_api_key
MARTA_TRAIN_API_KEY=your_marta_train_api_key
```

#### Testing Commands
```bash
# Start dev server
pnpm dev

# Verify build
pnpm build

# Check TypeScript
pnpm tsc --noEmit

# Lint code
pnpm lint
```

#### Reference Files
- `package.json` - Verify dependencies
- `next.config.ts` - Next.js configuration
- `tailwind.config.ts` - Tailwind configuration
- `tsconfig.json` - TypeScript configuration

---

### Issue #2: MS-01.02 - Google Maps Integration

**Milestone**: MS-01.02
**Dependencies**: Issue #1 (MS-01.01)
**Estimated Complexity**: Medium
**Labels**: `feature`, `maps`, `milestone-01`, `phase-1`

#### Objective
Implement full-screen Google Maps centered on Atlanta with Mercedes-Benz Stadium marker using @vis.gl/react-google-maps.

#### Required Tools & Packages
- ✅ **@vis.gl/react-google-maps** (official Google Maps React wrapper)
- ✅ **AdvancedMarker** (modern marker component)
- ✅ **Tailwind CSS** (full-screen layout)
- ❌ **NOT react-google-maps/api** (older, unmaintained library)
- ❌ **NOT google-map-react** (outdated)

#### Implementation Steps

1. **Create MapView component** (`src/components/MapView.tsx`):
```typescript
'use client';

import { APIProvider, Map, AdvancedMarker } from '@vis.gl/react-google-maps';

const ATLANTA_CENTER = { lat: 33.7490, lng: -84.3880 };
const STADIUM_LOCATION = { lat: 33.755487, lng: -84.400993 };

export default function MapView() {
  const apiKey = process.env.NEXT_PUBLIC_GMAK || '';

  return (
    <APIProvider apiKey={apiKey}>
      <div className="w-full h-screen">
        <Map
          defaultCenter={ATLANTA_CENTER}
          defaultZoom={12}
          mapId="fifa-navigator-map"
          disableDefaultUI={false}
        >
          <AdvancedMarker position={STADIUM_LOCATION}>
            <div className="bg-red-600 text-white px-3 py-2 rounded-lg shadow-lg font-bold">
              Mercedes-Benz Stadium
            </div>
          </AdvancedMarker>
        </Map>
      </div>
    </APIProvider>
  );
}
```

2. **Create map types** (`src/types/index.ts`):
```typescript
export interface Location {
  lat: number;
  lng: number;
}

export interface Venue {
  id: string;
  name: string;
  location: Location;
  capacity?: number;
}
```

3. **Update main page** (`src/app/page.tsx`):
```typescript
import MapView from '@/components/MapView';

export default function Home() {
  return (
    <main className="relative">
      <MapView />
    </main>
  );
}
```

4. **Configure Google Maps API**:
- Enable Maps JavaScript API in Google Cloud Console
- Enable Places API (for future features)
- Add API key to `.env.local`

5. **Test map rendering**:
```bash
pnpm dev
# Navigate to http://localhost:3000
```

#### Success Criteria
- [ ] Map loads and displays centered on Atlanta (33.7490, -84.3880)
- [ ] Mercedes-Benz Stadium marker visible at correct location
- [ ] Map is full-screen (w-full h-screen)
- [ ] Map controls (zoom, pan) work correctly
- [ ] No console errors related to Google Maps API
- [ ] API key loaded from NEXT_PUBLIC_GMAK environment variable
- [ ] Component uses 'use client' directive (client-side rendering)

#### Anti-Patterns to Avoid
- ❌ **Using react-google-maps/api package** - Older library, use @vis.gl instead
- ❌ **Hardcoding API key in code** - Always use environment variables
- ❌ **Using deprecated Marker component** - Use AdvancedMarker
- ❌ **Missing 'use client' directive** - Map must be client-side
- ❌ **Using custom CSS instead of Tailwind** - Stick to Tailwind utilities

#### Environment Variables
```bash
# Google Maps API Key (must be public for browser access)
NEXT_PUBLIC_GMAK=AIzaSy...your_actual_key_here

# Note: Get key from https://console.cloud.google.com/
# Enable: Maps JavaScript API, Places API
```

#### Testing Commands
```bash
# Start dev server
pnpm dev

# Check for TypeScript errors
pnpm tsc --noEmit

# Verify build
pnpm build

# Test in browser
# 1. Open http://localhost:3000
# 2. Verify map loads
# 3. Check stadium marker appears
# 4. Test zoom/pan controls
```

#### Reference Files
- `src/components/MapView.tsx` - Main map component
- `src/types/index.ts` - Type definitions
- `src/app/page.tsx` - Root page using MapView
- `.env.local` - Environment variables

#### Additional Notes
- Map ID (`mapId="fifa-navigator-map"`) can be created in Google Cloud Console for advanced styling
- Initial zoom level 12 provides good neighborhood-level view
- AdvancedMarker supports custom HTML content for rich markers

---

### Issue #3: MS-01.03 - MARTA Transit Real-Time Overlay

**Milestone**: MS-01.03
**Dependencies**: Issue #2 (MS-01.02)
**Estimated Complexity**: High
**Labels**: `feature`, `transit`, `animation`, `api-integration`, `milestone-01`, `phase-1`

#### Objective
Integrate real-time MARTA bus and train data with smoothly animated markers using Framer Motion.

#### Required Tools & Packages
- ✅ **SWR** (data fetching with 5-second auto-refresh)
- ✅ **Framer Motion** (smooth marker animations)
- ✅ **gtfs-realtime-bindings** (parse MARTA protobuf data)
- ❌ **NOT react-spring** (causes conflicts with Google Maps)
- ❌ **NOT axios** (use native fetch API)
- ❌ **NOT polling with setInterval** (use SWR's built-in refresh)

#### Implementation Steps

1. **Install additional dependencies**:
```bash
pnpm add gtfs-realtime-bindings
```

2. **Create transit API route** (`src/app/api/transit/route.ts`):
```typescript
import { NextResponse } from 'next/server';
import GtfsRealtimeBindings from 'gtfs-realtime-bindings';

const MARTA_BUS_URL = 'http://developer.itsmarta.com/BRDRestService/RestBusRealTimeService/GetAllBus';
const MARTA_TRAIN_URL = 'http://developer.itsmarta.com/RealtimeTrain/RestServiceNextTrain/GetRealtimeArrivals';

export async function GET() {
  try {
    // Fetch bus data
    const busResponse = await fetch(MARTA_BUS_URL, {
      headers: { 'X-API-Key': process.env.MTAK || '' }
    });
    const busData = await busResponse.json();

    // Fetch train data
    const trainResponse = await fetch(MARTA_TRAIN_URL, {
      headers: { 'X-API-Key': process.env.MARTA_TRAIN_API_KEY || '' }
    });
    const trainData = await trainResponse.json();

    return NextResponse.json({
      buses: busData || [],
      trains: trainData || [],
      timestamp: new Date().toISOString()
    });
  } catch (error) {
    console.error('Transit API error:', error);
    return NextResponse.json({ buses: [], trains: [], error: 'Failed to fetch transit data' }, { status: 500 });
  }
}
```

3. **Create AnimatedTransitMarker component** (`src/components/AnimatedTransitMarker.tsx`):
```typescript
'use client';

import { motion } from 'framer-motion';
import { AdvancedMarker } from '@vis.gl/react-google-maps';
import { useEffect, useState } from 'react';

interface AnimatedTransitMarkerProps {
  position: { lat: number; lng: number };
  type: 'bus' | 'train';
  vehicleId: string;
  route?: string;
}

export default function AnimatedTransitMarker({ position, type, vehicleId, route }: AnimatedTransitMarkerProps) {
  const [currentPosition, setCurrentPosition] = useState(position);

  useEffect(() => {
    setCurrentPosition(position);
  }, [position]);

  const icon = type === 'bus' ? (
    <div className="bg-yellow-400 text-black px-2 py-1 rounded-full text-xs font-bold border-2 border-yellow-600">
      🚌 {route}
    </div>
  ) : (
    <div className="bg-red-600 text-white px-2 py-1 rounded-full text-xs font-bold border-2 border-red-800">
      🚇 {route}
    </div>
  );

  return (
    <AdvancedMarker position={currentPosition}>
      <motion.div
        animate={{ scale: [1, 1.1, 1] }}
        transition={{ duration: 2, repeat: Infinity, ease: "easeInOut" }}
      >
        {icon}
      </motion.div>
    </AdvancedMarker>
  );
}
```

4. **Create transit hook** (`src/lib/hooks/useTransitData.ts`):
```typescript
import useSWR from 'swr';

interface TransitData {
  buses: Array<{
    VEHICLE: string;
    LATITUDE: string;
    LONGITUDE: string;
    ROUTE: string;
  }>;
  trains: Array<{
    VEHICLE: string;
    LATITUDE: string;
    LONGITUDE: string;
    LINE: string;
  }>;
}

const fetcher = (url: string) => fetch(url).then(res => res.json());

export function useTransitData() {
  const { data, error, isLoading } = useSWR<TransitData>(
    '/api/transit',
    fetcher,
    {
      refreshInterval: 5000, // Refresh every 5 seconds
      revalidateOnFocus: false,
      dedupingInterval: 4000
    }
  );

  return {
    buses: data?.buses || [],
    trains: data?.trains || [],
    isLoading,
    error
  };
}
```

5. **Update MapView to include transit markers** (`src/components/MapView.tsx`):
```typescript
'use client';

import { APIProvider, Map, AdvancedMarker } from '@vis.gl/react-google-maps';
import AnimatedTransitMarker from './AnimatedTransitMarker';
import { useTransitData } from '@/lib/hooks/useTransitData';

const ATLANTA_CENTER = { lat: 33.7490, lng: -84.3880 };
const STADIUM_LOCATION = { lat: 33.755487, lng: -84.400993 };

export default function MapView() {
  const apiKey = process.env.NEXT_PUBLIC_GMAK || '';
  const { buses, trains, isLoading } = useTransitData();

  return (
    <APIProvider apiKey={apiKey}>
      <div className="w-full h-screen relative">
        <Map
          defaultCenter={ATLANTA_CENTER}
          defaultZoom={12}
          mapId="fifa-navigator-map"
        >
          {/* Stadium marker */}
          <AdvancedMarker position={STADIUM_LOCATION}>
            <div className="bg-red-600 text-white px-3 py-2 rounded-lg shadow-lg font-bold">
              Mercedes-Benz Stadium
            </div>
          </AdvancedMarker>

          {/* Bus markers */}
          {buses.map((bus) => (
            <AnimatedTransitMarker
              key={bus.VEHICLE}
              position={{
                lat: parseFloat(bus.LATITUDE),
                lng: parseFloat(bus.LONGITUDE)
              }}
              type="bus"
              vehicleId={bus.VEHICLE}
              route={bus.ROUTE}
            />
          ))}

          {/* Train markers */}
          {trains.map((train) => (
            <AnimatedTransitMarker
              key={train.VEHICLE}
              position={{
                lat: parseFloat(train.LATITUDE),
                lng: parseFloat(train.LONGITUDE)
              }}
              type="train"
              vehicleId={train.VEHICLE}
              route={train.LINE}
            />
          ))}
        </Map>

        {/* Loading indicator */}
        {isLoading && (
          <div className="absolute top-4 right-4 bg-white px-4 py-2 rounded-lg shadow-lg">
            Loading transit data...
          </div>
        )}
      </div>
    </APIProvider>
  );
}
```

6. **Add transit types** (`src/types/index.ts`):
```typescript
export interface Location {
  lat: number;
  lng: number;
}

export interface TransitVehicle {
  vehicleId: string;
  location: Location;
  route: string;
  type: 'bus' | 'train';
  heading?: number;
}
```

#### Success Criteria
- [ ] Bus markers appear on map with yellow styling
- [ ] Train markers appear on map with red styling
- [ ] Markers update every 5 seconds automatically
- [ ] Markers animate smoothly between position updates (no jumping)
- [ ] SWR deduplication prevents excessive API calls
- [ ] Loading indicator shows during initial fetch
- [ ] No console errors from Framer Motion or Google Maps
- [ ] API route returns data in expected format
- [ ] Markers display route numbers/names

#### Anti-Patterns to Avoid
- ❌ **Using react-spring for animation** - Conflicts with Google Maps, use Framer Motion
- ❌ **Using setInterval for polling** - Use SWR's refreshInterval instead
- ❌ **Making API calls from client components** - Use API routes for MARTA calls
- ❌ **Not parsing LATITUDE/LONGITUDE strings** - MARTA returns strings, convert to numbers
- ❌ **Animating position directly** - Animate scale/opacity, not lat/lng coordinates

#### Environment Variables
```bash
# MARTA Bus API Key
MTAK=your_marta_bus_api_key

# MARTA Train API Key
MARTA_TRAIN_API_KEY=your_marta_train_api_key

# Get keys from: http://www.itsmarta.com/app-developer-resources.aspx
```

#### Testing Commands
```bash
# Start dev server
pnpm dev

# Test API route directly
curl http://localhost:3000/api/transit

# Check SWR behavior in browser DevTools:
# 1. Network tab - should see requests every 5 seconds
# 2. Components tab - check SWR cache
# 3. Console - verify no errors

# Verify animation
# 1. Open browser
# 2. Watch markers for 10 seconds
# 3. Markers should move smoothly, not jump
```

#### Reference Files
- `src/app/api/transit/route.ts` - Transit data API
- `src/components/AnimatedTransitMarker.tsx` - Animated marker component
- `src/lib/hooks/useTransitData.ts` - SWR hook for transit data
- `src/components/MapView.tsx` - Updated map with transit overlay
- `src/types/index.ts` - Transit type definitions

#### Additional Notes
- MARTA API may have rate limits - SWR's deduplication helps
- Consider adding error boundaries for API failures
- Future enhancement: Add direction arrows based on heading
- Consider clustering markers when zoomed out (future milestone)

---

### Issue #4: MS-01.04 - FIFA Event & Venue Data (Static JSON)

**Milestone**: MS-01.04
**Dependencies**: Issue #2 (MS-01.02)
**Estimated Complexity**: Medium
**Labels**: `feature`, `data`, `ui`, `milestone-01`, `phase-1`

#### Objective
Display FIFA match schedules and venue information from static JSON files with mobile-responsive sidebar.

#### Required Tools & Packages
- ✅ **Static JSON files** (no database needed)
- ✅ **Next.js API Routes** (server-side file reading)
- ✅ **Tailwind CSS** (responsive sidebar styling)
- ❌ **NO Prisma** (not needed until Phase 2)
- ❌ **NO database packages** (Phase 1 is database-free)
- ❌ **NO external CMS** (static files only)

#### Implementation Steps

1. **Create events data file** (`src/data/events.json`):
```json
{
  "events": [
    {
      "id": "fifa-2026-atl-01",
      "title": "FIFA World Cup 2026 - Match 1",
      "date": "2026-06-15T14:00:00Z",
      "venue": "mercedes-benz-stadium",
      "teams": {
        "home": "TBD",
        "away": "TBD"
      },
      "category": "group-stage"
    },
    {
      "id": "fifa-2026-atl-02",
      "title": "FIFA World Cup 2026 - Match 2",
      "date": "2026-06-18T19:00:00Z",
      "venue": "mercedes-benz-stadium",
      "teams": {
        "home": "TBD",
        "away": "TBD"
      },
      "category": "group-stage"
    }
  ]
}
```

2. **Create venues data file** (`src/data/venues.json`):
```json
{
  "venues": [
    {
      "id": "mercedes-benz-stadium",
      "name": "Mercedes-Benz Stadium",
      "location": {
        "lat": 33.755487,
        "lng": -84.400993
      },
      "address": "1 AMB Drive NW, Atlanta, GA 30313",
      "capacity": 71000,
      "amenities": ["wifi", "concessions", "accessible"]
    }
  ]
}
```

3. **Create events API route** (`src/app/api/events/route.ts`):
```typescript
import { NextResponse } from 'next/server';
import { promises as fs } from 'fs';
import path from 'path';

export async function GET() {
  try {
    const filePath = path.join(process.cwd(), 'src/data/events.json');
    const fileContents = await fs.readFile(filePath, 'utf8');
    const data = JSON.parse(fileContents);

    return NextResponse.json(data);
  } catch (error) {
    console.error('Error reading events:', error);
    return NextResponse.json({ events: [] }, { status: 500 });
  }
}
```

4. **Create venues API route** (`src/app/api/venues/route.ts`):
```typescript
import { NextResponse } from 'next/server';
import { promises as fs } from 'fs';
import path from 'path';

export async function GET() {
  try {
    const filePath = path.join(process.cwd(), 'src/data/venues.json');
    const fileContents = await fs.readFile(filePath, 'utf8');
    const data = JSON.parse(fileContents);

    return NextResponse.json(data);
  } catch (error) {
    console.error('Error reading venues:', error);
    return NextResponse.json({ venues: [] }, { status: 500 });
  }
}
```

5. **Create EventCard component** (`src/components/EventCard.tsx`):
```typescript
'use client';

interface EventCardProps {
  event: {
    id: string;
    title: string;
    date: string;
    teams: {
      home: string;
      away: string;
    };
    category: string;
  };
  onClick: () => void;
}

export default function EventCard({ event, onClick }: EventCardProps) {
  const formattedDate = new Date(event.date).toLocaleDateString('en-US', {
    month: 'short',
    day: 'numeric',
    year: 'numeric',
    hour: 'numeric',
    minute: '2-digit'
  });

  return (
    <button
      onClick={onClick}
      className="w-full text-left p-4 bg-white rounded-lg shadow hover:shadow-lg transition-shadow border border-gray-200"
    >
      <div className="flex flex-col gap-2">
        <h3 className="font-bold text-lg text-gray-900">{event.title}</h3>
        <p className="text-sm text-gray-600">{formattedDate}</p>
        <div className="flex items-center justify-between text-sm">
          <span className="font-semibold">{event.teams.home}</span>
          <span className="text-gray-400">vs</span>
          <span className="font-semibold">{event.teams.away}</span>
        </div>
        <span className="inline-block px-2 py-1 text-xs bg-blue-100 text-blue-800 rounded w-fit">
          {event.category.replace('-', ' ').toUpperCase()}
        </span>
      </div>
    </button>
  );
}
```

6. **Create EventList component** (`src/components/EventList.tsx`):
```typescript
'use client';

import { useState, useEffect } from 'react';
import EventCard from './EventCard';

interface Event {
  id: string;
  title: string;
  date: string;
  venue: string;
  teams: {
    home: string;
    away: string;
  };
  category: string;
}

interface EventListProps {
  onEventClick: (venueId: string) => void;
}

export default function EventList({ onEventClick }: EventListProps) {
  const [events, setEvents] = useState<Event[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/events')
      .then(res => res.json())
      .then(data => {
        setEvents(data.events || []);
        setLoading(false);
      })
      .catch(err => {
        console.error('Failed to load events:', err);
        setLoading(false);
      });
  }, []);

  if (loading) {
    return (
      <div className="p-4 text-center text-gray-600">
        Loading events...
      </div>
    );
  }

  if (events.length === 0) {
    return (
      <div className="p-4 text-center text-gray-600">
        No events scheduled
      </div>
    );
  }

  return (
    <div className="flex flex-col gap-3 p-4">
      <h2 className="text-2xl font-bold text-gray-900 mb-2">FIFA Matches</h2>
      {events.map(event => (
        <EventCard
          key={event.id}
          event={event}
          onClick={() => onEventClick(event.venue)}
        />
      ))}
    </div>
  );
}
```

7. **Update MapView with sidebar** (`src/components/MapView.tsx`):
```typescript
'use client';

import { useState } from 'react';
import { APIProvider, Map, AdvancedMarker } from '@vis.gl/react-google-maps';
import EventList from './EventList';

const ATLANTA_CENTER = { lat: 33.7490, lng: -84.3880 };
const STADIUM_LOCATION = { lat: 33.755487, lng: -84.400993 };

export default function MapView() {
  const apiKey = process.env.NEXT_PUBLIC_GMAK || '';
  const [selectedVenue, setSelectedVenue] = useState<string | null>(null);

  const handleEventClick = (venueId: string) => {
    setSelectedVenue(venueId);
    // Future: pan map to venue location
  };

  return (
    <APIProvider apiKey={apiKey}>
      <div className="flex h-screen">
        {/* Sidebar - responsive */}
        <aside className="w-full md:w-96 lg:w-[28rem] overflow-y-auto bg-gray-50 border-r border-gray-200">
          <EventList onEventClick={handleEventClick} />
        </aside>

        {/* Map */}
        <div className="flex-1">
          <Map
            defaultCenter={ATLANTA_CENTER}
            defaultZoom={12}
            mapId="fifa-navigator-map"
          >
            <AdvancedMarker position={STADIUM_LOCATION}>
              <div className="bg-red-600 text-white px-3 py-2 rounded-lg shadow-lg font-bold">
                Mercedes-Benz Stadium
              </div>
            </AdvancedMarker>
          </Map>
        </div>
      </div>
    </APIProvider>
  );
}
```

8. **Add event types** (`src/types/index.ts`):
```typescript
export interface Event {
  id: string;
  title: string;
  date: string;
  venue: string;
  teams: {
    home: string;
    away: string;
  };
  category: string;
}

export interface Venue {
  id: string;
  name: string;
  location: {
    lat: number;
    lng: number;
  };
  address: string;
  capacity: number;
  amenities: string[];
}
```

#### Success Criteria
- [ ] Events display in sidebar with proper formatting
- [ ] Event cards show title, date, teams, and category
- [ ] Clicking event card highlights associated venue (future enhancement)
- [ ] Sidebar is responsive (full-width mobile, fixed-width desktop)
- [ ] API routes return JSON data successfully
- [ ] Date formatting is human-readable
- [ ] Empty state shows when no events exist
- [ ] Loading state displays during fetch

#### Anti-Patterns to Avoid
- ❌ **Installing database packages** - Use static JSON files only
- ❌ **Making API calls to external services** - Data is local
- ❌ **Using custom CSS files** - Use Tailwind utilities
- ❌ **Hardcoding events in components** - Read from JSON files
- ❌ **Not handling loading/error states** - Always include fallbacks

#### Environment Variables
None required for this milestone (using static files).

#### Testing Commands
```bash
# Start dev server
pnpm dev

# Test API routes directly
curl http://localhost:3000/api/events
curl http://localhost:3000/api/venues

# Verify in browser:
# 1. Check sidebar displays events
# 2. Test responsive layout (resize window)
# 3. Click event cards
# 4. Verify date formatting
```

#### Reference Files
- `src/data/events.json` - Static event data
- `src/data/venues.json` - Static venue data
- `src/app/api/events/route.ts` - Events API
- `src/app/api/venues/route.ts` - Venues API
- `src/components/EventCard.tsx` - Individual event display
- `src/components/EventList.tsx` - Event list container
- `src/components/MapView.tsx` - Updated with sidebar
- `src/types/index.ts` - Event and venue types

#### Additional Notes
- Static JSON allows easy migration to database in Phase 2
- Consider adding filtering by date/category (future enhancement)
- Venue click-to-pan feature can be added in next iteration
- Event data can be replaced with real FIFA 2026 schedule when available

---

### Issue #5: MS-02.01 - Bilingual Support (English/Spanish)

**Milestone**: MS-02.01
**Dependencies**: Issue #1 (MS-01.01)
**Estimated Complexity**: Medium
**Labels**: `feature`, `i18n`, `milestone-02`, `phase-1`

#### Objective
Implement English/Spanish language switching using Next.js 16 built-in internationalization with persistent user preference.

#### Required Tools & Packages
- ✅ **Next.js 16 App Router i18n** (built-in, no extra packages)
- ✅ **Static JSON translation files** (simple key-value)
- ✅ **localStorage** (persist language preference)
- ❌ **NOT next-i18next** (complex, unnecessary)
- ❌ **NOT react-intl** (overkill for 2 languages)
- ❌ **NOT i18next library** (too complex)

#### Implementation Steps

1. **Create translation files**:

**English** (`src/i18n/en.json`):
```json
{
  "app": {
    "title": "FIFA Navigator",
    "tagline": "Your guide to FIFA World Cup 2026 in Atlanta"
  },
  "navigation": {
    "map": "Map",
    "events": "Events",
    "favorites": "Favorites",
    "language": "Language"
  },
  "events": {
    "title": "FIFA Matches",
    "noEvents": "No events scheduled",
    "loading": "Loading events...",
    "category": {
      "groupStage": "Group Stage",
      "knockout": "Knockout Round",
      "semifinal": "Semi-Final",
      "final": "Final"
    }
  },
  "transit": {
    "loading": "Loading transit data...",
    "bus": "Bus",
    "train": "Train"
  },
  "venue": {
    "capacity": "Capacity",
    "address": "Address"
  }
}
```

**Spanish** (`src/i18n/es.json`):
```json
{
  "app": {
    "title": "Navegador FIFA",
    "tagline": "Tu guía para la Copa Mundial FIFA 2026 en Atlanta"
  },
  "navigation": {
    "map": "Mapa",
    "events": "Eventos",
    "favorites": "Favoritos",
    "language": "Idioma"
  },
  "events": {
    "title": "Partidos FIFA",
    "noEvents": "No hay eventos programados",
    "loading": "Cargando eventos...",
    "category": {
      "groupStage": "Fase de Grupos",
      "knockout": "Ronda Eliminatoria",
      "semifinal": "Semifinal",
      "final": "Final"
    }
  },
  "transit": {
    "loading": "Cargando datos de tránsito...",
    "bus": "Autobús",
    "train": "Tren"
  },
  "venue": {
    "capacity": "Capacidad",
    "address": "Dirección"
  }
}
```

2. **Create i18n configuration** (`src/lib/i18n.ts`):
```typescript
import enTranslations from '@/i18n/en.json';
import esTranslations from '@/i18n/es.json';

export type Locale = 'en' | 'es';

export const locales: Locale[] = ['en', 'es'];
export const defaultLocale: Locale = 'en';

export const translations = {
  en: enTranslations,
  es: esTranslations
};

export function getTranslations(locale: Locale) {
  return translations[locale] || translations[defaultLocale];
}

export function getLocaleName(locale: Locale): string {
  const names: Record<Locale, string> = {
    en: 'English',
    es: 'Español'
  };
  return names[locale];
}
```

3. **Create language-aware layout** (`src/app/[lang]/layout.tsx`):
```typescript
import type { Metadata } from 'next';
import { Inter } from 'next/font/google';
import { locales, type Locale } from '@/lib/i18n';
import '../globals.css';

const inter = Inter({ subsets: ['latin'] });

export async function generateStaticParams() {
  return locales.map((lang) => ({ lang }));
}

export const metadata: Metadata = {
  title: 'FIFA Navigator',
  description: 'Your guide to FIFA World Cup 2026 in Atlanta',
};

export default function LocaleLayout({
  children,
  params
}: {
  children: React.ReactNode;
  params: { lang: Locale };
}) {
  return (
    <html lang={params.lang}>
      <body className={inter.className}>{children}</body>
    </html>
  );
}
```

4. **Create language-aware page** (`src/app/[lang]/page.tsx`):
```typescript
import MapView from '@/components/MapView';
import { getTranslations, type Locale } from '@/lib/i18n';

export default function Home({ params }: { params: { lang: Locale } }) {
  const t = getTranslations(params.lang);

  return (
    <main className="relative">
      <MapView lang={params.lang} translations={t} />
    </main>
  );
}
```

5. **Create LanguageSwitcher component** (`src/components/LanguageSwitcher.tsx`):
```typescript
'use client';

import { useRouter, usePathname } from 'next/navigation';
import { locales, getLocaleName, type Locale } from '@/lib/i18n';
import { useEffect, useState } from 'react';

export default function LanguageSwitcher({ currentLang }: { currentLang: Locale }) {
  const router = useRouter();
  const pathname = usePathname();
  const [isOpen, setIsOpen] = useState(false);

  // Persist language preference
  useEffect(() => {
    localStorage.setItem('preferredLanguage', currentLang);
  }, [currentLang]);

  const switchLanguage = (newLang: Locale) => {
    const newPathname = pathname.replace(`/${currentLang}`, `/${newLang}`);
    router.push(newPathname);
    setIsOpen(false);
  };

  return (
    <div className="relative">
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="px-4 py-2 bg-white rounded-lg shadow hover:shadow-lg transition-shadow border border-gray-200 font-medium"
      >
        🌐 {getLocaleName(currentLang)}
      </button>

      {isOpen && (
        <div className="absolute top-full mt-2 right-0 bg-white rounded-lg shadow-lg border border-gray-200 min-w-[120px] z-50">
          {locales.map((locale) => (
            <button
              key={locale}
              onClick={() => switchLanguage(locale)}
              className={`w-full text-left px-4 py-2 hover:bg-gray-100 first:rounded-t-lg last:rounded-b-lg ${
                locale === currentLang ? 'bg-blue-50 font-semibold' : ''
              }`}
            >
              {getLocaleName(locale)}
            </button>
          ))}
        </div>
      )}
    </div>
  );
}
```

6. **Create root page redirect** (`src/app/page.tsx`):
```typescript
import { redirect } from 'next/navigation';
import { defaultLocale } from '@/lib/i18n';

export default function RootPage() {
  // Check localStorage for preferred language (client-side)
  // For now, redirect to default locale
  redirect(`/${defaultLocale}`);
}
```

7. **Update MapView to use translations** (`src/components/MapView.tsx`):
```typescript
'use client';

import { type Locale } from '@/lib/i18n';
import LanguageSwitcher from './LanguageSwitcher';

interface MapViewProps {
  lang: Locale;
  translations: any;
}

export default function MapView({ lang, translations }: MapViewProps) {
  const t = translations;

  return (
    <div className="flex h-screen">
      {/* Language switcher in top-right corner */}
      <div className="absolute top-4 right-4 z-10">
        <LanguageSwitcher currentLang={lang} />
      </div>

      {/* Sidebar with translated content */}
      <aside className="w-full md:w-96 overflow-y-auto bg-gray-50">
        <div className="p-4">
          <h1 className="text-3xl font-bold mb-2">{t.app.title}</h1>
          <p className="text-gray-600 mb-4">{t.app.tagline}</p>
          <h2 className="text-2xl font-bold mb-3">{t.events.title}</h2>
          {/* Event list components will use translations */}
        </div>
      </aside>

      {/* Map component */}
      {/* ... rest of map code ... */}
    </div>
  );
}
```

#### Success Criteria
- [ ] Language switcher appears in top-right corner
- [ ] Clicking switcher shows EN/ES options
- [ ] Switching language updates all UI text immediately
- [ ] URL changes to reflect language (e.g., /en → /es)
- [ ] Language preference persists in localStorage
- [ ] All user-facing text uses translation keys (no hardcoded strings)
- [ ] Translation files are valid JSON with no missing keys
- [ ] Page generates static params for both languages

#### Anti-Patterns to Avoid
- ❌ **Installing next-i18next package** - Use Next.js built-in i18n
- ❌ **Hardcoding strings in components** - Always use translation keys
- ❌ **Mixing languages in same translation file** - Separate files per language
- ❌ **Not persisting preference** - Use localStorage
- ❌ **Complex i18n libraries** - Keep it simple with JSON files

#### Environment Variables
None required for this milestone.

#### Testing Commands
```bash
# Start dev server
pnpm dev

# Test language routes
curl http://localhost:3000/en
curl http://localhost:3000/es

# Verify in browser:
# 1. Navigate to /en - verify English text
# 2. Click language switcher
# 3. Select "Español"
# 4. Verify URL changes to /es
# 5. Verify all text is in Spanish
# 6. Refresh page - language should persist
```

#### Reference Files
- `src/i18n/en.json` - English translations
- `src/i18n/es.json` - Spanish translations
- `src/lib/i18n.ts` - i18n configuration
- `src/app/[lang]/layout.tsx` - Language-aware layout
- `src/app/[lang]/page.tsx` - Language-aware page
- `src/components/LanguageSwitcher.tsx` - Language switcher UI
- `src/app/page.tsx` - Root redirect

#### Additional Notes
- Future enhancement: Detect browser language and auto-redirect
- Consider adding more languages (German, Korean) in Phase 2
- Translation keys should be descriptive (e.g., `events.title` not `e1`)
- Use nested objects for organization in translation files

---

### Issue #6: MS-02.02 - User Favorites & Preferences

**Milestone**: MS-02.02
**Dependencies**: Issue #4 (MS-01.04)
**Estimated Complexity**: Low
**Labels**: `feature`, `user-preferences`, `milestone-02`, `phase-1`

#### Objective
Implement favorite venues functionality using localStorage with custom React hooks (no database needed in Phase 1).

#### Required Tools & Packages
- ✅ **localStorage** (browser storage for favorites)
- ✅ **Custom React hooks** (useLocalStorage, useFavorites)
- ✅ **Tailwind CSS** (styling for favorite buttons)
- ❌ **NO database** (Phase 1 is client-side only)
- ❌ **NO authentication** (not needed yet)
- ❌ **NO third-party storage libraries** (use native localStorage)

#### Implementation Steps

1. **Create useLocalStorage hook** (`src/lib/hooks/useLocalStorage.ts`):
```typescript
import { useState, useEffect } from 'react';

export function useLocalStorage<T>(key: string, initialValue: T): [T, (value: T) => void] {
  const [storedValue, setStoredValue] = useState<T>(initialValue);

  // Load from localStorage on mount
  useEffect(() => {
    try {
      const item = window.localStorage.getItem(key);
      if (item) {
        setStoredValue(JSON.parse(item));
      }
    } catch (error) {
      console.error(`Error loading ${key} from localStorage:`, error);
    }
  }, [key]);

  // Save to localStorage when value changes
  const setValue = (value: T) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(`Error saving ${key} to localStorage:`, error);
    }
  };

  return [storedValue, setValue];
}
```

2. **Create useFavorites hook** (`src/lib/hooks/useFavorites.ts`):
```typescript
import { useLocalStorage } from './useLocalStorage';

export interface Favorite {
  id: string;
  type: 'venue' | 'event';
  addedAt: string;
}

export function useFavorites() {
  const [favorites, setFavorites] = useLocalStorage<Favorite[]>('fifa-favorites', []);

  const addFavorite = (id: string, type: 'venue' | 'event') => {
    const exists = favorites.some(fav => fav.id === id);
    if (!exists) {
      setFavorites([...favorites, { id, type, addedAt: new Date().toISOString() }]);
    }
  };

  const removeFavorite = (id: string) => {
    setFavorites(favorites.filter(fav => fav.id !== id));
  };

  const isFavorite = (id: string) => {
    return favorites.some(fav => fav.id === id);
  };

  const toggleFavorite = (id: string, type: 'venue' | 'event') => {
    if (isFavorite(id)) {
      removeFavorite(id);
    } else {
      addFavorite(id, type);
    }
  };

  return {
    favorites,
    addFavorite,
    removeFavorite,
    isFavorite,
    toggleFavorite
  };
}
```

3. **Create FavoriteButton component** (`src/components/FavoriteButton.tsx`):
```typescript
'use client';

import { useFavorites } from '@/lib/hooks/useFavorites';

interface FavoriteButtonProps {
  itemId: string;
  itemType: 'venue' | 'event';
  size?: 'sm' | 'md' | 'lg';
}

export default function FavoriteButton({ itemId, itemType, size = 'md' }: FavoriteButtonProps) {
  const { isFavorite, toggleFavorite } = useFavorites();
  const favorited = isFavorite(itemId);

  const sizeClasses = {
    sm: 'w-6 h-6 text-xs',
    md: 'w-8 h-8 text-sm',
    lg: 'w-10 h-10 text-base'
  };

  return (
    <button
      onClick={(e) => {
        e.stopPropagation();
        toggleFavorite(itemId, itemType);
      }}
      className={`${sizeClasses[size]} rounded-full flex items-center justify-center transition-all ${
        favorited
          ? 'bg-red-500 text-white hover:bg-red-600'
          : 'bg-gray-200 text-gray-400 hover:bg-gray-300'
      }`}
      aria-label={favorited ? 'Remove from favorites' : 'Add to favorites'}
    >
      {favorited ? '❤️' : '🤍'}
    </button>
  );
}
```

4. **Update EventCard with favorite button** (`src/components/EventCard.tsx`):
```typescript
'use client';

import FavoriteButton from './FavoriteButton';

interface EventCardProps {
  event: {
    id: string;
    title: string;
    date: string;
    teams: {
      home: string;
      away: string;
    };
    category: string;
  };
  onClick: () => void;
}

export default function EventCard({ event, onClick }: EventCardProps) {
  const formattedDate = new Date(event.date).toLocaleDateString('en-US', {
    month: 'short',
    day: 'numeric',
    year: 'numeric',
    hour: 'numeric',
    minute: '2-digit'
  });

  return (
    <div className="relative">
      {/* Favorite button in top-right corner */}
      <div className="absolute top-2 right-2 z-10">
        <FavoriteButton itemId={event.id} itemType="event" size="sm" />
      </div>

      <button
        onClick={onClick}
        className="w-full text-left p-4 bg-white rounded-lg shadow hover:shadow-lg transition-shadow border border-gray-200"
      >
        <div className="flex flex-col gap-2 pr-10">
          <h3 className="font-bold text-lg text-gray-900">{event.title}</h3>
          <p className="text-sm text-gray-600">{formattedDate}</p>
          <div className="flex items-center justify-between text-sm">
            <span className="font-semibold">{event.teams.home}</span>
            <span className="text-gray-400">vs</span>
            <span className="font-semibold">{event.teams.away}</span>
          </div>
          <span className="inline-block px-2 py-1 text-xs bg-blue-100 text-blue-800 rounded w-fit">
            {event.category.replace('-', ' ').toUpperCase()}
          </span>
        </div>
      </button>
    </div>
  );
}
```

5. **Create FavoritesList component** (`src/components/FavoritesList.tsx`):
```typescript
'use client';

import { useFavorites } from '@/lib/hooks/useFavorites';
import { useState, useEffect } from 'react';

interface FavoritesListProps {
  translations: any;
}

export default function FavoritesList({ translations }: FavoritesListProps) {
  const { favorites } = useFavorites();
  const [events, setEvents] = useState<any[]>([]);

  useEffect(() => {
    // Load event details for favorited items
    fetch('/api/events')
      .then(res => res.json())
      .then(data => {
        const favoriteIds = favorites.map(fav => fav.id);
        const favoriteEvents = data.events.filter((event: any) =>
          favoriteIds.includes(event.id)
        );
        setEvents(favoriteEvents);
      });
  }, [favorites]);

  if (favorites.length === 0) {
    return (
      <div className="p-4 text-center text-gray-600">
        {translations?.favorites?.noFavorites || 'No favorites yet'}
      </div>
    );
  }

  return (
    <div className="p-4">
      <h2 className="text-2xl font-bold mb-4">
        {translations?.navigation?.favorites || 'Favorites'} ({favorites.length})
      </h2>
      <div className="flex flex-col gap-3">
        {events.map(event => (
          <div key={event.id} className="p-3 bg-white rounded-lg shadow border border-gray-200">
            <h3 className="font-bold">{event.title}</h3>
            <p className="text-sm text-gray-600">
              {new Date(event.date).toLocaleDateString()}
            </p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

6. **Add favorites to navigation** (`src/components/MapView.tsx`):
```typescript
'use client';

import { useState } from 'react';
import EventList from './EventList';
import FavoritesList from './FavoritesList';

export default function MapView({ lang, translations }: any) {
  const [activeTab, setActiveTab] = useState<'events' | 'favorites'>('events');

  return (
    <div className="flex h-screen">
      <aside className="w-full md:w-96 overflow-y-auto bg-gray-50">
        {/* Tab navigation */}
        <div className="flex border-b border-gray-200 bg-white sticky top-0 z-10">
          <button
            onClick={() => setActiveTab('events')}
            className={`flex-1 px-4 py-3 font-medium ${
              activeTab === 'events'
                ? 'border-b-2 border-blue-600 text-blue-600'
                : 'text-gray-600 hover:text-gray-900'
            }`}
          >
            {translations.navigation.events}
          </button>
          <button
            onClick={() => setActiveTab('favorites')}
            className={`flex-1 px-4 py-3 font-medium ${
              activeTab === 'favorites'
                ? 'border-b-2 border-blue-600 text-blue-600'
                : 'text-gray-600 hover:text-gray-900'
            }`}
          >
            {translations.navigation.favorites}
          </button>
        </div>

        {/* Content */}
        {activeTab === 'events' ? (
          <EventList onEventClick={() => {}} />
        ) : (
          <FavoritesList translations={translations} />
        )}
      </aside>

      {/* Map component */}
      {/* ... */}
    </div>
  );
}
```

7. **Update translation files** to include favorites text:

Add to `src/i18n/en.json`:
```json
{
  "favorites": {
    "noFavorites": "No favorites yet",
    "addToFavorites": "Add to favorites",
    "removeFromFavorites": "Remove from favorites"
  }
}
```

Add to `src/i18n/es.json`:
```json
{
  "favorites": {
    "noFavorites": "Aún no hay favoritos",
    "addToFavorites": "Agregar a favoritos",
    "removeFromFavorites": "Eliminar de favoritos"
  }
}
```

#### Success Criteria
- [ ] Favorite button appears on each event card
- [ ] Clicking heart toggles favorite status (empty ↔ filled)
- [ ] Favorites persist after page refresh
- [ ] Favorites tab shows all favorited items
- [ ] Empty state displays when no favorites
- [ ] Favorite count displays accurately
- [ ] localStorage contains favorites array
- [ ] No errors in console related to storage

#### Anti-Patterns to Avoid
- ❌ **Using database or API for favorites** - Phase 1 uses localStorage only
- ❌ **Not handling localStorage quota exceeded** - Add error handling
- ❌ **Storing entire event objects** - Store IDs only, fetch details when needed
- ❌ **Not cleaning up event listeners** - Use proper React hooks
- ❌ **Hardcoding storage keys** - Use constants or config

#### Environment Variables
None required for this milestone.

#### Testing Commands
```bash
# Start dev server
pnpm dev

# Test in browser:
# 1. Navigate to /en
# 2. Click heart icon on event card
# 3. Verify heart fills in
# 4. Switch to Favorites tab
# 5. Verify event appears in favorites
# 6. Refresh page
# 7. Verify favorite persists
# 8. Click heart again to remove
# 9. Verify removed from favorites list

# Check localStorage in DevTools:
# Application → Local Storage → http://localhost:3000
# Key: fifa-favorites
# Value: [{"id":"...","type":"event","addedAt":"..."}]
```

#### Reference Files
- `src/lib/hooks/useLocalStorage.ts` - Generic localStorage hook
- `src/lib/hooks/useFavorites.ts` - Favorites-specific hook
- `src/components/FavoriteButton.tsx` - Favorite toggle button
- `src/components/EventCard.tsx` - Updated with favorite button
- `src/components/FavoritesList.tsx` - Favorites display
- `src/components/MapView.tsx` - Updated with tabs
- `src/i18n/en.json` - English translations for favorites
- `src/i18n/es.json` - Spanish translations for favorites

#### Additional Notes
- Phase 2 migration path: Replace localStorage with database queries
- Consider adding favorite venues (not just events) in future
- localStorage has ~5-10MB limit - sufficient for favorites
- Consider adding export/import favorites feature later

---

### Issue #7: MS-03.01 - Vercel Deployment

**Milestone**: MS-03.01
**Dependencies**: All Phase 1 milestones (Issues #1-6)
**Estimated Complexity**: Low
**Labels**: `deployment`, `infrastructure`, `milestone-03`, `phase-1`

#### Objective
Configure and deploy Next.js application to Vercel with proper environment variables and production optimizations.

#### Required Tools & Packages
- ✅ **Vercel CLI** (deployment tool)
- ✅ **GitHub integration** (auto-deploy on push)
- ✅ **Environment variables** (set in Vercel dashboard)
- ❌ **NO custom webpack config** (use Next.js defaults)
- ❌ **NO Docker** (Vercel handles containers)
- ❌ **NO custom server** (use Next.js built-in server)

#### Implementation Steps

1. **Create .env.example file** (document required variables):
```bash
# Google Maps API Key (public)
NEXT_PUBLIC_GMAK=your_google_maps_api_key_here

# MARTA Bus API Key (server-side)
MTAK=your_marta_bus_api_key_here

# MARTA Train API Key (server-side)
MARTA_TRAIN_API_KEY=your_marta_train_api_key_here

# Stadium location (public)
NEXT_PUBLIC_STADIUM_NAME="Mercedes-Benz Stadium"
NEXT_PUBLIC_STADIUM_LAT="33.755487"
NEXT_PUBLIC_STADIUM_LNG="-84.400993"
```

2. **Update .gitignore** (ensure secrets not committed):
```
# Environment variables
.env
.env*.local
.env.production

# Vercel
.vercel
```

3. **Create vercel.json** (deployment configuration):
```json
{
  "buildCommand": "pnpm build",
  "devCommand": "pnpm dev",
  "installCommand": "pnpm install",
  "framework": "nextjs",
  "regions": ["iad1"],
  "env": {
    "NEXT_PUBLIC_GMAK": "@next-public-gmak",
    "MTAK": "@mtak",
    "MARTA_TRAIN_API_KEY": "@marta-train-api-key"
  }
}
```

4. **Verify build locally**:
```bash
# Clean install
rm -rf node_modules .next
pnpm install

# Build for production
pnpm build

# Test production build
pnpm start

# Check for build errors
# - No TypeScript errors
# - No missing dependencies
# - No database connections (Phase 1)
```

5. **Install Vercel CLI** (if not already installed):
```bash
pnpm add -g vercel
```

6. **Login to Vercel**:
```bash
vercel login
```

7. **Deploy to preview**:
```bash
# Initial deployment (creates project)
vercel

# Follow prompts:
# - Link to existing project? No
# - Project name: fifa-navigator
# - Directory: ./
# - Override settings? No
```

8. **Set environment variables in Vercel**:

**Option A: Via Dashboard**
1. Go to vercel.com → Projects → fifa-navigator
2. Settings → Environment Variables
3. Add each variable:
   - `NEXT_PUBLIC_GMAK` = your_google_maps_key (Production, Preview, Development)
   - `MTAK` = your_marta_bus_key (Production, Preview, Development)
   - `MARTA_TRAIN_API_KEY` = your_marta_train_key (Production, Preview, Development)

**Option B: Via CLI**
```bash
vercel env add NEXT_PUBLIC_GMAK production
# Paste value when prompted

vercel env add MTAK production
# Paste value when prompted

vercel env add MARTA_TRAIN_API_KEY production
# Paste value when prompted
```

9. **Deploy to production**:
```bash
vercel --prod
```

10. **Setup GitHub integration** (for auto-deploy):
1. Go to Vercel Dashboard → Project Settings → Git
2. Connect GitHub repository
3. Enable automatic deployments:
   - Production branch: `main`
   - Deploy preview branches: Yes
   - Auto-deploy commits: Yes

11. **Verify production deployment**:
```bash
# Get production URL
vercel --prod

# Test in browser:
# 1. Visit production URL
# 2. Verify map loads
# 3. Test language switching
# 4. Check favorites persist
# 5. Verify transit data loads
# 6. Test on mobile device
```

#### Success Criteria
- [ ] `pnpm build` completes without errors locally
- [ ] Application deploys successfully to Vercel
- [ ] Production URL is accessible (https://fifa-navigator.vercel.app)
- [ ] All environment variables set correctly in Vercel
- [ ] Map loads with correct API key
- [ ] MARTA transit data displays
- [ ] Language switching works
- [ ] Favorites persist in production
- [ ] No console errors in production
- [ ] Mobile responsiveness verified
- [ ] GitHub integration enabled (auto-deploy on push)

#### Anti-Patterns to Avoid
- ❌ **Committing .env files** - Always use .gitignore
- ❌ **Using database in Phase 1** - Vercel will fail if database packages present
- ❌ **Custom webpack config** - Stick to Next.js defaults
- ❌ **Hardcoded API keys** - Always use environment variables
- ❌ **Not testing build locally first** - Always verify locally before deploying

#### Environment Variables
```bash
# Production environment variables (set in Vercel Dashboard)

# Google Maps API (must start with NEXT_PUBLIC_ for client access)
NEXT_PUBLIC_GMAK=AIzaSy...actual_key_here

# MARTA APIs (server-side only, no NEXT_PUBLIC_ prefix)
MTAK=your_marta_bus_api_key
MARTA_TRAIN_API_KEY=your_marta_train_api_key

# Stadium location (public)
NEXT_PUBLIC_STADIUM_NAME="Mercedes-Benz Stadium"
NEXT_PUBLIC_STADIUM_LAT="33.755487"
NEXT_PUBLIC_STADIUM_LNG="-84.400993"

# Optional: Analytics, monitoring, etc.
# NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
```

#### Testing Commands
```bash
# Local build verification
pnpm build
pnpm start

# Deploy to preview
vercel

# Deploy to production
vercel --prod

# Check deployment logs
vercel logs [deployment-url]

# Test production build
curl -I https://fifa-navigator.vercel.app

# Verify environment variables
vercel env ls
```

#### Reference Files
- `.env.example` - Environment variable template
- `vercel.json` - Vercel deployment config
- `.gitignore` - Git ignore rules
- `package.json` - Build scripts
- `next.config.ts` - Next.js configuration

#### Additional Notes
- Vercel automatically detects Next.js and configures build
- Edge functions are used for API routes (fast, globally distributed)
- Static pages are served from CDN
- Preview deployments created for each PR
- Production domain can be customized in Vercel settings
- Consider adding custom domain in Phase 2

#### Troubleshooting Common Issues

**Build fails with "Module not found"**
- Run `pnpm install` locally first
- Check `package.json` has all dependencies
- Verify `pnpm-lock.yaml` is committed

**Map doesn't load in production**
- Check `NEXT_PUBLIC_GMAK` environment variable is set
- Verify API key has correct restrictions in Google Cloud Console
- Check browser console for API errors

**Transit data not loading**
- Verify `MTAK` and `MARTA_TRAIN_API_KEY` are set
- Check API route logs in Vercel dashboard
- Verify MARTA APIs are accessible from Vercel servers

**Favorites not persisting**
- localStorage requires HTTPS in production (Vercel provides this)
- Check browser's localStorage settings
- Verify no console errors related to storage

---

## Phase 2: Database Features (Future Milestones)

---

### Issue #8: MS-03.01-P2 - Database Integration (Phase 2)

**Milestone**: MS-03.01-P2 (Phase 2 only)
**Dependencies**: All Phase 1 issues complete, Vercel Postgres provisioned
**Estimated Complexity**: High
**Labels**: `database`, `migration`, `milestone-03`, `phase-2`

#### Objective
Migrate from localStorage and static JSON to Vercel Postgres with Prisma ORM for persistent data storage.

#### Required Tools & Packages
- ✅ **Prisma** (type-safe ORM)
- ✅ **@prisma/client** (database client)
- ✅ **@vercel/postgres** (connection pooling)
- ✅ **Vercel Postgres** (managed database)
- ❌ **NOT SQLite** (doesn't work on Vercel serverless)
- ❌ **NOT Turso/libsql** (too complex for students)
- ❌ **NOT MongoDB** (use PostgreSQL for consistency)

#### Implementation Steps

1. **Install database packages**:
```bash
pnpm add @prisma/client @vercel/postgres
pnpm add -D prisma
```

2. **Initialize Prisma**:
```bash
npx prisma init
```

3. **Create Prisma schema** (`prisma/schema.prisma`):
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("POSTGRES_PRISMA_URL")
}

model User {
  id        String   @id @default(cuid())
  createdAt DateTime @default(now())

  // User preferences
  language  String   @default("en")

  // Relations
  favorites UserFavorite[]
}

model UserFavorite {
  id        String   @id @default(cuid())
  userId    String
  itemId    String   // Event or venue ID
  itemType  String   // "event" or "venue"
  addedAt   DateTime @default(now())

  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([userId, itemId])
  @@index([userId])
}

model Event {
  id        String   @id
  title     String
  date      DateTime
  venueId   String
  homeTeam  String
  awayTeam  String
  category  String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  venue     Venue    @relation(fields: [venueId], references: [id])

  @@index([date])
  @@index([venueId])
}

model Venue {
  id        String   @id
  name      String
  latitude  Float
  longitude Float
  address   String
  capacity  Int?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  events    Event[]
}
```

4. **Provision Vercel Postgres**:
```bash
# Via Vercel Dashboard:
# 1. Go to project → Storage → Create Database
# 2. Choose "Postgres"
# 3. Select region (same as deployment region)
# 4. Create database

# Or via CLI:
vercel env pull .env.local
# This downloads POSTGRES_PRISMA_URL and other DB vars
```

5. **Run migrations**:
```bash
# Create migration
npx prisma migrate dev --name init

# Generate Prisma Client
npx prisma generate
```

6. **Create Prisma client singleton** (`src/lib/prisma.ts`):
```typescript
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({
    log: ['query', 'error', 'warn'],
  });

if (process.env.NODE_ENV !== 'production') {
  globalForPrisma.prisma = prisma;
}
```

7. **Migrate events API to database** (`src/app/api/events/route.ts`):
```typescript
import { NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';

export async function GET() {
  try {
    const events = await prisma.event.findMany({
      include: {
        venue: true
      },
      orderBy: {
        date: 'asc'
      }
    });

    return NextResponse.json({ events });
  } catch (error) {
    console.error('Database error:', error);
    return NextResponse.json({ events: [] }, { status: 500 });
  }
}
```

8. **Create seed script** (`prisma/seed.ts`):
```typescript
import { PrismaClient } from '@prisma/client';
import eventsData from '../src/data/events.json';
import venuesData from '../src/data/venues.json';

const prisma = new PrismaClient();

async function main() {
  // Seed venues
  for (const venue of venuesData.venues) {
    await prisma.venue.upsert({
      where: { id: venue.id },
      update: {},
      create: {
        id: venue.id,
        name: venue.name,
        latitude: venue.location.lat,
        longitude: venue.location.lng,
        address: venue.address,
        capacity: venue.capacity
      }
    });
  }

  // Seed events
  for (const event of eventsData.events) {
    await prisma.event.upsert({
      where: { id: event.id },
      update: {},
      create: {
        id: event.id,
        title: event.title,
        date: new Date(event.date),
        venueId: event.venue,
        homeTeam: event.teams.home,
        awayTeam: event.teams.away,
        category: event.category
      }
    });
  }

  console.log('✅ Database seeded successfully');
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

9. **Update package.json** with seed script:
```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "db:migrate": "prisma migrate dev",
    "db:generate": "prisma generate",
    "db:seed": "tsx prisma/seed.ts",
    "db:studio": "prisma studio"
  }
}
```

10. **Deploy database migrations to production**:
```bash
# Push schema to production database
npx prisma migrate deploy

# Seed production database
vercel env pull .env.production
pnpm db:seed
```

#### Success Criteria
- [ ] Prisma schema defined for all models
- [ ] Database migrations run successfully
- [ ] Seed script populates events and venues
- [ ] API routes return data from database (not JSON files)
- [ ] Favorites persist across devices (database-backed)
- [ ] No connection pooling errors
- [ ] Prisma Studio opens and shows data
- [ ] Production database deployed and seeded
- [ ] No performance degradation vs. localStorage

#### Anti-Patterns to Avoid
- ❌ **Using SQLite in production** - Vercel requires PostgreSQL
- ❌ **Not using connection pooling** - Use @vercel/postgres
- ❌ **Storing sensitive data without encryption** - Add encryption for user data
- ❌ **Not handling database errors** - Add proper error boundaries
- ❌ **Creating new Prisma Client on each request** - Use singleton pattern

#### Environment Variables
```bash
# Vercel Postgres (automatically set by Vercel)
POSTGRES_PRISMA_URL=postgres://...
POSTGRES_URL_NON_POOLING=postgres://...

# For local development
DATABASE_URL=postgresql://user:password@localhost:5432/fifa_navigator
```

#### Testing Commands
```bash
# Generate Prisma Client
pnpm db:generate

# Run migrations
pnpm db:migrate

# Seed database
pnpm db:seed

# Open Prisma Studio
pnpm db:studio

# Test API with database
curl http://localhost:3000/api/events

# Verify database connection
npx prisma db pull
```

#### Reference Files
- `prisma/schema.prisma` - Database schema
- `prisma/seed.ts` - Seed script
- `src/lib/prisma.ts` - Prisma client singleton
- `src/app/api/events/route.ts` - Updated events API
- `package.json` - Database scripts

#### Additional Notes
- This milestone should only be tackled after Phase 1 is fully working
- Consider adding authentication (Auth.js) in Phase 3
- Database enables multi-device favorites sync
- Future: Add caching layer (Redis) for high-traffic scenarios

---

## Summary: Milestone Dependencies

```
Phase 1 (No Database):
Issue #1 (MS-01.01) → Issue #2 (MS-01.02) → Issue #3 (MS-01.03)
                   → Issue #4 (MS-01.04) → Issue #5 (MS-02.01)
                   → Issue #6 (MS-02.02) → Issue #7 (MS-03.01)

Phase 2 (With Database):
Issue #7 (MS-03.01) → Issue #8 (MS-03.01-P2)
```

**Critical Path**: #1 → #2 → #7 (minimal viable product)
**Full Phase 1**: All issues #1-7 must complete before #8

---

## Agent Execution Guidelines

For autonomous agents executing these issues:

1. **Read the entire issue before starting** - Understand objective, tools, and anti-patterns
2. **Follow implementation steps sequentially** - Don't skip steps
3. **Verify each step** - Use testing commands to confirm success
4. **Check success criteria** - All must pass before marking complete
5. **Avoid anti-patterns** - Explicitly check "What NOT to do"
6. **Report blockers** - If stuck, document exact error and context
7. **Request clarification** - If instructions unclear, ask before proceeding
8. **Update documentation** - Keep README and comments in sync

---

**Document**: GitHub Issue Breakdown for Autonomous Execution
**Created**: 2025-11-05
**Based on**: GEMINI_MILESTONE_TOOLS.md
**Tested With**: N/A (new document)
**Next Review**: After first agent execution
