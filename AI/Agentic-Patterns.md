# Agentic AI Patterns

> Patterns for building AI systems that can plan, act, and learn autonomously.

---

## What Makes an Agent?

An AI agent has four key capabilities:
1. **Perceive** — read from the environment (files, APIs, web)
2. **Plan** — decide what steps to take
3. **Act** — execute actions (write files, call APIs, run code)
4. **Learn** — update its knowledge based on results

```
User Goal
    ↓
Agent Planning (LLM decides steps)
    ↓
Tool Selection & Execution
    ↓
Observe Result
    ↓
Continue or Report Done
```

---

## ReAct Pattern (Reason + Act)

The most common agentic loop pattern:

```
Thought: I need to find information about X
Action: search_web("X")
Observation: [search results]

Thought: I found Y, now I need to verify with Z
Action: read_file("notes/Z.md")  
Observation: [file contents]

Thought: I have enough information to answer
Action: write_note("findings/X.md", "...")
Observation: File written successfully

Final Answer: [response to user]
```

---

## Tool Design Principles

Good agent tools should be:
- **Atomic** — do one thing well
- **Idempotent** — safe to call multiple times
- **Descriptive** — clear name + docstring so the LLM understands when to use them

### Example Tool Definitions (Claude API format)

```json
{
  "name": "read_vault_note",
  "description": "Read a note from the Obsidian knowledge vault. Use when you need to recall saved information, check existing notes, or verify knowledge.",
  "input_schema": {
    "type": "object",
    "properties": {
      "file_path": {
        "type": "string",
        "description": "Path to the note relative to vault root, e.g. 'Security/OWASP-Top-10.md'"
      }
    },
    "required": ["file_path"]
  }
}
```

```json
{
  "name": "write_vault_note",
  "description": "Create or update a note in the Obsidian knowledge vault. Use when you learn something new worth remembering, or when the user asks you to save information.",
  "input_schema": {
    "type": "object",
    "properties": {
      "file_path": {
        "type": "string",
        "description": "Path for the note, e.g. 'Research/topic-name.md'"
      },
      "content": {
        "type": "string",
        "description": "Full markdown content of the note"
      },
      "commit_message": {
        "type": "string",
        "description": "Brief description of what was added/changed"
      }
    },
    "required": ["file_path", "content"]
  }
}
```

---

## Memory Systems

### Short-term Memory
- The conversation history (messages array)
- Limited by context window (~200k tokens for Claude)

### Long-term Memory (Armageddon's approach)
- Obsidian vault on GitHub
- AI writes summaries and learnings back to vault
- Pulled on every startup = persistent memory

### Memory Update Trigger
After each conversation, run:
```
"Summarise any new information learned in this conversation 
 that would be worth adding to the knowledge base. 
 If there is valuable new knowledge, write it to the vault
 in the appropriate category."
```

---

## Background Learning Agent

Schedule a recurring task to expand the knowledge base:

```python
# Pseudo-code for background agent loop
async def background_learning_agent():
    while True:
        # 1. Check for new security advisories
        new_cves = await fetch_nvd_feed()
        
        # 2. Check for relevant GitHub repos/commits
        trending = await search_github_trending('security')
        
        # 3. For each interesting item, summarise and save
        for item in new_cves + trending:
            if is_relevant(item):
                summary = await ai_summarise(item)
                await vault.write(
                    f"Auto-Research/{item.id}.md",
                    summary,
                    f"Auto-learned: {item.title}"
                )
        
        await asyncio.sleep(3600)  # run hourly
```

---

## Armageddon Agent Tools (Planned)

| Tool | Description | Phase |
|---|---|---|
| `read_vault` | Read notes from knowledge base | Phase 3 ✅ |
| `write_vault` | Save new knowledge to vault | Phase 3 ✅ |
| `search_vault` | Fuzzy search through all notes | Phase 3 ✅ |
| `search_web` | Search the internet | Phase 5 |
| `run_code` | Execute Python/JS code | Phase 5 |
| `search_github` | Search GitHub repos/code | Phase 5 |
| `scan_target` | Run nmap on a target | Phase 5 |
| `lookup_cve` | Look up CVE details from NVD | Phase 4 |
| `search_exploit` | Search exploit-db | Phase 5 |

---

## Safety Considerations

- Always confirm destructive actions with user before executing
- Log all agent actions for auditability
- Rate limit tool calls to prevent runaway loops
- Sandbox code execution (Docker/VM)
- Never store credentials in notes/vault
- Respect scope boundaries — only act within authorised targets
