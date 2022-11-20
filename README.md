# VSCode, MongoDB Playgrounds, Prettier and ESLint

My config which enables Prettier and ESLint for MongoDB Playgrounds in VSCode, whilst keeping the Intellisense and MongoDB Playground syntax highlighting.

This requires the following VSCode extensions:

- [MongoDB for VS Code](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)
- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
- [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

Once the extensions are installed I configured a simple Node project loosely following [Add Eslint, Prettier, and Airbnb Style Guide to Your Project](https://dev.to/saurabhggc/add-eslint-prettier-and-airbnb-to-your-project-3mo8), adding the [AirBnB styles](https://github.com/airbnb/javascript)

```bash
npm init -y
npm i eslint-plugin-import eslint-config-airbnb-base eslint-config-prettier eslint-plugin-prettier prettier -D
npx eslint --init
```

I use `.eslint.json` for configuration which I updated as follows

```json
{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": ["airbnb-base", "prettier"],
  "overrides": [
    {
      "files": ["**/*.js", "**/*.mongodb"]
    }
  ],
  "parserOptions": {
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "ignorePatterns": ["node_modules/"],
  "plugins": ["prettier"],
  "rules": {
    "no-console": "off",
    "prettier/prettier": "warn"
  }
}
```

Notice in the overrides section I have added `**/*.mongodb` to the files array. Which will allow ESLint to recognize `.mongodb` files and parse them as well.

Next I added `.prettierrc` which also includes `*.mongodb` in override files arrays.

```json
{
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "singleAttributePerLine": true,
  "overrides": [
    {
      "files": "*.mongodb",
      "options": {
        "parser": "flow"
      }
    }
  ]
}
```

All this config actually has an added bonus, in that in `package.json` I have defined the following scripts.

```json
"scripts": {
  "lint:check": "eslint ./",
  "lint:fix": "eslint --fix ./",
  "style:check": "prettier --check ./",
  "style:fix": "prettier --write ./",
  "mongo:check": "prettier --check '**/*.mongodb'",
  "mongo:fix": "prettier --write '**/*.mongodb'",
}
```

I can run these from command line, and they can also be used with pre-commit hooks.

Coming back to VSCode Config, there are a few settings that I added. For prettier there is a setting called `prettier.documentSelectors` and for ESLint there are `eslint.validate` and `eslint.probe`.

```json
//settings.json (extract)
"prettier.documentSelectors": ["**/*.mongodb"],
"eslint.probe": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "html",
    "vue",
    "markdown",
    "mongodb"
],
"eslint.validate": ["mongodb"],
```

Best to add the above settings into the user settings, although the seem to work even when added to the workspace settings (`.vscode/settings.json`).

After applying all these I now have ESLint linting the `*.mongodb` files and Prettier formatting them as well on save.

Pretty convenient when I can quickly build out and test Atlas Functions locally in playgrounds and then deploy them to Atlas.
