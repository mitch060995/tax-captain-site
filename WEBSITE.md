# The website: tax-captain.com

Six static pages, one stylesheet, no JavaScript and no build step. GitHub
Pages serves it for free, and Cloudflare holds the domain's DNS.

```
index.html      Home
features.html   Features
about.html      About - includes "Who we are" with the ABN
support.html    Support               <- Apple's Support URL
privacy.html    Privacy Policy        <- Apple's Privacy Policy URL
terms.html      Terms of Service      <- linked from the in-app paywall
styles.css      the whole design
favicon.svg     the browser-tab icon
CNAME           the custom domain - GitHub Pages reads this, never delete it
robots.txt      let search engines in
sitemap.xml     the pages, for search engines
```

---

## 1. DNS at Cloudflare (done - kept for reference)

Set up and working as of 24 September 2026. The domain's DNS lives at
Cloudflare, not at the registrar. If the site ever stops resolving, these are
the records that should be there - dash.cloudflare.com -> **tax-captain.com**
-> **DNS** -> **Records**:

| Type  | Name  | Content                  | Proxy status |
|-------|-------|--------------------------|--------------|
| A     | `@`   | `185.199.108.153`        | DNS only     |
| A     | `@`   | `185.199.109.153`        | DNS only     |
| A     | `@`   | `185.199.110.153`        | DNS only     |
| A     | `@`   | `185.199.111.153`        | DNS only     |
| AAAA  | `@`   | `2606:50c0:8000::153`    | DNS only     |
| AAAA  | `@`   | `2606:50c0:8001::153`    | DNS only     |
| AAAA  | `@`   | `2606:50c0:8002::153`    | DNS only     |
| AAAA  | `@`   | `2606:50c0:8003::153`    | DNS only     |
| CNAME | `www` | `mitch060995.github.io`  | DNS only     |

**Proxy status must be "DNS only" - the grey cloud, not the orange one.**
Cloudflare switches every new record to Proxied by default. Proxied records
answer with Cloudflare's addresses instead of GitHub's, so GitHub's check
keeps failing and it can never issue your HTTPS certificate. Click the orange
cloud on each record until it turns grey.

**Leave the MX and TXT records alone.** They are Cloudflare Email Routing -
they are what makes `support@tax-captain.com` receive mail.

Check it worked, from PowerShell, 5-15 minutes later:

```powershell
nslookup tax-captain.com
```

You want four `185.199.x.153` addresses. A `104.x` or `172.67.x` address
means a record is still proxied.

## 2. HTTPS

GitHub -> the `tax-captain-site` repo -> **Settings** -> **Pages**. Press
**Save** on the custom domain again to make GitHub re-check. When the warning
clears, the **Enforce HTTPS** box becomes tickable - that can take up to a day
after DNS starts working. Tick it.

```powershell
curl.exe -I https://tax-captain.com
```

`HTTP/2 200` means it is live.

---

## 3. Updating the site

Every change goes live the same way:

```powershell
cd C:\Users\mitch\Desktop\Hobbies\tax-captain-site
git pull
git add .
git commit -m "What changed"
git push
```

`git pull` first, every time. GitHub sometimes commits to this repo itself -
it rewrites `CNAME` when you save the custom domain - and a push on top of a
commit you do not have is refused with "Updates were rejected". Pulling first
means that never happens.

Live within a minute or two. To preview before pushing, double-click
`index.html` - there is no build, so what opens is exactly what goes live.

**Without the command line:** github.com -> the repo -> **Add file** ->
**Upload files**, drag the changed files in, **Commit changes**. It works, but
then your local folder is out of date - run `git pull` before the next change
you make locally.

---

## 4. Adding app screenshots

Real screenshots are the single biggest thing this site is missing. Take them
on the phone:

- **Use sample data, not your own.** A map screenshot of your real trips shows
  where you live and work, to anyone. A fresh install (or the web build) opens
  with sample trips - use that, or pick screens with no map on them.
- Four is the right number: the home screen, a trip with its route on the
  map, a receipt being scanned, and the year-end report.
- iPhone: side button + volume up. Android: power + volume down.

Send them over and they get resized, compressed and set into phone frames on
the home page. For reference, they go in an `img/` folder in this repo; keep
each file under about 200 KB, or the page gets slow on a phone on a job site.

---

## What Apple wants from this

**Developer Program enrolment** (the rejection you got): the site must load,
have real content, and be visibly tied to the organisation you enrolled as.
The footer on every page and the "Who we are" section on the About page name
**L.T AUSTIN & M.J CARTER** with the ABN, linked straight to the Australian
Business Register - which also lists the business name Tax-captain. A reviewer
can connect the domain to the partnership in one click.

When you resubmit, check that the organisation name on your enrolment matches
the ABR entity name exactly, and use the `support@tax-captain.com` address as
your contact where you can - an email on the same domain as the website is the
simplest evidence that the domain is yours.

**App Store submission** (later): the form asks for

- **Privacy Policy URL**: `https://tax-captain.com/privacy.html`
- **Support URL**: `https://tax-captain.com/support.html`
- **Marketing URL** (optional): `https://tax-captain.com/`

All three must be live over HTTPS when you submit, and stay live.

**The legal pages were drafted, not lawyered.** They describe what the app
actually does, accurately, which is the important part. Before the
subscription takes real money, an hour with a solicitor on `terms.html` and
`privacy.html` is money well spent.
