# Phase 5: Connection Building

## Purpose

Create accurate connections between nodes to establish proper data flow, implement conditional branching, handle errors, and ensure workflow execution follows the designed logic.

## Overview

This phase transforms configured nodes into a connected workflow by building the `connections` object. It uses Sequential Thinking for connection logic design, n8n MCP for validation, and Memory for proven connection patterns.

## Process Flow

```
Configured Nodes (Phase 4)
    ↓
Sequential Thinking MCP (Design connection structure)
    ↓
Create connections object
    ↓
Memory MCP (Apply validated patterns)
    ↓
n8n MCP (Validate connections)
    ↓
Complete Connections Object
```

## Step-by-Step Guide

### Step 1: Retrieve Configured Nodes and Flow Design

**Action**: Load node configurations and flow structure

**Memory MCP Operations**:
```
Use Memory MCP to retrieve:
- Complete node configurations from Phase 4
- Flow structure from Phase 2 (linear/branching/parallel)
- Task dependencies
- Node execution order
```

---

### Step 2: Understand n8n Connections Structure

**Action**: Learn the connections object format

#### Connection Object Format

```json
{
  "connections": {
    "Source Node Name": {
      "main": [
        [
          {
            "node": "Target Node Name",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```

#### Key Components

**Source Node Name** (string):
- The exact name of the node sending data
- Must match the `name` field in the node object

**Output Type** ("main" or "error"):
- **main**: Normal execution output
- **error**: Error handling output (when node fails)

**Target Array** (nested arrays):
- First level: Output index (usually 0)
- Second level: Array of target nodes for that output
- Allows multiple targets from same output

**Target Object**:
- **node**: Name of target node (string)
- **type**: Always "main" for regular connections
- **index**: Input index on target node (usually 0)

---

### Step 3: Design Connection Logic with Sequential Thinking

**Action**: Plan how nodes should connect based on workflow type

**Sequential Thinking Tasks**:

#### 3.1 Linear Connections

**Pattern**: A → B → C → D

```
For each node in sequence:
  Connect node[i] output → node[i+1] input
```

**Example**:
```json
{
  "Webhook": {
    "main": [[{"node": "Parse Data", "type": "main", "index": 0}]]
  },
  "Parse Data": {
    "main": [[{"node": "Transform", "type": "main", "index": 0}]]
  },
  "Transform": {
    "main": [[{"node": "Save to DB", "type": "main", "index": 0}]]
  }
}
```

#### 3.2 Branching Connections (IF Node)

**Pattern**: A → IF → [True Path | False Path]

```
IF node has two outputs:
  Output[0] = True path (condition met)
  Output[1] = False path (condition not met)
```

**Example**:
```json
{
  "Check Status": {
    "main": [
      [{"node": "Active Path", "type": "main", "index": 0}],     // Output 0: True
      [{"node": "Inactive Path", "type": "main", "index": 0}]    // Output 1: False
    ]
  }
}
```

#### 3.3 Parallel Connections

**Pattern**: A → [B + C + D] → E (merge)

```
One node sends to multiple targets:
  Source output 0 connects to multiple nodes
Each parallel path eventually merges
```

**Example**:
```json
{
  "Fetch Data": {
    "main": [[
      {"node": "Send Email", "type": "main", "index": 0},
      {"node": "Update CRM", "type": "main", "index": 0},
      {"node": "Log to DB", "type": "main", "index": 0}
    ]]
  }
}
```

#### 3.4 Merge Connections

**Pattern**: [A + B + C] → Merge → D

```
Multiple nodes connect to Merge node:
  Each sends to different input index of Merge
  Or all send to same input (Merge handles it)
```

**Example**:
```json
{
  "Process A": {
    "main": [[{"node": "Merge", "type": "main", "index": 0}]]
  },
  "Process B": {
    "main": [[{"node": "Merge", "type": "main", "index": 0}]]
  },
  "Process C": {
    "main": [[{"node": "Merge", "type": "main", "index": 0}]]
  },
  "Merge": {
    "main": [[{"node": "Final Output", "type": "main", "index": 0}]]
  }
}
```

---

### Step 4: Build Connections Object

**Action**: Create the complete connections object for the workflow

#### Step-by-Step Process:

1. **List all nodes in execution order**
2. **For each node, determine its target(s)**:
   - Linear: Next node in sequence
   - Branch: Different nodes based on output index
   - Parallel: Multiple nodes from same output
   - Merge: Single target after all inputs
3. **Create connection entry for each source node**
4. **Validate node names match exactly**

#### Example Build Process

**Workflow**: Webhook → Parse → IF(has_email) → [Send Email | Log Only]

**Nodes**:
1. Webhook (trigger)
2. Parse Data (transform)
3. Check Email (IF node)
4. Send Email (output - true path)
5. Log Entry (output - false path)

