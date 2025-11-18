# Install-and-Use AI Coding Orchestrators

## The Key Question

**You want:** Install a tool (like Claude Flow), give it a spec, and it orchestrates coding tasks (code + test + deploy + others) in parallel where sensible.

**You DON'T want:** Libraries that require building your own orchestration (CrewAI, LangGraph) or general workflow engines (Temporal, Airflow).

---

## Install-and-Use Coding Orchestrators (2024-2025)

### 1. **Claude Flow + Flow-Nexus** (The Full Stack)

**What it is:** Hooks framework for Claude Code + cloud execution platform

#### **Installation:**
```bash
# Claude Flow (local orchestration)
cd your-project
npx claude-flow@alpha init --force

# Flow-Nexus (cloud execution with E2B)
npx flow-nexus@latest
npx flow-nexus auth register -e your@email.com -p password
claude mcp add flow-nexus npx flow-nexus@latest mcp start
```

#### **What Flow-Nexus Adds:**

From the npm package and GitHub repo:

**96 MCP Tools Including:**
- **E2B Sandboxes** - Cloud code execution environments
- **AI Swarms** - Multi-agent coordination in cloud
- **Neural Processing** - Training/inference capabilities
- **Workflow Automation** - Task orchestration
- **Challenge System** - Gamified coding tasks
- **Marketplace** - Share/earn from agent workflows
- **rUv Credits** - Economic system for agent work

**Architecture:**
```
Claude Flow (local)
    ↓ via MCP
Flow-Nexus (cloud platform)
    ↓ spawns
E2B Sandboxes (isolated execution)
    ↓ runs
Agent swarms (parallel tasks)
```

**Key Features Claude Flow Alone Lacks:**
- ✅ **Cloud execution** - E2B sandboxes for isolated runs
- ✅ **Distributed agents** - swarms across multiple sandboxes
- ✅ **Challenge system** - structured coding tasks
- ✅ **Marketplace** - share/monetize agent workflows
- ✅ **Real isolation** - sandboxed execution (security)

**Reality Check:**
- ⚠️ **Same author (ruvnet)** - might have similar marketing inflation
- ⚠️ **Requires E2B account** - cloud service dependency
- ⚠️ **rUv credits** - economic system (gamification or actual cost?)
- ❓ **Actual functionality** - needs independent verification

**Usage Flow:**
```bash
# 1. Start Claude Code with Claude Flow
cd your-project
claude

# 2. Claude Flow sees Flow-Nexus MCP tools available
# 3. User: "Build authentication system with tests and deployment"
# 4. Claude Flow orchestrates:
#    - Researcher agent (local)
#    - Coder agent → E2B sandbox (cloud)
#    - Tester agent → E2B sandbox (cloud)
#    - Deploy agent → E2B sandbox (cloud)
# 5. Flow-Nexus coordinates cloud execution
# 6. Results sync back to local
```

**Pros:**
✅ Zero-code installation
✅ Integrated with Claude Code
✅ Cloud execution via E2B
✅ Parallel agent execution
✅ Full workflow (code + test + deploy)

**Cons:**
❌ Requires Claude Code host
❌ E2B cloud dependency (costs)
❌ Unclear pricing model
❌ Same author as CF (marketing concerns)

---

### 2. **OpenHands (formerly OpenDevin)**

**What it is:** Autonomous AI software engineer with web UI

#### **Installation:**
```bash
# Easiest: Use cloud version
# Go to: openhands.ai (free $10 credits)

# OR: Local installation
uvx --python 3.12 openhands serve
# Opens web UI at http://localhost:3000
```

#### **What It Does:**
```
You: "Build a REST API with JWT authentication and tests"

OpenHands:
1. Creates project plan
2. Spawns terminal agent
3. Spawns editor agent
4. Spawns browser agent (for testing)
5. Executes tasks in Docker sandbox:
   - mkdir project
   - npm init
   - Write server.js
   - Write auth.js
   - Write tests
   - Run tests
   - Fix failures
6. Shows progress in Web UI
7. Commits to git when done
```

