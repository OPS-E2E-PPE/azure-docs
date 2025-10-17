# Copilot Instructions for Microsoft Learn

These instructions define a unified style and process standard for authoring and maintaining .NET documentation with GitHub Copilot or other AI assistance.

## 1. AI Usage & Disclosure
All Markdown content created or substantially modified with AI assistance must include an `ai-usage` front matter entry:
- `ai-usage: ai-generated` – AI produced the initial draft with minimal human authorship.
- `ai-usage: ai-assisted` – Human-directed, reviewed, and edited with AI support.
- Omit only for purely human-authored legacy content.
If missing during review, **add it**.

## 2. Terminology
- Use current product names; avoid legacy branding unless discussing history.
- Prefer precise nouns ("SDK", "runtime", "CLI") over vague phrases ("the system").

## 3. Writing Style (Core Principles)
See the Microsoft Writing Style Guide: <https://learn.microsoft.com/style-guide/welcome/>. Baseline refinements:
- **Voice:** Active. **Person:** Second person (“you”).
- **Tone:** Direct, concise, instructive; professional but approachable.
- **Tense:** Present for behavior, future only for planned changes.
- **Imperative form** for steps: “Run the command”, not “You should run…”.
- Use **“might”** (possibility) and **“can”** (capability); avoid “may”.
- Avoid “we”/“our” when referring to docs authors or product teams.
- Avoid filler (“basically”, “simply”, “just”) and hedging (“probably”, “somewhat”).

## 4. Structure & Formatting
- **Headings:** Sentence case. No gerunds in titles unless describing an ongoing concept.
- Avoid heading jumps (don’t go from H2 to H4). No consecutive headings without explanatory text.
- **Lists:**
  - Ordered lists: write every item as `1.` (auto-numbering handled by renderer).
  - Unordered lists: use `-` (or `*` if repository-specific style requires). Keep consistent per file.
  - Each list item: Complete sentence with ending punctuation if >3 words or if it contains a verb.
  - Don’t end with “etc.” — instead trim or use “For example:”.
- **Paragraphs:** Keep lines short (≤ 100 chars) when practical for diff readability.

## 5. Formatting Conventions
| Element | Format |
|---------|--------|
| UI labels / button names | **Bold** |
| Code identifiers, filenames, paths, package IDs, literal values | `inline code` |
| Non-localizable tokens / placeholders | `{UPPERCASE_WITH_UNDERSCORES}` or `{PORT}` style |
| API cross-refs | `<xref:api-doc-ID>` |
| Raw URLs (when shown as literal) | `<https://example.com>` |
| External Learn links | Drop host + locale: `/dotnet/core/introduction` |

Use relative links inside the same repo. Never link to the physical `.md` path in content destined for Learn—use `xref` for published docs.

## 6. Links & Cross-References
- Remove locale segments (`/en-us/`, `/fr-fr/`, etc.) from Learn links.
- Prefer `<xref:...>` for published topics; ensure the target UID exists.
- For GitHub-only docs (like README), full URLs to `.md` files are acceptable.
- External standards (MDN, W3C): Use canonical URL without language segment.

## 7. API Reference Identifiers
When adding `<xref:api-doc-ID>`:
1. Find the type/member in `dotnet/dotnet-api-docs` XML.
2. Use the `Value` of `<TypeSignature>` or `<MemberSignature>` where `Language="DocId"`.
3. Strip the leading two characters (e.g., remove `T:` or `M:`). Keep the remainder exactly.
4. If unresolved, note uncertainty rather than guessing.

## 8. Redirections (When Deleting Articles)
If a published Markdown article (not an include) is removed:
1. Add entry to `.openpublishing.redirection.json`.
2. Use `source_path` matching the deleted path.
3. Prefer `redirect_url` to the most relevant successor or parent topic.
4. Maintain JSON syntax and ordering (alphabetical by `source_path` if repo standard).

## 9. Metadata Essentials
Front matter should (at minimum) include: `title`, `description` (concise, task-oriented), `ms.date` (MM/DD/YYYY), and any required repository fields. Update `ms.date` when substantial content changes (≥ ~50 changed chars).

## 10. Placeholders & Localization
- Use `{PLACEHOLDER}` (uppercase) in URIs and code. Document meaning inline or in adjacent text.
- Avoid embedding culturally specific references (dates, region-specific slang).
- Don’t hardcode locale in URLs.

## 11. Accessibility Considerations
- Provide meaningful alt text for images (describe purpose, not “screenshot of…” unless essential).
- Avoid directing by color alone (“Click the green button”).
- Use tables only for tabular data (not layout).
- Use semantic heading order for navigability.

## 12. Content Patterns to Prefer
| Pattern | Use When | Notes |
|---------|----------|-------|
| Concept → Procedure → Reference | Introductory flows | Helps new users ramp logically |
| Problem → Solution | Troubleshooting topics | Start with symptom summary |
| Compare approaches table | Alternate APIs / strategies | Keep neutral tone |

## 13. Copilot Behavioral Guidance
When generating or modifying content:
1. **Summarize intent** of an issue or change before drafting (internally – optional to output unless asked).
2. Prefer minimal diffs; preserve existing viable structure.
3. Flag uncertainty instead of hallucinating (e.g., “Unable to verify API doc ID for X”).
4. Offer 1–2 alternate phrasings only when clarity genuinely improves.
5. Avoid reformatting stable sections unless required for consistency.

## 14. Versioning & Future-Proofing
- Avoid hardcoding pre-release terms unless the article’s focus is that preview.
- When referencing future features, clearly mark with a note (e.g., > [!NOTE] Feature available in .NET X Preview). Remove or revise upon RTM.
