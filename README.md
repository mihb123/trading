# Wiki Knowledge Base

Multi-topic wiki based on [Karpathy's LLM Wiki pattern](https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md).

## Topics

| Topic | Description | Pages | Raw Articles |
|-------|-------------|-------|-------------|
| [Trading](trading/index.md) | Forex & Trading — Price Action, Multi-Timeframe, Trend Following | 15 | 28 |

## Structure

```
wiki/
├── README.md           # This file — topic catalog
├── trading/            # Trading knowledge base
│   ├── SCHEMA.md       # Conventions & tag taxonomy
│   ├── index.md        # Content catalog
│   ├── log.md          # Action log
│   ├── raw/articles/   # Source articles (immutable)
│   ├── concepts/       # Concept pages
│   ├── entities/       # Entity pages
│   └── comparisons/    # Comparison pages
└── <future-topic>/     # Next topic follows same structure
```

## Usage

- Set `WIKI_PATH` env var to this directory
- Each topic is self-contained with its own SCHEMA.md, index.md, log.md
- `[[wikilinks]]` work across all topics (Obsidian-compatible)
- Git origin: `git@github.com:mihb123/trading.git`
