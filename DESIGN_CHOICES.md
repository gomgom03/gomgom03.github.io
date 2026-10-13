# Design choices

Notes on why the site at <https://gomgom03.github.io/> looks and works the way it does, so it's easy to update without undoing anything on purpose. Based on a survey of ~19 academic homepages (professors, postdocs, and Ph.D. students across CS, physics, biology, econ, and humanities) and Google's own search documentation, October 2026.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole site: content, styles, and search metadata in one file |
| `img/headshot.jpg` | 400×400 headshot, ~31 KB |
| `robots.txt` | Lets all crawlers in and points them to the sitemap |
| `sitemap.xml` | Lists the one page; bump `<lastmod>` when content changes |
| `README.md` | One-line description for the GitHub repo page |
| `.gitignore` | Keeps `cv.tex` out of commits |

`cv.tex` is intentionally **not** part of the site: it contains a phone number. `.gitignore` keeps it out of commits, so it stays on your machine only.

## Format: one hand-written HTML page

- **No framework or build step.** Templates like al-folio (Jekyll) and Hugo Blox are popular, but they need Ruby/Go toolchains, GitHub Actions, and break on upstream updates. A single file in the style of [Jon Barron's template](https://jonbarron.info/) never breaks and loads instantly.
- **When to switch:** if the publication list grows past ~10 entries and editing it by hand becomes tedious, al-folio generates it from a BibTeX file.
- **No external requests.** No web fonts, icon libraries, or analytics. This keeps the page fast and private for visitors.

## Layout and section order

Strong early-career sites let a visitor answer *who, where, what, how to reach* within a few seconds, then go deeper.

1. **Header:** name, one-line role, link row (Email · Google Scholar · GitHub), kept short on purpose, headshot on the right. On phones the photo moves above the name.
2. **About:** two short paragraphs in your own voice: a friendly opener with position, advisor, and research focus (generative models and molecular world models for proteins); then background, including undergraduate metagenomics and microbial genomics research with Dr. Todd Treangen and industry roles as one short clause ("I also worked as … at … and as … at …"), titles only, no task descriptions. Advisors are titled "Dr." to match the CV and shown as plain text, not links.
3. **Publications:** one combined list, newest first, covering papers, posters, and published abstracts. Non-paper items carry a small *Poster* or *Abstract* label so nothing is overstated. Own name in **bold**, venue in *italics*, with Paper/Code/Abstract links on the entry itself, not on a separate page.
4. **Awards:** NSF GRFP only, in its own section rather than buried in the bio. College honors (President's Honor Roll, Louise J. Walsh Scholarship) are deliberately left off to keep the list to major awards.

There is no separate Experience section: with only a few past roles, one sentence in the About reads better than a résumé-style list, and the CV holds the details.

## Visual style

- Single centered column, max 760px wide, 16px side margins on phones.
- Serif body font (Charter, the same family as the CV) from the system font stack, so nothing is downloaded.
- One accent color for links. Light and dark themes follow the visitor's system setting (`prefers-color-scheme`).
- Colors are CSS variables at the top of the `<style>` block (`--bg`, `--text`, `--muted`, `--accent`, `--rule`). Change them there, not inline.
- Link-row separators (`·`) sit after each item so a wrapped line never starts with a dot.

## Content decisions

| Decision | Reason |
|---|---|
| **No News section** | The most recent item would be July 2025. Old news makes a site look abandoned, which was the most common flaw in the survey. Add one back only when there are recent items and you'll keep it current (≤6 dated entries, newest first). |
| **No "last updated" date in the footer** | Same reason: it only helps if it stays fresh. The date lives in the metadata instead. |
| **Email visible** | Missing contact info was the second most common flaw (even on some well-known professors' pages). Uses `eddie.kim@rice.edu`, matching the CV. |
| **Phone number omitted** | Not needed on a public page. |
| **No Teaching section** | TA work isn't teaching your own course, so it's left off. Add the section when you're instructor of record. |
| **Only one Omi entry** | The ISMB/ECCB 2025 poster supersedes the earlier Rice undergraduate symposium poster on the same project. |
| **Omitted from the CV** | High-school awards (2020), the local AI video project, the VGF peptide course project. Add projects back if they get a public repo or write-up. |
| **No CV link yet** | Broken links hurt. Add `cv.pdf` (without the phone number) and add `<li><a href="cv.pdf">CV</a></li>` to the link row. |
## Search and discoverability

Ranked by impact; the first two matter far more than the rest.

1. **Real text on the page.** Name, Rice, role, and research topics appear in the `<title>`, `<h1>`, and body. Search engines index visible text.
2. **Links from trusted pages.** The site is found mainly through links. Make sure these point here:
   - [ ] Kavraki Lab People page (your name is currently plain text)
   - [ ] GitHub profile → Website field
   - [ ] ORCID → Websites & social links
   - [ ] Google Scholar → Homepage
3. **Consistent name.** The site uses "Eddie Kim"; Google Scholar uses "Eddie H. Kim". The metadata lists both (`alternateName`), but matching everywhere is better.
4. **Google Search Console + Bing Webmaster Tools.** The Google verification tag is in `<head>`; don't remove it or the site un-verifies. Sitemap URL: `https://gomgom03.github.io/sitemap.xml`.
5. **Structured data (JSON-LD).** A schema.org `ProfilePage` → `Person` block ties the site to Google Scholar and GitHub via `sameAs`. It must match what's visible on the page; when you add or remove a profile link, update both.
6. **Link previews.** Open Graph tags (`og:title`, `og:image`, …) control how the link looks in Slack, LinkedIn, iMessage, etc.
7. **AI search engines** need nothing extra. Google says normal SEO is enough for AI Overviews; `llms.txt` is unproven and skipped.

Skipped on purpose: meta keywords (ignored by Google), a custom domain (the old `coronaspreadsim.me` shows how they lapse; `github.io` never expires).

## Updating the site

- **New paper, poster, or abstract:** copy an `<li>` in the Publications list (add a `<span class="tag">Poster</span>` label if it isn't a paper), keep your name in `<strong>`, link the DOI (`https://doi.org/...`) and code. Newest first.
- **New profile link:** add it to the header link row **and** the `sameAs` list in the JSON-LD.
- **New photo:** square, resize to 400×400 before adding (`sips -Z 400 photo.jpg --out img/headshot.jpg` on macOS). Aim for under 100 KB.
- **After any content change:** update `dateModified` in the JSON-LD and `<lastmod>` in `sitemap.xml`.
- **Self-hosted paper PDFs** (if ever): give each its own page with `citation_title`, `citation_author`, `citation_publication_date`, and `citation_pdf_url` meta tags so Google Scholar picks it up.

## Publishing

GitHub Pages serves the `master` branch of this repo at <https://gomgom03.github.io/>. Pushing to `master` publishes; changes appear within a minute or two.

```sh
git add -A
git commit -m "Update site"
git push
```

`cv.tex` is ignored, so `git add -A` is safe. Any other private file needs a line in `.gitignore` before committing. Note that this repo is public, so anything committed (including this file) is visible on GitHub.
