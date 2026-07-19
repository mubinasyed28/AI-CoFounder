# APPENDICES & REFERENCES
## AI Co-Founder Platform — Master Document

*This closes out the Master Document. The appendices below consolidate and make concrete the material that Parts A–E referenced but deferred — the unabridged Requirements Traceability Matrix, full API/dataset catalogs, actual DDL, an OpenAPI skeleton, the prompt/tool-schema libraries, and the reference indices. Nothing here introduces a new architectural decision; every entry traces back to a specific chapter in Parts A–E, and that chapter is the authority if any conflict is ever found.*

---

## Appendix A — Full Requirements Traceability Matrix (Expanded)

This expands Part B Table B15.1 into the complete chain: **PRD Feature (Part A) → Requirement (Part B) → Architecture Component (Part C) → Design Component (Part D) → Test Case (Part D Ch. 37)**. Part B Ch. 10 intentionally published a representative, fully-specified subset of the FR catalog per numeric range rather than every sequential ID (flagged explicitly at the time) — this appendix is the designated home for the complete set as it is filled in during implementation. The table below shows the full structure and a complete worked set for one range (Authentication, FR-001–FR-010) as the template every other range follows identically.

**Table App.A.1 — RTM Column Schema**

| Column | Description |
|---|---|
| Requirement ID | FR-XXX or NFR-XXX (Part B) |
| PRD Source | Part A section/feature/AC (e.g., §5.1.2 AC2) |
| Architecture Component | Part C chapter/section implementing the AI behavior, if applicable |
| Design Component | Part D chapter/section implementing the system behavior |
| Test Case ID | Part D Ch. 37 test case |
| Status | Not Started / In Progress / Done (per Part E §42.4 Definition of Done) |

**Table App.A.2 — Worked Example: Authentication Range (FR-001–FR-010)**

| Requirement ID | PRD Source | Architecture Component | Design Component | Test Case ID | Status |
|---|---|---|---|---|---|
| FR-001 | Part A §9.3 | — (no AI component) | Part D §31.2 (Auth Flow), §27.3 (Supabase Auth) | TC-AUTH-001 | Not Started |
| FR-002 | Security baseline | — | Part D §31.2 | TC-AUTH-002 | Not Started |
| FR-003 | Part A §5.6 | — | Part D §30.2 (ERD: COMPANY_WORKSPACE) | TC-AUTH-003 | Not Started |
| FR-004 | Part A §9.3 | — | Part D §30.5 (mandatory workspace filter), Table D33.1 | TC-AUTH-004 | Not Started |
| FR-005 | Part A §4.2 (Post-MVP) | — | Part D §30.3 (WORKSPACE_MEMBER schema) | TC-AUTH-005 | Not Started |
| FR-006 | Security baseline | — | Part D §31.2 | TC-AUTH-006 | Not Started |
| FR-007 | Ch. 41 Compliance | — | Part D §30.8 (backup/purge interaction), Part E §41.2 | TC-AUTH-007 | Not Started |
| FR-008 | Part D Ch. 33 | — | Part D §33.2 (audit log) | TC-AUTH-008 | Not Started |
| FR-009 | Part D Ch. 33 | — | Part D §29.6 (rate limiting) | TC-AUTH-009 | Not Started |
| FR-010 | Part D Ch. 31 | — | Part D §31.2 (JWT + refresh rotation) | TC-AUTH-010 | Not Started |

**Recommendation:** As each remaining range (Conversational Agent FR-021–FR-045, RAG FR-046–FR-065, Forecasting FR-066–FR-080, Documents FR-081–FR-095, Multi-Agent FR-096–FR-110, Multimodal FR-111–FR-125, Search FR-126–FR-135, Notifications FR-136–FR-145, Admin FR-146–FR-155, and all NFR-XXX) is implemented, it is added to this appendix following the exact column schema above — this keeps one single, queryable source of truth rather than requirement status scattered across issue trackers, code comments, and this document independently.

---

## Appendix B — Complete API Comparison Tables (Consolidated)

Consolidates every API comparison table introduced across Parts A–C into one reference, so an engineer choosing/verifying a provider never has to hunt across three documents.

**Table App.B.1 — LLM Provider Comparison (consolidated from Part A Table A5.1 / Part C Table C17.1)**

| API | Free Tier Limits | Latency | Quality | Context Length | Reasoning Ability | Cost | Recommendation |
|---|---|---|---|---|---|---|---|
| Google Gemini (Free API) | Generous daily quota, Flash-tier | Low | High | Very long (1M-token class available) | Strong | $0 | Primary — long-context/synthesis |
| Groq API | High req/min, generous quota | Very low | Good–High | Model-dependent | Good | $0 | Primary — low-latency/high-frequency |
| Mistral AI API | Moderate quota | Low | Good | Moderate (~32K) | Good | $0 | Secondary fallback |
| Hugging Face Inference API | Free, rate-limited | Variable | Variable | Model-dependent | Variable | $0 | Fallback / specialized open models |
| OpenRouter | Aggregated free-model quotas | Variable | Variable | Variable | Variable | $0 | Fallback aggregator |
| Ollama (local) | Unlimited (compute-bound) | Hardware-dependent | Lower | Model-dependent | Lower | $0 | Guaranteed last-resort fallback |

