# Pilpel Lab assistant - public site

Static landing page and privacy policy for the Pilpel Lab assistant, the lab's general internal assistant.

## Why this repo exists

Google requires an OAuth app with user type **External** to have a public home page and a public privacy policy URL before its consent screen can be switched from **Testing** to **Production**. While the app stays in Testing, Google issues refresh tokens that expire after **7 days**, which breaks the assistant's calendar and email access every week. Publishing to Production fixes the token lifetime.

The assistant's code lives in the private repo `pilpelab/pilpelab-bot`. GitHub Pages needs a **public** repo, so the two public-facing pages live here, separate from the bot code and from any personal site.

## Live URLs

| Page | URL |
|---|---|
| Home page | https://pilpelab.github.io/lab-bot/ |
| Privacy policy | https://pilpelab.github.io/lab-bot/privacy.html |

## Google Cloud values

Project `inner-analyzer-506707-b4`, in **Google Auth Platform -> Branding**:

| Field | Value |
|---|---|
| App name | `Pilpel Lab assistant` |
| Application home page | `https://pilpelab.github.io/lab-bot/` |
| Application privacy policy link | `https://pilpelab.github.io/lab-bot/privacy.html` |
| Authorized domains | `pilpelab.github.io` |

After saving, go to **Google Auth Platform -> Audience** and click **Publish app**. The app can stay unverified; sensitive and restricted scopes still work in Production for a small lab under the 100-user cap.

Publishing does not revive an already-dead token. After publishing, mint a new one with:

```bash
# on the bot host (node2lab / fedora-lab-laptop)
cd ~/pilpelab-bot
node .agents/skills/calendar-bot/scripts/oauth-reconsent.mjs
```

The token must be minted **after** publishing, otherwise it is still a 7-day Testing token.

## Keeping the policy honest

The assistant is general, but the privacy policy must stay accurate about the Google data actually accessed. The policy therefore states the general purpose (operating lab workflows) and then lists the scopes in use today: Calendar, Gmail, and the lab's schedule Sheet.

When a new capability needs a new Google scope or a new category of Google data:

1. Update `privacy.html` first, including the "last updated" date.
2. Add the scope to the OAuth consent flow.
3. Re-consent the lab account.

Never describe access the app does not have, and never add a scope without updating this page.

## Files

| File | Purpose |
|---|---|
| `index.html` | Landing page: what the assistant does today and how it is built to grow |
| `privacy.html` | Privacy policy: purpose, current scopes, least privilege, use, storage, sharing, no model training, revocation, Google API Services User Data Policy |
| `style.css` | Shared styles and light/dark tokens |

## How to edit and deploy

1. Edit the HTML or CSS.
2. Preview locally:

   ```bash
   cd ~/dev/lab-bot
   python3 -m http.server 8899
   # open http://127.0.0.1:8899/
   ```

3. Commit and push to `main`:

   ```bash
   git add -A
   git commit -m "content: <what changed>"
   git push origin main
   ```

GitHub Pages builds from `main` at the repository root. A push usually goes live within about a minute. Verify:

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://pilpelab.github.io/lab-bot/
curl -s -o /dev/null -w "%{http_code}\n" https://pilpelab.github.io/lab-bot/privacy.html
```

Both must return `200`. Google checks these URLs, so never leave one returning 404.

## Notes

- Both pages are public by design. Do not add credentials, tokens, or private lab data here.
- The theme toggle stores the choice in `localStorage` under `lab-site-theme` and defaults to the system preference.
- This public copy is the canonical privacy policy for Google. Do not also publish a copy on a personal site; one canonical location only.
