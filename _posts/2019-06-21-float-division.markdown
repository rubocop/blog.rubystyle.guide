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
to RuboCop and we need some help with that. The discussion on the topic started [here](https://github.com/rubocop-hq/ruby-style-guide/issues/628).[^1]

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

Our team is leaning toward recommending `fdiv` and coercing only the left operand (as we feel that way you'll spot more quickly what's going on).
For RuboCop the idea is to enforce the left coercion style by default, mostly because `fdiv` is not used much in the wild.

So, what do you think we should recommend and why?

[^1]: After closing the ticket we've decided to ask for more feedback. We know we should have done things the other way around. :-)
