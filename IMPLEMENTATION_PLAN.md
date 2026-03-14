# VectorShift Assessment - Detailed Implementation Plan

## Executive Summary

This document provides a step-by-step implementation plan for completing the VectorShift Frontend Technical Assessment. The plan is organized by priority and includes specific technical approaches for each requirement.

---

## Part 1: Node Abstraction System

### Overview
Create a reusable node abstraction to eliminate code duplication and accelerate new node creation.

### Current Issues
- Each node file has ~40-50 lines with significant overlap
- Common patterns: state management, handle positioning, styling, labels
- Creating new nodes requires copy-paste-modify approach

### Proposed Solution: Configuration-Based Abstraction

Create a `BaseNode` component that accepts configuration objects.

#### File Structure
```
frontend/src/
├── nodes/
│   ├── BaseNode.js          [NEW] - Core abstraction
│   ├── nodeConfigs.js       [NEW] - Node configurations
│   ├── inputNode.js         [REFACTOR] - Use BaseNode
│   ├── outputNode.js        [REFACTOR] - Use BaseNode
│   ├── llmNode.js           [REFACTOR] - Use BaseNode
│   ├── textNode.js          [REFACTOR] - Use BaseNode
│   ├── filterNode.js        [NEW] - Demo node 1
│   ├── transformNode.js     [NEW] - Demo node 2
│   ├── apiNode.js           [NEW] - Demo node 3
│   ├── conditionalNode.js   [NEW] - Demo node 4
│   └── aggregatorNode.js    [NEW] - Demo node 5
```

### Implementation Steps

#### Step 1.1: Create BaseNode Component

**File:** `frontend/src/nodes/BaseNode.js`

```javascript
import { useState, useEffect } from 'react';
import { Handle, Position } from 'reactflow';
import { useStore } from '../store';

export const BaseNode = ({ id, data, config }) => {
  const { title, fields, handles, style } = config;
  const updateNodeField = useStore(state => state.updateNodeField);

  // Dynamic state management
  const [nodeData, setNodeData] = useState(data || {});

  // Handle field changes
  const handleFieldChange = (fieldName, value) => {
    setNodeData(prev => ({ ...prev, [fieldName]: value }));
    updateNodeField(id, fieldName, value);
  };

  // Render field based on type
  const renderField = (field) => {
    switch (field.type) {
      case 'text':
        return <input type="text" value={nodeData[field.name] || field.default || ''}
                     onChange={(e) => handleFieldChange(field.name, e.target.value)} />;
      case 'select':
        return <select value={nodeData[field.name] || field.default || ''}
                      onChange={(e) => handleFieldChange(field.name, e.target.value)}>
                 {field.options.map(opt => <option key={opt} value={opt}>{opt}</option>)}
               </select>;
      case 'textarea':
        return <textarea value={nodeData[field.name] || field.default || ''}
                        onChange={(e) => handleFieldChange(field.name, e.target.value)} />;
      default:
        return null;
    }
  };

  return (
    <div style={{ ...defaultStyle, ...style }}>
      {/* Render input handles */}
      {handles?.inputs?.map((handle, idx) => (
        <Handle
          key={`input-${idx}`}
          type="target"
          position={Position[handle.position || 'Left']}
          id={handle.id || `${id}-input-${idx}`}
          style={handle.style || {}}
        />
      ))}

      {/* Node title */}
      <div className="node-title">
        <span>{title}</span>
      </div>

      {/* Render fields */}
      <div className="node-fields">
        {fields?.map((field, idx) => (
          <div key={idx} className="node-field">
            {field.label && <label>{field.label}:</label>}
            {renderField(field)}
          </div>
        ))}
      </div>

      {/* Render output handles */}
      {handles?.outputs?.map((handle, idx) => (
        <Handle
          key={`output-${idx}`}
          type="source"
          position={Position[handle.position || 'Right']}
          id={handle.id || `${id}-output-${idx}`}
          style={handle.style || {}}
        />
      ))}
    </div>
  );
};

const defaultStyle = {
  width: 200,
  minHeight: 80,
  border: '1px solid black',
  padding: '10px',
  borderRadius: '5px',
  backgroundColor: 'white',
};
```

#### Step 1.2: Create Node Configurations

**File:** `frontend/src/nodes/nodeConfigs.js`