**Table App.B.2 — Search API Comparison (consolidated from Part A Table A5.3 / Part C Table C23.1)**

| API | Free Tier Limits | Latency | Quality | Recommendation |
|---|---|---|---|---|
| Tavily Search API | Monthly search-credit allowance, agent-optimized | Low | High (structured) | Primary |
| DuckDuckGo Search (unofficial) | Effectively unlimited, no formal SLA | Low–Moderate | Moderate | High-volume fallback |
| SerpAPI (free tier) | Small monthly quota (~100/mo typical) | Low | High (real Google results) | Reserved for high-value queries |

**Table App.B.3 — Embedding Model Comparison (consolidated from Part C Table C19.1)**

| Model | Dimension | Strengths | Recommendation |
|---|---|---|---|
| BAAI BGE (base/small) | 384–1024 | Best accuracy-per-compute (MTEB) | Primary |
| Jina Embeddings | Varies | Long-context embedding | Secondary (long-doc use case) |
| E5 | Varies | Strong general retrieval | Viable alternative |
| Nomic Embed | Varies | Permissive license, actively maintained | Fallback/alternative |
| Sentence-Transformers (MiniLM) | 384 | Extremely lightweight | Fallback for constrained compute |

**Table App.B.4 — Vector DB Comparison (consolidated from Part C Table C19.2)**

| Vector DB | Filtering | Scalability | Ops Overhead | Recommendation |
|---|---|---|---|---|
| ChromaDB | Basic metadata | Small–medium scale | Very low | Primary (MVP) |
| FAISS | None built-in | Excellent ANN performance | Higher (build persistence yourself) | Narrow internal use (large-scale similarity search) |
| Qdrant CE | Rich payload filtering | Good, production-scale | Moderate | Recommended scale-up target |
| Weaviate CE | Rich filtering + native hybrid search | Good | Moderate–higher | Alternative scale-up target |

**Table App.B.5 — Forecasting Model Comparison (consolidated from Part A §5.4.5 / Part C Table C20.1)**

| Model | Data Requirement | Recommendation |
|---|---|---|
| Prophet | Minimal (few data points) | Primary/MVP default |
| LightGBM / XGBoost / CatBoost | Moderate–high (6+ months) | Phase 2 "advanced mode" |
| Temporal Fusion Transformer | High (many series/long history) | Deferred — future cross-company feature only |
| N-BEATS | High | Deferred — same disposition as TFT |

**Table App.B.6 — Multimodal Model Comparison (consolidated from Part C Ch. 21)**

| Domain | Model | Recommendation |
|---|---|---|
| Speech-to-text | Whisper vs. Faster-Whisper | Faster-Whisper (recommended) |
| OCR | PaddleOCR vs. Tesseract | PaddleOCR (structured docs) / Tesseract (plain text) |
| Vision-language | Florence-2 / Qwen2.5-VL / Gemma Vision / Llama Vision | Qwen2.5-VL primary; Gemma/Llama Vision fallback (shares LLM Gateway chain) |

**Table App.B.7 — Agent Framework Comparison (consolidated from Part C Table C18.1)**

| Framework | Recommendation |
|---|---|
| LangGraph | Primary orchestration backbone |
| CrewAI | Prototyping only, not deployed |
| AutoGen | Embedded as one node type (critic-pass debate pattern) |
| DSPy | Selective prompt optimization, orthogonal to orchestration |

---

## Appendix C — Complete Dataset Catalog (Consolidated)

Consolidates Part A Table A5.2 and Part C Tables C24.1–C24.5 into one lookup, organized by feature domain.

**Table App.C.1 — Dataset Catalog Index**

| Domain | Dataset(s) | Primary Source Tier (per Part C §24.1 priority order) | Owning Table |
|---|---|---|---|
| Idea validation | Startup success/failure compilations; industry classification sets | Kaggle (1) / Hugging Face (2) | Part A Table A5.2 |
| Market/industry sizing | World Bank Open Data; Google Dataset Search aggregated reports | Government open-data (7) / Google Dataset Search (4) | Part C Table C24.1 |
| Financial forecasting | Kaggle SaaS/startup financial compilations; synthetic time series | Kaggle (1) / Synthetic generation (10) | Part C Table C24.2 |
| Intent classification (NLP) | HF general-purpose intent-classification sets, remapped | Hugging Face (2) | Part C Table C24.3 |
| OCR | Kaggle/HF scanned-document and receipt/invoice benchmark sets | Kaggle (1) / Hugging Face (2) | Part C Table C24.4 |
| Vision/diagram understanding | HF document-understanding/diagram-captioning sets | Hugging Face (2) | Part C Table C24.5 |

**Recommendation:** Any new feature introduced after this Master Document's initial release should append a new row here following the same domain → dataset → source-tier → owning-table structure, and should apply the Part C §24.1 priority order (Kaggle → HF → UCI → Google Dataset Search → GitHub → academic → gov open data → Common Crawl → public APIs → synthetic) before defaulting to synthetic generation.

---

## Appendix D — Full Database Schema DDL Scripts

Concrete PostgreSQL DDL implementing the ERD in Part D Figure D30.1 and schema table Part D Table D30.1. This is the literal migration content Alembic (Part D §30.7) should version-control.

