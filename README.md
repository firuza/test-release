# Semantic release

[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)

## Installation and Configuration


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

* Configure custom type and scope
  To configure a type and scope of your choice, modify the ```releaseRules``` under ```semantic-release/commit-analyzer``` in the ```release.config.js``` file accordingly. For example, by adding the following rule, the commit with type ```docs``` and scope ```README``` will be released as a patch release.
  ```{"type": "docs", "scope":"README", "release": "patch"},```

  The example commit message would be:
  ```docs(README): installation instructions updated```

## References
* https://www.wizeline.com/blog-continuous-deployment-with-semantic-release-and-github-actions/

* https://dev.to/kouts/automated-versioning-and-package-publishing-using-github-actions-and-semantic-release-1kce

* https://github.com/semantic-release/commit-analyzer#release-rules