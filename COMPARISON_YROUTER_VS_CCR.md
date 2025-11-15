# y-router vs CCR: Complete Comparison

**TL;DR**: They solve COMPLETELY DIFFERENT problems. y-router is a simple translator, CCR is a sophisticated router. Use **BOTH TOGETHER** for maximum value, or CCR alone if you don't need translation.

---

## At A Glance

| Aspect | y-router | CCR (claude-code-router) | Winner |
|--------|----------|--------------------------|--------|
| **What it is** | Format translator | Multi-provider router | Different tools |
| **Code Size** | ~500 lines TS | ~3,000 lines TS | y-router (simpler) |
| **Package Size** | ~50 KB | 485 KB / 2.7 MB | y-router (smaller) |
| **Purpose** | Convert Anthropic ↔ OpenAI format | Route requests to optimal providers | Different purposes |
| **Routing Logic** | NONE (just translates) | Advanced category-based | CCR (actual routing) |
| **Multi-Provider** | No | Yes | CCR |
| **Cost Optimization** | No | Yes (40-60% savings) | CCR |
| **Tool Call Handling** | Basic translation | Advanced buffered | CCR (more robust) |
| **Deployment** | Cloudflare Workers / Docker | Node.js server | y-router (edge) |
| **Configuration** | 1 env var | 1 JSON file | Tie (both simple) |
| **Use Case** | Use Claude Code with OpenRouter | Use multiple providers intelligently | Different use cases |

**Verdict**: Not competitors! They solve different problems. Can be used together!

---

## What They Actually Do

### y-router: Format Translator Only

**Purpose**: Make Claude Code work with OpenRouter/OpenAI-compatible APIs

**What It Does**:
1. Receives Anthropic API format requests
2. Translates to OpenAI chat completion format
3. Forwards to OpenRouter (or any OpenAI-compatible API)
4. Translates response back to Anthropic format
5. Returns to Claude Code

**What It Does NOT Do**:
- ❌ No routing between multiple providers
- ❌ No optimization logic
- ❌ No cost analysis
- ❌ No provider selection
- ❌ No health monitoring
- ❌ No load balancing

**Simple Flow**:
```
Claude Code (Anthropic format)
    ↓
y-router (translate)
    ↓
OpenRouter (OpenAI format)
    ↓
y-router (translate back)
    ↓
Claude Code
```

### CCR: Intelligent Multi-Provider Router

**Purpose**: Route Claude Code requests to optimal providers based on request type

**What It Does**:
1. Receives Anthropic API format requests
2. Analyzes request (tokens, category, tools)
3. Selects optimal provider based on rules
4. Converts format if needed
5. Handles tool calls with buffering
6. Returns response

**What It Does**:
- ✅ Routes between multiple providers
- ✅ Category-based optimization
- ✅ Cost optimization (40-60% savings)
- ✅ Health monitoring
- ✅ Load balancing
- ✅ Automatic provider failover

**Complex Flow**:
```
Claude Code
    ↓
CCR (analyze: thinking mode?)
    ├─ Yes → AWS CodeWhisperer Claude 4
    ├─ Background task? → CodeWhisperer Haiku
    ├─ Search tools? → OpenAI GPT-4
    └─ Default → Your configured provider
    ↓
Selected Provider
```

---

## Key Difference: Translator vs. Router

### y-router is a TRANSLATOR
- Converts between API formats
- No decision making
- Always goes to same endpoint
- Like a language interpreter

### CCR is a ROUTER
- Makes intelligent routing decisions
- Selects between providers
- Optimizes for cost/performance
- Like a smart traffic director

---

## Technology Stack

### y-router

**Runtime**: Cloudflare Workers (serverless edge) or Docker
**Framework**: None (pure Worker API)
**Dependencies**: ZERO external dependencies
**Code**: ~500 lines TypeScript

**Architecture**:
```typescript
// Literally just this:
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const anthropicRequest = await request.json();
    const openaiRequest = formatAnthropicToOpenAI(anthropicRequest);

    const openaiResponse = await fetch(OPENROUTER_URL, {
      body: JSON.stringify(openaiRequest)
    });

    const anthropicResponse = formatOpenAIToAnthropic(openaiData);
    return new Response(JSON.stringify(anthropicResponse));
  }
}
```

