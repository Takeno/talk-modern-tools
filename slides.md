---
# You can also start simply with 'default'
theme: default

title: Upgrade your toolchain with modern tools like pnpm, biome, rspack
info: |
  In recent years, the web development tool landscape has evolved rapidly, yet we often stick to the same tools without exploring more efficient alternatives. What if I told you that there are modern, fast, and easy-to-integrate solutions that can revolutionize your workflow? In this talk, I'll show you how tools like pnpm, Biome, and Rspack, can boost your projects by enhancing speed, efficiency, and the developer experience. Get ready to discover new tools you can immediately adopt to elevate your toolchain to the next level.

transition: slide-left
mdc: true
---

# Upgrade your toolchain with modern tools<br />like _pnpm, biome, rspack_

_Rome, July 3rd 2025_

---
layout: cover
---
# 👋 Hi!

---
layout: default
---

# 👋 Hi!

<div class="grid grid-cols-2 gap-4 w-full">
  <div class="">
    <h2>Matteo Manchi</h2>
    <ul>
      <li>aka Takeno</li>
      <li>Senior Software Engineer</li>
      <li>CTO @ Impronta Advance</li>
      <li>Collaborator @ Jazz.tools</li>
      <li>Co-founder of RomaJS</li>
    </ul>
  </div>
  <div class="flex flex-col items-end">
    <img src="/images/photo.jpg" class="w-64">
    <img src="/images/romajs.png" class="w-24">
  </div>
</div>


---

# The Evolution of Frontend Tooling

In the early days of web development, frontend was simple.

- We coded files that were exposed directly on the network
- Each JS and CSS file linked individually in HTML, tag by tag, in specific order
- Direct server editing: make changes, refresh browser, repeat
- What you wrote was what the browser saw

<!-- Negli anni XY il frontend era composto da jquery e i nostri file .js importati uno per uno all'interno dell'html, così come i file css. Spesso e volentieri le modifiche si facevano direttamente sul server: una modifica al file, si ricarica, e via.
Il fatto che ancora oggi wordpress sia così lascia pensare...  -->

---

# The Preprocessing Era

As development methodologies evolved, we needed to transform our source code before it reached the browser.
This introduced the concept of a **build step** - code transformation before deployment.

- **Javascript Transpilation**: Babel, JSX, TypeScript, ...
- **CSS Preprocessors**: Less, Sass/SCSS, Stylus, CSS Modules...
- **Production changes**: env, minify, bundling, sourcemaps


<!-- Con l'evolvere delle metodologie di sviluppo, nasce la necessità di preprocessare il nostro codice sorgente per applicare delle trasformazioni. Ad esempio, i preprocessori di css come less e sass, e babel per rendere compatibile una sintassi moderna per browser più vecchi. -->

---

# The Build Tools Revolution

Between 2015-2020, tools emerged to orchestrate all these preprocessing operations.

### Task Runners / Bundler
Grunt > Gulp > Webpack | Vite | Rollup | esbuild

### Dependencies Managers
Github + Manual Download > Bower registry > npm registry

### Linters and Formatters
jshint > standard > eslint | prettier

<hr />

Eventually, the community converged on certain tools that became de facto standards. While these tools are now taken for granted, innovation continues with new alternatives emerging, though adoption is slower due to the high cost of migration.

---
layout: cover
---

## Let's explore some of these modern alternatives that don't get enough attention, and how to use them in our projects<v-click>, today.</v-click><br /><v-click>Without breaking anything.</v-click> <v-click>Hopefully.</v-click>


---
layout: cover
---

# Managing dependencies is<br /><v-click>always</v-click><br /><v-click>a mess.</v-click>

---
layout: image
image: images/node_modules.png
backgroundSize: contain
---

---


# NPM

NPM (Node Package Manager) has been the standard since 2010, evolving from a simple package manager into the de facto registry for the JavaScript ecosystem.

Under the name `npm`, there are two key components:

- **The registry**: A distributed infrastructure worldwide where developers publish their packages and users can pull them.
- **The CLI**: The tool used on local machines to manage dependencies, handle their dependencies, run scripts, and more.

---

# NPM

When you run `npm install`, the following steps occur:
- The CLI reads `package.json` and `package-lock.json` to determine the required dependencies
- It resolves and downloads all packages, along with their dependencies, from the npm registry
- The packages are installed into a flat `node_modules` directory
- Any defined install scripts for dependencies are executed
- Finally, `package-lock.json` is updated or created to lock the exact versions of the installed packages

