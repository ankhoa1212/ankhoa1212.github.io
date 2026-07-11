---
title: "feat: Add games showcase section beside bookshelf"
date: 2026-07-10
depth: lightweight
type: feat
---

## Summary

Add a new "games" page showcasing games the site owner has built, reachable from the same `submenus` nav dropdown that currently exposes `bookshelf`. The page reuses the existing `_projects`/card-grid pattern (`_pages/projects.md` + `_includes/projects.liquid`) driven by a new `_games` collection, rather than the `book-shelf` layout, since games need "View on GitHub" / "Play" links and descriptions, not cover art or reading status. Content is a curated, user-confirmed list of 7 real repos under `github.com/ankhoa1212` (not a live API pull).

Note: this is unrelated to `docs/plans/2026-07-10-001-feat-add-arcade-section-plan.md`, an already-existing plan (not yet implemented) for a separate "arcade" page listing games the owner has *played*. That plan is out of scope here — see Scope Boundaries.

---

## Problem Frame

The site has a `bookshelf` page (`_pages/books.md`) reachable only through the `submenus` dropdown in `_pages/dropdown.md`. The user wants an equivalent "games" section beside it, showing games *they've made*, referencing their GitHub profile (`ankhoa1212`).

An initial GitHub API scan of `ankhoa1212`'s 28 public repos found only one repo that was unambiguously a shipped game by auto-detection (`BattleBox`). The user then supplied the real list directly: `BattleBox`, `comp55-final-project`, and all `comp159-*` repos. A follow-up GitHub search confirmed exactly 5 `comp159-*` repos, all Unity/Java game projects built for a "Computer Game Technologies" course — for 7 games total. All 7 are technically GitHub forks of a course-template author (`a-nguyen1`) that the user personalized for coursework/capstone use, which is why they didn't surface in a naive "owned, non-fork repos" scan.

## Requirements

- R1. A new page at `/games/` shows all 7 confirmed games (`BattleBox`, `comp55-final-project`, `comp159-2d-randomly-generated-maze-escape`, `comp159-2d-randomly-generated-shooter`, `comp159-2d-tower-defense`, `comp159-2d-with-3d-endless-runner`, `comp159-3d-randomly-generated-platformer`) as cards with title and description.
- R2. Each game has its own detail page with a "View on GitHub" link to the real `ankhoa1212` repo, plus a "Play" / live-demo link when one exists (`BattleBox`, the maze escape, shooter, tower defense, and platformer games each have a live build; the endless runner and `comp55-final-project` do not).
- R3. `/games/` is reachable from the same `submenus` dropdown that lists `bookshelf`, appearing beside it.
- R4. The new collection, page, and entries follow existing repo conventions (mirrors `_projects`/`_pages/projects.md` shapes; dropdown-only pages use `nav: false` like `_pages/books.md`).

## Key Technical Decisions

- **KTD1: Model games on `_projects` + `projects.liquid`, not the `book-shelf` layout.** `_layouts/book-shelf.liquid` is purpose-built for the `_books` collection's cover/status/olid/isbn fields, which games don't have. `_includes/projects.liquid` already renders a generic card (title, description, optional `img`) with zero changes needed — reuse it as-is by passing `project=game`.
- **KTD2: No new `_includes/games.liquid`.** Confirmed by scanning every `_projects/*.md` entry: none set the include's `github`/`img` shortcut fields — all of them place a manual "View on GitHub" link in the page body instead (e.g. `_projects/1_project.md:13`, `_projects/2_project.md:13`). Following that same established convention for games (rather than the unused include shortcut) avoids introducing a second, inconsistent way of doing the same thing.
- **KTD3: Curated static `_games` collection, not a live GitHub API pull.** The user named specific repos rather than asking for auto-discovery. Static per-game files also allow a real, written description instead of an auto-generated GitHub stat card (which the site already has, dormant, via `_pages/repositories.md` + `_data/repositories.yml` — that page stays untouched).
- **KTD4: No screenshots at launch.** No local image assets exist for any of the 7 games. `projects.liquid` already guards thumbnail rendering behind an `if project.img` conditional, so cards render cleanly without one — no template change needed. Deferred to follow-up (see Scope Boundaries).
- **KTD5: Extend the existing `submenus` dropdown, not a new top-level nav item** — per explicit user choice. `_includes/header.liquid` iterates `page.children` generically, so no template change is needed to add a third entry (currently: bookshelf, divider, blog).
- **KTD6: No category tabs on `/games/`.** `_pages/projects.md`'s categorized branch (gated by `site.enable_project_categories`, currently `true` site-wide) exists for filtering many projects into groups; 7 games in one grid doesn't need that — use the simpler non-categorized branch shape instead.

