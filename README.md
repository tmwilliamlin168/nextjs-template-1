## Steps

`npx create-next-app --ts`

Add `"baseUrl": "."` to `"compilerOptions"` in `tsconfig.json`

[Linting stuff](https://paulintrognon.fr/blog/typescript-prettier-eslint-next-js):

In folder `.vscode`, create `settings.json`:

```
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

And `extensions.json`:

```
{
  "recommendations": ["dbaeumer.vscode-eslint"]
}
```

`npm i -D @typescript-eslint/eslint-plugin`, add `"plugin:@typescript-eslint/recommended"` to `"extends"` in `.eslintrc.json`

`npm i -D prettier eslint-config-prettier`, add `"prettier"` to `"extends"` in `.eslintrc.json`

Add to `settings.json`:

```
"editor.formatOnSave": true,
"editor.defaultFormatter": "esbenp.prettier-vscode"
```

Add `"esbenp.prettier-vscode"` to `extensions.json`

Create `.prettierrc.json`:

```
{
  "singleQuote": true
}
```
