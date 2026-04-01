# Keyword Research Report Template

Use this template to generate `docs/seo/keyword-research/YYYY-MM-DD-research.md` alongside the JSONL output.

---

```markdown
# Keyword Research Report — {YYYY-MM-DD}

## Research Parameters

| Parameter | Value |
|-----------|-------|
| **Date** | {YYYY-MM-DD} |
| **Site** | {site URL from keyword-strategy.md} |
| **Blog stage** | {New / Growing / Established} |
| **Max KD threshold** | {15 / 30 / 50} |
| **Seed keywords used** | {list of 3-5 seeds from "Topics to Focus On"} |
| **Competitors analyzed** | {list from keyword-strategy.md} |
| **Sources used** | {GSC, Ahrefs Keyword Explorer, Ahrefs Content Gap, PAA} |

---

## Executive Summary

{2-3 sentences: how many keywords found, how many are Tier 1, biggest opportunity cluster, and recommended first action.}

---

## Clusters Overview

{For each cluster, one subsection:}

### {Cluster Name}

- **Pillar keyword**: {highest-volume keyword in cluster} (vol: {X}, KD: {X})
- **Supporting keywords**: {count}
- **Total cluster volume**: {sum of all keyword volumes}
- **Avg KD**: {average KD across cluster}
- **Intent mix**: {e.g., 70% informational, 30% commercial}
- **LSI terms**: {comma-separated list of LSI keywords for this cluster}
- **Recommended pillar post**: {suggested title angle for the pillar content}
- **Quick wins**: {list Tier 1 keywords in this cluster}

---

## Top 10 Keywords by Opportunity

{Ranked table of the 10 best keywords, balancing volume, low KD, and business relevance:}

| Rank | Keyword | Volume | KD | Intent | Cluster | Tier | Source | Why |
|------|---------|--------|----|--------|---------|------|--------|-----|
| 1 | {keyword} | {vol} | {kd} | {intent} | {cluster} | {tier} | {source} | {1-sentence rationale} |
| ... | | | | | | | | |

---

## Source Breakdown

### GSC Striking Distance

- **Keywords found**: {count}
- **Skipped** (new blog): {yes/no}
- **Notable finds**: {2-3 most interesting striking distance keywords and why}
- **Optimization opportunities**: {keywords that should update existing posts, not create new ones}

### Ahrefs Keyword Explorer

- **Seed keywords searched**: {list}
- **Raw results before filtering**: {count}
- **After KD filter**: {count}
- **After word count filter**: {count}
- **After exclude/include filters**: {count}
- **Final keywords added**: {count}

### Ahrefs Content Gap

- **Competitors compared**: {list}
- **Gap keywords found**: {count}
- **Keywords where 2+ competitors rank**: {count}
- **Notable gaps**: {2-3 most surprising keywords competitors have that we don't}

### People Also Ask (PAA)

- **Seeds searched in Google**: {count} of {total seeds}
- **PAA questions captured**: {count}
- **Related searches captured**: {count}
- **Best question-format keywords**: {list top 3-5}

---

## Excluded Keywords

Keywords that looked promising by metrics but were excluded:

| Keyword | Volume | KD | Reason Excluded |
|---------|--------|----|-----------------|
| {keyword} | {vol} | {kd} | {e.g., "Listed in Topics Never to Focus On", "Off-topic", "Above KD threshold"} |

---

## Strategy Observations

{Flag anything the research surfaced that suggests the keyword strategy file should be updated:}

- **New topic opportunities**: {topics not in "Topics to Focus On" that show strong demand}
- **Topics to reconsider excluding**: {excluded topics that have surprisingly high volume/low KD}
- **Competitor insights**: {anything notable about competitor keyword strategies}
- **Gaps in service page coverage**: {keywords that map to services not listed in the strategy file}

---

## Files Generated

| File | Description |
|------|-------------|
| `docs/seo/keyword-research/{YYYY-MM-DD}-keywords.jsonl` | Full keyword list ({count} keywords) |
| `docs/seo/keyword-research/{YYYY-MM-DD}-research.md` | This report |

---

## Next Steps

1. Review Tier 1 keywords and confirm priority order
2. Run `seo-blog-s2-first-draft` to draft the first blog post
3. Update `docs/seo/keyword-strategy.md` if strategy observations warrant changes
```