Result:
```
node_modules/
├── react/          # Direct dependency
├── vue/            # Direct dependency
├── lodash/         # Dependency of react
└── object-assign/  # Dependency of react
```

<!-- Da notare che quando facciamo "import <pacchetto>" viene cercato all'interno di node_modules/<pacchetto>. Questo significa che possiamo fare import "lodash" anche se non lo abbiamo definito nei nostri node_modules.  -->

---

# <img src="https://pnpm.io/img/pnpm-no-name-with-frame.svg" style="display: inline; height: 30px; vertical-align: baseline" /> PNPM

PNPM was created by Zoltan Kochan in 2017 to solve NPM's fundamental problems.

## Key Philosophy
- **Content-addressable storage**: Packages are stored by their content hash.
- **Symlink-based linking**: This allows for efficient sharing of dependencies.
- **Strict dependency resolution**: Only declared dependencies are accessible, enhancing predictability.

## Workspace Support
- **Monorepo management**: Built-in features for managing multiple projects.
- **Hoisting control**: Configurable dependency hoisting to optimize project structure.
- **Cross-project sharing**: Dependencies can be shared across different workspaces.

<!-- pnpm history. main features: saving disk space, non-flat node_modules, installation speed, workspaces -->

---

# <img src="https://pnpm.io/img/pnpm-no-name-with-frame.svg" style="display: inline; height: 30px; vertical-align: baseline" /> PNPM one dependency for every project

### How hard links work
```
Global Store: /Users/username/.pnpm-store/v3/files/
├── 00/abc123... (React 18.2.0)
├── 01/def456... (Vue 3.3.0)
└── 02/ghi789... (Lodash 4.17.21)

Project A: node_modules/
├── react → ../../.pnpm-store/v3/files/00/abc123...
└── vue → ../../.pnpm-store/v3/files/01/def456...

Project B: node_modules/
├── react → ../../.pnpm-store/v3/files/00/abc123... <-- same file
└── lodash → ../../.pnpm-store/v3/files/02/ghi789...
```

### Space Savings
- **Single copy** of each package version globally
- **Hard links** point to the same physical file
- **Instant installation** for previously downloaded packages

<!-- How hard links works in pnpm and the saved space resulted -->

---

# <img src="https://pnpm.io/img/pnpm-no-name-with-frame.svg" style="display: inline; height: 30px; vertical-align: baseline" /> PNPM's node_modules

<div class="grid cols-2 gap-4">
<div>

### Traditional NPM Structure
```
node_modules/
├── react/          # Direct dependency
├── vue/            # Direct dependency
├── lodash/         # Dependency of react
└── object-assign/  # Dependency of react
```

</div>
<div>

### PNPM Structure
```
node_modules/
├── .pnpm/          # All packages stored here
│   ├── react@18.2.0/
│   ├── vue@3.3.0/
│   └── lodash@4.17.21/
├── react → .pnpm/react@18.2.0/node_modules/react
└── vue → .pnpm/vue@3.3.0/node_modules/vue
```

</div>
</div>

<br />

### Benefits
- **No phantom dependencies** - only declared packages accessible
- **Predictable imports** - clear dependency boundaries
- **Better security** - reduced attack surface

<!-- What non-flat node_modules means and how it improves stability of your code, with directory listing example -->

---

# <img src="https://pnpm.io/img/pnpm-no-name-with-frame.svg" style="display: inline; height: 30px; vertical-align: baseline" /> PNPM's node_modules

## Hoisting Configuration

PNPM provides flexible hoisting control through `.npmrc`:

```ini
# Default: strict (no hoisting)
node-linker=isolated

# Optional: hoisted (like npm, flat directory)
node-linker=hoisted
```
<!-- node-linker=hoisted option explained -->

---

# Start today with PNPM

- Install pnpm
  - `npm install -g pnpm`
  - `curl -fsSL https://get.pnpm.io/install.sh | sh -`
  - `brew install pnpm`

- Create lock file from the previous one: `pnpm import`

- Clean your old node_modules: `rm -rf node_modules`

- Install the dependencies with pnpm: `pnpm install`

---

## <img src="https://pnpm.io/img/pnpm-no-name-with-frame.svg" style="display: inline; height: 30px; vertical-align: baseline" /> PNPM Workspace setup (monorepo)
```json
// pnpm-workspace.yaml
packages:
  - 'packages/*'
  - 'apps/*'
```

