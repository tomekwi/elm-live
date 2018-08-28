[![Travis – build status
](https://img.shields.io/travis/tomekwi/elm-live/master.svg?style=flat-square
)](https://travis-ci.org/tomekwi/elm-live
) [![Coveralls – test coverage
](https://img.shields.io/coveralls/tomekwi/elm-live.svg?style=flat-square
)](https://coveralls.io/r/tomekwi/elm-live
)




# elm-live

**A flexible dev server for Elm  
Live reload included!**




<a id="/screenshot"></a>&nbsp;

<p align="center"><img
  alt="Screencast"
  src="https://cdn.rawgit.com/tomekwi/elm-live/b990094/screencast.gif"
  title="Sweet, isn’t it?"
  width="405"
/></p>




<a id="/installation"></a>&nbsp;

## INSTALLATION

### If you are using Elm 0.19

We are in a state where `elm-live` is ready to work with Elm 0.19, but we are waiting on one update from another package before we can officially launch `elm-live` 3.0.0. So, in the meantime use the options below to install `elm-live` for use with 0.19 and check out the [caveat](#my-app-is-not-getting-reloaded) below if you are using `Browser.document` or `Browser.application`.

```sh
# Globally for a user:
npm install --global elm elm-live@next

# …or locally for a project:
npm install --save-dev elm elm-live@next
```

### If you are using Elm 0.18

```sh
# Globally for a user:
npm install --global elm elm-live

# …or locally for a project:
npm install --save-dev elm elm-live
```

If you’d rather bring your own global `elm`, `npm install --global elm-live` will do.

Note that you need *node 6.0+* to run the tool natively. But if you’re stuck on an older version, don’t worry! [Rumour has it](https://github.com/tomekwi/elm-live/issues/2#issuecomment-156698732) that you can transpile the code to ES5!




<a id="/synopsis"></a>&nbsp;

## SYNOPSIS

```sh
elm-live [...<options>] [--] ...<elm make args>  
elm-live --help
```




<a id="/description"></a>&nbsp;

## DESCRIPTION

First, we spawn `elm make` with the `elm make args` you’ve given.

When the build is ready, we start a static HTTP server in the current directory. We inject a _live reload_ snippet into every HTML file we serve. Every time a static file has changed, we’ll reload your app in all browsers you’ve opened it with. (Mobile and IE included!)

We also watch all `*.elm` files in the current directory and its subdirectories. Whenever you change, add or remove one of them, we’ll rebuild your program and reload the page.




<a id="/options"></a>&nbsp;

## OPTIONS

#### `--port=PORT`
Set the port to start the server at. If the port is taken, we’ll use the next available one. `PORT` should be a valid port number. Default: `8000`.

#### `--path-to-elm=PATH`
An absolute or relative path to `elm`. If you’ve installed _elm-platform_ locally with _npm_ (`npm install --save-dev elm`), you’ll likely want to set this to `node_modules/.bin/elm`. Default: `elm`.

#### `--host=HOSTNAME|IP`
Set the host interface to attach the server to. Default: `localhost`.

#### `--dir=PATH`
The base for static content. Default: `.`.

#### `--open`
We’ll open the app in your default browser as soon as the server is up.

#### `--no-recover`
When _elm make_ encounters a compile error, we keep _elm-live_ running and give you time to fix your code. Pass `--no-recover` if you want the server to exit immediately whenever it encounters a compile error.

#### `--pushstate`
Serve `index.html` on 404 errors. This lets us use client-side routing in Elm. For instance, we can have a URL like `http://localhost:8000/account` get handled by the Elm _navigation_ package instead of failing with a 404 error.

#### `--proxy=PATH_PREFIX::HOST(::STRIP_PREFIX)
Proxy requests to paths that start with `PATH_PREFIX` to another server runing at `HOST`. This can be helpful when developing against a backend server serving an API.
If `STRIP_PREFIX` is given, it will be removed from the start of the path before being proxied.
As an example `--proxy=/api::http://localhost:8080::/api` will result in requests to `/api/posts` to be proxied to `http://localhost:8080/posts`.

#### `--before-build=EXECUTABLE`
Run `EXECUTABLE` before every rebuild. This way you can easily use other tools like _elm-css_ or _browserify_ in your workflow.

Heads up! At the moment, we only allow running a single executable without parameters. If you need more than that, please give us a shout at https://git.io/elm-live.before-build-args.

#### `--after-build=EXECUTABLE`
Just like `--before-build`, but runs after `elm make`.

#### `--help`
You’re looking at it.




<a id="/examples"></a>&nbsp;

## EXAMPLES

The simplest scenario:

```sh
$ elm-live Main.elm --open
```

Custom HTML file:

```html
$ cat <<——— > index.html
  <!doctype html>
  <link rel="stylesheet" href="style.css" />

  <body>
    <div></div>
    <script src="elm.js"></script>
    <script>Elm.Main.embed(document.querySelector("div"));</script>
  </body>
———
$ elm-live Main.elm --output=elm.js --open
```

Support client-side routing in Elm:

```sh
$ elm-live Main.elm --open --pushstate
```


<a id="/troubleshooting"></a>&nbsp;

## TROUBLESHOOTING

#### My app is not getting reloaded

If you are using `Browser.sandbox`, `Browser.element`, or Elm 0.18 your HTML file must have an explicit `<body>` tag, so that we know where to inject a LiveReload snippet.

If you are using `Browser.document` or `Browser.application` you will need to manually add the LiveReload script found below right after your opening `<head>` tag.

> We are working with the LiveReload package author to get this updated to where we can inject the script in the `<head>` tag by default, but this is the fix in the meantime

**LiveReload Script to add after `<head>` tag:**

```html
<script type="text/javascript" src="//localhost:35729/livereload.js?snipver=1" async="" defer=""></script>
```

If for any reason this is not working verify that the localhost address in the script above matches the port number shown in the output of `elm-live` on the line that reads:

```
[0001] info  LiveReload running on 35729
```


#### I’m seeing a SyntaxError about block-scoped declarations

If you’re seeing one of these:

```
SyntaxError: Block-scoped declarations (let, const, function, class) not yet supported outside strict mode
```

make sure you’re running on node 6+. If you can’t upgrade, consider [transpiling](https://github.com/tomekwi/elm-live/issues/2#issuecomment-156698732) `source/elm-live.js` to ES5.

By the way, [yarn](https://github.com/yarnpkg/yarn) should be warning you about installing incompatible packages. To get the same behavior in npm, [set the `engine-strict`](https://docs.npmjs.com/misc/config#engine-strict) flag.




<a id="/credits"></a>&nbsp;

## CREDITS

Many thanks to [Evan Czaplicki](https://github.com/evancz), the creator of Elm, for [git.io/elm-make](https://git.io/elm-make) – the most brilliant language compiler the world has ever seen! Without _elm make_, _elm-live_ would be a car without an engine.

Many thanks to [Matt DesLauriers](https://github.com/mattdesl) for the wonderful [git.io/budo-server](https://git.io/budo-server). That’s what does the heavy lifting on the static server side.

Warm thanks to our amazing contributors! Credits to [Brian](https://github.com/bdukes) for making Windows support possible, [Kurt](https://github.com/kbsymanz) for allowing a configurable `--host` and [Josh](https://github.com/joshmh) for his work on enabling client-side navigation. Thanks to [Ryan](https://github.com/Ryan1729) batch updates are nice and fast. Kudos to [Mathieu](https://github.com/magopian), [Rémy](https://github.com/natim) and [Nicolas](https://github.com/n1k0) for making the developer experience smoother and to [Gabriel](https://github.com/peacememories) for the `--before-build` option. Many thanks to [Noah](https://github.com/eeue56) for making sure elm-live works smoothly with [elm-test](https://github.com/elm-community/elm-test).




<a id="/license"></a>&nbsp;

## LICENSE

[MIT](https://git.io/elm-live.License) © [Tomek Wiszniewski](https://github.com/tomekwi)
