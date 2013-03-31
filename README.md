# Stepdown

A simple control-flow library for node.JS that makes parallel execution, serial execution, and error handling painless.

## How to install

    npm install stepdown

## Basic Usage (Serial)

Stepdown exports a single function:

    stepdown([steps], options, callback)

Each of the functions in the `steps` Array is called serially until all steps have completed:
```javascript
var $$ = require('stepdown');
function somethingAsync(callback) {
  $$([
    function readDir($) {
      var req = http.request(options);
      req.end();
      
      // All callbacks in a step are parallel
      fs.readdir(__dirname, $.first());

      // Event assumes first argument isn't error
      req.on('connect', $.event())
    },
    function readFiles($, files, res) {
      // Create a new group
      var group = $.group();
      files.forEach(function (filename) {
        fs.readFile(__dirname + "/" + filename, group());
      });

      // groups can also take a type
      // 'ignore' waits for any results, but ignores errors
      var group = $.group('ignore');
      files.forEach(function (filename) {
        // These files may not exist
        fs.readFile(__dirname + "/" + filename + '.bkup', group());
      });

      // Branching is also supported
      $.run([
        function($) {
          fs.readFile('one', $.first())
        },
        function($) {
          // Callbacks can be called synchronously without a change in behavior
          // Spread passes multiple arguments
          $.spread()(null, 'arg1', 'arg2')
          $.spread()(null, 'arg3', 'arg4')
        }
      ], $.spread())
    },
    function showAll($, fileContents, fileBackups, arg1, arg2, arg3, arg4) {
      console.log(fileContents);

      // If there were no files short-circuit
      if (!fileContents.length) return $.end(null, 'failure, no files')

      // Return (non-undefined) value to callback
      return 'success!'
    }
  // All callback errors are coalesced
  ], callback);
}
```
### More comprehensive docs coming soon. Meanwhile, see [tests](https://github.com/Schoonology/stepdown/blob/master/test/stepdown.js) for comprehensive functionality.

## Attribution

This work stands on the shoulders of giants:

 * Tim Caswell [creationix](https://github.com/creationix), who created [Step](https://github.com/creationix/step).
 * Adam Crabtree [CrabDude](https://github.com/CrabDude), who created [Stepup](https://github.com/CrabDude/stepup).

## Improvements

Stepdown improves upon step in the following ways:

 * Async try/catch integrated by default
 * Error coalescing
 * Thanks to callback generators, stepdown continues to the next step by default (step when undefined was returned)

