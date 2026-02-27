# 🏗️ BLOCKLOTTO - ESTRATEGIA DE AMBIENTES
## Development, Testing, Staging & Production Environments

---

## 📋 TABLA DE CONTENIDOS

1. [Resumen Ejecutivo](#resumen-ejecutivo)
2. [Arquitectura de Ambientes](#arquitectura-de-ambientes)
3. [Ambiente de Desarrollo](#ambiente-de-desarrollo)
4. [Ambiente de Testing](#ambiente-de-testing)
5. [Ambiente de Staging](#ambiente-de-staging)
6. [Ambiente de Producción](#ambiente-de-producción)
7. [CI/CD Pipeline](#cicd-pipeline)
8. [Infraestructura como Código](#infraestructura-como-código)
9. [Seguridad y Accesos](#seguridad-y-accesos)
10. [Monitoreo y Observabilidad](#monitoreo-y-observabilidad)
11. [Disaster Recovery](#disaster-recovery)
12. [Costos Estimados](#costos-estimados)

---

## 🎯 RESUMEN EJECUTIVO

### **Filosofía de Ambientes**

BlockLotto implementa una estrategia de **4 ambientes** para garantizar calidad, seguridad y confiabilidad:

```
LOCAL → DEV → STAGING → PRODUCTION
  ↓       ↓       ↓          ↓
(Devs) (Team)  (QA)    (Users)
```

### **Principios Clave**

1. **Paridad de Ambientes**: Staging debe ser idéntico a producción
2. **Inmutabilidad**: Nunca modificar producción directamente
3. **Automatización**: CI/CD para todos los deploys
4. **Observabilidad**: Monitoreo en todos los ambientes
5. **Seguridad**: Secrets management y acceso controlado

---

## 🏗️ ARQUITECTURA DE AMBIENTES

### **Vista General**

```
┌─────────────────────────────────────────────────────────────┐
│                     AMBIENTES                               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   LOCAL      │  │     DEV      │  │   STAGING    │      │
│  │              │  │              │  │              │      │
│  │ • Hardhat    │  │ • Mumbai     │  │ • Mumbai     │      │
│  │ • localhost  │  │ • Vercel Dev │  │ • Vercel Prev│      │
│  │ • Mock APIs  │  │ • Test DB    │  │ • Test DB    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                             │
│                    ┌──────────────┐                         │
│                    │  PRODUCTION  │                         │
│                    │              │                         │
│                    │ • Polygon    │                         │
│                    │ • Vercel Prod│                         │
│                    │ • Prod DB    │                         │
│                    └──────────────┘                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### **Matriz de Ambientes**

| Ambiente | Blockchain | Frontend | Backend | Database | Monitoring |
|----------|-----------|----------|---------|----------|------------|
| **Local** | Hardhat Node | localhost:3000 | localhost:4000 | SQLite | Console logs |
| **Dev** | Mumbai Testnet | dev.blocklotto.io | dev-api.blocklotto.io | PostgreSQL (Dev) | Sentry (Dev) |
| **Staging** | Mumbai Testnet | staging.blocklotto.io | staging-api.blocklotto.io | PostgreSQL (Staging) | Sentry + Tenderly |
| **Production** | Polygon Mainnet | app.blocklotto.io | api.blocklotto.io | PostgreSQL (Prod) | Full Stack |

---

## 💻 AMBIENTE DE DESARROLLO

### **Propósito**
- Desarrollo individual de features
- Pruebas rápidas sin consumir gas real
- Experimentación sin riesgos

### **Configuración Local**

#### **1. Blockchain Local (Hardhat Network)**

```bash
# hardhat.config.js
module.exports = {
  networks: {
    hardhat: {
      chainId: 31337,
      forking: {
        url: process.env.POLYGON_MUMBAI_RPC,
        enabled: false  // Opcional: fork Mumbai para testing
      },
      accounts: {
        mnemonic: "test test test test test test test test test test test junk",
        count: 20,  // 20 cuentas de prueba
        accountsBalance: "10000000000000000000000" // 10,000 ETH cada una
      }
    }
  }
};
```

**Comandos:**
```bash
# Iniciar nodo local
npx hardhat node

# Deploy contracts localmente
npx hardhat run scripts/deploy.js --network localhost

# Consola interactiva
npx hardhat console --network localhost
```

#### **2. Frontend Local**

```bash
# .env.local
NEXT_PUBLIC_CHAIN_ID=31337
NEXT_PUBLIC_RPC_URL=http://localhost:8545
NEXT_PUBLIC_CONTRACT_ADDRESS=0x5FbDB2315678afecb367f032d93F642f64180aa3
NEXT_PUBLIC_ENV=local

# Iniciar desarrollo
npm run dev
# Disponible en http://localhost:3000
```

#### **3. The Graph Local (Opcional)**

```bash
# docker-compose.yml para Graph Node local
version: '3'
services:
  graph-node:
    image: graphprotocol/graph-node
    ports:
      - '8000:8000'
      - '8001:8001'
      - '8020:8020'
      - '8030:8030'
      - '8040:8040'
    environment:
      postgres_host: postgres
      postgres_user: graph
      postgres_pass: let-me-in
      postgres_db: graph-node
      ipfs: 'ipfs:5001'
      ethereum: 'localhost:http://host.docker.internal:8545'
```

#### **4. Mock Services**

```javascript
// mocks/chainlink-vrf.js
// Mock de Chainlink VRF para desarrollo local
class MockVRFCoordinator {
  async requestRandomWords() {
    // Retorna números pseudo-aleatorios inmediatamente
    return Math.floor(Math.random() * 1000000);
  }
}
```

### **Herramientas de Desarrollo**

```bash
# Package.json scripts
{
  "scripts": {
    "dev": "next dev",
    "dev:node": "hardhat node",
    "dev:deploy": "hardhat run scripts/deploy.js --network localhost",
    "dev:console": "hardhat console --network localhost",
    "dev:test": "hardhat test",
    "dev:coverage": "hardhat coverage",
    "dev:graph": "docker-compose up graph-node"
  }
}
```

### **Ventajas del Ambiente Local**

✅ **Velocidad**: Bloques instantáneos
✅ **Sin costos**: No gasta MATIC real
✅ **Control total**: Manipular tiempo, bloques, balances
✅ **Debugging**: Logs detallados y stacktraces
✅ **Offline**: No requiere internet

---

## 🧪 AMBIENTE DE TESTING

### **Propósito**
- Integración continua (CI)
- Tests automatizados
- Validación pre-merge

### **1. Tests Unitarios (Hardhat)**

```javascript
// test/LotteryCore.test.js
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("LotteryCore", function () {
  let lottery, owner, user1;
  
  beforeEach(async function () {
    [owner, user1] = await ethers.getSigners();
    const Lottery = await ethers.getContractFactory("LotteryCore");
    lottery = await Lottery.deploy();
    await lottery.deployed();
  });
  
  it("Should allow ticket purchase", async function () {
    const numbers = [1, 2, 3, 4, 5, 6];
    const ticketPrice = ethers.utils.parseEther("2");
    
    await expect(
      lottery.connect(user1).buyTicket(numbers, { value: ticketPrice })
    ).to.emit(lottery, "TicketPurchased");
  });
  
  // Más tests...
});
```

**Comandos de Testing:**
```bash
# Tests unitarios
npm run test

# Coverage
npm run coverage

# Tests específicos
npm run test test/LotteryCore.test.js

# Watch mode
npm run test:watch
```

### **2. Tests de Integración (Mumbai Testnet)**

```javascript
// test/integration/fullflow.test.js
describe("Full User Flow", function () {
  it("Should complete full lottery cycle", async function () {
    // 1. User buys ticket
    // 2. Wait for draw
    // 3. VRF generates numbers
    // 4. Check if winner
    // 5. Claim prize
    
    this.timeout(300000); // 5 minutos para esperar bloques
  });
});
```

### **3. Fork Testing**

```bash
# hardhat.config.js - Fork Mumbai para tests
networks: {
  hardhat: {
    forking: {
      url: process.env.POLYGON_MUMBAI_RPC,
      blockNumber: 12345678  // Pin a bloque específico
    }
  }
}
```

**Ventaja**: Pruebas contra estado real de Mumbai sin gastar gas.

### **4. CI/CD Testing (GitHub Actions)**

```yaml
# .github/workflows/test.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Run Hardhat tests
        run: npm run test
        
      - name: Run coverage
        run: npm run coverage
        
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
```

### **5. Frontend Testing**

```javascript
// __tests__/BuyTicket.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { BuyTicket } from '@/components/BuyTicket';

describe('BuyTicket Component', () => {
  it('allows user to select 6 numbers', () => {
    render(<BuyTicket />);
    
    // Click 6 numbers
    for (let i = 1; i <= 6; i++) {
      fireEvent.click(screen.getByText(i.toString()));
    }
    
    expect(screen.getByText('Purchase Ticket')).toBeEnabled();
  });
});
```

**Stack de Testing Frontend:**
- Jest
- React Testing Library
- Cypress (E2E)

### **Métricas de Calidad**

```
Objetivos:
├─ Unit Test Coverage: >90%
├─ Integration Test Coverage: >80%
├─ E2E Test Coverage: Critical paths
└─ Performance: Lighthouse score >90
```

---

## 🎭 AMBIENTE DE STAGING

### **Propósito**
- Pre-producción idéntica a producción
- QA y user acceptance testing (UAT)
- Validación final antes de mainnet

### **Configuración**

#### **1. Smart Contracts (Mumbai Testnet)**

```bash
# .env.staging
POLYGON_MUMBAI_RPC=https://polygon-mumbai.g.alchemy.com/v2/YOUR_KEY
MUMBAI_PRIVATE_KEY=0x...
ETHERSCAN_API_KEY=YOUR_API_KEY

# Contratos desplegados en Mumbai
LOTTERY_CORE_ADDRESS=0x1234...
TICKET_NFT_ADDRESS=0x5678...
PRIZE_POOL_ADDRESS=0x9abc...
DRAW_MANAGER_ADDRESS=0xdef0...
```

**Deploy a Staging:**
```bash
npx hardhat run scripts/deploy.js --network mumbai
npx hardhat verify --network mumbai 0x1234... "Constructor args"
```

#### **2. Frontend (Vercel Preview)**

```bash
# vercel.json
{
  "env": {
    "NEXT_PUBLIC_CHAIN_ID": "80001",
    "NEXT_PUBLIC_RPC_URL": "@polygon_mumbai_rpc",
    "NEXT_PUBLIC_ENV": "staging"
  },
  "build": {
    "env": {
      "NEXT_TELEMETRY_DISABLED": "1"
    }
  }
}
```

**URL**: `https://staging.blocklotto.io` o `https://blocklotto-git-staging.vercel.app`

#### **3. The Graph (Hosted Service)**

```yaml
# subgraph.yaml (staging)
specVersion: 0.0.4
schema:
  file: ./schema.graphql
dataSources:
  - kind: ethereum/contract
    name: LotteryCore
    network: mumbai
    source:
      address: "0x1234..."
      abi: LotteryCore
      startBlock: 12345678
```

**Deploy:**
```bash
graph auth --product hosted-service YOUR_ACCESS_TOKEN
graph deploy --product hosted-service blocklotto/staging
```

#### **4. Database (PostgreSQL - Supabase Staging)**

```bash
# .env.staging
DATABASE_URL=postgresql://user:pass@staging-db.supabase.co:5432/blocklotto_staging
REDIS_URL=redis://staging-redis.railway.app:6379
```

**Schema Migration:**
```bash
# Prisma migrations
npx prisma migrate deploy --preview-feature

# Seed data para staging
npx prisma db seed
```

### **Testing en Staging**

#### **Checklist Pre-Production**

```markdown
## Smart Contracts
- [ ] Deploy exitoso en Mumbai
- [ ] Contracts verificados en PolygonScan
- [ ] Chainlink VRF subscription activa
- [ ] Chainlink Automation configurado
- [ ] Todos los permisos configurados (AccessControl)
- [ ] Prize pool tiene fondos de prueba

## Frontend
- [ ] Build exitoso sin warnings
- [ ] Wallet connection funciona (MetaMask, WalletConnect)
- [ ] Compra de tickets funciona end-to-end
- [ ] NFT metadata se genera correctamente
- [ ] Galería de NFTs carga
- [ ] Prize claiming funciona
- [ ] Mobile responsive verificado
- [ ] Lighthouse score >90

## Backend/APIs
- [ ] The Graph indexando correctamente
- [ ] API endpoints responden <500ms
- [ ] Rate limiting configurado
- [ ] Error handling apropiado
- [ ] CORS configurado correctamente

## Integración
- [ ] User flow completo: Buy → Draw → Claim
- [ ] Chainlink VRF genera números correctamente
- [ ] Prize distribution calcula bien
- [ ] Email notifications funcionan
- [ ] Analytics tracking funciona

## Performance
- [ ] Load testing con 100 usuarios concurrentes
- [ ] No memory leaks detectados
- [ ] Database queries optimizadas
- [ ] CDN configurado (imágenes)

## Security
- [ ] Secrets rotados desde dev
- [ ] Rate limiting en APIs
- [ ] Input validation en frontend y backend
- [ ] HTTPS enforced
- [ ] Security headers configurados
```

#### **Automated E2E Tests (Staging)**

```javascript
// cypress/e2e/lottery-flow.cy.js
describe('Complete Lottery Flow', () => {
  it('User can buy ticket and claim prize', () => {
    // 1. Connect wallet
    cy.visit('https://staging.blocklotto.io');
    cy.get('[data-cy=connect-wallet]').click();
    cy.get('[data-cy=metamask]').click();
    
    // 2. Select numbers
    for (let i = 1; i <= 6; i++) {
      cy.get(`[data-cy=number-${i}]`).click();
    }
    
    // 3. Purchase ticket
    cy.get('[data-cy=buy-ticket]').click();
    cy.get('[data-cy=confirm-transaction]').click();
    
    // 4. Wait for confirmation
    cy.get('[data-cy=success-message]', { timeout: 30000 })
      .should('be.visible');
    
    // 5. Check NFT appears
    cy.visit('https://staging.blocklotto.io/my-tickets');
    cy.get('[data-cy=ticket-nft]').should('exist');
  });
});
```

### **Staging Best Practices**

1. **Data Isolation**: Nunca usar datos reales de usuarios
2. **Automatic Cleanup**: Script para limpiar datos antiguos semanalmente
3. **Monitoring**: Mismo nivel que producción
4. **Access Control**: Solo team tiene acceso
5. **Parity**: Configuración idéntica a producción

---

## 🚀 AMBIENTE DE PRODUCCIÓN

### **Propósito**
- Servir usuarios reales
- Manejar dinero real
- Uptime crítico (99.9%)

### **Infraestructura**

#### **1. Blockchain (Polygon Mainnet)**

```bash
# .env.production
POLYGON_MAINNET_RPC=https://polygon-mainnet.g.alchemy.com/v2/YOUR_PROD_KEY
MAINNET_PRIVATE_KEY=0x... # HOT WALLET (limited funds)
DEPLOYER_PRIVATE_KEY=0x... # COLD WALLET (Ledger/Hardware)

# Contratos en Polygon Mainnet
LOTTERY_CORE_ADDRESS=0xABCD...
TICKET_NFT_ADDRESS=0xEF01...
PRIZE_POOL_ADDRESS=0x2345...
DRAW_MANAGER_ADDRESS=0x6789...

# Chainlink Production
VRF_COORDINATOR=0x... # Polygon VRF Coordinator
LINK_TOKEN=0x... # LINK on Polygon
VRF_KEY_HASH=0x...
VRF_SUBSCRIPTION_ID=123
```

**Deploy a Producción:**
```bash
# Multi-sig deployment (Gnosis Safe)
# Requiere aprobación de 3/5 signatarios

# 1. Deploy contracts
npx hardhat run scripts/deploy.js --network polygon

# 2. Verify on PolygonScan
npx hardhat verify --network polygon 0xABCD... "args"

# 3. Transfer ownership to multi-sig
npx hardhat run scripts/transfer-ownership.js --network polygon

# 4. Initialize via Gnosis Safe UI
```

#### **2. Frontend (Vercel Production)**

```javascript
// next.config.js
module.exports = {
  env: {
    NEXT_PUBLIC_CHAIN_ID: '137',
    NEXT_PUBLIC_RPC_URL: process.env.POLYGON_MAINNET_RPC,
    NEXT_PUBLIC_ENV: 'production',
    NEXT_PUBLIC_ANALYTICS_ID: process.env.MIXPANEL_TOKEN
  },
  images: {
    domains: ['ipfs.io', 'cloudflare-ipfs.com'],
    loader: 'cloudinary',
    path: 'https://res.cloudinary.com/blocklotto/'
  },
  headers: async () => [
    {
      source: '/:path*',
      headers: [
        { key: 'X-Frame-Options', value: 'DENY' },
        { key: 'X-Content-Type-Options', value: 'nosniff' },
        { key: 'Strict-Transport-Security', value: 'max-age=31536000' }
      ]
    }
  ]
};
```

**URL**: `https://app.blocklotto.io`

**CDN & Performance:**
- Vercel Edge Network (global)
- Cloudflare DNS
- Image optimization (Cloudinary)
- Static assets en CDN

#### **3. Database (PostgreSQL - Supabase Production)**

```bash
# High Availability Setup
Primary: us-east-1
Replica: eu-west-1 (read replica)
Backup: Daily automated + WAL archiving

# Connection Pooling
PgBouncer with 100 connections
Connection timeout: 30s
Max client connections: 1000
```

**Backup Strategy:**
```bash
# Automated daily backups
0 2 * * * pg_dump blocklotto_prod > backup_$(date +%Y%m%d).sql

# Retention: 30 days
# Point-in-time recovery: 7 days
```

#### **4. The Graph (Decentralized Network)**

```bash
# Deploy to The Graph Network (Mainnet)
graph deploy --node https://api.thegraph.com/deploy/ blocklotto/mainnet

# Subgraph URL
https://api.thegraph.com/subgraphs/name/blocklotto/mainnet
```

**Indexing:**
- Decentralized network (no single point of failure)
- Curated with GRT
- Monitoring via Graph Explorer

### **Security en Producción**

#### **1. Smart Contract Security**

```solidity
// Emergency pause
function pause() external onlyRole(PAUSER_ROLE) {
    _pause();
    emit EmergencyPause(msg.sender, block.timestamp);
}

// Timelock on upgrades
modifier timelock() {
    require(block.timestamp >= upgradeTimestamp, "Timelock active");
    _;
}

// Multi-sig ownership
// Gnosis Safe: 3/5 required
// Signatories: Founders + Advisors
```

#### **2. API Security**

```javascript
// Rate limiting
const rateLimit = require('express-rate-limit');

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100, // 100 requests por IP
  message: 'Too many requests'
});

app.use('/api/', apiLimiter);

// DDoS protection (Cloudflare)
// WAF rules
// Bot detection
```

#### **3. Secrets Management**

```bash
# Vercel Environment Variables (encrypted)
# Nunca commitear secrets a Git
# Rotar secrets cada 90 días

# Sensitive operations require MFA
# Hardware wallet para deployments
# Separate hot/cold wallets
```

### **Monitoring en Producción**

#### **Application Monitoring**

```javascript
// Sentry for error tracking
Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: 'production',
  tracesSampleRate: 0.1, // 10% de requests
  beforeSend(event, hint) {
    // Filtrar información sensible
    delete event.request.cookies;
    return event;
  }
});
```

#### **Blockchain Monitoring (Tenderly)**

```javascript
// Tenderly Alerts
{
  "alerts": [
    {
      "name": "Large Prize Claim",
      "conditions": {
        "event": "PrizeClaimed",
        "amount": "> 10000 MATIC"
      },
      "actions": ["slack", "pagerduty"]
    },
    {
      "name": "Contract Paused",
      "conditions": {
        "function": "pause"
      },
      "actions": ["email", "sms"]
    }
  ]
}
```

#### **Infrastructure Monitoring**

```yaml
# Prometheus + Grafana
metrics:
  - name: api_response_time
    type: histogram
    buckets: [0.1, 0.5, 1, 2, 5]
  
  - name: active_users
    type: gauge
  
  - name: ticket_purchases
    type: counter
  
  - name: prize_claims
    type: counter

# Alerting
alerts:
  - name: HighErrorRate
    condition: error_rate > 5%
    channels: [pagerduty, slack]
  
  - name: SlowQueries
    condition: p95_latency > 2s
    channels: [slack]
```

### **Uptime & SLA**

```
Target SLA: 99.9% uptime
Allowed Downtime: 43 minutes/month

Monitoring:
├─ Uptime Robot (external)
├─ StatusPage.io (public status)
└─ PagerDuty (on-call rotation)
```

---

## 🔄 CI/CD PIPELINE

### **Pipeline Overview**

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│   CODE   │ -> │   TEST   │ -> │  STAGE   │ -> │   PROD   │
│  COMMIT  │    │   AUTO   │    │  MANUAL  │    │  MANUAL  │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
     │               │                │                │
     │               │                │                │
   GitHub       GitHub Actions    Vercel          Vercel
   Push         CI Tests          Preview         Production
```

### **GitHub Actions Workflow**

```yaml
# .github/workflows/deploy.yml
name: Deploy Pipeline

on:
  push:
    branches: [main, staging, develop]
  pull_request:
    branches: [main]

jobs:
  # JOB 1: Lint & Test
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Type check
        run: npm run type-check
      
      - name: Unit tests
        run: npm run test
      
      - name: Coverage
        run: npm run coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
  
  # JOB 2: Build Frontend
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Build
        run: npm run build
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build
          path: .next/
  
  # JOB 3: Smart Contract Tests
  contracts:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install Foundry
        uses: foundry-rs/foundry-toolchain@v1
      
      - name: Run Forge tests
        run: forge test -vvv
      
      - name: Gas report
        run: forge test --gas-report
  
  # JOB 4: Security Scan
  security:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Run Slither
        uses: crytic/slither-action@v0.3.0
        with:
          target: 'contracts/'
      
      - name: Run npm audit
        run: npm audit --production
  
  # JOB 5: Deploy to Staging (auto)
  deploy-staging:
    needs: [build, contracts, security]
    if: github.ref == 'refs/heads/staging'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Vercel (Staging)
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          scope: blocklotto
          alias-domains: staging.blocklotto.io
  
  # JOB 6: Deploy to Production (manual approval)
  deploy-production:
    needs: [build, contracts, security]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://app.blocklotto.io
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Vercel (Production)
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
          scope: blocklotto
      
      - name: Notify Slack
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Production deployment completed!'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### **Smart Contract Deployment Pipeline**

```yaml
# .github/workflows/deploy-contracts.yml
name: Deploy Smart Contracts

on:
  workflow_dispatch:
    inputs:
      network:
        description: 'Network to deploy to'
        required: true
        type: choice
        options:
          - mumbai
          - polygon

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: actions/setup-node@v3
      
      - name: Install dependencies
        run: npm ci
      
      - name: Compile contracts
        run: npx hardhat compile
      
      - name: Run tests
        run: npx hardhat test
      
      - name: Deploy contracts
        env:
          NETWORK: ${{ github.event.inputs.network }}
          PRIVATE_KEY: ${{ secrets.DEPLOYER_PRIVATE_KEY }}
        run: npx hardhat run scripts/deploy.js --network $NETWORK
      
      - name: Verify contracts
        env:
          ETHERSCAN_API_KEY: ${{ secrets.ETHERSCAN_API_KEY }}
        run: npx hardhat run scripts/verify.js --network $NETWORK
      
      - name: Update contract addresses
        run: |
          node scripts/update-addresses.js
          git config user.name github-actions
          git config user.email github-actions@github.com
          git add contracts/addresses.json
          git commit -m "Update contract addresses for $NETWORK"
          git push
```

### **Deployment Checklist**

```markdown
## Pre-Deployment
- [ ] All tests passing (unit, integration, E2E)
- [ ] Security audit completed
- [ ] Code review approved (2+ reviewers)
- [ ] Changelog updated
- [ ] Environment variables configured
- [ ] Database migrations ready

## Deployment
- [ ] Deploy to staging first
- [ ] Run smoke tests on staging
- [ ] Get stakeholder approval
- [ ] Schedule maintenance window (if needed)
- [ ] Deploy to production
- [ ] Run smoke tests on production

## Post-Deployment
- [ ] Verify all features working
- [ ] Check error rates (Sentry)
- [ ] Monitor performance (response times)
- [ ] Update status page
- [ ] Notify team in Slack
- [ ] Document any issues
```

---

## 🏗️ INFRAESTRUCTURA COMO CÓDIGO

### **Terraform para Infrastructure**

```hcl
# terraform/main.tf
terraform {
  required_providers {
    vercel = {
      source = "vercel/vercel"
      version = "~> 0.15"
    }
  }
  
  backend "s3" {
    bucket = "blocklotto-terraform-state"
    key    = "production/terraform.tfstate"
    region = "us-east-1"
  }
}

# Vercel Project
resource "vercel_project" "blocklotto" {
  name      = "blocklotto"
  framework = "nextjs"
  
  git_repository = {
    type = "github"
    repo = "blocklotto/frontend"
  }
  
  environment = [
    {
      key    = "NEXT_PUBLIC_CHAIN_ID"
      value  = "137"
      target = ["production"]
    },
    {
      key    = "NEXT_PUBLIC_CHAIN_ID"
      value  = "80001"
      target = ["preview"]
    }
  ]
}

# Cloudflare DNS
resource "cloudflare_record" "app" {
  zone_id = var.cloudflare_zone_id
  name    = "app"
  value   = "cname.vercel-dns.com"
  type    = "CNAME"
  proxied = true
}

# Supabase Database (via API)
resource "supabase_project" "blocklotto_prod" {
  organization_id = var.supabase_org_id
  name           = "blocklotto-production"
  database_password = var.db_password
  region         = "us-east-1"
  
  settings {
    db_size = "medium"
    db_version = "15"
  }
}
```

### **Docker para Consistencia**

```dockerfile
# Dockerfile
FROM node:18-alpine AS base

# Dependencies
FROM base AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Builder
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Runner
FROM base AS runner
WORKDIR /app
ENV NODE_ENV production

COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
CMD ["node", "server.js"]
```

### **Docker Compose para Dev**

```yaml
# docker-compose.yml
version: '3.8'

services:
  hardhat:
    image: ethereum/hardhat:latest
    ports:
      - "8545:8545"
    command: npx hardhat node
    
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: blocklotto_dev
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: devpass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
  
  graph-node:
    image: graphprotocol/graph-node:latest
    ports:
      - "8000:8000"
      - "8020:8020"
    depends_on:
      - postgres
      - hardhat
    environment:
      postgres_host: postgres
      postgres_db: graph-node
      ipfs: 'https://ipfs.network.thegraph.com'
      ethereum: 'localhost:http://hardhat:8545'

volumes:
  postgres_data:
```

---

## 🔐 SEGURIDAD Y ACCESOS

### **Control de Accesos**

```yaml
# Matriz de permisos
Roles:
  - Admin (Founders): Full access
  - Developer: Dev + Staging
  - QA: Staging only
  - Support: Production read-only

Environments:
  Production:
    - Deploy: Admin only (with approval)
    - Database: Admin read/write, Support read-only
    - Secrets: Admin only
    - Monitoring: All roles
    
  Staging:
    - Deploy: Developer + Admin
    - Database: Developer + QA
    - Secrets: Developer + Admin
    
  Development:
    - Full access: All developers
```

### **Secrets Management**

```bash
# .env.example (template - nunca incluir valores reales)
# Blockchain
POLYGON_MAINNET_RPC=
POLYGON_MUMBAI_RPC=
DEPLOYER_PRIVATE_KEY=

# APIs
ALCHEMY_API_KEY=
INFURA_API_KEY=
ETHERSCAN_API_KEY=
POLYGONSCAN_API_KEY=

# Database
DATABASE_URL=
REDIS_URL=

# Services
SENTRY_DSN=
MIXPANEL_TOKEN=
SENDGRID_API_KEY=

# The Graph
GRAPH_API_KEY=

# Vercel
VERCEL_TOKEN=
```

**Herramientas:**
- GitHub Secrets (encrypted)
- Vercel Environment Variables
- AWS Secrets Manager (opcional para enterprise)
- 1Password Team (shared secrets)

### **Rotación de Secrets**

```bash
# Política de rotación
Database passwords: 90 días
API keys: 180 días
Deployment keys: 90 días
Hot wallet private keys: 30 días (rotar a nueva wallet)

# Script de rotación
./scripts/rotate-secrets.sh production
```

---

## 📊 MONITOREO Y OBSERVABILIDAD

### **Stack de Monitoring**

```
┌─────────────────────────────────────────────┐
│          OBSERVABILITY STACK                │
├─────────────────────────────────────────────┤
│                                             │
│  Logs                                       │
│  ├─ Vercel Logs (Frontend)                  │
│  ├─ Railway Logs (Backend)                  │
│  └─ CloudWatch (AWS services)               │
│                                             │
│  Metrics                                    │
│  ├─ Vercel Analytics                        │
│  ├─ Mixpanel (Product analytics)            │
│  └─ Prometheus + Grafana                    │
│                                             │
│  Errors                                     │
│  ├─ Sentry (Application errors)             │
│  └─ Tenderly (Blockchain events)            │
│                                             │
│  Uptime                                     │
│  ├─ Uptime Robot                            │
│  └─ StatusPage.io                           │
│                                             │
│  APM                                        │
│  └─ Vercel Speed Insights                   │
│                                             │
└─────────────────────────────────────────────┘
```

### **Dashboards Clave**

#### **1. Application Dashboard**

```
Metrics:
├─ Response Time (p50, p95, p99)
├─ Error Rate (%)
├─ Active Users (realtime)
├─ Requests per second
└─ Database queries performance
```

#### **2. Business Dashboard**

```
KPIs:
├─ Tickets sold (24h, 7d, 30d)
├─ Revenue (USD)
├─ Active users (DAU, WAU, MAU)
├─ Conversion rate (visitor → buyer)
└─ Average ticket value
```

#### **3. Blockchain Dashboard (Tenderly)**

```
Events:
├─ TicketPurchased
├─ DrawCompleted
├─ PrizeClaimed
├─ RandomnessRequested
└─ ContractPaused (alert)

Transactions:
├─ Success rate
├─ Gas used
├─ Failed transactions (investigate)
```

### **Alerting Rules**

```javascript
// PagerDuty integration
const alerts = {
  critical: {
    errorRate: "> 5%",
    responseTime: "> 5s (p95)",
    uptime: "< 99%",
    contractPaused: true,
    largePrizeClaim: "> $10,000"
  },
  warning: {
    errorRate: "> 1%",
    responseTime: "> 2s (p95)",
    slowQueries: "> 1s",
    lowBalance: "< 10 MATIC (hot wallet)"
  }
};

// On-call rotation
// Week 1: Dev A
// Week 2: Dev B
// Week 3: Dev C
```

---

## 🆘 DISASTER RECOVERY

### **Backup Strategy**

```yaml
Databases:
  Frequency: Daily (automated)
  Retention: 30 days
  Type: Full backup + WAL archiving
  Storage: AWS S3 (encrypted)
  Test Restore: Monthly
  
Smart Contracts:
  Immutable: Cannot be backed up
  Source Code: GitHub (multiple copies)
  Deployment Scripts: Version controlled
  
Application Code:
  Git Repository: GitHub (primary)
  Mirror: GitLab (secondary)
  Local: Developer machines
  
Configuration:
  Infrastructure as Code: Terraform (in Git)
  Environment Variables: Documented + encrypted backups
  DNS: Cloudflare (automatic backups)
```

### **Incident Response Plan**

```markdown
## Severity Levels

### P0 - Critical (Production Down)
- Response Time: Immediate
- On-call: Paged immediately
- Communication: Status page + Twitter
- Examples: Site down, contract paused, funds at risk

### P1 - High (Major Feature Down)
- Response Time: < 30 minutes
- On-call: Notified
- Communication: Status page
- Examples: Ticket purchasing broken, prize claiming fails

### P2 - Medium (Degraded Performance)
- Response Time: < 2 hours
- Team: Slack notification
- Communication: Internal only
- Examples: Slow loading, some images missing

### P3 - Low (Minor Issue)
- Response Time: Next business day
- Team: Ticket created
- Communication: None
- Examples: Typo, visual glitch
```

### **Recovery Procedures**

#### **Database Restore**

```bash
# 1. Identify backup
aws s3 ls s3://blocklotto-backups/postgres/

# 2. Download backup
aws s3 cp s3://blocklotto-backups/postgres/backup_20260224.sql ./

# 3. Stop application (prevent writes)
vercel env rm DATABASE_URL production

# 4. Restore
psql $DATABASE_URL < backup_20260224.sql

# 5. Verify
psql $DATABASE_URL -c "SELECT COUNT(*) FROM tickets;"

# 6. Resume application
vercel env add DATABASE_URL production
```

#### **Smart Contract Emergency Pause**

```bash
# Via Gnosis Safe (3/5 multi-sig)
# 1. Connect to Safe
# 2. Call pause() function
# 3. Approve transaction (3 signatories)
# 4. Execute
# 5. Announce on status page + Twitter

# Unpause after fix
# Same process for unpause()
```

#### **Frontend Rollback**

```bash
# Vercel instant rollback
vercel rollback <previous-deployment-url> --prod

# Alternative: Redeploy previous commit
git checkout <previous-commit>
vercel --prod
```

---

## 💰 COSTOS ESTIMADOS

### **Infraestructura Mensual**

```
DESARROLLO (Mensual):
├─ GitHub Team: $0 (Free for public repos)
├─ Vercel Hobby: $0 (Free tier)
├─ Alchemy Free Tier: $0
├─ The Graph Hosted: $0
└─ TOTAL DEV: $0/mes

STAGING (Mensual):
├─ Vercel Pro: $20
├─ Alchemy Growth: $50
├─ Supabase Pro: $25
├─ The Graph Hosted: $0
├─ Railway (Redis): $5
└─ TOTAL STAGING: $100/mes

PRODUCCIÓN (Mensual):
├─ Vercel Pro: $20
├─ Alchemy Growth: $200 (higher usage)
├─ Supabase Pro: $75 (larger DB)
├─ The Graph Network: $100 (curation)
├─ Cloudflare Pro: $20
├─ Railway Pro: $20
├─ Sentry Business: $80
├─ Tenderly Pro: $75
├─ StatusPage.io: $29
├─ PagerDuty: $21/user x 3 = $63
├─ Domain: $2
├─ Chainlink VRF: ~$200 (variable)
├─ Chainlink Automation: ~$100 (variable)
└─ TOTAL PRODUCTION: $984/mes

TOTAL MENSUAL: $1,084/mes
TOTAL ANUAL: ~$13,000/año
```

### **Costos One-Time**

```
Auditoría (CertiK): $35,000
Auditoría (OpenZeppelin): $20,000
Bug Bounty Pool: $50,000
Legal Setup: $10,000
────────────────────────────
TOTAL ONE-TIME: $115,000
```

### **Optimizaciones de Costos**

1. **Usar Free Tiers Máximo**: Alchemy, Vercel, etc tienen tiers generosos
2. **The Graph**: Hosted service gratis mientras esté disponible
3. **Caching Agresivo**: Reducir RPC calls
4. **Indexing Selectivo**: Solo eventos críticos
5. **Monitoring**: Start con free tiers, upgrade según necesidad

---

## ✅ CHECKLIST DE IMPLEMENTACIÓN

### **Fase 1: Setup Básico (Semana 1)**

```markdown
- [ ] Crear repositorios GitHub (frontend, contracts)
- [ ] Configurar Vercel project
- [ ] Setup Alchemy accounts (Mumbai + Polygon)
- [ ] Crear Supabase projects (dev, staging, prod)
- [ ] Configurar dominios (blocklotto.io)
- [ ] Setup GitHub Actions básicos
```

### **Fase 2: Ambiente Local (Semana 1-2)**

```markdown
- [ ] Hardhat config con networks
- [ ] Docker Compose para servicios locales
- [ ] Mock de Chainlink VRF
- [ ] Scripts de deploy local
- [ ] Documentation para nuevo devs
```

### **Fase 3: CI/CD (Semana 2-3)**

```markdown
- [ ] GitHub Actions para tests
- [ ] Automated deployments a staging
- [ ] Manual approval para production
- [ ] Status checks en PRs
- [ ] Code coverage reporting
```

### **Fase 4: Monitoring (Semana 3-4)**

```markdown
- [ ] Sentry integration
- [ ] Tenderly setup
- [ ] Analytics (Mixpanel)
- [ ] Uptime monitoring
- [ ] Alerting configured
```

### **Fase 5: Security (Semana 4-5)**

```markdown
- [ ] Secrets rotation procedures
- [ ] Access control matrix implemented
- [ ] Multi-sig wallets created
- [ ] Backup procedures tested
- [ ] Incident response plan documented
```

### **Fase 6: Production Ready (Semana 5-6)**

```markdown
- [ ] Load testing completed
- [ ] Security audit passed
- [ ] All tests green
- [ ] Monitoring dashboards ready
- [ ] On-call rotation set
- [ ] Go-live checklist completed
```

---

## 📚 DOCUMENTACIÓN REQUERIDA

```
/docs
├── /environments
│   ├── local-setup.md
│   ├── testing-guide.md
│   ├── staging-guide.md
│   └── production-ops.md
├── /deployment
│   ├── contracts-deployment.md
│   ├── frontend-deployment.md
│   └── rollback-procedures.md
├── /monitoring
│   ├── dashboards.md
│   ├── alerts.md
│   └── incident-response.md
└── /security
    ├── access-control.md
    ├── secrets-management.md
    └── audit-reports/
```

---

## 🎯 CONCLUSIÓN

Esta estrategia de ambientes provee:

✅ **Separación Clara**: Dev, Test, Staging, Prod bien diferenciados
✅ **Automatización**: CI/CD end-to-end
✅ **Seguridad**: Multi-sig, secrets management, audits
✅ **Observabilidad**: Monitoring completo
✅ **Resiliencia**: Backups, disaster recovery
✅ **Escalabilidad**: Infrastructure as Code

**Próximos Pasos:**
1. Aprobar estrategia con equipo
2. Comenzar implementación Fase 1
3. Documentar cada ambiente
4. Entrenar equipo en procedimientos

---

*Documento generado para BlockLotto*
*Versión: 1.0*
*Fecha: Febrero 2026*
