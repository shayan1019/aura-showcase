# Architecture and safety boundary

AURA separates three concerns: a CLI/TUI presentation layer, an agent orchestrator that routes between Ask/Plan/Build modes, and a local model runtime that the orchestrator talks to over HTTP.

Tool execution (file, shell, web) is never invoked directly by the orchestrator — every call passes through a permission gate first, so the agent can be run in progressively more autonomous modes without losing an explicit consent boundary. Session and memory state persist locally between runs so multi-turn work survives a restart.

The model runtime is a separate long-lived process (a local llama.cpp server) rather than an in-process model load — the CLI starts it once, keeps it warm, and streams responses back through an OpenAI-compatible local REST contract. This keeps repeated invocations fast and lets the daemon be stopped/restarted independently of the CLI session.

The public case study intentionally does not document the system prompts, tool-execution internals, permission-policy rule definitions, or session/memory storage format. Those are private implementation details.