**Connections**:
```json
{
  "connections": {
    "Webhook": {
      "main": [[
        {"node": "Parse Data", "type": "main", "index": 0}
      ]]
    },
    "Parse Data": {
      "main": [[
        {"node": "Check Email", "type": "main", "index": 0}
      ]]
    },
    "Check Email": {
      "main": [
        [{"node": "Send Email", "type": "main", "index": 0}],   // True path
        [{"node": "Log Entry", "type": "main", "index": 0}]     // False path
      ]
    }
    // Send Email and Log Entry are terminal nodes (no outgoing connections)
  }
}
```

---

### Step 5: Implement Error Handling Connections

**Action**: Add error output connections for critical nodes

#### Error Output Pattern

```json
{
  "Critical Node": {
    "main": [[
      {"node": "Success Path", "type": "main", "index": 0}
    ]],
    "error": [[
      {"node": "Error Handler", "type": "main", "index": 0}
    ]]
  }
}
```

#### When to Add Error Handling

**Add error connections for**:
- API calls (may fail due to network, auth, rate limits)
- Database operations (may fail due to connectivity, constraints)
- File operations (may fail due to permissions, disk space)
- External service integrations

**Example**:
```json
{
  "Create Jira Ticket": {
    "main": [[
      {"node": "Success Notification", "type": "main", "index": 0}
    ]],
    "error": [[
      {"node": "Error Alert to Slack", "type": "main", "index": 0}
    ]]
  }
}
```

**Error Handler Nodes**:
- Slack/Email notification nodes
- Logging nodes
- Retry logic nodes
- Fallback operation nodes

---

### Step 6: Handle Special Connection Cases

#### 6.1 Switch Node (Multiple Outputs)

```json
{
  "Switch": {
    "main": [
      [{"node": "Case 1", "type": "main", "index": 0}],    // Output 0
      [{"node": "Case 2", "type": "main", "index": 0}],    // Output 1
      [{"node": "Case 3", "type": "main", "index": 0}],    // Output 2
      [{"node": "Default", "type": "main", "index": 0}]    // Output 3 (fallback)
    ]
  }
}
```

#### 6.2 Split In Batches Node

```json
{
  "Split In Batches": {
    "main": [
      [{"node": "Process Batch", "type": "main", "index": 0}],  // Each batch
      []  // Done output (empty or connect to final node)
    ]
  }
}
```

#### 6.3 Loop Connections

**For nodes that loop back**:
```json
{
  "Process Batch": {
    "main": [[
      {"node": "Split In Batches", "type": "main", "index": 0}  // Loop back
    ]]
  }
}
```

**Important**: Ensure loop has exit condition to prevent infinite loops

---

### Step 7: Apply Memory MCP Patterns

**Action**: Use proven connection patterns

**Memory MCP Operations**:
```
Use Memory MCP to search for:
- "Successful connection patterns for [workflow_type]"
- "User's typical workflow structures"
- "Common branching patterns"
- "Error handling connection patterns"
```

**Reuse Patterns**:
- Standard linear flow connections
- Typical IF node branching
- Common error handling routes
- Proven parallel execution patterns

---

### Step 8: Validate Connections

**Action**: Check for common connection issues

**Validation Checklist**:

- [ ] **All node names match exactly**: Source and target names must match node object names
- [ ] **No orphan nodes**: Every non-trigger node has at least one incoming connection
- [ ] **No self-loops** (unless intentional with exit condition)
- [ ] **Correct output indices**: IF nodes use [0] and [1], not just [0]
- [ ] **Proper error connections**: Critical nodes have error handling
- [ ] **No circular dependencies** (unless intentional loops with exit)

**Manual Validation**:
```
For each node:
  1. Check if it appears as a target (except trigger nodes)
  2. Check if it has outgoing connections (except terminal nodes)
  3. Verify all referenced nodes exist in the node list
```

---

## Complete Connections Examples

### Example 1: Linear Workflow

**Workflow**: Schedule → Fetch Data → Transform → Save → Notify

```json
{
  "connections": {
    "Schedule Trigger": {
      "main": [[{"node": "Fetch Data", "type": "main", "index": 0}]]
    },
    "Fetch Data": {
      "main": [[{"node": "Transform", "type": "main", "index": 0}]]
    },
    "Transform": {
      "main": [[{"node": "Save to DB", "type": "main", "index": 0}]]
    },
    "Save to DB": {
      "main": [[{"node": "Notify Success", "type": "main", "index": 0}]],
      "error": [[{"node": "Notify Error", "type": "main", "index": 0}]]
    }
  }
}
```

### Example 2: Branching Workflow

**Workflow**: Webhook → Parse → IF(priority) → [High Path | Normal Path]

```json
{
  "connections": {
    "Webhook": {
      "main": [[{"node": "Parse Email", "type": "main", "index": 0}]]
    },
    "Parse Email": {
      "main": [[{"node": "Check Priority", "type": "main", "index": 0}]]
    },
    "Check Priority": {
      "main": [
        [{"node": "Alert Team", "type": "main", "index": 0}],        // High priority
        [{"node": "Create Ticket", "type": "main", "index": 0}]      // Normal priority
      ]
    }
  }
}
```

