# Phase 3: Node Matching

## Purpose

Select the most appropriate n8n nodes for each task identified in Phase 2. Match tasks to nodes based on functionality, reliability, ease of use, and community support.

## Overview

This phase combines information gathering (Tavily MCP), node discovery (n8n MCP), strategic evaluation (Sequential Thinking MCP), and historical validation (Memory MCP) to select optimal nodes for the workflow.

## Process Flow

```
Task List (Phase 2)
    ↓
Tavily MCP (Latest n8n documentation & examples)
    ↓
n8n MCP (Node search & information retrieval)
    ↓
Sequential Thinking MCP (Node evaluation & selection)
    ↓
Memory MCP (Validate against successful patterns)
    ↓
Node Chain with Selected Nodes
```

## Step-by-Step Guide

### Step 1: Retrieve Task List from Memory

**Action**: Load the task decomposition document from Phase 2

**Memory MCP Operations**:
```
Use Memory MCP to retrieve:
- Complete task list with descriptions
- Task dependencies and priorities
- Flow structure (linear/branching/parallel)
- User preferences for specific services
```

---

### Step 2: Gather Latest Information with Tavily MCP

**Action**: Search for up-to-date n8n documentation and best practices

**Tavily MCP Search Strategy**:

#### Search Queries by Task Category:

**For Input/Trigger tasks**:
```
- "n8n trigger nodes 2025"
- "n8n webhook trigger setup"
- "n8n schedule cron trigger examples"
- "n8n [service-name] trigger documentation"
```

**For Transform tasks**:
```
- "n8n data transformation nodes"
- "n8n code node examples"
- "n8n set node vs function node"
- "n8n json to csv conversion"
```

**For AI tasks**:
```
- "n8n AI agent node 2025"
- "n8n OpenAI integration examples"
- "n8n langchain nodes guide"
- "n8n vector store setup"
```

**For Logic tasks**:
```
- "n8n IF node conditional logic"
- "n8n switch node examples"
- "n8n merge node patterns"
```

**For Output tasks**:
```
- "n8n [service-name] node documentation 2025"
- "n8n API request examples"
- "n8n email notification setup"
```

#### What to Look For:

1. **Official Documentation**: docs.n8n.io - Most authoritative
2. **Community Examples**: community.n8n.io - Real-world usage
3. **GitHub Examples**: n8n-io/n8n - Code samples
4. **Recent Blog Posts**: Include year (2024/2025) in search
5. **Known Issues**: Check for reported problems with specific nodes

**Focus Areas**:
- Latest node versions and capabilities
- Breaking changes in recent updates
- Recommended node combinations
- Common gotchas and solutions
- Authentication best practices

---

### Step 3: Search and Discover Nodes with n8n MCP

**Action**: Use n8n MCP tools to find candidate nodes for each task

#### 3.1 Category-Based Search

For systematic exploration:

```
list_nodes({category: 'trigger', limit: 50})
list_nodes({category: 'transform', limit: 50})
list_nodes({category: 'AI', limit: 50})
```

#### 3.2 Keyword-Based Search

For specific functionality:

```
search_nodes({query: "slack", includeExamples: true})
search_nodes({query: "email trigger", includeExamples: true})
search_nodes({query: "json transform", includeExamples: true})
search_nodes({query: "openai", includeExamples: true})
```

**Why includeExamples=true?**
- Provides real-world configuration examples
- Shows typical use cases
- Demonstrates parameter structure
- Helps understand node capabilities

#### 3.3 Get Detailed Node Information

For each candidate node:

```
get_node_essentials({
  nodeType: "n8n-nodes-base.slack",
  includeExamples: true
})
```

Returns:
- Node description and purpose
- Available resources and operations
- Required and optional parameters
- Common configuration examples
- Authentication requirements

#### 3.4 Get Comprehensive Documentation

For complex nodes:

```
get_node_documentation({
  nodeType: "n8n-nodes-base.httpRequest"
})
```

Returns:
- Full usage guide
- All available options
- Authentication methods
- Examples and patterns
- Best practices

---

### Step 4: Evaluate Nodes with Sequential Thinking MCP

**Action**: Systematically evaluate candidate nodes and select the best fit

**Sequential Thinking Process**:

For each task, evaluate candidate nodes across multiple dimensions:

#### Evaluation Criteria:

1. **Functionality Match** (0-10):
   - Does it do exactly what the task requires?
   - Are all necessary features available?
   - Are there any limitations?

2. **Ease of Use** (0-10):
   - Is configuration straightforward?
   - Are examples readily available?
   - Is documentation clear?

3. **Reliability** (0-10):
   - Is the node actively maintained?
   - Are there known issues?
   - What's the community feedback?

4. **Integration Quality** (0-10):
   - Does it work well with other selected nodes?
   - Is data format compatible?
   - Are there version conflicts?

