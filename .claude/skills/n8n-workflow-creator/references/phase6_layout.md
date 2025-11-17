# Phase 6: Layout Optimization

## Purpose

Arrange nodes visually on the n8n canvas for optimal readability, maintainability, and user experience. Create clear, professional-looking workflows with logical left-to-right flow.

## Overview

This phase uses Sequential Thinking to calculate optimal node positions based on dependency graphs, execution levels, and visual design principles.

## Process Flow

```
Connections Object (Phase 5)
    ↓
Sequential Thinking MCP (Calculate layout algorithm)
    ↓
Build dependency graph
    ↓
Determine node levels (depth from trigger)
    ↓
Calculate positions (x, y coordinates)
    ↓
Optimize for clarity (minimize crossings, balance layout)
    ↓
Update position arrays in all nodes
```

## Step-by-Step Guide

### Step 1: Retrieve Nodes and Connections

**Action**: Load complete node configurations and connections

**Memory MCP Operations**:
```
Use Memory MCP to retrieve:
- All node configurations from Phase 4
- Complete connections object from Phase 5
- Flow structure type (linear/branching/parallel)
```

---

### Step 2: Build Dependency Graph

**Action**: Create a graph representation of node dependencies

**Sequential Thinking Process**:

1. **Create adjacency list**:
```
For each connection:
  Source node → [List of target nodes]
```

**Example**:
```javascript
dependencyGraph = {
  "Webhook": ["Parse Data"],
  "Parse Data": ["Check Priority"],
  "Check Priority": ["Alert Team", "Create Ticket"],
  "Alert Team": [],
  "Create Ticket": []
}
```

2. **Identify trigger node(s)**: Nodes with no incoming connections
3. **Identify terminal nodes**: Nodes with no outgoing connections

---

### Step 3: Calculate Node Levels

**Action**: Determine depth of each node from trigger (execution level)

**Algorithm**:

```
Level 0: Trigger node(s)
Level 1: Nodes directly connected to trigger
Level 2: Nodes connected to Level 1 nodes
Level N: Nodes connected to Level N-1 nodes

For branching nodes (IF, Switch):
  Both branches are same level
  Level is determined by longest path from trigger
```

**Example**:
```
Workflow: Webhook → Parse → IF → [High | Normal] → Notify

Levels:
  Level 0: Webhook
  Level 1: Parse Data
  Level 2: Check Priority (IF)
  Level 3: Alert Team, Create Ticket
  Level 4: Notify Success (if paths merge)
```

**Level Calculation Algorithm**:
```python
def calculate_levels(dependency_graph, trigger_nodes):
    levels = {}
    queue = [(node, 0) for node in trigger_nodes]  # (node, level)

    while queue:
        node, level = queue.pop(0)

        # Update level if this path is longer
        if node not in levels or level > levels[node]:
            levels[node] = level

            # Add children to queue
            for child in dependency_graph.get(node, []):
                queue.append((child, level + 1))

    return levels
```

---

### Step 4: Calculate Horizontal Positions (X Coordinates)

**Action**: Position nodes left-to-right based on execution levels

**Layout Constants**:
```javascript
const START_X = 250;                // Starting X position
const HORIZONTAL_SPACING = 300;     // Space between levels
```

**Formula**:
```
X position = START_X + (Level × HORIZONTAL_SPACING)
```

**Example**:
```
Level 0: X = 250 + (0 × 300) = 250
Level 1: X = 250 + (1 × 300) = 550
Level 2: X = 250 + (2 × 300) = 850
Level 3: X = 250 + (3 × 300) = 1150
```

**Adjustment for Complex Workflows**:
- For very wide nodes (e.g., complex Code nodes), increase spacing
- For dense workflows, reduce spacing slightly (min 250px)

---

### Step 5: Calculate Vertical Positions (Y Coordinates)

**Action**: Position nodes top-to-bottom within each level

**Layout Constants**:
```javascript
const START_Y = 300;                // Starting Y position
const VERTICAL_SPACING = 150;       // Space between nodes at same level
```

#### 5.1 Linear Flows (One Node Per Level)

**Simple case**: One node at each level
```
Y position = START_Y (all nodes aligned horizontally)
```

#### 5.2 Branching Flows (Multiple Nodes Per Level)

**Algorithm**:
```
For each level with N nodes:
  Calculate center Y position
  Distribute nodes above and below center

  If N is odd:
    Middle node at center
    Others distributed symmetrically

  If N is even:
    Nodes distributed symmetrically around center
```

**Example with IF Node (2 branches)**:
```
Level 3 has 2 nodes: Alert Team, Create Ticket

Center Y = 300
Node 1 (Alert Team):   Y = 300 - (VERTICAL_SPACING / 2) = 225
Node 2 (Create Ticket): Y = 300 + (VERTICAL_SPACING / 2) = 375
```

