# Web Performance Checklist
A checklist to make sure you website will be fast!


## HTML

- [ ]  critical link tags are in head
- [ ]  less critical link tags are end of body
- [ ]  less critical link tags lazy load
  - [ ]  `<link rel="preload" as="style" onload="this.rel='stylesheet'" id='dashicons-css' `
- [ ]  JS loads with the async property
  - [ ]  `<script async src="https://hi.js"></script>`
  - [ ]  or `defer` when scripts have dependencies

## Images

- [ ] Always use next gen formats
  - [ ] webp
  - [ ] jpeg xr
  - [ ] jpeg 2000
- [ ] Always use jpg for photography
- [ ] Size images properly
- [ ] Use srcsets for multiple image sizes
- [ ] Use the `<picture>` element to let the browser select the right image for a scenario
- [ ] Lazy load images below the fold

## Fonts

- [ ]  Fonts should always load `woff2` first
- [ ]  `woff` for fallback
- [ ]  You can use `font-display: swap;` to allow the browser to use a fallback font while custom font files are being downloaded.
- [ ]  eot, or truetype is only needed for `IE < 10`


## CSS

- [ ]  Avoid expensive selectors when possible
  - [ ]  `border-radius`
  - [ ]  `box-shadow`
  - [ ]  `transform`
  - [ ]  `filter`
  - [ ]  `:nth-child`
  - [ ]  `position: fixed;`
  - [ ]  Partial matching: `[class^="wrap"]`
- [ ]  Don't use universal selectors
  - [ ]  Universal selectors like `*, [disabled], [type=“text”]`, etc. are very expensive for the browser to match, as every element in the DOM must be checked.
- [ ]  Avoid deeply nested dependent selectors
  - [ ]  The descendant selector is very costly, as the browser must check for a match with every descendant element. On a complex web page, this can result in thousands and thousands (perhaps even more) of descendant selector searches.
- [ ]  Use media queries to load files based on use case
  
```css 
<link href="style.css"    rel="stylesheet" media="all">
<link href="portrait.css" rel="stylesheet" media="orientation:portrait">
<link href="print.css"    rel="stylesheet" media="print">
<link href="desktop.css"    rel="stylesheet" media="(min-width: 720px)">
```

## JS

- [ ]  Bundles should always be minified
- [ ]  Bundles should have 0 comments, and all license text extracted to a separate file

## Assets

- [ ]  All assets should be fingerprinted
- [ ]  All assets should have `Cache-Control: max-age=365000000, immutable` as a header
- [ ]  Assets should be served over http/2
- [ ]  Assets should only be served on a cookieless domain
- [ ]  All files should be cached by a CDN
- [ ]  Support Brotli compression
  - [ ]  15-30% smaller than gzip
- [ ]  Compress with gzip, or zopfli as a fallback to brotli
- [ ]  Do not ship unused css, js
  
## PWA
- [ ]  Use a service worker to cache assets
- [ ]  Use a service worker to prefetch pages users will most likely navigate to next
- [ ]  Support offline, and spotty networks

