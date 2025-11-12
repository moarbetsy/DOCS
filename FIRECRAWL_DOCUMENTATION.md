# Firecrawl - Documentation

This document contains all documentation extracted from Firecrawl MCP server and official Firecrawl documentation.

**Last Updated:** November 12, 2025

**Source:** https://docs.firecrawl.dev/

**Documentation:** https://docs.firecrawl.dev/

---

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Scraping](#scraping)
4. [Search](#search)
5. [Mapping](#mapping)
6. [Crawling](#crawling)
7. [Extraction](#extraction)
8. [API Reference](#api-reference)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)

---

## Introduction

Firecrawl is a powerful web scraping and crawling API designed for AI agents and builders. It provides tools for scraping single pages, searching the web, mapping websites, crawling entire sites, and extracting structured data.

### Key Features

- **Fast and Reliable**: Built for scale with high performance
- **AI-Optimized**: Designed specifically for AI agents and applications
- **Multiple Formats**: Supports markdown, HTML, JSON, and more
- **Advanced Options**: Custom actions, parsers, and extraction schemas
- **Caching**: Built-in caching for faster repeated requests

### Use Cases

- Web scraping and data extraction
- Content aggregation
- Website monitoring
- SEO analysis
- Data collection for AI training
- Research and analysis

---

## Getting Started

### Installation

Firecrawl is available as an MCP server. To use it, configure it in your MCP settings:

```json
{
  "mcpServers": {
    "firecrawl": {
      "command": "npx",
      "args": ["-y", "@firecrawl/mcp-server-firecrawl"]
    }
  }
}
```

### Authentication

Firecrawl requires an API key for authentication. Set it as an environment variable:

```bash
export FIRECRAWL_API_KEY="your-api-key-here"
```

Or configure it in your MCP server settings.

---

## Scraping

### Scrape Single URL

Scrape content from a single URL with advanced options.

**MCP Function:** `mcp_firecrawl_scrape`

**Best for:** Single page content extraction when you know exactly which page contains the information.

**Parameters:**
- `url` (string, required): The URL to scrape
- `formats` (array, optional): Output formats (markdown, html, rawHtml, screenshot, links, summary, etc.)
- `onlyMainContent` (boolean, optional): Extract only main content
- `maxAge` (number, optional): Use cached data if available (milliseconds)
- `waitFor` (number, optional): Wait time in milliseconds
- `removeBase64Images` (boolean, optional): Remove base64 encoded images
- `mobile` (boolean, optional): Use mobile user agent
- `actions` (array, optional): Custom actions (click, scroll, wait, etc.)

**Example Usage:**

```python
# Basic scrape
result = mcp_firecrawl_scrape(
    url="https://example.com",
    formats=["markdown"]
)

# Scrape with caching
result = mcp_firecrawl_scrape(
    url="https://example.com",
    formats=["markdown"],
    maxAge=172800000  # 2 days in milliseconds
)

# Scrape with actions
result = mcp_firecrawl_scrape(
    url="https://example.com",
    formats=["markdown"],
    actions=[
        {"type": "wait", "milliseconds": 2000},
        {"type": "scroll", "direction": "down"},
        {"type": "click", "selector": "button.submit"}
    ]
)

# Scrape with screenshot
result = mcp_firecrawl_scrape(
    url="https://example.com",
    formats=["screenshot"],
    screenshot={"fullPage": True, "quality": 90}
)
```

**Supported Formats:**
- `markdown`: Clean markdown format
- `html`: HTML content
- `rawHtml`: Raw HTML source
- `screenshot`: Page screenshot
- `links`: Extract all links
- `summary`: AI-generated summary
- `json`: Custom JSON extraction with schema

**Performance Tip:** Add `maxAge` parameter for 500% faster scrapes using cached data.

---

## Search

### Web Search

Search the web and optionally extract content from search results.

**MCP Function:** `mcp_firecrawl_search`

**Best for:** Finding specific information across multiple websites when you don't know which website has the information.

**Parameters:**
- `query` (string, required): Search query
- `limit` (number, optional): Maximum number of results
- `sources` (array, optional): Source types (web, images, news)
- `scrapeOptions` (object, optional): Options for scraping search results
- `location` (string, optional): Geographic location for search
- `tbs` (string, optional): Time-based search (e.g., "qdr:d" for past day)

**Search Operators:**
- `""` - Non-fuzzy matches (e.g., `"Firecrawl"`)
- `-` - Excludes keywords (e.g., `-bad`, `-site:firecrawl.dev`)
- `site:` - Only results from specified website (e.g., `site:firecrawl.dev`)
- `inurl:` - Results with word in URL (e.g., `inurl:firecrawl`)
- `intitle:` - Results with word in title (e.g., `intitle:Firecrawl`)

**Example Usage:**

```python
# Basic search
results = mcp_firecrawl_search(
    query="web scraping API",
    limit=5,
    sources=["web"]
)

# Search with scraping
results = mcp_firecrawl_search(
    query="latest AI research papers 2023",
    limit=5,
    sources=["web"],
    scrapeOptions={
        "formats": ["markdown"],
        "onlyMainContent": True
    }
)

# Search with filters
results = mcp_firecrawl_search(
    query='site:firecrawl.dev "API documentation"',
    limit=10
)

# Image search
results = mcp_firecrawl_search(
    query="machine learning",
    sources=["images"],
    limit=20
)
```

**Optimal Workflow:**
1. Search first without formats to get URLs
2. Use scrape tool to get content from relevant pages

---

## Mapping

### Map Website

Map a website to discover all indexed URLs on the site.

**MCP Function:** `mcp_firecrawl_map`

**Best for:** Discovering URLs on a website before deciding what to scrape.

**Parameters:**
- `url` (string, required): Base URL of the website
- `search` (string, optional): Search query to filter URLs
- `sitemap` (string, optional): Sitemap handling (include, skip, only)
- `includeSubdomains` (boolean, optional): Include subdomains
- `limit` (number, optional): Maximum number of URLs to return
- `ignoreQueryParameters` (boolean, optional): Ignore query parameters in URLs

**Example Usage:**

```python
# Map entire website
urls = mcp_firecrawl_map(
    url="https://example.com"
)

# Map with search filter
urls = mcp_firecrawl_map(
    url="https://example.com",
    search="blog"
)

# Map with sitemap only
urls = mcp_firecrawl_map(
    url="https://example.com",
    sitemap="only"
)

# Map with subdomains
urls = mcp_firecrawl_map(
    url="https://example.com",
    includeSubdomains=True
)
```

**Returns:** Array of URLs found on the site.

---

## Crawling

### Crawl Website

Start a crawl job on a website and extract content from all pages.

**MCP Function:** `mcp_firecrawl_crawl`

**Best for:** Extracting content from multiple related pages when you need comprehensive coverage.

**Warning:** Crawl responses can be very large and may exceed token limits. Limit the crawl depth and number of pages.

**Parameters:**
- `url` (string, required): Starting URL for the crawl
- `maxDiscoveryDepth` (number, optional): Maximum depth to crawl
- `limit` (number, optional): Maximum number of pages to crawl
- `allowExternalLinks` (boolean, optional): Allow crawling external links
- `allowSubdomains` (boolean, optional): Allow crawling subdomains
- `crawlEntireDomain` (boolean, optional): Crawl entire domain
- `delay` (number, optional): Delay between requests (milliseconds)
- `maxConcurrency` (number, optional): Maximum concurrent requests
- `deduplicateSimilarURLs` (boolean, optional): Deduplicate similar URLs
- `sitemap` (string, optional): Sitemap handling (skip, include, only)
- `scrapeOptions` (object, optional): Options for scraping pages

**Example Usage:**

```python
# Basic crawl
job_id = mcp_firecrawl_crawl(
    url="https://example.com/blog",
    maxDiscoveryDepth=2,
    limit=20
)

# Crawl with scraping options
job_id = mcp_firecrawl_crawl(
    url="https://example.com/blog",
    maxDiscoveryDepth=3,
    limit=50,
    scrapeOptions={
        "formats": ["markdown"],
        "onlyMainContent": True
    }
)

# Crawl with rate limiting
job_id = mcp_firecrawl_crawl(
    url="https://example.com",
    maxDiscoveryDepth=2,
    limit=100,
    delay=1000,  # 1 second delay
    maxConcurrency=5
)
```

**Returns:** Operation ID for status checking.

### Check Crawl Status

Check the status of a crawl job and retrieve results when available.

**MCP Function:** `mcp_firecrawl_check_crawl_status`

**Parameters:**
- `id` (string, required): Crawl job ID

**Example Usage:**

```python
# Check crawl status
status = mcp_firecrawl_check_crawl_status(id=job_id)

# Status will include:
# - status: "completed", "running", "failed"
# - progress: Current progress information
# - results: Crawled pages (when completed)
```

---

## Extraction

### Extract Structured Data

Extract structured information from web pages using LLM capabilities.

**MCP Function:** `mcp_firecrawl_extract`

**Best for:** Extracting specific structured data like prices, names, details from web pages.

**Parameters:**
- `urls` (array, required): Array of URLs to extract from
- `prompt` (string, optional): Custom prompt for extraction
- `schema` (object, optional): JSON schema for structured data
- `allowExternalLinks` (boolean, optional): Allow extraction from external links
- `enableWebSearch` (boolean, optional): Enable web search for context
- `includeSubdomains` (boolean, optional): Include subdomains

**Example Usage:**

```python
# Extract with schema
data = mcp_firecrawl_extract(
    urls=["https://example.com/product1", "https://example.com/product2"],
    prompt="Extract product information including name, price, and description",
    schema={
        "type": "object",
        "properties": {
            "name": {"type": "string"},
            "price": {"type": "number"},
            "description": {"type": "string"}
        },
        "required": ["name", "price"]
    }
)

# Extract with custom prompt
data = mcp_firecrawl_extract(
    urls=["https://example.com/article"],
    prompt="Extract the main headline, author, publication date, and article body"
)
```

**Returns:** Extracted structured data as defined by your schema.

---

## API Reference

### Scraping Functions

#### `mcp_firecrawl_scrape`
Scrape content from a single URL.

**Parameters:**
- `url` (string, required)
- `formats` (array, optional)
- `onlyMainContent` (boolean, optional)
- `maxAge` (number, optional)
- `waitFor` (number, optional)
- `actions` (array, optional)
- `mobile` (boolean, optional)

**Returns:** Scraped content in specified formats

#### `mcp_firecrawl_search`
Search the web and optionally extract content.

**Parameters:**
- `query` (string, required)
- `limit` (number, optional)
- `sources` (array, optional)
- `scrapeOptions` (object, optional)
- `location` (string, optional)

**Returns:** Search results with optional scraped content

### Discovery Functions

#### `mcp_firecrawl_map`
Map a website to discover all URLs.

**Parameters:**
- `url` (string, required)
- `search` (string, optional)
- `sitemap` (string, optional)
- `includeSubdomains` (boolean, optional)
- `limit` (number, optional)

**Returns:** Array of URLs

#### `mcp_firecrawl_crawl`
Start a crawl job on a website.

**Parameters:**
- `url` (string, required)
- `maxDiscoveryDepth` (number, optional)
- `limit` (number, optional)
- `allowExternalLinks` (boolean, optional)
- `scrapeOptions` (object, optional)

**Returns:** Operation ID

#### `mcp_firecrawl_check_crawl_status`
Check the status of a crawl job.

**Parameters:**
- `id` (string, required)

**Returns:** Status and results

### Extraction Functions

#### `mcp_firecrawl_extract`
Extract structured data from web pages.

**Parameters:**
- `urls` (array, required)
- `prompt` (string, optional)
- `schema` (object, optional)
- `allowExternalLinks` (boolean, optional)

**Returns:** Extracted structured data

---

## Best Practices

### Performance Optimization

1. **Use Caching**: Add `maxAge` parameter for faster repeated requests
   ```python
   result = mcp_firecrawl_scrape(
       url="https://example.com",
       maxAge=172800000  # 2 days
   )
   ```

2. **Limit Crawl Depth**: Set appropriate `maxDiscoveryDepth` and `limit`
   ```python
   job_id = mcp_firecrawl_crawl(
       url="https://example.com",
       maxDiscoveryDepth=2,
       limit=50
   )
   ```

3. **Use Map Before Crawl**: Discover URLs first, then scrape selectively
   ```python
   urls = mcp_firecrawl_map(url="https://example.com")
   # Then scrape specific URLs
   ```

### Content Extraction

1. **Use Main Content Only**: Enable `onlyMainContent` for cleaner results
   ```python
   result = mcp_firecrawl_scrape(
       url="https://example.com",
       onlyMainContent=True
   )
   ```

2. **Specify Formats**: Only request formats you need
   ```python
   result = mcp_firecrawl_scrape(
       url="https://example.com",
       formats=["markdown"]  # Only markdown, not HTML
   )
   ```

3. **Remove Images**: Use `removeBase64Images` to reduce response size
   ```python
   result = mcp_firecrawl_scrape(
       url="https://example.com",
       removeBase64Images=True
   )
   ```

### Error Handling

1. **Handle Rate Limits**: Implement retry logic with exponential backoff
2. **Validate URLs**: Ensure URLs are valid before scraping
3. **Check Status**: For crawl jobs, regularly check status
4. **Handle Timeouts**: Set appropriate timeouts for long-running operations

---

## Troubleshooting

### Common Issues

#### Scrape Returns Empty Content

1. Check if the page requires JavaScript rendering
2. Try adding `waitFor` parameter
3. Use `actions` to interact with the page
4. Check if the page blocks scrapers

#### Crawl Takes Too Long

1. Reduce `maxDiscoveryDepth`
2. Lower the `limit`
3. Increase `delay` between requests
4. Use `map` first to identify specific URLs

#### Extraction Returns Incorrect Data

1. Refine your `prompt` to be more specific
2. Provide a detailed `schema`
3. Test with a single URL first
4. Check if the page structure matches expectations

#### Rate Limiting

1. Implement delays between requests
2. Use caching with `maxAge`
3. Reduce concurrency
4. Contact support for higher limits

---

## Additional Resources

- **Firecrawl Documentation:** https://docs.firecrawl.dev/
- **Firecrawl Playground:** https://www.firecrawl.dev/playground
- **GitHub Repository:** https://github.com/firecrawl/firecrawl
- **API Reference:** https://docs.firecrawl.dev/api-reference

---

## Example Workflows

### Research Workflow

```python
# 1. Search for information
results = mcp_firecrawl_search(
    query="latest AI research papers 2023",
    limit=10
)

# 2. Extract key information
for result in results:
    data = mcp_firecrawl_extract(
        urls=[result['url']],
        prompt="Extract title, authors, abstract, and publication date",
        schema={
            "type": "object",
            "properties": {
                "title": {"type": "string"},
                "authors": {"type": "array"},
                "abstract": {"type": "string"},
                "date": {"type": "string"}
            }
        }
    )
```

### Content Aggregation Workflow

```python
# 1. Map website
urls = mcp_firecrawl_map(
    url="https://blog.example.com",
    search="tutorial"
)

# 2. Scrape specific pages
for url in urls[:10]:  # First 10 URLs
    content = mcp_firecrawl_scrape(
        url=url,
        formats=["markdown"],
        onlyMainContent=True
    )
```

### Monitoring Workflow

```python
# 1. Scrape page with caching
content = mcp_firecrawl_scrape(
    url="https://example.com/pricing",
    formats=["markdown"],
    maxAge=3600000  # 1 hour cache
)

# 2. Extract pricing data
pricing = mcp_firecrawl_extract(
    urls=["https://example.com/pricing"],
    prompt="Extract all pricing tiers with prices and features",
    schema={
        "type": "object",
        "properties": {
            "tiers": {
                "type": "array",
                "items": {
                    "type": "object",
                    "properties": {
                        "name": {"type": "string"},
                        "price": {"type": "string"},
                        "features": {"type": "array"}
                    }
                }
            }
        }
    }
)
```

---

*End of Documentation*








