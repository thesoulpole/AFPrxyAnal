# Claude-Flow vs Agentic-Flow: Complete Comparison

## Executive Summary

**KEY FINDING:** Claude-Flow and Agentic-Flow are **both by the same author (ruvnet)** but serve **different purposes** in the AI agent ecosystem. Contrary to initial assumptions, **Claude-Flow depends ON Agentic-Flow**, not the other way around.

**Relationship:**
```
┌────────────────────────────────────────┐
│         Claude-Flow v2.7.34            │
│   (Orchestration & MCP Platform)       │
│                                        │
│   Depends on:                          │
│   ├── agentic-flow ^1.9.4 ✅           │
│   ├── agentdb ^1.6.1                   │
│   ├── ruv-swarm ^1.0.14                │
│   └── flow-nexus ^0.1.128              │
└────────────────────────────────────────┘

┌────────────────────────────────────────┐
│      Agentic-Flow v1.10.2              │
│   (Multi-Model Router & Deployment)    │
│                                        │
│   Claims to integrate:                 │
│   └── claude-flow (101 tools) ⚠️       │
│       (But it's claude-flow that       │
│        depends on agentic-flow!)       │
└────────────────────────────────────────┘
```

**Rating:**
- **Claude-Flow:** 8/10 - Comprehensive orchestration platform
- **Agentic-Flow:** 6.5/10 - Good multi-model routing, oversold features

---

## 1. What Each Package Actually IS

### Claude-Flow v2.7.34

**Description:**
> "Enterprise-grade AI agent orchestration with WASM-powered ReasoningBank memory, distributed swarm intelligence, and 101 MCP tools for Claude Code"

**Core Purpose:** **Agent orchestration and swarm coordination platform**

**Key Components:**
1. **25 Claude Skills** - Natural language commands
2. **64 Specialized Agents** - Pre-configured agent types
3. **100+ MCP Tools** - Automation toolkit
4. **Hive-Mind Intelligence** - Queen-led multi-agent coordination
5. **Hybrid Memory System** - AgentDB + ReasoningBank
6. **Advanced Hooks** - Pre/post-operation automation

**Architecture:**
```typescript
// Claude-Flow provides the CLI and orchestration layer
npx claude-flow@alpha swarm "build REST API" --claude

// Internally uses:
// - agentic-flow for model routing
// - agentdb for memory storage
// - ruv-swarm for agent coordination
// - flow-nexus for flow management
```

**Primary Use Case:** **Claude Desktop extension and CLI orchestration**

---

### Agentic-Flow v1.10.2

**Description:**
> "Production-ready AI agent orchestration platform with 66 specialized agents, 213 MCP tools, ReasoningBank learning memory, and autonomous multi-agent swarms"

**Core Purpose:** **Multi-model router + agent deployment framework**

**Key Components:**
1. **Router/Proxy** - Switch between Anthropic/Gemini/OpenRouter/ONNX
2. **AgentDB** - SQLite memory wrapper (dependency, v1.4.3)
3. **ReasoningBank** - Google DeepMind paper implementation
4. **Agent Booster** - WASM code optimization
5. **Federation Hub** - Ephemeral agent coordination
6. **Swarm Infrastructure** - Multi-agent messaging

**Architecture:**
```typescript
// Agentic-Flow provides the execution engine
npx agentic-flow --agent coder --task "build API" --provider gemini

// Can be used standalone OR as a dependency by Claude-Flow
```

**Primary Use Case:** **Standalone agent execution with model flexibility**

---

## 2. Dependency Relationship (THE TRUTH)

### What Claude-Flow's package.json Shows:

```json
{
  "name": "claude-flow",
  "version": "2.7.34",
  "dependencies": {
    "@anthropic-ai/sdk": "^0.65.0",
    "@modelcontextprotocol/sdk": "^1.0.4",
    "agentic-flow": "^1.9.4",      // ← Claude-Flow DEPENDS ON Agentic-Flow!
    "ruv-swarm": "^1.0.14",
    "flow-nexus": "^0.1.128",
    "commander": "^12.1.0",
    "inquirer": "^12.3.0",
    // ...
  },
  "optionalDependencies": {
    "agentdb": "^1.6.1",
    "better-sqlite3": "^12.2.0"
  }
}
```