**Deployment**:
- Cloudflare Workers (edge, global)
- Docker (local)
- No server needed (serverless)

### CCR

**Runtime**: Node.js
**Framework**: Fastify (high-performance)
**Dependencies**: 12 packages (Fastify, tiktoken, etc.)
**Code**: ~3,000 lines TypeScript

**Architecture**:
```typescript
// Much more complex:
class RouterServer {
  inputProcessor: AnthropicInputProcessor
  routingEngine: RoutingEngine
  outputProcessor: AnthropicOutputProcessor
  providers: Map<ProviderId, ProviderClient>

  async route(request) {
    const category = determineCategory(request)
    const provider = selectProvider(category)
    const response = await provider.execute(request)
    return processResponse(response)
  }
}
```

**Deployment**:
- Node.js server (local or VPS)
- Requires persistent process

---

## Performance Comparison

### y-router Performance

**Latency Added**: ~1-3ms
- Format conversion: ~0.5-1ms (very fast)
- Cloudflare edge: ~0.5-1ms routing
- Network hop: ~0.5-1ms (edge network)

**Memory**: ~2-5 MB (Cloudflare Worker limit)
**CPU**: Negligible (just JSON manipulation)
**Deployment**: Global edge (low latency everywhere)

**Overhead**: ~0.2-0.6% of typical request

### CCR Performance

**Latency Added**: ~2-5ms
- Category detection: ~1-2ms (includes token counting)
- Routing decision: ~0.5-1ms
- Provider selection: ~0.5-1ms
- HTTP overhead: ~1-2ms (Fastify)

**Memory**: ~50-80 MB (Node.js process)
**CPU**: ~1-2% for routing logic
**Deployment**: Single server (latency varies by location)

**Overhead**: ~1% of typical request

**Winner**: y-router (faster) but both negligible

---

## Configuration Comparison

### y-router Configuration

**File**: NONE (optional docker.env)
**Environment Variables**: 1-2

```bash
# That's literally it
OPENROUTER_BASE_URL="https://openrouter.ai/api/v1"  # Optional, has default

# Usage:
export ANTHROPIC_BASE_URL="https://cc.yovy.app"  # Point to y-router
export ANTHROPIC_API_KEY="your-openrouter-key"
```

**Setup Time**: 2 minutes
**Complexity**: 1/10 (trivial)

### CCR Configuration

**File**: `~/.claude-code-router/config.json`
**Environment Variables**: 3-5

```json
{
  "server": { "port": 3456 },
  "routing": {
    "rules": {
      "default": { "provider": "codewhisperer", "model": "claude-4" },
      "background": { "provider": "codewhisperer", "model": "haiku" },
      "search": { "provider": "openai", "model": "gpt-4" }
    },
    "providers": {
      "codewhisperer": {
        "type": "codewhisperer",
        "endpoint": "https://codewhisperer.us-east-1.amazonaws.com",
        "authentication": { "tokenPath": "~/.aws/sso/cache/token.json" }
      }
    }
  }
}
```

**Setup Time**: 5 minutes
**Complexity**: 2/10 (simple)

**Winner**: y-router (simpler) but both are easy

---

## Use Case Analysis

### When to Use y-router

✅ **Use y-router if**:
- You want to use Claude Code with OpenRouter
- You want to access models not available via Anthropic API
- You need simple format translation
- You want edge deployment (Cloudflare Workers)
- You don't need multi-provider routing
- You want minimal overhead
- You want to use free/cheap models via OpenRouter

**Perfect For**:
- Testing different models via OpenRouter
- Using Claude Code in restricted regions
- Accessing OpenRouter's model catalog
- Simple proxy needs
- Serverless/edge deployment

### When to Use CCR

✅ **Use CCR if**:
- You have multiple API providers
- You want automatic cost optimization
- You need category-based routing (thinking/background/search)
- You want health monitoring and failover
- You need AWS CodeWhisperer support
- You want sophisticated tool call handling
- You need load balancing

**Perfect For**:
- Production use with multiple providers
- Cost optimization (40-60% savings)
- Complex routing rules
- High reliability (failover)
- AWS CodeWhisperer integration

### Can You Use Both Together?

**YES!** They complement each other:

