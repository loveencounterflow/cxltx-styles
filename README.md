

- [CXLTX Styles](#cxltx-styles)
	- [Installation](#installation)
	- [Badges](#badges)
	- [CXLTX Style: Position Absolute](#cxltx-style-position-absolute)
		- [paOriginTo, paOriginToPaper, paOriginToText, and paOriginIs](#paoriginto-paorigintopaper-paorigintotext-and-paoriginis)
		- [paRight, paCenter, and paLeft](#paright-pacenter-and-paleft)
		- [paGauge](#pagauge)
		- [Absolute Positioning and Page Breaks](#absolute-positioning-and-page-breaks)
		- [paShow and paHide](#pashow-and-pahide)
	- [CXLTX Style: Convert To](#cxltx-style-convert-to)
	- [CXLTX Style: Equals](#cxltx-style-equals)
	- [CXLTX Style: Odd / Even](#cxltx-style-odd--even)

> **Table of Contents**  *generated with [DocToc](http://doctoc.herokuapp.com/)*


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
\usepackage{cxltx-style-equals}               % equality testing made easy
\usepackage{cxltx-style-oddeven}              % checking whether we're on an odd or an even page
\usepackage{cxltx-style-trm}                  % make a hundred colors glow (in terminal output)
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
## Badges

Each CXLTX Style has a particular 'badge', i.e. a prefix, prepended to each of the names defined in that
style. Badges are mostly two lowercase letters out of `[a-z]` that are reminiscent of the style's name.
All names of private members (which are not documented here; see the source) start with the sigil `@`,
followed by the badge, followed by the name proper. Further, all names are written out (no surprising abbr.
pls.) and use CamelCase (coz that dash is no-go in TeX, y'know).

> Badges are necessitated by the fact that all of the huge ecosystem that is TeX & LaTeX lives within one
> single, *enormous* namespace. There's not even so much as a stringent convention to minimize chances of
> naming collisions. As a result, name and package option clashes are a frequently observed nuisance that
> any user of LaTeX has to put up with.


<!-- =================================================================================================== -->
## CXLTX Style: Position Absolute

*CXLTX Position Absolute* (PA) is based on
[textpos](http://www.tex.ac.uk/ctan/macros/latex/contrib/textpos/textpos.pdf). **It is intended to make it
easy to position single lines of text on the page** (much easier than using `textpos` directly), using
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
means that **all dimensions must be given as lengths rather than pure numbers**. I feel this is an
advantage, as **(1)** pure numbers are only meaningful as 'abstract lengths' when used in geometry, but not as
concrete lengths in the physical world; and **(2)** simple arithmetics in command arguments are only possible
with lengths, not with numbers (`textpos` uses `calc`).

The badge of this style is `pa`. PA defines the following items:

<!-- ................................................................................................... -->
### paOriginTo, paOriginToPaper, paOriginToText, and paOriginIs

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

**Though this option is the slowest one (because it performs a floating point (using the venerable
[`fp`](http://ctan.org/pkg/fp) package) calculation on each single text placement), i chose to make it the
default one**, simply because there is good reason to think that if anyone uses CXLTX Position Absolute, the
straightforward and reasonable handling of absolute placement *in the context of a real document* might be
the one selling point.

> The inefficiencies, where deemed unbearable, could probably be easily ironed out by caching values. **Pull
> Request Are Awesome.** In case you consider to improve on the current state of affairs, please consider
> that, as it stands, `\paOriginToText` *should* be able to handle intermittent layout changes gracefully,
> although this is as yet untested.

> **Update** I can now confirm (doing test runs of [CND Tides](https://github.com/loveencounterflow
> **/coffeenode-tides/)) that `fp` is really kind of slow—so slow it dominates total processing time.
> Optimizations, therefore, should consider to **(1)** replace `fp` with something suitable, but faster,
> and / or **(2)** cache intermediate results (i think one can assume layouts do not normally change within
> a document, and when they do, it should be the responsibility of the user to tell PA of the fact).

**Important**: in case you want to use the default `\paOriginToText` setting, please bear in mind that since
this option uses [`changepage`](http://ctan.org/pkg/changepage), **LaTeX has to run at least twice** before
you get correct results. You're already used to this, right?

Lastly, there is `\paOriginIs` which is internally used to keep state; it will expand to one of `text` (the
default), `page`, or some `custom (20mm,25mm)` (for the first example, above).

With these considerations out of the way, let's have a look at how to actually put stuff onto the page.

<!-- ................................................................................................... -->
### paRight, paCenter, and paLeft

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

The coordinate system origin and the direction of ascending values is indicated by the arrow; observe how
each text box comes with one or two 'struts' (the blue bars) that regulate box heights.

You can also see that `\paRight` produces a text (the bottommost line) that is set flush right against the
`x = 40mm` vertical, while `\paLeft` text (the topmost one) is set flush left against the same; `paCenter`
centers the text horizontally so the 40mm mark lands smack in the middle. No surprises here, which is really
the purpose of this package! (The boxes and struts are only shown for demonstration; they're of course
absent from the output unless you state to `\paShow` them.)


<!-- ................................................................................................... -->
### paGauge

CXLTX Position Absolute promises to deliver text positioned with respect to the *baseline* of the text. Now
the baseline is a fickle thing that is different from font to font. There are two ways to get the baseline
right: either ask PA to do it for you, or else do it yourself. The easy way first: use `\paGauge{...}` like

````latex
\paGauge{abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!?§\$}
````

(using a suitable selection of characters) or simply

````latex
\paGaugeSample
````

(which will use the sample shown above) in your document to have PA determine the (internal) dimensions
`\@paStrutHeight`, `\@paStrutDepth`, and the ratio

````latex
\@paHeightDepthRatio = \@paStrutHeight / ( \@paStrutHeight + \@paStrutDepth )
````

> Needless to say that you should make sure that both your character sample is representative and that the
> font that `\paGauge` implicitly uses is the same that will appear in your positioned material. `\paGauge`
> does not produce any output.

**Observe that while `\usepackage{cxltx-style-position-absolute}` will run `paGaugeSample` implicitly,
dimensions will at best be approximate in case you change fonts later in your document setup.**

> You may profit from using CXLTX Smashbox in case you want to mix characters from fonts with protuding
> heights and depths in your PA textboxes.

Now for the hard way: if you find you want to do it yourself (maybe because you're mixing typefaces on the
positioned line), you're free to add something like

````latex
\paSetStrut{4mm}{3mm}
````

which will both set the strut's height (the first argument) and depth (the second argument) and calculate
the ratio as detailed above to find the baseline. This feature may prove useful for positioning characters
from symbol fonts. For people who haven't heard of height and depth in type, here's a picture to clarify
those concepts:

![](https://raw.githubusercontent.com/loveencounterflow/cxltx-styles/master/art/height-and-depth.png)

You can see that the 'height' is the length that a given text extends *above* the baseline (i.e. the one in
the middle) and the 'depth' is the length that it it extends *below* the same. Now `textpos` wants to know
the ratio of those with respect to the *total* height (that is, `height + depth`) to position its point of
reference, reckoned from the top—which is, of course, `ratio = height / ( height + depth )`.

**Note** To obtain a correct positioning of text boxes, only the *relative* height and depth of the strut
are important. Since material typeset with PA does not 'take space' on the page (i.e. it can overlap with
other material, just as a HTML `<div/>` with CSS style `position: absolute` would), the absolute height of
the struts (as long as it equal to or greater than the height and depth of any material within the box) is
of little consequence.—Which brings us to the next point.

<!-- ................................................................................................... -->
### Absolute Positioning and Page Breaks

As said in the preceding paragraph, material typeset with PA does not take space on the page—it may overlap
without triggering any line justification or word hyphenation action. Since this is so, it may also be the
case that while *you* know you did put printing stuff onto the page, TeX (rather, its output routines) may
be blithely unaware of that—which matters as soon as you have a page full of absolutely positioned material
and nothing else, and you want to use `\newpage` to advance to the next page.

Mind you, LaTeX in all its incredibly convoluted glory—hardly adequately described by adjectives
such as 'baroque' or even 'byzantine'—*does care for you*! It keeps you from comitting grave errors such as
emitting blank sheets of paper! I mean, you'd totally loose your street cred if that should ever happen to
you, in the Data Center, with everyone and the staff looking at you, Waster of Computing Cycles! And Paper!!

I know i know. Anyhows, remember this: always use

````latex
\null\newpage
````

when in doubt whether there was non-PA material on that page, but *not* in doubt that a new page is what you
damn want.

> There's probably a gazillion other methods documented how to get that new page but i like the non-chalant
> understatement of this particular incantation. I mean, null is nought is what i'm saying, right?—zilch,
> nuthin! We *can* position printing stuff and TeX will think there's nil to print!, but when we
> *explicitly* tell TeX to send *nada* to output, *then* it will act like there *was* something to print...!
> Now i must stop at this or i'll get all worked up again.


<!-- ................................................................................................... -->
### paShow and paHide

You can use

````latex
\paShow
\paHide
````

respectively to show or hide the boxes and the struts that are used by PA. In case you want to muck with
their default appearances (boxes with slender, elegant outlines, whose creamy orange tint contrasts
gleefully with the sober marina blue of the rather sturdy struts that adorn each typeset line), refer to the
[source](https://github.com/loveencounterflow/cxltx-styles/blob/master/cxltx-styles-position-absolute.sty).
Use `\paShow` and `\usepackage[top-left]{pagegrid}` to debug your PA-powered document today! Experience the
Beauty of Simplicity! Never look back to those inferior and clumsy methods of days gone by!

Enough of that.


<!-- =================================================================================================== -->
## CXLTX Style: Convert To

Simplistic unit conversion routine; see http://tex.stackexchange.com/a/37317/28067. Badge is `ct`; a
single command is defined:

````latex
\ctConvertTo{mm}{\the\@paStrutHeight}mm
````


<!-- =================================================================================================== -->
## CXLTX Style: Equals

Routines to do equality checks. The badge is `eq`. Currently, a single command is defined:

````latex
\eqTextEquals{$valueOne}{$valueTwo}{if branch}{else branch}
````

`\eqTextEquals` checks whether `$valueOne` and `$valueTwo` are equal strings or expand to equal strings; as
such, it combines the functionalities of `\ifdefstrequal`, `\ifdefstring`, and `\ifstrequal` from the
`etoolbox` package. If we go and define the following macros:

````latex
\newcommand{\cmdA}{sometext}
\newcommand{\cmdB}{sometext}
\newcommand{\cmdC}{othertext}
\def\defA{sometext}
\def\defB{sometext}
\def\defC{othertext}
````

then all of the following comparisons work and will print out `OK`:

````latex
\eqTextEquals{sometext}{sometext}{OK}{wrong}
\eqTextEquals{sometext}{othertext}{wrong}{OK}

\eqTextEquals{\cmdA}{sometext}{OK}{wrong}
\eqTextEquals{\cmdA}{othertext}{wrong}{OK}

\eqTextEquals{\defA}{sometext}{OK}{wrong}
\eqTextEquals{\defA}{othertext}{wrong}{OK}

\eqTextEquals{\cmdA}{\cmdB}{OK}{wrong}
\eqTextEquals{\cmdA}{\cmdC}{wrong}{OK}

\eqTextEquals{\cmdA}{\defB}{OK}{wrong}
\eqTextEquals{\cmdA}{\defC}{wrong}{OK}

\eqTextEquals{\defA}{\cmdB}{OK}{wrong}
\eqTextEquals{\defA}{\cmdC}{wrong}{OK}

\eqTextEquals{\defA}{\defB}{OK}{wrong}
\eqTextEquals{\defA}{\defC}{wrong}{OK}
````

For more details, have a look at [The LaTeX String Equality Comparison
Primer](https://github.com/loveencounterflow/cxltx-styles/raw/master/style-demos/string-comparison.pdf)

Sometimes you need only take some action when string equality tests positively or negatively; in these
cases, you can either call `eqTextEquals` with an empty true or false branch:

````latex
\eqTextEquals{\a}{\b}{true branch}{}
\eqTextEquals{\a}{\b}{}{false branch}
````

or use the equivalent commands `\eqIfTextEqualsThen` or `\eqUnlessTextEqualsThen`:


````latex
\eqIfTextEqualsThen{\a}{\b}{true branch}
\eqUnlessTextEqualsThen{\a}{\b}{false branch}
````


<!-- =================================================================================================== -->
## CXLTX Style: OddEven

`cxltx-style-oddeven` is a very thin shim for the `\ifoddpage` command of the
[`changepage`](http://www.ctan.org/pkg/changepage) package. It exists to abbreviate the
incantation

````latex
\strictpagecheck\checkoddpage\ifoddpage
  odd branch
\else
  even branch
\fi%
````

to one of these forms:

````latex
\oeIfOddPage}{odd branch}{even branch}
\oeIfEvenPage}{even branch}{odd branch}
````

The advantage of `cxltx-style-oddeven` is that **(1)** it always uses the `strict` option so you don't have
to remember that one, and **(2)** it uses the more standards-conformant double-brace-pair syntax instead
of the somewhat unusual `\if ... \else ... \fi` syntax.


<!-- =================================================================================================== -->
## CXLTX Style: TRM

`cxltx-style-trm` adds color to your console `\typeout{}`s. Thanks to an [ingenious snippet of
code](http://tex.stackexchange.com/a/168477/28067) by one Mr. Oberdiek of LaTeX fame, you can now adorn your
diagnostic messages with colors—which sounds like a toy until you get to see the visual improvement that
colorized terminal outputs afford. It's no coincidence that many of the greatest and latest tools in a new
wave of programming—think `npm`, think `homebrew`, think `git`—embrace both the console and colored output.

**Prerequisites** Run `\trmTest` to get an overview of the colors and effects available with TRM. Chances
are that on first try, you'll get to see something like this:

````bash
^^[[38;05;61m████^^[[0m trmSolViolet
^^[[38;05;33m████^^[[0m trmSolBlue
^^[[38;05;37m████^^[[0m trmSolCyan
^^[[38;05;64m████^^[[0m trmSolGreen
````

This is because traditionally, TeX is a bit tight on *everything* that is not strictly a US-ASCII printable
character. You'll see a helpful message below the (un)colorized bars that says

````
In case you cannot see colors above:
====================================

— make sure your terminal is color-capable
 (see http://en.wikipedia.org/wiki/ANSI_escape_code)

— make sure you're running XeLaTeX with the `-8bit` switch
 (other TeXs may or may not work; some TeXs would appear to require
 a `-translate-file=natural` or `--tcx=natural` switch)

(now terminating so you can read this output.)
````


\newcommand{\trmBlink}[1]{\trmCsi5m#1\trmReset}
\newcommand{\trmBold}[1]{\trmCsi1m#1\trmReset}
\newcommand{\trmReverse}[1]{\trmCsi7m#1\trmReset}
\newcommand{\trmUnderline}[1]{\trmCsi4m#1\trmReset}

% ----------------------------------------------------------------------------------------------------------
% Effects Off
% ..........................................................................................................
\newcommand{\trmNoBlink}[1]{\trmCsi25m#1\trmReset}
\newcommand{\trmNoBold}[1]{\trmCsi22m#1\trmReset}
\newcommand{\trmNoReverse}[1]{\trmCsi27m#1\trmReset}
\newcommand{\trmNoUnderline}[1]{\trmCsi24m#1\trmReset}
\newcommand{\trmReset}{\trmCsi0m}

% ----------------------------------------------------------------------------------------------------------
% Colors
% ..........................................................................................................
\newcommand{\trmBlack}[1]{\trmCsi38;05;16m#1\trmReset}
\newcommand{\trmBlue}[1]{\trmCsi38;05;27m#1\trmReset}
\newcommand{\trmGreen}[1]{\trmCsi38;05;34m#1\trmReset}
\newcommand{\trmCyan}[1]{\trmCsi38;05;51m#1\trmReset}
\newcommand{\trmSepia}[1]{\trmCsi38;05;52m#1\trmReset}
\newcommand{\trmIndigo}[1]{\trmCsi38;05;54m#1\trmReset}
\newcommand{\trmSteel}[1]{\trmCsi38;05;67m#1\trmReset}
\newcommand{\trmBrown}[1]{\trmCsi38;05;94m#1\trmReset}
\newcommand{\trmOlive}[1]{\trmCsi38;05;100m#1\trmReset}
\newcommand{\trmLime}[1]{\trmCsi38;05;118m#1\trmReset}
\newcommand{\trmRed}[1]{\trmCsi38;05;124m#1\trmReset}
\newcommand{\trmCrimson}[1]{\trmCsi38;05;161m#1\trmReset}
\newcommand{\trmPlum}[1]{\trmCsi38;05;176m#1\trmReset}
\newcommand{\trmPink}[1]{\trmCsi38;05;199m#1\trmReset}
\newcommand{\trmOrange}[1]{\trmCsi38;05;208m#1\trmReset}
\newcommand{\trmGold}[1]{\trmCsi38;05;214m#1\trmReset}
\newcommand{\trmTan}[1]{\trmCsi38;05;215m#1\trmReset}
\newcommand{\trmYellow}[1]{\trmCsi38;05;226m#1\trmReset}
\newcommand{\trmGrey}[1]{\trmCsi38;05;240m#1\trmReset}
\newcommand{\trmDarkgrey}[1]{\trmCsi38;05;234m#1\trmReset}
\newcommand{\trmWhite}[1]{\trmCsi38;05;255m#1\trmReset}

% ..........................................................................................................
% experimental:
% colors as defined by http://ethanschoonover.com/solarized
\newcommand{\trmSolBaseOThree}[1]{\trmCsi38;05;234m#1\trmReset}
\newcommand{\trmSolBaseOTwo}[1]{\trmCsi38;05;235m#1\trmReset}
\newcommand{\trmSolBaseOOne}[1]{\trmCsi38;05;240m#1\trmReset}
\newcommand{\trmSolBaseOO}[1]{\trmCsi38;05;241m#1\trmReset}
\newcommand{\trmSolBaseO}[1]{\trmCsi38;05;244m#1\trmReset}
\newcommand{\trmSolBaseOne}[1]{\trmCsi38;05;245m#1\trmReset}
\newcommand{\trmSolBaseTwo}[1]{\trmCsi38;05;254m#1\trmReset}
\newcommand{\trmSolBaseThree}[1]{\trmCsi38;05;230m#1\trmReset}
\newcommand{\trmSolYellow}[1]{\trmCsi38;05;136m#1\trmReset}
\newcommand{\trmSolOrange}[1]{\trmCsi38;05;166m#1\trmReset}
\newcommand{\trmSolRed}[1]{\trmCsi38;05;124m#1\trmReset}
\newcommand{\trmSolMagenta}[1]{\trmCsi38;05;125m#1\trmReset}
\newcommand{\trmSolViolet}[1]{\trmCsi38;05;61m#1\trmReset}
\newcommand{\trmSolBlue}[1]{\trmCsi38;05;33m#1\trmReset}
\newcommand{\trmSolCyan}[1]{\trmCsi38;05;37m#1\trmReset}
\newcommand{\trmSolGreen}[1]{\trmCsi38;05;64m#1\trmReset}


% ==========================================================================================================
% TEST COMMAND
% ----------------------------------------------------------------------------------------------------------
\newcommand{\trmTest}{
  \typeout{}%
  \typeout{\trmBlink{XXXX}    trmBlink}%
  \typeout{\trmBold{XXXX}   trmBold}%
  \typeout{\trmReverse{XXXX}    trmReverse}%
  \typeout{\trmUnderline{XXXX}    trmUnderline}%
  \typeout{\trmBlack{████}    trmBlack}%
  \typeout{\trmBlue{████}   trmBlue}%
  \typeout{\trmGreen{████}    trmGreen}%
  \typeout{\trmCyan{████}   trmCyan}%
  \typeout{\trmSepia{████}    trmSepia}%
  \typeout{\trmIndigo{████}   trmIndigo}%
  \typeout{\trmSteel{████}    trmSteel}%
  \typeout{\trmBrown{████}    trmBrown}%
  \typeout{\trmOlive{████}    trmOlive}%
  \typeout{\trmLime{████}   trmLime}%
  \typeout{\trmRed{████}    trmRed}%
  \typeout{\trmCrimson{████}    trmCrimson}%
  \typeout{\trmPlum{████}   trmPlum}%
  \typeout{\trmPink{████}   trmPink}%
  \typeout{\trmOrange{████}   trmOrange}%
  \typeout{\trmGold{████}   trmGold}%
  \typeout{\trmTan{████}    trmTan}%
  \typeout{\trmYellow{████}   trmYellow}%
  \typeout{\trmGrey{████}   trmGrey}%
  \typeout{\trmDarkgrey{████}   trmDarkgrey}%
  \typeout{\trmWhite{████}    trmWhite}%
  \typeout{\trmSolBaseOThree{████}    trmSolBaseOThree}%
  \typeout{\trmSolBaseOTwo{████}    trmSolBaseOTwo}%
  \typeout{\trmSolBaseOOne{████}    trmSolBaseOOne}%
  \typeout{\trmSolBaseOO{████}    trmSolBaseOO}%
  \typeout{\trmSolBaseO{████}   trmSolBaseO}%
  \typeout{\trmSolBaseOne{████}   trmSolBaseOne}%
  \typeout{\trmSolBaseTwo{████}   trmSolBaseTwo}%
  \typeout{\trmSolBaseThree{████}   trmSolBaseThree}%
  \typeout{\trmSolYellow{████}    trmSolYellow}%
  \typeout{\trmSolOrange{████}    trmSolOrange}%
  \typeout{\trmSolRed{████}   trmSolRed}%
  \typeout{\trmSolMagenta{████}   trmSolMagenta}%
  \typeout{\trmSolViolet{████}    trmSolViolet}%
  \typeout{\trmSolBlue{████}    trmSolBlue}%
  \typeout{\trmSolCyan{████}    trmSolCyan}%
  \typeout{\trmSolGreen{████}   trmSolGreen}%
  \typeout{}%
  \typeout{In case you cannot see colors above:}%
  \typeout{====================================}%
  \typeout{}%
  \typeout{— make sure your terminal is color-capable}%
  \typeout{  (see http://en.wikipedia.org/wiki/ANSI_escape_code)}%
  \typeout{}%
  \typeout{— make sure you're running XeLaTeX with the `-8bit` switch}%
  \typeout{  (other TeXs may or may not work; some TeXs would appear to require}%
  \typeout{  a `-translate-file=natural` or `--tcx=natural` switch)}%
  \typeout{}%
  \typeout{(now terminating so you can read this output.)}%
  OUTPUT STOPPED BECAUSE OF trmTest
  \stop%
  }%
