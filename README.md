# jetpack 🚀

Start building apps with 0 setup.

Rapidly **start**, **develop**, **build** and **release** production ready apps. Jetpack makes running browser JavaScript as easy as using `node .`. Use `jetpack` to:

* run a bit of JavaScript in the browser
* build production ready client side only apps
* build production ready client and server apps

Jetpack is a **command line tool wrapping webpack and nodemon to give you the quickest development workflow**. It comes with `build` and `start` commands for running those applications in production.

## Convention over configuration

`jetpack` includes an opinionated, yet configurable, set of defaults.

For the **client**:
- client side bundling powered by [webpack](https://webpack.js.org/)
- es6 code transpiled with [babel](https://babeljs.io/) with [babel-preset-env](https://babeljs.io/docs/plugins/preset-env/)
- jsx is transpiled to h/React.createElement/Preact.h depending on what's installed
- use good ol css with autoprefixing with `require('./styles.css')`
- hot reload everything with `module.hot.accept()`

For the **server**:

- server side reloading powered by [nodemon](github.com/nodemon/nodemon)
- use the very lightweight built in express based server `jetpack/server` for rapid prototypes
- or use any server library of your choice and use `jetpack/handle` to handle the client side concerns seamlessly

## Architecture

Jetpack brings together a common pattern into a single command. It streamlines the dev and production flows for JavaScript projects.

![architecture](https://user-images.githubusercontent.com/324440/34653785-13f2b956-f3e9-11e7-8fb9-15285bb7f658.png)

## Reasoning

One way to think about `jetpack` is as a better `webpack-dev-server`. By better, I mean that `jetpack` is useful both for `dev` and `production`. The second way it's better is that it requires no configuration to get you started by providing sensible defaults.

Node is very cool in it's flexibility and very large ecosystem of approaches and libraries. But whenever someone new to node asked me - "how do you build a web app?", I sigh. Even me as an experienced JavaScripter I get frustrated when starting new projects, since it's not very clear how to best arrange `webpack-dev-server`/`webpack-dev-middleware`/`express` server such that it's both convenient to develop and is easily deployable.

The 2 goals for `jetpack` are:

* Enable anyone to run any client side bit of code in the browser immediately with `jetpack script.js` the way you'd run a server side script with `node script.js`.
* Enable anyone to build a web app that has client side code and server side bits (which are so often useful) without having to learn or remember anything about best practises of setting up JS projects.

I'm aware, from my own experience, that sometimes it's hard to trust a "magical" tool that does stuff for you. But I've built `jetpack`, because I think there's a gap in the ecosystem for a sensible default web app project approach.

## Usage

Install globally:

    npm install -g jetpack

In your project, with package.json and/or index.js, start your app on http://localhost:3000:

    jetpack

To build the app for production to `dist` directory:

    jetpack build

To serve the app in production after it's been built, run:

    jetpack start

If you've reached a point where you want to switch away from using jetpack and jump into raw webpack (coming soon):

    jetpack unstrap

## Configuration

You can change some config in `jetpack.config.js` or via command line arguments:

```js
module.exports = {
    port: 3000,
    jsx: "h", // Preact.h/React.createElement if preact/react is installed
    html: "./index.html", // if you want to change the default html served
    client: "client", // the directory with the client code, ignored by nodemon
    server: "server", // the directory with the server code,
    static: "static", // if you want to reference assets like images,
    dist: "dist", // the destination for building the client code
    quiet: false, // no jetpack logs, only errors and logs from the app,
    verbose: false, // more detailed logs, restarts, detailed webpack stats,
    hot: true // enable/disable hot reloading
}
```

## Hot reloading

CSS is hot reloaded automatically. For JavaScript you need a bit of code. E.g. in your main render module or entry point, you should accept hot reloads, which will rerender your app. You'll want to store the state on window or in localStorage if you want the app to remain in the same state after rerendering. If `module.hot.accept` is not called, the entire page will be reloaded. You can disable hot reloading entirely by passing `--no-hot` to jetpack or setting `hot: false` in your `jetpack.config.js`.

```js
if (module.hot) {
  module.hot.accept()
  module.hot.dispose(() => {
    // perform cleanup
  })
}
```

Server side code is reloaded using `nodemon`.

## Custom webpack loaders

*(coming soon)*

You can extend the default webpack config using `jetpack.config.js`:

```js
module.exports = {
  webpack: (config, { dev }) => {
    // Perform customizations to webpack config
    // Important: return the modified config
    return config
  },
  webpackDevMiddleware: config => {
    // Perform customizations to webpack dev middleware config
    // Important: return the modified config
    return config
  }
}
```

## FAQ

**Is this meant to replace webpack?** No.

**So when should I use this?** When you want to run a snippet of code in the browser, when you want to try an npm package, when you have an idea for an app and want to start hacking on it right away (instead of spending time setting up boilerplate), when you're live coding in a talk.

**Should I use it for production apps?** Absolutely, that's the idea.

**What about [Neutrino from Mozilla](https://neutrino.js.org/)** Neutrino seems awesome, I hope it takes off. I'd like to look into integrating Jetpack with Neutrino if that makes sense. Neutrino is a more ambitious approach of improving webpack's API for production apps by creating composable presets. It would be cool if jetpack automatically pulled in installed neutrino presets.

**Can I use TypeScript or Flow?** Hm, haven't figured this one out atm. Maybe via neutrino presets for the client.

**Can I use `jetpack` with a different server side language** Yeah, run your client side code server with `jetpack` and load `http://localhost:3000/bundle.js` in your server project.

**I want to do server side rendering** You can do it with `jetpack`, but there isn't any special help provided. You can render your own html in your custom server. Your best bet might be to use something like [next.js](https://github.com/zeit/next.js) in this case.
