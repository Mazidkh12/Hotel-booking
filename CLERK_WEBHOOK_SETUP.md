# Clerk Webhook Setup Guide

## Issue
Users are not being saved to MongoDB Atlas when they sign up/login because the Clerk webhook is not properly configured.

## Solution

### 1. Check Environment Variable
Make sure your `server/.env` file has:
```
CLERK_WEBHOOK_SECRET=whsec_your_webhook_secret_here
```

### 2. Configure Clerk Webhook (in Clerk Dashboard)

1. Go to [Clerk Dashboard](https://dashboard.clerk.com)
2. Select your application
3. Navigate to **Webhooks** in the sidebar
4. Click **Add Endpoint**
5. Enter your webhook URL:
   - For local development: `http://localhost:3000/api/clerk` (use ngrok or similar for testing)
   - For production: `https://your-domain.com/api/clerk`
6. Subscribe to these events:
   - `user.created`
   - `user.updated`
   - `user.deleted`
7. Copy the **Signing Secret** (starts with `whsec_`)
8. Add it to your `server/.env` file as `CLERK_WEBHOOK_SECRET`

### 3. Testing Locally with ngrok

Since Clerk needs a public URL to send webhooks, use ngrok for local testing:

```bash
# Install ngrok if you haven't
# Then run:
ngrok http 3000

# Use the ngrok URL in Clerk webhook settings
# Example: https://abc123.ngrok.io/api/clerk
```

### 4. Restart Your Server

After adding the webhook secret:
```bash
cd server
npm run server
```

### 5. Test the Webhook

1. Sign up a new user in your app
2. Check your server console for:
   - "Webhook event received: user.created"
   - "✓ User created in database: user_xxxxx"
3. Check MongoDB Atlas to verify the user was added

## Automatic Fallback

The app now includes an automatic fallback mechanism:
- If a user logs in but doesn't exist in the database, they will be automatically created
- This happens in the authentication middleware using Clerk's API
- You'll see a console message: "✓ User auto-created in database: user_xxxxx"

This means the app will work even if webhooks aren't configured yet, but webhooks are still recommended for production.

## Troubleshooting

- **"Webhook verification failed"**: Check that your `CLERK_WEBHOOK_SECRET` matches the one in Clerk Dashboard
- **No webhook received**: Make sure your webhook URL is publicly accessible (use ngrok for local dev)
- **"User not found in database"**: The app now auto-creates users, but make sure `CLERK_SECRET_KEY` is in your `.env`
- **User still not in database**: Check server console for error messages

## Required Environment Variables

Make sure your `server/.env` has:
```
CLERK_SECRET_KEY=sk_test_your_secret_key
CLERK_WEBHOOK_SECRET=whsec_your_webhook_secret
```
