# HOMEWORK IT AGENT

**HelpBot AI** is an enterprise IT support agent designed for the Anthropic Claude Console / Managed Agents (Workbench). Instead of dumping generic answers, it acts like a patient, methodical IT technician and guides the user through a structured troubleshooting process.

## Scope of support

1. **Network & VPN:** connectivity problems, timeouts, authentication errors
2. **Software & OS:** application crashes, installation errors, login loops
3. **Hardware & Peripherals:** printer configuration and peripheral connection issues
4. **Access & Credentials:** basic email, account access and password troubleshooting

## How it works

The agent follows a four-step flow and asks only one or two focused questions at a time:

1. **Acknowledge & Validate:** briefly empathize with the user's frustration
2. **Clarify & Isolate:** ask targeted questions to understand the exact symptoms
3. **Propose Safe Fixes:** clear, numbered, non-destructive instructions first
4. **Escalate if Needed:** provide a structured summary for IT Tier 2 if unresolved

It matches the user's language (Hebrew or English) and uses bold for UI elements, buttons and shortcuts.

## Where to look

| What | File |
|---|---|
| **The agent itself**: exact configuration exported from the Claude Console (model, effort, tools, system prompt) | [`agent-config.yaml`](agent-config.yaml) |
| Configuration explained: model and effort choice, tool permissions, environment, task prompt, test run | [`Agent-Configuration.md`](Agent-Configuration.md) |
| System design: trigger, data-flow diagrams, database, dashboard, outputs | [`Architecture.md`](Architecture.md) |
| **Proof it runs**: Console screenshots of the test sessions (security incident, VPN, request to run bash, blocked web search) | [`screenshots/`](screenshots/) |
| Original spec and system prompt | [`gemini-code-1792353385240.md`](gemini-code-1792353385240.md) |

The agent runs on **Anthropic Managed Agents** in the author's Claude Console account, so it cannot be chatted with from GitHub. `agent-config.yaml` is the Console's Raw export of the deployed agent.

## Status

Homework project.

- **Done:** agent built and configured (`claude-sonnet-5-5`, low effort, full system prompt, only `web_search` enabled) and tested in four scenarios. Security escalation, VPN questions and refusing to run commands passed; `web_search` fails under Limited networking (see `Agent-Configuration.md`).
- **Designed, not built:** form trigger, Make scenario, Airtable database and dashboard (see `Architecture.md`).
