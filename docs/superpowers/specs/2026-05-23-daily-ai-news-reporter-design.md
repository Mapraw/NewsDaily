# Daily AI News Reporter Design

## Purpose

The project builds an agent-heavy daily news reporter for AI, LLMs, AI use cases, technology, science, and world knowledge. Its primary job is not only to summarize news, but to identify trends and convert them into useful opportunity ideas.

The first version starts with Reddit only, while leaving clear extension points for Hacker News, arXiv, company blogs, mainstream news, YouTube/X, and other sources later.

## Audience And Priorities

The report is for a user who wants:

- AI and LLM news.
- Practical AI use cases.
- Ideas for products, workflows, or businesses.
- Typical knowledge about important world events.

Content priority order:

1. Trending topics and discussions.
2. Useful AI/use-case ideas.
3. Important global or technology knowledge.

The report should avoid filler. If the daily signal is weak, the system should expand to fallback subreddits instead of forcing low-quality stories from the core source list.

## Source Strategy

### MVP Sources

The MVP fetches from Reddit only.

Core subreddits:

- `r/artificial`
- `r/LocalLLaMA`
- `r/OpenAI`
- `r/ClaudeAI`
- `r/MachineLearning`
- `r/singularity`
- `r/technology`
- `r/Futurology`
- `r/worldnews`
- `r/science`

Fallback subreddits:

- `r/news`
- `r/worldnews`
- `r/science`
- `r/technology`

All source lists must be stored in config so they can be edited without code changes.

### Future Sources

The source layer should support adding new collectors later:

- Hacker News
- arXiv
- Company blogs
- News websites
- YouTube/X
- RSS feeds

Collectors should emit a shared candidate story format so downstream workers do not depend on Reddit-specific fields.

## Architecture

The MVP should use an agent-heavy workflow, but keep orchestration controlled. Agents are explicit worker roles with clear input/output files, not open-ended autonomous loops.

### Workers

| Worker | Responsibility | Input | Output |
| --- | --- | --- | --- |
| Researcher | Fetch Reddit posts and metadata. | Source config | Raw candidate JSON |
| Ranker | Score and deduplicate candidates. | Raw candidates, memory | Ranked candidates |
| Summarizer | Write concise summaries. | Selected candidates | Story summaries |
| FactChecker | Add confidence and uncertainty labels. | Selected candidates, links | Confidence metadata |
| IdeaExtractor | Generate opportunity cards. | Selected stories | Idea card JSON |
| Editor | Assemble the final report. | Summaries, confidence, ideas | Report JSON/Markdown |
| Publisher | Send Telegram and write HTML/archive files. | Final report | Telegram messages, files |
| MemoryManager | Track seen stories, topics, ideas, preferences, and entities. | Final report, ideas | Updated memory files |

### Data Flow

1. Load config.
2. Fetch Reddit candidates from core sources.
3. If signal is weak, fetch fallback sources.
4. Deduplicate by exact URL/title.
5. Rank candidates with deterministic metrics and AI scoring.
6. Select about 7 stories.
7. Summarize, fact-check, and extract opportunity cards.
8. Assemble the final report.
9. Save JSON, Markdown, and HTML archives.
10. Update memory files and idea board.
11. Send Telegram report sections.
12. Write logs for the run.

## Ranking

Ranking should use a weighted blend:

- Trend signal: upvotes, comments, freshness, and subreddit rank.
- AI/LLM relevance.
- Idea potential.
- World importance.
- Source confidence.
- Novelty.

The MVP deduplication rule is exact URL/title matching only. Similarity-based and memory-aware dedupe can be added later.

The default target is 7 stories per report. The valid range is 5-10 stories.

## Fact-Checking Policy

The MVP uses a hybrid policy:

- Reddit trends may be included even if not externally verified.
- Unverified claims must be labeled clearly.
- Every story must include a confidence level.
- Every story must include an uncertainty note.
- If a post links to an original source, that source link should be included.

The report must not present Reddit speculation as confirmed fact.

## Report Format

Each report should be structured and readable in about 3-5 minutes.

Each story includes:

- Title
- Summary
- Why it matters
- AI/use-case idea
- Trend signal
- Confidence level
- Uncertainty note
- Source links

## Opportunity Cards

Each selected story generates an opportunity card:

- Idea
- Target user
- Problem solved
- Possible product or workflow
- Difficulty
- Source story ID
- Status

Valid statuses:

- `new`
- `reviewed`
- `saved`
- `dismissed`
- `favorite`

All opportunity cards are auto-saved. The idea board should allow these cards to be viewed separately from the daily report.

## Memory

The MVP uses file-based memory. It should track:

- Seen stories
- Repeated topics
- Generated ideas
- Source quality
- User preferences
- Recurring entities such as companies, models, tools, products, countries, and organizations

Memory is used to reduce repetition, track emerging themes, and improve future ranking.

Evening reports should suppress duplicates from the morning report unless the story has materially changed or gained significant momentum.

## Delivery

### Telegram

Telegram should receive the report in multiple sections/messages with links. The report should be readable directly in Telegram, not just a notification.

Suggested Telegram sections:

1. Header and executive summary.
2. Top stories.
3. Opportunity ideas.
4. Links to local HTML/Markdown archive when available.

### Archive

Each run saves:

- Report JSON
- Markdown report
- HTML report
- Opportunity card JSON
- Run log

### Visualization

The visualization page should prioritize:

1. Idea board MVP.
2. Report archive browser.
3. Static HTML report viewing.

The first version can be static HTML generated from files. It should show opportunity cards and allow browsing reports by date/time.

## Schedule

The system runs twice daily in Asia/Bangkok timezone:

- Morning digest.
- Evening update.

Morning and evening reports use the same report structure. The evening report suppresses duplicates unless a story changed meaningfully.

## AI Provider Strategy

OpenRouter is the first AI provider, but the system must be provider-agnostic.

The AI layer should allow future providers:

- Qwen APIs
- Local LLMs
- Other commercial APIs

Default behavior:

- Use deterministic Reddit ranking first.
- Use AI for relevance scoring, summaries, confidence labels, and opportunity cards.

Config should control:

- Provider
- Model
- Budget
- Story count
- Source lists
- AI depth

## Storage

Use files only for MVP.

Suggested file categories:

- `config/*.yaml` or `config/*.json`
- `data/raw/*.json`
- `data/reports/*.json`
- `data/reports/*.md`
- `data/reports/*.html`
- `data/ideas/*.json`
- `data/memory/*.json`
- `logs/*.log`

No SQLite or Postgres is required for MVP.

## Error Handling

The system should log:

- Reddit fetch failures.
- LLM provider failures.
- Telegram delivery failures.
- Skipped stories.
- Empty or weak source results.

If Telegram delivery fails, the report should still be saved locally.

If LLM processing fails for some stories, the run should continue where possible and mark affected stories with lower confidence or an error note.

## MVP Success Criteria

The MVP is successful when one run can:

- Fetch Reddit posts from configured subreddits.
- Rank and select about 7 high-signal stories.
- Generate a structured report.
- Save Markdown, HTML, and JSON archives.
- Save opportunity cards with status.
- Update memory files.
- Send the report to Telegram in multiple sections with source links.
- Render a lightweight HTML idea board/report page.

## Explicit Non-Goals For MVP

- Full multi-source ingestion.
- Similarity-based duplicate detection.
- Database-backed storage.
- Editable web UI.
- Advanced trend charts.
- Strict external verification for every claim.
- Fully autonomous agents that run without fixed inputs and outputs.
