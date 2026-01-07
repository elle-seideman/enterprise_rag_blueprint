# Enterprise RAG Blueprint

This repository is a build space for:
- Retrieval-Augmented Generation (RAG)
- Enterprise-ready AI patterns
- Azure-based architecture
- LangChain-based pipelines

This repo will evolve with implementation ingestion, indexing, querying,
evaluation, and deployment patterns.

# Enterprise Azure RAG Chatbot — End-to-End Checklist

## Purpose
This project implements a Retrieval-Augmented Generation (RAG) chatbot using Azure services, designed for enterprise security, governance, and scalability. The system separates indexing, retrieval, generation, identity, and deployment concerns in line with best practices.

---

## Key Note
In enterprise Azure RAG systems, the application is only a consumer. Indexing, storage, identity, and governance live outside the app.

## PHASE 0 — Foundations (Clocal)

### Goals
- Understand RAG architecture before deploying to Azure
- Build a working local prototype
- Establish clean code separation

### Must Understand
- What RAG is: retrieve → ground → generate
- Indexing vs querying
- Embeddings vs LLMs
- Vector store vs retriever
- Why enterprise systems separate pipelines from apps

### Deliverables
- Local RAG app works
- Repo initialized in Git
- `.env` ignored, `.env.example` committed
- README documents architecture concepts

---

## PHASE 1 — Identity & Access (Critical for Enterprise)

### Concepts to Understand
- Azure Entra ID (Azure AD)
- Managed Identity
- RBAC (Role-Based Access Control)
- Scope (subscription, resource group, resource)

### Key Principle
> Applications authenticate as identities, not with secrets.

### What Replaces `.env` in Production
- Managed Identity
- RBAC permissions
- Key Vault (when secrets are unavoidable)

### Deliverables
- Ability to explain:
  - How the app authenticates without API keys
  - How access is controlled via RBAC
- Permissions requested or approved

---

## PHASE 2 — Document Storage

### Azure Service
- Azure Blob Storage

### Responsibilities
- Store company documents (PDFs, policies, procedures)
- Organize by container (e.g. policies, guidelines)
- Enforce access via RBAC

### App Permissions
- Read-only access (typically)

### Deliverables
- Documents uploaded to Blob Storage
- No documents bundled inside the app

---

## PHASE 3 — Indexing Pipeline (Offline)

### Azure Service
- Azure AI Search

### What Indexing Does
- Reads documents from Blob Storage
- Chunks text
- Generates embeddings (via Azure OpenAI)
- Stores vectors in a search index

### Concepts for use
- Index
- Data source
- Indexer
- Skillset 
- Index refresh strategies

### Deliverables
- Search index exists
- Documents are searchable
- Indexing runs independently of the app

---

## PHASE 4 — Retrieval (Runtime)

### Azure Service
- Azure AI Search

### Runtime Flow
1. User asks a question
2. Question is embedded
3. Search index returns top-k relevant chunks
4. Optional: MMR or reranking improves relevance

### Concepts to Understand
- Similarity search
- MMR (Maximal Marginal Relevance)
- Metadata filtering (department, date, role)

### Deliverables
- App retrieves relevant context only
- Retrieval is fast and cost-efficient

---

## PHASE 5 — Generation (Answer Creation)

### Azure Service
- Azure OpenAI

### Responsibilities
- Combine system prompt + retrieved context + user question
- Generate grounded answers
- Use conservative parameters (low temperature)

### Enterprise Constraints
- Hallucination avoidance
- “I don’t know” is acceptable
- No training on company data
- Data remains within Azure boundary

### Deliverables
- Deterministic, grounded responses
- Prompt behavior reviewed and controlled

---

## PHASE 6 — Application Layer

### App Responsibilities
- Authenticate using managed identity
- Query Azure AI Search
- Call Azure OpenAI for generation
- Manage chat/session state
- Render UI

### App Must NOT
- Build or rebuild indexes
- Store documents
- Embed large corpora at runtime
- Contain secrets

### Deliverables
- Thin, clean application
- Clear separation of concerns

---

## PHASE 7 — Deployment

### Common Targets
- Azure App Service
- Azure Container Apps
- Azure Functions (API-based)

### Key Configuration
- Managed Identity enabled
- App settings used (no `.env`)
- Logging and monitoring configured

### Deliverables
- App deploys without secrets
- Access enforced via RBAC

---

## PHASE 8 — Governance & Operations

### Expectations
- Logging (without sensitive data)
- Monitoring and alerts
- Access reviews
- Index refresh procedures
- Prompt and output review

### Deliverables
- Auditable system
- Operationally supportable solution


