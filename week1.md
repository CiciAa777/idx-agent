# Week 1 Deliverable — OpenClaw Architecture Fundamentals

## 1. Overview

OpenClaw is a multi-agent orchestration runtime that connects external communication channels (e.g., WhatsApp) to an internal agent system capable of routing messages to skills, executing tools, maintaining session memory, and generating responses.

The system acts as a bridge between users and backend AI capabilities, enabling real-time conversational workflows and tool-augmented reasoning.

---

## 2. System Architecture Flow
```text
       User
                    │
                    ▼
            WhatsApp Channel
                    │
                    ▼
        OpenClaw Gateway (Runtime)
                    │
                    ▼
        Session & Orchestrator
                    │
                    ▼
             LLM / Skill Router
          ┌─────────┴─────────┐
          ▼                   ▼
      Memory             Tool Calls
          └─────────┬─────────┘
                    ▼
             Response Generation
                    │
                    ▼
             WhatsApp Channel
                    │
                    ▼
                  User
```
---

## 3. Core Components

### 3.1 Channels
Channels are external communication interfaces.

Examples:
- WhatsApp
- Email
- Web UI

Role:
- Receive user messages
- Send responses back to users
- Normalize incoming message format into internal events

---

### 3.2 Gateway
The Gateway is the central runtime server.

Responsibilities:
- Accept incoming channel events
- Maintain active connections
- Load plugins (channels, tools, skills)
- Manage routing to orchestrator
- Handle authentication and session binding

---

### 3.3 Orchestrator (Skill Router)
The orchestrator decides which skill should handle a message.

Responsibilities:
- Intent detection / routing
- Selecting appropriate skill module
- Passing structured context to skills

Example routing logic:
- "what time is it" → Time Skill
- "property prices" → Real Estate Skill
- "general chat" → Default LLM Skill

---

### 3.4 Skills
Skills are modular capability units.

Examples:
- Property search skill
- Market analytics skill
- RAG-based document retrieval skill
- General assistant skill

Each skill:
- Receives a structured message
- Executes logic or calls tools
- Returns a response payload

---

### 3.5 Tools
Tools are deterministic async functions called by skills.

Example:

```ts
export async function getCurrentTime() {
  return {
    currentTime: new Date().toISOString()
  };
}
