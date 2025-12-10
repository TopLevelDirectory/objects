---
argument-hint: <project-description>
description: Generate a comprehensive spec-driven, test-driven PRD prompt for AI coding agents
---

# Meta-Prompt: AI Coding Agent PRD Generator

You are a **meta-prompt engineer**. Your task is to generate a comprehensive, production-ready PRD document that will serve as both a specification AND an executable prompt for an AI coding agent.

## Project to Generate PRD For:
**$ARGUMENTS**

---

## Your Task

Generate a complete PRD document in markdown format that an AI coding agent can use as its primary instruction set. The document must follow the structure and principles below.

**IMPORTANT**: Use web search and Context7 MCP (if available) to research:
- Best practices for the specific technology stack needed
- Current package versions and recommended libraries (use 2025 versions)
- Common pitfalls and edge cases for this type of project
- Testing patterns specific to the chosen stack

---

## Required PRD Structure

### 1. Project Overview Section
- Clear project title and one-paragraph summary
- Problem statement and solution overview
- Success criteria (measurable outcomes)
- Technology stack recommendations (research current best practices)

### 2. Architecture & Setup Phase (MUST BE FIRST TASKS)
Generate tasks for:
- [ ] Project scaffolding and directory structure
- [ ] Dependency installation with exact versions
- [ ] Configuration files (env, tsconfig, etc.)
- [ ] Development environment setup
- [ ] **Testing infrastructure setup** (unit + integration frameworks)
- [ ] CI/CD pipeline configuration (if applicable)

### 3. Testing Architecture Section
**This section is CRITICAL** - define before any feature implementation:
- Testing frameworks and tools to use
- Directory structure for tests (`__tests__/`, `*.test.ts`, etc.)
- Test naming conventions
- Mock/stub strategies
- Coverage requirements (aim for >80%)
- Integration test boundaries

### 4. Feature Tasks Section
For EACH feature, generate a task block in this exact format:

```markdown
#### Task [N]: [Feature Name]

**Description**: [2-3 sentences describing the feature]

**Acceptance Criteria**:
- [ ] [Specific, testable criterion 1]
- [ ] [Specific, testable criterion 2]
- [ ] [etc.]

**Test Requirements**:
- [ ] Unit tests written BEFORE implementation
- [ ] Edge cases covered: [list specific edge cases]
- [ ] Integration tests for: [list integration points]

```json
{
  "task_id": "TASK-[N]",
  "name": "[Feature Name]",
  "status": "pending",
  "tests_status": "not_written",
  "unit_tests_passing": false,
  "integration_tests_passing": false,
  "dependencies": ["TASK-X", "TASK-Y"],
  "estimated_complexity": "low|medium|high"
}
```
```

### 5. Agent Instructions Section
Include these explicit instructions for the AI coding agent:

```markdown
## Instructions for AI Coding Agent

### Development Methodology
You MUST follow **Test-Driven Development (TDD)** and **Spec-Driven Development (SDD)**:

1. **Read the spec first** - Understand the full requirement before writing code
2. **Write tests first** - Create failing tests that define expected behavior
3. **Implement minimally** - Write only enough code to pass tests
4. **Refactor** - Clean up while keeping tests green
5. **Update this document** - Mark checkboxes and update JSON blocks

### Web Search & Documentation Protocol
- Use web search to find current documentation for packages
- Use Context7 MCP (if available) to get library-specific context
- Always verify API signatures against latest docs
- Search for known issues/bugs before implementing workarounds

### Test Execution Protocol
After completing each task:
1. Run the current task's unit tests
2. Run the previous 2 tasks' unit tests (regression check)
3. Run all integration tests that touch modified code
4. Only mark task complete if ALL tests pass

### Document Update Protocol
When a task is complete:
1. Check off all acceptance criteria boxes
2. Update the JSON block:
   - Set `"status": "completed"`
   - Set `"tests_status": "passing"`
   - Set `"unit_tests_passing": true`
   - Set `"integration_tests_passing": true`
3. Add completion timestamp as comment

### Error Handling Standards
- Never silently swallow errors
- Log with appropriate severity levels
- Provide actionable error messages
- Include error recovery paths where applicable

### Code Quality Standards
- Follow language-specific conventions
- Use meaningful variable/function names
- Keep functions small and focused
- Document complex logic with comments
- No hardcoded values - use configuration
```

### 6. External Memory Section
Include a section for the agent to track state:

```markdown
## Project State (External Memory)

### Completed Tasks
<!-- Agent: Add completed task IDs here -->

### Current Task
<!-- Agent: Update with current task ID -->

### Blockers & Notes
<!-- Agent: Document any blockers or important discoveries -->

### Test Results Log
<!-- Agent: Log test run results with timestamps -->
```

### 7. Dependency Graph
Generate a visual or textual dependency graph showing task order.

---

## Output Format

Generate the complete PRD as a single markdown document that:
1. Can be saved as `PRD.md` or `SPEC.md` in the project root
2. Is self-contained - an agent can execute from this document alone
3. Uses proper markdown formatting with checkboxes `- [ ]`
4. Includes all JSON task blocks
5. Has clear section headers for navigation

---

## Quality Checklist for Generated PRD

Before finalizing, ensure:
- [ ] Architecture/setup tasks come FIRST
- [ ] Testing infrastructure is established BEFORE features
- [ ] Every feature has explicit test requirements
- [ ] JSON blocks are valid and parseable
- [ ] Dependencies between tasks are clearly defined
- [ ] Edge cases are enumerated for each feature
- [ ] Agent instructions are clear and actionable
- [ ] Web search recommendations are specific to the tech stack

---

Now generate the complete PRD for: **$ARGUMENTS**
