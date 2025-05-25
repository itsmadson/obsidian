
## 🐳 Docker Setup

### File Structure:

```
tile-server/
├── main.go
├── go.mod
├── go.sum
├── Dockerfile
├── docker-compose.yml
├── Makefile
├── .env
├── .dockerignore
├── init.sql
└── tiles/          # Your tile directories
    ├── 1/
    ├── 2/
    └── ...
```

### 🚀 Quick Start:

1. **Clone and setup:**

```bash
git clone <your-repo>
cd tile-server
```

2. **Configure environment:** Edit `.env` file with your actual values:

```bash
JWT_SECRET=your-actual-secret
DATABASE_URL=postgres://user:pass@host:port/dbname
```

3. **Deploy:**

```bash
make deploy
```

### 📋 Available Commands:

```bash
make build      # Build Docker image
make run        # Start all services
make stop       # Stop all services
make logs       # Show tile-server logs
make logs-all   # Show all service logs
make restart    # Restart tile-server
make status     # Check service status
make clean      # Remove everything
make dev        # Development mode
```

### 🔧 Configuration:

**Environment Variables:**

- `DATABASE_URL`: PostgreSQL connection
- `REDIS_URL`: Redis connection
- `JWT_SECRET`: Your JWT signing key
- `TILES_PATH`: Tiles directory path
- `PORT`: Server port (default: 8080)

**Volumes:**

- `./tiles:/tiles:ro` - Mount your tiles directory (read-only)
- Persistent volumes for PostgreSQL and Redis data

### 🏗️ Architecture:

```
┌─────────────────┐    ┌──────────────┐    ┌─────────────┐
│   Tile Server   │────│    Redis     │    │ PostgreSQL  │
│   (Port 8080)   │    │ (Port 6379)  │    │(Port 5432)  │
└─────────────────┘    └──────────────┘    └─────────────┘
         │
         │ Mount
         ▼
   ./tiles (read-only)
```

### 🔍 Health Monitoring:

- **Health Check Endpoint:** `GET /health`
- **Docker Health Check:** Built-in container health monitoring
- **Logs:** Structured logging with timestamps

### 🔒 Security Features:

- **Non-root container:** Runs as non-privileged user
- **Read-only tiles:** Tiles directory mounted read-only
- **Network isolation:** Services communicate via internal network
- **JWT validation:** Secure token-based authentication

### 🚀 Production Ready:

- **Multi-stage build:** Optimized Docker image
- **Health checks:** Automatic container monitoring
- **Graceful shutdown:** Proper signal handling
- **Connection retry:** Database/Redis connection resilience
- **Resource limits:** Can be added to docker-compose.yml

Just put your tiles in the `./tiles` directory and run `make deploy`! 🎉
## 🔄 Key Changes Made:

### 1. **Updated SQL Queries:**

- **Permissions Query:** Added `AND "type" = 'XYZ'` filter
- **Basemap URLs Query:** New query to fetch `id` and `url` from basemaps

### 2. **Tile Proxying (No Local Files):**

- ❌ Removed local file serving
- ✅ Added HTTP proxying with 5-second timeout
- ✅ Caches basemap URLs in Redis for fast access
- ✅ Dynamic URL building with `{x}`, `{y}`, `{z}` replacement

### 3. **Enhanced Features:**

- **Dual Background Tasks:** Sync permissions + basemap URLs every 15 minutes
- **HTTP Client:** 5-second timeout for tile requests
- **Better Headers:** User-Agent, Accept headers for compatibility
- **CORS Support:** For web applications
- **Caching Headers:** Proper tile caching (1 hour)

## 🚀 How It Works Now:

1. **User Request:** `GET /api/basemaps/1/9/329/209.png`
2. **JWT Validation:** Extract username from token
3. **Permission Check:** Redis lookup for user permissions
4. **URL Template:** Redis lookup for basemap URL template
5. **URL Building:** Replace `{x}`, `{y}`, `{z}` in template
6. **Proxy Request:** Fetch tile from actual source (Google, OSM, etc.)
7. **Response:** Stream tile back to client

## 🌐 Supported Tile Sources:

Based on your examples:

- **Google Satellite:** `https://mt1.google.com/vt/lyrs=s&x={x}&y={y}&z={z}`
- **Google Maps:** `https://mt1.google.com/vt/lyrs=m&x={x}&y={y}&z={z}`
- **OpenStreetMap:** `http://tile.openstreetmap.org/{z}/{x}/{y}.png`
- **Custom Servers:** `http://2.189.242.245:8080/tiles/test/{z}/{x}/{y}.png`

## 🔧 Redis Cache Structure:

```
user_basemaps:poshtiban = ["1", "2", "3"]
basemap_url:1 = "https://mt1.google.com/vt/lyrs=s&x={x}&y={y}&z={z}"
basemap_url:2 = "https://mt1.google.com/vt/lyrs=m&x={x}&y={y}&z={z}"
```

## 📦 Updated Docker Setup:

- ❌ Removed tiles volume mount (no longer needed)
- ✅ Cleaner docker-compose.yml
- ✅ Updated environment variables

The service now acts as a pure proxy - it fetches tiles from remote servers and caches permissions/URLs in Redis for lightning-fast responses! 🚀

Deploy with: `make deploy` and it's ready to proxy XYZ tiles! 🎯