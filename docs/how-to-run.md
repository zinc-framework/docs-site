# Как запустить Доку локально

To work with the platform, you will need [Node.js](https://nodejs.org/en/) and npm. We use the stable LTS version of Node.js and the version of npm that comes with it. If you have a different version of Node.js installed, you can use [nvm](https://github.com/nvm-sh/nvm) to switch to the required version.

## Minimum setup

To run the Docs locally, you need to:

1. Download the repository.
2. Install dependencies using the command `npm i`.
3. Make a copy of the `.env.example` file and name it `.env`. Set the required environment variables in it.
4. Start the local web server using the command `npm start`.

## Running with a service worker

To run the Docs with a service worker, you need to:

1. Follow all the steps mentioned in the previous section.
2. Add the `DOKA_MODE` variable to localStorage.
3. Set the value of the `DOKA_MODE` variable to `DEBUG`.

## Running with real content

1. Download the content and platform repositories into one folder.
2. Install dependencies using the command `npm i`.
3. Make a copy of the `.env.example` file and name it `.env`. Set the required environment variables in it:
  - `BASE_URL` - the base address for the website;
  - `SECTIONS` - the list of website sections;
  - `PATH_TO_CONTENT` - the path to the content repository;
  - `CONTENT_REP_FOLDERS` - folders with section content and build-related information;
  - `DOKA_ORG` - the path to the organization on GitHub;
  - `PLATFORM_REP_GITHUB_URL` - the path to the platform repository on GitHub;
  - `CONTENT_REP_GITHUB_URL` - the path to the content repository on GitHub;
  - `CONTENT_REP_GITHUB` - the link to the content repository on GitHub for working with Git;
  - `SERVER_PATH` - the absolute path to the folder on the server with the current build;
  - `GITHUB_TOKEN` - the token for working with GitHub's GraphQL (you can generate a personal token as described in the [instructions](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)).
4. Start the local web server using the command `npm start`.

If you leave the `GITHUB_TOKEN` field empty, the activity information on GitHub in the content repository will not be displayed on the participant pages.

