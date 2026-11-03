<p class="eyebrow">Technical log</p>

# Optimizations

| # | Issue | Fix | Result |
|---|---|---|---|
| 1 | Header top bar CSS loaded after first paint | Load it in `<head>` and preload the brand fonts | CLS 0.86 – 0.90 → **0** |
| 2 | Critical CSS generation failing | Absolute stylesheet URLs, then regenerate | Status **optimised** |
| 3 | ~50 render-blocking stylesheets | Critical CSS inlined, remaining stylesheets deferred | **0** on inner pages |
| 4 | 1-hour page-cache lifetime | 24-hour lifetime | More visits served from cache (0.34 s TTFB) |
| 5 | Stylesheets loaded where unused | FA4 shim off; Instagram CSS only on feed pages | 4 – 5 fewer requests |
| 6 | Delayed JavaScript holding back the Elementor hero | Elementor core scripts excluded from Delay JS | Hero renders on load, TBT stays 0 ms |
| 7 | Menu plugin (MLR) rendering | MLR scripts excluded from Delay JS; assets minified individually | Location finder and menus render correctly |

## 1. Layout shift from the header top bar

The header's language bar is a separate Elementor template (11086 EN / 11091 FR) embedded through a Template widget. Elementor printed its stylesheet inside `<body>`, so the bar painted unstyled and then resized, pushing the whole page down. I confirmed it by lining up `layout-shift` entries with the resource timeline: the shift landed right after that CSS arrived.

**Fix** — Elementor Custom Code *"Header CLS fix: fonts + top bar CSS in head"* (head, entire site):

```html
<link rel="stylesheet" href="https://www.pizzadelight.com/wp-content/plugins/elementor/assets/css/widget-icon-list.min.css">
<link rel="stylesheet" href="https://www.pizzadelight.com/wp-content/uploads/elementor/css/post-11086.css">
<link rel="stylesheet" href="https://www.pizzadelight.com/wp-content/uploads/elementor/css/post-11091.css">
<link rel="preload" href="https://www.pizzadelight.com/wp-content/uploads/2025/11/trade_gothic_lt_std_regular-webfont.woff2" as="font" type="font/woff2" crossorigin>
<link rel="preload" href="https://www.pizzadelight.com/wp-content/uploads/2025/11/trade_gothic_lt_std_bold-webfont.woff2" as="font" type="font/woff2" crossorigin>
<link rel="preload" href="https://www.pizzadelight.com/wp-content/uploads/elementor/google-fonts/fonts/roboto-0f7fcdc8.woff2" as="font" type="font/woff2" crossorigin>
```

The font preloads remove a smaller secondary shift caused by the fallback-to-web-font swap. CLS is now **0** on every page tested.

## 2. Critical CSS generation

Hummingbird generates critical CSS on WPMU DEV's servers. It could not resolve root-relative stylesheet URLs (`/wp-content/...`) and stopped the whole job with `File not found`. Switching the snippet to absolute URLs fixed it, and generation now completes.

## 3. Render-blocking CSS

Pages loaded ~50 separate stylesheets: Elementor, per-widget and per-template files, JetEngine, popups, filters and more. With critical CSS working, inner pages now inline their above-the-fold CSS and load the rest without blocking the first paint. Every stylesheet is served minified.

| Page | Render-blocking stylesheets |
|---|---|
| Offers & promotions | ~40 → **0** |
| Menu — Antigonish | ~42 → **0** |

## 4. Page cache

The host's static cache serves a page in **0.34 s** versus ~1 s for a PHP render. Raising the lifetime from 1 to **24 hours** keeps the ~1,200 low-traffic location pages cached between visits. Content edits still purge the affected pages automatically.

## 5. Unused stylesheets

- **Font Awesome 4 shim off** (Elementor → Settings → Advanced). Icons render as inline SVG, so nothing depends on it.
- **Instagram CSS only on feed pages** (Instagram Feed → Settings → Advanced). The feed appears only on the home page, About Us and the French home page.

## 6. Delayed JavaScript and Elementor

Hummingbird's Delay JavaScript held Elementor's frontend scripts until the visitor interacted, so first-time visitors on the home page could see the slideshow area before it initialised. I excluded the scripts Elementor needs to start (webpack runtime, frontend, frontend modules, Pro handlers, jQuery, Swiper, sticky, SmartMenus and `wp-hooks` / `wp-i18n`). Everything else stays delayed. The hero now renders on load, and Total Blocking Time is still **0 ms**.

## 7. Menu plugin compatibility

The location finder and menu navigation come from the in-house MLR Menu Core plugin. Its scripts (`mlr-front`, `mlr-gate`) are now excluded from Delay JavaScript, together with `jquery-ui-core` and `jquery-numerator`, so the finder and menus work on first load. Hummingbird serves each stylesheet and script minified as an individual file instead of combining them, which keeps the plugin's styles in their intended order. Total Blocking Time stays at **0–20 ms**.
