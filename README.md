# Vitedge

[Vite](https://github.com/vitejs/vite) _Edge Side Rendering_ (ESR) framework.

> What's ESR? Think of SSR (Server Side Rendering) in CDN nodes instead of actual servers. This is possible today thanks to [Cloudflare Workers](https://workers.cloudflare.com/) (and maybe some other platforms in the future).

See a full example [here](./example).

**Note: This is WIP.**

## Concept

Vitedge is "just a Vite app" that prerrenders the first view in a CF worker and runs the rest as an SPA. That means it will lead to good SEO while keeping the snappy routing and DX of an SPA.

It can replace static site generators in some situations since it builds on the fly and caches at the edge (cache is WIP). Therefore, instead of getting a static `index.html` from the CDN, the CDN itself will create it on the fly or provide it from cache if it was already accessed (with configurable cache age).

Apart from the normal Vite app, it provides some extra fullstack utilities.

### API

It can create a REST API based on filesystem routes: `<root>/api/my/function.js` will be built as `/api/my/function` endpoint and can be requested from the frontend.

### ESR Page Props

Each page can make an optional "get page props" request to the worker before rendering. For example, if a page's route is `/admin/customers/123`, the endpoint `/props/admin/customers/123` will be requested automatically before rendering. The handler for this route must be defined in `<root>/api/props/<route name>`.

## Usage

1. Create a normal Vite app.
2. Install `vitedge` with your package manager.
3. Import `vitedge/plugin` in your `vite.config.js`. [Example here](./example/vite.config.js).
4. Import and call Vitedge from your app's entry point providing your main `App.vue` and your page routes. Vitedge will create the router and attach the app to the DOM for you according to the running environment. [Example here](./example/src/main.js)
5. Build using `vitedge build` command

## TODOS

- [ ] TypeScript
- [ ] Custom Vite dev-server that serves API/Props during development
- [ ] Docs website
- [x] Extract CF worker boilerplate as utilities
- [ ] Cache props/html in worker and make it configurable
- [ ] i18n utilities
- [ ] Auth utilities (passing JWT in requests)
- [ ] List of pages that should be prerrendered automatically after deployment
- [ ] Compatibility with Node runtime for other providers (Vercel/...)

### Research/consider

- Research if Rollup can build directly for webworker target and remove Webpack
- Explore the possibility of extracting "getPageProps" from each page component using custom Vue blocks (currently these are provided in `<root>/api/props/` directory).
- Consider file sytem routing for pages (this should probably happen in user-land with `vite-plugin-voie` or similar)
