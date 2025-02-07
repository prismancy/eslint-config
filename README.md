# @in5net/eslint-config

- Aimed to be used **with** Prettier
- Reasonable defaults, best practices, only one line of config
- Designed to work with TypeScript, JSX, Svelte, JSON, etc. Out-of-box.
- Opinionated, but [very customizable](#customization)
- [ESLint Flat config](https://eslint.org/docs/latest/use/configure/configuration-files-new), compose easily!
- **Style principle**: Minimal for reading, stable for diff, consistent
  - Sorted imports, dangling commas
  - Double quotes, semi
- Respects `.gitignore` by default
- Requires ESLint v9.5.0+

## Usage

### Manual Install

If you prefer to set up manually:

```bash
pnpm i -D eslint @in5net/eslint-config
```

And create `eslint.config.mjs` in your project root:

```js
// eslint.config.mjs
import in5net from "@in5net/eslint-config";

export default in5net();
```

<details>
<summary>
Combined with legacy config:
</summary>

If you still use some configs from the legacy eslintrc format, you can use the [`@eslint/eslintrc`](https://www.npmjs.com/package/@eslint/eslintrc) package to convert them to the flat config.

```js
// eslint.config.mjs
import in5net from "@in5net/eslint-config";
import { FlatCompat } from "@eslint/eslintrc";

const compat = new FlatCompat();

export default in5net(
  {
    ignores: [],
  },

  // Legacy config
  ...compat.config({
    extends: [
      "eslint:recommended",
      // Other extends...
    ],
  }),

  // Other flat configs...
);
```

> Note that `.eslintignore` no longer works in Flat config, see [customization](#customization) for more details.

</details>

### Add script for package.json

For example:

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint:fix": "eslint . --fix"
  }
}
```

## IDE Support (auto fix on save)

<details>
<summary>🟦 VS Code support</summary>

<br>

Install [VS Code ESLint extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

Add the following settings to your `.vscode/settings.json`:

```jsonc
{
  // Disable the default formatter, use eslint instead
  "prettier.enable": false,
  "editor.formatOnSave": false,

  // Auto fix
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "never",
  },

  // Silent the stylistic rules in you IDE, but still auto fix them
  "eslint.rules.customizations": [
    { "rule": "style/*", "severity": "off", "fixable": true },
    { "rule": "format/*", "severity": "off", "fixable": true },
    { "rule": "*-indent", "severity": "off", "fixable": true },
    { "rule": "*-spacing", "severity": "off", "fixable": true },
    { "rule": "*-spaces", "severity": "off", "fixable": true },
    { "rule": "*-order", "severity": "off", "fixable": true },
    { "rule": "*-dangle", "severity": "off", "fixable": true },
    { "rule": "*-newline", "severity": "off", "fixable": true },
    { "rule": "*quotes", "severity": "off", "fixable": true },
    { "rule": "*semi", "severity": "off", "fixable": true },
  ],

  // Enable eslint for all supported languages
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "html",
    "json",
    "jsonc",
    "svelte",
    "css",
    "less",
    "scss",
    "pcss",
    "postcss",
  ],
}
```

</details>

<details>
<summary>🟩 Neovim Support</summary>

<br>

Update your configuration to use the following:

```lua
local customizations = {
  { rule = 'style/*', severity = 'off', fixable = true },
  { rule = 'format/*', severity = 'off', fixable = true },
  { rule = '*-indent', severity = 'off', fixable = true },
  { rule = '*-spacing', severity = 'off', fixable = true },
  { rule = '*-spaces', severity = 'off', fixable = true },
  { rule = '*-order', severity = 'off', fixable = true },
  { rule = '*-dangle', severity = 'off', fixable = true },
  { rule = '*-newline', severity = 'off', fixable = true },
  { rule = '*quotes', severity = 'off', fixable = true },
  { rule = '*semi', severity = 'off', fixable = true },
}

local lspconfig = require('lspconfig')
-- Enable eslint for all supported languages
lspconfig.eslint.setup(
  {
    filetypes = {
      "javascript",
      "javascriptreact",
      "javascript.jsx",
      "typescript",
      "typescriptreact",
      "typescript.tsx",
      "html",
      "json",
      "jsonc",
      "svelte",
      "css",
      "less",
      "scss",
      "pcss",
      "postcss"
    },
    settings = {
      -- Silent the stylistic rules in you IDE, but still auto fix them
      rulesCustomizations = customizations,
    },
  }
)
```

### Neovim format on save

There's few ways you can achieve format on save in neovim:

- `nvim-lspconfig` has a `EslintFixAll` command predefined, you can create a autocmd to call this command after saving file.

```lua
lspconfig.eslint.setup({
  --- ...
  on_attach = function(client, bufnr)
    vim.api.nvim_create_autocmd("BufWritePre", {
      buffer = bufnr,
      command = "EslintFixAll",
    })
  end,
})
```

- Use [conform.nvim](https://github.com/stevearc/conform.nvim).
- Use [none-ls](https://github.com/nvimtools/none-ls.nvim)
- Use [nvim-lint](https://github.com/mfussenegger/nvim-lint)

</details>

## Customization

Since v1.0, we migrated to [ESLint Flat config](https://eslint.org/docs/latest/use/configure/configuration-files-new). It provides much better organization and composition.

Normally you only need to import the `in5net` preset:

```js
// eslint.config.js
import in5net from "@in5net/eslint-config";

