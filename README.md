# Semantic release

## Installation


* Initialize npm
  * ```npm init -y```

* Intall semantic-release and plugins. Click [here](https://semantic-release.gitbook.io/semantic-release/extending/plugins-list) to view all plugins
    ```
    npm i -D semantic-release \
    @semantic-release/commit-analyzer \
    @semantic-release/release-notes-generator \
    @semantic-release/changelog \
    @semantic-release/npm \
    @semantic-release/github \
    @semantic-release/git
    ```

* Update ```package.json``` with the following
  * Avoid publishing to npm registry  <br>
    ```"private": true,```
  * Configure release branches 
    ```
    "release": {
       "branches": ["main"]
    },
    ```

* Create ```release.config.js``` 
  ```
    module.exports = {
    "plugins": [
        "@semantic-release/commit-analyzer",
        "@semantic-release/release-notes-generator",
        "@semantic-release/changelog",
        ["@semantic-release/npm", {
        "tarballDir": "release"
        }],
        ["@semantic-release/github", {
        "assets": "release/*.tgz"
        }],
        "@semantic-release/git"
    ],
    "preset": "angular"
    }
  ```

* Create GitHub Action file  
  Click [here](https://github.com/semantic-release/semantic-release/blob/1405b94296059c0c6878fb8b626e2c5da9317632/docs/recipes/github-actions.md) for more information. Setup action file as shown
  ```
    name: Release
    on:
    push:
        branches:
        - main
    jobs:
    release:
        name: Release
        runs-on: ubuntu-18.04
        steps:
        - name: Checkout
            uses: actions/checkout@v1
        - name: Setup Node.js
            uses: actions/setup-node@v1
            with:
            node-version: 12
        - name: Install dependencies
            run: npm ci
        - name: Release
            env:
            GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
            NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
            run: npx semantic-release
  ```
  Note: GITHUB_TOKEN will be automatically generated. However, NPM_TOKEN will need to be generated and included on GitHub.

  * Generate npm token
    * Create an account on https://www.npmjs.com/
    * Click ```User icon``` on top right > ```Access token```
    * Click ```Generate new token```
    * Choose ```Automation``` and click ```Generate Token```.
    * The token will be displayed. Make a note of it.

  * Add npm token generated on GitHub
    * Navigate to the settings of your repository
    * Click ```Secrets``` > ```New repository secret```  
    * Write the name as NPM_TOKEN and the value as the token generated above.
    * Click Add secret