```sql
-- Appendix D: Core Schema DDL (PostgreSQL)
-- Corresponds to Part D Figure D30.1 (ERD) and Table D30.1 (Schema Definitions)

CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE users (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email           VARCHAR(320) NOT NULL UNIQUE,
    auth_provider_id VARCHAR(255) NOT NULL,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE company_workspaces (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    owner_user_id   UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name            VARCHAR(255) NOT NULL,
    stage           VARCHAR(64),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_workspaces_owner ON company_workspaces(owner_user_id);

CREATE TABLE workspace_members (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    workspace_id    UUID NOT NULL REFERENCES company_workspaces(id) ON DELETE CASCADE,
    user_id         UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    role            VARCHAR(32) NOT NULL CHECK (role IN ('owner','editor','viewer')),
    UNIQUE (workspace_id, user_id)
);

CREATE TABLE conversation_turns (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    workspace_id    UUID NOT NULL REFERENCES company_workspaces(id) ON DELETE CASCADE,
    role            VARCHAR(16) NOT NULL CHECK (role IN ('user','assistant')),
    content         TEXT NOT NULL,
    citations       JSONB DEFAULT '[]',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_turns_workspace_time ON conversation_turns(workspace_id, created_at);

CREATE TABLE knowledge_items (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    workspace_id    UUID NOT NULL REFERENCES company_workspaces(id) ON DELETE CASCADE,
    source_type     VARCHAR(64) NOT NULL,
    raw_content_ref VARCHAR(512),
    embedding_ref   VARCHAR(255),
    content_hash    VARCHAR(64) NOT NULL,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_knowledge_workspace ON knowledge_items(workspace_id);
CREATE INDEX idx_knowledge_hash ON knowledge_items(content_hash);

CREATE TABLE research_briefs (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    workspace_id    UUID NOT NULL REFERENCES company_workspaces(id) ON DELETE CASCADE,
    type            VARCHAR(64) NOT NULL CHECK (type IN ('validation','competitor')),
    content         JSONB NOT NULL,
    sources         JSONB DEFAULT '[]',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_briefs_workspace ON research_briefs(workspace_id);

CREATE TABLE forecast_runs (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    workspace_id    UUID NOT NULL REFERENCES company_workspaces(id) ON DELETE CASCADE,
    version         INTEGER NOT NULL,
    assumptions_json JSONB NOT NULL,
    output_json     JSONB NOT NULL,
    model_used      VARCHAR(64) NOT NULL,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE (workspace_id, version)
);

CREATE TABLE generated_documents (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    workspace_id    UUID NOT NULL REFERENCES company_workspaces(id) ON DELETE CASCADE,
    type            VARCHAR(64) NOT NULL,
    content_ref     VARCHAR(512),
    version         INTEGER NOT NULL,
    exported_formats JSONB DEFAULT '[]',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE (workspace_id, type, version)
);

CREATE TABLE milestones (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    workspace_id    UUID NOT NULL REFERENCES company_workspaces(id) ON DELETE CASCADE,
    title           VARCHAR(255) NOT NULL,
    status          VARCHAR(32) NOT NULL DEFAULT 'proposed' CHECK (status IN ('proposed','accepted','in_progress','done','rejected')),
    due_estimate    VARCHAR(64),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_milestones_workspace_time ON milestones(workspace_id, created_at);

CREATE TABLE jobs (
    -- Backs the PostgreSQL-based job queue, Part D §29.4
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    job_type        VARCHAR(64) NOT NULL,
    payload         JSONB NOT NULL,
    status          VARCHAR(32) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending','running','done','failed')),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_jobs_status ON jobs(status);

CREATE TABLE feature_flags (
    -- Part D §38.3
    key             VARCHAR(128) PRIMARY KEY,
    enabled         BOOLEAN NOT NULL DEFAULT false,
    rollout_percent INTEGER NOT NULL DEFAULT 0 CHECK (rollout_percent BETWEEN 0 AND 100)
);

CREATE TABLE audit_log (
    -- Part D §33.2, FR-008/FR-149
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    actor_user_id   UUID REFERENCES users(id),
    action          VARCHAR(128) NOT NULL,
    target_ref      VARCHAR(255),
    metadata        JSONB DEFAULT '{}',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_audit_actor_time ON audit_log(actor_user_id, created_at);
```

**Trade-off note:** `citations`, `sources`, `assumptions_json`, `output_json`, and `exported_formats` are stored as `JSONB` rather than fully normalized child tables. This is a deliberate schema-design trade-off: normalization would give stronger referential integrity on citation records, but JSONB keeps the schema simpler for a small team to reason about and migrate (Part D §30.7), at the cost of not being able to run efficient relational queries *across* citations (e.g., "find all outputs citing source X") without a JSONB-aware index (`GIN` index on the JSONB column) — flagged here as the concrete migration path if that query pattern becomes necessary.

---

## Appendix E — Full OpenAPI/Swagger Specification Reference

Per Part D §31.6, the canonical spec is auto-generated by FastAPI at `/api/v1/openapi.json`, not hand-maintained. This appendix pins the **skeleton structure** every release's generated spec is expected to conform to, so a manual review can quickly spot drift.

