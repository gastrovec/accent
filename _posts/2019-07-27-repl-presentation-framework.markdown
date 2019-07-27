---
title: Python REPL as a presentation framework
author: L3viathan
profile_picture: /images/l3viathan.png
about_stub: Recreational linguistics, distributional gastronomics, and applied galettalogy.
author_site: https://github.com/L3viathan
---

I needed a presentation software for a talk at a Python meetup (in Karlsruhe,
but I've since held the talk at the [Python meetup
Stuttgart](http://meetup.com/python-stuttgart/), too). I wanted something
simple with which I could show code easily, but ideally there would be some
element of interactivity, too (what if I could demonstrate the code I show
within the presentation itself?).

One solution would of course be to fake it: I could just pretend something is
running and show the output I would see, maybe after some delay. This is
something even PowerPoint can do. But this is no fun, I wanted real Python
execution in the background. Next idea: Build my own script that can somehow
take Python code and `eval`/`exec` it inbetween showing slides. Maybe something
browser-based? Or I could make a fake Python REPL and interpret some special
strings as commands for showing "slides" in a terminal.

Then I saw a talk by the [mad scientist of the Python
universe](http://www.dabeaz.com) where he appeared to kind of do what I wanted,
with what he called a "modded Python interpreter". That gave me an idea: What
if I didn't have to write a fake REPL, but could use the real one?

Objects in Python can determine how their representation looks like, so I could
define a custom object that remembers the slide number and prints different
text each time it is `repr()`d. If I want to have a "next" and "previous"
operation, I could have two special objects that share the state of which slide
I'm currently on.

`__repr__` methods with side-effects was the first idea, but then I found
another, easier, hackier solution: `sys.displayhook`. It's a function that gets
called by the Python REPL whenever an object is being printed interactively,
its job is this printing. If I changed that, I wouldn't even need special
objects (and in the final version I don't; the two variables `n` and `p` are
just assigned to the strings `"n"` and `"p"` and my custom displayhook does the
rest).

Next challenge: I wanted to do what's known as a "prover" in the world of
magicians: Some way of (falsely) convincing the audience that there is no
trickery going on. Ideally, I would want to start a Python interpreter with the
`python3` command and have the presentation framework function without having
to import things or running any extra code. While I could have used a shell
alias or something like that to do this, that would have meant I would have had
to recreate the Python REPL header, since `python -i some_module.py` doesn't
print it. Instead, I went for a different tool: Environment variables. Python
looks at a few at startup, one of them being `PYTHONSTARTUP`, which I just had
to set to my script that replaces the displayhook and I'm done. I can do that
before the presentation or even put it in my `.zshrc`, so nobody needs to know.

I had most of the program set up, but I was still missing the actual
presentation (who hasn't written a presentation framework in order to
procrastinate preparing a talk?). I wanted it to be easily editable, and I
wanted the ability to show code _and have it executed in the background_.

As usual, I chose Markdown, as it's easy to edit, expressive enough to make
simple presentations, and text-based. A combination of `mistune` and `colorful`
allowed me to write a custom Markdown renderer that outputs ANSI escape
sequences to do color and font weight. Using `pygments` I could
syntax-highlight code. Markdown supports specifying a language in the beginning
of a fenced code block, so I invented some fake languages like `exec`, and
`exechidden` that would not only highlight like Python, but actually execute
the code and allow the resulting names to be used:

```python
def block_code(self, text, lang=None):
    if lang and lang.startswith("exec"):
        bindings = {}
        exec(text, bindings)
        for key, value in bindings.items():
            if key == "__builtins__":
                continue
            setattr(builtins, key, value)
```

The only Markdown feature I found hard to do in a terminal were headings. Yes,
I could have just printed them as in Markdown, with an octothorpe in front, or
made them all-caps. I wanted different font sizes.

Enter `Pillow`, your friendly neighbourhood PIL clone. Whenever I encounter a
heading, I draw text on a `PIL.Image` that has the size of my terminal window.
I then convert the 256-color-grayscale image to an 8-color-grayscale image and
then use the shaded block unicode characters (`░▒▓█`) to print the result.

Finally, I wanted multi-language support. I prepared the talk in English, but I
wanted to give the audience an option to switch to German instead. To do this
in an interesting way, I reused the `sys.displayhook` trick from earlier and
introduced some additional piece of state: the _mode_. Usually, the mode is set
to `"normal"`, but when I type `l`, which contains `"l"`, the shortcut for
language selection, it switches to a special mode that will cause the display
hook to intercept the next input and set a configuration value based on a
selection from a menu:

    >>> l
    [0] german
    [1] english
    [2] romanian
    >>> 2
    Switching language to romanian
    >>>

You can find the whole thing at <https://github.com/L3viathan/triotalk>,
including the actual talk.

---

But there's one more thing. If someone _had_ figured this whole thing out
during the talk and called me out on it, I wanted to have an extra ace up my
sleeve.

I found the answer in `sys.excepthook`, which is _similar_ to
`sys.displayhook`, but is responsible for printing exceptions and their
stacktraces. This allowed me to do this (`17` is _not really_ the return value,
it is merely printed):

    >>> 4/0
    17
