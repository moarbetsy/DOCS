# Camoufox Technical Manual

## Overview

Camoufox is an open‑source, Firefox‑based anti‑detect browser focused on robust, low‑level **fingerprint injection and rotation** without JavaScript (JS) injection. It blends device, network, and rendering characteristics into real‑world distributions to resist bot detection, graphical fingerprinting, and proxy‑based anomalies. A Python library wraps the Playwright API to automate fingerprint generation, injection, and environment configuration.

> **Project status:** Active development. Releases exist but may not be suitable for production use.

---

## Table of Contents

* [Overview](#overview)
* [Design & Architecture](#design--architecture)
* [Features](#features)

  * [Fingerprint Spoofing](#fingerprint-spoofing)
  * [Stealth Patches](#stealth-patches)
  * [Anti‑Font Fingerprinting](#anti-font-fingerprinting)
  * [Playwright Support](#playwright-support)
  * [Debloat/Optimizations](#debloatoptimizations)
  * [Addons](#addons)
* [Installation](#installation)

  * [Camoufox CLI](#camoufox-cli)
* [Python API](#python-api)

  * [Quick Start](#quick-start)
  * [Runtime Parameters](#runtime-parameters)
  * [GeoIP & Proxy Support](#geoip--proxy-support)
  * [Execution Contexts (Isolated vs Main World)](#execution-contexts-isolated-vs-main-world)
  * [Remote Server](#remote-server)
  * [Virtual Display](#virtual-display)
  * [BrowserForge Integration](#browserforge-integration)
  * [Overriding Config Values](#overriding-config-values)
* [Fingerprint Injection Reference](#fingerprint-injection-reference)

  * [Navigator](#navigator)
  * [Cursor Movement](#cursor-movement)
  * [Fonts](#fonts)
  * [Screen](#screen)
  * [Window](#window)
  * [Document](#document)
  * [Canvas](#canvas)
  * [WebGL](#webgl)
  * [Geolocation & Intl](#geolocation--intl)
  * [HTTP Headers](#http-headers)
  * [WebRTC IP](#webrtc-ip)
  * [Media & Audio](#media--audio)
  * [Voices](#voices)
  * [Addons (Runtime)](#addons-runtime)
  * [Miscellaneous](#miscellaneous)
* [Stealth Performance](#stealth-performance)
* [Development](#development)

  * [Build System](#build-system)
  * [Building on CLI](#building-on-cli)
  * [Building with Docker](#building-with-docker)
  * [Development Tools](#development-tools)
  * [Leak Debugging Workflow](#leak-debugging-workflow)
* [WebGL Research Snapshot](#webgl-research-snapshot)
* [Legal](#legal)
* [Appendix A: Fingerprintable Properties Tree](#appendix-a-fingerprintable-properties-tree)

---

## Design & Architecture

### Fingerprinting protection (low‑level injection)

Most privacy add‑ons are JS‑observable and create distinctive fingerprints. Camoufox modifies device, system, and rendering data inside the Firefox C++ implementation. No JS injection or DOM manipulation is needed, minimizing JS‑detectable artifacts.

### Crowdblending

To avoid statistical clustering, Camoufox rotates device traits (screen, OS, hardware, etc.) using **BrowserForge** so characteristics match real‑world distributions. A built‑in human‑like mouse model further normalizes behavior.

### Stealth

Playwright’s **Page Agent** scripts run in a sandboxed, isolated world. Combined with low‑level interception, this makes Playwright/Juggler presence non‑observable via standard JS inspection.

### Why Firefox (not Chromium)

1. Chrome/Chromium parity issues enable detection; Chrome is harder to patch (closed source).
2. CDP is widely targeted by bot detection; Juggler operates at a lower level.
3. Firefox has stronger prior art for fingerprint resistance and rotation.

### Open source model

Nearly all code is public. Some patches (e.g., **Canvas fingerprint rotation**) are closed‑source to reduce reverse‑engineering risk. Builds without closed components are supported via the public build guide.

### Roadmap (abridged)

* Ongoing leak research
* AI‑powered selector location
* Remote hosting as a Playwright server

---

## Features

### Fingerprint Spoofing

* Navigator/device/OS/hardware/browser properties
* Screen/viewport/window dimensions
* Geolocation, timezone, locale, `Intl.*`
* **WebRTC IP spoofing** at the protocol level
* Voices & speech playback parameters
* AudioContext sample rate/latency/channels
* Media device counts (mics/webcams/speakers)
* Network headers aligned to navigator
* Battery API
* WebGL: parameters, extensions, context attributes, shader precision

### Stealth Patches

* No main‑world execution leaks (sandboxed agents)
* No frame execution context leaks
* `navigator.webdriver` fixed
* Headless pointer‑type signal fixed
* Non‑default sizes; re‑enabled fission; re‑enabled PDF.js
* Human‑like cursor movement

### Anti‑Font Fingerprinting

* System‑correct fonts per User‑Agent (bundled Win/Mac/Linux sets)
* Font metrics perturbation to resist measurement

### Playwright Support

* Custom Playwright implementation for latest Firefox
* Config patches to evade detection

### Debloat/Optimizations

* Extensive telemetry/bloat removal; reduced memory usage
* Performance/network tuning; animations removed; minimal theming

### Addons

* Load addons via file paths; uBlock Origin included
* Addons auto‑enabled in Private Browsing; pinned to toolbar
* Prevent addon‑opened tabs; fix DNS prefetch leaks in uBO

---

## Installation

### 1) Install the Python package

```bash
pip install -U camoufox[geoip]
```

`[geoip]` is optional but recommended when using proxies (adds datasets for lat/lon, timezone, country, locale).

### 2) Download the browser binary

```bash
camoufox fetch
```

### 3) Uninstall (optional)

```bash
camoufox remove
```

### Camoufox CLI

```text
Usage: python -m camoufox [OPTIONS] COMMAND [ARGS]...

Options:
  --help  Show this message and exit.

Commands:
  fetch    Fetch the latest version of Camoufox
  path     Display the path to the Camoufox executable
  remove   Remove all downloaded files
  server   Launch a Playwright server
  test     Open the Playwright inspector
  version  Display the current version
```

**CLI command summary**

| Command   | Description                          |
| --------- | ------------------------------------ |
| `fetch`   | Download the latest Camoufox build.  |
| `path`    | Print the Camoufox executable path.  |
| `remove`  | Delete downloaded files.             |
| `server`  | Start a Playwright WebSocket server. |
| `test`    | Launch the Playwright inspector.     |
| `version` | Show current Camoufox version.       |

---

## Python API

### Quick Start

**Sync**

```python
from camoufox.sync_api import Camoufox

with Camoufox() as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

**Async**

```python
import asyncio
from camoufox.async_api import Camoufox

async def main():
    async with Camoufox() as browser:
        page = await browser.new_page()
        await page.goto("https://example.com")

asyncio.run(main())
```

### Runtime Parameters

All Playwright Firefox launch options are supported. The parameters below are **Camoufox‑specific**.

**Device rotation**

| Parameter      | Type                        | Description                                                                 |
| -------------- | --------------------------- | --------------------------------------------------------------------------- |
| `os`           | `Optional[ListOrString]`    | Target OS: `"windows"`, `"macos"`, `"linux"`, or a list for random choice.  |
| `fonts`        | `Optional[List[str]]`       | Extra font families to load in addition to OS defaults.                     |
| `screen`       | `Optional[Screen]`          | Constrain generated screen dimensions (`browserforge.fingerprints.Screen`). |
| `webgl_config` | `Optional[Tuple[str, str]]` | Fixed WebGL `(vendor, renderer)` pair.                                      |

**Configuration**

| Parameter            | Type                                        | Description                                                   |
| -------------------- | ------------------------------------------- | ------------------------------------------------------------- |
| `humanize`           | `Optional[Union[bool, float]]`              | Human‑like cursor movement. `True` or max duration (seconds). |
| `headless`           | `Optional[Union[bool, Literal['virtual']]]` | Headless mode; `'virtual'` uses Xvfb on Linux.                |
| `addons`             | `Optional[List[str]]`                       | Paths to extracted Firefox addons.                            |
| `exclude_addons`     | `Optional[List[DefaultAddons]]`             | Exclude bundled addons.                                       |
| `window`             | `Optional[Tuple[int, int]]`                 | Browser window size `(width, height)` in px.                  |
| `main_world_eval`    | `Optional[bool]`                            | Allow `mw:` prefix to run scripts in main world.              |
| `enable_cache`       | `Optional[bool]`                            | Enable page/request caching.                                  |
| `persistent_context` | `Optional[bool]`                            | Use a persistent context. Requires `user_data_dir`.           |

**Location & language**

| Parameter | Type                              | Description                                                                 |
| --------- | --------------------------------- | --------------------------------------------------------------------------- |
| `geoip`   | `Optional[Union[str, bool]]`      | Auto‑set lat/lon/timezone/country/locale from IP; pass IP string or `True`. |
| `locale`  | `Optional[Union[str, List[str]]]` | One or more locales (comma‑separated or list).                              |

**Toggles**

| Parameter      | Type             | Description                                |
| -------------- | ---------------- | ------------------------------------------ |
| `block_images` | `Optional[bool]` | Block all image requests.                  |
| `block_webrtc` | `Optional[bool]` | Disable WebRTC.                            |
| `block_webgl`  | `Optional[bool]` | Disable WebGL.                             |
| `disable_coop` | `Optional[bool]` | Disable Cross‑Origin‑Opener‑Policy (COOP). |

### GeoIP & Proxy Support

Install with GeoIP extras and pass proxy credentials; Camoufox will align **geolocation, timezone, locale, language**, and **WebRTC IP** to the target region.

```bash
pip install -U "camoufox[geoip]"
```

```python
from camoufox.sync_api import Camoufox

with Camoufox(
    geoip=True,
    proxy={
        'server': 'http://example.com:8080',
        'username': 'username',
        'password': 'password'
    }
) as browser:
    page = browser.new_page()
    page.goto("https://www.browserscan.net")
```

### Execution Contexts (Isolated vs Main World)

* **Isolated world (default):** JS runs in a sandbox invisible to the page. Safe for reading the DOM; cannot modify it.
* **Main world:** Enables DOM modification but is **detectable** by the site.

Enable main‑world execution and prefix code with `mw:`:

```python
page.evaluate("mw:document.querySelector('h1').remove()")
```

> **Leak warning:** Execute in main world **only if necessary**.

### Remote Server

Run Camoufox as a remote Playwright WebSocket server.

**CLI**

```bash
python -m camoufox server
```

**Python launcher**

```python
from camoufox.server import launch_server
launch_server(port=1234, ws_path='hello')
```

**Connect from Playwright**

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.firefox.connect('ws://localhost:1234/hello')
    page = browser.new_page()
```

### Virtual Display

Recommended for headful rendering on headless Linux. Install Xvfb and pass `headless="virtual"`.

**Install Xvfb**

```bash
# Debian-based
sudo apt-get install xvfb
# Arch-based
sudo pacman -S xorg-server-xvfb
```

**Use virtual display**

```python
from camoufox.sync_api import Camoufox

with Camoufox(headless="virtual") as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

### BrowserForge Integration

Camoufox uses BrowserForge fingerprints by default and allows constraints.

```python
from camoufox.sync_api import Camoufox
from browserforge.fingerprints import Screen

with Camoufox(
    os=('windows', 'macos', 'linux'),
    screen=Screen(max_width=1920, max_height=1080),
) as browser:
    page = browser.new_page()
```

### Overriding Config Values

You may override individual config keys. This is generally **not recommended**; Camoufox populates coherent values automatically.

```python
from camoufox.sync_api import Camoufox

with Camoufox(
    config={
        'webrtc:ipv4': '123.45.67.89',
        'webrtc:ipv6': 'e791:d37a:88f6:48d1:2cad:2667:4582:1d6d',
    }
) as browser:
    page = browser.new_page()
```

---

## Fingerprint Injection Reference

All spoofing occurs below JS level (C++), avoiding JS‑detectable mutations. Any field you do **not** set is auto‑filled using BrowserForge distributions.

### Navigator

`navigator.webdriver` is always `false`.

| Property                         | Type    |
| -------------------------------- | ------- |
| `navigator.userAgent`            | `str`   |
| `navigator.doNotTrack`           | `str`   |
| `navigator.appCodeName`          | `str`   |
| `navigator.appName`              | `str`   |
| `navigator.appVersion`           | `str`   |
| `navigator.oscpu`                | `str`   |
| `navigator.language`             | `str`   |
| `navigator.languages`            | `array` |
| `navigator.platform`             | `str`   |
| `navigator.hardwareConcurrency`  | `uint`  |
| `navigator.product`              | `str`   |
| `navigator.productSub`           | `str`   |
| `navigator.maxTouchPoints`       | `uint`  |
| `navigator.cookieEnabled`        | `bool`  |
| `navigator.globalPrivacyControl` | `bool`  |
| `navigator.buildID`              | `str`   |
| `navigator.onLine`               | `bool`  |

### Cursor Movement

| Property           | Type     | Description                                        |
| ------------------ | -------- | -------------------------------------------------- |
| `humanize`         | `bool`   | Enable/disable human‑like cursor. Default `False`. |
| `humanize:maxTime` | `double` | Max duration (s). Default `1.5`.                   |
| `humanize:minTime` | `double` | Min duration (s).                                  |
| `showcursor`       | `bool`   | Toggle cursor highlighter. Default `True`.         |

### Fonts

Bundled with Windows 11, macOS Sonoma, and Linux fonts; uses OS‑correct fonts per UA. Prevents font‑metric and glyph‑based fingerprinting via randomized sub‑pixel spacing.

| Property             | Type    | Description                            |
| -------------------- | ------- | -------------------------------------- |
| `fonts`              | `array` | Font families to include.              |
| `fonts:spacing_seed` | `uint`  | Seed for metric spacing randomization. |

### Screen

| Property             | Type     |
| -------------------- | -------- |
| `screen.availHeight` | `uint`   |
| `screen.availWidth`  | `uint`   |
| `screen.availTop`    | `uint`   |
| `screen.availLeft`   | `uint`   |
| `screen.height`      | `uint`   |
| `screen.width`       | `uint`   |
| `screen.colorDepth`  | `uint`   |
| `screen.pixelDepth`  | `uint`   |
| `screen.pageXOffset` | `double` |
| `screen.pageYOffset` | `double` |

### Window

| Property                  | Type     |
| ------------------------- | -------- |
| `window.scrollMinX`       | `int`    |
| `window.scrollMinY`       | `int`    |
| `window.scrollMaxX`       | `int`    |
| `window.scrollMaxY`       | `int`    |
| `window.outerHeight`      | `uint`   |
| `window.outerWidth`       | `uint`   |
| `window.innerHeight`      | `uint`   |
| `window.innerWidth`       | `uint`   |
| `window.screenX`          | `int`    |
| `window.screenY`          | `int`    |
| `window.history.length`   | `uint`   |
| `window.devicePixelRatio` | `double` |

### Document

| Property                     | Type   |
| ---------------------------- | ------ |
| `document.body.clientWidth`  | `uint` |
| `document.body.clientHeight` | `uint` |
| `document.body.clientTop`    | `uint` |
| `document.body.clientLeft`   | `uint` |

### Canvas

Canvas anti‑fingerprinting uses a patched Skia subpixel pipeline (**closed source**).

| Property             | Type   | Description                                  |
| -------------------- | ------ | -------------------------------------------- |
| `canvas:aaOffset`    | `int`  | Offset per‑pixel alpha during anti‑aliasing. |
| `canvas:aaCapOffset` | `bool` | Clamp alpha to `[0,255]` rather than wrap.   |

### WebGL

> **Important:** Do **not** assign random WebGL values. WAFs hash these and compare to real devices.

| Property                        | Description                   |
| ------------------------------- | ----------------------------- |
| `webGl:renderer`                | Unmasked renderer string.     |
| `webGl:vendor`                  | Unmasked vendor string.       |
| `webGl:supportedExtensions`     | Extensions for WebGL 1.       |
| `webGl2:supportedExtensions`    | Extensions for WebGL 2.       |
| `webGl:contextAttributes`       | Context attributes (WebGL 1). |
| `webGl2:contextAttributes`      | Context attributes (WebGL 2). |
| `webGl:parameters`              | GL enum → value (WebGL 1).    |
| `webGl2:parameters`             | GL enum → value (WebGL 2).    |
| `webGl:shaderPrecisionFormats`  | Precision formats (WebGL 1).  |
| `webGl2:shaderPrecisionFormats` | Precision formats (WebGL 2).  |

### Geolocation & Intl

| Property                | Type     | Required Keys           |
| ----------------------- | -------- | ----------------------- |
| `geolocation:latitude`  | `double` | `geolocation:longitude` |
| `geolocation:longitude` | `double` | `geolocation:latitude`  |
| `geolocation:accuracy`  | `double` | —                       |
| `timezone`              | `str`    | —                       |
| `locale:language`       | `str`    | `locale:region`         |
| `locale:region`         | `str`    | `locale:language`       |
| `locale:script`         | `str`    | —                       |
| `locale:all`            | `str`    | —                       |

### HTTP Headers

| Property                  | Type  |
| ------------------------- | ----- |
| `headers.User-Agent`      | `str` |
| `headers.Accept-Language` | `str` |
| `headers.Accept-Encoding` | `str` |

### WebRTC IP

Protocol‑level IP spoofing.

| Property      | Type  |
| ------------- | ----- |
| `webrtc:ipv4` | `str` |
| `webrtc:ipv6` | `str` |

### Media & Audio

**Media devices**

| Property                | Type   | Default |
| ----------------------- | ------ | ------- |
| `mediaDevices:enabled`  | `bool` | `false` |
| `mediaDevices:micros`   | `uint` | `3`     |
| `mediaDevices:webcams`  | `uint` | `1`     |
| `mediaDevices:speakers` | `uint` | `1`     |

**AudioContext**

| Property                       | Type     |
| ------------------------------ | -------- |
| `AudioContext:sampleRate`      | `uint`   |
| `AudioContext:outputLatency`   | `double` |
| `AudioContext:maxChannelCount` | `uint`   |

### Voices

| Property                               | Type     | Description                                       |
| -------------------------------------- | -------- | ------------------------------------------------- |
| `voices`                               | `array`  | Array of maps, one per voice.                     |
| `voices:blockIfNotDefined`             | `bool`   | Block system voices if not spoofed.               |
| `voices:fakeCompletion`                | `bool`   | Return successful TTS completion with fake voice. |
| `voices:fakeCompletion:charsPerSecond` | `double` | Playback rate for fake TTS.                       |

### Addons (Runtime)

Addons are provided via the `addons` parameter (paths to **extracted** `.xpi` folders). uBlock Origin is included by default. Use `exclude_addons` to remove defaults.

### Miscellaneous

Keep `pdfViewerEnabled` enabled; some sites flag its absence.

| Property                  | Type     |
| ------------------------- | -------- |
| `pdfViewerEnabled`        | `bool`   |
| `battery:charging`        | `bool`   |
| `battery:chargingTime`    | `double` |
| `battery:dischargingTime` | `double` |
| `battery:level`           | `double` |

---

## Stealth Performance

Playwright’s internal Page Agent runs in an isolated world, preventing detection via JS inspection. Empirical test results (author‑reported):

| Test                         | Status | Notes                                              |
| ---------------------------- | ------ | -------------------------------------------------- |
| **CreepJS**                  | ✔️     | 71.5%. Spoofs all OS predictions.                  |
| **Rebrowser Bot Detector**   | ✔️     | All tests pass.                                    |
| **BrowserScan**              | ✔️     | 100%. Spoofs geolocation & locale proxy detection. |
| **reCaptcha Score**          | ✔️     |                                                    |
| ‣ nopecha.com                | ✔️     |                                                    |
| ‣ recaptcha-demo.appspot.com | ✔️     | 0.9                                                |
| ‣ berstend.github.io         | ✔️     | 0.9                                                |
| **DataDome**                 | ✔️     |                                                    |
| ‣ DataDome bot bounty        | ✔️     | All test sites pass.                               |
| ‣ hermes.com                 | ✔️     |                                                    |
| **Imperva**                  | ✔️     |                                                    |
| ‣ ticketmaster.es            | ✔️     |                                                    |
| **Cloudflare**               | ✔️     |                                                    |
| ‣ Turnstile                  | ✔️     |                                                    |
| ‣ Interstitial               | ✔️     |                                                    |
| **WebRTC IP Spoofing**       | ✔️     |                                                    |
| ‣ Browserleaks WebRTC        | ✔️     | Public IP spoofed.                                 |
| ‣ CreepJS WebRTC             | ✔️     | Host & STUN IP spoofed.                            |
| ‣ BrowserScan WebRTC         | ✔️     | Host & STUN IP spoofed.                            |
| **Font Fingerprinting**      | ✔️     |                                                    |
| ‣ Browserleaks Fonts         | ✔️     | Rotates metrics.                                   |
| ‣ CreepJS TextMetrics        | ✔️     | Rotates metrics.                                   |
| **Incolumitas**              | ✔️     | 0.8–1.0                                            |
| **SannySoft**                | ✔️     |                                                    |
| **Fingerprint.com**          | ✔️     |                                                    |
| **IpHey**                    | ✔️     |                                                    |
| **Bet365**                   | ✔️     |                                                    |

> **Note:** Camoufox does **not** inject Chromium fingerprints. Some WAFs (e.g., Cloudflare Interstitial) test SpiderMonkey‑specific behaviors that cannot be spoofed as Chromium.

---

## Development

### Build System

LibreWolf‑style `make` workflow: fetch Firefox source, apply patches (fingerprint masking, debloat, addons), and package portable builds for Linux, Windows, macOS.

### Building on CLI

> **Requirement:** Linux host. WSL is not supported.

1. Clone:

   ```bash
   git clone --depth 1 https://github.com/daijro/camoufox && cd camoufox
   ```
2. Create directories:

   ```bash
   make dir
   ```
3. Bootstrap (one‑time):

   ```bash
   make bootstrap
   ```
4. Build & package:

   ```bash
   python3 multibuild.py --target linux windows macos --arch x86_64 arm64 i686
   ```

### Building with Docker

1. Build image:

   ```bash
   docker build -t camoufox-builder .
   ```
2. Build for target OS/arch:

   ```bash
   docker run -v "$(pwd)/dist:/app/dist" camoufox-builder --target <os> --arch <arch>
   ```

### Development Tools

Interactive patch manager:

* `make edits` to open the developer UI.
* Create a patch: reset → modify `camoufox-*/` → `make build` → `make run` → write patch file.
* Edit a patch: select patch → modify → overwrite.

### Leak Debugging Workflow

Incrementally reintroduce features into a near‑vanilla Firefox until a test flags the browser.

**Commands**

* `(1) make ff-dbg` — Prepare vanilla Firefox with minimal patches.
* `(2) make build` — Build source.
* `(3) make run` — Run the built browser.
* `(4) make run args="--headless https://test.com"` — Run URL headlessly.
* `(5) make edits` — Open developer UI.
* `(6) make edit-cfg` — Edit `camoufox.cfg`.

---

## WebGL Research Snapshot

*As of Dec 05, 2024 (analytics coverage varies).*

| Vendor                  | Renderer (examples)                     | Probability | Sufficient |
| ----------------------- | --------------------------------------- | ----------- | ---------- |
| Google Inc. (NVIDIA)    | ANGLE (NVIDIA GeForce GTX 980 …)        | 29.0%       | ✅          |
| Google Inc. (Intel)     | ANGLE (Intel HD Graphics …)             | 24.1%       | ✅          |
| Google Inc. (Intel)     | ANGLE (Intel HD Graphics 400 …)         | 19.7%       | ✅          |
| Google Inc. (AMD)       | ANGLE (Radeon HD 3200 …)                | 10.6%       | ✅          |
| Google Inc. (AMD)       | ANGLE (Radeon R9 200 Series …)          | 6.6%        | ✅          |
| Google Inc. (NVIDIA)    | ANGLE (GeForce GTX 480 …)               | 1.7%        | ✅          |
| Google Inc. (Microsoft) | ANGLE (Microsoft Basic Render Driver …) | 1.6%        | ✅          |
| …                       | …                                       | …           | …          |
| Google Inc. (AMD)       | ANGLE (Radeon HD 5850 …)                | 0.5%        | ❌          |
| Intel Inc.              | Intel Iris OpenGL Engine                | 0.5%        | ❌          |

---

## Legal

Camoufox provides **no warranty** and **no liability**. Intended for ethical and research use only. Users are responsible for lawful, non‑fraudulent use.

---

## Appendix A: Fingerprintable Properties Tree

Comprehensive (non‑exhaustive) set of browser properties targeted for spoofing:

* browser
* navigator.appName
* navigator.product
* navigator.productSub
* navigator.appCodeName
* platformInfo
* os
* navigator.userAgent
* gpu
* webgl
* webgl2
* gpuInfo.adapterInfo
* battery
* navigator.deviceMemory
* navigator.hardwareConcurrency
* drm
* mimeTypes
* permissions.microphone
* devices.audioinput
* permissions.camera
* devices.videoinput
* permissions.speaker
* devices.audiooutput
* permissions.bluetooth
* permissions.midi
* plugins.plugins
* plugins.mimeTypes
* rtcCapabilities.video
* rtcCapabilities.audio
* audio.signals
* audio.values
* location.country
* location.region
* intl
* languages
* navigator.language
* navigator.languages
* headers.accept-language
* voices
* matchMedia.mediaCss.pointer
* matchMedia.mediaCss.any-pointer
* matchMedia.mediaCss.hover
* matchMedia.mediaCss.any-hover
* screen
* matchMedia.screenQuery.width
* matchMedia.screenQuery.height
* matchMedia.mediaCss.device-aspect-ratio
* matchMedia.mediaCss.device-screen
* matchMedia.mediaCss.orientation
* window
* windowComponents
* systemStyles.fonts
* allFonts
* document
* documentPolicyFeatures
* cast
* client
* navigator.platform
* navigator.appVersion
* navigator.doNotTrack
* navigator.oscpu
* navigator.maxTouchPoints
* memory.jsHeapSizeLimit
* matchMedia.mediaCss.color-gamut
* system_styles.colors
* matchMedia.mediaCss.display-mode
* matchMedia.mediaCss.forced-colors
* matchMedia.mediaCss.inverted-colors
* matchMedia.mediaCss.monochrome
* matchMedia.mediaCss.prefers-color-scheme
* matchMedia.mediaCss.prefers-reduced-motion
* permissions.accelerometer
* permissions.accessibility-events
* permissions.background-fetch
* permissions.background-sync
* permissions.periodic-background-sync
* permissions.clipboard
* permissions.clipboard-read
* permissions.clipboard-write
* permissions.device-info
* permissions.display-capture
* permissions.font-access
* permissions.geolocation
* permissions.gyroscope
* permissions.idle-detection
* permissions.magnetometer
* permissions.nfc
* permissions.notifications
* permissions.payment-handler
* permissions.persistent-storage
* permissions.push
* permissions.screen-wake-lock
* permissions.storage-access
* permissions.system-wake-lock
* permissions.window-placement
* network.headers_fp
* network.tls.scrapfly_fp
* network.http2.fingerprint
* network.http2.http2_frames
* headers.accept-encoding
* headers.accept
* headers.priority
* headers.sec-ch-ua
* headers.sec-ch-ua-mobile
* headers.sec-ch-ua-platform
* headers.sec-fetch-dest
* headers.sec-fetch-mode
* headers.sec-fetch-site
* headers.sec-gpc
* headers.user-agent

---