```
Claude Code
    ↓
CCR (routes by category)
    ├─ Complex tasks → AWS CodeWhisperer
    ├─ Simple tasks → y-router → OpenRouter (cheap models)
    └─ Search → y-router → OpenRouter GPT-4
```

**Example Config for CCR**:
```json
{
  "providers": {
    "codewhisperer": {
      "type": "codewhisperer",
      "endpoint": "https://codewhisperer.us-east-1.amazonaws.com"
    },
    "openrouter-via-yrouter": {
      "type": "openai",
      "endpoint": "https://cc.yovy.app/v1/messages",
      "authentication": { "apiKey": "your-openrouter-key" }
    }
  },
  "routing": {
    "rules": {
      "default": { "provider": "codewhisperer" },
      "background": { "provider": "openrouter-via-yrouter", "model": "gemini-2.5-flash" },
      "search": { "provider": "openrouter-via-yrouter", "model": "gpt-4" }
    }
  }
}
```

**Benefits of Using Both**:
- CCR handles intelligent routing
- y-router provides access to OpenRouter models
- Best of both worlds: routing + model variety
- Maximum cost savings

---

## Feature Comparison Matrix

| Feature | y-router | CCR | Notes |
|---------|----------|-----|-------|
| **Format Translation** | ✅ Anthropic ↔ OpenAI | ✅ Multiple formats | y-router is simpler |
| **Multi-Provider** | ❌ Single endpoint | ✅ Multiple providers | CCR only |
| **Routing Logic** | ❌ None | ✅ Category-based | CCR only |
| **Cost Optimization** | ❌ None | ✅ 40-60% savings | CCR only |
| **Auto Provider Selection** | ❌ None | ✅ Yes | CCR only |
| **Health Monitoring** | ❌ None | ✅ Yes | CCR only |
| **Load Balancing** | ❌ None | ✅ Yes | CCR only |
| **Failover** | ❌ None | ✅ Automatic | CCR only |
| **Tool Call Handling** | ⚠️ Basic | ✅ Advanced buffered | CCR better |
| **Token Counting** | ❌ None | ✅ tiktoken | CCR only |
| **Category Detection** | ❌ None | ✅ Automatic | CCR only |
| **Edge Deployment** | ✅ Cloudflare Workers | ❌ Node.js only | y-router only |
| **Serverless** | ✅ Yes | ❌ No | y-router only |
| **Configuration** | ✅ 1 env var | ✅ 1 JSON file | Both simple |
| **Code Size** | ✅ ~500 lines | ⚠️ ~3,000 lines | y-router smaller |
| **Dependencies** | ✅ Zero | ⚠️ 12 packages | y-router fewer |
| **Setup Time** | ✅ 2 min | ✅ 5 min | Both quick |
| **OpenRouter Access** | ✅ Native | ⚠️ Via y-router | y-router easier |
| **AWS CodeWhisperer** | ❌ No | ✅ Native | CCR only |

---

## Real-World Scenarios

### Scenario 1: Simple Use - Just Want OpenRouter

**Goal**: Use Claude Code with OpenRouter models

**Best Choice**: y-router only

**Setup**:
```bash
# 1. Deploy y-router (or use public instance)
export ANTHROPIC_BASE_URL="https://cc.yovy.app"
export ANTHROPIC_API_KEY="your-openrouter-key"

# 2. Done!
claude "help me code"
```

**Why**: No need for CCR's complexity, just want format translation

### Scenario 2: Advanced Use - Multiple Providers + Cost Optimization

**Goal**: Route to AWS CodeWhisperer for main work, OpenRouter for cheap tasks

**Best Choice**: CCR (with optional y-router for OpenRouter)

**Setup**:
```json
{
  "routing": {
    "rules": {
      "default": { "provider": "codewhisperer", "model": "claude-4" },
      "background": { "provider": "openrouter", "model": "gemini-flash" }
    }
  }
}
```

**Why**: Need intelligent routing between providers

### Scenario 3: Maximum Flexibility

**Goal**: Access everything - AWS CodeWhisperer, OpenRouter, multiple providers

**Best Choice**: CCR + y-router together

