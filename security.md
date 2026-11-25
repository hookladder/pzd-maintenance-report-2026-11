<p class="eyebrow">Report</p>

# Security scan

I ran a full Wordfence scan once the final configuration was live. It flagged pending component updates, which were applied straight away. **No open issues remain.**

<div class="cards">
  <div class="card good"><h5>Results found</h5><div class="big">0</div><p>Scan completed Nov 25, 07:49</p></div>
  <div class="card"><h5>Files scanned</h5><div class="big">23,341</div><p>24 plugins · 2 themes · 44 posts</p></div>
  <div class="card"><h5>URLs checked</h5><div class="big">20,095</div><p>Against known-malicious lists</p></div>
  <div class="card"><h5>Users audited</h5><div class="big">4</div><p>Password strength and options</p></div>
</div>

<div class="shots single">
  <figure class="shot">
    <a href="assets/wordfence-scan.png" target="_blank" rel="noopener"><img src="assets/wordfence-scan.png" alt="Wordfence scan complete: 0 results found"></a>
    <figcaption><b>Wordfence → Scan</b> Nov 25 · 0 results · 23,341 files, 26 themes and plugins, 4 users and 20,095 URLs checked.</figcaption>
  </figure>
</div>

## Firewall

During the cycle, Wordfence's firewall blocked a legitimate admin request: Smash Balloon saves its settings as a large JSON string, which matched a generic XSS rule. I allow-listed that single parameter (`model` on `admin-ajax.php`). The firewall stays fully active for every other request.
