
Developing OpenUI5
==============

This page explains the initial setup, development workflow, and how tests are executed.

Setting up the OpenUI5 development environment
------------------------------------------
OpenUI5 content is developed in an environment based on Node.js. The [UI5 Build and Development Tooling](https://github.com/SAP/ui5-tooling) is used as development server and build tool.

### Basic Setup
This setup allows to start a server for the OpenUI5 project in an easy way.

1. Install [Node.js](http://nodejs.org/). This also includes npm, the [node package manager](https://docs.npmjs.com/getting-started/what-is-npm)
2. Clone the OpenUI5 Git repository (you can download and install Git from [git-scm.com](http://git-scm.com/download))
```sh
git clone https://github.com/SAP/openui5.git
```
3. Install all npm dependencies (optionally, you can use Yarn for this, see [Advanced Setup](#advanced-setup))
```sh
cd openui5
npm install
```
4. Start the server and open the TestSuite
```sh
npm run testsuite
```
*To just start the server:*
```sh
npm start
```
5. Your default browser should open automatically and show the TestSuite - **You're done!**

#### Configuring the TestSuite server
The OpenUI5 TestSuite server can be configured using environment variables. For example to allow remote access to the server (i.e. from an interface other than your computers loopback/localhost) you can configure the server like this:
```sh
OPENUI5_SRV_ACC_RMT_CON=true npm start
```

##### Available server configurations
- `OPENUI5_SRV_OPEN=index.html`
- `OPENUI5_SRV_ACC_RMT_CON=true`
- `OPENUI5_SRV_PORT=9090`

### Advanced Setup
The basic setup described above makes use of a custom setup focused on starting the [UI5 Server](https://github.com/SAP/ui5-server) for the OpenUI5 TestSuite project in an easy way.

The advanced setup allows you to use the [UI5 CLI](https://github.com/SAP/ui5-cli) and all of its features. The use of [Yarn](https://yarnpkg.com) is required in this setup, as [npm can't handle workspaces yet](https://github.com/SAP/ui5-tooling#whats-the-thing-with-yarn).

**You need to use the advanced setup if you plan to do any of the following:**
- **Build** an OpenUI5 project
- **Serve** a project with HTTPS or HTTP/2
- Use any of the other **[UI5 CLI](https://github.com/SAP/ui5-cli) features** and parameters

#### Setup
1. Install the UI5 Build and Development Tooling CLI globally
See [UI5 Tooling: Installing the UI5 CLI](https://github.com/SAP/ui5-tooling#installing-the-ui5-cli)
2. Install [Yarn](https://yarnpkg.com) from [here](https://yarnpkg.com/en/docs/install) (*also see [FAQ: What's the thing with Yarn?](https://github.com/SAP/ui5-tooling#whats-the-thing-with-yarn)*)
3. In the OpenUI5 repository root directory, install all dependencies using Yarn (this also links all OpenUI5 libraries between each other)
```sh
yarn
```
4. Navigate into the TestSuite project and start the UI5 Server
```sh
cd src/testsuite
ui5 serve --open index.html
```

#### Workflow
Now you can use the UI5 CLI in any of your local OpenUI5 libraries. Check the [UI5 CLI documentation](https://github.com/SAP/ui5-cli) for details.

Whenever you make changes to your OpenUI5 repositories `node_modules` directory (e.g. by executing `npm install`) you may need to recreate the links between the OpenUI5 libraries. You can always do this by executing `yarn` in the OpenUI5 root directory. Also see [FAQ: What's the thing with Yarn?](https://github.com/SAP/ui5-tooling#whats-the-thing-with-yarn)

### Legacy Setup
You can continue to use the legacy Grunt-based setup. However the setups described above are recommended for working with the OpenUI5 repository.

To use the legacy setup, execute `npm run start-grunt` (in the past this was the default `npm start` behavior).

For details please refer to the [legacy Grunt development environment documentation](developing_legacy_grunt.md).

#### Differences to the Standard Setups ([Basic](#basic-setup) and [Advanced](#advanced-setup))
1. `/testsuite` Path-prefix:

    Standard Setup: | `http://localhost:8080/test-resources/testsuite/testframe.html`
    :---- | :----
    Legacy Setup | `http://localhost:8080/testsuite/test-resources/testsuite/testframe.html`
2. SDK Documentation generated by [`grunt docs`](https://github.com/SAP/openui5/blob/master/docs/tools_legacy_grunt.md#docs) can only be served using the legacy setup


The Development Process
-----------------------

### Regular development: no build required

Just modify any source file and reload your browser. Now that's simple, isn't it?

This build-free development process does not feature optimized runtime performance (e.g. there are many small requests, which would not be acceptable for remote connections). But is the most convenient way to modify the OpenUI5 sources. Under the hood there are mainly two mechanisms applied that adapt the sources:

 * The Git repository path contains a folder named like the respective control library (e.g. "sap.m"), which is omitted at runtime. The UI5 CLI server is mapping the locations.
 * The CSS files are transformed (server-side) by the LESS pre-processor during the first request after a CSS file has been modified. This includes mirroring for right-to-left support.

### Working with other UI5 projects

When working on UI5 applications or libraries that already make use of the [`@openui5`-npm packages](https://www.npmjs.com/org/openui5) like the [OpenUI5 Sample App](https://github.com/SAP/openui5-sample-app), you can link your local OpenUI5 repository into that project. This allows you to make changes to the project itself as well as to the OpenUI5 libraries simultaneously and test them immediately.

A detailed step-by-step guide on how to achieve such a setup with the OpenUI5 Sample App can be found [here](https://github.com/SAP/openui5-sample-app#working-with-local-dependencies).

### Building UI5

The [UI5 Build and Development Tooling](https://github.com/SAP/ui5-tooling) is used to build a production-ready version of OpenUI5. Every library needs to be built individually. 

Usage:
```
ui5 build
```

The build is responsible for the following tasks:

 * Creation of the bundled library.css and library-RTL.css file for all available themes
 * Minification of CSS
 * Minification of JavaScript
 * Bundling the JavaScript modules of the libraries into a single library-preload.js file
 * Bundling of the most important UI5 Core modules into sap-ui-core.js

#### Troubleshooting

If you encounter errors like the one below, execute `yarn` in the OpenUI5 root directory: there might be new build tools required which need to be downloaded first.

```
Error: Cannot find module 'xyz'
```

Testing UI5
-----------

### Running the static code checks (ESLint)

All UI5 code must conform to a certain ruleset which is checked with ESLint (http://eslint.org/).  
To run an ESLint check, navigate to the root directory of the repository and execute:
```
npm run lint
```

### Running tests
Convenient, automated test execution is not yet possible with the UI5 Build and Development Tooling.  
Please refer to the [legacy Grunt development environment documentation](developing_legacy_grunt.md).
