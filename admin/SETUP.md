# Sveltia CMS Setup Guide for PanelFRAK

## Files Created

- `admin/config.yml` - Main Sveltia CMS configuration
- `admin/index.html` - CMS admin interface entry point

## S3-Compatible Storage Configuration

The config.yml includes S3 media library configuration. Update the following credentials:

### For AWS S3:
```yaml
bucket: your-bucket-name
region: us-east-1
access_key_id: YOUR_AWS_ACCESS_KEY
secret_access_key: YOUR_AWS_SECRET_KEY
```

### For MinIO (Self-hosted):
```yaml
bucket: your-bucket-name
region: auto
endpoint: https://minio.example.com:9000
access_key_id: YOUR_MINIO_ACCESS_KEY
secret_access_key: YOUR_MINIO_SECRET_KEY
```

### For DigitalOcean Spaces:
```yaml
bucket: your-bucket-name
region: nyc3
endpoint: https://nyc3.digitaloceanspaces.com
access_key_id: YOUR_SPACES_KEY
secret_access_key: YOUR_SPACES_SECRET
```

### For Wasabi:
```yaml
bucket: your-bucket-name
region: us-west-1
endpoint: https://s3.wasabisys.com
access_key_id: YOUR_WASABI_ACCESS_KEY
secret_access_key: YOUR_WASABI_SECRET_KEY
```

## Collections

The CMS is configured with 4 collections based on your archetypes:

1. **Pages** - Main site pages (About, Home, etc.)
2. **Blog Posts** - Posts collection with tags, categories, author, description
3. **Works** - Works/portfolio collection with genres and series support
4. **Hero Section** - Editable hero data from `data/hero.yaml`

## Deployment Requirements

1. **Git Gateway Backend** - Requires Netlify functions or similar
2. **Authentication** - Set up OAuth with your Git provider (GitHub, GitLab, Gitea)
3. **Media Storage** - Configure your S3-compatible bucket

## For Netlify Deployment

1. Install Netlify CLI: `npm install -g netlify-cli`
2. Set environment variables in Netlify dashboard:
   - AWS_ACCESS_KEY_ID
   - AWS_SECRET_ACCESS_KEY
   - AWS_S3_BUCKET
   - AWS_S3_REGION
3. Configure OAuth in Netlify site settings

## Local Development

For local testing without S3:
```yaml
media_library:
  name: uploadcare  # or use GitHub direct uploads
```

## Access the CMS

Once deployed, access Sveltia CMS at:
- `https://yourdomain.com/admin/`

## Additional Notes

- Media uploads will be stored in `static/uploads/` and synced to S3
- The public folder is set to `/uploads` for serving media files
- All content is managed through Git with Sveltia CMS commits
