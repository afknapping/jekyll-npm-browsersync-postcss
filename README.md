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



==========

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