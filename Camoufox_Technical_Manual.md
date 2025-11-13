# Camoufox Technical Manual

## Overview

Camoufox is an open-source anti-detect browser built on Firefox that provides C++ level fingerprint injection. It bypasses anti-bot systems by modifying device characteristics at the implementation level rather than through JavaScript injection.

**Status**: Active development. Not production-ready.

---

## Table of Contents

- [Core Capabilities](#core-capabilities)
- [Installation](#installation)
- [Python API](#python-api)
- [Fingerprint Configuration](#fingerprint-configuration)
- [Development](#development)

---

## Core Capabilities

### Architecture

- **C++ level spoofing**: All fingerprint modifications occur at implementation level, invisible to JavaScript inspection
- **Sandboxed Playwright**: Page Agent JavaScript isolated from main world, preventing detection
- **BrowserForge integration**: Automatic fingerprint generation matching real-world device distributions
- **Protocol-level WebRTC**: IP spoofing at network protocol level

### Stealth Features

- Navigator property spoofing (all device/OS/browser properties)
- Screen, window, viewport dimensions
- Canvas anti-fingerprinting via modified Skia subpixel rendering (closed source)
- WebGL parameters, extensions, context attributes, shader precision formats
- Font metrics randomization (0-0.1px letter spacing offset)
- Human-like cursor movement
- Geolocation, timezone, locale calculation from proxy IP
- AudioContext spoofing
- Media device enumeration control

### Performance

Passes major WAF tests:
- CreepJS: 71.5%
- BrowserScan: 100%
- reCaptcha v3: 0.9 score
- DataDome, Imperva, Cloudflare: Pass
- Incolumitas: 0.8-1.0

**Limitation**: Cannot spoof Chromium fingerprints. SpiderMonkey engine behavior is detectable.

---

## Installation

```bash
# Install package with GeoIP support
pip install -U camoufox[geoip]

# Download browser
camoufox fetch

# Verify installation
camoufox version
```

### CLI Commands

```bash
camoufox fetch    # Download browser
camoufox remove   # Uninstall
camoufox path     # Show executable path
camoufox server   # Launch Playwright server
camoufox test     # Open inspector
```

---

## Python API

### Basic Usage

**Synchronous**:
```python
from camoufox.sync_api import Camoufox

with Camoufox() as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

**Asynchronous**:
```python
from camoufox.async_api import Camoufox

async with Camoufox() as browser:
    page = await browser.new_page()
    await page.goto("https://example.com")
```

### Key Parameters

#### Device Rotation
```python
Camoufox(
    os='windows',  # 'windows', 'macos', 'linux', or list
    screen=Screen(max_width=1920, max_height=1080),
    fonts=['Arial', 'Helvetica'],  # Additional fonts
    webgl_config=('NVIDIA Corporation', 'GeForce GTX 1080')
)
```

#### Configuration
```python
Camoufox(
    humanize=True,  # or float for max duration (seconds)
    headless='virtual',  # False, True, or 'virtual' (Linux Xvfb)
    addons=['/path/to/addon'],  # Extracted addon directories
    window=(1920, 1080),  # Window size
    main_world_eval=True,  # Enable mw: prefix for DOM modification
    enable_cache=False,
    persistent_context=False  # Requires user_data_dir
)
```

#### Location & Proxy
```python
Camoufox(
    geoip=True,  # Auto-detect IP, or pass IP string
    locale='en-US',  # or ['en-US', 'en-GB']
    proxy={
        'server': 'http://proxy.com:8080',
        'username': 'user',
        'password': 'pass'
    }
)
```

**GeoIP calculates**: longitude, latitude, timezone, country, locale, WebRTC IP, browser language distribution

#### Toggles
```python
Camoufox(
    block_images=True,
    block_webrtc=True,
    block_webgl=False,
    disable_coop=True
)
```

### Main World Execution

**Default (Isolated)**: JavaScript invisible to page, cannot modify DOM.

**Main World**: Enable with `main_world_eval=True`, prefix scripts with `mw:`
```python
page.evaluate("mw:document.querySelector('h1').remove()")
```
**Warning**: Main world code is detectable.

### Remote Server

```bash
# Launch
python -m camoufox server
```

```python
# Or via script
from camoufox.server import launch_server
launch_server(port=1234, ws_path='hello')

# Connect
from playwright.sync_api import sync_playwright
with sync_playwright() as p:
    browser = p.firefox.connect('ws://localhost:1234/hello')
```

### Virtual Display (Linux)

```bash
# Install
sudo apt-get install xvfb  # Debian
sudo pacman -S xorg-server-xvfb  # Arch
```

```python
Camoufox(headless="virtual")
```

### Custom Config Override

```python
Camoufox(
    config={
        'webrtc:ipv4': '123.45.67.89',
        'webrtc:ipv6': 'e791:d37a:88f6:48d1:2cad:2667:4582:1d6d',
        'canvas:aaOffset': 5
    }
)
```

---

## Fingerprint Configuration

All properties populated automatically via BrowserForge unless explicitly set.

### Navigator

| Property | Type |
|----------|------|
| `navigator.userAgent` | `str` |
| `navigator.platform` | `str` |
| `navigator.hardwareConcurrency` | `uint` |
| `navigator.maxTouchPoints` | `uint` |
| `navigator.language` | `str` |
| `navigator.languages` | `array` |
| `navigator.doNotTrack` | `str` |
| `navigator.cookieEnabled` | `bool` |
| `navigator.globalPrivacyControl` | `bool` |

### Screen

| Property | Type |
|----------|------|
| `screen.width` | `uint` |
| `screen.height` | `uint` |
| `screen.availWidth` | `uint` |
| `screen.availHeight` | `uint` |
| `screen.colorDepth` | `uint` |
| `screen.pixelDepth` | `uint` |

### Window

| Property | Type |
|----------|------|
| `window.innerWidth` | `uint` |
| `window.innerHeight` | `uint` |
| `window.outerWidth` | `uint` |
| `window.outerHeight` | `uint` |
| `window.screenX` | `int` |
| `window.screenY` | `int` |
| `window.devicePixelRatio` | `double` |

### Canvas

| Property | Type | Description |
|----------|------|-------------|
| `canvas:aaOffset` | `int` | Pixel alpha offset for anti-aliasing |
| `canvas:aaCapOffset` | `bool` | Clamp offset to 0-255 range |

### WebGL

**Warning**: Use realistic vendor/renderer combinations. WAFs hash and validate against device datasets.

| Property | Description |
|----------|-------------|
| `webGl:renderer` | Unmasked renderer name |
| `webGl:vendor` | Unmasked vendor name |
| `webGl:supportedExtensions` | Array of extensions |
| `webGl:contextAttributes` | Dict of context attributes |
| `webGl:parameters` | Dict of parameters (GL enum keys) |
| `webGl:shaderPrecisionFormats` | Dict of precision formats |
| `webGl2:*` | WebGL2 equivalents |

### Geolocation

| Property | Type | Dependencies |
|----------|------|--------------|
| `geolocation:latitude` | `double` | Requires longitude |
| `geolocation:longitude` | `double` | Requires latitude |
| `geolocation:accuracy` | `double` | — |
| `timezone` | `str` | — |

### Locale

| Property | Type | Dependencies |
|----------|------|--------------|
| `locale:language` | `str` | Requires region |
| `locale:region` | `str` | Requires language |
| `locale:script` | `str` | — |

### Headers

| Property | Type |
|----------|------|
| `headers.User-Agent` | `str` |
| `headers.Accept-Language` | `str` |
| `headers.Accept-Encoding` | `str` |

### WebRTC

| Property | Type |
|----------|------|
| `webrtc:ipv4` | `str` |
| `webrtc:ipv6` | `str` |

### Media Devices

| Property | Type | Default |
|----------|------|---------|
| `mediaDevices:enabled` | `bool` | `false` |
| `mediaDevices:micros` | `uint` | `3` |
| `mediaDevices:webcams` | `uint` | `1` |
| `mediaDevices:speakers` | `uint` | `1` |

### AudioContext

| Property | Type |
|----------|------|
| `AudioContext:sampleRate` | `uint` |
| `AudioContext:outputLatency` | `double` |
| `AudioContext:maxChannelCount` | `uint` |

### Fonts

| Property | Type | Description |
|----------|------|-------------|
| `fonts` | `array` | Font family names |
| `fonts:spacing_seed` | `uint` | Metric randomization seed |

**Automatic**: Bundled Windows 11, macOS Sonoma, Linux fonts loaded based on User-Agent.

### Voices

| Property | Type | Description |
|----------|------|-------------|
| `voices` | `array` | Voice configuration objects |
| `voices:blockIfNotDefined` | `bool` | Block system voices |
| `voices:fakeCompletion` | `bool` | Return success for fake voices |
| `voices:fakeCompletion:charsPerSecond` | `double` | Fake voice playback rate |

### Cursor

| Property | Type | Description |
|----------|------|-------------|
| `humanize` | `bool` | Enable cursor movement |
| `humanize:maxTime` | `double` | Max duration (default: 1.5s) |
| `humanize:minTime` | `double` | Min duration |
| `showcursor` | `bool` | Show cursor highlighter |

### Battery

| Property | Type |
|----------|------|
| `battery:charging` | `bool` |
| `battery:chargingTime` | `double` |
| `battery:dischargingTime` | `double` |
| `battery:level` | `double` |

### Miscellaneous

| Property | Type | Note |
|----------|------|------|
| `pdfViewerEnabled` | `bool` | Recommended: keep enabled |

---

## Development

### Build Requirements

- **Platform**: Linux only (no WSL)
- **System**: LibreWolf-based build system using `make`

### Build Process

```bash
# Clone
git clone --depth 1 https://github.com/daijro/camoufox && cd camoufox

# Setup
make dir
make bootstrap  # First time only

# Build
python3 multibuild.py --target linux windows macos --arch x86_64 arm64 i686
```

### Docker Build

```bash
# Create image
docker build -t camoufox-builder .

# Build
docker run -v "$(pwd)/dist:/app/dist" camoufox-builder --target <os> --arch <arch>
```

### Development Commands

| Command | Purpose |
|---------|---------|
| `make ff-dbg` | Setup vanilla Firefox with minimal patches |
| `make build` | Build source |
| `make run` | Run built browser |
| `make run args="--headless https://test.com"` | Run URL headless |
| `make edits` | Open patch management UI |
| `make edit-cfg` | Edit camoufox.cfg |

### Leak Debugging Process

1. `make ff-dbg` - Start with vanilla Firefox
2. Test on target detection site
3. Incrementally enable Camoufox patches
4. `make build` and `make run` after each change
5. Identify leak-causing patch
6. Refine patch to eliminate detection

---

## WebGL Vendor/Renderer Data

Sufficient analytics for accurate simulation (as of Dec 2024):

| Vendor | Renderer Pattern | Probability |
|--------|------------------|-------------|
| Google Inc. (NVIDIA) | ANGLE (NVIDIA GeForce GTX 980...) | 29.0% |
| Google Inc. (Intel) | ANGLE (Intel HD Graphics...) | 24.1% |
| Google Inc. (Intel) | ANGLE (Intel HD Graphics 400...) | 19.7% |
| Google Inc. (AMD) | ANGLE (AMD Radeon HD 3200...) | 10.6% |
| Google Inc. (AMD) | ANGLE (AMD Radeon R9 200...) | 6.6% |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA GeForce GTX 480...) | 1.7% |
| Google Inc. (Microsoft) | ANGLE (Microsoft Basic Render Driver...) | 1.6% |

---

## Disclaimer

**NO LIABILITY OR WARRANTY**. Intended for ethical research only. Users responsible for legal compliance. Developers not liable for damages or misuse.

---
