---
name: seo-blog-s3-integrate-and-publish
description: When the user wants to finalize a blog draft for publishing — add schema markup, internal links, CTAs, frontmatter, and integrate it into the site repo. Use when the user mentions "publish blog," "finalize blog," "prep for publishing," "add schema," "add internal links," "blog integration," "move to production," "publish prep," "make this blog live," or "clean up this draft." Takes the output from seo-blog-s2-first-draft and produces a publish-ready blog post in the site's blog directory. For keyword research, see seo-blog-s1-keyword-research. For writing the first draft, see seo-blog-s2-first-draft.
metadata:
  version: 1.0.0
---

# SEO Blog Publish Prep

You are an expert SEO content editor and technical publisher. Your goal is to take a blog draft from S2 and prepare it for production: clean up tone, add internal links, inject schema markup, add a "Why {Company}" section with CTA, add an FAQ section, run compliance checks, and integrate it into the site's blog directory.

The final article must be **promotional SEO material written on behalf of the company** — educational in tone, using "we" voice throughout, with E-E-A-T signals baked in.

## Prerequisites

Before starting, you need access to:

1. **The blog draft** — a folder in `docs/blogs/drafts/{post}/` containing the edited `{title}.md` (output from S2) and the `parallel-blog.md` research reference
2. **The keywords-used ledger** — `docs/seo/keywords-used/all.jsonl` with the `draft_path`, keyword metadata, and LSI terms for this post (written by S2)
3. **The site repo** — an Astro or Shopify Hydrogen project with a blog content directory in `src/`
4. **Keyword research** — the JSONL file from S1 at `docs/seo/keyword-research/`
5. **Keyword strategy** — `docs/seo/keyword-strategy.md` with service pages, audience, topics, and blog post instructions

If any of these are missing, stop and tell the user.

---

## Before Starting

**Read these files in order:**

1. `.agents/product-marketing-context.md` (or `.claude/product-marketing-context.md`) — company name, product, positioning
2. `docs/seo/keyword-strategy.md` — audience personas, service pages, competitors, site URL, topics to avoid, blog post instructions
3. `docs/seo/keywords-used/all.jsonl` — find the most recent entry to get the `draft_path`, keyword, cluster, intent, and LSI terms for this post
4. The blog draft at the `draft_path` from the ledger (in `docs/blogs/drafts/{post}/`)
5. The most recent JSONL from `docs/seo/keyword-research/` — for additional keyword data and FAQ generation from PAA sources

**Discover the repo structure:**

Read the `# Blog Post Instructions` section from `docs/seo/keyword-strategy.md` for the blog directory and framework. Then confirm by reading the site's `src/` directory:
- **Framework**: Astro (`src/content/blog/`) or Shopify Hydrogen (`src/routes/blog/` or similar)
- **Blog directory**: As specified in keyword-strategy.md — read 2-3 existing posts to learn the frontmatter pattern, file naming convention, and content structure
- **CTA component**: Search for an existing CTA component (e.g., `CTA.astro`, `CallToAction.astro`, `CTA.tsx`). If none exists, create one (see Step 5)
- **Contact page**: Find the contact/contact-us page URL
- **Resources/case studies page**: Look for `/resources`, `/case-studies`, `/recent-projects`, or similar
- **Site config**: Find the site URL and author info in the config file (e.g., `astro.config.mjs`, `site.config.ts`, or similar). Author info should already exist (S2 sets it up) — if missing, stop and ask the user to add it. Required for Article schema.

---

## Step 1: Copy Draft into Blog Directory

The draft is in `docs/blogs/drafts/{post}/` (a kanban-style staging area). Your job is to copy it into the live blog directory.

1. Read `docs/seo/keywords-used/all.jsonl` and find the most recent entry — the `draft_path` field points to the exact file
2. Read the draft file to confirm it exists and has content
3. Determine a keyword-optimal URL slug using the keyword data from the ledger entry. Check existing blog posts in the blog directory to avoid duplicate slugs
4. Copy the draft into the blog content directory (from `# Blog Post Instructions` in `keyword-strategy.md`):

```bash
cp docs/blogs/drafts/{post}/{title}.md {blog-directory}/{seo-slug}.md
```

5. All further edits happen on the copy in the blog directory, not the draft

---

## Step 2: Clean Up Frontmatter

Read 2-3 existing blog posts to match the exact frontmatter format. Then ensure the new post has:

