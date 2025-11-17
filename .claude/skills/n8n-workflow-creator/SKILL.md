---
name: n8n-workflow-creator
description: Comprehensive system for automatically creating fully functional n8n workflows from natural language requests through a structured 9-phase process. Use this skill when users request to create, design, build, or automate n8n workflows, integrations, or AI agent workflows. Leverages 4 MCP tools (Memory, Sequential Thinking, Tavily, n8n) for systematic workflow generation.
---

# n8n Workflow Creator

A comprehensive system that transforms natural language requests into fully functional n8n workflows through a systematic 9-phase process, combining Memory MCP (context management), Sequential Thinking MCP (strategic planning), Tavily MCP (information gathering), and n8n MCP (workflow execution).

## When to use this skill

Use this skill when users request:
- "Create an n8n workflow that..."
- "Build automation for..."
- "Design a workflow to integrate X with Y"
- "Set up an AI agent workflow"
- "Automate the process of..."
- Converting manual processes to n8n workflows
- Building complex multi-service integrations

## System Overview

### Core Characteristics

- **Natural Language Input**: Accept user requests in plain language
- **9-Phase Systematic Process**: Structured approach from requirements to deployment
- **4-MCP Collaboration**: Memory, Sequential Thinking, Tavily, and n8n MCPs working together
- **Automatic Validation & Optimization**: Built-in quality assurance
- **Production-Ready Output**: Immediately usable workflows with documentation

### Example Usage

**Input**: "Create a workflow that receives Slack messages and saves them to Google Sheets"

**Output**: Complete n8n workflow JSON + configuration guide + documentation

## The 9-Phase Process

```
[Phase 1] Requirements Understanding & Clarification
    ↓
[Phase 2] Task Decomposition
    ↓
[Phase 3] Node Matching
    ↓
[Phase 4] Node Configuration
    ↓
[Phase 5] Connection Building
    ↓
[Phase 6] Layout Optimization
    ↓
[Phase 7] Workflow Assembly
    ↓
[Phase 8] Validation & Optimization
    ↓
[Phase 9] Output Generation
```

## MCP Tool Roles

### Memory MCP - The Memory Expert
- Remembers user patterns and preferences
- Manages success/failure history
- Reuses validated configurations
- Stores requirements across phases

### Sequential Thinking MCP - The Strategist
- Evaluates complexity and breaks down tasks
- Analyzes dependencies
- Determines execution order
- Validates logical consistency

### Tavily MCP - The Information Gatherer
- Searches latest n8n documentation
- Collects community best practices
- Verifies API changes
- Finds real-world examples

### n8n MCP - The Executor
- Searches and retrieves node information
- Validates parameters
- Creates and validates workflows
- Manages workflow lifecycle

## How to use this skill

### Initial Workflow Complexity Assessment

Before starting, assess the workflow complexity:

- **Simple**: Single trigger, 2-5 nodes, basic logic (linear flow)
- **Medium**: Multiple conditions, 5-15 nodes, data transformation
- **Complex**: Multiple triggers, 15+ nodes, AI integration, advanced logic, branching

For simple workflows, some phases can be streamlined. For complex workflows, follow all phases meticulously.

### Progressive Loading Strategy

**Core principle**: Load only what's needed for the current phase to maintain context efficiency.

**Always in context (SKILL.md)**:
- 9-Phase overview and structure
- MCP tool integration patterns
- Phase transition guidelines

**Load on phase entry**:
- Phase-specific reference file (e.g., `references/phase1_requirements.md`)

**Load on demand**:
- Supporting references only when needed
- Error handling guides when issues arise

## Phase Execution Guide

### Phase 1: Requirements Understanding & Clarification

**Load before starting**: [📋 Phase 1 Guide](./references/phase1_requirements.md)

**Purpose**: Transform ambiguous natural language requests into structured requirements

**Key MCP Tools**:
- **Memory MCP**: Retrieve user history, preferences, and past patterns
- **Sequential Thinking MCP**: Generate clarification questions, evaluate complexity
- **n8n MCP**: Not used in this phase

**Key Actions**:
1. Collect natural language request
2. Check user history with Memory MCP
3. Use Sequential Thinking to identify gaps and generate clarification questions
4. Create structured requirements document

**Output Format**: Structured requirements with intent, trigger, data source, processing steps, destination, and conditions

**Transition**: When requirements are clear and complete, proceed to Phase 2

---

### Phase 2: Task Decomposition

**Load before starting**: [🔨 Phase 2 Guide](./references/phase2_decomposition.md)

