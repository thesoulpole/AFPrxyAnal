# Agentic-Flow Architecture Analysis

## Package Information
- **Package**: agentic-flow
- **Version**: 1.10.2
- **NPM**: https://www.npmjs.com/package/agentic-flow
- **Analysis Date**: 2025-11-15

---

## Core Multi-Model Router/Proxy Files

### 1. Router Core (`dist/router/router.js`)
**Lines**: 350 lines
**Purpose**: Main routing logic for selecting providers based on different strategies

**Key Components**:
- `ModelRouter` class - Core router implementation
- `loadConfig()` - Loads configuration from multiple sources (lines 18-36)
- `selectProvider()` - Main provider selection logic (lines 198-219)
- `selectByRules()` - Rule-based routing (lines 228-240)
- `selectByCost()` - Cost optimization routing (lines 255-267)
- `selectByPerformance()` - Performance optimization routing (lines 268-284)

**Routing Modes** (line 208):
- `manual` - Uses default provider
- `rule-based` - Matches user-defined rules
- `cost-optimized` - Selects cheapest provider
- `performance-optimized` - Selects fastest provider

**Config File Loading Order** (lines 19-26):
1. Explicit configPath parameter
2. `process.env.AGENTIC_FLOW_ROUTER_CONFIG`
3. `~/.agentic-flow/router.config.json`
4. `./router.config.json`
5. `./config/router.config.json`
6. `./router.config.example.json`
7. Fallback to environment variables

---

### 2. Provider Manager (`dist/core/provider-manager.js`)
**Lines**: 435 lines
**Purpose**: Intelligent provider failover, health monitoring, and circuit breaking

**Key Components**:
- `ProviderManager` class - Provider lifecycle management
- `selectProvider()` - Strategy-based provider selection (lines 110-153)
- `executeWithFallback()` - Automatic fallback execution (lines 236-283)
- `executeWithRetry()` - Retry logic with backoff (lines 287-313)
- `recordSuccess()` / `recordFailure()` - Metrics tracking (lines 345-397)

**Selection Strategies** (lines 116-130):
- `priority` - Uses provider priority ordering
- `cost-optimized` - Calculates cheapest provider based on token estimates
- `performance-optimized` - Uses success rate + latency score
- `round-robin` - Distributes load evenly

**Health Tracking** (lines 36-46):
```javascript
{
  isHealthy: true,
  consecutiveFailures: 0,
  averageLatency: 0,
  successRate: 1.0,
  errorRate: 0.0,
  circuitBreakerOpen: false
}
```

**Task Complexity Heuristics** (lines 132-144):
- `complex` tasks → Prefer Anthropic Claude
- `simple` tasks → Prefer Gemini (faster, cheaper)

---

### 3. Adaptive Proxy (`dist/proxy/adaptive-proxy.js`)
**Lines**: 225 lines
**Purpose**: Multi-protocol proxy supporting HTTP/1.1, HTTP/2, HTTP/3, WebSocket

**Key Components**:
- `AdaptiveProxy` class - Protocol-agnostic proxy
- Protocol fallback chain: HTTP/3 → HTTP/2 → HTTP/1.1 → WebSocket
- `start()` - Initializes all enabled protocols (lines 48-154)

**Protocol Support**:
- **HTTP/3 (QUIC)**: Port 4433 (50-70% improvement) - DISABLED by default
- **HTTP/2**: Port 3001 (30-50% improvement)
- **HTTP/1.1**: Port 3000 (baseline, always available)
- **WebSocket**: Port 8080 (mobile/unstable connections)

**Configuration Options** (lines 22-31):
```javascript
{
  enableHTTP1: true,      // Always enabled
  enableHTTP2: true,      // Default enabled
  enableHTTP3: false,     // Requires QUIC, disabled by default
  enableWebSocket: true   // Default enabled
}
```

---

### 4. Provider Implementations

#### Anthropic Provider (`dist/router/providers/anthropic.js`)
- Direct Anthropic API integration
- Model mapping support

