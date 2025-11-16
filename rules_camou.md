# Camoufox Configuration Rules

## Installation

```bash
pip install -U "camoufox[geoip]"
camoufox fetch
```

## Configuration Parameters

### Core Settings
- **`humanize`**: `false` - Disable human-like cursor movements
- **`headless`**: `false` - Run browser in headed mode (visible window)
- **`os`**: `None` (default) - Let Camoufox auto-detect and use host OS for fingerprinting

### Privacy & Performance
- **`block_webrtc`**: `true` - Disable WebRTC entirely
- **`block_images`**: `true` - Block all image requests
- **`disable_coop`**: `true` - Disable Cross-Origin-Opener-Policy

### Location & Fingerprinting
- **`geoip`**: `true` - Auto-detect location, timezone, and locale from proxy IP
- **`locale`**: Auto-set via `geoip=True`, or manually specify (e.g., `"en-US"`)
- **`webgl_config`**: Use a valid `(vendor, renderer)` tuple to avoid suspicious/random values
  - Example: `webgl_config=("Intel Inc.", "Intel Iris OpenGL Engine")`

### Profile Persistence
- **`persistent_context`**: `true` - Enable persistent context for cookies, sessions, and addon state
- **`user_data_dir`**: Provide a directory path (e.g., `"./my_profile"`) for saving profile data

### Fingerprint Generation
- **BrowserForge Integration**: Camoufox automatically uses BrowserForge internally to generate realistic fingerprints that match real-world user distributions
- **Crowdblending**: Fingerprints blend into device, network, and rendering characteristics to avoid detection

## Proxy Configuration

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
```

## Complete Example

```python
from camoufox.sync_api import Camoufox

with Camoufox(
    # Core Settings
    humanize=False,
    headless=False,
    os=None,  # Auto-detect host OS
    
    # Privacy & Performance
    block_webrtc=True,
    block_images=True,
    disable_coop=True,
    
    # Location (auto-configured via proxy)
    geoip=True,
    
    # Profile Persistence
    persistent_context=True,
    user_data_dir="./my_profile",
    
    # WebGL (use valid pair)
    webgl_config=("Intel Inc.", "Intel Iris OpenGL Engine"),
    
    # Proxy Configuration
    proxy={
        'server': 'http://proxy.example.com:8080',
        'username': 'username',
        'password': 'password'
    }
) as browser:
    page = browser.new_page()
    page.goto("https://www.browserscan.net")
```

## Key Features Explained

### Host OS Detection
- Setting `os=None` (or omitting it) allows Camoufox to detect and match your actual host operating system
- This creates more realistic fingerprints aligned with your actual environment
- Alternatively, specify `os="windows"`, `os="macos"`, or `os="linux"` to spoof a different OS

### Automatic Fingerprint Generation (BrowserForge)
- Camoufox uses **BrowserForge** internally (no manual configuration needed)
- Generates coherent fingerprints with:
  - OS-correct fonts
  - Realistic screen dimensions
  - Valid hardware configurations
  - Proper navigator properties
- Fingerprints match real-world user distributions to avoid outlier detection

### GeoIP Auto-Configuration
- When `geoip=True` with a proxy, Camoufox automatically aligns:
  - **Timezone** - Matches proxy IP location
  - **Locale/Language** - Matches country/region
  - **Geolocation coordinates** - Approximate latitude/longitude
  - **WebRTC IPs** - Spoofed to match proxy (when WebRTC not blocked)

### Locale Behavior
- If `geoip=True`: Locale is automatically set based on proxy IP location
- To override: Specify `locale="en-US"` (or list of locales) explicitly
- GeoIP locale will be overridden by manual locale setting if both are provided
