# Phase 2: Task Decomposition

## Purpose

Break down the complete workflow into atomic, executable tasks with clear dependencies and execution order. Transform high-level requirements into a detailed implementation plan.

## Overview

This phase takes the structured requirements from Phase 1 and decomposes them into the smallest possible units of work (atomic tasks). Each task should be independent, testable, and mappable to specific n8n nodes.

## Process Flow

```
Structured Requirements (Phase 1)
    ↓
Sequential Thinking MCP (Primary Tool)
    ↓
Task List with Dependencies
    ↓
Flow Structure Design (Linear/Branching/Parallel)
    ↓
Execution Order & Priority
```

## Step-by-Step Guide

### Step 1: Retrieve Requirements from Memory

**Action**: Load the requirements document created in Phase 1

**Memory MCP Operations**:
```
Use Memory MCP to search for:
- workflow_requirements_[timestamp]
- user_intent_[workflow_name]
- Related requirements and context
```

---

### Step 2: Use Sequential Thinking for Decomposition

**Action**: Break down the workflow into atomic tasks

**Sequential Thinking Process**:

1. **Identify Major Components**:
   - What is the trigger mechanism?
   - What are the data input steps?
   - What transformations are needed?
   - What conditional logic exists?
   - What are the output steps?

2. **Break Down to Atomic Level**:
   - Each task should do ONE thing
   - Tasks should be testable independently
   - Tasks should map to a single node (or small node group)
   - No task should contain "and" in its description (sign of multiple tasks)

3. **Categorize Each Task**:
   - **Input**: Trigger and data ingestion tasks
   - **Transform**: Data manipulation, formatting, enrichment
   - **AI**: AI model processing, embeddings, completions
   - **Logic**: Conditional branching, routing, decisions
   - **Output**: Sending data, creating records, notifications

4. **Identify Dependencies**:
   - Which tasks must complete before others can start?
   - Which tasks can run in parallel?
   - Which tasks are independent?

---

### Step 3: Create Task List

**Action**: Document each atomic task with metadata

**Task Structure**:

```json
{
  "id": "task_1",
  "type": "trigger | processing | output | logic",
  "category": "Input | Transform | AI | Logic | Output",
  "description": "Clear, concise description of what this task does",
  "n8n_node_hint": "Suggested n8n node type (will be refined in Phase 3)",
  "dependencies": ["task_id1", "task_id2"],
  "priority": 1,
  "estimated_complexity": "simple | medium | complex"
}
```

**Task Types**:
- **trigger**: Workflow initiation (webhook, schedule, manual)
- **processing**: Data manipulation or transformation
- **output**: Final data destination or notification
- **logic**: Conditional branching or decision making

**Categories**:
- **Input**: Receiving or fetching data
- **Transform**: Changing data format, filtering, calculating
- **AI**: Using AI models for processing
- **Logic**: If/else, switch, routing decisions
- **Output**: Sending, storing, or displaying results

---

### Step 4: Design Flow Structure

**Action**: Determine how tasks connect and flow

**Flow Types**:

#### 1. Linear Structure
```
[Trigger] → [Task A] → [Task B] → [Task C] → [Output]
```
- **Use when**: Sequential processing with no branching
- **Example**: Webhook → Parse Data → Transform → Save to Database

#### 2. Branching Structure
```
[Trigger] → [Task A] → [Logic/IF] ─┬─ [True Path]  → [Output A]
                                     └─ [False Path] → [Output B]
```
- **Use when**: Different paths based on conditions
- **Example**: Check if email has attachment → If yes, save file | If no, just log

#### 3. Parallel Structure
```
                        ┌─ [Task A] ─┐
[Trigger] → [Task 0] ──├─ [Task B] ─┤→ [Merge] → [Output]
                        └─ [Task C] ─┘
```
- **Use when**: Multiple independent operations on same data
- **Example**: Send email, update CRM, and post to Slack simultaneously

#### 4. Complex Structure (Combination)
```
                    ┌─ [Logic] ─┬─ [Path A1] → [Output A]
[Trigger] → [Task] ─┤           └─ [Path A2] → [Output B]
                    └─ [Task B] ──→ [Output C]
```
- **Use when**: Multiple conditions and parallel paths
- **Example**: Process different types of events differently, with notifications

---

### Step 5: Determine Execution Priority

**Action**: Assign priority to tasks for optimal execution

**Priority Levels**:
- **Priority 1**: Must execute first (triggers, initial data fetch)
- **Priority 2**: Core processing tasks (transformations, validations)
- **Priority 3**: Conditional logic and routing
- **Priority 4**: Final outputs and notifications
- **Priority 5**: Cleanup, logging, optional tasks

