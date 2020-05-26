---
layout: post
title:  "Reconsidering described_class"
date:   2019-08-04 16:25:00 +0300
categories: rspec
author: Phil Pirozhkov
---

Hey, everyone!

`described_class` is no doubt widely used in specs, there are 1376 usages in repositories listed in [`real-world-ruby-apps`](https://github.com/jeromedalbert/real-world-ruby-apps).

According to [`described_class` docs](https://relishapp.com/rspec/rspec-core/v/3-8/docs/metadata/described-class):

> If the first argument to [...] example group is a class, the class is exposed to each example via the described_class() method.

However, in fact it's slightly different:

> It's more like described_thing -- which is a class if you pass a class or whatever object you pass.
>
> -- <cite>[Myron Marston](https://github.com/rspec/rspec-core/issues/1610#issuecomment-46236668)</cite>

With one exception though, when a string is passed as the first argument.

> The reason for the string exception is that strings, when passed to describe or context, are taken to be an English description of the context, and not the object-being-described.
> Any other type of object is assumed to be the thing the user is describing.
>
> -- <cite>[Myron Marston](https://github.com/rspec/rspec-core/issues/1610#issuecomment-46236668)</cite>

One common mistake is passing [metadata](https://relishapp.com/rspec/rspec-core/v/3-8/docs/metadata/user-defined-metadata) without a proper docstring, e.g.:

```ruby
RSpec.describe SomeClass do
  describe controller: true do
    it { expect(described_class).to eq(SomeClass) }
  end
end
```

results in:

```
  1) SomeClass {:controller=>true} should eq SomeClass
     Failure/Error: it { expect(described_class).to eq(SomeClass) }

       expected: SomeClass
            got: {:controller=>true}
```

It may be unclear why the first argument is taken from the innermost example group, not outermost (`SomeClass`).
There was [a breaking change](https://github.com/rspec/rspec-core/pull/1361) introduced [in RSpec 3.0](https://github.com/rspec/rspec-core/blob/master/Changelog.md#300rc1--2014-05-18) to take the first argument of the innermost example group as `described_class`, not outermost.
Unfortunately, this was not reflected in the docs ([1](https://relishapp.com/rspec/rspec-core/v/3-8/docs/metadata/described-class), [2](https://relishapp.com/rspec/rspec-core/v/3-8/docs/subject/implicitly-defined-subject)), and it's still not (it's on [`master`](https://github.com/rspec/rspec-core/blob/master/features/metadata/described_class.feature) but [not on `3-8-maintenance`](https://github.com/rspec/rspec-core/blob/3-8-maintenance/features/metadata/described_class.feature)), even after [this pull request](https://github.com/rspec/rspec-core/pull/2629).

> I also argue against using classes there (with describe) preferring strings
>
> -- <cite>[Jon Rowe](https://github.com/rspec/rspec-core/issues/1610#issuecomment-46255261)</cite>

```ruby
# bad
RSpec.describe Article do

# good
RSpec.describe “Article” do
```

> I recommend sticking to `describe <string arg>` for nested example groups as it avoids confusion.
>
> -- <cite>[Myron Marston](https://github.com/rspec/rspec-core/issues/1610#issuecomment-46236668)</cite>

> I'd naively expect described_class to return "the class of the described thing" (i.e. Class when describing an actual class). Leaving open because this is definitely still an inconsistency.
>
> -- <cite>[Xavier Shay](https://github.com/rspec/rspec-core/issues/1610#issuecomment-272717207)</cite>

There was [an idea](https://github.com/rspec/rspec-core/issues/1610#issuecomment-46316040) to change `described_class`'s behaviour:

```ruby
describe [1,2,3] do
  it { described_class.should eq(Array) }
  it { subject.should be_a(Array) }
end
```

Unfortunately, this idea was two weeks late for RSpec 3.0 release.

> I like your idea, @betesh. I wish we had thought of it before 3.0 shipped.
>
> -- <cite>[Myron Marston](https://github.com/rspec/rspec-core/issues/1610#issuecomment-46324892)</cite>

With all this confusion in mind:

> I think in RSpec 4 we should just remove described_class, we kind of consider it an anti-pattern (because using it means the class must be loaded when the file is evaluated rather than when it's run) and we thus don't recommend it, and it causes this kind of ambiguity.
>
> -- <cite>[Jon Rowe](https://github.com/rspec/rspec-core/issues/1610#issuecomment-515507697)</cite>

### Bottom Line

Pros of `described_class`:
 - is widely used
 - reads nicely (when used properly)
 - reduces churn if the name of the class under test changes

Cons of `described_class`:
 - leads to confusion with nested example groups
 - leads to confusion when metadata is used without docstring
 - leads to confusion when something that is neither a String nor a Class is passed to example group as the first argument
 - might disappear in RSpec 4.0

### How to Deal with This

What do you think about introducing a guideline to discourage passing anything than a literal class and a string as the first argument to an example group?
React with a :rocket: on GitHub issue if you agree.

Or would you be radical and discourage the use of `described_class` altogether, and recommend using class names explicitly?
Place a :+1: on GitHub issue if you decide to be explicit about what is being tested.

Please react with :-1: on GitHub issue if you'd like to stick to `described_class` (at least until RSpec 4 is out).

Put your reactions and add your comments on [corresponding RSpec Style Guide issue on GitHub](https://github.com/rubocop-hq/rspec-style-guide/issues/104).

Highly appreciate if you add additional arguments pro and contra.