### Benefits
- **Immediate space savings** - reduction in disk usage
- **Faster CI/CD** - quicker dependency installation
- **Better dependency management** - stricter resolution

<v-click>

## PNPM pro: merge conflicts

pnpm can automatically resolve merge conflicts in pnpm-lock.yaml. If you have conflicts, just run pnpm install and commit the changes.

_Be warned, however. It is advised that you review the changes prior to staging a commit, because we cannot guarantee that pnpm will choose the correct head - it instead builds with the most updated of lockfiles, which is ideal in most cases._

</v-click>

---
layout: cover
---

# Bundle. <v-click>Transpile.</v-click> <v-click>Precompile.</v-click> <v-click>Transform.</v-click> <v-click>And what we do in the meantime?</v-click>

---
layout: image
image: https://imgs.xkcd.com/comics/compiling.png
backgroundSize: contain
---


<span style="color: black">[https://xkcd.com/303/](https://xkcd.com/303/)</span>
---


# Webpack

Webpack has been the foundation of countless projects for over a decade. Published in 2014, it serves as a **module bundler** that:

- **Bundles** all your assets (JS, CSS, images) into optimized files
- **Transforms** code using loaders (Babel, Sass, etc.)
- **Optimizes** output for production
- **Provides** development server with hot reloading

For years, it was the default bundler for React through `create-react-app`. While Vite.js is now recommended for new projects, massive amounts of code still run through webpack loaders due to migration costs.

---

# Webpack's Configuration

```javascript
// webpack.config.js
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
        },
      },
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader'],
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: 'asset/resource',
      },
    ],
  },
};
```

<!-- È il caso di webpack, che da ormai più di X anni è alla base di tantissimi progetti. A cosa serve. Per anni è stato il bundler predefinito di React attraverso `create-react-app`, e sebbene ad oggi Vite.js sia consigliato per tutti i nuovi progetti, tantissimo codice passa ancora per i loader di webpack per via del costo di migrazione. -->

<!-- Snippet di codice di una configurazione webpack di base con react, sass e static loader -->

---
layout: image
image: images/rspack.png
backgroundSize: contain
---


---

# <img src="https://assets.rspack.rs/rspack/rspack-logo.svg" style="display: inline; height: 30px; vertical-align: baseline" /> Rspack (since 2023)

RSPack was born from the need for webpack-like functionality with modern performance. Developed by **ByteDance** (TikTok's parent company), it addresses webpack's performance bottlenecks.

- **Rust-based** - Written in Rust for blazing-fast performance
- **Webpack-compatible** - Drop-in replacement for existing webpack projects
- **Built-in optimizations** - No need for complex webpack optimization plugins

---

# <img src="https://assets.rspack.rs/rspack/rspack-logo.svg" style="display: inline; height: 30px; vertical-align: baseline" /> RSspack's Configuration

```javascript
// rspack.config.js
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'builtin:swc-loader', // Built-in fast JS transformer
        },
      },
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader'],
      },
    ],
  },
};
```

<!-- È sulle orme di webpack che nasce RSPack dalla mente di XXX. Raccontare breve storia di come è nato rspack. -->

---

# Webpack vs <img src="https://assets.rspack.rs/rspack/rspack-logo.svg" style="display: inline; height: 30px; vertical-align: baseline" /> Rspack

![comparison](/images/rspack-speed.png)

<v-click>

```bash
$ npm run build:webpack
webpack 5.89.0 compiled with 6 warnings in 13867 ms

$ npm run build:rspack
Rspack compiled with 6 warnings in 1.16 s
```

</v-click>

---


# Start today with <img src="https://assets.rspack.rs/rspack/rspack-logo.svg" style="display: inline; height: 30px; vertical-align: baseline" /> Rspack

Migrating from webpack to RSPack is surprisingly straightforward.

- Installation
  ```bash
  npm install @rspack/cli @rspack/core
  ```
- Configuration
  - **Rename** `webpack.config.js` to `rspack.config.js`
  - **Update** plugin names
  - **Replace** `babel-loader` with `builtin:swc-loader`
- Replace `webpack` with `rspack` in package.json's scripts
- Enjoy


_Note: some plugin are still unsupported or there are alternatives. Check the (check [compatibility guide](https://rspack.rs/plugins/webpack))._

---

# Other tools of the Rstack family

ByteDance's Rust-based tooling ecosystem extends beyond RSPack

- **Rspack** - Fast webpack-compatible bundler
- **Rsbuild** - Build tool with zero-config support
- **Rslib** - Library development tool
- **Rsdoctor** - Bundle analysis and optimization tool
- **Rstest** - Fast test runner for JavaScript/TypeScript

<!-- List of all rs-tools: rspack, rsbuild, rslib, rsdoctor, rstest -->


---
layout: cover
---

# <span style="text-decoration: underline wavy red;">Linters</span> and (formatters<span style="text-decoration: underline wavy red;">&nbsp;&nbsp;</span>).
<v-click>One helps finding bugs.</v-click> <v-click>One helps manage my OCD.</v-click>


<!-- Chi sa la differenza tra linters e formatters? Linter ti aiuta ad individuare possibili errori - la variabile che stai usando non esiste - l'altro mantiene lo stile del codice uguale per tutta la codebase. -->

---

# ESLint

ESLint belongs to the family of static analyzers. It scans code, detects patterns, and generates warnings or errors, assisting developers in maintaining consistent and mistake-free code.

It has become the de facto standard for linting within the JavaScript ecosystem, supporting JavaScript, TypeScript, JSX, and more.

Its plugin system enables users to develop custom rules and build modular configurations...

---

# ESLint

... until its configuration become complex as projects grow.

- Multiple config file formats: `.eslintrc`, `.eslintrc.js`, `.eslintrc.json`, etc.
- Plugins, presets, and overrides can be hard to manage.
- Extending and merging configs can lead to confusion.


```json
// package.json
"eslint-config-prettier": "^8.5.0",
"eslint-plugin-babel": "^5.3.1",
"eslint-plugin-eslint-comments": "^3.2.0",
"eslint-plugin-ft-flow": "^2.0.1",
"eslint-plugin-jest": "^27.9.0",
"eslint-plugin-jsx-a11y": "^6.6.0",
"eslint-plugin-react": "^7.30.1",
"eslint-plugin-react-native": "^4.0.0",
"eslint-plugin-redundant-undefined": "^0.4.0",
"eslint-plugin-relay": "^1.8.3",
```

---

# ESLint


```js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended',
    'prettier',
  ],
  plugins: ['react', '@typescript-eslint'],
  rules: {
    'no-console': 'warn',
    'react/prop-types': 'off',
    '@typescript-eslint/no-unused-vars': 'error',
  },
  overrides: [
    {
      files: ['*.ts', '*.tsx'],
      rules: {
        '@typescript-eslint/explicit-module-boundary-types': 'off',
      },
    },
  ],
};
```

---


# ESLint v9: Unified Configuration

With v9, ESLint introduced a unified, modern configuration system:

- **Single config file**: `eslint.config.js`
- **Flat config**: No more deep merging or multiple formats
- **Explicit plugin usage**: Plugins are imported and used directly

```js
// After (v9+)
import js from '@eslint/js';
import react from 'eslint-plugin-react';
export default [
  js.configs.recommended,
  {
    plugins: { react },
    rules: {
      'react/prop-types': 'off',
    },
  },
];
```

- **No more extends**: Use arrays to compose configs
- **Direct plugin imports**: More explicit and modular
- **Predictable config order**: No hidden merges

---

# Prettier

An opinionated code formatter

### Why a code formatter?
- **Consistency**: Automatically enforces a uniform code style across the entire codebase, reducing debates over formatting.
- **Productivity**: Saves time by handling all formatting, letting developers focus on logic instead of style.
- **Collaboration**: Makes code reviews easier by eliminating formatting-related diffs and discussions.
- **Integration**: Works seamlessly with most editors and CI pipelines, ensuring code is always formatted before merging.

Keeping a consistent coding style along the project is fundamental for readability, maintainability, and team efficiency.

---

# Prettier rules

```
{
  "semi": false,
  "singleQuote": true
}
```

<v-click>

Where?

- A "prettier" key in your package.json, or package.yaml file.
- A .prettierrc file written in JSON or YAML.
- A .prettierrc.json, .prettierrc.yml, .prettierrc.yaml, or .prettierrc.json5 file.
- A .prettierrc.js, prettier.config.js, .prettierrc.ts, or prettier.config.ts
- A .prettierrc.mjs, prettier.config.mjs, .prettierrc.mts, or prettier.config.mts
- A .prettierrc.cjs, prettier.config.cjs, .prettierrc.cts, or prettier.config.cts
- A .prettierrc.toml file.

</v-click>

---

# Prettier + ESLint

```js
// eslint.config.js
const eslintPluginPrettierRecommended = require('eslint-plugin-prettier/recommended');

module.exports = [
  // Any other config imports go at the top
  eslintPluginPrettierRecommended,
  {
    "prettier/prettier": [
      "error",
      {
        "singleQuote": true,
        "parser": "flow"
      }
    ]
  }
];
```


---

# <img src="https://avatars.githubusercontent.com/u/140182603?s=120&v=4" style="display: inline; height: 30px; vertical-align: baseline" /> Biome

Formely Rome Tools. Format, lint, and more in a fraction of a second.

- Written in Rust
- Fast Formatter, 97% compatibility with Prettier
- Performant Linter, 331 rules from ESLint, TypeScript, and others
- Everything in a single tool
- First-party extensions for VSCode, IntelliJ, Zed
- Support to monorepo with share configurations

- [Emanuele Stoppa, All tools lead to Rome \[ITA\] - RomaJS Meetup Aprile 2022](https://www.youtube.com/watch?v=3os5_xAYA0c)


---

# <img src="https://avatars.githubusercontent.com/u/140182603?s=120&v=4" style="display: inline; height: 30px; vertical-align: baseline" /> Biome

```json
// biome.json
{
  "$schema": "https://biomejs.dev/schemas/2.0.5/schema.json",
  "formatter": {
    "indentStyle": "space", // default is `tab`
    "lineWidth": 100 // default is `80`
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single", // default is `double`
      "lineWidth": 120 // override `formatter.lineWidth`
    }
  },
  "json": {
    "formatter": {
      "enabled": false
    }
  },
  "linter": {
    "enabled": false
  },
  "assist": {
    "enabled": false
  }
}
```

---

# Start today with <img src="https://avatars.githubusercontent.com/u/140182603?s=120&v=4" style="display: inline; height: 30px; vertical-align: baseline" /> Biome

Migrating from eslint/prettier to Biome is surprisingly straightforward.

- Installation
  ```bash
  pnpm add -D -E @biomejs/biome
  ```
- Init configuration
  ```bash
  npx biome init
  ```
- Import eslint / prettier configuration
  ```bash
  npx biome migrate eslint --write
  npx biome migrate prettier --write
  ```
- Run!
  ```bash
  # Format, lint, and organize imports of all files
  npx biome check --write
  ```


---
layout: cover
---

# Fast feedback enables us to deliver solid results faster. <v-click>And to avoid distractions.</v-click> <v-click>And be more productive.</v-click> <v-click>And have more time for ourselves.</v-click>


---

# <img src="https://vitest.dev/logo.svg" style="display: inline; height: 30px; vertical-align: baseline" /> Vitest

A Vite-native testing framework. It's fast!

- Based on vite - shareble configuration
- Compatible with Jest
- Smart watch mode
- Project mode
- Browser mode

---

# Start today with <img src="https://vitest.dev/logo.svg" style="display: inline; height: 30px; vertical-align: baseline" /> Vitest

```diff
+ import vi, {describe, it, test, expect} from 'vitest';

- jest.mock('./some-path', () => 'hello')
+ vi.mock('./some-path', () => ({
+   default: 'hello',
+ }))
```


Check some other diffs here: https://vitest.dev/guide/migration.html#jest

---
layout: cover
---

# The JavaScript ecosystem is vibrant and constantly evolving, facing new challenges every few months.

---

# New amazing tools

- [SWC - Speedy Web Compiler](https://swc.rs/) vs Babel
- [rollup.js](https://rollupjs.org/) bundler behind Vite
- [esbuild](https://esbuild.github.io/) extremely fast bundler for the web
- [tsup](https://tsup.egoist.dev/) bundler for TS libs (based on esbuild)
- [Volta](https://volta.sh/) vs nvm
- [Bun](https://bun.sh/) and [Deno](https://deno.com/) vs node.js
- [Tauri](https://v2.tauri.app/) for cross-platform applications

The excitement around Rust has driven a new wave of solutions aimed at accelerating tools for large codebases.

---
layout: cover
---

# And you? <v-click>What tool have you tried recently that has increased your productivity?</v-click>

---
layout: cover
---

# Thanks!

- https://takeno.dev
- https://github.com/Takeno
- https://romajs.org
- https://www.linkedin.com/in/matteo-manchi/