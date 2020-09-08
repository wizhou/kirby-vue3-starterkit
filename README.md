<p align="center">
  <img src="./public/img/favicon.svg" alt="Logo of Kirby + Vue.js 3 Starterkit" width="114" height="114">
</p>

<h3 align="center">Kirby + Vue.js 3 Starterkit</h3>

<p align="center">
  SPA with Vue.js 3 and Kirby. Uses Vite as web dev build tool.<br>
  <a href="https://kirby-vue3-starterkit.jhnn.dev"><strong>Explore the starterkit live »</strong></a>
</p>

<br>

## Kirby + Vue.js 3 Starterkit

### Key Features

- 🖖 Vue.js 3 powered
- ⚡️ [Vite](https://github.com/vitejs/vite) instead of Vue.js CLI
- ♿ Accessible routing
- 🔍 SEO-friendly ([server-side generated](site/snippets/meta.php) meta tags)
- 🚝 [Offline-first](#caching-offline-capability--stale-while-revalidate): Page data caching & offline redirection
- 💫 [Stale-while-revalidate](#stale-while-revalidate) page data
- 🗃️ Centralized state management without Vuex
- 🤝 Shared `.env` file for frontend & backend
- 🚀 Modern Kirby folder setup

### Introduction

This project is a starting point for [Vue.js v3](https://github.com/vuejs/vue-next) as the frontend UI library and [Kirby](https://getkirby.com) as headless CMS. The content is provided as JSON through Kirby and fetched by the frontend.

It's a simple, zero-setup, almost identical port of the [Kirby 3 starterkit](https://github.com/getkirby/starterkit) frontend (snippets, templates and their corresponding JS/CSS) to Vue.js single file components. By "almost" meaning that some features have been added like meta tags generation, environment variables support, accessible routing etc.

To compile the frontend sources, [Vite](https://github.com/vitejs/vite) comes to use. Vite is an opinionated web development build tool, created by Evan You. It serves code via native ES Module imports during development, allowing you to develop Vue.js single file components without a bundle step, and bundles it with Rollup for production. Features include:
- Lightning fast cold server start
- Instant hot module replacement (HMR)
- [Head over to the GitHub page](https://github.com/vitejs/vite) for more details

### Folder Structure

Some notes about the folder structure with some additional comments on important files.

<details>
<summary><b>Expand folder tree</b></summary>

```sh
kirby-vue3-starterkit/
|
|   # Includes all frontend-related files
├── frontend/
|   |
|   |   # Vue.js sources
|   ├── src/
|   |   |
|   |   |   # `Header`, `Footer`, `Intro` and more components
|   |   |   # (Vue.js components correspond to Kirby snippets)
|   |   ├── components/
|   |   |
|   |   |   # Commonly used helpers
|   |   ├── helpers/
|   |   |
|   |   |   # Hooks for common actions
|   |   ├── hooks/
|   |   |   |
|   |   |   |   # Hook to announce any useful information for screen readers
|   |   |   ├── useAnnouncer.js
|   |   |   |
|   |   |   |   # Hook for retrieving pages from the Kirby API as JSON
|   |   |   ├── useKirbyApi.js
|   |   |   |
|   |   |   |   # Returns page for current path corresponding to Kirby's `$page` object
|   |   |   ├── usePage.js
|   |   |   |
|   |   |   |   # Hook for various service worker methods like registering
|   |   |   ├── useServiceWorker.js
|   |   |   |
|   |   |   |   # Returns object corresponding to Kirby's global `$site`
|   |   |   └── useSite.js
|   |   |
|   |   |   # Vue Router related methods and exports
|   |   ├── router/
|   |   |   |
|   |   |   |   # Initializes and exports the router instance
|   |   |   ├── index.js
|   |   |   |
|   |   |   |   # Handles the router's scroll behaviour
|   |   |   └── scrollBehaviour.js
|   |   |
|   |   |   # Minimal store to cache page data fetched via api (Vuex-free)
|   |   ├── store/
|   |   |
|   |   |   # Vue.js views correspond to Kirby templates
|   |   |   # Routes are being automatically resolved 
|   |   ├── views/
|   |   |
|   |   ├── App.vue
|   |   ├── main.js
|   |   └── serviceWorker.js
|   |
|   |   # Index page used by Vite in development environment
|   └── index.html
|
|   # The main entry point of the website
|   # Therefore web servers can only access files based in that directory
├── public/
|   |
|   |   # JavaScript and CSS assets generated by Vite (not tracked)
|   ├── assets/
|   |
|   |   # Static images for web manifest and PWA icons
|   ├── img/
|   |
|   |   # Kirby's media folder for thumbnails and more (contents not tracked)
|   └── media/
|
|   # Various development-centric Node scripts
├── scripts/
|   |
|   |   # Service worker generator
|   ├── buildSw.js
|   |
|   |   # Starts a PHP server for Kirby, run by `npm run kirby:serve` from root
|   ├── serveKirby.js
|   |
|   |   # Builds the location from a given api path
|   └── useApiLocation.js
|
|   # Kirby's core folder containing templates, blueprints, snippets etc. for Kirby
├── site/
|   ├── blueprints/
|   ├── config/
|   ├── models/
|   ├── plugins/
|   ├── snippets/
|   |   |
|   |   |   # Other files
|   |   ├── [...]
|   |   |
|   |   |   # Index page used by Kirby in production environment
|   |   |   # Except for asset loading identical to `frontend/index.html`
|   |   └── index.php
|   |
|   |   # Templates for JSON content representations fetched by frontend
|   └── templates/
|
|   # Contains everything content and user data related (contents of each directory are not tracked)
├── storage/
|   ├── accounts/
|   ├── cache/
|   ├── content/
|   └── sessions/
|
|   # Kirby CMS and other PHP dependencies handled by Composer
├── vendor/
|
|   # Shared environment variables accessible from both Kirby and Vue.js
|   # To be duplicated as `.env`
├── .env.example
|
|   # Handles PHP dependencies
├── composer.json
|
|   # Handles NPM dependencies
├── package.json
|
|   # Router for the PHP built-in development server (used by `serveKirby.js`)
├── server.php
|
|   # Configuration file for Vite
└── vite.config.js
```

</details>

## Caching, Offline Capability & Stale-While-Revalidate

Even without a service worker installed, the frontend will store pages between indiviual routes/views (in-memory store). When you reload the tab, the data for each page is freshly fetched from the API once again.

For offline capability of your Vue app, you can choose to activate the included [service worker](#service-worker).

A visual explanation of both methods can be found in the following flow chart:

![Caching for Kirby and Vue 3 starterkit](./.github/kirby-vue-3-cache-and-store.png)

### Service Worker

The service worker precaches all CSS & JS assets required by the Vue app and caches the data of every requested page. All assets are versioned and served from the service worker cache directly.

Each JSON request will be freshly fetched from the network and saved to the cache. If the user's navigator turns out to be offline, the cached page request will be returned.

### Stale-While-Revalidate

The stale-while-revalidate mechanism for the [`usePage`](frontend/src/hooks/usePage.js) hook allows you to respond as quickly as possible with cached page data if available, falling back to the network request if it's not cached. The network request is then used to update the cached page data – which directly affects the view after lazily assigning changes (if any), thanks to Vue's reactivity.

## Prerequisites

- Node.js with npm (only required to build the frontend)
- PHP 7.4+

> Kirby is not a free software. You can try it for free on your local machine but in order to run Kirby on a public server you must purchase a [valid license](https://getkirby.com/buy).

## Setup

1. Duplicate the [`.env.example`](.env.example) as `.env` and optionally adapt its values:

```bash
cp .env.example .env
```

2. Install npm dependencies:

```bash
npm install
```

**Note**: Composer dependencies are tracked in this repository by default. Running `composer install` isn't necessary.

## Usage

### Serve backend & frontend for development

```bash
# Runs `npm run kirby:serve` and `npm run dev` in parallel
npm run start
```

Out of the box the backend is automatically served while developing. `npm run kirby:serve` spawns the PHP built-in web server by Node. You can also serve the backend by a web server of your choice. If done so, please specify hostname and port in your `.env` if they differ from `localhost`and `8080` respectively so that the decoupled frontend can call the Kirby API for JSON content in development.

### Compile for production

Build the frontend assets (CSS & JS files) to `public/assets`:

```bash
npm run build
```

**Note**: If you wish to target older browsers, run `npm run build:compat` which transpiles to `es2017`. For even older browsers, you can change the target in the `package.json`'s npm script.

### Configuration

All development and production related configurations for both backend and frontend code are located in your `.env` file:
- `KIRBY_SERVER_HOSTNAME` and `KIRBY_SERVER_PORT` specify the address where you wish the Kirby backend to be served from. It is used by the frontend to fetch content data as JSON.
- Keys starting with `VITE_` are available in your code following the `import.meta.env.VITE_CUSTOM_VARIABLE` syntax.

To enable the **service worker** which precaches essential assets and page API calls for offline capability, set:
- `VITE_ENABLE_SW` to `true`

To keep page data fresh with **stale-while-revalidate**, set:
- `VITE_ENABLE_SWR` to `true`

### Deployment

1. Deploy whole repository on your server.
2. Duplicate [`.env.example`](.env.example) as `.env`.
3. Install npm dependencies and build frontend assets: `npm i && npm run build`.
4. Change variables in your `.env`:
   - `KIRBY_DEBUG` to `false`
   - `KIRBY_CACHE` to `true` (recommended)
   - `VITE_ENABLE_SW` to `true` (recommended)
5. Point your web server to the `public` folder.
6. Some hosting environments require to uncomment `RewriteBase /` in [`.htaccess`](public/.htaccess) to make site links work.

Now your project is hopefully up 'n' running!

## Credits

- Big thanks to Jakub Medvecký Heretik for his inspirational work on [kirby-vue-starterkit](https://github.com/jmheretik/kirby-vue-starterkit).
- Mario Brendl for his [article on a Vue 3 based store w/o Vuex](https://medium.com/@mario.brendel1990/vue-3-the-new-store-a7569d4a546f).

## License

[MIT](https://opensource.org/licenses/MIT)

It is discouraged to use this starterkit in any project that promotes racism, sexism, homophobia, animal abuse, violence or any other form of hate speech.