```javascript
export const nodeConfigs = {
  customInput: {
    title: 'Input',
    fields: [
      { name: 'inputName', type: 'text', label: 'Name', default: 'input' },
      { name: 'inputType', type: 'select', label: 'Type',
        options: ['Text', 'File'], default: 'Text' }
    ],
    handles: {
      outputs: [{ position: 'Right', id: 'value' }]
    }
  },

  customOutput: {
    title: 'Output',
    fields: [
      { name: 'outputName', type: 'text', label: 'Name', default: 'output' },
      { name: 'outputType', type: 'select', label: 'Type',
        options: ['Text', 'Image'], default: 'Text' }
    ],
    handles: {
      inputs: [{ position: 'Left', id: 'value' }]
    }
  },

  llm: {
    title: 'LLM',
    fields: [
      { name: 'model', type: 'select', label: 'Model',
        options: ['GPT-3.5', 'GPT-4', 'Claude'], default: 'GPT-3.5' }
    ],
    handles: {
      inputs: [
        { position: 'Left', id: 'system', style: { top: '33%' } },
        { position: 'Left', id: 'prompt', style: { top: '66%' } }
      ],
      outputs: [{ position: 'Right', id: 'response' }]
    }
  },

  // 5 new nodes
  filter: {
    title: 'Filter',
    fields: [
      { name: 'condition', type: 'text', label: 'Condition', default: 'value > 0' }
    ],
    handles: {
      inputs: [{ position: 'Left', id: 'input' }],
      outputs: [
        { position: 'Right', id: 'pass', style: { top: '33%' } },
        { position: 'Right', id: 'fail', style: { top: '66%' } }
      ]
    },
    style: { backgroundColor: '#e3f2fd' }
  },

  transform: {
    title: 'Transform',
    fields: [
      { name: 'operation', type: 'select', label: 'Operation',
        options: ['uppercase', 'lowercase', 'trim', 'reverse'], default: 'uppercase' }
    ],
    handles: {
      inputs: [{ position: 'Left', id: 'input' }],
      outputs: [{ position: 'Right', id: 'output' }]
    },
    style: { backgroundColor: '#f3e5f5' }
  },

  api: {
    title: 'API Call',
    fields: [
      { name: 'url', type: 'text', label: 'URL', default: 'https://api.example.com' },
      { name: 'method', type: 'select', label: 'Method',
        options: ['GET', 'POST', 'PUT', 'DELETE'], default: 'GET' }
    ],
    handles: {
      inputs: [{ position: 'Left', id: 'params' }],
      outputs: [
        { position: 'Right', id: 'response', style: { top: '33%' } },
        { position: 'Right', id: 'error', style: { top: '66%' } }
      ]
    },
    style: { backgroundColor: '#e8f5e9' }
  },

  conditional: {
    title: 'Conditional',
    fields: [
      { name: 'condition', type: 'text', label: 'If', default: 'value === true' }
    ],
    handles: {
      inputs: [{ position: 'Left', id: 'input' }],
      outputs: [
        { position: 'Right', id: 'true', style: { top: '33%' } },
        { position: 'Right', id: 'false', style: { top: '66%' } }
      ]
    },
    style: { backgroundColor: '#fff3e0' }
  },

  aggregator: {
    title: 'Aggregator',
    fields: [
      { name: 'operation', type: 'select', label: 'Operation',
        options: ['concat', 'merge', 'join'], default: 'concat' }
    ],
    handles: {
      inputs: [
        { position: 'Left', id: 'input1', style: { top: '25%' } },
        { position: 'Left', id: 'input2', style: { top: '50%' } },
        { position: 'Left', id: 'input3', style: { top: '75%' } }
      ],
      outputs: [{ position: 'Right', id: 'output' }]
    },
    style: { backgroundColor: '#fce4ec' }
  }
};
```

#### Step 1.3: Refactor Existing Nodes

**Example: `frontend/src/nodes/inputNode.js`**

```javascript
import { BaseNode } from './BaseNode';
import { nodeConfigs } from './nodeConfigs';

export const InputNode = ({ id, data }) => {
  return <BaseNode id={id} data={data} config={nodeConfigs.customInput} />;
};
```

#### Step 1.4: Create New Node Files

Simply create wrapper components for each new node type following the same pattern.

#### Step 1.5: Update ui.js