#### OpenRouter Provider (`dist/router/providers/openrouter.js`)
- Access to 100+ models through OpenRouter
- Fallback provider support

#### Gemini Provider (`dist/router/providers/gemini.js`)
- Google Gemini API integration
- Free tier support

#### ONNX Local Provider (`dist/router/providers/onnx-local.js`)
- Local model execution (privacy mode)
- No API key required
- CPU-only execution

---

### 5. Model Mapping (`dist/router/model-mapping.js`)
**Lines**: 132 lines
**Purpose**: Translate model IDs between provider formats

**Supported Mappings**:
- Anthropic format: `claude-sonnet-4-5-20250929`
- OpenRouter format: `anthropic/claude-sonnet-4.5`
- AWS Bedrock format: `anthropic.claude-sonnet-4-5-v2:0`

**Functions**:
- `mapModelId()` - Convert between formats (lines 56-110)
- `getModelName()` - Get human-readable name (lines 114-123)
- `listModels()` - List available models for provider (lines 127-131)

---

## File Relationships

```
┌─────────────────────────────────────────────────────────────┐
│                     CLI Entry Point                          │
│              dist/cli-proxy.js (Main CLI)                    │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ├──────────────────────────────────────┐
                     │                                       │
                     ▼                                       ▼
        ┌──────────────────────┐               ┌──────────────────────┐
        │   ModelRouter        │               │   ProviderManager    │
        │  (router/router.js)  │               │ (core/provider-      │
        │                      │               │       manager.js)    │
        │ - selectProvider()   │               │ - Health monitoring  │
        │ - selectByCost()     │               │ - Circuit breaker    │
        │ - selectByPerf()     │               │ - Automatic failover │
        └──────────┬───────────┘               └──────────┬───────────┘
                   │                                       │
                   │                                       │
                   ▼                                       ▼
        ┌──────────────────────┐               ┌──────────────────────┐
        │   Provider Impls     │               │   AdaptiveProxy      │
        │                      │               │ (proxy/adaptive-     │
        │ - AnthropicProvider  │               │       proxy.js)      │
        │ - OpenRouterProvider │               │ - HTTP/1.1, 2, 3     │
        │ - GeminiProvider     │               │ - WebSocket          │
        │ - ONNXLocalProvider  │               │ - Protocol fallback  │
        └──────────────────────┘               └──────────────────────┘
                   │
                   │
                   ▼
        ┌──────────────────────┐
        │   Model Mapping      │
        │ (router/model-       │
        │       mapping.js)    │
        │ - mapModelId()       │
        │ - Format conversion  │
        └──────────────────────┘
```

---

## Configuration Flow

```
1. Load .env file (if exists)
   ↓
2. Load router.config.json (from multiple paths)
   ↓
3. Substitute environment variables in config
   ↓
4. Initialize providers based on config
   ↓
5. Start routing based on selected strategy
```

---

## Request Flow

```
User Request
    ↓
ModelRouter.chat()
    ↓
selectProvider() → Determines routing mode
    ↓
    ├─ manual → getDefaultProvider()
    ├─ rule-based → selectByRules()
    ├─ cost-optimized → selectByCost()
    └─ performance-optimized → selectByPerformance()
    ↓
Selected Provider.chat()
    ↓
handleProviderError() → Fallback chain (if error)
    ↓
Return response with metadata
```

---

## Key Observations

1. **Modular Architecture**: Clean separation between routing logic, provider management, and protocol handling

2. **Multiple Entry Points**:
   - Direct API usage via `ModelRouter`
   - CLI via `cli-proxy.js`
   - Proxy server via `AdaptiveProxy`

3. **Configuration Flexibility**:
   - JSON config files
   - Environment variables
   - Programmatic configuration
   - Config file variable substitution

4. **Provider Abstraction**:
   - Unified interface across providers
   - Easy to add new providers
   - Model ID translation layer

5. **Protocol Flexibility**:
   - Multiple HTTP versions
   - WebSocket support
   - Automatic protocol selection
