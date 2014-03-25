

# cxltx-styles

A (hopefully) sensible collection of basic X<sub>E</sub>L<sub>A</sub>T<sub>E</sub>X style sheets.

````latex
\usepackage{cxltx-styles-base}                % you always want this one
\usepackage{cxltx-styles-cjk}                 % Chinese / Japanese / Korean
\usepackage{cxltx-styles-accentbox}           % boxed accents as per http://www.eutypon.gr/eutypon/pdf/e2000-05/e05-a04.pdf
\usepackage{cxltx-styles-smashbox}            % boxed contents to preserve lineheights
\usepackage{cxltx-styles-multimulti}          % multiple columns and rows on pages and in tables
\usepackage{cxltx-styles-position-absolute}   % absolute textpositioning
````


## Installation

To make it easier for TeX to resolve `\usepackage{cxltx-styles-*}` commands, put a symlink to your
CXLTX Styles directory into a directory that is on LaTeX's search path. On OSX with TeX Live, that can
be achieved by doing

    cd ~/Library/texmf/tex/latex
    ln -s route/to/cxltx-styles cxltx-styles

In a [more general fashion](http://tex.stackexchange.com/a/1138/28067), you may want to

    kpsewhich -var-value=TEXMFHOME

then take that route (e.g. `/Users/$USER/Library/texmf`) and append `/tex/latex` (which gets you
`/Users/$USER/Library/texmf/tex/latex`) to obtain a suitable location. It may be necessary to first create
that location using `mkdir -p ~/texmf/tex/latex`. Confusing it certainly is.


### CXLTX Style: Position Absolute

This style essentially loads [textpos][1], configures it, and adds some useful commands; these steps are
intended to make it easier to specifically put *single* lines of text onto the page, using absolute
coordinates that take the left end, the center point, or the right end of the baseline of the text and the
edge of paper as reference points.

The 'badge' of this style is `Pa`; it defines the following items:

[1]: http://www.tex.ac.uk/ctan/macros/latex/contrib/textpos/textpos.pdf


