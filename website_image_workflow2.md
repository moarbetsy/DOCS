# The Ultimate Website Image Workflow
**A Standard Operating Procedure (SOP) for Designers & Developers**

This guide covers the lifecycle of an image from **Design Tool** → **Code** → **Browser**. Follow this to ensure your site scores 100 on Lighthouse Performance, Accessibility, and SEO.

---

## Part 1: The 6 Golden Rules

1.  **Choose the Right Format**
    *   **SVG:** Logos, icons, illustrations, graphs.
    *   **AVIF/WebP:** Photos, screenshots, hero backgrounds. (AVIF is smaller, WebP has wider support; use both).
    *   **PNG:** Only if you need transparency on a complex non-vector image.
    *   **JPEG:** The "fail-safe" fallback.

2.  **Size Budget (Hard Limits)**
    *   Icons/Logos: **< 5 KB**
    *   Standard Photos: **< 70 KB**
    *   Hero/LCP Images: **< 200 KB**

3.  **Reserve the Space**
    *   Always set `width` and `height` attributes on `<img>`. This prevents **CLS** (layout shifts) as the image loads.

4.  **Prioritize the "LCP" (Largest Contentful Paint)**
    *   The main image users see first (Hero) must **never** be lazy-loaded.
    *   Use `fetchpriority="high"`.

5.  **Lazy Load the Rest**
    *   Anything "below the fold" gets `loading="lazy"`.

6.  **Mobile vs. Desktop**
    *   Never serve a 1920px wide desktop image to a 390px mobile screen. Use `srcset` or `<picture>`.

---

## Part 2: Step-by-Step Workflow

### Phase 1: Design & Export

**Goal:** Get the assets out of Figma/XD/Sketch correctly.

1.  **Separate Vectors from Rasters**
    *   Is it an icon or logo? Export as **SVG**.
    *   Is it a photo? Export as **JPG** or **PNG** initially (we will convert later).

2.  **Export for High DPI (Retina)**
    *   Export images at **1.5x or 2x** the size they will appear on the screen.
    *   *Pro Tip:* When exporting at 2x, you can lower the quality (compression) significantly (e.g., 40% quality). A high-res, low-quality image often looks sharper and is smaller than a low-res, high-quality image.

3.  **File Naming (SEO Critical)**
    *   Use lowercase, hyphens, and keywords.
    *   ✅ `ninebot-scooter-side-view.jpg`
    *   ❌ `IMG_5502.jpg`, `final-hero-v3.png`

---

### Phase 2: Optimization

**Goal:** Minimize file size without losing visual quality.