### What This Means:

**Claude-Flow** is the **parent platform** that:
- Provides CLI orchestration (`npx claude-flow`)
- Offers 100+ MCP tools
- Implements swarm coordination
- Uses agentic-flow internally for agent execution

**Agentic-Flow** is a **dependency** that provides:
- Multi-model routing
- Agent execution engine
- ReasoningBank implementation
- Federation hub for distributed agents

### The Confusion in Agentic-Flow's README:

```markdown
# Agentic-Flow README claims:
"Built on Claude Agent SDK by Anthropic, powered by Claude Flow
(101 MCP tools), Flow Nexus (96 cloud tools)..."
```

**Reality:** This is **backwards**!
- Claude-Flow USES agentic-flow as a dependency
- Not the other way around
- Both are by same author (ruvnet), so marketing is confusing

---

## 3. Feature Comparison Matrix

| Feature | Claude-Flow v2.7.34 | Agentic-Flow v1.10.2 |
|---------|---------------------|----------------------|
| **Primary Purpose** | Orchestration & MCP platform | Multi-model routing & deployment |
| **Author** | ruvnet | ruvnet |
| **Depends On** | agentic-flow v1.9.4 | claude-flow (claimed, but false) |
| **MCP Tools** | 100+ (87 specialized + more) | 29 (AgentDB only) |
| **Agent Types** | 64 specialized | 66 prompt templates |
| **Memory System** | Hybrid (AgentDB + ReasoningBank) | AgentDB + ReasoningBank |
| **CLI** | `npx claude-flow` | `npx agentic-flow` |
| **Model Routing** | Via agentic-flow dependency | ✅ Core feature |
| **Swarm Coordination** | Queen-led hive-mind | QUIC coordinator |
| **Skills System** | 25 Claude Skills | None |
| **Hooks System** | Advanced pre/post | Basic lifecycle |
| **Claude Desktop** | Primary target (MCP) | Can integrate |
| **Standalone Use** | ✅ Yes | ✅ Yes |
| **Performance** | 84.8% SWE-Bench solve | Claims unverified |
| **Memory Speed** | 96x-164x faster (claimed) | Via AgentDB dependency |
| **Production Ready** | ✅ (per docs) | ⚠️ Questionable |
| **Version** | v2.7.34 (stable) | v1.10.2 |
| **Node Requirement** | >=20.0.0 | >=18.0.0 |
| **Package Size** | Larger (includes orchestration) | Smaller (focused) |

---

## 4. Component-by-Component Comparison

### 4.1 Memory Systems

**Claude-Flow:**
```typescript
// Hybrid memory with dual backends
{
  AgentDB: {
    version: "1.6.1",
    performance: "96x-164x faster vector search",
    features: ["HNSW indexing", "9 RL algorithms", "Reflexion memory"]
  },
  ReasoningBank: {
    backend: "SQLite",
    latency: "2-3ms pattern search",
    embeddings: "hash-based 1024-dim (no API key needed)"
  }
}
```

**Agentic-Flow:**
```typescript
// Same components but as dependencies
{
  AgentDB: {
    version: "1.4.3",  // Older version
    performance: "Brute-force similarity (no HNSW)",
    features: ["ReflexionMemory", "SkillLibrary", "CausalMemoryGraph"]
  },
  ReasoningBank: {
    version: "1.7.1",
    backend: "SQLite + WASM",
    latency: "Not separately benchmarked"
  }
}
```

**Winner:** **Claude-Flow** (newer AgentDB with HNSW, better performance)

---

### 4.2 MCP Tools Count

**Claude-Flow: 100+ Tools**

Categories:
- Orchestration & Coordination (3)
- Memory Management (2)
- Neural/Pattern Systems (3)
- GitHub Integration (multiple)
- Performance Analysis (multiple)
- Swarm operations (extensive)
- File system tools
- Process management
- ... (87 specialized tools documented)

