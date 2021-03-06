[![npm](http://img.shields.io/npm/v/grunt-process.svg?style=flat-square)](https://www.npmjs.com/package/grunt-process)
[![npm](http://img.shields.io/npm/l/grunt-process.svg?style=flat-square)](http://opensource.org/licenses/MIT)
[![Dependency Status](https://david-dm.org/aliaksandr-pasynkau/grunt-process.svg?style=flat-square)](https://david-dm.org/aliaksandr-pasynkau/grunt-process)
[![devDependency Status](https://david-dm.org/aliaksandr-pasynkau/grunt-process/dev-status.svg?style=flat-square)](https://david-dm.org/aliaksandr-pasynkau/grunt-process#info=devDependencies)
[![Build Status](https://travis-ci.org/aliaksandr-pasynkau/grunt-process.svg?branch=master&style=flat-square)](https://travis-ci.org/aliaksandr-pasynkau/grunt-process)
[![Coverage Status](https://img.shields.io/coveralls/aliaksandr-pasynkau/grunt-process.svg?style=flat-square)](https://coveralls.io/r/aliaksandr-pasynkau/grunt-process?branch=master)
[![Code Climate](https://codeclimate.com/github/aliaksandr-pasynkau/grunt-process/badges/gpa.svg)](https://codeclimate.com/github/aliaksandr-pasynkau/grunt-process)
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/aliaksandr-pasynkau/grunt-process?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

# grunt-process

Gruntplugin for processing (add, replace, split) any file

## Getting Started
This plugin requires Grunt `~0.4.x`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-process --save
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-process');
```

## The "process" task

### Overview
In your project's Gruntfile, add a section named `process` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
  process: {
    options: {
      // common options go here.
    },
    your_target: {
      options: {
        // Target-specific options go here.
      },
      files: [
        // Target-specific file lists and/or options go here.
      ]
    },
  },
});
```

### Options

#### options.read
Type: `Function`

method for open file and convert to string or any format what you need
```js
grunt.initConfig({
    customRead: {
        options: {
            read: function (src, dest, fileObject) {
                return grunt.file.readJSON(src); // default function for read

                // if want use async read function:
                // var done = this.async();
                // process.nextTick(function () {
                //     var content = grunt.file.readJSON(src, readOptions);
                //     done(null, content);
                // });
            }
        },
        files: [{
            src: 'some/path/to.file',
           dest: 'distr/path/to.file'
        }]
    }
});
```

#### options.process
Type: `Function`

method for process file and convert to any format what you need (for save)
```js
grunt.initConfig({
    customProcess: {
        options: {
            read: function (src, dest, content, fileObject) {
                return content;  // this is default function for process

                // if want use async process function:
                // var done = this.async();
                // process.nextTick(function () {
                //     content = content.replace(/[1-9]/g, ''); //remove all numbers for example
                //     done(null, content);
                // });
            }
        },
        files: [{
            src: 'some/path/to.file',
           dest: 'distr/path/to.file'
        }]
    }
});
```

#### options.save
Type: `Function`

method for save file(s). must return object (key - is file path; value - content)
```js
grunt.initConfig({
    customSave: {
        options: {
            save: function (src, dest, content, fileObject) {
                var fileObject = {};
                fileObject[dest] = content;
                return fileObject;

                // if want use async save function:
                // var done = this.async();
                // process.nextTick(function () {
                //     var obj = {};
                //     obj[dest + '/1.txt'] = '1';
                //     obj[dest + '/2.txt'] = '2';
                //     obj[dest + '/3.txt'] = '3';
                //     done(null, obj);
                // });
            }
        },
        files: [{
            src: 'some/path/to.file',
           dest: 'distr/path/to.file'
        }]
    }
});
```

### Usage
```js
grunt.initConfig({
    compressAndSplitJsonByKey: {
        options: {
            // read file and convert to JSON
            read: function (src, dest, fileObject) {
                return grunt.file.readJSON(src);
            },

            // add someKey to content object
            process: function (src, dest, content, fileObject) {
                content.someKey = 123;

                return content;
            },

            // split json by object key
            save: function (src, dest, content, fileObject) {
              var files = {};

              _.each(content, function (v, k) {
                var file = fileObject.orig.dest + '/' + k + '.json';
                var content = JSON.stringify(v);
                files[file] = content;
              });

              return files;
            }
        },
        files: [
          {
            expand: true,
            cwd: 'src/dir',
            dest: 'dest/dir',
            src: ['**/*.json']
          }
        ]
    },
});
```

more exampels in Gruntfile.js


### Extend this task

```js
var gruntProcess = require('grunt-process/lib');

gruntProcess(grunt, filesArray, options, done);
```

example
```js
'use strict';

var gruntProcess = require('grunt-process/lib');

module.exports = function (grunt) {
	grunt.registerMultiTask('my-json-processTask', function () {
	    var options = this.options({});

		task(grunt, this.files, {
		    read: function (src, dest, fileObject) {
		        return grunt.file.readJSON(src, options.readOptions);
		    },

		}, this.async());
	});
};
```