#### **Architecture:**
```
┌─────────────────────────────┐
│   OpenHands Web UI          │
│   - Task input              │
│   - Progress visualization  │
│   - Code editor view        │
│   - Terminal view           │
└──────────┬──────────────────┘
           │
┌──────────▼──────────────────┐
│   Execution Engine          │
│   - Planner agent           │
│   - Coder agent             │
│   - Terminal agent          │
│   - Browser agent           │
└──────────┬──────────────────┘
           │
┌──────────▼──────────────────┐
│   Docker Sandbox            │
│   - Isolated filesystem     │
│   - Terminal access         │
│   - Browser access          │
└─────────────────────────────┘
```

#### **Key Features:**
- ✅ **Web UI** - visual progress tracking
- ✅ **Docker sandbox** - isolated execution
- ✅ **Multi-agent** - planner, coder, terminal, browser
- ✅ **Autonomous** - minimal human intervention
- ✅ **Full workflow** - plan → code → test → debug
- ✅ **Git integration** - auto-commits
- ✅ **Browser testing** - can interact with web apps

#### **Best LLM Support:**
- Claude Sonnet 4.5 (best)
- GPT-4o
- DeepSeek R1/V3

**Pros:**
✅ True standalone server (not hooks)
✅ Web UI for monitoring
✅ Handles full SDLC
✅ Docker isolation
✅ Free tier available ($10 credits)
✅ Active development

**Cons:**
❌ More resource-intensive (Docker)
❌ Less integrated with existing IDEs
❌ Experimental (research project)
❌ Can be unpredictable

---

### 3. **Aider**

**What it is:** Terminal-based AI pair programmer

#### **Installation:**
```bash
# Via curl
curl -LsSf https://aider.chat/install.sh | sh

# OR via pip
pip install aider-chat

# Run
aider
```

#### **What It Does:**
```bash
$ aider src/auth.js tests/auth.test.js

Aider> Add JWT authentication with bcrypt password hashing

# Aider:
# 1. Analyzes existing codebase
# 2. Makes changes to src/auth.js
# 3. Makes changes to tests/auth.test.js
# 4. Auto-commits with message: "Add JWT auth with bcrypt"

Aider> Run the tests

# Aider:
# 1. Executes: npm test
# 2. Sees failures
# 3. Asks: "2 tests failed. Should I fix them?"

You> yes

# Aider:
# 1. Analyzes test failures
# 2. Updates code to fix
# 3. Reruns tests
# 4. Commits: "Fix JWT token expiry test"
```

#### **Architecture:**
```
Terminal (aider CLI)
    ↓
LLM (Claude/GPT-4)
    ↓
Code changes (edits files)
    ↓
Git auto-commit
```

**Key Features:**
- ✅ **Universal map** - understands entire codebase
- ✅ **Auto-commits** - sensible commit messages
- ✅ **Multi-file edits** - coordinated changes
- ✅ **Test-driven** - run tests, fix failures
- ✅ **Git aware** - works with branches, PRs

**Orchestration:**
- ⚠️ **Sequential, not parallel** - one task at a time
- ⚠️ **Single agent** - no multi-agent coordination
- ⚠️ **Human-in-loop** - requires interaction

**Pros:**
✅ Extremely easy to use
✅ Works with any codebase
✅ Best-in-class code editing
✅ Fast iteration
✅ Supports all major LLMs

**Cons:**
❌ Single-threaded (no parallel tasks)
❌ No multi-agent orchestration
❌ Requires human guidance
❌ No cloud execution

---

### 4. **GPT-Engineer**

**What it is:** Spec-to-codebase generator

#### **Installation:**
```bash
pip install gpt-engineer
```

#### **What It Does:**
```bash
# Create project directory
mkdir my-app
cd my-app

# Write spec in prompt file
echo "Build a REST API with:
- Express.js
- JWT authentication
- PostgreSQL database
- Jest tests
- Docker deployment" > prompt

# Run
gpt-engineer .

# GPT-Engineer:
# 1. Asks clarifying questions
# 2. Generates entire codebase:
#    - package.json
#    - src/server.js
#    - src/auth.js
#    - src/db.js
#    - tests/*.test.js
#    - Dockerfile
#    - docker-compose.yml
# 3. Creates all files
# 4. You can ask to modify
```

#### **Architecture:**
```
prompt (spec file)
    ↓
GPT-4 (planning)
    ↓
Code generation
    ↓
File creation
    ↓
Optional: Clarifications → Refinement
```