export default in5net();
```

And that's it! Or you can configure each integration individually, for example:

```js
// eslint.config.js
import in5net from "@in5net/eslint-config";

export default in5net({
  // Type of the project. 'lib' for libraries, the default is 'app'
  type: "lib",

  // Or customize the stylistic rules
  stylistic: {
    indent: 2, // 4, or 'tab'
    quotes: "single", // or 'double'
  },

  // TypeScript is autodetected, you can also explicitly enable it:
  typescript: true,

  // Disable jsonc
  jsonc: false,

  // `.eslintignore` is no longer supported in Flat config, use `ignores` instead
  ignores: [
    "**/fixtures",
    // ...globs
  ],
});
```

The `in5net` factory function also accepts any number of arbitrary custom config overrides:

```js
// eslint.config.js
import in5net from "@in5net/eslint-config";

export default in5net(
  {
    // Configures for in5net's config
  },

  // From the second arguments they are ESLint Flat Configs
  // you can have multiple configs
  {
    files: ["**/*.ts"],
    rules: {},
  },
  {
    rules: {},
  },
);
```

Going more advanced, you can also import fine-grained configs and compose them as you wish:

<details>
<summary>Advanced Example</summary>

We wouldn't recommend using this style in general unless you know exactly what they are doing, as there are shared options between configs and might need extra care to make them consistent.

```js
// eslint.config.js
import {
  combine,
  comments,
  ignores,
  imports,
  javascript,
  jsdoc,
  jsonc,
  node,
  sortPackageJson,
  sortTsconfig,
  typescript,
  unicorn,
} from "@in5net/eslint-config";

export default combine(
  ignores(),
  javascript(/* Options */),
  comments(),
  node(),
  jsdoc(),
  imports(),
  unicorn(),
  typescript(/* Options */),
  jsonc(),
);
```

</details>

Check out the [configs](https://github.com/in5net/eslint-config/blob/main/src/configs) and [factory](https://github.com/in5net/eslint-config/blob/main/src/factory.ts) for more details.

> Thanks to [sxzz/eslint-config](https://github.com/sxzz/eslint-config) for the inspiration and reference.

### Plugins Renaming

Since flat config requires us to explicitly provide the plugin names (instead of the mandatory convention from npm package name), we renamed some plugins to make the overall scope more consistent and easier to write.

| New Prefix | Original Prefix        | Source Plugin                                                                              |
| ---------- | ---------------------- | ------------------------------------------------------------------------------------------ |
| `import/*` | `import-x/*`           | [eslint-plugin-import-x](https://github.com/un-es/eslint-plugin-import-x)                  |
| `node/*`   | `n/*`                  | [eslint-plugin-n](https://github.com/eslint-community/eslint-plugin-n)                     |
| `ts/*`     | `@typescript-eslint/*` | [@typescript-eslint/eslint-plugin](https://github.com/typescript-eslint/typescript-eslint) |

When you want to override rules, or disable them inline, you need to update to the new prefix:

```diff
-// eslint-disable-next-line @typescript-eslint/consistent-type-definitions
+// eslint-disable-next-line ts/consistent-type-definitions
type foo = { bar: 2 }
```

> [!NOTE]
> About plugin renaming - it is actually rather a dangrous move that might leading to potential naming collisions, pointed out [here](https://github.com/eslint/eslint/discussions/17766) and [here](https://github.com/prettier/eslint-config-prettier#eslintconfigjs-flat-config-plugin-caveat). As this config also very **personal** and **opinionated**, I ambitiously position this config as the only **"top-level"** config per project, that might pivots the taste of how rules are named.
>
> This config cares more about the user-facings DX, and try to ease out the implementation details. For example, users could keep using the semantic `import/order` without ever knowing the underlying plugin has migrated twice to `eslint-plugin-i` and then to `eslint-plugin-import-x`. User are also not forced to migrate to the implicit `i/order` halfway only because we swapped the implementation to a fork.
>
> That said, it's probably still not a good idea. You might not want to doing this if you are maintaining your own eslint config.
>
> Feel free to open issues if you want to combine this config with some other config presets but faced naming collisions. I am happy to figure out a way to make them work. But at this moment I have no plan to revert the renaming.

Since v2.9.0, this preset will automatically rename the plugins also for your custom configs. You can use the original prefix to override the rules directly.

<details>
<summary>Change back to original prefix</summary>

If you really want to use the original prefix, you can revert the plugin renaming by:

```ts
import in5net from "@in5net/eslint-config";

export default in5net().renamePlugins({
  ts: "@typescript-eslint",
  yaml: "yml",
  node: "n",
  // ...
});
```

</details>

### Rules Overrides

Certain rules would only be enabled in specific files, for example, `ts/*` rules would only be enabled in `.ts` files and `svelte/*` rules would only be enabled in `.svelte` files. If you want to override the rules, you need to specify the file extension:

```js
// eslint.config.js
import in5net from "@in5net/eslint-config";

export default in5net(
  {
    svelte: true,
    typescript: true,
  },
  {
    // Remember to specify the file glob here, otherwise it might cause the svelte plugin to handle non-svelte files
    files: ["**/*.svelte"],
    rules: {
      "svelte/valid-prop-names-in-kit-pages": "off",
    },
  },
  {
    // Without `files`, they are general rules for all files
    rules: {
      "style/semi": ["error", "never"],
    },
  },
);
```

We also provided the `overrides` options in each integration to make it easier:

```js
// eslint.config.js
import in5net from '@in5net/eslint-config'

