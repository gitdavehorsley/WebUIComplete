# Simple Open WebUI Docker Setup

This is a simplified Docker setup for Open WebUI that follows their official installation recommendations.

## Quick Start

### Option 1: Using Docker Compose (Recommended)

1. **Clone or download the files:**
   ```bash
   # Make sure you have Dockerfile.simple, docker-compose.yml, and this README
   ```

2. **Update the secret key in docker-compose.yml:**
   ```yaml
   environment:
     - WEBUI_SECRET_KEY=your-secret-key-here  # Change this!
   ```

3. **Build and run:**
   ```bash
   docker-compose up --build
   ```

4. **Access Open WebUI:**
   - Open your browser to: http://localhost:8080

### Option 2: Using Docker directly

1. **Build the image:**
   ```bash
   docker build -f Dockerfile.simple -t open-webui .
   ```

2. **Run the container:**
   ```bash
   docker run -d \
     --name open-webui \
     -p 8080:8080 \
     -e WEBUI_SECRET_KEY=your-secret-key-here \
     -e WEBUI_AUTH=true \
     -e ENABLE_SIGNUP=true \
     open-webui
   ```

3. **Access Open WebUI:**
   - Open your browser to: http://localhost:8080

## Configuration

### Environment Variables

You can configure Open WebUI using environment variables:

```bash
# Basic configuration
WEBUI_SECRET_KEY=your-secret-key-here
WEBUI_AUTH=true
ENABLE_SIGNUP=true

# OpenAI API (optional)
OPENAI_API_BASE_URL=https://api.openai.com/v1
OPENAI_API_KEY=your-openai-api-key

# AWS Bedrock (optional)
AWS_ACCESS_KEY_ID=your-aws-access-key
AWS_SECRET_ACCESS_KEY=your-aws-secret-key
AWS_DEFAULT_REGION=us-east-1
```

### Data Persistence

The docker-compose setup includes a volume for data persistence:
- Data is stored in the `webui_data` volume
- This persists across container restarts

## What's Different from the Complex Build?

This simple setup:

✅ **Uses official Open WebUI installation method** (uv)  
✅ **Single-stage build** (no complex multi-stage)  
✅ **Follows Open WebUI recommendations**  
✅ **Easy to understand and modify**  
✅ **Includes health checks**  
✅ **Supports data persistence**  

❌ **No ECS optimization**  
❌ **No build caching**  
❌ **No complex retry logic**  
❌ **Larger final image size**  

## Troubleshooting

### Build Issues
- Make sure you have Docker installed and running
- Check that you have enough disk space (at least 2GB free)
- The first build may take 10-15 minutes

### Runtime Issues
- Check logs: `docker-compose logs open-webui`
- Verify the container is running: `docker ps`
- Check health status: `docker-compose ps`

### Port Issues
- Make sure port 8080 is not already in use
- Change the port in docker-compose.yml if needed:
  ```yaml
  ports:
    - "8081:8080"  # Use port 8081 on host
  ```

## Next Steps

Once you have Open WebUI running, you can:

1. **Configure your API endpoints** (OpenAI, Bedrock, etc.)
2. **Set up authentication** and user management
3. **Customize the interface** and settings
4. **Deploy to production** using the more complex build if needed

## Support

- Open WebUI Documentation: https://docs.openwebui.com/
- GitHub Repository: https://github.com/open-webui/open-webui
- Issues: https://github.com/open-webui/open-webui/issues 