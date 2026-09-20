# The website: tax-captain.com

Six static pages, one stylesheet, no JavaScript and no build step. GitHub
Pages serves it for free, including the HTTPS certificate for your own domain.

```
index.html      Home
features.html   Features
about.html      About
support.html    Support
privacy.html    Privacy Policy      <- Apple asks for this URL
terms.html      Terms of Service
styles.css      the whole design
CNAME           the custom domain, read by GitHub Pages
robots.txt      let search engines in
sitemap.xml     the six pages, for search engines
```

`CNAME` is not optional and not a normal file: GitHub Pages reads it on every
deploy to know which domain this site answers on. It contains one line,
`tax-captain.com`, no `https://` and no trailing slash. If it goes missing,
the custom domain silently unbinds and the site falls back to
`<you>.github.io/<repo>`.

---

## Step 0 - move this folder out of the app

It was delivered to `taxapp\_website\` because that is the only folder this
machine shares. It does not belong inside the app repo - the app repo is
private and this one has to be public. Move it first:

```powershell
Move-Item C:\Users\mitch\Desktop\Hobbies\taxapp\_website `
          C:\Users\mitch\Desktop\Hobbies\tax-captain-site
```

Everything below happens in the new folder.

---

## Step 1 - make the repository

The repository has to be **public**. GitHub Pages on a free account will not
serve a private repo, and there is nothing secret in here anyway.

1. github.com -> **+** (top right) -> **New repository**
2. Name: `tax-captain-site`
3. Visibility: **Public**
4. Leave "Add a README", .gitignore and licence all **unticked** - an empty
   repo is what the commands below expect
5. **Create repository**

## Step 2 - push the files

In `C:\Users\mitch\Desktop\Hobbies\tax-captain-site`:

```powershell
git init
git add .
git commit -m "Tax Captain website"
git branch -M main
git remote add origin https://github.com/<your-username>/tax-captain-site.git
git push -u origin main
```

Replace `<your-username>`. If git asks for a password, it wants a **personal
access token**, not your GitHub password: github.com -> Settings -> Developer
settings -> Personal access tokens -> Tokens (classic) -> Generate new token,
tick `repo`, copy it, paste it as the password. Windows will remember it.

## Step 3 - turn Pages on

Repository -> **Settings** -> **Pages** (left sidebar).

- **Source**: Deploy from a branch
- **Branch**: `main`, folder `/ (root)`
- **Save**

Wait a minute or two. The site appears at
`https://<your-username>.github.io/tax-captain-site/`. Check it works there
**before** touching DNS - if something is wrong, you want to know it is the
site and not the domain.

Note the pages will look slightly broken at that address if you visit a
sub-page directly, because links are relative. That is fine; on the real
domain everything sits at the root.

## Step 4 - point the domain at it

Two halves, and both are needed.

**On GitHub:** Settings -> Pages -> **Custom domain** -> type
`tax-captain.com` -> **Save**. GitHub will re-commit the `CNAME` file; that
is expected. Run `git pull` afterwards so your local copy matches.

**At your registrar** (wherever you bought tax-captain.com), open the DNS
records for the domain and add:

| Type  | Name / Host | Value                  |
|-------|-------------|------------------------|
| A     | `@`         | `185.199.108.153`      |
| A     | `@`         | `185.199.109.153`      |
| A     | `@`         | `185.199.110.153`      |
| A     | `@`         | `185.199.111.153`      |
| AAAA  | `@`         | `2606:50c0:8000::153`  |
| AAAA  | `@`         | `2606:50c0:8001::153`  |
| AAAA  | `@`         | `2606:50c0:8002::153`  |
| AAAA  | `@`         | `2606:50c0:8003::153`  |
| CNAME | `www`       | `<your-username>.github.io.` |

All four A records, all four AAAA records. `@` means the bare domain; some
registrars want the field left blank instead. The AAAA records are optional
but cost nothing and cover people on IPv6-only mobile networks.

**Do not delete your MX records.** Those are what make
`support@tax-captain.com` receive mail. A records, AAAA records and MX
records live side by side and do different jobs. If the registrar offers to
"reset to defaults" or "park the domain", say no.

## Step 5 - HTTPS

DNS takes anywhere from ten minutes to a few hours. Once GitHub sees it,
Settings -> Pages stops showing the yellow "DNS check in progress" warning
and the **Enforce HTTPS** checkbox becomes tickable. Tick it. GitHub issues
and renews a Let's Encrypt certificate automatically and forever.

Until that box is ticked the site is reachable over plain HTTP, which Apple
will not accept for a privacy policy URL. Check it before you submit.

Verify from PowerShell:

```powershell
nslookup tax-captain.com
curl.exe -I https://tax-captain.com
```

The first should list the four GitHub IPs. The second should say `HTTP/2 200`.

---

## Updating the site later

Edit the files, then:

```powershell
git add .
git commit -m "what changed"
git push
```

Live in under a minute. There is no build and no deploy step to wait on.

To preview a change before pushing, just double-click `index.html` - it opens
in your browser and works exactly as it will live, because there is no build.

---

## What Apple wants from this

The App Store submission form has fields for:

- **Privacy Policy URL** (required): `https://tax-captain.com/privacy.html`
- **Support URL** (required): `https://tax-captain.com/support.html`
- **Marketing URL** (optional): `https://tax-captain.com/`

All three must be live and reachable over HTTPS at the moment you submit, and
must stay live afterwards. A dead privacy policy URL is a rejection.

Two things the website does **not** do for you:

1. **The privacy "nutrition label"** in App Store Connect is a separate
   questionnaire you fill in there. For this app the honest answers are "no
   data collected" throughout - the app has no server and sends nothing. Be
   ready to say so when the review asks why an app with location permission
   collects nothing.
2. **`privacy.html` and `terms.html` were drafted, not lawyered.** They
   describe what the app actually does, accurately, which is the important
   part. But if this is going to take money or carry real liability, spend an
   hour with a solicitor on them. They are a starting point, not a finished
   legal document.
