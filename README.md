# Font Observer

Font Observer is a small `@font-face` loader and monitor (3.5KB minified and 1.3KB gzipped) compatible with any webfont service. It will monitor when a webfont is loaded and notify you. It does not limit you in any way in where, when, or how you load your webfonts. Unlike the [Web Font Loader](https://github.com/typekit/webfontloader) Font Face Observer uses scroll events to detect font loads efficiently and with minimum overhead.

## How to use

Include your `@font-face` rules as usual. Fonts can be supplied by either a font service such as [Google Fonts](http://www.google.com/fonts), [Typekit](http://typekit.com), and [Webtype](http://webtype.com) or be self-hosted. You can set up monitoring for a single font family at a time:

```js
var font = new FontObserver('My Family', {
  weight: 400
});

font.load().then(function () {
  console.log('Font is available');
}, function () {
  console.log('Font is not available');
});
```

The `FontObserver` constructor takes two arguments: the font-family name (required) and an object describing the variation (optional). The object can contain `weight`, `style`, and `stretch` properties. If a property is not present it will default to `normal`. To start loading the font, call the `load` method. It'll immediately return a new Promise that resolves when the font is loaded and rejected when the font fails to load.

If your font doesn't contain at least the latin "BESbwy" characters you must pass a custom test string to the `load` method.

```js
var font = new FontObserver('My Family');

font.load('中国').then(function () {
  console.log('Font is available');
}, function () {
  console.log('Font is not available');
});
```

The default timeout for giving up on font loading is 3 seconds. You can increase or decrease this by passing a number of milliseconds as the second parameter to the `load` method.

```js
var font = new FontObserver('My Family');

font.load(null, 5000).then(function () {
  console.log('Font is available');
}, function () {
  console.log('Font is not available after waiting 5 seconds');
});
```

Multiple fonts can be loaded by creating a `FontObserver` instance for each.

```js
var fontA = new FontObserver('Family A');
var fontB = new FontObserver('Family B');

fontA.load().then(function () {
  console.log('Family A is available');
});

fontB.load().then(function () {
  console.log('Family B is available');
});
```

You may also load both at the same time, rather than loading each individually.

```js
var fontA = new FontObserver('Family A');
var fontB = new FontObserver('Family B');

Promise.all([fontA.load(), fontB.load()]).then(function () {
  console.log('Family A & B have loaded');
});
```

If you are working with a large number of fonts, you may decide to create `FontObserver` instances dynamically:

```js
// An example collection of font data with additional metadata,
// in this case “color.”
var exampleFontData = {
  'Family A': { weight: 400, color: 'red' },
  'Family B': { weight: 400, color: 'orange' },
  'Family C': { weight: 900, color: 'yellow' },
  // Etc.
};

var observers = [];

// Make one observer for each font,
// by iterating over the data we already have
Object.keys(exampleFontData).forEach(function(family) {
  var data = exampleFontData[family];
  var obs = new FontObserver(family, data);
  observers.push(obs.load());
});

Promise.all(observers)
  .then(function(fonts) {
    fonts.forEach(function(font) {
      console.log(font.family + ' ' + font.weight + ' ' + 'loaded');

      // Map the result of the Promise back to our existing data,
      // to get the other properties we need.
      console.log(exampleFontData[font.family].color);
    });
  })
  .catch(function(err) {
    console.warn('Some critical font are not available:', err);
  });
```

The following example emulates FOUT with Font Face Observer for `My Family`.

```js
var font = new FontObserver('My Family');

font.load().then(function () {
  document.documentElement.className += " fonts-loaded";
});
```

```css
.fonts-loaded {
  body {
    font-family: My Family, sans-serif;
  }
}
```

## Installation

If you're using npm you can install Font Face Observer as a dependency:

```shell
$ npm install font-observer
```

You can then require `fontobserver` as a CommonJS (Browserify) module:

```js
var FontObserver = require('font-observer');

var font = new FontObserver('My Family');

font.load().then(function () {
  console.log('My Family has loaded');
});
```

## Browser support

font-observer has been tested and works on the following browsers:

* Chrome (desktop & Android)
* Firefox
* Opera
* Safari (desktop & iOS)
* IE8+
* Android WebKit

## License

Font Observer is licensed under the Apache Licence. Copyright 2014-2017 Anish M Prasad. All rights reserved.