# agents

The first curated collection of agent definitions for the [agentsHQ](https://agentshq.sh) ecosystem.

26 production-grade agents covering 13 tech stacks — each with a builder that writes code and a reviewer that audits it.

## Install

```bash
# Install all agents
npx agentshq add ulpi-io/agents

# Install a specific agent
npx agentshq add ulpi-io/agents --agent nextjs-senior-engineer
```

## Agents

### Builders

Expert agents that write production code with deep stack-specific knowledge.

| Agent | Description |
|---|---|
| `python-senior-engineer` | Python 3.12+, uv, ruff, pytest, Pydantic v2, async patterns |
| `python-fastapi-senior-engineer` | FastAPI, dependency injection, async DB, JWT auth |
| `express-senior-engineer` | Express.js, TypeScript, middleware, Bull queues, Pino |
| `nextjs-senior-engineer` | Next.js 14/15, App Router, RSC, Server Actions, caching |
| `react-vite-tailwind-engineer` | React 19, Vite, Tailwind CSS 3, TypeScript strict |
| `laravel-senior-engineer` | Laravel 12.x, Eloquent, Horizon, queues, caching |
| `expo-react-native-engineer` | Expo SDK 52+, New Architecture, EAS Build/Update |
| `nodejs-cli-senior-engineer` | Commander, Pino, inquirer, ora, cross-platform CLI |
| `go-senior-engineer` | Go 1.22+, Chi, gRPC, sqlx, concurrency patterns |
| `go-cli-senior-engineer` | Cobra, Bubble Tea, Viper, GoReleaser |
| `devops-aws-senior-engineer` | AWS CDK, CloudFormation, Terraform, CI/CD |
| `devops-docker-senior-engineer` | Docker, Compose, multi-stage builds, orchestration |
| `ios-macos-senior-engineer` | Swift 6.2, SwiftUI, SwiftData, Liquid Glass |

### Reviewers

Each builder has a matching reviewer that audits code against a 10-category checklist with severity-tagged findings.

| Agent | Reviews |
|---|---|
| `python-senior-engineer-reviewer` | Type safety, tooling, Pydantic, testing, async, security |
| `python-fastapi-senior-engineer-reviewer` | DI, Pydantic models, DB patterns, auth, middleware |
| `express-senior-engineer-reviewer` | Middleware, error handling, security, validation, queues |
| `nextjs-senior-engineer-reviewer` | RSC boundaries, data fetching, SEO, caching, file conventions |
| `react-vite-tailwind-engineer-reviewer` | Components, hooks, accessibility, Vite config, Tailwind |
| `laravel-senior-engineer-reviewer` | Architecture, Eloquent, validation, queues, caching |
| `expo-react-native-engineer-reviewer` | Navigation, hooks, native APIs, EAS, accessibility |
| `nodejs-cli-senior-engineer-reviewer` | Commands, exit codes, validation, cross-platform, packaging |
| `go-senior-engineer-reviewer` | Error handling, concurrency, HTTP, DB, API design |
| `go-cli-senior-engineer-reviewer` | Commands, TUI, config, cross-platform, distribution |
| `devops-aws-senior-engineer-reviewer` | IAM, IaC, networking, encryption, cost, HA, serverless |
| `devops-docker-senior-engineer-reviewer` | Dockerfiles, security, multi-stage, Compose, health checks |
| `ios-macos-senior-engineer-reviewer` | Concurrency, SwiftUI, SwiftData, accessibility, testing |

## Structure

```
agents/
├── {stack}-senior-engineer.md           # Builder agent definition
├── {stack}-senior-engineer-reviewer.md  # Reviewer agent definition
└── ...
```

Each agent file contains YAML frontmatter (`name`, `version`, `description`, `tools`, `model`) followed by the full agent definition including personality, rules, preferences, and deep domain knowledge.

## Supported IDEs

These agents work with any AI coding IDE that supports agent definitions, including Claude Code, Cursor, GitHub Copilot, Windsurf, Codex, and [40+ others](https://agentshq.sh/docs).

## License

MIT
