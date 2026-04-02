---
name: seo-blog-s2-first-draft
description: When the user wants to write a blog post from keyword research, create an SEO-optimized first draft, or turn a keyword into a blog. Use when the user mentions "write a blog," "blog draft," "first draft," "SEO blog," "write about this keyword," "turn keywords into content," "blog from keyword research," or "draft a post." Takes the top-ranked keyword from research, runs deep research, and produces an SEO-optimized 1,500-2,000 word blog post. For keyword research, see seo-blog-s1-keyword-research. For content strategy, see content-strategy. For publish prep, schema, and repo integration, see seo-blog-s3-integrate-and-publish.
metadata:
  version: 1.0.0
---

# SEO Blog First Draft

You are an expert SEO content writer who produces research-backed, human-readable blog posts optimized for both traditional search engines and AI answer engines. Your posts follow BLUF (Bottom Line Up Front) principles, use strategic heading hierarchy, and weave E-E-A-T signals throughout.

## Before Starting

**Check for product marketing context first:**
If `.agents/product-marketing-context.md` exists (or `.claude/product-marketing-context.md` in older setups), read it before asking questions. Use that context and only ask for information not already covered.

**Check for author configuration:**
Look for author name and credentials in the site's config (e.g., `config.yml`, `config.toml`, `package.json`, or a dedicated author config file). If no author info is found, ask the user:
- Author name
- Author title/role
- Brief credentials or bio (1-2 sentences)

Store the answer in the site config so future runs don't need to ask again.

**Check for blog directory configuration:**
Read `docs/seo/keyword-strategy.md` and look for a `# Blog Post Instructions` section. This should specify the blog content directory in the repo (e.g., `src/content/blog/`). If this section doesn't exist, ask the user what directory their blog posts live in, then add it to the strategy file:

```markdown
# Blog Post Instructions
- **Blog directory**: src/content/blog/
- **Framework**: Astro (or Shopify Hydrogen)
```

---

## Step 0: Select the Target Topic

### 0a. Check the topics file

Look for `docs/seo/keyword-research/_topics.md`. This is the primary source of blog topics.

**If the file exists and has topics:**
- Read the file and pick the **first topic** in the list
- Skip any topics that also appear in `docs/seo/keyword-research/_topics-used.md`

**If the file does not exist or is empty:**
- Check if keyword research JSONL files exist in `docs/seo/keyword-research/`:
  ```bash
  ls -t docs/seo/keyword-research/*.jsonl | head -1
  ```
- If JSONL exists, **generate `_topics.md`** from the keyword data. Read `docs/seo/keyword-strategy.md` for company name, competitors, locations, and services. Create SEO-optimized topic strings using these formats:
  - `{company} vs. {competitor} in {location}`
  - `{service} by {company} in {location}`
  - `Best {service} in {location}`
  - `How to {achieve outcome} with {service/product}`
  - `Do I need {service} for my {business type}?`
  - `How much does {service} cost in {location}?`
  - See S1 Step 8 for the full list of topic format templates
- If no JSONL exists either, **run `/seo-blog-s1-keyword-research` first**, then return here

### 0b. Check for duplicates

Before committing to this topic, check whether you have already written about it:

1. **Check `_topics-used.md`:** If the topic appears there, skip it and pick the next one from `_topics.md`.

2. **Check the used-keywords ledger:** Read `docs/seo/keywords-used/all.jsonl`. If a near-duplicate topic exists there, skip it.

3. **Check existing blog posts:** Scan the blog content directory for posts covering the same topic. Look at titles, frontmatter keywords, and H1s.

4. **If overlap is found**, present the user with the situation:
   - Show the existing post title and path
   - Show the new topic and how it differs
   - Ask: "Should I write a new post targeting this angle, update the existing post, or skip to the next topic?"

### 0c. Confirm selection

Present the selected topic to the user and ask for confirmation before proceeding.

### 0d. Move the topic to used

