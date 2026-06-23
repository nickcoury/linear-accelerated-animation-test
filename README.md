# linear(0,1) Compositor Acceleration Test

A test page to check whether browsers properly compositor-accelerate the CSS `linear(0, 1)` easing function — which is mathematically identical to the `linear` keyword but may not be recognized as a fast-path by all browsers.

## Live Demo

👉 **https://nickcoury.github.io/linear-accelerated-animation-test/**

## How It Works

Two boxes run identical `transform: translateY()` animations (which are eligible for compositor-thread acceleration):

| Section | Easing | Expected |
|---------|--------|----------|
| Green box | `linear` | Always smooth — browsers always accelerate this |
| Yellow box | `linear(0, 1)` | Should be identical, but may stutter if not accelerated |

A **jank injector** blocks the main thread with a spin-lock (`while` loop) every 200ms for ~90ms. This freezes any animation driven on the main thread while compositor-driven animations continue smoothly.

## How to Test

1. Open the page in a browser
2. Click **Enable Jank**
3. Watch both boxes:
   - **Both smooth** → browser accelerates `linear(0, 1)` ✓
   - **Green smooth, yellow stutters** → browser does NOT accelerate `linear(0, 1)` ✗

## Background

The CSS `linear()` easing function (part of [CSS Easing Level 2](https://drafts.csswg.org/css-easing-2/#linear-easing-functions)) allows defining piecewise-linear easing curves. `linear(0, 1)` produces a straight line from 0 to 1 — identical to the `linear` keyword. However, some browsers may not recognize this equivalence at parse time and may fall back to main-thread animation instead of promoting it to the compositor thread.
