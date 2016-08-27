#!/usr/bin/env python
from PIL import Image
from PIL import ImageDraw
import random


def start_of_line(y, qr_size):
    """
    Returns the x coordinate of the start position on a given y-level

    :param y: y-coordinate of the line
    :param qr_size: The size of the QR code in squares
    :type y: int
    :type qr_size: int

    :returns: The x-coordinate of the start position
    :rtype: int
    """

    if (qr_size >= 20):
        if y < 8 or y >= (qr_size - 8):
            return 8
        return 0
    else:
        if y < 8:
            return 8
        return 0


def length_of_line(y, qr_size):
    """
    Returns the length of the data area on a given y-level

    :param y: y-coordinate of the line
    :param qr_size: The size of the QR code in squares
    :type y: int
    :type qr_size: int

    :returns: The amount of squares data can be stored in
    :rtype: int
    """
    if qr_size >= 20:
        if y < 8:
            return qr_size - 16
        if y >= (qr_size - 8):
            return qr_size - 8

        return qr_size
    else:
        if y < 8:
            return qr_size - 8
        else:
            return qr_size


def square(image, position, size):
    """
    Draws a square on an image

    :param image: The image to draw on
    :param position: position of square in the grid, instead of position in img
    :param size: The size of the square in pixels
    :type image: PIL.Image
    :type position: tuple
    :type size int
    """
    x, y = position
    x, y = x * size, y * size
    draw = ImageDraw.Draw(image)
    draw.rectangle(
        [
            (x, y),
            (x + size, y + size)
        ],
        (0, 0, 0)
    )


def add_marker(image, position, size):
    """
    Adds a QR marker to an image

    :param image: The image to draw on
    :param position: The location to draw in pixels
    :param size: The size of the square in pixels
    :type image: PIL.Image
    :type position: tuple
    :type size int
    """
    x, y = position
    draw = ImageDraw.Draw(image)
    for i in range(0, 7):
        square(image, (x + i, y), size)
        square(image, (x + i, y + 6), size)
        square(image, (x, y + i), size)
        square(image, (x + 6, y + i), size)

    for i in range(0, 3):
        for j in range(0, 3):
            square(image, (x + 2 + i, y + 2 + j), size)


def bin2qr(binary, qr_size, square_size):
    """
    Converts binary information to a QR code without formatting

    :param binary A string containing 1's and 0's used for conversion
    :param qr_size The size of the QR code in squares
    :param square_size The size of one square in pixels
    :type binary str
    :type qr_size: int
    :type square_size intw

    :returns: The QR code
    :rtype: PIL.Image
    """
    side = qr_size * square_size
    img = Image.new("RGB", (side, side), "white")

    add_marker(img, (0, 0), square_size)
    if qr_size >= 20:
        add_marker(img, (0, qr_size - 7), square_size)
        add_marker(img, (qr_size - 7, 0), square_size)

    i = 0

    for y in range(0, qr_size):
        s = start_of_line(y, qr_size)
        e = s + length_of_line(y, qr_size)

        for x in range(s, e):
            if binary[i] == '1':
                square(img, (x, y), square_size)

            i += 1

    return img

if __name__ == "__main__":

    # Only needed if it is called from the command line
    # So import it only then
    import argparse
    import sys
    import select

    parser = argparse.ArgumentParser(
        description='Convert binary data to a QR code'
    )

    # Converting arguments
    parser.add_argument(
        'data',
        help="A string containing 0's and 1's. Defaults to stdin",
        nargs='?',
        default=None
    )
    parser.add_argument(
        'filename',
        help="The filename of the new image",
    )
    parser.add_argument(
        '-qr_size',
        type=int,
        help="The amount of squares in width.  Defaults to 25"
    )
    parser.add_argument(
        '-square_size',
        type=int,
        help="The width of a square in pixels. Default to 10"
    )
    parser.add_argument(
        '--text',
        action='store_true',
        default=False,
        help="Take input as a string, which is converted to binary"
    )
    parser.add_argument(
        '--exact',
        action='store_true',
        default=False,
        help="Throws an error when not supplied with exactly enough data"
    )

    args = parser.parse_args()

    qr_size = args.qr_size if args.qr_size is not None else 25
    square_size = args.square_size if args.square_size is not None else 10

    # Unix pipe workaround
    if args.data is None:

        if not select.select([sys.stdin, ], [], [], 0.0)[0]:
            print("No data supplied")
            exit(1)
        args.data = sys.stdin.readlines()
        data = "".join(args.data).strip()

    if args.text:
        data = ''.join(format(ord(x), 'b') for x in data)

    contains_other_chars = not all([c == '0' or c == '1' for c in data])
    if contains_other_chars:
        print("Contains other chars than 0's and 1's")
        exit(1)

    needed_length = sum(
        [length_of_line(i, qr_size) for i in range(0, qr_size)]
    )

    if len(data) < needed_length:
        print("Not enough data supplied")
        exit(1)

    if args.exact:
        if needed_length != len(data):
            print("The data is not the exact length needed: ")
            print("Supplied amount of bits: ", len(data))
            print("Needed amount of bits: ", needed_length)
            exit(1)

    image = bin2qr(data, qr_size, square_size)
    image.save(args.filename)
