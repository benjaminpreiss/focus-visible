[![Build Status](https://travis-ci.org/WICG/focus-visible.svg?branch=master)](https://travis-ci.org/WICG/focus-visible)

Based on the proposed CSS
[`:focus-visible`](https://drafts.csswg.org/selectors-4/#the-focus-visible-pseudo)
pseudo-selector,
this prototype adds a `focus-visible` class to the focused element,
in situations in which the `:focus-visible` pseudo-selector should match.

# Details

- Read the [Explainer](explainer.md).
- Read the [Spec](https://drafts.csswg.org/selectors-4/#the-focus-visible-pseudo).
- Try the [Demo](https://wicg.github.io/focus-visible/demo).
- [Give feedback!](https://github.com/WICG/focus-visible/issues)
    - Open discussions:
        - [What's the impact on users with low vision or cognitive impairments?](https://github.com/WICG/focus-visible/issues/128)
        - [Should :focus-visible match when returning focus or programmatically focusing?](https://github.com/WICG/focus-visible/issues/88)
        - [Brainstorm: options for opting in to always matching focus ring.](https://github.com/WICG/focus-visible/issues/42)

# Polyfill

## Installation

`npm install --save focus-visible`

_We recommend only using versions of the polyfill that have been published to npm, rather than
cloning the repo and using the source directly. This helps ensure the version you're using is stable
and thoroughly tested._

_If you do want to build from source, make sure you clone the latest tag!_

## Usage

### 1. Add the script to your page

```html
    ...
    <script src="/node_modules/focus-visible/dist/focus-visible.min.js"></script>
  </body>
</html>
```

### 2. Update your CSS

We suggest that users
selectively disable the default focus style
by selecting for the case when the polyfill is loaded
and `.focus-visible` is _not_ applied to the element:

```css
/*
  This will hide the focus indicator if the element receives focus via the mouse,
  but it will still show up on keyboard focus.
*/
.js-focus-visible :focus:not(.focus-visible) {
  outline: none;
}
```

If there are elements which should always have a focus ring shown,
authors may explicitly add the `focus-visible` class.
If explicitly added, it will not be removed on `blur`.

Alternatively, if you're using a framework which overwrites your classes ([#179](https://github.com/WICG/focus-visible/issues/179)),
you can rely on the `data-focus-visible-added` attribute.
```css
.js-focus-visible :focus:not([data-focus-visible-added]) {
  outline: none;
}
```

### How it works

The script uses two heuristics to determine whether the keyboard is being used:

- a `focus` event immediately following a `keydown` event where the key pressed was either `Tab`,
`Shift + Tab`, or an arrow key.
- focus moves into an element which requires keyboard interaction,
  such as a text field
- _TODO: ideally, we also trigger keyboard modality
  following a keyboard event which activates an element or causes a mutation;
  this still needs to be implemented._

### Dependencies

If you want to use `:focus-visible` with an older browser you'll need to include an additional polyfill for [`Element.prototype.classList`](https://caniuse.com/#feat=classlist).

In accordance with the W3C's new [polyfill
guidance](https://www.w3.org/2001/tag/doc/polyfills/#don-t-serve-unnecessary-polyfills), the
`:focus-visible` polyfill does not bundle other polyfills.

You can use a service like [Polyfill.io](https://polyfill.io) to download only the polyfills needed by the current browser. Just add the following line to the start of your page:

```html
<script src="https://cdn.polyfill.io/v2/polyfill.min.js?features=Element.prototype.classList"></script>
```

# Backwards compatibility
Until all browsers ship `:focus-visible` developers will need to use it defensively to avoid accidentally
removing focus styles in legacy browsers. This is easy to do with the polyfill.

```css
/*
  This will hide the focus indicator if the element receives focus via the mouse,
  but it will still show up on keyboard focus.
*/
.js-focus-visible :focus:not(.focus-visible) {
  outline: none;
}

/*
  Optionally: Define a strong focus indicator for keyboard focus.
  If you choose to skip this step then the browser's default focus
  indicator will be displayed instead.
*/
.js-focus-visible .focus-visible {
  …
}
```

As [explained by the Paciello Group](https://developer.paciellogroup.com/blog/2018/03/focus-visible-and-backwards-compatibility/), developers who don't use the polyfill can still defensively rely on `:focus-visible` using the
following snippet:

```css
/*
  Provide basic, default focus styles.
*/
button:focus {
  …
}

/*
  Remove default focus styles for mouse users ONLY if
  :focus-visible is supported on this platform.
*/
button:focus:not(:focus-visible) {
  …
}

/*
  Optionally: If :focus-visible is supported on this
  platform, provide enhanced focus styles for keyboard
  focus.
*/
button:focus-visible {
  …
}
```

In the future, when all browsers support `:focus-visible`, the
snippets above will be unnecessary. But until that time it's important
to be mindful when you use `:focus-visible` and to ensure you always
have a fallback strategy.

### Big Thanks

Cross-browser Testing Platform and Open Source <3 Provided by [Sauce Labs][homepage]

<a href="https://saucelabs.com"><img src="https://i.imgur.com/f2cK9ZQ.jpg" width="200"></a>

[homepage]: https://saucelabs.com