**Agentic-Flow: 29 Tools (AgentDB only)**

From AgentDB MCP server:
- Reflexion memory operations
- Skill library management
- Causal graph queries
- Pattern storage
- ... (29 total)

**Claimed:** "213 total" by counting:
- 29 from AgentDB
- 101 from "claude-flow" (external)
- 96 from "flow-nexus" (external)

**Winner:** **Claude-Flow** (actually provides 100+ tools, vs agentic-flow claiming external tools)

---

### 4.3 Swarm Coordination

**Claude-Flow:**
```typescript
// Queen-led hive-mind architecture
class HiveMind {
  topology: 'queen-led' | 'mesh' | 'hierarchical';
  queen: Agent;  // Coordinates workers
  workers: Agent[];

  async spawn(projectName) {
    // Creates coordinated agent team
    // Queen manages task distribution
    // Workers report back to queen
  }
}
```

**Features:**
- ✅ Queen agent for coordination
- ✅ Dynamic task distribution
- ✅ Fault tolerance (worker failures)
- ✅ Session persistence and resume
- ✅ Status tracking

**Agentic-Flow:**
```typescript
// QUIC-based swarm (as analyzed)
class QuicCoordinator {
  topology: 'mesh' | 'hierarchical' | 'ring' | 'star';

  // NO queen agent
  // NO hierarchical coordination
  // Just message passing
}
```

**Features:**
- ⚠️ QUIC messaging (partial)
- ⚠️ Topology routing (basic)
- ❌ No queen/leader election
- ❌ No fault recovery beyond retries

**Winner:** **Claude-Flow** (proper swarm architecture vs basic messaging)

---

### 4.4 Agent Types

**Claude-Flow: 64 Agents**
- Research Agents (analyst, data-scientist, ml-engineer)
- Development Agents (full-stack, backend, frontend, mobile)
- Testing Agents (qa-engineer, security-tester, performance-tester)
- DevOps Agents (sre, infrastructure, cloud-architect)
- Documentation Agents (technical-writer, api-doc-generator)
- Specialized (blockchain-dev, game-dev, embedded-systems)

**Agentic-Flow: 66 Agents**
- Core Development (coder, reviewer, tester, planner, researcher)
- Specialized (backend-dev, mobile-dev, ml-developer, system-architect)
- Swarm Coordinators (hierarchical, mesh, adaptive)
- GitHub Integration (pr-manager, code-review-swarm, issue-tracker)

**Reality:** Both are just **prompt templates**, not separate models

**Winner:** **Tie** (both overcount prompt variations as "agents")

---

### 4.5 Skills vs Optimization

**Claude-Flow: 25 Claude Skills**

Natural language commands:
```bash
# In Claude Desktop chat
@claude "pair program a REST API with me"
@claude "review this codebase for security issues"
@claude "coordinate a swarm to build feature X"
```

Skills are **activated keywords** that trigger:
- Pre-configured prompts
- Tool chains
- Memory retrieval
- Multi-agent workflows

**Agentic-Flow: Model Optimization**

```bash
# CLI optimization flags
npx agentic-flow --agent coder --task "..." --optimize
npx agentic-flow --agent coder --task "..." --priority cost
npx agentic-flow --agent coder --task "..." --provider gemini
```

Optimization is **model routing**:
- Select cheaper models
- Cost tracking
- Provider switching

**Winner:** **Claude-Flow** (skills are more sophisticated than basic routing)

---

## 5. Use Case Scenarios

### Scenario 1: Claude Desktop Power User

**Best Choice: Claude-Flow** ✅

```bash
# Install Claude-Flow
npm install -g claude-flow@alpha

# Add to Claude Desktop
claude mcp add claude-flow npx claude-flow@alpha mcp start

# Use in chat
@claude "coordinate a swarm to analyze this codebase"
@claude "use hive-mind to build feature X with multiple agents"
```

**Why:**
- Native MCP integration with 100+ tools
- Skills work directly in Claude chat
- Swarm coordination built-in
- Better memory system

