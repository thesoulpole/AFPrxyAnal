# Flow-Nexus Deep Dive: The Actual Workflow & E2B Billing

## TL;DR: The Confusion

**There are TWO separate credit/billing systems:**

1. **E2B Credits** ($100 free) - Real cloud infrastructure billing
2. **rUv Credits** (356 free) - Flow-Nexus gamification layer

**Flow-Nexus sits ON TOP of E2B, adding a gamified economic layer.**

---

## Part 1: E2B Billing (The Real Money)

### How E2B Billing Works

**E2B = Cloud infrastructure for code sandboxes**

#### **Pricing Tiers:**

| Tier | Cost | Credits | Sandbox Duration | Concurrency |
|------|------|---------|------------------|-------------|
| **Hobby (Free)** | $0/month | $100 compute credits | Limited session length | Limited |
| **Pro** | $150/month + usage | Pay as you go | Up to 24 hours | Higher |
| **Ultimate** | Custom | Custom | Unlimited | Unlimited |

#### **How Charges Work:**

```
Charge = (Sandbox runtime in seconds) × (CPU/memory spec)

Example:
- 1 sandbox with 2 vCPU, 4GB RAM
- Running for 3600 seconds (1 hour)
- Cost: ~$0.10-0.50 depending on spec

Your $100 free credits = ~200-1000 hours of basic sandbox use
```

#### **How to Pay E2B:**

1. **Sign up:** Go to https://e2b.dev
2. **Get API key:** Dashboard → API Keys → Create
3. **Free tier:** Automatic $100 credits
4. **Add payment:** Dashboard → Billing → Add payment method (credit card)
5. **Monitoring:** Dashboard shows credit consumption in real-time

#### **Why Your Credits "Disappeared":**

Common issues:

**Scenario 1: Auto-consumption**
```
You created a sandbox and forgot to close it.
Sandbox ran for 8 hours = $4-8 consumed.
```

**Scenario 2: Multiple sandboxes**
```
Parallel sandboxes multiply cost:
5 sandboxes × 1 hour each = 5 hours of charges
```

**Scenario 3: Expiration**
```
Some promotional credits have expiration dates.
Check: E2B Dashboard → Billing → Credits
```

**Scenario 4: Failed verification**
```
Account verification issues can lock credits.
Contact: E2B support to unlock
```

#### **How to Avoid Losing Credits:**

```javascript
// ALWAYS close sandboxes when done
const sandbox = await Sandbox.create();
try {
  await sandbox.execute("npm test");
} finally {
  await sandbox.close();  // ← CRITICAL!
}

// Set timeouts
const sandbox = await Sandbox.create({
  timeoutMs: 3600000  // 1 hour max
});

// Monitor usage
console.log(await E2B.getUsage());
```

---

## Part 2: Flow-Nexus Architecture

### What Flow-Nexus Actually Is

**Flow-Nexus = Gamified orchestration layer ON TOP of E2B**

```
┌─────────────────────────────────────┐
│    Flow-Nexus Platform              │
│    - rUv credit economy             │
│    - Challenge system               │
│    - Agent orchestration            │
│    - MCP tools (96+)                │
└──────────────┬──────────────────────┘
               │ Uses
┌──────────────▼──────────────────────┐
│    E2B Cloud Infrastructure         │
│    - Real sandboxes                 │
│    - Actual compute                 │
│    - Real billing ($)               │
└─────────────────────────────────────┘
```

**Key distinction:**
- **E2B:** Infrastructure provider (like AWS EC2)
- **Flow-Nexus:** Platform built on E2B (like Heroku on AWS)

### The Two Credit Systems

#### **E2B Credits (Real Money)**
- $100 free on signup
- Pay-as-you-go after that
- Charges for actual compute time
- Managed at e2b.dev dashboard

#### **rUv Credits (Gamification)**
- 356 starting credits (100 registration + 256 bonus)
- Earned through challenges, achievements, marketplace
- Spent on Flow-Nexus features
- **NOT real money** - internal currency

**Example:**
```
You have:
- $95 E2B credits (real)
- 500 rUv credits (gamification)

You run a Flow-Nexus swarm:
- Costs: 10 rUv (platform fee)
- Also consumes: ~$0.50 E2B credits (infrastructure)

Both systems charge simultaneously!
```

