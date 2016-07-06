# Jekyll via npm scripts, using PostCSS, served via Browsersync

## Why

tba


## Overview

- use terminal apis of jekyll and postcss
- do not use built in watchers but nodemon
- serve with browsersync

## commanding with npm

- npm init -f 31ceaae
- npm start -> jekyll serve 9e3d1a0
    - serve has no autoreload on markup or css changes
    - does not open :(
        - jekyll serve & sleep 2 && open http://127.0.0.1:4000/jekyll-npm-browsersync-postcss/ :/
    - browsersync will solve this
- get a grip on baseurl: jekyll serve --baseurl '' e5690e2

## serve with browsersync

- npm install browser-sync --save
- [Browsersync: Get started in 5 minutes](https://www.browsersync.io/#install)
- browser-sync start --server --files "css/*.css" --> browser-sync start --server --files "_site" 
- ./node_modules/.bin/browser-sync start --server "_site/" --files "_site"
    - url is wrong :/
- jekyll serve also did watch and triggered compile on file changes
- add `node_modules/` to `.gitignore`

e93f473

## watch and compile

- compile without watching
- script:
    "jekyll_compile": "jekyll build",
    "jekyll_compile_dev": "jekyll build --config _config.yml,_config_dev.yml",

- [nodemon](http://nodemon.io/) to watch source files
  - npm install nodemon --save
- [By default nodemon monitors the current working directory.](https://github.com/remy/nodemon#monitoring-multiple-directories)
- [By default, nodemon looks for files with the .js, .coffee, .litcoffee, and .json extensions.](https://github.com/remy/nodemon#specifying-extension-watch-list)
    - specify extensions `./node_modules/.bin/nodemon --ext html,md,yml`
- [nodemon can also be used to execute and monitor other programs.](https://github.com/remy/nodemon#running-non-node-scripts)
    - ./node_modules/.bin/nodemon --ext html,md,yml --exec 'npm run jekyll_compile_dev'
    - endless loop :/
- [ignore _site, so compiled files do not trigger re-run. only trigger compile of source files](https://github.com/remy/nodemon#ignoring-files) 
    - ./node_modules/.bin/nodemon --ignore _site/ --ext html,md,yml --exec 'npm run jekyll_compile_dev'
- exclude:
    - node_modules
    - package.json
    - README.md


## watch, compile, serve

    "jekyll_watch_compile_dev": "./node_modules/.bin/nodemon --ignore _site/ --ext html,md,yml --exec 'npm run jekyll_compile_dev'",
    "browsersync_serve": "./node_modules/.bin/browser-sync start --server '_site' --files '_site'",
    "start": "npm run jekyll_watch_compile_dev & browsersync_serve"

## cleanup

- configs: jekyll, browsersync. postcss will also have config.\

- why use rc files? [Configuration file - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Configuration_file) cmd-f "runcom"

- [Browsersync Command Line Usage](https://www.browsersync.io/docs/command-line): 
     browser-sync init                Create a configuration file

- clean up existing stuff first (jekyll), then add browsersync
- _config
    - .jekyllrc.yml
    - .jekyll_devrc.yml
    - .browsersyncrc.?
    - .postcssrc.?

- ./node_modules/.bin/browser-sync init

```
[BS] Config file created bs-config.js
[BS] To use it, in the same directory run: browser-sync start --config bs-config.js
```

- mv bs-config.js _config/.browsersyncrc.js
- browser-sync start --config _config/.browsersyncrc.js
- "browsersync_serve": "./node_modules/.bin/browser-sync start --server '_site' --files '_site'",
- "browsersync_serve": "./node_modules/.bin/browser-sync start --config _config/.browsersyncrc.js",

- [Browsersync options: server](https://www.browsersync.io/docs/options/#option-server)
    - // Serve files from the app directory
      server: "app"


## postcss

- npm compile task

  "postcss_compile": "postcss input output"

- [PostCSS - a tool for transforming CSS with JavaScript](http://postcss.org/)
    - postcss
    - [postcss/postcss-cli: CLI for postcss](https://github.com/postcss/postcss-cli)
    - sass-replace: [precss](https://github.com/jonathantneal/precss), postcss-scss
    - autoprefixer

    npm install postcss postcss-cli postcss-scss autoprefixer precss --save

- $ ./node_modules/.bin/postcss --config _config/.postcssrc.json


- comments break, mixins break, interpolated vars break
- do some reading

- postcss postcss-cli autoprefixer  

## [From Sass to PostCSS](https://pawelgrzybek.com/from-sass-to-postcss/)

- postcss-import postcss-simple-vars postcss-nested postcss-sassy-mixins
- postcss-mixins differs in syntax :/ so use postcss-sassy-mixins, via [PostCSS.parts | A searchable catalog of PostCSS plugins](http://postcss.parts/tag/sass)



npm install postcss postcss-cli autoprefixer postcss-import postcss-simple-vars postcss-nested postcss-sassy-mixins --save


- basically works
    - // comments produce errors
    - fix imports: full filenames, don't use sass import dir
    - interpolated variables have differing syntax :/
        - [postcss/postcss-simple-vars: PostCSS plugin for Sass-like variables](https://github.com/postcss/postcss-simple-vars#interpolation)
          There is special syntax if you want to use variable inside CSS words:

            $prefix: my-company-widget

            $prefix { }
            $(prefix)_button { }

    - font declaration
    - darken()
    - refactor calculation to vanilla css calc()


## conf for github build

ln -s _config/.jekyllrc.yml _config.yml

## automate

- css is not build on gh-pages, so needs to be in ./css and checked in so it gets copied on jekyll build
- after postcss, css/ needs to be copied into _site/

    "postcss_compile": "./node_modules/.bin/postcss --config _config/.postcssrc.json",
    "postcss_copy": "cp -r css/* _site/css/",
    "postcss_watch_compile_copy": "./node_modules/.bin/nodemon --ignore _site/ --ignore node_modules/ --ext scss --exec 'npm run postcss_compile && npm run postcss_copy'",

    "start": "npm run postcss_compile && npm run jekyll_compile_dev && npm run jekyll_watch_compile_dev & postcss_watch_compile_copy & npm run browsersync_serve"




========================================
DEPRECATED


./node_modules/.bin/browser-sync start --server "_site/" --proxy '/jekyll-npm-browsersync-postcss/'


./node_modules/.bin/browser-sync start --proxy 'http://127.0.0.1:4000/jekyll-npm-browsersync-postcss/' --serveStatic '_site'

./node_modules/.bin/browser-sync start --proxy 'http://127.0.0.1:4000/jekyll-npm-browsersync-postcss/' --serveStatic '_site' --files '_site'



browser-sync start --proxy 'mylocal.dev' --serveStatic 'public'


- [A better Jekyll workflow with Gulp](http://theblog.unpixel.fr/2015-11-15-better-jekyll-workflow-with-gulp/)
- 




[As the Browsersync CLI docs tell us](https://www.browsersync.io/docs/command-line) we can init a configuration file by doing `./node_modules/.bin/browser-sync init`. (We need to prepend the local path because we didn't install browsersync globally, but only locally.)

That command should return something like
```
[BS] Config file created bs-config.js
[BS] To use it, in the same directory run: browser-sync start --config bs-config.js
```

Again we have to prepend the command with the local path though: `./node_modules/.bin/browser-sync start --config bs-config.js`


$ browser-sync start --config 'conf/browser-sync.js'



module.exports = {
    server: {
        baseDir: '_site'
    }

};



module.exports = {
    debugInfo: true,
    files: [
        '_site/*.css',
        '_site/**/*.html'
    ],
    ghostMode: {
        forms: true,
        links: true,
        scroll: true
    },
    server: {
        baseDir: '_site'
    }
};