```yaml
openapi: 3.1.0
info:
  title: AI Co-Founder Platform API
  version: 1.0.0
  description: >
    REST API implementing the functional requirements specified in Part B
    (FR-001 through FR-155) of the Master Document.
servers:
  - url: /api/v1
paths:
  /auth/register:
    post:
      operationId: registerUser
      summary: Register a new user (FR-001)
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RegisterRequest'
      responses:
        '201':
          description: User created
        '409':
          description: Email already registered
  /workspaces:
    post:
      operationId: createWorkspace
      summary: Create a Company Workspace (FR-003)
      security: [{ bearerAuth: [] }]
      responses:
        '201':
          description: Workspace created
  /workspaces/{workspaceId}/messages:
    post:
      operationId: sendChatMessage
      summary: Send a chat message, streamed response (FR-021-FR-030)
      security: [{ bearerAuth: [] }]
      parameters:
        - $ref: '#/components/parameters/WorkspaceId'
      responses:
        '200':
          description: Streamed assistant response with citations
  /workspaces/{workspaceId}/documents:
    post:
      operationId: uploadDocument
      summary: Upload a document for RAG ingestion (FR-046-FR-049)
      security: [{ bearerAuth: [] }]
      requestBody:
        content:
          multipart/form-data:
            schema:
              type: object
              properties:
                file:
                  type: string
                  format: binary
      responses:
        '202':
          description: Ingestion job accepted
  /workspaces/{workspaceId}/research-briefs:
    post:
      operationId: requestResearchBrief
      summary: Request idea validation / competitor research (FR-126-FR-130)
      security: [{ bearerAuth: [] }]
      responses:
        '200':
          description: Structured brief with citations
  /workspaces/{workspaceId}/forecasts:
    post:
      operationId: runForecast
      summary: Submit assumptions, receive a versioned forecast (FR-066-FR-070)
      security: [{ bearerAuth: [] }]
      responses:
        '200':
          description: Forecast result (chart-ready JSON)
  /workspaces/{workspaceId}/documents/generate:
    post:
      operationId: generateDocument
      summary: Generate a pitch deck / document (FR-081-FR-083)
      security: [{ bearerAuth: [] }]
      responses:
        '200':
          description: Generated document draft
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
  parameters:
    WorkspaceId:
      name: workspaceId
      in: path
      required: true
      schema:
        type: string
        format: uuid
  schemas:
    RegisterRequest:
      type: object
      required: [email, password]
      properties:
        email:
          type: string
          format: email
        password:
          type: string
          minLength: 8
```

