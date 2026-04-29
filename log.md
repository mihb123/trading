# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete
> When this file exceeds 500 entries, rotate: rename to log-YYYY.md, start fresh.

## [2026-04-30] create | Wiki initialized
- Domain: [To be specified by user]
- Structure created with SCHEMA.md, index.md, log.md
- Directory structure: raw/{articles,papers,transcripts,assets}, entities, concepts, comparisons, queries, _archive

## [2026-04-30] create | Multi-timeframe forex concept
- Created: concepts/multi-timeframe-forex.md
- Created: concepts/price-action.md
- Created: entities/market-structure.md
- Updated: index.md with 3 new pages
- Topic: Top-Down Analysis + Price Action in forex trading
- Covers: timeframe hierarchy, price action patterns, confluence, risk management
- Tags: forex, price-action, multi-timeframe, technical-analysis

## [2026-04-30] ingest | Price Action & Candlestick Patterns Research
- Sources ingested:
  - raw/articles/wikipedia-price-action-trading.md (Wikipedia, 49K chars)
  - raw/articles/wikipedia-candlestick-pattern.md (Wikipedia, 2.2K chars)
  - raw/articles/tradingstrategyguides-price-action.md (TSG, extracted)
- Updated: SCHEMA.md (domain + tag taxonomy)
- Rewrote: concepts/price-action.md (full rewrite with high-quality content)
- Created: concepts/candlestick-patterns.md (12+ patterns with ASCII art)
- Created: entities/market-structure.md (was missing)
- Created: comparisons/price-action-vs-indicators.md
- Updated: index.md (6 total pages)
- Tags: candlesticks, price-action, comparison, market-structure

## [2026-04-30] ingest | Nial Fuller Price Action & Advanced Patterns
- Sources ingested:
  - raw/articles/priceaction-com-nial-fuller.md (PriceAction.com, Nial Fuller)
  - 5/8 URLs blocked by Cloudflare/bot detection (Capital.com, CMC Markets, ForexFactory, Quora, QuantVPS)
  - 2/8 URLs returned metadata only (TradingSetupsReview index, Scribd)
- Created: concepts/fakey-pattern.md (Inside Bar False Breakout with ASCII diagram)
- Created: concepts/price-action-confluence.md (scoring system, multi-TF, checklist)
- Updated: concepts/price-action.md (added KISS philosophy section from Nial Fuller)
- Updated: index.md (9 total pages)
- New concepts: Fakey pattern types, confluence scoring system (12+ points = quality setup)
