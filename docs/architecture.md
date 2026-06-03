# ZORO AI Platform — Architecture Overview

## 1. Architecture Goal

The goal of ZORO’s architecture is to simulate a real-world AI SaaS platform.

The system should be:

* Modular
* Scalable
* Secure
* Easy to understand
* Easy to deploy
* Good for interviews
* Good for future expansion

---

# 2. High-Level System Architecture

```text
User
 ↓
Frontend: Next.js + TypeScript + Tailwind CSS
 ↓
Backend API: FastAPI + Python
 ↓
Core Services
 ├── Authentication Service
 ├── Document Service
 ├── RAG Service
 ├── Agent Service
 ├── Task Service
 ├── Analytics Service
 └── Usage Tracking Service
 ↓
Data Layer
 ├── PostgreSQL Database
 ├── Vector Database
 └── File Storage
 ↓
AI Layer
 ├── Embedding Model
 ├── LLM API
 ├── LangChain RAG Pipeline
 └── LangGraph Agent Workflows
```

---

# 3. Frontend Architecture

## Technology

* Next.js
* TypeScript
* Tailwind CSS
* React components
* API client layer

## Main Pages

```text
frontend/
 └── app/
     ├── page.tsx
     ├── login/
     ├── register/
     ├── dashboard/
     ├── documents/
     ├── chat/
     ├── tasks/
     ├── analytics/
     └── settings/
```

## Frontend Responsibilities

The frontend is responsible for:

* Displaying the user interface.
* Handling user login and registration forms.
* Uploading documents.
* Showing uploaded documents.
* Providing RAG chat interface.
* Showing AI-generated summaries.
* Showing generated tasks.
* Displaying analytics dashboards.
* Calling backend APIs.

---

# 4. Backend Architecture

## Technology

* FastAPI
* Python
* Pydantic
* SQLAlchemy or SQLModel
* PostgreSQL
* LangChain
* LangGraph
* OpenAI API
* Vector database

## Backend Folder Structure

```text
backend/
 └── app/
     ├── main.py
     ├── api/
     │   ├── auth.py
     │   ├── documents.py
     │   ├── chat.py
     │   ├── tasks.py
     │   ├── agents.py
     │   └── analytics.py
     │
     ├── core/
     │   ├── config.py
     │   ├── security.py
     │   └── database.py
     │
     ├── models/
     │   ├── user.py
     │   ├── document.py
     │   ├── chat.py
     │   ├── task.py
     │   └── usage.py
     │
     ├── services/
     │   ├── auth_service.py
     │   ├── document_service.py
     │   ├── task_service.py
     │   └── analytics_service.py
     │
     ├── rag/
     │   ├── loader.py
     │   ├── splitter.py
     │   ├── embeddings.py
     │   ├── retriever.py
     │   └── generator.py
     │
     └── agents/
         ├── base_agent.py
         ├── document_agent.py
         ├── task_agent.py
         └── report_agent.py
```

## Backend Responsibilities

The backend is responsible for:

* User authentication.
* File upload handling.
* Document text extraction.
* Text chunking.
* Embedding generation.
* Vector search.
* LLM response generation.
* AI agent workflow execution.
* Task management.
* Analytics tracking.
* Database operations.

---

# 5. Database Architecture

## Primary Database

ZORO will use PostgreSQL for structured application data.

## Main Tables

```text
users
 ├── id
 ├── name
 ├── email
 ├── hashed_password
 ├── created_at
 └── updated_at

documents
 ├── id
 ├── user_id
 ├── file_name
 ├── file_type
 ├── file_path
 ├── status
 ├── created_at
 └── updated_at

document_chunks
 ├── id
 ├── document_id
 ├── chunk_text
 ├── chunk_index
 ├── embedding_id
 └── created_at

chat_sessions
 ├── id
 ├── user_id
 ├── title
 ├── created_at
 └── updated_at

chat_messages
 ├── id
 ├── session_id
 ├── role
 ├── content
 ├── sources
 ├── created_at
 └── updated_at

tasks
 ├── id
 ├── user_id
 ├── title
 ├── description
 ├── status
 ├── priority
 ├── source_type
 ├── created_at
 └── updated_at

usage_logs
 ├── id
 ├── user_id
 ├── feature_name
 ├── input_tokens
 ├── output_tokens
 ├── estimated_cost
 ├── latency_ms
 ├── created_at
 └── updated_at
```

