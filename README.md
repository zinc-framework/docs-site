# Платформа Доки

[![Linter Status](https://github.com/doka-guide/platform/actions/workflows/linting.yml/badge.svg?branch=main&event=push)](https://github.com/doka-guide/platform/actions/workflows/linting.yml)
[![W3C Validator](https://github.com/doka-guide/platform/actions/workflows/w3c-validator.yml/badge.svg?branch=main&event=push)](https://github.com/doka-guide/platform/actions/workflows/w3c-validator.yml)
[![Deployment Status](https://github.com/doka-guide/platform/actions/workflows/product-deploy.yml/badge.svg?branch=main&event=push)](https://github.com/doka-guide/platform/actions/workflows/product-deploy.yml)
[![Docker Status](https://github.com/doka-guide/platform/actions/workflows/docker-deploy.yml/badge.svg?branch=main&event=push)](https://github.com/doka-guide/platform/actions/workflows/docker-deploy.yml)

[⚠️ If the Doka website loads slowly or doesn't work at all](docs/load-fix.md)

Doka is a friendly encyclopedia for web developers. Our goal is to make web development documentation practical, understandable, and not boring.

Join our [Telegram community](https://t.me/doka_guide) to stay up to date with the latest news, or join the [chat](https://t.me/+qYFPI2mExuQxZTFi) to chat, ask questions, and have a good time.

This repository contains the platform for the "Doka" website. The platform collects articles from a [separate repository](https://github.com/doka-guide/content).

## How the website works

The "Doka" website is built on [Eleventy](https://www.11ty.dev). Using Nunjucks templates, Eleventy converts Markdown articles into HTML pages.

The project is built using GitHub Actions and hosted on a server, read more about deployment in the [deployment guide](./docs/deploy.md).

## How to work with it

To work with the platform, you will need [Node.js](https://nodejs.org/en/) and npm.

To run Doka locally, you need to:

1. Download the repository.
2. Make a copy of the `.env.example` file and name it `.env`. Set the necessary environment variables in it.
3. Install dependencies with the command `npm i`.
4. Start the local web server with the command `npm start`.

More options for running Doka locally can be found in the [running guide](docs/how-to-run.md).

---

The code is distributed under the [MIT license](LICENSE.md), fonts have their own licenses, read more in the [documentation](docs/license.md).

## How to run tests

We use [Jest](https://jestjs.io/docs/getting-started).
Add your tests by adding test files to the `__tests__` folder. It's best to name the test file the same as the file being tested.

Run the tests with the command `npm test`.
To run tests in `watch` mode, use the additional `--watch` flag: `npm test -- --watch`.

## How to debug

Run the command `npm run debug` and open the `chrome:://inspect` tab in Chrome.

Find the desired session in the list. Click `inspect` and start debugging.

By default, the debugger will stop immediately. To add more breakpoints, add `debugger;` to your code or find the desired file and set a breakpoint directly in the debugger interface.

