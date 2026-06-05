# WhatsApp AI Code Updater - Conversational DevOps Agent

`WhatsApp AI Code Updater` is an automated, AI-powered conversational DevOps agent built in n8n. It allows developers, product managers, or team members to update codebase files directly by sending simple, natural language instructions over WhatsApp (e.g., *"README me instruction section add karo"*). 

The system leverages OpenAI to parse instructions, fetch files from GitHub, edit the code with context, commit the changes back to the repository, and reply back to the user on WhatsApp with the commit status and SHA hash.

---

## 🌟 Key Features

1. **AI Intent Routing (Smart Agent):**
   - Automatically differentiates between **general chat/questions** (e.g., *"Hi"*, *"How does this work?"*) and **code update requests**.
   - Responds to general chat directly over WhatsApp without touching GitHub.
   - Routes coding tasks into the automation pipeline.

2. **Automated GitHub Updates:**
   - Fetches target files dynamically.
   - Rewrites file contents accurately according to natural language instructions using OpenAI `gpt-4o-mini`.
   - Commits and pushes updates back to the specified branch.

3. **Dual WhatsApp Integration:**
   - Supported out-of-the-box for both **Meta's WhatsApp Cloud API** (standard) and **Waapi.app** (unofficial gateway).

4. **Centralized Configurations:**
   - Manage all API tokens, repository settings, and preferences in a single n8n configuration node (**Set Context**).

---

## 📐 Workflow Architecture

```mermaid
graph TD
    A[WhatsApp Webhook] --> B[Extract Message]
    B --> C[Set Context (Central Config)]
    C --> D[AI Router / Instruction Parser]
    D --> E[Parse AI Response]
    E --> F{Route Intent}
    
    F -- general_chat --> G[Set Chat Message]
    
    F -- code_update --> H[Get File from GitHub]
    H --> I[Prepare Code Prompt]
    I --> J[AI Code Generator]
    J --> K[Parse Generated Code]
    K --> L[Prepare Commit Payload]
    L --> M[Commit to GitHub]
    M --> N[Set Commit Success Message]
    
    G --> P{WhatsApp Provider Switch}
    N --> P
    
    P -- meta --> Q[Send WhatsApp Meta]
    P -- waapi --> R[Send WhatsApp Waapi]
```

---

## 🛠️ Local Setup & Why Local Webhooks Don't Execute by Default

Since n8n runs locally on your computer (`http://localhost:5678`), Meta's WhatsApp servers (on the internet) **cannot reach your local server** directly to send incoming webhooks. To make it execute, follow these steps:

### Step 1: Expose n8n using a Tunnel (ngrok)
To allow Meta to send webhook events to your local n8n, you must create a public URL for your local port:
1. Download and install [ngrok](https://ngrok.com/).
2. Run the following command in your terminal/PowerShell:
   ```bash
   ngrok http 5678
   ```
3. Copy the secure forwarding URL (e.g., `https://abcd-1234.ngrok-free.app`).

### Step 2: Configure Meta WhatsApp Webhook URL
1. In your **Facebook Developer Portal** (WhatsApp Configuration):
   - Set **Webhook URL** to: `https://YOUR_NGROK_URL/webhook/whatsapp-webhook`
   - Set **Verify Token** to the token configured in n8n.
2. Click **Verify and Save**.

### Step 3: Run the Workflow in n8n
1. Open n8n (`http://localhost:5678`) and import `n8n.json`.
2. Double-click the **Set Context** node and configure your keys (`openai_api_key`, `github_token`, `meta_access_token`).
3. Click the **"Listen for test event"** button in the first **WhatsApp Webhook** node.
4. Send a WhatsApp message to your test number (e.g., `"Hi"`).
5. Watch the execution flow live through the nodes!

---

## 🎤 Presentation & Demo Guide

Use this script to present your project to an audience or client:

### 1. The Problem Statement
> *"As developers or managers, making quick patches or updates to a codebase, README, or configuration files normally requires opening a laptop, cloning the repo, making the edit, and running git commands. This is slow and impossible when you are on the go."*

### 2. The Solution
> *"WhatsApp AI Code Updater is a conversational DevOps tool. By sending a simple WhatsApp message from your phone, an AI Agent understands the task, fetches the file from GitHub, rewrites the code, pushes it, and reports the commit hash back to you on WhatsApp."*

### 3. Step-by-Step Live Demo
- **Show General Chat:**
  - Send: *"Hi, who are you?"*
  - Show WhatsApp reply: *"Hello! I am your AI Coding Assistant. Tell me what file to update in your GitHub repo..."*
  - Explain: *"The AI dynamically routed this request as general chat without causing workflow errors or requesting files from GitHub."*
- **Show Code Commit:**
  - Send: *"Add a section about prerequisites in README.md"*
  - Show GitHub: Open your repository `XYZ` on browser and show the file updating in real-time.
  - Show WhatsApp reply: Show the success reply with checkmark and SHA hash.
