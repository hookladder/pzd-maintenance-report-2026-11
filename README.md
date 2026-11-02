<p class="eyebrow">Pizza Delight × Hook + Ladder · Web Team</p>

# Web Maintenance Report — November 2026

<div class="band">
  <div><b>Property</b><span>www.pizzadelight.com</span></div>
  <div><b>Window</b><span>Oct 22 – Nov 1, 2026</span></div>
  <div><b>Stack</b><span>WordPress · Elementor Pro · Polylang · JetEngine</span></div>
  <div><b>Hosting</b><span>WPMU DEV · Static Server Cache · Hummingbird Pro</span></div>
</div>

## Overview

This cycle focused on front-end performance and site health. I removed a site-wide layout shift, got Hummingbird's asset optimization (combine and critical CSS) working, made sure delayed JavaScript no longer holds back the Elementor hero, extended the page-cache lifetime and trimmed unnecessary stylesheets. The home page now scores **93% on GTmetrix** and **92–100 on PageSpeed desktop**, with **zero layout shift** on every page tested. The security scan is clean.

<div class="cards">
  <div class="card good">
    <h5>GTmetrix · Home</h5>
    <div class="big">93% <small>B · 84% structure</small></div>
    <span class="delta up">▲ Performance 64% → 93%</span>
    <p>LCP 1.3 s · TBT 0 ms · CLS 0 · fully loaded in 1.3 s</p>
  </div>
  <div class="card good">
    <h5>PageSpeed · Desktop</h5>
    <div class="big">92–100</div>
    <span class="delta up">▲ Home 73 → 92 · Offers 76 → 100</span>
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
    <span class="delta up">21,484 files · 17,281 URLs</span>
    <p>No malware, no vulnerable components</p>
  </div>
</div>

### At a glance

| Area | Before | After |
|---|---|---|
| Layout shift (CLS), desktop | 0.86 – 0.90 | **0** |
| Render-blocking CSS, inner pages | ~45 stylesheets | **0** — critical CSS inlined |
| Stylesheet requests | ~50 per page | **16 – 21** |
| Page-cache lifetime | 1 hour | **24 hours** |
| Critical CSS generation | Failing | **Optimised** |
| Home hero for visitors | Held back by delayed JS | **Renders on load** |
| Security scan | — | **0 issues** |

> PageSpeed lab scores are simulated (slow 4G, mid-range Android) and vary a few points between runs. Real-user Core Web Vitals are a 28-day rolling average, so they reflect these improvements progressively over the coming weeks.
