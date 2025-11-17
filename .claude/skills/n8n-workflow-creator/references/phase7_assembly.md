# Phase 7: Workflow Assembly

## Purpose

Combine all components (nodes, connections, settings) into a complete, valid n8n workflow JSON ready for import or deployment.

## Overview

This phase brings together all previous work into the final workflow structure. It uses n8n MCP for JSON generation and Memory MCP for metadata (naming, tags, descriptions).

## Process Flow

```
Configured Nodes with Positions (Phase 6)
Connections Object (Phase 5)
    ↓
n8n MCP (Assemble workflow structure)
    ↓
Memory MCP (Generate metadata)
    ↓
Add workflow settings
    ↓
Complete Workflow JSON
```

## Step-by-Step Guide

### Step 1: Retrieve All Components

**Action**: Load all necessary pieces from previous phases

**Memory MCP Operations**:
```
Use Memory MCP to retrieve:
- Complete node objects with positions (Phase 6)
- Complete connections object (Phase 5)
- Original requirements (Phase 1)
- User preferences and patterns
```

---

### Step 2: Generate Workflow Name

**Action**: Create a descriptive, meaningful workflow name

**Naming Patterns**:

#### Pattern 1: Service Integration
```
Format: "[Source] to [Destination] [Action]"
Examples:
  - "Slack to Jira Ticket Automation"
  - "Gmail to Google Sheets Data Sync"
  - "GitHub to Discord Notification"
```

#### Pattern 2: Function Description
```
Format: "[Action] [Object] [Condition]"
Examples:
  - "Send Daily Summary Email"
  - "Process New Form Submissions"
  - "Monitor Server Health"
```

#### Pattern 3: AI/Automation Focus
```
Format: "[AI/Agent] [Purpose]"
Examples:
  - "AI Customer Support Agent"
  - "Automated Content Moderation"
  - "Smart Email Classifier"
```

**Memory MCP Input**:
```
Check user's previous workflow naming patterns
Apply consistent naming conventions
```

**Example**:
```
Requirements: "Monitor support emails and create Jira tickets"
Generated Name: "Support Email to Jira Automation"
```

---

### Step 3: Generate Tags

**Action**: Create relevant categorization tags

**Tag Categories**:

1. **Service Tags**: Names of services used
   - Examples: "slack", "jira", "gmail", "openai"

2. **Function Tags**: What the workflow does
   - Examples: "automation", "notification", "data-sync", "monitoring"

3. **Category Tags**: Workflow type
   - Examples: "integration", "ai-agent", "scheduled", "webhook"

4. **Department Tags**: Team or use case
   - Examples: "support", "sales", "marketing", "devops"

**Example**:
```json
{
  "tags": [
    "email",
    "jira",
    "support",
    "automation",
    "ticket-creation"
  ]
}
```

---

### Step 4: Create Workflow Description

**Action**: Generate comprehensive description

**Description Structure**:

```
[Purpose]

**Trigger**: [How workflow starts]
**Processing**: [What it does]
**Output**: [Where results go]

**Key Features**:
- Feature 1
- Feature 2
- Feature 3
```

**Example**:
```
Automatically creates Jira support tickets from incoming emails.

**Trigger**: Monitors support@company.com inbox every 5 minutes
**Processing**: Filters emails, checks for duplicates, formats data
**Output**: Creates Jira ticket in Support project, sends Slack notification

**Key Features**:
- Duplicate detection to prevent redundant tickets
- Priority mapping based on email subject tags
- Automatic Slack alerts to #support-ops channel
- Error handling with retry logic
```

---

### Step 5: Configure Workflow Settings

**Action**: Set workflow-level configuration

#### Essential Settings

```json
{
  "settings": {
    "executionOrder": "v1",              // Recommended: v1 (sequential)
    "saveExecutionProgress": true,       // Helps debugging
    "saveDataErrorExecution": "all",     // Save error logs
    "saveDataSuccessExecution": "none",  // Don't save success logs (saves space)
    "timezone": "America/Los_Angeles"    // User's timezone
  }
}
```

