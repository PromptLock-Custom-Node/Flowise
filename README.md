# Flowise Custom Node – Pre-Process Prompts via PromptLock

## API Key

Get your API key from **https://promptlock.io/**. This key is required to authenticate with the PromptLock API.

## Overview

This project provides a custom Flowise node that pre-processes prompts by sending them to the PromptLock API for security analysis.

It returns:

- **Redacted Prompt** — Sanitized text with sensitive data removed
- **Risk Score** — 0–100 security rating
- **Violations** — JSON array of detected issues

Plug this into your Flowise Agentflows to enforce compliance and catch risky prompts before they reach your LLM.

## Features

- One-click import into Flowise Agentflows
- **Prompt injection detection always enabled** (runs automatically on every request)
- Optional compliance frameworks: GDPR, HIPAA, PCI DSS
- Configurable actions for high-risk prompts
- Outputs can be passed to downstream nodes
- Sub-100ms analysis time

## Installation

1. **Download the Agentflow file:**
   - Download `Flowise Custom Node to pre-process prompts Agents.json` from this repo

2. **Import into Flowise:**
   - Go to **Agentflows** in the left sidebar
   - Click **Add New** to create a new Agentflow
   - Click the **settings icon** (top right of the canvas)
   - Select **Load Agents**
   - Choose the downloaded JSON file

3. **Configure the node:**
   - Double-click the **PromptLock Custom Node** to open the config panel
   - Enter your API key and configure settings

## Input Variables

The node accepts four inputs:

### 1. `API_Key` (Required)

Your PromptLock API key (e.g., `ps_xxxxx...`).

> **Note:** Do not include "Bearer" — it's added automatically by the node.

### 2. `Prompt` (Required)

The text you want to analyze. You can type directly or reference the output of another node.

### 3. `Compliance_Frameworks` (Optional)

An array of compliance frameworks to check **in addition to prompt injection**.

**Valid options:**

- `["GDPR"]`
- `["HIPAA"]`
- `["PCI"]`
- `["GDPR", "HIPAA"]`
- `["GDPR", "HIPAA", "PCI"]`
- `[]` (empty array — only prompt injection will be analyzed)

**Important Notes:**

- ⚡ **Prompt injection detection is ALWAYS enabled** — you don't need to specify it
- If you provide an empty array or omit this field, only prompt injection will be analyzed
- You cannot disable prompt injection detection
- Specify frameworks explicitly — there is no "All" option
- Do not include "Prompt Injection" in the array — it runs automatically

### 4. `Action_on_High_Risk` (Required)

Determines what happens if high risk is detected.

**Options:**

- `score` → Only return risk analysis without modifying text (recommended for testing)
- `flag` → Mark violations but allow the prompt to continue
- `redact` → Return sanitized text with violations removed/masked
- `block` → Prevent continuation if violations found

## Output Variables

The node returns three values you can use in downstream Flowise nodes:

| Output | Description |
|--------|-------------|
| `redacted_prompt` | Sanitized version of the text (or original if action is "score" or "flag") |
| `risk_score` | Numeric risk score from 0–100 (stringified) |
| `violations` | JSON string describing all violations found (use `JSON.parse` if you need object form) |

## Example Flow

This repo includes an example Agentflow:
**`Example Flow with Custom PromptLock Node.json`**

Import this file to see a working example where:

- The **Prompt** input references the output of a previous node
- The results (`redacted_prompt`, `risk_score`, `violations`) flow to the next node
- Demonstrates how compliance-safe text and metadata flow downstream

## How It Works

1. **Every request is analyzed for prompt injection** (automatic, always-on)
2. Selected compliance frameworks (GDPR, HIPAA, PCI) are checked in addition
3. Risk score is calculated based on all detected violations
4. Action is taken based on your `Action_on_High_Risk` setting
5. Results flow to downstream nodes in your Flowise pipeline

## Supported Detections

### Always Enabled

- ⚡ **Prompt Injection** — Detects jailbreaks, system prompt manipulation, malicious instructions

### Optional Compliance Frameworks

- 🏥 **HIPAA** — Protected health information (PHI)
- 🌍 **GDPR** — Personal data under EU regulations
- 💳 **PCI DSS** — Payment card information

## Troubleshooting

**Common Issues:**

| Problem | Solution |
|---------|----------|
| "Invalid framework" error | Use exact framework names: `GDPR`, `HIPAA`, or `PCI` |
| Authentication failed | Verify your API key starts with `ps_` (don't include "Bearer") |
| Node shows "outdated" warning | Re-download the latest JSON file from this repo |
| No violations detected | Prompt injection always runs; try different compliance frameworks |

## License

MIT License. Free to use, modify, and extend.

## Support

- **Documentation:** https://promptlock.io/docs
- **Email:** support@promptlock.io
