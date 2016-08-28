# bin2qr
[![PyPI version](https://badge.fury.io/py/bin2qr.svg)](https://badge.fury.io/py/bin2qr)

bin2qr is a command line utility which converts binary to a QR code. This QR code is not created according to the correct format, but purely from adding the bits with black and white pixels (1 and 0 respectively).

bin2qr can be pretty useful from time to time: I use it for Geocaching puzzles and some ARG's (Alternate Reality Games) have used this technique as well.

## Installation

bin2qr can be installed using pip:

```
pip install bin2qr
```

Installing it using pip will also add the executable to your /usr/bin/ directory for global use.

## Usage

```
usage: bin2qr.py [-h] [-qr_size QR_SIZE] [-square_size SQUARE_SIZE] [--text]
                 [--exact]
                 [data] filename

Convert binary data to a QR code

positional arguments:
  data                  A string containing 0's and 1's. Defaults to stdin
  filename              The filename of the new image

optional arguments:
  -h, --help            show this help message and exit
  -qr_size QR_SIZE      The amount of squares in width. Defaults to 25
  -square_size SQUARE_SIZE
                        The width of a square in pixels. Default to 10
  --text                Take input as a string, which is converted to binary
  --exact               Throws an error when not supplied with exactly enough
                        data

```