**Setup**:
```json
{
  "providers": {
    "codewhisperer": { "type": "codewhisperer" },
    "openrouter": {
      "type": "openai",
      "endpoint": "https://cc.yovy.app/v1/messages"  // Via y-router
    }
  },
  "routing": {
    "rules": {
      "thinking": { "provider": "codewhisperer", "model": "claude-4" },
      "background": { "provider": "openrouter", "model": "gemini-flash" },
      "search": { "provider": "openrouter", "model": "gpt-4-turbo" }
    }
  }
}
```

**Why**: Best of both - intelligent routing + full model catalog

---

## Strengths & Weaknesses

### y-router Strengths

✅ **Extreme simplicity** - ~500 lines, zero dependencies
✅ **Edge deployment** - Cloudflare Workers, global low latency
✅ **Serverless** - No server management
✅ **Free tier** - Cloudflare Workers free tier generous
✅ **Low overhead** - ~1-3ms
✅ **Easy debugging** - Simple code, easy to understand
✅ **OpenRouter native** - Direct access to OpenRouter models
✅ **Fast setup** - 2 minutes to deploy

### y-router Weaknesses

❌ **No routing** - Single endpoint only
❌ **No optimization** - Can't choose between providers
❌ **No failover** - If OpenRouter is down, you're stuck
❌ **No cost control** - Can't route cheap tasks to cheap models
❌ **Limited tool handling** - Basic translation only
❌ **No health monitoring** - Can't detect provider issues

### CCR Strengths

✅ **Intelligent routing** - Category-based provider selection
✅ **Cost optimization** - 40-60% savings via smart routing
✅ **Multi-provider** - Route between AWS, OpenAI, etc.
✅ **Health monitoring** - Automatic failover
✅ **Advanced tool calls** - Buffered processing
✅ **Production ready** - Comprehensive error handling
✅ **AWS CodeWhisperer** - Native support
✅ **Token counting** - Uses tiktoken for accurate counting

### CCR Weaknesses

❌ **Not edge** - Requires Node.js server
❌ **More complex** - ~3,000 lines vs 500
❌ **More dependencies** - 12 packages vs zero
❌ **More configuration** - JSON file vs 1 env var
❌ **No serverless** - Need persistent process
❌ **OpenRouter harder** - Need format conversion (or use y-router!)

---

## Performance Impact on Claude Code

### y-router Impact

**Added Latency**: 1-3ms (~0.2-0.6%)
- Format conversion: Very fast JSON manipulation
- Edge routing: Cloudflare global network
- Network hop: Minimal (edge deployment)

**User Experience**: Imperceptible
**Streaming**: Smooth, no lag
**Tool Calls**: Works but basic

### CCR Impact

**Added Latency**: 2-5ms (~1%)
- Routing decision: Includes token counting
- Category detection: Analyzes request
- Provider selection: Evaluates rules
- HTTP overhead: Fastify is fast

**User Experience**: Imperceptible
**Streaming**: Smooth, no lag
**Tool Calls**: Robust buffered handling

### Using Both Together Impact

**Added Latency**: 3-8ms (~1-1.5%)
- CCR routing: 2-5ms
- y-router translation: 1-3ms
- Still negligible!

**User Experience**: Still imperceptible

**Winner**: All three options are performant

---

## Cost Analysis

### y-router Costs

**Deployment**:
- Cloudflare Workers: **FREE** (100k requests/day)
- Docker: **FREE** (self-host)

**Usage**:
- No routing optimization
- All requests go to OpenRouter
- Cost = OpenRouter pricing (varies by model)

**Total**: $0 for infrastructure, pay OpenRouter rates

### CCR Costs

**Deployment**:
- Self-host: **FREE** (your server)
- VPS: ~$5-10/month

**Usage**:
- **Saves 40-60%** via intelligent routing
- Routes simple tasks to cheap models
- Routes complex tasks to good models

**Total**: ~$5/month infrastructure, but saves $$$$ on API calls

### Using Both Together Costs

**Deployment**:
- y-router: FREE (Cloudflare)
- CCR: ~$5/month (VPS)

**Usage**:
- **Maximum savings** - CCR optimizes routing
- y-router provides access to cheap OpenRouter models
- Best cost optimization possible

**Total**: ~$5/month infrastructure, maximum API savings

---

## Recommendation Matrix

### Use ONLY y-router If:

1. ✅ You want to use Claude Code with OpenRouter
2. ✅ You only need one API endpoint
3. ✅ You want simplest possible setup
4. ✅ You prefer serverless/edge deployment
5. ✅ You don't need routing optimization
6. ✅ You want to test models quickly

