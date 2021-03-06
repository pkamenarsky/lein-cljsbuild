# cljsbuild-example-advanced

This is an example web application that uses [lein-cljsbuild][1],
[Ring][2], and [Compojure][3]. It demonstrates several things:

1. The use of lein-cljsbuild to build ClojureScript into JavaScript.
2. How to share code between Clojure and ClojureScript, including macros.
3. How to use lein-cljsbuild's various REPL commands.
4. How to configure lein-cljsbuild to perform multiple parallel builds.

To play around with this example project, you will first need
[Leiningen][4] installed.

## Running the App

Set up and start the server like this:

    $ lein deps
    $ lein ring server-headless 3000

Now, point your web browser at `http://localhost:3000`, and see the web app in action!

## Running the Tests

To run the unit tests:

    $ lein cljsbuild test

Note that if more than one test were configured in the project, the above command would
run all tests.  To run the "unit" tests in isolation:

    $ lein cljsbuild test unit

The unit tests live in `test-cljs`.  They are written in ClojureScript, and thus must
be compiled, so they have their own entry in the `:builds` configuration.  Note that
all of the `:source-path` entries from the `:builds` are added to the classpath, so
the tests can `:require` ClojureScript namespaces from, e.g., the `src-cljs` directory.

The example configuration uses [PhantomJS] (http://www.phantomjs.org) to run the tests.
See the `phantom/unit-test.js` file for details on how this works.

## Connecting Firefox to a REPL

First, in one terminal, start the Ring server:

    $ lein ring server-headless 3000

Now, in a different terminal, run `repl-launch` with the "firefox" identifier and the URL of the REPL demo page:

    $ lein trampoline cljsbuild repl-launch firefox http://localhost:3000/repl-demo

The REPL should start, and in a moment, Firefox should start up and browse to the `repl-demo`
page.  Viewing the source for `repl-demo`, you'll see that after loading the main JavaScript
file, it calls `example.repl.connect()`.  This function connects back to the REPL, thereby
allowing you to execute arbitrary ClojureScript code in the context of the `repl-demo` page.

There's also a launcher configured for a "naked" page.  This is just a simple static
HTML page that will connect to the REPL.  This is convenient when you want to run
a ClojureScript REPL with access to your project, but don't need a specific page to
be loaded at the time.  The biggest advantage to the "naked" launcher is that you don't
need to have your app running in the background:

    $ lein trampoline cljsbuild repl-launch firefox-naked

## Connecting PhantomJS to a REPL

[PhantomJS] (http://www.phantomjs.org) is a headless Webkit browser, which can be automated
via Javascript.  It provides a Javascript execution environment with access to all browser
features (the DOM, etc), without opening a browser GUI.

To try out a PhantomJS-based REPL, first start the Ring server in one terminal:

    $ lein ring server-headless 3000

Now, in a different terminal, run `repl-launch` with the "phantom" identifier and the URL of the REPL demo page:

    $ lein trampoline cljsbuild repl-launch phantom http://localhost:3000/repl-demo

The REPL should start, and in a moment, PhantomJS should start up and browse to the `repl-demo`
page, in the background.  This is a convenient way to interact with your application in cases
where you don't need to open a full browser UI.

As with the Firefox example, there's a launch configured for a "naked" page.  This is probably
the most convenient way to launch a REPL when you just want to try running a couple snippets
of ClojureScript code.  As with the "firefox-naked" launcher, you don't need your app to be
running in the background:

    $ lein trampoline cljsbuild repl-launch phantom-naked

[1]: https://github.com/emezeske/lein-cljsbuild
[2]: https://github.com/mmcgrana/ring
[3]: https://github.com/weavejester/compojure
[4]: https://github.com/technomancy/leiningen