**Key Features:**
- ✅ **Spec-driven** - text file input
- ✅ **Full project** - generates everything
- ✅ **Interactive** - asks questions
- ✅ **Refinement** - iterative improvement

**Orchestration:**
- ⚠️ **Generation, not orchestration** - creates all files at once
- ⚠️ **No parallel agents** - single LLM session
- ⚠️ **No testing loop** - doesn't run tests

**Pros:**
✅ Perfect for greenfield projects
✅ Minimal input required
✅ Generates full stack
✅ Good for prototypes

**Cons:**
❌ No execution/testing
❌ No parallel tasks
❌ Weak on existing codebases
❌ One-shot generation (limited iteration)

---

### 5. **Cursor**

**What it is:** AI-powered IDE (VS Code fork)

#### **Installation:**
```bash
# Download from cursor.sh
# Install like any app
```

#### **What It Does:**
```
You: Cmd+K
"Add JWT authentication with tests"

Cursor:
1. Agent mode activates
2. Creates files:
   - src/auth.js
   - src/middleware/jwt.js
   - tests/auth.test.js
3. Edits existing files:
   - src/app.js (add middleware)
   - package.json (add dependencies)
4. Shows diff for approval
5. You accept/reject each change
6. Runs tests automatically
7. Fixes failures if you ask
```

#### **Key Features:**
- ✅ **IDE integration** - full development environment
- ✅ **Multi-file edits** - coordinated changes
- ✅ **Agent mode** - multi-step execution
- ✅ **Test awareness** - can run and fix tests
- ✅ **Chat + command** - flexible interaction

**Orchestration:**
- ⚠️ **Sequential execution** - one step at a time
- ⚠️ **No multi-agent** - single AI assistant
- ⚠️ **Human approval** - requires review

**Pros:**
✅ Full IDE features
✅ Best coding experience
✅ Fast iteration
✅ Composer mode for multi-file
✅ Test integration

**Cons:**
❌ Not orchestration (assistant, not autonomous)
❌ No parallel tasks
❌ Requires supervision
❌ Paid subscription

---

### 6. **Sweep**

**What it is:** GitHub issue → PR automation

#### **Installation:**
```bash
# Install GitHub App
# Go to: sweep.dev
# Connect to your repo
```

#### **What It Does:**
```
1. Create GitHub issue:
   "Add JWT authentication with tests"

2. Add label: "sweep"

3. Sweep (bot):
   - Reads issue
   - Analyzes codebase
   - Creates plan
   - Generates code
   - Writes tests
   - Creates PR
   - Responds to PR reviews
   - Fixes issues
```

#### **Architecture:**
```
GitHub Issue
    ↓
Sweep Bot (cloud)
    ↓
Code analysis
    ↓
Code generation
    ↓
PR creation
    ↓
Review response
```

**Key Features:**
- ✅ **GitHub-native** - works in your workflow
- ✅ **Autonomous PR creation** - no local setup
- ✅ **PR refinement** - responds to reviews
- ✅ **Full workflow** - code + tests

**Orchestration:**
- ⚠️ **Cloud-only** - no local control
- ⚠️ **Sequential** - not parallel
- ⚠️ **GitHub-dependent** - must use GitHub

**Pros:**
✅ Zero local installation
✅ Team collaboration
✅ PR-based workflow
✅ Iterative refinement

**Cons:**
❌ GitHub-only
❌ Cloud service (costs)
❌ No local execution
❌ Limited control

---

## Comparison: Install-and-Use Coding Orchestrators