**Effort**: 2 minutes
**Benefit**: Access to OpenRouter models
**Cost**: $0 infrastructure

### Use ONLY CCR If:

1. ✅ You have multiple API providers
2. ✅ You want cost optimization (40-60% savings)
3. ✅ You need intelligent routing
4. ✅ You want AWS CodeWhisperer support
5. ✅ You need health monitoring/failover
6. ✅ You're doing production work

**Effort**: 5 minutes
**Benefit**: 40-60% cost savings + reliability
**Cost**: ~$5/month VPS

### Use BOTH Together If:

1. ✅ You want maximum flexibility
2. ✅ You want intelligent routing + OpenRouter access
3. ✅ You want best cost optimization
4. ✅ You have multiple providers
5. ✅ You want CCR's routing with OpenRouter's model catalog

**Effort**: 10 minutes (setup both)
**Benefit**: Best of both worlds
**Cost**: ~$5/month VPS (y-router is free)

---

## Migration Paths

### From Nothing → y-router

```bash
# 1. Point Claude Code to y-router
export ANTHROPIC_BASE_URL="https://cc.yovy.app"
export ANTHROPIC_API_KEY="your-openrouter-key"

# Done! (2 minutes)
```

### From Nothing → CCR

```bash
# 1. Install CCR
npm install -g claude-code-router

# 2. Create config
mkdir -p ~/.claude-code-router
cat > ~/.claude-code-router/config.json << 'EOF'
{
  "server": { "port": 3456 },
  "routing": {
    "rules": {
      "default": { "provider": "main", "model": "claude-4" }
    },
    "providers": {
      "main": {
        "type": "codewhisperer",
        "endpoint": "https://codewhisperer.us-east-1.amazonaws.com",
        "authentication": { "tokenPath": "~/.aws/sso/cache/token.json" }
      }
    }
  }
}
EOF

# 3. Start CCR
ccr start

# 4. Point Claude Code to CCR
export ANTHROPIC_BASE_URL="http://127.0.0.1:3456"

# Done! (5 minutes)
```

### From y-router → y-router + CCR

```bash
# 1. Install CCR
npm install -g claude-code-router

# 2. Configure CCR to use y-router as one provider
cat > ~/.claude-code-router/config.json << 'EOF'
{
  "server": { "port": 3456 },
  "routing": {
    "rules": {
      "default": { "provider": "codewhisperer", "model": "claude-4" },
      "background": { "provider": "openrouter", "model": "gemini-flash" }
    },
    "providers": {
      "codewhisperer": {
        "type": "codewhisperer",
        "endpoint": "https://codewhisperer.us-east-1.amazonaws.com",
        "authentication": { "tokenPath": "~/.aws/sso/cache/token.json" }
      },
      "openrouter": {
        "type": "openai",
        "endpoint": "https://cc.yovy.app/v1/messages",  # Via y-router!
        "authentication": { "apiKey": "your-openrouter-key" }
      }
    }
  }
}
EOF

# 3. Start CCR and point Claude Code to it
ccr start
export ANTHROPIC_BASE_URL="http://127.0.0.1:3456"

# Done! Now you have intelligent routing + OpenRouter access
```

---

## Architecture Diagrams

### y-router Alone

```
┌─────────────┐
│ Claude Code │
└──────┬──────┘
       │ Anthropic format
       ▼
┌─────────────┐
│  y-router   │ (Cloudflare Edge)
│             │
│  Translate  │ Anthropic → OpenAI format
└──────┬──────┘
       │ OpenAI format
       ▼
┌─────────────┐
│ OpenRouter  │
└─────────────┘
```

**Hops**: 2
**Latency**: ~500-550ms (500ms API + 50ms y-router)

### CCR Alone

```
┌─────────────┐
│ Claude Code │
└──────┬──────┘
       │ Anthropic format
       ▼
┌─────────────────────────────┐
│           CCR               │
│                             │
│  1. Analyze (thinking?)     │
│  2. Route by category       │
│  3. Select provider         │
└──────┬──────────────────────┘
       │
       ├─────────────┬─────────────┬─────────────┐
       │             │             │             │
       ▼             ▼             ▼             ▼
┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│   AWS    │  │ OpenAI   │  │  Gemini  │  │ Anthropic│
│CodeWhisp.│  │          │  │          │  │          │
└──────────┘  └──────────┘  └──────────┘  └──────────┘
```

