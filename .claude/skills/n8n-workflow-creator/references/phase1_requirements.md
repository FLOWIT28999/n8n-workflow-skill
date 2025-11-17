# Phase 1: Requirements Understanding & Clarification

## Purpose

Transform ambiguous natural language requests into structured, actionable requirements that can guide the entire workflow creation process.

## Overview

This phase focuses on collecting user input, understanding their intent, and creating a comprehensive requirements document. The goal is to eliminate ambiguity and ensure all necessary information is available before proceeding to design.

## Process Flow

```
User Request (Natural Language)
    ↓
Memory MCP (Check User History)
    ↓
Sequential Thinking MCP (Generate Clarification Questions)
    ↓
Structured Requirements Document
```

## Step-by-Step Guide

### Step 1: Collect Natural Language Request

**Action**: Receive and analyze the user's initial request

**What to capture**:
- Core intent: What does the user want to accomplish?
- Mentioned services: What tools/platforms are involved?
- Desired outcome: What should the end result be?
- Any specific constraints or requirements

**Example Input**:
- "Create a workflow that sends Slack notifications when new rows are added to Google Sheets"
- "Build an AI agent that answers customer questions based on our documentation"
- "Automate the process of backing up database data to cloud storage"

---

### Step 2: Check User History with Memory MCP

**Action**: Use Memory MCP to retrieve relevant user context

**Memory MCP Operations**:

```
Use Memory MCP to search for:
- Previous workflows created by this user
- Preferred services and integrations
- Common authentication methods used
- Technical skill level (beginner/intermediate/expert)
- Previous successes and failures
- Naming conventions and organizational patterns
```

**What to look for**:

1. **Service Preferences**: Which services does the user frequently use?
2. **Authentication Patterns**: OAuth, API keys, or other methods?
3. **Error Handling Style**: Verbose logging or silent failures?
4. **Workflow Complexity**: Simple linear or complex branching?
5. **Past Issues**: Any recurring problems or patterns to avoid?

**Store retrieved context** for use throughout the creation process.

---

### Step 3: Use Sequential Thinking for Complexity Assessment

**Action**: Analyze the request to determine complexity and identify gaps

**Sequential Thinking Tasks**:

1. **Complexity Evaluation**:
   - How many services are involved?
   - Is the logic simple or complex (conditional branching)?
   - Are there data transformations required?
   - Is AI integration needed?
   - Estimate: Simple (2-5 nodes) | Medium (5-15 nodes) | Complex (15+ nodes)

2. **Initial Decomposition**:
   - What are the major steps?
   - What's the trigger mechanism?
   - What's the main processing logic?
   - Where does the data end up?

3. **Dependency Identification**:
   - What must happen first?
   - What can run in parallel?
   - Are there conditional branches?

4. **Gap Analysis**:
   - What information is missing?
   - What assumptions need verification?
   - What edge cases should be considered?

---

### Step 4: Generate Clarification Questions

**Action**: Create specific questions to fill information gaps

**Key Areas to Clarify**:

#### Trigger Conditions
- "What should trigger this workflow?"
  - Webhook (real-time event)
  - Schedule (cron job)
  - Manual execution
  - Polling (check periodically)
- "How frequently should it run?" (if scheduled)
- "What specific event should trigger it?" (if webhook)

#### Data Source
- "Where does the input data come from?"
- "What format is the data in?" (JSON, CSV, XML, etc.)
- "Are there any authentication requirements?"
- "What fields/data points are most important?"

#### Data Processing
- "How should the data be transformed or processed?"
- "Are there any validation rules?"
- "Should certain data be filtered out?"
- "Are calculations or aggregations needed?"

#### Destination
- "Where should the final result be sent?"
- "What format should the output be in?"
- "Should there be notifications or confirmations?"

#### Conditional Logic
- "Are there specific conditions that change the workflow behavior?"
- "What should happen if certain conditions are met/not met?"
- "Should there be error handling or fallback paths?"

#### Performance & Scale
- "How much data will be processed at once?"
- "Are there rate limits or API restrictions to consider?"
- "Should the workflow handle batch processing?"

---

### Step 5: Create Structured Requirements Document

**Action**: Compile all information into a standardized format

**Requirements Document Structure**:

```json
{
  "intent": "Description of what the workflow should accomplish",
  "workflow_name": "Descriptive name for the workflow",
  "complexity": "simple | medium | complex",

  "trigger": {
    "type": "webhook | schedule | manual | polling",
    "details": "Specific trigger configuration (cron expression, webhook path, etc.)",
    "frequency": "How often it runs (if applicable)"
  },

  "dataSource": {
    "service": "Name of the source service",
    "type": "API | webhook payload | database | file",
    "format": "JSON | CSV | XML | other",
    "authentication": "Required auth method",
    "key_fields": ["field1", "field2", "..."]
  },

  "processing": [
    {
      "step": 1,
      "action": "Description of processing step",
      "type": "transform | filter | validate | enrich | calculate",
      "details": "Specific requirements for this step"
    }
  ],

  "destination": {
    "service": "Name of the destination service",
    "action": "What to do with the data (create, update, send, etc.)",
    "format": "Expected output format",
    "authentication": "Required auth method"
  },

  "conditions": [
    {
      "condition": "Description of the condition",
      "true_path": "What happens if true",
      "false_path": "What happens if false (if applicable)"
    }
  ],

  "error_handling": {
    "strategy": "retry | notify | log | ignore",
    "notification_channel": "Slack | email | webhook | none",
    "retry_config": {
      "max_attempts": 3,
      "wait_between": "1 minute"
    }
  },

  "additional_requirements": [
    "Any other special requirements or constraints"
  ]
}
```

