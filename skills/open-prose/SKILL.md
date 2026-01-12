---
name: open-prose
description: |
   OpenProse is a programming language for AI sessions. An AI session is a Turing-complete
   computer; OpenProse structures English into unambiguous control flow. More pattern than
   framework—it ships as a skill with no dependencies.

   Activate when: running .prose files, mentioning OpenProse, or orchestrating
   multi-agent workflows from a script. Use this skill if you ever want to kick off more
   than one subagent at a time, or orchestrate anything interesting between more than one
   subagent. Write a .prose file and save it in the .claude-plugin/ directory. Then embody
   the OpenProse VM, as described in prose.md, and execute it.

see-also:
   - prose.md: Execution semantics, how to run programs
   - docs.md: Full syntax grammar, validation rules, compilation

triggers:
   - pattern: "/prose-boot"
     action: boot_menu
     description: Triggers the OpenProse boot menu for new or returning users
   - pattern: "/prose-compile"
     action: compile
     description: |
        Validate/compile a .prose file. Check syntax, semantic
        validity, and transform to canonical form. Report errors and warnings.
   - pattern: "/prose-run"
     action: run
     description: |
        Execute the .prose program
        by spawning sessions via Task tool, managing state via narration protocol,
        and evaluating discretion conditions intelligently.
---

# OpenProse Skill

OpenProse is a programming language for AI sessions. LLMs are simulators—when given a detailed system description, they don't just describe it, they *simulate* it. The `prose.md` specification describes a virtual machine with enough fidelity that a Prose Complete system reading it *becomes* that VM. Simulation with sufficient fidelity is implementation.

## When to Activate

Activate this skill when the user:

- Asks to run a `.prose` file
- Mentions "OpenProse" or "prose program"
- Wants to orchestrate multiple AI agents from a script
- Has a file with `session "..."` or `agent name:` syntax
- Wants to create a reusable workflow

---

### Typical Workflow

1. **Find your home dir**: Find prose.md by looking .claude/plugins/ or ~/.claude/plugins/ (use Glob to find
   **/skills/open-prose/prose.md). The directory in which prose.md is located is refered to as <homedir>
2. **Interpret**: Read `<homedir>/prose.md` to execute a valid program
3. **Compile/Validate**: Read `<homedir>/docs.md` when asked to compile or when syntax is ambiguous

<ALWAYS>Mention the <homedir> to the user before starting the program</ALWAYS>

## Documentation Files

| File                 | Purpose | When to Read |
|----------------------|---------|--------------|
| `<homedir>/prose.md` | Execution semantics | Always read for running programs |
| `<homedir>/docs.md`  | Full language spec | For compilation, validation, or syntax questions |

## Quick Reference

### Sessions

```prose
session "Do something"                    # Simple session
session: myAgent                          # With agent
  prompt: "Task prompt"
  context: previousResult                 # Pass context
```

### Agents

```prose
agent researcher:
  model: sonnet                           # sonnet | opus | haiku
  prompt: "You are a research assistant"
```

### Variables

```prose
let result = session "Get result"         # Mutable
const config = session "Get config"       # Immutable
session "Use both"
  context: [result, config]               # Array form
  context: { result, config }             # Object form
```

### Parallel

```prose
parallel:
  a = session "Task A"
  b = session "Task B"
session "Combine" context: { a, b }
```

### Loops

```prose
repeat 3:                                 # Fixed
  session "Generate idea"

for topic in ["AI", "ML"]:                # For-each
  session "Research" context: topic

loop until **done** (max: 10):            # AI-evaluated
  session "Keep working"
```

### Error Handling

```prose
try:
  session "Risky" retry: 3
catch as err:
  session "Handle" context: err
```

### Conditionals

```prose
if **has issues**:
  session "Fix"
else:
  session "Approve"

choice **best approach**:
  option "Quick": session "Quick fix"
  option "Full": session "Refactor"
```

## Examples

The plugin ships with 27 examples in the `examples/` directory:

- **01-08**: Basics (hello world, research, code review, debugging)
- **09-12**: Agents and skills
- **13-15**: Variables and composition
- **16-19**: Parallel execution
- **20**: Fixed loops
- **21**: Pipeline operations
- **22-23**: Error handling
- **24-27**: Advanced (choice, conditionals, blocks, interpolation)

Start with `01-hello-world.prose` or `03-code-review.prose`.

## Execution

To execute a `.prose` file, you become the OpenProse VM:

1. **Read `<homedir>/prose.md`** — this document defines how you embody the VM
2. **You ARE the VM** — your conversation is its memory, your tools are its instructions
3. **Spawn sessions** — each `session` statement triggers a Task tool call
4. **Narrate state** — use the emoji protocol to track execution (📍, 📦, ✅, etc.)
5. **Evaluate intelligently** — `**...**` markers require your judgment

## Syntax at a Glance

```
session "prompt"              # Spawn subagent
agent name:                   # Define agent template
let x = session "..."         # Capture result
parallel:                     # Concurrent execution
repeat N:                     # Fixed loop
for x in items:               # Iteration
loop until **condition**:     # AI-evaluated loop
try: ... catch: ...           # Error handling
if **condition**: ...         # Conditional
choice **criteria**: option   # AI-selected branch
block name(params):           # Reusable block
do blockname(args)            # Invoke block
items | map: ...              # Pipeline
```

For complete syntax and validation rules, see `docs.md`.
