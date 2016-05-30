---
layout: post
title:  "What is gulp.js and why use it?"
date:   April 10, 2015
permalink: /what-is-gulp-js-and-why-use-it/
tags:
  - Build Automation
  - Gulp.js
categories:
  - Tools
comments: true
header: what-is-gulpjs.jpg
---

There's no point in investing your time into learning a new tool if you don't even know what problem it solves. Gulp solves the problem of **repetition**. Many of the tasks that web developers find themselves doing over and over on a daily basis can be simplified by becoming automated. Automating repetitive tasks = more time to do non repetitive tasks = more productivity.

Gulp is a javascript task runner that lets you automate tasks such as...

- Bundling and minifying libraries and stylesheets.
- Refreshing your browser when you save a file.
- Quickly running unit tests
- Running code analysis
- Less/Sass to CSS compilation
- Copying modified files to an output directory

#### The gulp workflow

Below is a common workflow for performing certain build operations.

- We start by defining a task that we would like to accomplish.
- Within that task, a desired set of files are loaded into the gulp stream to be processed.
(Optional) Once files are in the stream, one or more modifications can be made to the files. Because the streams are processed in memory, no file - system writes to temporary directories between modifications are required.
- Send the new (possibly modified) files to a specified destination

So first, the original files go in, we optionally process modifications to the input files, then we copy the result of our stream to a destination directory.

#### The simple gulp API

Using gulp is super simple because you don't have to figure out how a complex API works in order to be productive with it. There are only 4 api's in gulp!

| API | Purpose |
|--------|--------|
| gulp.task | Define a task |
| gulp.src | Read files in |
| gulp.dest | Write files out |
| gulp.watch | Watch files for changes |

#### Installing gulp via npm

The npm package manager comes installed with Node.js. While node.js isn't a requirement to use gulp, it does make demonstrating it a lot easier. I will be installing gulp from npm locally into my project. Make sure that you're in your project's root folder before running the command, otherwise your node modules will be downloaded into the wrong folder.

```bash
cd myproject
npm install --save-dev gulp
```

This will install the gulp node module locally to the project (as opposed to globally). The `--save-dev` argument lets npm know to update it's package.json file with a new devDependencies record. devDependencies will need to be resolved at development time, where as dependencies will need to be resolved at run time. Because gulp is a tool to aid us in development, it needs to be resolved at development time.

#### Creating a gulpfile

A gulpfile is a file that will act as a manifest to define our tasks. Tasks that we want to execute will be found within this file. Whenever we run the command `gulp hello-world` from the command line, we are telling gulp that we want to run the hello-world task within gulpfile.js.

After creating gulpfile.js within the root of your project, add a basic tasks.

```javascript
var gulp = require('gulp');

gulp.task('hello-world', function(){
    console.log('hello world');
});
```

`require` is a function implemented by node (which is an implementation of the [CommonJS spec](http://wiki.commonjs.org/wiki/Modules/1.1.1){:target="_blank"}) that will add references to node modules that we have installed. Once we make a reference to the gulp module, we can use it to create a task. Here, our task simply writes to the console window, but you could have it do any number of automated tasks.

#### A practical example

A task to analyze all of your JavaScript files inside of a set of directories with JSHint and JSCS may look something like this. This example assumes that you have the module **gulp-load-plugins** installed. This plugin will essentially wrap all of your gulp plugins (prefixed with gulp-) into one plugin so that you don't have to have a require statement for every individual gulp module. You would also need to run `npm install` for each of the packages that you wanted to utilizes. This task would be accessible through the use of `gulp analyze` from the command line (while in your applications root directory where your gulpfile resides.)

```javascript
gulp.task('analyze', function() {
    log('Analyzing code with JSHint and JSCS.');
    return gulp
        .src('./src/scripts') // glob pattern for all of my JS

        // utilizes gulp-if and gulp-print to check for cmd args
        .pipe(plugins.if(args.verbose, plugins.print()))

        // utilizes gulp-jscs to inspect JS
        .pipe(plugins.jscs())

        // utilizes gulp-jshint to inspect JS
        .pipe(plugins.jshint())

        // utilizes jshint-stylish to show the results
        .pipe(plugins.jshint.reporter('jshint-stylish'));
});

function log(msg) {
   // utilizes gulp-util's log function
    plugins.util.log(plugins.util.colors.blue(msg));
}
```

Similarly, you may create a gulp task (or set of gulp tasks) for cleaning directories and compiling less to CSS. Such a set of tasks may look like the following. This set of tasks also has it's own set of dependencies, which would require using `npm install` to get installed locally.

```javascript
function clean(path, done) {
    log('Cleaning: ' + plugins.util.colors.blue(path));
    // utilizes del node module
    del(path, cb);
}

gulp.task('clean-styles', function(cb) {
    var files = './styles/*.css'; // glob pattern to any existing css
    clean(files, cb);

    // cb is called because clean uses del,
    // which does not use gulp streams.
});

// 2nd param signifies task dependencies
// in this case, clean-styles will run first.
gulp.task('styles', ['clean-styles'], function() {
    log('Compiling Less --> CSS');
    return gulp
        .src('./src/styles/*.less')
        .pipe(plugins.less()) // utilizes gulp-less to compile less -> css

        // utilizes gulp-autoprefixer to add
        // browser specific css prefixes
        .pipe(plugins.autoprefixer())

        // copies the generated css to a destination folder.
        .pipe(gulp.dest('./styles/*.css'));
});
```

Hopefully it's obvious why taking the time to create gulp tasks for automating repetitive development tasks can increase productivity. Gulp has over 1470 (at the time of this article) (**Edit on 5/29/16:** *Now 2429*) plugins already created that you can take advantage of on the [GulpJS Plugins](http://gulpjs.com/plugins/){:target="_blank"} page. In most cases, if you need to do something common, the plugin will already exist. If not, taking the time to do some research now may save from wasting time on repetitive tasks in the future.
