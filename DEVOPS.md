# CI/CD Pipeline Documentation

## Overview

This project has automated CI/CD pipelines using GitHub Actions to ensure code quality, build reliability, and automated deployment.

## Workflows

### 1. **CI/CD Pipeline** (`.github/workflows/ci-cd.yml`)

Runs automatically on every push and pull request to `master`, `main`, or `develop` branches.

**Jobs:**
- **Lint** ✅
  - Runs ESLint to check code quality
  - Catches styling and syntax errors
  - Continues even if linting fails (non-blocking)

- **Build** ✅
  - Installs dependencies
  - Runs Vite build process
  - Generates production-ready files in `dist/`
  - Uploads build artifacts for 7 days

- **Docker** ✅
  - Builds Docker image using Dockerfile
  - Uses BuildKit for optimized builds
  - Caches layers for faster subsequent builds
  - Ready to push to registry (optional)

- **Summary** ✅
  - Provides final status report

### 2. **Docker Push** (`.github/workflows/docker-push.yml`)

Triggers only when you create a new tag (version release).

**Usage:**
```bash
git tag v1.0.0
git push origin v1.0.0
```

This will automatically build and push the Docker image to Docker Hub.

---

## Setup Instructions

### For Basic CI/CD (No Docker Push)

The main `ci-cd.yml` workflow works out of the box!

1. **Commit and push your changes:**
   ```bash
   git add .
   git commit -m "Add CI/CD pipeline"
   git push origin master
   ```

2. **Go to GitHub repository → Actions tab** to view pipeline runs

3. Each workflow shows:
   - ✅ Passed jobs (green)
   - ❌ Failed jobs (red)
   - Detailed logs

---

### For Docker Push (Optional)

#### Step 1: Create Docker Hub Account
- Sign up at [hub.docker.com](https://hub.docker.com)
- Create a repository named `f1-dashboard`

#### Step 2: Generate Docker Access Token
- Go to **Account Settings → Security**
- Click **New Access Token**
- Create token with read/write permissions
- Copy the token (you won't see it again!)

#### Step 3: Add GitHub Secrets
1. Go to **GitHub Repository → Settings → Secrets and variables → Actions**
2. Click **New repository secret**
3. Add two secrets:
   - Name: `DOCKER_USERNAME` → Value: Your Docker Hub username
   - Name: `DOCKER_PASSWORD` → Value: Your Docker access token

#### Step 4: Enable Docker Push
Edit `.github/workflows/ci-cd.yml` and uncomment the Docker login & push sections:

```yaml
# - name: Login to Docker Hub
#   uses: docker/login-action@v3
#   with:
#     username: ${{ secrets.DOCKER_USERNAME }}
#     password: ${{ secrets.DOCKER_PASSWORD }}

# - name: Push to Docker Hub
#   uses: docker/build-push-action@v5
#   ...
```

#### Step 5: Create a Release
```bash
git tag v1.0.0
git push origin v1.0.0
```

The `docker-push.yml` workflow will automatically build and push!

---

## Pipeline Flow

```
Code Push
    ↓
GitHub Actions Triggered
    ↓
┌─────────────────────────────────────┐
│ Parallel Execution                  │
├─────────────────────────────────────┤
│ 1. Lint Code (ESLint)               │
│ 2. Build App (Vite)                 │
│ 3. Build Docker Image               │
└─────────────────────────────────────┘
    ↓
Summary Report
    ↓
✅ Pipeline Complete (Success/Failure)
```

---

## Monitoring & Debugging

### View Workflow Results
1. Go to **Actions** tab in GitHub
2. Click on latest workflow run
3. Click on job to see detailed logs
4. Look for error messages or warnings

### Common Issues

**Issue:** Linting fails
- **Solution:** Fix ESLint errors locally: `npm run lint`

**Issue:** Build fails
- **Solution:** Test build locally: `npm run build`

**Issue:** Docker build fails
- **Solution:** Test locally: `docker build -t f1-dashboard .`

**Issue:** Docker push fails
- **Solution:** Verify Docker secrets are set correctly in GitHub Settings

---

## Workflow Triggers

### CI/CD Pipeline (`ci-cd.yml`)
- ✅ On every push to: `master`, `main`, `develop`
- ✅ On every pull request to: `master`, `main`, `develop`
- ✅ Manual trigger (can be added)

### Docker Push (`docker-push.yml`)
- ✅ On new Git tag: `git tag v*` (e.g., `v1.0.0`, `v1.0.1`)

---

## Best Practices

1. **Always run locally first**
   ```bash
   npm run lint    # Check code
   npm run build   # Build app
   docker build .  # Build Docker
   ```

2. **Use semantic versioning for releases**
   ```bash
   git tag v1.0.0  # Major.Minor.Patch
   ```

3. **Keep secrets secure**
   - Never commit credentials
   - Use GitHub Secrets
   - Rotate tokens periodically

4. **Monitor workflow runs**
   - Check Actions tab regularly
   - Fix failures immediately
   - Review logs for warnings

5. **Update dependencies**
   - Regularly check for updates: `npm outdated`
   - Run `npm audit` for security

---

## Next Steps

- ✅ CI/CD Pipeline: Ready
- ⏳ Add automated testing (Jest, Vitest)
- ⏳ Deploy to production (AWS, Vercel, Heroku)
- ⏳ Add performance monitoring
- ⏳ Add security scanning (Snyk, CodeQL)

---

## Support

For GitHub Actions documentation: https://docs.github.com/en/actions
