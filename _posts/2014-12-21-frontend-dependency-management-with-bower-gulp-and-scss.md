---
layout: post
title: Frontend dependency management with Bower, Gulp and Scss
subtitle: The dependency management for front end is like a headache for the first time. I thought someone can save some hair from their head
categories: []
tags: [bower, gulp, scss, bootstrap, font-awesome]
published: True
author: Kamal Lamichhane

---

##Setting Environment for Bower, Gulp and Scss

The dependency management for front end is like a headache for the first time. I thought someone can save some hair from their head

###Install Node and npm
You can install Node and npm from http://nodejs.org

###Install Bower
Bower is a command line utility. Install it with npm.

```
$ npm install -g bower
```

Bower requires Node and npm and Git.

###Setup bower
The first step is to setup Bower. Create a new local directory and init bower following the instructions.

```
$ mkdir your_project

$ cd your_project/

$ bower init
```

After that finishes install our required bower packages:

```
$ bower install bootstrap-sass-official --save  
bower install fontawesome --save
```

By default this will put them in the bower_components directory which can be changed if you prefer. I will leave it as default.

###Install Gulp and its dependency

Now we need to setup our gulp dependencies, which pull from NPM. Create a new package.json file and just add an empty object, {} and save it.

Head back to the terminal and install our NPM dependencies:

```
$ npm install gulp gulp-ruby-sass gulp-notify gulp-autoprefixer gulp-bower --save-dev
```

This will install all the needed dependencies in a node_modules folder and also automatically update our package.json file with these dependencies.

###setting gulpfile.js

Finally we need to setup the gulpfile.js. Create this file and we’ll step through all the settings.

If you’ve looked at gulp files before then you might know they all start by including the dependencies by using the node require function. Here is the start:

```javascript
var gulp = require('gulp'),     
    sass = require('gulp-ruby-sass') 
    autoprefix = require('gulp-autoprefixer') 
    notify = require("gulp-notify") 
    bower = require('gulp-bower');
```

These are everything we installed in the NPM step above and at this point just included and not actually doing anything.

Next up I create a config object to hold various settings:

```javascript
var config = {
     sassPath: './resources/sass',
     bowerDir: './bower_components' 
}

var paths = {
    scss: [config.sassPath + '/**.scss', config.bowerDir + '/fontawesome/scss/font-awesome.scss'],
    font: config.bowerDir + '/fontawesome/fonts/**.*',
    js: [config.bowerDir + '/jquery/dist/**.*', config.bowerDir + '/bootstrap-sass-official/assets/javascripts/bootstrap.js']
}
```

###Gulp tasks

####Bower:
```javascript

gulp.task('bower', function() { 
    return bower()
         .pipe(gulp.dest(config.bowerDir)) 
});
```

####Font awesome icons:
This task generated fontawesome/icons to public/fonts

```javascript

gulp.task('icons', function() { 
    return gulp.src(paths.font) 
        .pipe(gulp.dest('./public/fonts')); 
});
```

####Sass on /resources folder and font-awesome
This task publishes scss files to public/css on css format 

```javascript

gulp.task('css', function() { 
    return gulp.src(paths.scss)
         .pipe(sass({style: 'compressed'}) 
            .on("error", notify.onError(function (error) {
                 return "Error: " + error.message;
             }))) 
        .pipe(autoprefix('last 2 version'))
         .pipe(gulp.dest('./public/css')); 
});
```

####javascripts
This task publishes bootstrap.js and jquery.js on public/js folder

```javascript

gulp.task('js', function() { 
    return gulp.src(paths.js)
        .pipe(gulp.dest('./public/js')); 
});
```

###Watch and Default Task

```javascript
gulp.task('watch', function() {
     gulp.watch(paths.scss, ['css']); 
});

  gulp.task('default', ['bower', 'icons', 'css', 'js']);
```

The first is the watch task which just listens for changes in the path and then runs the “css” task. Finally we have a default task which when called runs bower, icons, and the css task. This is useful so contributors can pull down your code, cd into it, run npm install, and finally gulp. Everything should be easily shared and used.

####Final gulp file looks like this

```javascript
var gulp = require('gulp'),     
    sass = require('gulp-ruby-sass') 
    autoprefix = require('gulp-autoprefixer') 
    notify = require("gulp-notify") 
    bower = require('gulp-bower');

var config = {
     sassPath: './resources/sass',
     bowerDir: './bower_components' 
}

var paths = {
    scss: [config.sassPath + '/**.scss', config.bowerDir + '/fontawesome/scss/font-awesome.scss'],
    font: config.bowerDir + '/fontawesome/fonts/**.*',
    js: [config.bowerDir + '/jquery/dist/**.*', config.bowerDir + '/bootstrap-sass-official/assets/javascripts/bootstrap.js']
}
gulp.task('bower', function() { 
    return bower()
         .pipe(gulp.dest(config.bowerDir)) 
});

gulp.task('icons', function() { 
    return gulp.src(paths.font) 
        .pipe(gulp.dest('./public/fonts')); 
});

gulp.task('css', function() { 
    return gulp.src(paths.scss)
         .pipe(sass({
             style: 'compressed'
         }) 
            .on("error", notify.onError(function (error) {
                 return "Error: " + error.message;
             }))) 
        .pipe(autoprefix('last 2 version'))
         .pipe(gulp.dest('./public/css')); 
});

gulp.task('js', function() { 
    return gulp.src(paths.js)
        .pipe(gulp.dest('./public/js')); 
});

// Rerun the task when a file changes
 gulp.task('watch', function() {
     gulp.watch(paths.scss, ['css']); 
});

  gulp.task('default', ['bower', 'icons', 'css', 'js']);
```

The final demo is on github. You can get the files from [here][github-demolink]

You can visit documentation section of each tools I have used from their official sites:
- [Bower][bowersite-link]
- [Gulp][gulpsite-link]
- [Scss][scsssite-link]

[bowersite-link]: http://bower.io/
[gulpsite-link]: http://www.gulpjs.com/
[scsssite-link]: http://sasscss.org/
[github-demolink]: https://github.com/lckamal/bower-gulp-scss 