---

## Part 3: The Actual Workflow

### Setup Sequence (First Time)

#### **Step 1: E2B Account Setup**

```bash
# 1. Go to https://e2b.dev
# 2. Sign up → verify email
# 3. Dashboard → API Keys → Create
# 4. Copy API key: e2b_xxxxxxxxxxxxxxxxxx
# 5. Set environment variable:
export E2B_API_KEY="e2b_xxxxxxxxxxxxxxxxxx"
```

**What you get:**
- ✅ E2B account
- ✅ $100 free credits
- ✅ API key for sandbox creation

#### **Step 2: Flow-Nexus Registration**

```bash
# Register Flow-Nexus account (separate from E2B!)
npx flow-nexus@latest auth register -e your@email.com -p password

# What happens:
# - Flow-Nexus account created
# - 356 rUv credits deposited
# - Authentication token issued
```

**What you get:**
- ✅ Flow-Nexus account (NOT E2B account)
- ✅ 356 rUv credits (gamification)
- ✅ Access to challenges, marketplace

**Important:** This is a SEPARATE account from E2B!

#### **Step 3: Claude Flow Initialization**

```bash
# Initialize Claude Flow with Flow-Nexus integration
npx claude-flow@alpha init --flow-nexus

# What happens:
# - Creates .claude/ directory
# - Writes CLAUDE.md
# - Configures Flow-Nexus MCP integration
# - Links E2B_API_KEY from environment
```

**What you get:**
- ✅ Claude Flow installed locally
- ✅ Flow-Nexus MCP tools configured
- ✅ E2B integration ready

#### **Step 4: MCP Server Registration**

```bash
# Add Flow-Nexus MCP server to Claude Desktop
claude mcp add flow-nexus npx flow-nexus@latest mcp start

# What happens:
# - Claude Desktop config updated
# - MCP server starts on Claude launch
# - 96+ Flow-Nexus tools available
```

**What you get:**
- ✅ Flow-Nexus tools in Claude Code
- ✅ Can call mcp__flow-nexus__* tools
- ✅ Agent orchestration ready

**Full Claude Desktop MCP config:**
```json
{
  "mcpServers": {
    "flow-nexus": {
      "command": "npx",
      "args": ["flow-nexus@latest", "mcp", "start"],
      "env": {
        "E2B_API_KEY": "e2b_xxxxxxxxx"
      }
    },
    "claude-flow": {
      "command": "npx",
      "args": ["claude-flow@alpha", "mcp", "start"]
    }
  }
}
```

---

### Usage Workflow (After Setup)

#### **Scenario 1: Just Coding (Simple)**

**What you do:**
```bash
# Start Claude Code
cd your-project
claude

# In chat:
You: "Build a REST API with JWT authentication"
```

**What happens:**
```
Claude Code (LLM):
├── Reads CLAUDE.md (agent instructions)
├── Decides to spawn agents
└── Uses Task tool (NOT Flow-Nexus)
    ├── Spawns researcher agent (local subprocess)
    ├── Spawns coder agent (local subprocess)
    └── Spawns tester agent (local subprocess)

Result: All LOCAL execution, no cloud, no E2B, no rUv cost
```

**Cost:**
- ❌ $0 E2B credits (not used)
- ❌ 0 rUv credits (not used)
- ✅ Only Anthropic API costs (for Claude LLM)

**Key point:** Flow-Nexus is OPTIONAL. You can use Claude Flow without it!

---

#### **Scenario 2: Using Flow-Nexus Cloud Execution**

**What you do:**
```bash
claude

You: "Build REST API using Flow-Nexus cloud sandboxes for parallel execution"
```

**What happens:**
```
Claude Code (LLM):
├── Sees "Flow-Nexus" mentioned
├── Calls MCP tool: mcp__flow-nexus__swarm_init({
│     topology: "mesh",
│     maxAgents: 5
│   })
└── Flow-Nexus MCP server:
    ├── Calls E2B API: Sandbox.create() × 5
    │   └── E2B: Spawns 5 Docker containers in cloud
    ├── Deploys agents to each sandbox
    ├── Executes code in parallel
    └── Streams results back to Claude Code

Result: Cloud execution across 5 E2B sandboxes
```

