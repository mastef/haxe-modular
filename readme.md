# Haxe Modular

Code splitting and hot-reload for Haxe JavaScript applications.

## Why?

If you use **Haxe for JavaScript**, directly or indirectly (React, OpenFl,...), then
you probably want to:

- make your web app load instantly,
- make your HTML5 game load quicker,
- load sections / features / mini-games on-demand.

Haxe has an excellent, compact and optimised JS output, but it's always a single file;
even with good minification / gzip compression it can be a large payload.

*Modular can split gigantic Haxe-JS outputs into load-on-demand features,
without size/speed overhead, and without losing sourcemaps.*

## How?

```haxe
import MyClass;
...
load(MyClass).then(function(_) {
	var c = new MyClass();
});
```

The approach is to *reference one class asynchronously* in your code:

- **at compile time**, the *dependency graph* of the class is built and one additional JS
file will be emitted (bundling this class and all its dependencies),
- **at run time**, when the aynchronous reference is evaluated, the additional JS is
loaded (once) automatically.

## Where to start?

There are 2 ways to use Haxe Modular, depending on your project/toolchain/goals:

1. [standalone Modular](doc/start.md); zero dependencies, for any Haxe-JS project,
2. [Webpack Haxe Loader](https://github.com/jasononeil/webpack-haxe-loader);
   leverage the famous JS toolchain.

In both cases, it is advisable to read about the technical details:

- [Using Reflection? Need more granular control of bundling?](doc/advanced.md)
- [How does on-demand loading work at run time?](doc/how.md)

### What is the difference?

Both solutions:

- use Modular splitting under the hood,
- split automatically using a single `hxml` build configuration,
- support NPM dependencies,
- allow hot-reloading of code.

### What should I use?

1. [Standalone Modular](doc/start.md) is an easy, drop-in, addition to a regular
   Haxe JS build process - it is very lightweight and unobstrusive, and you don't need
   to learn Webpack.

   Using NPM modules however requires a bit of ceremony: all the NPM dependencies have to
   be gathered (manually) in a `libs.js` which is loaded upfront.

2. [Webpack Haxe Loader](https://github.com/jasononeil/webpack-haxe-loader) is a more
   powerful setup but you'll have to learn Webpack. Webpack is a complex and large system
   offering vast possibilities from the JS ecosystem.

### FAQ

Q: Where can I get more help? I have questions / issues...

- Join [haxe-modular chat on Gitter.im](https://gitter.im/haxe-react/haxe-modular)

Q: Is it only for React projects?

- **Of course not**; anything targeting JavaScript can use it.
- However it does offer React-specific additional features for code hot-reloading.

Q: Is it possible to minify the code?

- **Yes**, you can run a script minifying each of the resulting JS file - for instance 
  using [Closure Compiler](https://developers.google.com/closure/) in "simple" mode, 
  or [UglifyJS](https://www.npmjs.com/package/uglify-js), including with the `mangle` 
  option which rename variables. 
- However, you can NOT use `-lib closure` which processes the output directly. 
  Generally, Modular must be the exclusive post-processor of the compiler output.

Q: Can I extract a library/package from my code?

- **No**. For technical reasons, extracting a library (e.g. many classes used across
  the application) isn't possible yet - it wouldn't work with OpenFl for instance.
- You normally shouldn't need that! Modular is "usage" based and can slice the biggest 
  libraries to keep only what you really use, where you really use it.

Q: Can I still use the `includeFile` macro to inject JS code in the output?

- **Yes, but** only when the code is inserted at the top of the file; this is the
  default position when using `--macro includeFile('my-lib.js')`.

