# Trailmix - Ambient Productivity Intelligence

## Overview

Trailmix is an ambient productivity intelligence system that automatically captures and organizes your digital workflow through a Chrome extension and web dashboard. The system uses AI to parse browsing activity, extract actionable tasks, and build a personal knowledge graph. It consists of a browser extension that monitors user activity locally, and a React web application that provides an intelligent dashboard for viewing timeline events, managing tasks, and exploring knowledge connections.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Framework**: React with TypeScript using Vite as the build tool
- **UI Framework**: Radix UI components with Tailwind CSS for styling
- **Routing**: Wouter for client-side navigation
- **State Management**: TanStack Query (React Query) for server state management
- **Authentication**: Session-based authentication integrated with Replit Auth
- **Component Library**: shadcn/ui component system with customizable themes

### Backend Architecture
- **Runtime**: Node.js with Express.js server
- **Language**: TypeScript with ES modules
- **API Design**: RESTful endpoints with WebSocket support for real-time updates
- **File Structure**: Monorepo with shared schema between client and server
- **Development**: Hot reload via Vite in development, esbuild for production builds

### Data Storage
- **Database**: PostgreSQL via Neon serverless with connection pooling
- **ORM**: Drizzle ORM with code-first schema definition
- **Schema**: Structured tables for users, timeline events, tasks, knowledge entities, and connections
- **Sessions**: PostgreSQL-backed session storage for authentication

### Chrome Extension Architecture
- **Manifest**: V3 extension with service worker background script
- **Content Monitoring**: Content scripts injected into all web pages for activity tracking
- **Local AI Processing**: On-device content analysis before sending structured data to backend
- **Privacy Controls**: Domain allowlists/blocklists, quiet hours, and private mode
- **Communication**: Message passing between content scripts, background, and popup

### AI Integration
- **Provider**: Google Gemini AI for content analysis and task extraction
- **Processing Strategy**: Local analysis in extension with selective data sharing
- **Features**: Automatic task detection, entity extraction, content summarization
- **Privacy**: Structured insights sent to backend, not raw content

### Authentication & Security
- **System**: Replit Auth with OpenID Connect
- **Session Management**: Secure HTTP-only cookies with PostgreSQL session store
- **API Security**: Authentication middleware on protected routes
- **Extension Security**: Content Security Policy and permission restrictions

## External Dependencies

### AI Services
- **Google Gemini AI**: Content analysis, task extraction, and natural language processing
- **Model**: Uses gemini-2.5-pro for structured JSON responses

### Database & Infrastructure
- **Neon Database**: Serverless PostgreSQL hosting with connection pooling
- **WebSocket**: Real-time communication for live updates

### Authentication
- **Replit Auth**: OpenID Connect provider for user authentication
- **connect-pg-simple**: PostgreSQL session store for Express sessions

### Development Tools
- **Replit Integrations**: Cartographer for code navigation, dev banner, and runtime error overlay
- **Vite Plugins**: React plugin, runtime error modal for development experience

### Browser APIs
- **Chrome Extension APIs**: Tabs, storage, scripting, webNavigation, desktopCapture
- **Permissions**: All URLs access for content monitoring with privacy controls

### UI & Styling
- **Radix UI**: Comprehensive component primitives for accessibility
- **Tailwind CSS**: Utility-first CSS framework with custom design tokens
- **Fonts**: Google Fonts integration (Inter, DM Sans, Fira Code, Geist Mono)