**Dependency Rules**:
- A task cannot execute until all its dependencies complete
- Tasks with no dependencies can be Priority 1 (except trigger is always first)
- Tasks that depend on conditional logic get priority after the logic node

---

### Step 6: Memory MCP - Validate with Past Patterns

**Action**: Check if similar workflow structures exist and worked well

**Memory MCP Operations**:
```
Use Memory MCP to search for:
- Similar task structures that succeeded
- Common patterns for this type of workflow
- Known issues with specific task combinations
- User's preferred workflow styles
```

**Apply Learnings**:
- Reuse successful task decomposition patterns
- Avoid task combinations that previously failed
- Apply user's preferred complexity level
- Consider user's technical expertise

---

## Output Format

**Complete Task Decomposition Document**:

```json
{
  "workflow_name": "Email to Jira Ticket Automation",
  "total_tasks": 8,
  "flow_type": "branching",

  "tasks": [
    {
      "id": "task_1",
      "type": "trigger",
      "category": "Input",
      "description": "Monitor support email inbox via IMAP every 5 minutes",
      "n8n_node_hint": "IMAP Email Trigger or Schedule Trigger + IMAP",
      "dependencies": [],
      "priority": 1,
      "estimated_complexity": "simple"
    },
    {
      "id": "task_2",
      "type": "processing",
      "category": "Transform",
      "description": "Extract email metadata (subject, from, body, message_id, date)",
      "n8n_node_hint": "Set node or Code node",
      "dependencies": ["task_1"],
      "priority": 2,
      "estimated_complexity": "simple"
    },
    {
      "id": "task_3",
      "type": "processing",
      "category": "Transform",
      "description": "Filter emails: only process if subject contains '[SUPPORT]'",
      "n8n_node_hint": "Filter node or IF node",
      "dependencies": ["task_2"],
      "priority": 2,
      "estimated_complexity": "simple"
    },
    {
      "id": "task_4",
      "type": "processing",
      "category": "Input",
      "description": "Search Jira for existing tickets with this message_id",
      "n8n_node_hint": "Jira node with search operation",
      "dependencies": ["task_3"],
      "priority": 2,
      "estimated_complexity": "medium"
    },
    {
      "id": "task_5",
      "type": "logic",
      "category": "Logic",
      "description": "Check if ticket already exists (deduplication)",
      "n8n_node_hint": "IF node",
      "dependencies": ["task_4"],
      "priority": 3,
      "estimated_complexity": "simple"
    },
    {
      "id": "task_6a",
      "type": "output",
      "category": "Output",
      "description": "If ticket exists: Log as duplicate and skip",
      "n8n_node_hint": "Set node or Stop and Error node",
      "dependencies": ["task_5"],
      "priority": 4,
      "estimated_complexity": "simple",
      "condition": "true_path"
    },
    {
      "id": "task_6b",
      "type": "processing",
      "category": "Transform",
      "description": "If ticket doesn't exist: Format email data for Jira",
      "n8n_node_hint": "Code node or Function node",
      "dependencies": ["task_5"],
      "priority": 4,
      "estimated_complexity": "medium",
      "condition": "false_path"
    },
    {
      "id": "task_7",
      "type": "output",
      "category": "Output",
      "description": "Create new Jira ticket with formatted data",
      "n8n_node_hint": "Jira node with create issue operation",
      "dependencies": ["task_6b"],
      "priority": 4,
      "estimated_complexity": "medium"
    },
    {
      "id": "task_8",
      "type": "output",
      "category": "Output",
      "description": "Send success notification to Slack #support-ops",
      "n8n_node_hint": "Slack node",
      "dependencies": ["task_7"],
      "priority": 5,
      "estimated_complexity": "simple"
    }
  ],

  "flow_diagram": {
    "description": "Branching structure with deduplication check",
    "structure": [
      "task_1 (Trigger: IMAP)",
      "task_2 (Extract email data)",
      "task_3 (Filter by [SUPPORT])",
      "task_4 (Search existing Jira tickets)",
      "task_5 (IF: Ticket exists?)",
      "  ├─ True → task_6a (Log duplicate)",
      "  └─ False → task_6b (Format data) → task_7 (Create ticket) → task_8 (Notify Slack)"
    ]
  },

  "error_handling_tasks": [
    {
      "id": "error_1",
      "type": "output",
      "category": "Output",
      "description": "On Jira API failure: Send alert to Slack with error details",
      "n8n_node_hint": "Slack node (on error path)",
      "dependencies": ["task_7"],
      "priority": 5,
      "estimated_complexity": "simple"
    }
  ]
}
```

