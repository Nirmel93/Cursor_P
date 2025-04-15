# Strategic Steel Purchasing Dashboard - Reference Guide

This reference guide provides quick access to key information about the Strategic Steel Purchasing Dashboard implementation.

## Table of Contents
1. [Technology Stack Reference](#technology-stack-reference)
2. [Database Schema Reference](#database-schema-reference)
3. [API Endpoints Reference](#api-endpoints-reference)
4. [Component Library Reference](#component-library-reference)
5. [Cursor AI Commands Reference](#cursor-ai-commands-reference)
6. [Environment Variables Reference](#environment-variables-reference)
7. [Common Issues and Solutions](#common-issues-and-solutions)

## Technology Stack Reference

### Core Technologies
- **Frontend Framework**: Next.js 14 (React 18)
- **Backend**: Next.js API Routes
- **Database**: PostgreSQL via Supabase
- **Authentication**: Supabase Auth
- **Styling**: Tailwind CSS
- **Visualization**: Chart.js, D3.js
- **Mapping**: Mapbox GL JS
- **Development Environment**: Cursor AI

### Key Dependencies
| Dependency | Version | Purpose |
|------------|---------|---------|
| @supabase/supabase-js | ^2.38.0 | Supabase client library |
| @supabase/auth-helpers-nextjs | ^0.8.1 | Supabase auth helpers for Next.js |
| chart.js | ^4.4.0 | Chart visualization library |
| react-chartjs-2 | ^5.2.0 | React wrapper for Chart.js |
| d3 | ^7.8.5 | Data visualization library |
| mapbox-gl | ^3.0.0 | Interactive mapping library |
| tailwindcss | ^3.4.0 | Utility-first CSS framework |
| @headlessui/react | ^1.7.17 | Unstyled UI components |
| @heroicons/react | ^2.1.0 | SVG icon components |
| date-fns | ^2.30.0 | Date utility library |
| axios | ^1.6.0 | HTTP client |

## Database Schema Reference

### Core Tables

#### `suppliers`
| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| name | TEXT | Supplier name |
| location | GEOGRAPHY(POINT) | Geographic location |
| country | TEXT | Country |
| region | TEXT | Region |
| contact_name | TEXT | Primary contact name |
| contact_email | TEXT | Contact email |
| contact_phone | TEXT | Contact phone |
| capabilities | JSONB | Array of capabilities |
| certifications | JSONB | Array of certifications |
| performance_score | DECIMAL | Overall performance score |
| quality_score | DECIMAL | Quality performance score |
| delivery_score | DECIMAL | Delivery performance score |
| is_active | BOOLEAN | Whether supplier is active |
| created_at | TIMESTAMP | Creation timestamp |
| updated_at | TIMESTAMP | Last update timestamp |

#### `parts`
can be found @C:\Users\nirme\Downloads\current_prices_rows.csv 
Structure:
part_number,description,steel_thickness_mm,forecast,qty_year,price,spend,supplier
| updated_at | TIMESTAMP | Last update timestamp |

#### `spend_data`
| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| part_id | UUID | Foreign key to parts.id |
| supplier_id | UUID | Foreign key to suppliers.id |
| date | DATE | Purchase date |
| quantity | INTEGER | Quantity purchased |
| price_per_unit | DECIMAL | Unit price |
| total_price | DECIMAL | Total price |
| currency | TEXT | Currency code |
| po_number | TEXT | Purchase order number |
| created_at | TIMESTAMP | Creation timestamp |
| updated_at | TIMESTAMP | Last update timestamp |

#### `market_indices`
| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| index_name | TEXT | Name of the index |
| date | DATE | Date of the index value |
| value | DECIMAL | Index value |
| currency | TEXT | Currency code |
| source | TEXT | Data source |
| created_at | TIMESTAMP | Creation timestamp |

#### `risk_data`
| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| country | TEXT | Country |
| region | TEXT | Region |
| risk_type | TEXT | Type of risk |
| risk_score | DECIMAL | Risk score (0-100) |
| description | TEXT | Risk description |
| date | DATE | Date of the risk assessment |
| source | TEXT | Data source |
| created_at | TIMESTAMP | Creation timestamp |

### Database Views

#### `v_supplier_spend`
Aggregated spend by supplier with metrics like total spend, unique parts, and purchase date range.

#### `v_part_spend`
Aggregated spend by part with metrics like total spend, quantity, supplier count, and price statistics.

#### `v_thickness_spend`
Aggregated spend by thickness range with part count and total spend.

#### `v_material_spend`
Aggregated spend by material grade with part count and total spend.

#### `v_time_series_spend`
Monthly time series of spend data with metrics like monthly spend, unique parts, and unique suppliers.

## API Endpoints Reference

### Authentication
- `POST /api/auth/login` - User login
- `POST /api/auth/logout` - User logout
- `GET /api/auth/user` - Get current user information

### Suppliers
- `GET /api/suppliers` - Get all suppliers
- `GET /api/suppliers/:id` - Get supplier by ID
- `POST /api/suppliers` - Create new supplier
- `PUT /api/suppliers/:id` - Update supplier
- `DELETE /api/suppliers/:id` - Delete supplier
- `GET /api/suppliers/map` - Get supplier geographical data
- `GET /api/suppliers/performance` - Get supplier performance metrics

### Parts
- `GET /api/parts` - Get all parts
- `GET /api/parts/:id` - Get part by ID
- `POST /api/parts` - Create new part
- `PUT /api/parts/:id` - Update part
- `DELETE /api/parts/:id` - Delete part
- `GET /api/parts/thickness` - Get thickness analysis
- `GET /api/parts/materials` - Get material analysis

### Spend Analysis
- `GET /api/spend/overview` - Get spend overview
- `GET /api/spend/by-supplier` - Get spend by supplier
- `GET /api/spend/by-part` - Get spend by part
- `GET /api/spend/by-time` - Get time series analysis
- `GET /api/spend/pareto` - Get ABC analysis

### Market Intelligence
- `GET /api/market/indices` - Get market indices data
- `GET /api/market/risks` - Get risk assessment data
- `GET /api/market/price-comparison` - Get internal vs. market price comparison

### Cost Simulation
- `POST /api/simulation/what-if` - Run what-if scenario
- `POST /api/simulation/cost-reduction` - Calculate cost reduction

## Component Library Reference

### Layout Components
- `DashboardLayout` - Main layout for dashboard pages
- `Sidebar` - Navigation sidebar
- `Header` - Top header with user menu

### Common Components
- `Card` - Container for dashboard content
- `Button` - Styled button component
- `Table` - Data table component
- `Pagination` - Table pagination component
- `Modal` - Modal dialog component
- `Tabs` - Tabbed interface component

### Chart Components
- `LineChart` - Line chart for time series data
- `BarChart` - Bar chart for categorical data
- `PieChart` - Pie chart for distribution data
- `ScatterChart` - Scatter plot for correlation data
- `GaugeChart` - Gauge chart for KPI visualization

### Map Components
- `SupplierMap` - Map showing supplier locations
- `RiskMap` - Map showing geopolitical risks

### Form Components
- `TextField` - Text input field
- `SelectField` - Dropdown select field
- `DatePicker` - Date selection field
- `FileUpload` - File upload component
- `SearchInput` - Search input with autocomplete

## Cursor AI Commands Reference

### General Commands
- `Cmd+K` (Mac) / `Ctrl+K` (Windows/Linux) - Open inline code generator
- `Cmd+L` (Mac) / `Ctrl+L` (Windows/Linux) - Open chat interface
- Select code + `Cmd+Shift+L` (Mac) / `Ctrl+Shift+L` (Windows/Linux) - Add code to chat context

### Useful Prompts for This Project
- "Generate a new API route for [feature]"
- "Create a React component for [feature]"
- "Write a database query to [task]"
- "Refactor this component to improve performance"
- "Add error handling to this function"
- "Create a test for this component"
- "Debug this issue: [description]"

### Agent Mode Tasks
- "Set up a new module for [feature]"
- "Implement the [feature] from requirements"
- "Create database migrations for [tables]"
- "Set up authentication with Supabase"
- "Integrate Chart.js for [visualization]"

## Environment Variables Reference

### Required Variables
- `NEXT_PUBLIC_SUPABASE_URL` - Supabase project URL
- `NEXT_PUBLIC_SUPABASE_ANON_KEY` - Supabase anonymous key
- `SUPABASE_SERVICE_ROLE_KEY` - Supabase service role key (server-side only)

### Optional Variables
- `NEXT_PUBLIC_MAPBOX_TOKEN` - Mapbox API token
- `MEPS_API_KEY` - MEPS Steel Index API key
- `RISK_API_KEY` - Risk intelligence API key
- `NEXT_PUBLIC_APP_URL` - Application URL
- `NODE_ENV` - Environment (development, production)

## Common Issues and Solutions

### Authentication Issues
- **Issue**: Users cannot log in
  - **Solution**: Check Supabase authentication settings, verify email confirmation settings, check for CORS issues

### Database Connection Issues
- **Issue**: Cannot connect to Supabase
  - **Solution**: Verify Supabase URL and API keys, check network connectivity, ensure IP restrictions are properly configured

### Deployment Issues
- **Issue**: Build fails during deployment
  - **Solution**: Check build logs for specific errors, verify all dependencies are installed, ensure environment variables are set

### Performance Issues
- **Issue**: Slow page loading
  - **Solution**: Implement code splitting, optimize database queries, use caching for frequently accessed data

### Data Visualization Issues
- **Issue**: Charts not rendering correctly
  - **Solution**: Check data format, ensure Chart.js is properly configured, verify component props

### Map Rendering Issues
- **Issue**: Maps not displaying
  - **Solution**: Verify Mapbox token, check location data format, ensure CSS height is set for container