---

### Scenario 2: Multi-Model Cost Optimization

**Best Choice: Agentic-Flow** ✅

```bash
# Switch to cheap model
npx agentic-flow --agent coder --task "..." --provider openrouter \
  --model "deepseek/deepseek-chat"

# Use free local model
npx agentic-flow --agent coder --task "..." --provider onnx

# Optimize for cost
npx agentic-flow --agent coder --task "..." --optimize --priority cost
```

**Why:**
- Core focus is model routing
- Supports 4 providers (Anthropic, Gemini, OpenRouter, ONNX)
- Cost tracking built-in
- Can switch models per task

---

### Scenario 3: Production Agent Deployment

**Best Choice: Agentic-Flow** ✅

```bash
# Deploy agent as service
docker build -f deployment/Dockerfile -t my-agent .

docker run -d \
  -e ANTHROPIC_API_KEY=... \
  my-agent --agent reviewer --task "..."
```

**Why:**
- Docker deployment docs
- Lighter weight (fewer dependencies)
- Federation hub for distributed agents
- Cloud deployment focus

---

### Scenario 4: Complex Multi-Agent Projects

**Best Choice: Claude-Flow** ✅

```bash
# Start hive-mind project
npx claude-flow@alpha hive-mind spawn "microservices-migration" --claude

# Queen agent coordinates:
# - Analyst agents (understand current system)
# - Architect agents (design new system)
# - Coder agents (implement services)
# - Tester agents (validate each service)
# - DevOps agents (deploy infrastructure)

# All coordinated by queen, with persistent session
```

**Why:**
- Queen-led coordination
- Session persistence and resume
- Built for complex, multi-day projects
- Better agent coordination

---

### Scenario 5: Learning/Experimentation

**Best Choice: Either** ⚠️

Both suitable for learning, depends on focus:
- **Claude-Flow**: Learn swarm coordination, MCP tools
- **Agentic-Flow**: Learn multi-model routing, ReasoningBank

---

## 6. Performance Claims Comparison

### Claude-Flow Claims

**Benchmarks:**
- 84.8% SWE-Bench solve rate ✅ (documented)
- 96x-164x faster vector search ⚠️ (AgentDB v1.6.1)
- 32.3% token reduction ✅ (context management)
- 2.8-4.4x speed improvement ✅ (parallel coordination)
- >90% test coverage ✅ (180 AgentDB tests)

**Verdict:** **More verifiable** - actual benchmark results documented

### Agentic-Flow Claims

**Benchmarks:**
- 352x faster code operations ⚠️ (Agent Booster, unverified)
- 46% faster execution ⚠️ (ReasoningBank, from paper not replicated)
- 85-99% cost savings ⚠️ (just routing to cheaper models)
- "p95 < 50ms" ⚠️ (AgentDB, depends on DB size)
- "150x faster HNSW" ❌ (Not in AgentDB v1.4.3)

**Verdict:** **More questionable** - many claims unverified or oversold

---

## 7. Architecture & Design Philosophy

### Claude-Flow: Orchestration-First

**Philosophy:** "One platform for all Claude workflows"

**Design:**
```
User → Claude Desktop → Claude-Flow MCP Tools → Coordinated Agents
                           ├── Skills activation
                           ├── Memory retrieval
                           ├── Swarm spawning
                           └── Task coordination
```

**Strengths:**
- Tight Claude Desktop integration
- Comprehensive tooling
- Swarm intelligence focus
- Enterprise features (sessions, persistence)

**Weaknesses:**
- Heavier dependency tree
- Requires Claude Desktop for full value
- More complex setup

---

### Agentic-Flow: Flexibility-First

**Philosophy:** "Run agents anywhere with any model"

**Design:**
```
User → CLI → Agentic-Flow Router → Model Provider
                 ├── Anthropic (Claude)
                 ├── Gemini (Google)
                 ├── OpenRouter (100+ models)
                 └── ONNX (local/free)
```

**Strengths:**
- Model flexibility
- Standalone deployment
- Lighter weight
- Cost optimization

