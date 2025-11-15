# Agentic-Flow Configuration System Analysis

## Overview

The configuration system is **chaotic** as you suspected. It uses multiple overlapping sources with unclear precedence and interdependencies.

---

## Configuration Sources (In Order of Precedence)

### 1. Explicit Configuration Parameters
Direct parameters passed to constructors (highest priority)

### 2. Environment Variables
Loaded from `.env` files or system environment

### 3. Configuration Files
JSON files loaded from multiple locations

### 4. Default Values
Hardcoded fallbacks in the code

---

## Complete Environment Variable Reference

### Router Configuration

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `AGENTIC_FLOW_ROUTER_CONFIG` | string | - | Path to router config JSON | router.js:21 |
| `PROVIDER` | string | `'anthropic'` | Default provider name | router.js:42 |
| `ROUTER_VERBOSE` | boolean | `false` | Enable verbose logging | router.js:94 |

### Provider API Keys

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `ANTHROPIC_API_KEY` | string | - | Anthropic API key | router.js:47 |
| `ANTHROPIC_BASE_URL` | string | - | Anthropic API base URL | router.js:50 |
| `OPENROUTER_API_KEY` | string | - | OpenRouter API key | router.js:54 |
| `OPENROUTER_BASE_URL` | string | - | OpenRouter base URL | router.js:57 |
| `GOOGLE_GEMINI_API_KEY` | string | - | Gemini API key | router.js:61 |
| `GEMINI_BASE_URL` | string | - | Gemini base URL | adaptive-proxy.js:209 |

### Local Model Configuration

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `ONNX_MODEL_PATH` | string | `'./models/phi-4/...'` | Path to ONNX model | router.js:68 |

### Proxy Configuration

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `TLS_CERT` | string | - | TLS certificate path | adaptive-proxy.js:206 |
| `TLS_KEY` | string | - | TLS private key path | adaptive-proxy.js:207 |
| `ANTHROPIC_PROXY_BASE_URL` | string | - | Proxy base URL override | (various) |

### Debug/Monitoring

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `DEBUG` | boolean | `false` | Enable debug mode | (various) |
| `DEBUG_LEVEL` | string | `'info'` | Log level | (various) |
| `DEBUG_ENABLED` | boolean | `false` | Enable debugging | (various) |
| `DEBUG_COLORIZE` | boolean | `true` | Colorize debug output | (various) |
| `DEBUG_FORMAT` | string | `'text'` | Debug format | (various) |
| `DEBUG_OUTPUT` | string | `'console'` | Where to output debug | (various) |
| `DEBUG_OUTPUT_FILE` | string | - | Debug log file path | (various) |
| `DEBUG_STREAMING` | boolean | `false` | Debug streaming | (various) |
| `DEBUG_TIMELINE` | boolean | `false` | Track timeline | (various) |
| `DEBUG_TRACK_COMMUNICATION` | boolean | `false` | Track communication | (various) |
| `DEBUG_TRACK_DECISIONS` | boolean | `false` | Track decisions | (various) |
| `DEBUG_TRACK_STATE` | boolean | `false` | Track state changes | (various) |

### Federation System

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `FEDERATION_HUB_PORT` | number | `8765` | Federation hub port | (various) |
| `FEDERATION_HUB_ENDPOINT` | string | - | Hub endpoint URL | (various) |
| `FEDERATION_DB_PATH` | string | - | Federation database path | (various) |
| `FEDERATION_MAX_AGENTS` | number | `10` | Max concurrent agents | (various) |
| `FEDERATION_HEARTBEAT_INTERVAL` | number | `30000` | Heartbeat interval (ms) | (various) |
| `FEDERATION_BROADCAST_LATENCY` | number | `100` | Broadcast latency (ms) | (various) |
| `FEDERATION_MEMORY_SYNC` | boolean | `true` | Enable memory sync | (various) |

### Agent System

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `AGENT` | string | - | Agent type to use | (various) |
| `AGENT_LIFETIME` | number | `3600000` | Agent lifetime (ms) | (various) |
| `AGENTIC_FLOW_AGENT_BOOSTER` | boolean | `false` | Enable agent booster | (various) |
| `AGENTIC_FLOW_BOOSTER_THRESHOLD` | number | `10` | Booster threshold | (various) |

