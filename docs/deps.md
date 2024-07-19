# Советы по работе с зависимостями

## Updating Dependencies

To check the freshness of project dependencies, the npm package manager has a built-in command `npm outdated`. Its output may look something like this:

```sh
Package             Current   Wanted   Latest  Location
autoprefixer         10.2.6   10.3.1   10.3.1  global
esbuild              0.12.9  0.12.15  0.12.15  global
eslint               7.29.0   7.31.0   7.31.0  global
gulp-esbuild          0.8.2    0.8.3    0.8.3  global
lint-staged          11.0.0   11.0.1   11.0.1  global
markdown-it          12.0.6   12.1.0   12.1.0  global
markdown-it-anchor    8.0.3    8.1.0    8.1.0  global
simple-git-hooks      2.4.1    2.5.1    2.5.1  global
```

From the table, it can be seen that some dependencies have updated minor or patch versions. In such cases, it is safe (from a semantic versioning perspective) to run the command `npm update` or `npm update <package-name>`.

Some dependencies have an updated major version. Before installing it, it is useful to find out what changes have been made to the package. These could be significant changes to the external API or the library's core. Usually, the changes are documented in files like _CHANGELOG_, _CHANGELOG.md_, or _NEWS_ in the project's repository. You can navigate to the repository using the command `npm repo <package-name>`.

If everything looks good, you can update the major version of the package using `npm i <package-name>@latest`. After the update, it is essential to verify the correctness of the `npm run build` command and its results.

There is a way to update all dependencies to their latest major versions using a third-party package called `npm-check-updates`:

```
npx npm-check-updates -u
npm install
```

## Resolving Conflicts in the _package-lock.json_ File

Sometimes, merge conflicts can occur in Git for the _package-lock.json_ file. Editing this file manually should only be done in exceptional situations.

To resolve conflicts in this file, you can follow the algorithm below:

- Resolve conflicts (if any) in the _package.json_ file.
- Accept changes in _package-lock.json_ from the branch you are merging into your own.
- Update the _package-lock.json_ file by running the command `npm install`.

