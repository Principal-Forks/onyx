# Onyx Architecture Diagram

## Main System Architecture

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'fontSize':'16px'}}}%%
graph TB
    %% Client Layer
    User[👤 User]

    %% Presentation Layer
    NextJS[🌐 Next.js Frontend<br/>React 19 · TypeScript]

    %% Gateway Layer
    Nginx[🔀 Nginx Proxy<br/>Load Balancer]

    %% API Layer
    FastAPI[⚙️ FastAPI Backend<br/>Python · REST API]

    %% Core Services Layer
    Auth[🔐 Authentication<br/>OAuth2 · OIDC · SAML]
    Chat[💬 Chat Engine<br/>LLM Orchestration]
    Index[📄 Indexing Pipeline<br/>Document Processing]
    Connect[🔌 Connectors<br/>40+ Data Sources]

    %% AI/ML Layer
    InferModel[🤖 Inference Server<br/>Chat & Embeddings]
    IndexModel[🧠 Indexing Server<br/>Vector Generation]
    LLM[☁️ LLM Providers<br/>OpenAI · Anthropic]

    %% Data Layer
    PG[(💾 PostgreSQL<br/>Primary DB)]
    Vespa[(🔍 Vespa<br/>Search Engine)]
    Redis[(⚡ Redis<br/>Cache & Queue)]
    Minio[(📦 MinIO<br/>File Storage)]

    %% Background Layer
    Celery[⚙️ Celery Workers<br/>Background Jobs]

    %% External Layer
    External[🌍 External Sources<br/>Slack · GitHub · etc]

    %% Main Flow
    User --> NextJS
    NextJS --> Nginx
    Nginx --> FastAPI

    %% API to Services
    FastAPI --> Auth
    FastAPI --> Chat
    FastAPI --> Index
    FastAPI --> Connect

    %% Services to AI
    Chat --> InferModel
    Index --> IndexModel
    InferModel --> LLM
    IndexModel --> LLM

    %% Services to Data
    FastAPI --> PG
    Chat --> PG
    Index --> PG
    Connect --> PG

    Chat --> Vespa
    Index --> Vespa

    Chat --> Redis
    FastAPI --> Redis

    Index --> Minio

    %% Background Processing
    FastAPI --> Celery
    Celery --> Index
    Celery --> Connect
    Celery --> PG

    %% External
    Connect --> External

    %% Styling
    classDef userStyle fill:#95a5a6,stroke:#7f8c8d,stroke-width:3px,color:#fff
    classDef frontendStyle fill:#3498db,stroke:#2980b9,stroke-width:3px,color:#fff
    classDef gatewayStyle fill:#2ecc71,stroke:#27ae60,stroke-width:3px,color:#fff
    classDef apiStyle fill:#2ecc71,stroke:#27ae60,stroke-width:3px,color:#fff
    classDef serviceStyle fill:#9b59b6,stroke:#8e44ad,stroke-width:3px,color:#fff
    classDef authStyle fill:#e74c3c,stroke:#c0392b,stroke-width:3px,color:#fff
    classDef aiStyle fill:#e67e22,stroke:#d35400,stroke-width:3px,color:#fff
    classDef dataStyle fill:#1abc9c,stroke:#16a085,stroke-width:3px,color:#fff
    classDef workerStyle fill:#f39c12,stroke:#e67e22,stroke-width:3px,color:#fff
    classDef externalStyle fill:#95a5a6,stroke:#7f8c8d,stroke-width:3px,color:#fff

    class User userStyle
    class NextJS frontendStyle
    class Nginx gatewayStyle
    class FastAPI apiStyle
    class Auth authStyle
    class Chat,Index,Connect serviceStyle
    class InferModel,IndexModel,LLM aiStyle
    class PG,Vespa,Redis,Minio dataStyle
    class Celery workerStyle
    class External externalStyle
```

---

## Simplified 3-Tier Architecture

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'fontSize':'18px'}}}%%
graph TB
    subgraph Tier1["🎨 PRESENTATION TIER"]
        A1[Next.js Web Application]
    end

    subgraph Tier2["⚙️ APPLICATION TIER"]
        B1[FastAPI Server]
        B2[Chat Engine]
        B3[Indexing Pipeline]
        B4[AI/ML Models]
    end

    subgraph Tier3["💾 DATA TIER"]
        C1[PostgreSQL Database]
        C2[Vespa Search]
        C3[Redis Cache]
        C4[MinIO Storage]
    end

    A1 --> B1
    B1 --> B2
    B1 --> B3
    B2 --> B4
    B3 --> B4

    B1 --> C1
    B2 --> C1
    B3 --> C1

    B2 --> C2
    B3 --> C2

    B2 --> C3
    B3 --> C3

    B3 --> C4

    style A1 fill:#3498db,stroke:#2980b9,stroke-width:4px,color:#fff
    style B1 fill:#2ecc71,stroke:#27ae60,stroke-width:4px,color:#fff
    style B2 fill:#9b59b6,stroke:#8e44ad,stroke-width:4px,color:#fff
    style B3 fill:#9b59b6,stroke:#8e44ad,stroke-width:4px,color:#fff
    style B4 fill:#e67e22,stroke:#d35400,stroke-width:4px,color:#fff
    style C1 fill:#1abc9c,stroke:#16a085,stroke-width:4px,color:#fff
    style C2 fill:#1abc9c,stroke:#16a085,stroke-width:4px,color:#fff
    style C3 fill:#e74c3c,stroke:#c0392b,stroke-width:4px,color:#fff
    style C4 fill:#f1c40f,stroke:#f39c12,stroke-width:4px,color:#000
```

