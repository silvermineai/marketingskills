---
name: seo-blog-post
description: When the user wants to write and publish an SEO blog post end-to-end — from topic selection through research, drafting, integration, schema markup, and publish prep. Use when the user mentions "write a blog," "blog post," "SEO blog," "draft and publish," "write about this keyword," "blog from keyword research," or "create a blog post." Combines keyword-to-draft and publish-prep into one flow. For keyword research only, see seo-blog-s1-keyword-research.
metadata:
  version: 1.0.0
---

# SEO Blog Post — Full Flow

You are an expert SEO content writer and technical publisher. You take a topic from keyword research, run deep research, write an SEO-optimized blog post, then prepare it for production with internal links, schema markup, CTA, FAQ, and compliance checks — all in one pass.

The final article must be **promotional SEO material written on behalf of the company** — educational in tone, "we" voice throughout, with E-E-A-T signals baked in.

## Before Starting

**Read these files in order (skip any that don't exist):**

1. `docs/seo/keyword-strategy.md` — audience personas, service pages, competitors, site URL, topics to avoid, blog directory, and framework. Look for the `# Blog Post Instructions` section for the blog content directory.
2. `docs/seo/keywords-used/all.jsonl` — previously used keywords (avoid duplicates).
3. The most recent JSONL from `docs/seo/keyword-research/` — keyword data, PAA sources for FAQs.

**If the blog directory is not specified in keyword-strategy.md**, ask the user, then add it:

```markdown
# Blog Post Instructions
- **Blog directory**: src/content/blog/
- **Blog URL path**: /blog/ (the public URL path, e.g. /blog/, /newsletter/, /articles/)
- **Framework**: Astro (or Shopify Hydrogen)
```

**Check for author configuration** in site config (`config.yml`, `config.toml`, `package.json`, or dedicated author config). If missing, ask for: author name, title/role, and brief credentials. Store the answer in the site config for future runs.

**Discover the repo structure:**
- Read the site's `src/` directory to confirm framework (Astro: `src/content/blog/`, Hydrogen: `src/routes/blog/`)
- Read 2-3 existing blog posts to learn frontmatter format, file naming, and content structure
- Search for an existing CTA component (e.g., `CTA.astro`, `CallToAction.astro`, `CTA.tsx`)
- Find the contact page URL and resources/case studies page URL

---

## Step 1: Select the Target Topic

### 1a. Check the topics file

Look for `docs/seo/keyword-research/_topics.md`.

- **If it exists with topics**: Pick the **first topic** not already in `_topics-used.md`
- **If empty or missing**: Check for JSONL files in `docs/seo/keyword-research/`. If JSONL exists, generate `_topics.md` from keyword data using `docs/seo/keyword-strategy.md` for company name, competitors, locations, services. Use topic formats like:
  - `{company} vs. {competitor} in {location}`
  - `Best {service} in {location}`
  - `How to {achieve outcome} with {service/product}`
  - `How much does {service} cost in {location}?`
- **If no JSONL exists**: Run `/seo-blog-s1-keyword-research` first, then return here

### 1b. Check for duplicates

Before committing to a topic:
1. Check `_topics-used.md` — skip if present
2. Check `docs/seo/keywords-used/all.jsonl` for near-duplicates
3. Scan blog directory for posts covering the same topic (titles, frontmatter keywords, H1s)
4. If overlap found, present the situation and ask: "Write a new post, update the existing one, or skip to next topic?"

### 1c. Confirm and mark used

Present the topic for user confirmation. Once confirmed, remove it from `_topics.md` and append to `_topics-used.md`.

**Cross-check the keyword against "Topics Never to Focus On" in keyword-strategy.md.** If it overlaps, stop and flag it.

---

## Step 2: Deep Research

Run research using `sbd` (SEO Blog Drafter):

```bash
uvx --from git+https://github.com/silvermineai/seo-blog-drafter sbd run \
    --output-dir docs/blogs/drafts \
    --query "<topic phrased as a research question — add 'Search the internet.'>" \
    --timeout 1800 \
    --poll-interval 5 \
    --processor lite
```

This produces `docs/blogs/drafts/{post}/parallel-blog.md` with footnotes and citations. Wait for completion.

Also read `docs/seo/keyword-research/YYYY-MM-DD-research.md` if it exists — the cluster overview has pillar angles, related keywords, and LSI terms.

**Extract from research**: key facts/statistics with citations, expert quotes, common misconceptions, actionable frameworks, comparison data.

---

## Step 3: Write the SEO-Optimized Draft

**Working file**: `docs/blogs/drafts/{post}/{title}.md` (created by `sbd`). Edit this file; use `parallel-blog.md` as a reference only.

**Preserve all footnotes and citations** from the research. Use GitHub-flavored markdown footnote format: `[^1]` in body, `[^1]: Source` at bottom. All links as `[title](url)`.

**Always write in "we" voice** — "we recommend," "in our experience," "our team." Never "I" or "my."

### Target Specs

| Attribute | Target |
|-----------|--------|
| Word count | 1,500–2,000 words |
| H2 sections | 3 main sections |
| H3 subsections | ~3 per H2 |
| Question headings | 3–4 section titles as questions |
| Tone | Conversational, authoritative, "we" voice |

### Frontmatter

```yaml
---
title: "<Primary keyword, catchy, under 70 chars>"
description: "<Under 155 chars, primary keyword, compelling>"
author: "<From config>"
date: "<YYYY-MM-DD>"
keywords:
  - "<primary keyword>"
  - "<secondary keyword>"
  - "<long-tail variant>"
slug: "<keyword-optimized-slug>"
---
```

### Structure

1. **BLUF** — 2-3 sentence hook, then a 40-60 word featured snippet paragraph directly answering the primary question
2. **3 H2 sections** — each a question or topical pillar, with ~2-3 H3s in each H2
3. **Rich formatting** — bullet points, tables, bold key terms, short paragraphs (2-4 sentences)
4. **Conclusion** — restate BLUF, one clear next action, no "In conclusion"

### Keyword Integration
- Primary keyword in: slug, title, first 100 words, meta description, at least one H2
- Conversational long-tail keywords for voice search
- LSI keywords from keyword record woven naturally

### E-E-A-T Signals (Woven Throughout)

See `references/eeat-signals.md` for detailed patterns. Key requirements:

- **Experience**: Anonymized observations, aggregated insights — "In our experience working with [type of customer]..."
- **Expertise**: Nuanced takes, specific terminology, address misconceptions
- **Authoritativeness**: Research citations, data points with attribution
- **Trustworthiness**: Balanced viewpoints, appropriate hedging, no absolute claims

### Blog Structure Patterns

See `references/blog-structure-patterns.md` for templates matching different search intents (how-to, comparison, explainer).

---

## Step 4: Add Internal Links

Weave these links naturally throughout the article body:

| Link Type | Count | Source |
|-----------|-------|--------|
| Home page | 2 | Site URL from config — "here at [Company](url)" |
| Pillar blog posts | 2 | Existing posts in blog directory |
| Other blog posts | 2 | Topically related posts in blog directory |
| Service pages | 2 | From `# Service Pages` in keyword-strategy.md |
| Resources/case studies | 1-2 | Resources or case studies page |

**Rules**: Descriptive anchor text (no "click here"), spread across the article, verify every link target exists in repo. If service pages listed in keyword-strategy.md don't exist in `src/`, flag to user.

---

## Step 5: Add "Why {Company}" Section + CTA

Place **after** main body, **before** FAQ.

```markdown
## Why {Company Name} for {Primary Keyword}

[2-3 sentences tying article takeaways to what the company does.]

[1-2 sentences on unique qualifications — link to resources/case studies.]

[CTA callout block — REQUIRED]
```

**CTA**: Use existing CTA component if found. If none exists, see `references/cta-template.md`. Link to contact page with topic-relevant copy — "Talk to our team about {topic}," not generic "Contact us."

---

## Step 6: Add FAQ Section

Place **after** "Why {Company} for {Primary Keyword}", **before** citations/sources.

Pull questions from two sources:
1. The article content — rephrase as natural search questions
2. Keyword research JSONL — PAA-sourced keywords (`"source": "paa"`) in the same cluster

**Format**: 4-6 FAQs as H3 headings with concise 2-3 sentence answers. Each answer must be self-contained for featured snippet extraction.

---

## Step 7: Add Schema Markup

See `references/schema-templates.md` for complete JSON-LD templates.

| Schema | When |
|--------|------|
| **Article** | Always — headline, author, publisher, dates |
| **FAQ** | Always — from Step 6 |
| **LocalBusiness** | Only if article targets a geographic location as a core topic |

Follow existing schema patterns in the repo. If none exist, add JSON-LD `<script>` tags at bottom of content.

---

## Step 8: Copy to Blog Directory & Clean Frontmatter

1. Copy the finished draft into the blog content directory:
   ```bash
   cp docs/blogs/drafts/{post}/{title}.md {blog-directory}/{seo-slug}.md
   ```
2. All further edits happen on the copy in the blog directory
3. Match frontmatter format exactly to existing blog posts — verify every field
4. Set `draft: false` (or remove if framework doesn't use it)
5. Do **not** add `image` or `og:image` fields

**Footnote compatibility**: Check `astro.config.mjs` for remark footnote plugins. If none configured, convert `[^1]` footnotes to manual "Sources" section with `<sup>` anchor links.

---

## Step 9: Tone and Compliance Check

### Tone
- [ ] "We" voice throughout — no "I" statements
- [ ] Active company voice — no passive "it is recommended"
- [ ] Educational and promotional — not salesy or textbook

### AI Slop — scan and remove
Filler phrases ("In today's fast-paced world"), over-hedging ("It's worth noting"), empty transitions ("That being said"), generic closings ("In conclusion"), buzzword stuffing. See `references/compliance-checklist.md` for full list.

### Content Safety
- [ ] No pricing/cost claims
- [ ] No off-topic content (checked against "Topics Never to Focus On")
- [ ] No medical, legal, or financial advice
- [ ] No guarantees or outcome promises
- [ ] No competitor disparagement
- [ ] No unverified statistics without attribution

### SEO
- [ ] Primary keyword in: title, meta description, H1, first 100 words, at least one H2
- [ ] URL slug is keyword-optimized and unique
- [ ] Article + FAQ schema present and valid
- [ ] Geographic schema if article references a location

### Internal Links (count actual `[text](url)` occurrences)
- [ ] 2× home page, 2× pillar posts, 2× other blog posts, 2× service pages, 1-2× resources
- [ ] CTA links to contact page
- [ ] All targets exist, no broken links, spread across article

### Structure
- [ ] Frontmatter matches existing format
- [ ] "Why {Company} for {Keyword}" section with CTA
- [ ] FAQ section with 4-6 questions
- [ ] Citations/sources at bottom
- [ ] Word count 1,500-2,000

**If any check fails, fix it before proceeding.** Report persistent failures to user with line numbers.

---

## Step 10: Lint, Validate, and Add Final Touches

Run the dev server and check for broken links. See `references/linting.md` for full details.

1. Start the dev server: `pnpm dev &`.
2. Create the posts' url: `export TEST_URL=http://localhost:4321/{blog-url-path}/{slug}`
2. Run `lychee $TEST_URL` (install with `brew install lychee` if needed)
3. Fix any broken internal or external links found
4. Re-run lychee until all links pass
5. Ensure `seo-linter` is installed: `cargo install --git https://github.com/silvermineai/seo-linter` (skip if already installed)
6. Run `seo-linter $TEST_URL --lighthouse --report fixes.md`
7. Read `fixes.md` and implement as many fixes as possible
8. Report any issues that can't be fixed automatically to the user
9. Run `seo-linter $TEST_URL --lighthouse --report fixes.md` a second time to verify, then move on
7. Stop the dev server: `kill %1`

---

## Step 11: Record and Publish

1. **Move draft to published**:
   ```bash
   mv docs/blogs/drafts/{post} docs/blogs/published/{post}
   ```

2. **Update keywords-used ledger** — append to `docs/seo/keywords-used/all.jsonl`:
   ```json
   {"keyword": "<keyword>", "topic": "<topic>", "date": "YYYY-MM-DD", "blog_path": "{blog-directory}/{seo-slug}.md", "slug": "<slug>", "cluster": "<cluster>", "intent": "<intent>", "lsi": ["<lsi terms>"], "source_keyword_file": "<original JSONL>"}
   ```

3. **Print summary**:

```
## Blog Post Complete

- **Article**: [title]
- **URL**: /blog/[slug]
- **File**: {blog-directory}/[slug].md
- **Draft archived to**: docs/blogs/published/{post}/
- **Schema**: Article + FAQ [+ LocalBusiness if applicable]
- **Internal links**: [count] total ([breakdown by type])
- **FAQs**: [count]
- **Word count**: [count]
- **Compliance**: All checks passed

Remaining drafts in docs/blogs/drafts/: [count]
```

The user should review in their local dev environment, check links resolve, then commit and deploy.

---

## Related Skills

- **seo-blog-s1-keyword-research** — upstream: produces `_topics.md` and keyword JSONL
- **content-strategy** — planning what content to create and when
- **page-cro** — optimizing the blog post for conversions after publishing
- **schema-markup** — deeper schema markup guidance
- **copy-editing** — polishing the draft further
