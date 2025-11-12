# Camoufox - Documentation

This document contains all documentation extracted from camoufox.com

**Last Updated:** November 9, 2025

**Source:** https://github.com/daijro/camoufox

**Documentation:** https://camoufox.com/

---

## Table of Contents

1. [Introduction](#introduction)
2. [Features](#features)
3. [Stealth Performance](#stealth-performance)
4. [Python Interface](#python-interface)
5. [Python Usage](#python-usage)
6. [Python Installation](#python-installation)
7. [Python Configuration](#python-configuration)
8. [Python GeoIP & Proxy Support](#python-geoip--proxy-support)
9. [Python Main World Execution](#python-main-world-execution)
10. [Python Remote Server](#python-remote-server)
11. [Python Virtual Display](#python-virtual-display)
12. [Python BrowserForge Integration](#python-browserforge-integration)
13. [Fingerprint Injection](#fingerprint-injection)
14. [Fingerprint Details](#fingerprint-details)
15. [Development](#development)
16. [Legal](#legal)
17. [Donate](#donate)
18. [Test Pages](#test-pages)
19. [WebGL Research](#webgl-research)

---

## Introduction

Source: https://camoufox.com

### An open source anti-detect browser with robust fingerprint injection. 🦊

[![daijro%2Fcamoufox | Trendshift](https://trendshift.io/api/badge/repositories/12224)](https://trendshift.io/repositories/12224)

**Warning:** Camoufox is in active development! Releases are available, but may not be suitable for production use.

### Features

- **Invisible to all anti-bot systems** 🎭
  - Camoufox performs better than most commercial anti-bot browsers.
- **Fingerprint injection & rotation (without JS injection!)**
  - All navigator properties (device, OS, hardware, browser, etc.) ✅
  - Screen size, resolution, window, & viewport properties ✅
  - Geolocation, timezone, locale, & Intl spoofing ✅
  - WebRTC IP spoofing at the protocol level ✅
  - Voices, speech playback rate, etc. ✅
  - And much, much more!
- **Anti Graphical fingerprinting**
  - Canvas anti-fingerprinting ✅
  - WebGL parameters, supported extensions, context attributes, & shader precision formats ✅
  - Font spoofing & anti-fingerprinting ✅
- **Quality of life features**
  - Human-like mouse movement 🖱️
  - Blocks & circumvents ads 🛡️
  - No CSS animations 💨
- Debloated & optimized for memory efficiency ⚡
- [PyPi package](https://pypi.org/project/camoufox/) for updates & auto fingerprint injection 📦
- Stays up to date with the latest Firefox version 🕓

### Design and Implementation

The goal of Camoufox is to provide a robust, undetectable anti-fingerprinting solution that blends in with regular user traffic.

#### 1. Fingerprinting Protection

Privacy-focused browsers and add-ons often become counterproductive by making users more identifiable through detectable modifications or JavaScript inspection. This, ironically, makes the user stand out by creating unique fingerprints, making them more susceptible to be tracked or flagged by anti-bot systems.

Camoufox solves this by operating at a lower level, using fingerprint rotation and crowdblending techniques by modifying device information in the C++ implementation instead of injecting JavaScript, which leaves a trail. Built on Firefox and research from the Tor project, LibreWolf, and Arkenfox, Camoufox aims to avoid common user identification methods to provide a robust, undetectable anti-fingerprinting.

#### 2. Crowdblending

A design goal of Camoufox is to blend in with real world traffic. It must behave like a normal user and avoid all statistical fingerprinting, meaning there should _not_ be thousands of requests using the same device information on a large scale.

To achieve this, Camoufox uses [BrowserForge](https://github.com/daijro/browserforge) to rotate device information such as the screen, OS, and hardware to mimic the statistical distribution of device characteristics in real-world traffic. Additionally, Camoufox uses a natural mouse movement algorithm to behave like a normal user.

#### 3. Stealth

Finally, Camoufox should avoid all bot detection, of any kind, by any means. It should not inject any JavaScript into the page or manipulate the DOM in any way.

In Camoufox, all of Playwright's internal Page Agent Javascript is sandboxed and isolated from the main world. This makes it **impossible** for a page to detect the presence of Playwright/Juggler. In addition, data is intercepted at the C++ implementation level, making the changes undetectable through JavaScript inspection.

### Why Firefox and not Chromium?

Camoufox is built on top of Firefox instead of Chromium for these main reasons:

1. Chrome is bundled with certain features that Chromium does not have. Anti-bot providers can detect if you are using Chromium rather than Chrome. Since Chrome is closed source, patching Chrome is significantly more difficult.
2. CDP is more widely used and known, so it's a more common target for bot detection
3. Juggler operates on a lower level than CDP, making it less prone to JS leaks.
4. Firefox is more ideal for fingerprint rotation. More research has been made on Firefox for fingerprinting resistance than on Chromium.

### Is Camoufox open source?

**Almost all** of Camoufox's code is publicly available on GitHub. However, certain features like the [Canvas fingerprint rotation](https://camoufox.com/fingerprint/canvas/) patch are closed source to prevent anti-bot providers from reverse engineering Camoufox.

If you'd like to build Camoufox without the closed source features, please refer to the [build guide](https://camoufox.com/development/buildcli/).

### What's planned?

- Continue research on potential leaks
- AI powered selector location
- Remote hosting Camoufox as a Playwright server

### Important Notice

Hello! In March 2025, I had a medical emergency and have been hospitalized since. I haven't been able to continue my work for some time.

I was previously updating Camoufox daily, and plan to return to that schedule as soon as I'm able. My goal is to have Camoufox back to its usual performance and be fully caught up by the end of 2025.

Thank you for your patience and understanding.

---

## Features

Source: https://camoufox.com/features

Below is a list of patches and features implemented in Camoufox.

### Fingerprint spoofing

- Navigator properties spoofing (device, browser, locale, etc.)
- Support for emulating screen size, resolution, etc.
- Spoof WebGL parameters, supported extensions, context attributes, and shader precision formats.
- Spoof the CPU Canvas fingerprint by manipulating the anti-aliasing logic (closed source)
- Spoof inner and outer window viewport sizes
- Spoof AudioContext sample rate, output latency, and max channel count
- Spoof device voices & playback rates
- Spoof the amount of microphones, webcams, and speakers available.
- Network headers (Accept-Languages and User-Agent) are spoofed to match the navigator properties
- WebRTC IP spoofing at the protocol level
- Geolocation, timezone, and locale spoofing
- Battery API spoofing
- etc.

### Stealth patches

- Avoids main world execution leaks. All page agent javascript is sandboxed
- Avoids frame execution context leaks
- Fixes `navigator.webdriver` detection
- Fixes Firefox headless detection via pointer type ([#26](https://github.com/daijro/camoufox/issues/26))
- Removed potentially leaking anti-zoom/meta viewport handling patches
- Uses non-default screen & window sizes
- Re-enable fission content isolations
- Re-enable PDF.js
- Other leaking config properties changed
- Human-like cursor movement

### Anti font fingerprinting

- Automatically uses the correct system fonts for your User Agent
- Bundled with Windows, Mac, and Linux system fonts
- Prevents font metrics fingerprinting by randomly offsetting letter spacing

### Playwright support

- Custom implementation of Playwright for the latest Firefox
- Various config patches to evade bot detection

### Debloat/Optimizations

- Stripped out/disabled _many, many_ Mozilla services. Runs faster than the original Mozilla Firefox, and uses less memory (200mb)
- Patches from LibreWolf & Ghostery to help remove telemetry & bloat
- Debloat config from PeskyFox, LibreWolf, and others
- Speed & network optimizations from FastFox
- Removed all CSS animations
- Minimalistic theming
- etc.

### Addons

- Load Firefox addons without a debug server by passing a list of paths to the `addons` property
- Added uBlock Origin with custom privacy filters
- Addons are not allowed to open tabs
- Addons are automatically enabled in Private Browsing mode
- Addons are automatically pinned to the toolbar
- Fixes DNS leaks with uBO prefetching

### Python Interface

- Automatically generates & injects unique device characteristics into Camoufox based on their real-world distribution
- WebGL fingerprint injection & rotation
- Uses the correct system fonts and subpixel antialiasing & hinting based on your target OS
- Avoid proxy detection by calculating your target geolocation, timezone, & locale from your proxy's target region
- Calculate and spoof the browser's language based on the distribution of language speakers in the proxy's target region
- Remote server hosting to use Camoufox with other languages that support Playwright
- Built-in virtual display buffer to run Camoufox headfully on a headless server
- Toggle image loading, WebRTC, and WebGL
- etc.

### Thanks ❤️

The design and implementation of Camoufox was inspired by the following projects, in no particular order:

| Project | Contribution |
| --- | --- |
| [LibreWolf](https://gitlab.com/librewolf-community/browser/source) | Debloat patches & build system inspiration |
| [BetterFox](https://github.com/yokoffing/BetterFox) | Debloat & optimizations |
| [Ghostery](https://github.com/ghostery/user-agent-desktop) | Debloat reference |
| [TOR Browser](https://2019.www.torproject.org/projects/torbrowser/design/) | Anti fingerprinting reference |
| [Jamir-boop/minimalisticfox](https://github.com/Jamir-boop/minimalisticfox) | Inspired Camoufox's minimalistic theming |
| [nicoth-in/Dark-Space-Theme](https://github.com/nicoth-in/Dark-Space-Theme) | Camoufox's dark theme |
| [Playwright](https://github.com/microsoft/playwright/tree/main/browser_patches/firefox), [Puppeteer/Juggler](https://github.com/puppeteer/juggler) | Original Juggler implementation |
| [CreepJS](https://github.com/abrahamjuliot/creepjs), [Browserleaks](https://browserleaks.com/), [BrowserScan](https://www.browserscan.net/) | Valuable leak testing sites |
| [riflosnake/HumanCursor](https://github.com/riflosnake/HumanCursor) | Original human-like cursor movement algorithm |

---

## Stealth Performance

Source: https://camoufox.com/stealth

In Camoufox, all of Playwright's internal Page Agent Javascript is sandboxed and isolated. This makes it **impossible** for a page to detect the presence of Playwright through Javascript inspection.

### Tests

Camoufox performs well against every major WAF I've tested. (Original test sites from [Botright](https://github.com/Vinyzu/botright/?tab=readme-ov-file#browser-stealth))

| Test | Status | Notes |
| --- | --- | --- |
| [**CreepJS**](https://abrahamjuliot.github.io/creepjs/) | ✔️ | 71.5%. Successfully spoofs all OS predictions. |
| [**Rebrowser Bot Detector**](https://bot-detector.rebrowser.net/) | ✔️ | All tests pass. |
| [**BrowserScan**](https://browserscan.net/) | ✔️ | 100%. Spoofs all geolocation & locale proxy detection. |
| **reCaptcha Score** | ✔️ |  |
| ‣ [nopecha.com](https://nopecha.com/demo/recaptcha) | ✔️ |  |
| ‣ [recaptcha-demo.appspot.com](https://recaptcha-demo.appspot.com/recaptcha-v3-request-scores.php) | ✔️ | 0.9 |
| ‣ [berstend.github.io](https://berstend.github.io/static/recaptcha/v3-programmatic.html) | ✔️ | 0.9 |
| **DataDome** | ✔️ |  |
| ‣ [DataDome bot bounty](https://yeswehack.com/programs/datadome-bot-bounty#program-description) | ✔️ | All test sites pass. |
| ‣ [hermes.com](https://www.hermes.com/us/en/) | ✔️ |  |
| **Imperva** | ✔️ |  |
| ‣ [ticketmaster.es](https://www.ticketmaster.es/) | ✔️ |  |
| **Cloudflare** | ✔️ |  |
| ‣ [Turnstile](https://nopecha.com/demo/turnstile) | ✔️ |  |
| ‣ [Interstitial](https://nopecha.com/demo/cloudflare) | ✔️ |  |
| **WebRTC IP Spoofing** | ✔️ |  |
| ‣ [Browserleaks WebRTC](https://browserleaks.net/webrtc) | ✔️ | Spoofs public IP correctly. |
| ‣ [CreepJS WebRTC](https://abrahamjuliot.github.io/creepjs/) | ✔️ | Spoofs Host & STUN IP correctly. |
| ‣ [BrowserScan WebRTC](https://www.browserscan.net/webrtc) | ✔️ | Spoofs Host & STUN IP correctly. |
| **Font Fingerprinting** | ✔️ |  |
| ‣ [Browserleaks Fonts](https://browserleaks.net/fonts) | ✔️ | Rotates all metrics. |
| ‣ [CreepJS TextMetrics](https://abrahamjuliot.github.io/creepjs/tests/fonts.html) | ✔️ | Rotates all metrics. |
| [**Incolumitas**](https://bot.incolumitas.com/) | ✔️ | 0.8-1.0 |
| [**SannySoft**](https://bot.sannysoft.com/) | ✔️ |  |
| [**Fingerprint.com**](https://fingerprint.com/products/bot-detection/) | ✔️ |  |
| [**IpHey**](https://iphey.com/) | ✔️ |  |
| [**Bet365**](https://www.bet365.com/#/AC/B1/C1/D1002/E79147586/G40/) | ✔️ |  |

**Warning:** Camoufox does **not** support injecting Chromium fingerprints. Some WAFs (such as [Interstitial](https://nopecha.com/demo/cloudflare)) test for Spidermonkey engine behavior, which is impossible to spoof.

---

## Python Interface

Source: https://camoufox.com/python

### Lightweight wrapper around the Playwright API to help launch Camoufox. 🚀

Camoufox's Python library wraps around Playwright's API to help automatically generate & inject unique device characteristics into Camoufox such as the OS, device info, navigator, fonts, headers, screen/viewport size, addons, etc.

It uses [BrowserForge](https://github.com/daijro/browserforge) under the hood to generate fingerprints that mimic the statistical distribution of device characteristics in real-world traffic.

In addition, it will also calculate your target geolocation, timezone, and locale to avoid proxy detection.

---

## Python Usage

Source: https://camoufox.com/python/usage

Camoufox is fully compatible with your existing Playwright code. You only have to change your browser initialization:

**Sync API:**
```python
from camoufox.sync_api import Camoufox

with Camoufox() as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

**Async API:**
```python
from camoufox.async_api import Camoufox

async with Camoufox() as browser:
    page = await browser.new_page()
    await page.goto("https://example.com")
```

### Parameters List

**All Playwright Firefox launch options are accepted, along with the following:**

### Device Rotation

Camoufox will generate device information for you based on the following parameters.

#### `os`

**Type:** `Optional[ListOrString]`

Operating system to use for the fingerprint generation. Can be `"windows"`, `"macos"`, `"linux"`, or a list to randomly choose from. By default, Camoufox will randomly choose from a list of all three.

```python
# Use a specific OS
with Camoufox(os="windows") as browser:
    ...

# Randomly choose from a list of OS
with Camoufox(os=["windows", "macos", "linux"]) as browser:
    ...
```

#### `fonts`

**Type:** `Optional[List[str]]`

Fonts to load into Camoufox, in addition to the default fonts for the target `os`. Takes a list of font family names that are installed on the system.

See [Fonts & font fingerprinting](../../fingerprint/fonts/) for more information.

```python
custom_fonts = ["Arial", "Helvetica", "Times New Roman"]
with Camoufox(fonts=custom_fonts) as browser:
    ...
```

#### `screen`

**Type:** `Optional[Screen]`

Constrains the screen dimensions of the generated fingerprint. Takes a `browserforge.fingerprints.Screen` instance.

While this sets the screen dimensions, it has very light impact on the size of the window. Sometimes the window will be larger, sometimes smaller.

```python
from browserforge.fingerprints import Screen

constrains = Screen(max_width=1920, max_height=1080)
with Camoufox(screen=constrains) as browser:
    ...
```

#### `webgl_config`

**Type:** `Optional[Tuple[str, str]]`

Use a specific WebGL vendor/renderer pair. Passed as a tuple of (vendor, renderer). The vendor & renderer combination must be supported for the target `os` or this will cause leaks.

Check [here](https://camoufox.com/webgl-research/) for a list of Camoufox's supported vendor & renderer combinations.

```python
with Camoufox(
    webgl_config=("Apple", "Apple M1, or similar"),
    os="macos",
) as browser:
    ...
```

#### `config`

**Type:** `Optional[Dict[str, Any]]`

If needed, individual Camoufox config properties can be overridden by passing them as a dictionary to the `config` parameter. This can be used to enable features that have not yet been implemented into the Python library.

**Warning:** This is an advanced feature and should be used with caution. The Camoufox Python library is designed to populate these properties for you.

See [Passing config](../config/) for more information.

### Configuration

Extra feature configuration and quality of life options.

#### `humanize`

**Type:** `Optional[Union[bool, float]]`

Humanize the cursor movement. Takes either `True`, or the MAX duration in seconds of the cursor movement. The cursor typically takes up to 1.5 seconds to move across the window.

See [Cursor movement info & demo](../../fingerprint/cursor-movement/) for more information.

```python
# Enable humanization with default settings
with Camoufox(humanize=True) as browser:
    ...

# Set a custom max duration for cursor movement
with Camoufox(humanize=2.0) as browser:
    ...
```

#### `headless`

**Type:** `Optional[Union[bool, Literal['virtual']]]`

Whether to run the browser in headless mode. Defaults to False. If you are running linux, passing 'virtual' will use Xvfb.

See [Virtual Display](../virtual-display/) for more information.

```python
# Run in headless mode
with Camoufox(headless=True) as browser:
    ...

# Run in headless mode on linux
with Camoufox(headless="virtual") as browser:
    ...
```

#### `addons`

**Type:** `Optional[List[str]]`

List of Firefox addons to use. Must be paths to extracted addons.

To load an `.xpi` file, rename it to a `.zip` file, extract it, and pass the extracted folder.

```python
addons = ["/path/to/addon1", "/path/to/addon2"]
with Camoufox(addons=addons) as browser:
    ...
```

#### `exclude_addons`

**Type:** `Optional[List[DefaultAddons]]`

Exclude the default addons. Passed as a list of `camoufox.DefaultAddons` enums.

See [Default addons](../../fingerprint/addons/#default-addons) for more information.

#### `window`

**Type:** `Optional[Tuple[int, int]]`

Set the window size in (width, height) pixels. This will also set the `window.screenX` and `window.screenY` properties to position the window at the center of the generated screen.

Camoufox will automatically generate a window size for you. Using a fixed window size can lead to fingerprinting. Do not set the window size to a fixed value unless for debugging purposes.

```python
with Camoufox(window=(1282, 955)) as browser:
    ...
```

#### `main_world_eval`

**Type:** `Optional[bool]`

Whether to inject scripts into the main world when prefixed with `mw:`

```python
with Camoufox(main_world_eval=True) as browser:
    page = browser.new_page()
    page.goto("https://example.com")
    # Modify the DOM
    page.evaluate("mw:document.querySelector('h1').remove()")
```

See [Main World Execution](../main-world-eval/) for more info.

#### `enable_cache`

**Type:** `Optional[bool]`

Whether to cache previous pages, requests, etc. Disabled by default as it uses more memory.

Keeping this disabled will not allow you to use the `page.go_back()` or `page.go_forward()` methods.

```python
with Camoufox(enable_cache=True) as browser:
    ...
```

#### `persistent_context`

**Type:** `Optional[bool]`

Whether to create a persistent context. Requires `user_data_dir`.

```python
with Camoufox(
    persistent_context=True,
    user_data_dir='/path/to/profile/dir',
) as context:
    ...
```

### Location & Language

Prevent proxy detection by matching your geolocation & locale with your target IP. This will populate the [Geolocation & Intl](../geoip/) properties for you.

#### `geoip`

**Type:** `Optional[Union[str, bool]]`

Calculates longitude, latitude, timezone, country, & locale based on the IP address. Pass the target IP address to use, or `True` to find the IP address automatically.

See [Geolocation & Proxies](../geoip/) for more information.

```python
# Use a specific IP address
with Camoufox(geoip="203.0.113.0", proxy=...) as browser:
    ...

# Automatically find the IP address
with Camoufox(geoip=True, proxy=...) as browser:
    ...
```

#### `locale`

**Type:** `Optional[Union[str, List[str]]]`

Locale(s) to use in Camoufox. Can be a list of strings, or a single string separated by a comma. The first locale in the list will be used for the Intl API.

```python
# Use a single locale
with Camoufox(locale="en-US") as browser:
    ...

# Generate a language based on the distribution of speakers the US
with Camoufox(locale="US") as browser:
    ...

# Use multiple accepted locales
with Camoufox(locale=["en-US", "fr-FR", "de-DE"]) as browser:
    ...
with Camoufox(locale="en-US,fr-FR,de-DE") as browser:
    ...
```

### Toggles

Shortcuts for common Firefox preferences and security toggles.

#### `block_images`

**Type:** `Optional[bool]`

Blocks all requests to images. This can help save your proxy usage.

```python
with Camoufox(block_images=True) as browser:
    ...
```

#### `block_webrtc`

**Type:** `Optional[bool]`

Blocks WebRTC entirely.

```python
with Camoufox(block_webrtc=True) as browser:
    ...
```

#### `block_webgl`

**Type:** `Optional[bool]`

Whether to block WebGL. To prevent leaks, only use this for special cases.

```python
with Camoufox(block_webgl=True) as browser:
    ...
```

#### `disable_coop`

**Type:** `Optional[bool]`

Disables the Cross-Origin-Opener-Policy (COOP). This allows elements in `cross-origin` iframes, such as the Turnstile checkbox, to be clicked.

```python
# Cloudflare turnstile example
with Camoufox(disable_coop=True, window=(1280, 720)) as browser:
    page = browser.new_page()
    page.goto('https://nopecha.com/demo/cloudflare')
    page.wait_for_load_state(state="domcontentloaded")
    page.wait_for_load_state('networkidle')
    page.wait_for_timeout(5000)  # 5 seconds
    page.mouse.click(210, 290)
    ...
```

Camoufox will warn you if your passed configuration might cause leaks.

---

## Python Installation

Source: https://camoufox.com/python/installation

First, install the `camoufox` package:

```bash
pip install -U camoufox[geoip]
```

The `geoip` parameter is optional, but heavily recommended if you are using proxies. It will download an extra dataset to determine the user's longitude, latitude, timezone, country, & locale.

### Download the browser

**Windows:**
```bash
camoufox fetch
```

**MacOS:**
```bash
camoufox fetch
```

**Linux:**
```bash
camoufox fetch
```

To uninstall, run `camoufox remove`.

### Camoufox CLI

Options:

```none
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

---

## Python Configuration

Source: https://camoufox.com/python/config

### Passing Config

If needed, Camoufox [config data](https://github.com/daijro/camoufox?tab=readme-ov-file#fingerprint-injection) can be overridden/passed as a dictionary to the `config` parameter. This can be used to enable features that have not yet been implemented into the Python library.

**Warning:** This isn't recommended, as Camoufox will populate this data for you automatically. See the [parameters list](https://camoufox.com/python/usage/#parameters-list) for more proper usage.

```python
from camoufox.sync_api import Camoufox

with Camoufox(
    config={
        'webrtc:ipv4': '123.45.67.89',
        'webrtc:ipv6': 'e791:d37a:88f6:48d1:2cad:2667:4582:1d6d',
    }
) as browser:
    page = browser.new_page()
    page.goto("https://www.browserscan.net/webrtc")
```

Camoufox will warn you if you are manually setting properties that the Python library handles internally.

---

## Python GeoIP & Proxy Support

Source: https://camoufox.com/python/geoip

By passing `geoip=True`, or passing in a target IP address, Camoufox will automatically use the target IP's longitude, latitude, timezone, country, locale, & spoof the WebRTC IP address.

It will also calculate and spoof the browser's language based on the distribution of language speakers in the target region.

### Installation

Install Camoufox with the `geoip` extra:

```bash
pip install -U "camoufox[geoip]"
```

### Usage

Pass in `geoip=True` with Playwright's `proxy` parameter:

```python
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

---

## Python Main World Execution

Source: https://camoufox.com/python/main-world-eval

### Reading the DOM - Isolated World

By default, all JavaScript execution is ran in an isolated scope, invisible to the page.

This makes it impossible for the page to detect JavaScript reading the DOM:

```python
>>> page.goto("https://example.com/")
>>> page.evaluate("document.querySelector('h1').innerText")
'Example Domain'
```

However, your JavaScript will **not** be able to modify the DOM:

```python
>>> page.evaluate("document.querySelector('h1').remove()")
# Will not work!
```

### Modifying the DOM - Main World

To be able to modify the DOM, run JavaScript in the **main world**—the non-isolated scope.

**Leak Warning:** All code executed in the main world can be detected by the target website. Only execute JavaScript in the main world if absolutely necessary.

To enable main world execution, set the `main_world_eval` parameter to `True`:

```python
with Camoufox(main_world_eval=True) as browser:
    page = browser.new_page()
    page.goto("https://example.com/")
```

Now, you can inject JavaScript in the main world by prefixing the code with `mw:`

```python
>>> page.evaluate("mw:document.querySelector('h1').remove()")
# h1 is now removed!
```

You can also return JSON serializable data from the main world:

```python
>>> page.evaluate("mw:{key: 'value'}")
{'key': 'value'}
```

**Note:** Returning references to nodes/elements from the main world is not supported, since Playwright's utilities do not run in the main world.

To retrieve node references, use the isolated world instead.

---

## Python Remote Server

Source: https://camoufox.com/python/remote-server

*(Note: This page timed out during scraping, but the functionality is available through the CLI `server` command)*

You can launch a Playwright server to use Camoufox with other languages that support Playwright:

```bash
camoufox server
```

---

## Python Virtual Display

Source: https://camoufox.com/python/virtual-display

While Camoufox includes patches to prevent headless detection, running in headless mode may still be detectable in the future. It's recommended to use a virtual display buffer to run Camoufox headlessly.

If you are running Linux, and would like to run Camoufox headlessly in a virtual display, install `xvfb`:

**Debian-based distros:**
```bash
sudo apt-get install xvfb
```

**Arch-based distros:**
```bash
sudo pacman -S xorg-server-xvfb
```

### Confirm `Xvfb` is installed:

```bash
$ which Xvfb
/usr/bin/Xvfb
```

### Usage

Passing `headless="virtual"` will spawn a new lightweight virtual display in the background for Camoufox to run in.

**Sync API:**
```python
from camoufox.sync_api import Camoufox

with Camoufox(
    headless="virtual"
) as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

**Async API:**
```python
from camoufox.async_api import Camoufox

async with Camoufox(
    headless="virtual"
) as browser:
    page = await browser.new_page()
    await page.goto("https://example.com")
```

---

## Python BrowserForge Integration

Source: https://camoufox.com/python/browserforge

Camoufox is compatible with [BrowserForge](https://github.com/daijro/browserforge) fingerprints.

By default, Camoufox will generate an use a random BrowserForge fingerprint based on the target `os` & `screen` constraints.

```python
from camoufox.sync_api import Camoufox
from browserforge.fingerprints import Screen

with Camoufox(
    os=('windows', 'macos', 'linux'),
    screen=Screen(max_width=1920, max_height=1080),
) as browser:
    page = browser.new_page()
    page.goto("https://example.com/")
```

If Camoufox is being ran in headful mode, the max screen size will be generated based on your monitor's dimensions unless otherwise specified.

**Note:** To prevent UA mismatch detection, Camoufox only generates fingerprints with the same browser version as the current Camoufox version by default. If rotating the Firefox version is absolutely necessary, it would be more advisable to rotate between older versions of Camoufox instead.

### Injecting custom Fingerprint objects...

**Warning:** It is recommended to pass `os` & `screen` constraints into Camoufox instead. Camoufox will handle fingerprint generation for you. This will be deprecated in the future.

You can also inject your own Firefox BrowserForge fingerprint into Camoufox.

```python
from camoufox.sync_api import Camoufox
from browserforge.fingerprints import FingerprintGenerator

fg = FingerprintGenerator(browser='firefox')

# Launch Camoufox with a random Firefox fingerprint
with Camoufox(fingerprint=fg.generate()) as browser:
    page = browser.new_page()
    page.goto("https://example.com/")
```

**Note:** As of now, some properties from BrowserForge fingerprints will not be passed to Camoufox. This is due to the outdated fingerprint dataset from Apify's fingerprint-suite (see [here](https://github.com/apify/fingerprint-suite/discussions/308)). Properties will be re-enabled as soon as an updated dataset is available.

---

## Fingerprint Injection

Source: https://camoufox.com/fingerprint

In Camoufox, data is intercepted at the C++ implementation level, making the changes undetectable through JavaScript inspection.

To spoof fingerprint properties, pass a JSON containing properties to spoof to the [Python interface](https://github.com/daijro/camoufox/tree/main/pythonlib#camoufox-python-interface):

```python
>>> with Camoufox(config={"property": "value"}) as browser:
```

Config data not set by the user will be automatically populated using [BrowserForge](https://github.com/daijro/browserforge) fingerprints, which mimic the statistical distribution of device characteristics in real-world traffic.

### Getting started

Camoufox accepts the following properties:

- [Navigator](navigator/)
- [Cursor Movement](cursor-movement/)
- [Fonts](fonts/)
- [Screen](screen/)
- [Window](window/)
- [Document](document/)
- [HTTP Headers](headers/)
- [Geolocation & Intl](geolocation/)
- [WebRTC IP](webrtc/)
- [WebGL](webgl/)
- [Media & Audio](media-audio/)
- [Voices](voices/)
- [Addons](addons/)
- [Miscellaneous](miscellaneous/)

---

## Fingerprint Details

### Cursor Movement

Source: https://camoufox.com/fingerprint/cursor-movement

#### Human-like Cursor movement

Camoufox has built-in support for human-like cursor movement. The natural motion algorithm was originally from [rifosnake's HumanCursor](https://github.com/riflosnake/HumanCursor), but has been rewritten in C++ and modified for more distance-aware trajectories.

#### Properties

| Property | Type | Description |
| --- | --- | --- |
| `humanize` | bool | Enable/disable human-like cursor movement. Defaults to False. |
| `humanize:maxTime` | double | Maximum time in seconds for the cursor movement. Defaults to `1.5`. |
| `humanize:minTime` | double | Minimum time in seconds for the cursor movement. |
| `showcursor` | bool | Toggles the cursor highlighter. Defaults to True. |

**Note:** The cursor highlighter is **not** ran in the page context. It will not be visible to the page. You don't have to worry about it leaking.

---

### Fonts

Source: https://camoufox.com/fingerprint/fonts

Fonts can be passed to be used in Camoufox through the `fonts` config property.

| Property | Type | Description |
| --- | --- | --- |
| `fonts` | array | List of font names to be used |
| `fonts:spacing_seed` | uint | Seed to use for font metric spacing randomization |

By default, Camoufox is bundled with the default Windows 11 22H2 fonts, macOS Sonoma fonts, and Linux fonts used in the TOR bundle. Camoufox will automatically add the default fonts associated your spoofed User-Agent OS (the value passed in `navigator.userAgent`).

#### Mac OS fonts

Camoufox includes a comprehensive list of macOS Sonoma fonts. See the [full list](https://camoufox.com/fingerprint/fonts/#mac-os-fonts) on the website.

#### Windows fonts

Camoufox includes Windows 11 22H2 fonts.

#### Linux fonts

Camoufox includes Linux fonts used in the TOR bundle.

#### New fonts

Other fonts can be added by copying them into the `fonts/` directory in Camoufox, or by installing them on your system.

**Tip:** It is recommended that you **randomly** pass custom fonts to the `fonts` config property to avoid font fingerprinting!

#### Font Metrics

Camoufox has a built in mechanism to prevent fingerprinting by font metrics & unicode glyphs:

This works by shifting the spacing of each letter by a random value between 0-0.1px.

---

### Screen

Source: https://camoufox.com/fingerprint/screen

#### Spoof the screen size, pixel depth, and position of the window.

#### Properties

| Property | Type | Description |
| --- | --- | --- |
| `screen.availHeight` | uint | Available height of the screen |
| `screen.availWidth` | uint | Available width of the screen |
| `screen.availTop` | uint | Y-coordinate of the top of the available area |
| `screen.availLeft` | uint | X-coordinate of the left of the available area |
| `screen.height` | uint | Total height of the screen |
| `screen.width` | uint | Total width of the screen |
| `screen.colorDepth` | uint | Color depth of the screen |
| `screen.pixelDepth` | uint | Pixel depth of the screen |
| `screen.pageXOffset` | double | Horizontal scroll position |
| `screen.pageYOffset` | double | Vertical scroll position |

**Note:** `screen.colorDepth` and `screen.pixelDepth` are synonymous.

---

### Navigator

Source: https://camoufox.com/fingerprint/navigator

Navigator properties can be fully spoofed to other Firefox fingerprints, and it is **completely safe**!

#### Properties

| Property | Type | Description |
| --- | --- | --- |
| `navigator.userAgent` | str | Browser and system information |
| `navigator.doNotTrack` | str | User's tracking preference |
| `navigator.appCodeName` | str | Browser's code name |
| `navigator.appName` | str | Browser's name |
| `navigator.appVersion` | str | Browser's version |
| `navigator.oscpu` | str | Operating system and CPU information |
| `navigator.language` | str | Preferred language of the user |
| `navigator.languages` | array | User's preferred languages |
| `navigator.platform` | str | Platform the browser is running on |
| `navigator.hardwareConcurrency` | uint | Number of logical processors |
| `navigator.product` | str | Product name of the browser |
| `navigator.productSub` | str | Build number of the browser |
| `navigator.maxTouchPoints` | uint | Maximum number of simultaneous touch contact points |
| `navigator.cookieEnabled` | bool | Whether cookies are enabled |
| `navigator.globalPrivacyControl` | bool | User's global privacy control preference |
| `navigator.buildID` | str | Build identifier of the browser |
| `navigator.onLine` | bool | Whether the browser is online |

**Note:**
- **navigator.webdriver** is set to false at all times.
- `navigator.language` & `navigator.languages` will fall back to the `locale:language`/`locale:region` values if not set.
- When spoofing Chrome fingerprints, the following may leak:
  - navigator.userAgentData missing.
  - navigator.deviceMemory missing.
- Changing the presented Firefox version can be detected by some testing websites, but typically will not flag production WAFs.

---

### Window

Source: https://camoufox.com/fingerprint/window

#### Spoof the inner/outer window size, scroll offsets, window position, history length, and more.

#### Properties

| Property | Type | Description |
| --- | --- | --- |
| `window.scrollMinX` | int | Minimum horizontal scroll offset |
| `window.scrollMinY` | int | Minimum vertical scroll offset |
| `window.scrollMaxX` | int | Maximum horizontal scroll offset |
| `window.scrollMaxY` | int | Maximum vertical scroll offset |
| `window.outerHeight` | uint | Total height of the browser window |
| `window.outerWidth` | uint | Total width of the browser window |
| `window.innerHeight` | uint | Height of the browser viewport |
| `window.innerWidth` | uint | Width of the browser viewport |
| `window.screenX` | int | Horizontal distance from the left border |
| `window.screenY` | int | Vertical distance from the top border |
| `window.history.length` | uint | Number of entries in the session history |
| `window.devicePixelRatio` | double | Ratio of physical pixels to CSS pixels |

**Note:** Setting the outer window viewport will cause some cosmetic defects to the Camoufox window if the user attempts to manually resize it. Under no circumstances will Camoufox allow the outer window viewport to be resized.

---

### Document

Source: https://camoufox.com/fingerprint/document

Spoof the size of the HTML body element.

**Warning:** Spoofing document.body has been implemented, but it is more advisable to set `window.innerWidth` and `window.innerHeight` instead.

#### Properties

| Property | Type | Description |
| --- | --- | --- |
| `document.body.clientWidth` | uint | Width of the document's body |
| `document.body.clientHeight` | uint | Height of the document's body |
| `document.body.clientTop` | uint | Width of the top border of the document's body |
| `document.body.clientLeft` | uint | Width of the left border of the document's body |

---

### Canvas

Source: https://camoufox.com/fingerprint/canvas

#### What most anti-detect browsers do

Nearly all commercial anti-detect solutions add noise to the canvas to avoid canvas fingerprinting, even on shapes that render the same on every device. This technique is easily detectable by drawing a rectangle and comparing it to an expected result.

#### What Camoufox does

Camoufox implements Canvas anti-fingerprinting by using a patched build of [Skia](https://skia.org/) with modified subpixel rendering logic, making it impossible to tell your device apart from a device that renders anti-aliasing in a different way.

#### Properties

| Property | Type | Description |
| --- | --- | --- |
| `canvas:aaOffset` | int | Offset each pixel's transparency when drawing anti-aliasing. |
| `canvas:aaCapOffset` | bool | By default, the offset will cause the pixel's transparency to wrap around the 0-255 alpha range. By enabling this property, the offset will be clamped to 0-255. |

This patch is closed source. Releases with this patch is only available on the GitHub releases page.

#### See related

- [Canvas fingerprint & integrity tests](https://camoufox.com/tests/canvas)
- [2D Shape drawing tests](https://camoufox.com/tests/shapetests)
- [Research discussion](https://github.com/daijro/camoufox/discussions/41)

---

### WebGL

Source: https://camoufox.com/fingerprint/webgl

**Research:** As more users visit this site and opt into helping Camoufox's WebGL research, I will have a more solid profile of each GPU's parameters, supported extensions, context attributes, & shader precision formats.

See [analytics & research](../../webgl-research/) for more information.

#### Demo site

This repository includes a demo site that prints your browser's WebGL parameters. You can use this site to generate WebGL fingerprints for Camoufox from other devices.

See [demo](https://camoufox.com/tests/webgl) for more information.

#### Properties

Camoufox supports spoofing WebGL parameters, supported extensions, context attributes, and shader precision formats.

**Warning:** Do **NOT** randomly assign values to these properties. WAFs hash your WebGL fingerprint and compare it against a dataset. Randomly assigning values will lead to detection as an unknown device.

| Property | Description | Example |
| --- | --- | --- |
| `webGl:renderer` | Spoofs the name of the unmasked WebGL renderer. | `"NVIDIA GeForce GTX 980, or similar"` |
| `webGl:vendor` | Spoofs the name of the unmasked WebGL vendor. | `"NVIDIA Corporation"` |
| `webGl:supportedExtensions` | An array of supported WebGL extensions ([full list](https://registry.khronos.org/webgl/extensions/)). | `["ANGLE_instanced_arrays", "EXT_color_buffer_float", "EXT_disjoint_timer_query", ...]` |
| `webGl2:supportedExtensions` | The same as `webGl:supportedExtensions`, but for WebGL2. | `["ANGLE_instanced_arrays", "EXT_color_buffer_float", "EXT_disjoint_timer_query", ...]` |
| `webGl:contextAttributes` | A dictionary of WebGL context attributes. | `{"alpha": true, "antialias": true, "depth": true, ...}` |
| `webGl2:contextAttributes` | The same as `webGl:contextAttributes`, but for WebGL2. | `{"alpha": true, "antialias": true, "depth": true, ...}` |
| `webGl:parameters` | A dictionary of WebGL parameters. Keys must be GL enums, and values are the values to spoof them as. | `{"2849": 1, "2884": false, "2928": [0, 1], ...}` |
| `webGl2:parameters` | The same as `webGl:parameters`, but for WebGL2. | `{"2849": 1, "2884": false, "2928": [0, 1], ...}` |
| `webGl:parameters:blockIfNotDefined` | If set to `true`, only the parameters in `webGl:parameters` will be allowed. Can be dangerous if not used correctly. | `true`/`false` |
| `webGl2:parameters:blockIfNotDefined` | If set to `true`, only the parameters in `webGl2:parameters` will be allowed. Can be dangerous if not used correctly. | `true`/`false` |
| `webGl:shaderPrecisionFormats` | A dictionary of WebGL shader precision formats. Keys are formatted as `"<shaderType>,<precisionType>"`. | `{"35633,36336": {"rangeMin": 127, "rangeMax": 127, "precision": 23}, ...}` |
| `webGl2:shaderPrecisionFormats` | The same as `webGL:shaderPrecisionFormats`, but for WebGL2. | `{"35633,36336": {"rangeMin": 127, "rangeMax": 127, "precision": 23}, ...}` |
| `webGl:shaderPrecisionFormats:blockIfNotDefined` | If set to `true`, only the shader precisions in `webGl:shaderPrecisionFormats` will be allowed. | `true`/`false` |
| `webGl2:shaderPrecisionFormats:blockIfNotDefined` | If set to `true`, only the shader precisions in `webGl2:shaderPrecisionFormats` will be allowed. | `true`/`false` |

---

### Geolocation & Intl

Source: https://camoufox.com/fingerprint/geolocation

#### Properties

| Property | Type | Description | Required Keys |
| --- | --- | --- | --- |
| `geolocation:latitude` | double | Latitude to use. | `geolocation:longitude` |
| `geolocation:longitude` | double | Longitude to use. | `geolocation:latitude` |
| `geolocation:accuracy` | double | Accuracy in meters. This will be calculated automatically using the decimal precision of `geolocation:latitude` & `geolocation:longitude` if not set. |  |
| `timezone` | str | Set a custom TZ timezone (e.g. "America/Chicago"). This will also change `Date()` to return the local time. |  |
| `locale:language` | str | Spoof the Intl API and system language (e.g. "en") | `locale:region` |
| `locale:region` | str | Spoof the Intl API and system region (e.g. "US"). | `locale:language` |
| `locale:script` | str | Set a custom script (e.g. "Latn"). Will be set automatically if not specified. |  |
| `locale:all` | str | List of all accepted locale values (separated by comma). The first item should match the Intl API. If not passed, it will default to the locale used in the Intl API in the format `"en-US, en"`. |  |

The **Required Keys** are keys that must also be set for the property to work.

**Note:**
- Location permission prompts will be accepted automatically if `geolocation:latitude` and `geolocation:longitude` are set.
- `timezone` **must** be set to a valid TZ identifier. See [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) for a list of valid timezones.
- `locale:language` & `locale:region` **must** be set to valid locale values. See [here](https://simplelocalize.io/data/locales/) for a list of valid locale-region values.

---

### HTTP Headers

Source: https://camoufox.com/fingerprint/headers

#### Camoufox can override the following network headers.

#### Properties

| Property | Type | Description |
| --- | --- | --- |
| `headers.User-Agent` | str | Browser and system information |
| `headers.Accept-Language` | str | Acceptable languages for the response |
| `headers.Accept-Encoding` | str | Acceptable encodings for the response |

**Note:** If `headers.User-Agent` is not set, it will fall back to `navigator.userAgent`.

---

### WebRTC IP

Source: https://camoufox.com/fingerprint/webrtc

Camoufox implements WebRTC IP spoofing at the protocol level by modifying ICE candidates and SDP before they're sent.

#### Properties

| Property | Type | Description |
| --- | --- | --- |
| `webrtc:ipv4` | str | IPv4 address to use |
| `webrtc:ipv6` | str | IPv6 address to use |

**Note:** To completely disable WebRTC, set the `media.peerconnection.enabled` preference to `false`.

---

### Addons

Source: https://camoufox.com/fingerprint/addons

In the Camoufox Python library, addons can be loaded with the `addons` parameter.

Camoufox takes extracted addons. To load an `.xpi` file, rename it to a `.zip` file, extract it, and pass the extracted folder.

```python
from camoufox.sync_api import Camoufox

with Camoufox(addons=['/path/to/addon', '/path/to/addon2']) as browser:
    page = browser.new_page()
```

#### Default Addons

Camoufox will automatically download and use the latest [uBlock Origin](https://ublockorigin.com/) with custom privacy/adblock filters to help with ad circumvention.

You can also **exclude** default addons with the `exclude_addons` parameter:

```python
from camoufox.sync_api import Camoufox
from camoufox import DefaultAddons

with Camoufox(exclude_addons=[DefaultAddons.UBO]) as browser:
    page = browser.new_page()
```

---

### Media & Audio

Source: https://camoufox.com/fingerprint/media-audio

#### Media Devices

Spoof the amount of microphones, webcams, and speakers available.

| Property | Type | Description | Default |
| --- | --- | --- | --- |
| `mediaDevices:enabled` | bool | Whether to enable media device spoofing | `false` |
| `mediaDevices:micros` | uint | Amount of microphones | `3` |
| `mediaDevices:webcams` | uint | Amount of webcams | `1` |
| `mediaDevices:speakers` | uint | Amount of speakers | `1` |

#### AudioContext

Spoof the AudioContext sample rate, output latency, and max channel count.

| Property | Type | Description |
| --- | --- | --- |
| `AudioContext:sampleRate` | uint | AudioContext sample rate |
| `AudioContext:outputLatency` | double | AudioContext output latency |
| `AudioContext:maxChannelCount` | uint | AudioContext max channel count |

Testing site: https://audiofingerprint.openwpm.com/

---

### Voices

Source: https://camoufox.com/fingerprint/voices

Spoof the voices used in the browser's Web Speech API.

| Property | Type | Description | Example |
| --- | --- | --- | --- |
| `voices` | array | An array of maps for each voice. Each map must contain the keys `isLocalService`, `isDefault`, `voiceUri`, `name`, and `lang`. | See example below |
| `voices:blockIfNotDefined` | bool | Whether to block system voices. Default is `false`. | `true` |
| `voices:fakeCompletion` | bool | Return a successful TTS completion when a fake voice is used. If disabled, an error is thrown instead (default in RFP). Default is `false`. | `true` |
| `voices:fakeCompletion:charsPerSecond` | double | Playback rate of a fake voice in characters per second. Default is `12.5`, which is ~150wpm. | `15.0` |

Example `voices` array:
```json
[
  {
    "isLocalService": true,
    "isDefault": true,
    "voiceUri": "Ting-Ting",
    "name": "Ting-Ting",
    "lang": "zh-CN"
  },
  ...
]
```

Using `voices:fakeCompletion` is not fully implemented. It can be hypothetically detectable if a website measures the utterance duration and compares it with the elapsed time taken. However, anti-bots will never play TTS to detect you.

---

### Miscellaneous

Source: https://camoufox.com/fingerprint/miscellaneous

#### Spoof battery status and PDF viewer.

| Property | Type | Description |
| --- | --- | --- |
| `pdfViewerEnabled` | bool | Sets navigator.pdfViewerEnabled. Please keep this on though, many websites will flag a lack of pdfViewer as a headless browser. |
| `battery:charging` | bool | Battery charging status |
| `battery:chargingTime` | double | Battery charging time |
| `battery:dischargingTime` | double | Battery discharging time |
| `battery:level` | double | Battery level |

**Note:** Please keep pdfViewer on, as many websites will flag a lack of PDF viewer as a headless browser.

---

## Development

Source: https://camoufox.com/development

**Note:** The following content is intended for those interested in building & debugging Camoufox.

### Building

Instructions on how to build Camoufox from scratch:

- [Build system overview](overview/)
- [Build via CLI](buildcli/)
- [Build via Docker](docker/)

### Developing patches

How to develop patches for Camoufox and find leaks:

- [Development tools](tools/)
- [Leak debugging workflow](workflow/)

---

### Build System Overview

Source: https://camoufox.com/development/overview

Here is a diagram of the build system, and its associated make commands:

```
Local
  │
  ├─ make build
  ├─ make package-linux
  ├─ make package-windows
  ├─ make package-macos
  ├─ make fetch
  └─ make dir
      │
      ▼
Camoufox Repository
  │
  ├─ Fingerprint masking patches
  ├─ uBlock & B.P.C.
  ├─ Debloat/optimizations
  ├─ Win, Mac, Linux fonts
  └─ Patched Juggler
      │
      ▼
Firefox Source
  │
  ▼
Patched Source
  │
  ▼
Built
  │
  ├─ Linux Portable
  ├─ Windows Portable
  └─ macOS Portable
```

This was originally based on the LibreWolf build system.

---

### Building in CLI

Source: https://camoufox.com/development/buildcli

**Warning:** Camoufox's build system is designed to be used in Linux. WSL will not work!

First, clone this repository with Git:

```bash
git clone --depth 1 https://github.com/daijro/camoufox
cd camoufox
```

Next, build the Camoufox source code with the following command:

```bash
make dir
```

After that, you have to bootstrap your system to be able to build Camoufox. You only have to do this one time. It is done by running the following command:

```bash
make bootstrap
```

Finally you can build and package Camoufox the following command:

```bash
python3 multibuild.py --target linux windows macos --arch x86_64 arm64 i686
```

#### CLI Parameters

```bash
Options:
  -h, --help            show this help message and exit
  --target {linux,windows,macos} [{linux,windows,macos} ...]
                        Target platforms to build
  --arch {x86_64,arm64,i686} [{x86_64,arm64,i686} ...]
                        Target architectures to build for each platform
  --bootstrap           Bootstrap the build system
  --clean               Clean the build directory before starting

Example:
$ python3 multibuild.py --target linux windows macos --arch x86_64 arm64
```

---

### Building in Docker

Source: https://camoufox.com/development/docker

To build Camoufox on a non-Linux system you can use Docker.

1. Create the Docker image containing Firefox's source code:

```bash
docker build -t camoufox-builder .
```

2. Build Camoufox patches to a target platform and architecture:

```bash
docker run -v "$(pwd)/dist:/app/dist" camoufox-builder --target <os> --arch <arch>
```

#### How can I use my local ~/.mozbuild directory?

If you want to use the host's .mozbuild directory, you can use the following command instead to run the docker:

```bash
docker run \
  -v "$HOME/.mozbuild":/root/.mozbuild:rw,z \
  -v "$(pwd)/dist:/app/dist" \
  camoufox-builder \
  --target <os> \
  --arch <arch>
```

#### Docker CLI Parameters

```bash
Options:
  -h, --help            show this help message and exit
  --target {linux,windows,macos} [{linux,windows,macos} ...]
                        Target platforms to build
  --arch {x86_64,arm64,i686} [{x86_64,arm64,i686} ...]
                        Target architectures to build for each platform
  --bootstrap           Bootstrap the build system
  --clean               Clean the build directory before starting

Example:
$ docker run -v "$(pwd)/dist:/app/dist" camoufox-builder --target windows macos linux --arch x86_64 arm64 i686
```

Build artifacts will now appear written under the `dist/` folder.

---

### Development Tools

Source: https://camoufox.com/development/tools

This repo comes with a developer UI under scripts/developer.py:

```none
make edits
```

Patches can be edited, created, removed, and managed through here.

#### How to make a patch

1. In the developer UI, click **Reset workspace**.
2. Make changes in the `camoufox-*/` folder as needed. You can test your changes with `make build` and `make run`.
3. After you're done making changes, click **Write workspace to patch** and save the patch file.

#### How to work on an existing patch

1. In the developer UI, click **Edit a patch**.
2. Select the patch you'd like to edit. Your workspace will be reset to the state of the selected patch.
3. After you're done making changes, hit **Write workspace to patch** and overwrite the existing patch file.

---

### Leak Debugging

Source: https://camoufox.com/development/workflow

This is a flow chart demonstrating my process for determining leaks without deobfuscating WAF Javascript. The method incrementally reintroduces Camoufox's features into Firefox's source code until the testing site flags.

This process requires a Linux system and assumes you have Firefox build tools installed (see [here](https://github.com/daijro/camoufox?tab=readme-ov-file#build-cli)).

#### Flowchart

```
Start
  │
  ▼
Does website flag in the official Firefox?
  │
  ├─ Yes → Likely bad IP/rate-limiting. If the website fails on both headless and headful mode on the official Firefox distribution, the issue is not with the browser.
  │
  └─ No
      │
      ▼
Run make ff-dbg(1) and build(2) a clean distribution of Firefox.
Does the website flag in Firefox headless mode(4)?
  │
  ├─ Yes → Does the website flag in headful mode(3) AND headless mode(4)?
  │   │
  │   ├─ Yes → Open the developer UI(5), apply config.patch, then rebuild(2). Does the website still flag(3)?
  │   │   │
  │   │   ├─ Yes → Enable privacy.resistFingerprinting in the config(6). Does the website still flag(3)?
  │   │   │   │
  │   │   │   ├─ Yes → In the config(6), enable FPP and start omitting overrides until you find the one that fixed the leak.
  │   │   │   │
  │   │   │   └─ No → If you get to this point, you may need to deobfuscate the Javascript behind the website to identify what it's testing.
  │   │   │
  │   │   └─ No → Open the developer UI, apply the playwright bootstrap patch, then rebuild. Does it still flag?
  │   │       │
  │   │       ├─ Yes → Omit options from camoufox.cfg(6) and rerun(3) until you find the one causing the leak.
  │   │       │
  │   │       └─ No → Juggler needs to be debugged to locate the leak.
  │   │
  │   └─ No → The issue has nothing to do with Playwright. Apply the rest of the Camoufox patches one by one until the one causing the leak is found.
  │
  └─ No → Continue with normal testing
```

#### Cited Commands

| # | Command | Description |
| --- | --- | --- |
| (1) | `make ff-dbg` | Setup vanilla Firefox with minimal patches. |
| (2) | `make build` | Build the source code. |
| (3) | `make run` | Runs the built browser. |
| (4) | `make run args="--headless https://test.com"` | Run a URL in headless mode. All redirects will be printed to the console to determine if the test passed. |
| (5) | `make edits` | Opens the developer UI. Allows the user to apply/undo patches, and see which patches are currently applied. |
| (6) | `make edit-cfg` | Edit camoufox.cfg in the default system editor. |

---

## Legal

Source: https://camoufox.com/legal

### Liability Disclaimer

### Camoufox does **NOT** provide **liability or warranty**.

**No responsibility is accepted for the use of this software.** This software is intended for ethical and research purposes only. Users should use this software at their own risk. The developers of the software cannot be held liable for any damages that result from the use of this software. The software must not be used for malicious purposes. By using this software, you agree to this disclaimer and acknowledge that you are using the software responsibly and in compliance with all applicable laws and regulations.

### Acceptable Use

By using Camoufox, the User agrees **NOT**:

- to violate, or encourage the violation of, the legal rights of others;
- to engage in, promote or encourage illegal activity, including child sexual exploitation, child abuse, or terrorism or violence that can cause death, serious harm, or injury to individuals or groups of individuals;
- to use Camoufox for any unlawful, invasive, infringing, defamatory or fraudulent purpose including Non-Consensual Explicit Imagery (NCEI), violating intellectual property rights of others, phishing, or creating a pyramid scheme;
- to distribute viruses, worms, Trojan horses, corrupted files, hoaxes, or other items of a destructive or deceptive nature;
- to gain unauthorized access to, disrupt, or impair the use of any service, or the equipment used to provide service, by customers, authorized resellers, or other authorized users;
- to generate, distribute, publish or facilitate unsolicited mass email, promotions, advertisements, or other solicitations ("spam"); or
- to use any service, or any interfaces provided with any service, to access any other product or service in a manner that violates the terms of service of such other product or service without authorization.

---

## Donate

Source: https://camoufox.com/donate

### About Me

I'm an independent web security researcher working from a dorm room at the University of Texas at Austin.

### Support Camoufox

If you'd like to support my work and my tuition, feel free to donate!

[Buy daijro a Coffee](https://ko-fi.com/daijro)

---

## Test Pages

### Button Click Test

Source: https://camoufox.com/tests/buttonclick.html

A simple test page for button clicking functionality.

### Canvas Test

Source: https://camoufox.com/tests/canvas.html

Canvas Fingerprinting Demo with various tests including:
- Pixel Integrity Check
- Text Rendering
- Shape Rendering (Rectangle, Circle, Triangle, and Arc)
- Shadows
- Text Shadow
- Gradients
- Composite Operations
- Combined Fingerprint

### Shadow DOM Test

Source: https://camoufox.com/tests/shadowdom.html

A demo page for testing clicks within the shadow DOM.

### Shape Tests

Source: https://camoufox.com/tests/shapetests.html

Various canvas shape rendering tests including:
- Solid Image
- Red Rectangle
- Red Triangle
- Black to White Gradient
- Blended Squares
- Circle
- 1px Diagonal Line
- Aliased Line
- Ellipse
- Curved Line

### WebGL Test

Source: https://camoufox.com/tests/webgl.html

A demo site that prints your browser's WebGL parameters in Camoufox format. You can use this site to generate WebGL fingerprints for Camoufox from other devices.

### Canvas Low Entropy Test

Source: https://camoufox.com/tests/private/canvas-low-entropy.html

A test to detect canvas randomization by comparing pixel data between two canvases.

### Fingerprint Generator Tree

Source: https://camoufox.com/assets/fpgen/tree.html

A comprehensive tree view of all fingerprint properties that can be generated and tested.

---

## WebGL Research

Source: https://camoufox.com/webgl-research

As of January 2025, a newer fingerprint generation library with WebGL is in the works!

Below is a full list of possible WebGL vendor & renderer combinations in Firefox. Items that are checked off indicate that Camoufox has compiled a sufficient amount of analytics on that vendor/renderer combination for accurate simulation and fingerprint rotation.

### Possible Firefox WebGL Combinations

**Last Updated:** This report was last updated on December 05, 2024.

#### Windows

| Vendor | Renderer | Probability | Sufficient |
| --- | --- | --- | --- |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce GTX 980 Direct3D11 vs_5_0 ps_5_0), or similar | 29.0% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics Direct3D11 vs_5_0 ps_5_0), or similar | 24.1% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics 400 Direct3D11 vs_5_0 ps_5_0), or similar | 19.7% | ✅ |
| Google Inc. (AMD) | ANGLE (AMD, Radeon HD 3200 Graphics Direct3D11 vs_5_0 ps_5_0), or similar | 10.6% | ✅ |
| Google Inc. (AMD) | ANGLE (AMD, Radeon R9 200 Series Direct3D11 vs_5_0 ps_5_0), or similar | 6.6% | ✅ |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce GTX 480 Direct3D11 vs_5_0 ps_5_0), or similar | 1.7% | ✅ |
| Google Inc. (Microsoft) | ANGLE (Microsoft, Microsoft Basic Render Driver Direct3D11 vs_5_0 ps_5_0), or similar | 1.6% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics Direct3D11 vs_5_0 ps_5_0) | 0.9% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics Direct3D11 vs_4_1 ps_4_1), or similar | 0.5% | ✅ |
| Google Inc. (Google) | ANGLE (Google, Vulkan 1.3.0 (SwiftShader Device (Subzero) (0x0000C0DE)), SwiftShader driver) | 0.3% | ✅ |
| Google Inc. (AMD) | ANGLE (AMD, Radeon R9 200 Series Direct3D11 vs_5_0 ps_5_0) | 0.2% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics Direct3D11 vs_4_0 ps_4_0) | 0.2% | ✅ |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce 8800 GTX Direct3D11 vs_4_0 ps_4_0) | 0.2% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel 945GM Direct3D11 vs_4_0 ps_4_0) | 0.2% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics Direct3D11 vs_4_1 ps_4_1) | 0.2% | ✅ |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce GTX 980 Direct3D11 vs_5_0 ps_5_0) | 0.2% | ✅ |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce GTX 480 Direct3D11 vs_5_0 ps_5_0) | 0.2% | ✅ |
| Google Inc. (AMD) | ANGLE (AMD, Radeon HD 5850 Direct3D11 vs_5_0 ps_5_0), or similar | 0.5% | ❌ |
| Google Inc. (AMD) | ANGLE (AMD, Radeon HD 3200 Graphics Direct3D11 vs_4_1 ps_4_1), or similar | 0.3% | ❌ |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce 8800 GTX Direct3D11 vs_5_0 ps_5_0), or similar | 0.3% | ❌ |
| Google Inc. (Microsoft) | ANGLE (Microsoft, Microsoft Basic Render Driver Direct3D11 vs_5_0 ps_5_0) | 0.3% | ❌ |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce RTX 3080 Direct3D11 vs_5_0 ps_5_0, D3D11-27.21.14.6663) | 0.2% | ❌ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics 630 Direct3D11 vs_5_0 ps_5_0, D3D11-26.20.100.6861) | 0.2% | ❌ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) UHD Graphics 730 Direct3D11 vs_5_0 ps_5_0, D3D11-30.0.101.1340) | 0.2% | ❌ |
| Google Inc. (Intel) | ANGLE (Intel, Intel 945GM Direct3D11 vs_4_0 ps_4_0), or similar | 0.2% | ❌ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) Arc(TM) A750 Graphics Direct3D11 vs_5_0 ps_5_0), or similar | 0.2% | ❌ |
| Google Inc. (Unknown) | ANGLE (Unknown, Generic Renderer Direct3D11 vs_5_0 ps_5_0), or similar | 0.2% | ❌ |
| Google Inc. (AMD) | ANGLE (AMD, Radeon RX 570 Series Direct3D11 vs_5_0 ps_5_0, D3D11-30.0.14023.1009) | 0.2% | ❌ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) UHD Graphics 750 Direct3D11 vs_5_0 ps_5_0, D3D11) | 0.2% | ❌ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics 520 Direct3D11 vs_5_0 ps_5_0, D3D11-26.20.100.7263) | 0.2% | ❌ |

#### MacOS

*(Similar table structure for MacOS combinations - see website for full details)*

#### Linux

*(Similar table structure for Linux combinations - see website for full details)*

---

## End of Documentation

This documentation has been compiled from all available pages on camoufox.com as of November 9, 2025. All content has been re-scraped to ensure completeness and accuracy.