#### Settings Explained

**executionOrder**:
- `"v1"`: Sequential execution (recommended)
- `"v0"`: Legacy mode (avoid)

**saveExecutionProgress**:
- `true`: Save intermediate results during execution (useful for debugging)
- `false`: Don't save progress (faster, but harder to debug)

**saveDataErrorExecution**:
- `"all"`: Save all error data (recommended)
- `"none"`: Don't save error data

**saveDataSuccessExecution**:
- `"all"`: Save all successful execution data (can use lots of storage)
- `"none"`: Don't save successful runs (recommended for production)

**timezone**:
- User's timezone (e.g., "America/New_York", "Europe/London", "Asia/Tokyo")
- Important for scheduled workflows
- Get from user context or ask

#### Optional Settings

```json
{
  "settings": {
    "executionTimeout": 300,             // Max execution time (seconds)
    "errorWorkflow": "error_handler_id", // Fallback workflow for errors
    "saveManualExecutions": true,        // Save manual test runs
    "callerPolicy": "workflowsFromSameOwner"  // Security setting
  }
}
```

---

### Step 6: Set Workflow Activation State

**Action**: Determine if workflow should be active

**Recommendation**: Always set `active: false` initially

**Reasoning**:
- User should review before activation
- Allows testing in safe mode
- Prevents accidental triggers
- User activates manually after validation

```json
{
  "active": false
}
```

**Exception**: If deploying directly via n8n MCP, may set `true` after user confirmation

---

### Step 7: Add Metadata Fields

**Action**: Include creation and modification timestamps

```json
{
  "createdAt": "2025-01-15T10:30:00.000Z",
  "updatedAt": "2025-01-15T10:30:00.000Z"
}
```

**Note**: These are typically added by n8n server, but good to include for completeness

---

### Step 8: Assemble Complete Workflow JSON

**Action**: Combine all components into final structure

**Complete Workflow Structure**:

```json
{
  "name": "Workflow Name",
  "nodes": [
    {
      "id": "node_1",
      "name": "Node Name",
      "type": "n8n-nodes-base.nodeType",
      "typeVersion": 1,
      "position": [x, y],
      "parameters": {...},
      "credentials": {...},
      "continueOnFail": false,
      "retryOnFail": false
    },
    // ... all other nodes ...
  ],
  "connections": {
    "Node Name 1": {
      "main": [[...]]
    },
    // ... all connections ...
  },
  "settings": {
    "executionOrder": "v1",
    "saveExecutionProgress": true,
    "saveDataErrorExecution": "all",
    "saveDataSuccessExecution": "none",
    "timezone": "America/Los_Angeles"
  },
  "active": false,
  "tags": ["tag1", "tag2"],
  "createdAt": "2025-01-15T10:30:00.000Z",
  "updatedAt": "2025-01-15T10:30:00.000Z"
}
```

---

### Step 9: Validate JSON Structure

**Action**: Ensure JSON is valid and complete

**Validation Checklist**:

- [ ] **Valid JSON**: No syntax errors
- [ ] **Required fields present**:
  - `name` (string)
  - `nodes` (array, not empty)
  - `connections` (object)
- [ ] **All nodes have**:
  - Unique `id`
  - `name`, `type`, `typeVersion`
  - `position` array [x, y]
  - `parameters` object
- [ ] **Connections reference existing nodes**: All node names in connections exist in nodes array
- [ ] **No duplicate node IDs**: Each node has unique identifier
- [ ] **Settings are valid**: All setting values are correct types

---

## Complete Workflow Example

**Simple Email to Slack Workflow**:

