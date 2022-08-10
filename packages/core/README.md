## Folder structure

- `.panda`
  - `design-tokens` (index.js, index.d.ts, index.css)
  - `styled-system` (index.js, index.d.ts)
  - `package.json`

```js
const tokens = {
  'colors.red.400': { value: '...', variable: '...' },
}

const tokenMap = {
  colors: [{ group: 'red', key: 'red.400', value: '...' }],
  fonts: [],
}

function getToken(path) {
  const { value } = tokens[path] || {}
  return value
}

function getTokenVar(path) {
  const { variable } = tokens[path] || {}
  return variable
}
```

```js
import { generateCssVar, generateDts, generateJs } from '@css-panda/generator'
import { createDictionary } from '@css-panda/dictionary'

const conf = new Conf()

const dict = createDictionary(config)

const cssVars = generateCssVar(dict, { root: ':root' })
const dts = generateDts(dict)
const files = generateJs(dict, { formats: ['esm', 'cjs'] })
```

```json
{
  "name": "dot-panda",
  "description": "...",
  "exports": {
    "./design-tokens": {
      "import": "./generated/design-tokens/index.mjs"
    },
    "./css": {
      "import": "./generated/css/index.mjs"
    }
  },
  "typeVersions": {
    "*": {
      "design-tokens": ["./generated/design-tokens"],
      "css": ["./generated/css"]
    }
  }
}
```

```js
import { definePackage, writePackage } from '@css-panda/generators'

const pkg = setupPackage({
  name: 'dot-panda',
  description: '...',
  dir: 'generated',
  exports: ['design-tokens', 'css'],
})

writePackage(pkg)

updateTsConfig({
  compilerOptions: {
    paths: {
      'design-system': ['./.panda'],
    },
  },
})

updateGitIgnore({ comment: '# Panda', path: '.css-panda' })
```

```ts
type ConditionType =
  | 'color-scheme'
  | 'resolution'
  | 'writing-mode'
  | 'pseudo'
  | 'selector'
  | 'viewport'
  | 'interaction-media'
  | 'reduced-motion'
  | 'reduced-data'
  | 'reduced-transparent'
  | 'contrast'

const conditions = {
  dark: {
    type: 'color-scheme',
    value: '[data-theme=dark]',
    colorScheme: 'dark',
  },
  darkDimmed: {
    type: 'color-scheme',
    value: '[data-theme=dark_dimmed]',
    colorScheme: 'dark',
  },
  ltr: {
    type: 'dir',
    value: '[dir=rtl]',
  },
  rtl: {
    type: 'dir',
    value: '[dir=rtl]',
  },
  hover: {
    type: 'pseudo',
    value: '&:hover',
  },
  focus: {
    type: 'pseudo',
    value: '&:focus',
  },
  sm: {
    type: 'viewport',
    value: '@media (min-width: 480px)',
  },
}
```

```ts
const defaults = {
  className: ({ prop, value, esc }) => `${prop}-${esc(value)}`,
}

const tt = defineConfig({
  utilities: [
    {
      properties: {
        display: {
          className: ({ value, esc, dashCase }) => `d-${value}`,
        },
        background: {
          className: ({ prop, value }) => `bg-${value}`,
          values: ({ tokens }) => ({
            ...tokens.colors,
            inherit: 'inherit',
          }),
        },
        color: {
          className: ({ prop, value }) => `text-${value}`,
          values: (tokens) => ({ ...tokens.colors }),
        },
        fill: { scale: 'colors' },
        lineClamp: {
          className: ({ props, value }) => `clamp-${value}`,
          values: {
            '1': {
              '--line-clamp': '1',
            },
          },
        },
      },
      shorthands: {
        bg: 'background',
      },
    },

    {
      properties: {
        strokeWidth: {
          values: { '1': '1px', 2: '2px' },
        },
      },
    },

    {
      properties: {
        paddingLeft: { scale: 'space', className: 'pl' },
        paddingRight: { scale: 'space', className: 'pr' },
        paddingX: {
          className: 'px',
          values({ theme, map }) {
            return map(theme.space, (value) => ({
              paddingLeft: value,
              paddingRight: value,
            }))
          },
        },
      },
      shorthands: {
        pl: 'paddingLeft',
        px: 'paddingX',
      },
    },
  ],
})
```