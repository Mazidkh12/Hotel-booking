# Vercel Deployment Guide

This guide will help you deploy your hotel booking application to Vercel.

## Project Structure

This is a monorepo with two separate applications:
- **Client**: React + Vite frontend
- **Server**: Node.js + Express backend

## Deployment Steps

### 1. Prepare Your Repository

Make sure your code is pushed to a Git repository (GitHub, GitLab, or Bitbucket).

```bash
git add .
git commit -m "Prepare for Vercel deployment"
git push origin main
```

### 2. Deploy Backend (Server)

#### Option A: Deploy via Vercel Dashboard

1. Go to [vercel.com](https://vercel.com) and sign in
2. Click "Add New Project"
3. Import your repository
4. Configure the project:
   - **Framework Preset**: Other
   - **Root Directory**: `server`
   - **Build Command**: Leave empty
   - **Output Directory**: Leave empty
   - **Install Command**: `npm install`

5. Add Environment Variables:
   ```
   MONGODB_URI=your_mongodb_connection_string
   CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
   CLERK_SECRET_KEY=your_clerk_secret_key
   CLERK_WEBHOOK_SECRET=your_clerk_webhook_secret
   CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name
   CLOUDINARY_API_KEY=your_cloudinary_api_key
   CLOUDINARY_API_SECRET=your_cloudinary_api_secret
   SENDER_EMAIL=your_sender_email
   SMTP_USER=your_smtp_user
   SMTP_PASS=your_smtp_password
   CURRENCY=₹
   ```

6. Click "Deploy"

#### Option B: Deploy via Vercel CLI

```bash
cd server
vercel
# Follow the prompts
# Add environment variables when prompted
```

7. After deployment, copy your backend URL (e.g., `https://your-backend.vercel.app`)

### 3. Deploy Frontend (Client)

#### Option A: Deploy via Vercel Dashboard

1. Click "Add New Project" again
2. Import the same repository
3. Configure the project:
   - **Framework Preset**: Vite
   - **Root Directory**: `client`
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
   - **Install Command**: `npm install`

4. Add Environment Variables:
   ```
   VITE_BACKEND_URL=https://your-backend.vercel.app
   VITE_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
   VITE_CURRENCY=₹
   ```

5. Click "Deploy"

#### Option B: Deploy via Vercel CLI

```bash
cd client
vercel
# Follow the prompts
# Add environment variables when prompted
```

### 4. Configure Clerk Webhook

After deploying the backend:

1. Go to [Clerk Dashboard](https://dashboard.clerk.com)
2. Navigate to Webhooks
3. Add a new endpoint: `https://your-backend.vercel.app/api/clerk`
4. Subscribe to events:
   - `user.created`
   - `user.updated`
   - `user.deleted`
5. Copy the signing secret and update your backend environment variable `CLERK_WEBHOOK_SECRET`

### 5. Update CORS Settings (if needed)

If you encounter CORS issues, update `server/server.js`:

```javascript
app.use(cors({
  origin: ['https://your-frontend.vercel.app', 'http://localhost:5173'],
  credentials: true
}))
```

## Environment Variables Checklist

### Backend (.env)
- [ ] MONGODB_URI
- [ ] CLERK_PUBLISHABLE_KEY
- [ ] CLERK_SECRET_KEY
- [ ] CLERK_WEBHOOK_SECRET
- [ ] CLOUDINARY_CLOUD_NAME
- [ ] CLOUDINARY_API_KEY
- [ ] CLOUDINARY_API_SECRET
- [ ] SENDER_EMAIL
- [ ] SMTP_USER
- [ ] SMTP_PASS
- [ ] CURRENCY

### Frontend (.env)
- [ ] VITE_BACKEND_URL
- [ ] VITE_CLERK_PUBLISHABLE_KEY
- [ ] VITE_CURRENCY

## Troubleshooting

### Backend Issues

**Problem**: API routes not working
- **Solution**: Make sure `server/vercel.json` is configured correctly
- Check that all routes start with `/api/`

**Problem**: Database connection fails
- **Solution**: Verify MongoDB URI is correct and IP whitelist includes `0.0.0.0/0` in MongoDB Atlas

**Problem**: Clerk authentication fails
- **Solution**: Verify all Clerk environment variables are set correctly

### Frontend Issues

**Problem**: API calls fail
- **Solution**: Check that `VITE_BACKEND_URL` points to your deployed backend
- Verify CORS is configured correctly on the backend

**Problem**: Build fails
- **Solution**: Run `npm run build` locally to check for errors
- Fix any TypeScript or linting errors

**Problem**: Routes not working (404 on refresh)
- **Solution**: Verify `client/vercel.json` has the rewrite rule

## Post-Deployment

1. Test all features:
   - [ ] User registration/login
   - [ ] Hotel registration
   - [ ] Room creation
   - [ ] Room booking
   - [ ] View bookings
   - [ ] Dashboard analytics

2. Monitor logs in Vercel dashboard for any errors

3. Set up custom domain (optional):
   - Go to Project Settings > Domains
   - Add your custom domain
   - Update DNS records as instructed

## Continuous Deployment

Vercel automatically redeploys when you push to your main branch:

```bash
git add .
git commit -m "Update feature"
git push origin main
# Vercel will automatically deploy
```

## Important Notes

- Vercel serverless functions have a 10-second timeout on the free plan
- File uploads are limited to 4.5MB on Vercel (use Cloudinary for images)
- MongoDB Atlas free tier has connection limits
- Keep your environment variables secure and never commit them to Git

## Support

If you encounter issues:
1. Check Vercel deployment logs
2. Check browser console for frontend errors
3. Check Network tab for API call failures
4. Verify all environment variables are set correctly
