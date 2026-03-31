# Schema Markup Templates

JSON-LD templates for blog post structured data. Copy and adapt these for each post.

## Article Schema

Required for every blog post.

```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Your Blog Post Title Here",
  "description": "Meta description from frontmatter",
  "author": {
    "@type": "Person",
    "name": "Author Name",
    "url": "https://author-profile-url.com",
    "jobTitle": "Author Title"
  },
  "publisher": {
    "@type": "Organization",
    "name": "Company Name",
    "url": "https://company-site.com",
    "logo": {
      "@type": "ImageObject",
      "url": "https://company-site.com/logo.png"
    }
  },
  "datePublished": "2026-03-31",
  "dateModified": "2026-03-31",
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "https://company-site.com/blog/post-slug"
  }
}
```

### Required Fields
- `headline` — must match the H1/title
- `author` — pull from site config; must be a real person for E-E-A-T
- `publisher` — the company
- `datePublished` — today's date
- `dateModified` — same as published for new posts

### Optional but Recommended
- `author.jobTitle` — strengthens E-E-A-T
- `publisher.logo` — skip if no logo URL available
- `mainEntityOfPage` — canonical URL of the post

## FAQ Schema

Required for every blog post that has an FAQ section.

```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is [topic]?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Concise answer that stands alone. Can include basic HTML like <strong> and <a href>."
      }
    },
    {
      "@type": "Question",
      "name": "How do you [action]?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Another standalone answer."
      }
    }
  ]
}
```

### Rules
- Each FAQ answer must be self-contained (makes sense without reading the article)
- Answers can include basic HTML: `<strong>`, `<em>`, `<a href>`, `<br>`, `<ul>`, `<li>`
- Keep answers concise: 2-4 sentences
- Use 4-6 FAQs per post
- Questions should mirror how real users search (check PAA data)

## LocalBusiness Schema

Only add when the article references a specific geographic location as a core topic.

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "Company Name",
  "url": "https://company-site.com",
  "telephone": "+1-555-000-0000",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "123 Main St",
    "addressLocality": "Austin",
    "addressRegion": "TX",
    "postalCode": "78701",
    "addressCountry": "US"
  },
  "areaServed": {
    "@type": "Place",
    "name": "Austin, Texas"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": "30.2672",
    "longitude": "-97.7431"
  }
}
```

### When to Use
- Article targets "[service] in [city]" or "[city] [service]" keywords
- Article discusses location-specific advice or services
- The company has a physical presence in the referenced area

### When NOT to Use
- Article mentions a city in passing (e.g., "like companies in Austin")
- Article is about a general topic that happens to use a location as an example
- The company has no connection to the referenced geography

### Fields
- `address` — only include if the company has a real address there; omit if virtual
- `telephone` — only include if publicly listed
- `areaServed` — the geographic area the article focuses on
- `geo` — coordinates of the area; omit if not a physical location article

## Combining Multiple Schemas

When a post needs Article + FAQ + LocalBusiness, combine them in a single `<script>` tag using an array:

```json
<script type="application/ld+json">
[
  {
    "@context": "https://schema.org",
    "@type": "Article",
    ...
  },
  {
    "@context": "https://schema.org",
    "@type": "FAQPage",
    ...
  },
  {
    "@context": "https://schema.org",
    "@type": "LocalBusiness",
    ...
  }
]
</script>
```

Or use separate `<script>` tags — both are valid. Check which pattern the site already uses.
