# github dev stats dashboard

> a minimal, real-time dashboard that transforms public GitHub data into a clean portfolio piece — no auth required.

![preview](https://img.shields.io/badge/api-github%20public-black?style=flat-square) ![preview](https://img.shields.io/badge/auth-none-black?style=flat-square) ![preview](https://img.shields.io/badge/style-minimal%20clean-black?style=flat-square)

---

## about

a single-page dashboard built as an interactive artifact that fetches live data from the [GitHub Public API](https://docs.github.com/en/rest) and surfaces it as a clean, readable dev profile. designed to live inside a portfolio with zero backend setup.

type any github username → hit enter → get real metrics, instantly.

---

## features

| section | what it shows |
|---|---|
| **profile** | avatar, name, bio, location, company, website |
| **metrics** | repos, total stars, followers, member since, gists, forks |
| **top languages** | proportional bar + percentage breakdown from repo analysis |
| **top repositories** | sorted by stars — name, description, language, forks |
| **activity grid** | contribution-style 52-week visual |

---

## how it works

```
user types username
       ↓
GitHub API /users/{username}          → profile, follower counts, metadata
GitHub API /users/{username}/repos    → top 100 repos sorted by stars
       ↓
client-side aggregation
  · total stars    = sum of stargazers_count across all repos
  · top languages  = repo count per language, ranked and normalized to %
  · top repos      = sorted by stars, top 6 displayed
       ↓
rendered in < 1s with no server, no auth token, no build step
```

---

## data sources

all data is fetched from the **GitHub REST API v3** — public endpoints, no authentication needed.

```
https://api.github.com/users/{username}
https://api.github.com/users/{username}/repos?sort=stars&per_page=100
```

**rate limit:** unauthenticated requests are limited to 60/hour per IP. for portfolio use this is more than sufficient.

> note: the activity grid is a simulated visualization. the GitHub API does not expose per-week contribution history without authentication.

---

## tech stack

- **vanilla html/css/js** — no framework, no dependencies, no build step
- **github public api** — zero auth required
- **css variables** — full light/dark mode support automatic
- **tabler icons** — outline icon set loaded via cdn
- **font:** system monospace stack for that dev-native feel

---

## design decisions

**why monospace?** dev dashboards live in a world of terminals, editors, and code. a mono font makes numbers and labels feel native rather than dressed-up.

**why no auth token?** simplicity for portfolio embedding. the public API covers everything displayed — profile, repos, stars, languages, forks.

**why simulate the activity grid?** the contribution graph requires authenticated GraphQL access. a simulated grid preserves the visual intent without requiring a token or backend proxy.

**why top 6 repos?** enough signal to show range, not so many it becomes noise.

---

## usage

### embed in a portfolio (html)

```html
<!-- drop inside an iframe or render directly in your page -->
<iframe
  src="your-deployed-dashboard-url"
  width="100%"
  height="700"
  frameborder="0"
></iframe>
```

### run locally

no build step needed — open the html file directly in a browser:

```bash
# clone or download the file
open index.html
# or
python3 -m http.server 3000
```

### default username

the dashboard defaults to `torvalds` on load. change the default in the script:

```js
// find this line near the bottom of the script
fetchGitHub();  // calls with the value in the input field

// update the input default value in html:
<input ... value="your-username" />
```

---

## customization

### change default user

```html
<input type="text" id="username-input" value="your-github-username" />
```

### adjust number of repos shown

```js
const topRepos = repos
  .sort((a, b) => b.stargazers_count - a.stargazers_count)
  .slice(0, 6);  // ← change 6 to any number
```

### add/remove metric cards

edit the `metrics` array in the script:

```js
const metrics = [
  { label: 'repositories', value: fmtNum(user.public_repos), sub: `${nonForkRepos.length} original` },
  { label: 'total stars',  value: fmtNum(totalStars),        sub: 'across all repos' },
  // add or remove objects here
];
```

### change language color mapping

```js
const LANG_COLORS = {
  JavaScript: '#f1e05a',
  TypeScript: '#3178c6',
  // add any language not listed
  Zig: '#ec915c',
};
```

---

## api reference

### `GET /users/{username}`

returns user profile. fields used:

```
avatar_url · name · login · bio · location · company · blog
public_repos · public_gists · followers · following · created_at
```

### `GET /users/{username}/repos`

returns up to 100 repos. fields used:

```
name · description · language · stargazers_count · forks_count
fork · topics
```

---

## limitations

- **no commit history** — requires auth
- **no private repos** — public API only
- **100 repo cap** — GitHub API max per page (covers most users)
- **60 req/hour** — unauthenticated rate limit
- **no real activity data** — grid is simulated

---

## license

mit — use freely in your portfolio, fork, modify, deploy.

---

*built with the github public api · no backend · no auth · no framework*
