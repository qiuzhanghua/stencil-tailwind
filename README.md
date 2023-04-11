# Stencil + TailwindCSS Template

## Getting Started

```bash
pnpm create stencil component stencil-tailwind
cd stencil-tailwind
pnpm i -D @fullhuman/postcss-purgecss @stencil-community/postcss autoprefixer cssnano postcss-replace tailwindcss
# for testing
pnpm install --save-dev @types/jest jest jest-cli
pnpm up
pnpx tailwindcss init
```

## Modify `stencil.config.ts`

```ts
import { Config } from '@stencil/core';
import { postcss } from "@stencil-community/postcss";
import autoprefixer from "autoprefixer";
import tailwindcss from "tailwindcss";
import cssnano from "cssnano";
import purgecss from "@fullhuman/postcss-purgecss";
import replace from "postcss-replace"

//purge function to keep only the classes used in EACH component
const purge = purgecss({
  content: ["./src/**/*.tsx", "./src/index.html"],
  safelist: [':host'],
  defaultExtractor: (content) => content.match(/[A-Za-z0-9-_:/]+/g) || [],
});

export const config: Config = {
  namespace: 'stencil-tailwind',
  outputTargets: [
    {
      type: 'dist',
      esmLoaderPath: '../loader',
    },
    {
      type: 'dist-custom-elements',
    },
    {
      type: 'docs-readme',
    },
    {
      type: 'www',
      serviceWorker: null, // disable service workers
    },
  ],
  plugins: [
    postcss({
      // add postcss plugins
      plugins: [
        // add tailwind css. Config file was added using `npx tailwindcss init`
        tailwindcss("./tailwind.config.js"),
        autoprefixer(),
        // shadow dom does not respect 'html' and 'body' styling, so we replace it with ':host' instead
        replace({ pattern: 'html', data: { replaceAll: ':host' } }),
        // purge and cssnano if production build
        ...(!process.argv.includes("--dev")
          ? [ purge, cssnano() ]
          : [])
      ]
    })
  ]
};
```

## Modify `tailwind.config.js`
```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.tsx", "./src/index.html"],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

## Modify `src/components/my-component/my-component.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Modify `src/components/my-component/my-component.tsx`

```tsx
render() {
  return <div class="text-purple-600">Hello, World! I'm {this.getText()}</div>;
}
```

## Start the dev server

```bash
pnpm start
```

and open http://localhost:3333/, you should see a purple text.


## Test
  ```bash
pnpm test
# maybe prompt to install specific version of jest:
# pnpm install --save-dev @types/jest@27.0.3 jest@27.0.3 jest-cli@27.4.5
# just do it 
# and modify `src/components/my-component/my-component.spec.tsx` 
# and run test again
```

## Thanks
https://medblocks.medium.com/setting-up-a-stencil-js-project-with-tailwind-css-for-beautiful-web-components-49d20e5c6be6

