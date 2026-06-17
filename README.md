# My Blog

A static blog built with [Hugo](https://gohugo.io) and the [Hextra](https://github.com/imfing/hextra) theme, deployed for free to GitHub Pages via GitHub Actions.

- Live site: `https://YOUR-USERNAME.github.io/`
- Theme: Hextra (imported as a Hugo Module, pinned in `go.mod`)
- Hosting: GitHub Pages (free tier, public repository)
- Deploy: GitHub Actions workflow at `.github/workflows/pages.yaml`

Replace `YOUR-USERNAME` and any template author references throughout this repo with your own.

## How deployment works

There is no manual publish step. The flow is:

1. You commit and push to the `main` branch (or edit a file directly in the GitHub web UI, which is also a commit).
2. The Actions workflow installs Hugo and Go, builds the static site into `public/`, and uploads it to GitHub Pages.
3. The site is live in roughly one to two minutes.

The workflow builds with `--baseURL` taken from the Pages configuration, so the correct URL is injected automatically at build time. You do not need to hand edit the deploy URL.

To watch a deploy: open the **Actions** tab and click the running job. A green check means it is live. A red X means the build failed; open the job logs to see the error.

To redeploy without changing content: **Actions** tab, select "Deploy Hugo site to Pages", then **Run workflow**.

## One-time setup (already done, kept here for reference)

1. Repository created from the `imfing/hextra-starter-template` template.
2. **Settings > Pages > Build and deployment > Source** set to **GitHub Actions**. This must be set or nothing deploys.
3. Repository visibility kept **Public**. On a free GitHub account, Pages only serves from public repos. The published site is public regardless of repo visibility, and because the site is static, anyone can read the rendered source anyway. Do not commit secrets.

## Repository structure

```
.
├── .github/workflows/pages.yaml   # build + deploy pipeline (do not delete)
├── content/                       # all your pages and posts (Markdown)
│   ├── _index.md                  # home / landing page
│   ├── about.md                   # about page
│   └── blog/                      # blog section (create if missing)
│       ├── _index.md              # blog index page
│       └── my-first-post.md       # individual posts
├── hugo.yaml                      # site config: title, menu, navbar, footer, params
├── go.mod / go.sum                # pins the Hextra theme version
└── README.md                      # this file
```

Optional folders you can add when needed:

```
assets/css/custom.css   # your own CSS overrides
static/                 # files copied as-is (favicon, robots.txt, downloadable files)
layouts/                # template overrides (advanced, only to override theme HTML)
```

## Managing posts

Posts live in `content/blog/`. Each post is one Markdown file. The filename becomes the URL slug, so `content/blog/why-i-blog.md` serves at `/blog/why-i-blog/`.

### Create a post

Add a new file, for example `content/blog/hello-world.md`:

```markdown
---
title: "Hello World"
date: 2026-06-17
authors:
  - name: Your Name
    link: https://github.com/YOUR-USERNAME
    image: https://github.com/YOUR-USERNAME.png
tags:
  - General
---

Short intro paragraph that shows up as the summary.

<!--more-->

The rest of the post goes here. Standard Markdown works: headings,
**bold**, lists, links, images, and fenced code blocks.
```

Front matter notes:

- `title` and `date` are the essentials. The list is sorted by `date`, newest first.
- `authors` and `tags` are optional but match the akitaonrails style.
- `<!--more-->` marks where the summary ends. Text above it is the excerpt on the blog index.
- `draft: true` keeps a post out of the production build until you remove it.
- `excludeSearch: true` hides a page from the built-in search index.

Commit and push. The post is live after the next deploy.

### Edit a post

Open the file, change the content, commit, push. Fastest path for a quick fix: click the pencil icon on the file in the GitHub web UI, edit, and commit directly. To bump the displayed date when republishing, update `date` in the front matter.

### Delete a post

Delete the Markdown file and push. The page disappears on the next deploy. In the web UI: open the file, use the file menu, **Delete file**, commit.

### Pages that are not blog posts

Top-level pages (About, etc.) live directly in `content/`, for example `content/about.md`. They use the same Markdown format. Add them to the navbar via the `menu` block in `hugo.yaml`.

## Changing the design and configuration

Almost all visual and structural settings live in `hugo.yaml`.

### Site title and base URL

```yaml
title: My Blog
baseURL: "https://YOUR-USERNAME.github.io/"
```

### Navbar menu

Each entry is an item under `menu.main`. Lower `weight` shows first.

```yaml
menu:
  main:
    - name: Blog
      pageRef: /blog
      weight: 1
    - name: About
      pageRef: /about
      weight: 2
    - name: Search
      weight: 3
      params:
        type: search
    - name: GitHub
      weight: 4
      url: "https://github.com/YOUR-USERNAME"
      params:
        icon: github
```

Use `pageRef` for internal pages and `url` for external links. The `icon` param accepts Hextra's built-in icon names (for example `github`, `x-twitter`, `rss`).

### Navbar logo and title display

```yaml
params:
  navbar:
    displayTitle: true
    displayLogo: false
```

To show a logo, set `displayLogo: true` and add the logo config plus an image under `static/` or `assets/`. See the Hextra docs section on the navbar.

### Footer

```yaml
params:
  footer:
    displayCopyright: false
    displayPoweredBy: true
```

### Colors, fonts, dark mode

Hextra ships light and dark mode and a primary color hue. Theme color and appearance are configured under `params` (`theme`, `color`). See the Hextra "Theme Configuration" docs for the exact keys, since they track the theme version pinned in `go.mod`.

### Custom CSS

Create `assets/css/custom.css` and put your overrides there. Hextra loads it automatically. This is the safe place for tweaks that survive theme updates, since it does not touch theme files.

### Favicon and static files

Drop a `favicon.ico` (and any other static assets) into `static/`. Everything in `static/` is copied to the site root unchanged.

### Deeper layout changes

To change the actual HTML of a component, copy the relevant template from the theme into a matching path under `layouts/` and edit your copy. Hugo prefers your `layouts/` file over the theme's. This is advanced and is the main thing that can break on a theme update, so keep overrides minimal.

## Local preview

Editing on GitHub directly works, but for design changes a local preview is much faster because you see results instantly.

Prerequisites:

- **Hugo (extended edition)**, version close to the one in the workflow (`HUGO_VERSION` in `pages.yaml`).
- **Go** (the theme is a Hugo Module, so Go is required to fetch it). Version per `go.mod`.
- **Git**.

Install (macOS with Homebrew):

```bash
brew install hugo go git
```

Install (Debian/Ubuntu): install Go from go.dev, and grab the extended Hugo `.deb` from the Hugo releases page (the `hugo_extended_*` asset). The plain `apt` Hugo is often not the extended build, which Hextra needs.

Run the site locally:

```bash
git clone https://github.com/YOUR-USERNAME/YOUR-USERNAME.github.io.git
cd YOUR-USERNAME.github.io
hugo mod tidy
hugo server --disableFastRender
```

Open `http://localhost:1313/`. The preview live-reloads as you save files. Drafts are shown locally if you run `hugo server -D`.

## Updating the theme

The Hextra version is pinned in `go.mod`. To pull the latest:

```bash
hugo mod get -u
hugo mod tidy
```

Commit the changed `go.mod` and `go.sum`, push, and verify the deploy. Review the Hextra release notes for breaking changes before updating, especially if you have files under `layouts/`.

## Public access and privacy

- The site is publicly reachable at the Pages URL with no login.
- On the free plan the repo must be public for Pages to serve.
- The blog is static. There is no server, database, login, or comment backend by default. Comments, analytics, or contact forms require a third-party service added separately.
- Never commit anything sensitive. Treat the repo as fully public.

## Troubleshooting

- **Build fails on `hugo mod`**: Go is missing or too old locally, or the module cache is stale. Run `hugo mod tidy`. In CI this is handled by the workflow.
- **Site deployed but CSS or links are broken**: usually a `baseURL` mismatch. The CI build sets it automatically; if you only see this locally, that is expected and not a problem for the live site.
- **Pushed but nothing changed online**: check the **Actions** tab for a failed or skipped run, and confirm **Settings > Pages > Source** is still **GitHub Actions**.
- **404 at the root**: confirm `content/_index.md` exists and the deploy is green.
