# CHANGES IN knitr VERSION 1.7

## NEW FEATURES

- added a simple Fortran engine after sitting with John Nash for a few minutes at UseR!2014; now we can use the chunk option `engine = 'fortran'` to include Fortran code in a source document, which will be compiled and loaded via `R CMD SHILIB` and `dyn.load()`, respectively

- added vignette engines with the suffix `_notangle`, which have the same weave functions as those engines without this suffix but have disabled the tangle function, meaning there will not be R scripts generated from the vignettes during `R CMD build` or `R CMD check` (thanks, Carl Boettiger and Michael Koohafkan, #784)

- added an argument `col.names` to `kable()`, so we can specify different column names (thanks, @jackflibb, #801)

- added a chunk option `fig.showtext` to support the **showtext** package; if `fig.showtext = TRUE` (which is what you should do if you use the **showtext** package), `showtext::showtext.begin()` is called before drawing plots (thanks, @yufree, #799)

- added a new language engine `node` for Node.js (thanks, Jake Burkhead, #823)

- added a package option `global.par`; if we set `opts_knit$set(global.par = TRUE)` (by default it is FALSE), the `par()` settings from the last code chunk will be preserved and applied to the next code chunk (thanks, Jim Winget)

- language engines also write error messages (if there are any) in the output now (thanks, Fabian Hirschmann, #789)

- added the `envir` argument to `knit_child()` so that users can specify a different environment to evaluate the child documents (thanks, Stéphane Laurent, http://stackoverflow.com/q/24009622/559676)

- for `set_parent()`, the lines in the parent document that start with `\bibliography` are matched and inserted in the child document so LaTeX bibliography also works for the child document (thanks, Mark Heckmann, #819)

## BUG FIXES

- fixed #779: when the chunk options `tidy=FALSE` and `eval=FALSE`, `prompt=TRUE` did not work for R expressions of multiple lines (thanks, Qijie Zhao)

- fixed #788: there was no increment in the chunk counter when the code chunks were read through `read_chunk()`, which may lead to clashes of chunk labels (thanks, Jason Ackman)

- fixed #790: when chunk A reuses code from chunk B via `<<B>>`, and only the first line of B is empty, chunk reuse can fail because A sees B as empty (thanks, @kingaa)

- fixed #791: if one has specified the chunk option `dev.args`, only `pointsize` and `bg` in it can be passed to the default recording device (the `pdf()` device) (thanks, @M-Russell and @joelgombin)

- fixed #822: `cache.lazy = FALSE` did not really work (thanks, Liz Ing-Simmons)

- fixed #704: **knitr** will emit a warning when a plot file from a previous chunk is to be overwritten; this may occur, for example, when two chunks are named `test` and `test1`, respectively, but the chunk `test` produces two plots (e.g. test1.png and test2.png) and `test1` produces one (e.g. test1.png) (thanks, Kevin Ushey and Kohske Takahashi)

- fixed rstudio/rmarkdown#205: when R marks the encoding of the input document as latin1, `knit()` can fail to convert its encoding (thanks, @ripkrizbi)

- fixed #828: scientific notation for inline numbers did not work in R Markdown v2 when the output format is LaTeX (thanks, @nacnudus)

- fixed #833: for the LaTeX output format, when `fig.cap` contains `.` in `{}`, the automatic short caption does not work (thanks, Roman Luštrik)

## MAJOR CHANGES

- the `knit()` function no longer modifies R's default `options(digits)` from 7 to 4, since it may lead to confusion especially when printing `summary()` output; for those who want the old behavior, you must set `options(digits = 4)` in the beginning of your document (thanks, John Honaker, #777)

- for the engine `coffee` (CoffeeScript), the flag `-p` has been removed from the command line arguments, which means the default behavior of this engine is to evaluate the code, instead of printing JavaScript; if you want the old behavior, you need the chunk option `engine.opts = '-p'` (thanks, Jake Burkhead, #821)

- when the chunk option `results = 'hold'`, the text output blocks will be collapsed into a single block (thanks, Gavin Simpson, #798)

- the video format for animations (when the chunk option `fig.show='animate'`) was changed from OGG to WebM (http://www.webmproject.org), which has many benefits over other formats, especially for the web (thanks, @gaorongchao, #641)

## MINOR CHANGES

- scientific formatting for inline R output is only applied to objects of which the first class is `numeric`, e.g. `chron::chron()` objects will no longer be formatted using scientific notations (thanks, @sanfordweisberg, #806)

- for R Markdown v2 documents, if the inline R output is formatted using the scientific notation, the output must be put in a math environment, e.g. `` $`r 2e10`$ ``

# CHANGES IN knitr VERSION 1.6

## NEW FEATURES

- added Textile (http://txstyle.org) support thanks to Richard Cotton; see the example 105 at https://github.com/yihui/knitr-examples (#623)

- added an argument `inline` to `spin()` so that some R code (by default, code of the form `{{code}}`) can be treated as inline expressions (thanks, Kirill Müller, #620)

- added an argument `prefix` to `write_bib()` so that we can customize the prefix for bib entries; we can also set `options(knitr.bib.prefix = 'a_string')` so that `write_bib()` uses this global option as the default value for `prefix` (thanks, Michael Friendly)

- the chunk option `dev.args` will be passed to the `pdf` recording device if `pdf` is also in the chunk option `dev` (note the recording device can be different with the actual drawing device); for example, you can pass a different `pointsize` to the PDF recording device (thanks, Felix Klein)

- intermediate files generated during `spin()` are deleted by default, this can be overridden by setting the new parameter `precious` to `TRUE` (thanks, Kirill Müller, #628)

- exposed the previously internal chunk option `code`, which is used to store the source code of a chunk; now users also have control over this option, which means we can programmatically assign source code for a code chunk, e.g. `code = capture.output(dump('fivenum', ''))`

- added a new chunk option `collapse` for Markdown output; if `collapse = TRUE`, **knitr** will try to merge the source and output blocks into one block; see example 039 at https://github.com/yihui/knitr-examples (thanks, Hadley Wickham)

- added a new chunk option `fig.retina` for better display quality of images in HTML output; for example, the physical size of an image is doubled and its display size is halved when `fig.retina = 2`

- added a new chunk option `strip.white` to trim the white lines in the beginning or end of a source chunk in the output (`strip.white = TRUE` by default)

- added a new chunk option `render` for custom rendering of R objects in the output; by default, the render function is `print()` (or `show()` for S4 objects) (thanks, Milan Bouchet-Valat, #484)

- added a new S3 generic function `knit_print()`, which is used as the default printing function for R objects in the code output now; users can define custom S3 methods to change the printing behavior of R objects in **knitr**

- added a new engine `scala` for Scala thanks to Simeon Fitch (#640)

- added a new engine named `asis` to write the chunk content without processing it; it also respects the chunk options `echo` and `eval` -- when either one is `FALSE`, the chunk will be hidden; this makes it possible to write text conditionally (thanks, Simon, #622)

- the Haskell engine supports multiline clauses using `:set +m` now, thanks to Adam Vogt (#633)

- added a new hook function `hook_purl()` so that code can be more reliably extracted from input documents; see `?hook_purl` for details (#607)

- exported a function `plot_crop()` which was used in `hook_pdfcrop()` in previous versions to crop the white margin of plots

- added a new chunk option `cache.lazy` (TRUE/FALSE) to decide whether to save and lazy load objects when `cache` is enabled; for really large objects, you may need `cache.lazy=FALSE` (thanks, Dario Strbenac, Scott Simpkins, and Mattrition, #572)

- added a chunk option `cache.comments` (TRUE/FALSE) to decide whether updating R comments in a code chunk should invalidate the cache database (thanks, @knokknok, #718)

- `kable()` supports alignment for HTML tables now (thanks, Joseph Larmarange, #656 and #683)

- `kable()` supports table captions in LaTeX, HTML and Pandoc's Markdown now (thanks, Joseph Larmarange, #687)

- the `digits` argument for `kable(x)` can also be a vector of length `ncol(x)`, in which case the number of digits is set for each column separately, e.g. `kable(data.frame(x=rnorm(10), y=rnorm(10)), digits = c(1, 4))` (thanks, @nacnudus, #771)

- for Markdown tables, `kable()` gained a new argument `padding` to specify the inner padding of table cells using spaces (thanks, @gavril0, #699)

- added a new vignette engine called `rmarkdown`, which uses `rmarkdown::render()` to create a package vignette from an R Markdown document; see http://rmarkdown.rstudio.com for more information about the **rmarkdown** package, and the vignette `knit_print.Rmd` in **knitr** for an example (basically you specify `\VignetteEngine{knitr::rmarkdown}` in your vignette)

- indentation is preserved when using chunk references `<<>>`, i.e., if `<<>>` is indented, the spaces before it will be applied to the code that it refers to (thanks, Terry Therneau)

- added a chunk option `fig.process`, which can be set as a function to take the path of a plot and process it, e.g. `fig.process = function(x) knitr::plot_crop(x)`; note the `fig.process` function must return a character string, such as the path of the figure, which might have been changed inside the function

- added a chunk option `R.options` so that we can temporarily set local options() for the current code chunk, and the options will be restored after the chunk (thanks, @r2evans, #764)

- the argument `shortcode` in `knit2wp()` can take a logical vector of length 2 now; the first element determines whether to highlight source code, and the second decides syntax highlighting for text output (thanks, Derek Ogle, #663)

- Sweave2knitr() will report the line numbers indicating where the Sweave-specific syntax was used (thanks, Kirill Muller, #676)

- added a function `current_input()` to return the filename of the input document (thanks, Kate Davis, #701)

- for Markdown, if n (n >= 3) backticks are detected in the code output, the output will be wrapped in n+1 backticks to make sure the original backticks in the output are not interpreted as the token of fenced code blocks

- for a chunk hook function, we can use a fourth optional argument `name`, which takes the value of the hook name, e.g. for `knit_hooks$set(foo = hook_foo)`, `hook_foo` can be of the form `function(before, options, envir, name)`, where `name == 'foo'` (thanks, Thell Fowler, #733)

- all the four arguments `before`, `options`, `envir`, and `name` for a chunk hook are optional now, e.g. you can define a hook function of the form `function(before, options)`, `function(before, name, envir)`, and so on

- added two read-only chunk options `out.width.px` and `out.height.px`, which are the numbers of pixels calculated from `fig.width` and `fig.height` (which have units in inches), respectively, and the chunk options `out.width` and `out.height` can override the calculated natural size

- added a new argument `encoding` to the `pandoc()` function to specify the character encoding of the input file(s) (fixed the problem http://stackoverflow.com/q/22198832/559676)

## BUG FIXES

- due to the change in evaluate v0.5, evaluate() may return the raw values of expressions, but the S3 method wrap() does not know how to handle them; now these values are just ignored (thanks, Dan Tenenbaum)

- fixed a bug for dep_auto() that may occur if old cache files generated from previous versions of knitr are used (thanks, Jeffrey Racine)

- fixed the bug reported at http://stackoverflow.com/q/19166724/559676: the inline hook did not work well with non-numeric values, e.g. Date (thanks, Waldir Leoncio)

- infinity is formatted as a symbol in inline LaTeX and HTML modes (thanks, Kirill Müller, #629)

- `kable()` did not keep the row name when the data only has one row (thanks, @eev2, #636)

- `kable()` did not recycle the `align` argument correctly (thanks, Adam Cooper, #638)

- `kable()` chokes on tables with NA's (thanks, Harlan Harris and Benjamin Schiller, #720)

- `kable()` did not work with non-numeric data frames (thanks, @talexand, #702)

- for Markdown/reST tables, `kable()` should make sure there are at least one empty line before the table output (thanks, @talexand, #705)

- fixed a bug related to child documents -- chunk options passed from a parent document may not be restored after the child document exits (thanks, Frank Harrell, http://bit.ly/17yitsD)

- fixed a bug for `hook_rgl()` when the chunk option `dev` is a vector of length greater than one (thanks, Ashley Manton, #703)

- `hook_rgl()` did not work with the chunk option `fig.cap` (thanks, Kohske Takahashi, #700)

- `%\documentclass{}` was mistakenly treated as the specification of the document class instead of a comment in a LaTeX document (thanks, Jarad Niemi, #713)

## MAJOR CHANGES

- the default value for the chunk option `tidy` is `FALSE` now, which means the R source code in chunks will no longer be reformatted by `formatR::tidy.source()` by default; this feature must be explicitly turned on by `tidy=TRUE`, and it has brought a lot of confusion in the past, so it is perhaps a good idea not to reformat the source code by default

- now we treat the closing mark (e.g. `@` in Sweave or the three backticks in R Markdown) as part of the code chunk, instead of the beginning of a text chunk, and a consequence for this change is that **knitr** no longer adds blank lines to the beginning of the text chunks (thanks, Thell 'Bo' Fowler, #752)

- inline R expressions will no longer be evaluated in `try()`, which means errors in inline R code will be emitted immediately

- the first argument of the `plot` hook is the filename of the plot now; in previous versions, it was a vector of length 2 (basename and file extension); see `?hook_plot`

- the default value for the `format` argument in `pandoc()` has changed: it defaults to the `t` field in the configuration if found, otherwise it defaults to `'html'` (thanks, Kohske Takahashi, #697)

- in the previous version, we can set `options(knitr.foo = value)` so that **knitr** can adjust the package options `opts_knit$set(foo = value)` before knitting a document; now the prefix for package options has been changed to `knitr.package.`, i.e. we should set `options(knitr.package.foo)` to achieve `opts_knit$set(foo)`; besides, it is also possible to change the default chunk options using `options(knitr.chunk.foo)` now, but you are warned that this may bring reproducibility issues, so please use with care

## MINOR CHANGES

- for R Markdown/AsciiDoc, line breaks are allowed in the inline R expressions now (thanks, Andrew MacDonald, #655)

- for AsciiDoc input, the R inline expressions can be written using the same syntax as R Markdown, i.e. `r expression`

- numbers containing characters other than digits 0-9, periods or commas are formatted as math in inline LaTeX, for negative numbers, infinity symbol, corner cases such as `10^{n}`, ... (thanks, Jeffrey Racine and Kirill Müller, #635)

- the column name `id` for row names in the `kable()` output is removed when the output format is Markdown (thanks, Artem Klevtsov, http://stackoverflow.com/q/19875529/559676)

- for R Markdown, special characters in figure filenames are no longer replaced by `_`, since RStudio v0.98.490 has fixed the base64 encoding bug; please upgrade RStudio to the latest version: http://www.rstudio.com/ide/download/

- the `tikzMetricsDictionary` option (for the **tikzDevice** package) is no longer overridden if it has already been set in `options()` (thanks, @rmatev, #708)

# CHANGES IN knitr VERSION 1.5

## NEW FEATURES

- a new option value `results='hold'` to flush all text output to the end of a chunk like `fig.show='hold'` (thanks, Harlan Harris, #593)

- when cache is enabled, automatic chunk dependencies can be truly automatic now; there is no need to call `dep_auto()` explicitly, and all we need to do is the chunk option `autodep=TRUE`; the chunk dependencies will be rebuilt after each chunk, so when new chunks are inserted into the document, **knitr** can also figure out the new dependencies automatically (thanks, @knokknok, #592)

- for Sublime Text users, there is a [SublimeKnitr](https://github.com/andrewheiss/SublimeKnitr) package to support LaTeX and Markdown with **knitr**; thanks, Andrew Heiss (#449) (this is not really a new feature of knitr **itself**, though)

- now the chunk options `warning` and `message` can also take numeric values as indices to select which warnings/messages to include in the output (thanks, Simon Urbanek, #590)

## BUG FIXES

- code changes in chunks should invalidate the cache when the chunk option `cache < 3`; fixed by @knokknok in #587

- fixed the bug reported at http://stackoverflow.com/q/18302179/559676; before evaluating inline R code, the chunk options `eval` must be evaluated to a logical value

- fixed the bug reported at http://stackoverflow.com/q/18544045/559676; read_chunk() no longer excludes code without chunk headers, so stitch() will include all the code as expected

- fixed the bug reported under http://stackoverflow.com/a/18541083/559676; now `\ensuremath{}` can be correctly applied to numbers of the form `10^{-n}`

- fixes a regression bug reported by Graham Williams: https://groups.google.com/forum/#!topic/knitr/_I5rlo9tOeA the global chunk options set in child documents are no longer restored

- fixed #604: `kable()` did not work on data frames/matrices of one row (thanks, Kevin Ushey)

- fixes the bug reported at http://stackoverflow.com/q/18992260/559676; `render_jekyll('prettify')` should have pasted the source code lines into one character string

## MAJOR CHANGES

- when the chunk option `cache=2`, the recorded plots (i.e. display lists) will no longer be cached, and the figure files will be assumed to exist the next time the document is compiled, otherwise the cache will be purged and the chunk needs to be recomputed; this will save some disk space and avoid bugs like #588 (thanks, @knokknok)

## MINOR CHANGES

- the Rnw chunk syntax is more tolerant on chunk headers in the sense that any characters after `<<>>=` are discarded, e.g. `<<echo=TRUE>>===foo` will be treated as `<<echo=TRUE>>=` (thanks, Michael Friendly)

- `knitr:::.onLoad()` no longer modifies the `PATH` variable when `/usr/texbin` is not in `PATH` under Mac OS

- when a message/warning/error contains line breaks, they are preserved and the message will not be re-wrapped (#602, thanks, Tyler Rinker)

- `read_chunk()` tolerates white spaces in the end of the chunk headers now (suggested by John Maindonald, #606)

- for R HTML documents, only the `highlight` component in `opts_knit$get('header')` was used in previous versions; now all components except `framed` and `tikz` are used; this makes it possible to further customize the HTML header (thanks, Wahlen Neuwirth and Erich Neuwirth)

- in previous versions, the global option `KNITR_WIDTH` in R was used to set `options(width)`; now this option is set from `opts_knit$get('width')`, which has the same default value `75` (#597)

# CHANGES IN knitr VERSION 1.4

## NOTE

- if you are using Windows or Linux, you need to read the news about both versions 1.3 and 1.4, because the version 1.3 did not manage to survive on CRAN

## NEW FEATURES

- the cache system is more granular now: instead of the binary option `cache = TRUE / FALSE`, we can specify `cache = 0, 1, 2, 3` (`TRUE` indicates 3, and `FALSE` indicates 0; for 1 and 2, see the documentation for `cache`: http://yihui.name/knitr/options); this means we may change the chunk options involved only with output rendering (e.g. from `echo = TRUE` to `FALSE`, or set `fig.cap = 'a new caption'`) without breaking the cache (thanks, Jeroen Ooms, Clark Kogan, and Roman Lustrik, #396, #536)

- added two new vignette engines called `docco_linear` and `docco_classic` using the Docco styles (http://jashkenas.github.io/docco/); see `browseVignettes(package = 'knitr')` for examples

- added a function `rocco()` to compile R Markdown documents to HTML using the classic Docco style, i.e. a two-column layout, with text on the left and code on the right (thanks, Weicheng Zhu, #577)

- added an argument `comment` in `spin()` to specify comment lines that will be ignored by `spin()`; by default, the block comment `/* comment */` is recognized; thanks, Bryan Hanson http://stackoverflow.com/q/17664401/559676

- it is possible to set package options prior to loading the **knitr** package now: for a package option `foo`, we can set `options(knitr.foo = value)` so that **knitr** will `opts_knit$set(foo = value)` when calling `knit()`; see `?opts_knit` for details (thanks, Zhiguang Zhao)

- added a new argument `ext` to the `pandoc()` function so that users can manually specify the output filename extensions (thanks, baptiste, http://stackoverflow.com/q/17710249/559676)

- for LaTeX and HTML output, syntax highlighting can be done for languages besides R (e.g. Python, Perl, ...); this is achieved by `highr::hi_andre()`, so Andre Simon's Highlight must be installed, otherwise **knitr** will fall back to verbatim output for source code; see https://github.com/yihui/knitr-examples/blob/master/098-highlight-python.Rnw for an example (#495)

## MAJOR CHANGES

- **knitr** formally depends on the **highr** package now (for syntax highlighting of LaTeX and HTML)

- the package option `stop_on_error` has been deprecated; now it is much easier to specify whether you want to stop on errors or not by using the existing chunk option `error`; if you want to stop, use `error=FALSE`; see the documentation for details: http://yihui.name/knitr/options

- the meanings of the chunk options `warning` and `message` when they take the value `FALSE` have also changed: `FALSE` means the warnings/messages will be printed in the R console and not recorded; this makes it easier to know when/where the warnings/messages were produced during `knit()`

- syntax highlighting and adding prompts are done in the `source` hook now; in previous versions, they were done in `knitr:::wrap.source`; now the `source` hook receives the pure source code instead of syntax highlighted code

## MINOR CHANGES

- for the chunk options set in package option `opts_knit$get('eval.after')`, they will not be evaluated after a chunk if `eval=FALSE` for that chunk (#570, thanks, @knokknok)

- for document formats that produce HTML output, the default graphical device is changed to `png` only if it is `pdf`; if the device has been changed to values other than `pdf`, **knitr** will no longer modify it internally; when the `png` device is not available, the `svg` device will be used instead

- removed the global option `KNITR_PROGRESS`, which was introduced to suppress the progress bar, but now we have got `knit(..., quiet = TRUE)`, so this option is redundant

## BUG FIXES

- in LaTeX output, the double quotes `"` in the messages, errors, and warnings are replaced by `"{}` because they might cause trouble to babel: http://stackoverflow.com/q/18125539/559676 (thanks, Thierry)

# CHANGES IN knitr VERSION 1.3

## NEW FEATURES

- added support for AsciiDoc; see example 089 at https://github.com/yihui/knitr-examples/blob/master/089-minimal.Rasciidoc (thanks, Richard Cotton)

- added support for reStructuredText vignettes in packages; now `*.Rrst` documents are recognized as vignettes as well, and they will be compiled to PDF via `rst2pdf` (#533) (thanks, Trevor L Davis)

- a new function `kable()` to produce simple tables; see the vignette `datatables` in `browseVignettes(package = 'knitr')` for an application; also see a LaTeX example at https://github.com/yihui/knitr-examples/blob/master/091-knitr-table.Rnw

- the chunk options `fig.width` and `fig.height` are vectorized according to the `dev` option, e.g. `fig.wdith = c(7, 10)` and `dev = c('pdf', 'png')` (#538) (thanks, @baptiste)

- for `purl()`, code chunks with the option `purl=FALSE` will not be included in the R script (#519, thanks, Sebastian)

- new `'hide'` value to the `fig.show` option; the figures are created but not included in the output document (#532) (thanks, Simon)

- the `sas` engine uses the listings output now (#541) and was tweaked for better LaTeX display (#562) (thanks, Nick Salkowski)

- added a quick and dirty `c` engine (via `R CMD SHLIB`); see https://github.com/yihui/knitr-examples/blob/master/090-engine-c.Rmd for an example

- added a new engine `asy` for Asymptote, a vector graphics language (http://asymptote.sourceforge.net); see examples 093 at https://github.com/yihui/knitr-examples (thanks, Thibaut Lamadon, #559)

- added a new engine `cat` to write the content of a code chunk to a file; see the example 095 at https://github.com/yihui/knitr-examples (thanks, Stephen Eglen)

- added a new function `knit_exit()` to allow `knit()` to exit early as if it had reached the end of the document (#524, thanks, Renaud)

- the chunk option `fig.align` also works for Markdown output now, e.g., `fig.align = 'center'` will center images in HTML via the `style` attribute of the `<img>` tag (#387)

- the argument `format` in the `pandoc()` function was vectorized, e.g. we can call `pandoc(input, format = c('html', 'latex'))` and the input file will be converted to HTML and LaTeX, respectively (#547, thanks, Jeroen Ooms)

- added an argument `options` to `knit_child()` to set global chunk options for child documents; if a parent chunk calls a child document (via the `child` option), the chunk options of the parent chunk will be used as global options for the child document, e.g. for `<<foo, child='bar.Rnw', fig.path='figure/foo-'>>=`, the figure path prefix will be `figure/foo-` in `bar.Rnw`; see http://stackoverflow.com/q/17514055/559676 for an application

- `eclipse_theme()` works with font weight (bold) and font style (italic) now when parsing themes from http://eclipsecolorthemes.org

- added two package options `latex.options.graphicx` and `latex.options.color` to allow customization of LaTeX package options, e.g. `opts_knit$set(latex.options.color = 'monochrome')` generates `\usepackage[monochrome]{color}` in the LaTeX output (#546)

- added a new package option `unnamed.chunk.label` to set the chunk labels for unnamed chunks, e.g. `opts_knit$set(unnamed.chunk.label = 'fig')` will generate chunk labels `fig-1`, `fig-2`, ... (#555) (thanks, Noam Ross)

- when `knit()` is running, a global option `knitr.in.progress` will be set to `TRUE`; this allows other package authors (e.g. `rCharts` and `googleVis`) to adjust certain package options according to `getOption('knitr.in.progress')` (thanks, Ramnath V, #564)

## BUG FIXES

- fixed #502: using `layout()` and `par()` at the same time under R 3.0.0 may lead to a corrupt plot (thanks, Hong Xu http://tex.stackexchange.com/q/108335/9128)

- fixed a bug in `pandoc()`: for single-lettered Pandoc arguments, the values are passed to them after spaces instead of equal signs (reported at http://stackoverflow.com/q/16569010/559676)

- fixed #542: when a child document has a sub-child document and also uses `set_parent()`, the LaTeX header will be added to the wrong file (thanks, Johan Toloe)

- `stitch_rmd()` was using a wrong R Markdown template

- fixed #537: misleading error message when the graphical device does not exist (thanks, Scott Kostyshak)

- fixed a bug in `hook_rgl()` reported at http://cos.name/cn/topic/110742 (incorrect LaTeX code when `fig.align='center'` and `fig.show='hold'`)

- fixed #565: added the `envir` argument to `spin()` so that the code is evaluated in the correct environment by default (thanks, @GillesSanMartin)

## MAJOR CHANGES

- `purl()` will write the chunk headers in `# ---- label, opts ----` instead of `# @knitr label, opts`, i.e. `@knitr` was changed to four dashes `----`; similarly, `read_chunk()` will use the pattern `# ---- label ----` to read external code (the old syntax `# @knitr` is still preserved for compatibility); the main reason for this change is that RStudio uses four dashes in R comments as section headings, so that it is possible to fold sections of R

- syntax highlighting is done by the **highr** package if it has been installed, otherwise the old regular-expression-based syntax highlighting will still be used; the **highr** package does much better syntax highlighting than the regexp-based approach (#327)

- the commands for syntax highlighting were changed for compatibility with Andre Simon's Highlight package; this will affect LaTeX and HTML users, e.g. `\hlnumber` was renamed to `\hlnum`; cached LaTeX and HTML will have to be rebuilt for the new syntax highlighting commands to work (#470)

- the argument `eval` was removed in `knit_child()`; if we do not want to evaluate a child document, we can set `eval=FALSE` in its parent chunk

- the script `inst/bin/knit` gains an option `-o` to specify the output filenames for `knit()` (#525, thanks, Aaron Wolen)

- the default video format for animations is OGG (it is open and free) instead of MP4 (non-free) now; this means Internet Explorer under Windows may not work with the animations (consider Firefox, Chrome and other modern web browsers)

- warnings and messages in adjacent output chunks are merged, respectively (#534)

- when the package option `verbose = TRUE`, the time stamp will be printed after each chunk using `timestamp()`, but this will mess up with the R command history, so now **knitr** uses `cat()` to write the time stamp (#545) (thanks, @knokknok)

- the argument `base` in `read_rforge()` was removed and hard-coded inside the function instead

- for Markdown output, the figure filenames no longer allow special characters like spaces, and special characters will be automatically replaced by `_`; this change will avoid problems when publishing to RPubs from RStudio: if figure paths contain special characters, the figures will not be uploaded (thanks, Sangsoon Woo)

## MINOR CHANGES

- the package vignettes uses `\VignetteEngine{knitr::knitr}` instead of `\VignetteEngine{knitr}` so that the next version of R can compile the vignettes out of the box (via `R CMD Sweave`) and no longer need to build the whole package in order to build the vignettes

## MAINTAINENCE

- the package vignettes were moved to the `vignettes` directory from `inst/doc` since the former will be preferred by the future versions of R

- the testing is done via the **testit** package now (http://cran.r-project.org/package=testit)

## MISC

- **knitr** becomes an affiliated project of FOAS (Foundation for Open Access Statistics): http://www.foastat.org/projects.html

# CHANGES IN knitr VERSION 1.2

## NEW FEATURES

- added a Pandoc wrapper function `pandoc()` to convert Markdown documents to other formats that Pandoc support such as LaTeX/PDF, OpenDocument, HTML5 slides and unfortunately also Word (#206)

- in the past the chunk hooks were ignored when the chunk option `engine != 'R'` (i.e. code in the chunk is not R); now they are executed regardless of the language of the code chunk (#483) (thanks, @cdrv)

- multiple WebGL objects from the **rgl** package can be generated in the same web page now; each object is uniquely identified by the corresponding chunk label; see https://dl.dropbox.com/u/15335397/misc/webgl-rmd.html for an example (#494) (thanks, Carson Sievert)

- if multiple graphical devices are used (the chunk option `dev` is a vector of length greater than 1), the chunk option `dev.args` can be a named list of lists of device arguments, with each element a list of arguments to be passed to the single device; see http://yihui.name/knitr/options

- as announced in the last version, R 3.0.0 will support non-Sweave vignettes; now it is also possible to compile R HTML vignettes via **knitr** since `*.Rhtml` files are also registered by **knitr** as vignette files

- a new chunk option `cache.vars` to manually specify which variables to save in the cache database; by default all newly created and modified variables are identified and saved, but in some cases, **knitr** may not be able to identify the modified variables, e.g. `DT[, foo:=value]` in **data.table** (we can set `cache.vars='DT'` to force **knitr** to save a copy of `DT`)

- added a new engine `Rscript` to run the R code in a new R session; see http://stackoverflow.com/q/15271406/559676 for an example

- the executable script `inst/bin/knit` can accept multiple input files now (e.g. `knit foo.Rnw bar.Rmd zzz.Rhtml`)

- `knit()` and `knit2html()` gained a `quiet` argument to suppress messages and the progress bar (thanks, Vince Buffalo)

- added the `text` argument to `spin()` and `stitch()` respectively as an alternative way to provide the input like `knit()` (#509) (thanks, Craig Watson)

- a new function `wrap_rmd()` to wrap long lines in Rmd files without affecting the code blocks (if there are any); this makes it easier for verson control purposes

- `rst2pdf()` will pass a default output filename to `rst2pdf` (if the input is `foo.rst`, the output will be `foo.pdf`)

- `knit2wp()` gained a new argument `publish = TRUE` (thanks, Eric Nantz) (#512)

## BUG FIXES

- fixed the problem reported in http://stackoverflow.com/q/12448507/559676 now \usepackage{upquote} should appear after \usepackage{fontenc}, so single quotes in verbatim environments will no longer cause problems

- fixed #487: `stitch_rhtml()` and `stitch_rmd()` should not use the chunk option `out.width = '.6\\linewidth'` (thanks, Tal Galili)

- when the chunk option `engine` is not `R`, the code is also executed under the directory `opts_knit$get('root.dir')` (if specified); thanks, Winawer http://stackoverflow.com/q/15512545/559676

- `:` is permitted in `fig.path` now (#513) (thanks, Sebastian)

- fixed an encoding problem (`CP950`) for Hong Kong Windows users reported at http://bit.ly/16RQL5E

## MAJOR CHANGES

- all child documents are inserted into the parent document as character strings of the (compiled) content, instead of being saved into files (e.g. `\input{foo-child.tex}`); no matter how many child documents there are, only one main output file will be generated; the package option `child.command` was removed accordingly since it is no longer used

- no longer generates concordance data for child documents; the past attempt did not really work well and the implementation was complicated, so now we only support concordance for the main document; the consequence of this change is the synchronization between PDF and Rnw for child documents no longer works at the line level (clicking in PDF will still bring the focus back to the child chunk)

- in previous versions, cached chunks were evaluated in separate (empty) environments in order to capture the newly created variables, but this brings confusion when we use functions depending on the current environment such as `ls()` (which will return `character(0)`); now all chunks, cached or not, are evaluated in the same environment `knit_global()` (finally fixed #456)

- `knit2pdf()` and `knit2html()` return the output filename when the input is a file (in previous versions, `NULL` was returned in this case)

- the package option `stop_on_error` is set to `2` now when building package vignettes, which means R will stop on errors in vignettes; this make it easier to find out possible problems in vignettes during `R CMD build`

- the document hook `hook_rjournal()` was removed; it was too hackish (see http://yihui.name/en/2013/02/contribute-to-the-r-journal-with-lyx-knitr/ for how to write an article for The R Journal in a less hackish way)

## MINOR CHANGES

- the progress bar symbol was changed from `>` to `.` so it looks less intrusive (#395) (thanks, Michael Friendly)

## DOCUMENTATION

- the **knitr** book is forthcoming: http://www.crcpress.com/product/isbn/9781482203530 run `citation('knitr')` or `toBibtex(citation('knitr'))` in R to obtain the citation info

- open `help(package = 'knitr', help_type = 'html')` to see the vignette examples (Rnw, R Markdown and R HTML)

# CHANGES IN knitr VERSION 1.1

## NEW FEATURES

- (experimental) R 3.0.0 will support non-Sweave vignettes, e.g. Rnw documents can be compiled by **knitr** instead of Sweave; in addition, R Markdown vignettes have also become first-class citizens as R package vignettes; see http://yihui.name/knitr/demo/vignette/ for details

- a new engine for coffeescript (i.e. the chunk option `engine='coffee'`); see https://github.com/yihui/knitr-examples/blob/master/080-engine-coffeescript.Rmd for an example (thanks, Nacho Caballero)

- when the chunk option `eval=FALSE`, `purl()` will comment out the code when extracting code chunks (thanks, Randall Pruim)

- the global option `KNITR_PROGRESS` can be used to set the package option `progress` in `opts_knit`, e.g. after `options(KNITR_PROGRESS = FALSE)`, this option will be set to `FALSE` when the package is loaded (#395)

- the global option `KNITR_WIDTH` can be use to set the R option `width` before **knitr** is called; in the past this option was hard-coded to `75`, and now it is set as `options(width = getOption('KNITR_WIDTH', 75L))`

- a new function `knit2wp()` which compiles R Markdown documents and publishes the results to WordPress; see http://yihui.name/knitr/demo/wordpress/ for details

- a new hook `hook_webgl()` which writes the WebGL code of an **rgl** scene into the output using `rgl::writeWebGL()` so we can reproduce a 3D plot in the browser (thanks, Stephane Laurent http://stackoverflow.com/q/14879210/559676)

## BUG FIXES

- fixed #465: when `eval=FALSE` and `echo` is numeric, code was incorrectly filtered by the indices in `echo` (thanks, @ateucher)

- `>` was not allowed in HTML inline code expressions (http://stackoverflow.com/q/14360296/559676); now the regular expression works correctly to look for `-->` instead of `>`

- `set_parent()` should not print `NULL` in the child document: http://stackoverflow.com/q/14487718/559676 (thanks, Thomas Holz)

- child documents now inherit the encoding parameter from their parent document, i.e. the `encoding` value in `knit(..., encoding = ?)` is applied to `knit_child()`; note this assumes the parent document and the child documents use the same encoding (thanks, Henrik Nyhus)

## MAJOR CHANGES

- empty inline expressions are no longer recognized, e.g. `\Sexpr{}` will not be parsed; this allows one to write such markers in tutorials (e.g. `<!--rinline -->` and `` `r ` ``); internally `all_patterns$foo$inline.code` was changed

- the function `build_dep()` was removed (the warning has been there for a long time); please use `dep_auto()` instead

- the package option `filter.chunk.end` was removed; this means in Rnw documents, a single line `@` has the meaning of terminating a chunk _only if_ there is a chunk header `<<>>=` before it; otherwise it does not have any special meanings

- the function `run_chunk()` was removed; it is redundant because we already have the chunk option `ref.label` as well as in-chunk reference `<<label>>`

## MINOR CHANGES

- the function `imgur_upload()` uses Imgur API version 3 now; if you are using the key obtained from version 2, you need to register for your own client id: http://api.imgur.com (#439)

- allow users to pass a custom environment to `Rcpp::sourceCpp()` in the `Rcpp` engine; fixes http://stackoverflow.com/q/14882486/559676

- slight improvement of encoding support in `knit()`

# CHANGES IN knitr VERSION 1.0

## NEW FEATURES

- a new function `knit_expand()` which is similar to **brew** and mustache, e.g. it expands `pi is {{pi}}` to `pi is 3.14`; it can also be used for building child documents (see https://github.com/yihui/knitr-examples/blob/master/075-knit-expand.Rnw for example) (#397) (thanks, Frank Harrell)

- `knit()` gained a new argument `encoding` to specify the encoding of the input file (multilingual support is complete now), e.g. `knit(..., encoding = 'GBK')` for Simplified Chinese

- a new function `Sweave2knitr()` to convert Sweave documents to **knitr**; several automatic translations can be done, e.g. `results=tex` to `results='asis'`, `width=5` to `fig.width=5`, `echo=true` to `echo=TRUE`, `keep.source=TRUE` to `tidy=FALSE`, `eps=TRUE` to `dev='postscript'`, `\SweaveOpts{...}` to `opts_chunk$set(...)` and so on; see the documentation in the package for details (#451)

- if the Sweave syntax is detected in an Rnw document, a message box (**tcltk**) will pop up as a reminder on how to fix it

- inline R code also respects the option `opts_knti$get('stop_on_error')` now, e.g. if we set this option to `2L`, R will completely stop when error occurs in inline R code

- a new function `all_labels()` to get all chunk labels in a document; see one application at https://github.com/yihui/knitr-examples/blob/master/073-code-appendix.Rnw

- chunk hooks will be run (if exist) even if the code chunk is empty

- added two wrapper functions `stitch_rhtml()` and `stitch_rmd()` which use the R HTML and R Markdown templates respectively when calling `stitch()`

- the chunk label is used as the id of the div element in R HTML output, e.g. `<div id='chunk-label'>...</div>`

## MAJOR CHANGES

- (IMPORTANT) the internal compatibility with Sweave has been dropped as scheduled in the last version, and the **knitr** parser was greatly simplified accordingly; Sweave users can call the function `Sweave2knitr()` to convert old Rnw files to **knitr** files before running `knit()`

- accordingly, the pattern elements `global.options` and `inline.doc` were removed from `knit_patterns` (`\SweaveOpts{}` and `\SweaveInput{}` will no longer be supported; please call `Sweave2knitr()` to convert incompatible Sweave documents)

- chunk labels can be arbitrary characters now; in the past they had to be valid R symbols, e.g. `2a` was an invalid label; this restriction has been removed, because chunk labels will be automatically quoted when necessary (`<<2a>>=` will become `<<'2a'>>=`, but `<<'3-function'>>=` will remain untouched)

- if the chunk option `include=FALSE`, the evaluation will stop if errors occur (i.e. `stop_on_error=2L` for **evaluate**), because otherwise it will be very difficult for authors to notice errors in chunks which have `include=FALSE` (#453) (thanks, Joshua Pritikin)

- the function `knit_env()` is no longer available (it is not exported any more), and `knit_global()` has been exported now

## MINOR CHANGES

- for inline R code, the value is returned only if the R code prints a visible value, e.g. `\Sexpr{x <- 1}` will be empty, and `\Sexpr{pi}` will return the value of pi

## BUG FIXES

- fixed #432: no longer uses `\\\\` in LaTeX output; only a single line break is converted to `\\` (thanks, Kevin Wright)

- `render_html()` guarantees that the R source code is highlighted when the chunk option `highlight = TRUE` (#447) (thanks, Ramnath Vaidyanathan)

- `dep_auto()` was unable to find the cache files if the input document is not under the current working directory (thanks, Hui Yao)

## Documentation

- because Github has deprecated downloads, all downloads were moved to Bitbucket, and the links in the package website as well as all examples have been updated (#438)

# CHANGES IN knitr VERSION 0.9

## NEW FEATURES

- added a demo named `notebook` which is an R notebook based on the **shiny** package (https://github.com/rstudio/shiny); use `demo('notebook', package = 'knitr')` to see it, or visit http://glimmer.rstudio.com/yihui/knitr

- for numeric inline output in LaTeX, the `I()` trick is no longer needed, e.g. `$x=\Sexpr{1.2e10}$` is safe for LaTeX now due to `\ensuremath{}` (#137) (thanks, Randall Pruim)

- the chunk option `eval` can take numeric values now, specifying which expressions to evaluate in a chunk (#383) (thanks, Jared Lander)

- a new package option `stop_on_error` which specifies the behavior of errors in code chunks; this option is passed to the **evaluate** package, e.g. `opts_knit$set(stop_on_error = 2L)` will make **knitr** completely stop on errors (the default value is `0L` which means to move on even if errors occurred); this makes it possible to see the call stacks via `traceback()` in an interactive R session when an error occurs (#344) (thanks, Hadley Wickham and Dzidorius Martinaitis)

- added support to the **Rcpp** package through the chunk option `engine='Rcpp'` so that we can write C++ source code in the document; see https://github.com/yihui/knitr-examples/blob/master/029-engine-Rcpp.Rmd for an example (#415) (thanks, JJ Allaire)

- **knitr** throws a warning when a cached chunk depends on an uncached chunk because this kind of dependency will be ignored (#431) (thanks, @ghostwheel)

- a list of arguments can be passed to `formatR::tidy.source()` as the chunk option `tidy.opts` now, e.g. `tidy.opts=list(width.cutoff=60, keep.blank.line=FALSE)` (#429)

- some chunk options are recycled for plots such as `fig.env`, `out.width` and `out.extra`, etc; this means if there are multiple plots per chunk, we can specify different output options for them individually (e.g. `out.width=c('2in', '.4\\linewidth')` for two plots); see https://github.com/yihui/knitr-examples/blob/master/067-graphics-options.Rnw for an example (motivated by #430) (thanks, @autumnlin)

- added a new chunk option `fig.subcap` for captions of subfigures in LaTeX; when there are multiple plots in a chunk, and neither `fig.subcap` nor `fig.cap` is NULL, `\subfloat{}` will be used for individual plots (you need to add `\usepackage{subfig}` in the preamble); also see https://github.com/yihui/knitr-examples/blob/master/067-graphics-options.Rnw for an example (#388) (thanks, @skipperhoyer)

- `stitch()` accepts labeled R scripts now; if an R script contains chunk headers of the form `## @knitr label, options`, they will be used in the template (#411) (thanks, @jamiefolson)

- the function `read_chunk()` gained a few new arguments so that we can reference external code chunks in another way, which was an idea from the **SweaveListingUtils** package (thanks, Peter Ruckdeschel)

- a new function `read_demo()` based on `read_chunk()` to read demo scripts in R packages

- a new convenience function `read_rforge()` to read code from R-Forge repositories; combined with `read_chunk()`, it can insert R code from R-Forge into **knitr** dynamically; see https://github.com/yihui/knitr-examples/blob/master/046-read-rforge.Rmd for an example (thanks, Peter Ruckdeschel)

- chunk options are also written after `## @knitr` along with chunk labels when tangling R scripts via `purl()`

- `purl()` gained a new argument `documentation` to also write documentation lines into the R script (#391 and #401) (thanks, Noam Ross and Fernando Mayer)

- `knit_rd()` generates a navigation frame on the left and builds links now; this is like the CHM help in old days (thanks, Michael Friendly)

- a new function `knit_rd_all()` to build static html help pages for all the packages installed

- we can also use `## @knitr` to write chunk options for `spin()` now (`#+` and `#-` still work)

- added new language support for Perl and Z Shell (`zsh`); see an example at https://github.com/yihui/knitr-examples/blob/master/028-engine-perl.Rmd (#406) (thanks, Jim Hester)

- `render_jekyll()` gained an argument `highlight` to specify which highlighting engine to use (Pygments or Prettify.js) (thanks, Yanping Chen)

- two new chunk options for language engines: `engine.path` and `engine.opts`; the former can be used to specify the path of the program (e.g. `<<engine='ruby', engine.path='/usr/bin/ruby1.9.1'>>=`); the latter can be used to pass additional arguments to the engine program

- added new engines for GraphViz (`engine='dot'`) and TikZ (`engine='tikz'`); see https://github.com/yihui/knitr-examples/blob/master/057-engine-dot.Rmd and https://github.com/yihui/knitr-examples/blob/master/058-engine-tikz.Rmd for examples (#419) (thanks, Michel Kuhlmann)

- added a preliminary engine for SAS which is basically a call like `system('sas chunk-code.sas')` (#354)

- a new `document` hook to post-process the LaTeX output document to move code chunks out of figure/table environments so that code chunks will not float with the environments; see `?hook_movecode` for details

- chunk hooks are called in the _reverse_ order after a chunk (and natural order before a chunk); this allows one to, e.g. write an opening environment before a chunk and close it properly after a chunk

- all language engines also respect the `comment` option when writing output just like R code chunks (by default the output is commented out by `##`)

- added a new function `set_alias()` as a wrapper to `opts_knit$set(aliases = ...)`, e.g. `set_alias(w = 'fig.width')` sets `w` as an alias for the chunk option `fig.width`

## MAJOR CHANGES

- global options are strongly recommended to be set via real R code `opts_chunk$set(opt = value)` in a code chunk instead of the old syntax in text chunks like `\SweaveOpts{opt = value}`, or `<!--roptions opt=value-->`, etc, which will be deprecated in the next version; this will make it cleaner and safer to parse the source documents, e.g. we can write arbitrarily complicated expressions like `opts_chunk$set(fig.width = if (foo == 'beamer') { 5 } else { 7 })` which is impossible in the old syntax; if you still use the old syntax like `\SweaveOpts{}`, you will see a warning with a pause of 10 seconds

- based on the same reason, it is recommended to use the chunk option `child` to input child documents; old syntax like `\SweaveInput{}` will be deprecated

- for markdown output, results from inline R code will no longer be put in a pair of backticks (#379)

- the package option `opts_knit$get('cache.extra')` was removed because this option should really be a chunk option instead; see http://yihui.name/knitr/demo/cache/ for the updated documentation (#404 and #405) (thanks, Jim Hester)

- the chunk option `highlight.opts` was deprecated and renamed to `engine.opts`; this affects users who use Andre Simon's highlight through the `highlight` engine in **knitr**

- the chunk option `file` for Awk was deprecated; we can also use `engine.opts` to specify the file for Awk; see https://github.com/yihui/knitr-examples/blob/master/024-engine-awk.Rmd for example

- the pattern `knit_pattern$get('ref.label')` was deprecated since it is unlikely to be customized; a fixed pattern `'^#+\\s*@knitr(.*)$'` will be used instead

## MINOR CHANGES

- when `opts_knit$get('verbose')` is `TRUE`, a `timestamp()` will be printed before each code chunk is evaluated (#377) (thanks, Carl Boettiger)

- `stitch()` will no longer copy the template over to the current working directory (thanks, Michael Friendly)

- `stitch()` will no longer open the PDF/HTML output automatically (#411) (thanks, Michel Kuhlmann)

- the script `inst/bin/knit` can also convert R Markdown documents to HTML now; the argument `--pdf` was removed and a new argument `--no-convert` was added

- dots in figure filenames will not be replaced with `_` when the output is not LaTeX (thanks, Stewart Macarthur)

## BUG FIXES

- fixed #410: when the inline R code returns `NA_real_`, the scientific notation of numbers will run into errors (thanks, Rafik)

- the syntax pattern for Rnw documents was not quite right: `all_patterns$rnw$chunk.end = '^\\s*@\\s*%*'` actually allows any characters after `@`, but only LaTeX comments and white spaces are allowed; it has been fixed to `^\\s*@\\s*(%+.*|)$` now

## DOCUMENTATION

- an example of combining R, knitr and D3 to draw a contour plot: http://yihui.name/knitr/demo/javascript/

# CHANGES IN knitr VERSION 0.8

## NEW FEATURES

- output from other languages (e.g. python, awk, ...) can also be cached like R output when `cache=TRUE`; see [023-engine-python.Rmd](https://github.com/yihui/knitr-examples/blob/master/023-engine-python.Rmd) for an example

- added support for bash/shell scripts; use the chunk option `engine='bash'` or `engine='sh'` to write shell scripts in code chunks (#361)

- a new function `knit_rd()` to knit package documentation (run examples code and insert output in the HTML documentation) (#227) (thanks, Taiyun Wei)

- added LuaTeX support for tikz graphics with the **tikzDevice** package (set `options(tikzDefaultEngine = 'luatex')`); this feature requires **tikzDevice** version > 0.6.2 (#358) (thanks, Alastair Andrew)

- a new chunk option `fig.env` to set which environment to use for figures in LaTeX, e.g. we can set `fig.env='marginfigure'` to use `\begin{marginfigure}` (#364) (thanks, Bryan Hanson)

- added a new package option `global.device` (default `FALSE`) which specifies whether to use a global graphics device to capture plots; if `TRUE`, it is possible to write `plot(1:10)` in a previous chunk and `abline(0, 1)` in a latter chunk because all code chunks share the same device, however, this may also bring unexpected consequences (in particular, using `par()` can bring redundant plots)

## BUG FIXES

- dots in figure paths are more safely replaced with `_` now, e.g. `fig.path='../figure'` will no longer be replaced by `__/figure` (#346) (thanks, @ralfer)

- the `c()` syntax for the chunk option `dependson` did not actually work, e.g. `dependson=c('foo', 'bar')` (#350) (thanks, Cassio Pereira)

- fixed a bug when `eval=FALSE` and `prompt=TRUE` (the continuation character was used in some places where there should be the prompt character) (thanks, Derek Ogle)

- `persp()` plots were not recognized in the last version (thanks, Jeffrey Racine)

## MAJOR CHANGES

- leading spaces are allowed in chunk headers now, e.g. in the past `<<>>=` must appear in the beginning of a line, but now we can indent the chunk header by a number of white spaces; this amount of spaces will be stripped off the chunk if the whole chunk is indented (#236) (thanks, @jamiefolson and Vitalie Spinu)

- markdown output will be indented if the original code chunk is indented; this allows chunk output to be nested within its parent environment, e.g. inside an ordered list (see [001-minimal.Rmd](https://github.com/yihui/knitr-examples/blob/master/001-minimal.Rmd) for example)

- when the global chunk option `eval=FALSE`, inline R code will not be evaluated, and `??` is returned for inline R expressions (#367)

## MINOR CHANGES

- if `getOption('OutDec')` is not `.`, inline numeric output will be put inside `\text{}` in LaTeX to avoid situations like #348 (the normal math mode may add a space after the comma in `3,1415`)

- if the chunk option `external==FALSE` (default is `TRUE`), **knitr** will no longer automatically add `\usepackage{tikz}` to the LaTeX preamble; you need to add it manually (but it is recommended to use `external=TRUE` with `cache=TRUE` for the sake of speed, because compilation of tikz graphics may be slow)

- `*.brew` generates `*.txt` by default (instead of `*-out.brew`)

- `knit(text = ...)` will no longer write output in the console (the output is only returned as a character string)

## DOCUMENTATION

- added a simple reference card: https://bitbucket.org/stat/knitr/downloads/knitr-refcard.pdf

# CHANGES IN knitr VERSION 0.7

## NEW FEATURES

- added a new chunk option `out.extra` to write extra graphics output options, e.g. `<<out.extra='angle=90'>>=` to rotate the figure by 90 degrees; see http://yihui.name/knitr/options (#301) (thanks, @knokknok)

- when `opts_knit$get('verbose')` is TRUE, logs (messages, warnings and errors) along with the corresponding R code will be printed after `knit()` is done; this might help users figure out possible problems in R code quickly (#276)

- `.Random.seed` is cached again for the sake of reproducibility; see http://yihui.name/knitr/demo/cache/ for how to maintain reproducibility when the computation involves with random number generation (#274) (thanks, Renaud)

- the package option `opts_knit$get('cache.extra')` can be an unevaluated R expression now, e.g. `opts_knit$set(cache.extra = quote(.Random.seed))`; see the cache page above for a concrete example

- added a new package option `'root.dir'` (default `NULL`) which can be used to set the root directory to evaluate code chunks in a document; by default, the root directory is the directory of the input document, and this option enables users to set other directories as the working directory for code chunks (#277) (thanks, Ken Williams)

- `spin()` will add `\documentclass{article}` for Rnw output if no document class is specified in the R script so that the LaTeX output will be a complete document (#295) (thanks, Christiaan Klijn)

- added Ruby support in the `engine` option; see the example https://github.com/yihui/knitr/blob/master/inst/examples/knitr-lang.Rmd (#294) (thanks, Ramnath Vaidyanathan)

- also added Haskell support in the option `engine='haskell'` through calling `ghc` (#336) (thanks, Michel Kuhlmann)

- added support to Andre Simon's `highlight` through the option `engine='highlight'`; see https://gist.github.com/3114112 for an example of highlighting Matlab code in LaTeX (thanks, Dirk Eddelbuettel and Renaud Gaujoux)

- the output hooks for LaTeX, HTML, Markdown and reST will respect the `engine` option now, so these hooks can be readily used for output when the language is not R, e.g. `render_markdown(strict = TRUE)` also works for Python output (#251) (thanks, Chris Fonnesbeck)

- the chunk options `eval`, `echo` and `results` are also respected when the language is not R, e.g. for a Python code chunk with `eval=FALSE`, the code will not be evaluated, or for a Ruby chunk with `results='hide'`, the output will be hidden (#293) (thanks, Ramnath Vaidyanathan)

- chunk options `out.width`, `out.height` and `out.extra` also work for plots in HTML and Markdown output now, e.g. `out.width='200px'` or `out.extra='style="display:block;"'` (#297) (thanks, Taiyun Wei and Alan Severini)

- the hook function to create animations in HTML output is exported now as `hook_ffmpeg_html()`

- added a package option `opts_knit$get('animation.fun')` which defaults to `hook_ffmpeg_html`; this option is used to create animations in HTML output so that we do not have to use FFmpeg

- added two functions `hook_r2swf()` and `hook_scianimator()` which can be set as the package option `opts_knit$get('animation.fun')` and create animations via the **R2SWF** package or the **SciAnimator** library (see `animation::saveHTML`) (thanks, Taiyun Wei)

- a new function `image_uri()` to create data URIs for image files; we can set `opts_knit$set(upload.fun = image_uri)` so that images are embedded in the HTML output as data URIs (hence the HTML page does not depend on external images) (#298, #324) (thanks, Wush Wu)

- added a new object `opts_template` which can be used to set a group of chunk options and they can be referenced later with the new chunk option `opts.label`; see `?opts_template` for examples; this makes it easy to reuse groups of frequently used chunk options (#316, #320) (thanks, Cassio Pereira)

- a new function `dep_prev()` to build chunk cache dependencies so that all later chunks will depend on previous chunks; if any of a previous chunk is updated, the cache of all chunks after it will be updated as well (#285) (thanks, @muelleki)

- a new chunk hook function `hook_optipng()` to optimize PNG images using `optipng` (#272) (thanks, Winston Chang)

- added a new output hook named `document` in `knit_hooks` (see `knit_hooks$get('document')`); this hook function is used to process the output of the whole document; it can be useful when we want to post-process the whote output before writing it to the output file

- a new function `rst2pdf()` which uses the program `rst2pdf` to convert reST documents to PDF; it is also supported by `knit2pdf()` when `compiler='rst2pdf'` (#300) (thanks, Alex Zvoleff)

## BUG FIXES

- fixed #286: messages (including warnings and errors) are guaranteed to be ended by `\n`, so even when chunk option `comment=NA`, messages will also be rendered correctly (thanks, Carl Boettiger)

- fixed #273: when knitting a file under another directory with cache turned on (e.g. `knit('foo/bar.Rnw')`), `lazyLoad()` is unable to load the cache files under a relative path because the working directory has been changed to the directory of the input file during evaluation

- fixed #292: layout() may cause the capture of unwanted plots (thanks, Austen Wallace Head)

- fixed #302: when there are multiple instances of `\documentclass` in the source document, **knitr** will be unable to insert the LaTeX preamble

- fixed #308: if `options('OutDec')` was set to a character other than `.`, the LaTeX code can be malformed (#308) (thanks, Cassio Pereira)

- `opts_chunk$set()` in a child document was only working in that child document, but was expected to change chunk options globally; now it works everywhere, and will affect all chunks after this setting, no matter where `opts_chunk$set()` is (thanks, Guy Lebanon) (http://bit.ly/MexHXd)

- fixed #332: calling `purl()` inside a source document when `knit()` the same document could cause clashes; now it is safe to put `purl()` inside a source document and `knit()` it

- fixed #342: when `eval=FALSE`, line breaks in the source code were missing

## MAJOR CHANGES

- if the chunk label contains non-alphanumeric characters (except `-` and `_`), these characters will be replaced by `_` in the figure filenames (if there are any) and a warning will be issued; this is to guarantee the figure filenames are valid to LaTeX (#321) (thanks, (Roman Lustrik)

- the [**highlight**](http://cran.r-project.org/package=highlight) package is not enabled by default; use `opts_knit$set(use.highlight = TRUE)` to enable it

- the default LaTeX output will put messages, warnings and errors in special LaTeX environments: errors are red, warnings are magenta, and messages are italic; in previous versions they were in the `verbatim` environment (#264) (thanks, @muelleki)

- unnamed chunks are named sequentially in a single call of `knit()` according to the order of their appearance no matter where they are, e.g. if there are two unnamed chunks in two child documents respectively, they will be named as `unnamed-chunk-1` and `unnamed-chunk-2`; in previous versions, both will be named as `unnamed-chunk-1` which can cause clashes of cache and figure files

- the function `build_dep()` was renamed to `dep_auto()` which better reflects what this function really does; it is still available in this package but may be removed in future versions

- the package **tikzDevice** was removed from the `Suggests` field, but this will not affect users who have already installed **tikzDevice**; for those who have not, this package has been archived on CRAN (hopefully only temporarily), so you have to install from the source

## MINOR CHANGES

- the LaTeX environment `kframe` was updated so that it can be used in other environments such as `center` or `tabular` (#283) (thanks, @muelleki)

- the OpenCPU demo is more robust to double quotes in the text (#271); see http://public.opencpu.org/userapps/opencpu/knitr/

- for Sweave output, the results will not be put inside the Schunk environment when `results='asis'`

- `stitch()` templates use more sensible figure paths by default: the path for figures is `'figure/input-script-name'` now, i.e. it will be different for different input R scripts to avoid possible clashes

- `stitch()` templates no longer use default title and author names; if the user did not set them in the R script (as meta comments `# title:` and `# author:`), there will not be titles or authors in the output

- **knitr** will no longer use scientific notations for integers in inline R code output; sci notation only applies to double-precision numbers (#296) (thanks, @knokknok)

- `options()` set in the main document will apply to its child documents (e.g. `options('digits')`) (#306) (thanks, Cassio Pereira)

- the `...` argument in `knit2html()` is passed to `markdownToHTML()` in the **markdown** package instead of `knit()`; this allows us to pass more arguments to control the rendering of HTML output, e.g. `knit2html(input, fragment.only = TRUE)` (#333) (thanks, @Bart6114)

## DOCUMENTATION

- the example using other languages was updated to show how some chunk options (`eval`, `echo` and `results`) also work for foreign languages: https://github.com/yihui/knitr/blob/master/inst/examples/knitr-lang.Rmd

# CHANGES IN knitr VERSION 0.6.3

## MAJOR CHANGES

- this is a urgent patch version for CRAN: the dependencies on **highlight** and **parser** were removed because these two package were orphaned on CRAN; now **knitr** uses a naive syntax highlighter for LateX and HTML output if **highlight** is not available, which has a similar appearance with **highlight**; when the **parser** package is not available, users should be careful with the chunk option `tidy=TRUE`: `replace.assign` may not work as expected; see the NEWS of **formatR** for details: https://github.com/yihui/formatR/blob/master/NEWS; you are welcome to improve the naive highlighter: https://github.com/yihui/knitr/tree/master/R/highlight.R

## NEW FEATURES

- `spin()` gained a new argument `report` for Rnw/Rtex/Rmd documents to be compiled to PDF/HTML respectively (#287) (thanks, kohske takahashi)

## BUG FIXES

- `stitch()` and `spin()` should use `parent.frame()` to evaluate code by default; in 0.6, the default parent frame for the inner `knit()` was a wrong environment to use (thanks, Michael Nelson)

- use `unnamed-chunk-i` as the label when chunk `label == ''` (#280) (thanks, Josh Paulson)

- fixed #279 and #281, both of which are about concordance

- `'\\maxwidth'` does not apply to LaTeX/PDF animations, so the default value of `out.width` for animations in LaTeX is still `NULL`; you will have to set `out.width` manually for a reasonable width (#282) (thanks, Ramnath Vaidyanathan)

## MISC

- **knitr** and RStudio had an invited talk at useR! 2012; slides at http://yihui.name/slides/2012-knitr-RStudio.html (source at https://github.com/yihui/knitr-talks)

# CHANGES IN knitr VERSION 0.6

## NEW FEATURES

- for LaTeX output, the chunk option `out.width` defaults to `'\\maxwidth'` now; see http://yihui.name/knitr/demo/framed/ for its definition; this makes sure the figures do not overflow the page margins (#221)

- the chunk option `size` now defines the font size of the whole chunk instead of only some special characters in it, so that the old trick of redefining the `knitrout` environment for different font sizes is no longer necessary; see updates in the beamer examples: http://yihui.name/knitr/demo/beamer/ (thanks, Baptiste Auguie)

- added a new chunk option `dev.args` which can be used to pass more arguments to the graphical device (#254) (thanks, knokknok)

- warnings, messages and errors will be wrapped according to `options('width')`; this will make long messages stay within the page margin when the width option is appropriately small (#259) (thanks, @muelleki)

- added a new function `spin()` to turn a specially formatted R script to a report; see http://yihui.name/knitr/demo/stitch/ (#223) (thanks, Rich FitzJohn)

- `knit()` gained a new argument `envir` to specify the environment in which to evaluate code chunks (by default in the parent frame); this will allow users to run code in a specified environment so that the global environment will not be cluttered by objects in chunks, which can hopefully make the documents more reproducible (#228)

- added a new component `inline.comment` in `knit_patterns` to strip off tokens of inline R code from inline comments, e.g. a comment line like `% x is \Sexpr{x}` will become `% x is x` (#110); this will only happen to LaTeX documents because HTML does not have inline comments (it only has block comments `<!-- -->`)

- concordance works for child documents as well now (try RStudio), although it is not very precise (#225); note when concordance is enabled, the results from child documents will be written into the parent output instead of individual tex files; also note you have to set `opts_knit$set(self.contained = FALSE)` for concordance to work better

- added an OpenCPU app so that we can run knitr in the cloud now; see `system.file('opencpu', 'apps', 'index.html', package = 'knitr')` or http://public.opencpu.org/apps/knitr (thanks, Jeroen Ooms)

- all messages, errors and warnings during evaluation are recorded in an internal object `knit_log` (use `knitr:::knit_log$get()` to get all messages) and they are printed if the package option `verbose` is `TRUE` (i.e. `opts_knit$get('verbose')`) (#224)

- child documents are also supported in other document formats besides LaTeX, e.g. Markdown and HTML, etc; please use the chunk option `child` to specify the child documents; see `system.file('examples', 'child', 'knitr-main.Rmd', package = 'knitr')` for an example in markdown (#268) (thanks, @martinaryee)

## BUG FIXES

- the templates for `stitch()` used `results=hide` which should really be `results='hide'` (#219) (thanks, @r2d3)

- format numbers with the reST syntax instead of HTML (#218) (thanks, Jeffrey Arnold)

- `hook_pdfcrop()` should work better under Windows now (#209) (thanks @DCCKC and @r2d3)

- tikz compilation fails on Windows network drives

- FFmpeg does not really work for HTML/Markdown output because the dot in figure filenames was omitted (thanks, Ming Kuo)

- child documents can fail when they are in different sub directories (thanks, Christoph J)

- `set_parent()` failed to work in the last version due to a bug when inserting the parent preamble into the child document (#240)

- better preserve plot sizes in interactive sessions (#258)

## MAJOR CHANGES

- `.Random.seed` is not cached any more because of weird problems due to lazy loading (#248 and #253); users should use `set.seed()` to make sure reproducibility of random simulations; the chunk output is cached in a `.RData` database instead of a lazy load database to avoid problems in #253

- the default graphics device is set to the null PDF device before evaluating code chunks, i.e. `pdf(file = NULL)`, so that neither `Rplots.pdf` nor plot windows will be opened during knitting

## MINOR CHANGES

- **knitr** will show a message when a chunk is empty; this helps users who do not actually want a chunk to be empty realize the problem like #229; in the past, knitr just silently returns an empty string from such chunks

- **knitr** will show a message when the cache is loaded and the option `opts_knit$get('verbose')` is `TRUE` (#249) (thanks, Carl Boettiger)

- the filename extensions `Snw` and `Stex` are also supported (`foo.Snw`/`foo.Stex` produces `foo.tex`)

- the HTML output hooks are changed according to the suggestion of Barry Rowlingson (#250) and the default CSS is also slightly modified

- `knit()` will no longer try to remove the file `NA` generated by `pdf(file = NULL)` before R 2.14.2 (which was a bug and fixed later); you should update R if you see this file

## DOCUMENTATION

- added a minimal brew example under `system.file('examples', package = 'knitr')`

# CHANGES IN knitr VERSION 0.5

## NEW FEATURES

- white spaces are allowed before `<<>>` when using chunk references, and this approach of references is supported in tex and html documents as well

- added a new pattern list named `md` so that R code blocks can be written more naturally in extended markdown (e.g. GFM and pandoc): use ```` ```{r label, opts}```` to begin a chunk and ```` ``` ```` (three or more backticks) to begin normal text, and write inline R code in `` `r code.here` ``; it is the default syntax for markdown input, or you can call `pat_md()` before `knit()` so **knitr** can make use of this pattern list to process the input document

- RStudio has added full support to **knitr**: we can knit HTML and Markdown documents easily now, and markdown output will be automatically converted to an HTML preview page just like TeX to PDF

- if the pattern list is not set in advance, **knitr** will try to detect the syntax automatically by matching all built-in pattern lists against the input document, e.g. if the input contains `<<>>=`, the Rnw syntax will be used, and if ```` ```{r} ```` is detected, the markdown syntax will be used; this feature enables us to use different sets of syntax freely, e.g. we can use Sweave syntax in markdown files and knitr will be able to recognize it (#189)

- new filename extensions with a prefix `R` or `r` are introduced: `*.Rhtml`, `*.Rhtm`, `*.Rtex`, `*.Rmd` and `*.Rmarkdown` will be recognized as source documents like `*.Rnw`, and the output filename will be automatically determined by removing the `R` prefix, e.g. `foo.Rmd` produces `foo.md` by default; the old clumsy naming convention `foo_knit_.md` is still preserved but not recommended any more (#187)

- new function `knit2html()` to knit an Rmd file (R with markdown) and convert to HTML in one step using the **markdown** package

- new functions `pat_rst()` and `render_rst()` to support reStructuredText; use `.. {r label, options}` and `.. ..` to write R code chunks; see https://github.com/yihui/knitr/tree/master/inst/examples/knitr-minimal.Rrst (thanks, Jeffrey Arnold and Ramnath Vaidyanathan)

- new package option `self.contained` which decides whether to write style definitions (highlighting) in external files or put them in the output document; the highlighting definitions in LaTeX output is often too long, so `opts_knit$set(self.contained = FALSE)` can help in this case (#176) (thanks, Ramnath Vaidyanathan)

- new package option `filter.chunk.end` which decides if the `chunk.end` pattern really means `chunk.end`; see http://yihui.name/knitr/options (thanks, Joe Cheng)

- syntax highlighting themes are also available to HTML output now; the usage is the same as in LaTeX (#179) (thanks, Ramnath Vaidyanathan)

- the chunk option `fig.cap` is also used in markdown output now

- the random seed `.Random.seed` is also cached for the sake of reprodubibility in random simulations

- the function call `read_chunk()` will be evaluated when tangling R code via `purl()` (#175) (thanks, Carl Boettiger)

- the default LaTeX output will use the **upquote** package if it exists so that single quotes are straight in the output (thanks, Mathematical Coffee http://bit.ly/IKjluw)

- the chunk option `engine` is back but it is used to run code from other languages instead of just ignoring the code in chunks which have `engine != 'R'`; currently this option is still in rough edges and only supports python and awk; other languages may be added in future versions, but users can also do it by themselves by `knit_engines$set(language = function(options) {...})`; see an example at `system.file('examples', 'knitr-lang.Rmd')` (#201)

- new function `write_bib()` to write Bibtex citations for R packages; see the main manual for a sample usage (#13)

- `hook_pdfcrop()` also supports cropping other image formats like PNG or JPEG through ImageMagick (`convert -trim`) (#203) (thanks, @r2d3)

## MAJOR CHANGES

- **knitr** will completely stop when duplicated labels are found and the corresponding chunks are non-empty; in previous version, only a warning is given and R code in later chunks will override previous chunks (#185) (thanks, Randall Pruim)

- the default graphical device for HTML and markdown output is `png()` now to avoid the possible unexpected consequence that PDF plots are included in an HTML page which will not work normally in browsers

- markdown output will use the extended markdown hooks by default now: `render_markdown(strict = FALSE)`; in the past the default was `render_jekyll()`; the major difference is that the code blocks are put in ```` ```r and ``` ````; if you want the strict markdown syntax, you can all `render_markdown(strict = TRUE)` which will indent code blocks by 4 spaces

- `render_gfm()` has been removed because the name can be misleading; the main purpose of this function was to put code blocks in ```` ``` ````, and now we can replace it by `render_markdown(FALSE)`; other markdown flavors also support such fenced code blocks (e.g. pandoc) -- it is not limited to Github only

- the default value for the `fig.path` option is `'figure/'` now so that plots will be put under this directory by default; the default was `'./'` in the past which makes the current directory messy when there are many plots

- **knitr** will fully stop when an error is encountered in `knit()`; in the past only a message was issued in this case in an interactive R session

- the package option `all.patterns` has been dropped; please use the objects `all_patterns` or `knit_patterns` directly if you want to tweak the syntax

## BUG FIXES

- the compilation of tikz graphics can hang up when there are TeX errors in the tikz file; now we use `\nonstopmode` to avoid hiccup (#188)

- multiple devices per chunk was buggy (#181)

- S4 objects will be printed by `show()` instead of `print()`; this is a bug of the **evaluate** package, which has been fixed (please update it to be > 0.4.2)

## MISC

- it is recommended to use `opts_chunk$set()` to set global chunk options now instead of `\SweaveOpts{}`; all documentation has been updated (#216)

- number of downloads (https://github.com/yihui/knitr/downloads) of **knitr** documentation before I removed and updated them on GitHub: c(main = ?, graphics = 275+)

# CHANGES IN knitr VERSION 0.4

## NEW FEATURES

- Sweave concordance was finally implemented: when `opts_knit$get('concordance')` is `TRUE`, **knitr** will write a file named `'input-concordance.tex'` which contains the mapping between input Rnw and output tex line numbers; this feature is mainly for (but not limited to) RStudio to provide better error navigations: you can jump from the TeX error message to the Rnw source directly to know where the error comes from (the line number of the source of the error may not be accurate but should be in the ballpark) (#133) (thanks, JJ Allaire and Joe Cheng)

- if output hooks have been set before calling `knit()`, they will be respected, i.e. **knitr** will no longer override them by default hooks; you need to make sure *all* output hooks are set appropriately, e.g. you can start by `render_latex()` and change some individual hooks later (#165) (thanks, Andrew Redd)

- newly created objects in the global environment will also be cached if cache is turned on (`cache=TRUE`); in previous versions **knitr** is unaware of objects created in `globalenv()`, e.g. `setGeneric()` creates S4 generic functions in `globalenv()` and **knitr** was unable to capture them (#138) (thanks, syoh)

- chunk options `dev`, `fig.ext` and `dpi` can be vectors now; this allows one to save a plot to multiple formats, e.g. `<<foo, dev=c('pdf', 'png')>>=` creates two files for the same plot: `foo.pdf` and `foo.png` (#168) (thanks, MYaseen208)

- an experimental feature for animations created by FFmpeg in HTML/markdown output when `fig.show='animate'` (#166) (thanks, gabysbrain)

- the chunk option `fig.cap` supports multiple figure captions in LaTeX now, e.g. if a chunk produces two plots, we can use `fig.cap = c('first caption', 'second caption')` to assign two different captions to them respectively when `fig.show = 'asis'` (#155) (thanks, Jonathan Kennel)

- new package option `opts_knit$get('upload.fun')` which is a function that takes a plot file to upload to a certain host and returns the link to the image; by default it is `imgur_upload()`, and you can use your own function to upload images to other hosts like Flickr (#159) (thanks, Carl Boettiger)

- all packages loaded in the current session are also cached, so as long as a package has been loaded previously, it will be available to all following chunks (#160)

- new chunk option `autodep` and function `build_dep()` to build cache dependencies among cached chunks automatically by analyzing object names in all cached chunks; this is a loose alternative to the `dependson` option (see main manual and `?build_dep` for details) (#72) (thanks, Seth Falcon)

- input and output in `knit()` are no longer restricted to files; they can be `stdin()`/`stdout()` or other types of connections (#162; see https://github.com/yihui/knitr/issues/162) (thanks, gabysbrain)

- as-is output (`results='asis'`) and plots are no longer put in the framed environments because of incompatibilities (#163) (thanks, DCCKC, Murray Logan and Jennym Hutchison)

## BUG FIXES

- for plots in LaTeX output, centering should be done with `{\centering }` instead of `\centering{}` (#156) (thanks, Ramnath Vaidyanathan)

- the recorded plot is a more precise representation of the expected plot now, because the recording device also takes the plot size into consideration (#157) (thanks, Christiaan Klijn and Frank Harrell)

- `format_sci()` now correctly formats 0; this function is used for inline R code to format numbers in scientific notation (#161) (thanks, Kihoro J. M.)

- fixed a bug for the case in which the chunk option only contains the label like `<<label=foobar>>=`; knitr 0.3 was unable to parse the label correctly (`<<foobar>>=` is OK) (thanks, Muhammad Yaseen)

## MINOR CHANGES

- `imgur_upload()` returns the link to the image directly, with the XML list as its attribute (in v0.3 the list was returned)

- more verbose messages in case of chunk errors: both line numbers of the source and chunk info will be printed

## DOCUMENTATION

- website updated as usual: http://yihui.name/knitr

- added an example for subfloat environment: https://bitbucket.org/stat/knitr/downloads/knitr-subfloats.pdf

- most manuals (main or graphics manuals) have been updated

## MISC

- number of downloads (https://github.com/yihui/knitr/downloads) of knitr documentation before I removed and updated them on GitHub: c(main = 400, graphics = 177)

# CHANGES IN knitr VERSION 0.3

## NEW FEATURES

- a fundamental and important new feature for writing chunk options: they can be written as valid R code now, just like we write function arguments (e.g. `echo=c(1, 3, 5)`, `fig.cap="my figure caption"`); all options will be parsed and evaluated as R code by default; see http://yihui.name/knitr/options for details (#142) (thanks, Baptiste Auguie)

- chunk references using `<<label>>` is supported now (#86); thanks to Kevin R. Coombe and Terry Therneau for the discussion

- new function `run_chunk()` to run the code in a specified chunk, which is an alternative to the chunk reference in Sweave; see http://yihui.name/knitr/demo/reference/

- a executable script `knit` under `system.files('bin', package = 'knitr')` which makes it easier to call knitr via command line under *nix (call `knit input [output] [--pdf]`)

- the inline hooks respect `getOption('digits')` and `getOption('scipen')` now (see `?options`); numbers returned from inline R code will be formatted according to these two options (see a demo at http://yihui.name/knitr/demo/output/)

- if you still use old Sweave syntax for chunk options, it is possible to write literal commas in chunk options now -- they have to be escaped by `\`, e.g. `caption=hello\, world`; this will be parsed to `'hello, world'` as a character string; of course this looks ugly and has limited power, so please please consider the new syntax!

- `knit2pdf()` gained another argument `compiler` which can be used to specify the program to compile the tex document to PDF, such as xelatex (#131) (thanks, Ramnath Vaidyanathan and Dennis Murphy)

- a new function `imgur_upload()` to upload images to imgur.com; it can be used in HTML or Markdown hooks so the output is a self-contained document which does not need additional image files; `opts_knit$get('upload.fun'`) can use this function (#66) (thanks, Ramnath Vaidyanathan)

- a child document can be compiled individually with the LaTeX preamble borrowed automatically from a parent document using a new function `set_parent()`; see the help page for details (#136) (thanks, Helder Correia)

- to avoid `$$` around numbers in the inline output, we can use `I()` to protect the numeric inline output, e.g. `$x = \Sexpr{I(10^7)}$` gives `$x = 10^7$` whereas `\Sexpr{10^7}` gives `$10^7$` (thanks, Kevin Middleton)

- the listings package is formally supported now (see `?render_listings`); the default style is borrowed from `Sweavel.sty` written by Frank Harrell (#101) (thanks, Frank)

- new package option `cache.extra` which allows more objects to affect cache; see http://yihui.name/knitr/demo/cache/ (#134)

- new package option `child.path` to specify the search path of child documents relative to the parent document (#141)

- new package option `aliases` to set aliases for chunk options; see http://yihui.name/knitr/options (#144)

- new chunk options `fig.cap`, `fig.scap` and `fig.lp` to write captions, short captions, label prefix for the figure environment in LaTeX (#145) (thanks, Frank Harrell)

- new package option `eval.after` to set a character vector of chunk options which should be evaluated _after_ a chunk is executed (thanks, Frank Harrell)

- a series of convenience functions `pat_rnw()`, `pat_tex()`, `pat_brew()` and `pat_html()` to set built-in patterns (syntax) to read input

## MINOR CHANGES

- package option `eval.opts` has been dropped: all options of classes `symbol` or `language` will be evaluated, so there is no need to specify which options to evaluate manually; remember, the chunk options are similar to function arguments, so you can use any valid R code there

- the default value for the `output` argument in `knit()` is NULL now, so we can also provide output filenames to `stitch()` and `knit2pdf()` (#119)

- standard LaTeX messages are suppressed when a tikz plot is compiled to PDF so that we can see the **knitr** process more clearly

- `%\SweaveInput{}` will be ignored now (#150)

- `results=asis` will no longer affect the `chunk` hook (in the past, the chunk output was not wrapped in the `kframe` environment when `results=asis`); it only affects the `output` hook now

- the package website allows comments now

## MAJOR CHANGES

- the starting pattern of normal texts in an Rnw document is `^@\\s*%*` instead of `^@\\s*$` now, meaning you can write `@ % a comment` to end a code chunk (this is consistent with Sweave)

- the default value of the argument `output` of `knit()` will be a filename under the current working directory; in previous versions, the output file will be under the same directory as the input file; this change makes it possible to completely separate the input files and output files into different places, and hopefully will give users better experience in managing a whole collection of files (including child documents): put all source files in one place and output files in another place

- the package homepage is http://yihui.name/knitr now (the previous URL yihui.github.com/knitr will be automatically redirected to the new address)

## BUG FIXES

- the object `opts_current` does not give the evaluated version of the current chunk options because it was created before the options are evaluated; this has been fixed and `opts_current$get()` will give the expected values of options (thanks, Frank Harrell)

## MISC

- number of downloads (https://github.com/yihui/knitr/downloads) of knitr documentation before I removed and updated them on GitHub: c(main = 1300, graphics = 549, themes = 130, beamer = 565, listings = 240, minimal = 160)

# CHANGES IN knitr VERSION 0.2

## NEW FEATURES

- added support for including child documents in a main document (like `\SweaveInput{}` but with different implementations); see http://yihui.name/knitr/demo/child/ (#92)

- for inline R code, character results are returned as-is now (without `\texttt{}`)

- new function `purl()` as a wrapper to `knit(..., tangle = TRUE)` which extracts R code from the input document (thanks to Dieter Menne's wife who suggested the function name)

- the error hook applies to inline R code when an error occurs in the inline R code, in which case knitr will not stop by default; instead, it writes the error message into the output (#85)

- chunk option `split` also works for HTML output now using `<iframe></iframe>` (#82)

- `knit()` gained an argument `text` as an alternative to `input` (#88)

- new chunk option `child` to include child documents into the main document (#92)

- chunk option `external` defaults to `TRUE` now (was `FALSE` by default in the last version)

- added a new demo to show how to build package vignettes with knitr: http://yihui.name/knitr/demo/vignette/

- added support to the `quartz()` device under Mac (#103); now the `dev` option has more choices (see http://yihui.name/knitr/options)

- chunk option `echo` can take a numeric vector to select which R expressions to echo into the output now (#108); see http://yihui.name/knitr/options

- a new function `stitch()` which is a convenience function to insert an R script into a template and compile (to quickly create a report
  based on an R script)

- for a chunk hook to run, the corresponding chunk option no longer has to be `TRUE`; it can be any non-null values; this enables us to make use of the option value directly instead of only knowing it is `TRUE` (see http://yihui.name/knitr/demo/cache/ for an example)

- `knit()` will no longer writes figure or cache files in the same directory as the input document; instead, these files are written in the current working directory (see ?knit)

- a new function `knit_env()` that makes the environment of the current chunk accessible to the user

## BUG FIXES

- the code used to merge global chunk options and local options was buggy for cache; it has been fixed now, so cache is more stable (#105), but users may see previously cached chunks being re-evaluated with this version, which should be regarded as a normal phenomenon, and on the second run, the cached chunks will not be evaluated again

- fixed a buglet when using both options `out.width` and `out.height` in Rnw (#113)


# CHANGES IN knitr VERSION 0.1

## NEW FEATURES

- first version of knitr: it covers most features in Sweave, **cacheSweave** and **pgfSweave**; see package homepage for documentation and examples: http://yihui.name/knitr/

## MISC

- **knitr** won an Honorable Mention prize (before it was formally released to CRAN) in the Applications of R in Business Contest hosted by Revolution Analytics: http://bit.ly/wP1Dii http://bit.ly/wDRCPV

- in this NEWS file, #n means the issue number on GitHub, e.g. #142 is https://github.com/yihui/knitr/issues/142

