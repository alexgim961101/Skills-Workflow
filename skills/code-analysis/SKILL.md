---
name: code-analysis
description: |
  Skill for deep analysis of code structure, logic, and execution flow, producing structured reports.
  Used for code analysis requests, execution flow understanding, and architecture comprehension.
  Triggers: "code analysis", "code explanation", "execution flow", "code walkthrough"
---

# Code Analysis

## Goal
Perform deep analysis of how code works and
generate structured reports understandable even by junior developers.

## Instructions

### Step 1: Identify Analysis Scope

- Precisely identify what the user wants to know (logic flow, variable state, side effects, specific function behavior, etc.)
- Determine the scope of analysis (single function / class / module / full flow)

### Step 2: Deep Code Inspection

1. **Syntax/Logic analysis**: Identify the core algorithms and data flow
2. **Dependency tracing**: Find definitions of called functions/classes for context
3. **Technical details**: Variable scope, memory management, async handling, error handling, etc.

### Step 3: Related Code Exploration

- Find definitions of other modules that the analysis target imports/calls
- Identify interfaces, data models, utility functions needed for understanding

### Step 4: Write Analysis Report

Write the report using the structure below.

```markdown
## 🧐 Deep Code Analysis

**Key Summary**: 1–2 sentence summary of what this code does.

**Detailed Logic**: In-depth technical analysis.
(Including variable scope, memory management, async handling, error handling, etc.)

## 🪜 Step-by-Step Flow

1. **[Step Name]**: Description → Result
2. **[Step Name]**: Description → Result
3. ...

## 🔗 Related Code & References

- [filename]: [function/class name] - [One-line role description]
- ...

## 💡 Optimizations & Notes

- Potential bugs, edge cases
- Performance improvement points
- Tricks or pitfalls to watch out for
```

## Constraints
- No vague descriptions ("performs some logic") — explain specifically *what* logic
- Do not guess without actually reading the code
- Use English for explanations; keep code terms/identifiers as-is from the source