---

## Decomposition Best Practices

### 1. Task Granularity
- **Too broad**: "Process data and send to API"
- **Too narrow**: "Extract first character of email subject"
- **Just right**: "Extract email metadata (subject, from, body, date)"

### 2. Clear Dependencies
- Explicitly list all dependencies
- Don't create circular dependencies
- Minimize cross-dependencies for parallel execution

### 3. Testability
- Each task should be testable independently
- Clear input and output for each task
- Verifiable success/failure conditions

### 4. Naming Convention
- Use verb-noun format: "Extract email data", "Create Jira ticket"
- Be specific: Not "Process data" but "Transform JSON to CSV"
- Include key details: "Search Jira for existing tickets with message_id"

---

## Common Workflow Patterns

### Pattern 1: Simple Data Pipeline
```
Trigger → Fetch Data → Transform → Validate → Send to Destination
(5 tasks, linear, Priority 1-4)
```

### Pattern 2: Conditional Processing
```
Trigger → Fetch Data → Check Condition → [Path A | Path B] → Output
(5+ tasks, branching, Priority 1-4)
```

### Pattern 3: Multi-Destination
```
Trigger → Process → [Send to API + Update DB + Notify User]
(5+ tasks, parallel output, Priority 1-4)
```

### Pattern 4: Enrichment Pipeline
```
Trigger → Fetch A → Fetch B → Merge → Transform → Output
(6+ tasks, sequential with merging, Priority 1-4)
```

### Pattern 5: AI Processing
```
Trigger → Prepare Data → AI Analysis → Format Results → Output
(5+ tasks, linear with AI, Priority 1-4)
```

---

## Output Checklist

Before proceeding to Phase 3, verify:

- [ ] All major workflow steps are represented as tasks
- [ ] Each task is atomic (does one thing)
- [ ] All tasks are categorized (Input/Transform/AI/Logic/Output)
- [ ] Dependencies are clearly defined
- [ ] Execution priority is assigned
- [ ] Flow structure is identified (linear/branching/parallel)
- [ ] Error handling tasks are included
- [ ] Task decomposition is stored in Memory MCP
- [ ] No circular dependencies exist

---

## Common Pitfalls to Avoid

1. **Too few tasks**: Combining multiple operations into one task
2. **Too many tasks**: Over-splitting simple operations
3. **Missing dependencies**: Tasks that rely on others not marked
4. **Circular dependencies**: Task A depends on B, B depends on A
5. **Vague descriptions**: "Handle data" instead of "Transform JSON to CSV"
6. **Ignoring error paths**: Not planning for failure scenarios
7. **Forgetting parallel opportunities**: Sequential when parallel is possible

---

## Example: Task Decomposition

**Simple Workflow**: "Send daily summary email of new GitHub issues"

**Decomposition**:
```json
{
  "workflow_name": "Daily GitHub Issues Summary",
  "total_tasks": 5,
  "flow_type": "linear",

  "tasks": [
    {
      "id": "task_1",
      "type": "trigger",
      "category": "Input",
      "description": "Trigger workflow daily at 9 AM",
      "n8n_node_hint": "Schedule Trigger (cron: 0 9 * * *)",
      "dependencies": [],
      "priority": 1
    },
    {
      "id": "task_2",
      "type": "processing",
      "category": "Input",
      "description": "Fetch GitHub issues created in last 24 hours",
      "n8n_node_hint": "GitHub node",
      "dependencies": ["task_1"],
      "priority": 2
    },
    {
      "id": "task_3",
      "type": "processing",
      "category": "Transform",
      "description": "Format issues list as HTML email body",
      "n8n_node_hint": "Code node or Function node",
      "dependencies": ["task_2"],
      "priority": 3
    },
    {
      "id": "task_4",
      "type": "processing",
      "category": "Logic",
      "description": "Check if there are any issues (skip email if none)",
      "n8n_node_hint": "IF node",
      "dependencies": ["task_3"],
      "priority": 3
    },
    {
      "id": "task_5",
      "type": "output",
      "category": "Output",
      "description": "Send summary email via Gmail",
      "n8n_node_hint": "Gmail node or Send Email node",
      "dependencies": ["task_4"],
      "priority": 4
    }
  ]
}
```

---

## Transition to Phase 3

**When to proceed**:
- Task decomposition is complete and validated
- All dependencies are resolved
- Task list is stored in Memory MCP
- Flow structure is clearly defined

**Next Phase**: Load `phase3_node_matching.md` to select appropriate n8n nodes for each task.

**Handoff**: Pass the complete task decomposition document to Phase 3 for node selection.