```javascript
import { FilterNode } from './nodes/filterNode';
import { TransformNode } from './nodes/transformNode';
import { APINode } from './nodes/apiNode';
import { ConditionalNode } from './nodes/conditionalNode';
import { AggregatorNode } from './nodes/aggregatorNode';

const nodeTypes = {
  customInput: InputNode,
  llm: LLMNode,
  customOutput: OutputNode,
  text: TextNode,
  filter: FilterNode,
  transform: TransformNode,
  api: APINode,
  conditional: ConditionalNode,
  aggregator: AggregatorNode,
};
```

#### Step 1.6: Update toolbar.js

Add the new node types to the toolbar.

---

## Part 2: Styling

### Design System

**Color Palette:**
- Primary: #6366f1 (Indigo)
- Secondary: #8b5cf6 (Purple)
- Success: #10b981 (Green)
- Warning: #f59e0b (Amber)
- Danger: #ef4444 (Red)
- Background: #f9fafb (Light Gray)
- Surface: #ffffff (White)
- Text: #1f2937 (Dark Gray)

### Implementation Steps

#### Step 2.1: Create Global Styles

**File:** `frontend/src/styles.css`

```css
/* Color variables */
:root {
  --primary: #6366f1;
  --secondary: #8b5cf6;
  --success: #10b981;
  --warning: #f59e0b;
  --danger: #ef4444;
  --background: #f9fafb;
  --surface: #ffffff;
  --text: #1f2937;
  --border: #e5e7eb;
}

/* App container */
.app-container {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
  background-color: var(--background);
  min-height: 100vh;
}

/* Toolbar styling */
.pipeline-toolbar {
  background: linear-gradient(135deg, var(--primary), var(--secondary));
  padding: 20px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

/* Node base styles */
.react-flow__node {
  border: 2px solid var(--border);
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
}

.react-flow__node:hover {
  box-shadow: 0 8px 16px rgba(0,0,0,0.15);
  transform: translateY(-2px);
}
```

#### Step 2.2: Style Individual Components

Apply CSS classes and styled-components to:
- Toolbar
- Draggable nodes
- Canvas nodes
- Submit button
- Handles

---

## Part 3: Text Node Logic

### Feature 1: Dynamic Resizing

#### Step 3.1: Replace Input with Textarea

```javascript
const [currText, setCurrText] = useState(data?.text || '{{input}}');
const textareaRef = useRef(null);

useEffect(() => {
  // Auto-resize textarea
  if (textareaRef.current) {
    textareaRef.current.style.height = 'auto';
    textareaRef.current.style.height = textareaRef.current.scrollHeight + 'px';
  }
}, [currText]);

return (
  <textarea
    ref={textareaRef}
    value={currText}
    onChange={handleTextChange}
    style={{
      width: '100%',
      minHeight: '60px',
      resize: 'none',
      overflow: 'hidden'
    }}
  />
);
```

### Feature 2: Variable Detection & Dynamic Handles

#### Step 3.2: Extract Variables

```javascript
const extractVariables = (text) => {
  const regex = /\{\{\s*([a-zA-Z_$][a-zA-Z0-9_$]*)\s*\}\}/g;
  const variables = new Set();
  let match;

  while ((match = regex.exec(text)) !== null) {
    variables.add(match[1]);
  }

  return Array.from(variables);
};

const [variables, setVariables] = useState([]);

useEffect(() => {
  const vars = extractVariables(currText);
  setVariables(vars);
}, [currText]);
```

#### Step 3.3: Render Dynamic Handles

```javascript
{variables.map((varName, idx) => (
  <Handle
    key={`var-${varName}`}
    type="target"
    position={Position.Left}
    id={`${id}-${varName}`}
    style={{ top: `${((idx + 1) * 100) / (variables.length + 1)}%` }}
  >
    <span className="handle-label">{varName}</span>
  </Handle>
))}
```

---

## Part 4: Backend Integration

### Frontend Updates

#### Step 4.1: Update submit.js

```javascript
import { useStore } from './store';

export const SubmitButton = () => {
  const nodes = useStore(state => state.nodes);
  const edges = useStore(state => state.edges);

  const handleSubmit = async () => {
    try {
      const response = await fetch('http://localhost:8000/pipelines/parse', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ nodes, edges })
      });

      const data = await response.json();

      alert(
        `Pipeline Analysis:\n` +
        `Number of Nodes: ${data.num_nodes}\n` +
        `Number of Edges: ${data.num_edges}\n` +
        `Is DAG: ${data.is_dag ? 'Yes ✓' : 'No ✗'}`
      );
    } catch (error) {
      alert(`Error: ${error.message}`);
    }
  };

  return (
    <div style={{display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
      <button type="button" onClick={handleSubmit}>Submit Pipeline</button>
    </div>
  );
};
```