| Feature | Claude Flow + Nexus | OpenHands | Aider | GPT-Engineer | Cursor | Sweep |
|---------|---------------------|-----------|-------|--------------|--------|-------|
| **Installation** | `npx` commands | `uvx` or cloud | `pip install` | `pip install` | Download app | GitHub App |
| **Interface** | Claude Code CLI | Web UI | Terminal | Terminal | IDE | GitHub |
| **Execution** | Local + E2B cloud | Docker sandbox | Local | Local | Local | Cloud |
| **Orchestration** | Multi-agent swarm | Multi-agent autonomous | Single agent | Single LLM | Single assistant | Single bot |
| **Parallel Tasks** | ✅ Yes | ✅ Yes | ❌ No | ❌ No | ❌ No | ❌ No |
| **Full Workflow** | ✅ Code+Test+Deploy | ✅ Code+Test+Debug | ✅ Code+Test | ⚠️ Code only | ✅ Code+Test | ✅ Code+Test |
| **Spec-Driven** | Via CLAUDE.md | Via chat | Via chat | Via prompt file | Via command | Via issue |
| **Cloud Execution** | ✅ E2B (Nexus) | ⚠️ Cloud option | ❌ No | ❌ No | ❌ No | ✅ Yes |
| **Autonomy** | ⚠️ Semi (via LLM) | ✅ High | ⚠️ Low | ⚠️ Medium | ⚠️ Low | ✅ High |
| **Monitoring UI** | ❌ No (CF alone) | ✅ Web UI | ❌ Terminal only | ❌ Terminal only | ✅ IDE | ✅ GitHub |
| **Cost** | Free + E2B costs | Free tier + costs | Free + LLM API | Free + LLM API | Paid subscription | Paid service |
| **Best For** | Claude Code users | Autonomous projects | Interactive coding | New projects | Daily development | GitHub teams |

---

## Which One for Your Use Case?

### **You want:**
1. ✅ Install and use (not build)
2. ✅ Give it a spec (maybe BMad)
3. ✅ Orchestrates full workflow (code + test + deploy + others)
4. ✅ Parallel execution where sensible

### **Best Options:**

#### **Option 1: Claude Flow + Flow-Nexus** ⭐⭐⭐⭐

**Why:**
- ✅ Install with `npx` (zero code)
- ✅ Spec via CLAUDE.md or chat
- ✅ Multi-agent parallel execution
- ✅ Full workflow (code + test + deploy via E2B)
- ✅ Cloud execution (Flow-Nexus)
- ✅ Works with Claude Code (best coding LLM)

**Caveat:**
- Requires Claude Code as host
- E2B costs for cloud execution
- Same author (ruvnet) - marketing concerns
- Flow-Nexus maturity unclear

**Example Flow:**
```bash
# Install
npx claude-flow@alpha init --force
npx flow-nexus@latest
claude mcp add flow-nexus npx flow-nexus@latest mcp start

# Start Claude Code
claude

# Give spec
You: "Build a REST API with:
- Express.js + TypeScript
- JWT authentication with refresh tokens
- PostgreSQL with Prisma ORM
- Jest unit tests
- Supertest integration tests
- Docker deployment
- GitHub Actions CI/CD

Use Flow-Nexus E2B sandboxes for parallel execution."

# Claude Flow orchestrates:
# - Researcher: analyze requirements (local)
# - Backend dev: Express + auth (E2B sandbox 1)
# - Database dev: Prisma schema (E2B sandbox 2)
# - Test engineer: Jest + Supertest (E2B sandbox 3)
# - DevOps: Docker + GH Actions (E2B sandbox 4)
# All run in parallel, coordinated via Flow-Nexus
```

---

#### **Option 2: OpenHands** ⭐⭐⭐⭐

**Why:**
- ✅ True standalone (not hooks)
- ✅ Web UI for monitoring
- ✅ Autonomous multi-agent
- ✅ Full workflow (plan → code → test → debug)
- ✅ Docker isolation
- ✅ Free tier ($10 credits)

**Caveat:**
- More experimental
- Can be unpredictable
- Higher resource usage (Docker)

**Example Flow:**
```bash
# Install
uvx --python 3.12 openhands serve

# Go to http://localhost:3000

# Input spec:
"Build a REST API with Express, JWT auth, PostgreSQL,
tests, and Docker deployment"

# OpenHands:
# 1. Creates execution plan (shows in UI)
# 2. Spawns agents in Docker sandbox:
#    - Planner agent
#    - Coder agent (backend)
#    - Coder agent (database)
#    - Terminal agent (tests)
#    - Browser agent (manual testing)
# 3. Executes in parallel where possible
# 4. Shows live progress in UI
# 5. Commits to git
# 6. Asks for feedback
```

---

#### **Option 3: Aider (if you're okay with sequential)** ⭐⭐⭐

**Why:**
- ✅ Easiest installation
- ✅ Best code editing quality
- ✅ Handles full workflow
- ✅ Works with any codebase

**Caveat:**
- ❌ No parallel execution
- ❌ Single agent (not swarm)
- ❌ Requires human interaction

