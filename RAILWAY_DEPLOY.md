# Railway Deployment Guide for Mealie

This guide explains how to deploy Mealie to [Railway](https://railway.app) with the domain `thebenzings.com`.

## Prerequisites

1. A [Railway](https://railway.app) account
2. A domain (`thebenzings.com`) with DNS access

## Quick Deploy

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/mealie?referralCode=mealie)

Or manually:

1. Fork/clone this repository to your GitHub account
2. Log in to Railway and create a new project
3. Select "Deploy from GitHub repo"
4. Connect your GitHub account and select this repository
5. Railway will automatically detect the `railway.toml` configuration

## Required Environment Variables

Configure these environment variables in your Railway project settings:

### Essential Configuration

| Variable | Description | Example Value |
|----------|-------------|---------------|
| `BASE_URL` | The full URL where Mealie will be accessible | `https://thebenzings.com` |
| `ALLOW_SIGNUP` | Allow new user registration | `true` or `false` |
| `PORT` | Port for the application (Railway sets this automatically) | `9000` |

### Database Configuration

Railway provides PostgreSQL as an add-on service. Add a PostgreSQL database to your project and configure:

| Variable | Description | Example Value |
|----------|-------------|---------------|
| `DB_ENGINE` | Database engine type | `postgres` |
| `POSTGRES_USER` | PostgreSQL username | `${{Postgres.PGUSER}}` |
| `POSTGRES_PASSWORD` | PostgreSQL password | `${{Postgres.PGPASSWORD}}` |
| `POSTGRES_SERVER` | PostgreSQL host | `${{Postgres.PGHOST}}` |
| `POSTGRES_PORT` | PostgreSQL port | `${{Postgres.PGPORT}}` |
| `POSTGRES_DB` | PostgreSQL database name | `${{Postgres.PGDATABASE}}` |

> **Note:** The `${{Postgres.*}}` syntax uses Railway's variable references to automatically connect to your PostgreSQL add-on.

### Optional Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `LOG_LEVEL` | Logging verbosity | `INFO` |
| `PRODUCTION` | Enable production mode | `true` |
| `TZ` | Timezone | `America/New_York` |
| `TOKEN_TIME` | Session token expiry (hours) | `48` |

### Email Configuration (Optional)

To enable email features like password reset:

| Variable | Description |
|----------|-------------|
| `SMTP_HOST` | SMTP server hostname |
| `SMTP_PORT` | SMTP server port |
| `SMTP_FROM_NAME` | Sender display name |
| `SMTP_FROM_EMAIL` | Sender email address |
| `SMTP_AUTH_STRATEGY` | `TLS`, `SSL`, or `NONE` |
| `SMTP_USER` | SMTP username |
| `SMTP_PASSWORD` | SMTP password |

## Domain Configuration

### Setting Up thebenzings.com

1. In your Railway project, go to **Settings** → **Networking** → **Public Networking**
2. Click **Generate Domain** or **Custom Domain**
3. Add your custom domain: `thebenzings.com`
4. Railway will provide DNS records to configure

### DNS Configuration

Add these records to your DNS provider (e.g., Cloudflare, Route53, Namecheap):

**Option 1: CNAME Record (Recommended)**
```
Type: CNAME
Name: @ (or leave blank for root domain)
Value: <your-railway-domain>.railway.app
```

> **Note:** Some DNS providers don't support CNAME at root. Use ALIAS or ANAME if available, or configure a subdomain.

**Option 2: A Record (if CNAME not supported at root)**
```
Type: A
Name: @ (or leave blank for root domain)
Value: <Railway's IP address>
```

**For www subdomain:**
```
Type: CNAME
Name: www
Value: <your-railway-domain>.railway.app
```

### SSL/TLS

Railway automatically provisions and manages SSL certificates via Let's Encrypt. No additional configuration is needed.

## Persistent Storage

Railway provides ephemeral storage by default. For persistent data (recipes, images, backups):

### Option 1: Railway Volume (Recommended)

1. In Railway, go to your service settings
2. Click **Add Volume**
3. Set the mount path to `/app/data`
4. This persists your Mealie data across deployments

### Option 2: External Storage

Configure Mealie to use external storage solutions for production deployments.

## Sample Railway Environment Variables

Copy these to your Railway project's **Variables** section:

```env
# Required
BASE_URL=https://thebenzings.com
ALLOW_SIGNUP=false
PRODUCTION=true

# Database (use Railway's PostgreSQL add-on)
DB_ENGINE=postgres
POSTGRES_USER=${{Postgres.PGUSER}}
POSTGRES_PASSWORD=${{Postgres.PGPASSWORD}}
POSTGRES_SERVER=${{Postgres.PGHOST}}
POSTGRES_PORT=${{Postgres.PGPORT}}
POSTGRES_DB=${{Postgres.PGDATABASE}}

# Optional
LOG_LEVEL=INFO
TZ=America/New_York
```

## Deployment Steps Summary

1. **Create Railway Project**: Log in to Railway → New Project → Deploy from GitHub repo
2. **Add PostgreSQL**: In your project, click **Add Service** → **Database** → **PostgreSQL**
3. **Configure Variables**: Add the environment variables listed above
4. **Add Volume**: Click your service → **Settings** → **Add Volume** → Mount at `/app/data`
5. **Configure Domain**: **Settings** → **Networking** → Add custom domain `thebenzings.com`
6. **Configure DNS**: Add the provided DNS records to your domain registrar
7. **Deploy**: Railway will automatically build and deploy

## Troubleshooting

### Build Fails

- Ensure the repository has the `docker/Dockerfile` present
- Check Railway build logs for specific errors
- Verify all required environment variables are set

### Application Won't Start

- Check that `DB_ENGINE` is set correctly (`postgres` or `sqlite`)
- Verify PostgreSQL connection variables are correct
- Review application logs in Railway dashboard

### Database Connection Issues

- Ensure PostgreSQL add-on is properly provisioned
- Use Railway's variable reference syntax: `${{Postgres.PGHOST}}`
- Check that PostgreSQL service is running

### Domain Not Working

- Verify DNS records are correctly configured
- Wait for DNS propagation (can take up to 48 hours)
- Check Railway's domain verification status

## Default Credentials

On first launch, Mealie creates a default admin user:

- **Email**: `changeme@example.com`
- **Password**: `MyPassword`

> ⚠️ **Important**: Change these credentials immediately after first login!

## Additional Resources

- [Mealie Documentation](https://docs.mealie.io/)
- [Railway Documentation](https://docs.railway.app/)
- [Railway Discord](https://discord.gg/railway)
