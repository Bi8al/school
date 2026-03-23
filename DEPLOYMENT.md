# Vercel Deployment Guide

## Project Structure
```
school/
├── backend/                 # Express API server
│   ├── index.js
│   └── package.json
├── frontend/                # React frontend
│   ├── src/
│   ├── public/
│   └── package.json
├── vercel.json             # Vercel configuration
├── .vercelignore           # Files to ignore during deployment
└── package.json            # Root monorepo configuration
```

## Setup Steps

### 1. Prepare Backend for Vercel
Update your `backend/package.json` build script:
```json
{
  "scripts": {
    "start": "node index.js",
    "build": "npm install"
  }
}
```

### 2. Update Environment Variables
In Vercel Dashboard:
1. Go to Settings → Environment Variables
2. Add your environment variables:
   - `MONGODB_URI`: Your MongoDB connection string
   - `NODE_ENV`: Set to `production`
   - Any other API keys needed

### 3. Update Frontend API Calls
In your React components, update API calls to use relative URLs or environment variables:
```javascript
// Instead of: http://localhost:5000/api/...
// Use: /api/...
const response = await fetch('/api/endpoint');
```

Or use environment variables:
```javascript
const API_URL = process.env.REACT_APP_API_URL || '/api';
```

### 4. Build Frontend
Make sure frontend builds correctly:
```bash
npm run build --prefix frontend
```

This should create a `frontend/build` directory.

### 5. Deploy to Vercel
Option A - Via Vercel CLI:
```bash
npm install -g vercel
vercel
```

Option B - Via Vercel Dashboard:
1. Push code to GitHub
2. Go to vercel.com
3. Click "New Project"
4. Import your GitHub repository
5. Configure environment variables
6. Deploy

## How It Works
- **Frontend routes** (`/`, `/about`, etc.) are served from `frontend/build`
- **API routes** (`/api/*`) are proxied to your Express backend
- Backend runs as Node.js serverless functions on Vercel

## Important Notes
- Vercel serverless functions have a **5-minute timeout** (12s for hobby plans)
- If you need websockets or long-running connections, consider alternatives like Railway or Render
- Database connections should use connection pooling (e.g., MongoDB Atlas)
- Environment variables must be set in Vercel dashboard

## Troubleshooting

### Backend not found
- Ensure `backend/index.js` exists and exports an Express app
- Check that `vercel.json` has correct paths

### Frontend build fails
- Run `npm run build --prefix frontend` locally to debug
- Check that `package.json` has proper build script

### API calls return 404
- Ensure frontend uses `/api/*` paths, not `http://localhost:5000/*`
- Check CORS settings in Express backend

### Environment variables not working
- Add variables in Vercel Dashboard (not `.env` file)
- Redeploy after adding variables