**Example Flow:**
```bash
pip install aider-chat

# Start with spec file
cat > SPEC.md << EOF
Build REST API:
- Express.js + TypeScript
- JWT authentication
- PostgreSQL with Prisma
- Jest tests
EOF

aider SPEC.md

Aider> Read SPEC.md and implement the API
# (works through tasks sequentially)

Aider> Now write tests
# (writes tests)

Aider> Run tests and fix any failures
# (iterates until passing)
```

---

## The Verdict

### **For Your Use Case (spec → orchestrated parallel execution):**

**#1: Claude Flow + Flow-Nexus**
- ✅ Best fit for spec-driven parallel orchestration
- ✅ E2B cloud execution enables true parallelism
- ✅ Multi-agent swarm coordination
- ⚠️ Requires verification that Flow-Nexus actually works as advertised

**#2: OpenHands**
- ✅ True standalone orchestrator
- ✅ Multi-agent autonomous execution
- ✅ Web UI for monitoring
- ⚠️ More experimental, less stable

**#3: Aider**
- ✅ Most reliable for quality
- ✅ Easiest to use
- ❌ No parallel execution

---

## About Flow-Nexus Reality Check

**What we know:**
- ✅ Real npm package (v0.1.128+)
- ✅ MCP server implementation
- ✅ E2B integration (E2B is real, widely used)
- ✅ 96+ MCP tools claimed
- ⚠️ Same author as Claude Flow (ruvnet)
- ⚠️ Marketing language similar to CF
- ❓ Independent verification needed

**What to verify:**
1. Does E2B sandbox spawning actually work?
2. Do agents actually run in parallel in cloud?
3. Is rUv credits system real or just gamification?
4. What are actual E2B costs?
5. Does marketplace actually exist?

**How to verify:**
```bash
# Install and test
npx flow-nexus@latest
npx flow-nexus auth register -e test@test.com -p password
npx flow-nexus challenge list

# Try spawning E2B sandbox
# Check if agents actually run in cloud
# Monitor actual resource usage
```

---

## BMad Method Integration

If you're using **BMad (Business Modeling as Design)** for specs:

**All these tools can accept BMad specs:**

### **Format 1: Markdown Spec File**
```markdown
# BMad Specification: Authentication Service

## Business Model Canvas
- Value Proposition: Secure user authentication
- Customer Segments: Web applications
- Key Activities: JWT generation, token validation
...

## User Stories
- As a user, I want to login with email/password
- As a user, I want password reset via email
...

## Technical Requirements
- Stack: Node.js, Express, PostgreSQL
- Auth: JWT with refresh tokens
- Tests: Jest unit + Supertest integration
...
```

**Use with:**
- **Claude Flow:** Add to `CLAUDE.md` or chat
- **OpenHands:** Paste into Web UI
- **Aider:** `aider BMAD_SPEC.md`
- **GPT-Engineer:** Save as `prompt` file

### **Format 2: Structured JSON**
```json
{
  "business_model": {
    "value_proposition": "Secure auth",
    "customer_segments": ["web_apps"]
  },
  "user_stories": [
    "As a user, I want to login..."
  ],
  "technical_requirements": {
    "stack": ["node", "express", "postgresql"],
    "auth_method": "jwt"
  },
  "tasks": [
    {"name": "Setup project", "parallel": false},
    {"name": "Implement auth", "parallel": true, "subtasks": [
      "User model",
      "JWT service",
      "Auth controller",
      "Tests"
    ]},
    {"name": "Deploy", "parallel": false}
  ]
}
```

**Best for:** OpenHands (can parse JSON), Claude Flow (via CLAUDE.md)

---

## Summary

**You want install-and-use coding orchestrators that:**
1. ✅ Install with simple command
2. ✅ Accept spec (BMad or text)
3. ✅ Orchestrate full workflow
4. ✅ Run tasks in parallel

**Top recommendation:** **Claude Flow + Flow-Nexus**
- Meets all criteria
- E2B enables real parallel execution
- Multi-agent swarm coordination
- Caveat: Verify Flow-Nexus actually works

**Alternative:** **OpenHands** (more standalone, experimental)
**Fallback:** **Aider** (sequential but highest quality)

---

**Next Step:** Test Flow-Nexus to verify it actually provides the parallel cloud execution it claims.