**Example with 3 Parallel Paths**:
```
Level 2 has 3 nodes: Email, Slack, Database

Center Y = 300
Node 1 (Email):    Y = 300 - VERTICAL_SPACING = 150
Node 2 (Slack):    Y = 300 = 300
Node 3 (Database): Y = 300 + VERTICAL_SPACING = 450
```

#### 5.3 Positioning Algorithm

```python
def calculate_vertical_positions(nodes_at_level, center_y=300, spacing=150):
    count = len(nodes_at_level)
    positions = []

    if count == 1:
        return [center_y]

    # Calculate starting Y (top-most node)
    start_y = center_y - ((count - 1) / 2) * spacing

    # Distribute nodes
    for i in range(count):
        y = start_y + (i * spacing)
        positions.append(y)

    return positions
```

---

### Step 6: Handle Special Layout Cases

#### 6.1 IF Node Branching

**Layout Pattern**:
```
                   ┌─ True Path  (Y - offset)
IF Node (center) ──┤
                   └─ False Path (Y + offset)
```

**Positioning**:
```
IF Node:    X = level × spacing,  Y = center
True Path:  X = (level+1) × spacing, Y = center - 75
False Path: X = (level+1) × spacing, Y = center + 75
```

#### 6.2 Merge Nodes

**Layout Pattern**:
```
Node A (Y1) ─┐
Node B (Y2) ─┤─ Merge (center)
Node C (Y3) ─┘
```

**Positioning**:
```
Merge Y = average of all input node Y positions
```

#### 6.3 Parallel Execution

**Layout Pattern**:
```
                ┌─ Process A (Y - spacing)
Source Node ───├─ Process B (Y)
                └─ Process C (Y + spacing)
```

**Spacing**: Increase VERTICAL_SPACING to 200px for clarity

#### 6.4 Error Handling Nodes

**Layout Pattern**:
```
Main Path:  Y = base position
Error Path: Y = base position + 200 (below main path)
```

**Positioning**:
```
Main Node:        X, Y = standard position
Error Handler:    X = main_x + HORIZONTAL_SPACING, Y = main_y + 200
```

---

### Step 7: Minimize Connection Crossings

**Action**: Adjust positions to reduce visual complexity

**Sequential Thinking Optimization**:

1. **Identify crossings**: Check if connections cross each other
2. **Swap node positions**: Try swapping nodes at same level
3. **Evaluate improvement**: Count crossings before/after
4. **Apply best arrangement**: Use configuration with fewest crossings

**Example**:
```
Before (2 crossings):
  A (Y=200) → C (Y=400)  \  crossing
  B (Y=400) → D (Y=200)  /  crossing

After (0 crossings):
  A (Y=200) → D (Y=200)  —  no crossing
  B (Y=400) → C (Y=400)  —  no crossing
```

---

### Step 8: Apply Positions to Nodes

**Action**: Update each node's position array

**Position Format**:
```json
{
  "position": [x, y]
}
```

**Example**:
```json
{
  "id": "webhook_1",
  "name": "Webhook",
  "type": "n8n-nodes-base.webhook",
  "position": [250, 300],  // Level 0, center
  "parameters": {...}
}
```

---

## Complete Layout Examples

### Example 1: Linear Workflow

**Workflow**: Trigger → Fetch → Transform → Save → Notify

**Positions**:
```json
[
  {"name": "Schedule Trigger", "position": [250, 300]},   // Level 0
  {"name": "Fetch Data",       "position": [550, 300]},   // Level 1
  {"name": "Transform",        "position": [850, 300]},   // Level 2
  {"name": "Save to DB",       "position": [1150, 300]},  // Level 3
  {"name": "Notify Success",   "position": [1450, 300]}   // Level 4
]
```

### Example 2: Branching Workflow

**Workflow**: Webhook → Parse → IF → [High | Normal]

**Positions**:
```json
[
  {"name": "Webhook",       "position": [250, 300]},   // Level 0, center
  {"name": "Parse Email",   "position": [550, 300]},   // Level 1, center
  {"name": "Check Priority","position": [850, 300]},   // Level 2, IF node
  {"name": "Alert Team",    "position": [1150, 225]},  // Level 3, true path (above)
  {"name": "Create Ticket", "position": [1150, 375]}   // Level 3, false path (below)
]
```

### Example 3: Parallel Execution with Merge

**Workflow**: Trigger → [Email + Slack + DB] → Merge → Log

**Positions**:
```json
[
  {"name": "Schedule",    "position": [250, 300]},    // Level 0
  {"name": "Fetch Data",  "position": [550, 300]},    // Level 1
  {"name": "Send Email",  "position": [850, 150]},    // Level 2, top
  {"name": "Post Slack",  "position": [850, 300]},    // Level 2, middle
  {"name": "Save to DB",  "position": [850, 450]},    // Level 2, bottom
  {"name": "Merge",       "position": [1150, 300]},   // Level 3, center
  {"name": "Log Summary", "position": [1450, 300]}    // Level 4
]
```

