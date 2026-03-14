# VectorShift Frontend Technical Assessment - Project Analysis

## Project Overview

This is a **VectorShift Frontend Technical Assessment** project that implements a visual pipeline builder application. It's a React-based drag-and-drop interface for creating node-based workflows, similar to tools like n8n, Zapier, or Node-RED.

### Technology Stack

**Frontend:**
- React 18.2.0
- ReactFlow 11.8.3 (for node-based visual editor)
- Zustand (for state management)
- Create React App (bootstrapped)

**Backend:**
- Python with FastAPI
- Uvicorn (ASGI server)

### Current Application Structure

```
/drive-download-20260313T150513Z-1-001/
├── frontend/
│   ├── src/
│   │   ├── nodes/
│   │   │   ├── inputNode.js    - Input node with name/type fields
│   │   │   ├── outputNode.js   - Output node with name/type fields
│   │   │   ├── llmNode.js      - LLM node with system/prompt inputs
│   │   │   └── textNode.js     - Text node with text input field
│   │   ├── App.js              - Main app component
│   │   ├── ui.js               - ReactFlow canvas implementation
│   │   ├── toolbar.js          - Draggable node toolbar
│   │   ├── draggableNode.js    - Reusable draggable node component
│   │   ├── submit.js           - Submit button (needs implementation)
│   │   ├── store.js            - Zustand state management
│   │   └── index.js            - App entry point
│   └── package.json
└── backend/
    └── main.py                  - FastAPI backend (minimal implementation)
```

## What This Project Does

The application is a **visual pipeline builder** that allows users to:

1. **Drag and drop nodes** from a toolbar onto a canvas
2. **Connect nodes** together to form data pipelines
3. **Configure nodes** with different parameters (names, types, text)
4. **Submit pipelines** to a backend for validation and analysis

### Current Features

- ✅ Drag-and-drop node creation
- ✅ Node connection with animated edges
- ✅ 4 node types: Input, Output, LLM, Text
- ✅ Basic state management with Zustand
- ✅ ReactFlow controls (zoom, pan, minimap)
- ✅ Basic FastAPI backend with ping endpoint

### Missing Features (To Be Implemented)

- ❌ Node abstraction system (too much code duplication)
- ❌ Modern styling (currently very basic borders)
- ❌ Dynamic text node resizing
- ❌ Variable detection with dynamic handles in text nodes
- ❌ Backend integration for pipeline analysis
- ❌ DAG (Directed Acyclic Graph) validation

## Assessment Requirements Breakdown

### Part 1: Node Abstraction (HIGH PRIORITY)

**Problem:** Each node file contains duplicated code. Creating new nodes requires copying existing ones.

**Solution Required:**
- Create a reusable abstraction/base component for nodes
- Extract common patterns (handles, styling, state management)
- Make it easy to define new nodes with minimal code

**Deliverable:**
- Abstract node component/system
- 5 new custom nodes demonstrating the abstraction

**Suggested New Nodes:**
1. **Filter Node** - Filters data based on conditions
2. **Transform Node** - Transforms/maps data
3. **API Node** - Makes API calls
4. **Conditional Node** - If/else branching
5. **Aggregator Node** - Combines multiple inputs

### Part 2: Styling (MEDIUM PRIORITY)

**Problem:** No significant styling applied. Very basic appearance with black borders.

**Solution Required:**
- Create unified, appealing design system
- Style all components (toolbar, nodes, canvas, submit button)
- Consider VectorShift's style or create original design

**Deliverable:**
- Modern, professional UI with consistent styling
- Could use CSS-in-JS, styled-components, Tailwind, or Material-UI

**Styling Ideas:**
- Color scheme with primary/secondary colors
- Rounded corners, shadows, gradients
- Hover effects and transitions
- Proper spacing and typography
- Node type differentiation with colors/icons

### Part 3: Text Node Logic (HIGH PRIORITY)

**Problem:** Text node has fixed size and no variable detection.

**Solution Required:**

1. **Dynamic Resizing:**
   - Text node width/height should grow with content
   - Use textarea instead of input
   - Auto-resize based on content

2. **Variable Detection:**
   - Detect `{{ variableName }}` patterns in text
   - Extract valid JavaScript variable names
   - Create dynamic left-side Handles for each variable
   - Update handles when text changes

**Example:**
```
Text: "Hello {{ username }}, your age is {{ age }}"
Result: Creates two left handles: "username" and "age"
```

**Deliverable:**
- Auto-resizing text node
- Dynamic handle creation based on {{ variable }} patterns

### Part 4: Backend Integration (HIGH PRIORITY)

**Problem:** No connection between frontend and backend.

**Solution Required:**

**Frontend (submit.js):**
- Get nodes and edges from store
- Send POST request to `http://localhost:8000/pipelines/parse`
- Display response in alert

**Backend (main.py):**
- Change endpoint to POST (currently GET)
- Parse nodes and edges from request body
- Calculate: `num_nodes`, `num_edges`
- Implement DAG detection algorithm
- Return: `{num_nodes: int, num_edges: int, is_dag: bool}`

