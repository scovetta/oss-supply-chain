---
name: Weekly Chapter Review
description: Reviews a random book chapter against recent events and verifies its citations.
on:
  schedule: weekly
  workflow_dispatch:

permissions:
  contents: read
  pull-requests: read
  copilot-requests: write

engine: copilot
strict: true
timeout-minutes: 45

network: defaults

tools:
  bash:
  edit:
  web-fetch:
  web-search:

pre-agent-steps:
  - name: Select a random chapter
    run: |
      mkdir -p /tmp/gh-aw/agent
      find contents -mindepth 2 -maxdepth 2 -type d -name 'chapter-*' \
        | shuf -n 1 > /tmp/gh-aw/agent/selected-chapter.txt
      test -s /tmp/gh-aw/agent/selected-chapter.txt
      echo "Selected $(cat /tmp/gh-aw/agent/selected-chapter.txt)"

safe-outputs:
  create-pull-request:
    title-prefix: "[weekly chapter review] "
    draft: true
    max: 1
    expires: 14d
  noop:
---

# Weekly Chapter Review

Review exactly one randomly selected chapter of the software supply chain
security book series and propose any necessary corrections in a draft pull
request.

The selected chapter directory is recorded in
`/tmp/gh-aw/agent/selected-chapter.txt`. Read that file first. Treat its value
as the only content directory you may modify. Review every Markdown file in
that directory, including its index.

## Review process

1. Read `CONTRIBUTING.md`, `CLAUDE.md`, the selected chapter, and the relevant
   book's `README.md` before making changes.
2. Identify claims that may have changed or become incomplete. Search for
   authoritative current sources and material events from the last twelve
   months that are directly relevant to the chapter. Prefer primary sources,
   including official advisories, standards, regulations, project
   announcements, and incident reports.
3. Check every external citation in the selected chapter:
   - Fetch the cited URL and confirm it remains reachable.
   - Confirm the source supports the nearby claim, statistic, quotation, date,
     and attribution. A reachable page alone is not sufficient.
   - Replace dead or redirected links with canonical authoritative URLs when
     available. Do not replace a strong source with an aggregator or
     lower-quality source.
   - Never invent a citation, quotation, statistic, incident, date, or source.
4. Correct verified factual mistakes, stale statements, broken citations, and
   materially outdated coverage. Add recent events only when they improve the
   chapter and can be supported by authoritative sources.
5. Keep edits focused and preserve the existing structure, voice, terminology,
   reference-link style, and section numbering. Do not modify any file outside
   the selected chapter directory.
6. Re-read every changed passage and fetch every new or replacement citation.
   If a claim cannot be verified confidently, do not present it as fact.
7. Review the final diff. If no defensible content or citation change is
   needed, use the `noop` safe output and do not open a pull request.

## Pull request

When changes are warranted, use the `create-pull-request` safe output. Follow
`.github/PULL_REQUEST_TEMPLATE.md`, mark applicable change types, and:

- Name the selected chapter and all changed section files.
- Summarize the current or recent events considered.
- List each factual mistake or stale statement found and how it was corrected.
- List citation repairs and the authoritative sources used to verify them.
- Call out any suspected issue that could not be verified or safely corrected.
- State that the changes were generated with AI and require human verification,
  as required by `CONTRIBUTING.md`.

Use a concise title that names the chapter. Do not claim checks passed unless
you actually ran them.
