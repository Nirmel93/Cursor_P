# Strategic Steel Purchasing Dashboard - Architecture Design

## 1. Overall Application Architecture

### 1.1 Technology Stack
- **Frontend**: Next.js (React framework)
- **Backend**: Next.js API routes + Supabase
- **Database**: PostgreSQL (via Supabase)
- **Authentication**: Supabase Auth
- **Hosting**: Vercel (for Next.js) + Supabase (for backend)
- **Development Environment**: Cursor AI

### 1.2 Architecture Overview

The application will follow a modern web architecture pattern:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│                 │     │                 │     │                 │
│  Next.js        │     │  Next.js API    │     │  Supabase       │
│  Frontend       │────▶│  Routes         │────▶│  (PostgreSQL)   │
│  (React)        │     │                 │     │                 │
│                 │     │                 │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        ▲                        ▲                      ▲
        │                        │                      │
        │                        │                      │
        │                        │                      │
        ▼                        ▼                      ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│                 │     │                 │     │                 │
│  External APIs  │     │  Authentication │     │  File Storage   │
│  (Market Data)  │     │  (Supabase)     │     │  (Supabase)     │
│                 │     │                 │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

### 1.3 Key Components

1. **Frontend Layer**:
   - Next.js pages and components
   - React hooks for state management
   - Tailwind CSS for styling
   - Chart.js/D3.js for data visualization
   - Mapbox GL JS for geographical mapping

2. **API Layer**:
   - Next.js API routes for backend logic
   - Supabase client for database operations
   - External API integrations (MEPS, risk data)