export default in5net({
  svelte: {
    overrides: {
      'svelte/valid-prop-names-in-kit-pages': 'off,
    },
  },
  typescript: {
    overrides: {
      'ts/consistent-type-definitions': ['error', 'interface'],
    },
  },
  yaml: {
    overrides: {
      // ...
    },
  },
})
```

### Config Composer

Since v2.10.0, the factory function `in5net()` returns a [`FlatConfigComposer` object from `eslint-flat-config-utils`](https://github.com/antfu/eslint-flat-config-utils#composer) where you can chain the methods to compose the config even more flexibly.

```js
// eslint.config.js
import in5net from "@in5net/eslint-config";

export default in5net()
  .prepend
  // some configs before the main config
  ()
  // overrides any named configs
  .override("in5net/imports", {
    rules: {
      "import/order": ["error", { "newlines-between": "always" }],
    },
  })
  // rename plugin prefixes
  .renamePlugins({
    "old-prefix": "new-prefix",
    // ...
  });
// ...
```

### Svelte

Svelte support is detected automatically by checking if `svelte` is installed in your project. You can also explicitly enable/disable it:

```js
// eslint.config.js
import in5net from "@in5net/eslint-config";

export default in5net({
  svelte: true,
});
```

### Optional Rules

This config also provides some optional plugins/rules for extended usage.

#### `command`

Powered by [`eslint-plugin-command`](https://github.com/antfu/eslint-plugin-command). It is not a typical rule for linting, but an on-demand micro-codemod tool that triggers by specific comments.

For a few triggers, for example:

- `/// to-function` - converts an arrow function to a normal function
- `/// to-arrow` - converts a normal function to an arrow function
- `/// to-for-each` - converts a for-in/for-of loop to `.forEach()`
- `/// to-for-of` - converts a `.forEach()` to a for-of loop
- `/// keep-sorted` - sorts an object/array/interface
- ... etc. - refer to the [documentation](https://github.com/antfu/eslint-plugin-command#built-in-commands)

You can add the trigger comment one line above the code you want to transform, for example (note the triple slash):

<!-- eslint-skip -->

```ts
/// to-function
const foo = async (msg: string): void => {
  console.log(msg);
};
```

Will be transformed to this when you hit save with your editor or run `eslint . --fix`:

```ts
async function foo(msg: string): void {
  console.log(msg);
}
```

The command comments are usually one-off and will be removed along with the transformation.

### Type Aware Rules

You can optionally enable the [type aware rules](https://typescript-eslint.io/linting/typed-linting/) by passing the options object to the `typescript` config:

```js
// eslint.config.js
import in5net from "@in5net/eslint-config";

export default in5net({
  typescript: {
    tsconfigPath: "tsconfig.json",
  },
});
```

### Lint Staged

If you want to apply lint and auto-fix before every commit, you can add the following to your `package.json`:

```json
{
  "simple-git-hooks": {
    "pre-commit": "pnpm lint-staged"
  },
  "lint-staged": {
    "*": "eslint --fix"
  }
}
```

and then

```bash
npm i -D lint-staged simple-git-hooks

// to active the hooks
npx simple-git-hooks
```

## View what rules are enabled

[antfu](https://github.com/antfu) built a visual tool to help you view what rules are enabled in your project and apply them to what files, [@eslint/config-inspector](https://github.com/eslint/config-inspector)

Go to your project root that contains `eslint.config.js` and run:

```bash
npx @eslint/config-inspector
```

## Versioning Policy

This project follows [Semantic Versioning](https://semver.org/) for releases. However, since this is just a config and involves opinions and many moving parts, we don't treat rules changes as breaking changes.

### Changes Considered as Breaking Changes

- Node.js version requirement changes
- Huge refactors that might break the config
- Plugins made major changes that might break the config
- Changes that might affect most of the codebases

### Changes Considered as Non-breaking Changes

- Enable/disable rules and plugins (that might become stricter)
- Rules options changes
- Version bumps of dependencies

## Check Also

- [antfu/eslint-config](https://github.com/antfu/eslint-config) - The original ESLint config this was forked from

## License

[MIT](./LICENSE) License &copy; 2019-PRESENT [Anthony Fu](https://github.com/antfu)