### Database Paths

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `AGENTDB_PATH` | string | - | AgentDB database path | (various) |
| `CLAUDE_FLOW_DB_PATH` | string | - | Claude Flow DB path | (various) |

### Model Configuration

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `DEFAULT_MODEL` | string | - | Default model to use | (various) |
| `COMPLETION_MODEL` | string | - | Completion model | (various) |

### MCP Servers

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `ENABLE_CLAUDE_FLOW_MCP` | boolean | `false` | Enable Claude Flow MCP | (various) |
| `ENABLE_CLAUDE_FLOW_SDK` | boolean | `false` | Enable SDK | (various) |
| `ENABLE_FLOW_NEXUS_MCP` | boolean | `false` | Enable Flow Nexus MCP | (various) |
| `ENABLE_AGENTIC_PAYMENTS_MCP` | boolean | `false` | Enable payments MCP | (various) |

### Other Flags

| Variable | Type | Default | Purpose | File |
|----------|------|---------|---------|------|
| `ENABLE_STREAMING` | boolean | `false` | Enable streaming | (various) |
| `AGENTIC_FLOW_ENABLE_QUIC` | boolean | `false` | Enable QUIC transport | (various) |
| `DIFF` | boolean | `false` | Show diffs | (various) |
| `DATASET` | string | - | Dataset to use | (various) |

**Total Unique Environment Variables**: 50+

---

## Configuration File Format

### Router Config JSON (`router.config.json`)

**Location Search Order**:
1. Explicit path parameter
2. `$AGENTIC_FLOW_ROUTER_CONFIG`
3. `~/.agentic-flow/router.config.json`
4. `./router.config.json`
5. `./config/router.config.json`
6. `./router.config.example.json`

**Format**:
```json
{
  "version": "1.0",
  "defaultProvider": "anthropic",

  "routing": {
    "mode": "cost-optimized",
    "rules": [
      {
        "condition": {
          "agentType": ["coder", "reviewer"],
          "requiresTools": true
        },
        "action": {
          "provider": "anthropic"
        },
        "reason": "Code tasks benefit from Claude"
      }
    ]
  },

  "providers": {
    "anthropic": {
      "apiKey": "${ANTHROPIC_API_KEY}",
      "baseUrl": "${ANTHROPIC_BASE_URL:-https://api.anthropic.com}",
      "priority": 1,
      "costPerToken": 0.003,
      "maxRetries": 3,
      "enabled": true
    },
    "openrouter": {
      "apiKey": "${OPENROUTER_API_KEY}",
      "baseUrl": "${OPENROUTER_BASE_URL:-https://openrouter.ai/api/v1}",
      "priority": 2,
      "costPerToken": 0.001,
      "enabled": true
    },
    "gemini": {
      "apiKey": "${GOOGLE_GEMINI_API_KEY}",
      "priority": 3,
      "costPerToken": 0.0001,
      "enabled": true
    },
    "onnx": {
      "modelPath": "${ONNX_MODEL_PATH:-./models/phi-4/model.onnx}",
      "executionProviders": ["cpu"],
      "maxTokens": 100,
      "temperature": 0.7,
      "priority": 99,
      "enabled": true
    }
  },

  "fallbackChain": ["anthropic", "openrouter", "gemini", "onnx"]
}
```

---

## Environment Variable Substitution

**Pattern**: `${VAR_NAME}` or `${VAR_NAME:-default}`

**Implementation** (router.js:73-91):
```javascript
substituteEnvVars(obj) {
    if (typeof obj === 'string') {
        // Replace ${VAR_NAME} with environment variable value
        return obj.replace(/\$\{([^}]+)\}/g, (_, key) => {
            const [varName, defaultValue] = key.split(':-');
            return process.env[varName] || defaultValue || '';
        });
    }
    // ... handles objects and arrays recursively
}
```

**Examples**:
- `"${ANTHROPIC_API_KEY}"` → Value of `ANTHROPIC_API_KEY`
- `"${API_URL:-https://default.com}"` → `API_URL` or default
- `"${MISSING_VAR}"` → Empty string if not set

---

## .env File Loading

### Loading Strategy (cli-proxy.js:11-25)

