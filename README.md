# marky-markdown

The thing npm uses to clean up READMEs and other markdown files.

## What it does

- Removes broken and malicious input like script tags and iframes using [sanitize-html](https://www.npmjs.com/package/sanitize-html)
- Turns headings (h1, h2, etc) into deep links
- Rewrites relative GitHub links to their absolute equivalents
- Rewrites relative GitHub images sources to their absolute equivalents
- Rewrites insecure Gravatar URLs to use HTTPS
- Highlights code syntax using pygments.
- Wraps embedded YouTube videos so they can be styled.
- Removes redundant content that closely matches npm package name and description
- Parses `package.description` as markdown
- Applies CSS classes to badge images, so we can do something interesting with them one day.

## Installation

Download node at [nodejs.org](http://nodejs.org) and install it, if you haven't already.

```sh
npm install marky-markdown --save
```

## Programmatic Usage

```js
var marky = require("marky-markdown")

// Here's the API signature
marky(inputString, [optionsObject], nodeStyleCallback)

// Clean up a regular old markdown string
marky("# hello, I'm markdown", function(err, $){
  console.log($.html())
})

// Pass in an npm `package` object to do stuff like
// rewriting relative URLs to their absolute equivalent on github,
// normalizing package metadata with redundant readme content,
// etcs
var package = {
  name: "foo"
  name: "foo is a thing"
  repository: {
    type: "git",
    url: "https://github.com/kung/foo"
  }
}

marky(
  "# hello, I am the foo readme",
  {package: package},
  function(err, $) {
    console.log($.html())
  }
)

// Syntax highlighting is disabled by default.
marky(
  "# I'm a string with github flavored markdown",
  {highlightSyntax: true},
  function(err, $) {
    console.log($.html())
  }
)

// Pass in a `debug` for verbose output
marky(
  "# hello, I'm an evil document",
  {debug: true},
  function(err, $) {
    console.log($.html())
  }
)

```

## Command-line Usage

You can use marky-markdown to parse markdown files in the shell.
The easiest way to do this is to install globally:

```
npm i -g marky-markdown
marky-markdown some.md > some.html
```

## Tests

```sh
npm install
npm test
```
```

> marky-markdown@1.3.0 test /Users/z/n/marky-markdown
> mocha
  before everything
    ✓ removes the pygments cache directory
  marky-markdown
    ✓ is a function
    ✓ accepts a markdown string and returns a cheerio DOM object
    ✓ calls back with an error if first argument is not a string
  markdown processing and syntax highlighting
    ✓ preserves query parameters in URLs when making them into links
    ✓ converts github flavored fencing to code blocks
    ✓ adds js class to javascript blocks
    ✓ adds sh class to shell blocks
    ✓ adds sh class to shell blocks
    ✓ adds hightlight class to all blocks
    ✓ applies inline syntax highlighting classes to javascript
    ✓ applies inline syntax highlighting classes to shell
    ✓ applies inline syntax highlighting classes to coffeesript
  sanitize
    ✓ removes script tags
    ✓ allows img tags
    ✓ allows h1/h2/h3/h4/h5/h6 tags to preserve their dom id
    ✓ removes classnames from elements
    ✓ allows classnames on code tags
    ✓ disallows iframes from sources other than youtube
  badges
    ✓ adds a badge class to img tags containing badge images
    ✓ adds a badge-only class to p tags containing nothing more than a badge
  gravatar
    ✓ replaces insecure gravatar img src URLs with secure HTTPS URLs
    ✓ leaves secure gravatar URLs untouched
    ✓ leaves non-gravtar URLs untouched
  github
    when package repo is on github
      ✓ rewrites relative link hrefs to absolute
      ✓ rewrites slashy relative links hrefs to absolute
      ✓ leaves protocol-relative URLs alone
      ✓ leaves hashy URLs alone
      ✓ replaces relative img URLs with npm CDN URLs
      ✓ replaces slashy relative img URLs with npm CDN URLs
      ✓ leaves protocol relative URLs alone
      ✓ leaves HTTPS URLs alone
    when package repo is NOT on github
      ✓ leaves relative URLs alone
      ✓ leaves slashy relative URLs alone
      ✓ leaves protocol-relative URLs alone
      ✓ leaves hashy URLs alone
      ✓ leaves relative img alone
      ✓ leaves slashy relative img URLs alone
      ✓ leaves protocol relative URLs alone
      ✓ leaves HTTPS URLs alone
  youtube
    ✓ wraps iframes in a div for stylability
    ✓ removes iframe width and height properties
    ✓ preserves src, frameborder, and allowfullscreen properties
  packagize
    name
      ✓ prepends an h1.package-name element into readme with value of package.name
      ✓ adds .package-name-redundant class to first h1 if it's similar to package.name
      ✓ leaves first h1 alone if it differs from package.name
    description
      ✓ prepends package.description in a p.package-description element
      ✓ adds .package-description-redundant class to first h1 if it's similar to package.description
      ✓ leaves first h1 alone if it differs from package.description
      ✓ adds .package-description-redundant class to first p if it's similar to package.description
      ✓ leaves first p alone if it differs from package.description
  fixtures
    ✓ is a key-value object
    ✓ contains stringified markdown files as values
    ✓ includes some real package readmes right from node_modules
  headings
    ✓ injects hashy anchor tags into headings that have DOM ids
    ✓ adds deep-link class to modified headings
    ✓ doesn't inject anchor tags into headings that already contain anchors
  frontmatter
    ✓ rewrites HTML frontmatter as <meta> tags
  cdn
    when serveImagesWithCDN is true
      ✓ replaces relative img URLs with npm CDN URLs
      ✓ replaces slashy relative img URLs with npm CDN URLs
      ✓ leaves protocol relative URLs alone
      ✓ leaves HTTPS URLs alone
    when serveImagesWithCDN is false (default)
      ✓ leaves relative img alone
      ✓ leaves slashy relative img URLs alone
      ✓ leaves protocol relative URLs alone
      ✓ leaves HTTPS URLs alone
  real readmes in the wild
    express
      ✓ successfully parses readme.md
      ✓ adds package name h1
      ✓ identifies and marks redundant package description, even when it is not the the first paragraph
    benchmark
      ✓ successfully parses
      ✓ linkifies headings
    async
      ✓ successfully parses
    johnny-five
      ✓ successfully parses
      ✓ throws out HTML comments
    wzrd
      ✓ successfully parses
      ✓ processes package description as markdown
    memoize
      ✓ successfully parses
    mkhere
      ✓ successfully parses
    cicada
      ✓ successfully parses
    flake
      ✓ successfully parses
    grunt-angular-templates
      ✓ successfully parses
  after everything
    ✓ removes the pygments cache directory
  82 passing (840ms)

```

## Dependencies

- [cheerio](https://github.com/cheeriojs/cheerio): Tiny, fast, and elegant implementation of core jQuery designed specifically for the server
- [github-url-to-object](https://github.com/zeke/github-url-to-object): Extract user, repo, and other interesting properties from GitHub URLs
- [html-frontmatter](https://github.com/zeke/html-frontmatter): Extract key-value metadata from HTML comments
- [js-beautify](https://github.com/beautify-web/js-beautify): jsbeautifier.org for node
- [lodash](https://github.com/lodash/lodash): A utility library delivering consistency, customization, performance, &amp; extras.
- [marked](https://github.com/chjj/marked): A markdown parser built for speed
- [pretty](https://github.com/jonschlinkert/pretty): Some tweaks for beautifying HTML with js-beautify according to my preferences.
- [pygmentize-bundled](https://github.com/rvagg/node-pygmentize-bundled): A simple wrapper around Python&#39;s Pygments code formatter, with Pygments bundled
- [pygmentize-bundled-cached](https://github.com/rvagg/pygmentize-bundled-cached): A caching interface to pygmentize-bundled
- [pygments-tokens](https://github.com/zeke/pygments-tokens): A map of the tokens used by the pygments syntax highlighter
- [sanitize-html](https://github.com/punkave/sanitize-html): Clean up user-submitted HTML, preserving whitelisted elements and whitelisted attributes on a per-element basis
- [similarity](https://github.com/zeke/similarity): How similar are these two strings?

## Dev Dependencies

- [async](https://github.com/caolan/async): Higher-order functions and common patterns for asynchronous code
- [benchmark](https://github.com/bestiejs/benchmark.js): A benchmarking library that works on nearly all JavaScript platforms, supports high-resolution timers, and returns statistically significant results.
- [cicada](https://github.com/substack/cicada): a teeny git-based continuous integration server
- [express](https://github.com/strongloop/express): Fast, unopinionated, minimalist web framework
- [flake](https://github.com/chilts/flake): Generate practically unique (approximately sortable) IDs in a distributed environment.
- [grunt-angular-templates](https://github.com/ericclemmons/grunt-angular-templates): Grunt build task to concatenate &amp; register your AngularJS templates in the $templateCache
- [johnny-five](https://github.com/rwldrn/johnny-five): The JavaScript Hardware Programming Framework. Use with: Arduino (all models), Electric Imp, Beagle Bone, Intel Galileo &amp; Edison, Linino One, Pinoccio, Raspberry Pi, Spark Core, TI Launchpad and more!
- [memoize](https://github.com/stagas/memoize): memoize caches your callbacks given a set of arguments w/ persistence
- [mkhere](https://github.com/sanusart/mkhere): Make files based on template
- [mocha](https://github.com/mochajs/mocha): simple, flexible, fun test framework
- [package-json-to-readme](https://github.com/zeke/package-json-to-readme): Generate a README.md from package.json contents
- [rimraf](https://github.com/isaacs/rimraf): A deep deletion module for node (like `rm -rf`)
- [userhome](https://github.com/shama/userhome): A cross-platform path to the user&#39;s home
- [wzrd](https://github.com/maxogden/wzrd): Super minimal browserify development server. Inspired by [beefy](http://npmjs.org/beefy) but with less magic


## License

ISC

_Generated by [package-json-to-readme](https://github.com/zeke/package-json-to-readme)_
