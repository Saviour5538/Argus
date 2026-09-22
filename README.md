# Argus — Autonomous Multi-Agent SRE Copilot

> 🚧 **Status: In active development.** Building module-by-module — see progress below.
> Not yet deployed. This README will be replaced with the full version (live demo,
> metrics, architecture diagram) once the build is complete.

Argus is an open-source, multi-agent AI SRE copilot that autonomously triages production
incidents, investigates root cause across logs/metrics/traces using parallel hypothesis
agents, proposes a human-approved remediation, and drafts a blameless postmortem — fully
traced, evaluated, and guardrailed.

## Why multi-agent
Investigation agents run in genuine parallel fan-out against shared state — a Deploy
Regression agent, a Resource Saturation agent, and a Dependency Failure agent each pursue
a distinct hypothesis independently, using different tools, before an Aggregator
reconciles their findings into a ranked root-cause report. A human approval gate (a real
LangGraph interrupt, not a fake pause) sits between diagnosis and any proposed
remediation. This decomposition — parallelism, heterogeneous tool-scoped roles, and a
genuine control-flow interrupt — is what makes this a multi-agent system rather than a
single prompt with extra steps.

## Tech stack
| Layer | Tool |
|---|---|
| Orchestration | [LangGraph](https://github.com/langchain-ai/langgraph) |
| LLM inference | [Groq](https://groq.com/) (open-weight models) |
| Vector DB | [Qdrant](https://qdrant.tech/) |
| Observability | [Langfuse](https://langfuse.com/) |
| Evaluation | [DeepEval](https://github.com/confident-ai/deepeval), [RAGAS](https://github.com/explodinggradients/ragas), [promptfoo](https://github.com/promptfoo/promptfoo) |
| Guardrails | [LLM Guard](https://github.com/protectai/llm-guard) |
| API | [FastAPI](https://fastapi.tiangolo.com/) |
| Demo UI | [Gradio](https://www.gradio.app/) |
| Deployment | Docker → [Hugging Face Spaces](https://huggingface.co/spaces) |

Every layer above is open source and free-tier. Full architecture and design rationale:
[`docs/build-plan.md`](docs/build-plan.md).

## Build progress
- [ ] Module 0 — Architecture overview
- [ ] Module 1 — Repo, environment & tooling setup
- [ ] Module 2 — Data & fixture layer
- [ ] Module 3 — Model provider abstraction (Groq)
- [ ] Module 4 — LangGraph state schema & graph skeleton
- [ ] Module 5 — Triage agent
- [ ] Module 6 — Parallel investigation agents
- [ ] Module 7 — RAG / knowledge agent (Qdrant)
- [ ] Module 8 — Remediation agent + human approval gate
- [ ] Module 9 — Postmortem / scribe agent
- [ ] Module 10 — Guardrails
- [ ] Module 11 — Observability (Langfuse)
- [ ] Module 12 — Evaluation harness
- [ ] Module 13 — API layer + demo UI
- [ ] Module 14 — CI/CD
- [ ] Module 15 — Deployment
- [ ] Module 16 — Documentation & portfolio packaging (replaces this README)

## Local setup
```bash
git clone https://github.com/<your-username>/argus-sre-copilot.git
cd argus-sre-copilot
uv sync  # or: pip install -e .
cp .env.example .env  # fill in GROQ_API_KEY, QDRANT_URL, QDRANT_API_KEY, LANGFUSE_* keys
```

## Scope boundaries (stated upfront, not hidden)
- No live Kubernetes cluster — agents investigate replayed, real-labeled incident data
  (sourced from [LOGPAI/loghub](https://github.com/logpai/loghub)), not a live cluster.
- No local GPU inference — LLM calls go through the Groq API; the provider layer is
  designed to swap in self-hosted vLLM/Ollama without touching agent code.
- No unattended remediation execution — every proposed fix is simulated/logged, gated by
  a genuine human-in-the-loop approval step.

## License
[MIT](LICENSE) / [Apache-2.0](LICENSE) — pick one and update this line.
