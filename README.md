## Steps

`npx create-next-app --ts`

Add `"baseUrl": "."` to `"compilerOptions"` in `tsconfig.json`

[Linting stuff](https://paulintrognon.fr/blog/typescript-prettier-eslint-next-js):

In folder `.vscode`, make `settings.json`:

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