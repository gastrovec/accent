---
title: Esoteric Annoyances
author: L3viathan
profile_picture: /images/l3viathan.png
about_stub: Recreational linguistics, distributional gastronomics, and applied galettalogy.
author_site: https://github.com/L3viathan
---

About six years ago (during my time at [Experimental
TES](http://experimental.gr)) I wrote an esoteric programming language called
`FABSIL`. It stood for "FABSIL Array-Based Stupid Interpreted Language" (you
can guess I loved recursive acronyms at the time) and the reference
implementation was written in [AutoIt3](https://www.autoitscript.com), a
Windows-only scripting language that was also my main language at the time
(after coming from VBScript, but that's a story for another time).

The design goal had been to make a language that is really annoying to read and
write, and has a degree of introspection that is dangerously high. I knew of
[Brainfuck](https://en.wikipedia.org/wiki/Brainfuck) (and derivates) at the
time, but not of any really different esoteric languages. The core idea of
FABSIL was that memory and code share a single location (I hadn't heard of
Turing machines yet, so I considered myself the inventor of the concept). The
[original interpreter code](http://old.l3vi.de/fabsil_interpreter.au3)
(including the 11 line documentation comment) is still online.

Yesterday, I decided to make a new version of the language, true to the
original, but better defined and with two or three changes. The language was
also renamed to OIL, _Overly Introspective Language_.

---

A OIL file consists of a number of lines. When a script is run, the source
file is read in line by line and each line is put into a tape that has as many
cells as the file has lines, but can be extended by the code (to a potentially
discontinuous tape). If a line can be casted to an integer, this is done,
otherwise it remains a string.

The tape has a head that starts at line/cell 0 (Yes, lines/cells are
zero-indexed). It reads the value of the current cell as an integer (again
converting to 0 if needed), and executes the command associated with the value.
There are 14 different commands, all described in the
[reference](https://github.com/L3viathan/OIL/blob/master/README.md):

{% highlight cpp %}
Hello World
4
0
{% endhighlight %}

It ignores the first line (because it is a string which is mapped to 0), then
prints (4) line 0.

---

The following example checks if two entered (positive) integers are identical.
It demonstrates many of the features of OIL. In the code, I use C++ style
comments, but they need to be removed before running the script, as OIL knows
no comments (you can just use strings).

{% highlight cpp %}
5 //Read the first number
Yes //into cell 0.
5 //Reads the second number
2 //into cell 2 (not 1, because we use its string value later)
10 //conditional jump:
18 //is zero
No //equal to cell 0 (the first number)?
9 //then jump to cell 9 (two lines ahead)
17 //otherwise to cell 17 (the first containing a 9)
10 //is the second number 0 too?
18
2
14 //then we jump two lines ahead
22 //otherwise we jump to the last line with a 4
4 //output the text
1 //from cell 1 ("Yes")
3 //and exit
9 //decrement both numbers
0 //(this also acts as the zero to compare to)
9
2
6 //and jump to line 4
4 //both the 4 for the jump before and for printing "No"
6
{% endhighlight %}

In this example, you can see what the sharing of code and data means: Not only
are unused cells reused and unchanging 0-value cells used as string storage,
but we also use a line both as the argument for the previous line, and as a
command itself when we jump to it directly.

## Coding in OIL

Writing scripts in OIL is pretty hard, because if you insert a single line
anywhere, all lines after that one shift their location by one. Furthermore, it
is easy to write difficult to understand code that modifies itself (e.g. reuses
unneeded command cells to store data; see
[examples](https://github.com/L3viathan/OIL/tree/master/examples), or code
sections that are executed once forwards, once backwards (and does completely
different things).

At this time, in addition to the scripts shown above, I have managed to write
one script per basic arithmetic operation (addition and substraction aren't too
bad, multiplication and division are slightly worse), and a script that checks
for whether the user input contains an @ character. I will try to write more
classic scripts as time goes by.

You can find the interpreter (requires Python 3) and the example scripts
[here](https://github.com/L3viathan/OIL/){:.capsule}. I would be interested
if anyone can shorten any of the scripts by one or more lines.
