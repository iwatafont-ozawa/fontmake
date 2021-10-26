![GitHub Actions Build Status][] ![Python Versions][] [![PyPI
Version][]][1]

# fontmake

`fontmake` compiles fonts from various sources (`.glyphs`, `.ufo`, `designspace`) into binaries (`.otf`, `.ttf`). You can use it to create static instances and variable fonts.

## Installation

Fontmake requires Python 3.7 or later.

Releases are available on [PyPI][] and can be installed with [pip][].

``` bash
pip3 install fontmake
```

Use the `-U`, `--upgrade` option to update fontmake and its dependencies
to the newest available release:

``` bash
pip3 install -U fontmake
```

## Basic Usage

After installation, you will be able to use the `fontmake` executable.

For example, to compile a variable font from a Glyphs source file, use:

``` bash
fontmake MyFont.glyphs -o variable
```

The most important command line arguments to the `fontmake` executable are the required input, specified either as positional argument or using one of `-g`/`-u`/`-m` flags, and the optional `-o` flag, which chooses the output file format.

### Source file format options

There are two ways to specify the source file or files:

One can either use the following, mutually exclusive, flags:
* `-g filename.glyphs`: Converts a Glyphs source file to binary.
* `-u filename.ufo ...`: Converts one or more UFO files to binary.
* `-m filename.designspace`: Converts a Designspace file to binary. (The `-m` is for `mutatormath`, an old Python library for handling designspaces.)

Alternatively, one can specify the input(s) as positional arguments without the flag, letting fontmake infer the source format from the file extension: e.g. ``fontmake MyFont.designspace``, etc.

Note: if the positional arguments are preceded by an option that takes one or more arguments, you need to use the special `--` separator to mark all following
arguments as posional (non-options), otherwise the parser gets confused. E.g., the `-i` option takes zero or one arguments (see futher below for details); without `--`, argparse thinks you didn't provide any inputs:

```bash
fontmake -i -- MyFont.designspace
```

Exactly one type of input can/must be specified, using either approaches.

### Output file format options

You may provide one or more output file formats after the `-o` option. For example, `-o otf ttf` creates OTF and TTF binary font files for each master in your input file.

The following output file formats are available:

* `otf`: Per-master OTF (CFF-outline) binaries. Placed in the `master_otf/` directory.
* `ttf`: Per-master TTF (TrueType-outline) binaries. Placed in the `master_ttf/` directory.
* `otf-cff2`: Per-master OTF binaries with CFF2 outlines. Placed in the `master_otf/` directory.
* `variable`: A TrueType variable font. Placed in the `variable_ttf/` directory.
* `variable-cff2`: A variable font with CFF2 outlines. Placed in the `variable_otf/` directory.

The following output file formats are also available, but are generally used internally by fontmake as an intermediate step to one of the above outputs:

* `otf-interpolatable`: OTF binaries suitable for merging into a variable font. Placed in the `master_otf_interpolatable/` directory. (These differ from `otf` in that the outlines are unoptimized.)
* `ttf-interpolatable`: TTF binaries suitable for merging into a variable font. Placed in the `master_ttf_interpolatable/` directory. (The outlines are converted to quadratic curves in an interpolation-compatible way.)
* `ufo`: Glyphs sources can be converted to UFO. Placed in the `master_ufo/` directory.

If no format option is specified, the default is `-o otf ttf`.

### Other important command line options

* `-i` (Interpolate instances): Having per-master binaries is not always what you expect; if you have defined instances ("exports") in your Glyphs file, they will *not* be generated by default. To generate them, pass the `-i` flag, which interpolates static instances, and places them in the `instance_ttf/` or `instance_otf/` directory as appropriate.

* `--output-dir <some_directory>`: Places all output in the given directory, instead of the per-format directories mentioned above.

* `--output-path <filename>`: This is only valid if the output is a single binary file, and writes the output to the given filename.

* `-f` (Flatten components): Ensures that any glyphs which contain components which *themselves* contain components are decomposed to a single level. This is recommended as certain rendering environments do not correctly handle nested components - see [this link](https://github.com/googlefonts/fontbakery/issues/2961) for more details.

## Advanced Usage

See [`USAGE.md`](USAGE.md).

## Troubleshooting

See [`TROUBLESHOOTING.md`](TROUBLESHOOTING.md).

## Developers

Developers can get the latest version of `fontmake` by cloning the git repository:

``` bash
git clone https://github.com/googlefonts/fontmake
cd fontmake
pip install .
```

Developers who want to quickly test changes to the source code without re-installing can use the "--editable" option when installing from a local source checkout:

``` bash
pip install -e .
```

It is recommended to install fontmake inside a virtual environment to
prevent conflicts between its dependencies and other modules installed
globally.

You could also use the [pipx][] tool to automate the
installation/upgrade of python apps like fontmake in isolated
environments.

  [GitHub Actions Build Status]: https://github.com/googlefonts/fontmake/workflows/Test%20+%20Deploy/badge.svg
  [Python Versions]: https://img.shields.io/badge/python-3.7-blue.svg
  [PyPI Version]: https://img.shields.io/pypi/v/fontmake.svg
  [1]: https://pypi.org/project/fontmake/
  [PyPI]: https://pypi.org/project/fontmake
  [pip]: https://pip.pypa.io
  [pipx]: https://github.com/pipxproject/pipx