**Weaknesses:**
- Less sophisticated orchestration
- Oversold features (causal reasoning, Byzantine consensus)
- Fragmented ecosystem (multiple external repos)

---

## 8. Ecosystem & Maintenance

### Claude-Flow

**Repository:** github.com/ruvnet/claude-flow
**Stars:** 9.8k
**Forks:** 1.3k
**Commits:** 3,086
**Status:** Active development (v2.7.34)

**Release Cadence:**
- v2.0.0-alpha → v2.7.34 (frequent alphas)
- Active issue tracking
- Regular bug fixes

**Community:**
- Larger following (9.8k stars)
- Active discussions
- Wiki documentation

---

### Agentic-Flow

**Repository:** github.com/ruvnet/agentic-flow
**Stars:** ~1k (est., not shown in search)
**Status:** Active development (v1.10.2)

**Release Cadence:**
- v1.0.0 → v1.10.2
- Stable releases (not alpha)

**Community:**
- Smaller (dependency of Claude-Flow)
- Less documented
- More fragmented (agentdb, flow-nexus separate)

**Winner:** **Claude-Flow** (more popular, better maintained)

---

## 9. Pricing & Cost Comparison

### Claude-Flow

**Package Cost:** FREE (MIT license)

**Runtime Costs:**
- Claude API: $3-15/1M tokens (if using Claude)
- AgentDB: FREE (local SQLite)
- ReasoningBank: FREE (local)
- MCP tools: FREE

**Total:** $0 + API costs (if using Claude)

---

### Agentic-Flow

**Package Cost:** FREE (MIT license)

**Runtime Costs:**
- Anthropic: $3-15/1M tokens
- Gemini: $0.07-0.30/1M tokens (cheaper)
- OpenRouter: Varies by model
- ONNX: FREE (local)

**Total:** $0 to $15/1M tokens (depends on provider choice)

**Winner:** **Agentic-Flow** (can use free local models)

---

## 10. When to Use Which

### Use Claude-Flow When:

✅ Primary development environment is **Claude Desktop**
✅ Need **comprehensive MCP tool** integration
✅ Building **complex multi-agent projects** (hive-mind)
✅ Want **session persistence** and resume capability
✅ Need **queen-led swarm** coordination
✅ Prefer **skills-based** interaction (natural language)
✅ Willing to use **Claude API** (not cost-sensitive)

### Use Agentic-Flow When:

✅ Need **multi-model flexibility** (Gemini, OpenRouter, ONNX)
✅ Optimizing for **cost** (use cheaper/free models)
✅ Deploying **standalone agents** (Docker, cloud)
✅ Want **lightweight** deployment (fewer dependencies)
✅ Need **Federation Hub** for ephemeral agents
✅ Building on **ReasoningBank** research
✅ Don't need Claude Desktop integration

### Use BOTH When:

✅ Claude-Flow for orchestration, Agentic-Flow for execution (Claude-Flow already does this!)
✅ Want Claude Desktop + model flexibility (install both)

---

## 11. Bottom Line

### Claude-Flow: 8/10

**Positives (+):**
- ✅ Comprehensive orchestration platform
- ✅ 100+ MCP tools actually delivered
- ✅ Proper swarm architecture (queen-led)
- ✅ Better memory system (newer AgentDB)
- ✅ Active development and community
- ✅ Verifiable performance benchmarks

**Negatives (-):**
- ⚠️ Heavier dependencies
- ⚠️ Requires Claude Desktop for full value
- ⚠️ Some performance claims need verification

**Best For:** Claude Desktop power users, complex multi-agent projects

---

### Agentic-Flow: 6.5/10

**Positives (+):**
- ✅ Multi-model routing works well
- ✅ Cost optimization through model switching
- ✅ ReasoningBank is legitimate (Google DeepMind paper)
- ✅ Federation Hub smart for cost savings
- ✅ Standalone deployment

