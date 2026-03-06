# Super Search with QMD

QMD is a hybrid search engine that runs locally and indexes your markdown files.

## Install
```bash
# Install via bun
bun add -g @tobilu/qmd
```

## Create a Collection
```bash
# Index your notes
qmd collection add ~/notes --name my-notes --mask "**/*.md"

# Index a project
qmd collection add ~/projects/my-app --name my-app --mask "**/*.md"
```

## Search Modes
```bash
# Fast keyword search (BM25)
qmd search "machine learning" -c my-notes

# Semantic vector search (finds similar meaning, not just keywords)
qmd vsearch "how do neural networks learn?" -c my-notes

# Best quality: hybrid + LLM reranking
qmd query "what patterns emerge in customer feedback?" -c my-notes
```

## Why This Matters
- **Local**: No data leaves your machine
- **Fast**: BM25 search is instant, vector search < 1 second
- **Smart**: Semantic search finds related content even without keyword matches
- **Agent-ready**: MCP integration means any AI agent can search your files

## Use Case: Agent Memory
Your AI agents can search across all your notes, journals, and project docs:
```
"Barack, what did we decide about the trading strategy last week?"
→ Searches 1,586 files across 4 collections
→ Finds the relevant memory entry from 3 days ago
→ Answers with full context
```
