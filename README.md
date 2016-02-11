### PurifyCSS
Exposes a function that takes content (HTML/JS/etc) and CSS, and returns purified CSS.

Purified CSS is made up of only the selectors you use. The goal is to reduce the size of CSS, similar to minify.

If your application is using a CSS framework, this is especially useful as many selectors are often unused.

<br />
***
<br />

### Install
```bash
npm install --save purify-css
```

### Require
```js
var purify = require('purify-css');
```
<br />
***
<br />
### Potential reduction
* [Bootstrap](https://github.com/twbs/bootstrap) file: ~140k
* App using ~40% of selectors.
* Minified: ~117k
* Purified + Minified: **~35k**


<br />
***
<br />

### Examples

<br />

##### Example with source strings

```js
var content = '<button class="button-active"> Login </button>';
var css = '.button-active { color: green; }   .unused-class { display: block; }';

console.log(purify(content, css));
```
logs out:

```
.button-active { color: green; }
```

<br />

##### Example with source strings + rejected option

```js
var content = '<button class="button-active"> Login </button>';
var css = '.button-active { color: green; }   .unused-class { display: block; }';

var options = {
  // Logs out the removed selectors.
  rejected: true
};

purify(content, css, options);
```
logs out:

```
.unused-class { display: block; }
```

<br />

##### Example with file patterns + writing to a file

```js
var content = ['**/src/js/*.js', '**/src/html/*.html'];
var css = ['**/src/css/*.css'];

var options = {
  // Will write purified CSS to this file.
  output: './dist/purified.css'
};

purify(content, css, options);
```

<br />

##### Example with mixed file patterns and source strings + minify

```js
var content = ['**/src/js/*.js', '**/src/html/*.html'];
var css = '.button-active { color: green; } .unused-class { display: block; }';

var options = {
  output: './dist/purified.css',
  
  // Will minify CSS code in addition to purify.
  minify: true
};

purify(content, css, options);
```

<br />

##### Example with callback

```js
var content = ['**/src/js/*.js', '**/src/html/*.html'];
var css = ['**/src/css/*.css'];

purify(content, css, function (purifiedResult) {
  console.log(purifiedResult);
});
```

<br />

##### Example with callback and options

```js
var content = ['**/src/js/*.js', '**/src/html/*.html'];
var css = ['**/src/css/*.css'];

var options = {
  minify: true
};

purify(content, css, options, function (purifiedAndMinifiedResult) {
  console.log(purifiedAndMinifiedResult);
});
```

<br />
***
<br />

### API in depth

```javascript
// Four possible arguments.
purify(content, css, options, callback);
```

<br />


#####  The ```content``` argument
##### Type: ```Array``` or ```String```

**```Array```** of file patterns to the files you want to search through for used classes (HTML, JS, PHP, ERB, Templates, anything that uses CSS selectors).

**```String```** of content you want us to look for used classes.

<br />

##### The ```css``` argument
##### Type: ```Array``` or ```String```

**```Array```** of file patterns to the CSS files you want us to filter.

**```String```** of CSS you want us to filter.

<br />

##### The (optional) ```options``` argument
##### Type: ```Object```

##### Properties of options object:

* **```minify:```** Set to ```true``` to minify. Default: ```false```.

* **```output:```** Filepath to write purified CSS to. Returns raw string if ```false```. Default: ```false```.

* **```info:```** Logs info on how much CSS was removed if ```true```. Default: ```false```.

* **```rejected:```** Logs the CSS rules that were removed if ```true```. Default: ```false```.

<br />

##### The (optional) ```callback``` argument
##### Type: ```Function```

A function that will receive the purified CSS as it's argument.

##### Example of callback use
``` javascript
purify(content, css, options, function(purifiedCSS){
  console.log(purifiedCSS, ' is the result of purify');
});
```

##### Example of callback use without options
``` javascript
purify(content, css, function(purifiedCSS){
  console.log('callback without options and received', purifiedCSS);
});
```

<br />
***
<br />

### Selector Detection in Content
<br />

##### Will be able to find ```button-active```
``` html
  <!-- html -->
  <!-- class directly on element -->
  <div class="button-active">click</div>
```

``` javascript
  // javascript
  // Anytime your class name is together in your files, it will find it.
  $(button).addClass('button-active');
```

<br />

##### Example finding dynamically created classes (```button-active```)
``` javascript
  // Can detect if class is split.
  var half = 'button-';
  $(button).addClass(half + 'active');

  // Can detect if class is joined.
  var dynamicClass = ['button', 'active'].join('-');
  $(button).addClass(dynamicClass);
  
  // Can detect various more ways, even if using a Javascript framework.
  // A React example.
  var classes = classNames({
    button-active: this.state.buttonActive
  });
  
  return (
    <button className={classes}>Submit</button>;
  );
```


<br />
***
<br />

### CLI Usage

```
$ npm install -g purify-css
```

```
$ purifycss
usage: purifycss <css> <content> [option ...]

options:
 --min                Minify CSS
 --out [filepath]     Filepath to write purified CSS to
 --info               Logs info on how much CSS was removed
 --rejected           Logs the CSS rules that were removed

 -h, --help           Prints help (this message) and exits
```
<br />
##### Example CLI Usage

```
$ purifycss src/js/main.js src/css/main.css src/css/bootstrap.css --min --info --out src/dist/index.css
```
This will concat both `main.css` and `bootstrap.css` and purify it by looking at what CSS selectors were used inside of `main.js`. It will then write the result to `dist/index.css`

The `--min` flag minfies the result.

The `--info` flag will print this to stdout:
```
##################################
PurifyCSS has reduced the file size by ~35.2%
##################################
```
The CLI currently does not support file patterns.

<br />
***
<br />

### Usage at Build Time

[Grunt](https://github.com/purifycss/grunt-purify-css)

[Gulp](https://github.com/purifycss/gulp-purifycss)

[webpack](https://github.com/purifycss/purifycss-webpack-plugin)
