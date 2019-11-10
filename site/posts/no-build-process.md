---
title: Where is the build process?
date: "2019-10-31"
description: "Supermaya wraps all the build processes into Eleventy itself."
tags:
  - demo-content
  - sample-post
  - blog
---

A lot of modern sites rely on tools like Sass and Babel to transpile CSS and JavaScript. They usually require build tools like Gulp or Webpack to trigger this and most larger sites have additional build steps that do anything from fetching data, to minify code, to optimise images or almost anything else.

When you add static site generators to this, things can get a little weird. Most static site generators have a development server that watches for changes and will rebuild the site as needed. They are often smart and will only rebuild pages that have changed. You can end up with situations where the site generator rebuilds multiple times as each of the build steps is run.

This results in complex build processes where multiple passes are required and sometimes it takes several minutes just to generate a few pages. This isn't fun and trying to get them to work together can be a lot of effort.

Supermaya avoids these pitfalls by wrapping all the build processes into Eleventy itself.

Eleventy has [global data files](https://www.11ty.io/docs/data-global/) that allow asynchronous fetching and resolution of data before each build as well as [transforms](https://www.11ty.io/docs/config/#transforms) that can completely modify the output of generated HTML.

Supermaya makes use of both these features to compile Sass, Webpack JavaScript files, Generate Critical CSS, as well as minify and optimise content.

## CSS

In Sypermaya SCSS files are compiled on-the-fly and added to global data. This means you can write inline CSS directly into templates and partials like this: `<style>{{css["compilation-target"] | safe}}</style>`. Where _"compilation-target"_ is the key added to the list of SCSS files to compile in the data file:

```
site/_data/css.js
```

Each entry in the `targets` array will also be available as a static file will written to the path `/css/[compilation-target].css`.

Source files for `scss` can be found in the directory `site/src/scss`.

## JavaScript

Similar to the CSS, JavaScript in Sypermaya are also compiled on-the-fly and added to global data.

This is done with Babel and Webpack. The Webpack configuration contains a loader for `.js` files that will transpile from ES6 to ES5 meaning you can safely write modern JavaScript. The Webpack configuration can be extended or modified in: `site/utils/compile-webpack.js`.

Files generated by Webpack are added to site data so you can write inline JavaScript in templates and partials like this: `<script>{{css["output-filename"] | safe}}</script>`. The "output-filename" should be the full name of a file generated by Webpack including the extension.

You can add additional entry points by modifying the `targets` array in:

```
site/_data/js.js
```

Each file generated will also be available as a static file at the path: `js/[output-filename]`.

Source files for javascript have been added to the directory `site/src/js`.

## Critical CSS

Sypermaya is capable of generating criticalCSS although, for performance reasons, this is turned off by default. You can turn this feature on by modifying:

```
site/_data/site.js
```