---

## Chat Flow (Request → Response)

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'fontSize':'16px'}}}%%
graph LR
    A[👤 User Query] --> B[🌐 Frontend]
    B --> C[⚙️ Backend API]
    C --> D[💬 Chat Engine]

    D --> E1[🔍 Search<br/>Vespa]
    D --> E2[💾 Context<br/>PostgreSQL]

    E1 --> F[📋 Build<br/>Context]
    E2 --> F

    F --> G[🤖 LLM<br/>Model]
    G --> H[💬 Stream<br/>Response]
    H --> B
    B --> A

    style A fill:#95a5a6,stroke:#7f8c8d,stroke-width:3px,color:#fff
    style B fill:#3498db,stroke:#2980b9,stroke-width:3px,color:#fff
    style C fill:#2ecc71,stroke:#27ae60,stroke-width:3px,color:#fff
    style D fill:#9b59b6,stroke:#8e44ad,stroke-width:3px,color:#fff
    style E1 fill:#1abc9c,stroke:#16a085,stroke-width:3px,color:#fff
    style E2 fill:#1abc9c,stroke:#16a085,stroke-width:3px,color:#fff
    style F fill:#f39c12,stroke:#e67e22,stroke-width:3px,color:#fff
    style G fill:#e67e22,stroke:#d35400,stroke-width:3px,color:#fff
    style H fill:#9b59b6,stroke:#8e44ad,stroke-width:3px,color:#fff
```

---

## Document Indexing Flow

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'fontSize':'16px'}}}%%
graph LR
    A[🌍 External<br/>Source] --> B[🔌 Connector]
    B --> C[📄 Raw<br/>Documents]
    C --> D[⚙️ Indexing<br/>Pipeline]

    D --> E1[✂️ Chunk]
    E1 --> E2[🧠 Embed]
    E2 --> E3[💾 Store]

    E2 --> F[🤖 AI Model]
    F --> E2

    E3 --> G1[🔍 Vespa]
    E3 --> G2[💾 PostgreSQL]
    E3 --> G3[📦 MinIO]

    style A fill:#95a5a6,stroke:#7f8c8d,stroke-width:3px,color:#fff
    style B fill:#f1c40f,stroke:#f39c12,stroke-width:3px,color:#000
    style C fill:#ecf0f1,stroke:#bdc3c7,stroke-width:3px,color:#000
    style D fill:#9b59b6,stroke:#8e44ad,stroke-width:3px,color:#fff
    style E1 fill:#3498db,stroke:#2980b9,stroke-width:3px,color:#fff
    style E2 fill:#3498db,stroke:#2980b9,stroke-width:3px,color:#fff
    style E3 fill:#3498db,stroke:#2980b9,stroke-width:3px,color:#fff
    style F fill:#e67e22,stroke:#d35400,stroke-width:3px,color:#fff
    style G1 fill:#1abc9c,stroke:#16a085,stroke-width:3px,color:#fff
    style G2 fill:#1abc9c,stroke:#16a085,stroke-width:3px,color:#fff
    style G3 fill:#f1c40f,stroke:#f39c12,stroke-width:3px,color:#000
```

---

## Component Legend

### 🎨 Presentation Layer
- **Next.js Frontend**: Web application UI (React 19, TypeScript)

### ⚙️ Application Layer
- **FastAPI Backend**: Main API server (Python, REST)
- **Chat Engine**: LLM orchestration and streaming
- **Indexing Pipeline**: Document processing and embedding
- **Connectors**: 40+ data source integrations

### 🤖 AI/ML Layer
- **Inference Server**: Chat responses and embeddings
- **Indexing Server**: Document vector generation
- **LLM Providers**: OpenAI, Anthropic, Cohere, Google

### 💾 Data Layer
- **PostgreSQL**: Primary relational database
- **Vespa**: Hybrid search engine (BM25 + vectors)
- **Redis**: Cache and async task queue
- **MinIO**: S3-compatible file storage

### ⚡ Processing Layer
- **Celery Workers**: Background job processing
- **Nginx**: Reverse proxy and load balancing

### 🌍 External Layer
- **Data Sources**: Slack, GitHub, Confluence, Gmail, etc.

---

## Technology Stack

| Layer | Technologies |
|-------|-------------|
| **Frontend** | Next.js 16, React 19, TypeScript, Tailwind CSS |
| **Backend** | FastAPI, Python 3.11-3.12, Pydantic 2 |
| **Database** | PostgreSQL 15, SQLAlchemy 2.0, Alembic |
| **Search** | Vespa 8.x (BM25 + Vector) |
| **Cache** | Redis 7.4 |
| **Storage** | MinIO (S3-compatible) |
| **Queue** | Celery + Redis |
| **AI/ML** | Sentence-Transformers, PyTorch, OpenAI, Anthropic |
| **Proxy** | Nginx 1.25.5 |

---

## Key Features

✅ **Hybrid Search**: Combines BM25 lexical search with vector embeddings
✅ **Streaming Responses**: Server-Sent Events (SSE) for real-time chat
✅ **Multi-LLM Support**: OpenAI, Anthropic, Cohere, Google, Bedrock, vLLM
✅ **40+ Connectors**: Slack, GitHub, Confluence, Gmail, Notion, and more
✅ **Background Processing**: Async indexing and connector syncing
✅ **Multi-tenant Ready**: Database design supports tenancy
✅ **Scalable**: Containerized with Docker for horizontal scaling
