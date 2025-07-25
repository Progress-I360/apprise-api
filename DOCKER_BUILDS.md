# Apprise API Docker Build Options

This repository provides multiple optimized Docker build targets for different use cases.

## Build Targets

### 1. `runtime` (Default - Full Featured)
**Complete Apprise API with Web UI**
- ✅ Full web interface for configuration management
- ✅ nginx reverse proxy + gunicorn
- ✅ Static file serving
- ✅ All API endpoints
- 📦 Size: ~400-600MB

```bash
docker build -f Dockerfile.optimized --target runtime -t apprise-api:runtime .
```

### 2. `api-only` (Recommended for Microservices)
**API-only without Web UI**
- ✅ All API endpoints (`/notify`, `/add`, `/get`, etc.)
- ✅ Direct gunicorn (no nginx)
- ❌ No web UI/static files
- 📦 Size: ~200-300MB (50%+ smaller)

```bash
docker build -f Dockerfile.optimized --target api-only -t apprise-api:api-only .
```

### 3. `runtime-distroless` (Maximum Security)
**Minimal distroless image**
- ✅ All API endpoints
- ✅ Google distroless base (no shell, minimal attack surface)
- ❌ No nginx/supervisord (requires different startup approach)
- 📦 Size: ~150-250MB

```bash
docker build -f Dockerfile.optimized --target runtime-distroless -t apprise-api:distroless .
```

### 4. `api-only-distroless` (Maximum Security + API-Only)
**Distroless API-only without Web UI**
- ✅ All API endpoints (`/notify`, `/add`, `/get`, etc.)
- ✅ Google distroless base (maximum security)
- ✅ Direct gunicorn (no nginx)
- ❌ No web UI/static files
- ❌ No shell access
- 📦 Size: ~100-200MB (smallest option)

```bash
docker build -f Dockerfile.optimized --target api-only-distroless -t apprise-api:api-only-distroless .
```

## Usage Examples

### API-Only (Your Use Case)
```bash
# Build API-only image
docker build -f Dockerfile.optimized --target api-only -t apprise-api:api-only .

# Run API-only container
docker run -p 8000:8000 \
  -v ./config:/config \
  -v ./attach:/attach \
  apprise-api:api-only

# Test API endpoint
curl -X POST http://localhost:8000/notify/ \
  -H "Content-Type: application/json" \
  -d '{
    "urls": ["mailto://user:pass@gmail.com"],
    "body": "Test notification from API-only container"
  }'
```

### API-Only Distroless (Maximum Security)
```bash
# Build API-only distroless image (smallest & most secure)
docker build -f Dockerfile.optimized --target api-only-distroless -t apprise-api:api-only-distroless .

# Run API-only distroless container
docker run -p 8000:8000 \
  -v ./config:/config \
  -v ./attach:/attach \
  apprise-api:api-only-distroless

# Test API endpoint
curl -X POST http://localhost:8000/notify/ \
  -H "Content-Type: application/json" \
  -d '{
    "urls": ["mailto://user:pass@gmail.com"],
    "body": "Test notification from distroless API-only container"
  }'
```

### Full Runtime
```bash
# Build full runtime image
docker build -f Dockerfile.optimized --target runtime -t apprise-api:runtime .

# Run with web UI
docker run -p 8000:8000 apprise-api:runtime

# Access web UI at http://localhost:8000
```

## GitHub Actions

The repository includes automated builds for all variants:

- `apprise-api:latest` (runtime)
- `apprise-api:api-only`
- `apprise-api:distroless`
- `apprise-api:api-only-distroless`## API Endpoints (Available in All Variants)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/notify/` | POST | Send stateless notification |
| `/notify/{key}` | POST | Send notification with config key |
| `/add/{key}` | POST | Add configuration |
| `/get/{key}` | GET | Get configuration |
| `/del/{key}` | DELETE | Delete configuration |
| `/status` | GET | Health check |
| `/details` | GET | API details |

## Recommendations

- **For microservices/API-only**: Use `api-only` target
- **For maximum security + API-only**: Use `api-only-distroless` target (smallest & most secure)
- **For full web application**: Use `runtime` target
- **For maximum security + full features**: Use `runtime-distroless` target