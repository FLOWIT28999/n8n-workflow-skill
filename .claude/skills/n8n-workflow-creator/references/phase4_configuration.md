# Phase 4: Node Configuration

## Purpose

Configure each node's parameters accurately and completely, ensuring all required fields are set, dependencies are handled, and expressions are properly formatted.

## Overview

This phase transforms selected nodes into fully configured node objects ready for workflow assembly. It uses n8n MCP for parameter validation, Tavily for complex configuration examples, Memory for reusing successful settings, and Sequential Thinking for logical validation.

## Process Flow

```
Node Chain (Phase 3)
    ↓
n8n MCP (Parameter discovery & validation)
    ↓
Tavily MCP (Configuration examples for complex nodes)
    ↓
Memory MCP (Reuse successful configurations)
    ↓
Sequential Thinking MCP (Validate configuration logic)
    ↓
Complete Node Objects with Parameters
```

## Step-by-Step Guide

### Step 1: Retrieve Node Chain from Memory

**Action**: Load the selected nodes from Phase 3

**Memory MCP Operations**:
```
Use Memory MCP to retrieve:
- Complete node chain with selected nodeTypes
- Task descriptions and requirements
- User preferences for configurations
- Previously successful configurations for these nodes
```

---

### Step 2: Discover Required Parameters with n8n MCP

**Action**: For each node, identify required and optional parameters

#### 2.1 Check Minimal Requirements

```
validate_node_minimal({
  nodeType: "n8n-nodes-base.slack",
  config: {}
})
```

**Returns**:
- List of absolutely required fields
- Validation errors for missing required fields
- Minimum viable configuration

**Example Response**:
```json
{
  "valid": false,
  "errors": [
    "Missing required field: resource",
    "Missing required field: operation",
    "Missing required field: channel"
  ],
  "required_fields": ["resource", "operation", "channel"]
}
```

#### 2.2 Analyze Property Dependencies

```
get_property_dependencies({
  nodeType: "n8n-nodes-base.httpRequest"
})
```

**Returns**:
- Which fields affect other fields' visibility
- Conditional field requirements
- Field dependency chains

**Example Response**:
```json
{
  "dependencies": [
    {
      "field": "sendBody",
      "affects": ["body", "bodyParameters"],
      "condition": "When sendBody=true, body fields become visible and required"
    },
    {
      "field": "authentication",
      "affects": ["credentials"],
      "condition": "Different auth types require different credential structures"
    }
  ]
}
```

---

### Step 3: Build Base Configuration

**Action**: Create initial configuration for each node

#### 3.1 Essential Node Fields

Every node object must have:

```json
{
  "id": "unique_node_id",                  // UUID or sequential ID
  "name": "Human Readable Name",            // Display name in n8n editor
  "type": "n8n-nodes-base.nodeName",       // Full node type
  "typeVersion": 1,                         // Node version (check latest)
  "position": [x, y],                       // Canvas position (set in Phase 6)
  "parameters": {}                          // Node-specific configuration
}
```

#### 3.2 Optional Fields

```json
{
  "credentials": {                          // If authentication needed
    "credentialName": {
      "id": "credential_id",
      "name": "My Credential Name"
    }
  },
  "continueOnFail": false,                  // Continue if this node fails
  "retryOnFail": false,                     // Retry on failure
  "maxTries": 3,                           // Max retry attempts (if retryOnFail=true)
  "waitBetweenTries": 1000,                // Wait time between retries (ms)
  "disabled": false,                        // Node is disabled
  "notes": "Optional description"           // Developer notes
}
```

---

### Step 4: Configure Node Parameters

**Action**: Set all required and relevant optional parameters

#### 4.1 Parameter Types and Formats

**String Parameters**:
```json
{
  "parameters": {
    "fieldName": "string value"
  }
}
```

**Number Parameters**:
```json
{
  "parameters": {
    "timeout": 3000,
    "limit": 10
  }
}
```

**Boolean Parameters**:
```json
{
  "parameters": {
    "sendHeaders": true,
    "resolveData": false
  }
}
```