**Cost:**
- ✅ 10 rUv credits (Flow-Nexus swarm init fee)
- ✅ ~$0.50-2 E2B credits (5 sandboxes × runtime)
- ✅ Anthropic API costs (Claude LLM)

**What you get:**
- ✅ Parallel execution in cloud
- ✅ Isolated sandboxes (security)
- ✅ Distributed agent swarm
- ✅ Faster completion

---

#### **Scenario 3: Flow-Nexus Challenges (Gamification)**

**What you do:**
```bash
# List available challenges
npx flow-nexus challenge list

# Submit solution
npx flow-nexus challenge submit challenge-id
```

**What happens:**
```
Flow-Nexus Platform:
├── Creates E2B sandbox
├── Runs your solution code
├── Tests against hidden test cases
├── "Queen Seraphina" AI judge evaluates
└── Awards rUv credits if successful

Leaderboard updated, achievements unlocked
```

**Cost:**
- ✅ 5 rUv credits (challenge submission fee)
- ✅ ~$0.10 E2B credits (test execution)

**What you get:**
- ✅ 500 rUv credits (1st place)
- ✅ Achievements, leaderboard ranking
- ✅ Marketplace visibility boost

---

## Part 4: What You ACTUALLY Get from Flow-Nexus

### Without Flow-Nexus (Just Claude Flow)

```
Capabilities:
✅ Local agent orchestration
✅ Multi-agent coordination (local subprocesses)
✅ AgentDB memory (SQLite)
✅ ReasoningBank patterns
✅ SPARC methodology
✅ Hooks integration

Limitations:
❌ No cloud execution
❌ No parallel sandboxes
❌ No distributed agents
❌ Single machine only
❌ No isolation (all local)
```

### With Flow-Nexus Added

```
Additional Capabilities:
✅ Cloud E2B sandboxes (parallel execution)
✅ Distributed agent swarms
✅ Isolated execution environments
✅ Multiple topologies (mesh, hierarchical, star, ring)
✅ Challenge system (gamification)
✅ Marketplace (share/earn from templates)
✅ Neural network training (distributed)
✅ 96+ MCP tools for orchestration

Requirements:
⚠️ E2B account + API key
⚠️ E2B credits ($100 free, then pay)
⚠️ Flow-Nexus account + rUv credits
⚠️ More complex setup
⚠️ Both billing systems to track
```

---

## Part 5: Command Reference

### Authentication Commands

```bash
# Flow-Nexus registration (first time)
npx flow-nexus@latest auth register -e your@email.com -p password

# Flow-Nexus login (subsequent)
npx flow-nexus@latest auth login -e your@email.com -p password

# Check rUv balance
npx flow-nexus@latest ruv balance

# Logout
npx flow-nexus@latest auth logout
```

### Sandbox Commands

```bash
# List available templates
npx flow-nexus@latest sandbox list-templates

# Create sandbox
npx flow-nexus@latest sandbox create --template node --name my-sandbox

# Execute code in sandbox
npx flow-nexus@latest sandbox execute --id sandbox-123 --code "npm test"

# Delete sandbox (FREE CREDITS!)
npx flow-nexus@latest sandbox delete --id sandbox-123
```

### Challenge Commands

```bash
# List challenges
npx flow-nexus@latest challenge list

# Get challenge details
npx flow-nexus@latest challenge info challenge-id

# Submit solution
npx flow-nexus@latest challenge submit challenge-id

# View leaderboard
npx flow-nexus@latest challenge leaderboard
```

### Marketplace Commands

```bash
# Browse templates
npx flow-nexus@latest marketplace browse

# Publish your template
npx flow-nexus@latest marketplace publish --template my-template

# Check earnings
npx flow-nexus@latest marketplace earnings
```

---

## Part 6: MCP Tools (Inside Claude Code)

### When to Use MCP vs CLI

**Use CLI when:**
- Initial setup (registration, config)
- Manual operations (check balance, browse challenges)
- Testing/debugging

**Use MCP tools when:**
- Inside Claude Code chat
- Automated workflows
- Agent orchestration
- Conversational interface

