# Market Making Simulator

A real-time multiplayer trading simulation with **bundle markets** and **true value resolution**. Built with Node.js, Express, Socket.IO, Next.js, React, and TypeScript.

## Features

- 🎮 **Multi-game support** with 4-digit game codes
- 📊 **Click trading** directly on order book prices
- 📜 **Scrollable order books** with auto-extension
- 🎯 **Bundle markets** with custom formulas (e.g., `2*A + B + C`)
- 🏆 **Game resolution** with automatic bundle value calculation
- 📈 **Real-time leaderboard** with final P&L standings
- 👥 **Admin controls** for market management
- 💼 **Position tracking** and trade statistics

## Tech Stack

**Backend:**
- Node.js + Express
- Socket.IO for real-time communication
- Custom order matching engine with FIFO price-time priority

**Frontend:**
- Next.js 16 (React 19)
- TypeScript
- Tailwind CSS
- Socket.IO client

## Local Development

### Prerequisites
- Node.js 18+ (or latest LTS)
- npm or yarn

### Setup

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd MarketMakingSim-main
   ```

2. **Install dependencies**
   ```bash
   # Install server dependencies
   cd server && npm install && cd ..

   # Install client dependencies
   cd client && npm install && cd ..
   ```

3. **Start the application**

   Option A - Run both together (from root):
   ```bash
   npm run dev
   ```

   Option B - Run separately:
   ```bash
   # Terminal 1: Start server
   cd server && npm start

   # Terminal 2: Start client
   cd client && npm run dev
   ```

4. **Open your browser**
   - Navigate to `http://localhost:3000`

## Deployment

### Option 1: Deploy to Render (Recommended)

**Backend Deployment:**
1. Go to [render.com](https://render.com) and sign up/login
2. Click "New +" → "Web Service"
3. Connect your GitHub repository
4. Configure:
   - **Name**: market-sim-server
   - **Root Directory**: `server`
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Port**: 4000
5. Add environment variable: `PORT=4000`
6. Click "Create Web Service"
7. **Note the deployed URL** (e.g., `https://market-sim-server.onrender.com`)

**Frontend Deployment:**
1. Click "New +" → "Static Site"
2. Connect the same repository
3. Configure:
   - **Name**: market-sim-client
   - **Root Directory**: `client`
   - **Build Command**: `npm install && npm run build`
   - **Publish Directory**: `.next`
4. Add environment variable:
   - `NEXT_PUBLIC_SERVER_URL=https://market-sim-server.onrender.com`
5. Click "Create Static Site"

**Update Frontend to use environment variable:**
In `client/app/page.tsx`, update line 89:
```typescript
const s = io(process.env.NEXT_PUBLIC_SERVER_URL || "http://localhost:4000", { transports: ["websocket"] });
```

### Option 2: Deploy to Vercel + Render

**Backend on Render** (same as above)

**Frontend on Vercel:**
1. Go to [vercel.com](https://vercel.com) and sign up/login
2. Click "Add New..." → "Project"
3. Import your GitHub repository
4. Configure:
   - **Root Directory**: `client`
   - **Framework Preset**: Next.js
5. Add environment variable:
   - `NEXT_PUBLIC_SERVER_URL=https://market-sim-server.onrender.com`
6. Click "Deploy"

### Option 3: All-in-One on Render

Create a `render.yaml` in the root:
```yaml
services:
  - type: web
    name: market-sim-server
    env: node
    rootDir: server
    buildCommand: npm install
    startCommand: npm start
    envVars:
      - key: PORT
        value: 4000

  - type: web
    name: market-sim-client
    env: static
    rootDir: client
    buildCommand: npm install && npm run build
    staticPublishPath: .next
    envVars:
      - key: NEXT_PUBLIC_SERVER_URL
        fromService:
          name: market-sim-server
          type: web
          property: host
```

Then deploy via Render's Blueprint feature.

## How to Play

### For Admin:
1. Click "Admin" on landing page
2. Enter a 4-digit game code (e.g., "1234")
3. Enter admin password: `incorect`
4. Create markets:
   - **Basic markets**: Simple assets (A, B, C, etc.)
   - **Bundle markets**: Combinations with formulas (e.g., "2*A + B")
5. Click "Create & Enter"
6. Use admin controls:
   - Open/Close markets
   - Add events
   - **Resolve game** with true values

### For Players:
1. Click "Player" on landing page
2. Enter the game code
3. Enter your username
4. Start trading:
   - Click on prices to place orders
   - Click on "My" column to cancel orders
   - Use "Click" input to set order size
   - Watch positions and P&L

### Bundle Markets:
- Bundle markets have a **purple border**
- Formula shown under market name
- Trade independently from component markets
- Create arbitrage opportunities!

### Game Resolution:
1. Admin clicks "🏆 Resolve Game & Show Final Standings"
2. Enter true values for basic markets
3. Bundle values automatically calculated from formulas
4. Final leaderboard shows:
   - Player rankings with medals 🥇🥈🥉
   - Cash and position values
   - Final P&L

## Game Mechanics

### Order Matching
- **Price-Time Priority**: Best price first, earliest timestamp breaks ties
- **Partial Fills**: Orders can be partially filled
- **Click Trading**: Instant execution by clicking prices
- **Position Limits**: Enforced per market (default: ±100)

### Bundle Markets
- Define custom formulas with coefficients
- Examples:
  - `A+B` - Simple sum
  - `2*A + B` - Weighted bundle
  - `A+B+C` - Three-asset bundle
- Independent order books
- Separate position tracking
- True values auto-calculated on resolution

## API Events (Socket.IO)

### Client → Server
- `admin_create_game` - Create game with markets
- `player_join` - Join existing game
- `place_order` - Submit limit order
- `cancel_at_price` - Cancel orders at price level
- `click_trade` - Market order at specific price
- `admin_toggle_market` - Open/close market
- `admin_settle` - Settle market with price
- `admin_resolve_game` - Resolve with true values
- `admin_add_event` - Broadcast event message

### Server → Client
- `markets_meta` - Market metadata updates
- `book_snapshot` - Order book state
- `position` - Player position update
- `user_summary` - Trade statistics
- `pnl_implied` - Implied P&L calculation
- `trade` - Trade execution notification
- `game_resolved` - Final standings and true values
- `order_reject` - Order rejection (e.g., position limit)

## Configuration

### Server (port 4000)
Default settings in `server/index.js`:
- Admin password: `incorect`
- Max markets per game: 5

### Client (port 3000)
Settings in `client/app/page.tsx`:
- Default server: `http://localhost:4000`
- Theme: Dark/Light toggle
- Auto-scroll order books

## License

MIT License - feel free to use and modify!

## Contributing

Pull requests welcome! Please ensure code quality and test thoroughly.

## Support

For issues or questions, open an issue on GitHub.
