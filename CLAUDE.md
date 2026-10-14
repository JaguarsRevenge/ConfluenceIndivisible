# Confluence Indivisible website

Static site for Confluence Indivisible, a group in the Greater Wenatchee Valley, WA. Plain HTML/CSS: no build step, no framework, and only a tiny script for the "Copy link" buttons (plus the admin page).

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
cheatsheets/index.html         cheatsheet viewer: both cheatsheets on one page, chosen from a left-hand list
images/2026-general-election-cheatsheet-large.jpg  1530px render of the cheatsheet PDF, shown in the viewer
images/og-votenote-edition-N.jpg  1200x630 Facebook preview image for each issue
share/votenote-N.html          per-issue share page: Open Graph tags for Facebook previews, then redirects to the PDF
```

## Admin page (/admin/)

`admin/index.html` is a hidden, unlinked, noindex page (the only substantial JavaScript on the site; the public page has just the small "Copy link" script) for adding/editing events and publishing VoteNote issues. It is not truly server-protected, since GitHub Pages is static: a shared password decrypts (AES-GCM, PBKDF2) a GitHub token stored in `admin/config.json`, and the page commits to `main` through the GitHub API. First-time setup/password change is in the page itself (needs a fine-grained token with Contents: read and write). Use a long passphrase; the encrypted token is public. It edits `index.html` only between the `<!-- EVENTS:START/END -->`, `<!-- TILES:START/END -->` (Micro Activism tiles; text is edited as `[words](url)`, `**bold**`, `*italic*` markup) and `<!-- ISSUES:START/END -->` markers, so keep those markers. Events carry `data-date="YYYY-MM-DD"`. Untested against the live GitHub API as of creation; test after first deploy.

## Cheatsheet viewer (/cheatsheets/)

`cheatsheets/index.html` shows both cheatsheets in one place, large enough to read without clicking: a left-hand list selects which one fills the pane on the right (a two-tab strip on phones). There is no JavaScript. Two `position:fixed` anchor spans (`#chelan-douglas`, `#statewide`) act as the selector through CSS `:target` and sibling selectors; because they are fixed, choosing a sheet never scrolls the page. Chelan & Douglas shows by default and `cheatsheets/index.html#statewide` opens the other, which is how the main-page cards link in. Links to the viewer name `index.html` explicitly so they also work when files are opened from disk (a bare folder link shows a directory listing there). The large image is a render of the PDF; regenerate it when the PDF changes: `python -c "import fitz; fitz.open('guides/2026-general-election-cheatsheet.pdf')[0].get_pixmap(matrix=fitz.Matrix(2.5,2.5)).save('images/2026-general-election-cheatsheet-large.jpg', jpg_quality=84)"`. To add a cheatsheet: add an anchor span, a nav link and a `.viewer-pane`, then copy the `#statewide` rules in the "Cheatsheet viewer" block of `css/styles.css`.

## Content structure (index.html, top to bottom)

1. Header: logo and nav (Cheatsheet, Events, Take Action, VoteNote, Contact)
2. Hero: the **group** is the headline. VoteNote is only the newsletter; don't make it the main brand.
3. Gradient band: one-line call to action
4. `#cheatsheet`: Voter Cheatsheet (two `.issue.cheatsheet-card` cards: the Chelan & Douglas cheatsheet with the four voting steps and Read/Download buttons, then the statewide judges/initiatives/school bonds image). Kept near the top on purpose. Each card, and the "See both cheatsheets in one place" button, links to the viewer at `cheatsheets/` (`#chelan-douglas` or `#statewide` opens that one); the Download buttons still go straight to the file.
5. `#events`: Upcoming Events (`.event` list items with a `.date` badge)
6. `#act`: Micro Activism FOR THE WIN (`.tile` grid)
7. `#records`: Know the Record, linking to the NoTo candidate sites
8. `#votenote`: VoteNote newsletter archive (`.issue` items, newest first, with a "Latest" `.tag` on the newest)
9. `#contact`: Get Involved (elections@confluenceindivisible.org)

Section backgrounds alternate white and `--mist` (the `.micro` and `.alt` classes).

The Know Your Ballot section (`#resources`, a `.card` grid) was removed on Oct 14, 2026. The `.cards` / `.card` styles in `css/styles.css` are now unused; keep them if the section returns.

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
4. Facebook sharing: Facebook can't preview a PDF, so each issue has a `share/votenote-N.html` page (copy the previous one, change the number, date, summary, and image name). Make the preview image: crop the top of page 1 to 1.91:1 and resize to 1200x630 as `images/og-votenote-edition-N.jpg`. The "Copy link" button on each issue copies the URL of this page (the admin page generates it too; the small script is at the end of `index.html`); the URL to paste in Facebook is `https://confluenceindivisible.org/share/votenote-N.html`. After publishing, run it through https://developers.facebook.com/tools/debug/ to refresh Facebook's cache.
5. Pull the issue's new events, actions, and links into the Events, Micro Activism, and Know the Record sections. Extract the text and links with fitz: `page.get_text()` and `page.get_links()`.
6. Remove events whose dates have passed.

## Related sites (source in C:\voting)

| Site | Race | Local source |
|---|---|---|
| notobrianburnett.org | WA House LD12 Pos 1 | `C:\voting\NoToBrianBurnett` |
| notomikesteele.org | WA House LD12 Pos 2 (vs Maggie Adams) | `C:\voting\NoToMikeSteele` |
| notoshonsmith.org | Chelan County Commissioner Dist 2 (vs Clint Strand) | `C:\voting\NoToShonSmith` |

## Conventions

- Git commits use the GitHub noreply identity set in this repo's local config. Don't commit with a personal email.
- Everything in this repo is public, including this file.
- Check pages at desktop and phone widths. Headless Edge screenshots work: `msedge --headless=new --window-size=1280,3400 --screenshot=out.png file:///C:/ConfluenceIndivisible/index.html`. Edge won't render narrower than about 500px, so check phone layout at 500.
- Open items: the newsletter's "Talk to voters @ WVC" tile shows a volunteer's personal Gmail (confirm OK or switch to elections@). The hero description is placeholder wording.
