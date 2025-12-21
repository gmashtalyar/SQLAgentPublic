# SQL Agent CLI 🤖💬

[![Python](https://img.shields.io/badge/python-blue.svg)](https://www.python.org/downloads/) [![LangGraph](https://img.shields.io/badge/LangGraph-orange.svg)](https://github.com/langchain-ai/langgraph) [![OpenAI](https://img.shields.io/badge/OpenAI-412991.svg?logo=openai&logoColor=white)](https://openai.com) [![Anthropic](https://img.shields.io/badge/Anthropic-191919.svg?logo=anthropic&logoColor=white)](https://anthropic.com) [![Ollama](https://img.shields.io/badge/Ollama-000000.svg?logo=ollama&logoColor=white)](https://ollama.ai)

> **Transform natural language into SQL queries** with AI-powered database exploration. A production-ready CLI tool that brings conversational intelligence to your database workflows.

**[Business Value](#-business-value)** • **[Use Cases](#-use-cases)** • **[Advanced Features](#-advanced-features)** • **[Installation & Usage](#-installation--usage)** • **[Technical Architecture](#-technical-architecture)** • **[Security & Compliance](#-security--compliance)** • **[Technical Highlights](#-technical-highlights)**

## 🎯 What is SQL Agent CLI?

SQL Agent CLI is an intelligent command-line interface that bridges the gap between human language and databases. Ask questions in natural language, and watch as AI translates your intent into optimized SQL queries—executed safely with enterprise-grade security controls.

### The Problem It Solves

- **Database Complexity**: Modern databases are powerful but intimidating. SQL syntax is a barrier for many professionals.
- **Context Switching**: Developers waste time switching between documentation, SQL references, and their terminal.
- **Safety Concerns**: Direct database access is risky—one wrong query can corrupt data or crash production systems.
- **Schema Discovery**: Understanding unfamiliar database structures takes hours of exploration.

### The Solution

SQL Agent CLI provides:
- 🗣️ **Natural Language Queries**: "Show me all users who signed up last week" → Production SQL
- 🛡️ **Built-in Safety**: Read-only mode by default, query validation, and dangerous operation blocking
- 🔍 **Intelligent Insights**: AI-powered schema analysis and optimization suggestions
- 🔐 **Enterprise Security**: Encrypted credential storage, timeout controls, and audit logging
- 🚀 **Zero Configuration**: Works with PostgreSQL, MySQL, SQLite out of the box

---

## 💼 Business Value

### For Data Analysts
- Query databases without memorizing SQL syntax
- Explore unfamiliar schemas with AI-guided insights
- Export results (CSV/JSON) for further analysis

### For Developers
- Rapid prototyping of database queries
- Safe production database exploration
- Cross-database compatibility (PostgreSQL, MySQL, SQLite)

### For Engineering Managers
- Reduce onboarding time for new developers
- Minimize database-related incidents with safety controls
- Enable data-driven decision making across teams

---

## 🎯 Use Cases

### 1. Database Migration Planning
```
> Show me tables with more than 10 million rows
> Analyze foreign key dependencies for the orders table
> What indexes are missing on frequently joined columns?
```

### 2. Performance Troubleshooting
```
> Show me queries that would benefit from indexes
> Find tables with no primary key
> Analyze query execution plan for: SELECT ...
```

### 3. Data Quality Audits
```
> Find users with invalid email formats
> Show me products with negative prices
> Identify orphaned records in the orders table
```

### 4. Business Intelligence
```
> What's the average order value by month?
> Show me customer retention by cohort
> Find our top 10 customers by lifetime value
```

---

## 🎓 Advanced Features

### 1. Natural Language Understanding

The agent understands context and intent:

```
> What tables are in this database?
[Shows schema overview]

> Tell me about the users table
[AI analyzes schema and provides insights]

> Show me users with no orders
[Generates: SELECT u.* FROM users u LEFT JOIN orders o ON u.id = o.user_id WHERE o.id IS NULL]
```

### 2. AI-Powered Insights

```bash
# Interactive command
> insights users

[AI Response]
📊 Table: users (1,247 rows)
- Columns: id, email, created_at, last_login, subscription_tier
- Observations:
  • 23% of users never logged in (potential onboarding issue)
  • Premium tier has 2.3x retention vs. free tier
- Suggested queries:
  • Cohort analysis by signup month
  • Churn prediction by login frequency
```

### 3. Query Optimization

```bash
> SELECT * FROM orders WHERE DATE(created_at) = '2024-01-01'

⚠️  Performance Warning:
- Using function on indexed column prevents index usage
- Recommendation: Use created_at >= '2024-01-01' AND created_at < '2024-01-02'
```

### 4. Safe Write Operations

```bash
# Enable write mode (with confirmation)
sql-agent connect --url "..." --write

> DELETE FROM users WHERE inactive = true

🛑 Dangerous Operation Detected!
Query: DELETE FROM users WHERE inactive = true
Affected: ~342 rows

Type 'DELETE 342 ROWS' to confirm: _
```

---

## 🚀 Installation & Usage

SQL Agent CLI is distributed as a standalone executable—no Python installation required. Install via your platform's package manager:

**macOS**:
```bash
# Install via DMG
# Download sql-agent.dmg from releases
# Drag to Applications, then run from terminal:
sql-agent connect --url "postgresql://user:pass@host/db"

# Or via Homebrew
brew install sql-agent-cli
```

**Windows**:
```powershell
# Install via Scoop
scoop install sql-agent-cli

# Or download sql-agent-setup.exe from releases and run the installer
```

**Linux**:
```bash
# Install via .deb package
sudo dpkg -i sql-agent.deb

# Or download the binary and install manually
sudo cp sql-agent /usr/local/bin/
sql-agent --help
```

### Quick Start

```bash
# Connect to a database
sql-agent connect --url "postgresql://user:pass@host/db"

# Use saved profile
sql-agent use-profile production

# Interactive session starts automatically
> Show me all users created in the last 7 days

[AI generates and executes SQL]
SELECT * FROM users WHERE created_at >= NOW() - INTERVAL '7 days';

# View query history
sql-agent history

# Manage profiles
sql-agent profiles
sql-agent export-profiles --output backup.json
```

### Configuration

```bash
# Switch LLM provider
sql-agent configuration llm.provider --value anthropic
sql-agent configuration llm.model --value claude-3-sonnet

# Adjust security settings
sql-agent configuration security.allow_write_operations --value false
```

---

## 🏗️ Technical Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     CLI Interface (Typer)                   │
│  Commands: connect, profiles, config, history, use-profile  │
└────────────────────┬────────────────────────────────────────┘
                     │
         ┌───────────┴───────────┐
         │                       │
┌────────▼────────┐    ┌────────▼──────────┐
│  Config Manager │    │ Database Manager  │
│  - Profiles     │    │  - SQLAlchemy     │
│  - Encryption   │    │  - Schema Cache   │
│  - History      │    │  - Connections    │
└─────────────────┘    └───────────────────┘
                              │
                    ┌─────────┴─────────┐
                    │                   │
         ┌──────────▼─────────┐  ┌─────▼──────────┐
         │   SQL Agent        │  │ Query Executor │
         │  (LangGraph)       │  │  - Validation  │
         │  ┌──────────────┐  │  │  - Timeout     │
         │  │ Analyze      │  │  │  - Limits      │
         │  │ Input        │  │  └────────────────┘
         │  └──────┬───────┘  │
         │         │          │
         │  ┌──────▼───────┐  │
         │  │ Generate SQL │  │
         │  │ (LLM)        │  │
         │  └──────┬───────┘  │
         │         │          │
         │  ┌──────▼───────┐  │
         │  │ Validate     │  │
         │  │ Query        │  │
         │  └──────┬───────┘  │
         │         │          │
         │  ┌──────▼───────┐  │
         │  │ Format       │  │
         │  │ Response     │  │
         │  └──────────────┘  │
         └────────────────────┘
                    │
         ┌──────────▼──────────┐
         │   LLM Providers     │
         │  - OpenAI (GPT-4)   │
         │  - Anthropic Claude │
         │  - Groq             │
         │  - Ollama (Local)   │
         └─────────────────────┘
```

### Core Components

#### 1. **LangGraph State Machine** (`agent.py`)
The heart of the system—a graph-based workflow engine that orchestrates query processing:

```python
# Workflow Nodes
analyze_input → generate_sql → validate_query → format_response
                     ↓
              (conditional routing)
```

**State Management**:
- `AgentState` TypedDict tracks conversation context, schema, and query results
- Conditional edges route between natural language processing and direct SQL
- Persistent conversation history enables multi-turn interactions

**Key Innovation**: Uses LangGraph's stateful workflow to maintain context across queries, enabling follow-up questions like "show me the top 10" after an initial query.

#### 2. **LLM Factory Pattern** (`agent.py`)
Abstraction layer supporting multiple AI providers with hot-swappable backends:

```python
class LLMFactory:
    @staticmethod
    def create_llm(config: Dict) -> BaseChatModel:
        # Dynamic provider selection
        # Falls back gracefully on failure
```

**Supported Providers**:
- **OpenAI**: GPT-4, GPT-3.5-turbo (default)
- **Anthropic**: Claude 3 Opus/Sonnet/Haiku
- **Groq**: Llama 2, Mixtral (high-speed inference)
- **Ollama**: Local models (privacy-focused, no API costs)

**Design Pattern**: Strategy pattern implementation allowing runtime LLM switching without code changes.

#### 3. **Database Manager** (`database.py`)
SQLAlchemy-powered connection management with automatic schema introspection:

**Features**:
- Connection pooling for performance
- Multi-dialect support (PostgreSQL, MySQL, SQLite)
- Automatic schema discovery and caching
- Foreign key relationship tracking
- Read-only mode enforcement at connection level

**Schema Introspection**:
```python
# Automatically discovers:
- Tables and views
- Column types and constraints
- Primary/foreign keys
- Indexes and relationships
```

#### 4. **Query Executor** (`query_executor.py`)
Query execution with multiple safety layers:

**Security Features**:
- ✅ Dangerous keyword blocking (DROP, TRUNCATE, DELETE, ALTER)
- ✅ SIGALRM-based timeout (prevents resource exhaustion)
- ✅ Row limit enforcement (10,000 row hard cap)
- ✅ Query complexity scoring (warns on expensive operations)
- ✅ Execution plan analysis (EXPLAIN integration)
- ✅ User confirmation prompts

**Complexity Analysis**:
```
Simple (0):         SELECT * FROM users
Moderate (1-3):     SELECT * FROM users JOIN orders
Complex (4-6):      Multi-join with subqueries
Very Complex (7+):  CTEs, nested subqueries, unions
```

#### 5. **Configuration Manager** (`config.py`)
Encrypted credential storage with profile-based connections:

**Encryption**:
- Fernet symmetric encryption (AES-128)
- Per-installation unique keys (`~/.sql-agent/.key`)
- Automatic credential detection (encrypts URLs with `@`)

**Profile System**:
```json
{
  "production": {
    "url": "encrypted_connection_string",
    "write_enabled": false
  }
}
```

---

## 🔧 Technical Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **CLI Framework** | Typer | Type-safe command-line interface |
| **AI Orchestration** | LangGraph | State machine workflow for agent logic |
| **LLM Integration** | LangChain | Unified interface for multiple AI providers |
| **Database** | SQLAlchemy 2.0 | ORM and connection management |
| **Security** | Cryptography (Fernet) | Credential encryption |
| **UI/UX** | Rich | Terminal formatting and tables |
| **SQL Parsing** | sqlparse | Query structure analysis |
| **Distribution** | PyInstaller | Standalone executable builds |

---

## 🔐 Security & Compliance

### Encryption
- **AES-128 Fernet encryption** for database credentials
- **0600 permissions** on key file (`~/.sql-agent/.key`)
- **Automatic key rotation** support

### Access Control
- **Read-only mode by default** (prevents accidental writes)
- **Query validation** blocks DROP, TRUNCATE, ALTER operations
- **Timeout enforcement** (30s default, prevents DoS)

### Audit Trail
- **Query history logging** with timestamps
- **Execution time tracking** for performance monitoring
- **Error logging** for security analysis

### Compliance Ready
- No sensitive data logged (results not stored)
- Configurable retention policies
- Export functionality for compliance reporting

---

## 🧠 Technical Highlights

### 1. LangGraph Workflow Engine
- **Stateful agent architecture** with persistent context
- **Conditional routing** based on query type
- **Fault-tolerant design** with graceful error handling

### 2. Multi-Provider LLM Support
- **Hot-swappable backends** (OpenAI, Anthropic, Groq, Ollama)
- **Automatic fallback** on provider failures
- **Cost optimization** (local Ollama for development)

### 3. Database Abstraction
- **SQLAlchemy 2.0 core** (not ORM, for flexibility)
- **Connection pooling** for performance
- **Dialect-aware** query generation

### 4. Security-First Design
- **Defense in depth**: Encryption + validation + timeouts + read-only mode
- **Principle of least privilege**: Minimal permissions by default
- **Audit logging**: Complete query history

---
