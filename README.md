# Deploy Raganork

**Reproducible, opinionated Docker deployments. Works on first deploy.**

Deploy Raganork is a Docker-based deployment setup that simplifies containerized application deployment. It prioritizes working deployments out of the box over endless configuration options.

## Philosophy

### Problems It Solves

- **Configuration Hell**: Deployment configs are fragmented (Docker, Heroku, Kubernetes manifests)
- **Environment Drift**: What works locally doesn't work in production
- **First Deploy Friction**: Setting up deployment for a new project takes hours
- **Complexity Overload**: Kubernetes is overkill for most applications

### Approach

Raganork provides **one opinionated way** to deploy Docker containers:
- Simple, reproducible process
- Minimal configuration
- Works immediately for common cases
- Clear upgrade path when you outgrow it

## What It Does

- **Docker Image Building**: Standardized Dockerfile patterns
- **Environment Management**: Simple `.env` variable handling
- **Container Orchestration**: Uses Heroku or similar platforms (no Kubernetes overhead)
- **Deployment Workflow**: Single command to deploy

## Quick Start

### 1. Fork This Repository
Click the fork button at the top right of this page.

### 2. Customize for Your App

#### Update `Dockerfile`
```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .

EXPOSE 3000
CMD ["npm", "start"]
```

Replace:
- Base image (e.g., `python:3.11`, `rust:latest`)
- Build steps
- Port number
- Start command

#### Configure `app.json` (Heroku)
```json
{
  "name": "my-awesome-app",
  "description": "Your app description",
  "buildpacks": [
    {"url": "heroku/dockerfile"},
    {"url": "heroku/procfile"}
  ],
  "env": {
    "API_KEY": {
      "description": "API key for external service",
      "required": false
    }
  }
}
```

### 3. Deploy

**To Heroku**:
```bash
heroku login
heroku create your-app-name
git push heroku main
```

**To Fly.io**:
```bash
fly auth login
fly launch
fly deploy
```

**Local Testing**:
```bash
docker build -t my-app .
docker run -p 3000:3000 my-app
```

## Configuration Files

### Dockerfile
Defines your application's container image.

**Key Sections**:
- **FROM**: Base image (language + OS)
- **WORKDIR**: Working directory in container
- **COPY**: Copy source code
- **RUN**: Install dependencies, build steps
- **EXPOSE**: Port your app listens on
- **CMD**: Default startup command

### app.json
Heroku-specific configuration. Enables one-click deploy and environment variable setup.

### Deployment Platforms

#### Heroku
- ✅ One-click deploy button
- ✅ Automatic HTTPS
- ✅ Simple scaling
- Pricing: Free tier, then $5+/month per dyno

#### Fly.io
- ✅ Modern alternative to Heroku
- ✅ Better pricing
- ✅ Global deployment
- Pricing: Free tier, then $0.003/hour per VM

## Troubleshooting

**Docker build fails**
- Check Dockerfile syntax: `docker build -t test .`
- Ensure all files are copied correctly

**Container runs locally but not in Heroku**
- Port must match in Dockerfile and config
- Check logs: `heroku logs --tail`

## Best Practices

1. **Keep images small** (use `alpine` base images)
2. **Don't hardcode secrets** in Dockerfile
3. **Use environment variables** for all config
4. **Monitor logs** in production
5. **Tag releases** for reproducibility

## License

MIT - See original repository for full details