**Purpose**: Break down the workflow into atomic, executable tasks

**Key MCP Tools**:
- **Sequential Thinking MCP**: Primary tool for decomposition, dependency analysis
- **Memory MCP**: Search for similar patterns, suggest validated structures

**Key Actions**:
1. Decompose requirements into atomic tasks
2. Categorize tasks (Input/Transform/AI/Logic/Output)
3. Identify task dependencies
4. Design flow structure (linear/branching/parallel)
5. Set execution priority

**Output Format**: Task list with id, type, category, description, dependencies, and priority

**Transition**: When all tasks are identified and ordered, proceed to Phase 3

---

### Phase 3: Node Matching

**Load before starting**: [🔗 Phase 3 Guide](./references/phase3_node_matching.md)

**Purpose**: Select the most appropriate n8n nodes for each task

**Key MCP Tools**:
- **Tavily MCP**: Search latest n8n documentation, community examples
- **n8n MCP**: Search nodes, get node info, retrieve examples
- **Sequential Thinking MCP**: Evaluate and decide best nodes
- **Memory MCP**: Apply validated node combinations

**Key Actions**:
1. Use Tavily to gather latest node information and best practices
2. Use n8n MCP to search and retrieve node details:
   - `list_nodes({category: 'trigger', limit: 50})`
   - `search_nodes({query: "slack", includeExamples: true})`
   - `get_node_essentials({nodeType: "...", includeExamples: true})`
3. Evaluate nodes using Sequential Thinking
4. Select optimal nodes for each task

**Output Format**: Node chain with position, nodeType, role, score, and selection reason

**Transition**: When all tasks have matched nodes, proceed to Phase 4

---

### Phase 4: Node Configuration

**Load before starting**: [⚙️ Phase 4 Guide](./references/phase4_configuration.md)

**Purpose**: Configure each node's parameters accurately

**Key MCP Tools**:
- **n8n MCP**: Primary tool for parameter configuration and validation
- **Tavily MCP**: Find configuration examples for complex nodes
- **Memory MCP**: Reuse successful configurations
- **Sequential Thinking MCP**: Validate configuration logic

**Key Actions**:
1. Check required parameters: `validate_node_minimal({nodeType: "...", config: {}})`
2. Analyze property dependencies: `get_property_dependencies({nodeType: "..."})`
3. Apply default values and user-specific settings
4. Validate configuration: `validate_node_operation({nodeType: "...", config: {...}, profile: "ai-friendly"})`
5. Handle credentials (document required auth)

**Output Format**: Complete node objects with id, name, type, typeVersion, position, parameters, and credentials

**Transition**: When all nodes are configured and validated, proceed to Phase 5

---

### Phase 5: Connection Building

**Load before starting**: [🔀 Phase 5 Guide](./references/phase5_connections.md)

**Purpose**: Create accurate data flow connections between nodes

**Key MCP Tools**:
- **Sequential Thinking MCP**: Design connection structure (linear/branching/parallel)
- **n8n MCP**: Create connections object, validate connections
- **Memory MCP**: Apply validated connection patterns

**Key Actions**:
1. Design connection logic (linear, branching, parallel, error handling)
2. Create connections object following n8n format
3. Implement error handling connections
4. Handle conditional branching (IF nodes)
5. Validate connection structure

**Output Format**: Connections object with proper main/error routing

**Transition**: When all connections are defined, proceed to Phase 6

---

### Phase 6: Layout Optimization

**Load before starting**: [📐 Phase 6 Guide](./references/phase6_layout.md)

**Purpose**: Arrange nodes visually for optimal readability

**Key MCP Tools**:
- **Sequential Thinking MCP**: Calculate layout algorithm, minimize crossings

**Key Actions**:
1. Build dependency graph and determine levels
2. Calculate horizontal positions (300px spacing)
3. Calculate vertical positions (150px spacing)
4. Handle branching layouts (IF nodes)
5. Optimize for visual clarity

**Output Format**: Position arrays [x, y] for each node

**Transition**: When layout is optimized, proceed to Phase 7

---

### Phase 7: Workflow Assembly

**Load before starting**: [🏗️ Phase 7 Guide](./references/phase7_assembly.md)

**Purpose**: Combine all components into complete workflow JSON

**Key MCP Tools**:
- **n8n MCP**: Generate final workflow JSON
- **Memory MCP**: Generate metadata, naming, tags

**Key Actions**:
1. Combine nodes, connections, and settings
2. Generate workflow name and tags
3. Configure workflow settings (executionOrder, timezone, error handling)
4. Set initial state (active: false for safety)
5. Create complete workflow JSON

