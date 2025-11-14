# Manual Addendum 

## 0) Scope

This addendum extends the Camoufox Technical Manual; it does not duplicate installation or API basics. It defines project‑level defaults, prevention of OS‑mismatch risk, and concrete dashboard/back‑end gates that realize these policies. Camoufox capabilities referenced here include protocol‑level WebRTC IP spoofing, Geo/IP‑aligned locale/timezone/headers, execution‑context isolation, and curated WebGL. 

### 0.1) Data provisioning & licensing (owner‑supplied)

**I will provide the databases for GeoLite Country, GeoLite City, and GeoLite ASN, as well as my GeoIP databases and the license key that can be used to automate the database downloads.**

Operational guidance:

* Place the `.mmdb` files where the back‑end expects them (or configure the search path), and keep them refreshed with the provided license key via your update job.
* When `geoip=True` is enabled, Camoufox aligns geolocation, timezone, locale, `Accept‑Language`, and—if WebRTC is enabled—WebRTC IP to the proxy’s region. This behavior depends on valid/updated GeoIP data. 

---

## 1) Project Defaults (policy)

**Headful by default**

* Launch browsers non‑headless by default. On Linux servers, use `headless="virtual"` (Xvfb). Camoufox exposes this option in its runtime parameters. 

**Headful = Humanization off by default**

* In headful sessions, set `humanize=False` by default. Camoufox documents `humanize` with a default of `False`. Mirror that in the UI and launcher. 

**WebRTC blocked by default (leak‑prevention)**

* Set `block_webrtc=True` by default. Only enable WebRTC for workflows that require it and only under proxy + `geoip=True` so host/STUN IPs match egress (Camoufox supports protocol‑level spoofing). 

**GeoIP on by default**

* Enable `geoip=True` by default to align timezone, geolocation, `Intl`/locale and `Accept‑Language`—and, when WebRTC is enabled, its IP—with the exit region. 

**Reference launch options (sync)**

```python
from camoufox.sync_api import Camoufox

with Camoufox(
    headless=False,          # Headful by default
    humanize=False,          # Humanization off in headful
    block_webrtc=True,       # WebRTC disabled by default
    geoip=True               # GeoIP alignment by default
) as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

> Notes: `headless="virtual"` is available for Xvfb on Linux; GeoIP & proxy alignment are first‑class features; `block_webrtc` is a documented toggle. 

---

## 2) OS‑Mismatch — definition, causes, prevention

**Definition**
An OS mismatch occurs when the OS implied by one layer of signals (UA/JS traits) conflicts with the OS inferred from other layers (graphics stack, headers/Intl, TLS/HTTP2 behavior, or proxy egress). Result: CAPTCHAs, flags, or blocks.

**Common causes**

1. **Cross‑layer browser inconsistency** — UA says one OS while fonts/screen/WebGL imply another. Use curated, coherent bundles; avoid ad‑hoc WebGL randomization. 
2. **Geo/Intl/headers drift** — IP shows Region A but timezone/`Intl`/`Accept‑Language` show Region B. Use `geoip=True`. 
3. **TLS/HTTP2 vs UA family** — Keep UA consistent with the Firefox engine and avoid middleboxes that rewrite TLS. 
4. **Proxy egress vs UA** — Don’t “follow” the proxy OS; pin the profile’s OS bundle and align only region/timezone/locale. 
5. **Profile churn** — Avoid changing UA/OS/renderer/fonts/region between logins; use persistent contexts. 

**Prevention**

* **OS pinning:** UA/OS/fonts/screen/WebGL are profile‑pinned and coherent. Avoid random WebGL; keep PDF viewer enabled. 
* **Network/Intl coherence:** With GeoIP on, timezone, locale/Intl, geolocation, `Accept‑Language` (and WebRTC IP if enabled) align to egress. 
* **WebRTC baseline:** Blocked by default; if enabling, require proxy + `geoip=True` so Camoufox’s protocol‑level spoofing exposes only the egress IP. 
* **Sticky profiles:** Prefer `persistent_context=True` with a unique `user_data_dir`. 

---

## 3) Execution‑context hygiene

Read/measure in the **isolated world**; switch to **main world** only when necessary to modify the DOM, as main‑world execution is detectable. 

---

## 4) Launch‑time gates (backend must enforce)

**Block launch** if any of the following holds:

* `block_webrtc=False` **and** (`proxy` missing **or** `geoip=False`).
* OS/UA/fonts/screen/WebGL set is not from a curated, coherent bundle.

**Preflight checklist (block on fail)**

* UA/OS ↔ fonts/screen/WebGL coherence; no randomized WebGL. 
* Geo/Intl/timezone/`Accept‑Language` coherent with GeoIP. 
* WebRTC blocked **or** (proxy + `geoip=True`) verified. 
* Headers aligned to navigator (Camoufox aligns `Accept‑Language` to navigator). 

---

## 5) Dashboard mapping (UI behavior)

**Defaults in UI**

* **Headful by default:** leave the **Headless** checkbox unchecked in the modal (current HTML renders it unchecked). 
* **Humanization off for headful:** flip the modal default to unchecked; the shipped HTML currently has `humanize` **checked** by default. 
* **Proxy geo checks:** the per‑row **Check proxy geo** control and the `/profiles/:id/check-proxy` flow already exist in the enhanced dashboard JS; keep this as the entry point for GeoIP alignment helpers.

**Lifecycle & feedback**

* Real‑time status polling is already implemented at a 3 s cadence; retain this and avoid overlapping requests. 

**Sanitization & safety**

* Render server‑sourced strings with safe insertions; avoid raw HTML concatenation in dynamic rows.

---

## 6) Minimal patterns (correct and aligned with defaults)

**Headful + Humanization off + GeoIP on + WebRTC blocked**

```python
from camoufox.sync_api import Camoufox

with Camoufox(
    headless=False,
    humanize=False,
    geoip=True,
    block_webrtc=True
) as ctx:
    page = ctx.new_page()
    page.goto("https://example.com")
```

Camoufox documents `geoip`, `block_webrtc`, `humanize`, and headless/virtual‑display options; use them exactly as shown. 

**If a workflow requires WebRTC (only under proxy + GeoIP)**

```python
from camoufox.sync_api import Camoufox

with Camoufox(
    headless=False,
    humanize=False,
    proxy={'server': 'http://host:port'},
    geoip=True,
    block_webrtc=False
) as ctx:
    page = ctx.new_page()
    page.goto("https://browserleaks.com/webrtc")
```

Camoufox will spoof host/STUN IPs at the protocol level to match egress. 

---