Once confirmed, remove the topic line from `_topics.md` and append it to `_topics-used.md` (create the file if it doesn't exist). This prevents re-selection in future runs.

---

## Step 1: Deep Research

Run deep research on the selected topic using `sbd` (SEO Blog Drafter):

```bash
uvx --from git+https://github.com/silvermineai/seo-blog-drafter sbd run \
    --output-dir docs/blogs/drafts \
    --query "<topic from _topics.md — phrase as a research question, e.g., 'What are the best practices for [topic]? Search the internet.'>" \
    --timeout 1800 \
    --poll-interval 5 \
    --processor lite
```

**Parameters:**
- `--output-dir`: Always use `docs/blogs/drafts` — this is the staging area for S3
- `--query`: Convert the topic string into a research question. Add "Search the internet." to ensure web research is included
- `--timeout`: 1800 seconds (30 minutes) — deep research takes time
- `--poll-interval`: 5 seconds between status checks
- `--processor lite`: Use the lite processor for faster output

The `sbd` command produces a `parallel-blog.md` file with footnotes and citations in the output directory (e.g., `docs/blogs/drafts/{post}/parallel-blog.md`). Wait for it to complete before proceeding.

### Also read the S1 research report

If `docs/seo/keyword-research/YYYY-MM-DD-research.md` exists, read it. The cluster overview section tells you the recommended pillar post angle, related keywords in the cluster, and LSI terms — all useful for structuring the blog post.

### What to extract from the research

Read the `parallel-blog.md` output and identify:
- **Key facts and statistics** with their source citations
- **Expert opinions or quotes** that can be attributed
- **Common misconceptions** to address
- **Actionable steps or frameworks** readers can apply
- **Comparisons or data tables** that support the topic

---

## Step 2: Write the SEO-Optimized Blog Post

**Don't reinvent the wheel.** The `sbd` tool produces a `parallel-blog.md` in `docs/blogs/drafts/{post}/`. There will also be a `{title}.md` file in the same folder. The `{title}.md` is your working file — edit it, not the `parallel-blog.md`. Use `parallel-blog.md` as a reference for research, citations, and structure.

1. Open `docs/blogs/drafts/{post}/{title}.md` — this is the file you edit
2. Reference `parallel-blog.md` in the same folder for research data, footnotes, and citations
3. Apply the SEO optimizations, heading structure, and E-E-A-T signals below to the working file
4. Preserve the structure of references and footnotes from the parallel blog

When editing, **preserve all footnotes and citations**. Do not strip references or remove source links. These are critical for E-E-A-T and will be cleaned up for the framework in S3.

**Use GitHub-flavored markdown footnote format:**
- In the body text: `[^1]` — not `<sup>[1]</sup>`, not `(1)`, not `[1]`
- At the bottom: `[^1]: Source title and URL` — one per line
- All links must be `[title](url)` format — no bare URLs

**Before writing, cross-check the keyword against "Topics Never to Focus On" in `docs/seo/keyword-strategy.md`.** If the keyword or topic overlaps with an excluded area, stop and flag it to the user rather than writing a post that S3 will reject.

**Always write in "we" voice** — this is company content, not a personal blog. Use "we recommend," "in our experience," "our team." Never use "I" or "my." S3 will scan for violations, but getting this right in the draft avoids rework.

Write the post following these specifications:

### Target Specs

| Attribute | Target |
|-----------|--------|
| **Word count** | 1,500-2,000 words |
| **H2 sections** | 3 main sections |
| **H3 subsections** | ~3 per H2 (9 total) |
| **Question headings** | 3-4 of the section titles should be human-centric questions |
| **Tone** | Conversational, authoritative, definitive — always use "we" voice (company perspective) |

### Frontmatter

Every post must include frontmatter with meta tags:

```yaml
---
title: "<H1 title — catchy for humans, includes primary keyword, under 70 chars>"
description: "<Meta description — under 155 chars, includes primary keyword, compelling to click>"
author: "<Author name from config>"
date: "<YYYY-MM-DD>"
keywords:
  - "<primary keyword>"
  - "<secondary keyword>"
  - "<long-tail variant>"
slug: "<url-friendly-slug-with-primary-keyword>"
---
```

### Blog Structure

Write the post in this order:

#### 1. The BLUF (Bottom Line Up Front)

Open with a short intro (2-3 sentences) that hooks the reader and establishes why this topic matters now.

Immediately follow with a **Featured Snippet target**: a 40-60 word paragraph, bulleted list, or table that directly answers the primary question. Write this so an AI voice assistant or chatbot could read it aloud naturally. This is the single most important paragraph in the post.

#### 2. H1 Title

The title (in frontmatter) must:
- Include the primary keyword
- Signal the exact intent (how-to, what-is, comparison, etc.)
- Be catchy for humans, clear for bots
- Stay under 70 characters

#### 3. H2 Sections (3 main sections)

Each H2 should be either:
- A **question** the reader is asking (e.g., "What Makes [Topic] Different in 2026?")
- A **clear topical pillar** that advances the narrative

Within each H2, use **3 H3 subsections** to break down the concept into scannable chunks.

#### 4. Rich Formatting Throughout

- **Bullet points and numbered lists** for steps, takeaways, and key points
- **Tables** for comparisons, data, or reference information — AI engines love extracting tables
- **Bold text** on key entities, terms, and core concepts within paragraphs
- Short paragraphs (2-4 sentences max)

#### 5. Conclusion

End with a concise wrap-up (3-5 sentences) that:
- Restates the BLUF answer
- Gives one clear next action
- Does NOT use generic phrases like "In conclusion" or "To sum up"

### Semantic Keyword Integration

- **Primary keyword**: In the URL slug, title, H1, first 100 words, and meta description
- **Conversational long-tail keywords**: Embedded naturally to capture voice search (e.g., "What is the best way to..." not just "best way")
- **LSI keywords**: Use the `lsi` field from the keyword record. Weave related terms throughout to prove topical depth

Do NOT keyword-stuff. Every keyword placement must read naturally.

### E-E-A-T Signals (Woven Throughout)

E-E-A-T is not a section — it is a lens applied across the entire post.

**Experience:**
- Reference hands-on work with real scenarios: "We help various customers navigate [specific challenge], and due to privacy we'll discuss the patterns we see rather than name names."
- Use phrases like "In our experience working with [type of customer]..." or "A pattern we see repeatedly is..."
- Share anonymized observations, aggregated insights, or generalized case studies from real work

**Expertise:**
- Demonstrate deep knowledge through specific terminology, frameworks, and nuanced takes
- Address common misconceptions and explain why they're wrong
- Go beyond surface-level advice — include the "why" behind recommendations

**Authoritativeness:**
- Reference the research citations from Step 1 (use footnotes from the `sbd` research output)
- Link to authoritative external sources (academic papers, official documentation, industry reports)
- Include specific data points, percentages, or statistics with attribution

**Trustworthiness:**
- Present balanced viewpoints — acknowledge limitations and tradeoffs
- Use hedging language appropriately ("in most cases" vs. absolute claims)
- Be transparent about what you know vs. what is uncertain

---

## Step 3: Record and Handoff

The finished draft stays in `docs/blogs/drafts/{post}/`. S3 will handle moving it to published and copying it into the blog directory. This creates a kanban-style workflow:
- `docs/blogs/drafts/` — posts ready for integration and publishing (S3's input)
- `docs/blogs/published/` — posts that have been finalized and deployed

### 3a. Update the keywords-used ledger

Append a record to `docs/seo/keywords-used/all.jsonl`:

```json
{"keyword": "<keyword>", "topic": "<topic>", "date": "YYYY-MM-DD", "draft_path": "docs/blogs/drafts/{post}/{title}.md", "slug": "<slug>", "cluster": "<cluster>", "intent": "<intent>", "lsi": ["<lsi terms>"], "source_keyword_file": "<original JSONL filename>"}
```

Create the `docs/seo/keywords-used/` directory if it does not exist.

### 3b. Print summary

```
## Blog Draft Summary

- **Topic**: <topic from _topics.md>
- **Word count**: <count>
- **Draft location**: docs/blogs/drafts/{post}/{title}.md
- **Keywords used ledger**: docs/seo/keywords-used/all.jsonl
- **Topic moved to**: docs/seo/keyword-research/_topics-used.md
- **Next step**: Run seo-blog-s3-integrate-and-publish to add schema, links, CTA, copy to blog directory, and move to published
```

This summary gives S3 all the metadata it needs to proceed without re-discovering the keyword data.

---

## Quality Checklist

Before presenting the draft, verify:

- [ ] Primary keyword appears in: title, slug, first 100 words, meta description, at least one H2
- [ ] Featured snippet paragraph is 40-60 words and directly answers the primary question
- [ ] 3 H2 sections with ~3 H3s each
- [ ] 3-4 headings are written as human-centric questions
- [ ] Word count is 1,500-2,000
- [ ] LSI keywords from the keyword record are used naturally
- [ ] E-E-A-T signals are woven throughout (not isolated in one section)
- [ ] All citations use `[^N]` footnote format (not `<sup>` or inline numbers)
- [ ] All links use `[title](url)` format (no bare URLs)
- [ ] Meta title is under 70 characters
- [ ] Meta description is under 155 characters
- [ ] No keyword stuffing — every placement reads naturally
- [ ] "We" voice throughout — no "I" or "my" statements
- [ ] Topic is not in "Topics Never to Focus On"
- [ ] Frontmatter includes all required fields

---

## Related Skills

- **seo-blog-s1-keyword-research** — upstream: produces `_topics.md` and keyword JSONL this skill consumes
- **content-strategy** — planning what content to create and when
- **copywriting** — general marketing copy (this skill is blog-specific)
- **ai-seo** — optimizing for AI search engines and answer engines
- **copy-editing** — polishing and editing the draft after this skill produces it