**Output Format**: Complete n8n workflow JSON ready for import or deployment

**Transition**: When workflow is assembled, proceed to Phase 8

---

### Phase 8: Validation & Optimization

**Load before starting**: [✅ Phase 8 Guide](./references/phase8_validation.md)

**Purpose**: Thoroughly validate workflow functionality

**Key MCP Tools**:
- **n8n MCP**: Primary validation tool
- **Sequential Thinking MCP**: Logic verification
- **Memory MCP**: Check against failure patterns
- **Tavily MCP**: Verify against best practices

**Key Actions**:
1. Comprehensive validation:
   - `validate_workflow_connections()`: Check connection integrity
   - `validate_workflow_expressions()`: Verify expression syntax
   - `validate_workflow()`: Full node and workflow validation
2. Auto-fix common errors: `n8n_autofix_workflow({id, applyFixes: true, confidenceThreshold: "high"})`
3. Verify logical consistency with Sequential Thinking
4. Check against known failure patterns with Memory

**Output Format**: Validation results with errors, warnings, suggestions, and confidence score

**Transition**: When workflow passes validation, proceed to Phase 9

---

### Phase 9: Output Generation

**Load before starting**: [📤 Phase 9 Guide](./references/phase9_output.md)

**Purpose**: Deliver production-ready workflow with complete documentation

**Key MCP Tools**:
- **n8n MCP**: Generate JSON file, optional deployment
- **Sequential Thinking MCP**: Generate documentation
- **Memory MCP**: Store learning data for future improvements

**Key Actions**:
1. Generate final workflow JSON file
2. Create comprehensive documentation:
   - Overview and features
   - Setup instructions
   - Required credentials
   - Testing procedures
   - Troubleshooting guide
3. Store workflow creation process in Memory for future reference
4. Optional: Deploy directly to n8n instance using `n8n_create_workflow()`

**Output Format**:
- Workflow JSON file
- Markdown documentation
- Setup checklist

**Final Step**: Present complete package to user

---

## MCP Integration Patterns

### Memory MCP Usage Pattern

**Store information**:
```
Use Memory MCP to create entities and store:
- User requirements and preferences
- Selected nodes and configurations
- Design decisions and rationale
- Success/failure patterns
```

**Retrieve information**:
```
Use Memory MCP to search for:
- Similar past workflows
- Validated node combinations
- User preferences and patterns
- Previous failure causes
```

### Sequential Thinking MCP Usage Pattern

```
Use Sequential Thinking to:
1. Break down complex requirements into steps
2. Analyze task dependencies
3. Evaluate node options systematically
4. Design connection structures
5. Calculate layout positions
6. Verify logical consistency
```

### Tavily MCP Usage Pattern

```
Use Tavily to search:
- "n8n [service-name] node documentation 2025"
- "n8n [function] example workflow"
- "n8n [service1] [service2] integration best practices"
- "n8n [node-name] configuration examples"

Focus on:
- Official documentation (docs.n8n.io)
- Community examples (community.n8n.io)
- GitHub repositories
- Recent blog posts (include year)
```

### n8n MCP Usage Pattern

**Node Discovery**:
```
list_nodes({category: 'trigger', limit: 50})
search_nodes({query: "slack trigger", includeExamples: true})
get_node_essentials({nodeType: "n8n-nodes-base.slack", includeExamples: true})
get_node_documentation({nodeType: "n8n-nodes-base.slack"})
```

**Validation**:
```
validate_node_minimal({nodeType: "...", config: {}})
validate_node_operation({nodeType: "...", config: {...}, profile: "ai-friendly"})
get_property_dependencies({nodeType: "..."})
validate_workflow({workflow: {...}, options: {validateNodes: true, validateConnections: true, validateExpressions: true}})
```

**Workflow Management**:
```
n8n_create_workflow({name, nodes, connections})
n8n_validate_workflow({id})
n8n_autofix_workflow({id, applyFixes: true, confidenceThreshold: "high"})
n8n_get_workflow({id})
```

## Quick Reference

### Common Workflow Patterns

**HTTP API Integration**:
```
Webhook/Schedule Trigger → HTTP Request → Data Processing → Output
```

**AI Agent Workflow**:
```
Trigger → AI Agent → Tool Nodes → Response Formatter → Output
```

**Data Sync**:
```
Schedule Trigger → Fetch Data → Transform → Update Destination → Log
```

