# Drishti AI

## System Design Document

**Voice-Driven Web Automation Agent for Bharat**

------------------------------------------------------------------------

# 1. Overview

Drishti AI is a voice-driven, agentic web automation system designed to
help visually impaired users in India independently navigate digital
services using natural speech.

This document describes the high-level design (HLD), component
architecture, agent execution loop, data flow, security considerations,
and scalability model.

------------------------------------------------------------------------

# 2. Design Objectives

-   Accessibility-first architecture\
-   Reliable DOM-based browser automation\
-   LLM-driven task planning\
-   Deterministic tool execution\
-   Low-latency conversational interaction\
-   AWS-native scalable deployment\
-   Secure handling of user preferences

------------------------------------------------------------------------

# 3. High-Level Architecture (HLD)

User (Voice Input)\
↓\
Amazon Transcribe (Speech-to-Text)\
↓\
Amazon Bedrock (LLM Planning Engine)\
↓\
Agent Controller (Tool Orchestrator)\
↓\
Playwright (DOM-Based Browser Automation)\
↓\
Webpage State Feedback\
↓\
Amazon Polly (Text-to-Speech)\
↓\
User (Voice Response)

**Additional Component:**

-   Memory Store (User Preferences Layer)

------------------------------------------------------------------------

# 4. System Components

## 4.1 Voice Interface Layer

### Components

-   Microphone Input\
-   Amazon Transcribe\
-   Amazon Polly

### Responsibilities

-   Capture real-time voice input\
-   Convert speech to text\
-   Convert system responses to speech\
-   Maintain conversational interaction

------------------------------------------------------------------------

## 4.2 AI Planning Layer

### Service

-   Amazon Bedrock (LLM)

### Responsibilities

-   Interpret user intent\
-   Break down tasks into structured steps\
-   Generate tool-based instructions\
-   Decide next action based on browser feedback

### Output Format (Structured JSON)

``` json
{
  "action": "click",
  "selector": "#search-button"
}
```

**Important Principle:**

The LLM does **NOT** directly control browser coordinates.\
It generates structured tool calls only.

------------------------------------------------------------------------

## 4.3 Agent Controller (Core Orchestrator)

The Agent Controller manages the reasoning-execution loop.

### Responsibilities

-   Receive structured LLM output\
-   Validate requested action\
-   Map action to Playwright function\
-   Execute action\
-   Capture updated page state\
-   Feed state back to LLM if further planning required

### Execution Pattern

**Plan → Execute → Observe → Re-plan**

------------------------------------------------------------------------

## 4.4 Tool Execution Layer

### Tool Set

-   `open_url(url)`\
-   `click(selector)`\
-   `fill(selector, value)`\
-   `scroll(direction)`\
-   `extract_data(selector)`

### Execution Engine

-   Playwright (Python)

### Design Principles

-   DOM-based selectors are primary\
-   No coordinate-based clicking\
-   Deterministic execution only

------------------------------------------------------------------------

## 4.5 Memory Layer

### Storage Type

-   JSON-based preference store (MVP)\
-   Optional graph model (future)

### Stored Entities

-   User address\
-   Passenger details\
-   Product preferences\
-   Frequently used destinations\
-   Frequently visited portals

### Memory Flow

Before filling forms:

1.  Query memory store\
2.  Auto-fill known data\
3.  Confirm with user before submission

------------------------------------------------------------------------

# 5. Detailed Agent Execution Flow

## Step 1: Voice Input

User speaks:

"Delhi se Kolkata AC 3-tier ticket book karo."

------------------------------------------------------------------------

## Step 2: Speech-to-Text

Amazon Transcribe converts audio to text.

------------------------------------------------------------------------

## Step 3: Intent Processing

Amazon Bedrock receives:

-   User query\
-   Current page state\
-   Available tool list

------------------------------------------------------------------------

## Step 4: Structured Task Plan Generation

Example Plan:

-   `open_url(IRCTC)`\
-   `fill(source_field, Delhi)`\
-   `fill(destination_field, Kolkata)`\
-   `click(search_button)`

------------------------------------------------------------------------

## Step 5: Tool Execution

Agent Controller executes actions sequentially via Playwright.

------------------------------------------------------------------------

## Step 6: Page State Feedback

Extract train list using:

-   `extract_data(train_list_selector)`

------------------------------------------------------------------------

## Step 7: Decision Loop

LLM evaluates extracted options and selects optimal choice.

------------------------------------------------------------------------

## Step 8: Conversational Feedback

Amazon Polly speaks:

"I found Rajdhani Express. Shall I proceed?"

------------------------------------------------------------------------

# 6. Hybrid Agent Design Rationale

Drishti AI uses a hybrid model:

-   **LLM → Planning & Reasoning**\
-   **DOM Automation → Deterministic Execution**

### Why Not Screenshot-Only Automation?

-   Higher latency\
-   Risk of hallucination\
-   Demo instability

### Benefits of Hybrid Approach

-   Lower latency\
-   Higher reliability\
-   Production-ready architecture\
-   Reduced hallucination risk

------------------------------------------------------------------------

# 7. Data Flow Diagram

Voice Input\
↓\
Speech-to-Text\
↓\
Intent + Page State → LLM\
↓\
Structured Tool Call\
↓\
Browser Execution\
↓\
Updated State\
↓\
LLM Decision\
↓\
Voice Response

------------------------------------------------------------------------

# 8. Error Handling Strategy

## Selector Not Found

-   Retry with alternative selector\
-   Request re-plan from LLM

## Page Load Timeout

-   Refresh page\
-   Inform user

## Unexpected Modal / Popup

-   Detect modal via DOM inspection\
-   Close using predefined selectors

## Invalid LLM Action

-   Reject malformed tool call\
-   Trigger re-planning

------------------------------------------------------------------------

# 9. Performance Design Targets

-   Speech-to-text latency: \< 1 second\
-   LLM planning latency: \< 3 seconds\
-   DOM action execution: \< 1 second per action\
-   Total conversational loop: \< 5 seconds

------------------------------------------------------------------------

# 10. Security & Privacy Design

-   No persistent raw voice storage\
-   Encrypted preference storage\
-   No automatic credential retention\
-   Confirmation before critical actions

------------------------------------------------------------------------

# 11. Scalability Design

-   Stateless Agent Controller\
-   Horizontal scaling via AWS Auto Scaling

------------------------------------------------------------------------

# 12. Deployment Architecture

Frontend → FastAPI Backend → AWS Services → Agent Controller →
Playwright → Memory Store

------------------------------------------------------------------------

# 13. Design Constraints

-   Hackathon timeline\
-   MVP-scoped flows

------------------------------------------------------------------------

# 14. Future Enhancements

-   Regional languages\
-   Graph memory\
-   Mobile integration\
-   UPI voice payments

------------------------------------------------------------------------

# 15. Design Summary

Drishti AI combines Voice + LLM + Deterministic Automation to create an
inclusive accessibility layer for Bharat.