1.  **Manual Method (Small Sites)**
    *   Use **[Squoosh.app](https://squoosh.app/)** or **ImageOptim**.
    *   Convert Photos to **WebP/AVIF** (Quality ~75-80).
    *   Sanitize SVGs using **[SVGOMG](https://jakearchibald.github.io/svgomg/)** (removes editor metadata).

2.  **Automated Method (Large Sites)**
    *   If using Next.js, Astro, or Nuxt, let the framework handle this via their `<Image />` component.
    *   If using a CMS/CDN (Cloudinary, Contentful), upload the high-res master and let the URL API handle resizing/formats.

---

### Phase 3: Implementation (The Code)

This is where most developers fail. Choose the scenario that matches your image.

#### Scenario A: The "Hero" Image (LCP)
*This image is at the top of the page. It must load instantly and adapt to mobile screens.*

**Key Attributes:**
*   `loading="eager"` (default, but good to know)
*   `fetchpriority="high"` (Critical for Core Web Vitals)
*   `decoding="sync"` (Ensures immediate painting; do **not** use async here)
*   `sizes` (Tells the browser how wide the image will be on screen)

```html
<picture>
  <!-- Best Format (AVIF) + Resolution Switching -->
  <source 
    srcset="/img/hero-400.avif 400w, /img/hero-800.avif 800w, /img/hero-1200.avif 1200w" 
    sizes="100vw"
    type="image/avif"
  >
  <!-- Good Format (WebP) + Resolution Switching -->
  <source 
    srcset="/img/hero-400.webp 400w, /img/hero-800.webp 800w, /img/hero-1200.webp 1200w" 
    sizes="100vw"
    type="image/webp"
  >
  <!-- Fallback (JPG) -->
  <img
    src="/img/hero-1200.jpg"
    srcset="/img/hero-400.jpg 400w, /img/hero-800.jpg 800w, /img/hero-1200.jpg 1200w"
    sizes="100vw"
    alt="Dashboard view of analytics platform"
    width="1200" 
    height="600"
    fetchpriority="high"
    decoding="sync"
  >
</picture>
```

#### Scenario B: Standard Content Image (Below the fold)
*General images users scroll down to see.*

**Key Attributes:**
*   `loading="lazy"`
*   `decoding="async"` (Prevents main-thread jank while scrolling)
*   `srcset` (Resolution Switching)

```html
<img
  src="/img/blog-post-800.jpg"
  srcset="/img/blog-post-400.webp 400w, 
          /img/blog-post-800.webp 800w"
  sizes="(max-width: 600px) 100vw, 800px"
  alt="Detail view of the product features"
  width="800"
  height="600"
  loading="lazy"
  decoding="async"
>
```

#### Scenario C: Art Direction
*When you need to crop the image differently for mobile (e.g., wide landscape on desktop, square close-up on mobile).*

```html
<picture>
  <!-- Mobile Crop (Square) -->
  <source 
    media="(max-width: 799px)" 
    srcset="/img/product-square.webp"
  >
  <!-- Desktop Crop (Wide) -->
  <source 
    media="(min-width: 800px)" 
    srcset="/img/product-wide.webp"
  >
  <!-- Fallback -->
  <img 
    src="/img/product-wide.jpg" 
    alt="Product shot"
    width="1200"
    height="600"
    loading="lazy"
    decoding="async"
  >
</picture>
```

#### Scenario D: Icons (SVG)
*   **Inline:** If you need to animate it or change color on hover (`fill: currentColor`).
*   **Img Tag:** If it's just a static logo (better for browser caching).

```html
<!-- Inline Example -->
<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor">
  <path d="...vector data..." />
</svg>

<!-- Image Tag Example -->
<img src="/icons/logo.svg" alt="Company Logo" width="150" height="40">
```

#### Scenario E: Background Images (CSS)
*Do not serve a 2MB desktop background to a phone.*

```css
/* Modern Approach: image-set */
.hero-section {
  background-image: image-set(
    url("/img/bg-mobile.webp") 1x,
    url("/img/bg-desktop.webp") 2x
  );
  background-size: cover;
}

/* Fallback approach (using Media Queries) */
@media (min-width: 1024px) {
  .hero-section {
    background-image: url('/img/bg-desktop.webp');
  }
}
```

---

### Phase 4: Accessibility & SEO

1.  **Describe the content**
    *   ✅ `alt="A red Tesla Model 3 driving on a mountain road"`
    *   ❌ `alt="car"` or `alt="image"`

2.  **Hide decorative elements**
    *   If the image adds no meaning (blobs, shapes, dividers), hide it from screen readers.
    *   Code: `alt=""` (Empty string is the standard).

3.  **Social Sharing (OpenGraph)**
    *   Create a specific image for social links (usually **1200x630px**).
    *   Add it to your `<head>`:
    ```html
    <meta property="og:image" content="https://site.com/img/og-share.jpg">
    ```

---

### Phase 5: Deployment & Caching

1.  **Cache-Control**
    *   Ensure your server/host sets long cache headers for images.
    *   Ideal: `Cache-Control: public, max-age=31536000, immutable`.

2.  **Versioning (Hashing)**
    *   If you update an image, the filename must change so users don't see the old cached version.
    *   Most build tools (Vite, Webpack) do this automatically: `hero.a1b2c3.webp`.

---

## Summary Checklist

Before publishing, ask:

1.  [ ] **Format:** Is it WebP/AVIF (photos) or SVG (icons)?
2.  [ ] **Size:** Is the file under 100KB (or 200KB for hero)?
3.  [ ] **Dimensions:** Did I add `width` and `height` attributes?
4.  [ ] **LCP:** Does the Hero have `fetchpriority="high"`, 