**Array Parameters**:
```json
{
  "parameters": {
    "options": ["option1", "option2"]
  }
}
```

**Object Parameters**:
```json
{
  "parameters": {
    "additionalFields": {
      "field1": "value1",
      "field2": "value2"
    }
  }
}
```

#### 4.2 n8n Expression Syntax

**Basic Expression**:
```javascript
// Access current item JSON data
"={{$json.fieldName}}"

// Example:
{
  "parameters": {
    "message": "={{$json.email_subject}}"
  }
}
```

**Reference Previous Node**:
```javascript
// Access data from specific node
"={{$node['Node Name'].json.field}}"

// Example:
{
  "parameters": {
    "ticket_id": "={{$node['Jira Search'].json.id}}"
  }
}
```

**Use Functions**:
```javascript
// JavaScript functions in expressions
"={{new Date().toISOString()}}"
"={{Math.round($json.value * 100)}}"
"={{$json.name.toUpperCase()}}"

// Example:
{
  "parameters": {
    "timestamp": "={{new Date().toISOString()}}",
    "total": "={{$json.price * $json.quantity}}"
  }
}
```

**Conditional Expressions**:
```javascript
// Ternary operator
"={{$json.status === 'active' ? 'yes' : 'no'}}"

// Example:
{
  "parameters": {
    "priority": "={{$json.urgent ? 'High' : 'Normal'}}"
  }
}
```