5. **User Context** (0-10):
   - Has the user successfully used this node before?
   - Does it match user's technical level?
   - Is it a preferred service?

#### Evaluation Matrix Example:

**Task**: "Send notification to Slack"

**Candidate Nodes**:

```
Node: n8n-nodes-base.slack
├─ Functionality: 10/10 (Native Slack support, all features)
├─ Ease of Use: 8/10 (Requires Slack app setup, but well-documented)
├─ Reliability: 10/10 (Official node, actively maintained)
├─ Integration: 10/10 (Works seamlessly with other nodes)
├─ User Context: 9/10 (User has Slack credentials already)
└─ Total Score: 47/50 (94%)

Node: n8n-nodes-base.httpRequest
├─ Functionality: 8/10 (Can call Slack API, but manual)
├─ Ease of Use: 5/10 (Requires knowing Slack API details)
├─ Reliability: 10/10 (Core node, very stable)
├─ Integration: 8/10 (Needs manual formatting)
├─ User Context: 6/10 (More complex setup)
└─ Total Score: 37/50 (74%)

Decision: Select n8n-nodes-base.slack (higher score, native support)
```

---

### Step 5: Validate with Memory MCP

**Action**: Check selected nodes against historical success/failure patterns

**Memory MCP Operations**:

```
Use Memory MCP to search for:
- "Has user successfully used [node-name] before?"
- "Are there known issues with [node-name] for this user?"
- "What node combinations worked well in the past?"
- "Were there any failed workflows using [node-name]?"
```

**Apply Historical Insights**:
- **Prefer nodes the user has successfully used**: Reduce learning curve
- **Avoid nodes that previously failed**: Unless issues are resolved
- **Reuse successful node combinations**: Proven patterns
- **Consider user's technical level**: Simpler nodes for beginners

**If conflicts arise**:
- High-scoring node vs. user's unfamiliar node → Consider both, explain tradeoff
- Previously failed node now updated → Check if issues are fixed
- User preference conflicts with best practice → Discuss with user

---

### Step 6: Create Node Chain

**Action**: Compile the final list of selected nodes with rationale

**Node Chain Structure**:

```json
{
  "workflow_name": "Email to Jira Ticket Automation",
  "total_nodes": 8,

  "node_chain": [
    {
      "position": 1,
      "task_id": "task_1",
      "task_description": "Monitor support email inbox via IMAP every 5 minutes",
      "nodeType": "n8n-nodes-base.scheduleTrigger",
      "node_name": "Schedule Trigger",
      "role": "trigger",
      "score": 0.95,
      "reason": "Reliable trigger for polling operations. Simple cron configuration. Well-documented.",
      "alternative_considered": "Manual trigger (score: 0.6 - not suitable for automation)"
    },
    {
      "position": 2,
      "task_id": "task_1",
      "task_description": "Fetch emails via IMAP",
      "nodeType": "n8n-nodes-base.emailReadImap",
      "node_name": "IMAP Email",
      "role": "input",
      "score": 0.92,
      "reason": "Native IMAP support. Handles email parsing. Supports filtering.",
      "configuration_notes": "Will need IMAP credentials for support@company.com"
    },
    {
      "position": 3,
      "task_id": "task_3",
      "task_description": "Filter emails by [SUPPORT] in subject",
      "nodeType": "n8n-nodes-base.if",
      "node_name": "IF",
      "role": "logic",
      "score": 0.90,
      "reason": "Simple conditional logic. Easy to configure. Can filter by regex or exact match.",
      "configuration_notes": "Use condition: subject contains '[SUPPORT]'"
    },
    {
      "position": 4,
      "task_id": "task_4",
      "task_description": "Search Jira for existing tickets",
      "nodeType": "n8n-nodes-base.jira",
      "node_name": "Jira",
      "role": "input",
      "score": 0.93,
      "reason": "Official Jira integration. Supports JQL search. Well-maintained.",
      "configuration_notes": "Use JQL search operation, search by message_id in description"
    },
    {
      "position": 5,
      "task_id": "task_5",
      "task_description": "Check if ticket exists (deduplication)",
      "nodeType": "n8n-nodes-base.if",
      "node_name": "IF",
      "role": "logic",
      "score": 0.90,
      "reason": "Check search results. Branch to duplicate or create paths.",
      "configuration_notes": "Condition: {{$json.issues.length > 0}}"
    },
    {
      "position": 6,
      "task_id": "task_6b",
      "task_description": "Format email data for Jira",
      "nodeType": "n8n-nodes-base.code",
      "node_name": "Code",
      "role": "transform",
      "score": 0.88,
      "reason": "Flexible JavaScript code execution. Can handle complex formatting. Supports markdown conversion.",
      "alternative_considered": "Function node (score: 0.85 - similar but Code node is more powerful)"
    },
    {
      "position": 7,
      "task_id": "task_7",
      "task_description": "Create new Jira ticket",
      "nodeType": "n8n-nodes-base.jira",
      "node_name": "Jira",
      "role": "output",
      "score": 0.93,
      "reason": "Same node as search, create issue operation. Consistent authentication.",
      "configuration_notes": "Use create issue operation, map email fields to Jira fields"
    },
    {
      "position": 8,
      "task_id": "task_8",
      "task_description": "Send success notification to Slack",
      "nodeType": "n8n-nodes-base.slack",
      "node_name": "Slack",
      "role": "output",
      "score": 0.94,
      "reason": "Native Slack support. Easy message formatting. User already has credentials.",
      "configuration_notes": "Post to #support-ops channel"
    }
  ],

  "error_handling_nodes": [
    {
      "position": "error_path_from_7",
      "task_id": "error_1",
      "task_description": "Alert on Jira API failure",
      "nodeType": "n8n-nodes-base.slack",
      "node_name": "Slack Error Alert",
      "role": "output",
      "score": 0.94,
      "reason": "Same Slack node, connect to error output of Jira create node"
    }
  ]
}
```