```javascript
function loadEnvRecursive(startPath = process.cwd()) {
    let currentPath = startPath;
    const root = pathResolve('/');

    while (currentPath !== root) {
        const envPath = pathResolve(currentPath, '.env');
        if (existsSync(envPath)) {
            dotenv.config({ path: envPath });
            return true;
        }
        currentPath = pathResolve(currentPath, '..');
    }

    // Fallback to default behavior
    dotenv.config();
    return false;
}
```

**Search Order**:
1. Current directory `.env`
2. Parent directory `.env`
3. Continue up to filesystem root
4. Default dotenv behavior (stops at first match)

---

## Configuration Precedence (Detailed)

### Example Scenario: Setting Anthropic API Key

**All possible ways** (from highest to lowest priority):

1. **Direct parameter**:
```javascript
const router = new ModelRouter();
router.providers.set('anthropic', new AnthropicProvider({
  apiKey: 'sk-ant-direct-key'  // HIGHEST PRIORITY
}));
```

2. **Config file with literal value**:
```json
{
  "providers": {
    "anthropic": {
      "apiKey": "sk-ant-config-literal"
    }
  }
}
```

3. **Config file with env variable**:
```json
{
  "providers": {
    "anthropic": {
      "apiKey": "${ANTHROPIC_API_KEY}"
    }
  }
}
```

4. **Environment variable** (if no config file):
```bash
export ANTHROPIC_API_KEY="sk-ant-env-direct"
```

5. **.env file**:
```bash
# .env
ANTHROPIC_API_KEY=sk-ant-dotenv
```

6. **Default value in config**:
```json
{
  "providers": {
    "anthropic": {
      "apiKey": "${ANTHROPIC_API_KEY:-sk-ant-default}"
    }
  }
}
```

---

## Configuration Conflicts and Issues

### ⚠️ Issue 1: Multiple Config File Paths

**Problem**: The system checks 6 different paths for config files:
- `$AGENTIC_FLOW_ROUTER_CONFIG`
- `~/.agentic-flow/router.config.json`
- `./router.config.json`
- `./config/router.config.json`
- `./router.config.example.json`

**What Happens**: First file found wins, but unclear which one is being used

**Fix Needed**:
- Add logging to show which file was loaded
- Use `ROUTER_VERBOSE=true` to see provider initialization

---

### ⚠️ Issue 2: Env Variable Substitution Failures

**Problem**: If env variable is missing, it becomes empty string:
```json
{
  "apiKey": "${MISSING_API_KEY}"  // Becomes "" not undefined
}
```

**Impact**: Provider initialization fails silently

**Fix Needed**:
- Add validation for required fields
- Throw error if critical API keys are empty

---

### ⚠️ Issue 3: Provider Initialization Order

**Problem**: Providers are initialized in code order (lines 93-154):
1. Anthropic
2. OpenRouter
3. ONNX
4. Gemini

**Impact**:
- Initialization errors are logged but not fatal
- Silent failures if provider config is wrong
- Hard to debug which provider failed

**Fix Needed**:
- Better error reporting
- Validation before initialization

---

### ⚠️ Issue 4: Priority vs. Cost vs. Performance

**Problem**: Three different selection mechanisms:
- `priority` field in provider config (ProviderManager)
- `costPerToken` field (ProviderManager.selectByCost)
- `avgLatency` from metrics (Router.selectByPerformance)

**Confusion**:
- Priority is used in `priority` mode
- Cost is used in `cost-optimized` mode
- Performance is used in `performance-optimized` mode
- **They don't interact or combine**

**Fix Needed**:
- Document the modes clearly
- Make it explicit that only one mode is active

---

### ⚠️ Issue 5: Routing Mode Selection

**Problem**: Routing mode can be set in multiple places:
- Config file: `{ "routing": { "mode": "cost-optimized" } }`
- But also defaults to `"manual"` if not specified

**Confusion**:
- `manual` mode ignores all optimization
- `cost-optimized` mode uses broken TODO implementation
- Must explicitly set mode to get any optimization

