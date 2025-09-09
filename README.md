# unplugin-auto-import

[![NPM version](https://img.shields.io/npm/v/unplugin-auto-import?color=a1b858&label=)](https://www.npmjs.com/package/unplugin-auto-import)
为 Vite、Webpack、Rspack、Rollup 和 esbuild 按需自动导入 API。支持 TypeScript。由 [unplugin](https://github.com/unjs/unplugin) 提供。
---

without

```ts
import { computed, ref } from 'vue'

const count = ref(0)
const doubled = computed(() => count.value * 2)
```

with

```ts
const count = ref(0)
const doubled = computed(() => count.value * 2)
```

---

without

```tsx
import { useState } from 'react'

export function Counter() {
  const [count, setCount] = useState(0)
  return <div>{ count }</div>
}
```

with

```tsx
export function Counter() {
  const [count, setCount] = useState(0)
  return <div>{ count }</div>
}
```

## Install

```bash
npm i -D unplugin-auto-import
```

<details>
<summary>Vite</summary><br>

```ts
// vite.config.ts
import AutoImport from 'unplugin-auto-import/vite'

export default defineConfig({
  plugins: [
    AutoImport({ /* options */ }),
  ],
})
```

Example: [`playground/`](./playground/)

<br></details>

<details>
<summary>Rollup</summary><br>

```ts
// rollup.config.js
import AutoImport from 'unplugin-auto-import/rollup'

export default {
  plugins: [
    AutoImport({ /* options */ }),
    // other plugins
  ],
}
```

<br></details>

<details>
<summary>Rolldown</summary><br>

```ts
// rolldown.config.js
import AutoImport from 'unplugin-auto-import/rolldown'

export default {
  plugins: [
    AutoImport({ /* options */ }),
    // other plugins
  ],
}
```

<br></details>

<details>
<summary>Webpack</summary><br>

```ts
// webpack.config.js
module.exports = {
  /* ... */
  plugins: [
    require('unplugin-auto-import/webpack')({ /* options */ }),
  ],
}
```

<br></details>

<details>
<summary>Rspack</summary><br>

```ts
// rspack.config.js
module.exports = {
  /* ... */
  plugins: [
    require('unplugin-auto-import/rspack')({ /* options */ }),
  ],
}
```

<br></details>

<details>
<summary>Nuxt</summary><br>

> You **don't need** this plugin for Nuxt, it's already built-in.

<br></details>

<details>
<summary>Vue CLI</summary><br>

```ts
// vue.config.js
module.exports = {
  /* ... */
  plugins: [
    require('unplugin-auto-import/webpack')({ /* options */ }),
  ],
}
```

You can also rename the Vue configuration file to `vue.config.mjs` and use static import syntax (you should use latest `@vue/cli-service ^5.0.8`):
```ts
// vue.config.mjs
import AutoImport from 'unplugin-auto-import/webpack'

export default {
  configureWebpack: {
    plugins: [
      AutoImport({ /* options */ }),
    ],
  },
}
```

<br></details>

<details>
<summary>Quasar</summary><br>

```ts
// vite.config.js [Vite]
import AutoImport from 'unplugin-auto-import/vite'
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [
    AutoImport({ /* options */ })
  ]
})
```

```ts
// quasar.config.js
export default defineConfig(() => {
  return {
    build: {
      vitePlugins: [
        ['unplugin-auto-import/vite', { /* options */ }],
      ]
    },
  }
})
```

<br></details>

<details>
<summary>esbuild</summary><br>

```ts
// esbuild.config.js
import { build } from 'esbuild'
import AutoImport from 'unplugin-auto-import/esbuild'

build({
  /* ... */
  plugins: [
    AutoImport({
      /* options */
    }),
  ],
})
```

<br></details>

<details>
<summary>Astro</summary><br>

```ts
// astro.config.mjs
import AutoImport from 'unplugin-auto-import/astro'

export default defineConfig({
  integrations: [
    AutoImport({
      /* options */
    })
  ],
})
```

<br></details>

## 配置

```ts
AutoImport({
  // 要转换的目标文件
  include: [
    /\.[tj]sx?$/, // .ts, .tsx, .js, .jsx
    /\.vue$/,
    /\.vue\?vue/, // .vue
    /\.vue\.[tj]sx?\?vue/, // .vue（启用 vue-loader 的 experimentalInlineMatchResource）
    /\.md$/, // .md
  ],

  // 要注册的全局导入
  imports: [
    // 预设
    'vue',
    'vue-router',
    // 自定义
    {
      '@vueuse/core': [
        // 命名导入
        'useMouse', // import { useMouse } from '@vueuse/core',
        // 别名
        ['useFetch', 'useMyFetch'], // import { useFetch as useMyFetch } from '@vueuse/core',
      ],
      'axios': [
        // 默认导入
        ['default', 'axios'], // import { default as axios } from 'axios',
      ],
      '[package-name]': [
        '[import-names]',
        // 别名
        ['[from]', '[alias]'],
      ],
    },
    // 示例类型导入
    {
      from: 'vue-router',
      imports: ['RouteLocationRaw'],
      type: true,
    },
  ],

  // 包含需要过滤掉的导入的正则表达式数组
  ignore: [
    'useMouse',
    'useFetch'
  ],

  // 按文件名启用默认模块导出的自动导入
  defaultExportByFilename: false,

  // 扫描目录以进行自动导入的选项
  dirsScanOptions: {
    filePatterns: ['*.ts'], // 用于匹配文件的 Glob 模式
    fileFilter: file => file.endsWith('.ts'), // 过滤文件
    types: true // 启用目录下的类型自动导入
  },

  // 目录下模块导出的自动导入
  // 默认仅扫描目录下的一级模块
  dirs: [
    './hooks',
    './composables', // 仅根模块
    './composables/**', // 所有嵌套模块
    // ...

    {
      glob: './hooks',
      types: true // 启用类型导入
    },
    {
      glob: './composables',
      types: false // 如果顶级的 dirsScanOptions.types 启用了导入，此目录仅禁用类型导入
    }
    // ...
  ],

  // 生成对应的 .d.ts 文件的路径
  // 当本地安装了 `typescript` 时，默认为 './auto-imports.d.ts'
  // 设置为 `false` 以禁用
  dts: './auto-imports.d.ts',

  // 生成 .d.ts 文件的模式
  // 'overwrite': 用新的类型定义覆盖整个现有的 .d.ts 文件
  // 'append': 仅将新的类型定义追加到现有的 .d.ts 文件，保留现有类型定义
  // 默认为 'append'
  dtsMode: 'append',

  // 在生成声明文件时，包含需要忽略的导入的正则表达式数组
  // 当需要为函数提供自定义签名时可能有用
  ignoreDts: [
    'ignoredFunction',
    /^ignore_/
  ],

  // 在 Vue 模板中启用自动导入
  // 参见 https://github.com/unjs/unimport/pull/15 和 https://github.com/unjs/unimport/pull/72
  vueTemplate: false,

  // 在 Vue 模板中启用指令的自动导入
  // 参见 https://github.com/unjs/unimport/pull/374
  vueDirectives: undefined,

  // 自定义解析器，兼容 `unplugin-vue-components`
  // 参见 https://github.com/antfu/unplugin-auto-import/pull/23/
  resolvers: [
    /* ... */
  ],

  // 将自动导入的包包含在 Vite 的 `optimizeDeps` 选项中
  // 推荐启用
  viteOptimizeDeps: true,

  // 在其他导入的末尾注入导入
  injectAtEnd: true,

  // 生成对应的 .eslintrc-auto-import.json 文件
  // ESLint 全局变量文档 - https://eslint.org/docs/user-guide/configuring/language-options#specifying-globals
  eslintrc: {
    enabled: false, // 默认 `false`
    // 提供以 `.mjs` 或 `.cjs` 结尾的路径以生成相应格式的文件
    filepath: './.eslintrc-auto-import.json', // 默认 `./.eslintrc-auto-import.json`
    globalsPropValue: true, // 默认 `true`，(true | false | 'readonly' | 'readable' | 'writable' | 'writeable')
  },

  // 生成对应的 .biomelintrc-auto-import.json 文件
  // BiomeJS 扩展文档 - https://biomejs.dev/guides/how-biome-works/#the-extends-option
  biomelintrc: {
    enabled: false, // 默认 `false`
    filepath: './.biomelintrc-auto-import.json', // 默认 `./.biomelintrc-auto-import.json`
  },

  // 将 unimport 项保存为 JSON 文件，供其他工具使用
  dumpUnimportItems: './auto-imports.json', // 默认 `false`
})
```

Refer to the [type definitions](./src/types.ts) for more options.

## Presets

See [src/presets](./src/presets).

## Package Presets

We only provide presets for the most popular packages, to use any package not included here you can install it as dev dependency and add it to the `packagePresets` array option:
```ts
AutoImport({
  /* other options */
  packagePresets: ['detect-browser-es'/* other local package names */]
})
```

You can check the [Svelte example](./examples/vite-svelte) for a working example registering `detect-browser-es` package preset and auto importing `detect` function in [App.svelte](./examples/vite-svelte/src/App.svelte).

Please refer to the [unimport PackagePresets jsdocs](https://github.com/unjs/unimport/blob/main/src/types.ts) for more information about options like `ignore` or `cache`.

**Note**: ensure local packages used have package exports configured properly, otherwise the corresponding modules exports will not be detected.

## TypeScript

In order to properly hint types for auto-imported APIs

<table>
<tr>
<td width="400px" valign="top">

1. Enable `options.dts` so that `auto-imports.d.ts` file is automatically generated
2. Make sure `auto-imports.d.ts` is not excluded in `tsconfig.json`

</td>
<td width="600px"><br>

```ts
AutoImport({
  dts: true // or a custom path
})
```

</td>
</tr>
</table>

## ESLint

> 💡 When using TypeScript, we recommend to **disable** `no-undef` rule directly as TypeScript already check for them and you don't need to worry about this.

If you have encountered ESLint error of `no-undef`:

<table>
<tr>
<td width="400px">

1. Enable `eslintrc.enabled`

</td>
<td width="600px"><br>

```ts
AutoImport({
  eslintrc: {
    enabled: true, // <-- this
  },
})
```

</td></tr></table>
<table><tr><td width="400px">

2. Update your `eslintrc`:
[Extending Configuration Files](https://eslint.org/docs/user-guide/configuring/configuration-files#extending-configuration-files)

</td>
<td width="600px"><br>

```ts
// .eslintrc.js
module.exports = {
  extends: [
    './.eslintrc-auto-import.json',
  ],
}
```

</td>
</tr>
</table>

## FAQ

### Compare to [`unimport`](https://github.com/unjs/unimport)

From v0.8.0, `unplugin-auto-import` **uses** `unimport` underneath. `unimport` is designed to be a lower-level tool (it also powered Nuxt's auto import). You can think `unplugin-auto-import` is a wrapper of it that provides more user-friendly config APIs and capabilities like resolvers. Development of new features will mostly happen in `unimport` from now.

### Compare to [`vue-global-api`](https://github.com/antfu/vue-global-api)

You can think of this plugin as a successor to `vue-global-api`, but offering much more flexibility and bindings with libraries other than Vue (e.g. React).

###### Pros

- Flexible and customizable
- Tree-shakable (on-demand transforming)
- No global population

###### Cons

- Relying on build tools integrations (while `vue-global-api` is pure runtime) - but hey, we have supported quite a few of them already!

## Sponsors

<p align="center">
  <a href="https://cdn.jsdelivr.net/gh/antfu/static/sponsors.svg">
    <img src='https://cdn.jsdelivr.net/gh/antfu/static/sponsors.svg'/>
  </a>
</p>

## License

[MIT](./LICENSE) License © 2021-PRESENT [Anthony Fu](https://github.com/antfu)
