<p class="eyebrow">Technical log</p>

# Redirects & links

As part of maintenance I implemented the redirect plan from our SEO specialist and cleaned up the internal links.

| Item | Result |
|---|---|
| Old-site URLs redirected | **867** permanent (301) redirects, each landing in one hop on a live page |
| Internal links pointing at redirects or 404s | **586 pages → 0** |
| Retired pages | `/camper-cooler-contest` returns **410 Gone** |

Redirects are managed in **Tools → Redirection**. The plugin ignores tracking parameters and letter case, so one rule covers every variant of an old URL.