**Fix Needed**:
- Default to `performance-optimized` (the one that works)
- Warn if using `cost-optimized` (it's broken)

---

## Setting Interactions

### How Settings Affect Each Other

#### 1. Routing Mode → Provider Selection

```
routing.mode = "manual"
  → Uses defaultProvider only
  → Ignores: priority, costPerToken, metrics

routing.mode = "cost-optimized"
  → Uses hardcoded list (BROKEN)
  → Ignores: priority, metrics
  → Should use: costPerToken (but doesn't)

routing.mode = "performance-optimized"
  → Uses avgLatency from metrics
  → Ignores: priority, costPerToken

routing.mode = "rule-based"
  → Uses routing.rules
  → Falls back to defaultProvider if no match
```

#### 2. Provider Config → Initialization

```
providers.{name}.enabled = false
  → Provider not initialized
  → Not available for routing
  → Falls back to next provider

providers.{name}.apiKey = "" (empty)
  → Initialization fails silently
  → Provider skipped
  → Logged if ROUTER_VERBOSE=true
```

#### 3. Provider Priority → Selection

```
Only used in "priority" strategy mode (ProviderManager)
Lower number = higher priority

Example:
  anthropic.priority = 1  (highest)
  openrouter.priority = 2
  gemini.priority = 3     (lowest)

If priority mode:
  → Returns first healthy provider by priority
If cost/perf mode:
  → Priority ignored completely
```

#### 4. Fallback Chain → Error Recovery

```
fallbackChain = ["anthropic", "openrouter", "gemini"]

If primary provider fails:
  1. Try next in chain
  2. Continue until success or all fail
  3. Each provider can have maxRetries

Independent of routing mode!
```

#### 5. Health Monitoring → Circuit Breaker

```
strategy.maxFailures = 3 (ProviderManager)

If provider fails 3 times:
  → circuitBreakerOpen = true
  → Provider excluded from selection
  → Recovers after strategy.recoveryTime (default 60s)

Affects ALL routing modes
```

---

## Recommended Configuration

### Minimal Working Config

```bash
# .env
ANTHROPIC_API_KEY=sk-ant-xxx
OPENROUTER_API_KEY=sk-or-xxx
GOOGLE_GEMINI_API_KEY=xxx
ROUTER_VERBOSE=true
```

### Production Config

```json
{
  "version": "1.0",
  "defaultProvider": "anthropic",

  "routing": {
    "mode": "performance-optimized",
    "rules": []
  },

  "providers": {
    "anthropic": {
      "apiKey": "${ANTHROPIC_API_KEY}",
      "priority": 1,
      "costPerToken": 0.003,
      "maxRetries": 3,
      "enabled": true,
      "healthCheckInterval": 60000
    },
    "openrouter": {
      "apiKey": "${OPENROUTER_API_KEY}",
      "priority": 2,
      "costPerToken": 0.001,
      "enabled": true
    },
    "gemini": {
      "apiKey": "${GOOGLE_GEMINI_API_KEY}",
      "priority": 3,
      "costPerToken": 0.0001,
      "enabled": true
    }
  },

  "fallbackChain": ["anthropic", "openrouter", "gemini"],

  "strategy": {
    "type": "performance-optimized",
    "maxFailures": 3,
    "recoveryTime": 60000,
    "retryBackoff": "exponential"
  }
}
```

---

## Configuration Best Practices

### ✅ DO:

1. **Use ROUTER_VERBOSE=true** during setup
2. **Set routing.mode to "performance-optimized"** (the only one that works)
3. **Configure fallbackChain** for reliability
4. **Set provider.priority** for fallback order
5. **Use environment variables** for API keys
6. **Put config in ~/.agentic-flow/** for global use

### ❌ DON'T:

1. **Don't use "cost-optimized" mode** (it's broken)
2. **Don't rely on complexity analysis** (doesn't exist)
3. **Don't leave API keys in config files** (use env vars)
4. **Don't enable all providers** if you don't have API keys
5. **Don't expect budget limits** (not implemented)

---

## Summary

**Configuration Chaos Level**: 🔴 HIGH

**Main Issues**:
1. Too many overlapping configuration sources
2. Silent failures on missing env variables
3. Unclear precedence rules
4. Broken cost-optimized mode
5. Missing features claimed in docs

**What Works**:
1. Environment variable substitution
2. Multiple provider support
3. Fallback chains
4. Health monitoring
5. Performance-based routing

**Recommendation**:
Use performance-optimized mode with explicit provider configuration. Don't trust the docs - test everything yourself.
