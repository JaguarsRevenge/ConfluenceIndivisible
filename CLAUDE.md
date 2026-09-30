# Confluence Indivisible website

Static site for Confluence Indivisible, a group in the Greater Wenatchee Valley, WA. Plain HTML/CSS: no build step, no framework, no JavaScript.

- **Live:** https://jaguarsrevenge.github.io/ConfluenceIndivisible/
- **Repo:** https://github.com/JaguarsRevenge/ConfluenceIndivisible
- **Hosting:** GitHub Pages, deploy from branch `main`, folder `/ (root)`. Pushing to `main` publishes in about a minute. `.nojekyll` is present so files are served as-is.
- **Custom domain:** not set up yet. When it is, add a `CNAME` file containing the domain (same pattern as the NoTo sites) and set the DNS records.

## Files

```
index.html                     the whole site (single page)
css/styles.css                 all styles; brand tokens on :root
images/logo.png                group logo (from the newsletter; low-res 260x246, replace if a better original turns up)
images/votenote-edition-N.png  newsletter cover thumbnails
newsletters/votenote-edition-N.pdf  VoteNote issues (clickable PDFs, links inside work)
```

## Content structure (index.html, top to bottom)

1. Header: logo and nav (Events, Take Action, VoteNote, Contact)
2. Hero: the **group** is the headline. VoteNote is only the newsletter; don't make it the main brand.
3. Gradient band: one-line call to action
4. `#events`: Upcoming Events (`.event` list items with a `.date` badge)
5. `#act`: Micro Activism FOR THE WIN (`.tile` grid)
6. `#records`: Know the Record, linking to the NoTo candidate sites
7. `#resources`: Know Your Ballot (`.card` grid)
8. `#votenote`: VoteNote newsletter archive (`.issue` items, newest first, with a "Latest" `.tag` on the newest)
9. `#contact`: Get Involved (elections@confluenceindivisible.org)

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
4. Pull the issue's new events, actions, and links into the Events, Micro Activism, and Know Your Ballot sections. Extract the text and links with fitz: `page.get_text()` and `page.get_links()`.
5. Remove events whose dates have passed.

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
