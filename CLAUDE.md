# BrendanSheppard.com — Site Workflow

## Repos

| Site | Repo | Branch | Local Path |
|------|------|--------|------------|
| dev.brendansheppard.com | github.com/bsh3p/dev.brendansheppard.com | master | ~/dev.brendansheppard.com |
| brendansheppard.com (prod) | github.com/bsh3p/brendansheppard.com | master | not cloned locally |

Both hosted on GitHub Pages — push to master deploys automatically.

## Domain

- Domain registrar: Namecheap
- DNS: CNAME records point to tunnel.cfargotunnel.com (Cloudflare Tunnel)
- Cloudflare Tunnel: Named tunnel "hermes" on this machine, runs as systemd user service
- Cloudflare Access: Both sites gated behind Google OAuth — only Brendan's Google account can access
- CNAME file in repo root: dev.brendansheppard.com / brendansheppard.com respectively

## Cloudflare Tunnel

- Single named tunnel "hermes" handles both domains (plus knowva.app domains)
- Runs via systemctl --user start cloudflared
- Config: ~/.config/systemd/user/cloudflared.service
- Uses token-based auth (no file-based ingress config currently)
- Restart: systemctl --user restart cloudflared

### Quick Tunnels (for one-off exposures)
cloudflared tunnel --url http://localhost:<port>
Creates a trycloudflare.com URL for temporary access. Resets on restart.

## Deploy Workflow

### dev.brendansheppard.com
1. Make changes to files in ~/dev.brendansheppard.com/
2. git add -A && git commit -m "message" && git push origin master
3. GitHub Pages deploys automatically (1-2 min)
4. Verify via curl (expects 302 for Cloudflare Access):
   curl -s -o /dev/null -w "%{http_code}" https://dev.brendansheppard.com/

### brendansheppard.com (prod)
1. Clone or pull brendansheppard.com repo
2. Copy dev site files over
3. Update CNAME to brendansheppard.com
4. git add -A && git commit -m "message" && git push origin master
5. Verify: curl -s -o /dev/null -w "%{http_code}" https://brendansheppard.com/

## Linear

- Team: BRE (brendans)
- Board URL: https://linear.app/brendans
- Issue template: BRE-5
- States: Backlog -> Todo -> In Progress -> Done (or Canceled)
- API script: ~/.hermes/.../linear/scripts/linear_api.py
- Pattern: Always comment on the issue with commit hash and what changed

### Common Linear commands
python3 linear_api.py list-states --team BRE
python3 linear_api.py update-status BRE-N "Done"
python3 linear_api.py add-comment BRE-N "description"

## Site Architecture

Static site — no build step. Plain HTML + CSS (Tailwind v4) + JS.

### File structure
/
├── index.html            # Main page (single-page design)
├── CNAME                 # Custom domain
├── README.md
└── assets/
    ├── main-*.css        # Tailwind v4 compiled CSS with CSS custom properties
    └── main-*.js         # JS bundle

### CSS architecture (Tailwind v4)
- All styles in a single CSS file compiled from Tailwind
- Uses CSS custom properties (--bg, --fg, --accent, etc.) for theming
- Two themes via [data-theme="dark"] attribute on <html>
- Theme persistence via localStorage.getItem('theme') — defaults to dark
- Components: hero, header, cards, logo marquee, contact drawer, mobile nav, footer
- Animations: scroll reveal (IntersectionObserver), staggered delays (d1-d4), marquee, status pill pulse

### JS bundle features
- Theme toggle (sun/moon icons, localStorage, desktop + mobile controls)
- Sticky header with scrolled class
- Mobile nav hamburger drawer with backdrop
- Contact drawer with copy-to-clipboard
- Footer email tooltip on copy
- Scroll reveal animations (disable for prefers-reduced-motion)

## Past Deployments

| Date | Description | Commit | Linear |
|------|-------------|--------|--------|
| 2026-07-17 | Complete site rebuild: Tailwind v4, new design | d28a851 | BRE-5 |
| 2026-07-17 | Fix hero-media column layout | a2312a1 | BRE-5 |

## Other Resources

- Cloudinary account: textac — hosts headshot (Headshot_white_tn3o7u.png) and company logos
- Logos: Use logo-mono class for single-colour logos. Light/dark pair pattern via logo-light/logo-dark classes with onerror fallback
- Status pill: Shows availability — status-pill--available (green pulse), status-pill--limited (amber), status-pill--unavailable (red)
- Email: hello@brendan.pro
- LinkedIn: /in/brendansheppard