**Negatives (-):**
- ❌ AgentDB "causal reasoning" is false
- ❌ "213 MCP tools" counts external packages
- ❌ Byzantine consensus doesn't exist
- ❌ Many performance claims unverified
- ⚠️ Fragmented ecosystem

**Best For:** Cost-conscious deployments, model flexibility, standalone agents

---

## 12. The Ironic Truth

### Marketing vs Reality

**Agentic-Flow claims:**
> "Built on... Claude Flow (101 MCP tools)"

**Reality:**
> **Claude-Flow** depends on **Agentic-Flow** (v1.9.4), not vice versa!

**The Actual Relationship:**
```
claude-flow v2.7.34
├── agentic-flow ^1.9.4 (dependency)
├── agentdb ^1.6.1
├── ruv-swarm ^1.0.14
└── flow-nexus ^0.1.128

All by same author (ruvnet), all cross-reference each other in marketing!
```

### Why the Confusion?

**Same author** (ruvnet) markets them as:
- Agentic-Flow: "Powered by Claude Flow"
- Claude-Flow: "Uses Agentic-Flow for execution"

**Reality:**
- Claude-Flow is the **parent platform**
- Agentic-Flow is a **component/dependency**
- Marketing creates circular reference illusion

---

## 13. Final Recommendations

### For Claude Desktop Users

**Winner:** **Claude-Flow** (8/10)

Install Claude-Flow, which includes agentic-flow as dependency. Get:
- 100+ MCP tools
- Swarm coordination
- Skills system
- Better memory
- All agentic-flow features

---

### For Cost-Sensitive Deployments

**Winner:** **Agentic-Flow** (6.5/10)

Use agentic-flow standalone for:
- Free ONNX models
- Cheap Gemini routing
- OpenRouter flexibility
- Lighter deployment

---

### For Enterprise

**Winner:** **Claude-Flow** (8/10)

Better for enterprise because:
- More comprehensive platform
- Better architecture
- Session persistence
- Verifiable benchmarks
- Active community

---

### For Research/Learning

**Winner:** **Agentic-Flow** (6.5/10)

If studying:
- ReasoningBank (Google DeepMind paper)
- Federation Hub patterns
- Multi-model routing
- Ephemeral agents

**Winner:** **Claude-Flow** (8/10)

If studying:
- Swarm intelligence
- MCP protocol
- Agent orchestration
- Production AI systems

---

## Summary Table

| Aspect | Claude-Flow | Agentic-Flow |
|--------|-------------|--------------|
| **Purpose** | Orchestration platform | Multi-model router |
| **Relationship** | Parent (depends on agentic-flow) | Dependency |
| **MCP Tools** | 100+ (actual) | 29 (claimed 213) |
| **Memory** | Hybrid (newer AgentDB) | AgentDB + ReasoningBank |
| **Swarms** | Queen-led ✅ | Message-passing |
| **Benchmarks** | Verified ✅ | Mostly unverified ❌ |
| **Marketing** | More honest | Oversells significantly |
| **Community** | 9.8k stars | Smaller |
| **Rating** | **8/10** | **6.5/10** |
| **Best For** | Claude Desktop users | Cost-conscious devs |

---

## Related Documents

- [ANALYSIS_6_AGENTIC_FLOW_COMPLETE.md](ANALYSIS_6_AGENTIC_FLOW_COMPLETE.md) - Full agentic-flow analysis
- [ANALYSIS_4_AGENTDB_MODULE.md](ANALYSIS_4_AGENTDB_MODULE.md) - AgentDB deep dive
- [ANALYSIS_5_REASONINGBANK_MODULE.md](ANALYSIS_5_REASONINGBANK_MODULE.md) - ReasoningBank analysis
- [ANALYSIS_7_FEDERATION_HUB.md](ANALYSIS_7_FEDERATION_HUB.md) - Federation Hub architecture

---

**Analysis Date:** 2025-11-15
**Claude-Flow Version:** v2.7.34
**Agentic-Flow Version:** v1.10.2
**Key Finding:** Claude-Flow is parent platform, Agentic-Flow is dependency
**Analyst:** Claude (Sonnet 4.5)
