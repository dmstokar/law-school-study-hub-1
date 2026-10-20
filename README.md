# Law School Study Hub on GitHub Pages + Cloudflare Worker

GitHub Pages hosts the static app. A separate Cloudflare Worker securely calls OpenRouter; the API key is never put in this folder or the GitHub repository. The Worker is in `../law-school-hub-worker` in the provided package.

## 1. Publish the site with GitHub Pages

1. Create a GitHub repository and upload the contents of this folder (`index.html` and this README) to its publishing root.
2. In the repository, open **Settings → Pages**. Under **Build and deployment**, choose **Deploy from a branch**, select the default branch and `/ (root)`, then save. GitHub Pages may take a few minutes to publish.
3. Copy the site's origin (the beginning of its URL, through `.io`; do not include a path or trailing slash). For a project site, the origin is still `https://USERNAME.github.io`, while the full app URL includes `/REPOSITORY/`.

## 2. Deploy the AI backend

Install Node.js, then open a terminal in `law-school-hub-worker` and run:

```sh
npx wrangler login
npx wrangler secret put OPENROUTER_API_KEY
npx wrangler deploy
```

When prompted for the secret, paste your OpenRouter key in the terminal. Do not add the key to GitHub, this HTML file, or a message. `wrangler.toml` has an `ALLOWED_ORIGIN` placeholder; replace it with the GitHub Pages origin before deploying. To change it later, update the value in `wrangler.toml` and deploy again.

Cloudflare prints a Worker URL ending in `.workers.dev`. Copy the URL and append `/api/chat`.

## 3. Connect the app to the Worker

In `index.html`, replace the example URL in `AI_API_URL` with the Worker URL ending in `/api/chat`, commit the change, and wait for GitHub Pages to publish it. Then try the Exam Practice Coach.

## Notes

- The Worker only accepts the `openrouter/free` model and limits request size and response tokens.
- CORS allows the exact `ALLOWED_ORIGIN` configured for your GitHub Pages site. For a repository site, use the origin such as `https://USERNAME.github.io`, not the full `/REPOSITORY/` URL.
- The site is public. Anyone who can use it can submit AI requests against the configured OpenRouter account. CORS controls which websites can read responses; it is not user authentication. Monitor usage and rotate the key if it is exposed.
