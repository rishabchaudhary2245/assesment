# VectorShift Frontend Technical Assessment

## 📋 Project Summary

This is a **visual pipeline builder application** built with React and FastAPI. It allows users to create node-based workflows by dragging and dropping nodes onto a canvas and connecting them together.

### Quick Links
- **[📊 Full Project Analysis](./PROJECT_ANALYSIS.md)** - Detailed analysis of what the project is and what needs to be done
- **[🗺️ Implementation Plan](./IMPLEMENTATION_PLAN.md)** - Step-by-step technical implementation guide

---

## 🎯 What This Project Is

A **drag-and-drop pipeline builder** similar to tools like:
- n8n (workflow automation)
- Zapier (app integration)
- Node-RED (flow-based programming)
- VectorShift (AI workflow builder)

**Core Functionality:**
- Drag nodes from a toolbar onto a canvas
- Connect nodes together to form data pipelines
- Configure node properties (names, types, text inputs)
- Validate and analyze pipelines on a backend

---

## 🏗️ Technology Stack

**Frontend:**
- React 18.2.0
- ReactFlow 11.8.3 (visual node editor)
- Zustand (state management)
- Create React App

**Backend:**
- Python with FastAPI
- Uvicorn ASGI server

---

## 📝 Assessment Requirements (4 Parts)

### Part 1: Node Abstraction ⚙️
**Goal:** Eliminate code duplication in node components

**Current Problem:**
- Each node type has ~40-50 lines of repeated code
- Creating new nodes requires copy-paste-modify

**Solution:**
- Create a reusable `BaseNode` component
- Use configuration objects to define node types
- Create 5 new node types to demonstrate flexibility

**New Nodes to Create:**
1. Filter Node
2. Transform Node
3. API Node
4. Conditional Node
5. Aggregator Node

---

### Part 2: Styling 🎨
**Goal:** Create a modern, unified design

**Current Problem:**
- Minimal styling (just black borders)
- No visual polish

**Solution:**
- Design a color scheme and style guide
- Apply consistent styling to all components
- Add hover effects, shadows, gradients
- Make it visually appealing and professional

---

### Part 3: Text Node Logic 📝
**Goal:** Enhance text node with dynamic features

**Two Features:**

**3.1 - Dynamic Resizing:**
- Text node should grow/shrink based on content
- Replace `<input>` with auto-resizing `<textarea>`

**3.2 - Variable Detection:**
- Detect `{{ variableName }}` patterns in text
- Automatically create left-side handles for each variable
- Example: `"Hello {{ name }}, age: {{ age }}"` creates 2 handles

---

### Part 4: Backend Integration 🔌
**Goal:** Connect frontend to backend for pipeline analysis

**Frontend Tasks:**
- Update submit button to send nodes/edges to backend
- Display response in user-friendly alert

**Backend Tasks:**
- Change `/pipelines/parse` to POST endpoint
- Calculate number of nodes and edges
- Implement DAG (Directed Acyclic Graph) detection
- Return: `{num_nodes: int, num_edges: int, is_dag: bool}`

**DAG Detection:** Use topological sort or DFS to detect cycles

---

## 🚀 Getting Started

### Run the Frontend
```bash
cd drive-download-20260313T150513Z-1-001/frontend
npm install
npm start
# Opens at http://localhost:3000
```

### Run the Backend
```bash
cd drive-download-20260313T150513Z-1-001/backend
pip install fastapi uvicorn
uvicorn main:app --reload
# Runs at http://localhost:8000
```

---

## 📂 Project Structure

```
drive-download-20260313T150513Z-1-001/
├── frontend/
│   ├── src/
│   │   ├── nodes/              # Node components (input, output, llm, text)
│   │   ├── App.js              # Main app
│   │   ├── ui.js               # ReactFlow canvas
│   │   ├── toolbar.js          # Draggable node toolbar
│   │   ├── submit.js           # Submit button
│   │   ├── store.js            # Zustand state
│   │   └── draggableNode.js    # Reusable draggable component
│   └── package.json
└── backend/
    └── main.py                  # FastAPI backend
```

---

## ✅ Implementation Checklist

### Part 1: Node Abstraction
- [ ] Create `BaseNode.js` component
- [ ] Create `nodeConfigs.js` with configurations
- [ ] Refactor existing 4 nodes to use BaseNode
- [ ] Create 5 new node types
- [ ] Update `ui.js` and `toolbar.js`
- [ ] Test all nodes

### Part 2: Styling
- [ ] Design color scheme
- [ ] Create global styles
- [ ] Style toolbar
- [ ] Style nodes
- [ ] Style submit button
- [ ] Add animations and effects

### Part 3: Text Node Logic
- [ ] Replace input with auto-resizing textarea
- [ ] Implement variable extraction regex
- [ ] Create dynamic handles based on variables
- [ ] Update store to handle dynamic handles
- [ ] Test with various inputs

### Part 4: Backend Integration
- [ ] Install FastAPI dependencies
- [ ] Update backend endpoint to POST
- [ ] Implement DAG detection algorithm
- [ ] Update submit button with fetch call
- [ ] Create alert display
- [ ] Enable CORS
- [ ] Test end-to-end

---

## 📊 Expected Outcomes

After completion, the application will:

✅ Support 9+ node types with minimal code per node
✅ Have professional, modern styling throughout
✅ Feature dynamic text nodes with variable handles
✅ Validate pipelines as DAGs (no cycles)
✅ Provide instant feedback on pipeline structure
✅ Have complete frontend-backend integration

---

## ⏱️ Estimated Effort

- **Node Abstraction:** 4-6 hours
- **Styling:** 3-4 hours
- **Text Node Logic:** 3-4 hours
- **Backend Integration:** 2-3 hours
- **Testing & Polish:** 2-3 hours

**Total: 14-20 hours** (2-3 days of focused work)

---

## 🎓 Key Learning Objectives

This assessment tests:

1. **Component Abstraction** - Creating reusable patterns
2. **State Management** - Zustand with ReactFlow
3. **Dynamic UI** - Variable detection and handle creation
4. **API Integration** - Frontend-backend communication
5. **Algorithm Implementation** - DAG detection
6. **UI/UX Design** - Creating appealing interfaces

---

## 📚 Additional Resources

- [ReactFlow Documentation](https://reactflow.dev/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Zustand Documentation](https://github.com/pmndrs/zustand)
- [DAG Detection Algorithms](https://en.wikipedia.org/wiki/Topological_sorting)

---

## 📞 Contact

Questions? Email: recruiting@vectorshift.ai

---

**Status:** ✅ Analysis Complete - Ready for Implementation

See [PROJECT_ANALYSIS.md](./PROJECT_ANALYSIS.md) for detailed breakdown and [IMPLEMENTATION_PLAN.md](./IMPLEMENTATION_PLAN.md) for technical implementation steps.
