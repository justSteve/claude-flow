# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Claude-Flow** is an enterprise-grade AI orchestration platform combining:
- **MCP Server**: 100+ tools for swarm coordination, memory, neural training, GitHub integration
- **CLI Framework**: Multi-agent swarm orchestration with persistent memory
- **ReasoningBank**: SQLite-based persistent memory with semantic search (2-3ms latency)
- **SPARC Methodology**: Specification, Pseudocode, Architecture, Refinement, Completion for TDD
- **54 Specialized Agents**: From core development to consensus protocols to GitHub automation

## Essential Commands

### Development
```bash
# Build & Test
npm run build                    # Full build: clean → version update → ESM → CJS
npm run typecheck                # TypeScript type checking
npm run lint                     # ESLint with max-warnings 0
npm test                         # All tests with --bail --maxWorkers=1 --forceExit

# Test Suites
npm run test:unit               # Unit tests only
npm run test:integration        # Integration tests
npm run test:e2e                # End-to-end tests
npm run test:coverage           # With coverage report
npm run test:ci                 # CI mode with --ci --coverage --maxWorkers=2

# Single test file
NODE_OPTIONS='--experimental-vm-modules' jest path/to/test.test.ts

# Debug tests
npm run test:debug              # With --inspect-brk
```

### SPARC Workflow
```bash
# Core SPARC modes
npx claude-flow sparc modes                    # List all modes
npx claude-flow sparc run <mode> "<task>"      # Execute specific mode
npx claude-flow sparc tdd "<feature>"          # Complete TDD workflow
npx claude-flow sparc info <mode>              # Mode details

# Parallel execution (batchtools)
npx claude-flow sparc batch <modes> "<task>"   # Parallel mode execution
npx claude-flow sparc pipeline "<task>"        # Full pipeline
```

### Memory & Swarm
```bash
# ReasoningBank memory (persistent SQLite)
npx claude-flow memory store <key> "<content>" --namespace <ns> --reasoningbank
npx claude-flow memory query "<query>" --namespace <ns> --reasoningbank
npx claude-flow memory list --namespace <ns> --reasoningbank
npx claude-flow memory status --reasoningbank

# Swarm orchestration
npx claude-flow swarm "task description" --claude
npx claude-flow swarm init --topology mesh --max-agents 5
npx claude-flow swarm spawn <agent-type> "task"
npx claude-flow swarm status

# Hive-mind (complex projects)
npx claude-flow hive-mind wizard
npx claude-flow hive-mind spawn "task" --claude
npx claude-flow hive-mind status
```

## Architecture Overview

### Core Module Structure (src/)

**CLI Layer** (`src/cli/`):
- `main.ts` - Entry point with Commander.js integration
- `commands/` - 30+ command implementations (agent, swarm, memory, sparc, hive-mind, etc.)
- `init/` - Project initialization and SPARC environment setup
- `agents/` - Base agent classes and hive-mind coordination

**MCP Server** (`src/mcp/`):
- `server.ts` - Main MCP server implementation
- `claude-flow-tools.ts` - 100+ MCP tools for orchestration
- `session-manager.ts` - Session state and lifecycle
- `protocol-manager.ts` - MCP protocol handling
- `transports/` - stdio and SSE transports

**Orchestration** (`src/swarm/`):
- Multi-agent coordination with mesh/hierarchical/adaptive topologies
- Dynamic agent spawning and task distribution
- Fault tolerance and self-healing workflows

**Memory System** (`src/memory/` + `src/reasoningbank/`):
- `reasoningbank-wrapper.ts` - SQLite-based persistent memory
- Hash-based embeddings (1024-dim) or OpenAI embeddings (1536-dim)
- Semantic search with MMR ranking
- Storage in `.swarm/memory.db`

**Neural & Learning** (`src/neural/`):
- SAFLA (Self-Aware Feedback Loop Algorithm)
- 27+ neural models for pattern recognition
- Adaptive learning from workflow outcomes

**Agents** (`src/agents/` + `.claude/agents/`):
- 54 specialized agent types dynamically loaded
- Custom agent definitions in `.claude/agents/`
- Agent capabilities and type schemas

**Coordination** (`src/coordination/`):
- Byzantine fault tolerance
- Raft consensus
- Gossip protocols
- CRDT synchronization

