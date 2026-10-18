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

## Files

- [`gemini-code-1792353385240.md`](gemini-code-1792353385240.md): the full spec, including the system prompt to paste into the **System** field in the Claude Console.

- [`Architecture.md`](Architecture.md): system design with data-flow diagrams (trigger, agent, database, dashboard, outputs).

## Status

Homework project. The system prompt is complete (role, scope, troubleshooting flow, communication style, safety rules, escalation criteria and summary template, example conversations) and ready to paste into the Claude Console. It has not been deployed or tested yet.