**Conditional Logic**:
```
Trigger → IF Node → [True Path | False Path] → Merge (optional) → Output
```

### Essential n8n Expression Syntax

```javascript
// Access current item data
={{$json.fieldName}}

// Reference previous node
={{$node["Node Name"].json.field}}

// Use functions
={{new Date().toISOString()}}

// Conditional expressions
={{$json.status === 'active' ? 'yes' : 'no'}}
```

### Phase Completion Checklist

Before transitioning to the next phase, verify:
- [ ] Current phase objectives completed
- [ ] Required outputs generated
- [ ] Data stored in Memory MCP (if needed)
- [ ] No blocking errors or issues
- [ ] Ready to load next phase guide

## Error Handling and Recovery

### If a Phase Fails

1. Review phase requirements in the reference file
2. Use Sequential Thinking to identify root cause
3. Check Memory MCP for similar past issues
4. Use Tavily to search for solutions
5. Retry with corrected approach

### Common Issues

**Phase 1-2**: Unclear requirements
- Solution: Ask more specific clarification questions

**Phase 3**: No suitable node found
- Solution: Use Tavily to search for alternative approaches or custom solutions

**Phase 4**: Configuration validation fails
- Solution: Check node documentation, use `get_property_dependencies()`

**Phase 5**: Connection logic errors
- Solution: Review workflow design, simplify structure if too complex

**Phase 8**: Validation errors
- Solution: Use `n8n_autofix_workflow()` first, then manual fixes

## Important Notes

### Security Considerations

- **Never store credentials in workflow JSON**: Document required credentials separately
- **Minimize permissions**: Request only necessary API scopes
- **Validate external inputs**: Always validate webhook and API inputs
- **Handle sensitive data carefully**: Use n8n's credential system

### Performance Optimization

- **Avoid unnecessary nodes**: Keep workflows simple when possible
- **Use pagination**: For large data sets, implement pagination
- **Set appropriate timeouts**: Configure execution timeout for long-running operations
- **Error handling**: Always implement proper error handling

### Best Practices

- **Start simple**: Begin with basic workflow, add complexity incrementally
- **Test thoroughly**: Test with various inputs before production deployment
- **Document well**: Provide clear documentation for maintenance
- **Version control**: Consider using n8n workflow versions for important changes
- **Monitor executions**: Set up monitoring and alerting for production workflows

## Reference Files

Load these reference files as needed during each phase:

### Phase Guides (Load at phase entry)
- [📋 Phase 1: Requirements](./references/phase1_requirements.md) - Requirements gathering and clarification
- [🔨 Phase 2: Decomposition](./references/phase2_decomposition.md) - Task breakdown and dependency analysis
- [🔗 Phase 3: Node Matching](./references/phase3_node_matching.md) - Node selection and evaluation
- [⚙️ Phase 4: Configuration](./references/phase4_configuration.md) - Parameter configuration and validation
- [🔀 Phase 5: Connections](./references/phase5_connections.md) - Connection structure and error handling
- [📐 Phase 6: Layout](./references/phase6_layout.md) - Visual layout optimization
- [🏗️ Phase 7: Assembly](./references/phase7_assembly.md) - Workflow composition
- [✅ Phase 8: Validation](./references/phase8_validation.md) - Comprehensive validation
- [📤 Phase 9: Output](./references/phase9_output.md) - Final output generation

### Templates (Use as starting points)
- `assets/templates/basic_workflow.json` - Simple webhook to HTTP request
- `assets/templates/ai_agent_workflow.json` - AI agent with tools

## Workflow Execution Example

**User Request**: "Create a workflow that monitors a Google Sheet for new rows and sends a Slack notification"

**Process**:
1. **Phase 1**: Clarify trigger frequency, sheet details, Slack channel
2. **Phase 2**: Break into tasks: trigger (schedule/webhook), read sheet, detect new rows, format message, send Slack
3. **Phase 3**: Select nodes: Schedule Trigger, Google Sheets, IF node, Slack
4. **Phase 4**: Configure: sheet ID, check interval, Slack credentials, message template
5. **Phase 5**: Connect: Trigger → Sheets → IF (new rows?) → Slack
6. **Phase 6**: Layout nodes left-to-right with proper spacing
7. **Phase 7**: Assemble complete workflow JSON
8. **Phase 8**: Validate all configurations and connections
9. **Phase 9**: Generate JSON file + setup documentation

**Result**: Production-ready n8n workflow with full documentation

---

To begin, collect the user's workflow requirements and load the Phase 1 reference guide to start the structured creation process.