### Key MCP Tools

```javascript
// Authentication
mcp__flow-nexus__user_register({ email, password })
mcp__flow-nexus__user_login({ email, password })
mcp__flow-nexus__ruv_balance()

// Sandbox management
mcp__flow-nexus__sandbox_create({
  template: "node",
  name: "api-dev",
  env_vars: { API_KEY: "xxx" }
})

mcp__flow-nexus__sandbox_execute({
  sandbox_id: "sandbox-123",
  code: "npm install && npm test",
  language: "bash"
})

mcp__flow-nexus__sandbox_delete({ sandbox_id: "sandbox-123" })

// Swarm orchestration
mcp__flow-nexus__swarm_init({
  topology: "mesh",
  maxAgents: 5
})

mcp__flow-nexus__agent_spawn({
  type: "coder",
  capabilities: ["typescript", "testing"]
})

mcp__flow-nexus__task_orchestrate({
  task: "Build authentication API",
  strategy: "parallel"
})

// Challenges
mcp__flow-nexus__challenge_list()
mcp__flow-nexus__challenge_submit({ challenge_id, code })

// Workflows
mcp__flow-nexus__workflow_create({
  name: "CI/CD Pipeline",
  steps: [
    { type: "sandbox", template: "node" },
    { type: "execute", code: "npm test" },
    { type: "deploy", target: "production" }
  ]
})
```

---

## Part 7: Actual Usage Example (Full Workflow)

### Scenario: Build Authentication API with Tests

#### **Step 1: Start Claude Code**
```bash
cd my-project
claude
```

#### **Step 2: Request with Flow-Nexus**
```
You: "Build a REST API with JWT authentication.
     Use Flow-Nexus to run tests in isolated E2B sandbox."
```

#### **Step 3: Claude Code Executes**

```javascript
// Claude Code (behind the scenes):

// 1. Create local agents for design
Task("Architect", "Design authentication API", "system-architect")

// 2. Create cloud sandbox for execution
mcp__flow-nexus__sandbox_create({
  template: "node",
  name: "auth-api-dev"
})
// → E2B creates Docker container
// → Cost: 1 rUv + ~$0.01 E2B

// 3. Write code locally
Write("src/auth.js", code)
Write("src/middleware/jwt.js", code)

// 4. Execute tests in cloud sandbox
mcp__flow-nexus__sandbox_execute({
  sandbox_id: "sandbox-abc123",
  code: "npm install && npm test",
  language: "bash"
})
// → Runs in isolated E2B container
// → Cost: 2 rUv + ~$0.05 E2B (for runtime)

// 5. If tests pass, deploy
mcp__flow-nexus__sandbox_execute({
  sandbox_id: "sandbox-abc123",
  code: "npm run build && docker build -t api .",
  language: "bash"
})
// → Build happens in cloud
// → Cost: 2 rUv + ~$0.10 E2B

// 6. Cleanup
mcp__flow-nexus__sandbox_delete({ sandbox_id: "sandbox-abc123" })
// → Stops billing!
```

#### **Total Cost:**
- **rUv credits:** 5 (1 + 2 + 2)
- **E2B credits:** ~$0.16 (~$0.01 + ~$0.05 + ~$0.10)
- **Anthropic API:** ~$0.50 (for Claude LLM tokens)

**Total:** ~$0.66 + 5 rUv

---

## Part 8: Common Issues & Solutions

### Issue 1: "E2B credits disappeared"

**Diagnosis:**
```bash
# Check E2B usage
curl -H "Authorization: Bearer e2b_xxx" https://api.e2b.dev/usage

# Look for:
# - Running sandboxes (forgot to close?)
# - Unexpected usage spikes
```

**Solutions:**
- Close all sandboxes: `npx flow-nexus sandbox list` → delete each
- Set timeouts on sandbox creation
- Enable usage alerts in E2B dashboard

### Issue 2: "Flow-Nexus commands fail"

**Check:**
```bash
# 1. E2B API key set?
echo $E2B_API_KEY

# 2. Flow-Nexus authenticated?
npx flow-nexus@latest auth login -e your@email.com -p password

# 3. MCP server running?
ps aux | grep "flow-nexus mcp"
```

### Issue 3: "Charges are higher than expected"

