# Handoff: ship gavag.ai

The repo is committed locally at `/Users/juliantogelius/code/gavagai` on `main`.
I couldn't push because `gh` wasn't authenticated and no GitHub SSH key was set up.
Three short steps to get it live.

## 1. Authenticate gh (one-time)

```sh
gh auth login
```

Pick: GitHub.com → HTTPS → Login with a web browser. Paste the device code, done.

## 2. Create the repo and push

```sh
cd /Users/juliantogelius/code/gavagai
gh repo create togelius/gavagai --public --source=. --push \
  --description "a page for an indeterminate word"
```

## 3. Turn on Pages with the custom domain

```sh
# Enable Pages from main branch root
gh api -X POST repos/togelius/gavagai/pages \
  -f 'source[branch]=main' -f 'source[path]=/'

# Tell Pages about the custom domain (CNAME file is already in the repo,
# but this also sets it server-side and enables HTTPS provisioning)
gh api -X PUT repos/togelius/gavagai/pages \
  -f 'cname=gavag.ai' -F 'https_enforced=true'
```

If either of those errors, just open
`https://github.com/togelius/gavagai/settings/pages`
and set Source = `main /`, Custom domain = `gavag.ai`, Enforce HTTPS = on.

## 4. DNS at your registrar (the part GitHub can't do)

`gavag.ai` is an apex domain, so it needs **A records** (not a CNAME).
At your registrar's DNS panel, add for `@` / apex:

```
A    @    185.199.108.153
A    @    185.199.109.153
A    @    185.199.110.153
A    @    185.199.111.153
```

(Optional IPv6 — add as AAAA records on `@`:)

```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

(Optional `www` subdomain — CNAME `www` → `togelius.github.io`.)

DNS propagation can take a few minutes to an hour. GitHub then provisions a
Let's Encrypt cert — that can take another 10–30 min after DNS resolves.
You'll see the status under repo Settings → Pages.

---

Local preview (sanity check before pushing):

```sh
cd /Users/juliantogelius/code/gavagai && python3 -m http.server 8765
# then open http://localhost:8765
```
