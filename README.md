# Web Performance Checklist
A checklist to make sure your website is fast and hits green Core Web Vitals!

## HTML & Resource Loading

- [ ] Critical CSS is inline or loaded immediately in the `<head>`
- [ ] Non-critical JS loads with `defer` (to preserve execution order) or `async`
- [ ] Use the Fetch Priority API (`fetchpriority="high"`) for critical above-the-fold resources (e.g., LCP Hero Image)
- [ ] Use `fetchpriority="low"` for non-critical resources (e.g., below-the-fold images, async trackers)
- [ ] Implement the Speculation Rules API for near-instant background pre-rendering of highly probable next-page navigations

## Images & Media

- [ ] Use WebP as your universal default format for standard web graphics and transparency (replaces PNG/JPG)
- [ ] Use AVIF for heavy photographic content and hero images to maximize compression (up to 50% smaller than JPEG)
- [ ] Provide fallbacks using the `<picture>` element for older environments:
  <picture>
    <source srcset="image.avif" type="image/avif">
    <source srcset="image.webp" type="image/webp">
    <img src="image.jpg" alt="..." loading="lazy" width="800" height="600">
  </picture>
- [ ] Provide explicit `width` and `height` attributes on all image and video tags to reserve layout space and prevent Cumulative Layout Shift (CLS)
- [ ] Lazy load below-the-fold images natively via `loading="lazy"`

## Fonts

- [ ] Fonts should always load `woff2` first
- [ ] Use `font-display: swap;` to allow a fallback font while the custom font downloads
- [ ] Preload truly critical fonts only (e.g., `<link rel="preload" as="font" type="font/woff2" crossorigin>`), and limit this to 1 or 2 files max

## CSS

- [ ] Use media queries to conditionally load layout-specific files (`media="(min-width: 720px)"`)
- [ ] Avoid deeply nested selectors that force complex style recalculations
- [ ] Use `content-visibility: auto;` on complex, below-the-fold layout sections to skip rendering invisible off-screen content
- [ ] Keep properties that trigger heavy paint cycles (`box-shadow`, `filter`) isolated to their own rendering layers via `will-change` if they animate

## JavaScript & Interactivity (Optimizing for INP)

- [ ] Code-split bundles so users only download the JS required for the current route
- [ ] Break up Long Tasks (>50ms) using native `scheduler.yield()` to keep the main thread free for user inputs
- [ ] Defer or lazy-init non-essential third-party scripts (chat widgets, analytics) until after the main page load or until initial user interaction
- [ ] Bundles must be minified, with comments stripped or extracted to separate license files

## Networking & Infrastructure

- [ ] Serve all assets over HTTP/3 (QUIC) to prevent head-of-line blocking on mobile networks
- [ ] Ensure all assets utilize optimal cache headers: `Cache-Control: max-age=31536000, immutable` alongside unique file fingerprinting (cache busting)
- [ ] Use a Global CDN to cache static assets and HTML pages closer to edge users
- [ ] Utilize Brotli compression as the primary compression standard, falling back to Gzip only when necessary
- [ ] Utilize **HTTP 103 Early Hints** to eliminate "server think-time" bottlenecks
  - [ ] Implement Early Hints at your CDN edge (e.g., Cloudflare, Akamai) or origin proxy (e.g., Nginx 1.25+) to send critical asset cues while the backend compiles the HTML payload.
  - [ ] Only send hints for highly stable assets (like global stylesheets or primary fonts) or domain preconnects (`rel="preconnect"`) to avoid hinting outdated hashed files.
  - [ ] Ensure Early Hints are restricted to HTTP/2 and HTTP/3 connections to prevent protocol errors on ancient HTTP/1.1 clients.
## Architectural & Edge Rendering

- [ ] Default to a server-first framework architecture to minimize client-side bundle weight
- [ ] Implement Streaming SSR to deliver immediate HTML layout discovery to the browser
- [ ] Push dynamic logic to Edge Workers to maintain sub-50ms Time to First Byte (TTFB) globally

## DOM & Browser Rendering (CSS)

- [ ] Use `content-visibility: auto;` to skip layout and painting for off-screen/below-the-fold components
- [ ] Apply CSS Containment (`contain`) to dynamic or third-party components to prevent page-wide layout recalculations

## Third-Party Tracking

- [ ] Offload analytics and tracking scripts to background Web Workers using Partytown to protect the main thread
- [ ] Enforce `requestIdleCallback()` for lazy-initializing non-critical elements like support widgets

## Measuring Performance

- PageSpeed Insights / Lighthouse: To quickly get lab data scores and find quick wins.
- Chrome DevTools Performance Panel: Vital for profiling Interaction to Next Paint (INP) issues and locating long tasks blocking the main thread.
- CrUX Vis & CrUX History API: To monitor real-user field data aggregated over time by Google.
- WebPageTest: For advanced waterfall analysis and connection throttling simulation.
