---
layout: post
title:  "Floating-point Division"
date:   2019-06-21 12:45:05 +0300
categories: ruby
author: Bozhidar Batsov
---

Hey, everyone!

We're in the process of adding a guideline about the recommended way to do floating-point division with integer operands to
the [Ruby style guide](https://rubystyle.guide) and a [matching cop](https://github.com/rubocop-hq/rubocop/pull/7153)
to RuboCop and we need some help with that.

There are four ways to do float division:

``` ruby
# foo and bar are both Integer values.

# Coerce both operands to Float.
foo.to_f / bar.to_f
# Coerce only left one.
foo.to_f / bar
# Coerce only the right one.
foo / bar.to_f
# Use the dedicated fdiv method.
foo.fdiv(bar)
```

The whole discussion started [here](https://github.com/rubocop-hq/ruby-style-guide/issues/628).[^1]
Our team is leaning toward recommending `fdiv` and coercing only the left operand (as we feel that way you'll spot more quickly what's going on).
For RuboCop the idea is to enforce the left coercion style by default, mostly because `fdiv` is not used much in the wild.

In the spirit of broader community collaboration we've started this blog so that more people can see what we're working on, and participate in the discussions
around introducing new guidelines and changing existing ones.[^2]
So, what do you think we should recommend and why?

[^1]: After closing the ticket we've decided to ask for more feedback.
[^2]: Discussions have been happening for years on the issue trackers of the style guides, but we hope that this blog is going to help us to reach out to more people.
