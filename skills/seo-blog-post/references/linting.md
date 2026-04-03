# Blog Post Linting

After copying the blog post into the blog directory, validate it by running the dev server and checking for broken links.

## 1. Start the dev server

```bash
pnpm dev &
```

Wait for the server to be ready (listen for the "Local:" URL in output, typically `http://localhost:4321`).

## 2. Run lychee link checker

```bash
lychee http://localhost:4321/blog/{slug}
```

If `lychee` is not installed:

```bash
brew install lychee
```

## 3. Fix errors

Review lychee output for:
- **Broken internal links** — the target page doesn't exist. Fix the href or remove the link.
- **Broken external links** — the URL is dead or returns an error. Replace with a working source or remove.
- **Timeout errors** — may be transient. Retry once before removing.

Re-run lychee after fixes until all links pass.

## 4. Stop the dev server

```bash
kill %1
```
