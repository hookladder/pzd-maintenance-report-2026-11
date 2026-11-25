<p class="eyebrow">Technical log</p>

# Hummingbird Pro notes

Hummingbird Pro is the right optimization layer for this site: WPMU DEV, our host, builds it, and it drives the host's static cache and CDN directly. With the configuration below, the site performs well and every plugin keeps working. During this cycle I documented a few limitations of the tool itself, together with the practice that keeps each one from affecting the site.

## Current configuration

| Module | Setting |
|---|---|
| Page caching | Static server · 24-hour lifetime · homepage and updated pages preloaded · purge on update |
| Minify | CSS and JavaScript minified as individual files · combine off for plugin compatibility · fonts off |
| CDN | WPMU DEV CDN on · CSS and JavaScript served from edge servers |
| Critical CSS | On · remaining stylesheets load asynchronously · pages, archives, menu items and the MLR plugin load their full CSS up front |
| Delay JavaScript | On · 20 s timeout · inline scripts not delayed · whole plugins excluded: Elementor, Elementor Pro, the active theme, Happy Addons + Pro, JetEngine, JetPopup, JetSmartFilters, JetGridBuilder, MLR Menu Core, Polylang + Polylang for Elementor · core libraries excluded by file |
| Text compression | Brotli (HTML, CSS, JS) |
| Advanced tools | Emoji scripts removed · viewport meta optimized · asset version strings kept for reliable cache busting |

## Tool limitations and how we handle them

| Limitation | How we handle it |
|---|---|
| **Delay JavaScript has no awareness of page builders or plugins.** Delaying their core scripts stops sliders, popups, filters and menus from initialising until the first interaction. | Plugins that drive the interface are excluded **as whole plugins**, so any script they add in a future update is covered automatically. Their inline configuration is never delayed. After adding a plugin with a frontend script, I check the site as a logged-out visitor. |
| **Critical CSS is a generic above-the-fold extract.** Templates built from listings or plugin markup can paint incomplete if only the extract loads first. | Pages, archives, menu items and the MLR plugin load their full CSS up front; blog posts use critical CSS with the rest loaded asynchronously. |
| **Lab scores reward aggressive deferral.** Deferring all CSS and JavaScript raises the simulated mobile score but breaks the interface on first load. | The configuration favours a correct, fully interactive first load. Real-user Core Web Vitals on mobile pass site-wide. |
| **Dashboard status can differ from the front end.** Critical CSS can show "Optimized" while the live HTML is not yet updated, with no warning in the UI. | After any asset change, I verify the live HTML and the rendered page as a logged-out visitor. |
| **Critical CSS stops on one unresolvable file.** The remote generator cannot read root-relative URLs, and a single one aborts the whole job. | Every hand-added stylesheet uses an absolute URL. |
| **"Clear all cache" also deletes optimized assets.** Pages warmed straight afterwards can be cached before their critical CSS is regenerated. | To refresh content I clear the **page cache only** (Caching → Clear cache). "Clear all cache" is reserved for asset changes. |
| **Toggling Combine re-enables font optimization.** | Combine stays off; after any asset change, I re-check that *Fonts* stays off. |
| **Combining files can change stylesheet order.** Plugins that rely on load order, such as the MLR menu plugin, can render differently once merged. | Files are minified individually and served from the CDN over HTTP/2, so the extra requests cost little. |
| **Query-string list does not reach the host cache.** URLs with unique ad-click IDs (`gclid`, `fbclid`) are cached separately. | Normal visits are unaffected. The host can ignore these parameters at server level if paid traffic grows. |
| **Cache lifetime is capped at 24 hours.** | Purges are event-driven and key pages are preloaded, so 24 hours is enough for this content. |
