# Как работает Дока
## Repositories

Doka lives on GitHub and operates based on several repositories:

- [Content](https://github.com/doka-guide/content), which contains all the materials;
- [Platform](https://github.com/doka-guide/platform), which contains all the build code and client code of the web application;
- [Backend](https://github.com/doka-guide/api), which contains the Doka API;
- [Search](https://github.com/doka-guide/search), which contains the API for content search.

Authors of Doka primarily interact with the **Content** repository, which contains docs, articles, practices for all sections, as well as materials from the "Interviewing" section.

**Platform** is based on the static site generator [11ty](https://www.11ty.dev). The site pages are styled as templates in the [Nunjucks](https://mozilla.github.io/nunjucks/) format.

**Backend** is a REST API implemented in Go. The API allows saving user-submitted forms and reactions to articles, and also serves as a platform for email newsletters.

**Search** contains an engine that enables searching through a pre-built inverted index. The index is generated during site deployment from the main branch.

The Content and Platform repositories are used for building the static site, while Backend and Search contribute to the site's interactivity.

All repositories are open, and we always welcome new contributors. The workflow in the repositories follows the GitHub Flow: there is a main branch that holds the current version of the product, and other branches with changes that undergo review in pull requests.

## Platform

To build the site, it is necessary to connect the Content. This is an independent repository that "knows nothing" about the Platform. The Platform, however, "knows" some things about the Content: paths, section list and colors, material settings, and the structure of material folders.

Separating content and platform into different repositories is an unconventional approach for working with an 11ty project. With this approach, we lose some of the standard engine functionality (e.g., automatic date updates based on git history). However, it allows us to:

- work with Doka materials directly without using a build process (assuming that all standard Markdown syntax is correctly processed on the platform side);
- develop the platform independently from the materials (e.g., no need to wait for the entire content to be built on each iteration, just working with typical materials is sufficient);
- create less noise for developers and authors in their respective repositories;
- conduct independent build testing;
- always get the latest version of content and its representation during builds in both content and platform;
- eliminate the need for authors to maintain the necessary development environment (the result can be previewed in the generated preview with each push to GitHub in a pull request).

### Constants and Default Behavior

**List of environment variables**:

- `BASE_URL` — base URL for the website;
- `SECTIONS` — list of main sections of the website;
- `PATH_TO_CONTENT` — path to the content repository;
- `CONTENT_REP_FOLDERS` — folders containing section content and build-related information;
- `DOKA_ORG` — path to the organization on GitHub;
- `PLATFORM_REP_GITHUB_URL` — path to the platform repository on GitHub;
- `CONTENT_REP_GITHUB_URL` — path to the content repository on GitHub;
- `CONTENT_REP_GITHUB` — link to the content repository on GitHub for Git operations;
- `SERVER_PATH` — absolute path to the folder on the server with the current build;
- `GITHUB_TOKEN` — token for working with the GitHub GraphQL API. [Instructions for generating a personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

The `settings` folder from the `CONTENT_REP_FOLDERS` list is mandatory for the build. If the `GITHUB_TOKEN` field is left empty, the activity information of contributors on GitHub will not be displayed on the content repository pages.

The list of Doka sections included in the website build is defined by the `SECTIONS` variable in the [_.env.example_](https://github.com/doka-guide/platform/blob/main/.env.example) files, where environment variables are set (more details on the required configuration before running can be found in the [documentation](https://github.com/doka-guide/platform/blob/main/docs/how-to-run.md)), and in [_constants.js_](https://github.com/doka-guide/platform/blob/main/config/constants.js), where default values are specified if they are not set in the environment variable file. Section material folders can be empty or even absent. The build will only consider the materials present in the specified folders. The folder structure for the section materials should be as follows:

```bash
section                         # Section folder
    └── doka-or-article           # Folder for the material
            ├── demos                 # Folder for demos
            │    └── first-demo       # Demo folder
            │         └── index.html  # Demo page (loaded in <iframe>)
            ├── images                # Folder for images
            │    └── picture.png      # Image files
            ├── practice              # Folder for the "On Practice" section
            │    └── author.md        # Text for the "On Practice" section from a specific author
            └── index.md              # Material text (guide / article)
```

The section colors are defined in the following files:
- [_category-colors.js_](https://github.com/doka-guide/platform/blob/main/config/category-colors.js);
- [_base-colors.css_](https://github.com/doka-guide/platform/blob/main/src/styles/base-colors.css) - base colors;
- [_light-theme.css_](https://github.com/doka-guide/platform/blob/main/src/styles/light-theme.css) - for the light theme;
- [_dark-theme.css_](https://github.com/doka-guide/platform/blob/main/src/styles/dark-theme.css) - for the dark theme.

#### Paths

The website is built from two repositories for the Platform, so it is necessary to prepare a set of paths with the Content materials before the build. This is done by running the script [_make-links.js_](https://github.com/doka-guide/platform/blob/main/make-links.js) in one of two modes: locally for users - in interactive mode, on the server - in deployment mode. By default, the Content folders are linked into the _src/_ folder.

Platform folder structure:

```bash
platform
    ├── .github         # GitHub service folder
    ├── config          # Configuration files folder
    ├── docs            # Doka Platform documentation
    └── src             # Source code for the build
        ├── data        # Folder for data connection (11ty entity)
        ├── fonts       # Folder for prepared fonts
        ├── images      # Folder for icons and non-content images of the site
        ├── includes    # Folder for templates of some blocks
        ├── layouts     # Folder for main page layouts
        ├── libs        # Folder for build libraries
        ├── scripts     # Folder for build and client scripts
        ├── styles      # Site styles
        ├── transforms  # Transforms (11ty entity)
        └── views       # Views of main site pages
```

#### Site Build

_.eleventy.js_ is the main file for the build. It contains the configuration for the 11ty engine.

11ty entities:

- Content: Texts on the site (in Doka, content files are stored in subfolders of the _src_ folder with extensions _*.md_ and _*.html_) and various media files.
- Templates: The main way to generate markup (in Doka, these are files with page or block markup with extensions _*.njk_).
- Data: Predefined or specially prepared JavaScript data needed for the build (in Doka, these are files with extensions _*.11tydata.js_).
- Collections: A special object in the engine that can store specially prepared data (in Doka, collections are defined in the main configuration file).
- Transformations: The process of processing finished site pages.
- Permalinks: Permanent addresses for pages that differ from the paths set by 11ty and are manually specified by the user. They are used to provide flexibility to the engine in terms of content file storage path.

The site build process follows these steps:

1. Searching for template and content files in the repository based on the specified paths.
2. Iterating through all found files:
    - Creating a list of all non-content files (in Doka, these include fonts, styles, client scripts, icons, photos, videos, etc.).
    - Preprocessing templates and preparing content for templates.
3. Asynchronously copying all non-content files. The copying process runs in parallel with the preprocessing stages.
4. Generating data and computed values for the build (to populate ready-made templates with content).
5. Building the dependency graph in the following order:
    - Processing templates that do not have dependencies.
    - Processing templates that use tags (an embedded field in content files).
    - Processing templates that use pagination and any other collections.
    - Processing templates that use pagination and the Configuration API to add collections.
    - Processing templates that use pagination and prepare local collection objects (`collection`).
    - Processing templates that use pagination and prepare the global collection object (`collection.all`).
6. Generating collections in the correct order for the dependency graph.
7. Building an additional dependency graph for generating computed data, permalinks, and paths to source content files.
8. Processing templates without generating page markup.
9. Checking for duplicates.
10. Processing templates with final page markup.
11. Applying transformations.

The configuration file is used to set up the folders where different types of entities are stored. In Doka, the folders are used as follows:

- _dist/_: For the built site.
- _src/_: For the source code of the site.
- _src/data/_: For global site data.
- _src/includes/_: For markup of page blocks.
- _src/layouts/_: For the main page markup.

As the main library for processing content files, the `markdown-it` library is used. However, by default, the library does not handle videos as needed in Doka. Therefore, a [custom solution](https://github.com/doka-guide/platform/blob/main/src/markdown-it.js) is used.

Special attention is given to transformations during the Doka site build, which is the post-processing of the generated page markup. The following transformations are used:

1. `answers-link-transform` - adjusts paths to demos and images embedded in the "On Practice" section.
2. `article-code-blocks-transform` - generates markup for code blocks in the text of materials with copy functionality.
3. `article-inline-code-transform` - generates markup for inline code in the text of materials with copy functionality.
4. `callout-transform` - generates markup for callouts.
5. `code-breakify-transform` - adds line breaks in code text.
6. `code-classes-transform` - adds classes to inline code blocks.
7. `color-picker-transform` - adds color squares in code after color mentions.
8. `demo-external-link-transform` - adds links to open demos in a new window.
9. `demo-link-transform` - adjusts paths to demos and images embedded in the "On Practice" section.
10. `details-transform` - wraps content inside `<details>` tags with a `.content` class.
11. `headings-anchor-transform` - generates anchor links for headings.
12. `headings-id-transform` - generates markup to create `id` attributes for headings.
13. `iframe-attr-transform` - adds attributes to `<iframe>` tags if they are missing.
14. `image-place-transform` - places images with captions inside `<figure>` tags.
15. `image-transform` - prepares images in different formats for optimized page loading.
16. `link-transform` - adds classes to links.
17. `table-transform` - wraps tables in containers with scrolling.
18. `toc-transform` - generates a table of contents for the page.

#### Tests

In Doka, we use unit testing with the [Jest](https://jestjs.io) framework. Its configuration can be found in the files [_jest.config.js_](https://github.com/doka-guide/platform/blob/main/jest.config.js) and [_jest.setup.js_](https://github.com/doka-guide/platform/blob/main/jest.setup.js). The tests themselves are located in the subfolders of the scripts folder under the *__tests__/* directory.

#### Launch Scripts

Different modes can be used to work with the platform (described in the [_package.json_](https://github.com/doka-guide/platform/blob/main/package.json) file):

- `debug` - for debugging the platform and content (the site doesn't crash if something goes wrong, but informs about what happened);
- `start` - for running the platform locally with automatic reloading when content or platform files change;
- `build` - for building the website (with code minification and all transformations);
- `preview` - for previewing the build (without code minification and all transformations except `image-transform`);
- `deploy` - for deploying the site on the Doka infrastructure (if access is available);
- `editorconfig` - for checking files against the Editorconfig requirements, according to the [configuration](https://github.com/doka-guide/platform/blob/main/.editorconfig);
- `stylelint` - for checking files against the Stylelint requirements, according to the [configuration](https://github.com/doka-guide/platform/blob/main/.stylelintrc.json);
- `eslint` - for checking files against the ESLint requirements, according to the [configuration](https://github.com/doka-guide/platform/blob/main/.eslintrc.json);
- `lint-check` - runs all linters;
- `test` - runs unit tests;
- `make-links` - for creating symbolic links to content.

#### Templates

Templates in Doka are implemented using [Nunjucks](https://mozilla.github.io/nunjucks/). The _src/layouts_ folder contains a base template _base.njk_ with the main layout of any page on the site. It includes the main `<html>`, `<head>`, and `<body>` tags, microdata, and meta tags using _meta.njk_, and it also includes styles and client-side scripts.

The templates for the site pages are located in the _src/views_ folder:

- _[404.njk](https://github.com/doka-guide/platform/blob/main/src/views/404.njk)_ - the page shown when a requested page doesn't exist;
- _[all.njk](https://github.com/doka-guide/platform/blob/main/src/views/all.njk)_ - the index page for all materials (docs, articles);
- _[article-index.njk](https://github.com/doka-guide/platform/blob/main/src/views/article-index.njk)_ - index pages for materials in each section;
- _[doc.njk](https://github.com/doka-guide/platform/blob/main/src/views/doc.njk)_ - pages with materials;
- _[feed.njk](https://github.com/doka-guide/platform/blob/main/src/views/feed.njk)_ - XML document for organizing an RSS feed;
- _[index.njk](https://github.com/doka-guide/platform/blob/main/src/views/index.njk)_ - the main page of the site;
- _[page.njk](https://github.com/doka-guide/platform/blob/main/src/views/page.njk)_ - pages with text that are not materials;
- _[people.njk](https://github.com/doka-guide/platform/blob/main/src/views/people.njk)_ - page with a list of participants (contributors);
- _[person-json.njk](https://github.com/doka-guide/platform/blob/main/src/views/person-json.njk)_ - information about a project participant in JSON format;
- _[person.njk](https://github.com/doka-guide/platform/blob/main/src/views/person.njk)_ - personal pages of participants;
- _[sc-index.njk](https://github.com/doka-guide/platform/blob/main/src/views/sc-index.njk)_ - cards for social networks (used to generate images for social networks) in HTML format for sections;
- _[sc.njk](https://github.com/doka-guide/platform/blob/main/src/views/sc.njk)_ - cards for social networks (used to generate images for social networks) in HTML format for materials;
- _[search.njk](https://github.com/doka-guide/platform/blob/main/src/views/search.njk)_ - search page;
- _[sitemap.njk](https://github.com/doka-guide/platform/blob/main/src/views/sitemap.njk)_ - XML document with the site map;
- _[specials.njk](https://github.com/doka-guide/platform/blob/main/src/views/specials.njk)_ - pages for special projects;
- _[subscribe.njk](https://github.com/doka-guide/platform/blob/main/src/views/subscribe.njk)_ - page for managing email subscriptions.

The templates for individual page blocks are located in the _src/includes_ folder:

- _[analytics/google.njk](https://github.com/doka-guide/platform/blob/main/src/includes/analytics/google.njk)_ - Google Analytics integration on the site;
- _[analytics/metrika.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/article-image.njk)_ - Yandex.Metrica integration on the site;
- _[blocks/article-image.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/article-image.njk)_ - illustrations for materials;
- ~~_[blocks/aside.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/aside.njk)_ - layout for pages with sidebar navigation (currently not used)~~;
- _[blocks/cookie-notification.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/cookie-notification.njk)_ - popup with information about cookie usage;
- _[blocks/featured-article.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/featured-article.njk)_ - block for displaying a single material as a featured item on the main page of the site;
- _[blocks/footer.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/footer.njk)_ - site footer (theme switcher, secondary menu);
- _[blocks/header.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/header.njk)_ - site header (logo, breadcrumbs, search, main menu);
- _[blocks/linked-article.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/linked-article.njk)_ - buttons for navigating to the previous or next material in a section;
- _[blocks/logo.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/logo.njk)_ - layout for the logo;
- _[blocks/nav-list.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/nav-list.njk)_ - menu with a list of site sections;
- _[blocks/person-avatar.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/person-avatar.njk)_ - participant avatar;
- _[blocks/person-badges.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/person-badges.njk)_ - set of participant badges;
- _[blocks/person.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/person.njk)_ - representation of brief information about a participant on the page with the list of participants;
- _[blocks/search-category.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/search-category.njk)_ - category filter for the search page;
- _[blocks/search-hits.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/search-hits.njk)_ - a single search result with brief information about the material;
- _[blocks/search-tags.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/search-tags.njk)_ - material type filter for the search page;
- _[blocks/search.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/search.njk)_ - search block in the main menu;
- _[blocks/snow-toggle.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/snow-toggle.njk)_ - toggle for snowfall effect (activated during the New Year holidays);
- _[blocks/snow.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/snow.njk)_ - block that implements the snowfall effect (activated during the New Year holidays);
- _[blocks/social-card.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/social-card.njk)_ - card for social networks;
- _[blocks/theme-toggle.njk](https://github.com/doka-guide/platform/blob/main/src/includes/blocks/theme-toggle.njk)_ - theme switcher on the site;
- _[articles-gallery.njk](https://github.com/doka-guide/platform/blob/main/src/includes/articles-gallery.njk)_ - block with all materials on the main page that were selected for featuring (the list of materials is updated weekly);
- _[contributors.njk](https://github.com/doka-guide/platform/blob/main/src/includes/contributors.njk)_ - block for materials that shows all contributors;
- _[feedback-form.njk](https://github.com/doka-guide/platform/blob/main/src/includes/feedback-form.njk)_ - form for submitting feedback for materials;
- _[meta.njk](https://github.com/doka-guide/platform/blob/main/src/includes/meta.njk)_ - includes icons, manifest, fonts, and styles, sets available themes, generates meta tags and microdata for pages;
- _[practices.njk](https://github.com/doka-guide/platform/blob/main/src/includes/practices.njk)_ - block for the "Practical" section in materials;
- _[questions.njk](https://github.com/doka-guide/platform/blob/main/src/includes/questions.njk)_ - block for the "Interview" section in materials;
- _[related-articles-gallery.njk](https://github.com/doka-guide/platform/blob/main/src/includes/related-articles-gallery.njk)_ - block for presenting related docs and articles with the current material;
- _[subscribe-popup.njk](https://github.com/doka-guide/platform/blob/main/src/includes/subscribe-popup.njk)_ - popup for submitting an email to subscribe to the newsletter.

