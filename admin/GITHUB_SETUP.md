# Netlify Functions + GitHub App Setup Guide

## Step 1: Create a GitHub OAuth App

1. Go to GitHub Settings → Developer settings → OAuth Apps
2. Click "New OAuth App" and fill in:
   - **Application name**: PanelFRAK CMS
   - **Homepage URL**: `https://precious-panda-b1eea2.netlify.app`
   - **Authorization callback URL**: `https://precious-panda-b1eea2.netlify.app/.netlify/functions/auth`
3. Copy your **Client ID** and **Client Secret**

## Step 2: Set Up Netlify Environment Variables

1. Go to Netlify → Site settings → Build & deploy → Environment
2. Add environment variables:
   - `GITHUB_APP_CLIENT_ID`: Your GitHub app Client ID
   - `GITHUB_APP_CLIENT_SECRET`: Your GitHub app Client Secret

## Step 3: Update config.yml

Replace in [admin/config.yml](admin/config.yml):
```yaml
repo: YOUR_GITHUB_USERNAME/YOUR_REPO_NAME
site_domain: precious-panda-b1eea2.netlify.app
```

## Step 4: Install Dependencies

Add to [package.json](package.json):
```json
{
  "devDependencies": {
    "@netlify/functions": "^2.1.0",
    "@octokit/rest": "^20.0.0",
    "node-fetch": "^2.7.0"
  }
}
```

Or run:
```bash
npm install --save-dev @netlify/functions @octokit/rest node-fetch
```

## Step 5: Deploy

1. Push your changes to your repo
2. Netlify will automatically build and deploy
3. The Netlify functions will be created at:
   - `https://precious-panda-b1eea2.netlify.app/.netlify/functions/auth`
   - `https://precious-panda-b1eea2.netlify.app/.netlify/functions/git`

## Step 6: Test the CMS

1. Go to `https://precious-panda-b1eea2.netlify.app/admin/`
2. Click "Login with GitHub"
3. Authorize the app
4. Start managing content!

## How It Works

1. **auth.ts** - Handles GitHub OAuth flow
   - User clicks "Login with GitHub" in Sveltia CMS
   - Function exchanges authorization code for access token
   - Token is stored in browser session

2. **git.ts** - Handles Git operations
   - Sveltia CMS uses token to make requests
   - Function proxies requests to GitHub API
   - Creates commits with content changes

## Troubleshooting

- **"Invalid credentials"**: Check `GITHUB_APP_CLIENT_ID` and `GITHUB_APP_CLIENT_SECRET`
- **"Permission denied"**: Ensure GitHub app has necessary permissions (repo access)
- **Functions not loading**: Verify `functions` directory in netlify.toml
- **Callback URL mismatch**: Ensure GitHub app callback matches your Netlify domain exactly