**Hops**: 2
**Latency**: ~505ms (500ms API + 5ms CCR)

### CCR + y-router (Maximum Power)

```
┌─────────────┐
│ Claude Code │
└──────┬──────┘
       │ Anthropic format
       ▼
┌─────────────────────────────────┐
│             CCR                 │
│                                 │
│  Intelligent Routing:           │
│  • Thinking → CodeWhisperer     │
│  • Background → OpenRouter      │
│  • Search → OpenRouter GPT-4    │
└──────┬──────────────────────────┘
       │
       ├─────────────┬─────────────────────┐
       │             │                     │
       ▼             ▼                     ▼
┌──────────┐  ┌──────────┐        ┌──────────┐
│   AWS    │  │ y-router │        │ Others   │
│CodeWhisp.│  │          │        │          │
└──────────┘  └────┬─────┘        └──────────┘
                   │
                   ▼
              ┌──────────┐
              │OpenRouter│
              │ (100+    │
              │  models) │
              └──────────┘
```

**Hops**: 2-3 (depending on route)
**Latency**:
- Direct route: ~505ms (500ms + 5ms CCR)
- Via y-router: ~508ms (500ms + 5ms CCR + 3ms y-router)

**Still negligible!**

---

## Final Verdict

### Are They Competitors?

**NO!** They solve different problems:

- **y-router**: Format translator (Anthropic ↔ OpenAI)
- **CCR**: Intelligent router (multi-provider selection)

### Which is Better?

**It depends on your needs:**

| Your Need | Best Choice |
|-----------|-------------|
| Just want OpenRouter | y-router only |
| Just want AWS CodeWhisperer | Neither (use directly) |
| Want cost optimization | CCR only |
| Want multiple providers | CCR only |
| Want OpenRouter + routing | **CCR + y-router** |
| Simplest possible | y-router only |
| Most powerful | **CCR + y-router** |

### Is There Any Reason to Use y-router Over CCR?

**YES**, use y-router instead of CCR if:

1. ✅ You ONLY use OpenRouter (no other providers)
2. ✅ You want edge deployment (Cloudflare Workers)
3. ✅ You want zero dependencies
4. ✅ You want absolute simplest setup
5. ✅ You don't need routing logic
6. ✅ You want serverless (no server management)

**But for most production use: CCR > y-router alone**

### Is There Any Reason to Use y-router WITH CCR?

**YES!** They complement each other perfectly:

1. ✅ CCR provides intelligent routing
2. ✅ y-router provides OpenRouter access
3. ✅ Together: Maximum flexibility + cost savings
4. ✅ CCR routes simple tasks → y-router → OpenRouter (cheap!)
5. ✅ CCR routes complex tasks → AWS CodeWhisperer (quality!)

---

## Summary Recommendations

### Beginner / Simple Needs
→ **Use y-router only**
- 2 minute setup
- Access to OpenRouter
- No complexity

### Intermediate / Cost-Conscious
→ **Use CCR only**
- 5 minute setup
- 40-60% cost savings
- Multi-provider support

### Advanced / Maximum Value
→ **Use CCR + y-router together**
- 10 minute setup
- Best cost optimization
- Maximum flexibility
- All providers + all models

### Production / Enterprise
→ **Use CCR + y-router together**
- Intelligent routing (CCR)
- OpenRouter access (y-router)
- Health monitoring (CCR)
- Cost optimization (CCR)
- Maximum reliability

---

## Bottom Line

**y-router**:
- ✅ Simpler
- ✅ Smaller
- ✅ Edge deployment
- ❌ No routing
- ❌ No optimization
- **Best for**: Quick OpenRouter access

**CCR**:
- ✅ Intelligent routing
- ✅ Cost optimization
- ✅ Multi-provider
- ❌ Not serverless
- ❌ More complex
- **Best for**: Production use with optimization

**CCR + y-router**:
- ✅ Everything from both
- ✅ Maximum value
- ✅ Best cost savings
- **Best for**: Power users

**They're not competitors - they're complementary tools!**

Use y-router alone for simplicity, CCR alone for routing, or both together for maximum power.
