# Clojure Style Sheets

<img src="logo.png" width="155" height="68" alt="cljss logo" />

[CSS-in-JS](https://speakerdeck.com/vjeux/react-css-in-js) for ClojureScript

[![Clojars](https://img.shields.io/clojars/v/org.roman01la/cljss.svg)](https://clojars.org/org.roman01la/cljss)

## Table of Contents
- [Features](#features)
- [How it works](#how-it-works)
- [Installation](#installation)
- [Usage](#usage)
- [Production build](#production-build)
- [Issues](#issues)
- [License](#license)

## Features
- Isolated styles by generating unique names
- Supports CSS pseudo-classes and pseudo-elements
- Injects styles into DOM within `<style>` tags in development
- Outputs styles into a single file for production

## How it works
`defstyles` macro accepts a hash map of style definitions and returns a hash map from style names to generated unique names.

## Installation

Add to project.clj: `[org.roman01la/cljss "0.1.0-SNAPSHOT"]`

## Usage

`(defstyles name styles)`

- `name` name of a var
- `styles` a hash map of styles definitions

Using [Sablono](https://github.com/r0man/sablono) templating for [React](https://facebook.github.io/react/)
```clojure
(ns example
  (:require [cljss.core :refer-macros [defstyles]]
            [sablono.core :refer-macros [html]]))

(defstyles styles
  {:list {:display "flex"
          :flex-direction "column"}
   :list-item {:height "48px"
               :background "#cccccc"
               :color "#242424"
               :font-size "18px"}
   :list-item:hover {:background "#dddddd"}
   :list-item:last-child::after {:content "last item"
                                 :display "block"
                                 :position "relative"}})

;; styles =>
;; {:list "list43696",
;;  :list-item "list-item43697"}

(html
  [:ul {:class (:list styles)}
   [:li {:class (:list-item styles)} "Item#1"]
   [:li {:class (:list-item styles)} "Item#2"]
   [:li {:class (:list-item styles)} "Item#3"]])
```

Output CSS (pretty):
```css
.list43696 {
  display: flex;
  flex-direction: column;
}
.list-item43697 {
  height: 48px;
  background: #cccccc;
  color: #242424;
  font-size: 18px;
}
.list-item43697:hover {
  background: #dddddd;
}
.list-item43697:last-child::after {
  content: "last item";
  display: block;
  position: relative;
}
```

## Production build

Compiler options

```clojure
{:compiler
 {:css-output-to "resources/public/css/styles.css"}}
```

## Issues
- `shouldComponentUpdate` optimization in React components prevents applying new styles when using code reloading e.g. [Figwheel](https://github.com/bhauman/lein-figwheel). A workaround would be to pass styles explicitly into a component.
- If you are using [Figwheel](https://github.com/bhauman/lein-figwheel) with build config validation enabled, you'll see an error `The key :css-output-to is unrecognized` in REPL when starting a project.
Set `:validate-config :ignore-unknown-keys` in Figwheel config to only validate options it recognizes.

## License

Copyright © 2017 Roman Liutikov

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