### Example 4: Complex Workflow with Error Handling

**Workflow**: Webhook → Parse → Check → IF → [Skip | Process → Create → Notify]
              with error handling on Create

**Positions**:
```json
[
  {"name": "Webhook",         "position": [250, 300]},    // Level 0
  {"name": "Parse Data",      "position": [550, 300]},    // Level 1
  {"name": "Check Duplicate", "position": [850, 300]},    // Level 2
  {"name": "IF Duplicate",    "position": [1150, 300]},   // Level 3, IF
  {"name": "Log Skipped",     "position": [1450, 225]},   // Level 4, true (skip)
  {"name": "Process Data",    "position": [1450, 375]},   // Level 4, false (process)
  {"name": "Create Record",   "position": [1750, 375]},   // Level 5, main path
  {"name": "Notify Success",  "position": [2050, 375]},   // Level 6, success
  {"name": "Notify Error",    "position": [2050, 575]}    // Level 6, error (below)
]
```

---

## Layout Best Practices

### 1. Consistent Spacing

Use standard spacing values throughout:
- Horizontal: 300px between levels
- Vertical: 150px between parallel nodes
- Error paths: 200px below main path

### 2. Left-to-Right Flow

Always flow from left to right (trigger on left, outputs on right)

### 3. Center Alignment

Align main flow at Y = 300 (center of typical canvas view)

### 4. Visual Balance

Distribute parallel paths symmetrically around center:
```
    Top path (Y = 150)
    Middle path (Y = 300)
    Bottom path (Y = 450)
```

### 5. Clear Branching

Make IF/Switch branches visually distinct with vertical separation

### 6. Error Path Separation

Place error handling nodes clearly below main execution path

### 7. Avoid Overlaps

Ensure minimum spacing:
- No nodes closer than 150px vertically
- No nodes closer than 250px horizontally

---

## Layout Validation Checklist

Before proceeding to Phase 7, verify:

- [ ] All nodes have position arrays [x, y]
- [ ] No nodes overlap (check minimum distances)
- [ ] Flow proceeds left-to-right consistently
- [ ] Branches are visually separated
- [ ] Parallel paths are evenly distributed
- [ ] Error paths are clearly distinguished
- [ ] Entire workflow fits reasonably on canvas (consider zoom)
- [ ] Connection lines are clear (minimal crossings)
- [ ] Visual balance and symmetry achieved

---

## Common Pitfalls to Avoid

1. **Overlapping nodes**: Insufficient spacing causing visual collision
2. **Inconsistent alignment**: Some nodes at Y=300, others at Y=305
3. **Too cramped**: Using spacing < 150px vertically or < 250px horizontally
4. **Too spread out**: Workflow spans excessively wide canvas
5. **Backwards flow**: Right-to-left connections (confusing)
6. **Random positioning**: No logical structure to placement
7. **Crossing connections everywhere**: Poor vertical distribution

---

## Advanced Layout Techniques

### Technique 1: Dynamic Spacing

Adjust spacing based on workflow complexity:
```
Simple (< 5 nodes):  Use standard spacing
Medium (5-15 nodes): Reduce spacing by 10%
Complex (15+ nodes): Reduce spacing by 20%, or split into sub-workflows
```

### Technique 2: Visual Grouping

Group related nodes closer together:
```
Error handling group: Reduce internal spacing to 100px
Parallel processing group: Standard spacing
```

### Technique 3: Swim Lanes

For complex workflows, create horizontal "lanes":
```
Lane 1 (Y=150):  High priority path
Lane 2 (Y=300):  Normal processing
Lane 3 (Y=450):  Logging/monitoring
Lane 4 (Y=600):  Error handling
```

---

## Output Format

**Updated Node Objects with Positions**:

```json
[
  {
    "id": "webhook_1",
    "name": "Webhook",
    "type": "n8n-nodes-base.webhook",
    "typeVersion": 1,
    "position": [250, 300],
    "parameters": {...}
  },
  {
    "id": "parse_1",
    "name": "Parse Data",
    "type": "n8n-nodes-base.code",
    "typeVersion": 1,
    "position": [550, 300],
    "parameters": {...}
  }
  // ... all other nodes with positions ...
]
```

---

## Transition to Phase 7

**When to proceed**:
- All nodes have calculated positions
- Layout is validated and optimized
- No overlaps or visual issues
- Positions are updated in node objects

**Next Phase**: Load `phase7_assembly.md` to combine all components into final workflow JSON.

**Handoff**: Pass complete node objects with positions and connections object to Phase 7 for final assembly.
