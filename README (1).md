# Tulia

A trade journal and risk trainer that connects to Deriv. One static file, no build step, no backend.

## What it does

- **Journal**: signs in to Deriv, pulls your last 500 closed trades, and lists the habits costing you money (stakes after losses, quick re-entries, bad hours, weak markets, winning-streak slumps).
- **Trainer**: five short risk lessons with quizzes, a daily streak, a stake sizer, and a losing-streak table.
- **Practice**: live Volatility 100 prices and five-tick Rise/Fall trades on a **demo account only**, with a session loss limit that blocks further trades.
- It never places a trade on a real account.

## Run it

Any static host works (Netlify, Vercel, GitHub Pages, Cloudflare Pages), or locally:

    cd tulia
    python3 -m http.server 8080

Then open http://localhost:8080. Local testing works because Deriv allows `http://localhost` redirects.

## Connect it to Deriv

1. At https://developers.deriv.com/dashboard choose **Web-based apps** (OAuth 2.0) and register the app.
2. Add Tulia's exact address as the redirect URL. Tulia shows it in **Settings**. Include any subfolder.
3. Copy the app ID, paste it in Tulia's **Settings**, and save.
4. Press **Sign in with Deriv**.

Sign-in uses OAuth 2.0 with PKCE: Tulia sends you to auth.deriv.com, exchanges the returned code for an access token, lists your accounts, and asks Deriv for a one-time WebSocket URL for the account you pick (demo is chosen first).

Alternative without a redirect: create a **Native apps** (PAT) app, make a personal access token in the dashboard, and paste it under "Or paste an API token".

## What is not verified

I could not run this against a live Deriv account. These parts follow Deriv's published docs but are untested, and the first two are where a problem would show up:

- The account list call (`GET /trading/v1/options/accounts`). The docs I could read show only the create call, so the response parsing is deliberately forgiving.
- `profit_table`, `ticks`, `proposal` and `buy` messages on the new authenticated WebSocket. They are the classic API messages and should carry over.
- The token exchange may need a `client_secret` for some app types. The docs contradict themselves on this.

If sign-in fails, the message on the login screen says which step failed. The code to adjust is in the `auth` section near the top of the script in `index.html`.

## Notes

- Tokens are kept in sessionStorage and cleared on sign out or when the tab closes. OAuth access tokens expire (about an hour), so you sign in again.
- Tulia is independent and not endorsed by Deriv. It does not copy Deriv's branding or login page.
