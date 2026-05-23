# Daily AI News Reporter

Build an agent-heavy daily reporter that tracks Reddit trends around AI, LLMs, AI use cases, technology, science, and world knowledge. The system should help discover useful ideas, understand important trends, and turn selected stories into structured reports and opportunity cards.

## Primary Goals

- Produce a structured daily report with about 7 high-signal stories.
- Prioritize trending Reddit discussions first, useful AI/use-case ideas second, and important global knowledge third.
- Send the report to Telegram in multiple readable sections with source links.
- Save local Markdown, HTML, and JSON archives for each report.
- Maintain a file-based idea board and memory system.

## MVP Sources

Core Reddit sources:

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

Fallback sources when daily signal is weak:

- `r/news`
- `r/worldnews`
- `r/science`
- `r/technology`

All source lists must be configurable so subreddits can be added or removed later.

## Agent Worker Stack

| Agent | Responsibility |
| --- | --- |
| Researcher | Fetch Reddit posts and collect source metadata. |
| Ranker | Score candidates using trend, relevance, idea potential, importance, confidence, and novelty. |
| Summarizer | Summarize selected stories clearly and concisely. |
| FactChecker | Label confidence and uncertainty; include original source links when available. |
| IdeaExtractor | Create opportunity cards from selected stories. |
| Editor | Assemble the final structured report. |
| Publisher | Send Telegram messages and write report files. |
| MemoryManager | Track seen stories, topics, ideas, preferences, source quality, and recurring entities. |

## Report Format

Each report should contain 5-10 stories, targeting 7 stories and a 3-5 minute reading time.

Each story should include:

- Title
- Summary
- Why it matters
- AI/use-case idea
- Trend signal
- Confidence level
- Uncertainty note
- Source links

Each story should also generate an opportunity card with:

- Idea
- Target user
- Problem solved
- Possible product or workflow
- Difficulty
- Status: `new`, `reviewed`, `saved`, `dismissed`, or `favorite`

## Memory And Storage

Use files only for the MVP:

- JSON for structured data, memory, reports, and idea cards.
- Markdown for readable report archives.
- HTML for report pages and the idea board.
- Log files for runs, errors, skipped stories, and Telegram delivery results.

Memory should track:

- Seen stories
- Repeated topics
- Generated ideas
- Source quality
- User preferences
- Recurring companies, models, tools, products, countries, and organizations

## Delivery And Visualization

- Send the report to Telegram in multiple sections/messages with links.
- Save every report locally as Markdown, HTML, and JSON.
- Build a lightweight visualization page focused first on the idea board, then archive browsing, then static report viewing.

## Schedule

- Run twice daily in Asia/Bangkok timezone.
- Morning and evening reports use the same structure.
- Evening reports should suppress duplicates unless a story has significantly changed or gained new momentum.

## AI Provider Strategy

- Use OpenRouter as the first provider.
- Keep the AI layer provider-agnostic so it can later support Qwen APIs, local LLMs, or another provider.
- Default to a balanced mode: deterministic Reddit ranking first, then AI for relevance scoring, summaries, confidence labels, and opportunity cards.
- Config should control provider, model, budget, story count, source lists, and AI depth.

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
