---
title: Abusing function annotations to add checks
author: L3viathan
profile_picture: /images/l3viathan.png
about_stub: Recreational linguistics, distributional gastronomics, and applied galettalogy.
author_site: https://github.com/L3viathan
---

In my ongoing quest to torture the Python language, I came across an interesting
project yesterday:
[Zach Mitchell](https://github.com/zmitchell) describes [in a blog
post](https://tinkering.xyz/abusing-type-annotations/) how he abused type
annotations to build what he calls "macros" (inspired by Rust): Annotating class
members to restrict them to certain values. He
[uses](https://github.com/zmitchell/annotation-abuse) the "new" type annotation
feature of Python, with strings:

{% highlight python %}
@inrange
class MyClass:
    var: "0 < var < 10"
{% endhighlight %}

This defines a class with a property called `var` that is only allowed to be set
to a value between 0 and 10.

After having presented this idea at a lightning talk, during discussion he got
another idea: Using lambdas instead of strings:

{% highlight python %}
class MyClass:
    foo: lambda x: 0 < x < 10
{% endhighlight %}

As far as I know, this second part remains unimplemented.

----

When I saw this, I had an thought: Can we do what he proposed... but *drop* the
lambda? At first thought, that seems impossible, or rather impractical:

{% highlight python %}
class MyClass:
    foo: 0 < foo < 10
{% endhighlight %}

While the above is _syntactically valid_, it will crash with a `NameError`,
unless one first defines `foo` somewhere else, the reason being that the
annotation has to be evaluated before it is attached to the variable.

My first thought was to use some special name that takes the role of any
variable, e.g.:

{% highlight python %}
class MyClass:
    foo: 0 < _ < 10
{% endhighlight %}

While this would work, it seemed needlessly restrictive. Then I had another
idea: PEP 563 ("Postponed Evaluation of Annotations"). I know about it, because
it breaks several hacky things I have written in the past (namely
[Aceto](https://github.com/aceto/aceto) and
[OIL](https://github.com/L3viathan/OIL)). Essentially, one can use a future
import (`annotations`) in order to stop the evaluation of function annotations
(all of them will become strings instead). Instead, they are attached in string
form to the `__annotations__` attribute of a class or function. Importantly
however, they still have to be valid Python syntax, and are parsed normally.
It is only during evaluation that they are converted to strings.

The only missing piece to actually do something with it was a way to get the
code of the class we'll decorate: Unlike functions, classes have no `__code__`
attribute.

Luckily, there's a built-in module that's always there for you when the others
give up: [`inspect`](https://docs.python.org/3/library/inspect.html).

And finally, it worked:

{% highlight python %}
from __future__ import annotations

from typeanno import restrict

@restrict
class Test:
    x: 0 < x < 10
    def __init__(self, x):
        self.x = x

if __name__ == '__main__':
    t = Test(7)
    print(t.x, "is 7")
    t.x = 3
    print(t.x, "is 3")
    try:
        t.x = 12
        print("This won't be printed")
    except ValueError:
        print("Couldn't set property")
{% endhighlight %}

If you want to see exactly how it works, then sit down at [**go
here**](https://gist.github.com/L3viathan/0a70241d8ab4ca20c8a87c99d7a9875b).