**Common causes:**
```javascript
// Forgot to delete sandbox
const sandbox = await mcp__flow-nexus__sandbox_create(...);
// ... (sandbox runs for 8 hours) ...
// Cost: $4-8!

// Solution: Always delete
try {
  await sandbox.execute();
} finally {
  await mcp__flow-nexus__sandbox_delete({ sandbox_id: sandbox.id });
}
```

### Issue 4: "Not using Flow-Nexus, just Claude Flow"

**If you DON'T want cloud execution:**
```bash
# Just use Claude Flow alone
npx claude-flow@alpha init --force
# (Don't add --flow-nexus)

# Start coding
claude

# All execution is LOCAL
# No E2B costs, no rUv credits, no cloud
```

---

## Part 9: The Value Proposition

### When Flow-Nexus Makes Sense

✅ **Parallel execution:** 5+ tasks that can run concurrently
✅ **Isolation needed:** Security testing, untrusted code
✅ **Distributed compute:** Heavy workloads (ML training)
✅ **Team collaboration:** Shared sandboxes, marketplace templates
✅ **Learning:** Challenges, leaderboards, gamification

### When Flow-Nexus Doesn't Make Sense

❌ **Simple coding:** Single-file edits, basic features
❌ **Cost-sensitive:** Every $0.01 matters
❌ **Local preference:** Don't want cloud dependency
❌ **Simple projects:** Overhead not worth it
❌ **Prototyping:** Just testing ideas

---

## Part 10: Summary

### The Three Layers

```
┌─────────────────────────────────────┐
│  1. Claude Flow (Local)             │
│     - Hooks + templates             │
│     - Local agent orchestration     │
│     - AgentDB + ReasoningBank       │
│     Cost: $0                        │
└──────────────┬──────────────────────┘
               │ Optional upgrade
┌──────────────▼──────────────────────┐
│  2. Flow-Nexus (Orchestration)      │
│     - MCP tools (96+)               │
│     - rUv credit economy            │
│     - Challenge system              │
│     Cost: rUv credits (gamification)│
└──────────────┬──────────────────────┘
               │ Uses
┌──────────────▼──────────────────────┐
│  3. E2B (Infrastructure)            │
│     - Real cloud sandboxes          │
│     - Docker containers             │
│     - Actual compute                │
│     Cost: $100 free → pay-as-you-go │
└─────────────────────────────────────┘
```

### Command Order (First Time Setup)

```bash
# 1. E2B Account
# → Go to e2b.dev, sign up, get API key
export E2B_API_KEY="e2b_xxx"

# 2. Flow-Nexus Registration
npx flow-nexus@latest auth register -e your@email.com -p password

# 3. Claude Flow Init
npx claude-flow@alpha init --flow-nexus

# 4. MCP Server
claude mcp add flow-nexus npx flow-nexus@latest mcp start

# 5. Start Coding
claude
```

### What You Actually Get

**From Claude Flow alone:**
- Local multi-agent orchestration
- Memory/learning (AgentDB, ReasoningBank)
- Hooks integration
- Cost: $0 (just Anthropic API)

**Adding Flow-Nexus:**
- Cloud sandbox execution (E2B)
- Parallel distributed agents
- Isolation/security
- Gamification (challenges, marketplace)
- Cost: $100 E2B free credits + 356 rUv credits + ongoing usage

---

## Recommendations

**Start simple:**
1. Use Claude Flow alone first
2. Learn local orchestration
3. Add Flow-Nexus only when you need cloud/parallel execution

**Monitor costs:**
1. E2B Dashboard → Usage (real money)
2. Flow-Nexus → rUv balance (gamification)
3. Always close sandboxes when done!

**Best practices:**
```javascript
// Set timeouts
const sandbox = await create({ timeoutMs: 3600000 });

// Always cleanup
try {
  await sandbox.execute();
} finally {
  await sandbox.delete();
}

// Monitor usage
console.log(await E2B.getUsage());
console.log(await FlowNexus.getRuvBalance());
```

**Key insight:** Flow-Nexus is OPTIONAL. Claude Flow works great without it. Only add Flow-Nexus when you specifically need cloud sandboxes for parallel/isolated execution.
