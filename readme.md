
# API Rate Limiter

```
   ___   _____ _  ____   __
  / _ | / __/ | |/_/\ \ / /
 / __ |_\ \ >  <   \ V / 
/_/ |_/___/_/|_|    |_|  
                         
```

A high-performance API rate limiter written in Go with support for both fixed and sliding window algorithms.

## Features

- 🚀 **Two Rate Limiting Algorithms**
  - Fixed Window: Simple and efficient
  - Sliding Window: Precise and fair
- 🔒 **Per-IP Rate Limiting**
- 💾 **In-Memory Cache** with automatic cleanup
- 📊 **Metrics Endpoint** for monitoring
- ⚙️ **Configurable** via environment variables
- 🎯 **HTTP Headers** for rate limit information
- 🏥 **Health Check** endpoint

## Project Structure

```
api-rate-limiter/
├─ main.go
├─ limiter/
│  ├─ limiter.go
├─ utils/
│  ├─ cache.go
└─ README.md
```

## Installation

```bash
git clone <repository-url>
cd api-rate-limiter
go mod init api-rate-limiter
go mod tidy
```

## Usage

### Basic Usage

```bash
go run main.go
```

### Configuration

Configure the rate limiter using environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | Server port | `8080` |
| `ALGORITHM` | Rate limiting algorithm (`fixed` or `sliding`) | `sliding` |
| `REQUESTS_LIMIT` | Maximum requests per window | `100` |
| `WINDOW_SECONDS` | Time window in seconds | `60` |

### Examples

```bash
# Run with fixed window algorithm
ALGORITHM=fixed REQUESTS_LIMIT=50 WINDOW_SECONDS=30 go run main.go

# Run on custom port with sliding window
PORT=3000 ALGORITHM=sliding REQUESTS_LIMIT=200 WINDOW_SECONDS=120 go run main.go
```

## API Endpoints

### Protected Resource
```
GET /api/resource
```

Returns a successful response if rate limit is not exceeded.

**Response Headers:**
- `X-RateLimit-Limit`: Maximum requests allowed
- `X-RateLimit-Remaining`: Remaining requests in current window
- `X-RateLimit-Reset`: Unix timestamp when limit resets

**Success Response (200):**
```json
{
  "message": "request successful",
  "ip": "192.168.1.1"
}
```

**Rate Limit Exceeded (429):**
```json
{
  "error": "rate limit exceeded",
  "reset": 1698765432
}
```

### Metrics
```
GET /metrics
```

Returns current rate limiter statistics.

**Response (200):**
```json
{
  "total_keys": 15,
  "total_requests": 1523,
  "algorithm": "sliding",
  "limit": 100,
  "window": 60
}
```

### Health Check
```
GET /health
```

Returns server health status.

**Response (200):**
```
OK
```

## Rate Limiting Algorithms

### Fixed Window
Counts requests in fixed time intervals. Simple and memory-efficient, but can allow burst traffic at window boundaries.

### Sliding Window
Tracks individual request timestamps within a rolling time window. More accurate and fair, but uses slightly more memory.

## Testing

```bash
# Test rate limiting
for i in {1..105}; do
  curl -i http://localhost:8080/api/resource
done

# Check metrics
curl http://localhost:8080/metrics

# Health check
curl http://localhost:8080/health
```

## Building

```bash
# Build binary
go build -o rate-limiter main.go

# Run binary
./rate-limiter
```

## Docker Support

```dockerfile
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY . .
RUN go mod download
RUN go build -o rate-limiter main.go

FROM alpine:latest
WORKDIR /app
COPY --from=builder /app/rate-limiter .
EXPOSE 8080
CMD ["./rate-limiter"]
```

```bash
docker build -t api-rate-limiter .
docker run -p 8080:8080 -e ALGORITHM=sliding -e REQUESTS_LIMIT=100 api-rate-limiter
```

## Performance Considerations

- In-memory cache with automatic cleanup every 10 minutes
- Thread-safe operations using mutex locks
- Efficient timestamp pruning in sliding window algorithm
- Suitable for single-instance deployments

For distributed systems, consider using Redis or similar distributed cache.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License

## Contact

For questions or support, reach out to: **contact.amish@yahoo.com**

---

```
Made with ❤️ by ASXY
```
