---
title: "feat: Add arcade section listing games played"
date: 2026-07-10
depth: lightweight
type: feat
---

## Summary

Add a new "arcade" page listing games the site owner has played, as a simple list, reachable from the same "submenus" nav dropdown that currently exposes the bookshelf page. No new layout, collection, or styling is required — the page reuses the existing `page` layout and dropdown nav mechanism already used by `bookshelf` and `blog`.

---

## Problem Frame

The site has a `bookshelf` page (`_pages/books.md`) reachable only through the `submenus` dropdown in `_pages/dropdown.md`. The user wants an equivalent, simpler "arcade" section — a plain list of games played — placed beside bookshelf in that same dropdown. The user supplied a flat list of 14 game titles (with a couple of stray Markdown artifacts — a leading `#` on "Pokemon" and bold/italic emphasis on "Marvel's Spider-Man" — that should be cleaned up rather than preserved literally).

## Requirements

- R1. A new page exists listing all 14 games the user provided, rendered as a clean list (no fabricated metadata such as platform, status, or rating).
- R2. The page is reachable from the site nav via the same `submenus` dropdown that lists `bookshelf`, appearing beside it.
- R3. Stray Markdown artifacts in the source list (`# Pokemon`, `***Marvel's Spider-Man***`) are normalized to plain titles, not rendered as a heading or bold/italic text.
- R4. The new page follows existing repo conventions for page front matter (`_pages/*.md`, `layout: page`, `permalink`, `nav: false` since it's dropdown-only) — mirroring how `_pages/books.md` is set up.

## Key Technical Decisions

- **KTD1: Reuse `layout: page`, not `book-shelf`.** The `book-shelf` layout (`_layouts/book-shelf.liquid`) renders a cover-image grid driven by a `_books` collection with per-item front matter (`cover`/`olid`/`isbn`, `status`, `started`). Games have none of that data, and the user chose "simple list" over a bookshelf-style table. Building a parallel `_games` collection and per-game files would be pure scope creep for a flat list. Rationale confirmed with user (Content format question, Phase 0).
- **KTD2: Extend the existing `submenus` dropdown rather than adding a new top-level nav item.** Confirmed with user (Nav placement question, Phase 0). `_pages/dropdown.md` already drives the dropdown via its `children` front matter list consumed generically by `_includes/header.liquid:60-96` — no template change needed, only a new child entry.
- **KTD3: No new CSS/layout code.** `_includes/header.liquid` iterates `page.children` generically (lines 65, 86) and needs no changes to support a third dropdown entry (currently: bookshelf, divider, blog).

---

## Implementation Units

### U1. Create the arcade page

**Goal:** Add `_pages/arcade.md` listing all 14 games as a plain list.

**Requirements:** R1, R3, R4

**Dependencies:** None

**Files:**
- `_pages/arcade.md` (new)

**Approach:** Mirror `_pages/books.md`'s front matter shape but with `layout: page` (per KTD1) instead of `book-shelf`, since there is no games collection to render below the list. Front matter: `layout: page`, `title: arcade`, `permalink: /arcade/`, `nav: false` (matches `books.md`'s `nav: false` — the page is dropdown-only per KTD2). Body: a short intro line (optional, mirroring the bookshelf page's tone) followed by a Markdown bullet list of the 14 games, normalized:
  - "Pokemon" (drop the stray leading `#`; do not render as a heading)
  - Hades
  - Marvel's Spider-Man (drop the `***bold-italic***` emphasis; plain text)
  - The Elder Scrolls V: Skyrim
  - Hogwarts Legacy
  - Risk of Rain 2
  - Risk of Rain Returns
  - Hollow Knight
  - Undertale
  - Super Auto Pets
  - Fortnite
  - Elden Ring
  - Baldur's Gate 3
  - Halo Infinite

**Patterns to follow:** `_pages/books.md:1-13` for front matter and intro-line tone.

**Test expectation:** none — static content page, no behavior to test. Verification is visual (Phase 4.2/5.4 build check).

**Verification:** `docker compose up --build`, visit `http://localhost:8080/arcade/`, confirm the list renders as plain bullets (no literal `#` heading, no bold/italic artifacts) and the page loads without Jekyll build errors.

---

### U2. Wire arcade into the submenus dropdown beside bookshelf

**Goal:** Make `/arcade/` reachable from the same dropdown that lists `/books/`.

**Requirements:** R2

**Dependencies:** U1 (the target permalink must exist before linking to it)

**Files:**
- `_pages/dropdown.md` (modify)

**Approach:** Add a new entry to the `children` list in `_pages/dropdown.md`, placed immediately beside the existing `bookshelf` entry (per KTD2), before the `divider` that separates it from `blog`:

```yaml
children:
  - title: bookshelf
    permalink: /books/
  - title: arcade
    permalink: /arcade/
  - title: divider
  - title: blog
    permalink: /blog/
```

No changes to `_includes/header.liquid` are needed (KTD3) — it already iterates `page.children` generically.

**Patterns to follow:** `_pages/dropdown.md:6-12` (existing children list shape).

**Test expectation:** none — front-matter-only data change, no behavior to test.

**Verification:** After rebuilding, open the "submenus" dropdown in the nav and confirm "arcade" appears between "bookshelf" and the divider, and clicking it navigates to `/arcade/`.

---

## Scope Boundaries

**In scope:** A single new static page and one dropdown nav entry.

**Out of scope / Deferred to Follow-Up Work:**
- Per-game detail pages, cover art, ratings, or play-status tracking (would require a `_games` collection and a dedicated layout, mirroring the bookshelf/book-review pattern — not requested and not warranted for a flat list).
- Any table-style metadata (platform, hours played, rating) — explicitly declined by the user in favor of a simple list.

## Sources & Research

- `_pages/books.md`, `_pages/dropdown.md`, `_layouts/book-shelf.liquid`, `_layouts/book-review.liquid`, `_includes/header.liquid:60-96`, `_books/*.md` — read directly to confirm the bookshelf pattern and nav mechanism. No external research was needed (Phase 1.2 skip: strong, sufficient local pattern for a content-only change).
