# Google Maps Scraper - Coolify Deployment

Deploy the Google Maps Scraper on Coolify with a custom domain.

## Quick Deploy via Coolify

### Method 1: Docker Image (Simplest)

1. Go to Coolify Dashboard
2. Click **+ New Resource**
3. Select **Docker Image**
4. Configure:

| Setting | Value |
|---------|-------|
| Image | `gosom/google-maps-scraper:latest` |
| Port | `8080` |
| Command | `-web -dsn /data/leads.db` |

5. Add **Volume Mount**:
   - Host Path: `/data/google-maps-scraper`
   - Container Path: `/data`

6. Set **Domain** (e.g., `scraper.yourdomain.com`)
7. Enable **HTTPS** (Let's Encrypt)
8. Click **Deploy**

### Method 2: Docker Compose

1. Push this repo to GitHub
2. In Coolify: **+ New Resource** → **Docker Compose**
3. Connect your GitHub repo
4. Set domain and deploy

## After Deployment

### Access Points

| URL | Purpose |
|-----|---------|
| `https://scraper.yourdomain.com` | Web UI |
| `https://scraper.yourdomain.com/api/docs` | API Documentation |
| `https://scraper.yourdomain.com/api/v1/jobs` | REST API |

### Test the API

```bash
# Check API is running
curl https://scraper.yourdomain.com/api/v1/jobs

# Create a scrape job
curl -X POST https://scraper.yourdomain.com/api/v1/jobs \
  -H "Content-Type: application/json" \
  -d '{
    "query": "roofing contractors Dallas TX",
    "max_results": 100
  }'
```

## n8n Integration

Use this endpoint in your n8n HTTP Request node:

```
POST https://scraper.yourdomain.com/api/v1/jobs
```

Body:
```json
{
  "query": "roofing contractors {{$json.city}} {{$json.state}}",
  "max_results": 100
}
```

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/jobs` | List all jobs |
| POST | `/api/v1/jobs` | Create new scrape job |
| GET | `/api/v1/jobs/{id}` | Get job status |
| DELETE | `/api/v1/jobs/{id}` | Delete a job |
| GET | `/api/v1/jobs/{id}/download` | Download results as CSV |

## Scaling Tips

- **More concurrent scrapes:** Add `concurrency` parameter
- **Proxy support:** Add proxy configuration for high volume
- **Multiple instances:** Scale horizontally in Coolify

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Browser not installing | Check volume permissions |
| Slow scrapes | Reduce max_results per job |
| Rate limiting | Add delays between requests |
| Memory issues | Increase container resources in Coolify |
