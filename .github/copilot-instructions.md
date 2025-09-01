# ChatBB - Brazilian Biodiversity Chat Assistant (DwC2JSON V5.0)

ChatBB is a Brazilian biodiversity AI assistant that combines Astro.js web application with TypeScript data processing scripts for MongoDB integration. The system processes biodiversity data from multiple sources and provides an AI chat interface for querying Brazilian flora, fauna, and occurrence data.

Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.

## Working Effectively

### Environment Setup and Dependencies
- Install Node.js v20.19.4 or later: `curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - && sudo apt-get install -y nodejs`
- Install Deno v2.x for data processing scripts: `curl -fsSL https://deno.land/install.sh | sh && export PATH="$HOME/.deno/bin:$PATH"`
- Install zip utility for data processing: `sudo apt update && sudo apt install zip`

### Core Build and Development Commands
- **NEVER CANCEL any build or test commands** - All commands complete quickly (under 30 seconds)
- Navigate to web application: `cd web/`
- Install dependencies: `npm install` -- takes ~14 seconds. Set timeout to 120+ seconds.
- Build application: `npm run build` -- takes ~13 seconds. Set timeout to 60+ seconds.
- Run development server: `npm run dev` -- starts in <1 second on http://localhost:4321/
- Run production server: `node dist/server/entry.mjs` -- NOT the npm preview command (requires Deno)

### Data Processing Scripts (Deno)
- Run flora data update: `deno run -A src/flora.ts [DWCA_URL]`
- Run fauna data update: `deno run -A src/fauna.ts [DWCA_URL]`
- Run occurrence data update: `deno run -A src/ocorrencia.ts [DWCA_URL]`
- These scripts require MongoDB connection via MONGO_URI environment variable

### Web Application Commands
- Start cache cron job: `npm run start-cache-cron`
- Run dashboard cache job: `npm run cache-dashboard` (requires .env file)
- Check formatting: `npx prettier --check src/` (will show formatting issues)
- Fix formatting: `npx prettier --write src/` (ALWAYS run before committing)
- TypeScript compilation check: `npx tsc --noEmit` (may show unused variable warnings)

## Database Requirements
- **CRITICAL**: Application requires MongoDB connection
- Copy `.env.example` to `.env` and configure `MONGO_URI`
- Example: `MONGO_URI=mongodb://localhost:27017/your_database_name`
- Without proper MongoDB configuration, web application will fail to start properly

## Validation and Testing
- **No automated test suite available** - manual testing required
- Always validate web application starts: Access http://localhost:4321/ after running `npm run dev`
- Check TypeScript compilation: `npx tsc --noEmit` (may show warnings, but should not error)
- Verify formatting: `npx prettier --check src/`
- **MANUAL VALIDATION SCENARIOS**:
  1. **Homepage**: http://localhost:4321/ should load with link to taxa search
  2. **Chat Interface**: http://localhost:4321/chat should load ChatBB AI interface
  3. **Taxa Search**: http://localhost:4321/taxa should load species search interface
  4. **Dashboard**: http://localhost:4321/dashboard should load data visualization dashboard
  5. **API Health**: http://localhost:4321/api/health should return JSON status
  6. **Tree View**: http://localhost:4321/tree should load taxonomic tree browser
  7. **Map**: http://localhost:4321/mapa should load species occurrence map
- **Database-dependent features**: Chat, taxa search, and dashboard require MongoDB connection
- Build succeeds and production server starts on port 4321

## Project Structure
```
/
├── .github/workflows/        # CI/CD pipelines for MongoDB updates and Docker
├── src/                     # Deno TypeScript scripts for data processing
│   ├── fauna.ts            # Fauna data processing
│   ├── flora.ts            # Flora data processing  
│   ├── ocorrencia.ts       # Occurrence data processing
│   └── lib/dwca.ts         # Darwin Core Archive utilities
├── web/                    # Main Astro.js web application
│   ├── src/
│   │   ├── pages/          # Astro pages (chat.astro, dashboard.astro, etc.)
│   │   ├── components/     # React components
│   │   ├── scripts/        # TypeScript utilities
│   │   └── prompts/        # AI prompt configurations
│   ├── package.json        # Node.js dependencies and scripts
│   └── Dockerfile          # Production container build
└── tsconfig.json           # TypeScript configuration for Deno scripts
```

## Common Issues and Solutions
- **"deno: not found"**: Preview command uses Deno. Use `node dist/server/entry.mjs` instead
- **MongoDB connection errors**: Ensure .env file exists with valid MONGO_URI
- **TypeScript warnings**: `MapPage.tsx(15,6): 'Kingdom' is declared but never used` - harmless warning
- **Build warnings**: Large chunk size warnings are expected for Swagger UI components
- **Port conflicts**: Default port 4321 - change in astro.config.mjs if needed

## CI/CD Information
- Docker builds triggered on every push to main branch
- MongoDB update workflows run weekly via cron (Sunday 2:00-3:00 AM)
- Uses self-hosted runners for data processing jobs
- Production deployment via SSH to container registry

## Key Application Features
- AI chat interface for biodiversity queries
- Interactive dashboard with data visualizations  
- Taxonomic tree browser
- Species occurrence mapping
- API endpoints for programmatic access
- Integration with Flora do Brasil, Fauna do Brasil, and occurrence databases

## Development Workflow
1. Always work in the `web/` directory for application changes
2. Run `npm install` after pulling changes
3. Use `npm run dev` for development with hot reload
4. Check formatting with `npx prettier --check src/`
5. Build and test production: `npm run build && node dist/server/entry.mjs`
6. Ensure MongoDB connection configured for full functionality testing
7. Always validate TypeScript compilation: `npx tsc --noEmit`

## Performance Notes
- Web build completes in ~13 seconds
- Development server starts immediately (<1 second)
- Dependency installation takes ~14 seconds
- Production server starts in <2 seconds
- Data processing scripts timing depends on external data sources
- Prettier formatting takes ~2 seconds for all files