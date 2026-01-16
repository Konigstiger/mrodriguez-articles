# mrodriguez-articles

A content-only repository for articles (Markdown + YAML frontmatter). This repo contains only markdown files and documentation — no app code. Use it as the canonical source of articles for your React site and Azure Functions.

Repository goals
- Single source-of-truth for articles, easy to edit in GitHub or via PRs.
- Clean frontmatter schema so your React app / Azure Function can reliably parse metadata.
- Filenames follow a predictable pattern for slugs, archives, and tooling.

Recommended structure
- content/
  - articles/
    - YYYY-MM-DD-slug.md        (article files)
  - drafts/
    - slug.md                   (optional drafts)
- docs/
  - FRONTMATTER.md             (schema & rules)
- README.md

Frontmatter (YAML) is required at the top of each article. See docs/FRONTMATTER.md for the schema and examples.

How to add an article
1. Create a new file under `content/articles/` using the pattern `YYYY-MM-DD-your-slug.md` (or `your-slug.md` and set `date:` in frontmatter).
2. Add the required YAML frontmatter (see `docs/FRONTMATTER.md`).
3. Write article content in Markdown below the frontmatter.
4. Open a PR to `main` if you use a review workflow.

Consumption options (pick one)
- Option A: React app fetches raw Markdown directly from GitHub (raw.githubusercontent.com).
  - Use when articles are public and you want a simple setup.
- Option B: React app lists and fetches via GitHub Content API (better listing, supports private repos with token).
  - Requires a server token or server-side proxy (recommended for private content).
- Option C: Use an Azure Function as a proxy + cache + markdown->HTML renderer.
  - Recommended if you want control over rate-limits, caching, transformation, or to keep GitHub token secret.

Security & caching
- For public repos, raw.githubusercontent.com is easiest.
- For private content, serve the content through Azure Functions using a GitHub token stored in Key Vault or app settings.
- Cache transformed HTML in the Azure Function (in-memory or Azure Cache / CDN) to reduce GitHub calls.

Examples and implementation guidance are below (in this README and docs/FRONTMATTER.md).
