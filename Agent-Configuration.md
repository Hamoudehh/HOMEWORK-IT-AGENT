# Agent Configuration: HelpBot AI

The agent is built and running on **Anthropic Managed Agents** (Claude Console). This document records its configuration as deployed. The exact configuration exported from the Console (Raw view) is in [`agent-config.yaml`](agent-config.yaml). The design around it is in [Architecture.md](Architecture.md).

## 1. Summary

| Setting | Value |
|---|---|
| Platform | Anthropic Claude Console, Managed Agents |
| Agent name | תרגול בניית סוכן |
| Description | IT agent for help desk |
| Agent ID | `agent_01M1zJ5WFzfJNXaNSi3ajMwL` |
| **Model** | **`claude-sonnet-5-5`** (speed: standard) |
| **Effort level** | **Low** |
| System prompt | Full version (role, scope, rules, style, safety, escalation, examples) |
| Built-in tools | `agent_toolset_20260401`: only `web_search` enabled, seven tools disabled |
| MCP servers | None |
| Custom tools | None |
| Skills | None |
| Multiagent (Workflows, Subagents, Advisor) | Off |
| Environment | `deep-researcher-env` |

## 2. Model and effort

- **Model:** `claude-sonnet-5-5`. A mid-size model that is fast and cheap enough for a chat-style help desk, and strong enough to follow the multi-step troubleshooting rules and answer in Hebrew.
- **Effort:** **Low**. Help-desk replies are short (one or two diagnostic questions), so deep reasoning adds cost and delay without improving the answer. If the agent starts misdiagnosing harder cases, raise it to Medium.

## 3. Tools

The agent uses the built-in toolset `agent_toolset_20260401` with default config `enabled: true`, permission policy `auto`, and seven tools explicitly disabled:

| Tool | Description | State |
|---|---|---|
| `bash` | Execute bash commands | **Disabled** |
| `read` | Read files | **Disabled** |
| `write` | Write files | **Disabled** |
| `edit` | String replacement in files | **Disabled** |
| `glob` | File pattern matching | **Disabled** |
| `grep` | Text search with regex | **Disabled** |
| `web_fetch` | Fetch URL content | **Disabled** |
| `web_search` | Search the web | Enabled (Auto), by the default config |

**Why:** a help desk is a text conversation. The agent's input comes from employees and is untrusted, so tools that run commands, touch files or fetch arbitrary URLs are switched off. `web_search` stays on because it is useful for looking up an error code or a vendor article.

No MCP servers, custom tools or skills are attached. HelpBot cannot see or control the employee's computer: it gives instructions and the employee performs them. In the planned design, ticket storage and notifications are done by **Make**, not by the agent, so the agent does not need access to Airtable or email.

## 4. Environment

The environment is the container the agent's sessions run in.

| Setting | Value |
|---|---|
| Name | `deep-researcher-env` |
| ID | `env_012Grw4P9176Zhtt4uAKta3X` |
| Hosting | Cloud (cannot be changed after creation), scope: Organization |
| Networking | Unrestricted when last checked. Target: **Limited** |
| Extra packages | None (standard image only) |

A help-desk agent has no reason to reach arbitrary websites, so networking should be **Limited**. The environment name suggests a research use; a dedicated `helpbot-env` would be cleaner.

## 5. System prompt

The deployed system prompt is the full version from [`gemini-code-1792353385240.md`](gemini-code-1792353385240.md). It is stored verbatim in the `system:` field of [`agent-config.yaml`](agent-config.yaml). Sections:

| Section | Purpose |
|---|---|
| Role & Objective | Patient, methodical IT technician, not a generic answer machine |
| Scope of Support | Network/VPN, software and login, printers and peripherals, email and account access |
| Operational Rules | One or two questions at a time; four-step flow: acknowledge, clarify, safe fixes, escalate |
| Communication Style | Calm tone, matches the user's language (Hebrew or English), short replies under about 120 words |
| Safety & Security Rules | Never ask for passwords or MFA codes; no risky actions without warning; no bypassing company security policy; stop and escalate security incidents; never claim to have performed an action |
| When to Escalate | Unsolved after 2-3 rounds, outage, admin rights needed, data loss or security incident, user asks for a person |
| Escalation template | `ESCALATION SUMMARY` block (category, priority, impact, device, problem, error, timeline, steps tried, notes) |
| Example Interactions | VPN (English), printer (Hebrew), phishing incident (English) |

The `ESCALATION SUMMARY` header is what the Make router in the architecture searches for to detect an escalation.

## 6. Task prompt (what starts a session)

In Managed Agents the task is the **first user message** of a session. The system prompt defines how the agent behaves; the task prompt carries the specific problem.

**Manual test in the Console:** the message is typed directly, for example:

```text
שומע, אין אינטרנט
```

**Planned (via Make):** Make builds the first message from the support-form fields:

```text
Employee: {employee_name}
Reported at: {timestamp}
Problem: {message}
```

Follow-up messages in the same session contain only the employee's new text.

## 7. Test run

First test session, run **before** the full prompt and the tool restrictions were applied:

| Item | Value |
|---|---|
| Session | `sesn_01R6LXiCCtVZA5dqysFZhRNW` |
| Input | `שומע, אין אינטרנט` |
| Agent reply | Hebrew, short empathy, two diagnostic questions (connection type; error icon on the network indicator) |
| Output tokens | 292 |
| Duration | about 43 seconds |
| Cost | $0.02 |

Tests of the final configuration (security incident, VPN, request to run bash) are listed under known gaps until they are run.

## 8. Known gaps

1. The final configuration has not been tested yet: security-incident escalation, VPN questions, and a request to run a command.
2. Environment networking is still to be confirmed as **Limited**.
3. Trigger (form and Make), database (Airtable) and dashboard from [Architecture.md](Architecture.md) are not built yet.
