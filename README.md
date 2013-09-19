TraceKit - Cross browser stack traces.
=====================================

[![Build Status](https://travis-ci.org/occ/TraceKit.png?branch=master)](https://travis-ci.org/occ/TraceKit)

### Supports all major browsers, from IE6 to Opera, the Andriod webiew and everywhere in between.

Not all browsers support stack traces on error objects, but TraceKit squeezes
out as much useful information as possible and normalizes it. 3kB minified + gzipped


## Install

```
bower install tracekit
```
This places TraceKit at `components/tracekit/tracekit.js`. Install [bower](http://twitter.github.com/bower/): `npm install bower -g`, download npm with Node: http://nodejs.org

Then include the `<script>` in your page.

## Usage

First, register a subscriber for error reports:
```javascript
TraceKit.report.subscribe(function yourLogger(errorReport) {
  //send via ajax to server, or use console.error in development
  //to get you started see: https://gist.github.com/4491219
});
```

Then, make sure all your code is in a try/catch block:
```javascript
try {
  /*
   * your application code here
   *
   */
  throw new Error('oops');
} catch (e) {
  TraceKit.report(e); //error with stack trace gets normalized and sent to subscriber
}
```

In order to get stack traces, you need to wrap your code in a try/catch block like above. Otherwise the error hits `window.onerror` handler and will only contain the error message, line number, and column number.

You also need to throw errors with `throw new Error('foo')` instead of `throw 'foo'`.

You can unsubscribe some subscriber function by doing `TraceKit.report.unsubscribe(someFunction)`

#### Eliminating (anonymous function)'s

```javascript
Api.foo = function Api_foo() {
};
var bar = function barFn() { //'Fn' is to avoid errors in IE
};
```

We recommend the above convention of function naming, `Api_foo` always corresponds to `Api.foo`, `barFn` corresponds to `bar` - just as long as the function name is not the same as the identifier. Otherwise, you can have bugs in IE.

## Options

TraceKit will attempt to fetch an analyze source files, but you can turn this off using:

```javascript
TraceKit.remoteFetching = false;
```

You can also tell TraceKit to ignore global window errors with:

```javascript
TraceKit.collectWindowErrors = false;
```

View the source for more details and examples.

## Plugins

Because most browsers support a [very limited window.onerror](https://bugzilla.mozilla.org/show_bug.cgi?id=355430) that
does not pass an actual stack trace, TraceKit works around this with `TraceKit.wrap`. This is an easy mechanism for
wrapping async functions. Previous versions of TraceKit wrapped jQuery event handlers and setTimeout/setInterval by default.
That code is now in the plugins folder and can be used at will.

In most applications, you'll want to include all plugins to ensure full stacktraces for as many errors as possible.

Thankfully, in the newest version of the HTML5 Spec, and as of early September 2013, Chrome Canary [supports an
extended window.onerror signature](https://code.google.com/p/chromium/issues/detail?id=147127) that passes an actual
Error object along. In this case, TraceKit has the function `TraceKit.supportsExtendedWindowOnError`, which calls back 
with a boolean. If true, `window.onerror` has superpowers and wrapping plugins should halt as their functionality
is no longer needed.

`tracekit.noplugins.min.js` does not wrap setTimeout/setInterval or jQuery. 

`tracekit.min.js` contains setTimeout/setInterval & jQuery wrapping. If you need one but not the other,
include the file in /plugins manually.

![Stacktrace or GTFO](http://i.imgur.com/jacoj.jpg)

## Contributing

Right now the project is being over-hauled, please jump into #shieldjs on freenode to discuss before submitting a big/medium PR (this saves us all time)

All code must pass JSHint and tests, run `grunt` for this. New features need accompanying documentation in the README, changes to existing api's need updated documentation. In general, open an issue for whatever it is you're thinking, get some quick feedback, make good stuff, and we'll accept the PR.

## License

(The MIT License)

Copyright (c) 2013 Onur Can Cakmak <onur.cakmak@gmail.com> and all TraceKit contributors.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
