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

---

## Step 0: Select the Target Keyword

### 0a. Load the keyword research

Find the latest JSONL file in `docs/seo/keyword-research/`:

```bash
ls -t docs/seo/keyword-research/*.jsonl | head -1
```

If the directory does not exist or contains no JSONL files, **run the `/seo-blog-s1-keyword-research` skill first**, then return here.

Parse the file to extract the highest-tier keyword (Tier 1 first, then Tier 2, then Tier 3). Within a tier, prefer higher volume and lower KD. Use `jq` to extract and sort:

```bash
cat <latest-file>.jsonl | jq -s 'sort_by(.tier, -.volume, .kd) | .[0]'
```

### 0b. Check for duplicates

Before committing to this keyword, check whether you have already written about it:

1. **Check the used-keywords ledger:** Read `docs/seo/keywords-used/all.jsonl`. If the keyword or a near-duplicate topic exists there, skip it and pick the next keyword from 0a.

2. **Check existing blog posts:** Scan the blog content directory for posts covering the same topic. Look at titles, frontmatter keywords, and H1s. Ask yourself:
   - Have I written about this exact keyword before?
   - Can I expand upon an earlier blog post or extend the content in a new way?

3. **If overlap is found**, present the user with the situation:
   - Show the existing post title and path
   - Show the new keyword and how it differs
   - Ask: "Should I write a new post targeting this angle, update the existing post, or skip to the next keyword?"

### 0c. Confirm selection

Present the selected keyword to the user with its metadata (volume, KD, intent, topic, cluster, notes) and ask for confirmation before proceeding.

### 0d. Move the keyword to the used file

Once confirmed, move the keyword record from the source JSONL to `docs/seo/keyword-research/YYYY-MM-DD-used.jsonl` (today's date). Remove it from the original file so it is not picked again.

---

## Step 1: Deep Research

Run deep research on the selected keyword/topic using `tools2`:

```bash
tools2 <keyword-or-topic> [--output-dir <directory>]
```

**TODO: Update this section with the exact `tools2` CLI signature once available.**

The `tools2` command produces a markdown file with footnotes and citations. Note the output directory — the final blog post will be saved there.

If `tools2` specifies an output directory, use it. If not, check if `docs/seo/keyword-strategy.md` specifies a blog output directory. If neither exists, ask the user where to save the blog post.

### What to extract from the research

Read the research output and identify:
- **Key facts and statistics** with their source citations
- **Expert opinions or quotes** that can be attributed
- **Common misconceptions** to address
- **Actionable steps or frameworks** readers can apply
- **Comparisons or data tables** that support the topic

---

## Step 2: Write the SEO-Optimized Blog Post

Using the research from Step 1, write a blog post following these specifications:

### Target Specs

| Attribute | Target |
|-----------|--------|
| **Word count** | 1,500-2,000 words |
| **H2 sections** | 3 main sections |
| **H3 subsections** | ~3 per H2 (9 total) |
| **Question headings** | 3-4 of the section titles should be human-centric questions |
| **Tone** | Conversational, authoritative, definitive |

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
- Reference the research citations from Step 1 (use footnotes from the `tools2` output)
- Link to authoritative external sources (academic papers, official documentation, industry reports)
- Include specific data points, percentages, or statistics with attribution

**Trustworthiness:**
- Present balanced viewpoints — acknowledge limitations and tradeoffs
- Use hedging language appropriately ("in most cases" vs. absolute claims)
- Be transparent about what you know vs. what is uncertain

---

## Step 3: Save and Record

### 3a. Save the blog post

Save the finished blog post as a markdown file in the same directory where `tools2` saved the research output. Use the slug from the frontmatter as the filename:

```
<output-dir>/<slug>.md
```

### 3b. Update the keywords-used ledger

Append a record to `docs/seo/keywords-used/all.jsonl`:

```json
{"keyword": "<keyword>", "topic": "<topic>", "date": "YYYY-MM-DD", "blog_path": "<relative path to blog file>", "slug": "<slug>", "cluster": "<cluster>", "source_keyword_file": "<original JSONL filename>"}
```

Create the `docs/seo/keywords-used/` directory if it does not exist.

### 3c. Print summary

```
## Blog Draft Summary

- **Keyword**: <keyword>
- **Topic**: <topic>
- **Word count**: <count>
- **Saved to**: <file path>
- **Keywords used ledger**: docs/seo/keywords-used/all.jsonl
- **Keyword moved to**: docs/seo/keyword-research/YYYY-MM-DD-used.jsonl
- **Next step**: Run the publishing/integration skill to finalize
```

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
- [ ] All citations from research are preserved with footnotes
- [ ] Meta title is under 70 characters
- [ ] Meta description is under 155 characters
- [ ] No keyword stuffing — every placement reads naturally
- [ ] Frontmatter includes all required fields

---

## Related Skills

- **seo-blog-s1-keyword-research** — upstream: produces the keyword JSONL this skill consumes
- **content-strategy** — planning what content to create and when
- **copywriting** — general marketing copy (this skill is blog-specific)
- **ai-seo** — optimizing for AI search engines and answer engines
- **copy-editing** — polishing and editing the draft after this skill produces it
