# npm-typescript-greeter

Boilerplate for npm package with typescript

[Manual Setup](#Manual-Setup)

- [1. Create Package Folder](#1-Create-Package-Folder)
- [2. Create a git repository](#2-Create-a-git-repository)
- [3. Init npm Package](#3-Init-npm-Package)
- [4. Install typescript](#4-Install-typescript)
- [5. Formatting and Linting](#5-Formatting-and-Linting)
- [6. Setup git hook](#6-Setup-git-hook)
- [7. Your Greeter](#7-Your-Greeter)
- [8. ignore build folder](#8-ignore-build-folder)
- [9. Unit Test](#9-Unit-Test)
- [10. Write Unit Test](#10-Write-Unit-Test)
- [11. Add userfull npm scripts](#11-Add-userfull-npm-scripts)
- [12. Finish package.json](#12-Finish-package.json)
- [13. Push to repository](#13-Push-to-repository)
- [14. Publish package to NPM](#14-Publish-package-to-NPM)
- [15. Bumping a new version](#15-Bumping-a-new-version)
- [16. Publishing a Scoped Package](#16-Publishing-a-Scoped-Package)

[npm scripts](#npm-scripts)

## Manual Setup

### 1. Create Package Folder

```
mkdir my-package
cd my-package
```

### 2. Create a git repository

- init

  ```
  git init
  echo "# My Package" >> README.md
  ```

- add `.gitignore`

  ```
  touch .gitignore
  ```

  > copy ignore data into `.gitignore` from [Node.gitignore](https://github.com/github/gitignore/blob/master/Node.gitignore)

- push to your git repository

  ```
  git remote add origin <Git Repository Url>
  git push -u origin master
  ```

  > Replace `<Git Repository Url>` to be the remote repository url

### 3. Init npm Package

```
npm init --y
```

### 4. Install `typescript`

- #### install as DevDependency
  ```
  npm install --save-dev typescript
  ```
- #### init `tsconfig`
  ```
  tsc --init
  ```
- #### modify `.tsconfig.json`

  ```json
  {
    "compilerOptions": {
      "target": "es5",
      "module": "commonjs",
      "declaration": true,
      "outDir": "./lib",
      "strict": true
    },
    "include": ["src"],
    "exclude": ["node_modules", "**/__tests__/*"]
  }
  ```

  | Option        | Description                                                                                                                                                                                                   |
  | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
  | `target`      | We want to compile to `es5` since we want to build a package with browser compatibility.                                                                                                                      |
  | `module`      | Use commonjs for compatibility.                                                                                                                                                                               |
  | `declaration` | When you building packages, this should be true. Typescript will then also export type definitions together with the compiled javascript code so the package can be used with both Typescript and Javascript. |
  | `outDir`      | The javascript will be compiled to the lib folder.                                                                                                                                                            |
  | `include`     | All source files in the src folder                                                                                                                                                                            |
  | `exclude`     | We don’t want to transpile node_modules, neither tests since these are only used during development.                                                                                                          |

### 5. Formatting and Linting

- #### install `prettier`, `tslint`, and `tslint-config-prettier` as DevDependency

  ```
  npm install --save-dev prettier tslint tslint-config-prettier
  ```

- #### create `tslint.json`

  ```json
  {
    "extends": ["tslint:recommended", "tslint-config-prettier"],
    "rules": {
      "no-console": false,
      "object-literal-sort-keys": false,
      "member-access": false,
      "ordered-imports": false
    },
    "linterOptions": {
      "exclude": ["**/*.json", "node_modules"]
    }
  }
  ```

- #### create `.prettierrc`

  ```json
  {
    "trailingComma": "all",
    "tabWidth": 4,
    "semi": false,
    "singleQuote": true,
    "endOfLine": "lf",
    "printWidth": 120,
    "overrides": [
      {
        "files": ["*.md", "*.json", "*.yml", "*.yaml"],
        "options": {
          "tabWidth": 2
        }
      }
    ]
  }
  ```

- #### create `.editorconfig`

  ```
  # EditorConfig is awesome: https://EditorConfig.org

  # top-most EditorConfig file
  root = true

  # Unix-style newlines with a newline ending every file
  [*]
  end_of_line = lf
  insert_final_newline = true
  charset = utf-8
  indent_style = space
  indent_size = 4

  [{*.json,*.md,*.yml,*.*rc}]
  indent_style = space
  indent_size = 2
  ```

- #### add `npm scripts`

  ```json
  {
    "scripts": {
      "format": "prettier --write \"src/**/*.ts\" \"src/**/*.js\"",
      "lint": "tslint -p tsconfig.json"
    }
  }
  ```

  | Script   | Description                  |
  | -------- | ---------------------------- |
  | `format` | format your code by prettier |
  | `lint`   | run tslint                   |

### 6. Setup `git hook`

> Hook will run `npm test` before `git commit` and `git push`

- #### install

  ```
  npm install --save-dev husky @commitlint/config-conventional @commitlint/cli commitizen cz-conventional-changelog
  ```

- ### create `commitlint.config.js`

  ```js
  module.exports = {
    extends: ["@commitlint/config-conventional"]
  };
  ```

- ### create `.huskyrc`

  ```json
  {
    "hooks": {
      "pre-commit": "npm run format && npm run lint && npm test",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
  ```

- ### create `.czrc`

  ```json
  { "path": "cz-conventional-changelog" }
  ```

- #### add `npm scripts`

  ```json
  {
    "scripts": {
      "commit": "git-cz"
    }
  }
  ```

### 7. Your Greeter

```
cd my-package
mkdir src
cd src touch index.ts
```

- `index.ts`

  ```ts
  export const Greeter = (name: string) => `Hello ${name}`;
  ```

- #### add `npm scripts`

  ```json
  {
    "scripts": {
      "start": "tsc -w",
      "build": "tsc"
    }
  }
  ```

  | Script  | Description              |
  | ------- | ------------------------ |
  | `start` | developing in watch mode |
  | `build` | build your project       |

### 8. ignore build folder

> After `npm build` or `npm start`, `/lib` will be generated

> add `/lib` to `.gitignore`

### 9. Unit Test

- #### install as DevDependency

  `npm install --save-dev jest ts-jest @types/jest`

- #### create `jestconfig.json`

  ```json
  {
    "transform": {
      "^.+\\.(t|j)sx?$": "ts-jest"
    },
    "testRegex": "(/__tests__/.*|(\\.|/)(test|spec))\\.(jsx?|tsx?)$",
    "moduleFileExtensions": ["ts", "tsx", "js", "jsx", "json", "node"]
  }
  ```

- #### add `npm scripts`

  ```json
  {
    "scripts": {
      "test": "jest --config jestconfig.json"
    }
  }
  ```

  | Script | Description   |
  | ------ | ------------- |
  | `test` | run unit test |

### 10. Write Unit Test

- #### add `__tests__` folder into `src` for unit test files, and add `Greeter.test.ts`

  ```ts
  import { Greeter } from "../index";
  test("My Greeter", () => {
    expect(Greeter("Carl")).toBe("Hello Carl");
  });
  ```

- #### run test

  ```
  npm test
  ```

### 11. Add userfull npm scripts

```json
{
  "scripts": {
    "prepare": "npm run build",
    "prepublishOnly": "npm test && npm run lint",
    "preversion": "npm run lint",
    "version": "npm run format && git add -A src",
    "postversion": "git push && git push --tags"
  }
}
```

| Script           | Description                                                                                   |
| ---------------- | --------------------------------------------------------------------------------------------- |
| `prepare`        | `prepare` will run both BEFORE the package is packed and published, and on local npm install. |
| `prepublishOnly` | `prepublishOnly` will run BEFORE prepare and ONLY on `npm publish`.                           |
| `preversion`     | `preversion` will run before bumping a new package version.                                   |
| `version`        | `Version` will run after a new version has been bumped.                                       |
| `postversion`    | `Postversion` will run after the commit has been made.                                        |

### 12. Finish `package.json`

```json
{
  "name": "my-package",
  "description": "A nice greeter",
  "main": "lib/index.js",
  "types": "lib/index.d.ts"
}
```

| Script        | Description                                                              |
| ------------- | ------------------------------------------------------------------------ |
| `name`        | your package name, it will show in npm                                   |
| `description` | about your package                                                       |
| `main`        | tell `npm` where it can import the modules from                          |
| `types`       | tell `Typescript` and `Code-editors` where can find the type definitions |

## 13. Push to repository

```
git add .
git commit -m "feat: init"
git push
```

## 14. Publish package to NPM

- create an NPM account

  > signup at website [https://www.npmjs.com/signup](https://www.npmjs.com/signup)

  > or rum command `npm adduser`

- login to NPM

  ```
  npm login
  ```

- publish to NPM
  ```
  npm publish
  ```
  > `prepare` -> `lint` -> `prepublishOnly`

### 15. Bumping a new version

```
npm version patch
```

```
npm publish
```

### 16. Publishing a Scoped Package

- #### Creating a New Scoped Package

  ```
  npm init --scope=<org_scope>
  ```

  > `@org_scope/<pkg_name>`

- ### Publishing a Private Scoped Package

```
npm publish
```

- ### Publishing a Public Scoped Package

```
npm publish --access public
```

## `npm scripts`

```json
{
  "scripts": {
    "start": "tsc -w",
    "build": "tsc",
    "format": "prettier --write \"src/**/*.ts\" \"src/**/*.js\"",
    "lint": "tslint -p tsconfig.json",
    "commit": "git-cz",
    "test": "jest --config jestconfig.json",
    "prepare": "npm run build",
    "prepublishOnly": "npm test && npm run lint",
    "preversion": "npm run lint",
    "version": "npm run format && git add -A src",
    "postversion": "git push && git push --tags",
    "bump-version": "npm version patch",
    "publish-public": "npm publish --access public"
  }
}
```

| Script           | Description                                                                                   |
| ---------------- | --------------------------------------------------------------------------------------------- |
| `start`          | developing in watch mode                                                                      |
| `build`          | build your project                                                                            |
| `format`         | format your code by prettier                                                                  |
| `lint`           | run tslint                                                                                    |
| `commit`         |                                                                                               |
| `test`           | run unit test                                                                                 |
| `prepare`        | `prepare` will run both BEFORE the package is packed and published, and on local npm install. |
| `prepublishOnly` | `prepublishOnly` will run BEFORE prepare and ONLY on `npm publish`.                           |
| `preversion`     | `preversion` will run before bumping a new package version.                                   |
| `version`        | `Version` will run after a new version has been bumped.                                       |
| `postversion`    | `Postversion` will run after the commit has been made.                                        |
| `bump-version`   | bump a new patch version of the package                                                       |
| `publish-public` | publishing a public scoped package                                                            |
