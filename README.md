# Bumm Frontend 2.0

**AI-Powered Solana Smart Contract Platform — Dashboard & dApp**

Next.js application with Solana wallet integration where users describe smart contracts in natural language, and the AI generates, audits, builds, and deploys them on Solana.

## Tech Stack

| Technology | Version | Purpose |
|-----------|---------|---------|
| Next.js | 15.5 | React framework (App Router) |
| React | 19.1 | UI library |
| TypeScript | 5.x | Type safety |
| Tailwind CSS | 4.x | Styling |
| Framer Motion | 12.x | Animations |
| GSAP | 3.13 | Advanced animations |
| Solana Wallet Adapter | 0.15+ | Wallet connectivity (Phantom, Solflare, etc.) |
| Solana Web3.js | 1.98+ | Blockchain interactions |
| Prism.js | 1.30 | Code syntax highlighting |
| Lucide React | 0.543+ | Icons |

## Architecture

```
┌──────────────────────────────────────────────────┐
│                  Next.js App                      │
│                                                   │
│  ┌─────────────┐    ┌──────────────────────────┐ │
│  │  Dashboard   │    │     API Proxy Layer       │ │
│  │  (Chat +     │───▶│  /api/backend/[...path]  │─┼──▶ Backend API
│  │   Editor)    │    │  (bypasses CORS)          │ │    (FastAPI)
│  └──────┬──────┘    └──────────────────────────┘ │
│         │                                         │
│  ┌──────▼──────┐    ┌──────────────────────────┐ │
│  │  useBummApi  │───▶│  services/bummService    │ │
│  │  (hook)      │    │  services/api (HTTP)     │ │
│  └──────┬──────┘    └──────────────────────────┘ │
│         │                                         │
│  ┌──────▼──────┐    ┌──────────────────────────┐ │
│  │  Solana      │    │  Mock API (fallback)     │ │
│  │  Wallet      │    │  lib/mockApi.ts          │ │
│  └─────────────┘    └──────────────────────────┘ │
└──────────────────────────────────────────────────┘
```

## Quick Start

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Open http://localhost:3000
```

### Production Build

```bash
npm run build
npm run start
```

### External Access (for mobile testing)

```bash
npm run dev:external    # Listens on 0.0.0.0:3000
npm run start:external  # Production on 0.0.0.0:3000
```

## Project Structure

```
src/
├── app/                          # Next.js App Router
│   ├── api/
│   │   └── backend/              # API proxy to FastAPI backend
│   │       ├── [...path]/route.ts   # Dynamic proxy for all endpoints
│   │       └── health/route.ts      # Health check proxy
│   ├── cookies/page.tsx          # Cookie policy
│   ├── privacy/page.tsx          # Privacy policy
│   ├── terms/page.tsx            # Terms of service
│   ├── globals.css               # Global styles (Tailwind)
│   ├── layout.tsx                # Root layout (providers, fonts)
│   └── page.tsx                  # Home page → Dashboard
│
├── components/
│   ├── dashboard/                # Main application screens
│   │   ├── Dashboard.tsx         # Main orchestrator (state, API calls)
│   │   ├── ChatScreen.tsx        # AI chat + code editor split view
│   │   └── LoginScreen.tsx       # Wallet connect screen
│   ├── ui/                       # Reusable UI components
│   │   ├── InteractiveCodeEditor.tsx  # Code editor with Prism.js
│   │   ├── BuildModal.tsx        # Build flow modal
│   │   ├── AuditModal.tsx        # Audit flow modal
│   │   ├── DeployModal.tsx       # Deploy flow modal
│   │   ├── WalletModal.tsx       # Wallet connect modal
│   │   ├── ProjectsList.tsx      # Sidebar project list
│   │   ├── SmartActionButton.tsx # Context-aware action button
│   │   ├── CreditHistory.tsx     # Credit transaction history
│   │   └── ...                   # Other UI components
│   ├── providers/
│   │   └── WalletProvider.tsx    # Solana wallet adapter setup
│   └── analytics/
│       └── GoogleTagManager.tsx  # GTM integration
│
├── config/
│   └── api.ts                    # API endpoints, status constants, helpers
│
├── hooks/
│   ├── useBummApi.ts             # Main API hook (generate, audit, build, deploy)
│   ├── useCredits.ts             # Credit system hook
│   ├── useAnalytics.ts           # Analytics tracking hook
│   └── useGSAPAnimations.ts     # GSAP animation hook
│
├── services/
│   ├── api.ts                    # HTTP client (ApiClient class with retries)
│   ├── bummService.ts            # Business logic (generate, audit, build, deploy)
│   └── creditService.ts          # Credit balance & purchasing
│
├── lib/
│   ├── api.ts                    # BummApiClient facade + types
│   ├── mockApi.ts                # Mock API fallback for dev/demo
│   └── utils.ts                  # Utility functions (cn, etc.)
│
├── types/
│   └── dashboard.ts              # TypeScript interfaces (Project, ChatMessage, etc.)
│
└── utils/
    └── generationCommands.ts     # Command detection for AI generation
```

## Key Features

### AI Chat Interface
Users type natural language descriptions like "Create an NFT marketplace" and the AI generates full Anchor/Rust smart contracts through the backend API.

### Contract Lifecycle
1. **Generate** — AI creates Anchor/Rust smart contract from description
2. **Audit** — AI security review of the contract code
3. **Build** — Compile the contract
4. **Deploy** — Deploy to Solana (Devnet/Mainnet)

### API Integration
- All API calls go through Next.js proxy (`/api/backend/*`) to avoid CORS
- Backend proxy routes forward to FastAPI at the configured `BACKEND_URL`
- Automatic fallback to Mock API when backend is unavailable
- Status polling with configurable intervals for long-running tasks
- `x-user-id` header authentication from Solana wallet registration

### Wallet Integration
- Solana Wallet Adapter with Phantom, Solflare, Brave, Coin98, Trust
- Wallet address used to create user on backend → returns UUID
- UUID stored in localStorage and sent as `x-user-id` header

### Credit System
- Operations (generate, audit, build, deploy) cost credits
- SOL/USDC payment for credit purchases
- Balance tracking and transaction history

## Backend Configuration

The backend URL is configured in `src/app/api/backend/[...path]/route.ts`:

```typescript
const BACKEND_URL = 'http://69.62.126.240:8081';
```

Change this to point to your backend instance.

## Deployment

### Vercel (recommended)

```bash
# Deploy to Vercel
vercel deploy
```

Configuration is in `vercel.json`.

### Docker / Self-hosted

```bash
npm run build
npm run start:external  # Listens on 0.0.0.0:3000
```

## Related Repositories

- **Backend API**: [bumm-ai/bumm-api-2.0](https://github.com/bumm-ai/bumm-api-2.0) — FastAPI backend with Claude/GPT integration
- **AI Audit Agent**: [bumm-ai/ai-audit-agent](https://github.com/bumm-ai/ai-audit-agent) — Autonomous security analysis
- **Main Repository**: [bumm-ai/bumm](https://github.com/bumm-ai/bumm) — Project documentation & hackathon submission

## License

[MIT License](LICENSE) — Copyright (c) 2025 bumm-ai
