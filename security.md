<p class="eyebrow">Report</p>

# Security scan

I ran a full Wordfence scan after all the changes in this cycle were live. **No issues were found.**

<div class="cards">
  <div class="card good"><h5>Results found</h5><div class="big">0</div><p>Scan completed Nov 1, 18:50</p></div>
  <div class="card"><h5>Files scanned</h5><div class="big">21,484</div><p>24 plugins · 2 themes · 44 posts</p></div>
  <div class="card"><h5>URLs checked</h5><div class="big">17,281</div><p>Against known-malicious lists</p></div>
  <div class="card"><h5>Users audited</h5><div class="big">6</div><p>Password strength and options</p></div>
</div>

<div class="shots single">
  <figure class="shot">
    <a href="assets/wordfence-scan.png" target="_blank" rel="noopener"><img src="assets/wordfence-scan.png" alt="Wordfence scan complete: 0 results found"></a>
    <figcaption><b>Wordfence → Scan</b> Server state, file changes, malware, content safety, public files, passwords, vulnerabilities and user audit all passed.</figcaption>
  </figure>
</div>

## Firewall

During the cycle, Wordfence's firewall blocked a legitimate admin request: Smash Balloon saves its settings as a large JSON string, which matched a generic XSS rule. I allow-listed that single parameter (`model` on `admin-ajax.php`). The firewall stays fully active for every other request.
