## Setup

1. Use VSCode, make sure the recommended eslint and prettier plugins are installed. Automatic linting should occur when you save!
2. `npx husky install`. Automatic linting should occur when you commit!

## Commands

- `npm run dev`: Runs the local Next.js dev server.
- `npm run build`: Generates the Next.js production build.
- `npm start`: Starts the Next.js production build.

## Steps to reproduce template

Note that the actual commits in this repo do not exactly reflect these steps in the same order

`npx create-next-app --ts`

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
