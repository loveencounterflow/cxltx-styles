

# CXLTX Styles

An incipient and hopefully sensible collection of basic X<sub>E</sub>L<sub>A</sub>T<sub>E</sub>X style
sheets.

**Pull Requests Are Awesome.**

````latex
\usepackage{cxltx-style-base}                 % you always want this one
\usepackage{cxltx-style-cjk}                  % Chinese / Japanese / Korean
\usepackage{cxltx-style-accentbox}            % boxed accents as per http://www.eutypon.gr/eutypon/pdf/e2000-05/e05-a04.pdf
\usepackage{cxltx-style-smashbox}             % boxed contents to preserve lineheights
\usepackage{cxltx-style-multimulti}           % multiple columns and rows on pages and in tables
\usepackage{cxltx-style-position-absolute}    % absolute textpositioning
\usepackage{cxltx-style-convert-to}           % simplistic unit conversion
````


<!-- ################################################################################################### -->
## Installation

Simply clone

````bash
git clone https://github.com/loveencounterflow/cxltx-styles.git
````

or [download an archive](https://github.com/loveencounterflow/cxltx-styles/archive/master.zip) to get
the code.

To enable TeX to resolve `\usepackage{cxltx-styles-*}` commands, put a symlink to your
CXLTX Styles directory into a directory that is on LaTeX's search path. On OSX with TeX Live, that can
be achieved by doing

````bash
cd ~/Library/texmf/tex/latex
ln -s route/to/cxltx-styles cxltx-styles
````

In a [more general fashion](http://tex.stackexchange.com/a/1138/28067), you may want to

````bash
kpsewhich -var-value=TEXMFHOME
````

then take that route (e.g. `/Users/$USER/Library/texmf`) and append `/tex/latex` (which gets you
`/Users/$USER/Library/texmf/tex/latex`) to obtain a suitable location. It may be necessary to first create
that location using `mkdir -p ~/texmf/tex/latex`. Confusing it certainly is.


<!-- =================================================================================================== -->
### Badges

Each CXLTX Style has a particular 'badge', i.e. a prefix, prepended to each of the names defined in that
style. Badges are mostly two lowercase letters out of `[a-z]` that are reminiscent of the style's name.
All names of private members (which are not documented here; see the source) start with the sigil `@`,
followed by the badge, followed by the name proper. Further, all names are written out (no surprising abbr.
pls.) and use CamelCase (coz that dash is no-go in TeX, y'know).

> Badges are necessitated by the fact that all of the huge ecosystem that is TeX & LaTeX has only one
> single, *huge* namespace.


<!-- =================================================================================================== -->
### CXLTX Style: Position Absolute

*CXLTX Position Absolute* (PA) is based on
[textpos](http://www.tex.ac.uk/ctan/macros/latex/contrib/textpos/textpos.pdf). It is intended to make it
easy (much easier than using `textpos` directly) to position single lines of text on the page, using
absolute coordinates that take the left edge, the center point, or the right edge of the baseline of the
text and the edges of the paper, the text area, or custom coordinates as reference points.

PA loads `textpos` as

````latex
\usepackage[absolute,overlay]{textpos}
````

so all material will be absolutely positioned (rather than relative to the current point of insertion), and
put on top of everything (?) else on the page.

> The latter option has been chosen to ensure `pagegrid`s will
> not cover material typeset with Position Absolute.

PA uses the starred form of the `textblock` environment, `\begin{textblock*}... \end{textblock*}`, which
means that **all dimensions must be given as lengths rather than as pure numbers**. I feel this is an
advantage, as (1) pure numbers are only meaningful as 'abstract lengths' when used in geometry, but not as
concrete lengths in the physical world; and (2) simple arithmetics in command arguments are only possible
with lengths, not with numbers (`textpos` uses `calc`).

The badge of this style is `pa`. PA defines the following items:

<!-- ................................................................................................... -->
#### paOriginTo, paOriginToPaper, paOriginToText, and paOriginIs

These commands are all concerned with the origin (the reference point) of the measurements you will want to
give when using `\paRight`, `\paCenter`, or `\paLeft` (for which see below).

The most basic command is `\paOriginTo`, which you can use to set the origin absolutely; the measurements
given here are always in turn interpreted as being relative to the edge of the paper, so

````latex
\paOriginTo{20mm}{25mm}
````

will set the reference point to 20mm to the right from the left edge and 25mm down from the top edge of the
paper. If your coordinates refer to the paper without any offset, you can, instead of `\paOriginTo{0mm}{0mm}`,
simply say

````latex
\paOriginToPaper
````

However, i anticipate that most of the time people will want to place their text lines relative to the
current text extent, *not* the paper. This can be done by placing

````latex
\paOriginToText
````

somewhere near the beginning of the document (or just leave it with the default settings).

**Though this option is the slowest one (because it performs a floating point calculation on each single
text placement), i chose to make it the default one**, simply because there is good reason to think that if
anyone uses CXLTX Position Absolute, the straightforward and reasonable handling of absolute placement *in
the context of a real document* might be the one selling point.

> The inefficiencies, where deemed unbearable, could probably be easily ironed out by caching values. **Pull
> Request Are Awesome.** In case you consider to improve on the current state of affairs, please consider
> that, as it stands, `\paOriginToText` *should* be able to handle intermittent layout changes gracefully,
> although this is as yet untested.

**Important**: in case you want to use the default `\paOriginToText` setting, please bear in mind that since
this option uses [`changepage`](http://ctan.org/pkg/changepage), **LaTeX has to run at least twice** before
you get correct results. You're already used to this, right?

Lastly, there is `\paOriginIs` which is internally used to keep state; it will expand to one of `text` (the
default), `page`, or some `custom (20mm,25mm)` (for the first example, above).

With these considerations out of the way, let's have a look at how to actually put stuff onto the page.

<!-- ................................................................................................... -->
#### paRight, paCenter, and paLeft

These are the basic commands to place printing material onto absolute positions; unlike their counterpart
in `textpos` (the `textblock` environment), each takes three regular arguments, namely, the `x` position,
the `y` position (growing from the top to the bottom), and the content of the box:

````latex
\pa〚Left|Center|Right〛{$x}{$y}{$text}
````

This code:

````latex
\paLeft{40mm}{10mm}{this text starts at 40mm}
\paCenter{40mm}{15mm}{centered centered}
\paRight{40mm}{20mm}{this text ends at 40mm}
````

produces (with `\usepackage[top-left]{pagegrid}` and `\paShow` in the preamble):

![](https://raw.githubusercontent.com/loveencounterflow/cxltx-styles/master/art/Screen%20Shot%202014-03-26%20at%2001.47.44.png)

The origin and direction of ascending values is indicated by the arrow; observe how each text box comes
with one or two 'struts' (the blue bars) that regulate the box heights. You can see how `\paRight` produces
a flush right text against the `x = 40mm` vertical, while `\paLeft` is flush left and `paCenter` centers
the text horizontally so the 40mm mark lands smack in the middle. No surprises here, which is really the
purpose of this package!


<!-- ................................................................................................... -->
#### paGauge

Use `\paGauge{...}` like

````latex
\paGauge{abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!?§\$}
````

or simply

````latex
\paGaugeSample
````

(which will use the sample shown above) in a suitable place of your document with a suitable sample of
characters to have PA determine the dimensions `\paStrutHeight`, `\paStrutDepth`, and the ratio

````latex
\paHeightDepthRatio = \paStrutHeight / ( \paStrutHeight + \paStrutDepth )
````

PA uses these figures to determine where the baseline of the specific font lies.

> Needless to say that
> you should make sure that both your character sample is representative and that the font that `\paGauge`
> implicitly uses is the same that will appear in your positioned material. `\paGauge` does not produce any
> output.

Observe that while loading this style will run `paGaugeSample` as shown above, dimensions will at best be
approximate in case you change fonts later in your document setup.

You may profit from using CXLTX Smashbox in case you want to mix characters from fonts with protuding
heights and depths in your PA textboxes.

<!-- ................................................................................................... -->
#### paShow and paHide

You can use

````latex
\paShow
\paHide
````

respectively to show or hide the boxes and the struts that are used by PA and `textpos` to produce the
output. In case you want to muck with their default appearances, refer to the
[source](https://github.com/loveencounterflow/cxltx-styles/blob/master/cxltx-styles-position-absolute.sty).



<!-- =================================================================================================== -->
### CXLTX Style: Convert To

Simplistic unit conversion routine; see http://tex.stackexchange.com/a/37317/28067. Badge is `ct`; a
single command is defined:

````latex
\ctConvertTo{mm}{\the\paStrutHeight}mm
````






