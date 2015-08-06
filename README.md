# grunt-replace [![Build Status](https://secure.travis-ci.org/outaTiME/grunt-replace.png?branch=master)](http://travis-ci.org/outaTiME/grunt-replace)

> Replace text patterns with [applause](https://github.com/outaTiME/applause).



## Install

From NPM:

```shell
npm install grunt-replace --save-dev
```

## Replace Task

Assuming installation via NPM, you can use `grunt-replace` in your gruntfile like this:

```javascript
module.exports = function (grunt) {
  grunt.loadNpmTasks('grunt-replace');
  grunt.initConfig({
    replace: {
      dist: {
        options: {
          patterns: [
            {
              match: 'foo',
              replacement: 'bar'
            }
          ]
        },
        files: [
          {expand: true, flatten: true, src: ['src/index.html'], dest: 'build/'}
        ]
      }
    }
  });
  grunt.registerTask('default', 'replace');
};
```

### Options

function () {
      var name = 'Applause Options';
      return sections[name] || '_(Coming soon)_'; // empty
    }

#### excludeBuiltins
Type: `Boolean`
Default: `false`

If set to `true`, we exclude built-in matching rules.

#### force
Type: `Boolean`
Default: `true`

Force the copy of files even when those files don't have any match found for replacement.

#### noProcess
Type: `String`

This option is an advanced way to control which file contents are processed.

> `processContentExclude` has been renamed to `noProcess` and the option name will be removed in the future.

#### encoding
Type: `String`
Default: `grunt.file.defaultEncoding`

The file encoding to copy files with.

#### mode
Type: `Boolean` or `Number`
Default: `false`

Whether to copy or set the existing file permissions. Set to `true` to copy the existing file permissions. Or set to the mode, i.e.: `0644`, that copied files will be set to.

#### silent
Type: `Boolean`
Default: `false`

If set to `true`, removes the output from stdout.

#### pedantic
Type: `Boolean`
Default: `false`

If set to `true`, the task will fail with a `grunt.fail.warn` when no matches are present.

### Built-in Replacements

Few matching rules are provided by default and can be used anytime (these will be affected by the `options` given):

 *  `__SOURCE_FILE__`:

    Replace match with the source file.

 *  `__SOURCE_PATH__`:

    Replace match with the path of source file.

 *  `__SOURCE_FILENAME__`:

    Replace match with the filename of source file.

 *  `__TARGET_FILE__`:

    Replace match with the target file.

 *  `__TARGET_PATH__`:

    Replace match with the path of target file.

 *  `__TARGET_FILENAME__`:

    Replace match with the filename of target file.

> If you are looking how to use an `built-in` replacements, check out the [How to insert filename in target](#how-to-insert-filename-in-target) usage.

### Usage Examples

#### Basic

File `src/manifest.appcache`:

```
CACHE MANIFEST
# @@timestamp

CACHE:

favicon.ico
index.html

NETWORK:
*
```

Gruntfile, define pattern (for timestamp) and the source files for lookup:

```js
replace: {
  dist: {
    options: {
      patterns: [
        {
          match: 'timestamp',
          replacement: '<%= grunt.template.today() %>'
        }
      ]
    },
    files: [
      {expand: true, flatten: true, src: ['src/manifest.appcache'], dest: 'build/'}
    ]
  }
}
```

#### Multiple matching

File `src/manifest.appcache`:

```
CACHE MANIFEST
# @@timestamp

CACHE:

favicon.ico
index.html

NETWORK:
*
```

File `src/humans.txt`:

```
              __     _
   _    _/__  /./|,//_`
  /_//_// /_|///  //_, outaTiME v.@@version

/* TEAM */
  Web Developer / Graphic Designer: Ariel Oscar Falduto
  Site: http://www.outa.im
  Twitter: @outa7iME
  Contact: afalduto at gmail dot com
  From: Buenos Aires, Argentina

/* SITE */
  Last update: @@timestamp
  Standards: HTML5, CSS3, robotstxt.org, humanstxt.org
  Components: H5BP, Modernizr, jQuery, Twitter Bootstrap, LESS, Jade, Grunt
  Software: Sublime Text 2, Photoshop, LiveReload

```

Gruntfile:

```js
replace: {
  dist: {
    options: {
      patterns: [
        {
          match: 'version',
          replacement: '<%= pkg.version %>'
        },
        {
          match: 'timestamp',
          replacement: '<%= grunt.template.today() %>'
        }
      ]
    },
    files: [
      {expand: true, flatten: true, src: ['src/manifest.appcache', 'src/humans.txt'], dest: 'build/'}
    ]
  }
}
```

#### Cache busting

File `src/assets/index.html`:

```html
<head>
  <link rel="stylesheet" href="/css/style.css?rel=@@timestamp">
  <script src="/js/app.js?rel=@@timestamp"></script>
</head>
```

Gruntfile:

```js
replace: {
  dist: {
    options: {
      patterns: [
        {
          match: 'timestamp',
          replacement: '<%= new Date().getTime() %>'
        }
      ]
    },
    files: [
      {src: ['src/assets/index.html'], dest: 'build/index.html'}
    ]
  }
}
```

#### Include file

File `src/index.html`:

```html
<body>
  @@include
</body>
```

Gruntfile:

```js
replace: {
  dist: {
    options: {
      patterns: [
        {
          match: 'include',
          replacement: '<%= grunt.file.read("includes/content.html") %>'
        }
      ]
    },
    files: [
      {expand: true, flatten: true, src: ['src/index.html'], dest: 'build/'}
    ]
  }
}
```

#### Regular expression

File `src/username.txt`:

```
John Smith
```

Gruntfile:

```js
replace: {
  dist: {
    options: {
      patterns: [
        {
          match: /(\w+)\s(\w+)/,
          replacement: '$2, $1' // replaces "John Smith" to "Smith, John"
        }
      ]
    },
    files: [
      {expand: true, flatten: true, src: ['src/username.txt'], dest: 'build/'}
    ]
  }
}
```

#### Lookup for `foo` instead of `@@foo`

Gruntfile:

```js
// option 1 (explicitly using an regexp)
replace: {
  dist: {
    options: {
      patterns: [
        {
          match: /foo/g,
          replacement: 'bar'
        }
      ]
    },
    files: [
      {expand: true, flatten: true, src: ['src/foo.txt'], dest: 'build/'}
    ]
  }
}

// option 2 (easy way)
replace: {
  dist: {
    options: {
      patterns: [
        {
          match: 'foo',
          replacement: 'bar'
        }
      ],
      usePrefix: false
    },
    files: [
      {expand: true, flatten: true, src: ['src/foo.txt'], dest: 'build/'}
    ]
  }
}

// option 3 (old way)
replace: {
  dist: {
    options: {
      patterns: [
        {
          match: 'foo',
          replacement: 'bar'
        }
      ],
      prefix: '' // remove prefix
    },
    files: [
      {expand: true, flatten: true, src: ['src/foo.txt'], dest: 'build/'}
    ]
  }
}
```

#### How to insert filename in target

File `src/app.js`:

```js
// filename: @@__SOURCE_FILENAME__

var App = App || (function () {

  return {

    // app contents

  };

}());

window.App = App;
```

Gruntfile:

```js
replace: {
  dist: {
    options: {
      // pass, we use built-in replacements
    },
    files: [
      {expand: true, flatten: true, src: ['src/**/*.js'], dest: 'build/'}
    ]
  }
}
```

## Release History

 * 2015-08-06   v0.9.3   New pedantic option (thanks [@donkeybanana](https://github.com/donkeybanana)). Fix issue with special characters attributes ($$, $&, $`, $', $n or $nn) on JSON, YAML and CSON.
 * 2015-05-07   v0.9.2   Fix regression issue with empty string in replacement.
 * 2015-05-01   v0.9.1   Better output.
 * 2015-05-01   v0.9.0   Output available via --verbose flag. The mode option now also applies to directories. Fix path issue on Windows. Display warning message when no matches and overall of replacements. Update to [applause](https://github.com/outaTiME/applause) v0.4.0.
 * 2014-10-10   v0.8.0   Escape regexp when matching type is `String`.
 * 2014-08-26   v0.7.9   Fixes backwards incompatible changes introduced in NPM.
 * 2014-06-10   v0.7.8   Remove node v.8.0 support and third party dependencies updated. Force flag now are true by default.
 * 2014-04-20   v0.7.7   JSON / YAML / CSON as function supported. Readme updated (thanks [@milanlandaverde](https://github.com/milanlandaverde)).
 * 2014-03-23   v0.7.6   Readme updated.
 * 2014-03-22   v0.7.5   Modular core renamed to [applause](https://github.com/outaTiME/applause). Performance improvements. Expression flag removed. New pattern matching for CSON object. More test cases, readme updated and code cleanup.
 * 2014-03-21   v0.7.4   Test cases in Mocha, readme updated and code cleanup.
 * 2014-03-17   v0.7.3   Update script files for readme file generation.
 * 2014-03-12   v0.7.2   Typo error, replace task name again.
 * 2014-03-11   v0.7.1   Task name update.
 * 2014-03-11   v0.7.0   New [pattern-replace](https://github.com/outaTiME/pattern-replace) modular core for replacements.
 * 2014-02-13   v0.6.2   Attach process data for function replacements (source / target). Add delimiter option for object as replacement. Dependencies updated.
 * 2014-02-06   v0.6.1   Rename excludePrefix to preservePrefix (more readable) and adds usePrefix flag. Support the noProcess option like [grunt-contrib-copy](https://github.com/gruntjs/grunt-contrib-copy).
 * 2014-02-05   v0.6.0   Object replacement allowed. New excludePrefix flag (thanks [@shinnn](https://github.com/shinnn)). Encoding / Mode options added.
 * 2013-09-18   v0.5.1   New pattern matching for JSON object.
 * 2013-09-17   v0.5.0   Regular expression matching now supported and notation has been updated but is backward compatible.
 * 2013-05-03   v0.4.4   Fix escape $ before performing regexp replace (thanks [@warpech](https://github.com/warpech)).
 * 2013-04-14   v0.4.3   Detect path destinations correctly on Windows.
 * 2013-04-02   v0.4.2   Add peerDependencies and update description.
 * 2013-04-02   v0.4.1   Add trace when force flag.
 * 2013-02-28   v0.4.0   First official release for Grunt 0.4.0.
 * 2012-11-20   v0.3.2   New examples added.
 * 2012-09-25   v0.3.1   Rename grunt-contrib-lib dep to grunt-lib-contrib, add force flag.
 * 2012-09-25   v0.3.0   General cleanup and consolidation. Global options depreciated.

---

Task submitted by [Ariel Falduto](http://outa.im/)
