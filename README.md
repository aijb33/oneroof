# OneRoof: AI-First Listing Operating System

OneRoof is a comprehensive PropTech SaaS designed to transform the fragmented real estate marketing workflow into a unified, AI-driven experience. Traditionally, real estate agents spend hours manually coordinating virtual staging, drafting property descriptions, checking for Fair Housing compliance, and building individual property websites—often across four or five different platforms. This fragmentation leads to high operational overhead, inconsistent branding, and significant legal risk regarding fair housing violations. OneRoof solves this by centralizing the entire "Listing Lifecycle" into a single intelligent platform that automates asset creation while enforcing strict regulatory guardrails at the edge.

## Core Features

*   **AI Virtual Staging:** High-fidelity room transformation that preserves architectural integrity (windows, flooring, structures) while adding stylized furniture and decor.
*   **Smart Copywriter:** Generates captivating, professional listing descriptions tailored to specific property features and neighborhood vibes.
*   **Compliance Guard:** A real-time auditing engine that scans listing text for Fair Housing Act (FHA) violations, MLS rule breaks, and ethical steering language.
*   **Neighborhood Intelligence:** Automated aggregation of local schools, transit scores, and "lifestyle" amenities (coffee, parks, gyms) via geospatial data.
*   **Microsite Builder:** Instant deployment of high-conversion, single-property websites featuring interactive before/after sliders and lead capture forms.
*   **Agent Hub:** A centralized dashboard for managing property "Vitals," marketing assets, and buyer leads across an entire listing portfolio.
*   **Association-First Architecture:** Multi-tenant infrastructure designed to be whitelabeled and deployed by large real estate boards or brokerages.
*   **Verified Tour Pass:** A standardized digital entry system to track and verify unrepresented buyers during property tours.

## Technical Stack

*   **React 18 / TypeScript:** Core frontend framework providing a type-safe, component-driven architecture for complex UI state management.
*   **Vite:** High-performance build tool and development server used to optimize asset delivery and developer experience.
*   **Supabase (PostgreSQL):** Backend-as-a-Service providing authentication, real-time database capabilities, and row-level security (RLS).
*   **Cloudflare Workers:** Edge computing layer used for secure AI API proxying, credit-based rate limiting, and Stripe webhook orchestration.
*   **Tailwind CSS:** Utility-first styling engine for building a responsive, high-fidelity "Staging Design System."
*   **Google Gemini 2.5 Flash:** Multimodal LLM utilized for advanced image analysis, alt-text generation, and architectural preservation logic.
*   **OpenRouter API:** Unified interface for accessing high-reasoning text models (e.g., GLM-4.5) for sophisticated copywriting tasks.
*   **Nano Banana API:** Specialized real estate computer vision service for optimized geometry and lighting in virtual staging.
*   **Stripe:** Enterprise-grade billing infrastructure for subscription management and dynamic credit top-ups.
*   **Leaflet.js:** Interactive mapping library for visualizing neighborhood context and property proximity data.

## Architecture Overview

OneRoof utilizes a "Thin Client, Heavy Edge" architecture. User inputs—such as raw property photos or street addresses—are captured via the React frontend and transmitted with a Supabase JWT to a Cloudflare Worker. This edge worker serves as the secure orchestration layer; it validates the user's remaining "Credits," retrieves global AI configuration settings from Supabase, and dispatches parallel requests to various AI providers (Gemini for images, OpenRouter for text). The resulting data is then "cleaned" (e.g., parsing AI JSON or resizing R2-hosted images) before being persisted to the database and returned to the client. This flow ensures that sensitive API keys are never exposed to the browser and that compute-heavy tasks like compliance scanning are offloaded from the main UI thread.

## AI Capabilities

### 1. Agent Orchestration Patterns
The platform employs a **Linear Orchestrator** pattern (see `CampaignWizard.tsx`) that sequences multiple specialized AI "tools." When a user launches a campaign, the orchestrator triggers a Neighborhood Research Tool (Location Intelligence), feeds that output into a Creative Copywriter Tool (LLM), and finally passes the text to a Compliance Auditor. This mimics a multi-step human workflow while maintaining state across the entire chain.

### 2. Compliance Scanning Logic
OneRoof uses a **Hybrid Compliance Engine**. Local scanning (regex/deterministic) identifies common prohibited terms (e.g., "walking distance," "family-friendly"), while a Semantic LLM scan detects nuanced violations like "familial steering" or "demographic targeting" that simple keyword filters often miss.

### 3. Vector Search Implementation
The system is architected for **Semantic Property Retrieval** using `pgvector` in the Supabase PostgreSQL layer. This allows the platform to store listing descriptions as high-dimensional embeddings, enabling agents to find "comparable listings" based on architectural style and neighborhood "vibe" rather than just bed/bath counts.

### 4. LLM Integrations
The application features a **Provider-Agnostic LLM Plane**. Through a centralized `configService`, administrators can toggle the global model provider (Gemini vs. OpenRouter) without modifying component-level code. This enables seamless switching between models based on cost, latency, or performance requirements.

## Setup & Environment Variables

To run OneRoof locally, the following environment variables are required in your `.env` or Cloudflare Wrangler configuration:

**Frontend (Vite):**
*   `VITE_SUPABASE_URL`: Your Supabase project URL.
*   `VITE_SUPABASE_ANON_KEY`: Your Supabase anonymous public key.

**Backend (Cloudflare Worker):**
*   `SUPABASE_SERVICE_ROLE_KEY`: Admin access for credit deduction and global config.
*   `GEMINI_API_KEY`: Google AI Studio key for image/multimodal tasks.
*   `OPENROUTER_API_KEY`: Access key for diverse LLM text generation.
*   `NANO_BANANA_API_KEY`: Specialized staging API key.
*   `STRIPE_SECRET_KEY`: Backend key for processing payments.
*   `GOOGLE_SEARCH_API_KEY`: For neighborhood data aggregation.
*   `GOOGLE_SEARCH_CX`: Google Custom Search Engine ID.