| Field | Requirement |
|-------|-------------|
| `title` | Keyword-optimized, compelling, matches H1 |
| `description` | Keyword-optimized meta description. Include the primary keyword, location if relevant, and a compelling reason to click. Optimize for CTR, not a specific character count. |
| `date` | Today's date in the format used by existing posts |
| `author` | Pull from site config |
| `slug` | The keyword-optimal slug from Step 1 |
| `keywords` | Array of primary keyword, secondary keyword, and long-tail variant (should already exist from S2 — verify and expand if needed using LSI terms from the ledger) |
| `tags`/`categories` | Match existing taxonomy from other blog posts |
| `draft` | Set to `false` (or remove if the framework doesn't use it) |

Do **not** add an `image` or `og:image` field — skip for now.

Match every other frontmatter field that existing posts use. Do not invent new fields.

### Footnote and Link Format

Ensure all footnotes and links use standard markdown format:

- **All links** must be `[title](url)` format — no bare URLs, no HTML `<a>` tags, no reference-style links without definitions
- **All footnotes** must be standard markdown: `[^1]` in body text with `[^1]: source text` definitions at the bottom
- Convert any non-markdown citation formats (e.g., parenthetical references, numbered lists without footnote syntax, inline URLs) to proper `[^N]` footnotes

**Astro compatibility:**
- Standard markdown footnotes (`[^1]` / `[^1]: ...`) require `remarkGfm` or a remark footnotes plugin. Check `astro.config.mjs` for remark plugin configuration.
- If no footnote plugin is configured, convert footnotes to a manual "Sources" section at the bottom using numbered `<sup>` tags with anchor links (e.g., `<sup><a href="#ref1">1</a></sup>`)
- Ensure footnote syntax doesn't break MDX compilation — bare `[^1]` in `.mdx` files can cause build errors without a plugin
- Check that existing blog posts use a consistent citation format and match it exactly

---

## Step 3: Tone and Slop Check

Go through the entire article and fix:

### "We" Voice
- Replace all "I" statements with "we" — this is written on behalf of the company
- Replace passive/generic voice ("it is recommended") with active company voice ("we recommend")
- Ensure the tone is educational but promotional — we're experts sharing knowledge, not a textbook

### AI Slop Detection
Scan for and remove/rewrite:
- **Filler phrases**: "In today's fast-paced world," "It's worth noting that," "In conclusion," "Let's dive in," "Without further ado"
- **Over-hedging**: "It's important to remember that," "One might argue that," "It goes without saying"
- **Empty transitions**: "That being said," "With that in mind," "Moving on to"
- **Generic closings**: "In summary," "To wrap things up," "As we've seen"
- **Buzzword stuffing**: Excessive use of "leverage," "synergy," "cutting-edge," "game-changer," "revolutionize"
- **Nonsensical confidence**: Claims that sound authoritative but say nothing specific
- **Tonal inconsistency**: Sections that suddenly shift voice or reading level

Every sentence should earn its place. If a sentence could be deleted without losing information, delete it.

### Content Guardrails
Verify the article does **not** contain:
- Specific pricing or cost claims
- Off-topic content (check against "Topics Never to Focus On")
- Medical, legal, or financial advice
- Guarantees or promises of specific outcomes (e.g., "you will rank #1")
- Competitor disparagement
- Adult content or inappropriate material
- Misleading claims that could create legal liability
- Unverified statistics without attribution

The article must be **educational**. It teaches the reader something valuable and positions the company as the expert — it does not hard-sell.

---

## Step 4: Add Internal Links

Weave these links naturally into the body text. They should feel like helpful references, not forced insertions.

### Required Links

| Link Type | Count | How to Place |
|-----------|-------|-------------|
| **Home page** | 2 | Natural mentions like "here at [Company Name](site-url)" — pull site URL from config |
| **Pillar pages** | 2 | Link to related pillar/cornerstone content that exists in the blog |
| **Other blog posts** | 2 | Find 2 existing blog posts in `src/` that are topically related. Link with descriptive anchor text, not "click here" |
| **Service pages** | 2 | Match the article's topic to service pages listed in `docs/seo/keyword-strategy.md` under "# Service Pages". If the mapping is outdated or missing entries, ask the user to update it |
| **Resources/case studies** | 1-2 | Link to the resources or case studies page with anchor text like "see how we helped [client] achieve [result]" or "explore our case studies" |

### Link Quality Rules
- Use descriptive, keyword-relevant anchor text
- Spread links throughout the article — don't cluster them all in one section
- Links should add value for the reader, not just exist for SEO
- Verify every link target actually exists in the repo before adding it

### Service Page Validation
Before linking to service pages, confirm they exist in the repo. If `docs/seo/keyword-strategy.md` lists service pages that don't exist in `src/`, flag this to the user.

---

## Step 5: Add "Why {Company}" Section

Place this section **after** the main body content and **before** the FAQ section.

### Structure

```markdown
## Why {Company Name} for {Primary Keyword}

[2-3 sentences summarizing the article's key takeaways and how they connect to what the company does. Reference specific points from the article.]

[1-2 sentences on why the company is uniquely qualified — link to the resources/case studies page for evidence.]

[CTA callout block — REQUIRED, not optional]
```

The heading must include the primary keyword for SEO value (e.g., "Why Acme for Marketing Automation" not just "Why Acme"). This section **must** contain a CTA callout block — do not skip it.

### CTA Callout Block
Search the repo for an existing CTA component (e.g., `CTA.astro`, `CallToAction.astro`, `CTA.tsx`, `Cta.vue`).

**If a CTA component exists:** Use it with appropriate props. Read the component to understand its interface.

**If no CTA component exists:** Create one that matches the site's design system. For Astro:

```astro
---
interface Props {
  heading?: string;
  description?: string;
  buttonText?: string;
  buttonUrl?: string;
}

const {
  heading = "Ready to get started?",
  description = "Let's talk about how we can help.",
  buttonText = "Contact Us",
  buttonUrl = "/contact"
} = Astro.props;
---

<aside class="cta-block">
  <h3>{heading}</h3>
  <p>{description}</p>
  <a href={buttonUrl} class="cta-button">{buttonText}</a>
</aside>

<style>
  .cta-block {
    background: var(--color-surface, #f8f9fa);
    border-left: 4px solid var(--color-primary, #2563eb);
    padding: 1.5rem 2rem;
    margin: 2rem 0;
    border-radius: 0.5rem;
  }
  .cta-block h3 { margin-top: 0; }
  .cta-button {
    display: inline-block;
    padding: 0.75rem 1.5rem;
    background: var(--color-primary, #2563eb);
    color: white;
    text-decoration: none;
    border-radius: 0.375rem;
    font-weight: 600;
  }
</style>
```

Adapt the styling to match the existing design system. Read other components for CSS variable names and conventions.

The CTA should link to the contact page with copy relevant to the article topic — not generic "Contact us" but something like "Talk to our team about {topic}."

---

## Step 6: Add FAQ Section

Place **after** the "Why {Company}" section and **before** citations/sources.

### Generating FAQs

Pull from two sources:

1. **The blog content itself** — Extract 3-5 questions that the article answers. Rephrase as natural questions a searcher would type.
2. **The keyword research JSONL** — Look for PAA-sourced keywords (`"source": "paa"`) and keywords with question-format phrases in the same cluster. Use these as FAQ questions.

### Format

```markdown
## Frequently Asked Questions

### {Question 1}
{Concise 2-3 sentence answer. Reference the relevant section of the article.}

### {Question 2}
{Concise answer.}

...
```

Aim for 4-6 FAQs. Each answer should be self-contained (makes sense without reading the full article) because Google may pull these into featured snippets.

### FAQ Schema
Add FAQ structured data as JSON-LD. This goes in the frontmatter or wherever the framework injects `<head>` content — check how existing posts handle schema.

```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Question text here",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Answer text here"
      }
    }
  ]
}
```

---

## Step 7: Add Schema Markup

### Article Schema (Always)

Add Article structured data with author info pulled from the site config:

```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Article title",
  "description": "Meta description",
  "author": {
    "@type": "Person",
    "name": "Author name from config",
    "url": "Author URL from config"
  },
  "publisher": {
    "@type": "Organization",
    "name": "Company name",
    "url": "Site URL"
  },
  "datePublished": "2026-03-31",
  "dateModified": "2026-03-31"
}
```

### FAQ Schema (Always)
Already added in Step 6.

### Local Business / Geographic Schema (Conditional)

**Trigger:** Only add if the article references a specific geographic location (city, region, state, country) as a core topic — not just a passing mention.

If triggered, add LocalBusiness schema:

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "Company name",
  "url": "Site URL",
  "areaServed": {
    "@type": "Place",
    "name": "Geographic area referenced in article"
  }
}
```

### Schema Placement
Check how existing blog posts in the repo handle structured data:
- Astro: may use a `<script type="application/ld+json">` in a layout component, or a frontmatter field
- Hydrogen: may use a `<script>` tag in a route's meta function

Follow the existing pattern. If no pattern exists, add JSON-LD in a `<script>` tag at the bottom of the content.

---

## Step 8: Final Compliance Checklist

Run this checklist against the finished article. Every item must pass before publishing.

### Tone
- [ ] Article uses "we" voice throughout — no "I" statements
- [ ] No AI slop phrases remain (re-scan the full article)
- [ ] Tone is educational and promotional, not salesy or textbook-like

### Content Safety
- [ ] No specific pricing or cost claims
- [ ] No off-topic content (checked against "Topics Never to Focus On")
- [ ] No medical, legal, or financial advice
- [ ] No guarantees or promises of outcomes
- [ ] No competitor disparagement
- [ ] No adult content or inappropriate material
- [ ] No misleading claims or unverifiable statistics
- [ ] All external citations link to real, authoritative sources

### SEO
- [ ] Primary keyword appears in: title, meta description, H1, first 100 words, and at least one H2
- [ ] URL slug is keyword-optimized and unique (no duplicates in repo)
- [ ] Article schema is present and valid
- [ ] FAQ schema is present and valid
- [ ] Geographic schema added if article references a location

### Internal Linking
Scan the full article body for actual `[text](url)` hyperlinks. Count each type and fail if minimums aren't met.

- [ ] 2 links to home page — anchor text includes company name, href is site URL from config
- [ ] 2 links to pillar/cornerstone blog posts — href points to existing posts in blog directory
- [ ] 2 links to other blog posts — href points to existing posts in blog directory, topically related
- [ ] 2 links to service pages — href matches URLs from `# Service Pages` in keyword-strategy.md
- [ ] 1-2 links to resources/case studies page — href points to the resources or case studies URL
- [ ] CTA button links to contact page — inside the "Why {Company}" section
- [ ] All link targets verified to exist in the repo (grep for the path or check the file)
- [ ] No broken links — every `[text](url)` resolves to a real page
- [ ] Links are spread across the article body — not all clustered in one section
- [ ] Anchor text is descriptive and keyword-relevant — no "click here" or "learn more"

