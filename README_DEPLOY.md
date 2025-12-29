This README contains exact steps to preview locally, push your changes to GitHub, and publish the site to the custom domain `complie.io`.

Overview
- I prepared a `CNAME` file (with `complie.io`) and a GitHub Actions workflow `.github/workflows/deploy.yml` that will publish your repository root to the `gh-pages` branch on every push to `main`.

What I cannot do for you
- I cannot push commits to your remote GitHub repository or change DNS records at your domain registrar. You (or someone with repository & DNS access) must run the `git` commands and update DNS.

Quick checklist (what you'll run locally)
1) Preview locally
- From the project folder:

```bash
cd /Users/admin/liana-profile-site
python3 -m http.server 8000
# open http://localhost:8000 in your browser
```

If the repo uses a build system (vite), run the project's dev script instead (see package.json).

2) Commit & push to GitHub
- If this repo is already a Git repo with a remote named `origin`:

```bash
cd /Users/admin/liana-profile-site
git status
git add -A
git commit -m "Website: content & mobile scrolling fixes, deploy setup"
git push origin main
```

- If you don't have a remote yet, create a new repo on GitHub (manually via GitHub UI) and then run the commands they show, e.g.: (replace the URL with your repo)

```bash
git remote add origin git@github.com:Complieio/liana-profile-site.git
git branch -M main
git push -u origin main
```

Alternative using GitHub CLI (if you have `gh` installed):

```bash
cd /Users/admin/liana-profile-site
gh repo create Complieio/liana-profile-site --public --source=. --remote=origin --push
```

3) What happens after push
- The GitHub Actions workflow `.github/workflows/deploy.yml` will run on push to `main` and publish the repository root to the `gh-pages` branch using the GitHub token.
- The action result is visible in the repository Actions tab. The action will create/overwrite the `gh-pages` branch with the built site (in this case, the raw root files).

4) Configure GitHub Pages or check the published site
- GitHub Pages can be configured at: `Settings > Pages`.
- If you want GitHub to serve the site from the `gh-pages` branch, set the Pages source to `gh-pages` branch and `/ (root)` folder.
- Alternatively, the action already publishes to `gh-pages` branch — after the action finishes, Pages will often auto-detect the site. If it doesn't, set it manually in Settings.

5) Connect the custom domain (`complie.io`) — DNS changes (must be done at your domain provider)
- In your domain provider's DNS UI, add these records (for GitHub Pages):

Add four A records pointing to GitHub Pages IPs:
- 185.199.108.153
- 185.199.109.153
- 185.199.110.153
- 185.199.111.153

Add a CNAME record for `www` (optional):
- Name: www
- Value: <your-github-username>.github.io

You already have a `CNAME` file in the repo containing `complie.io` which tells GitHub Pages to use that domain after you add the DNS records.

If you'd rather use Netlify or Vercel (they provide simpler CNAME targets), connect the repo via their UI instead — no CNAME file is required for Netlify/Cloudflare Pages.

6) After DNS changes
- It can take a few minutes to a few hours for DNS to propagate and for GitHub to provision HTTPS. Check the site at https://complie.io.

7) Troubleshooting
- If the GitHub Actions fail: open the Actions tab, click the failed run, and read the logs. Usually a missing token or permissions issue is obvious.
- If the site is not served: confirm Pages source is `gh-pages` and your `CNAME` file contains `complie.io`.
- If you want me to help prepare a Netlify `netlify.toml` or Vercel config instead, say so and I will add it.

Security & notes
- The workflow uses the automatically provided `GITHUB_TOKEN` which is sufficient to push to `gh-pages` when the action runs.
- You will need access to the domain registrar to change DNS.

If you want me to continue: give me permission to create any additional config files (Netlify, Vercel) or tell me whether you'd like GitHub Pages or Netlify. Once you push, I can guide you through verifying the deploy and DNS steps interactively.