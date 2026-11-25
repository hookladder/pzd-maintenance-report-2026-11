<p class="eyebrow">Technical log</p>

# Optimizations

| # | Issue | Fix | Result |
|---|---|---|---|
| 1 | Header top bar CSS loaded after first paint | Load it in `<head>` and preload the brand fonts | CLS 0.86 – 0.90 → **0** |
| 2 | Critical CSS generation failing | Absolute stylesheet URLs, then regenerate | Status **optimised** |
| 3 | ~50 stylesheets competing for the first paint | Full CSS up front on pages, archives and menus; critical CSS plus async loading on blog posts | **Styled first paint** on every template |
| 4 | 1-hour page-cache lifetime | 24-hour lifetime with homepage preload | More visits served from cache (TTFB 0.5 s for real users) |
| 5 | Stylesheets loaded where unused | FA4 shim off; Instagram CSS only on feed pages | 4 – 5 fewer requests |
| 6 | Delayed JavaScript holding back the interface | Interface plugins excluded from Delay JS as whole plugins | Hero, popups, filters and menus work on load · **INP 78 ms** |
| 7 | Menu plugin (MLR) rendering | MLR excluded from critical CSS and Delay JS; assets minified individually | Location finder and menus render correctly |
| 8 | Assets served from the origin server | WPMU DEV CDN enabled | CSS and JavaScript delivered from edge servers |

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

## 3. Stylesheet loading

Pages load ~50 separate stylesheets: Elementor, per-widget and per-template files, JetEngine, popups, filters and more. Each template now gets the strategy that suits it:

| Template | Strategy | First paint |
|---|---|---|
| Pages (Home, About, Stories…) | Full stylesheets loaded up front | **Styled** |
| Archives (Offers) and MLR menus | Full stylesheets loaded up front | **Styled** |
| Blog posts | Critical CSS inlined, remaining stylesheets loaded asynchronously | **Styled** |

Pages, archives and menus are built from Elementor templates, JetEngine listings and the MLR plugin, which a generic critical-CSS extract cannot capture reliably. Loading their full CSS up front guarantees a correct first paint on every template; the stylesheets themselves are minified and served from the CDN. Blog posts have a simple, predictable layout, so they keep critical CSS.

## 4. Page cache

The host's static cache serves cached pages without touching PHP. Raising the lifetime from 1 to **24 hours** keeps the ~1,200 low-traffic location pages cached between visits, and the homepage plus any updated page are preloaded automatically. Real visitors see a **0.5 s** Time to First Byte.

## 5. Unused stylesheets

- **Font Awesome 4 shim off** (Elementor → Settings → Advanced). Icons render as inline SVG, so nothing depends on it.
- **Instagram CSS only on feed pages** (Instagram Feed → Settings → Advanced). The feed appears only on the home page, About Us and the French home page.

## 6. Delayed JavaScript and plugin compatibility

Hummingbird's Delay JavaScript holds scripts until the visitor interacts. Applied to everything, it stopped Elementor sliders, JetPopup popups, Smart Filters and the menu plugin from initialising on load. I excluded the plugins that drive the interface **as whole plugins**, so any script they add in a future update is covered automatically:

- **Page builder and theme:** Elementor, Elementor Pro, the active Hello Elementor child theme
- **Add-ons:** Happy Addons and Happy Addons Pro
- **JetEngine family:** JetEngine, JetPopup, JetSmartFilters, JetGridBuilder
- **MLR Menu Core**
- **Languages:** Polylang and Connect Polylang for Elementor

Shared libraries they rely on (jQuery, jQuery Migrate, jQuery UI, imagesLoaded, `wp-hooks` / `wp-i18n`) are excluded by file, and inline scripts are never delayed, so the configuration these plugins print is available when their files run. Only third-party and non-essential scripts stay delayed: the cookie banner, Google Analytics, the Instagram feed and two small helpers. Every interactive element works on first load, and real visitors record an Interaction to Next Paint of **78 ms**.

## 7. Menu plugin compatibility

The location finder and menu navigation come from the in-house MLR Menu Core plugin. Its scripts run on load (section 6), its templates and menu items are excluded from critical CSS (section 3), and Hummingbird serves every stylesheet and script minified as an individual file instead of combining them, which keeps the plugin's styles in their intended order.

## 8. CDN

I enabled the WPMU DEV CDN from Hummingbird. Minified CSS and JavaScript are now served from edge servers (`*.assetcdn.net`) close to each visitor instead of from the origin in Toronto, which shortens download times across Atlantic Canada and Ontario.