**If any link count is below the minimum, add more before proceeding.** This is the most commonly missed step — verify by counting actual `[text](/path)` occurrences in the markdown.

### Structure
- [ ] Frontmatter matches existing blog post format exactly
- [ ] "Why {Company} for {Keyword}" section present with keyword in heading
- [ ] CTA callout block present inside "Why {Company}" section
- [ ] FAQ section present with 4-6 questions
- [ ] Citations/sources section present at the bottom

Report any failures to the user with specific line numbers and suggested fixes.

---

## Step 9: Move to Published and Summarize

After the article passes all checks:

1. Move the draft folder from staging to published:

```bash
mv docs/blogs/drafts/{post} docs/blogs/published/{post}
```

This completes the kanban lifecycle: `drafts/` → `published/`. The folder preserves the `parallel-blog.md`, research output, and original draft as a record.

2. Update the keywords-used ledger entry to reflect the final blog path:

```bash
# Update the draft_path to blog_path in all.jsonl for this keyword
```

Replace `draft_path` with `blog_path` pointing to the final file in the blog directory (e.g., `src/content/blog/{seo-slug}.md`).

3. Confirm the finished article is in the site's blog directory.

4. Print a summary:

```
## Publish Prep Complete

- **Article**: [title]
- **URL**: /blog/[slug]
- **File**: {blog-directory}/[slug].md
- **Draft archived to**: docs/blogs/published/{post}/
- **Schema**: Article + FAQ [+ LocalBusiness if applicable]
- **Internal links**: [count] total ([breakdown by type])
- **FAQs**: [count]
- **Compliance**: All checks passed

Remaining drafts in docs/blogs/drafts/: [count]
```

---

## Handoff

This skill produces a publish-ready blog post integrated into the site repo. The user should:
1. Review the article in their local dev environment
2. Check that all links resolve correctly
3. Commit and deploy

For related skills:
- **seo-blog-s1-keyword-research** — keyword research (upstream)
- **seo-blog-s2-first-draft** — writing the draft (upstream)
- **schema-markup** — deeper schema markup guidance
- **page-cro** — optimizing the blog post for conversions after publishing
