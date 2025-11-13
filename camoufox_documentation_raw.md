# Camoufox Raw Documentation

## 1. Introduction

### An open source anti-detect browser with robust fingerprint injection. 

**Warning**: Camoufox is in active development! Releases are available, but may not be suitable for production use.

### Features
-   **Invisible to all anti-bot systems** 
    -   Camoufox performs better than most commercial anti-bot browsers.
-   **Fingerprint injection & rotation (without JS injection!)**
    -   All navigator properties (device, OS, hardware, browser, etc.) 
    -   Screen size, resolution, window, & viewport properties 
    -   Geolocation, timezone, locale, & Intl spoofing 
    -   WebRTC IP spoofing at the protocol level 
    -   Voices, speech playback rate, etc. 
    -   And much, much more!
-   **Anti Graphical fingerprinting**
    -   Canvas anti-fingerprinting 
    -   WebGL parameters, supported extensions, context attributes, & shader precision formats 
    -   Font spoofing & anti-fingerprinting 
-   **Quality of life features**
    -   Human-like mouse movement 
    -   Blocks & circumvents ads 
    -   No CSS animations 
-   Debloated & optimized for memory efficiency 
-   [PyPi package](https://pypi.org/project/camoufox/) for updates & auto fingerprint injection 
-   Stays up to date with the latest Firefox version 

### Design and Implementation
The goal of Camoufox is to provide a robust, undetectable anti-fingerprinting solution that blends in with regular user traffic.

**1. Fingerprinting Protection**
Privacy-focused browsers and add-ons often become counterproductive by making users more identifiable through detectable modifications or JavaScript inspection. This, ironically, makes the user stand out by creating unique fingerprints, making them more susceptible to be tracked or flagged by anti-bot systems.

Camoufox solves this by operating at a lower level, using fingerprint rotation and crowdblending techniques by modifying device information in the C++ implementation instead of injecting JavaScript, which leaves a trail. Built on Firefox and research from the Tor project, LibreWolf, and Arkenfox, Camoufox aims to avoid common user identification methods to provide a robust, undetectable anti-fingerprinting.

**2. Crowdblending**
A design goal of Camoufox is to blend in with real world traffic. It must behave like a normal user and avoid all statistical fingerprinting, meaning there should *not* be thousands of requests using the same device information on a large scale.

To achieve this, Camoufox uses [BrowserForge](https://github.com/daijro/browserforge) to rotate device information such as the screen, OS, and hardware to mimic the statistical distribution of device characteristics in real-world traffic. Additionally, Camoufox uses a natural mouse movement algorithm to behave like a normal user.

**3. Stealth**
Finally, Camoufox should avoid all bot detection, of any kind, by any means. It should not inject any JavaScript into the page or manipulate the DOM in any way.

In Camoufox, all of Playwright's internal Page Agent Javascript is sandboxed and isolated from the main world. This makes it **impossible** for a page to detect the presence of Playwright/Juggler. In addition, data is intercepted at the C++ implementation level, making the changes undetectable through JavaScript inspection.

**Why Firefox and not Chromium?**
Camoufox is built on top of Firefox instead of Chromium for these main reasons:
1.  Chrome is bundled with certain features that Chromium does not have. Anti-bot providers can detect if you are using Chromium rather than Chrome. Since Chrome is closed source, patching Chrome is significantly more difficult.
2.  CDP is more widely used and known, so it's a more common target for bot detection
3.  Juggler operates on a lower level than CDP, making it less prone to JS leaks.
4.  Firefox is more ideal for fingerprint rotation. More research has been made on Firefox for fingerprinting resistance than on Chromium.

**Is Camoufox open source?**
**Almost all** of Camoufox's code is publicly available on GitHub. However, certain features like the [Canvas fingerprint rotation](https://camoufox.com/fingerprint/canvas/) patch are closed source to prevent anti-bot providers from reverse engineering Camoufox. If you'd like to build Camoufox without the closed source features, please refer to the [build guide](https://camoufox.com/development/buildcli/).

**What's planned?**
-   Continue research on potential leaks
-   AI powered selector location
-   Remote hosting Camoufox as a Playwright server

---

## 2. Features List

Below is a list of patches and features implemented in Camoufox.

### Fingerprint spoofing
-   Navigator properties spoofing (device, browser, locale, etc.)
-   Support for emulating screen size, resolution, etc.
-   Spoof WebGL parameters, supported extensions, context attributes, and shader precision formats.
-   Spoof the CPU Canvas fingerprint by manipulating the anti-aliasing logic (closed source)
-   Spoof inner and outer window viewport sizes
-   Spoof AudioContext sample rate, output latency, and max channel count
-   Spoof device voices & playback rates
-   Spoof the amount of microphones, webcams, and speakers available.
-   Network headers (Accept-Languages and User-Agent) are spoofed to match the navigator properties
-   WebRTC IP spoofing at the protocol level
-   Geolocation, timezone, and locale spoofing
-   Battery API spoofing
-   etc.

### Stealth patches
-   Avoids main world execution leaks. All page agent javascript is sandboxed
-   Avoids frame execution context leaks
-   Fixes `navigator.webdriver` detection
-   Fixes Firefox headless detection via pointer type
-   Removed potentially leaking anti-zoom/meta viewport handling patches
-   Uses non-default screen & window sizes
-   Re-enable fission content isolations
-   Re-enable PDF.js
-   Other leaking config properties changed
-   Human-like cursor movement

### Anti font fingerprinting
-   Automatically uses the correct system fonts for your User Agent
-   Bundled with Windows, Mac, and Linux system fonts
-   Prevents font metrics fingerprinting by randomly offsetting letter spacing

### Playwright support
-   Custom implementation of Playwright for the latest Firefox
-   Various config patches to evade bot detection

### Debloat/Optimizations
-   Stripped out/disabled *many, many* Mozilla services. Runs faster than the original Mozilla Firefox, and uses less memory (200mb)
-   Patches from LibreWolf & Ghostery to help remove telemetry & bloat
-   Debloat config from PeskyFox, LibreWolf, and others
-   Speed & network optimizations from FastFox
-   Removed all CSS animations
-   Minimalistic theming
-   etc.

### Addons
-   Load Firefox addons without a debug server by passing a list of paths to the `addons` property
-   Added uBlock Origin with custom privacy filters
-   Addons are not allowed to open tabs
-   Addons are automatically enabled in Private Browsing mode
-   Addons are automatically pinned to the toolbar
-   Fixes DNS leaks with uBO prefetching

### Python Interface
-   Automatically generates & injects unique device characteristics into Camoufox based on their real-world distribution
-   WebGL fingerprint injection & rotation
-   Uses the correct system fonts and subpixel antialiasing & hinting based on your target OS
-   Avoid proxy detection by calculating your target geolocation, timezone, & locale from your proxy's target region
-   Calculate and spoof the browser's language based on the distribution of language speakers in the proxy's target region
-   Remote server hosting to use Camoufox with other languages that support Playwright
-   Built-in virtual display buffer to run Camoufox headfully on a headless server
-   Toggle image loading, WebRTC, and WebGL
-   etc.

---

## 3. Stealth Performance

In Camoufox, all of Playwright's internal Page Agent Javascript is sandboxed and isolated. This makes it **impossible** for a page to detect the presence of Playwright through Javascript inspection.

### Tests
Camoufox performs well against every major WAF I've tested.

| Test | Status | Notes |
| --- | --- | --- |
| [**CreepJS**](https://abrahamjuliot.github.io/creepjs/) | ✔️ | 71.5%. Successfully spoofs all OS predictions. |
| [**Rebrowser Bot Detector**](https://bot-detector.rebrowser.net/) | ✔️ | All tests pass. |
| [**BrowserScan**](https://browserscan.net/) | ✔️ | 100%. Spoofs all geolocation & locale proxy detection. |
| **reCaptcha Score** | ✔️ | |
| ‣ [nopecha.com](https://nopecha.com/demo/recaptcha) | ✔️ | |
| ‣ [recaptcha-demo.appspot.com](https://recaptcha-demo.appspot.com/recaptcha-v3-request-scores.php) | ✔️ | 0.9 |
| ‣ [berstend.github.io](https://berstend.github.io/static/recaptcha/v3-programmatic.html) | ✔️ | 0.9 |
| **DataDome** | ✔️ | |
| ‣ [DataDome bot bounty](https://yeswehack.com/programs/datadome-bot-bounty#program-description) | ✔️ | All test sites pass. |
| ‣ [hermes.com](https://www.hermes.com/us/en/) | ✔️ | |
| **Imperva** | ✔️ | |
| ‣ [ticketmaster.es](https://www.ticketmaster.es/) | ✔️ | |
| **Cloudflare** | ✔️ | |
| ‣ [Turnstile](https://nopecha.com/demo/turnstile) | ✔️ | |
| ‣ [Interstitial](https://nopecha.com/demo/cloudflare) | ✔️ | |
| **WebRTC IP Spoofing** | ✔️ | |
| ‣ [Browserleaks WebRTC](https://browserleaks.net/webrtc) | ✔️ | Spoofs public IP correctly. |
| ‣ [CreepJS WebRTC](https://abrahamjuliot.github.io/creepjs/) | ✔️ | Spoofs Host & STUN IP correctly. |
| ‣ [BrowserScan WebRTC](https://www.browserscan.net/webrtc) | ✔️ | Spoofs Host & STUN IP correctly. |
| **Font Fingerprinting** | ✔️ | |
| ‣ [Browserleaks Fonts](https://browserleaks.net/fonts) | ✔️ | Rotates all metrics. |
| ‣ [CreepJS TextMetrics](https://abrahamjuliot.github.io/creepjs/tests/fonts.html) | ✔️ | Rotates all metrics. |
| [**Incolumitas**](https://bot.incolumitas.com/) | ✔️ | 0.8-1.0 |
| [**SannySoft**](https://bot.sannysoft.com/) | ✔️ | |
| [**Fingerprint.com**](https://fingerprint.com/products/bot-detection/) | ✔️ | |
| [**IpHey**](https://iphey.com/) | ✔️ | |
| [**Bet365**](https://www.bet365.com/#/AC/B1/C1/D1002/E79147586/G40/) | ✔️ | |

**Warning**: Camoufox does **not** support injecting Chromium fingerprints. Some WAFs (such as [Interstitial](https://nopecha.com/demo/cloudflare)) test for Spidermonkey engine behavior, which is impossible to spoof.

---

## 4. Python Interface

#### Lightweight wrapper around the Playwright API to help launch Camoufox. 

Camoufox's Python library wraps around Playwright's API to help automatically generate & inject unique device characteristics into Camoufox such as the OS, device info, navigator, fonts, headers, screen/viewport size, addons, etc.

It uses [BrowserForge](https://github.com/daijro/browserforge) under the hood to generate fingerprints that mimic the statistical distribution of device characteristics in real-world traffic. In addition, it will also calculate your target geolocation, timezone, and locale to avoid proxy detection.

### 4.1. Installation

First, install the `camoufox` package:

```bash
pip install -U camoufox[geoip]
```
The `geoip` parameter is optional, but heavily recommended if you are using proxies. It will download an extra dataset to determine the user's longitude, latitude, timezone, country, & locale.

#### Download the browser
```bash
camoufox fetch
```
To uninstall, run `camoufox remove`.

#### Camoufox CLI
```
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

### 4.2. Usage

Camoufox is fully compatible with your existing Playwright code. You only have to change your browser initialization:

**Sync API**
```python
from camoufox.sync_api import Camoufox

with Camoufox() as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

**Async API**
```python
import asyncio
from camoufox.async_api import Camoufox

async def main():
    async with Camoufox() as browser:
        page = await browser.new_page()
        await page.goto("https://example.com")

asyncio.run(main())
```

### Parameters List

**All Playwright Firefox launch options are accepted, along with the following:**

#### Device Rotation
Camoufox will generate device information for you based on the following parameters.

- **`os`**
  **Type:** `Optional[ListOrString]`
  Operating system to use for the fingerprint generation. Can be `"windows"`, `"macos"`, `"linux"`, or a list to randomly choose from. By default, Camoufox will randomly choose from a list of all three.
- **`fonts`**
  **Type:** `Optional[List[str]]`
  Fonts to load into Camoufox, in addition to the default fonts for the target `os`. Takes a list of font family names that are installed on the system.
- **`screen`**
  **Type:** `Optional[Screen]`
  Constrains the screen dimensions of the generated fingerprint. Takes a `browserforge.fingerprints.Screen` instance.
- **`webgl_config`**
  **Type:** `Optional[Tuple[str, str]]`
  Use a specific WebGL vendor/renderer pair. Passed as a tuple of (vendor, renderer).

#### Configuration
Extra feature configuration and quality of life options.

- **`humanize`**
  **Type:** `Optional[Union[bool, float]]`
  Humanize the cursor movement. Takes either `True`, or the MAX duration in seconds of the cursor movement.
- **`headless`**
  **Type:** `Optional[Union[bool, Literal['virtual']]]`
  Whether to run the browser in headless mode. Defaults to False. If you are running linux, passing 'virtual' will use Xvfb.
- **`addons`**
  **Type:** `Optional[List[str]]`
  List of Firefox addons to use. Must be paths to extracted addons.
- **`exclude_addons`**
  **Type:** `Optional[List[DefaultAddons]]`
  Exclude the default addons. Passed as a list of `camoufox.DefaultAddons` enums.
- **`window`**
  **Type:** `Optional[Tuple[int, int]]`
  Set the window size in (width, height) pixels.
- **`main_world_eval`**
  **Type:** `Optional[bool]`
  Whether to inject scripts into the main world when prefixed with `mw:`.
- **`enable_cache`**
  **Type:** `Optional[bool]`
  Whether to cache previous pages, requests, etc.
- **`persistent_context`**
  **Type:** `Optional[bool]`
  Whether to create a persistent context. Requires `user_data_dir`.

#### Location & Language
Prevent proxy detection by matching your geolocation & locale with your target IP.

- **`geoip`**
  **Type:** `Optional[Union[str, bool]]`
  Calculates longitude, latitude, timezone, country, & locale based on the IP address. Pass the target IP address to use, or `True` to find the IP address automatically.
- **`locale`**
  **Type:** `Optional[Union[str, List[str]]]`
  Locale(s) to use in Camoufox. Can be a list of strings, or a single string separated by a comma.

#### Toggles
Shortcuts for common Firefox preferences and security toggles.

- **`block_images`**
  **Type:** `Optional[bool]`
  Blocks all requests to images.
- **`block_webrtc`**
  **Type:** `Optional[bool]`
  Blocks WebRTC entirely.
- **`block_webgl`**
  **Type:** `Optional[bool]`
  Whether to block WebGL.
- **`disable_coop`**
  **Type:** `Optional[bool]`
  Disables the Cross-Origin-Opener-Policy (COOP).

### 4.3. GeoIP & Proxy Support

By passing `geoip=True`, or passing in a target IP address, Camoufox will automatically use the target IP's longitude, latitude, timezone, country, locale, & spoof the WebRTC IP address. It will also calculate and spoof the browser's language based on the distribution of language speakers in the target region.

**Installation**: Install Camoufox with the `geoip` extra: `pip install -U "camoufox[geoip]"`

**Usage**:
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

### 4.4. Main World Execution

#### Reading the DOM - Isolated World
By default, all JavaScript execution is ran in an isolated scope, invisible to the page. This makes it impossible for the page to detect JavaScript reading the DOM. However, your JavaScript will **not** be able to modify the DOM.

#### Modifying the DOM - Main World
To be able to modify the DOM, run JavaScript in the **main world**.
**Leak Warning**: All code executed in the main world can be detected by the target website. Only execute JavaScript in the main world if absolutely necessary.

To enable, set `main_world_eval=True`. You can then inject JavaScript in the main world by prefixing the code with `mw:`
```python
page.evaluate("mw:document.querySelector('h1').remove()")
```

### 4.5. Remote Server

**Warning**: This feature is experimental.

Camoufox can be ran as a remote websocket server. It can be accessed from other devices, and languages other than Python supporting the Playwright API.

**Launching**:
```bash
python -m camoufox server
```
Or via a script:
```python
from camoufox.server import launch_server
launch_server(port=1234, ws_path='hello')
```

**Connecting**:
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.firefox.connect('ws://localhost:1234/hello')
    page = browser.new_page()
```

### 4.6. Virtual Display

It's recommended to use a virtual display buffer to run Camoufox headlessly. If you are running Linux, install `xvfb`:
```bash
# Debian-based
sudo apt-get install xvfb
# Arch-based
sudo pacman -S xorg-server-xvfb
```
**Usage**: Passing `headless="virtual"` will spawn a new virtual display.
```python
from camoufox.sync_api import Camoufox

with Camoufox(headless="virtual") as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

### 4.7. BrowserForge Integration

Camoufox is compatible with [BrowserForge](https://github.com/daijro/browserforge) fingerprints. By default, Camoufox will generate and use a random BrowserForge fingerprint based on `os` & `screen` constraints.
```python
from camoufox.sync_api import Camoufox
from browserforge.fingerprints import Screen

with Camoufox(
    os=('windows', 'macos', 'linux'),
    screen=Screen(max_width=1920, max_height=1080),
) as browser:
    page = browser.new_page()
```

### 4.8. Passing Config

If needed, individual Camoufox config properties can be overridden by passing them as a dictionary to the `config` parameter.
**Warning**: This isn't recommended, as Camoufox will populate this data for you automatically.
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

## 5. Fingerprint Injection

In Camoufox, data is intercepted at the C++ implementation level, making the changes undetectable through JavaScript inspection. To spoof properties, pass a JSON to the Python interface. Data not set by the user will be automatically populated using BrowserForge.

### 5.1. Navigator
Navigator properties can be fully spoofed. `navigator.webdriver` is set to `false` at all times.

| Property | Type |
| --- | --- |
| `navigator.userAgent` | str |
| `navigator.doNotTrack` | str |
| `navigator.appCodeName` | str |
| `navigator.appName` | str |
| `navigator.appVersion` | str |
| `navigator.oscpu` | str |
| `navigator.language` | str |
| `navigator.languages` | array |
| `navigator.platform` | str |
| `navigator.hardwareConcurrency` | uint |
| `navigator.product` | str |
| `navigator.productSub` | str |
| `navigator.maxTouchPoints` | uint |
| `navigator.cookieEnabled` | bool |
| `navigator.globalPrivacyControl` | bool |
| `navigator.buildID` | str |
| `navigator.onLine` | bool |

### 5.2. Cursor Movement

Camoufox has built-in support for human-like cursor movement.

| Property | Type | Description |
| --- | --- | --- |
| `humanize` | bool | Enable/disable human-like cursor movement. Defaults to False. |
| `humanize:maxTime` | double | Maximum time in seconds for the cursor movement. Defaults to `1.5`. |
| `humanize:minTime` | double | Minimum time in seconds for the cursor movement. |
| `showcursor` | bool | Toggles the cursor highlighter. Defaults to True. |

### 5.3. Fonts

Fonts can be passed via the `fonts` config property. Camoufox is bundled with default Windows 11, macOS Sonoma, and Linux fonts and will automatically use them based on the spoofed User-Agent.

| Property | Type | Description |
| --- | --- | --- |
| `fonts` | array | List of font names to be used |
| `fonts:spacing_seed` | uint | Seed for font metric spacing randomization |

**Font Metrics**: Camoufox prevents fingerprinting by font metrics & unicode glyphs by shifting the spacing of each letter by a random value between 0-0.1px.

### 5.4. Screen

Spoof the screen size, pixel depth, and position of the window.

| Property | Type |
| --- | --- |
| `screen.availHeight` | uint |
| `screen.availWidth` | uint |
| `screen.availTop` | uint |
| `screen.availLeft` | uint |
| `screen.height` | uint |
| `screen.width` | uint |
| `screen.colorDepth` | uint |
| `screen.pixelDepth` | uint |
| `screen.pageXOffset` | double |
| `screen.pageYOffset` | double |

### 5.5. Window

Spoof the inner/outer window size, scroll offsets, window position, and history length.

| Property | Type |
| --- | --- |
| `window.scrollMinX` | int |
| `window.scrollMinY` | int |
| `window.scrollMaxX` | int |
| `window.scrollMaxY` | int |
| `window.outerHeight` | uint |
| `window.outerWidth` | uint |
| `window.innerHeight` | uint |
| `window.innerWidth` | uint |
| `window.screenX` | int |
| `window.screenY` | int |
| `window.history.length` | uint |
| `window.devicePixelRatio`| double |

### 5.6. Document

Spoof the size of the HTML body element.

| Property | Type |
| --- | --- |
| `document.body.clientWidth` | uint |
| `document.body.clientHeight`| uint |
| `document.body.clientTop` | uint |
| `document.body.clientLeft` | uint |

### 5.7. Canvas

Most anti-detect browsers add detectable noise to the canvas. Camoufox implements Canvas anti-fingerprinting by using a patched build of Skia with modified subpixel rendering logic. This patch is closed source.

| Property | Type | Description |
| --- | --- | --- |
| `canvas:aaOffset` | int | Offset each pixel's transparency when drawing anti-aliasing. |
| `canvas:aaCapOffset`| bool | Clamp the offset to the 0-255 alpha range instead of wrapping. |

### 5.8. WebGL

Camoufox supports spoofing WebGL parameters, supported extensions, context attributes, and shader precision formats.
**Warning**: Do **NOT** randomly assign values to these properties. WAFs hash your WebGL fingerprint and compare it against a dataset.

| Property | Description |
| --- | --- |
| `webGl:renderer` | Spoofs the name of the unmasked WebGL renderer. |
| `webGl:vendor` | Spoofs the name of the unmasked WebGL vendor. |
| `webGl:supportedExtensions` | An array of supported WebGL extensions. |
| `webGl2:supportedExtensions`| The same as above, but for WebGL2. |
| `webGl:contextAttributes` | A dictionary of WebGL context attributes. |
| `webGl2:contextAttributes`| The same as above, but for WebGL2. |
| `webGl:parameters` | A dictionary of WebGL parameters (keys are GL enums). |
| `webGl2:parameters` | The same as above, but for WebGL2. |
| `webGl:shaderPrecisionFormats` | A dictionary of WebGL shader precision formats. |
| `webGl2:shaderPrecisionFormats`| The same as above, but for WebGL2. |

### 5.9. Geolocation & Intl

| Property | Type | Required Keys |
| --- | --- | --- |
| `geolocation:latitude` | double | `geolocation:longitude` |
| `geolocation:longitude` | double | `geolocation:latitude` |
| `geolocation:accuracy` | double | |
| `timezone` | str | |
| `locale:language` | str | `locale:region` |
| `locale:region` | str | `locale:language` |
| `locale:script` | str | |
| `locale:all` | str | |

### 5.10. HTTP Headers

Camoufox can override the following network headers.

| Property | Type |
| --- | --- |
| `headers.User-Agent` | str |
| `headers.Accept-Language`| str |
| `headers.Accept-Encoding`| str |

### 5.11. WebRTC IP

Camoufox implements WebRTC IP spoofing at the protocol level.

| Property | Type |
| --- | --- |
| `webrtc:ipv4` | str |
| `webrtc:ipv6` | str |

### 5.12. Media & Audio

#### Media Devices
Spoof the amount of microphones, webcams, and speakers available.

| Property | Type | Default |
| --- | --- | --- |
| `mediaDevices:enabled` | bool | `false` |
| `mediaDevices:micros` | uint | `3` |
| `mediaDevices:webcams` | uint | `1` |
| `mediaDevices:speakers` | uint | `1` |

#### AudioContext
Spoof the AudioContext sample rate, output latency, and max channel count.

| Property | Type |
| --- | --- |
| `AudioContext:sampleRate` | uint |
| `AudioContext:outputLatency` | double |
| `AudioContext:maxChannelCount` | uint |

### 5.13. Voices

Spoof the voices used in the browser's Web Speech API.

| Property | Type | Description |
| --- | --- | --- |
| `voices` | array | An array of maps for each voice. |
| `voices:blockIfNotDefined` | bool | Whether to block system voices. |
| `voices:fakeCompletion` | bool | Return a successful TTS completion when a fake voice is used. |
| `voices:fakeCompletion:charsPerSecond` | double | Playback rate of a fake voice. |

### 5.14. Addons

Addons can be loaded with the `addons` parameter in the Python library. Camoufox takes extracted addons (`.xpi` files should be extracted to a folder). By default, Camoufox includes uBlock Origin. You can exclude default addons with the `exclude_addons` parameter.

### 5.15. Miscellaneous

Spoof battery status and PDF viewer.
**Note**: It is recommended to keep `pdfViewerEnabled` on, as many websites flag its absence as a headless browser.

| Property | Type |
| --- | --- |
| `pdfViewerEnabled` | bool |
| `battery:charging` | bool |
| `battery:chargingTime` | double |
| `battery:dischargingTime` | double |
| `battery:level` | double |

---

## 6. Development

### 6.1. Build System

The build system is based on LibreWolf's and uses `make` commands to fetch the Firefox source, apply patches (fingerprint masking, debloating, addons), and package portable versions for Linux, Windows, and macOS.

### 6.2. Building in CLI

**Warning**: Camoufox's build system is designed to be used in Linux. WSL will not work.
1.  Clone the repository: `git clone --depth 1 https://github.com/daijro/camoufox && cd camoufox`
2.  Create directory structure: `make dir`
3.  Bootstrap the system (one time only): `make bootstrap`
4.  Build and package: `python3 multibuild.py --target linux windows macos --arch x86_64 arm64 i686`

### 6.3. Building in Docker

To build Camoufox on a non-Linux system you can use Docker.
1.  Create the Docker image: `docker build -t camoufox-builder .`
2.  Build for a target platform and architecture: `docker run -v "$(pwd)/dist:/app/dist" camoufox-builder --target <os> --arch <arch>`

### 6.4. Development Tools

The repo includes a developer UI for managing patches (`make edits`).
-   **To make a patch**: Reset the workspace, make changes in the `camoufox-*/` folder, test with `make build` and `make run`, then write the workspace to a new patch file.
-   **To work on an existing patch**: Select "Edit a patch", make your changes, and then overwrite the existing patch file.

### 6.5. Leak Debugging

A flowchart and process for determining leaks without deobfuscating WAF Javascript is provided. The method incrementally reintroduces Camoufox's features into Firefox's source code until a testing site flags the browser.
**Cited Commands**:
-   `(1) make ff-dbg`: Setup vanilla Firefox with minimal patches.
-   `(2) make build`: Build the source code.
-   `(3) make run`: Runs the built browser.
-   `(4) make run args="--headless https://test.com"`: Run a URL in headless mode.
-   `(5) make edits`: Opens the developer UI.
-   `(6) make edit-cfg`: Edit camoufox.cfg.

---

## 7. WebGL Research

As of January 2025, a newer fingerprint generation library with WebGL is in the works. The following is a list of possible WebGL vendor & renderer combinations in Firefox. Items checked off indicate that Camoufox has sufficient analytics for accurate simulation.

**Last Updated**: December 05, 2024.

| Vendor | Renderer | Probability | Sufficient |
| --- | --- | --- | --- |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce GTX 980...), or similar | 29.0% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics...), or similar | 24.1% | ✅ |
| Google Inc. (Intel) | ANGLE (Intel, Intel(R) HD Graphics 400...), or similar | 19.7% | ✅ |
| Google Inc. (AMD) | ANGLE (AMD, Radeon HD 3200 Graphics...), or similar | 10.6% | ✅ |
| Google Inc. (AMD) | ANGLE (AMD, Radeon R9 200 Series...), or similar | 6.6% | ✅ |
| Google Inc. (NVIDIA) | ANGLE (NVIDIA, NVIDIA GeForce GTX 480...), or similar | 1.7% | ✅ |
| Google Inc. (Microsoft) | ANGLE (Microsoft, Microsoft Basic Render Driver...), or similar | 1.6% | ✅ |
| ...and many others | ... | ... | ... |
| Google Inc. (AMD) | ANGLE (AMD, Radeon HD 5850...), or similar | 0.5% | ❌ |
| Intel Inc. | Intel Iris OpenGL Engine | 0.5% | ❌ |
| ...and many others | ... | ... | ... |

---

## 8. Liability Disclaimer & Donation

### Liability Disclaimer
Camoufox does **NOT** provide **liability or warranty**. It is intended for ethical and research purposes only. The developers cannot be held liable for any damages that result from its use. By using Camoufox, you agree not to violate laws, engage in illegal activity, or use the software for fraudulent purposes.

---

## Appendix A: Fingerprintable Properties Tree

This is the comprehensive list of browser properties that can be fingerprinted and are targeted by Camoufox for spoofing.

-   browser
-   navigator.appName
-   navigator.product
-   navigator.productSub
-   navigator.appCodeName
-   platformInfo
-   os
-   navigator.userAgent
-   gpu
-   webgl
-   webgl2
-   gpuInfo.adapterInfo
-   battery
-   navigator.deviceMemory
-   navigator.hardwareConcurrency
-   drm
-   mimeTypes
-   permissions.microphone
-   devices.audioinput
-   permissions.camera
-   devices.videoinput
-   permissions.speaker
-   devices.audiooutput
-   permissions.bluetooth
-   permissions.midi
-   plugins.plugins
-   plugins.mimeTypes
-   rtcCapabilities.video
-   rtcCapabilities.audio
-   audio.signals
-   audio.values
-   location.country
-   location.region
-   intl
-   languages
-   navigator.language
-   navigator.languages
-   headers.accept-language
-   voices
-   matchMedia.mediaCss.pointer
-   matchMedia.mediaCss.any-pointer
-   matchMedia.mediaCss.hover
-   matchMedia.mediaCss.any-hover
-   screen
-   matchMedia.screenQuery.width
-   matchMedia.screenQuery.height
-   matchMedia.mediaCss.device-aspect-ratio
-   matchMedia.mediaCss.device-screen
-   matchMedia.mediaCss.orientation
-   window
-   windowComponents
-   systemStyles.fonts
-   allFonts
-   document
-   documentPolicyFeatures
-   cast
-   client
-   navigator.platform
-   navigator.appVersion
-   navigator.doNotTrack
-   navigator.oscpu
-   navigator.maxTouchPoints
-   memory.jsHeapSizeLimit
-   matchMedia.mediaCss.color-gamut
-   system_styles.colors
-   matchMedia.mediaCss.display-mode
-   matchMedia.mediaCss.forced-colors
-   matchMedia.mediaCss.inverted-colors
-   matchMedia.mediaCss.monochrome
-   matchMedia.mediaCss.prefers-color-scheme
-   matchMedia.mediaCss.prefers-reduced-motion
-   permissions.accelerometer
-   permissions.accessibility-events
-   permissions.background-fetch
-   permissions.background-sync
-   permissions.periodic-background-sync
-   permissions.clipboard
-   permissions.clipboard-read
-   permissions.clipboard-write
-   permissions.device-info
-   permissions.display-capture
-   permissions.font-access
-   permissions.geolocation
-   permissions.gyroscope
-   permissions.idle-detection
-   permissions.magnetometer
-   permissions.nfc
-   permissions.notifications
-   permissions.payment-handler
-   permissions.persistent-storage
-   permissions.push
-   permissions.screen-wake-lock
-   permissions.storage-access
-   permissions.system-wake-lock
-   permissions.window-placement
-   network.headers_fp
-   network.tls.scrapfly_fp
-   network.http2.fingerprint
-   network.http2.http2_frames
-   headers.accept-encoding
-   headers.accept
-   headers.priority
-   headers.sec-ch-ua
-   headers.sec-ch-ua-mobile
-   headers.sec-ch-ua-platform
-   headers.sec-fetch-dest
-   headers.sec-fetch-mode
-   headers.sec-fetch-site
-   headers.sec-gpc
-   headers.user-agent
