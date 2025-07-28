# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Database Setup
- `./start-database.sh` - Start PostgreSQL database using Docker
- `pnpm run db:generate` - Run Prisma migrations in development
- `pnpm run db:migrate` - Deploy Prisma migrations
- `pnpm run db:push` - Push schema changes to database
- `pnpm run db:studio` - Open Prisma Studio for database inspection

### Development
- `pnpm run dev` - Start development server with Turbo (http://localhost:3000)
- `pnpm run build` - Build production application
- `pnpm run start` - Start production server
- `pnpm run preview` - Build and start production server

### Code Quality
- `pnpm run lint` - Run ESLint
- `pnpm run lint:fix` - Run ESLint with auto-fix
- `pnpm run typecheck` - Run TypeScript type checking
- `pnpm run check` - Run both linting and type checking
- `pnpm run format:check` - Check code formatting with Prettier
- `pnpm run format:write` - Format code with Prettier

### UI Components
- `pnpm run ui:add` - Add new shadcn/ui components

## Application Architecture

### Core Concept
This is a **Prompt MCP** application that allows users to create one-shot prompts and convert them into reusable tools for MCP (Model Context Protocol) clients like Claude Desktop, Cursor, or Cline.

### Tech Stack
- **Frontend**: Next.js 15 with React 19, TypeScript, Tailwind CSS, shadcn/ui
- **Backend**: tRPC for type-safe APIs, NextAuth.js for authentication
- **Database**: PostgreSQL with Prisma ORM
- **AI Integration**: AI SDK with support for OpenAI, Anthropic, Google, DeepSeek providers

### Database Schema
Core entities follow this hierarchy:
- **Organizations** - Top-level containers owned by users
- **Projects** - Groups tools under organizations
- **Tools** - Individual prompt templates with structured arguments
- **API Keys** - Scoped access tokens for tools within projects

### Key Architectural Patterns

#### Authentication & Authorization
- NextAuth.js with both OAuth (GitHub) and credentials providers
- Automatic organization creation on user signup
- Session-based authentication with JWT tokens
- Multi-user organization support with role management

#### API Structure
- **tRPC Routers**: `project`, `organization`, `tool`, `apiKey`, `user` (src/server/api/routers/)
- **REST Endpoints**: 
  - `/api/chat` - AI chat interface with tool integration
  - `/api/tools` - API key-based tool access for MCP clients
- **Type-safe API**: Full end-to-end type safety with tRPC

#### Tool System
Tools are structured prompts with:
- **Template interpolation**: `{variable}` syntax for dynamic content
- **Typed arguments**: JSON schema validation with Zod
- **AI Integration**: Automatic conversion to AI SDK tools for chat interface
- **MCP Export**: JSON format compatible with MCP clients

#### File Organization
- **App Router**: Next.js 15 app directory structure
- **Protected Routes**: `(protected)/` - requires authentication
- **Public Routes**: `(public)/` - login, signup, password reset
- **Components**: Reusable UI components in `/components`
- **Server Logic**: tRPC routers and database operations in `/server`

### Environment Setup
Requires `.env` file with:
- Database connection (`DATABASE_URL`)
- NextAuth configuration (`NEXTAUTH_SECRET`, `NEXTAUTH_URL`)
- OAuth providers (optional)
- Email service configuration (for password reset)

### Development Workflow
1. Start database with `./start-database.sh`
2. Install dependencies with `pnpm install` (runs migrations automatically)
3. Start development server with `pnpm run dev`
4. Use `pnpm run db:studio` for database inspection
5. Run `pnpm run check` before committing to ensure code quality