**GitHub Integration** (`src/integration/`):
- Repository analysis
- PR management
- Issue tracking
- Workflow automation

### Key Technologies

- **TypeScript**: ES2022 target with NodeNext modules
- **Testing**: Jest with ts-jest, experimental VM modules
- **Build**: SWC for compilation + pkg for binaries
- **Database**: better-sqlite3 for ReasoningBank
- **MCP**: @modelcontextprotocol/sdk ^1.0.4
- **Dependencies**: agentic-flow (always latest), ruv-swarm, flow-nexus

### File Organization Rules

**CRITICAL**: Never save working files to root directory!
- `/src` - Source code
- `/tests` - Test files
- `/docs` - Documentation
- `/config` - Configuration
- `/scripts` - Utility scripts
- `/examples` - Example projects
- `.claude/` - Claude Code configuration (commands, agents, hooks)
- `.swarm/` - Swarm runtime data (memory.db, sessions, checkpoints)

## Agent System

### Available Agents (54 total)

**Core Development**: coder, reviewer, tester, planner, researcher

**Swarm Coordination**: hierarchical-coordinator, mesh-coordinator, adaptive-coordinator, collective-intelligence-coordinator, swarm-memory-manager, queen-coordinator, scout-explorer, worker-specialist

**Consensus & Distributed**: byzantine-coordinator, raft-manager, gossip-coordinator, crdt-synchronizer, quorum-manager, security-manager

**Performance**: perf-analyzer, performance-benchmarker, task-orchestrator, memory-coordinator, smart-agent

**GitHub**: github-modes, pr-manager, code-review-swarm, issue-tracker, release-manager, workflow-automation, project-board-sync, repo-architect, multi-repo-swarm, swarm-issue, swarm-pr

**SPARC**: sparc-coord, sparc-coder, specification, pseudocode, architecture, refinement

**Specialized**: backend-dev, mobile-dev, ml-developer, cicd-engineer, api-docs, system-architect, code-analyzer, base-template-generator, production-validator

**Planning**: goal-planner, code-goal-planner, migration-planner, swarm-init

**Flow Nexus**: flow-nexus-auth, flow-nexus-sandbox, flow-nexus-workflow, flow-nexus-neural, flow-nexus-swarm, flow-nexus-payments, flow-nexus-challenges, flow-nexus-app-store

### Custom Agents

Load from `.claude/agents/` directory. Each agent type has:
- Capabilities (tools, permissions)
- System prompt template
- Default configuration
- Tool access permissions

## Testing Strategy

### Test Structure
- Unit tests: `src/__tests__/unit/`
- Integration tests: `src/__tests__/integration/`
- E2E tests: `src/__tests__/e2e/`
- Performance tests: `src/__tests__/performance/`

### Test Configuration (jest.config.js)
- **Environment**: Node.js with experimental VM modules
- **Preset**: ts-jest with ESM support
- **Timeout**: 30000ms default
- **Transform**: ts-jest for .ts, babel-jest for .js
- **Module mapper**: `~/` and `@/` → `src/`, `@tests/` → `tests/`
- **Transform ignore**: Allows chalk, ora, inquirer, nanoid, fs-extra, ruv-swarm

### Running Tests
```bash
# Always use NODE_OPTIONS with experimental-vm-modules
NODE_OPTIONS='--experimental-vm-modules' jest <path>

# Or use npm scripts which include the flag
npm test                    # All tests
npm run test:unit          # Fast unit tests
npm run test:integration   # Integration tests
npm run test:coverage      # With coverage
```

## Code Style & Conventions

### TypeScript Configuration
- **Target**: ES2022
- **Module**: NodeNext with NodeNext resolution
- **Strict**: All strict flags enabled
- **Output**: dist/ for compiled code
- **Source maps**: Enabled with declaration maps

### ESLint Rules
- No explicit `any` (warn)
- No unused vars except `_` prefix
- Prefer const over let
- No var keyword
- Console: Only warn/error allowed

### Best Practices
- **Modular design**: Keep files under 500 lines
- **No hardcoded secrets**: Use environment variables
- **Test-first**: Write tests before implementation
- **Clean architecture**: Separate concerns (CLI → MCP → Core → Services)
- **Error handling**: Always handle async errors
- **Type safety**: Avoid `any`, use proper types from `src/types/`

## MCP Integration

