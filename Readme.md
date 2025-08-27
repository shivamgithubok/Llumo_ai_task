# Multi-Agent System with LangGraph and Gemini

This project implements a **multi-agent system** capable of planning, routing queries to the correct tools, and generating answers based on a knowledge base and computational tools.  
The agent is built using the **langgraph** library, with **Google's Gemini Pro** as the core reasoning model.

---

##  Features
- **Interactive Chat**: Allows users to input queries in a command-line interface.  
- **Multi-Tool Capability**: Integrates several tools, including:
  - **Retriever**: For general knowledge base lookups.  
  - **PolicyLookup**: A specialized tool for company policies and FAQs.  
  - **Calculator**: For safe mathematical computations.  
  - **StringTools**: For extracting patterns from text.  
- **Stateful and Cyclical Logic**: Built on langgraph to manage complex, multi-step conversations.  
- **Structured Logging**: Logs the entire execution flow for each query into a `logs.json` file for easy debugging and auditing.  

---

## Project Structure

```
.
├── agent.py              # Main agent logic and tool definitions [cite: 28]
├── knowledgeBase.txt     # Provided knowledge base with source documents [cite: 29]
├── logs.json             # Structured JSON logs for the 5 test queries [cite: 30]
└── README.md             # This file 
```

 ## Agent Logic and Design

This agent is built as a state graph using langgraph, ideal for cyclical and stateful agent runtimes.

🔹 State

AgentState: Tracks the history of messages in the conversation.

🔹 Nodes

agent: Calls the Gemini model with the conversation history. The model decides whether to use a tool or respond directly.
action: Executes the tool call requested by the model and returns the result.

🔹 Edges

Conditional edge (should_continue): If the model requests a tool, execution goes to the action node. Otherwise, it ends.
Loop edge: Connects action → agent so the model can continue reasoning after tool output.

This creates a robust loop:
Think -----→ Act -------→ Observe ------→ Think until the agent has enough information to answer.


## Tool Design and Retriever Choice
Retriever and PolicyLookup
Choice: Simple keyword-based retriever.
Reasoning: Lightweight, fast, effective for a small, well-defined knowledge base.
Enhancements: Searches in both title and text, returns only clean content (no metadata).

## Calculator and StringTools
Calculator: Used numexpr (safe alternative to Python’s eval()).
StringTools: Used re (regex) for reliable text pattern extraction.

## Assumptions
knowledgeBase.txt is present in the same directory as agent.py.


## USE Gemini API

## Results
- **Query** : What is LLUMO AI's core value proposition?.
     - **ANS** :  I was not able to find the core values of LLUMO AI. However, I found that LLUMO AI is an evaluation-first reliability layer for LLM apps. It focuses on automated scoring, actionable insights, and cost savings via prompt compression—helping teams ship trustworthy AI.

- **Query** : What does the LLUMO Debugger show that helps isolate failures?.
     - **ANS** : LLUMO's Debugger provides run-level logs, stepwise tool traces, and a flow graph to help you identify failure points, such as tool selection errors or context mismatch.

- **Query** : What are the official working hours and overtime rules?.
     - **ANS** : Standard working hours are 10:00 to 18:30 IST, Monday to Friday. Flexible timings are allowed with manager approval.
- **Query** : How do reimbursements work and how long do they take after approval?
     - **ANS** : Reimbursements should be submitted within 30 days, and you should keep the original receipts. The typical settlement time is 7 business days after approval.

- **Query** : Compute: (125 * 6) - 50 .
     - **ANS** : 700
- **Query** : 15% of 640.
     - **ANS** : 15% of 640 is 96.0