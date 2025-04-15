# Strategic Steel Purchasing Dashboard - Deployment Guide

This comprehensive guide details the deployment process for the Strategic Steel Purchasing Dashboard application, covering local development setup, Supabase configuration, production deployment, and maintenance procedures.

## Table of Contents
1. [Local Development Environment](#1-local-development-environment)
2. [Supabase Configuration](#2-supabase-configuration)
3. [Production Deployment](#3-production-deployment)
4. [Testing Procedures](#4-testing-procedures)
5. [Maintenance and Updates](#5-maintenance-and-updates)
6. [Troubleshooting](#6-troubleshooting)

## 1. Local Development Environment

### 1.1 Prerequisites

Before setting up the local development environment, ensure you have the following installed:

- **Node.js** (v18.0.0 or later)
- **npm** (v8.0.0 or later)
- **Git** (v2.30.0 or later)
- **Cursor AI** (latest version)

### 1.2 Clone and Setup Repository

```bash
# Clone the repository
git clone https://github.com/your-organization/steel-purchasing-dashboard.git
cd steel-purchasing-dashboard

# Install dependencies
npm install
```

### 1.3 Environment Variables Setup

Create a `.env.local` file in the project root with the following variables:

```
# Supabase Configuration
NEXT_PUBLIC_SUPABASE_URL=your-supabase-project-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-supabase-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-supabase-service-role-key

# External APIs
NEXT_PUBLIC_MAPBOX_TOKEN=your-mapbox-token
MEPS_API_KEY=your-meps-api-key
RISK_API_KEY=your-risk-api-key

# Application Settings
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

### 1.4 Running the Development Server

```bash
# Start the development server
npm run dev
```

The application will be available at `http://localhost:3000`.

### 1.5 Using Cursor AI for Development

Cursor AI can significantly enhance your development workflow:

1. **Open the project in Cursor AI**:
   ```bash
   cd steel-purchasing-dashboard
   cursor .
   ```

2. **Leverage Cursor AI features**:
   - Use `Cmd+K` (or `Ctrl+K` on Windows/Linux) for inline code generation
   - Use `Cmd+L` (or `Ctrl+L` on Windows/Linux) to open the chat interface
   - Use the agent mode for complex tasks by selecting 'agent' in Composer

3. **Recommended Cursor AI prompts for this project**:
   - "Generate a new component for displaying supplier details"
   - "Create a function to fetch and process market index data"
   - "Refactor this component to improve performance"
   - "Debug this API route that's returning an error"

## 2. Supabase Configuration

### 2.1 Creating a Supabase Project

1. Sign up or log in at [Supabase](https://supabase.com/)
2. Click "New Project" and fill in the details:
   - Name: `steel-purchasing-dashboard`
   - Database Password: Create a strong password
   - Region: Choose the region closest to your users
   - Pricing Plan: Select appropriate plan (Free tier works for development)

3. Wait for the project to be created (usually takes 1-2 minutes)

### 2.2 Database Schema Setup

1. Navigate to the SQL Editor in your Supabase dashboard
2. Create the database tables using the SQL script from the implementation guide
3. Alternatively, use the migration files in the project:

```bash
# Install Supabase CLI if not already installed
npm install -g supabase

# Login to Supabase
supabase login

# Link your project
supabase link --project-ref your-project-ref

# Push the migrations
supabase db push
```

### 2.3 Authentication Configuration

1. In the Supabase dashboard, go to Authentication > Settings
2. Configure the following:
   - Enable Email/Password sign-in
   - Set up password policies
   - Configure email templates
   - Set up redirect URLs (for production)

3. For additional providers (optional):
   - Go to Authentication > Providers
   - Enable and configure Google, Microsoft, or other providers

### 2.4 Storage Configuration

1. Go to Storage in the Supabase dashboard
2. Create the following buckets:
   - `documents` (for storing uploaded documents)
   - `avatars` (for user profile images)

3. Configure bucket permissions:
   - For `documents`: Authenticated users can read, only authorized roles can upload
   - For `avatars`: Authenticated users can read and upload their own avatars

### 2.5 Row Level Security (RLS) Policies

Implement RLS policies for each table to ensure data security:

```sql
-- Example for suppliers table
ALTER TABLE suppliers ENABLE ROW LEVEL SECURITY;

-- Allow read access for all authenticated users
CREATE POLICY "Allow read access for authenticated users" ON suppliers
  FOR SELECT USING (auth.role() = 'authenticated');

-- Allow write access only for admin users
CREATE POLICY "Allow write access for admin users" ON suppliers
  FOR INSERT USING (auth.role() = 'authenticated' AND auth.email() IN (SELECT email FROM admin_users));

CREATE POLICY "Allow update access for admin users" ON suppliers
  FOR UPDATE USING (auth.role() = 'authenticated' AND auth.email() IN (SELECT email FROM admin_users));

CREATE POLICY "Allow delete access for admin users" ON suppliers
  FOR DELETE USING (auth.role() = 'authenticated' AND auth.email() IN (SELECT email FROM admin_users));
```

Repeat similar policies for all tables in the database.

### 2.6 API Keys and Security

1. Locate your Supabase API keys in Project Settings > API
2. Never expose the `service_role` key in client-side code
3. Use the `anon` key for client-side operations
4. Set up appropriate CORS settings in Project Settings > API > CORS

## 3. Production Deployment

### 3.1 Preparing for Deployment

1. Ensure all environment variables are properly set
2. Run the build process locally to check for any issues:

```bash
npm run build
```

3. Verify that all tests pass:

```bash
npm run test
```

### 3.2 Deployment to Vercel

Vercel is the recommended hosting platform for Next.js applications:

1. Install Vercel CLI:
```bash
npm install -g vercel
```

2. Login to Vercel:
```bash
vercel login
```

3. Deploy to Vercel preview environment:
```bash
vercel
```

4. Deploy to production:
```bash
vercel --prod
```

5. Configure environment variables in the Vercel dashboard:
   - Go to your project settings
   - Navigate to the Environment Variables section
   - Add all the environment variables from your `.env.local` file

### 3.3 Alternative Deployment Options

#### Netlify Deployment

1. Install Netlify CLI:
```bash
npm install -g netlify-cli
```

2. Login to Netlify:
```bash
netlify login
```

3. Deploy to Netlify:
```bash
netlify deploy
```

4. Deploy to production:
```bash
netlify deploy --prod
```

#### Docker Deployment

1. Create a `Dockerfile` in the project root:

```dockerfile
FROM node:18-alpine AS base

# Install dependencies only when needed
FROM base AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production image, copy all the files and run next
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000

CMD ["node", "server.js"]
```

2. Create a `.dockerignore` file:

```
node_modules
.next
.git
```

3. Build and run the Docker image:

```bash
# Build the Docker image
docker build -t steel-purchasing-dashboard .

# Run the container
docker run -p 3000:3000 -e NEXT_PUBLIC_SUPABASE_URL=your-url -e NEXT_PUBLIC_SUPABASE_ANON_KEY=your-key steel-purchasing-dashboard
```

### 3.4 Custom Domain Setup

#### Vercel Custom Domain

1. In the Vercel dashboard, go to your project settings
2. Navigate to the Domains section
3. Add your custom domain (e.g., `dashboard.yourcompany.com`)
4. Follow the DNS configuration instructions provided by Vercel

#### SSL Certificate

Vercel and Netlify automatically provision SSL certificates for your domains. For custom deployments:

1. Use Let's Encrypt to generate free SSL certificates
2. Configure your web server (Nginx, Apache) to use the certificates
3. Set up auto-renewal for the certificates

### 3.5 Continuous Integration/Continuous Deployment (CI/CD)

Set up a CI/CD pipeline for automated testing and deployment:

#### GitHub Actions Example

Create `.github/workflows/main.yml`:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Use Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
      - name: Run tests
        run: npm test

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
          vercel-args: '--prod'
```

## 4. Testing Procedures

### 4.1 Automated Testing

Run the automated test suite to ensure all components and functionality work as expected:

```bash
# Run all tests
npm test

# Run tests with coverage report
npm test -- --coverage

# Run specific test file
npm test -- src/components/charts/__tests__/LineChart.test.tsx
```

### 4.2 Manual Testing Checklist

Before each deployment, perform the following manual tests:

1. **Authentication**
   - [ ] User registration works
   - [ ] User login works
   - [ ] Password reset functionality works
   - [ ] User logout works
   - [ ] Protected routes are properly secured

2. **Dashboard Modules**
   - [ ] Market Intelligence module loads and displays data
   - [ ] Supplier Management module shows supplier information
   - [ ] Spend Analysis module displays charts and data
   - [ ] Component Analysis module shows part information
   - [ ] Cost Simulation module allows scenario creation

3. **Data Operations**
   - [ ] Data can be created, read, updated, and deleted (CRUD)
   - [ ] Filters and sorting work correctly
   - [ ] Search functionality works
   - [ ] Data export works

4. **Responsive Design**
   - [ ] Application works on desktop browsers
   - [ ] Application works on tablet devices
   - [ ] Application works on mobile devices
   - [ ] Charts and maps are responsive

5. **Performance**
   - [ ] Pages load within acceptable time (< 3 seconds)
   - [ ] Charts render quickly
   - [ ] API responses are fast
   - [ ] No memory leaks during extended use

### 4.3 User Acceptance Testing (UAT)

Conduct UAT with stakeholders before final deployment:

1. Prepare a UAT environment with real data
2. Create test scenarios for each user role
3. Document feedback and issues
4. Prioritize and address issues before production deployment

## 5. Maintenance and Updates

### 5.1 Regular Maintenance Tasks

Schedule the following maintenance tasks:

1. **Weekly**
   - Check application logs for errors
   - Monitor database performance
   - Update market indices data

2. **Monthly**
   - Apply security updates
   - Review and optimize database queries
   - Check for outdated dependencies

3. **Quarterly**
   - Perform full backup of database
   - Review and update documentation
   - Conduct performance optimization

### 5.2 Updating Dependencies

Regularly update project dependencies to ensure security and performance:

```bash
# Check for outdated dependencies
npm outdated

# Update dependencies
npm update

# Update a specific package
npm update @supabase/supabase-js

# Install security updates
npm audit fix
```

### 5.3 Database Maintenance

Perform regular database maintenance:

1. **Backups**
   - Supabase provides automatic backups
   - Set up additional backup procedures if needed

2. **Performance Optimization**
   - Monitor query performance
   - Add indexes for frequently queried columns
   - Optimize slow queries

3. **Data Archiving**
   - Archive old data that's no longer frequently accessed
   - Create archive tables for historical data

### 5.4 Monitoring and Alerts

Set up monitoring and alerts for the application:

1. **Application Monitoring**
   - Implement error tracking with Sentry or similar service
   - Set up performance monitoring with New Relic or similar

2. **Server Monitoring**
   - Monitor server resources (CPU, memory, disk)
   - Set up alerts for resource thresholds

3. **Database Monitoring**
   - Monitor database connections
   - Track query performance
   - Set up alerts for slow queries

## 6. Troubleshooting

### 6.1 Common Issues and Solutions

#### Authentication Issues

**Issue**: Users cannot log in
**Solution**:
- Check Supabase authentication settings
- Verify email confirmation settings
- Check for CORS issues
- Ensure environment variables are correctly set

#### Database Connection Issues

**Issue**: Cannot connect to Supabase
**Solution**:
- Verify Supabase URL and API keys
- Check network connectivity
- Ensure IP restrictions are properly configured
- Verify RLS policies

#### Deployment Issues

**Issue**: Build fails during deployment
**Solution**:
- Check build logs for specific errors
- Verify all dependencies are installed
- Ensure environment variables are set in the deployment platform
- Check for TypeScript errors

#### Performance Issues

**Issue**: Slow page loading
**Solution**:
- Implement code splitting
- Optimize database queries
- Use caching for frequently accessed data
- Optimize images and assets

### 6.2 Logging and Debugging

Implement comprehensive logging for easier debugging:

```typescript
// src/lib/logger.ts
export const logger = {
  info: (message: string, data?: any) => {
    console.log(`[INFO] ${message}`, data || '');
    // In production, send to logging service
  },
  error: (message: string, error?: any) => {
    console.error(`[ERROR] ${message}`, error || '');
    // In production, send to error tracking service
  },
  warn: (message: string, data?: any) => {
    console.warn(`[WARN] ${message}`, data || '');
    // In production, send to logging service
  }
};
```

### 6.3 Support Resources

- **Supabase Documentation**: [https://supabase.com/docs](https://supabase.com/docs)
- **Next.js Documentation**: [https://nextjs.org/docs](https://nextjs.org/docs)
- **Vercel Documentation**: [https://vercel.com/docs](https://vercel.com/docs)
- **Cursor AI Support**: [https://cursor.com/support](https://cursor.com/support)

## Conclusion

This deployment guide provides comprehensive instructions for setting up, deploying, and maintaining the Strategic Steel Purchasing Dashboard. By following these procedures, you can ensure a smooth deployment process and reliable operation of the application.

Remember to regularly update the application, monitor its performance, and address any issues promptly to provide the best experience for users.
