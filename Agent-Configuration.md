# Agent Configuration: HelpBot AI

The agent is built and running on **Anthropic Managed Agents** (Claude Console). This document records its configuration **as it is deployed**, taken from the Console, and notes where it differs from the target design in [Architecture.md](Architecture.md).

## 1. Summary

| Setting | Value |
|---|---|
| Platform | Anthropic Claude Console, Managed Agents |
| Agent name | תרגול בניית סוכן |
| Description | IT agent for help desk |
| Agent ID | `agent_01M1zJ5WFzfJNXaNSi3ajMwL` |
| Version | 1 (Latest) |
| **Model** | **`claude-sonnet-5-5`** |
| **Effort level** | **Low effort** |
| Environment | `deep-researcher-env` |
| Built-in tools | `agent_toolset_20260401`, 8 tool permissions, permission mode **Auto** |
| MCP servers | None |
| Custom tools | None |
| Skills | None |
| Multiagent (Workflows, Subagents, Advisor) | All off |
| Vaults / Deployment | None |

## 2. Model and effort

- **Model:** `claude-sonnet-5-5`. A mid-size model that is fast and cheap enough for a chat-style help desk, and strong enough to follow the multi-step troubleshooting rules and answer in Hebrew.
- **Effort:** **Low**. Help-desk replies are short (one or two diagnostic questions), so deep reasoning adds cost and delay without improving the answer. If the agent starts misdiagnosing harder cases, raise it to Medium.

## 3. Tools

| Tool group | State | Notes |
|---|---|---|
| Built-in tools (`agent_toolset_20260401`) | Enabled, 8 permissions, Auto | Default toolset of Managed Agents, running inside the environment sandbox |
| MCP servers | None | Not needed yet. Airtable would be added here only if the agent itself should read or write tickets |
| Custom tools | None | |
| Skills | None | |
| Subagents / Workflows / Advisor | Off | A single-agent chat flow does not need them |

HelpBot cannot see or control the employee's computer. It only gives instructions, and the employee performs them. In the planned design, ticket storage and notifications are done by **Make**, not by the agent, so the agent does not need tool access to Airtable or email.

> **Open point:** the built-in toolset is on by default and the agent does not need most of it for a text-only help desk. Tools that are not required (for example shell, file or web tools) should be switched off under **Tool permissions** to reduce risk and cost.

## 4. System prompt

### As currently deployed in the Console (Version 1)

```text
# Role & Objective
You are "HelpBot AI", an expert IT Support and Helpdesk Assistant designed for enterprise employees. Your primary goal is not just to give instant generic answers, but to act like a professional, patient, and methodical IT technician. You guide users through a structured troubleshooting process to diagnose and resolve technical issues efficiently.

# Scope of Support
You handle common workplace IT issues, including:
1. Network & VPN connectivity problems.
2. Software crashes, installation errors, and authentication/login loops.
3. Printer and hardware peripherals configuration.
4. Basic email and account access troubleshooting.

# Operational Rules & Guidelines
1. Never dump a massive list of solutions at once.
Ask ONE or TWO focused diagnostic questions at a time.
2. Follow a Step-by-Step Troubleshooting Flow:
   - Step 1: Acknowledge & Validate. Empathize briefly with the user's frustration.
   - Step 2: Clarify & Isolate. Ask targeted questions to understand exact symptoms.
   - Step 3: Propose Safe Fixes. Give clear, numbered, non-destructive instructions first.
   - Step 4: Escalate if Needed. Provide a structured summary for IT Tier 2 if unresolved.

# Communication Style
- Tone: Professional, calm, reassuring, and clear.
- Language: Match the user's language (Hebrew if Hebrew, English if English).
- Formatting: Use bolding for UI elements, buttons, or shortcuts (e.g., Ctrl + Shift + R).
```

### Target version (not yet deployed)

The full prompt in [`gemini-code-1792353385240.md`](gemini-code-1792353385240.md) adds what the deployed version lacks:

- **Safety rules:** never ask for passwords or MFA codes, no risky actions without warning, no bypassing company security policy.
- **Escalation criteria** and the `ESCALATION SUMMARY` template. This is what the Make router in the architecture looks for, so **the integration will not work until this version is deployed.**
- Reply length limit and three example conversations.

To deploy it: Console, Managed Agents, Agents, open the agent, replace the **System prompt**, save (this creates Version 2).

## 5. Task prompt (what starts a session)

In Managed Agents the task is the **first user message** of a session. The system prompt defines how the agent behaves; the task prompt carries the specific problem.

**Today (manual test in the Console):** the message is typed directly. Example from a test session:

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

## 6. Test run

| Item | Value |
|---|---|
| Session | `sesn_01R6LXiCCtVZA5dqysFZhRNW` |
| Input | `שומע, אין אינטרנט` |
| Agent reply | Hebrew, short empathy, two diagnostic questions (connection type; error icon on the network indicator) |
| Output tokens | 292 |
| Duration | about 43 seconds |
| Cost | $0.02 |

The reply follows the deployed prompt: it validates briefly, asks exactly two questions and answers in the user's language.

## 7. Known gaps

1. Deployed prompt is the short version (no safety rules, no escalation template).
2. Built-in toolset is enabled with default permissions and not trimmed.
3. Only one manual test was run; the VPN, printer and security-incident scenarios are still to be tested.
4. Trigger, database and dashboard from [Architecture.md](Architecture.md) are not built yet.
