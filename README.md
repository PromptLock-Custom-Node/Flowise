# Flowise Custom Node – Pre-Process Prompts via PromptLock

## API Key
You can acquire the API key from **https://promptlock.io/**. This key is required to authenticate with the `/v1/analyze` API and must be provided in the `API_Key` field when configuring the custom node.

## Overview
This project provides a custom Flowise node that pre-processes prompts or text by sending them to the `/v1/analyze` API.

It returns:
* Redacted Prompt
* Risk Score
* Violations
* Compliance Status for selected frameworks

You can plug this into your Flowise projects to enforce compliance and handle risky prompts before they flow further in your pipeline.

## Features
* Custom node integration for Flowise
* **Prompt injection detection always enabled** (runs automatically on every request)
* Optional compliance frameworks: GDPR, HIPAA, PCI DSS
* Configurable actions for high-risk prompts
* Outputs can be passed to downstream nodes

## Installation & Setup

1. **Clone this repo**
2. **Load the provided Flowise Agent:**
   * Go to your Flowise dashboard
   * In your project, click **Load Agent**
   * Select the file: `Flowise Custom Node to pre-process prompts Agents.json`
   * Save it with any name you want and click on it.
3. **Open the custom node:**
   * Inside the agent, look for `PromptLock Custom Node`
   * Double-click it to open the config panel

## Input Variables

The node accepts four inputs:

### 1. `API_Key` (Required)
Your PromptLock service key (Bearer token format: `Bearer ps_xxxxx...`).

### 2. `Prompt` (Required)
The text you want to analyze. You can type directly or reference the output of another node.

### 3. `Compliance_Frameworks` (Optional)
An array of compliance frameworks to check **in addition to prompt injection**.

**Valid options:**
* `["GDPR"]`
* `["HIPAA"]`
* `["PCI"]`
* `["GDPR", "HIPAA"]`
* `["GDPR", "HIPAA", "PCI"]`
* `[]` (empty array - only prompt injection will be analyzed)

**Important Notes:**
* ⚡ **Prompt injection detection is ALWAYS enabled** - you don't need to specify it
* If you provide an empty array or omit this field, only prompt injection will be analyzed
* You cannot disable prompt injection detection
* Specify frameworks explicitly - there is no "All" option
* Do not include "Prompt Injection" in the array - it runs automatically

### 4. `Action_on_High_Risk` (Required)
Determines what happens if high risk is detected.

**Options:**
* `score` → Only return risk analysis without modifying text (recommended for demo/testing)
* `flag` → Mark violations but allow the prompt to continue
* `redact` → Return sanitized text with violations removed/masked
* `block` → Prevent continuation if violations found (requires full API access)

## Output Variables

The node returns three values you can use in downstream Flowise nodes:

* **`redacted_prompt`** → Sanitized version of the text (or original if action is "score" or "flag")
* **`risk_score`** → Numeric risk score from 0-100 (stringified)
* **`violations`** → JSON string describing all violations found (use `JSON.parse` if you need object form)

You can reference these outputs in your downstream Flowise nodes.

## Example

This repo includes an example agent file:
**`Example Flow with Custom PromptLock Node.json`**

Load this file in your Flowise dashboard to see a working example flow. In this example:

* The **Prompt** input of the custom node is provided by referencing the output of a previous node (instead of typing text directly)
* The results of the custom node (`redacted_prompt`, `risk_score`, `violations`) are then used in the next node
* Demonstrates how compliance-safe text and metadata flow downstream in real-world pipelines

## How It Works

1. **Every request is analyzed for prompt injection** (automatic, always-on)
2. Selected compliance frameworks (GDPR, HIPAA, PCI) are checked in addition
3. Risk score is calculated based on all detected violations
4. Action is taken based on your `Action_on_High_Risk` setting
5. Results flow to downstream nodes in your Flowise pipeline

## Supported Detections

### Always Enabled
* ⚡ **Prompt Injection** - Detects jailbreaks, system prompt manipulation, malicious instructions

### Optional Compliance Frameworks
* 🏥 **HIPAA** - Protected health information (PHI)
* 🌍 **GDPR** - Personal data under EU regulations
* 💳 **PCI DSS** - Payment card information

## Notes

* Prompt injection detection runs on **every request** regardless of configuration
* Requires a valid PromptLock API key
* You can chain multiple compliance checks by specifying multiple frameworks
* Outputs can be connected to any downstream Flowise node
* Sub-100ms analysis time for real-time applications

## Troubleshooting

**Common Issues:**

1. **"Invalid framework" error** - Make sure you're using exact framework names: `GDPR`, `HIPAA`, or `PCI`
2. **Authentication failed** - Verify your API key starts with `ps_` and is in Bearer format
3. **No violations detected** - Prompt injection always runs; if other frameworks show nothing, try selecting different frameworks

## License

MIT License. Free to use, modify, and extend.

## Support

For issues or questions:
* Documentation: https://promptlock.io/docs
* Email: support@promptlock.io