---

# 6. Vector Database Architecture

## Purpose

The vector database stores embeddings for document chunks so the system can retrieve relevant content during RAG chat.

## Options

For MVP:

* ChromaDB
* pgvector

For future production:

* Qdrant
* Pinecone
* Weaviate

## RAG Storage Flow

```text
Uploaded Document
 ↓
Extract Text
 ↓
Split Text into Chunks
 ↓
Generate Embeddings
 ↓
Store Embeddings in Vector DB
 ↓
Retrieve Relevant Chunks During Chat
```

---

# 7. RAG Pipeline Architecture

## RAG Flow

```text
User Question
 ↓
Convert Question to Embedding
 ↓
Search Vector Database
 ↓
Retrieve Relevant Document Chunks
 ↓
Build Prompt with Retrieved Context
 ↓
Send Prompt to LLM
 ↓
Generate Answer
 ↓
Return Answer with Sources
```

## RAG Responsibilities

The RAG system should:

* Retrieve relevant document chunks.
* Avoid unsupported answers.
* Show sources when possible.
* Keep answers grounded in uploaded content.
* Save questions and answers in chat history.

---

# 8. AI Agent Architecture

## Purpose

AI agents are used for multi-step workflows where the system needs to plan, retrieve, analyze, and generate structured output.

## Example Agent Flow

```text
User Request:
"Analyze this resume and create improvement tasks."

Agent Steps:
1. Understand the user request.
2. Retrieve relevant resume content.
3. Analyze strengths and weaknesses.
4. Generate improvement suggestions.
5. Convert suggestions into tasks.
6. Save tasks to the database.
7. Return final summary to user.
```

## Initial Agents

### Document Analysis Agent

Analyzes uploaded documents and gives structured insights.

### Task Generation Agent

Turns AI output into actionable tasks.

### Report Generation Agent

Creates structured reports from documents and user requests.

---

# 9. API Architecture

## Main API Groups

```text
/api/auth
/api/documents
/api/chat
/api/tasks
/api/agents
/api/analytics
/api/usage
```

## Example Endpoints

```text
POST   /api/auth/register
POST   /api/auth/login
GET    /api/auth/me

POST   /api/documents/upload
GET    /api/documents
GET    /api/documents/{document_id}
DELETE /api/documents/{document_id}

POST   /api/chat
GET    /api/chat/sessions
GET    /api/chat/sessions/{session_id}

POST   /api/tasks
GET    /api/tasks
PATCH  /api/tasks/{task_id}
DELETE /api/tasks/{task_id}

POST   /api/agents/document-analysis
POST   /api/agents/task-generation
POST   /api/agents/report-generation

GET    /api/analytics/summary
GET    /api/usage
```

---

# 10. Authentication Architecture

## MVP Authentication

The MVP will use:

* Email and password login.
* Password hashing.
* JWT access tokens.
* Protected backend routes.
* Protected frontend dashboard routes.

## Future Authentication

Later versions may include:

* Google OAuth
* GitHub OAuth
* Role-based access control
* Team workspaces
* Admin users

---

# 11. Deployment Architecture

## Local Development

```text
Frontend: http://localhost:3000
Backend:  http://localhost:8000
Database: PostgreSQL local or Docker
Vector DB: ChromaDB or pgvector
```

## Future Production Deployment

Possible deployment plan:

```text
Frontend → Vercel
Backend  → Render / Railway / AWS
Database → Supabase / Neon / AWS RDS
Storage  → AWS S3 / Cloudinary
CI/CD    → GitHub Actions
```

---

# 12. Security Considerations

ZORO should include:

* Password hashing.
* JWT authentication.
* Protected API routes.
* Environment variables for secrets.
* File type validation.
* File size limits.
* User-specific document access.
* Input validation.
* Error handling without leaking secrets.

---

# 13. Monitoring and Logging

The system should eventually track:

* API errors.
* Request latency.
* AI response time.
* Token usage.
* Estimated cost.
* Document processing failures.
* User activity.

---

# 14. Final Architecture Vision

ZORO should not be just a chatbot. It should be a production-style AI platform with frontend, backend, database, RAG, agents, analytics, authentication, deployment, and monitoring.

The architecture should show that the developer understands how real companies build, ship, and maintain AI-powered software products.
