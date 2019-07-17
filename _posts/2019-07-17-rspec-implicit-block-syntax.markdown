---
layout: post
title:  "Implicit Block Expectation Syntax"
date:   2019-07-17 10:15:00 +0300
categories: rspec
author: Phil Pirozhkov
---

Hey, everyone!

We planned to add a guideline to recommend against using an RSpec practice codenamed "Implicit Block Expectation Syntax", but received some controversial feedback.
In order to keep the [RSpec style guide](https://rspec.rubystyle.guide) reflecting the opinion of the Ruby community, we decided to highlight this topic and start a discussion.

The syntax is as following:

```ruby
subject { -> { do_something } }
it { is_expected.to change(something).to(new_value) }
```

As you know, [`change`](https://relishapp.com/rspec/rspec-expectations/v/3-8/docs/built-in-matchers/change-matcher) is a block matcher.
The above Implicit Block Expectation Syntax can be expressed with explicit syntax:

```ruby
it 'changes something to a new value' do
  expect { do_something }.to change(something).to(new_value) }
end
```

Pros for using the syntax in question that we're aware of are:
 - allows to use block matchers with one-liner syntax (`is_expected`)
 - no need to repeat `expect { ... }`

Cons:
 - an exception to the case when `subject` is cached, e.g. on each `is_subject` call the passed lambda will be re-executed
 - rarely used and not unfamiliar to many people
 - barely possible to detect it as a block expectation syntax with static analysis tools (RuboCop, Reek)
 - the guide [doesn't recommend using one-liner syntax](https://rspec.rubystyle.guide/#use-subject) except in some cases
 - there are no tests or documentation in RSpec that cover this syntax

Here's RSpec core team opinion on the topic:

> pretty obtuse and not something I'd recommend, generally.

[Myron Marston](https://github.com/rspec/rspec-expectations/issues/805#issuecomment-112239820)

> > Should we add one-liner Relish documentation about using block matchers?

> We should mention they're not supported.

[Jon Rowe](https://github.com/rspec/rspec-expectations/issues/805#issuecomment-112238533)

The discussion is [here](https://github.com/rubocop-hq/rspec-style-guide/issues/76).[^1]

In the spirit of broader community collaboration we've started this blog so that more people can see what we're working on, and participate in the discussions around introducing new guidelines and changing existing ones.[^2]

Feel free to leave your reaction (a thumbs up or down, heart, rocket, or even a confused face) on the issue.
Thumbs up will count towards recommending against this syntax, and thumbs down to leave out this guideline.

We encourage you to bring additional arguments for and against the syntax being discussed in the comments.

The expected outcome of the discussion can be either leaving the guideline out, or adding a guideline that would recommend avoiding this syntax, adding a [`rubocop-rspec`](https://github.com/rubocop-hq/rubocop-rspec) cop to enforce it, and doing our best to make it practically impossible in the next major release of RSpec itself.
But it's all up to you how it will end up!

We highly appreciate if you take the time to glance over [the other open questions](https://github.com/rubocop-hq/rspec-style-guide/issues) and leave your reaction.
Basing on your activity, we'll pick what to discuss next, or will go ahead and add guidelines that got a prevailing number of positive reactions.

[^1]: Please keep to the point of this practice, and feel free to open issues for other, even closely related topics.
[^2]: Discussions traditionally happen on the issue trackers of the style guides, we're announcing them on this blog to reach out to more people.
