<p class="eyebrow">Technical log</p>

# Hummingbird Pro notes

Hummingbird Pro is the right optimization layer for this site: WPMU DEV, our host, builds it, and it drives the host's static cache directly. With the configuration below, the site performs well. During this cycle I documented a few limitations of the tool itself, together with the practice that keeps each one from affecting the site.

## Current configuration

| Module | Setting |
|---|---|
| Page caching | Static server · 24-hour lifetime · purge on update |
| Minify | CSS and JavaScript minified as individual files · combine off for plugin compatibility · fonts off |
| Critical CSS | Above the fold · load asynchronously |
| Delay JavaScript | On · 20 s timeout · Elementor and MLR menu scripts excluded |
| Text compression | Brotli (HTML, CSS, JS) |

## Tool limitations and how we handle them

| Limitation | How we handle it |
|---|---|
| **Dashboard status can differ from the front end.** Combine and critical CSS showed "Optimized" while the live HTML was not yet optimized, with no warning in the UI. | After any asset change, I verify the live HTML: inner pages should show critical CSS inlined and no render-blocking stylesheets. |
| **Critical CSS stops on one unresolvable file.** The remote generator cannot read root-relative URLs, and a single one aborts the whole job. | Every hand-added stylesheet uses an absolute URL. |
| **"Clear all cache" also deletes optimized assets.** Pages warmed straight afterwards can be cached before their critical CSS is regenerated. | To refresh content I clear the **page cache only** (Caching → Clear cache). "Clear all cache" is reserved for asset changes. |
| **Toggling Combine re-enables font optimization.** | After any Combine change, I re-check that *Fonts* stays off. |
| **Combining files can change stylesheet order.** Plugins that rely on load order, such as the MLR menu plugin, can render differently once merged. | Files are minified individually. Over HTTP/2 the extra requests cost little, and every plugin keeps its intended styling. |
| **"Load on user interaction" mode defers all CSS until the first tap or scroll.** It looks best in lab tests but can leave the header unstyled on first paint. | The site uses **Load asynchronously**, so every visitor and crawler gets a fully styled first paint. |
| **Query-string list does not reach the host cache.** URLs with unique ad-click IDs (`gclid`, `fbclid`) are cached separately. | Normal visits are unaffected. The host can ignore these parameters at server level if paid traffic grows. |
| **Delay JavaScript has no Elementor awareness.** Delaying Elementor's core scripts stops sliders and entrance animations from initialising until the first interaction. | Elementor, jQuery and their WordPress dependencies are on the exclusion list. After adding a plugin with a frontend script, I check the home hero as a logged-out visitor. |
| **Cache lifetime is capped at 24 hours.** | Purges are event-driven, so 24 hours is enough for this content. |