---

## Node Selection Best Practices

### 1. Prefer Official Nodes Over HTTP Requests

**Good**:
```
Task: "Send Slack message"
Selected: n8n-nodes-base.slack
Reason: Native support, easier configuration, better error handling
```

**Avoid** (unless necessary):
```
Task: "Send Slack message"
Selected: n8n-nodes-base.httpRequest
Reason: More complex, requires API knowledge, manual error handling
```

**When to use HTTP Request**:
- Service doesn't have official node
- Need very specific API endpoint not supported
- Custom authentication required

### 2. Match Node Capabilities to Task Requirements

**Exact match is best**:
- Task needs "Send email" → Use "Send Email" node
- Task needs "Create database record" → Use specific DB node (PostgreSQL, MySQL)

**Avoid over-engineering**:
- Don't use Code node when Set node suffices
- Don't use Function node for simple transformations

### 3. Consider Maintenance and Updates

**Prefer**:
- Nodes with recent updates (check last update date)
- Nodes with active community support
- Nodes from `n8n-nodes-base` (core nodes)

**Be cautious with**:
- Deprecated nodes (check documentation)
- Nodes with many open issues
- Community nodes without recent updates

### 4. Optimize for User Experience

**For beginners**:
- Choose nodes with visual configurators
- Prefer nodes with good documentation
- Select nodes with examples

**For advanced users**:
- Can use Code/Function nodes for flexibility
- Can use HTTP Request for custom endpoints
- Can chain complex logic

---

## Common Node Patterns

### Pattern 1: Webhook to API

```
Nodes: Webhook → Code (validate) → HTTP Request → Set (format) → Response
```

### Pattern 2: Scheduled Data Sync

```
Nodes: Schedule Trigger → [Service A] → Transform → [Service B] → Log
```

### Pattern 3: AI Processing

```
Nodes: Trigger → Prepare Data → AI Agent → Tool nodes → Format → Output
```

### Pattern 4: Conditional Routing

```
Nodes: Trigger → Fetch Data → IF → [Path A | Path B] → Merge (optional) → Output
```

### Pattern 5: Multi-Service Integration

```
Nodes: Trigger → Fetch → [Service A + Service B + Service C] → Combine → Output
```

---

## Output Checklist

Before proceeding to Phase 4, verify:

- [ ] Every task has a matched node
- [ ] Node scores are documented with rationale
- [ ] All nodes are compatible with each other
- [ ] Alternative nodes are considered
- [ ] User context and history are applied
- [ ] Error handling nodes are included
- [ ] Latest node information from Tavily is incorporated
- [ ] Node chain is stored in Memory MCP
- [ ] No deprecated or problematic nodes selected

---

## Common Pitfalls to Avoid

1. **Using HTTP Request when official node exists**: Harder to maintain
2. **Ignoring node version compatibility**: Can cause execution errors
3. **Not checking for deprecated nodes**: May stop working in future
4. **Selecting unfamiliar nodes without justification**: Increases complexity
5. **Forgetting error handling nodes**: Workflows fail silently
6. **Not considering authentication requirements**: Blocked during implementation
7. **Over-complicating with Code nodes**: Use built-in transformations when possible

---

## Transition to Phase 4

**When to proceed**:
- All tasks have matched nodes
- Node selections are validated and scored
- Node chain is complete and stored in Memory
- User has confirmed node selections (if needed)

**Next Phase**: Load `phase4_configuration.md` to configure each node's parameters.

**Handoff**: Pass the complete node chain with selected nodes to Phase 4 for detailed configuration.
