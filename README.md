**gatsby-plugin-transitions** enables animated page-transitions. It uses react-spring for smooth, customizable animations.

- Default animation for every page-transition
- Define Special animations for certain links additionally
- Two animation-modes: successive (animate out, then animate in) and immediate (in and out at the same time)

[![NPM](https://img.shields.io/npm/v/gatsby-plugin-transitions.svg)](https://www.npmjs.com/package/gatsby-plugin-transitions) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

🚀 [Have a look at the example!](https://andreasfaust.github.io/gatsby-plugin-transitions/)

**This Project is under development. Please join and contribute!**

## Install

Install all dependencies via Yarn or NPM.

```bash
yarn add gatsby-plugin-transitions gatsby-plugin-layout react-spring react react-dom
```

## Usage

### 1. Register gatsby-plugin-layout

Register `gatsby-plugin-layout` in your `gatsby-config.js`:

```js
module.exports = {
  plugins: ["gatsby-plugin-layout"]
};
```

### 2. Disable default scroll-to-top

Copy this into your `gatsby-browser.js`:

```js
exports.shouldUpdateScroll = () => {
  return false;
};
```

### 3. Create default Layout-file

Create the folder `src/layouts` and the file `src/layouts/index.js`.
Here you need to wrap all `children` into the component `TransitionProvider`

```jsx
import React from "react";
import { TransitionProvider } from "gatsby-plugin-transitions";

const Layout = ({ location, children }) => {
  return (
    <TransitionProvider location={location}>{children}</TransitionProvider>
  );
};

export default Layout;
```

🎉 **Voila!** You have smooth animated page-transitions! **Now customize these!**

## TransitionProvider

List of props:

| **Name**     | **Type** | **Default**                       | **Description**                                  |
| :----------- | :------- | :-------------------------------- | :----------------------------------------------- |
| **location** | Object   | `null`                            | **required.** Gatsby’s location-object.          |
| **mode**     | String   | `'successive'`                    | Transition-mode: `'successive'` or `'immediate'` |
| **enter**    | object   | `{ opacity: 0, config: 'stiff' }` | From-values, when the view is entering           |
| **usual**    | object   | `{ opacity: 1, config: 'stiff' }` | Normal state of the view.                        |
| **leave**    | object   | `{ opacity: 0, config: 'stiff' }` | To-Values, when the view is leaving.             |
| **y**        | number   | `0`                               | Scroll position of the next view.                |

### Transition-Mode

- `successive`: Wait till previous view has disappeared.
- `immediate`: Next view is entering while previous view is disappearing.

### Default-Springs

You can enter default-springs for all animation-states:

- `enter`: From-values, when the view is entering.
- `usual`: Normal animation-state of the view.
- `leave`: To-Values, when the view is leaving.

🔥 **Caution:** In react-spring the values of the previous animation persist. For example: If you want to execute a `onRest`-function only on `enter`, you have to overwrite it in `leave`!

#### opacity and transform

These props accept a regular [**react-spring**-object](https://www.react-spring.io/docs/hooks/api).
Animated are currently only the keys `opacity` and `transform`.

#### config

The key `config` can be either a regular **react-spring**-config-object.

Or pass in the name of a **react-spring**-default (`default`, `gentle`, `wobbly`, `stiff`, `slow`, `molasses`) as string.

## TransitionLink

`gatsby-plugin-transition` works out of the box with Gatsby's default `Link`-component. If you want to apply custom animations to certain links, use `TransitionLink`.

```jsx
import React from "react";
import { TransitionLink } from "gatsby-plugin-transitions";

const MyComponent = () => (
  <div className="content content--1">
    <h1>gatsby-plugin-transitions</h1>
    <p>Transitions are easy.</p>
    <p>Now go build something great.</p>
    <TransitionLink
      to="/page-2"
      style={{ color: "red" }}
      className="my-custom-link"
      leave={{
        opacity: 0,
        transform: "translate3d(100vh,0vh,0)",
        config: "slow"
      }}
      enter={{
        opacity: 0,
        transform: "translate3d(100vh,0vh,0)",
        config: "stiff"
      }}
      usual={{
        transform: "translate3d(0vh,0vh,0)",
        opacity: 1
      }}
      mode="immediate"
      y={1000}
    >
      I have a special animation!
      <br />
      And mode 'immediate'!
      <br />
      Go to page 2
    </TransitionLink>
  </div>
);

export default MyComponent;
```

List of props:

| **Name**  | **Type** | **Default**                       | **Description**                                  |
| :-------- | :------- | :-------------------------------- | :----------------------------------------------- |
| **to**    | Object   | `''`                              | **required.** Pathname of your link-target.      |
| **mode**  | String   | `'successive'`                    | Transition-mode: `'successive'` or `'immediate'` |
| **enter** | object   | `{ opacity: 0, config: 'stiff' }` | From-values, when the view is entering           |
| **usual** | object   | `{ opacity: 1, config: 'stiff' }` | Normal state of the view.                        |
| **leave** | object   | `{ opacity: 0, config: 'stiff' }` | To-Values, when the view is leaving.             |
| **y**     | number   | `0`                               | Scroll position of the next view.                |

## useTransitionStore

A hook, that exposes the plugin’s state-management.
It returns an `Array` with 2 elements:

1.  **state** of type `object`
2.  **dispatch** of type `function`

Get some useful information from the module’s store!
**For example get the current location-object:**

```jsx
import React from "react";
import { useTransitionStore } from "gatsby-plugin-transitions";

const MyComponent = () => {
  const [{ currentLocation }] = useTransitionStore();
  return <h1>{currentLocation.pathname}</h1>;
};

export default MyComponent;
```

**Or route to another page, when the user scrolls to the bottom of the page:**
Works the same way like `TransitionLink`!

```jsx
import React, { useEffect, useState } from "react";
import { useTransitionStore } from "../transitions";

const MyComponent = () => {
  const [, dispatch] = useTransitionStore();
  useEffect(() => {
    function onScroll() {
      if (
        window.innerHeight + window.pageYOffset >=
        document.body.offsetHeight - 2
      ) {
        dispatch({
          type: "NAVIGATE",
          to: "/",
          leave: {
            opacity: 0,
            transform: "translate3d(0, -50vh, 0)",
            config: "stiff"
          },
          y: 1000
        });
      }
    }
    window.addEventListener("scroll", onScroll);
    return () => window.removeEventListener("scroll", onScroll);
  }, []);

  return (
    <div className="content" style={{ minHeight: "300vh" }}>
      <h1>Scroll down to get back home!</h1>
    </div>
  );
};

export default MyComponent;
```

## To-Do

- [ ] Testing

## Contributing

Every contribution is very much appreciated.

😍 **If you like gatsby-plugin-transitions, don't hesitate to star it on [GitHub](https://github.com/AndreasFaust/gatsby-plugin-transitions).**

## License

MIT © [AndreasFaust](https://github.com/AndreasFaust)
