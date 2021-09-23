# hotfuzz

[![test](https://github.com/axelf4/hotfuzz/actions/workflows/test.yml/badge.svg)](https://github.com/axelf4/hotfuzz/actions/workflows/test.yml)
[![codecov](https://codecov.io/gh/axelf4/hotfuzz/branch/master/graph/badge.svg?token=OV1BqTB7QL)](https://codecov.io/gh/axelf4/hotfuzz)
[![MELPA](https://melpa.org/packages/hotfuzz-badge.svg)](https://melpa.org/#/hotfuzz)

This is a fuzzy Emacs completion style similar to the built-in `flex` style,
but with a better scoring algorithm.
Specifically, it is non-greedy and ranks completions that match at
word; path component; or camelCase boundaries higher.

To use hotfuzz, add it to the `completion-styles` list:
```elisp
(setq completion-styles '(hotfuzz))
```
or, if using [Selectrum], enable `hotfuzz-selectrum-mode`.

**Note:** For now highlighting is only applied with `hotfuzz-selectrum-mode`
because doing it for the default completions API would require
the highlighting to be computed even for completions that are not displayed.

## Customization

Hotfuzz adheres to a few of the default Emacs completion configuration options:
* `completion-ignore-case` specifies whether case should be considered
  significant when matching.
* The face `completions-common-part` is used for highlighting the
  characters of a candidate that the search string matched.

## Dynamic module

Optionally, you can compile the bundled dynamic module
to improve the performance of `hotfuzz-selectrum-mode`.
(The completion style API is infinitely more awkward to interface with,
mostly because matching and sorting are forced to be done separately.)
Once the shared object is available in `load-path`
it will automatically be picked up when hotfuzz is loaded,
or you may evaluate `(require 'hotfuzz-module)`
if hotfuzz has already been loaded.
To compile, make sure GCC, CMake and GNU Make or similar are present,
and run

```sh
mkdir build
cd build
cmake -G 'Unix Makefiles' \
	-DCMAKE_C_FLAGS='-O3 -march=native' \
	.. \
	&& make
```

and place the resulting shared library somewhere in `load-path`.

Unlike the Lisp implementation,
the dynamic module uses an unstable sorting algorithm
and is always case-insensitive.

## Related projects

### The `flex` completion style

The `flex` completion style included with Emacs
does the same matching as hotfuzz, and scores candidates based on gap sizes.
It does not, however, attempt to find the optimal score.
For example, given the search string `"foo"`,
the matched characters in a candidate could look like

> x**f**xxx**o**xxx**o**xfoox

which would score low even though
there is a contiguous match later in the string.

### flx

The [flx] package - which out-of-the-box only supports [Ido] -
has scoring criteria similar to those used by hotfuzz,
but works a little differently.
Its bountiful use of caching
means it can be faster at scoring long candidates.
Since the ordering of completions differs between flx and hotfuzz
you are encouraged to try both.

### orderless

The [orderless] completion style allows
every component of a space-delimited (by default) pattern
to match according to any other completion style.
It is very customizable,
but does no sorting and allows the individual sub-patterns to overlap
(`"foo foo"` filters no additional items compared to `"foo"`).
Hotfuzz on the other hand tries to be more *clever* about sorting,
and so users who dislike that may prefer orderless.

[Selectrum]: https://github.com/raxod502/selectrum
[flx]: https://github.com/lewang/flx
[Ido]: https://www.gnu.org/software/emacs/manual/html_node/ido/index.html
[orderless]: https://github.com/oantolin/orderless