**DAG Detection Algorithm:**
- Use topological sort or DFS cycle detection
- Graph is DAG if no cycles exist

**Deliverable:**
- Working submit button that sends pipeline data
- Backend endpoint that analyzes pipeline
- User-friendly alert showing results

## Implementation Plan

### Phase 1: Setup & Understanding (Day 1)
- [x] Analyze project structure
- [x] Document requirements
- [ ] Install dependencies and run both frontend/backend
- [ ] Test current functionality

### Phase 2: Node Abstraction (Day 1-2)
- [ ] Create BaseNode component with common logic
- [ ] Refactor existing nodes to use abstraction
- [ ] Create 5 new node types
- [ ] Update toolbar and store to support new nodes
- [ ] Test node creation and connections

### Phase 3: Styling (Day 2)
- [ ] Design color scheme and style guide
- [ ] Style toolbar and draggable nodes
- [ ] Style canvas and controls
- [ ] Style all node types consistently
- [ ] Style submit button
- [ ] Add responsive design considerations

### Phase 4: Text Node Enhancements (Day 2-3)
- [ ] Implement auto-resizing textarea
- [ ] Create variable extraction regex/parser
- [ ] Implement dynamic handle creation
- [ ] Update store to handle dynamic handles
- [ ] Test variable detection with various inputs

### Phase 5: Backend Integration (Day 3)
- [ ] Install FastAPI dependencies (if needed)
- [ ] Update backend endpoint to POST
- [ ] Implement node/edge counting
- [ ] Implement DAG detection algorithm
- [ ] Test backend with sample data
- [ ] Update frontend submit button
- [ ] Implement fetch/axios call
- [ ] Create alert component/modal
- [ ] Test end-to-end integration

### Phase 6: Testing & Polish (Day 3-4)
- [ ] Test all node types
- [ ] Test edge cases (empty pipeline, cycles, etc.)
- [ ] Fix bugs
- [ ] Code cleanup
- [ ] Documentation updates
- [ ] Final review

## Technical Considerations

### Node Abstraction Approach

**Option 1: Configuration-Based**
```javascript
const nodeConfig = {
  title: "Input",
  fields: [
    { name: "inputName", type: "text", label: "Name" },
    { name: "inputType", type: "select", options: ["Text", "File"] }
  ],
  handles: [
    { type: "source", position: "right", id: "value" }
  ]
};
```

**Option 2: Component Composition**
```javascript
<BaseNode title="Input">
  <NodeField name="inputName" type="text" label="Name" />
  <NodeHandle type="source" position="right" />
</BaseNode>
```

**Option 3: Higher-Order Component**
```javascript
export const InputNode = createNode({
  title: "Input",
  fields: [...],
  handles: [...]
});
```

### DAG Detection Algorithm

**Kahn's Algorithm (Topological Sort):**
1. Calculate in-degree for each node
2. Start with nodes having in-degree 0
3. Remove nodes and edges iteratively
4. If all nodes removed → DAG, else cycle exists

**DFS Approach:**
1. Use DFS with three colors (white, gray, black)
2. Gray node revisited during DFS → cycle found
3. No cycles → is DAG

### Variable Extraction Regex

```javascript
const regex = /\{\{\s*([a-zA-Z_$][a-zA-Z0-9_$]*)\s*\}\}/g;
```

This matches:
- `{{ variable }}`
- `{{name}}`
- `{{ _private }}`
- `{{ $jquery }}`

## Expected Outcomes

After completing this assessment, the application will:

1. ✅ Have a flexible, maintainable node system
2. ✅ Support 9+ node types with minimal code per node
3. ✅ Have professional, modern styling
4. ✅ Support dynamic text nodes with variable handles
5. ✅ Validate pipelines as DAGs
6. ✅ Provide user feedback on pipeline structure
7. ✅ Have full frontend-backend integration

## Development Commands

**Frontend:**
```bash
cd /home/runner/work/assesment/assesment/drive-download-20260313T150513Z-1-001/frontend
npm install
npm start  # Runs on http://localhost:3000
```

**Backend:**
```bash
cd /home/runner/work/assesment/assesment/drive-download-20260313T150513Z-1-001/backend
pip install fastapi uvicorn
uvicorn main:app --reload  # Runs on http://localhost:8000
```

## Success Criteria

- [ ] All 4 parts of the assessment completed
- [ ] Code is clean, maintainable, and well-structured
- [ ] Application runs without errors
- [ ] All features work as specified
- [ ] Styling is modern and consistent
- [ ] Node abstraction demonstrates flexibility
- [ ] Backend correctly identifies DAGs

## Estimated Effort

- **Node Abstraction:** 4-6 hours
- **Styling:** 3-4 hours
- **Text Node Logic:** 3-4 hours
- **Backend Integration:** 2-3 hours
- **Testing & Polish:** 2-3 hours

**Total:** 14-20 hours (2-3 days of focused work)
