# Confluence Indivisible website

Static site for Confluence Indivisible, a group in the Greater Wenatchee Valley, WA. Plain HTML/CSS: no build step, no framework, no JavaScript.

- **Live:** https://confluenceindivisible.org/ (the old https://jaguarsrevenge.github.io/ConfluenceIndivisible/ address redirects here)
- **Repo:** https://github.com/JaguarsRevenge/ConfluenceIndivisible
- **Hosting:** GitHub Pages, deploy from branch `main`, folder `/ (root)`. Pushing to `main` publishes in about a minute. `.nojekyll` is present so files are served as-is.
- **Custom domain:** `confluenceindivisible.org`, set by the `CNAME` file (same pattern as the NoTo sites). HTTPS is live with Enforce HTTPS on (certificate issued Oct 5, 2026 after removing and re-adding the domain in Settings > Pages; if it ever breaks, do the same). GitHub renews the certificate automatically. `www` redirects to the apex.
- **Pushing:** git over HTTPS uses username `JaguarsRevenge` and a fine-grained personal access token (Contents: read and write on this repo) as the password. The GitHub account signs in with Google, which git can't use. The token is saved by `credential.helper store`. Prompts for a username and token can't be answered from inside Claude Code, so run the first push in a separate terminal tab.

## Files

```
index.html                     the whole site (single page)
css/styles.css                 all styles; brand tokens on :root
images/logo.png                group logo (from the newsletter; low-res 260x246, replace if a better original turns up)
images/votenote-edition-N.png  newsletter cover thumbnails
images/2026-general-election-cheatsheet.png  cheatsheet thumbnail
guides/2026-general-election-cheatsheet.pdf  Voter Cheatsheet (one-page key races guide)
newsletters/votenote-edition-N.pdf  VoteNote issues (clickable PDFs, links inside work)
images/2026-general-election-judges-initiatives.jpg  statewide cheatsheet (Supreme Court, initiatives, school bonds); second card in #cheatsheet
images/og-votenote-edition-N.jpg  1200x630 Facebook preview image for each issue
share/votenote-N.html          per-issue share page: Open Graph tags for Facebook previews, then redirects to the PDF
```

## Admin page (/admin/)

`admin/index.html` is a hidden, unlinked, noindex page (the only JavaScript on the site) for adding/editing events and publishing VoteNote issues. It is not truly server-protected, since GitHub Pages is static: a shared password decrypts (AES-GCM, PBKDF2) a GitHub token stored in `admin/config.json`, and the page commits to `main` through the GitHub API. First-time setup/password change is in the page itself (needs a fine-grained token with Contents: read and write). Use a long passphrase; the encrypted token is public. It edits `index.html` only between the `<!-- EVENTS:START/END -->` and `<!-- ISSUES:START/END -->` markers, so keep those markers. Events carry `data-date="YYYY-MM-DD"`. Untested against the live GitHub API as of creation; test after first deploy.

## Content structure (index.html, top to bottom)

1. Header: logo and nav (Cheatsheet, Events, Take Action, VoteNote, Contact)
2. Hero: the **group** is the headline. VoteNote is only the newsletter; don't make it the main brand.
3. Gradient band: one-line call to action
4. `#cheatsheet`: Voter Cheatsheet (two `.issue.cheatsheet-card` cards: the Chelan & Douglas cheatsheet with the four voting steps and Read/Download buttons, then the statewide judges/initiatives/school bonds image). Kept near the top on purpose.
5. `#events`: Upcoming Events (`.event` list items with a `.date` badge)
6. `#act`: Micro Activism FOR THE WIN (`.tile` grid)
7. `#records`: Know the Record, linking to the NoTo candidate sites
8. `#resources`: Know Your Ballot (`.card` grid)
9. `#votenote`: VoteNote newsletter archive (`.issue` items, newest first, with a "Latest" `.tag` on the newest)
10. `#contact`: Get Involved (elections@confluenceindivisible.org)

Section backgrounds alternate white and `--mist` (the `.micro` and `.alt` classes).

## Brand (taken from the VoteNote newsletter)

| Token | Hex | Use |
|---|---|---|
| `--red` | `#c64a4d` | "Confluence", primary buttons, accents |
| `--blue` | `#77a0bd` | "Indivisible", action tiles |
| `--navy` | `#34445d` | headings, dark panels, footer |
| `--charcoal` | `#3f3d3a` | section title bars, card headers |
| `--red-soft` | `#d97677` | slogan circle |
| `--blush` / `--peach` | `#e6a8a3` / `#fbc6ad` | tile headings, gradient end |
| `--link` | `#07296f` | links on light/blue backgrounds |

Fonts (Google Fonts): **Oswald** 700 for display/wordmarks, **League Spartan** for the slogan, **Montserrat** for body text (standing in for the newsletter's Kief Montaser), **Playwrite US Modern** for handwritten-style intros, **Sanchez** for small caps labels.

## Weekly VoteNote update (every Tuesday until election day, Nov 3, 2026)

1. Copy the PDF to `newsletters/votenote-edition-N.pdf`.
2. Make a thumbnail with PyMuPDF, which is installed:
   `python -c "import fitz; fitz.open('newsletters/votenote-edition-N.pdf')[0].get_pixmap(dpi=45).save('images/votenote-edition-N.png')"`
3. Add a new `.issue` at the top of `#votenote`, move the "Latest" tag onto it, and write a one-line summary.
4. Facebook sharing: Facebook can't preview a PDF, so each issue has a `share/votenote-N.html` page (copy the previous one, change the number, date, summary, and image name). Make the preview image: crop the top of page 1 to 1.91:1 and resize to 1200x630 as `images/og-votenote-edition-N.jpg`. The "Share link" button on each issue points at this page; the URL to paste in Facebook is `https://confluenceindivisible.org/share/votenote-N.html`. After publishing, run it through https://developers.facebook.com/tools/debug/ to refresh Facebook's cache.
5. Pull the issue's new events, actions, and links into the Events, Micro Activism, and Know Your Ballot sections. Extract the text and links with fitz: `page.get_text()` and `page.get_links()`.
6. Remove events whose dates have passed.

## Related sites (source in C:\voting)

| Site | Race | Local source |
|---|---|---|
| notobrianburnett.org | WA House LD12 Pos 1 | `C:\voting\Brian Burnett\site-today` |
| notomikesteele.org | WA House LD12 Pos 2 (vs Maggie Adams) | `C:\voting\NoToMikeSteele` |
| notoshonsmith.org | Chelan County Commissioner Dist 2 (vs Clint Strand) | `C:\voting\NoToShonSmith` |

## Conventions

- Git commits use the GitHub noreply identity set in this repo's local config. Don't commit with a personal email.
- Everything in this repo is public, including this file.
- Check pages at desktop and phone widths. Headless Edge screenshots work: `msedge --headless=new --window-size=1280,3400 --screenshot=out.png file:///C:/ConfluenceIndivisible/index.html`. Edge won't render narrower than about 500px, so check phone layout at 500.
- Open items: the newsletter's "Talk to voters @ WVC" tile shows a volunteer's personal Gmail (confirm OK or switch to elections@). The hero description is placeholder wording.