### Backend Updates

#### Step 4.2: Update main.py

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import List, Dict

app = FastAPI()

# Enable CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

class Node(BaseModel):
    id: str

class Edge(BaseModel):
    source: str
    target: str

class Pipeline(BaseModel):
    nodes: List[Node]
    edges: List[Edge]

@app.get('/')
def read_root():
    return {'Ping': 'Pong'}

@app.post('/pipelines/parse')
def parse_pipeline(pipeline: Pipeline):
    num_nodes = len(pipeline.nodes)
    num_edges = len(pipeline.edges)
    is_dag = check_dag(pipeline.nodes, pipeline.edges)

    return {
        'num_nodes': num_nodes,
        'num_edges': num_edges,
        'is_dag': is_dag
    }

def check_dag(nodes: List[Node], edges: List[Edge]) -> bool:
    """Check if the graph is a Directed Acyclic Graph using DFS"""
    # Build adjacency list
    graph = {node.id: [] for node in nodes}
    for edge in edges:
        if edge.source in graph:
            graph[edge.source].append(edge.target)

    # DFS with colors: 0=white, 1=gray, 2=black
    color = {node.id: 0 for node in nodes}

    def has_cycle(node_id):
        if color[node_id] == 1:  # Gray node = cycle
            return True
        if color[node_id] == 2:  # Black node = already processed
            return False

        color[node_id] = 1  # Mark as gray

        for neighbor in graph.get(node_id, []):
            if has_cycle(neighbor):
                return True

        color[node_id] = 2  # Mark as black
        return False

    # Check each node
    for node in nodes:
        if color[node.id] == 0:
            if has_cycle(node.id):
                return False

    return True
```

---

## Testing Checklist

### Part 1 Testing
- [ ] Create each new node type from toolbar
- [ ] Verify nodes render correctly
- [ ] Test node field interactions
- [ ] Verify handles connect properly
- [ ] Test with different configurations

### Part 2 Testing
- [ ] Verify consistent styling across all components
- [ ] Test responsive design
- [ ] Check hover effects and transitions
- [ ] Verify color contrast and accessibility

### Part 3 Testing
- [ ] Test text node resizing with various lengths
- [ ] Test variable detection: `{{ var }}`, `{{var}}`, `{{ var1 }} {{ var2 }}`
- [ ] Test invalid patterns: `{{123}}`, `{{var-name}}`
- [ ] Verify dynamic handles appear/disappear correctly
- [ ] Test handle connections

### Part 4 Testing
- [ ] Test with empty pipeline
- [ ] Test with single node
- [ ] Test with linear pipeline (should be DAG)
- [ ] Test with branching pipeline (should be DAG)
- [ ] Test with cycle (should NOT be DAG)
- [ ] Verify alert displays correctly
- [ ] Test error handling (backend down, network error)

---

## Timeline

**Day 1 (6-8 hours):**
- Morning: Part 1 - Node abstraction (4-5 hours)
- Afternoon: Start Part 2 - Basic styling (2-3 hours)

**Day 2 (6-8 hours):**
- Morning: Complete Part 2 - Advanced styling (2-3 hours)
- Afternoon: Part 3 - Text node logic (3-4 hours)

**Day 3 (4-6 hours):**
- Morning: Part 4 - Backend integration (2-3 hours)
- Afternoon: Testing and polish (2-3 hours)

**Total: 16-22 hours over 3 days**

---

## Deliverables

1. **Code:**
   - Refactored node system with abstraction
   - 5 new node types
   - Styled components
   - Enhanced text node
   - Integrated backend

2. **Documentation:**
   - This implementation plan
   - Code comments
   - README updates (optional)

3. **Demonstration:**
   - Working application
   - All features functional
   - Clean, professional appearance

---

## Success Metrics

- ✅ Node abstraction reduces code duplication by 70%+
- ✅ New nodes can be created in <10 lines of code
- ✅ Application has cohesive, modern design
- ✅ Text node dynamically creates handles for variables
- ✅ Backend correctly identifies DAGs
- ✅ All parts work together seamlessly
- ✅ Code is maintainable and well-structured
