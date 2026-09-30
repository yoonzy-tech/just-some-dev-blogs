# Ruby's Openbook

My personal dev blog and notes, built with [Quartz 5](https://quartz.jzhao.xyz/) and published on GitHub Pages.

🌐 **https://yoonzy-tech.github.io/openbook/**

## How this repo is set up

The site code and the writing live in two separate repos, so work in progress never becomes public.

| Repo | Contains | Visibility |
|---|---|---|
| **yoonzy-tech/openbook** (this one) | Quartz, site settings (`quartz.config.yaml`), deploy workflow | Public |
| **yoonzy-tech/quartz-content** | Everything in `content/`: posts, notes, images, templates, Obsidian settings | Private |

`content/` is gitignored here. Locally, it's a separate clone of the private repo, and it doubles as an Obsidian vault.

### How publishing works

```
Obsidian (content/) ──push──▶ quartz-content ──"content-updated" dispatch──▶ openbook
                                                                                   │
                                     checks out quartz-content into content/, builds, deploys to Pages
```

- Any push to `main` in `quartz-content` triggers [`deploy.yml`](.github/workflows/deploy.yml) in this repo. A push to `v5` here triggers it too.
- Notes with `draft: true` in their frontmatter are filtered out of the build, so they stay private. Set `draft: false` to publish.
- Non-Markdown files (images, PDFs) are always published, even when the note using them is a draft.

## Setting up on a new machine

Requirements: Node 22 or later, and Git.

```bash
git clone https://github.com/yoonzy-tech/openbook.git
cd openbook
git clone https://github.com/yoonzy-tech/quartz-content.git content
npm ci
npx quartz build --serve   # preview at http://localhost:8080
```

Then, in Obsidian, choose **Open folder as vault** and pick `content/`. Plugins and settings come with the repo. Obsidian Git handles commits and pushes.

## One-time GitHub setup

This is already done, but here it is in case it ever needs redoing, for example after a token expires.

1. **Pages:** in this repo, go to Settings → Pages and set Source to **GitHub Actions**. Under Settings → Environments → `github-pages`, allow the `v5` branch.
2. **Access token:** create a [fine-grained personal access token](https://github.com/settings/personal-access-tokens) with:
   - Repository access: `openbook` and `quartz-content`
   - Permissions: **Contents: Read and write**
3. **Secrets:** add the token as an Actions secret named **`CONTENT_TOKEN`** in **both** repos (Settings → Secrets and variables → Actions → Repository secrets).
   - This repo uses it to check out the private content during the build.
   - `quartz-content` uses it to trigger a rebuild here ([`publish.yml`](https://github.com/yoonzy-tech/quartz-content/blob/main/.github/workflows/publish.yml), private).

If a deploy fails at "Check out content from the private repo", the token has most likely expired or lost access to one of the repos.

## Maintenance

| Task | How |
|---|---|
| Write or publish a post | In the vault, see `Blog Guide.md` (private) |
| Change site settings | Edit `quartz.config.yaml`, then commit and push to `v5` |
| Update Quartz | `npx quartz upgrade`, check with `npx quartz build --serve`, then push to `v5` |
| Rebuild the site by hand | Actions → "Deploy Quartz site to GitHub Pages" → **Run workflow** |

## Credits

Built with [Quartz](https://github.com/jackyzha0/quartz) by [@jackyzha0](https://github.com/jackyzha0), MIT licensed (see [LICENSE.txt](LICENSE.txt)).
