# **Camoufox Documentation**



Camoufox is an open‑source, Firefox‑based anti-detect browser focused on robust, low‑level **fingerprint injection and rotation** without JavaScript (JS) injection. It blends device, network, and rendering characteristics into real‑world distributions to resist bot detection. A Python library wraps the Playwright API to automate fingerprint generation, injection, and environment configuration.

## **Table of Contents**

*   [1. Quick Start](#1-quick-start)
*   [2. Installation & CLI](#2-installation--cli)
    *   [Installation Steps](#installation-steps)
    *   [OS Dependencies](#os-dependencies)
    *   [CLI Reference](#cli-reference)
*   [3. Python API & Parameters](#3-python-api--parameters)
    *   [Device Rotation](#device-rotation)
    *   [Configuration](#configuration)
    *   [Location & Language](#location--language)
    *   [Toggles](#toggles)
    *   [Overriding Config Values](#overriding-config-values)
*   [4. Practical Guides](#4-practical-guides)
    *   [Proxies & GeoIP](#proxies--geoip)
    *   [Execution Contexts (Isolated vs. Main World)](#execution-contexts-isolated-vs-main-world)
    *   [Headless vs. Virtual Display (Linux)](#headless-vs-virtual-display-linux)
    *   [Persistent Profiles](#persistent-profiles)
*   [5. Troubleshooting](#5-troubleshooting)
*   [6. Architecture & Fingerprinting Concepts](#6-architecture--fingerprinting-concepts)
    *   [Core Architecture](#core-architecture)
    *   [Key Fingerprinting Areas](#key-fingerprinting-areas)
*   [7. Remote Server (Playwright WebSocket)](#7-remote-server-playwright-websocket)
*   [8. Stealth Performance](#8-stealth-performance)
*   [9. Advanced: Fingerprint Injection Reference](#9-advanced-fingerprint-injection-reference)
    *   [Navigator](#navigator)
    *   [Screen & Window](#screen--window)
    *   [Canvas](#canvas)
    *   [WebGL](#webgl)
    *   [WebRTC IP](#webrtc-ip)
    *   [And many more...](#and-many-more)
*   [10. Development](#10-development)

---

## **1. Quick Start**

### Install the Python package

```bash
pip install -U "camoufox[geoip]"
```

### Fetch the browser binary

```bash
camoufox fetch
```

### Sync Usage

```python
from camoufox.sync_api import Camoufox

with Camoufox() as browser:
    page = browser.new_page()
    page.goto("https://www.browserscan.net")
```

### Async Usage

```python
import asyncio
from camoufox.async_api import Camoufox

async def main():
    async with Camoufox() as browser:
        page = await browser.new_page()
        await page.goto("https://www.browserscan.net")

asyncio.run(main())
```

---

## **2. Installation & CLI**

### Installation Steps

1.  **Install Python Package:** `pip install -U "camoufox[geoip]"`
2.  **Download Browser:** `camoufox fetch`

### OS Dependencies

| OS      | Dependencies                                                |
| :------ | :---------------------------------------------------------- |
| Linux   | `libgtk-3`, `libnss3`, `xvfb` (if using `headless="virtual"`) |
| macOS   | Xcode Command Line Tools                                    |
| Windows | Visual C++ runtime (if missing)                             |

### CLI Reference

The CLI helps manage the browser binary and run the remote server.

| Command   | Description                          |
| :-------- | :----------------------------------- |
| `fetch`   | Download the latest Camoufox build.  |
| `remove`  | Delete all downloaded files.         |
| `path`    | Print the Camoufox executable path.  |
| `server`  | Start a Playwright WebSocket server. |
| `test`    | Launch the Playwright inspector.     |
| `version` | Show the current Camoufox version.   |

---

## **3. Python API & Parameters**

All standard Playwright Firefox launch options are supported. The parameters below are **Camoufox‑specific**.

### Device Rotation

| Parameter      | Type                        | Description                                                                 |
| :------------- | :-------------------------- | :-------------------------------------------------------------------------- |
| `os`           | `Optional[ListOrString]`    | Target OS: `"windows"`, `"macos"`, `"linux"`, or a list for random choice.  |
| `screen`       | `Optional[Screen]`          | Constrain screen dimensions (`browserforge.fingerprints.Screen`).           |
| `webgl_config` | `Optional[Tuple[str, str]]` | Fixed WebGL `(vendor, renderer)` pair. Avoids random, detectable values.    |
| `fonts`        | `Optional[List[str]]`       | Extra font families to load in addition to OS defaults.                     |

### Configuration

| Parameter            | Type                                        | Description                                                                      |
| :------------------- | :------------------------------------------ | :------------------------------------------------------------------------------- |
| `humanize`           | `Optional[Union[bool, float]]`              | Human‑like cursor movement. `True` enables default, or pass max duration (sec).  |
| `headless`           | `Optional[Union[bool, Literal['virtual']]]` | Headless mode. `'virtual'` uses a virtual display (Xvfb) on Linux for realism. |
| `window`             | `Optional[Tuple[int, int]]`                 | Browser window size `(width, height)` in pixels.                                 |
| `addons`             | `Optional[List[str]]`                       | Paths to **extracted** Firefox addons to load.                                   |
| `exclude_addons`     | `Optional[List[DefaultAddons]]`             | Exclude bundled addons like uBlock Origin.                                       |
| `main_world_eval`    | `Optional[bool]`                            | Allow `mw:` prefix to run scripts in the page's main world (use with caution).   |
| `enable_cache`       | `Optional[bool]`                            | Enable page/request caching.                                                     |
| `persistent_context` | `Optional[bool]`                            | Use a persistent context. Requires `user_data_dir` in launch options.            |

### Location & Language

| Parameter | Type                              | Description                                                                      |
| :-------- | :-------------------------------- | :------------------------------------------------------------------------------- |
| `geoip`   | `Optional[Union[str, bool]]`      | Auto‑set lat/lon, timezone, country, and locale from an IP. Pass IP or `True`.   |
| `locale`  | `Optional[Union[str, List[str]]]` | One or more locales (e.g., `"en-US"`). Overrides GeoIP locale if both are set.   |

### Toggles

| Parameter      | Type             | Description                                                  |
| :------------- | :--------------- | :----------------------------------------------------------- |
| `block_images` | `Optional[bool]` | Block all image requests.                                    |
| `block_webrtc` | `Optional[bool]` | Disable WebRTC entirely.                                     |
| `block_webgl`  | `Optional[bool]` | Disable WebGL entirely (can be a detection vector).          |
| `disable_coop` | `Optional[bool]` | Disable Cross‑Origin‑Opener‑Policy (useful for iframe interaction). |

### Overriding Config Values

For advanced use, you can override any individual fingerprint property. This is generally **not recommended** as Camoufox creates a coherent profile for you.

```python
with Camoufox(config={
    'webrtc:ipv4': '123.45.67.89',
    'webrtc:ipv6': 'e791:d37a:88f6:48d1:2cad:2667:4582:1d6d',
}) as browser:
    page = browser.new_page()
```

---

## **4. Practical Guides**

### Proxies & GeoIP

To avoid detection, your browser's fingerprint (timezone, language, location) must match your proxy's exit IP. Camoufox automates this.

```python
with Camoufox(
    geoip=True,  # Automatically detects location from proxy
    proxy={
        'server': 'http://proxy.example.com:8080',
        'username': 'username',
        'password': 'password'
    }
) as browser:
    page = browser.new_page()
    # Timezone, locale, and WebRTC IP are now aligned with the proxy
    page.goto("https://www.browserscan.net")
```

### Execution Contexts (Isolated vs. Main World)

*   **Isolated World (Default):** Your scripts run in a sandbox invisible to the page. It's safe for reading the DOM but cannot modify it. All `page.evaluate()` calls use this by default.
*   **Main World:** Your script runs in the page's context. This allows you to modify the DOM (e.g., click a button), but it is **detectable** by the website.

Enable `main_world_eval=True` and prefix your script with `mw:` to use it.
**Warning:** Only use this when absolutely necessary.

```python
with Camoufox(main_world_eval=True) as browser:
    page = browser.new_page()
    page.goto("https://example.com")
    page.evaluate("mw:document.querySelector('h1').remove()") # This is detectable
```

### Headless vs. Virtual Display (Linux)

*   `headless=True`: Standard headless mode. Some advanced bot detectors can identify this.
*   `headless="virtual"`: **Recommended for realism.** Uses Xvfb to run a full graphical browser instance in a virtual display, making it appear headful.

To use virtual display, install Xvfb:
```bash
# Debian/Ubuntu
sudo apt-get install xvfb
# Arch
sudo pacman -S xorg-server-xvfb
```

### Persistent Profiles

Use a persistent profile for tasks requiring login sessions or multi-step flows. This saves cookies, local storage, etc., between runs.

```python
# Note: You must provide a path for the profile data
with Camoufox(persistent_context=True, user_data_dir="./my_profile") as browser:
    page = browser.new_page()
```

---

## **5. Troubleshooting**

| Symptom                              | Common Cause                               | Solution                                                                  |
| :----------------------------------- | :----------------------------------------- | :------------------------------------------------------------------------ |
| Turnstile/checkbox cannot be clicked | Cross-Origin-Opener-Policy (COOP) isolation | Set `disable_coop=True` or use main-world execution (`mw:`) for the click. |
| Headless mode increases detections   | Standard headless is detectable            | Use `headless="virtual"` on Linux for a more realistic headful simulation. |
| Wrong timezone/locale on checker sites | Mismatch between browser and proxy IP      | Use `geoip=True` when using a proxy.                                      |
| WebGL is flagged as suspicious       | Invalid renderer/vendor or disabled        | Use a valid `webgl_config` pair or leave it enabled with default values.  |
| Add-ons are not saving their state     | Non-persistent context                     | Set `persistent_context=True` and provide a `user_data_dir`.              |

---

## **6. Architecture & Fingerprinting Concepts**

### Core Architecture

*   **Low-Level Injection:** Camoufox modifies device, system, and rendering data directly in the Firefox C++ source code. This avoids JS-observable DOM manipulation used by browser extensions, which is a common detection vector.
*   **Crowdblending:** To avoid standing out, Camoufox uses **BrowserForge** to generate fingerprints (screen, OS, hardware) that match real-world user distributions.
*   **Playwright Stealth:** Playwright's automation scripts run in a sandboxed, isolated world, making their presence non-observable via standard JavaScript inspection.
*   **Why Firefox:** Compared to Chromium, Firefox has stronger prior art for fingerprint resistance, a more transparent codebase, and uses an automation protocol (Juggler) less targeted by bot detectors than the Chrome DevTools Protocol (CDP).

### Key Fingerprinting Areas

*   **Navigator & Headers:** `userAgent`, `platform`, `languages`, and `Accept-Language` headers are all aligned to a consistent OS and locale.
*   **Fonts:** System-correct fonts for Windows, macOS, and Linux are bundled and loaded based on the user agent. Font metrics are slightly perturbed to resist measurement-based fingerprinting.
*   **Screen & Window:** Dimensions for the screen, window, and viewport are generated to be consistent with the spoofed device class.
*   **Canvas:** A closed-source patch to the Skia rendering engine rotates canvas fingerprints at a sub-pixel level, making them unique but consistent per session.
*   **WebGL:** A complete set of WebGL parameters, extensions, and shader precision formats are spoofed using valid `(vendor, renderer)` pairs from real devices.
*   **WebRTC:** The public and local IP addresses exposed via WebRTC are spoofed at the protocol level to match the proxy IP, or WebRTC is disabled entirely.

---

## **7. Remote Server (Playwright WebSocket)**

Run Camoufox as a remote server and connect to it from any Playwright client.

**Start the server via CLI:**
```bash
python -m camoufox server --port 4444
```

**Connect from a Playwright script:**
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    # Connect to the running Camoufox server
    browser = p.firefox.connect_over_cdp("http://localhost:4444")
    page = browser.new_page()
    page.goto("https://example.com")
    browser.close()
```

---

## **8. Stealth Performance**

Empirical test results against major bot detection and fingerprinting services (author-reported):

| Test                         | Status | Notes                                                        |
| :--------------------------- | :----- | :----------------------------------------------------------- |
| **CreepJS**                  | ✔️     | 71.5% score. Spoofs all OS, browser, and hardware predictions. |
| **BrowserScan**              | ✔️     | 100% score. Spoofs geolocation & locale proxy detection.   |
| **DataDome**                 | ✔️     | Passes on all public test sites, including the bot bounty.     |
| **Cloudflare**               | ✔️     | Passes Turnstile and standard interstitial challenges.       |
| **Imperva/Incapsula**        | ✔️     | Passes on major sites like Ticketmaster.                       |
| **reCaptcha v3 Score**       | ✔️     | Achieves 0.9 score on demo sites.                            |
| **WebRTC IP Spoofing**       | ✔️     | Public, host, and STUN IPs are correctly spoofed on checker sites. |
| **Font Fingerprinting**      | ✔️     | Rotates font metrics on Browserleaks and CreepJS.            |
| **Fingerprint.com**          | ✔️     | Passes detection.                                            |

---

## **9. Advanced: Fingerprint Injection Reference**

This is a partial reference of properties spoofed at the C++ level. All fields not explicitly set are auto-populated by BrowserForge. For a full list of overridable keys, refer to the source.

<details>
<summary><strong>Click to expand full reference table</strong></summary>

| Category      | Property                         | Type     |
| :------------ | :------------------------------- | :------- |
| **Navigator** | `navigator.userAgent`            | `str`    |
|               | `navigator.platform`             | `str`    |
|               | `navigator.hardwareConcurrency`  | `uint`   |
|               | `navigator.languages`            | `array`  |
|               | `navigator.webdriver`            | `bool`   |
| **Screen**    | `screen.width` / `height`        | `uint`   |
|               | `screen.availWidth` / `availHeight` | `uint`   |
|               | `screen.colorDepth` / `pixelDepth`  | `uint`   |
| **Window**    | `window.innerWidth` / `innerHeight` | `uint`   |
|               | `window.outerWidth` / `outerHeight` | `uint`   |
|               | `window.devicePixelRatio`        | `double` |
| **Canvas**    | `canvas:aaOffset`                | `int`    |
|               | `canvas:aaCapOffset`             | `bool`   |
| **WebGL**     | `webGl:vendor`                   | `str`    |
|               | `webGl:renderer`                 | `str`    |
|               | `webGl:supportedExtensions`      | `array`  |
|               | `webGl:parameters`               | `map`    |
| **WebRTC IP** | `webrtc:ipv4`                    | `str`    |
|               | `webrtc:ipv6`                    | `str`    |
| **Geo & Intl**| `geolocation:latitude`           | `double` |
|               | `geolocation:longitude`          | `double` |
|               | `timezone`                       | `str`    |
|               | `locale:language`                | `str`    |
| **Audio**     | `AudioContext:sampleRate`        | `uint`   |
|               | `mediaDevices:micros`            | `uint`   |
|               | `mediaDevices:webcams`           | `uint`   |
| **Battery**   | `battery:charging`               | `bool`   |
|               | `battery:level`                  | `double` |

</details>

---

## **10. Development**

For those who wish to build Camoufox from source. A Linux host is required.

1.  **Clone the repository:**
    ```bash
    git clone --depth 1 https://github.com/daijro/camoufox && cd camoufox
    ```
2.  **Create directories:**
    ```bash
    make dir
    ```
3.  **Bootstrap dependencies (one-time):**
    ```bash
    make bootstrap
    ```
4.  **Build for target platforms:**
    ```bash
    python3 multibuild.py --target linux windows macos --arch x86_64
    ```