**Recommendation:** Treat any generated-spec drift from this skeleton (missing `operationId`, missing `security` on a workspace-scoped route, etc.) as a CI-checkable lint rule (Part D Fig. D34.1's lint stage) rather than a manual review burden.

---

## Appendix F — Prompt Template Library

Per Part C §17.3/17.4, every prompt is a versioned template, not an inline string. Representative templates below; the full library grows alongside the agent catalog (Part C §18.2).

**Template: `intent_classifier_v1`**
```
SYSTEM:
You are an intent router for a founder's AI co-founder platform.
Classify the user's message into exactly one of:
[general_chat, research_request, validation_request, forecast_request,
 document_request, planning_request].
Respond with ONLY the label, nothing else.

CONTEXT:
{recent_conversation_summary}

USER MESSAGE:
{user_message}
```

**Template: `research_agent_v2`**
```
SYSTEM:
You are the Research Agent for an AI co-founder platform (Part C §18.2).
You have run the following web searches and retrieved these results.
Summarize findings in your own words. For every factual claim, attach
a citation marker [S1], [S2], etc. matching the numbered sources below.
If a claim cannot be supported by a source, explicitly label it
"[unverified estimate]". Do not fabricate sources.

SOURCES:
{numbered_search_results}

USER REQUEST:
{user_request}

OUTPUT FORMAT (strict JSON):
{
  "market_size_estimate": {"value": "...", "citation": "S#"},
  "top_competitors": [{"name": "...", "citation": "S#"}],
  "trend_signal": {"direction": "growing|flat|declining", "citation": "S#"}
}
```

**Template: `validation_brief_repair_v1`** *(used when the primary generation's JSON fails schema validation, per Part C §17.3's retry-once policy)*
```
SYSTEM:
Your previous output failed to parse as valid JSON against this schema:
{json_schema}

Your previous output was:
{previous_output}

Return ONLY corrected, valid JSON matching the schema. No explanation.
```

**Template: `document_agent_pitch_deck_v1`**
```
SYSTEM:
You are the Document Agent (Part C §18.2). Generate a pitch deck outline
using ONLY the retrieved company context below — do not invent facts
not present in the context or clearly marked as a reasonable inference.
Structure: Problem, Solution, Market, Traction, Team, Ask.

RETRIEVED COMPANY CONTEXT:
{rag_retrieved_chunks}

USER REQUEST:
{user_request}
```

**Recommendation:** Every template above is stored as a separate versioned file (e.g., `prompts/research_agent/v2.txt`), never edited in place — a new version increments the suffix, and Part C §25.6's regression suite runs against both old and new versions before a version is promoted to default, so a prompt change is treated with the same rigor as a code change.

---

## Appendix G — Agent Tool/Function Schema Definitions

Per Part C §18.6, every tool an agent can call is a strictly-typed JSON Schema function definition, uniform across every provider in the LLM fallback chain.

```json
{
  "name": "retrieve_knowledge_base",
  "description": "Retrieve top-k relevant chunks from the workspace's Knowledge Base via RAG (Part C Ch. 19).",
  "parameters": {
    "type": "object",
    "properties": {
      "workspace_id": { "type": "string", "format": "uuid" },
      "query": { "type": "string" },
      "top_k": { "type": "integer", "default": 5, "minimum": 1, "maximum": 20 }
    },
    "required": ["workspace_id", "query"]
  }
}
```

```json
{
  "name": "web_search",
  "description": "Execute a web search via the Search Layer fallback chain (Part C Ch. 23.1: Tavily -> DuckDuckGo -> SerpAPI).",
  "parameters": {
    "type": "object",
    "properties": {
      "query": { "type": "string" },
      "max_results": { "type": "integer", "default": 5 }
    },
    "required": ["query"]
  }
}
```

```json
{
  "name": "run_financial_forecast",
  "description": "Compute a runway/revenue forecast (Part C Ch. 20).",
  "parameters": {
    "type": "object",
    "properties": {
      "workspace_id": { "type": "string", "format": "uuid" },
      "starting_cash": { "type": "number" },
      "monthly_burn": { "type": "number" },
      "historical_revenue": {
        "type": "array",
        "items": { "type": "object", "properties": {
          "month": { "type": "string" }, "revenue": { "type": "number" } } }
      }
    },
    "required": ["workspace_id", "starting_cash", "monthly_burn"]
  }
}
```

```json
{
  "name": "propose_milestone",
  "description": "Propose a roadmap milestone for user confirmation (Part C §18.7 human-in-the-loop checkpoint before persistence).",
  "parameters": {
    "type": "object",
    "properties": {
      "workspace_id": { "type": "string", "format": "uuid" },
      "title": { "type": "string" },
      "due_estimate": { "type": "string" }
    },
    "required": ["workspace_id", "title"]
  }
}
```

**Recommendation:** Any tool with a destructive or irreversible effect (e.g., a hypothetical future `delete_knowledge_item` tool) must be modeled with an explicit two-step schema (`propose_*` returning a pending action, then a separate `confirm_*` call gated on user approval) — never a single-step tool call — directly implementing Part C §18.7's human-in-the-loop principle at the schema level, not just as a policy note.

---

## Appendix H — Glossary (Expanded, Alphabetical)

| Term | Definition |
|---|---|
| Agent | A specialized LLM-driven component with a defined role (Research, Validation, Forecasting, Document, Planning, Critic) invoked by the Orchestrator (Part C Ch. 18.2). |
| BGE (BAAI General Embedding) | The primary open-source embedding model family used for RAG (Part C §19.3). |
| Company Workspace | The top-level data-isolation container for a single company's chat history, documents, forecasts, and roadmap (Part B §14.1). |
| Critic Agent | A Phase-2 agent that reviews multi-agent compound outputs against a quality checklist before final presentation (Part C §18.2, FR-100). |
| Definition of Done | The full completion checklist (tests, docs, RTM update, cost check) a feature must satisfy beyond merely merging code (Part E §42.4). |
| Feature Flag | A database-backed toggle enabling progressive rollout of Phase 2/3 features without long-lived branches (Part D §38.3). |
| Fallback Chain | The ordered sequence of providers (LLM or search) an agent falls through on failure/rate-limit, terminating in a guaranteed-available option (Ollama for LLM, Part C §17.2). |
| GraphRAG | Retrieval that combines vector-similarity search with structured Knowledge Graph queries (Part C §19.8). |
| Hybrid Search | Combining dense (embedding) and sparse (BM25) retrieval with score fusion (Part C §19.5). |
| Knowledge Base | The umbrella term for a workspace's persistent memory: the Vector Store + Knowledge Graph + raw stored documents/conversation turns (Part A §5.6). |
| Knowledge Graph | The Neo4j CE-backed structured entity/relationship store (companies, investors, milestones) complementing vector-based RAG (Part C §19.7). |
| LangGraph | The chosen agent-orchestration framework, modeling agent workflows as an explicit, inspectable state machine (Part C §18.1). |
| LLM Gateway | The internal abstraction layer that isolates all agent code from any specific LLM provider SDK, enabling the fallback chain and config-only provider swaps (Part C §17.2). |
| Modular Monolith | The chosen architecture style: one deployable backend service with enforced internal module boundaries, as opposed to microservices (Part D §27.2). |
| MoSCoW | Prioritization scale (Must/Should/Could/Won't-have) used throughout Part A/B to rank features and requirements. |
| Orchestrator / Supervisor Agent | The top-level agent that classifies intent and routes requests to specialized sub-agents (Part C §18.2). |
| RAG (Retrieval-Augmented Generation) | Grounding LLM generation in retrieved, relevant chunks of stored/searched content rather than relying purely on model memory (Part C Ch. 19). |
| RTM (Requirements Traceability Matrix) | The table linking every requirement to its PRD source, implementing architecture/design component, and test case (Part B Ch. 15, Appendix A). |
| STRIDE | The threat-modeling framework (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege) used in Part D §33.1. |
| Vector Store | The database storing chunk embeddings for semantic retrieval (ChromaDB at MVP, Qdrant CE at scale, Part C §19.4). |
| Workspace Isolation | The architectural guarantee that a user's data access is always scoped to workspaces they belong to, enforced at the abstraction layer, not per-call-site (Part D §30.5, FR-004). |
| Zero-Budget Constraint | The governing design principle that all recurring infrastructure cost must remain $0, filtered through every architectural decision in this Master Document (Part A §1.6). |

---

## Appendix I — Free-Tier Quota Reference Sheet

**This appendix is explicitly a living document, not a fixed reference** — every figure below is illustrative/structural and must be re-verified against each provider's live documentation before every release, per the recurring note repeated in Parts A–D's reference sections. A snapshot review date is recorded each time this table is updated.

**Table App.I.1 — Free-Tier Quota Snapshot Template**

| Provider | Quota Type | Last Verified Value | Last Verified Date | Verification Source (URL) |
|---|---|---|---|---|
| Gemini Free API | Requests/day (Flash-tier) | *(re-verify before each release)* | — | ai.google.dev docs |
| Groq API | Requests/minute | *(re-verify)* | — | console.groq.com/docs |
| Mistral AI API | Requests/month | *(re-verify)* | — | docs.mistral.ai |
| Hugging Face Inference API | Requests/hour | *(re-verify)* | — | huggingface.co/docs |
| Tavily Search API | Search credits/month | *(re-verify)* | — | docs.tavily.com |
| SerpAPI | Searches/month | *(re-verify)* | — | serpapi.com/pricing |
| Supabase (free project) | DB storage / row count / bandwidth | *(re-verify)* | — | supabase.com/pricing |
| Railway / Render / Fly.io | Compute-hour or usage-credit allowance | *(re-verify)* | — | respective pricing pages |
| GitHub Actions | Minutes/month (public vs. private repo) | *(re-verify)* | — | docs.github.com |

**Recommendation:** This table should be re-populated as one of the standing tasks in the Ch. 42.1 workflow (e.g., a recurring GitHub Issue tagged `quota-review`, checked before every milestone release per Part D §38.2's release checklist item 3), since it is the single most time-sensitive table in the entire Master Document.

---

## Appendix J — Environment Variables & Configuration Reference

**Table App.J.1 — Environment Variable Catalog**

| Variable | Purpose | Owning Component |
|---|---|---|
| `DATABASE_URL` | PostgreSQL connection string | Part D §30.7 (portable across hosting migration, NFR-011) |
| `SUPABASE_URL` / `SUPABASE_ANON_KEY` / `SUPABASE_SERVICE_ROLE_KEY` | Auth + Storage access | Part D §27.3, §31.2 |
| `GEMINI_API_KEY` | Gemini Free API access | Part C §17.2 (LLM Gateway) |
| `GROQ_API_KEY` | Groq API access | Part C §17.2 |
| `MISTRAL_API_KEY` | Mistral API access | Part C §17.2 |
| `HF_API_TOKEN` | Hugging Face Inference API access | Part C §17.2 |
| `OPENROUTER_API_KEY` | OpenRouter access | Part C §17.2 |
| `OLLAMA_HOST` | Local Ollama endpoint (default `http://localhost:11434`) | Part C §17.2 (guaranteed fallback) |
| `TAVILY_API_KEY` | Tavily Search access | Part C §23.1 |
| `SERPAPI_KEY` | SerpAPI access (reserved-quota use) | Part C §23.1 |
| `VECTOR_STORE_BACKEND` | `chromadb` \| `qdrant` — selects active Vector Store adapter | Part C §19.4, Part D §36.3 scale-out trigger |
| `NEO4J_URI` / `NEO4J_USER` / `NEO4J_PASSWORD` | Knowledge Graph connection | Part C §19.7 |
| `OBJECT_STORAGE_BACKEND` | `supabase` \| `minio` — selects active storage adapter | Part D §32.1 |
| `JWT_PUBLIC_KEY` | JWT signature verification | Part D §33.2 |
| `LANGSMITH_API_KEY` | LLM call tracing (optional, free tier) | Part D §35.3 |
| `FEATURE_FLAGS_DEFAULT_ROLLOUT` | Default rollout percent for new flags | Part D §38.3 |

**Recommendation:** All secrets above are injected via the hosting platform's secret manager and GitHub Actions Secrets (Part D §33.4) — a committed `.env.example` file (values redacted) documents every variable's presence without ever containing a real credential, and CI's secret-scanning step (Fig. D34.1) fails the build if a real-looking credential pattern is detected in a diff.

---

## Appendix K — Sample User Stories & Personas (Full Text)

Full persona text is defined in Part A §3.1; reproduced here as the standalone reference artifact a design or engineering session can pull up without re-opening Part A.

**Aisha — Student Technical Founder.** 21, computer-science undergraduate, building a SaaS side project into a startup candidate. Strong technical ability; limited exposure to go-to-market strategy, financial modeling, or investor communication. Operates on strictly $0 budget, already deploying her own product on free-tier infrastructure. Primary need: an AI system that can competently handle the "business side" she has not yet developed expertise in, while remaining transparent enough that she learns from its outputs rather than becoming dependent on an opaque black box.

**Marcus — Solo Indie Hacker.** 27, former corporate employee, building his second startup attempt after a first unsuccessful launch. A capable generalist working entirely without a team or advisor. Primary need: a research and planning partner that substitutes for the sanity-checking, idea-pressure-testing, and drafting support a real co-founder or team would normally provide.

**Priya — Non-Technical Co-Founder.** 24, on an MBA-track academic program, informally paired with a technical friend on a not-yet-incorporated venture. Owns the "business side" (market sizing, financial modeling, pitch materials) without formal training in any of these areas. Primary need: credible, defensible, evidence-grounded outputs she can present to mentors and investors without overstating expertise she does not have.

**Representative User Stories (traced to Part A Ch. 5 / Part B Ch. 10):**

- *As Aisha, I want the AI to validate my idea against real market data (not just its opinion) so I know whether to keep building before I invest more time.* → Part A §5.2, FR-130.
- *As Marcus, I want an agent to catch blind spots in my plan since I have no one else to review it with.* → Part A §5.7/§5.8, FR-096–FR-101.
- *As Priya, I want a financial forecast whose assumptions I can see and defend, not a black-box number.* → Part A §5.4, FR-069.

---

## Appendix L — Wireframe Gallery

Per Part A §6.3, low-fidelity wireframes for the core screens are produced in Excalidraw or draw.io (both free) and stored as image assets alongside this Master Document (e.g., `/docs/wireframes/`). This appendix is the index into that asset folder, not the wireframes themselves (which are visual artifacts, not markdown-representable content).

**Table App.L.1 — Wireframe Index**

| Screen | File (illustrative naming convention) | Corresponding IA Node (Part A Fig. A6.1) |
|---|---|---|
| Chat/Co-Founder home | `wireframes/01-chat-home.png` | Home/Chat |
| Knowledge Base browser | `wireframes/02-knowledge-base.png` | Knowledge Base |
| Research Brief viewer | `wireframes/03-research-brief.png` | Research Briefs |
| Financial Forecast viewer | `wireframes/04-forecast.png` | Financial Forecasts |
| Document Generator/editor | `wireframes/05-document-editor.png` | Documents |
| Roadmap/Task board | `wireframes/06-roadmap.png` | Roadmap/Tasks |

---

## Appendix M — Diagram Index (All Figures, Cross-Referenced)

**Table App.M.1 — Full Diagram Index**

| Figure ID | Title | Part | Chapter |
|---|---|---|---|
| Fig. A1.1 | Value Proposition Canvas | A | 1.4 |
| Fig. A3.1 | End-to-End User Journey Map | A | 3.3 |
| Fig. A6.1 | Information Architecture Diagram | A | 6.2 |
| Fig. A7.1 | Roadmap Timeline (Gantt) | A | 7.3 |
| Fig. B9.1 | System Context Diagram (Level 0) | B | 9.1 |
| Fig. B13.1 | Master Use Case Diagram | B | 13.1 |
| Fig. C16.1 | Master AI Architecture Diagram | C | 16.3 |
| Fig. C17.1 | LLM Routing & Fallback Flowchart | C | 17.2 |
| Fig. C18.1 | Agent State Machine (LangGraph Graph) | C | 18.4 |
| Fig. C19.1 | RAG Pipeline Data-Flow Diagram | C | 19.1 |
| Fig. C21.1 | Multimodal Ingestion Pipeline Diagram | C | 21.5 |
| Fig. D27.1 | High-Level System Architecture Diagram | D | 27.1 |
| Fig. D27.2 | Deployment Topology Diagram | D | 27.4 |
| Fig. D28.1 | Frontend Component Hierarchy Diagram | D | 28.5 |
| Fig. D29.1 | Backend Service Architecture Diagram | D | 29.2 |
| Fig. D29.2 | Sequence Diagram: Chat Request Lifecycle | D | 29.7 |
| Fig. D29.3 | Sequence Diagram: Document Upload → RAG Ingestion | D | 29.7 |
| Fig. D29.4 | Sequence Diagram: Forecast Generation Request | D | 29.7 |
| Fig. D30.1 | Entity-Relationship Diagram (ERD) | D | 30.2 |
| Fig. D31.1 | Auth Flow Sequence Diagram | D | 31.2 |
| Fig. D32.1 | File Upload & Storage Flow Diagram | D | 32.2 |
| Fig. D33.1 | Security Architecture Diagram | D | 33 |
| Fig. D34.1 | CI/CD Pipeline Diagram | D | 34.2 |
| Fig. D35.1 | Observability Stack Architecture Diagram | D | 35.5 |
| Fig. D36.1 | Scaling Decision Tree | D | 36.4 |
| Fig. D37.1 | Test Pyramid Diagram | D | 37.1 |
| Fig. D38.1 | Deployment Pipeline Diagram | D | 38.4 |
| Fig. E42.1 | Git Branching & Review Workflow | E | 42.1 |
| Fig. E42.2 | Definition of Done Flow | E | 42.4 |

---

## Appendix N — Table Index (All Tables, Cross-Referenced)

**Table App.N.1 — Full Table Index** *(major tables; every numbered table across Parts A–E is included by this same referencing convention)*

| Table ID | Title | Part | Chapter |
|---|---|---|---|
| Table A2.1 / A2.2 | Competitor Comparison / SWOT | A | 2 |
| Table A3.1 | JTBD Statements | A | 3.2 |
| Table A4.1 | Zero-Budget Feasibility Checklist | A | 4.5 |
| Table A5.0–A5.3 | Feature Priority Matrix / API & Dataset Tables | A | 5 |
| Table A7.1 | Release Milestones & Exit Criteria | A | 7.4 |
| Table B10.1 | FR Traceability Table | B | 10 |
| Table B11.1 | NFR Matrix | B | 11 |
| Table B12.1 | External API Interface Contracts | B | 12.3 |
| Table B13.1 | Use Case Catalog | B | 13.2 |
| Table B14.1 | Data Dictionary | B | 14.1 |
| Table B15.1 | Abridged RTM | B | 15.2 |
| Table C17.1 | LLM Provider Comparison | C | 17.1 |
| Table C18.1 | Agent Framework Comparison | C | 18.1 |
| Table C19.1–C19.3 | Embedding / Vector DB / KG Comparisons | C | 19 |
| Table C20.1 | Forecasting Model Comparison | C | 20.2 |
| Table C21.1 | Multimodal Model Comparison | C | 21 |
| Table C23.1 | Search API Comparison | C | 23.1 |
| Table C24.1–C24.5 | Dataset Catalogs | C | 24 |
| Table C25.1 (implicit) | AI Evaluation Metrics | C | 25.3 |
| Table D27.1 | Technology Stack Decision Table | D | 27.2 |
| Table D30.1–D30.2 | Schema Definition / Vector Collection Schema | D | 30 |
| Table D31.1 | Full Endpoint Catalog | D | 31.3 |
| Table D33.1 | STRIDE Threat Model | D | 33.1 |
| Table D34.1 | Hosting Platform Comparison | D | 34.4 |
| Table D36.1 | Free-Tier Limits Reference | D | 36.3 |
| Table D37.1 | Test Case Matrix | D | 37.5 |
| Table D39.1 | Risk Register | D | 39.1 |
| Table D40.1–D40.2 | Cost Confirmation / Migration Roadmap | D | 40 |
| Table E41.1–E41.3 | License Compatibility / Privacy Checklist / ToS Considerations | E | 41 |

---

## Consolidated References (All Parts)

*This is the single merged bibliography for the entire Master Document. Every citation below appeared in at least one Part's individual References section; duplicates are collapsed here. Provider documentation entries carry the same standing caveat repeated throughout Parts A–E: free-tier terms, quotas, and pricing change independent of this document's revision cycle and must be re-verified against the live source before any release (see Appendix I).*

**Standards & Methodologies**
1. IEEE Std 830-1998 — Recommended Practice for Software Requirements Specifications.
2. ISO/IEC/IEEE 29148:2018 — Systems and software engineering — Life cycle processes — Requirements engineering.
3. W3C Web Content Accessibility Guidelines (WCAG) 2.1.
4. OWASP Top 10 and STRIDE threat-modeling methodology.
5. SPDX License List / OSI (Open Source Initiative) license definitions.
6. Conventional Commits / trunk-based development methodology.
7. Architecture Decision Record (ADR) format (Michael Nygard's original template).

**Product & Design Methodology**
8. Osterwalder, A. & Pigneur, Y. — *Value Proposition Design*.

**AI/ML Frameworks & Models**
9. LangGraph, LangChain, CrewAI, AutoGen, DSPy — official documentation.
10. Google Gemini API, Groq API, Mistral AI API, Hugging Face Inference API, OpenRouter, Ollama — provider documentation.
11. Prophet (Meta), LightGBM, XGBoost, CatBoost, Temporal Fusion Transformer, N-BEATS — original papers/documentation.
12. BAAI BGE, Jina Embeddings, E5, Nomic Embed, Sentence-Transformers — model cards and MTEB benchmark results.
13. ChromaDB, FAISS, Qdrant, Weaviate — official documentation.
14. Neo4j Community Edition, Memgraph — official documentation.
15. Whisper, Faster-Whisper (CTranslate2) — original paper and implementation repository.
16. PaddleOCR, Tesseract OCR — official documentation.
17. Florence-2, Qwen2.5-VL, Gemma Vision, Llama Vision — model cards/technical reports.
18. PyTorch Geometric, DGL — official documentation.
19. Meta Llama Community License, Google Gemma Terms of Use, Alibaba Qwen/Tongyi Qianwen License, DeepSeek model licenses — provider-published license texts.

**Search & Data**
20. Tavily Search API, DuckDuckGo Search libraries, SerpAPI — provider documentation.
21. Kaggle, Hugging Face Datasets, UCI Machine Learning Repository, Google Dataset Search, World Bank Open Data — dataset sources.

**Infrastructure & Hosting**
22. FastAPI, Next.js, PostgreSQL, Alembic — official documentation.
23. Supabase, Railway, Render, Fly.io, Vercel, Netlify, Cloudflare Pages, GitHub Pages — platform documentation.
24. GitHub Actions — official documentation.
25. Prometheus, Grafana, LangSmith, MLflow — official documentation.
26. Locust (load testing) — official documentation.
27. MinIO — official documentation.

**Regulatory Frameworks**
28. GDPR (EU Regulation 2016/679).
29. India's Digital Personal Data Protection Act (DPDP), 2023.
30. CCPA (California Consumer Privacy Act).

---

*This concludes the Master Document — Parts A through E and Appendices A through N, plus the Consolidated References. Every architectural claim, model choice, and requirement throughout is cross-referenced back to the chapter that owns the decision; this Appendices section is deliberately non-authoritative on anything beyond formatting/indexing — if any conflict is ever found between an Appendix entry and its owning Part, the owning Part (A–E) governs.*
