# Dolphin Anty - Documentation

This document contains all documentation extracted from Dolphin Anty official documentation and API references.

**Last Updated:** November 12, 2025

**Source:** https://help.dolphin-anty.com/ and https://documenter.getpostman.com/view/15402503/Tzm8Fb5f

**Documentation:** https://help.dolphin-anty.com/

**API Documentation:** https://documenter.getpostman.com/view/15402503/Tzm8Fb5f

---

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [API Overview](#api-overview)
4. [Browser Profiles](#browser-profiles)
5. [Proxy Management](#proxy-management)
6. [Cookies](#cookies)
7. [Fingerprints](#fingerprints)
8. [Extensions](#extensions)
9. [Automation](#automation)
10. [Best Practices](#best-practices)
11. [Troubleshooting](#troubleshooting)

---

## Introduction

Dolphin Anty is an anti-detect browser designed for affiliate marketing, traffic arbitrage, and multi-accounting. It allows you to create and manage multiple browser profiles with unique fingerprints, proxies, and configurations.

### Key Features

- **Anti-Detect Technology**: Unique browser fingerprints for each profile
- **Proxy Management**: Support for HTTP, SOCKS5, and SOCKS4 proxies
- **Automation**: API-based automation with Chrome DevTools Protocol
- **Cookie Management**: Import and export cookies
- **Extension Support**: Add Chrome extensions to profiles
- **Team Collaboration**: Share profiles and manage team access
- **Fingerprint Management**: Customize WebGL, Canvas, WebRTC, and more

### Use Cases

- Affiliate marketing
- Traffic arbitrage
- Multi-accounting
- Social media management
- E-commerce operations
- Web scraping with unique identities

---

## Getting Started

### Installation

1. Download Dolphin Anty from https://dolphin-anty.com/
2. Install the application
3. Create an account or sign in
4. Generate API token from Personal Area → API

### API Authentication

**Getting API Token:**
1. Go to https://dolphin-anty.com/panel/#/api
2. Fill name, select expiration days
3. Click "Create token"
4. Copy the token immediately (you won't see it again)

**Using API Token:**
Include the token in the Authorization header as Bearer token:

```bash
Authorization: Bearer YOUR_API_TOKEN
```

**Request Limit:** 500 requests per minute

---

## API Overview

### Base URLs

- **Remote API:** `https://dolphin-anty-api.com`
- **Local API:** `http://localhost:3001/v1.0`
- **Sync API:** `https://sync.dolphin-anty-api.com`

### Authentication

All API requests require Bearer token authentication:

```bash
curl --location 'https://dolphin-anty-api.com/browser_profiles' \
  --header 'Authorization: Bearer YOUR_API_TOKEN'
```

### Rate Limits

- **Request Limit:** 500 requests per minute
- Exceeding limit returns 429 Too Many Requests

### Response Format

Most endpoints return JSON:

```json
{
  "success": 1,
  "data": { ... }
}
```

---

## Browser Profiles

### List Browser Profiles

**Endpoint:** `GET /browser_profiles`

**Parameters:**
- `limit` (number, optional): Items per page (max 50, default 50)
- `query` (string, optional): Text search by profile name
- `tags[]` (array, optional): Filter by tags (AND mode)
- `statuses[]` (array, optional): Filter by status IDs
- `mainWebsites[]` (array, optional): Filter by main website (`['facebook', 'google', 'crypto', 'tiktok']`)
- `users[]` (array, optional): Filter by user IDs
- `page` (number, optional): Page number

**Example:**
```python
import requests

url = "https://dolphin-anty-api.com/browser_profiles"
headers = {
    "Authorization": "Bearer YOUR_API_TOKEN"
}
params = {
    "limit": 50,
    "query": "test",
    "tags[]": ["facebook", "italy"],
    "page": 1
}

response = requests.get(url, headers=headers, params=params)
profiles = response.json()
```

### Get One Browser Profile

**Endpoint:** `GET /browser_profiles/:browser_profile_id`

**Example:**
```python
profile_id = 76854661
url = f"https://dolphin-anty-api.com/browser_profiles/{profile_id}"
response = requests.get(url, headers=headers)
profile = response.json()
```

### Create Browser Profile

**Endpoint:** `POST /browser_profiles`

**Required Parameters:**
- `name` (string): Browser profile name
- `platform` (string): OS to emulate (`'windows'`, `'linux'`, `'macos'`)
- `browserType` (string): Browser type (`'anty'`)
- `useragent[mode]` (string): User agent mode (`'manual'`)
- `useragent[value]` (string): Manual user agent string
- `webrtc[mode]` (string): WebRTC mode (`'off'`, `'real'`, `'altered'`, `'manual'`, `'udpDisabled'`)
- `canvas[mode]` (string): Canvas mode (`'off'`, `'real'`, `'noise'`)
- `webgl[mode]` (string): WebGL mode (`'off'`, `'real'`, `'noise'`)

**Optional Parameters:**
- `tags[]` (array): Array of tags
- `mainWebsite` (string): Main website (`'google'`, `'facebook'`, `'crypto'`, `'tiktok'`)
- `platformVersion` (string): OS version (e.g., `'10.0.0'` for Windows 10)
- `timezone[mode]` (string): Timezone mode (`'auto'`, `'manual'`)
- `timezone[value]` (string): Timezone (e.g., `'Europe/Moscow'`)
- `locale[mode]` (string): Locale mode (`'auto'`, `'manual'`)
- `locale[value]` (string): Locale (e.g., `'en_GB'`)
- `geolocation[mode]` (string): Geolocation mode (`'auto'`, `'manual'`)
- `geolocation[latitude]` (number): Latitude
- `geolocation[longitude]` (number): Longitude
- `cpu[mode]` (string): CPU mode (`'real'`, `'manual'`)
- `cpu[value]` (number): CPU cores (`2`, `4`, `8`, `16`)
- `memory[mode]` (string): Memory mode (`'real'`, `'manual'`)
- `memory[value]` (number): RAM in GB (`2`, `4`, `8`)
- `proxy[id]` (number): Existing proxy ID
- `proxy[type]` (string): Proxy type (`'http'`, `'socks5'`, `'socks4'`)
- `proxy[host]` (string): Proxy host
- `proxy[port]` (string): Proxy port
- `proxy[login]` (string): Proxy login
- `proxy[password]` (string): Proxy password

**Example:**
```python
data = {
    "name": "Test Profile",
    "platform": "windows",
    "browserType": "anty",
    "useragent": {
        "mode": "manual",
        "value": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
    },
    "webrtc": {
        "mode": "altered",
        "ipAddress": None
    },
    "canvas": {
        "mode": "real"
    },
    "webgl": {
        "mode": "real"
    },
    "tags": ["test", "facebook"],
    "mainWebsite": "facebook"
}

response = requests.post(
    "https://dolphin-anty-api.com/browser_profiles",
    headers=headers,
    json=data
)
result = response.json()
profile_id = result["browserProfileId"]
```

### Update Browser Profile

**Endpoint:** `PATCH /browser_profiles/:browser_profile_id`

**Parameters:** Same as create, but all optional

**Example:**
```python
profile_id = 76854661
data = {
    "name": "Updated Profile Name",
    "tags": ["updated", "test"]
}

response = requests.patch(
    f"https://dolphin-anty-api.com/browser_profiles/{profile_id}",
    headers=headers,
    json=data
)
```

### Delete Browser Profile

**Endpoint:** `DELETE /browser_profiles/:browser_profile_id?forceDelete=1`

**Parameters:**
- `forceDelete` (number): `1` to delete permanently (required on Free plan)

**Example:**
```python
profile_id = 76854661
url = f"https://dolphin-anty-api.com/browser_profiles/{profile_id}?forceDelete=1"
response = requests.delete(url, headers=headers)
```

### Multiple Create Browser Profiles

**Endpoint:** `POST /browser_profiles/mass`

**Body:**
```json
{
    "common": {
        "platform": "macos",
        "browserType": "anty",
        "useragent": { ... },
        "webrtc": { ... },
        "canvas": { ... },
        "webgl": { ... }
    },
    "items": [
        {
            "name": "Profile 1",
            "tags": ["test"],
            "mainWebsite": ""
        },
        {
            "name": "Profile 2",
            "tags": ["test"],
            "mainWebsite": ""
        }
    ]
}
```

---

## Proxy Management

### List Proxies

**Endpoint:** `GET /proxy`

**Parameters:**
- `limit` (number, optional): Items per page (max 50, default 50)
- `page` (number, optional): Page number
- `query` (string, optional): Search query

**Example:**
```python
url = "https://dolphin-anty-api.com/proxy"
params = {"limit": 50, "page": 1}
response = requests.get(url, headers=headers, params=params)
proxies = response.json()
```

### Create Proxy

**Endpoint:** `POST /proxy`

**Parameters:**
- `type` (string, required): Proxy type (`'http'`, `'socks5'`, `'socks4'`)
- `host` (string, required): Proxy host
- `port` (string, required): Proxy port
- `login` (string, optional): Proxy login
- `password` (string, optional): Proxy password
- `name` (string, optional): Proxy name
- `changeIpUrl` (string, optional): Change IP URL

**Example:**
```python
data = {
    "type": "http",
    "host": "81.217.5.11",
    "port": "11111",
    "login": "username",
    "password": "password",
    "name": "My Proxy"
}

response = requests.post(
    "https://dolphin-anty-api.com/proxy",
    headers=headers,
    json=data
)
proxy = response.json()
```

### Update Proxy

**Endpoint:** `PATCH /proxy/:proxy_id`

**Parameters:** Same as create

### Delete Proxy

**Endpoint:** `DELETE /proxy/:proxy_id`

---

## Cookies

### Import Cookies

**Endpoint:** `POST http://localhost:3001/v1.0/cookies/import`

**Note:** Does not work on completely free plans (requires subscription)

**Body:**
```json
{
    "cookies": [
        {
            "domain": ".example.com",
            "name": "session",
            "value": "abc123",
            "path": "/",
            "secure": true,
            "httpOnly": true,
            "sameSite": "Lax"
        }
    ],
    "profileId": 1,
    "transfer": 0,
    "cloudSyncDisabled": false
}
```

**Example:**
```python
url = "http://localhost:3001/v1.0/cookies/import"
data = {
    "cookies": [...],
    "profileId": 76854661,
    "transfer": 0,
    "cloudSyncDisabled": False
}

response = requests.post(url, headers=headers, json=data)
```

### Export Cookies

**Endpoint:** `POST http://localhost:3001/v1.0/export-cookies`

**Note:** Does not work on completely free plans (requires subscription)

**Body:**
```json
{
    "browserProfiles": [
        {
            "transfer": 0,
            "id": 76854684,
            "name": "test 19"
        }
    ]
}
```

### Export Cookies for Single Profile

**Endpoint:** `GET https://sync.dolphin-anty-api.com/?actionType=getCookies&browserProfileId=:browser_profile_id`

**Example:**
```python
profile_id = 76854661
url = f"https://sync.dolphin-anty-api.com/?actionType=getCookies&browserProfileId={profile_id}"
response = requests.get(url, headers=headers)
cookies = response.json()
```

---

## Fingerprints

### Get New User Agent

**Endpoint:** `GET /fingerprints/useragent`

**Parameters:**
- `browser_type` (string, required): `'anty'`
- `browser_version` (number, optional): Chrome version (if not specified, latest)
- `platform` (string, required): `'windows'`, `'linux'`, `'macos'`

**Example:**
```python
url = "https://dolphin-anty-api.com/fingerprints/useragent"
params = {
    "browser_type": "anty",
    "browser_version": 101,
    "platform": "windows"
}
response = requests.get(url, headers=headers, params=params)
useragent = response.json()
```

### Get New Fingerprints for WebGL Info

**Endpoint:** `GET /fingerprints/fingerprint`

**Parameters:**
- `platform` (string, required): `'windows'`, `'linux'`, `'macos'`
- `browser_type` (string, required): `'anty'`
- `browser_version` (number, required): Chrome version
- `type` (string, required): `'fingerprint'`
- `screen` (string, optional): Screen resolution (e.g., `'2560x1440'`)

**Example:**
```python
url = "https://dolphin-anty-api.com/fingerprints/fingerprint"
params = {
    "platform": "linux",
    "browser_type": "anty",
    "browser_version": 112,
    "type": "fingerprint",
    "screen": "2560x1440"
}
response = requests.get(url, headers=headers, params=params)
fingerprint = response.json()
```

---

## Extensions

### Add Extension from Chrome Store

**Endpoint:** `POST /extensions`

**Body:**
```json
{
    "url": "https://chrome.google.com/webstore/detail/extension-id",
    "sharedToEntireTeam": true,
    "mainWebsite": ["google"]
}
```

**Example:**
```python
data = {
    "url": "https://chrome.google.com/webstore/detail/google-translate/aapbdbdomjkkjkaonfhkkikfgjllcleb",
    "sharedToEntireTeam": True,
    "mainWebsite": ["google"]
}

response = requests.post(
    "https://dolphin-anty-api.com/extensions",
    headers=headers,
    json=data
)
```

### Add Extension from ZIP

**Endpoint:** `POST /extensions/upload-zipped`

**Body:** Form data with file upload

**Parameters:**
- `file` (file, required): ZIP file containing extension
- `mainWebsite[]` (array, required): Main websites (`'all'`, `'facebook'`, `'google'`, `'tiktok'`, `'crypto'`)
- `sharedToEntireTeam` (number, required): `0` or `1`
- `extensionName` (string, required): Extension name

**Example:**
```python
files = {
    'file': ('extension.zip', open('extension.zip', 'rb'), 'application/zip')
}
data = {
    'mainWebsite[]': ['all'],
    'sharedToEntireTeam': '0',
    'extensionName': 'My Extension'
}

response = requests.post(
    "https://dolphin-anty-api.com/extensions/upload-zipped",
    headers=headers,
    files=files,
    data=data
)
```

### Delete Extensions

**Endpoint:** `DELETE /extensions`

**Body:**
```json
{
    "ids": [235669, 246828]
}
```

---

## Automation

### Start Profile via API

**Endpoint:** `GET http://localhost:3001/v1.0/browser_profiles/:browser_profile_id/start`

**Parameters:**
- `automation` (number, optional): `1` to enable automation
- `headless` (number, optional): `1` to run in headless mode

**Example:**
```python
profile_id = 76854661
url = f"http://localhost:3001/v1.0/browser_profiles/{profile_id}/start"
params = {
    "automation": 1,
    "headless": 1
}
response = requests.get(url, params=params)
```

**Note:** 
- Dolphin Anty is not supported on OS without GUI
- Headless mode runs profile without interface
- Automation is not available on Free plan

### Connect via Chrome DevTools Protocol

After starting a profile via API, connect using Chrome DevTools Protocol:

1. Get WebSocket URL from API response
2. Connect using Chrome DevTools Protocol
3. Use Selenium, Puppeteer, or Playwright

**Example with Selenium:**
```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

# Profile started via API returns WebSocket URL
ws_url = "ws://localhost:3001/devtools/browser/..."

chrome_options = Options()
chrome_options.add_experimental_option("debuggerAddress", "localhost:3001")

driver = webdriver.Chrome(options=chrome_options)
driver.get("https://example.com")
```

---

## Best Practices

### 1. Profile Management

- Use tags to organize profiles
- Set appropriate mainWebsite for better fingerprint matching
- Regularly update browser versions
- Use unique fingerprints for each profile

### 2. Proxy Management

- Test proxies before assigning to profiles
- Use residential proxies for better success rates
- Rotate proxies regularly
- Monitor proxy health

### 3. Fingerprint Management

- Use `'altered'` WebRTC mode for most cases
- Set `'real'` canvas and WebGL for better compatibility
- Match timezone and locale to proxy location
- Use appropriate screen resolutions

### 4. Automation

- Always check profile status before automation
- Use headless mode for server environments
- Implement retry logic for failed operations
- Monitor automation sessions

### 5. Error Handling

```python
import requests
import time

def make_request_with_retry(url, headers, max_retries=3):
    for attempt in range(max_retries):
        try:
            response = requests.get(url, headers=headers, timeout=30)
            if response.status_code == 200:
                return response.json()
            elif response.status_code == 429:
                # Rate limited
                wait_time = 2 ** attempt
                print(f"Rate limited. Waiting {wait_time}s...")
                time.sleep(wait_time)
                continue
            else:
                response.raise_for_status()
        except requests.exceptions.RequestException as e:
            if attempt == max_retries - 1:
                raise
            time.sleep(2 ** attempt)
    
    raise Exception("Max retries exceeded")
```

---

## Troubleshooting

### Common Issues

#### "Automation is not possible on the Free plan"

**Solution:**
- Upgrade to a paid plan
- Free plan does not support automation
- See https://help.dolphin-anty.com/en/articles/10385548-tariff-plans

#### "Unauthorized" Error

**Solution:**
1. Verify Bearer token is included in Authorization header
2. Check token is still valid
3. Generate new token if needed
4. Ensure token format is correct: `Bearer YOUR_TOKEN`

#### Profile Won't Start

**Solution:**
1. Check profile was created correctly via API
2. Verify all required parameters are set
3. Check profile status
4. Ensure Dolphin Anty application is running
5. Verify profile is not already running

#### Cookies Import/Export Not Working

**Solution:**
1. Verify you have a paid subscription (not available on Free plan)
2. Check cookie format is correct
3. Ensure profile ID is valid
4. Verify profile is not running during import

#### API Rate Limiting

**Solution:**
1. Implement exponential backoff
2. Reduce request frequency
3. Batch operations when possible
4. Monitor request count

### Getting Help

- **Help Center:** https://help.dolphin-anty.com/
- **API Documentation:** https://documenter.getpostman.com/view/15402503/Tzm8Fb5f
- **Basic Automation Guide:** https://help.dolphin-anty.com/en/articles/7127390-basic-automation-dolphin-anty

---

## Additional Resources

- **Dolphin Anty Website:** https://dolphin-anty.com/
- **Help Center:** https://help.dolphin-anty.com/
- **API Documentation:** https://documenter.getpostman.com/view/15402503/Tzm8Fb5f
- **Personal Area:** https://dolphin-anty.com/panel/

---

*End of Documentation*