---

## Implementation Units

### U1. Register the `games` collection

**Goal:** Make `_games/*.md` files build as pages and expose them via `site.games`.

**Requirements:** R4

**Dependencies:** None

**Files:**
- `_config.yml` (modify — `collections:` block, alongside `books`/`projects`/`teachings`)

**Approach:** Add a `games:` entry with `output: true`, matching the shape of the existing `projects:` and `teachings:` entries.

**Patterns to follow:** `_config.yml` `collections:` block (existing `projects: output: true`).

**Test expectation:** none -- config-only change with no independent behavior; verified indirectly by U4's build check.

**Verification:** Jekyll build does not error on the new collection declaration.

---

### U2. Add the 7 game entries

**Goal:** One markdown file per confirmed game, mirroring the `_projects` entry shape.

**Requirements:** R1, R2, R4

**Dependencies:** U1

**Files:**
- `_games/battlebox.md` (new)
- `_games/comp55-final-project.md` (new)
- `_games/comp159-maze-escape.md` (new)
- `_games/comp159-shooter.md` (new)
- `_games/comp159-tower-defense.md` (new)
- `_games/comp159-endless-runner.md` (new)
- `_games/comp159-platformer.md` (new)

**Approach:** Each file uses front matter `layout: page`, `title`, `description`, `importance` (sets card order via `sort: "importance"` in U3) — mirroring `_projects/1_project.md`'s front matter shape but omitting `img`/`category`/`related_publications` (no screenshots per KTD4; no category tabs per KTD6). Body content is a short paragraph plus a "View on GitHub" link, and a "Play" link where a live build exists (per KTD2, matching `_projects/1_project.md:13`'s manual-link convention):

| File | Title | importance | GitHub repo | Demo/Play link |
| --- | --- | --- | --- | --- |
| `battlebox.md` | BattleBox | 1 | `ankhoa1212/BattleBox` | `https://clintpenafiel.github.io/BattleBox/` |
| `comp159-maze-escape.md` | Lookabout (Maze Escape) | 2 | `ankhoa1212/comp159-2d-randomly-generated-maze-escape` | `https://vprez.itch.io/lookabout` |
| `comp159-shooter.md` | Randomly Generated Shooter | 3 | `ankhoa1212/comp159-2d-randomly-generated-shooter` | `https://ankhoa1212.github.io/comp159-2d-randomly-generated-shooter/` |
| `comp159-tower-defense.md` | 2D Tower Defense | 4 | `ankhoa1212/comp159-2d-tower-defense` | `https://ankhoa1212.github.io/comp159-2d-tower-defense/` |
| `comp159-endless-runner.md` | 2D/3D Endless Runner | 5 | `ankhoa1212/comp159-2d-with-3d-endless-runner` | none |
| `comp159-platformer.md` | 3D Randomly Generated Platformer | 6 | `ankhoa1212/comp159-3d-randomly-generated-platformer` | `https://ankhoa1212.github.io/comp159-3d-randomly-generated-platformer/` |
| `comp55-final-project.md` | COMP 55 Final Project | 7 | `ankhoa1212/comp55-final-project` | none |

Description content, drawn from each repo's real GitHub description:
- **BattleBox:** a real-time strategy game built in Unity, using the Unity ML-Agents Toolkit to train autonomous agents; built as a CS Senior Project.
- **Lookabout (Maze Escape):** a 2D Unity horror maze-escape game with randomly generated levels, built for COMP 159 Computer Game Technologies and submitted to SCREAM JAM 2023.
- **Randomly Generated Shooter:** a 2D Unity run-and-gun shooter with randomly generated levels, built as the final project for COMP 159 Computer Game Technologies.
- **2D Tower Defense:** a 2D Unity tower defense strategy game built for COMP 159 Computer Game Technologies.
- **2D/3D Endless Runner:** a hybrid 2D/3D Unity endless runner built for COMP 159 Computer Game Technologies.
- **3D Randomly Generated Platformer:** a 3D Unity platformer with randomly generated levels, built as an individual project for COMP 159 Computer Game Technologies.
- **COMP 55 Final Project:** a team project for COMP 55 Application Development (Spring 2022) — random level generation, UI, and enemy logic, built in Java with the ACM graphics library.

