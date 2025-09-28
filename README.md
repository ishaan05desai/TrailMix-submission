# Project Trailmix - Ambient Productivity Intelligence

Your AI companion that observes your workflow and builds a personal productivity system.

## Local Development Setup

### Prerequisites
- Node.js 18+ 
- Docker and Docker Compose
- Chrome browser for extension testing

### Quick Start

1. **Clone and setup environment:**
   ```bash
   git clone <repository-url>
   cd TrailMix
   cp .env.example .env
   # Edit .env with your GEMINI_API_KEY
   ```

2. **Start local database:**
   ```bash
   docker compose up -d db
   ```

3. **Install dependencies:**
   ```bash
   npm install
   ```

4. **Setup database:**
   ```bash
   npm run db:push
   ```

5. **Start development server:**
   ```bash
   npm run dev
   ```

6. **Load Chrome Extension:**
   - Open Chrome and go to `chrome://extensions/`
   - Enable "Developer mode"
   - Click "Load unpacked" and select the `extension/` folder
   - In the extension popup, set API URL to `http://localhost:5000`

### Environment Variables

Create a `.env` file with:

```env
# Database
DATABASE_URL=postgresql://trailmix:trailmix@localhost:5432/trailmix

# Authentication
AUTH_MODE=local
SESSION_SECRET=your-session-secret-here

# AI Provider (Required)
AI_PROVIDER=gemini
GEMINI_API_KEY=your-gemini-api-key-here
GEMINI_MODEL=gemini-2.0-flash-exp
AI_TEMPERATURE=0.7
AI_MAX_TOKENS=4000

# Privacy Settings
AI_SEND_RAW=false

# CORS
ALLOWED_ORIGINS=http://localhost:5000,http://127.0.0.1:5000

# Server
PORT=5000
NODE_ENV=development
```

### Scripts

- `npm run dev` - Start development server (Express + Vite)
- `npm run build` - Build for production
- `npm start` - Start production server
- `npm run db:push` - Push database schema changes

### Architecture

- **Client**: React + Vite frontend
- **Server**: Express API + WebSocket
- **Database**: PostgreSQL with Drizzle ORM
- **Extension**: Chrome MV3 extension
- **AI**: Gemini API for content analysis

### Features

- **Passive Intelligence**: Chrome extension monitors page visits and content
- **Smart Capture**: Rectangular screenshot capture with AI analysis
- **Timeline**: Chronological feed of your digital activity
- **Tasks**: Auto-extracted and manually created tasks
- **Knowledge Graph**: Connected concepts and entities
- **Privacy Controls**: Local-first with optional cloud AI

### Troubleshooting

1. **Database connection issues**: Ensure Docker is running and `docker compose up -d db` completed
2. **Extension not connecting**: Check API URL in extension popup is `http://localhost:5000`
3. **AI analysis failing**: Verify `GEMINI_API_KEY` is set in `.env`
4. **CORS errors**: Check `ALLOWED_ORIGINS` includes your localhost URL

### Development Notes

- The app runs on port 5000 by default
- Extension communicates with local server via CORS
- All AI processing uses Gemini API (cloud-based)
- Database migrations are handled by Drizzle
- WebSocket provides real-time updates