### Example 3: Parallel Execution with Merge

**Workflow**: Trigger → Fetch → [Email + Slack + DB] → Merge → Final

```json
{
  "connections": {
    "Schedule": {
      "main": [[{"node": "Fetch Data", "type": "main", "index": 0}]]
    },
    "Fetch Data": {
      "main": [[
        {"node": "Send Email", "type": "main", "index": 0},
        {"node": "Post Slack", "type": "main", "index": 0},
        {"node": "Save to DB", "type": "main", "index": 0}
      ]]
    },
    "Send Email": {
      "main": [[{"node": "Merge", "type": "main", "index": 0}]]
    },
    "Post Slack": {
      "main": [[{"node": "Merge", "type": "main", "index": 0}]]
    },
    "Save to DB": {
      "main": [[{"node": "Merge", "type": "main", "index": 0}]]
    },
    "Merge": {
      "main": [[{"node": "Log Summary", "type": "main", "index": 0}]]
    }
  }
}
```

### Example 4: Complex Branching with Error Handling

**Workflow**: Webhook → Parse → Check Duplicate → IF → [Skip | Process → Create → Notify]

```json
{
  "connections": {
    "Webhook": {
      "main": [[{"node": "Parse Data", "type": "main", "index": 0}]]
    },
    "Parse Data": {
      "main": [[{"node": "Check Duplicate", "type": "main", "index": 0}]]
    },
    "Check Duplicate": {
      "main": [[{"node": "IF Duplicate", "type": "main", "index": 0}]]
    },
    "IF Duplicate": {
      "main": [
        [{"node": "Log Skipped", "type": "main", "index": 0}],       // Duplicate found
        [{"node": "Process Data", "type": "main", "index": 0}]       // No duplicate
      ]
    },
    "Process Data": {
      "main": [[{"node": "Create Record", "type": "main", "index": 0}]]
    },
    "Create Record": {
      "main": [[{"node": "Notify Success", "type": "main", "index": 0}]],
      "error": [[{"node": "Notify Error", "type": "main", "index": 0}]]
    }
  }
}
```

---

## Connection Best Practices

### 1. Descriptive Node Names in Connections

Use the same descriptive names from node configuration:
```json
// Good
{"node": "Send Success Email", ...}

// Bad
{"node": "SendEmail1", ...}
```

### 2. Consistent Error Handling

All critical operations should have error outputs:
```json
{
  "API Call": {
    "main": [[...success path...]],
    "error": [[...error handler...]]
  }
}
```

### 3. Avoid Over-Branching

**Too complex**:
```
IF → IF → IF → IF (4 levels of nesting)
```

**Better**:
```
Use Switch node or consolidate conditions
```

### 4. Document Complex Connections

Add notes to nodes explaining complex routing logic

### 5. Terminal Nodes

Nodes with no outgoing connections (last nodes in each path) don't need connection entries:
```json
{
  "connections": {
    // ... other connections ...
    // "Final Node" has no entry here (it's terminal)
  }
}
```

---

## Common Connection Patterns

### Pattern 1: Try-Catch

```
Node → Success → Continue
     → Error → Error Handler → Continue (optional)
```

### Pattern 2: Fan-Out

```
One Source → Multiple Parallel Targets
```

### Pattern 3: Fan-In

```
Multiple Sources → Single Merge → Continue
```

### Pattern 4: Conditional Split

```
IF Node → True → Path A
       → False → Path B
```

### Pattern 5: Loop

```
Process → Check Complete → IF → Done → Exit
                              → Not Done → Process (loop)
```

---

## Output Checklist

Before proceeding to Phase 6, verify:

- [ ] All nodes (except triggers and terminals) have connections
- [ ] Node names in connections match node object names exactly
- [ ] Output indices are correct (especially for IF, Switch nodes)
- [ ] Error handling connections are present for critical nodes
- [ ] No orphan nodes (unconnected)
- [ ] No invalid circular dependencies
- [ ] Connection logic matches designed flow structure
- [ ] Connections object is complete and stored in Memory

---

## Common Pitfalls to Avoid

1. **Name mismatches**: "Slack" in node vs "Slack Notification" in connection
2. **Wrong output index**: Using [0] for both IF branches instead of [0] and [1]
3. **Missing error connections**: No error handling for API calls
4. **Orphan nodes**: Nodes created but not connected
5. **Circular loops without exit**: Infinite loop risk
6. **Empty output arrays**: `"main": [[]]` instead of proper connections

---

## Transition to Phase 6

**When to proceed**:
- Connections object is complete
- All connections are validated
- No orphan or disconnected nodes
- Error handling is implemented
- Connections are stored in Memory

**Next Phase**: Load `phase6_layout.md` to optimize visual node positioning.

**Handoff**: Pass complete connections object to Phase 6 for layout optimization.