### Setting up MCP Servers
```bash
# Required: Claude Flow MCP server
claude mcp add claude-flow npx claude-flow@alpha mcp start

# Optional: Enhanced coordination
claude mcp add ruv-swarm npx ruv-swarm mcp start

# Optional: Cloud features (requires auth)
claude mcp add flow-nexus npx flow-nexus@latest mcp start
```

### MCP Tool Categories (100+ tools)
- **Coordination**: swarm_init, agent_spawn, task_orchestrate
- **Monitoring**: swarm_status, agent_list, agent_metrics, task_status
- **Memory**: memory_usage, memory_search
- **Neural**: neural_status, neural_train, neural_patterns
- **GitHub**: github_repo_analyze, github_pr_manage, github_issue_track
- **Performance**: benchmark_run, performance_report, bottleneck_analyze

## Hooks System

Hooks automate workflows at key lifecycle points. Configured in `.claude/commands/hooks/`.

### Available Hooks
**Pre-operation**: pre-task, pre-edit, pre-command, pre-search
**Post-operation**: post-task, post-edit, post-command
**Session**: session-start, session-end, session-restore
**Notification**: notification

### Hook Execution Pattern
Agents spawned via CLI automatically run hooks:
```bash
# Before work
npx claude-flow hooks pre-task --description "task"
npx claude-flow hooks session-restore --session-id "swarm-id"

# During work
npx claude-flow hooks post-edit --file "file.ts" --memory-key "swarm/agent/step"
npx claude-flow hooks notify --message "status update"

# After work
npx claude-flow hooks post-task --task-id "task"
npx claude-flow hooks session-end --export-metrics true
```

## Common Workflows

### Adding a New Feature
1. Use SPARC methodology: `npx claude-flow sparc tdd "feature description"`
2. Follow phases: Specification → Pseudocode → Architecture → Refinement → Completion
3. Write tests first, implement, refactor
4. Use swarm for parallel development: `npx claude-flow swarm "implement feature" --claude`

### Debugging Issues
1. Check logs: `.swarm/sessions/` for session data
2. Query memory: `npx claude-flow memory query "error keyword" --reasoningbank`
3. Run specific test: `NODE_OPTIONS='--experimental-vm-modules' jest path/to/test`
4. Use debug mode: `npm run test:debug`

### Adding New Agents
1. Create agent definition in `.claude/agents/<agent-type>.json`
2. Define capabilities, system prompt, tools
3. Register in `src/agents/agent-registry.ts` if needed
4. Test with: `npx claude-flow swarm spawn <agent-type> "test task"`

## Performance Metrics

- **84.8% SWE-Bench solve rate**: Industry-leading problem-solving accuracy
- **32.3% token reduction**: Efficient context management through memory
- **2.8-4.4x speed improvement**: Parallel agent coordination
- **2-3ms query latency**: ReasoningBank semantic search
- **100+ MCP tools**: Comprehensive automation toolkit

## Important Notes

### Windows Compatibility
- Recommended: Use pnpm instead of npm
- Native dependencies (better-sqlite3, node-pty) may need special handling
- See `docs/windows-installation.md` for detailed setup

### Alpha Release Caveats
- Version: v2.7.0-alpha.14
- Always use @alpha tag: `npx claude-flow@alpha`
- Some features still experimental (neural patterns, advanced consensus)
- Report issues: https://github.com/ruvnet/claude-flow/issues

### Memory Management
- ReasoningBank uses `.swarm/memory.db` (SQLite)
- Cleanup old sessions: Check `.swarm/sessions/` periodically
- Checkpoints stored: `.claude/checkpoints/`
- Memory stats: `npx claude-flow memory status --reasoningbank`

### Publishing
- Alpha releases: `npm run publish:alpha`
- Version updates automatically update bin/claude-flow-pkg.js
- Prepack: Skips build for alpha releases
- Files: Includes dist/, src/, .claude/, docs/, scripts/

## Support & Documentation

- **Main Docs**: README.md (comprehensive feature list)
- **Issues**: https://github.com/ruvnet/claude-flow/issues
- **Discord**: https://discord.com/invite/dfxmpwkG2D (Agentics Foundation)
- **Examples**: `/examples` directory with 15+ sample projects
- **Release Notes**: `/docs/RELEASE-NOTES-*.md`

---

**Remember**: Claude Flow coordinates strategy via MCP tools, Claude Code executes implementation via Task tool and file operations!
