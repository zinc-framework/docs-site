# Деплой Доки

The documentation is hosted on the server as a set of static files.

## When we deploy

We deploy automatically when a pull request is merged into the `main` branch in the `content` or `platform` repositories.

## How we build

The project is built using [GitHub Actions](https://docs.github.com/en/actions).

Each repository has its own workflow for building, but they are identical in content:

- [Content workflow](https://github.com/doka-guide/content/blob/main/.github/workflows/product-deploy.yml)
- [Platform workflow](https://github.com/doka-guide/platform/blob/main/.github/workflows/product-deploy.yml)

The build consists of five stages:

1. Download the latest versions of the content and platform.
2. Install dependencies.
3. Connect articles to the platform.
4. Build the project.
5. Send the build folder to the server.

On the server side, no actions are taken except for publishing the provided build folder.

