# api documentation for  [google-closure-compiler-js (v20170218.0.0)](https://developers.google.com/closure/compiler/)  [![npm package](https://img.shields.io/npm/v/npmdoc-google-closure-compiler-js.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-google-closure-compiler-js) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-google-closure-compiler-js.svg)](https://travis-ci.org/npmdoc/node-npmdoc-google-closure-compiler-js)
#### Check, compile, transpile, optimize and compress JavaScript with Closure Compiler in JS

[![NPM](https://nodei.co/npm/google-closure-compiler-js.png?downloads=true)](https://www.npmjs.com/package/google-closure-compiler-js)

[![apidoc](https://npmdoc.github.io/node-npmdoc-google-closure-compiler-js/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-google-closure-compiler-js_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-google-closure-compiler-js/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-google-closure-compiler-js/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-google-closure-compiler-js/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "The Closure Compiler Authors"
    },
    "bin": {
        "google-closure-compiler-js": "cmd.js"
    },
    "bugs": {
        "url": "https://github.com/google/closure-compiler/issues"
    },
    "dependencies": {
        "minimist": "^1.2.0",
        "vinyl": "^2.0.1",
        "webpack-core": "^0.6.8"
    },
    "description": "Check, compile, transpile, optimize and compress JavaScript with Closure Compiler in JS",
    "devDependencies": {
        "ncp": "^2.0.0"
    },
    "directories": {},
    "dist": {
        "shasum": "31637019c15fb14e3d491ec663ee404a1d33da14",
        "tarball": "https://registry.npmjs.org/google-closure-compiler-js/-/google-closure-compiler-js-20170218.0.0.tgz"
    },
    "gitHead": "dffdf9a0fee16cb484e5db750a54be98594a4b02",
    "homepage": "https://developers.google.com/closure/compiler/",
    "keywords": [
        "javascript",
        "compiler",
        "optimizer",
        "minifier",
        "closure"
    ],
    "license": "Apache-2.0",
    "main": "index.js",
    "maintainers": [
        {
            "name": "blickly",
            "email": "blickly+npm@google.com"
        },
        {
            "name": "brad4d",
            "email": "bradfordcsmith@google.com"
        },
        {
            "name": "dimvar",
            "email": "dimvar@google.com"
        },
        {
            "name": "dominator008",
            "email": "zhoumotongxue008@gmail.com"
        },
        {
            "name": "joeltine",
            "email": "jmarti221@gmail.com"
        },
        {
            "name": "matrixfrog",
            "email": "tbreisacher@google.com"
        },
        {
            "name": "samthor",
            "email": "sam.thorogood@gmail.com"
        }
    ],
    "name": "google-closure-compiler-js",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/google/closure-compiler-js.git"
    },
    "scripts": {},
    "version": "20170218.0.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module google-closure-compiler-js](#apidoc.module.google-closure-compiler-js)
1.  [function <span class="apidocSignatureSpan">google-closure-compiler-js.</span>compile (flags)](#apidoc.element.google-closure-compiler-js.compile)
1.  [function <span class="apidocSignatureSpan">google-closure-compiler-js.</span>gulp ()](#apidoc.element.google-closure-compiler-js.gulp)
1.  [function <span class="apidocSignatureSpan">google-closure-compiler-js.</span>logger (options, output, logger)](#apidoc.element.google-closure-compiler-js.logger)
1.  [function <span class="apidocSignatureSpan">google-closure-compiler-js.</span>webpack (args)](#apidoc.element.google-closure-compiler-js.webpack)



# <a name="apidoc.module.google-closure-compiler-js"></a>[module google-closure-compiler-js](#apidoc.module.google-closure-compiler-js)

#### <a name="apidoc.element.google-closure-compiler-js.compile"></a>[function <span class="apidocSignatureSpan">google-closure-compiler-js.</span>compile (flags)](#apidoc.element.google-closure-compiler-js.compile)
- description and source-code
```javascript
compile = function (flags) {
  const clone = {};
  for (const k in flags) {
    clone[k] = flags[k];
  }
  const out = jscomp(clone);

  // hide weird GWT internals
  out.warnings = Array.prototype.slice.call(out.warnings);
  out.errors = Array.prototype.slice.call(out.errors);

  return out;
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.google-closure-compiler-js.gulp"></a>[function <span class="apidocSignatureSpan">google-closure-compiler-js.</span>gulp ()](#apidoc.element.google-closure-compiler-js.gulp)
- description and source-code
```javascript
gulp = function () {
  const events = require('events');
  const path = require('path');

  const compile = require('../compile');
  const logger = require('../logger');
  const File = require('vinyl');
  const Transform = require('stream').Transform;

  class CompilationStream extends Transform {
    constructor(compilationOptions, pluginOptions) {
      super({objectMode: true});

      this.compilationOptions_ = compilationOptions;
      this.pluginName_ = pluginOptions.pluginName || PLUGIN_NAME;
      this.logger_ = pluginOptions.logger || (message => console.info(message));

      this.fileList_ = [];
    }

    _transform(file, enc, cb) {
      if (file.isNull()) {
        // Ignore empty files.
      } else if (file.isStream()) {
        this.emit('error', new PluginError(this.pluginName_, 'Streaming not supported'));
      } else {
        this.fileList_.push(file);
      }
      cb();
    }

    _flush(cb) {
      const options = {};
      for (const k in this.compilationOptions_) {
        options[cleanupOptionKey(k)] = this.compilationOptions_[k];
      }
      options.jsCode = (options.jsCode || []).concat(this.fileList_.map(file => {
        return {
          // TODO(samthor): It's not clear we always want to have modules rooted 'here'
          path: path.relative(process.cwd(), file.path),
          src: file.contents.toString(),
          sourceMap: file.sourceMap ? JSON.stringify(file.sourceMap) : undefined,
        };
      }));

      const outputFile = options.jsOutputFile;
      delete options.jsOutputFile;

      const output = compile(options);
      if (logger(options, output, this.logger_)) {
        const message = 'Compilation error, ${output.errors.length} errors';
        this.emit('error', new PluginError(this.pluginName_, message));
      }

      const file = new File({
        path: outputFile || DEFAULT_OUTPUT_PATH,
        contents: new Buffer(output.compiledCode),
      });
      if (output.sourceMap) {
        file.sourceMap = JSON.parse(output.sourceMap);
      }
      this.push(file);

      cb();
    }
  };

  return function(compilationOptions, pluginOptions) {
    return new CompilationStream(compilationOptions || {}, pluginOptions || {});
  };
}
```
- example usage
```shell
...
'''

### Gulp

Your 'gulpfile.js' should contain a task like this-

'''js
const compiler = require('google-closure-compiler-js').gulp();

gulp.task('script', function() {
return gulp.src('./path/to/src.js', {base: './'})
    // your other steps here
    .pipe(compiler({
        compilationLevel: 'SIMPLE',
        warningLevel: 'VERBOSE',
...
```

#### <a name="apidoc.element.google-closure-compiler-js.logger"></a>[function <span class="apidocSignatureSpan">google-closure-compiler-js.</span>logger (options, output, logger)](#apidoc.element.google-closure-compiler-js.logger)
- description and source-code
```javascript
logger = function (options, output, logger) {
  logger = logger || console.warn;
  // TODO(samthor): If this file has a sourceMap, then follow it back out of the rabbit hole.
  function fileFor(file) {
    if (!file) { return null; }

    // Filenames are the same across source and externs, so prefer source files.
    for (const files of [options.jsCode, options.externs]) {
      if (!files) { continue; }

      for (const cand of files) {
        if (cand.path == file) {
          return cand;
        }
      }
    }

    return null;
  }

  function writemsg(color, msg) {
    if (!msg.file && msg.lineNo < 0) {
      logger(msg.type);
    } else {
      logger('${msg.file}:${msg.lineNo} (${msg.type})')
    }
    logger(msg.description);

    const file = fileFor(msg.file);
    if (file) {
      const lines = file.src.split('\n');  // TODO(samthor): cache this for logger?
      const line = lines[msg.lineNo - 1] || '';
      logger(color + line + COLOR_END);
      logger(COLOR_GREEN + caretPrefix(line, msg.charNo) + '^' + COLOR_END);
    }
    logger('');
  }

  output.warnings.forEach(writemsg.bind(null, COLOR_YELLOW));
  output.errors.forEach(writemsg.bind(null, COLOR_RED));

  return output.errors.length > 0;
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.google-closure-compiler-js.webpack"></a>[function <span class="apidocSignatureSpan">google-closure-compiler-js.</span>webpack (args)](#apidoc.element.google-closure-compiler-js.webpack)
- description and source-code
```javascript
webpack = function (args) {
  const compile = require('../compile');
  const logger = require('../logger');
  const RawSource = require('webpack-core/lib/RawSource');
  const SourceMapSource = require('webpack-core/lib/SourceMapSource');

  args.test = args && args.test ? new RegExp(args.test) : /\.js($|\?)/i;

  this.apply = function(compiler) {
    compiler.plugin('compilation', compilation => {
      compilation.plugin('normal-module-loader', context => {
        context.minimize = true;
      });

      compilation.plugin('optimize-chunk-assets', (chunks, callback) => {
        chunks.forEach(chunk => {
          const files = chunk.files.filter(name => args.test.test(name));
          files.forEach(name => {
            const options = args && args.options ? args.options : {};  // TODO

            const asset = compilation.assets[name];
            const sourceMap = asset.map();
            const file = {
              path: name,
              src: asset.source(),
              sourceMap: sourceMap ? JSON.stringify(sourceMap) : undefined,
            };
            options.jsCode = [file];

            const output = compile(options);
            if (logger(options, output)) {
              const message = 'Compilation error, ${output.errors.length} errors';
              compilation.errors.push(new Error(message));
              return;  // don't save compilation
            }

            let result;
            if (output.sourceMap) {
              result = new SourceMapSource(
                output.compiledCode, name, JSON.parse(output.sourceMap), file.src, file.sourceMap);
            } else {
              result = new RawSource(output.compiledCode);
            }
            compilation.assets[name] = result;

          });
        });

        callback();
      });

    });
  };

}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
