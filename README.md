# Flowise Custom Node – Pre-Process Prompts via `/v1/analyze`

This project provides a custom Flowise node that pre-processes prompts or text by sending them to the `/v1/analyze` API.  

It returns:
- Redacted Prompt  
- Risk Score  
- Violations  
- Compliance Frameworks (e.g., Prompt Injection, GDPR, HIPAA, PCI)  

You can plug this into your Flowise projects to enforce compliance and handle risky prompts before they flow further in your pipeline.

---

## Features

- Custom node integration for Flowise  
- Enforces compliance frameworks (GDPR, HIPAA, PCI, Prompt Injection, etc.)  
- Configurable actions for high-risk prompts  
- Outputs can be passed to downstream nodes  

---

## Installation & Setup

1. Clone this repo  

2. Load the provided Flowise Agent:  
   - Go to your Flowise dashboard  
   - In your project, click **Load Agent**  
   - Select the file:  
     `Flowise Custom Node to pre-process prompts Agents.json`  
   - Save it with any name you want and click on it. 

3. Open the custom node:  
   - Inside the agent, look for  
     `PromptLock Custom Node`  
   - Double-click it to open the config panel  

---

## Input Variables

The node accepts four inputs:

- **API_Key**  
  Your PromptShield service key (Bearer token).

- **Prompt**  
  The text you want to analyze. You can type directly or reference the output of another node.

- **Compliance_Frameworks**  
  Examples:  
  `["GDPR"]`  
  `["PCI"]`  
  `["HIPAA"]`  
  `["PCI","GDPR"]`  
  `["PCI","GDPR","HIPAA"]`  
  `["Prompt Injection"]`  
  `["All"]`  

  If you leave this empty then only Prompt Injection will be analyzed. If you provide any other framework from above, then that along with Prompt Injection will be analyzed.

- **Action_on_High_Risk**  
  Determines what happens if high risk is detected. Options:  
  - `flag` → mark but allow  
  - `redact` → return redacted text  
  - `block` → prevent continuation  
  - `score` → only return a numeric score  

---

## Output Variables

The node returns three values you can use in downstream Flowise nodes:

- `redacted_prompt` → sanitized version of the text  
- `risk_score` → numeric score (stringified)  
- `violations` → JSON string describing violations (use `JSON.parse` if you need object form)  

You can reference this output in your downstream Flowise nodes as well.  

---

## Example

This repo also includes an example agent file:  

`Example Flow with Custom PromptLock Node.json`  

Load this file in your Flowise dashboard to see a working example flow. In this example:

- The **Prompt** input of the custom node is provided by referencing the output of a previous node (instead of typing text directly).  
- The results of the custom node (`redacted_prompt`, `risk_score`, `violations`) are then used in the next node to demonstrate how compliance-safe text and metadata flow downstream.  

This makes it easier to understand how to connect the node in real-world pipelines.

---

## Notes

- Works with Prompt Injection, GDPR, HIPAA, PCI.  
- Requires a valid PromptShield API key.  
- You can chain multiple compliance checks by feeding outputs into downstream nodes.  

---

## License

MIT License. Free to use, modify, and extend.  
