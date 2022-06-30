## Setup

1. `npm i`
2. Use VSCode, make sure the recommended eslint and prettier plugins are installed. Automatic linting should occur when you save!
3. `npx husky install`. Automatic linting should occur when you commit!

## Commands

- `npm run dev`: Runs the local Next.js dev server.
- `npm run build`: Generates the Next.js production build.
- `npm start`: Starts the Next.js production build.

## Steps to reproduce template

Note that the actual commits in this repo do not exactly reflect these steps in the same order

`npx create-next-app --ts`

Add `.env` to `.gitignore`.

Add `"baseUrl": "."` to `"compilerOptions"` in `tsconfig.json`

Add to `.eslintrc.json`:

```json
"rules": {
  "eqeqeq": "error"
}
```

[Linting stuff](https://paulintrognon.fr/blog/typescript-prettier-eslint-next-js):

In folder `.vscode`, create `settings.json`:

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

And `extensions.json`:

```json
{
  "recommendations": ["dbaeumer.vscode-eslint"]
}
```

`npm i -D @typescript-eslint/eslint-plugin`, add `"plugin:@typescript-eslint/recommended"` to `"extends"` in `.eslintrc.json`

`npm i -D prettier eslint-config-prettier`, add `"prettier"` to `"extends"` in `.eslintrc.json`

Add to `settings.json`:

```json
"editor.formatOnSave": true,
"editor.defaultFormatter": "esbenp.prettier-vscode"
```

Add `"esbenp.prettier-vscode"` to `extensions.json`

Create `.prettierrc.json`:

```json
{
  "singleQuote": true
}
```

Create `.prettierignore`:

```
.next
```

`npm i -D husky lint-staged`, `npx husky install`, `npx husky add .husky/pre-commit "npx lint-staged"`

Create `lint-staged.config.js`:

```js
module.exports = {
  '**/*.(ts|tsx)': () => 'npx tsc --noEmit',
  '**/*.(ts|tsx|js)': (filenames) => [
    `npx eslint --fix ${filenames.join(' ')}`,
    `npx prettier --write ${filenames.join(' ')}`,
  ],
  '**/*.(md|json)': (filenames) =>
    `npx prettier --write ${filenames.join(' ')}`,
};
```

[Twin styled-components](https://github.com/ben-rogerson/twin.examples/tree/master/next-styled-components):

`npm i styled-components`, `npm i -D twin.macro tailwindcss babel-plugin-macros @types/styled-components`

Create `components/GlobalStyles.tsx`:

```js
import { createGlobalStyle } from 'styled-components';
import { GlobalStyles as BaseStyles } from 'twin.macro';

const CustomStyles = createGlobalStyle`
  body {}
`;

const GlobalStyles = () => (
  <>
    <BaseStyles />
    <CustomStyles />
  </>
);

export default GlobalStyles;
```

Modify `pages/_app.tsx`:

```js
import GlobalStyles from 'components/GlobalStyles';
import type { AppProps } from 'next/app';

const App = ({ Component, pageProps }: AppProps) => (
  <div>
    <GlobalStyles />
    <Component {...pageProps} />
  </div>
);

export default App;
```

Create `babel-plugin-macros.config.js`:

```js
module.exports = {
  twin: {
    preset: 'styled-components',
    autoCssProp: false,
  },
};
```

Create `.babelrc.js`:

```js
module.exports = {
  presets: [['next/babel', { 'preset-react': { runtime: 'automatic' } }]],
  plugins: [
    'babel-plugin-macros',
    ['babel-plugin-styled-components', { ssr: true }],
  ],
};
```

Create `_document.tsx`:

```js
import Document, { DocumentContext } from 'next/document';
import { ServerStyleSheet } from 'styled-components';

export default class MyDocument extends Document {
  static async getInitialProps(ctx: DocumentContext) {
    const sheet = new ServerStyleSheet();
    const originalRenderPage = ctx.renderPage;
    try {
      ctx.renderPage = () =>
        originalRenderPage({
          enhanceApp: (App) => (props) =>
            sheet.collectStyles(<App {...props} />),
        });
      const initialProps = await Document.getInitialProps(ctx);

      return {
        ...initialProps,
        styles: (
          <>
            {initialProps.styles}
            {sheet.getStyleElement()}
          </>
        ),
      };
    } finally {
      sheet.seal();
    }
  }
}
```

Typescript does not like twin.macro and styled-components together for some reason, I searched a bit and it seems like creating `types/twin.d.ts` solves the problem (IDK why, from [here](https://github.com/ben-rogerson/twin.examples/blob/master/webpack-styled-components-typescript/types/twin.d.ts)):

```js
import 'twin.macro';
import styledImport, { CSSProp, css as cssImport } from 'styled-components';

declare module 'twin.macro' {
  // The styled and css imports
  const styled: typeof styledImport;
  const css: typeof cssImport;
}

declare module 'react' {
  // The css prop
  interface HTMLAttributes<T> extends DOMAttributes<T> {
    css?: CSSProp;
    tw?: string;
  }
  // The inline svg css prop
  interface SVGProps<T> extends SVGProps<SVGSVGElement> {
    css?: CSSProp;
    tw?: string;
  }
}

// The 'as' prop on styled components
declare global {
  namespace JSX {
    interface IntrinsicAttributes<T> extends DOMAttributes<T> {
      as?: string;
    }
  }
}
```

Removed `styles/` for our own styling system!