```json
{
  "name": "Email to Slack Notification",
  "nodes": [
    {
      "id": "webhook_1",
      "name": "Webhook",
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 1,
      "position": [250, 300],
      "parameters": {
        "path": "email-notification",
        "httpMethod": "POST",
        "responseMode": "onReceived"
      }
    },
    {
      "id": "set_1",
      "name": "Format Message",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3,
      "position": [550, 300],
      "parameters": {
        "mode": "manual",
        "assignments": {
          "assignments": [
            {
              "id": "field1",
              "name": "slack_message",
              "type": "string",
              "value": "={{\"New email: \" + $json.subject + \" from \" + $json.from}}"
            }
          ]
        }
      }
    },
    {
      "id": "slack_1",
      "name": "Send to Slack",
      "type": "n8n-nodes-base.slack",
      "typeVersion": 2,
      "position": [850, 300],
      "parameters": {
        "resource": "message",
        "operation": "post",
        "channel": "#notifications",
        "text": "={{$json.slack_message}}"
      },
      "credentials": {
        "slackOAuth2Api": {
          "id": "{{SLACK_CREDENTIAL_ID}}",
          "name": "My Slack Workspace"
        }
      }
    }
  ],
  "connections": {
    "Webhook": {
      "main": [[{"node": "Format Message", "type": "main", "index": 0}]]
    },
    "Format Message": {
      "main": [[{"node": "Send to Slack", "type": "main", "index": 0}]]
    }
  },
  "settings": {
    "executionOrder": "v1",
    "saveExecutionProgress": true,
    "saveDataErrorExecution": "all",
    "saveDataSuccessExecution": "none",
    "timezone": "America/Los_Angeles"
  },
  "active": false,
  "tags": ["email", "slack", "notification", "webhook"],
  "createdAt": "2025-01-15T10:30:00.000Z",
  "updatedAt": "2025-01-15T10:30:00.000Z"
}
```

---

## Assembly Best Practices

### 1. Consistent Formatting

Use proper JSON formatting with indentation for readability

### 2. Meaningful Metadata

- Names should be descriptive
- Tags should be relevant and searchable
- Description should explain purpose clearly

### 3. Safe Defaults

- `active: false` (user manually activates)
- `saveDataErrorExecution: "all"` (capture errors)
- `saveDataSuccessExecution: "none"` (save storage)

### 4. Timezone Awareness

Always set timezone for scheduled workflows

### 5. Version Tracking

Include creation/update timestamps for version tracking

---

## Output Format

**Two Outputs**:

1. **Complete Workflow JSON File**:
   - Ready for n8n import
   - All components assembled
   - Valid JSON structure

2. **Workflow Summary**:
   ```json
   {
     "workflow_name": "Email to Slack Notification",
     "total_nodes": 3,
     "node_types": ["webhook", "set", "slack"],
     "tags": ["email", "slack", "notification"],
     "estimated_complexity": "simple",
     "ready_for_deployment": true
   }
   ```

---

## Output Checklist

Before proceeding to Phase 8, verify:

- [ ] Workflow name is descriptive and meaningful
- [ ] All nodes are included in nodes array
- [ ] All connections are in connections object
- [ ] Settings are configured appropriately
- [ ] Tags are relevant and useful
- [ ] JSON structure is valid (no syntax errors)
- [ ] Metadata is complete
- [ ] Workflow is set to inactive (active: false)
- [ ] Complete JSON is stored in Memory MCP

---

## Common Pitfalls to Avoid

1. **Missing required fields**: Forgetting name, nodes, or connections
2. **Invalid JSON**: Syntax errors, trailing commas, quotes
3. **Active by default**: Setting active: true (risky)
4. **Empty arrays**: nodes: [] or connections: {}
5. **Inconsistent node references**: Names in connections don't match node names
6. **Missing settings**: Not configuring timezone or execution order
7. **Poor naming**: Generic names like "Workflow 1"

---

## Transition to Phase 8

**When to proceed**:
- Complete workflow JSON is assembled
- JSON is valid and complete
- All metadata is generated
- Workflow is stored in Memory

**Next Phase**: Load `phase8_validation.md` to thoroughly validate the workflow.

**Handoff**: Pass complete workflow JSON to Phase 8 for comprehensive validation and optimization.
