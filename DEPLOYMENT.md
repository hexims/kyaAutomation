# ZLocker Deployment Guide

## Table of Contents
- [Local Development](#local-development)
- [Docker Deployment](#docker-deployment)
- [Production Deployment](#production-deployment)
- [Environment Configuration](#environment-configuration)
- [Database Setup](#database-setup)
- [Monitoring](#monitoring)

## Local Development

### Prerequisites
```bash
Node.js >= 18
npm or pnpm
```

### Setup

1. **Clone Repository**
   ```bash
   git clone https://github.com/hexims/kyaAutomation.git
   cd kyaAutomation
   ```

2. **Install Dependencies**
   ```bash
   pnpm install
   ```

3. **Configure Environment**
   ```bash
   cp .env.example .env.local
   ```

4. **Initialize Database**
   ```bash
   pnpm db:migrate
   pnpm db:seed
   ```

5. **Start Development Server**
   ```bash
   pnpm dev
   ```

   Backend: http://localhost:3000
   Frontend: http://localhost:3001

## Docker Deployment

### Using Docker Compose

1. **Build and Run**
   ```bash
   docker-compose up --build
   ```

2. **Access Services**
   - Backend API: http://localhost:3000
   - Frontend: http://localhost:3001
   - MySQL: localhost:3306
   - Redis: localhost:6379
   - PhpMyAdmin: http://localhost:8080

3. **Stop Services**
   ```bash
   docker-compose down
   ```

### Individual Docker Build

**Backend**
```bash
docker build -t zlocker-backend:latest -f Dockerfile.backend .
docker run -p 3000:3000 --env-file .env zlocker-backend:latest
```

**Frontend**
```bash
docker build -t zlocker-frontend:latest -f Dockerfile.frontend .
docker run -p 3001:3001 zlocker-frontend:latest
```

## Production Deployment

### Prerequisites
- Ubuntu 20.04 LTS or similar
- Docker & Docker Compose
- Nginx
- SSL Certificate (Let's Encrypt)
- AWS S3 bucket (for document storage)

### Deploy on AWS EC2

1. **SSH into Instance**
   ```bash
   ssh -i your-key.pem ubuntu@your-ec2-instance
   ```

2. **Install Dependencies**
   ```bash
   sudo apt update
   sudo apt install -y docker.io docker-compose nginx
   sudo systemctl start docker
   sudo usermod -aG docker ubuntu
   ```

3. **Clone Repository**
   ```bash
   git clone https://github.com/hexims/kyaAutomation.git
   cd kyaAutomation
   ```

4. **Setup Environment**
   ```bash
   cp .env.example .env.production
   # Edit with production values
   nano .env.production
   ```

5. **Deploy with Docker Compose**
   ```bash
   docker-compose -f docker-compose.prod.yml up -d
   ```

6. **Configure Nginx**
   ```bash
   sudo cp nginx/zlocker.conf /etc/nginx/sites-available/
   sudo ln -s /etc/nginx/sites-available/zlocker.conf /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl restart nginx
   ```

7. **Setup SSL (Let's Encrypt)**
   ```bash
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d your-domain.com
   ```

### Deploy on Kubernetes

1. **Create Namespace**
   ```bash
   kubectl create namespace zlocker
   ```

2. **Configure Secrets**
   ```bash
   kubectl create secret generic zlocker-secrets \
     --from-file=.env.production \
     -n zlocker
   ```

3. **Deploy Services**
   ```bash
   kubectl apply -f k8s/backend.yaml -n zlocker
   kubectl apply -f k8s/frontend.yaml -n zlocker
   kubectl apply -f k8s/postgres.yaml -n zlocker
   kubectl apply -f k8s/redis.yaml -n zlocker
   ```

4. **Check Status**
   ```bash
   kubectl get pods -n zlocker
   kubectl logs -f deployment/zlocker-backend -n zlocker
   ```

## Environment Configuration

### Production Environment Variables

```env
# Server
NODE_ENV=production
PORT=3000
FRONTEND_URL=https://zlocker.example.com

# Database
DB_HOST=db.example.com
DB_PORT=3306
DB_NAME=zlocker_prod
DB_USER=zlocker_user
DB_PASSWORD=strong_password_here
DB_SSL=true

# Redis
REDIS_URL=redis://redis.example.com:6379
REDIS_PASSWORD=redis_password

# JWT
JWT_SECRET=very_long_random_string_here
JWT_EXPIRY=24h

# AWS S3
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
S3_BUCKET=zlocker-documents-prod

# AI/ML Services
OCR_API_KEY=your_ocr_api_key
FACE_RECOGNITION_API_KEY=your_face_api_key

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=hello@ihex.co
SMTP_PASSWORD=email_password
SMTP_FROM=ZLocker <hello@ihex.co>

# Security
CORE_ORIGINS=https://zlocker.example.com,https://app.zlocker.example.com
RATE_LIMIT=100
RATE_WINDOW=15m

# Logging
LOG_LEVEL=info
LOG_FORMAT=json

# Sentry (Error Tracking)
SENTRY_DSN=your_sentry_dsn
```

## Database Setup

### Initial Migration
```bash
pnpm db:migrate
pnpm db:seed
```

### Backup
```bash
# Full backup
mysqldump -h $DB_HOST -u $DB_USER -p $DB_NAME > backup.sql

# Restore
mysql -h $DB_HOST -u $DB_USER -p $DB_NAME < backup.sql
```

## Monitoring

### Health Checks
```bash
curl https://api.zlocker.example.com/health
```

### Logs
```bash
# Docker logs
docker-compose logs -f backend

# System logs
journalctl -u docker -f
```

### Metrics
- CPU: Monitor CPU usage
- Memory: Keep < 80% utilization
- Disk: Monitor available space
- Network: Check bandwidth

### Uptime Monitoring
- Use UptimeRobot or similar service
- Set alerts for downtime
- Monitor API response times

## Troubleshooting

### Common Issues

**Port Already in Use**
```bash
lsof -i :3000
kill -9 <PID>
```

**Database Connection Failed**
```bash
# Check MySQL is running
mysql -h $DB_HOST -u $DB_USER -p

# Check credentials in .env
```

**Redis Connection Issues**
```bash
# Test Redis
redis-cli -h $REDIS_HOST ping
```

## Support

For deployment issues, please open an issue on GitHub or contact:
- Email: hello@ihex.co
- Discord: [Join our server]