**Patterns to follow:** `_projects/1_project.md`, `_projects/2_project.md` (front matter shape, manual "View on GitHub" body link).

**Test expectation:** none -- static content, no behavior to test.

**Verification:** Each of the 7 pages builds and renders its title, description, GitHub link, and (where applicable) Play link without Jekyll errors.

---

### U3. Create the `/games/` listing page

**Goal:** A grid page at `/games/` listing all 7 `_games` entries as cards.

**Requirements:** R1, R4

**Dependencies:** U2

**Files:**
- `_pages/games.md` (new)

**Approach:** Front matter: `layout: page`, `title: games`, `permalink: /games/`, a short `description`, `nav: false` (dropdown-only, matching `_pages/books.md`'s `nav: false`). Body mirrors `_pages/projects.md`'s non-categorized branch (`_pages/projects.md:40-64`) but without the category-toggle wrapper (per KTD6): sort `site.games` by `importance` and render each through the `projects.liquid` include, passing `project=game`, inside a `row row-cols-1 row-cols-md-3` grid.

**Patterns to follow:** `_pages/projects.md:40-64` (non-categorized card grid), `_pages/books.md:1-6` (dropdown-only front matter shape).

**Test expectation:** none -- static page composition, no behavior to test.

**Verification:** `docker compose up --build`, visit `http://localhost:8080/games/`, confirm 7 cards render in importance order, each linking to its own detail page.

---

### U4. Wire `/games/` into the submenus dropdown beside bookshelf

**Goal:** Make `/games/` reachable from the same dropdown as `/books/`.

**Requirements:** R3

**Dependencies:** U3 (target permalink must exist before linking to it)

**Files:**
- `_pages/dropdown.md` (modify)

**Approach:** Add a `games` entry to the `children` list immediately beside the existing `bookshelf` entry, before the `divider`:

```yaml
children:
  - title: bookshelf
    permalink: /books/
  - title: games
    permalink: /games/
  - title: divider
  - title: blog
    permalink: /blog/
```

No changes to `_includes/header.liquid` are needed (KTD5) — it already iterates `page.children` generically.

**Patterns to follow:** `_pages/dropdown.md:6-12` (existing children list shape).

**Test expectation:** none -- front-matter-only data change, no behavior to test.

**Verification:** After rebuilding, open the "submenus" dropdown and confirm "games" appears between "bookshelf" and the divider, and clicking it navigates to `/games/`.

---

## Scope Boundaries

**In scope:** One new collection (`_games`), 7 curated game entries, one listing page, one dropdown nav entry.

**Out of scope / Deferred to Follow-Up Work:**
- Screenshots or thumbnails per game (KTD4) — no local image assets exist yet; can be added later via each entry's `img:` front matter with zero template changes.
- Category filtering/tabs on `/games/` (KTD6) — not warranted for 7 items.
- Live GitHub stats (stars/forks) auto-embedded per game — the dormant `_pages/repositories.md` / `_data/repositories.yml` mechanism already covers that use case separately and is left untouched.
- Additional games not in the user-confirmed list (e.g. future projects) — can be added later by dropping in a new `_games/*.md` file; no template changes required.
- **Not part of this plan at all:** `docs/plans/2026-07-10-001-feat-add-arcade-section-plan.md` — a separate, already-written (not yet implemented) plan for an "arcade" page listing games the owner has *played*. That work is independent of this one; both can be implemented in either order since they touch `_pages/dropdown.md`'s same `children` list but add distinct entries.

## Sources & Research

- Local patterns read directly: `_pages/books.md`, `_pages/dropdown.md`, `_pages/projects.md`, `_pages/repositories.md`, `_layouts/book-shelf.liquid`, `_includes/projects.liquid`, `_includes/repository/repo.liquid`, `_includes/figure.liquid`, `_projects/1_project.md`, `_projects/2_project.md`, `_data/repositories.yml`, `_config.yml` (collections, `enable_project_categories`).
- External research (GitHub REST/Search API, `github.com/ankhoa1212`): confirmed 28 public repos total, 18 non-fork via Search API; identified `BattleBox` as the one auto-detectable game; confirmed the user-supplied `comp55-final-project` and all 5 `comp159-*` repos via `search/repositories?q=user:ankhoa1212+comp159+in:name`; fetched each of the 7 repos' description/homepage/topics/fork-status individually to source real card copy and demo links.
