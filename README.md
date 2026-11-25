<p class="eyebrow">Pizza Delight × Hook + Ladder · Web Team</p>

# Web Maintenance Report — November 2026

<div class="band">
  <div><b>Property</b><span>www.pizzadelight.com</span></div>
  <div><b>Window</b><span>Oct 22 – Nov 25, 2026</span></div>
  <div><b>Stack</b><span>WordPress · Elementor Pro · Polylang · JetEngine</span></div>
  <div><b>Hosting</b><span>WPMU DEV · Static Server Cache · Hummingbird Pro · CDN</span></div>
</div>

## Overview

This cycle focused on front-end performance and site health. I removed a site-wide layout shift, extended the page-cache lifetime, trimmed unnecessary stylesheets and then tuned Hummingbird into a configuration that is fully compatible with Elementor, the JetEngine plugins and the MLR menu plugin, with the WPMU DEV CDN serving assets. Real visitors on mobile now **pass all Core Web Vitals** across the site, desktop pages score **91–99 on PageSpeed**, and layout shift is **0** on every page tested. The security scan is clean.

<div class="cards">
  <div class="card good">
    <h5>Real users · Mobile</h5>
    <div class="big">Passed <small>Core Web Vitals</small></div>
    <span class="delta up">LCP 1.1 s · INP 78 ms · CLS 0</span>
    <p>Site-wide, Chrome UX Report, 28 days</p>
  </div>
  <div class="card good">
    <h5>PageSpeed · Desktop</h5>
    <div class="big">91–99</div>
    <span class="delta up">▲ Home 73 → 91 · Offers 76 → 99</span>
    <p>Home, offers and menu templates</p>
  </div>
  <div class="card good">
    <h5>Layout shift (CLS)</h5>
    <div class="big">0</div>
    <span class="delta up">▲ from 0.86 – 0.90</span>
    <p>All pages tested, mobile and desktop</p>
  </div>
  <div class="card good">
    <h5>Wordfence scan</h5>
    <div class="big">0 <small>issues</small></div>
    <span class="delta up">23,341 files · 20,095 URLs</span>
    <p>No malware, no open vulnerabilities</p>
  </div>
</div>

### At a glance

| Area | Before | After |
|---|---|---|
| Core Web Vitals, mobile (real users) | Home LCP 5.2 s | **Passed** site-wide · LCP 1.1 s |
| Layout shift (CLS), desktop | 0.86 – 0.90 | **0** |
| Styled first paint | Not on every template | **Every template** |
| Elementor, JetEngine and MLR with optimizations on | Conflicts | **Fully working** |
| Asset delivery | Origin server | **WPMU DEV CDN** |
| Page-cache lifetime | 1 hour | **24 hours** |
| Security scan | — | **0 issues** |

> PageSpeed lab scores are simulated (slow 4G, mid-range Android) and vary a few points between runs. Real-user Core Web Vitals are a 28-day rolling average and keep improving as older visits age out of the window.
