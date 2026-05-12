# Features & Comparison

[← Back to README](../README.md)

## Why This Skill?

| Feature | This Skill | Native Claude Code | Other Skills | MCP Server |
|---------|-----------|-------------------|--------------|------------|
| **Write Mermaid syntax** | Guided by examples | Built-in capability | Varies | Varies |
| **Validation before export** | Required step | No validation loop | Often skipped | Varies |
| **Export to PNG/SVG/PDF** | Automatic | Manual — user must ask | Usually one method | Web only |
| **Zero-install fallback** | Kroki needs only curl | No fallback | Requires install | Requires setup |
| **Proactive triggering** | Auto-triggers for 3+ components | Only when explicitly asked | Manual only | Manual |
| **Chinese language support** | Chinese keyword triggers | No keyword triggers | English only | English only |
| **End-to-end workflow** | Generate → Validate → Export → Report | Generate only | Partial | Partial |
| **Progressive disclosure** | Syntax in separate files | N/A | All inline | N/A |

**Key advantages over native Claude Code:**
- **Complete pipeline** — Claude Code can write Mermaid, but stops at `.mmd`. This skill adds validation, export, and error recovery automatically
- **Catches errors early** — validation loop prevents broken diagrams from being exported
- **Flexible export** — local mmdc or Kroki API fallback (no install needed)
- **Proactive diagramming** — auto-triggers when discussing architecture, not just when you ask for a diagram

## What This Skill Can Do

### Diagram Types (11+)

| Type | Use for | Example |
|------|---------|---------|
| **Flowchart** | Processes, pipelines, decision trees | CI/CD pipeline, user registration flow |
| **Sequence** | API calls, authentication flows | JWT auth, microservice communication |
| **Class** | OOP models, data structures | Domain models, inheritance hierarchies |
| **ER** | Database schemas | User-Order-Product relationships |
| **State** | State machines, lifecycles | Order status, connection states |
| **Gantt** | Project timelines | Sprint planning, release schedules |
| **Pie** | Proportions, distributions | Market share, resource allocation |
| **Git Graph** | Branch strategies | GitFlow, trunk-based development |
| **C4 Context** | High-level architecture | System context, container diagrams |
| **Mind Map** | Topic breakdowns | Feature planning, brainstorming |

### Output Formats

- **PNG** — High resolution (2048px), white background, multiple themes
- **SVG** — Scalable vector, perfect for docs
- **PDF** — Print-ready documents

### Automatic Triggering

The skill activates when you:
- Ask for diagrams explicitly: *"create a flowchart"*, *"draw architecture"*
- Explain complex systems: *"how does authentication work"* (3+ components)
- Use Chinese: *"画一个时序图"*, *"架构图"*
