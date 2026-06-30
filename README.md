# Web Performance Checklist

A checklist to make sure your website is fast, smooth, and hits green Core Web Vitals!

## HTML & Resource Loading
- [ ] Critical CSS is inline or loaded immediately in the `<head>`
- [ ] Non-critical JS loads with `defer` (to preserve execution order) or `async`
- [ ] Use the Fetch Priority API (`fetchpriority="high"`) for critical above-the-fold resources (e.g., LCP Hero Image)
- [ ] Use `fetchpriority="low"` for non-critical resources (e.g., below-the-fold images, async trackers)
- [ ] Implement the Speculation Rules API for near-instant background pre-rendering of highly probable next-page navigations

## Architectural & Edge Rendering
- [ ] Default to a server-first framework architecture (e.g., Server Components) to minimize client-side bundle weight
- [ ] Implement Streaming SSR to deliver immediate HTML layout discovery to the browser while the database completes heavy operations

## Images & Media
- [ ] Use WebP as your universal default format for standard web graphics and transparency (replaces PNG/JPG)
- [ ] Use AVIF for heavy photographic content and hero images to maximize compression (up to 50% smaller than JPEG)
- [ ] Provide fallbacks using the `<picture>` element for older environments:
```html
<picture>
  <source srcset="image.avif" type="image/avif">
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="..." loading="lazy" width="800" height="600">
</picture>
```
- [ ] Provide explicit `width` and `height` attributes on all image and video tags to reserve layout space and prevent Cumulative Layout Shift (CLS)
- [ ] Lazy load below-the-fold images natively via `loading="lazy"`

## Fonts
- [ ] Fonts should always load `woff2` first
- [ ] Use `font-display: swap;` to allow a fallback font while the custom font downloads
- [ ] Preload truly critical fonts only (e.g., `<link rel="preload" as="font" type="font/woff2" crossorigin>`), and limit this to 1 or 2 files max

## CSS & DOM Rendering
- [ ] Use media queries to conditionally load layout-specific files (`media="(min-width: 720px)"`)
- [ ] Avoid deeply nested selectors that force complex style recalculations
- [ ] Use `content-visibility: auto;` on complex, below-the-fold layout sections to skip rendering invisible off-screen content
- [ ] Apply CSS Containment (`contain`) to dynamic or third-party components to prevent page-wide layout recalculations

## JavaScript & Interactivity (Optimizing for INP)
- [ ] Code-split bundles so users only download the JS required for the current route
- [ ] Break up Long Tasks (>50ms) using native `scheduler.yield()` to keep the main thread free for user inputs
- [ ] Defer or lazy-init non-essential third-party scripts (chat widgets, analytics) until after the main page load or until initial user interaction
- [ ] Bundles must be minified, with comments stripped or extracted to separate license files

## Third-Party Tracking & Containment
- [ ] Offload analytics, heatmaps, and tracking scripts to background Web Workers using Partytown to protect the main thread
- [ ] Enforce `requestIdleCallback()` for lazy-initializing non-critical elements like support widgets so they don't fight for rendering cycles

## Animations & Motion Performance
- [ ] Only animate `transform` and `opacity` properties to ensure animations run entirely on the GPU compositor thread and bypass Layout/Paint steps
- [ ] Use the FLIP Technique (First, Last, Invert, Play) when animating elements that change layout geometry (e.g., expanding grid cards, list reordering) to protect INP metrics
- [ ] Perform geometric calculations via `getBoundingClientRect()` immediately before and after the DOM modification
- [ ] Use the native Web Animations API (`element.animate()`) to execute the inverted transition entirely on the GPU compositor thread
- [ ] Apply CSS `will-change` strictly to complex, frequently animated elements to pre-render GPU layers, and avoid layer bloating by using it sparingly
- [ ] Replace external JavaScript animation libraries with the native Web Animations API (WAAPI) (`element.animate()`) to reduce bundle sizes
- [ ] Support the `prefers-reduced-motion` media query to respect accessibility settings and save hardware cycles on low-end devices
- [ ] Use native `@starting-style` and `transition-behavior: allow-discrete` to animate entry/exit DOM flows cleanly without JS timeout hacks

## Networking & Infrastructure
- [ ] Serve all assets over HTTP/3 (QUIC) to prevent head-of-line blocking on mobile networks
- [ ] Utilize HTTP 103 Early Hints at the CDN edge to send critical asset cues to the browser while the backend origin server compiles the HTML
- [ ] Enable HTTP/3 0-RTT (Zero Round-Trip Time) connection handshakes for returning visitors to eliminate connection setup latency
- [ ] Ensure all assets utilize optimal cache headers: `Cache-Control: max-age=31536000, immutable` alongside unique file fingerprinting (cache busting)
- [ ] Use a Global CDN to cache static assets and HTML pages closer to edge users
- [ ] Utilize Brotli compression as the primary compression standard, falling back to Gzip only when necessary

## Resource Hints (Preconnecting)
- [ ] Use `<link rel="preconnect">` or `<link rel="dns-prefetch">` to establish early connections to important third-party origins (e.g., external APIs, analytics domains).

## Responsive Images & Decoding
- [ ] Implement `srcset` and `sizes` on images to serve appropriately scaled image sizes based on the user's viewport, saving bandwidth on mobile.
- [ ] Add `decoding="async"` to non-critical `<img>` tags to allow the browser to decode images off the main thread, reducing INP impact.
- [ ] Use `<video>` tags with h.264/WebM formats instead of heavy `.gif` files for animated content. Set `autoplay`, `loop`, `muted`, and `playsinline`.

## DOM Health
- [ ] Keep the total DOM size lean (under ~1,500 total nodes). An excessively large DOM bloats memory, slows down style calculations, and causes layout thrashing.

## Advanced Caching & Offline
- [ ] Implement a Service Worker (e.g., via Workbox) to cache static assets locally and intercept network requests, enabling instant repeat visits and offline resilience.
- [ ] Utilize Stale-While-Revalidate caching strategies for API requests that don't need absolute real-time freshness.

## Build Tooling & Bundling
- [ ] Ensure strict Tree-Shaking is enabled in your bundler (Vite, Webpack, Rollup) to automatically strip unused exports and dead code.
- [ ] Explicitly minify HTML and CSS (not just JS) in the production build pipeline.
- [ ] Analyze bundle sizes regularly using tools like `source-map-explorer` or Vite/Webpack bundle analyzers to catch accidental bloat from large dependencies.

## UX Performance (Perceived Performance)
- [ ] Implement Optimistic UI updates for user interactions (e.g., clicking "Like" or submitting a form) so the UI responds instantly while the server request happens in the background.
- [ ] Use skeleton screens instead of blank loading spinners to give the user structural context while content is fetched.

## Measuring Performance
- **PageSpeed Insights / Lighthouse**: To quickly get lab data scores and find quick wins.
- **Chrome DevTools Performance Panel**: Vital for profiling Interaction to Next Paint (INP) issues and locating long tasks blocking the main thread.
- **CrUX Vis & CrUX History API**: To monitor real-user field data (RUM) aggregated over time by Google.
- **WebPageTest**: For advanced waterfall analysis and connection throttling simulation.