**Important Expression Rules**:
- Always start with `={{` and end with `}}`
- No `{{` without `=` prefix (that's the old format)
- Can mix static text and expressions: `"User {{$json.name}} logged in"` → `"={{\"User \" + $json.name + \" logged in\"}}"`

---

### Step 5: Handle Authentication

**Action**: Configure credentials for nodes that require authentication

#### 5.1 Identify Required Credentials

Check node documentation for authentication requirements:

```
get_node_essentials({
  nodeType: "n8n-nodes-base.slack",
  includeExamples: true
})
```

Look for `credentials` section in response.

#### 5.2 Document Required Credentials

**Don't hardcode credentials in workflow JSON**. Instead:

1. **Document what credentials are needed**:
```json
{
  "required_credentials": [
    {
      "node_name": "Slack",
      "credential_type": "slackOAuth2Api",
      "credential_name": "My Slack Account",
      "required_scopes": ["chat:write", "channels:read"],
      "setup_instructions": "Create Slack app at api.slack.com/apps"
    }
  ]
}
```

2. **Use placeholder structure in node**:
```json
{
  "credentials": {
    "slackOAuth2Api": {
      "id": "{{CREDENTIAL_ID_TO_BE_SET}}",
      "name": "My Slack Account"
    }
  }
}
```

3. **Provide setup documentation** (in Phase 9 output)

---

### Step 6: Use Tavily for Complex Configuration Examples

**Action**: For complex nodes, search for real-world configuration examples

**When to use Tavily**:
- Node has many parameters (>10)
- Configuration is complex (nested objects, arrays)
- Official documentation is unclear
- User is unfamiliar with the node

**Search Strategy**:
```
Tavily Search Queries:
- "n8n [node-name] configuration example 2025"
- "n8n [service] node setup guide"
- "n8n [node-name] parameters explained"
- "n8n [service] integration best practices"
```

**What to extract from examples**:
- Common parameter values
- Required vs optional fields
- Typical use case configurations
- Expression patterns
- Error handling approaches

---

### Step 7: Apply Memory MCP for Successful Configurations

**Action**: Reuse configurations that worked well for this user

**Memory MCP Operations**:

```
Use Memory MCP to search for:
- "Previous successful configurations for [nodeType]"
- "User's default settings for [service]"
- "Common parameter values for [user]"
```

**What to reuse**:
- Authentication credentials (reference by name)
- Common timeout values
- Preferred data formats
- Error handling settings
- Notification channels

**Example**:
If user always sends Slack notifications to `#alerts` channel, reuse that default.

---

### Step 8: Validate Configuration with n8n MCP

**Action**: Verify each node's configuration is valid

```
validate_node_operation({
  nodeType: "n8n-nodes-base.slack",
  config: {
    "resource": "message",
    "operation": "post",
    "channel": "#alerts",
    "text": "={{$json.message}}"
  },
  profile: "ai-friendly"
})
```

**Validation Profiles**:
- **minimal**: Basic validation only
- **runtime**: Validation for execution (recommended)
- **ai-friendly**: Optimized for AI-generated configs
- **strict**: Strictest validation

**Returns**:
```json
{
  "valid": true,
  "errors": [],
  "warnings": [
    "Consider adding 'attachments' for richer formatting"
  ],
  "suggestions": [
    "Use 'blocks' for better message layout"
  ]
}
```

**If validation fails**:
1. Review error messages
2. Check property dependencies
3. Verify expression syntax
4. Consult node documentation
5. Fix and re-validate

---

### Step 9: Verify Logic with Sequential Thinking MCP

**Action**: Ensure configuration makes logical sense

**Sequential Thinking Tasks**:

1. **Cross-Node Consistency**:
   - Do output fields from one node match input expectations of the next?
   - Are data types compatible?
   - Are field names correctly referenced?

2. **Expression Validation**:
   - Are all referenced fields actually available?
   - Are expressions syntactically correct?
   - Are data transformations logical?

3. **Business Logic Validation**:
   - Does the configuration achieve the intended outcome?
   - Are edge cases handled?
   - Is error handling appropriate?

---

## Complete Node Configuration Example

**Task**: "Send Slack notification with email subject and sender"

**Node Type**: `n8n-nodes-base.slack`

**Complete Configuration**:

```json
{
  "id": "slack_notification_1",
  "name": "Slack Notification",
  "type": "n8n-nodes-base.slack",
  "typeVersion": 2,
  "position": [1200, 300],
  "parameters": {
    "resource": "message",
    "operation": "post",
    "channel": "#support-ops",
    "text": "={{\"New support email from \" + $json.from + \": \" + $json.subject}}",
    "attachments": [],
    "otherOptions": {}
  },
  "credentials": {
    "slackOAuth2Api": {
      "id": "{{CREDENTIAL_ID}}",
      "name": "My Slack Workspace"
    }
  },
  "continueOnFail": false,
  "retryOnFail": true,
  "maxTries": 3,
  "waitBetweenTries": 1000,
  "notes": "Sends notification to #support-ops when new support email arrives"
}
```

---

## Common Node Configurations

### Schedule Trigger

```json
{
  "id": "schedule_1",
  "name": "Every 5 Minutes",
  "type": "n8n-nodes-base.scheduleTrigger",
  "typeVersion": 1,
  "position": [250, 300],
  "parameters": {
    "rule": {
      "interval": [
        {
          "field": "minutes",
          "minutesInterval": 5
        }
      ]
    }
  }
}
```

### Webhook Trigger

```json
{
  "id": "webhook_1",
  "name": "Webhook",
  "type": "n8n-nodes-base.webhook",
  "typeVersion": 1,
  "position": [250, 300],
  "parameters": {
    "path": "support-email",
    "httpMethod": "POST",
    "responseMode": "responseNode",
    "options": {}
  }
}
```

### HTTP Request

```json
{
  "id": "http_1",
  "name": "API Request",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4,
  "position": [550, 300],
  "parameters": {
    "method": "POST",
    "url": "https://api.example.com/data",
    "authentication": "genericCredentialType",
    "sendHeaders": true,
    "headerParameters": {
      "parameters": [
        {
          "name": "Content-Type",
          "value": "application/json"
        }
      ]
    },
    "sendBody": true,
    "bodyParameters": {
      "parameters": [
        {
          "name": "data",
          "value": "={{$json}}"
        }
      ]
    },
    "options": {
      "timeout": 10000
    }
  }
}
```

### IF Node (Conditional Logic)

```json
{
  "id": "if_1",
  "name": "Check Status",
  "type": "n8n-nodes-base.if",
  "typeVersion": 1,
  "position": [550, 300],
  "parameters": {
    "conditions": {
      "string": [
        {
          "value1": "={{$json.status}}",
          "operation": "equal",
          "value2": "active"
        }
      ]
    }
  }
}
```

### Code Node (JavaScript)

```json
{
  "id": "code_1",
  "name": "Transform Data",
  "type": "n8n-nodes-base.code",
  "typeVersion": 1,
  "position": [550, 300],
  "parameters": {
    "mode": "runOnceForAllItems",
    "jsCode": "// Access input data\nconst items = $input.all();\n\n// Transform\nconst transformed = items.map(item => ({\n  json: {\n    id: item.json.id,\n    name: item.json.name.toUpperCase(),\n    timestamp: new Date().toISOString()\n  }\n}));\n\nreturn transformed;"
  }
}
```

### Set Node (Simple Transformation)

```json
{
  "id": "set_1",
  "name": "Set Fields",
  "type": "n8n-nodes-base.set",
  "typeVersion": 3,
  "position": [550, 300],
  "parameters": {
    "mode": "manual",
    "duplicateItem": false,
    "assignments": {
      "assignments": [
        {
          "id": "field1",
          "name": "ticket_title",
          "type": "string",
          "value": "={{$json.email_subject}}"
        },
        {
          "id": "field2",
          "name": "ticket_description",
          "type": "string",
          "value": "={{$json.email_body}}"
        },
        {
          "id": "field3",
          "name": "created_at",
          "type": "string",
          "value": "={{new Date().toISOString()}}"
        }
      ]
    }
  }
}
```

---

## Configuration Best Practices

### 1. Use Meaningful Node Names

**Good**:
- "Check if Ticket Exists"
- "Format Email for Jira"
- "Send Success Notification"

**Bad**:
- "IF"
- "Code"
- "HTTP Request 1"

### 2. Add Notes for Complex Logic

```json
{
  "notes": "Searches Jira for existing tickets using JQL: description ~ 'message_id:{email_id}'. Returns empty array if no match."
}
```

### 3. Set Appropriate Timeouts

```json
{
  "parameters": {
    "options": {
      "timeout": 30000  // 30 seconds for slow APIs
    }
  }
}
```

### 4. Enable Retry for Critical Nodes

```json
{
  "retryOnFail": true,
  "maxTries": 3,
  "waitBetweenTries": 2000
}
```

### 5. Use continueOnFail Carefully

```json
{
  "continueOnFail": true  // Only for non-critical nodes (logging, notifications)
}
```

**When to use**:
- Optional notifications
- Non-critical logging
- Fallback operations

**When NOT to use**:
- Data creation/modification
- Critical business logic
- Authentication steps

---

## Output Checklist

Before proceeding to Phase 5, verify:

- [ ] All nodes have complete configurations
- [ ] Required parameters are set for every node
- [ ] Expressions use correct n8n syntax (={{...}})
- [ ] Referenced fields actually exist in data flow
- [ ] Authentication is documented (not hardcoded)
- [ ] Node validations pass (validate_node_operation)
- [ ] Timeout and retry settings are appropriate
- [ ] Node names are descriptive
- [ ] Complex logic has notes/comments
- [ ] Configuration is stored in Memory MCP

---

## Common Pitfalls to Avoid

1. **Old expression syntax**: Using `{{...}}` instead of `={{...}}`
2. **Missing required fields**: Not checking validate_node_minimal
3. **Hardcoded credentials**: Never put credentials in JSON
4. **Incorrect data references**: `$json.field` when field doesn't exist
5. **Wrong typeVersion**: Using outdated node versions
6. **Ignoring property dependencies**: Setting fields without checking dependencies
7. **No error handling**: Not setting retry or continueOnFail
8. **Poor node naming**: Generic names that don't describe purpose

---

## Transition to Phase 5

**When to proceed**:
- All nodes are fully configured
- All validations pass
- Credentials are documented
- Configuration is stored in Memory

**Next Phase**: Load `phase5_connections.md` to connect nodes and create data flow.

**Handoff**: Pass complete node configurations to Phase 5 for connection building.