---

### Step 6: Store Requirements in Memory MCP

**Action**: Save the structured requirements for reference in later phases

**Memory MCP Operations**:

```
Use Memory MCP to create entities:
- Entity: "workflow_requirements_[timestamp]"
  Type: "requirements"
  Observations: [complete requirements document]

- Entity: "user_intent_[workflow_name]"
  Type: "intent"
  Observations: ["Primary goal", "Success criteria", "Expected outcome"]

- Create relations:
  - User → created → workflow_requirements
  - workflow_requirements → defines → workflow_[name]
```

This allows later phases to reference requirements without re-asking questions.

---

## Output Checklist

Before proceeding to Phase 2, verify:

- [ ] User request is fully understood
- [ ] All critical questions have been answered
- [ ] Trigger mechanism is clearly defined
- [ ] Data source and destination are identified
- [ ] Processing steps are outlined
- [ ] Conditional logic (if any) is specified
- [ ] Error handling preferences are documented
- [ ] Requirements are stored in Memory MCP
- [ ] No ambiguous or missing information

---

## Common Pitfalls to Avoid

1. **Assuming default behavior**: Always clarify, don't assume
2. **Skipping error handling discussion**: Critical for production workflows
3. **Not considering authentication**: Can block implementation later
4. **Ignoring scale and performance**: Important for data-heavy workflows
5. **Missing conditional logic**: Can significantly change workflow design
6. **Vague processing requirements**: "Transform data" is not specific enough

---

## Example: Requirements Gathering

**User Input**:
"Create a workflow that monitors our support email and creates Jira tickets"

**Clarification Questions**:
1. Trigger: "How should we monitor emails? (IMAP polling every 5 minutes? Email webhook?)"
2. Filtering: "Should all emails create tickets, or only specific ones? (subject keywords, sender domains?)"
3. Data mapping: "What email fields map to which Jira fields? (subject→title, body→description?)"
4. Jira config: "Which Jira project? What issue type? Default priority?"
5. Error handling: "What if ticket creation fails? Retry? Alert someone?"
6. Deduplication: "How to avoid creating duplicate tickets for the same email?"

**Resulting Requirements**:
```json
{
  "intent": "Automatically create Jira support tickets from incoming support emails",
  "workflow_name": "Email to Jira Ticket Automation",
  "complexity": "medium",

  "trigger": {
    "type": "polling",
    "details": "IMAP email polling on support@company.com",
    "frequency": "Every 5 minutes"
  },

  "dataSource": {
    "service": "Email (IMAP)",
    "type": "Email messages",
    "format": "Email",
    "authentication": "IMAP credentials",
    "key_fields": ["subject", "from", "body", "date", "message_id"]
  },

  "processing": [
    {
      "step": 1,
      "action": "Filter emails by subject containing '[SUPPORT]'",
      "type": "filter",
      "details": "Only process emails with [SUPPORT] tag"
    },
    {
      "step": 2,
      "action": "Check if ticket already exists for this message_id",
      "type": "validate",
      "details": "Search Jira for existing ticket with this email ID in description"
    },
    {
      "step": 3,
      "action": "Extract customer info from email sender",
      "type": "transform",
      "details": "Parse name and email from 'from' field"
    },
    {
      "step": 4,
      "action": "Format email body as Jira description",
      "type": "transform",
      "details": "Convert HTML email to Markdown, add metadata"
    }
  ],

  "destination": {
    "service": "Jira",
    "action": "Create issue",
    "format": "Jira issue JSON",
    "authentication": "Jira API token"
  },

  "conditions": [
    {
      "condition": "Ticket with this message_id already exists",
      "true_path": "Skip creation, log as duplicate",
      "false_path": "Proceed with ticket creation"
    }
  ],

  "error_handling": {
    "strategy": "retry and notify",
    "notification_channel": "Slack #support-ops",
    "retry_config": {
      "max_attempts": 3,
      "wait_between": "2 minutes"
    }
  },

  "additional_requirements": [
    "Include original email message_id in ticket description for tracking",
    "Set ticket priority based on email subject: [URGENT] = High, otherwise Normal",
    "Assign to 'Support Triage' user initially"
  ]
}
```

---

## Transition to Phase 2

**When to proceed**:
- All requirements are documented and clear
- Requirements are stored in Memory MCP
- User has confirmed the requirements are correct

**Next Phase**: Load `phase2_decomposition.md` to break down the workflow into atomic tasks.

**Handoff**: Pass the structured requirements document to Phase 2 for task decomposition.
